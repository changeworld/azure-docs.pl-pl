---
title: Samouczek — Konfigurowanie laboratoriów w usłudze Azure DevTest Labs, za pomocą rozwiązania Ansible | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować laboratorium w usłudze Azure DevTest Labs, za pomocą rozwiązania Ansible
ms.service: ansible
keywords: ansible, azure, devops, bash, playbook, devtest labs
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: c6bc4d50e4db52f772a137495658492018ee5360
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2019
ms.locfileid: "65230961"
---
# <a name="tutorial-configure-labs-in-azure-devtest-labs-using-ansible"></a>Samouczek: Skonfiguruj laboratoria w usłudze Azure DevTest Labs, za pomocą rozwiązania Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[Usługa Azure DevTest Labs](/azure/lab-services/devtest-lab-overview) umożliwia deweloperom automatyzację tworzenia maszyn wirtualnych środowisk dla swoich aplikacji. Te środowiska można skonfigurować dla aplikacji, rozwoju, testowania i szkolenia. 

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Tworzenie laboratorium
> * Ustawianie zasad laboratorium
> * Ustawianie harmonogramów laboratorium
> * Tworzenie sieci wirtualnej w laboratorium
> * Zdefiniuj źródło artefaktu dla laboratorium
> * Tworzenie maszyny Wirtualnej w laboratorium
> * Lista źródeł artefaktów i artefakty w laboratorium
> * Uzyskaj informacje o usłudze Azure Resource Manager dla źródeł artefaktów
> * Utwórz środowisko laboratoryjne
> * Tworzenie obrazu laboratorium
> * Usuń laboratorium

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-resource-group"></a>Tworzenie grupy zasobów

Fragment kodu z elementów playbook przykładowy tworzy grupę zasobów platformy Azure. Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

```yml
  - name: Create a resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"
```

## <a name="create-the-lab"></a>Tworzenie laboratorium

Następne zadanie tworzy laboratorium próbki.

```yml
- name: Create the lab
  azure_rm_devtestlab:
    resource_group: "{{ resource_group }}"
    name: "{{ lab_name }}"
    location: "{{ location }}"
    storage_type: standard
    premium_data_disks: no
  register: output_lab
```

## <a name="set-the-lab-policies"></a>Ustawianie zasad laboratorium

Można skonfigurować ustawienia zasad laboratorium. Można ustawić następujące wartości:

- `user_owned_lab_vm_count` jest to liczba maszyn wirtualnych mogą być właścicielami przez użytkownika
- `user_owned_lab_premium_vm_count` jest to liczba maszyn wirtualnych usługi premium mogą być właścicielami przez użytkownika
- `lab_vm_count` jest maksymalna liczba maszyn wirtualnych laboratorium
- `lab_premium_vm_count` jest maksymalna liczba laboratorium, maszyny wirtualne usługi premium
- `lab_vm_size` jest całkowitą pojemność komputerze dla maszyn wirtualnych laboratorium dozwolone
- `gallery_image` obrazy galerii dozwolone jest
- `user_owned_lab_vm_count_in_subnet` jest maksymalna liczba maszyn wirtualnych w podsieci z użytkownika
- `lab_target_cost` jest to koszt docelowej laboratorium

```yml
- name: Set the lab policies
  azure_rm_devtestlabpolicy:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
    policy_set_name: myDtlPolicySet
    name: myDtlPolicy
    fact_name: user_owned_lab_vm_count
    threshold: 5
```

## <a name="set-the-lab-schedules"></a>Ustawianie harmonogramów laboratorium

Zadanie próbkowania w tej sekcji służy do konfigurowania harmonogramu laboratorium. 

W poniższym fragmencie kodu `lab_vms_startup` wartość służy do określania czasu uruchamiania maszyny Wirtualnej. Podobnie, ustawienie `lab_vms_shutdown` wartość ustanawia czas zamykania maszyn wirtualnych laboratorium.

```yml
- name: Set the lab schedule
  azure_rm_devtestlabschedule:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
    name: lab_vms_shutdown
    time: "1030"
    time_zone_id: "UTC+12"
  register: output
```

## <a name="create-the-lab-virtual-network"></a>Tworzenie sieci wirtualnej w laboratorium

To następujące zadanie tworzy domyślnej sieci wirtualnej laboratorium.

```yml
- name: Create the lab virtual network
  azure_rm_devtestlabvirtualnetwork:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
    name: "{{ vn_name }}"
    location: "{{ location }}"
    description: My lab virtual network
  register: output
```

## <a name="define-an-artifact-source-for-the-lab"></a>Zdefiniuj źródło artefaktu dla laboratorium

Źródła artefaktów jest prawidłowo ze strukturą repozytorium GitHub, który zawiera definicję artefaktów i szablonów usługi Azure Resource Manager. Każdy laboratorium jest powiązana z wstępnie zdefiniowanych artefaktów publicznych. Wykonaj zadania pokazuje, jak tworzenie źródła artefaktu dla laboratorium.

