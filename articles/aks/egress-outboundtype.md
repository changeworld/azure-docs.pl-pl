---
title: Dostosowywanie tras zdefiniowanych przez użytkownika (UDR) w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jak zdefiniować niestandardową trasę wychodzącą w usłudze Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 03/16/2020
ms.openlocfilehash: fa64294939ea487b3123d1db5ef6c8a5f30fcf72
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80129389"
---
# <a name="customize-cluster-egress-with-a-user-defined-route-preview"></a>Dostosowywanie wyjścia klastra za pomocą trasy zdefiniowanej przez użytkownika (wersja zapoznawcza)

Ruch wychodzący z klastra AKS można dostosować do określonych scenariuszy. Domyślnie AKS będzie aprowizować standardowy moduł równoważenia obciążenia SKU do konfiguracji i używane dla ruchu wychodzącego. Jednak domyślna konfiguracja może nie spełniać wymagań wszystkich scenariuszy, jeśli publiczne adresy IP są niedozwolone lub dodatkowe przeskoki są wymagane dla ruchu wychodzącego.

W tym artykule opisano, jak dostosować trasę wychodzącą klastra do obsługi scenariuszy sieci niestandardowych, takich jak te, które nie zezwalają na publiczne adresy IP i wymaga klastra, aby usiąść za wirtualnym urządzeniem sieciowym (NVA).

> [!IMPORTANT]
> Funkcje w wersji zapoznawczej usługi AKS są samoobsługowe i są oferowane na zasadzie opt-in. Wersje zapoznawcza są dostarczane *w stanie, w jakim są* *dostępne* i są wyłączone z umowy dotyczącej poziomu usług (SLA) i ograniczonej gwarancji. Podglądy AKS są częściowo objęte obsługą klienta na podstawie *najlepszych starań.* W związku z tym funkcje nie są przeznaczone do użytku produkcyjnego. Aby uzyskać więcej informacji, zobacz następujące artykuły pomocy technicznej:
>
> * [Zasady wsparcia AKS](support-policies.md)
> * [Często zadawane pytania dotyczące pomocy technicznej platformy Azure](faq.md)

## <a name="prerequisites"></a>Wymagania wstępne
* Interfejsu wiersza polecenia platformy Azure w wersji 2.0.81 lub większej
* Rozszerzenie interfejsu wiersza polecenia platformy Azure w wersji 0.4.28 lub większej
* Wersja interfejsu `2020-01-01` API lub większa

## <a name="install-the-latest-azure-cli-aks-preview-extension"></a>Instalowanie najnowszego rozszerzenia usługi Azure CLI AKS Preview
Aby ustawić typ wychodzący klastra, potrzebujesz rozszerzenia AKS Preview platformy Azure w wersji 0.4.18 lub nowszej. Zainstaluj rozszerzenie AKS Preview platformy Azure przy użyciu polecenia dodawania rozszerzenia az, a następnie sprawdź dostępność dostępnych aktualizacji za pomocą następującego polecenia aktualizacji rozszerzenia az:

```azure-cli
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="limitations"></a>Ograniczenia
* Podczas podglądu, `outboundType` mogą być definiowane tylko w czasie tworzenia klastra i nie mogą być aktualizowane później.
* Podczas podglądu klastry `outboundType` usługi AKS powinny używać usługi Azure CNI. Kubenet jest konfigurowalny, użycie wymaga ręcznych skojarzeń tabeli marszruty z podsiecią AKS.
* Ustawienie `outboundType` wymaga klastrów AKS `vm-set-type` z i `VirtualMachineScaleSets` `load-balancer-sku` z `Standard`.
* Ustawienie `outboundType` wartości `UDR` wymaga trasy zdefiniowanej przez użytkownika z prawidłową łącznością wychodzącą dla klastra.
* Ustawienie `outboundType` wartości oznacza, że adres IP źródła ruchu przychodzącego `UDR` kierowanego do modułu równoważenia obciążenia może nie **odpowiadać** wychodzącemu adresowi docelowemu wychodzącego wyjścia klastra.

## <a name="overview-of-outbound-types-in-aks"></a>Omówienie typów ruchu wychodzącego w aks

Klaster AKS można dostosować za `outboundType` pomocą unikatowego modułu równoważenia obciążenia typu lub routingu zdefiniowanego przez użytkownika.

> [!IMPORTANT]
> Typ wychodzący wpływa tylko na ruch wychodzący klastra. Aby uzyskać więcej informacji, zobacz [konfigurowanie kontrolerów transferu danych przychodzących.](ingress-basic.md)

### <a name="outbound-type-of-loadbalancer"></a>Typ ładunku loadBalancer wychodzący

Jeśli `loadBalancer` jest ustawiona, program AKS automatycznie zakończy następującą konfigurację. Moduł równoważenia obciążenia jest używany do wyjścia za pośrednictwem publicznego adresu IP przypisanego przez usługi AKS. Typ wychodzący `loadBalancer` obsługuje usługi typu `loadBalancer`Kubernetes, które oczekują wyjścia z modułu równoważenia obciążenia utworzonego przez dostawcę zasobów usługi AKS.

Następująca konfiguracja jest wykonywana przez AKS.
   * Publiczny adres IP jest aprowizowany dla wyjścia klastra.
   * Publiczny adres IP jest przypisany do zasobu modułu równoważenia obciążenia.
   * Pule wewnętrznej bazy danych dla modułu równoważenia obciążenia są konfigurowane dla węzłów agenta w klastrze.

Poniżej znajduje się topologia sieci wdrożona domyślnie w `outboundType` klastrach AKS, które używają pliku `loadBalancer`.

![typ-lb](media/egress-outboundtype/outboundtype-lb.png)

### <a name="outbound-type-of-userdefinedrouting"></a>Typ ruchu wychodzącego użytkownikaDefdefiniowanyRouting

> [!NOTE]
> Przy użyciu typu wychodzącego jest zaawansowany scenariusz sieci i wymaga odpowiedniej konfiguracji sieci.

Jeśli `userDefinedRouting` jest ustawiona, AKS nie będzie automatycznie konfigurować ścieżki wychodzące. Użytkownik powinien wykonać następujące **czynności.**

Klaster musi zostać wdrożony w istniejącej sieci wirtualnej z skonfigurowaną podsiecią. Prawidłowa trasa zdefiniowana przez użytkownika (UDR) musi istnieć w podsieci z łącznością wychodzącą.

Dostawca zasobów AKS wdroży standardowy moduł równoważenia obciążenia (SLB). Moduł równoważenia obciążenia nie jest skonfigurowany z żadnymi regułami i [nie pobiera opłaty, dopóki nie zostanie umieszczona reguła.](https://azure.microsoft.com/pricing/details/load-balancer/) Usługa AKS **nie** będzie automatycznie aprowizować publiczny adres IP dla frontendu SLB. AKS **nie** będzie automatycznie konfigurować puli wewnętrznej bazy danych modułu równoważenia obciążenia.

## <a name="deploy-a-cluster-with-outbound-type-of-udr-and-azure-firewall"></a>Wdrażanie klastra z typem UDR i Zaporą platformy Azure typu wychodzącego

Aby zilustrować zastosowanie klastra z typem wychodzącym przy użyciu trasy zdefiniowanej przez użytkownika, klaster można skonfigurować w sieci wirtualnej równorzędnej z zaporą platformy Azure.

![Zamknięta topologia](media/egress-outboundtype/outboundtype-udr.png)

* Ruch przychodzący jest zmuszony do przepływu przez filtry zapory
   * Izolowana podsieć przechowuje wewnętrzny moduł równoważenia obciążenia do routingu do węzłów agenta
   * Węzły agenta są izolowane w dedykowanej podsieci
* Żądania wychodzące rozpoczynają się od węzłów agenta do wewnętrznego adresu IP zapory platformy Azure przy użyciu trasy zdefiniowanej przez użytkownika
   * Żądania z węzłów agenta AKS są zgodne z identyfikatorem UDR umieszczonym w podsieci, w których wdrożono klaster AKS.
   * Wyjście zapory azure z sieci wirtualnej z publicznego frontendu IP
   * Dostęp do płaszczyzny sterowania AKS jest chroniony przez grupę sieciową sieciowej, która włączyła adres IP frontonu zapory
   * Dostęp do publicznego Internetu lub innych usług platformy Azure przepływa do i z adresu IP frontonia zapory

### <a name="set-configuration-via-environment-variables"></a>Ustawianie konfiguracji za pomocą zmiennych środowiskowych

Zdefiniuj zestaw zmiennych środowiskowych, które mają być używane w tworzeniu zasobów.

```bash
PREFIX="contosofin"
RG="${PREFIX}-rg"
LOC="eastus"
NAME="${PREFIX}outboundudr"
AKS_NAME="${PREFIX}aks"
VNET_NAME="${PREFIX}vnet"
AKSSUBNET_NAME="${PREFIX}akssubnet"
SVCSUBNET_NAME="${PREFIX}svcsubnet"
# DO NOT CHANGE FWSUBNET_NAME - This is currently a requirement for Azure Firewall.
FWSUBNET_NAME="AzureFirewallSubnet"
FWNAME="${PREFIX}fw"
FWPUBLICIP_NAME="${PREFIX}fwpublicip"
FWIPCONFIG_NAME="${PREFIX}fwconfig"
FWROUTE_TABLE_NAME="${PREFIX}fwrt"
FWROUTE_NAME="${PREFIX}fwrn"
FWROUTE_NAME_INTERNET="${PREFIX}fwinternet"
DEVSUBNET_NAME="${PREFIX}dev"
```

Następnie ustaw identyfikatory subskrypcji.

```azure-cli
# Get ARM Access Token and Subscription ID - This will be used for AuthN later.

