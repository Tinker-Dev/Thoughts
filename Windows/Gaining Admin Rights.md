# Gaining Admin Rights

The method below will should work it you have at least some Power User Rights. If you don’t already have that much then you are looking at an up hill battle.  

  

## The “psexec” Method

- Going this route will need an application from the System Internals package. Now that pack is a pain to download these days. However you can still get what you need. They moved the System Internals items to a “Live” version so you can download it that way.
    
- Try getting it from here: [https://live.sysinternals.com/PsExec.exe](https://live.sysinternals.com/PsExec.exe)
    
- If you were able to download it then you can proceed forward otherwise you are out of luck and will have to try another method.
    
- Just run the following command:
    

  

```
psexec –i –s CMD

```

  

Then run this command to see if you succeeded:  

```
whoami

```

- If all well you will see "nt authority\\system32"