```yml
- name: Define the lab artifacts source
  azure_rm_devtestlabartifactsource:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
    name: "{{ artifacts_name }}"
    uri: https://github.com/Azure/azure_preview_modules.git
    source_type: github
    folder_path: /tasks
    security_token: "{{ github_token }}"
```

## <a name="create-a-vm-within-the-lab"></a>Tworzenie maszyny Wirtualnej w laboratorium

Tworzenie maszyny Wirtualnej w laboratorium.

```yml
- name: Create a VM within the lab
  azure_rm_devtestlabvirtualmachine:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
    name: "{{ vm_name }}"
    notes: Virtual machine notes, just something....
    os_type: linux
    vm_size: Standard_A2_v2
    user_name: dtladmin
    password: ZSasfovobocu$$21!
    lab_subnet:
      virtual_network_name: "{{ vn_name }}"
      name: "{{ vn_name }}Subnet"
    disallow_public_ip_address: no
    image:
      offer: UbuntuServer
      publisher: Canonical
      sku: 16.04-LTS
      os_type: Linux
      version: latest
    artifacts:
      - source_name: "{{ artifacts_name }}"
        source_path: "/Artifacts/linux-install-mongodb"
    allow_claim: no
    expiration_date: "2029-02-22T01:49:12.117974Z"
```

## <a name="list-the-labs-artifact-sources-and-artifacts"></a>Lista źródeł artefaktów i artefakty w laboratorium

Aby wyświetlić listę wszystkich domyślne oraz źródła niestandardowe artefakty w laboratorium, należy użyć następujących zadań:

```yml
- name: List the artifact sources
  azure_rm_devtestlabartifactsource_facts:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
  register: output
- debug:
    var: output
```

Następujące zadanie zawiera listę wszystkich artefaktów:

```yml
- name: List the artifact facts
  azure_rm_devtestlabartifact_facts:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
    artifact_source_name: public repo
  register: output
- debug:
    var: output
```

## <a name="get-azure-resource-manager-information-for-the-artifact-sources"></a>Uzyskaj informacje o usłudze Azure Resource Manager dla źródeł artefaktów

Aby wyświetlić listę wszystkich szablonów usługi Azure Resource Manager w `public environment repository`, wstępnie zdefiniowanych repozytorium za pomocą szablonów:

```yml
- name: List the Azure Resource Manager template facts
  azure_rm_devtestlabartifactsource_facts:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
  register: output
- debug:
    var: output
```

I następujące zadanie pobiera szczegóły określonego szablonu usługi Azure Resource Manager z repozytorium:

```yml
- name: Get Azure Resource Manager template facts
  azure_rm_devtestlabarmtemplate_facts:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
    artifact_source_name: "public environment repo"
    name: ServiceFabric-LabCluster
  register: output
- debug:
    var: output
```

## <a name="create-the-lab-environment"></a>Utwórz środowisko laboratoryjne

Następujące zadanie tworzy środowisko laboratoryjne, na podstawie jednego z szablonów z repozytorium publicznego środowiska.

```yml
- name: Create the lab environment
      azure_rm_devtestlabenvironment:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        user_name: "@me"
        name: myEnvironment
        location: eastus
        deployment_template: "{{ output_lab.id }}/artifactSources/public environment repo/armTemplates/WebApp"
      register: output
```

## <a name="create-the-lab-image"></a>Tworzenie obrazu laboratorium

Następujące zadanie tworzy obraz maszyny wirtualnej. Obraz, który umożliwia tworzenie identycznych maszyn wirtualnych.

```yml
- name: Create the lab image
  azure_rm_devtestlabcustomimage:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
    name: myImage
    source_vm: "{{ output_vm.virtualmachines[0]['name'] }}"
    linux_os_state: non_deprovisioned
```

## <a name="delete-the-lab"></a>Usuń laboratorium

Aby usunąć laboratorium, należy użyć następujących zadań:

```yml
- name: Delete the lab
  azure_rm_devtestlab:
    resource_group: "{{ resource_group }}"
    name: "{{ lab_name }}"
    state: absent
  register: output
- name: Assert the change was correctly reported
  assert:
    that:
      - output.changed
```

## <a name="get-the-sample-playbook"></a>Pobierz Podręcznik próbki

