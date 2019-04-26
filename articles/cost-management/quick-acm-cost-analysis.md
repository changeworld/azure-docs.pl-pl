---
title: Szybki start — eksplorowanie kosztów platformy Azure za pomocą analizy kosztów | Microsoft Docs
description: Ten przewodnik Szybki start ułatwia eksplorowanie i analizowanie kosztów organizacyjnych platformy Azure za pomocą funkcji analizy kosztów.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/13/2019
ms.topic: quickstart
ms.service: cost-management
manager: dougeby
ms.custom: seodec18
ms.openlocfilehash: 55407ec1846a0fe2eb037756dc2e97d8b05e7330
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60312313"
---
# <a name="quickstart-explore-and-analyze-costs-with-cost-analysis"></a>Szybki start: Poznawanie i analizowanie kosztów za pomocą funkcji Analiza kosztów

Aby poprawnie kontrolować i optymalizować koszty platformy Azure, trzeba zrozumieć, skąd pochodzą koszty w organizacji. Warto również wiedzieć, ile kosztują usługi oraz jakie środowiska i systemy obsługują. Widoczność pełnego zakresu kosztów to kluczowy element wymagany do dokładnego zrozumienia wzorca wydatków organizacji. Wzorce wydatków mogą być używane do wymuszania mechanizmów kontroli kosztów, takich jak budżety.

Ten przewodnik Szybki start ułatwia eksplorowanie i analizowanie kosztów organizacyjnych przy użyciu funkcji analizy kosztów. Możesz wyświetlić zagregowane koszty według organizacji, aby dowiedzieć się, gdzie występują koszty w miarę upływu czasu, i zidentyfikować trendy wydatków. Zakumulowane koszty w czasie można wyświetlać w celu oszacowania miesięcznych, kwartalnych, a nawet rocznych trendów kosztów w odniesieniu do budżetu. Budżet pomaga działać zgodnie z ograniczeniami finansowymi. Budżet jest również używany do wyświetlania kosztów dziennych lub miesięcznych w celu odizolowania nieprawidłowości wydatków. Ponadto można pobrać dane bieżącego raportu do dalszej analizy lub do użycia w systemie zewnętrznym.

W tym przewodniku Szybki start zawarto informacje na temat wykonywania następujących czynności:

- Przeglądanie kosztów w obrębie analizy kosztów
- Dostosowywanie widoków kosztów
- Pobieranie danych analizy kosztów


## <a name="prerequisites"></a>Wymagania wstępne

Analiza kosztów obsługuje wiele różnych typów kont platformy Azure. Aby wyświetlić pełną listę obsługiwanych typów kont, zobacz [Omówienie danych usługi Cost Management](understand-cost-mgt-data.md). Aby wyświetlić dane kosztów, potrzebujesz przynajmniej dostępu do odczytu dla Twojego konta platformy Azure.

Dla klientów [Umowy Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) musisz mieć przynajmniej dostęp do odczytu do co najmniej jednego z poniższych zakresów, aby wyświetlić dane kosztów.

- Konto billingowe
- Dział
- Konto rejestracji
- Grupa zarządzania
- Subskrypcja
- Grupa zasobów

Aby uzyskać więcej informacji na temat przypisywania dostępu do danych usługi Cost Management, zobacz [Przypisywanie dostępu do danych](assign-access-acm-data.md).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

- Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

## <a name="review-costs-in-cost-analysis"></a>Przeglądanie kosztów w obrębie analizy kosztów

Aby zapoznać się z kosztami analizy kosztów, otwórz żądany zakres w witrynie Azure portal i wybierz pozycję **analiza kosztów** w menu. Na przykład, przejdź do **subskrypcje**, wybierz subskrypcję z listy, a następnie wybierz **analiza kosztów** w menu. Użyj **zakres** skażone, aby przełączyć się do innego zasięgu analizy kosztów. Aby uzyskać więcej informacji na temat zakresów, zobacz [poznawanie i Praca z zakresami](understand-work-scopes.md).

Wybrany zakres będzie używany w całej usłudze Cost Management w celu zapewnienia konsolidacji danych i kontrolowania dostępu do informacji o kosztach. Gdy używasz zakresów, nie wybierasz równocześnie wielu z nich. W zamian wybierasz większy zakres obejmujący inne zakresy, a następnie filtrujesz zawartość do potrzebnych szczegółów. Zrozumienie tej kwestii jest ważne, ponieważ niektóre osoby nie powinny mieć dostępu do zakresu nadrzędnego, który obejmuje zakresy podrzędne.

