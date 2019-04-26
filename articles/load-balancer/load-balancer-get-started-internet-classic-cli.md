---
title: Tworzenie modułu równoważenia obciążenia dostępnego z Internetu — Azure klasyczny interfejs wiersza polecenia
titlesuffix: Azure Load Balancer
description: Dowiedz się, jak utworzyć dostępny z Internetu moduł równoważenia obciążenia w klasycznym modelu wdrażania za pomocą klasycznego interfejsu wiersza polecenia platformy Azure
services: load-balancer
documentationcenter: na
author: genlin
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: fa89117e85bc3d3c9664e6aa037fac923b7432ce
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60544897"
---
# <a name="get-started-creating-an-internet-facing-load-balancer-classic-in-the-azure-classic-cli"></a>Wprowadzenie do tworzenia modułu równoważenia obciążenia (klasycznego) nakierowanego na Internet w usłudze Azure klasyczny interfejs wiersza polecenia

> [!div class="op_single_selector"]
> * [Program PowerShell](../load-balancer/load-balancer-get-started-internet-classic-ps.md)
> * [Klasyczny interfejs wiersza polecenia platformy Azure](../load-balancer/load-balancer-get-started-internet-classic-cli.md)
> * [Azure Cloud Services](../load-balancer/load-balancer-get-started-internet-classic-cloud.md)

