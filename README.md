# RHCSA Certification Prep 
![image](https://github.com/user-attachments/assets/6a5b629b-3f1d-4cd7-824d-39a0f22b2eb1)

## 1. Essential Tools

## 1. Accessing and Managing the System

### Open a Terminal
- **`Ctrl + Alt + F2`**: Opens a terminal on a Linux system.

### Connect to Remote Systems
- **VNC**: Used to connect to a remote desktop over a network.
- **SSH**: Establishes a secure, encrypted connection to a remote server using OpenSSH.
    - **Command**: `ssh username@hostname` to log in securely.
    - **Debugging SSH**: Use verbose mode to troubleshoot issues: 
        ```bash
        ssh -v username@hostname
        ```

### Check IP Address
- **Command**: `ip a`  
  This command displays your network interfaces and their IP addresses.

---

## 2. Linux Security

### Access Control and Security Mechanisms
- **PAM (Pluggable Authentication Modules)**: A flexible mechanism for authenticating users for services.
- **Network Security**: Managed with `iptables`.
- **SSH Hardening**: Secure SSH configuration.
- **SELinux**: A Linux security module that provides mechanisms for supporting access control security policies.

### User and Group Information
- **User Information**: Stored in `/etc/passwd`.
- **Group Information**: Stored in `/etc/group`.

### User Accounts
- **Superuser**: Has UID 0.
- **System Accounts**: UID ranges from 1 to 999 (depending on the Linux distribution).

### Useful Commands:
- **Check User ID**: 
    ```bash
    id
    ```
- **See Who is Logged In**:
    ```bash
    who
    ```
- **View Last Reboots**: 
    ```bash
    last reboot
    ```

### Switch User
- **Command**: `su username`  
  Switches to another user.

### Configure Sudo Access
The default configuration for `sudo` is stored in the `sudoers` file.  
Reference: [Managing Sudo Access](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/9/html/configuring_basic_system_settings/managing-sudo-access_configuring-basic-system-settings#granting-sudo-access-to-a-user_managing-sudo-access).

If you forget a command:
1. **Update manual pages**: `sudo mandb`
2. **Search for a command**: `apropos keyword`

---

## 3. File and Directory Management

### Hard & soft Links

- **Check Link Count**: 
    ```bash
    stat filename
    ```
Hard Link: A direct reference to the same inode on the disk. Both the original file and its hard link share the same inode number and data blocks.
Soft Link (Symbolic Link): A reference or pointer to another file's path. It has its own inode and points to the target file by path.
Practical Example:
Creating a Hard Link: Let's create a file named original.txt and a hard link to it called hard_link.txt.


### Step 1: Create the original file
    ```echo "This is the original file." > original.txt    ```

# Step 2: Create a hard link
    ```ln original.txt hard_link.txt    ```
Explanation:
Both original.txt and hard_link.txt share the same inode and data blocks. They are essentially the same file with two different names.

If you modify one, the changes will reflect in the other.
If you delete one of the files, the other remains and the data is not lost.

# Check inode numbers for verification
    ```ls -li original.txt hard_link.txt    ```
You should see the same inode number for both files.

Creating a Soft Link (Symbolic Link): Now, let’s create a symbolic link named soft_link.txt that points to original.txt.

bash
Copier le code
### Step 1: Create a symbolic link
    ```ln -s original.txt soft_link.txt    ```
Explanation:

soft_link.txt acts as a pointer to original.txt. It does not share the same inode number or data blocks.
If you modify original.txt, the changes are reflected when accessing soft_link.txt because it points to the same file.
If you delete original.txt, the soft_link.txt becomes a broken link and points to a non-existent file.

# Check inode numbers for verification
    ```ls -li original.txt soft_link.txt    ```
You will see different inode numbers for original.txt and soft_link.txt.
  **Considerations**:
  - Only files on the same partition can be hard-linked.

### Copying Files with Attributes
- **Preserve File Attributes**:
    ```bash
    cp -p source_file destination_file
    ```

### Change Group and Ownership
- **Change Group**:
    ```bash
    chgrp group_name file
    ```
- **Change Owner**:
    ```bash
    chown username file
    ```
- **Change Owner and Group**:
    ```bash
    chown username:group file
    ```

---

## 4. Permissions Management

### Basic Permissions
- **Change Permissions**: 
    ```bash
    chmod u-rwx file  # Remove permissions for user
    chmod g+rw file   # Add read-write for group
    chmod o-r file    # Remove read for others
    ```
- **Using Octal Notation**:
    - Read = 4, Write = 2, Execute = 1
    - Example:
        - `rwx` = 7, `rw-` = 6, `r--` = 4
        ```bash
        chmod 755 file  # rwxr-xr-x
        ```

### Special Permissions

- **SUID (Set User ID)**: When set on an executable file, the file runs with the permissions of the file owner (usually root), not the user who executed it.
𝘾𝙤𝙢𝙢𝙖𝙣𝙙:  ``` 𝙘𝙝𝙢𝙤𝙙 4755 𝙛𝙞𝙡𝙚𝙣𝙖𝙢𝙚 𝙊𝙍 𝗰𝗵𝗺𝗼𝗱 𝘂+𝘀 𝗳𝗶𝗹𝗲𝗻𝗮𝗺𝗲 ```
𝙀𝙭𝙥𝙡𝙖𝙣𝙖𝙩𝙞𝙤𝙣: The "4" enables the SUID bit, and "755" is the standard permission set (owner: read, write, execute; group/others: read, execute).
- **SGID (Set Group ID)**: When set on a directory, files created inside inherit the group ownership of the directory.
𝗖𝗼𝗺𝗺𝗮𝗻𝗱: ```𝗰𝗵𝗺𝗼𝗱 2755 𝗳𝗶𝗹𝗲𝗻𝗮𝗺𝗲 𝗢𝗥 𝗰𝗵𝗺𝗼𝗱 𝗴+𝘀 𝗳𝗶𝗹𝗲𝗻𝗮𝗺𝗲```
𝗘𝘅𝗽𝗹𝗮𝗻𝗮𝘁𝗶𝗼𝗻: The "2" sets the SGID bit, and "755" is the permission mode (owner: read, write, execute; group/others: read, execute).
- **Sticky Bit**: Typically set on directories like `/tmp`, it ensures only the file owner (or root) can delete or modify the files, even if others have write permissions. 
𝗖𝗼𝗺𝗺𝗮𝗻𝗱:``` 𝗰𝗵𝗺𝗼𝗱 1777 𝗱𝗶𝗿𝗲𝗰𝘁𝗼𝗿𝘆𝗻𝗮𝗺𝗲 𝗢𝗥 𝗰𝗵𝗺𝗼𝗱 +𝘁 𝗱𝗶𝗿𝗲𝗰𝘁𝗼𝗿𝘆𝗻𝗮𝗺𝗲```
𝗘𝘅𝗽𝗹𝗮𝗻𝗮𝘁𝗶𝗼𝗻: The "1" sets the sticky bit, and "777" gives full permissions (read, write, execute) to everyone, but only owners can delete their files.

---

## 5. Searching Files

### Find Files by Name
- **Find by Name**: 
    ```bash
    find /path -name "filename"
    ```
- **Find by Size**:
    ```bash
    find /path -size 512k
    ```
- **Find Modified in Last X Minutes**:
    ```bash
    find /path -mmin -5   # Files modified in the last 5 minutes
   find -mmine 5 #(find modifed file in minute 5)
    find -mmine +5 #(find more than last 5 mins ago)
    ```

### Find by Permissions
- **Find Files with Specific Permissions**:
    ```bash
    find /path -perm 777
    ```

### Example:
Find all directories named `pets` under `/var` and save the output to a file:
```bash
sudo find /var/ -type d -name pets > /home/bob/pets.txt
```
**PS**: you can find -name -size (acts like AND)
add -o flag: find -name -o -size (acts like OR)
use -not flag or use ! but **PS**: ! is special caracter so you need to skip it to consider it a NOT like this \!
---
inverse example : find -perm -100 (find who is user can execute at least and group and other are anying)

find \! -perm -o=r (find who others can read)

----
In the `find` command, the `-perm` option is used to search for files based on their permission settings. The use of a slash (`/`) in the permission specification alters how these permissions are interpreted. Let's delve into the command you provided and understand the role of the slash:

```bash
find -perm /u=r,o=r,g=r
```
- **Slash (`/`) in `-perm`**:
  - **Purpose**: Indicates that the `find` command should match files where **any one** of the specified permission bits is set.
  - **Logic**: **OR** condition among the permissions.
  - **Use Case**: When you want to find files that are readable by the user, group, or others, without requiring all categories to have read permissions.


**Command Without Slash:**

```bash
find . -perm u=r,g=r,o=r
```

- **Matches Only**: Files where **all three** permissions are set.
- **Result**: **File C** (`-rw-r--r--`) because it has read permissions for user, group, and others.

**Command With Slash:**

```bash
find . -perm /u=r,o=r,g=r
```

- **Matches If Any**: Any one of the specified permissions is set.
- **Result**: **File A**, **File B**, and **File C**
  - **File A**: User and group have read permissions.
  - **File B**: User has read permissions.
  - **File C**: User, group, and others have read permissions.


## 6. Text Manipulation

### Word Count
- **Count Lines**: 
    ```bash
    wc -l filename
    ```

### Sed for Substitution
- **Replace Word Globally**:
    ```bash
    sed 's/oldword/newword/g' file.txt
    ```
- **Replace in a Range of Lines**:
    ```bash
    sed -i '500,2000s/enabled/disabled/g' file.txt
    ```

### Grep for Searching
- **Search Recursively, Ignoring Case**: 
    ```bash
    grep -ir 'word' /path/
    ```
- **Search for Exact Word**:
    ```bash
    grep -w 'word' /path/
    ```

### Regex in Grep
- **Match Start of Line**: 
    ```bash
    grep '^word' file.txt
    ```
- **Match End of Line**: 
    ```bash
    grep 'word$' file.txt
    ```

### Example:
Find and count numbers in `/home/bob/textfile` that begin with 2:
```bash
grep -c '^2' /home/bob/textfile > /home/bob/count
```

---

## 7. Archiving and Compressing

### Tar Commands
- **List Files in Archive**:
    ```bash
    tar -tf archive.tar
    ```
- **Create an Archive**:
    ```bash
    tar cf archive.tar file1 file2
    ```
- **Extract an Archive**:
    ```bash
    tar xf archive.tar
    ```
- **Add to Existing Archive**:
    ```bash
    tar rf archive.tar newfile
    ```
- **Extract Archive to /tmp**:
    ```bash
    tar xf /home/bob/archive.tar.gz -C /tmp
    ```


### Compress Files
- **Gzip**: 
    ```bash
    gzip filename
    ```
- **Unzip**: 
    ```bash
    gunzip file.gz
    ```

### Combine Tar and Gzip
- **Create Compressed Archive**:
    ```bash
    tar czf archive.tar.gz file
    ```
- **Extract Compressed Archive**:
    ```bash
    tar xzf archive.tar.gz
    ```
### Exercice:
- **Create a Compressed Tar Archive**:
    ```bash
    sudo tar czfP /home/bob/logs.tar.gz /var/log/ #P flag to avoid skipping first '/'
    ```
  
---

## 8. Star vs Tar: Archiving with `star`

`star` is another tool for archiving and compressing files, with additional features over `tar`.

### Basic Commands with Star
- **Create Archive**:
    ```bash
    star -cv file=/home/aaron/archive.star file1
    ```
- **List Contents of Archive**:
    ```bash
    star -tv file=/home/aaron/archive.star
    ```
- **Extract Archive**:
    ```bash
    star -xv file=/home/aaron/archive.star
    ```

### Compression and Decompression with Star
- **Create Compressed Archive with gzip**:
    ```bash
    star -cv -z file=/home/aaron/archive.star.gz file1
    ```
- **Extract gzip Compressed Archive**:
    ```bash
    star -xv file=/home/aaron/archive.star.gz
    ```

### Comparison: `tar` vs `star`
- **`tar`**: Standard tool for archiving, compressing, and extracting files.
- **`star`**: A more advanced tool that provides faster archiving, enhanced error handling, and extended file attributes.

---

## 9. Redirection and Output Handling

### Difference Between `>` and `>>`
- **`>`**: Redirects output and overwrites the file:
    ```bash
    date > file.txt  # Overwrites file.txt with the current date
    ```
- **`>>`**: Appends output to the file:
    ```bash
    date >> file.txt  # Appends the current date to file.txt
    ```

### Redirecting Errors and Output
- **Redirect Both Output and Errors**:
    ```bash
    grep -r '^The' /etc/ > output.txt 2>&1
    ```

- **Separate Output and Errors**:
    ```bash
    grep -r '^The' /

etc/ 1>output.txt 2>errors.txt
    ```

---

## 10. Transferring Files Between Servers Securely

### Using SCP (Secure Copy Protocol)
- **Copy a File from Remote to Local**:
    ```bash
    scp aaron@192.168.1.27:/home/aaron/myfile.tgz /home/aaron/myfile.tgz
    ```
- **Copy a File from Local to Remote**:
    ```bash
    scp /home/aaron/my_archive.tar aaron@192.168.1.27:/home/aaron/my_archive.tar
    ```

### Using SFTP (Secure File Transfer Protocol)
- **Start SFTP Session**:
    ```bash
    sftp aaron@192.168.1.27
    ```
- **Upload a File**:
    ```bash
    sftp> put myarchive.tgz
    ```

---

## 11. Backup Data to a Remote Server

### Using Rsync for File Transfer
- **Backup Local Directory to Remote**:
    ```bash
    rsync -a local-dir/ username@ip:/remote-dir/
    ```

- **Backup Remote Directory to Local**:
    ```bash
    rsync -a username@ip:/remote-dir/ local-dir/
    ```

---

## 12. sorting

- **Sort File and Remove Duplicates**:
    ```bash
    sort -du /home/bob/values.conf > /home/bob/values.sort
    ```
---
