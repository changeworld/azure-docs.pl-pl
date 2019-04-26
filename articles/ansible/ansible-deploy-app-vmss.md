---
title: Wdrażanie aplikacji w zestawach skalowania maszyn wirtualnych na platformie Azure przy użyciu rozwiązania Ansible
description: Dowiedz się, jak skonfigurować zestaw skalowania maszyn wirtualnych i wdrożyć aplikację w zestawie skalowania maszyn wirtualnych na platformie Azure za pomocą rozwiązania Ansible
ms.service: azure
keywords: ansible, azure, devops, bash, playbook, virtual machine, virtual machine scale set, vmss
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 09/11/2018
ms.openlocfilehash: 2214dd9505dff86ac26f01967a360140dee0069f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60396895"
---
# <a name="deploy-applications-to-virtual-machine-scale-sets-in-azure-using-ansible"></a>Wdrażanie aplikacji w zestawach skalowania maszyn wirtualnych na platformie Azure przy użyciu rozwiązania Ansible
Rozwiązanie Ansible umożliwia zautomatyzowanie wdrażania i konfigurowania zasobów w Twoim środowisku. Możesz go użyć do wdrażania aplikacji na platformie Azure. W tym artykule przedstawiono sposób wdrażania aplikacji Java w zestawie skalowania maszyn wirtualnych platformy Azure (VMSS).

