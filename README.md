# Ansible Playbook to setup a simple k8s cluster

Checkout repo and switch to the directorty `~/lx-cad-deploy`

Install ansible via virtual env

```
sudo apt install python3-pip python3.10-venv
python3.10 -m venv ~/.local/venv/ansible
source ~/.local/venv/ansible/bin/activate
pip install ansible
ansible --version
```

Install required roles:

```
ansible-galaxy install -f -p roles -r roles/requirements.yml
```

Generate token for the cluster, this assumes ansible vault has been setup

```
./roles/k8s/files/token-vault.sh ./group_vars/lx_cad/k8s-vault.yml
```

Configure firewalld and nginx for hosts

```
ansible-playbook -i hosts site.yml --tags=firewalld,nginx
```

Install Stack Orchestrator for control hosts

```
ansible-playbook -i hosts site.yml --tags=so --limit=so
```

Deploy k8s

```
ansible-playbook -i hosts site.yml --tags=k8s --limit=lx_cad
```

Install k8s helper tools

```
sudo ~/lx-cad-deploy/roles/k8s/files/get-kube-tools.sh
```

Verify cluster creation

```
kubie ctx lx-cad
kubectl get nodes -o wide
```

DNS Secret example

```
apiVersion: v1
data:
  access-token: XXX
kind: Secret
metadata:
  name: someprovider-dns
  namespace: cert-manager
```
