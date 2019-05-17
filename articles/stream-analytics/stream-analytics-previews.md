---
title: Funkcje w wersji zapoznawczej usługa Azure Stream Analytics
description: W tym artykule wymieniono funkcje usługi Azure Stream Analytics, które są obecnie dostępne w wersji zapoznawczej.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/07/2019
ms.openlocfilehash: f3838bf6b9f7daa24c0cdb5b0c5a08d41d164530
ms.sourcegitcommit: 179918af242d52664d3274370c6fdaec6c783eb6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/13/2019
ms.locfileid: "65561134"
---
# <a name="azure-stream-analytics-preview-features"></a>Funkcje w wersji zapoznawczej usługa Azure Stream Analytics

Ten artykuł zawiera podsumowanie wszystkich funkcji, obecnie w wersji zapoznawczej usługi Azure Stream Analytics. Korzystanie z funkcji w wersji zapoznawczej w środowisku produkcyjnym nie jest zalecane.

## <a name="public-previews"></a>Publiczne wersje zapoznawcze

Następujące funkcje są w publicznej wersji zapoznawczej. Można korzystać z tych funkcji już dziś, ale nie są używane w środowisku produkcyjnym.

### <a name="visual-studio-code-for-azure-stream-analytics-released-may-2019"></a>Visual Studio Code dla usługi Azure Stream Analytics (wydane maja 2019 r)

