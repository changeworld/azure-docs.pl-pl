---
title: Mapowanie przepływu danych w zestawach danych w usłudze Azure Data Factory
description: Azure Data Factory mapowania przepływ danych ma sepecific ze zgodnością z zestawu danych
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/14/2019
ms.openlocfilehash: ccf4273489d739bb9b0d802b79944efefcd02ff4
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/16/2019
ms.locfileid: "56331225"
---
# <a name="mapping-data-flow-datasets"></a>Mapowanie przepływu danych w zestawach danych

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Zestawy danych są konstrukcję usługi Data Factory, który definiuje kształt danych, którą pracujesz w potoku. W przepływie danych wierszy i kolumn danych na poziomie wymaga znacznie bardziej precyzyjnie szczegółowej definicji, niż jest to wymagane przez zestawy danych w przepływie sterowania potoku.

Użyj zestawów danych w źródle przepływu danych i przekształcenia ujścia, aby zdefiniować schemat danych podstawowych. Jeśli nie masz schematu w Twoich danych, można ustawiać dryfu schematu dla źródła i ujścia. Schemat zdefiniowany z zestawu danych trzeba będzie typy danych powiązane, formatów danych, lokalizacja pliku i informacji o połączeniu ze skojarzonej połączonej usługi.

Obecnie przepływ danych zawiera cztery zestawy danych:

* Azure SQL DB
* Magazyn danych SQL Azure
* Parquet
* Tekst rozdzielany

Przepływ danych w zestawach danych oddzielić źródła *typu* z połączonej usługi typu połączenia. Zwykle znajduje się w fabryce danych możesz wybrać typ połączenia (z obiektu Blob, Azure Data Lake Store itp.), a następnie zdefiniować typu pliku w zestawie danych. Wewnątrz przepływu danych wybierze typy źródeł, które mogą być skojarzone z różnymi typami połączeń połączonej usługi.

![Opcje przekształcania źródła](media/data-flow/dataset1.png "źródeł")

Tworząc nowy zestaw danych jest pole wyboru "Data Flow zgodny" w prawym górnym rogu panelu. Kliknięcie tego przycisku spowoduje odfiltrowanie tylko zestawy danych, które mogą być używane z przepływu danych. 

Importuj schematy

Podczas importowania schematu przepływ danych zestawów danych, zostanie wyświetlony przycisk Importuj schemat. Kliknięcie tego przycisku spowoduje wyświetlenie dwóch opcji: Importuj ze źródła lub Importuj z pliku lokalnego. W większości przypadków będzie zaimportować schemat bezpośrednio ze źródła. Jeśli jednak masz istniejący plik schematu, możesz wskazać ten plik lokalny i Data Factory określi schematu, w zależności od tego pliku schematu.