Kliknij pozycję **Otwórz analizę kosztów**.

Początkowy widok analizy kosztów zawiera następujące obszary:

**Suma** — przedstawia łączne koszty w bieżącym miesiącu.

**Budżet** — przedstawia limit planowanych wydatków w wybranym zakresie, jeśli jest dostępny.

**Skumulowany koszt** — przedstawia sumę naliczonych wydatków dziennych od początku miesiąca. Po [utworzeniu budżetu](tutorial-acm-create-budgets.md) subskrypcji lub konta billingowego możesz szybko wyświetlić trend wydatków w odniesieniu do budżetu. Umieść kursor nad datą, aby wyświetlić skumulowany koszt w tym dniu.

**Wykresy przestawne (pierścieniowe)** — są to dynamiczne elementy przestawne, które przedstawiają podział kosztów według typowego zestaw właściwości standardowych. Pokazują one koszt naliczony w bieżącym miesiącu — od wartości najwyższej do najniższej. Wykresy przestawne można zmieniać w dowolnym momencie, wybierając inny element przestawny. Koszty są domyślnie dzielone na kategorie według usługi (kategorii miernika), lokalizacji (regionu) i zakresu podrzędnego. Są to na przykład konta rejestracji w ramach kont bilingowych, grupy zasobów w ramach subskrypcji i zasoby w ramach grup zasobów.

![Początkowy widok analizy kosztów w witrynie Azure Portal](./media/quick-acm-cost-analysis/cost-analysis-01.png)

## <a name="customize-cost-views"></a>Dostosowywanie widoków kosztów

Widok domyślny oferuje szybkie odpowiedzi na często zadawane pytania, takie jak:

- Ile pieniędzy wydano?
- Czy wydatki zmieściły się w budżecie?

W większości przypadków będziesz jednak potrzebować dokładniejszej analizy. Dostosowywanie rozpoczyna się od wybrania daty w górnej części strony.

Analiza kosztów domyślnie przedstawia dane z bieżącego miesiąca. Aby szybko przełączyć się do ostatniego miesiąca, bieżącego miesiąca, bieżącego kwartału kalendarzowego, bieżącego roku kalendarzowego lub wybranego niestandardowego zakresu dat, można użyć selektora dat. Wybór ostatniego miesiąca jest najszybszym sposobem na przeanalizowanie ostatniej faktury dotyczącej platformy Azure i rozliczenia opłat. Opcje bieżącego kwartału i roku pomagają śledzić koszty w odniesieniu do budżetów obejmujących dłuższe okresy. Możesz również wybrać inny zakres dat. Może być to na przykład jeden dzień, siedem ostatnich dni lub dowolny okres do roku przed bieżącym miesiącem.

![Selektor daty przedstawiający przykładowy wybór dla tego miesiąca](./media/quick-acm-cost-analysis/date-selector.png)

Analiza kosztów domyślnie przedstawia **skumulowane** koszty. Skumulowane koszty obejmują wszystkie koszty dla każdego dnia oraz poprzednich dni i umożliwiają tworzenie wciąż rosnącego widoku naliczonych kosztów dziennych. Ten widok jest optymalizowany w celu pokazania trendów względem budżetu dla wybranego zakresu czasu.

Istnieje również widok **dzienny** przedstawiający koszty danego dnia. Widok dzienny nie zawiera trendu wzrostu. Widok został zaprojektowany z myślą o wyświetlaniu nieprawidłowości, gdy koszt gwałtownie wzrasta lub spada z dnia na dzień. W przypadku wybrania budżetu widok dzienny pokazuje również szacowany budżet na dany dzień. Jeśli koszty dzienne stale przekraczają szacowany budżet dzienny, można oczekiwać przekroczenia kwoty budżetu miesięcznego. Szacowany budżet dzienny to po prostu metoda ułatwiająca wizualizowanie budżetu na niższym poziomie. W przypadku wahań kosztów dziennych porównanie szacowanego budżetu dziennego z budżetem miesięcznym jest mniej dokładne.

Ogólnie rzecz biorąc, w ciągu ośmiu godzin można spodziewać się danych lub powiadomień dotyczących wykorzystanych zasobów.

