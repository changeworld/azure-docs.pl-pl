---
title: Analiza zagrożeń & mapa alertów zabezpieczeń — Usługa Azure Security Center
description: Dowiedz się, jak korzystać z mapy alertów zabezpieczeń i funkcji analizy zagrożeń w usłudze Azure Security Center w celu identyfikowania potencjalnych zagrożeń na maszynach wirtualnych i komputerach.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: a771a3a1-2925-46ca-8e27-6f6a0746f58b
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/3/2018
ms.author: rkarlin
ms.openlocfilehash: 39835d9d73adcbe474d3b70dfced313e18d1a3b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77603418"
---
# <a name="security-alerts-map-and-threat-intelligence"></a>Mapa alertów zabezpieczeń i analiza zagrożeń
Ten artykuł ułatwia korzystanie z mapy alertów zabezpieczeń usługi Azure Security Center i mapy analizy zagrożeń opartej na zdarzeniach zabezpieczeń w celu rozwiązania problemów związanych z zabezpieczeniami.

> [!NOTE]
> Przycisk Mapa *zdarzeń* bezpieczeństwa został wycofany 31 lipca 2019. Aby uzyskać więcej informacji i usług alternatywnych, zobacz [Wycofanie funkcji Centrum zabezpieczeń (lipiec 2019)](security-center-features-retirement-july2019.md#menu_securityeventsmap).


## <a name="how-the-security-alerts-map-works"></a>Jak działa mapa alertów zabezpieczeń
Usługa Security Center udostępnia mapę ułatwianą identyfikację zagrożeń bezpieczeństwa dla środowiska. Na przykład można określić, czy dany komputer jest częścią botnetu i skąd pochodzi zagrożenie. Komputery mogą stać się węzłami w botnecie, gdy atakujący nielegalnie instalują złośliwe oprogramowanie, które potajemnie współdziała z poleceniami i kontrolą, które zarządzają botnetem. 

Aby utworzyć tę mapę, Usługa Security Center używa danych pochodzących z wielu źródeł w firmie Microsoft. Usługa Security Center używa tych danych do mapowania potencjalnych zagrożeń dla środowiska. 

Jeden z etapów [procesu reagowania na incydenty związane z zabezpieczeniami](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide#incident-response) polega na określeniu dotkliwości danego incydentu dla systemów. Etap ten powinien obejmować następujące działania:

- Określenie natury ataku.
- Określ punkt pochodzenia ataku.
- Określenie celu ataku Czy atak miał charakter przypadkowy czy był skierowany przeciwko Twojej organizacji w celu uzyskania konkretnych informacji?
- Zidentyfikowanie systemów, których zabezpieczenia zostały naruszone.
- Zidentyfikowanie plików, do których nastąpił dostęp, aby ustalić istotność tych plików.

Aby ułatwić wykonywanie tych zadań, można użyć mapy Alerty zabezpieczeń w centrum zabezpieczeń.

## <a name="access-the-security-alerts-map"></a>Dostęp do mapy Alerty zabezpieczeń
Aby zwizualizować bieżące zagrożenia w środowisku, otwórz mapę Alerty zabezpieczeń:

1. Otwórz pulpit nawigacyjny usługi **Security Center**.
2. W lewym okienku w obszarze **Ochrona przed zagrożeniami** wybierz **mapę Alertów zabezpieczeń**. Zostanie otwarta mapa.
3. Aby uzyskać więcej informacji na temat alertu i otrzymać kroki korygowania, kliknij kropkę alert na mapie i postępuj zgodnie z instrukcjami. 
 
Mapa alertów zabezpieczeń jest oparta na alertach. Alerty te są oparte na działaniach, dla których komunikacja sieciowa została skojarzona z adresem IP, który został pomyślnie rozwiązany, niezależnie od tego, czy adres IP jest znanym ryzykownym adresem IP (na przykład znanym cryptominerem) czy adresem IP, który nie został rozpoznany wcześniej jako ryzykowne. Mapa zawiera alerty we wszystkich subskrypcjach wcześniej wybranych na platformie Azure. 

Alerty na mapie są wyświetlane zgodnie z lokalizacją geograficzną, z której są wykrywane jako pochodzące, i są oznaczone kolorami według ważności. 
    ![Informacje dotyczące analizy zagrożeń](./media/security-center-threat-intel/security-center-alert-map.png)



## <a name="see-also"></a>Zobacz też
W tym artykule przedstawiono sposób korzystania z analizy zagrożeń w usłudze Security Center, która ułatwia wykrywanie podejrzanej aktywności. Aby dowiedzieć się więcej na temat usługi Security Center, zobacz następujące artykuły:

* [Zarządzanie alertami zabezpieczeń i reagowanie na nie w usłudze Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Dowiedz się, jak zarządzać alertami i reagować na zdarzenia związane z bezpieczeństwem w usłudze Security Center.
* [Monitorowanie kondycji zabezpieczeń w usłudze Azure Security Center](security-center-monitoring.md). Informacje na temat sposobu monitorowania kondycji zasobów platformy Azure.
* [Informacje o alertach zabezpieczeń w usłudze Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Poznaj różne typy alertów zabezpieczeń.
* [Przewodnik po rozwiązywaniu problemów z usługą Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Dowiedz się, jak rozwiązywać typowe problemy z usługą Security Center.