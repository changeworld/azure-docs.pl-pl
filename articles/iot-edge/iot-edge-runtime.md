---
title: Zrozumienie środowiska uruchomieniowego Azure IoT krawędzi | Dokumentacja firmy Microsoft
description: Więcej informacji na temat obsługi krawędzi IoT Azure oraz jak pozwalają uzyskać urządzenia krawędzi
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/05/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: aa371ef2ebad01fba379675e8438f56dca9ce356
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37096971"
---
# <a name="understand-the-azure-iot-edge-runtime-and-its-architecture"></a>Zrozumienie jego architektura i środowiska uruchomieniowego krawędzi IoT Azure

Środowisko uruchomieniowe krawędzi IoT jest kolekcją programów, które muszą być zainstalowane na urządzeniu, aby można je było uważać urządzenia IoT. Zbiorczo składników środowiska uruchomieniowego krawędzi IoT Włącz urządzenia brzegowe IoT otrzymać kod wymagany do uruchomienia na brzegu i komunikacji wyniki. 

Środowisko uruchomieniowe krawędzi IoT wykonuje następujące funkcje na urządzeniach krawędzi IoT:

* Instaluje i aktualizuje obciążenia na urządzeniu.
* Zapewnia zachowanie standardów zabezpieczeń usługi Azure IoT Edge na urządzeniu.
* Zapewnia, że [modułów krawędzi IoT][modułów lnk] zawsze są uruchomione.
* Przesyła raporty o kondycji modułów do chmury na potrzeby zdalnego monitorowania.
* Usprawnia komunikację między podrzędnymi urządzeniami liścia a urządzeniem usługi IoT Edge.
* Usprawnia komunikację między modułami na urządzeniu usługi IoT Edge.
* Usprawnia komunikację między urządzeniem usługi IoT Edge a chmurą.

![Środowisko uruchomieniowe krawędzi IoT komunikuje się insights i kondycji modułu do Centrum IoT][1]

Obowiązki środowiska uruchomieniowego krawędzi IoT można podzielić na dwie kategorie: moduł zarządzania i komunikacji. Te dwie role są wykonywane przez dwa składniki wchodzące w skład środowiska uruchomieniowego IoT krawędzi. Centrum IoT krawędzi jest odpowiedzialny za komunikacji, gdy agent krawędzi IoT zarządza wdrażania i monitorowania modułów. 

Zarówno Centrum krawędzi, jak i agenta krawędzi są moduły, podobnie jak inne modułu uruchamiania na urządzeniu IoT krawędzi. Aby uzyskać więcej informacji na temat działania modułów, zobacz [modułów lnk]. 

## <a name="iot-edge-hub"></a>Centrum IoT krawędzi

Koncentrator krawędzi jest jednym z dwóch modułów, które tworzą środowiska uruchomieniowego Azure IoT krawędzi. Działa ona jako lokalny serwer proxy dla Centrum IoT przez udostępnianie tego samego punkty końcowe protokołu jako centrum IoT. Ta spójności oznacza, że klienci (czy urządzeń lub moduły) mogą łączyć się z środowiska uruchomieniowego krawędzi IoT tak samo, jak z Centrum IoT. 

>[!NOTE]
>Koncentrator krawędzi obsługuje klientów łączących się za pomocą MQTT lub AMQP. Nie obsługuje klientów korzystających z protokołu HTTP. 

Koncentrator Edge nie jest pełną wersję Centrum IoT uruchomionej na komputerze lokalnym. Istnieje kilka kwestii, które Centrum krawędzi w trybie dyskretnym deleguje do Centrum IoT. Na przykład Centrum krawędzi przekazuje żądania uwierzytelniania do Centrum IoT, gdy urządzenia po raz pierwszy próbuje nawiązać. Po pierwsze połączenie zostanie nawiązane, informacje o zabezpieczeniach są buforowane lokalnie przez koncentrator krawędzi. Kolejnych połączeń z tym urządzenia mogą bez konieczności uwierzytelniania w chmurze. 

>[!NOTE]
>Środowisko uruchomieniowe muszą być połączone za każdym razem, gdy próbuje uwierzytelniania urządzenia.

