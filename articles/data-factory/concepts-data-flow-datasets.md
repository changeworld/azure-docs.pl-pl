---
title: Mapowanie przepływu danych w zestawach danych w usłudze Azure Data Factory
description: Azure Data Factory mapowania przepływ danych ma zgodności określonego zestawu danych
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/14/2019
ms.openlocfilehash: efb82c57a5620ef3eace8b39f6f27f2286202f84
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2019
ms.locfileid: "58521843"
---
# <a name="mapping-data-flow-datasets"></a>Mapowanie przepływu danych w zestawach danych

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Zestawy danych są konstrukcję usługi Data Factory, który definiuje kształt danych, którą pracujesz w potoku. Przepływ danych wierszy i kolumn danych na poziomie wymagane precyzyjnie szczegółowej definicji zestawu danych. Zestawów danych używanych w potoków przepływów sterowania nie wymagają tę samą głębokość interpretacji danych.

Zestawy danych na przepływ danych są używane w przekształcenia źródła i ujścia. Są one używane do definiowania schematów danych podstawowych. Jeśli nie masz schematu w Twoich danych, można ustawiać dryfu schematu dla źródła i ujścia. Schemat zdefiniowany z zestawu danych trzeba będzie typy danych powiązane, formatów danych, lokalizacja pliku i informacji o połączeniu ze skojarzonej połączonej usługi. Metadane z zestawów danych pojawi się w swojej transformacji źródła jako źródło "Rzutowania". Schemat w zestawie danych reprezentuje typ danych fizycznych i kształt podczas projekcji w Przekształcenie źródła reprezentuje przepływ danych reprezentacja danych przy użyciu zdefiniowanych nazw i typów.

## <a name="dataset-types"></a>Typy zestawów danych

Obecnie przepływ danych zawiera cztery typy zestawów danych:

* Azure SQL DB
* Magazyn danych SQL Azure
* Parquet (z usługi ADLS i obiektów Blob)
* Tekst rozdzielany (z usługi ADLS i obiektów Blob)

Przepływ zestawów danych danych oddzielnych *typ źródła* z *połączonej usługi typu połączenia*. Zwykle znajduje się w fabryce danych możesz wybrać typ połączenia (z obiektu Blob, Azure Data Lake Store itp.), a następnie zdefiniować typu pliku w zestawie danych. Wewnątrz przepływu danych wybierze typy źródeł, które mogą być skojarzone z różnymi typami połączeń połączonej usługi.

![Opcje przekształcania źródła](media/data-flow/dataset1.png "źródeł")

## <a name="data-flow-compatible-datasets"></a>Zgodne zestawów danych przepływu danych

Tworząc nowy zestaw danych jest pole wyboru "Data Flow zgodny" w prawym górnym rogu panelu. Kliknięcie tego przycisku spowoduje odfiltrowanie tylko zestawy danych, które mogą być używane z przepływu danych. 

## <a name="import-schemas"></a>Importuj schematy

Podczas importowania schematu przepływ danych zestawów danych, zostanie wyświetlony przycisk Importuj schemat. Kliknięcie tego przycisku spowoduje wyświetlenie dwóch opcji: Importuj ze źródła lub Importuj z pliku lokalnego. W większości przypadków będzie zaimportować schemat bezpośrednio ze źródła. Jednak jeśli masz istniejący plik schematu (pliku Parquet lub CSV z nagłówkami), możesz wskazać czy lokalnego pliku i Data Factory definiowania schematu, w zależności od tego pliku schematu.

## <a name="create-new-table"></a>Tworzenie nowej tabeli

W przepływu danych możesz poprosić usługi ADF, aby utworzyć nową definicję tabeli w docelowej bazie danych przez ustawienie zestawu danych podczas przekształcania ujścia, który ma nową nazwę tabeli. W zestawie danych SQL kliknij przycisk "Edit" poniżej nazwę tabeli i wprowadź nową nazwę tabeli. Następnie podczas przekształcania ujścia, włącz "Zezwalaj na schemat odejściem od tego stanu". Seth ustawienie "Importuj schemat" na wartość None.

![Źródło przekształcenia schematu](media/data-flow/dataset2.png "schematu SQL")

## <a name="choose-your-type-of-data-first"></a>Najpierw wybierz typ danych

### <a name="delimited-text"></a>Tekst rozdzielany

W dataset tekst rozdzielany ustawi ogranicznik, który ma obsługiwać albo jednego ograniczniki ("\t"dla TSV,"," dla woluminów CSV, ' |'...) lub użyć wielu znaków w poszukiwaniu ogranicznika. Ustaw przełącznik wiersza nagłówka, a następnie przejdź do przekształcania źródła automatyczne wykrywanie typów danych. Jeśli używasz tekst rozdzielany zestaw danych, aby ładowanie danych w ujściu, wystarczy wybrać folder docelowy. W obszarze Ustawienia ujścia można zdefiniować nazwę plików wyjściowych.

### <a name="parquet"></a>Parquet

Użyj Parquet jako preferowany przemieszczania typ zestawu danych w usłudze ADF przepływów danych. Parquet zapisze obszerne metadane schematu razem z danymi.

### <a name="database-types"></a>Typy bazy danych

Możesz wybrać bazy danych SQL Azure lub usługi Azure SQL data Warehouse.

Dla innych ADF zestawu danych typów należy użyć działania kopiowania do przygotowania danych. Brak szablonu usługi ADF w galerii szablonów, aby ułatwić Ci budowanie tego wzorca.

![Kopiowanie tymczasowego](media/data-flow/templatedf.png "skopiuj przemieszczania")

## <a name="choose-your-connection-type"></a>Wybierz typ połączenia

Jeśli używasz Parquet lub tekst rozdzielany zestawów danych można następnie wybrać lokalizację danych: Azure Data Lake Store lub obiektu Blob.

## <a name="next-steps"></a>Kolejne kroki

Rozpocznij od [tworzenia nowego przepływu danych](data-flow-create.md) i Dodaj Przekształcenie źródła. Następnie można skonfigurować zestaw danych dla źródła.

Użyj [działania kopiowania](copy-activity-overview.md) na pobranie danych z dowolnej usługi ADF źródła danych i przygotowania w usłudze ADLS lub obiektu Blob dla dostępu przez przepływ danych.

