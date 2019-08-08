---
title: Szybki Start — Eksplorowanie kosztów platformy Azure dzięki analizie kosztów | Microsoft Docs
description: Ten przewodnik Szybki start ułatwia eksplorowanie i analizowanie kosztów organizacyjnych platformy Azure za pomocą funkcji analizy kosztów.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 06/11/2019
ms.topic: quickstart
ms.service: cost-management
manager: micflan
ms.custom: seodec18
ms.openlocfilehash: 2ca0c0741ea543b30695303d473389979f2e1045
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2019
ms.locfileid: "68855050"
---
# <a name="quickstart-explore-and-analyze-costs-with-cost-analysis"></a>Szybki start: Eksplorowanie i analizowanie kosztów za pomocą analizy kosztów

Aby poprawnie kontrolować i optymalizować koszty platformy Azure, trzeba zrozumieć, skąd pochodzą koszty w organizacji. Warto również wiedzieć, ile pieniędzy jest kosztem usług i w jakim są obsługiwane środowiska i systemy. Widoczność pełnego zakresu kosztów to kluczowy element wymagany do dokładnego zrozumienia wzorca wydatków organizacji. Wzorców wydatków można użyć do wymuszania mechanizmów kontroli kosztów, takich jak budżety.

Ten przewodnik Szybki start ułatwia eksplorowanie i analizowanie kosztów organizacyjnych przy użyciu funkcji analizy kosztów. Możesz wyświetlić zagregowane koszty według organizacji, aby dowiedzieć się, gdzie występują koszty w miarę upływu czasu, i zidentyfikować trendy wydatków. Zakumulowane koszty w czasie można wyświetlać w celu oszacowania miesięcznych, kwartalnych, a nawet rocznych trendów kosztów w odniesieniu do budżetu. Budżet pomaga działać zgodnie z ograniczeniami finansowymi. Budżet jest również używany do wyświetlania kosztów dziennych lub miesięcznych w celu odizolowania nieprawidłowości wydatków. Ponadto można pobrać dane bieżącego raportu do dalszej analizy lub do użycia w systemie zewnętrznym.

W tym przewodniku Szybki start zawarto informacje na temat wykonywania następujących czynności:

- Przeglądanie kosztów w obrębie analizy kosztów
- Dostosowywanie widoków kosztów
- Pobieranie danych analizy kosztów


## <a name="prerequisites"></a>Wymagania wstępne

Analiza kosztów obsługuje różne rodzaje typów kont platformy Azure. Aby wyświetlić pełną listę obsługiwanych typów kont, zobacz [Omówienie danych usługi Cost Management](understand-cost-mgt-data.md). Aby wyświetlić dane kosztów, potrzebujesz przynajmniej dostępu do odczytu dla Twojego konta platformy Azure.

Dla klientów [Umowy Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) musisz mieć przynajmniej dostęp do odczytu do co najmniej jednego z poniższych zakresów, aby wyświetlić dane kosztów.

- Konto rozliczeniowe
- Dział
- Konto rejestracji
- Grupa zarządzania
- Subscription
- Resource group

Aby uzyskać więcej informacji o przypisywaniu dostępu do danych Azure Cost Management, zobacz [Przypisywanie dostępu do danych](assign-access-acm-data.md).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

- Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

## <a name="review-costs-in-cost-analysis"></a>Przeglądanie kosztów w obrębie analizy kosztów

Aby sprawdzić koszty w analizie kosztów, Otwórz zakres w Azure Portal a następnie wybierz pozycję **Analiza kosztów** w menu. Na przykład przejdź do pozycji **subskrypcje**, wybierz subskrypcję z listy, a następnie wybierz pozycję **Analiza kosztów** w menu. Użyj **zakresu** pill, aby przełączyć się do innego zakresu w analizie kosztów. Aby uzyskać więcej informacji na temat zakresów, zobacz [Opis i współpraca z zakresami](understand-work-scopes.md).

Wybrany zakres jest używany w całym Cost Management, aby zapewnić konsolidację danych i kontrolować dostęp do informacji o kosztach. Gdy używasz zakresów, nie wybierasz równocześnie wielu z nich. Zamiast tego należy wybrać większy zakres, którego mogą dotyczyć inne, a następnie odfiltrować do zakresów zagnieżdżonych, które są potrzebne. Ta metoda jest ważna, ponieważ niektóre osoby mogą nie mieć dostępu do jednego zakresu nadrzędnego, który obejmuje wiele zakresów zagnieżdżonych.