ACCESS_TOKEN=$(az account get-access-token -o tsv --query 'accessToken')

# NOTE: Update Subscription Name
# Set Default Azure Subscription to be Used via Subscription ID

az account set -s <SUBSCRIPTION_ID_GOES_HERE>

# NOTE: Update Subscription Name for setting SUBID

SUBID=$(az account show -s '<SUBSCRIPTION_NAME_GOES_HERE>' -o tsv --query 'id')
```

## <a name="create-a-virtual-network-with-multiple-subnets"></a>Tworzenie sieci wirtualnej z wieloma podsieciami

Aprowizuj sieć wirtualną z trzema oddzielnymi podsieciami, jedną dla klastra, jedną dla zapory i jedną dla transferu danych przychodzących usługi.

![Pusta topologia sieci](media/egress-outboundtype/empty-network.png)

Utwórz grupę zasobów, aby pomieścić wszystkie zasoby.

```azure-cli
# Create Resource Group

az group create --name $RG --location $LOC
```

Utwórz dwie sieci wirtualne do obsługi klastra AKS i Zapory azure. Każdy z nich będzie miał własną podsieć. Zacznijmy od sieci AKS.

```
# Dedicated virtual network with AKS subnet

az network vnet create \
    --resource-group $RG \
    --name $VNET_NAME \
    --address-prefixes 100.64.0.0/16 \
    --subnet-name $AKSSUBNET_NAME \
    --subnet-prefix 100.64.1.0/24

# Dedicated subnet for K8s services

az network vnet subnet create \
    --resource-group $RG \
    --vnet-name $VNET_NAME \
    --name $SVCSUBNET_NAME \
    --address-prefix 100.64.2.0/24

# Dedicated subnet for Azure Firewall (Firewall name cannot be changed)

az network vnet subnet create \
    --resource-group $RG \
    --vnet-name $VNET_NAME \
    --name $FWSUBNET_NAME \
    --address-prefix 100.64.3.0/24
```

## <a name="create-and-setup-an-azure-firewall-with-a-udr"></a>Tworzenie i konfigurowanie zapory platformy Azure przy za pomocą narzędzia UDR

Należy skonfigurować reguły przychodzące i wychodzące zapory azure. Głównym celem zapory jest umożliwienie organizacjom konfigurowania szczegółowych reguł ruchu przychodzącego i wychodzącego do i z klastra AKS.

![Zapora sieciowa i UDR](media/egress-outboundtype/firewall-udr.png)

Utwórz standardowy publiczny zasób IP jednostki SKU, który będzie używany jako adres frontendu zapory platformy Azure.

```azure-cli
az network public-ip create -g $RG -n $FWPUBLICIP_NAME -l $LOC --sku "Standard"
```

Zarejestruj rozszerzenie interfejsu wiersza polecenia podglądu, aby utworzyć zaporę platformy Azure.
```azure-cli
# Install Azure Firewall preview CLI extension

