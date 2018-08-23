---
title: Słownik dynamiczny interfejs API tekstu usługi Microsoft Translator | Dokumentacja firmy Microsoft
description: Jak używać funkcji dynamiczny słownik interfejs API tekstu usługi Microsoft Translator.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-text
ms.topic: article
ms.date: 12/14/2017
ms.author: v-jansko
ms.openlocfilehash: dbc754093827730b8709d67f314e5b327518ef50
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/11/2018
ms.locfileid: "41988622"
---
# <a name="how-to-use-the-dynamic-dictionary-feature-of-the-microsoft-translator-text-api"></a>Jak używać funkcji dynamiczny słownik interfejs API tekstu usługi Microsoft Translator

Jeśli znasz już tłumaczeń, które mają zostać zastosowane do wyrazu lub frazy, możesz podać go jako kod znaczników w żądaniu. Dynamiczny słownik jest tylko bezpieczne dla rzeczowniki złożone, np. prawidłowe nazwy i nazwy produktu. 

**Składnia:** 

< tłumaczenia mstrans:dictionary = "tłumaczenia frazę" > frazy < / mstrans:dictionary >

**Przykład: en-de:**

Źródło danych wejściowych: słowo < tłumaczenia mstrans:dictionary =\"wordomatic\"> wyrazu lub frazy < / mstrans:dictionary > wpis słownika.

Docelowe danych wyjściowych: brzeczki Das "wordomatic" ISTA NIP Wörterbucheintrag.

Ta funkcja działa tak samo z lub bez trybu HTML. 

Funkcja powinny być używane rzadko. Odpowiednie i znacznie lepiej sposób dostosowywania tłumaczenia jest przy użyciu niestandardowych w usłudze Translator. Niestandardowe w usłudze Translator sprawia, że pełne wykorzystanie kontekstu i prawdopodobieństwa statystycznych. Jeśli masz lub można utworzyć dane szkoleniowe, które z miejsca pracy lub frazę w kontekście otrzymasz znacznie lepsze wyniki. Można znaleźć więcej informacji na temat niestandardowych w usłudze Translator w [ http://aka.ms/CustomTranslator ](http://aka.ms/CustomTranslator).

