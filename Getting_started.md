### Getting started
We'll start by logging into Ron using the `ssh` command. Ron is a UNH server computer where Qiime and many other programs are installed. Type the following command using the username assigned to you.

```
ssh username@ron.sr.unh.edu
```
Once you hit enter, type in your password which won't show on the screen but it's there. 

Next, change your password by typing the `passwd` command and hit enter. 

Choose a new password.

### Next, run

```
source .bashrc
```

### To use Qiime, run the commands below

```
enable_qiime.sh
```
```
source activate qiime1
```

Now, you should be in the Qiime environment and ready for the pipeline.


