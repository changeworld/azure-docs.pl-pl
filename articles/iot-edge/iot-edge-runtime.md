---
title: Dowiedz się, jak środowisko uruchomieniowe zarządza urządzeniami — usługi Azure IoT Edge | Dokumentacja firmy Microsoft
description: Dowiedz się, jak środowisko uruchomieniowe IoT Edge zarządza modułami, zabezpieczeniami, komunikacją i raportowaniem na urządzeniach
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/01/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c184972789c412406f264f725f8b94e1f7f162ce
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76547051"
---
# <a name="understand-the-azure-iot-edge-runtime-and-its-architecture"></a>Omówienie środowiska uruchomieniowego usługi Azure IoT Edge oraz jej architektury

Środowisko uruchomieniowe IoT Edge to zbiór programów, które włączają urządzenie do urządzenia IoT Edge. Zbiorowo składniki środowiska uruchomieniowego IoT Edge umożliwiają IoT Edge urządzeniom otrzymywanie kodu do uruchomienia na brzegu i przekazywanie wyników.

Środowisko uruchomieniowe IoT Edge jest odpowiedzialne za następujące funkcje na urządzeniach IoT Edge:

* Instaluje i aktualizuje pakiety robocze na urządzeniu.
* Utrzymuje standardy zabezpieczeń usługi Azure IoT Edge na urządzeniu.
* Upewnij się, że [moduły IoT Edge](iot-edge-modules.md) są zawsze uruchomione.
* Przesyła raporty o kondycji modułów do chmury na potrzeby zdalnego monitorowania.
* Zarządzanie komunikacją między urządzeniami podrzędnymi a urządzeniami IoT Edge.
* Zarządzanie komunikacją między modułami na urządzeniu IoT Edge.
* Zarządzanie komunikacją między urządzeniem IoT Edge i chmurą.

![Środowisko uruchomieniowe komunikuje się szczegółowe informacje i kondycji modułów do usługi IoT Hub](./media/iot-edge-runtime/Pipeline.png)

Obowiązki środowiska uruchomieniowego usługi IoT Edge można podzielić na dwie kategorie: Zarządzanie komunikacji i moduł. Te dwie role są wykonywane przez dwa składniki, które są częścią środowiska uruchomieniowego IoT Edge. *Centrum IoT Edge* jest odpowiedzialne za komunikację, a *Agent IoT Edge* wdraża i monitoruje moduły.

Zarówno centrum IoT Edge, jak i Agent IoT Edge są modułami, podobnie jak każdy inny moduł uruchomiony na IoT Edge urządzeniu. Są one czasami określane jako *moduły środowiska uruchomieniowego*.

## <a name="iot-edge-hub"></a>Centrum usługi IoT Edge

IoT Edge Hub to jeden z dwóch modułów, które tworzą Azure IoT Edge środowiska uruchomieniowego. Działa ona jako lokalny serwer proxy dla usługi IoT Hub, zapewniając tymi samymi punktami końcowymi protokołu, jak usługa IoT Hub. Ten spójności oznacza, że klienci (czy urządzeń lub moduły) mogą łączyć się z środowiska uruchomieniowego usługi IoT Edge tak samo, jak do usługi IoT Hub.

>[!NOTE]
> IoT Edge Hub obsługuje klientów, którzy łączą się za pomocą MQTT lub AMQP. Nie obsługuje klientów korzystających z protokołu HTTP.

Centrum IoT Edge nie jest pełną wersją IoT Hub uruchomioną lokalnie. IoT Edge Hub dyskretnie delegowanie niektórych zadań do IoT Hub. Na przykład IoT Edge centrum przekazuje żądania uwierzytelniania do IoT Hub, gdy urządzenie próbuje nawiązać połączenie. Po ustanowieniu pierwszego połączenia informacje o zabezpieczeniach są buforowane lokalnie przez IoT Edge centrum. Przyszłe połączenia z tego urządzenia są dozwolone bez konieczności ponownego uwierzytelniania w chmurze.

Aby zmniejszyć przepustowość używaną przez rozwiązanie IoT Edge, centrum IoT Edge optymalizuje, ile rzeczywistych połączeń odbywa się w chmurze. Usługa IoT Edge Hub pobiera logiczne połączenia z modułów lub urządzeń podrzędnych oraz łączy je z jednym połączeniem fizycznym z chmurą. Szczegóły tego procesu są niewidoczne w pozostałej części rozwiązania. Klienci wydaje się, że mają oni własne połączenia do chmury, nawet jeśli te są wszystkie są wysyłane za pośrednictwem tego samego połączenia.

