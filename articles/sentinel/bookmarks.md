---
title: Używanie zakładek polowania do badania danych w usłudze Azure Sentinel
description: W tym artykule opisano, jak używać zakładek polowania wartownika platformy Azure do śledzenia danych.
author: yelevin
ms.author: yelevin
manager: rkarlin
ms.assetid: 320ccdad-8767-41f3-b083-0bc48f1eeb37
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.custom: mvc
ms.date: 10/24/2019
ms.openlocfilehash: eacc104bf7157732f91507f631bcda2cd166bfb3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588692"
---
# <a name="keep-track-of-data-during-hunting-with-azure-sentinel"></a>Śledzenie danych podczas polowania za pomocą usługi Azure Sentinel

Polowanie na zagrożenia zazwyczaj wymaga przeglądania gór danych dziennika w poszukiwaniu dowodów złośliwego zachowania. Podczas tego procesu badacze znajdują wydarzenia, które chcą zapamiętać, ponownie i przeanalizować w ramach sprawdzania potencjalnych hipotez i zrozumienia pełnej historii kompromisu.

Zakładki polowania w usłudze Azure Sentinel pomagają to zrobić, zachowując zapytania, które zostały uruchomiony w **usłudze Azure Sentinel — dzienniki**, wraz z wynikami kwerendy, które uważasz za istotne. Można również rejestrować swoje obserwacje kontekstowe i odwoływać się do wyników, dodając notatki i tagi. Dane oznaczone zakładkami są widoczne dla Ciebie i Twoich kolegów z drużyny w celu łatwej współpracy.

W każdej chwili możesz ponownie przejrzeć dane z zakładkami na karcie **Zakładki** okienka **Polowanie.** Możesz użyć opcji filtrowania i wyszukiwania, aby szybko znaleźć określone dane dla bieżącego dochodzenia. Alternatywnie można wyświetlić dane oznaczone zakładkami bezpośrednio w tabeli **HuntingBookmark** w obszarze roboczym usługi Log Analytics. Przykład:

> [!div class="mx-imgBorder"]
> ![zobacz tabelę HuntingBookmark](./media/bookmarks/bookmark-table.png)

Wyświetlanie zakładek z tabeli umożliwia filtrowanie, podsumowywanie i dołączanie danych z zakładkami z innymi źródłami danych, co ułatwia wyszukanie dowodów potwierdzających.

Obecnie w wersji zapoznawczej, jeśli znajdziesz coś, co pilnie musi zostać rozwiązane podczas polowania w dziennikach, za pomocą kilku kliknięć możesz utworzyć zakładkę i promować ją do incydentu lub dodać zakładkę do istniejącego zdarzenia. Aby uzyskać więcej informacji o zdarzeniach, zobacz [Samouczek: Badanie zdarzeń za pomocą usługi Azure Sentinel](tutorial-investigate-cases.md). 

Również w wersji zapoznawczej można wizualizować dane z zakładkami, klikając **pozycję Zbadaj** ze szczegółów zakładki. Spowoduje to uruchomienie środowiska dochodzenia, w którym można wyświetlać, badać i wizualnie komunikować swoje ustalenia za pomocą interaktywnego diagramu jednostki i osi czasu.

## <a name="add-a-bookmark"></a>Dodawanie zakładki

1. W witrynie Azure portal przejdź do funkcji**Polowanie na** **zarządzanie zagrożeniami** >  **wartownika,** > aby uruchamiać zapytania dotyczące podejrzanych i nietypowych zachowań.

2. Wybierz jedno z zapytań myśliwskich i po prawej stronie w szczegółach kwerendy myśliwskiej wybierz pozycję **Uruchom kwerendę**. 

3. Wybierz **pozycję Wyświetl wyniki kwerendy**. Przykład:
    
    > [!div class="mx-imgBorder"]
    > ![wyświetlanie wyników kwerend z polowania na usługę Azure Sentinel](./media/bookmarks/new-processes-observed-example.png)
    
    Ta akcja powoduje otwarcie wyników kwerendy w okienku **Dzienniki.**

4. Na liście wyników kwerendy dziennika użyj pól wyboru, aby zaznaczyć jeden lub więcej wierszy zawierających informacje, które są interesujące.

5. Wybierz **dodaj zakładkę:**
    
    > [!div class="mx-imgBorder"]
    > ![Dodawanie zakładki polowania do kwerendy](./media/bookmarks/add-hunting-bookmark.png)

