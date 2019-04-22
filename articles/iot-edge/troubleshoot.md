---
title: Rozwiązywanie problemów — usługa Azure IoT Edge | Dokumentacja firmy Microsoft
description: Skorzystaj z tego artykułu się standardowa umiejętności diagnostyczne dla usługi Azure IoT Edge, takie jak pobieranie dzienniki i stanem składnika i rozwiązywania typowych problemów
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/26/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 83595bf045de412954c176028babc4f94fcb21e1
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "58847536"
---
# <a name="common-issues-and-resolutions-for-azure-iot-edge"></a>Typowe problemy z usługą Azure IoT Edge i ich rozwiązania

Jeśli wystąpią problemy z uruchamianiem usługi Azure IoT Edge w danym środowisku, użyj tego artykułu jako przewodnika ułatwiającego rozwiązywanie problemów. 

## <a name="standard-diagnostic-steps"></a>Standardowe kroki diagnostyki 

W przypadku wystąpienia problemu Dowiedz się więcej o stanie urządzenia usługi IoT Edge, przeglądając dzienniki kontenera i komunikaty, które przekazują do i z urządzenia. Użyj poleceń i narzędzi w tej sekcji, aby zebrać informacje. 

### <a name="check-the-status-of-the-iot-edge-security-manager-and-its-logs"></a>Sprawdź stan IoT Edge Security Manager i jej dzienników:

W systemie Linux:
- Aby wyświetlić stan Menedżera zabezpieczeń przeglądarki Microsoft Edge IoT:

   ```bash
   sudo systemctl status iotedge
   ```

- Aby wyświetlić dzienniki Menedżera zabezpieczeń przeglądarki Microsoft Edge IoT:

    ```bash
    sudo journalctl -u iotedge -f
    ```

- Aby wyświetlić bardziej szczegółowe dzienniki Menedżera zabezpieczeń przeglądarki Microsoft Edge IoT:

   - Edytuj ustawienia demona iotedge:

      ```bash
      sudo systemctl edit iotedge.service
      ```
   
   - Zaktualizuj następujące wiersze:
    
      ```
      [Service]
      Environment=IOTEDGE_LOG=edgelet=debug
      ```
    
   - Ponownie uruchom demona zabezpieczeń usługi IoT Edge:
    
      ```bash
      sudo systemctl cat iotedge.service
      sudo systemctl daemon-reload
      sudo systemctl restart iotedge
      ```

W systemie Windows:
- Aby wyświetlić stan Menedżera zabezpieczeń przeglądarki Microsoft Edge IoT:

   ```powershell
   Get-Service iotedge
   ```

