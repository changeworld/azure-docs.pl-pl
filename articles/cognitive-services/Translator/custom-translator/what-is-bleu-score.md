---
title: Co to jest wynik BLEU? - Tłumacz niestandardowy
titleSuffix: Azure Cognitive Services
description: BLEU jest miarą różnic między tłumaczeniem maszynowym a tłumaczeniami referencyjnymi tego samego zdania źródłowego.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: swmachan
ms.openlocfilehash: 85c4ee27a828a05c64ca6cbf84bff438535328be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73647371"
---
# <a name="what-is-a-bleu-score"></a>Co to jest wynik BLEU?

[BLEU (Dwujęzyczna ocena understudy)](https://en.wikipedia.org/wiki/BLEU) jest miarą różnic między automatycznym tłumaczeniem a jednym lub kilkoma tłumaczeniami referencyjnymi tego samego zdania źródłowego utworzonym przez człowieka.

## <a name="scoring-process"></a>Proces oceniania

Algorytm BLEU porównuje kolejne frazy automatycznego tłumaczenia z kolejnymi frazami, które znajdzie w tłumaczeniu referencyjnym, i zlicza liczbę dopasowań w sposób ważony. Te mecze są niezależne od pozycji. Wyższy stopień dopasowania wskazuje wyższy stopień podobieństwa do tłumaczenia referencyjnego i wyższy wynik. Nie uwzględnia się zrozumiałości i poprawności gramatycznej.

## <a name="how-bleu-works"></a>Jak działa BLEU?

Siła BLEU jest to, że koreluje dobrze z ludzkiego osądu uśredniając błędy poszczególnych wyroków zdanie nad korpusem testowym, a nie próbuje opracować dokładny ludzki osąd dla każdego zdania.

Szerszej dyskusji na temat wyników BLEU jest [tutaj](https://youtu.be/-UqDljMymMg).

Wyniki BLEU zależą w dużym stopniu od zakresu domeny, spójności danych testowych z danymi szkolenia i dostrajania oraz ilości danych dostępnych do przeszkolenia. Jeśli modele zostały przeszkolone w wąskiej domenie, a dane szkoleniowe są zgodne z danymi testowymi, można oczekiwać wysokiego wyniku BLEU.

>[!NOTE]
>Porównanie wyników BLEU jest uzasadnione tylko wtedy, gdy wyniki BLEU są porównywane z tym samym zestawem testów, tą samą parą językową i tym samym aparatem MT. Wynik BLEU z innego zestawu testów jest zobowiązany do różnych.
