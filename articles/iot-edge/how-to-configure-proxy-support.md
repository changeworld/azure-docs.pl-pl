---
title: Konfigurowanie urządzeń dla serwerów proxy sieci — usługi Azure IoT Edge | Dokumentacja firmy Microsoft
description: Jak skonfigurować środowisko uruchomieniowe usługi Azure IoT Edge i wszystkie moduły usługi IoT Edge Internetu komunikację za pośrednictwem serwera proxy.
author: kgremban
manager: ''
ms.author: kgremban
ms.date: 06/05/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 1c0da1a768b894f543b9089643622c31d6a8758d
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2019
ms.locfileid: "66730145"
---
# <a name="configure-an-iot-edge-device-to-communicate-through-a-proxy-server"></a>Konfigurowanie urządzenia usługi IoT Edge do komunikowania się za pośrednictwem serwera proxy

Urządzenia usługi IoT Edge wysyłał żądania HTTPS do komunikacji z usługą IoT Hub. Jeśli urządzenie jest podłączone do sieci, która korzysta z serwera proxy, należy skonfigurować środowisko uruchomieniowe usługi IoT Edge do komunikowania się za pośrednictwem serwera. Serwery proxy może również wpływać na poszczególne moduły usługi IoT Edge jeśli dokonają żądań HTTP lub HTTPS, które nie są kierowane za pośrednictwem Centrum usługi IoT Edge. 

Ten artykuł przeprowadzi poniższe cztery kroki, aby skonfigurować, a następnie Zarządzaj urządzenia usługi IoT Edge za serwerem proxy: 

