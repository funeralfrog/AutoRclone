<h1 align="center">AutoRclone 🔥</h1> 


<hr>


> ## AutoRclone: rclone copy/move/sync (automatically) with thousands of service accounts.


- [x] Create service accounts using script
- [x] Add massive service accounts into rclone config file
- [x] Add massive service accounts into groups for your organization
- [x] Automatically switch accounts when rclone copy/move/sync 
- [x] Windows system is supported

Step 1. Copy code to your VPS or local machine
---------------------------------
_Before everything, install python3. Because we use python as our programing language._

**For Linux system**: Install
[screen](https://www.interserver.net/tips/kb/using-screen-to-attach-and-detach-console-sessions/),
`git` 
and [latest rclone](https://rclone.org/downloads/#script-download-and-install). 
If in Debian/Ubuntu, directly use this command
```
sudo apt-get install screen git && curl https://rclone.org/install.sh | sudo bash
```
After all dependency above are successfully installed, run this command
```
sudo git clone https://github.com/sawankumar/AutoRclone && cd AutoRclone && sudo pip3 install -r requirements.txt
```
**For Windows system**: Directly download this project then [install latest rclone](https://rclone.org/downloads/). 
Then run this command (type in cmd command windows or PowerShell windows) in our project folder
```
pip3 install -r requirements.txt
```
**For Android**: Setting Up AutoRclone in Termux 
```
termux-setup-storage
```
```
apt update
```
```
apt upgrade
```
```
apt-get install wget
```
```
apt-get install tar
```
```
apt-get install gzip
```
```
pip install --upgrade pip
```
```
pkg install python
```
```
pkg install rclone
```
```
cd storage/downloads
```
```
git clone https://github.com/sawankumar/AutoRclone
```
```
pip3 install -r requirements.txt
```

Step 2. Generate service accounts 
---------------------------------

Enable the Drive API in [Python Quickstart](https://developers.google.com/drive/api/v3/quickstart/python)
and save the file `credentials.json` into project directory.

If you do not have any project in your account then 
* Create 1 project
* Enable the required services
* Create 100 Service Accounts (1 project, each with 100) 
* And download their credentials into a folder named `accounts`


**Note: 1 service account can copy around 750gb a day, 1 project makes 100 service accounts so that's 75tb a day.**


Run this and replace "1" with the number of projects you want

```
python3 gen_sa_accounts.py --quick-setup 1
```

If you have already N projects and want to create service accounts only in newly created projects, to 

* Create additional 1 project (project N+1 to project N+2)
* Enable the required services
* Create 100 Service Accounts (1 project, with 100)
* And download their credentials into a folder named `accounts`
 
Run 

```
python3 gen_sa_accounts.py --quick-setup 1 --new-only
```

If you want to create some service accounts using existing projects (do not want to create more projects), run 

```
python3 gen_sa_accounts.py --quick-setup -1
```

Note that this will overwrite the existing service accounts.

After it is finished, there will be many json files in one folder named `accounts`. 

**NOTE:** If you have created SAs in past from this script, you can also just re download the keys by running:
```
python3 gen_sa_accounts.py --download-keys project_id
```

**If there is issue when creating Service accounts, Check if all these APIs are enabled in**

```
https://console.developers.google.com/apis/dashboard
```

1. [Google Drive API](https://console.developers.google.com/apis/api/drive.googleapis.com/overview?project=project-name)

2. [Cloud Resource Manager API](https://console.developers.google.com/apis/api/cloudresourcemanager.googleapis.com/overview?project=project-name)

3. [IAM Service Account Credentials API](https://console.developers.google.com/apis/api/iamcredentials.googleapis.com/overview?project=project-name)

4. [Identity and Access Management (IAM) API](https://console.developers.google.com/apis/api/iam.googleapis.com/overview?project=project-name)

5. [Service Usage API](https://console.developers.google.com/apis/api/serviceusage.googleapis.com/overview?project=project-name)

Step 3. Add service accounts to Google Groups 
---------------------------------
Use Google Groups to manager service accounts considering the  
[Official limits to the members of Team Drive](https://support.google.com/a/answer/7338880?hl=en) (Limit for individuals and groups directly added as members: 600).

#### For GSuite Admin
1. Turn on the Directory API following [official steps](https://developers.google.com/admin-sdk/directory/v1/quickstart/python) (save the generated json file to folder `credentials`).

2. Create group for your organization [in the Admin console](https://support.google.com/a/answer/33343?hl=en). After create a group, you will have an address for example`sa@yourdomain.com`.

3. Run `python3 add_to_google_group.py -g sa@yourdomain.com`

_For meaning of above flags, please run `python3 add_to_google_group.py -h`_

#### For Normal User

Create [Google Group](https://groups.google.com/) then add the service accounts as members manually.
Limit is 10 at a time, 100 a day . 

Step 4. Add service accounts or Google Groups into Team Drive 
---------------------------------
_If you do not use Team Drive, just skip._

**Warning:** It is **NOT** recommended to use service accounts to clone "to" folders that are not in teamdrives, SA work best for teamdrives. 

If you have already created Google Groups (**Step 2**) to manager your service accounts, add the group address `sa@yourdomain.com` or `sa@googlegroups.com` to your source Team Drive (tdsrc) and destination Team Drive (tddst).
 
Otherwise, add service accounts directly into Team Drive.
> Enable the Drive API in [Python Quickstart](https://developers.google.com/drive/api/v3/quickstart/python) 
and save the `credentials.json` into project root path if you have not done it in **Step 2**.

> - Add service accounts into your source Team Drive:

```
python3 add_to_team_drive.py -d SharedTeamDriveSrcID
```

> - Add service accounts into your destination Team Drive:

```
python3 add_to_team_drive.py -d SharedTeamDriveDstID
```

Step 5. Start your task
---------------------------------
Let us copy hundreds of TB resource using service accounts. 

#### For server side copy
- [x] publicly shared folder to Team Drive
- [x] Team Drive to Team Drive
- [ ] publicly shared folder to publicly shared folder (with write privilege)
- [ ] Team Drive to publicly shared folder
```
python3 rclone_sa_magic.py -s SourceID -d DestinationID -dp DestinationPathName -b 1 -e 100
```
- _For meaning of above flags, please run python3 rclone_sa_magic.py -h_

- _Add `--disable_list_r` if `rclone` [cannot read all contents of public shared folder](https://forum.rclone.org/t/rclone-cannot-see-all-files-folder-in-public-shared-folder/12351)._

- _Please make sure the Rclone can read your source and destination directory. Check it using `rclone size`:_

1. ```rclone --config rclone.conf size --disable ListR src001:```

2. ```rclone --config rclone.conf size --disable ListR dst001:```

#### For local to Google Drive (BETA)
- [x] local to Team Drive
- [ ] local to private folder
- [ ] private folder to any 
```
python3 rclone_sa_magic.py -sp YourLocalPath -d DestinationID -dp DestinationPathName -b 1 -e 100
```

* Run command `tail -f log_rclone.txt` to see what happens in details (linux only).
 
FAQ :-
---------------------------------

#### 1. What is Dst001 and SA001 ?

Dst001 is a SA001 and it goes to up whatever number SA you created at intial setup.

AutoRClone has default setting to retry process up to 3 times to determine if the process have already finished or not. It also means to verify the consistency of any files.

So, if you were trying to copy 3 TB of files, AutoRClone will use Dst001 to Dst005 and Dst006-007 to verify all the initial process.

You can simply choose the SA or dst by select any number you want in -b flag. Eg. -b 77 -e 100. Means you start from SA 77 to SA 100.

#### 2. What about skipping files ?

I've been copying PBs of data using AutoRClone and I'll tell you this, 99% it won't skip files. 

Most people thought AutoRClone skipped files because they don't know that the source they were trying to copy from is a bloat! Too many duplication, to be precise.

AutoRClone won't copy the same file that match the MD5 number. Hence, it creates an illusion that AutoRClone copies the less number that it should've been which is not, in this case.

AutoRClone sometimes creates multiple duplication files indeed but not skip files as my memory serves. The only major flaw I've ever encountered is it's pretty damn slow! You can alter the default setting, though if you want to gain some speed.