# Trabalho Final – Fundamentos de DevOps

##### Aluno: Otávio Luiz de Souza da Silva

## 1. Introdução
	
 O projeto descrito neste documento técnico consiste em uma simples aplicação de um fórum de perguntas e respostas, o intuito de desenvolver está aplicação é adquirir maior compreensão sobre o funcionamento de contêineres, bem como aprender sobre o provisionamento de máquinas e outros conceitos fundamentais de DevOps. O projeto foi desenvolvido utilizando-se das seguintes tecnologias: Packer, para realizar a montagem da imagem da máquina virtual; Vagrant para realizar o provisionamento da máquina; Ansible para a instalação de dependências e configuração de serviços; Github, para armazenar os arquivos e versionamento; Dockerhub, para armazenar a imagem docker da aplicação desenvolvida e versionamento; Docker, Kubectl e Kind, para realizar o gerenciamento dos contêineres clusters e nós; Laravel para o desenvolvimento da aplicação back end; html, JavaScript e css para o desenvolvimento da aplicação front end; mysql para o desenvolvimento do banco de dados.
## 2. Escolha do Ambiente
	
 O ambiente feito para desenvolvimento do projeto foi criado utilizando os serviços do Vagrant e Ansible, com o Packer para criar a imagem da máquina virtual provisionada pelo Vagrant. Esse ambiente foi escolhido por não necessitar de contas em programas de terceiros, para o provisionamento, e permitir a execução de maneira local.
Para o ambiente foi feita uma máquina com 2 GB de memória RAM, 2 CPUs, 60 gb de espaço. O sistema operacional utilizado foi o Debian 12. 
## 3. Provisionamento
Para o provisionamento da máquina foram utilizados Vagrant e Ansible. Ambos os serviços foram escolhidos para que se tornasse possível realizar o provisionamento das máquinas de maneira local. 
Foram criados scripts de códigos individualmente para: A instalação do Nginx; A instalação do Docker; A instalação do Kind; A instalação do Kubect; A criação do cluster; A instalação do Argocd.
	Durante o desenvolvimento dos scripts, problemas que foram encontrados envolviam questões de permissão de usuário ou o intervalo entre a execução dos scripts.
## 4. Cluster Kubernetes
	
 As ferramentas utilizadas para o levantamento do cluster, foram: Docker; Kind; Kubectl. Foram levantados 3 nós para o cluster. Com um deles sendo o master. A configuração dos nós e das aplicações neles foi feita através do argocd.
## 5. GitOps com Argocd
	
 A instalação do Argocd e suas dependências foi realizada através do Ansible, bem como a exposição de sua porta. Todos os repositórios do Github tinham secrets que continham o usuário e o token do DockerHub, a configuração dos repositórios foram feitas de forma que fosse possível a execução dos actions sem nenhum problema.
	O deploy da aplicação foi executada de maneira automatizada através do Github Actions permitindo que o projeto funcionasse seguindo os conceitos de integração e entrega contínua. Após a execução do actions as aplicações são sincronizadas dentro do Argocd.
## 6. Aplicação
A aplicação desenvolvida consiste em um fórum de perguntas e respostas, desenvolvido como uma aplicação web. Ela foi dividida em front end, back end e banco de dados. O front end foi desenvolvido utilizando html, css e javascript, além do Nginx para realizar a disponibilização do front na web. O backend foi desenvolvido utilizando laravel, e em sua imagem foram passadas as informações necessárias para sua configuração. O banco de dados foi criado utilizando mysql, e em sua imagem docker o código necessário para a criação do DB é enviado.
## 7. Conclusão
	
 A criação e provisionamento das máquinas e aplicações, serviu como ótimo exemplo prático de devops e provisionamento de máquinas, fornecendo maior compreensão sobre o funcionamento de máquinas virtuais, provisionamento, entrega e integração contínua, além de configuração e gerenciamento de ips.
	Dentre dificuldades relacionadas ao projeto, uma das maiores encontradas foi a de configurar IPs e fazer as aplicações serem exportadas para a web. Outro problema encontrado foi a de se configurar uma máquina para que ela seja capaz de realizar todo o projeto necessário.
	Para fazer outro projeto, alteraria os códigos dos playbooks para garantir mais estabilidade, e provavelmente utilizaria outras ferramentas para que a automação do projeto seja mais rápida.
## 8. Link para Repositório
[Repositório das Máquinas Provisionadas](https://github.com/Otaviopax/Packer-Projetodevops)
[Repositório do Banco de Dados](https://github.com/Otaviopax/MySQL-Projetodevops.git)
[Repositório do Front end](https://github.com/victtows/Front-Projetodevops.git) 
[Repositório do Back end](https://github.com/FabioPYAug/AnonyQuest.git) 

## Packer - Provadevops

Este repósitório contém todos os arquivos necessarios para se realizar o provisionamento das maquinas utilizadas para este projeto.
Para realizar o provisionamento das maquinas é necessário:

- ## 1 Clonar este repositório
É preciso que clone este repositório para utilizar-se de seus arquivos.

- ## 2 Baixe os binários do Packer e do Vagrant
Para executar os comando você utilizará eles, os binários estão disponiveis nos links a seguir:

[Packer](https://developer.hashicorp.com/packer/install)

[Vagrant](https://developer.hashicorp.com/vagrant/install)

Os binarios devem estar na mesma pasa que o arquivo packer.pkr.hcl.

Além disso será necessario que seu computador tenha tanto Python, quanto Ansible instalados em seu coputador.

- ## 3 Executar os seguintes comandos, nesta ordem.
~~~
packer init .

packer plugin install github.com/hashicorp/virtualbox

packer plugin install github.com/hashicorp/vagrant

packer build debian.json

vagrant box add debian12 debian12.box

vagrant up

# No terminal da maquina hospedeira |

ssh-keygen # Não precisa preencher nenhuma opção

ssh-copy-id -i <caminho da chave gerada> vagrant@<ip da maquina virtual gerada>

# Na pasta do ansible |

ansible-playbook -i hosts install_nginx.yml
ansible-playbook -i hosts install_docker.yml
ansible-playbook -i hosts install_kind.yml
ansible-playbook -i hostsinstall_kubectl.yml
ansible-playbook -i hosts raise_nodes.yml
ansible-playbook -i hosts install_argocd.yml

# Após ter executado todos os comandos, caso deseje hostear o argocd novamente, basta executar o seguinte comando:

ansible-playbook -i hosts start_argocd.yml

~~~

- ## 4 Argocd
Após a utilização de todos os playbooks o ambiente virtual estará pronto e para que as aplicações sejam executadas será necessario que adicione elas às aplicações do Argocd, para fazer isso basta acessar a porta 8080 da maquina e então logar no Argocd coma o usuario admin e a senha gerada pelo seguinte comando:
~~~
kubectl get secret argocd-initial-admin-secret -n argocd -o jsonpath="{.data.password}" | base64 -d; echo
~~~
Após isso você deve clicar na opção de adicionar aplicação. Nesta opção você irá colocar um nome para a aplicação (em letras minuscualas), um link para o repositório da aplicação, selecionar as opções prune last, auto create namespace apply out of sync only, nomeie a aplicação como default, selecione o caminho do k8s.


- ## 5 Exportar os IPs
Para exportas os IPs da aplicação basta executar o seguinte comando em diferentes terminais, para o front e o back end.
~~~
kubectl port-forward svc/<nome da aplicação> -n argocd --address 0.0.0.0 <A porta para qual a aplicação será esxportada>:<A porta da aplicação>
~~~
