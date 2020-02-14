---
title: Konfigurowanie urządzeń dla serwerów proxy sieci — usługi Azure IoT Edge | Dokumentacja firmy Microsoft
description: Jak skonfigurować środowisko uruchomieniowe usługi Azure IoT Edge i wszystkie moduły usługi IoT Edge Internetu komunikację za pośrednictwem serwera proxy.
author: kgremban
ms.author: kgremban
ms.date: 11/19/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: a8ee1e07dafac46467aa26f89b609cd499346974
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77186580"
---
# <a name="configure-an-iot-edge-device-to-communicate-through-a-proxy-server"></a>Konfigurowanie urządzenia usługi IoT Edge do komunikowania się za pośrednictwem serwera proxy

Urządzenia usługi IoT Edge wysyłał żądania HTTPS do komunikacji z usługą IoT Hub. Jeśli urządzenie jest podłączone do sieci, która korzysta z serwera proxy, należy skonfigurować środowisko uruchomieniowe usługi IoT Edge do komunikowania się za pośrednictwem serwera. Serwery proxy mogą również wpływać na poszczególne moduły IoT Edge, jeśli wysyłają żądania HTTP lub HTTPS, które nie są kierowane przez Centrum IoT Edge.

W tym artykule omówiono następujące cztery kroki konfigurowania urządzenia IoT Edge za serwerem proxy oraz zarządzania nim:

