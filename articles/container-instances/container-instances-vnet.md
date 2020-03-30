---
title: Wdrażanie grupy kontenerów w sieci wirtualnej platformy Azure
description: Dowiedz się, jak wdrożyć grupy kontenerów w nowej lub istniejącej sieci wirtualnej platformy Azure.
ms.topic: article
ms.date: 01/06/2020
ms.author: danlep
ms.openlocfilehash: 318576e9b5c5b32bbc993ea16494c938b74bd2f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77200065"
---
# <a name="deploy-container-instances-into-an-azure-virtual-network"></a>Wdrażanie wystąpień kontenerów w sieci wirtualnej platformy Azure

[Usługa Azure Virtual Network](../virtual-network/virtual-networks-overview.md) zapewnia bezpieczną sieć prywatną dla zasobów platformy Azure i lokalnych. Wdrażając grupy kontenerów w sieci wirtualnej platformy Azure, kontenery mogą bezpiecznie komunikować się z innymi zasobami w sieci wirtualnej.

Grupy kontenerów wdrożone w sieci wirtualnej platformy Azure umożliwiają scenariusze, takie jak:

* Bezpośrednia komunikacja między grupami kontenerów w tej samej podsieci
* Wysyłanie danych wyjściowych obciążenia [opartego na zadaniach](container-instances-restart-policy.md) z wystąpień kontenerów do bazy danych w sieci wirtualnej
* Pobieranie zawartości dla wystąpień kontenera z [punktu końcowego usługi](../virtual-network/virtual-network-service-endpoints-overview.md) w sieci wirtualnej
* Komunikacja kontenerowa z maszynami wirtualnymi w sieci wirtualnej
* Komunikacja kontenerowa z zasobami lokalnymi za pośrednictwem [bramy sieci VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) lub [usługi ExpressRoute](../expressroute/expressroute-introduction.md)

> [!IMPORTANT]
> Wdrożenia grup kontenerów w sieci wirtualnej są ogólnie dostępne dla obciążeń produkcyjnych tylko w następujących regionach: Wschodnie stany **USA, południowo-środkowe stany USA i zachodnie stany USA 2**. W innych regionach, w których funkcja jest dostępna, wdrożenia sieci wirtualnej są obecnie w wersji zapoznawczej, a ogólna dostępność planowana jest w najbliższej przyszłości. Wersje zapoznawcze są udostępniane pod warunkiem udzielenia zgody na [dodatkowe warunki użytkowania][terms-of-use]. 


## <a name="virtual-network-deployment-limitations"></a>Ograniczenia wdrażania sieci wirtualnej

Pewne ograniczenia mają zastosowanie podczas wdrażania grup kontenerów w sieci wirtualnej.

* Aby wdrożyć grupy kontenerów w podsieci, podsieć nie może zawierać żadnych innych typów zasobów. Usuń wszystkie istniejące zasoby z istniejącej podsieci przed wdrożeniem grup kontenerów do niej lub utwórz nową podsieć.
* Nie można użyć [tożsamości zarządzanej](container-instances-managed-identity.md) w grupie kontenerów wdrożonej w sieci wirtualnej.
* Nie można włączyć [sondy żywotności](container-instances-liveness-probe.md) lub [sondy gotowości](container-instances-readiness-probe.md) w grupie kontenerów wdrożonych w sieci wirtualnej.
* Ze względu na dodatkowe zasoby sieciowe zaangażowanych wdrażania grupy kontenerów w sieci wirtualnej jest zazwyczaj wolniejsze niż wdrażanie wystąpienia kontenera standardowego.

[!INCLUDE [container-instances-vnet-limits](../../includes/container-instances-vnet-limits.md)]

Limity zasobów kontenera mogą różnić się od limitów dla wystąpień kontenerów niesawiązanych z siecią w tych regionach. Obecnie dla tej funkcji obsługiwane są tylko kontenery systemu Linux. Planowane jest wprowadzenie obsługi w systemie Windows.

### <a name="unsupported-networking-scenarios"></a>Nieobsługiwały scenariusze sieci 

* **Równoważenie obciążenia platformy Azure** — umieszczanie modułu równoważenia obciążenia platformy Azure przed wystąpieniami kontenerów w sieciowej grupie kontenerów nie jest obsługiwane
* **Komunikacja równorzędna sieci wirtualnej**
  * Komunikacja równorzędna sieci wirtualnej nie będzie działać dla usługi ACI, jeśli sieć, w której sieć wirtualna ACI jest równorzędna, używa publicznej przestrzeni IP. Sieć równorzędna potrzebuje prywatnej przestrzeni IP RFC 1918, aby sieć wirtualna działała. 
  * Sieć wirtualną można równorzędnie tylko z jedną inną siecią wirtualną
