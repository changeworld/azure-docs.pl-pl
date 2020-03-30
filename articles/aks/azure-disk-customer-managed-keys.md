---
title: Szyfrowanie dysków platformy Azure w usłudze Azure Kubernetes (Azure Kubernetes Service) (AKS) za pomocą klucza zarządzanego przez klienta
description: Przynieś własne klucze (BYOK), aby zaszyfrować dyski systemu operacyjnego AKS i data.
services: container-service
ms.topic: article
ms.date: 01/12/2020
ms.openlocfilehash: bb6ba5e6dd4ace9e33043079c0f435c10baf5cb2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77596508"
---
# <a name="bring-your-own-keys-byok-with-azure-disks-in-azure-kubernetes-service-aks"></a>Przynieś własne klucze (BYOK) z dyskami platformy Azure w usłudze Azure Kubernetes Service (AKS)

Usługa Azure Storage szyfruje wszystkie dane na koncie magazynu w stanie spoczynku. Domyślnie dane są szyfrowane za pomocą kluczy zarządzanych przez firmę Microsoft. Aby uzyskać dodatkową kontrolę nad kluczami szyfrowania, można podać [klucze zarządzane][customer-managed-keys] przez klienta do szyfrowania w spoczynku zarówno dla systemu operacyjnego, jak i dysków danych dla klastrów AKS.

> [!NOTE]
> Klastry AKS oparte na systemach BYOK i Windows są dostępne w [regionach platformy Azure,][supported-regions] które obsługują szyfrowanie po stronie serwera dysków zarządzanych platformy Azure.

## <a name="before-you-begin"></a>Przed rozpoczęciem

* W tym artykule założono, że tworzysz *nowy klaster AKS*.

* Należy włączyć ochronę usuwania nietrwałego i przeczyszczać dla *usługi Azure Key Vault* podczas szyfrowania dysków zarządzanych za pomocą usługi Key Vault.

* Potrzebujesz interfejsu wiersza polecenia platformy Azure w wersji 2.0.79 lub nowszej oraz rozszerzenia aks-preview 0.4.26

> [!IMPORTANT]
> Funkcje podglądu usługi AKS są samoobsługowe. Wersje zapoznawcza są dostarczane w stanie "tak jak jest" i "w miarę dostępności" i są wyłączone z umów o gwarantowanym poziomie usług i ograniczonej gwarancji. Podglądy AKS są częściowo objęte obsługą klienta na podstawie najlepszych starań. W związku z tym te funkcje nie są przeznaczone do użytku produkcyjnego. Aby uzyskać dodatkowe infromation, zobacz następujące artykuły pomocy technicznej:
>
> * [Zasady wsparcia AKS](support-policies.md)
> * [Często zadawane pytania dotyczące pomocy technicznej platformy Azure](faq.md)

## <a name="install-latest-aks-cli-preview-extension"></a>Instalowanie najnowszego rozszerzenia podglądu interfejsu wiersza polecenia AKS

