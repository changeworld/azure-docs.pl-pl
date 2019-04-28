---
title: Funkcje w wersji zapoznawczej usługa Azure Stream Analytics
description: W tym artykule wymieniono funkcje usługi Azure Stream Analytics, które są obecnie dostępne w wersji zapoznawczej.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/05/2019
ms.openlocfilehash: 08430f3eee858cdb6c9a7fbdfe11bd4c00ef148d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61485679"
---
# <a name="azure-stream-analytics-preview-features"></a>Funkcje w wersji zapoznawczej usługa Azure Stream Analytics

Ten artykuł zawiera podsumowanie wszystkich funkcji, obecnie w wersji zapoznawczej usługi Azure Stream Analytics. Korzystanie z funkcji w wersji zapoznawczej w środowisku produkcyjnym nie jest zalecane.

## <a name="public-previews"></a>Publiczne wersje zapoznawcze

Następujące funkcje są w publicznej wersji zapoznawczej. Można korzystać z tych funkcji już dziś, ale nie są używane w środowisku produkcyjnym.

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

## <a name="private-previews"></a>Prywatnych wersji zapoznawczych

Następujące funkcje są w prywatnej wersji zapoznawczej.

### <a name="c-custom-deserializer-for-azure-stream-analytics-on-iot-edge"></a>C# niestandardowe Deserializator dla usługi Azure Stream Analytics w usłudze IoT Edge

Deweloperzy teraz wdrożyć deserializers niestandardowych w języku C# do deserializacji zdarzeń otrzymanych przez usługę Azure Stream Analytics. Przykłady formatów, które może być zdeserializowany Parquet, Protobuf, XML lub w dowolnym formacie binarnym.

### <a name="visual-studio-code-for-azure-stream-analytics"></a>Program Visual Studio Code dla usługi Azure Stream Analytics

Zadania usługi Azure Stream Analytics mogą być tworzone w programie Visual Studio Code. Aby uzyskać dostęp do narzędzi prywatnej wersji zapoznawczej funkcji, skontaktuj się z pomocą do *ASAToolsfeedback\@microsoft.com*.

## <a name="next-steps"></a>Kolejne kroki

* [Osiem nowych funkcji w usłudze Azure Stream Analytics](https://azure.microsoft.com/blog/eight-new-features-in-azure-stream-analytics/)

* [Cztery nowe najbardziej pożądanych funkcji dostępnych w usłudze Azure Stream Analytics](https://azure.microsoft.com/blog/4-new-features-now-available-in-azure-stream-analytics/)
