---
title: Śledź dane podczas polowania na platformie Azure — wskaźnikiem polowań | Microsoft Docs
description: W tym artykule opisano, jak używać zakładek polowania na platformie Azure do śledzenia danych.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 320ccdad-8767-41f3-b083-0bc48f1eeb37
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/24/2019
ms.author: rkarlin
ms.openlocfilehash: aa414e37470cc11b7dc83e7416590aa2babf6818
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240249"
---
# <a name="keep-track-of-data-during-hunting-with-azure-sentinel"></a>Śledź dane podczas polowania z platformą Azure — wskaźnikiem

Polowanie zagrożeń zwykle wymaga przejrzenia górach danych dzienników szukających dowodu złośliwego zachowania. W trakcie tego procesu badacze znajdą zdarzenia, które chcą zapamiętać, odwiedzać i przeanalizować w ramach walidacji potencjalnych postanowień, a także zrozumieć pełną historię naruszenia.

Zakładki łowiectwa na platformie Azure — wskaźnik pomocy, dzięki zachowaniu zapytań, które zostały uruchomione w Log Analytics, wraz z wynikami zapytania, które są odpowiednie. Możesz również rejestrować uwagi kontekstowe i odwoływać się do wyników, dodając notatki i Tagi. Dane oznaczone zakładką są widoczne dla Ciebie i Twoich członków zespołu w celu ułatwienia współpracy.

W dowolnym momencie możesz ponownie odwiedzić dane z zakładkami na karcie **zakładka** okienka **polowania** . Możesz użyć opcji filtrowania i wyszukiwania, aby szybko znaleźć konkretne dane dla bieżącego badania. Alternatywnie możesz wyświetlać dane z zakładek bezpośrednio w tabeli **HuntingBookmark** w Azure monitor. Dzięki temu można filtrować, podsumowywać i łączyć dane z zakładkami z innymi źródłami danych, co ułatwia wyszukiwanie corroboratingych dowodów.

Obecnie w wersji zapoznawczej, jeśli okaże się, że w przypadku niektórych problemów należy rozwiązać problem w dziennikach, w kilku kliknięciach można utworzyć zakładkę i podwyższyć poziom do zdarzenia lub dodać zakładkę do istniejącego zdarzenia. Aby uzyskać więcej informacji na temat zdarzeń, [zobacz Samouczek: Zbadaj zdarzenia za pomocą usługi](tutorial-investigate-cases.md)Azure wskaźnikowej. 

Ponadto w wersji zapoznawczej można wizualizować dane oznaczone zakładką, klikając polecenie **Zbadaj** w obszarze Szczegóły zakładki. Spowoduje to uruchomienie środowiska dochodzeniowego, w którym można wyświetlać, badać i wizualnie komunikować swoje wyniki przy użyciu interaktywnego diagramu i osi czasu grafu obiektów.

## <a name="add-a-bookmark"></a>Dodawanie zakładki

1. W Azure Portal przejdź do**polowania** do > **zarządzania zagrożeniami** **wskaźnikiem** > , aby uruchomić zapytania dotyczące podejrzanego i nietypowego zachowania.

2. Wybierz jedną z zapytań polowania i po prawej stronie, w szczegółach zapytania polowania wybierz pozycję **Uruchom zapytanie**. 

3. Wybierz pozycję **Wyświetl wyniki zapytania**. Na przykład:
    
    > [!div class="mx-imgBorder"]
    > ![Wyświetl wyniki zapytania z polowania wskaźnikowego platformy Azure](./media/bookmarks/new-processes-observed-example.png)
    
    Ta akcja powoduje otwarcie wyników zapytania w okienku **dzienniki** .

4. Z listy wyników zapytania dziennika Rozwiń wiersz zawierający informacje interesujące.

5. Wybierz wielokropek (...) po lewej stronie, a następnie wybierz pozycję **Dodaj zakładkę polowania**:
    
    > [!div class="mx-imgBorder"]
    > ![Dodaj zakładkę polowania do zapytania](./media/bookmarks/add-hunting-bookmark.png)