1. **Zainstaluj środowisko uruchomieniowe IoT Edge na urządzeniu.**

   Program IoT Edge tworzy skrypty ściągania pakietów i plików z Internetu, dzięki czemu urządzenie musi się komunikować za pośrednictwem serwera proxy, aby wykonać te żądania. Aby uzyskać szczegółowe instrukcje, zobacz sekcję [Instalowanie środowiska uruchomieniowego za pomocą serwera proxy](#install-the-runtime-through-a-proxy) w tym artykule. W przypadku urządzeń z systemem Windows skrypt instalacji udostępnia również opcję [instalacji w trybie offline](how-to-install-iot-edge-windows.md#offline-or-specific-version-installation) .

   Ten krok to proces jednorazowy wykonywany na urządzeniu IoT Edge podczas jego pierwszego skonfigurowania. Te same połączenia są również wymagane w przypadku aktualizowania środowiska uruchomieniowego IoT Edge.

2. **Skonfiguruj demona platformy Docker i demona IoT Edge na urządzeniu.**

   IoT Edge używa dwóch demonów na urządzeniu, z których oba muszą wykonywać żądania sieci Web za pośrednictwem serwera proxy. Demon IoT Edge jest odpowiedzialny za komunikację z IoT Hub. Demon Moby jest odpowiedzialny za zarządzanie kontenerami, dlatego komunikuje się z rejestrami kontenerów. Szczegółowe instrukcje znajdują się w sekcji [Konfigurowanie demonów](#configure-the-daemons) w tym artykule.

   Ten krok to proces jednorazowy wykonywany na urządzeniu IoT Edge podczas jego pierwszego skonfigurowania.

3. **Skonfiguruj właściwości agenta IoT Edge w pliku config. YAML na urządzeniu.**

   Demon IoT Edge uruchamia początkowo moduł edgeAgent, ale następnie moduł edgeAgent jest odpowiedzialny za pobieranie manifestu wdrożenia z IoT Hub i uruchamianie wszystkich innych modułów. Aby Agent IoT Edge mógł nawiązać początkowe połączenie IoT Hub, ręcznie skonfiguruj zmienne środowiskowe modułu edgeAgent na samym urządzeniu. Po początkowym połączeniu można skonfigurować moduł edgeAgent zdalnie. Szczegółowe instrukcje znajdują się w sekcji [Konfigurowanie agenta IoT Edge](#configure-the-iot-edge-agent) w tym artykule.

   Ten krok to proces jednorazowy wykonywany na urządzeniu IoT Edge podczas jego pierwszego skonfigurowania.

4. **Dla wszystkich przyszłych wdrożeń modułu należy ustawić zmienne środowiskowe dla dowolnego modułu, który komunikuje się za pomocą serwera proxy.**

   Gdy urządzenie IoT Edge zostanie skonfigurowane i połączone z IoT Hub za pomocą serwera proxy, należy zachować połączenie we wszystkich przyszłych wdrożeniach modułów. Szczegółowe instrukcje znajdują się w sekcji [Konfigurowanie manifestów wdrożenia](#configure-deployment-manifests) w tym artykule.

   Ten krok to proces wykonywany zdalnie w taki sposób, że każdy nowy moduł lub aktualizacja wdrożenia utrzymuje zdolność urządzenia do komunikowania się za pomocą serwera proxy.

## <a name="know-your-proxy-url"></a>Znasz adres URL serwera proxy

Przed rozpoczęciem któregokolwiek z kroków opisanych w tym artykule musisz znać adres URL serwera proxy.

Adresy URL serwera proxy mają następujący format: **Protokół**://**proxy_host**:**proxy_port**.

* **Protokół** to http lub https. Demon platformy Docker może używać dowolnego protokołu, w zależności od ustawień rejestru kontenerów, ale IoT Edge w celu nawiązania połączenia z serwerem proxy należy używać protokołu HTTP.

* **Proxy_host** jest adresem serwera proxy. Jeśli serwer proxy wymaga uwierzytelnienia, możesz podać swoje poświadczenia jako część hosta proxy w następującym formacie: **User**:**Password**\@**proxy_host**.

* **Proxy_port** jest portem sieciowym, w którym serwer proxy reaguje na ruch sieciowy.

## <a name="install-the-runtime-through-a-proxy"></a>Instalowanie środowiska uruchomieniowego za pomocą serwera proxy

Niezależnie od tego, czy urządzenie IoT Edge działa w systemie Windows, czy Linux, musisz uzyskać dostęp do pakietów instalacyjnych za pomocą serwera proxy. W zależności od używanego systemu operacyjnego postępuj zgodnie z instrukcjami, aby zainstalować środowisko uruchomieniowe IoT Edge za pomocą serwera proxy.

### <a name="linux-devices"></a>Urządzeniami z systemem Linux

Jeśli instalujesz środowisko uruchomieniowe usługi IoT Edge na urządzeniu z systemem Linux, należy skonfigurować Menedżera pakietów przechodzić przez serwer proxy do dostępu do pakietu instalacyjnego. Na przykład [Skonfiguruj apt-get do korzystania z protokołu HTTP-proxy](https://help.ubuntu.com/community/AptGet/Howto/#Setting_up_apt-get_to_use_a_http-proxy). Po skonfigurowaniu Menedżera pakietów postępuj zgodnie z instrukcjami w temacie [Install Azure IoT Edge Runtime on Linux](how-to-install-iot-edge-linux.md) w zwykły sposób.

### <a name="windows-devices"></a>Urządzenia Windows

Jeśli instalujesz środowisko uruchomieniowe IoT Edge na urządzeniu z systemem Windows, musisz dwa razy przejść przez serwer proxy. Pierwsze połączenie pobiera plik skryptu Instalatora, a drugie podczas instalacji, aby pobrać niezbędne składniki. Informacje o serwerze proxy można skonfigurować w ustawieniach systemu Windows lub dołączyć informacje o serwerze proxy bezpośrednio w poleceniach programu PowerShell.

Poniższe kroki przedstawiają przykład instalacji systemu Windows przy użyciu argumentu `-proxy`:

1. Polecenie Invoke-WebRequest wymaga informacji o serwerze proxy w celu uzyskania dostępu do skryptu Instalatora. Następnie polecenie Deploy-IoTEdge wymaga informacji o serwerze proxy do pobrania plików instalacyjnych.

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Deploy-IoTEdge -proxy <proxy URL>
   ```

2. Polecenie Initialize-IoTEdge nie musi przechodzić przez serwer proxy, więc drugi krok wymaga tylko informacji o serwerze proxy dla elementu Invoke-WebRequest.

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Initialize-IoTEdge
   ```

Jeśli masz skomplikowane poświadczenia dla serwera proxy, którego nie można uwzględnić w adresie URL, użyj parametru `-ProxyCredential` w `-InvokeWebRequestParameters`. Na przykład:

```powershell
$proxyCredential = (Get-Credential).GetNetworkCredential()
. {Invoke-WebRequest -proxy <proxy URL> -ProxyCredential $proxyCredential -useb aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge -InvokeWebRequestParameters @{ '-Proxy' = '<proxy URL>'; '-ProxyCredential' = $proxyCredential }
```

Aby uzyskać więcej informacji na temat parametrów serwera proxy, zobacz [Invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest). Aby uzyskać więcej informacji o opcjach instalacji systemu Windows, w tym o instalacji w trybie offline, zobacz [Instalowanie programu Azure IoT Edge Runtime w systemie Windows](how-to-install-iot-edge-windows.md).

## <a name="configure-the-daemons"></a>Konfigurowanie demonów

IoT Edge opiera się na dwóch demonach uruchomionych na urządzeniu IoT Edge. Demon Moby wykonuje żądania sieci Web w celu ściągania obrazów kontenerów z rejestrów kontenerów. Demon usługi IoT Edge zgłasza żądania sieci web do komunikowania się z usługą IoT Hub.

Zarówno Moby, jak i demon IoT Edge muszą być skonfigurowane do korzystania z serwera proxy na potrzeby ciągłej funkcjonalności urządzenia. Ten krok odbywa się na urządzeniu IoT Edge podczas początkowego konfigurowania urządzenia.

### <a name="moby-daemon"></a>Demon Moby

Ponieważ Moby jest oparty na platformie Docker, zapoznaj się z dokumentacją platformy Docker, aby skonfigurować demona Moby ze zmiennymi środowiskowymi. Większość rejestrów kontenerów (w tym DockerHub i rejestrów kontenerów platformy Azure) obsługuje żądania HTTPS, więc parametr, który należy ustawić, jest **HTTPS_PROXY**. W przypadku ściągania obrazów z rejestru, który nie obsługuje protokołu TLS (Transport Layer Security), należy ustawić parametr **HTTP_PROXY** .

Wybierz artykuł dotyczący systemu operacyjnego urządzenia IoT Edge:

* [Konfigurowanie demona platformy Docker w systemie Linux](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy) Demon Moby na urządzeniach z systemem Linux utrzymuje nazwę Docker.
* [Konfigurowanie demona platformy Docker w systemie Windows](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon#proxy-configuration) Demon Moby na urządzeniach z systemem Windows ma nazwę iotedge-Moby. Nazwy różnią się, ponieważ możliwe jest uruchamianie na urządzeniu z systemem Windows zarówno oprogramowania Docker, jak i Moby.

### <a name="iot-edge-daemon"></a>Demon usługi IoT Edge

Demon IoT Edge jest konfigurowany w podobny sposób do demona Moby. Wykonaj następujące kroki, aby ustawić zmienną środowiskową dla usługi, w oparciu o system operacyjny.

Demon IoT Edge zawsze wysyła żądania do IoT Hub przy użyciu protokołu HTTPS.

#### <a name="linux"></a>Linux

Otwórz Edytor w terminalu, aby skonfigurować demon usługi IoT Edge.

```bash
sudo systemctl edit iotedge
```

Wprowadź następujący tekst, zastępując adres **URL\<proxy >** z adresem i portem serwera proxy. Następnie zapisz i zamknij.

```ini
[Service]
Environment="https_proxy=<proxy URL>"
```

Odśwież Menedżera usług, aby pobrać nową konfigurację dla IoT Edge.

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

#### <a name="windows"></a>System Windows

Otwórz okno programu PowerShell jako administrator i uruchom następujące polecenie, aby edytować rejestr przy użyciu nowej zmiennej środowiskowej. Zastąp **\<adres URL serwera proxy >** adresem i portem serwera proxy.

```powershell
reg add HKLM\SYSTEM\CurrentControlSet\Services\iotedge /v Environment /t REG_MULTI_SZ /d https_proxy=<proxy URL>
```

Uruchom ponownie IoT Edge, aby zmiany zaczęły obowiązywać.

```powershell
Restart-Service iotedge
```

## <a name="configure-the-iot-edge-agent"></a>Konfigurowanie agenta IoT Edge

Agent IoT Edge to pierwszy moduł do uruchomienia na dowolnym IoT Edge urządzeniu. Jest ona uruchomiona po raz pierwszy, na podstawie informacji w pliku config.yaml usługi IoT Edge. Agent IoT Edge następnie łączy się z IoT Hub, aby pobrać manifesty wdrożenia, które deklarują, jakie inne moduły należy wdrożyć na urządzeniu.

Ten krok odbywa się raz na urządzeniu IoT Edge podczas początkowego konfigurowania urządzenia.

1. Otwórz plik config.yaml na urządzeniu usługi IoT Edge. W systemach Linux ten plik znajduje się w lokalizacji **/etc/iotedge/config.YAML**. W systemach Windows ten plik znajduje się w lokalizacji **C:\ProgramData\iotedge\config.YAML**. Plik konfiguracji jest chroniony, dzięki czemu niezbędne są uprawnienia administracyjne do niego dostęp. W systemach Linux Użyj polecenia `sudo` przed otwarciem pliku w preferowanym edytorze tekstu. W systemie Windows otwórz Edytor tekstu, na przykład Notepad jako administrator, a następnie otwórz plik.

2. W pliku config. YAML Znajdź sekcję **specyfikacji modułu agenta brzegowego** . Definicja agenta IoT Edge zawiera parametr **ENV** , w którym można dodać zmienne środowiskowe.

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

Po skonfigurowaniu urządzenia IoT Edge do pracy z serwerem proxy należy nadal deklarować zmienne środowiskowe w przyszłych manifestach wdrożenia. Można edytować manifesty wdrożenia za pomocą Kreatora Azure Portal lub edytując plik JSON manifestu wdrożenia.

Należy zawsze skonfigurować dwa moduły środowiska uruchomieniowego, edgeAgent i edgeHub, aby komunikować się za pomocą serwera proxy, aby mogli obsługiwać połączenie z IoT Hub. W przypadku usunięcia informacji o serwerze proxy z modułu edgeAgent jedynym sposobem ponownego nawiązania połączenia jest edytowanie pliku config. YAML na urządzeniu zgodnie z opisem w poprzedniej sekcji.

Inne moduły IoT Edge, które łączą się z Internetem, powinny być skonfigurowane tak, aby komunikować się za pomocą serwera proxy. Jednak moduły, które kierują swoje wiadomości za pomocą edgeHub lub komunikują się tylko z innymi modułami na urządzeniu, nie potrzebują szczegółów serwera proxy.

Ten krok jest realizowany w całym okresie użytkowania urządzenia IoT Edge.

### <a name="azure-portal"></a>Portalu Azure

W przypadku tworzenia wdrożeń dla IoT Edge urządzeń za pomocą kreatora **ustawiania modułów** każdy moduł zawiera sekcję **zmienne środowiskowe** , za pomocą której można skonfigurować połączenia z serwerem proxy.

Aby skonfigurować agenta IoT Edge i moduły IoT Edge Hub, wybierz pozycję **Ustawienia środowiska uruchomieniowego** w pierwszym kroku kreatora.

![Skonfiguruj ustawienia zaawansowane środowisko uruchomieniowe usługi Edge](./media/how-to-configure-proxy-support/configure-runtime.png)

Dodaj zmienną środowiskową **HTTPS_PROXY** do definicji modułu IoT Edge agent i IoT Edge. Jeśli dołączono zmienną środowiskową **UpstreamProtocol** w pliku config. YAML na urządzeniu IoT Edge, należy dodać ją do definicji modułu IoT Edge agenta.

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
            "value": "http://proxy.example.com:3128"
        }
    },
    "status": "running",
    "restartPolicy": "always"
}
```

Jeśli w pliku config. YAML na urządzeniu IoT Edge została uwzględniona zmienna środowiskowa **UpstreamProtocol** , należy dodać ją do definicji modułu IoT Edge agenta.

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

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o rolach [środowiska uruchomieniowego IoT Edge](iot-edge-runtime.md).

Rozwiązywanie problemów z błędami instalacji i konfiguracji wraz z [typowymi problemami i rozwiązaniami dotyczącymi Azure IoT Edge](troubleshoot.md)
