---
title: Używanie tożsamości usługi AAD w usłudze sieci Web
titleSuffix: Azure Machine Learning
description: Użyj tożsamości usługi AAD z usługą sieci web w usłudze Azure Kubernetes, aby uzyskać dostęp do zasobów w chmurze podczas oceniania.
services: machine-learning
author: trevorbye
ms.author: trbye
ms.reviewer: aashishb
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: f997aef59e91bed325b84af855a84f43cd639d83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77122846"
---
# <a name="use-azure-ad-identity-with-your-machine-learning-web-service-in-azure-kubernetes-service"></a>Używanie tożsamości usługi Azure AD za pomocą usługi uczenia maszynowego w usłudze Azure Kubernetes

W tym instrukcje dowiesz się, jak przypisać tożsamość usługi Azure Active Directory (AAD) do wdrożonego modelu uczenia maszynowego w usłudze Azure Kubernetes. Projekt [Tożsamości zasobnika usługi AAD](https://github.com/Azure/aad-pod-identity) umożliwia aplikacjom bezpieczny dostęp do zasobów w chmurze za pomocą usługi AAD przy użyciu ćwi0dów [tożsamości zarządzanej](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) i ćwigów Kubernetes. Dzięki temu usługa sieci web bezpiecznie uzyskać dostęp do zasobów platformy Azure bez konieczności `score.py` osadzania poświadczeń lub zarządzania tokenami bezpośrednio wewnątrz skryptu. W tym artykule opisano kroki tworzenia i instalowania usługi Azure Identity w klastrze usługi Azure Kubernetes i przypisywanie tożsamości do wdrożonej usługi sieci web.

## <a name="prerequisites"></a>Wymagania wstępne

- [Rozszerzenie interfejsu wiersza polecenia platformy Azure dla usługi Machine Learning](reference-azure-machine-learning-cli.md), zestaw [SDK usługi Azure Machine Learning dla języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)lub rozszerzenie kodu usługi Azure Machine Learning Visual [Studio.](tutorial-setup-vscode-extension.md)

- Dostęp do klastra AKS za pomocą `kubectl` polecenia. Aby uzyskać więcej informacji, zobacz [Łączenie się z klastrem](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough#connect-to-the-cluster)

- Usługa sieci web usługi Azure Machine Learning wdrożona w klastrze AKS.

## <a name="create-and-install-an-azure-identity-in-your-aks-cluster"></a>Tworzenie i instalowanie tożsamości platformy Azure w klastrze usługi AKS

1. Aby ustalić, czy klaster AKS jest włączony, należy użyć następującego polecenia:

    ```azurecli-interactive
    az aks show --name <AKS cluster name> --resource-group <resource group name> --subscription <subscription id> --query enableRbac
    ```

    To polecenie zwraca `true` wartość, jeśli jest włączony rbac. Ta wartość określa polecenie do użycia w następnym kroku.

1. Aby zainstalować [tożsamość zasobnika usługi AAD](https://github.com/Azure/aad-pod-identity#getting-started) w klastrze usługi AKS, użyj jednego z następujących poleceń:

    * Jeśli klaster AKS ma **włączoną funkcję RBAC,** użyj następującego polecenia:
    
        ```azurecli-interactive
        kubectl apply -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment-rbac.yaml
        ```
    
    * Jeśli klaster AKS **nie ma włączonego rbac,** użyj następującego polecenia:
    
        ```azurecli-interactive
        kubectl apply -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment.yaml
        ```
    
        Dane wyjściowe polecenia są podobne do następującego tekstu:

        ```text
        customresourcedefinition.apiextensions.k8s.io/azureassignedidentities.aadpodidentity.k8s.io created
        customresourcedefinition.apiextensions.k8s.io/azureidentitybindings.aadpodidentity.k8s.io created
        customresourcedefinition.apiextensions.k8s.io/azureidentities.aadpodidentity.k8s.io created
        customresourcedefinition.apiextensions.k8s.io/azurepodidentityexceptions.aadpodidentity.k8s.io created
        daemonset.apps/nmi created
        deployment.apps/mic created
        ```

1. [Utwórz tożsamość platformy Azure](https://github.com/Azure/aad-pod-identity#2-create-an-azure-identity) zgodnie z instrukcjami wyświetlanymi na stronie projektu tożsamości podsytu usługi AAD.

1. [Zainstaluj usługę Azure Identity,](https://github.com/Azure/aad-pod-identity#3-install-the-azure-identity) wykonując kroki pokazane na stronie projektu tożsamości podsytu usługi AAD.

1. [Zainstaluj powiązanie tożsamości platformy Azure](https://github.com/Azure/aad-pod-identity#5-install-the-azure-identity-binding) zgodnie z instrukcjami wyświetlanymi na stronie projektu tożsamości podsytu usługi AAD.

1. Jeśli tożsamość platformy Azure utworzona w poprzednim kroku nie znajduje się w tej samej grupie zasobów co klaster AKS, wykonaj kroki opisane w [krokach wyświetlanych na](https://github.com/Azure/aad-pod-identity#6-set-permissions-for-mic) stronie projektu tożsamości podsyciągów usługi AAD.

## <a name="assign-azure-identity-to-machine-learning-web-service"></a>Przypisywanie tożsamości platformy Azure do usługi sieci web uczenia maszynowego

Poniższe kroki korzystają z usługi Azure Identity utworzonej w poprzedniej sekcji i przypisują ją do usługi sieci web usługi AKS za pomocą **etykiety selektora**.

Najpierw zidentyfikuj nazwę i obszar nazw wdrożenia w klastrze usługi AKS, który chcesz przypisać tożsamość platformy Azure. Informacje te można uzyskać, uruchamiając następujące polecenie. Obszary nazw powinny być nazwą obszaru roboczego usługi Azure Machine Learning, a nazwa wdrożenia powinna być nazwą punktu końcowego, jak pokazano w portalu.

```azurecli-interactive
kubectl get deployment --selector=isazuremlapp=true --all-namespaces --show-labels
```

Dodaj etykietę selektora tożsamości platformy Azure do wdrożenia, edytując specyfikację wdrożenia. Wartość selektora powinna być zdefiniowana w kroku 5 [install the Azure Identity Binding](https://github.com/Azure/aad-pod-identity#5-install-the-azure-identity-binding).

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzureIdentityBinding
metadata:
  name: demo1-azure-identity-binding
spec:
  AzureIdentity: <a-idname>
  Selector: <label value to match>
```

Edytuj wdrożenie, aby dodać etykietę selektora tożsamości platformy Azure. Przejdź do poniższej `/spec/template/metadata/labels`sekcji w obszarze . Powinny być widoczne `isazuremlapp: “true”`wartości, takie jak . Dodaj etykietę aad-pod-identity, jak pokazano poniżej.

```azurecli-interactive
    kubectl edit deployment/<name of deployment> -n azureml-<name of workspace>
```

```yaml
spec:
  template:
    metadata:
      labels:
      - aadpodidbinding: "<value of Selector in AzureIdentityBinding>"
      ...
```

Aby sprawdzić, czy etykieta została poprawnie dodana, uruchom następujące polecenie.

```azurecli-interactive
   kubectl get deployment <name of deployment> -n azureml-<name of workspace> --show-labels
```

Aby wyświetlić wszystkie stany zasobników, uruchom następujące polecenie.

```azurecli-interactive
    kubectl get pods -n azureml-<name of workspace>
```

Po uruchomieniu zasobników usługi sieci web dla tego wdrożenia będą teraz mogły uzyskiwać dostęp do zasobów platformy Azure za pośrednictwem usługi Azure Identity bez konieczności osadzania poświadczeń w kodzie. 

## <a name="assign-the-appropriate-roles-to-your-azure-identity"></a>Przypisywanie odpowiednich ról do usługi Azure Identity

[Przypisz swoją tożsamość zarządza zarządzałą platformy Azure z odpowiednimi rolami,](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal) aby uzyskać dostęp do innych zasobów platformy Azure. Upewnij się, że przypisywane role mają poprawne **akcje danych**. Na przykład [rola czytnika danych obiektów blob magazynu](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader) będzie miała uprawnienia do odczytu do obiektu blob magazynu, podczas gdy ogólna [rola czytnika](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader) może nie.

## <a name="use-azure-identity-with-your-machine-learning-web-service"></a>Korzystanie z usługi Azure Identity za pomocą usługi uczenia maszynowego

Wdrażanie modelu w klastrze usługi AKS. Skrypt `score.py` może zawierać operacje wskazujące zasoby platformy Azure, do których ma dostęp twoja tożsamość azure. Upewnij się, że zainstalowano wymagane zależności biblioteki klienta dla zasobu, do którego próbujesz uzyskać dostęp. Poniżej znajduje się kilka przykładów, jak można użyć usługi Azure Identity, aby uzyskać dostęp do różnych zasobów platformy Azure z usługi.

### <a name="access-key-vault-from-your-web-service"></a>Dostęp do usługi Key Vault z usługi sieci Web

Jeśli dałeś dostęp do odczytu usługi Azure Identity `score.py` do klucza tajnego wewnątrz usługi Key **Vault,** możesz uzyskać do niego dostęp przy użyciu następującego kodu.

```python
from azure.identity import DefaultAzureCredential
from azure.keyvault.secrets import SecretClient

my_vault_name = "yourkeyvaultname"
my_vault_url = "https://{}.vault.azure.net/".format(my_vault_name) 
my_secret_name = "sample-secret"

# This will use your Azure Managed Identity
credential = DefaultAzureCredential()
secret_client = SecretClient(
    vault_url=my_vault_url,
    credential=credential)
secret = secret_client.get_secret(my_secret_name)
```

### <a name="access-blob-from-your-web-service"></a>Dostęp do obiektów Blob z usługi sieci web

Jeśli udzielono usługi Azure Identity dostęp do odczytu `score.py` danych wewnątrz **obiektu blob magazynu,** można uzyskać do niego dostęp przy użyciu następującego kodu.

```python
from azure.identity import DefaultAzureCredential
from azure.storage.blob import BlobServiceClient

my_storage_account_name = "yourstorageaccountname"
my_storage_account_url = "https://{}.blob.core.windows.net/".format(my_storage_account_name)

# This will use your Azure Managed Identity
credential = DefaultAzureCredential()
blob_service_client = BlobServiceClient(
    account_url=my_storage_account_url,
    credential=credential
)
blob_client = blob_service_client.get_blob_client(container="some-container", blob="some_text.txt")
blob_data = blob_client.download_blob()
blob_data.readall()
```

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat korzystania z biblioteki klienta usługi Azure Identity języka Python, zobacz [repozytorium](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/identity/azure-identity#azure-identity-client-library-for-python) w usłudze GitHub.
* Aby uzyskać szczegółowy przewodnik dotyczący wdrażania modeli w klastrach usługi Azure Kubernetes, zobacz [instrukcje](how-to-deploy-azure-kubernetes-service.md).