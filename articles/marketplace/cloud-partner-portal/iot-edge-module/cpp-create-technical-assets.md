---
title: Tworzenie zasobów technicznych modułu Usługi Azure IoT Edge | Azure Marketplace
description: Tworzenie zasobów technicznych dla modułu usługi IoT Edge.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: b36d03695c107054549fba716f4cc27ecb7bba26
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80983487"
---
# <a name="prepare-your-iot-edge-module-technical-assets"></a>Przygotowanie zasobów technicznych modułu IoT Edge

>[!Important]
>Od 13 kwietnia 2020 r. rozpoczniemy przenoszenie zarządzania ofertami modułu IoT Edge do Centrum partnerskiego. Po migracji utworzysz oferty i zarządzasz nimi w Centrum partnerów. Postępuj zgodnie z instrukcjami w [tworzenie oferty modułu IoT Edge,](https://aka.ms/AzureIoTTechAsset) aby zarządzać zmigrowanymi ofertami.

W tym artykule opisano wymagania, które muszą spełniać zasoby techniczne modułu usługi IoT Edge przed opublikowaniem w portalu Azure Marketplace.

## <a name="understanding-iot-edge-modules-and-getting-started"></a>Opis modułów IoT Edge i rozpoczęcie pracy

Moduł usługi IoT Edge to kontener zgodny z docker, który jest uruchamiany na urządzeniu usługi IoT Edge.

- Aby dowiedzieć się więcej o modułach usługi IoT Edge, zobacz [Opis modułów usługi Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-modules).
- Aby rozpocząć prace nad rozwojem modułów IoT Edge, zapoznaj się z [wymaganiami i narzędziami dotyczącymi opracowywania modułów IoT Edge](https://docs.microsoft.com/azure/iot-edge/module-development).

## <a name="technical-requirements"></a>Wymagania techniczne

Aby moduł usługi IoT Edge został certyfikowany i opublikowany w portalu Azure Marketplace, muszą być spełnione następujące wymagania techniczne.

### <a name="platform-support"></a>Obsługa platform

Moduł IoT Edge musi obsługiwać jedną z następujących opcji platformy.

#### <a name="tier-1-platforms-supported-by-iot-edge"></a>Platformy poziomu 1 obsługiwane przez IoT Edge

Obsługa wszystkich platform warstwy 1 obsługiwanych przez usługę IoT Edge (zgodnie z zapisem w [usłudze Azure IoT Edge).](https://docs.microsoft.com/azure/iot-edge/support) Zalecamy tę opcję, ponieważ zapewnia lepszą obsługę klienta. Zaprezentowane zostaną moduły spełniające te kryteria. Moduł korzystający z tej opcji platformy musi:

- Podaj `latest` znacznik i tag wersji `1.0.1`(na przykład), które są znacznikami manifestu utworzonymi za pomocą [narzędzia manifestu](https://github.com/estesp/manifest-tool)GitHub .
- Karta [Marketplace](./cpp-marketplace-tab.md) służy do dodawania łącza do [urządzeń z certyfikatem IoT Edge.](https://aka.ms/iot-edge-certified) Ten link jest `https://aka.ms/iot-edge-certified`rozpoznawany na stronie internetowej, na której klienci mogą przeglądać lub wyszukiwać certyfikowane urządzenia. Ta witryna sieci Web jest również znana jako katalog urządzeń [z certyfikatem usługi Azure IoT Edge.](https://catalog.azureiotsolutions.com/)

#### <a name="a-subset-of-tier-1-platforms-supported-by-iot-edge"></a>Podzbiór platform warstwy 1 obsługiwanych przez IoT Edge
  
Obsługa podzbioru (co najmniej jednego) platform warstwy 1 obsługiwanych przez usługę IoT Edge (zgodnie z zapisem w [pomocy technicznej usługi Azure IoT Edge).](https://docs.microsoft.com/azure/iot-edge/support) Moduł korzystający z tej opcji platformy musi:

- Podaj `latest` tag i tag wersji `1.0.1`(na przykład ), które są znacznikami manifestu utworzonymi za pomocą [narzędzia manifestu](https://github.com/estesp/manifest-tool) GitHub, jeśli obsługiwana jest więcej niż jedna platforma. Znaczniki manifestu są opcjonalne tylko wtedy, gdy obsługiwana jest jedna platforma.
- Karta [Marketplace](./cpp-marketplace-tab.md) służy do umieszczania łącza do co najmniej jednego urządzenia usługi IoT Edge w katalogu urządzeń [z certyfikatem usługi Azure IoT Edge.](https://catalog.azureiotsolutions.com/)

### <a name="device-dimensions"></a>Wymiary urządzenia

Wymiary modułu IoT Edge (CPU/RAM/Storage/GPU/ETC.) na ukierunkowanych urządzeniach IoT Edge muszą spełniać następujące wymagania:

- Moduł musi współpracować z co najmniej jednym urządzeniem **z certyfikatem Usługi IoT Edge** w katalogu urządzeń [z certyfikatem usługi Azure IoT Edge.](https://catalog.azureiotsolutions.com/)
- **Minimalne wymagania sprzętowe** muszą być udokumentowane jako ostatni akapit w opisie oferty (w [zakładce Marketplace).](./cpp-marketplace-tab.md) Opcjonalnie można również wyświetlić listę zalecanych wymagań sprzętowych, jeśli znacznie się różnią. Na przykład dodaj następującą sekcję na końcu opisu oferty:

  ```html
    <p><u>Minimum hardware requirements:</u> Linux x64 and arm32  OS, 1GB of RAM, 500 Mb of storage</p>
  ```

### <a name="configuration"></a>Konfigurowanie

Zawiera również domyślne ustawienia konfiguracji, aby wdrożenie na urządzeniu usługi IoT Edge było jak najprostsze. Kontener może również zawierać SDK modułu usługi IoT Edge, aby umożliwić komunikację z edgeHub i IoT Hub.

#### <a name="default-configuration"></a>Konfiguracja domyślna

Moduły usługi IoT Edge muszą być w stanie rozpocząć od ustawień domyślnych podanych na [karcie SKU portalu Cloud Partner](./cpp-skus-tab.md). Dostępne są następujące ustawienia domyślne:

- **Trasy domyślne**
- Domyślne **właściwości — dwójce — żądane**
- Domyślne **zmienne środowiskowe**
- Domyślne **opcje tworzenia**

W scenariuszu, w którym parametr wymagany dla wartości domyślnej nie ma sensu (na przykład adres IP serwera klienta), należy dodać parametr jako wartość domyślną. Ta wartość jest ujęta w nawiasy i wielkie litery. W tym przykładzie należy skonfigurować następującą domyślną zmienną środowiskową:

```
    ServerIPAddress = <MY_SERVER_IP_ADDRESS>
```

#### <a name="configuration-documentation"></a>Dokumentacja konfiguracjna

Wszystkie ustawienia konfiguracji modułu usługi IoT Edge muszą być wyraźnie udokumentowane (jak używać jego tras, bliźniaczych żądanych właściwości, zmiennych środowiskowych, createOptions i tak dalej). Podaj link do dokumentacji lub dokumentacja musi być częścią opisu oferty/sku.

### <a name="tags-and-versioning"></a>Tagi i przechowywanie wersji

Klienci muszą mieć możliwość łatwego wdrażania modułu i automatycznego ujmowanie aktualizacji z portalu Marketplace (w scenariuszu dla deweloperów). Muszą również mieć możliwość używania i zamrażania dokładnej wersji, którą przetestowali (w scenariuszu produkcyjnym).

Aby spełnić oczekiwania tych klientów i zostać opublikowane na rynku, moduły Usługi IoT Edge muszą spełniać następujące wymagania:

- Dołącz znacznik `latest` manifestu, który wskazuje najnowszą wersję na wszystkich obsługiwanych platformach.
- Tagi wersji muszą mieć postać X.Y.Z, gdzie X, Y i Z są liczbami całkowitymi.
- Dołącz tag "version", `1.0.1`taki jak , który wskazuje określoną wersję na wszystkich obsługiwanych platformach.
- Nie aktualizuj tagów "version", `1.0.1`takich jak , ponieważ muszą być niezmienne.

>[!Note]
>Opcjonalnie przechowywanie wersji może zawierać znaczniki "wersja `2.0` `1.0`toczenia", takie jak i . Obsługuje to utrzymanie wielu głównych wersji równolegle.

### <a name="telemetry"></a>Telemetria

Moduły korzystające z zestawu SDK modułu IoT muszą ustawić unikatowy identyfikator modułu na `PublisherId.OfferId.SkuId` potrzeby telemetrii. Unikatowy identyfikator umożliwia witrynie Azure Marketplace identyfikowanie liczby uruchomionych wystąpień modułu.

 Użyj następujących metod z zestawu SDK modułu `ProductInfo` IoT, aby ustawić ten identyfikator:

- [C\#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.productinfo?view=azure-dotnet#Microsoft_Azure_Devices_Client_DeviceClient_ProductInfo) 
- [C](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Python](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.productinfo?view=azure-java-stable)

W przypadku modułów, które nie używają zestawu SDK modułu IoT, mniej precyzyjne szczegółowe informacje są dostępne za pośrednictwem portalu cloud partner, takich jak liczba pobrań.

### <a name="security"></a>Zabezpieczenia

Moduły IoT Edge muszą poprosić o najmniej uprzywilejowany dostęp do hosta, jak to możliwe. Należy unikać [uprzywilejowanych modułów.](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities)

### <a name="module-iot-sdk"></a>Moduł IoT SDK

Włączenie SDK modułu IoT nie jest warunkiem wstępnym certyfikacji. Jednak w tym moduł IoT SDK moduł może zapewnić lepsze środowisko użytkownika. Na przykład do obsługi routingu lub wysyłania wiadomości do chmury.

Zestaw SDK modułu IoT jest wymagany do uzyskania danych telemetrycznych dotyczących liczby uruchomionych wystąpień modułu.


## <a name="recertification-process"></a>Proces ponownej certyfikacji

<!-- Add legal time windows-->
Partnerzy otrzymają powiadomienia za każdym razem, gdy pojawi się przełomowa zmiana, która wpływa na ich moduły, takie jak:

- Macierz wsparcia systemu operacyjnego/łuku poziomu obsługiwanego przez IoT Edge
- SDK modułu IoT
- Środowisko uruchomieniowe ioT Edge
- Wytyczne dotyczące certyfikacji modułów IoT Edge

Partnerzy będą musieli zaktualizować swoje moduły i ponownie certyfikować je za pomocą narzędzia Cloud Partner Portal.

## <a name="host-your-iot-edge-module-in-an-azure-container-registry"></a>Hostuj moduł usługi IoT Edge w rejestrze kontenerów platformy Azure

Aby przekazać moduł usługi IoT Edge do portalu cloud partner, należy najpierw hostować go w [rejestrze kontenerów platformy Azure](https://azure.microsoft.com/services/container-registry/) (ACR). Moduł musi zawierać wszystkie znaczniki, które chcesz opublikować, w tym znaczniki obrazu, do których odwołuje się znacznik manifestu.


## <a name="next-steps"></a>Następne kroki

- [Stwórz ofertę modułów IoT Edge](./cpp-create-offer.md)
