---
title: Parowanie i wyrównywanie zdań — tłumacz niestandardowy
titleSuffix: Azure Cognitive Services
description: Podczas wykonywania szkolenia zdania obecne w dokumentach równoległych są sparowane lub wyrównane. Custom Translator uczy się tłumaczeń po jednym zdaniu naraz, czytając zdanie, tłumaczenie tego zdania. Następnie wyrównuje słowa i wyrażenia w tych dwóch zdaniach do siebie.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: cf5b2b84142c9104ea5b3afa3ad179fd0ec07449
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80370135"
---
# <a name="sentence-pairing-and-alignment-in-parallel-documents"></a>Parowanie i wyrównywanie zdań w dokumentach równoległych

Podczas szkolenia zdania obecne w dokumentach równoległych są sparowane lub wyrównane. Translator niestandardowy raportuje liczbę zdań, które można było sparować jako wyrównane zdania w każdym z zestawów danych.

## <a name="pairing-and-alignment-process"></a>Proces parowania i wyrównywania

Custom Translator uczy się tłumaczeń zdań po jednym zdaniu naraz. Odczytuje zdanie ze źródła, a następnie tłumaczenie tego zdania z obiektu docelowego. Następnie wyrównuje słowa i wyrażenia w tych dwóch zdaniach do siebie. Proces ten umożliwia tworzenie mapy słów i fraz w jednym zdaniu do równoważnych słów i fraz w tłumaczeniu tego zdania. Wyrównanie stara się zapewnić, że system trenuje na zdaniach, które są tłumaczenia siebie nawzajem.

## <a name="pre-aligned-documents"></a>Wstępnie wyrównane dokumenty

Jeśli wiesz, że masz dokumenty równoległe, możesz zastąpić wyrównanie zdania, podając wstępnie wyrównane pliki tekstowe. Można wyodrębnić wszystkie zdania z obu dokumentów do pliku tekstowego, `.align` zorganizować jedno zdanie w wierszu i przekazać z rozszerzeniem. Rozszerzenie `.align` sygnalizuje Niestandardowy translator, że należy pominąć wyrównanie zdania.

Aby uzyskać najlepsze wyniki, upewnij się, że masz jedno zdanie w wierszu w plikach.Nie ma znaków nowego linii w zdaniu, ponieważ spowoduje to słabe wyrównanie.

## <a name="suggested-minimum-number-of-sentences"></a>Sugerowana minimalna liczba zdań

Aby szkolenie zakończyło się pomyślnie, poniższa tabela przedstawia minimalną liczbę zdań wymaganych w każdym typie dokumentu.To ograniczenie jest siatką bezpieczeństwa, aby upewnić się, że zdania równoległe zawierają wystarczającą ilość unikalnego słownictwa, aby skutecznie wyszkolić model tłumaczenia. Ogólne wytyczne są o więcej w domenie równoległych zdań jakości tłumaczenia człowieka powinny produkować modele wyższej jakości.

| Typ dokumentu   | Sugerowana minimalna liczba zdań | Maksymalna liczba zdań |
|------------|--------------------------------------------|--------------------------------|
| Szkolenia   | 10 000                                     | Brak górnej granicy                 |
| Dostrajania     | 500                                      | 2500       |
| Testowanie    | 500                                      | 2500  |
| Słownik | 0                                          | Brak górnej granicy                 |

> [!NOTE]
> - Szkolenie nie rozpocznie się i zakończy się niepowodzeniem, jeśli minimalna liczba zdań 10 000 dla szkolenia nie zostanie spełniona. 
> - Dostrajanie i testowanie są opcjonalne. Jeśli ich nie podasz, system usunie odpowiednią wartość procentową z szkolenia do użycia do sprawdzania poprawności i testowania. 
> - Można trenować model przy użyciu tylko danych słownika. Zapoznaj się [z What is Dictionary](https://docs.microsoft.com/azure/cognitive-services/translator/custom-translator/what-is-dictionary).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak używać [słownika](what-is-dictionary.md) w umykaczu niestandardowym.
