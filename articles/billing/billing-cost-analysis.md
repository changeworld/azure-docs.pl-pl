---
title: Zapoznaj się z kosztów platformy Azure za pomocą analizy kosztów | Dokumentacja firmy Microsoft
description: Ten artykuł ułatwia korzystanie analiza kosztów można badać i analizować kosztów platformy Azure organizacji.
services: billing
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 08/16/2018
ms.topic: conceptual
ms.service: billing
manager: dougeby
ms.custom: ''
ms.openlocfilehash: db06956e01ed38ce1f8ee0ce92526be16733fd73
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/24/2018
ms.locfileid: "42818128"
---
# <a name="explore-and-analyze-costs-with-cost-analysis"></a>Eksploruj i Analizuj kosztów za pomocą analizy kosztów

Aby można było poprawnie kontrolować i Optymalizowanie kosztów platformy Azure, należy zrozumieć, skąd pochodzi koszty w Twojej organizacji. Jest również grupowaniu można sprawdzić, jaka kwota usługi kosztów i w celu, jakie środowisk i systemów. Widoczność pełnego zakresu koszty bardzo ważne jest dokładne określenie organizacji wydatków wzorców, które mogą być używane do wymuszania mechanizmów kontroli kosztów, takich jak budżetów.

W tym artykule używasz analiza kosztów można badać i analizować koszty organizacji. Możesz wyświetlić zagregowane koszty przez Twoją organizację, aby zrozumieć, w której są naliczane koszty i identyfikowania trendów wydatków. Możesz wyświetlić łączne koszty wraz z upływem czasu, można oszacować, co miesiąc, co kwartał, lub nawet rocznego kosztu trendów w odniesieniu do budżetu. Budżetu pomaga spełnić ograniczeń finansowych lub wyświetlić koszty codziennie lub co miesiąc, aby wyizolować wydatków nieprawidłowości. Ponadto można pobrać danych bieżący raport do dalszej analizy, lub do użycia w systemie zewnętrznym.

## <a name="requirements"></a>Wymagania

Analiza kosztów jest dostępna dla wszystkich klientów z umową Enterprise Agreement (EA). Musi mieć dostęp do odczytu do co najmniej jeden z następujących zakresów do wyświetlania danych kosztów.

- EA platformy Azure (rejestracji) konto rozliczeniowe
- Subskrypcja platformy Azure EA
- Grupa zasobów subskrypcji w usłudze Azure EA

## <a name="review-costs-in-cost-analysis"></a>Przejrzyj koszty w analizy kosztów

Aby przejrzeć koszty przy użyciu analizy kosztów, otwórz witrynę Azure portal, a następnie przejdź do **Zarządzanie kosztami i rozliczenia** &gt; **kont rozliczeń** &gt; wybierz umowy EA kontorozliczeniowe&gt; Cost Management wybierz **analiza kosztów**.

Początkowy koszt analitycznym obejmuje następujące obszary:

**Łączna liczba** — przedstawia łączne koszty dla bieżącego miesiąca.

**Budżet** — pokazuje limit planowanych wydatków dla wybranego zakresu, jeśli jest dostępny.

