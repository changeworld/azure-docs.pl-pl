---
title: Serwera interfejsu API autoryzowane zakresy adresów IP w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jak zabezpieczyć klaster przy użyciu zakresu adresów IP, aby uzyskać dostęp do serwera interfejsu API w usłudze Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/06/2019
ms.author: iainfou
ms.openlocfilehash: d425fc3e037b43321a278fb16781e86dfafef15c
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67441525"
---
# <a name="preview---secure-access-to-the-api-server-using-authorized-ip-address-ranges-in-azure-kubernetes-service-aks"></a>(Wersja zapoznawcza) — bezpieczny dostęp do serwera interfejsu API przy użyciu autoryzowane zakresy adresów IP w usłudze Azure Kubernetes Service (AKS)

W usłudze Kubernetes serwera interfejsu API odbiera żądania do wykonywania akcji w klastrze, takich jak tworzenie zasobów lub skalować liczbę węzłów. Serwer interfejs API jest centralnym sposób interakcji z klastra i zarządzanie nim. Aby zwiększyć zabezpieczenia klastra i zminimalizować ataków, serwera interfejsu API tylko powinny być dostępne z określonych zakresów adresów IP.

Ten artykuł pokazuje, jak używać zakresów adresów IP serwera autoryzacji interfejsu API w celu ograniczenia żądań na płaszczyźnie kontroli. Ta funkcja jest obecnie dostępna w wersji zapoznawczej.

> [!IMPORTANT]
> Funkcje w wersji zapoznawczej usługi AKS są samoobsługi, uczestnictwo. Są one udostępniane do zbierania opinii i błędy z naszej społeczności. W wersji zapoznawczej te funkcje nie są przeznaczone do użytku produkcyjnego. Funkcje w wersji zapoznawczej objęte "starań" pomocy technicznej. Pomoc od zespołów pomocy technicznej usługi AKS jest dostępna w godzinach pracy i Pacyfik, część strefy czasowej (PST) tylko. Aby uzyskać więcej informacji zobacz następujące artykuły pomocy technicznej:
>
> * [Zasady pomocy technicznej usługi AKS][aks-support-policies]
> * [Pomoc techniczna platformy Azure — często zadawane pytania][aks-faq]

## <a name="before-you-begin"></a>Przed rozpoczęciem

Serwera interfejsu API używać tylko w przypadku nowych klastrów usługi AKS, które tworzysz zakresów adresów IP autoryzowanych. W tym artykule pokazano, jak utworzyć klaster usługi AKS przy użyciu wiersza polecenia platformy Azure.

Potrzebujesz wiersza polecenia platformy Azure w wersji 2.0.61 lub później zainstalowane i skonfigurowane. Uruchom polecenie  `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli potrzebujesz instalacja lub uaktualnienie, zobacz [interfejsu wiersza polecenia platformy Azure Zainstaluj][install-azure-cli].

### <a name="install-aks-preview-cli-extension"></a>Zainstaluj rozszerzenie interfejsu wiersza polecenia w wersji zapoznawczej usługi aks

Aby skonfigurować zakresy adresów IP serwera autoryzacji interfejsu API, musisz mieć *podglądu usługi aks* interfejsu wiersza polecenia wersja rozszerzenia 0.4.1 lub nowszej. Zainstaluj *podglądu usługi aks* rozszerzenie interfejsu wiersza polecenia platformy Azure przy użyciu polecenia [Dodaj rozszerzenie az][az-extension-add] command, then check for any available updates using the [az extension update][az-extension-update] polecenia:

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-feature-flag-for-your-subscription"></a>Zarejestruj flagę funkcji dla Twojej subskrypcji

Używanie interfejsu API serwera autoryzowane zakresy adresów IP, należy najpierw włączyć flagi funkcji w ramach Twojej subskrypcji. Aby zarejestrować *APIServerSecurityPreview* flagę funkcji, należy użyć [az feature register][az-feature-register] polecenia, jak pokazano w poniższym przykładzie:

> [!CAUTION]
> Po zarejestrowaniu funkcji w ramach subskrypcji, nie można obecnie wyrejestrować tę funkcję. Po włączeniu niektóre funkcje w wersji zapoznawczej, wartości domyślne mogą służyć dla wszystkich klastrów usługi AKS, które następnie są tworzone w ramach subskrypcji. Nie włączaj funkcji w wersji zapoznawczej w ramach subskrypcji w środowisku produkcyjnym. Aby przetestować funkcje w wersji zapoznawczej i zbieranie opinii, należy użyć oddzielnej subskrypcji.

```azurecli-interactive
az feature register --name APIServerSecurityPreview --namespace Microsoft.ContainerService
```

Zajmuje kilka minut, zanim stan wyświetlany *zarejestrowanej*. Można sprawdzić stan rejestracji przy użyciu [lista funkcji az][az-feature-list] polecenia:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/APIServerSecurityPreview')].{Name:name,State:properties.state}"
```

