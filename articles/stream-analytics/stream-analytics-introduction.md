---
title: Omówienie usługi Azure Stream Analytics
description: Poznaj usługę Stream Analytics — zarządzaną usługę, która pomaga analizować dane przesyłane strumieniowo z Internetu rzeczy (IoT) w czasie rzeczywistym.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/17/2017
ms.openlocfilehash: 1912972b2a5ef40bcc61140225f1fdbcbb1535c3
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="what-is-stream-analytics"></a>Co to jest usługa Stream Analytics?

Usługa Azure Stream Analytics jest w pełni zarządzanym aparatem przetwarzania zdarzeń, który umożliwia ustawienie w czasie rzeczywistym obliczeń analitycznych na danych przesyłanych strumieniowo. Dane mogą pochodzić między innymi z urządzeń, czujników, witryn internetowych, kanałów informacyjnych mediów społecznościowych, aplikacji, systemów infrastruktury itp. 

Za pomocą usługi Stream Analytics można badać duże ilości danych przesyłanych strumieniowo z urządzeń lub procesów, wyodrębniać informacje ze strumienia danych oraz wyszukiwać wzorce, trendy i relacje. Dzięki tym wzorcom można wyzwalać inne procesy i działania, takie jak alerty, zautomatyzowane przepływy pracy, przekazywać informacje do narzędzia do raportowania lub przechowywać je w razie potrzeby późniejszego badania. 

Oto niektóre przykłady:

* Analiza danych giełdowych i alerty.
* Wykrywanie oszustw, identyfikacja danych i zabezpieczeń. 
* Analiza danych wygenerowanych przez czujniki i siłowniki.
* Analiza strumienia kliknięć w sieci Web.

## <a name="how-does-stream-analytics-work"></a>Jak działa usługa Stream Analytics?

Na tym diagramie przedstawiono potok analizy usługi Stream Analytics, przedstawiający sposób, w jaki dane są pozyskiwane, analizowane, a następnie wysyłane w celu prezentacji lub wykonania akcji. 

![Potok usługi Stream Analytics](./media/stream-analytics-introduction/stream_analytics_intro_pipeline.png)

Analiza przesyłania strumieniowego jest uruchamiana w powiązaniu ze źródłem danych przesyłanych strumieniowo. Dane można pozyskać z urządzenia do platformy Azure przy użyciu Centrum zdarzeń platformy Azure lub usługi IoT Hub. Można również pobrać dane z magazynu danych, na przykład z usługi Azure Blob Storage. 

Aby zbadać strumień, należy utworzyć *zadanie* usługi Stream Analytics, które określa, skąd pochodzą dane. Zadanie określa również *przekształcenie*, jak wyszukiwać dane, wzorce lub relacje. Dla tego zadania usługa Stream Analytics obsługuje język zapytań przypominający SQL, który umożliwia filtrowanie, sortowanie, agregowanie i łączenie danych przesyłanych strumieniowo w określonym czasie.

Na koniec zadanie określa miejsce, w którym znajdą się przekształcone dane. Możesz kontrolować, co należy zrobić w odpowiedzi na informacje, które zostały przeanalizowane. Na przykład w odpowiedzi na wyniki analizy można:

* Wysłać polecenie zmiany ustawień urządzenia. 
* Wysłać dane do monitorowanej kolejki w celu kontynuowania działań na podstawie otrzymanych wyników. 
* Wysłać dane do pulpitu nawigacyjnego usługi Power BI.
* Wysłać dane do magazynu, takiego jak Data Lake Store, baza danych SQL Azure albo usługa Azure Blog Storage.

Gdy zadanie jest uruchomione, można dostosować liczbę zdarzeń przetwarzanych na sekundę. Można także tworzyć dzienniki diagnostyczne na potrzeby rozwiązywania problemów.

## <a name="key-capabilities-and-benefits"></a>Najważniejsze funkcje i korzyści

Usługa Stream Analytics została zaprojektowana tak, aby była łatwa w użyciu, elastyczna i skalowalna do dowolnej wielkości zadania.

### <a name="connect-inputs-and-outputs"></a>Łączenie danych wejściowych i wyjściowych

