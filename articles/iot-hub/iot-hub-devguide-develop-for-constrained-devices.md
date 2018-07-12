---
title: Usługa Azure IoT Hub opracowywanie zawartości dla urządzeń z ograniczeniami | Dokumentacja firmy Microsoft
description: Przewodnik dewelopera — wskazówki dotyczące sposobu tworzenia przy użyciu zestawów SDK usługi Azure IoT dla urządzeń z ograniczeniami.
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
ms.openlocfilehash: 15fc5794c71428b5fb1036060af3e9c4a6890f4f
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/12/2018
ms.locfileid: "38969065"
---
# <a name="develop-for-constrained-devices-using-azure-iot-sdks"></a>Opracowywanie zawartości dla ograniczonego urządzeń przy użyciu zestawów SDK usługi Azure IoT

## <a name="develop-using-azure-iot-hub-c-sdk"></a>Programowanie przy użyciu zestawu SDK usługi Azure IoT Hub C
Zestaw SDK usługi Azure IoT Hub C został napisany w ANSI C (C99), dzięki czemu dobrze nadaje się do obsługi wielu różnych platformach za pomocą niewielkie rozmiary dysku i pamięci.  Zalecana ilość pamięci RAM jest co najmniej 64 KB, ale dokładna ilość pamięci zajmowaną zależy od protokół używany, liczba otwartych połączeń, a także platformy docelowej.

Zestawu SDK języka C jest dostępna w pakiecie formularz za pomocą polecenia apt-get, NuGet i MBED.  Pod kątem urządzeń ograniczone, można skompilować zestaw SDK lokalnie dla danej platformy docelowej. Tej dokumentacji pokazano, jak można usunąć niektórych funkcji, aby zmniejszyć zużycie przy użyciu zestawu SDK języka C [cmake][lnk-cmake].  Ponadto ta dokumentacja zawiera omówienie najlepszych praktyk modelami programowania do pracy z urządzeń z ograniczeniami.

### <a name="building-the-c-sdk-for-constrained-devices"></a>Tworzenie zestawu SDK języka C dla urządzeń z ograniczeniami
#### <a name="prerequisites"></a>Wymagania wstępne
Postępuj zgodnie z tym [przewodnik konfiguracji] [ lnk-devbox-setup] do przygotowania środowiska programowania do tworzenia zestawu SDK języka C.  Przed zagłębieniem się krok do kompilowania za pomocą narzędzia cmake, można wywołać narzędzia cmake flagi, aby usunąć nieużywane funkcje.

#### <a name="remove-additional-protocol-libraries"></a>Usuń biblioteki są dostępne dodatkowe protokoły
Zestawu SDK języka C obsługuje protokoły pięć dzisiaj: MQTT, MQTT za pośrednictwem protokołu WebSocket, AMQPs, AMQP przez WebSocket i HTTPS.    Większość scenariuszy wymaga jednej do dwóch protokołów uruchomiony na komputerze klienckim, dlatego można usunąć biblioteki protokołu, które nie korzystają z zestawu SDK.  Dodatkowe informacje o wybieraniu protokołu komunikacyjnego odpowiednie dla danego scenariusza można znaleźć w tym [dokumentu][lnk-choosing-protocol].  Na przykład przekształca protokół MQTT to lekki protokół, który często lepiej jest odpowiedni dla urządzeń z ograniczeniami.

Możesz usunąć protokołów AMQP oraz HTTP bibliotek, korzystając z następującego polecenia cmake:
```
cmake -Duse_amqp=OFF -Duse_http=OFF <Path_to_cmake>
```

#### <a name="remove-sdk-logging-capability"></a>Usuń możliwość rejestrowania zestawu SDK
Zestawu SDK języka C zapewnia rozbudowane rejestrowanie w całym pomocy w debugowaniu. Możesz usunąć rejestrować urządzeń produkcyjnych przy użyciu następującego polecenia cmake:
```
cmake -Dno_logging=OFF <Path_to_cmake>
```