Gdy wszystko będzie gotowe, Odśwież rejestracji *Microsoft.ContainerService* dostawcę zasobów przy użyciu [az provider register][az-provider-register] polecenia:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="limitations"></a>Ograniczenia

Podczas konfigurowania zakresów adresów IP serwera autoryzacji interfejsu API, obowiązują następujące ograniczenia:

* Obecnie nie można używać usługi Azure Dev miejsca do magazynowania, zgodnie z komunikacji z serwerem interfejsu API także jest blokowana.

## <a name="overview-of-api-server-authorized-ip-ranges"></a>Omówienie serwera interfejsu API autoryzowane zakresy adresów IP

Serwer interfejsu API rozwiązania Kubernetes jest o tym, jak podstawowych interfejsów API rozwiązania Kubernetes są widoczne. Ten składnik zapewnia interakcji dla narzędzia do zarządzania, takich jak `kubectl` lub pulpit nawigacyjny platformy Kubernetes. AKS zapewnia głównego klastra pojedynczej dzierżawy, za pomocą dedykowanego serwera interfejsu API. Domyślnie serwera interfejsu API jest przypisany publiczny adres IP, a powinien mieć kontrolę dostępu przy użyciu kontroli dostępu opartej na rolach (RBAC).

Aby zabezpieczyć dostęp do warstwą kontroli w przeciwnym razie jest dostępny publicznie AKS / serwera interfejsu API, można włączyć i używać autoryzowane zakresy adresów IP. Te zakresy adresów IP autoryzowanych Zezwalaj tylko na określonych zakresów adresów IP do komunikacji z serwerem interfejsu API. Żądanie skierowane do serwera interfejsu API z adresu IP, który nie jest częścią te zakresy adresów IP autoryzowanych jest zablokowany. Można nadal następnie autoryzować użytkowników i akcje, które żądają przy użyciu kontroli RBAC.

Aby uzyskać więcej informacji na temat serwera interfejsu API i inne składniki klastra, zobacz [Kubernetes podstawowe pojęcia dla usługi AKS][concepts-clusters-workloads].

## <a name="create-an-aks-cluster"></a>Tworzenie klastra AKS

Adres IP serwera, o których uprawnień interfejsu API zakresy działają tylko w przypadku nowych klastrów usługi AKS. Nie można włączyć autoryzowane zakresy adresów IP, ponieważ operacja tworzenia częścią klastra. Jeśli zostanie podjęta próba włączenia autoryzowane zakresy adresów IP w ramach klastra utworzyć procesu, węzły klastra są w stanie uzyskać dostępu do serwera interfejsu API podczas wdrażania, jak adres IP ruchu wychodzącego nie jest zdefiniowany w tym momencie.

Najpierw Utwórz klaster przy użyciu [tworzenie az aks][az-aks-create] polecenia. W poniższym przykładzie tworzony jest klaster jednowęzłowy, o nazwie *myAKSCluster* w grupie zasobów o nazwie *myResourceGroup*.

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

## <a name="create-outbound-gateway-for-firewall-rules"></a>Tworzenie bramy ruchu wychodzącego dla reguł zapory

Aby upewnić się, że węzłów w klastrze może niezawodnie komunikować się z serwera interfejsu API po włączeniu autoryzowane zakresy adresów IP w następnej sekcji, należy utworzyć zaporę platformy Azure do użycia jako brama ruchu wychodzącego. Adres IP zapory platformy Azure jest dodawane do listy interfejsu API adresów IP autoryzowanych serwerów w następnej sekcji.

> [!WARNING]
> Korzystanie z zapory usługi Azure mogą być naliczane znacznych kosztów miesięcznego cyklu rozliczeniowego. Wymóg dotyczący używania zapory usługi Azure powinny być tylko niezbędne w tym okresie początkowego okresu zapoznawczego. Aby uzyskać więcej informacji i planowanie kosztów, zobacz [zapory usługi Azure, cennik][azure-firewall-costs].

Najpierw pobierz *MC_* nazwy grupy zasobów klastra usługi AKS i sieci wirtualnej. Następnie utwórz podsieci przy użyciu [az podsieci sieci wirtualnej Utwórz][az-network-vnet-subnet-create] polecenia. Poniższy przykład tworzy podsieć o nazwie *AzureFirewallSubnet* zakres CIDR *10.200.0.0/16*:

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

