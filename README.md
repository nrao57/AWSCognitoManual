# AWS Cognito Manual
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

1. User Pool - Database for storing all Users and User Information 
2. Identity Pool - Stores Credentials and Roles for User Pools

![](https://github.com/nrao57/AWSCognitoManual/blob/master/imgs/CognitoDiagram.PNG)

[More information can be found here](https://docs.aws.amazon.com/cognito/latest/developerguide/what-is-amazon-cognito.html)

### Setup User Pool on AWS Cognito Console
  - Under App Integration
  ![](https://github.com/nrao57/AWSCognitoManual/blob/master/imgs/userpool.png)

### Only code needed to get the signed-in user info:
    var poolData = {
        UserPoolId : 'us-east-1_1v9ATpWph', // Your user pool id here
        ClientId : '4lskkp5jn5b0ru8048ag5m88d7' // Your client id here
    };
    var userPool = new AmazonCognitoIdentity.CognitoUserPool(poolData);
    var cognitoUser = userPool.getCurrentUser();

### To set the authenticated and unauthenticated credentials of the user pool
1. Go to the Cognito identity pool settings
![](https://github.com/nrao57/AWSCognitoManual/blob/master/imgs/identitypool.png)

2. Click "Edit Identity Pool"
3. Create a new "Unauthenticated Role" and "Authenticated Role"
![](https://github.com/nrao57/AWSCognitoManual/blob/master/imgs/createroles.png)

4. Add polcies for the Amazon Resources you want the signed-in users to have access to 

### To Register New Users
Create a config file that looks like
    
    window._config = {
        cognito: {
            userPoolId: '..', // e.g. us-east-2_...
            userPoolClientId: '...', 
            region: '...', // e.g. us-east-2
            identityPoolId : '...',
            clientId: '...'
        },
    api: {
        invokeUrl: '' // e.g. https://rc7nyt4tql.execute-api.us-west-2.amazonaws.com/prod',
    }
    };

To register a user with the username "username" and password "password"

    <script>
		
		var username;
		var password;
		var poolData;
		
	  function registerButton() {
			
		username = document.getElementById("emailInputRegister").value;
		password =  document.getElementById("passwordInputRegister").value;	
		
		poolData = {
				UserPoolId : '...', // Your user pool id here
				ClientId : '...' // Your client id here
			};		
		
		var userPool = new AmazonCognitoIdentity.CognitoUserPool(poolData);

		var attributeList = [];

		var dataEmail = {
			Name : 'email', 
			Value : username, //get from form field
		};

		var dataPhoneNumber = { //not needed for our application
			Name : 'phone_number', //sdfsdfsd
			Value : '+15555555555' //sdfsdfsdf
		};
		
		var attributeEmail = new AmazonCognitoIdentity.CognitoUserAttribute(dataEmail);
		//var attributePhoneNumber = new AmazonCognitoIdentity.CognitoUserAttribute(dataPhoneNumber);

		attributeList.push(attributeEmail);
		//attributeList.push(attributePhoneNumber);

		userPool.signUp(username, password, attributeList, null, function(err, result){
			if (err) {
				alert(err.message || JSON.stringify(err));
				return;
			}
			cognitoUser = result.user;
			console.log('user name is ' + cognitoUser.getUsername());
			//change elements of page
			document.getElementById("titleheader").innerHTML = "Enter your verification code";
			var pass1 = document.getElementById("passwordInputRegister");
			pass1.type="hidden";
			var pass2 = document.getElementById("password2InputRegister");
			pass2.type="hidden";
			var vCode = document.getElementById("codeInputVerify");
			vCode.type="text";
			var emailfield = document.getElementById("emailInputRegister");
			emailfield.type="hidden"
			//change name and action of registerButton
			var mButt = document.getElementById("mainbutton");
			mButt.innerHTML="Verify";
			mButt.onclick = verifyButton;
			
		});
	  }

And to verify once the verfification code is sent

	function verifyButton() {	
		var verificationCode = document.getElementById("codeInputVerify").value;

		var userPool = new AmazonCognitoIdentity.CognitoUserPool(poolData);
		var userData = {
			Username : username,
			Pool : userPool
		};

		var cognitoUser = new AmazonCognitoIdentity.CognitoUser(userData);
		cognitoUser.confirmRegistration(verificationCode, true, function(err, result) {
			if (err) {
				alert(err.message || JSON.stringify(err));
				return;
			} 
			
			location.href='thanks_in.html';
			console.log('call result: ' + result);
		});
		
		
		
		 cognitoUser.resendConfirmationCode(function(err, result) {
			if (err) {
				alert(err.message || JSON.stringify(err));
				return;
			}
			console.log('call result: ' + result);
		});
		
	}
    </script>

	
### Get the logged in user from local storage 
(Don't forget: GetSession needed to get current logged in user)

    var poolData = {
        UserPoolId : _config.cognito.userPoolId, // Your user pool id here
        ClientId : _config.cognito.clientId // Your client id here
    };
    var userPool = new AmazonCognitoIdentity.CognitoUserPool(poolData);
    var cognitoUser = userPool.getCurrentUser();
	
	cognitoUser.getUserAttributes(function(err, result) {
        if (err) {
            alert(err.message || JSON.stringify(err));
            return;
        }
		
        for (i = 0; i < result.length; i++) {
            console.log('attribute ' + result[i].getName() + ' has value ' + result[i].getValue());
        }
    });
	
### Log Out Current User

    var poolData = {
        UserPoolId : '...', // Your user pool id here
        ClientId : '...' // Your client id here
    };
    var userPool = new AmazonCognitoIdentity.CognitoUserPool(poolData);
    var cognitoUser = userPool.getCurrentUser();
	
	//log out 
	cognitoUser.signOut();
	

### App Client settings (OAuth, OpenID, ...)

### User settings from local storage


