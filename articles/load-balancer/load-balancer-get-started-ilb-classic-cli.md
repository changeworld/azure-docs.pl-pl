---
title: Tworzenie wewnętrznego modułu równoważenia obciążenia — klasycznego wiersza polecenia platformy Azure
titlesuffix: Azure Load Balancer
description: Dowiedz się, jak utworzyć wewnętrzny moduł równoważenia obciążenia w klasycznym modelu wdrażania przy użyciu klasycznego interfejsu wiersza polecenia Azure
services: load-balancer
documentationcenter: na
author: genlin
manager: dcscontentpm
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: c0b061da308f2e8635cf1722cb6a735624501374
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71090993"
---
# <a name="get-started-creating-an-internal-load-balancer-using-the-azure-classic-cli"></a>Wprowadzenie do tworzenia wewnętrznego modułu równoważenia obciążenia przy użyciu klasycznego interfejsu wiersza polecenia Azure

> [!div class="op_single_selector"]
> * [Program PowerShell](../load-balancer/load-balancer-get-started-ilb-classic-ps.md)
> * [Interfejs wiersza polecenia platformy Azure](../load-balancer/load-balancer-get-started-ilb-classic-cli.md)
> * [Usługi w chmurze](../load-balancer/load-balancer-get-started-ilb-classic-cloud.md)

