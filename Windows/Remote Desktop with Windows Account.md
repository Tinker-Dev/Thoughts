# Remote Desktop with Windows Account

When you are going to login to a machine with RDP that is using a Windows Account you will find that it won’t let you in. Outside of performing the normal process for setting up RDP you have to complete one more step.

  

Open and Command Prompt and enter your variation of this.

```
runas /u:MicrosoftAccount\your@email.com cmd.exe

```

  

This will tie and alias of sorts to the account on the machine that will allow things to work.