# Swift_TransportException Connection could not be established

The problem has occured when send mail from kyo server using Gmail server.

> Swift_TransportException Connection could not be established with host smtp.gmail.com [Permission denied #13]

## Causes

This problem is caused by SELinux. So we will run some command for accepting httpd can open socket and send mail to outside destination.

## Solution

To allow for HTTPD (i.e. PHP) to make Network Socket connections

`setsebool -P httpd_can_network_connect on`

To allow for HTTPD (i.e. PHP) to send out mail.

`setsebool -P httpd_can_sendmail on`
