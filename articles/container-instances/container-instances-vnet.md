---
title: Wdrażanie wystąpień kontenerów w sieci wirtualnej platformy Azure
description: Dowiedz się, jak wdrożyć grup kontenerów do nowej lub istniejącej sieci wirtualnej platformy Azure.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 03/26/2019
ms.author: danlep
ms.openlocfilehash: a4da7a23d6dcb50164829507130fed145abeebbd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60684222"
---
# <a name="deploy-container-instances-into-an-azure-virtual-network"></a>Wdrażanie wystąpień kontenerów w sieci wirtualnej platformy Azure

[Usługa Azure Virtual Network](../virtual-network/virtual-networks-overview.md) zapewnia bezpieczny, prywatnych sieci na potrzeby subskrypcji platformy Azure i zasobów lokalnych. Wdrażając grup kontenerów w sieci wirtualnej platformy Azure, kontenery można bezpiecznego komunikowania się z innymi zasobami w sieci wirtualnej.

Grupy kontenerów wdrożonych w sieci wirtualnej platformy Azure obsługi takich scenariuszy jak:

* Bezpośrednia komunikacja między grupami kontenerów w tej samej podsieci
* Wyślij [opartego na zadaniach](container-instances-restart-policy.md) obciążenia dane wyjściowe z wystąpienia kontenera, do bazy danych w sieci wirtualnej
* Pobieranie zawartości dla wystąpienia kontenera z [punktu końcowego usługi](../virtual-network/virtual-network-service-endpoints-overview.md) w sieci wirtualnej
* Kontener komunikacji z maszynami wirtualnymi w sieci wirtualnej
* Kontener komunikację z zasobami lokalnymi za pośrednictwem [bramy sieci VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) lub [usługi ExpressRoute](../expressroute/expressroute-introduction.md)

