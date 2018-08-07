# Base app development steps

----------

### Build from green field

1. Create django app using the DjangoX application (install DjangoX)

This app manages custom user account and has configured all-auth (web templates) and rest-framework (json data) API

It has already implemented setting to login by email (no username)

Create Django project named django_proj

2. Implement registration (with no mail confirmation)

Implement rest-auth authorization. Ensure the acount registration workflow in initiated by the rest-auth-registration API and migrates its processing to all-auth API 

3. Implement registration (with mail confirmation)

Configure account validation settings to require email validation prior to login

4. Install reset password

Ensure password reset can be initiated by rest API but then the workflow is processed through all-auth (via templates -> change password screen is web and not mobile)

5. Reformat file structure (django_proj) to accomodate local, production

Separe common from local and production settings in different files

6. Implement applicative API (all non-auth)

7. Implement internationalization

8. Implement custom login to receive device statistics

9. If its the case, move from session authentication to JWT authentication. The basic difference is that a JWT token expires and session token no. For security reasons better work with tokend that expire

10. Migrate to production

