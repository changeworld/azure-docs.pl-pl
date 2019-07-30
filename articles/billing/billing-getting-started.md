---
title: Unikaj nieoczekiwanych kosztów i Zarządzaj rozliczeniami na platformie Azure
description: Dowiedz się, jak uniknąć nieoczekiwanych opłat na rachunku na korzystanie z platformy Azure. Korzystaj z funkcji śledzenia kosztów i zarządzania dla subskrypcji platformy Azure.
author: bandersmsft
manager: amberb
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: b64e84c3fff27675029ff35f27972a4aca014ec3
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68612127"
---
# <a name="prevent-unexpected-charges-with-azure-billing-and-cost-management"></a>Zapobieganie nieoczekiwanym opłatom za korzystanie z usługi Azure rozliczeń i kosztów

Po zarejestrowaniu się na platformie Azure istnieje kilka rzeczy, które można zrobić, aby lepiej zrozumieć swoje wydatki:

- [Kalkulator cen](https://azure.microsoft.com/pricing/calculator/) może zapewnić oszacowanie kosztów przed utworzeniem zasobu platformy Azure. 

- [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) zapewnia bieżący podział kosztów i prognozę dla subskrypcji. 

- Jeśli chcesz grupować i zrozumieć koszty dla różnych projektów lub zespołów, zapoznaj się z [tagowaniem zasobów](../azure-resource-manager/resource-group-using-tags.md). Jeśli Twoja organizacja ma system raportowania, którego wolisz użyć, zapoznaj się z [interfejsami API](billing-usage-rate-card-overview.md)rozliczeń.

- Jeśli Twoja subskrypcja została utworzona na podstawie Umowa Enterprise (EA), możesz wyświetlić swoje koszty w Azure Portal. Jeśli Twoja subskrypcja odbywa się za pomocą dostawcy rozwiązań w chmurze (CSP) lub Dostęp sponsorowany Azure, niektóre z następujących funkcji mogą nie mieć zastosowania. Aby uzyskać więcej informacji, zobacz [dodatkowe zasoby dla EA, CSP i sponsorowania](#other-offers).

- Jeśli Twoja subskrypcja jest ofertą bezpłatnej wersji próbnej, [Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), Azure in Open (AIO) lub BizSpark, subskrypcja zostanie automatycznie wyłączona, gdy zostaną użyte wszystkie kredyty. Dowiedz się więcej na temat [limitów wydatków](#spending-limit) , aby uniknąć nieoczekiwanego wyłączenia subskrypcji.

- Jeśli zarejestrowano się w celu korzystania z [bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/), możesz [bezpłatnie korzystać z najpopularniejszych usług platformy Azure przez 12 miesięcy](billing-create-free-services-included-free-account.md). Wraz z zaleceniami wymienionymi poniżej Zobacz unikanie korzystania z [bezpłatnego konta](billing-avoid-charges-free-account.md).

## <a name="get-estimated-costs-before-adding-azure-services"></a>Uzyskaj szacowane koszty przed dodaniem usług platformy Azure

Poniżej znajdują się dodatkowe informacje na temat szacowania kosztów przy użyciu następujących narzędzi:
- Kalkulator cen platformy Azure
- Azure Portal
- Limit wydatków

W poniższych sekcjach znajdują się przykładowe ceny w dolarach amerykańskich.

### <a name="estimate-cost-online-using-the-pricing-calculator"></a>Oszacuj koszt online za pomocą kalkulatora cen

Zapoznaj się z [kalkulatorem cen](https://azure.microsoft.com/pricing/calculator/) , aby uzyskać szacowany miesięczny koszt usługi, która Cię interesuje. Do uzyskania szacowanego kosztu możesz dodać dowolny zasób platformy Azure dla pierwszej firmy. W kalkulatorze cen można zmienić typ waluty.

![Zrzut ekranu przedstawiający menu Kalkulator cen](./media/billing-getting-started/pricing-calc.png)

Na przykład w kalkulatorze cen szacuje się, że maszyna wirtualna z systemem Windows (VM) ma kosztować określoną kwotę/miesiąc w godzinach obliczeniowych, jeśli zostanie ona uruchomiona cały czas:

![Zrzut ekranu kalkulatora cen pokazujący szacowany koszt maszyny wirtualnej w systemie Windows na miesiąc](./media/billing-getting-started/pricing-calcvm.png)

Aby uzyskać więcej informacji na temat cen, zobacz [często zadawane pytania dotyczące cen](https://azure.microsoft.com/pricing/faq/). Jeśli chcesz skontaktować się z sprzedawcą platformy Azure, wywołaj numer telefonu widoczny w górnej części strony często zadawane pytania.

### <a name="review-estimated-costs-in-the-azure-portal"></a>Przejrzyj szacowane koszty w Azure Portal

Zwykle po dodaniu usługi w Azure Portal istnieje widok pokazujący szacowany koszt miesięcznie w walucie rozliczeniowej. Na przykład po wybraniu rozmiaru maszyny wirtualnej z systemem Windows zobaczysz szacowany miesięczny koszt dla godzin obliczeniowych:

![Przykład: w przypadku maszyny wirtualnej z systemem Windows w a1 zostanie wyświetlony szacowany koszt miesięcznie](./media/billing-getting-started/vm-size-cost.png)

### <a name="spending-limit"></a>Sprawdź, czy masz limit wydatków

Jeśli masz subskrypcję, która korzysta z kredytów, limit wydatków jest domyślnie włączony. W ten sposób, gdy spędzasz wszystkie kredyty, karta kredytowa nie zostanie obciążona. Zapoznaj się z [pełną listą ofert platformy Azure i dostępnością limitu wydatków](https://azure.microsoft.com/support/legal/offer-details/).

Jednak po osiągnięciu limitu wydatków usługi zostaną wyłączone. Oznacza to, że maszyny wirtualne są cofane. Aby uniknąć przestoju usługi, należy wyłączyć limit wydatków. Wszelkie nadwyżki są naliczone na kartę kredytową w pliku.

Aby sprawdzić, czy masz limit wydatków, przejdź do [widoku subskrypcje w centrum konta](https://account.windowsazure.com/Subscriptions). Transparent pojawia się, jeśli limit wydatków jest włączony, podobny do następującego:

![Zrzut ekranu pokazujący ostrzeżenie o limicie wydatków w centrum konta](./media/billing-getting-started/spending-limit-banner.png)

Kliknij transparent i postępuj zgodnie z monitami, aby usunąć limit wydatków. Jeśli podczas rejestracji nie wprowadzono informacji o karcie kredytowej, musisz wprowadzić ją, aby usunąć limit wydatków. Aby uzyskać więcej informacji, zobacz [limit wydatków platformy Azure — jak to działa i jak go włączyć lub usunąć](https://azure.microsoft.com/pricing/spending-limits/).

## <a name="use-budgets-and-cost-alerts"></a>Korzystanie z budżetów i alertów o kosztach

Można utworzyć [budżety](../cost-management/tutorial-acm-create-budgets.md) służące do zarządzania kosztami i tworzenia [alertów](../cost-management/cost-mgt-alerts-monitor-usage-spending.md) , które automatycznie powiadamiają uczestników wydatków o wykorzystaniu anomalii i poświęcają ryzyko. Alerty są oparte na wydatkach w porównaniu z budżetem i progami kosztów.

## <a name="monitor-costs-when-using-azure-services"></a>Monitoruj koszty w przypadku korzystania z usług platformy Azure
Koszty można monitorować przy użyciu następujących narzędzi:

- `Tags`
- Podział kosztów i szybkość spalania
- Analiza kosztów

### <a name="tags"></a>Dodawanie tagów do zasobów w celu grupowania danych dotyczących rozliczeń

Za pomocą tagów można grupować dane rozliczeń dla obsługiwanych usług. Jeśli na przykład uruchomisz kilka maszyn wirtualnych dla różnych zespołów, możesz użyć tagów do kategoryzacji kosztów według centrum kosztów (na przykład: HR, Marketing, finanse itp.) lub środowisko (na przykład: produkcja, przedprodukcyjne, testowe).

![Zrzut ekranu pokazujący Konfigurowanie tagów w portalu](./media/billing-getting-started/tags.png)

Tagi są wyświetlane w różnych widokach raportów o kosztach. Na przykład są one widoczne w [widoku analizy kosztów](#costs) od razu i w szczegółowym pliku CSV użycia po pierwszym okresie rozliczeniowym.

Aby uzyskać więcej informacji, zobacz [Używanie tagów do organizowania zasobów platformy Azure](../azure-resource-manager/resource-group-using-tags.md).

### <a name="costs"></a>Monitoruj podział kosztów i szybkość spalania

Po uruchomieniu usług platformy Azure regularnie sprawdzaj opłaty. Bieżące wydatki i szybkość spalania można zobaczyć w Azure Portal.

1. Odwiedź [subskrypcje w Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) i wybierz subskrypcję.

2. Jeśli jest ona obsługiwana dla Twojej subskrypcji, zobaczysz podział kosztów i szybkość spalania.

    ![Zrzut ekranu przedstawiający stopień spalania i podział w Azure Portal](./media/billing-getting-started/burn-rate.PNG)

3. Kliknij pozycję [Analiza kosztów](../cost-management/quick-acm-cost-analysis.md) na liście po lewej stronie, aby wyświetlić podział kosztów według zasobu. Po dodaniu usługi odczekaj 24 godziny, aż dane mają być wyświetlane.

    ![Zrzut ekranu przedstawiający widok koszt analizy w Azure Portal](./media/billing-getting-started/cost-analysis.png)

4. Można filtrować według różnych właściwości, takich jak [Tagi](#tags), typ zasobu, Grupa zasobów i TimeSpan. Kliknij przycisk **Zastosuj** , aby potwierdzić filtry i **pobrać** , jeśli chcesz wyeksportować widok do pliku z wartościami rozdzielanymi przecinkami (CSV).

5. Ponadto możesz kliknąć zasób, aby zobaczyć dzienną historię wydatków oraz ilość zasobów każdego dnia.

    ![Zrzut ekranu przedstawiający widok historia wydatków w Azure Portal](./media/billing-getting-started/costhistory.png)

Porównaj koszty, które zobaczysz wraz z oszacowaniami, które były widoczne po wybraniu usług. Jeśli koszty znacznie różnią się od oszacowania, Sprawdź plan cenowy, który został wybrany dla Twoich zasobów.

## <a name="optimize-and-reduce-costs"></a>Optymalizowanie i obniżanie kosztów
Jeśli nie znasz zasad zarządzania kosztami, przeczytaj artykuł [jak zoptymalizować inwestycje w chmurze za pomocą Azure Cost Management](../cost-management/cost-mgt-best-practices.md).

W Azure Portal można także zoptymalizować i zmniejszyć koszty związane z platformą Azure dzięki automatycznemu wyłączaniu maszyn wirtualnych i zaleceń usługi Advisor.

### <a name="consider-cost-cutting-features-like-auto-shutdown-for-vms"></a>Weź pod uwagę funkcje wycinania kosztów, takie jak automatyczne zamykanie maszyn wirtualnych

W zależności od scenariusza można skonfigurować automatyczne zamykanie maszyn wirtualnych w Azure Portal. Aby uzyskać więcej informacji, zobacz [automatyczne zamykanie maszyn wirtualnych przy użyciu Azure Resource Manager](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/).

![Zrzut ekranu opcji automatycznego zamykania w portalu](./media/billing-getting-started/auto-shutdown.png)

Automatyczne zamykanie nie jest takie samo, jak w przypadku zamykania maszyny wirtualnej przy użyciu opcji zasilania. Automatyczne zamykanie powoduje zatrzymanie i cofnięcie przydziału maszyn wirtualnych w celu zatrzymania dodatkowych opłat za użycie. Aby uzyskać więcej informacji, zobacz często zadawane pytania dotyczące cen [maszyn wirtualnych systemu Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) i [maszyn wirtualnych z systemem Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) dotyczących Stanów maszyn wirtualnych.

Aby uzyskać więcej funkcji zwiększania kosztów dla środowisk deweloperskich i testowych, zapoznaj się z [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab/).

### <a name="turn-on-and-review-azure-advisor-recommendations"></a>Włącz i przejrzyj rekomendacje Azure Advisor

[Azure Advisor](../advisor/advisor-overview.md) pomaga obniżyć koszty przez zidentyfikowanie zasobów o niskim zużyciu. Odwiedź witrynę Advisor w Azure Portal:

![Zrzut ekranu przedstawiający przycisk Azure Advisor w Azure Portal](./media/billing-getting-started/advisor-button.png)

Możesz uzyskać zalecenia z możliwością wykonywania akcji na karcie **koszt** na pulpicie nawigacyjnym usługi Advisor:

![Zrzut ekranu przedstawiający przykład rekomendacji dotyczącej kosztów usługi Advisor](./media/billing-getting-started/advisor-action.png)

Zapoznaj się z samouczkiem [Optymalizacja kosztów z zaleceń](../cost-management/tutorial-acm-opt-recommendations.md) , aby zapoznać się z samouczkiem dotyczącym oszczędzania kosztów.

## <a name="review-costs-against-your-latest-invoice"></a>Przejrzyj koszty dotyczące najnowszej faktury

Na koniec cyklu rozliczeniowego dostępna jest najnowsza faktura. Możesz również [pobrać faktury i szczegółowe pliki użycia](billing-download-azure-invoice-daily-usage-date.md) , aby upewnić się, że nastąpiło poprawne naliczanie opłat. Aby uzyskać więcej informacji na temat porównywania dziennego użycia z fakturą, zobacz [Opis rachunku na Microsoft Azure](billing-understand-your-bill.md).

### <a name="billing-api"></a>Interfejs API rozliczeń

Użyj interfejsu API rozliczeń platformy Azure, aby programowo pobrać dane dotyczące użycia. Użyj interfejsu API RateCard i interfejsu API użycia razem, aby uzyskać rozliczane użycie. Aby uzyskać więcej informacji, zobacz [uzyskiwanie wglądu w dane dotyczące użycia zasobów Microsoft Azure](billing-usage-rate-card-overview.md).

## <a name="other-offers"></a>Dodatkowe zasoby i specjalne przypadki

### <a name="ea-csp-and-sponsorship-customers"></a>Klienci z umowami EA, CSP i sponsorowania
Skontaktuj się z menedżerem kont lub partnerem platformy Azure, aby rozpocząć pracę.

| Oferta | Zasoby |
|-------------------------------|-----------------------------------------------------------------------------------|
| Enterprise Agreement (EA) | [Portal EA](https://ea.azure.com/), [Dokumentacja pomocy](https://ea.azure.com/helpdocs)i [raport Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-enterprise/) |
| Cloud Solution Provider (CSP) | Porozmawiaj z dostawcą |
| Dostęp sponsorowany Microsoft Azure | [Portal sponsorowania](https://www.microsoftazuresponsorships.com/) |

Jeśli zarządzasz nim na potrzeby dużej organizacji, zalecamy odczytywanie [szkieletu platformy Azure Enterprise](/azure/architecture/cloud-adoption-guide/subscription-governance) i [oficjalnego dokumentu IT dla przedsiębiorstw](https://download.microsoft.com/download/F/F/F/FFF60E6C-DBA1-4214-BEFD-3130C340B138/Azure_Onboarding_Guide_for_IT_Organizations_EN_US.pdf) (pobieranie plików PDF, tylko w języku angielskim).

#### <a name="EA"></a>Umowa Enterprise widoki kosztów w Azure Portal

Widoki kosztów przedsiębiorstwa są obecnie dostępne w publicznej wersji zapoznawczej. Elementy do uwagi:

- Opłaty za subskrypcję bazują na użyciu i nie obejmują kwot przedpłat, nadwyżki, uwzględnionych wartości, korekt i podatków. Rzeczywiste opłaty są obliczane na poziomie rejestracji.
- Kwoty podane w Azure Portal mogą różnić się od zawartości witryny Enterprise Portal. Aktualizacje w witrynie Enterprise Portal mogą potrwać kilka minut, zanim zmiany zostaną wyświetlone w Azure Portal.
- Jeśli nie widzisz kosztów, może to być spowodowane jedną z następujących przyczyn:
    - Nie masz uprawnień na poziomie subskrypcji. Aby wyświetlić widoki kosztów przedsiębiorstwa, musisz być Czytelnikem, Czytelnikem, współautorem lub właścicielem rozliczeń na poziomie subskrypcji.
    - Jesteś właścicielem konta, a administrator rejestracji wyłączył ustawienie "AO View charges".  Skontaktuj się z administratorem rejestracji, aby uzyskać dostęp do kosztów.
    - Jesteś administratorem działu, a administrator rejestracji wyłączył ustawienie funkcji **da Podgląd** .  Skontaktuj się z administratorem rejestracji, aby uzyskać dostęp.
    - Zakupiono platformę Azure za pośrednictwem partnera kanału, a partner nie zwolnił informacji o cenach.  
- W przypadku aktualizowania ustawień związanych z dostępem do kosztów w witrynie Enterprise Portal istnieje opóźnienie kilku minut, po upływie których zmiany zostaną wyświetlone w Azure Portal.
- Limit wydatków i wskazówki dotyczące faktur nie mają zastosowania do subskrypcji umowy EA.

### <a name="check-your-subscription-and-access"></a>Sprawdź swoją subskrypcję i dostęp

Aby wyświetlić koszty, musisz mieć [dostęp do informacji rozliczeniowych na poziomie subskrypcji](billing-manage-access.md). Tylko administrator konta może uzyskać dostęp do [centrum konta](https://account.azure.com/Subscriptions), zmienić informacje dotyczące rozliczeń i zarządzać subskrypcjami. Administrator konta to osoba, która przeszedł przez proces rejestracji. Aby uzyskać więcej informacji, zobacz [Dodawanie lub zmienianie ról administratora platformy Azure, które zarządzają subskrypcją lub usługami](billing-add-change-azure-subscription-administrator.md).

Aby sprawdzić, czy jesteś administratorem konta, przejdź do pozycji [subskrypcje w Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Wyświetl listę subskrypcji i Znajdź **moją rolę**. Jeśli rolę *administratora konta* jest rola, masz pełne uprawnienia. Jeśli mówi coś innego, takiego jak *właściciel*, nie masz pełnych uprawnień.

![Zrzut ekranu roli w widoku subskrypcje w Azure Portal](./media/billing-getting-started/sub-blade-view.PNG)

Aby zarządzać subskrypcjami i zmienić informacje dotyczące rozliczeń, [Znajdź administratora konta](billing-subscription-transfer.md#whoisaa). Poprosimy administratora konta o ukończenie zadań lub [przeniesienie subskrypcji do Ciebie](billing-subscription-transfer.md).

Jeśli administrator konta nie jest już w Twojej organizacji i musisz zarządzać rozliczeniami, [skontaktuj się z nami](https://go.microsoft.com/fwlink/?linkid=2083458).


### <a name="request-a-service-level-agreement-credit-for-a-service-incident"></a>Żądaj Umowa dotycząca poziomu usług środków dla zdarzenia usługi

W umowie dotyczącej poziomu usług (SLA) opisano zobowiązania firmy Microsoft dotyczące czasu dostępności i połączeń. Zdarzenie usługi jest zgłaszane, gdy usługi platformy Azure napotykają problem, który ma wpływ na czas przestoju lub łączności , często określany jako awaria. Jeśli nie osiągniemy ani nie utrzymujemy poziomów usług dla każdej usługi zgodnie z opisem w umowie SLA, możesz kwalifikować się do otrzymania kredytu na część miesięcznych opłat za korzystanie z usług.

Aby zażądać kredytu:

1. Zaloguj się do [Azure Portal](https://portal.azure.com/). Jeśli masz wiele kont, upewnij się, że korzystasz z nich, na które miało wpływ przestój platformy Azure. 
2. Utwórz nowe żądanie obsługi.
3. W obszarze **typ problemu**wybierz pozycję rozliczenia.
4. W obszarze **typ problemu**wybierz pozycję **żądanie zwrotu**.
5. Dodaj szczegóły, aby określić, że prosisz o środki na korzystanie z umowy SLA, podaj datę/godzinę/strefę czasową, a także usługi, których dotyczy problem (maszyny wirtualne, witryny sieci Web itp.).
6. Sprawdź szczegóły kontaktu i wybierz pozycję **Utwórz** , aby przesłać żądanie.

Progi umowy SLA różnią się w zależności od usługi. Na przykład warstwa sieci Web SQL jest objęta umową SLA 99,9%, maszyny wirtualne mają umowę SLA o 99,95%, a w przypadku warstwy SQL Standard jest dostępna umowa SLA do 99,99%.

W przypadku niektórych usług obowiązują wymagania wstępne dotyczące umowy SLA. Na przykład maszyny wirtualne muszą mieć wdrożone co najmniej dwa wystąpienia w tym samym zestawie dostępności.

Aby uzyskać więcej informacji, zobacz [umowy dotyczące poziomu usług](https://azure.microsoft.com/support/legal/sla/) oraz [Podsumowanie umów SLA dla usług platformy Azure](https://azure.microsoft.com/support/legal/sla/summary/) .

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie pomocy technicznej](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o korzystaniu z [limitów wydatków](billing-spending-limit.md) , aby zapobiec przenoszeniu.
- Rozpocznij [Analizowanie kosztów platformy Azure](../cost-management/quick-acm-cost-analysis.md).
