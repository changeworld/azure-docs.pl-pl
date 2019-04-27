---
title: Informacje o danych podczas myślistwo w przez wartownika wersji zapoznawczej Azure korzystanie z zakładek myślistwo | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak używać zakładek myślistwo przez wartownika platformy Azure do śledzenia danych.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 320ccdad-8767-41f3-b083-0bc48f1eeb37
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 9a7ceeab6d2ad761752f778038692256bd87624b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60596909"
---
# <a name="keep-track-of-data-during-hunting"></a>Zachowaj informacje o danych podczas myślistwo

> [!IMPORTANT]
> Wartownik platformy Azure jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 
Myślistwo zagrożeń wymaga zwykle od przeglądanie tam dane dziennika szuka dowodu złośliwego zachowania. W trakcie tego procesu badaczy znaleźć zdarzenia, które mają do zapamiętania, ponownie i analizować jako część sprawdzania poprawności potencjalnych hipotez i zrozumienie całą historię naruszenia zabezpieczeń.
Zakładki myślistwo pomóc Ci to zrobić, przy zachowaniu zapytań, że został uruchomiony w usłudze Log Analytics oraz wyniki zapytania, które można uznać za istotne. Można również rejestrować swoje kontekstowych uwag i odwoływać się swoimi ustaleniami poprzez dodanie uwagi i tagów. Dane z zakładką jest widoczna członkom zespołu łatwo współpracy.   

Dane z zakładką możesz wrócić w dowolnym momencie, w **zakładki** karcie **myślistwo** strony. Możesz użyć filtrowania i opcje, aby szybko znaleźć określone dane dla bieżącego badania wyszukiwania. Alternatywnie można wyświetlić dane zakładką bezpośrednio w **HuntingBookmark** tabeli w usłudze Log Analytics. Dzięki temu można filtrować, podsumowanie i Dołącz do dodanego danych z innych źródeł danych, co ułatwia poszukaj dowody potwierdzające.

Można również wizualizować dane z zakładką, klikając **zbadaj**. Spowoduje to uruchomienie badać, w którym można wyświetlać, badanie i wizualnie komunikują się swoimi ustaleniami przy użyciu diagramu interaktywne wykres jednostki i osi czasu.


## <a name="run-a-log-analytics-query-from-azure-sentinel"></a>Uruchom zapytanie usługi Log Analytics z platformy Azure przez wartownika

1. W portalu Azure przez wartownika kliknij **myślistwo** do uruchamiania zapytań dla podejrzanych i nietypowych zachowań.

1. Aby uruchomić kampanii myślistwo, wybierz jedno z zapytań myślistwo i przegląd po lewej stronie, wyniki. 

1. Kliknij przycisk **wyświetlenia wyników zapytania** w zapytaniu myślistwo **szczegóły** strony, aby wyświetlić zapytanie wyników w usłudze Log Analytics. Oto przykład wyświetlanych jeżeli uruchomiono zapytanie niestandardowe ataku bruteforce SSH.
  
   ![Pokaż wyniki](./media/bookmarks/ssh-bruteforce-example.png)

## <a name="add-a-bookmark"></a>Dodawanie zakładki

1. Na liście wyników zapytania usługi Log Analytics należy rozwinąć wiersz zawierający informacje możesz znaleźć interesujące.

4. Wybierz przycisk wielokropka (...) na końcu wiersza, a następnie wybierz pozycję **dodać zakładki myślistwo**.
5. Po prawej stronie w **szczegóły** strony, zaktualizuj nazwę, a następnie dodaj znaczniki i notatki, aby pomóc w zidentyfikowaniu, jaki był interesujący o elemencie.
6. Kliknij przycisk **Zapisz** aby zatwierdzić zmiany. Wszystkie dane z zakładką jest współużytkowany z innymi badaczy i jest to pierwszy krok w kierunku współpracy badać.

   ![Pokaż wyniki](./media/bookmarks/add-bookmark-la.png)

 
