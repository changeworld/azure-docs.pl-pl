---
title: Samouczek — wdrażanie aplikacji w zestawach skalowania maszyn wirtualnych na platformie Azure przy użyciu rozwiązania ansible
description: Dowiedz się, jak używać rozwiązania ansible do konfigurowania zestawów skalowania maszyn wirtualnych platformy Azure i wdrażania aplikacji w zestawie skalowania
keywords: ansible, azure, devops, bash, playbook, virtual machine, virtual machine scale set, vmss
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 7c54790a3d988341caa65bdf0ce33c240f0580fb
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/18/2019
ms.locfileid: "74156391"
---
# <a name="tutorial-deploy-apps-to-virtual-machine-scale-sets-in-azure-using-ansible"></a>Samouczek: wdrażanie aplikacji w zestawach skalowania maszyn wirtualnych na platformie Azure przy użyciu rozwiązania ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-vmss.md](../../includes/open-source-devops-intro-vmss.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Pobieranie informacji o hoście dla grupy maszyn wirtualnych platformy Azure
> * Klonowanie i kompilowanie przykładowej aplikacji
> * Instalowanie środowiska JRE (Java Runtime Environment) na zestawie skalowania
> * Wdrażanie aplikacji Java w zestawie skalowania

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)] 
[!INCLUDE [ansible-prereqs-vm-scale-set.md](../../includes/ansible-prereqs-vm-scale-set.md)]
- **Usługa git** - [git](https://git-scm.com) jest używana do pobrania przykładu w języku Java używanego w tym samouczku.
- **Java SE Development Kit (JDK)** — zestaw [JDK](https://aka.ms/azure-jdks) służy do utworzenia przykładowego projektu w języku Java.
- Platforma **Apache Maven** - [Apache Maven](https://maven.apache.org/download.cgi) jest używana do kompilowania przykładowego projektu Java.

## <a name="get-host-information"></a>Pobieranie informacji o hoście

Kod element PlayBook w tej sekcji pobiera informacje o hoście dla grupy maszyn wirtualnych. Kod pobiera publiczne adresy IP i moduł równoważenia obciążenia w ramach określonej grupy zasobów i tworzy grupę hostów o nazwie `scalesethosts` w spisie.

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

Kod element PlayBook w tej sekcji używa `git` do klonowania przykładowego projektu Java z usługi GitHub i kompilowania projektu. 

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

Po uruchomieniu element PlayBook są wyświetlane dane wyjściowe podobne do następujących:

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

Kod element PlayBook w tej sekcji jest używany do:

* Zainstaluj środowisko JRE dla grupy hostów o nazwie `saclesethosts`
* Wdróż aplikację Java w grupie hostów o nazwie `saclesethosts`

Istnieją dwa sposoby uzyskania element PlayBook przykładu:

* [Pobierz element PlayBook](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-setup-deploy.yml) i Zapisz go w `vmss-setup-deploy.yml`.
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

Przed uruchomieniem element PlayBook zapoznaj się z następującymi uwagami:

* W sekcji `vars` Zastąp symbol zastępczy `{{ admin_password }}` własnym hasłem.
* Aby użyć typu połączenia SSH z hasłami, zainstaluj program sshpass:

    Ubuntu:

    ```bash
    apt-get install sshpass
    ```

    CentOS:

    ```bash
    yum install sshpass
    ```

* W niektórych środowiskach może zostać wyświetlony błąd dotyczący użycia hasła SSH zamiast klucza. W przypadku otrzymania tego błędu można wyłączyć sprawdzanie klucza hosta, dodając następujący wiersz do `/etc/ansible/ansible.cfg` lub `~/.ansible.cfg`:

    ```bash
    [defaults]
    host_key_checking = False
    ```

Uruchom podręcznik za pomocą następującego polecenia:

  ```bash
  ansible-playbook vmss-setup-deploy.yml
  ```

Wyjście z uruchamiania polecenia rozwiązania ansible-element PlayBook wskazuje, że przykładowa aplikacja Java została zainstalowana do grupy hostów zestawu skalowania:

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

Sprawdź wyniki pracy, przechodząc do adresu URL modułu równoważenia obciążenia dla zestawu skalowania:

![Aplikacja Java działająca w zestawie skalowania na platformie Azure.](media/ansible-vmss-deploy/ansible-deploy-app-vmss.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: automatyczne skalowanie zestawów skalowania maszyn wirtualnych na platformie Azure przy użyciu rozwiązania ansible](./ansible-auto-scale-vmss.md)