- Aby wyświetlić dzienniki Menedżera zabezpieczeń przeglądarki Microsoft Edge IoT:

   ```powershell
   # Displays logs from today, newest at the bottom.
 
   Get-WinEvent -ea SilentlyContinue `
   -FilterHashtable @{ProviderName= "iotedged";
     LogName = "application"; StartTime = [datetime]::Today} |
   select TimeCreated, Message |
   sort-object @{Expression="TimeCreated";Descending=$false} |
   format-table -autosize -wrap
   ```

### <a name="if-the-iot-edge-security-manager-is-not-running-verify-your-yaml-configuration-file"></a>Jeśli Menedżer zabezpieczeń IoT Edge nie jest uruchomiona, sprawdź plik konfiguracji yaml

> [!WARNING]
> Pliki kodu YAML nie może zawierać karty jako identation. Zamiast tego użyj 2 spacje.

W systemie Linux:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

W systemie Windows:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

### <a name="check-container-logs-for-issues"></a>Sprawdź dzienniki kontenerów w przypadku problemów

Gdy demona zabezpieczeń IoT Edge jest uruchomiona, sprawdź dzienniki kontenerów, aby wykryć problemy. Rozpocznij od wdrożonych kontenerów, a następnie sprawdź kontenery, które tworzą środowisko uruchomieniowe usługi IoT Edge: edgeAgent i edgeHub. Dzienniki agenta usługi IoT Edge zwykle zawierają informacje o cyklu życia każdego kontenera. Dzienniki Centrum usługi IoT Edge zawierają informacje dotyczące obsługi komunikatów i routingu. 

   ```cmd
   iotedge logs <container name>
   ```

### <a name="view-the-messages-going-through-the-iot-edge-hub"></a>Przejrzyj komunikaty przechodzące przez Centrum usługi IoT Edge

Przejrzyj komunikaty przechodzące przez Centrum usługi IoT Edge i Zbierz analizy z pełne dzienniki z kontenerów środowiska wykonawczego. Aby włączyć pełne dzienniki w tych kontenerach, należy ustawić `RuntimeLogLevel` w pliku konfiguracyjnym yaml. Można otworzyć pliku:

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
   > Pliki kodu YAML nie może zawierać karty jako identation. Zamiast tego użyj 2 spacje.

Zapisz plik i ponownie uruchom Menedżera zabezpieczeń usługi IoT Edge.

Możesz także sprawdzić komunikaty przesyłane między usługą IoT Hub i urządzeniami usługi IoT Edge. Te komunikaty można wyświetlić za pomocą [Azure IoT Hub Toolkit](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) rozszerzenia (dawniej: rozszerzenie Azure IoT Toolkit) dla programu Visual Studio Code. Aby uzyskać więcej informacji, zobacz [przydatnym narzędziem podczas programowania za pomocą usługi Azure IoT](https://blogs.msdn.microsoft.com/iotdev/2017/09/01/handy-tool-when-you-develop-with-azure-iot/).

### <a name="restart-containers"></a>Uruchom ponownie kontenerów
Po przeanalizowaniu dzienników i komunikatów, aby uzyskać informacje, możesz spróbować ponownego uruchamiania kontenerów:

```
iotedge restart <container name>
```

Uruchom ponownie usługę kontenerów środowiska uruchomieniowego usługi IoT Edge:

```
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

## <a name="iot-edge-agent-stops-after-about-a-minute"></a>Agent usługi IoT Edge jest zatrzymywany po około minucie

Moduł edgeAgent rozpoczyna się i działa poprawnie przez około minutę, a następnie zatrzymuje się. Dzienniki wskazują, że agenta usługi IoT Edge próbuje nawiązać połączenia z Centrum IoT Hub za pośrednictwem protokołu AMQP, a następnie próbuje nawiązać połączenie za pomocą protokołu AMQP przez WebSocket. Jeśli się nie powiedzie, agent usługi IoT Edge kończy pracę. 

Przykład edgeAgent dzienniki:

```output
2017-11-28 18:46:19 [INF] - Starting module management agent. 
2017-11-28 18:46:19 [INF] - Version - 1.0.7516610 (03c94f85d0833a861a43c669842f0817924911d5) 
2017-11-28 18:46:19 [INF] - Edge agent attempting to connect to IoT Hub via AMQP... 
2017-11-28 18:46:49 [INF] - Edge agent attempting to connect to IoT Hub via AMQP over WebSocket... 
```

### <a name="root-cause"></a>Główna przyczyna
Konfiguracja sieci na hoście uniemożliwia dostęp do sieci agenta usługi IoT Edge. Agent najpierw próbuje nawiązać połączenie za pośrednictwem protokołu AMQP (port 5671). Jeśli połączenie nie powiedzie się, próbuje Websocket (port 443).

Środowisko uruchomieniowe usługi IoT Edge konfiguruje sieć dla każdego z modułów na potrzeby komunikacji. W systemie Linux ta sieć jest siecią mostka. W systemie Windows jest używany translator adresów sieciowych. Ten problem występuje częściej na urządzeniach z systemem Windows używających kontenerów systemu Windows korzystających z sieci NAT. 

