---
title: Dowiedz się, jak moduły uruchamiają logikę na twoich urządzeniach — Usługa Azure IoT Edge | Dokumenty firmy Microsoft
description: Moduły usługi Azure IoT Edge to konteneryzowane jednostki logiki, które można wdrożyć i zarządzać zdalnie, dzięki czemu można uruchamiać logikę biznesową na urządzeniach usługi IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/21/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 1c625e628f53d156ad56a1c69df1c23aec9120ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76548717"
---
# <a name="understand-azure-iot-edge-modules"></a>Omówienie modułów usługi Azure IoT Edge

Usługa Azure IoT Edge umożliwia wdrażanie logiki biznesowej i zarządzanie nią na krawędzi w postaci *modułów.* Moduły usługi Azure IoT Edge są najmniejszą jednostką obliczeń zarządzaną przez usługę IoT Edge i mogą zawierać usługi platformy Azure (takie jak usługa Azure Stream Analytics) lub własny kod specyficzny dla rozwiązania. Aby zrozumieć, w jaki sposób moduły są opracowywane, wdrażane i utrzymywane, należy wziąć pod uwagę cztery elementy koncepcyjne modułu:

* Obraz **modułu** to pakiet zawierający oprogramowanie definiujące moduł.
* **Wystąpienie modułu** jest określoną jednostką obliczeń z uruchomionym obrazem modułu na urządzeniu Z krawędzią IoT. Wystąpienie modułu jest uruchamiane przez środowisko uruchomieniowe IoT Edge.
* **Tożsamość modułu** to informacje (w tym poświadczenia zabezpieczeń) przechowywane w centrum IoT Hub, które są skojarzone z każdym wystąpieniem modułu.
* **Bliźniacza reprezentacja modułu** to dokument JSON przechowywany w centrum IoT Hub, który zawiera informacje o stanie wystąpienia modułu, w tym metadane, konfiguracje i warunki.

## <a name="module-images-and-instances"></a>Obrazy i wystąpienia modułów

Obrazy modułów usługi IoT Edge zawierają aplikacje, które wykorzystują funkcje zarządzania, zabezpieczeń i komunikacji środowiska wykonawczego usługi IoT Edge. Można tworzyć własne obrazy modułów lub eksportować je z obsługiwanej usługi platformy Azure, takiej jak usługa Azure Stream Analytics.
Obrazy istnieją w chmurze i mogą być aktualizowane, zmieniane i wdrażane w różnych rozwiązaniach. Na przykład moduł, który używa uczenia maszynowego do przewidywania danych wyjściowych linii produkcyjnej istnieje jako oddzielny obraz niż moduł, który używa wizji komputerowej do sterowania dronem.

Za każdym razem, gdy obraz modułu jest wdrażany na urządzeniu i uruchamiany przez środowisko uruchomieniowe IoT Edge, tworzone jest nowe wystąpienie tego modułu. Dwa urządzenia w różnych częściach świata mogą korzystać z tego samego obrazu modułu. Jednak każde urządzenie będzie miało własne wystąpienie modułu, gdy moduł jest uruchamiany na urządzeniu.

![Diagram - Obrazy modułów w chmurze, wystąpienia modułów na urządzeniach](./media/iot-edge-modules/image_instance.png)

W implementacji obrazy modułów istnieją jako obrazy kontenerów w repozytorium, a wystąpienia modułów są kontenerami na urządzeniach.

<!--
As use cases for Azure IoT Edge grow, new types of module images and instances will be created. For example, resource constrained devices cannot run containers so may require module images that exist as dynamic link libraries and instances that are executables. 
-->

## <a name="module-identities"></a>Tożsamości modułu

Gdy nowe wystąpienie modułu jest tworzony przez środowisko uruchomieniowe usługi IoT Edge, pobiera odpowiednią tożsamość modułu. Tożsamość modułu jest przechowywana w centrum IoT Hub i jest używana jako zakres adresowania i zabezpieczeń dla całej komunikacji lokalnej i w chmurze dla tego wystąpienia modułu.

Tożsamość skojarzona z wystąpieniem modułu zależy od tożsamości urządzenia, na którym jest uruchomione wystąpienie i nazwy, którą podasz do tego modułu w rozwiązaniu. Na przykład jeśli `insight` wywołasz moduł, który używa usługi Azure Stream Analytics `Hannover01`i wdrożyć go na urządzeniu o nazwie `/devices/Hannover01/modules/insight`, środowisko uruchomieniowe usługi IoT Edge tworzy odpowiednią tożsamość modułu o nazwie .

Oczywiście w scenariuszach, gdy trzeba wdrożyć jeden obraz modułu wiele razy na tym samym urządzeniu, można wdrożyć ten sam obraz wiele razy z różnymi nazwami.

![Diagram — identyfikatory modułów są unikatowe w obrębie urządzeń i na różnych urządzeniach](./media/iot-edge-modules/identity.png)

## <a name="module-twins"></a>Bliźnięta modułu

Każde wystąpienie modułu ma również odpowiednie bliźniacze modułu, którego można użyć do skonfigurowania wystąpienia modułu. Wystąpienie i bliźniaczej reprezentacji są skojarzone ze sobą za pośrednictwem tożsamości modułu.

Bliźniacza reprezentacja modułu to dokument JSON, który przechowuje właściwości informacji i konfiguracji modułu. Ta koncepcja jest równoległa do koncepcji [bliźniaczej reprezentacji urządzenia](../iot-hub/iot-hub-devguide-device-twins.md) z usługi IoT Hub. Struktura bliźniaczej reprezentacji modułu jest taka sama jak bliźniaczej reprezentacji urządzenia. Interfejsy API używane do interakcji z obu typów bliźniąt są również takie same. Jedyną różnicą między nimi jest tożsamość używana do tworzenia wystąpienia sdk klienta.

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

Moduły usługi Azure IoT Edge mogą działać w trybie offline przez czas nieokreślony po synchronizacji z usługą IoT Hub co najmniej raz. Urządzenia IoT Edge można również rozszerzyć tę funkcję w trybie offline na inne urządzenia IoT. Aby uzyskać więcej informacji, zobacz [Opis rozszerzonych funkcji offline dla urządzeń, modułów i urządzeń podrzędnych usługi IoT Edge.](offline-capabilities.md)

## <a name="next-steps"></a>Następne kroki

* [Poznaj wymagania i narzędzia do tworzenia modułów IoT Edge](module-development.md)
* [Opis środowiska wykonawczego usługi Azure IoT Edge i jego architektury](iot-edge-runtime.md)