> [!NOTE]
> Za pomocą dowolnego zapytań usługi Log Analytics uruchomionego z dzienników platformy Azure przez wartownika Log Analytics strony lub zapytań tworzone na bieżąco ze strony usługi Log Analytics i otwarty na stronie myślistwo można również używać zakładek. Nie można dodać zakładkę, jeśli uruchomienie usługi Log Analytics z poza przez wartownika platformy Azure. 

## <a name="view-and-update-bookmarks"></a>Wyświetl i zaktualizuj zakładki 

1. W portalu Azure przez wartownika kliknij **myślistwo**. 
2. Kliknij przycisk **zakładki** kartę pośrodku strony, aby wyświetlić listę zakładek.
3. Użyj opcji wyszukiwania w polu lub filtr, aby znaleźć określonej zakładki.
4. Wybierz poszczególne zakładki w siatce poniżej, aby wyświetlić szczegóły zakładkę w okienku szczegółów po prawej stronie.
5. Aby zaktualizować znaczniki i notatki, kliknij pozycję edytowalnymi polami tekstowymi, a następnie kliknij przycisk **Zapisz** Aby zachować zmiany.

   ![Pokaż wyniki](./media/bookmarks/view-update-bookmarks.png)

## <a name="view-bookmarked-data-in-log-analytics"></a>Wyświetl dane z zakładką w usłudze Log Analytics 

Istnieje wiele sposobów nawiązania wyświetlanie zakładką danych w usłudze Log Analytics. 

Najprostszym sposobem wyświetlenia zapytań zakładką, wyniki lub historii polega na wybraniu żądaną zakładki w **zakładki** tabeli i użyć linków dostępnych w okienku szczegółów. Dostępne są następujące opcje: 
- Kliknij pozycję **Wyświetl zapytanie** Aby wyświetlić zapytania źródłowego w usłudze Log Analytics.  
- Kliknij pozycję **wyświetlić historię zakładki** aby zobaczyć wszystkie zakładki metadanych, w tym: kto wprowadził aktualizacji, zaktualizowanymi wartościami i czas wystąpiła aktualizacja. 

- Można również wyświetlić dane pierwotne zakładki wszystkie zakładki, klikając **zakładki dzienniki** nad siatką zakładki. Ten widok zostaną wyświetlone wszystkie zakładki w tabeli zakładki myślistwo ze skojarzonymi metadanymi. Zapytania KQL służy do odfiltrowania najnowszej wersji określonej zakładki, którego szukasz.  


> [!NOTE]
> Może być znaczne opóźnienie (mierzony w minutach) między tworzenia zakładki i kiedy jest wyświetlany w **HuntingBookmark** tabeli. Zalecane jest, aby najpierw utworzyć zakładki, a następnie analizowanie ich po dane są pozyskiwane. 

## <a name="delete-a-bookmark"></a>Usuń zakładkę
Jeśli chcesz usunąć zakładki, wykonaj następujące czynności: 
1.  Otwórz th **zakładki myślistwo** kartę. 
2.  Wybierz zakładkę docelowego.
3.  Wybierz przycisk wielokropka (...) na końcu wiersza i wybierz pozycję **zakładki Delete**.
    
Usuwanie zakładki zakładka zostanie usunięta z listy w **zakładki** kartę.  Usługa Log Analytics "HuntingBookmark" tabela będzie w dalszym ciągu zawierają wcześniejsze wpisy zakładki, ale zmieni się najnowszy wpis **SoftDelete** wartość true, co ułatwia filtrowanie starych zakładek.  Usuwanie zakładki nie powoduje usunięcia żadnych jednostek ze środowiska badania, które są skojarzone z innymi zakładek lub alertów. 


## <a name="next-steps"></a>Kolejne kroki

W tym artykule przedstawiono sposób uruchamiania badania myślistwo korzystanie z zakładek w przez wartownika platformy Azure. Aby dowiedzieć się więcej na temat platformy Azure przez wartownika, zobacz następujące artykuły:


- [Proaktywnie możliwe pod kątem zagrożeń](hunting.md)
- [Korzystanie z notesów, aby uruchomić zautomatyzowane myślistwo kampanii](notebooks.md)