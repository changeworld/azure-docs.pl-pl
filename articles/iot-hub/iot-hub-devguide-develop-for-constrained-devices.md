---
title: Tworzenie Centrum IoT Azure dla urządzeń ograniczonego | Dokumentacja firmy Microsoft
description: Przewodnik dewelopera — wskazówki dotyczące sposobu tworzenia przy użyciu usługi Azure IoT SDK dla urządzeń ograniczone.
services: iot-hub
documentationcenter: c
author: yzhong94
manager: timlt
editor: ''
ms.assetid: 979136db-c92d-4288-870c-f305e8777bdd
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/24/2018
ms.author: yizhon
ms.openlocfilehash: 62065b78e3f8191c6423ba9dba4a8f7d16fad114
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655572"
---
# <a name="develop-for-constrained-devices-using-azure-iot-sdks"></a>Tworzenie dla ograniczonego urządzeń przy użyciu zestawów SDK IoT Azure

## <a name="develop-using-azure-iot-hub-c-sdk"></a>Tworzenie przy użyciu zestawu SDK usługi Azure IoT Hub C
Zestaw SDK platformy Azure IoT Hub C są zapisywane w ANSI C (C99), dzięki czemu dobrze nadaje się do obsługi różnych platform z niewielkie rozmiary dysków i pamięci.  Zalecana ilość pamięci RAM jest co najmniej 64 KB, ale dokładne pamięci zależy od protokół używany, liczba otwartych połączeń, a także platformy docelowej.

C zestawu SDK jest dostępny w formie pakietu z stanie get, NuGet i MBED.  Do ograniczonego urządzeń można kompilacji zestawu SDK lokalnie dla danej platformy docelowej. Ta dokumentacja pokazano, jak usunąć niektórych funkcji, aby zmniejszyć rozmiaru przy użyciu zestawu SDK C [cmake][lnk-cmake].  Ponadto tej dokumentacji opisano najlepiej programowania modeli do pracy z urządzeniami ograniczone.

### <a name="building-the-c-sdk-for-constrained-devices"></a>Tworzenie zestawu SDK C dla urządzeń z ograniczeniami
#### <a name="prerequisites"></a>Wymagania wstępne
Wykonaj to [przewodnik konfiguracji] [ lnk-devbox-setup] do przygotowania środowiska programowania do tworzenia C zestawu SDK.  Zanim będzie można pobrać do kroku budowania z cmake, można wywołać flagi cmake usuwanie nieużywanych funkcji.

#### <a name="remove-additional-protocol-libraries"></a>Usuń biblioteki są dostępne dodatkowe protokoły
C zestawu SDK obsługuje protokoły pięć dzisiaj: MQTT, MQTT za pośrednictwem protokołu WebSocket, AMQPs, AMQP za pośrednictwem protokołu WebSocket i HTTPS.    Większość scenariuszy wymaga jednej do dwóch protokołów uruchomiona na kliencie, dlatego można usunąć biblioteki protokołu, które nie korzystają z zestawu SDK.  Dodatkowe informacje o wybieraniu protokołu komunikacyjnego odpowiednie dla danego scenariusza można znaleźć w tym [dokumentu][lnk-choosing-protocol].  Na przykład MQTT jest lekki protokół, który często lepiej jest odpowiedni dla urządzeń z ograniczeniami.

Możesz usunąć biblioteki AMQP i HTTP przy użyciu następującego polecenia cmake:
```
cmake -Duse_amqp=OFF -Duse_http=OFF <Path_to_cmake>
```

#### <a name="remove-sdk-logging-capability"></a>Usuń możliwość rejestrowania zestawu SDK
C zestawu SDK udostępnia szeroką gamę rejestrowania w całym ułatwiające debugowania. Możesz usunąć możliwość rejestrowania urządzeń produkcji przy użyciu następującego polecenia cmake:
```
cmake -Dno_logging=OFF <Path_to_cmake>
```

#### <a name="remove-upload-to-blob-capability"></a>Usuń przekazywanie do obiektu blob możliwości
Możesz przekazać dużych plików do magazynu Azure przy użyciu wbudowanych funkcji w zestawie SDK.  Centrum IoT Azure działa jako dyspozytora skojarzone konto magazynu Azure.  Ta funkcja służy do wysyłania plików multimedialnych, partie dużych telemetrii i dzienniki.  Dowiedz się więcej na temat przekazywania plików z Centrum IoT w tym [dokumentu][lnk-hub-file-upload].  Jeśli aplikacja nie wymaga tej funkcji, należy usunąć tę funkcję za pomocą następującego polecenia cmake:
```
cmake -Ddont_use_uploadtoblob=ON <Path_to_cmake>
```
#### <a name="running-strip-on-linux-environment"></a>Paska uruchomionej na środowisko systemu Linux
Jeśli Twoje pliki binarne uruchamiane w systemie Linux, można wykorzystać [paska poleceń] [ lnk-strip] Aby zmniejszyć rozmiar końcowy aplikacji po kompilacji.
```
strip -s <Path_to_executable>
```

### <a name="programming-models-for-constrained-devices"></a>Modele programowania dla urządzeń z ograniczeniami
#### <a name="avoid-using-the-serializer"></a>Unikaj używania serializator
Zestaw SDK C ma opcjonalny [serializator][lnk-serializer], co pozwala na deklaratywne mapowania tabele umożliwiają definiowanie metod i właściwości dwie urządzenia.  Serializator zaprojektowano w celu uproszczenia programowanie, ale jest dodawany nakładów pracy, które nie są optymalne dla urządzeń z ograniczeniami.  W takim przypadku należy wziąć pod uwagę przy użyciu interfejsów API klienta pierwotne i przeanalizować składni json przy użyciu analizatora lekkie takich jak [parson][lnk-parson].

#### <a name="use-the-lower-layer-ll"></a>Użyj dolnej warstwy (_LL_)
Zestaw SDK C obsługuje dwa modele programowania.  Jeden zestaw zawiera interfejsy API z _LL_ element infiksu, która oznacza dolnej warstwy.  Ten zestaw interfejsów API są wagi jaśniejsze i nie pokrętła zapasowej wątków roboczych, co oznacza, że użytkownik musi ręcznie kontrolować planowania.  Na przykład dla klienta urządzenia _LL_ interfejsów API znajduje się w tym [pliku nagłówka](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_device_client_ll.h).  Inny zestaw interfejsów API bez _LL_ indeksu jest nazywany warstwy wygody, gdy wątek roboczy jest wykonana automatycznie.  Na przykład warstwy wygody interfejsów API klienta urządzenia znajdują się w tym [pliku nagłówka](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_device_client.h).  Ograniczone urządzeń, gdzie każdy dodatkowego wątek może zająć znacznej procent zasobów systemowych, warto rozważyć użycie _LL_ interfejsów API.

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej o architekturze zestawu SDK usługi Azure IoT C:
-   [Kod źródłowy zestawu SDK C IoT Azure](https://github.com/Azure/azure-iot-sdk-c/)
-   [Azure urządzenia IoT SDK wprowadzenie C](https://docs.microsoft.com/azure/iot-hub/iot-hub-device-sdk-c-intro)

------
[lnk-cmake]: https://cmake.org/
[lnk-devbox-setup]:  https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md
[lnk-choosing-protocol]: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-protocols
[lnk-hub-file-upload]: https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-devguide-file-upload
[lnk-strip]: https://en.wikipedia.org/wiki/Strip_(Unix)
[lnk-serializer]: https://github.com/Azure/azure-iot-sdk-c/tree/master/serializer
[lnk-parson]: https://github.com/kgabis/parson