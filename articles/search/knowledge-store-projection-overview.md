---
title: Projekcje w magazynie wiedzy (wersja zapoznawcza)
titleSuffix: Azure Cognitive Search
description: Zapisz i ukształtuj wzbogacone dane z potoku indeksowania wzbogacania AI do magazynu wiedzy do użytku w scenariuszach innych niż wyszukiwanie pełnotekstowe. Magazyn wiedzy jest obecnie w publicznej wersji zapoznawczej.
manager: nitinme
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: d264768bf27967d1a778400ae4e9e6f2e054d746
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78942967"
---
# <a name="projections-in-a-knowledge-store-in-azure-cognitive-search"></a>Projekcje w magazynie wiedzy w usłudze Azure Cognitive Search

> [!IMPORTANT] 
> Magazyn wiedzy jest obecnie w publicznej wersji zapoznawczej. Funkcja w wersji zapoznawczej jest dostarczana bez umowy dotyczącej poziomu usług i nie jest zalecana dla obciążeń produkcyjnych. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Interfejs [API REST w wersji 2019-05-06-Preview](search-api-preview.md) udostępnia funkcje w wersji zapoznawczej. Obecnie istnieje ograniczona obsługa portalu i nie ma obsługi sdk .NET.

Usługa Azure Cognitive Search umożliwia wzbogacanie zawartości za pomocą wbudowanych umiejętności poznawczych i umiejętności niestandardowych w ramach indeksowania. Wzbogacenia tworzą nowe informacje, w których wcześniej nie istniały: wyodrębnianie informacji z obrazów, wykrywanie tonacji, fraz kluczowych i encji z tekstu, aby wymienić tylko kilka. Wzbogacenia również dodać strukturę do niezróżiącego tekstu. Wszystkie te procesy powodują, że dokumenty, które sprawiają, że wyszukiwanie pełnotekstowe jest bardziej skuteczne. W wielu przypadkach wzbogacone dokumenty są przydatne w scenariuszach innych niż wyszukiwanie, takich jak wyszukiwanie wiedzy.

Projekcje, element [magazynu wiedzy,](knowledge-store-concept-intro.md)są widokami wzbogaconych dokumentów, które można zapisać w fizycznym magazynie do celów eksploracji wiedzy. Projekcja umożliwia "projekcję" danych w kształt, który jest zgodny z potrzebami, zachowując relacje, dzięki czemu narzędzia takie jak Power BI mogą odczytywać dane bez dodatkowego wysiłku.

Projekcje mogą być tabelaryczne, z danymi przechowywanymi w wierszach i kolumnach w magazynie tabel platformy Azure lub obiektach JSON przechowywanych w magazynie obiektów blob platformy Azure. Można zdefiniować wiele projekcji danych, ponieważ są wzbogacane. Wiele projekcji są przydatne, gdy mają być te same dane ukształtowane inaczej dla indywidualnych przypadków użycia.

Magazyn wiedzy obsługuje trzy rodzaje projekcji:

+ **Tabele:** W przypadku danych, które najlepiej są reprezentowane jako wiersze i kolumny, rzuty tabeli umożliwiają zdefiniowanie schematyzowanego kształtu lub projekcji w magazynie tabel. Tylko prawidłowe obiekty JSON mogą być rzutowane jako tabele, wzbogacony dokument może zawierać węzły, które nie są nazwane obiektami JSON, a podczas rzutowania tych obiektów należy utworzyć prawidłowy obiekt JSON z umiejętnością shapera lub kształtowaniem w linii.

+ **Obiekty:** Jeśli potrzebna jest reprezentacja JSON danych i wzbogacenia, rzuty obiektów są zapisywane jako obiekty blob. Tylko prawidłowe obiekty JSON mogą być rzutowane jako obiekty, wzbogacony dokument może zawierać węzły, które nie są nazwane JSON obiektów i podczas rzutowania tych obiektów, utworzyć prawidłowy obiekt JSON z umiejętności shaper lub kształtowania w linii.

+ **Pliki**: Gdy trzeba zapisać obrazy wyodrębnione z dokumentów, projekcje plików umożliwiają zapisanie znormalizowanych obrazów w magazynie obiektów blob.

Aby wyświetlić projekcje zdefiniowane w kontekście, po kroku [utwórz magazyn wiedzy w rest](knowledge-store-create-rest.md).

## <a name="projection-groups"></a>Grupy projekcyjne