Zadania usługi Azure Stream Analytics mogą być tworzone w programie Visual Studio Code. Zobacz nasze [programu VS Code Wprowadzenie — samouczek](https://docs.microsoft.com/azure/stream-analytics/quick-create-vs-code).

### <a name="anomaly-detection"></a>Wykrywanie anomalii

Usługa Azure Stream Analytics wprowadzono nowe modele uczenia maszynowego dzięki obsłudze *kolekcji* i *spadki* wykrywania oprócz dwukierunkowej, będzie wolno działać pozytywne i bardzo wolno ujemna trendów, wykrywania. Aby uzyskać więcej informacji, odwiedź stronę [wykrywania anomalii w usłudze Azure Stream Analytics](stream-analytics-machine-learning-anomaly-detection.md).

### <a name="sql-database-reference-data"></a>Dane referencyjne bazy danych SQL

Usługa Azure Stream Analytics obsługuje usługi Azure SQL Database jako źródło danych wejściowych danych referencyjnych. Bazy danych SQL można użyć jako danych referencyjnych dla zadania usługi Stream Analytics w witrynie Azure portal i w programie Visual Studio za pomocą narzędzi usługi Stream Analytics. Aby uzyskać więcej informacji odwiedź stronę, [Użyj odwołanie do danych z bazy danych SQL dla zadania usługi Azure Stream Analytics](sql-reference-data.md).

### <a name="integration-with-azure-machine-learning"></a>Integracja z usługą Azure Machine Learning

Możesz skalować zadania usługi Stream Analytics, za pomocą usługi Machine Learning () ml. Aby dowiedzieć się więcej na temat wykorzystania funkcji uczenia Maszynowego w ramach zadania usługi Stream Analytics, odwiedź stronę [skalować zadania usługi Stream Analytics przy użyciu funkcji usługi Azure Machine Learning](stream-analytics-scale-with-machine-learning-functions.md). Zapoznaj się z rzeczywistych scenariuszy za pomocą [przeprowadzania analizy tonacji za pomocą usługi Azure Stream Analytics i Azure Machine Learning](stream-analytics-machine-learning-integration-tutorial.md).

### <a name="javascript-user-defined-aggregate"></a>Agregacja zdefiniowana przez użytkownika języka JavaScript

Usługa Azure Stream Analytics obsługuje agregacje zdefiniowane przez użytkownika (UDA) napisanych w języku JavaScript, które umożliwiają implementowanie złożoną stanowych logikę biznesową. Dowiedz się, jak używać uda z [agregacje zdefiniowane przez użytkownika usługi Azure Stream Analytics JavaScript](stream-analytics-javascript-user-defined-aggregates.md) dokumentacji. 

### <a name="live-data-testing-in-visual-studio"></a>Na żywo danych testowania w programie Visual Studio

Visual Studio tools dla usługi Azure Stream Analytics pozwala zwiększyć lokalnego testowania funkcji, dająca możliwość testowania zapytań dla strumieni zdarzeń na żywo ze źródeł w chmurze takich jak Centrum zdarzeń lub IoT hub. Dowiedz się, jak [danych na żywo lokalnie przy użyciu usługi Azure Stream Analytics tools for Visual Studio Test](stream-analytics-live-data-local-testing.md).

### <a name="net-user-defined-functions-on-iot-edge"></a>Funkcje zdefiniowane przez użytkownika platformy .NET na brzegowych urządzeniach IoT

Za pomocą platformy .NET standard funkcje zdefiniowane przez użytkownika może uruchamiać kod .NET Standard, jako część potoku transmisji strumieniowych. Można utworzyć proste klas języka C# lub zaimportować pełny projektu i bibliotek. Pełne tworzenia i obsługi debugowania jest obsługiwana w programie Visual Studio. Aby uzyskać więcej informacji, odwiedź stronę [opracowywanie .NET Standard funkcje zdefiniowane przez użytkownika dla zadań usługi Azure Stream Analytics Edge](stream-analytics-edge-csharp-udf-methods.md).

## <a name="other-previews"></a>Inne wersje zapoznawcze

Następujące funkcje są również dostępne w wersji zapoznawczej.

### <a name="c-custom-deserializer-for-azure-stream-analytics-on-iot-edge-and-cloud-announced-may-2019"></a>C#Deserializator niestandardowe dla Azure Stream Analytics w usłudze IoT Edge i w chmurze (Announced maja 2019 r)

Deweloperzy mogą implementować niestandardowe deserializers w C# deserializować zdarzeń otrzymanych przez usługę Azure Stream Analytics. Przykłady formatów, które może być zdeserializowany Parquet, Protobuf, XML lub w dowolnym formacie binarnym. Zarejestruj się w tej wersji zapoznawczej [tutaj](https://aka.ms/asapreview1).

### <a name="parquet-output-announced-may-2019"></a>Dane wyjściowe parquet (ogłosiła maja 2019 r)
Parquet jest tabelarycznego, umożliwiające wydajne przetwarzanie danych big Data. Za wyprowadzanie danych w formacie Parquet w usłudze data lake, mogą korzystać z zalet usługi Azure Stream Analytics na dużą skalę zasilania, przesyłanie strumieniowe ETL i uruchamianie przetwarzania wsadowego, szkolenie algorytmów uczenia maszynowego lub uruchamianie interakcyjnych zapytań na podstawie danych historycznych. Zarejestruj się w tej wersji zapoznawczej [tutaj](https://aka.ms/asapreview1).

### <a name="one-click-integration-with-event-hubs-announced-may-2019"></a>Jednym kliknięciem integracji z usługą Event Hubs (Announced maja 2019 r) 
Dzięki tej integracji teraz można wizualizować dane przychodzące i zacznij pisać zapytania usługi Stream Analytics, za pomocą jednego kliknięcia w portalu Centrum zdarzeń. Gdy zapytanie jest gotowy, można wypróbowujemy go za pomocą kilku kliknięć i rozpocząć uzyskiwanie szczegółowych informacji w czasie rzeczywistym. Zmniejsza to znacznie czas i pieniądze, umożliwiające tworzenie rozwiązań analizy w czasie rzeczywistym. Zarejestruj się w tej wersji zapoznawczej [tutaj](https://aka.ms/asapreview1).

### <a name="support-for-azure-stack-announced-may-2019"></a>Obsługa usługi Azure Stack (ogłosiła maja 2019 r)
Ta funkcja jest włączona w środowisku uruchomieniowym usługi Azure IoT Edge, korzysta z funkcji niestandardowych usługi Azure Stack, takie jak Natywna obsługa lokalnych danych wejściowych i generuje uruchomiona w usłudze Azure Stack (na przykład usługi Event Hubs, IoT Hub, magazynu obiektów Blob). Ta nowa integracja umożliwia tworzenie architektur hybrydowych, które można analizować swoje dane blisko gdzie jest generowany, zmniejszenie opóźnień i maksymalizując szczegółowych informacji.
Zarejestruj się w tej wersji zapoznawczej [tutaj](https://aka.ms/asapreview1).

