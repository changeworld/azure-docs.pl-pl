---
title: Praca z projekcjami w sklepie merytorycznym (wersja zapoznawcza) — Azure Search
description: Zapisz i Przekształć dane wzbogacone z potoku indeksowania AI do użycia w scenariuszach innych niż wyszukiwanie
manager: nitinme
author: vkurpad
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: vikurpad
ms.openlocfilehash: c5fb547b18bc4014f91341070f49c4af84c01005
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2019
ms.locfileid: "71265184"
---
# <a name="working-with-projections-in-a-knowledge-store-in-azure-search"></a>Praca z projekcjami w sklepie z bazami danych w Azure Search

> [!Note]
> Magazyn wiedzy jest w wersji zapoznawczej i nie jest przeznaczony do użycia w środowisku produkcyjnym. [Interfejs API REST w wersji 2019-05-06 — wersja zapoznawcza](search-api-preview.md) zawiera tę funkcję. W tej chwili nie ma obsługi zestawu SDK platformy .NET.
>

Azure Search umożliwia wzbogacanie zawartości dzięki umiejętnościom poznawczym AI i umiejętnościom niestandardowym w ramach indeksowania. Wzbogacania dodają strukturę do dokumentów i ułatwiają wyszukiwanie. W wielu przypadkach wzbogacone dokumenty są przydatne w scenariuszach innych niż wyszukiwanie, na przykład w przypadku wyszukiwania w bazie wiedzy.

Projekcje, składnik [magazynu wiedzy](knowledge-store-concept-intro.md), są widokami wzbogaconych dokumentów, które można zapisać w magazynie fizycznym na potrzeby wyszukiwania w bazie wiedzy. Projekcja pozwala na "projekt" dane do kształtu, który jest zgodny z potrzebami, dzięki czemu można zachować relacje, tak aby narzędzia takie jak Power BI mogły odczytywać dane bez dodatkowych nakładów pracy. 

Projekcje mogą być tabelaryczne, z danymi przechowywanymi w wierszach i kolumnach w usłudze Azure Table Storage lub w obiektach JSON przechowywanych w usłudze Azure Blob Storage. Można zdefiniować wiele projekcji danych w miarę ich wzbogacania. Jest to przydatne, gdy chcesz, aby te same dane miały różne znaczenie w przypadku poszczególnych przypadków użycia. 

Magazyn wiedzy obsługuje dwa typy projekcji:

+ **Tabele**: dla danych, które są najlepiej reprezentowane jako wiersze i kolumny, projekcje tabeli umożliwiają definiowanie kształtu schematized lub projekcji w magazynie tabel. 

+ **Obiekty**: gdy potrzebna jest reprezentacja w formacie JSON danych i wzbogacania, projekcje obiektów są zapisywane jako obiekty blob.

Aby wyświetlić projekcje zdefiniowane w kontekście, krok po kroku, [jak rozpocząć pracę z magazynem wiedzy](knowledge-store-howto.md).

## <a name="projection-groups"></a>Grupy projekcji

W niektórych przypadkach należy zaprojektować wzbogacone dane w różnych kształtach, aby zaspokoić różne cele. Magazyn wiedzy pozwala definiować wiele grup projekcji. Grupy projekcji mają następujące kluczowe cechy wzajemnej wyłączności i pokrewności.

### <a name="mutually-exclusivity"></a>Wzajemnie wykluczające się

Cała zawartość zadana w jednej grupie jest niezależna od danych rzutowanych na inne grupy projekcji. Oznacza to, że te same dane mogą wyglądać inaczej, ale powtórzone w każdej grupie projekcji. 

Jedno ograniczenie wymuszane w grupach projekcji jest wzajemnym wyłącznością typów projekcji z grupą projekcji. Można definiować tylko projekcje tabeli lub projekcje obiektów w jednej grupie. Jeśli chcesz, aby tabele i obiekty były zdefiniowane, zdefiniuj jedną grupę projekcji dla tabel i drugą grupę projekcji dla obiektów.

### <a name="relatedness"></a>Pokrewieństwo

Cała zawartość zadana w ramach jednej grupy projekcji zachowuje relacje w danych. Relacje opierają się na wygenerowanym kluczu, a każdy węzeł podrzędny zachowuje odwołanie do węzła nadrzędnego. Relacje nie obejmują grup projekcji, a tabele lub obiekty utworzone w jednej grupie projekcji nie mają relacji z danymi wygenerowanymi w innych grupach projekcji.

## <a name="input-shaping"></a>Kształtowanie danych wejściowych
Pobieranie danych w odpowiednim kształcie lub strukturze jest kluczem do efektywnego użycia, być tabelami lub obiektami. Możliwość kształtowania lub struktury danych w zależności od sposobu, w jaki planujesz uzyskać dostęp do niej, jest kluczową funkcją udostępnianą jako umiejętność **kształtu** w ramach zestawu umiejętności.  

Projekcje są łatwiejsze do zdefiniowania, gdy masz obiekt w drzewie wzbogacania, który jest zgodny ze schematem projekcji. Zaktualizowana [umiejętność kształtu](cognitive-search-skill-shaper.md) umożliwia redagowanie obiektów z różnych węzłów drzewa wzbogacania i ich nadrzędne używanie w nowym węźle. Umiejętność **kształtu** pozwala definiować typy złożone z zagnieżdżonymi obiektami.

