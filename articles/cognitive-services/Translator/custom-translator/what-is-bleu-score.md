---
title: Co to jest ocena BELEU? — Niestandardowy w usłudze Translator
titleSuffix: Azure Cognitive Services
description: BELEU jest pomiar różnice między automatycznego tłumaczenia i co najmniej jedno tłumaczenie utworzone przez człowieka odwołanie zdania tego samego źródła. Algorytm BELEU porównuje kolejnych fraz automatycznego tłumaczenia oraz kolejnych oznaczenia znajduje się w tłumaczeniu odwołania i zlicza liczbę dopasowań w sposób ważonych.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.component: custom-translator
ms.topic: article
ms.date: 11/13/2018
ms.author: v-rada
ms.openlocfilehash: 887eaeac5d74aafd9839495939a2079b288738e4
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2018
ms.locfileid: "51627684"
---
# <a name="what-is-a-bleu-score"></a>Co to jest ocena BELEU?

[BELEU (Understudy dwujęzyczny oceny)](https://en.wikipedia.org/wiki/BLEU) jest pomiar różnice między automatycznego tłumaczenia i co najmniej jedno tłumaczenie utworzone przez człowieka odwołanie zdania tego samego źródła.

## <a name="scoring-process"></a>Proces oceny

Algorytm BELEU porównuje kolejnych fraz automatycznego tłumaczenia oraz kolejnych oznaczenia znajduje się w tłumaczeniu odwołania i zlicza liczbę dopasowań w sposób ważonych. Te dopasowaniach rozróżniana niezależnie od pozycji. Wyższy stopień dopasowania oznacza wyższy stopień podobieństwa tłumaczenia dokumentacja i wyższą ocenę. Zrozumiałość informacji i poprawność gramatyczną nie są brane pod uwagę.

## <a name="how-bleu-works"></a>Jak działa BELEU?

Firmy BELEU jest mają związek on również z ludzi orzeczenia uśrednianie błędy orzeczenia zdania za pośrednictwem korpus testów, zamiast próby należy opracować dokładne orzeczenia przez ludzi dla każdego zdania.

Jest bardziej rozległe dyskusję na temat oceny BELEU [tutaj](https://youtu.be/-UqDljMymMg).

BELEU wyniki zależą od silnie szeroki zakres domeny użytkownika spójność danych testowych dzięki szkoleniom i dostrajanie danych i ilość danych dostępnych do uczenia. Jeśli swoje modele są przeszkoleni na wąski domeny, a danych szkoleniowych jest spójna ze swoimi danymi testu, można oczekiwać, że Wysoka ocena BELEU.

>[!NOTE]
>Porównanie wyników BELEU jest uzasadnione tylko w przypadku, gdy wyniki BELEU są porównywane z tego samego zestawu testów, tej samej pary języka i tego samego silnika MT. Wynik BELEU z zestawu testowego różnych jest powiązany z różnić.