#### <a name="remove-upload-to-blob-capability"></a>Usuń przekazać go do obiektu blob możliwości
Możesz przekazać dużych plików do usługi Azure Storage za pomocą wbudowanych możliwości w zestawie SDK.  Usługa Azure IoT Hub działa jako wysyłający do skojarzonego konta usługi Azure Storage.  Ta funkcja służy do przesyłania plików multimedialnych, telemetrii dużych partii i dzienniki.  Dowiedz się więcej na temat przekazywania plików z usługą IoT Hub, w tym [dokumentu][lnk-hub-file-upload].  Jeśli aplikacja nie wymaga tej funkcji, należy usunąć tę funkcję za pomocą następującego polecenia cmake:
```
cmake -Ddont_use_uploadtoblob=ON <Path_to_cmake>
```
#### <a name="running-strip-on-linux-environment"></a>Usuń uruchomionych w środowisku systemu Linux
Jeśli pliki binarne uruchamiane w systemie Linux, możesz wykorzystać [paska poleceń] [ lnk-strip] Aby zmniejszyć rozmiar końcowy aplikacji po kompilacji.
```
strip -s <Path_to_executable>
```

### <a name="programming-models-for-constrained-devices"></a>Modele programowania dla urządzeń z ograniczeniami
#### <a name="avoid-using-the-serializer"></a>Unikaj korzystania z serializatora
Zestaw SDK języka C ma opcjonalny [serializator][lnk-serializer], co pozwala na korzystanie z tabel deklaratywne mapowania do definiowania metod i właściwości bliźniaczych reprezentacji urządzeń.  Serializator zaprojektowano w celu uproszczenie projektowania aplikacji, ale powoduje dodanie obciążenie, które nie są optymalne dla urządzeń z ograniczeniami.  W takim przypadku należy wziąć pod uwagę przy użyciu interfejsów API klienta pierwotnych i Przeanalizuj dane json przy użyciu analizatora uproszczonego, takiego jak [parson][lnk-parson].

#### <a name="use-the-lower-layer-ll"></a>Użyj niższej warstwie (_LL_)
Zestaw SDK języka C obsługuje dwa modele programowania.  Jeden zestaw ma interfejsów API za pomocą _LL_ wrostkowe, które mają wpływ na jakość do niższej warstwy.  Ten zestaw interfejsów API są mniejsza waga i nie uruchamiaj wątków roboczych, co oznacza, że użytkownik musi ręcznie kontrolować planowania.  Na przykład dla klienta urządzenia _LL_ interfejsów API można znaleźć w tym [plik nagłówkowy](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_device_client_ll.h).  Inny zestaw interfejsów API bez _LL_ indeksu jest wywoływana warstwy wygodne, gdy wątek roboczy zostanie automatycznie rozszerzona.  Na przykład warstwa wygody interfejsów API klienta urządzenia, można znaleźć w tym [plik nagłówkowy](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_device_client.h).  Dla urządzeń ograniczone, gdzie każdy dodatkowy wątek może zająć znaczną procent zasobów systemowych, należy wziąć pod uwagę przy użyciu _LL_ interfejsów API.

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat architektury zestawu SDK C usługi Azure IoT:
-   [Kod źródłowy zestawu SDK C usługi IoT platformy Azure](https://github.com/Azure/azure-iot-sdk-c/)
-   [Azure zestaw SDK urządzeń IoT dla języka C wprowadzenie](https://docs.microsoft.com/azure/iot-hub/iot-hub-device-sdk-c-intro)

------
[lnk-cmake]: https://cmake.org/
[lnk-devbox-setup]:  https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md
[lnk-choosing-protocol]: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-protocols
[lnk-hub-file-upload]: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-file-upload
[lnk-strip]: https://en.wikipedia.org/wiki/Strip_(Unix)
[lnk-serializer]: https://github.com/Azure/azure-iot-sdk-c/tree/master/serializer
[lnk-parson]: https://github.com/kgabis/parson