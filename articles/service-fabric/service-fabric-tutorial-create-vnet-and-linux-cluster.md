---
title: Tworzenie klastra usługi Service Fabric systemu Linux na platformie Azure | Microsoft Docs
description: Z tego samouczka dowiesz się, jak wdrożyć klaster usługi Service Fabric systemu Linux w istniejącej sieci wirtualnej platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/27/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: b494da1c87feafd1b9db8485d16a9dcf5b999e3d
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2019
ms.locfileid: "55101809"
---
# <a name="tutorial-deploy-a-linux-service-fabric-cluster-into-an-azure-virtual-network"></a>Samouczek: wdrażanie klastra usługi Service Fabric systemu Linux w sieci wirtualnej platformy Azure

Niniejszy samouczek jest pierwszą częścią serii. Dowiesz się w nim, jak wdrożyć klaster usługi Service Fabric systemu Linux w [sieci wirtualnej (VNET) platformy Azure](../virtual-network/virtual-networks-overview.md) za pomocą szablonu i interfejsu wiersza polecenia platformy Azure. Po wykonaniu tych czynności powstanie działający w chmurze klaster, w którym można będzie wdrażać aplikacje. Aby utworzyć klaster systemu Windows za pomocą programu PowerShell, zobacz [Tworzenie bezpiecznego klastra systemu Windows na platformie Azure](service-fabric-tutorial-create-vnet-and-windows-cluster.md).

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie sieci wirtualnej na platformie Azure przy użyciu interfejsu wiersza polecenia platformy Azure
> * Tworzenie bezpiecznego klastra usługi Service Fabric na platformie Azure przy użyciu interfejsu wiersza polecenia platformy Azure
> * Zabezpieczanie klastra za pomocą certyfikatu X.509
> * Łączenie się z klastrem przy użyciu interfejsu wiersza polecenia usługi Service Fabric
> * Usuwanie klastra

Ta seria samouczków zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Tworzenie bezpiecznego klastra na platformie Azure
> * [Skalowanie klastra na zewnątrz lub do wewnątrz](service-fabric-tutorial-scale-cluster.md)
> * [Uaktualnianie środowiska uruchomieniowego klastra](service-fabric-tutorial-upgrade-cluster.md)
> * [Usuwanie klastra](service-fabric-tutorial-delete-cluster.md)

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka:

* Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Zainstaluj [interfejs wiersza polecenia usługi Service Fabric](service-fabric-cli.md).
* Zainstaluj [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli)

