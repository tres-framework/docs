# Changing file permissions

## Shared hosts
Shared hosts should give you the option to connect through (S)FTP. In this
manual, you will see how you can CHMOD with FileZilla.

### FileZilla
1. Connect to your host.
2. Go to the desired file/directory to CHMOD.
3. Right click on the file/directory and click on "File permissions".
4. You should now be able to grant permissions.
   In case of Tres Framework, `/app/storage/` must be CHMOD'ed to 0777. You
   also have to check "Recurse into subdirectories" and "Apply to directories
   only", so its subdirectories get the same permissions.

![Changing permissions in FileZilla](https://i.imgur.com/9OCZMjZ.png)

## VPS

If you are using a VPS, you have a lot more flexibility. You can follow the
same instructions for shared hosts if you wish, or do it through your terminal.

### SSH through terminal
*Windows users can use [PuTTY][putty]. Linux and Mac users may use their
built-in terminal.*

*Warning: You should know how to do this by yourself, otherwise it is
recommended to stick with FileZilla.*

Here is an example for Debian-based Linux distributions, but note that your
actions might differ:

```bash
# Connect to the server using username@domain
ssh john@example.com

# Change current working directory
cd /home/john/www/example.com/app/storage/

# Check the current permissions
ls -l

# Check once again that the current working directory really is the directory
# you want to CHMOD
pwd

# CHMOD the current working directory and all of its sub-directories.
sudo find . -type d -exec chmod 0777 {} +

# Check the new permissions
ls -l

# Quit SSH session
exit
```

[putty]: http://www.putty.org/
