# Setup deployment with Git to a Digital Ocean droplet (VPS)

How to use Git to deploy your application on Digital Ocean

----------


### Creating our Repository in Digital Ocean
1. Login to your VPS from command line and type the following
```
cd /var
mkdir repo && cd repo
mkdir site.git && cd site.git
git init --bare
``` 
`--bare` means that our folder will have no source files, just the version control.


### Creating your hooks file control
Git repositories have a folder called 'hooks'. This folder contains some sample files for possible actions that you can hook and perform custom actions set by you.

In our repository type
```
ls
``` 
You will see a few files and folders, including the 'hooks' folder. So let's go to 'hooks' folder:
```
cd hooks
``` 
Now, create the file 'post-receive'. Replace <your-dir> with your project production folder
```
cat > post-receive
#!/bin/sh
git --work-tree=/var/www/<your-dir> --git-dir=/var/repo/site.git checkout -f
``` 
When finished typing, press 'control-d' to save.
Now, set the proper permissions to execute the file
```
chmod +x post-receive
``` 
The 'post-receive' file will be looked into every time a push is completed and it's saying that your files need to be in /var/www/<your-dir>.


#### Deploy from your local Git
Go to your project folder. Then we need to configure the remote path of our repository. Tell Git to add a remote called 'live':
```
cd ~/Dev/<your-project>
git remote add live ssh://user@mydomain.com/var/repo/site.git
``` 
Here we should give the repository link and not the live folder

To deploy our project, commit your changes and push everything to the server
```
git add .
git commit -m "My project is ready"

git push live master
``` 



