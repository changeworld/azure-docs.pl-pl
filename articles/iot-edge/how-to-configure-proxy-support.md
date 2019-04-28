---
title: Konfigurowanie urządzeń dla serwerów proxy sieci — usługi Azure IoT Edge | Dokumentacja firmy Microsoft
description: Jak skonfigurować środowisko uruchomieniowe usługi Azure IoT Edge i wszystkie moduły usługi IoT Edge Internetu komunikację za pośrednictwem serwera proxy.
author: kgremban
manager: ''
ms.author: kgremban
ms.date: 03/20/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 4fa5402b87eea969a5a4093000dda06d3cb5675d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61216241"
---
# <a name="configure-an-iot-edge-device-to-communicate-through-a-proxy-server"></a>Konfigurowanie urządzenia usługi IoT Edge do komunikowania się za pośrednictwem serwera proxy

Urządzenia usługi IoT Edge wysyłał żądania HTTPS do komunikacji z usługą IoT Hub. Jeśli urządzenie jest podłączone do sieci, która korzysta z serwera proxy, należy skonfigurować środowisko uruchomieniowe usługi IoT Edge do komunikowania się za pośrednictwem serwera. Serwery proxy może również wpływać na poszczególne moduły usługi IoT Edge jeśli dokonają żądań HTTP lub HTTPS, które nie są kierowane za pośrednictwem Centrum usługi IoT Edge. 

Konfigurowanie urządzenia usługi IoT Edge do pracy z serwerem proxy obejmuje następujące podstawowe kroki: 

1. Zainstaluj środowisko uruchomieniowe usługi IoT Edge na urządzeniu. 
2. Skonfiguruj demona platformy Docker i demona usługi IoT Edge na urządzeniu, aby używać serwera proxy.
3. Konfigurowanie właściwości edgeAgent w pliku config.yaml na urządzeniu.
4. Ustaw zmienne środowiskowe dla optymalizacji środowiska uruchomieniowego usługi IoT Edge i inne usługi IoT Edge modułów w pliku manifestu wdrożenia.

## <a name="know-your-proxy-url"></a>Znasz adres URL serwera proxy

Aby skonfigurować zarówno demona platformy Docker, jak i usługi IoT Edge na urządzeniu, musisz znać adres URL serwera proxy.

Adresy URL serwera proxy, wykonaj następujący format: **protokołu**://**proxy_host**:**proxy_port**.

* **Protokołu** protokół HTTP lub HTTPS. Demona platformy Docker można użyć dowolnego z tych protokołów, w zależności od ustawień rejestru kontenerów, ale kontenery demona i środowisko uruchomieniowe usługi IoT Edge należy zawsze używać protokołu HTTPS.

* **Proxy_host** nie jest adresem serwera proxy. Serwer proxy wymaga uwierzytelniania, można podać swoje poświadczenia w ramach hosta serwera proxy, w następującym formacie: **użytkownika**:**hasło**\@**proxy_host** .

* **Proxy_port** jest port sieci, w którym serwer proxy reaguje na ruch sieciowy.

## <a name="install-the-runtime"></a>Instalowanie środowiska uruchomieniowego

