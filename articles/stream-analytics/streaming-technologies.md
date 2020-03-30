---
title: Wybieranie rozwiązania do przetwarzania w czasie rzeczywistym i przesyłania strumieniowego na platformie Azure
description: Dowiedz się, jak wybrać odpowiednią technologię analizy i przetwarzania przesyłania strumieniowego w czasie rzeczywistym, aby utworzyć aplikację na platformie Azure.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: 2146b1bd782aba5d98729a2d37d956744e469ba1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75860252"
---
# <a name="choose-a-real-time-analytics-and-streaming-processing-technology-on-azure"></a>Wybierz technologię analizy i przetwarzania strumieniowego przesyłania strumieniowego w czasie rzeczywistym na platformie Azure

Istnieje kilka usług dostępnych do analizy w czasie rzeczywistym i przetwarzania przesyłania strumieniowego na platformie Azure. Ten artykuł zawiera informacje potrzebne do podjęcia decyzji, która technologia jest najlepiej dopasowana do aplikacji.

## <a name="when-to-use-azure-stream-analytics"></a>Kiedy korzystać z usługi Azure Stream Analytics

Usługa Azure Stream Analytics to zalecana usługa do analizy strumienia na platformie Azure. Jest przeznaczony dla szerokiej gamy scenariuszy, które obejmują, ale nie są ograniczone do:

* Pulpity nawigacyjne do wizualizacji danych
* [Alerty](stream-analytics-set-up-alerts.md) w czasie rzeczywistym z wzorców czasowych i przestrzennych lub anomalii
* Ekstrakcja, transformacja, ładowanie (ETL)
* [Wzorzec pozyskiwania zdarzeń](/azure/architecture/patterns/event-sourcing)
* [IoT Edge](stream-analytics-edge.md)

Dodawanie zadania usługi Azure Stream Analytics do aplikacji jest najszybszym sposobem uruchomienia analizy strumieniowej na platformie Azure przy użyciu języka SQL, który już znasz. Usługa Azure Stream Analytics to usługa pracy, więc nie musisz poświęcać czasu na zarządzanie klastrami i nie musisz się martwić o przestoje z 99,9% umowy SLA na poziomie zadania. Rozliczenia są również wykonywane na poziomie zadania, co sprawia, że koszty uruchamiania są niskie (jedna jednostka przesyłania strumieniowego), ale skalowalne (do 192 jednostek przesyłania strumieniowego). Uruchomienie kilku zadań usługi Stream Analytics jest o wiele bardziej opłacalne niż uruchamianie i utrzymywanie klastra.

Usługa Azure Stream Analytics ma bogate środowisko out-of-the-box. Możesz natychmiast skorzystać z następujących funkcji bez dodatkowej konfiguracji:

* Wbudowane operatory czasowe, takie jak [agregaty okienne,](stream-analytics-window-functions.md)sprzężenia czasowe i funkcje analityczne czasowe.
* Karty [danych wejściowych](stream-analytics-add-inputs.md) i [wyjściowych](stream-analytics-define-outputs.md) platformy Azure natywnych
* Obsługa powolnych zmian [danych referencyjnych](stream-analytics-use-reference-data.md) (nazywanych również tabelami odnośników), w tym łączenia się z geoprzestrzennymi danymi referencyjnymi dla geofencingu.
* Zintegrowane rozwiązania, takie jak [wykrywanie anomalii](stream-analytics-machine-learning-anomaly-detection.md)
* Wiele okien czasu w tej samej kwerendzie
* Możliwość komponowania wielu operatorów czasowych w dowolnych sekwencjach.
* W obszarze 100 ms opóźnienia end-to-end od wejścia docierającego do Centrów zdarzeń do lądowania wyjściowego w Centrach zdarzeń, w tym opóźnienia sieci z i do centrów zdarzeń, przy utrzymującej się wysokiej przepustowości

## <a name="when-to-use-other-technologies"></a>Kiedy korzystać z innych technologii

### <a name="you-want-to-write-udfs-udas-and-custom-deserializers-in-a-language-other-than-javascript-or-c"></a>Chcesz pisać pliki UDF, UDA i deserializatory niestandardowe w języku innym niż JavaScript lub C #

Usługa Azure Stream Analytics obsługuje funkcje zdefiniowane przez użytkownika (UDF) lub agregaty zdefiniowane przez użytkownika (UDA) w języku JavaScript dla zadań w chmurze i C# dla zadań usługi IoT Edge. Deserializatory zdefiniowane przez użytkownika języka C# są również obsługiwane. Jeśli chcesz zaimplementować deserializer, UDF lub UDA w innych językach, takich jak Java lub Python, można użyć Spark Structured Streaming. Można również uruchomić Event Hubs **EventProcessorHost** na własnych maszynach wirtualnych, aby wykonać dowolne przetwarzanie przesyłania strumieniowego.

### <a name="your-solution-is-in-a-multi-cloud-or-on-premises-environment"></a>Rozwiązanie znajduje się w środowisku wielochmurowym lub lokalnym

Usługa Azure Stream Analytics jest zastrzeżoną technologią firmy Microsoft i jest dostępna tylko na platformie Azure. Jeśli potrzebujesz, aby twoje rozwiązanie było przenośne w chmurach lub lokalnie, rozważ technologie typu open source, takie jak Spark Structured Streaming lub Storm.

## <a name="next-steps"></a>Następne kroki

* [Tworzenie zadania usługi Stream Analytics przy użyciu portalu Azure](stream-analytics-quick-create-portal.md)
* [Tworzenie zadania usługi Stream Analytics przy użyciu programu Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Tworzenie zadania usługi Stream Analytics przy użyciu programu Visual Studio](stream-analytics-quick-create-vs.md)
* [Tworzenie zadania usługi Stream Analytics przy użyciu kodu programu Visual Studio](quick-create-vs-code.md)
