# Register and IAM

Manipulate Services by IAM user, not Root account

## Register

1. Register a free tier account

## Create an IAM User

### Create Group

1. Get into `IAM`
1. `Create a Group` for Administrator, give a group name you like, like Administrator
1. Attach Policy `AdministratorAccess` to this group

### Create User

1. Add User for yourself
1. Give yourself all `Access Type`
1. Add this user to `Administrator` Group
1. Login from `IAM users sign-in link` by this IAM User
1. Logout Root User and Login from `IAM users sign-in link` by IAM User Account

### Additional

* [Understand Policy](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies.html)
* [Policy Example](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_examples.html)
* [IAM Best Practice](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html)
