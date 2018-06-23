---
title: Format danych w interfejsie API usługi eksploracji wiedzy | Dokumentacja firmy Microsoft
description: Więcej informacji na temat formatu danych w wiedzy eksploracji usługi (KES) interfejsu API w usługach kognitywnych.
services: cognitive-services
author: bojunehsu
manager: stesp
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: article
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: a763505ac6458d68df74ae73e71029b81202ec8b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346936"
---
# <a name="data-format"></a>Format danych
Plik danych w tym artykule opisano listy obiektów przeznaczonych do indeksu.
Każdy wiersz w pliku określa wartości atrybutów obiektu w [formatu JSON](http://json.org/) kodowania UTF-8.
Oprócz atrybuty zdefiniowane w [schematu](SchemaFormat.md), każdy obiekt ma atrybut opcjonalny "logprob", który określa względną dziennika prawdopodobieństwo między obiektami.
Gdy usługa zwraca obiekty w kolejności malejącej prawdopodobieństwa, możemy użyć "logprob" wskazującego zwracany zgodnych obiektów.
Podane prawdopodobieństwo *p* od 0 do 1, można obliczyć odpowiedniego prawdopodobieństwo dziennika logowania (*p*), gdzie log() jest funkcja logarytm naturalny.
Jeśli nie określono wartości dla logprob, zostanie użyta domyślna wartość 0.

```json
{"logprob":-5.3, "Title":"latent dirichlet allocation", "Year":2003, "Author":{"Name":"david m blei", "Affiliation":"uc berkeley"}, "Author":{"Name":"andrew y ng", "Affiliation":"stanford"}, "Author":{"Name":"michael i jordan", "Affiliation":"uc berkeley"}}
{"logprob":-6.1, "Title":"probabilistic latent semantic indexing", "Year":1999, "Author":{"Name":"thomas hofmann", "Affiliation":"uc berkeley"}}
...
```

W przypadku atrybutów String, GUID i obiektów Blob wartość jest reprezentowany jako ciągu w cudzysłowie JSON.  Dla atrybutów liczbowych (Int32, Int64, Double) wartość jest reprezentowany jako numer JSON.  Dla atrybutów złożonego wartość jest obiekt JSON, który określa wartości atrybutu podrzędnego.  Dla kompilacji indeksu szybsze presort obiektów, zmniejszając prawdopodobieństwo dziennika.

Ogólnie rzecz biorąc atrybut może być równa 0 lub więcej wartości.  Jeśli atrybut nie ma wartości, możemy wystarczy usunąć ją z JSON.  Jeśli atrybut wartości 2 lub większą, możemy Powtórz pary wartości atrybutów w obiekcie JSON.  Firma Microsoft można również przypisać tablicę JSON zawierający wiele wartości atrybutu.

```json
{"logprob":0, "Title":"0 keyword"}
{"logprob":0, "Title":"1 keyword", "Keyword":"foo"}
{"logprob":0, "Title":"2 keywords", "Keyword":"foo", "Keyword":"bar"}
{"logprob":0, "Title":"2 keywords (alt)", "Keyword":["foo", "bar"]}
```
