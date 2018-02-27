# Django - elastic beanstalk deployment guide

An installation guide for getting Django setup on Amazon Web Services (AWS) Elastic Beanstalk

----------

*NOTE* Replace `projname` in all commands with the name of your project

1. Setup git.
	```
	cd ~/Dev/projname/src
	```

	**Activate virtualenv** 

	Mac/ Linux : `source ../env/bin/activate`

	Windows: `.\Scripts\activate`

	**Initialize git (need to [install it](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)?) inside of your `virtualenv`.**
	```
	git init 
	```

	**Create gitignore (Mac/Linux)**
	```
	touch .gitignore
	open .gitignore
	```

	**Create gitignore (Windows)**
	1. Download and Install  [Sublime Text](http://www.sublimetext.com/) -- Free version works fine. Or any other code text editor.
	2. Open `Sublime Text` (or your code text editor)
	3. `File` > `New File`
	4. `File` > `Save`
	5. Save the file as `.gitignore` (not the `.` prior.) into your newly created virtualenv


	**Add & save the following to your newly created `.gitignore` file:**
	```
	env/
	*.py[cod]
	.DS_Store
	*.xlsx
	*.log
	*.docx
	```



2. **Install [AWS command line interface](http://docs.aws.amazon.com/elasticbeanstalk/latest/dg/eb-cli3-install.html)**:
	
	**Install AWS CLI**
	```
	pip install awsebcli
	```
		
	__Test installation__
	
	```
	eb --version
	```
	
	**Returns** something like 
	```
	EB CLI 3.12.3 (Python 3.5.1)
	```

	**Update (or create) `requirements.txt`**
	Everytime you add new python packages, such as the `Django Rest Framework` or `Requests`, you need to update and commit your requirements.

	```
    # currently working in 
    # ~/Dev/projname/src on mac/linux
    # \Users\YourName\Dev\projname\src on windows

	pip freeze  #returns what packages are required/installed in your virtualenv.
	pip freeze > requirements.txt
	```

3. **Git commit**
	```
	git add .
	git commit -m "first commit"
	```

4. **Create AWS user credentials**
	
	1. Navigate to [IAM Users](https://console.aws.amazon.com/iam/home?#users)
	
	2. Select `Create New Users`
	
	3. Enter `username` as your username
	
	4. Ensure `Generate an access key for each user` is **selected**.
	
	5. Select `Download credentials` and keep them safe. 
	
	6. Open the `credentials.csv` file that was just downloaded/created 

	7. Note the `Access Key Id` and `Secret Access Key` as they are needed for a future step. These will be referred to as `<your_access_key_id>` and `<your_secret_access_key>`


5. **Create EB application**
	** Ensure virtualenv is activated **
	```
	cd /path/to/root/of/your/virtualenv/
	source bin/activate # if Mac/Linux
	.\Scripts\activate * if Windows
	```
	Initialize EB:

	```
	eb init 
	```

	This will respond with a few prompts you need to answer. Here's what we did:
	```
		Select a default region
		1) us-east-1 : US East (N. Virginia)
		2) us-west-1 : US West (N. California)
		3) us-west-2 : US West (Oregon)
		4) eu-west-1 : EU (Ireland)
		5) eu-central-1 : EU (Frankfurt)
		6) ap-south-1 : Asia Pacific (Mumbai)
		7) ap-southeast-1 : Asia Pacific (Singapore)
		8) ap-southeast-2 : Asia Pacific (Sydney)
		9) ap-northeast-1 : Asia Pacific (Tokyo)
		10) ap-northeast-2 : Asia Pacific (Seoul)
		11) sa-east-1 : South America (Sao Paulo)
		12) cn-north-1 : China (Beijing)
		13) cn-northwest-1 : China (Ningxia)
		14) us-east-2 : US East (Ohio)
		15) ca-central-1 : Canada (Central)
		16) eu-west-2 : EU (London)
		17) eu-west-3 : EU (Paris)
		(default is 3): 17                # this is our answer

		You have not yet set up your credentials or your credentials are incorrect
		You must provide your credentials.
		(aws-access-id): <your_access_key_id>
		(aws-secret-key): <your_secret_access_key>

		Enter Application Name
		(default is "src"):         # press enter to use the default
		Application awsbean has been created.

		It appears you are using Python. Is this correct?
		(Y/n): y

		Select a platform version.
		1) Python 3.6
		2) Python 3.4
		3) Python 3.4 (Preconfigured - Docker)
		4) Python 2.7
		5) Python
		(default is 1): 1		# press enter to use the default

		Do you want to set up SSH for your instances?
		(y/n): y                            # Optional, not needed at this point.

		Note: Elastic Beanstalk now supports AWS CodeCommit; a fully-managed source control service. To learn more, see Docs: https://aws.amazon.com/codecommit/
		Do you wish to continue with CodeCommit? (y/N) (default is n):						# press enter to use the default

		Do you want to set up SSH for your instances?
		(Y/n):
		
		Select a keypair.
		1) aws-eb
		2) aws-eb2
		3) [ Create new KeyPair ]      # If you said yes to SSH, this is required.
	```


6. **Setup EB extension**
	Create new folder called `.ebextensions` in the root of your virtualenv where the `.elasticbeanstalk` directory is as well.
	Add a new file called `python.config` in `.ebextensions` with contents of:
	*NOTE* Replace 'django_proj' with your django settings path.

	```
	mkdir .ebextensions
	nano .ebextensions/python.config 
	```

	Add the following to the configuration file:

	```
   container_commands:
     01_migrate:
       command: "python manage.py migrate"
       leader_only: true
     02_collectstatic:
       command: "python manage.py collectstatic --noinput"
       
   option_settings:
      "aws:elasticbeanstalk:application:environment":DJANGO_SETTINGS_MODULE: "django_proj.settings.settings"
      PYTHONPATH: "$PYTHONPATH"
      "aws:elasticbeanstalk:container:python": WSGIPath: "django_proj/wsgi.py"
      StaticFiles: "/static/=www/static/"
      
   packages:
      yum:
      	postgresql95-devel: []
	```

	Commit in git:
	```
	git add .
	git commit -m "Created EB extensions"
	```


7. **Django production `settings.py`:**
	* In many cases, you will have a `production` version of your `settings.py` instead of the default `Django` install.

	```
	if 'RDS_DB_NAME' in os.environ:
		DATABASES = {
		    'default': {
		        'ENGINE': 'django.contrib.gis.db.backends.postgis',
		        'NAME': os.environ['RDS_DB_NAME'],
		        'USER': os.environ['RDS_USERNAME'],
		        'PASSWORD': os.environ['RDS_PASSWORD'],
		        'HOST': os.environ['RDS_HOSTNAME'],
		        'PORT': os.environ['RDS_PORT'],
		    }
		}
	```

	Commit in git:
	```
	git add .
	git commit -m "Added EB database config"
	```


8. **1st Deploy to EB**
	Do Deployment:
	```
	eb deploy
	```
	**Returns** something like:
	```
	Creating application version archive "app-903f-151207_174054".
	Uploading awsbean/app-903f-151207_174054.zip to S3. This may take a while.
	Upload Complete.
	INFO: Environment update is starting.                               
	INFO: Deploying new version to instance(s).                         
	INFO: New application version was deployed to running EC2 instances.
	INFO: Environment update completed successfully.
	```


10. **Create Superuser with Custom Management** 
	Nice! We have deployed Django to elastic beanstalk! Great work. Now how do we create a superuser? Run collect static? This is when we need to add a few more customizations to the `.ebextensions` folder.
	
	1. Create a new Django App
		```
		cd src
		python manage.py startapp accounts
		cd accounts
		mkdir management
		cd management
		touch __init__.py
		mkdir commands
		cd commands
		touch __init__.py
		touch makesuper.py
		open makesuper.py
		```

	2. Create the `makesuper` Command inside the `makesuper.py` file we just created:
		```
		from django.contrib.auth import get_user_model
		from django.core.management.base import BaseCommand

		class Command(BaseCommand):
		    def handle(self, *args, **options):
		        User = get_user_model()
		        if not User.objects.filter(username="superu").exists():
		            User.objects.create_superuser("superu", "superu@teamcfe.com", "superuPASS")
		```
		This code checks if the `superu` exists and creates it if it doesn't. 

	3. Add the new `accounts` app to `INSTALLED_APPS` in `settings.py` 

	4. Now, we can use `python manage.py makesuper` which will automatcially create a Django super user with the username `superu` and password `superuPASS` -- this means we can login to the Django admin.


	5. Add new additions to git:
		```
		git add --all
		git commit -m "Makesuper command & Accounts App"
		```

	6. Create new EB Extension:
		Add `04_python.config` to `.ebextensions` folder with the following:
		```
		container_commands:
		  01_migrate:
		    command: "python src/manage.py makesuper"
		    leader_only: true
		```

	7. Add new eb ebextension to git:
		```
		git add --all
		git commit -m "Updated Eb Extensions"
		```
	
11. **Deploy!** 

	`eb deploy`

