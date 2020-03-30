---
title: Używanie prywatnego adresu IP do routingu wewnętrznego dla punktu końcowego transferu ruchu przychodzącego
description: Ten artykuł zawiera informacje na temat używania prywatnych adresów IP do routingu wewnętrznego, a tym samym uwidaczniania punktu końcowego transferu danych przychodzących w klastrze na pozostałą część sieci wirtualnej.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 570f28ce559ff1c1180ffaacb781b9120b1890a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73795491"
---
# <a name="use-private-ip-for-internal-routing-for-an-ingress-endpoint"></a>Użyj prywatnego adresu IP dla routingu wewnętrznego dla punktu końcowego transferu ruchu przychodzącego 

Ta funkcja umożliwia uwidacznianie punktu `Virtual Network` końcowego transferu ruchu przychodzącego w ramach prywatnego adresu IP.

## <a name="pre-requisites"></a>Wymagania wstępne  
Brama aplikacji z [prywatną konfiguracją IP](https://docs.microsoft.com/azure/application-gateway/configure-application-gateway-with-private-frontend-ip)

Istnieją dwa sposoby konfigurowania kontrolera do używania prywatnego adresu IP do

## <a name="assign-to-a-particular-ingress"></a>Przypisywanie do określonego transferu danych przychodzących
Aby udostępnić określonego transferu danych przychodzących za [`appgw.ingress.kubernetes.io/use-private-ip`](./ingress-controller-annotations.md#use-private-ip) pomocą prywatnego adresu IP, należy użyć adnotacji w przypadku transferu danych przychodzących.

### <a name="usage"></a>Sposób użycia
```yaml
appgw.ingress.kubernetes.io/use-private-ip: "true"
```

W przypadku bram aplikacji bez prywatnego adresu IP przychyłówki z adnotacjami `appgw.ingress.kubernetes.io/use-private-ip: "true"` zostaną zignorowane. Zostanie to wskazane w zdarzeniu przychodzącym i dzienniku zasobników AGIC.

* Błąd wskazany w zdarzeniu przychodzącym

    ```bash
    Events:
    Type     Reason       Age               From                                                                     Message
    ----     ------       ----              ----                                                                     -------
    Warning  NoPrivateIP  2m (x17 over 2m)  azure/application-gateway, prod-ingress-azure-5c9b6fcd4-bctcb  Ingress default/hello-world-ingress requires Application Gateway 
    applicationgateway3026 has a private IP address
    ```

* Błąd wskazany w dziennikach AGIC

    ```bash
    E0730 18:57:37.914749       1 prune.go:65] Ingress default/hello-world-ingress requires Application Gateway applicationgateway3026 has a private IP address
    ```


## <a name="assign-globally"></a>Przypisywanie globalnie
W przypadku, gdy wymaganie jest ograniczenie wszystkich ingresses `appgw.usePrivateIP: true` `helm` być narażone na prywatne IP, użyj w konfiguracji.

### <a name="usage"></a>Sposób użycia
```yaml
appgw:
    subscriptionId: <subscriptionId>
    resourceGroup: <resourceGroupName>
    name: <applicationGatewayName>
    usePrivateIP: true
```

Spowoduje to, że kontroler transferu danych przychodzących filtruje konfiguracje adresów IP dla prywatnego adresu IP podczas konfigurowania odbiorników frontu w bramie aplikacji.
AGIC będzie panikować i awarii, jeśli `usePrivateIP: true` i nie prywatne IP jest przypisany.

> [!NOTE]
> Jednostka SKU bramy aplikacji w wersji 2 wymaga publicznego adresu IP. Jeśli brama aplikacji ma być [`Network Security Group`](https://docs.microsoft.com/azure/virtual-network/security-overview) prywatna, Dołącz a do podsieci bramy aplikacji, aby ograniczyć ruch.
