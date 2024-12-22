# Purple Teaming

- Every one has a vulnerable app.
- Every n minutes, the modification made by the teams is pushed.
- Everyone can attack everyone else during this time.
- Team has a flag which reset every iterations.
- A healthcheck bot can be made to verify the defined functionnalities are still available.

## Main

The main application would be a manager of the services.
- Healthcheck
- Log monitoring
- Points
- VPN
- Network

The application will need two parts: common and variable short and long term. The common part is everything every team has in common such has the application, the system, the database schema, etc. The variable part is everything that changes from team to team.
- Short term variable change at every push and includes flags, etc.
- Long term variable doesn't change for the event and includes VPN connections, credentials for known user of the platform and password such as root.

## Network

Docker compose with an image for each users. Every N minutes, we can pull the latest version. Since docker hub teams is not free, we can build it from github using private repo fork from a base example.

## Healthcheck

How to make something patchable while preventing any functionality of the thing being taken down? I feel like it is the main problem with this kind of CTF.

### How

- TryHackMe King of the hill set the rules and monitor the service. If people doesn't respect the rules, they are ban.
- A healthcheck bot can be made to verify the defined functionalities are still available and periodically look it up. Obviously, it shouldn't be easily detectable so a team could block all traffic but the bot one. And a change could make the bot not work even if the service is still perfectly up, like a timeout change.
- The application files could be mounted as Read-Only volume. I don't think this actually fix the problem. If we want defense, we need to be able to add security around the app and those still can prevent the functionnality.
- Make the root user hackable and the service run by him. I think those have the same problem as the Read-Only volume.

### Choice

Make a bot using the common functionality of the service.
Defining Users. Each user (bot) would do a specific set of action on the platform.
Since a bot is highly custom to the service, we should make a template to make basic action such as connect, click, wait.
Since bots represents Users, they can be a attack vector such as XXS, Social Engineering (which shouldn't work on bot) and session hijacking.

### Result

If a healthcheck work, nothing happen.
If a healthcheck doesn't work, a penalty is applied to the team. It can be in different form such as points, timeout from the network or other teams, a number of version block from update.

### Code

It is really complicated to design a framework to include every possible use case. I think I have 2 options:
- Make an interface / Abstract Base Class. This has the lowest overhead and would directly integrate in a base code which would call the bots with the appropriate timing. The main problem is the flexibility has only the chosen language would be available. 
- Use a multiprocessor system. A base system would call those binary / scripts. Use stdout for information logging, stderr to explain the healthcheck fail. The return value would be 0 for success and anything else for a fail.

I like the multiprocessor more. The bots could use Google Chrome headless to simulate browser uses. [chromedp](https://github.com/chromedp/chromedp?tab=readme-ov-file), [selenium](https://selenium-python.readthedocs.io/) or any way based on the language you use.

The base code require:
- Input the path to the binaries and map them to a name, parameters (credential) and dependencies (such as python).
- Frequency median and deviation of calling the bot + timeout. Calling at the same time for everyone.
- Get the result. Log.
- Set a penalty.

Since we want every bot to have their own ip address, but I rather have one container running every bots, we can use from inside the docker container:
```sh
ip address add 10.5.0.6/32 dev eth0
```

In an exemple like this: 
```dockerfile
services:
  health:
    image: ubuntu
    networks:
      vpcbr:
        ipv4_address: 10.5.0.5
    command: "tail -f /dev/null"
    cap_add:
      - NET_ADMIN

networks:
  vpcbr:
    driver: bridge
    ipam:
      config:
        - subnet: 10.5.0.0/32
          gateway: 10.5.0.1
```


### Team complaint

Since a bot still has a comportment and a distinct ip, I still want a page to be able to complain with proof about a team that would break the rules.
- timestamp
- page in CTFd
- team selector
- screenshots
- message

## Log monitoring

Being able to monitor what happens on the platform is important to detect users interaction. Maybe running an elasticsearch server with an agent collection logs from the network. 

## Points

For this mode, the flags are short term variable. On every push, flags can be submitted. A team can't submit their own flag. A dashboard to show the points and advancement.
Use [CTFd](https://github.com/CTFd/CTFd).

## VPN

Add a wireguard server in a dedicated server for each team: this will be their entrypoint. We will need a service to create those certificates. 
Make sure to have a way to connect to the VPN. If using auto SERVER\_URL, make sure to forward the port 51820 in the router.

