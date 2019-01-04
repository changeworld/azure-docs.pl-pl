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
ms.date: 08/30/2018
ms.author: jeffgilb
ms.reviewer: wamota
ms.openlocfilehash: 12219e2df875d317aece73cabebdfb55115f7b41
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2019
ms.locfileid: "54021088"
---
# <a name="border-connectivity"></a>Łączność obramowania 
Planowanie integracji sieci jest ważnego wymagania wstępnego pomyślne wdrożenie systemów zintegrowanych w usłudze Azure Stack, operacji i zarządzania. Planowanie łączności obramowania rozpoczyna się przez wybranie, czy nie używają routingu dynamicznego przy użyciu protokołu border gateway protocol (BGP). Wymaga to przypisanie 16-bitowy numer systemu autonomicznego BGP (publicznym lub prywatnym) lub przy użyciu routingu statycznego, gdy domyślnej trasy statycznej jest przypisany do urządzeń obramowania.

> [!IMPORTANT]
> Górnej przełączników rack (TOR) wymagają pasm warstwy 3 z adresów IP Point-to-Point (/ 30 sieci) skonfigurowany na interfejsy fizyczne. Za pomocą usług warstwy 2 pasm przełączniki TOR obsługują operacje usługi Azure Stack nie jest obsługiwana. 

## <a name="bgp-routing"></a>Routing protokołu BGP
Przy użyciu protokołu routingu dynamicznego takich jak Protokół BGP gwarantuje, że system jest zawsze pamiętać o zmiany w sieci i ułatwia administrowanie. Zabezpieczeń WE4 wprowadziła zaawansowany hasła mogą zostać ustawione dla protokołu BGP komunikacja równorzędna między TOR i obramowanie. 

Jak pokazano na poniższym diagramie, anonsowanie z prywatnym adresem IP miejsca na przełączniku TOR jest blokowana przy użyciu listę prefiksów. Anons sieć prywatna nie zezwala na listę prefiksów i jest stosowane jako mapę trasy dla połączenia między TOR i obramowanie.

Oprogramowanie modułu równoważenia obciążenia (SLB) uruchomionych elementów równorzędnych rozwiązania usługi Azure Stack na urządzeniach z sieci TOR tak dynamicznie można ogłaszać dłuższe adresów VIP.

Aby zapewnić, że ruch użytkowników tak, natychmiast i w sposób niewidoczny dla użytkownika przywraca po awarii, VPC lub MLAG skonfigurowana między urządzeniami sieci TOR umożliwia korzystanie z wielu obudowy łącze agregacji do hostów i HSRP lub VRRP, która zapewnia sieci nadmiarowości w sieciach IP.

![Routing protokołu BGP](media/azure-stack-border-connectivity/bgp-routing.png)

## <a name="static-routing"></a>Routing statyczny
Routing statyczny wymaga dodatkowej konfiguracji do urządzenia obramowania. Wymaga ona więcej ręcznej interwencji i zarządzania, a także dogłębnej analizy przed wszelkie zmiany i problemy spowodowane przez błąd konfiguracji może zająć więcej czasu wycofywania, w zależności od zmian. Nie jest to zalecana metoda routingu, ale jest on obsługiwany.

Aby zintegrować usługi Azure Stack w środowisku sieci przy użyciu routingu statycznego, wszystkie cztery łącza fizycznego między obramowania i urządzeniem sieci TOR musi być podłączony, i nie można zagwarantować wysoką dostępność ze względu na statyczne jak działa routing.

Urządzenie obramowania musi być skonfigurowany z trasy statyczne, które wskazuje urządzeniom TOR P2P, aby ruch skierowany do *zewnętrznych* sieci lub publicznych adresów VIP i *infrastruktury* sieci. Wymaga tras statycznych do *BMC* i *zewnętrznych* sieci dla wdrożenia. Operatory można pozostawić trasy statyczne w granicy do dostępu do zarządzania zasobami, które znajdują się na *BMC* sieci. Dodawanie tras statycznych do *infrastruktury przełącznika* i *przełącznika zarządzania* sieci jest opcjonalna.

Urządzenia TOR dostarczane skonfigurowany z domyślnej trasy statycznej wysyłania całego ruchu do urządzeń obramowania. Jedynym wyjątkiem ruchu domyślna reguła dotyczy prywatnej przestrzeni jest zablokowane, za pomocą listy kontroli dostępu stosowane na TOR połączenia obramowania.

Routing statyczny dotyczy tylko łącza nadrzędne między przełączniki TOR i obramowanie. Routingu dynamicznego BGP jest używany w stojaku, ponieważ niezbędnego narzędzia, usługi SLB i inne składniki i nie może być wyłączone lub usunięte.

![Routing statyczny](media/azure-stack-border-connectivity/static-routing.png)

<sup>\*</sup> Sieci kontrolera zarządzania płytą GŁÓWNĄ jest opcjonalny po wdrożeniu.

<sup>\*\*</sup> Sieć infrastruktury przełącznika jest opcjonalny, zgodnie z całej sieci mogą być zawarte w sieci przełącznika zarządzania.

<sup>\*\*\*</sup> Sieć zarządzania przełącznik jest wymagany i można dodać oddzielnie od infrastruktury przełącznika sieci.

## <a name="transparent-proxy"></a>Przezroczystym serwerem proxy
Jeśli centrum danych wymaga cały ruch do korzystania z serwera proxy, należy skonfigurować *przezroczystym serwerem proxy* do przetworzenia cały ruch z stojak, aby postępować zgodnie z zasadami oddzielania ruchu między strefami w sieci.

> [!IMPORTANT]
> Rozwiązania usługi Azure Stack nie obsługuje normalne internetowego serwera proxy.  

Przezroczystym serwerem proxy (znany także jako przechwytuje, wbudowane lub wymuszonego proxy) przechwytuje Normalna komunikacja w warstwie sieciowej bez żadnej konfiguracji specjalnych klienta. Klienci nie muszą mieć świadomość istnienia serwera proxy.

![Przezroczystym serwerem proxy](media/azure-stack-border-connectivity/transparent-proxy.png)

## <a name="next-steps"></a>Kolejne kroki
[Integracja z usługą DNS](azure-stack-integrate-dns.md)
