---
title: Funkcje w wersji zapoznawczej usługa Azure Stream Analytics
description: W tym artykule wymieniono funkcje usługi Azure Stream Analytics, które są obecnie dostępne w wersji zapoznawczej.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/05/2018
ms.openlocfilehash: 124e936b619e3078c71094156bf91a437a28492b
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2018
ms.locfileid: "48862774"
---
# <a name="azure-stream-analytics-preview-features"></a>Funkcje w wersji zapoznawczej usługa Azure Stream Analytics

Ten artykuł zawiera podsumowanie wszystkich funkcji, obecnie w wersji zapoznawczej usługi Azure Stream Analytics. Korzystanie z funkcji w wersji zapoznawczej w środowisku produkcyjnym nie jest zalecane.

## <a name="public-previews"></a>Publiczne wersje zapoznawcze

Następujące funkcje są w publicznej wersji zapoznawczej. Można korzystać z tych funkcji już dziś, ale nie są używane w środowisku produkcyjnym.

### <a name="azure-stream-analytics-on-iot-edge"></a>Azure Stream Analytics na urządzeniach brzegowych IoT Edge

Azure Stream Analytics w usłudze IoT Edge umożliwia deweloperom wdrażanie analizy prawie w czasie rzeczywistym na urządzeniach IoT Edge. Aby uzyskać więcej informacji, zobacz [Azure Stream Analytics w usłudze IoT Edge](stream-analytics-edge.md) dokumentacji.

### <a name="integration-with-azure-machine-learning"></a>Integracja z usługą Azure Machine Learning

Możesz skalować zadania usługi Stream Analytics, za pomocą usługi Machine Learning () ml. Aby dowiedzieć się więcej na temat wykorzystania funkcji uczenia Maszynowego w ramach zadania usługi Stream Analytics, odwiedź stronę [skalować zadania usługi Stream Analytics przy użyciu funkcji usługi Azure Machine Learning](stream-analytics-scale-with-machine-learning-functions.md). Zapoznaj się z rzeczywistych scenariuszy za pomocą [przeprowadzania analizy tonacji za pomocą usługi Azure Stream Analytics i Azure Machine Learning](stream-analytics-machine-learning-integration-tutorial.md).

### <a name="session-windows"></a>Sesji systemu windows

