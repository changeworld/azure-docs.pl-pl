---
title: Zapobieganie powstawaniu nieoczekiwanych kosztów, zarządzania rozliczeniami na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak uniknąć nieoczekiwanych opłat na rachunku dotyczącym platformy Azure. Użyj funkcji Śledzenie kosztów i zarządzania dla subskrypcji Microsoft Azure.
services: ''
documentationcenter: ''
author: bandersmsft
manager: alherz
editor: ''
tags: billing
ms.assetid: 482191ac-147e-4eb6-9655-c40c13846672
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2018
ms.author: banders
ms.openlocfilehash: d215e2ba5d650b532b9d7554ccca9ad5537cf4de
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60919450"
---
# <a name="prevent-unexpected-charges-with-azure-billing-and-cost-management"></a>Uniknąć nieoczekiwanych opłat za pomocą rozliczeń platformy Azure i zarządzania kosztami

Po zarejestrowaniu się na platformie Azure, istnieje kilka rzeczy, które można zrobić, aby lepiej zrozumieć, wydatki. [Kalkulator cen](https://azure.microsoft.com/pricing/calculator/) może zapewnić oszacowanie kosztów, przed przystąpieniem do tworzenia zasobów platformy Azure. [Witryny Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) zapewnia bieżącego podział kosztów i prognozy dla Twojej subskrypcji. Jeśli chcesz grupować i poznanie kosztów dla różnych projektów lub zespołów, przyjrzyj [tagowania zasobów](../azure-resource-manager/resource-group-using-tags.md). Jeśli Twoja organizacja ma system raportowania, którego chcesz użyć, zapoznaj się z [interfejsy API rozliczeń](billing-usage-rate-card-overview.md).

- Jeśli Twoja subskrypcja jest umowy Enterprise Agreement (EA), dostępna jest publiczną wersję zapoznawczą do zobaczenia koszty w witrynie Azure portal. Jeśli Twoja subskrypcja znajduje się za pośrednictwem dostawcy rozwiązań w chmurze (CSP) lub dostęp sponsorowany Azure, następnie niektóre następujące funkcje mogą nie mieć zastosowania do użytkownika. Zobacz [dodatkowe zasoby dotyczące umowy EA, dostawca usług Kryptograficznych i dostępu Sponsorowanego](#other-offers) Aby uzyskać więcej informacji.

- Jeśli Twoja subskrypcja jest bezpłatna wersja próbna [programu Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)platformy Azure w otwartych (AIO) lub BizSpark, Twoja subskrypcja jest automatycznie wyłączana, gdy używane są wszystkie środki. Dowiedz się więcej o [limitów wydatków](#spending-limit) pozwala uniknąć nieoczekiwanie wyłączony subskrypcji.

- Jeśli logujesz się w celu [bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/), [można korzystać z niektórych z najbardziej popularnych usług platformy Azure bezpłatnie przez 12 miesięcy](billing-create-free-services-included-free-account.md). Wraz z zaleceniami wymienione poniżej, zobacz [uniknąć opłat za darmo konta](billing-avoid-charges-free-account.md).

## <a name="get-estimated-costs-before-adding-azure-services"></a>Uzyskaj szacowane koszty przed dodaniem usług platformy Azure

### <a name="estimate-cost-online-using-the-pricing-calculator"></a>Oszacuj koszt w trybie online za pomocą kalkulatora cen.

Zapoznaj się z [Kalkulator cen](https://azure.microsoft.com/pricing/calculator/) można pobrać szacowany koszt miesięczny interesuje Cię usługi. Możesz dodać pierwszej strony zasobów platformy Azure, aby zidentyfikować szacowany koszt.

![Zrzut ekranu przedstawiający menu Kalkulator cen](./media/billing-getting-started/pricing-calc.png)

Na przykład Windows Virtual Machine (maszyna wirtualna A1) szacuje się koszt 66.96 USD/miesiąc w godzin obliczeniowych, jeśli to pole pozostanie uruchomione przez cały czas:

![Zrzut ekranu przedstawiający maszyny Wirtualnej z systemem Windows A1 jest szacowane koszty 66.96 USD miesięcznie kalkulatora cen.](./media/billing-getting-started/pricing-calcVM.png)

Aby uzyskać więcej informacji na temat cen, zobacz ten [— często zadawane pytania](https://azure.microsoft.com/pricing/faq/). Lub jeśli chcesz komunikować się z platformy Azure sprzedawcy, skontaktuj się z pomocą techniczną firmy 1-800-867-1389.

### <a name="review-the-estimated-cost-in-the-azure-portal"></a>Przegląd szacowanego kosztu w witrynie Azure portal

Zwykle po dodaniu usługi w witrynie Azure portal, istnieje widok przedstawiający podobne szacowany koszt na miesiąc. Na przykład w przypadku wybrania rozmiaru maszyny Wirtualnej Windows, zostanie wyświetlony szacowany miesięczny koszt godzin obliczeniowych:

![Przykład: maszyna wirtualna A1 Windows jest szacowany koszt 66.96 USD na miesiąc](./media/billing-getting-started/vm-size-cost.PNG)

### <a name="spending-limit"></a> Sprawdź, czy limit wydatków

Jeśli masz subskrypcję, która korzysta z kredytów, następnie limit wydatków jest dla Ciebie domyślnie włączona. W ten sposób gdy wydatki wszystkich środków, karta kredytowa nie jest naliczana opłata. Zobacz [pełną listę ofert platformy Azure i dostępność limitu wydatków](https://azure.microsoft.com/support/legal/offer-details/).

Jednak jeśli zostanie osiągnięty limit wydatków, usługi wyłączone. Oznacza to, że maszyny wirtualne są dealokowane. Aby uniknąć przestoju usługi, musisz wyłączyć limit wydatków. Nadwyżkowe pobiera opłata na karty kredytowej w pliku. 

Aby zobaczyć, jeśli możesz już stało się limit wydatków o wartości, przejdź do [subskrypcje wyświetlone w Centrum konta](https://account.windowsazure.com/Subscriptions). Zostanie wyświetlony transparent, jeśli limit wydatków, znajduje się na:

![Zrzut ekranu przedstawiający ostrzeżenie o wydatków limit zablokowany z przyczyn w Centrum konta](./media/billing-getting-started/spending-limit-banner.PNG)

Kliknij transparent i postępuj zgodnie z monitami, aby usunąć limit wydatków. Jeśli informacje o karcie kredytowej nie została wprowadzona podczas tworzenia konta, należy wprowadzić, aby usunąć limit wydatków. Aby uzyskać więcej informacji, zobacz [limit wydatków platformy Azure — jak działa i jak włączyć lub usunąć go](https://azure.microsoft.com/pricing/spending-limits/).

Możesz użyć [Cloudyn](https://www.cloudyn.com/) usługi w celu tworzenia alertów, które automatycznie powiadamiają uczestników projektu o anomaliach wydatków i przekraczania ryzyka. Alerty można tworzyć za pomocą raportów obsługujących alerty oparte na budżecie i progach kosztów. Aby uzyskać więcej informacji na temat korzystania z platformy Cloudyn, zobacz [samouczka: Przeglądanie użycia i kosztów](../cost-management/tutorial-review-usage.md).

W tym przykładzie skorzystasz z raportu **Actual Cost Over Time** (Koszt rzeczywisty na przestrzeni czasu), aby wysłać powiadomienie, gdy wydatki na maszyny wirtualne platformy Azure zbliżą się do poziomu łącznego budżetu. W tym scenariuszu łączny budżet wynosi 20 000 USD. Chcesz otrzymywać powiadomienie, gdy koszty osiągną poziom 9000 USD, a potem kolejne powiadomienie po osiągnięciu wartości 10 000 USD.

1. W menu w górnej części portalu Cloudyn wybierz pozycję **Costs** > **Cost Analysis** > **Actual Cost Over Time** (Koszty > Analiza kosztów > Koszt rzeczywisty na przestrzeni czasu). 
2. Ustaw pozycję **Groups** (Grupy) na **Service** (Usługa), a pozycję **Filter on the service** (Filtruj usługę) na **Azure/VM**. 
3. W prawym górnym rogu raportu wybierz pozycję **Actions** (Akcje), a następnie wybierz pozycję **Schedule report** (Zaplanuj raport).
4. Aby wysyłać do siebie wiadomość e-mail dotyczącą raportu w zaplanowanych odstępach czasu, wybierz kartę **Scheduling** (Planowanie) w oknie dialogowym **Save or Schedule this report** (Zapisz lub zaplanuj ten raport). Należy wybrać opcję **Send via email** (Wyślij w wiadomości e-mail). Wszystkie użyte tagi, grupowania i filtry zostaną uwzględnione w raporcie wysłanym w wiadomości e-mail. 
5. Wybierz kartę **Threshold** (Próg) i wybierz pozycję **Actual Cost vs. Threshold** (Koszt rzeczywisty a próg). 
   1. W polu progu **Red alert** (Czerwony alert) wprowadź wartość 10 000. 
   2. W polu progu **Yellow alert** (Żółty alert) wprowadź wartość 9000. 
   3. W polu **Number of consecutive alerts** (Liczba kolejnych alertów) wprowadź liczbę alertów, które mają być wysyłane. Po wysłaniu określonej całkowitej liczby alertów dodatkowe alerty nie są już wysyłane. 
6. Wybierz pozycję **Zapisz**.

    ![Przykład pokazujący czerwone i żółte alerty na podstawie progów wydatków](./media/billing-getting-started/schedule-alert01.png)

Możesz również wybrać metrykę **Cost Percentage vs. Budget threshold** (Procent kosztów a próg budżetu), aby utworzyć alerty. Pozwala to określić progi jako wartości procentowe budżetu, a nie jako wartości pieniężne.

## <a name="ways-to-monitor-your-costs-when-using-azure-services"></a>Sposoby monitorowania koszty, korzystając z usług platformy Azure

### <a name="tags"></a> Dodawanie tagów do zasobów do grupowania danych dotyczących rozliczeń

W przypadku obsługiwanych usług, można użyć tagów do grupy danych dotyczących rozliczeń. Na przykład jeśli uruchomisz kilka maszyn wirtualnych przez różne zespoły, następnie umożliwia tagi kategoryzowanie kosztów według Centrum kosztu (HR, marketingu, not) lub środowiska (test przedprodukcyjnego, produkcji,). 

![Zrzut ekranu przedstawiający Konfigurowanie znaczników w portalu](./media/billing-getting-started/tags.PNG)

Znaczniki są wyświetlane w całym raportowania widoki kosztów. Na przykład, są one widoczne w Twojej [widok analiza kosztów](#costs) razu i szczegółów użycie.csv od pierwszego okresu rozliczeniowego.

Aby uzyskać więcej informacji, zobacz [porządkowanie zasobów na platformie Azure za pomocą tagów](../azure-resource-manager/resource-group-using-tags.md).

### <a name="costs"></a> Regularnie Sprawdź portal dla podział kosztów i współczynnik spalania

Po uzyskaniu swoich usług, regularnie sprawdzać, ile są one wyceny użytkownik. Można wyświetlić bieżące wydatków i współczynnik spalania w witrynie Azure portal.

1. Odwiedź stronę [subskrypcje w witrynie Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) i wybierz subskrypcję.

2. Jeśli jest obsługiwany dla Twojej subskrypcji, zobacz podział kosztów i współczynnik spalania.

    ![Zrzut ekranu przedstawiający spalania i podział w witrynie Azure portal](./media/billing-getting-started/burn-rate.PNG)

3. Kliknij przycisk **analiza kosztów** na liście z lewej strony, aby zobaczyć podział kosztów przez zasób. Poczekaj 24 godziny po dodaniu usługi danych wypełnić.

    ![Zrzut ekranu przedstawiający widok analizy kosztów w witrynie Azure portal](./media/billing-getting-started/cost-analysis.PNG)

4. Można filtrować według różnych właściwości, takie jak [tagi](#tags), typ zasobu, grupy zasobów i przedziału czasu. Kliknij przycisk **Zastosuj** aby upewnić się, filtry i **Pobierz** Jeśli chcesz wyeksportować w widoku do pliku wartości Comma-Separated (CSV).

5. Ponadto można kliknąć zasób, aby zobaczyć swój dzienny poświęcić historii i ile zasobów kosztuje każdego dnia.

    ![Zrzut ekranu przedstawiający widok Historia ma wydatków w witrynie Azure portal](./media/billing-getting-started/costhistory.PNG)

Firma Microsoft zaleca, Sprawdź koszty, które widzisz szacunkowe, które zostały użyte w przypadku wybrania usługi. Jeśli koszty są bardzo popularny różni się od szacunki, sprawdź plan taryfowy wybranego dla zasobów.

### <a name="consider-enabling-cost-cutting-features-like-auto-shutdown-for-vms"></a>Rozważ włączenie funkcji jednoczesnym obniżeniu kosztów, takich jak automatyczne zamknięcie dla maszyn wirtualnych

Zależnie od scenariusza można skonfigurować automatyczne zamykanie dla maszyn wirtualnych w witrynie Azure portal. Aby uzyskać więcej informacji, zobacz [automatyczne zamykanie dla maszyn wirtualnych przy użyciu usługi Azure Resource Manager](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/).

![Zrzut ekranu przedstawiający automatycznego zamykania opcji w portalu](./media/billing-getting-started/auto-shutdown.PNG)

Automatyczne zamknięcie nie jest taka sama jak podczas zamykania na maszynie wirtualnej za pomocą apletu Opcje zasilania. Automatyczne zamknięcie zatrzymanie i cofnięcie przydziału maszyn wirtualnych, aby zatrzymać opłaty za dodatkowe użycie. Aby uzyskać więcej informacji, zobacz często zadawane pytania dotyczące cennika [maszyn wirtualnych systemu Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) i [maszyn wirtualnych Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) o stanach maszyn wirtualnych.

Aby uzyskać więcej funkcji jednoczesnym obniżeniu kosztów dla środowisk programowania i testowania, zapoznaj się [usługi Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab/).

### <a name="turn-on-and-check-out-azure-advisor-recommendations"></a>Włącz i zapoznaj się z zaleceń usługi Azure Advisor

[Usługa Azure Advisor](../advisor/advisor-overview.md) jest funkcją, która pomoże Ci zmniejszenie kosztów związanych z identyfikowanie zasobów z niskim użyciem. Odwiedź usługę Advisor w witrynie Azure portal:

![Zrzut ekranu usługi Azure Advisor przycisku w witrynie Azure portal](./media/billing-getting-started/advisor-button.PNG)

Następnie możesz uzyskać praktyczne zalecenia w **koszt** karcie pulpit nawigacyjny usługi Advisor:

![Przykład zalecenie dotyczące kosztów zrzut ekranu usługi Advisor](./media/billing-getting-started/advisor-action.PNG)

Aby uzyskać więcej informacji, zobacz [rekomendacji dotyczących kosztu Advisor](../advisor/advisor-cost-recommendations.md).

## <a name="reviewing-costs-at-the-end-of-your-billing-cycle"></a>Przeglądanie koszty pod koniec cyklu rozliczeniowym

Po zakończeniu cyklu rozliczeniowego Twoja faktura stanie się dostępna. Możesz również [pobrać przeszłe faktury i pliki użycia szczegółów](billing-download-azure-invoice-daily-usage-date.md) się upewnić, że naliczono poprawnie. Aby uzyskać więcej informacji na temat porównanie z dziennym użyciem za pomocą faktury, zobacz [opis zawartości rachunku dla systemu Microsoft Azure](billing-understand-your-bill.md).

### <a name="billing-api"></a>Interfejs API rozliczeń

Nasze interfejsy API, rozliczeń umożliwia programowe pobieranie danych użycia. Użyj interfejsu API RateCard i interfejs API użycia ze sobą można pobrać rozliczane użycie. Aby uzyskać więcej informacji, zobacz [wgląd w użycie zasobów usługi Microsoft Azure](billing-usage-rate-card-overview.md).

## <a name="other-offers"></a> Dodatkowe zasoby oraz specjalne przypadki

### <a name="ea-csp-and-sponsorship-customers"></a>Klienci EA, dostawca usług Kryptograficznych i dostępu Sponsorowanego
Porozmawiaj z opiekunem albo partnerów platformy Azure, aby rozpocząć pracę.

| Oferta | Zasoby |
|-------------------------------|-----------------------------------------------------------------------------------|
| Enterprise Agreement (EA) | [Portalu EA](https://ea.azure.com/), [pomocy docs](https://ea.azure.com/helpdocs), i [raportu usługi Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-enterprise/) |
| Cloud Solution Provider (CSP) | Skontaktuj się z dostawcą |
| Sponsorowanie systemu Azure | [Portalu dostęp sponsorowany](https://www.microsoftazuresponsorships.com/) |

W przypadku zarządzania IT dla dużych organizacji, zalecamy przeczytanie [Azure enterprise scaffold](/azure/architecture/cloud-adoption-guide/subscription-governance) i [organizacji IT oficjalny dokument dotyczący](https://download.microsoft.com/download/F/F/F/FFF60E6C-DBA1-4214-BEFD-3130C340B138/Azure_Onboarding_Guide_for_IT_Organizations_EN_US.pdf) (PDF, pobieranie, tylko w języku angielskim).

#### <a name="EA"></a> Koszt widokach w ramach witryny Azure portal umowy Enterprise Agreement (wersja zapoznawcza) 

Widoki kosztów przedsiębiorstwa są obecnie dostępne w publicznej wersji zapoznawczej. Kwestie, które należy zwrócić uwagę:

- Koszty subskrypcji są na podstawie użycia i nie uwzględniają przedpłaconych kwot, opłaty za dodatkowe transakcje, wliczonych zasobów, korekt i podatków. Rzeczywiste opłaty są obliczane na poziomie rejestracji.
- Kwoty podane w witrynie Azure portal może być inny niż co znajduje się w witrynie Enterprise portal. Aktualizacje w witrynie Enterprise portal może potrwać kilka minut, zanim zmiany są wyświetlane w witrynie Azure portal.
- Jeśli nie widzisz kosztów, może być jednego z następujących powodów:
    - Nie masz uprawnień na poziomie subskrypcji. Aby wyświetlić widoki kosztów przedsiębiorstwa, musi być Czytelnik rozliczenia, Czytelnik, współautora lub właściciela na poziomie subskrypcji.
    - Jesteś właścicielem konta i Administrator rejestracji została wyłączona "opłaty widoku AO" ustawienie.  Skontaktuj się z administratorem rejestracji w celu uzyskania dostępu do kosztów. 
    - Jesteś administratorem działu i administratorem rejestracji została wyłączona "opłaty widoku DA" ustawienie.  Skontaktuj się z administratorem rejestracji, aby uzyskać dostęp.
    - Zakupiono platformy Azure za pośrednictwem partnera kanału i partnera nie wersji informacji o cenach.  
- Możesz zaktualizować ustawienia związane z koszty dostępu w witrynie Enterprise portal, czy istnieje opóźnienie kilka minut, zanim zmiany są wyświetlane w witrynie Azure portal.
- Limit wydatków i wskazówki dotyczące faktury nie mają zastosowania do subskrypcji umowy EA.

### <a name="check-your-subscription-and-access"></a>Sprawdź subskrypcję i dostępu

Aby wyświetlić koszty, konieczne jest posiadanie [dostępu do informacji dotyczących rozliczeń na poziomie subskrypcji](billing-manage-access.md). Tylko administrator konta może uzyskać dostęp [Centrum kont](https://account.azure.com/Subscriptions), zmieniać informacji dotyczących rozliczeń i zarządzania subskrypcjami. Administrator konta to osoba, która kolejno przerobione procesu rejestracji. Aby uzyskać więcej informacji, zobacz [apletu Dodaj lub zmień role administratora platformy Azure, które zarządzają subskrypcją lub usługami](billing-add-change-azure-subscription-administrator.md).

Aby zobaczyć, jeśli jesteś administratorem konta, przejdź do [subskrypcje w witrynie Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Przyjrzyj się liście subskrypcji, do których masz dostęp do. Sprawdź w obszarze **Moja rola**. Jeśli wyświetlany jest tekst *administrator konta*, jesteś ok. Jeśli wyświetlany jest tekst coś innego, takich jak *właściciela*, wówczas masz pełne uprawnienia.

![Zrzut ekranu przedstawiający swojej roli w widoku subskrypcje w witrynie Azure portal](./media/billing-getting-started/sub-blade-view.PNG)

Jeśli nie jesteś administratorem konta, a następnie ktoś prawdopodobnie udostępniła Ci częściowy dostęp za pomocą [kontroli dostępu opartej na roli Azure Active Directory](../role-based-access-control/role-assignments-portal.md) (RBAC). Do zarządzania subskrypcjami i zmiany rozliczeń info, [znaleźć administrator konta](billing-subscription-transfer.md#whoisaa). Poproś administratora konta, aby wykonać zadania lub [przenieść subskrypcję do Ciebie](billing-subscription-transfer.md).

Jeśli administrator konta nie jest już w organizacji, należy do zarządzania rozliczeniami, [skontaktuj się z nami](https://go.microsoft.com/fwlink/?linkid=2083458).


### <a name="how-to-request-a-service-level-agreement-credit-for-a-service-incident"></a>Jak utworzyć żądanie umowy dotyczącej poziomu usług środki na korzystanie z zdarzenie w usłudze

W umowie dotyczącej poziomu usług (SLA) opisano zobowiązania firmy Microsoft dotyczące czasu dostępności i połączeń. Zdarzenie w usłudze jest zgłaszany w przypadku usług platformy Azure napotkania problemu tego czasu wpływa na działania lub łączności, często nazywane "awarii." Jeśli nie możemy osiągnąć i Obsługa poziomów usług dla każdej usługi, zgodnie z opisem w umowie SLA, może być kwalifikuje się środki w wysokości część miesięcznych opłat za usługę.

Aby zażądać środki:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/). Jeśli masz wiele kont, upewnij się, użyj jednego, który miała wpływ przestojów platformy Azure. Ułatwia to obsługę automatycznego zbierania niezbędne informacje i szybciej rozwiązać sprawę.
2. Utwórz nowe żądanie pomocy technicznej.
3. W obszarze **typ problemu**, wybierz opcję **rozliczeń**.
4. W obszarze **typ problemu**, wybierz opcję **żądanie zwrotu pieniędzy**.
5. Dodaj szczegóły, aby określić, że jest wysyłane żądanie kredytu SLA, wspominanie daty/godziny/strefy czasowej, a także objęte usługi, (maszyny wirtualne, witryny sieci Web itp.)
6. Sprawdź swoje szczegóły dotyczące kontaktu i wybrać **Utwórz** przycisk, aby przesłać żądanie.

Umowa SLA progi różnią się usługi. Na przykład warstwa sieci Web SQL zgodnie z umową SLA na poziomie 99,9%, maszyny wirtualne mają umową SLA gwarantującą dostępność przez 99,95%, a SQL w warstwie standardowa ma umową SLA gwarantującą dostępność przez 99,99%.

W przypadku niektórych usług istnieją wymagania wstępne dotyczące umowy SLA do zastosowania. Na przykład maszyny wirtualne, musi mieć co najmniej dwóch wystąpieniach wdrożonych w tym samym zestawie dostępności.

Aby uzyskać więcej informacji, zobacz [umowy dotyczące poziomu usług](https://azure.microsoft.com/en-us/support/legal/sla/) dokumentacji i [podsumowanie umów SLA dla usług platformy Azure](https://azure.microsoft.com/en-us/support/legal/sla/summary/) dokumentacji.

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie obsługi](https://go.microsoft.com/fwlink/?linkid=2083458).
