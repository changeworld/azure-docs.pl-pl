---
title: Używanie danych referencyjnych do wyszukiwania w usłudze Azure Stream Analytics
description: W tym artykule opisano sposób używania danych referencyjnych do wyszukiwania lub korelowania danych w projekcie kwerendy zadania usługi Azure Stream Analytics.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/8/2019
ms.openlocfilehash: b3808524706b13761dd8eccffa301c602d08f481
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267289"
---
# <a name="using-reference-data-for-lookups-in-stream-analytics"></a>Korzystanie z danych referencyjnych dla odnośnych w usłudze Stream Analytics

Dane referencyjne (znane również jako tabela odnośności) to skończony zestaw danych, który ma charakter statyczny lub powoli się zmienia, używany do odnośności lub rozszerzania strumieni danych. Na przykład w scenariuszu IoT można przechowywać metadane dotyczące czujników (które nie zmieniają się często) w danych referencyjnych i łączyć go ze strumieniami danych IoT w czasie rzeczywistym. Usługa Azure Stream Analytics ładuje dane referencyjne w pamięci, aby osiągnąć przetwarzanie strumienia o małych opóźnieniach. Aby korzystać z danych referencyjnych w zadaniu usługi Azure Stream Analytics, zazwyczaj należy użyć [sprzężenia danych referencyjnych](https://docs.microsoft.com/stream-analytics-query/reference-data-join-azure-stream-analytics) w zapytaniu. 

Usługa Stream Analytics obsługuje magazyn obiektów Blob platformy Azure i usługę Azure SQL Database jako warstwę magazynu danych referencyjnych. Można również przekształcić i/lub skopiować dane referencyjne do magazynu obiektów Blob z usługi Azure Data Factory, aby użyć [dowolnej liczby chmurowych i lokalnych magazynów danych.](../data-factory/copy-activity-overview.md)

## <a name="azure-blob-storage"></a>Azure Blob Storage

Dane referencyjne są modelowane jako sekwencja obiektów blob (zdefiniowanych w konfiguracji wejściowej) w porządku rosnącym daty/godziny określonej w nazwie obiektu blob. Obsługuje **tylko** dodawanie na końcu sekwencji przy użyciu daty/godziny **większej** niż określona przez ostatni obiekt blob w sekwencji.

### <a name="configure-blob-reference-data"></a>Konfigurowanie danych odwołania do obiektów blob

Aby skonfigurować dane referencyjne, należy najpierw utworzyć dane wejściowe typu **Dane referencyjne**. W poniższej tabeli wyjaśniono każdą właściwość, którą należy podać podczas tworzenia danych referencyjnych z opisem:

|**Nazwa właściwości**  |**Opis**  |
|---------|---------|
|Alias danych wejściowych   | Przyjazna nazwa, która będzie używana w kwerendzie zadania, aby odwołać się do tego danych wejściowych.   |
|Konto magazynu   | Nazwa konta magazynu, na którym znajdują się obiekty blob. Jeśli jest w tej samej subskrypcji co zadanie usługi Stream Analytics, możesz wybrać ją z listy rozwijanej.   |
|Klucz konta magazynu   | Klucz tajny skojarzony z kontem magazynu. Spowoduje to automatyczne wypełnianie, jeśli konto magazynu znajduje się w tej samej subskrypcji co zadanie usługi Stream Analytics.   |
|Kontener do przechowywania   | Kontenery zapewniają logiczne grupowanie obiektów blob przechowywanych w usłudze obiektów Blob platformy Microsoft Azure. Podczas przekazywania obiektu blob do usługi obiektów Blob, należy określić kontener dla tego obiektu blob.   |
|Wzorzec ścieżki   | Ścieżka używana do lokalizowania obiektów blob w określonym kontenerze. W ramach ścieżki można określić jedno lub więcej wystąpień następujących 2 zmiennych:<BR>{date}, {time}<BR>Przykład 1: produkty/{date}/{time}/product-list.csv<BR>Przykład 2: produkty/{date}/product-list.csv<BR>Przykład 3: product-list.csv<BR><br> Jeśli obiekt blob nie istnieje w określonej ścieżce, zadanie usługi Stream Analytics będzie czekać przez czas nieokreślony, aż obiekt blob staną się dostępne.   |
|Format daty [opcjonalnie]   | Jeśli użyto {date} w określonym wzorze ścieżki, można wybrać format daty, w którym obiekty blob są zorganizowane z listy rozwijanej obsługiwanych formatów.<BR>Przykład: YYYY/MM/DD, MM/DD/YYYY itp.   |
|Format czasu [opcjonalnie]   | Jeśli użyto {time} w określonym wzorze ścieżki, można wybrać format czasu, w którym obiekty blob są zorganizowane z listy rozwijanej obsługiwanych formatów.<BR>Przykład: HH, HH/mm lub HH-mm.  |
|Format serializacji zdarzeń   | Aby upewnić się, że zapytania działają zgodnie z oczekiwaniami, usługa Stream Analytics musi wiedzieć, którego formatu serializacji używasz dla przychodzących strumieni danych. W przypadku danych referencyjnych obsługiwane formaty to CSV i JSON.  |
|Kodowanie   | UTF-8 jest jedynym obsługiwanym formatem kodowania w tej chwili.  |

### <a name="static-reference-data"></a>Statyczne dane referencyjne

Jeśli dane referencyjne nie powinny ulec zmianie, obsługa statycznych danych referencyjnych jest włączona przez określenie ścieżki statycznej w konfiguracji wejściowej. Usługa Azure Stream Analytics pobiera obiekt blob z określonej ścieżki. Tokeny podstawienia {date} i {time} nie są wymagane. Ponieważ dane referencyjne są niezmienne w usłudze Stream Analytics, zastępowanie statycznego obiektu blob danych referencyjnych nie jest zalecane.

### <a name="generate-reference-data-on-a-schedule"></a>Generowanie danych referencyjnych zgodnie z harmonogramem

Jeśli dane referencyjne są powoli zmieniającym się zestawem danych, obsługa odświeżania danych referencyjnych jest włączona przez określenie wzorca ścieżki w konfiguracji wejściowej przy użyciu tokenów podstawiania {date} i {time}. Usługa Stream Analytics pobiera zaktualizowane definicje danych referencyjnych na podstawie tego wzorca ścieżki. Na przykład `sample/{date}/{time}/products.csv` wzorzec z formatem **daty "YYYY-MM-DD"** i formatem czasu **"HH-mm"** nakazuje `sample/2015-04-16/17-30/products.csv` firmie Stream Analytics pobranie zaktualizowanego obiektu blob o godzinie 17:30 w strefie czasowej UTC 16 kwietnia 2015 r.

Usługa Azure Stream Analytics automatycznie skanuje w poszukiwaniu odświeżonych obiektów blob danych referencyjnych w odstępie jednej minuty. Jeśli obiekt blob z sygnaturą czasową 10:30:00 zostanie przekazany z niewielkim opóźnieniem (na przykład 10:30:30), można zauważyć niewielkie opóźnienie w zadaniu usługi Stream Analytics odwołując się do tego obiektu blob. Aby uniknąć takich scenariuszy, zaleca się przekazać obiekt blob wcześniej niż docelowy czas efektywny (10:30:00 w tym przykładzie), aby umożliwić zadanie usługi Stream Analytics wystarczająco dużo czasu, aby odnaleźć i załadować go do pamięci i wykonać operacje. 

> [!NOTE]
> Obecnie zadania usługi Stream Analytics szukają odświeżania obiektu blob tylko wtedy, gdy czas pracy maszyny zostanie przesunął się do czasu zakodowanego w nazwie obiektu blob. Na przykład zadanie będzie `sample/2015-04-16/17-30/products.csv` szukać tak szybko, jak to możliwe, ale nie wcześniej niż 5:30 PM w dniu 16 kwietnia 2015 strefa czasowa UTC. Nigdy *nie* będzie szukać obiektu blob z zakodowanym czasem wcześniej niż ostatni, który został odkryty.
> 
> Na przykład po znalezieniu zadania `sample/2015-04-16/17-30/products.csv` obiektu blob zignoruje wszystkie pliki z zakodowaną datą wcześniejszą niż 16 kwietnia 2015 r. 16 kwietnia 2015 r., więc jeśli obiekt blob przylatujący `sample/2015-04-16/17-25/products.csv` z opóźnieniem zostanie utworzony w tym samym kontenerze, zadanie nie będzie go używać.
> 
> Podobnie jeśli `sample/2015-04-16/17-30/products.csv` jest produkowany tylko w 10:03 PM 16 kwietnia 2015, ale nie obiekt blob z wcześniejszą datą jest obecny w kontenerze, zadanie użyje tego pliku począwszy od 22:03 16 kwietnia 2015 i używać poprzednich danych referencyjnych do tego czasu.
> 
> Wyjątek od tego jest, gdy zadanie musi ponownie przetworzyć dane w czasie lub po pierwszym uruchomieniu zadania. W czasie rozpoczęcia zadanie szuka najnowszego obiektu blob wyprodukowanego przed określoną godziną rozpoczęcia zadania. Odbywa się to w celu zapewnienia, że istnieje **niepusty** zestaw danych referencyjnych po uruchomieniu zadania. Jeśli nie można go odnaleźć, zadanie wyświetla następującą diagnostykę: `Initializing input without a valid reference data blob for UTC time <start time>`.

[Usługa Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) może służyć do organizowania zadania tworzenia zaktualizowanych obiektów blob wymaganych przez usługę Stream Analytics w celu zaktualizowania definicji danych referencyjnych. Fabryka danych jest usługą integracji danych w chmurze, która służy do aranżacji i automatyzacji przenoszenia i przekształcania danych. Usługa Data Factory obsługuje [łączenie się z dużą liczbą magazynów danych opartych na chmurze i lokalnych](../data-factory/copy-activity-overview.md) oraz łatwe przenoszenie danych według określonego harmonogramu. Aby uzyskać więcej informacji i wskazówki krok po kroku dotyczące konfigurowania potoku fabryki danych w celu generowania danych referencyjnych dla usługi Stream Analytics, który jest odświeżane zgodnie ze wstępnie zdefiniowanym harmonogramem, zapoznaj się z tym [przykładem github](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ReferenceDataRefreshForASAJobs).

### <a name="tips-on-refreshing-blob-reference-data"></a>Porady dotyczące odświeżania danych referencyjnych obiektów blob

1. Nie należy zastępystywnie obiektów blob danych referencyjnych, ponieważ są one niezmienne.
2. Zalecanym sposobem odświeżania danych referencyjnych jest:
    * Użyj {date}/{time} we wzorcu ścieżki
    * Dodawanie nowego obiektu blob przy użyciu tego samego wzorca kontenera i ścieżki zdefiniowanego w danych wejściowych zadania
    * Użyj daty/godziny **większej** niż określona przez ostatni obiekt blob w sekwencji.
3. Obiekty BLOB danych odwołań **nie** są uporządkowane przez czas "Ostatniej modyfikacji" obiektu blob, ale tylko przez godzinę i datę określoną w nazwie obiektu blob przy użyciu podstawienia {date} i {time}.
3. Aby uniknąć konieczności listy dużej liczby obiektów blob, należy wziąć pod uwagę usunięcie bardzo starych obiektów blob, dla których przetwarzanie nie będzie już wykonywane. Należy pamiętać, że ASA może przejść do ponownego przetworzenia niewielką kwotę w niektórych scenariuszach, takich jak ponowne uruchomienie.

## <a name="azure-sql-database"></a>Azure SQL Database

Dane referencyjne usługi Azure SQL Database są pobierane przez zadanie usługi Stream Analytics i są przechowywane jako migawka w pamięci do przetworzenia. Migawka danych referencyjnych jest również przechowywana w kontenerze na koncie magazynu, który można określić w ustawieniach konfiguracji. Kontener jest tworzony automatycznie po uruchomieniu zadania. Jeśli zadanie zostanie zatrzymane lub wejdzie w stan awarii, automatycznie utworzone kontenery są usuwane po ponownym uruchomieniu zadania.  

Jeśli dane referencyjne są powoli zmieniającym się zestawem danych, należy okresowo odświeżać migawkę używaną w zadaniu. Usługa Stream Analytics umożliwia ustawienie częstotliwości odświeżania podczas konfigurowania połączenia wejściowego usługi Azure SQL Database. Środowisko uruchomieniowe usługi Stream Analytics będzie wysyłać zapytania do bazy danych SQL usługi Azure w interwale określonym przez częstotliwość odświeżania. Najszybsza obsługiwana częstotliwość odświeżania jest raz na minutę. Dla każdego odświeżania usługa Stream Analytics przechowuje nową migawkę na koncie magazynu.

Usługa Stream Analytics udostępnia dwie opcje wykonywania zapytań o bazę danych SQL platformy Azure. Kwerenda migawka jest obowiązkowa i musi być uwzględniona w każdym zadaniu. Usługa Stream Analytics uruchamia kwerendę migawki okresowo na podstawie interwału odświeżania i używa wyniku kwerendy (migawki) jako zestawu danych referencyjnych. Kwerenda migawki powinna pasować do większości scenariuszy, ale jeśli napotkasz problemy z wydajnością z dużymi zestawami danych i szybkością szybkiego odświeżania, możesz użyć opcji kwerendy różnicowej. Kwerendy, które trwać dłużej niż 60 sekund, aby zwrócić zestaw danych referencyjnych spowoduje limit czasu.

Dzięki opcji kwerendy różnicowej usługa Stream Analytics uruchamia kwerendę migawki początkowo w celu uzyskania zestawu danych referencyjnych linii bazowej. Następnie usługa Stream Analytics uruchamia kwerendę różnicową okresowo na podstawie interwału odświeżania w celu pobrania zmian przyrostowych. Te przyrostowe zmiany są stale stosowane do zestawu danych referencyjnych, aby go aktualizować. Użycie kwerendy różnicowej może pomóc w zmniejszeniu kosztów magazynowania i operacji we/wy sieci.

### <a name="configure-sql-database-reference"></a>Konfigurowanie odwołania do bazy danych SQL

Aby skonfigurować dane referencyjne bazy danych SQL, należy najpierw utworzyć dane **wejściowe danych referencyjnych.** W poniższej tabeli wyjaśniono każdą właściwość, która należy podać podczas tworzenia danych referencyjnych danych wejściowych z jego opisem. Aby uzyskać więcej informacji, zobacz [Używanie danych referencyjnych z bazy danych SQL dla zadania usługi Azure Stream Analytics](sql-reference-data.md).

Można użyć [wystąpienia zarządzanego usługi Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) jako danych wejściowych danych referencyjnych. Musisz [skonfigurować publiczny punkt końcowy w wystąpieniu zarządzanym bazy danych SQL platformy Azure,](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure) a następnie ręcznie skonfigurować następujące ustawienia w usłudze Azure Stream Analytics. Maszyna wirtualna platformy Azure z uruchomionym programem SQL Server z dołączoną bazą danych jest również obsługiwana przez ręczne konfigurowanie poniższych ustawień.

|**Nazwa właściwości**|**Opis**  |
|---------|---------|
|Alias danych wejściowych|Przyjazna nazwa, która będzie używana w kwerendzie zadania, aby odwołać się do tego danych wejściowych.|
|Subskrypcja|Wybierz subskrypcję|
|baza danych|Baza danych SQL platformy Azure zawierająca dane referencyjne. W przypadku wystąpienia zarządzanego usługi Azure SQL Database wymagane jest określenie portu 3342. Na przykład *plik sampleserver.public.database.windows.net,3342*|
|Nazwa użytkownika|Nazwa użytkownika skojarzona z bazą danych SQL azure.|
|Hasło|Hasło skojarzone z bazą danych SQL platformy Azure.|
|Okresowo odświeżanie|Ta opcja umożliwia wybranie częstotliwości odświeżania. Wybranie opcji "On" umożliwia określenie częstotliwości odświeżania w DD:HH:MM.|
|Kwerenda migawki|Jest to domyślna opcja kwerendy, która pobiera dane referencyjne z bazy danych SQL.|
|Kwerenda delta|W przypadku zaawansowanych scenariuszy z dużymi zestawami danych i krótką częstotliwością odświeżania wybierz opcję dodania kwerendy różnicowej.|

## <a name="size-limitation"></a>Ograniczenie rozmiaru

Usługa Stream Analytics obsługuje dane referencyjne **o maksymalnym rozmiarze 300 MB**. Limit 300 MB maksymalnego rozmiaru danych referencyjnych jest osiągalny tylko w przypadku prostych zapytań. Wraz ze wzrostem złożoności kwerendy w celu uwzględnienia przetwarzania stanowego, takiego jak agregaty okienne, sprzężenia czasowe i funkcje analityczne czasowe, oczekuje się, że zmniejszy się maksymalny obsługiwany rozmiar danych referencyjnych. Jeśli usługa Azure Stream Analytics nie może załadować danych referencyjnych i wykonać złożonych operacji, zadanie zabraknie pamięci i zakończy się niepowodzeniem. W takich przypadkach wskaźnik wykorzystania SU % osiągnie 100%.    

|**Liczba jednostek przesyłania strumieniowego**  |**Obsługa maksymalnego rozmiaru (w MB)**  |
|---------|---------|
|1   |50   |
|3   |150   |
|6 i więcej   |300   |

Zwiększenie liczby jednostek przesyłania strumieniowego zadania powyżej 6 nie zwiększa maksymalny obsługiwany rozmiar danych referencyjnych.

Obsługa kompresji nie jest dostępna dla danych referencyjnych. 

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Szybki start: tworzenie zadania usługi Stream Analytics przy użyciu witryny Azure Portal](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