**Skumulowany koszt** — przedstawia łączny naliczane codzienne wydatki, zaczynając od początku miesiąca. Jeśli masz [utworzony budżet](billing-cost-management-budget-scenario.md#create-the-azure-budget) dla rozliczeń konta lub subskrypcji, można szybko wyświetlić swoje trendu wydatków w odniesieniu do budżetu. Umieść kursor nad datę skumulowany koszt do tego samego dnia.

**Przestawianie (pierścieniowe)** — zapewniają dynamiczne tabele przestawne. Awarie łączny koszt przez typowe zestaw właściwości standardowych. Wykresy pokazują najbardziej do najmniej koszty naliczane dla bieżącego miesiąca. Wykresów przestawnych można zmienić w dowolnym momencie, wybierając inny element przestawny. Domyślnie koszty są podzielone według usługi (kategoria licznika), lokalizacji (regionu) i zakresie podrzędnym (na przykład rejestracji konta w ramach rozliczeń kont, grup zasobów w ramach subskrypcji i zasobów w ramach grupy zasobów).

![Widok początkowy koszt analizy](./media/billing-cost-analysis/cost-analysis-01.png)



## <a name="customizing-cost-views"></a>Dostosowywanie widoków kosztów

Widok domyślny zawiera szybkich odpowiedzi na często zadawane pytania, takie jak:

- Ile wydatków?
- Będą przechowywane w ramach budżetu?

Istnieją jednak wiele przypadków, gdy potrzebujesz dokładniejszej analizy. Dostosowywanie rozpoczyna się w górnej części strony, z zastosowaniem wyboru daty.

Domyślnie analiza kosztów zawiera dane dla bieżącego miesiąca. Selektor daty szybkie przełączanie się do ostatniego miesiąca, w tym miesiącu, ten kwartał kalendarzowy, w tym roku kalendarzowym lub niestandardowy zakres dat wybranych przez użytkownika. Wybór w ciągu ostatniego miesiąca jest najszybszym sposobem na analizowanie Twojej ostatniej faktury platformy Azure i łatwo uzgodnienia opłaty. Bieżące opcje kwartalnych i rocznych śledzić koszty w porównaniu do budżetów dłuższy okres. Lub bardziej precyzyjny lub szerszy zakres dat można wybrać z jednego dnia, ostatnich siedmiu dni lub cokolwiek nawet sprzed roku przed bieżącego miesiąca.

![Selektor daty](./media/billing-cost-analysis/date-selector.png)

Również domyślnie koszt analizy pokazuje **skumulowana** kosztów. Łączne koszty obejmują wszelką cenę za każdy dzień poza poprzednich dni, dla rosnącego widoku codziennie, naliczone koszty. Ten widok jest zoptymalizowany do pokazania, jak możesz Trend w odniesieniu do budżetu dla wybranego zakresu czasu.

Istnieje również **codzienne** widoku. Pokazuje kosztów dla każdego dnia i nie pokazuje trend wzrostu. Widok dzienny jest zoptymalizowany do wyświetlania nieprawidłowości jako nagły wzrost kosztów lub zanurzyć z dnia na dzień. Po wybraniu budżetu, widok dzienny pokazuje również oszacowanie, jak może wyglądać codzienne budżetu. W przypadku dziennych kosztów stale powyżej szacowany dzienny budżetu, wówczas można spodziewać się, że będziesz przekracza swój budżet miesięczne. Szacowany dzienny budżet jest po prostu oznacza ułatwiają wizualizowanie swój budżet na niższym poziomie. W przypadku wahania dziennych kosztów szacowany dzienny porównania budżetu do Twojego budżetu miesięcznych jest mniej dokładne.

![Widok dzienny](./media/billing-cost-analysis/daily-view.png)

Możesz **Grupuj według** wybrać kategorię grupy, aby zmienić dane wyświetlane na wykresie najważniejsze całkowitego obszaru. Grupowanie pozwala szybko sprawdzić, jak wydatków jest dzielony na kategorie według typu zasobów. Oto widok kosztów usługi platformy Azure w celu wyświetlenia ostatniego miesiąca.

![Zgrupowane Widok zebranych dzienny](./media/billing-cost-analysis/grouped-daily-accum-view.png)

Wykresów przestawnych znajdują się w górnym widoku łączna liczba. Użyj ich, aby wyświetlić widoki dla różnych grupowanie i filtrowanie kategorii. Po wybraniu dowolnej grupy kategorii, pełny zestaw danych dla widoku całkowita jest w dolnej części widoku. Oto przykład dla grupy zasobów.

![Pełnych danych dla bieżącego widoku](./media/billing-cost-analysis/full-data-set.png)

### <a name="download-cost-analysis-data"></a>Pobierz dane analizy kosztów

Gdy użytkownik **Pobierz** informacje z analiza kosztów, generowany jest plik CSV dla wszystkich danych, które obecnie są wyświetlane w witrynie Azure portal. Jeśli zostały zastosowane jakiekolwiek filtry lub grupowania, następnie są one dołączone w pliku. Niektóre dane bazowe dla górnego wykresu całkowita, która aktywnie nie jest wyświetlana w portalu znajduje się również w pliku CSV.

## <a name="next-steps"></a>Kolejne kroki

Wyświetl inne [rozliczenia platformy Azure i kosztów zarządzania dokumentacja](billing-cost-management-budget-scenario.md).
