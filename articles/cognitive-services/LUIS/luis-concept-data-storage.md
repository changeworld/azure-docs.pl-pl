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
ms.date: 03/28/2019
ms.author: diberry
ms.openlocfilehash: af0223db944f01346ddcbc1f198ac0c15a426be4
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68564023"
---
# <a name="data-storage-and-removal-in-language-understanding-luis-cognitive-services"></a>Magazyn danych i usuwania w usługach Cognitive Services Language Understanding (LUIS)
Usługa LUIS przechowuje dane są szyfrowane w magazynie danych platformy Azure odpowiadające regionowi, określony przez klucz. Te dane są przechowywane przez 30 dni. 

## <a name="export-and-delete-app"></a>Eksportowanie i usuwanie aplikacji
Użytkownicy mają pełną kontrolę nad [eksportowanie](luis-how-to-start-new-app.md#export-app) i [usuwanie](luis-how-to-start-new-app.md#delete-app) aplikacji. 

## <a name="utterances"></a>Wypowiedzi

Wyrażenia długości mogą być przechowywane w dwóch różnych miejscach. 

* Podczas **procesu tworzenia**wyrażenia długości są tworzone i przechowywane w zamierzeniu. Wyrażenia długości w intencjach są wymagane do pomyślnej aplikacji LUIS. Gdy aplikacja zostanie opublikowana i otrzyma zapytania w punkcie końcowym, ciąg QueryString `log=false`żądania punktu końcowego określa, czy punkt końcowy wypowiedź jest przechowywany. Jeśli punkt końcowy jest przechowywany, stał się częścią aktywnego uczenia wyrażenia długości, którą można znaleźć w sekcji **kompilacja** portalu, w sekcji **Przegląd wyrażenia długości punktu końcowego** . 
* Gdy **przeglądasz punkt końcowy wyrażenia długości**i dodasz wypowiedź do celu, wypowiedź nie jest już przechowywana jako część punktu końcowego wyrażenia długości do zweryfikowania. Jest on dodawany do intencji aplikacji. 

<a name="utterances-in-an-intent"></a>

### <a name="delete-example-utterances-from-an-intent"></a>Usuń przykład wyrażenia długości z zamiaru
Usuń wypowiedzi przykład używane na potrzeby szkolenia [LUIS](luis-reference-regions.md). Jeśli usuniesz wypowiedź przykład z aplikacją usługi LUIS, zostanie usunięty z usługi sieci web usługi LUIS i jest niedostępna w przypadku eksportu.

<a name="utterances-in-review"></a>

### <a name="delete-utterances-in-review-from-active-learning"></a>Usuń wyrażenia długości z aktywnego uczenia

Wypowiedzi można usunąć z listy wypowiedzi użytkowników usługi LUIS sugeruje w  **[stronie wypowiedzi punkt końcowy Przegląd](luis-how-to-review-endpoint-utterances.md)** . Usuwanie wypowiedzi z tej listy zapobiega sugerowane, ale nie powoduje usunięcia ich z dzienników.

Jeśli nie chcesz, aby usługa Active Learning wyrażenia długości, możesz [wyłączyć aktywną naukę](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Wyłączenie usługi Active Learning powoduje także wyłączenie rejestrowania.

### <a name="disable-logging-utterances"></a>Wyłącz rejestrowanie wyrażenia długości
[Wyłączenie aktywnego uczenia](luis-how-to-review-endpoint-utterances.md#disable-active-learning) powoduje wyłączenie rejestrowania.


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
