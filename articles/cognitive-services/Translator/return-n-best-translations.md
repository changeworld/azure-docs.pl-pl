---
title: Zwróć N-najlepsze tłumaczenia — interfejs API tłumaczenia tekstu w usłudze Translator
titleSuffix: Azure Cognitive Services
description: Zwróć N-najlepsze tłumaczenia przy użyciu interfejs API tłumaczenia tekstu w usłudze Translator Microsoft.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: swmachan
ms.openlocfilehash: 7aec6bb81f3ca99ead744767668c2e4f1d1d4d53
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68594889"
---
# <a name="how-to-return-n-best-translations"></a>Jak zwrócić najlepsze tłumaczenia (N)

> [!NOTE]
> Ta metoda jest przestarzała. Nie jest dostępna w wersji 3.0 interfejs API tłumaczenia tekstu w usłudze Translator.

Metody gettranslations () i GetTranslationsArray () interfejsu API usługi Microsoft Translator zawierają opcjonalną flagę logiczną "IncludeMultipleMTAlternatives".
Metoda zwróci do maxTranslations alternatywy, w przypadku których Delta jest dostarczana z N-najlepszych list aparatu translatora.

Sygnatura:

**Składnia**

| C# |
|:---|
| GetTranslationsResponse Microsoft. translator. gettranslations (appId, tekst, od, do, maxTranslations, Options); |

**Parametry**

| Parametr | Opis |
|:---|:---|
| appId | **Wymagane** Jeśli jest używany nagłówek autoryzacji, pozostaw pole AppID puste, a następnie wpisz ciąg zawierający "Bearer" + "" + token dostępu.|
| text | **Wymagane** Ciąg reprezentujący tekst do przetłumaczenia. Rozmiar tekstu nie może przekraczać 10000 znaków.|
| from | **Wymagane** Ciąg reprezentujący kod języka tekstu do przetłumaczenia. |
| to | **Wymagane** Ciąg reprezentujący kod języka służący do tłumaczenia tekstu. |
| maxTranslations | **Wymagane** Int reprezentuje maksymalną liczbę tłumaczeń do zwrócenia. |
| options | **Opcjonalne** Obiekt TranslateOptions, który zawiera wartości wymienione poniżej. Są one opcjonalne i domyślne dla najczęściej używanych ustawień.

* Kategorii Jedyna obsługiwana i domyślna opcja to "ogólne".
* ContentType Jedyna obsługiwana i domyślna opcja to "text/zwykły".
* Stan: Stan użytkownika, aby pomóc skorelować żądanie i odpowiedź. Ta sama zawartość zostanie zwrócona w odpowiedzi.
* IncludeMultipleMTAlternatives: Oflaguj, aby określić, czy zwrócić więcej niż jeden alternatywę z aparatu MT. Wartość domyślna to false i zawiera tylko 1 alternatywę.

## <a name="ratings"></a>Klasyfikowani
Klasyfikacje są stosowane w następujący sposób: Najlepsze automatyczne tłumaczenie ma klasyfikację 5.
Alternatywy do tłumaczenia generowane automatycznie (N-Najlepsza) mają klasyfikację 0 i mają dopasowanie stopień 100.

## <a name="number-of-alternatives"></a>Liczba wariantów
Liczba zwróconych alternatyw jest równa maxTranslations, ale może być mniejsza.

## <a name="language-pairs"></a>Pary językowe
Ta funkcja jest niedostępna w przypadku tłumaczeń między uproszczonym i tradycyjnym chińskim, w obu kierunkach. Jest ona dostępna dla wszystkich innych obsługiwanych par językowych usługi Microsoft Translator.
