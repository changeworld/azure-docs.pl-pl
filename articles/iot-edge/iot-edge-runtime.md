---
title: Dowiedz się, jak środowisko uruchomieniowe zarządza urządzeniami — usługi Azure IoT Edge | Dokumentacja firmy Microsoft
description: Dowiedz się, jak moduły, zabezpieczeń, komunikacji i raportowanie na urządzeniach, które są zarządzane przez środowisko uruchomieniowe usługi Azure IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/06/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 423825540c02d2788de7a6148ddcec3c654fd450
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2019
ms.locfileid: "66754793"
---
# <a name="understand-the-azure-iot-edge-runtime-and-its-architecture"></a>Omówienie środowiska uruchomieniowego usługi Azure IoT Edge oraz jej architektury

Środowisko uruchomieniowe usługi IoT Edge to kolekcja programów, które Włącz urządzenie do urządzenia usługi IoT Edge. Zbiorczo składniki środowiska uruchomieniowego usługi IoT Edge włączyć urządzenia usługi IoT Edge otrzymać kod wymagany do uruchomienia na urządzeniach brzegowych i przesyłanie ich wyników. 

Środowisko uruchomieniowe usługi IoT Edge wykonuje następujące funkcje na urządzeniach IoT Edge:

* Instaluje i aktualizuje pakiety robocze na urządzeniu.
* Utrzymuje standardy zabezpieczeń usługi Azure IoT Edge na urządzeniu.
* Upewnij się, że [moduły usługi IoT Edge](iot-edge-modules.md) nieprzerwane działanie.
* Przesyła raporty o kondycji modułów do chmury na potrzeby zdalnego monitorowania.
* Usprawnij komunikację między podrzędnymi urządzeniami liścia a urządzenia usługi IoT Edge.
* Usprawnij komunikację między modułami na urządzeniu usługi IoT Edge.
* Usprawnij komunikację między chmurą a urządzeniem usługi IoT Edge.

![Środowisko uruchomieniowe komunikuje się szczegółowe informacje i kondycji modułów do usługi IoT Hub](./media/iot-edge-runtime/Pipeline.png)

Obowiązki środowiska uruchomieniowego usługi IoT Edge można podzielić na dwie kategorie: Zarządzanie komunikacji i moduł. Te dwie role są wykonywane przez dwa składniki, które są częścią środowiska uruchomieniowego usługi IoT Edge. *Centrum usługi IoT Edge* jest odpowiedzialny za komunikację, podczas gdy *agenta usługi IoT Edge* służy do wdrażania i monitoruje modułów. 

Zarówno Centrum IoT Edge, jak i agent usługi IoT Edge są moduły, podobnie jak każdy inny moduł, działające na urządzeniu usługi IoT Edge. 

## <a name="iot-edge-hub"></a>Centrum usługi IoT Edge

Centrum usługi IoT Edge jest jednym z dwóch modułów, które tworzą środowisko uruchomieniowe usługi Azure IoT Edge. Działa ona jako lokalny serwer proxy dla usługi IoT Hub, zapewniając tymi samymi punktami końcowymi protokołu, jak usługa IoT Hub. Ten spójności oznacza, że klienci (czy urządzeń lub moduły) mogą łączyć się z środowiska uruchomieniowego usługi IoT Edge tak samo, jak do usługi IoT Hub. 

>[!NOTE]
> Centrum usługi IoT Edge obsługuje klientów łączących się przy użyciu protokołu MQTT lub AMQP. Nie obsługuje klientów korzystających z protokołu HTTP. 

Centrum usługi IoT Edge nie jest pełną wersję usługi IoT Hub działa lokalnie. Istnieje kilka kwestii, które Centrum usługi IoT Edge dyskretnie deleguje odpowiednie uprawnienia do usługi IoT Hub. Na przykład Centrum usługi IoT Edge przekazuje żądania uwierzytelnienia do usługi IoT Hub, gdy urządzenie po raz pierwszy próbuje połączyć. Po pierwszym nawiązaniu połączenia informacji o zabezpieczeniach są buforowane lokalnie przez Centrum usługi IoT Edge. Kolejnych połączeń z tego urządzenia są dozwolone bez konieczności uwierzytelniania w chmurze. 

