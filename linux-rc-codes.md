# Linux Return Codes (RC) – Practical Notes

## What is RC?

RC means:

```text
Return Code
```

Every Linux command returns a status code after execution.

Check it using:

```bash
echo $?
```

---

# Standard Meaning

| RC Code | Meaning           |
| ------- | ----------------- |
| 0       | Success           |
| 1       | General Error     |
| 2       | Misuse of Command |
| 126     | Permission Denied |
| 127     | Command Not Found |

---

# 1. RC = 0 (Success)

Command:

```bash
ls /tmp
echo $?
```

Output:

```text
0
```

Meaning:

```text
Command executed successfully
```

---

# 2. RC = 1 (General Error)

Command:

```bash
grep root /abcxyz
echo $?
```

Output:

```text
grep: /abcxyz: No such file or directory
1
```

Meaning:

```text
General error occurred
```

---

# 3. RC = 2 (Misuse of Command)

Command:

```bash
ls --abc
echo $?
```

Output:

```text
ls: unrecognized option '--abc'
2
```

Meaning:

```text
Wrong syntax or invalid option used
```

---

# 4. RC = 126 (Permission Denied)

Create file:

```bash
vi test.sh
```

Content:

```bash
#!/bin/bash
echo hello
```

Run without execute permission:

```bash
./test.sh
echo $?
```

Output:

```text
Permission denied
126
```

Meaning:

```text
File exists but execute permission missing
```

Fix:

```bash
chmod +x test.sh
./test.sh
```

---

# 5. RC = 127 (Command Not Found)

Command:

```bash
abcxyz
echo $?
```

Output:

```text
bash: abcxyz: command not found
127
```

Meaning:

```text
Command does not exist
```

---

# Important Note

```bash
echo $?
```

Always shows the return code of the immediately previous command.

Example:

```bash
ls /tmp
pwd
echo $?
```

Here:

```text
echo $?
```

shows the RC of:

```bash
pwd
```

NOT:

```bash
ls /tmp
```

---

# Ansible Relation

Example:

```yaml
- command: subscription-manager identity
  register: registration_check
```

Check RC:

```yaml
when: registration_check.rc == 0
```

Meaning:

```text
Run task only if previous command succeeded
```

---

# Easy Memory Trick

```text
0 = OK
Anything else = Problem
```
