# Project lifecycle management

Perspectives on how to organize software development 

----------

### Overall architechture

* 'npm install jspm -g'
* 'npm install'
* 'npm start'

![screenshot](https://github.com/ct1/guides/sw_lifecycle_manag.md)
 

Git is used to control SW versions and control the development process.. Repository (Git branch) hierarchy
    ```
	Master branch reflects what is currently in production
	1. From master create a branch test
	2. From test create a branch development
	3. From development create a branch feature-1 (by programmer?)
	4. From development create a branch feature-n (by programmer?)
    ```

### Development team
#### Responsibilities
    ```
	Individual developers are responsible for:
	Developing new features
	First-level tests of the developed features
	Correcting pull requests

	The development manager (or the team together), based on pull requests priorities, and project development priorities, decides on individual workplans
    ```

#### Git support
    ```
	There is one centralized (GitHub/development) repository with team commits and local repositories by individual (or features or pull request) developer
    ```

#### Operations
    ```
	Individual developers  decide on individual commits to the development repository 
	Individual developers constantly synchronize with development repository to maintian themselves as updated as possible (apart own developments)


	Confilcts arise if developers work in the same line/file of code and make individual commits. They need to solve manual this conflicts.
	To minimize conflicts, synchronize must be executed prior to any commit
    ```

### QA team

#### Responsibilities
    ```
	Testers are responsible for: 
		Testing life cycle
		Developing automated tests
		End customer interface
		Pull requests management
		Production releases management
	The QA manager (or the team together) decides on individual workplans and, based on project developments priorities,  on SW release schedules.
    ```

#### Git support
    ```
	There is one centralized (GitHub/test) repository with team commits and local repositories by individual testers
    ```

#### Operations
    ```
	Test cycle is initiated merging the development and the testing environments
	Test cycle ends merging the testing and the production environments

	Testing activities generate pull requests (with assigned priorities)
	Custom reports, upon evaluation, may also generate pull requests (with assigned priorities)
    ```

