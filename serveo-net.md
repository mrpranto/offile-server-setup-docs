#### Setp 7: Open server access on internet
- Run this command to open server tunel and also add on crontab -e
> `ssh -f -N -R 3333:localhost:22 serveo.net`
> `@reboot ssh -f -N -R 3333:localhost:22 serveo.net`

- Run this command to open mysql server tunel and also add on crontab -e
> `ssh -f -N -R 3360:localhost:3306 serveo.net`
> `@reboot ssh -f -N -R 3360:localhost:3306 serveo.net`


- Try to login on mysql server remotly through this command
>`mysql -u replica -pReplica@123 -h serveo.net -P 3360`

- Try to login on server remotly through this command
> `ssh -p 3333 familymart@serveo.net`
