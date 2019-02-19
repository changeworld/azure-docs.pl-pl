---
title: Mapowanie przepływu danych w zestawach danych w usłudze Azure Data Factory
description: Azure Data Factory mapowania przepływ danych ma sepecific ze zgodnością z zestawu danych
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/14/2019
ms.openlocfilehash: 36ca5e07adf79de77ac4ab4149ff8e96a1dece8d
ms.sourcegitcommit: 4bf542eeb2dcdf60dcdccb331e0a336a39ce7ab3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/19/2019
ms.locfileid: "56408752"
---
# <a name="mapping-data-flow-datasets"></a>Mapowanie przepływu danych w zestawach danych

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Zestawy danych są konstrukcję usługi Data Factory, który definiuje kształt danych, którą pracujesz w potoku. Przepływ danych wierszy i kolumn danych na poziomie wymagane precyzyjnie szczegółowej definicji zestawu danych. Zestawów danych używanych w potoków przepływów sterowania nie wymagają tę samą głębokość interpretacji danych.

Zestawy danych w źródle przepływu danych i przekształcenia ujścia są używane do definiowania schematu danych podstawowych. Jeśli nie masz schematu w Twoich danych, można ustawiać dryfu schematu dla źródła i ujścia. Schemat zdefiniowany z zestawu danych trzeba będzie typy danych powiązane, formatów danych, lokalizacja pliku i informacji o połączeniu ze skojarzonej połączonej usługi.

## <a name="dataset-types"></a>Typy zestawów danych

Obecnie przepływ danych zawiera cztery typy zestawów danych:

* Azure SQL DB
* Magazyn danych SQL Azure
* Parquet
* Tekst rozdzielany

Przepływ danych w zestawach danych oddzielić źródła *typu* z połączonej usługi typu połączenia. Zwykle znajduje się w fabryce danych możesz wybrać typ połączenia (z obiektu Blob, Azure Data Lake Store itp.), a następnie zdefiniować typu pliku w zestawie danych. Wewnątrz przepływu danych wybierze typy źródeł, które mogą być skojarzone z różnymi typami połączeń połączonej usługi.

![Opcje przekształcania źródła](media/data-flow/dataset1.png "źródeł")

## <a name="data-flow-compatible-datasets"></a>Zgodne zestawów danych przepływu danych

Tworząc nowy zestaw danych jest pole wyboru "Data Flow zgodny" w prawym górnym rogu panelu. Kliknięcie tego przycisku spowoduje odfiltrowanie tylko zestawy danych, które mogą być używane z przepływu danych. 

## <a name="import-schemas"></a>Importuj schematy

Podczas importowania schematu przepływ danych zestawów danych, zostanie wyświetlony przycisk Importuj schemat. Kliknięcie tego przycisku spowoduje wyświetlenie dwóch opcji: Importuj ze źródła lub Importuj z pliku lokalnego. W większości przypadków będzie zaimportować schemat bezpośrednio ze źródła. Jednak jeśli masz istniejący plik schematu (pliku Parquet lub CSV z nagłówkami), możesz wskazać czy lokalnego pliku i Data Factory definiowania schematu, w zależności od tego pliku schematu.

