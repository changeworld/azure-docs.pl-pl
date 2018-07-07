---
title: Omówienie przechowywania danych w usługi LUIS — Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dane są przechowywane w Language Understanding (LUIS)
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/08/2018
ms.author: v-geberr
ms.openlocfilehash: 24e179e24423412a5ff25a64157e273b1a025a6f
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/06/2018
ms.locfileid: "37888698"
---
# <a name="data-storage-and-removal"></a>Magazyn danych i usuwania
Usługa LUIS przechowuje dane są szyfrowane w magazynie danych platformy Azure odpowiadające regionowi, określony przez klucz. Te dane są przechowywane przez 30 dni. 

## <a name="export-and-delete-app"></a>Eksportowanie i usuwanie aplikacji
Użytkownicy mają pełną kontrolę nad [eksportowanie](create-new-app.md#export-app) i [usuwanie](create-new-app.md#delete-app) aplikacji. 

## <a name="utterances-in-an-intent"></a>Wypowiedzi w intencji
Usuń wypowiedzi przykład używane na potrzeby szkolenia [LUIS](luis-reference-regions.md). Jeśli usuniesz wypowiedź przykład z aplikacją usługi LUIS, zostanie usunięty z usługi sieci web usługi LUIS i jest niedostępna w przypadku eksportu.

## <a name="utterances-in-review"></a>Wypowiedzi w przeglądzie
Wypowiedzi można usunąć z listy wypowiedzi użytkowników usługi LUIS sugeruje w  **[stronie wypowiedzi punkt końcowy Przegląd](label-suggested-utterances.md)**. Usuwanie wypowiedzi z tej listy zapobiega sugerowane, ale nie powoduje usunięcia ich z dzienników.

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
> [Więcej informacji na temat eksportowania i usuwania aplikacji](create-new-app.md)