# FAQ

## How do I get Reporting?

Please see the [Reporting FAQ](./ee/reporting/functions/faq.md)

## I just updated and now it doesn't work. 

One or more of these things probably just happened:

1. You didn't read the [update instructions](update_server.md). They have been known to change. Frequently. And you're probably [not staying updated](install_server.md#update-regularly) with versions as they come out.
2. You didn't review ALL the pertinent release notes between your version, and the [latest one](https://github.com/amidaware/tacticalrmm/releases).
3. You said `Yes` to nginx.conf change, when you should have said [No](https://github.com/amidaware/tacticalrmm/releases/tag/v0.14.3).

To fix number 3, edit `/etc/nginx/nginx.conf` and make it:

```conf
worker_rlimit_nofile 1000000;
user www-data;
worker_processes auto;
pid /run/nginx.pid;
include /etc/nginx/modules-enabled/*.conf;
events {
        worker_connections 4096;
}
http {
        sendfile on;
        tcp_nopush on;
        types_hash_max_size 2048;
        server_names_hash_bucket_size 64;
        include /etc/nginx/mime.types;
        default_type application/octet-stream;
        ssl_protocols TLSv1.2 TLSv1.3;
        ssl_prefer_server_ciphers on;
        access_log /var/log/nginx/access.log;
        error_log /var/log/nginx/error.log;
        gzip on;
        include /etc/nginx/conf.d/*.conf;
        include /etc/nginx/sites-enabled/*;
}
```

## Is Tactical RMM vulnerable to Log4j

No.

## Can I __________?

If you've been sent a link to this, we are not going to allow or support things like your request. The reason is because it would facilitate people avoiding to pay for/supporting Tactical RMM for these/similar premium paid features.

So the answer is No.

## I'd like to be able to __________?

If you've been sent a link to this, it's possible that we could support this but it would most likely be a paid premium feature. Please contact [Commercial Support](support.md#commercial-support) to discuss further.

## Why isn't the Code Signing free?

It is recommended because it helps the project mature, Tactical is source available and free. Community support is also free on top of this we have spent a lot of time developing the docs, please follow them! 
With many source available (and similar) projects devs get bored of them because they don't make money out of it. So here is your reasons to pay for code signing and also why it was discussed and implemented. 

1. Code signing costs a lot of money. OV code signing requires a legitimate legal business...Amidaware was setup for this purpose. Code signing + operating a business costs thousands of dollars a year.
2. It helps the project move forward and it can support devs spending time on it, they have lives, wives, jobs and kids which all demands attention.
3. It should stop bad actors using it maliciously.
4. It helps with AVs detecting it as anything malicious.

We had github sponsors up for many months before code signing. Very few people donated, some $5 and $10. maybe $40 a month. Once we announced code signing, sponsors came in like crazy, and many people upgraded their $5 to $55 so whilst everyone believes people would gladly donate, that's just not the case. We already tried.

## Linux/macOS Agents

**Why do I see?**

```
Missing code signing token
400: Bad Request
```

You must have a [paid code signing certificate](code_signing.md) while Linux/macOS support is in the post-alpha/beta:

- Code signing makes these installs easy and is a benefit offered to code signing sponsors. [So become a sponsor and enjoy the easy life](sponsor.md)
- DIYer can read thru the code and... DIY.

This is primarily for 2 reasons: 

1. As this has been a sponsorship goal it seems only fair that those who contributed to make this a reality get early access to easy agent installs.
2. We're looking for good bug reports from active users to get these agent into production ready code. 

## Who is Amidaware Inc?

The Legal entity behind Tactical RMM.

## Is it possible to use XXX with Tactical RMM

While it _may be possible_ to use XXX, we have not configured it and therefore it is [Unsupported](unsupported_guidelines.md). We cannot help you configure XXX as it pertains to **your environment**.

## Is it possible to use XXX proxy server with Tactical RMM

If you wish to stray from the [easy install](install_server.md#option-1-easy-install-on-a-vps) of a standard install in a VPS, you need to have the knowledge on how to troubleshoot your own custom environment.

The most common reasons you're running a proxy are:

1. Because you only have a single public IP and you already have something on Port 443. **Workaround**: Get another public IP from your ISP.
2. Because you want to monitor traffic for security reasons: You're a [Networking Wizard](unsupported_guidelines.md).

There are some [implementations](unsupported_scripts.md) that others have done, but be aware it is [Unsupported](unsupported_guidelines.md) and if you're requesting help in Discord please let us know in advance.

## How do I do X feature in the web UI?

A lot of features in the web UI are hidden behind right-click menus. Almost everything has a right click menu so if you don't see something, try right clicking on it.

## Can I run Tactical RMM locally behind NAT **without** exposing my RMM server to the internet?

Yes, you will just need to setup local DNS for the 3 subdomains, either by editing host files on all your agents or through a local DNS server.

Similarly asked: Can I use onsite DNS servers (I don’t want my server accessible from the internet).

Yes, you can use (only) internal DNS (if you want) for api, mesh and rmm domains. You don't have to put these records in your public DNS servers.

The Let's Encrypt DNS `TXT` wildcard cert request process **does not** require any inbound connection from the internet (port forwarding etc) to be enabled. This does not expose your RMM server to the internet in any way.

## I forgot my username to login to the web UI, how can I find it?

Do the following as the `tactical` user which will list all user accounts:

```bash
tactical@trmm:~$ /rmm/api/env/bin/python /rmm/api/tacticalrmm/manage.py shell
Python 3.11.3 (main, Apr  9 2023, 04:41:05) [GCC 10.2.1 20210110] on linux
Type "help", "copyright", "credits" or "license" for more information.
(InteractiveConsole)
>>> from accounts.models import User
>>> User.objects.exclude(is_installer_user=True).filter(agent__isnull=True)
```

## How do I make another superuser for logging into `rmm.`

```sh
cd /rmm/api/tacticalrmm/
source ../env/bin/activate
python manage.py createsuperuser
```

## I am locked out of the web UI. How do I reset my password?

SSH into your server and run:

```bash
/rmm/api/env/bin/python /rmm/api/tacticalrmm/manage.py reset_password <username>
```

## How do I reset password or 2 factor token?

### From TRMM Admin GUI

From the web UI, click **Settings > User Administration** and then right-click on a user:
![reset2fa](images/reset2fa.png)

### From SSH

Login with SSH using your install ID (eg `tactical`)

and [Reset Password](management_cmds.md#reset-a-users-password)

**OR**

???+ note "Reset 2FA token for a TRMM user"

    === ":material-ubuntu: standard"

        ```bash
        /rmm/api/env/bin/python /rmm/api/tacticalrmm/manage.py reset_2fa <username>
        ```
    === ":material-docker: docker"
        ```bash
        docker exec -it trmm-backend /bin/bash
        ```

Then simply log out of the web UI and next time the user logs in they will be redirected to the 2FA setup page which will present a barcode to be scanned with the Authenticator app.

## How do I recover my MeshCentral login credentials?

From Tactical's web UI: **Settings > Global Settings > MeshCentral**

Copy the username, then ssh into the server and run:

```bash
cd /meshcentral/
sudo systemctl stop meshcentral.service
node node_modules/meshcentral --resetaccount <username> --pass <newpassword>
sudo systemctl start meshcentral.service
```

to reset Mesh password for user.

!!!warning
    Resetting the default admin in mesh will break agent installs if you don't also update the mesh connection into in Settings > General > Meshcentral and make sure [it's working](troubleshooting.md#agents-not-installing-or-updating)

## Help! I've been hacked and there are weird agents appearing in my Tactical RMM!

No, you haven't.

1. Your installer was scanned by an antivirus.

2. It didn't recognize the exe.

3. You have the option enabled to submit unknown applications for analysis.

    ![AV Option1](images/faq_av_option1.png)

4. They ran it against their virtualization testing cluster.

5. You allow anyone to connect to your rmm server (you should look into techniques to hide your server from the internet).

6. Here are some examples of what that looks like.

![AV Sandbox1](images/faq_av_sandbox1.png)

![AV Sandbox1](images/faq_av_sandbox2.png)

![AV Sandbox1](images/faq_av_sandbox3.png)

![AV Sandbox1](images/faq_av_sandbox4.png)

## DNS can't find record

Q. My DNS isn’t working.

A. Make sure it’s correctly formatted, as most DNS providers add in the domain automatically.

![DNS Examples](images/trmmdnsexample.png)

## License FAQ

If you're not certain if your business use case is allowed by the license please open a [ticket](https://support.amidaware.com) and let us know how you're planning on using it.

## Can I password protect the uninstalling of the TRMM agent?

From the client / agent side: Installing and uninstalling software is part of system administration. Administrators can install / uninstall. Users cannot. Configure your system appropriately.

From the TRMM Admin panel: Use `Permissions Manager` to restrict your techs permissions.

## I want to move and/or migrate my server? How do I do it?

### Changing a public IP or private LAN IP address? 

TRMM doesn't care about that. TRMM uses DNS, and only the 3 configured DNS names used during install. Make them right, and you'll be fine (with a backup/restore script if necessary)

### Are you keeping the same DNS names?

Use the [backup](backup.md) and [restore](restore.md) process. 

!!!note
    It's best practice to make sure you're running the latest version before running the backup script, and make sure you have the latest backup script. It does change.

### Are you wanting to change the DNS names on the server?

We [warned you](install_considerations.md#install-considerations) you should choose the right DNS names during install. There is no supported way to do this because of the complexities involved. We do offer a paid migration service, please [open a ticket](https://support.amidaware.com) for pricing.

If you want to try it yourself, your best bet is to use another service outside of TRMM to uninstall and reinstall your agents to a new TRMM server. Then you'll need to migrate all your automation policies, alert policies, patch policies, custom scripts, reports, users, user permissions, user settings, custom fields, api keys, key store items, email configuration, sms configuration etc from your old TRMM server to the new one manually.

## If you received this link

You have asked for assistance in Discord or Github, and you have not provided enough [information](https://discord.com/channels/736478043522072608/921501747648811038/936258143380443226).

Please provide the following information by submitting [this](https://docs.google.com/forms/d/e/1FAIpQLScfgZDGrdrCFgPTz5TIBEvrflkHPZe3S0QvTN4OC7_Hnf74tg/viewform), or copy/pasting this text and answering the questions:

```
Install type (Standard/Docker) and amount of RAM on server?
Did you deviate IN ANY WAY from these Standard server install instructions https://docs.tacticalrmm.com/install_server/ ?
Did you deviate IN ANY WAY from these Docker server install instructions https://docs.tacticalrmm.com/install_docker/ ?
Where is the server (VPS/onprem)?
New/old install? Rough age of TRMM server (days/weeks/months)?

Server Install Specific questions:
What OS/version is TRMM installed on.
Using a real domain?
Did letsencrypt finalize and work?
Have you looked at the troubleshooting steps to see if something there is appropriate to you situation? Test DNS from an agent etc. https://docs.tacticalrmm.com/troubleshooting/
Run the Server Troubleshooting Script and DM the person (from @Support) that is working with you https://docs.tacticalrmm.com/troubleshooting/#server-troubleshooting-script
What kind of ssl certs? Let's Encrypt, or purchased.
Check Expiry date of your certificates in the browser (at https://rmm.example.com/ )

Network Troubleshooting
Are you using a proxy?
Are you a wizard? See https://docs.tacticalrmm.com/unsupported_guidelines/
If so, what's in the network between agent and server?

Agent Troubleshooting
Is there ANY 3rd party Antivirus installed on the computer?
Is there any network based filtering/AV filtering?

Asking yourself questions like: When did it go from working to broken? What changed during that time? This will help you. 
```

We can not help you until we understand your setup. Just posting an error log is not enough information.

You can help yourself by running [this](troubleshooting.md#server-troubleshooting-script) and [this](troubleshooting.md#agents-not-installing-or-updating) that will test your server and and tell you common problems.

If you would like us to do it for you please [contact support](support.md#commercial-support).