Istnieją dwa sposoby pobrania Podręcznik pełny przykład:
- [Pobierz Podręcznik](https://github.com/Azure-Samples/ansible-playbooks/blob/master/devtestlab-create.yml) i zapisać go w celu `devtestlab-create.yml`.
- Utwórz nowy plik o nazwie `devtestlab-create.yml` i skopiuj do niego następującą zawartość:

```yml
---
- hosts: localhost
  #roles:
  #  - azure.azure_preview_modules
  vars:
    resource_group: "{{ resource_group_name }}"
    lab_name: myLab
    vn_name: myLabVirtualNetwork
    vm_name: myLabVm
    artifacts_name: myArtifacts
    github_token: "{{ lookup('env','GITHUB_ACCESS_TOKEN') }}"
    location: eastus
  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"

    - name: Create the lab
      azure_rm_devtestlab:
        resource_group: "{{ resource_group }}"
        name: "{{ lab_name }}"
        location: eastus
        storage_type: standard
        premium_data_disks: no
      register: output_lab

    - name: Set the lab policies
      azure_rm_devtestlabpolicy:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        policy_set_name: myDtlPolicySet
        name: myDtlPolicy
        fact_name: user_owned_lab_vm_count
        threshold: 5

    - name: Set the lab schedule
      azure_rm_devtestlabschedule:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        name: lab_vms_shutdown
        time: "1030"
        time_zone_id: "UTC+12"
      register: output

    - name: Create the lab virtual network
      azure_rm_devtestlabvirtualnetwork:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        name: "{{ vn_name }}"
        location: eastus
        description: My lab virtual network
      register: output

    - name: Define the lab artifacts source
      azure_rm_devtestlabartifactsource:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        name: "{{ artifacts_name }}"
        uri: https://github.com/Azure/azure_preview_modules.git
        source_type: github
        folder_path: /tasks
        security_token: "{{ github_token }}"

    - name:
      set_fact:
        artifact_source:
          - source_name: "{{ artifacts_name }}"
            source_path: "/Artifacts/linux-install-mongodb"
      when: "github_token | length > 0"

    - name:
      set_fact:
        artifact_source: null
      when: "github_token | length == 0"

    - name: Create a VM within the lab
      azure_rm_devtestlabvirtualmachine:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        name: "{{ vm_name }}"
        notes: Virtual machine notes, just something....
        os_type: linux
        vm_size: Standard_A2_v2
        user_name: dtladmin
        password: ZSasfovobocu$$21!
        lab_subnet:
          virtual_network_name: "{{ vn_name }}"
          name: "{{ vn_name }}Subnet"
        disallow_public_ip_address: no
        image:
          offer: UbuntuServer
          publisher: Canonical
          sku: 16.04-LTS
          os_type: Linux
          version: latest
        artifacts:
          - source_name: "{{ artifacts_name }}"
            source_path: "/Artifacts/linux-install-mongodb"
        allow_claim: no
        expiration_date: "2029-02-22T01:49:12.117974Z"

    - name: List the artifact sources
      azure_rm_devtestlabartifactsource_facts:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
      register: output
    - debug:
        var: output

    - name: List the artifact facts
      azure_rm_devtestlabartifact_facts:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        artifact_source_name: public repo
      register: output
    - debug:
        var: output

    - name: List the Azure Resource Manager template facts
      azure_rm_devtestlabarmtemplate_facts:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        artifact_source_name: "public environment repo"
      register: output
    - debug:
        var: output

    - name: Get Azure Resource Manager template facts
      azure_rm_devtestlabarmtemplate_facts:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        artifact_source_name: "public environment repo"
        name: ServiceFabric-LabCluster
      register: output
    - debug:
        var: output

    - name: Create the lab environment
      azure_rm_devtestlabenvironment:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        user_name: "@me"
        name: myEnvironment
        location: eastus
        deployment_template: "{{ output_lab.id }}/artifactSources/public environment repo/armTemplates/WebApp"

    - name: Create the lab image
      azure_rm_devtestlabcustomimage:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        name: myImage
        source_vm: "{{ vm_name }}"
        linux_os_state: non_deprovisioned

    - name: Delete the lab
      azure_rm_devtestlab:
        resource_group: "{{ resource_group }}"
        name: "{{ lab_name }}"
        state: absent
```

## <a name="run-the-playbook"></a>Uruchom element playbook

W tej sekcji Uruchamianie elementu playbook, aby przetestować różne funkcje przedstawione w tym artykule.

Przed uruchomieniem elementu playbook, należy wprowadzić następujące zmiany:
- W `vars` sekcji i Zastąp `{{ resource_group_name }}` nazwą grupy zasobów.
- Store tokenu usługi GitHub jako zmienną środowiskową o nazwie `GITHUB_ACCESS_TOKEN`.

Uruchamianie elementu playbook, przy użyciu `ansible-playbook` polecenia:

```bash
ansible-playbook devtestlab-create.yml
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie są już potrzebne, Usuń zasoby utworzone w tym artykule. 

Zapisz poniższy kod jako `cleanup.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        force_delete_nonempty: yes
        state: absent
```

Uruchamianie elementu playbook, przy użyciu `ansible-playbook` polecenia:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"] 
> [Rozwiązanie Ansible na platformie Azure](/azure/ansible/)