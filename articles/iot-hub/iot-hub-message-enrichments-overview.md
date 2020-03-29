---
title: Omówienie wzbogacania komunikatów usługi Azure IoT Hub
description: W tym artykule przedstawiono wzbogacenia wiadomości, które dają Centrum IoT możliwość stemplowania wiadomości z dodatkowymi informacjami, zanim wiadomości są wysyłane do wyznaczonego punktu końcowego.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: robinsh
ms.openlocfilehash: c3dbd01faf61c164c88f09b0da03c07be4abd187
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75429120"
---
# <a name="message-enrichments-for-device-to-cloud-iot-hub-messages"></a>Wzbogacanie wiadomości dla komunikatów Usługi IoT Hub między urządzeniami a chmurą

*Wzbogacenia wiadomości* jest możliwość Usługi IoT Hub do *stemplowania* wiadomości z dodatkowych informacji przed wiadomości są wysyłane do wyznaczonego punktu końcowego. Jednym z powodów, aby użyć wzbogacenia wiadomości jest uwzględnienie danych, które mogą służyć do uproszczenia przetwarzania podrzędnego. Na przykład wzbogacanie komunikatów telemetrycznych urządzenia o podwójny tag urządzenia może zmniejszyć obciążenie klientów, aby interfejs API bliźniaczej reprezentacji urządzenia powodem wywołać te informacje.

![Przepływ wzbogacania wiadomości](./media/iot-hub-message-enrichments-overview/message-enrichments-flow.png)

Wzbogacenie wiadomości ma trzy kluczowe elementy:

* Nazwa lub klucz wzbogacenia

* Wartość

* Jeden lub więcej [punktów końcowych,](iot-hub-devguide-endpoints.md) dla których należy zastosować wzbogacenie.

**Klucz** jest ciągiem. Klucz może zawierać tylko znaki alfanumeryczne lub te`-`znaki`_`specjalne: łącznik`.`( ), znak podkreślenia ( ) i kropka ( ).

**Wartość** może być dowolną z następujących przykładów:

* Dowolny ciąg statyczny. Wartości dynamiczne, takie jak warunki, logika, operacje i funkcje nie są dozwolone. Na przykład jeśli opracujesz aplikację SaaS, która jest używana przez kilku klientów, można przypisać identyfikator do każdego klienta i udostępnić ten identyfikator w aplikacji. Po uruchomieniu aplikacji Usługa IoT Hub będzie stemplować komunikaty telemetryczne urządzenia za pomocą identyfikatora klienta, umożliwiając przetwarzanie wiadomości w różny sposób dla każdego klienta.

* Nazwa centrum IoT, które wysyła wiadomość. Ta wartość jest *$iothubname*.

* Informacje z bliźniaczej reprezentacji urządzenia, takie jak jego ścieżka. Przykładami mogą być *$twin.tags.field* i *$twin.tags.latitude*.

   > [!NOTE]
   > W tej chwili tylko $iothubname, $twin.tags, $twin.properties.desired i $twin.properties.reported są obsługiwane zmienne do wzbogacania wiadomości.

Wzbogacenia wiadomości są dodawane jako właściwości aplikacji do wiadomości wysyłanych do wybranych punktów końcowych.  

## <a name="applying-enrichments"></a>Stosowanie wzbogacenia

Wiadomości mogą pochodzić z dowolnego źródła danych obsługiwanego przez [routing komunikatów usługi IoT Hub,](iot-hub-devguide-messages-d2c.md)w tym następujące przykłady:

* telemetrii urządzenia, takich jak temperatura lub ciśnienie
* powiadomienia o podwójnej zmianie urządzenia - zmiany w bliźniaczej reprezentacji urządzenia
* zdarzeń cyklu życia urządzenia, takich jak podczas tworzenia lub usuwania urządzenia

Można dodać wzbogacenia do wiadomości, które idą do wbudowanego punktu końcowego usługi IoT Hub lub wiadomości, które są kierowane do niestandardowych punktów końcowych, takich jak magazyn obiektów Blob platformy Azure, kolejka usługi Service Bus lub temat usługi Service Bus.

Można dodać wzbogacenia do wiadomości, które są publikowane w uścisnaniu zdarzeń, wybierając punkt końcowy jako siatka zdarzeń. Tworzymy domyślną trasę w Uorie IoT Hub do telemetrii urządzenia, na podstawie subskrypcji usługi Event Grid. Ta pojedyncza trasa może obsłużyć wszystkie subskrypcje usługi Event Grid. Po utworzeniu subskrypcji siatki zdarzeń w danych telemetrycznych urządzenia można skonfigurować wzbogacenia dla punktu końcowego siatki zdarzeń. Aby uzyskać więcej informacji, zobacz [Centrum Iot i siatka zdarzeń](iot-hub-event-grid.md).

