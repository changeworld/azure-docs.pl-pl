---
title: Omówienie wzbogacania komunikatów IoT Hub platformy Azure
description: Omówienie wzbogacania komunikatów dla komunikatów usługi Azure IoT Hub
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: robinsh
ms.openlocfilehash: 9e3df59af33d6e5fdd1024d42c34dc2b4ed3184e
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69873194"
---
# <a name="message-enrichments-for-device-to-cloud-iot-hub-messages-preview"></a>Wzbogacanie komunikatów dla komunikatów IoT Hub między urządzeniami a chmurą (wersja zapoznawcza)

*Wzbogacanie komunikatów* polega na tym, że IoT Hub *sygnatury* wiadomości z dodatkowymi informacjami przed wysłaniem komunikatów do określonego punktu końcowego. Jednym z powodów użycia wzbogacania komunikatów jest dołączenie danych, które mogą być używane do uproszczenia przetwarzania podrzędnego. Na przykład wzbogacanie komunikatów telemetrycznych urządzeń za pomocą znacznika sznurka urządzenia może zmniejszyć obciążenie klientów, aby umożliwić wywoływanie interfejsu API z użyciem urządzeń z systemem dla tych informacji.

![Przepływ wzbogacania komunikatów](./media/iot-hub-message-enrichments-overview/message-enrichments-flow.png)

Wzbogacanie komunikatów ma trzy kluczowe elementy:

* Nazwa lub klucz wzbogacenia

* Wartość

* Co najmniej jeden [punkt końcowy](iot-hub-devguide-endpoints.md) , dla którego ma zostać zastosowane wzbogacanie.

**Klucz** jest ciągiem. Klucz może zawierać tylko znaki alfanumeryczne lub znaki specjalne: Łącznik (`-`), podkreślenie (`_`) i kropka (`.`).

Może to być dowolny z następujących przykładów:

* Dowolny ciąg statyczny. Wartości dynamiczne, takie jak warunki, logika, operacje i funkcje, są niedozwolone. Na przykład jeśli tworzysz aplikację SaaS, która jest używana przez kilku klientów, można przypisać identyfikator do każdego klienta i udostępnić ten identyfikator w aplikacji. Gdy aplikacja zostanie uruchomiona, IoT Hub będzie sygnaturą komunikatów telemetrycznych urządzenia z identyfikatorem klienta, dzięki czemu można przetwarzać komunikaty inaczej dla każdego klienta.

* Nazwa wysyłanego przez Centrum IoT Hub. Ta wartość jest *$iothubname*.

* Informacje z sznurka urządzenia, takie jak jego ścieżka. Przykładami mogą być *$Twin. Tags. Field* i *$Twin. Tags. Latitude*.

   > [!NOTE]
   > W tej chwili tylko $iothubname, $twin. Tags, $twin. Properties. pożądaną i $twin. Properties. raportowane są obsługiwane zmienne do wzbogacania komunikatów.

Wzbogacanie komunikatów są dodawane jako właściwości aplikacji do komunikatów wysyłanych do wybranych punktów końcowych.  

## <a name="applying-enrichments"></a>Stosowanie wzbogacania

Komunikaty mogą pochodzić z dowolnego źródła danych obsługiwanego przez [IoT Hub Routing komunikatów](iot-hub-devguide-messages-d2c.md), w tym następujące przykłady:

* dane telemetryczne urządzenia, takie jak temperatura lub ciśnienie
* powiadomienia o zmianie przędzy urządzenia — zmiany w bliźniaczych urządzeniach
* zdarzenia cyklu życia urządzenia, takie jak podczas tworzenia lub usuwania urządzenia

Można dodawać wzbogacenia do komunikatów, które przechodzą do wbudowanego punktu końcowego IoT Hub, lub komunikatów, które są kierowane do niestandardowych punktów końcowych, takich jak Azure Blob Storage, Service Bus Queue lub Service Bus temat.

Możesz również dodać wzbogacania do komunikatów, które są publikowane w Event Grid, wybierając punkt końcowy jako Event Grid. Aby uzyskać więcej informacji, zobacz [Centrum IoT i Event Grid](iot-hub-event-grid.md).

