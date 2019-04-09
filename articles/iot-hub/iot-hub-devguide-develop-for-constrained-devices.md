---
title: Azure IoT Hub opracowywanie zawartości dla ograniczonego urządzeń przy użyciu zestawu SDK C usługi IoT Hub | Dokumentacja firmy Microsoft
description: Przewodnik dewelopera — wskazówki dotyczące sposobu tworzenia przy użyciu zestawów SDK usługi Azure IoT dla urządzeń z ograniczeniami.
author: yzhong94
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/24/2018
ms.author: yizhon
ms.openlocfilehash: 7788bca621a59ec8cdfe36edf73a99efca8c460c
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59261398"
---
# <a name="develop-for-constrained-devices-using-azure-iot-c-sdk"></a>Opracowywanie zawartości dla ograniczonego urządzeń przy użyciu zestawu SDK C usługi Azure IoT

Zestaw SDK usługi Azure IoT Hub C został napisany w ANSI C (C99), dzięki czemu dobrze nadaje się do obsługi wielu różnych platformach za pomocą niewielkie rozmiary dysku i pamięci. Zalecana ilość pamięci RAM jest co najmniej 64 KB, ale dokładna ilość pamięci zajmowaną zależy od protokół używany, liczba otwartych połączeń, a także platformy docelowej.
> [!NOTE]
> * Zestaw SDK C usługi Azure IoT regularnie publikuje informacje pomagające w rozwoju.  Odwiedź nasze [repozytorium GitHub](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/c_sdk_resource_information.md) i Przejrzyj najnowsze testów porównawczych.
>

