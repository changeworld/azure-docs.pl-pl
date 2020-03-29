---
title: Zarządzanie wersjami — USŁUGA LUIS
titleSuffix: Azure Cognitive Services
description: Wersje umożliwiają tworzenie i publikowanie różnych modeli. Dobrą praktyką jest sklonowanie bieżącego aktywnego modelu do innej wersji aplikacji przed wprowadzeniem zmian w modelu.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: 138b84a9b7f54782fd6254304a3fdcf4dba83182
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220898"
---
# <a name="use-versions-to-edit-and-test-without-impacting-staging-or-production-apps"></a>Używanie wersji do edytowania i testowania bez wpływu na aplikacje przejściowe lub produkcyjne

Wersje umożliwiają tworzenie i publikowanie różnych modeli. Dobrą praktyką jest sklonowanie bieżącego aktywnego modelu do innej [wersji](luis-concept-version.md) aplikacji przed wprowadzeniem zmian w modelu. 

Aby pracować z wersjami, otwórz aplikację, wybierając jej nazwę na stronie **Moje aplikacje,** a następnie wybierz pozycję **Zarządzaj** na górnym pasku, a następnie wybierz pozycję **Wersje** w lewej nawigacji. 

Lista wersji pokazuje, które wersje są publikowane, gdzie są publikowane i która wersja jest aktualnie aktywna. 

> [!div class="mx-imgBorder"]
> [![Zarządzanie sekcją, strona wersje](./media/luis-how-to-manage-versions/versions-import.png "Zarządzanie sekcją, strona wersje")](./media/luis-how-to-manage-versions/versions-import.png#lightbox)

## <a name="clone-a-version"></a>Klonowanie wersji

1. Wybierz wersję, którą chcesz sklonować, a następnie wybierz pozycję **Klonuj** z paska narzędzi. 

2. W oknie dialogowym **Klonowanie wersji** wpisz nazwę nowej wersji, takiej jak "0.2".

   ![Okno dialogowe Klonowanie wersji](./media/luis-how-to-manage-versions/version-clone-version-dialog.png)
 
     > [!NOTE]
     > Identyfikator wersji może składać się tylko ze znaków, cyfr lub '.' i nie może być dłuższy niż 10 znaków.
 
   Nowa wersja o określonej nazwie jest tworzona i ustawiana jako wersja aktywna.

## <a name="set-active-version"></a>Ustawianie aktywnej wersji

Wybierz wersję z listy, a następnie wybierz **pozycję Aktywuj** na pasku narzędzi. 

> [!div class="mx-imgBorder"]
> [![Zarządzanie sekcją, strony wersje, wykonać akcję wersji](./media/luis-how-to-manage-versions/versions-other.png "Zarządzanie sekcją, strony wersje, wykonać akcję wersji")](./media/luis-how-to-manage-versions/versions-other.png#lightbox)

## <a name="import-version"></a>Importowanie wersji

Można `.json` zaimportować `.lu` lub wersję aplikacji.

1. Wybierz **pozycję Importuj** z paska narzędzi, a następnie wybierz format. 

2. W oknie podręcznym **Importuj nową wersję** wprowadź nową nazwę wersji dziesięciu znaków. Identyfikator wersji wystarczy tylko ustawić, jeśli wersja w pliku już istnieje w aplikacji.

    ![Zarządzanie sekcją, stroną wersji, importowanie nowej wersji](./media/luis-how-to-manage-versions/versions-import-pop-up.png)

    Po zaimportowaniu wersji nowa wersja staje się wersją aktywną.

### <a name="import-errors"></a>Błędy importu

* Błędy tokenizatora: Jeśli podczas importowania pojawia się **błąd tokenizatora,** próbujesz zaimportować wersję, która używa innego [tokenizatora](luis-language-support.md#custom-tokenizer-versions) niż obecnie używana aplikacja. Aby rozwiązać ten problem, zobacz [Migrowanie między wersjami tokenizatora](luis-language-support.md#migrating-between-tokenizer-versions).

<a name = "export-version"></a>

## <a name="other-actions"></a>Inne akcje

* Aby **usunąć** wersję, wybierz wersję z listy, a następnie wybierz pozycję **Usuń** z paska narzędzi. Wybierz **ok**. 
* Aby **zmienić nazwę** wersji, wybierz wersję z listy, a następnie wybierz **pozycję Zmień nazwę** z paska narzędzi. Wprowadź nową nazwę i wybierz pozycję **Gotowe**. 
* Aby **wyeksportować** wersję, wybierz wersję z listy, a następnie wybierz **pozycję Eksportuj aplikację** z paska narzędzi. Wybierz JSON do wyeksportowania do kopii zapasowej, wybierz pozycję Eksportuj dla **kontenera,** aby [użyć tej aplikacji w kontenerze usługi LUIS](luis-container-howto.md).  

