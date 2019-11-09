---
title: Słownik dynamiczny — interfejs API tłumaczenia tekstu w usłudze Translator
titleSuffix: Azure Cognitive Services
description: W tym artykule wyjaśniono, jak używać funkcji słownika dynamicznego w interfejs API tłumaczenia tekstu w usłudze Translator Cognitive Services platformy Azure.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 5f007660b98a22ff9ffad072991a39cf59518b6a
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837353"
---
# <a name="how-to-use-a-dynamic-dictionary"></a>Jak używać słownika dynamicznego

Jeśli znasz już tłumaczenie, które chcesz zastosować do wyrazu lub frazy, możesz podać je jako znacznik w żądaniu. Słownik dynamiczny jest bezpieczny tylko w przypadku rzeczowników złożonych, takich jak poprawne nazwy i nazwy produktów.

**Obowiązuje**

< msTrans: tłumaczenie słownika = "translation of phrase" > phrase </msTrans: dictionary >

**Wymagania**

* Języki `From` i `To` muszą być różne. 
* Zamiast korzystać z funkcji automatycznego wykrywania, należy w żądaniu translacji interfejsu API uwzględnić parametr `From`. 

**Przykład: en-de:**

Dane wejściowe źródła: słowo < msTrans: tłumaczenie słownika =\"wordomatic\"> wyrazu lub frazy </msTrans: dictionary > to wpis słownika.

Docelowa produkcja wyjściowa: Das Wort "wordomatic" ist Ein Wörterbucheintrag.

Ta funkcja działa tak samo jak w przypadku trybu HTML i bez niego.

Ta funkcja powinna być stosowana oszczędnie. Odpowiednim i znacznie lepszym sposobem dostosowywania tłumaczenia jest użycie translatora niestandardowego. W przypadku translatora niestandardowego w pełni stosowane są prawdopodobieństwa kontekstowe i statystyczne. Jeśli masz lub możesz utworzyć dane szkoleniowe, które pokazują swoją służbę lub frazę w kontekście, uzyskasz dużo lepszych wyników. Więcej informacji na temat usługi tłumaczenia niestandardowego można znaleźć pod adresem [https://aka.ms/CustomTranslator](https://aka.ms/CustomTranslator).
