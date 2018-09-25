---
title: Magazyn danych w usłudze usługi LUIS — Language Understanding
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak dane są przechowywane w Language Understanding (LUIS). Usługa LUIS przechowuje dane są szyfrowane w magazynie danych platformy Azure odpowiadające regionowi, określony przez klucz.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 6d42246b3078e0f4684dfe29bca720e8f8d779cd
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "47034709"
---
# <a name="data-storage-and-removal-in-language-understanding-luis-cognitive-services"></a>Magazyn danych i usuwania w usługach Cognitive Services Language Understanding (LUIS)
Usługa LUIS przechowuje dane są szyfrowane w magazynie danych platformy Azure odpowiadające regionowi, określony przez klucz. Te dane są przechowywane przez 30 dni. 

## <a name="export-and-delete-app"></a>Eksportowanie i usuwanie aplikacji
Użytkownicy mają pełną kontrolę nad [eksportowanie](luis-how-to-start-new-app.md#export-app) i [usuwanie](luis-how-to-start-new-app.md#delete-app) aplikacji. 

## <a name="utterances-in-an-intent"></a>Wypowiedzi w intencji
Usuń wypowiedzi przykład używane na potrzeby szkolenia [LUIS](luis-reference-regions.md). Jeśli usuniesz wypowiedź przykład z aplikacją usługi LUIS, zostanie usunięty z usługi sieci web usługi LUIS i jest niedostępna w przypadku eksportu.

## <a name="utterances-in-review"></a>Wypowiedzi w przeglądzie
Wypowiedzi można usunąć z listy wypowiedzi użytkowników usługi LUIS sugeruje w  **[stronie wypowiedzi punkt końcowy Przegląd](luis-how-to-review-endoint-utt.md)**. Usuwanie wypowiedzi z tej listy zapobiega sugerowane, ale nie powoduje usunięcia ich z dzienników.

## <a name="accounts"></a>Konta
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