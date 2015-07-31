# ssh-keyrot

SSH-KeyRot is a program that will rotate ssh authentication keys on local and remote systems.
The purpose is to defeat the effectiveness of stolen keys.

We recommend that you rotate your ssh keys daily, preferably automated with cron.
We also recommend that you have one keypair per each user at each host, identified as ~/.ssh/id_dsa-user@host

If this is your first time running ssh-keyrot, you may be prompted for the remote user's password 5 times.  An alternative is to use the --pwfile option with a file that contains a list of possible passwords.  Once initialized, you may rotate the key non-interactively as often as you like.
Before you begin, please read the help screen by executing 'ssh-keyrot --help'

How the key rotation works:
1- Check for authorized_keys lockfile on remote server.  If a lockfile exists, ssh-keyrot will try again a few times at 20-40 second wait intervals.  Ssh-keyrot will send an email and exit if the lockfile remains.
2- Ssh-keyrot will create a lockfile on the remote server, containing information such as the originating user, host, and pid.
3- Generate new keys
4- Pull authorized_keys from remote host
5- Put new key in the remote's authorized_keys file
6- Push the new authorized_keys file to the remote server
7- Re-pull the authorized_keys from the remote server.  If this fails, ssh-keyrot will send an email and exit, leaving the current local keys untouched.
8- Remove any keys from the remote's authorized_keys that matches the originating user & host, ignoring the current keyrot timestamp tag.  This is identified by the identity field of the public key
9- Replace the current keys on the originating server.  Backups will be made with a '.old' extension.



How to use ssh-keyrot
Anything starting with * is required.
If you are running this for the very first time, they you will have to type the password several times.
  --bits=bits                Number of bits in the key to create.
* --localkey=filename        FULL PATH to the ssh private key file.
  --type={dsa|rsa}           Specify type of key to create.  Options are rsa or dsa.
  --comment="comment"        Provide comment that will be appended to the public key.
* --remoteuser=username      Log in using this user name.
  --sshconfig=filename       Config file (default: ~/.ssh/config).
  --port=port                Connect to non-standard port.  Server must be on the same port.
  --option="option"          Process the option as if it was read from a configuration file.
* --mail="email"             Email all errors to specified recepient.
* --targetfile="remote file" FULL PATHNAME to the authorized keys file on the target system.
  --pwfile="password file"   Contains list of possible passwords to attempt if keys are not setup yet.
  --debug                    debug
  --keyoptions="options"     Options for the authorized key file.
* --host=host                Host. 
  --with-ssh=                Specify the fullpath of ssh executable.  Default is /usr/bin/ssh
  --with-scp=                Specify the fullpath of scp executable.  Default is /usr/bin/scp
  --with-ssh-keygen=         Specify the fullpath of ssh-keygen executable.  Default is /usr/bin/ssh-keygen
  --help                     Show ssh-keyrot usage and exit

example: ssh-keyrot --type=dsa --remoteuser=apache  --mail='emailalerts@domain.com' --targetfile=/home/apache/.ssh/authorized_keys --host=webserver01 --localkey=/home/user/.ssh/id_dsa-apache_webserver01



