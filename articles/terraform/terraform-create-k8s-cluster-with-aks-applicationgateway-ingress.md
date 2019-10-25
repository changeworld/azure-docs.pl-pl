---
title: Tworzenie kontrolera Application Gateway transferu danych przychodzących w usłudze Azure Kubernetes Service (AKS)
description: Samouczek pokazujący sposób tworzenia klastra Kubernetes za pomocą usługi Azure Kubernetes Service z usługą Application Gateway jako kontrolerem ruchu przychodzącego
services: terraform
ms.service: azure
keywords: terraform, devops, maszyna wirtualna, azure, kubernetes, ruch przychodzący, application gateway
author: tomarcher
manager: gwallace
ms.author: tarcher
ms.topic: tutorial
ms.date: 10/23/2019
ms.openlocfilehash: 82cee1e5c93eb21fa8db29985d26fe75bde970d2
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72882604"
---
# <a name="create-an-application-gateway-ingress-controller-in-azure-kubernetes-service"></a>Tworzenie Application Gatewayowego kontrolera w usłudze Azure Kubernetes

Usługa [Azure Kubernetes Service (AKS)](/azure/aks/) zarządza hostowanym środowiskiem Kubernetes. Usługa AKS umożliwia szybkie i łatwe wdrażanie konteneryzowanych aplikacji i zarządzanie nimi bez specjalistycznej wiedzy z zakresu aranżacji kontenerów. AKS również eliminuje obciążenie związane z podejmowaniem aplikacji w trybie offline na potrzeby zadań operacyjnych i konserwacji. Korzystając z AKS, te zadania — w tym Inicjowanie obsługi, uaktualnianie i skalowanie zasobów — można wykonać na żądanie.

Kontroler transferu danych przychodzących udostępnia różne funkcje usług Kubernetes Services. Te funkcje obejmują zwrotny serwer proxy, konfigurowalne Routing ruchu i zakończenie protokołu TLS. Kubernetes zasoby związane z ruchem przychodzącym są używane do konfigurowania reguł związanych z transferem danych przychodzących dla poszczególnych usług Kubernetes. Przy użyciu kontrolera transferu danych przychodzących i zasad transferu pojedynczy adres IP może kierować ruch do wielu usług w klastrze Kubernetes. Wszystkie te funkcje są udostępniane przez usługę Azure [Application Gateway](/azure/Application-Gateway/), co sprawia, że jest to idealny kontroler transferu danych przychodzących dla Kubernetes na platformie Azure. 

W tym samouczku dowiesz się, jak wykonywać następujące zadania:

> [!div class="checklist"]
> * Utwórz klaster [Kubernetes](https://www.redhat.com/en/topics/containers/what-is-kubernetes) przy użyciu AKS z Application Gateway jako kontroler transferu danych przychodzących.
> * Użyj HCL (HashiCorp Language), aby zdefiniować klaster Kubernetes.
> * Użyj Terraform, aby utworzyć zasób Application Gateway.
> * Utwórz klaster Kubernetes za pomocą Terraform i AKS.
> * Użyj narzędzia polecenia kubectl, aby przetestować dostępność klastra Kubernetes.

## <a name="prerequisites"></a>Wymagania wstępne

- **Subskrypcja platformy Azure**: jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- **Skonfiguruj narzędzie Terraform**: postępuj zgodnie ze wskazówkami w artykule [Terraform and configure access to Azure](/azure/virtual-machines/linux/terraform-install-configure) (Terraform i konfigurowanie dostępu do platformy Azure).

- **Jednostka usługi platformy Azure**: postępuj zgodnie z instrukcjami w sekcji dotyczącej **tworzenia jednostki usługi** artykułu [Tworzenie jednostki usługi platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest). Zapisz wartości pozycji appId, displayName i password.

- **Uzyskaj identyfikator obiektu jednostki usługi**: Uruchom następujące polecenie w Cloud Shell: `az ad sp list --display-name <displayName>`

## <a name="create-the-directory-structure"></a>Tworzenie struktury katalogów

Pierwszym krokiem jest utworzenie katalogu, w którym będą przechowywane pliki konfiguracji narzędzia Terraform dla tego ćwiczenia.

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).

