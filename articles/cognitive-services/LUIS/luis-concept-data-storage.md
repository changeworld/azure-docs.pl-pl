---
title: Magazyn danych
titleSuffix: Language Understanding - Azure Cognitive Services
description: Usługa LUIS przechowuje dane są szyfrowane w magazynie danych platformy Azure odpowiadające regionowi, określony przez klucz.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: diberry
ms.openlocfilehash: a1093c2a6303b453a17a52058303913de5ecfa8d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60812942"
---
# <a name="data-storage-and-removal-in-language-understanding-luis-cognitive-services"></a>Magazyn danych i usuwania w usługach Cognitive Services Language Understanding (LUIS)
Usługa LUIS przechowuje dane są szyfrowane w magazynie danych platformy Azure odpowiadające regionowi, określony przez klucz. Te dane są przechowywane przez 30 dni. 

## <a name="export-and-delete-app"></a>Eksportowanie i usuwanie aplikacji
Użytkownicy mają pełną kontrolę nad [eksportowanie](luis-how-to-start-new-app.md#export-app) i [usuwanie](luis-how-to-start-new-app.md#delete-app) aplikacji. 

## <a name="utterances"></a>Wypowiedzi

Wypowiedzi mogą być przechowywane w dwóch różnych miejscach. 

* Podczas **procesu tworzenia**, wypowiedzi są tworzone i przechowywane w profilu konwersji. Wypowiedzi w opcji są wymagane do pomyślnego aplikacją usługi LUIS. Gdy aplikacja została opublikowana i otrzymuje kwerendy w punkcie końcowym, ciąg zapytania żądania punktu końcowego, `log=false`, określa, czy jest przechowywana wypowiedź punktu końcowego. Punkt końcowy jest przechowywany, staje się częścią wypowiedzi aktywne uczenie w **kompilacji** sekcji w portalu w **Przejrzyj wypowiedzi punktu końcowego** sekcji. 
* Gdy możesz **Przejrzyj wypowiedzi punktu końcowego**i Dodaj wypowiedź na intencje, wypowiedź już nie są przechowywane jako część wypowiedzi punkt końcowy do przeglądu. Jest ono dodane do intencji aplikacji. 

<a name="utterances-in-an-intent"></a>

### <a name="delete-example-utterances-from-an-intent"></a>Usuwanie wypowiedzi przykład intencji
Usuń wypowiedzi przykład używane na potrzeby szkolenia [LUIS](luis-reference-regions.md). Jeśli usuniesz wypowiedź przykład z aplikacją usługi LUIS, zostanie usunięty z usługi sieci web usługi LUIS i jest niedostępna w przypadku eksportu.

<a name="utterances-in-review"></a>

### <a name="delete-utterances-in-review-from-active-learning"></a>Usuwanie wypowiedzi w przeglądzie aktywne uczenie

Wypowiedzi można usunąć z listy wypowiedzi użytkowników usługi LUIS sugeruje w  **[stronie wypowiedzi punkt końcowy Przegląd](luis-how-to-review-endpoint-utterances.md)**. Usuwanie wypowiedzi z tej listy zapobiega sugerowane, ale nie powoduje usunięcia ich z dzienników.

Jeśli nie chcesz wypowiedzi aktywne uczenie, możesz to zrobić [Wyłącz aktywne uczenie](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Również wyłączenie aktywna nauka powoduje wyłączenie rejestrowania.

### <a name="disable-logging-utterances"></a>Wyłącz rejestrowanie wypowiedzi
[Wyłączanie aktywne uczenie](luis-how-to-review-endpoint-utterances.md#disable-active-learning) wyłącza rejestrowanie zdarzeń.


<a name="accounts"></a>

## <a name="delete-an-account"></a>Usuwanie konta
Jeśli usuniesz konto, wszystkie aplikacje zostaną usunięte wraz z ich wypowiedzi przykład i dzienniki. Dane są przechowywane przez 60 dni, konto i dane zostaną trwale usunięte.

Trwa usuwanie konta jest dostępne z **ustawienia** strony. Wybierz nazwę swojego konta w górnym prawym pasku nawigacyjnym, aby uzyskać dostęp do **ustawienia** strony.

## <a name="data-inactivity-as-an-expired-subscription"></a>Braku danych, co w przypadku wygasłych subskrypcji
Na potrzeby przechowywania danych i usuwania nieaktywnych aplikacją usługi LUIS może na _według uznania firmy Microsoft_ być traktowane jako wygasłej subskrypcji. Aplikacja jest uznawany za nieaktywny, jeśli dana jednostka spełnia następujące kryteria w ciągu ostatnich 90 dni: 

* Nastąpiła **nie** wywołania do niego.
* Nie został zmodyfikowany.
* Nie ma bieżącego klucza przypisany do niego.
* Nie zostało poddane użytkownika, zaloguj się do niego.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Więcej informacji na temat eksportowania i usuwania aplikacji](luis-how-to-start-new-app.md)
