---
title: Użyj danych odwołanie wyszukiwania w usłudze Azure Stream Analytics
description: W tym artykule opisano, jak znaleźć lub skorelować danych w projekcie zapytania zadanie usługi analiza strumienia Azure za pomocą danych referencyjnych.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/25/2018
ms.openlocfilehash: 25c25a58b4c6eab5419f645e8e916e034e7803dd
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37016894"
---
# <a name="using-reference-data-for-lookups-in-stream-analytics"></a>Przy użyciu danych odwołanie wyszukiwania w analiza strumienia
Dane referencyjne (znanej także jako tabela odnośnika) jest ograniczone zestawu danych, który jest statyczny lub powoli zmianę charakteru, używany do wyszukiwania lub do skorelowania ze strumienia danych. Usługa Azure Stream Analytics ładuje dane referencyjne w pamięci, aby osiągnąć przetwarzania strumienia małe opóźnienia. Aby użyć danych odwołanie do zadania usługi analiza strumienia Azure, zwykle użyje [dołączenia danych odwołania](https://msdn.microsoft.com/library/azure/dn949258.aspx) w zapytaniu. Stream Analytics korzysta z magazynu obiektów Blob platformy Azure jako warstwy magazynu danych referencyjnych i z odwołaniem do fabryki danych Azure danych można przekształcone lub kopiowane do magazynu obiektów Blob platformy Azure, do użycia jako dane odwołanie z [dowolną liczbę oparte na chmurze i magazyny danych lokalnymi](../data-factory/copy-activity-overview.md). Dane referencyjne ma formę sekwencji obiektów blob (zdefiniowany w konfiguracji wejściowych) w rosnącej kolejności Data/Godzina podana w nazwie obiektu blob. On **tylko** obsługuje dodawanie na końcu sekwencji za pomocą daty/godziny **większa** niż określona przez ostatnich obiektów blob w sekwencji.

Analiza strumienia obsługuje dane referencyjne z **maksymalny rozmiar 300 MB**. 300 MB limit maksymalny rozmiar danych referencyjnych jest osiągalny tylko z prostego zapytania. Jak złożonością kwerendy wzrasta do uwzględnienia stanowe przetwarzania agregacjami sprzężenia danych czasowych i danych czasowych funkcje analityczne, np. oczekuje się, że maksymalny obsługiwany rozmiar spadku danych odwołania. Jeśli usługi Azure Stream Analytics nie można załadować danych referencyjnych i wykonywać operacje złożonych, zadanie będzie za mało pamięci i zakończyć się niepowodzeniem. W takich przypadkach SU % wykorzystania Metryka osiągną 100%.    

|**Liczbę jednostek przesyłania strumieniowego**  |**Obsługiwane szacunkowa maksymalny rozmiar (w MB)**  |
|---------|---------|
|1   |50   |
|3   |150   |
|6 i nowszych   |300   |

Zwiększenie liczby jednostek przesyłania strumieniowego zadania poza 6 nie zwiększyć maksymalny rozmiar obsługiwany danych referencyjnych.

Obsługa kompresji nie jest dostępna dla danych referencyjnych. 

## <a name="configuring-reference-data"></a>Konfigurowanie danych referencyjnych
Aby skonfigurować dane odwołanie, należy najpierw utworzyć danych wejściowych typu **danych referencyjnych**. W poniższej tabeli opisano każdej właściwości, które należy podać podczas tworzenia danych referencyjnych dane wejściowe z jego opisem:

|**Nazwa właściwości**  |**Opis**  |
|---------|---------|
|Alias wejściowy   | Przyjazna nazwa, który będzie używany w zapytaniu zadania do odwołania tych danych wejściowych.   |
|Konto magazynu   | Nazwa konta magazynu, w którym znajdują się obiektów blob. Jeśli znajduje się w tej samej subskrypcji co Twoje zadania usługi analiza strumienia, zostanie ona wybrana z listy rozwijanej.   |
|Klucz konta magazynu   | Klucz tajny, skojarzone z kontem magazynu. Pobiera to wypełnione automatycznie, jeśli konto magazynu znajduje się w tej samej subskrypcji co zadania usługi analiza strumienia.   |
|Kontener magazynu   | Kontenery umożliwiają logiczne grupowanie dla obiektów blob przechowywanych w usłudze Microsoft Azure Blob. Przekazywanie obiektu blob do usługi Blob, należy określić kontener dla tego obiektu blob.   |
|Wzorzec ścieżki   | Ścieżka używana do lokalizowania obiektów blob w określonym kontenerze. W ścieżce można określić co najmniej jedno wystąpienie następujących zmiennych 2:<BR>{date} {time}<BR>Przykład 1: products/{date}/{time}/product-list.csv<BR>Przykład 2: products/{date}/product-list.csv   |
|Format daty [opcjonalnie]   | {Date} użycie wewnątrz ścieżki określony format daty, w którym są zorganizowane obiektów blob można wybrać z listy rozwijanej obsługiwanych formatów.<BR>Przykład: RRRR/MM/DD/MM/DD/RRRR, itp.   |
|Format czasu [opcjonalnie]   | {Time} użycie wewnątrz ścieżki określony format czasu, w którym są zorganizowane obiektów blob można wybrać z listy rozwijanej obsługiwanych formatów.<BR>Przykład: HH gg/mm lub HH mm.  |
|Format serializacji zdarzeń   | Aby zapytania działały zgodnie z oczekiwaniami, usługa Stream Analytics musi znać format serializacji używany w przypadku przychodzących strumieni danych. Dla danych referencyjnych obsługiwane formaty to CSV i JSON.  |
|Kodowanie   | UTF-8 to jedyny obsługiwany obecnie format kodowania.  |

## <a name="generating-reference-data-on-a-schedule"></a>Generowanie danych referencyjnych zgodnie z harmonogramem
Jeśli dane odwołanie jest wolno zmieniającego zestawu danych, następnie obsługuje odświeżania odwołanie do danych jest włączona, określając wzorzec ścieżki w konfiguracji wejściowego przy użyciu {date} i {time} tokenów podstawienia. Analiza strumienia przejmuje zaktualizowano odwołanie definicje danych oparte na ten wzorzec ścieżki. Na przykład wzorzec `sample/{date}/{time}/products.csv` z datą o postaci **"RRRR-MM-DD"** i format czasu **"HH mm"** nakazuje Stream Analytics, aby pobrać zaktualizowane obiektu blob `sample/2015-04-16/17-30/products.csv` na 5:30 będzie na kwietnia 16 , Strefę czasową UTC 2015.

> [!NOTE]
> Obecnie zadania usługi analiza strumienia poszukaj odświeżania obiektów blob tylko wtedy, gdy czas komputera przechodzi do czasu zakodowane w nazwie obiektu blob. Na przykład zadanie będzie szukać `sample/2015-04-16/17-30/products.csv` jak to możliwe, ale nie wcześniej niż 5:30 będzie 16 kwietnia 2015 UTC czasu strefy. Będzie on *nigdy nie* wyszukiwania dla obiektu blob z wcześniej niż ostatni odnalezionej zakodowanego czasu.
> 
> Na przykład gdy to zadanie wyszukuje obiektu blob `sample/2015-04-16/17-30/products.csv` będzie ignorował wszystkie pliki z datą zakodowanego starszych niż 5:30 będzie 16 kwietnia 2015 r. tak więc jeśli późne odbieranych `sample/2015-04-16/17-25/products.csv` obiektu blob jest tworzony w tym samym kontenerze zadanie nie będzie używać go.
> 
> Podobnie jeśli `sample/2015-04-16/17-30/products.csv` dostarczać tylko godzinie 10:03 16 kwietnia 2015 r., ale nie obiektów blob z wcześniejszą datę znajduje się w kontenerze, zadania będą używać tego pliku, zaczynając od 10:03 PM 16 kwietnia 2015 i korzystanie z poprzednich danych odwołania do tego czasu.
> 
> Wyjątkiem jest gdy zadanie musi ponownie przetworzyć dane na czas lub gdy zadanie zostanie uruchomiona. Podczas uruchamiania zadania szuka najnowszych obiektu blob utworzonej przed zadanie określono godzinę rozpoczęcia. Pozwala to zapewnić, że istnieje **niepustym** odwołania zestawu danych podczas uruchamiania zadania. Jeśli nie można odnaleźć jednego, zadanie będzie wyświetlany podczas diagnostyki: `Initializing input without a valid reference data blob for UTC time <start time>`.
> 
> 

[Fabryka danych Azure](https://azure.microsoft.com/documentation/services/data-factory/) może służyć do organizowania zadanie tworzenia zaktualizowane obiekty BLOB wymagane przez usługę Stream Analytics aktualizacji definicji danych odwołania. Fabryka danych jest usługą integracji danych w chmurze, która służy do aranżacji i automatyzacji przenoszenia i przekształcania danych. Fabryka danych obsługuje [nawiązywania połączenia z dużą liczbą chmury na podstawie i lokalnych magazynów danych](../data-factory/copy-activity-overview.md) i łatwe przenoszenie danych zgodnie z ustalonym harmonogramem, który określisz. Aby uzyskać więcej informacji oraz wskazówki krok po kroku dotyczące sposobu konfigurowania potoku fabryki danych w celu wygenerowania danych referencyjnych dla usługi analiza strumienia, który odświeża na wstępnie zdefiniowany harmonogram, zapoznaj się z tym [próbki GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ReferenceDataRefreshForASAJobs).

## <a name="tips-on-refreshing-your-reference-data"></a>Porady dotyczące odświeżania danych odwołania
1. Zastępowanie obiektów blob danych odwołania nie spowoduje Stream Analytics ponownie załadować obiektu blob, a w niektórych przypadkach może spowodować niepowodzenie zadania. Zalecanym sposobem zmiany danych referencyjnych jest dodanie nowego obiektu blob przy użyciu tego samego wzorca kontenera i ścieżki zdefiniowane w danych wejściowych zadania i Użyj daty/godziny **większa** niż określona przez ostatnich obiektów blob w sekwencji.
2. Obiekty BLOB danych odwołania są **nie** uporządkowana obiektu blob "Ostatniej modyfikacji" czasu, ale tylko w programie określona w obiekcie blob Data i godzina nazwy przy użyciu {date} i {time} podstawienia.
3. Aby uniknąć konieczności listy dużą liczbę obiektów blob, rozważ usunięcie bardzo stare obiekty BLOB, dla których przetwarzanie zostanie już wykonane. Należy pamiętać, ASA może przejść, trzeba ponownie przetworzyć niewielkie w niektórych scenariuszach, takich jak ponowne uruchomienie komputera.

## <a name="next-steps"></a>Kolejne kroki
> [!div class="nextstepaction"]
> [Szybki Start: Tworzenie zadania usługi analiza strumienia za pomocą portalu Azure](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
