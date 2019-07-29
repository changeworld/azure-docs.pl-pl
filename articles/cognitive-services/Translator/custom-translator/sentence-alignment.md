---
title: Parowanie i wyrównywanie zdań — translator niestandardowy
titleSuffix: Azure Cognitive Services
description: W trakcie wykonywania szkolenia zdania obecne w dokumentach równoległych są sparowane lub wyrównane. Translator niestandardowy uzyskuje tłumaczenie jednego zdania jednocześnie, odczytując zdanie, tłumaczenie tego zdania. Następnie wyrównuje słowa i frazy w tych dwóch zdaniach.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: e9bc5c876da6bd2be1b22b389b819e51330b2e50
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595457"
---
# <a name="sentence-pairing-and-alignment-in-parallel-documents"></a>Parowanie i wyrównanie zdań w dokumentach równoległych

W trakcie szkolenia zdania obecne w dokumentach równoległych są sparowane lub wyrównane. Translator niestandardowy zgłasza liczbę zdań, które było w stanie sparować jako wyrównane zdania w każdym z zestawów danych.

## <a name="pairing-and-alignment-process"></a>Proces parowania i wyrównywania

Translator niestandardowy uzyskuje translację zdań z jednego zdania w danym momencie. Odczytuje zdanie z lokalizacji źródłowej, a następnie tłumaczenie tego zdania z celu. Następnie wyrównuje słowa i frazy w tych dwóch zdaniach. Ten proces umożliwia działowi IT utworzenie mapy wyrazów i fraz w jednym zdaniu do równoważnych słów i fraz w tłumaczeniu tego zdania. Wyrównania próbuje upewnić się, że system pociąga za sobą tłumaczenie.

## <a name="pre-aligned-documents"></a>Wstępnie wyrównane dokumenty

Jeśli wiesz, że masz dokumenty równoległe, możesz zastąpić wyrównanie zdania, dostarczając wstępnie wyrównane pliki tekstowe. Można wyodrębnić wszystkie zdania z dokumentów do pliku tekstowego, zorganizować jedno zdanie na wiersz i przekazać je z `.align` rozszerzeniem. `.align` Rozszerzenie sygnalizuje translatorowi niestandardowemu, że powinien pominąć wyrównanie zdania.

Aby uzyskać najlepsze wyniki, należy się upewnić, że w plikach znajduje się jedno zdanie na wiersz. Nie mają znaków nowego wiersza w obrębie zdania, ponieważ spowoduje to słabą wyrównania.

## <a name="suggested-minimum-number-of-extracted-and-aligned-sentences"></a>Sugerowana minimalna liczba wyodrębnionych i wyrównanych zdań

Aby szkolenie powiodło się, w poniższej tabeli przedstawiono minimalną liczbę wyodrębnionych zdań i rozmieszczonych zdań wymaganych w każdym zestawie danych. Sugerowana minimalna liczba wyodrębnionych zdań jest znacznie wyższa niż Sugerowana minimalna liczba rozciągniętych zdań do uwzględnienia faktu, że wyrównanie zdania może nie być w stanie wyrównać, że wszystkie wyodrębnione zdania zostały zakończone pomyślnie.

| Zestaw danych   | Sugerowana minimalna liczba wyodrębnionych zdań | Sugerowana minimalna wartość liczby zdań wyrównanych | Maksymalna liczba wyrównanych zdań |
|------------|--------------------------------------------|------------------------------------------|--------------------------------|
| Szkolenia   | 10 000                                     | 2000                                    | Brak górnego limitu                 |
| BIÓR     | 2000                                      | 500                                      | 2,500                          |
| Testowanie    | 2000                                      | 500                                      | 2,500                          |
| Słownik | 0                                          | 0                                        | Brak górnego limitu                 |

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak używać [słownika](what-is-dictionary.md) w translatoru niestandardowym.
