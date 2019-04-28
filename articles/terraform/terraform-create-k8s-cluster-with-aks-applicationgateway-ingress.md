---
title: Tworzenie klastra Kubernetes z usługą Application Gateway jako kontrolerem ruchu przychodzącego za pomocą usługi Azure Kubernetes Service (AKS)
description: Samouczek pokazujący sposób tworzenia klastra Kubernetes za pomocą usługi Azure Kubernetes Service z usługą Application Gateway jako kontrolerem ruchu przychodzącego
services: terraform
ms.service: azure
keywords: terraform, devops, maszyna wirtualna, azure, kubernetes, ruch przychodzący, application gateway
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 1/10/2019
ms.openlocfilehash: 477b2ec1af4c52f51c3ab20ac2ddf7ef043dfcc7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60885478"
---
# <a name="create-a-kubernetes-cluster-with-application-gateway-ingress-controller-using-azure-kubernetes-service-and-terraform"></a>Tworzenie klastra Kubernetes z kontrolerem ruchu przychodzącego Application Gateway za pomocą usług Azure Kubernetes Service i Terraform
Usługa [Azure Kubernetes Service (AKS)](/azure/aks/) zarządza hostowanym środowiskiem Kubernetes. Usługa AKS umożliwia szybkie i łatwe wdrażanie konteneryzowanych aplikacji i zarządzanie nimi bez specjalistycznej wiedzy z zakresu aranżacji kontenerów. Eliminuje to również uciążliwości związane z bieżącą obsługą i konserwacją dzięki aprowizowaniu, aktualizowaniu i skalowaniu zasobów na żądanie bez przełączania aplikacji do trybu offline.

Kontroler ruchu przychodzącego to element oprogramowania dostarczający odwrotny serwer proxy, konfigurowalne trasowanie ruchu oraz zakończenie protokołu TLS dla usług Kubernetes. Zasoby ruchu przychodzącego usług Kubernetes są używane do skonfigurowania zasad ruchu przychodzącego oraz tras dla poszczególnych usług Kubernetes. Dzięki korzystaniu z kontrolera ruchu przychodzącego oraz zasad ruchu przychodzącego można użyć jednego adresu IP do trasowania ruchu w wielu usługach w klastrze Kubernetes. Wszystkie powyższe funkcje są udostępniane przez usługę [Application Gateway](/azure/Application-Gateway/) platformy Azure, co sprawia, że jest to idealny kontroler ruchu przychodzącego dla usługi Kubernetes na platformie Azure. 

W tym samouczku dowiesz się, jak wykonywać następujące zadania podczas tworzenia klastra [Kubernetes](https://www.redhat.com/en/topics/containers/what-is-kubernetes) przy użyciu usługi AKS z kontrolerem ruchu przychodzącego Application Gateway:

> [!div class="checklist"]
> * Definiowanie klastra Kubernetes za pomocą języka HCL (HashiCorp Language)
> * Korzystanie z narzędzia Terraform do utworzenia zasobu Application Gateway
> * Tworzenie klastra Kubernetes za pomocą narzędzia Terraform i usługi AKS
> * Testowanie dostępności klastra Kubernetes za pomocą narzędzia kubectl

## <a name="prerequisites"></a>Wymagania wstępne

- **Subskrypcja platformy Azure**: Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- **Skonfigurowane narzędzie Terraform**: postępuj zgodnie ze wskazówkami w artykule [Terraform and configure access to Azure (Terraform i konfigurowanie dostępu do platformy Azure)](/azure/virtual-machines/linux/terraform-install-configure).

- **Jednostka usługi platformy Azure**: postępuj zgodnie z instrukcjami w sekcji dotyczącej **tworzenia jednostki usługi** artykułu [Tworzenie jednostki usługi platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest). Zapisz wartości pozycji appId, displayName i password.
  - Zanotuj wartość pozycji Object ID jednostki usługi, uruchamiając następujące polecenie.

    ```bash
    az ad sp list --display-name <displayName>
    ```

## <a name="create-the-directory-structure"></a>Tworzenie struktury katalogów
Pierwszym krokiem jest utworzenie katalogu, w którym będą przechowywane pliki konfiguracji narzędzia Terraform dla tego ćwiczenia.

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).

