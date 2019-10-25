---
title: Samouczek — Tworzenie maszyny wirtualnej lub zestawu skalowania maszyn wirtualnych na podstawie galerii udostępnionych obrazów systemu Azure za pomocą rozwiązania ansible
description: Dowiedz się, jak za pomocą rozwiązania ansible utworzyć maszynę wirtualną lub zestaw skalowania maszyn wirtualnych na podstawie uogólnionego obrazu w galerii obrazów udostępnionych.
keywords: rozwiązania ansible, Azure, DevOps, bash, element PlayBook, maszyna wirtualna, zestaw skalowania maszyn wirtualnych, Galeria obrazów udostępnionych
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 10/14/2019
ms.openlocfilehash: 4b4190ddabe90af135ea64a8ba3d5905f23c457e
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72808944"
---
# <a name="tutorial-create-a-vm-or-virtual-machine-scale-set-from-the-azure-shared-image-gallery-using-ansible"></a>Samouczek: Tworzenie maszyny wirtualnej lub zestawu skalowania maszyn wirtualnych na podstawie galerii udostępnionych obrazów systemu Azure za pomocą rozwiązania ansible

[!INCLUDE [ansible-29-note.md](../../includes/ansible-29-note.md)]

[Udostępniona Galeria obrazów](/azure/virtual-machines/windows/shared-image-galleries) to usługa, która umożliwia łatwe zarządzanie, udostępnianie i organizowanie niestandardowych obrazów zarządzanych. Ta funkcja jest korzystna dla scenariuszy, w których wiele obrazów jest utrzymywanych i udostępnianych. Obrazy niestandardowe mogą być współużytkowane przez subskrypcje i między Azure Active Directory dzierżawcami. Obrazy mogą być również replikowane do wielu regionów w celu szybkiego skalowania wdrożenia.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Tworzenie uogólnionej maszyny wirtualnej i obrazu niestandardowego
> * Tworzenie galerii obrazów udostępnionych
> * Tworzenie udostępnionego obrazu i wersji obrazu
> * Tworzenie maszyny wirtualnej przy użyciu uogólnionego obrazu
> * Tworzenie zestawu skalowania maszyn wirtualnych przy użyciu uogólnionego obrazu
> * Uzyskaj informacje na temat udostępnionej galerii obrazów, obrazu i wersji.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="get-the-sample-playbooks"></a>Pobierz przykładową elementy PlayBook

Istnieją dwa sposoby uzyskania pełnego zestawu przykładowych elementy PlayBook:

