---
title: Tworzenie prywatnego klastra usługi Azure Kubernetes Service
description: Dowiedz się, jak utworzyć prywatny klaster usługi Azure Kubernetes Service (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 1/24/2020
ms.author: mlearned
ms.openlocfilehash: a477c2011ff3c6cf1987ed80ef5c19c26abc40f0
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76713324"
---
# <a name="create-a-private-azure-kubernetes-service-cluster-preview"></a>Tworzenie prywatnego klastra usługi Azure Kubernetes Service (wersja zapoznawcza)

W klastrze prywatnym płaszczyzna kontroli lub serwer interfejsu API ma wewnętrzne adresy IP, które są zdefiniowane w [alokacji RFC1918-Address dla prywatnych Internetu](https://tools.ietf.org/html/rfc1918) dokumentów. Za pomocą klastra prywatnego można zapewnić, że ruch sieciowy między serwerem interfejsu API i pulami węzłów pozostanie tylko w sieci prywatnej.

Płaszczyzna kontroli lub serwer interfejsu API znajduje się w subskrypcji platformy Azure zarządzanej przez usługę Azure Kubernetes Service (AKS). Klaster klienta lub Pula węzłów znajduje się w subskrypcji klienta. Serwer i Pula węzłów mogą komunikować się ze sobą za pomocą [usługi Azure Private link][private-link-service] w sieci wirtualnej serwera interfejsu API i prywatnego punktu końcowego, który jest udostępniany w podsieci klastra AKS klienta.

> [!IMPORTANT]
> Funkcje w wersji zapoznawczej AKS są samoobsługowe i są oferowane na zasadzie zgody. Wersje zapoznawcze są udostępniane *w postaci* , w jakiej są *dostępne* i są wyłączone z umowy dotyczącej poziomu usług (SLA) i ograniczonej rękojmi. Wersje zapoznawcze AKS są częściowo objęte wsparciem klienta w oparciu o *najlepszą* pracę. W związku z tym funkcje te nie są przeznaczone do użytku produkcyjnego. Aby uzyskać więcej informacji, zobacz następujące artykuły pomocy technicznej:
>
> * [Zasady pomocy technicznej AKS](support-policies.md)
> * [Pomoc techniczna platformy Azure — często zadawane pytania](faq.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Interfejs wiersza polecenia platformy Azure w wersji 2.0.77 lub nowszej oraz interfejs wiersza polecenia platformy Azure w wersji zapoznawczej 0.4.18

## <a name="currently-supported-regions"></a>Aktualnie obsługiwane regiony
* Zachodnie stany USA
* Zachodnie stany USA 2
* Wschodnie stany USA 2
* Kanada Środkowa
* Europa Północna
* Europa Zachodnia
* Australia Wschodnia

## <a name="install-the-latest-azure-cli-aks-preview-extension"></a>Zainstaluj najnowsze rozszerzenie AKS w wersji zapoznawczej interfejsu wiersza polecenia platformy Azure

Aby można było korzystać z klastrów prywatnych, wymagany jest interfejs wiersza polecenia platformy Azure AKS w wersji zapoznawczej 0.4.18 lub nowszej. Zainstaluj rozszerzenie AKS interfejsu wiersza polecenia platformy Azure w wersji zapoznawczej za pomocą poleceń [AZ Extension Add][az-extension-add] , a następnie sprawdź, czy są dostępne aktualizacje przy użyciu następującego polecenia [AZ Extension Update][az-extension-update] :

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```
> [!CAUTION]
> Po zarejestrowaniu funkcji w ramach subskrypcji nie można obecnie wyrejestrować tej funkcji. Po włączeniu niektórych funkcji w wersji zapoznawczej można użyć ustawień domyślnych dla wszystkich klastrów AKS utworzonych w ramach subskrypcji. Nie włączaj funkcji w wersji zapoznawczej w ramach subskrypcji produkcyjnych. Korzystaj z oddzielnej subskrypcji, aby testować funkcje w wersji zapoznawczej i zbierać opinie.

```azurecli-interactive
az feature register --name AKSPrivateLinkPreview --namespace Microsoft.ContainerService
```

Wyświetlanie stanu rejestracji może potrwać kilka *minut.* Stan można sprawdzić za pomocą następującego polecenia [AZ Feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSPrivateLinkPreview')].{Name:name,State:properties.state}"
```

Po zarejestrowaniu stanu Odśwież rejestrację dostawcy zasobów *Microsoft. ContainerService* za pomocą następującego polecenia [AZ Provider Register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
az provider register --namespace Microsoft.Network
```
## <a name="create-a-private-aks-cluster"></a>Tworzenie prywatnego klastra AKS

### <a name="default-basic-networking"></a>Domyślna sieć podstawowa 

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster  
```
Where *--enable-Private-Cluster* jest obowiązkową flagą dla klastra prywatnego. 

### <a name="advanced-networking"></a>Zaawansowane sieci  

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
Where *--enable-Private-Cluster* jest obowiązkową flagą dla klastra prywatnego. 

> [!NOTE]
> Jeśli adres CIDR (172.17.0.1/16) mostka platformy Docker koliduje z maską CIDR podsieci, należy odpowiednio zmienić adres mostka platformy Docker.

## <a name="connect-to-the-private-cluster"></a>Nawiązywanie połączenia z klastrem prywatnym
Punkt końcowy serwera interfejsu API nie ma publicznego adresu IP. W związku z tym należy utworzyć maszynę wirtualną platformy Azure w sieci wirtualnej i połączyć się z serwerem interfejsu API. Aby to zrobić, wykonaj następujące czynności:

1. Pobierz poświadczenia, aby połączyć się z klastrem.

   ```azurecli-interactive
   az aks get-credentials --name MyManagedCluster --resource-group MyResourceGroup
   ```

1. Wykonaj jedną z następujących czynności:
   * Utwórz MASZYNę wirtualną w tej samej sieci wirtualnej co klaster AKS.  
   * Utwórz MASZYNę wirtualną w innej sieci wirtualnej i równorzędną sieć wirtualną z siecią wirtualną klastra AKS.

     Jeśli tworzysz MASZYNę wirtualną w innej sieci wirtualnej, skonfiguruj link między tą siecią wirtualną i prywatną strefą DNS. W tym celu:
    
     a. Przejdź do grupy zasobów MC_ * w Azure Portal.  
     b. Wybierz prywatną strefę DNS.   
     d. W lewym okienku wybierz łącze **Sieć wirtualna** .  
     d. Utwórz nowy link, aby dodać sieć wirtualną maszyny wirtualnej do prywatnej strefy DNS. Udostępnienie linku strefy DNS może potrwać kilka minut.  
     e. Wróć do grupy zasobów MC_ * w Azure Portal.  
     f. W prawym okienku wybierz sieć wirtualną. Nazwa sieci wirtualnej ma postać *AKS-VNET-\** .  
     g. W lewym okienku wybierz pozycję **Komunikacja równorzędna**.  
     h. Wybierz pozycję **Dodaj**, Dodaj sieć wirtualną maszyny wirtualnej, a następnie utwórz komunikację równorzędną.  
     i. Przejdź do sieci wirtualnej, w której znajduje się maszyna wirtualna, wybierz pozycję **Komunikacja równorzędna**, wybierz sieć wirtualną AKS, a następnie utwórz komunikację równorzędną. Jeśli zakresy adresów w sieci wirtualnej AKS i konflikty sieci wirtualnej maszyn wirtualnych są niepowodzeniem, Komunikacja równorzędna nie powiedzie się. Aby uzyskać więcej informacji, zobacz [wirtualne sieci równorzędne][virtual-network-peering].

1. Uzyskaj dostęp do maszyny wirtualnej za pośrednictwem Secure Shell (SSH).
1. Zainstaluj narzędzie polecenia kubectl i uruchom polecenia polecenia kubectl.


## <a name="dependencies"></a>Zależności  
* Usługa link prywatny jest obsługiwana tylko w przypadku standardowej Azure Load Balancer. Podstawowa Azure Load Balancer nie jest obsługiwana.  
* Aby użyć niestandardowego serwera DNS, wdróż serwer usługi AD przy użyciu systemu DNS do przesyłania dalej do tego 168.63.129.16 IP

## <a name="limitations"></a>Ograniczenia 
* Strefy dostępności nie są obecnie obsługiwane
* [Ograniczenia usługi Azure Private link][private-link-service] są stosowane do prywatnych klastrów, prywatnych punktów końcowych platformy Azure i punktów końcowych usługi sieci wirtualnej, które nie są obecnie obsługiwane w tej samej sieci wirtualnej.
* Brak obsługi węzłów wirtualnych w klastrze prywatnym do Azure Container Instances prywatnego (ACI) w prywatnej sieci wirtualnej platformy Azure
* Brak wsparcia dla integracji usługi Azure DevOps z użyciem klastrów prywatnych
* W przypadku klientów, którzy muszą umożliwić Azure Container Registry pracy z prywatnym AKS, Container Registry sieci wirtualnej musi być połączona z siecią wirtualną klastra agentów.
* Brak bieżącej obsługi Azure Dev Spaces
* Brak obsługi konwertowania istniejących klastrów AKS do klastrów prywatnych
* Usunięcie lub zmodyfikowanie prywatnego punktu końcowego w podsieci klienta spowoduje, że klaster przestanie działać. 
* Azure Monitor kontenerów danych na żywo nie są obecnie obsługiwane.
* *Korzystanie z własnego systemu DNS* nie jest obecnie obsługiwane.


<!-- LINKS - internal -->
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[private-link-service]: https://docs.microsoft.com/azure/private-link/private-link-service-overview
[virtual-network-peering]: ../virtual-network/virtual-network-peering-overview.md

