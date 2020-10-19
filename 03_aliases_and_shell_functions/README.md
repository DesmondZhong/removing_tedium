# Aliases and Shell Functions

While working on the HPC clusters you will probably find yourself entering the same commands over and over again. There will also be times when you will run multiple commands to get a result. These two types of actions reduce your productivity and they can be partially eliminated.

Commonly repeated commands should be replaced by an alias, which is a shorter name for the command. An alias can also combine multiple commands into one. Shell functions are like aliases but they are more flexible because they accept command-line parameters. This page illustrates who to use aliases and shell functions to improve productivity.

## First Alias

Let's illustrate the process of creating an alias using this popular command:

```
$ squeue -u <YourNetID>
```

Instead of typing this every time, we will use the much shorter alias of `sq`:

```
$ alias sq='squeue -u <YourNetID>'
```

Be sure to replace `<YourNetID>` with your actual NetID (e.g., aturing). After defining this alias, one can type `sq` instead of the much longer `squeue -u <YourNetID>`:

```
$ sq
```

Note that aliases defined on the command line will only be available in the current shell. To make them permanent see the next section.

## Store your aliases and shell functions in .bashrc

To make your aliases and shell functions available each time you log in, store them in your `.bashrc` file. Here is the contents of `.bashrc` for a new account:

```
$ cat ~/.bashrc

# Source global definitions
if [ -f /etc/bashrc ]; then
	. /etc/bashrc
fi

# Uncomment the following line if you don't like systemctl's auto-paging feature:
# export SYSTEMD_PAGER=

# User specific aliases and functions
```

The last line above indicates where to store aliases and shell functions, but exactly how to do this depends on where you have accounts.

### If you only have an account on Adroit ...

You make your aliases and shell functions permanent by adding them to your `.bashrc` file and then sourcing the file. For instance, use a text editor like vim or emacs to add the `sq` alias:

```
$ nano ~/.bashrc  # or vim, emacs, MyAdroit, etc.
```

Add this line:

```
# User specific aliases and functions
alias sq='squeue -u <YourNetID>'
```

Save the changes and then return to the command line. Make the new alias available in the current shell by "sourcing" your `.bashrc` file:

```
$ source ~/.bashrc
```

Now try out the alias:

```
$ sq
```

You only need to source your `~/.bashrc` file when you add an alias in the current session. When you first log in all aliases will be available.

