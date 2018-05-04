---
title: Użyj raportów dotyczących zarządzania koszt w Azure kosztów zarządzania | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób używania różnych raportach kosztów zarządzania w portalu Cloudyn.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/26/2018
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: db7663623ada5d7799c46018be9e3b66afd004ca
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="use-cost-management-reports"></a>Korzystanie z raportów rozwiązania Cost Management

W tym artykule opisano sposób używania różnych raportach kosztów zarządzania w portalu Cloudyn. Większość raportów Cloudyn są intuicyjne i zapewnia jednolite wyglądu i działania. Aby uzyskać ogólne informacje o raportach Cloudyn, zobacz [opis koszt raporty](understanding-cost-reports.md). Artykuł opisuje również różne opcje i pola używane w większości raportów.

## <a name="cost-analysis-reports"></a>Raporty analizy kosztów

Raporty analizy kosztów wyświetlania rozliczeń danych od dostawców w chmurze. Korzystanie z raportów można grupy i przejdź do różnych segmentów danych wymienione w pliku rozliczeń. Raporty umożliwiają szczegółowego koszt nawigowanie między nieprzetworzone dane rozliczeń dostawców chmury.

Raporty analizy kosztów nie Grupuj według znaczników kosztów. Raportowanie oparte na tag jest dostępna tylko w raportach alokacji kosztów ustawić po utworzeniu koszt modelu przy użyciu 360 alokacji kosztów.

### <a name="actual-cost-analysis"></a>Rzeczywista analiza kosztów

Rzeczywiste analizy kosztów przedstawia uczestników koszt głównym łącznie z bieżących kosztów i jednorazowe opłaty.

 Raport analizy kosztów rzeczywiste do:

- Analizowanie i rzeczywistych kosztów monitor spędzony w określonym czasie
- Harmonogram alertu progu
- Analizowanie koszty ogólne analizy kosztów i obciążeń zwrotnych

#### <a name="to-use-the-actual-cost-analysis-report"></a>Aby korzystać z raportu analizy kosztów rzeczywistych

Co najmniej wykonaj następujące kroki. Umożliwia także inne opcje i pól.

1. Wybierz zakres dat.
2. Wybierz filtr.

Możesz kliknąć prawym przyciskiem myszy raportu dotyczącego wyników, aby przejść do nich i wyświetlić bardziej szczegółowe informacje.

![Rzeczywisty przykład raportu analizy kosztów](./media/use-reports/actual-cost-analysis.png)

### <a name="actual-cost-over-time"></a>Rzeczywisty koszt wraz z upływem czasu

Rzeczywisty koszt w miarę upływu czasu raport jest ewidencyjnych analizy dystrybucji koszt za pośrednictwem rozwiązania zdefiniowanego czasu. Ten raport wyświetla listę wydatków czasie można obserwować trendy i wykrywanie wydatków nieprawidłowości. Ten raport przedstawia Twojej współautorzy głównego koszt tym bieżących kosztów i opłat jednorazowe wystąpienia zastrzeżonych, przeznaczanych w zadanym przedziale czasu.

Użyj raportu rzeczywisty koszt w miarę upływu czasu, aby:

- Zobacz trendów kosztów wraz z upływem czasu.
- Znajdź nieprawidłowości kosztów.
- Znajdź wszystkie pytania związane z kosztów związanych z usług Amazon Web Services.

#### <a name="to-use-the-actual-cost-over-time-report"></a>Aby korzystać z raportu rzeczywisty koszt w miarę upływu czasu:

Co najmniej wykonaj następujące kroki. Umożliwia także inne opcje i pól.

- Wybierz zakres dat.

Na przykład można wybrać grupy, aby wyświetlić ich kosztów wraz z upływem czasu. A następnie Dodaj filtry, aby zawęzić wyniki wyszukiwania.

![Rzeczywisty koszt w czasie przykład raportu](./media/use-reports/actual-cost-over-time.png)



### <a name="amortized-cost-reports"></a>Raporty amortyzowanego koszt

Ten zestaw amortyzowanego koszt raporty pokazuje linearized z systemem innym niż obciążenia na podstawie opłaty lub jednorazowe dostawcami koszty i rozpowszechniania kosztów wraz z upływem czasu równomiernie podczas ich cykl życia.

Na przykład opłaty jednorazowe mogą być następujące:

- Opłaty roczne
- Opłaty roczne składnika zabezpieczeń
- Zarezerwowane wystąpień zakupu opłat
- Niektóre elementy portalu Azure Marketplace

W pliku rozliczeń jednorazowe opłaty są określony podczas usługi zużycie początkową i końcową lub timestamp, mają takie same wartości. Cloudyn następnie rozpoznaje je jako jednorazowe opłaty, które mogą być amortyzowanego. Nie może być amortyzowanego innych usług na podstawie zużycia kosztów użytkowania na żądanie.

Aby zilustrować amortyzowanego kosztów, przejrzyj następujący przykład obrazu rzeczywisty koszt w czasie — raport. W tym przykładzie przedstawiono kolekcji koszt na 23 sierpnia. Może wydawać się anomalii w porównaniu do zwykłych dzienne trendy kosztów. Główny przyczynę problemu i nawigowanie wśród danych identyfikowane ten koszt jako roczne usług AWS usługi APN rezerwacji, czyli jednorazową opłatę zakupione i rozliczany w tym dniu. Aby zobaczyć, jak ten koszt jest amortyzowanego w następnej sekcji.

