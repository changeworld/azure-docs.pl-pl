---
title: Uniknąć kosztów nieoczekiwany, Zarządzanie rozliczeniami - Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zapobiec zmianom nieoczekiwany na rachunku platformy Azure. Przy użyciu funkcji Śledzenie kosztów i zarządzania dla subskrypcji Microsoft Azure.
services: ''
documentationcenter: ''
author: tonguyen10
manager: tonguyen
editor: ''
tags: billing
ms.assetid: 482191ac-147e-4eb6-9655-c40c13846672
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2018
ms.author: tonguyen
ms.openlocfilehash: db81baa5df13c8c5ca38e0e1ab2dffda1e31370a
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="prevent-unexpected-charges-with-azure-billing-and-cost-management"></a>Zapobiegaj nieoczekiwany opłat rozliczenia Azure i kosztów zarządzania

Podczas tworzenia konta platformy Azure, istnieje kilka kwestii, które można wykonać, aby lepiej zrozumieć sieci wydatków. [Kalkulator cen](https://azure.microsoft.com/pricing/calculator/) zapewniają oszacowanie kosztów, przed utworzeniem zasobów platformy Azure. [Portalu Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) zapewnia bieżącego podziału kosztów i prognozy dla Twojej subskrypcji. Jeśli chcesz grupy i zrozumieć koszty różnych projektów lub zespołów przyjrzeć się [znakowanie zasobów](../azure-resource-manager/resource-group-using-tags.md). Jeśli Twoja organizacja ma raportowania systemu, w którym można skorzystać, zapoznaj się [rozliczeń interfejsów API](billing-usage-rate-card-overview.md). 

- Jeśli Twoja subskrypcja jest Enterprise Agreement (EA), dostępna jest publicznej wersji zapoznawczej przy przeglądaniu kosztów w portalu Azure. Jeśli Twoja subskrypcja jest za pośrednictwem Cloud Solution Provider (CSP) lub dostęp sponsorowany Azure, następnie niektóre z następujących funkcji mogą nie dotyczyć użytkownika. Zobacz [dodatkowe zasoby dla przedsiębiorstw, dostawca usług Kryptograficznych i dostęp sponsorowany](#other-offers) Aby uzyskać więcej informacji.

- Jeśli Twoja subskrypcja jest bezpłatna wersja próbna [programu Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), Azure Otwórz (AIO) lub BizSpark, subskrypcja jest automatycznie wyłączana, gdy środki zostaną są używane. Dowiedz się więcej o [limitów wydatków](#spending-limit) Aby uniknąć subskrypcją unexpectantly wyłączone.

- Jeśli użytkownik utworzył konto na [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/), [można korzystać z niektórych najbardziej popularnych usług Azure bezpłatnie przez 12 miesięcy](billing-create-free-services-included-free-account.md). Wraz z zaleceniami wymienione poniżej, zobacz [uniknąć pobieranie opłaty łączone zgodnie z bezpłatne konto](billing-avoid-charges-free-account.md).

## <a name="get-estimated-costs-before-adding-azure-services"></a>Pobierz szacowane koszty przed dodaniem usług platformy Azure

### <a name="estimate-cost-online-using-the-pricing-calculator"></a>Szacowanie kosztów online z użyciem Kalkulator cen

Zapoznaj się z [Kalkulator cen](https://azure.microsoft.com/pricing/calculator/) uzyskać szacowany koszt miesięczne interesuje Cię usługi. Możesz dodać pierwszej strony zasobów platformy Azure można pobrać szacowania kosztów.

![Zrzut ekranu przedstawiający menu Kalkulator cen](./media/billing-getting-started/pricing-calc.png)

Na przykład A1 systemu Windows maszyny wirtualnej (VM) szacuje się na koszt $66.96 USD miesięcznie w godziny obliczeń, jeśli usługa będzie działać przez cały czas:

![Zrzut ekranu przedstawiający Kalkulator cen, pokazujący, że maszyna wirtualna A1 Windows szacuje się na koszt $66.96 USD miesięcznie](./media/billing-getting-started/pricing-calcVM.png)

Aby uzyskać więcej informacji o cenach, zobacz ten [— często zadawane pytania](https://azure.microsoft.com/pricing/faq/). Lub jeśli chcesz rozmawiać z Azure sprzedawcy, skontaktuj się z 1-800-867-1389.

### <a name="review-the-estimated-cost-in-the-azure-portal"></a>Przejrzyj szacowany koszt w portalu Azure

Zwykle po dodaniu usługi w portalu Azure istnieje widok pokazujący podobne szacowany koszt miesięcznie. Na przykład jeśli wybrano rozmiar maszyny Wirtualnej systemu Windows, zobaczysz szacowany koszt miesięczne godziny obliczeniowe:

![Przykład: maszyna wirtualna A1 systemu Windows jest szacowany koszt $66.96 USD miesięcznie](./media/billing-getting-started/vm-size-cost.PNG)

### <a name="set-up-billing-alerts"></a>Ustawianie alertów dotyczących rozliczeń

Skonfiguruj alerty dotyczące rozliczeń, aby otrzymywać wiadomości e-mail po koszty użycia przekracza ilość, którą określisz. Jeśli masz miesięczne Ustawianie alertów dla używania zapasowej określonej wartości. Aby uzyskać więcej informacji, zobacz [skonfigurować alerty dotyczące subskrypcji platformy Microsoft Azure billing](billing-set-up-alerts.md).

![Zrzut ekranu przedstawiający rozliczeń e-mail alertów](./media/billing-getting-started/billing-alert.png)

> [!NOTE]
> Ta funkcja jest dostępny w wersji zapoznawczej, dlatego należy sprawdzić użycie regularnie.

Można użyć szacowania kosztów z Kalkulator cen przyjąć pierwszy alertu.

### <a name="spending-limit"></a> Sprawdź, czy istnieje limit wydatków

Jeśli masz subskrypcję, która używa środków następnie limit wydatków jest dla Ciebie domyślnie włączona. Dzięki temu, gdy spędzają z środków karty kredytowej nie zostały naliczone opłaty. Zobacz [pełną listę oferty Azure i dostępność limit wydatków](https://azure.microsoft.com/support/legal/offer-details/).

Jednak jeśli osiągnęła limit wydatków, usługi wyłączone. Oznacza to, że maszyny wirtualne są alokację. Aby uniknąć przestoju usługi, należy wyłączyć funkcję limitu wydatków. Wszelkie nadwyżkowe pobiera obciążony na Twojej karty kredytowej. 

Aby sprawdzić, czy możesz już uzyskano limit wydatków na, przejdź do [wyświetlić subskrypcje w Centrum konta](https://account.windowsazure.com/Subscriptions). Transparent pojawia się, gdy limit wydatków znajduje się na:

![Zrzut ekranu pokazujący ostrzeżenie dotyczące wydatków limit znajdujące się w w Centrum konta](./media/billing-getting-started/spending-limit-banner.PNG)

Kliknij transparent i postępuj zgodnie z monitami, aby usunąć limit wydatków. Jeśli po utworzeniu konta nie wprowadzono karty kredytowej, będzie konieczne podanie, aby usunąć limit wydatków. Aby uzyskać więcej informacji, zobacz [limit wydatków Azure — jak działa i jak włączyć lub usuń go](https://azure.microsoft.com/pricing/spending-limits/).

## <a name="ways-to-monitor-your-costs-when-using-azure-services"></a>Sposoby monitorowania koszty podczas korzystania z usług Azure

### <a name="tags"></a> Dodawanie tagów do zasobów do grupowania danych rozliczeń

Można użyć znaczników do grupowania danych rozliczeń obsługiwanych usług. Na przykład po uruchomieniu wielu maszyn wirtualnych dla różnych zespołów, następnie służy tagi kategoryzację koszty przez Centrum kosztów (HR marketingu finance) lub środowiska (środowiska produkcyjnego, produkcji wstępnej, test). 

![Zrzut ekranu pokazujący konfigurowania tagów w portalu](./media/billing-getting-started/tags.PNG)

Znaczniki są wyświetlane w różnych koszt raportowania widoków. Na przykład, są one widoczne w Twojej [koszt analitycznego](#costs) od razu i [szczegółów CSV użycia](#invoice-and-usage) po Twojego pierwszego okresu rozliczeniowego.

Aby uzyskać więcej informacji, zobacz [organizowania zasobów na platformie Azure przy użyciu tagów](../azure-resource-manager/resource-group-using-tags.md).

### <a name="costs"></a> Regularnie Sprawdź portalu do podziału kosztów i ocena

Po uzyskaniu usługi uruchomione regularnie sprawdzać, ile ich jest wyceny należy. Możesz wyświetlić bieżący wydatków i ocena wydajności w portalu Azure. 

1. Odwiedź stronę [bloku subskrypcji w portalu Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) i wybrać subskrypcję.

2. Powinni widzieć podziału kosztów i ocena wydajności w bloku menu podręczne. Nie może być obsługiwana dla danej oferty (ostrzeżenie będzie wyświetlana u góry).

    ![Zrzut ekranu przedstawiający ocena i podział w portalu Azure](./media/billing-getting-started/burn-rate.PNG)

3. Kliknij przycisk **analizy kosztów** na liście z lewej strony, aby wyświetlić podziału kosztów przez zasób. Poczekaj 24 godziny po dodaniu usługi do wypełniania danych.

    ![Zrzut ekranu przedstawiający widok analizy kosztów w portalu Azure](./media/billing-getting-started/cost-analysis.PNG)

4. Można filtrować według różnych właściwości, takie jak [tagi](#tags), grupy zasobów i przedziału czasu. Kliknij przycisk **Zastosuj** o potwierdzenie filtry i **Pobierz** Jeśli chcesz wyeksportować widoku pliku Comma-Separated wartości (CSV).

5. Ponadto można kliknąć zasób, aby wyświetlić codziennie spędzają na historię i ile zasobu koszty każdego dnia.

    ![Zrzut ekranu przedstawiający widok Historia wydatków w portalu Azure](./media/billing-getting-started/costhistory.PNG)

Firma Microsoft zaleca sprawdzenie koszty widocznej szacunkowe, który został wyświetlony po wybraniu usług. Jeżeli koszty bardzo różnią się od szacuje, sprawdź planu cenowego (vs A1 maszyna wirtualna A0, na przykład) wybranego dla zasobów. 

### <a name="consider-enabling-cost-cutting-features-like-auto-shutdown-for-vms"></a>Rozważ włączenie funkcji wycinania kosztów, takich jak automatyczne zamykanie dla maszyn wirtualnych

W zależności od scenariusza można skonfigurować automatyczne zamykanie dla maszyn wirtualnych w portalu Azure. Aby uzyskać więcej informacji, zobacz [automatyczne zamykanie dla maszyn wirtualnych przy użyciu usługi Azure Resource Manager](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/).

![Zrzut ekranu opcji automatycznego zamknięcia w portalu](./media/billing-getting-started/auto-shutdown.PNG)

Automatyczne zamykanie nie jest taka sama jak podczas zamykania w ramach maszyny Wirtualnej z apletu Opcje zasilania. Automatyczne zamykanie zatrzymuje i zwalnia maszyn wirtualnych można zatrzymać użycie dodatkowych opłat. Aby uzyskać więcej informacji, zobacz często zadawane pytania dotyczące cen [maszyn wirtualnych systemu Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) i [maszyn wirtualnych systemu Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) o stanach maszyny Wirtualnej.

Więcej funkcji wycinania koszt dla Twojego środowiska projektowania i testowania, zapoznaj się z [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab/).

### <a name="turn-on-and-check-out-azure-advisor-recommendations"></a>Włącz i zapoznaj się z zalecenia usługi Advisor Azure

[Klasyfikator Azure](../advisor/advisor-overview.md) jest funkcja w wersji zapoznawczej, która ułatwia obniżenie kosztów, określając z niskim użycia zasobów. Ją włączyć w portalu Azure:

![Przycisk Advisor zrzut ekranu Azure w portalu Azure](./media/billing-getting-started/advisor-button.PNG)

Następnie, aby pobrać praktyczne zalecenia **koszt** kartę na pulpicie nawigacyjnym usługi Advisor:

![Przykład zalecenie koszt zrzut ekranu Advisor](./media/billing-getting-started/advisor-action.PNG)

Aby uzyskać więcej informacji, zobacz [zalecenia usługi Advisor koszt](../advisor/advisor-cost-recommendations.md).

## <a name="reviewing-costs-at-the-end-of-your-billing-cycle"></a>Przeglądanie kosztów na końcu Twojej cyklu rozliczeniowego

Po zakończeniu Twojej cyklu rozliczeniowego faktury staną się dostępne. Możesz również [Pobierz poza faktury i szczegółów plików użycia](billing-download-azure-invoice-daily-usage-date.md) się upewnić, że naliczono poprawnie. Aby uzyskać więcej informacji na temat porównanie dzienne użycie z faktury, zobacz [zrozumieć rachunku platformy Microsoft Azure](billing-understand-your-bill.md).

### <a name="billing-api"></a>Interfejs API rozliczeń

Programowo Pobierz dane użycia za pomocą naszych rozliczeń interfejsu API. Użyć interfejsu API RateCard i użycia interfejsu API można pobrać rachunku użycie. Aby uzyskać więcej informacji, zobacz [uzyskać wgląd w Microsoft Azure użycia zasobów](billing-usage-rate-card-overview.md).

## <a name="other-offers"></a> Zasoby dodatkowe i szczególnych przypadkach

### <a name="ea-csp-and-sponsorship-customers"></a>Umowa EA, dostawca usług Kryptograficznych i dostęp sponsorowany klientów
Skontaktuj się z menedżerem lub partnera Azure, aby rozpocząć.

| Oferta | Zasoby |
|-------------------------------|-----------------------------------------------------------------------------------|
| Enterprise Agreement (EA) | [EA portal](https://ea.azure.com/), [pomocy docs](https://ea.azure.com/helpdocs), i [raportu usługi Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-enterprise/) |
| Cloud Solution Provider (CSP) | Komunikować się z dostawcą |
| Sponsorowanie systemu Azure | [Dostęp sponsorowany portalu](https://www.microsoftazuresponsorships.com/) |

Jeśli zarządzasz IT dla dużych organizacji zalecamy odczytu [szkieletu Azure enterprise](../azure-resource-manager/resource-manager-subscription-governance.md) i [organizacji IT oficjalny dokument](http://download.microsoft.com/download/F/F/F/FFF60E6C-DBA1-4214-BEFD-3130C340B138/Azure_Onboarding_Guide_for_IT_Organizations_EN_US.pdf) (do pobrania PDF, tylko w języku angielskim).

#### <a name="EA"></a> Podgląd umowy Enterprise Agreement koszt widoków w portalu Azure 

Enterprise koszt widoków obecnie znajdują się w publicznej wersji zapoznawczej. Elementy należy pamiętać:
- Koszty subskrypcji są na podstawie użycia i bez uwzględnienia przedpłacone kwoty, nadwyżki ilości dołączone, dostosowania i podatki. Rzeczywiste opłaty są obliczane na poziomie rejestrowania. 
- Kwoty wyświetlone w portalu Azure mogą być opóźnione w porównaniu do wartości w witrynie Enterprise portal.  
- Jeśli nie widzisz kosztów, może to wynikać z jednego z następujących powodów:
    - Nie masz uprawnień wystarczających RBAC na poziomie subskrypcji. Aby wyświetlić widoki koszt przedsiębiorstwa, musi być czytnika rozliczeń, czytnika, współautora lub właściciela na poziomie subskrypcji.
    - Jesteś właścicielem konta i Administrator rejestracji wyłączył "opłaty widoku AO" ustawienie.  Skontaktuj się z administratorem rejestracji, aby uzyskać dostęp do kosztów. 
    - Jesteś administratorem działu, a Administrator rejestracji wyłączył "opłaty widoku DA" ustawienie.  Skontaktuj się z administratorem rejestracji w celu uzyskania dostępu. 
    - Azure zostały zakupione w ramach partnera kanału i partner nie udostępnił informacje o cenach.  
- Ustawienia związane z kosztem dostępu są aktualizowane w witrynie Enterprise portal, ma opóźnienia kilka minut, zanim zmiany zostaną odzwierciedlone w portalu Azure.
- Limit wydatków, alerty dotyczące rozliczeń i wskazówki faktury nie dotyczą EA subskrypcji.

### <a name="check-your-subscription-and-access"></a>Sprawdź subskrypcję i dostępu

Koszty wyświetlania wymagają [dostępu do informacji dotyczących rozliczeń na poziomie subskrypcji](billing-manage-access.md), ale tylko administrator konta ma dostęp do [Centrum konta](https://account.azure.com/Subscriptions), zmienić informacje rozliczeniowe i zarządzać subskrypcjami. Administrator konta jest osobą, która wykonano kroki procesu tworzenia konta. Aby uzyskać więcej informacji, zobacz [Dodawanie lub zmienianie ról administrator usługi Azure, które zarządzają subskrypcji lub usługi](billing-add-change-azure-subscription-administrator.md).

Aby sprawdzić, czy jesteś administratorem konta, przejdź do [bloku subskrypcji w portalu Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) i przyjrzyj się listy mają dostęp do subskrypcji. Sprawdź w obszarze **Moje roli**. Informacja *administrator konta*, a następnie jest ok. Informacja czegoś innego, takich jak *właściciela*, a następnie masz pełne uprawnienia.

![Zrzut ekranu przedstawiający roli użytkownika w widoku subskrypcji w portalu Azure](./media/billing-getting-started/sub-blade-view.PNG)

Jeśli nie jesteś administratorem konta, a następnie ktoś prawdopodobnie udostępniła Ci częściowy dostęp za pośrednictwem [kontroli dostępu opartej na roli Azure Active Directory](../role-based-access-control/role-assignments-portal.md) (RBAC). Do zarządzania subskrypcjami i rozliczeń info, zmień [znaleźć administrator konta](billing-subscription-transfer.md#whoisaa) i poproś go do wykonywania zadań lub [przenieść subskrypcję na Twoje](billing-subscription-transfer.md).

Jeśli administrator konta nie jest już w swojej organizacji i trzeba zarządzać rozliczeń, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). 
## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Kontakt z pomocą techniczną

Jeśli potrzebujesz pomocy, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) uzyskać szybkie rozwiązanie problemu.