Aby utworzyć zaporę na platformie Azure, instalowanie *zapory usługi azure* rozszerzenie interfejsu wiersza polecenia przy użyciu polecenia [Dodaj rozszerzenie az][az-extension-add] command. Then, create a firewall using the [az network firewall create][az-network-firewall-create] polecenia. Poniższy przykład tworzy zaporę platformy Azure o nazwie *myAzureFirewall*:

```azurecli-interactive
# Install the CLI extension for Azure Firewall
az extension add --name azure-firewall

# Create an Azure firewall
az network firewall create \
    --resource-group $MC_RESOURCE_GROUP\
    --name myAzureFirewall
```

Zaporę platformy Azure jest przypisany publiczny adres IP, który ruch wychodzący przepływów za pomocą. Tworzenie przy użyciu publicznego adresu [tworzenie sieci az public-ip][az-network-public-ip-create] command, then create an IP configuration on the firewall using the [az network firewall ip-config create][az-network-firewall-ip-config-create] stosująca publiczny adres IP:

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

Teraz utworzyć regułę sieciowej zapory platformy Azure, która *Zezwalaj* wszystkich *TCP* ruchu przy użyciu [tworzenie az sieci reguła sieciowej][az-network-firewall-network-rule-create] polecenia. Poniższy przykład tworzy regułę sieciowej o nazwie *AllowTCPOutbound* ruchu przy użyciu dowolnego adresu źródłowego lub docelowego:

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

Aby skojarzyć zaporę platformy Azure przy użyciu trasy sieci, uzyskać informacje o istniejącym tabeli tras, wewnętrzny adres IP zapory platformy Azure i adres IP serwera interfejsu API. Te adresy IP są określone w następnej sekcji, aby kontrolować, jak ruch powinien być kierowany do komunikacji klastra.

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

Na koniec utwórz trasę w istniejącej usłudze AKS sieci trasy tabeli przy użyciu [tworzenie az sieci route-table route][az-network-route-table-route-create] polecenia, która zezwala na ruch do użycia urządzenia zapory platformy Azure na potrzeby komunikacji z serwerem interfejsu API.

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

Zanotuj publiczny adres IP urządzenia zapory usługi Azure. Ten adres jest dodawany do listy zakresów adresów IP serwera autoryzacji interfejsu API w następnej sekcji.

## <a name="enable-authorized-ip-ranges"></a>Włącz autoryzowane zakresy adresów IP

Aby włączyć zakresów adresów IP serwera autoryzacji interfejsu API, musisz podać listę autoryzowanych zakresów adresów IP. Podczas określania zakresu CIDR rozpoczynać od pierwszego adresu IP z zakresu. Na przykład *137.117.106.90/29* jest prawidłowym zakresem, ale upewnij się, że możesz określić pierwszy adres IP z zakresu, takie jak *137.117.106.88/29*.

Użyj [aktualizacji az aks][az-aks-update] polecenia i określić *— interfejs api —-autoryzacji — — zakresy adresów ip serwerów* umożliwia. Te zakresy adresów IP są zwykle zakresami adresów używanymi przez sieci w środowisku lokalnym. Dodaj publiczny adres IP platformy Azure zapory uzyskanego w poprzednim kroku, takie jak *20.42.25.196/32*.

Poniższy przykład umożliwia zakresy adresów IP serwera autoryzacji interfejsu API w klastrze o nazwie *myAKSCluster* w grupie zasobów o nazwie *myResourceGroup*. Zakresy adresów IP, aby autoryzować to *20.42.25.196/32* (Zapora usługi Azure publiczny adres IP), następnie *172.0.0.10/16* i *168.10.0.10/18*:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges 20.42.25.196/32,172.0.0.10/16,168.10.0.10/18
```

## <a name="update-or-disable-authorized-ip-ranges"></a>Zaktualizuj lub wyłącz autoryzowane zakresy adresów IP

Aby zaktualizować lub wyłączyć zakresów adresów IP autoryzowanych, należy ponownie użyć [aktualizacji az aks][az-aks-update] polecenia. Określ zaktualizowano zakres CIDR, które chcesz zezwolić lub określić, że pustego zakresu, aby wyłączyć serwera interfejsu API uprawnienia zakresów adresów IP, jak pokazano w poniższym przykładzie:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges ""
```

## <a name="next-steps"></a>Kolejne kroki

W tym artykule został włączony zakresów adresów IP serwera autoryzacji interfejsu API. To podejście jest jedną z części pakietu działanie z bezpiecznym klastrem AKS.

Aby uzyskać więcej informacji, zobacz [pojęcia dotyczące zabezpieczeń dla aplikacji i klastrów w usłudze AKS][concepts-security] and [Best practices for cluster security and upgrades in AKS][operator-best-practices-cluster-security].

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
