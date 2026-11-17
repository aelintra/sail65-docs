# Upgrade 

SARKV6.5  upgrades are handled via migration.   In other words, you create a new bare server install of SARK V6.5 and then load a backup from a previous version.   This will usually work without issue for all V6.x releases.  It *may* work for prevous V5.x releases but YMMV.   

It's important to note that V6.5 uses the PJSIP module in Asterisk and this may have implications if you have any custom apps (or your own custom code) which, for example, dial *SIP*/. You should examine your custom apps to ensure that any such occurrences are updated to dial *PJSIP/* instead.

##Step 1 

Create a new instance according to the Installation section.   Once you have your new instance and can login to it you are ready to migrate the old system forward.  

*Top Tip* - Assuming you are working with a VM instance (and you should be for testing), take a snapshot of your clean-install system before proceeding.  This will allow you to quickly recover in case of accidents later in the process.  

##Step 2 

Download a recent backup from your old system to your workstation.   You will use this to create the new system

##Step 3

Login to the new system and navigate to *Settings->Backup/Restore*.   Click the *Upload* button at the top right of the panel to load your backup to the new system.  The backup will appear in the *Saved Backups* list.   Click the restore icon for that backup set.  Restore the following sections 

* Sark database
* Voicemail
* usergreetings
* LDAP (if required)

**Do NOT restore the Asterisk folder**

## Step 4

Issue a Commit 

**N.B.** Backups do not contain firewall rules so you will need to recreate them on the new instance.

Once you have set the firewall to your taste, reboot the instance,

You now should have your new 6.5 system ready to test.