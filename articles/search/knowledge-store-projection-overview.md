---
title: Praca z projekcjami w sklepie merytorycznym (wersja zapoznawcza)
titleSuffix: Azure Cognitive Search
description: Zapisz i umieść wzbogacone dane z potoku indeksowania wzbogacenia AI do magazynu wiedzy, który ma być używany w scenariuszach innych niż wyszukiwanie pełnotekstowe. Magazyn wiedzy jest obecnie w publicznej wersji zapoznawczej.
manager: nitinme
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: bb6af4be232810c1f5d135e459238e2e4f2cd5d8
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720038"
---
# <a name="working-with-projections-in-a-knowledge-store-in-azure-cognitive-search"></a>Praca z projekcjami w sklepie z bazami danych na platformie Azure Wyszukiwanie poznawcze

> [!IMPORTANT] 
> Magazyn wiedzy jest obecnie w publicznej wersji zapoznawczej. Funkcje wersji zapoznawczej są dostępne bez umowy dotyczącej poziomu usług i nie są zalecane w przypadku obciążeń produkcyjnych. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). [Interfejs API REST w wersji 2019-05-06 — wersja zapoznawcza](search-api-preview.md) zapewnia funkcje w wersji zapoznawczej. Dostępna jest obecnie ograniczona obsługa portalu i nie ma obsługi zestawu SDK platformy .NET.

Usługa Azure Wyszukiwanie poznawcze umożliwia wzbogacanie zawartości dzięki wbudowanym umiejętnościom poznawczym i umiejętnościom niestandardowym w ramach indeksowania. Wzbogacania dodają strukturę do dokumentów i ułatwiają wyszukiwanie. W wielu przypadkach wzbogacone dokumenty są przydatne w scenariuszach innych niż wyszukiwanie, na przykład w przypadku wyszukiwania w bazie wiedzy.

Projekcje, składnik [magazynu wiedzy](knowledge-store-concept-intro.md), są widokami wzbogaconych dokumentów, które można zapisać w magazynie fizycznym na potrzeby wyszukiwania w bazie wiedzy. Projekcja pozwala na "projekt" dane do kształtu, który jest zgodny z potrzebami, dzięki czemu można zachować relacje, tak aby narzędzia takie jak Power BI mogły odczytywać dane bez dodatkowych nakładów pracy.

Projekcje mogą być tabelaryczne, z danymi przechowywanymi w wierszach i kolumnach w usłudze Azure Table Storage lub w obiektach JSON przechowywanych w usłudze Azure Blob Storage. Można zdefiniować wiele projekcji danych w miarę ich wzbogacania. Wiele projekcji jest przydatne, gdy chcesz, aby te same dane różniły się inaczej w przypadku poszczególnych przypadków użycia.

Magazyn wiedzy obsługuje trzy typy projekcji:

+ **Tabele**: w przypadku danych, które są najlepiej reprezentowane jako wiersze i kolumny, projekcje tabeli umożliwiają definiowanie kształtu schematized lub projekcji w magazynie tabel.

+ **Obiekty**: gdy potrzebna jest reprezentacja w formacie JSON danych i wzbogacania, projekcje obiektów są zapisywane jako obiekty blob.

+ **Pliki**: Jeśli zachodzi potrzeba zapisania obrazów wyodrębnionych z dokumentów, projekcje plików umożliwia zapisanie znormalizowanych obrazów.

Aby wyświetlić projekcje zdefiniowane w kontekście, krok po kroku, [jak rozpocząć pracę z magazynem wiedzy](knowledge-store-howto.md).

## <a name="projection-groups"></a>Grupy projekcji

W niektórych przypadkach należy zaprojektować wzbogacone dane w różnych kształtach, aby zaspokoić różne cele. Magazyn wiedzy pozwala definiować wiele grup projekcji. Grupy projekcji mają następujące kluczowe cechy wzajemnej wyłączności i pokrewności.

### <a name="mutual-exclusivity"></a>Wzajemne wyłączności

