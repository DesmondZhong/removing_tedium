# Automated data analysis and presentation

Most HPC researchers start their day by downloading data from the clusters, running analysis scripts and then examining the resulting figures. This page shows you how to automate the first two of these operations so that when you arrive in the morning the figures will have been generated just a few minutes earlier.

Note that the techniques presented here only work if one has followed the previous steps to suppressed DUO and enable password-less logins.

## Running scripts just before you start the day using cron

`cron` is a scheduler used to run commands at specific times. It is not available on the cluster head nodes. However, we can use it on our local machines. Below shows the format of an entry in `crontab`.

```
* * * * * command
* - minute (0-59)
* - hour (0-23)
* - day of the month (1-31)
* - month (1-12)
* - day of the week (0-6, 0 is Sunday)
command - command to execute
(from left-to-right)
```

Make an entry with `crontab -e`. To view the entries:

```
$ crontab -l
0 9 * * 1-5 cd ~/research/automate && ./auto_single.sh
```

The entry above will run `auto_single.sh` Monday thru Friday at 9 am. If `cron` is not available then consider `at`. One can also simply run the script manually when arriving or run it remotely.

## Pipeline example script

The contents of the Bash script `auto_single.sh` are shown below. After defining some variables the script downloads data from Tiger. It then runs a self-written utility called `calc` on that data to generate two figures and an HTML page. The figures and HTML page are then uploaded to Tiger where they are moved onto `tigress-web`. One can then view the figures in a web browser.

```bash
#!/bin/bash
NETID=ceisgrub
JOBNAME=myjob
JOBPATH=/home/$NETID/$JOBNAME
TGR=/tigress/$NETID/public_html/$JOBNAME

scp $NETID@tiger.princeton.edu:$JOBPATH/fluid.dat .
calc -f fluid.dat --plot   # generates pressure.jpg, temperature.jpg and index.html
scp pressure.jpg temperature.jpg index.html $NETID@tiger.princeton.edu:$JOBPATH
ssh $NETID@tiger.princeton.edu "cd $JOBPATH; mkdir -p $TGR; mv *.jpg index.html $TGR;"

echo "Point your browser to https://tigress-web.princeton.edu/~$NETID/$JOBNAME"
```
In this example we focused on a single job but this approach can be extended to multiple jobs. Furthermore, after generating the figures one could also run a second script on the new data and take actions like launching additional jobs or canceling queued jobs. And all of this occurs before you arrive in the morning.

If you only have an account on Adroit then you will not be able to use `tigress-web`. In this case simply view the generated plots on your local machine.

Mac users can add the following line to `auto_single.sh` to load the web page automatically in Safari:

```
open -a Safari https://tigress-web.princeton.edu/~$NETID/$JOBNAME
```