![Rzeczywisty koszt w czasie raport przykładowy jednorazowe koszt](./media/use-reports/actual-amort-example.png)

#### <a name="to-use-the-amortized-cost-over-time-report"></a>Aby korzystać z raportu amortyzowanego koszt w miarę upływu czasu:

Co najmniej wykonaj następujące kroki. Umożliwia także inne opcje i pól.

1. Wybierz zakres dat.
2. Wybierz usługę i dostawcy.

Wykonywanie następny poprzedni przykład, zobaczysz, że koszt jednorazowego jest teraz amortyzowanego na poniższej ilustracji:

![Amortyzowanego przykładowy raport koszt w czasie](./media/use-reports/amort-cost-over-time.png)

Poprzedni obraz zawiera koszt amortized kosztów rezerwacji APN wraz z upływem czasu. Ten raport przedstawia amortyzacji jednorazową opłatę i kosztów APN jako roczne zakupu rezerwacji. Koszt APN jest rozmieszczana równomiernie codziennie, 1/365th kosztów ponoszonych z góry rezerwacji.

## <a name="cost-allocation-analysis-reports"></a>Raporty analizy alokacji kosztów

Raporty analizy alokacji kosztów są dostępne po utworzeniu koszt modelu przy użyciu 360 alokacji kosztów. Cloudyn przetwarza dane koszt/rozliczeń i zgodna ze strukturą danych do danych użycia i tagu kont chmury. Cloudyn pasują do danych, wymaga dostępu do danych użycia. Konta, których brakuje poświadczeń, są oznaczone jako zasoby bez kategorii.

### <a name="cost-analysis-report"></a>Raport analizy kosztów

Raport analizy kosztów zapewnia wgląd w chmurze użycia i wydatków w zadanym przedziale czasu. Zasadami ustawionymi w Menedżerze alokacji kosztów są używane w raporcie analizy kosztów.

Jak Cloudyn obliczyć ten raport?

Cloudyn gwarantuje, że alokacji zachowuje integralności każdego połączonego konta, stosując koligacji konta. Koligacja gwarantuje, że konto, które nie używa określonej usługi nie ma kosztów tej usługi przydzielone do niego. Koszty naliczone konta pozostają na tym koncie i nie są obliczane przy użyciu zasad alokacji. Na przykład może być połączone konta. Jeśli tylko trzy z nich korzystać z usług magazynu koszt magazynowania tylko jest przydzielany przez tagów w trzech kont.

 Raport analizy kosztów do:

- Wyświetlanie zagregowane całego wdrożenia dla w określonym przedziale czasu.
- Wyświetl koszty według kategorii tagu na podstawie zasad utworzonych w modelu kosztów.

#### <a name="to-use-the-cost-analysis-report"></a>Aby korzystać z raportu analizy kosztów:

1. Wybierz zakres dat.
2. Dodaj znaczniki, zgodnie z potrzebami.
3. Dodaj grupy.
4. Wybierz wcześniej utworzony model kosztów.

Na poniższej ilustracji przedstawiono przykład raportu analizy kosztów w formacie sunburst. Pierścieni Pokaż grupy. Zewnętrzny pierścień przedstawia usługi i wewnętrzny okrąg zawiera jednostki.

![Przykład raportu analizy kosztów](./media/use-reports/cost-analysis01.png)



Oto przykład tych samych informacji w widoku tabeli.

![Przykład raportu analizy kosztów](./media/use-reports/cost-analysis02.png)



### <a name="cost-over-time-report"></a>Raport kosztu w czasie

Raport koszt w czasie Wyświetla wydatków wraz z upływem czasu, dzięki czemu można obserwować trendy i wykrycia nieprawidłowości w danym wdrożeniu. Przedstawia on zasadniczo rozłożone w określonym przedziale czasu. Raport zawiera Twoje współautorzy głównego koszt tym bieżących kosztów i opłat jednorazowe wystąpienia zastrzeżonych, przeznaczanych w zadanym przedziale czasu. Zasadami ustawionymi w Menedżerze koszt 360° służy w tym raporcie.

Raport koszt w czasie do:

- Zmiany są widoczne w czasie i które wpływ Zmień jeden dzień (lub zakres dat) do następnego.
- Analizować kosztów wraz z upływem czasu określonego wystąpienia.
- Zrozumieć, dlaczego zaobserwowano zwiększenie kosztów dla określonego wystąpienia.

#### <a name="to-use-the-cost-over-time-report"></a>Aby korzystać z raportu kosztów w czasie:

1. Wybierz zakres dat.
2. Dodaj znaczniki, zgodnie z potrzebami.
3. Dodaj grupy.
4. Wybierz wcześniej utworzony model kosztów.
5. Wybierz rzeczywistych kosztów lub amortyzowanego kosztów.
6. Określ, czy do zastosowania reguły do widoku rozliczeń widoku danych pierwotnych lub do obliczenia kosztu widoku.

Oto przykład raportu.

![Przykład kosztów w czasie](./media/use-reports/cost-over-time.png)



## <a name="next-steps"></a>Kolejne kroki

- Jeśli nie zostało już ukończone pierwszy samouczek koszt zarządzania, przeczytaj je na [Przejrzyj użycia i koszty](tutorial-review-usage.md).
