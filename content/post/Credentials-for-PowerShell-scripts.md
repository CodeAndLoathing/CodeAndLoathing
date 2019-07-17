---
title: "PowerShell Automation and Credentials"
date: 2019-05-18T12:05:20-08:00
tags: ["O365", "PowerShell", "Auth"]
---

## Credentials in PowerShell 

[PSCredentials](https://docs.microsoft.com/en-us/dotnet/api/system.management.automation.pscredential) objects are widely used in PowerShell by both built-in and 3rd party cmdlets that require credentials as an argument.

To create a PSCredential object interactively we can use the [Get-Credential](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.security/get-credential?view=powershell-6) cmdlet.

For example, we could log into Azure AD using the following snippet which will prompt you for the username and password to use.
```
    $Credential = Get-Credential
    Connect-AzureAD -Credential $Credential
```

## Non-interactive creation of PSCredential

But an interactive prompt is not helpful if we want to automate something since the script will just hang waiting for input. So how do we create a PSCredential object by script? 

Well, we can use the PSCredential constructor. It takes a SecureString for the password argument and not a plaintext string.

```powershell
    $Username = "serviceAccount@codeandloathing.com"
    $Password = ConvertTo-SecureString -AsPlainText "mySecretPassword333" -Force
    $Credential = New-Object System.Management.Automation.PSCredential ($Username, $Password)
```
While this works - it is only as secure as however tightly you've buttoned down read access to your script file since you would have the password sitting right there in plaintext.

Note that you can easily retrieve the plaintext password from the PSCredential object if running in the same PowerShell session using the provided GetNetworkCredential method:
```powershell
    $Credential.GetNetworkCredential().Password
    mySecretPassword333
```

Also note that Microsoft generally recommends [moving away](https://github.com/dotnet/platform-compat/blob/master/docs/DE0001.md) from SecureString as they continue on their transistion of .NET to CORE. 

## Encrypt and persist Credentials

We can marginally increase the security of our above solution by storing the credentials encrypted instead of in plaintext.

A SecureString can be converted into an encrypted string using PowerShell cmdlet [ConvertFrom-SecureString](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.security/convertfrom-securestring?view=powershell-6) and back again using [ConvertTo-SecureString](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.security/convertto-securestring?view=powershell-6)

Here's an exampe of how we could take a set of known credentials, encrypt them, and save them out to a file for later use.

```powershell
    $Username = "serviceAccount@codeandloathing.com"
    $Password = ConvertTo-SecureString -AsPlainText "mySecretPassword333" -Force


    $EncryptedCredentials = @{
     "Username" = "serviceAccount@codeandloathing.com"
      "Password" = ($Password | ConvertFrom-SecureString)
    }

    $EncryptedCredentials | ConvertTo-Json | Out-File ".\creds.json"
```

If you look inside the creds.json file that was created you'll see something like the following with the password stored in an encrypted format:
```json
{
    "Password":  "01000000d08c9ddf0115d1118c7a00c04fc297eb0100000056fdf10ebc97c74083012e637d2e45e400000000020000000000106600000001000020000000220e823f388dd8450809004ef0ef2bd3e3260ed8f24f18aceeab63c73c9e88e3000000000e8000000002000020000000be1c5c1445ce7c4aa9f953f1fd8569f15ef1613da19ca128c21d0a73e8b2bd0230000000394ea56efe09ba74963aa1d80a42f8d3995303f3ebba13559775d2af6fe28520c4ad8713571e590236b2010855820bc3400000004e2b42bf083b46d31824967d9c5fa653a5cf13f9556a3d3e6fa2b953e4b35bc9c2146eb31a64ab0c87b0f83cc87c59586a25cf66c2cc225edbd5da2ecd707d46",
    "Username":  "serviceAccount@codeandloathing.com"
}
```

Since we did not specify a key as an argument on ConvertFrom-SecureString, it has used the Windows Data Protection API (DPAPI) to encrypt the value to *both* the **user** and the **machine** it was running as/on when created. If you attempt to decrypt running as a different user, or from a different machine, it will fail to decrypt. This is kind of nice because it makes it a little harder for folks to get your password even if they can read your script file but comes with the nuisance that each time you move your script to a new machine or user you have to recreate your credential file.

And this is how we could convert the encrypted credentials back to a PSCredential object

```powershell
    $EncryptedCredentials = Get-Content ".\creds.json" | ConvertFrom-Json
    $Credentials = New-Object System.Management.Automation.PSCredential (
        $EncryptedCredentials.Username,
        ($EncryptedCredentials.Password | ConvertTo-SecureString)
    )
```

If you have to share the encrypted credential file with other users then you would have to specify a key youself instead of relying on DPAPI for handling encryption and decryption.