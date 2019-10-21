---
title: Zapobieganie nieoczekiwanym kosztom i zarządzanie rozliczeniami na platformie Azure
description: Dowiedz się, jak unikać nieoczekiwanych opłat na rachunku za korzystanie z platformy Azure. Używaj funkcji śledzenia kosztów i zarządzania dla konta platformy Azure.
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
ms.openlocfilehash: 6f7a29adfbd145be11f9f6c91e8e66dd229fed62
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72375643"
---
# <a name="prevent-unexpected-charges-with-azure-billing-and-cost-management"></a>Zapobieganie nieoczekiwanym opłatom w rozliczeniach platformy Azure i zarządzanie kosztami

Kiedy rejestrujesz się na platformie Azure, możesz zrobić kilka rzeczy, aby lepiej zrozumieć swoje wydatki:

- Poznaj szacowane koszty przed dodaniem usług przy użyciu [kalkulatora cen](https://azure.microsoft.com/pricing/calculator/), arkusza cen platformy Azure lub podczas dodawania usług w witrynie Azure Portal.
- Monitoruj koszty za pomocą [budżetów](../cost-management/tutorial-acm-create-budgets.md), [alertów](../cost-management/cost-mgt-alerts-monitor-usage-spending.md) i [analizy kosztów](../cost-management/quick-acm-cost-analysis.md).
- Sprawdź opłaty na fakturze, porównując je z [plikami szczegółowego użycia](billing-download-azure-invoice-daily-usage-date.md).
- Integruj dane dotyczące rozliczeń i kosztów z własnym systemem raportowania przy użyciu interfejsów API [rozliczeń](https://docs.microsoft.com/rest/api/billing/) i [użycia](https://docs.microsoft.com/rest/api/consumption/).
- Korzystaj z dodatkowych zasobów i narzędzi dla klientów z umową Enterprise (EA), należących do programu Cloud Solution Provider (CSP) i korzystających z Dostępu sponsorowanego Azure.
- [Przez 12 miesięcy korzystaj bezpłatnie z niektórych najpopularniejszych usług platformy Azure](billing-create-free-services-included-free-account.md) dostępnych na [bezpłatnym koncie platformy Azure](https://azure.microsoft.com/free/). Oprócz zaleceń wymienionych poniżej, zobacz [Unikanie naliczania opłat za bezpłatne konto](billing-avoid-charges-free-account.md).

## <a name="get-estimated-costs-before-adding-azure-services"></a>Uzyskiwanie szacowanych kosztów przed dodaniem usług platformy Azure

Użyj jednego z następujących narzędzi, aby oszacować koszt korzystania z usługi platformy Azure:
- Kalkulator cen platformy Azure
- Arkusz cen platformy Azure
- Portal Azure

Obrazy w poniższych sekcjach pokazują przykładowe ceny w dolarach amerykańskich.

### <a name="estimate-cost-online-using-the-pricing-calculator"></a>Szacowanie kosztów online za pomocą kalkulatora cen

Skorzystaj z [kalkulatora cen](https://azure.microsoft.com/pricing/calculator/), aby uzyskać szacowany miesięczny koszt usługi, którą chcesz dodać. Możesz zmienić walutę, aby uzyskać oszacowanie w walucie lokalnej.

![Zrzut ekranu przedstawiający menu kalkulatora cen](./media/billing-getting-started/pricing-calc.png)

Szacowany koszt możesz sprawdzić dla każdej usługi platformy Azure oferowanej przez firmę Microsoft. Przykładowo na poniższym zrzucie ekranu widać, że szacowany koszt maszyny wirtualnej A1 z systemem Windows to 66,96 USD miesięcznie w godzinach obliczeniowych, jeśli będzie działać przez cały czas:

![Zrzut ekranu kalkulatora cen z szacowanym miesięcznym kosztem maszyny wirtualnej A1 z systemem Windows](./media/billing-getting-started/pricing-calcvm.png)

Aby uzyskać więcej informacji na temat cen, zobacz [Cennik — często zadawane pytania](https://azure.microsoft.com/pricing/faq/). Jeśli chcesz skontaktować się ze sprzedawcą platformy Azure, zadzwoń na numer telefonu widoczny w górnej części strony często zadawanych pytań.

### <a name="view-and-download-azure-price-sheet"></a>Wyświetlanie i pobieranie arkusza cen platformy Azure

Jeśli masz dostęp do platformy Azure za pomocą umowy Enterprise (EA) lub umowy klienta firmy Microsoft (MCA), możesz wyświetlić i pobrać arkusz cen dla swojego konta platformy Azure. Arkusz cen to plik programu Excel, który zawiera ceny dla wszystkich usług platformy Azure. Aby uzyskać więcej informacji, zobacz [Wyświetlanie i pobieranie cennika platformy Azure](billing-ea-pricing.md).

### <a name="review-estimated-costs-in-the-azure-portal"></a>Przeglądanie szacowanych kosztów w witrynie Azure Portal

Szacowany koszt miesięczny możesz wyświetlić podczas dodawania usługi w witrynie Azure Portal. Na przykład po wybraniu rozmiaru maszyny wirtualnej z systemem Windows zobaczysz szacowany miesięczny koszt dla godzin obliczeniowych:

![Przykład: maszyna wirtualna A1 z systemem Windows z widocznym szacowanym kosztem miesięcznym](./media/billing-getting-started/vm-size-cost.png)

## <a name="monitor-costs-when-using-azure-services"></a>Monitorowanie kosztów podczas korzystania z usług platformy Azure
Koszty można monitorować przy użyciu następujących narzędzi:

- Ustawianie alertów dotyczących budżetu i kosztów
- Analiza kosztów

### <a name="track-costs-with-budgets-and-cost-alerts"></a>Śledzenie kosztów za pomocą alertów dotyczących budżetów i kosztów

W celu zarządzania kosztami można utworzyć [budżety](../cost-management/tutorial-acm-create-budgets.md) oraz [alerty](../cost-management/cost-mgt-alerts-monitor-usage-spending.md), które automatycznie powiadamiają Ciebie oraz Twoich interesariuszy o anomaliach w wydatkach i nadmiernych wydatkach. 

### <a name="costs"></a> Poznawanie i analizowanie kosztów za pomocą analizy kosztów

Po uruchomieniu usług platformy Azure regularnie sprawdzaj koszty, aby śledzić wydatki na platformę Azure. Analizy kosztów możesz używać, aby zrozumieć, skąd biorą się koszty związane z użyciem platformy Azure. 

1. Przejdź na stronę [Zarządzanie kosztami i rozliczenia w witrynie Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade).

2. Kliknij pozycję **Analiza kosztów** po lewej stronie ekranu, aby zobaczyć bieżące koszty podzielone według różnych kryteriów, takich jak usługa, lokalizacja i subskrypcja. Po dodaniu usługi lub dokonaniu zakupu poczekaj 24 godziny na wyświetlenie danych. Domyślnie analiza kosztów pokazuje koszt dla zakresu, w którym się znajdujesz. Na przykład na poniższym zrzucie ekranu jest wyświetlany koszt dla konta rozliczeniowego Contoso. Użyj kapsułki Zakres, aby przełączyć się na inny zakres w analizie kosztów. Aby uzyskać więcej informacji na temat zakresów, zobacz [Omówienie zakresów i praca z nimi](../cost-management/understand-work-scopes.md#scopes)

    ![Zrzut ekranu przedstawiający widok analizy kosztów w witrynie Azure Portal](./media/billing-getting-started/cost-analysis.png)

4. Filtrować można według różnych właściwości, takich jak tagi, typ zasobu i przedział czasu. Kliknij pozycję **Dodaj filtr**, aby dodać filtr dla właściwości i wybrać wartości do odfiltrowania. Wybierz pozycję **Eksportuj**, aby wyeksportować widok do pliku z wartościami rozdzielanymi przecinkami (csv).

5. Ponadto możesz klikać etykiety wykresu, aby zobaczyć dzienną historię wydatków dla konkretnych etykiet. Na przykład: Na poniższym zrzucie ekranu klikanie maszyn wirtualnych powoduje wyświetlenie dziennego kosztu działania tych maszyn.

    ![Zrzut ekranu przedstawiający widok historii wydatków w witrynie Azure Portal](./media/billing-getting-started/costhistory.png)

## <a name="optimize-and-reduce-costs"></a>Optymalizowanie i zmniejszanie kosztów
Jeśli nie znasz zasad zarządzania kosztami, przeczytaj temat [Jak zoptymalizować inwestycję w chmurę za pomocą usługi Azure Cost Management](../cost-management/cost-mgt-best-practices.md).

W witrynie Azure Portal możesz także zoptymalizować i zmniejszyć koszty związane z platformą Azure za pomocą automatycznego zamykania maszyn wirtualnych i rekomendacji usługi Advisor.

### <a name="consider-cost-cutting-features-like-auto-shutdown-for-vms"></a>Rozważanie funkcji obcinania kosztów, takich jak automatyczne zamykanie maszyn wirtualnych

W zależności od scenariusza można skonfigurować automatyczne zamykanie maszyn wirtualnych w witrynie Azure Portal. Aby uzyskać więcej informacji, zobacz [Automatyczne zamykanie maszyn wirtualnych przy użyciu usługi Azure Resource Manager](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/).

![Zrzut ekranu przedstawiający opcję automatycznego zamykania w portalu](./media/billing-getting-started/auto-shutdown.png)

Automatyczne zamykanie to nie to samo, co wyłączanie maszyny wirtualnej przy użyciu opcji zasilania. Automatyczne zamykanie powoduje zatrzymanie i cofnięcie przydziału maszyn wirtualnych w celu uniknięcia dodatkowych opłat za użycie. Aby uzyskać więcej informacji, zobacz często zadawane pytania dotyczące cen dla [maszyn wirtualnych z systemem Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) i [maszyn wirtualnych z systemem Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) o stanach maszyn wirtualnych.

Aby poznać więcej funkcji obcinania kosztów dla środowisk deweloperskich i testowych, zobacz usługę [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab/).

### <a name="turn-on-and-review-azure-advisor-recommendations"></a>Włączanie i przeglądanie rekomendacji usługi Azure Advisor

Usługa [Azure Advisor](../advisor/advisor-overview.md) pomaga obniżyć koszty, identyfikując zasoby o niskim użyciu. Wyszukaj usługę **Advisor** w witrynie Azure Portal:

![Zrzut ekranu przedstawiający przycisk Azure Advisor w witrynie Azure Portal](./media/billing-getting-started/advisor-button.png)

Wybierz pozycję **Koszty** po lewej stronie. Zobaczysz praktyczne zalecenia na karcie **Koszty**:

![Zrzut ekranu przedstawiający przykład rekomendacji dotyczącej kosztów usługi Advisor](./media/billing-getting-started/advisor-action.png)

Zapoznaj się z samouczkiem [Optymalizowanie kosztów na podstawie rekomendacji](../cost-management/tutorial-acm-opt-recommendations.md), aby dowiedzieć się, jak korzystać z rekomendacji dotyczących oszczędzania kosztów usługi Advisor.

## <a name="review-charges-against-your-latest-invoice"></a>Porównywanie opłat z ostatnią fakturą

Faktura jest dostępna na koniec cyklu rozliczeniowego. Możesz [pobrać faktury i pliki ze szczegółami użycia](billing-download-azure-invoice-daily-usage-date.md) oraz porównać je, aby upewnić się, że opłaty zostały naliczone poprawnie. Aby uzyskać więcej informacji na temat porównywania dziennego użycia z fakturą, zobacz [Informacje o rachunku za korzystanie z platformy Microsoft Azure](billing-understand-your-bill.md).

Jeśli korzystasz z platformy Azure za pośrednictwem umowy klienta firmy Microsoft (MCA), możesz również [porównać fakturę z transakcjami](billing-mca-understand-your-bill.md#view-transactions-for-an-invoice-in-the-azure-portal), aby zrozumieć opłaty na fakturze.

## <a name="integrate-with-billing-and-consumption-apis"></a>Integrowanie z interfejsami API rozliczeń i użycia

Aby programowo uzyskać dane dotyczące rozliczeń i kosztów, użyj interfejsów API [rozliczeń](https://docs.microsoft.com/rest/api/billing/) i [użycia](https://docs.microsoft.com/rest/api/consumption/) na platformie Azure. Aby pobrać rozliczane użycie, użyj interfejsu API usługi RateCard wraz z interfejsem API użycia. Aby uzyskać więcej informacji, zobacz [Uzyskiwanie szczegółowych informacji o użyciu zasobów platformy Microsoft Azure](billing-usage-rate-card-overview.md).

## <a name="other-offers"></a> Dodatkowe zasoby i przypadki specjalne

### <a name="ea-csp-and-sponsorship-customers"></a>Klienci z umową Enterprise Agreement, umową od dostawcy CSP i dostępem sponsorowanym
Skontaktuj się ze swoim menedżerem konta lub partnerem platformy Azure, aby rozpocząć.

| Oferta | Zasoby |
|-------------------------------|-----------------------------------------------------------------------------------|
| Umowa Enterprise Agreement (EA) | [Witryna EA Portal](https://ea.azure.com/), [dokumenty pomocy](https://ea.azure.com/helpdocs) i [raport usługi Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-enterprise/) |
| Dostawca rozwiązań w chmurze (CSP) | Porozmawiaj ze swoim dostawcą |
| Dostęp sponsorowany Azure | [Portal dostępu sponsorowanego](https://www.microsoftazuresponsorships.com/) |

Jeśli zarządzasz działem IT w dużej organizacji, zalecamy przeczytanie dokumentu [Szkielet platformy Azure dla przedsiębiorstw](/azure/architecture/cloud-adoption-guide/subscription-governance) i [oficjalnego dokumentu dla działu IT przedsiębiorstw](https://download.microsoft.com/download/F/F/F/FFF60E6C-DBA1-4214-BEFD-3130C340B138/Azure_Onboarding_Guide_for_IT_Organizations_EN_US.pdf) (plik PDF do pobrania, tylko w języku angielskim).

### <a name="EA"></a> Widoki kosztów dla umowy Enterprise Agreement w witrynie Azure Portal

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

Aby wyświetlić koszty, musisz mieć dostęp do informacji o kosztach lub rozliczeniach na poziomie konta lub subskrypcji. Dostęp jest różny w zależności od typu konta rozliczeniowego. Aby dowiedzieć się więcej na temat kont rozliczeniowych i sprawdzić typ takiego konta, zobacz [Wyświetlanie kont rozliczeniowych w witrynie Azure Portal](billing-view-all-accounts.md).

Jeśli masz dostęp do platformy Azure za pomocą konta rozliczeniowego w programie Microsoft Online Service Program (MOSP), zobacz [Zarządzanie dostępem do informacji rozliczeniowych dla platformy Azure](billing-manage-access.md).

Jeśli masz dostęp do platformy Azure za pomocą konta rozliczeniowego umowy Enterprise Agreement (EA), zobacz [Omówienie ról administracyjnych dla umowy Azure Enterprise Agreement na platformie Azure](billing-understand-ea-roles.md).

Jeśli masz dostęp do platformy Azure za pomocą konta rozliczeniowego umowy klienta firmy Microsoft (MCA), zobacz [Omówienie ról administracyjnych dla umowy klienta firmy Microsoft na platformie Azure](billing-understand-mca-roles.md).

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