6. Po prawej stronie w okienku **Dodaj zakładkę polowania** możesz opcjonalnie zaktualizować nazwę zakładki, dodać tagi i notatki, aby ułatwić identyfikację interesujących elementów.

7. W sekcji **Informacje o zapytaniu** Użyj pól listy rozwijanej, aby wyodrębnić informacje z wyników zapytania dla typu **konta**, **hosta**i jednostki **adresów IP** . Ta akcja mapuje wybrany typ jednostki na określoną kolumnę z wyniku zapytania. Na przykład:
    
    > [!div class="mx-imgBorder"]
    > ![Mapowanie typów jednostek dla zakładki polowania](./media/bookmarks/map-entity-types-bookmark.png)
    
    Aby wyświetlić zakładkę na grafie badania (obecnie w wersji zapoznawczej), należy zmapować co najmniej jeden typ jednostki, który jest **kontem**, **hostem**lub **adresem IP**. 

5. Kliknij przycisk **Dodaj** , aby zatwierdzić wprowadzone zmiany i dodać zakładkę. Wszystkie dane oznaczone zakładką są współużytkowane z innymi osobami badającymi i to pierwszy krok w kierunku współpracy z zespołowym badaniem.

 
> [!NOTE]
> Wyniki zapytania dziennika obsługują zakładki, za każdym razem, gdy to okienko zostanie otwarte z platformy Azure. Na przykład wybierz pozycję**dzienniki** **Ogólne** > na pasku nawigacyjnym, wybierz pozycję linki zdarzeń w grafie badania lub wybierz identyfikator alertu z pełnych szczegółów zdarzenia (obecnie w wersji zapoznawczej). Nie można tworzyć zakładek, gdy okienko **dzienniki** jest otwierane z innych lokalizacji, na przykład bezpośrednio z Azure monitor.

## <a name="view-and-update-bookmarks"></a>Wyświetl i Zaktualizuj zakładki 

1. W Azure Portal przejdź do**polowania**dotyczącego > **zarządzania zagrożeniami** **wskaźnikiem** > . 

2. Wybierz zakładkę **zakładki** , aby wyświetlić listę zakładek.

3. Aby ułatwić znalezienie określonej zakładki, użyj pola wyszukiwania lub opcji filtrowania.

4. Wybierz poszczególne zakładki i sprawdź szczegóły zakładki w okienku szczegółów z prawej strony.

5. Wprowadź odpowiednie zmiany, które są zapisywane automatycznie.

## <a name="exploring-bookmarks-in-the-investigation-graph"></a>Eksplorowanie zakładek na grafie badania

> [!IMPORTANT]
> Eksplorowanie zakładek na grafie badania i samym grafie badania jest obecnie w publicznej wersji zapoznawczej.
> Te funkcje są dostępne bez umowy dotyczącej poziomu usług i nie są zalecane w przypadku obciążeń produkcyjnych.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

1. W Azure Portal przejdź do karty **wskaźnik** > **polowania** > na > potrzeby**zarządzania zagrożeniami**, a następnie**Wybierz zakładkę lub** zakładki, które chcesz zbadać.

2. Upewnij się, że w obszarze Szczegóły zakładki jest zamapowana co najmniej jedna jednostka. Na przykład w przypadku **jednostek**widoczne są wpisy dotyczące **adresu IP**, **komputera**lub **konta**.

3. Kliknij przycisk **Zbadaj** , aby wyświetlić zakładkę w grafie badania.

