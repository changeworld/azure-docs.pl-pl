---
title: Analiza zagrożeń i mapa alertów zabezpieczeń w Azure Security Center | Microsoft Docs
description: Dowiedz się, jak używać mapy alertów zabezpieczeń i możliwości analizy zagrożeń w Azure Security Center, aby identyfikować potencjalne zagrożenia na maszynach wirtualnych i komputerach.
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
ms.openlocfilehash: 0740beb3b1ffc200c36ca4b5c15b25017821587e
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2019
ms.locfileid: "68662335"
---
# <a name="security-alerts-map-and-threat-intelligence"></a>Mapa alertów zabezpieczeń i analiza zagrożeń
W tym artykule opisano, jak używać mapy alertów zabezpieczeń Azure Security Center i mapy analizy zagrożeń na podstawie zdarzeń zabezpieczeń w celu rozwiązywania problemów związanych z zabezpieczeniami.

> [!NOTE]
> Przycisk mapy *zdarzeń* zabezpieczeń został wycofany z 31 lipca 2019. Aby uzyskać więcej informacji i alternatywnych usług, zobacz wycofywanie [funkcji Security Center (lipiec 2019)](security-center-features-retirement-july2019.md#menu_securityeventsmap).


## <a name="how-the-security-alerts-map-works"></a>Jak działa Mapa alertów zabezpieczeń
Security Center udostępnia mapę ułatwiającą identyfikowanie zagrożeń bezpieczeństwa w środowisku. Można na przykład określić, czy dany komputer jest częścią botnet, oraz miejsce, z którego pochodzą zagrożenie. Komputery mogą stać się węzłami w botnet, gdy osoby atakujące bezprawnie zainstalują złośliwe oprogramowanie, które tajnie współdziała z poleceniem i kontrolą, które zarządzają botnet. 

Aby skompilować tę mapę, Security Center używa danych pochodzących z wielu źródeł w firmie Microsoft. Security Center używa tych danych do mapowania potencjalnych zagrożeń związanych ze środowiskiem. 

Jeden z etapów [procesu reagowania na incydenty związane z zabezpieczeniami](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide#incident-response) polega na określeniu dotkliwości danego incydentu dla systemów. Etap ten powinien obejmować następujące działania:

- Określenie natury ataku.
- Ustal punkt pochodzenia ataku.
- Określenie celu ataku Czy atak miał charakter przypadkowy czy był skierowany przeciwko Twojej organizacji w celu uzyskania konkretnych informacji?
- Zidentyfikowanie systemów, których zabezpieczenia zostały naruszone.
- Zidentyfikowanie plików, do których nastąpił dostęp, aby ustalić istotność tych plików.

Możesz użyć mapy alerty zabezpieczeń w Security Center, aby ułatwić wykonywanie tych zadań.

## <a name="access-the-security-alerts-map"></a>Dostęp do mapy alertów zabezpieczeń
Aby wizualizować bieżące zagrożenia w środowisku, Otwórz mapę alerty zabezpieczeń:

1. Otwórz pulpit nawigacyjny usługi **Security Center**.
2. W lewym okienku w obszarze **Ochrona przed zagrożeniami** wybierz pozycję **Mapa alerty zabezpieczeń**. Zostanie otwarta mapa.
3. Aby uzyskać więcej informacji na temat alertu i otrzymywać czynności zaradcze, kliknij kropkę alertu na mapie i postępuj zgodnie z instrukcjami. 
 
Mapa alertów zabezpieczeń jest oparta na alertach. Te alerty są oparte na działaniach, dla których komunikacja sieciowa była skojarzona z adresem IP, który został pomyślnie rozwiązany, niezależnie od tego, czy adres IP jest znanym ryzykownym adresem IP (na przykład znanym cryptominer) czy adresem IP, który nie został rozpoznany. wcześniej jako ryzykowne. Mapa zawiera alerty dla wszystkich subskrypcji wcześniej wybranych na platformie Azure. 

Alerty na mapie są wyświetlane zgodnie z lokalizacją geograficzną, w której zostały wykryte jako pochodzące z i są oznaczone kolorem o ważności. 
    ![Informacje o analizie zagrożeń](./media/security-center-threat-intel/security-center-alert-map.png)



## <a name="see-also"></a>Zobacz także
W tym artykule przedstawiono sposób korzystania z analizy zagrożeń w usłudze Security Center, która ułatwia wykrywanie podejrzanej aktywności. Aby dowiedzieć się więcej na temat usługi Security Center, zobacz następujące artykuły:

* [Zarządzanie alertami zabezpieczeń i reagowanie na nie w usłudze Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Dowiedz się, jak zarządzać alertami i reagować na zdarzenia związane z bezpieczeństwem w usłudze Security Center.
* [Monitorowanie kondycji zabezpieczeń w usłudze Azure Security Center](security-center-monitoring.md). Informacje na temat sposobu monitorowania kondycji zasobów platformy Azure.
* [Informacje o alertach zabezpieczeń w usłudze Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Poznaj różne typy alertów zabezpieczeń.
* [Przewodnik rozwiązywania problemów z usługą Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Dowiedz się, jak rozwiązywać typowe problemy z usługą Security Center.
* [Azure Security Center — często zadawane pytania](security-center-faq.md). Odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
* [Blog Azure Security](https://blogs.msdn.com/b/azuresecurity/). Wpisy na blogu dotyczące zabezpieczeń i zgodności platformy Azure.