> [!IMPORTANT]
> Ta funkcja jest obecnie dostępna w wersji zapoznawczej, a niektóre [ograniczenia](#preview-limitations). Wersje zapoznawcze są udostępniane pod warunkiem udzielenia zgody na [dodatkowe warunki użytkowania][terms-of-use]. Niektóre cechy funkcji mogą ulec zmianie, zanim stanie się ona ogólnie dostępna.

## <a name="virtual-network-deployment-limitations"></a>Ograniczenia wdrożenia sieci wirtualnej

Niektóre ograniczenia mają zastosowanie w przypadku wdrażania grup kontenerów z siecią wirtualną.

* Aby wdrożyć grup kontenerów do podsieci, podsieci nie może zawierać innych typów zasobów. Usuń wszystkie istniejące zasoby z istniejącej podsieci przed wdrożeniem grup kontenerów do niej, lub Utwórz nową podsieć.
* Nie można użyć [tożsamości zarządzanej](container-instances-managed-identity.md) w grupie kontenerów wdrożonych w sieci wirtualnej.
* Ze względu na dodatkowe zaangażowanych zasobów sieciowych wdrożenie grupy kontenera z siecią wirtualną jest zazwyczaj trwa nieco dłużej niż wdrażanie wystąpienia standard kontenera.

## <a name="preview-limitations"></a>Ograniczenia wersji zapoznawczej

Chociaż ta funkcja jest dostępna w wersji zapoznawczej, podlegają następującym ograniczeniom podczas wdrażania grup kontenerów z siecią wirtualną. 

[!INCLUDE [container-instances-vnet-limits](../../includes/container-instances-vnet-limits.md)]

Ograniczenia zasobów kontenera, może różnić się od limity wystąpień kontenerów obsługi sieci w tych regionach. Obecnie kontenery systemu Linux są obsługiwane dla tej funkcji. Planowane jest wprowadzenie obsługi w systemie Windows.

### <a name="unsupported-networking-scenarios"></a>Nieobsługiwane scenariusze sieci 

* **Usługa Azure Load Balancer** — wprowadzenie do usługi Azure Load Balancer przed wystąpienia kontenerów w grupie kontenerów sieciowych nie jest obsługiwane.
* **Wirtualne sieci równorzędne** — nie komunikacja równorzędna sieć wirtualna, zawierający podsieci delegowanie do usługi Azure Container Instances do innej sieci wirtualnej
* **Tabele tras** — nie można ich konfigurować trasy zdefiniowane przez użytkownika w podsieci delegować domenę do usługi Azure Container Instances
* **Sieciowe grupy zabezpieczeń** -reguły zabezpieczeń dla ruchu wychodzącego w NSG stosowana do podsieci delegować domenę do usługi Azure Container Instances obecnie nie są wymuszane. 
* **Publiczny adres IP lub DNS etykiety** -grup kontenerów wdrożonych w sieci wirtualnej, obecnie nie obsługują uwidaczniającą kontenerów bezpośrednio w Internecie przy użyciu publicznego adresu IP lub w pełni kwalifikowanej nazwy domeny
* **Rozpoznawania nazw wewnętrznych** — rozpoznawanie nazw dla zasobów platformy Azure w sieci wirtualnej za pomocą wewnętrznego serwera DNS platformy Azure nie jest obsługiwana.

**Usunięcie zasobu sieciowego** wymaga [dodatkowe kroki](#delete-network-resources) po wdrożeniu grupy kontenerów w sieci wirtualnej.

## <a name="required-network-resources"></a>Wymaganych zasobów sieciowych

Istnieją trzy zasoby usługi Azure Virtual Network, wymaganą do wdrożenia grup kontenerów w sieci wirtualnej: [sieci wirtualnej](#virtual-network) , [delegowane podsieci](#subnet-delegated) w ramach sieci wirtualnej i [sieci profilu](#network-profile). 

### <a name="virtual-network"></a>Sieć wirtualna

Sieć wirtualna definiuje przestrzeń adresową, w którym należy utworzyć co najmniej jednej podsieci. Następnie Wdrażaj zasoby platformy Azure (np. grupy kontenerów) do podsieci w sieci wirtualnej.

### <a name="subnet-delegated"></a>Podsieć (delegowany)

Podsieci sieci wirtualnej na segmenty na oddzielne przestrzenie adresowe można używać przez zasoby platformy Azure, które można umieścić w nich. Możesz utworzyć jeden lub kilka podsieci w sieci wirtualnej.

Podsieci, której użyjesz dla grupy kontenerów może zawierać tylko grupy kontenerów. Przy pierwszym wdrożeniu grupy kontenerów do podsieci, Azure deleguje tej podsieci do usługi Azure Container Instances. Gdy delegowana, podsieć może służyć tylko dla grupy kontenerów. Jeśli spróbujesz do wdrażania zasobów innych niż grup kontenerów delegowanego podsieci, kończy się niepowodzeniem.

### <a name="network-profile"></a>Profil sieciowy

Profil sieciowy jest szablonu konfiguracji sieci dla zasobów platformy Azure. Określa niektóre właściwości sieciowe dla zasobu, na przykład podsieci, do którego powinny zostać wdrożone. Po raz pierwszy używasz [utworzyć kontener az] [ az-container-create] polecenia, aby wdrożyć grupę kontenerów do podsieci (i w związku z tym sieci wirtualnej), platforma Azure tworzy profil sieciowy dla Ciebie. Można następnie użyć tego profilu sieci do przyszłych wdrożeń do podsieci. 

Aby użyć szablonu usługi Resource Manager, plik YAML lub programistycznej metody wdrażania grupy kontenerów do podsieci, musisz podać pełny identyfikator zasobu usługi Resource Manager profil sieciowy. Można użyć profil, który został wcześniej utworzony przy użyciu [utworzyć kontener az][az-container-create], lub Utwórz profil przy użyciu szablonu usługi Resource Manager (zobacz [przykładowy szablon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet) i [odwołanie](https://docs.microsoft.com/azure/templates/microsoft.network/networkprofiles)). Aby uzyskać identyfikator utworzony uprzednio profil, należy użyć [Lista profilów sieciowych az] [ az-network-profile-list] polecenia. 

Na poniższym diagramie kilka grup kontenerów zostały wdrożone do podsieci delegować domenę do usługi Azure Container Instances. Po wdrożeniu jedną grupę kontenerów do podsieci, można wdrożyć dodatkowe kontenera grupy do niej, określając ten sam profil sieci.

![Grupy kontenerów w ramach sieci wirtualnej][aci-vnet-01]

## <a name="deployment-scenarios"></a>Scenariusze wdrażania

Możesz użyć [utworzyć kontener az] [ az-container-create] wdrażanie grup kontenerów do nowej sieci wirtualnej i zezwolić na platformie Azure, aby utworzyć wymaganych zasobów sieciowych dla Ciebie, lub Wdróż do istniejącej sieci wirtualnej. 

### <a name="new-virtual-network"></a>Nową sieć wirtualną

Aby wdrożyć nową sieć wirtualną, a platforma Azure automatycznie tworzenie zasobów sieciowych dla Ciebie, podaj następujące informacje podczas wykonywania [utworzyć kontener az][az-container-create]:

* Nazwa sieci wirtualnej
* Prefiks adresu sieci wirtualnej w formacie CIDR
* Nazwa podsieci
* Prefiks adresu podsieci w formacie CIDR

Sieć wirtualną i podsieć prefiksy adresów przestrzeni adresów dla sieci wirtualnej i podsieci, należy określić odpowiednio. Te wartości są reprezentowane w notacji Bezklasowego routingu międzydomenowego (CIDR), na przykład `10.0.0.0/16`. Aby uzyskać więcej informacji na temat pracy z podsieciami, zobacz [Dodawanie, zmienianie lub usuwanie podsieci sieci wirtualnej](../virtual-network/virtual-network-manage-subnet.md).

Po wdrożeniu pierwszej grupy kontenerów przy użyciu tej metody, można wdrożyć do tej samej podsieci, określając sieci wirtualnej i nazwy podsieci lub profil sieci, które platforma Azure automatycznie tworzy za Ciebie. Ponieważ Azure deleguje podsieci do usługi Azure Container Instances, możesz wdrożyć *tylko* grup kontenerów do podsieci.

### <a name="existing-virtual-network"></a>Istniejąca sieć wirtualna

Aby wdrożyć grupę kontenerów do istniejącej sieci wirtualnej:

1. Utwórz podsieć w istniejącej sieci wirtualnej lub być puste istniejącej podsieci *wszystkich* inne zasoby
1. Wdróż grupę kontenerów, z [utworzyć kontener az] [ az-container-create] i określ jedną z następujących czynności:
   * Nazwa sieci wirtualnej i podsieci
   * Zasób sieci wirtualnej, identyfikator i identyfikator zasobu podsieci, który umożliwia używanie sieci wirtualnej z innej grupy zasobów
   * Nazwa profilu sieciowego lub identyfikator, który można uzyskać za pomocą [Lista profilów sieciowych az][az-network-profile-list]

Po wdrożeniu pierwszej grupy kontenera do istniejącej podsieci, Azure deleguje tej podsieci do usługi Azure Container Instances. Nie można wdrażać zasobów innych niż grup kontenerów, z tą podsiecią.

## <a name="deployment-examples"></a>Przykłady wdrożeń

W następujących sekcjach opisano sposób wdrażania grup kontenerów z siecią wirtualną przy użyciu wiersza polecenia platformy Azure. W przykładach poleceń sformatowanych w systemie **Bash** powłoki. Jeśli wolisz inny powłoki, takich jak program PowerShell lub wierszu polecenia, należy odpowiednio dostosować znaki kontynuacji wiersza.

### <a name="deploy-to-a-new-virtual-network"></a>Wdrożyć nową sieć wirtualną

Najpierw należy wdrożyć grupę kontenerów i określ parametry w celu nową sieć wirtualną i podsieć. Po określeniu tych parametrów, Azure tworzy sieć wirtualną i podsieć, deleguje podsieci do usługi Azure Container instances i również tworzy profil sieciowy. Po utworzeniu tych zasobów grupy kontenerów jest wdrażana do podsieci.

Uruchom następujące polecenie [utworzyć kontener az] [ az-container-create] polecenia, który określa ustawienia dla nowej sieci wirtualnej i podsieci. Należy podać nazwę grupy zasobów, która została utworzona w regionie, [obsługuje](#preview-limitations) grup kontenerów w sieci wirtualnej. To polecenie wdraża publicznej Microsoft [aci-helloworld] [ aci-helloworld] kontenera uruchamiającego małych Node.js serwerem sieci Web obsługująca statyczną stronę sieci web. W następnej sekcji będzie wdrożyć drugiej grupy kontenerów na tej samej podsieci, a testowanie komunikacji między wystąpieniami dwóch kontenerów.

```azurecli
az container create \
    --name appcontainer \
    --resource-group myResourceGroup \
    --image mcr.microsoft.com/azuredocs/aci-helloworld \
    --vnet aci-vnet \
    --vnet-address-prefix 10.0.0.0/16 \
    --subnet aci-subnet \
    --subnet-address-prefix 10.0.0.0/24
```

Podczas wdrażania nowej sieci wirtualnej za pomocą tej metody, wdrożenie może potrwać kilka minut, podczas gdy zasoby sieciowe zostaną utworzone. Po początkowym wdrożeniu wdrożeń grupy kontenerów dodatkowe ukończyć szybciej.

### <a name="deploy-to-existing-virtual-network"></a>Wdrażanie w istniejącej sieci wirtualnej

Teraz, gdy wdrożono grupy kontenerów do nowej sieci wirtualnej, wdrożyć drugiej grupy kontenerów tej samej podsieci, a następnie sprawdzić łączność między wystąpieniami dwóch kontenerów.

Najpierw pobierz adres IP został wdrożony, pierwszej grupy kontenerów *appcontainer*:

```azurecli
az container show --resource-group myResourceGroup --name appcontainer --query ipAddress.ip --output tsv
```

Dane wyjściowe powinien być wyświetlany adres IP z grupy kontenerów w podsieci prywatnej:

```console
$ az container show --resource-group myResourceGroup --name appcontainer --query ipAddress.ip --output tsv
10.0.0.4
```

Teraz ustaw `CONTAINER_GROUP_IP` do adresu IP pobierany za pomocą `az container show` polecenia i wykonaj następujące czynności, `az container create` polecenia. Ten drugi kontener *commchecker*, przeprowadza obrazu na podstawie Alpine Linux i wykonuje `wget` względem adresu IP podsieci prywatnej pierwszej grupy kontenerów.

```azurecli
CONTAINER_GROUP_IP=<container-group-IP-here>

az container create \
    --resource-group myResourceGroup \
    --name commchecker \
    --image alpine:3.5 \
    --command-line "wget $CONTAINER_GROUP_IP" \
    --restart-policy never \
    --vnet aci-vnet \
    --subnet aci-subnet
```

Po zakończeniu tego drugiego wdrożenia kontenera ściągnięcia jej dzienników, dzięki czemu możesz zobaczyć dane wyjściowe `wget` polecenia ono wykonywane:

```azurecli
az container logs --resource-group myResourceGroup --name commchecker
```

W przypadku drugiego kontenera pomyślnie z pierwszym dane wyjściowe powinny wyglądać podobnie do:

```console
$ az container logs --resource-group myResourceGroup --name commchecker
Connecting to 10.0.0.4 (10.0.0.4:80)
index.html           100% |*******************************|  1663   0:00:00 ETA
```

Dane wyjściowe dziennika powinien pokazują, że `wget` był w stanie nawiązać połączenie i Pobierz plik indeksu z pierwszego kontenera przy użyciu jego prywatny adres IP w podsieci lokalnej. Pozostały ruch sieciowy między grupami dwóch kontenerów w ramach sieci wirtualnej.

### <a name="deploy-to-existing-virtual-network---yaml"></a>Wdrażanie w istniejącej sieci wirtualnej — YAML

Grupy kontenerów można także wdrożyć do istniejącej sieci wirtualnej przy użyciu pliku YAML. Aby wdrożyć w podsieci w sieci wirtualnej, należy określić kilka dodatkowych właściwości w YAML:

* `ipAddress`: Ustawienia adresu IP dla grupy kontenerów.
  * `ports`: Porty, aby otworzyć program, jeśli istnieje.
  * `protocol`: Protokół (TCP lub UDP) dla otwartego portu.
* `networkProfile`: Określa ustawienia sieciowe, takie jak sieć wirtualną i podsieć dla zasobów platformy Azure.
  * `id`: Pełny identyfikator zasobu usługi Resource Manager z `networkProfile`.

Aby wdrożyć grupy kontenerów w sieci wirtualnej przy użyciu pliku YAML, należy najpierw uzyskać identyfikator profilu sieciowego. Wykonaj [Lista profilów sieciowych az] [ az-network-profile-list] polecenie, określając nazwę grupy zasobów, zawierającą sieć wirtualna i podsieć delegowanego.

``` azurecli
az network profile list --resource-group myResourceGroup --query [0].id --output tsv
```

Dane wyjściowe polecenia wyświetla pełny identyfikator zasobu dla profilu sieciowego:

```console
$ az network profile list --resource-group myResourceGroup --query [0].id --output tsv
/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkProfiles/aci-network-profile-aci-vnet-aci-subnet
```

Po utworzeniu sieci identyfikator profilu, skopiuj poniższego kodu YAML do nowego pliku o nazwie *vnet wdrażanie aci.yaml*. W obszarze `networkProfile`, Zastąp `id` wartość o identyfikatorze właśnie pobierane, następnie zapisz plik. Ta YAML tworzy grupę kontenerów o nazwie *appcontaineryaml* w Twojej sieci wirtualnej.

```YAML
apiVersion: '2018-09-01'
location: westus
name: appcontaineryaml
properties:
  containers:
  - name: appcontaineryaml
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld
      ports:
      - port: 80
        protocol: TCP
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  ipAddress:
    type: Private
    ports:
    - protocol: tcp
      port: '80'
  networkProfile:
    id: /subscriptions/<Subscription ID>/resourceGroups/container/providers/Microsoft.Network/networkProfiles/aci-network-profile-aci-vnet-subnet
  osType: Linux
  restartPolicy: Always
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Wdróż grupę kontenerów z [utworzyć kontener az] [ az-container-create] polecenie, określając nazwę pliku YAML `--file` parametru:

```azurecli
az container create --resource-group myResourceGroup --file vnet-deploy-aci.yaml
```

Po zakończeniu wdrożenia Uruchom [az container show] [ az-container-show] polecenie, aby wyświetlić jego stan:

```console
$ az container show --resource-group myResourceGroup --name appcontaineryaml --output table
Name              ResourceGroup    Status    Image                                       IP:ports     Network    CPU/Memory       OsType    Location
----------------  ---------------  --------  ------------------------------------------  -----------  ---------  ---------------  --------  ----------
appcontaineryaml  myResourceGroup  Running   mcr.microsoft.com/azuredocs/aci-helloworld  10.0.0.5:80  Private    1.0 core/1.5 gb  Linux     westus
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

### <a name="delete-container-instances"></a>Usuwanie wystąpień kontenerów

Po zakończeniu pracy z usługą container instances został utworzony, usuń je za pomocą następujących poleceń:

```azurecli
az container delete --resource-group myResourceGroup --name appcontainer -y
az container delete --resource-group myResourceGroup --name commchecker -y
az container delete --resource-group myResourceGroup --name appcontaineryaml -y
```

### <a name="delete-network-resources"></a>Usuwanie zasobów sieciowych

Początkowa wersja zapoznawcza tej funkcji wymaga kilku dodatkowych poleceń można usunąć zasobów sieciowych, że została utworzona wcześniej. Jeśli przykładowe polecenia w poprzednich sekcjach tego artykułu jest używany do tworzenia sieci wirtualnej i podsieci, można użyć poniższy skrypt do usuwania tych zasobów sieciowych.

Przed wykonaniem skryptu, ustaw `RES_GROUP` zmienną nazwy grupy zasobów zawierająca sieć wirtualną i podsieć, która powinna zostać usunięta. Skrypt jest sformatowany do powłoki Bash. Jeśli wolisz inny powłoki, takich jak program PowerShell lub wierszu polecenia, należy odpowiednio zmienić przypisanie zmiennej i metod dostępu.

> [!WARNING]
> Ten skrypt spowoduje usunięcie zasobów! Usuwa sieć wirtualną i wszystkie podsieci, które zawiera. Pamiętaj, że nie są już potrzebne *wszelkie* zasobów w sieci wirtualnej, w tym wszelkie podsieci, które zawiera, przed uruchomieniem tego skryptu. Po usunięciu **te zasoby są nieodwracalny**.

```azurecli
# Replace <my-resource-group> with the name of your resource group
RES_GROUP=<my-resource-group>

# Get network profile ID
NETWORK_PROFILE_ID=$(az network profile list --resource-group $RES_GROUP --query [0].id --output tsv)

# Delete the network profile
az network profile delete --id $NETWORK_PROFILE_ID -y

# Get the service association link (SAL) ID
SAL_ID=$(az network vnet subnet show --resource-group $RES_GROUP --vnet-name aci-vnet --name aci-subnet --query id --output tsv)/providers/Microsoft.ContainerInstance/serviceAssociationLinks/default

# Delete the default SAL ID for the subnet
az resource delete --ids $SAL_ID --api-version 2018-07-01

# Delete the subnet delegation to Azure Container Instances
az network vnet subnet update --resource-group $RES_GROUP --vnet-name aci-vnet --name aci-subnet --remove delegations 0

# Delete the subnet
az network vnet subnet delete --resource-group $RES_GROUP --vnet-name aci-vnet --name aci-subnet

# Delete virtual network
az network vnet delete --resource-group $RES_GROUP --name aci-vnet
```

## <a name="next-steps"></a>Kolejne kroki

Aby wdrożyć nową sieć wirtualną, podsiecią, profil sieci i grupy kontenerów przy użyciu szablonu usługi Resource Manager, zobacz [Utwórz grupę kontenerów platformy Azure z siecią wirtualną](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
).

Kilka zasoby sieci wirtualnej i funkcje zostały omówionych w tym artykule, chociaż krótko. Dokumentacji usługi Azure Virtual Network, które często obejmuje następujące tematy:

* [Sieć wirtualna](../virtual-network/manage-virtual-network.md)
* [Podsieć](../virtual-network/virtual-network-manage-subnet.md)
* [Punkty końcowe usługi](../virtual-network/virtual-network-service-endpoints-overview.md)
* [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md)
* [ExpressRoute](../expressroute/expressroute-introduction.md)

<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-vnet/aci-vnet-01.png

<!-- LINKS - External -->
[aci-helloworld]: https://hub.docker.com/_/microsoft-azuredocs-aci-helloworld
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-network-vnet-create]: /cli/azure/network/vnet#az-network-vnet-create
[az-network-profile-list]: /cli/azure/network/profile#az-network-profile-list
