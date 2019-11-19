---
title: Samouczek — Tworzenie klastra Kubernetes za pomocą usługi Azure Kubernetes Service (AKS) za pomocą Terraform
description: Samouczek pokazujący sposób tworzenia klastra Kubernetes za pomocą usługi Azure Kubernetes Service i narzędzia Terraform
ms.topic: tutorial
ms.date: 11/07/2019
ms.openlocfilehash: 792c075cfb40eb4904a30b63e9902a59ceda9bc1
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/18/2019
ms.locfileid: "74159300"
---
# <a name="tutorial-create-a-kubernetes-cluster-with-azure-kubernetes-service-using-terraform"></a>Samouczek: Tworzenie klastra Kubernetes za pomocą usługi Azure Kubernetes Service przy użyciu Terraform

Usługa [Azure Kubernetes Service (AKS)](/azure/aks/) zarządza hostowanym środowiskiem Kubernetes. AKS umożliwia wdrażanie aplikacji kontenerowych i zarządzanie nimi bez wiedzy o aranżacji kontenerów. AKS umożliwia również wykonywanie wielu typowych operacji konserwacyjnych bez przełączania aplikacji do trybu offline. Te operacje obejmują Inicjowanie obsługi, uaktualnianie i skalowanie zasobów na żądanie.

W tym samouczku dowiesz się, jak wykonywać następujące zadania:

> [!div class="checklist"]
> * Definiowanie klastra Kubernetes za pomocą języka HCL (HashiCorp Language)
> * Tworzenie klastra Kubernetes za pomocą narzędzia Terraform i usługi AKS
> * Testowanie dostępności klastra Kubernetes za pomocą narzędzia kubectl

## <a name="prerequisites"></a>Wymagania wstępne

- **Subskrypcja platformy Azure**: jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- **Skonfiguruj narzędzie Terraform**: postępuj zgodnie ze wskazówkami w artykule [Terraform and configure access to Azure](/azure/virtual-machines/linux/terraform-install-configure) (Terraform i konfigurowanie dostępu do platformy Azure).

- **Jednostka usługi platformy Azure**: postępuj zgodnie z instrukcjami w sekcji dotyczącej **tworzenia jednostki usługi** artykułu [Tworzenie jednostki usługi platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest). Zapisz wartości pozycji appId, displayName, password i tenant.

## <a name="create-the-directory-structure"></a>Tworzenie struktury katalogów

Pierwszym krokiem jest utworzenie katalogu, w którym będą przechowywane pliki konfiguracji narzędzia Terraform dla tego ćwiczenia.

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).

1. Otwórz usługę [Azure Cloud Shell](/azure/cloud-shell/overview). Jeśli środowisko nie zostało wybrane wcześniej, wybierz pozycję **Bash** jako swoje środowisko.

    ![Wiersz polecenia usługi Cloud Shell](./media/terraform-create-k8s-cluster-with-tf-and-aks/azure-portal-cloud-shell-button-min.png)

