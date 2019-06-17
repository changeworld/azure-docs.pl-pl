---
title: Praca z projekcji w magazynie wiedzy (wersja zapoznawcza) — usługa Azure Search
description: Zapisz i kształtować wzbogaconego danych z potokiem indeksowania sztucznej Inteligencji do użytku w scenariuszach niż wyszukiwania
manager: eladz
author: vkurpad
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: vikurpad
ms.custom: seomay2019
ms.openlocfilehash: f1c7278909557dc92f86c5dfc1f190fddf33f607
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65540809"
---
# <a name="working-with-projections-in-a-knowledge-store-in-azure-search"></a>Praca z projekcji w magazynie wiedzę w usłudze Azure Search

> [!Note]
> Magazyn wiedzy jest w wersji zapoznawczej i nie przeznaczonych do użycia w środowisku produkcyjnym. [Wersji interfejsu API REST 2019-05-06-Preview](search-api-preview.md) zapewnia tę funkcję. Brak obsługi zestawu SDK platformy .NET w tej chwili.
>

Usługa Azure Search umożliwia zawartości wzbogacania umiejętności poznawcze sztucznej Inteligencji i umiejętności niestandardowe jako część indeksowania. Wzbogacenia dodawania struktury do dokumentów i wprowadzić bardziej skuteczne wyszukiwanie. W wielu przypadkach wzbogaconego dokumenty są przydatne w przypadku scenariuszy innych niż wyszukiwania, takie jak w przypadku wyszukiwania wiedzy.

Projekcje, składnik [magazynu wiedzy](knowledge-store-concept-intro.md), widoków wzbogaconego dokumentów, które można zapisywać do pamięci fizycznej do celów wyszukiwania wiedzy. Rzutowanie umożliwia "Projekt" dane do kształtu, który zgodnie z Twoimi potrzebami zachowaniu relacji, dzięki czemu narzędzi, takich jak usługa Power BI mogą odczytać dane za pomocą nie dodatkowego nakładu pracy. 

Projekcje może być tabelarycznych, z danych przechowywanych w wiersze i kolumny w usłudze Azure Table storage lub obiektami JSON przechowywane w usłudze Azure Blob storage. Można zdefiniować wiele projekcji danych, ponieważ są wzbogacane. Jest to przydatne, jeśli chcesz, aby te same dane ukształtowane w różny sposób dla poszczególnych zastosowań. 

Magazyn wiedzy obsługuje dwa typy projekcji:

+ **Tabele**: W przypadku danych, który najlepiej jest reprezentowany jako wiersze i kolumny tabeli projekcje umożliwiają definiowanie informatycznych kształtu lub projekcji w usłudze Table storage. 

+ **Obiekty**: Gdy będziesz potrzebować JSON reprezentacja danych i wzbogacenia, projekcji obiektu są zapisywane jako obiekty BLOB.

Aby wyświetlić projekcji zdefiniowanych w kontekście, krok po kroku [jak rozpocząć pracę z magazynem wiedzy](knowledge-store-howto.md)

## <a name="projection-groups"></a>Projekcja grup

W niektórych przypadkach należy wzbogaconego danych w różnych kształtów, do spełnienia celów innego projektu. Magazyn wiedzy umożliwia definiowanie wielu grup projekcji. Projekcja grupy mają następujące cechy klucza wzajemnego wyłączności i pokrewieństwo.

### <a name="mutually-exclusivity"></a>Wzajemnie się wyłączności

Cała zawartość odwzorowane w pojedynczej grupy jest niezależna od danych w innych grupach projekcji. Oznacza to, że może mieć te same dane ukształtowane w różny sposób, ale powtarzany w każdej grupie projekcji. 

Jedno ograniczenie wymuszane w grupach w projekcji jest wzajemnego wyłączności Projekcja typów z grupą projekcji. Można zdefiniować tylko projekcje tabeli lub projekcji obiektu z jednej grupy. Jeśli chcesz, tabel i obiektów, zdefiniuj jedna grupa projekcji dla tabel, a druga grupa projekcji dla obiektów.