W celu zmniejszenia obciążenia przepustowości rozwiązania usługi IoT Edge korzysta, Centrum usługi IoT Edge optymalizuje, jak wiele rzeczywistych połączenia są nawiązywane z chmurą. Centrum usługi IoT Edge przyjmuje logiczny połączenia od klientów, takich jak moduły lub urządzeniami liścia i łączy je do jednego fizycznego połączenia z chmurą. Szczegóły tego procesu są niewidoczne w pozostałej części rozwiązania. Klienci wydaje się, że mają oni własne połączenia do chmury, nawet jeśli te są wszystkie są wysyłane za pośrednictwem tego samego połączenia. 

![Centrum usługi IoT Edge jest brama między urządzeniami fizycznymi i IoT Hub](./media/iot-edge-runtime/Gateway.png)

Centrum usługi IoT Edge można określić, czy jest ona dołączona do usługi IoT Hub. W przypadku utraty połączenia Centrum IoT Edge zapisuje komunikaty lub lokalnie w aktualizacji bliźniaczej reprezentacji. Po ustanowieniu połączenia, synchronizuje wszystkie dane. Lokalizacja używana dla tego tymczasowa pamięć podręczna jest określany przez właściwość z bliźniaczej reprezentacji modułu usługi IoT Edge hub. Rozmiar pamięci podręcznej nie jest ograniczone i będzie się zwiększać tak długo, jak urządzenie ma pojemność magazynu. Aby uzyskać więcej informacji, zobacz [możliwości w trybie Offline](offline-capabilities.md).

### <a name="module-communication"></a>Moduł komunikacji

Centrum usługi IoT Edge usprawnia komunikację na moduł. Za pomocą usługi IoT Edge hub jako broker komunikatów przechowuje moduły niezależni od siebie nawzajem. Moduły muszą tylko określić danych wejściowych, które akceptują wiadomości i danych wyjściowych, do których one zapisywania komunikatów. Rozwiązanie dla deweloperów można łączyć te dane wejściowe i wyjściowe, aby moduły przetwarzać dane w kolejności, które są specyficzne dla tego rozwiązania. 

![Usługi IoT Edge Hub usprawnia komunikację na moduł](./media/iot-edge-runtime/module-endpoints.png)

Aby wysyłać dane do Centrum IoT Edge, moduł wywołuje metodę SendEventAsync. Pierwszy argument określa, w której dane wyjściowe, aby wysłać wiadomość. Poniższym pseudokodzie wysyła komunikat **output1**:

   ```csharp
   ModuleClient client = new ModuleClient.CreateFromEnvironmentAsync(transportSettings); 
   await client.OpenAsync(); 
   await client.SendEventAsync(“output1”, message); 
   ```

Aby komunikat o błędzie, należy zarejestrować wywołanie zwrotne, które przetwarza komunikaty pochodzące na określone dane wejściowe. Poniższym pseudokodzie rejestruje messageProcessor funkcji, które ma być używany do przetwarzania wszystkich komunikatów odebranych w **wejście1**:

   ```csharp
   await client.SetInputMessageHandlerAsync(“input1”, messageProcessor, userContext);
   ```

Aby uzyskać więcej informacji na temat klasy ModuleClient i jego metod komunikacji zobacz dokumentacja interfejsu API preferowanego języka zestawu SDK: [C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet), [C i Python](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h), [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable), lub [Node.js](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest).

Rozwiązanie dla deweloperów jest odpowiedzialny za określenie reguł, które określają, jak Centrum usługi IoT Edge przekazuje komunikatów między modułami. Reguły routingu są zdefiniowane w chmurze i przekazywany do Centrum IoT Edge w jego bliźniaczej reprezentacji urządzenia. Tej samej składni dla tras usługi IoT Hub jest używane do definiowania trasy między modułami w usłudze Azure IoT Edge. Aby uzyskać więcej informacji, zobacz [Dowiedz się, jak wdrażać moduły oraz określenia trasy w usługi IoT Edge](module-composition.md).   

![Trasy między modułami przechodzą przez Centrum usługi IoT Edge](./media/iot-edge-runtime/module-endpoints-with-routes.png)

## <a name="iot-edge-agent"></a>Agent usługi IoT Edge

Agent usługi IoT Edge jest inny moduł, który tworzy środowisko uruchomieniowe usługi Azure IoT Edge. Jest odpowiedzialny za utworzenie wystąpienia modułów, zapewnienie, że nadal działać i raportowania stanu modułów powrót do Centrum IoT Hub. Podobnie jak każdy inny moduł agent usługi IoT Edge korzysta z jego bliźniaczą reprezentację modułu do przechowywania danych konfiguracji. 