az extension add --name azure-firewall

# Deploy Azure Firewall

az network firewall create -g $RG -n $FWNAME -l $LOC
```

Adres IP utworzony wcześniej można teraz przypisać do frontendu zapory.
> [!NOTE]
> Konfiguracja publicznego adresu IP w Zaporze platformy Azure może potrwać kilka minut.
> 
> Jeśli błędy są wielokrotnie odbierane w poniższym poleceniu, usuń istniejącą zaporę i publiczny adres IP i aprowizuj publiczny adres IP i zaporę platformy Azure za pośrednictwem portalu w tym samym czasie.

```azure-cli
# Configure Firewall IP Config

az network firewall ip-config create -g $RG -f $FWNAME -n $FWIPCONFIG_NAME --public-ip-address $FWPUBLICIP_NAME --vnet-name $VNET_NAME
```

Po pomyślnym wykonaniu poprzedniego polecenia zapisz adres IP frontendu zapory do konfiguracji później.

```bash
# Capture Firewall IP Address for Later Use

FWPUBLIC_IP=$(az network public-ip show -g $RG -n $FWPUBLICIP_NAME --query "ipAddress" -o tsv)
FWPRIVATE_IP=$(az network firewall show -g $RG -n $FWNAME --query "ipConfigurations[0].privateIpAddress" -o tsv)
```

### <a name="create-a-udr-with-a-hop-to-azure-firewall"></a>Tworzenie UDR z przeskokiem do zapory platformy Azure

Platforma Azure automatycznie kieruje ruchem między podsieciami platformy Azure, sieciami wirtualnymi i sieciami lokalnymi. Jeśli chcesz zmienić dowolną z domyślnych routingu platformy Azure, należy to zrobić, tworząc tabelę marszruty.

Utwórz pustą tabelę marszruty, która ma być skojarzona z daną podsiecią. Tabela marszruty zdefiniuje następny przeskok jako zaporę azure utworzoną powyżej. Każda podsieć może mieć skojarzoną ze sobą żadną lub jedną tabelę tras.

```azure-cli
# Create UDR and add a route for Azure Firewall

az network route-table create -g $RG --name $FWROUTE_TABLE_NAME
az network route-table route create -g $RG --name $FWROUTE_NAME --route-table-name $FWROUTE_TABLE_NAME --address-prefix 0.0.0.0/0 --next-hop-type VirtualAppliance --next-hop-ip-address $FWPRIVATE_IP --subscription $SUBID
az network route-table route create -g $RG --name $FWROUTE_NAME_INTERNET --route-table-name $FWROUTE_TABLE_NAME --address-prefix $FWPUBLIC_IP/32 --next-hop-type Internet
```

Zobacz [dokumentację tabeli tras sieci wirtualnej,](../virtual-network/virtual-networks-udr-overview.md#user-defined) w jaki sposób można zastąpić domyślne trasy systemowe platformy Azure lub dodać dodatkowe trasy do tabeli tras podsieci.

## <a name="adding-network-firewall-rules"></a>Dodawanie reguł zapory sieciowej

> [!WARNING]
> Poniżej przedstawiono jeden przykład dodawania reguły zapory. Wszystkie punkty końcowe ruchu wychodzącego zdefiniowane w [wymaganych punktach końcowych ruchu wychodzącego](egress.md) muszą być włączone przez reguły zapory aplikacji dla klastrów AKS do działania. Bez tych punktów końcowych włączone, klaster nie może działać.

Poniżej znajduje się przykład reguły sieci i aplikacji. Dodajemy regułę sieci, która zezwala na dowolny protokół, adres źródłowy, adres docelowy i porty docelowe. Możemy również dodać regułę aplikacji dla **niektórych** punktów końcowych wymaganych przez usługi AKS.

W scenariuszu produkcyjnym należy włączyć dostęp tylko do wymaganych punktów końcowych dla aplikacji i tych zdefiniowanych w [programie AKS wymagane wyjście](egress.md).

```
# Add Network FW Rules

