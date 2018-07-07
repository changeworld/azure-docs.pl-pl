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
ms.openlocfilehash: 4862e3aa976287512fd69fdfe9295e3f3328d5a7
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/06/2018
ms.locfileid: "37887780"
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
   sort-object @{Expression="TimeCreated";Descending=$false}
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

Przejrzyj komunikaty przechodzące przez Centrum usługi Edge i Zbierz analizy dotyczące aktualizacji właściwości urządzenia za pomocą szczegółowych dzienników z kontenerów środowiska wykonawczego edgeAgent i edgeHub. Aby włączyć pełne dzienniki w tych kontenerach, należy ustawić `RuntimeLogLevel` zmienną środowiskową: 

W systemie Linux:
    
   ```cmd
   export RuntimeLogLevel="debug"
   ```
    
W systemie Windows:
    
   ```powershell
   [Environment]::SetEnvironmentVariable("RuntimeLogLevel", "debug")
   ```

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

## <a name="next-steps"></a>Kolejne kroki
Uważasz, że znaleziono usterkę platformy IoT Edge? [Prześlij problem](https://github.com/Azure/iotedge/issues), aby umożliwić nam naprawę. 

<!-- Images -->
[img-optimize-for-perf]: ./media/troubleshoot/OptimizeForPerformanceFalse.png