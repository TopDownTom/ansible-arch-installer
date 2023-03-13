## initial_user

Create an admin user on the install so we can run our post-install tasks. This essentially is a bare-bones version of create_users, getting us just enough privileges to allow the admin user to sudo.

### Variables

- admin_pass
- admin_user
- hostname
