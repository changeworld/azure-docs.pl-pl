---
title: Tworzenie klastra usługi Azure Red Hat OpenShift 4,3 | Microsoft Docs
description: Tworzenie klastra przy użyciu usługi Azure Red Hat OpenShift 3,11
author: lamek
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/06/2020
keywords: ARO, OpenShift, AZ ARO, Red Hat, CLI
ms.openlocfilehash: 81f8edb42be1f73692062d36440890ef5a1e7c9a
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2020
ms.locfileid: "78899310"
---
# <a name="create-access-and-manage-an-azure-red-hat-openshift-43-cluster"></a>Tworzenie klastra usługi Azure Red Hat OpenShift 4,3, uzyskiwanie do niego dostępu i zarządzanie nim

> [!IMPORTANT]
> Azure Red Hat OpenShift (ARO) 4,3 jest oferowany w wersji zapoznawczej. Funkcje w wersji zapoznawczej są samoobsługowe i udostępniane jako dostępne i są wykluczone z umowy dotyczącej poziomu usług (SLA) i ograniczonej rękojmi. W związku z tym funkcje te nie są przeznaczone do użytku produkcyjnego.

## <a name="prerequisites"></a>Wymagania wstępne

Aby utworzyć klaster usługi Azure Red Hat OpenShift 4,3, potrzebne są następujące elementy:

- Interfejs wiersza polecenia platformy Azure w wersji 2.0.72 lub nowszej
  
- Rozszerzenie "AZ ARO"

- Sieć wirtualna zawierająca dwie puste podsieci, z których każda nie jest podłączona do sieciowej grupy zabezpieczeń.  Klaster zostanie wdrożony w tych podsieciach.

- Aplikacja usługi AAD klastra (identyfikator klienta i klucz tajny) oraz jednostka usługi lub wystarczające uprawnienia w usłudze AAD dla `az aro create` w celu automatycznego utworzenia aplikacji i nazwy głównej usług w usłudze AAD.

- Nazwa główna usługi RP i jednostka usługi klastra muszą mieć rolę współautor w sieci wirtualnej klastra.  Jeśli masz rolę "Administrator dostępu użytkowników" w sieci wirtualnej, `az aro create` automatycznie skonfiguruje przypisania ról.

### <a name="install-the-az-aro-extension"></a>Zainstaluj rozszerzenie "AZ ARO"
Rozszerzenie `az aro` umożliwia tworzenie i usuwanie klastrów usługi Azure Red Hat OpenShift oraz uzyskiwanie do nich dostępu bezpośrednio z wiersza polecenia przy użyciu interfejsu CLI platformy Azure.

> [!Note] 
> `az aro` rozszerzenie jest w wersji zapoznawczej. Może zostać zmieniona lub usunięta w przyszłej wersji.
> Aby skorzystać z wersji zapoznawczej rozszerzenia `az aro`, należy zarejestrować dostawcę zasobów `Microsoft.RedHatOpenShift`.
> 
>    ```console
>    az provider register -n Microsoft.RedHatOpenShift --wait
>    ```

1. Loguje się do platformy Azure.

   ```console
   az login
   ```

2. Uruchom następujące polecenie, aby zainstalować rozszerzenie `az aro`:

   ```console
   az extension add --source https://arosvc.blob.core.windows.net/az-preview/aro-0.1.0-py2.py3-none-any.whl
   ```

3. Sprawdź, czy rozszerzenie ARO jest zarejestrowane.

   ```console
   az -v
   ...
   Extensions:
   aro                                0.1.0
   ...
   ```
  
### <a name="create-a-virtual-network-containing-two-empty-subnets"></a>Tworzenie sieci wirtualnej zawierającej dwie puste podsieci

Wykonaj następujące kroki, aby utworzyć sieć wirtualną zawierającą dwie puste podsieci.

1. Ustaw następujące zmienne.

   ```console
   LOCATION=eastus        #the location of your cluster
   RESOURCEGROUP="v4-$LOCATION"    #the name of the resource group where you want to create your cluster
   CLUSTER=cluster        #the name of your cluster
   ```

2. Utwórz grupę zasobów dla klastra.

   ```console
   az group create -g "$RESOURCEGROUP" -l $LOCATION
   ```

3. Utwórz sieć wirtualną.

   ```console
   az network vnet create \
     -g "$RESOURCEGROUP" \
     -n vnet \
     --address-prefixes 10.0.0.0/9 \
     >/dev/null
   ```

4. Dodaj dwie puste podsieci do sieci wirtualnej.

   ```console
    for subnet in "$CLUSTER-master" "$CLUSTER-worker"; do
     az network vnet subnet create \
       -g "$RESOURCEGROUP" \
       --vnet-name vnet \
       -n "$subnet" \
       --address-prefixes 10.$((RANDOM & 127)).$((RANDOM & 255)).0/24 \
       --service-endpoints Microsoft.ContainerRegistry \
       >/dev/null
   done
   ```

5. Wyłączenie zasad sieciowych dla usługi link prywatny w sieci wirtualnej i podsieciach. Jest to wymaganie do uzyskiwania dostępu do klastra i zarządzania nim za pomocą usługi ARO.

   ```console
   az network vnet subnet update \
     -g "$RESOURCEGROUP" \
     --vnet-name vnet \
     -n "$CLUSTER-master" \
     --disable-private-link-service-network-policies true \
     >/dev/null
   ```

## <a name="create-a-cluster"></a>Tworzenie klastra

Uruchom następujące polecenie, aby utworzyć klaster.

```console
az aro create \
  -g "$RESOURCEGROUP" \
  -n "$CLUSTER" \
  --vnet vnet \
  --master-subnet "$CLUSTER-master" \
  --worker-subnet "$CLUSTER-worker"
```

>[!NOTE]
> Utworzenie klastra zwykle trwa około 35 minut.

## <a name="access-the-cluster-console"></a>Dostęp do konsoli klastra

Adres URL konsoli klastra (formularza `https://console-openshift-console.apps.<random>.<location>.aroapp.io/`) można znaleźć w obszarze zasobów klastra usługi Azure Red Hat OpenShift 4,3. Uruchom następujące polecenie, aby wyświetlić zasób:

```console
az aro list -o table
```

Możesz zalogować się do klastra przy użyciu `kubeadmin` użytkownika.  Uruchom następujące polecenie, aby znaleźć hasło dla `kubeadmin` użytkownika:

```dotnetcli
az aro list-credentials -g "$RESOURCEGROUP" -n "$CLUSTER"
```

## <a name="delete-a-cluster"></a>Usuwanie klastra

Uruchom następujące polecenie, aby usunąć klaster.

```console
az aro delete -g "$RESOURCEGROUP" -n "$CLUSTER"

# (optional)
for subnet in "$CLUSTER-master" "$CLUSTER-worker"; do
  az network vnet subnet delete -g "$RESOURCEGROUP" --vnet-name vnet -n "$subnet"
done
```