[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

> [!IMPORTANT]
> Przed rozpoczęciem pracy z zasobami platformy Azure, ważne jest zrozumienie, że platforma Azure ma obecnie dwa modele wdrażania: Usługa Azure Resource Manager i model klasyczny. Przed rozpoczęciem pracy z dowolnym zasobem Azure należy zapoznać się z [modelami i narzędziami wdrażania](../azure-classic-rm.md). Dokumentację dotyczącą różnych narzędzi można wyświetlić, klikając karty w górnej części artykułu. W tym artykule opisano klasyczny model wdrażania. Możesz też zapoznać się z artykułem na temat [tworzenia dostępnego z Internetu modułu równoważenia obciążenia za pomocą usługi Azure Resource Manager](load-balancer-get-started-internet-arm-ps.md).

[!INCLUDE [requires-classic-cli](../../includes/contains-classic-cli-content.md)]

[!INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="create-an-internet-facing-load-balancer-using-cli"></a>Tworzenie modułu równoważenia obciążenia połączonego z Internetem przy użyciu interfejsu wiersza polecenia

W tym przewodniku opisano sposób tworzenia internetowego modułu równoważenia obciążenia w oparciu o powyższy scenariusz.

1. Jeśli po raz pierwszy używasz klasycznego interfejsu wiersza polecenia platformy Azure, zobacz artykuł [Instalowanie klasycznego interfejsu wiersza polecenia platformy Azure](../cli-install-nodejs.md) i postępuj zgodnie z instrukcjami aż do momentu, gdy należy wybrać konto i subskrypcję platformy Azure.
2. Uruchom polecenie **azure config mode**, aby przełączyć tryb na klasyczny, jak pokazano poniżej.

    ```azurecli
    azure config mode asm
    ```

    Oczekiwane dane wyjściowe:

        info:    New mode is asm

## <a name="create-endpoint-and-load-balancer-set"></a>Tworzenie punktu końcowego i zestawu modułu równoważenia obciążenia

Na potrzeby scenariusza przyjmuje się, że utworzono maszyny wirtualne „web1” i „web2”.
W tym przewodniku opisano sposób tworzenia modułu równoważenia obciążenia przy użyciu portu 80 jako portu publicznego i lokalnego. Port sondy to również port 80, a zestaw modułu równoważenia obciążenia ma nazwę „lbset”.

### <a name="step-1"></a>Krok 1

Utwórz pierwszy punkt końcowy oraz zestaw modułu równoważenia obciążenia za pomocą polecenia `azure network vm endpoint create` dla maszyny wirtualnej „web1”.

```azurecli
azure vm endpoint create web1 80 --local-port 80 --protocol tcp --probe-port 80 --load-balanced-set-name lbset
```

### <a name="step-2"></a>Krok 2

Dodaj drugą maszynę wirtualną „web2” do zestawu modułu równoważenia obciążenia.

```azurecli
azure vm endpoint create web2 80 --local-port 80 --protocol tcp --probe-port 80 --load-balanced-set-name lbset
```

### <a name="step-3"></a>Krok 3

Sprawdź konfigurację modułu równoważenia obciążenia za pomocą polecenia `azure vm show`.

```azurecli
azure vm show web1
```

Dane wyjściowe to:

    data:    DNSName "contoso.cloudapp.net"
    data:    Location "East US"
    data:    VMName "web1"
    data:    IPAddress "10.0.0.5"
    data:    InstanceStatus "ReadyRole"
    data:    InstanceSize "Standard_D1"
    data:    Image "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-2015
    6-en.us-127GB.vhd"
    data:    OSDisk hostCaching "ReadWrite"
    data:    OSDisk name "joaoma-1-web1-0-201509251804250879"
    data:    OSDisk mediaLink "https://XXXXXXXXXXXXXXX.blob.core.windows.
    /vhds/joaomatest-web1-2015-09-25.vhd"
    data:    OSDisk sourceImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Se
    r-2012-R2-20150916-en.us-127GB.vhd"
    data:    OSDisk operatingSystem "Windows"
    data:    OSDisk iOType "Standard"
    data:    ReservedIPName ""
    data:    VirtualIPAddresses 0 address "XXXXXXXXXXXXXXXX"
    data:    VirtualIPAddresses 0 name "XXXXXXXXXXXXXXXXXXXX"
    data:    VirtualIPAddresses 0 isDnsProgrammed true
    data:    Network Endpoints 0 loadBalancedEndpointSetName "lbset"
    data:    Network Endpoints 0 localPort 80
    data:    Network Endpoints 0 name "tcp-80-80"
    data:    Network Endpoints 0 port 80
    data:    Network Endpoints 0 loadBalancerProbe port 80
    data:    Network Endpoints 0 loadBalancerProbe protocol "tcp"
    data:    Network Endpoints 0 loadBalancerProbe intervalInSeconds 15
    data:    Network Endpoints 0 loadBalancerProbe timeoutInSeconds 31
    data:    Network Endpoints 0 protocol "tcp"
    data:    Network Endpoints 0 virtualIPAddress "XXXXXXXXXXXX"
    data:    Network Endpoints 0 enableDirectServerReturn false
    data:    Network Endpoints 1 localPort 5986
    data:    Network Endpoints 1 name "PowerShell"
    data:    Network Endpoints 1 port 5986
    data:    Network Endpoints 1 protocol "tcp"
    data:    Network Endpoints 1 virtualIPAddress "XXXXXXXXXXXX"
    data:    Network Endpoints 1 enableDirectServerReturn false
    data:    Network Endpoints 2 localPort 3389
    data:    Network Endpoints 2 name "Remote Desktop"
    data:    Network Endpoints 2 port 58081
    info:    vm show command OK

## <a name="create-a-remote-desktop-endpoint-for-a-virtual-machine"></a>Tworzenie punktu końcowego pulpitu zdalnego maszyny wirtualnej

Możesz utworzyć punkt końcowy pulpitu zdalnego w celu przesyłania ruchu sieciowego z portu publicznego do lokalnego dla danej maszyny wirtualnej za pomocą polecenia `azure vm endpoint create`.

```azurecli
azure vm endpoint create web1 54580 -k 3389
```

## <a name="remove-virtual-machine-from-load-balancer"></a>Usuwanie maszyny wirtualnej z modułu równoważenia obciążenia

Musisz usunąć punkt końcowy skojarzony z zestawem modułu równoważenia obciążenia z maszyny wirtualnej. Po usunięciu punktu końcowego maszyna wirtualna przestanie należeć do zestawu modułu równoważenia obciążenia.

Korzystając z powyższego przykładu, możesz usunąć punkt końcowy utworzony dla maszyny wirtualnej „web1” z modułu równoważenia obciążenia „lbset” za pomocą polecenia `azure vm endpoint delete`.

```azurecli
azure vm endpoint delete web1 tcp-80-80
```

> [!NOTE]
> Możesz poznać więcej opcji zarządzania punktem końcowym, korzystając z polecenia `azure vm endpoint --help`

## <a name="next-steps"></a>Kolejne kroki

[Get started configuring an internal load balancer](load-balancer-get-started-ilb-arm-ps.md) (Wprowadzenie do konfigurowania wewnętrznego modułu równoważenia obciążenia)

[Configure a load balancer distribution mode](load-balancer-distribution-mode.md) (Konfigurowanie trybu dystrybucji modułu równoważenia obciążenia)

[Configure idle TCP timeout settings for your load balancer](load-balancer-tcp-idle-timeout.md) (Konfigurowanie ustawień limitu czasu bezczynności protokołu TCP dla modułu równoważenia obciążenia)
