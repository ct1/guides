# Setup deployment with Git to a VPS

How to use Git to deploy your application on Digital Ocean

----------

### 1. Install Git and create Git repository in Digital Ocean
Login to your production server.
```
ssh root@<production-server-ip-address>
``` 
Install Git
```
sudo apt-get update
sudo apt-get install git-core
``` 

Create folder to deploy to
```
cd /var
mkdir repo && cd repo
mkdir site.git && cd site.git
git init --bare
``` 
`--bare` means that our folder will have no source files, just the version control.


### 2. Add the post-receive hook
Git repositories have a folder called 'hooks'. This folder contains sample files for possible actions that you can hook and perform custom actions set by you.

In our repository type
```
ls
``` 

Confirm the existance and go to 'hooks' folder:
```
cd hooks
``` 

Create the file 'post-receive'. Replace <projname> with your project production folder. Replace <master> with your emmiting repository
```
cat > post-receive
#!/bin/bash
TARGET='/var/www/<projname>/src'
GIT_DIR='/var/repo/site.git'
BRANCH='<master>'

while read oldrev newrev ref
do
	# only checking out the master (or whatever branch you would like to deploy)
	if [[ $ref=refs/heads/$BRANCH ]];
	then
		echo "Ref $ref received. Deploying ${BRANCH} branch to production..."
		git --work-tree=$TARGET --git-dir=$GIT_DIR checkout -f
	else
		echo "Ref $ref received. Doing nothing: only the ${BRANCH} branch may be deployed on this server."
	fi
done
``` 
When finished typing, press 'control-d' to save.

Now, set permissions to execute this file
```
chmod +x post-receive
``` 
The 'post-receive' file will be looked into every time a push is completed and it's saying that your files need to be in /var/www/<your-dir>.


### 3. Add remote-repository localy
Now add this bare repository to your local system as a remote ideintified by `production`. It can also be called `staging` or `live` or `test` or ... depending on your configuration.

Add a remote repository called 'production':
```
cd ~/Dev/<your-project>
git remote add production ssh://root@<your-DO-ip-address>/var/repo/site.git
``` 
Here we should give the repository link and not the production folder. Make sure `/var/repo/site.git` coresponds to the name you gave in step 1.

### 4. Deploy the project to production
To deploy the project, commit changes and push the master branch to the production server
```
git add .
git commit -m "Project ready"

git push production master
``` 

### 5. Login to production server
```
ssh root@<production-server-ip-address>
``` 

### 6. Remove local settings files
```
rm /var/www/<projname>/src/django_proj/settings/local*
``` 


### NOTE: Alternatively, you can FTP your local django project to Digital Ocean

1. Open an FTP Client (like Transmit or Cyberduck)

2. SFTP into your `<ip_address>` using `root` and your `password`

3. Navigate to `/var/www`

4. Navigate to `<your-project>/django_proj/settings/` and remove `local.py`

5. Open Terminal/PuTTY

6. SSH into your `<ip_address>` like `ssh root@<ip_address>` 

7. Update Apache2 to your project's name/settings if needed.
    confirm 

8. Restart apache `sudo service apache2 restart`

9. Create django models in database
    ```
    cd /var/www/<your-project>
    source env/bin/activate
    python manage.py makemigrations
    python manage.py migrate
    python manage.py collectstatic
    ```
    Two options to create a database superuser
    ```
    # option 1
    python manage.py createsuperuser

    # option 2. Replace superuser (name, email, password) values and execute the command
    echo "from django.contrib.auth import get_user_model; User = get_user_model(); User.objects.create_superuser('superuser_name', 'superuser_email', 'superuser_password')" | python manage.py shell
    ```

10. Restart apache `sudo service apache2 restart`


