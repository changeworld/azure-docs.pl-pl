---
title: Magazyn danych — LUIS
titleSuffix: Azure Cognitive Services
description: Usługa LUIS przechowuje dane są szyfrowane w magazynie danych platformy Azure odpowiadające regionowi, określony przez klucz.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: e3028a9f046edb4013d8a2d6ec88d8c3cb2ca489
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79220018"
---
# <a name="data-storage-and-removal-in-language-understanding-luis-cognitive-services"></a>Magazyn danych i usuwania w usługach Cognitive Services Language Understanding (LUIS)
Usługa LUIS przechowuje dane są szyfrowane w magazynie danych platformy Azure odpowiadające regionowi, określony przez klucz. Te dane są przechowywane przez 30 dni. 

## <a name="export-and-delete-app"></a>Eksportowanie i usuwanie aplikacji
Użytkownicy mają pełną kontrolę nad [eksportowaniem](luis-how-to-start-new-app.md#export-app) i [usuwaniem](luis-how-to-start-new-app.md#delete-app) aplikacji. 

## <a name="utterances"></a>Wypowiedzi

Wyrażenia długości mogą być przechowywane w dwóch różnych miejscach. 

* Podczas **procesu tworzenia**wyrażenia długości są tworzone i przechowywane w zamierzeniu. Wyrażenia długości w intencjach są wymagane do pomyślnej aplikacji LUIS. Gdy aplikacja zostanie opublikowana i otrzyma zapytania w punkcie końcowym, ciąg QueryString żądania punktu końcowego `log=false`, określa, czy punkt końcowy wypowiedź jest przechowywany. Jeśli punkt końcowy jest przechowywany, stał się częścią aktywnego uczenia wyrażenia długości, którą można znaleźć w sekcji **kompilacja** portalu, w sekcji **Przegląd wyrażenia długości punktu końcowego** . 
* Gdy **przeglądasz punkt końcowy wyrażenia długości**i dodasz wypowiedź do celu, wypowiedź nie jest już przechowywana jako część punktu końcowego wyrażenia długości do zweryfikowania. Jest on dodawany do intencji aplikacji. 

<a name="utterances-in-an-intent"></a>

### <a name="delete-example-utterances-from-an-intent"></a>Usuń przykład wyrażenia długości z zamiaru

Usuń przykład wyrażenia długości używany do uczenia [Luis](luis-reference-regions.md). Jeśli usuniesz wypowiedź przykład z aplikacją usługi LUIS, zostanie usunięty z usługi sieci web usługi LUIS i jest niedostępna w przypadku eksportu.

<a name="utterances-in-review"></a>

### <a name="delete-utterances-in-review-from-active-learning"></a>Usuń wyrażenia długości z aktywnego uczenia

Wyrażenia długości można usunąć z listy wyrażenia długości użytkownika, którą LUIS sugeruje na **[stronie Recenzja punktu końcowego wyrażenia długości](luis-how-to-review-endpoint-utterances.md)** . Usuwanie wypowiedzi z tej listy zapobiega sugerowane, ale nie powoduje usunięcia ich z dzienników.

Jeśli nie chcesz, aby usługa Active Learning wyrażenia długości, możesz [wyłączyć aktywną naukę](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Wyłączenie usługi Active Learning powoduje także wyłączenie rejestrowania.

### <a name="disable-logging-utterances"></a>Wyłącz rejestrowanie wyrażenia długości
[Wyłączenie aktywnego uczenia](luis-how-to-review-endpoint-utterances.md#disable-active-learning) powoduje wyłączenie rejestrowania.


<a name="accounts"></a>

## <a name="delete-an-account"></a>Usuwanie konta
Jeśli usuniesz konto, wszystkie aplikacje zostaną usunięte wraz z ich wypowiedzi przykład i dzienniki. Dane są przechowywane przez 60 dni, konto i dane zostaną trwale usunięte.

Usuwanie konta jest dostępne na stronie **Ustawienia** . Wybierz nazwę swojego konta w prawym górnym pasku nawigacyjnym, aby przejść do strony **ustawień** .

## <a name="data-inactivity-as-an-expired-subscription"></a>Braku danych, co w przypadku wygasłych subskrypcji
Na potrzeby przechowywania i usuwania danych nieaktywna aplikacja LUIS może być traktowana jako niezależna od _firmy Microsoft_ jako wygasła subskrypcja. Aplikacja jest uznawany za nieaktywny, jeśli dana jednostka spełnia następujące kryteria w ciągu ostatnich 90 dni: 

* Nie ma **żadnych** wywołań.
* Nie został zmodyfikowany.
* Nie ma bieżącego klucza przypisany do niego.
* Nie zostało poddane użytkownika, zaloguj się do niego.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat eksportowania i usuwania aplikacji](luis-how-to-start-new-app.md)
