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
ms.openlocfilehash: 744a901c6b0260f4fc14a2f06b88dfb36973b0f8
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456586"
---
# <a name="understand-azure-iot-edge-modules"></a>Omówienie modułów usługi Azure IoT Edge

Azure IoT Edge pozwala wdrożyć logikę biznesową i zarządzać nią na krawędzi w formie *modułów*. Moduły platformy Azure IoT Edge to najmniejsza obliczeń zarządzane przez usługi IoT Edge i może zawierać usług platformy Azure (np. usługi Azure Stream Analytics) lub kodu specyficznego dla rozwiązania. Aby zrozumieć, jak moduły są opracowywane, wdrażane i konserwowane, ułatwiają one poznanie czterech elementów koncepcyjnych modułu:

* **Obraz modułu** to pakiet zawierający oprogramowanie, które definiuje moduł.
* **Wystąpienie modułu** to określona jednostka obliczeniowa, która uruchamia obraz modułu na urządzeniu IoT Edge. Wystąpienia modułu jest uruchomiona przez środowisko uruchomieniowe usługi IoT Edge.
* **Tożsamość modułu** jest częścią informacji (łącznie z poświadczeniami zabezpieczeń) przechowywaną w IoT Hub, która jest skojarzona z każdym wystąpieniem modułu.
* **Sznurek modułu** jest dokument JSON przechowywany w IoT Hub, który zawiera informacje o stanie wystąpienia modułu, w tym metadane, konfiguracje i warunki. 

## <a name="module-images-and-instances"></a>Obrazy modułu i wystąpienia

Obrazy modułu usługi IoT Edge zawierają aplikacje wykorzystujące zarządzania, zabezpieczeń i funkcji komunikacji środowiska uruchomieniowego usługi IoT Edge. Możesz tworzyć swoje własne obrazy, modułu lub wyeksportować jeden z obsługiwanych usług platformy Azure, takich jak Azure Stream Analytics.
Obrazy istnieją w chmurze i mogą być aktualizowane, zmienić i wdrożone w różnych rozwiązaniach. Na przykład moduł, który korzysta z uczenia maszynowego przewiduje linii produkcyjnej, wyjściowy istnieje jako osobny obraz niż moduł, który używa przetwarzania obrazów w celu kontrolowania nagrodę: drona. 

Każdorazowo obraz modułu jest wdrażana na urządzeniu i uruchomiona przez środowisko uruchomieniowe usługi IoT Edge, tworzone jest nowe wystąpienie tego modułu. Dwa urządzenia w różnych częściach świata mogą korzystać z tego samego obrazu modułu. Jednak każde urządzenie będzie miało własne wystąpienie modułu, gdy moduł zostanie uruchomiony na urządzeniu. 

![Diagram — obrazy modułu w chmurze, wystąpień modułu na urządzeniach](./media/iot-edge-modules/image_instance.png)

W implementacji obrazów modułów istnieje jako obrazów kontenerów w repozytorium, a wystąpienia modułu to kontenery, które na urządzeniach. 

<!--
As use cases for Azure IoT Edge grow, new types of module images and instances will be created. For example, resource constrained devices cannot run containers so may require module images that exist as dynamic link libraries and instances that are executables. 
-->

## <a name="module-identities"></a>Moduł tożsamości

Po utworzeniu nowego wystąpienia modułu przez środowisko uruchomieniowe usługi IoT Edge to wystąpienie jest skojarzony z odpowiedniej tożsamości modułu. Tożsamość modułu jest przechowywany w usłudze IoT Hub, a jako zakres adresowania i zabezpieczeń dla wszystkich lokalnych i chmurze komunikacji dla tego wystąpienia określonego modułu.

Tożsamość skojarzoną z wystąpieniem modułu zależy od tożsamości urządzenia działającego wystąpienia i nazwę tego modułu jest zapewnienie w rozwiązaniu. Na przykład jeśli wywołasz `insight` moduł, który używa Azure Stream Analytics, i wdrażasz go na urządzeniu o nazwie `Hannover01`, środowisko uruchomieniowe IoT Edge tworzy odpowiednią tożsamość modułu o nazwie `/devices/Hannover01/modules/insight`.

Wyraźnie widać w scenariuszach, gdy należy wdrożyć jeden obraz modułu wiele razy w tym samym urządzeniu, można wdrożyć ten sam obraz wielokrotnie pod różnymi nazwami.

![Diagram — moduł tożsamości są unikatowe w obrębie urządzeń i na urządzeniach](./media/iot-edge-modules/identity.png)

## <a name="module-twins"></a>Bliźniaczych reprezentacjach modułów

Każde wystąpienie modułu ma też odpowiedni bliźniaczą reprezentację modułu, które umożliwiają skonfigurowanie wystąpienia modułu. Wystąpienia i bliźniaczej reprezentacji są powiązane ze sobą za pomocą tożsamości modułu. 

Bliźniacza reprezentacja modułu jest dokumentem JSON, która przechowuje informacje i konfiguracji właściwości modułu. Pojęcie to jest równoległe do koncepcji [przędzy urządzenia](../iot-hub/iot-hub-devguide-device-twins.md) z IoT Hub. Struktura sznurka modułu jest taka sama jak sznurki urządzenia. Interfejsy API używane do interakcji z oboma typami bliźniaczych elementów również są takie same. Jedyną różnicą między tymi dwoma jest to tożsamość używana do tworzenia wystąpienia zestawu SDK klienta. 

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

Moduły Azure IoT Edge mogą działać w trybie offline w nieskończoność po synchronizacji z IoT Hub co najmniej raz. Urządzenia IoT Edge mogą również zwiększyć tę możliwość w trybie offline na innych urządzeniach IoT. Aby uzyskać więcej informacji, zobacz informacje o [rozszerzonych możliwościach trybu offline dla urządzeń IoT Edge, modułów i urządzeń podrzędnych](offline-capabilities.md).

## <a name="next-steps"></a>Następne kroki
 - [Informacje na temat wymagań i narzędzi do opracowywania modułów IoT Edge](module-development.md)
 - [Poznaj środowisko uruchomieniowe Azure IoT Edge i jego architekturę](iot-edge-runtime.md)

