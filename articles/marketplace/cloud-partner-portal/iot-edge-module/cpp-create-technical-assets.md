---
title: Utwórz moduł usługi Azure IoT Edge, zasoby techniczne | Portal Azure Marketplace
description: Utwórz zasoby techniczne dla modułu usługi IoT Edge.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: pabutler
ms.openlocfilehash: 9f40e2272388e6f467b8c3d0b01a6639bf652e80
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64942386"
---
# <a name="prepare-your-iot-edge-module-technical-assets"></a>Przygotowanie modułu usługi IoT Edge, zasoby techniczne

W tym artykule opisano wymagania, które zasoby techniczne moduł usługi IoT Edge, które muszą spełniać zanim został opublikowany w witrynie Azure Marketplace.

## <a name="understanding-iot-edge-modules-and-getting-started"></a>Opis modułów usługi IoT Edge i wprowadzenie

Moduł usługi IoT Edge to kontener zgodnego z platformą Docker, który wprowadził do uruchamiania na urządzeniu usługi IoT Edge.

- Aby dowiedzieć się więcej na temat moduły usługi IoT Edge, zobacz [modułów Omówienie usługi Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-modules).
- Wprowadzenie do tworzenia modułu usługi IoT Edge, zobacz [wymagania i narzędzia do tworzenia modułów usługi IoT Edge](https://docs.microsoft.com/azure/iot-edge/module-development).

## <a name="technical-requirements"></a>Wymagania techniczne

Muszą być spełnione następujące wymagania techniczne w kolejności dla modułu usługi IoT Edge certyfikowane i opublikowania w portalu Azure Marketplace.

### <a name="platform-support"></a>Obsługa platform

Moduł usługi IoT Edge musi być spełniony jeden z następujących opcji platformy.

#### <a name="tier-1-platforms-supported-by-iot-edge"></a>Warstwa 1 wsparcia platform obsługiwanych przez usługi IoT Edge

Obsługa wszystkich platform warstwy 1 obsługiwanych przez usługi IoT Edge (zarejestrowane w [pomocy technicznej usługi Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/support)). Zaleca się tej opcji, ponieważ zapewnia lepsze środowisko klienta. Moduły spełniające te kryteria będą pokazywane. Moduł przy użyciu tej opcji platformy musi:

- Podaj `latest` znacznikiem i wersji (na przykład `1.0.1`) manifestu tagi utworzonych za pomocą usługi GitHub, które są [narzędzia manifestu](https://github.com/estesp/manifest-tool).
- Użyj [kartę Marketplace](./cpp-marketplace-tab.md) dodać link do [zgodne usługi IoT Edge certyfikowanych urządzeń](https://aka.ms/iot-edge-certified). Ten link jest rozpoznawana jako `https://aka.ms/iot-edge-certified`, witryny sieci Web, gdzie klienci mogą przeglądać lub wyszukiwać certyfikowanych urządzeń. Ta witryna sieci Web jest także znana jako [IoT Edge z certyfikatem platformy Azure](https://catalog.azureiotsolutions.com/) — wykaz urządzeń.

#### <a name="a-subset-of-tier-1-platforms-supported-by-iot-edge"></a>Podzbiór platformach warstwy 1 obsługiwanych przez usługi IoT Edge
  
Obsługuje podzbiór (co najmniej jeden) platformach warstwy 1 obsługiwanych przez usługi IoT Edge (zarejestrowane w [pomocy technicznej usługi Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/support)). Moduł przy użyciu tej opcji platformy musi:

- Podaj `latest` znacznikiem i wersji (na przykład `1.0.1`) manifestu tagi utworzonych za pomocą usługi GitHub, które są [narzędzia manifestu](https://github.com/estesp/manifest-tool) Jeśli więcej niż jedną platformę jest obsługiwana. Tagi manifestu są opcjonalne tylko, gdy jedna platforma jest obsługiwana.
- Użyj [kartę Marketplace](./cpp-marketplace-tab.md) się podanie linku do co najmniej jedno urządzenie usługi IoT Edge w [IoT Edge z certyfikatem platformy Azure](https://catalog.azureiotsolutions.com/) — wykaz urządzeń.

### <a name="device-dimensions"></a>Wymiary urządzenia

Wymiary modułu usługi IoT Edge (Procesora/pamięci RAM/Storage/GPU/itp.) na urządzeniach usługi IoT Edge docelowych musi spełniać następujące wymagania:

- Moduł musi **działają z co najmniej jeden certyfikat usługi IoT Edge** urządzenia w [IoT Edge z certyfikatem platformy Azure](https://catalog.azureiotsolutions.com/) — wykaz urządzeń.
- **Minimalne wymagania sprzętowe** musi zostać udokumentowana jako ostatni akapit w opisie oferty (w obszarze [kartę Marketplace](./cpp-marketplace-tab.md)). Opcjonalnie możesz także wyświetlić listę zalecane wymagania sprzętowe jeśli różnią się znacznie. Na przykład dodaj następującą sekcję na końcu opis oferty:

  ```html
    <p><u>Minimum hardware requirements:</u> Linux x64 and arm32  OS, 1GB of RAM, 500 Mb of storage</p>
  ```

### <a name="configuration"></a>Konfigurowanie

Zawiera on również domyślne ustawienia konfiguracji, aby wdrożenie na urządzeniu usługi IoT Edge tak proste, jak to możliwe. Kontener może również obejmować SDK IoT Edge modułu celu umożliwienia komunikacji z Centrum IoT i edgeHub.

#### <a name="default-configuration"></a>Konfiguracja domyślna

Moduły usługi IoT Edge, musi być w stanie uruchomić przy użyciu ustawień domyślnych w [jednostki SKU na karcie portalu Cloud Partner](./cpp-skus-tab.md). Dostępne są następujące ustawienia domyślne:

- Domyślne **trasy**
- Domyślne **bliźniaczej reprezentacji żądane właściwości**
- Domyślne **zmiennych środowiskowych**
- Domyślne **CreateOptions, można żądań**

W scenariuszu, gdzie wymagane dla wartości domyślnej parametru nie ma sensu (np. adres IP serwera klienta) Dodaj parametr z wartością domyślną. Ta wartość jest ujęta w nawiasy kwadratowe i napisane wielkimi literami. W tym przykładzie należy skonfigurować następującą zmienną środowiskową domyślne:

```
    ServerIPAddress = <MY_SERVER_IP_ADDRESS>
```

#### <a name="configuration-documentation"></a>Dokumentacja konfiguracji

Wszystkie ustawienia konfiguracji modułu usługi IoT Edge, muszą być wyraźnie udokumentowane (jak korzystać z jego trasy, bliźniacza reprezentacja żądane właściwości, zmienne środowiskowe, CreateOptions, można żądań i tak dalej.) Podaj link do dokumentacji lub dokumentacji musi być częścią opis oferty/jednostki sku.

### <a name="tags-and-versioning"></a>Znaczniki i przechowywania wersji

Musi umożliwiać klientom w łatwy sposób Wdrażaj modułu i automatycznie pobierać aktualizacje z witryny marketplace (w przypadku scenariusza dla deweloperów.) Również muszą być w stanie używać, i Zablokuj dokładnie wersji, które zostały przetestowane (w scenariuszu produkcji.)

Aby spełnić oczekiwania tych klientów i zostaną opublikowane w portalu marketplace, moduły usługi IoT Edge musi spełniać następujące wymagania:

- Dołączenie manifestu `latest` tag, który wskazuje najnowszej wersji na wszystkich obsługiwanych platformach.
- Tagi wersji musi być w postaci X.Y.Z, gdzie X, Y i Z są liczbami całkowitymi.
- Obejmują tagu "wersja", takie jak `1.0.1`, który wskazuje na określonej wersji na wszystkich obsługiwanych platformach.
- Nie aktualizuj tagi "wersja", takie jak `1.0.1`, ponieważ muszą być niezmienialne.

>[!Note]
>Opcjonalnie, przechowywanie wersji może zawierać tagi "Uaktualnianie z wersji", takich jak `2.0` i `1.0`. Obejmuje to obsługę, obsługa wielu wersji głównych równolegle.

### <a name="telemetry"></a>Telemetria

Modułów przy użyciu zestawu SDK usługi IoT moduł musi równa identyfikator unikatowy moduł `PublisherId.OfferId.SkuId` celów telemetrii. Unikatowy identyfikator umożliwia w portalu Azure Marketplace zidentyfikować liczbę wystąpień modułu, które są uruchomione.

 Umożliwia ustawienie następujących metod z zestawami SDK IoT moduł `ProductInfo` do tego identyfikatora:

- [C\#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.productinfo?view=azure-dotnet#Microsoft_Azure_Devices_Client_DeviceClient_ProductInfo) 
- [C](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Python](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.productinfo?view=azure-java-stable)

Dla modułów, które nie korzystają z zestawu SDK modułu IoT mniej dokładne informacje są dostępne za pośrednictwem portalu Cloud Partner, takie jak liczba plików do pobrania.

### <a name="security"></a>Bezpieczeństwo

Moduły usługi IoT Edge musisz poprosić o najniższych uprawnieniach dostępu do hosta, jak to możliwe. [Uprzywilejowany modułów](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities) należy unikać.

### <a name="module-iot-sdk"></a>Moduł zestawu SDK usługi IoT

Łącznie z zestawu SDK usługi IoT moduł nie jest wymaganiem wstępnym dla certyfikacji. Jednak w tym Module zestawu SDK usługi IoT oferują lepsze środowisko użytkownika. Na przykład, aby obsługiwać routing lub wysyłanie komunikatów do chmury.

Zestaw SDK modułu IoT jest wymagany do Pobierz dane telemetryczne dotyczące liczby uruchomionych wystąpień modułu.


## <a name="recertification-process"></a>Certyfikacja procesu

<!-- Add legal time windows-->
Partnerzy otrzymasz powiadomienie zawsze wtedy, gdy istnieje istotną zmianę, która wpływa na ich moduły, takie jak:

- Warstwa 1 systemu operacyjnego/arch matryca obsługi replikacji serwera obsługiwanych przez usługi IoT Edge
- Moduł IoT SDK
- Środowisko uruchomieniowe usługi IoT Edge
- Wytyczne dotyczące certyfikacji dla modułu usługi IoT Edge

Partnerzy będą musieli zaktualizować ich modułów i ponownie certyfikując je przy użyciu narzędzia portalu Cloud Partner.

## <a name="host-your-iot-edge-module-in-an-azure-container-registry"></a>Hostowanie modułu usługi IoT Edge w usługi Azure Container Registry

Aby przekazać modułu usługi IoT Edge do portalu Cloud Partner, najpierw musisz hostować go w [usługi Azure Container Registry](https://azure.microsoft.com/services/container-registry/) (ACR). Moduł musi zawierać wszystkie tagi, które chcesz opublikować, w tym znaczniki obrazów odwołuje się tag manifestu.


## <a name="next-steps"></a>Kolejne kroki

- [Utwórz ofertę moduł usługi IoT Edge](./cpp-create-offer.md)
