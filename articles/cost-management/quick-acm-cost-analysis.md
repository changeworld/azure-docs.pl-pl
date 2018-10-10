---
title: Szybki start — eksplorowanie kosztów platformy Azure za pomocą analizy kosztów | Microsoft Docs
description: Ten przewodnik Szybki start ułatwia eksplorowanie i analizowanie kosztów organizacyjnych platformy Azure za pomocą funkcji analizy kosztów.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/21/2018s
ms.topic: quickstart
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: 9092629c7bef46cdb7c464fca5e22d4aea0da9fc
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "47041544"
---
# <a name="quickstart-explore-and-analyze-costs-with-cost-analysis"></a>Szybki start: eksplorowanie kosztów za pomocą funkcji analizy kosztów

Aby poprawnie kontrolować i optymalizować koszty platformy Azure, trzeba zrozumieć, skąd pochodzą koszty w organizacji. Warto również wiedzieć, ile kosztują usługi oraz jakie środowiska i systemy obsługują. Widoczność pełnego zakresu kosztów to kluczowy element wymagany do dokładnego zrozumienia wzorca wydatków organizacji. Wzorce wydatków mogą być używane do wymuszania mechanizmów kontroli kosztów, takich jak budżety.

Ten przewodnik Szybki start ułatwia eksplorowanie i analizowanie kosztów organizacyjnych przy użyciu funkcji analizy kosztów. Możesz wyświetlić zagregowane koszty według organizacji, aby dowiedzieć się, gdzie występują koszty w miarę upływu czasu, i zidentyfikować trendy wydatków. Zakumulowane koszty w czasie można wyświetlać w celu oszacowania miesięcznych, kwartalnych, a nawet rocznych trendów kosztów w odniesieniu do budżetu. Budżet pomaga działać zgodnie z ograniczeniami finansowymi. Budżet jest również używany do wyświetlania kosztów dziennych lub miesięcznych w celu odizolowania nieprawidłowości wydatków. Ponadto można pobrać dane bieżącego raportu do dalszej analizy lub do użycia w systemie zewnętrznym.

W tym przewodniku Szybki start zawarto informacje na temat wykonywania następujących czynności:

- Przeglądanie kosztów w obrębie analizy kosztów
- Dostosowywanie widoków kosztów
- Pobieranie danych analizy kosztów


## <a name="prerequisites"></a>Wymagania wstępne

Analiza kosztów jest dostępna dla wszystkich klientów z umową [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/). Aby wyświetlać dane kosztów, musisz mieć co najmniej prawa dostępu do odczytu co najmniej jednego z poniższych zakresów.

- Konto billingowe
- Dział
- Konto rejestracji
- Grupa zarządzania
- Subskrypcja
- Grupa zasobów


## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

- Zaloguj się do witryny Azure Portal pod adresem http://portal.azure.com.

## <a name="review-costs-in-cost-analysis"></a>Przeglądanie kosztów w obrębie analizy kosztów

Aby przeglądać koszty przy użyciu analizy kosztów, w witrynie Azure Portal przejdź kolejno do pozycji **Zarządzanie kosztami i rozliczenia** &gt; **Zarządzanie kosztami** &gt; **Zmień zakres**, wybierz zakres, a następnie kliknij pozycję **Wybierz**.

Wybrany zakres będzie używany w całej usłudze Cost Management w celu zapewnienia konsolidacji danych i kontrolowania dostępu do informacji o kosztach. Gdy używasz zakresów, nie wybierasz równocześnie wielu z nich. W zamian wybierasz większy zakres obejmujący inne zakresy, a następnie filtrujesz zawartość do potrzebnych szczegółów. Zrozumienie tej kwestii jest ważne, ponieważ niektóre osoby nie powinny mieć dostępu do zakresu nadrzędnego, który obejmuje zakresy podrzędne.

Kliknij pozycję **Otwórz analizę kosztów**.

Początkowy widok analizy kosztów zawiera następujące obszary:

**Suma** — przedstawia łączne koszty w bieżącym miesiącu.

**Budżet** — przedstawia limit planowanych wydatków w wybranym zakresie, jeśli jest dostępny.

**Skumulowany koszt** — przedstawia sumę naliczonych wydatków dziennych od początku miesiąca. Po [utworzeniu budżetu](tutorial-acm-create-budgets.md) subskrypcji lub konta billingowego możesz szybko wyświetlić trend wydatków w odniesieniu do budżetu. Umieść kursor nad datą, aby wyświetlić skumulowany koszt w tym dniu.

**Wykresy przestawne (pierścieniowe)** — są to dynamiczne elementy przestawne, które przedstawiają podział kosztów według typowego zestaw właściwości standardowych. Pokazują one koszt naliczony w bieżącym miesiącu — od wartości najwyższej do najniższej. Wykresy przestawne można zmieniać w dowolnym momencie, wybierając inny element przestawny. Koszty są domyślnie dzielone na kategorie według usługi (kategorii miernika), lokalizacji (regionu) i zakresu podrzędnego. Są to na przykład konta rejestracji w ramach kont bilingowych, grupy zasobów w ramach subskrypcji i zasoby w ramach grup zasobów.

