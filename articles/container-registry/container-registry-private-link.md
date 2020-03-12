---
title: Konfigurowanie linku prywatnego
description: Skonfiguruj prywatny punkt końcowy w rejestrze kontenerów i Włącz prywatny link w lokalnej sieci wirtualnej
ms.topic: article
ms.date: 03/10/2020
ms.openlocfilehash: b7dcf2d1eb1a77ea8b9660318ed2a7d4ec183b42
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2020
ms.locfileid: "79128387"
---
# <a name="configure-azure-private-link-for-an-azure-container-registry"></a>Konfigurowanie prywatnego linku platformy Azure dla usługi Azure Container Registry 

Można skonfigurować [prywatny punkt końcowy](../private-link/private-endpoint-overview.md) dla usługi Azure Container Registry, aby klienci w sieci wirtualnej platformy Azure mogli bezpiecznie uzyskiwać dostęp do rejestru za pośrednictwem [prywatnego linku](../private-link/private-link-overview.md). Prywatny punkt końcowy używa adresu IP z przestrzeni adresowej sieci wirtualnej dla rejestru. Ruch sieciowy między klientami w sieci wirtualnej a rejestrem przechodzi przez sieć wirtualną i prywatny link do sieci szkieletowej firmy Microsoft, eliminując ekspozycję z publicznego Internetu.

