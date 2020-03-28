---
title: Samouczek — tworzenie zestawu skalowania maszyny wirtualnej lub maszyny wirtualnej z galerii obrazów udostępnionych platformy Azure przy użyciu aplikacji Ansible
description: Dowiedz się, jak używać ansible do tworzenia zestawu skalowania maszyny wirtualnej lub maszyny wirtualnej na podstawie uogólnionego obrazu w Galerii obrazów udostępnionych.
keywords: ansible, azure, devops, bash, playbook, maszyna wirtualna, zestaw skalowania maszyny wirtualnej, galeria obrazów udostępnionych
ms.topic: tutorial
ms.date: 10/14/2019
ms.openlocfilehash: f784419736854095cc1bc5da14f3867ac3f7eb12
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74155828"
---
# <a name="tutorial-create-a-vm-or-virtual-machine-scale-set-from-the-azure-shared-image-gallery-using-ansible"></a>Samouczek: Tworzenie zestawu skalowania maszyny wirtualnej lub maszyny wirtualnej z galerii obrazów udostępnionych platformy Azure przy użyciu metody ansible

[!INCLUDE [ansible-29-note.md](../../includes/ansible-29-note.md)]

[Shared Image Gallery](/azure/virtual-machines/windows/shared-image-galleries) to usługa, która umożliwia łatwe zarządzanie, udostępnianie i organizowanie obrazów zarządzanych na zamówienie. Ta funkcja jest korzystna dla scenariuszy, w których wiele obrazów są przechowywane i udostępniane. Obrazy niestandardowe mogą być udostępniane w ramach subskrypcji i między dzierżawami usługi Azure Active Directory. Obrazy mogą być również replikowane do wielu regionów w celu szybszego skalowania wdrażania.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Tworzenie uogólnionej maszyny Wirtualnej i obrazu niestandardowego
> * Tworzenie galerii obrazów udostępnionych
> * Tworzenie udostępnionej wersji obrazu i obrazu
> * Tworzenie maszyny wirtualnej przy użyciu obrazu uogólnionego
> * Tworzenie zestawu skalowania maszyny wirtualnej przy użyciu obrazu uogólnionego
> * Uzyskaj informacje o udostępnionej galerii obrazów, obrazie i wersji.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="get-the-sample-playbooks"></a>Pobierz przykładowe podręczniki

Istnieją dwa sposoby uzyskania pełnego zestawu przykładowych podręczników:

- [Pobierz folder SIG](https://github.com/Azure-Samples/ansible-playbooks/tree/master/SIG_generalized_image) i zapisz go na swoim lokalnym komputerze.
- Utwórz nowy plik dla każdej sekcji i skopiuj w niej przykładowy podręcznik.

Plik `vars.yml` zawiera zmienne używane przez wszystkie przykładowe podręczniki dla tego samouczka. Można edytować plik, aby podać unikatowe nazwy i wartości.

Pierwszy przykładowy `00-prerequisites.yml` podręcznik tworzy to, co jest niezbędne do ukończenia tego samouczka:
- Grupa zasobów, która jest kontenerem logicznym, w którym zasoby platformy Azure są wdrażane i zarządzane.
- Sieć wirtualna; podsieci; publicznego adresu IP i karty interfejsu sieciowego maszyny Wirtualnej.
- Źródło maszyny wirtualnej, który jest używany do tworzenia obrazu uogólnionego.

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

Uruchom podręcznik za `ansible-playbook` pomocą polecenia:

```bash
ansible-playbook 00-prerequisites.yml
```

W [witrynie Azure portal](https://portal.azure.com)sprawdź grupę zasobów określoną w `vars.yml` celu wyświetlenia nowej maszyny wirtualnej i różnych utworzonych zasobów.

## <a name="generalize-the-vm-and-create-a-custom-image"></a>Uogólnianie maszyny Wirtualnej i tworzenie obrazu niestandardowego

Następny podręcznik , `01a-create-generalized-image.yml`uogólnia źródłową maszynę wirtualną utworzoną w poprzednim kroku, a następnie utwórz obraz niestandardowy na jej podstawie.

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

Uruchom podręcznik za `ansible-playbook` pomocą polecenia:

```bash
ansible-playbook 01a-create-generalized-image.yml
```

Sprawdź grupę zasobów `testimagea` i upewnij się, że jest wyświetlany.

## <a name="create-the-shared-image-gallery"></a>Tworzenie galerii obrazów udostępnionych

Galeria zdjęć jest repozytorium do udostępniania i zarządzania obrazami. Przykładowy kod podręcznika w `02-create-shared-image-gallery.yml` tworzy Galerię obrazów udostępnionych w grupie zasobów.

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

Uruchom podręcznik za `ansible-playbook` pomocą polecenia:

```bash
ansible-playbook 02-create-shared-image-gallery.yml
```

Teraz zobaczysz nową galerię w `myGallery`grupie zasobów.

## <a name="create-a-shared-image-and-image-version"></a>Tworzenie udostępnionej wersji obrazu i obrazu

Następny podręcznik, `03a-create-shared-image-generalized.yml` tworzy definicję obrazu i wersję obrazu.

Definicje obrazów obejmują typ obrazu (Windows lub Linux), informacje o wersji oraz minimalne i maksymalne wymagania dotyczące pamięci. Wersja obrazu jest wersją obrazu. Galeria, definicja obrazu i wersja obrazu ułatwiają organizowanie obrazów w grupach logicznych. 

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

Uruchom podręcznik za `ansible-playbook` pomocą polecenia:

```bash
ansible-playbook 03a-create-shared-image-generalized.yml
```

Grupa zasobów ma teraz definicję obrazu i wersję obrazu dla galerii.

## <a name="create-a-vm-based-on-the-generalized-image"></a>Tworzenie maszyny wirtualnej na podstawie uogólnionego obrazu

Na koniec `04a-create-vm-using-generalized-image.yml` uruchom, aby utworzyć maszynę wirtualną na podstawie uogólnionego obrazu utworzonego w poprzednim kroku.

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

Uruchom podręcznik za `ansible-playbook` pomocą polecenia:

```bash
ansible-playbook 04a-create-vm-using-generalized-image.yml
```

## <a name="create-a-virtual-machine-scale-sets-based-on-the-generalized-image"></a>Tworzenie zestawów skalowania maszyny wirtualnej na podstawie obrazu uogólnionego

Można również utworzyć zestaw skalowania maszyny wirtualnej na podstawie obrazu uogólnionego. Uruchom, `05a-create-vmss-using-generalized-image.yml` aby to zrobić.

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

Uruchom podręcznik za `ansible-playbook` pomocą polecenia:

```bash
ansible-playbook 05a-create-vmss-using-generalized-image.yml
```

## <a name="get-information-about-the-gallery"></a>Uzyskaj informacje o galerii

Informacje o galerii, definicji obrazu i `06-get-info.yml`wersji można uzyskać, uruchamiając program .

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

Uruchom podręcznik za `ansible-playbook` pomocą polecenia:

```bash
ansible-playbook 06-get-info.yml
```

## <a name="delete-the-shared-image"></a>Usuwanie obrazu udostępnionego

Aby usunąć zasoby galerii, zapoznaj `07-delete-gallery.yml`się z przykładowym podręcznikiem . Usuwanie zasobów w odwrotnej kolejności. Zacznij od usunięcia wersji obrazu. Po usunięciu wszystkich wersji obrazu można usunąć definicję obrazu. Po usunięciu wszystkich definicji obrazów można usunąć galerię.

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

Uruchom podręcznik za `ansible-playbook` pomocą polecenia:

```bash
ansible-playbook 07-delete-gallery.yml
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie są już potrzebne, usuń zasoby utworzone w tym artykule. 

Przykładowy kod podręcznika w tej sekcji jest używany do:

- Usuwanie dwóch grup zasobów utworzonych wcześniej

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

Oto kilka kluczowych uwag, które należy wziąć pod uwagę podczas pracy z przykładowym podręcznikiem:

- Zastąp `{{ resource_group_name }}` symbol zastępczy nazwą grupy zasobów.
- Wszystkie zasoby w ramach dwóch określonych grup zasobów zostaną usunięte.

Uruchom podręcznik za `ansible-playbook` pomocą polecenia:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"] 
> [Rozwiązanie Ansible na platformie Azure](/azure/ansible/)