Stream Analytics zapewnia natywną obsługę funkcji obsługi okien, dzięki czemu deweloperzy mogą zadania przetwarzania strumieniowego złożonych Autor przy minimalnym nakładzie pracy. [Sesji systemu windows](https://msdn.microsoft.com/azure/stream-analytics/reference/session-window-azure-stream-analytics) grupie zdarzenia odbierane w czasie podobne odfiltrowanie okresach czasu w przypadku, gdy nie ma żadnych danych. Aby dowiedzieć się więcej na temat funkcji obsługi okien, odwiedź stronę [wprowadzenie do funkcji obsługi okien usługi Stream Analytics](stream-analytics-window-functions.md).

### <a name="blob-output-partitioning-by-custom-time"></a>Dane wyjściowe obiektu blob partycjonowanie przez niestandardowy czas

Usługa Azure Stream Analytics mogą przesyłać dane wyjściowe do magazynu obiektów Blob, w oparciu o atrybuty niestandardowe czasu. Aby uzyskać więcej informacji, odwiedź stronę [wzorców ścieżki daty/godziny niestandardowego usługi Azure Stream Analytics obiektu blob magazynu danych wyjściowych](stream-analytics-custom-path-patterns-blob-storage-output.md).

### <a name="javascript-user-defined-aggregate"></a>Agregacja zdefiniowana przez użytkownika języka JavaScript

Usługa Azure Stream Analytics obsługuje agregacje zdefiniowane przez użytkownika (UDA) napisanych w języku JavaScript, które umożliwiają implementowanie złożoną stanowych logikę biznesową. Dowiedz się, jak używać uda z [agregacje zdefiniowane przez użytkownika usługi Azure Stream Analytics JavaScript](stream-analytics-javascript-user-defined-aggregates.md) dokumentacji. 

### <a name="live-data-testing-in-visual-studio"></a>Na żywo danych testowania w programie Visual Studio

Visual Studio tools dla usługi Azure Stream Analytics pozwala zwiększyć lokalnego testowania funkcji, dająca możliwość testowania zapytań dla strumieni zdarzeń na żywo ze źródeł w chmurze takich jak Centrum zdarzeń lub IoT hub. Dowiedz się, jak [danych na żywo lokalnie przy użyciu usługi Azure Stream Analytics tools for Visual Studio Test](stream-analytics-live-data-local-testing.md).

### <a name="net-user-defined-functions-on-iot-edge"></a>Funkcje zdefiniowane przez użytkownika platformy .NET na brzegowych urządzeniach IoT

Za pomocą platformy .NET standard funkcje zdefiniowane przez użytkownika może uruchamiać kod .NET Standard, jako część potoku transmisji strumieniowych. Można utworzyć proste klas języka C# lub zaimportować pełny projektu i bibliotek. Pełne tworzenia i obsługi debugowania jest obsługiwana w programie Visual Studio. Aby uzyskać więcej informacji, odwiedź stronę [opracowywanie .NET Standard funkcje zdefiniowane przez użytkownika dla zadań usługi Azure Stream Analytics Edge](stream-analytics-edge-csharp-udf-methods.md).

## <a name="private-previews"></a>Prywatnych wersji zapoznawczych

Następujące funkcje są w prywatnej wersji zapoznawczej. Aby uzyskać dostęp do tych wersji zapoznawczych, odwiedź stronę prywatnej wersji zapoznawczej usługi Azure Stream Analytics [Zarejestruj](https://aka.ms/ASApreview1) strony.

### <a name="anomaly-detection"></a>Wykrywanie anomalii

Usługa Azure Stream Analytics wprowadzono nowe modele uczenia maszynowego dzięki obsłudze *kolekcji* i *spadki* wykrywania oprócz dwukierunkowej, będzie wolno działać pozytywne i bardzo wolno ujemna trendów, wykrywania.

### <a name="c-custom-deserializer-for-azure-stream-analytics-on-iot-edge"></a>C# niestandardowe Deserializator dla usługi Azure Stream Analytics w usłudze IoT Edge

Deweloperzy teraz wdrożyć deserializers niestandardowych w języku C# do deserializacji zdarzeń otrzymanych przez usługę Azure Stream Analytics. Przykłady formatów, które może być zdeserializowany Parquet, Protobuf, XML lub w dowolnym formacie binarnym.

### <a name="blob-output-partitioning-by-custom-attribute"></a>Dane wyjściowe obiektu blob partycjonowanie przez atrybut niestandardowy

Teraz jest możliwa do partycjonowania danych wyjściowych usługi Azure Stream Analytics do magazynu obiektów Blob opartego na dowolną kolumnę w zapytaniu.

### <a name="managed-service-identity-msi-authentication-to-azure-data-lake-storage"></a>Zarządzane uwierzytelnianie usługi Identity (MSI) do usługi Azure Data Lake Storage

Teraz możesz operacjonalizować potoków w czasie rzeczywistym przy użyciu uwierzytelniania opartego na MSI, podczas zapisywania do usługi Azure Data Lake Storage Gen1 umożliwia programowe tworzenie zadań. Aby uzyskać więcej informacji, odwiedź stronę [Użyj zarządzanych tożsamości do uwierzytelniania usługi Azure Stream Analytics zadania, aby dane wyjściowe usługi Azure Data Lake Storage Gen1](stream-analytics-managed-identities-adls.md).

## <a name="next-steps"></a>Kolejne kroki

* [Osiem nowych funkcji w usłudze Azure Stream Analytics](https://azure.microsoft.com/blog/eight-new-features-in-azure-stream-analytics/)

* [4 nowe najbardziej pożądanych funkcji dostępnych w usłudze Azure Stream Analytics](https://azure.microsoft.com/blog/4-new-features-now-available-in-azure-stream-analytics/)