1. Zmień katalog na `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Utwórz katalog o nazwie `terraform-aks-k8s`.

    ```bash
    mkdir terraform-aks-k8s
    ```

1. Zmień katalog na nowy:

    ```bash
    cd terraform-aks-k8s
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
        version = "~>1.5"
    }

    terraform {
        backend "azurerm" {}
    }
    ```

1. Zapisz plik ( **&lt;Ctrl > S**) i wyjdź z edytora ( **&lt;Ctrl > Q**).

## <a name="define-a-kubernetes-cluster"></a>Definiowanie klastra Kubernetes

Utwórz plik konfiguracji narzędzia Terraform zawierający deklarację zasobów dla klastra Kubernetes.

1. W usłudze Cloud Shell utwórz plik o nazwie `k8s.tf`.

    ```bash
    code k8s.tf
    ```

1. Wklej następujący kod do edytora:

    ```hcl
    resource "azurerm_resource_group" "k8s" {
        name     = var.resource_group_name
        location = var.location
    }
    
    resource "random_id" "log_analytics_workspace_name_suffix" {
        byte_length = 8
    }

    resource "azurerm_log_analytics_workspace" "test" {
        # The WorkSpace name has to be unique across the whole of azure, not just the current subscription/tenant.
        name                = "${var.log_analytics_workspace_name}-${random_id.log_analytics_workspace_name_suffix.dec}"
        location            = var.log_analytics_workspace_location
        resource_group_name = azurerm_resource_group.k8s.name
        sku                 = var.log_analytics_workspace_sku
    }

    resource "azurerm_log_analytics_solution" "test" {
        solution_name         = "ContainerInsights"
        location              = azurerm_log_analytics_workspace.test.location
        resource_group_name   = azurerm_resource_group.k8s.name
        workspace_resource_id = azurerm_log_analytics_workspace.test.id
        workspace_name        = azurerm_log_analytics_workspace.test.name

        plan {
            publisher = "Microsoft"
            product   = "OMSGallery/ContainerInsights"
        }
    }

    resource "azurerm_kubernetes_cluster" "k8s" {
        name                = var.cluster_name
        location            = azurerm_resource_group.k8s.location
        resource_group_name = azurerm_resource_group.k8s.name
        dns_prefix          = var.dns_prefix

        linux_profile {
            admin_username = "ubuntu"

            ssh_key {
                key_data = file(var.ssh_public_key)
            }
        }

        agent_pool_profile {
            name            = "agentpool"
            count           = var.agent_count
            vm_size         = "Standard_DS1_v2"
            os_type         = "Linux"
            os_disk_size_gb = 30
        }

        service_principal {
            client_id     = var.client_id
            client_secret = var.client_secret
        }

        addon_profile {
            oms_agent {
            enabled                    = true
            log_analytics_workspace_id = azurerm_log_analytics_workspace.test.id
            }
        }

        tags = {
            Environment = "Development"
        }
    }
    ```

    Poprzedni kod ustawia nazwę klastra, lokalizację i nazwę grupy zasobów. Zostanie również ustawiony prefiks dla w pełni kwalifikowanej nazwy domeny (FQDN). Nazwa FQDN jest używana w celu uzyskania dostępu do klastra.

    Rekord `linux_profile` umożliwia skonfigurowanie ustawień umożliwiających zalogowanie się w węzłach procesu roboczego przy użyciu protokołu SSH.

    W przypadku usługi AKS płacisz tylko za węzły procesu roboczego. Rekord `agent_pool_profile` konfiguruje szczegóły dla tych węzłów procesu roboczego. `agent_pool_profile record` obejmuje liczbę węzłów procesu roboczego do utworzenia i typ węzłów procesu roboczego. Jeśli w przyszłości konieczne jest skalowanie klastra w górę lub w dół, należy zmodyfikować wartość `count` w tym rekordzie.

1. Zapisz plik ( **&lt;Ctrl > S**) i wyjdź z edytora ( **&lt;Ctrl > Q**).

## <a name="declare-the-variables"></a>Deklarowanie zmiennych

1. W usłudze Cloud Shell utwórz plik o nazwie `variables.tf`.

    ```bash
    code variables.tf
    ```

1. Wklej następujący kod do edytora:

    ```hcl
    variable "client_id" {}
    variable "client_secret" {}

    variable "agent_count" {
        default = 3
    }

    variable "ssh_public_key" {
        default = "~/.ssh/id_rsa.pub"
    }

    variable "dns_prefix" {
        default = "k8stest"
    }

    variable cluster_name {
        default = "k8stest"
    }

    variable resource_group_name {
        default = "azure-k8stest"
    }

    variable location {
        default = "Central US"
    }

    variable log_analytics_workspace_name {
        default = "testLogAnalyticsWorkspaceName"
    }

    # refer https://azure.microsoft.com/global-infrastructure/services/?products=monitor for log analytics available regions
    variable log_analytics_workspace_location {
        default = "eastus"
    }

   # refer https://azure.microsoft.com/pricing/details/monitor/ for log analytics pricing 
   variable log_analytics_workspace_sku {
        default = "PerGB2018"
   }
    ```

1. Zapisz plik ( **&lt;Ctrl > S**) i wyjdź z edytora ( **&lt;Ctrl > Q**).

## <a name="create-a-terraform-output-file"></a>Tworzenie pliku wyjściowego narzędzia Terraform

[Dane wyjściowe narzędzia Terraform](https://www.terraform.io/docs/configuration/outputs.html) umożliwiają definiowanie wartości, które będą wyróżnione dla użytkownika, gdy narzędzie Terraform zastosuje plan, i do których będzie można wysyłać zapytania za pomocą polecenia `terraform output`. W tej sekcji utworzysz plik wyjściowy umożliwiający uzyskanie dostępu do klastra za pomocą narzędzia [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/).

1. W usłudze Cloud Shell utwórz plik o nazwie `output.tf`.

    ```bash
    code output.tf
    ```

1. Wklej następujący kod do edytora:

    ```hcl
    output "client_key" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.client_key
    }

    output "client_certificate" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.client_certificate
    }

    output "cluster_ca_certificate" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.cluster_ca_certificate
    }

    output "cluster_username" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.username
    }

    output "cluster_password" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.password
    }

    output "kube_config" {
        value = azurerm_kubernetes_cluster.k8s.kube_config_raw
    }

    output "host" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.host
    }
    ```

1. Zapisz plik ( **&lt;Ctrl > S**) i wyjdź z edytora ( **&lt;Ctrl > Q**).

## <a name="set-up-azure-storage-to-store-terraform-state"></a>Konfigurowanie usługi Azure Storage na potrzeby przechowywania stanu narzędzia Terraform

Narzędzie Terraform śledzi stan lokalnie za pośrednictwem pliku `terraform.tfstate`. Ten wzorzec sprawdza się dobrze w środowisku jednoosobowym. W środowisku z obsługą kilku osób [usługa Azure Storage](/azure/storage/) jest używana do śledzenia stanu.

W tej sekcji przedstawiono sposób wykonywania następujących zadań:
- Pobieranie informacji o koncie magazynu (nazwa konta i klucz konta)
- Utwórz kontener magazynu, w którym będą przechowywane informacje o stanie Terraform.

1. W witrynie Azure Portal wybierz pozycję **Wszystkie usługi** z menu po lewej stronie.

1. Wybierz pozycję **Konta usługi Storage**.

1. Na karcie **Konta usługi Storage** wybierz nazwę konta magazynu, na którym ma być przechowywany stan narzędzia Terraform. Na przykład możesz skorzystać z konta magazynu utworzonego przy pierwszym otwarciu usługi Cloud Shell.  Nazwa konta magazynu utworzonego przez usługę Cloud Shell zwykle zaczyna się od ciągu `cs`, po którym następuje losowy ciąg liter i cyfr. Zanotuj wybrane konto magazynu. Ta wartość jest wymagana później.

1. Na karcie konta magazynu wybierz pozycję **Klucze dostępu**.

    ![Menu konta magazynu](./media/terraform-create-k8s-cluster-with-tf-and-aks/storage-account.png)

1. Zanotuj wartość **klucza** **key1**. (Wybranie ikony po prawej stronie klucza spowoduje skopiowanie wartości do schowka).

    ![Klucze dostępu konta magazynu](./media/terraform-create-k8s-cluster-with-tf-and-aks/storage-account-access-key.png)

1. W Cloud Shell Utwórz kontener na koncie usługi Azure Storage. Zastąp symbole zastępcze odpowiednimi wartościami dla danego środowiska.

    ```azurecli
    az storage container create -n tfstate --account-name <YourAzureStorageAccountName> --account-key <YourAzureStorageAccountKey>
    ```

## <a name="create-the-kubernetes-cluster"></a>Tworzenie klastra Kubernetes

W tej sekcji dowiesz się, jak za pomocą polecenia `terraform init` utworzyć zasoby zdefiniowane w plikach konfiguracji, które zostały utworzone w poprzednich sekcjach.

1. W Cloud Shell zainicjuj Terraform. Zastąp symbole zastępcze odpowiednimi wartościami dla danego środowiska.

    ```bash
    terraform init -backend-config="storage_account_name=<YourAzureStorageAccountName>" -backend-config="container_name=tfstate" -backend-config="access_key=<YourStorageAccountAccessKey>" -backend-config="key=codelab.microsoft.tfstate" 
    ```
    
    `terraform init` polecenie wyświetla powodzenie inicjowania wtyczki zaplecza i dostawcy:

    ![Przykładowe wyniki wykonania polecenia „terraform init”](./media/terraform-create-k8s-cluster-with-tf-and-aks/terraform-init-complete.png)

1. Wyeksportuj poświadczenia jednostki usługi. Zastąp symbole zastępcze odpowiednimi wartościami z nazwy głównej usługi.

    ```bash
    export TF_VAR_client_id=<service-principal-appid>
    export TF_VAR_client_secret=<service-principal-password>
    ```

1. Uruchom polecenie `terraform plan`, aby utworzyć plan narzędzia Terraform definiujący elementy infrastruktury. 

    ```bash
    terraform plan -out out.plan
    ```

    Polecenie `terraform plan` spowoduje wyświetlenie zasobów, które zostaną utworzone po uruchomieniu polecenia `terraform apply`:

    ![Przykładowe wyniki wykonania polecenia „terraform plan”](./media/terraform-create-k8s-cluster-with-tf-and-aks/terraform-plan-complete.png)

1. Uruchom polecenie `terraform apply`, aby zastosować plan w celu utworzenia klastra Kubernetes. Proces tworzenia klastra Kubernetes może potrwać kilka minut, co spowodowało przekroczenie limitu czasu sesji Cloud Shell. Jeśli sesja Cloud Shell przekroczy limit czasu, możesz wykonać czynności opisane w sekcji "Odzyskaj po upływie limitu czasu Cloud Shell", aby umożliwić ukończenie tego samouczka.

    ```bash
    terraform apply out.plan
    ```

    Polecenie `terraform apply` spowoduje wyświetlenie wyników tworzenia zasobów zdefiniowanych w plikach konfiguracji:

    ![Przykładowe wyniki wykonania polecenia „terraform apply”](./media/terraform-create-k8s-cluster-with-tf-and-aks/terraform-apply-complete.png)

1. W Azure Portal wybierz pozycję **wszystkie zasoby** w menu po lewej stronie, aby wyświetlić zasoby utworzone dla nowego klastra Kubernetes.

    ![Wszystkie zasoby w Azure Portal](./media/terraform-create-k8s-cluster-with-tf-and-aks/k8s-resources-created.png)

## <a name="recover-from-a-cloud-shell-timeout"></a>Odzyskiwanie sprawności po przekroczeniu limitu czasu w usłudze Cloud Shell

Jeśli sesja Cloud Shell przekroczy limit czasu, możesz wykonać następujące czynności, aby odzyskać:

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

    ![Narzędzie kubectl umożliwia sprawdzenie kondycji klastra Kubernetes](./media/terraform-create-k8s-cluster-with-tf-and-aks/kubectl-get-nodes.png)

## <a name="monitor-health-and-logs"></a>Monitorowanie kondycji i dzienników

Podczas tworzenia klastra AKS włączono monitorowanie, aby przechwycić metryki kondycji dla zasobników i węzłów klastra. Te metryki kondycji są dostępne w witrynie Azure Portal. Aby uzyskać więcej informacji dotyczących monitorowania kondycji kontenera, zobacz [Monitorowanie kondycji usługi Azure Kubernetes Service](/azure/azure-monitor/insights/container-insights-overview).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"] 
> [Dowiedz się więcej o korzystaniu z Terraform na platformie Azure](/azure/terraform)