Jeśli instalujesz środowisko uruchomieniowe usługi IoT Edge na urządzeniu z systemem Linux, należy skonfigurować Menedżera pakietów przechodzić przez serwer proxy do dostępu do pakietu instalacyjnego. Na przykład [ustawiane polecenia apt-get, aby używać serwera proxy http](https://help.ubuntu.com/community/AptGet/Howto/#Setting_up_apt-get_to_use_a_http-proxy). Po skonfigurowaniu usługi Menedżer pakietów, postępuj zgodnie z instrukcjami [runtime Instalowanie usługi Azure IoT Edge w systemie Linux (ARM32v7/armhf)](how-to-install-iot-edge-linux-arm.md) lub [zainstalować środowisko uruchomieniowe usługi Azure IoT Edge w systemie Linux (x64)](how-to-install-iot-edge-linux.md) w zwykły sposób.

Jeśli instalujesz środowisko uruchomieniowe usługi IoT Edge na urządzeniu z systemem Windows, musisz przejść przez serwer proxy dwa razy. Pierwsze połączenie jest pobranie pliku skryptu Instalatora, a drugie połączenie jest podczas instalacji, aby pobrać niezbędne składniki. Możesz skonfigurować informacje o serwerze proxy w ustawieniach Windows lub zawierają swoje informacje o serwerze proxy bezpośrednio w skrypcie instalacji. Poniższy skrypt programu powershell jest przykładem instalacji systemu windows za pomocą `-proxy` argumentu:

```powershell
. {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; `
Install-SecurityDaemon -Manual -ContainerOs Windows -proxy <proxy URL>
```

Jeśli masz skomplikowane poświadczenia dla serwera proxy, którego nie można uwzględnić w adresie URL, użyj `-ProxyCredential` parametru w `-InvokeWebRequestParameters`. Na przykład:

```powershell
$proxyCredential = (Get-Credential).GetNetworkCredential()
. {Invoke-WebRequest -proxy <proxy URL> -ProxyCredential $proxyCredential -useb aka.ms/iotedge-win} | Invoke-Expression; `
Install-SecurityDaemon -Manual -ContainerOs Windows -InvokeWebRequestParameters @{ '-Proxy' = '<proxy URL>'; '-ProxyCredential' = $proxyCredential }
```

Aby uzyskać więcej informacji na temat parametrów serwera proxy, zobacz [Invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest). Aby uzyskać więcej informacji na temat opcji instalacji, zobacz [runtime Instalowanie usługi Azure IoT Edge w Windows](how-to-install-iot-edge-windows.md).

Po zainstalowaniu środowiska uruchomieniowego usługi IoT Edge następująca sekcja służy do jego konfiguracji z informacjami o serwera proxy. 

## <a name="configure-the-daemons"></a>Konfigurowanie demonów

Demony Moby i usługi IoT Edge na urządzeniu usługi IoT Edge z muszą być skonfigurowane do używania serwera proxy. Demon Moby wysyła żądania sieci web do Ściągnij obrazy kontenerów z rejestrów kontenerów. Demon usługi IoT Edge zgłasza żądania sieci web do komunikowania się z usługą IoT Hub.

### <a name="moby-daemon"></a>Demon Moby

Ponieważ Moby jest oparta na platformy Docker, zapoznaj się z dokumentacją platformy Docker do skonfigurowania demona Moby za pomocą zmiennych środowiskowych. Większość rejestry kontenerów (w tym DockerHub i rejestry kontenerów platformy Azure) obsługuje żądania HTTPS, więc jest parametr, który należy ustawić **HTTPS_PROXY**. Jeśli masz ściąganie obrazów z rejestru, który nie obsługuje transport layer security (TLS), a następnie należy skonfigurować **że** parametru. 

Wybierz artykuł, która ma zastosowanie do systemu operacyjnego urządzenia usługi IoT Edge: 

* [Konfigurowanie demona platformy Docker w systemie Linux](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy)
    * Demon Moby na urządzeniach z systemem Linux przechowuje nazwę platformy Docker.
* [Skonfiguruj demona platformy Docker na Windows](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon#proxy-configuration)
    * Demon Moby na urządzeniach Windows nosi nazwę iotedge moby. Nazwy są różne, ponieważ istnieje możliwość uruchomienia pulpitu platformy Docker i Moby równolegle na urządzeniu z systemem Windows. 

### <a name="iot-edge-daemon"></a>Demon usługi IoT Edge

Demon usługi IoT Edge jest skonfigurowany w sposób podobny do demona Moby. Wszystkie żądania, które usługi IoT Edge wysyła do usługi IoT Hub przy użyciu protokołu HTTPS. Wykonaj następujące kroki, aby ustawić zmienną środowiskową dla usługi, w oparciu o system operacyjny. 

#### <a name="linux"></a>Linux

Otwórz Edytor w terminalu, aby skonfigurować demon usługi IoT Edge. 

```bash
sudo systemctl edit iotedge
```

Wprowadź następujący tekst zastępując  **\<adres URL serwera proxy >** z adres serwera proxy i port. Następnie zapisz i zamknij. 

```ini
[Service]
Environment="https_proxy=<proxy URL>"
```

Odśwież programu service manager w celu zastosowania nowej konfiguracji do usługi IoT Edge.

```bash
sudo systemctl daemon-reload
```

Uruchom ponownie IoT Edge, aby zmiany zaczęły obowiązywać.

```bash
sudo systemctl restart iotedge
```

Sprawdź, czy Twoja zmienna środowiskowa została utworzona oraz Nowa konfiguracja została załadowana. 

```bash
systemctl show --property=Environment iotedge
```

#### <a name="windows"></a>Windows

Otwórz okno programu PowerShell jako administrator i uruchom następujące polecenie, aby edytować rejestr przy użyciu nowej zmiennej środowiskowej. Zastąp  **\<adres url serwera proxy >** z adres serwera proxy i port. 

```powershell
reg add HKLM\SYSTEM\CurrentControlSet\Services\iotedge /v Environment /t REG_MULTI_SZ /d https_proxy=<proxy URL>
```

Uruchom ponownie IoT Edge, aby zmiany zaczęły obowiązywać.

```powershell
Restart-Service iotedge
```

## <a name="configure-the-iot-edge-agent"></a>Konfigurowanie agenta usługi IoT Edge

Agent usługi IoT Edge jest pierwszego modułu, można uruchomić na dowolnym urządzeniu usługi IoT Edge. Jest ona uruchomiona po raz pierwszy, na podstawie informacji w pliku config.yaml usługi IoT Edge. Agent usługi IoT Edge łączy się następnie z IoT Hub, aby pobrać manifestów wdrożenia, które deklarować, jakie inne moduły powinny być wdrażane na urządzeniu.

Otwórz plik config.yaml na urządzeniu usługi IoT Edge. W systemach Linux, ten plik znajduje się w **/etc/iotedge/config.yaml**. W systemach Windows, ten plik znajduje się w **C:\ProgramData\iotedge\config.yaml**. Plik konfiguracji jest chroniony, dzięki czemu niezbędne są uprawnienia administracyjne do niego dostęp. W systemach Linux, oznacza to, za pomocą `sudo` polecenia przed otwarciem pliku w preferowanym edytorze tekstu. W Windows oznacza to otwarcie edytora tekstów, takiego jak Notatnik aby Uruchom jako administrator, a następnie otwarcie pliku. 

W pliku config.yaml Znajdź **Specyfikacja modułu Agent usługi Edge** sekcji. Definicja agenta usługi IoT Edge obejmuje **env** parametru, w którym można dodać zmienne środowiskowe. 

<!--
![edgeAgent definition](./media/how-to-configure-proxy-support/edgeagent-unedited.png)
-->

Usuń nawiasów klamrowych, które są symbole zastępcze dla parametru env, a następnie dodaj nową zmienną w nowym wierszu. Należy pamiętać, że wcięcia w YAML są dwie spacje. 

```yaml
https_proxy: "<proxy URL>"
```

Środowisko uruchomieniowe usługi IoT Edge domyślnie używa protokołu AMQP w na komunikowanie się z Centrum IoT Hub. Niektóre serwery proxy blokować porty protokołu AMQP. Jeśli tak jest rzeczywiście, również należy skonfigurować edgeAgent użycie połączeń AMQP przez WebSocket. Dodaj drugi zmiennej środowiskowej.

```yaml
UpstreamProtocol: "AmqpWs"
```

![Definicja edgeAgent ze zmiennymi środowiskowymi](./media/how-to-configure-proxy-support/edgeagent-edited.png)

Config.yaml zapisać zmiany i zamknąć Edytor. Uruchom ponownie IoT Edge, aby zmiany zaczęły obowiązywać. 

* W systemie Linux: 

   ```bash
   sudo systemctl restart iotedge
   ```

* W systemie Windows:

   ```powershell
   Restart-Service iotedge
   ```

## <a name="configure-deployment-manifests"></a>Konfigurowanie manifestów wdrożenia  

Po urządzenia usługi IoT Edge jest skonfigurowane do pracy z serwerem proxy, należy zadeklarować zmienne środowiskowe w przyszłości manifesty wdrożenia w dalszym ciągu. Zawsze należy skonfigurować dwa moduły środowiska uruchomieniowego, edgeAgent i edgeHub do komunikowania się za pośrednictwem serwera proxy, dzięki czemu utrzymują one połączenia z usługą IoT Hub. Inne moduły usługi IoT Edge, które łączą się z Internetem, należy skonfigurować dla serwera proxy. Jednakże moduły, rozsyłania spamu edgeHub lub które komunikują się tylko z innymi modułami na urządzeniu nie ma potrzeby szczegółów serwera proxy. 

Można utworzyć manifesty wdrożenia za pomocą witryny Azure portal lub ręcznie przez edycję pliku JSON. 

### <a name="azure-portal"></a>Azure Portal

Kiedy używać **Ustaw moduły** kreatora w celu utworzenia wdrożenia dla usługi IoT Edge urządzenia, co moduł ma **zmienne środowiskowe** sekcja, która służy do konfigurowania połączeń z serwerem proxy. 

Aby skonfigurować agenta usługi IoT Edge i moduły Centrum usługi IoT Edge, wybierz **skonfiguruj zaawansowane ustawienia środowiska uruchomieniowego Edge** w pierwszym kroku kreatora. 

![Konfiguruj zaawansowane ustawienia środowiska uruchomieniowego Edge](./media/how-to-configure-proxy-support/configure-runtime.png)

Dodaj **https_proxy** zmiennej środowiskowej, aby agent usługi IoT Edge i definicje modułów usługi IoT Edge hub. W przypadku dołączenia **UpstreamProtocol** zmiennej środowiskowej w pliku config.yaml na urządzeniu usługi IoT Edge, dodaj ją do definicji modułu agenta usługi IoT Edge za. 

![Ustaw dla zmiennej https_proxy](./media/how-to-configure-proxy-support/edgehub-environmentvar.png)

Wszystkie moduły, które dodajesz do manifestu wdrażania postępuj zgodnie z tym samym wzorcem. Na stronie, gdzie ustawiasz Nazwa modułu i obrazów Brak sekcji zmiennych środowiskowych.

### <a name="json-deployment-manifest-files"></a>Pliki manifestu wdrożenia JSON

Jeśli tworzysz wdrożenia dla usługi IoT Edge urządzenia przy użyciu szablonów w programie Visual Studio Code lub za pomocą ręcznego tworzenia pliki w formacie JSON, można dodać zmienne środowiskowe bezpośrednio do każdego definicji modułu. 

Użyj następującego formatu JSON: 

```json
"env": {
    "https_proxy": {
        "value": "<proxy URL>"
    }
}
```

Zmienne środowiskowe uwzględnione definicji modułu powinien wyglądać jak w poniższym przykładzie edgeHub:

```json
"edgeHub": {
    "type": "docker",
    "settings": {
        "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
        "createOptions": ""
    },
    "env": {
        "https_proxy": {
            "value": "https://proxy.example.com:3128"
        }
    },
    "status": "running",
    "restartPolicy": "always"
}
```

W przypadku dołączenia **UpstreamProtocol** zmiennej środowiskowej w pliku confige.yaml na urządzeniu usługi IoT Edge, dodaj ją do definicji modułu agenta usługi IoT Edge za. 

```json
"env": {
    "https_proxy": {
        "value": "<proxy URL>"
    },
    "UpstreamProtocol": {
        "value": "AmqpWs"
    }
}
```

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o rolach [środowisko uruchomieniowe usługi IoT Edge](iot-edge-runtime.md).

Rozwiązywanie problemów z błędami instalacji i konfiguracji z [typowe problemy i rozwiązania dla usługi Azure IoT Edge](troubleshoot.md)