![Widok dzienny przedstawiający przykładowe koszty dzienne dla bieżącego miesiąca](./media/quick-acm-cost-analysis/daily-view.png)

Możesz użyć opcji **Grupuj według**, aby wybrać kategorię grupy i zmienić dane wyświetlane w obszarze górnego wykresu sumy. Grupowanie pozwala szybko zobaczyć, jak wydatki są kategoryzowane według typowych właściwości zasobów i użycia, takich jak grupa zasobów lub tagi zasobów. Aby grupować według tagów, wybierz klucz tagu, według którego chcesz grupować. Spowoduje to wyświetlenie kosztów podzielonych według poszczególnych wartości dla tego tagu oraz dodatkowego segmentu dla zasobów, do których nie zastosowano tego tagu.

Większość [zasobów platformy Azure obsługuje tagowanie](../azure-resource-manager/tag-support.md), jednak niektóre tagi nie są dostępne na stronie Zarządzanie kosztami i rozliczenia. Ponadto nie są obsługiwane tagi grup zasobów. Rozwiązanie Cost Management obsługuje tylko tagi zasobów od daty bezpośredniego zastosowania tagów do zasobu.

Oto widok kosztów platformy Azure w ostatnim miesiącu.

![Skumulowany, zgrupowany widok dzienny przedstawiający przykładowe koszty usług platformy Azure w ostatnim miesiącu](./media/quick-acm-cost-analysis/grouped-daily-accum-view.png)

Wykresy przestawne w obszarze wykresu głównego przedstawiają różne sposoby grupowania, co daje szerszy obraz kosztów ogólnych dla wybranego przedziału czasu i filtrów. Wybierz właściwość lub tag, aby wyświetlić zagregowane koszty według dowolnego wymiaru.


![Pełne dane dla bieżącego widoku, w którym są wyświetlane nazwy grup zasobów](./media/quick-acm-cost-analysis/full-data-set.png)

Na poprzedniej ilustracji pokazano nazwy grup zasobów. Możesz grupować według tagów, aby wyświetlić całkowite koszty na tag, ale wyświetlanie wszystkich tagów na zasób lub grupę zasobów nie jest dostępne w żadnym widoku analizy kosztów.

Podczas grupowania kosztów według konkretnego atrybutu dziesięć największych składników kosztów jest pokazanych od najwyższego do najniższego. Jeśli istnieje więcej niż dziesięć grup, wyświetlane jest dziewięć największych składników kosztów. Wyświetlana jest również grupa **Inne** obejmująca wszystkie pozostałe grupy. W przypadku grupowania według tagów może również zostać wyświetlona grupa **Bez tagów** dla kosztów, w których nie zastosowano klucza tagu. Grupa **Bez tagów** jest zawsze wyświetlana jako ostatnia, nawet jeśli koszty bez tagów są wyższe niż koszty z tagami. Jeśli liczba wartości tagów jest równa dziesięć lub większa, koszty bez tagów będą częścią grupy **Inne**.

*Klasyczne* maszyny wirtualne (usługi Azure Service Management, ASM), sieci i zasoby magazynu nie udostępniają szczegółowych danych dotyczących rozliczeń. Są one scalane jako **Usługi klasyczne** podczas grupowania kosztów.

Możesz wyświetlić pełny zestaw danych dla dowolnego widoku. Jakiekolwiek zastosowane opcje lub filtry wpływają na prezentowane dane. Aby wyświetlić pełny zestaw danych, kliknij listę **typ wykresu**, a następnie kliknij widok **tabeli**.

![Dane dla bieżącego widoku w widoku tabeli](./media/quick-acm-cost-analysis/chart-type-table-view.png)


## <a name="download-cost-analysis-data"></a>Pobieranie danych analizy kosztów

Możesz **pobrać** informacje z funkcji analizy kosztów, aby wygenerować plik CSV zawierający wszystkie dane wyświetlane obecnie w witrynie Azure Portal. Ten plik zawiera wszystkie stosowane filtry i grupowania. Podstawowe dane dotyczące górnego wykresu sumy, który nie jest aktywnie wyświetlany, znajdują się w pliku CSV.

## <a name="next-steps"></a>Kolejne kroki

Przejdź do pierwszego samouczka, aby dowiedzieć się, jak tworzyć budżety i nimi zarządzać.

> [!div class="nextstepaction"]
> [Tworzenie budżetów i zarządzanie nimi](tutorial-acm-create-budgets.md)