Zestawu SDK języka C jest dostępna w pakiecie formularz za pomocą polecenia apt-get, NuGet i MBED. Pod kątem urządzeń ograniczone, można skompilować zestaw SDK lokalnie dla danej platformy docelowej. Tej dokumentacji pokazano, jak można usunąć niektórych funkcji, aby zmniejszyć zużycie przy użyciu zestawu SDK języka C [cmake](https://cmake.org/). Ponadto ta dokumentacja zawiera omówienie najlepszych praktyk modelami programowania do pracy z urządzeń z ograniczeniami.

## <a name="building-the-c-sdk-for-constrained-devices"></a>Tworzenie zestawu SDK języka C dla urządzeń z ograniczeniami

Tworzenie zestawu SDK języka C dla urządzeń z ograniczeniami.

### <a name="prerequisites"></a>Wymagania wstępne

Postępuj zgodnie z tym [Przewodnik instalacji zestawu SDK języka C](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) do przygotowania środowiska programowania do tworzenia zestawu SDK języka C. Przed zagłębieniem się krok do kompilowania za pomocą narzędzia cmake, można wywołać narzędzia cmake flagi, aby usunąć nieużywane funkcje.

### <a name="remove-additional-protocol-libraries"></a>Usuń biblioteki są dostępne dodatkowe protokoły

Zestawu SDK języka C obsługuje protokoły pięć dzisiaj: Protokołu MQTT protokołu MQTT za pośrednictwem protokołu WebSocket, AMQPs, AMQP przez WebSocket i HTTPS. Większość scenariuszy wymaga jednej do dwóch protokołów uruchomiony na komputerze klienckim, dlatego można usunąć biblioteki protokołu, które nie korzystają z zestawu SDK. Dodatkowe informacje o wybieraniu protokołu komunikacyjnego odpowiednie dla danego scenariusza można znaleźć w [wybierz protokół komunikacyjny usługi IoT Hub](iot-hub-devguide-protocols.md). Na przykład przekształca protokół MQTT to lekki protokół, który często lepiej jest odpowiedni dla urządzeń z ograniczeniami.

Możesz usunąć biblioteki protokołów AMQP oraz HTTP, używając następującego polecenia cmake:

```
cmake -Duse_amqp=OFF -Duse_http=OFF <Path_to_cmake>
```

### <a name="remove-sdk-logging-capability"></a>Usuń możliwość rejestrowania zestawu SDK

Zestawu SDK języka C zapewnia rozbudowane rejestrowanie w całym pomocy w debugowaniu. Możesz usunąć rejestrować urządzeń produkcyjnych przy użyciu następującego polecenia cmake:

```
cmake -Dno_logging=OFF <Path_to_cmake>
```

### <a name="remove-upload-to-blob-capability"></a>Usuń przekazać go do obiektu blob możliwości

Możesz przekazać dużych plików do usługi Azure Storage za pomocą wbudowanych możliwości w zestawie SDK. Usługa Azure IoT Hub działa jako wysyłający do skojarzonego konta usługi Azure Storage. Ta funkcja służy do przesyłania plików multimedialnych, telemetrii dużych partii i dzienniki. Więcej informacji można znaleźć [przekazywania plików z usługą IoT Hub](iot-hub-devguide-file-upload.md). Jeśli aplikacja nie wymaga tej funkcji, należy usunąć tę funkcję za pomocą następującego polecenia cmake:

```
cmake -Ddont_use_uploadtoblob=ON <Path_to_cmake>
```

### <a name="running-strip-on-linux-environment"></a>Usuń uruchomionych w środowisku systemu Linux

Jeśli pliki binarne uruchamiane w systemie Linux, możesz wykorzystać [paska poleceń](https://en.wikipedia.org/wiki/Strip_(Unix)) Aby zmniejszyć rozmiar końcowy aplikacji po kompilacji.

```
strip -s <Path_to_executable>
```

## <a name="programming-models-for-constrained-devices"></a>Modele programowania dla urządzeń z ograniczeniami

Następnie obejrzyj modelami programowania do urządzeń z ograniczeniami.

### <a name="avoid-using-the-serializer"></a>Unikaj korzystania z serializatora

Zestaw SDK języka C ma opcjonalny [serializator zestawu SDK języka C](https://github.com/Azure/azure-iot-sdk-c/tree/master/serializer), co pozwala na korzystanie z tabel deklaratywne mapowania do definiowania metod i właściwości bliźniaczych reprezentacji urządzeń. Serializator zaprojektowano w celu uproszczenie projektowania aplikacji, ale powoduje dodanie obciążenie, które nie są optymalne dla urządzeń z ograniczeniami. W takim przypadku należy wziąć pod uwagę przy użyciu interfejsów API klienta pierwotnych i Przeanalizuj dane JSON przy użyciu analizatora uproszczonego, takiego jak [parson](https://github.com/kgabis/parson).

### <a name="use-the-lower-layer-ll"></a>Użyj niższej warstwie (_LL_)

Zestaw SDK języka C obsługuje dwa modele programowania. Jeden zestaw ma interfejsów API za pomocą _LL_ wrostkowe, które mają wpływ na jakość do niższej warstwy. Ten zestaw interfejsów API jest jaśniejszy wagi i nie uruchamiaj wątków roboczych, co oznacza, że użytkownik musi ręcznie kontrolować planowania. Na przykład dla klienta urządzenia _LL_ interfejsów API można znaleźć w tym [plik nagłówkowy](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_device_client_ll.h). 

Inny zestaw interfejsów API bez _LL_ indeksu jest wywoływana warstwy wygodne, gdy wątek roboczy zostanie automatycznie rozszerzona. Na przykład warstwa wygody interfejsów API klienta urządzenia, można znaleźć w tym [plik nagłówkowy klienta urządzenia IoT](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_device_client.h). Dla urządzeń ograniczone, gdzie każdy dodatkowy wątek może zająć znaczną procent zasobów systemowych, należy wziąć pod uwagę przy użyciu _LL_ interfejsów API.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat architektury zestawu SDK C usługi Azure IoT:
-   [Kod źródłowy zestawu SDK C usługi IoT platformy Azure](https://github.com/Azure/azure-iot-sdk-c/)
-   [Azure zestaw SDK urządzeń IoT dla języka C wprowadzenie](iot-hub-device-sdk-c-intro.md)