Poniższe procedury umożliwiają utworzenie klastra usługi Service Fabric z pięcioma węzłami. Aby obliczyć koszt działania klastra usługi Service Fabric na platformie Azure, skorzystaj z [Kalkulatora cen platformy Azure](https://azure.microsoft.com/pricing/calculator/).

## <a name="key-concepts"></a>Kluczowe pojęcia

[Klaster usługi Service Fabric](service-fabric-deploy-anywhere.md) jest połączonym z siecią zestawem maszyn wirtualnych lub fizycznych, w którym wdraża się mikrousługi i nimi zarządza. Klastry mogą obejmować nawet tysiące maszyn. Maszyna lub maszyna wirtualna, która jest częścią klastra, jest nazywana węzłem. Każdy węzeł ma przypisaną nazwę węzła (ciąg). Węzły mają swoje właściwości — na przykład właściwości dotyczące umieszczania.

Typ węzła definiuje rozmiar, liczbę i właściwości zestawu maszyn wirtualnych w klastrze. Każdy zdefiniowany typ węzła jest konfigurowany jako [zestaw skalowania maszyn wirtualnych](/azure/virtual-machine-scale-sets/), czyli zasób obliczeniowy platformy Azure używany do wdrażania kolekcji maszyn wirtualnych i zarządzania nimi. Następnie każdy typ węzła może być niezależnie skalowany w górę lub w dół oraz może mieć różne zestawy otwartych portów i różne metryki pojemności. Typy węzłów służą do definiowania ról zestawu węzłów klastra, takich jak „fronton” lub „zaplecze”.  Klaster może mieć więcej niż jeden typ węzła, ale podstawowy typ węzła musi obejmować co najmniej pięć maszyn wirtualnych w przypadku klastrów produkcyjnych (lub co najmniej trzy maszyny wirtualne w przypadku klastrów testowych).  [Usługi systemowe Service Fabric](service-fabric-technical-overview.md#system-services) są umieszczane w węzłach podstawowego typu.

Klaster jest zabezpieczony przy użyciu certyfikatu klastra. Certyfikat klastra to certyfikat X.509 używany do zabezpieczania komunikacji między węzłami i uwierzytelniania punktów końcowych zarządzania klastrem na kliencie zarządzania.  Certyfikat klastra zapewnia również protokół SSL dla interfejsu API zarządzania protokołu HTTPS i narzędzia Service Fabric Explorer za pośrednictwem protokołu HTTPS. Certyfikaty z podpisem własnym są przydatne w przypadku klastrów testowych.  W przypadku klastrów produkcyjnych jako certyfikatu klastra należy używać certyfikatu z urzędu certyfikacji.

Certyfikat klastra musi spełniać następujące wymagania:

* Zawiera klucz prywatny.
* Został utworzony na potrzeby wymiany kluczy, co umożliwia eksport do pliku wymiany informacji osobistych (pfx).
* Ma nazwę podmiotu zgodną z domeną używaną w celu uzyskiwania dostępu do klastra usługi Service Fabric. To dopasowanie jest wymagane, aby można było zapewnić protokół SSL dla punktów końcowych zarządzania protokołu HTTPS klastra i dla narzędzia Service Fabric Explorer. Nie można uzyskać certyfikatu SSL od urzędu certyfikacji dla domeny .cloudapp.azure.com. Musisz uzyskać niestandardową nazwę domeny dla klastra. W przypadku żądania certyfikatu od urzędu certyfikacji nazwa podmiotu certyfikatu musi być zgodna z niestandardową nazwą domeny używaną dla danego klastra.

Usługa Azure Key Vault służy do zarządzania certyfikatami klastrów usługi Service Fabric na platformie Azure.  Po wdrożeniu klastra na platformie Azure dostawca zasobów platformy Azure odpowiedzialny za utworzenie klastrów usługi Service Fabric pobiera certyfikaty z usługi Key Vault i instaluje je w klastrze maszyn wirtualnych.

W tym samouczku przedstawiono wdrożenie klastra z pięcioma węzłami o tym samym typie. Ważnym etapem wdrożeń produkcyjnych jest [planowanie pojemności](service-fabric-cluster-capacity.md). Poniżej przedstawiono niektóre zagadnienia, które należy uwzględnić w ramach tego procesu.

* Liczba węzłów oraz typy węzłów wymagane dla danego klastra
* Właściwości poszczególnych typów węzłów (np. rozmiar, typ podstawowy, połączenie z Internetem i liczba maszyn wirtualnych)
* Charakterystyka niezawodności i trwałości klastra

## <a name="download-and-explore-the-template"></a>Pobieranie i eksplorowanie szablonu

Pobierz poniższe pliki szablonu usługi Resource Manager:

* [AzureDeploy.json][template]
* [AzureDeploy.Parameters.json][parameters]

Ten szablon umożliwia wdrożenie zabezpieczonego klastra pięciu maszyn wirtualnych z jednym typem węzła w sieci wirtualnej.  Inne przykładowe szablony można znaleźć w witrynie [GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates). Plik [AzureDeploy.json][template] umożliwia wdrożenie szeregu zasobów, w tym następujących.

### <a name="service-fabric-cluster"></a>Klaster usługi Service Fabric

W zasobie **Microsoft.ServiceFabric/clusters** został wdrożony klaster systemu Linux o następujących właściwościach:

* Jeden typ węzła
* Pięć węzłów o podstawowym typie (z możliwością konfiguracji za pomocą parametrów szablonu)
* System operacyjny: Ubuntu 16.04 LTS (z możliwością konfiguracji w parametrach szablonu)
* Zabezpieczenie przy użyciu certyfikatu (z możliwością konfiguracji za pomocą parametrów szablonu)
* [Usługa DNS](service-fabric-dnsservice.md) jest włączona
* [Poziom trwałości](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster): Brązowy (z możliwością konfiguracji za pomocą parametrów szablonu)
* [Poziom niezawodności](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster): Srebrny (z możliwością konfiguracji za pomocą parametrów szablonu)
* Punkt końcowy połączenia klienta: 19000 (z możliwością konfiguracji w parametrach szablonu)
* Punkt końcowy bramy protokołu HTTP: 19080 (z możliwością konfiguracji w parametrach szablonu)

### <a name="azure-load-balancer"></a>Moduł równoważenia obciążenia platformy Azure

W zasobie **Microsoft.Network/loadBalancers** skonfigurowano moduł równoważenia obciążenia, a sondy i reguły skonfigurowano dla następujących portów:

* Punkt końcowy połączenia klienta: 19000
* Punkt końcowy bramy protokołu HTTP: 19080
* Port aplikacji: 80
* Port aplikacji: 443

### <a name="virtual-network-and-subnet"></a>Sieć wirtualna i podsieć

Nazwy sieci wirtualnej i podsieci są deklarowane w parametrach szablonu.  Przestrzenie adresowe sieci wirtualnej i podsieci również są deklarowane w parametrach szablonu i skonfigurowane w zasobie **Microsoft.Network/virtualNetworks**:

* Przestrzeń adresowa sieci wirtualnej: 10.0.0.0/16
* Przestrzeń adresowa podsieci usługi Service Fabric: 10.0.2.0/24

Jeśli będą potrzebne dowolne inne porty aplikacji, będzie trzeba dostosować zasób Microsoft.Network/loadBalancers, aby zezwolić na ruch przychodzący.

## <a name="set-template-parameters"></a>Ustawianie parametrów szablonu

Plik parametrów [AzureDeploy.Parameters][parameters] deklaruje wiele wartości służących do wdrażania klastra i skojarzonych zasobów. Niektóre parametry, które być może będzie trzeba zmodyfikować na potrzeby danego wdrożenia:

|Parametr|Przykładowa wartość|Uwagi|
|---|---||
|adminUserName|vmadmin| Nazwa użytkownika będącego administratorem maszyn wirtualnych klastra. |
|adminPassword|Haslo#1234| Hasło administratora maszyn wirtualnych klastra.|
|clusterName|mojklastersf123| Nazwa klastra. |
|location|southcentralus| Lokalizacja klastra. |
|certificateThumbprint|| <p>W przypadku tworzenia certyfikatu z podpisem własnym lub podania pliku certyfikatu ta wartość powinna być pusta.</p><p>Aby użyć istniejącego certyfikatu, który został wcześniej przekazany do magazynu kluczy, wprowadź wartość odcisku palca SHA1 certyfikatu. Na przykład „6190390162C988701DB5676EB81083EA608DCCF3”. </p>|
|certificateUrlValue|| <p>W przypadku tworzenia certyfikatu z podpisem własnym lub podania pliku certyfikatu ta wartość powinna być pusta.</p><p>Aby użyć istniejącego certyfikatu, który został wcześniej przekazany do magazynu kluczy, wprowadź adres URL certyfikatu. Na przykład „https://mykeyvault.vault.azure.net:443/secrets/mycertificate/02bea722c9ef4009a76c5052bcbf8346”.</p>|
|sourceVaultValue||<p>W przypadku tworzenia certyfikatu z podpisem własnym lub podania pliku certyfikatu ta wartość powinna być pusta.</p><p>Aby użyć istniejącego certyfikatu, który został wcześniej przekazany do magazynu kluczy, wprowadź wartość magazynu źródłowego. Na przykład „/subscriptions/333cc2c84-12fa-5778-bd71-c71c07bf873f/resourceGroups/MyTestRG/providers/Microsoft.KeyVault/vaults/MYKEYVAULT”.</p>|

<a id="createvaultandcert" name="createvaultandcert_anchor"></a>

## <a name="deploy-the-virtual-network-and-cluster"></a>Wdrażanie sieci wirtualnej i klastra

Następnym etapem jest skonfigurowanie topologii sieci i wdrożenie klastra usługi Service Fabric. Plik [AzureDeploy.json][template] szablonu usługi Resource Manager tworzy sieć wirtualną (VNET) i podsieć dla usługi Service Fabric. Szablon pozwala również wdrożyć klaster z włączonymi zabezpieczeniami opartymi na certyfikacie.  W przypadku klastrów produkcyjnych jako certyfikatu klastra należy używać certyfikatu z urzędu certyfikacji. Do zabezpieczenia klastrów testowych może służyć certyfikat z podpisem własnym.

### <a name="create-a-cluster-using-an-existing-certificate"></a>Tworzenie klastra przy użyciu istniejącego certyfikatu

W poniższym skrypcie wdrożenie nowego klastra zabezpieczonego przy użyciu istniejącego certyfikatu odbywa się za pomocą polecenia [az sf cluster create](/cli/azure/sf/cluster?view=azure-cli-latest#az_sf_cluster_create) oraz szablonu. Polecenie to powoduje także utworzenie nowego magazynu kluczy na platformie Azure i przekazanie danego certyfikatu.

```azurecli
ResourceGroupName="sflinuxclustergroup"
Location="southcentralus"
Password="q6D7nN%6ck@6"
VaultName="linuxclusterkeyvault"
VaultGroupName="linuxclusterkeyvaultgroup"
CertPath="C:\MyCertificates\MyCertificate.pem"

# sign in to your Azure account and select your subscription
az login
az account set --subscription <guid>

# Create a new resource group for your deployment and give it a name and a location.
az group create --name $ResourceGroupName --location $Location

# Create the Service Fabric cluster.
az sf cluster create --resource-group $ResourceGroupName --location $Location \
   --certificate-password $Password --certificate-file $CertPath \
   --vault-name $VaultName --vault-resource-group $ResourceGroupName  \
   --template-file AzureDeploy.json --parameter-file AzureDeploy.Parameters.json
```

### <a name="create-a-cluster-using-a-new-self-signed-certificate"></a>Tworzenie klastra przy użyciu nowego certyfikatu z podpisem własnym

Poniższy skrypt używa polecenia [az sf cluster create](/cli/azure/sf/cluster?view=azure-cli-latest#az_sf_cluster_create) i szablonu, aby wdrożyć nowy klaster na platformie Azure. Polecenie tworzy również nowy magazyn kluczy na platformie Azure, dodaje nowy certyfikat z podpisem własnym do magazynu kluczy i pobiera plik certyfikatu do środowiska lokalnego.

```azurecli
ResourceGroupName="sflinuxclustergroup"
ClusterName="sflinuxcluster"
Location="southcentralus"
Password="q6D7nN%6ck@6"
VaultName="linuxclusterkeyvault"
VaultGroupName="linuxclusterkeyvaultgroup"
CertPath="C:\MyCertificates"

az sf cluster create --resource-group $ResourceGroupName --location $Location --cluster-name $ClusterName --template-file C:\temp\cluster\AzureDeploy.json --parameter-file C:\temp\cluster\AzureDeploy.Parameters.json --certificate-password $Password --certificate-output-folder $CertPath --certificate-subject-name $ClusterName.$Location.cloudapp.azure.com --vault-name $VaultName --vault-resource-group $ResourceGroupName
```

## <a name="connect-to-the-secure-cluster"></a>Nawiązywanie połączenia z zabezpieczonym klastrem

Połącz się z klastrem przy użyciu polecenia `sfctl cluster select` interfejsu wiersza polecenia usługi Service Fabric oraz swojego klucza.  Użyj opcji **--no-verify** tylko w przypadku certyfikatu z podpisem własnym.

```azurecli
sfctl cluster select --endpoint https://aztestcluster.southcentralus.cloudapp.azure.com:19080 \
--pem ./aztestcluster201709151446.pem --no-verify
```

Za pomocą polecenia `sfctl cluster health` sprawdź poprawność połączenia i upewnij się, że klaster jest w dobrej kondycji.

```azurecli
sfctl cluster health
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

W pozostałych artykułach w tej serii samouczków jest używany utworzony właśnie klaster. Jeśli nie przechodzisz od razu do następnego artykułu, rozważ [usunięcie klastra](service-fabric-cluster-delete.md), aby uniknąć naliczania opłat.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie sieci wirtualnej na platformie Azure przy użyciu interfejsu wiersza polecenia platformy Azure
> * Tworzenie bezpiecznego klastra usługi Service Fabric na platformie Azure przy użyciu interfejsu wiersza polecenia platformy Azure
> * Zabezpieczanie klastra za pomocą certyfikatu X.509
> * Łączenie się z klastrem przy użyciu interfejsu wiersza polecenia usługi Service Fabric
> * Usuwanie klastra

Przejdź do kolejnego samouczka, aby dowiedzieć się, jak skalować klaster.
> [!div class="nextstepaction"]
> [Skalowanie klastra](service-fabric-tutorial-scale-cluster.md)

[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/5-VM-Ubuntu-1-NodeTypes-Secure/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/5-VM-Ubuntu-1-NodeTypes-Secure/AzureDeploy.Parameters.json
