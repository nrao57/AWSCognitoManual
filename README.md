# AWSCognitoManual
A manual for how to use AWS Javascript SDK

AWS Resources Needed
--------------------
1. User Pool
2. Identity Pool

SDK's Needed
------------
  - https://sdk.amazonaws.com/js/aws-sdk-2.7.16.min.js
  - amazon-cognito-identity.min.js

Other Resources
---------------
The full How-to can be found at https://github.com/aws/aws-amplify/tree/master/packages/amazon-cognito-identity-js

## How-To

### Setup User Pool on AWS Cognito Console
  - Under App Integration
  ![](https://github.com/nrao57/AWSCognitoManual/blob/master/userpool.png)

### Only code needed to get the signed-in user info:
    var poolData = {
        UserPoolId : 'us-east-1_1v9ATpWph', // Your user pool id here
        ClientId : '4lskkp5jn5b0ru8048ag5m88d7' // Your client id here
    };
    var userPool = new AmazonCognitoIdentity.CognitoUserPool(poolData);
    var cognitoUser = userPool.getCurrentUser();

### To set the authenticated and unauthenticated credentials of the user pool
1. Go to the Cognito identity pool settings
![](https://github.com/nrao57/AWSCognitoManual/blob/master/identitypool.png)

2. Click "Edit Identity Pool"
3. Create a new "Unauthenticated Role" and "Authenticated Role"
![](https://github.com/nrao57/AWSCognitoManual/blob/master/createroles.png)


