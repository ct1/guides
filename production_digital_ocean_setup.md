# Digital Ocean setup

An installation guide for creating a Digital Ocean environment

----------

### 1. Digital Ocean setup

1. Sign up for a Digital Ocean account (https://www.digitalocean.com/)

2. Login

3. Create Droplet

4. Under `Distributions` select `Debian` version `9.3 x64` or newer.

5. Choose a size in your budget

6. Choose a `datacenter region` likely one close to your target audience

7. Leave out SSH Key unless you know how to create one.

8. Finalize and create: 1 Droplet is fine

Let Droplet finish initializing. Check email for the following (unless you setup an SSH Key on step #7):

```
Droplet Name: <droplet_name>
IP Address: <ip_address>
Username: <username>
Password: <password>
``` 


#### 2. Connect to Digital Ocean server

1. In terminal([PuTTY](http://www.putty.org/) if on Windows) enter:
    ```
     # format
     ssh root@<ip_address>

     #example:
     ssh root@104.131.146.34
    ```

2. You should see a message like:
    ```
    The authenticity of host '104.131.146.34 (104.131.146.34)' can't be established.
    ECDSA key fingerprint is SHA256:Dg4o2zcwBMMx72IJEgdhJm/iDWtoQzWVmSuRV8B4db4.
    Are you sure you want to continue connecting (yes/no)?   
    ```
    Type `yes` then hit `enter`

3. It should ask for your `<password>`, we received in our email (unless you setup an SSH Key)

4. It will ask for your `<password>` again so you can reset it. Set a `<new_password>` and then confirm it.
From now on, use this `<new_password>` with `ssh root@<ip_address>` to acccess your `Droplet`.
 