Usługa Stream Analytics łączy się bezpośrednio z usługami [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) i [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) w celu pozyskiwania danych ze strumieni oraz z usługą [Azure Blob Storage](https://docs.microsoft.com/azure/storage/storage-introduction#blob-storage-accounts) w celu pozyskiwania danych historycznych. W przypadku danych z centrów zdarzeń usługę Stream Analytics można łączyć z innymi źródłami danych i aparatami przetwarzania. Dane wejściowe zadania mogą także zawierać dane referencyjne (dane statyczne lub zmieniające się powoli). Dane przesyłane strumieniowo można połączyć z tymi danymi referencyjnymi w celu wykonania operacji wyszukiwania w taki sam sposób, jak w przypadku zapytań bazy danych.

Możesz kierować dane wyjściowe zadania usługi Stream Analytics w wielu kierunkach. Zapisz dane w magazynie, takim jak usługa Azure Blob, Azure SQL Database, Azure Data Lake Store lub Azure Cosmos DB. Następnie możesz uruchomić analizę wsadową za pomocą usługi Azure HDInsight. Możesz też wysłać dane wyjściowe do innej usługi, aby zostały wykorzystane w innym procesie, takim jak centra zdarzeń, usługa Azure Service Bus, kolejki, lub do usługi Power BI w celu utworzenia wizualizacji.

### <a name="simple-to-use"></a>Łatwość użycia

Do definiowania przekształceń można użyć prostego, deklaratywnego [języka zapytań usługi Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx), który umożliwia tworzenie zaawansowanych analiz bez konieczności programowania. Język zapytań przyjmuje dane przesyłane strumieniowo jako dane wejściowe. Następnie można filtrować i sortować dane, agregować wartości, wykonywać obliczenia, łączyć dane (w ramach strumienia lub z danymi referencyjnymi) oraz używać funkcji geoprzestrzennych. Zapytania można edytować w portalu, korzystając z funkcji IntelliSense i funkcji sprawdzania składni. Można je także testować przy użyciu przykładowych danych wyodrębnianych na żywo ze strumienia.

### <a name="extensible-query-language"></a>Rozszerzalny język zapytań

Możliwości języka zapytań można rozszerzyć, definiując i wywołując dodatkowe funkcje. Można zdefiniować wywołania funkcji w usłudze Azure Machine Learning, aby skorzystać z rozwiązań usługi Azure Machine Learning. Można również zintegrować funkcje JavaScript zdefiniowane przez użytkownika (UDF, user-defined functions), aby wykonywać złożone obliczenia w ramach zapytań usługi Stream Analytics.

### <a name="scalable"></a>Skalowalność

Usługa Stream Analytics może obsługiwać maksymalnie 1 GB danych przychodzących w ciągu sekundy. Integracja z usługami [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) i [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) pozwala zadaniom pozyskiwać w ciągu sekundy miliony zdarzeń przychodzących z połączonych urządzeń, strumieni kliknięć, plików dziennika i innych źródeł. Za pomocą funkcji partycji centrów zdarzeń obliczenia można podzielić na etapy logiczne, z których każdy może zostać dalej podzielony w celu zwiększenia skalowalności.

### <a name="low-cost"></a>Niski koszt

Jako usługa w chmurze usługa Stream Analytics jest zoptymalizowana pod kątem niskiego kosztu. Usługę zaprojektowano pod kątem płatności zgodnie z użyciem jednostek przesyłania strumieniowego i ilości przetwarzanych danych. Użycie jest określane na podstawie ilości przetworzonych zdarzeń i ilości mocy obliczeniowej aprowizowanej w klastrze zadań.

### <a name="reliable"></a>Niezawodność

Jako usługa zarządzana usługa Stream Analytics pomaga uniknąć utraty danych i zapewnia ciągłość działalności biznesowej. Jeśli wystąpią błędy, usługa zapewnia wbudowane funkcje odzyskiwania. Dzięki możliwości wewnętrznego przechowywania stanu usługa zapewnia powtarzalne wyniki, co gwarantuje możliwość archiwizacji zdarzeń i ponownego stosowania przetwarzania w przyszłości, zawsze uzyskując takie same wyniki. Umożliwia to klientom przechodzenie wstecz w czasie i badanie obliczeń podczas ustalania głównej przyczyny problemu, analizy warunkowej itp.

## <a name="next-steps"></a>Kolejne kroki

* Rozpoczynanie pracy przez [eksperymentowanie z danymi wejściowymi i zapytaniami z urządzeń IoT](stream-analytics-get-started-with-azure-stream-analytics-to-process-data-from-iot-devices.md).
* Tworzenie [kompleksowego rozwiązania usługi Stream Analytics](stream-analytics-real-time-fraud-detection.md), które analizuje metadane telefonu, wyszukując fałszywe wywołania.
* Znajdź odpowiedzi na swoje pytania dotyczące usługi Stream Analytics na [forum usługi Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

