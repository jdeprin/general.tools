#!/bin/bash
#complete_new_user.sh

echo "Deleting the 'saildrone' user account"
sudo deluser --remove-home saildrone

echo "Adding $USER to groups"
sudo usermod -aG docker $USER
sudo usermod -aG dialout $USER

echo "Installing vscode extensions"
code --install-extension ms-vscode-remote.remote-ssh
code --install-extension ms-vscode-remote.remote-ssh-edit
code --install-extension ms-vscode.cpptools
code --install-extension ms-python.python
code --install-extension bazelbuild.vscode-bazel
code --install-extension zxh404.vscode-proto3

echo "Installing new encryption key for user (please enter your JumpCloud password or another password you will remember"
sudo cryptsetup luksAddKey /dev/nvme0n1p3

echo "User setup completed!  Please log out and back in, and then complete your personal setup."


------
#!/bin/bash
# configure_hostname.sh

USERNAME="$1"

if [ -z "$USERNAME" ]; then
  echo "Must give the username as a parameter: $0 <username>"
  exit 1
fi

SERIAL=$(sudo dmidecode | grep "Serial Number" | head -n1 | awk '{print $3}' | sed -e 's/.*\(....\)$/\1/g')

HOSTNAME="AL-WS-$USERNAME-$SERIAL"

echo "Username: $USERNAME"
echo "Serial:   $SERIAL"
echo "Hostname: $HOSTNAME"

echo -n "Press enter to continue or Ctrl+C if this is incorrect"
read

cat /etc/hosts | sed "s/saildrone-XPS/$HOSTNAME/g" > /tmp/hosts
sudo mv /tmp/hosts /etc/hosts
sudo chmod 644 /etc/hosts

echo $HOSTNAME | sudo tee /etc/hostname >/dev/null
sudo chmod 644 /etc/hostname

-----
#!/bin/bash -x

curl --tlsv1.2 --silent --show-error --header 'x-connect-key: 3d315b0b18cf95c2e6e56815c4c9ccd769ee20b5' https://kickstart.jumpcloud.com/Kickstart | sudo bash