[Demona zabezpieczeń usługi IoT Edge](iot-edge-security-manager.md) uruchamia agenta usługi IoT Edge podczas uruchamiania urządzenia. Agent pobiera jego bliźniaczą reprezentację modułu z usługi IoT Hub i sprawdza manifestu wdrażania. Manifest wdrożenia to plik JSON, która deklaruje moduły, które musiały zostać uruchomione. 

Każdego elementu w manifeście wdrożenia zawiera szczegółowe informacje na temat modułu i jest używane przez agenta usługi IoT Edge do kontrolowania modułu cyklu życia. Bardziej interesujące właściwości, należą: 

* **Settings.Image** — obraz kontenera, który korzysta z agenta usługi IoT Edge można uruchomić modułu. Agent usługi IoT Edge należy określić przy użyciu poświadczeń dla rejestru kontenerów, jeśli obraz, który jest chroniony hasłem. Poświadczenia dla rejestru kontenerów można skonfigurować przy użyciu pliku manifestu wdrożenia lub na samym urządzeniu usługi IoT Edge, aktualizując `config.yaml` pliku w folderze programu usługi IoT Edge.
* **settings.createOptions** — ciąg, który jest przekazywana bezpośrednio do demona kontenera Moby podczas uruchamiania modułu kontenera. Dodanie opcji w tej właściwości umożliwia zaawansowane konfiguracje, takie jak port przekazywania lub instalowanie woluminów do modułu kontenera.  
* **Stan** — stan, w którym agent usługi IoT Edge umieszcza modułu. Zazwyczaj ta wartość jest równa *systemem* dowolną większość osób agenta usługi IoT Edge, aby natychmiast uruchomić wszystkie moduły na urządzeniu. Jednak można określić stanu początkowego modułu, aby zostać zatrzymane i poczekaj na czas w przyszłości mówić agenta usługi IoT Edge, które można uruchomić modułu. Agent usługi IoT Edge raportuje stan każdego modułu ją do chmury w zgłaszanych właściwości. Różnica między żądaną właściwość i zgłaszanych właściwości jest wskaźnikiem nieprawidłowo funkcjonujące urządzenia. Są obsługiwane stany:
   * Pobieranie
   * Działanie
   * W złej kondycji
   * Niepowodzenie
   * Zatrzymano
* **restartPolicy** — w jaki sposób moduł powoduje ponowne uruchomienie agenta usługi IoT Edge. Możliwe wartości obejmują:
   * `never` — Agenta usługi IoT Edge nigdy nie uruchamia ponownie moduł.
   * `on-failure` — Jeśli moduł ulegnie awarii, agent usługi IoT Edge ponownie go uruchamia. Jeśli moduł, który zamyka się prawidłowo, agent usługi IoT Edge nie uruchom go ponownie.
   * `on-unhealthy` — Jeśli moduł ulegnie awarii lub jest określana jako zła, agent usługi IoT Edge ponownie go uruchamia.
   * `always` — Jeśli moduł ulega awarii, jest uznawana za złą lub zamyka w jakikolwiek sposób, agent usługi IoT Edge ponownie go uruchamia. 

Agent usługi IoT Edge wysyła odpowiedź środowiska uruchomieniowego do usługi IoT Hub. Oto lista możliwych odpowiedzi:
  * 200 — OK
  * 400 - Konfiguracja wdrożenia jest źle sformułowany lub nieprawidłowy.
  * 417 — urządzenie nie ma konfiguracji wdrożenia, ustawić.
  * 412 — wersja schematu w konfiguracji wdrożenia jest nieprawidłowa.
  * 406 — urządzenie usługi IoT Edge jest w trybie offline lub nie wysyła raporty.
  * 500 — Błąd wystąpił w środowisku uruchomieniowym usługi IoT Edge.

Aby uzyskać więcej informacji, zobacz [Dowiedz się, jak wdrażać moduły oraz określenia trasy w usługi IoT Edge](module-composition.md).   

### <a name="security"></a>Bezpieczeństwo

Agent usługi IoT Edge odgrywa kluczową rolę w zabezpieczeniach urządzenia usługi IoT Edge. Na przykład wykonuje akcje, takie jak weryfikacja do obrazu modułu przed jego uruchomienie. 

Aby uzyskać więcej informacji na temat struktury zabezpieczeń usługi Azure IoT Edge, przeczytaj temat [Menedżera zabezpieczeń usługi IoT Edge](iot-edge-security-manager.md).

## <a name="next-steps"></a>Kolejne kroki

[Omówienie modułów usługi Azure IoT Edge](iot-edge-modules.md)