az network firewall network-rule create -g $RG -f $FWNAME --collection-name 'aksfwnr' -n 'netrules' --protocols 'Any' --source-addresses '*' --destination-addresses '*' --destination-ports '*' --action allow --priority 100

# Add Application FW Rules
# IMPORTANT: Add AKS required egress endpoints

az network firewall application-rule create -g $RG -f $FWNAME \
    --collection-name 'AKS_Global_Required' \
    --action allow \
    --priority 100 \
    -n 'required' \
    --source-addresses '*' \
    --protocols 'http=80' 'https=443' \
    --target-fqdns \
        'aksrepos.azurecr.io' \
        '*blob.core.windows.net' \
        'mcr.microsoft.com' \
        '*cdn.mscr.io' \
        '*.data.mcr.microsoft.com' \
        'management.azure.com' \
        'login.microsoftonline.com' \
        'ntp.ubuntu.com' \
        'packages.microsoft.com' \
        'acs-mirror.azureedge.net'
```

Aby dowiedzieć się więcej o usłudze Zapora azure, zobacz [dokumentację zapory platformy](https://docs.microsoft.com/azure/firewall/overview) Azure.

## <a name="associate-the-route-table-to-aks"></a>Skojarz tabelę marszruty z programem AKS

Aby skojarzyć klaster z zaporą, dedykowana podsieć podsieci klastra musi odwoływać się do tabeli marszruty utworzonej powyżej. Skojarzenie można wykonać, wydając polecenie do sieci wirtualnej, w którym znajduje się klaster i zapora, aby zaktualizować tabelę tras podsieci klastra.

```azure-cli
# Associate route table with next hop to Firewall to the AKS subnet

az network vnet subnet update -g $RG --vnet-name $VNET_NAME --name $AKSSUBNET_NAME --route-table $FWROUTE_TABLE_NAME
```

## <a name="deploy-aks-with-outbound-type-of-udr-to-the-existing-network"></a>Wdrażanie usługi AKS z typem chYłka w istniejącej sieci

Teraz klaster AKS można wdrożyć w istniejącej konfiguracji sieci wirtualnej. Aby ustawić typ wychodzący klastra na routing zdefiniowany przez użytkownika, do usługi AKS należy podać istniejącą podsieć.

![aks-deploy](media/egress-outboundtype/outboundtype-udr.png)

### <a name="create-a-service-principal-with-access-to-provision-inside-the-existing-virtual-network"></a>Tworzenie jednostki usługi z dostępem do aprowizy w istniejącej sieci wirtualnej

Podmiot zabezpieczeń usługi jest używany przez usługę AKS do tworzenia zasobów klastra. Podmiot zabezpieczeń usługi przekazywane w czasie tworzenia jest używany do tworzenia podstawowych zasobów AKS, takich jak maszyny wirtualne, magazyn i moduły równoważenia obciążenia używane przez usługę AKS. Jeśli udzielono zbyt mało uprawnień, nie będzie w stanie aprowizować klastra usługi AKS.

```azure-cli
# Create SP and Assign Permission to Virtual Network

az ad sp create-for-rbac -n "${PREFIX}sp" --skip-assignment
```

Teraz zastąp `APPID` i `PASSWORD` poniżej z jednostki usługi appid i service dublowane hasło jednostki generowane przez poprzednie dane wyjściowe polecenia. Odwołamy się do identyfikatora zasobu sieci wirtualnej, aby udzielić uprawnień podmiotowi usługi, aby usługa AKS mogła wdrażać w nim zasoby.

```azure-cli
APPID="<SERVICE_PRINCIPAL_APPID_GOES_HERE>"
PASSWORD="<SERVICEPRINCIPAL_PASSWORD_GOES_HERE>"
VNETID=$(az network vnet show -g $RG --name $VNET_NAME --query id -o tsv)

# Assign SP Permission to VNET

az role assignment create --assignee $APPID --scope $VNETID --role Contributor

