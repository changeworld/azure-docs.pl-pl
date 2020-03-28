---
title: Samouczek — wdrażanie aplikacji do zestawów skalowania maszyny wirtualnej na platformie Azure przy użyciu ansible
description: Dowiedz się, jak skonfigurować zestawy skalowania maszyny wirtualnej platformy Azure i wdrożyć aplikację w zestawie skalowania
keywords: ansible, azure, devops, bash, playbook, virtual machine, virtual machine scale set, vmss
ms.topic: tutorial
ms.date: 01/13/2020
ms.openlocfilehash: d638ae3f0c33734b42ef5456772fcd2bc62e35a4
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75940861"
---
# <a name="tutorial-deploy-apps-to-virtual-machine-scale-sets-in-azure-using-ansible"></a>Samouczek: Wdrażanie aplikacji do zestawów skalowania maszyny wirtualnej na platformie Azure przy użyciu ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-vmss.md](../../includes/open-source-devops-intro-vmss.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Pobieranie informacji o hoście dla grupy maszyn wirtualnych platformy Azure
> * Klonowanie i tworzenie przykładowej aplikacji
> * Instalowanie środowiska JRE (Java Runtime Environment) w zestawie skalowania
> * Wdrażanie aplikacji Java do zestawu skalowania

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)] 
[!INCLUDE [ansible-prereqs-vm-scale-set.md](../../includes/ansible-prereqs-vm-scale-set.md)]
- **git** - [git](https://git-scm.com) służy do pobierania próbki Java używane w tym poradniku.
- **Java SE Development Kit (JDK)** — zestaw [JDK](https://aka.ms/azure-jdks) służy do utworzenia przykładowego projektu w języku Java.
- **Apache Maven** - [Apache Maven](https://maven.apache.org/download.cgi) jest używany do tworzenia przykładowego projektu Java.

## <a name="get-host-information"></a>Pobieranie informacji o hoście

Kod podręcznika w tej sekcji pobiera informacje o hoście dla grupy maszyn wirtualnych. Kod pobiera publiczne adresy IP i moduł równoważenia obciążenia `scalesethosts` w określonej grupie zasobów i tworzy grupę hostów o nazwie w magazynie.

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
      hostname: "{{ output_ip_address.publicipaddresses[0].ip_address }}_{{ item.properties.frontendPort }}"
      ansible_host: "{{ output_ip_address.publicipaddresses[0].ip_address }}"
      ansible_port: "{{ item.properties.frontendPort }}"
      ansible_ssh_user: "{{ admin_username }}"
      ansible_ssh_pass: "{{ admin_password }}"
    with_items:
      - "{{ output.ansible_info.azure_loadbalancers[0].properties.inboundNatRules }}"
  ```

## <a name="prepare-an-application-for-deployment"></a>Przygotowywanie aplikacji do wdrożenia

Kod podręcznika w tej `git` sekcji używa do klonowania przykładowego projektu Java z gitHub i tworzy projekt. 

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

Po uruchomieniu podręcznika zobaczysz dane wyjściowe podobne do następujących wyników:

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

## <a name="deploy-the-application-to-a-scale-set"></a>Wdrażanie aplikacji do zestawu skalowania

Kod podręcznika w tej sekcji służy do:

* Instalowanie środowiska JRE w grupie hostów o nazwie`saclesethosts`
* Wdrażanie aplikacji Java w grupie hostów o nazwie`saclesethosts`

Przykładowy podręcznik można uzyskać na dwa sposoby:

* [Pobierz podręcznik](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-setup-deploy.yml) i zapisz `vmss-setup-deploy.yml`go w pliku .
* Utwórz nowy `vmss-setup-deploy.yml` plik o nazwie i skopiuj do niego następującą zawartość:

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

Przed uruchomieniem podręcznika zapoznaj się z następującymi uwagami:

* W `vars` sekcji zastąp symbol zastępczy `{{ admin_password }}` własnym hasłem.
* Aby użyć typu połączenia ssh z hasłami, zainstaluj program sshpass:

     Ubuntu:

    ```bash
    apt-get install sshpass
    ```

    Centos:

    ```bash
    yum install sshpass
    ```

* W niektórych środowiskach może pojawić się błąd dotyczący używania hasła SSH zamiast klucza. Jeśli ten błąd zostanie wyświetlony, możesz wyłączyć sprawdzanie klucza `/etc/ansible/ansible.cfg` hosta, dodając następujący wiersz do lub: `~/.ansible.cfg`

    ```bash
    [defaults]
    host_key_checking = False
    ```

Uruchom podręcznik za pomocą następującego polecenia:

  ```bash
  ansible-playbook vmss-setup-deploy.yml
  ```

Dane wyjściowe z uruchomienia polecenia ansible-playbook wskazują, że przykładowa aplikacja Java została zainstalowana w grupie hostów zestawu skalowania:

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

## <a name="verify-the-results"></a>Weryfikowanie wyników

Sprawdź wyniki swojej pracy, przechodząc do adresu URL modułu równoważenia obciążenia dla zestawu skalowania:

![Aplikacja Java działająca w skali ustawionej na platformie Azure.](media/ansible-vmss-deploy/ansible-deploy-app-vmss.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: Automatyczne skalowanie zestawów skalowania maszyn wirtualnych na platformie Azure przy użyciu ansible](./ansible-auto-scale-vmss.md)