Aby ograniczyć przepustowość rozwiązania IoT krawędzi używa, Centrum krawędzi optymalizuje, ile rzeczywiste połączenia są nawiązywane do chmury. Koncentrator krawędzi ma logiczny połączenia od klientów, takie jak moduły lub urządzeń typu liść i łączy je dla pojedynczego połączenia fizycznego do chmury. Szczegóły tego procesu są niewidoczne dla pozostałej części rozwiązania. Klienci wydaje się, że mają własne połączenie w chmurze, nawet jeśli ich są wszystkie wysyłane za pośrednictwem tego samego połączenia. 

![Koncentrator krawędzi działa jak brama między wiele urządzeń fizycznych i chmury][2]

Koncentrator krawędzi można określić, czy jest ona połączona z Centrum IoT. W przypadku utraty połączenia koncentratora krawędzi zapisuje komunikaty lub dwie aktualizacji lokalnie. Po ustanowieniu połączenia, synchronizuje wszystkie dane. Lokalizacja używana do tymczasowego pamięci podręcznej jest określany przez właściwość dwie modułu Centrum krawędzi. Rozmiar pamięci podręcznej nie jest ograniczona i będzie rosnąć tak długo, jak urządzenie ma pojemności. 

>[!NOTE]
>Dodawanie kontrolę nad dodatkowe parametry buforowania zostaną dodane do produktu przed uśpieniem ogólnej dostępności.

### <a name="module-communication"></a>Moduł komunikacji

Koncentrator krawędzi umożliwia komunikację modułu do modułu. Przy użyciu koncentratora krawędź jako brokera komunikatów przechowuje moduły od siebie niezależne. Tylko moduły należy określić danych wejściowych, na których akceptacji wiadomości i danych wyjściowych, do których one zapisywanie wiadomości. Następnie dewelopera rozwiązania odwzorowywały tych danych wejściowych i danych wyjściowych ze sobą, aby modułów przetwarzania danych w kolejności specyficzne dla tego rozwiązania. 

![Koncentrator krawędzi umożliwia komunikację moduł — moduł][3]

Aby wysyłać dane do Centrum krawędzi, moduł wywołuje metodę SendEventAsync. Pierwszy argument określa, w których dane wyjściowe do wysłania tej wiadomości. Następujące pseudocode wysyła komunikat na output1:

   ```csharp
   DeviceClient client = new DeviceClient.CreateFromConnectionString(moduleConnectionString, settings); 
   await client.OpenAsync(); 
   await client.SendEventAsync(“output1”, message); 
   ```

Aby komunikat o błędzie, należy zarejestrować wywołania zwrotnego, który przetwarza wiadomości przychodzących na określone dane wejściowe. Następujące pseudocode rejestruje messageProcessor funkcji przeznaczonych do przetwarzania wszystkich komunikatów odebranych na input1:

   ```csharp
   await client.SetEventHandlerAsync(“input1”, messageProcessor, userContext);
   ```

Deweloper rozwiązania jest odpowiedzialny za określenie reguł określających sposób Centrum krawędzi przekazuje wiadomości między modułami. Reguły routingu są zdefiniowane w chmurze i przesuwana Centrum krawędzi w jego dwie urządzenia. Taka sama składnia dla tras Centrum IoT służy do definiowania tras między modułami w programie Azure IoT Edge. 

<!--- For more info on how to declare routes between modules, see []. --->   

![Tras między modułami][4]

## <a name="iot-edge-agent"></a>Agent krawędzi IoT

Agent krawędzi IoT jest moduł tworzącą środowiska uruchomieniowego Azure IoT krawędzi. Jest on odpowiedzialny za tworzenia wystąpienia modułów, zapewniając, że nadal uruchomione i raportowania stanu modułów powrót do Centrum IoT. Podobnie jak każdy moduł krawędź agent używa jej dwie modułu do przechowywania tych danych konfiguracji. 

Aby rozpocząć wykonywania agenta krawędzi, uruchom polecenie start azure-iot-edge — środowisko uruchomieniowe ctl.py. Agent pobiera jego dwie modułu z Centrum IoT i sprawdza słownik modułów. Słownik modułów, to zbiór modułów, które musiały zostać uruchomione. 

