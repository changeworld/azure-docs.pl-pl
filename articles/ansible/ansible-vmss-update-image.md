---
title: Samouczek — aktualizowanie niestandardowego obrazu zestawów skalowania maszyn wirtualnych platformy Azure przy użyciu rozwiązania ansible
description: Dowiedz się, jak używać rozwiązania ansible do aktualizowania zestawów skalowania maszyn wirtualnych na platformie Azure przy użyciu obrazu niestandardowego
keywords: ansible, azure, devops, bash, playbook, virtual machine, virtual machine scale set, vmss
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: b7d3053c09d2dcb667a4fc407035f4814f786932
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/18/2019
ms.locfileid: "74155853"
---
# <a name="tutorial-update-the-custom-image-of-azure-virtual-machine-scale-sets-using-ansible"></a>Samouczek: aktualizowanie niestandardowego obrazu zestawów skalowania maszyn wirtualnych platformy Azure za pomocą rozwiązania ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-vmss.md](../../includes/open-source-devops-intro-vmss.md)]

Po wdrożeniu maszyny wirtualnej należy ją skonfigurować przy użyciu oprogramowania wymaganego przez aplikację. Zamiast tego zadania konfiguracji dla każdej maszyny wirtualnej można utworzyć niestandardowy obraz. Obraz niestandardowy to migawka istniejącej maszyny wirtualnej, która zawiera dowolne zainstalowane oprogramowanie. Podczas [konfigurowania zestawu skalowania](./ansible-create-configure-vmss.md)należy określić obraz do użycia dla maszyn wirtualnych zestawu skalowania. Przy użyciu obrazu niestandardowego każde wystąpienie maszyny wirtualnej jest identycznie skonfigurowane dla Twojej aplikacji. Czasami może być konieczne zaktualizowanie niestandardowego obrazu zestawu skalowania. To zadanie jest skoncentrowane na tym samouczku.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Skonfiguruj dwie maszyny wirtualne przy użyciu protokołu HTTP
> * Tworzenie obrazu niestandardowego na podstawie istniejącej maszyny wirtualnej
> * Tworzenie zestawu skalowania na podstawie obrazu
> * Aktualizowanie obrazu niestandardowego

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="configure-two-vms"></a>Konfigurowanie dwóch maszyn wirtualnych

Kod element PlayBook w tej sekcji tworzy dwie maszyny wirtualne z zainstalowanym protokołem HTTP. 

Na stronie `index.html` dla każdej maszyny wirtualnej jest wyświetlany ciąg testowy:

* Pierwsza maszyna wirtualna wyświetla wartość `Image A`
* Druga maszyna wirtualna wyświetla wartość `Image B`

Ten ciąg jest przeznaczony do naśladowania konfiguracji każdej maszyny wirtualnej przy użyciu innego oprogramowania.

Istnieją dwa sposoby uzyskania element PlayBook przykładu:

* [Pobierz element PlayBook](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss_images/01-create-vms.yml) i Zapisz go w `create_vms.yml`.
* Utwórz nowy plik o nazwie `create_vms.yml` i skopiuj do niego następującą zawartość:

```yml
- name: Create two VMs (A and B) with HTTPS
  hosts: localhost
  connection: local
  vars:
    vm_name: vmforimage
    admin_username: testuser
    admin_password: Pass123$$$abx!
    location: eastus
  tasks:
  - name: Create a resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"

  - name: Create virtual network
    azure_rm_virtualnetwork:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}"
      address_prefixes: "10.0.0.0/16"

  - name: Create subnets for VM A and B
    azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      virtual_network: "{{ vm_name }}"
      name: "{{ vm_name }}"
      address_prefix: "10.0.1.0/24"

  - name: Create Network Security Group that allows HTTP
    azure_rm_securitygroup:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}"
      rules:
        - name: HTTP
          protocol: Tcp
          destination_port_range: 80
          access: Allow
          priority: 1002
          direction: Inbound

  - name: Create public IP addresses for VM A and B
    azure_rm_publicipaddress:
      resource_group: "{{ resource_group }}"
      allocation_method: Static
      name: "{{ vm_name }}_{{ item }}"
    loop:
      - A
      - B
    register: pip_output

  - name: Create virtual network inteface cards for VM A and B
    azure_rm_networkinterface:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}_{{ item }}"
      virtual_network: "{{ vm_name }}"
      subnet: "{{ vm_name }}"
      public_ip_name: "{{ vm_name }}_{{ item }}"
      security_group: "{{ vm_name }}"
    loop:
      - A
      - B

  - name: Create VM A and B
    azure_rm_virtualmachine:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}{{ item }}"
      admin_username: "{{ admin_username }}"
      admin_password: "{{ admin_password }}"
      vm_size: Standard_B1ms
      network_interfaces: "{{ vm_name }}_{{ item }}"
      image:
        offer: UbuntuServer
        publisher: Canonical
        sku: 16.04-LTS
        version: latest
    loop:
      - A
      - B

  - name: Create VM Extension
    azure_rm_virtualmachineextension:
      resource_group: "{{ resource_group }}"
      name: testVMExtension
      virtual_machine_name: "{{ vm_name }}{{ item }}"
      publisher: Microsoft.Azure.Extensions
      virtual_machine_extension_type: CustomScript
      type_handler_version: 2.0
      auto_upgrade_minor_version: true
      settings: {"commandToExecute": "sudo apt-get -y install apache2"}
    loop:
      - A
      - B

  - name: Create VM Extension
    azure_rm_virtualmachineextension:
      resource_group: "{{ resource_group }}"
      name: testVMExtension
      virtual_machine_name: "{{ vm_name }}{{ item }}"
      publisher: Microsoft.Azure.Extensions
      virtual_machine_extension_type: CustomScript
      type_handler_version: 2.0
      auto_upgrade_minor_version: true
      settings: {"commandToExecute": "printf '<html><body><h1>Image {{ item }}</h1></body></html>' >> index.html; sudo cp index.html /var/www/html/"}
    loop:
      - A
      - B

  - debug:
      msg: "Public IP Address A: {{ pip_output.results[0].state.ip_address }}"

  - debug:
      msg: "Public IP Address B: {{ pip_output.results[1].state.ip_address }}"
```

Uruchom element PlayBook przy użyciu polecenia `ansible-playbook`, zastępując `myrg` nazwą grupy zasobów:

```bash
ansible-playbook create-vms.yml --extra-vars "resource_group=myrg"
```

Ze względu na `debug` sekcje element PlayBook polecenie `ansible-playbook` drukuje adres IP każdej maszyny wirtualnej. Skopiuj te adresy IP do późniejszego użycia.

![Adresy IP maszyn wirtualnych](media/ansible-vmss-update-image/vmss-update-vms-ip-addresses.png)

## <a name="connect-to-the-two-vms"></a>Nawiązywanie połączenia z dwiema maszynami wirtualnymi

W tej sekcji połączysz się z każdą maszyną wirtualną. Jak wspomniano w poprzedniej sekcji, ciągi `Image A` i `Image B` naśladowania mają dwie odrębne maszyny wirtualne z różnymi konfiguracjami.

Korzystając z adresów IP z poprzedniej sekcji, Połącz się z obiema maszynami wirtualnymi:

![Zrzut ekranu z maszyny wirtualnej A](media/ansible-vmss-update-image/vmss-update-browser-vma.png)

![Zrzut ekranu z maszyny wirtualnej B](media/ansible-vmss-update-image/vmss-update-browser-vmb.png)

## <a name="create-images-from-each-vm"></a>Tworzenie obrazów z każdej maszyny wirtualnej

W tym momencie masz dwie maszyny wirtualne z nieco różnymi konfiguracjami (ich `index.html` pliki).

Kod element PlayBook w tej sekcji tworzy niestandardowy obraz dla każdej maszyny wirtualnej:

* `image_vmforimageA` — obraz niestandardowy utworzony dla maszyny wirtualnej, która wyświetla `Image A` na stronie głównej.
* `image_vmforimageB` — obraz niestandardowy utworzony dla maszyny wirtualnej, która wyświetla `Image B` na stronie głównej.

Istnieją dwa sposoby uzyskania element PlayBook przykładu:

* [Pobierz element PlayBook](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss_images/02-capture-images.yml) i Zapisz go w `capture-images.yml`.
* Utwórz nowy plik o nazwie `capture-images.yml` i skopiuj do niego następującą zawartość:

```yml
- name: Capture VM Images
  hosts: localhost
  connection: local
  vars:
    vm_name: vmforimage
  tasks:

  - name: Stop and generalize VMs
    azure_rm_virtualmachine:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}{{ item }}"
      generalized: yes
    loop:
      - A
      - B

  - name: Create an images from a VMs
    azure_rm_image:
      resource_group: "{{ resource_group }}"
      name: "image_{{ vm_name }}{{ item }}"
      source: "{{ vm_name }}{{ item }}"
    loop:
      - A
      - B
```

Uruchom element PlayBook przy użyciu polecenia `ansible-playbook`, zastępując `myrg` nazwą grupy zasobów:

```bash
ansible-playbook capture-images.yml --extra-vars "resource_group=myrg"
```

## <a name="create-scale-set-using-image-a"></a>Tworzenie zestawu skalowania przy użyciu obrazu A

W tej sekcji element PlayBook służy do konfigurowania następujących zasobów platformy Azure:

* Publiczny adres IP
* Moduł równoważenia obciążenia
* Zestaw skalowania, który odwołuje się `image_vmforimageA`

Istnieją dwa sposoby uzyskania element PlayBook przykładu:

* [Pobierz element PlayBook](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss_images/03-create-vmss.yml) i Zapisz go w `create-vmss.yml`.
* Utwórz nowy plik o nazwie `create-vmss.yml` i skopiuj do niego następującą zawartość: "

```yml
---
- hosts: localhost
  vars:
    vmss_name: vmsstest
    location: eastus
    admin_username: vmssadmin
    admin_password: User123!!!abc
    vm_name: vmforimage
    image_name: "image_vmforimageA"

  tasks:

    - name: Create public IP address
      azure_rm_publicipaddress:
        resource_group: "{{ resource_group }}"
        allocation_method: Static
        name: "{{ vmss_name }}"
      register: pip_output

    - name: Create a load balancer
      azure_rm_loadbalancer:
        name: "{{ vmss_name }}lb"
        location: "{{ location }}"
        resource_group: "{{ resource_group }}"
        public_ip: "{{ vmss_name }}"
        probe_protocol: Tcp
        probe_port: 80
        probe_interval: 10
        probe_fail_count: 3
        protocol: Tcp
        load_distribution: Default
        frontend_port: 80
        backend_port: 80
        idle_timeout: 4
        natpool_frontend_port_start: 50000
        natpool_frontend_port_end: 50040
        natpool_backend_port: 22
        natpool_protocol: Tcp

    - name: Create a scale set
      azure_rm_virtualmachinescaleset:
        resource_group: "{{ resource_group }}"
        name: "{{ vmss_name }}"
        vm_size: Standard_DS1_v2
        admin_username: "{{ admin_username }}"
        admin_password: "{{ admin_password }}"
        ssh_password_enabled: true
        capacity: 2
        virtual_network_name: "{{ vm_name }}"
        subnet_name: "{{ vm_name }}"
        upgrade_policy: Manual
        tier: Standard
        managed_disk_type: Standard_LRS
        os_disk_caching: ReadWrite
        image:
          name: "{{ image_name }}"
          resource_group: "{{ resource_group }}"
        load_balancer: "{{ vmss_name }}lb"

    - debug:
        msg: "Scale set public IP address: {{ pip_output.state.ip_address }}"
```

Uruchom element PlayBook przy użyciu polecenia `ansible-playbook`, zastępując `myrg` nazwą grupy zasobów:

```bash
ansible-playbook create-vmss.yml --extra-vars "resource_group=myrg"
```

Ze względu na `debug` sekcji element PlayBook polecenie `ansible-playbook` drukuje adres IP zestawu skalowania. Skopiuj ten adres IP do późniejszego użycia.

![Publiczny adres IP](media/ansible-vmss-update-image/vmss-update-vmss-public-ip.png)

## <a name="connect-to-the-scale-set"></a>Połącz z zestawem skalowania