- [Pobierz folder SIG](https://github.com/Azure-Samples/ansible-playbooks/tree/master/SIG_generalized_image) i Zapisz go na komputerze lokalnym.
- Utwórz nowy plik dla każdej sekcji i skopiuj do niego przykładową element PlayBook.

Plik `vars.yml` zawiera zmienne używane przez wszystkie Przykładowe elementy PlayBook dla tego samouczka. Plik można edytować w celu zapewnienia unikatowych nazw i wartości.

Pierwszy przykład element PlayBook `00-prerequisites.yml` tworzy, co jest niezbędne do ukończenia tego samouczka:
- Grupa zasobów, która jest kontenerem logicznym, w którym są wdrażane i zarządzane zasoby platformy Azure.
- Sieć wirtualna; podsieci publiczny adres IP i karta sieciowa dla maszyny wirtualnej.
- Źródłowa maszyna wirtualna, która jest używana do tworzenia uogólnionego obrazu.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
    - name: Create resource group if doesn't exist
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"
    
    - name: Create virtual network
      azure_rm_virtualnetwork:
        resource_group: "{{ resource_group }}"
        name: "{{ virtual_network_name }}"
        address_prefixes: "10.0.0.0/16"

    - name: Add subnet
      azure_rm_subnet:
        resource_group: "{{ resource_group }}"
        name: "{{ subnet_name }}"
        address_prefix: "10.0.1.0/24"
        virtual_network: "{{ virtual_network_name }}"

    - name: Create public IP address
      azure_rm_publicipaddress:
        resource_group: "{{ resource_group }}"
        allocation_method: Static
        name: "{{ ip_name }}"

    - name: Create virtual network inteface cards for VM A and B
      azure_rm_networkinterface:
        resource_group: "{{ resource_group }}"
        name: "{{ network_interface_name }}"
        virtual_network: "{{ virtual_network_name }}"
        subnet: "{{ subnet_name }}"

    - name: Create VM
      azure_rm_virtualmachine:
        resource_group: "{{ resource_group }}"
        name: "{{ source_vm_name }}"
        admin_username: testuser
        admin_password: "Password1234!"
        vm_size: Standard_B1ms
        network_interfaces: "{{ network_interface_name }}"
        image:
          offer: UbuntuServer
          publisher: Canonical
          sku: 16.04-LTS
          version: latest
```

Uruchom element PlayBook za pomocą polecenia `ansible-playbook`:

```bash
ansible-playbook 00-prerequisites.yml
```

W [Azure Portal](https://portal.azure.com)Sprawdź grupę zasobów określoną w `vars.yml`, aby wyświetlić nową maszynę wirtualną i różne utworzone zasoby.

## <a name="generalize-the-vm-and-create-a-custom-image"></a>Uogólnianie maszyny wirtualnej i tworzenie obrazu niestandardowego

Następny element PlayBook, `01a-create-generalized-image.yml`, uogólniuje źródłową maszynę wirtualną utworzoną w poprzednim kroku, a następnie tworzy na podstawie tego obrazu niestandardowego.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
    - name: Generalize VM
      azure_rm_virtualmachine:
        resource_group: "{{ resource_group }}"
        name: "{{ source_vm_name }}"
        generalized: yes

    - name: Create custom image
      azure_rm_image:
        resource_group: "{{ resource_group }}"
        name: "{{ image_name }}"
        source: "{{ source_vm_name }}"
```

Uruchom element PlayBook za pomocą polecenia `ansible-playbook`:

```bash
ansible-playbook 01a-create-generalized-image.yml
```

Sprawdź grupę zasobów i upewnij się, że `testimagea` pojawia się.

## <a name="create-the-shared-image-gallery"></a>Tworzenie galerii obrazów udostępnionych

Galeria obrazów jest repozytorium do udostępniania obrazów i zarządzania nimi. Przykładowy kod element PlayBook w `02-create-shared-image-gallery.yml` tworzy galerię obrazów udostępnionych w grupie zasobów.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
    - name: Create shared image gallery
      azure_rm_gallery:
        resource_group: "{{ resource_group }}"
        name: "{{ shared_gallery_name }}"
        location: "{{ location }}"
        description: This is the gallery description.
```

Uruchom element PlayBook za pomocą polecenia `ansible-playbook`:

```bash
ansible-playbook 02-create-shared-image-gallery.yml
```

Zobaczysz teraz nową galerię, `myGallery` w grupie zasobów.

## <a name="create-a-shared-image-and-image-version"></a>Tworzenie udostępnionego obrazu i wersji obrazu

Następna element PlayBook, `03a-create-shared-image-generalized.yml` tworzy definicję obrazu i wersję obrazu.

Definicje obrazu obejmują typ obrazu (system Windows lub Linux), informacje o wersji oraz minimalne i maksymalne wymagania dotyczące pamięci. Wersja obrazu jest wersją obrazu. Galeria, definicja obrazu i wersja obrazu ułatwiają organizowanie obrazów w grupach logicznych. 

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
    - name: Create shared image
      azure_rm_galleryimage:
        resource_group: "{{ resource_group }}"
        gallery_name: "{{ shared_gallery_name }}"
        name: "{{ shared_image_name }}"
        location: "{{ location }}"
        os_type: linux
        os_state: generalized
        identifier:
          publisher: myPublisherName
          offer: myOfferName
          sku: mySkuName
        description: Image description
    
    - name: Create or update a simple gallery image version.
      azure_rm_galleryimageversion:
        resource_group: "{{ resource_group }}"
        gallery_name: "{{ shared_gallery_name }}"
        gallery_image_name: "{{ shared_image_name }}"
        name: "{{ shared_image_version }}"
        location: "{{ location }}"
        publishing_profile:
          end_of_life_date: "2020-10-01t00:00:00+00:00"
          exclude_from_latest: yes
          replica_count: 3
          storage_account_type: Standard_LRS
          target_regions:
            - name: West US
              regional_replica_count: 1
            - name: East US
              regional_replica_count: 2
              storage_account_type: Standard_ZRS
          managed_image:
            name: "{{ image_name }}"
            resource_group: "{{ resource_group }}"
      register: output

    - debug:
        var: output
```

Uruchom element PlayBook za pomocą polecenia `ansible-playbook`:

```bash
ansible-playbook 03a-create-shared-image-generalized.yml
```

Twoja grupa zasobów ma teraz definicję obrazu i wersję obrazu dla galerii.

## <a name="create-a-vm-based-on-the-generalized-image"></a>Tworzenie maszyny wirtualnej na podstawie uogólnionego obrazu

Na koniec Uruchom `04a-create-vm-using-generalized-image.yml`, aby utworzyć maszynę wirtualną na podstawie uogólnionego obrazu utworzonego w poprzednim kroku.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
  - name: Create VM using shared image
    azure_rm_virtualmachine:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}"
      vm_size: Standard_DS1_v2
      admin_username: adminUser
      admin_password: PassWord01
      managed_disk_type: Standard_LRS
      image:
        id: "/subscriptions/{{ lookup('env', 'AZURE_SUBSCRIPTION_ID') }}/resourceGroups/{{ resource_group }}/providers/Microsoft.Compute/galleries/{{ shared_gallery_name }}/images/{{ shared_image_name }}/versions/{{ shared_image_version }}"
