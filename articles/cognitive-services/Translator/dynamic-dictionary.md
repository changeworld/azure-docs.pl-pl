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
ms.openlocfilehash: c268616c015c1c8735b2bdddc057f235d02cdc2a
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73161746"
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
