---
title: Statyczny adres IP dla grupy kontenerów
description: Tworzenie grupy kontenerów w sieci wirtualnej i używanie bramy aplikacji platformy Azure do udostępnienia statycznego adresu IP frontonu na konteneryzowaną aplikację sieci web
ms.topic: article
ms.date: 03/16/2020
ms.openlocfilehash: 5c3a14f93af3ecc614dc296f0a4d2815d7a64a66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481793"
---
# <a name="expose-a-static-ip-address-for-a-container-group"></a>Uwidacznianie statycznego adresu IP dla grupy kontenerów

W tym artykule przedstawiono jeden ze sposobów udostępnienia statycznego, publicznego adresu IP dla [grupy kontenerów](container-instances-container-groups.md) przy użyciu [bramy aplikacji](../application-gateway/overview.md)platformy Azure . Wykonaj następujące kroki, gdy potrzebujesz statycznego punktu wejścia dla konteneryzowanej aplikacji skierowanej do zewnątrz, która jest uruchamiana w wystąpieniach kontenera platformy Azure. 

W tym artykule używasz interfejsu wiersza polecenia platformy Azure do utworzenia zasobów dla tego scenariusza:

* Sieć wirtualna platformy Azure
* Grupa kontenerów wdrożona [w sieci wirtualnej (wersja zapoznawcza)](container-instances-vnet.md) zawierająca małą aplikację sieci web
* Brama aplikacji z publicznym adresem IP wewnętrznej bazy danych, odbiornik do hosta witryny sieci Web w bramie i trasa do grupy kontenerów wewnętrznej bazy danych

Tak długo, jak brama aplikacji działa, a grupa kontenerów udostępnia stabilny prywatny adres IP w sieci delegowanej podsieci, grupa kontenerów jest dostępna pod tym publicznym adresem IP.