Każdy element w słowniku modułów zawiera informacje na temat modułu i jest używana przez agenta krawędzi kontroli cyklu życia modułu. Niektóre z bardziej interesującego właściwości to: 

* **Settings.Image** — obraz kontenera, który używa krawędź agent można uruchomić modułu. Musi być skonfigurowany agent krawędzi przy użyciu poświadczeń dla kontenera rejestru, jeśli obraz jest chroniony hasłem. Aby skonfigurować agenta krawędzi, zaktualizuj `config.yaml` pliku. W systemie Linux Użyj następującego polecenia: `sudo nano /etc/iotedge/config.yaml`
* **settings.createOptions** — ciąg, który jest przekazywany bezpośrednio do demona Docker podczas uruchamiania modułu kontenera. Umożliwia dodanie opcji Docker w tej właściwości zaawansowane opcje, takie jak port przekazywania lub instalowanie woluminów do kontenera modułu.  
* **Stan** — stan, w którym agent krawędzi umieszcza modułu. Ta wartość jest zazwyczaj równa *systemem* większość użytkowników ma agenta krawędzi, aby natychmiast uruchomić wszystkie moduły na urządzeniu. Można jednak określić początkowy stan modułu, aby zostać zatrzymane i poczekaj, aż przyszłości mówić krawędź agent można uruchomić modułu. Agent krawędzi raportuje stan każdego modułu ją z chmurą we właściwościach zgłoszony. Różnica między żądanej właściwości, a właściwość zgłoszone jest wskaźnikiem błędna urządzenia. Dostępne są następujące stany obsługiwane:
   * Pobieranie
   * Działanie
   * W złej kondycji
   * Niepowodzenie
   * Zatrzymano
* **restartPolicy** — jak moduł ponowne uruchomienie agenta krawędzi. Możliwe wartości obejmują:
   * Nigdy nie — agent krawędzi nigdy nie uruchamia ponownie moduł.
   * onFailure — jeśli wystąpiła awaria modułu, agent krawędzi ponownie go uruchamia. Jeśli moduł, który zamyka się prawidłowo, agent krawędzi nie uruchom go ponownie.
   * Zła kondycja — Jeśli moduł awarii lub uznana za nieprawidłową, agent krawędzi ponownie go uruchamia.
   * Zawsze — Jeśli moduł ulegnie awarii, uznana za nieprawidłową lub zamyka się w dowolny sposób, agent krawędzi ponownie go uruchamia. 

IoT krawędź agent wysyła odpowiedź środowiska uruchomieniowego Centrum IoT. Poniżej przedstawiono listę możliwych odpowiedzi:
  * 200 - OK
  * 400 - konfiguracji wdrożenia jest źle sformułowany lub nieprawidłowy.
  * 417 — urządzenie nie ma zestawu konfiguracyjnego wdrożenia.
  * 412 — wersja schematu w konfiguracji wdrożenia jest nieprawidłowa.
  * 406 — urządzenie brzegowe jest w trybie offline lub nie wysyłającą raporty.
  * 500 — wystąpił błąd w czasie wykonywania krawędzi.

### <a name="security"></a>Bezpieczeństwo

Agent krawędzi IoT odgrywa kluczową rolę w oknie zabezpieczenia urządzenia IoT. Na przykład wykonuje akcje, takie jak weryfikacja obrazu moduł przed jego uruchomienie. Te funkcje zostaną dodane ogólnie dostępna. 

<!-- For more information about the Azure IoT Edge security framework, see []. -->

## <a name="next-steps"></a>Kolejne kroki

- [Zrozumienie modułów Azure IoT krawędzi][modułów lnk]

<!-- Images -->
[1]: ./media/iot-edge-runtime/Pipeline.png
[2]: ./media/iot-edge-runtime/Gateway.png
[3]: ./media/iot-edge-runtime/ModuleEndpoints.png
[4]: ./media/iot-edge-runtime/ModuleEndpointsWithRoutes.png

<!-- Links -->
[modułów lnk]: iot-edge-modules.md
