---
title: Wdrażanie rozwiązania OpenShift Container Platform na platformie Azure | Dokumentacja firmy Microsoft
description: Wdrażanie rozwiązania OpenShift Container Platform na platformie Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/18/2019
ms.author: haroldw
ms.openlocfilehash: 664099322bef3ac85d980fbe5e43dcc49cba862b
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2019
ms.locfileid: "65411562"
---
# <a name="deploy-openshift-container-platform-in-azure"></a>Wdrażanie rozwiązania OpenShift Container Platform na platformie Azure

Do wdrożenia rozwiązania OpenShift Container Platform na platformie Azure, można użyć jednej z kilku metod:

- Można ręcznie wdrożyć składniki niezbędne infrastruktury platformy Azure, a następnie wykonaj [dokumentacja platformy OpenShift Container Platform](https://docs.openshift.com/container-platform).
- Można także użyć istniejącego [szablonu usługi Resource Manager](https://github.com/Microsoft/openshift-container-platform/) upraszczające proces wdrożenia klastra OpenShift Container Platform.
- Innym rozwiązaniem jest użycie [oferty w portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview).

Wszystkie opcje wymagana jest subskrypcja firmy Red Hat. Podczas wdrażania wystąpienie Red Hat Enterprise Linux jest zarejestrowany do subskrypcji Red Hat i dołączony do identyfikator puli, który zawiera uprawnień dla rozwiązania OpenShift Container Platform.
Upewnij się, że masz prawidłowe Red Hat subskrypcji Menedżera (RHSM) nazwa użytkownika, hasło i identyfikator puli. Można użyć klucza aktywacji, Org ID i identyfikator puli. Możesz sprawdzić te informacje, logując się do https://access.redhat.com.


## <a name="deploy-using-the-openshift-container-platform-resource-manager-template"></a>Wdrażanie przy użyciu szablonu usługi Resource Manager platformy OpenShift Container Platform

### <a name="private-clusters"></a>Klastry prywatne

Wdrażanie prywatnego klastrów platformy OpenShift wymaga więcej niż tylko nie ma publicznego adresu IP skojarzone z modułem równoważenia obciążenia głównej (Konsola sieci web) lub do infrastruktury usługi load balancer (router).  Klaster prywatny jest ogólnie używa niestandardowego serwera DNS (nie Domyślnie usługa Azure DNS), niestandardową nazwę domeny (np. contoso.com) i wstępnie zdefiniowanych sieci wirtualnych.  W przypadku klastrów prywatnej należy skonfigurować sieci wirtualnej ze wszystkimi odpowiednie podsieci i ustawienia serwera DNS z wyprzedzeniem.  Następnie użyj **existingMasterSubnetReference**, **existingInfraSubnetReference**, **existingCnsSubnetReference**, i  **existingNodeSubnetReference** Aby określić istniejącą podsieć do użycia przez klaster.

Jeśli wybrano wzorzec prywatne (**masterClusterType**= prywatny), statyczny prywatny adres IP musi być określona dla **masterPrivateClusterIp**.  Ten adres IP zostanie przypisany do frontonu modułu równoważenia obciążenia głównej.  Adres IP musi być CIDR wzorca podsieci i jest nie używane.  **masterClusterDnsType** musi być ustawiona na "niestandardowe" i główną, należy podać nazwę DNS **masterClusterDns**.  Nazwa DNS musi być mapowane statyczny prywatny adres IP i będzie służyć do dostępu do konsoli w węźle głównym.

Jeśli wybrano opcję prywatnej routera (**routerClusterType**= prywatny), statyczny prywatny adres IP musi być określona dla **routerPrivateClusterIp**.  Ten adres IP zostanie przypisany do przedniego końca infra modułu równoważenia obciążenia.  Adres IP musi być CIDR dla infra podsieci i nie jest używany.  **routingSubDomainType** musi być ustawiona na "niestandardowe" i wieloznaczną nazwę DNS dla routingu wymaga podania **routingSubDomain**.  

Jeśli wybrano prywatnej wzorców i prywatne routera, również należy wprowadzić nazwę domeny niestandardowej dla **nazwa_domeny**

Po pomyślnym wdrożeniu węzeł bastionu jest tylko węzeł z publicznym adresem IP, jak ssh do.  Nawet jeśli węzłów głównych są skonfigurowane na potrzeby dostępu publicznego, ich nie są widoczne dla protokołu ssh dostępu.

Aby wdrożyć przy użyciu szablonu usługi Resource Manager, umożliwia pliku parametrów podać parametry wejściowe. Aby dostosować wdrożenia, rozwidlenie repozytorium GitHub i zmień odpowiednie elementy.

Niektóre typowe opcje dostosowywania obejmują, ale nie są ograniczone do:

- Rozmiar maszyny Wirtualnej bastionu (zmienna w azuredeploy.json)
- Konwencje nazewnictwa (zmienne w azuredeploy.json)
- Szczegóły klastra OpenShift, zmodyfikować za pomocą pliku hosts (deployOpenShift.sh)

### <a name="configure-the-parameters-file"></a>Konfigurowanie pliku parametrów

[Szablonu OpenShift Container Platform](https://github.com/Microsoft/openshift-container-platform) ma wiele gałęzi dostępne dla różnych wersji OpenShift Container Platform.  Zgodnie z potrzebami, można wdrażać bezpośrednio z repozytorium lub można utworzyć Rozgałęzienie repozytorium i zmiany niestandardowych szablonów lub skryptów przed wdrożeniem.

Użyj `appId` wartości z nazwy głównej usługi została wcześniej utworzona dla `aadClientId` parametru.

Poniższy przykład pokazuje plik parametrów o nazwie azuredeploy.parameters.json przy użyciu wszystkich wymaganych danych wejściowych.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "_artifactsLocation": {
            "value": "https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master"
        },
        "location": {
            "value": "eastus"
        },
        "masterVmSize": {
            "value": "Standard_E2s_v3"
        },
        "infraVmSize": {
            "value": "Standard_D4s_v3"
        },
        "nodeVmSize": {
            "value": "Standard_D4s_v3"
        },
        "cnsVmSize": {
            "value": "Standard_E4s_v3"
        },
        "osImageType": {
            "value": "defaultgallery"
        },
        "marketplaceOsImage": {
            "value": {
                "publisher": "RedHat",
                "offer": "RHEL",
                "sku": "7-RAW",
                "version": "latest"
            }
        },
        "storageKind": {
            "value": "changeme"
        },
        "openshiftClusterPrefix": {
            "value": "changeme"
        },
        "minorVersion": {
            "value": "69"
        },
        "masterInstanceCount": {
            "value": 3
        },
        "infraInstanceCount": {
            "value": 3
        },
        "nodeInstanceCount": {
            "value": 3
        },
        "cnsInstanceCount": {
            "value": 3
        },
        "osDiskSize": {
            "value": 64
        },
        "dataDiskSize": {
            "value": 64
        },
        "cnsGlusterDiskSize": {
            "value": 128
        },
        "adminUsername": {
            "value": "changeme"
        },
        "enableMetrics": {
            "value": "false"
        },
        "enableLogging": {
            "value": "false"
        },
        "enableCNS": {
            "value": "false"
        },
        "rhsmUsernameOrOrgId": {
            "value": "changeme"
        },
        "rhsmPoolId": {
            "value": "changeme"
        },
        "rhsmBrokerPoolId": {
            "value": "changeme"
        },
        "sshPublicKey": {
            "value": "GEN-SSH-PUB-KEY"
        },
        "keyVaultSubscriptionId": {
            "value": "255a325e-8276-4ada-af8f-33af5658eb34"
        },
        "keyVaultResourceGroup": {
            "value": "changeme"
        },
        "keyVaultName": {
            "value": "changeme"
        },
        "enableAzure": {
            "value": "true"
        },
        "aadClientId": {
            "value": "changeme"
        },
        "domainName": {
            "value": "contoso.com"
        },
        "masterClusterDnsType": {
            "value": "default"
        },
        "masterClusterDns": {
            "value": "console.contoso.com"
        },
        "routingSubDomainType": {
            "value": "nipio"
        },
        "routingSubDomain": {
            "value": "apps.contoso.com"
        },
        "virtualNetworkNewOrExisting": {
            "value": "new"
        },
        "virtualNetworkName": {
            "value": "changeme"
        },
        "addressPrefixes": {
            "value": "10.0.0.0/14"
        },
        "masterSubnetName": {
            "value": "changeme"
        },
        "masterSubnetPrefix": {
            "value": "10.1.0.0/16"
        },
        "infraSubnetName": {
            "value": "changeme"
        },
        "infraSubnetPrefix": {
            "value": "10.2.0.0/16"
        },
        "nodeSubnetName": {
            "value": "changeme"
        },
        "nodeSubnetPrefix": {
            "value": "10.3.0.0/16"
        },
        "existingMasterSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/mastersubnet"
        },
        "existingInfraSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/infrasubnet"
        },
        "existingCnsSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/cnssubnet"
        },
        "existingNodeSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/nodesubnet"
        },
        "masterClusterType": {
            "value": "public"
        },
        "masterPrivateClusterIp": {
            "value": "10.1.0.200"
        },
        "routerClusterType": {
            "value": "public"
        },
        "routerPrivateClusterIp": {
            "value": "10.2.0.200"
        },
        "routingCertType": {
            "value": "selfsigned"
        },
        "masterCertType": {
            "value": "selfsigned"
        }
    }
}
```

Zastąp parametry konkretnych informacji.

Różne wersje mogą mieć różnych parametrów, dlatego Sprawdź niezbędne parametry dla gałęzi, którego używasz.

### <a name="azuredeployparametersjson-file-explained"></a>azuredeploy. Opis pliku parameters.JSON

| Właściwość | Opis | Prawidłowe opcje | Wartość domyślna |
|----------|-------------|---------------|---------------|
| `_artifactsLocation`  | Adres URL dla artefaktów (json, skrypty itp.) |  |  https:\//raw.githubusercontent.com/Microsoft/openshift-container-platform/master  |
| `location` | Region platformy Azure do wdrażania zasobów |  |  |
| `masterVmSize` | Rozmiar maszyny Wirtualnej wzorca. Wybierz jedną z dozwolonych rozmiarów maszyn wirtualnych wymienionych w pliku azuredeploy.json |  | Standardowa_E2s_v3 |
| `infraVmSize` | Rozmiar Infra maszyny Wirtualnej. Wybierz jedną z dozwolonych rozmiarów maszyn wirtualnych wymienionych w pliku azuredeploy.json |  | Standardowa_D4s_v3 |
| `nodeVmSize` | Rozmiar węzła aplikacji maszyny Wirtualnej. Wybierz jedną z dozwolonych rozmiarów maszyn wirtualnych wymienionych w pliku azuredeploy.json |  | Standardowa_D4s_v3 |
| `cnsVmSize` | Rozmiar węzła magazynu natywnego (CNS) kontenera maszyny Wirtualnej. Wybierz jedną z dozwolonych rozmiarów maszyn wirtualnych wymienionych w pliku azuredeploy.json |  | Standardowa_E4s_v3 |
| `osImageType` | RHEL obrazu do użycia. defaultgallery: Na żądanie; Portal Marketplace: obraz innych firm | defaultgallery <br> marketplace | defaultgallery |
| `marketplaceOsImage` | Jeśli `osImageType` jest witryna marketplace, a następnie wprowadź odpowiednie wartości dla "publisher", "oferują", "sku", wersja i oferty w portalu marketplace. Ten parametr jest typu obiektu |  |  |
| `storageKind` | Typ magazynu do użycia  | Zarządzane<br> niezarządzane | Zarządzane |
| `openshiftClusterPrefix` | Klaster prefiks używany do konfigurowania nazw hostów dla wszystkich węzłów.  Od 1 do 20 znaków |  | mycluster |
| `minoVersion` | Wersja pomocnicza OpenShift Container Platform 3.11 do wdrożenia |  | 69 |
| `masterInstanceCount` | Liczba węzłów wzorców do wdrożenia | 1, 3, 5 | 3 |
| `infraInstanceCount` | Liczba infra węzłów do wdrożenia | 1, 2, 3 | 3 |
| `nodeInstanceCount` | Liczba węzłów do wdrożenia | 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30 | 2 |
| `cnsInstanceCount` | Liczba węzłów CNS do wdrożenia | 3, 4 | 3 |
| `osDiskSize` | Rozmiar dysku systemu operacyjnego dla maszyny Wirtualnej (w GB) | 64, 128, 256, 512, 1024, 2048 | 64 |
| `dataDiskSize` | Rozmiar dysku danych do dołączenia do węzłów dla woluminu platformy Docker (w GB) | 32, 64, 128, 256, 512, 1024, 2048 | 64 |
| `cnsGlusterDiskSize` | Rozmiar dysku danych do dołączenia do węzłów CNS do użytku przez glusterfs (w GB | 32, 64, 128, 256, 512, 1024, 2048 | 128 |
| `adminUsername` | Nazwa użytkownika administratora dla nazwy logowania systemu operacyjnego (VM) i początkowej użytkownika platformy OpenShift |  | ocpadmin |
| `enableMetrics` | Włącz metryki. Metryki wymaga większej ilości zasobów więc wybierz rozmiar właściwe dla infrastruktury maszyny Wirtualnej | true <br> false | false |
| `enableLogging` | Włącz rejestrowanie. Zasobnik elasticsearch wymaga 8 GB pamięci RAM więc wybierz rozmiar właściwe dla infrastruktury maszyny Wirtualnej | true <br> false | false |
| `enableCNS` | Włączanie natywnego pamięci kontenera | true <br> false | false |
| `rhsmUsernameOrOrgId` | Red Hat Menedżer subskrypcji, nazwę użytkownika lub organizacji identyfikator |  |  |
| `rhsmPoolId` | Identyfikator Red Hat subskrypcji Menedżera puli, zawierający uwzględniane OpenShift dla węzłów obliczeniowych |  |  |
| `rhsmBrokerPoolId` | Red Hat subskrypcji Menedżera puli identyfikator który zawiera węzły uwzględniane OpenShift wzorców i infrastruktury. Jeśli nie masz inną pulę identyfikatorów, wprowadź ten sam identyfikator puli jako "rhsmPoolId" |  |
| `sshPublicKey` | Skopiuj z protokołu SSH oraz publicznego klucza w tym miejscu |  |  |
| `keyVaultSubscriptionId` | Identyfikator subskrypcji, która zawiera usługę Key Vault |  |  |
| `keyVaultResourceGroup` | Nazwa grupy zasobów, który zawiera usługi Key Vault |  |  |
| `keyVaultName` | Nazwa usługi Key Vault, został utworzony |  |  |
| `enableAzure` | Włącz dostawca usług w chmurze platformy Azure | true <br> false | true |
| `aadClientId` | Usługa Azure identyfikator klienta Active Directory tzw. Identyfikator aplikacji dla jednostki usługi |  |  |
| `domainName` | Nazwa niestandardowej nazwy domeny do użycia (jeśli dotyczy). Wartość "none" w przeciwnym razie wdrażanie klastra pełnej prywatne |  | brak |
| `masterClusterDnsType` | Typ domeny dla konsoli sieci web platformy OpenShift. "default" użyje Etykieta DNS serwera głównego infra publicznego adresu IP. "custom" pozwala na zdefiniowanie swoją własną nazwę | domyślny <br> niestandardowe | domyślny |
| `masterClusterDns` | Niestandardową nazwę DNS na potrzeby dostępu do konsoli sieci web platformy OpenShift, w przypadku wybrania "custom" dla `masterClusterDnsType` |  | console.contoso.com |
| `routingSubDomainType` | Jeśli ustawiona na "nipio" `routingSubDomain` użyje nip.io.  Użyj "custom", jeśli masz własną domenę, którego chcesz użyć do routingu | nipio <br> niestandardowe | nipio |
| `routingSubDomain` | Wieloznaczną nazwę DNS, którego chcesz użyć do routingu w przypadku wybrania "custom" dla `routingSubDomainType` |  | apps.contoso.com |
| `virtualNetworkNewOrExisting` | Wybierz, czy chcesz użyć istniejącej sieci wirtualnej lub utworzyć nową sieć wirtualną | istniejące <br> nowy | nowy |
| `virtualNetworkResourceGroupName` | Nazwa grupy zasobów dla nowej sieci wirtualnej w przypadku wybrania przycisku "new" dla `virtualNetworkNewOrExisting` |  | resourceGroup().name |
| `virtualNetworkName` | Nazwa nowej sieci wirtualnej, aby utworzyć w przypadku wybrania przycisku "new" dla `virtualNetworkNewOrExisting` |  | openshiftvnet |
| `addressPrefixes` | Prefiks adresu nową sieć wirtualną |  | 10.0.0.0/14 |
| `masterSubnetName` | Nazwa głównego podsieci |  | mastersubnet |
| `masterSubnetPrefix` | CIDR używany dla podsieci master - musi być podzestawem prefiks adresu |  | 10.1.0.0/16 |
| `infraSubnetName` | Nazwa infra podsieci |  | infrasubnet |
| `infraSubnetPrefix` | CIDR umożliwiający infra podsieci - musi być podzestawem prefiks adresu |  | 10.2.0.0/16 |
| `nodeSubnetName` | Nazwa podsieci węzła |  | nodesubnet |
| `nodeSubnetPrefix` | CIDR używany dla podsieci węzeł - musi być podzbiorem prefiks adresu |  | 10.3.0.0/16 |
| `existingMasterSubnetReference` | Pełną dokumentację do istniejącej podsieci dla węzłów głównych. Nie wymagane w przypadku tworzenia nowej sieci wirtualnej / podsieci |  |  |
| `existingInfraSubnetReference` | Pełna odwołanie do istniejącej podsieci dla infra węzłów. Nie wymagane w przypadku tworzenia nowej sieci wirtualnej / podsieci |  |  |
| `existingCnsSubnetReference` | Pełną dokumentację na istniejącą podsieć CNS węzłów. Nie wymagane w przypadku tworzenia nowej sieci wirtualnej / podsieci |  |  |
| `existingNodeSubnetReference` | Pełną dokumentację na istniejącą podsieć dla węzłów obliczeniowych. Nie wymagane w przypadku tworzenia nowej sieci wirtualnej / podsieci |  |  |
| `masterClusterType` | Określ, czy klaster używa prywatnej lub publicznej węzłów głównych. Jeśli wybrano prywatne, węzły główne nie będą dostępne do Internetu za pośrednictwem publicznego adresu IP. Zamiast tego należy użyć prywatnym adresem IP określone w `masterPrivateClusterIp` | publiczny <br> prywatna | publiczny |
| `masterPrivateClusterIp` | W przypadku prywatnych węzłów głównych następnie prywatny adres IP jest wymagane do użycia przez wewnętrzny moduł równoważenia obciążenia dla węzłów głównych. To statyczny adres IP musi być w bloku CIDR wzorca podsieci i nie jest jeszcze używana. W przypadku publicznych węzłów głównych tej wartości nie będą używane, ale nadal musi być określona |  | 10.1.0.200 |
| `routerClusterType` | Określ, czy klaster używa opcji prywatny lub publiczny infra węzłów. Jeśli wybrano prywatne, infra węzłów nie będą dostępne do Internetu za pośrednictwem publicznego adresu IP. Zamiast tego należy użyć prywatnym adresem IP określone w `routerPrivateClusterIp` | publiczny <br> prywatna | publiczny |
| `routerPrivateClusterIp` | Jeśli prywatne infra węzły są zaznaczone, a następnie prywatny adres IP musi być określona dla użyć wewnętrznego modułu równoważenia obciążenia dla infra węzłów. To statyczny adres IP musi być w bloku CIDR wzorca podsieci i nie jest jeszcze używana. Jeśli jest to publiczna infra węzły są zaznaczone, ta wartość nie będą używane, ale musi być określona |  | 10.2.0.200 |
| `routingCertType` | Użyj niestandardowego certyfikatu dla domeny routingu lub certyfikatu z podpisem własnym domyślne — wykonaj instrukcje z sekcji **niestandardowych certyfikatów** sekcji | selfsigned <br> niestandardowe | selfsigned |
| `masterCertType` | Użyj niestandardowego certyfikatu dla domeny głównej lub certyfikatu z podpisem własnym domyślne — wykonaj instrukcje z sekcji **niestandardowych certyfikatów** sekcji | selfsigned <br> niestandardowe | selfsigned |

<br>

### <a name="deploy-using-azure-cli"></a>Wdrażanie przy użyciu wiersza polecenia platformy Azure

> [!NOTE] 
> Poniższe polecenie wymaga interfejsu wiersza polecenia Azure 2.0.8 lub nowszej. Możesz sprawdzić wersję interfejsu wiersza polecenia przy użyciu `az --version` polecenia. Aby zaktualizować wersję interfejsu wiersza polecenia, zobacz [interfejsu wiersza polecenia platformy Azure Zainstaluj](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latesti).

Poniższy przykład służy do wdrażania klastra OpenShift i wszystkie powiązane zasoby w grupie zasobów o nazwie openshiftrg, o nazwie wdrożenia myOpenShiftCluster. Szablon odwołuje się bezpośrednio z repozytorium GitHub i parametrów lokalnych, który jest używany plik o nazwie pliku azuredeploy.parameters.json.

```azurecli 
az group deployment create -g openshiftrg --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

