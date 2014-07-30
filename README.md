## Clean directories with scheduled task in OS X

My Downloads and Desktop directories tend to grow with temporary installation files and documents.
I want to have them cleaned with a daily scheduled task to prevent myself from even saving important stuff there.

You can do this too.

### Here's how

<span style="color: red">
**WARNING: Running these commands will remove all files in your Downloads and Desktop directories! Have backups!**
</span>

These commands will schedule task which will remove all files(but not folders or their contents)
from your user's ~/Downloads and ~/Desktop folders daily at 14:00(2pm).

```sh
git clone https://github.com/kimmobrunfeldt/clean-workspace-dirs.git
cd clean-workspace-dirs

cp clean-workspace-dirs.sh /usr/bin/local/
chmod +x /usr/bin/local/clean-workspace-dirs.sh
cp com.kimmobrunfeldt.clean-workspace-dirs.plist ~/Library/LaunchAgents/

# WARNING! This will also run the clean task
launchctl load ~/Library/LaunchAgents/com.kimmobrunfeldt.clean-workspace-dirs.plist
```

I strongly recommend you to read [com.kimmobrunfeldt.clean-workspace-dirs.plist](com.kimmobrunfeldt.clean-workspace-dirs.plist)
and [clean-workspace-dirs.sh](clean-workspace-dirs.sh) to see what they actually do.

#### More details

When configuring the clean script, remember that `rm -rf` will follow mounted directories also.
So if you have external drive mounted to your Desktop, it will remove files from it also.
The current script does not use `-r` flag.

You might want to edit [com.kimmobrunfeldt.clean-workspace-dirs.plist](com.kimmobrunfeldt.clean-workspace-dirs.plist) to your needs. Now the settings are:

* `KeepAlive` is `false`. The script will only be run on defined calendar interval.

        This optional key is used to control whether your job is to be kept continuously running or to let
        demand and conditions control the invocation. The default is false and therefore only demand will start
        the job. The value may be set to true to unconditionally keep the job alive.

* `RunAtLoad` is `true`. The script will be run when command `launchctl load ...` is executed.

        This optional key is used to control whether your job is launched once at the time the job is loaded.

* `Program` is `/usr/bin/local/clean-workspace-dirs.sh`. Program to be run. You cannot add parameters to this value, check `ProgramArguments` instead.

* `StartCalendarInterval` is every day 14:00.

        This optional key causes the job to be started every calendar interval as specified. Missing arguments
        are considered to be wildcard. The semantics are much like crontab(5).  Unlike cron which skips job
        invocations when the computer is asleep, launchd will start the job the next time the computer wakes
        up.  If multiple intervals transpire before the computer is woken, those events will be coalesced into
        one event upon wake from sleep.

    **Note: task is not run if computer is shutdown. It will only work if computer is asleep.**

Check Apple's detailed [documentation for launchd](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man5/launchd.plist.5.html) for more.
