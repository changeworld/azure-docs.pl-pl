---
title: Zarządzanie ruchem internetowym przy użyciu usługi Azure Application Gateway za pomocą rozwiązania Ansible (wersja zapoznawcza)
description: Dowiedz się, jak za pomocą rozwiązania Ansible tworzyć i konfigurować bramę usługi Azure Application Gateway do zarządzania ruchem internetowym
ms.service: ansible
keywords: ansible, azure, devops, bash, playbook, azure application gateway, load balancer, web traffic
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 09/20/2018
ms.openlocfilehash: e3c165c87d6c179141f2ddd44f00f0f62a84b285
ms.sourcegitcommit: 799a4da85cf0fec54403688e88a934e6ad149001
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/02/2018
ms.locfileid: "50912870"
---
# <a name="manage-web-traffic-with-azure-application-gateway-by-using-ansible-preview"></a>Zarządzanie ruchem internetowym przy użyciu usługi Azure Application Gateway za pomocą rozwiązania Ansible (wersja zapoznawcza)

Usługa [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/) to moduł równoważenia obciążenia ruchu internetowego, który umożliwia zarządzanie ruchem kierowanym do aplikacji internetowych.

Rozwiązanie Ansible ułatwia automatyzowanie wdrażania i konfigurowania zasobów w środowisku. Ten artykuł przedstawia sposób tworzenia bramy aplikacji za pomocą rozwiązania Ansible. Dowiesz się również, jak za pomocą bramy zarządzać ruchem do dwóch serwerów internetowych działających w wystąpieniach kontenera platformy Azure.

Ten samouczek przedstawia sposób wykonania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie sieci
> * Tworzenie dwóch wystąpień kontenera platformy Azure przy użyciu obrazów HTTPD
> * Tworzenie bramy aplikacji współdziałającej z wystąpieniami kontenera platformy Azure w puli serwerów

## <a name="prerequisites"></a>Wymagania wstępne

- **Subskrypcja Azure** — jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

> [!Note]
> Rozwiązanie Ansible 2.7 jest wymagane do uruchamiania następujących przykładowych elementów playbook w ramach tego samouczka. Rozwiązanie Ansible 2.7 RC można zainstalować, uruchamiając polecenie `sudo pip install ansible[azure]==2.7.0rc2`. Po wydaniu rozwiązania Ansible 2.7 nie trzeba będzie określać wersji.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.  

Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie **myResourceGroup** w lokalizacji **eastus**.

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus 
  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"
```

Zapisz ten element playbook jako plik *rg.yml*. Aby uruchomić element playbook, użyj polecenia **ansible-playbook** w następujący sposób:

```bash
ansible-playbook rg.yml
```

## <a name="create-network-resources"></a>Tworzenie zasobów sieciowych

Najpierw utwórz sieć wirtualną, aby brama aplikacji mogła komunikować się z innymi zasobami.

Poniższy przykład tworzy sieć wirtualną o nazwie **myVNet**, podsieć o nazwie **myAGSubnet** i publiczny adres IP o nazwie **myAGPublicIPAddress** z domeną o nazwie **mydomain**.

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus 
    vnet_name: myVNet 
    subnet_name: myAGSubnet 
    publicip_name: myAGPublicIPAddress
    publicip_domain: mydomain
  tasks:
    - name: Create a virtual network
      azure_rm_virtualnetwork:
        name: "{{ vnet_name }}"
        resource_group: "{{ resource_group }}"
        address_prefixes_cidr:
            - 10.1.0.0/16
            - 172.100.0.0/16
        dns_servers:
            - 127.0.0.1
            - 127.0.0.2

    - name: Create a subnet
      azure_rm_subnet:
        name: "{{ subnet_name }}"
        virtual_network_name: "{{ vnet_name }}"
        resource_group: "{{ resource_group }}"
        address_prefix_cidr: 10.1.0.0/24

    - name: Create a public IP address
      azure_rm_publicipaddress:
        resource_group: "{{ resource_group }}" 
        allocation_method: Dynamic
        name: "{{ publicip_name }}"
        domain_name_label: "{{ publicip_domain }}"
```

Zapisz ten element playbook jako plik *vnet_create.yml*. Aby uruchomić element playbook, użyj polecenia **ansible-playbook** w następujący sposób:

```bash
ansible-playbook vnet_create.yml
```

## <a name="create-servers"></a>Tworzenie serwerów

Ten przykład przedstawia sposób tworzenia dwóch wystąpień kontenera platformy Azure za pomocą obrazów HTTPD do użycia jako serwerów internetowych bramy aplikacji.  

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus 
    aci_1_name: myACI1
    aci_2_name: myACI2
  tasks:
    - name: Create a container with httpd image 
      azure_rm_containerinstance:
        resource_group: "{{ resource_group }}"
        name: "{{ aci_1_name }}"
        os_type: linux
        ip_address: public
        location: "{{ location }}"
        ports:
          - 80
        containers:
          - name: mycontainer
            image: httpd
            memory: 1.5
            ports:
              - 80

    - name: Create another container with httpd image 
      azure_rm_containerinstance:
        resource_group: "{{ resource_group }}"
        name: "{{ aci_2_name }}"
        os_type: linux
        ip_address: public
        location: "{{ location }}"
        ports:
          - 80
        containers:
          - name: mycontainer
            image: httpd
            memory: 1.5
            ports:
              - 80
