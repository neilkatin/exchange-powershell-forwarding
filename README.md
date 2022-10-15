Control up outlook 365 forwarding in powershell
===============================================

It is possible for exchange admins to turn off the UI to control mail forwarding.

If the admins also disable forwarding to an external account: everyone who sends to
your account will get an error (even though the email is delivered to the local
account).

This document describes how to control mail forwarding via powershell as an
alternate way to control your forwarding settings.

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
