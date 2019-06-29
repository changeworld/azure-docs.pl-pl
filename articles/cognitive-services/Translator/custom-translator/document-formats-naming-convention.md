---
title: Formaty dokumentów i konwencje nazewnictwa — niestandardowe w usłudze Translator
titleSuffix: Azure Cognitive Services
description: Jest to przewodnik po formaty dokumentów i konwencję nazewnictwa niestandardowych w usłudze Translator. Takie podejście ułatwia zarządzanie nazwami dokumentów lepsze abd uniknąć konfliktu nazw.
author: swmachan
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 2f7a83be510e608bb3f630a2fb1860502d8e4475
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443423"
---
# <a name="document-formats-and-naming-convention-guidance"></a>Formaty i nazewnictwa wskazówki Konwencji dokumentów

Każdy plik używany do tłumaczenia niestandardowych musi wynosić co najmniej **cztery** znaków.

Poniższa tabela zawiera wszystkie obsługiwane formaty plików, które można użyć do tworzenia systemu tłumaczenia:

| Format            | Rozszerzenia   | Opis                                                                                                                                                                                                                                                                    |
|-------------------|--------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| XLIFF             | . XLF. XLIFF | Format dokumentu równoległe eksportu systemów Translation pamięci. Języki używane są definiowane w pliku.                                                                                                                                                              |
| TMX               | .TMX         | Format dokumentu równoległe eksportu systemów Translation pamięci. Języki używane są definiowane w pliku.                                                                                                                                                              |
| ZIP               | .ZIP         | ZIP to format pliku archiwum.                                                                                                                                                                                                        |
| Locstudio         | .LCL         | Format Microsoft równoległe dokumentów                                                                                                                                                                                                                                      |
| Microsoft Word    | .DOCX        | Dokument programu Microsoft Word                                                                                                                                                                                                                                                        |
| Adobe Acrobat     | .PDF         | Adobe Acrobat przenośny dokument                                                                                                                                                                                                                                                |
| HTML              | .HTML, .HTM  | Dokument HTML                                                                                                                                                                                                                                                                  |
| Plik tekstowy         | .TXT         | UTF-16 lub UTF-8 zakodowane pliki tekstowe. Nazwa pliku nie może zawierać znaki japońskie.                                                                                                                                                                                        |
| Tekst wyrównany w pliku | . DOPASUJ       | Rozszerzenie `.ALIGN` to specjalne rozszerzenie, które można użyć, jeśli wiesz, że zdań w parze dokumentu są dokładnie wyrównana. Jeśli podasz `.ALIGN` pliku, niestandardowe w usłudze Translator nie zostaną wyrównane zdania dla Ciebie. |
| Excel file        | .XLSX        | Plik programu Excel (2013 lub nowszy). Pierwszy wiersz / wiersz arkusz kalkulacyjny powinien być kod języka.                                                                                                                                                                                                                                                      |

## <a name="dictionary-formats"></a>Słownik formatów

Słowników niestandardowe w usłudze Translator obsługuje wszystkie formaty plików, które są obsługiwane w przypadku szkolenia zestawów. Jeśli używasz słownika Excel pierwszy wiersz / wiersz arkusz kalkulacyjny powinien być kodów języków.

## <a name="zip-file-formats"></a>Formaty plików zip

Dokumenty można grupować w pliku zip pojedynczego i przekazywane. Obsługuje niestandardowe w usłudze Translator zip formatów plików (pliku ZIP, GZ i TGZ).

Każdy dokument w pliku zip z rozszerzeniem TXT, HTML, HTM, PDF, DOCX, WYRÓWNANIE, należy wykonać tę konwencję nazewnictwa:

{Nazwa dokumentu} \_{kod języka} {nazwa dokumentu} w przypadku nazwy dokumentu, {kod języka} jest LanguageID ISO (dwóch znaków), wskazujący, że dokument zawiera zdań w tym języku. Musi to być znaku podkreślenia (_), zanim kod języka.

Na przykład aby przekazać dwa dokumenty równoległe w ramach zip dla języka angielskiego systemu hiszpańskim, pliki powinny mieć nazwę "data_en" i "data_es".

Pliki w pamięci tłumaczenia (TMX XLF, XLIFF, LCL, XLSX) nie muszą przestrzegać konwencji nazewnictwa określonego języka.  

## <a name="next-steps"></a>Kolejne kroki

- Przeczytaj o [projektu](workspace-and-project.md#what-is-a-custom-translator-project) do tworzenia i zarządzania nimi.
