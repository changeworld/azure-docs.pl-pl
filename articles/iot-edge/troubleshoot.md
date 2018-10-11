---
title: Rozwiązywanie problemów z usługą Azure IoT Edge | Microsoft Docs
description: Rozwiązywanie typowych problemów i nabywanie umiejętności rozwiązywania problemów z usługą Azure IoT Edge
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/26/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: a63a31c5ceb4298829f85627196fea5d7a38ca4b
ms.sourcegitcommit: 7b0778a1488e8fd70ee57e55bde783a69521c912
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2018
ms.locfileid: "49068506"
---
# <a name="common-issues-and-resolutions-for-azure-iot-edge"></a>Typowe problemy z usługą Azure IoT Edge i ich rozwiązania

Jeśli wystąpią problemy z uruchamianiem usługi Azure IoT Edge w danym środowisku, użyj tego artykułu jako przewodnika ułatwiającego rozwiązywanie problemów. 

## <a name="standard-diagnostic-steps"></a>Standardowe kroki diagnostyki 

W przypadku wystąpienia problemu dowiedz się więcej o stanie urządzenia usługi IoT Edge, przeglądając dzienniki kontenera i komunikaty przekazywane do i z urządzenia. Użyj poleceń i narzędzi w tej sekcji, aby zebrać informacje. 

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

Gdy demona zabezpieczeń IoT Edge jest uruchomiona, sprawdź dzienniki kontenerów, aby wykryć problemy. Rozpocznij od wdrożonych kontenerów, a następnie sprawdź kontenery, które tworzą środowisko uruchomieniowe usługi IoT Edge: Agent usługi Edge i Centrum usługi Edge. Dzienniki agenta usługi Edge zwykle zawierają informacje o cyklu życia każdego kontenera. Dzienniki centrum usługi Edge zawierają informacje dotyczące obsługi komunikatów i routingu. 

   ```cmd
   iotedge logs <container name>
   ```

### <a name="view-the-messages-going-through-the-edge-hub"></a>Przejrzyj komunikaty przechodzące przez Centrum usługi Edge

Przejrzyj komunikaty przechodzące przez Centrum usługi Edge i Zbierz analizy dotyczące aktualizacji właściwości urządzenia za pomocą szczegółowych dzienników z kontenerów środowiska wykonawczego edgeAgent i edgeHub. Aby włączyć pełne dzienniki w tych kontenerach, należy ustawić `RuntimeLogLevel` w pliku konfiguracyjnym yaml. Można otworzyć pliku:

W systemie Linux:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

W systemie Windows:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

Domyślnie `agent` elementu będą wyglądać mniej więcej tak:

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

> [!WARNING]
> Pliki kodu YAML nie może zawierać karty jako identation. Zamiast tego użyj 2 spacje.

   ```yaml
   env:
     RuntimeLogLevel: debug
   ```

Zapisz plik i ponownie uruchom Menedżera zabezpieczeń usługi IoT Edge.

