# Silent install Oracle JDK 8 on Ubuntu

Problem: During installation of Oracle JDK 8 on Ubuntu, we need to agree on license agreement manually.

Solution: [Installing Java Automatically (With Silent Option)](http://askubuntu.com/questions/190582/installing-java-automatically-with-silent-option)

Shell script:

```shell
sudo apt-get install -y python-software-properties debconf-utils
sudo add-apt-repository -y ppa:webupd8team/java
sudo apt-get update
echo "oracle-java8-installer shared/accepted-oracle-license-v1-1 select true" | sudo debconf-set-selections
sudo apt-get install -y oracle-java8-installer
```

Ansible:

```yaml
---
- name: Add apt repository
  apt_repository:
      repo: 'ppa:webupd8team/java'
      state: present
      update_cache: yes

- name: Accept license
  debconf:
    name: 'oracle-java8-installer'
    question: 'shared/accepted-oracle-license-v1-1'
    value: 'true'
    vtype: 'select'

- name: Install java
  apt: name={{ item }} state=present
  with_items:
      - oracle-java8-installer
      - ca-certificates
      - oracle-java8-set-default
```