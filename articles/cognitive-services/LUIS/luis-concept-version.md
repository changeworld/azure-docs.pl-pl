---
title: Przechowywanie wersji — LUIS
titleSuffix: Azure Cognitive Services
description: Wersje w programie LUIS są podobne do wersji w tradycyjnych programowaniu. Każda wersja jest migawką w czasie aplikacji. Przed wprowadzeniem zmian w aplikacji Utwórz nową wersję. Łatwiejszym rozwiązaniem jest powracanie do dokładnej aplikacji, a następnie próba nieskórowania i zamiaru aplikacji oraz wyrażenia długości do poprzedniego stanu.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: 2e13efa70d0344defeb306a92ac405439635e929
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68619701"
---
# <a name="understand-how-and-when-to-use-a-luis-version"></a>Informacje o tym, jak i kiedy używać wersji LUIS

Wersje w programie LUIS są podobne do wersji w tradycyjnych programowaniu. Każda wersja jest migawką w czasie aplikacji. Przed wprowadzeniem zmian w aplikacji Utwórz nową wersję. Łatwiej jest wrócić do dokładnej wersji, a następnie próbować usunąć intencje i wyrażenia długości do poprzedniego stanu.

Utwórz różne modele tej samej aplikacji z [wersjami](luis-how-to-manage-versions.md). 

## <a name="version-id"></a>Identyfikator wersji
Identyfikator wersji składa się z znaków, cyfr lub "." i nie może być dłuższa niż 10 znaków.

## <a name="initial-version"></a>Wersja początkowa
Wersja początkowa (0,1) jest domyślną wersją aktywną. 

## <a name="active-version"></a>Aktywna wersja
Aby [ustawić wersję](luis-how-to-manage-versions.md#set-active-version) jako aktywną, jest obecnie edytowana i testowana w witrynie sieci Web [Luis](luis-reference-regions.md) . Ustaw wersję jako aktywną, aby uzyskać dostęp do jej danych, wprowadź aktualizacje, a także Przetestuj i opublikuj ją.

Nazwa aktualnie aktywnej wersji jest wyświetlana w lewym panelu po nazwie aplikacji. 

[![Zmień aktywną wersję](./media/luis-concept-version/version-in-nav-bar-inline.png)](./media/luis-concept-version/version-in-nav-bar-expanded.png#lightbox)

## <a name="versions-and-publishing-slots"></a>Wersje i miejsca publikowania
Publikujesz na etapie i w gnieździe produktu. Każde gniazdo może mieć inną wersję lub tę samą wersję. Jest to przydatne w przypadku sprawdzania zmian wersji modelu za pośrednictwem punktu końcowego, który jest dostępny dla botów lub innych aplikacji wywołujących LUIS. 

## <a name="clone-a-version"></a>Klonowanie wersji
Sklonuj wersję, aby utworzyć kopię istniejącej wersji i zapisać ją jako nową wersję. Sklonuj wersję, aby użyć tej samej zawartości istniejącej wersji jako punktu wyjścia dla nowej wersji. Po sklonowaniu wersji Nowa wersja zostanie **uaktywniona** . 

## <a name="import-and-export-a-version"></a>Importowanie i eksportowanie wersji
Możesz zaimportować wersję na poziomie aplikacji. Ta wersja jest wersją aktywną i używała identyfikatora wersji we właściwości "versionId" pliku aplikacji. Możesz również zaimportować na poziomie wersji do istniejącej aplikacji. Nowa wersja zostanie uaktywniona. 

Możesz wyeksportować wersję na poziomie aplikacji lub wyeksportować wersję na poziomie wersji. Jedyną różnicą jest to, że wersja wyeksportowana na poziomie aplikacji jest obecnie aktywna, a na poziomie wersji można wybrać dowolną wersję do eksportowania na stronie **[Ustawienia](luis-how-to-manage-versions.md)** . 

Wyeksportowany plik nie zawiera informacji o maszynach, ponieważ aplikacja jest ponownie przeszkolna po zaimportowaniu. Wyeksportowany plik nie zawiera współpracowników — należy dodać je ponownie, gdy wersja zostanie zaimportowana do nowej aplikacji.

## <a name="export-each-version-as-app-backup"></a>Eksportuj każdą wersję jako kopię zapasową aplikacji
Aby utworzyć kopię zapasową aplikacji LUIS, wyeksportuj poszczególne wersje na stronie **[Ustawienia](luis-how-to-manage-versions.md)** .

## <a name="delete-a-version"></a>Usuń wersję
Wszystkie wersje, z wyjątkiem aktywnej wersji, można usunąć z listy wersje na stronie Ustawienia. 

## <a name="version-availability-at-the-endpoint"></a>Dostępność wersji w punkcie końcowym
Przeszkolone wersje nie są automatycznie dostępne w [punkcie końcowym](luis-glossary.md#endpoint)aplikacji. Musisz [opublikować](luis-how-to-publish-app.md) lub ponownie opublikować wersję, aby była dostępna w punkcie końcowym aplikacji. Możesz publikować w ramach **przemieszczania** i **produkcji**, oferując do dwóch wersji aplikacji dostępnych w punkcie końcowym. Jeśli potrzebujesz więcej wersji aplikacji dostępnych w punkcie końcowym, należy wyeksportować wersję i ponownie zaimportować ją do nowej aplikacji. Nowa aplikacja ma inny identyfikator aplikacji.

## <a name="collaborators"></a>Współpracownicy
Właściciel i wszyscy [współpracownicy](luis-how-to-collaborate.md) mają pełny dostęp do wszystkich wersji aplikacji.

## <a name="next-steps"></a>Kolejne kroki

Zobacz, jak dodać [przechowywanie wersji](luis-how-to-manage-versions.md) na stronie Ustawienia aplikacji. 

Dowiedz się, [](luis-concept-intent.md) jak zaprojektować intencje w modelu.
