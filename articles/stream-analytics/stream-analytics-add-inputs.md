---
title: Zrozumieć dane wejściowe dla usługi Azure Stream Analytics
description: W tym artykule opisano pojęcia danych wejściowych do zadania usługi Azure Stream Analytics, porównanie przesyłania strumieniowego dane wejściowe wejściowych danych referencyjnych.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/25/2018
ms.openlocfilehash: 926821e2ba9912ae0140f11c9fe9a2d504609a1e
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32186066"
---
# <a name="understand-inputs-for-azure-stream-analytics"></a>Zrozumieć dane wejściowe dla usługi Azure Stream Analytics

Zadania usługi analiza strumienia Azure nawiązać połączenia z co najmniej jeden danych wejściowych danych. Każdy wprowadzania definiuje połączenie z istniejącym źródłem danych. Analiza strumienia akceptuje przychodzące dane z kilku rodzajów źródeł zdarzeń, łącznie z magazynem usługi Event Hubs, Centrum IoT i obiektów Blob. Dane wejściowe odwołuje się nazwa w zapytaniu SQL przesyłania strumieniowego, który można zapisać dla każdego zadania. W zapytaniu można dołączyć wielu danych wejściowych blend danych lub do porównywania dane przesyłane strumieniowo z wyszukiwania odwoływanie się do danych i przekazać wyniki do danych wyjściowych. 

Analiza strumienia ma najwyższej jakości integracji z trzech rodzajów zasobów jako dane wejściowe:
- [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)
- [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) 
- [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/) 

Te zasoby wejściowy może na żywo w tej samej subskrypcji platformy Azure jako zadania usługi analiza strumienia lub z innej subskrypcji.

Można użyć [portalu Azure](stream-analytics-quick-create-portal.md#configure-input-to-the-job), [programu Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/New-AzureRmStreamAnalyticsInput), [.Net interfejsu API](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.inputsoperationsextensions), [interfejsu API REST](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-input), i [programu Visual Studio](stream-analytics-tools-for-visual-studio.md)do tworzenia, edytowania i przetestować dane wejściowe zadania usługi analiza strumienia.

## <a name="stream-and-reference-inputs"></a>Dokumentacja i strumienia danych wejściowych
Jak przekazania danych do źródła danych, ma używane przez zadanie usługi analiza strumienia i przetwarzane w czasie rzeczywistym. Dane wejściowe są podzielone na dwa typy: dane strumienia danych wejściowych i odwołują się dane wejściowe dane.

### <a name="data-stream-input"></a>Dane wejściowe strumienia danych
Strumień danych jest niepowiązany sekwencji zdarzeń w czasie. Zadania usługi analiza strumienia musi zawierać co najmniej jednego danych elementu wejściowego strumienia. Centra zdarzeń, Centrum IoT i magazynu obiektów Blob są obsługiwane jako źródeł dla wejścia strumienia danych. Centra zdarzeń są używane do zbierania strumieni zdarzeń z wielu urządzeń i usług. Strumienie te mogą obejmować źródła działań mediów społecznościowych, handlu standardowych informacji lub dane z czujników. Centra IoT są optymalizowane w celu zbierania danych z połączonych urządzeń w scenariuszach Internetu rzeczy (IoT).  Magazyn obiektów blob może służyć jako źródło danych wejściowych do wprowadzania danych zbiorczego jako strumienia, takich jak pliki dziennika.  

Aby uzyskać więcej informacji na temat strumienia danych wejściowych danych, zobacz [przesyłać strumieniowo dane jako dane wejściowe do usługi analiza strumienia](stream-analytics-define-inputs.md)

### <a name="reference-data-input"></a>Odwołanie do danych wejściowych
Analiza strumienia obsługuje również znane jako dane wejściowe *danych referencyjnych*. Dane referencyjne jest całkowicie statyczny lub zmienia się powoli. Zwykle służy do wykonywania korelacji i wyszukiwania. Na przykład można sprzęgnąć danych w danych wejściowych strumienia z danymi w danych referencyjnych, tak jak będzie wykonywać sprzężenia SQL do odszukania wartości statyczne. Magazyn obiektów Blob Azure jest obecnie obsługiwane tylko źródła danych wejściowych danych referencyjnych. Odwołanie do źródła danych typu blob są ograniczone do 100 MB rozmiar.

Aby uzyskać więcej informacji o danych wejściowych danych odwołania, zobacz [danych referencyjnych Using odnośników w analiza strumienia](stream-analytics-use-reference-data.md)

W tym artykule jest krokiem w [ścieżka szkoleniowa dotycząca usługi analiza strumienia](/documentation/learning-paths/stream-analytics/).

## <a name="next-steps"></a>Kolejne kroki
> [!div class="nextstepaction"]
> [Szybki Start: Tworzenie zadania usługi analiza strumienia za pomocą portalu Azure](stream-analytics-quick-create-portal.md)