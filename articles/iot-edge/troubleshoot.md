---
title: Rozwiązywanie problemów — usługa Azure IoT Edge | Dokumentacja firmy Microsoft
description: Skorzystaj z tego artykułu się standardowa umiejętności diagnostyczne dla usługi Azure IoT Edge, takie jak pobieranie dzienniki i stanem składnika i rozwiązywania typowych problemów
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/20/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 98d75f75a985fca3448becab216ad6570d948468
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2020
ms.locfileid: "76772227"
---
# <a name="common-issues-and-resolutions-for-azure-iot-edge"></a>Typowe problemy z usługą Azure IoT Edge i ich rozwiązania

Jeśli wystąpią problemy z uruchamianiem usługi Azure IoT Edge w danym środowisku, użyj tego artykułu jako przewodnika ułatwiającego rozwiązywanie problemów.

## <a name="run-the-iotedge-check-command"></a>Uruchom polecenie "Check" iotedge "

Pierwszy krok podczas rozwiązywania problemów IoT Edge powinien używać polecenia `check`, które uruchamia kolekcję testów konfiguracji i łączności dla typowych problemów. Polecenie `check` jest dostępne w [wersji 1.0.7](https://github.com/Azure/azure-iotedge/releases/tag/1.0.7) lub nowszej.

Można uruchomić polecenie `check` w następujący sposób lub dodać flagę `--help`, aby wyświetlić pełną listę opcji:

* W systemie Linux:

  ```bash
  sudo iotedge check
  ```

* W systemie Windows:

  ```powershell
  iotedge check
  ```

Typy kontroli wykonywane przez narzędzie mogą być klasyfikowane jako:

* Sprawdzanie konfiguracji: sprawdza szczegóły, które mogą uniemożliwić nawiązywanie połączenia z chmurą przez urządzenia brzegowe, w tym problemy z *konfiguracją. YAML* i aparatem kontenerów.
* Sprawdzanie połączeń: sprawdza, czy środowisko uruchomieniowe IoT Edge może uzyskać dostęp do portów na urządzeniu hosta, a wszystkie składniki IoT Edge mogą łączyć się z IoT Hub.
* Kontrole gotowości produkcyjnej: szuka zalecanych najlepszych rozwiązań w zakresie produkcji, takich jak stan certyfikatów urzędu certyfikacji urządzeń i konfiguracji pliku dziennika modułu.

Aby zapoznać się z pełną listą testów diagnostycznych, zobacz [Wbudowana funkcja rozwiązywania problemów](https://github.com/Azure/iotedge/blob/master/doc/troubleshoot-checks.md).

## <a name="standard-diagnostic-steps"></a>Standardowe kroki diagnostyki

Jeśli wystąpi problem, możesz dowiedzieć się więcej o stanie urządzenia IoT Edge, przeglądając dzienniki kontenerów i komunikaty przekazywane do i z urządzenia. Użyj poleceń i narzędzi w tej sekcji, aby zebrać informacje.

### <a name="check-the-status-of-the-iot-edge-security-manager-and-its-logs"></a>Sprawdź stan programu IoT Edge Security Manager i jego dzienników

W systemie Linux:

* Aby wyświetlić stan Menedżera zabezpieczeń przeglądarki Microsoft Edge IoT:

   ```bash
   sudo systemctl status iotedge
   ```

* Aby wyświetlić dzienniki Menedżera zabezpieczeń przeglądarki Microsoft Edge IoT:

    ```bash
    sudo journalctl -u iotedge -f
    ```

* Aby wyświetlić bardziej szczegółowe dzienniki Menedżera zabezpieczeń przeglądarki Microsoft Edge IoT:

  * Edytuj ustawienia demona iotedge:

      ```bash
      sudo systemctl edit iotedge.service
      ```

  * Zaktualizuj następujące wiersze:

      ```bash
      [Service]
      Environment=IOTEDGE_LOG=edgelet=debug
      ```

  * Ponownie uruchom demona zabezpieczeń usługi IoT Edge:

      ```bash
      sudo systemctl cat iotedge.service
      sudo systemctl daemon-reload
      sudo systemctl restart iotedge
      ```

W systemie Windows:

* Aby wyświetlić stan Menedżera zabezpieczeń przeglądarki Microsoft Edge IoT:

   ```powershell
   Get-Service iotedge
   ```

* Aby wyświetlić dzienniki Menedżera zabezpieczeń przeglądarki Microsoft Edge IoT:

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
   ```

### <a name="if-the-iot-edge-security-manager-is-not-running-verify-your-yaml-configuration-file"></a>Jeśli Menedżer zabezpieczeń IoT Edge nie jest uruchomiona, sprawdź plik konfiguracji yaml

> [!WARNING]
> Pliki YAML nie mogą zawierać tabulatorów jako wcięcia. Zamiast tego użyj 2 spacje. Elementy najwyższego poziomu nie powinny zawierać spacji wiodących.

W systemie Linux:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

W systemie Windows:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

### <a name="check-container-logs-for-issues"></a>Sprawdź dzienniki kontenerów w przypadku problemów

Gdy demona zabezpieczeń IoT Edge jest uruchomiona, sprawdź dzienniki kontenerów, aby wykryć problemy. Zacznij od wdrożonych kontenerów, a następnie Przyjrzyj się kontenerom, które składają się na IoT Edge środowisko uruchomieniowe: edgeAgent i edgeHub. Dzienniki agenta IoT Edge zwykle zawierają informacje dotyczące cyklu życia każdego kontenera. Dzienniki Centrum IoT Edge zawierają informacje dotyczące komunikatów i routingu.

   ```cmd
   iotedge logs <container name>
   ```

### <a name="view-the-messages-going-through-the-iot-edge-hub"></a>Wyświetlanie komunikatów przechodzących przez Centrum IoT Edge

Możesz wyświetlić komunikaty przechodzące przez Centrum IoT Edge i zebrać informacje z pełnych dzienników z kontenerów środowiska uruchomieniowego. Aby włączyć pełne dzienniki w tych kontenerach, należy ustawić `RuntimeLogLevel` w pliku konfiguracyjnym yaml. Można otworzyć pliku:

W systemie Linux:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

W systemie Windows:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

Domyślnie `agent` element będzie wyglądać następująco:

   ```yaml
   agent:
     name: edgeAgent
     type: docker
     env: {}
     config:
       image: mcr.microsoft.com/azureiotedge-agent:1.0
       auth: {}
   ```

Zastąp `env: {}` za pomocą:

   ```yaml
   env:
     RuntimeLogLevel: debug
   ```

   > [!WARNING]
   > Pliki kodu YAML nie może zawierać karty jako identation. Zamiast tego użyj 2 spacje. Elementy najwyższego poziomu nie mogą zawierać odstępów wiodących.

Zapisz plik i ponownie uruchom Menedżera zabezpieczeń usługi IoT Edge.

Możesz także sprawdzić komunikaty przesyłane między usługą IoT Hub i urządzeniami usługi IoT Edge. Te komunikaty są wyświetlane przy użyciu [rozszerzenia IoT Hub platformy Azure dla Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit). Aby uzyskać więcej informacji, zobacz [przydatnym narzędziem podczas programowania za pomocą usługi Azure IoT](https://blogs.msdn.microsoft.com/iotdev/2017/09/01/handy-tool-when-you-develop-with-azure-iot/).

### <a name="restart-containers"></a>Uruchom ponownie kontenerów

Po przeanalizowaniu dzienników i komunikatów, aby uzyskać informacje, możesz spróbować ponownego uruchamiania kontenerów:

```cmd
iotedge restart <container name>
```

Uruchom ponownie usługę kontenerów środowiska uruchomieniowego usługi IoT Edge:

```cmd
iotedge restart edgeAgent && iotedge restart edgeHub
```

### <a name="restart-the-iot-edge-security-manager"></a>Ponownie uruchom Menedżera zabezpieczeń usługi IoT Edge

Jeśli problem nadal jest przechowywanie, możesz spróbować ponownie uruchomić Menedżera zabezpieczeń usługi IoT Edge.

W systemie Linux:

   ```cmd
   sudo systemctl restart iotedge
   ```

W systemie Windows:

   ```powershell
   Stop-Service iotedge -NoWait
   sleep 5
   Start-Service iotedge
   ```

## <a name="iot-edge-agent-stops-after-about-a-minute"></a>Agent IoT Edge zatrzymał się po około minucie

Moduł edgeAgent jest uruchamiany i uruchamiany pomyślnie przez około minutę, a następnie zostaje zatrzymany. Dzienniki wskazują, że Agent IoT Edge próbuje nawiązać połączenie z IoT Hub za pośrednictwem AMQP, a następnie próbuje nawiązać połączenie przy użyciu AMQP za pośrednictwem protokołu WebSocket. Gdy to się nie powiedzie, Agent IoT Edge kończy pracę.

Przykładowe dzienniki edgeAgent:

```output
2017-11-28 18:46:19 [INF] - Starting module management agent.
2017-11-28 18:46:19 [INF] - Version - 1.0.7516610 (03c94f85d0833a861a43c669842f0817924911d5)
2017-11-28 18:46:19 [INF] - Edge agent attempting to connect to IoT Hub via AMQP...
2017-11-28 18:46:49 [INF] - Edge agent attempting to connect to IoT Hub via AMQP over WebSocket...
```

**Główna przyczyna**

Konfiguracja sieci w sieci hosta uniemożliwia agentowi IoT Edge uzyskiwanie dostępu do sieci. Agent najpierw próbuje nawiązać połączenie za pośrednictwem protokołu AMQP (port 5671). Jeśli połączenie nie powiedzie się, próbuje Websocket (port 443).

Środowisko uruchomieniowe usługi IoT Edge konfiguruje sieć dla każdego z modułów na potrzeby komunikacji. W systemie Linux ta sieć jest siecią mostka. W systemie Windows jest używany translator adresów sieciowych. Ten problem występuje częściej na urządzeniach z systemem Windows używających kontenerów systemu Windows korzystających z sieci NAT.

**Rozdzielczość**

Upewnij się, że istnieje trasa do Internetu dla adresów IP przypisanych do tej sieci mostka lub sieci NAT. Czasem konfiguracja sieci VPN na hoście przesłania sieć usługi IoT Edge.

## <a name="iot-edge-hub-fails-to-start"></a>Nie można uruchomić Centrum IoT Edge

Nie można uruchomić modułu edgeHub i do dzienników zostanie wyświetlony następujący komunikat:

```output
One or more errors occurred. 
(Docker API responded with status code=InternalServerError, response=
{\"message\":\"driver failed programming external connectivity on endpoint edgeHub (6a82e5e994bab5187939049684fb64efe07606d2bb8a4cc5655b2a9bad5f8c80): 
Error starting userland proxy: Bind for 0.0.0.0:443 failed: port is already allocated\"}\n)
```

**Główna przyczyna**

Jakiś inny proces na komputerze hosta korzysta z portu 443. IoT Edge Hub mapuje porty 5671 i 443 do użycia w scenariuszach bramy. To mapowanie portów kończy się niepowodzeniem, jeśli inny proces jest już powiązany z portem 443.

**Rozdzielczość**

Znajdź i zatrzymaj proces używający portu 443. Ten proces to zwykle serwer internetowy.

## <a name="iot-edge-agent-cant-access-a-modules-image-403"></a>Agent IoT Edge nie może uzyskać dostępu do obrazu modułu (403)

Nie można uruchomić kontenera, a w dziennikach edgeAgent jest wyświetlany błąd 403.

**Główna przyczyna**

Agent IoT Edge nie ma uprawnień dostępu do obrazu modułu.

**Rozdzielczość**

Upewnij się, czy poświadczenia rejestru są poprawnie określone w manifeście wdrożenia

## <a name="iot-edge-security-daemon-fails-with-an-invalid-hostname"></a>Demon zabezpieczeń usługi IoT Edge kończy się niepowodzeniem z nieprawidłową nazwę hosta

Polecenie `sudo journalctl -u iotedge` nie powiedzie się i wyświetla następujący komunikat:

```output
Error parsing user input data: invalid hostname. Hostname cannot be empty or greater than 64 characters
```

**Główna przyczyna**

Środowisko uruchomieniowe usługi IoT Edge może obsługiwać tylko nazwy hostów, które są mniej niż 64 znaki. Maszyny fizyczne zwykle nie mają długie nazwy hostów, ale ten problem występuje częściej na maszynie wirtualnej. Automatycznie generowanych nazw hostów dla maszyn wirtualnych Windows hostowanych na platformie Azure, w szczególności, zwykle za długa.

**Rozdzielczość**

Gdy zostanie wyświetlony ten błąd, możesz rozwiązać ten problem, konfigurowanie nazwę DNS maszyny wirtualnej, a następnie ustawić nazwę DNS jako nazwa hosta polecenia Instalatora.

1. W witrynie Azure portal przejdź do strony Przegląd swojej maszyny wirtualnej.
2. Wybierz **skonfigurować** pod nazwą DNS. Jeśli maszyna wirtualna ma już nazwę DNS skonfigurowany, nie trzeba skonfigurować nowe konto.

   ![Skonfiguruj nazwę DNS maszyny wirtualnej](./media/troubleshoot/configure-dns.png)

3. Wprowadź wartość w polu **etykiety nazwy DNS** i wybierz **Zapisz**.
4. Skopiuj nową nazwę DNS, która powinna być w formacie  **\<DNSnamelabel\>.\< vmlocation\>. cloudapp.azure.com**.
5. Na maszynie wirtualnej Użyj następującego polecenia, aby skonfigurować środowisko uruchomieniowe usługi IoT Edge z Twoją nazwą DNS:

   * W systemie Linux:

      ```bash
      sudo nano /etc/iotedge/config.yaml
      ```

   * W systemie Windows:

      ```cmd
      notepad C:\ProgramData\iotedge\config.yaml
      ```

## <a name="stability-issues-on-resource-constrained-devices"></a>Problemów ze stabilnością zasobu ograniczonego urządzeń

Mogą wystąpić problemy stabilności na urządzeniach ograniczone, takich jak Raspberry Pi, szczególnie w przypadku, gdy używany jako brama. Objawy to poza wyjątkami pamięci w module Centrum usługi edge, podrzędne urządzenia nie można nawiązać połączenia lub urządzenia zatrzymuje, wysyła komunikaty telemetryczne po kilku godzinach.

**Główna przyczyna**

Centrum IoT Edge, które jest częścią środowiska uruchomieniowego IoT Edge, jest zoptymalizowane pod kątem wydajności domyślnie i próbuje przydzielić duże fragmenty pamięci. Tego rodzaju optymalizacji nie jest idealnym rozwiązaniem dla urządzeń brzegowych ograniczone i może powodować problemy stabilności.

**Rozdzielczość**

W przypadku Centrum IoT Edge Ustaw dla zmiennej środowiskowej **OptimizeForPerformance** **wartość false**. Istnieją dwa sposoby ustawiania zmiennych środowiskowych:

W witrynie Azure Portal:

W IoT Hub wybierz urządzenie IoT Edge i na stronie Szczegóły urządzenia wybierz pozycję **Ustaw moduły** > **Ustawienia środowiska uruchomieniowego**. Utwórz zmienną środowiskową dla modułu centrum brzegowego o nazwie *OptimizeForPerformance* , która ma wartość *false*.

![OptimizeForPerformance ustawiony na wartość false](./media/troubleshoot/optimizeforperformance-false.png)

**OR**

W manifeście wdrożenia:

```json
  "edgeHub": {
    "type": "docker",
    "settings": {
      "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
      "createOptions": <snipped>
    },
    "env": {
      "OptimizeForPerformance": {
          "value": "false"
      }
    },
```

## <a name="cant-get-the-iot-edge-daemon-logs-on-windows"></a>Nie można pobrać usługi IoT Edge dzienniki demona dla Windows

Jeśli otrzymasz EventLogException, korzystając z `Get-WinEvent` na Windows, Sprawdź wpisy rejestru.

**Główna przyczyna**

`Get-WinEvent` Polecenia programu PowerShell, który opiera się na wpis rejestru znajdować się pod kątem wyszukiwania dzienników przy określonym `ProviderName`.

**Rozdzielczość**

Ustaw wpis rejestru demona usługi IoT Edge. Tworzenie **iotedge.reg** pliku o następującej zawartości i importowanie w rejestrze Windows przez dwukrotne kliknięcie go lub za pomocą `reg import iotedge.reg` polecenia:

```reg
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Application\iotedged]
"CustomSource"=dword:00000001
"EventMessageFile"="C:\\ProgramData\\iotedge\\iotedged.exe"
"TypesSupported"=dword:00000007
```

## <a name="iot-edge-module-fails-to-send-a-message-to-the-edgehub-with-404-error"></a>Moduł usługi IoT Edge nie uda się wysłać wiadomość do edgeHub z powodu błędu 404

Niestandardowy moduł usługi IoT Edge nie uda się wysłać wiadomość do edgeHub z 404 `Module not found` błędu. Demon usługi IoT Edge wyświetla następujący komunikat do dzienników:

```output
Error: Time:Thu Jun  4 19:44:58 2018 File:/usr/sdk/src/c/provisioning_client/adapters/hsm_client_http_edge.c Func:on_edge_hsm_http_recv Line:364 executing HTTP request fails, status=404, response_buffer={"message":"Module not found"}u, 04 )
```

**Główna przyczyna**

Demon usługi IoT Edge wymusza identyfikator procesu dla wszystkich modułów nawiązywania połączenia z edgeHub ze względów bezpieczeństwa. Weryfikuje, że wszystkie komunikaty wysyłane przez moduł pochodzą z Identyfikatorem procesu głównego modułu. Jeśli komunikat jest przesyłany przez moduł z Identyfikatorem innego procesu niż ustanowione początkowo, odrzuci wiadomości z komunikatem o błąd 404.

**Rozdzielczość**

W przypadku wersji 1.0.7 wszystkie procesy modułów są autoryzowane do nawiązania połączenia. Jeśli uaktualnienie do 1.0.7 nie jest możliwe, wykonaj następujące czynności. Aby uzyskać więcej informacji, zobacz [Dziennik zmian wersji 1.0.7](https://github.com/Azure/iotedge/blob/master/CHANGELOG.md#iotedged-1).

Upewnij się, że ten sam identyfikator procesu jest zawsze używany przez niestandardowy moduł usługi IoT Edge do wysyłania komunikatów do edgeHub. Na przykład upewnij się, że `ENTRYPOINT` zamiast polecenia `CMD` w pliku Docker, ponieważ `CMD` będzie prowadzić do jednego identyfikatora procesu dla modułu i innego identyfikatora procesu dla polecenia bash, w którym działa program, a `ENTRYPOINT` będzie prowadzić do jednego identyfikatora procesu.

## <a name="firewall-and-port-configuration-rules-for-iot-edge-deployment"></a>Reguły konfiguracji zapory i portu dla wdrożenia usługi IoT Edge

Azure IoT Edge umożliwia komunikację między serwerem lokalnym a chmurą platformy Azure przy użyciu obsługiwanych protokołów IoT Hub, zobacz [Wybieranie protokołu komunikacyjnego](../iot-hub/iot-hub-devguide-protocols.md). Aby zwiększyć bezpieczeństwo kanałów komunikacji między usługą Azure IoT Edge i usługi Azure IoT Hub są zawsze skonfigurowane jako ruchu wychodzącego. Ta konfiguracja jest oparta na [wzorzec komunikacji korzystającej z usług](https://blogs.msdn.microsoft.com/clemensv/2014/02/09/service-assisted-communication-for-connected-devices/), minimalizując obszar narażony na zamierzających do zbadania. Komunikacji przychodzącej jest tylko wymagane dla konkretnych scenariuszy, których wymaga usługi Azure IoT Hub do wypychania komunikatów do urządzenia usługi Azure IoT Edge. Komunikaty z chmury do urządzenia są chronione przy użyciu bezpiecznych kanałów TLS i można dodatkowo zabezpieczyć przy użyciu certyfikatów X.509 i moduły urządzenia modułu TPM. Menedżer zabezpieczeń usługi Azure IoT Edge decyduje, jak ta komunikacja może być nawiązane, zobacz [Menedżera zabezpieczeń usługi IoT Edge](../iot-edge/iot-edge-security-manager.md).

Chociaż usługi IoT Edge zapewnia rozszerzoną konfigurację do zabezpieczania środowiska uruchomieniowego usługi Azure IoT Edge i wdrożone moduły, jest nadal zależne od podstawowej konfiguracji komputera i sieci. Z tego względu należy upewnić się, że skonfigurowano odpowiednie reguły sieci i zapory na potrzeby bezpiecznej krawędzi komunikacji z chmurą. Poniższa tabela może służyć jako wytyczna, gdy reguły zapory konfiguracyjnej dla serwerów źródłowych, na których jest hostowana Azure IoT Edge środowisko uruchomieniowe:

|Protocol (Protokół)|Port|przychodzące|Wychodzące|Wskazówka|
|--|--|--|--|--|
|MQTT|8883|ZABLOKOWANE (ustawienie domyślne)|ZABLOKOWANE (ustawienie domyślne)|<ul> <li>Skonfiguruj wychodzące (wychodzące) jest otwarte, gdy jako protokół komunikacji przy użyciu protokołu MQTT.<li>1883 dla protokołu MQTT nie jest obsługiwany przez usługi IoT Edge. <li>Połączenia przychodzące (przychodzące) powinien być blokowany.</ul>|
|AMQP|5671|ZABLOKOWANE (ustawienie domyślne)|Otwórz (ustawienie domyślne)|<ul> <li>Protokół komunikacji domyślnego dla usługi IoT Edge. <li> Musi być skonfigurowany jako Otwórz, czy usługi Azure IoT Edge nie jest skonfigurowany dla innych obsługiwanych protokołów AMQP jest protokół komunikacyjny żądaną.<li>5672 dla protokołu AMQP nie jest obsługiwany przez usługi IoT Edge.<li>Zablokować tego portu, w przypadku usługi Azure IoT Edge, używa innej usługi IoT Hub z obsługiwanych protokołów.<li>Połączenia przychodzące (przychodzące) powinien być blokowany.</ul></ul>|
|HTTPS|443|ZABLOKOWANE (ustawienie domyślne)|Otwórz (ustawienie domyślne)|<ul> <li>Skonfiguruj wychodzące (wychodzące) to Open na porcie 443 dla usługi IoT Edge inicjowania obsługi administracyjnej. Ta konfiguracja jest wymagana w przypadku korzystania z ręcznego skryptów lub usługi Azure IoT urządzenia inicjowania obsługi usługi (DPS). <li>Połączenia przychodzącego (przychodzące) powinny być otwarte tylko w określonych scenariuszach: <ul> <li>  Jeśli masz z urządzeniami liścia, które może wysyłać żądania do metody przezroczystej bramy. W tym przypadku Port 443 nie musi być otwarty z sieciami zewnętrznymi, aby połączyć się IoTHub lub świadczyć usługi IoTHub za pośrednictwem usługi Azure IoT Edge. Ten sposób przychodzącej reguły może być ograniczony do otwierać tylko przychodzące (przychodzący) z siecią wewnętrzną. <li> Klient scenariuszy dotyczących urządzeń (C2D).</ul><li>80 dla protokołu HTTP nie jest obsługiwany przez usługi IoT Edge.<li>Jeśli nie można skonfigurować protokołów innych niż HTTP (na przykład protokół AMQP lub MQTT) w przedsiębiorstwie; komunikaty mogą być wysyłane przez protokół WebSockets. Port 443 będzie służyć do komunikacji protokołu WebSocket w takiej sytuacji.</ul>|

## <a name="edge-agent-module-continually-reports-empty-config-file-and-no-modules-start-on-the-device"></a>Moduł agenta programu Edge ciągle raportuje pusty plik konfiguracji i nie uruchamia żadnych modułów na urządzeniu.

Urządzenie ma problemy z uruchamianiem modułów zdefiniowanych we wdrożeniu. Tylko edgeAgent jest uruchomiona, ale ciągle zgłasza "pusty plik konfiguracji"... ".

**Główna przyczyna**

Domyślnie IoT Edge uruchamia moduły w swojej własnej izolowanej sieci kontenerów. Urządzenie może mieć problemy z rozpoznawaniem nazw DNS w ramach tej sieci prywatnej.

**Rozdzielczość**

**Opcja 1. Ustawianie serwera DNS w ustawieniach aparatu kontenera**

Określ serwer DNS dla środowiska w ustawieniach aparatu kontenera, które będą stosowane do wszystkich modułów kontenera uruchomionych przez aparat. Utwórz plik o nazwie `daemon.json` określający serwer DNS, który ma być używany. Przykład:

```json
{
    "dns": ["1.1.1.1"]
}
```

W powyższym przykładzie serwer DNS jest ustawiany publicznie dostępną usługę DNS. Jeśli urządzenie brzegowe nie może uzyskać dostępu do tego adresu IP ze środowiska, zastąp go adresem serwera DNS, który jest dostępny.

Umieść `daemon.json` w odpowiedniej lokalizacji dla Twojej platformy:

| Platforma | Lokalizacja |
| --------- | -------- |
| Linux | `/etc/docker` |
| Host z systemem Windows z kontenerami systemu Windows | `C:\ProgramData\iotedge-moby\config` |

Jeśli lokalizacja zawiera już plik `daemon.json`, Dodaj do niego klucz **DNS** i Zapisz plik.

Aby aktualizacje zaczęły obowiązywać, uruchom ponownie aparat kontenerów.

| Platforma | Polecenie |
| --------- | -------- |
| Linux | `sudo systemctl restart docker` |
| Windows (administracyjny program PowerShell) | `Restart-Service iotedge-moby -Force` |

**Opcja 2: Ustaw serwer DNS w IoT Edge wdrożenia na moduł**

Można ustawić serwer DNS dla *opcji* "wszystkie" modułu we wdrożeniu IoT Edge. Przykład:

```json
"createOptions": {
  "HostConfig": {
    "Dns": [
      "x.x.x.x"
    ]
  }
}
```

Należy pamiętać, aby skonfigurować również tę konfigurację dla modułów *edgeAgent* i *edgeHub* .

## <a name="next-steps"></a>Następne kroki

Uważasz, że znaleziono usterkę platformy IoT Edge? [Prześlij problem](https://github.com/Azure/iotedge/issues) tak, aby mogli dalej ulepszać.

Jeśli masz więcej pytań, Utwórz [żądania pomocy technicznej](https://portal.azure.com/#create/Microsoft.Support) Aby uzyskać pomoc.
