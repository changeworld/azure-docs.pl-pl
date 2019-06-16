---
title: Myślistwo możliwości w wersji zapoznawczej Azure przez wartownika | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak korzystać z możliwości platformy Azure przez wartownika myślistwo.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 6aa9dd27-6506-49c5-8e97-cc1aebecee87
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 754722508de248294667ee9e923e8faf3ad67f61
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65236285"
---
# <a name="hunt-for-threats-with-in-azure-sentinel-preview"></a>Hunt pod kątem zagrożeń przy użyciu w wersji zapoznawczej Azure przez wartownika

> [!IMPORTANT]
> Wartownik platformy Azure jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Jeśli badającego, który chce, aby stosować proaktywne podejście o wyszukiwanie pod kątem zagrożeń zabezpieczeń platformy Azure przez wartownika zaawansowane myślistwo Przeszukuj i Badaj narzędzia do wyszukiwania pod kątem zagrożeń bezpieczeństwa w źródłach danych Twojej organizacji. Jednak systemy i urządzenia zabezpieczeń generuje górach danych, który może być trudne do analizowania i filtrowania do istotnych zdarzeń. Ułatwia zabezpieczeń analityków poszukaj aktywnie nowe anomalie, które nie zostały wykryte przez aplikacje zabezpieczeń platformy Azure przez wartownika "zapytaniach wbudowane myślistwo prowadzą do zadawać właściwe pytania, aby znaleźć problemy w danych, masz już w sieci. 

Na przykład jeden wbudowaną kwerendę dostarczają danych o najbardziej nietypowe procesów uruchomionych w ramach swojej infrastruktury — nie byłoby dobrze alert o każdym są uruchamiane, może być całkowicie nieszkodliwie, ale możesz chcieć spojrzeć na zapytanie, które czasami, aby sprawdzić, czy th ere przez coś szczególnego. 



Za pomocą platformy Azure przez wartownika myślistwo, możesz korzystać z zalet następujące możliwości:

- Wbudowane zapytania: Aby ułatwić rozpoczęcie pracy, strony początkowej zawiera przykłady zapytań załadowanych, umożliwiające pracę i zapoznać się z tabel i język zapytań. Te zapytania myślistwo wbudowane są opracowywane przez Microsoft naukowo w sposób ciągły, dodawanie nowych zapytań i dostosowawczych istniejące zapytanie do przedstawienia punkt wejścia, wyszukaj wykrywanie nowych zagrożeń i zorientować się, jak zacząć dłoni początku nowymi atakami. 

- Zaawansowany język zapytań za pomocą funkcji IntelliSense: Oparty na język zapytań, który zapewnia elastyczność, należy wykonać myślistwo na następny poziom.

- Utwórz własne zakładek: W trakcie myślistwo mogą pochodzić między dopasowań lub ustalenia, pulpitów nawigacyjnych lub działań, które wyglądają na nietypowe lub podejrzane. Aby można było oznaczyć te elementy, dzięki czemu możesz wrócić do nich w przyszłości, należy korzystać z funkcji zakładki. Zakładki pozwalają na zapisywanie elementów na później, można użyć do utworzenia przypadku badania. Aby uzyskać więcej informacji na temat zakładek Zobacz Użyj [zakładki w myślistwo].

- Korzystanie z notesów, aby zautomatyzować badania: Notesy są podobne elementy playbook krok po kroku, tworzących do wykonywania kroków badania i możliwe.  Notesy hermetyzacji wszystkich kroków myślistwo wielokrotnego użytku element playbook, który można udostępniać innym osobom w organizacji. 
- Zapytanie przechowywane dane: Dane te są dostępne w tabelach służących do wykonywania zapytań. Można na przykład kwerendy procesu tworzenia zdarzeń DNS i wiele innych typów zdarzeń.

- Linki do społeczności: Możliwościom większa społeczności można znaleźć dodatkowe zapytania i źródła danych.
 
## <a name="get-started-hunting"></a>Rozpoczynanie pracy myślistwo

1. W portalu Azure przez wartownika kliknij **myślistwo**.
  ![Azure wartownik uruchamia myślistwo](media/tutorial-hunting/hunting-start.png)

2. Po otwarciu **myślistwo** stronie wszystkich zapytań myślistwo są wyświetlane w jednej tabeli. Tabela zawiera listę wszystkich zapytań, które są zapisywane przez zespół firmy Microsoft, analityków zabezpieczeń, jak również wszelkie dodatkowe kwerendy utworzony lub zmodyfikowany. Każde zapytanie zawiera opis jego hunts dla i jakiego rodzaju dane, jest uruchamiany na. Te szablony są pogrupowane według ich różnych taktyka — ikony w prawym rogu kategoryzować rodzaju zagrożenia, takie jak początkowego dostępu, trwałość i wykradanie. Możesz filtrować te szablony zapytania myślistwo przy użyciu dowolnych pól. Możesz zapisać każde zapytanie do ulubionych. Zapisane zapytania do ulubionych, zapytanie automatycznie za każdym razem uruchamia **myślistwo** uzyskiwać dostęp do strony. Można utworzyć własne zapytanie myślistwo lub klonowania i dostosować istniejący szablon myślistwo zapytania. 
 
2. Kliknij przycisk **uruchom zapytanie** myślistwo szczegóły kwerendy stronie do uruchamiania każdego zapytania bez opuszczania strony myślistwo.  Liczba dopasowań jest wyświetlany w tabeli. Przejrzyj listę myślistwo zapytań i ich odpowiedniki. Zapoznaj się z etap w łańcuchu kończenia dopasowanie jest skojarzony.

