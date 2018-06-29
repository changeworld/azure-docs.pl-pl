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
ms.openlocfilehash: efe3e31a1a92e21f2c3a3461deba248d2a8c97fa
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37029445"
---
# <a name="common-issues-and-resolutions-for-azure-iot-edge"></a>Typowe problemy z usługą Azure IoT Edge i ich rozwiązania

Jeśli wystąpią problemy z uruchamianiem usługi Azure IoT Edge w danym środowisku, użyj tego artykułu jako przewodnika ułatwiającego rozwiązywanie problemów. 

## <a name="standard-diagnostic-steps"></a>Standardowe kroki diagnostyki 

W przypadku wystąpienia problemu dowiedz się więcej o stanie urządzenia usługi IoT Edge, przeglądając dzienniki kontenera i komunikaty przekazywane do i z urządzenia. Użyj poleceń i narzędzi w tej sekcji, aby zebrać informacje. 

### <a name="check-the-status-of-the-iot-edge-security-manager-and-its-logs"></a>Sprawdź stan Menedżera zabezpieczeń krawędzi IoT i jej dzienników:

W systemie Linux:
- Aby wyświetlić stan Menedżera zabezpieczeń krawędzi IoT:

   ```bash
   sudo systemctl status iotedge
   ```

- Aby wyświetlić dzienniki Menedżera zabezpieczeń krawędzi IoT:

    ```bash
    sudo journalctl -u iotedge -f
    ```

- Aby wyświetlić bardziej szczegółowe dzienniki Menedżera zabezpieczeń krawędzi IoT:

   - Edytuj ustawienia demon iotedge:

      ```bash
      sudo systemctl edit iotedge.service
      ```
   
   - Zaktualizuj następujące wiersze:
    
      ```
      [Service]
      Environment=IOTEDGE_LOG=edgelet=debug
      ```
    
   - Uruchom ponownie demona zabezpieczeń krawędzi IoT:
    
      ```bash
      sudo systemctl cat iotedge.service
      sudo systemctl daemon-reload
      sudo systemctl restart iotedge
      ```

W systemie Windows:
- Aby wyświetlić stan Menedżera zabezpieczeń krawędzi IoT:

   ```powershell
   Get-Service iotedge
   ```

- Aby wyświetlić dzienniki Menedżera zabezpieczeń krawędzi IoT:

   ```powershell
   # Displays logs from today, newest at the bottom.
 
   Get-WinEvent -ea SilentlyContinue `
   -FilterHashtable @{ProviderName= "iotedged";
     LogName = "application"; StartTime = [datetime]::Today} |
   select TimeCreated, Message |
   sort-object @{Expression="TimeCreated";Descending=$false}
   ```

### <a name="if-the-iot-edge-security-manager-is-not-running-verify-your-yaml-configuration-file"></a>Jeśli Menedżera zabezpieczeń krawędzi IoT nie jest uruchomiona, należy sprawdzić plik konfiguracyjny yaml programu

> [!WARNING]
> Pliki yaml programu nie może zawierać kart jako identation. Zamiast tego użyj spacji 2.

W systemie Linux:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

W systemie Windows:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

### <a name="check-container-logs-for-issues"></a>Sprawdź dzienniki kontenera problemów

Po uruchomieniu demona IoT krawędzi zabezpieczeń, należy znaleźć w dziennikach, kontenerów do wykrywania problemów. Rozpocznij od wdrożonych kontenerów, a następnie sprawdź kontenery, które tworzą środowisko uruchomieniowe usługi IoT Edge: Agent usługi Edge i Centrum usługi Edge. Dzienniki agenta usługi Edge zwykle zawierają informacje o cyklu życia każdego kontenera. Dzienniki centrum usługi Edge zawierają informacje dotyczące obsługi komunikatów i routingu. 

   ```cmd
   iotedge logs <container name>
   ```

### <a name="view-the-messages-going-through-the-edge-hub"></a>Wyświetlanie komunikatów pośrednictwa Centrum krawędzi

Wyświetlanie komunikatów pośrednictwa Centrum Edge i zbierać szczegółowych informacji o aktualizacji właściwości urządzenia z dziennikami verbose z kontenerów środowiska uruchomieniowego edgeAgent i edgeHub. Aby włączyć pełne dzienniki do tych kontenerów, ustaw `RuntimeLogLevel` zmiennej środowiskowej: 

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
Po zbadaniu dzienniki i komunikaty, aby uzyskać informacje, można spróbować uruchomić kontenerów:

```
iotedge restart <container name>
```

Uruchom ponownie kontenery środowiska uruchomieniowego krawędzi IoT:

```
iotedge restart edgeAgent && iotedge restart edgeHub
```

### <a name="restart-the-iot-edge-security-manager"></a>Ponownie uruchom Menedżera zabezpieczeń krawędzi IoT

Jeśli problem nadal jest trwałym, można spróbować uruchomić Menedżera zabezpieczeń IoT krawędzi.

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
Upewnij się, że poświadczenia rejestru są poprawnie określona w manifeście wdrażania

## <a name="iot-edge-security-daemon-fails-with-an-invalid-hostname"></a>Demon zabezpieczeń krawędzi IoT kończy się niepowodzeniem z nieprawidłową nazwę hosta

Polecenie `sudo journalctl -u iotedge` nie powiedzie się i wyświetla następujący komunikat: 

```output
Error parsing user input data: invalid hostname. Hostname cannot be empty or greater than 64 characters
```

### <a name="root-cause"></a>Główna przyczyna
Środowisko uruchomieniowe krawędzi IoT może obsługiwać tylko nazwy hostów, które są krótsze niż 64 znaki. To zwykle nie jest problemem w przypadku komputerów fizycznych, ale może wystąpić podczas konfigurowania środowiska uruchomieniowego na maszynie wirtualnej. Automatycznie generowane nazwy hostów dla maszyn wirtualnych systemu Windows hostowana na platformie Azure, w szczególności, zwykle za długa. 

### <a name="resolution"></a>Rozwiązanie
Gdy zostanie wyświetlony ten błąd, można rozwiązać, konfigurowanie nazwę DNS maszyny wirtualnej, a następnie ustawić nazwę DNS jako nazwa hosta polecenia Instalatora.

1. W portalu Azure przejdź do strony Przegląd maszyny wirtualnej. 
2. Wybierz **skonfigurować** pod nazwą DNS. Maszyna wirtualna ma już skonfigurowaną nazwę DNS, nie trzeba skonfigurować nowy. 

   ![Konfigurowanie nazwy DNS](./media/troubleshoot/configure-dns.png)

3. Podaj wartość dla **etykieta nazwy DNS** i wybierz **zapisać**.
4. Skopiuj nowej nazwy DNS, która powinna być w formacie  **\<DNSnamelabel\>.\< vmlocation\>. cloudapp.azure.com**.
5. Na maszynie wirtualnej Użyj następującego polecenia, aby skonfigurować środowisko uruchomieniowe krawędzi IoT z nazwą DNS:

   - W systemie Linux:

      ```bash
      sudo nano /etc/iotedge/config.yaml
      ```

   - W systemie Windows:

      ```cmd
      notepad C:\ProgramData\iotedge\config.yaml
      ```

## <a name="next-steps"></a>Kolejne kroki
Uważasz, że znaleziono usterkę platformy IoT Edge? [Prześlij problem](https://github.com/Azure/iot-edge/issues), aby umożliwić nam naprawę. 