```

Zapisz ten element playbook jako plik *aci_create.yml*. Aby uruchomić element playbook, użyj polecenia **ansible-playbook** w następujący sposób:

```bash
ansible-playbook aci_create.yml
```

## <a name="create-the-application-gateway"></a>Tworzenie bramy aplikacji

Poniższy przykład przedstawia sposób tworzenia bramy aplikacji o nazwie **myAppGateway** z konfiguracjami zaplecza, frontonu i protokołu HTTP.  

* Element **appGatewayIP** jest definiowany w bloku **gateway_ip_configurations**. Odwołanie do podsieci jest wymagane do konfiguracji adresu IP bramy.
* Element **appGatewayBackendPool** jest definiowany w bloku **backend_address_pools**. Brama aplikacji musi mieć co najmniej jedną pulę adresów zaplecza.
* Element **appGatewayBackendHttpSettings** jest definiowany w bloku **backend_http_settings_collection**. Określa on, że do komunikacji jest używany port 80 i protokół HTTP.
* Element **appGatewayHttpListener** jest definiowany w bloku **backend_http_settings_collection**. Jest to domyślny odbiornik skojarzony z pulą appGatewayBackendPool.
* Element **appGatewayFrontendIP** jest definiowany w bloku **frontend_ip_configurations**. Przypisuje on adres myAGPublicIPAddress do odbiornika appGatewayHttpListener.
* Element **rule1** jest definiowany w bloku **request_routing_rules**. Jest to domyślna reguła rozsyłania skojarzona z odbiornikiem appGatewayHttpListener.

```yml
- hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    vnet_name: myVNet
    subnet_name: myAGSubnet
    location: eastus
    publicip_name: myAGPublicIPAddress
    appgw_name: myAppGateway
    aci_1_name: myACI1
    aci_2_name: myACI2
  tasks:
    - name: Get info of Subnet
      azure_rm_resource_facts:
        api_version: '2018-08-01'
        resource_group: "{{ resource_group }}"
        provider: network
        resource_type: virtualnetworks
        resource_name: "{{ vnet_name }}"
        subresource:
          - type: subnets
            name: "{{ subnet_name }}"
      register: subnet

    - name: Get info of backend server 2
      azure_rm_resource_facts:
        api_version: '2018-04-01'
        resource_group: "{{ resource_group }}"
        provider: containerinstance
        resource_type: containergroups
        resource_name: "{{ aci_1_name }}"
      register: aci_1_output
    - name: Get info of backend server 2
      azure_rm_resource_facts:
        api_version: '2018-04-01'
        resource_group: "{{ resource_group }}"
        provider: containerinstance
        resource_type: containergroups
        resource_name: "{{ aci_2_name }}"
      register: aci_2_output

    - name: Create instance of Application Gateway
      azure_rm_appgateway:
        resource_group: "{{ resource_group }}"
        name: "{{ appgw_name }}"
        sku:
          name: standard_small
          tier: standard
          capacity: 2
        gateway_ip_configurations:
          - subnet:
              id: "{{ subnet.response[0].id }}"
            name: appGatewayIP
        frontend_ip_configurations:
          - public_ip_address: "{{ publicip_name }}"
            name: appGatewayFrontendIP
        frontend_ports:
          - port: 80
            name: appGatewayFrontendPort
        backend_address_pools:
          - backend_addresses:
              - ip_address: "{{ aci_1_output.response[0].properties.ipAddress.ip }}"
              - ip_address: "{{ aci_2_output.response[0].properties.ipAddress.ip }}"
            name: appGatewayBackendPool
        backend_http_settings_collection:
          - port: 80
            protocol: http
            cookie_based_affinity: enabled
            name: appGatewayBackendHttpSettings
        http_listeners:
          - frontend_ip_configuration: appGatewayFrontendIP
            frontend_port: appGatewayFrontendPort
            name: appGatewayHttpListener
        request_routing_rules:
          - rule_type: Basic
            backend_address_pool: appGatewayBackendPool
            backend_http_settings: appGatewayBackendHttpSettings
            http_listener: appGatewayHttpListener
            name: rule1
```

Zapisz ten element playbook jako plik *appgw_create.yml*. Aby uruchomić element playbook, użyj polecenia **ansible-playbook** w następujący sposób:

```bash
ansible-playbook appgw_create.yml
```

Tworzenie bramy aplikacji może potrwać kilka minut.

## <a name="test-the-application-gateway"></a>Testowanie bramy aplikacji

W przykładowym elemencie playbook dotyczącym zasobów sieci domena o nazwie **mydomain** została utworzona w regionie **eastus**. Przejdź do adresu `http://mydomain.eastus.cloudapp.azure.com` w przeglądarce. Jeśli zostanie wyświetlona poniższa strona, bramy aplikacji działa zgodnie z oczekiwaniami.

![Pomyślnie zakończony test działającej bramy aplikacji](media/ansible-create-configure-application-gateway/applicationgateway.PNG)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie potrzebujesz tych zasobów, możesz je usunąć przez uruchomienie następującego kodu. Usuwa on grupę zasobów o nazwie **myResourceGroup**.

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        state: absent
```

Zapisz ten element playbook jako plik *rg_delete.yml*. Aby uruchomić element playbook, użyj polecenia **ansible-playbook** w następujący sposób:

```bash
ansible-playbook rg_delete.yml
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Rozwiązanie Ansible na platformie Azure](https://docs.microsoft.com/azure/ansible/)
