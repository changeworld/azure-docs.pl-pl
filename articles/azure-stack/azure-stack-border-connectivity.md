---
title: Obramowanie zagadnienia dotyczące integracji sieci łączności dla usługi Azure Stack, zintegrowanych systemów | Dokumentacja firmy Microsoft
description: Dowiedz się, jak planować łączności sieciowej obramowania centrum danych z wieloma węzłami usługi Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2018
ms.author: jeffgilb
ms.reviewer: wamota
ms.openlocfilehash: 260c58ad9099a4532c8a6558cfcf5c13f0fc8d52
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/27/2018
ms.locfileid: "39282012"
---
# <a name="border-connectivity"></a>Łączność obramowania 
Planowanie integracji sieci jest ważnego wymagania wstępnego pomyślne wdrożenie systemów zintegrowanych w usłudze Azure Stack, operacji i zarządzania. Planowanie łączności obramowania rozpoczyna się przez wybranie, czy nie używają routingu dynamicznego przy użyciu protokołu border gateway protocol (BGP). Wymaga to przypisanie 16-bitowy numer systemu autonomicznego BGP (publicznym lub prywatnym) lub przy użyciu routingu statycznego, gdy domyślnej trasy statycznej jest przypisany do urządzeń obramowania.

> [!IMPORTANT]
> Górnej przełączników rack (TOR) wymagają pasm warstwy 3 z adresów IP Point-to-Point (/ 30 sieci) skonfigurowany na interfejsy fizyczne. Za pomocą usług warstwy 2 pasm przełączniki TOR obsługują operacje usługi Azure Stack nie jest obsługiwana. 

## <a name="bgp-routing"></a>Routing protokołu BGP
Przy użyciu protokołu routingu dynamicznego takich jak Protokół BGP gwarantuje, że system jest zawsze pamiętać o zmiany w sieci i ułatwia administrowanie. 

Jak pokazano na poniższym diagramie, anonsowanie z prywatnym adresem IP miejsca na przełączniku TOR jest ograniczony przy użyciu listę prefiksów. Wyświetla prefiks nie zezwala na prywatne podsieci IP i zastosowanie go jako mapę trasy dla połączenia między TOR i obramowanie.

Oprogramowanie modułu równoważenia obciążenia (SLB) uruchomionych elementów równorzędnych rozwiązania usługi Azure Stack na urządzeniach z sieci TOR tak dynamicznie można ogłaszać dłuższe adresów VIP.

Aby zapewnić, że ruch użytkowników tak, natychmiast i w sposób niewidoczny dla użytkownika przywraca po awarii, VPC lub MLAG skonfigurowana między urządzeniami sieci TOR umożliwia korzystanie z wielu obudowy łącze agregacji do hostów i HSRP lub VRRP, która zapewnia sieci nadmiarowości w sieciach IP.

![Routing protokołu BGP](media/azure-stack-border-connectivity/bgp-routing.png)

## <a name="static-routing"></a>Routing statyczny
Routing statyczny wymaga dodatkowej konfiguracji do urządzenia obramowania. Wymaga ona więcej ręcznej interwencji i zarządzania, a także dogłębnej analizy przed wszelkie zmiany i problemy spowodowane przez błąd konfiguracji może zająć więcej czasu wycofywania, w zależności od zmian. Nie jest to zalecana metoda routingu, ale jest on obsługiwany.

Aby zintegrować usługi Azure Stack w środowisku sieci przy użyciu routingu statycznego, wszystkie cztery łącza fizycznego między obramowania i urządzeniem sieci TOR musi być podłączony, i nie można zagwarantować wysoką dostępność ze względu na statyczne jak działa routing.

Urządzenie obramowania musi być skonfigurowany przy użyciu tras statycznych, wskazując urządzeń TOR P2P ruch kierowany do sieci zewnętrznej lub publiczne adresy VIP i siecią infrastruktury. Wymaga to trasy statyczne w sieci kontrolera zarządzania płytą GŁÓWNĄ dla wdrożenia. Klienci mogą wybrać pozostawienie trasy statyczne w obramowania, aby uzyskać dostęp do niektórych zasobów, które znajdują się w sieci kontrolera zarządzania płytą GŁÓWNĄ.  Dodawanie tras statycznych do *infrastruktury przełącznika* i *przełącznika zarządzania* sieci jest opcjonalna.

Urządzenia TOR dostarczane skonfigurowany z domyślnej trasy statycznej wysyłania całego ruchu do urządzeń obramowania. Jedynym wyjątkiem ruchu domyślna reguła dotyczy prywatnej przestrzeni jest zablokowane, za pomocą listy kontroli dostępu stosowane na TOR połączenia obramowania.

Routing statyczny dotyczy tylko łącza nadrzędne między przełączniki TOR i obramowanie. Routingu dynamicznego BGP jest używany w stojaku, ponieważ niezbędnego narzędzia, usługi SLB i inne składniki i nie może być wyłączone lub usunięte.

![Routing statyczny](media/azure-stack-border-connectivity/static-routing.png)

## <a name="transparent-proxy"></a>Przezroczystym serwerem proxy
Jeśli centrum danych wymaga cały ruch do korzystania z serwera proxy, należy skonfigurować *przezroczystym serwerem proxy* do przetworzenia cały ruch z stojak, aby postępować zgodnie z zasadami oddzielania ruchu między strefami w sieci.

> [!IMPORTANT]
> Rozwiązania usługi Azure Stack nie obsługuje normalne internetowego serwera proxy.  

Przezroczystym serwerem proxy (znany także jako przechwytuje, wbudowane lub wymuszonego proxy) przechwytuje Normalna komunikacja w warstwie sieciowej bez żadnej konfiguracji specjalnych klienta. Klienci nie muszą mieć świadomość istnienia serwera proxy.

![Przezroczystym serwerem proxy](media/azure-stack-border-connectivity/transparent-proxy.png)

## <a name="next-steps"></a>Kolejne kroki
[Integracja z usługą DNS](azure-stack-integrate-dns.md)