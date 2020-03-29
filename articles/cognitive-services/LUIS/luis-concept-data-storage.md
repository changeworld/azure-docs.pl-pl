---
title: Przechowywanie danych — usługa LUIS
titleSuffix: Azure Cognitive Services
description: Usługa LUIS przechowuje dane zaszyfrowane w magazynie danych platformy Azure odpowiadające regionowi określonemu przez klucz.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220018"
---
# <a name="data-storage-and-removal-in-language-understanding-luis-cognitive-services"></a>Przechowywanie i usuwanie danych w usługach cognitive (Language Understanding) (LUIS)
Usługa LUIS przechowuje dane zaszyfrowane w magazynie danych platformy Azure odpowiadające regionowi określonemu przez klucz. Dane te są przechowywane przez 30 dni. 

## <a name="export-and-delete-app"></a>Eksportowanie i usuwanie aplikacji
Użytkownicy mają pełną kontrolę nad [eksportem](luis-how-to-start-new-app.md#export-app) i [usuwaniem](luis-how-to-start-new-app.md#delete-app) aplikacji. 

## <a name="utterances"></a>Wypowiedzi

Wypowiedzi mogą być przechowywane w dwóch różnych miejscach. 

* Podczas **procesu tworzenia**wypowiedzi są tworzone i przechowywane w intencji. Wypowiedzi w intencji są wymagane dla pomyślnej aplikacji usługi LUIS. Po opublikowaniu aplikacji i odbieraniu zapytań w punkcie końcowym, querystring `log=false`żądania punktu końcowego, określa, czy wypowiedź punktu końcowego jest przechowywana. Jeśli punkt końcowy jest przechowywany, staje się częścią aktywnych wypowiedzi uczenia znaleźć w **sekcji Kompilacja** portalu, w **sekcji Przejrzyj wypowiedzi punktu końcowego przeglądu.** 
* Podczas **przeglądania wypowiedzi punktu końcowego**i dodać wypowiedź do intencji, wypowiedź nie jest już przechowywana jako część wypowiedzi punktu końcowego do przeglądu. Jest on dodawany do intencji aplikacji. 

<a name="utterances-in-an-intent"></a>

### <a name="delete-example-utterances-from-an-intent"></a>Usuwanie przykładowych wypowiedzi z intencji

Usuń przykładowe wypowiedzi używane do szkolenia [usługi LUIS](luis-reference-regions.md). Jeśli usuniesz wypowiedź przykład z aplikacji usługi LUIS, zostanie on usunięty z usługi sieci web usługi LUIS i jest niedostępny do eksportu.

<a name="utterances-in-review"></a>

### <a name="delete-utterances-in-review-from-active-learning"></a>Usuwanie wypowiedzi w recenzji z aktywnego uczenia się

Wypowiedzi można usuwać z listy wypowiedzi użytkowników, które usługa LUIS sugeruje na **[stronie Przejrzyj wypowiedzi punktów końcowych.](luis-how-to-review-endpoint-utterances.md)** Usuwanie wypowiedzi z tej listy uniemożliwia im sugerowanie, ale nie usuwa ich z dzienników.

Jeśli nie chcesz, aby aktywne wypowiedzi edukacyjne, można [wyłączyć aktywne uczenie](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Wyłączenie aktywnego uczenia się wyłącza również rejestrowanie.

### <a name="disable-logging-utterances"></a>Wyłączanie wypowiedzi rejestrowania
[Wyłączenie aktywnego uczenia się](luis-how-to-review-endpoint-utterances.md#disable-active-learning) powoduje wyłączenie rejestrowania.


<a name="accounts"></a>

## <a name="delete-an-account"></a>Usuwanie konta
Jeśli usuniesz konto, wszystkie aplikacje zostaną usunięte wraz z ich wypowiedzi przykład i dzienniki. Dane są przechowywane przez 60 dni, zanim konto i dane zostaną trwale usunięte.

Usunięcie konta jest dostępne na stronie **Ustawienia.** Wybierz nazwę konta na prawym górnym pasku nawigacyjnym, aby przejść do strony **Ustawienia.**

## <a name="data-inactivity-as-an-expired-subscription"></a>Brak aktywności danych jako wygasła subskrypcja
Do celów przechowywania i usuwania danych nieaktywna aplikacja usługi LUIS może według uznania firmy Microsoft być _traktowana_ jako wygasła subskrypcja. Aplikacja jest uważana za nieaktywną, jeśli spełnia następujące kryteria w ciągu ostatnich 90 dni: 

* Nie miał **żadnych** połączeń do niego.
* Nie został zmodyfikowany.
* Nie ma przypisanego bieżącego klucza.
* Nie miał zalogować się do niego przez użytkownika.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o eksportowaniu i usuwaniu aplikacji](luis-how-to-start-new-app.md)
