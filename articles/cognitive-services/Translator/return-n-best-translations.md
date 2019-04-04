---
title: Zwraca N najlepszych tłumaczeń — interfejs API tekstu usługi Translator
titlesuffix: Azure Cognitive Services
description: Zwraca N najlepszych tłumaczeń za pomocą interfejsu API tekstu usługi Translator firmy Microsoft.
services: cognitive-services
author: v-pawal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: v-jansko
ms.openlocfilehash: 62992122dc16003078bb80ecd87c2bd3692586dd
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/04/2019
ms.locfileid: "58918318"
---
# <a name="how-to-return-n-best-translations"></a>Sposób zwracania N najlepszych tłumaczenia

> [!NOTE]
> Ta metoda jest przestarzała. Nie jest dostępna w wersji 3.0 z interfejsu API tłumaczenia tekstu.

GetTranslations() i GetTranslationsArray() metody interfejsu API usługi Microsoft Translator to opcjonalna Flaga wartości logicznej "IncludeMultipleMTAlternatives".
Metoda zwróci maksymalnie alternatywy maxTranslations, gdzie delta jest dostarczany z listy N najlepszych aparatu w usłudze translator.

Podpis jest:

**Składnia**

| C# |
|:---|
| GetTranslationsResponse Microsoft.Translator.GetTranslations(appId, text, from, to, maxTranslations, options); |

**Parametry**

| Parametr | Opis |
|:---|:---|
| appId | **Wymagane** Jeśli nagłówek autoryzacji jest używany, pozostaw to pole puste appid przeciwnym razie Określ ciąg zawierający "Bearer" + "" + tokenu dostępu.|
| tekst | **Wymagane** ciąg reprezentujący tekstu do przetłumaczenia. Rozmiar tekstu nie może przekraczać 10 000 znaków.|
| z | **Wymagane** ciąg reprezentujący kod języka tekstu do przetłumaczenia. |
| na | **Wymagane** ciąg reprezentujący kod języka umożliwia tłumaczenie tekstu w. |
| maxTranslations | **Wymagane** wartość int reprezentująca maksymalną liczbę tłumaczeń do zwrócenia. |
| opcje | **Opcjonalnie** obiekt TranslateOptions, który zawiera wartości wymienione poniżej. Są wszystkie opcjonalny i domyślnie najczęściej używane ustawienia.

* Kategoria: Obsługiwana jest tylko, a wartość domyślna opcja to "general".
* Typ zawartości: Obsługiwana jest tylko, a wartość domyślna opcja to "text/plain".
* Stan: Stan użytkownika, który ułatwia korelowanie żądań i odpowiedzi. Tę samą zawartość, zostaną zwrócone w odpowiedzi.
* IncludeMultipleMTAlternatives: Oznacz flagą, aby określić, czy należy zwrócić więcej niż jedno alternatywne z aparatu MT. Domyślny ma wartość FAŁSZ i zawiera tylko 1 alternatywą.

## <a name="ratings"></a>Klasyfikacje
Klasyfikacje są stosowane w następujący sposób: Najlepsze automatycznego tłumaczenia ma klasyfikację od 5.
Automatycznie generowane alternatywy tłumaczenia (N najlepszych) mają ma klasyfikację od 0 i stopnia dopasowania 100.

## <a name="number-of-alternatives"></a>Liczba alternatywy
Liczba zwróconych alternatywy zależy od maxTranslations, ale może być mniejsza.

## <a name="language-pairs"></a>Kierunki
Ta funkcja nie jest dostępna w poszukiwaniu tłumaczeń między uproszczony i chiński tradycyjny, chiński obu kierunkach. Jest ona dostępna dla wszystkich innych par języka Microsoft Translator obsługiwane.
