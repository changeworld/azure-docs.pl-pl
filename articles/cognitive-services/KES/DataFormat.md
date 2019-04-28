---
title: Format danych — interfejs API usługi Eksploracja wiedzy
titlesuffix: Azure Cognitive Services
description: Więcej informacji na temat formatu danych w wiedzy Exploration Service (KES) interfejsu API.
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: bba257c61509d862bb3161625750f05a86af8770
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60815088"
---
# <a name="data-format"></a>Format danych

Plik danych w tym artykule opisano listy obiektów do indeksowania.
Każdy wiersz w pliku określa wartości atrybutów obiektu w [formatu JSON](https://json.org/) przy użyciu kodowania UTF-8.
Oprócz atrybutów zdefiniowanych w [schematu](SchemaFormat.md), każdy obiekt ma atrybut opcjonalny "logprob", który określa prawdopodobieństwo, że względne dziennika między obiektami.
Gdy usługa zwraca obiekty w kolejności malejących prawdopodobieństwa, możemy użyć "logprob" do wskazania kolejności zwracany zgodnych obiektów.
Biorąc pod uwagę prawdopodobieństwo *p* od 0 do 1 odpowiedniego prawdopodobieństwo, że dziennik może zostać obliczony jako dziennik (*p*), gdzie log() jest funkcja logarytmu naturalnego.
Jeśli nie określono wartości dla logprob, jest używana wartość domyślna 0.

```json
{"logprob":-5.3, "Title":"latent dirichlet allocation", "Year":2003, "Author":{"Name":"david m blei", "Affiliation":"uc berkeley"}, "Author":{"Name":"andrew y ng", "Affiliation":"stanford"}, "Author":{"Name":"michael i jordan", "Affiliation":"uc berkeley"}}
{"logprob":-6.1, "Title":"probabilistic latent semantic indexing", "Year":1999, "Author":{"Name":"thomas hofmann", "Affiliation":"uc berkeley"}}
...
```

W przypadku atrybutów String, GUID i obiektów Blob wartość jest przedstawiana jako ciąg JSON w cudzysłowie.  Dla atrybutów liczbowych (typu Int32, Int64, Double) wartość jest reprezentowana jako numer JSON.  W przypadku złożonych atrybutów wartość jest obiekt JSON, który określa wartości atrybutów podrzędnych.  Aby przyspieszyć kompilowanie indeksu presort obiektów, zmniejszając prawdopodobieństwo dziennika.

Ogólnie rzecz biorąc atrybut może mieć wartość 0 lub więcej wartości.  Jeśli atrybut nie ma wartości, możemy po prostu usunąć ją z kodu JSON.  Jeśli atrybut zawiera wartości 2 lub więcej, firma Microsoft Powtórz para wartości atrybutu w obiekcie JSON.  Alternatywnie można przypisywać tablicą JSON zawierającą wiele wartości atrybutu.

```json
{"logprob":0, "Title":"0 keyword"}
{"logprob":0, "Title":"1 keyword", "Keyword":"foo"}
{"logprob":0, "Title":"2 keywords", "Keyword":"foo", "Keyword":"bar"}
{"logprob":0, "Title":"2 keywords (alt)", "Keyword":["foo", "bar"]}
```