Wzbogacania są stosowane w odniesieniu do punktu końcowego. Jeśli określisz pięć wzbogaceń dla określonego punktu końcowego, wszystkie komunikaty przechodzą do tego punktu końcowego będą znakowane z tymi samymi pięcioma wzbogacaniem.

Aby dowiedzieć się, jak wypróbować wzbogacanie komunikatów, zobacz [Samouczek dotyczący wzbogacania komunikatów](tutorial-message-enrichments.md)

## <a name="limitations"></a>Ograniczenia

* Można dodać do 10 wzbogaceń IoT Hub dla tych centrów w warstwie Standardowa lub podstawowa. W przypadku centrów IoT w warstwie Bezpłatna można dodać do 2 wzbogacania.

* W niektórych przypadkach, jeśli stosujesz wzbogacanie z wartością ustawioną na tag lub właściwość w bliźniaczym urządzeniu, wartość zostanie zastosowana jako wartość ciągu. Na przykład jeśli wartość wzbogacania jest ustawiona na $twin. Tags. pole, komunikaty będą oznaczone ciągiem "$twin. Tags. Field" zamiast wartości tego pola z sznurka. Dzieje się tak w następujących przypadkach:

   * IoT Hub znajduje się w warstwie Podstawowa. Centra IoT warstwy Podstawowa nie obsługują bliźniaczych reprezentacji urządzeń.

   * IoT Hub znajduje się w warstwie Standardowa, ale urządzenie wysyłające wiadomość nie ma sznurka urządzenia.

   * IoT Hub znajduje się w warstwie Standardowa, ale nie istnieje ścieżka ze osiową urządzenia użytą dla wartości wzbogacania. Na przykład jeśli wartość wzbogacania jest ustawiona na $twin. Tags. Location, a sznurki urządzenia nie mają właściwości Location w obszarze Tagi, komunikat jest oznaczany ciągiem "$twin. Tags. Location". 

* Aktualizacje sznurka urządzenia mogą potrwać do 5 minut, aby można było je odzwierciedlić w odpowiedniej wartości wzbogacania.

* Łączny rozmiar komunikatów, łącznie z wzbogacami, nie może przekroczyć 256 KB. Jeśli rozmiar komunikatu przekracza 256 KB, IoT Hub spowoduje porzucenie komunikatu. Za pomocą [metryk IoT Hub](iot-hub-metrics.md) można identyfikować i debugować błędy podczas usuwania komunikatów. Na przykład można monitorować D2C. telemetrię. ruch wychodzący. nieprawidłowy.

* Wzbogacanie komunikatów nie mają zastosowania do zdarzeń zmiany wieloosiowych (część [Plug and Play publicznej wersji zapoznawczej IoT](../iot-pnp/overview-iot-plug-and-play.md)).

## <a name="pricing"></a>Cennik

Wzbogacanie komunikatów są dostępne bez dodatkowych opłat. Obecnie opłata jest naliczana w przypadku wysyłania komunikatu do IoT Hub. Opłata jest naliczana tylko raz dla tego komunikatu, nawet jeśli komunikat przejdzie do wielu punktów końcowych.

## <a name="availability"></a>Dostępność

Ta funkcja jest dostępna w wersji zapoznawczej i jest dostępna we wszystkich regionach z wyjątkiem Wschodnie stany USA, zachodnie stany USA, Europa Zachodnia, [Azure Government](/azure/azure-government/documentation-government-welcome), [chiński (Chiny](/azure/china)) i [Azure (Niemcy](https://azure.microsoft.com/global-infrastructure/germany/)).

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z tymi artykułami, aby uzyskać więcej informacji na temat routingu komunikatów do IoT Hub:

* [Samouczek dotyczący ulepszeń komunikatów](tutorial-message-enrichments.md)

* [Używanie routingu komunikatów IoT Hub do wysyłania komunikatów z urządzenia do chmury do różnych punktów końcowych](iot-hub-devguide-messages-d2c.md)

* [Samouczek: Routing IoT Hub](tutorial-routing.md)
