---
title: Formaty dokumentów i konwencje nazewnictwa — Translator niestandardowy
titleSuffix: Azure Cognitive Services
description: Jest to przewodnik dotyczący formatów dokumentów i konwencji nazewnictwa w translatorze niestandardowym. Ta koncepcja pomaga zarządzać nazwami dokumentów lepiej abd uniknąć konfliktów nazewnictwa.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 41b15cc998a7bacd033ef2fe083fc99f1bff0286
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "68595854"
---
# <a name="document-formats-and-naming-convention-guidance"></a>Formaty dokumentów i wskazówki dotyczące konwencji nazewnictwa

Każdy plik używany do tłumaczenia niestandardowego musi mieć co najmniej **cztery** znaki długości.

Ta tabela zawiera wszystkie obsługiwane formaty plików, których można użyć do zbudowania systemu tłumaczenia:

| Format            | Rozszerzenia   | Opis                                                                                                                                                                                                                                                                    |
|-------------------|--------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Xliff             | . Xlf. Xliff | Równoległy format dokumentu, eksport systemów pamięci tłumaczeniowej. Używane języki są definiowane wewnątrz pliku.                                                                                                                                                              |
| Tmx               | . Tmx         | Równoległy format dokumentu, eksport systemów pamięci tłumaczeniowej. Używane języki są definiowane wewnątrz pliku.                                                                                                                                                              |
| Zip               | . Zip         | ZIP to format pliku archiwum.                                                                                                                                                                                                        |
| Locstudio ( Locstudio )         | . Lcl         | Format firmy Microsoft dla dokumentów równoległych                                                                                                                                                                                                                                      |
| Microsoft Word    | . Docx        | Dokument programu Microsoft Word                                                                                                                                                                                                                                                        |
| Adobe Acrobat     | . Pdf         | Przenośny dokument programu Adobe Acrobat                                                                                                                                                                                                                                                |
| HTML              | . Html. Htm  | Dokument HTML                                                                                                                                                                                                                                                                  |
| Plik tekstowy         | . Txt         | Utf-16 lub UTF-8 zakodowane pliki tekstowe. Nazwa pliku nie może zawierać znaków japońskich.                                                                                                                                                                                        |
| Wyrównany plik tekstowy | . Wyrównaj       | Rozszerzenie `.ALIGN` jest specjalnym rozszerzeniem, którego można użyć, jeśli wiadomo, że zdania w parze dokumentów są idealnie wyrównane. Jeśli podasz `.ALIGN` plik, Tłumacz niestandardowy nie wyrówna zdania dla Ciebie. |
| Plik programu Excel        | . Xlsx        | Plik programu Excel (2013 lub nowsza). Pierwszy wiersz/ wiersz arkusza kalkulacyjnego powinien być kodem języka.                                                                                                                                                                                                                                                      |

## <a name="dictionary-formats"></a>Formaty słowników

W przypadku słowników usługa Custom Translator obsługuje wszystkie formaty plików, które są obsługiwane dla zestawów szkoleniowych. Jeśli używasz słownika programu Excel, pierwszym wierszem/ wierszem arkusza kalkulacyjnego powinny być kody językowe.

## <a name="zip-file-formats"></a>Formaty plików zip

Dokumenty można pogrupować w jeden plik zip i przesłać. Translator niestandardowy obsługuje formaty plików zip (ZIP, GZ i TGZ).

Każdy dokument w pliku zip z rozszerzeniem TXT, HTML, HTM, PDF, DOCX, ALIGN musi być zgodny z tą konwencją nazewnictwa:

{nazwa dokumentu} \_{language code} gdzie {nazwa dokumentu} jest nazwą dokumentu, {kod języka} to identyfikator języka ISO (dwa znaki), co oznacza, że dokument zawiera zdania w tym języku. Musi istnieć podkreślenie (_) przed kodem języka.

Na przykład, aby przesłać dwa równoległe dokumenty w zip dla systemu angielskiego do hiszpańskiego, pliki powinny mieć nazwy "data_en" i "data_es".

Pliki pamięci tłumaczeniowej (TMX, XLF, XLIFF, LCL, XLSX) nie są wymagane do przestrzegania określonej konwencji nazewnictwa języków.  

## <a name="next-steps"></a>Następne kroki

- Przeczytaj o [projekcie,](workspace-and-project.md#what-is-a-custom-translator-project) aby je utworzyć i zarządzać nim.