Wzbogacenia są stosowane na punkt końcowy. Jeśli określisz pięć wzbogacenia, które mają być stemplowane dla określonego punktu końcowego, wszystkie komunikaty przechodzące do tego punktu końcowego są stemplowane z tych samych pięciu wzbogacenia.

Wzbogacenia można konfigurować przy użyciu następujących metod:

| **Metoda** | **Polecenie** |
| ----- | -----| 
| Portal | [Portal Azure](https://portal.azure.com) | Zobacz [samouczek wzbogacania wiadomości](tutorial-message-enrichments.md) | 
| Interfejs wiersza polecenia platformy Azure   | [az iot centrum wzbogacenia wiadomości](https://docs.microsoft.com/cli/azure/iot/hub/message-enrichment?view=azure-cli-latest) |
| Azure PowerShell | [Add-AzIotHubMessageEnrichment](https://docs.microsoft.com/powershell/module/az.iothub/add-aziothubmessageenrichment?view=azps-2.8.0) |

Dodawanie wzbogacenia wiadomości nie dodaje opóźnienia do routingu wiadomości.

Aby wypróbować wzbogacenia wiadomości, zobacz [samouczek wzbogacania wiadomości](tutorial-message-enrichments.md)

## <a name="limitations"></a>Ograniczenia

* Możesz dodać maksymalnie 10 wzbogacenia na centrum IoT hub dla tych koncentratorów w warstwie standardowej lub podstawowej. W przypadku centrów IoT w warstwie bezpłatnej można dodać maksymalnie 2 wzbogacenia.

* W niektórych przypadkach, jeśli stosujesz wzbogacenie z wartością ustawioną na tag lub właściwość w bliźniaczej reprezentacji urządzenia, wartość zostanie ostemplowana jako wartość ciągu. Na przykład jeśli wartość wzbogacenia jest ustawiona na $twin.tags.field, wiadomości będą stemplowane ciągiem "$twin.tags.field", a nie wartością tego pola z bliźniaczej reprezentacji. Dzieje się tak w następujących przypadkach:

   * Centrum IoT hub znajduje się w warstwie podstawowej. Podstawowe koncentratory IoT warstwy nie obsługują bliźniaczych urządzeń.

   * Usługa IoT Hub znajduje się w warstwie standardowej, ale urządzenie wysyłające wiadomość nie ma bliźniaczej reprezentacji urządzenia.

   * Centrum IoT hub jest w warstwie standardowej, ale ścieżka bliźniaczej reprezentacji urządzenia używana dla wartości wzbogacenia nie istnieje. Na przykład jeśli wartość wzbogacenia jest ustawiona na $twin.tags.location, a bliźniacza reprezentacja urządzenia nie ma właściwości lokalizacji pod tagami, wiadomość jest stemplowana ciągiem "$twin.tags.location". 

* Aktualizacje bliźniaczej reprezentacji urządzenia może potrwać do pięciu minut, aby odzwierciedlić w odpowiedniej wartości wzbogacenia.

* Całkowity rozmiar wiadomości, łącznie z wzbogaceniami, nie może przekraczać 256 KB. Jeśli rozmiar wiadomości przekracza 256 KB, Centrum IoT upuści wiadomość. [Metryki usługi IoT Hub](iot-hub-metrics.md) można użyć do identyfikowania i debugowania błędów, gdy wiadomości są odrzucane. Na przykład można monitorować d2c.telemetry.egress.invalid.

* Wzbogacenia wiadomości nie mają zastosowania do zdarzeń cyfrowej zmiany bliźniaczej (część [publicznej wersji zapoznawczej IoT Plug and Play).](../iot-pnp/overview-iot-plug-and-play.md)

## <a name="pricing"></a>Cennik

Wzbogacenia wiadomości są dostępne bez dodatkowych opłat. Obecnie naliczane są opłaty podczas wysyłania wiadomości do centrum IoT Hub. Opłata jest naliczana tylko raz za tę wiadomość, nawet jeśli wiadomość przechodzi do wielu punktów końcowych.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z tymi artykułami, aby uzyskać więcej informacji na temat routingu wiadomości do Centrum IoT Hub:

* [Samouczek wzbogacania wiadomości](tutorial-message-enrichments.md)

* [Wysyłanie wiadomości z urządzenia do chmury za pomocą usługi IoT Hub do wysyłania wiadomości z urządzenia do chmury do różnych punktów końcowych](iot-hub-devguide-messages-d2c.md)

* [Samouczek: Routing usługi IoT Hub](tutorial-routing.md)