# View Role Assignment
az role assignment list --assignee $APPID --all -o table
```

### <a name="deploy-aks"></a>Wdrażanie AKS

Na koniec klaster AKS można wdrożyć w istniejącej podsieci, którą mamy dedykowaną dla klastra. Podsieć docelowa, w która ma zostać `$SUBNETID`wdrożona, jest definiowana ze zmienną środowiskową . Nie zdefiniowaliśmy zmiennej `$SUBNETID` w poprzednich krokach. Aby ustawić wartość identyfikatora podsieci, można użyć następującego polecenia:

```azurecli
SUBNETID="/subscriptions/$SUBID/resourceGroups/$RG/providers/Microsoft.Network/virtualNetworks/$VNET_NAME/subnets/$AKSSUBNET_NAME"
```

Firma We will define the outbound type to follow the UDR which exists on the subnet, enabling AKS to skip setup and IP provisioning for the load balanceer which can now be strictly internal.

Funkcję AKS dla [nieautoryzowanych zakresów adresów IP serwera interfejsu API](api-server-authorized-ip-ranges.md) można dodać, aby ograniczyć dostęp serwera interfejsu API tylko do publicznego punktu końcowego zapory. Autoryzowana funkcja zakresów adresów IP jest oznaczona na diagramie jako grupa sieciowych sieciowej, która musi zostać przekazana, aby uzyskać dostęp do płaszczyzny sterowania. Po włączeniu funkcji nieautoryzowanego zakresu adresów IP w celu ograniczenia dostępu do serwera interfejsu API narzędzia programistyczne muszą używać pola szybkiego dostępu z sieci wirtualnej zapory lub należy dodać wszystkie punkty końcowe dewelopera do autoryzowanego zakresu adresów IP.

> [!TIP]
> Dodatkowe funkcje można dodać do wdrożenia klastra, takie jak (Private Cluster)[]. W przypadku korzystania z autoryzowanych zakresów adresów IP do uzyskania dostępu do serwera interfejsu API wymagany będzie jumpbox wewnątrz sieci klastra.

```azure-cli
az aks create -g $RG -n $AKS_NAME -l $LOC \
  --node-count 3 \
  --network-plugin azure --generate-ssh-keys \
  --service-cidr 192.168.0.0/16 \
  --dns-service-ip 192.168.0.10 \
  --docker-bridge-address 172.22.0.1/29 \
  --vnet-subnet-id $SUBNETID \
  --service-principal $APPID \
  --client-secret $PASSWORD \
  --load-balancer-sku standard \
  --outbound-type userDefinedRouting \
  --api-server-authorized-ip-ranges $FWPUBLIC_IP
  ```

### <a name="enable-developer-access-to-the-api-server"></a>Włączanie dostępu deweloperów do serwera interfejsu API

Ze względu na autoryzowaną konfigurację zakresów adresów IP dla klastra należy dodać adresy IP narzędzi programisty do listy zatwierdzonych zakresów adresów IP programu AKS, aby uzyskać dostęp do serwera interfejsu API. Inną opcją jest skonfigurowanie jumpbox z niezbędnym narzędziem wewnątrz oddzielnej podsieci w sieci wirtualnej zapory.

Dodaj kolejny adres IP do zatwierdzonych zakresów za pomocą następującego polecenia

```bash
# Retrieve your IP address
CURRENT_IP=$(dig @resolver1.opendns.com ANY myip.opendns.com +short)

# Add to AKS approved list
az aks update -g $RG -n $AKS_NAME --api-server-authorized-ip-ranges $CURRENT_IP/32

```

### <a name="setup-the-internal-load-balancer"></a>Ustawianie wewnętrznego modułu równoważenia obciążenia

AKS wdrożył moduł równoważenia obciążenia z klastrem, który można skonfigurować jako [wewnętrzny moduł równoważenia obciążenia](internal-lb.md).

Aby utworzyć wewnętrzny moduł równoważenia obciążenia, utwórz manifest usługi o nazwie internal-lb.yaml o typie usługi LoadBalancer i adnotację azure-load-balance-internal, jak pokazano w poniższym przykładzie:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
    service.beta.kubernetes.io/azure-load-balancer-internal-subnet: "contosofinsvcsubnet"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: internal-app
```

Wdrażanie wewnętrznego modułu równoważenia obciążenia przy użyciu aplikacji kubectl i określ nazwę manifestu YAML:

```bash
kubectl apply -f internal-lb.yaml
```

## <a name="deploy-a-kubernetes-service"></a>Wdrażanie usługi Kubernetes