![IoT Edge Hub jest bramą między urządzeniami fizycznymi i IoT Hub](./media/iot-edge-runtime/Gateway.png)

IoT Edge Hub może określić, czy jest on połączony z IoT Hub. W przypadku utraty połączenia IoT Edge centrum zapisuje na komputerze aktualizacje lub sznurki. Po ustanowieniu połączenia, synchronizuje wszystkie dane. Lokalizacja używana dla tymczasowej pamięci podręcznej jest określana przez właściwość sznurka modułu IoT Edge Hub. Rozmiar pamięci podręcznej nie jest ograniczone i będzie się zwiększać tak długo, jak urządzenie ma pojemność magazynu. Aby uzyskać więcej informacji, zobacz [możliwości trybu offline](offline-capabilities.md).

### <a name="module-communication"></a>Moduł komunikacji

IoT Edge Hub ułatwia komunikację modułu z modułem. Używanie Centrum IoT Edge jako brokera komunikatów zachowuje moduły niezależne od siebie. Moduły muszą tylko określić danych wejściowych, które akceptują wiadomości i danych wyjściowych, do których one zapisywania komunikatów. Programista rozwiązań może połączyć te dane wejściowe i dane wyjściowe, aby moduły przetwarzali danych w kolejności specyficznej dla tego rozwiązania.

![IoT Edge Hub ułatwia komunikację między modułami](./media/iot-edge-runtime/module-endpoints.png)

Aby wysłać dane do centrum IoT Edge, moduł wywołuje metodę SendEventAsync. Pierwszy argument określa, w której dane wyjściowe, aby wysłać wiadomość. Następujący pseudokodzie wysyła komunikat w **output1**:

   ```csharp
   ModuleClient client = await ModuleClient.CreateFromEnvironmentAsync(transportSettings);
   await client.OpenAsync();
   await client.SendEventAsync("output1", message);
   ```

Aby komunikat o błędzie, należy zarejestrować wywołanie zwrotne, które przetwarza komunikaty pochodzące na określone dane wejściowe. Następujący pseudokodzie rejestruje funkcję messageProcessor, która ma być używana do przetwarzania wszystkich komunikatów odebranych w **INPUT1**:

   ```csharp
   await client.SetInputMessageHandlerAsync("input1", messageProcessor, userContext);
   ```