* **Routing ruchu w sieci wirtualnej** — nie można skonfigurować tras niestandardowych wokół publicznych adresów IP. Trasy można ustawić w prywatnym obszarze IP w podsieci delegowanej, w której są wdrażane zasoby ACI 
* **Sieciowe grupy zabezpieczeń** — reguły zabezpieczeń ruchu wychodzącego w grupach zabezpieczeń zastosowanych do podsieci delegowanej do wystąpień kontenerów platformy Azure nie są obecnie wymuszane 
* **Publiczna etykieta IP lub DNS** — grupy kontenerów wdrożone w sieci wirtualnej nie obsługują obecnie uwidaczniania kontenerów bezpośrednio w Internecie przy pomocy publicznego adresu IP lub w pełni kwalifikowanej nazwy domeny
* **Wewnętrzna rozdzielczość nazw** — rozpoznawanie nazw zasobów platformy Azure w sieci wirtualnej za pośrednictwem wewnętrznego systemu Azure DNS nie jest obsługiwane

**Usuwanie zasobów sieciowych** wymaga [dodatkowych kroków](#delete-network-resources) po wdrożeniu grup kontenerów w sieci wirtualnej.

## <a name="required-network-resources"></a>Wymagane zasoby sieciowe

Istnieją trzy zasoby sieci wirtualnej platformy Azure wymagane do wdrażania grup kontenerów w sieci wirtualnej: sama [sieć wirtualna,](#virtual-network) [delegowana podsieć](#subnet-delegated) w sieci wirtualnej i [profil sieci.](#network-profile) 

### <a name="virtual-network"></a>Sieć wirtualna

Sieć wirtualna definiuje przestrzeń adresową, w której utworzysz co najmniej jedną podsieci. Następnie wdrożyć zasoby platformy Azure (takie jak grupy kontenerów) w podsieciach w sieci wirtualnej.

### <a name="subnet-delegated"></a>Podsieć (delegowane)

Podsieci segmentują sieć wirtualną na oddzielne przestrzenie adresowe, z których można dzielić zasoby platformy Azure, które w nich umieszczasz. Utworzysz jedną lub kilka podsieci w sieci wirtualnej.

Podsieć używana dla grup kontenerów może zawierać tylko grupy kontenerów. Po pierwszym wdrożeniu grupy kontenerów w podsieci platforma Azure deleguje tę podsieci do wystąpień kontenera platformy Azure. Po delegacie podsieć może być używana tylko dla grup kontenerów. Jeśli spróbujesz wdrożyć zasoby inne niż grupy kontenerów w podsieci delegowanej, operacja zakończy się niepowodzeniem.

### <a name="network-profile"></a>Profil sieciowy

Profil sieciowy to szablon konfiguracji sieci dla zasobów platformy Azure. Określa pewne właściwości sieci dla zasobu, na przykład podsieć, w której powinien zostać wdrożony. Gdy po raz pierwszy [użyjesz polecenia tworzenia kontenera az,][az-container-create] aby wdrożyć grupę kontenerów w podsieci (a tym samym w sieci wirtualnej), platforma Azure tworzy profil sieciowy. Następnie można użyć tego profilu sieciowego dla przyszłych wdrożeń w podsieci. 

Aby użyć szablonu Menedżera zasobów, pliku YAML lub metody programowej do wdrożenia grupy kontenerów w podsieci, należy podać pełny identyfikator zasobu Menedżera zasobów profilu sieciowego. Można użyć profilu utworzonego wcześniej przy użyciu [programu az container create][az-container-create]lub utworzyć profil przy użyciu szablonu Menedżera zasobów (zobacz przykład [szablonu](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet) i [odwołanie).](https://docs.microsoft.com/azure/templates/microsoft.network/networkprofiles) Aby uzyskać identyfikator wcześniej utworzonego profilu, użyj polecenia [az network profile list.][az-network-profile-list] 

Na poniższym diagramie kilka grup kontenerów zostało wdrożonych w podsieci delegowanej do wystąpień kontenera platformy Azure. Po wdrożeniu jednej grupy kontenerów w podsieci można wdrożyć dodatkowe grupy kontenerów, określając ten sam profil sieciowy.

![Grupy kontenerów w sieci wirtualnej][aci-vnet-01]

## <a name="deployment-scenarios"></a>Scenariusze wdrażania

Tworzenie [kontenera az][az-container-create] służy do wdrażania grup kontenerów w nowej sieci wirtualnej i zezwalania na tworzenie wymaganych zasobów sieciowych lub wdrażanie w istniejącej sieci wirtualnej. 

### <a name="new-virtual-network"></a>Nowa sieć wirtualna

Aby wdrożyć w nowej sieci wirtualnej i automatycznie utworzyć zasoby sieciowe platformy Azure, należy określić następujące elementy podczas [wykonywania tworzenia kontenera az:][az-container-create]

* Nazwa sieci wirtualnej
* Prefiks adresu sieci wirtualnej w formacie CIDR
* Nazwa podsieci
* Prefiks adresu podsieci w formacie CIDR

Prefiksy adresu sieci wirtualnej i podsieci określają odpowiednio przestrzenie adresowe dla sieci wirtualnej i podsieci. Wartości te są reprezentowane na przykład `10.0.0.0/16`w notacji CIDR (Classless Inter-Domain Routing). Aby uzyskać więcej informacji na temat pracy z podsieciami, zobacz [Dodawanie, zmienianie lub usuwanie podsieci sieci wirtualnej](../virtual-network/virtual-network-manage-subnet.md).

Po wdrożeniu pierwszej grupy kontenerów za pomocą tej metody można wdrożyć w tej samej podsieci, określając nazwy sieci wirtualnej i podsieci lub profil sieciowy, który platforma Azure automatycznie tworzy dla Ciebie. Ponieważ platforma Azure deleguje podsieć do wystąpień kontenera platformy Azure, można wdrożyć *tylko* grupy kontenerów do podsieci.

### <a name="existing-virtual-network"></a>Istniejąca sieć wirtualna

Aby wdrożyć grupę kontenerów w istniejącej sieci wirtualnej:

1. Tworzenie podsieci w istniejącej sieci wirtualnej, używanie istniejącej podsieci, w której grupa kontenerów jest już wdrożona, lub używanie istniejącej podsieci opróżnioneją *wszystkich* innych zasobów
1. Wdrażanie grupy kontenerów z [tworzeniem kontenera az][az-container-create] i określanie jednej z następujących czynności:
   * Nazwa sieci wirtualnej i nazwa podsieci
   * Identyfikator zasobu sieci wirtualnej i identyfikator zasobu podsieci, który umożliwia korzystanie z sieci wirtualnej z innej grupy zasobów
   * Nazwa profilu sieciowego lub identyfikator, który można uzyskać za pomocą [listy profilów sieciowych az][az-network-profile-list]

Po wdrożeniu pierwszej grupy kontenerów w istniejącej podsieci platforma Azure deleguje tę podsieci do wystąpień kontenera platformy Azure. Nie można już wdrażać zasobów innych niż grupy kontenerów w tej podsieci.

## <a name="deployment-examples"></a>Przykłady wdrożeń

W poniższych sekcjach opisano sposób wdrażania grup kontenerów w sieci wirtualnej za pomocą interfejsu wiersza polecenia platformy Azure. Przykłady poleceń są sformatowane dla powłoki **Bash.** Jeśli wolisz inną powłokę, taką jak PowerShell lub wiersz poleceń, odpowiednio dostosuj znaki kontynuacji wiersza.

### <a name="deploy-to-a-new-virtual-network"></a>Wdrażanie w nowej sieci wirtualnej

Najpierw należy wdrożyć grupę kontenerów i określić parametry dla nowej sieci wirtualnej i podsieci. Po określeniu tych parametrów platforma Azure tworzy sieć wirtualną i podsieć, deleguje podsieć do wystąpień kontenera platformy Azure, a także tworzy profil sieciowy. Po utworzeniu tych zasobów grupa kontenerów jest wdrażana w podsieci.

Uruchom następujące polecenie [tworzenia kontenera az,][az-container-create] które określa ustawienia nowej sieci wirtualnej i podsieci. Należy podać nazwę grupy zasobów utworzonej w regionie, w którym [dostępne](#virtual-network-deployment-limitations)są wdrożenia grup kontenerów w sieci wirtualnej . To polecenie wdraża publiczny kontener [Microsoft aci-helloworld,][aci-helloworld] który uruchamia mały serwer www Node.js obsługujący statyczną stronę sieci Web. W następnej sekcji wdrożysz drugą grupę kontenerów w tej samej podsieci i przetestujesz komunikację między dwoma wystąpieniami kontenera.

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

Po wdrożeniu w nowej sieci wirtualnej przy użyciu tej metody wdrożenie może potrwać kilka minut podczas tworzenia zasobów sieciowych. Po początkowym wdrożeniu dodatkowe wdrożenia grup kontenerów są szybciej.

### <a name="deploy-to-existing-virtual-network"></a>Wdrażanie w istniejącej sieci wirtualnej

Teraz, gdy grupa kontenerów została wdrożona w nowej sieci wirtualnej, wdrożyć drugą grupę kontenerów w tej samej podsieci i sprawdzić komunikację między dwoma wystąpieniami kontenera.

Najpierw uzyskaj adres IP pierwszej wdrożonej grupy kontenerów, *appcontainer:*

```azurecli
az container show --resource-group myResourceGroup --name appcontainer --query ipAddress.ip --output tsv
```

Dane wyjściowe powinny wyświetlać adres IP grupy kontenerów w podsieci prywatnej:

```console
$ az container show --resource-group myResourceGroup --name appcontainer --query ipAddress.ip --output tsv
10.0.0.4
```

Teraz ustaw `CONTAINER_GROUP_IP` adres IP pobrany `az container show` za pomocą polecenia `az container create` i wykonaj następujące polecenie. Ten drugi kontener, *commchecker*, uruchamia obraz oparty `wget` na systemie Alpine Linux i jest wykonywany względem adresu IP prywatnej podsieci pierwszej grupy kontenerów.

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

Po zakończeniu tego drugiego wdrożenia kontenera, ściągnij jego `wget` dzienniki, dzięki czemu można zobaczyć dane wyjściowe polecenia, które wykonało:

```azurecli
az container logs --resource-group myResourceGroup --name commchecker
```

Jeśli drugi kontener komunikuje się pomyślnie z pierwszym, dane wyjściowe powinny być podobne do:

```console
$ az container logs --resource-group myResourceGroup --name commchecker
Connecting to 10.0.0.4 (10.0.0.4:80)
index.html           100% |*******************************|  1663   0:00:00 ETA
```

Dane wyjściowe dziennika `wget` powinny wykazywać możliwość łączenia się i pobierania pliku indeksu z pierwszego kontenera przy użyciu prywatnego adresu IP w podsieci lokalnej. Ruch sieciowy między dwiema grupami kontenerów pozostał w sieci wirtualnej.

### <a name="deploy-to-existing-virtual-network---yaml"></a>Wdrażanie w istniejącej sieci wirtualnej — YAML

Grupę kontenerów można również wdrożyć w istniejącej sieci wirtualnej przy użyciu pliku YAML, szablonu Menedżera zasobów lub innej metody programowej, takiej jak pakiet SDK języka Python. Aby wdrożyć w podsieci w sieci wirtualnej, należy określić kilka dodatkowych właściwości w YAML:

* `ipAddress`: Ustawienia adresu IP dla grupy kontenerów.
  * `ports`: Porty do otwarcia, jeśli istnieją.
  * `protocol`: Protokół (TCP lub UDP) dla otwartego portu.
* `networkProfile`: Określa ustawienia sieci, takie jak sieć wirtualna i podsieć zasobu platformy Azure.
  * `id`: Pełny identyfikator zasobu Menedżera `networkProfile`zasobów .

Aby wdrożyć grupę kontenerów w sieci wirtualnej z plikiem YAML, należy najpierw uzyskać identyfikator profilu sieciowego. Wykonaj polecenie [az network profile list,][az-network-profile-list] określając nazwę grupy zasobów zawierającej sieć wirtualną i podsieć delegowaną.

``` azurecli
az network profile list --resource-group myResourceGroup --query [0].id --output tsv
```

Dane wyjściowe polecenia wyświetla pełny identyfikator zasobu dla profilu sieciowego:

```console
$ az network profile list --resource-group myResourceGroup --query [0].id --output tsv
/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkProfiles/aci-network-profile-aci-vnet-aci-subnet
```

Po uzyskaniu identyfikatora profilu sieciowego skopiuj następujący plik YAML do nowego pliku o nazwie *vnet-deploy-aci.yaml*. W `networkProfile`obszarze `id` zastąp wartość właśnie pobranym identyfikatorem, a następnie zapisz plik. Ten YAML tworzy grupę kontenerów o nazwie *appcontaineryaml* w sieci wirtualnej.

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
    id: /subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkProfiles/aci-network-profile-aci-vnet-subnet
  osType: Linux
  restartPolicy: Always
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Wdrażanie grupy kontenerów za pomocą polecenia [tworzenia kontenera az,][az-container-create] określającego nazwę pliku YAML dla tego parametru: `--file`

```azurecli
az container create --resource-group myResourceGroup --file vnet-deploy-aci.yaml
```

Po zakończeniu wdrażania uruchom polecenie [az container show,][az-container-show] aby wyświetlić jego stan:

```console
$ az container show --resource-group myResourceGroup --name appcontaineryaml --output table
Name              ResourceGroup    Status    Image                                       IP:ports     Network    CPU/Memory       OsType    Location
----------------  ---------------  --------  ------------------------------------------  -----------  ---------  ---------------  --------  ----------
appcontaineryaml  myResourceGroup  Running   mcr.microsoft.com/azuredocs/aci-helloworld  10.0.0.5:80  Private    1.0 core/1.5 gb  Linux     westus
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

### <a name="delete-container-instances"></a>Usuwanie wystąpień kontenera

Po zakończeniu pracy z utworzonymi wystąpieniami kontenera usuń je za pomocą następujących poleceń:

```azurecli
az container delete --resource-group myResourceGroup --name appcontainer -y
az container delete --resource-group myResourceGroup --name commchecker -y
az container delete --resource-group myResourceGroup --name appcontaineryaml -y
```

### <a name="delete-network-resources"></a>Usuwanie zasobów sieciowych

Ta funkcja wymaga obecnie kilku dodatkowych poleceń, aby usunąć zasoby sieciowe utworzone wcześniej. Jeśli w poprzednich sekcjach tego artykułu użyto przykładowych poleceń do utworzenia sieci wirtualnej i podsieci, można użyć następującego skryptu do usunięcia tych zasobów sieciowych. Skrypt zakłada, że grupa zasobów zawiera jedną sieć wirtualną z jednym profilem sieciowym.

Przed wykonaniem skryptu `RES_GROUP` należy ustawić zmienną na nazwę grupy zasobów zawierającej sieć wirtualną i podsieć, które powinny zostać usunięte. Zaktualizuj nazwę sieci wirtualnej, `aci-vnet` jeśli nazwa nie została wcześniej użyta. Skrypt jest sformatowany dla powłoki Bash. Jeśli wolisz inną powłokę, taką jak PowerShell lub wiersz polecenia, musisz odpowiednio dostosować przypisanie zmiennych i akcesory.

> [!WARNING]
> Ten skrypt usuwa zasoby! Usuwa sieć wirtualną i wszystkie podsieci, które zawiera. Upewnij się, że nie potrzebujesz już *żadnych* zasobów w sieci wirtualnej, w tym wszystkich podsieci, które zawiera, przed uruchomieniem tego skryptu. Po usunięciu **te zasoby są nieodwracalne**.

```azurecli
# Replace <my-resource-group> with the name of your resource group
# Assumes one virtual network in resource group
RES_GROUP=<my-resource-group>

# Get network profile ID
# Assumes one profile in virtual network
NETWORK_PROFILE_ID=$(az network profile list --resource-group $RES_GROUP --query [0].id --output tsv)

# Delete the network profile
az network profile delete --id $NETWORK_PROFILE_ID -y

# Delete virtual network
az network vnet delete --resource-group $RES_GROUP --name aci-vnet
```

## <a name="next-steps"></a>Następne kroki

Aby wdrożyć nową sieć wirtualną, podsiecię, profil sieciowy i grupę kontenerów przy użyciu szablonu Menedżera zasobów, zobacz [Tworzenie grupy kontenerów platformy Azure za pomocą sieci wirtualnej](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
).

Kilka zasobów sieci wirtualnej i funkcje zostały omówione w tym artykule, choć krótko. Dokumentacja sieci wirtualnej platformy Azure obejmuje te tematy w szerokim zakresie:

* [Sieć wirtualna](../virtual-network/manage-virtual-network.md)
* [Podsieci](../virtual-network/virtual-network-manage-subnet.md)
* [Punkty końcowe usługi](../virtual-network/virtual-network-service-endpoints-overview.md)
* [Brama sieci VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)
* [Expressroute](../expressroute/expressroute-introduction.md)

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