![Początkowy widok analizy kosztów](./media/quick-acm-cost-analysis/cost-analysis-01.png)

## <a name="customize-cost-views"></a>Dostosowywanie widoków kosztów

Widok domyślny oferuje szybkie odpowiedzi na często zadawane pytania, takie jak:

- Ile pieniędzy wydano?
- Czy wydatki zmieściły się w budżecie?

W większości przypadków będziesz jednak potrzebować dokładniejszej analizy. Dostosowywanie rozpoczyna się od wybrania daty w górnej części strony.

Analiza kosztów domyślnie przedstawia dane z bieżącego miesiąca. Aby szybko przełączyć się do ostatniego miesiąca, bieżącego miesiąca, bieżącego kwartału kalendarzowego, bieżącego roku kalendarzowego lub wybranego niestandardowego zakresu dat, można użyć selektora dat. Wybór ostatniego miesiąca jest najszybszym sposobem na przeanalizowanie ostatniej faktury dotyczącej platformy Azure i rozliczenia opłat. Opcje bieżącego kwartału i roku pomagają śledzić koszty w odniesieniu do budżetów obejmujących dłuższe okresy. Możesz również wybrać inny zakres dat. Może być to na przykład jeden dzień, siedem ostatnich dni lub dowolny okres do roku przed bieżącym miesiącem.

![Selektor daty](./media/quick-acm-cost-analysis/date-selector.png)

Analiza kosztów domyślnie przedstawia **skumulowane** koszty. Skumulowane koszty obejmują wszystkie koszty dla każdego dnia oraz poprzednich dni i umożliwiają tworzenie wciąż rosnącego widoku naliczonych kosztów dziennych. Ten widok jest optymalizowany w celu pokazania trendów względem budżetu dla wybranego zakresu czasu.

Istnieje również widok **dzienny** przedstawiający koszty danego dnia. Widok dzienny nie zawiera trendu wzrostu. Widok został zaprojektowany z myślą o wyświetlaniu nieprawidłowości, gdy koszt gwałtownie wzrasta lub spada z dnia na dzień. W przypadku wybrania budżetu widok dzienny pokazuje również szacowany budżet na dany dzień. Jeśli koszty dzienne stale przekraczają szacowany budżet dzienny, można oczekiwać przekroczenia kwoty budżetu miesięcznego. Szacowany budżet dzienny to po prostu metoda ułatwiająca wizualizowanie budżetu na niższym poziomie. W przypadku wahań kosztów dziennych porównanie szacowanego budżetu dziennego z budżetem miesięcznym jest mniej dokładne.

![Widok dzienny](./media/quick-acm-cost-analysis/daily-view.png)

Możesz użyć opcji **Grupuj według**, aby wybrać kategorię grupy i zmienić dane wyświetlane w obszarze górnego wykresu sumy. Grupowanie pozwala szybko sprawdzić, jak wydatki są dzielone na kategorie według typu zasobów. Oto widok kosztów platformy Azure w ostatnim miesiącu.

![Skumulowany widok pogrupowanych kosztów dziennych](./media/quick-acm-cost-analysis/grouped-daily-accum-view.png)

Wykresy przestawne w górnym widoku sumy pokazują widoki dla różnych kategorii grupowania i filtrowania. Po wybraniu dowolnej kategorii grupowania pełny zestaw danych dla widoku sumy będzie znajdować się w dolnej części widoku. Oto przykład dotyczący grup zasobów.

![Pełne dane dla widoku bieżącego](./media/quick-acm-cost-analysis/full-data-set.png)

Na poprzedniej ilustracji pokazano nazwy grup zasobów. Wyświetlanie tagów zasobów nie jest dostępne we wszystkich widokach, filtrach i grupowaniach analizy kosztów.

## <a name="download-cost-analysis-data"></a>Pobieranie danych analizy kosztów

Możesz **pobrać** informacje z funkcji analizy kosztów, aby wygenerować plik CSV zawierający wszystkie dane wyświetlane obecnie w witrynie Azure Portal. Ten plik zawiera wszystkie stosowane filtry i grupowania. Podstawowe dane dotyczące górnego wykresu sumy, który nie jest aktywnie wyświetlany, znajdują się w pliku CSV.

## <a name="next-steps"></a>Następne kroki

Przejdź do pierwszego samouczka, aby dowiedzieć się, jak tworzyć budżety i nimi zarządzać.

> [!div class="nextstepaction"]
> [Tworzenie budżetów i zarządzanie nimi](tutorial-acm-create-budgets.md)