[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

> [!IMPORTANT]
> Platforma Azure oferuje dwa różne modele wdrażania związane z tworzeniem zasobów i pracą z nimi:  [model wdrażania przy użyciu usługi Resource Manager i model klasyczny](../azure-resource-manager/resource-manager-deployment-model.md).  Ten artykuł dotyczy klasycznego modelu wdrożenia. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager. Dowiedz się, jak [wykonać te kroki przy użyciu modelu usługi Resource Manager](load-balancer-get-started-ilb-arm-cli.md).

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## <a name="to-create-an-internal-load-balancer-set-for-virtual-machines"></a>Tworzenie zestawu wewnętrznego modułu równoważenia obciążenia dla maszyn wirtualnych

Aby utworzyć zestaw wewnętrznego modułu równoważenia obciążenia, a także serwery, które przesyłają do niego ruch, wykonaj następujące czynności:

1. Utwórz wystąpienie wewnętrznego równoważenia obciążenia, które będzie punktem końcowym przychodzącego ruchu sieciowego, aby obciążenie było zrównoważone między serwerami w zestawie o zrównoważonym obciążeniu.
2. Dodaj punkty końcowe odpowiadające maszynom wirtualnym, które mogą otrzymywać ruch przychodzący.
3. Skonfiguruj serwery wysyłające ruch, aby wysyłały ruch na wirtualny adres IP (VIP) wystąpienia wewnętrznego równoważenia obciążenia.

## <a name="step-by-step-creating-an-internal-load-balancer-using-classic-cli"></a>Szczegółowy opis tworzenia wewnętrznego modułu równoważenia obciążenia przy użyciu klasycznego interfejsu wiersza polecenia

W tym przewodniku opisano sposób tworzenia wewnętrznego modułu równoważenia obciążenia w oparciu o powyższy scenariusz.

1. Jeśli po raz pierwszy używasz klasycznego interfejsu wiersza polecenia, zobacz artykuł [Instalowanie i konfigurowania interfejsu wiersza polecenia Azure](../cli-install-nodejs.md) i postępuj zgodnie z instrukcjami aż do punktu, w którym należy wybrać konto platformy Azure i subskrypcję.
2. Uruchom polecenie **azure config mode**, aby przełączyć tryb na klasyczny, jak pokazano poniżej.

    ```azurecli
    azure config mode asm
    ```

    Oczekiwane dane wyjściowe:

        info:    New mode is asm

## <a name="create-endpoint-and-load-balancer-set"></a>Tworzenie punktu końcowego i zestawu modułu równoważenia obciążenia

Na potrzeby scenariusza przyjmuje się, że utworzono maszyny wirtualne „DB1” i „DB2” w usłudze w chmurze o nazwie „mytestcloud”. Obie maszyny wirtualne korzystają z sieci wirtualnej o nazwie „testvnet” z podsiecią o nazwie „subnet-1”.

W tym przewodniku opisano sposób tworzenia zestawu wewnętrznego modułu równoważenia obciążenia przy użyciu portu 1433 jako portu prywatnego i lokalnego.

Jest to typowy scenariusz, w którym na zapleczu występują maszyny wirtualne SQL korzystające z wewnętrznego modułu równoważenia obciążenia, aby zagwarantować, że serwery bazy danych nie będą dostępne bezpośrednio przez publiczny adres IP.

### <a name="step-1"></a>Krok 1

Utwórz zestaw wewnętrznego modułu równoważenia obciążenia za pomocą polecenia `azure network service internal-load-balancer add`.

```azurecli
azure service internal-load-balancer add --serviceName mytestcloud --internalLBName ilbset --subnet-name subnet-1 --static-virtualnetwork-ipaddress 192.168.2.7
```

Użyj polecenia `azure service internal-load-balancer --help`, aby uzyskać więcej informacji.

Możesz sprawdzić właściwości wewnętrznego modułu równoważenia obciążenia za pomocą polecenia `azure service internal-load-balancer list` *nazwa usługi w chmurze*.

Przykładowe dane wyjściowe:

    azure service internal-load-balancer list my-testcloud
    info:    Executing command service internal-load-balancer list
    + Getting cloud service deployment
    data:    Name    Type     SubnetName  StaticVirtualNetworkIPAddress
    data:    ------  -------  ----------  -----------------------------
    data:    ilbset  Private  subnet-1    192.168.2.7
    info:    service internal-load-balancer list command OK


### <a name="step-2"></a>Krok 2

Konfiguracja zestawu wewnętrznego modułu równoważenia obciążenia zachodzi podczas dodawania pierwszego punktu końcowego. W tym kroku punkt końcowy, maszyna wirtualna i port sondy można skojarzyć z zestawem wewnętrznego modułu równoważenia obciążenia.

```azurecli
azure vm endpoint create db1 1433 --local-port 1433 --protocol tcp --probe-port 1433 --probe-protocol tcp --probe-interval 300 --probe-timeout 600 --internal-load-balancer-name ilbset
```

### <a name="step-3"></a>Krok 3

Sprawdź konfigurację modułu równoważenia obciążenia za pomocą polecenia `azure vm show` *nazwa maszyny wirtualnej*

```azurecli
azure vm show DB1
```

Wynik jest następujący:

    azure vm show DB1
    info:    Executing command vm show
    + Getting virtual machines
    data:    DNSName "mytestcloud.cloudapp.net"
    data:    Location "East US 2"
    data:    VMName "DB1"
    data:    IPAddress "192.168.2.4"
    data:    InstanceStatus "ReadyRole"
    data:    InstanceSize "Standard_D1"
    data:    Image "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-20151022-en.us-127GB.vhd"
    data:    OSDisk hostCaching "ReadWrite"
    data:    OSDisk name "db1-DB1-0-201511120457370846"
    data:    OSDisk mediaLink "https://XXXX.blob.core.windows.net/vhd"
    data:    OSDisk sourceImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-20151022-en.us-127GB.vhd"
    data:    OSDisk operatingSystem "Windows"
    data:    OSDisk iOType "Standard"
    data:    ReservedIPName ""
    data:    VirtualIPAddresses 0 address "137.116.64.107"
    data:    VirtualIPAddresses 0 name "db1ContractContract"
    data:    VirtualIPAddresses 0 isDnsProgrammed true
    data:    VirtualIPAddresses 1 address "192.168.2.7"
    data:    VirtualIPAddresses 1 name "ilbset"
    data:    Network Endpoints 0 localPort 5986
    data:    Network Endpoints 0 name "PowerShell"
    data:    Network Endpoints 0 port 5986
    data:    Network Endpoints 0 protocol "tcp"
    data:    Network Endpoints 0 virtualIPAddress "137.116.64.107"
    data:    Network Endpoints 0 enableDirectServerReturn false
    data:    Network Endpoints 1 localPort 3389
    data:    Network Endpoints 1 name "Remote Desktop"
    data:    Network Endpoints 1 port 60173
    data:    Network Endpoints 1 protocol "tcp"
    data:    Network Endpoints 1 virtualIPAddress "137.116.64.107"
    data:    Network Endpoints 1 enableDirectServerReturn false
    data:    Network Endpoints 2 localPort 1433
    data:    Network Endpoints 2 name "tcp-1433-1433"
    data:    Network Endpoints 2 port 1433
    data:    Network Endpoints 2 loadBalancerProbe port 1433
    data:    Network Endpoints 2 loadBalancerProbe protocol "tcp"
    data:    Network Endpoints 2 loadBalancerProbe intervalInSeconds 300
    data:    Network Endpoints 2 loadBalancerProbe timeoutInSeconds 600
    data:    Network Endpoints 2 protocol "tcp"
    data:    Network Endpoints 2 virtualIPAddress "192.168.2.7"
    data:    Network Endpoints 2 enableDirectServerReturn false
    data:    Network Endpoints 2 loadBalancerName "ilbset"
    info:    vm show command OK

## <a name="create-a-remote-desktop-endpoint-for-a-virtual-machine"></a>Tworzenie punktu końcowego pulpitu zdalnego maszyny wirtualnej

Możesz utworzyć punkt końcowy pulpitu zdalnego w celu przesyłania ruchu sieciowego z portu publicznego do lokalnego dla danej maszyny wirtualnej za pomocą polecenia `azure vm endpoint create`.

```azurecli
azure vm endpoint create web1 54580 -k 3389
```

## <a name="remove-virtual-machine-from-load-balancer"></a>Usuwanie maszyny wirtualnej z modułu równoważenia obciążenia

Możesz usunąć maszynę wirtualną z zestawu wewnętrznego modułu równoważenia obciążenia przez usunięcie skojarzonego punktu końcowego. Po usunięciu punktu końcowego maszyna wirtualna nie będzie należeć do zestawu modułu równoważenia obciążenia.

Korzystając z powyższego przykładu, możesz usunąć punkt końcowy utworzony dla maszyny wirtualnej „DB1” z wewnętrznego modułu równoważenia obciążenia „ilbset” za pomocą polecenia `azure vm endpoint delete`.

```azurecli
azure vm endpoint delete DB1 tcp-1433-1433
```

Użyj polecenia `azure vm endpoint --help`, aby uzyskać więcej informacji.

## <a name="next-steps"></a>Kolejne kroki

[Configure a load balancer distribution mode using source IP affinity](load-balancer-distribution-mode.md) (Konfigurowanie trybu dystrybucji modułu równoważenia obciążenia przy użyciu koligacji źródłowych adresów IP)

[Configure idle TCP timeout settings for your load balancer](load-balancer-tcp-idle-timeout.md) (Konfigurowanie ustawień limitu czasu bezczynności protokołu TCP dla modułu równoważenia obciążenia)
