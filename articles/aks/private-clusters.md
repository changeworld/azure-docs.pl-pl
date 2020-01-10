---
title: Prywatny klaster usługi Azure Kubernetes Service
description: Dowiedz się, jak utworzyć prywatny klaster usługi Azure Kubernetes Service (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 12/10/2019
ms.author: mlearned
ms.openlocfilehash: 6152becb8debd0700ddab6190284514c6d6cf69d
ms.sourcegitcommit: 8b37091efe8c575467e56ece4d3f805ea2707a64
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75830058"
---
# <a name="public-preview---private-azure-kubernetes-service-cluster"></a>Publiczna wersja zapoznawcza — prywatny klaster usługi Azure Kubernetes Service

W klastrze prywatnym dla warstwy kontroli/serwera interfejsu API będą dostępne wewnętrzne adresy IP zdefiniowane w [RFC1918](https://tools.ietf.org/html/rfc1918).  Za pomocą klastra prywatnego można zapewnić, że ruch sieciowy między serwerem interfejsu API a pulami węzłów pozostanie tylko w sieci prywatnej.

Komunikacja między płaszczyzną kontroli/serwerem interfejsu API, który znajduje się w subskrypcji platformy Azure zarządzanej przez usługę AKS oraz pula klastrów/węzłów klientów, która znajduje się w subskrypcji klienta, może komunikować się ze sobą za pomocą [usługi link prywatny][private-link-service] w sieci wirtualnej serwera interfejsu API i prywatnego punktu końcowego uwidocznionego w podsieci klienta AKS klastra.

> [!IMPORTANT]
> Funkcja AKS w wersji zapoznawczej to samoobsługowe uczestnictwo. Wersje zapoznawcze są udostępniane w postaci "AS-IS" i "jako dostępne" i są wyłączone z umów dotyczących poziomu usług i ograniczonej rękojmi. Wersje zapoznawcze AKS są częściowo objęte obsługą klienta w oparciu o najlepszy nakład pracy. W związku z tym te funkcje nie są przeznaczone do użytku produkcyjnego. Aby dowiedzieć się więcej, zobacz następujące artykuły pomocy technicznej:
>
> * [Zasady pomocy technicznej AKS](support-policies.md)
> * [Pomoc techniczna platformy Azure — często zadawane pytania](faq.md)

## <a name="before-you-begin"></a>Przed rozpoczęciem

* Potrzebujesz interfejsu wiersza polecenia platformy Azure w wersji 2.0.77 lub nowszej oraz rozszerzenia AKS-Preview 0.4.18

## <a name="current-supported-regions"></a>Bieżące Obsługiwane regiony
* Zachodnie stany USA
* Zachodnie stany USA 2
* Wschodnie stany USA 2
* Kanada Środkowa
* Europa Północna
* Europa Zachodnia
* Australia Wschodnia

## <a name="install-latest-aks-cli-preview-extension"></a>Zainstaluj rozszerzenie najnowszej wersji zapoznawczej interfejsu wiersza polecenia AKS

Aby można było korzystać z klastrów prywatnych, wymagany jest interfejs wiersza polecenia *AKS-Preview* w wersji 0.4.18 lub nowszej. Zainstaluj rozszerzenie interfejsu wiersza polecenia platformy Azure w *wersji zapoznawczej* przy użyciu poleceń [AZ Extension Add][az-extension-add] , a następnie wyszukaj wszystkie dostępne aktualizacje za pomocą polecenia [AZ Extension Update][az-extension-update] ::

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```
> [!CAUTION]
> Po zarejestrowaniu funkcji w ramach subskrypcji nie można obecnie wyrejestrować tej funkcji. Po włączeniu niektórych funkcji w wersji zapoznawczej można użyć wartości domyślnych dla wszystkich klastrów AKS utworzonych w ramach subskrypcji. Nie włączaj funkcji w wersji zapoznawczej w ramach subskrypcji produkcyjnych. Korzystaj z oddzielnej subskrypcji, aby testować funkcje w wersji zapoznawczej i zbierać opinie.

```azurecli-interactive
az feature register --name AKSPrivateLinkPreview --namespace Microsoft.ContainerService
```

Wyświetlenie *zarejestrowanego*stanu może potrwać kilka minut. Stan rejestracji można sprawdzić za pomocą polecenia [AZ Feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSPrivateLinkPreview')].{Name:name,State:properties.state}"
```

Po zarejestrowaniu stanu Odśwież rejestrację dostawcy zasobów *Microsoft. ContainerService* za pomocą polecenia [AZ Provider Register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
az provider register --namespace Microsoft.Network
```
## <a name="create-a-private-aks-cluster"></a>Tworzenie prywatnego klastra AKS

#### <a name="default-basic-networking"></a>Domyślna sieć podstawowa 

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster  
```
WHERE--Enable-Private-Cluster to obowiązkowa Flaga dla klastra prywatnego 

#### <a name="advanced-networking"></a>Zaawansowane sieci  

```azurecli-interactive
az aks create \
    --resource-group <private-cluster-resource-group> \
    --name <private-cluster-name> \
    --load-balancer-sku standard \
    --enable-private-cluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 
```
WHERE--Enable-Private-Cluster to obowiązkowa Flaga dla klastra prywatnego 

## <a name="steps-to-connect-to-the-private-cluster"></a>Procedura łączenia się z klastrem prywatnym
Punkt końcowy serwera interfejsu API nie ma publicznego adresu IP. W związku z tym użytkownicy będą musieli utworzyć maszynę wirtualną platformy Azure w sieci wirtualnej i połączyć się z serwerem interfejsu API. Kroki opisane w

* Pobieranie poświadczeń w celu nawiązania połączenia z klastrem

   ```azurecli-interactive
   az aks get-credentials --name MyManagedCluster --resource-group MyResourceGroup
   ```
* Utwórz maszynę wirtualną w tej samej sieci wirtualnej co klaster AKS lub Utwórz maszynę wirtualną w innej sieci wirtualnej i równorzędnej tę sieć wirtualną z siecią wirtualną klastra AKS
* Jeśli tworzysz maszynę wirtualną w innej sieci wirtualnej, musisz skonfigurować link między tą siecią wirtualną i strefą Prywatna strefa DNS
    * Przejdź do grupy zasobów MC_ * w portalu 
    * Kliknij strefę Prywatna strefa DNS 
    * Wybierz łącze sieci wirtualnej w lewym okienku
    * Utwórz nowe łącze, aby dodać sieć wirtualną maszyny wirtualnej do strefy Prywatna strefa DNS (aby *łącze strefy DNS stało się dostępne, potrwa kilka minut)*
    * Wróć do grupy zasobów MC_ * w portalu
    * Wybierz sieć wirtualną w okienku po prawej stronie. Nazwa sieci wirtualnej będzie mieć postać AKS-VNET-*.
    * Wybierz pozycję Komunikacja równorzędna w okienku po lewej stronie
    * Kliknij pozycję Dodaj i Dodaj sieć wirtualną maszyny wirtualnej i Utwórz komunikację równorzędną.
    * Przejdź do sieci wirtualnej, w której znajduje się maszyna wirtualna, a następnie kliknij pozycję Komunikacja równorzędna i wybierz sieć wirtualną AKS i Utwórz komunikację równorzędną. Jeśli zakresy adresów w sieci wirtualnej AKS oraz w sieci wirtualnej maszyny wirtualnej są kolizje, Komunikacja równorzędna nie powiedzie się. Zapoznaj się z tym [dokumentem][virtual-network-peering] , aby uzyskać więcej informacji na temat komunikacji równorzędnej sieci wirtualnych.
* Użyj protokołu SSH do maszyny wirtualnej
* Instalowanie narzędzia polecenia kubectl i uruchamianie poleceń polecenia kubectl

## <a name="dependencies"></a>Zależności  
* Tylko w warstwie Standardowa LB — brak obsługi dla podstawowego modułu równoważenia obciążenia  

## <a name="limitations"></a>Ograniczenia 
* Te same [ograniczenia usługi Azure Private link][private-link-service] dotyczą klastrów prywatnych, prywatnych punktów końcowych platformy Azure i punktów końcowych usługi Virtual Network nie są obecnie obsługiwane w tej samej sieci wirtualnej
* Brak obsługi węzłów wirtualnych w klastrze prywatnym, aby obsłużyć prywatne wystąpienia ACI w prywatnej sieci wirtualnej platformy Azure
* Brak wsparcia dla integracji usługi Azure DevOps z użyciem klastrów prywatnych
* Jeśli klienci muszą włączyć ACR do pracy z prywatnymi AKS, Sieć wirtualna ACR będzie musiała być połączona z siecią wirtualną klastra agenta
* Brak bieżącej obsługi Azure Dev Spaces
* Brak obsługi konwersji istniejących klastrów AKS do klastrów prywatnych  
* Usunięcie lub zmodyfikowanie prywatnego punktu końcowego w podsieci klienta spowoduje, że klaster przestanie działać 
* Azure Monitor kontenerów danych na żywo nie są obecnie obsługiwane
* Korzystanie z własnego systemu DNS nie jest obecnie obsługiwane


<!-- LINKS - internal -->
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[private-link-service]: https://docs.microsoft.com/azure/private-link/private-link-service-overview
[virtual-network-peering]: ../virtual-network/virtual-network-peering-overview.md