Cała zawartość zadana w jednej grupie jest niezależna od danych rzutowanych na inne grupy projekcji.
Niezależność oznacza, że te same dane mogą wyglądać inaczej, ale powtórzone w każdej grupie projekcji.

### <a name="relatedness"></a>Pokrewieństwo

Grupy projekcji teraz umożliwiają projekcję dokumentów w typach projekcji przy zachowaniu relacji między typami projekcji. Cała zawartość zadana w ramach jednej grupy projekcji zachowuje relacje między danymi w typach projekcji. W tabelach relacje są oparte na wygenerowanym kluczu, a każdy węzeł podrzędny zachowuje odwołanie do węzła nadrzędnego. Relacje między typami (tabelami, obiektami i plikami) są zachowywane, gdy pojedynczy węzeł jest rzutowany na różne typy. Rozważmy na przykład scenariusz, w którym dokument zawiera obrazy i tekst. Można projektować tekst do tabel lub obiektów oraz obrazów do plików, w których tabele lub obiekty mają właściwość zawierającą adres URL pliku.

## <a name="input-shaping"></a>Kształtowanie danych wejściowych

Pobieranie danych w odpowiednim kształcie lub strukturze jest kluczem do efektywnego użycia, być tabelami lub obiektami. Możliwość kształtowania lub struktury danych w zależności od sposobu, w jaki planujesz uzyskać dostęp do niej, jest kluczową funkcją udostępnianą jako umiejętność **kształtu** w ramach zestawu umiejętności.  

Projekcje są łatwiejsze do zdefiniowania, gdy masz obiekt w drzewie wzbogacania, który jest zgodny ze schematem projekcji. Zaktualizowana [umiejętność kształtu](cognitive-search-skill-shaper.md) umożliwia redagowanie obiektów z różnych węzłów drzewa wzbogacania i ich nadrzędne używanie w nowym węźle. Umiejętność **kształtu** pozwala definiować typy złożone z zagnieżdżonymi obiektami.

Jeśli masz zdefiniowany nowy kształt, który zawiera wszystkie elementy potrzebne do wykreślania, możesz teraz użyć tego kształtu jako źródła dla projekcji lub jako dane wejściowe do innej umiejętności.

## <a name="projection-slicing"></a>Dzielenie wycinków

Podczas definiowania grupy projekcji pojedynczy węzeł drzewa wzbogacania można podzielić na wiele powiązanych tabel lub obiektów. Dodanie projekcji z ścieżką źródłową, która jest elementem podrzędnym istniejącej projekcji, spowoduje, że węzeł podrzędny zostanie podzielony na węzeł nadrzędny, a następnie nastąpi umieszczenie nowej, powiązanej tabeli lub obiektu. Ta technika pozwala zdefiniować pojedynczy węzeł w umiejętności kształtu, który może być źródłem dla wszystkich projekcji.

## <a name="table-projections"></a>Projekcje tabeli

Ponieważ ułatwia to importowanie, zalecamy projekcje tabeli na potrzeby eksploracji danych za pomocą Power BI. Ponadto projekcje tabeli umożliwiają zmianę kardynalności między relacjami między tabelami. 

Można projektować pojedynczy dokument w indeksie w wielu tabelach, zachowując relacje. Podczas projekcji w wielu tabelach, cały kształt zostanie rzutowany na każdą tabelę, chyba że węzeł podrzędny jest źródłem innej tabeli w tej samej grupie.

### <a name="defining-a-table-projection"></a>Definiowanie projekcji tabeli

Podczas definiowania projekcji tabeli w `knowledgeStore` elemencie zestawu umiejętności, Zacznij od mapowania węzła w drzewie wzbogacania do źródła tabeli. Zazwyczaj ten węzeł jest wynikiem umiejętności **kształtu** , który został dodany do listy umiejętności w celu utworzenia określonego kształtu, który jest potrzebny do projektu w tabelach. Wybrany dla projektu węzeł można podzielić na projekt na wiele tabel. Definicja tabel jest listą tabel, które mają być przeznaczone do projektu.

Każda tabela wymaga trzech właściwości:

+ TableName: Nazwa tabeli w usłudze Azure Storage.

