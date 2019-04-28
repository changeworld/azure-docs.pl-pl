---
title: Samouczek — Zarządzanie ruchem internetowym przy użyciu usługi Azure Application Gateway, za pomocą rozwiązania Ansible | Dokumentacja firmy Microsoft
description: Dowiedz się, jak za pomocą rozwiązania Ansible tworzyć i konfigurować bramę usługi Azure Application Gateway do zarządzania ruchem internetowym
keywords: ansible, azure, devops, bash, playbook, application gateway, load balancer, web traffic
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/22/2019
ms.openlocfilehash: 3fd16381aba87b711e799835c9f069e9c53a02ce
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2019
ms.locfileid: "63765973"
---
# <a name="tutorial-manage-web-traffic-with-azure-application-gateway-using-ansible"></a>Samouczek: Zarządzanie ruchem internetowym przy użyciu usługi Azure Application Gateway, za pomocą rozwiązania Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

Usługa [Azure Application Gateway](/azure/application-gateway/overview) to moduł równoważenia obciążenia ruchu internetowego, który umożliwia zarządzanie ruchem kierowanym do aplikacji internetowych. Na podstawie źródłowego adresu IP i port, moduły równoważenia obciążenia tradycyjnych kierować ruch do docelowego adresu IP i port. Usługa Application Gateway zapewnia bardziej precyzyjną stopień kontroli, gdzie można je skierować ruchu na podstawie adresu URL. Na przykład można zdefiniować że jeśli `images` jest ścieżka adresu URL, ruch jest kierowany do określonego zestawu serwerów (znanych jako pulę) skonfigurowana dla obrazów.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Konfigurowanie sieci
> * Tworzenie dwóch wystąpień kontenera platformy Azure przy użyciu obrazów HTTPD
> * Tworzenie bramy aplikacji współdziałającej z wystąpieniami kontenera platformy Azure w puli serwerów

## <a name="prerequisites"></a>Wymagania wstępne

* [!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
* [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Kod elementu playbook w tej sekcji tworzy grupę zasobów platformy Azure. Grupa zasobów to logiczny kontener, na platformie Azure, które zasoby zostały skonfigurowane.  

Zapisz następujący podręcznik jako `rg.yml`:

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

Przed uruchomieniem elementu playbook, zobacz następujące uwagi:

- Nazwa grupy zasobów jest `myResourceGroup`. Ta wartość jest używana w samouczku.
- Grupa zasobów jest tworzona w `eastus` lokalizacji.

Uruchamianie elementu playbook, przy użyciu `ansible-playbook` polecenia:

```bash
ansible-playbook rg.yml
```

## <a name="create-network-resources"></a>Tworzenie zasobów sieciowych

Kod elementu playbook w tej sekcji tworzy sieć wirtualną, aby umożliwić bramy aplikacji do komunikowania się z innymi zasobami.

Zapisz następujący podręcznik jako `vnet_create.yml`:

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

Przed uruchomieniem elementu playbook, zobacz następujące uwagi:

* `vars` Sekcja zawiera wartości, które są używane do tworzenia zasobów sieciowych. 
* Musisz zmienić te wartości dla określonego środowiska.

Uruchamianie elementu playbook, przy użyciu `ansible-playbook` polecenia:

```bash
ansible-playbook vnet_create.yml
```

## <a name="create-servers"></a>Tworzenie serwerów

Kod elementu playbook w tej sekcji tworzy dwa wystąpienia kontenera platformy Azure przy użyciu obrazów host z wieloma ADRESAMI ma być używany jako serwery sieci web w usłudze application gateway.  

Zapisz następujący podręcznik jako `aci_create.yml`:

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

Uruchamianie elementu playbook, przy użyciu `ansible-playbook` polecenia:

```bash
ansible-playbook aci_create.yml
```

## <a name="create-the-application-gateway"></a>Tworzenie bramy aplikacji

Kod elementu playbook w tej sekcji umożliwia utworzenie bramy aplikacji o nazwie `myAppGateway`.  

Zapisz następujący podręcznik jako `appgw_create.yml`:

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

Przed uruchomieniem elementu playbook, zobacz następujące uwagi:

* `appGatewayIP` jest zdefiniowany w `gateway_ip_configurations` bloku. Odwołanie do podsieci jest wymagane do konfiguracji adresu IP bramy.
* `appGatewayBackendPool` jest zdefiniowany w `backend_address_pools` bloku. Brama aplikacji musi mieć co najmniej jedną pulę adresów zaplecza.
* `appGatewayBackendHttpSettings` jest zdefiniowany w `backend_http_settings_collection` bloku. Określa, że portu 80 oraz protokołu HTTP są używane do komunikacji.
* `appGatewayHttpListener` jest zdefiniowany w `backend_http_settings_collection` bloku. Jest to domyślny odbiornik skojarzony z pulą appGatewayBackendPool.
* `appGatewayFrontendIP` jest zdefiniowany w `frontend_ip_configurations` bloku. Przypisuje on adres myAGPublicIPAddress do odbiornika appGatewayHttpListener.
* `rule1` jest zdefiniowany w `request_routing_rules` bloku. Jest to domyślna reguła rozsyłania skojarzona z odbiornikiem appGatewayHttpListener.

Uruchamianie elementu playbook, przy użyciu `ansible-playbook` polecenia:

```bash
ansible-playbook appgw_create.yml
```

Tworzenie bramy aplikacji może potrwać kilka minut.

## <a name="test-the-application-gateway"></a>Testowanie bramy aplikacji

1. W [Utwórz grupę zasobów](#create-a-resource-group) sekcji, określ lokalizację. Zanotuj jego wartość.

1. W [tworzenie zasobów sieciowych](#create-network-resources) sekcji, określ nazwę domeny. Zanotuj jego wartość.

1. Dla adresu URL do testowania, zastępując z następującym wzorcem lokalizacji i domena: `http://<domain>.<location>.cloudapp.azure.com`.

1. Przejdź do adresu URL do testowania.

1. Jeśli zostanie wyświetlona poniższa strona, bramy aplikacji działa zgodnie z oczekiwaniami.

    ![Pomyślnie zakończony test działającej bramy aplikacji](media/ansible-application-gateway-configure/application-gateway.png)

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
        state: absent
```

Uruchamianie elementu playbook, przy użyciu `ansible-playbook` polecenia:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Rozwiązanie Ansible na platformie Azure](/azure/ansible/)