6. Po prawej stronie w okienku **Dodaj zakładkę** opcjonalnie zaktualizuj nazwę zakładki, dodaj tagi i notatki, aby ułatwić identyfikację tego, co było interesujące w elemencie.

7. W sekcji **Informacje o kwerendach** użyj pól rozwijanych, aby wyodrębnić informacje z wyników kwerendy dla typów encji **Konto,** **Host**i **Adres IP.** Ta akcja mapuje wybrany typ encji na określoną kolumnę z wyniku kwerendy. Przykład:
    
    > [!div class="mx-imgBorder"]
    > ![Mapowanie typów encji dla zakładek polowania](./media/bookmarks/map-entity-types-bookmark.png)
    
    Aby wyświetlić zakładkę na wykresie dochodzenia (obecnie w wersji zapoznawczej), należy zamapować co najmniej jeden typ encji, który jest **kontem,** **hostem**lub **adresem IP.** 

5. Kliknij **przycisk Zapisz,** aby zatwierdzić zmiany i dodać zakładkę. Wszystkie dane z zakładkami są udostępniane innym badaczom i są pierwszym krokiem w kierunku wspólnego dochodzenia.

 
> [!NOTE]
> Wyniki kwerendy dziennika obsługują zakładki za każdym razem, gdy to okienko jest otwierane z usługi Azure Sentinel. Na przykład wybierz**dzienniki** **ogólne** > z paska nawigacyjnego, wybierz łącza zdarzeń na wykresie dochodzeń lub wybierz identyfikator alertu z pełnych szczegółów zdarzenia (obecnie w wersji zapoznawczej). Nie można tworzyć zakładek, gdy okienko **Dzienniki** jest otwierane z innych lokalizacji, takich jak bezpośrednio z usługi Azure Monitor.

## <a name="view-and-update-bookmarks"></a>Wyświetlanie i aktualizowanie zakładek 

1. W witrynie Azure portal przejdź do pozycji Polowanie na**zarządzanie zagrożeniami** >  **wartownika** > .**Hunting** 

2. Wybierz kartę **Zakładki,** aby wyświetlić listę zakładek.

3. Aby ułatwić znalezienie określonej zakładki, użyj pola wyszukiwania lub opcji filtru.

4. Wybierz poszczególne zakładki i wyświetl szczegóły zakładki w okienku szczegółów po prawej stronie.

5. W razie potrzeby wejdę w życie zmiany, które zostaną automatycznie zapisane.

## <a name="exploring-bookmarks-in-the-investigation-graph"></a>Eksplorowanie zakładek na wykresie dochodzenia

> [!IMPORTANT]
> Eksplorowanie zakładek na wykresie dochodzenia i sam wykres dochodzenia są obecnie w publicznej wersji zapoznawczej.
> Te funkcje są dostarczane bez umowy dotyczącej poziomu usług i nie są zalecane dla obciążeń produkcyjnych.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

1. W witrynie Azure portal przejdź do karty Księgi**polowania na** > zagrożenia związane**z zagrożeniami** >  **wartowników** > i wybierz zakładkę lub zakładki, które chcesz zbadać.**Bookmarks**

2. W szczegółach zakładki upewnij się, że co najmniej jedna encja jest mapowana. Na przykład dla **jednostek**, są widoczne wpisy dla **IP**, **Maszyny**lub **konta**.

3. Kliknij **przycisk Zbadaj,** aby wyświetlić zakładkę na wykresie dochodzenia.

