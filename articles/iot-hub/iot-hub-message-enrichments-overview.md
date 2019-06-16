---
title: Omówienie usługi Azure IoT Hub komunikatów wzbogacenia
description: Omówienie wzbogacenia komunikatu dla komunikatów usługi Azure IoT Hub
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: robinsh
ms.openlocfilehash: 13e35ab93fc37541548785c6355489eaf3a3efc2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66754547"
---
# <a name="message-enrichments-for-device-to-cloud-iot-hub-messages-preview"></a>Wzbogacenia komunikatu dla komunikatów przesyłanych z chmury do urządzenia usługi IoT Hub (wersja zapoznawcza)

*Komunikat wzbogaceń* to zdolność usług IoT Hub *sygnatury* wiadomości z dodatkowymi informacjami przed wysłaniem wiadomości do wyznaczonej punktu końcowego. Jednym z powodów używania wzbogacenia wiadomości jest zawierają dane, do którego można uproszczenie przetwarzania transmisji dla klientów. Na przykład wzbogaca komunikaty telemetryczne urządzenia przy użyciu tagu bliźniaczej reprezentacji urządzenia można zmniejszyć obciążenie klientom na podejmowanie bliźniaczej reprezentacji urządzenia, które wywołań interfejsu API, aby uzyskać te informacje.

![Przepływu wzbogaceń wiadomości](./media/iot-hub-message-enrichments-overview/message-enrichments-flow.png)

Wzbogacania komunikatów ma trzy kluczowe elementy:

* Nazwa wzbogacania lub klucz

* Wartość

* Co najmniej jeden [punktów końcowych](iot-hub-devguide-endpoints.md) dla której wzbogacanie powinny być stosowane.

Klucz może być dowolnym ciągiem.

Wartość może być dowolny z poniższych przykładów:

* Dowolny ciąg statyczny. Wartości dynamiczne, takich jak warunki, Logic Apps, operacje i funkcje nie są dozwolone. Na przykład w przypadku tworzenia aplikacji SaaS, która jest używana przez wielu klientów, można przypisać identyfikator do każdego klienta i udostępnić ten identyfikator w aplikacji. Po uruchomieniu aplikacji, Centrum IoT Hub będzie sygnatury urządzenia komunikaty telemetryczne z identyfikatorem klienta, dzięki czemu można przetwarzać komunikaty inaczej dla każdego klienta.

* Informacje z bliźniaczej reprezentacji urządzenia, takie jak jego ścieżki. Przykłady będą *$twin.tags.field* i *$twin.tags.latitude*.

* Nazwa centrum IoT hub do wysyłania wiadomości. Ta wartość jest *$iothubname*.

## <a name="applying-enrichments"></a>Stosowanie wzbogacenia

Komunikaty mogą pochodzić z dowolnego źródła danych obsługiwane przez [routing komunikatów usługi IoT Hub](iot-hub-devguide-messages-d2c.md), w poniższych przykładach w tym:

* dane telemetryczne z urządzeń, takich jak temperatury i ciśnienia
* powiadomienia o zmianie bliźniacza reprezentacja urządzenia — zmiany w bliźniaczej reprezentacji urządzenia
* zdarzenia cyklu życia urządzenia, na przykład po utworzeniu lub usunięciu urządzenia

Możesz dodać wzbogacenia wiadomości, które mają wbudowany punkt końcowy usługi IoT Hub lub wiadomości, które są rozsyłane do niestandardowych punktów końcowych, takich jak magazyn Azure Blob, kolejki usługi Service Bus lub tematu usługi Service Bus.

Wzbogacenia można również dodać do wiadomości, które są publikowane do usługi Event Grid, wybierając opcję punkt końcowy jako usługi Event Grid. Aby uzyskać więcej informacji, zobacz [usługi Iot Hub i Event Grid](iot-hub-event-grid.md).

