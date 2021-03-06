# -*- mode: ruby -*-
# vi: set ft=ruby :
#
# Licensed to the Apache Software Foundation (ASF) under one
# or more contributor license agreements.  See the NOTICE file
# distributed with this work for additional information
# regarding copyright ownership.  The ASF licenses this file
# to you under the Apache License, Version 2.0 (the
# "License"); you may not use this file except in compliance
# with the License.  You may obtain a copy of the License at
#
#   http://www.apache.org/licenses/LICENSE-2.0
#
#   Unless required by applicable law or agreed to in writing,
#   software distributed under the License is distributed on an
#   "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
#   KIND, either express or implied.  See the License for the
#   specific language governing permissions and limitations
#   under the License.

Vagrant.configure(2) do |config|

  # How much memory does the master need to test the setup? How much do the
  # workers need? I have no clue.
  config.vm.provider "virtualbox" do |vb|
    vb.memory = "1024"
    # vb.gui = true # enable this to start vbox with GUI to debug problems on startup
  end

  # The base boxes also have the Vagrant ssh key copied into root's
  # authorized_keys so we can ssh into the box via root. This way, there is no
  # mention of the user vagrant in the Ansible scripts.
  config.ssh.username = "root"

  config.vm.define "couchdb-jenkins-master" do |node|
    node.vm.box = "couchdb-ci-ubuntu-14.04"
    node.vm.hostname = "master"
    node.vm.network "forwarded_port", guest:   80, host: 10080
    node.vm.network "forwarded_port", guest: 8080, host: 18080
    node.vm.network "private_network", ip: "10.20.1.2"
    node.vm.provision :hosts
  end

  config.vm.define "couchdb-ubuntu-14.04-worker" do |node|
    node.vm.box = "couchdb-ci-ubuntu-14.04"
    node.vm.hostname = "ubuntu1404"
    node.vm.network "private_network", ip: "10.20.1.3"
    node.vm.provision :hosts
  end


  # Workaround for "stdin: is not a tty error" -- make Vagrants ssh shell a
  # non-login one. See https://github.com/mitchellh/vagrant/issues/1673#issuecomment-28288042
  config.ssh.shell = "bash -c 'BASH_ENV=/etc/profile exec bash'"

  config.vm.provision "ansible" do |ansible|
    ansible.playbook = "ansible/site.yml"
    ansible.groups = {
      "jenkins-master" => ["couchdb-jenkins-master"],
      "jenkins-workers" => ["couchdb-ubuntu-14.04-worker"],
      "ubuntu-workers" => ["couchdb-ubuntu-14.04-worker"]
    }
  end

end
