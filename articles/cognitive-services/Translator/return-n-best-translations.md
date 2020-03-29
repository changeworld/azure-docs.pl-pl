---
title: Powrót N-Najlepsze tłumaczenia - Tekst tłumacza
titleSuffix: Azure Cognitive Services
description: Zwracanie tłumaczeń N-Best przy użyciu interfejsu API tekstu tłumacza.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: swmachan
ROBOTS: NOINDEX
ms.openlocfilehash: eff25877165ac365e0af77651147fcdd1eebe294
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73837241"
---
# <a name="how-to-return-n-best-translations"></a>Jak zwrócić n-najlepsze tłumaczenia

> [!NOTE]
> Ta metoda jest przestarzała. Nie jest dostępna w wersji 3.0 interfejsu API tekstu translatora.

Metody GetTranslations() i GetTranslationsArray() interfejsu API usługi Microsoft Translator zawierają opcjonalną flagę logiczną "IncludeMultipleMTAlternatives".
Metoda zwróci do maxTranslations alternatywy, gdzie delta jest dostarczany z listy N-Best aparatu tłumacza.

Podpis jest:

**Składni**

| C# |
|:---|
| GetTranslationsResponse Microsoft.Translator.GetTranslations(appId, text, from, to, maxTranslations, options); |

**Parametry**

| Parametr | Opis |
|:---|:---|
| appId | **Wymagane** Jeśli używany jest nagłówek Autoryzacja, pozostaw pole appid puste, a w przeciwnym razie określ ciąg zawierający token "Nadźwig" + " + dostęp.|
| tekst | **Wymagane** Ciąg reprezentujący tekst do przetłumaczenia. Rozmiar tekstu nie może przekraczać 10000 znaków.|
| Z | **Wymagane** Ciąg reprezentujący kod języka tekstu do przetłumaczenia. |
| na | **Wymagane** Ciąg reprezentujący kod języka do przetłumaczenia tekstu na. |
| maxTranslations (maks. | **Wymagane** Int reprezentujący maksymalną liczbę tłumaczeń do zwrócenia. |
| opcje | **Opcjonalnie** Obiekt TranslateOptions, który zawiera wartości wymienione poniżej. Wszystkie są opcjonalne i domyślnie do najczęstszych ustawień.

* Kategoria: Jedyną obsługiwaną i domyślną opcją jest "ogólne".
* ContentType: Jedyną obsługiwaną i domyślną opcją jest "text/plain".
* Stan: Stan użytkownika, aby pomóc skorelować żądanie i odpowiedź. Ta sama zawartość zostanie zwrócona w odpowiedzi.
* IncludeMultipleMTAlternatives: flaga, aby ustalić, czy zwrócić więcej niż jedną alternatywę z aparatu MT. Wartość domyślna jest false i zawiera tylko 1 alternatywę.

## <a name="ratings"></a>Klasyfikacje
Oceny są stosowane w następujący sposób: Najlepsze tłumaczenie automatyczne ma ocenę 5.
Automatycznie generowane alternatywy tłumaczenia (N-Best) mają ocenę 0 i mają stopień dopasowania 100.

## <a name="number-of-alternatives"></a>Liczba alternatyw
Liczba zwróconych alternatyw jest do maxTranslations, ale może być mniejsza.

## <a name="language-pairs"></a>Pary językowe
Ta funkcja nie jest dostępna w przypadku tłumaczeń między chińskim uproszczonym i tradycyjnym, w obu kierunkach. Jest on dostępny dla wszystkich innych par obsługiwanych przez usługę Microsoft Translator.
