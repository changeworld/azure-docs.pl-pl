---
title: Zarządzanie wersjami
titleSuffix: Language Understanding - Azure Cognitive Services
description: Wersje umożliwiają tworzenie i publikowanie różnych modeli. Dobrą praktyką jest, aby sklonować bieżącego aktywnego modelu do innej wersji aplikacji przed wprowadzeniem zmian w modelu.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: 15ca8cf116defc2877b54789a039c72d70de1164
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2019
ms.locfileid: "54882485"
---
# <a name="use-versions-to-edit-and-test-without-impacting-staging-or-production-apps"></a>Użyj wersji do edycji i przetestować bez wywierania wpływu na aplikacje przejściowych lub produkcyjnych

Wersje umożliwiają tworzenie i publikowanie różnych modeli. Dobrym rozwiązaniem jest klonowanie bieżącego aktywnego modelu z innym [wersji](luis-concept-version.md) aplikacji przed wprowadzeniem zmian w modelu. 

Aby pracować z wersjami, Otwórz aplikację, wybierając jego nazwę na **Moje aplikacje** strony, a następnie wybierz pozycję **Zarządzaj** w górnym pasku, a następnie zaznacz **wersji** w nawigacji po lewej stronie. 

Listę wersji pokazują, które wersje są publikowane, gdzie są publikowane i która wersja jest obecnie aktywny. 

[![Sekcji Zarządzanie, strona wersji](./media/luis-how-to-manage-versions/versions-import.png "sekcji Zarządzanie, strona wersji")](./media/luis-how-to-manage-versions/versions-import.png#lightbox)

## <a name="clone-a-version"></a>Klonowanie wersji

1. Wybierz wersję chcesz sklonować wybierz **klonowania** na pasku narzędzi. 

2. W **wersji klonowania** okno dialogowe, wpisz nazwę dla nowej wersji, takich jak "0,2".

   ![Okno dialogowe wersji klonowania](./media/luis-how-to-manage-versions/version-clone-version-dialog.png)
 
     > [!NOTE]
     > Wersja identyfikator może zawierać tylko znaki, cyfry lub "." i nie może być dłuższa niż 10 znaków.
 
 Nową wersję przy użyciu określonej nazwy jest utworzona i ustawiona jako aktywnej wersji.

## <a name="set-active-version"></a>Ustaw wersję active

Wybierz wersję z listy, a następnie wybierz **wprowadzić Active** na pasku narzędzi. 

[![Sekcji Zarządzanie, strona wersji upewnij akcji wersji](./media/luis-how-to-manage-versions/versions-other.png "sekcji Zarządzanie, strona wersji upewnij akcji wersji")](./media/luis-how-to-manage-versions/versions-other.png#lightbox)

## <a name="import-version"></a>Wersja importu

1. Wybierz **wersji importu** na pasku narzędzi. 

2. W **importowania nowej wersji** oknie podręcznym, wprowadź nową nazwę w wersji 10 znaków. Należy ustawić identyfikator wersji, jeśli w pliku JSON w wersji już istnieje w aplikacji.

    ![Zarządzanie sekcji stronę wersje, zaimportowaniu nowej wersji](./media/luis-how-to-manage-versions/versions-import-pop-up.png)

    Po zaimportowaniu wersji nowej wersji staje się aktywny wersji.

<a name = "export-version"></a>

## <a name="other-actions"></a>Inne akcje

* Aby **Usuń** wersji, wybierz wersję z listy, a następnie wybierz **Usuń** na pasku narzędzi. Wybierz przycisk **OK**. 
* Aby **Zmień nazwę** wersji, wybierz wersję z listy, a następnie wybierz **Zmień nazwę** na pasku narzędzi. Wprowadź nową nazwę, a następnie wybierz pozycję **gotowe**. 
* Aby **wyeksportować** wersji, wybierz wersję z listy, a następnie wybierz **eksportowania aplikacji** na pasku narzędzi. Plik jest pobierany na komputer lokalny. 

