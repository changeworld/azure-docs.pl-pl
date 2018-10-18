---
title: Omówienie środowiska uruchomieniowego usługi Azure IoT Edge | Dokumentacja firmy Microsoft
description: Więcej informacji na temat środowiska uruchomieniowego usługi Azure IoT Edge i jak go z możliwości urządzenia brzegowe
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 08/13/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 97a2180aaf236d3541cff30d2151f26ce70b14af
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2018
ms.locfileid: "49393478"
---
# <a name="understand-the-azure-iot-edge-runtime-and-its-architecture"></a>Omówienie środowiska uruchomieniowego usługi Azure IoT Edge oraz jej architektury

Środowisko uruchomieniowe usługi IoT Edge to kolekcja programów, które muszą być zainstalowane na urządzeniu dla niego wziąć pod uwagę urządzenia usługi IoT Edge. Zbiorczo składników środowiska uruchomieniowego usługi IoT Edge włączyć urządzenia usługi IoT Edge otrzymać kod wymagany do uruchomienia na urządzeniach brzegowych i przesyłanie ich wyników. 

Środowisko uruchomieniowe usługi IoT Edge wykonuje następujące funkcje na urządzeniach IoT Edge:

* Instaluje i aktualizuje obciążenia na urządzeniu.
* Zapewnia zachowanie standardów zabezpieczeń usługi Azure IoT Edge na urządzeniu.
* Zapewnia, że [moduły usługi IoT Edge](iot-edge-modules.md) nieprzerwane działanie.
* Przesyła raporty o kondycji modułów do chmury na potrzeby zdalnego monitorowania.
* Usprawnia komunikację między podrzędnymi urządzeniami liścia a urządzenia usługi IoT Edge.
* Usprawnia komunikację między modułami na urządzeniu usługi IoT Edge.
* Usprawnia komunikację między urządzeniem usługi IoT Edge a chmurą.

![Środowisko uruchomieniowe usługi IoT Edge komunikuje się szczegółowe informacje i kondycji modułów do usługi IoT Hub](./media/iot-edge-runtime/Pipeline.png)

Obowiązki środowiska uruchomieniowego usługi IoT Edge można podzielić na dwie kategorie: Zarządzanie komunikacji i moduł. Te dwie role są wykonywane przez dwa składniki, które tworzą środowisko uruchomieniowe usługi IoT Edge. Centrum usługi IoT Edge jest odpowiedzialny za komunikację, podczas gdy zarządza agent usługi IoT Edge, wdrażania i monitorowania modułów. 

Agent usługi Edge i Centrum usługi Edge są moduły, podobnie jak każdy inny moduł, działające na urządzeniu usługi IoT Edge. 

## <a name="iot-edge-hub"></a>Centrum usługi IoT Edge

Centrum usługi Edge jest jednym z dwóch modułów, które tworzą środowisko uruchomieniowe usługi Azure IoT Edge. Działa ona jako lokalny serwer proxy dla usługi IoT Hub, zapewniając tymi samymi punktami końcowymi protokołu, jak usługa IoT Hub. Ten spójności oznacza, że klienci (czy urządzeń lub moduły) mogą łączyć się z środowiska uruchomieniowego usługi IoT Edge tak samo, jak do usługi IoT Hub. 

>[!NOTE]
>Centrum usługi Edge obsługuje klientów łączących się przy użyciu protokołu MQTT lub AMQP. Nie obsługuje klientów korzystających z protokołu HTTP. 

Centrum usługi Edge nie jest pełną wersję usługi IoT Hub działa lokalnie. Istnieje kilka kwestii, które Centrum usługi Edge dyskretnie deleguje odpowiednie uprawnienia do usługi IoT Hub. Na przykład Centrum usługi Edge przekazuje żądania uwierzytelnienia do usługi IoT Hub, gdy urządzenie po raz pierwszy próbuje połączyć. Po nawiązaniu pierwszego połączenia informacji o zabezpieczeniach są buforowane lokalnie przez Centrum usługi Edge. Kolejnych połączeń z tego urządzenia są dozwolone bez konieczności uwierzytelniania w chmurze. 

>[!NOTE]
>Środowisko wykonawcze musi być podłączony, za każdym razem, gdy próbuje uwierzytelnić urządzenia.

