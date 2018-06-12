---
title: Tworzenie klastra Kubernetes z usługi Kubernetes Azure (AKS) i Terraform
description: Samouczek pokazujący sposób tworzenia klastra Kubernetes z usługą Kubernetes Azure i Terraform
keywords: terraform, devops, maszyny wirtualnej, azure, kubernetes
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.date: 06/11/2018
ms.topic: article
ms.openlocfilehash: bd00a0cc8446802a03570edd58949a46c0769101
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35304202"
---
# <a name="create-a-kubernetes-cluster-with-azure-kubernetes-service-and-terraform"></a>Tworzenie klastra Kubernetes z usługą Kubernetes Azure i Terraform
[Usługa Kubernetes Azure (AKS)](/azure/aks/) zarządza środowisku hostowanej Kubernetes, ułatwiając szybkie i łatwe wdrażanie i zarządzanie nimi konteneryzowanych aplikacji bez wiedzy aranżacji kontenera. Eliminuje to również uciążliwości związane z bieżącą obsługą i konserwacją dzięki aprowizowaniu, aktualizowaniu i skalowaniu zasobów na żądanie bez przełączania aplikacji do trybu offline.

W tym samouczku opisano sposób wykonywania następujących zadań tworzenia [Kubernetes](https://www.redhat.com/en/topics/containers/what-is-kubernetes) klastra przy użyciu [Terraform](http://terraform.io) i AKS:

> [!div class="checklist"]
> * Umożliwia definiowanie klastra Kubernetes HCL (HashiCorp Language)
> * Umożliwia utworzenie klastra Kubernetes Terraform i AKS
> * Użyj narzędzia kubectl do testowania dostępności klastra Kubernetes

## <a name="prerequisites"></a>Wymagania wstępne

- **Subskrypcja platformy Azure**: jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- **Skonfiguruj Terraform**: postępuj zgodnie z instrukcjami w artykule [Terraform i Konfiguracja dostępu do platformy Azure](/azure/virtual-machines/linux/terraform-install-configure)

- **Nazwy głównej usługi Azure**: postępuj zgodnie z instrukcjami w sekcji **Tworzenie nazwy głównej usługi** w artykule, [zapisać nazwy głównej usługi platformy Azure z Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest#create-the-service-principal). Zanotuj wartości appId, nazwa wyświetlana, hasło i dzierżawcy.

## <a name="create-the-directory-structure"></a>Utwórz strukturę katalogów
Pierwszym krokiem jest można utworzyć katalogu, który zawiera pliki konfiguracji Terraform wykonywania.

1. Przejdź do [portalu Azure](http://portal.azure.com).

1. Otwórz [powłoki chmury Azure](/azure/cloud-shell/overview). Jeśli wcześniej nie wybrano środowisko, wybierz **Bash** jako środowiska.

    ![Chmura wierszu polecenia powłoki](./media/terraform-create-k8s-cluster-with-tf-and-aks/azure-portal-cloud-shell-button-min.png)

1. Przejdź do `clouddrive` katalogu.

    ```bash
    cd clouddrive
    ```

1. Utwórz katalog o nazwie `terraform-aks-k8s`.

    ```bash
    mkdir terraform-aks-k8s
    ```

1. Przejdź do nowego katalogu:

    ```bash
    cd terraform-aks-k8s
    ```

## <a name="declare-the-azure-provider"></a>Deklarowanie dostawca usługi Azure
Utwórz plik konfiguracji Terraform, który deklaruje dostawca usługi Azure.

1. W chmurze powłoki, Utwórz plik o nazwie `main.tf`.

    ```bash
    vi main.tf
    ```

1. Wprowadź tryb wstawiania, wybierając I klucza.

1. Wklej następujący kod do edytora:

    ```JSON
    provider "azurerm" {
        version = "=1.5.0"
    }

    terraform {
        backend "azurerm" {}
    }

    ```

1. Zamknij tryb wstawiania, wybierając **Esc** klucza.

1. Zapisz plik i zamknij Edytor vi, wprowadzając następujące polecenie:

    ```bash
    :wq
    ```

## <a name="define-a-kubernetes-cluster"></a>Definiowanie klastra Kubernetes
Utwórz plik konfiguracji Terraform, który deklaruje zasobów klastra Kubernetes.

1. W chmurze powłoki, Utwórz plik o nazwie `k8s.tf`.

    ```bash
    vi k8s.tf
    ```

1. Wprowadź tryb wstawiania, wybierając I klucza.

1. Wklej następujący kod do edytora:

    ```JSON
    resource "azurerm_resource_group" "k8s" {
        name     = "${var.resource_group_name}"
        location = "${var.location}"
    }

    resource "azurerm_kubernetes_cluster" "k8s" {
        name                = "${var.cluster_name}"
        location            = "${azurerm_resource_group.k8s.location}"
        resource_group_name = "${azurerm_resource_group.k8s.name}"
        dns_prefix          = "${var.dns_prefix}"

        linux_profile {
            admin_username = "ubuntu"

            ssh_key {
            key_data = "${file("${var.ssh_public_key}")}"
            }
        }

        agent_pool_profile {
            name            = "default"
            count           = "${var.agent_count}"
            vm_size         = "Standard_D2"
            os_type         = "Linux"
            os_disk_size_gb = 30
        }

        service_principal {
            client_id     = "${var.client_id}"
            client_secret = "${var.client_secret}"
        }

        tags {
            Environment = "Development"
        }
    }
    ```

    Poprzedni kod ustawia nazwę klastra, lokalizacji i resource_group_name. Ponadto dns_prefix - stanowiącej część w pełni kwalifikowanej nazwy domeny (FQDN) używane do dostępu do klastra - ma wartość.

    **Linux_profile** rekordu umożliwia konfigurowanie ustawień, które umożliwiają logowanie do węzłów procesu roboczego przy użyciu protokołu SSH.

    Z AKS zapłacisz tylko dla węzłów procesu roboczego. **Agent_pool_profile** rekordu konfiguruje szczegółów dla tych węzłów procesu roboczego. **Rekordu agent_pool_profile** zawiera liczbę węzłów procesu roboczego do utworzenia i rodzaj węzłów procesu roboczego. Skalowanie w górę lub w przyszłości skali klastra należy zmodyfikować **liczba** wartości w tym rekordzie.

1. Zamknij tryb wstawiania, wybierając **Esc** klucza.

1. Zapisz plik i zamknij Edytor vi, wprowadzając następujące polecenie:

    ```bash
    :wq
    ```

## <a name="declare-the-variables"></a>Deklarowanie zmiennych

1. W chmurze powłoki, Utwórz plik o nazwie `variables.tf`.

    ```bash
    vi variables.tf
    ```

1. Wprowadź tryb wstawiania, wybierając I klucza.

1. Wklej następujący kod do edytora:

    ```JSON
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
    ```

1. Zamknij tryb wstawiania, wybierając **Esc** klucza.

1. Zapisz plik i zamknij Edytor vi, wprowadzając następujące polecenie:

    ```bash
    :wq
    ```

## <a name="create-a-terraform-output-file"></a>Utwórz plik wyjściowy Terraform
[Generuje Terraform](https://www.terraform.io/docs/configuration/outputs.html) zezwala na określanie wartości, które zostaną wyróżnione dla użytkownika, gdy Terraform stosuje planu i można wyświetlić przy użyciu `terraform output` polecenia. W tej sekcji utworzysz plik wyjściowy, który umożliwia dostęp do klastra z [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/).

1. W chmurze powłoki, Utwórz plik o nazwie `output.tf`.

    ```bash
    vi output.tf
    ```

1. Wprowadź tryb wstawiania, wybierając I klucza.

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

1. Zamknij tryb wstawiania, wybierając **Esc** klucza.

1. Zapisz plik i zamknij Edytor vi, wprowadzając następujące polecenie:

    ```bash
    :wq
    ```

## <a name="set-up-azure-storage-to-store-terraform-state"></a>Konfigurowanie magazynu Azure do przechowywania stanu Terraform
Terraform śledzi stan lokalnie za pomocą `terraform.tfstate` pliku. Ten wzorzec działa dobrze w środowisku pojedynczej osoby. Jednak w środowisku bardziej praktyczne wieloosobowej zachodzi potrzeba śledzenia stanu na serwerze, przy użyciu [magazynu Azure](/azure/storage/). W tej sekcji możesz pobrać niezbędnych informacji o koncie magazynu (nazwa konta i klucz konta) i utworzyć kontenera magazynu, w którym będą przechowywane informacje o stanie Terraform.

1. W portalu Azure wybierz **wszystkie usługi** w lewym menu.

1. Wybierz **kont magazynu**.

1. Na **kont magazynu** , a następnie wybierz nazwę konta magazynu, do którego Terraform jest do przechowywania stanu. Na przykład można użyć na koncie magazynu utworzonym podczas otwierania powłoki chmury po raz pierwszy.  Nazwa konta magazynu utworzone przez powłokę chmury zazwyczaj rozpoczyna się od `cs` następuje ciąg losowych liter i cyfr. **Pamiętać nazwy konta magazynu, które można wybrać, ponieważ jest on potrzebny później.**

1. Na karcie Konto magazynu, wybierz **klucze dostępu**.

    ![Menu konta magazynu](./media/terraform-create-k8s-cluster-with-tf-and-aks/storage-account.png)

1. Zwróć uwagę na **klucz1** **klucza** wartość. (Wybierając ikonę z prawej strony klucz skopiowanie wartości do Schowka.)

    ![Klucze dostępu do konta magazynu](./media/terraform-create-k8s-cluster-with-tf-and-aks/storage-account-access-key.png)

1. W powłoce chmury, utworzenia kontenera na koncie magazynu Azure (Zastąp &lt;YourAzureStorageAccountName > i &lt;YourAzureStorageAccountAccessKey > symboli zastępczych z odpowiednimi wartościami dla konta magazynu Azure ).

    ```bash
    az storage container create -n tfstate --account-name <YourAzureStorageAccountName> --account-key <YourAzureStorageAccountKey>
    ```

## <a name="create-the-kubernetes-cluster"></a>Tworzenie klastra Kubernetes
W tej sekcji, zobacz temat jak korzystać `terraform init` polecenie, aby utworzyć zasoby zdefiniowane pliki konfiguracji utworzone w poprzednich sekcjach.

1. W powłoce chmury zainicjować Terraform (Zastąp &lt;YourAzureStorageAccountName > i &lt;YourAzureStorageAccountAccessKey > symboli zastępczych z odpowiednimi wartościami dla konta magazynu Azure).

    ```bash
    terraform init -backend-config="storage_account_name=<YourAzureStorageAccountName>" -backend-config="container_name=tfstate" -backend-config="access_key=<YourStorageAccountAccessKey>" -backend-config="key=codelab.microsoft.tfstate" 
    ```
    
    `terraform init` Polecenie wyświetla Powodzenie inicjowanie wtyczki wewnętrznej bazy danych i dostawcy:

    ![Przykładowe wyniki "terraform init"](./media/terraform-create-k8s-cluster-with-tf-and-aks/terraform-init-complete.png)

1. Uruchom `terraform plan` polecenie, aby utworzyć plan Terraform, który definiuje elementy infrastruktury. Polecenie będą wymagane dwie wartości: **var.client_id** i **var.client_secret**. Aby uzyskać **var.client_id** zmiennej, wprowadź **appId** wartość skojarzoną z Twojej nazwy głównej usługi. Aby uzyskać **var.client_secret** zmiennej, wprowadź **hasło** wartość skojarzoną z Twojej nazwy głównej usługi.

    ```bash
    terraform plan -out out.plan
    ```

    `terraform plan` Polecenie wyświetla zasoby, które zostaną utworzone po uruchomieniu `terraform apply` polecenia:

    ![Przykładowe wyniki "terraform planu"](./media/terraform-create-k8s-cluster-with-tf-and-aks/terraform-plan-complete.png)

1. Uruchom `terraform apply` polecenie, aby zastosować plan do utworzenia klastra Kubernetes. Proces tworzenia klastra Kubernetes może potrwać kilka minut, co przekroczeniem limitu czasu sesji powłoki chmury. Jeśli upłynie limit czasu sesji powłoki w chmurze, można wykonać czynności opisane w sekcji ["Odzyskanie limit powłoki chmury"](#recover-from-a-dloud-shell-timeout) pozwala na ukończenie tego samouczka.

    ```bash
    terraform apply out.plan
    ```

    `terraform apply` Polecenie wyświetla wyniki tworzenia zasoby zdefiniowane w plikach konfiguracji:

    ![Przykładowe wyniki "Zastosuj terraform"](./media/terraform-create-k8s-cluster-with-tf-and-aks/terraform-apply-complete.png)

1. W portalu Azure wybierz **wszystkie usługi** w menu po lewej stronie, aby wyświetlić zasoby utworzone dla nowego klastra Kubernetese.

    ![Chmura wierszu polecenia powłoki](./media/terraform-create-k8s-cluster-with-tf-and-aks/k8s-resources-created.png)

## <a name="recover-from-a-cloud-shell-timeout"></a>Odzyskiwanie z powłoki chmury przekroczenie limitu czasu
Jeśli upłynie limit czasu sesji powłoki w chmurze, można wykonać następujące kroki, aby odzyskać:

1. Rozpocznij sesję powłoki chmury.

1. Przejdź do katalogu zawierającego Terraform plików konfiguracji.

    ```bash
    cd /clouddrive/terraform-aks-k8s
    ```

1. Uruchom następujące polecenie:

    ```bash
    export KUBECONFIG=./azurek8s
    ```
    
## <a name="test-the-kubernetes-cluster"></a>Przetestowanie klastra Kubernetes
Narzędzia Kubernetes może służyć do Sprawdź nowo utworzonego klastra.

1. Uzyskiwanie konfiguracji Kubernetes ze stanu Terraform i zapisz go w pliku tego kubectl może odczytywać.

    ```bash
    echo "$(terraform output kube_config)" > ./azurek8s
    ```

1. Ustaw zmienną środowiskową kubectl przejmuje poprawne konfiguracji.

    ```bash
    export KUBECONFIG=./azurek8s
    ```

1. Sprawdź kondycję klastra.

    ```bash
    kubectl get nodes
    ```

    Powinny pojawić się szczegóły węzłów procesu roboczego, a ich powinny mieć stan **gotowe**, jak pokazano na poniższej ilustracji:

    ![Narzędzie kubectl pozwala sprawdzić kondycję Kubernetes klastra](./media/terraform-create-k8s-cluster-with-tf-and-aks/kubectl-get-nodes.png)

## <a name="next-steps"></a>Kolejne kroki
W tym artykule przedstawiono sposób umożliwiają utworzenie klastra Kubernetes Terraform i AKS. Poniżej przedstawiono dodatkowe zasoby ułatwiające Dowiedz się więcej o Terraform na platformie Azure: 

 [Koncentrator Terraform w domenie Microsoft.com](https://docs.microsoft.com/azure/terraform/)  
 [Dokumentacji dostawcy Terraform platformy Azure](http://aka.ms/terraform)  
 [Źródłowy dostawca Terraform Azure](http://aka.ms/tfgit)  
 [Moduły Terraform Azure](http://aka.ms/tfmodules)