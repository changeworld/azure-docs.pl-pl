---
title: Zakresy adresów IP autoryzowanych serwerów interfejsu API w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jak zabezpieczyć klaster przy użyciu zakresu adresów IP na potrzeby dostępu do serwera interfejsu API w usłudze Azure Kubernetes Service (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/06/2019
ms.author: mlearned
ms.openlocfilehash: 6516bbcb4ea879279812d61d9fe31f1ea4268280
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "67616255"
---
# <a name="preview---secure-access-to-the-api-server-using-authorized-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Wersja zapoznawcza — bezpieczny dostęp do serwera interfejsu API przy użyciu zakresów autoryzowanych adresów IP w usłudze Azure Kubernetes Service (AKS)

W programie Kubernetes serwer interfejsu API odbiera żądania wykonania akcji w klastrze, takie jak tworzenie zasobów lub skalowanie liczby węzłów. Serwer interfejsu API jest centralną metodą współdziałania z klastrem i zarządzania nim. Aby zwiększyć bezpieczeństwo klastra i zminimalizować ataki, serwer interfejsu API powinien być dostępny tylko z ograniczonym zestawem zakresów adresów IP.

W tym artykule pokazano, jak używać zakresów adresów IP autoryzowanych przez serwer API do ograniczania żądań do płaszczyzny kontroli. Ta funkcja jest obecnie dostępna w wersji zapoznawczej.

> [!IMPORTANT]
> Funkcje AKS w wersji zapoznawczej to samoobsługowe i niezgodne. Są one dostarczane w celu zebrania opinii i usterek z naszej społeczności. W wersji zapoznawczej te funkcje nie są przeznaczone do użytku produkcyjnego. Funkcje w publicznej wersji zapoznawczej są objęte wsparciem "najlepsze wysiłki". Pomoc dla zespołów pomocy technicznej AKS jest dostępna w godzinach pracy w czasie pracy (PST). Dodatkowe informacje można znaleźć w następujących artykułach pomocy technicznej:
>
> * [Zasady pomocy technicznej AKS][aks-support-policies]
> * [Pomoc techniczna platformy Azure — często zadawane pytania][aks-faq]

## <a name="before-you-begin"></a>Przed rozpoczęciem

Zakres adresów IP autoryzowanych przez serwer API działa tylko w przypadku nowych klastrów AKS utworzonych przez Ciebie. W tym artykule opisano sposób tworzenia klastra AKS przy użyciu interfejsu wiersza polecenia platformy Azure.

Wymagany jest interfejs wiersza polecenia platformy Azure w wersji 2.0.61 lub nowszej. Uruchom polecenie  `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne jest zainstalowanie lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

### <a name="install-aks-preview-cli-extension"></a>Zainstaluj rozszerzenie interfejsu wiersza polecenia AKS-Preview

Aby skonfigurować zakres adresów IP autoryzowanych przez serwer interfejsu API, musisz mieć rozszerzenie interfejsu wiersza polecenia *AKS-Preview* w wersji 0.4.1 lub nowszej. Zainstaluj rozszerzenie interfejsu wiersza polecenia platformy Azure w *wersji* zapoznawczej przy użyciu poleceń [AZ Extension Add][az-extension-add] , a następnie wyszukaj wszystkie dostępne aktualizacje za pomocą polecenia [AZ Extension Update][az-extension-update] :

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-feature-flag-for-your-subscription"></a>Zarejestruj flagę funkcji dla subskrypcji

Aby korzystać z autoryzowanych zakresów adresów IP serwera interfejsu API, należy najpierw włączyć flagę funkcji w subskrypcji. Aby zarejestrować flagę funkcji *APIServerSecurityPreview* , użyj polecenia [AZ Feature Register][az-feature-register] , jak pokazano w następującym przykładzie:

> [!CAUTION]
> Po zarejestrowaniu funkcji w ramach subskrypcji nie można obecnie wyrejestrować tej funkcji. Po włączeniu niektórych funkcji w wersji zapoznawczej można użyć wartości domyślnych dla wszystkich klastrów AKS utworzonych w ramach subskrypcji. Nie włączaj funkcji w wersji zapoznawczej w ramach subskrypcji produkcyjnych. Korzystaj z oddzielnej subskrypcji, aby testować funkcje w wersji zapoznawczej i zbierać opinie.

```azurecli-interactive
az feature register --name APIServerSecurityPreview --namespace Microsoft.ContainerService
```

Wyświetlenie stanu *rejestracji*może potrwać kilka minut. Stan rejestracji można sprawdzić za pomocą polecenia [AZ Feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/APIServerSecurityPreview')].{Name:name,State:properties.state}"
```

Gdy wszystko będzie gotowe, Odśwież rejestrację dostawcy zasobów *Microsoft. ContainerService* za pomocą polecenia [AZ Provider Register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="limitations"></a>Ograniczenia

Podczas konfigurowania zakresów autoryzowanych adresów IP serwera interfejsu API obowiązują następujące ograniczenia:

* Obecnie nie można używać Azure Dev Spaces, ponieważ komunikacja z serwerem interfejsu API jest również blokowana.

## <a name="overview-of-api-server-authorized-ip-ranges"></a>Przegląd zakresów adresów IP autoryzowanych serwerów interfejsu API

Serwer interfejsu API Kubernetes to sposób ujawniania podstawowych interfejsów API Kubernetes. Ten składnik zapewnia interakcję z narzędziami do zarządzania, `kubectl` takimi jak lub pulpitem nawigacyjnym Kubernetes. AKS zapewnia jednodostępny wzorzec klastra z dedykowanym serwerem interfejsu API. Domyślnie serwer interfejsu API ma przypisany publiczny adres IP i należy kontrolować dostęp przy użyciu kontroli dostępu opartej na rolach (RBAC).

Aby zabezpieczyć dostęp do dostępnego publicznie AKS kontroli/serwera interfejsu API, można włączyć i używać autoryzowanych zakresów adresów IP. Te autoryzowane zakresy adresów IP zezwalają na komunikowanie się z serwerem interfejsu API tylko zdefiniowanym zakresom IP. Żądanie wysłane do serwera interfejsu API z adresu IP, który nie jest częścią tych autoryzowanych zakresów adresów IP, jest blokowane. Należy nadal używać RBAC, aby autoryzować użytkowników i akcje, które żądają.

Aby uzyskać więcej informacji na temat serwera interfejsu API i innych składników klastra, zobacz [Kubernetes podstawowe pojęcia dotyczące AKS][concepts-clusters-workloads].

## <a name="create-an-aks-cluster"></a>Tworzenie klastra AKS

Zakres adresów IP autoryzowanych przez serwer interfejsu API działa tylko w przypadku nowych klastrów AKS. Nie można włączyć autoryzowanych zakresów adresów IP w ramach operacji tworzenia klastra. Jeśli spróbujesz włączyć autoryzowane zakresy adresów IP w ramach procesu tworzenia klastra, węzły klastra nie będą mogły uzyskać dostępu do serwera interfejsu API podczas wdrażania, ponieważ w tym punkcie nie jest zdefiniowany adres IP ruchu wychodzącego.

Najpierw utwórz klaster przy użyciu polecenia [AZ AKS Create][az-aks-create] . Poniższy przykład tworzy klaster z jednym węzłem o nazwie *myAKSCluster* w grupie zasobów o nazwiemoja ResourceName.

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location eastus

# Create an AKS cluster
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --generate-ssh-keys

# Get credentials to access the cluster
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="create-outbound-gateway-for-firewall-rules"></a>Utwórz bramę wychodzącą dla reguł zapory

Aby upewnić się, że węzły w klastrze mogą niezawodnie komunikować się z serwerem interfejsu API po włączeniu dozwolonych zakresów adresów IP w następnej sekcji, należy utworzyć zaporę platformy Azure do użycia jako bramę wychodzącą. Adres IP zapory platformy Azure zostanie następnie dodany do listy autoryzowanych adresów IP serwerów interfejsu API w następnej sekcji.

> [!WARNING]
> Korzystanie z zapory platformy Azure może wiązać się ze znacznymi kosztami w ramach miesięcznego cyklu rozliczeniowego. Wymóg używania zapory platformy Azure powinien być konieczny tylko w tym okresie wstępnej wersji zapoznawczej. Aby uzyskać więcej informacji i planowanie kosztów, zobacz [Cennik usługi Azure firewall][azure-firewall-costs].

Najpierw Pobierz nazwę grupy zasobów *MC_* dla klastra AKS i sieci wirtualnej. Następnie Utwórz podsieć za pomocą polecenia [AZ Network VNET Subnet Create][az-network-vnet-subnet-create] . Poniższy przykład tworzy podsieć o nazwie *AzureFirewallSubnet* z zakresem CIDR *10.200.0.0/16*:

```azurecli-interactive
# Get the name of the MC_ cluster resource group
MC_RESOURCE_GROUP=$(az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --query nodeResourceGroup -o tsv)

# Get the name of the virtual network used by the cluster
VNET_NAME=$(az network vnet list \
    --resource-group $MC_RESOURCE_GROUP \
    --query [0].name -o tsv)

# Create a subnet in the virtual network for Azure Firewall
az network vnet subnet create \
    --resource-group $MC_RESOURCE_GROUP \
    --vnet-name $VNET_NAME \
    --name AzureFirewallSubnet \
    --address-prefixes 10.200.0.0/16
```

Aby utworzyć zaporę platformy Azure, zainstaluj rozszerzenie interfejsu wiersza polecenia *platformy Azure dla zapory* za pomocą poleceń [AZ Extension Add][az-extension-add] . Następnie utwórz zaporę za pomocą polecenia [AZ Network firewall Create][az-network-firewall-create] . Poniższy przykład tworzy zaporę platformy Azure o nazwie *myAzureFirewall*:

```azurecli-interactive
# Install the CLI extension for Azure Firewall
az extension add --name azure-firewall

# Create an Azure firewall
az network firewall create \
    --resource-group $MC_RESOURCE_GROUP\
    --name myAzureFirewall
```

Zapora platformy Azure ma przypisany publiczny adres IP, za pomocą którego ruch wychodzący przechodzi przez. Utwórz adres publiczny za pomocą polecenia [AZ Network Public-IP Create][az-network-public-ip-create] , a następnie utwórz konfigurację adresu IP w zaporze przy użyciu opcji [AZ Network firewall IP-config Create][az-network-firewall-ip-config-create] , która stosuje publiczny adres IP:

```azurecli-interactive
# Create a public IP address for the firewall
FIREWALL_PUBLIC_IP=$(az network public-ip create \
    --resource-group $MC_RESOURCE_GROUP \
    --name myAzureFirewallPublicIP \
    --sku Standard \
    --query publicIp.ipAddress -o tsv)

# Associated the firewall with virtual network
az network firewall ip-config create \
    --resource-group $MC_RESOURCE_GROUP \
    --name myAzureFirewallIPConfig \
    --vnet-name $VNET_NAME \
    --firewall-name myAzureFirewall \
    --public-ip-address myAzureFirewallPublicIP
```

Teraz Utwórz regułę sieci zapory platformy Azure, aby *zezwolić na* cały ruch *TCP* przy użyciu polecenia [AZ Network firewall Network-Rule Create][az-network-firewall-network-rule-create] . Poniższy przykład tworzy regułę sieci o nazwie *AllowTCPOutbound* dla ruchu z dowolnym adresem źródłowym lub docelowym:

```azurecli-interactive
az network firewall network-rule create \
    --resource-group $MC_RESOURCE_GROUP \
    --firewall-name myAzureFirewall \
    --name AllowTCPOutbound \
    --collection-name myAzureFirewallCollection \
    --priority 200 \
    --action Allow \
    --protocols TCP \
    --source-addresses '*' \
    --destination-addresses '*' \
    --destination-ports '*'
```

Aby skojarzyć zaporę platformy Azure z trasą sieciową, uzyskaj istniejące informacje tabeli tras, wewnętrzny adres IP zapory platformy Azure, a następnie adres IP serwera interfejsu API. Te adresy IP są określone w następnej sekcji, aby określić, jak ruch powinien być kierowany do komunikacji klastra.

```azurecli-interactive
# Get the AKS cluster route table
ROUTE_TABLE=$(az network route-table list \
    --resource-group $MC_RESOURCE_GROUP \
    --query "[?contains(name,'agentpool')].name" -o tsv)

# Get internal IP address of the firewall
FIREWALL_INTERNAL_IP=$(az network firewall show \
    --resource-group $MC_RESOURCE_GROUP \
    --name myAzureFirewall \
    --query ipConfigurations[0].privateIpAddress -o tsv)

# Get the IP address of API server endpoint
K8S_ENDPOINT_IP=$(kubectl get endpoints -o=jsonpath='{.items[?(@.metadata.name == "kubernetes")].subsets[].addresses[].ip}')
```

Na koniec Utwórz trasę w istniejącej tabeli tras sieciowych AKS za pomocą polecenia [AZ Network Route-Table Route Create][az-network-route-table-route-create] , które zezwala na ruch do korzystania z urządzenia zapory platformy Azure do komunikacji z serwerem interfejsu API.

```azurecli-interactive
az network route-table route create \
    --resource-group $MC_RESOURCE_GROUP \
    --route-table-name $ROUTE_TABLE \
    --name AzureFirewallAPIServer \
    --address-prefix $K8S_ENDPOINT_IP/32 \
    --next-hop-ip-address $FIREWALL_INTERNAL_IP \
    --next-hop-type VirtualAppliance

echo "Public IP address for the Azure Firewall instance that should be added to the list of API server authorized addresses is:" $FIREWALL_PUBLIC_IP
```

Zanotuj publiczny adres IP urządzenia zapory platformy Azure. Ten adres jest dodawany do listy dozwolonych zakresów adresów IP serwera interfejsu API w następnej sekcji.

## <a name="enable-authorized-ip-ranges"></a>Włącz dozwolone zakresy adresów IP

Aby włączyć zakres adresów IP autoryzowanych przez serwer interfejsu API, należy podać listę autoryzowanych zakresów adresów IP. Po określeniu zakresu CIDR, należy zacząć od pierwszego adresu IP z zakresu. Na przykład *137.117.106.90/29* jest prawidłowym zakresem, ale należy określić pierwszy adres IP z zakresu, na przykład *137.117.106.88/29*.

Użyj polecenia [AZ AKS Update][az-aks-update] i określ *zakresy--API-Server-autoryzowane-IP-* allows, aby zezwolić. Te zakresy adresów IP są zwykle zakresami adresów używanymi przez sieci lokalne. Dodaj publiczny adres IP własnej zapory platformy Azure uzyskany w poprzednim kroku, na przykład *20.42.25.196/32*.

Poniższy przykład umożliwia włączenie zakresów adresów IP autoryzowanych serwerów interfejsu API w klastrze o nazwie *myAKSCluster* w grupie zasobów o nazwie Moja *resourceName*. Zakresy adresów IP do autoryzacji to *20.42.25.196/32* (publiczny adres IP zapory platformy Azure), a następnie *172.0.0.10/16* i *168.10.0.10/18*:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges 20.42.25.196/32,172.0.0.10/16,168.10.0.10/18
```

## <a name="update-or-disable-authorized-ip-ranges"></a>Aktualizowanie lub wyłączanie autoryzowanych zakresów adresów IP

Aby zaktualizować lub wyłączyć autoryzowane zakresy adresów IP, należy ponownie użyć polecenia [AZ AKS Update][az-aks-update] . Określ zaktualizowany zakres CIDR lub określ pusty zakres, aby wyłączyć autoryzowane zakresy adresów IP serwera interfejsu API, jak pokazano w następującym przykładzie:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges ""
```

## <a name="next-steps"></a>Kolejne kroki

W tym artykule włączono zakres adresów IP autoryzowanych przez serwer interfejsu API. To podejście jest jedną z metod uruchomienia bezpiecznego klastra AKS.

Aby uzyskać więcej informacji, zobacz [pojęcia dotyczące zabezpieczeń aplikacji i klastrów w AKS][concepts-security] i [najlepszych rozwiązaniach dotyczących zabezpieczeń klastrów i uaktualnień w AKS][operator-best-practices-cluster-security].

<!-- LINKS - external -->
[azure-firewall-costs]: https://azure.microsoft.com/pricing/details/azure-firewall/

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[concepts-security]: concepts-security.md
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[create-aks-sp]: kubernetes-service-principal.md#manually-create-a-service-principal
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-create
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-network-firewall-create]: /cli/azure/ext/azure-firewall/network/firewall#ext-azure-firewall-az-network-firewall-create
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-firewall-ip-config-create]: /cli/azure/ext/azure-firewall/network/firewall/ip-config#ext-azure-firewall-az-network-firewall-ip-config-create
[az-network-firewall-network-rule-create]: /cli/azure/ext/azure-firewall/network/firewall/network-rule#ext-azure-firewall-az-network-firewall-network-rule-create
[az-network-route-table-route-create]: /cli/azure/network/route-table/route#az-network-route-table-route-create
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
