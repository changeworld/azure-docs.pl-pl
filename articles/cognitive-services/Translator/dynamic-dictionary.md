---
title: Słownik interfejsu API, dynamiczne tekst Microsoft Translator | Dokumentacja firmy Microsoft
description: Jak używać funkcji dynamiczny słownik interfejsu API Microsoft Translator tekstu.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-text
ms.topic: article
ms.date: 12/14/2017
ms.author: v-jansko
ms.openlocfilehash: a18348c9786669ac41c4e149577d97cd631d5531
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349717"
---
# <a name="how-to-use-the-dynamic-dictionary-feature-of-the-microsoft-translator-text-api"></a>Jak używać funkcji dynamiczny słownik interfejsu API Microsoft Translator tekstu

Jeśli znasz już tłumaczenia, które chcesz zastosować do wyraz lub frazę, możesz podać go jako kod znaczników w żądaniu. Dynamiczny słownik jest tylko bezpieczne dla rzeczowniki złożone, takich jak nazwy i nazwy produktu. 

**Składnia:** 

< tłumaczenia mstrans:dictionary = "tłumaczenia frazy" > wyrażenie < / mstrans:dictionary >

**Przykład: en-de:**

Źródło danych wejściowych: słowo < tłumaczenia mstrans:dictionary =\"wordomatic\"> wyraz lub frazę < / mstrans:dictionary > jest wpisu słownika.

Docelowe danych wyjściowych: brzeczki Das "wordomatic" ist NIP Wörterbucheintrag.

Ta funkcja działa tak samo z włączonymi i wyłączonymi trybie HTML. 

Funkcja powinny być używane rzadko. Jest odpowiedni i znacznie sposób dostosowywania tłumaczenia za pomocą Centrum firmy Microsoft translatora. Koncentrator sprawia, że pełne wykorzystanie kontekstu i statystyczne prawdopodobieństwa. Jeśli masz lub akceptowalny Utwórz dane szkoleniowe, pokazujący pracodawcę lub frazę w kontekście, możesz uzyskać znacznie lepszych wyników. Można znaleźć więcej informacji na temat Centrum w [ http://hub.microsofttranslator.com ](http://hub.microsofttranslator.com).

