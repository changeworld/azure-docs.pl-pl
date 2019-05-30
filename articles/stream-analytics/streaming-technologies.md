---
title: Wybierz analizy w czasie rzeczywistym i przesyłania strumieniowego technologii przetwarzania na platformie Azure
description: Więcej informacji na temat sposobu wybierania analizy bezpośrednio w czasie rzeczywistym oraz technologii przetwarzania strumieniowego przetwarzania do budowania aplikacji na platformie Azure.
author: zhongc
ms.author: zhongc
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: 7ef8715f23d21a23dc3f37b192314b922847d3e1
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65805909"
---
# <a name="choose-a-real-time-analytics-and-streaming-processing-technology-on-azure"></a>Wybierz analizy w czasie rzeczywistym i przesyłania strumieniowego technologii przetwarzania na platformie Azure

Brak dostępnych wiele usług analizy w czasie rzeczywistym i przesyłania strumieniowego przetwarzania na platformie Azure. Ten artykuł zawiera informacje, które należy zdecydować, która technologia jest najlepszym rozwiązaniem dla twojej aplikacji.

## <a name="when-to-use-azure-stream-analytics"></a>Kiedy należy używać usługi Azure Stream Analytics

Usługa Azure Stream Analytics jest usługą zalecane dla usługi stream analytics na platformie Azure. Jest przeznaczona dla szerokiej gamy scenariuszy, które obejmują, ale nie są ograniczone do:

* Pulpity nawigacyjne na potrzeby wizualizacji danych
* W czasie rzeczywistym [alerty](stream-analytics-set-up-alerts.md) z wzorców danych czasowych i przestrzennych lub anomalii
* Ekstrakcja, transformacja, ładowanie (ETL)
* [Wzorzec określania źródła zdarzeń](/azure/architecture/patterns/event-sourcing.md)
* [IoT Edge](stream-analytics-edge.md)

Dodawanie usługi Azure Stream Analytics zadania do aplikacji jest najszybszym sposobem na uzyskiwanie Stream analytics w górę i działających na platformie Azure, przy użyciu języka SQL już znasz. Usługa Azure Stream Analytics jest usługą zadań, dzięki czemu nie trzeba poświęcać czasu Zarządzanie klastrami i nie masz już martwić się o przestoje z umową SLA 99,9% na poziomie zadania. Rozliczanie odbywa się również na poziomie zadania, dzięki czemu niskie koszty uruchamiania (jedna jednostka przesyłania strumieniowego) ale skalowalne (maksymalnie 192 jednostki przesyłania strumieniowego). Jest znacznie bardziej opłacalne niż do uruchamiania i obsługi klastra, uruchom kilka zadań usługi Stream Analytics.

Usługa Azure Stream Analytics ma bogate poza pole. Można od razu korzystać z następujących funkcji bez żadnej dodatkowej konfiguracji:

* Wbudowane operatory czasowe, takich jak [okresowymi](stream-analytics-window-functions.md), danych czasowych sprzężeń i funkcji analitycznych danych czasowych.
* Natywne Azure [wejściowych](stream-analytics-add-inputs.md) i [dane wyjściowe](stream-analytics-define-outputs.md) kart
* Obsługa wolnych zmiana [dane referencyjne](stream-analytics-use-reference-data.md) (znany także jako tabele odnośników), tym łączącymi się z danymi geograficznymi danych referencyjnych dla wirtualnego grodzenia.
* Zintegrowane rozwiązania, takie jak [wykrywania anomalii](stream-analytics-machine-learning-anomaly-detection.md)
* Wiele okien czasu, w tym samym zapytaniu
* Możliwość tworzenia wielu operatory czasowe w dowolnej sekwencji.
* W obszarze 100 ms opóźnienie end-to-end z danych wejściowych otrzymywanych z usługi Event Hubs w danych wyjściowych docelowej w usłudze Event Hubs, w tym opóźnienia sieci z i do usługi Event Hubs przez dużą przepływnością

## <a name="when-to-use-other-technologies"></a>Kiedy należy używać innych technologii

### <a name="you-need-to-input-from-or-output-to-kafka"></a>Musisz danych wejściowych z lub dane wyjściowe do platformy Kafka

Usługa Azure Stream Analytics nie ma danych wejściowych platformy Apache Kafka lub danych wyjściowych karty. Jeśli masz kierowanych do zdarzenia lub potrzebujesz do wysłania do platformy Kafka i nie ma konieczności uruchamiania klastra Kafka, można nadal wysyła zdarzenia do usługi Event Hubs przy użyciu interfejsu API platformy Kafka centra zdarzeń, bez konieczności zmieniania nadawcy zdarzeń za pomocą usługi Stream Analytics. Jeśli musisz uruchomić klaster platformy Kafka, możesz użyć platformy Spark przesyłanie strumieniowe ze strukturą, który jest w pełni obsługiwany w [usługi Azure Databricks](../azure-databricks/index.yml), lub Storm na [Azure HDInsight](../hdinsight/storm/apache-storm-tutorial-get-started-linux.md).

### <a name="you-want-to-write-udfs-udas-and-custom-deserializers-in-a-language-other-than-javascript-or-c"></a>Chcesz napisać funkcje zdefiniowane przez użytkownika agregatów uda i deserializers niestandardowych w języku innym niż język JavaScript lubC#

Usługa Azure Stream Analytics obsługuje funkcje zdefiniowane przez użytkownika (UDF) lub agregatów zdefiniowanych przez użytkownika (UDA) w języku JavaScript dla zadania w chmurze i C# dla zadań usługi IoT Edge. C#obsługiwane są również deserializers zdefiniowanych przez użytkownika. Jeśli chcesz zaimplementować Deserializator UDF i agregacji w innych językach, takich jak Java lub Python, można użyć, przesyłanie strumieniowe ze strukturą platformy Spark. Można również uruchomić usługi Event Hubs **EventProcessorHost** na własnych maszynach wirtualnych wykonywać dowolne przetwarzanie przesyłania strumieniowego.

### <a name="your-solution-is-in-a-multi-cloud-or-on-premises-environment"></a>Rozwiązanie znajduje się w środowisku wielu chmur i lokalnie

Usługa Azure Stream Analytics jest technologia firmy Microsoft i jest dostępna tylko na platformie Azure. Jeśli potrzebujesz rozwiązania do jako przenośne w ramach chmury lub środowisku lokalnym, należy wziąć pod uwagę technologii typu open source, takich jak Spark Structured Streaming i Storm.

## <a name="next-steps"></a>Kolejne kroki

* [Tworzenie zadania usługi Stream Analytics przy użyciu witryny Azure portal](stream-analytics-quick-create-portal.md)
* [Tworzenie zadania usługi Stream Analytics przy użyciu programu Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Tworzenie zadania usługi Stream Analytics przy użyciu programu Visual Studio](stream-analytics-quick-create-vs.md)
* [Tworzenie zadania usługi Stream Analytics przy użyciu programu Visual Studio Code](quick-create-vs-code.md)