W celu zmniejszenia obciążenia przepustowości rozwiązania usługi IoT Edge korzysta, Centrum usługi Edge optymalizuje, jak wiele rzeczywistych połączenia są nawiązywane z chmurą. Centrum usługi Edge przyjmuje logiczny połączenia od klientów, takich jak moduły lub urządzeniami liścia i łączy je do jednego fizycznego połączenia z chmurą. Szczegóły tego procesu są niewidoczne w pozostałej części rozwiązania. Klienci wydaje się, że mają oni własne połączenia do chmury, nawet jeśli te są wszystkie są wysyłane za pośrednictwem tego samego połączenia. 

![Centrum usługi Edge działa jako brama między wiele urządzeń fizycznych i w chmurze](./media/iot-edge-runtime/Gateway.png)

Centrum usługi Edge można określić, czy jest ona dołączona do usługi IoT Hub. W przypadku utraty połączenia Centrum usługi Edge zapisuje komunikaty lub lokalnie w aktualizacji bliźniaczej reprezentacji. Po ustanowieniu połączenia, synchronizuje wszystkie dane. Lokalizacja używana dla tego tymczasowa pamięć podręczna jest określany przez właściwość z bliźniaczej reprezentacji modułu Centrum usługi Edge. Rozmiar pamięci podręcznej nie jest ograniczone i będzie się zwiększać tak długo, jak urządzenie ma pojemność magazynu. 

### <a name="module-communication"></a>Moduł komunikacji

Centrum usługi Edge usprawnia komunikację na moduł. Przy użyciu Centrum usługi Edge jako broker komunikatów przechowuje moduły niezależni od siebie nawzajem. Moduły muszą tylko określić danych wejściowych, które akceptują wiadomości i danych wyjściowych, do których one zapisywania komunikatów. Rozwiązanie dla deweloperów następnie odwzorowywały te dane wejściowe i dane wyjściowe ze sobą tak, aby moduły przetwarzać dane w kolejności, które są specyficzne dla tego rozwiązania. 

![Centrum usługi Edge usprawnia komunikację na moduł](./media/iot-edge-runtime/ModuleEndpoints.png)

Aby wysyłać dane do Centrum usługi Edge, moduł wywołuje metodę SendEventAsync. Pierwszy argument określa, w której dane wyjściowe, aby wysłać wiadomość. Poniższym pseudokodzie wysyła komunikat na output1:

   ```csharp
   ModuleClient client = new ModuleClient.CreateFromEnvironmentAsync(transportSettings); 
   await client.OpenAsync(); 
   await client.SendEventAsync(“output1”, message); 
   ```

Aby komunikat o błędzie, należy zarejestrować wywołanie zwrotne, które przetwarza komunikaty pochodzące na określone dane wejściowe. Poniższym pseudokodzie rejestruje messageProcessor funkcji, które ma być używany do przetwarzania wszystkich komunikatów odebranych na wejście1:

   ```csharp
   await client.SetEventHandlerAsync(“input1”, messageProcessor, userContext);
   ```

Rozwiązanie dla deweloperów jest odpowiedzialny za określenie reguł, które określają, jak Centrum usługi Edge przekazuje komunikatów między modułami. Reguły routingu są zdefiniowane w chmurze i przekazywany do Centrum usługi Edge w jego bliźniaczej reprezentacji urządzenia. Tej samej składni dla tras usługi IoT Hub jest używane do definiowania trasy między modułami w usłudze Azure IoT Edge. 

<!--- For more info on how to declare routes between modules, see []. --->   

![Trasy między modułami](./media/iot-edge-runtime/ModuleEndpointsWithRoutes.png)

## <a name="iot-edge-agent"></a>Agent usługi IoT Edge

Agent usługi IoT Edge jest inny moduł, który tworzy środowisko uruchomieniowe usługi Azure IoT Edge. Jest odpowiedzialny za utworzenie wystąpienia modułów, zapewnienie, że nadal działać i raportowania stanu modułów powrót do Centrum IoT Hub. Podobnie jak każdy inny moduł agent usługi Edge używa jej bliźniaczą reprezentację modułu do przechowywania danych konfiguracji. 

