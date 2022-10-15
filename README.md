Control up outlook 365 forwarding in powershell
===============================================

It is possible for exchange admins to turn off the UI to control mail forwarding.

If the admins also disable forwarding to an external account: everyone who sends to
your account will get an error (even though the email is delivered to the local
account).

This document describes how to control mail forwarding via powershell as an
alternate way to control your forwarding settings.

The below commands need to be run in windows powershell.  This is pre-installed
on windows 10 and beyond.  To open a powershell window:

* run it directly: hold "windows" key and press R, type "powershell" in the text field, and hit enter.
  You should have a new powershell window open
* or: open the start menu, type "powershell" (without the quotes), and click on "Windows Powershell"

If you are on a mac: you can install powershell via `brew install --cask powershell`.
Then run powershell by running `pwsh` in a terminal window.
If that is meaningless gibberish to you: you might not be a good candidate for this
procedure.  But if you are feeling adventurous:
[here is a more detailed set of powershell installation instructions](https://learn.microsoft.com/en-us/powershell/scripting/install/installing-powershell-on-macos?view=powershell-7.2)

``` powershell
# step 1: set your email address on this line
$username = "email@domain"

# step 2: install the exchange online powershell module
Install-Module ExchangeOnlineManagement -MinimumVersion 3.0.0

# step 3: do the login dance, including any 2-factor auth.  You will
# get redirected to a web browser to complete the authorization
# process
Connect-ExchangeOnline -UserPrincipalName $username

# step 4: simple test to make sure the login worked.  You should see
# a title row with Name, Alias, Database, etc, and a data
# row with your account info
Get-Mailbox -Identity $username

# step 5: show your current forwarding status.
Get-Mailbox $username | FL DeliverToMailboxAndForward,ForwardingAddress,ForwardingSmtpAddress

# step 6a: turn forwarding off
Set-Mailbox $username -ForwardingSmtpAddress $Null

# step 6b: or instead: turn forwarding on, while leaving a copy of the message
# in your mailbox (the safest option).  If you set to $False instead
# the message will be forwarded, but not sent to your local mailbox
set-mailbox $username -DeliverToMailboxAndForward $True
Set-Mailbox $username -ForwardingSmtpAddress PUT-YOUR-EXTERNAL-EMAIL-ADDRESS-HERE

# step 7: optional, but show final forwarding settings (same cammand as step 5)
Get-Mailbox $username | FL DeliverToMailboxAndForward,ForwardingAddress,ForwardingSmtpAddress
```
