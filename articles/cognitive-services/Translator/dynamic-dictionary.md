---
title: Dynamiczny słownik — interfejs API tekstu usługi Translator
titlesuffix: Azure Cognitive Services
description: Jak używać funkcji dynamiczny słownik interfejsu API tłumaczenia tekstu.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 2ef1e474dd5d36f1967501ea7bdedc4736954a2b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67436024"
---
# <a name="how-to-use-a-dynamic-dictionary"></a>Jak używać dynamiczny słownik

Jeśli znasz już tłumaczeń, które mają zostać zastosowane do wyrazu lub frazy, możesz podać go jako kod znaczników w żądaniu. Dynamiczny słownik jest tylko bezpieczne dla rzeczowniki złożone, np. prawidłowe nazwy i nazwy produktu.

**Składnia:**

< tłumaczenia mstrans:dictionary = "tłumaczenia frazę" > frazy < / mstrans:dictionary >

**Przykład: en-de:**

Źródło danych wejściowych: Wyraz < tłumaczenia mstrans:dictionary =\"wordomatic\"> wyrazu lub frazy < / mstrans:dictionary > wpis słownika.

Dane wyjściowe docelowej: Das Wort "wordomatic" ist ein Wörterbucheintrag.

Ta funkcja działa tak samo z lub bez trybu HTML.

Funkcja powinny być używane rzadko. Odpowiednie i znacznie lepiej sposób dostosowywania tłumaczenia jest przy użyciu niestandardowych w usłudze Translator. Niestandardowe w usłudze Translator sprawia, że pełne wykorzystanie kontekstu i prawdopodobieństwa statystycznych. Jeśli masz lub można utworzyć dane szkoleniowe, które z miejsca pracy lub frazę w kontekście otrzymasz znacznie lepsze wyniki. Można znaleźć więcej informacji na temat niestandardowych w usłudze Translator w [ https://aka.ms/CustomTranslator ](https://aka.ms/CustomTranslator).