Aby uzyskać więcej informacji na temat klasy ModuleClient i jej metod komunikacji, zobacz Dokumentacja interfejsu API dla preferowanego języka SDK [C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet):, [C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h), [Python](https://docs.microsoft.com/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient?view=azure-python), [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable)lub [Node. js](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest).

Deweloper rozwiązania jest odpowiedzialny za określenie reguł, które określają, jak centrum IoT Edge przekazuje komunikaty między modułami. Reguły routingu są zdefiniowane w chmurze i wypychane do IoT Edge Hub w swoim sznurze modułu. Tej samej składni dla tras usługi IoT Hub jest używane do definiowania trasy między modułami w usłudze Azure IoT Edge. Aby uzyskać więcej informacji, zobacz [Informacje o sposobie wdrażania modułów i ustanawiania tras w programie IoT Edge](module-composition.md).

![Trasy między modułami przechodzą przez Centrum IoT Edge](./media/iot-edge-runtime/module-endpoints-with-routes.png)

## <a name="iot-edge-agent"></a>Agent usługi IoT Edge

Agent usługi IoT Edge jest inny moduł, który tworzy środowisko uruchomieniowe usługi Azure IoT Edge. Jest odpowiedzialny za utworzenie wystąpienia modułów, zapewnienie, że nadal działać i raportowania stanu modułów powrót do Centrum IoT Hub. Te dane konfiguracyjne są zapisywane jako właściwości sznurka modułu agenta IoT Edge.

[Demon zabezpieczenia IoT Edge](iot-edge-security-manager.md) uruchamia agenta IoT Edge przy uruchamianiu urządzenia. Agent pobiera jego bliźniaczą reprezentację modułu z usługi IoT Hub i sprawdza manifestu wdrażania. Manifest wdrożenia to plik JSON, która deklaruje moduły, które musiały zostać uruchomione.

Każdy element w manifeście wdrożenia zawiera określone informacje o module i jest używany przez agenta IoT Edge do kontrolowania cyklu życia modułu. Bardziej interesujące właściwości, należą:

* **Settings. Image** — obraz kontenera, którego używa Agent IoT Edge do uruchomienia modułu. Jeśli obraz jest chroniony hasłem, Agent IoT Edge musi być skonfigurowany przy użyciu poświadczeń dla rejestru kontenerów. Poświadczenia dla rejestru kontenerów można skonfigurować zdalnie przy użyciu manifestu wdrażania lub na IoT Edge samym urządzeniu, aktualizując plik `config.yaml` w folderze programu IoT Edge.
* **Settings. SetOptions** — ciąg, który jest przesyłany bezpośrednio do demona kontenera Moby podczas uruchamiania kontenera modułu. Dodanie opcji w tej właściwości pozwala na zaawansowane konfiguracje, takie jak przekazywanie portów lub instalowanie woluminów do kontenera modułu.  
* **status** — stan, w którym Agent IoT Edge umieszcza moduł. Zazwyczaj ta wartość jest ustawiana jako *uruchomiona* , gdy większość osób chce, aby Agent IoT Edge natychmiast uruchomił wszystkie moduły na urządzeniu. Można jednak określić początkowy stan modułu, który ma zostać zatrzymany, i poczekać na przyszły czas, aby poinformować IoT Edge agenta o konieczności uruchomienia modułu. Agent IoT Edge raportuje stan każdego modułu z powrotem do chmury w raportowanych właściwościach. Różnica między żądaną właściwość i zgłaszanych właściwości jest wskaźnikiem nieprawidłowo funkcjonujące urządzenia. Są obsługiwane stany:

  * Pobieranie
  * Działanie
  * W złej kondycji
  * Niepowodzenie
  * Zatrzymane

* **restartPolicy** — sposób ponownego uruchomienia modułu przez agenta IoT Edge. Możliwe wartości obejmują:
  
  * `never` — Agent IoT Edge nigdy nie uruchamia ponownie modułu.
  * `on-failure` — Jeśli moduł ulegnie awarii, Agent IoT Edge ponownie go uruchomi. Jeśli moduł nie zostanie prawidłowo zamknięty, Agent IoT Edge nie uruchomi go ponownie.
  * `on-unhealthy` — Jeśli moduł ulegnie awarii lub jest uznawany za nieprawidłowy, Agent IoT Edge ponownie go uruchomi.
  * `always` — Jeśli moduł ulegnie awarii, jest uznawany za w złej kondycji lub zamknięty w dowolny sposób, Agent IoT Edge ponownie go uruchomi.

* **imagePullPolicy** — czy Agent IoT Edge próbuje pobrać najnowszy obraz dla modułu automatycznie. Jeśli nie określisz wartości, wartość domyślna to *OnCreate*. Możliwe wartości obejmują:

  * `on-create` — podczas uruchamiania modułu lub aktualizowania modułu na podstawie nowego manifestu wdrożenia Agent IoT Edge podejmie próbę ściągnięcia obrazu modułu z rejestru kontenerów.
  * `never` — Agent IoT Edge nigdy nie podejmie próby ściągnięcia obrazu modułu z rejestru kontenerów. W przypadku tej konfiguracji użytkownik jest odpowiedzialny za pobieranie obrazu modułu na urządzenie i zarządzanie wszelkimi aktualizacjami obrazu.

Agent usługi IoT Edge wysyła odpowiedź środowiska uruchomieniowego do usługi IoT Hub. Oto lista możliwych odpowiedzi:
  
* 200 — OK
* 400 - Konfiguracja wdrożenia jest źle sformułowany lub nieprawidłowy.
* 417 — urządzenie nie ma ustawionej konfiguracji wdrożenia.
* 412 — wersja schematu w konfiguracji wdrożenia jest nieprawidłowa.
* 406 — urządzenie IoT Edge jest w trybie offline lub nie wysyła raportów o stanie.
* 500 — Wystąpił błąd w czasie wykonywania IoT Edge.

Aby uzyskać więcej informacji, zobacz [Informacje o sposobie wdrażania modułów i ustanawiania tras w programie IoT Edge](module-composition.md).

### <a name="security"></a>Zabezpieczenia

Agent usługi IoT Edge odgrywa kluczową rolę w zabezpieczeniach urządzenia usługi IoT Edge. Na przykład wykonuje akcje, takie jak weryfikacja do obrazu modułu przed jego uruchomienie.

Aby uzyskać więcej informacji na temat środowiska zabezpieczeń Azure IoT Edge, Przeczytaj o programie [IoT Edge Security Manager](iot-edge-security-manager.md).

## <a name="next-steps"></a>Następne kroki

[Omówienie modułów usługi Azure IoT Edge](iot-edge-modules.md)