+ generatedKeyName: Nazwa kolumny klucza, który jednoznacznie identyfikuje ten wiersz.

+ Źródło: węzeł z drzewa wzbogacania, z którego pochodzą Twoje wzbogacenia. Ten węzeł jest zwykle wynikiem kształtu, ale może być wyjściem z dowolnych umiejętności.

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

Jak pokazano w tym przykładzie, kluczowe frazy i jednostki są modelowane w różnych tabelach i zawierają odwołanie z powrotem do elementu nadrzędnego (Mainname) dla każdego wiersza.

Poniższa ilustracja przedstawia informacje na [temat sposobu rozpoczęcia pracy z usługą Knowledge Store](knowledge-store-howto.md). W scenariuszu, w którym sprawa ma wiele opinii, a każda opinia jest wzbogacana przez zidentyfikowanie jednostek zawartych w nim, można modelować projekcje, jak pokazano poniżej.

![Jednostki i relacje w tabelach](media/knowledge-store-projection-overview/TableRelationships.png "Relacje modelowania w projekcjach tabeli")

## <a name="object-projections"></a>Projekcje obiektów

Projekcje obiektów to reprezentacje JSON drzewa wzbogacania, które mogą być źródłem z dowolnego węzła. W wielu przypadkach ta sama umiejętność **kształtu** , która tworzy projekcję tabeli, może służyć do generowania projekcji obiektu. 

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
        },
        {
            "files": [ ]
        }
      ]
    }
}
```

Generowanie projekcji obiektu wymaga kilku atrybutów specyficznych dla obiektu:

+ storageContainer: kontener, w którym zostaną zapisane obiekty
+ Źródło: ścieżka do węzła drzewa wzbogacania, który jest elementem głównym projekcji
+ klucz: ścieżka reprezentująca unikatowy klucz dla obiektu, który ma być przechowywany. Zostanie ona użyta do utworzenia nazwy obiektu BLOB w kontenerze.

## <a name="file-projection"></a>Projekcja pliku

Projekcje plików są podobne do projekcji obiektów i działają tylko w kolekcji `normalized_images`. Podobnie jak w projekcjach obiektów, projekcje plików są zapisywane w kontenerze obiektów blob z prefiksem folderu w wartości zakodowanej Base64 identyfikatora dokumentu. Projekcje plików nie mogą współużytkować tego samego kontenera co projekcje obiektów i muszą być rzutowane do innego kontenera.

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

Projekcje mają cykl życia, który jest powiązany z danymi źródłowymi w źródle danych. Gdy dane są aktualizowane i ponownie indeksowane, projekcje są aktualizowane przy użyciu wyników wzbogacania, dzięki czemu projekcje są ostatecznie spójne z danymi w źródle danych. Projekcje dziedziczą zasady usuwania skonfigurowane dla Twojego indeksu. Projekcje nie są usuwane, gdy zostanie usunięty indeksator lub usługa wyszukiwania.

## <a name="using-projections"></a>Korzystanie z projekcji

Po uruchomieniu indeksatora można odczytać dane z projektu w kontenerach lub tabelach określonych za pomocą projekcji.

W przypadku analiz poszukiwanie w Power BI jest proste, jak w przypadku ustawienia usługi Azure Table Storage jako źródła danych. Można łatwo utworzyć zestaw wizualizacji na danych przy użyciu relacji w ramach programu.

Alternatywnie, jeśli konieczne jest użycie wzbogaconych danych w potoku analizy danych, można [załadować dane z obiektów BLOB do Pandas Dataframe](../machine-learning/team-data-science-process/explore-data-blob.md).

Na koniec Jeśli trzeba wyeksportować dane ze sklepu z bazami danych, Azure Data Factory ma łączniki do eksportowania danych i wystawić je w wybranej bazie danych. 

## <a name="next-steps"></a>Następne kroki

Następnym krokiem jest utworzenie pierwszego sklepu z wiedzą przy użyciu przykładowych danych i instrukcji.

> [!div class="nextstepaction"]
> [Tworzenie sklepu z bazami](knowledge-store-howto.md)danych.
