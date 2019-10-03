---
title: Zapobieganie nieoczekiwanym kosztom i zarządzanie rozliczeniami na platformie Azure
description: Dowiedz się, jak unikać nieoczekiwanych opłat na rachunku za korzystanie z platformy Azure. Używanie funkcji śledzenia kosztów i zarządzania dla subskrypcji platformy Azure.
author: bandersmsft
manager: amberb
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: d6c287d5ead0095a4f7bb5ad754212b134f7103c
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71719819"
---
# <a name="prevent-unexpected-charges-with-azure-billing-and-cost-management"></a>Zapobieganie nieoczekiwanym opłatom w rozliczeniach platformy Azure i zarządzanie kosztami

Kiedy rejestrujesz się na platformie Azure, możesz zrobić kilka rzeczy, aby lepiej zrozumieć swoje wydatki:

- Za pomocą [kalkulatora cen](https://azure.microsoft.com/pricing/calculator/) możesz uzyskać oszacowanie kosztów przed utworzeniem zasobu platformy Azure. 

- Witryna [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) oferuje podział bieżących kosztów i prognozę dla Twojej subskrypcji. 

- Jeśli chcesz pogrupować i poznać koszty dla różnych projektów lub zespołów, zapoznaj się z [tagowaniem zasobów](../azure-resource-manager/resource-group-using-tags.md). Jeśli Twoja organizacja ma system raportowania, którego wolisz używać, zapoznaj się z [interfejsami API rozliczeń](billing-usage-rate-card-overview.md).

- Jeśli Twoja subskrypcja została utworzona na podstawie umowy Enterprise Agreement (EA), możesz wyświetlić swoje koszty w witrynie Azure Portal. Jeśli Twoja subskrypcja została nabyta za pośrednictwem dostawcy rozwiązań w chmurze (CSP) lub programu Dostęp sponsorowany Azure, niektóre z poniższych funkcji mogą Cię nie dotyczyć. Aby uzyskać więcej informacji, zobacz [Dodatkowe zasoby dla umowy Enterprise Agreement, dostawcy rozwiązań w chmurze i dostępu sponsorowanego](#other-offers).

- Jeśli Twoja subskrypcja to oferta bezpłatnej wersji próbnej, program [Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), opcja Azure w ramach programu licencjonowania Open (AIO) lub program BizSpark, subskrypcja zostanie automatycznie wyłączona po zużyciu wszystkich środków. Dowiedz się więcej na temat [limitów wydatków](#spending-limit), aby uniknąć nieoczekiwanego wyłączenia subskrypcji.

- Jeśli Twoja rejestracja obejmuje [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/), [możesz bezpłatnie korzystać z najpopularniejszych usług platformy Azure przez 12 miesięcy](billing-create-free-services-included-free-account.md). Oprócz zaleceń wymienionych poniżej, zobacz [Unikanie naliczania opłat za bezpłatne konto](billing-avoid-charges-free-account.md).

## <a name="get-estimated-costs-before-adding-azure-services"></a>Uzyskiwanie szacowanych kosztów przed dodaniem usług platformy Azure

Poniżej znajdują się dodatkowe informacje na temat szacowania kosztów przy użyciu następujących narzędzi:
- Kalkulator cen platformy Azure
- Portal Azure
- Limit wydatków

Obrazy w poniższych sekcjach pokazują przykładowe ceny w dolarach amerykańskich.

### <a name="estimate-cost-online-using-the-pricing-calculator"></a>Szacowanie kosztów online za pomocą kalkulatora cen

Skorzystaj z [kalkulatora cen](https://azure.microsoft.com/pricing/calculator/), aby uzyskać szacowany miesięczny koszt usługi, która Cię interesuje. Aby uzyskać szacowany koszt, możesz dodać dowolny, oryginalny zasób platformy Azure. W kalkulatorze cen można zmieniać typ waluty.

![Zrzut ekranu przedstawiający menu kalkulatora cen](./media/billing-getting-started/pricing-calc.png)

Kalkulator cen szacuje na przykład, że maszyna wirtualna A1 z systemem Windows kosztuje określoną kwotę/miesiąc w godzinach obliczeniowych, jeśli będzie działać przez cały czas:

![Zrzut ekranu kalkulatora cen z szacowanym miesięcznym kosztem maszyny wirtualnej A1 z systemem Windows](./media/billing-getting-started/pricing-calcvm.png)

Aby uzyskać więcej informacji na temat cen, zobacz [Cennik — często zadawane pytania](https://azure.microsoft.com/pricing/faq/). Jeśli chcesz skontaktować się ze sprzedawcą platformy Azure, zadzwoń na numer telefonu widoczny w górnej części strony często zadawanych pytań.

### <a name="review-estimated-costs-in-the-azure-portal"></a>Przeglądanie szacowanych kosztów w witrynie Azure Portal

Na ogół po dodaniu usługi w witrynie Azure Portal jest wyświetlany widok pokazujący szacowany miesięczny koszt w Twojej walucie rozliczeniowej. Na przykład po wybraniu rozmiaru maszyny wirtualnej z systemem Windows zobaczysz szacowany miesięczny koszt dla godzin obliczeniowych:

![Przykład: maszyna wirtualna A1 z systemem Windows z widocznym szacowanym kosztem miesięcznym](./media/billing-getting-started/vm-size-cost.png)

### <a name="spending-limit"></a> Sprawdzanie, czy limit wydatków jest włączony

Jeśli masz subskrypcję z dostępnymi środkami, limit wydatków jest domyślnie włączony. Dzięki temu, gdy zużyjesz wszystkie środki, karta kredytowa nie zostanie obciążona. Zobacz [pełną listę ofert platformy Azure i dostępność limitu wydatków](https://azure.microsoft.com/support/legal/offer-details/).

Jednak po osiągnięciu limitu wydatków usługi są wyłączane. Oznacza to, że przydział maszyn wirtualnych jest cofany. Aby uniknąć przestoju usługi, musisz wyłączyć limit wydatków. Wszelkie nadwyżki są naliczane na wprowadzoną kartę kredytową.

Aby sprawdzić, czy masz włączony limit wydatków, przejdź do [widoku subskrypcji w Centrum konta](https://account.windowsazure.com/Subscriptions). Jeśli limit wydatków jest włączony, będzie widoczny baner podobny do następującego:

![Zrzut ekranu pokazujący ostrzeżenie o włączonym limicie wydatków w Centrum konta](./media/billing-getting-started/spending-limit-banner.png)

Kliknij baner i postępuj zgodnie z wyświetlanymi monitami, aby usunąć limit wydatków. Jeśli podczas rejestracji nie wprowadzono informacji o karcie kredytowej, należy je wprowadzić, aby usunąć limit wydatków. Aby uzyskać więcej informacji, zobacz [Limit wydatków platformy Azure — jak to działa i jak włączyć lub usunąć tę funkcję](https://azure.microsoft.com/pricing/spending-limits/).

## <a name="use-budgets-and-cost-alerts"></a>Używanie alertów dotyczących budżetu i kosztów

W celu zarządzania kosztami można utworzyć [budżety](../cost-management/tutorial-acm-create-budgets.md) oraz [alerty](../cost-management/cost-mgt-alerts-monitor-usage-spending.md), które automatycznie powiadamiają uczestników o anomaliach w wydatkach i o ryzyku nadmiernych wydatków. Alerty są oparte na wydatkach w porównaniu z budżetem i progami kosztów.

## <a name="monitor-costs-when-using-azure-services"></a>Monitorowanie kosztów podczas korzystania z usług platformy Azure
Koszty można monitorować przy użyciu następujących narzędzi:

- Tagi
- Podział kosztów i ocena wydajności
- Analiza kosztów

### <a name="tags"></a> Dodawanie tagów do zasobów w celu grupowania danych dotyczących rozliczeń

Za pomocą tagów można grupować dane dotyczące rozliczeń dla obsługiwanych usług. Jeśli na przykład uruchamiasz kilka maszyn wirtualnych dla różnych zespołów, możesz przy użyciu tagów podzielić koszty na kategorie według centrum kosztów (na przykład: dział kadr, marketing, finanse itp.) lub według środowiska (na przykład: produkcyjne, przedprodukcyjne, testowe).

![Zrzut ekranu przedstawiający konfigurowanie tagów w portalu](./media/billing-getting-started/tags.png)

Tagi są wyświetlane w różnych widokach raportów o kosztach. Na przykład są one od razu widoczne w [widoku analizy kosztów](#costs), a po pierwszym okresie rozliczeniowym są widoczne w pliku CSV szczegółowego użycia.

Aby uzyskać więcej informacji, zobacz [Porządkowanie zasobów na platformie Azure za pomocą tagów](../azure-resource-manager/resource-group-using-tags.md).

### <a name="costs"></a> Monitorowanie podziału kosztów i oceny wydajności

Po uruchomieniu usług platformy Azure regularnie sprawdzaj opłaty. Bieżące wydatki i ocena wydajności są widoczne w witrynie Azure Portal.

1. Odwiedź stronę [Subskrypcje w witrynie Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) i wybierz subskrypcję.

2. Jeśli te funkcje są obsługiwana dla Twojej subskrypcji, zobaczysz podział kosztów i ocenę wydajności.

    ![Zrzut ekranu przedstawiający ocenę wydajności i podział w witrynie Azure Portal](./media/billing-getting-started/burn-rate.PNG)

3. Kliknij pozycję [Analiza kosztów](../cost-management/quick-acm-cost-analysis.md) na liście po lewej stronie, aby wyświetlić podział kosztów według zasobu. Po dodaniu usługi poczekaj 24 godziny na wyświetlenie danych.

    ![Zrzut ekranu przedstawiający widok analizy kosztów w witrynie Azure Portal](./media/billing-getting-started/cost-analysis.png)

4. Możesz filtrować według różnych właściwości, takich jak [tagi](#tags), typ zasobu, grupa zasobów i przedział czasu. Kliknij pozycję **Zastosuj**, aby potwierdzić filtry, i pozycję **Pobierz**, jeśli chcesz wyeksportować widok do pliku wartości rozdzielanych przecinkami (CSV).

5. Ponadto możesz kliknąć zasób, aby zobaczyć dzienną historię wydatków oraz codzienny koszt zasobu.

    ![Zrzut ekranu przedstawiający widok historii wydatków w witrynie Azure Portal](./media/billing-getting-started/costhistory.png)

Porównaj pokazywane koszty z oszacowaniami, które zostały obliczone po wybraniu usług. Jeśli koszty znacznie różnią się od oszacowania, sprawdź plan cenowy, który został wybrany dla zasobów.

## <a name="optimize-and-reduce-costs"></a>Optymalizowanie i zmniejszanie kosztów
Jeśli nie znasz zasad zarządzania kosztami, przeczytaj temat [Jak zoptymalizować inwestycję w chmurę za pomocą usługi Azure Cost Management](../cost-management/cost-mgt-best-practices.md).

W witrynie Azure Portal możesz także zoptymalizować i zmniejszyć koszty związane z platformą Azure za pomocą automatycznego zamykania maszyn wirtualnych i rekomendacji usługi Advisor.

### <a name="consider-cost-cutting-features-like-auto-shutdown-for-vms"></a>Rozważanie funkcji obcinania kosztów, takich jak automatyczne zamykanie maszyn wirtualnych

W zależności od scenariusza można skonfigurować automatyczne zamykanie maszyn wirtualnych w witrynie Azure Portal. Aby uzyskać więcej informacji, zobacz [Automatyczne zamykanie maszyn wirtualnych przy użyciu usługi Azure Resource Manager](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/).

![Zrzut ekranu przedstawiający opcję automatycznego zamykania w portalu](./media/billing-getting-started/auto-shutdown.png)

Automatyczne zamykanie to nie to samo, co wyłączanie maszyny wirtualnej przy użyciu opcji zasilania. Automatyczne zamykanie powoduje zatrzymanie i cofnięcie przydziału maszyn wirtualnych w celu uniknięcia dodatkowych opłat za użycie. Aby uzyskać więcej informacji, zobacz często zadawane pytania dotyczące cen dla [maszyn wirtualnych z systemem Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) i [maszyn wirtualnych z systemem Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) o stanach maszyn wirtualnych.

Aby poznać więcej funkcji obcinania kosztów dla środowisk deweloperskich i testowych, zobacz usługę [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab/).

### <a name="turn-on-and-review-azure-advisor-recommendations"></a>Włączanie i przeglądanie rekomendacji usługi Azure Advisor

Usługa [Azure Advisor](../advisor/advisor-overview.md) pomaga obniżyć koszty, identyfikując zasoby o niskim użyciu. Odwiedź stronę usługi Advisor w witrynie Azure Portal:

![Zrzut ekranu przedstawiający przycisk Azure Advisor w witrynie Azure Portal](./media/billing-getting-started/advisor-button.png)

Rekomendacje z możliwością wykonywania akcji możesz uzyskać na karcie **Koszt** na pulpicie nawigacyjnym usługi Advisor:

![Zrzut ekranu przedstawiający przykład rekomendacji dotyczącej kosztów usługi Advisor](./media/billing-getting-started/advisor-action.png)

Zapoznaj się z samouczkiem [Optymalizowanie kosztów na podstawie rekomendacji](../cost-management/tutorial-acm-opt-recommendations.md), aby dowiedzieć się, jak korzystać z rekomendacji dotyczących oszczędzania kosztów usługi Advisor.

## <a name="review-costs-against-your-latest-invoice"></a>Porównywanie kosztów z ostatnią fakturą

Na koniec cyklu rozliczeniowego dostępna jest najnowsza faktura. Możesz również[pobrać faktury i pliki ze szczegółami użycia](billing-download-azure-invoice-daily-usage-date.md), aby upewnić się, że opłaty zostały naliczone poprawnie. Aby uzyskać więcej informacji na temat porównywania dziennego użycia z fakturą, zobacz [Informacje o rachunku za korzystanie z platformy Microsoft Azure](billing-understand-your-bill.md).

### <a name="billing-api"></a>Interfejs API rozliczeń

Przy użyciu interfejsu API rozliczeń platformy Azure możesz programowo pobierać dane dotyczące użycia. Aby pobrać rozliczane użycie, użyj interfejsu API usługi RateCard wraz z interfejsem API użycia. Aby uzyskać więcej informacji, zobacz [Uzyskiwanie szczegółowych informacji o użyciu zasobów platformy Microsoft Azure](billing-usage-rate-card-overview.md).

## <a name="other-offers"></a> Dodatkowe zasoby i przypadki specjalne

### <a name="ea-csp-and-sponsorship-customers"></a>Klienci z umową Enterprise Agreement, umową od dostawcy CSP i dostępem sponsorowanym
Skontaktuj się ze swoim menedżerem konta lub partnerem platformy Azure, aby rozpocząć.

| Oferta | Zasoby |
|-------------------------------|-----------------------------------------------------------------------------------|
| Umowa Enterprise Agreement (EA) | [Witryna EA Portal](https://ea.azure.com/), [dokumenty pomocy](https://ea.azure.com/helpdocs) i [raport usługi Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-enterprise/) |
| Dostawca rozwiązań w chmurze (CSP) | Porozmawiaj ze swoim dostawcą |
| Dostęp sponsorowany Azure | [Portal dostępu sponsorowanego](https://www.microsoftazuresponsorships.com/) |

Jeśli zarządzasz działem IT w dużej organizacji, zalecamy przeczytanie dokumentu [Szkielet platformy Azure dla przedsiębiorstw](/azure/architecture/cloud-adoption-guide/subscription-governance) i [oficjalnego dokumentu dla działu IT przedsiębiorstw](https://download.microsoft.com/download/F/F/F/FFF60E6C-DBA1-4214-BEFD-3130C340B138/Azure_Onboarding_Guide_for_IT_Organizations_EN_US.pdf) (plik PDF do pobrania, tylko w języku angielskim).

#### <a name="EA"></a> Widoki kosztów dla umowy Enterprise Agreement w witrynie Azure Portal

Widoki kosztów dla przedsiębiorstw są obecnie w publicznej wersji zapoznawczej. Uwagi:

- Koszty subskrypcji są oparte na użyciu i nie obejmują kwot przedpłat, nadwyżek, uwzględnionych ilości, korekt i podatków. Rzeczywiste opłaty są obliczane na poziomie rejestracji.
- Kwoty podane w witrynie Azure Portal mogą różnić się od tego, co jest widoczne w witrynie Enterprise Portal. Aktualizacje wprowadzone w witrynie Enterprise Portal mogą być widoczne w witrynie Azure Portal po kilku minutach.
- Jeśli nie widzisz kosztów, może to być spowodowane jedną z następujących przyczyn:
    - Nie masz uprawnień na poziomie subskrypcji. Aby wyświetlić widoki kosztów przedsiębiorstwa, musisz być czytelnikiem rozliczeń, czytelnikiem, współautorem lub właścicielem na poziomie subskrypcji.
    - Jesteś właścicielem konta, ale administrator rejestracji wyłączył ustawienie „wyświetlania opłat przez właściciela konta”.  Skontaktuj się z administratorem rejestracji, aby uzyskać dostęp do kosztów.
    - Jesteś administratorem działu, ale administrator rejestracji wyłączył ustawienie **wyświetlania opłat przez administratora działu**.  Skontaktuj się z administratorem rejestracji, aby uzyskać dostęp.
    - Platformę Azure zakupiono za pośrednictwem partnera handlowego, który nie opublikował informacji o cenniku.  
- Po zaktualizowaniu ustawień związanych z dostępem do kosztów w witrynie Enterprise Portal zmiany są widoczne w witrynie Azure Portal z kilkuminutowym opóźnieniem.
- Limit wydatków i wskazówki dotyczące faktur nie dotyczą subskrypcji umowy EA.

### <a name="check-your-subscription-and-access"></a>Sprawdzanie subskrypcji i dostępu

Aby wyświetlić koszty, musisz mieć [dostęp do informacji o rozliczeniach na poziomie subskrypcji](billing-manage-access.md). Tylko administrator konta ma dostęp do [Centrum konta](https://account.azure.com/Subscriptions) i może zmienić informacje dotyczące rozliczeń oraz zarządzać subskrypcjami. Administrator konta to osoba, która przeprowadziła proces rejestracji. Aby uzyskać więcej informacji, zobacz [Dodawanie i zmienianie ról administratora platformy Azure, które zarządzają subskrypcją lub usługami](billing-add-change-azure-subscription-administrator.md).

Aby sprawdzić, czy jesteś administratorem konta, przejdź do strony [Subskrypcje w witrynie Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Wyświetl listę subskrypcji i znajdź pozycję **Moja rola**. Jeśli rola to *Administrator konta*, masz pełne uprawnienia. Jeśli Twoja rola jest inna, na przykład *Właściciel*, nie masz pełnych uprawnień.

![Zrzut ekranu przedstawiający rolę w widoku subskrypcji w witrynie Azure Portal](./media/billing-getting-started/sub-blade-view.PNG)

Aby zarządzać subskrypcjami i zmienić informacje dotyczące rozliczeń, [znajdź administratora konta](billing-subscription-transfer.md#whoisaa). Poproś administratora konta o wykonanie zadań lub [przeniesienie subskrypcji na Ciebie](billing-subscription-transfer.md).

Jeśli administrator konta nie pracuje już w Twojej organizacji i chcesz zarządzać rozliczeniami, [skontaktuj się z nami](https://go.microsoft.com/fwlink/?linkid=2083458).


### <a name="request-a-service-level-agreement-credit-for-a-service-incident"></a>Żądanie środków umowy dotyczącej poziomu usług dla zdarzenia związanego z usługą

W umowie dotyczącej poziomu usług (SLA) opisano zobowiązania firmy Microsoft dotyczące czasu dostępności i połączeń. Zdarzenie związane z usługą jest zgłaszane, gdy usługi platformy Azure napotykają problem, który ma wpływ na dostępność i połączenia, co często jest określane mianem *awarii*. Jeśli nie uzyskamy i nie utrzymamy poziomów usług dla każdej usługi zgodnie z opisem w umowie SLA, możesz kwalifikować się do otrzymania środków na część miesięcznych opłat za korzystanie z usług.

Aby zażądać środków:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/). Jeśli masz wiele kont, upewnij się, że korzystasz z tego, na które miał wpływ przestój na platformie Azure. 
2. Utwórz nowy wniosek o pomoc techniczną.
3. W kategorii **Typ problemu** wybierz pozycję **Rozliczenia**.
4. W obszarze **Typ problemu** wybierz pozycję **Żądanie zwrotu pieniędzy**.
5. Dodaj szczegółowe informacje, aby określić, że prosisz o środki z umowy SLA, podaj datę/godzinę/strefę czasową, a także usługi, których dotyczy problem (maszyny wirtualne, witryny internetowe itp.).
6. Sprawdź informacje kontaktowe i wybierz pozycję **Utwórz**, aby przesłać żądanie.

Progi umowy SLA różnią się w zależności od usługi. Na przykład warstwa Internetowa programu SQL jest objęta umową SLA na poziomie 99,9%, maszyny wirtualne mają umowę SLA na poziomie 99,95%, a w przypadku warstwy Standardowa programu SQL jest dostępna umowa SLA na poziomie 99,99%.

W przypadku niektórych usług obowiązują wymagania wstępne dotyczące umowy SLA. Na przykład maszyny wirtualne muszą mieć wdrożone co najmniej dwa wystąpienia w tym samym zestawie dostępności.

Aby uzyskać więcej informacji, zobacz [Umowy dotyczące poziomu usług](https://azure.microsoft.com/support/legal/sla/) oraz dokumentację [podsumowania umów SLA dla usług platformy Azure](https://azure.microsoft.com/support/legal/sla/summary/).

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o korzystaniu z [limitów wydatków](billing-spending-limit.md), aby zapobiec nadmiernym kosztom.
- Zacznij [analizować swoje koszty związane z platformą Azure](../cost-management/quick-acm-cost-analysis.md).
