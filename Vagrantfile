# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|


 config.vm.define "worker0" do |worker0|
   worker0.vm.hostname = "worker0"
   worker0.vm.box = "centos/stream8"
   worker0.vm.provision "shell", inline: <<-SHELL
     sudo dnf module disable container-tools:rhel8 -y
     sudo dnf module enable container-tools:3.0 -y
     sudo dnf clean metadata
     sudo dnf install -y git python3-pip python3-virtualenv
     sudo dnf install -y "@Development tools" 
     sudo dnf install -y libxml2-devel libxslt-devel crun
     su - vagrant -c "pip3 install --upgrade pip --user"
     su - vagrant -c "pip3 install setuptools-rust ansible --user"     
     git clone  https://opendev.org/opendev/base-jobs.git
     git clone https://opendev.org/zuul/zuul-jobs.git
     git clone https://opendev.org/openstack/tripleo-ansible.git
     echo "localhost ansible_connection=local" > /home/vagrant/inventory
     mkdir -p /home/vagrant/.ansible/roles
     cp -a /home/vagrant/zuul-jobs/roles/* /home/vagrant/.ansible/roles/
     cp -a /home/vagrant/base-jobs/roles/* /home/vagrant/.ansible/roles/
     mkdir -p /home/vagrant/.ansible/plugins/modules/
     cp -a /home/vagrant/tripleo-ansible/tripleo_ansible/ansible_plugins/* /home/vagrant/.ansible/plugins/
     sudo chown -R vagrant:vagrant /home/vagrant/*
     sudo chown -R vagrant:vagrant .ansible
     ssh-keygen  -t rsa -f /home/vagrant/.ssh/id_rsa  -P ''
     sudo chown -R vagrant:vagrant /home/vagrant/.ssh/*
     cat /home/vagrant/.ssh/id_rsa.pub >> /home/vagrant/.ssh/authorized_keys
     cat > /home/vagrant/0001-Vagrant-test.patch <<EOF
From e3fecc5531140ab3b2981e92aa17b39f49fcbbf4 Mon Sep 17 00:00:00 2001
From: Your Name <you@example.com>
Date: Thu, 7 Oct 2021 13:54:13 +0000
Subject: [PATCH] test vagrant

---
 zuul.d/playbooks/pre.yml | 6 +++---
 1 file changed, 3 insertions(+), 3 deletions(-)

diff --git a/zuul.d/playbooks/pre.yml b/zuul.d/playbooks/pre.yml
index 10d86cc6..6e9d6c37 100644
--- a/zuul.d/playbooks/pre.yml
+++ b/zuul.d/playbooks/pre.yml
@@ -4,7 +4,7 @@
   pre_tasks:
     - name: Set project path fact
       set_fact:
-        tripleo_ansible_project_path: "{{ ansible_user_dir }}/{{ zuul.projects['opendev.org/openstack/tripleo-ansible'].src_dir }}"
+        tripleo_ansible_project_path: "{{ ansible_user_dir }}/tripleo-ansible"
 
     - name: Ensure output dirs
       file:
@@ -104,7 +104,7 @@
   tasks:
     - name: Get Ansible Galaxy roles
       command: >-
-        {{ ansible_user_dir }}/test-python/bin/ansible-galaxy install
+        ansible-galaxy install
         -fr
         {{ tripleo_ansible_project_path }}/tripleo_ansible/ansible-role-requirements.yml
       environment:
@@ -112,7 +112,7 @@
 
     - name: Get Ansible Galaxy collections
       command: >-
-        {{ ansible_user_dir }}/test-python/bin/ansible-galaxy collection install
+        ansible-galaxy collection install
         -fr
         {{ tripleo_ansible_project_path }}/tripleo_ansible/requirements.yml
       environment:
-- 
2.27.0

EOF
     cd /home/vagrant/tripleo-ansible
     git apply /home/vagrant/0001-Vagrant-test.patch
     cd ~
     su - vagrant -c "ansible-playbook  -i /home/vagrant/inventory /home/vagrant/tripleo-ansible/tripleo_ansible/playbooks/prepare-test-host.yml -e ansible_user=vagrant -e ansible_user_dir=/home/vagrant/"
     su - vagrant -c "ansible-playbook  -i /home/vagrant/inventory /home/vagrant/tripleo-ansible/zuul.d/playbooks/pre.yml  -e ansible_user=vagrant -e ansible_user_dir=/home/vagrant"
     su - vagrant -c "echo 'source /home/vagrant/test-python/bin/activate' >> /home/vagrant/.bashrc"
  SHELL

  end

end

