---
title: Wdrażanie aplikacji na zestawach skalowania maszyn wirtualnych na platformie Azure za pomocą rozwiązania Ansible
description: Dowiedz się, jak skonfigurować zestaw skalowania maszyn wirtualnych i wdrażanie aplikacji w skali maszyny wirtualnej, ustaw na platformie Azure za pomocą rozwiązania Ansible
ms.service: ansible
keywords: rozwiązanie ansible, azure, devops, bash, elementu playbook, maszyny wirtualnej, zestawu skalowania maszyn wirtualnych, zestawu skalowania maszyn wirtualnych
author: tomarcher
manager: jpconnock
editor: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.date: 07/11/2018
ms.author: tarcher
ms.openlocfilehash: b9c8058606e13c0db4908530e98cddb69d2caf50
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/13/2018
ms.locfileid: "39008853"
---
# <a name="deploy-applications-to-virtual-machine-scale-sets-in-azure-using-ansible"></a>Wdrażanie aplikacji na zestawach skalowania maszyn wirtualnych na platformie Azure za pomocą rozwiązania Ansible
Rozwiązanie Ansible pozwala zautomatyzować procesy wdrażania i konfiguracji zasobów w danym środowisku. Rozwiązanie Ansible służy do wdrażania aplikacji na platformie Azure. W tym artykule przedstawiono sposób wdrażania aplikacji języka Java do zestawu skalowania maszyn wirtualnych platformy Azure (zestawu skalowania maszyn wirtualnych).  

## <a name="prerequisites"></a>Wymagania wstępne
- **Subskrypcja platformy Azure** — Jeśli nie masz subskrypcji platformy Azure, Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) przed przystąpieniem do wykonywania.
- **Konfigurowanie rozwiązania Ansible** - [Azure Utwórz poświadczenia i konfigurowanie rozwiązania Ansible](../virtual-machines/linux/ansible-install-configure.md#create-azure-credentials)
- **Rozwiązanie Ansible i moduły zestawu Azure Python SDK** 
  - [CentOS 7.4](../virtual-machines/linux/ansible-install-configure.md#centos-74)
  - [Ubuntu 16.04 LTS](../virtual-machines/linux/ansible-install-configure.md#ubuntu-1604-lts)
  - [SLES 12 Z DODATKIEM SP2](../virtual-machines/linux/ansible-install-configure.md#sles-12-sp2)
- **Zestaw skalowania maszyn wirtualnych** — Jeśli nie masz jeszcze skalowania maszyn wirtualnych zestawu, można [tworzenie maszyny wirtualnej zestawu skalowania przy użyciu rozwiązania Ansible](ansible-create-configure-vmss.md). 
- **git** - [git](https://git-scm.com) jest używany do pobierania próbki Java, w tym samouczku używane.
- **Java SE Development Kit (JDK)** -JDK służy do utworzenia przykładowego projektu języka Java.
- **Narzędzia do kompilacji narzędzia Apache Maven** — [narzędzia do kompilacji narzędzia Apache Maven](https://maven.apache.org/download.cgi) są używane do utworzenia przykładowego projektu języka Java.

> [!Note]
> Ansible 2.6 jest wymagana do uruchamiania następujących przykładowych elementów playbook w ramach tego samouczka. 

## <a name="get-host-information"></a>Uzyskiwanie informacji dotyczących hosta

W tej sekcji pokazano, jak za pomocą rozwiązania Ansible, można pobrać informacji o hoście grupy maszyn wirtualnych platformy Azure. Poniżej przedstawiono playbook rozwiązania Ansible próbki. Ten kod pobiera publiczne adresy IP i równoważenia obciążenia w ramach określonej grupy zasobów i tworzy grupy hostów o nazwie **saclesethosts** w spisie. 

Zapisz następujący element playbook przykładowe jako `get-hosts-tasks.yml`: 

  ```yaml
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

W tej sekcji usługa git umożliwia klonowanie projektu przykładowego Java z usługi GitHub i skompiluj projekt. Zapisz poniższy element playbook jako `app.yml`:

  ```yaml
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

Uruchom element playbook rozwiązania Ansible przykładowe za pomocą następującego polecenia:

  ```bash
  ansible-playbook app.yml
  ```

Dane wyjściowe polecenia playbook rozwiązania ansible przedstawia się podobnie do poniższego, gdzie wyświetlone utworzone sklonować przykładową aplikację, z usługi GitHub:

  ```bash
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

## <a name="deploy-the-application-to-vmss"></a>Wdrażanie aplikacji do zestawu skalowania maszyn wirtualnych

Następujących sekcji w element playbook rozwiązania Ansible instaluje środowiska JRE (Java Runtime Environment) w grupie hostów o nazwie **saclesethosts**i wdraża aplikację Java do grupy hostów o nazwie **saclesethosts**: 

(Zmień `admin_password` do własnego hasła.)

  ```yaml
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

Możesz zapisać poprzedni element playbook rozwiązania Ansible przykładowe jako `vmss-setup-deploy.yml`, lub [Pobierz Podręcznik cały przykładowy](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss). 

Aby używać protokołu ssh typu połączenia przy użyciu hasła, należy zainstalować sshpass program. 
  - Ubunto 16.04, uruchom polecenie `apt-get install sshpass`.
  - CentOS 7.4, uruchom polecenie `yum install sshpass`.

Może zostać wyświetlony błąd taki jak **przy użyciu hasła SSH, zamiast klucza jest niemożliwe, ponieważ klucz hosta sprawdzanie jest włączone i sshpass nie obsługuje tego.  Dodaj odcisk palca tego hosta do pliku known_hosts sobie z tego hosta.** Jeśli zostanie wyświetlony ten błąd, możesz wyłączyć klucz hosta, sprawdzanie, dodając następujący wiersz do jednej `/etc/ansible/ansible.cfg` pliku lub `~/.ansible.cfg` pliku:
  ```bash
  [defaults]
  host_key_checking = False
  ```

Uruchom element playbook za pomocą następującego polecenia:

  ```bash
  ansible-playbook vmss-setup-deploy.yml
  ```

Dane wyjściowe z polecenia playbook rozwiązania ansible wskazuje, że zainstalowano przykładowej aplikacji Java do grupy hostów w zestawie skalowania maszyn wirtualnych:

  ```bash
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

Gratulacje! Aplikacja jest teraz uruchomiona na platformie Azure. Możesz teraz przejść do adresu URL usługi równoważenia obciążenia dla zestawu skalowania maszyn wirtualnych:

![Aplikacja Java uruchomiona w skali maszyny wirtualnej jest ustawiony na platformie Azure.](media/ansible-deploy-app-vmss/ansible-deploy-app-vmss.png)

## <a name="next-steps"></a>Kolejne kroki
> [!div class="nextstepaction"] 
> [Rozwiązanie Ansible przykładowe Podręcznik dla zestawu skalowania maszyn wirtualnych](https://github.com/Azure-Samples/ansible-playbooks/tree/master/vmss)