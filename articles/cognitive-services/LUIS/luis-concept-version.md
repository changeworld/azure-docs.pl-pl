---
title: Zrozumienie przechowywania wersji LUIS - Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać wersji do zarządzania zmianami w opis języka (LUIS)
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/13/2018
ms.author: v-geberr
ms.openlocfilehash: dabe7def2766770b686be3c43d4af4f331dd9577
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266077"
---
# <a name="versions"></a>Wersje
Utwórz różne modele w tej samej aplikacji przy użyciu [wersji](luis-how-to-manage-versions.md). 

## <a name="version-id"></a>Identyfikator wersji
Identyfikator wersji zawiera znaki, cyfry lub '.' i nie może być dłuższa niż 10 znaków.

## <a name="initial-version"></a>Wersja początkowa
Wersja początkowa (0,1) jest aktywna wersja domyślna. 

## <a name="active-version"></a>Wersja aktywna
Aby [Ustaw wersję](luis-how-to-manage-versions.md#set-active-version) jako aktywne oznacza jest obecnie edytowany i testowane w [LUIS] [ LUIS] witryny sieci Web. Ustaw wersję jako aktywny do jego dane dotyczące dostępu, Sprawdź aktualizacje, a także testowanie i opublikować go.

Nazwa wersji aktualnie aktywny jest wyświetlany w panelu, lewa górna po nazwy aplikacji. 

[ ![Zmień wersję active](./media/luis-concept-version/version-in-nav-bar-inline.png) ](./media/luis-concept-version/version-in-nav-bar-expanded.png#lightbox)

## <a name="versions-and-publishing-slots"></a>Wersje i publikowania gniazda
Możesz opublikować etap i produktu miejsc. Inna wersja lub tej samej wersji, może być każdego miejsca. Jest to przydatne sprawdzania zmian między wersjami modelu za pośrednictwem punktu końcowego, który jest dostępny dla robotów lub innych LUIS wywoływania aplikacji. 

## <a name="clone-a-version"></a>Klonowanie wersji
Klonowanie wersji, aby utworzyć kopię istniejącej wersji i zapisać je jako nową wersję. Klonowanie wersja, aby można było używać tej samej zawartości istniejącą wersję jako punkt początkowy dla nowej wersji. Po w klonowania wersję, staje się nowej wersji **active** wersji. 

## <a name="import-and-export-a-version"></a>Importowanie i eksportowanie wersji
Możesz zaimportować wersji na poziomie aplikacji. Ta wersja staje się aktywny wersji i używane identyfikator wersji we właściwości "versionId" pliku aplikacji. Możesz również zaimportować na poziomie wersji do istniejącej aplikacji. Nowa wersja staje się aktywny wersji. 

Można wyeksportować wersję na poziomie aplikacji, lub można wyeksportować wersję na poziomie wersji. Jedyna różnica polega na że eksportowanych wersji poziomie aplikacji jest aktualnie aktywnej wersji znajduje się na poziomie wersji, można wybrać dowolnej wersji, aby wyeksportować na **[ustawienia](luis-how-to-manage-versions.md)** strony. 

Wyeksportowany plik nie zawiera informacji rozpoznane maszyny, ponieważ aplikacja jest retrained po zakończeniu importowania. Wyeksportowany plik nie zawiera współpracownicy — należy dodać te wstecz po wersji jest importowany do nowej aplikacji.

## <a name="export-each-version-as-app-backup"></a>Eksportuj poszczególnych wersji na kopii zapasowej aplikacji
Aby można było utworzyć kopię zapasową aplikacji LUIS, należy wyeksportować poszczególnych wersji na **[ustawienia](luis-how-to-manage-versions.md)** strony.

## <a name="delete-a-version"></a>Usuwanie wersji
Wszystkie wersje, z wyjątkiem wersji active można usunąć z listy wersji na stronie ustawień. 

## <a name="version-availability-at-the-endpoint"></a>Dostępność wersji w punkcie końcowym
Wersje przeszkolone nie są automatycznie dostępne w Twojej aplikacji [punktu końcowego](luis-glossary.md#endpoint). Należy [publikowania](PublishApp.md) lub ponownie opublikować wersji w celu jej mają być dostępne w punkcie końcowym w aplikacji. Można opublikować **przemieszczania** i **produkcji**, co daje maksymalnie dwie wersje aplikacji, które są dostępne w punkcie końcowym. Jeśli potrzebujesz więcej wersji aplikacji, które są dostępne w punkcie końcowym, należy wyeksportować wersję i ponownie zaimportować nowej aplikacji. Nowa aplikacja ma identyfikator inny aplikacji.

## <a name="collaborators"></a>Współpracownicy
Właściciela i wszystkie [współpracownicy](luis-how-to-collaborate.md) mają pełny dostęp do wszystkich wersji aplikacji.

## <a name="next-steps"></a>Kolejne kroki

Jak dodać [versioning](luis-how-to-manage-versions.md) na stronie Ustawienia aplikacji. 

Dowiedz się, jak zaprojektować [intencje](luis-concept-intent.md) do modelu.

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions