---
title: Korzystanie z danych referencyjnych dla wyszukiwania w usłudze Azure Stream Analytics
description: W tym artykule opisano, jak użyć danych referencyjnych do wyszukiwania lub skorelowania danych w projekcie zapytanie zadania usługi Azure Stream Analytics.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/29/2019
ms.openlocfilehash: 4ddbec6b163a939c1663630e39e89140ac6f7efe
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60761492"
---
# <a name="using-reference-data-for-lookups-in-stream-analytics"></a>Przy użyciu danych referencyjnych dla wyszukiwania w usłudze Stream Analytics
Dane referencyjne (znany także jako tabela odnośnika) jest ograniczone zestaw danych, który jest statyczny lub wolno zmieniający się charakter, używane do wyszukiwania lub do skorelowania ze strumienia danych. Można na przykład w scenariuszu IoT są przechowywane metadane dotyczące czujniki, (które nie zmieniają się często) w danych referencyjnych i przyłączyć go ze strumieniami danych IoT w czasie rzeczywistym. Usługa Azure Stream Analytics ładuje dane referencyjne w pamięci w celu uzyskania małych opóźnień przetwarzania strumienia. Aby korzystać z danych referencyjnych w ramach zadania usługi Azure Stream Analytics, będzie na ogół służy [Dołącz dane odwołanie](https://msdn.microsoft.com/library/azure/dn949258.aspx) w zapytaniu. 

Stream Analytics obsługuje usługi Azure Blob storage i Azure SQL Database jako Warstwa przechowywania danych referencyjnych. Użytkownik może także przekształcić i/lub skopiowania do magazynu obiektów Blob usługi Azure Data Factory, aby użyć danych referencyjnych [dowolną liczbę chmurowych i lokalnych magazynów danych](../data-factory/copy-activity-overview.md).

## <a name="azure-blob-storage"></a>Azure Blob Storage

Dane referencyjne są modelowane jako sekwencję obiektów blob (zdefiniowany w konfiguracji danych wejściowych) w kolejności rosnącej kolejności daty/godziny określone w nazwie obiektu blob. Jego **tylko** obsługuje dodawanie do końca sekwencji za pomocą daty/godziny **większą** niż określona przez ostatni obiekt blob w sekwencji.

### <a name="configure-blob-reference-data"></a>Konfigurowanie obiektu blob danych referencyjnych

Aby skonfigurować swoje dane referencyjne, najpierw musisz utworzyć danych wejściowych, którego typ jest **danych referencyjnych**. W poniższej tabeli opisano każdej właściwości, należy podać podczas tworzenia danych referencyjnych dane wejściowe z jego opisem:

|**Nazwa właściwości**  |**Opis**  |
|---------|---------|
|Alias danych wejściowych   | Przyjazna nazwa, która będzie służyć w zapytaniu zadania można odwoływać się do tych danych wejściowych.   |
|Konto magazynu   | Nazwa konta magazynu, w którym znajdują się obiekty BLOB. Jeśli jest w tej samej subskrypcji co zadanie usługi Stream Analytics, możesz wybrać go z listy rozwijanej.   |
|Klucz konta magazynu   | Klucz tajny skojarzony z kontem magazynu. To jest automatycznie wypełniana w przypadku konta magazynu w tej samej subskrypcji co zadanie usługi Stream Analytics.   |
|Kontener magazynu   | Kontenery umożliwiają logiczne grupowanie obiektów blob przechowywanych w usłudze Microsoft Azure Blob. Podczas przekazywania obiektu blob do usługi obiektów Blob, należy określić kontener dla tego obiektu blob.   |
|Wzorzec ścieżki   | Ścieżka używana do lokalizowania obiektów blob w określonym kontenerze. W ścieżce można określić co najmniej jedno wystąpienie następujących 2 zmiennych:<BR>{date}, {time}<BR>Przykład 1: products/{date}/{time}/product-list.csv<BR>Przykład 2: products/{date}/product-list.csv<BR>Przykład 3: Lista-produktów.csv<BR><br> Jeśli obiekt blob nie istnieje w określonej ścieżce, zadanie usługi Stream Analytics będzie czekać w nieskończoność dla obiektu blob staną się dostępne.   |
|Format daty [opcjonalnie]   | Jeśli używano {date} w ramach wzorzec ścieżki, który określiłeś, format daty, w którym sklasyfikowano obiektów blob można wybrać z listy rozwijanej obsługiwanych formatów.<BR>Przykład: RRRR/MM/DD/MM/DD/RRRR, itp.   |
|Format czasu [opcjonalnie]   | Jeśli używano {time} w ramach wzorzec ścieżki, który określiłeś, format czasu, w którym sklasyfikowano obiektów blob można wybrać z listy rozwijanej obsługiwanych formatów.<BR>Przykład: HH gg/mm i HH mm.  |
|Format serializacji zdarzeń   | Aby zapytania działały zgodnie z oczekiwaniami, usługa Stream Analytics musi znać format serializacji używany w przypadku przychodzących strumieni danych. Dane referencyjne są obsługiwane formaty: CSV i JSON.  |
|Kodowanie   | UTF-8 to jedyny obsługiwany obecnie format kodowania.  |

### <a name="static-reference-data"></a>Statyczne dane referencyjne

Jeśli dane odwołanie nie powinna się zmienić, obsługuje odwołania statyczne, danych jest włączona, określając ścieżkę statycznych w konfiguracji danych wejściowych. Usługa Azure Stream Analytics przejmuje obiektu blob z określonej ścieżki. {date} i {time} podstawienia tokenów nie są wymagane. Dane referencyjne są niezmienne w usłudze Stream Analytics. W związku z tym zastępując obiekt blob danych odwołania statycznego nie jest zalecane.

### <a name="generate-reference-data-on-a-schedule"></a>Generowanie danych referencyjnych, zgodnie z harmonogramem

Jeśli dane odwołanie jest wolno zmieniający zestawu danych, następnie wspierać odświeżania odwołanie do danych jest włączona, określając wzorzec ścieżki w danych wejściowych konfiguracji za pomocą {date} i {time} tokenów podstawienia. Stream Analytics przejmuje definicje danych zaktualizowano odwołania na podstawie tego wzorca ścieżki. Na przykład wzorzec `sample/{date}/{time}/products.csv` z datą o postaci **"RRRR-MM-DD"** i format czasu **"HH mm"** powoduje, że usługi Stream Analytics do pobrania zaktualizowanych obiektów blob `sample/2015-04-16/17-30/products.csv` o 17:30:00 na 16 kwietnia , Strefie czasowej UTC 2015.

Usługa Azure Stream Analytics automatycznie skanuje w poszukiwaniu obiekty BLOB danych referencyjnych odświeżane w odstępach jednej minuty.

> [!NOTE]
> Obecnie zadania usługi Stream Analytics Wyszukaj odświeżanie obiektów blob tylko wtedy, gdy czas maszyny jest przesuwany do czasu zakodowane w nazwie obiektu blob. Na przykład, zadanie będzie szukać `sample/2015-04-16/17-30/products.csv` tak szybko, jak to możliwe, ale nie wcześniej niż 17:30:00 od 16 kwietnia 2015 UTC strefę czasową. Będzie ono *nigdy nie* Wyszukaj obiekt blob z wcześniej, niż ostatni odnaleziono zakodowany czasu.
> 
> Na przykład Po zadania znajduje się obiekt blob `sample/2015-04-16/17-30/products.csv` go będzie ignorować wszystkie pliki z datą zakodowany starszych niż 17:30:00 16 kwietnia 2015 r. Jeśli opóźnione odebranych `sample/2015-04-16/17-25/products.csv` tworzony obiekt blob w tym samym kontenerze zadania nie są one używane.
> 
> Podobnie jeśli `sample/2015-04-16/17-30/products.csv` tylko jest generowany w 22:03:00 16 kwietnia 2015 r., ale nie obiektu blob z datą wcześniejszą znajduje się w kontenerze, zadanie będzie używać tego pliku, zaczynając od 10: 18:03 16 kwietnia 2015 r. i użyć danych referencyjnych, poprzednie do tego czasu.
> 
> Wyjątek, to gdy zadanie musi ponownie przetworzyć dane w czasie, lub gdy zadanie jest pierwszym uruchomieniu. Podczas uruchamiania zadania szuka ostatnich obiektów blob utworzone przed określony czas rozpoczęcia zadania. W ten sposób upewnić się, że **niepuste** zestaw danych referencyjnych podczas uruchamiania zadania. Jeśli nie można odnaleźć jednego, zadania są wyświetlane podczas diagnostyki: `Initializing input without a valid reference data blob for UTC time <start time>`.

[Usługa Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) mogą być używane do organizowania zadanie tworzenia zaktualizowanych obiektów blob do aktualizacji definicji danych odwołania wymagane przez usługę Stream Analytics. Fabryka danych jest usługą integracji danych w chmurze, która służy do aranżacji i automatyzacji przenoszenia i przekształcania danych. Usługa Data Factory obsługuje [nawiązywania połączenia z dużą liczbą chmury na podstawie lokalnych i w magazynach danych](../data-factory/copy-activity-overview.md) i łatwe przenoszenie danych zgodnie z ustalonym harmonogramem, który określisz. Aby uzyskać więcej informacji oraz wskazówki krok po kroku dotyczące sposobu konfigurowania potoku usługi Data Factory do generowania danych referencyjnych dla usługi Stream Analytics, która odświeża zgodnie ze wstępnie zdefiniowanym harmonogramem, zapoznaj się z tą [przykładowe GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ReferenceDataRefreshForASAJobs).

### <a name="tips-on-refreshing-blob-reference-data"></a>Porady dotyczące odświeżania danych referencyjnych obiektów blob

1. Nie należy zastępować obiekty BLOB danych referencyjnych są niezmienne.
2. Jest to zalecany sposób odświeżania danych referencyjnych:
    * Użyj {date} / {time} wzorzec ścieżki
    * Dodaj nowy obiekt blob przy użyciu tego samego wzorca kontenera i ścieżka zdefiniowana w danych wejściowych zadania
    * Użyj daty/godziny **większą** niż określona przez ostatni obiekt blob w sekwencji.
3. Obiekty BLOB danych referencyjnych są **nie** uporządkowane w czasie "Ostatniej modyfikacji" obiektu blob, ale tylko w programie Data i godzina określone w obiekcie blob przy użyciu {date} i {time} podstawienia.
3. Aby uniknąć konieczności listy dużą liczbę obiektów blob, rozważ usunięcie bardzo starych obiektów blob, dla których przetwarzanie zostanie już wykonane. Należy pamiętać, ASA może przejść, trzeba ponownie przetworzyć małą ilością w niektórych scenariuszach, takich jak ponowne uruchomienie komputera.

## <a name="azure-sql-database-preview"></a>Usługa Azure SQL Database (wersja zapoznawcza)

Danych odwołania w usłudze Azure SQL Database są pobierane przez zadanie usługi Stream Analytics i są przechowywane jako migawka w pamięci dla przetwarzania. Migawki danych odwołania także są przechowywane w kontenerze na koncie magazynu, który określisz w ustawieniach konfiguracji. Kontener jest utworzony automatycznie podczas uruchamiania zadania. Jeśli zadanie jest zatrzymana lub przechodzi do stanu nie powiodło się, kontenery utworzone automatycznie są usuwane po ponownym uruchomieniu zadania.  

Dane odwołanie jest wolno zmieniający zestawu danych, należy okresowo odświeżane migawkę, która jest używana w ramach zadania. Stream Analytics umożliwia ustawić częstotliwość odświeżania, podczas konfigurowania połączenia danych wejściowych usługi Azure SQL Database. Środowisko uruchomieniowe usługi Stream Analytics będzie zapytania usługi Azure SQL Database z interwałem określonym przez częstotliwość odświeżania. To najszybszy obsługiwana częstotliwość odświeżania raz na minutę. W przypadku każdego odświeżania usługi Stream Analytics zapisuje nową migawkę w podane konto magazynu.

Stream Analytics oferuje dwie opcje zapytań usługi Azure SQL Database. Zapytanie migawki jest wymagane i muszą być zawarte w poszczególnych zadaniach. Usługi Stream Analytics wykonuje zapytanie migawki okresowo oparte na Twoje interwał odświeżania i używa wynik kwerendy (Migawka) jako zestawu danych referencyjnych. Zapytanie migawki powinien się zmieścić większości scenariuszy, ale Jeśli napotkasz problemy z wydajnością z dużych zestawów danych i częstotliwości odświeżania szybkie, można użyć opcji zapytania delta. Zapytania, które zajmują więcej niż 60 sekund, aby zwrócić odwołanie do zestawu danych spowoduje przekroczenie limitu czasu.

Z opcją delta zapytania usługi Stream Analytics wykonuje kwerendę migawki początkowo, aby uzyskać bazowego zestawu danych referencyjnych. Po wykonaniu Stream Analytics będzie zapytania różnicowego okresowo oparte na Twoje interwał odświeżania można pobrać zmiany przyrostowe. Te zmiany przyrostowe ciągle są stosowane do zestawu danych referencyjnych, aby zachować aktualność. Użycie zapytania różnicowego może pomóc zmniejszyć koszt magazynu i sieci operacji We/Wy.

### <a name="configure-sql-database-reference"></a>Konfiguruj odwołanie do bazy danych SQL

Aby skonfigurować dane odwołanie do bazy danych SQL, należy najpierw utworzyć **danych referencyjnych** danych wejściowych. W poniższej tabeli opisano każdej właściwości, należy podać podczas tworzenia danych referencyjnych dane wejściowe z jego opis. Aby uzyskać więcej informacji, zobacz [Użyj odwołanie do danych z bazy danych SQL dla zadania usługi Azure Stream Analytics](sql-reference-data.md).

|**Nazwa właściwości**|**Opis**  |
|---------|---------|
|Alias danych wejściowych|Przyjazna nazwa, która będzie służyć w zapytaniu zadania można odwoływać się do tych danych wejściowych.|
|Subskrypcja|Wybierz subskrypcję|
|Database (Baza danych)|Azure SQL Database zawierającą dane odwołanie.|
|Nazwa użytkownika|Nazwa użytkownika skojarzony z usługi Azure SQL Database.|
|Hasło|Hasło skojarzone z usługi Azure SQL Database.|
|Odświeżaj okresowo|Ta opcja pozwala wybrać częstotliwość odświeżania. Wybieranie "Włączone" pozwoli określić częstotliwość odświeżania w DD:HH:MM.|
|Zapytanie migawki|Jest to domyślna opcja zapytanie pobiera dane referencyjne z bazy danych SQL.|
|Zapytanie delta|W przypadku zaawansowanych scenariuszy z dużych zestawów danych i krótki częstotliwość odświeżania, dodasz zapytania różnicowego.|

## <a name="size-limitation"></a>Ograniczenie rozmiaru

Stream Analytics obsługuje dane referencyjne z **maksymalny rozmiar 300 MB**. 300 MB limit maksymalnego rozmiaru danych referencyjnych jest osiągalna tylko z prostych zapytań. W miarę zwiększania stopnia złożoności zapytania, obejmujący stanowych przetwarzania, takich jak okna agregacje, sprzężenia danych czasowych i danych czasowych funkcje analityczne, oczekuje się, co przekracza maksymalną obsługiwaną rozmiar zmniejszy danych odwołania. Jeśli usługi Azure Stream Analytics nie można załadować danych referencyjnych i wykonywania złożonych operacji, zadanie będzie skończy się pamięć i zakończyć się niepowodzeniem. W takich przypadkach metryki SU % będzie korzystał z 100%.    

|**Liczbę jednostek przesyłania strumieniowego**  |**O rozmiar maksymalny obsługiwany (w MB)**  |
|---------|---------|
|1   |50   |
|3   |150   |
|6 i nie tylko   |300   |

Zwiększenie liczby jednostek przesyłania strumieniowego zadania poza 6 nie zwiększa maksymalny obsługiwany rozmiar danych referencyjnych.

Obsługa kompresji nie jest dostępna dla danych referencyjnych. 

## <a name="next-steps"></a>Kolejne kroki
> [!div class="nextstepaction"]
> [Szybki start: Tworzenie zadania usługi Stream Analytics przy użyciu witryny Azure portal](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
