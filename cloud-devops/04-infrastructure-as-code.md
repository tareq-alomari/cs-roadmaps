# 04 — Infrastructure as Code

انظر `03-aws-azure-gcp.md` لأمثلة Terraform.

---

## Ansible — Configuration Management

```yaml
# playbook.yml
- hosts: webservers
  become: yes
  tasks:
    - name: Install nginx
      apt:
        name: nginx
        state: present

    - name: Copy config
      template:
        src: nginx.conf.j2
        dest: /etc/nginx/nginx.conf
      notify: restart nginx

    - name: Start nginx
      service:
        name: nginx
        state: started
        enabled: yes

  handlers:
    - name: restart nginx
      service:
        name: nginx
        state: restarted
```

```bash
ansible-playbook -i inventory.ini playbook.yml
ansible all -m ping -i inventory.ini
```

---

## GitOps — ArgoCD

```yaml
# ArgoCD Application
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: my-app
spec:
  source:
    repoURL: https://github.com/org/repo
    path: k8s/
    targetRevision: main
  destination:
    server: https://kubernetes.default.svc
    namespace: production
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
```

---

## ⏱️ الوقت المقدر: 4-6 أسابيع

## مصادر
- [Ansible Documentation](https://docs.ansible.com/)
- [ArgoCD Documentation](https://argo-cd.readthedocs.io/)
