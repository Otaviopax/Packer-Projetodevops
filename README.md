# Packer - Provadevops

Este repósitório contém todos os arquivos necessarios para se realizar o provisionamento das maquinas utilizadas para este projeto.
Para realizar o provisionamento das maquinas é necessário:

- # 1 Clonar este repositório
É preciso que clone este repositório para utilizar-se de seus arquivos.

- # 2 Baixe os binários do Packer e do Vagrant
Para executar os comando você utilizará eles, os binários estão disponiveis nos links a seguir:

[Packer](https://developer.hashicorp.com/packer/install)

[Vagrant](https://developer.hashicorp.com/vagrant/install)

Os binarios devem estar na mesma pasa que o arquivo packer.pkr.hcl.

Além disso será necessario que seu computador tenha tanto Python, quanto Ansible instalados em seu coputador.

- # 3 Executar os seguintes comandos, nesta ordem.
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

- # 4 Argocd
Após a utilização de todos os playbooks o ambiente virtual estará pronto e para que as aplicações sejam executadas será necessario que adicione elas às aplicações do Argocd, para fazer isso basta acessar a porta 8080 da maquina e então logar no Argocd coma o usuario admin e a senha gerada pelo seguinte comando:
~~~
kubectl get secret argocd-initial-admin-secret -n argocd -o jsonpath="{.data.password}" | base64 -d; echo
~~~
Após isso você deve clicar na opção de adicionar aplicação. Nesta opção você irá colocar um nome para a aplicação (em letras minuscualas), um link para o repositório da aplicação, selecionar as opções prune last, auto create namespace apply out of sync only, nomeie a aplicação como default, selecione o caminho do k8s.


- # 5 Exportar os IPs
Para exportas os IPs da aplicação basta executar o seguinte comando em diferentes terminais, para o front e o back end.
~~~
kubectl port-forward svc/<nome da aplicação> -n argocd --address 0.0.0.0 <A porta para qual a aplicação será esxportada>:<A porta da aplicação>
~~~
