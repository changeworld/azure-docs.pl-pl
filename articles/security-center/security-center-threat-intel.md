---
title: Analiza zagrożeń w usłudze Azure Security Center | Microsoft Docs
description: Dowiedz się, jak wykrywać potencjalne zagrożenia na maszynach wirtualnych i komputerach przy użyciu funkcji analizy zagrożeń w usłudze Azure Security Center.
services: security-center
documentationcenter: na
author: terrylan
manager: mbaldwin
editor: ''
ms.assetid: a771a3a1-2925-46ca-8e27-6f6a0746f58b
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2017
ms.author: yurid
ms.openlocfilehash: 9654c25bf6b51d48dc195d6a536b61312fdcbeff
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
ms.locfileid: "32774577"
---
# <a name="threat-intelligence-in-azure-security-center"></a>Analiza zagrożeń w usłudze Azure Security Center
Ten artykuł ułatwia korzystanie z analizy zagrożeń usługi Azure Security Center w celu rozwiązywania problemów związanych z zabezpieczeniami.

## <a name="what-is-threat-intelligence"></a>Czym jest analiza zagrożeń?
Używając opcji Analiza zagrożeń dostępnej w usłudze Security Center, administratorzy IT mogą identyfikować zagrożenia dla bezpieczeństwa środowiska. Na przykład mogą zidentyfikować, czy określony komputer jest częścią botnetu. Komputery mogą stać się węzłami w botnecie, gdy osoby atakujące bezprawnie zainstalują złośliwe oprogramowanie, które potajemnie łączy komputer z centrum sterowania. Analiza zagrożeń może też identyfikować potencjalne zagrożenia pochodzące z tajnych kanałów komunikacji, takich jak podziemny Internet.

Aby umożliwić używanie analizy zagrożeń, usługa Security Center korzysta z danych, które pochodzą z wielu źródeł firmy Microsoft. Usługa Security Center używa tych danych do wykrywania potencjalnych zagrożeń dla Twojego środowiska. Okienko **Analiza zagrożeń** zawiera trzy główne opcje:

- Typy wykrytych zagrożeń
- Pochodzenie zagrożeń
- Mapa analizy zagrożeń


## <a name="when-should-you-use-threat-intelligence"></a>Kiedy należy używać analizy zagrożeń?
Jeden z etapów [procesu reagowania na incydenty związane z zabezpieczeniami](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide#incident-response) polega na określeniu dotkliwości danego incydentu dla systemów. Etap ten powinien obejmować następujące działania:

- Określenie natury ataku.
- Określenie źródła ataku.
- Określenie celu ataku Czy atak miał charakter przypadkowy czy był skierowany przeciwko Twojej organizacji w celu uzyskania konkretnych informacji?
- Zidentyfikowanie systemów, których zabezpieczenia zostały naruszone.
- Zidentyfikowanie plików, do których nastąpił dostęp, aby ustalić istotność tych plików.

Informacji dotyczących analizy zagrożeń możesz użyć w usłudze Security Center, aby ułatwić te zadania. 

## <a name="access-the-threat-intelligence"></a>Uzyskiwanie dostępu do analizy zagrożeń
Aby wyświetlić bieżącą analizę zagrożeń dla środowiska, musisz najpierw wybrać obszar roboczy, gdzie są przechowywane informacje. Jeśli masz tylko jeden obszar roboczy, pomiń selektor obszarów roboczych i od razu przejdź do pulpitu nawigacyjnego **Analiza zagrożeń**. Aby uzyskać dostęp do pulpitu nawigacyjnego:

1. Otwórz pulpit nawigacyjny usługi **Security Center**.

2. W lewym okienku, w obszarze **Wykrywanie**, wybierz pozycję **Analiza zagrożeń**. Zostanie wyświetlony pulpit nawigacyjny **Analiza zagrożeń**.

    ![Pulpit nawigacyjny analizy zagrożeń](./media/security-center-threat-intel/security-center-threat-intel-fig1.png)

    > [!NOTE]
    > Jeśli skrajna prawa kolumna zawiera napis **PLAN UAKTUALNIENIA**, ten obszar roboczy używa bezpłatnej subskrypcji. Uaktualnij go do wersji Standard, aby użyć tej funkcji. Jeśli skrajna prawa kolumna zawiera napis **WYMAGA AKTUALIZACJI**, zaktualizuj usługę [Azure Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview), aby użyć tej funkcji. Więcej informacji na temat planu cenowego zawiera cennik usługi Azure Security Center. 
    > 
3. Jeśli masz więcej niż jeden obszar roboczy do sprawdzenia, określ priorytet dochodzenia zgodnie z kolumną **Złośliwy adres IP**. Pokazuje ona bieżącą liczbę złośliwych adresów IP w tym obszarze roboczym. Po wybraniu odpowiedniego obszaru roboczego pojawi się pulpit nawigacyjny **Analiza zagrożeń**.

    ![Informacje dotyczące analizy zagrożeń](./media/security-center-threat-intel/security-center-threat-intel-fig5.png)

4. Pulpit nawigacyjny jest podzielony na cztery kafelki:

    a.  **Typy zagrożeń**. Zawiera podsumowanie typów zagrożeń, które zostały wykryte w wybranym obszarze roboczym.

    b.  **Kraj pochodzenia**. Zawiera zagregowane informacje o ruchu sieciowym zgrupowane według lokalizacji źródłowej.

    d.  **Lokalizacja zagrożeń**. Ułatwia ustalenie, z których miejsc na świecie są wysyłane komunikaty do Twojego środowiska. Na pokazanej mapie strzałki pomarańczowa (ruch przychodzący) i czerwona (ruch wychodzący) wskazują kierunki ruchu. Po wybraniu jednej z tych strzałek zostanie wyświetlony typ zagrożeń i kierunek ruchu.

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


## <a name="see-also"></a>Zobacz też
W tym artykule przedstawiono sposób korzystania z analizy zagrożeń w usłudze Security Center, która ułatwia wykrywanie podejrzanej aktywności. Aby dowiedzieć się więcej na temat usługi Security Center, zobacz następujące artykuły:

* [Zarządzanie alertami zabezpieczeń i reagowanie na nie w usłudze Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Dowiedz się, jak zarządzać alertami i reagować na zdarzenia związane z bezpieczeństwem w usłudze Security Center.
* [Monitorowanie kondycji zabezpieczeń w usłudze Azure Security Center](security-center-monitoring.md). Informacje na temat sposobu monitorowania kondycji zasobów platformy Azure.
* [Informacje o alertach zabezpieczeń w usłudze Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Poznaj różne typy alertów zabezpieczeń.
* [Przewodnik rozwiązywania problemów z usługą Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Dowiedz się, jak rozwiązywać typowe problemy z usługą Security Center. 
* [Azure Security Center — często zadawane pytania](security-center-faq.md). Odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
* [Blog Azure Security](http://blogs.msdn.com/b/azuresecurity/). Wpisy na blogu dotyczące zabezpieczeń i zgodności platformy Azure.