1. Otwórz usługę [Azure Cloud Shell](/azure/cloud-shell/overview). Jeśli środowisko nie zostało wybrane wcześniej, wybierz pozycję **Bash** jako swoje środowisko.

    ![Wiersz polecenia usługi Cloud Shell](./media/terraform-k8s-cluster-appgw-with-tf-aks/azure-portal-cloud-shell-button-min.png)

1. Zmień katalog na `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Utwórz katalog o nazwie `terraform-aks-k8s`.

    ```bash
    mkdir terraform-aks-appgw-ingress
    ```

1. Zmień katalog na nowy:

    ```bash
    cd terraform-aks-appgw-ingress
    ```

## <a name="declare-the-azure-provider"></a>Deklarowanie dostawcy platformy Azure
Utwórz plik konfiguracji narzędzia Terraform zawierający deklarację dostawcy platformy Azure.

1. W usłudze Cloud Shell utwórz plik o nazwie `main.tf`.

    ```bash
    vi main.tf
    ```

1. Przejdź do trybu wstawiania, naciskając klawisz I.

1. Wklej następujący kod do edytora:

    ```JSON
    provider "azurerm" {
        version = "~>1.18"
    }

    terraform {
        backend "azurerm" {}
    }
    ```

1. Opuść tryb wstawiania, naciskając klawisz **Esc**.

1. Zapisz plik i zamknij edytor VI, wprowadzając następujące polecenie:

    ```bash
    :wq
    ```
   ## <a name="define-input-variables"></a>Definiowanie zmiennych wejściowych
   Tworzenie pliku konfiguracji narzędzia Terraform z listą wszystkich zmiennych wymaganych podczas tego wdrożenia
1. W usłudze Cloud Shell utwórz plik o nazwie `variables.tf`
    ```bash
    vi variables.tf
    ```
1. Przejdź do trybu wstawiania, naciskając klawisz I.

2. Wklej następujący kod do edytora:
    
    ```JSON
    variable "resource_group_name" {
      description = "Name of the resource group already created."
    }
    
    variable "location" {
      description = "Location of the cluster."
    }
    
    variable "aks_service_principal_app_id" {
      description = "Application ID/Client ID  of the service principal. Used by AKS to manage AKS related resources on Azure like vms, subnets."
    }
    
    variable "aks_service_principal_client_secret" {
      description = "Secret of the service principal. Used by AKS to manage Azure."
    }
    
    variable "aks_service_principal_object_id" {
      description = "Object ID of the service principal."
    }
    
    variable "virtual_network_name" {
      description = "Virtual network name"
      default     = "aksVirtualNetwork"
    }
    
    variable "virtual_network_address_prefix" {
      description = "Containers DNS server IP address."
      default     = "15.0.0.0/8"
    }
    
    variable "aks_subnet_name" {
      description = "AKS Subnet Name."
      default     = "kubesubnet"
    }
    
    variable "aks_subnet_address_prefix" {
      description = "Containers DNS server IP address."
      default     = "15.0.0.0/16"
    }
    
    variable "app_gateway_subnet_address_prefix" {
      description = "Containers DNS server IP address."
      default     = "15.1.0.0/16"
    }
    
    variable "app_gateway_name" {
      description = "Name of the Application Gateway."
      default = "ApplicationGateway1"
    }
    
    variable "app_gateway_sku" {
      description = "Name of the Application Gateway SKU."
      default = "Standard_v2"
    }
    
    
    variable "app_gateway_tier" {
      description = "Tier of the Application Gateway SKU."
      default = "Standard_v2"
    }
    
    
    variable "aks_name" {
      description = "Name of the AKS cluster."
      default     = "aks-cluster1"
    }
    variable "aks_dns_prefix" {
      description = "Optional DNS prefix to use with hosted Kubernetes API server FQDN."
      default     = "aks"
    }
    
    
    variable "aks_agent_os_disk_size" {
      description = "Disk size (in GB) to provision for each of the agent pool nodes. This value ranges from 0 to 1023. Specifying 0 will apply the default disk size for that agentVMSize."
      default     = 40
    }
    
    variable "aks_agent_count" {
      description = "The number of agent nodes for the cluster."
      default     = 3
    }
    
    variable "aks_agent_vm_size" {
      description = "The size of the Virtual Machine."
      default     = "Standard_D3_v2"
    }
    
    variable "kubernetes_version" {
      description = "The version of Kubernetes."
      default     = "1.11.5"
    }
    
    variable "aks_service_cidr" {
      description = "A CIDR notation IP range from which to assign service cluster IPs."
      default     = "10.0.0.0/16"
    }
    
    variable "aks_dns_service_ip" {
      description = "Containers DNS server IP address."
      default     = "10.0.0.10"
    }
    
    variable "aks_docker_bridge_cidr" {
      description = "A CIDR notation IP for Docker bridge."
      default     = "172.17.0.1/16"
    }
    
    variable "aks_enable_rbac" {
      description = "Enable RBAC on the AKS cluster. Defaults to false."
      default     = "false"
    }
    
    variable "vm_user_name" {
      description = "User name for the VM"
      default     = "vmuser1"
    }
    
    variable "public_ssh_key_path" {
      description = "Public key path for SSH."
      default     = "~/.ssh/id_rsa.pub"
    }
    
    variable "tags" {
      type = "map"
    
      default = {
        source = "terraform"
      }
    }
    ```

## <a name="define-the-resources"></a>Definiowanie zasobów 
Utwórz plik konfiguracji narzędzia Terraform używany do utworzenia wszystkich zasobów. 

1. W usłudze Cloud Shell utwórz plik o nazwie `resources.tf`.

    ```bash
    vi resources.tf
    ```

1. Przejdź do trybu wstawiania, naciskając klawisz I.

1. Wklej następujące bloki kodu do edytora:

    a. Tworzenie bloków lokalnych dla zmiennych obliczanych do ponownego użycia

    ```JSON
    # # Locals block for hardcoded names. 
    locals {
        backend_address_pool_name      = "${azurerm_virtual_network.test.name}-beap"
        frontend_port_name             = "${azurerm_virtual_network.test.name}-feport"
        frontend_ip_configuration_name = "${azurerm_virtual_network.test.name}-feip"
        http_setting_name              = "${azurerm_virtual_network.test.name}-be-htst"
        listener_name                  = "${azurerm_virtual_network.test.name}-httplstn"
        request_routing_rule_name      = "${azurerm_virtual_network.test.name}-rqrt"
        app_gateway_subnet_name = "appgwsubnet"
    }
    ```
    b. Tworzenie źródła danych dla pozycji Grupa zasobów, Tożsamość nowego użytkownika
    ```JSON
    data "azurerm_resource_group" "rg" {
      name = "${var.resource_group_name}"
    }
    
    # User Assigned Idntities 
    resource "azurerm_user_assigned_identity" "testIdentity" {
      resource_group_name = "${data.azurerm_resource_group.rg.name}"
      location            = "${data.azurerm_resource_group.rg.location}"
    
      name = "identity1"
    
      tags = "${var.tags}"
    }
    ```
    c. Tworzenie podstawowych zasobów sieciowych
   ```JSON
    resource "azurerm_virtual_network" "test" {
      name                = "${var.virtual_network_name}"
      location            = "${data.azurerm_resource_group.rg.location}"
      resource_group_name = "${data.azurerm_resource_group.rg.name}"
      address_space       = ["${var.virtual_network_address_prefix}"]
    
      subnet {
        name           = "${var.aks_subnet_name}"
        address_prefix = "${var.aks_subnet_address_prefix}" 
      }
    
      subnet {
        name           = "appgwsubnet"
        address_prefix = "${var.app_gateway_subnet_address_prefix}"
      }
    
      tags = "${var.tags}"
    }
    
    data "azurerm_subnet" "kubesubnet" {
      name                 = "${var.aks_subnet_name}"
      virtual_network_name = "${azurerm_virtual_network.test.name}"
      resource_group_name  = "${data.azurerm_resource_group.rg.name}"
    }
    
    data "azurerm_subnet" "appgwsubnet" {
      name                 = "appgwsubnet"
      virtual_network_name = "${azurerm_virtual_network.test.name}"
      resource_group_name  = "${data.azurerm_resource_group.rg.name}"
    }
    
    # Public Ip 
    resource "azurerm_public_ip" "test" {
      name                         = "publicIp1"
      location                     = "${data.azurerm_resource_group.rg.location}"
      resource_group_name          = "${data.azurerm_resource_group.rg.name}"
      public_ip_address_allocation = "static"
      sku                          = "Standard"
    
      tags = "${var.tags}"
    }
    ```
    d. Tworzenie zasobu usługi Application Gateway
    ```JSON
    resource "azurerm_application_gateway" "network" {
      name                = "${var.app_gateway_name}"
      resource_group_name = "${data.azurerm_resource_group.rg.name}"
      location            = "${data.azurerm_resource_group.rg.location}"
    
      sku {
        name     = "${var.app_gateway_sku}"
        tier     = "Standard_v2"
        capacity = 2
      }
    
      gateway_ip_configuration {
        name      = "appGatewayIpConfig"
        subnet_id = "${data.azurerm_subnet.appgwsubnet.id}"
      }
    
      frontend_port {
        name = "${local.frontend_port_name}"
        port = 80
      }
    
      frontend_port {
        name = "httpsPort"
        port = 443
      }
    
      frontend_ip_configuration {
        name                 = "${local.frontend_ip_configuration_name}"
        public_ip_address_id = "${azurerm_public_ip.test.id}"
      }
    
      backend_address_pool {
        name = "${local.backend_address_pool_name}"
      }
    
      backend_http_settings {
        name                  = "${local.http_setting_name}"
        cookie_based_affinity = "Disabled"
        port                  = 80
        protocol              = "Http"
        request_timeout       = 1
      }
    
      http_listener {
        name                           = "${local.listener_name}"
        frontend_ip_configuration_name = "${local.frontend_ip_configuration_name}"
        frontend_port_name             = "${local.frontend_port_name}"
        protocol                       = "Http"
      }
    
      request_routing_rule {
        name                       = "${local.request_routing_rule_name}"
        rule_type                  = "Basic"
        http_listener_name         = "${local.listener_name}"
        backend_address_pool_name  = "${local.backend_address_pool_name}"
        backend_http_settings_name = "${local.http_setting_name}"
      }
    
      tags = "${var.tags}"
    
      depends_on = ["azurerm_virtual_network.test", "azurerm_public_ip.test"]
    }
    ```
    e. Tworzenie przypisań roli
    ```JSON
    resource "azurerm_role_assignment" "ra1" {
      scope                = "${data.azurerm_subnet.kubesubnet.id}"
      role_definition_name = "Network Contributor"
      principal_id         = "${var.aks_service_principal_object_id }"
    
      depends_on = ["azurerm_virtual_network.test"]
    }
    
    resource "azurerm_role_assignment" "ra2" {
      scope                = "${azurerm_user_assigned_identity.testIdentity.id}"
      role_definition_name = "Managed Identity Operator"
      principal_id         = "${var.aks_service_principal_object_id}"
      depends_on           = ["azurerm_user_assigned_identity.testIdentity"]
    }
    
    resource "azurerm_role_assignment" "ra3" {
      scope                = "${azurerm_application_gateway.network.id}"
      role_definition_name = "Contributor"
      principal_id         = "${azurerm_user_assigned_identity.testIdentity.principal_id}"
      depends_on           = ["azurerm_user_assigned_identity.testIdentity", "azurerm_application_gateway.network"]
    }
    
    resource "azurerm_role_assignment" "ra4" {
      scope                = "${data.azurerm_resource_group.rg.id}"
      role_definition_name = "Reader"
      principal_id         = "${azurerm_user_assigned_identity.testIdentity.principal_id}"
      depends_on           = ["azurerm_user_assigned_identity.testIdentity", "azurerm_application_gateway.network"]
    }
    ```
    f. Tworzenie klastra Kubernetes
    ```JSON
    resource "azurerm_kubernetes_cluster" "k8s" {
      name       = "${var.aks_name}"
      location   = "${data.azurerm_resource_group.rg.location}"
      dns_prefix = "${var.aks_dns_prefix}"
    
      resource_group_name = "${data.azurerm_resource_group.rg.name}"
    
      linux_profile {
        admin_username = "${var.vm_user_name}"
    
        ssh_key {
          key_data = "${file(var.public_ssh_key_path)}"
        }
      }
    
      addon_profile {
        http_application_routing {
          enabled = false
        }
      }
    
      agent_pool_profile {
        name            = "agentpool"
        count           = "${var.aks_agent_count}"
        vm_size         = "${var.aks_agent_vm_size}"
        os_type         = "Linux"
        os_disk_size_gb = "${var.aks_agent_os_disk_size}"
        vnet_subnet_id  = "${data.azurerm_subnet.kubesubnet.id}"
      }
    
      service_principal {
        client_id     = "${var.aks_service_principal_app_id}"
        client_secret = "${var.aks_service_principal_client_secret}"
      }
    
      network_profile {
        network_plugin     = "azure"
        dns_service_ip     = "${var.aks_dns_service_ip}"
        docker_bridge_cidr = "${var.aks_docker_bridge_cidr}"
        service_cidr       = "${var.aks_service_cidr}"
      }
    
      depends_on = ["azurerm_virtual_network.test", "azurerm_application_gateway.network"]
      tags       = "${var.tags}"
    }
    
    ```

    Powyższy kod ustawia wartości name, location i resource_group_name klastra. Ponadto ustawiana jest wartość dns_prefix, która stanowi część w pełni kwalifikowanej nazwy domeny (FQDN) umożliwiającej dostęp do klastra.

    Rekord **linux_profile** pozwala na skonfigurowanie ustawień umożliwiających logowanie się do węzłów procesu roboczego przy użyciu protokołu SSH.

    W przypadku usługi AKS płacisz tylko za węzły procesu roboczego. Rekord **agent_pool_profile** konfiguruje szczegółowe informacje dotyczące tych węzłów procesu roboczego. Rekord **agent_pool_profile** zawiera liczbę węzłów procesu roboczego do utworzenia i typ węzłów procesu roboczego. Jeśli w przyszłości będzie konieczne powiększenie lub zmniejszenie klastra, zmodyfikuj wartość **count** w tym rekordzie.

1. Opuść tryb wstawiania, naciskając klawisz **Esc**.

1. Zapisz plik i zamknij edytor VI, wprowadzając następujące polecenie:

    ```bash
    :wq
    ```


## <a name="create-a-terraform-output-file"></a>Tworzenie pliku wyjściowego narzędzia Terraform
[Dane wyjściowe narzędzia Terraform](https://www.terraform.io/docs/configuration/outputs.html) umożliwiają definiowanie wartości, które będą wyróżnione dla użytkownika, gdy narzędzie Terraform zastosuje plan, i do których będzie można wysyłać zapytania za pomocą polecenia `terraform output`. W tej sekcji utworzysz plik wyjściowy umożliwiający uzyskanie dostępu do klastra za pomocą narzędzia [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/).

1. W usłudze Cloud Shell utwórz plik o nazwie `output.tf`.

    ```bash
    vi output.tf
    ```

1. Przejdź do trybu wstawiania, naciskając klawisz I.

1. Wklej następujący kod do edytora:

    ```JSON
    output "client_key" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.client_key}"
    }

    output "client_certificate" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.client_certificate}"
    }

    output "cluster_ca_certificate" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.cluster_ca_certificate}"
    }

    output "cluster_username" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.username}"
    }

    output "cluster_password" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.password}"
    }

    output "kube_config" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config_raw}"
    }

    output "host" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.host}"
    }
    ```

1. Opuść tryb wstawiania, naciskając klawisz **Esc**.

1. Zapisz plik i zamknij edytor VI, wprowadzając następujące polecenie:

    ```bash
    :wq
    ```

## <a name="set-up-azure-storage-to-store-terraform-state"></a>Konfigurowanie usługi Azure Storage na potrzeby przechowywania stanu narzędzia Terraform
Narzędzie Terraform śledzi stan lokalnie za pośrednictwem pliku `terraform.tfstate`. Ten wzorzec sprawdza się dobrze w środowisku jednoosobowym. Jednak w bardziej praktycznym środowisku wieloosobowym musisz śledzić stan na serwerze przy użyciu usługi [Azure Storage](/azure/storage/). W tej sekcji pobierzesz niezbędne informacje o koncie magazynu (nazwę konta i klucz konta) oraz utworzysz kontener magazynu, w którym będą przechowywane informacje o stanie narzędzia Terraform.

1. W witrynie Azure Portal wybierz pozycję **Wszystkie usługi** z menu po lewej stronie.

1. Wybierz pozycję **Konta usługi Storage**.

1. Na karcie **Konta usługi Storage** wybierz nazwę konta magazynu, na którym ma być przechowywany stan narzędzia Terraform. Na przykład możesz skorzystać z konta magazynu utworzonego przy pierwszym otwarciu usługi Cloud Shell.  Nazwa konta magazynu utworzonego przez usługę Cloud Shell zwykle zaczyna się od ciągu `cs`, po którym następuje losowy ciąg liter i cyfr. **Zanotuj nazwę wybranego konta magazynu, ponieważ będzie ona potrzebna później.**

1. Na karcie konta magazynu wybierz pozycję **Klucze dostępu**.

    ![Menu konta magazynu](./media/terraform-k8s-cluster-appgw-with-tf-aks/storage-account.png)

1. Zanotuj wartość **klucza** **key1**. (Wybranie ikony po prawej stronie klucza spowoduje skopiowanie wartości do schowka).

    ![Klucze dostępu konta magazynu](./media/terraform-k8s-cluster-appgw-with-tf-aks/storage-account-access-key.png)

1. W usłudze Cloud Shell utwórz kontener na koncie usługi Azure Storage (zamień symbole zastępcze &lt;YourAzureStorageAccountName> i &lt;YourAzureStorageAccountAccessKey> na wartości odpowiednie dla Twojego konta usługi Azure Storage).

    ```bash
    az storage container create -n tfstate --account-name <YourAzureStorageAccountName> --account-key <YourAzureStorageAccountKey>
    ```

## <a name="create-the-kubernetes-cluster"></a>Tworzenie klastra Kubernetes
W tej sekcji dowiesz się, jak za pomocą polecenia `terraform init` utworzyć zasoby zdefiniowane w plikach konfiguracji, które zostały utworzone w poprzednich sekcjach.

1. W usłudze Cloud Shell zainicjuj narzędzie Terraform (zamień symbole zastępcze &lt;YourAzureStorageAccountName> i &lt;YourAzureStorageAccountAccessKey> na wartości odpowiednie dla Twojego konta usługi Azure Storage).

    ```bash
    terraform init -backend-config="storage_account_name=<YourAzureStorageAccountName>" -backend-config="container_name=tfstate" -backend-config="access_key=<YourStorageAccountAccessKey>" -backend-config="key=codelab.microsoft.tfstate" 
    ```
    
    Polecenie `terraform init` spowoduje wyświetlenie informacji o pomyślnym zainicjowaniu wtyczki dostawcy i zaplecza:

    ![Przykładowe wyniki wykonania polecenia „terraform init”](./media/terraform-k8s-cluster-appgw-with-tf-aks/terraform-init-complete.png)

1. Utwórz plik zmiennych, aby dostarczyć wartości danych wejściowych. W usłudze Cloud Shell utwórz plik o nazwie `main.tf`.

    ```bash
    vi terraform.tfvars
    ```

1. Przejdź do trybu wstawiania, naciskając klawisz I.

1. Wklej następujące wcześniej utworzone zmienne do edytora:

    ```JSON
      resource_group_name = <Name of the Resource Group already created>

      location = <Location of the Resource Group>
        
      aks_service_principal_app_id = <Service Principal AppId>
        
      aks_service_principal_client_secret = <Service Principal Client Secret>
        
      aks_service_principal_object_id = <Service Principal Object Id>
        
    ```

1. Opuść tryb wstawiania, naciskając klawisz **Esc**.

1. Zapisz plik i zamknij edytor VI, wprowadzając następujące polecenie:

    ```bash
    :wq
    ```

1. Uruchom polecenie `terraform plan`, aby utworzyć plan narzędzia Terraform definiujący elementy infrastruktury. 

    ```bash
    terraform plan -out out.plan
    ```

    Polecenie `terraform plan` spowoduje wyświetlenie zasobów, które zostaną utworzone po uruchomieniu polecenia `terraform apply`:

    ![Przykładowe wyniki wykonania polecenia „terraform plan”](./media/terraform-k8s-cluster-appgw-with-tf-aks/terraform-plan-complete.png)

1. Uruchom polecenie `terraform apply`, aby zastosować plan w celu utworzenia klastra Kubernetes. Proces tworzenia klastra Kubernetes może potrwać kilka minut, powodując przekroczenie limitu czasu sesji usługi Cloud Shell. W przypadku przekroczenia limitu czasu sesji usługi Cloud Shell możesz wykonać kroki opisane w sekcji „Odzyskiwanie sprawności po przekroczeniu limitu czasu w usłudze Cloud Shell”, aby ukończyć ten samouczek.

    ```bash
    terraform apply out.plan
    ```

    Polecenie `terraform apply` spowoduje wyświetlenie wyników tworzenia zasobów zdefiniowanych w plikach konfiguracji:

    ![Przykładowe wyniki wykonania polecenia „terraform apply”](./media/terraform-k8s-cluster-appgw-with-tf-aks/terraform-apply-complete.png)

1. W witrynie Azure Portal wybierz pozycję **Grupy zasobów** z menu po lewej stronie, aby wyświetlić zasoby utworzone dla nowego klastra Kubernetes w wybranej grupie zasobów.

    ![Wiersz polecenia usługi Cloud Shell](./media/terraform-k8s-cluster-appgw-with-tf-aks/k8s-resources-created.png)

## <a name="recover-from-a-cloud-shell-timeout"></a>Odzyskiwanie sprawności po przekroczeniu limitu czasu w usłudze Cloud Shell
Jeśli limit czasu sesji usługi Cloud Shell zostanie przekroczony, możesz wykonać poniższe czynności, aby odzyskać usługę:

1. Uruchom sesję usługi Cloud Shell.

1. Przejdź do katalogu zawierającego pliki konfiguracji narzędzia Terraform.

    ```bash
    cd /clouddrive/terraform-aks-k8s
    ```

1. Uruchom następujące polecenie:

    ```bash
    export KUBECONFIG=./azurek8s
    ```
    
## <a name="test-the-kubernetes-cluster"></a>Testowanie klastra Kubernetes
Nowo utworzony klaster można sprawdzić za pomocą narzędzi usługi Kubernetes.

1. Pobierz konfigurację usługi Kubernetes ze stanu narzędzia Terraform i zapisz ją w pliku możliwym do odczytania przez narzędzie kubectl.

    ```bash
    echo "$(terraform output kube_config)" > ./azurek8s
    ```

1. Ustaw zmienną środowiskową tak, aby narzędzie kubectl pobrało poprawną konfigurację.

    ```bash
    export KUBECONFIG=./azurek8s
    ```

1. Sprawdź kondycję klastra.

    ```bash
    kubectl get nodes
    ```

    Powinny zostać wyświetlone szczegóły węzłów procesu roboczego. Wszystkie one powinny mieć stan **Gotowy**, jak pokazano na poniższej ilustracji:

    ![Narzędzie kubectl umożliwia sprawdzenie kondycji klastra Kubernetes](./media/terraform-k8s-cluster-appgw-with-tf-aks/kubectl-get-nodes.png)


## <a name="next-steps"></a>Kolejne kroki
W tym artykule przedstawiono sposób tworzenia klastra Kubernetes przy użyciu narzędzia Terraform i usługi AKS. Poniżej przedstawiono kilka dodatkowych zasobów zawierających więcej informacji na temat narzędzia Terraform na platformie Azure.
 
 > [!div class="nextstepaction"] 
 > [Centrum narzędzia Terraform w witrynie Microsoft.com](https://docs.microsoft.com/azure/terraform/)
 
