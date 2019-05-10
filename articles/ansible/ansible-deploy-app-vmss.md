---
title: Samouczek — wdrażanie aplikacji na zestawach skalowania maszyn wirtualnych na platformie Azure za pomocą rozwiązania Ansible | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować zestawy skalowania maszyn wirtualnych platformy Azure i wdrażanie aplikacji w zestawie skalowania za pomocą rozwiązania Ansible
keywords: ansible, azure, devops, bash, playbook, virtual machine, virtual machine scale set, vmss
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: a44fd06ace9b21122f5f4253ac7d9601b54e6b62
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2019
ms.locfileid: "65231051"
---
# <a name="tutorial-deploy-apps-to-virtual-machine-scale-sets-in-azure-using-ansible"></a>Samouczek: Wdrażanie aplikacji na zestawach skalowania maszyn wirtualnych na platformie Azure za pomocą rozwiązania Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-vmss.md](../../includes/open-source-devops-intro-vmss.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Pobieranie informacji o hoście grupy maszyn wirtualnych platformy Azure
> * Klonowanie oraz tworzenie przykładowej aplikacji
> * Instalowanie środowiska JRE w (Java Runtime Environment) w zestawie skalowania
> * Wdrażanie aplikacji Java w zestawie skalowania

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)] 
[!INCLUDE [ansible-prereqs-vm-scale-set.md](../../includes/ansible-prereqs-vm-scale-set.md)]
- **Usługa git** - [git](https://git-scm.com) jest używana do pobrania przykładu w języku Java używanego w tym samouczku.
- **Java SE Development Kit (JDK)** — zestaw [JDK](https://aka.ms/azure-jdks) służy do utworzenia przykładowego projektu w języku Java.
- **Narzędzia Apache Maven** - [narzędzia Apache Maven](https://maven.apache.org/download.cgi) służy do utworzenia przykładowego projektu języka Java.

## <a name="get-host-information"></a>Pobieranie informacji o hoście

Kod elementu playbook w tej sekcji pobiera informacje dotyczące hosta dla grupy maszyn wirtualnych. Ten kod pobiera publiczne adresy IP usługi load balancer w określonej grupie zasobów i tworzy grupy hostów o nazwie `scalesethosts` w spisie.

Zapisz następujący przykładowy podręcznik jako `get-hosts-tasks.yml`:

  ```yml
  - name: Get facts for all Public IPs within a resource groups
    azure_rm_publicipaddress_facts:
      resource_group: "{{ resource_group }}"
    register: output_ip_address

  - name: Get loadbalancer info
    azure_rm_loadbalancer_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ loadbalancer_name }}"
    register: output

  - name: Add all hosts
    add_host:
      groups: scalesethosts
      hostname: "{{ output_ip_address.ansible_facts.azure_publicipaddresses[0].properties.ipAddress }}_{{ item.properties.frontendPort }}"
      ansible_host: "{{ output_ip_address.ansible_facts.azure_publicipaddresses[0].properties.ipAddress }}"
      ansible_port: "{{ item.properties.frontendPort }}"
      ansible_ssh_user: "{{ admin_username }}"
      ansible_ssh_pass: "{{ admin_password }}"
    with_items:
      - "{{ output.ansible_facts.azure_loadbalancers[0].properties.inboundNatRules }}"
  ```

## <a name="prepare-an-application-for-deployment"></a>Przygotowywanie aplikacji do wdrożenia

Kod elementu playbook w tej sekcji używa `git` sklonować przykładowy projekt języka Java z usługi GitHub oraz kompilowanie projektu. 

Zapisz następujący podręcznik jako `app.yml`:

  ```yml
  - hosts: localhost
    vars:
      repo_url: https://github.com/spring-guides/gs-spring-boot.git
      workspace: ~/src/helloworld

    tasks:
    - name: Git Clone sample app
      git:
        repo: "{{ repo_url }}"
        dest: "{{ workspace }}"

    - name: Build sample app
      shell: mvn package chdir="{{ workspace }}/complete"
  ```

Uruchom przykładowy podręcznik rozwiązania Ansible za pomocą następującego polecenia:

  ```bash
  ansible-playbook app.yml
  ```

Po uruchomieniu elementu playbook, zobaczysz dane wyjściowe podobne do następujących wyników:

  ```Output
  PLAY [localhost] 

  TASK [Gathering Facts] 
  ok: [localhost]

  TASK [Git Clone sample app] 
  changed: [localhost]

  TASK [Build sample app] 
  changed: [localhost]

  PLAY RECAP 
  localhost                  : ok=3    changed=2    unreachable=0    failed=0

  ```

## <a name="deploy-the-application-to-a-scale-set"></a>Wdrażanie aplikacji w zestawie skalowania

Kod elementu playbook w tej sekcji służy do:

* Zainstalować środowisko JRE w grupie hostów o nazwie `saclesethosts`
* Wdrażanie aplikacji Java do grupy hostów o nazwie `saclesethosts`

Istnieją dwa sposoby pobrania Podręcznik próbki:

* [Pobierz Podręcznik](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-setup-deploy.yml) i zapisać go w celu `vmss-setup-deploy.yml`.
* Utwórz nowy plik o nazwie `vmss-setup-deploy.yml` i skopiuj do niego następującą zawartość:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    scaleset_name: myScaleSet
    loadbalancer_name: myScaleSetLb
    admin_username: azureuser
    admin_password: "{{ admin_password }}"
  tasks:
  - include: get-hosts-tasks.yml

- name: Install JRE on a scale set
  hosts: scalesethosts
  become: yes
  vars:
    workspace: ~/src/helloworld
    admin_username: azureuser

  tasks:
  - name: Install JRE
    apt:
      name: default-jre
      update_cache: yes

  - name: Copy app to Azure VM
    copy:
      src: "{{ workspace }}/complete/target/gs-spring-boot-0.1.0.jar"
      dest: "/home/{{ admin_username }}/helloworld.jar"
      force: yes
      mode: 0755

  - name: Start the application
    shell: java -jar "/home/{{ admin_username }}/helloworld.jar" >/dev/null 2>&1 &
    async: 5000
    poll: 0
```

Przed uruchomieniem elementu playbook, zobacz następujące uwagi:

* W `vars` sekcji i Zastąp `{{ admin_password }}` symbol zastępczy przy użyciu własnego hasła.
* Aby używać protokołu ssh typu połączenia przy użyciu haseł, zainstaluj sshpass program:

    Ubuntu:

    ```bash
    apt-get install sshpass
    ```

    CentOS:

    ```bash
    yum install sshpass
    ```

* W niektórych środowiskach może zostać wyświetlony błąd dotyczący przy użyciu hasła SSH, zamiast klucza. Jeśli zostanie wyświetlony ten błąd, możesz wyłączyć klucz hosta, sprawdzanie, dodając następujący wiersz do `/etc/ansible/ansible.cfg` lub `~/.ansible.cfg`:

    ```bash
    [defaults]
    host_key_checking = False
    ```

Uruchom podręcznik za pomocą następującego polecenia:

  ```bash
  ansible-playbook vmss-setup-deploy.yml
  ```

Dane wyjściowe z polecenia playbook rozwiązania ansible wskazuje, że zainstalowano przykładowej aplikacji Java do grupy hostów w zestawie skalowania:

  ```Output
  PLAY [localhost]

  TASK [Gathering Facts]
  ok: [localhost]

  TASK [Get facts for all Public IPs within a resource groups]
  ok: [localhost]

  TASK [Get loadbalancer info]
  ok: [localhost]

  TASK [Add all hosts]
  changed: [localhost] ...

  PLAY [Install JRE on scale set]

  TASK [Gathering Facts]
  ok: [40.114.30.145_50000]
  ok: [40.114.30.145_50003]

  TASK [Copy app to Azure VM]
  changed: [40.114.30.145_50003]
  changed: [40.114.30.145_50000]

  TASK [Start the application]
  changed: [40.114.30.145_50000]
  changed: [40.114.30.145_50003]

  PLAY RECAP
  40.114.30.145_50000        : ok=4    changed=3    unreachable=0    failed=0
  40.114.30.145_50003        : ok=4    changed=3    unreachable=0    failed=0
  localhost                  : ok=4    changed=1    unreachable=0    failed=0
  ```

## <a name="verify-the-results"></a>Sprawdź wyniki

Sprawdź wyniki swojej pracy, przechodząc do adresu URL usługi równoważenia obciążenia dla zestawu skalowania:

![Aplikacja Java uruchomiona w ramach skalowania jest ustawiony na platformie Azure.](media/ansible-vmss-deploy/ansible-deploy-app-vmss.png)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Samouczek: Zestawy skalowania maszyn wirtualnych skalowania automatycznego na platformie Azure za pomocą rozwiązania Ansible](./ansible-auto-scale-vmss.md)