W niektórych przypadkach należy rzutować wzbogacone dane w różnych kształtach, aby osiągnąć różne cele. Magazyn wiedzy umożliwia zdefiniowanie wielu grup projekcji. Grupy projekcyjne mają następujące kluczowe cechy wzajemnej wyłączności i pokłosie.

### <a name="mutual-exclusivity"></a>Wzajemna wyłączność

Cała zawartość wyświetlana w jednej grupie jest niezależna od danych wyświetlanych na inne grupy projekcyjne.
Ta niezależność oznacza, że można mieć te same dane ukształtowane inaczej, ale powtórzone w każdej grupie projekcji.

### <a name="relatedness"></a>Pokrytość

Grupy rzutowania umożliwiają teraz wyświetlanie dokumentów między typami projekcji przy zachowaniu relacji między typami projekcji. Cała zawartość wyświetlana w ramach jednej grupy projekcji zachowuje relacje w obrębie danych między typami projekcji. W tabelach relacje są oparte na wygenerowanym kluczu, a każdy węzeł podrzędny zachowuje odwołanie do węzła nadrzędnego. W różnych typach (tabelach, obiektach i plikach) relacje są zachowywane, gdy pojedynczy węzeł jest rzutowany na różne typy. Rozważmy na przykład scenariusz, w którym masz dokument zawierający obrazy i tekst. Tekst można wyświetlać do tabel lub obiektów, a obrazy do plików, w których tabele lub obiekty mają kolumnę/właściwość zawierającą adres URL pliku.

## <a name="input-shaping"></a>Kształtowanie danych wejściowych

Uzyskanie danych we właściwym kształcie lub strukturze jest kluczem do skutecznego użycia, czy to tabele lub obiekty. Możliwość kształtowania lub struktury danych w oparciu o sposób, w jaki masz zamiar uzyskać do nich dostęp i używać jest kluczową funkcją ujawnioną jako umiejętność **Shaper** w komecie umiejętności.  

Projekcje są łatwiejsze do zdefiniowania, gdy masz obiekt w drzewie wzbogacania, który pasuje do schematu projekcji. Zaktualizowana [umiejętność Shaper](cognitive-search-skill-shaper.md) umożliwia skomponowanie obiektu z różnych węzłów drzewa wzbogacania i nadrzędne je w nowym węźle. **Umiejętność Shaper** umożliwia definiowanie złożonych typów za pomocą obiektów zagnieżdżonych.

Po zdefiniowaniu nowego kształtu, który zawiera wszystkie elementy potrzebne do rzutowania, można teraz użyć tego kształtu jako źródła projekcji lub jako danych wejściowych do innej umiejętności.

## <a name="projection-slicing"></a>Krojenie projekcji

Podczas definiowania grupy projekcji pojedynczy węzeł w drzewie wzbogacania można podzielić na wiele powiązanych tabel lub obiektów. Dodanie projekcji ze ścieżką źródłową, która jest elementem podrzędnym istniejącej projekcji spowoduje wycięcie węzła podrzędnego z węzła nadrzędnego i rzutowanie do nowej, ale powiązanej tabeli lub obiektu. Ta technika umożliwia zdefiniowanie pojedynczego węzła w umiejętności shaper, która może być źródłem dla wszystkich projekcji.

## <a name="table-projections"></a>Projekcje tabeli

Ponieważ ułatwia importowanie, zalecamy projekcje tabel do eksploracji danych za pomocą usługi Power BI. Ponadto rzuty tabeli umożliwiają zmianę kardynalności między relacjami tabeli. 

Można rzutować pojedynczy dokument w indeksie w wielu tabelach, zachowując relacje. Podczas rzutowania do wielu tabel, pełny kształt będzie rzutowany do każdej tabeli, chyba że węzeł podrzędny jest źródłem innej tabeli w tej samej grupie.

### <a name="defining-a-table-projection"></a>Definiowanie projekcji tabeli

Podczas definiowania projekcji `knowledgeStore` tabeli w elemencie zestaw umiejętności, należy rozpocząć od mapowania węzła w drzewie wzbogacania do źródła tabeli. Zazwyczaj ten węzeł jest dane wyjściowe **Shaper** umiejętności, które zostały dodane do listy umiejętności do tworzenia określonego kształtu, który należy rzutować do tabel. Węzeł, który wybierzesz do projektu można podzielić na projekt do wielu tabel. Definicja tabel to lista tabel, które chcesz wyświetlić.

