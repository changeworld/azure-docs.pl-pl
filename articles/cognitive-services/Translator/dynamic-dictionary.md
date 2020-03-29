---
title: Słownik dynamiczny — interfejs API tekstu tłumacza
titleSuffix: Azure Cognitive Services
description: W tym artykule wyjaśniono, jak używać funkcji słownika dynamicznego interfejsu API tekstu usługi Azure Cognitive Services Translator.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 944bca8644da6127e73af04eb75d01697cd34399
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75446708"
---
# <a name="how-to-use-a-dynamic-dictionary"></a>Jak korzystać ze słownika dynamicznego

Jeśli znasz już tłumaczenie, które chcesz zastosować do wyrazu lub frazy, możesz podać je jako znaczniki w żądaniu. Słownik dynamiczny jest bezpieczny tylko dla rzeczowników złożonych, takich jak nazwy własne i nazwy produktów.

**Składni:**

<mstrans:dictionary translation="translation of phrase">phrase</mstrans:dictionary>

**Wymagania:**

* Języki `From` `To` te muszą zawierać język angielski i inny obsługiwany język. 
* Parametr należy `From` dołączyć do żądania tłumaczenia interfejsu API zamiast funkcji automatycznego wykrywania. 

**Przykład: en-de:**

Wejście źródłowe:`The word <mstrans:dictionary translation=\"wordomatic\">word or phrase</mstrans:dictionary> is a dictionary entry.`

Dane wyjściowe docelowe:`Das Wort "wordomatic" ist ein Wörterbucheintrag.`

Ta funkcja działa w ten sam sposób z trybem HTML i bez niego.

Oszczędnie używaj funkcji. Lepszym sposobem dostosowania tłumaczenia jest użycie translatora niestandardowego. Custom Translator w pełni wykorzystuje kontekst i prawdopodobieństwa statystyczne. Jeśli masz lub możesz utworzyć dane szkoleniowe, które pokazują twoją pracę lub frazę w kontekście, otrzymujesz znacznie lepsze wyniki. Więcej informacji na temat tłumacza niestandardowego można znaleźć na 100 [https://aka.ms/CustomTranslator](https://aka.ms/CustomTranslator)000 000 000 000
