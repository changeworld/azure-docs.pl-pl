---
title: Zagrożeń analizy i Mapa alertów zabezpieczeń w usłudze Azure Security Center | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać funkcji alertu zabezpieczeń mapy i zagrożeniami analizy w usłudze Azure Security Center można wykrywać potencjalne zagrożenia na maszynach wirtualnych i komputerów.
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
ms.openlocfilehash: 36201bad64e5516375afe1ec9ce141c3fd311d48
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64574349"
---
# <a name="security-alerts-map-and-threat-intelligence"></a>Mapa alertów zabezpieczeń i analiza zagrożeń
Ten artykuł ułatwia korzystanie Mapa alertów zabezpieczeń usługi Azure Security Center i Mapa analizy zagrożeń na podstawie zdarzeń zabezpieczeń do rozwiązania problemów związanych z zabezpieczeniami.

> [!NOTE]
> Zabezpieczenia *zdarzenia* przycisk mapy zostaną wycofane z dniem 31 lipca 2019 r. Aby uzyskać więcej informacji i alternatywne usług zobacz [wycofywania Centrum zabezpieczeń funkcji (2019 lipca)](security-center-features-retirement-july2019.md#menu_securityeventsmap).


## <a name="how-the-security-alerts-map-works"></a>Sposób mapowania alerty zabezpieczeń działania
Usługa Security Center zapewnia, że Ci mapę, która pomoże Ci identyfikować zagrożenia bezpieczeństwa środowiska. Na przykład można zidentyfikować, czy dany komputer jest częścią botnetu, a w przypadku, gdy zagrożenia pochodzi z. Komputerów może stać się węzłami w botnecie, gdy osoby atakujące bezprawnie zainstalują złośliwe oprogramowanie, które potajemnie wchodzi w interakcję z poleceń i kontroli, która zarządzać botnetem. 

Aby utworzyć to mapowanie, Security Center korzysta z danych pochodzących z wielu źródeł firmy Microsoft. Usługa Security Center używa tych danych, aby zamapować potencjalnych zagrożeń dla środowiska. 

Jeden z etapów [procesu reagowania na incydenty związane z zabezpieczeniami](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide#incident-response) polega na określeniu dotkliwości danego incydentu dla systemów. Etap ten powinien obejmować następujące działania:

- Określenie natury ataku.
- Określ punkt początkowy ataku.
- Określenie celu ataku Czy atak miał charakter przypadkowy czy był skierowany przeciwko Twojej organizacji w celu uzyskania konkretnych informacji?
- Zidentyfikowanie systemów, których zabezpieczenia zostały naruszone.
- Zidentyfikowanie plików, do których nastąpił dostęp, aby ustalić istotność tych plików.

Mapa alertów zabezpieczeń w usłudze Security Center umożliwia ułatwić wykonanie tych zadań.

## <a name="access-the-security-alerts-map"></a>Dostęp do mapy alerty zabezpieczeń
Aby wyświetlić bieżące zagrożenia w swoim środowisku, otwórz mapę alerty zabezpieczeń:

1. Otwórz pulpit nawigacyjny usługi **Security Center**.
2. W okienku po lewej stronie w obszarze **ochrony przed zagrożeniami** wybierz **Mapa alertów zabezpieczeń**. Zostanie otwarty mapy.
3. Aby uzyskać więcej informacji na temat alertu i odbierać rekomendowania czynności naprawczych, kliknij Alert kropki (.) na mapie i postępuj zgodnie z instrukcjami. 
 
Mapa alertów zabezpieczeń jest na podstawie alertów. Te alerty są oparte na działania dla sieci, w której został skojarzony z adresu IP, która została pomyślnie rozpoznana, czy adres IP jest znane ryzykownego adresu IP (na przykład znanych cryptominer) lub adres IP, który nie jest rozpoznawany komunikacji wcześniej jako ryzykowne. Mapy zawiera alertów w subskrypcjach, wszystkie wcześniej wybrane na platformie Azure. 

Alerty na mapie są wyświetlane według lokalizacji geograficznej, gdzie one są wykrywane jako pochodzące z komputera i są one kolorami według ważności. 
    ![Informacje dotyczące analizy zagrożeń](./media/security-center-threat-intel/security-center-alert-map.png)

## <a name="viewing-the-event-based-threat-intelligence-dashboard"></a>Wyświetlanie pulpitu nawigacyjnego oparty na zdarzeniach analizy zagrożeń
Aby wyświetlić Mapa analizy zagrożeń na podstawie zdarzeń zabezpieczeń nieprzetworzone, można wykonać tę procedurę. Ta mapa wyświetla tylko te zdarzenia, które obejmują adres IP, który jest uważany za zagrożenie, na przykład adres IP znanego botnetu.

1. Otwórz pulpit nawigacyjny usługi **Security Center**.

1. W okienku po lewej stronie w obszarze **ochrony przed zagrożeniami** wybierz **Mapa alertów zabezpieczeń**. Zostanie otwarty mapy.
2. W prawym górnym rogu, kliknij przycisk **przejdź do mapy zdarzeń zabezpieczeń**.
3. Wybierz obszar roboczy, dla którego chcesz wyświetlić pulpit nawigacyjny.
4. W górnej części mapy wybierz **wyświetlić analizy zagrożeń klasycznego**. **Analiza zagrożeń** zostanie otwarty pulpit nawigacyjny.

   > [!NOTE]
   > Jeśli skrajna prawa kolumna zawiera napis **PLAN UAKTUALNIENIA**, ten obszar roboczy używa bezpłatnej subskrypcji. Uaktualnij go do wersji Standard, aby użyć tej funkcji. Jeśli skrajna prawa kolumna zawiera napis **wymaga aktualizacji**, zaktualizuj [dzienniki usługi Azure Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) Aby użyć tej funkcji. Więcej informacji na temat planu cenowego zawiera cennik usługi Azure Security Center.
   >
5. Jeśli masz więcej niż jeden obszar roboczy do sprawdzenia, określ priorytet dochodzenia zgodnie z kolumną **Złośliwy adres IP**. Pokazuje ona bieżącą liczbę złośliwych adresów IP w tym obszarze roboczym. Po wybraniu odpowiedniego obszaru roboczego pojawi się pulpit nawigacyjny **Analiza zagrożeń**.

    ![Informacje dotyczące analizy zagrożeń](./media/security-center-threat-intel/security-center-threat-intel-fig5.png)

6. Pulpit nawigacyjny jest podzielony na cztery kafelki:

    a.  **Typy zagrożeń**. Zawiera podsumowanie typów zagrożeń, które zostały wykryte w wybranym obszarze roboczym.

    b.  **Kraj pochodzenia**. Zawiera zagregowane informacje o ruchu sieciowym zgrupowane według lokalizacji źródłowej.

    c.  **Lokalizacja zagrożeń**. Ułatwia ustalenie, z których miejsc na świecie są wysyłane komunikaty do Twojego środowiska. Na pokazanej mapie strzałki pomarańczowa (ruch przychodzący) i czerwona (ruch wychodzący) wskazują kierunki ruchu. Po wybraniu jednej z tych strzałek zostanie wyświetlony typ zagrożeń i kierunek ruchu.

    d.  **Szczegóły zagrożenia**. Zawiera więcej informacji na temat zagrożenia zaznaczonego na mapie.

Niezależnie od tego, który kafelek opcji wybierzesz, wyświetlany pulpit nawigacyjny jest oparty na zapytaniu przeszukiwania dzienników. Jedyną różnicą jest typ zapytania i wynik.

### <a name="threat-types"></a>Typy zagrożeń
Wybierz kafelek **Typy zagrożeń**, aby otworzyć pulpit nawigacyjny **Przeszukiwanie dzienników**. Opcje filtrowania są wyświetlane po lewej stronie, a po prawej stronie są wyświetlane wyniki zapytania.

![Przeszukiwanie dzienników](./media/security-center-threat-intel/security-center-threat-intel-fig3.png)

Wynik zapytania przedstawia zagrożenia według nazwy. W okienku po lewej stronie możesz wybrać atrybut, który chcesz odfiltrować. Na przykład aby wyświetlić tylko zagrożenia, które są aktualnie połączone z maszynami, w obszarze **STAN SESJI**, wybierz pozycje **Połączony** > **Zastosuj**.

![Stan sesji](./media/security-center-threat-intel/security-center-threat-intel-fig4.png)

W przypadku maszyn wirtualnych platformy Azure pulpit nawigacyjny **Analiza zagrożeń** zawiera tylko dane sieciowe, które przechodzą przez agenta. Funkcja analizy zagrożeń korzysta również z następujących typów danych:

- Dane CEF (typ = CommonSecurityLog)
- Dane WireData (typ = WireData)
- Dzienniki usług IIS (typ = W3CIISLog)
- Zapora systemu Windows (typ = WindowsFirewall)
- Zdarzenia DNS (typ = DnsEvents)


## <a name="see-also"></a>Zobacz także
W tym artykule przedstawiono sposób korzystania z analizy zagrożeń w usłudze Security Center, która ułatwia wykrywanie podejrzanej aktywności. Aby dowiedzieć się więcej na temat usługi Security Center, zobacz następujące artykuły:

* [Zarządzanie alertami zabezpieczeń i reagowanie na nie w usłudze Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Dowiedz się, jak zarządzać alertami i reagować na zdarzenia związane z bezpieczeństwem w usłudze Security Center.
* [Monitorowanie kondycji zabezpieczeń w usłudze Azure Security Center](security-center-monitoring.md). Informacje na temat sposobu monitorowania kondycji zasobów platformy Azure.
* [Informacje o alertach zabezpieczeń w usłudze Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Poznaj różne typy alertów zabezpieczeń.
* [Przewodnik rozwiązywania problemów z usługą Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Dowiedz się, jak rozwiązywać typowe problemy z usługą Security Center.
* [Azure Security Center — często zadawane pytania](security-center-faq.md). Odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
* [Blog Azure Security](https://blogs.msdn.com/b/azuresecurity/). Wpisy na blogu dotyczące zabezpieczeń i zgodności platformy Azure.
