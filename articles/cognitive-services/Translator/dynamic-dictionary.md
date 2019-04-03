---
title: Dynamiczny słownik — interfejs API tekstu usługi Translator
titlesuffix: Azure Cognitive Services
description: Jak używać funkcji dynamiczny słownik interfejsu API tłumaczenia tekstu.
services: cognitive-services
author: Jann-Skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: v-jansko
ms.openlocfilehash: 17ec3732a9d2ea59274989a5ebb052f1b73d701f
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58885473"
---
# <a name="how-to-use-the-dynamic-dictionary-feature-of-the-translator-text-api"></a>Jak używać funkcji dynamiczny słownik interfejs API tekstu usługi Translator

Jeśli znasz już tłumaczeń, które mają zostać zastosowane do wyrazu lub frazy, możesz podać go jako kod znaczników w żądaniu. Dynamiczny słownik jest tylko bezpieczne dla rzeczowniki złożone, np. prawidłowe nazwy i nazwy produktu.

**Składnia:**

< tłumaczenia mstrans:dictionary = "tłumaczenia frazę" > frazy < / mstrans:dictionary >

**Przykład: en-de:**

Źródło danych wejściowych: Wyraz < tłumaczenia mstrans:dictionary =\"wordomatic\"> wyrazu lub frazy < / mstrans:dictionary > wpis słownika.

Dane wyjściowe docelowej: Das Wort "wordomatic" ist ein Wörterbucheintrag.

Ta funkcja działa tak samo z lub bez trybu HTML.

Funkcja powinny być używane rzadko. Odpowiednie i znacznie lepiej sposób dostosowywania tłumaczenia jest przy użyciu niestandardowych w usłudze Translator. Niestandardowe w usłudze Translator sprawia, że pełne wykorzystanie kontekstu i prawdopodobieństwa statystycznych. Jeśli masz lub można utworzyć dane szkoleniowe, które z miejsca pracy lub frazę w kontekście otrzymasz znacznie lepsze wyniki. Można znaleźć więcej informacji na temat niestandardowych w usłudze Translator w [ https://aka.ms/CustomTranslator ](https://aka.ms/CustomTranslator).