### <a name="relatedness"></a>Pokrewieństwo

Cała zawartość przewidywany w obrębie grupy pojedynczej projekcji zachowuje relacji w danych. Relacje są oparte na wygenerowany klucz, a każdy węzeł podrzędny przechowuje odwołania do węzła nadrzędnego. Relacje nie obejmują projekcji grup i tabele lub obiekty utworzone w jednej grupie projekcji nie mają relacji z danymi wygenerowanymi w innych grupach projekcji.

## <a name="input-shaping"></a>Dane wejściowe kształtowania
Trwa pobieranie danych w właściwy kształt lub struktury jest użycie kluczy efektywna, tabele lub obiekty. Możliwość kształt i danych na jak zaplanować dostęp i korzystać z niego to kluczowy czynnik wpływający widoczne jako **Shaper** umiejętności w ramach zestawu umiejętności.  

Prognozy są łatwiejsze do definiowania, gdy obiekt drzewa wzbogacanie, który jest zgodny ze schematem projekcji. Zaktualizowany interfejs [umiejętności Shaper](cognitive-search-skill-shaper.md) pozwala na tworzenie obiektu z różnych węzłach drzewa wzbogacanie i nadrzędnych je w węźle nowego. **Shaper** umiejętności pozwala zdefiniować typy złożone o obiektach zagnieżdżonych.

W przypadku nowego kształtu definicja zawiera wszystkie elementy potrzebne do projektów na poziomie można teraz używać tego kształtu, jako źródło dla swoich projekcje lub jako dane wejściowe do innej umiejętności.

## <a name="table-projections"></a>Projekcje tabeli

Ponieważ sprawia, że importowanie łatwiej, firma Microsoft zaleca tabeli prognozę eksplorację danych za pomocą usługi Power BI. Ponadto projekcje tabeli umożliwić zmiana Kardynalność między relacji między tabelami. 

Projekt pojedynczego dokumentu w indeksie w wielu tabelach zachowaniu relacji. Podczas projekcji z wieloma tabelami, pełną kształt zostanie przedstawione każdej tabeli, chyba że węzeł podrzędny jest źródłem innej tabeli w ramach tej samej grupie.

### <a name="defining-a-table-projection"></a>Definiowanie projekcji tabeli

Podczas definiowania projekcji tabeli, w ramach `knowledgeStore` element z zestawu swoje umiejętności, początek mapowania węzeł w drzewie wzbogacania źródło tabeli. Zazwyczaj ten węzeł jest dane wyjściowe **Shaper** umiejętności, który został dodany do listy umiejętności do tworzenia określonych kształtu, który należy do projektu do tabel. Węzeł, istnieje możliwość projektu można określonego przedziału do projektu na wiele tabel. Definicja tabeli jest listę tabel, które mają być projektu. 

Każda tabela wymaga trzech właściwości:

+ Właściwość TableName: Nazwa tabeli w usłudze Azure Storage.

+ generatedKeyName: Nazwa kolumny klucza, który unikatowo identyfikuje ten wiersz.

+ Źródło: Węzeł w drzewie wzbogacania są źródłem usługi wzbogacenia z. Jest to zazwyczaj dane wyjściowe shaper, ale może być danych wyjściowych dowolnego umiejętności.

Oto przykład projekcji tabeli.

```json
{
    "name": "your-skillset",
    "skills": [
      …your skills
      
    ],
"cognitiveServices": {
… your cognitive services key info
    },

    "knowledgeStore": {
      "storageConnectionString": "an Azure storage connection string",
      "projections" : [
        {
          "tables": [
            { "tableName": "MainTable", "generatedKeyName": "SomeId", "source": "/document/EnrichedShape" },
            { "tableName": "KeyPhrases", "generatedKeyName": "KeyPhraseId", "source": "/document/EnrichedShape/*/KeyPhrases/*" },
            { "tableName": "Entities", "generatedKeyName": "EntityId", "source": "/document/EnrichedShape/*/Entities/*" }
          ]
        },
        {
          "objects": [
            
          ]
        }
      ]
    }
}
```
Jak pokazano w tym przykładzie, wyrażeń kluczowych i jednostek są modelowane w różnych tabelach i będzie zawierać odwołanie do obiektu nadrzędnego (MainTable) dla każdego wiersza. 