1. **Zainstaluj środowisko uruchomieniowe usługi IoT Edge na urządzeniu.**

   Skrypty instalacji usługi IoT Edge ściągania pakiety i pliki z Internetu, więc urządzenie musi komunikować się za pośrednictwem serwera proxy, aby wprowadzić te żądania. Aby uzyskać szczegółowe instrukcje, zobacz [zainstalować środowisko uruchomieniowe za pośrednictwem serwera proxy](#install-the-runtime-through-a-proxy) dalszej części tego artykułu. W przypadku urządzeń Windows udostępnia również skrypt instalacji [instalacji w trybie Offline](how-to-install-iot-edge-windows.md#offline-installation) opcji. 

   Ten krok to proces jednorazowy przeprowadzone na urządzeniu usługi IoT Edge należy najpierw skonfigurować go. Tych samych połączeń są również wymagane, gdy aktualizujesz środowiska uruchomieniowego usługi IoT Edge. 

2. **Skonfiguruj demona platformy Docker i demona usługi IoT Edge na urządzeniu.**

   Usługi IoT Edge korzysta z dwóch demonów na urządzeniu, które należy do wysyłania żądań sieci web za pośrednictwem serwera proxy. Demon usługi IoT Edge jest odpowiedzialny za komunikację z usługą IoT Hub. Demon Moby jest odpowiedzialny za zarządzanie kontenerem, więc komunikuje się z rejestrów kontenerów. Aby uzyskać szczegółowe instrukcje, zobacz [skonfigurować demonów](#configure-the-daemons) dalszej części tego artykułu. 

   Ten krok to proces jednorazowy przeprowadzone na urządzeniu usługi IoT Edge należy najpierw skonfigurować go.

3. **Konfigurowanie właściwości agenta usługi IoT Edge w pliku config.yaml na urządzeniu.**

   Początkowo moduł edgeAgent uruchamia demona usługi IoT Edge, ale następnie modułu edgeAgent jest odpowiedzialny za pobieranie manifestu wdrażania z usługi IoT Hub i od innych modułów. Dla agenta usługi IoT Edge do nawiązania początkowego połączenia do usługi IoT Hub należy ręcznie skonfigurować zmienne środowiskowe modułu edgeAgent na samym urządzeniu. Po zainicjowaniu połączenia można skonfigurować moduł edgeAgent zdalnie. Aby uzyskać szczegółowe instrukcje, zobacz [konfigurowania agenta usługi IoT Edge](#configure-the-iot-edge-agent) dalszej części tego artykułu.

   Ten krok to proces jednorazowy przeprowadzone na urządzeniu usługi IoT Edge należy najpierw skonfigurować go.

4. **We wszystkich wdrożeniach przyszłych modułu należy ustawić zmienne środowiskowe dla każdego modułu, które komunikują się za pośrednictwem serwera proxy.**

   Po urządzenia usługi IoT Edge została ustawiona i podłączone do usługi IoT Hub za pośrednictwem serwera proxy, należy zachować połączenie we wszystkich wdrożeniach przyszłych modułu. Aby uzyskać szczegółowe instrukcje, zobacz [skonfigurować manifesty wdrożenia](#configure-deployment-manifests) dalszej części tego artykułu. 

   Ten krok jest procesem stałym wykonana zdalnie, dzięki czemu w każdej nowej aktualizacji modułu lub wdrożenie obsługuje możliwość urządzenia komunikują się za pośrednictwem serwera proxy. 

## <a name="know-your-proxy-url"></a>Znasz adres URL serwera proxy

Przed przystąpieniem do wykonywania wszystkich kroków opisanych w tym artykule, musisz znać adres URL serwera proxy.

Adresy URL serwera proxy, wykonaj następujący format: **protokołu**://**proxy_host**:**proxy_port**.

* **Protokołu** protokół HTTP lub HTTPS. Demona platformy Docker można użyć dowolnego z tych protokołów, w zależności od ustawień rejestru kontenerów, ale kontenery demona i środowisko uruchomieniowe usługi IoT Edge należy zawsze używać protokołu HTTPS.

* **Proxy_host** nie jest adresem serwera proxy. Serwer proxy wymaga uwierzytelniania, można podać swoje poświadczenia w ramach hosta serwera proxy, w następującym formacie: **użytkownika**:**hasło**\@**proxy_host** .

* **Proxy_port** jest port sieci, w którym serwer proxy reaguje na ruch sieciowy.

## <a name="install-the-runtime-through-a-proxy"></a>Instalowanie środowiska uruchomieniowego za pośrednictwem serwera proxy

Czy urządzenia usługi IoT Edge działa w systemie Windows lub Linux, należy otworzyć pakiety instalacyjne, za pośrednictwem serwera proxy. W zależności od systemu operacyjnego wykonaj kroki, aby zainstalować środowisko uruchomieniowe usługi IoT Edge za pośrednictwem serwera proxy. 

### <a name="linux"></a>Linux

Jeśli instalujesz środowisko uruchomieniowe usługi IoT Edge na urządzeniu z systemem Linux, należy skonfigurować Menedżera pakietów przechodzić przez serwer proxy do dostępu do pakietu instalacyjnego. Na przykład [ustawiane polecenia apt-get, aby używać serwera proxy http](https://help.ubuntu.com/community/AptGet/Howto/#Setting_up_apt-get_to_use_a_http-proxy). Po skonfigurowaniu usługi Menedżer pakietów, postępuj zgodnie z instrukcjami [runtime Instalowanie usługi Azure IoT Edge w systemie Linux (ARM32v7/armhf)](how-to-install-iot-edge-linux-arm.md) lub [zainstalować środowisko uruchomieniowe usługi Azure IoT Edge w systemie Linux (x64)](how-to-install-iot-edge-linux.md) w zwykły sposób.

### <a name="windows"></a>Windows

Jeśli instalujesz środowisko uruchomieniowe usługi IoT Edge na urządzeniu z systemem Windows, musisz przejść przez serwer proxy dwa razy. Pierwszego połączenia spowoduje pobranie pliku skryptu Instalatora, a drugie połączenie jest podczas instalacji, aby pobrać niezbędne składniki. Możesz skonfigurować informacje o serwerze proxy w ustawieniach Windows lub zawierają swoje informacje o serwerze proxy bezpośrednio w poleceniach programu PowerShell. 

Następująca procedura przedstawia przykład instalacji systemu windows za pomocą `-proxy` argumentu:

1. Polecenie Invoke-WebRequest wymaga informacji serwera proxy do dostępu do skryptu Instalatora. Polecenie Wdróż IoTEdge musi informacje o serwerze proxy, aby pobrać pliki instalacyjne. 

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Deploy-IoTEdge -proxy <proxy URL>
   ```

2. Polecenie IoTEdge inicjowanie nie musi przechodzić przez serwer proxy, więc drugi etap wymaga jedynie informacje o serwerze proxy dla Invoke-WebRequest.

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Initialize-IoTEdge

If you have complicated credentials for the proxy server that can't be included in the URL, use the `-ProxyCredential` parameter within `-InvokeWebRequestParameters`. For example,

```powershell
$proxyCredential = (Get-Credential).GetNetworkCredential()
. {Invoke-WebRequest -proxy <proxy URL> -ProxyCredential $proxyCredential -useb aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge -InvokeWebRequestParameters @{ '-Proxy' = '<proxy URL>'; '-ProxyCredential' = $proxyCredential }
```

Aby uzyskać więcej informacji na temat parametrów serwera proxy, zobacz [Invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest). Aby uzyskać więcej informacji na temat opcji instalacji Windows, w tym instalacji w trybie offline, zobacz [runtime Instalowanie usługi Azure IoT Edge w Windows](how-to-install-iot-edge-windows.md).

## <a name="configure-the-daemons"></a>Konfigurowanie demonów

Usługi IoT Edge opiera się na dwóch demonów uruchomionych na urządzeniu usługi IoT Edge. Demon Moby wysyła żądania sieci web do Ściągnij obrazy kontenerów z rejestrów kontenerów. Demon usługi IoT Edge zgłasza żądania sieci web do komunikowania się z usługą IoT Hub.

Moby i demonów usługi IoT Edge, muszą być skonfigurowana do używania serwera proxy dla urządzenia w trwające funkcji. W tym kroku odbywa się na urządzeniu usługi IoT Edge podczas początkowego konfigurowania urządzenia. 

### <a name="moby-daemon"></a>Demon Moby

Ponieważ Moby jest oparta na platformy Docker, zapoznaj się z dokumentacją platformy Docker do skonfigurowania demona Moby za pomocą zmiennych środowiskowych. Większość rejestry kontenerów (w tym DockerHub i rejestry kontenerów platformy Azure) obsługuje żądania HTTPS, więc jest parametr, który należy ustawić **HTTPS_PROXY**. Jeśli masz ściąganie obrazów z rejestru, który nie obsługuje transport layer security (TLS), a następnie należy skonfigurować **że** parametru. 

Wybierz artykuł, która ma zastosowanie do systemu operacyjnego urządzenia usługi IoT Edge: 

* [Konfigurowanie demona platformy Docker w systemie Linux](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy)
    * Demon Moby na urządzeniach z systemem Linux przechowuje nazwę platformy Docker.
* [Skonfiguruj demona platformy Docker na Windows](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon#proxy-configuration)
    * Demon Moby na urządzeniach Windows nosi nazwę iotedge moby. Nazwy są różne, ponieważ istnieje możliwość uruchomienia pulpitu platformy Docker i Moby równolegle na urządzeniu z systemem Windows. 

### <a name="iot-edge-daemon"></a>Demon usługi IoT Edge

Demon usługi IoT Edge jest skonfigurowany w sposób podobny do demona Moby. Wykonaj następujące kroki, aby ustawić zmienną środowiskową dla usługi, w oparciu o system operacyjny. 

Demon usługi IoT Edge zawsze używa protokołu HTTPS do wysyłania żądań do usługi IoT Hub.

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

W tym kroku odbywa się raz na urządzeniu usługi IoT Edge podczas początkowego konfigurowania urządzenia. 

1. Otwórz plik config.yaml na urządzeniu usługi IoT Edge. W systemach Linux, ten plik znajduje się w **/etc/iotedge/config.yaml**. W systemach Windows, ten plik znajduje się w **C:\ProgramData\iotedge\config.yaml**. Plik konfiguracji jest chroniony, dzięki czemu niezbędne są uprawnienia administracyjne do niego dostęp. W systemach Linux, należy użyć `sudo` polecenia przed otwarciem pliku w preferowanym edytorze tekstu. W Windows otwórz Edytor tekstu, takiego jak Notatnik jako administrator, a następnie otwórz plik. 

2. W pliku config.yaml Znajdź **Specyfikacja modułu Agent usługi Edge** sekcji. Definicja agenta usługi IoT Edge obejmuje **env** parametru, w którym można dodać zmienne środowiskowe. 

3. Usuń nawiasów klamrowych, które są symbole zastępcze dla parametru env, a następnie dodaj nową zmienną w nowym wierszu. Należy pamiętać, że wcięcia w YAML są dwie spacje. 

   ```yaml
   https_proxy: "<proxy URL>"
   ```

4. Środowisko uruchomieniowe usługi IoT Edge domyślnie używa protokołu AMQP w na komunikowanie się z Centrum IoT Hub. Niektóre serwery proxy blokować porty protokołu AMQP. Jeśli tak jest rzeczywiście, również należy skonfigurować edgeAgent użycie połączeń AMQP przez WebSocket. Dodaj drugi zmiennej środowiskowej.

   ```yaml
   UpstreamProtocol: "AmqpWs"
   ```

   ![Definicja edgeAgent ze zmiennymi środowiskowymi](./media/how-to-configure-proxy-support/edgeagent-edited.png)

5. Config.yaml zapisać zmiany i zamknąć Edytor. Uruchom ponownie IoT Edge, aby zmiany zaczęły obowiązywać. 

   * W systemie Linux: 

      ```bash
      sudo systemctl restart iotedge
      ```

   * W systemie Windows:

      ```powershell
      Restart-Service iotedge
      ```

## <a name="configure-deployment-manifests"></a>Konfigurowanie manifestów wdrożenia  

Po urządzenia usługi IoT Edge jest skonfigurowane do pracy z serwerem proxy, należy zadeklarować zmienne środowiskowe w przyszłości manifesty wdrożenia w dalszym ciągu. Możesz edytować manifestów wdrożenia, albo za pomocą Kreatora portalu Azure lub edytując wdrożenia plik JSON manifestu. 

Zawsze należy skonfigurować dwa moduły środowiska uruchomieniowego, edgeAgent i edgeHub do komunikowania się za pośrednictwem serwera proxy, dzięki czemu utrzymują one połączenia z usługą IoT Hub. Jeśli informacje o serwerze proxy zostanie usunięty z modułu edgeAgent, jedynym sposobem, aby ponownie ustanowić połączenia jest, edytując plik config.yaml na urządzeniu, zgodnie z opisem w poprzedniej sekcji. 

Inne moduły usługi IoT Edge, które łączą się z Internetem, należy skonfigurować do komunikowania się za pośrednictwem serwera proxy za. Jednakże moduły, rozsyłania spamu edgeHub lub które komunikują się tylko z innymi modułami na urządzeniu nie ma potrzeby szczegółów serwera proxy. 

Ten krok jest ciągły w całym cyklu życia urządzenia usługi IoT Edge. 

### <a name="azure-portal"></a>Azure Portal

Kiedy używać **Ustaw moduły** kreatora w celu utworzenia wdrożenia dla usługi IoT Edge urządzenia, co moduł ma **zmienne środowiskowe** sekcja, która służy do konfigurowania połączeń z serwerem proxy. 

Aby skonfigurować agenta usługi IoT Edge i moduły Centrum usługi IoT Edge, wybierz **skonfiguruj zaawansowane ustawienia środowiska uruchomieniowego Edge** w pierwszym kroku kreatora. 

![Skonfiguruj ustawienia zaawansowane środowisko uruchomieniowe usługi Edge](./media/how-to-configure-proxy-support/configure-runtime.png)

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