Jeśli masz zdefiniowany nowy kształt, który zawiera wszystkie elementy potrzebne do wykreślania, możesz teraz użyć tego kształtu jako źródła dla projekcji lub jako dane wejściowe do innej umiejętności.

## <a name="table-projections"></a>Projekcje tabeli

Ponieważ ułatwia to importowanie, zalecamy projekcje tabeli na potrzeby eksploracji danych za pomocą Power BI. Ponadto projekcje tabeli umożliwiają zmianę kardynalności relacji między tabelami. 

Można projektować pojedynczy dokument w indeksie w wielu tabelach, zachowując relacje. Podczas projekcji w wielu tabelach, cały kształt zostanie rzutowany na każdą tabelę, chyba że węzeł podrzędny jest źródłem innej tabeli w tej samej grupie.

### <a name="defining-a-table-projection"></a>Definiowanie projekcji tabeli

Podczas definiowania projekcji tabeli w `knowledgeStore` elemencie zestawu umiejętności, Zacznij od mapowania węzła w drzewie wzbogacania do źródła tabeli. Zazwyczaj ten węzeł jest wynikiem umiejętności **kształtu** , który został dodany do listy umiejętności w celu utworzenia określonego kształtu, który jest potrzebny do projektu w tabelach. Wybrany dla projektu węzeł można podzielić na projekt na wiele tabel. Definicja tabel jest listą tabel, które mają być przeznaczone do projektu. 

#### <a name="projection-slicing"></a>Dzielenie wycinków
Podczas definiowania grupy projekcji tabeli pojedynczy węzeł drzewa wzbogacania można podzielić na wiele powiązanych tabel. Dodanie tabeli ze ścieżką źródłową, która jest elementem podrzędnym istniejącej tabeli projekcji, spowoduje, że węzeł podrzędny zostanie wycięty z węzła nadrzędnego i podzielony na nową, powiązaną tabelę. Pozwala to definiować pojedynczy węzeł w umiejętności kształtu, który może być źródłem dla wszystkich projekcji tabeli.

Każda tabela wymaga trzech właściwości:

+ TableName: Nazwa tabeli w usłudze Azure Storage.

+ generatedKeyName: Nazwa kolumny klucza, który jednoznacznie identyfikuje ten wiersz.

+ Źródło: węzeł z drzewa wzbogacania, z którego pochodzą Twoje wzbogacenia. Zwykle jest to dane wyjściowe kształtu, ale mogą to być dane wyjściowe dowolnych umiejętności.

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
Jak pokazano w tym przykładzie, kluczowe frazy i jednostki są modelowane w różnych tabelach i zawierają odwołanie z powrotem do elementu nadrzędnego (Mainname) dla każdego wiersza. 

Poniższa ilustracja przedstawia odwołanie do ćwiczenia Caselaw w temacie [jak rozpocząć pracę z magazynem wiedzy](knowledge-store-howto.md). W scenariuszu, w którym sprawa ma wiele opinii, a każda opinia jest wzbogacana przez zidentyfikowanie jednostek zawartych w nim, można modelować projekcje, jak pokazano poniżej.

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
        }
      ]
    }
}
```

Generowanie projekcji obiektu wymaga kilku atrybutów specyficznych dla obiektu:

+ storageContainer: kontener, w którym zostaną zapisane obiekty
+ Źródło: ścieżka do węzła drzewa wzbogacania, który jest elementem głównym projekcji
+ klucz: ścieżka reprezentująca unikatowy klucz dla obiektu, który ma być przechowywany. Zostanie ona użyta do utworzenia nazwy obiektu BLOB w kontenerze.

## <a name="projection-lifecycle"></a>Cykl życia projekcji

Projekcje mają cykl życia, który jest powiązany z danymi źródłowymi w źródle danych. W miarę aktualizowania i ponownego indeksowania danych projekcje są aktualizowane przy użyciu wyników wzbogacania, dzięki czemu projekcje są ostatecznie spójne z danymi w źródle danych. Projekcje dziedziczą zasady usuwania skonfigurowane dla Twojego indeksu. 

## <a name="using-projections"></a>Korzystanie z projekcji

Po uruchomieniu indeksatora można odczytać dane z projektu w kontenerach lub tabelach określonych za pomocą projekcji. 

W przypadku analiz poszukiwanie w Power BI jest proste, jak w przypadku ustawienia usługi Azure Table Storage jako źródła danych. Można bardzo łatwo utworzyć zestaw wizualizacji danych, wykorzystując relacje w ramach programu.

Alternatywnie, jeśli konieczne jest użycie wzbogaconych danych w potoku analizy danych, można [załadować dane z obiektów BLOB do Pandas Dataframe](../machine-learning/team-data-science-process/explore-data-blob.md).

Na koniec Jeśli trzeba wyeksportować dane ze sklepu z bazami danych, Azure Data Factory ma łączniki do eksportowania danych i wystawić je w wybranej bazie danych. 

## <a name="next-steps"></a>Następne kroki

Następnym krokiem jest utworzenie pierwszego sklepu z wiedzą przy użyciu przykładowych danych i instrukcji.

> [!div class="nextstepaction"]
> [Tworzenie sklepu z bazami](knowledge-store-howto.md)danych.
