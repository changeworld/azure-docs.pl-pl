---
title: Samouczek — zarządzanie ruchem internetowym za pomocą usługi Azure Application Gateway przy użyciu rozwiązania ansible
description: Dowiedz się, jak za pomocą rozwiązania Ansible tworzyć i konfigurować bramę usługi Azure Application Gateway do zarządzania ruchem internetowym
keywords: ansible, azure, devops, bash, playbook, application gateway, load balancer, web traffic
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 07f75e39b8c6f592ecd4c48697527493b1109bb9
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/18/2019
ms.locfileid: "74156614"
---
# <a name="tutorial-manage-web-traffic-with-azure-application-gateway-using-ansible"></a>Samouczek: zarządzanie ruchem internetowym za pomocą usługi Azure Application Gateway przy użyciu rozwiązania ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

Usługa [Azure Application Gateway](/azure/application-gateway/overview) to moduł równoważenia obciążenia ruchu internetowego, który umożliwia zarządzanie ruchem kierowanym do aplikacji internetowych. Na podstawie źródłowego adresu IP i portu tradycyjne usługi równoważenia obciążenia kierują ruch do docelowego adresu IP i portu. Application Gateway zapewnia bardziej precyzyjną kontrolę nad tym, w jaki sposób ruch może być kierowany na podstawie adresu URL. Można na przykład określić, że jeśli `images` jest ścieżką adresu URL, ruch jest kierowany do określonego zestawu serwerów (nazywanego pulą) skonfigurowanych dla obrazów.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Konfigurowanie sieci
> * Tworzenie dwóch wystąpień kontenera platformy Azure przy użyciu obrazów HTTPD
> * Tworzenie bramy aplikacji współdziałającej z wystąpieniami kontenera platformy Azure w puli serwerów

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Kod element PlayBook w tej sekcji tworzy grupę zasobów platformy Azure. Grupa zasobów jest kontenerem logicznym, w którym są skonfigurowane zasoby platformy Azure.  

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

Przed uruchomieniem element PlayBook zapoznaj się z następującymi uwagami:

- Nazwa grupy zasobów jest `myResourceGroup`. Ta wartość jest używana w całym samouczku.
- Grupa zasobów zostanie utworzona w lokalizacji `eastus`.

Uruchom element PlayBook przy użyciu polecenia `ansible-playbook`:

```bash
ansible-playbook rg.yml
```

## <a name="create-network-resources"></a>Tworzenie zasobów sieciowych

Kod element PlayBook w tej sekcji tworzy sieć wirtualną umożliwiającą usłudze Application Gateway komunikowanie się z innymi zasobami.

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

Przed uruchomieniem element PlayBook zapoznaj się z następującymi uwagami:

* Sekcja `vars` zawiera wartości, które są używane do tworzenia zasobów sieciowych. 
* Należy zmienić te wartości dla określonego środowiska.

Uruchom element PlayBook przy użyciu polecenia `ansible-playbook`:

```bash
ansible-playbook vnet_create.yml
```

## <a name="create-servers"></a>Tworzenie serwerów

Kod element PlayBook w tej sekcji tworzy dwa wystąpienia kontenera platformy Azure z obrazami HTTP, które mają być używane jako serwery sieci Web dla bramy aplikacji.  

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

Uruchom element PlayBook przy użyciu polecenia `ansible-playbook`:

```bash
ansible-playbook aci_create.yml
```

## <a name="create-the-application-gateway"></a>Tworzenie bramy aplikacji

Kod element PlayBook w tej sekcji tworzy bramę aplikacji o nazwie `myAppGateway`.  

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

Przed uruchomieniem element PlayBook zapoznaj się z następującymi uwagami:

* `appGatewayIP` jest zdefiniowany w bloku `gateway_ip_configurations`. Odwołanie do podsieci jest wymagane do konfiguracji adresu IP bramy.
* `appGatewayBackendPool` jest zdefiniowany w bloku `backend_address_pools`. Brama aplikacji musi mieć co najmniej jedną pulę adresów zaplecza.
* `appGatewayBackendHttpSettings` jest zdefiniowany w bloku `backend_http_settings_collection`. Określa, że port 80 i protokół HTTP są używane do komunikacji.
* `appGatewayHttpListener` jest zdefiniowany w bloku `backend_http_settings_collection`. Jest to domyślny odbiornik skojarzony z pulą appGatewayBackendPool.
* `appGatewayFrontendIP` jest zdefiniowany w bloku `frontend_ip_configurations`. Przypisuje on adres myAGPublicIPAddress do odbiornika appGatewayHttpListener.
* `rule1` jest zdefiniowany w bloku `request_routing_rules`. Jest to domyślna reguła rozsyłania skojarzona z odbiornikiem appGatewayHttpListener.

Uruchom element PlayBook przy użyciu polecenia `ansible-playbook`:

```bash
ansible-playbook appgw_create.yml
```

Tworzenie bramy aplikacji może potrwać kilka minut.

## <a name="test-the-application-gateway"></a>Testowanie bramy aplikacji

1. W sekcji [Tworzenie grupy zasobów](#create-a-resource-group) Określ lokalizację. Zanotuj jego wartość.

1. W sekcji [Tworzenie zasobów sieciowych](#create-network-resources) należy określić domenę. Zanotuj jego wartość.

1. Dla testowego adresu URL, zastępując następujący wzorzec lokalizacją i domeną: `http://<domain>.<location>.cloudapp.azure.com`.

1. Przejdź do adresu URL testu.

1. Jeśli zostanie wyświetlona poniższa strona, bramy aplikacji działa zgodnie z oczekiwaniami.

    ![Pomyślnie zakończony test działającej bramy aplikacji](media/ansible-application-gateway-configure/application-gateway.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie jest już potrzebne, Usuń zasoby utworzone w tym artykule. 

Zapisz następujący kod jako `cleanup.yml`:

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

Uruchom element PlayBook przy użyciu polecenia `ansible-playbook`:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Rozwiązanie Ansible na platformie Azure](/azure/ansible/)