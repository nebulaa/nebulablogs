---
title: "Securing Streamlit: A Guide to Detecting Brute Force Attacks, Validating Referral Codes, and Automating Account Security with AWS Cognito and Boto3"
datePublished: Sun Nov 26 2023 20:27:29 GMT+0000 (Coordinated Universal Time)
cuid: clpfxlyth000009l37sz83wr9
slug: securing-streamlit-a-guide-to-detecting-brute-force-attacks-validating-referral-codes-and-automating-account-security-with-aws-cognito-and-boto3
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/WVKFthwtJwU/upload/58c94871e2a9fbacf46f5aaf710f75c8.jpeg
tags: lambda, aws, python, security, streamlit

---

In my previous post, I discussed how to use Streamlit for building forms and enabling users to upload images. This project also needed an additional layer of access control to only allow a subset of users with referral codes to view the form.

The main issue to address here is to prevent brute-force attacks on the referral code input field. However, this could extend to any resource in Streamlit that you may want to rate-limit to not overwhelm the backend services.

Streamlit has a cool feature called '[**Session State**](https://docs.streamlit.io/library/api-reference/session-state)' which allows us to keep track of user actions. According to Streamlit's documentation:

*"Session State is a way to share variables between reruns, for each user session. In addition to the ability to store and persist state, Streamlit also exposes the ability to manipulate state using Callbacks. Session state also persists across apps inside a* [*multipage app*](https://docs.streamlit.io/library/get-started/multipage-apps)*."*

Using this feature we could keep track of how many times a user attempts to enter the referral code and provide immediate feedback on incorrect attempts so users can use the appropriate channel to get a valid referral code.

If the user continues to brute force the referral code, we will employ a Lambda trigger to disable the user account in the AWS Cognito user pool.

Example Streamlit code which explains how to use 'session state' to track user re-tries.

```python
import streamlit as st
import json

if 'count' not in st.session_state:
    st.session_state.count = 0

def increment_counter():
    st.session_state.count += 1

st.write("**Please enter the invite code.**")

if st.session_state.count <= 5:
    ref_code = st.text_input("Enter the referral code", on_change=increment_counter, key="ref_code", help="You have 5 retries to enter the correct referral code.")

else:
    st.error("You have exceeded the maximum number of retries.")
    ref_code = None
    disable_user_api = "https://test.execute-api.us-east-2.amazonaws.com/disable_user"

    disable_user_payload = {
    "uuid": uuid
    }

    disable_user_headers = {
        'Content-Type': 'application/json',
        'Authorization': access_token # This is the access token for API Gateway if authorization is enabled.
    }

    try:
        disable_user_response = requests.post(disable_user_api, data=json.dumps(disable_user_payload), headers=disable_user_headers)
        st.success("Your account has been disabled. Please contact the administrator.")
        
    except Exception as disable_user_err:
        st.error(f'An error occurred: {disable_user_err}')


# Continue to check if the referral code matches a valid code in your database.
# Sample code:

if ref_code == "1234":
    st.write("Referral code is correct. Please enter your details below.")
    # Display the form
else:
    st.error("Referral code is invalid. If you do not have a referral code, please write to us @ some email.")
    st.error("Invalid attempt count: " + str(st.session_state.count) + ". You have " + str(5 - st.session_state.count) + " attempts left.")
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1701027874965/23bb007f-6680-4398-adc5-0afed929bb52.png align="center")

Streamlit renders the above text box upon running the Python code `streamlit run app.py`. After submitting an incorrect code, Streamlit throws the following error message:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1701028321454/5f2ebffc-94a3-4bc2-8722-77d3e840a1e7.png align="center")

In this example, I've set the maximum retries to 5 before the account gets disabled. Session State uses the callback functionality by using the 'on\_change' parameter to keep track of the number of submission attempts.

The 6th incorrect submission will trigger the backend Lambda function by sending an HTTP POST request along with the user's unique account ID (uuid) in the request body.

Example Lambda function:

```python
import json
import boto3

def lambda_handler(event, context):
    http_method = event['requestContext']['http']['method']
    
    if http_method == 'POST':
        body = json.loads(event['body'])
        uuid = body['uuid']

        disable_user(uuid)
        handle_sns(uuid)
        
        return {
            'statusCode': 200,
            'body': json.dumps("User disabled.")
        }
    
    if http_method == 'GET':
        return {
            'statusCode': 200
        }

    return {
        'statusCode': 200
    }


def disable_user(uuid):

    client_idp = boto3.client('cognito-idp')

    client_idp.admin_disable_user(
        UserPoolId='us-east-1_xxxxxxx',
        Username=uuid
    )

def handle_sns(uuid):

    client_sns = boto3.client('sns')

    sns_message = """
        User account has been disabled. Find the UUID below:

        UUID    : {uuid}
        """.format(uuid=uuid)
        
    client_sns.publish(
        TopicArn='arn:aws:sns:us-east-1:XXXXXXXXXX:topic-name',
        Message= sns_message,
        Subject='Account disabled')
```

The example lambda function performs two actions:

1. Uses `boto3.client('cognito-idp')` to disable the user account in AWS Cognito based on the User-Pool ID.
    
2. Uses `boto3.client('sns')` to send a notification to the administrator's email as per the TopicARN (SNS configuration).
    

For the lambda function to properly execute the actions, ensure that the AWS role associated with the lambda function has permission to disable the user for the particular AWS pool ID as an administrator and to trigger the SNS Topic.