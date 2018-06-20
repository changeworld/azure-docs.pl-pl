---
title: Zrozumienie przechowywania danych w LUIS - Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dane są przechowywane w opis języka (LUIS)
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/08/2018
ms.author: v-geberr
ms.openlocfilehash: f235c787e7d2064696e5421219a297d914b5882d
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266009"
---
# <a name="data-storage-and-removal"></a>Magazyn danych i usuwania
LUIS przechowuje dane zaszyfrowane w magazynie danych Azure odpowiadający określonego za pomocą klucza regionu. Te dane są przechowywane przez 30 dni. 

## <a name="export-and-delete-app"></a>Eksportowanie i Usuń aplikację
Użytkownicy mają pełną kontrolę nad [eksportowanie](create-new-app.md#export-app) i [usuwanie](create-new-app.md#delete-app) aplikacji. 

## <a name="utterances-in-an-intent"></a>Zniesławiających w celem
Usuń zniesławiających przykład używany do trenowania [LUIS][LUIS]. Usunięcie z aplikacji LUIS utterance przykład, zostanie usunięta z usługi sieci web LUIS i jest niedostępny dla operacji eksportowania.

## <a name="utterances-in-review"></a>Zniesławiających w przeglądzie
Zniesławiających można usunąć z listy zniesławiających użytkownika, które sugeruje LUIS w  **[stronę zniesławiających punktu końcowego przeglądu](label-suggested-utterances.md)**. Usunięcie zniesławiających z tej listy uniemożliwia sugerowane, ale nie powoduje usunięcia ich z dzienników.

## <a name="accounts"></a>Konta
Jeśli usuniesz konto, wszystkie aplikacje są usuwane, wraz z ich zniesławiających przykład i dzienniki. Dane są przechowywane przez 60 dni przed konta, a dane są trwale usuwane.

Usunięcie konta jest dostępne z **ustawienia** strony. Wybierz nazwę konta w górnym prawym pasku nawigacyjnym na uzyskanie dostępu do **ustawienia** strony.

## <a name="data-inactivity-as-an-expired-subscription"></a>Aktywności danych jako wygasłych subskrypcji
Na potrzeby przechowywania danych i usuwanie nieaktywne aplikacji LUIS może na _uznania firmy Microsoft_ traktowane jako wygasłych subskrypcji. Aplikacja jest uznawany za nieaktywny, jeśli w ciągu ostatnich 90 dni spełnia następujące kryteria: 

* Wystąpiła **nie** wywołania go.
* Nie został zmodyfikowany.
* Nie bieżącego klucza przypisano do niej.
* Nie ma użytkownika, zaloguj się do niego.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Więcej informacji na temat eksportowania i usuwanie aplikacji](create-new-app.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions