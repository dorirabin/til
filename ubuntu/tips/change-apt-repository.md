# Change Apt repository

## Example

From: ```http://us.archive.ubuntu.com/ubuntu/```

To: ```http://linux.yz.yamagata-u.ac.jp/pub/linux/ubuntu-archive/```

```shell
sudo sed -i.bak -e "s%http://us.archive.ubuntu.com/ubuntu/%http://linux.yz.yamagata-u.ac.jp/pub/linux/ubuntu-archive/%g" /etc/apt/sources.list
sudo apt-get update
```
