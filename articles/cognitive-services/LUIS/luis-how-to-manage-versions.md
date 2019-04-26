---
title: Zarządzanie wersjami
titleSuffix: Language Understanding - Azure Cognitive Services
description: Wersje umożliwiają tworzenie i publikowanie różnych modeli. Dobrą praktyką jest, aby sklonować bieżącego aktywnego modelu do innej wersji aplikacji przed wprowadzeniem zmian w modelu.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 04/16/2019
ms.author: diberry
ms.openlocfilehash: f919651cf39d1f2c48fca87da935e49e3affa79f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60198907"
---
# <a name="use-versions-to-edit-and-test-without-impacting-staging-or-production-apps"></a>Użyj wersji do edycji i przetestować bez wywierania wpływu na aplikacje przejściowych lub produkcyjnych

Wersje umożliwiają tworzenie i publikowanie różnych modeli. Dobrym rozwiązaniem jest klonowanie bieżącego aktywnego modelu z innym [wersji](luis-concept-version.md) aplikacji przed wprowadzeniem zmian w modelu. 

Aby pracować z wersjami, Otwórz aplikację, wybierając jego nazwę na **Moje aplikacje** strony, a następnie wybierz pozycję **Zarządzaj** w górnym pasku, a następnie zaznacz **wersji** w nawigacji po lewej stronie. 

Listę wersji pokazuje, które wersje są publikowane, gdzie są publikowane i która wersja jest obecnie aktywny. 

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

### <a name="import-errors"></a>Błędy importowania

* Tokenizator błędy: Jeśli otrzymasz **błąd tokenizatora** podczas importowania, próbujesz zaimportować wersję, który używa innego [tokenizatora](luis-language-support.md#custom-tokenizer-versions) niż aktualnie używa aplikacja. Aby rozwiązać ten problem, zobacz [migracji między wersjami tokenizatora](luis-language-support.md#migrating-between-tokenizer-versions).

<a name = "export-version"></a>

## <a name="other-actions"></a>Inne akcje

* Aby **Usuń** wersji, wybierz wersję z listy, a następnie wybierz **Usuń** na pasku narzędzi. Wybierz przycisk **OK**. 
* Aby **Zmień nazwę** wersji, wybierz wersję z listy, a następnie wybierz **Zmień nazwę** na pasku narzędzi. Wprowadź nową nazwę, a następnie wybierz pozycję **gotowe**. 
* Aby **wyeksportować** wersji, wybierz wersję z listy, a następnie wybierz **eksportowania aplikacji** na pasku narzędzi. Wybierz opcję ciąg JSON do eksportu do tworzenia kopii zapasowych, wybierz **eksportu dla kontenera** do [za pomocą tej aplikacji w kontenerze usługi LUIS](luis-container-howto.md).  

