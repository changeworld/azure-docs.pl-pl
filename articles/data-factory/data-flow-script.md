---
title: Skrypt przepływu danych mapowania Azure Data Factory
description: Przegląd kodu skryptu przepływu danych Data Factory w języku związanym z kodem
author: kromerm
ms.author: nimoolen
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/10/2019
ms.openlocfilehash: 4ff5a05fd40ef086c1f2332443ca03d5e872e9a8
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74010159"
---
# <a name="data-flow-script-dfs"></a>Skrypt przepływu danych (DFS)

Skrypt przepływu danych (DFS) to metadane bazowe, podobne do języka kodowania, które są używane do wykonywania transformacji zawartych w przepływie danych mapowania. Każda transformacja jest reprezentowana przez serię właściwości, które zawierają informacje niezbędne do poprawnego uruchomienia zadania. Skrypt jest widoczny i można go edytować w ramach funkcji ADF, klikając przycisk "skrypt" na górnej Wstążce interfejsu użytkownika przeglądarki.

![Przycisk skryptu](media/data-flow/scriptbutton.png "Przycisk skryptu")

Na przykład, `allowSchemaDrift: true,` w transformacji źródłowej instruuje usługę, aby dołączył wszystkie kolumny ze źródłowego zestawu danych w przepływie danych, nawet jeśli nie znajdują się one w projekcji schematu.

## <a name="use-cases"></a>Przypadki zastosowań
System plików DFS jest automatycznie tworzony przez interfejs użytkownika. Kliknięcie przycisku skryptu umożliwia wyświetlenie i dostosowanie skryptu. Możesz również generować skrypty poza interfejsem użytkownika funkcji ADF, a następnie przekazać je do polecenia cmdlet programu PowerShell. Podczas debugowania złożonych przepływów danych może okazać się łatwiejsze skanowanie kodu skryptu, a nie skanowanie wykresu interfejsu użytkownika w celu przedstawienia przepływów.

Oto kilka przykładowych przypadków użycia:
- Programowo wytwarzający wiele przepływów danych, które są dość podobne, czyli przepływy danych "stemplowanie".
- Złożone wyrażenia, które trudno zarządzać w interfejsie użytkownika lub są wynikiem problemów ze sprawdzaniem poprawności.
- Debugowanie i lepsze zrozumienie różnych błędów zwróconych podczas wykonywania.

Podczas kompilowania skryptu przepływu danych, który ma być używany z programem PowerShell lub interfejsem API, należy zwinąć sformatowany tekst w jeden wiersz. Tabulatory i znaki nowego wiersza można przechowywać jako znak ucieczki. Ale tekst musi być sformatowany do dopasowania wewnątrz właściwości JSON. Istnieje przycisk w interfejsie użytkownika edytora skryptów u dołu, który sformatuje skrypt jako jeden wiersz.

![Przycisk kopiowania](media/data-flow/copybutton.png "Kopiuj")

## <a name="how-to-add-transforms"></a>Jak dodać przekształcenia
Dodawanie transformacji wymaga trzech podstawowych kroków: dodanie podstawowych danych transformacji, przekierowanie strumienia wejściowego, a następnie ponowne przekierowanie strumienia wyjściowego. Może to być widoczne najłatwiej na przykład.
Załóżmy, że zaczynamy od prostego źródła do przepływu danych ujścia, tak jak przedstawiono poniżej:

```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

Jeśli zdecydujesz się dodać transformację pochodną, najpierw musimy utworzyć podstawowy tekst transformacji, który ma proste wyrażenie umożliwiające dodanie nowej kolumny z wielką literą o nazwie `upperCaseTitle`:
```
derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
```

Następnie zajmiemy istniejący system plików DFS i dodamy transformację:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

A teraz ponownie kierujemy strumień przychodzący, identyfikując, która transformacja chce, aby nowa transformacja była dostępna po (w tym przypadku `source1`) i skopiować nazwę strumienia do nowej transformacji:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

Wreszcie zidentyfikujemy transformację, którą chcemy po tej nowej transformacji, i Zastąp strumień wejściowy (w tym przypadku `sink1`) nazwą strumienia wyjściowego dla nowej transformacji:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
deriveTransformationName sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

## <a name="dfs-fundamentals"></a>Podstawy systemu plików DFS
System plików DFS składa się z szeregu połączonych transformacji, w tym źródeł, ujść i różnych osób, które mogą dodawać nowe kolumny, filtrować dane, dołączać dane i wiele innych. Zwykle skrypt rozpoczynający się od co najmniej jednego źródła, po którym następuje wiele przekształceń i kończący się na jednym lub większej liczbie zlewów.

Wszystkie źródła mają tę samą konstrukcję podstawową:
```
source(
  source properties
) ~> source_name
```

Na przykład proste źródło z trzema kolumnami (movieId, title, gatunek) byłoby następujące:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
```

Wszystkie przekształcenia inne niż źródła mają tę samą konstrukcję podstawową:
```
name_of_incoming_stream transformation_type(
  properties
) ~> new_stream_name
```

Na przykład prostą transformację pochodną, która przyjmuje kolumnę (tytuł) i zastępuje ją wielką wersją, będzie następująca:
```
source1 derive(
  title = upper(title)
) ~> derive1
```

I ujścia bez schematu zostałby po prostu:
```
derive1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

## <a name="next-steps"></a>Następne kroki

Eksploruj przepływy danych, zaczynając od [artykułu Omówienie przepływów danych](concepts-data-flow-overview.md)