W tej sekcji połączysz się z zestawem skalowania. 

Korzystając z adresu IP z poprzedniej sekcji, Połącz się z zestawem skalowania.

Jak wspomniano w poprzedniej sekcji, ciągi `Image A` i `Image B` naśladowania mają dwie odrębne maszyny wirtualne z różnymi konfiguracjami.

Zestaw skalowania odwołuje się do obrazu niestandardowego o nazwie `image_vmforimageA`. `image_vmforimageA` obrazu niestandardowego został utworzony na podstawie maszyny wirtualnej, której Strona główna wyświetla `Image A`.

W efekcie zostanie wyświetlona strona główna wyświetlająca `Image A`:

![Zestaw skalowania jest skojarzony z pierwszą maszyną wirtualną.](media/ansible-vmss-update-image/vmss-update-browser-initial-vmss.png)

Pozostaw otwarte okno przeglądarki, aby przejść do następnej sekcji.

## <a name="change-custom-image-in-scale-set-and-upgrade-instances"></a>Zmienianie obrazu niestandardowego w zestawie skalowania i wystąpieniach uaktualnienia

Kod element PlayBook w tej sekcji zmienia obraz zestawu skalowania z `image_vmforimageA` na `image_vmforimageB`. Ponadto zostaną zaktualizowane wszystkie bieżące maszyny wirtualne wdrożone przez zestaw skalowania.

Istnieją dwa sposoby uzyskania element PlayBook przykładu:

* [Pobierz element PlayBook](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss_images/04-update-vmss-image.yml) i Zapisz go w `update-vmss-image.yml`.
* Utwórz nowy plik o nazwie `update-vmss-image.yml` i skopiuj do niego następującą zawartość:

```yml
- name: Update scale set image reference
  hosts: localhost
  connection: local
  vars:
    vmss_name: vmsstest
    image_name: image_vmforimageB
    admin_username: vmssadmin
    admin_password: User123!!!abc
  tasks:

  - name: Update scale set - second image
    azure_rm_virtualmachinescaleset:
      resource_group: "{{ resource_group }}"
      name: "{{ vmss_name }}"
      vm_size: Standard_DS1_v2
      admin_username: "{{ admin_username }}"
      admin_password: "{{ admin_password }}"
      ssh_password_enabled: true
      capacity: 3
      virtual_network_name: "{{ vmss_name }}"
      subnet_name: "{{ vmss_name }}"
      upgrade_policy: Manual
      tier: Standard
      managed_disk_type: Standard_LRS
      os_disk_caching: ReadWrite
      image:
        name: "{{ image_name }}"
        resource_group: "{{ resource_group }}"
      load_balancer: "{{ vmss_name }}lb"

  - name: List all of the instances
    azure_rm_virtualmachinescalesetinstance_facts:
      resource_group: "{{ resource_group }}"
      vmss_name: "{{ vmss_name }}"
    register: instances

  - debug:
      var: instances

  - name: manually upgrade all the instances 
    azure_rm_virtualmachinescalesetinstance:
      resource_group: "{{ resource_group }}"
      vmss_name: "{{ vmss_name }}"
      instance_id: "{{ item.instance_id }}"
      latest_model: yes
    with_items: "{{ instances.instances }}"
```

Uruchom element PlayBook przy użyciu polecenia `ansible-playbook`, zastępując `myrg` nazwą grupy zasobów:

```bash
ansible-playbook update-vmss-image.yml --extra-vars "resource_group=myrg"
```

Wróć do przeglądarki i Odśwież stronę. 

Zobaczysz, że obraz obrazu niestandardowego jest zaktualizowany dla tej maszyny wirtualnej.

![Zestaw skalowania jest skojarzony z drugą maszyną wirtualną](media/ansible-vmss-update-image/vmss-update-browser-updated-vmss.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie jest już potrzebne, Usuń zasoby utworzone w tym artykule. 

Zapisz następujący kod jako `cleanup.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myrg
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        force_delete_nonempty: yes
        state: absent
```

Uruchom element PlayBook przy użyciu polecenia `ansible-playbook`:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"] 
> [Rozwiązanie Ansible na platformie Azure](/azure/ansible)