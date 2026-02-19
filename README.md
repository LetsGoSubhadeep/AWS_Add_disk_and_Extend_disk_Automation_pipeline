# In this project I have automated the process of adding a disk into AWS and making it to FS from OS end.
# also, Extending an existing disk in AWS and grow the FS from os end. 
# this project creted only targetting the Linux systems. Windows or any other OS will not work in this case
# In the Jenkins pipeline user have to put the server name for which user want to extend the disk or add a new disk and user have to provide the Mountpoint Name, Disk Size and region.
# After selecting all the required parameters in the Jenkins Pipeline, it will automatically trigger the Ansible playbook
