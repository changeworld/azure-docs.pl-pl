---
title: Dowiedz się, jak moduły uruchamiają logikę na urządzeniach z systemem — usługi Azure IoT Edge | Dokumentacja firmy Microsoft
description: Moduły platformy Azure IoT Edge to jednostki konteneryzowanych logiki, która może być wdrożone i zdalne zarządzanie, dzięki czemu możesz uruchomić logiki biznesowej na urządzeniach brzegowych IoT urządzenia
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/21/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 65cac484a9395aca47a38e2ba430b80c868267f5
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65152666"
---
# <a name="understand-azure-iot-edge-modules"></a>Omówienie modułów usługi Azure IoT Edge

Usługa Azure IoT Edge umożliwia wdrażanie i zarządzanie nimi logiki biznesowej na urządzeniach brzegowych w formie *modułów*. Moduły platformy Azure IoT Edge to najmniejsza obliczeń zarządzane przez usługi IoT Edge i może zawierać usług platformy Azure (np. usługi Azure Stream Analytics) lub kodu specyficznego dla rozwiązania. Aby dowiedzieć się, jak moduły są opracowane, wdrażane i obsługiwane, warto traktować czterech elementów koncepcyjny modułu:

* A **obraz modułu** to pakiet zawierający wersję oprogramowania, który definiuje moduł.
* A **wystąpienia modułu** jest konkretnej jednostki obliczeń systemem obraz modułu urządzenia usługi IoT Edge. Wystąpienia modułu jest uruchomiona przez środowisko uruchomieniowe usługi IoT Edge.
* A **tożsamości modułu** to fragment informacji (w tym poświadczenia zabezpieczeń), przechowywane w usłudze IoT Hub, który jest skojarzony z każdego wystąpienia modułu.
* A **bliźniaczą reprezentację modułu** to dokument JSON przechowywany w usłudze IoT Hub, który zawiera informacje o stanie dla wystąpienia modułu, w tym metadane, konfiguracje i warunki. 

## <a name="module-images-and-instances"></a>Obrazy modułu i wystąpienia

Obrazy modułu usługi IoT Edge zawierają aplikacje wykorzystujące zarządzania, zabezpieczeń i funkcji komunikacji środowiska uruchomieniowego usługi IoT Edge. Możesz tworzyć swoje własne obrazy, modułu lub wyeksportować jeden z obsługiwanych usług platformy Azure, takich jak Azure Stream Analytics.
Obrazy istnieją w chmurze i mogą być aktualizowane, zmienić i wdrożone w różnych rozwiązaniach. Na przykład moduł, który korzysta z uczenia maszynowego przewiduje linii produkcyjnej, wyjściowy istnieje jako osobny obraz niż moduł, który używa przetwarzania obrazów w celu kontrolowania nagrodę: drona. 

Każdorazowo obraz modułu jest wdrażana na urządzeniu i uruchomiona przez środowisko uruchomieniowe usługi IoT Edge, tworzone jest nowe wystąpienie tego modułu. Dwa urządzenia w różnych częściach świata, można użyć tego samego obrazu modułu. Jednak każde urządzenie będzie mieć własne wystąpienie modułu, podczas uruchamiania modułu na urządzeniu. 

![Diagram — obrazy modułu w chmurze, wystąpień modułu na urządzeniach](./media/iot-edge-modules/image_instance.png)

W implementacji obrazów modułów istnieje jako obrazów kontenerów w repozytorium, a wystąpienia modułu to kontenery, które na urządzeniach. 

<!--
As use cases for Azure IoT Edge grow, new types of module images and instances will be created. For example, resource constrained devices cannot run containers so may require module images that exist as dynamic link libraries and instances that are executables. 
-->

## <a name="module-identities"></a>Moduł tożsamości

Po utworzeniu nowego wystąpienia modułu przez środowisko uruchomieniowe usługi IoT Edge to wystąpienie jest skojarzony z odpowiedniej tożsamości modułu. Tożsamość modułu jest przechowywany w usłudze IoT Hub, a jako zakres adresowania i zabezpieczeń dla wszystkich lokalnych i chmurze komunikacji dla tego wystąpienia określonego modułu.

Tożsamość skojarzoną z wystąpieniem modułu zależy od tożsamości urządzenia działającego wystąpienia i nazwę tego modułu jest zapewnienie w rozwiązaniu. Na przykład jeśli wywołasz `insight` moduł, który korzysta z usługi Azure Stream Analytics i wdrożyć go na urządzenie o nazwie `Hannover01`, środowisko uruchomieniowe usługi IoT Edge tworzy odpowiedniej tożsamości modułu o nazwie `/devices/Hannover01/modules/insight`.

Wyraźnie widać w scenariuszach, gdy należy wdrożyć jeden obraz modułu wiele razy w tym samym urządzeniu, można wdrożyć ten sam obraz wielokrotnie pod różnymi nazwami.

![Diagram — moduł tożsamości są unikatowe w obrębie urządzeń i na urządzeniach](./media/iot-edge-modules/identity.png)

## <a name="module-twins"></a>Bliźniaczych reprezentacjach modułów

Każde wystąpienie modułu ma też odpowiedni bliźniaczą reprezentację modułu, które umożliwiają skonfigurowanie wystąpienia modułu. Wystąpienia i bliźniaczej reprezentacji są powiązane ze sobą za pomocą tożsamości modułu. 

Bliźniacza reprezentacja modułu jest dokumentem JSON, która przechowuje informacje i konfiguracji właściwości modułu. To pojęcie równoleżnikami [bliźniaczej reprezentacji urządzenia](../iot-hub/iot-hub-devguide-device-twins.md) koncepcji z usługi IoT Hub. Struktura bliźniaczą reprezentację modułu jest taka sama jak w bliźniaczej reprezentacji urządzenia. Interfejsy API używane do interakcji z oboma typami bliźniaczych elementów również są takie same. Jedyną różnicą między tymi dwoma jest to tożsamość używana do tworzenia wystąpienia zestawu SDK klienta. 

```csharp
// Create a ModuleClient object. This ModuleClient will act on behalf of a 
// module since it is created with a module’s connection string instead 
// of a device connection string. 
ModuleClient client = new ModuleClient.CreateFromEnvironmentAsync(settings); 
await client.OpenAsync(); 
 
// Get the module twin 
Twin twin = await client.GetTwinAsync(); 
```

## <a name="offline-capabilities"></a>Możliwości w trybie offline

Moduły platformy Azure IoT Edge mogą działać w trybie offline na czas nieokreślony po zakończeniu synchronizacji z usługą IoT Hub co najmniej raz. Urządzenia usługi IoT Edge można rozszerzać ten tryb offline na innych urządzeniach IoT. Aby uzyskać więcej informacji, zobacz [opis rozszerzony możliwości w trybie offline dla usługi IoT Edge, urządzeń, moduły i urządzeń podrzędnych](offline-capabilities.md).

## <a name="next-steps"></a>Kolejne kroki
 - [Zrozumienie wymagań i narzędzia do tworzenia modułów usługi IoT Edge](module-development.md)
 - [Omówienie środowiska uruchomieniowego usługi Azure IoT Edge oraz jej architektury](iot-edge-runtime.md)

