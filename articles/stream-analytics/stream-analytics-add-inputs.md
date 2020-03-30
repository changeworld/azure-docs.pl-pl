---
title: Opis danych wejściowych dla usługi Azure Stream Analytics
description: W tym artykule opisano pojęcie danych wejściowych w zadaniu usługi Azure Stream Analytics, porównując dane wejściowe przesyłania strumieniowego z danymi referencyjnymi.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: 6b841d6b47e009c3b01d9925e11d352c00ed5c19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426429"
---
# <a name="understand-inputs-for-azure-stream-analytics"></a>Opis danych wejściowych dla usługi Azure Stream Analytics

Zadania usługi Azure Stream Analytics łączą się z co najmniej jednym wejściem danych. Każde wejście definiuje połączenie z istniejącym źródłem danych. Usługa Stream Analytics akceptuje przychodzące dane z kilku rodzajów źródeł zdarzeń, w tym z centrów zdarzeń, centrum IoT hub i magazynu obiektów blob. Dane wejściowe są odwoływane przez nazwę w kwerendzie SQL przesyłania strumieniowego, które piszesz dla każdego zadania. W kwerendzie można dołączyć wiele danych wejściowych do mieszania danych lub porównywania danych przesyłania strumieniowego z wyszukiwaniem danych referencyjnych i przekazywania wyników do danych wyjściowych. 

Usługa Stream Analytics ma pierwszorzędną integrację z trzema rodzajami zasobów jako dane wejściowe:
- [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)
- [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) 
- [Magazyn obiektów Blob platformy Azure](https://azure.microsoft.com/services/storage/blobs/) 

Te zasoby wejściowe mogą być dostępne w tej samej subskrypcji platformy Azure, co zadanie usługi Stream Analytics lub z innej subskrypcji.

Za pomocą [witryny Azure portal](stream-analytics-quick-create-portal.md#configure-job-input), [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.streamanalytics/New-azStreamAnalyticsInput), [.NET API](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.inputsoperationsextensions), REST [API](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-input)i [Visual Studio](stream-analytics-tools-for-visual-studio-install.md) można tworzyć, edytować i testować dane wejściowe zadań usługi Stream Analytics.

## <a name="stream-and-reference-inputs"></a>Dane wejściowe strumienia i odniesień
Ponieważ dane są wypychane do źródła danych, są używane przez zadanie usługi Stream Analytics i przetwarzane w czasie rzeczywistym. Istnieją dwa typy danych wejściowych: dane strumienia danych i dane referencyjne.

### <a name="data-stream-input"></a>Wprowadzanie strumienia danych
Strumień danych jest nieograniczoną sekwencją zdarzeń w czasie. Zadania usługi Stream Analytics musza zawierać co najmniej jedne dane wejściowe strumienia danych. Jako źródła danych wejściowych strumienia danych są obsługiwane usługi Event Hubs, IoT Hub i Blob Storage. Centra zdarzeń są używane do zbierania strumieni zdarzeń z wielu urządzeń i usług. Strumienie te mogą obejmować kanały aktywności w mediach społecznościowych, informacje o handlu akcjami lub dane z czujników. Centra IoT są zoptymalizowane do zbierania danych z podłączonych urządzeń w scenariuszach Internetu rzeczy (IoT).  Magazyn obiektów blob może służyć jako źródło wejściowe do pozyskiwania danych zbiorczych jako strumienia, takich jak pliki dziennika.  

Aby uzyskać więcej informacji na temat przesyłania strumieniowego danych wejściowych, zobacz [Przesyłanie danych jako dane wejściowe do usługi Stream Analytics](stream-analytics-define-inputs.md)

### <a name="reference-data-input"></a>Wprowadzanie danych referencyjnych
Usługa Stream Analytics obsługuje również dane wejściowe znane jako *dane referencyjne.* Dane referencyjne są całkowicie statyczne lub zmieniają się powoli. Zazwyczaj jest używany do wykonywania korelacji i odnośne. Na przykład można dołączyć dane w danych wejściowych strumienia danych do danych w danych referencyjnych, podobnie jak można wykonać sprzężenie SQL do wyszukiwania wartości statycznych. Usługa Azure Blob storage i azure SQL Database są obecnie obsługiwane jako źródła danych wejściowych dla danych referencyjnych. Obiekty BLOB źródła danych referencyjnych mają limit do 300 MB w rozmiarze, w zależności od złożoności zapytania i przydzielonych jednostek przesyłania strumieniowego (zobacz sekcji [Ograniczenie rozmiaru](stream-analytics-use-reference-data.md#size-limitation) dokumentacji danych referencyjnych, aby uzyskać więcej szczegółów).

Aby uzyskać więcej informacji na temat danych wejściowych, zobacz [Używanie danych referencyjnych do wyszukiwania w usłudze Stream Analytics](stream-analytics-use-reference-data.md)

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Szybki start: tworzenie zadania usługi Stream Analytics przy użyciu witryny Azure Portal](stream-analytics-quick-create-portal.md)
