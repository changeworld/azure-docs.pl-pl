---
title: Słownik dynamiczny — interfejs API tłumaczenia tekstu w usłudze Translator
titleSuffix: Azure Cognitive Services
description: Jak korzystać z funkcji słownika dynamicznego interfejs API tłumaczenia tekstu w usłudze Translator.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: f586702350b38c6d546767f64a8e2f6115b59817
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595291"
---
# <a name="how-to-use-a-dynamic-dictionary"></a>Jak używać słownika dynamicznego

Jeśli znasz już tłumaczenie, które chcesz zastosować do wyrazu lub frazy, możesz podać je jako znacznik w żądaniu. Słownik dynamiczny jest bezpieczny tylko w przypadku rzeczowników złożonych, takich jak poprawne nazwy i nazwy produktów.

**Obowiązuje**

< msTrans: tłumaczenie słownika = "translation of phrase" > phrase </msTrans: dictionary >

**Wymagania**

* Języki `From` i`To` muszą być różne. 
* Zamiast korzystać z funkcji `From` automatycznego wykrywania, należy dołączyć parametr do żądania tłumaczenia interfejsu API. 

**Przykład: en-de:**

Dane wejściowe źródła: Słowo < msTrans: tłumaczenie słownika =\"wordomatic\"> Word lub phrase </msTrans: słownik > jest wpisem słownika.

Docelowy wynik: Das Wort "wordomatic" ist Ein Wörterbucheintrag.

Ta funkcja działa tak samo jak w przypadku trybu HTML i bez niego.

Ta funkcja powinna być stosowana oszczędnie. Odpowiednim i znacznie lepszym sposobem dostosowywania tłumaczenia jest użycie translatora niestandardowego. W przypadku translatora niestandardowego w pełni stosowane są prawdopodobieństwa kontekstowe i statystyczne. Jeśli masz lub możesz utworzyć dane szkoleniowe, które pokazują swoją służbę lub frazę w kontekście, uzyskasz dużo lepszych wyników. Więcej informacji na temat usługi tłumaczenia niestandardowego można znaleźć [https://aka.ms/CustomTranslator](https://aka.ms/CustomTranslator)pod adresem.
