6700 Dashboard Backend Codebase Documentation

1) System Overview
Project: 6700 Dashboard – Backend API
Runtime / Framework: Node.js (TypeScript) with NestJS
ORM: TypeOrm (MySQL)
Primary Modules: Users, Auth, Permissions, USSD Shortcodes
Key Concerns: Secure authentication (including OTP), strict access control, validated CRUD operations.

2) Project Structure
src/
  main.ts
  app.module.ts
  auth/
  users/
  permissions/
  ussd/
  database/

4) Modules & Endpoints
4.1 Users Module
Responsibilities: The Users Module manages user-related resources, including user profiles, creation, updates, and two-factor authentication (2FA)
Endpoints:
Endpoint
Method
Description
/user
POST
Creates a new user (accessible only to admins)
/user/:userId
PATCH
Updates a user’s profile (name, email, 2FA activation status)
/user
GET
Fetches all users in the system
/user/:userId
GET
Retrieves details of a specific user by ID
/user/:id/permissions
PATCH
Grants or revokes permissions for a user with the provided id.




Schema:
Property
Type
Nullable
Description
id
Number
No
Unique identifier for each user
first_name
String
No
User’s first name
last_name
String
No
User’s last name
email
String
No
User’s email address
password
String
Yes
User’s password
createdAt
Date
No
Timestamp when the user was created
updatedAt
Date
No
Timestamp of the most recent update
lastLoginAt
Date
Yes
Timestamp of last login
is2faEnabled
Boolean
No
Indicates if 2FA is enabled
twoFASecret
String
Yes
Secret key for 2FA setup
Status
String (enum)
No
Current status of the user (active, inactive)
isAdmin
Boolean
No
Indicates if a user is admin or not.



4.2 Auth Module
Responsibilities: The Authentication Module is responsible for managing user authentication processes, including login, logout, and two-factor authentication (2FA)
Endpoints:

Endpoint
Method
Description
/auth/validate_email
POST
Validates users’ email addresses
/auth/validate_password
POST
Validates admins password, sends an OTP to admin’s password is valid, otherwise an error response is returned.
/auth/setup_2fa
POST
Generates a QR code with which users can set up an authenticator app.
/auth/validate_2fa_code
POST
Validate the TOTP generated from an authenticator app or the OTP sent to admin’s email. If TOTP or OTP is valid, logs user in, otherwise an error response is returnen



4.3 Permissions Module
Responsibilities: The Permissions Module handles user permissions, allowing assignment and un-assignment of permissions to users. Permissions are pre-populated in the database.
Endpoints:
Endpoint
Method
Description
/permissions
GET
Fetches all available permissions
/permissions/:id
GET
Fetches permission with the provided ID


Schema (Permission and User):
Property
Type
Nullable
Description
id
Number
No
Unique identifier for the permission
name
String
No
Name of the permission
isActive
Boolean
No
Indicates if permission is active or not


Property
Type
Nullable
Description
userId
Number
No
ID of the user that owns this permission
permissionId
number
No
ID of permission owned by this user



4.4 USSD Module
Responsibility: The USSD Module is responsible for managing USSD short codes, including creation, updating, and activation or deactivation. Only users with the appropriate permissions can manage USSD short codes.
Endpoints:
Endpoint
Method
Description
/ussd
POST
Creates a new USSD short code
/ussd
GET
Retrieves all USSD short codes
/ussd/:ussdId
GET
Retrieves details of a specific USSD code by ID
/ussd/:ussdId
PATCH
Updates an existing USSD short code



Schema:
Property
Type
Nullable
Description
id
Number
No
Unique identifier for the USSD code
name
String
No
Name of the USSD code
extension
String
No
USSD extension number
url
String
Yes
URL associated with the USSD code
type
String (enum)
No
Type of USSD code (e.g., static, dynamic)
Short_code
String
No
The short code (e.g. 6700) this extension belongs to.
isActive
Boolean
No
An indicator that shows if this ussd is currently active or not.