### <a name="resolution"></a>Rozwiązanie
Upewnij się, że istnieje trasa do Internetu dla adresów IP przypisanych do tej sieci mostka lub sieci NAT. Czasem konfiguracja sieci VPN na hoście przesłania sieć usługi IoT Edge. 

## <a name="iot-edge-hub-fails-to-start"></a>Nie można uruchomić Centrum usługi IoT Edge

EdgeHub modułu zakończy się niepowodzeniem do rozpoczęcia i drukuje znajduje się w dziennikach następujący komunikat: 

```output
One or more errors occurred. 
(Docker API responded with status code=InternalServerError, response=
{\"message\":\"driver failed programming external connectivity on endpoint edgeHub (6a82e5e994bab5187939049684fb64efe07606d2bb8a4cc5655b2a9bad5f8c80): 
Error starting userland proxy: Bind for 0.0.0.0:443 failed: port is already allocated\"}\n) 
```

### <a name="root-cause"></a>Główna przyczyna
Jakiś inny proces na komputerze hosta korzysta z portu 443. Centrum usługi IoT Edge mapuje porty 5671 i 443 do użycia w scenariuszach z bramą. To mapowanie portów kończy się niepowodzeniem, jeśli inny proces jest już powiązany z portem 443. 

### <a name="resolution"></a>Rozwiązanie
Znajdź i zatrzymaj proces używający portu 443. Ten proces to zwykle serwer internetowy.

## <a name="iot-edge-agent-cant-access-a-modules-image-403"></a>Agent usługi IoT Edge nie może uzyskać dostępu do obrazu modułu (403)
Kontener nie działa, a dzienniki edgeAgent przedstawiają błąd 403. 

### <a name="root-cause"></a>Główna przyczyna
Agent usługi Iot Edge nie ma uprawnień do uzyskania dostępu do obrazu modułu. 

### <a name="resolution"></a>Rozwiązanie
Upewnij się, czy poświadczenia rejestru są poprawnie określone w manifeście wdrożenia

## <a name="iot-edge-security-daemon-fails-with-an-invalid-hostname"></a>Demon zabezpieczeń usługi IoT Edge kończy się niepowodzeniem z nieprawidłową nazwę hosta

Polecenie `sudo journalctl -u iotedge` nie powiedzie się i wyświetla następujący komunikat: 

```output
Error parsing user input data: invalid hostname. Hostname cannot be empty or greater than 64 characters
```

### <a name="root-cause"></a>Główna przyczyna
Środowisko uruchomieniowe usługi IoT Edge może obsługiwać tylko nazwy hostów, które są mniej niż 64 znaki. Maszyny fizyczne zwykle nie mają długie nazwy hostów, ale ten problem występuje częściej na maszynie wirtualnej. Automatycznie generowanych nazw hostów dla maszyn wirtualnych Windows hostowanych na platformie Azure, w szczególności, zwykle za długa. 

### <a name="resolution"></a>Rozwiązanie
Gdy zostanie wyświetlony ten błąd, możesz rozwiązać ten problem, konfigurowanie nazwę DNS maszyny wirtualnej, a następnie ustawić nazwę DNS jako nazwa hosta polecenia Instalatora.

1. W witrynie Azure portal przejdź do strony Przegląd swojej maszyny wirtualnej. 
2. Wybierz **skonfigurować** pod nazwą DNS. Jeśli maszyna wirtualna ma już nazwę DNS skonfigurowany, nie trzeba skonfigurować nowe konto. 

   ![Skonfiguruj nazwę DNS maszyny wirtualnej](./media/troubleshoot/configure-dns.png)