1. Otwórz usługę [Azure Cloud Shell](/azure/cloud-shell/overview).

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
    code main.tf
    ```

1. Wklej następujący kod do edytora:

    ```hcl
    provider "azurerm" {
        version = "~>1.18"
    }

    terraform {
        backend "azurerm" {}
    }
    ```

1. Zapisz plik ( **&lt;Ctrl > S**) i wyjdź z edytora ( **&lt;Ctrl > Q**).

## <a name="define-input-variables"></a>Definiowanie zmiennych wejściowych

Utwórz plik konfiguracji Terraform, który zawiera listę wszystkich zmiennych wymaganych dla tego wdrożenia.

1. W usłudze Cloud Shell utwórz plik o nazwie `variables.tf`.

    ```bash
    code variables.tf
    ```

1. Wklej następujący kod do edytora:
    
    ```hcl
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
      description = "Disk size (in GB) to provision for each of the agent pool nodes. This value ranges from 0 to 1023. Specifying 0 applies the default disk size for that agentVMSize."
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

1. Zapisz plik ( **&lt;Ctrl > S**) i wyjdź z edytora ( **&lt;Ctrl > Q**).

## <a name="define-the-resources"></a>Definiowanie zasobów 
Utwórz plik konfiguracji narzędzia Terraform używany do utworzenia wszystkich zasobów. 

1. W usłudze Cloud Shell utwórz plik o nazwie `resources.tf`.

    ```bash
    code resources.tf
    ```

1. Wklej poniższy blok kodu, aby utworzyć blok lokalny dla zmiennych obliczanych do ponownego użycia:

    ```hcl
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

1. Wklej poniższy blok kodu, aby utworzyć źródło danych dla grupy zasobów, Nowa tożsamość użytkownika:

    ```hcl
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

1. Wklej poniższy blok kodu, aby utworzyć podstawowe zasoby sieciowe:

    ```hcl
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

1. Wklej poniższy blok kodu, aby utworzyć zasób Application Gateway:

    ```hcl
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

1. Wklej poniższy blok kodu, aby utworzyć przypisania ról:

    ```hcl
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

1. Wklej następujący blok kodu, aby utworzyć klaster Kubernetes:

    ```hcl
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

1. Zapisz plik i Zamknij Edytor.

Kod przedstawiony w tej sekcji określa nazwę klastra, lokalizacji i resource_group_name. Wartość `dns_prefix` — która stanowi część w pełni kwalifikowanej nazwy domeny (FQDN) używanej do uzyskiwania dostępu do klastra — jest ustawiona.

Rekord `linux_profile` umożliwia skonfigurowanie ustawień umożliwiających zalogowanie się w węzłach procesu roboczego przy użyciu protokołu SSH.

W przypadku usługi AKS płacisz tylko za węzły procesu roboczego. Rekord `agent_pool_profile` konfiguruje szczegóły dla tych węzłów procesu roboczego. `agent_pool_profile record` obejmuje liczbę węzłów procesu roboczego do utworzenia i typ węzłów procesu roboczego. Jeśli w przyszłości konieczne jest skalowanie klastra w górę lub w dół, należy zmodyfikować wartość `count` w tym rekordzie.

## <a name="create-a-terraform-output-file"></a>Tworzenie pliku wyjściowego narzędzia Terraform