Ponieważ typ wychodzący klastra jest ustawiony jako UDR, kojarzenie węzłów agenta jako puli wewnętrznej bazy danych dla modułu równoważenia obciążenia nie jest automatycznie wykonywane przez AKS w czasie tworzenia klastra. Jednak skojarzenie puli wewnętrznej bazy danych jest obsługiwane przez dostawcę chmury platformy Azure platformy Kubernetes po wdrożeniu usługi Kubernetes.

Wdrażanie aplikacji do głosowania platformy Azure przez skopiowanie `example.yaml`yaml poniżej do pliku o nazwie .

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-back
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-back
  template:
    metadata:
      labels:
        app: azure-vote-back
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-back
        image: redis
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 6379
          name: redis
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-back
spec:
  ports:
  - port: 6379
  selector:
    app: azure-vote-back
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-front
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-front
  template:
    metadata:
      labels:
        app: azure-vote-front
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-front
        image: microsoft/azure-vote-front:v1
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 80
        env:
        - name: REDIS
          value: "azure-vote-back"
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
    service.beta.kubernetes.io/azure-load-balancer-internal-subnet: "contosofinsvcsubnet"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

Wdrażanie usługi przez uruchomienie:

```bash
kubectl apply -f example.yaml
```

## <a name="add-a-dnat-rule-to-azure-firewall"></a>Dodawanie reguły DNAT do zapory platformy Azure

Aby skonfigurować łączność przychodzącą, reguła DNAT musi być zapisana w zaporze platformy Azure. Aby przetestować łączność z naszym klastrem, reguła jest zdefiniowana dla publicznego adresu IP zapory do trasy do wewnętrznego adresu IP udostępnianego przez usługę wewnętrzną.

Adres docelowy można dostosować, ponieważ jest to port na zaporze, do który ma zostać dostęp. Przetłumaczony adres musi być adresem IP wewnętrznego modułu równoważenia obciążenia. Przetłumaczony port musi być narażonym portem usługi Kubernetes.

Należy określić wewnętrzny adres IP przypisany do modułu równoważenia obciążenia utworzonego przez usługę Kubernetes. Pobierz adres, uruchamiając:

```bash
kubectl get services
```

Potrzebny adres IP zostanie wyświetlony w kolumnie EXTERNAL-IP, podobnie jak w poniższej kolumnie.

```bash
NAME               TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
azure-vote-back    ClusterIP      192.168.92.209   <none>        6379/TCP       23m
azure-vote-front   LoadBalancer   192.168.19.183   100.64.2.5    80:32106/TCP   23m
kubernetes         ClusterIP      192.168.0.1      <none>        443/TCP        4d3h
```

```azure-cli
az network firewall nat-rule create --collection-name exampleset --destination-addresses $FWPUBLIC_IP --destination-ports 80 --firewall-name $FWNAME --name inboundrule --protocols Any --resource-group $RG --source-addresses '*' --translated-port 80 --action Dnat --priority 100 --translated-address <INSERT IP OF K8s SERVICE>
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

> [!NOTE]
> Podczas usuwania usługi wewnętrznej usługi Kubernetes, jeśli wewnętrzny moduł równoważenia obciążenia nie jest już używany przez żadną usługę, dostawca chmury platformy Azure usunie wewnętrzny moduł równoważenia obciążenia. Przy następnym wdrożeniu usługi moduł równoważenia obciążenia zostanie wdrożony, jeśli nie można znaleźć żadnego z żądaną konfiguracją.

Aby wyczyścić zasoby platformy Azure, usuń grupę zasobów AKS.

```azure-cli
az group delete -g $RG
```

## <a name="validate-connectivity"></a>Sprawdzanie poprawności łączności

Przejdź do adresu IP frontendu zapory platformy Azure w przeglądarce, aby sprawdzić poprawność łączności.

Powinien zostać wyświetlony obraz aplikacji do głosowania platformy Azure.

## <a name="next-steps"></a>Następne kroki

Zobacz [omówienie UDR sieci platformy Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview).

Zobacz, [jak utworzyć, zmienić lub usunąć tabelę marszrut](https://docs.microsoft.com/azure/virtual-network/manage-route-table).
