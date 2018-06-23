---
title: Obsługiwane języki w interfejsie API z analizy tekstu - kognitywnych usług Azure | Dokumentacja firmy Microsoft
description: Lista ogólnie dostępna i języka w wersji zapoznawczej obsługuje operacje interfejsu API z analizy tekstu. Ma zastosowanie do analizy wskaźniki nastrojów klientów, wyodrębniania klucza frazy i wykrywania języka.
services: cognitive-services
author: ashmaka
manager: cgronlun
ms.service: cognitive-services
ms.technology: text-analytics
ms.topic: conceptual
ms.date: 05/02/2018
ms.author: ashmaka
ms.openlocfilehash: 2d341cfaf261bea6367bb55dd5d322f419e22d34
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349408"
---
# <a name="supported-languages-in-the-text-analytics-api"></a>Obsługiwane języki w interfejsie API Analiza tekstu

W tym artykule opisano, które języki są obsługiwane dla każdej operacji: wskaźniki nastrojów klientów analizy, wyodrębniania klucza frazy i wykrywania języka.

## <a name="language-detection"></a>Wykrywanie języka

Interfejsu API z analizy tekstu może wykryć maksymalnie 120 różnych języków. Wykrywanie języka zwraca "skrypt" języka. Na przykład frazy "Mam dog" zwróci `en` zamiast `en-US`. Tylko w szczególnych przypadkach jest chińskich, gdzie będzie zwracać możliwości wykrywania języka `zh_CHS` lub `zh_CHT` Jeśli można określić skrypt podanych podano tekstu. W sytuacjach, gdy określony skrypt nie może zostać zidentyfikowana dla Chińskiej wersji językowej dokumentu, to zostanie zwrócona po prostu `zh`.

## <a name="sentiment-analysis-key-phrase-extraction-and-entity-linking"></a>Wskaźniki nastrojów klientów analizy, wyodrębniania klucza frazy i łączenie jednostki

W przypadku analizy wskaźniki nastrojów klientów, wyodrębniania klucza frazy i łączenie jednostki listę obsługiwanych języków jest bardziej selektywnego analizatorów są rozbudowana do uwzględnienia językowe reguły dodatkowe języki.

## <a name="language-list-and-status"></a>Lista języków i stanu

Obsługa języków początkowo jest wdrażana w wersji zapoznawczej, szkołę ogólnie dostępna stanu (GA), niezależnie od siebie i ogólnej usługi Analiza tekstu. Istnieje możliwość dla języków ma pozostać w wersji zapoznawczej, mimo że interfejsu API z analizy tekstu przejścia do ogólnie dostępna.

| Język    | Kod języka | Opinia | Kluczowe frazy | Łączenie jednostek |   Uwagi  |
|:----------- |:-------------:|:---------:|:-----------:|:-----------:|:-----------:
| Duński      | `da`          | ✔ \*     | ✔           |             |     |
| Holenderski       | `nl`          | ✔ \*     | ✔          |             |     |
| Polski     | `en`          | ✔        | ✔           |  ✔ \*   |      |
| Fiński     | `fi`          | ✔ \*     | ✔           |             |     |
| Francuski      | `fr`          | ✔        | ✔           |             |     |
| Niemiecki      | `de`          | ✔ \*     | ✔           |            |     |
| Grecki       | `el`          | ✔ \*     |             |            |     |
| Włoski     | `it`          | ✔ \*     | ✔           |             |     |
| Japoński    | `ja`          |          | ✔           |            |     |
| Koreański      | `ko`          |          | ✔           |            |     |
| Norweski (Bokmål) | `no`          | ✔ \*     |  ✔          |             |     |
| Polski      | `pl`          | ✔ \*     |  ✔          |             |     |
| Portugalski (Portugalia) | `pt-PT`| ✔        |  ✔          |       |`pt` również zaakceptowana|
| Portugalski (Brazylia)   | `pt-BR`|          |  ✔   |         |     |
| Rosyjski     | `ru`          | ✔ \*     | ✔           |             |     |
| Hiszpański     | `es`          | ✔        | ✔           |     |     |
| Szwedzki     | `sv`          | ✔ \*     | ✔           |             |     |
| Turecki     | `tr`          | ✔ \*     |             |             |     |

\* Wskazuje obsługę języka w wersji zapoznawczej

## <a name="see-also"></a>Zobacz także

[Kognitywnych stronę dokumentacji usług](https://docs.microsoft.com/azure/cognitive-services/)   
[Strona produktu usługi kognitywnych](https://azure.microsoft.com/services/cognitive-services/)
