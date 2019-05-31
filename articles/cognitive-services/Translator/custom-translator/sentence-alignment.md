---
title: Jak w zdaniu dotyczących parowania i wyrównania — niestandardowe w usłudze Translator
titleSuffix: Azure Cognitive Services
description: Podczas wykonywania szkolenia zdania obecne w dokumentach równoległe są sparowane lub wyrównane. Niestandardowe w usłudze Translator uzyskuje informacje o jednym zdaniu tłumaczenia w czasie, czytając zdania, tłumaczenie to pole następujące zdanie. Następnie wyrównuje słowa i zdania w te dwa zdania ze sobą.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: v-pawal
ms.topic: conceptual
ms.openlocfilehash: 99538fd18067377efcc9e744e9ec611e0f685273
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66389596"
---
# <a name="sentence-pairing-and-alignment-in-parallel-documents"></a>Jak w zdaniu dotyczących parowania i wyrównania w dokumentach równoległe

Podczas szkolenia, zdania obecne w dokumentach równoległe są sparowane lub wyrównane. Niestandardowe w usłudze Translator zgłasza liczbę zdań, które było możliwe pair jako zdania wyrównane we wszystkich zestawów danych.

## <a name="pairing-and-alignment-process"></a>Proces dotyczących parowania i wyrównania

Niestandardowe w usłudze Translator uczy się tłumaczenia zdań w jednym zdaniu w danym momencie. Jego odczytów w zdaniu ze źródła i tłumaczenie to pole następujące zdanie z docelowego. Następnie wyrównuje słowa i zdania w te dwa zdania ze sobą. Ten proces umożliwia tworzenie mapy słów i fraz w jednym zdaniu na równoważne słowa i frazy tłumaczeniami to pole następujące zdanie. Wyrównanie próbuje upewnij się, że system szkolenie modeli na zdań, które są tłumaczenia od siebie.

## <a name="pre-aligned-documents"></a>Wstępnie wyrównany dokumentów

Jeśli wiesz, że masz równoległe dokumentów, mogą zastąpić wyrównanie zdania, podając plików wstępnie wyrównane tekstowych. Można wyodrębnić wszystkie zdania z oba dokumenty do pliku tekstowego, zorganizowanym jednym zdaniu na wiersz i przekazywania za pomocą `.align` rozszerzenia. `.align` Rozszerzenia sygnalizuje niestandardowe w usłudze Translator, należy go pominąć wyrównanie zdania.

Aby uzyskać najlepsze wyniki spróbuj upewnij się, że jedno zdanie w każdym wierszu w plikach. Nie ma znaków nowego wiersza w obrębie zdania, jako spowoduje niską wyrównania.

## <a name="suggested-minimum-number-of-extracted-and-aligned-sentences"></a>Sugerowane minimalna liczba wyodrębnionych i wyrównane zdań

Szkolenia została wykonana pomyślnie, w poniższej tabeli przedstawiono minimalna liczba wyodrębnionych zdania i wyrównane zdania wymagane w każdym zestawie danych. Sugerowane minimalna liczba wyodrębnionych zdania jest znacznie wyższa niż sugerowane minimalną liczbę wyrównany zdania wziąć pod uwagę fakt, że wyrównanie zdania może nie być mógł pomyślnie Dopasuj wszystkie wyodrębnione zdania.

| Zestaw danych   | Zalecane minimalne zdania wyodrębnione liczba | Zalecane minimalne zdania wyrównany liczba | Liczba maksymalna wyrównany zdania |
|------------|--------------------------------------------|------------------------------------------|--------------------------------|
| Szkolenia   | 10 000                                     | 2000                                    | Żadnego górnego limitu                 |
| Dostrajanie     | 2000                                      | 500                                      | 2,500                          |
| Testowanie    | 2000                                      | 500                                      | 2,500                          |
| Słownik | 0                                          | 0                                        | Żadnego górnego limitu                 |

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak używać [słownika](what-is-dictionary.md) w niestandardowych w usłudze Translator.
