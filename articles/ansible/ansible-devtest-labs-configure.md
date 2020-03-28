---
title: Samouczek — konfigurowanie laboratoriów w laboratoriach devtest platformy Azure przy użyciu programu Ansible
description: Dowiedz się, jak skonfigurować laboratorium w laboratorium usługi Azure DevTest Labs przy użyciu programu Ansible
keywords: ansible, azure, devops, bash, playbook, devtest labs
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: b6981ee94c4d82997c574db037befb9782465d08
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74156281"
---
# <a name="tutorial-configure-labs-in-azure-devtest-labs-using-ansible"></a>Samouczek: Konfigurowanie laboratoriów w laboratoriach devtest azure przy użyciu ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[Usługa Azure DevTest Labs](/azure/lab-services/devtest-lab-overview) umożliwia deweloperom automatyzację tworzenia środowisk maszyn wirtualnych dla swoich aplikacji. Te środowiska można skonfigurować do tworzenia aplikacji, testowania i szkolenia. 

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Tworzenie laboratorium
> * Ustawianie zasad laboratoryjnych
> * Ustawianie harmonogramów laboratoryjnych
> * Tworzenie sieci wirtualnej laboratorium
> * Definiowanie źródła artefaktów dla laboratorium
> * Tworzenie maszyny wirtualnej w laboratorium
> * Lista źródeł artefaktów i artefaktów laboratorium
> * Pobierz informacje usługi Azure Resource Manager dla źródeł artefaktów
> * Tworzenie środowiska laboratoryjne
> * Tworzenie obrazu laboratorium
> * Usuwanie laboratorium

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-resource-group"></a>Tworzenie grupy zasobów

Przykładowy fragment kodu podręcznika tworzy grupę zasobów platformy Azure. Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

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

## <a name="set-the-lab-policies"></a>Ustawianie zasad laboratoryjnych

Można skonfigurować ustawienia zasad laboratoryjnych. Można ustawić następujące wartości:

- `user_owned_lab_vm_count`to liczba maszyn wirtualnych, które użytkownik może posiadać
- `user_owned_lab_premium_vm_count`to liczba maszyn wirtualnych premium, które użytkownik może posiadać
- `lab_vm_count`to maksymalna liczba maszyn wirtualnych w laboratorium
- `lab_premium_vm_count`to maksymalna liczba maszyn wirtualnych klasy premium w laboratorium
- `lab_vm_size`jest dozwolonym rozmiarem maszyn wirtualnych w laboratorium
- `gallery_image`to dozwolone zdjęcia z galerii
- `user_owned_lab_vm_count_in_subnet`to maksymalna liczba maszyn wirtualnych użytkownika w podsieci
- `lab_target_cost`jest kosztem docelowym laboratorium

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

## <a name="set-the-lab-schedules"></a>Ustawianie harmonogramów laboratoryjnych

Przykładowe zadanie w tej sekcji konfiguruje harmonogram laboratorium. 

We wyszczep kodu poniżej `lab_vms_startup` wartość służy do określania czasu uruchamiania maszyny Wirtualnej. Podobnie ustawienie `lab_vms_shutdown` wartości ustanawia czas zamykania maszyny Wirtualnej laboratorium.

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

## <a name="create-the-lab-virtual-network"></a>Tworzenie sieci wirtualnej laboratorium

To następujące zadanie tworzy domyślną sieć wirtualną laboratorium.

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

## <a name="define-an-artifact-source-for-the-lab"></a>Definiowanie źródła artefaktów dla laboratorium

Źródło artefaktów to prawidłowo ustrukturyzowane repozytorium GitHub, które zawiera definicję artefaktów i szablony usługi Azure Resource Manager. Każde laboratorium zawiera wstępnie zdefiniowane artefakty publiczne. Następujące zadania pokazują, jak utworzyć źródło artefaktu dla laboratorium.

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

## <a name="create-a-vm-within-the-lab"></a>Tworzenie maszyny wirtualnej w laboratorium

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

## <a name="list-the-labs-artifact-sources-and-artifacts"></a>Lista źródeł artefaktów i artefaktów laboratorium

Aby wyświetlić listę wszystkich domyślnych i niestandardowych źródeł artefaktów w laboratorium, należy użyć następującego zadania:

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

## <a name="get-azure-resource-manager-information-for-the-artifact-sources"></a>Pobierz informacje usługi Azure Resource Manager dla źródeł artefaktów

Aby wyświetlić listę wszystkich szablonów usługi Azure Resource Manager w `public environment repository`, wstępnie zdefiniowanym repozytorium z szablonami:

```yml
- name: List the Azure Resource Manager template facts
  azure_rm_devtestlabartifactsource_facts:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
  register: output
- debug:
    var: output
```

Następujące zadanie pobiera szczegóły określonego szablonu usługi Azure Resource Manager z repozytorium:

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

## <a name="create-the-lab-environment"></a>Tworzenie środowiska laboratoryjne

Następujące zadanie tworzy środowisko laboratoryjne na podstawie jednego z szablonów z repozytorium środowiska publicznego.

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

Następujące zadanie tworzy obraz z maszyny Wirtualnej. Obraz umożliwia tworzenie identycznych maszyn wirtualnych.

```yml
- name: Create the lab image
  azure_rm_devtestlabcustomimage:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
    name: myImage
    source_vm: "{{ output_vm.virtualmachines[0]['name'] }}"
    linux_os_state: non_deprovisioned
```

## <a name="delete-the-lab"></a>Usuwanie laboratorium

Aby usunąć laboratorium, należy użyć następującego zadania:

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

## <a name="get-the-sample-playbook"></a>Pobierz przykładowy podręcznik

Istnieją dwa sposoby uzyskania pełnego przykładowego podręcznika:
- [Pobierz podręcznik](https://github.com/Azure-Samples/ansible-playbooks/blob/master/devtestlab-create.yml) i zapisz `devtestlab-create.yml`go w pliku .
- Utwórz nowy `devtestlab-create.yml` plik o nazwie i skopiuj do niego następującą zawartość:

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

## <a name="run-the-playbook"></a>Uruchamianie podręcznika

W tej sekcji uruchom podręcznik, aby przetestować różne funkcje pokazane w tym artykule.

Przed uruchomieniem podręcznika należy wprowadzić następujące zmiany:
- W `vars` sekcji zastąp `{{ resource_group_name }}` symbol zastępczy nazwą grupy zasobów.
- Token GitHub jest przechowywany `GITHUB_ACCESS_TOKEN`jako zmienna środowiskowa o nazwie .

Uruchom podręcznik za `ansible-playbook` pomocą polecenia:

```bash
ansible-playbook devtestlab-create.yml
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie są już potrzebne, usuń zasoby utworzone w tym artykule. 

Zapisz następujący kod `cleanup.yml`jako:

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

Uruchom podręcznik za `ansible-playbook` pomocą polecenia:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"] 
> [Rozwiązanie Ansible na platformie Azure](/azure/ansible/)