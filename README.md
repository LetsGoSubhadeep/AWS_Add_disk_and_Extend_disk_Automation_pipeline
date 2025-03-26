# In this project I have automated the process of adding a disk into AWS and making it to FS from OS end.
# also, Extending an existing disk in AWS and grow the FS from os end. 
# this project creted only targetting the Linux systems. Windows or any other OS will not work in this case
# In the Jenkins pipeline user have to put the server name for which user want to extend the disk or add a new disk and user have to provide the Mountpoint Name, Disk Size.
# After selecting all the required parameters in the Jenkins Pipeline, it will automatically trigger the Ansible playbook
# NOTE: In the case of extend an existing disk, we are assuming in AWS the EBS volume taging is same as the mountpoint name or tag name contains the mountpoint name. For ex: if the mount point name is "/apps" then the AWS volume name should also contain the mount point name # ex: "server1-/apps". The playbook will fail if voulume name doesn't contains mountpoint name  