Na poniższej ilustracji jest odwołaniem do wykonywania orzecznictwa w [jak rozpocząć pracę z magazynem wiedzy](knowledge-store-howto.md). W scenariuszu, gdzie przypadek ma wiele opinii i każdego Opinia jest wspieranym przez identyfikację jednostek, które w nim zawarte rzuty można modelować, jak pokazano poniżej.

![Jednostek i relacji w tabelach](media/knowledge-store-projection-overview/TableRelationships.png "modelowania relacji w tabeli projekcji")

## <a name="object-projections"></a>Obiekt projekcji

Projekcje obiektu są reprezentacji JSON drzewa wzbogacanie, który może być źródło z dowolnego węzła. W wielu przypadkach takie same **Shaper** umiejętności, która tworzy projekcję tabela może służyć do generowania projekcji obiektu. 

```json
{
 
    "name": "your-skillset",
    "skills": [
      …your skills
      
    ],
"cognitiveServices": {
… your cognitive services key info
    },

    "knowledgeStore": {
      "storageConnectionString": "an Azure storage connection string",
      "projections" : [
        {
          "tables": [ ]
        },
        {
          "objects": [
            {
              "storageContainer": "Reviews", 
              "format": "json", 
              "source": "/document/Review", 
              "key": "/document/Review/Id" 
            }
          ]
        }
      ]
    }
}
```

Generowanie projekcji obiektu wymaga kilku atrybutów określonego obiektu:

+ storageContainer: Kontener, w którym zostanie zapisany obiektów
+ Źródło: Ścieżka do węzła drzewa wzbogacanie, który jest katalogiem głównym projekcji
+ key: Ścieżka, który reprezentuje unikatowy klucz dla obiektu, który ma być przechowywany. Będzie służyć do tworzenia nazwy obiektu blob w kontenerze.

## <a name="projection-lifecycle"></a>Cykl życia projekcji

Swoich projekcje mają cykl życia, który jest powiązany z danymi źródłowymi w źródle danych. Ponieważ dane są aktualizowane i zindeksowane ponownie, swoich projekcje są aktualizowane przy użyciu wyników wzbogacenia zapewnienie, że Twoje projekcje są ostatecznie spójny z danych w źródle danych. Rzuty dziedziczenie zasad usuwania, które zostały skonfigurowane dla indeksu. 

## <a name="using-projections"></a>Za pomocą projekcji

Po uruchomieniu indeksatora możesz odczytać prognozowanych danych w kontenerach lub tabel, które określono za pomocą projekcji. 

Do analizy Eksplorowanie w usłudze Power BI jest proste i polega na ustawienie usługi Azure Table storage jako źródło danych. Można bardzo łatwo utworzyć zestaw wizualizacji na wykorzystaniu relacji w danych.

Alternatywnie, jeśli musisz użyć wzbogaconego danych w potoku do nauki o danych można wykonać następujące akcje [załadowania danych z obiektów blob do elementów Pandas DataFrame](../machine-learning/team-data-science-process/explore-data-blob.md).

Na koniec należy wyeksportować dane z magazynu wiedzy, usługi Azure Data Factory ma łączników, aby wyeksportować dane i znajdą się go w wybranej bazy danych. 

## <a name="next-steps"></a>Kolejne kroki

Kolejnym krokiem tworzenia pierwszego sklep wiedzy za pomocą przykładowych danych i instrukcji.

> [!div class="nextstepaction"]
> [Jak utworzyć magazyn wiedzy](knowledge-store-howto.md).