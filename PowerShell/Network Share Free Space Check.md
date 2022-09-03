# Network Share Free Space Check

```
"{0:N2}" -f ((Get-ChildItem -path \\QNAP1\dbshare1\Backups -recurse | Measure-Object -property length -sum ).sum /1TB) + " TB -- Used on QNAP 1"

"{0:N2}" -f ((Get-ChildItem -path \\QNAP2\dbshare2\Backups -recurse | Measure-Object -property length -sum ).sum /1TB) + " TB -- Used on QNAP 2"


# Debugging Line
Read-Host -Prompt "Press Enter to continue"

```