## <a name="prerequisites"></a>Wymagania wstępne
- **Subskrypcja Azure** — jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]
- **Zestaw skalowania maszyn wirtualnych** — jeśli nie masz jeszcze zestawu skalowania maszyn wirtualnych, możesz [utworzyć zestaw skalowania maszyn wirtualnych przy użyciu rozwiązania Ansible](ansible-create-configure-vmss.md).
- **Usługa git** - [git](https://git-scm.com) jest używana do pobrania przykładu w języku Java używanego w tym samouczku.
- **Java SE Development Kit (JDK)** — zestaw [JDK](https://aka.ms/azure-jdks) służy do utworzenia przykładowego projektu w języku Java.
- **Narzędzia kompilacji Apache Maven** — [narzędzia kompilacji Apache Maven](https://maven.apache.org/download.cgi) służą do utworzenia przykładowego projektu w języku Java.

> [!Note]
> Rozwiązanie Ansible 2.6 jest wymagane do uruchamiania następujących przykładowych podręczników w ramach tego samouczka.

## <a name="get-host-information"></a>Pobieranie informacji o hoście

W tej sekcji pokazano, jak za pomocą rozwiązania Ansible można pobrać informacje o hoście dla grupy maszyn wirtualnych platformy Azure. Poniżej znajduje się przykładowy podręcznik rozwiązania Ansible. Ten kod pobiera publiczne adresy IP i moduł równoważenia obciążenia w ramach określonej grupy zasobów i tworzy grupę hostów o nazwie **scalesethosts** w spisie.

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

W tej sekcji usługa Git umożliwia sklonowanie przykładowego projektu w języku Java z witryny GitHub i utworzenie projektu. Zapisz następujący podręcznik jako `app.yml`:

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

Dane wyjściowe z polecenia podręcznika rozwiązania Ansible przypominają te przedstawione poniżej, które pokazują, że utworzona przykładowa aplikacja została sklonowana z witryny GitHub:

  ```Output
  PLAY [localhost] **********************************************************

  TASK [Gathering Facts] ****************************************************
  ok: [localhost]

  TASK [Git Clone sample app] ***************************************************************************
  changed: [localhost]

  TASK [Build sample app] ***************************************************
  changed: [localhost]

  PLAY RECAP ***************************************************************************
  localhost                  : ok=3    changed=2    unreachable=0    failed=0

  ```

## <a name="deploy-the-application-to-vmss"></a>Wdrażanie aplikacji w zestawie skalowania maszyn wirtualnych

Następująca sekcja w podręczniku rozwiązania Ansible instaluje środowisko JRE (Java Runtime Environment) w grupie hostów o nazwie **saclesethosts** i wdraża aplikację języka Java w grupie hostów o nazwie **saclesethosts**:

(Zmień wartość `admin_password` na własne hasło).

  ```yml
  - hosts: localhost
    vars:
      resource_group: myResourceGroup
      scaleset_name: myVMSS
      loadbalancer_name: myVMSSlb
      admin_username: azureuser
      admin_password: "your_password"
    tasks:
    - include: get-hosts-tasks.yml

  - name: Install JRE on VMSS
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

Poprzedni przykładowy podręcznik rozwiązania Ansible możesz zapisać jako `vmss-setup-deploy.yml` lub [pobrać cały przykładowy podręcznik](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss).

Aby można było używać typu połączenia ssh z hasłami, należy zainstalować program SSHPass.
  - Dla systemu Ubuntu 16.04 uruchom polecenie `apt-get install sshpass`.
  - Dla systemu CentOS 7.4 uruchom polecenie `yum install sshpass`.

Może pojawić się błąd informujący, że **Używanie hasła SSH zamiast klucza jest niemożliwe, ponieważ włączono sprawdzanie klucza hosta, które nie jest obsługiwane przez program SSHPass. Dodaj odcisk palca tego hosta do pliku known_hosts, aby zarządzać tym hostem.** Jeśli pojawi się ten błąd, możesz wyłączyć sprawdzanie klucza hosta, dodając następujący wiersz do pliku `/etc/ansible/ansible.cfg` lub pliku `~/.ansible.cfg`:
  ```bash
  [defaults]
  host_key_checking = False
  ```

Uruchom podręcznik za pomocą następującego polecenia:

  ```bash
  ansible-playbook vmss-setup-deploy.yml
  ```

Dane wyjściowe z uruchomionego polecenia podręcznika rozwiązania Ansible wskazują, że przykładowa aplikacja Java została zainstalowana w grupie hostów zestawu skalowania maszyn wirtualnych:

  ```Output
  PLAY [localhost] **********************************************************

  TASK [Gathering Facts] ****************************************************
  ok: [localhost]

  TASK [Get facts for all Public IPs within a resource groups] **********************************************
  ok: [localhost]

  TASK [Get loadbalancer info] ****************************************************************************
  ok: [localhost]

  TASK [Add all hosts] *****************************************************************************
  changed: [localhost] ...

  PLAY [Install JRE on VMSS] *****************************************************************************

  TASK [Gathering Facts] *****************************************************************************
  ok: [40.114.30.145_50000]
  ok: [40.114.30.145_50003]

  TASK [Copy app to Azure VM] *****************************************************************************
  changed: [40.114.30.145_50003]
  changed: [40.114.30.145_50000]

  TASK [Start the application] ********************************************************************
  changed: [40.114.30.145_50000]
  changed: [40.114.30.145_50003]

  PLAY RECAP ************************************************************************************************
  40.114.30.145_50000        : ok=4    changed=3    unreachable=0    failed=0
  40.114.30.145_50003        : ok=4    changed=3    unreachable=0    failed=0
  localhost                  : ok=4    changed=1    unreachable=0    failed=0
  ```

Gratulacje! Twoja aplikacja działa teraz na platformie Azure. Możesz teraz przejść do adresu URL modułu równoważenia obciążenia zestawu skalowania maszyn wirtualnych:

![Aplikacja języka Java uruchomiona w zestawie skalowania maszyn wirtualnych na platformie Azure.](media/ansible-deploy-app-vmss/ansible-deploy-app-vmss.png)

## <a name="next-steps"></a>Kolejne kroki
> [!div class="nextstepaction"]
> [Automatyczne skalowanie zestawu skalowania maszyn wirtualnych przy użyciu rozwiązania Ansible](https://docs.microsoft.com/azure/ansible/ansible-auto-scale-vmss)