Przeprowadzenie wdrożenia zajmuje co najmniej 60 minut, na podstawie całkowitej liczby wdrożonych węzłów i skonfigurowanych opcji. Bastionu FQDN DNS i adres URL konsoli usługi OpenShift drukuje do terminalu, po zakończeniu wdrożenia.

```json
{
  "Bastion DNS FQDN": "bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com",
  "OpenShift Console URL": "http://openshiftlb.eastus.cloudapp.azure.com/console"
}
```

Jeśli nie chcesz blokowały wiersza polecenia, oczekiwanie na wdrożenie w celu ukończenia, Dodaj `--no-wait` jako jedną z opcji dla wdrożenia grupy. Dane wyjściowe z wdrożenia mogą być pobierane z witryny Azure portal w sekcji Wdrażanie dla grupy zasobów.

## <a name="connect-to-the-openshift-cluster"></a>Nawiąż połączenie z klastrem platformy OpenShift

Po zakończeniu wdrożenia, należy pobrać połączenie z sekcji wyjściowej wdrożenia. Łączenie z konsolą OpenShift za pomocą przeglądarki przy użyciu **adres URL konsoli OpenShift**. Można również SSH z hostem bastionu. Poniżej znajduje się przykład, w którym nazwa użytkownika administratora jest clusteradmin i publiczny adres IP bastionu pełni kwalifikowaną nazwę domeny DNS jest bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com:

```bash
$ ssh clusteradmin@bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Użyj [usunięcie grupy az](/cli/azure/group) polecenia, aby usunąć grupę zasobów, klaster OpenShift i wszystkie pokrewne zasoby, gdy nie są już potrzebne.

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>Kolejne kroki

- [Po wdrożeniu zadania](./openshift-post-deployment.md)
- [Rozwiązywanie problemów z wdrożenia OpenShift na platformie Azure](./openshift-troubleshooting.md)
- [Wprowadzenie do platformy OpenShift Container Platform](https://docs.openshift.com/container-platform)