Once you have built up your `.bashrc` file it will contain aliases and shell functions like those in the example [myshortcuts.sh](https://github.com/PrincetonUniversity/removing_tedium/blob/master/03_aliases_and_shell_functions/myshortcuts.sh) file in this repo. If you wish to use all the shortcuts in that file then run these commands:

```
$ wget https://raw.githubusercontent.com/PrincetonUniversity/removing_tedium/master/03_aliases_and_shell_functions/myshortcuts.sh
# add "source myshortcuts.sh" to your .bashrc file
$ source ~/.bashrc
```

### If you have an account on Della, Perseus, Tiger or Tigressdata ...

Della, Perseus, Tiger and Tigressdata all mount the `/tigress` filesystem. If you have an account on one or more of these clusters it is recommended that you store your aliases and shell functions in a file on `/tigress` and `source` this from each `.bashrc` file for each account. Here is the three-step procedure for this:

![shortcuts](https://tigress-web.princeton.edu/~jdh4/myshortcuts_diagram.png)
<br/><br/>

That is, make the file `/tigress/<YourNetID>/myshortcuts.sh` and put your aliases and functions there. Then add the following line to each of your `.bashrc` files:

```
# User specific aliases and functions
source /tigress/<YourNetID>/myshortcuts.sh
```

This approach ensures that your shortcuts remain in sync across all of you accounts. Unfortunately, this will not work for Adroit or Nobel since those clusters do not mount `/tigress`. You will have to manually update the `~/.bashrc` files for those systems.  One way to do this is to scp `myshortcuts.sh` from `/tigress` to those machines.

Once the setup is complete, begin adding aliases and shell functions to `myshortcuts.sh`. See an [example file](https://github.com/PrincetonUniversity/removing_tedium/blob/master/03_aliases_and_shell_functions/myshortcuts.sh) which is in this repo. 

You can make new additions to `myshortcuts.sh` activate in the current shell by sourcing your `.bashrc` file which will in turn source `myshortcuts.sh`:

```
$ source ~/.bashrc
```

## Checking which aliases are defined

To see your aliases use the `alias` command:

```
$ alias
alias egrep='egrep --color=auto'
alias fgrep='fgrep --color=auto'
alias grep='grep --color=auto'
alias l.='ls -d .* --color=auto'
alias ll='ls -l --color=auto'
alias ls='ls --color=auto'
alias sq='squeue -u aturing'
alias vi='vim'
alias which='alias | /usr/bin/which --tty-only --read-alias --show-dot --show-tilde'
```

Note that some aliases are automatically created. The above is saying that if with run the `l.` command, for example, the shell is really running `ls -d .* --color=auto`.

## A word of caution

Be sure not to name an alias after an existing command. If your shell is not behaving as expected it may be because you created an alias using the name of a pre-existing command. Try running your proposed alias name on the command line to see if it is already a command before creating a new alias.

Aliases take precedence over commands loaded via modules. This is illustrated below with the `intel` module:

```
$ module load intel
$ icc
$ module purge
$ alias icc='ps -u $USER'
$ module load intel
$ icc
```

If you run the commands above, `icc` will not be the Intel C compiler as one may expect. Be careful of this and put some effort into choosing alias names. When choosing a name for an alias, always make sure that it is not already in use:

```
$ cq
-bash: cq: command not found
```

We see that `cq` is not in use so this could be used as an alias for the `checkquota` command:

```
alias cq='checkquota'
```

## Viewing your aliases and selectively turning them off

To see the aliases that you are using run this command:

```
$ alias
```

To turn off a specific alias for the current shell session:

```
$ unalias <alias>
```

Or for a single command:

```
$ \<alias>
```

Return to the **Word of caution** section above and try running `\icc` at the very end.

## The home keys system: Working with recent files

On a QWERTY keyboard the home keys are A, S, D, F, J, K, L and the semicolon. Since your fingers typically rest on these keys they make great alias names.

![home_keys](https://upload.wikimedia.org/wikipedia/commons/0/0d/QWERTY-home-keys-position.svg)

Consider the system below based on the home keys:

```
export EDITOR=/usr/bin/vim   # or emacs or nano
alias ll='ls -ltrh'
alias jj='cat -- "$(ls -t | head -n 1)"'
alias kk='cat -- "$(ls -t | head -n 2 | tail -n 1)"'
alias ff='$EDITOR -- "$(ls -t | head -n 1)"'
alias dd='$EDITOR -- "$(ls -t | head -n 2 | tail -n 1)"'
```

The `ll` alias lists the files in the current directory in long format and sorts them by modification time with the newest at the bottom.

The `jj` command prints the contents of the newest file in the current working directory to the terminal while `kk` prints out the second newest file. `jj` **is arguably the most useful alias on this entire page. Start using it**! The `ff` command loads the newest file in your specified text editor while `dd` loads the second newest file. The routine use of `ll`, `jj` and `ff` can save you lots of time. Note that `dd` overwrites an existing command. Because the original `dd` is obscure, this can be overlooked. If you are left-handed then you may consider transposing the aliases.

Note that `aa` and `ss` are waiting to be defined. While `ss` is a pre-existing command, it is obscure and can be overwritten. `gg` and `hh` are also available.

The meaning of `--` in the commands above is explained [here](https://unix.stackexchange.com/questions/510857/what-is-meaning-of-double-hyphen-in-ls-command).

## Navigation

Here are some shell functions and aliases for creating directories and moving around the filesystem:

```
mk() { mkdir -p "$1" && cd "$1"; }
cl() { cd "$1" && ll; } # uses alias defined above
alias ..='cd ..'
alias ...='cd ../..'
```

`mk` is the first shell function that we have encountered. The existence of `$1` in the body of the function corresponds to the input paramter. The `&&` operator ensures that the command on the right is only executed if the command on the left is successful. The `mk` function makes a directory and then cd's into that directory:

```
$ pwd
/home/aturing
$ mk myproj
$ pwd
/home/aturing/myproj
```

The `cl` function cd's into a specified directory and runs the `ll` alias. The `..` alias above allows us to type 2 keys instead of 5 to go up a level.

## Environment modules

Here are some aliases for quickly working with modules:

```
alias ma='module avail'
alias mp='module purge'
alias ml='echo && module -l list 2>&1 | tail -n +3 && echo'
alias mla='module load anaconda3'
alias mlc='module load cudatoolkit'
alias rh8='module load rh/devtoolset/8'
```

Another approach would be to define:

```
alias modl='module load'
```

And then use it like this:

```
$ modl anaconda3
```

## Conda environments

The shell functions and alias below can be used to work with conda environments:

```bash
conen() {
  if [ $(module -l list 2>&1 | grep -c anaconda3) -eq 0 ]; then
    echo "Loading anaconda3 module ..."
    module load anaconda3
  fi 
  conda info --envs | grep . | grep -v "#" | cat -n
}
conac() {
  name=$(conda info --envs | grep -v "#" | awk 'NR=="'$1'"' | tr -s ' ' | cut -d' ' -f 1)
  conda activate $name
}
alias conde='conda deactivate'
conrm() {
  name=$(conda info --envs | grep -v "#" | awk 'NR=="'$1'"' | tr -s ' ' | cut -d' ' -f 1)
  conda remove --name $name --all -y -q
  echo; conen; echo
}
```

### conen
Displayed your enumerated conda environments (and load the anaconda3 module if necessary). This is similar to `conda env list` which can also be used.

### conac
Activate an environment by number. This is similar to `conda activate <name>` which can also be used.

### conde
Deactivate the current environment.

### conrm
Remove an environment by number. This is similar to the by name command of `conda remove --name <name> --all`.


conen - displays your conda environments (and load anaconda3 if necessary)  
conac - activates an environment by number  
conde - deactivates the current environment  
conrm - removes an environment by number  

A session using the two of the functions above might look like this:

```
[aturing@tigergpu ~]$ conen
     1	tf2-gpu                  /home/jdh4/.conda/envs/tf2-gpu
     2	torch-env                /home/jdh4/.conda/envs/torch-env
     3	base                  *  /usr/licensed/anaconda3/2019.10
[aturing@tigergpu ~]$ conac 2
(torch-env) [aturing@tigergpu ~]$
```

Note that aliases do not work in Slurm scripts. You will need to explicitly load your modules in Slurm scripts.

## Slurm

Below are several aliases and shell functions for working with Slurm:

```bash
SLURMSCRIPT="job.slurm"
alias sq='squeue -u $USER'
alias sqs='squeue -u $USER --start'
alias wq='watch -n 1 squeue -u $USER'
alias sb='sbatch $SLURMSCRIPT'
alias cpu5='salloc --nodes=1 --ntasks=1 --mem=4G --time=00:05:00'
alias gpu5='salloc --nodes=1 --ntasks=1 --mem=4G --time=00:05:00 --gres=gpu:1'
alias st='slurmtop'
alias fair='echo "Fairshare: " && sshare | cut -c 84- | sort -g | uniq | tail -1'
FRMT="1.1,1.3,1.4,1.5,1.6,1.7,2.3"
alias myprio='join -j 1 -o ${FRMT} <(sqs | sort) <(sprio | sort) | sort -g'
mycancel() { scancel $(squeue -u $USER | tail -1 | tr -s [:blank:] | cut -d' ' -f2); }
maxmem() { snodes | tr -s [:blank:] | cut -d' ' -f7 | sort -g | uniq; }
eff() { seff $(( $(echo $(ls -t slurm-*.out | head -n 1) | tr -dc '0-9' ))); }
goto() { ssh $(squeue -u $USER | tail -1 | tr -s [:blank:] | cut -d' ' -f9); }
alias sw='sbatch $SLURMSCRIPT && watch -n 1 squeue -u $USER'
```

There are advantages to naming all your Slurm scripts the same.

Most of the shortcuts above are self-evident. Here are some of the nonobvious ones:

sqs - show the expected start times of queued jobs  
wq - watch your queued jobs (use Ctrl + c to exit)  
mycancel - run scancel on your most recently submitted job  
maxmem - list the amount of memory of the different nodes on the cluster  
eff - run seff using the job id from the newest slurm-xxxxxx.out file in the current directory  
goto - ssh to the compute node where your most recently submitted job is running  
sw - "submit" and "watch" the queue  

### wq

Watch the list of jobs:

```
alias wq='watch -n 1 squeue -u <YourNetID>'
```

This will create an alias which will display the result of the squeue command for a given user and update the output every second. This is very useful for monitoring short test jobs. To end the command hold down [Ctrl] and press [c].

### sb and sw

If you submit a lot of jobs with commands like `sbatch job.slurm` or `sbatch submit.sh`. You may try calling all your Slurm scripts by the same name (e.g., `job.slurm`) and then introducing this alias:

```
SLURMSCRIPT='job.slurm'
alias sb='sbatch $SLURMSCRIPT'
```

You can distinguish different jobs by setting the job name in the Slurm script:

```
#SBATCH --job-name=multivar      # create a short name for your job
```

This alias submits the job then launches watch:

```
alias sw='sbatch $SLURMSCRIPT && watch -n 1 squeue -u $USER'
```

#### eff

If you set `#SBATCH --mail-user` in your Slurm script then you will receive an efficiency report by email. The following command can also be used from the directory containing the slurm output file (e.g., `slurm-3741530.out`):

```
eff() { seff $(ls -t slurm-*.out | head -n 1 | tr -dc '0-9'); }
```

Note that the Slurm database is purged every so often so your results may not be available for very old jobs. 

#### goto

It is often useful to SSH to the compute node where your job is running. From there one can inspect memory usage, whether threads are performing properly and examine GPU utilization, for instance. The following function will connect you to the compute node that your most recent job is on.

This method will not work when multiple nodes are used to run the job.

### mycancel

Canceling the most recently submitted job:

```
mycancel() { scancel $(squeue -u $USER | tail -1 | tr -s [:blank:] | cut -d' ' --fields=2); }
```

### myprio

The following alias combines the output of squeue and sprio to explicitly show your job priority and expected start time for queued jobs:

```
alias pending='join -j 1 -o 1.1,1.3,1.4,1.5,1.6,1.7,2.3 <(squeue -u $USER --start | sort) <(sprio | sort) | sort -g'
```

### Getting your fairshare value

Your fairshare value plays a key role in determining your job priority. The more jobs you or members of your group run in a given period of time the lower your fairshare value. The maximum value is 1.

```
alias fair='echo "Fairshare: " && sshare | cut -c 84- | sort -g | uniq | tail -1'
```

To learn more about job priority see [this post](https://researchcomputing.princeton.edu/priority).

### salloc

For a 5-minute interactive allocation on a CPU or GPU node:

```
alias cpu5='salloc -N 1 -n 1 -t 5'
alias gpu5='salloc -N 1 -n 1 -t 5 --gres=gpu:1'
```

## GPU aliases

```
alias smi='nvidia-smi'
alias wsmi='watch -n 1 nvidia-smi'
```

After submitting a GPU job it is common to run `goto` followed by `wsmi` on the compute node.


## Specific to Adroit

```
if [[ $(hostname) == adroit* ]]; then
  alias gpu5='salloc -N 1 -n 1 -t 5 --gres=gpu:tesla_v100:1'
  alias v100='ssh adroit-h11g1'
fi
```

There is one node on Adroit with V100 GPUs. The aliases are concerned with this node.

## Specific to Della

To get the run time limits for the different job partitions (qos) on Della:

```
if [[ $(hostname) == della* ]]; then
    alias limits='cat /etc/slurm/job_submit.lua | egrep -v "job_desc|--" | awk '"'"'/_MINS/ \
                  {print "  "$1,"<=",$3" mins ("$3/60 " hrs)"}'"'"''
fi
```

## TurboVNC

While X11 forwarding (via `ssh -X`) is usually sufficient to work with graphics on the HPC clusters, TurboVNC is a faster alternative. See the bottom of [this page](https://researchcomputing.princeton.edu/faq/how-do-i-use-vnc-on-tigre) for shells function to ease the setup.


## checkquota

The `checkquota` command provides information about available storage space and number of files. While it's only 10 characters, given its frequent use you may consider reducing it to 2:

```
alias cq='checkquota'
```

Another tip is to put the following in your `~/.bashrc` file to see your remaining space each time you login:

```bash
if [ ! -z "$PS1" ]; then
        checkquota
fi
```

To list the size of each directory:

```
alias dirsize='du -h --max-depth=1 | sort -hr'
```

## Jupyter notebooks

On your laptop:

```
alias jn='jupyter notebook'
```

## Listing, activating and removing Conda environments

Python programmers may benefit from:

```
alias mla='module load anaconda3'
```

If you are a Python user with many Conda environments then the following can be used to print out your environments:

```
alias myenvs='module load anaconda3 && conda info --envs | grep . | grep -v "#" | cat -n'
```

The above alias uses two commands.  Can you think of an alias involving the modules you use?

To activate an environment by its number:

```
actenv() { conda activate $(conda info --envs | grep -v "#" | awk 'NR=="'$1'"' | tr -s ' ' | cut -d ' ' -f 1); }
```

A session using the two aliases above might look like this:

```
[ceisgrub@tigergpu ~]$ myenvs
     1	tf2-gpu                  /home/jdh4/.conda/envs/tf2-gpu
     2	torch-env                /home/jdh4/.conda/envs/torch-env
     3	base                  *  /usr/licensed/anaconda3/2019.10
[ceisgrub@tigergpu ~]$ actenv 2
(torch-env) [ceisgrub@tigergpu ~]$
```

Note that aliases do not work in Slurm scripts. You will need to explicitly load your modules in these scripts.

Below is a shell function to remove an environment by name (e.g., `$ rmenv torch-env`):

```
rmenv() { conda remove --name "$1" --all; }
```

## Weather

Get a weather report for Princeton, NJ:

```
alias wthr='/usr/bin/clear && date && curl -s wttr.in/princeton'
```

This alias was contributed by T. Comi.

```
$ wthr
Tuesday, October 08, 2019  12:30:44 PM
Weather report: princeton

               Overcast
      .--.     57 °F          
   .-(    ).   ↓ 8 mph        
  (___.__)__)  9 mi           
               0.0 in         
                                                       ┌─────────────┐                                                       
┌──────────────────────────────┬───────────────────────┤  Tue 08 Oct ├───────────────────────┬──────────────────────────────┐
│            Morning           │             Noon      └──────┬──────┘     Evening           │             Night            │
├──────────────────────────────┼──────────────────────────────┼──────────────────────────────┼──────────────────────────────┤
│               Overcast       │    \  /       Partly cloudy  │    \  /       Partly cloudy  │    \  /       Partly cloudy  │
│      .--.     57..59 °F      │  _ /"".-.     62 °F          │  _ /"".-.     64 °F          │  _ /"".-.     60 °F          │
│   .-(    ).   ↙ 7-10 mph     │    \_(   ).   ↙ 8-10 mph     │    \_(   ).   ↙ 8-11 mph     │    \_(   ).   ↓ 8-13 mph     │
│  (___.__)__)  6 mi           │    /(___(__)  6 mi           │    /(___(__)  6 mi           │    /(___(__)  6 mi           │
│               0.0 in | 0%    │               0.0 in | 0%    │               0.0 in | 0%    │               0.0 in | 0%    │
└──────────────────────────────┴──────────────────────────────┴──────────────────────────────┴──────────────────────────────┘
                                                       ┌─────────────┐                                                       
┌──────────────────────────────┬───────────────────────┤  Wed 09 Oct ├───────────────────────┬──────────────────────────────┐
│            Morning           │             Noon      └──────┬──────┘     Evening           │             Night            │
├──────────────────────────────┼──────────────────────────────┼──────────────────────────────┼──────────────────────────────┤
│      .-.      Light rain     │               Overcast       │               Overcast       │      .-.      Light drizzle  │
│     (   ).    51..55 °F      │      .--.     50..53 °F      │      .--.     50..53 °F      │     (   ).    48..53 °F      │
│    (___(__)   ↙ 11-17 mph    │   .-(    ).   ↙ 11-16 mph    │   .-(    ).   ↓ 11-18 mph    │    (___(__)   ↓ 11-17 mph    │
│     ‘ ‘ ‘ ‘   6 mi           │  (___.__)__)  5 mi           │  (___.__)__)  3 mi           │     ‘ ‘ ‘ ‘   4 mi           │
│    ‘ ‘ ‘ ‘    0.0 in | 27%   │               0.0 in | 54%   │               0.0 in | 57%   │    ‘ ‘ ‘ ‘    0.0 in | 30%   │
└──────────────────────────────┴──────────────────────────────┴──────────────────────────────┴──────────────────────────────┘
                                                       ┌─────────────┐                                                       
┌──────────────────────────────┬───────────────────────┤  Thu 10 Oct ├───────────────────────┬──────────────────────────────┐
│            Morning           │             Noon      └──────┬──────┘     Evening           │             Night            │
├──────────────────────────────┼──────────────────────────────┼──────────────────────────────┼──────────────────────────────┤
│               Overcast       │               Overcast       │               Overcast       │               Overcast       │
│      .--.     50..53 °F      │      .--.     51..55 °F      │      .--.     50..53 °F      │      .--.     48..53 °F      │
│   .-(    ).   ↓ 11-16 mph    │   .-(    ).   ↓ 12-14 mph    │   .-(    ).   ↓ 11-16 mph    │   .-(    ).   ↓ 11-16 mph    │
│  (___.__)__)  6 mi           │  (___.__)__)  6 mi           │  (___.__)__)  6 mi           │  (___.__)__)  6 mi           │
│               0.0 in | 0%    │               0.0 in | 0%    │               0.0 in | 0%    │               0.0 in | 0%    │
└──────────────────────────────┴──────────────────────────────┴──────────────────────────────┴──────────────────────────────┘
Location: Princeton, Mercer County, New Jersey, United States of America [40.3492744,-74.6592957]

Follow @igor_chubin for wttr.in updates
```

## Calling applications and better commands

```
alias vi='vim'
alias top='htop'
alias cmake='cmake3'
```

Shadowing is the remapping of a command with different parameters.

## Watch anything

Use this alias to watch anything:

```
alias wa='watch -n 1'
```

Use it as follows:

```
$ wa ls -l # if downloading a file
$ wa date
$ wa squeue -u $USER
```

## Make Mac more like Linux

Add this alias to `~/.bash_profile` on your Mac:

```
alias wget='curl -O'
alias ldd='otool -L'
```

This will allow you call `wget` as you would on a Linux machine. The `wget` command can be used to download files from the internet.

## To see shell functions

```
$ set | less
```

## Examine your history for commands to be aliased

Try running the following command on your history to look for common commands to create an alias for:

```
history | awk '{CMD[$2]++;count++;}END { for (a in CMD)print CMD[a] " " CMD[a]/count*100 "% " a;}' | grep -v "./" | column -c3 -s " " -t | sort -nr | nl |  head -n10
```

## More ideas

See [this page](https://www.digitalocean.com/community/tutorials/an-introduction-to-useful-bash-aliases-and-functions) for more aliases and shell functions. To see aliases used by other users on the cluster, run this command:

```
find /home -maxdepth 2 -type f -name '.bashrc' 2>/dev/null | xargs grep 'alias' | grep -v 'User specific aliases and functions' | sed 's/^.*\(alias\)/\1/' | sort | uniq | cat -n
```