Wzbogacenia są stosowane na punkt końcowy. Jeśli określisz pięć wzbogacenia do opatrzone dla określonego punktu końcowego, wszystkie komunikaty, przechodząc do tego punktu końcowego są oznaczone zgodnie z tych samych pięciu wzbogacenia.

Aby zobaczyć, jak możesz wypróbować wzbogacenia wiadomości, zobacz [samouczek wzbogacenia wiadomości](tutorial-message-enrichments.md)

## <a name="limitations"></a>Ograniczenia

* Możesz dodać maksymalnie 10 wzbogacenia dla usługi IoT Hub dla tych usług w warstwie standard lub basic. W przypadku centra IoT Hub w warstwie bezpłatna można dodać maksymalnie 2 wzbogacenia.

* W niektórych przypadkach Jeśli chcesz zastosować wzbogacania z wartością tagu lub właściwości w bliźniaczej reprezentacji urządzenia, wartość będzie należy oznaczyć jako wartość ciągu. Na przykład jeśli ustawiono wartość będącą liczbą wzbogacania $twin.tags.field, komunikaty będą opatrzone ciąg "$twin.tags.field" zamiast wartości tego pola z bliźniaczej reprezentacji. Dzieje się w następujących przypadkach:

   * Usługi IoT Hub to w warstwie podstawowa. Centra iot Hub w warstwie podstawowa obsługuje bliźniacze reprezentacje urządzeń.

   * Usługi IoT Hub to w warstwie standardowa, ale urządzenia wysyłającego wiadomość ma nie bliźniaczej reprezentacji urządzenia.

   * Usługi IoT Hub to w warstwie standardowa, ale nie istnieje ścieżka bliźniaczej reprezentacji urządzenia, które są używane dla wartości wzbogacanie. Na przykład jeśli ustawiono wartość wzbogacania $twin.tags.location i bliźniaczej reprezentacji urządzenia nie ma właściwość lokalizacji, w obszarze tagi, komunikat jest oznaczony przy użyciu parametrów "$twin.tags.location". 

* Aktualizacje w bliźniaczej reprezentacji urządzenia może potrwać do pięciu minut zostaną odzwierciedlone w odpowiedniej wartości wzbogacania.

* Rozmiar łączna liczba wiadomości, w tym wzbogacenia, nie może przekraczać 256 KB. Jeśli rozmiar komunikatu przekracza 256 KB, komunikat będzie się zmniejszać usługi IoT Hub. Możesz użyć [metryki usługi IoT Hub](iot-hub-metrics.md) do identyfikowania i debugować błędy, gdy komunikaty są odrzucane. Na przykład można monitorować d2c.telemetry.egress.invalid.

## <a name="pricing"></a>Cennik

Wzbogacenia komunikat dostępnych bez dodatkowej opłaty. Obecnie opłaty są naliczane podczas wysyłania komunikatu do usługi IoT Hub. Opłaty są naliczane tylko raz dla tego komunikatu, nawet jeśli wiadomość jest wysyłana do wielu punktów końcowych.

## <a name="availability"></a>Dostępność

Ta funkcja jest dostępna w wersji zapoznawczej i jest dostępna we wszystkich regionach z wyjątkiem wschodnie stany USA, zachodnie stany USA, Europa Zachodnia, [Azure dla instytucji rządowych](/azure/azure-government/documentation-government-welcome), [Azure China 21Vianet](/azure/china), i [Azure (Niemcy)](https://azure.microsoft.com/global-infrastructure/germany/).

## <a name="next-steps"></a>Kolejne kroki

Zapoznaj się z następującymi artykułami, aby uzyskać więcej informacji na temat routingu komunikatów do Centrum IoT:

* [Umożliwia routing komunikatów usługi IoT Hub wysyłanie komunikatów z urządzenia do chmury do różnych punktów końcowych](iot-hub-devguide-messages-d2c.md)

* [Samouczek: Routing w Centrum IoT Hub](tutorial-routing.md)