Aby korzystać z kluczy zarządzanych przez klienta, potrzebujesz rozszerzenia interfejsu wiersza polecenia *aks-preview* w wersji 0.4.26 lub nowszej. Zainstaluj rozszerzenie interfejsu wiersza polecenia interfejsu wiersza polecenia platformy Azure *aks-preview* przy użyciu polecenia [dodawania rozszerzenia az,][az-extension-add] a następnie sprawdź dostępność dostępnych aktualizacji za pomocą polecenia [aktualizacji rozszerzenia az:][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="create-an-azure-key-vault-instance"></a>Tworzenie wystąpienia usługi Azure Key Vault

Użyj wystąpienia usługi Azure Key Vault do przechowywania kluczy.  Opcjonalnie można użyć portalu Azure do [konfigurowania kluczy zarządzanych przez klienta za pomocą usługi Azure Key Vault][byok-azure-portal]

Utwórz nową *grupę zasobów*, a następnie utwórz nowe *wystąpienie usługi Key Vault* i włącz ochronę przed usuwaniem i usuwaniem nietrwałym.  Upewnij się, że używasz tych samych nazw regionów i grup zasobów dla każdego polecenia.

```azurecli-interactive
# Optionally retrieve Azure region short names for use on upcoming commands
az account list-locations
```

```azurecli-interactive
# Create new resource group in a supported Azure region
az group create -l myAzureRegionName -n myResourceGroup

# Create an Azure Key Vault resource in a supported Azure region
az keyvault create -n myKeyVaultName -g myResourceGroup -l myAzureRegionName  --enable-purge-protection true --enable-soft-delete true
```

## <a name="create-an-instance-of-a-diskencryptionset"></a>Tworzenie wystąpienia zestawu DiskEncryptionSet

Zastąp *myKeyVaultName* nazwą magazynu kluczy.  Aby wykonać następujące kroki, potrzebny jest również *klucz* przechowywany w usłudze Azure Key Vault.  Przechowuj istniejący klucz w przechowalni kluczy utworzonej w poprzednich krokach lub [wygeneruj nowy klucz][key-vault-generate] i zastąp *myKeyName* poniżej nazwą klucza.
    
```azurecli-interactive
# Retrieve the Key Vault Id and store it in a variable
keyVaultId=$(az keyvault show --name myKeyVaultName --query [id] -o tsv)

# Retrieve the Key Vault key URL and store it in a variable
keyVaultKeyUrl=$(az keyvault key show --vault-name myKeyVaultName  --name myKeyName  --query [key.kid] -o tsv)

# Create a DiskEncryptionSet
az disk-encryption-set create -n myDiskEncryptionSetName  -l myAzureRegionName  -g myResourceGroup --source-vault $keyVaultId --key-url $keyVaultKeyUrl 
```

## <a name="grant-the-diskencryptionset-access-to-key-vault"></a>Udzielanie dostępu DiskEncryptionSet do magazynu kluczy

Użyj zestawu DiskEncryptionSet i grup zasobów utworzonych w poprzednich krokach i udziel dostępu do zasobów DiskEncryptionSet do usługi Azure Key Vault.

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
desIdentity=$(az disk-encryption-set show -n myDiskEncryptionSetName  -g myResourceGroup --query [identity.principalId] -o tsv)

# Update security policy settings
az keyvault set-policy -n myKeyVaultName -g myResourceGroup --object-id $desIdentity --key-permissions wrapkey unwrapkey get

# Assign the reader role
az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId
```

## <a name="create-a-new-aks-cluster-and-encrypt-the-os-disk"></a>Tworzenie nowego klastra AKS i szyfrowanie dysku systemu operacyjnego

Utwórz **nową grupę zasobów** i klaster AKS, a następnie użyj klucza do zaszyfrowania dysku systemu operacyjnego. Klucze zarządzane przez klienta są obsługiwane tylko w wersjach kubernetes większych niż 1.17. 

> [!IMPORTANT]
> Upewnij się, że tworzysz nową grupę resoruce dla klastra AKS

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
diskEncryptionSetId=$(az resource show -n mydiskEncryptionSetName -g myResourceGroup --resource-type "Microsoft.Compute/diskEncryptionSets" --query [id] -o tsv)

# Create a resource group for the AKS cluster
az group create -n myResourceGroup -l myAzureRegionName

# Create the AKS cluster
az aks create -n myAKSCluster -g myResourceGroup --node-osdisk-diskencryptionset-id $diskEncryptionSetId --kubernetes-version 1.17.0 --generate-ssh-keys
```

Po dodaniu nowych pul węzłów do klastra utworzonego powyżej klucz zarządzany przez klienta podany podczas tworzenia jest używany do szyfrowania dysku systemu operacyjnego.

## <a name="encrypt-your-aks-cluster-data-disk"></a>Szyfrowanie dysku danych klastra AKS

Można również zaszyfrować dyski danych AKS za pomocą własnych kluczy.

> [!IMPORTANT]
> Upewnij się, że masz odpowiednie poświadczenia AKS. Podmiot zabezpieczeń usługi musi mieć dostęp współautora do grupy zasobów, w której jest wdrażany zestaw dysków. W przeciwnym razie pojawi się błąd sugerujący, że podmiot zabezpieczeń usługi nie ma uprawnień.

```azurecli-interactive
# Retrieve your Azure Subscription Id from id property as shown below
az account list
```

```
someuser@Azure:~$ az account list
[
  {
    "cloudName": "AzureCloud",
    "id": "666e66d8-1e43-4136-be25-f25bb5de5893",
    "isDefault": true,
    "name": "MyAzureSubscription",
    "state": "Enabled",
    "tenantId": "3ebbdf90-2069-4529-a1ab-7bdcb24df7cd",
    "user": {
      "cloudShellID": true,
      "name": "someuser@azure.com",
      "type": "user"
    }
  }
]
```

Utwórz plik o nazwie **byok-azure-disk.yaml,** który zawiera następujące informacje.  Zastąp myAzureSubscriptionId, myResourceGroup i myDiskEncrptionSetName wartościami i zastosuj yaml.  Upewnij się, że używasz grupy zasobów, w której jest wdrożony zestaw DiskEncryptionSet.  Jeśli używasz usługi Azure Cloud Shell, ten plik można utworzyć przy użyciu vi lub nano, tak jakby działał na systemie wirtualnym lub fizycznym:

```
kind: StorageClass
apiVersion: storage.k8s.io/v1  
metadata:
  name: hdd
provisioner: kubernetes.io/azure-disk
parameters:
  skuname: Standard_LRS
  kind: managed
  diskEncryptionSetID: "/subscriptions/{myAzureSubscriptionId}/resourceGroups/{myResourceGroup}/providers/Microsoft.Compute/diskEncryptionSets/{myDiskEncryptionSetName}"
```
Następnie uruchom to wdrożenie w klastrze AKS:
```azurecli-interactive
# Get credentials
az aks get-credentials --name myAksCluster --resource-group myResourceGroup --output table

# Update cluster
kubectl apply -f byok-azure-disk.yaml
```

## <a name="limitations"></a>Ograniczenia

* Byok jest obecnie dostępny tylko w ga i wersji zapoznawczej w niektórych [regionach platformy Azure][supported-regions]
* Szyfrowanie dysku systemu operacyjnego obsługiwane przez kubernetes w wersji 1.17 lub nowszej   
* Dostępne tylko w regionach, w których obsługiwany jest byok
* Szyfrowanie za pomocą kluczy zarządzanych przez klienta jest obecnie przeznaczone tylko dla nowych klastrów AKS, nie można uaktualnić istniejących klastrów
* Klaster AKS przy użyciu zestawów skalowania maszyny wirtualnej są wymagane, nie jest obsługiwana dla zestawów dostępności maszyny wirtualnej


## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [najlepszymi rozwiązaniami w zakresie zabezpieczeń klastra AKS][best-practices-security]

<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[best-practices-security]: /azure/aks/operator-best-practices-cluster-security
[byok-azure-portal]: /azure/storage/common/storage-encryption-keys-portal
[customer-managed-keys]: /azure/virtual-machines/windows/disk-encryption#customer-managed-keys
[key-vault-generate]: /azure/key-vault/key-vault-manage-with-cli2
[supported-regions]: /azure/virtual-machines/windows/disk-encryption#supported-regions
