---
title: Szybki start — eksplorowanie kosztów platformy Azure za pomocą analizy kosztów | Microsoft Docs
description: Ten przewodnik Szybki start ułatwia eksplorowanie i analizowanie kosztów organizacyjnych platformy Azure za pomocą funkcji analizy kosztów.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 12/04/2019
ms.topic: quickstart
ms.service: cost-management-billing
manager: micflan
ms.custom: seodec18
ms.openlocfilehash: f053b30d344e5372617a5bf98c087056c4fe2911
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76294154"
---
# <a name="quickstart-explore-and-analyze-costs-with-cost-analysis"></a>Szybki start: Eksplorowanie i analizowanie kosztów za pomocą analizy kosztów

Aby poprawnie kontrolować i optymalizować koszty platformy Azure, trzeba zrozumieć, skąd pochodzą koszty w organizacji. Warto również wiedzieć, ile kosztują usługi oraz które środowiska i systemy obsługują. Widoczność pełnego zakresu kosztów to kluczowy element wymagany do dokładnego zrozumienia wzorca wydatków organizacji. Za pomocą wzorców wydatków możesz wymuszać mechanizmy kontroli kosztów, takie jak budżety.

Ten przewodnik Szybki start ułatwia eksplorowanie i analizowanie kosztów organizacyjnych przy użyciu funkcji analizy kosztów. Możesz wyświetlić zagregowane koszty według organizacji, aby dowiedzieć się, gdzie występują koszty w miarę upływu czasu, i zidentyfikować trendy wydatków. Zakumulowane koszty w czasie można wyświetlać w celu oszacowania miesięcznych, kwartalnych, a nawet rocznych trendów kosztów w odniesieniu do budżetu. Budżet pomaga działać zgodnie z ograniczeniami finansowymi. Budżet jest również używany do wyświetlania kosztów dziennych lub miesięcznych w celu odizolowania nieprawidłowości wydatków. Ponadto można pobrać dane bieżącego raportu do dalszej analizy lub do użycia w systemie zewnętrznym.

W tym przewodniku Szybki start zawarto informacje na temat wykonywania następujących czynności:

- Przeglądanie kosztów w obrębie analizy kosztów
- Dostosowywanie widoków kosztów
- Pobieranie danych analizy kosztów


## <a name="prerequisites"></a>Wymagania wstępne

Analiza kosztów obsługuje różne typy kont platformy Azure. Aby wyświetlić pełną listę obsługiwanych typów kont, zobacz [Omówienie danych usługi Cost Management](understand-cost-mgt-data.md). Aby wyświetlić dane kosztów, potrzebujesz przynajmniej dostępu do odczytu dla Twojego konta platformy Azure.

Aby uzyskać informacje na temat przypisywania dostępu do danych usługi Azure Cost Management, zobacz [Przypisywanie dostępu do danych](../../cost-management/assign-access-acm-data.md).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

- Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

## <a name="review-costs-in-cost-analysis"></a>Przeglądanie kosztów w obrębie analizy kosztów

Aby sprawdzić swoje koszty w analizie kosztów, otwórz zakres w witrynie Azure Portal, a następnie wybierz pozycję **Analiza kosztów** w menu. Na przykład przejdź do obszaru **Subskrypcje**, wybierz subskrypcję z listy, a następnie wybierz pozycję **Analiza kosztów** w menu. Użyj kapsułki **Zakres**, aby przełączyć na inny zakres w analizie kosztów. Aby uzyskać więcej informacji na temat zakresów, zobacz [Omówienie zakresów i praca z nimi](understand-work-scopes.md).

Wybrany zakres będzie używany w całej usłudze Cost Management w celu zapewnienia konsolidacji danych i kontrolowania dostępu do informacji o kosztach. Gdy używasz zakresów, nie wybierasz równocześnie wielu z nich. W zamian wybierasz większy zakres obejmujący inne zakresy, a następnie filtrujesz zawartość do zagnieżdżonych zakresów, których potrzebujesz. Zrozumienie tej metody jest istotne, ponieważ niektóre osoby mogą nie mieć dostępu do pojedynczego zakresu nadrzędnego, który obejmuje wiele zakresów zagnieżdżonych.