[Ustawienia DNS](../private-link/private-endpoint-overview.md#dns-configuration) dla prywatnego punktu końcowego można skonfigurować tak, aby ustawienia były rozpoznawane jako prywatny adres IP przydzielony do rejestru. Dzięki konfiguracji DNS klienci i usługi w sieci mogą nadal uzyskiwać dostęp do rejestru w w pełni kwalifikowanej nazwie domeny, takiej jak *myregistry.azurecr.IO*.

Ta funkcja jest dostępna w warstwie usługi kontenera **Premium** . Aby uzyskać informacje o warstwach i ograniczeniach usługi Registry, zobacz [Azure Container Registry SKU](container-registry-skus.md).

> [!IMPORTANT]
> Ta funkcja jest obecnie dostępna w wersji zapoznawczej, a niektóre [ograniczenia](#preview-limitations) mają zastosowanie. Wersje zapoznawcze są udostępniane pod warunkiem udzielenia zgody na [dodatkowe warunki użytkowania][terms-of-use]. Niektóre cechy funkcji mogą ulec zmianie, zanim stanie się ona ogólnie dostępna.

## <a name="preview-limitations"></a>Ograniczenia wersji zapoznawczej

* Obecnie nie można skonfigurować prywatnego linku z prywatnym punktem końcowym w rejestrze z [replikacją geograficzną](container-registry-geo-replication.md). 

## <a name="prerequisites"></a>Wymagania wstępne

* Aby skorzystać z kroków interfejsu wiersza polecenia platformy Azure w tym artykule, zaleca się użycie interfejsu wiersza polecenia platformy Azure w wersji 2.2.0 lub nowszej. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli]. Lub Uruchom w [Azure Cloud Shell](../cloud-shell/quickstart.md).
* Jeśli nie masz jeszcze rejestru kontenerów, utwórz go (wymagana warstwa Premium) i wypchnij przykładowy obraz, taki jak `hello-world` z usługi Docker Hub. Na przykład użyj [Azure Portal][quickstart-portal] lub [interfejsu wiersza polecenia platformy Azure][quickstart-cli] , aby utworzyć rejestr. 

Przykłady interfejsu wiersza polecenia platformy Azure w tym artykule używają następujących zmiennych środowiskowych. Zastąp wartości odpowiednie dla danego środowiska. Wszystkie przykłady są sformatowane dla powłoki bash:

```bash
registryName=<container-registry-name>
registryLocation=<container-registry-location> # Azure region such as westeurope where registry created
resourceGroup=<resource-group-name>
vmName=<virtual-machine-name>
```

## <a name="create-a-docker-enabled-virtual-machine"></a>Tworzenie maszyny wirtualnej z obsługą platformy Docker

Do celów testowych Użyj maszyny wirtualnej Ubuntu z obsługą platformy Docker, aby uzyskać dostęp do usługi Azure Container Registry. Aby użyć uwierzytelniania Azure Active Directory do rejestru, należy również zainstalować [interfejs wiersza polecenia platformy Azure][azure-cli] na maszynie wirtualnej. Jeśli masz już maszynę wirtualną platformy Azure, Pomiń ten krok tworzenia.

Możesz użyć tej samej grupy zasobów dla maszyny wirtualnej i rejestru kontenerów. Ta konfiguracja upraszcza czyszczenie na końcu, ale nie jest wymagane. Jeśli zdecydujesz się utworzyć oddzielną grupę zasobów dla maszyny wirtualnej i sieci wirtualnej, uruchom polecenie [AZ Group Create][az-group-create]:

```azurecli
az group create --name $resourceGroup --location $registryLocation
```

Teraz Wdróż domyślną Ubuntu maszynę wirtualną platformy Azure za pomocą [AZ VM Create][az-vm-create]. Poniższy przykład tworzy maszynę wirtualną o nazwie *myDockerVM*.

```azurecli
az vm create \
  --resource-group $resourceGroup \
  --name $vmName \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

Utworzenie maszyny wirtualnej może potrwać kilka minut. Po zakończeniu wykonywania polecenia Zanotuj `publicIpAddress` wyświetlane przez interfejs wiersza polecenia platformy Azure. Użyj tego adresu, aby nawiązać połączenia SSH z maszyną wirtualną.

### <a name="install-docker-on-the-vm"></a>Zainstaluj platformę Docker na maszynie wirtualnej

Po uruchomieniu maszyny wirtualnej należy nawiązać połączenie SSH z maszyną wirtualną. Zastąp *publicIpAddress* publicznym adresem IP maszyny wirtualnej.

```bash
ssh azureuser@publicIpAddress
```

Uruchom następujące polecenia, aby zainstalować platformę Docker na maszynie wirtualnej Ubuntu:

```bash
sudo apt-get update
sudo apt install docker.io -y
```

Po zakończeniu instalacji uruchom następujące polecenie, aby sprawdzić, czy program Docker działa prawidłowo na maszynie wirtualnej:

```bash
sudo docker run -it hello-world
```

Dane wyjściowe:

```
Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

### <a name="install-the-azure-cli"></a>Zainstaluj interfejs wiersza polecenia platformy Azure

Wykonaj kroki opisane w temacie [Instalowanie interfejsu wiersza polecenia platformy Azure z programem apt](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) , aby zainstalować interfejs wiersza polecenia platformy Azure na maszynie wirtualnej Ubuntu. Na przykład:

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

Wyjdź z połączenia SSH.

## <a name="set-up-private-link---cli"></a>Konfigurowanie linku prywatnego — interfejs wiersza polecenia

### <a name="get-network-and-subnet-names"></a>Pobieranie nazw sieci i podsieci

Jeśli ich nie masz, będziesz potrzebować nazw sieci wirtualnej i podsieci w celu skonfigurowania prywatnego linku. W tym przykładzie należy użyć tej samej podsieci dla maszyny wirtualnej i prywatnego punktu końcowego rejestru. Jednak w wielu scenariuszach można skonfigurować punkt końcowy w oddzielnej podsieci. 

Podczas tworzenia maszyny wirtualnej platforma Azure domyślnie tworzy sieć wirtualną w tej samej grupie zasobów. Nazwa sieci wirtualnej jest oparta na nazwie maszyny wirtualnej. Na przykład jeśli nazwasz maszyny wirtualnej *myDockerVM*, domyślną nazwą sieci wirtualnej jest *myDockerVMVNET*, z podsiecią o nazwie *myDockerVMSubnet*. Ustaw te wartości w zmiennych środowiskowych, uruchamiając polecenie [AZ Network VNET list][az-network-vnet-list] :

```azurecli
networkName=$(az network vnet list \
  --resource-group $resourceGroup \
  --query '[].{Name: name}' --output tsv)

subnetName=$(az network vnet list \
  --resource-group $resourceGroup \
  --query '[].{Subnet: subnets[0].name}' --output tsv)

echo networkName=$networkName
echo subnetName=$subnetName
```

### <a name="disable-network-policies-in-subnet"></a>Wyłączanie zasad sieciowych w podsieci

[Wyłącz zasady sieciowe](../private-link/disable-private-endpoint-network-policy.md) , takie jak sieciowe grupy zabezpieczeń w podsieci dla prywatnego punktu końcowego. Zaktualizuj konfigurację podsieci przy użyciu [AZ Network VNET Subnet Update][az-network-vnet-subnet-update]:

```azurecli
az network vnet subnet update \
 --name $subnetName \
 --vnet-name $networkName \
 --resource-group $resourceGroup \
 --disable-private-endpoint-network-policies
```

### <a name="configure-the-private-dns-zone"></a>Skonfiguruj prywatną strefę DNS

Utwórz prywatną strefę DNS dla domeny usługi priviate Azure Container Registry. W kolejnych krokach utworzysz rekordy DNS dla domeny rejestru w tej strefie DNS.

Aby użyć strefy prywatnej do zastąpienia domyślnego rozpoznawania nazw DNS dla rejestru kontenerów platformy Azure, strefa musi mieć nazwę **privatelink.azurecr.IO**. Uruchom następujące polecenie [AZ Network Private-DNS Zone Create][az-network-private-dns-zone-create] , aby utworzyć strefę prywatną:

```azurecli
az network private-dns zone create \
  --resource-group $resourceGroup \
  --name "privatelink.azurecr.io"
```

### <a name="create-an-association-link"></a>Tworzenie linku skojarzenia

Uruchom [AZ Network Private-DNS link VNET Create][az-network-private-dns-link-vnet-create] , aby skojarzyć strefę prywatną z siecią wirtualną. Ten przykład tworzy łącze o nazwie *myDNSLink*.

```azurecli
az network private-dns link vnet create \
  --resource-group $resourceGroup \
  --zone-name "privatelink.azurecr.io" \
  --name MyDNSLink \
  --virtual-network $networkName \
  --registration-enabled false
```

### <a name="create-a-private-registry-endpoint"></a>Tworzenie prywatnego punktu końcowego rejestru

W tej sekcji Utwórz prywatny punkt końcowy rejestru w sieci wirtualnej. Najpierw Pobierz identyfikator zasobu rejestru:

```azurecli
registryID=$(az acr show --name $registryName \
  --query 'id' --output tsv)
```

Uruchom polecenie [AZ Network Private-Endpoint Create][az-network-private-endpoint-create] , aby utworzyć prywatny punkt końcowy rejestru.

Poniższy przykład tworzy *połączenie*punktów końcowych *myPrivateEndpoint* i połączenia z usługą. Aby określić zasób rejestru kontenerów dla punktu końcowego, należy przekazać `--group-ids registry`:

```azurecli
az network private-endpoint create \
    --name myPrivateEndpoint \
    --resource-group $resourceGroup \
    --vnet-name $networkName \
    --subnet $subnetName \
    --private-connection-resource-id $registryID \
    --group-ids registry \
    --connection-name myConnection
```

### <a name="get-private-ip-addresses"></a>Pobierz prywatne adresy IP

Uruchom [AZ Network Private-Endpoint show][az-network-private-endpoint-show] , aby wysłać zapytanie do punktu końcowego dla identyfikatora interfejsu sieciowego:

```azurecli
networkInterfaceID=$(az network private-endpoint show \
  --name myPrivateEndpoint \
  --resource-group $resourceGroup \
  --query 'networkInterfaces[0].id' \
  --output tsv)
```

Skojarzona z interfejsem sieciowym to dwa prywatne adresy IP dla rejestru kontenerów: jeden dla samego rejestru i jeden dla punktu końcowego danych rejestru. Uruchom następujące polecenie [AZ Resource show][az-resource-show] Commands, aby uzyskać prywatne adresy IP dla rejestru kontenerów i punktu końcowego danych rejestru:

```azurecli
privateIP=$(az resource show \
  --ids $networkInterfaceID \
  --api-version 2019-04-01 --query 'properties.ipConfigurations[1].properties.privateIPAddress' \
  --output tsv)

dataEndpointPrivateIP=$(az resource show \
  --ids $networkInterfaceID \
  --api-version 2019-04-01 \
  --query 'properties.ipConfigurations[0].properties.privateIPAddress' \
  --output tsv)
```

### <a name="create-dns-records-in-the-private-zone"></a>Tworzenie rekordów DNS w strefie prywatnej

Następujące polecenia tworzą rekordy DNS w strefie prywatnej dla punktu końcowego rejestru i jego punktu końcowego danych. Na przykład jeśli w regionie *westeurope* istnieje rejestr o nazwie *Registry* , nazwy punktów końcowych są `myregistry.azurecr.io` i `myregistry.westeurope.data.azurecr.io`. 

Najpierw uruchom [AZ Network Private-DNS record-Set a Create][az-network-private-dns-record-set-a-create] , aby utworzyć puste zestawy rekordów dla punktu końcowego rejestru i punktu końcowego danych:

```azurecli
az network private-dns record-set a create \
  --name $registryName \
  --zone-name privatelink.azurecr.io \
  --resource-group $resourceGroup

# Specify registry region in data endpoint name
az network private-dns record-set a create \
  --name ${registryName}.${registryLocation}.data \
  --zone-name privatelink.azurecr.io \
  --resource-group $resourceGroup
```

Uruchom polecenie [AZ Network Private-DNS record-Set a Add-Record][az-network-private-dns-record-set-a-add-record] , aby utworzyć rekordy a dla punktu końcowego rejestru i punktu końcowego danych:

```azurecli
az network private-dns record-set a add-record \
  --record-set-name $registryName \
  --zone-name privatelink.azurecr.io \
  --resource-group $resourceGroup \
  --ipv4-address $privateIP

# Specify registry region in data endpoint name
az network private-dns record-set a add-record \
  --record-set-name ${registryName}.${registryLocation}.data \
  --zone-name privatelink.azurecr.io \
  --resource-group $resourceGroup \
  --ipv4-address $dataEndpointPrivateIP
```

Link prywatny jest teraz skonfigurowany i gotowy do użycia.

## <a name="set-up-private-link---portal"></a>Skonfiguruj prywatny link — Portal

W poniższych krokach założono, że masz już skonfigurowaną sieć wirtualną i podsieć z maszyną wirtualną do testowania. Możesz również [utworzyć nową sieć wirtualną i podsieć](../virtual-network/quick-create-portal.md).

### <a name="create-a-private-endpoint"></a>Tworzenie prywatnego punktu końcowego

1. W portalu przejdź do rejestru kontenerów.
1. W obszarze **Ustawienia**wybierz pozycję **połączenia prywatnego punktu końcowego (wersja zapoznawcza)** .
1. Wybierz pozycję **+ prywatny punkt końcowy**.
1. Na karcie **podstawy** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | **Szczegóły projektu** | |
    | Subskrypcja | Wybierz subskrypcję. |
    | Grupa zasobów | Wprowadź nazwę istniejącej grupy lub Utwórz nową.|
    | **Szczegóły wystąpienia** |  |
    | Name (Nazwa) | Wprowadź unikatową nazwę. |
    |Region|Wybierz region.|
    |||
5. Wybierz pozycję **Dalej: zasób**.
6. Wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    |Metoda połączenia  | Wybierz pozycję **Połącz z zasobem platformy Azure w moim katalogu**.|
    | Subskrypcja| Wybierz subskrypcję. |
    | Typ zasobu | Wybierz pozycję **Microsoft. ContainerRegistry/rejestry**. |
    | Zasób |Wybierz nazwę rejestru|
    |Podzasób docelowy |Wybierz **Rejestr**|
    |||
7. Wybierz pozycję **Dalej: Konfiguracja**.
8. Wprowadź lub wybierz informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    |**Sieć**| |
    | Sieć wirtualna| Wybierz sieć wirtualną, w której wdrożono maszynę wirtualną, taką jak *myDockerVMVNET*. |
    | Podsieć | Wybierz podsieć, na przykład *myDockerVMSubnet* , w której wdrożono maszynę wirtualną. |
    |**Integracja Prywatna strefa DNS**||
    |Integracja z prywatną strefą DNS |Wybierz pozycję **Tak**. |
    |Strefa Prywatna strefa DNS |Wybierz *(nowy) privatelink.azurecr.IO* |
    |||

1. Wybierz pozycję **Przegląd + utwórz**. Nastąpi przejście do strony **Recenzja i tworzenie** , w której platforma Azure weryfikuje konfigurację. 
2. Gdy zobaczysz komunikat o **przekazaniu walidacji** , wybierz pozycję **Utwórz**.

Po utworzeniu prywatnego punktu końcowego ustawienia DNS w strefie prywatnej są wyświetlane na stronie **Przegląd** punktu końcowego.

![Ustawienia DNS punktu końcowego](./media/container-registry-private-link/private-endpoint-overview.png)

Link prywatny jest teraz skonfigurowany i gotowy do użycia.

## <a name="validate-private-link-connection"></a>Weryfikowanie połączenia prywatnego linku

Należy sprawdzić, czy zasoby w podsieci prywatnego punktu końcowego łączą się z rejestrem za pośrednictwem prywatnego adresu IP i mają poprawną integrację prywatnej strefy DNS.

Aby sprawdzić poprawność połączenia z linkiem prywatnym, Użyj protokołu SSH do maszyny wirtualnej skonfigurowanej w sieci wirtualnej.

Uruchom `nslookup` polecenie, aby rozwiązać adres IP rejestru za pośrednictwem prywatnego linku:

```bash
nslookup $registryName.azurecr.io
```

Przykładowe dane wyjściowe przedstawiają adres IP rejestru w przestrzeni adresowej podsieci:

```console
[...]
myregistry.azurecr.io       canonical name = myregistry.privatelink.azurecr.io.
Name:   myregistry.privatelink.azurecr.io
Address: 10.0.0.6
```

Porównaj ten wynik z publicznym adresem IP w `nslookup` danych wyjściowych dla tego samego rejestru przez publiczny punkt końcowy:

```console
[...]
Non-authoritative answer:
Name:   myregistry.westeurope.cloudapp.azure.com
Address: 40.78.103.41
```

### <a name="registry-operations-over-private-link"></a>Operacje na rejestrze za pośrednictwem prywatnego linku

Sprawdź również, czy można wykonywać operacje na rejestrze z maszyny wirtualnej w podsieci. Nawiąż połączenie SSH z maszyną wirtualną, a następnie uruchom polecenie [AZ ACR login][az-acr-login] , aby zalogować się do rejestru. W zależności od konfiguracji maszyny wirtualnej może być konieczne dodanie prefiksu poniższych poleceń przy użyciu `sudo`.

```bash
az acr login --name $registryName
```

Wykonaj operacje rejestru, takie jak `docker pull`, aby ściągnąć przykładowy obraz z rejestru. Zastąp `hello-world:v1` obrazem i znacznikiem odpowiednim dla rejestru poprzedzonym prefiksem nazwy serwera logowania rejestru (wszystkie małe litery):

```bash
docker pull myregistry.azurecr.io/hello-world:v1
``` 

Platforma Docker pomyślnie ściąga obraz do maszyny wirtualnej.

## <a name="manage-private-endpoint-connections"></a>Zarządzanie połączeniami prywatnego punktu końcowego

Zarządzanie połączeniami prywatnego punktu końcowego rejestru przy użyciu Azure Portal lub za pomocą poleceń w grupie poleceń [AZ ACR Private-Endpoint-Connection][az-acr-private-endpoint-connection] . Operacje obejmują Zatwierdź, Usuń, Wyświetl, Odrzuć lub Pokaż szczegóły połączeń prywatnych punktów końcowych rejestru.

Na przykład aby wyświetlić listę połączeń prywatnych punktów końcowych rejestru, uruchom polecenie [AZ ACR Private-Endpoint-Connection list][az-acr-private-endpoint-connection-list] . Na przykład:

```azurecli
az acr private-endpoint-connection list \
  --registry-name $registryName 
```

W przypadku skonfigurowania połączenia prywatnego punktu końcowego za pomocą kroków opisanych w tym artykule rejestr automatycznie akceptuje połączenia od klientów i usług z uprawnieniami RBAC w rejestrze. Można skonfigurować punkt końcowy, aby wymagał ręcznego zatwierdzania połączeń. Aby uzyskać informacje na temat zatwierdzania i odrzucania połączeń prywatnych punktów końcowych, zobacz [Zarządzanie połączeniem prywatnego punktu końcowego](../private-link/manage-private-endpoint.md).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli wszystkie zasoby platformy Azure zostały utworzone w tej samej grupie zasobów i nie będą już potrzebne, możesz opcjonalnie usunąć zasoby za pomocą jednego polecenia [AZ Group Delete](/cli/azure/group) :

```azurecli
az group delete --name $resourceGroup
```

Aby wyczyścić zasoby w portalu, przejdź do grupy zasobów. Po załadowaniu grupy zasobów kliknij pozycję **Usuń grupę zasobów** , aby usunąć grupę zasobów i przechowywane w niej zasoby.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej na temat linku prywatnego, zapoznaj się z dokumentacją [prywatnego linku do platformy Azure](../private-link/private-link-overview.md) .
* Alternatywą dla linku prywatnego jest skonfigurowanie zasad dostępu do sieci w celu ograniczenia dostępu do rejestru. Aby dowiedzieć się więcej, zobacz [ograniczanie dostępu do usługi Azure Container Registry przy użyciu sieci wirtualnej platformy Azure lub reguł zapory](container-registry-vnet.md).

<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-list]: /cli/azure/acr/repository#az-acr-repository-list
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-private-endpoint-connection]: /cli/azure/acr/private-endpoint-connection
[az-acr-private-endpoint-connection-list]: /cli/azure/acr/private-endpoint-connection#az-acr-private-endpoint-connection-list
[az-acr-private-endpoint-connection-approve]: /cli/azure/acr/private-endpoint-connection#az-acr-private-endpoint-connection-approve
[az-group-create]: /cli/azure/group
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-show
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-update
[az-network-vnet-list]: /cli/azure/network/vnet/#az-network-vnet-list
[az-network-private-endpoint-create]: /cli/azure/network/private-endpoint#az-network-private-endpoint-create
[az-network-private-endpoint-show]: /cli/azure/network/private-endpoint#az-network-private-endpoint-show
[az-network-private-dns-zone-create]: /cli/azure/network/private-dns-zone/create#az-network-private-dns-zone-create
[az-network-private-dns-link-vnet-create]: /cli/azure/network/private-dns-link/vnet#az-network-private-dns-link-vnet-create
[az-network-private-dns-record-set-a-create]: /cli/azure/network/private-dns-record/set/a#az-network-private-dns-record-set-a-create
[az-network-private-dns-record-set-a-add-record]: /cli/azure/network/private-dns-record/set/a#az-network-private-dns-record-set-a-add-record
[az-resource-show]: /cli/azure/resource#az-resource-show
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com
