## Cannot start Apache server



```bash

Starting Apache Web Server...
Exit code: 8
Stdout:
apache config test fails, aborting
Stderr:
/opt/lampp/bin/httpd: error while loading shared libraries: libcrypt.so.1: cannot open shared object file: No such file or directory
Starting Apache Web Server...
Exit code: 8
Stdout:
apache config test fails, aborting
Stderr:
/opt/lampp/bin/httpd: error while loading shared libraries: libcrypt.so.1: cannot open shared object file: No such file or directory
Starting all servers...
Starting MySQL Database...

Starting Apache Web Server...
Exit code: 8
Stdout:
apache config test fails, aborting
Stderr:
/opt/lampp/bin/httpd: error while loading shared libraries: libcrypt.so.1: cannot open shared object file: No such file or directory
Starting ProFTPD...
Exit code: 8
Stdout:
proftpd config test fails, aborting
Stderr:
/opt/lampp/sbin/proftpd: error while loading shared libraries: libcrypt.so.1: cannot open shared object file: No such file or directory

```


### Solution

sudo dnf install libxcrypt-compat





---

## Apache server and mysql not working 

```bash
Starting Apache Web Server...
Exit code: 8
Stdout:
apache config test fails, aborting
Stderr:
/opt/lampp/bin/httpd: error while loading shared libraries: libcrypt.so.1: cannot open shared object file: No such file or directory
Starting Apache Web Server...
Exit code: 8
Stdout:
apache config test fails, aborting
Stderr:
/opt/lampp/bin/httpd: error while loading shared libraries: libcrypt.so.1: cannot open shared object file: No such file or directory
Starting all servers...
Starting MySQL Database...

Starting Apache Web Server...
Exit code: 8
Stdout:
apache config test fails, aborting
Stderr:
/opt/lampp/bin/httpd: error while loading shared libraries: libcrypt.so.1: cannot open shared object file: No such file or directory
Starting ProFTPD...
Exit code: 8
Stdout:
proftpd config test fails, aborting
Stderr:
/opt/lampp/sbin/proftpd: error while loading shared libraries: libcrypt.so.1: cannot open shared object file: No such file or directory
Stopping all servers...
Restarting all servers...
Starting MySQL Database...

Starting Apache Web Server...
Exit code: 8
Stdout:
apache config test fails, aborting
Stderr:
httpd: Syntax error on line 522 of /opt/lampp/etc/httpd.conf: Syntax error on line 6 of /opt/lampp/etc/extra/httpd-xampp.conf: Cannot load modules/mod_perl.so into server: libnsl.so.1: cannot open shared object file: No such file or directory
Starting ProFTPD...
Checking syntax of configuration file
/opt/lampp/proftpd/scripts/ctl.sh : proftpd started
```

### Solution:


```bash
sudo systemctl stop mariadb
sudo dnf install libxcrypt-compat libnsl
```


---



## http://localhost/openemr/setup.php?site=default might have a temporary problem or it could have moved.


## conflicting pdf issue:


Perfect! Now I can see the exact issue. The problem is a **class conflict with the Cpdf class**. OpenEMR has its own PDF library, but XAMPP also includes a Cpdf.php file that's being loaded first, causing a "Cannot declare class Cpdf, because the name is already in use" fatal error.

Here's how to fix this:

## Solution: Rename or Move the Conflicting XAMPP Cpdf File

```bash
# Backup the conflicting file
sudo mv /opt/lampp/lib/php/Cpdf.php /opt/lampp/lib/php/Cpdf.php.backup

# Or alternatively, rename it
sudo mv /opt/lampp/lib/php/Cpdf.php /opt/lampp/lib/php/Cpdf.php.old
```

## Additional Issues to Fix

I also notice some missing PHP extensions. Let's fix those too:

### 1. Check which extensions are actually available
```bash
ls /opt/lampp/lib/php/extensions/no-debug-non-zts-20220829/ | grep -E "(xml|mbstring|openssl)"
```

### 2. Edit the PHP configuration to fix extension loading
```bash
sudo nano /opt/lampp/etc/php.ini
```

Look for these lines and comment them out (add semicolon at the beginning) if the .so files don't exist:
```ini
;extension=xml
;extension=mbstring  
;extension=openssl
```

Or find the correct extension names:
```ini
extension=libxml
extension=mbstring.so
extension=openssl.so
```

### 3. Restart XAMPP
```bash
sudo /opt/lampp/lampp restart
```

### 4. Test the fix
```bash
curl -I http://localhost/openemr/setup.php
```

You should now get a `200 OK` response instead of `500 Internal Server Error`.

The main issue was the Cpdf class conflict - OpenEMR includes its own PDF generation library, but XAMPP's included Cpdf.php was being loaded first and conflicting with it. By renaming or removing the XAMPP version, OpenEMR can use its own PDF library without conflicts.

Try this fix and let me know if OpenEMR's setup page loads correctly now!