Początkowy widok analizy kosztów zawiera poniższe obszary.

**Widok kosztów skumulowanych**: Przedstawia wstępnie zdefiniowaną konfigurację widoku analizy kosztów. Każdy widok zawiera ustawienia zakresu dat, stopnia szczegółowości, sposobu grupowania i filtrowania. W widoku domyślnym wyświetlane są skumulowane koszty dla bieżącego okresu rozliczeniowego, ale można go zmienić na inne wbudowane widoki. Aby uzyskać więcej informacji, zobacz [Dostosowywanie widoków kosztów](#customize-cost-views).

**Rzeczywisty koszt**: Przedstawia sumę kosztów użycia i zakupów dla bieżącego miesiąca, które będą widoczne na fakturze, w miarę ich naliczania.

**Prognoza**: Przedstawia sumę prognozowanych kosztów w wybranym okresie. (Prognoza jest w wersji zapoznawczej).

**Budżet**: Przedstawia limit planowanych wydatków w wybranym zakresie, jeśli jest dostępny.

**Skumulowany stopień szczegółowości**: Przedstawia sumę zagregowanych kosztów dziennych od początku okresu rozliczeniowego. Po [utworzeniu budżetu](tutorial-acm-create-budgets.md) subskrypcji lub konta billingowego możesz szybko wyświetlić trend wydatków w odniesieniu do budżetu. Umieść kursor nad datą, aby wyświetlić skumulowany koszt w tym dniu.

**Wykresy przestawne (pierścieniowe)** : Są to dynamiczne elementy przestawne, które przedstawiają podział kosztów według typowego zestawu właściwości standardowych. Przedstawiają one koszty w bieżącym miesiącu, w kolejności od największych do najmniejszych. Wykresy przestawne można zmieniać w dowolnym momencie, wybierając inny element przestawny. Koszty są domyślnie dzielone na kategorie według usługi (kategorii miernika), lokalizacji (regionu) i zakresu podrzędnego. Na przykład konta rejestracji należą do kont rozliczeniowych, grupy zasobów należą do subskrypcji, a zasoby należą do grup zasobów.

![Początkowy widok analizy kosztów w witrynie Azure Portal](./media/quick-acm-cost-analysis/cost-analysis-01.png)

## <a name="customize-cost-views"></a>Dostosowywanie widoków kosztów

Analiza kosztów ma cztery wbudowane widoki zoptymalizowane pod kątem najpopularniejszych celów:

Widok | Odpowiada na pytania
--- | ---
Skumulowany koszt | Ile wynoszą dotychczasowe wydatki w tym miesiącu? Czy wydatki zmieściły się w budżecie?
Dzienny koszt | Czy w ciągu ostatnich 30 dni wystąpiły jakiekolwiek wzrosty kosztów dziennych?
Koszt według usługi | Jak moje miesięczne użycie zmieniało się w okresie objętym przez trzy ostatnie faktury?
Koszt według zasobu | Które zasoby kosztowały dotychczas najwięcej w tym miesiącu?

![Selektor widoku przedstawiający przykładowy wybór dla tego miesiąca](./media/quick-acm-cost-analysis/view-selector.png)

W większości przypadków będziesz jednak potrzebować dokładniejszej analizy. Dostosowywanie rozpoczyna się od wybrania daty w górnej części strony.

Analiza kosztów domyślnie przedstawia dane z bieżącego miesiąca. Selektor daty służy do szybkiego przełączania typowych zakresów dat. Przykłady to: ostatnie siedem dni, ostatni miesiąc, bieżący rok lub niestandardowy zakres dat. Subskrypcje płatne zgodnie z rzeczywistym użyciem obejmują również zakresy dat oparte na okresie rozliczeniowym, który nie jest powiązany z miesiącem kalendarzowym, takie jak bieżący okres rozliczeniowy lub ostatnia faktura. Aby przejść odpowiednio do poprzedniego lub następnego okresu, skorzystaj z linków **< WSTECZ** i **DALEJ >** u góry menu. Na przykład kliknięcie linku **< WSTECZ** spowoduje przełączenie z okresu **Ostatnie 7 dni** do okresu **8–14 dni temu** lub **15–21 dni temu**.

![Selektor daty przedstawiający przykładowy wybór dla tego miesiąca](./media/quick-acm-cost-analysis/date-selector.png)

Analiza kosztów domyślnie przedstawia **skumulowane** koszty. Skumulowane koszty obejmują wszystkie koszty dla każdego dnia oraz poprzednich dni i umożliwiają tworzenie wciąż rosnącego widoku zagregowanych kosztów dziennych. Ten widok jest optymalizowany w celu pokazania trendów względem budżetu dla wybranego zakresu czasu.

Widok wykresu prognozy służy do identyfikowania potencjalnych naruszeń budżetu. Gdy istnieje możliwość naruszenia budżetu, przewidywane przekroczenie jest wyświetlane na czerwono. Na wykresie jest również widoczny symbol wskaźnika. Umieszczając wskaźnik myszy nad symbolem, można zobaczyć szacowaną datę naruszenia budżetu.

![Przykład pokazujący potencjalne naruszenie budżetu](./media/quick-acm-cost-analysis/budget-breach.png)

Istnieje również widok **dzienny** przedstawiający koszty danego dnia. Widok dzienny nie zawiera trendu wzrostu. Widok został zaprojektowany z myślą o wyświetlaniu nieprawidłowości, gdy koszt gwałtownie wzrasta lub spada z dnia na dzień. W przypadku wybrania budżetu widok dzienny pokazuje również szacowany budżet na dany dzień.

Jeśli koszty dzienne stale przekraczają szacowany budżet dzienny, można oczekiwać przekroczenia kwoty budżetu miesięcznego. Szacowany budżet dzienny to metoda ułatwiająca wizualizowanie budżetu na niższym poziomie. W przypadku wahań kosztów dziennych porównanie szacowanego budżetu dziennego z budżetem miesięcznym jest mniej dokładne.

Oto widok dzienny ostatnich wydatków z włączoną prognozą wydatków.
![Widok dzienny przedstawiający przykładowe koszty dzienne dla bieżącego miesiąca](./media/quick-acm-cost-analysis/daily-view.png)

Po wyłączeniu prognozy wydatków nie widać przewidywanych wydatków w przyszłości. Ponadto podczas przeglądania kosztów dla ostatnich okresów prognoza kosztów nie pokazuje kosztów.

Ogólnie rzecz biorąc, w ciągu 8–12 godzin można spodziewać się danych lub powiadomień dotyczących wykorzystanych zasobów.


**Grupuj według** typowych właściwości, aby dzielić koszty i identyfikować ich najważniejsze składniki. Na przykład aby grupować według tagów zasobów, wybierz klucz tagu, według którego chcesz grupować. Koszty zostaną podzielone według poszczególnych wartości tagu oraz dodatkowego segmentu dla zasobów, do których nie zastosowano tego tagu.

Większość [zasobów platformy Azure obsługuje tagowanie](../../azure-resource-manager/management/tag-support.md). Jednak niektóre tagi nie są dostępne na stronie Zarządzanie kosztami i rozliczenia. Ponadto nie są obsługiwane tagi grup zasobów. Obsługa tagów dotyczy użycia zgłaszanego *po tym*, gdy tag został zastosowany do zasobu. Tagi nie są stosowane wstecznie dla celów zestawień kosztów.

Obejrzyj film wideo [How to review tag policies with Azure Cost Management](https://www.youtube.com/watch?v=nHQYcYGKuyw) (Jak przeglądać zasady tagów za pomocą usługi Azure Cost Management), aby dowiedzieć się więcej o korzystaniu z zasad tagów platformy Azure w celu zwiększenia widoczności danych dotyczących kosztów.

Oto widok kosztów usług platformy Azure dla bieżącego miesiąca.

![Skumulowany, zgrupowany widok dzienny przedstawiający przykładowe koszty usług platformy Azure w ostatnim miesiącu](./media/quick-acm-cost-analysis/grouped-daily-accum-view.png)

Domyślnie analiza kosztów pokazuje wszystkie koszty użycia i zakupów, które będą widoczne na fakturze, w miarę ich naliczania. Ta funkcja nosi również nazwę **rzeczywistego kosztu**. Wyświetlanie rzeczywistego kosztu idealnie nadaje się do uzgadniania faktury. Jednak skokowe wzrosty kosztu zakupów mogą być alarmujące, gdy pilnujesz anomalii w wydatkach i innych zmian kosztów. Aby wyrównać wzrosty wynikające z kosztów zakupu rezerwacji, przełącz się na **koszt zamortyzowany**.

![Zmiana kosztu rzeczywistego na zamortyzowany w celu wyświetlenia zakupów rezerwacji rozłożonych w całym okresie i przydzielonych do zasobów, które używały rezerwacji](./media/quick-acm-cost-analysis/metric-picker.png)

Koszt zamortyzowany dzieli zakupy rezerwacji na dzienne fragmenty i rozkłada je w czasie trwania terminu rezerwacji. Na przykład zamiast zakupu na kwotę 365 USD w dniu 1 stycznia, każdego dnia w okresie od 1 stycznia do 31 grudnia będzie wyświetlany zakup w wysokości 1 USD. Oprócz podstawowej amortyzacji te koszty są również ponownie przydzielane i kojarzone przy użyciu określonych zasobów, które używały rezerwacji. Na przykład jeśli opłata dzienna w wysokości 1 USD została podzielona między dwie maszyny wirtualne, każdego dnia będą widoczne dwie opłaty w wysokości 0,50 USD. Jeśli część rezerwacji nie została wykorzystana danego dnia, zobaczysz jedną opłatę 0,50 USD skojarzoną z odpowiednią maszyną wirtualną i drugą opłatę 0,50 USD typu `UnusedReservation`. Pamiętaj, że koszty niewykorzystanych rezerwacji są widoczne tylko w przypadku wyświetlania kosztu zamortyzowanego.

W związku ze zmianą sposobu reprezentowania kosztów trzeba pamiętać, że w widokach kosztów rzeczywistych i zamortyzowanych będą wyświetlane różne sumy. Ogólnie rzecz biorąc, podczas przeglądania kosztów zamortyzowanych suma kosztów w miesiącach z zakupem rezerwacji będzie się zmniejszała, a w miesiącach po zakupie rezerwacji — zwiększała. Amortyzacja jest dostępna tylko w przypadku zakupów rezerwacji i w tej chwili nie dotyczy zakupów w witrynie Azure Marketplace.

Na poniższej ilustracji pokazano nazwy grup zasobów. Możesz grupować według tagów, aby wyświetlić sumę kosztów każdego tagu, lub użyć widoku **Koszt według zasobu**, aby wyświetlić wszystkie tagi dla określonego zasobu.

![Pełne dane dla bieżącego widoku, w którym są wyświetlane nazwy grup zasobów](./media/quick-acm-cost-analysis/full-data-set.png)

Podczas grupowania kosztów według konkretnego atrybutu 10 największych składników kosztów jest pokazanych od najwyższego do najniższego. Jeśli jest ich więcej niż 10, pokazanych jest dziewięć największych składników kosztów oraz grupa **Inne**, która przedstawia wszystkie pozostałe grupy łącznie. W przypadku grupowania według tagów wyświetlana jest także grupa **Bez tagów** dla kosztów, w których nie zastosowano klucza tagu. Grupa **Bez tagów** jest zawsze wyświetlana jako ostatnia, nawet jeśli koszty bez tagów są wyższe niż koszty z tagami. Jeśli istnieje 10 lub więcej wartości tagów, koszty bez tagów będą częścią grupy **Inne**. Przejdź do widoku tabeli i zmień poziom szczegółowości na **Brak**, aby wyświetlić wszystkie wartości sklasyfikowane od najwyższego do najniższego kosztu.

Klasyczne maszyny wirtualne, sieci i zasoby magazynu nie udostępniają szczegółowych danych dotyczących rozliczeń. Są one scalane jako **Usługi klasyczne** podczas grupowania kosztów.

Wykresy przestawne w obszarze wykresu głównego przedstawiają różne sposoby grupowania, które dają szerszy obraz kosztów ogólnych dla wybranego przedziału czasu i filtrów. Wybierz właściwość lub tag, aby wyświetlić zagregowane koszty według dowolnego wymiaru.

![Przykład przedstawiający wykresy przestawne](./media/quick-acm-cost-analysis/pivot-charts.png)

Możesz wyświetlić pełny zestaw danych dla dowolnego widoku. Wszelkie zastosowane opcje lub filtry wpływają na prezentowane dane. Aby wyświetlić pełny zestaw danych, wybierz listę **typ wykresu**, a następnie wybierz widok **tabeli**.

![Dane dla bieżącego widoku w widoku tabeli](./media/quick-acm-cost-analysis/chart-type-table-view.png)


## <a name="understanding-grouping-and-filtering-options"></a>Omówienie opcji grupowania i filtrowania

W poniższej tabeli wymieniono niektóre z najpopularniejszych opcji grupowania i filtrowania oraz ich przeznaczenie.

| Właściwość | Kiedy stosować | Uwagi |
| --- | --- | --- |
| **Strefy dostępności** | Podział kosztów platformy AWS według strefy dostępności. | Dotyczy tylko zakresów platformy AWS i grup zarządzania. Dane platformy Azure nie zawierają strefy dostępności i będą wyświetlane jako **Nie dotyczy**. |
| **Okres rozliczeniowy** | Podział kosztów płatności zgodnie z rzeczywistym użyciem według miesiąca, w którym zostały (lub zostaną) zafakturowane. | Aby uzyskać dokładną reprezentację zafakturowanych płatności zgodnie z rzeczywistym użyciem, użyj właściwości **Okres rozliczeniowy**. W przypadku filtrowania do niestandardowego zakresu dat uwzględnij 2 dodatkowe dni przed okresem rozliczeniowym i po nim. Ograniczenie do dokładnych dat okresu rozliczeniowego nie będzie pasować do faktury. Zostaną wyświetlone koszty ze wszystkich faktur w okresie rozliczeniowym. Aby filtrować do określonej faktury, użyj właściwości **Identyfikator faktury**. Dotyczy tylko subskrypcji płatnych zgodnie z rzeczywistym użyciem, ponieważ umowy EA i umowy klienta Microsoft (MCA) są rozliczane według miesięcy kalendarzowych. Konta umów EA/MCA mogą używać miesięcy kalendarzowych w selektorze daty lub miesięcznego stopnia szczegółowości do osiągnięcia tego samego celu. |
| **Typ opłaty** | Podział kosztów użycia, zakupów, zwrotów i niewykorzystanych rezerwacji. | Zakupy rezerwacji i zwroty są dostępne tylko w przypadku korzystania z kosztów rzeczywistych, a nie w przypadku korzystania z kosztów zamortyzowanych. Koszty niewykorzystanych rezerwacji są dostępne tylko podczas przeglądania kosztów zamortyzowanych. |
| **Dział** | Podział kosztów według działów EA. | Dostępne tylko w przypadku umów EA i grup zarządzania. Subskrypcje płatne zgodnie z rzeczywistym użyciem nie mają działu i będą wyświetlane jako **Nie dotyczy** lub **nieprzypisane**. |
| **Konto rejestracji** | Podział kosztów według właściciela konta EA. | Dostępne tylko w przypadku kont rozliczeniowych, działów i grup zarządzania umowy EA. Subskrypcje płatne zgodnie z rzeczywistym użyciem nie mają kont rejestracji umowy EA i będą wyświetlane jako **Nie dotyczy** lub **nieprzypisane**. |
| **Częstotliwość** | Podział kosztów opartych na użyciu, jednorazowych i cyklicznych. | |
| **Identyfikator faktury** | Podział kosztów według naliczonej faktury. | Nienaliczone opłaty nie mają jeszcze identyfikatora faktury, a koszty umowy EA nie zawierają szczegółów faktury i są wyświetlane jako **Nie dotyczy**.  |
| **Miernik** | Podział kosztów według miernika użycia. | Zakupy i użycie witryny Marketplace będą wyświetlane jako **Nie dotyczy**. Zapoznaj się z właściwością **Typ opłaty**, aby zidentyfikować zakupy, oraz **Typ wydawcy**, aby zidentyfikować opłaty z witryny Marketplace. |
| **Operacja** | Podział kosztów platformy AWS według operacji. | Dotyczy tylko zakresów platformy AWS i grup zarządzania. Dane platformy Azure nie zawierają operacji i będą wyświetlane jako **Nie dotyczy**. Zamiast nich użyj właściwości **Miernik**. |
| **Model cen** | Podział kosztów na żądanie, rezerwacji lub użycia na miejscu. | Zakupy są wyświetlane jako **Na żądanie**. Jeśli widoczny jest komunikat **Nie dotyczy**, grupuj według **rezerwacji**, aby ustalić, czy użycie jest rezerwacją, czy użyciem na żądanie, oraz **typu opłaty** w celu zidentyfikowania zakupów.
| **Dostawca** | Podział kosztów według platformy AWS i platformy Azure. | Dostępne tylko w przypadku grup zarządzania. |
| **Typ wydawcy** | Podział kosztów platformy AWS, platformy Azure i witryny Marketplace. |  |
| **Rezerwacja** | Podział kosztów według rezerwacji. | Każde użycie lub wszystkie zakupy, które nie są skojarzone z rezerwacją, będą wyświetlane jako **Nie dotyczy**. Grupuj według **typu wydawcy**, aby zidentyfikować inne zakupy na platformie Azure, na platformie AWS lub w witrynie Marketplace. |
| **Zasób** | Podział kosztów według zasobu. | Zakupy są wyświetlane jako **Nie dotyczy**, ponieważ są stosowane na koncie rozliczeniowym umowy EA/płatności zgodnie z rzeczywistym użyciem lub na poziomie profilu rozliczeniowego umowy MCA i nie są skojarzone z określonym zasobem. Grupuj według **typu wydawcy**, aby zidentyfikować inne zakupy na platformie Azure, na platformie AWS lub w witrynie Marketplace. |
| **Grupa zasobów** | Podział kosztów według grupy zasobów. | Zakupy, zasoby dzierżawy nieskojarzone z subskrypcjami, zasoby subskrypcji niewdrożone w grupie zasobów i zasoby klasyczne nie mają grupy zasobów i będą wyświetlane jako **inne**, **usługi klasyczne**, **$system** lub **Nie dotyczy**. |
| **Typ zasobu** | Podział kosztów według typu zasobu. | Zakupy i usługi klasyczne nie mają typu zasobu usługi Azure Resource Manager i będą wyświetlane jako **inne**, **usługi klasyczne** lub **Nie dotyczy**. |
| **Lokalizacja zasobu** | Podział kosztów według lokalizacji lub regionu. | Zakupy i użycie witryny Marketplace mogą być wyświetlane jako **nieprzypisane**, **nieznane**, **niezamapowane** lub **Nie dotyczy**. |
| **Nazwa usługi** lub **Kategoria miernika** | Podział kosztów według usługi platformy Azure. | Zakupy i użycie witryny Marketplace będą wyświetlane jako **Nie dotyczy** lub **nieprzypisane**. |
| **Warstwa usługi** lub **Podkategoria miernika** | Podział kosztów według podklasyfikacji miernika użycia platformy Azure. | Zakupy i użycie witryny Marketplace będą wyświetlane jako **Nie dotyczy** lub **nieprzypisane**. |
| **Subskrypcja** | Podział kosztów według subskrypcji platformy Azure i połączonego konta platformy AWS. | Zakupy i zasoby dzierżawy mogą być wyświetlane jako **Nie dotyczy**. |
| **Tag** | Podział kosztów według wartości tagów dla określonego klucza tagu. | Tagi są niedostępne w przypadku zakupów, zasobów dzierżawy nieskojarzonych z subskrypcjami, zasobów subskrypcji niewdrożonych w grupie zasobów lub zasobów klasycznych. Pamiętaj, że niektóre usługi nie zawierają tagów w danych dotyczących użycia. Dowiedz się więcej na temat [obsługi tagów dla każdego typu zasobu](https://docs.microsoft.com/azure/azure-resource-manager/management/tag-support). |

Aby uzyskać więcej informacji na temat terminów, zobacz [Omówienie terminów używanych w pliku użycia i opłat platformy Azure](../understand/understand-usage.md).


## <a name="saving-and-sharing-customized-views"></a>Zapisywanie i udostępnianie dostosowanych widoków

Dostosowane widoki można zapisywać i udostępniać innym osobom, przypinając analizę kosztów do pulpitu nawigacyjnego witryny Azure Portal lub kopiując link do analizy kosztów.

Aby przypiąć analizę kosztów, wybierz ikonę pinezki w prawym górnym rogu. Przypięcie analizy kosztów spowoduje zapisanie tylko głównego wykresu lub widoku tabeli. Udostępnij pulpit nawigacyjny, aby umożliwić innym użytkownikom dostęp do kafelka. Pamiętaj, że spowoduje to tylko udostępnienie konfiguracji pulpitu nawigacyjnego i nie udzieli innym użytkownikom dostępu do danych źródłowych. Jeśli nie masz dostępu do kosztów, ale masz dostęp do udostępnionego pulpitu nawigacyjnego, zobaczysz komunikat „odmowa dostępu”.

Aby udostępnić link do analizy kosztów, wybierz pozycję **Udostępnij** w górnej części bloku. Zostanie wyświetlony niestandardowy adres URL, który otwiera ten konkretny widok dla określonego zakresu. Jeśli nie masz dostępu do kosztów i uzyskasz ten adres URL, zobaczysz komunikat „odmowa dostępu”.

Aby dowiedzieć się więcej o udzielaniu dostępu do kosztów dla każdego obsługiwanego zakresu, przejrzyj artykuł [Omówienie zakresów i praca z nimi](understand-work-scopes.md).

## <a name="automation-and-offline-analysis"></a>Automatyzacja i analiza w trybie offline

Czasami musisz pobrać dane do dalszej analizy, scalić je z własnymi danymi lub zintegrować ze swoimi systemami. Usługa Cost Management oferuje kilka różnych opcji. Na początek, jeśli potrzebujesz podsumowania wysokiego poziomu ad hoc, takiego jak to, które otrzymujesz w ramach analizy kosztów, utwórz potrzebny widok. Następnie pobierz go, wybierając pozycję **Eksportuj**, a następnie pozycję **Pobierz dane do pliku CSV** lub **Pobierz dane do programu Excel**. Pobranie danych do programu Excel zapewnia dodatkowy kontekst w widoku użytym do wygenerowania pobierania, taki jak zakres, konfiguracja zapytania, suma i data wygenerowania.

Jeśli potrzebujesz pełnego, niezagregowanego zestawu danych, pobierz go z konta rozliczeniowego. Następnie na liście usług w okienku nawigacji po lewej stronie portalu przejdź do pozycji **Zarządzanie kosztami i rozliczenia**. Wybierz konto rozliczeniowe, jeśli ma to zastosowanie. Przejdź do pozycji **Użycie + opłaty**, a następnie wybierz ikonę **Pobierz** dla żądanego okresu rozliczeniowego.

Aby zautomatyzować otrzymywanie danych dotyczących kosztów, zastosuj podobne podejście. Użyj [interfejsu API Query](/rest/api/cost-management/query) do zaawansowanej analizy z dynamicznym filtrowaniem, grupowaniem i agregacją lub użyj [interfejsu API UsageDetails](/rest/api/consumption/usageDetails), aby uzyskać pełny, niezagregowany zestaw danych. Ogólnie dostępna wersja tych interfejsów API to 2019-01-01. Aby uzyskać dostęp do wersji zapoznawczej rezerwacji i zakupów w witrynie Marketplace w ramach tych interfejsów API, użyj wersji **2019-04-01-preview**.

Na przykład poniżej przedstawiono zagregowany widok kosztów zamortyzowanych podzielony według typu opłaty (użycie, zakup lub zwrot), typu wydawcy (Azure lub Marketplace), grupy zasobów (pusta w przypadku zakupów) i rezerwacji (pusta, jeśli nie dotyczy).

```
POST https://management.azure.com/{scope}/providers/Microsoft.CostManagement/query?api-version=2019-04-01-preview
Content-Type: application/json

{
  "type": "AmortizedCost",
  "timeframe": "Custom",
  "timePeriod": { "from": "2019-04-01", "to": "2019-04-30" },
  "dataset": {
    "granularity": "None",
    "aggregation": {
      "totalCost": { "name": "PreTaxCost", "function": "Sum" }
    },
    "grouping": [
      { "type": "dimension", "name": "ChargeType" },
      { "type": "dimension", "name": "PublisherType" },
      { "type": "dimension", "name": "Frequency" },
      { "type": "dimension", "name": "ResourceGroup" },
      { "type": "dimension", "name": "SubscriptionName" },
      { "type": "dimension", "name": "SubscriptionId" },
      { "type": "dimension", "name": "ReservationName" },
      { "type": "dimension", "name": "ReservationId" },
    ]
  },
}
```

A jeśli nie potrzebujesz agregacji i wolisz pełny, nieprzetworzony zestaw danych:

```
GET https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?metric=AmortizedCost&$filter=properties/usageStart+ge+'2019-04-01'+AND+properties/usageEnd+le+'2019-04-30'&api-version=2019-04-01-preview
```

Jeśli potrzebujesz kosztów rzeczywistych, aby wyświetlać zakupy w miarę ich naliczania, zmień atrybut **typu**/**miernika** na **ActualCost**. Aby uzyskać więcej informacji na temat tych interfejsów API, zobacz dokumentację interfejsów API [Query](/rest/api/cost-management/query) i [UsageDetails](/rest/api/consumption/usageDetails). Pamiętaj, że opublikowane dokumenty dotyczą wersji ogólnie dostępnej. Jednak oba działają tak samo w przypadku interfejsu API w wersji *2019-04-01-preview* poza nowym atrybutem typu/metryki i zmienionymi nazwami właściwości. (Więcej informacji na temat nazw właściwości znajduje się poniżej).

Interfejsy API usługi Cost Management działają we wszystkich zakresach powyżej zasobów: w grupie zasobów, subskrypcji i grupie zarządzania w przypadku funkcji RBAC na platformie Azure, na kontach rozliczeniowych umowy EA (rejestracjach), w działach i na kontach rejestracji w przypadku dostępu do portalu EA. Dowiedz się więcej o zakresach, w tym o sposobach określania identyfikatora zakresu lub zarządzania dostępem, korzystając z artykułu [Omówienie zakresów i praca z nimi](understand-work-scopes.md).

## <a name="next-steps"></a>Następne kroki

Przejdź do pierwszego samouczka, aby dowiedzieć się, jak tworzyć budżety i nimi zarządzać.

> [!div class="nextstepaction"]
> [Tworzenie budżetów i zarządzanie nimi](tutorial-acm-create-budgets.md)