```

Uruchom element PlayBook za pomocą polecenia `ansible-playbook`:

```bash
ansible-playbook 04a-create-vm-using-generalized-image.yml
```

## <a name="create-a-virtual-machine-scale-sets-based-on-the-generalized-image"></a>Tworzenie zestawów skalowania maszyn wirtualnych na podstawie uogólnionego obrazu

Możesz również utworzyć zestaw skalowania maszyn wirtualnych na podstawie uogólnionego obrazu. Uruchom `05a-create-vmss-using-generalized-image.yml`, aby to zrobić.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
  - name: Create a virtual machine scale set using a shared image
    azure_rm_virtualmachinescaleset:
      resource_group: "{{ resource_group }}"
      name: "{{ vmss_name }}"
      vm_size: Standard_DS1_v2
      admin_username: adminUser
      admin_password: PassWord01
      capacity: 2
      virtual_network_name: "{{ virtual_network_name }}"
      upgrade_policy: Manual
      subnet_name: "{{ subnet_name }}"
      managed_disk_type: Standard_LRS
      image:
        id: "/subscriptions/{{ lookup('env', 'AZURE_SUBSCRIPTION_ID') }}/resourceGroups/{{ resource_group }}/providers/Microsoft.Compute/galleries/{{ shared_gallery_name }}/images/{{ shared_image_name }}/versions/{{ shared_image_version }}"
```

Uruchom element PlayBook za pomocą polecenia `ansible-playbook`:

```bash
ansible-playbook 05a-create-vmss-using-generalized-image.yml
```

## <a name="get-information-about-the-gallery"></a>Uzyskaj informacje o galerii

Możesz uzyskać informacje na temat galerii, definicji obrazu i wersji, uruchamiając `06-get-info.yml`.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
  - name: Get Shared Image Gallery information
    azure_rm_gallery_info:
      resource_group: "{{ resource_group }}"
      name: "{{ shared_gallery_name }}"
  - name: Get shared image information
    azure_rm_galleryimage_info:
      resource_group: "{{ resource_group }}"
      gallery_name: "{{ shared_gallery_name }}"
      name: "{{ shared_image_name }}"
  - name: Get Shared Image Gallery image version information
    azure_rm_galleryimageversion_info:
      resource_group: "{{ resource_group }}"
      gallery_name: "{{ shared_gallery_name }}"
      gallery_image_name: "{{ shared_image_name }}"
      name: "{{ shared_image_version }}"
```

Uruchom element PlayBook za pomocą polecenia `ansible-playbook`:

```bash
ansible-playbook 06-get-info.yml
```

## <a name="delete-the-shared-image"></a>Usuwanie obrazu udostępnionego

Aby usunąć zasoby galerii, zapoznaj się z przykładem element PlayBook `07-delete-gallery.yml`. Usuwanie zasobów w odwrotnej kolejności. Zacznij od usunięcia wersji obrazu. Po usunięciu wszystkich wersji obrazu można usunąć definicję obrazu. Po usunięciu wszystkich definicji obrazu można usunąć galerię.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
  - name: Delete gallery image version.
    azure_rm_galleryimageversion:
      resource_group: "{{ resource_group }}"
      gallery_name: "{{ shared_gallery_name }}"
      gallery_image_name: "{{ shared_image_name }}"
      name: "{{ shared_image_version }}"
      state: absent

  - name: Delete gallery image
    azure_rm_galleryimage:
      resource_group: "{{ resource_group }}"
      gallery_name: "{{ shared_gallery_name }}"
      name: "{{ shared_image_name }}"
      state: absent

  - name: Delete a simple gallery.
    azure_rm_gallery:
      resource_group: "{{ resource_group }}"
      name: "{{ shared_gallery_name }}"
      state: absent
```

Uruchom element PlayBook za pomocą polecenia `ansible-playbook`:

```bash
ansible-playbook 07-delete-gallery.yml
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie jest już potrzebne, Usuń zasoby utworzone w tym artykule. 

Przykładowy kod element PlayBook w tej sekcji jest używany do:

- Usuń dwie utworzone wcześniej grupy zasobów

Zapisz następujący podręcznik jako `cleanup.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: "{{ resource_group_name }}"
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        force_delete_nonempty: yes
        state: absent
```

Poniżej przedstawiono niektóre kluczowe uwagi, które należy wziąć pod uwagę podczas pracy z przykładową element PlayBook:

- Zastąp symbol zastępczy `{{ resource_group_name }}` nazwą grupy zasobów.
- Wszystkie zasoby w ramach dwóch określonych grup zasobów zostaną usunięte.

Uruchom element PlayBook za pomocą polecenia `ansible-playbook`:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"] 
> [Rozwiązanie Ansible na platformie Azure](/azure/ansible/)