3. Przeprowadź krótkiej recenzji podstawowego zapytania w okienku szczegółów zapytania lub kliknij przycisk **wyświetlić wynik zapytania** aby otworzyć kwerendę w usłudze Log Analytics. Na dole należy przejrzeć dopasowania dla zapytania.

4.  Kliknij wiersz i wybierz pozycję **Dodaj zakładkę** dodawania wierszy do należy zbadać — można to zrobić dla każdego elementu, który pewne informacje wyglądają podejrzanie. 

5. Następnie wróć do głównego **myślistwo** strony, a następnie kliknij przycisk **zakładki** kartę, aby zobaczyć wszystkie podejrzane działania. 

6. Wybierz zakładkę, a następnie kliknij przycisk **zbadaj** otworzyć środowisko badania. Można filtrować zakładek. Na przykład jeśli badasz kampanii, możesz utworzyć tag dla kampanii i następnie filtrować wszystkie zakładki w oparciu kampanii.

1. Po odnalezieniu którym zapytaniu myślistwo informują o wysokiej wartości możliwych ataków można również utworzyć niestandardowe wykrywania reguł na podstawie Twojego zapytania i prezentuj takie wnioski jako alerty do usługi obiektów odpowiadających w zdarzenia zabezpieczeń.

 

## <a name="query-language"></a>Język zapytań 

Myślistwo w przez wartownika Azure opiera się na język zapytań usługi Azure Log Analytics. Aby uzyskać więcej informacji na temat obsługiwanych operatory i język zapytań, zobacz [dokumentacja języka zapytań](https://docs.loganalytics.io/docs/Language-Reference/).

## <a name="public-hunting-query-github-repository"></a>Repozytorium GitHub zapytania publicznych myślistwo

Zapoznaj się z [repozytorium zapytania myślistwo](https://github.com/Azure/Orion). Współtworzenie i użyj przykładowych zapytań udostępnionych przez naszych klientów.

 

## <a name="sample-query"></a>Przykładowe zapytanie

Typowe zapytania zaczyna się od nazwy tabeli, następuje szereg operatory rozdzielone \|.

W przykładzie powyżej rozpoczyna się od tabeli SecurityEvent Określanie nazwy i gazociągami elementów zgodnie z potrzebami.

1. Zdefiniuj filtr czasu, aby zapoznać się z tylko rekordy z ostatnich siedmiu dni.

2. Dodaj filtr w zapytaniu tylko w celu wyświetlenia zdarzeń 4688 identyfikator.

3. Dodaj filtr w zapytaniu w wierszu polecenia, aby zawierała tylko wystąpienia cscript.exe.

4. Projekt tylko kolumny interesuje eksplorowania i ograniczyć wyniki do 1000 i kliknij przycisk **uruchom zapytanie**.
5. Kliknij zielony trójkąt, a następnie uruchomić zapytanie. Można przetestować zapytanie i uruchom go, aby szukać nietypowego zachowania.

## <a name="useful-operators"></a>Przydatne operatory

Zaawansowany język zapytań i ma wiele dostępnych operatorów, poniżej przedstawiono niektóre przydatne operatory:

**gdzie** — Filtruj tabelę do podzestawu wierszy, które spełniają predykat.

**Podsumowanie** -utworzyć tabelę, która agreguje zawartość tabeli wejściowej.

**sprzężenia** -scalenie wierszy z dwóch tabel w celu utworzenia nowej tabeli, dopasowując wartości określone kolumny z każdej tabeli.

**Liczba** — zwraca liczbę rekordów w danych wejściowych zestawu rekordów.

**TOP** -Return pierwsze N rekordów posortowanych według określonych kolumn.

**limit** -zwracają maksymalnie określoną liczbę wierszy.

**Projekt** — wybierz kolumny do dołączania, zmienić lub porzucić i Wstaw nowe kolumny obliczanej.

**Rozszerzanie** — Tworzenie kolumn obliczeniowych i dołączyć je do zestawu wyników.

**makeset** -zwrócić zestawu różne wartości, które pobiera wyrażenie tablic dynamicznych (JSON) w grupie

**Znajdź** — Znajdź wiersze spełniające predykat w różnych tabelach.

## <a name="save-a-query"></a>Zapisz zapytanie

Można utworzyć lub zmodyfikować zapytanie i zapisz go jako własną kwerendę lub udostępnić go użytkownikom, którzy znajdują się w tej samej dzierżawy.

   ![Zapisz zapytanie](./media/tutorial-hunting/save-query.png)

Utwórz nowe zapytanie myślistwo:

1. Kliknij przycisk **nowe zapytanie** i wybierz **Zapisz**.
2. Wypełnij puste pola i wybierz **Zapisz**.

   ![Nowe zapytanie](./media/tutorial-hunting/new-query.png)

Klonowanie i zmodyfikować istniejące zapytanie myślistwo:

1. Wybierz zapytanie myślistwo w tabeli, którą chcesz zmodyfikować.
2. Wybierz przycisk wielokropka (...) w wierszu kwerendę, o których chcesz zmodyfikować, a następnie wybierz pozycję **Klonuj zapytanie**.

   ![Klonuj zapytanie](./media/tutorial-hunting/clone-query.png)
 

3. Zmodyfikuj zapytanie i wybierz **Utwórz**.

   ![Zapytanie niestandardowe](./media/tutorial-hunting/custom-query.png)

## <a name="next-steps"></a>Kolejne kroki
W tym artykule przedstawiono sposób uruchamiania badania myślistwo za pomocą platformy Azure przez wartownika. Aby dowiedzieć się więcej na temat platformy Azure przez wartownika, zobacz następujące artykuły:


- [Korzystanie z notesów, aby uruchomić zautomatyzowane myślistwo kampanii](notebooks.md)
- [Używanie zakładek w celu zapisania interesujące informacje podczas myślistwo](bookmarks.md)