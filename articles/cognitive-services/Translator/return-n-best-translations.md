---
title: Zwraca N-najlepiej tłumaczenia z tekstem Microsoft Translator interfejsu API | Dokumentacja firmy Microsoft
description: Zwróć tłumaczeń N najlepiej przy użyciu interfejsu API usługi Microsoft Translator tekstu.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-text
ms.topic: article
ms.date: 12/14/2017
ms.author: v-jansko
ms.openlocfilehash: 3eafe50f69ae1a6748342e64a414ecee4467d0d1
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347684"
---
# <a name="how-to-return-n-best-translations"></a>Sposób zwracania tłumaczeń najlepiej N

> [!NOTE]
> Ta metoda jest przestarzała. Nie jest dostępna w 3.0 Translator API tekstu.

Metody GetTranslations() i GetTranslationsArray() Translator interfejsu API firmy Microsoft to opcjonalna Flaga wartości logicznej "IncludeMultipleMTAlternatives".
Metoda zwróci maksymalnie alternatyw maxTranslations, gdzie różnicowej znajduje się na liście najlepszych N aparatu translatora.

Podpis jest:

**Składnia**

| C# |
|:---|
| GetTranslationsResponse Microsoft.Translator.GetTranslations(appId, text, from, to, maxTranslations, options); |

**Parametry**

| Parametr | Opis |
|:---|:---|
| appId | **Wymagane** Jeśli nagłówek uwierzytelnienia jest używany, pozostaw puste pole appid przeciwnym razie Określ ciąg zawierający "Bearer" + "" + tokenu dostępu.|
| tekst | **Wymagane** ciąg reprezentujący tekst do tłumaczenia. Rozmiar tekstu nie może przekroczyć 10 000 znaków.|
| z | **Wymagane** ciąg reprezentujący kod języka tekstu do tłumaczenia. |
| na | **Wymagane** ciąg reprezentujący kod języka umożliwia tłumaczenie tekstu do. |
| maxTranslations | **Wymagane** całkowitą reprezentującą maksymalną liczbę tłumaczeń do zwrócenia. |
| opcje | **Opcjonalne** A TranslateOptions obiekt, który zawiera wartości wymienionych poniżej. Są wszystkie opcjonalny i domyślnie najczęściej używane ustawienia.

* Jedynym obsługiwanym kategoria:, A wartość domyślna opcja jest "Ogólne".
* Jedynym obsługiwanym ContentType:, A wartość domyślna jest opcja "text/plain".
* Stan: Stan użytkownika ułatwia korelowanie żądania i odpowiedzi. Tę samą zawartość zostanie zwrócony w odpowiedzi.
* IncludeMultipleMTAlternatives: Oznacz flagą, aby określić, czy mają być zwracane więcej niż jeden opis rozwiązań alternatywnych, z aparatu MT. Domyślna ma wartość false i zawiera tylko 1 alternatywne.

## <a name="ratings"></a>Klasyfikacje
Klasyfikacje są stosowane w następujący sposób: najlepsze automatyczne tłumaczenie ma klasyfikację 5.
Automatycznie wygenerowany alternatyw tłumaczenia (N-najlepszy) mają klasyfikacji 0 i stopień dopasowania 100.

## <a name="number-of-alternatives"></a>Liczba rozwiązań alternatywnych
Liczba zwróconych alternatyw zależy od maxTranslations, ale może być mniejsza.

## <a name="language-pairs"></a>Kierunki
Ta funkcja nie jest dostępny do tłumaczenia między uproszczony i chiński tradycyjny, obu kierunkach. Jest ona dostępna dla wszystkich innych par języka Microsoft Translator obsługiwane.
