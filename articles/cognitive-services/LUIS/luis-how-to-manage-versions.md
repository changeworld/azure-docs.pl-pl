---
title: Zarządzanie wersjami w aplikacjach usługi LUIS
titleSuffix: Azure Cognitive Services
description: Wersje umożliwiają tworzenie i publikowanie różnych modeli. Dobrą praktyką jest, aby sklonować bieżącego aktywnego modelu do innej wersji aplikacji przed wprowadzeniem zmian w modelu.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: db461191b70aabc322e570ecc814a076c21206f2
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "47033791"
---
# <a name="manage-versions"></a>Zarządzanie wersjami

Wersje umożliwiają tworzenie i publikowanie różnych modeli. Dobrym rozwiązaniem jest klonowanie bieżącego aktywnego modelu z innym [wersji](luis-concept-version.md) aplikacji przed wprowadzeniem zmian w modelu. 

Aby pracować z wersjami, Otwórz aplikację, wybierając jego nazwę na **Moje aplikacje** strony, a następnie wybierz pozycję **Zarządzaj** w górnym pasku, a następnie zaznacz **wersji** w nawigacji po lewej stronie. 

Listę wersji pokazują, które wersje są publikowane, gdzie są publikowane i która wersja jest obecnie aktywny. 

[![](./media/luis-how-to-manage-versions/versions-import.png "Zarządzanie sekcję strony z wersjami")](./media/luis-how-to-manage-versions/versions-import.png#lightbox)

## <a name="clone-a-version"></a>Klonowanie wersji

1. Wybierz wersję chcesz sklonować wybierz **klonowania** na pasku narzędzi. 

2. W **wersji klonowania** okno dialogowe, wpisz nazwę dla nowej wersji, takich jak "0,2".

   ![Okno dialogowe wersji klonowania](./media/luis-how-to-manage-versions/version-clone-version-dialog.png)
 
     > [!NOTE]
     > Wersja identyfikator może zawierać tylko znaki, cyfry lub "." i nie może być dłuższa niż 10 znaków.
 
 Nową wersję przy użyciu określonej nazwy jest utworzona i ustawiona jako aktywnej wersji.

## <a name="set-active-version"></a>Ustaw wersję active

Wybierz wersję z listy, a następnie wybierz **wprowadzić Active** na pasku narzędzi. 

[![](./media/luis-how-to-manage-versions/versions-other.png "Zarządzanie sekcję strony z wersjami")](./media/luis-how-to-manage-versions/versions-other.png#lightbox)

## <a name="import-version"></a>Wersja importu

1. Wybierz **wersji importu** na pasku narzędzi. 

2. W **importowania nowej wersji** oknie podręcznym, wprowadź nową nazwę w wersji 10 znaków. Należy ustawić identyfikator wersji, jeśli w pliku JSON w wersji już istnieje w aplikacji.

    ![Zarządzanie sekcję strony z wersjami](./media/luis-how-to-manage-versions/versions-import-pop-up.png)

    Po zaimportowaniu wersji nowej wersji staje się aktywny wersji.

<a name = "export-version"></a>

## <a name="other-actions"></a>Inne akcje

* Aby **Usuń** wersji, wybierz wersję z listy, a następnie wybierz **Usuń** na pasku narzędzi. Wybierz przycisk **OK**. 
* Aby **Zmień nazwę** wersji, wybierz wersję z listy, a następnie wybierz **Zmień nazwę** na pasku narzędzi. Wprowadź nową nazwę, a następnie wybierz pozycję **gotowe**. 
* Aby **wyeksportować** wersji, wybierz wersję z listy, a następnie wybierz **eksportowania aplikacji** na pasku narzędzi. Plik jest pobierany na komputer lokalny. 