Aby uzyskać instrukcje dotyczące korzystania z wykresu badania, zobacz [Użyj wykresu badania do głębokiego nurkowania](tutorial-investigate-cases.md#use-the-investigation-graph-to-deep-dive).

## <a name="add-bookmarks-to-a-new-or-existing-incident"></a>Dodawanie zakładek do nowego lub istniejącego zdarzenia

> [!IMPORTANT]
> Dodawanie zakładek do nowego lub istniejącego zdarzenia jest obecnie w publicznej wersji zapoznawczej.
> Ta funkcja jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana dla obciążeń produkcyjnych.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

1. W witrynie Azure portal przejdź do karty Księgi**polowania na** > zagrożenia związane**z zagrożeniami** > **wartownika** **Sentinel** > i wybierz zakładkę lub zakładki, które chcesz dodać do zdarzenia.

2. Wybierz **akcje Zdarzenia (Podgląd)** z paska poleceń:
    
    > [!div class="mx-imgBorder"]
    > ![Dodawanie zakładek do zdarzenia](./media/bookmarks/incident-actions.png)

3. W razie potrzeby wybierz pozycję **Utwórz nowe zdarzenie** lub **Dodaj do istniejącego zdarzenia.** Następnie:
    
    - W przypadku nowego zdarzenia: Opcjonalnie zaktualizuj szczegóły zdarzenia, a następnie wybierz pozycję **Utwórz**.
    - Aby dodać zakładkę do istniejącego zdarzenia: wybierz jedno zdarzenie, a następnie wybierz pozycję **Dodaj**. 

Aby wyświetlić zakładkę w ramach zdarzenia: przejdź do zdarzenia**zarządzania zagrożeniami** >  **sentinel** > **i** wybierz zdarzenie za pomocą zakładki. Wybierz **pozycję Wyświetl pełne szczegóły**, a następnie wybierz kartę **Zakładki.**

> [!TIP]
> Alternatywnie do opcji **Zdarzenia (Podgląd)** na pasku poleceń można użyć menu kontekstowego (**...**) dla jednej lub więcej zakładek, aby wybrać opcje **Utwórz nowy incydent**, Dodaj **do istniejącego zdarzenia**i Usuń z **incydentu**. 

## <a name="view-bookmarked-data-in-logs"></a>Wyświetlanie danych z zakładkami w dziennikach

Aby wyświetlić zapytania, wyniki lub historię zakładek, wybierz zakładkę z karty **Księgi myśliwskie** > i użyj łączy**podanych** w okienku szczegółów: 

- **Wyświetl kwerendę źródłą,** aby wyświetlić kwerendę źródłą w okienku **Dzienniki.**

- **Wyświetl dzienniki zakładek,** aby wyświetlić wszystkie metadane zakładek, w tym, kto dokonał aktualizacji, zaktualizowane wartości i czas, w którym nastąpiła aktualizacja.

Można również wyświetlić surowe dane zakładek dla wszystkich zakładek, wybierając **dzienniki zakładek** na pasku poleceń na karcie**Księgi myśliwskie:** **Hunting** > 

> [!div class="mx-imgBorder"]
> ![Dzienniki zakładek](./media/bookmarks/bookmark-logs.png)

W tym widoku są wyświetlane wszystkie zakładki ze skojarzonymi metadanymi. Za pomocą zapytań [języka KQL (Keyword Query Language)](https://docs.microsoft.com/sharepoint/dev/general-development/keyword-query-language-kql-syntax-reference) można filtrować do najnowszej wersji konkretnej zakładki, której szukasz.

> [!NOTE]
> Może wystąpić znaczne opóźnienie (mierzone w minutach) między momentem utworzenia zakładki a wyświetlaniem jej na karcie **Zakładki.**

## <a name="delete-a-bookmark"></a>Usuwanie zakładki
 
1.  W witrynie Azure portal przejdź do karty Księgi**polowania na** > zagrożenia związane**z zagrożeniami** >  **wartowników** > i wybierz zakładkę lub zakładki, które chcesz usunąć.**Bookmarks** 

2. Kliknij prawym przyciskiem myszy zaznaczenia i wybierz opcję, aby usunąć zakładkę lub zakładki. Na przykład **usuń zakładkę,** jeśli wybrano tylko jedną zakładkę, i **Usuń 2 zakładki,** jeśli wybrano dwie zakładki.
    
Usunięcie zakładki powoduje usunięcie zakładki z listy na karcie **Zakładka.** **Tabela HuntingBookmark** dla obszaru roboczego usługi Log Analytics będzie nadal zawierać poprzednie wpisy zakładek, ale najnowszy wpis zmieni wartość **SoftDelete** na true, ułatwiając odfiltrowanie starych zakładek. Usunięcie zakładki nie powoduje usunięcia żadnych jednostek z środowiska badania, które są skojarzone z innymi zakładkami lub alertami. 


## <a name="next-steps"></a>Następne kroki

W tym artykule dowiesz się, jak uruchomić badanie polowania przy użyciu zakładek w usłudze Azure Sentinel. Aby dowiedzieć się więcej o usłudze Azure Sentinel, zobacz następujące artykuły:


- [Proaktywne polowanie na zagrożenia](hunting.md)
- [Uruchamianie automatycznych kampanii łowieckich za pomocą notesów](notebooks.md)