Aby uzyskać instrukcje dotyczące korzystania z grafu badania, zobacz temat [Korzystanie z grafu badania do głębokiego szczegółowe](tutorial-investigate-cases.md#use-the-investigation-graph-to-deep-dive).

## <a name="add-bookmarks-to-a-new-or-existing-incident"></a>Dodaj zakładki do nowego lub istniejącego zdarzenia

> [!IMPORTANT]
> Dodawanie zakładek do nowego lub istniejącego zdarzenia jest obecnie w publicznej wersji zapoznawczej.
> Ta funkcja jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

1. W Azure Portal przejdź do karty **wskaźnik** > **polowania** > na > potrzeby**zarządzania zagrożeniami**, a następnie**Wybierz zakładkę lub** zakładki, które chcesz dodać do zdarzenia.

2. Wybierz **Akcje zdarzenia (wersja zapoznawcza)** z paska poleceń:
    
    > [!div class="mx-imgBorder"]
    > ![Dodaj zakładki do zdarzenia](./media/bookmarks/incident-actions.png)

3. Wybierz opcję **Utwórz nowe zdarzenie** lub **Dodaj do istniejącego zdarzenia**, zgodnie z potrzebami. Następnie:
    
    - Nowe zdarzenie: Opcjonalnie Zaktualizuj szczegóły zdarzenia, a następnie wybierz pozycję **Utwórz**.
    - Aby dodać zakładkę do istniejącego zdarzenia: Wybierz jedno zdarzenie, a następnie wybierz pozycję **Dodaj**. 

Aby wyświetlić zakładkę w zdarzeniu: Przejdź > do**zdarzenia** **zarządzania** > zagrożeniami **wskaźnikiem**, a następnie wybierz zdarzenie z zakładką. Wybierz pozycję **Wyświetl pełne szczegóły**, a następnie wybierz zakładkę **zakładki** .

## <a name="view-bookmarked-data-in-logs"></a>Wyświetlanie danych z zakładkami w dziennikach

Aby wyświetlić zapytania z zakładkami, wyniki lub ich historię, wybierz zakładkę z karty**zakładki** **polowania** > i użyj linków w okienku szczegółów: 

- **Wyświetl zapytanie źródłowe** , aby wyświetlić zapytanie źródłowe w okienku **dzienniki** .

- **Wyświetl dzienniki zakładek** , aby wyświetlić wszystkie metadane zakładki, w tym osoby, które dokonały aktualizacji, zaktualizowane wartości i czas wystąpienia aktualizacji.

Możesz również wyświetlić nieprzetworzone dane zakładki dla wszystkich zakładek, wybierając pozycję **dzienniki zakładek** na pasku poleceń na karcie**zakładki** **polowania** > :

> [!div class="mx-imgBorder"]
> ![Dzienniki zakładek](./media/bookmarks/bookmark-logs.png)

Ten widok przedstawia wszystkie zakładki ze skojarzonymi metadanymi. Możesz użyć zapytań [języka KQL (keyword Query Language](https://docs.microsoft.com/sharepoint/dev/general-development/keyword-query-language-kql-syntax-reference) ), aby odfiltrować do najnowszej wersji konkretnej zakładki.

> [!NOTE]
> Istnieje duże opóźnienie (mierzone w minutach) między czasem utworzenia zakładki a wyświetleniem jej na karcie **zakładki** .

## <a name="delete-a-bookmark"></a>Usuwanie zakładki
 
1.  W Azure Portal przejdź do karty **wskaźnik** > **polowania** > na > potrzeby**zarządzania zagrożeniami**, a następnie**Wybierz zakładkę lub** zakładki, które chcesz usunąć. 

2. Wybierz wielokropek (...) na końcu wiersza, a następnie wybierz pozycję **Usuń zakładkę**.
    
Usunięcie zakładki spowoduje usunięcie zakładki z listy na karcie **zakładka** . Tabela Log Analytics **HuntingBookmark** będzie nadal zawierać poprzednie wpisy zakładki, ale Najnowsza pozycja zmieni wartość **SoftDelete** na true, co ułatwia odfiltrowanie starych zakładek. Usunięcie zakładki nie powoduje usunięcia żadnych jednostek ze środowiska badania, które są skojarzone z innymi zakładkami lub alertami. 


## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób uruchamiania badania polowania przy użyciu zakładek na platformie Azure. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:


- [Proaktywne wyszukiwanie zagrożeń](hunting.md)
- [Korzystanie z notesów do uruchamiania zautomatyzowanych kampanii łowieckich](notebooks.md)
