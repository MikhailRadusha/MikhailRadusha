### In this Guide I will set up and use telegram status & alarm system which was developed by Cyberomanov, to whom  I am very greatful, you can find original scripts here in his repository:   https://github.com/cyberomanov 

### Feel free to use this guide for your validators.

# status
This system will alert you with telegram about jails and inactive status. Also it sends you every hour short info about your node status.

Instruction:

1. Create telegram bot via `@BotFather`, customize it and `get bot API token` ([how_to](https://www.siteguarding.com/en/how-to-get-telegram-bot-api-token)).
2. Create at least 2 groups: `alarm` and `log`. Customize them, add your bot into your chats and `get chats IDs` ([how_to](https://stackoverflow.com/questions/32423837/telegram-bot-how-to-get-a-group-chat-id)).
3. Connect to your server and create `status` folder in the `$HOME directory` with `mkdir $HOME/status/`.
4. In this folder, `$HOME/status/`, you have to create `cosmos.sh` file with `nano $HOME/status/cosmos.sh`. You don't have to do any edits on `cosmos.sh` file, it's ready to use.
> You can find `cosmos.sh` in this repository.
5. In this folder, `$HOME/status/`, you have to create `cosmos.conf` file with `nano $HOME/status/cosmos.conf`. Customize it.
> You can find `cosmos.conf` in this repository.
6. Also you have to create as many `name.conf` files with `nano $HOME/status/name.conf`, as many nodes you have on the current server. Customize your config files. For ex: I have haqq and sei on the same server, so I have to create 2 files: `haqq.conf` and `sei.conf`.
> You can find `name.conf` and `curl.md` in this repository.
7. Install some packages with `sudo apt-get install jq sysstat bc smartmontools fdisk -y`.
8. Run `bash cosmos.sh` to check your settings. Normal output:
```
root@Ubuntu-2004-focal-64-minimal:~/status# bash cosmos.sh

/// 2022-09-19 10:35:17 ///

Hetzner_4  |  load

cpu_used >>>> 19%.
ram_used >>>> 36%.
swap_used >>> 5%.
part_used >>> 79%.
serv_load >>> 3.36.

disk_spare >> nvme1n1 has 100% spare.
disk_used >>> nvme1n1 has 8% used.
disk_spare >> nvme0n1 has 100% spare.
disk_used >>> nvme0n1 has 8% used.

haqq-t  |  StingRay

exp/me >>>>>> 144577/144577.
priv_key >>>> right.
place >>>>>>> 52/150.
stake >>>>>>> 10010.00 ISLM.
missed >>>>>> 0 blocks.
_gov >>>>>>>> #61.
upgrade >>>>> no.

```
9. Add some rules with `chmod u+x $HOME/status/cosmos.sh`.
10. Edit crontab with `crontab -e`.
> You can find `crontab` in this repository.
11. Check your logs with `cat $HOME/status/cosmos.log` or `tail $HOME/status/cosmos.log -f`.
12. Now we receive every hour the status of validator in log telegram channel

![image](https://user-images.githubusercontent.com/87991370/190980350-6dff309d-655a-48aa-9204-cc60bb0f27bf.png)

13. In case of upgrade or emergency ( such as lost contact to validator or validator skipping the blocks or jailed) or if the validator went to inactive set the system will send a message to alarm telegram channel. 

![image](https://user-images.githubusercontent.com/87991370/191571588-ddb87c86-a7c2-45f5-9137-71562846b324.png)