Początkowy widok kosztów analizy obejmuje następujące obszary.

**Widok skumulowanego kosztu**: Przedstawia wstępnie zdefiniowaną konfigurację widoku analizy kosztów. Każdy widok obejmuje ustawienia zakres dat, stopień szczegółowości, grupowanie według i filtrowanie. Widok domyślny pokazuje skumulowane koszty bieżącego okresu rozliczeniowego, ale można je zmienić na inne wbudowane widoki. Aby uzyskać więcej informacji, zobacz [Dostosowywanie widoków kosztów](#customize-cost-views).

**Rzeczywisty koszt**: Pokazuje łączne koszty użycia i zakupu dla bieżącego miesiąca, gdy są naliczane i będą wyświetlane na rachunku.

**Prognoza**: Pokazuje łączną liczbę przewidywanych kosztów w wybranym okresie. (Prognoza jest w wersji zapoznawczej).

**Budżet**: Pokazuje planowany limit wydatków dla wybranego zakresu, jeśli jest dostępny.

**Skumulowany stopień szczegółowości**: Pokazuje łączną wartość zagregowanych kosztów na początku okresu rozliczeniowego. Po [utworzeniu budżetu](tutorial-acm-create-budgets.md) subskrypcji lub konta billingowego możesz szybko wyświetlić trend wydatków w odniesieniu do budżetu. Umieść kursor nad datą, aby wyświetlić skumulowany koszt w tym dniu.

**Wykresy przestawne (pierścieniowe)** : Zapewnij dynamiczne przestawne, dzieląc łączny koszt przez wspólny zestaw standardowych właściwości. Są one wyświetlane od największych do najmniejszych kosztów w bieżącym miesiącu. Wykresy przestawne można zmieniać w dowolnym momencie, wybierając inny element przestawny. Koszty są domyślnie klasyfikowane według usługi (kategorii licznika), lokalizacji (regionu) i zakresu podrzędnego. Na przykład konta rejestracji są w obszarze konta rozliczeń, grupy zasobów znajdują się w obszarze subskrypcje, a zasoby znajdują się w obszarze grupy zasobów.

![Początkowy widok analizy kosztów w witrynie Azure Portal](./media/quick-acm-cost-analysis/cost-analysis-01.png)

## <a name="customize-cost-views"></a>Dostosowywanie widoków kosztów

Analiza kosztów ma cztery wbudowane widoki zoptymalizowane pod kątem najpopularniejszych celów:

Widok | Odpowiedzi na pytania
--- | ---
Skumulowany koszt | Ile pozostało dotąd w tym miesiącu? Czy wydatki zmieściły się w budżecie?
Dzienny koszt | Czy wystąpiły wzrost kosztów dziennie w ciągu ostatnich 30 dni?
Koszt według usługi | Jak moje miesięczne użycie różni się od ostatnich trzech faktur?
Koszt według zasobu | Które zasoby są kosztowne do tej pory w tym miesiącu?

![Selektor widoku pokazujący przykład wyboru dla tego miesiąca](./media/quick-acm-cost-analysis/view-selector.png)

W większości przypadków będziesz jednak potrzebować dokładniejszej analizy. Dostosowywanie rozpoczyna się od wybrania daty w górnej części strony.

Analiza kosztów domyślnie przedstawia dane z bieżącego miesiąca. Użyj selektora dat, aby szybko przełączać się do wspólnych zakresów dat. Przykładami mogą być ostatnie siedem dni, ostatni miesiąc, bieżący rok lub niestandardowy zakres dat. Subskrypcje z płatnością zgodnie z rzeczywistym użyciem obejmują również zakresy dat na podstawie okresu rozliczeniowego, który nie jest powiązany z miesiącem kalendarzowym, na przykład bieżącym okresem rozliczeniowym lub ostatnią fakturą. Użyj linków **< poprzednie** i **Next >** w górnej części menu, aby przejść odpowiednio do poprzedniego lub następnego okresu. Na przykład **< poprzedni** zostanie przełączony z **ostatnich 7 dni** do **8-14 dni temu** lub **15-21 dni temu**.

![Selektor daty przedstawiający przykładowy wybór dla tego miesiąca](./media/quick-acm-cost-analysis/date-selector.png)

Analiza kosztów domyślnie przedstawia **skumulowane** koszty. Koszty skumulowane obejmują wszystkie koszty dla każdego dnia oraz poprzednie dni, co pozwala na stale rosnący wgląd w dzienne zagregowane koszty. Ten widok jest optymalizowany w celu pokazania trendów względem budżetu dla wybranego zakresu czasu.

Widok Wykres prognozy służy do identyfikowania potencjalnych naruszeń budżetu. W przypadku wystąpienia potencjalnego naruszenia budżetu przewidywane przekroczenie jest pokazane na czerwono. Symbol wskaźnika jest również pokazywany na wykresie. Umieszczenie wskaźnika myszy nad symbolem pokazuje szacowaną datę naruszenia budżetu.

![Przykład pokazujący potencjalne naruszenie budżetu](./media/quick-acm-cost-analysis/budget-breach.png)

Istnieje również widok **dzienny** przedstawiający koszty danego dnia. Widok dzienny nie zawiera trendu wzrostu. Widok został zaprojektowany z myślą o wyświetlaniu nieprawidłowości, gdy koszt gwałtownie wzrasta lub spada z dnia na dzień. W przypadku wybrania budżetu dzienny widok zawiera również szacunkowy budżet dzienny.

Gdy dzienne koszty są spójne powyżej szacowanego dziennego budżetu, można oczekiwać, że zostanie przekroczony miesięczny budżet. Szacowany dzienny budżet jest środkiem ułatwiającym wizualizację budżetu na niższym poziomie. W przypadku wahań kosztów dziennych porównanie szacowanego budżetu dziennego z budżetem miesięcznym jest mniej dokładne.

Oto dzienny widok ostatnich wydatków dzięki włączonej prognozie wydatków.
![Widok dzienny przedstawiający przykład dziennych kosztów dla bieżącego miesiąca](./media/quick-acm-cost-analysis/daily-view.png)

Po wyłączeniu prognozy wydatków nie widzisz prognozowanych wydatków w przyszłości. Ponadto podczas przeglądania kosztów dla ostatnich okresów prognoza kosztów nie pokazuje kosztów.

Ogólnie rzecz biorąc, można oczekiwać, że dane lub powiadomienia dotyczące zużytych zasobów są widoczne w ciągu 8 do 12 godzin.


**Grupuj według** wspólnych właściwości, aby podzielić koszty i identyfikować najważniejszych współautorów. Aby grupować według tagów zasobów, na przykład wybierz klucz tagu, według którego chcesz grupować. Koszt jest podzielony na poszczególne wartości tagów, z dodatkowym segmentem dla zasobów, które nie mają zastosowanych tagów.

Większość [zasobów platformy Azure obsługuje tagowanie](../azure-resource-manager/tag-support.md). Niektóre Tagi nie są jednak dostępne w Cost Management i rozliczeń. Ponadto nie są obsługiwane tagi grup zasobów. Rozwiązanie Cost Management obsługuje tylko tagi zasobów od daty bezpośredniego zastosowania tagów do zasobu. Zapoznaj się z [tematem jak przeglądać zasady tagów za pomocą Azure Cost Management](https://www.youtube.com/watch?v=nHQYcYGKuyw) wideo, aby dowiedzieć się więcej o korzystaniu z zasad tagów platformy Azure w celu zwiększenia widoczności danych kosztów

Oto widok kosztów usługi platformy Azure dla bieżącego miesiąca.

![Skumulowany, zgrupowany widok dzienny przedstawiający przykładowe koszty usług platformy Azure w ostatnim miesiącu](./media/quick-acm-cost-analysis/grouped-daily-accum-view.png)

Domyślnie analiza kosztów pokazuje wszystkie koszty użycia i zakupu w miarę ich naliczania i będzie wyświetlana na fakturze, znane także jako **rzeczywisty koszt**. Wyświetlanie rzeczywistego kosztu jest idealne do uzgadniania faktury. Jednak zakup skoków w koszcie może być alarmem, gdy nie ma żadnego efektu, aby zachować anomalie i inne zmiany kosztów. Aby spłaszczyć wzrosty wynikające z zastrzeżeń zakupu, należy przełączyć się na **amortyzowany koszt**. 

![Zmiana między rzeczywistym i amortyzowanym kosztem w celu wyświetlenia rezerwacji zakupów rozmieszczonych w całym okresie i przydzielenia do zasobów, które używały rezerwacji](./media/quick-acm-cost-analysis/metric-picker.png)

Amortyzowany koszt powoduje zerwanie zakupów rezerwacji w codzienne fragmenty i rozłożenie ich w czasie trwania okresu rezerwacji. Na przykład zamiast $1 korzystania z $365 zakupu w dniu 1 stycznia zobaczysz od 1 stycznia do 31 grudnia. Oprócz podstawowej amortyzacji te koszty są również ponownie przydzielone i skojarzone przy użyciu określonych zasobów, które używały rezerwacji. Na przykład, jeśli opłata dzienna $1 została podzielona między dwie maszyny wirtualne, zobaczysz $2 0,50 opłaty za dzień. Jeśli część rezerwacji nie zostanie wykorzystana na dzień, zobaczysz opłatę $1 0,50 skojarzoną z odpowiednią maszyną wirtualną i inną opłatą $0,50 z typem `UnusedReservation`opłat. Należy pamiętać, że nieużywane koszty rezerwacji mogą być widoczne tylko w przypadku wyświetlania amortyzowanego kosztu.

Ze względu na zmianę sposobu reprezentowania kosztów należy zauważyć, że rzeczywiste wartości w widokach koszt rzeczywisty i amortyzowany są różne. Ogólnie rzecz biorąc, łączny koszt miesięcy z zakupem rezerwacji będzie zmniejszany podczas wyświetlania amortyzowanych kosztów i miesięcy po zakupie zakupu. Amortyzacja jest dostępna tylko w przypadku zakupów rezerwacji i nie dotyczy zakupów w portalu Azure Marketplace.

Na poniższej ilustracji przedstawiono nazwy grup zasobów. Możesz grupować według tagów, aby wyświetlić łączne koszty na tag, lub użyć widoku **Koszt według zasobów** , aby wyświetlić wszystkie Tagi dla określonego zasobu.

![Pełne dane dla bieżącego widoku, w którym są wyświetlane nazwy grup zasobów](./media/quick-acm-cost-analysis/full-data-set.png)

Gdy grupujesz koszty według określonego atrybutu, 10 głównych współpracowników jest pokazywanych od najwyższego do najniższego. Jeśli jest więcej niż 10, pierwsze dziewięciu współautorów kosztów są pokazywane z **innymi** grupami, które reprezentują wszystkie pozostałe połączone grupy. Gdy grupujesz według tagów, nieoznakowana grupa pojawia się w przypadku kosztów, w których nie zastosowano klucza tagu. Nieoznakowany jest zawsze ostatni, nawet jeśli nieoznakowane koszty są wyższe niż oznakowane koszty. Nieoznakowane koszty będą częścią **innych**, jeśli istnieją co najmniej 10 wartości tagów. Przejdź do widoku tabeli i zmień poziom szczegółowości na **wartość Brak** , aby wyświetlić wszystkie wartości sklasyfikowane od najwyższego do najniższego kosztu.

Klasyczne maszyny wirtualne, sieci i zasoby magazynu nie udostępniają szczegółowych danych dotyczących rozliczeń. Są one scalane jako **Usługi klasyczne** podczas grupowania kosztów.

Wykresy przestawne na wykresie głównym przedstawiają różne grupowania, które zapewniają szersze zdjęcie ogólnych kosztów w wybranym okresie i filtrach. Wybierz właściwość lub tag, aby wyświetlić zagregowane koszty według dowolnego wymiaru.

![Przykład przedstawiający wykresy przestawne](./media/quick-acm-cost-analysis/pivot-charts.png)

Możesz wyświetlić pełny zestaw danych dla dowolnego widoku. Wybrane opcje lub filtry mają wpływ na prezentowane dane. Aby wyświetlić pełny zestaw danych, wybierz listę **Typ wykresu** , a następnie wybierz pozycję Widok **tabeli** .

![Dane dla bieżącego widoku w widoku tabeli](./media/quick-acm-cost-analysis/chart-type-table-view.png)


## <a name="understanding-grouping-and-filtering-options"></a>Omówienie opcji grupowania i filtrowania

W poniższej tabeli wymieniono niektóre z najpopularniejszych opcji grupowania i filtrowania oraz kiedy należy ich używać.

| Właściwość | Kiedy stosować |
| --- | --- |
| **Okres rozliczeniowy** | Rozbicie kosztów według miesiąca faktury. Ta opcja jest ważna w przypadku subskrypcji z opcją płatność zgodnie z rzeczywistym użyciem i tworzenia/testowania, które nie są powiązane z miesiącami kalendarzowymi. Konta EA/MCA mogą używać miesięcy kalendarzowych w selektorze dat lub w celu osiągnięcia tego samego celu. |
| **Typ opłaty** | Przerwij użycie, zakup, zwrot i niewykorzystane koszty rezerwacji. Zakup rezerwacji i zwroty są dostępne tylko w przypadku korzystania z kosztów akcji, a nie przy korzystaniu z amortyzowanych kosztów. Niewykorzystane koszty rezerwacji są dostępne tylko w przypadku ponoszenia kosztów z amortyzacją. |
| **Chmura** | Podziel koszty na AWS i platformę Azure. Koszty AWS są dostępne tylko z grup zarządzania, zewnętrznych kont rozliczeń i subskrypcji zewnętrznych. |
|  / **Sekcja faktury** działu | Rozbicie kosztów według działu EA lub faktury MCA. Ta opcja jest dostępna tylko w przypadku kont rozliczania EA/MCA i profilów rozliczania MCA. |
| **Konto rejestracji** | Podziel koszty na właściciela konta EA. Ta opcja jest dostępna tylko w przypadku kont i działów rozliczeniowych EA. |
| **Częstotliwość** | Podział kosztów opartych na użyciu, jednorazowych i cyklicznych. |
| **Wyższy** | Rozbicie kosztów według licznika użycia platformy Azure. Ta opcja jest dostępna tylko w przypadku użycia platformy Azure. Wszystkie zakupy i użycie w witrynie Marketplace będą wyświetlane jako nieokreślone lub **nieprzypisane**. |
| **Typ wydawcy** | Podziel się kosztami AWS, Azure i Marketplace. |
| **Przyszł** | Rozbicie kosztów według rezerwacji. Żadne użycie, które nie zawiera zastrzeżenia, będzie wyświetlanejako nieokreślony. |
| **Zasób** | Podziel koszty według zasobu. Wszystkie zakupy będą wyświetlane jako **nieokreślone**, ponieważ są stosowane na koncie rozliczania EA/PAYG lub na poziomie profilu rozliczania MCA.  |
| **Grupa zasobów** | Podziel koszty według grupy zasobów. Ta opcja jest dostępna tylko dla nieklasycznych zastosowań. Klasyczne użycie zasobów będzie widoczne jako **inne**, a zakupy będą widoczne jako **nieokreślone**. |
| **Typ zasobu** | Podziel koszty według typu zasobu. Ta opcja jest dostępna tylko dla nieklasycznych zastosowań. Klasyczne użycie zasobów będzie widoczne jako **inne**, a zakupy będą widoczne jako **nieokreślone**. |
| **Nazwa usługi** lub **Kategoria licznika** | Podziel koszt według usługi platformy Azure. Ta opcja jest dostępna tylko w przypadku użycia platformy Azure. Wszystkie zakupy i użycie w witrynie Marketplace będą wyświetlane jako nieokreślone lub **nieprzypisane**. |
| **Podkategoria** **warstwy usług** lub miernika | Obniżenie kosztów według podklasyfikacji licznika użycia platformy Azure. Ta opcja jest dostępna tylko w przypadku użycia platformy Azure. Wszystkie zakupy i użycie w witrynie Marketplace będą wyświetlane jako nieokreślone lub **nieprzypisane**. |
| **Subskrypcja** | Podziel koszty według subskrypcji. Wszystkie zakupy są wyświetlanejako nieokreślone. |
| **Tag** | Podziel koszty na wartości tagów dla określonego klucza tagu. |

Aby uzyskać więcej informacji na temat terminów, zobacz [Opis terminów używanych w pliku użycia i opłat na platformie Azure](../billing/billing-understand-your-usage.md).


## <a name="saving-and-sharing-customized-views"></a>Zapisywanie i Udostępnianie widoków niestandardowych

Zapisuj i udostępniaj widoki niestandardowe innym osobom, przypinając analizę kosztów do pulpitu nawigacyjnego Azure Portal lub kopiując łącze do analizy kosztów. 

Aby przypiąć analizę kosztów, wybierz ikonę pinezki w prawym górnym rogu. Przypinanie analizy kosztów spowoduje zapisanie tylko głównego wykresu lub widoku tabeli. Udostępnij pulpit nawigacyjny, aby umożliwić innym użytkownikom dostęp do kafelka. Należy pamiętać, że ta wartość udostępnia tylko konfigurację pulpitu nawigacyjnego i nie udziela innym dostępu do danych źródłowych. Jeśli nie masz dostępu do kosztów, ale masz dostęp do udostępnionego pulpitu nawigacyjnego, zobaczysz komunikat "odmowa dostępu".

Aby udostępnić link do analizy kosztów, wybierz pozycję **Udostępnij** w górnej części bloku. Zostanie wyświetlony niestandardowy adres URL, który otwiera ten konkretny widok dla określonego zakresu. Jeśli nie masz dostępu do kosztów i uzyskasz ten adres URL, zobaczysz komunikat "odmowa dostępu". 

Aby dowiedzieć się więcej o udzielaniu dostępu do kosztów dla każdego z obsługiwanych zakresów, przejrzyj [Opis i pracuj z zakresami](understand-work-scopes.md).

## <a name="automation-and-offline-analysis"></a>Automatyzacja i analiza w trybie offline

Czasami trzeba będzie pobrać dane do dalszej analizy, scalić je ze swoimi danymi lub zintegrować je z własnymi systemami. Cost Management oferuje kilka różnych opcji. Jako punkt początkowy, jeśli potrzebujesz podsumowania wysokiego poziomu ad hoc, takiego jak to, co otrzymujesz w analizie kosztów, Utwórz potrzebny widok. Następnie pobierz go, wybierając pozycję **Eksportuj** i wybierając pozycję **Pobierz dane do pliku CSV** , lub **Pobierz dane do programu Excel**. Pobieranie z programu Excel zawiera dodatkowy kontekst w widoku używanym do generowania pobierania, taki jak zakres, konfiguracja zapytania, suma i Data wygenerowania.

Jeśli potrzebujesz pełnego, niezagregowanego zestawu danych, Pobierz go z konta rozliczeniowego. Następnie na liście usług w lewym okienku nawigacji portalu przejdź do pozycji **Cost Management + rozliczenia**. Wybierz konto rozliczeniowe, jeśli ma zastosowanie. Przejdź do pozycji **użycie i opłaty**, a następnie wybierz ikonę **pobierania** dla żądanego okresu rozliczeniowego.

W celu zautomatyzowania otrzymywania danych kosztów należy zastosować podobne podejście. Za pomocą [interfejsu API zapytań](/rest/api/cost-management/query) można bogatszej analizy z filtrowaniem dynamicznym, grupowaniem i agregacją lub użyć [interfejsu API UsageDetails](/rest/api/consumption/usageDetails) dla pełnego, niezagregowanego zestawu danych. Wersja ogólna dostępności tych interfejsów API to 2019-01-01. Skorzystaj z **2019-04-01 — wersja** zapoznawcza, aby uzyskać dostęp do wersji zapoznawczej rezerwacji i zakupów w portalu Marketplace w ramach tych interfejsów API. 

Poniżej znajduje się na przykład Zagregowany widok kosztów amortyzowanych podzielony według typu opłaty (użycie, zakup lub zwrot), typ wydawcy (Azure lub Marketplace), Grupa zasobów (pusta dla zakupów) i rezerwacja (wartość pusta, jeśli nie dotyczy).

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

Jeśli potrzebujesz rzeczywistych kosztów, aby pokazać zakupy w miarę ich naliczania, Zmień **Typ**/**metryki** na **ActualCost**. Aby uzyskać więcej informacji na temat tych interfejsów API, zobacz dokumentację interfejsu API [zapytań](/rest/api/cost-management/query) i [UsageDetails](/rest/api/consumption/usageDetails) . Zwróć uwagę na to, że opublikowane dokumenty są przeznaczone dla wersji GA. Jednak obie te działania działają tak samo w przypadku wersji interfejsu API *2019-04-01-Preview* poza nowym atrybutem typu/metryki i zmienionymi nazwami właściwości. (Więcej informacji na temat nazw właściwości znajduje się poniżej).
 
Cost Management interfejsy API działają między wszystkimi zakresami powyżej zasobów: grupą zasobów, subskrypcją i grupą zarządzania za pośrednictwem dostępu do usługi Azure RBAC, kontami rozliczeniowymi EA (rejestracje), działów i kont rejestracji za pośrednictwem dostępu do portalu EA. Dowiedz się więcej o zakresach, w tym o sposobach określania identyfikatora zakresu lub zarządzaniu dostępem, w temacie [Omówienie i współpraca z zakresami](understand-work-scopes.md).

## <a name="next-steps"></a>Kolejne kroki

Przejdź do pierwszego samouczka, aby dowiedzieć się, jak tworzyć budżety i nimi zarządzać.

> [!div class="nextstepaction"]
> [Tworzenie budżetów i zarządzanie nimi](tutorial-acm-create-budgets.md)
