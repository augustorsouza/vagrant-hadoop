Vagrant e Hadoop
==============

Este é um projeto para auxiliar que deseja montar um cluster em uma única máquina utilizando Virtual Box como provider das VMs e configurar a distribuição CDH (da Cloudera) do Apache Hadoop neste cluster.

Como configurar VMs VirtualBox com Vagrant?
------------------------------------------

Instale a aplicação Oracle Virtual Box (https://www.virtualbox.org/) e depos instale o Vagrant (http://www.vagrantup.com/).

Agora crie a box do Vagrant chamada "precise64" que trata-se de uma distribuição de Ubuntu. Para isso, rode o comando:
```
$ vagrant box add precise64 http://files.vagrantup.com/precise64.box
```

Em seguinda, clone este repositório e inicialize o cluster
```
$ git clone https://github.com/augustorsouza/vagrant-hadoop.git
$ cd vagrant-hadoop
$ vagrant up # opcionalmente, voce pode configurar a quantidade de memória das VMs com "MEMSIZE=X vagrant up", o valor padrão para X é 3072 MB.
```

Esta etapa é opcional, mas facilita o acesso às máquinas virtuais do cluster. Configure o seu /etc/hosts para apontar para os IPs das máquinas do cluster, adicionando as linhas a seguir a estes arquivo:
```
10.211.55.100   vm-cluster-node1
10.211.55.101   vm-cluster-node2
10.211.55.102   vm-cluster-node3
10.211.55.103   vm-cluster-node4
10.211.55.104   vm-cluster-node5
10.211.55.105   vm-cluster-client
```

Pronto, agora acesse no seu navegador o endereço http://vm-cluster-node1:7180 para iniciar o Cloudera Manager na máquina master e complete o wizard para terminar de configurar seu cluster utilizando Hadoop.


Links de referência:
-------------------
* http://blog.cloudera.com/blog/2013/04/how-to-use-vagrant-to-set-up-a-virtual-hadoop-cluster/
* http://www.vagrantbox.es/
* http://www.vagrantup.com/
* https://www.virtualbox.org/