> [!NOTE]
> Opłaty platformy Azure za bramę aplikacji na podstawie czasu, przez jaki brama jest aprowizowana i dostępna, a także ilości danych, które przetwarza. Zobacz [cennik](https://azure.microsoft.com/pricing/details/application-gateway/).

## <a name="create-virtual-network"></a>Tworzenie sieci wirtualnej

W typowym przypadku może już mieć sieci wirtualnej platformy Azure. Jeśli go nie masz, utwórz go w sposób pokazany za pomocą poniższych przykładowych poleceń. Sieć wirtualna potrzebuje oddzielnych podsieci dla bramy aplikacji i grupy kontenerów.

Jeśli tego potrzebujesz, utwórz grupę zasobów platformy Azure. Przykład:

```azureci
az group create --name myResourceGroup --location eastus
```

Utwórz sieć wirtualną za pomocą polecenia [tworzenie sieci wirtualnej az.][az-network-vnet-create] To polecenie tworzy podsieć *myAGSubnet* w sieci.

```azurecli
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroup \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24
```

Użyj polecenia [tworzenia podsieci sieci AZ,][az-network-vnet-subnet-create] aby utworzyć podsieć dla grupy kontenerów wewnętrznej bazy danych. Tutaj nazywa się *myACISubnet*.

```azurecli
az network vnet subnet create \
  --name myACISubnet \
  --resource-group myResourceGroup \
  --vnet-name myVNet   \
  --address-prefix 10.0.2.0/24
```

Użyj polecenia [tworzenia sieci az public-ip,][az-network-public-ip-create] aby utworzyć statyczny publiczny zasób IP. W późniejszym kroku ten adres jest skonfigurowany jako front-end bramy aplikacji.

```azurecli
az network public-ip create \
  --resource-group myResourceGroup \
  --name myAGPublicIPAddress \
  --allocation-method Static \
  --sku Standard
```

## <a name="create-container-group"></a>Tworzenie grupy kontenerów

Uruchom następujące [tworzenie kontenera az,][az-container-create] aby utworzyć grupę kontenerów w sieci wirtualnej skonfigurowaną w poprzednim kroku. 

Grupa jest wdrażana w podsieci *myACISubnet* i zawiera jedno wystąpienie `aci-helloworld` o nazwie *appcontainer,* który pobiera obraz. Jak pokazano w innych artykułach w dokumentacji, ten obraz pakiety małej aplikacji sieci web napisanej w Node.js, który służy statycznej strony HTML. 

```azurecli
az container create \
  --name appcontainer \
  --resource-group myResourceGroup \
  --image mcr.microsoft.com/azuredocs/aci-helloworld \
  --vnet myVNet \
  --subnet myACISubnet
```

Po pomyślnym wdrożeniu grupie kontenerów jest przypisywany prywatny adres IP w sieci wirtualnej. Na przykład uruchom następujące polecenie [az container show,][az-container-show] aby pobrać adres IP grupy:

```azurecli
az container show \
  --name appcontainer --resource-group myResourceGroup \
  --query ipAddress.ip --output tsv
```

Dane wyjściowe są podobne do następujących: `10.0.2.4`.

Aby użyć w późniejszym kroku, zapisz adres IP w zmiennej środowiskowej:

```azurecli
ACI_IP=$(az container show \
  --name appcontainer \
  --resource-group myResourceGroup \
  --query ipAddress.ip --output tsv)
```

## <a name="create-application-gateway"></a>Tworzenie bramy aplikacji

Utwórz bramę aplikacji w sieci wirtualnej, wykonując kroki w [przewodniku Szybki start bramy aplikacji](../application-gateway/quick-create-cli.md). Następujące polecenie [tworzenia bramy aplikacji sieciowej az][az-network-application-gateway-create] tworzy bramę z publicznym adresem IP frontendu i marszrutą do grupy kontenerów wewnętrznej bazy danych. Szczegółowe informacje na temat ustawień bramy można znaleźć w [dokumentacji bramy aplikacji.](/azure/application-gateway/)

```azurecli
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroup \
  --capacity 2 \
  --sku Standard_v2 \
  --http-settings-protocol http \
  --public-ip-address myAGPublicIPAddress \
  --vnet-name myVNet \
  --subnet myAGSubnet \
  --servers "$ACI_IP" 
```


Utworzenie bramy aplikacji na platformie Azure może potrwać do 15 minut. 

## <a name="test-public-ip-address"></a>Testowanie publicznego adresu IP
  
Teraz możesz przetestować dostęp do aplikacji sieci web uruchomionej w grupie kontenerów za bramą aplikacji.

Uruchom polecenie [az network public-ip show,][az-network-public-ip-show] aby pobrać publiczny adres IP portalu frontonijowego:

```azurecli
az network public-ip show \
--resource-group myresourcegroup \
--name myAGPublicIPAddress \
--query [ipAddress] \
--output tsv
```

Dane wyjściowe to publiczny adres `52.142.18.133`IP, podobny do: .

Aby wyświetlić uruchomiającą aplikację sieci web po pomyślnym skonfigurowaniu, przejdź do publicznego adresu IP bramy w przeglądarce. Pomyślny dostęp jest podobny do:

![Zrzut ekranu przedstawiający aplikację uruchomioną w wystąpieniu kontenera platformy Azure](./media/container-instances-application-gateway/aci-app-app-gateway.png)

## <a name="next-steps"></a>Następne kroki

* Zobacz [szablon szybkiego startu,](https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress-vnet) aby utworzyć grupę kontenerów z wystąpieniem kontenera WordPress jako serwer zaplecza za bramą aplikacji.
* Można również skonfigurować bramę aplikacji z certyfikatem zakończenia SSL. Zobacz [przegląd](../application-gateway/ssl-overview.md) i [samouczek](../application-gateway/create-ssl-portal.md).
* W zależności od scenariusza należy rozważyć użycie innych rozwiązań równoważenia obciążenia platformy Azure z wystąpieniami kontenera platformy Azure. Na przykład użyj [usługi Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) do dystrybucji ruchu w wielu wystąpieniach kontenera i w wielu regionach. Zobacz ten [wpis w blogu](https://aaronmsft.com/posts/azure-container-instances/).

[az-network-vnet-create]:  /cli/azure/network/vnet#az-network-vnet-create
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-create
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-public-ip-show]: /cli/azure/network/public-ip#az-network-public-ip-show
[az-network-application-gateway-create]: /cli/azure/network/application-gateway#az-network-application-gateway-create
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show