3. Wprowadź wartość w polu **etykiety nazwy DNS** i wybierz **Zapisz**.
4. Skopiuj nową nazwę DNS, która powinna być w formacie  **\<DNSnamelabel\>.\< vmlocation\>. cloudapp.azure.com**.
5. Na maszynie wirtualnej Użyj następującego polecenia, aby skonfigurować środowisko uruchomieniowe usługi IoT Edge z Twoją nazwą DNS:

   - W systemie Linux:

      ```bash
      sudo nano /etc/iotedge/config.yaml
      ```

   - W systemie Windows:

      ```cmd
      notepad C:\ProgramData\iotedge\config.yaml
      ```

## <a name="stability-issues-on-resource-constrained-devices"></a>Problemów ze stabilnością zasobu ograniczonego urządzeń 
Mogą wystąpić problemy stabilności na urządzeniach ograniczone, takich jak Raspberry Pi, szczególnie w przypadku, gdy używany jako brama. Objawy to poza wyjątkami pamięci w module Centrum usługi edge, podrzędne urządzenia nie można nawiązać połączenia lub urządzenia zatrzymuje, wysyła komunikaty telemetryczne po kilku godzinach.

### <a name="root-cause"></a>Główna przyczyna
Centrum usługi IoT Edge, która jest częścią środowiska uruchomieniowego usługi IoT Edge, zoptymalizowana pod kątem wydajności domyślnie i próbuje przydzielić dużych bloków pamięci. Tego rodzaju optymalizacji nie jest idealnym rozwiązaniem dla urządzeń brzegowych ograniczone i może powodować problemy stabilności.

### <a name="resolution"></a>Rozwiązanie
Centrum usługi IoT Edge, należy ustawić zmienną środowiskową **OptimizeForPerformance** do **false**. Istnieją dwa sposoby, w tym celu:

W Interfejsie użytkownika: 

W portalu, przejdź do **szczegóły urządzenia** > **Ustaw moduły** > **skonfiguruj zaawansowane ustawienia środowiska uruchomieniowego Edge**. Utwórz zmienną środowiskową o nazwie modułu Centrum usługi Edge *OptimizeForPerformance* który jest skonfigurowany do *false*.

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

### <a name="root-cause"></a>Główna przyczyna
`Get-WinEvent` Polecenia programu PowerShell, który opiera się na wpis rejestru znajdować się pod kątem wyszukiwania dzienników przy określonym `ProviderName`.

### <a name="resolution"></a>Rozwiązanie
Ustaw wpis rejestru demona usługi IoT Edge. Tworzenie **iotedge.reg** pliku o następującej zawartości i importowanie w rejestrze Windows przez dwukrotne kliknięcie go lub za pomocą `reg import iotedge.reg` polecenia:

```
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

### <a name="root-cause"></a>Główna przyczyna
Demon usługi IoT Edge wymusza identyfikator procesu dla wszystkich modułów nawiązywania połączenia z edgeHub ze względów bezpieczeństwa. Weryfikuje, że wszystkie komunikaty wysyłane przez moduł pochodzą z Identyfikatorem procesu głównego modułu. Jeśli komunikat jest przesyłany przez moduł z Identyfikatorem innego procesu niż ustanowione początkowo, odrzuci wiadomości z komunikatem o błąd 404.

### <a name="resolution"></a>Rozwiązanie
Upewnij się, że ten sam identyfikator procesu jest zawsze używany przez niestandardowy moduł usługi IoT Edge do wysyłania komunikatów do edgeHub. Na przykład, upewnij się, że `ENTRYPOINT` zamiast `CMD` polecenia w pliku platformy Docker, ponieważ `CMD` doprowadzi do jednego procesu Identyfikator modułu i inny identyfikator procesu działania głównego programu, natomiast polecenia powłoki bash `ENTRYPOINT` doprowadzi do Identyfikator pojedynczego procesu.


## <a name="firewall-and-port-configuration-rules-for-iot-edge-deployment"></a>Reguły konfiguracji zapory i portu dla wdrożenia usługi IoT Edge
Usługa Azure IoT Edge umożliwia komunikację z serwera lokalnego do chmury platformy Azure za pomocą obsługiwanych protokołów IoT Hub, zobacz [wybór protokołu komunikacyjnego](../iot-hub/iot-hub-devguide-protocols.md). Aby zwiększyć bezpieczeństwo kanałów komunikacji między usługą Azure IoT Edge i usługi Azure IoT Hub są zawsze skonfigurowane jako ruchu wychodzącego. Ta konfiguracja jest oparta na [wzorzec komunikacji korzystającej z usług](https://blogs.msdn.microsoft.com/clemensv/2014/02/09/service-assisted-communication-for-connected-devices/), minimalizując obszar narażony na zamierzających do zbadania. Komunikacji przychodzącej jest tylko wymagane dla konkretnych scenariuszy, których wymaga usługi Azure IoT Hub do wypychania komunikatów do urządzenia usługi Azure IoT Edge. Komunikaty z chmury do urządzenia są chronione przy użyciu bezpiecznych kanałów TLS i można dodatkowo zabezpieczyć przy użyciu certyfikatów X.509 i moduły urządzenia modułu TPM. Menedżer zabezpieczeń usługi Azure IoT Edge decyduje, jak ta komunikacja może być nawiązane, zobacz [Menedżera zabezpieczeń usługi IoT Edge](../iot-edge/iot-edge-security-manager.md).

Chociaż usługi IoT Edge zapewnia rozszerzoną konfigurację do zabezpieczania środowiska uruchomieniowego usługi Azure IoT Edge i wdrożone moduły, jest nadal zależne od podstawowej konfiguracji komputera i sieci. Dlatego konieczne jest zapewnienie odpowiedniego sieci i reguły zapory są skonfigurowane do krawędzi bezpiecznej komunikacji w chmurze. Poniższa tabela może służyć jako wytyczne podczas konfiguracji zapory reguły dla podstawowych serwerów hostujące środowisko uruchomieniowe usługi Azure IoT Edge:

|Protokół|Port|przychodzące|Wychodzące|Wskazówki|
|--|--|--|--|--|
|MQTT|8883|ZABLOKOWANE (ustawienie domyślne)|ZABLOKOWANE (ustawienie domyślne)|<ul> <li>Skonfiguruj wychodzące (wychodzące) jest otwarte, gdy jako protokół komunikacji przy użyciu protokołu MQTT.<li>1883 dla protokołu MQTT nie jest obsługiwany przez usługi IoT Edge. <li>Połączenia przychodzące (przychodzące) powinien być blokowany.</ul>|
|AMQP|5671|ZABLOKOWANE (ustawienie domyślne)|Otwórz (ustawienie domyślne)|<ul> <li>Protokół komunikacji domyślnego dla usługi IoT Edge. <li> Musi być skonfigurowany jako Otwórz, czy usługi Azure IoT Edge nie jest skonfigurowany dla innych obsługiwanych protokołów AMQP jest protokół komunikacyjny żądaną.<li>5672 dla protokołu AMQP nie jest obsługiwany przez usługi IoT Edge.<li>Zablokować tego portu, w przypadku usługi Azure IoT Edge, używa innej usługi IoT Hub z obsługiwanych protokołów.<li>Połączenia przychodzące (przychodzące) powinien być blokowany.</ul></ul>|
|HTTPS|443|ZABLOKOWANE (ustawienie domyślne)|Otwórz (ustawienie domyślne)|<ul> <li>Skonfiguruj wychodzące (wychodzące) to Open na porcie 443 dla usługi IoT Edge inicjowania obsługi administracyjnej. Ta konfiguracja jest wymagana w przypadku korzystania z ręcznego skryptów lub usługi Azure IoT urządzenia inicjowania obsługi usługi (DPS). <li>Połączenia przychodzącego (przychodzące) powinny być otwarte tylko w określonych scenariuszach: <ul> <li>  Jeśli masz z urządzeniami liścia, które może wysyłać żądania do metody przezroczystej bramy. W tym przypadku Port 443 nie musi być otwarty z sieciami zewnętrznymi, aby połączyć się IoTHub lub świadczyć usługi IoTHub za pośrednictwem usługi Azure IoT Edge. Ten sposób przychodzącej reguły może być ograniczony do otwierać tylko przychodzące (przychodzący) z siecią wewnętrzną. <li> Klient scenariuszy dotyczących urządzeń (C2D).</ul><li>80 dla protokołu HTTP nie jest obsługiwany przez usługi IoT Edge.<li>Jeśli nie można skonfigurować protokołów innych niż HTTP (na przykład protokół AMQP lub MQTT) w przedsiębiorstwie; komunikaty mogą być wysyłane przez protokół WebSockets. Port 443 będzie służyć do komunikacji protokołu WebSocket w takiej sytuacji.</ul>|

## <a name="edge-agent-module-continually-reports-empty-config-file-and-no-modules-start-on-the-device"></a>Moduł agenta usługi Edge stale raportów "pliku konfiguracji puste" i nie modułów uruchomione na urządzeniu

Urządzenie ma problemów podczas uruchamiania moduły zdefiniowane we wdrożeniu. Tylko edgeAgent jest uruchomiona, ale ciągle raportowania "pliku konfiguracji puste...".

### <a name="potential-root-cause"></a>Potencjalną główną przyczynę
Domyślnie usługi IoT Edge uruchamia moduły w sieci do izolowanego kontenera. Urządzenie może mieć problemy z rozpoznawaniem nazw DNS w ramach tej sieci prywatnej.

### <a name="resolution"></a>Rozwiązanie

**Opcja 1: Ustawienia serwera DNS w kontenerze aparatu**

Określenie serwera DNS dla danego środowiska, w ustawieniach aparatu kontenera, które będą stosowane do wszystkich modułów kontenera uruchomione przez aparat. Utwórz plik o nazwie `daemon.json` Określanie serwera DNS, należy użyć. Na przykład:

```
{
    "dns": ["1.1.1.1"]
}
```

Powyższy przykład ustawia serwer DNS publicznie usługi DNS. Jeśli urządzenia usługi edge nie dostępu do tego adresu IP w swoim środowisku, należy ją zastąpić adres serwera DNS, który jest dostępny.

Miejsce `daemon.json` we właściwym miejscu dla danej platformy: 

| Platforma | Lokalizacja |
| --------- | -------- |
| Linux | `/etc/docker` |
| Host Windows za pomocą kontenerów Windows | `C:\ProgramData\iotedge-moby-data\config` |

Jeśli lokalizacja zawiera już `daemon.json` Dodaj **dns** klawisz, aby go, a następnie zapisz plik.

*Uruchom ponownie aparat container aktualizacji zastosować zmiany*

| Platforma | Polecenie |
| --------- | -------- |
| Linux | `sudo systemctl restart docker` |
| Windows (Admin Powershell) | `Restart-Service iotedge-moby -Force` |

**Opcja 2: Ustaw serwer DNS we wdrożeniu usługi IoT Edge dla modułu**

Można ustawić serwera DNS dla każdego modułu *CreateOptions, można żądań* we wdrożeniu usługi IoT Edge. Na przykład:

```
"createOptions": {
  "HostConfig": {
    "Dns": [
      "x.x.x.x"
    ]
  }
}
```

Pamiętaj ustawić dla *edgeAgent* i *edgeHub* także modułów. 

## <a name="next-steps"></a>Kolejne kroki
Uważasz, że znaleziono usterkę platformy IoT Edge? [Prześlij problem](https://github.com/Azure/iotedge/issues) tak, aby mogli dalej ulepszać. 

Jeśli masz więcej pytań, Utwórz [żądania pomocy technicznej](https://portal.azure.com/#create/Microsoft.Support) Aby uzyskać pomoc. 

