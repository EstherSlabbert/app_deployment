# Automation of app deployment

See manual steps and more detailed information of app deployment here: [app deployment](https://github.com/EstherSlabbert/tech230_app_deployment/blob/main/app_deployment.md)

Rather than going through all the required updates and installs we can modify our 'provision.sh' file to automate them.

## Steps to automate

1. Ensure Vagrant is installed on your device and you have all the appropriate files and folders.
2. Have the following code in your 'provision.sh' file:
```shell
#!/bin/bash
sudo apt-get update
sudo apt-get upgrade -y
sudo apt-get install nginx -y
sudo systemctl start nginx
sudo apt-get install python-software-properties -y
curl -sL https://deb.nodesource.com/setup_6.x | sudo -E bash -
sudo apt-get install nodejs -y
sudo npm install pm2 -g
cd /home/vagrant/app
npm install
node app.js
```

(Note: you may use `npm start` as the last command in the 'provision.sh' file rather than `node app.js`. Either will work.)

And in your 'Vagrantfile':
```ruby
Vagrant.configure("2") do |config|

  config.vm.box = "ubuntu/xenial64"
  config.vm.network "private_network", ip:"192.168.10.100"
  config.vm.provision "shell", path: "provision.sh"
  config.vm.synced_folder "app", "/home/vagrant/app"

end
```

2. Run `vagrant up` (this will take a while) and your app should automatically run, once `Your app is ready and listening on port 3000` is displayed in your command terminal your server should be accessible on your web browser with the following link: http://192.168.10.100:3000/.
3. If you wish to end this and remove your VM use the following command: `kill $(ps aux | grep '[v]agrant' | awk '{print $2}')`, this kills vagrant processes in your VM and allows you to `vagrant destroy`.

Note: To move running app to the background: Press Ctrl + Z. This will suspend the app and bring you back to the command prompt. Then type the command `bg` (short for "background"). This will move the app to the background and allow you to continue using the terminal. If you want to bring the app back to the foreground later, you can type the command `fg` (short for "foreground"). **Warning**: some apps may not work properly when they are moved to the background, so you should test your app to make sure it still functions as expected.