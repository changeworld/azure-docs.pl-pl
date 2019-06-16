---
title: Obsługa wersji
titleSuffix: Language Understanding - Azure Cognitive Services
description: Wersje w LUIS są podobne do wersji w tradycyjnego programowania. Każda wersja jest migawką w czasie aplikacji. Przed wprowadzeniem zmian w aplikacji, należy utworzyć nową wersję. Łatwiej można wrócić do dokładnie aplikacji jest następnie próbuje unpeel i przeznaczenie aplikacji i wypowiedzi do poprzedniego stanu.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/15/2019
ms.author: diberry
ms.openlocfilehash: 9da79e5b744f8ba70c0e265f0d1f0126b37eba49
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60509693"
---
# <a name="understand-how-and-when-to-use-a-luis-version"></a>Zrozumienie, jak i kiedy używać wersji usługi LUIS

Wersje w LUIS są podobne do wersji w tradycyjnego programowania. Każda wersja jest migawką w czasie aplikacji. Przed wprowadzeniem zmian w aplikacji, należy utworzyć nową wersję. Jest łatwiejsze, aby wrócić do dokładnie wersji, a następnie spróbuj usunąć intencje i wypowiedzi do poprzedniego stanu.

Utwórz różne modele taką samą aplikację na [wersji](luis-how-to-manage-versions.md). 

## <a name="version-id"></a>Identyfikator wersji
Identyfikator wersji zawiera znaki, cyfry lub "." i nie może być dłuższa niż 10 znaków.

## <a name="initial-version"></a>Wersja początkowa
Wersja początkowa (0,1) to domyślna wersja active. 

## <a name="active-version"></a>Wersja aktywna
Aby [Ustaw wersję](luis-how-to-manage-versions.md#set-active-version) jako aktywny oznacza jest obecnie edytować i testowane na platformie [LUIS](luis-reference-regions.md) witryny sieci Web. Ustaw wersję jako aktywny, aby uzyskać dostęp do swoich danych, wprowadzenia aktualizacji, jak również testowanie i opublikować go.

Nazwa aktualnie aktywnego wersji jest wyświetlane w panelu po lewej stronie, górna po nazwie aplikacji. 

[![Zmień wersję active](./media/luis-concept-version/version-in-nav-bar-inline.png)](./media/luis-concept-version/version-in-nav-bar-expanded.png#lightbox)

## <a name="versions-and-publishing-slots"></a>Wersje i gniazda publikowania
Opublikuj etapu i produktu miejsca. Każdego miejsca może mieć inną wersję lub tej samej wersji. Jest to przydatne w celu sprawdzenia zmian między wersjami modelu za pośrednictwem punktu końcowego, który jest dostępny do botów lub inne usługi LUIS wywołanie aplikacji. 

## <a name="clone-a-version"></a>Klonowanie wersji
Klonuj wersji, aby utworzyć kopię istniejącej wersji i zapisać ją jako nową wersję. Klonuj wersji, aby użyć tej samej zawartości istniejącej wersji jako punktu wyjścia dla nowej wersji. Po klonowania wersji staje się nowej wersji **active** wersji. 

## <a name="import-and-export-a-version"></a>Importowanie i eksportowanie wersji
Można zaimportować wersji na poziomie aplikacji. Ta wersja staje się aktywny wersji i używane identyfikator wersji we właściwości "versionId" pliku aplikacji. Można również zaimportować się na poziomie wersji, do istniejącej aplikacji. Nowa wersja staje się aktywny wersji. 

Możesz wyeksportować wersji na poziomie aplikacji, lub możesz wyeksportować wersji na poziomie wersji. Jedyna różnica polega na czy wersja wyeksportowanych poziomie aplikacji jest aktualnie aktywne wersji znajduje się na poziomie wersji, możesz wybrać dowolnej wersji, aby wyeksportować na **[ustawienia](luis-how-to-manage-versions.md)** strony. 

Wyeksportowany plik nie zawiera informacji przedstawiono maszyny, ponieważ aplikacja jest retrained po ich zaimportowaniu. Wyeksportowany plik nie zawiera współpracowników — należy dodać te wstecz po wersji są importowane do nowej aplikacji.

## <a name="export-each-version-as-app-backup"></a>Eksportowanie każdej wersji jako kopia zapasowa aplikacji
Aby utworzyć kopię zapasową aplikacją usługi LUIS, należy wyeksportować poszczególnych wersji na **[ustawienia](luis-how-to-manage-versions.md)** strony.

## <a name="delete-a-version"></a>Usuwanie wersji
Wszystkie wersje, z wyjątkiem aktywnej wersji można usunąć z listy wersji na stronie Ustawienia. 

## <a name="version-availability-at-the-endpoint"></a>Dostępność wersji w punkcie końcowym
Uczony wersje nie są automatycznie dostępne w Twojej aplikacji [punktu końcowego](luis-glossary.md#endpoint). Należy najpierw [publikowania](luis-how-to-publish-app.md) lub ponownie opublikować wersję w kolejności, aby była dostępna w punkcie końcowym w aplikacji. Możesz opublikować **przemieszczania** i **produkcji**, co daje maksymalnie dwie wersje aplikacji, które są dostępne w punkcie końcowym. Jeśli potrzebujesz więcej wersji aplikacji, które są dostępne w punkcie końcowym, możesz wyeksportować wersję i ponownie zaimportować do nowej aplikacji. Nowa aplikacja ma identyfikator innej aplikacji.

## <a name="collaborators"></a>Współpracownicy
Właściciela i wszystkie [współpracowników](luis-how-to-collaborate.md) mają pełny dostęp do wszystkich wersji aplikacji.

## <a name="next-steps"></a>Kolejne kroki

Zobacz, jak dodać [versioning](luis-how-to-manage-versions.md) na stronie Ustawienia aplikacji. 

Dowiedz się, jak projektować [intencji](luis-concept-intent.md) do modelu.