Możesz także sprawdzić komunikaty przesyłane między usługą IoT Hub i urządzeniami usługi IoT Edge. Te komunikaty można wyświetlić przy użyciu rozszerzenia [Azure IoT Toolkit](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) programu Visual Studio Code. Więcej pomocy zawiera temat [Handy tool when you develop with Azure IoT (Przydatne narzędzie dla programowania w usłudze Azure IoT)](https://blogs.msdn.microsoft.com/iotdev/2017/09/01/handy-tool-when-you-develop-with-azure-iot/).

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

## <a name="edge-agent-stops-after-about-a-minute"></a>Agent usługi Edge jest zatrzymywany po około minucie

Agent usługi Edge jest uruchamiany i działa poprawnie przez około minutę, a następnie jest zatrzymywany. Dzienniki wskazują, że agent usługi Edge próbuje nawiązać połączenie z usługą IoT Hub za pośrednictwem protokołu AMQP, a następnie po około 30 sekundach próbuje nawiązać połączenie za pomocą protokołu AMQP przez WebSocket. Jeśli to się nie powiedzie, agent usługi Edge kończy pracę. 

Przykład dzienników agenta usługi Edge:

```output
2017-11-28 18:46:19 [INF] - Starting module management agent. 
2017-11-28 18:46:19 [INF] - Version - 1.0.7516610 (03c94f85d0833a861a43c669842f0817924911d5) 
2017-11-28 18:46:19 [INF] - Edge agent attempting to connect to IoT Hub via AMQP... 
2017-11-28 18:46:49 [INF] - Edge agent attempting to connect to IoT Hub via AMQP over WebSocket... 
```

### <a name="root-cause"></a>Główna przyczyna
Konfiguracja sieci na hoście uniemożliwia agentowi usługi Edge dostęp do sieci. Agent najpierw próbuje nawiązać połączenie za pośrednictwem protokołu AMQP (port 5671). W przypadku niepowodzenia podejmuje próbę przy użyciu protokołu WebSocket (port 443).

Środowisko uruchomieniowe usługi IoT Edge konfiguruje sieć dla każdego z modułów na potrzeby komunikacji. W systemie Linux ta sieć jest siecią mostka. W systemie Windows jest używany translator adresów sieciowych. Ten problem występuje częściej na urządzeniach z systemem Windows używających kontenerów systemu Windows korzystających z sieci NAT. 

### <a name="resolution"></a>Rozwiązanie
Upewnij się, że istnieje trasa do Internetu dla adresów IP przypisanych do tej sieci mostka lub sieci NAT. Czasem konfiguracja sieci VPN na hoście przesłania sieć usługi IoT Edge. 

## <a name="edge-hub-fails-to-start"></a>Nie można uruchomić centrum usługi Edge

Nie można uruchomić centrum usługi Edge, a w dzienniku znajduje się następujący komunikat: 

```output
One or more errors occurred. 
(Docker API responded with status code=InternalServerError, response=
{\"message\":\"driver failed programming external connectivity on endpoint edgeHub (6a82e5e994bab5187939049684fb64efe07606d2bb8a4cc5655b2a9bad5f8c80): 
Error starting userland proxy: Bind for 0.0.0.0:443 failed: port is already allocated\"}\n) 
```

### <a name="root-cause"></a>Główna przyczyna
Jakiś inny proces na komputerze hosta korzysta z portu 443. Centrum usługi Edge mapuje porty 5671 i 443 do użycia w scenariuszach z bramą. To mapowanie portów kończy się niepowodzeniem, jeśli inny proces jest już powiązany z portem 443. 

### <a name="resolution"></a>Rozwiązanie
Znajdź i zatrzymaj proces używający portu 443. Ten proces to zwykle serwer internetowy.

## <a name="edge-agent-cant-access-a-modules-image-403"></a>Agent usługi Edge nie ma dostępu do obrazu modułu (błąd 403)
Uruchomienie kontenera nie powodzi się, a w dziennikach agenta usługi Edge jest zgłaszany błąd 403. 

### <a name="root-cause"></a>Główna przyczyna
Agent usługi Edge nie ma uprawnień dostępu do obrazu modułu. 

### <a name="resolution"></a>Rozwiązanie
Upewnij się, czy poświadczenia rejestru są poprawnie określone w manifeście wdrożenia

## <a name="iot-edge-security-daemon-fails-with-an-invalid-hostname"></a>Demon zabezpieczeń usługi IoT Edge kończy się niepowodzeniem z nieprawidłową nazwę hosta

Polecenie `sudo journalctl -u iotedge` nie powiedzie się i wyświetla następujący komunikat: 

```output
Error parsing user input data: invalid hostname. Hostname cannot be empty or greater than 64 characters
```

### <a name="root-cause"></a>Główna przyczyna
Środowisko uruchomieniowe usługi IoT Edge może obsługiwać tylko nazwy hostów, które są mniej niż 64 znaki. To zwykle nie jest problemem w przypadku komputerów fizycznych, ale mogą wystąpić podczas konfigurowania środowiska uruchomieniowego na maszynie wirtualnej. Automatycznie generowanych nazw hostów dla maszyn wirtualnych Windows hostowanych na platformie Azure, w szczególności, zwykle za długa. 

### <a name="resolution"></a>Rozwiązanie
Gdy zostanie wyświetlony ten błąd, możesz rozwiązać ten problem, konfigurowanie nazwę DNS maszyny wirtualnej, a następnie ustawić nazwę DNS jako nazwa hosta polecenia Instalatora.

1. W witrynie Azure portal przejdź do strony Przegląd swojej maszyny wirtualnej. 
2. Wybierz **skonfigurować** pod nazwą DNS. Jeśli maszyna wirtualna ma już nazwę DNS skonfigurowany, nie trzeba skonfigurować nowe konto. 

   ![Konfigurowanie nazwy DNS](./media/troubleshoot/configure-dns.png)

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
Centrum usługi edge, która jest częścią środowiska uruchomieniowego usługi edge, zoptymalizowana pod kątem wydajności domyślnie i próbuje przydzielić dużych bloków pamięci. To nie jest idealnym rozwiązaniem dla urządzeń brzegowych ograniczone i może powodować problemy stabilności.

### <a name="resolution"></a>Rozwiązanie
Dla przeglądarki edge hub ustawić zmienną środowiskową **OptimizeForPerformance** do **false**. Istnieją dwa sposoby, w tym celu:

W Interfejsie użytkownika: 

W portalu pochodzące ze *szczegóły urządzenia*->*Ustaw moduły*->*skonfiguruj zaawansowane ustawienia środowiska uruchomieniowego Edge*, Utwórz zmienną środowiskową wywołuje się *OptimizeForPerformance* który jest skonfigurowany do *false* dla *Centrum usługi Edge*.

![optimizeforperformance][img-optimize-for-perf]

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
`Get-WinEvent` Polecenia programu PowerShell, który opiera się na wpis rejestru, aby znajdować się pod kątem wyszukiwania dzienników przy określonym `ProviderName`.

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
Demon usługi IoT Edge wymusza identyfikator procesu dla wszystkich modułów nawiązywania połączenia z edgeHub ze względów bezpieczeństwa. Weryfikuje, że wszystkie komunikaty wysyłane przez moduł pochodzą z identyfikatorem procesu głównego modułu. Jeśli komunikat jest przesyłany przez moduł z identyfikatorem innego procesu niż ustanowione początkowo, odrzuci wiadomości z komunikatem o błąd 404.

### <a name="resolution"></a>Rozwiązanie
Upewnij się, że ten sam identyfikator procesu jest zawsze używany przez niestandardowy moduł usługi IoT Edge do wysyłania komunikatów do edgeHub. Na przykład, upewnij się, że `ENTRYPOINT` zamiast `CMD` polecenia w pliku platformy Docker, ponieważ `CMD` doprowadzi do jednego procesu Identyfikator modułu i inny identyfikator procesu działania głównego programu, natomiast polecenia powłoki bash `ENTRYPOINT` doprowadzi do Identyfikator pojedynczego procesu.


## <a name="firewall-and-port-configuration-rules-for-iot-edge-deployment"></a>Reguły konfiguracji zapory i portu dla wdrożenia usługi IoT Edge
Usługa Azure IoT Edge umożliwia komunikację z serwer graniczny środowiska lokalnego do chmury platformy Azure za pomocą obsługiwanych protokołów IoT Hub, zobacz [wybór protokołu komunikacyjnego](../iot-hub/iot-hub-devguide-protocols.md). Aby zwiększyć bezpieczeństwo kanałów komunikacji między usługą Azure IoT Edge i usługi Azure IoT Hub są zawsze skonfigurowane jako ruchu wychodzącego; zależy to [wzorzec komunikacji korzystającej z usług](https://blogs.msdn.microsoft.com/clemensv/2014/02/09/service-assisted-communication-for-connected-devices/), minimalizując obszar narażony na zamierzających do zbadania. Komunikacji przychodzącej jest tylko wymagane dla konkretnych scenariuszy, gdzie usługi Azure IoT Hub potrzebne do wypychania komunikatów do serwera usługi Azure IoT Edge (np. w chmurze do komunikaty urządzenia), są ponownie są chronione przy użyciu bezpiecznych kanałów TLS i można dodatkowo zabezpieczyć przy użyciu X.509 certyfikaty i moduły urządzenia modułu TPM. Menedżer zabezpieczeń usługi Azure IoT Edge decyduje, jak ta komunikacja może być nawiązane, zobacz [Menedżera zabezpieczeń usługi IoT Edge](../iot-edge/iot-edge-security-manager.md).

Chociaż usługi IoT Edge zapewnia rozszerzoną konfigurację do zabezpieczania środowiska uruchomieniowego usługi Azure IoT Edge i wdrożone moduły, jest nadal zależne od podstawowej konfiguracji komputera i sieci. W związku z tym koniecznie upewnij się, że odpowiednie reguły sieci i zapory są skonfigurowane do bezpiecznego urządzeniami brzegowymi w celu komunikacji z chmury. Następujące może służyć jako wytyczne podczas konfiguracji zapory reguły dla podstawowych serwerów hostujące środowisko uruchomieniowe usługi Azure IoT Edge:

|Protokół|Port|przychodzące|Wychodzący|Wskazówki|
|--|--|--|--|--|
|MQTT|8883|ZABLOKOWANE (ustawienie domyślne)|ZABLOKOWANE (ustawienie domyślne)|<ul> <li>Skonfiguruj wychodzące (wychodzące) jest otwarte, gdy jako protokół komunikacji przy użyciu protokołu MQTT.<li>1883 dla protokołu MQTT nie jest obsługiwany przez usługi IoT Edge. <li>Połączenia przychodzące (przychodzące) powinien być blokowany.</ul>|
|AMQP|5671|ZABLOKOWANE (ustawienie domyślne)|Otwórz (ustawienie domyślne)|<ul> <li>Protokół komunikacji domyślnego dla usługi IoT Edge. <li> Musi być skonfigurowany jako Otwórz, czy usługi Azure IoT Edge nie jest skonfigurowany dla innych obsługiwanych protokołów AMQP jest protokół komunikacyjny żądaną.<li>5672 dla protokołu AMQP nie jest obsługiwany przez usługi IoT Edge.<li>Zablokować tego portu, w przypadku użycia usługi Azure IoT Edge różne usługi IoT Hub z obsługiwanych protokołów.<li>Połączenia przychodzące (przychodzące) powinien być blokowany.</ul></ul>|
|HTTPS|443|ZABLOKOWANE (ustawienie domyślne)|Otwórz (ustawienie domyślne)|<ul> <li>Konfigurowanie wychodzące (wychodzące) to Open na porcie 443 dla usługi IoT Edge inicjowania obsługi administracyjnej, jest to wymagane, gdy za pomocą skryptów ręczne lub usługi Azure IoT urządzenia inicjowania obsługi usługi (DPS). <li>Połączenia przychodzącego (przychodzące) powinny być otwarte tylko w określonych scenariuszach: <ul> <li>  Jeśli masz z urządzeniami liścia, które może wysyłać żądania do metody przezroczystej bramy. W tym przypadku Port 443 nie musi być otwarty z sieciami zewnętrznymi, aby połączyć się IoTHub lub świadczyć usługi IoTHub za pośrednictwem usługi Azure IoT Edge. Ten sposób przychodzącej reguły może być ograniczony do otwierać tylko przychodzące (przychodzący) z siecią wewnętrzną. <li> Klient scenariuszy dotyczących urządzeń (C2D).</ul><li>80 dla protokołu HTTP nie jest obsługiwany przez usługi IoT Edge.<li>Jeśli nie można skonfigurować protokołów innych niż HTTP (np. protokołu AMQP, MQTT) w przedsiębiorstwie; komunikaty mogą być wysyłane przez protokół WebSockets. Port 443 będzie służyć do komunikacji protokołu WebSocket w takiej sytuacji.</ul>|


## <a name="next-steps"></a>Kolejne kroki
Uważasz, że znaleziono usterkę platformy IoT Edge? [Prześlij problem](https://github.com/Azure/iotedge/issues), aby umożliwić nam naprawę. 

<!-- Images -->
[img-optimize-for-perf]: ./media/troubleshoot/OptimizeForPerformanceFalse.png