Dane [wyjściowe Terraform](https://www.terraform.io/docs/configuration/outputs.html) umożliwiają zdefiniowanie wartości, które są wyróżnione użytkownikowi, gdy Terraform stosuje plan, i można wykonać zapytania przy użyciu `terraform output` polecenia. W tej sekcji utworzysz plik wyjściowy umożliwiający uzyskanie dostępu do klastra za pomocą narzędzia [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/).

1. W usłudze Cloud Shell utwórz plik o nazwie `output.tf`.

    ```bash
    code output.tf
    ```

1. Wklej następujący kod do edytora:

    ```hcl
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

    output "identity_resource_id" {
        value = "${azurerm_user_assigned_identity.testIdentity.id}"
    }

    output "identity_client_id" {
        value = "${azurerm_user_assigned_identity.testIdentity.client_id}"
    }
    ```

1. Zapisz plik ( **&lt;Ctrl > S**) i wyjdź z edytora ( **&lt;Ctrl > Q**).

## <a name="configure-azure-storage-to-store-terraform-state"></a>Konfigurowanie usługi Azure Storage do przechowywania stanu Terraform

Narzędzie Terraform śledzi stan lokalnie za pośrednictwem pliku `terraform.tfstate`. Ten wzorzec sprawdza się dobrze w środowisku jednoosobowym. Jednak w bardziej praktycznym środowisku wieloosobowym musisz śledzić stan na serwerze przy użyciu usługi [Azure Storage](/azure/storage/). W tej sekcji dowiesz się, jak pobrać niezbędne informacje o koncie magazynu i utworzyć kontener magazynu. Informacje o stanie Terraform są następnie przechowywane w tym kontenerze.

1. W witrynie Azure Portal wybierz pozycję **Wszystkie usługi** z menu po lewej stronie.

1. Wybierz pozycję **Konta usługi Storage**.

1. Na karcie **Konta usługi Storage** wybierz nazwę konta magazynu, na którym ma być przechowywany stan narzędzia Terraform. Na przykład możesz skorzystać z konta magazynu utworzonego przy pierwszym otwarciu usługi Cloud Shell.  Nazwa konta magazynu utworzonego przez usługę Cloud Shell zwykle zaczyna się od ciągu `cs`, po którym następuje losowy ciąg liter i cyfr. 

    Zanotuj wybrane konto magazynu, które będzie potrzebne później.

1. Na karcie konta magazynu wybierz pozycję **Klucze dostępu**.

    ![Menu konta magazynu](./media/terraform-k8s-cluster-appgw-with-tf-aks/storage-account.png)

1. Zanotuj wartość **klucza** **key1**. (Wybranie ikony po prawej stronie klucza spowoduje skopiowanie wartości do schowka).

    ![Klucze dostępu konta magazynu](./media/terraform-k8s-cluster-appgw-with-tf-aks/storage-account-access-key.png)

1. W usłudze Cloud Shell utwórz kontener na koncie usługi Azure Storage (zamień symbole zastępcze &lt;YourAzureStorageAccountName> i &lt;YourAzureStorageAccountAccessKey> na wartości odpowiednie dla Twojego konta usługi Azure Storage).

    ```azurecli
    az storage container create -n tfstate --account-name <YourAzureStorageAccountName> --account-key <YourAzureStorageAccountKey>
    ```

## <a name="create-the-kubernetes-cluster"></a>Tworzenie klastra Kubernetes
W tej sekcji dowiesz się, jak za pomocą polecenia `terraform init` utworzyć zasoby zdefiniowane w plikach konfiguracji, które zostały utworzone w poprzednich sekcjach.

1. W usłudze Cloud Shell zainicjuj narzędzie Terraform (zamień symbole zastępcze &lt;YourAzureStorageAccountName> i &lt;YourAzureStorageAccountAccessKey> na wartości odpowiednie dla Twojego konta usługi Azure Storage).

    ```bash
    terraform init -backend-config="storage_account_name=<YourAzureStorageAccountName>" -backend-config="container_name=tfstate" -backend-config="access_key=<YourStorageAccountAccessKey>" -backend-config="key=codelab.microsoft.tfstate" 
    ```
  
    `terraform init` polecenie wyświetla powodzenie inicjowania wtyczki zaplecza i dostawcy:

    ![Przykładowe wyniki wykonania polecenia „terraform init”](./media/terraform-k8s-cluster-appgw-with-tf-aks/terraform-init-complete.png)

1. W Cloud Shell Utwórz plik o nazwie `main.tf`:

    ```bash
    code terraform.tfvars
    ```

1. Wklej następujące wcześniej utworzone zmienne do edytora:

    ```hcl
    resource_group_name = <Name of the Resource Group already created>

    location = <Location of the Resource Group>
      
    aks_service_principal_app_id = <Service Principal AppId>
      
    aks_service_principal_client_secret = <Service Principal Client Secret>
      
    aks_service_principal_object_id = <Service Principal Object Id>
        
    ```

1. Zapisz plik ( **&lt;Ctrl > S**) i wyjdź z edytora ( **&lt;Ctrl > Q**).

1. Uruchom polecenie `terraform plan`, aby utworzyć plan narzędzia Terraform definiujący elementy infrastruktury. 

    ```bash
    terraform plan -out out.plan
    ```

    Polecenie `terraform plan` wyświetla zasoby, które są tworzone podczas uruchamiania polecenia `terraform apply`:

    ![Przykładowe wyniki wykonania polecenia „terraform plan”](./media/terraform-k8s-cluster-appgw-with-tf-aks/terraform-plan-complete.png)

1. Uruchom polecenie `terraform apply`, aby zastosować plan w celu utworzenia klastra Kubernetes. Proces tworzenia klastra Kubernetes może potrwać kilka minut, co spowodowało przekroczenie limitu czasu sesji Cloud Shell. Jeśli sesja Cloud Shell przekroczy limit czasu, możesz wykonać czynności opisane w sekcji "Odzyskaj po upływie limitu czasu Cloud Shell", aby umożliwić ukończenie tego samouczka.

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

## <a name="install-azure-ad-pod-identity"></a>Zainstaluj tożsamość usługi Azure AD pod

Azure Active Directory pod tożsamością zapewnia dostęp oparty na tokenach do [Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview).

[Usługa Azure AD pod tożsamością](https://github.com/Azure/aad-pod-identity) dodaje następujące składniki do klastra Kubernetes:

  - Kubernetes [CRDs](https://kubernetes.io/docs/tasks/access-kubernetes-api/custom-resources/custom-resource-definitions/): `AzureIdentity`, `AzureAssignedIdentity`, `AzureIdentityBinding`
  - Składnik [zarządzanego kontrolera tożsamości (MIC)](https://github.com/Azure/aad-pod-identity#managed-identity-controllermic)
  - Składnik [tożsamości zarządzanej przez węzeł (NMI)](https://github.com/Azure/aad-pod-identity#node-managed-identitynmi)

Jeśli funkcja RBAC jest **włączona**, uruchom następujące polecenie, aby zainstalować tożsamość usługi Azure AD pod kątem klastra:

    ```bash
    kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment-rbac.yaml
    ```

Jeśli RBAC jest **wyłączone**, uruchom następujące polecenie, aby zainstalować tożsamość usługi Azure AD pod klastrem:

    ```bash
    kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment.yaml
    ```

## <a name="install-helm"></a>Zainstaluj Helm

Kod w tej sekcji używa Menedżera pakietów [Helm](/azure/aks/kubernetes-helm) -Kubernetes — aby zainstalować pakiet `application-gateway-kubernetes-ingress`:

1. Jeśli funkcja RBAC jest **włączona**, uruchom następujący zestaw poleceń, aby zainstalować i skonfigurować Helm:

    ```bash
    kubectl create serviceaccount --namespace kube-system tiller-sa
    kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller-sa
    helm init --tiller-namespace kube-system --service-account tiller-sa
    ```

1. Jeśli RBAC jest **wyłączone**, uruchom następujące polecenie, aby zainstalować i skonfigurować Helm:

    ```bash
    helm init
    ```

1. Dodaj repozytorium AGIC Helm:

    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
    ```

## <a name="install-ingress-controller-helm-chart"></a>Instalowanie wykresu Helm kontrolera danych wejściowych

1. Pobierz `helm-config.yaml`, aby skonfigurować AGIC:

    ```bash
    wget https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/sample-helm-config.yaml -O helm-config.yaml
    ```

1. Edytuj `helm-config.yaml` i wprowadź odpowiednie wartości w sekcjach `appgw` i `armAuth`.

    ```bash
    nano helm-config.yaml
    ```

    Wartości są opisane w następujący sposób:

    - `verbosityLevel`: ustawia poziom szczegółowości infrastruktury rejestrowania AGIC. Zobacz [poziomy rejestrowania](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/463a87213bbc3106af6fce0f4023477216d2ad78/docs/troubleshooting.md#logging-levels) dla możliwych wartości.
    - `appgw.subscriptionId`: Identyfikator subskrypcji platformy Azure dla bramy App Gateway. Przykład: `a123b234-a3b4-557d-b2df-a0bc12de1234`
    - `appgw.resourceGroup`: Nazwa grupy zasobów platformy Azure, w której została utworzona Brama aplikacji. 
    - `appgw.name`: Nazwa Application Gateway. Przykład: `applicationgateway1`.
    - `appgw.shared`: Ta flaga logiczna powinna mieć wartość domyślną `false`. Wartość `true` powinna być wymagana przez [bramę aplikacji udostępnionej](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/072626cb4e37f7b7a1b0c4578c38d1eadc3e8701/docs/setup/install-existing.md#multi-cluster--shared-app-gateway).
    - `kubernetes.watchNamespace`: Określ przestrzeń nazw, która AGIC powinna być obserwowana. Przestrzeń nazw może być pojedynczą wartością ciągu lub rozdzielaną przecinkami listą przestrzeni nazw.
    - `armAuth.type`: wartość `aadPodIdentity` lub `servicePrincipal`.
    - `armAuth.identityResourceID`: Identyfikator zasobu tożsamości zarządzanej.
    - `armAuth.identityClientId`: identyfikator klienta tożsamości.
    - `armAuth.secretJSON`: jest to konieczne tylko wtedy, gdy wybrano typ wpisu tajnego jednostki usługi (gdy `armAuth.type` ustawiono na `servicePrincipal`).

    Kluczowe uwagi:
    - Wartość `identityResourceID` jest tworzona w skrypcie Terraform i można ją znaleźć, uruchamiając: `echo "$(terraform output identity_client_id)"`.
    - Wartość `identityClientID` jest tworzona w skrypcie Terraform i można ją znaleźć, uruchamiając: `echo "$(terraform output identity_resource_id)"`.
    - Wartość `<resource-group>` jest grupą zasobów bramy aplikacji.
    - Wartość `<identity-name>` to nazwa utworzonej tożsamości.
    - Wszystkie tożsamości dla danej subskrypcji można wyświetlić przy użyciu: `az identity list`.

1. Zainstaluj pakiet Application Gateway transferu danych przychodzących:

    ```bash
    helm install -f helm-config.yaml application-gateway-kubernetes-ingress/ingress-azure
    ```

### <a name="install-a-sample-app"></a>Instalowanie przykładowej aplikacji

Po zainstalowaniu bramy App Gateway, AKS i AGIC można zainstalować przykładową aplikację za pośrednictwem [Azure Cloud Shell](https://shell.azure.com/):

1. Użyj polecenia zwinięcie, aby pobrać plik YAML:

    ```bash
    curl https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml -o aspnetapp.yaml
    ```

2. Zastosuj plik YAML:

    ```bash
    kubectl apply -f apsnetapp.yaml
    ```

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób tworzenia klastra Kubernetes przy użyciu narzędzia Terraform i usługi AKS. Poniżej przedstawiono kilka dodatkowych zasobów zawierających więcej informacji na temat narzędzia Terraform na platformie Azure.
 
 > [!div class="nextstepaction"] 
  > [Application Gateway kontroler transferu danych przychodzących](https://azure.github.io/application-gateway-kubernetes-ingress/)