Każda tabela wymaga trzech właściwości:

+ nazwa tabeli: Nazwa tabeli w usłudze Azure Storage.

+ generatedKeyName: Nazwa kolumny klucza, który jednoznacznie identyfikuje ten wiersz.

+ źródło: Węzeł z drzewa wzbogacania, z którego zaopatrujesz się w wzbogacenia. Ten węzeł jest zwykle dane wyjściowe shaper, ale może być wyjście dowolnego z umiejętności.

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
          "objects": [ ]
        },
        {
            "files": [ ]
        }
      ]
    }
}
```

Jak pokazano w tym przykładzie, kluczowe frazy i jednostki są modelowane na różne tabele i będą zawierać odwołanie z powrotem do nadrzędnego (MainTable) dla każdego wiersza.

## <a name="object-projections"></a>Rzuty obiektów

Rzuty obiektów są reprezentacje JSON drzewa wzbogacania, które mogą być pozyskiwane z dowolnego węzła. W wielu przypadkach do generowania projekcji obiektu może służyć ta sama umiejętność **Shaper,** która tworzy rzutowanie tabeli. 

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
              "storageContainer": "hotelreviews", 
              "source": "/document/hotel"
            }
          ]
        },
        {
            "files": [ ]
        }
      ]
    }
}
```

Generowanie projekcji obiektu wymaga kilku atrybutów specyficznych dla obiektu:

+ storageContainer: kontener obiektów blob, w którym obiekty zostaną zapisane
+ źródło: Ścieżka do węzła drzewa wzbogacania, który jest katalogiem głównym projekcji

## <a name="file-projection"></a>Projekcja pliku

Rzuty plików są podobne do projekcji `normalized_images` obiektów i działają tylko na kolekcji. Podobnie jak w rzutach obiektów, rzuty plików są zapisywane w kontenerze obiektów blob z prefiksem folderu wartości zakodowanej base64 identyfikatora dokumentu. Rzuty plików nie mogą współużytkować tego samego kontenera co rzuty obiektów i muszą być rzutowane w innym kontenerze.

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
          "objects": [ ]
        },
        {
            "files": [
                 {
                  "storageContainer": "ReviewImages",
                  "source": "/document/normalized_images/*"
                }
            ]
        }
      ]
    }
}
```

## <a name="projection-lifecycle"></a>Cykl życia projekcji

Projekcje mają cykl życia powiązany z danymi źródłowymi w źródle danych. Ponieważ dane są aktualizowane i ponownie indeksowane, prognozy są aktualizowane o wyniki wzbogacenia, dzięki czemu prognozy są ostatecznie zgodne z danymi w źródle danych. Projekcje dziedziczą zasady usuwania skonfigurowane dla indeksu. Projekcje nie są usuwane po usunięciu indeksatora lub samej usługi wyszukiwania.

## <a name="using-projections"></a>Korzystanie z projekcji

Po uruchomieniu indeksatora można odczytać przewidywane dane w kontenerach lub tabelach określonych za pomocą prognoz.

W przypadku analiz eksploracja w usłudze Power BI jest tak prosta, jak ustawienie magazynu tabel platformy Azure jako źródła danych. Można łatwo utworzyć zestaw wizualizacji danych przy użyciu relacji wewnątrz.

Alternatywnie, jeśli chcesz użyć wzbogaconych danych w potoku nauki o danych, można [załadować dane z obiektów blob do Pandas DataFrame](../machine-learning/team-data-science-process/explore-data-blob.md).

Na koniec, jeśli trzeba wyeksportować dane z magazynu wiedzy, usługa Azure Data Factory ma łączniki do eksportowania danych i wyładowywać je w wybranej bazie danych. 

## <a name="next-steps"></a>Następne kroki

Następnym krokiem należy utworzyć pierwszy magazyn wiedzy, korzystając z przykładowych danych i instrukcji.

> [!div class="nextstepaction"]
> [Tworzenie magazynu wiedzy w rest](knowledge-store-create-rest.md).

W przypadku samouczka obejmującego zaawansowane koncepcje projekcji, takie jak krojenie, kształtowanie w linii i relacje, zacznij od [zdefiniowania projekcji w magazynie wiedzy](knowledge-store-projections-examples.md)

> [!div class="nextstepaction"]
> [Definiowanie projekcji w magazynie wiedzy](knowledge-store-projections-examples.md)