[Demona zabezpieczeń usługi IoT Edge](iot-edge-security-manager.md) uruchamia agenta usługi Edge podczas uruchamiania urządzenia. Agent pobiera jego bliźniaczą reprezentację modułu z usługi IoT Hub i sprawdza manifestu wdrażania. Manifest wdrożenia to plik JSON, która deklaruje moduły, które musiały zostać uruchomione. 

Każdego elementu w manifeście wdrożenia zawiera szczegółowe informacje na temat modułu i jest używany przez agenta usługi Edge do kontrolowania modułu cyklu życia. Bardziej interesujące właściwości, należą: 

* **Settings.Image** — obraz kontenera, który korzysta z agenta usługi Edge można uruchomić modułu. Agent usługi Edge należy określić przy użyciu poświadczeń dla rejestru kontenerów, jeśli obraz, który jest chroniony hasłem. Poświadczenia dla rejestru kontenerów można skonfigurować przy użyciu pliku manifestu wdrożenia lub na samym urządzeniu usługi Edge, aktualizując `config.yaml` pliku w folderze programu usługi IoT Edge.
* **settings.createOptions** — ciąg, który jest przekazywana bezpośrednio do demona platformy Docker, podczas uruchamiania modułu kontenera. Dodanie opcji platformy Docker w tej właściwości umożliwia zaawansowane opcje, takie jak port przekazywania lub instalowanie woluminów do modułu kontenera.  
* **Stan** — stan, w którym agent usługi Edge umieszcza modułu. Ta wartość jest zazwyczaj równa *systemem* dowolną większość osób agent usługi Edge, aby natychmiast uruchomić wszystkie moduły na urządzeniu. Jednak można określić stanu początkowego modułu, aby zostać zatrzymane i poczekaj na czas w przyszłości stwierdzić, agent usługi Edge, które można uruchomić modułu. Agent usługi Edge raportuje stan każdego modułu ją do chmury w zgłaszanych właściwości. Różnica między żądaną właściwość i zgłaszanych właściwości jest wskaźnikiem nieprawidłowo funkcjonujące urządzenia. Są obsługiwane stany:
   * Pobieranie
   * Działanie
   * W złej kondycji
   * Niepowodzenie
   * Zatrzymano
* **restartPolicy** — w jaki sposób agent usługi Edge powoduje ponowne uruchomienie modułu. Możliwe wartości obejmują:
   * Nigdy nie — agent usługi Edge nigdy nie uruchamia ponownie moduł.
   * onFailure — jeśli wystąpiła awaria modułu, agent usługi Edge ponownie go uruchamia. Jeśli moduł, który zamyka się prawidłowo, agent usługi Edge nie uruchom go ponownie.
   * Zła — Jeśli moduł ulegnie awarii lub jest uznawany za złej kondycji, agent usługi Edge ponownie go uruchamia.
   * Zawsze — Jeśli moduł ulega awarii, jest uznawana za złej kondycji lub zamyka w jakikolwiek sposób, agent usługi Edge ponownie go uruchamia. 

Agent usługi IoT Edge wysyła odpowiedź środowiska uruchomieniowego do usługi IoT Hub. Oto lista możliwych odpowiedzi:
  * 200 — OK
  * 400 - Konfiguracja wdrożenia jest źle sformułowany lub nieprawidłowy.
  * 417 urządzenie nie ma konfiguracji wdrożenia, ustawić.
  * 412 — wersja schematu w konfiguracji wdrożenia jest nieprawidłowa.
  * 406 — urządzenie usługi edge jest w trybie offline lub nie wysyła raporty.
  * 500 — Błąd wystąpił w środowiska uruchomieniowego usługi edge.

### <a name="security"></a>Bezpieczeństwo

Agent usługi IoT Edge odgrywa kluczową rolę w zabezpieczeniach urządzenia usługi IoT Edge. Na przykład wykonuje akcje, takie jak weryfikacja do obrazu modułu przed jego uruchomienie. 

Aby uzyskać więcej informacji na temat struktury zabezpieczeń usługi Azure IoT Edge, przeczytaj temat [Menedżera zabezpieczeń usługi IoT Edge](iot-edge-security-manager.md)

## <a name="next-steps"></a>Kolejne kroki

[Świadomość, że certyfikaty usługi Azure IoT Edge](iot-edge-certs.md)
