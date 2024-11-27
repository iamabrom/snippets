# Create a Amazon Cognito user pool that supports the use of a preferred username

## Overview

If you're needing to create an Amazon Cognito user pool that supports logging in with a preferred username (using the `preferred_username` attribute), then you must create the user pool through either the CLI, SDKs, or CloudFormation. You can of use other supported Infrastructure as Code tools as well (e.g. Terraform).

You must use the [CreateUserPool](https://docs.aws.amazon.com/cognito-user-identity-pools/latest/APIReference/API_CreateUserPool.html) API to create a Cognito user pool programmatically.

Below are examples of various ways create a user pool with the bare minimum needed to create a new user pool supporting the use of a preferred username. The key aspect is ensuring you're supplying the `preferred_username` attribute to the `alias-attributes` request parameter for the `CreateUserPool` API.

User Pool settings for the below examples are all the same and set the following:

- Allow signing in with email, username, and a preferred username
- The `preferred_username` is not a required attribute
- The `email` is a required attribute
- Usernames are not case sensitive
- The user pool will handle auto verification of the email attribute

### Examples
- [CLI](#cli)
- [CloudFormation (YAML)](#cloudformation-yaml)
- [CloudFormation (JSON)](#cloudformation-json)
- [JavaScript](#aws-sdk-for-javascript-v3)
- [Python](#aws-sdk-for-python-boto3)
- [Java](#aws-sdk-for-java-v2)

> [!CAUTION]
> The below examples are for informational purposes only and you are responsible for making your own independent assessment of the information shared here. This is provided “as is” without warranties, representations, or conditions of any kind, whether expressed or implied.

---

> [!IMPORTANT]  
> Ensure to update the `<USER-POOL-NAME>` and `<REGION>`, as applicable, in the below examples.

## CLI

```shell
aws cognito-idp create-user-pool \
  --pool-name "<USER-POOL-NAME>" \
  --username-configuration CaseSensitive=false \
  --alias-attributes "email" "preferred_username" \
  --auto-verified-attributes "email" \
  --schema '[
    {"Name":"preferred_username","AttributeDataType":"String","Mutable":true,"Required":false},
    {"Name":"email","AttributeDataType":"String","Mutable":true,"Required":true}
  ]'
```

## CloudFormation (YAML)

```yaml
Resources:
  MyTestUserPool:
    Type: AWS::Cognito::UserPool
    Properties:
      PoolName: <USER-POOL-NAME>
      UsernameConfiguration:
        CaseSensitive: false
      AliasAttributes:
        - email
        - preferred_username
      AutoVerifiedAttributes:
        - email
      Schema:
        - Name: preferred_username
          AttributeDataType: String
          Mutable: true
          Required: false
        - Name: email
          AttributeDataType: String
          Mutable: true
          Required: true
```

## CloudFormation (JSON)

```json
{
  "Resources": {
    "MyTestUserPool": {
      "Type": "AWS::Cognito::UserPool",
      "Properties": {
        "PoolName": "<USER-POOL-NAME>",
        "UsernameConfiguration": {
          "CaseSensitive": false
        },
        "AliasAttributes": [
          "email",
          "preferred_username"
        ],
        "AutoVerifiedAttributes": [
          "email"
        ],
        "Schema": [
          {
            "Name": "preferred_username",
            "AttributeDataType": "String",
            "Mutable": true,
            "Required": false
          },
          {
            "Name": "email",
            "AttributeDataType": "String",
            "Mutable": true,
            "Required": true
          }
        ]
      }
    }
  }
}

```
## AWS SDK for JavaScript (v3)

```javascript
import { CognitoIdentityProviderClient, CreateUserPoolCommand } from "@aws-sdk/client-cognito-identity-provider";

const client = new CognitoIdentityProviderClient({ region: "<REGION>" });

const input = {
    PoolName: "<USER-POOL-NAME>",
    UsernameConfiguration: { CaseSensitive: false },
    AliasAttributes: ["email", "preferred_username"],
    AutoVerifiedAttributes: ["email"],
    Schema: [
        {
            Name: "preferred_username",
            AttributeDataType: "String",
            Mutable: true,
            Required: false
        },
        {
            Name: "email",
            AttributeDataType: "String",
            Mutable: true,
            Required: true
        }
    ]
};

const command = new CreateUserPoolCommand(input);

const run = async () => {
    try {
        const response = await client.send(command);
        console.log("User Pool Created:", response);
    } catch (err) {
        console.error("Error creating user pool:", err);
    }
};

run();

```

## AWS SDK for Python (Boto3)

``` python
import boto3

client = boto3.client("cognito-idp", region_name="<REGION>")

response = client.create_user_pool(
    PoolName="<USER-POOL-NAME>",
    UsernameConfiguration={"CaseSensitive": False},
    AliasAttributes=["email", "preferred_username"],
    AutoVerifiedAttributes=["email"],
    Schema=[
        {
            "Name": "preferred_username",
            "AttributeDataType": "String",
            "Mutable": True,
            "Required": False
        },
        {
            "Name": "email",
            "AttributeDataType": "String",
            "Mutable": True,
            "Required": True
        }
    ]
)

print("User Pool Created:", response)

```

## AWS SDK for Java V2

``` java
import software.amazon.awssdk.services.cognitoidentityprovider.CognitoIdentityProviderClient;
import software.amazon.awssdk.services.cognitoidentityprovider.model.*;

public class CreateUserPoolExample {
    public static void main(String[] args) {
        CognitoIdentityProviderClient client = CognitoIdentityProviderClient.builder().region(Region.<REGION>).build();

        CreateUserPoolRequest request = CreateUserPoolRequest.builder()
                .poolName("<USER-POOL-NAME>")
                .usernameConfiguration(UsernameConfigurationType.builder().caseSensitive(false).build())
                .aliasAttributes("email", "preferred_username")
                .autoVerifiedAttributes("email")
                .schema(
                        SchemaAttributeType.builder()
                                .name("preferred_username")
                                .attributeDataType(AttributeDataType.STRING)
                                .mutable(true)
                                .required(false)
                                .build(),
                        SchemaAttributeType.builder()
                                .name("email")
                                .attributeDataType(AttributeDataType.STRING)
                                .mutable(true)
                                .required(true)
                                .build()
                )
                .build();

        CreateUserPoolResponse response = client.createUserPool(request);
        System.out.println("User Pool Created: " + response.userPool().id());
        client.close();
    }
}

```
