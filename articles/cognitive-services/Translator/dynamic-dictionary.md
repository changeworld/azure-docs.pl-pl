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
ms.openlocfilehash: a815434cb8797acf6b92a8fe4a4f1ff69508975d
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839216"
---
# <a name="how-to-use-a-dynamic-dictionary"></a>Jak używać dynamiczny słownik

Jeśli znasz już tłumaczeń, które mają zostać zastosowane do wyrazu lub frazy, możesz podać go jako kod znaczników w żądaniu. Dynamiczny słownik jest tylko bezpieczne dla rzeczowniki złożone, np. prawidłowe nazwy i nazwy produktu.

**Składnia:**

< tłumaczenia mstrans:dictionary = "tłumaczenia frazę" > frazy < / mstrans:dictionary >

**Wymagania:**

* `From` i `To` języków muszą się różnić. 
* Musi zawierać `From` parametru w żądaniu interfejsu API tłumaczenia zamiast przy użyciu funkcji auto-detect. 

**Przykład: en-de:**

Źródło danych wejściowych: Wyraz < tłumaczenia mstrans:dictionary =\"wordomatic\"> wyrazu lub frazy < / mstrans:dictionary > wpis słownika.

Dane wyjściowe docelowej: Das Wort "wordomatic" ist ein Wörterbucheintrag.

Ta funkcja działa tak samo z lub bez trybu HTML.

Funkcja powinny być używane rzadko. Odpowiednie i znacznie lepiej sposób dostosowywania tłumaczenia jest przy użyciu niestandardowych w usłudze Translator. Niestandardowe w usłudze Translator sprawia, że pełne wykorzystanie kontekstu i prawdopodobieństwa statystycznych. Jeśli masz lub można utworzyć dane szkoleniowe, które z miejsca pracy lub frazę w kontekście otrzymasz znacznie lepsze wyniki. Można znaleźć więcej informacji na temat niestandardowych w usłudze Translator w [ https://aka.ms/CustomTranslator ](https://aka.ms/CustomTranslator).
