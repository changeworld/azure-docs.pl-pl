---
title: Tworzenie modułu równoważenia obciążenia dostępnego z Internetu dla usług Azure cloud services
titlesuffix: Azure Load Balancer
description: Dowiedz się, jak utworzyć dostępny z Internetu moduł równoważenia obciążenia w klasycznym modelu wdrażania do usług w chmurze
services: load-balancer
documentationcenter: na
author: genlin
manager: cshepard
tags: azure-service-management
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: genli
ms.openlocfilehash: 66c978a7eb151ce9df939a11e2e3c0016c8e7c9e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60532516"
---
# <a name="get-started-creating-an-internet-facing-load-balancer-for-cloud-services"></a>Wprowadzenie do tworzenia dostępnego z Internetu modułu równoważenia obciążenia do usług w chmurze

> [!div class="op_single_selector"]
> * [Program PowerShell](../load-balancer/load-balancer-get-started-internet-classic-ps.md)
> * [Interfejs wiersza polecenia platformy Azure](../load-balancer/load-balancer-get-started-internet-classic-cli.md)
> * [Azure Cloud Services](../load-balancer/load-balancer-get-started-internet-classic-cloud.md)

[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

> [!IMPORTANT]
> Przed rozpoczęciem pracy z zasobami platformy Azure, ważne jest zrozumienie, że platforma Azure ma obecnie dwa modele wdrażania: Usługa Azure Resource Manager i model klasyczny. Przed rozpoczęciem pracy z dowolnym zasobem Azure należy zapoznać się z [modelami i narzędziami wdrażania](../azure-classic-rm.md). Dokumentację dotyczącą różnych narzędzi można wyświetlić, klikając karty w górnej części artykułu. W tym artykule opisano klasyczny model wdrażania. Możesz też zapoznać się z artykułem na temat [tworzenia dostępnego z Internetu modułu równoważenia obciążenia za pomocą usługi Azure Resource Manager](load-balancer-get-started-internet-arm-ps.md).

Usługi w chmurze są konfigurowane automatycznie z modułem równoważenia obciążenia i mogą być dostosowane za pośrednictwem modelu usług.

## <a name="create-a-load-balancer-using-the-service-definition-file"></a>Tworzenie modułu równoważenia obciążenia z użyciem pliku definicji usługi

Do aktualizacji usługi w chmurze można wykorzystać zestaw narzędzi Azure SDK do platformy .NET 2.5. Ustawienia punktu końcowego usług w chmurze są określane w pliku [definicji usługi](https://msdn.microsoft.com/library/azure/gg557553.aspx) .csdef.

Poniższy przykład zawiera informacje dotyczące konfiguracji pliku servicedefinition.csdef do wdrożenia w chmurze:

Podczas sprawdzania fragmentu pliku .csdef wygenerowanego przez wdrażanie w chmurze widoczny jest zewnętrzny punkt końcowy z konfiguracją do użycia portów protokołu HTTP na portach 10000, 10001 oraz 10002.

```xml
<ServiceDefinition name="Tenant">
    <WorkerRole name="FERole" vmsize="Small">
        <Endpoints>
            <InputEndpoint name="FE_External_Http" protocol="http" port="10000" />
            <InputEndpoint name="FE_External_Tcp"  protocol="tcp"  port="10001" />
            <InputEndpoint name="FE_External_Udp"  protocol="udp"  port="10002" />

            <InputEndpoint name="HTTP_Probe" protocol="http" port="80" loadBalancerProbe="MyProbe" />

            <InstanceInputEndpoint name="InstanceEP" protocol="tcp" localPort="80">
                <AllocatePublicPortFrom>
                    <FixedPortRange min="10110" max="10120"  />
                </AllocatePublicPortFrom>
            </InstanceInputEndpoint>
            <InternalEndpoint name="FE_InternalEP_Tcp" protocol="tcp" />
        </Endpoints>
    </WorkerRole>
</ServiceDefinition>
```

## <a name="check-load-balancer-health-status-for-cloud-services"></a>Sprawdzanie kondycji modułu równoważenia obciążenia do usług w chmurze

Poniżej podano przykład sondy kondycji:

```xml
<LoadBalancerProbes>
    <LoadBalancerProbe name="MyProbe" protocol="http" path="Probe.aspx" intervalInSeconds="5" timeoutInSeconds="100" />
</LoadBalancerProbes>
```

Moduł równoważenia obciążenia łączy informacje z punktu końcowego oraz informacje z sondy i tworzy z nich adres URL w postaci `http://{DIP of VM}:80/Probe.aspx`, którego można użyć do zapytania o kondycję usługi.

Usługa wykrywa sondy wysyłane okresowo z tego samego adresu IP. Jest to żądanie sondy kondycji wysyłane z hosta węzła, w którym działa maszyna wirtualna. Usługa musi odpowiedzieć kodem stanu HTTP 200, aby moduł równoważenia obciążenia przyjął, że jej kondycja jest dobra. Każdy inny kod stanu HTTP (np. 503) powoduje bezpośrednie wykluczenie maszyny wirtualnej z rotacji.

Definicja sondy kontroluje również częstotliwość jej wysyłania. W podanym powyżej przypadku moduł równoważenia obciążenia sonduje punkt końcowy co 5 s. Jeśli przez 10 s (w ciągu dwóch interwałów sondy) nie zostanie odebrana odpowiedź pozytywna, uznaje się, że sonda nie odpowiada i maszyna wirtualna jest wykluczana z rotacji. Jeśli natomiast usługa jest wykluczona z rotacji, ale zostanie odebrana pozytywna odpowiedź, zostaje ona od razu przywrócona do rotacji. Jeśli kondycja usługi zmienia się często z dobrego na zły, moduł równoważenia obciążenia może zdecydować o opóźnieniu przywrócenia jej do rotacji do momentu odebrania kilku sond informujących o dobrej kondycji.

Aby dowiedzieć się więcej, zapoznaj się ze schematem definicji usługi [sondy kondycji](https://msdn.microsoft.com/library/azure/jj151530.aspx).

## <a name="next-steps"></a>Kolejne kroki

[Get started configuring an internal load balancer](load-balancer-get-started-ilb-arm-ps.md) (Wprowadzenie do konfigurowania wewnętrznego modułu równoważenia obciążenia)

[Configure a load balancer distribution mode](load-balancer-distribution-mode.md) (Konfigurowanie trybu dystrybucji modułu równoważenia obciążenia)

[Configure idle TCP timeout settings for your load balancer](load-balancer-tcp-idle-timeout.md) (Konfigurowanie ustawień limitu czasu bezczynności protokołu TCP dla modułu równoważenia obciążenia)

