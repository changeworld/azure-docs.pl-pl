---
title: Funkcje w wersji zapoznawczej usługi Azure Stream Analytics
description: W tym artykule wymieniono funkcje usługi Azure Stream Analytics, które są obecnie w wersji zapoznawczej.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 3/9/2020
ms.openlocfilehash: 3e99263f6bf472c256e1747b8567249bbd62a445
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969631"
---
# <a name="azure-stream-analytics-preview-features"></a>Funkcje w wersji zapoznawczej usługi Azure Stream Analytics

W tym artykule podsumowano wszystkie funkcje aktualnie w wersji zapoznawczej usługi Azure Stream Analytics. Korzystanie z funkcji w wersji zapoznawczej w środowisku produkcyjnym nie jest zalecane.

## <a name="public-previews"></a>Publiczne wersje zapoznawcze

Następujące funkcje znajdują się w publicznej wersji zapoznawczej. Można korzystać z tych funkcji już dziś, ale nie należy ich używać w środowisku produkcyjnym.

### <a name="online-scaling"></a>Skalowanie online

W przypadku skalowania online nie jest wymagane, aby zatrzymać pracę, jeśli trzeba zmienić alokacji SU. Można zwiększyć lub zmniejszyć pojemność SU uruchomionego zadania bez konieczności jej zatrzymywania. Opiera się to na obietnicy klienta, że od dawna potoki o znaczeniu krytycznym, które stream analytics oferuje obecnie. Aby uzyskać więcej informacji, zobacz [Konfigurowanie jednostek przesyłania strumieniowego usługi Azure Stream Analytics](stream-analytics-streaming-unit-consumption.md#configure-stream-analytics-streaming-units-sus).

### <a name="c-custom-de-serializers"></a>Niestandardowe de-serializatory c#
Deweloperzy mogą korzystać z możliwości usługi Azure Stream Analytics do przetwarzania danych w protobuf, XML lub dowolnym formacie niestandardowym. Można zaimplementować [niestandardowe de-serializatory](custom-deserializer-examples.md) w języku C#, który następnie może służyć do de-serializacji zdarzeń odebranych przez usługę Azure Stream Analytics.

### <a name="extensibility-with-c-custom-code"></a>Rozszerzalność za pomocą kodu niestandardowego języka C#

Deweloperzy tworzący moduły usługi Stream Analytics w chmurze lub w usłudze IoT Edge mogą zapisywać lub ponownie używać niestandardowych funkcji języka C# i wywoływać je bezpośrednio w kwerendzie za pomocą [funkcji zdefiniowanych przez użytkownika.](stream-analytics-edge-csharp-udf-methods.md)


### <a name="debug-query-steps-in-visual-studio"></a>Kroki kwerendy debugowania w programie Visual Studio

Można łatwo wyświetlić podgląd wiersza pośredniego ustawionego na diagramie danych podczas wykonywania lokalnych testów w narzędziach usługi Azure Stream Analytics dla programu Visual Studio. 

### <a name="local-testing-with-live-data-in-visual-studio-code"></a>Lokalne testowanie danych na żywo w programie Visual Studio Code

Można przetestować zapytania względem danych na żywo na komputerze lokalnym przed przesłaniem zadania do platformy Azure. Każda iteracja testowa trwa średnio mniej niż dwie do trzech sekund, co powoduje bardzo wydajny proces rozwoju.

### <a name="visual-studio-code-for-azure-stream-analytics"></a>Kod programu Visual Studio dla usługi Azure Stream Analytics

Zadania usługi Azure Stream Analytics można tworzyć w programie Visual Studio Code. Zobacz nasz [samouczek rozpoczynanie pracy z kodem VS](https://docs.microsoft.com/azure/stream-analytics/quick-create-vs-code).


### <a name="integration-with-azure-machine-learning"></a>Integracja z usługą Azure Machine Learning

Zadania usługi Stream Analytics można skalować za pomocą funkcji uczenia maszynowego (ML). Aby dowiedzieć się więcej o tym, jak używać funkcji uczenia maszynowego w zadaniu usługi Stream Analytics, odwiedź stronę [Skalowanie zadania usługi Stream Analytics za pomocą funkcji usługi Azure Machine Learning.](stream-analytics-scale-with-machine-learning-functions.md) Zapoznaj się z rzeczywistym scenariuszem z [wykonywaniem analizy tonacji przy użyciu usługi Azure Stream Analytics i usługi Azure Machine Learning.](stream-analytics-machine-learning-integration-tutorial.md)


### <a name="live-data-testing-in-visual-studio"></a>Testowanie danych na żywo w programie Visual Studio

Narzędzia programu Visual Studio dla usługi Azure Stream Analytics rozszerzają funkcję testowania lokalnego, która umożliwia testowanie zapytań dotyczących strumieni zdarzeń na żywo ze źródeł w chmurze, takich jak Centrum zdarzeń lub centrum IoT. Dowiedz się, jak [testować dane na żywo lokalnie przy użyciu narzędzi usługi Azure Stream Analytics dla programu Visual Studio.](stream-analytics-live-data-local-testing.md)


### <a name="net-user-defined-functions-on-iot-edge"></a>Funkcje zdefiniowane przez użytkownika platformy .NET w u danych IoT Edge

Za pomocą standardowych funkcji zdefiniowanych przez użytkownika platformy .NET można uruchomić kod .NET Standard jako część potoku przesyłania strumieniowego. Można utworzyć proste klasy Języka C# lub zaimportować pełny projekt i biblioteki. Pełne środowisko tworzenia i debugowania jest obsługiwane w programie Visual Studio. Aby uzyskać więcej informacji, odwiedź stronę [Opracowywanie standardowych funkcji zdefiniowanych przez użytkownika w usłudze Azure Stream Analytics.](stream-analytics-edge-csharp-udf-methods.md)

## <a name="other-previews"></a>Inne podglądy

Następujące funkcje są również dostępne w wersji zapoznawczej na życzenie.

### <a name="real-time-high-performance-scoring-with-custom-ml-models-managed-by-azure-machine-learning"></a>Ocena wysokiej wydajności w czasie rzeczywistym dzięki niestandardowym modelom uczenia maszynowego zarządzanym przez usługę Azure Machine Learning

Usługa Azure Stream Analytics obsługuje wysokowydajne ocenianie w czasie rzeczywistym przy użyciu niestandardowych wstępnie przeszkolonych modeli usługi Machine Learning zarządzanych przez usługę Azure Machine Learning i hostowanych w usłudze Azure Kubernetes Service (AKS) lub instancjach kontenerów platformy Azure (ACI) przy użyciu przepływu pracy który nie wymaga pisania kodu. [Zarejestruj się w](https://aka.ms/asapreview1) wersji zapoznawczej

### <a name="support-for-azure-stack"></a>Obsługa usługi Azure Stack
Ta funkcja jest włączona w środowisku uruchomieniowym usługi Azure IoT Edge, wykorzystuje niestandardowe funkcje usługi Azure Stack, takie jak natywna obsługa lokalnych danych wejściowych i wyjść uruchomionych w usłudze Azure Stack (na przykład usługi Event Hubs, Centrum IoT, magazyn obiektów Blob). Ta nowa integracja umożliwia tworzenie architektur hybrydowych, które można analizować dane w pobliżu miejsca, w którym są generowane, obniżenie opóźnienia i maksymalizacji szczegółowych informacji.
Ta funkcja umożliwia tworzenie architektur hybrydowych, które można analizować dane w pobliżu miejsca, w którym są generowane, obniżenie opóźnienia i maksymalizacji szczegółowych informacji. Musisz [zarejestrować się w](https://aka.ms/asapreview1) tej wersji zapoznawczej.
