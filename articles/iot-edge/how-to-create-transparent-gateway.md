---
title: Tworzenie przezroczystego urządzenia bramy — Azure IoT Edge | Microsoft Docs
description: Używanie urządzenia Azure IoT Edge jako przezroczystej bramy, która może przetwarzać informacje z urządzeń podrzędnych
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c005dcd91412552e2b10c27a7809ca4bc46d4709
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792339"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>Skonfiguruj urządzenie IoT Edge jako niejawną bramę

Ten artykuł zawiera szczegółowe instrukcje dotyczące konfigurowania urządzenia IoT Edge jako przezroczystej bramy dla innych urządzeń w celu komunikowania się z IoT Hub. W tym artykule termin *IoT Edge Brama* odwołuje się do urządzenia IoT Edge używanego jako niewidoczna brama. Aby uzyskać więcej informacji, zobacz [jak urządzenie IoT Edge może być używane jako brama](./iot-edge-as-gateway.md).

>[!NOTE]
>Aktualne
> * Urządzenia z włączoną krawędzią nie mogą łączyć się z bramami IoT Edge. 
> * Urządzenia podrzędne nie mogą używać przekazywania plików.

Należy wykonać trzy ogólne kroki, aby skonfigurować pomyślne, przezroczyste połączenie bramy. W tym artykule omówiono pierwszy krok:

1. **Urządzenie bramy musi być w stanie bezpiecznie łączyć się z urządzeniami podrzędnymi, odbierać komunikaty z urządzeń podrzędnych i kierować komunikaty do odpowiednich miejsc docelowych.**
2. Urządzenie podrzędne musi mieć tożsamość urządzenia, aby można było uwierzytelnić się przy użyciu IoT Hub i wiedzieć o komunikacji za pomocą swojego urządzenia bramy. Aby uzyskać więcej informacji, zobacz [uwierzytelnianie urządzenia podrzędnego w usłudze Azure IoT Hub](how-to-authenticate-downstream-device.md).
3. Urządzenie podrzędne musi mieć możliwość bezpiecznego łączenia się z urządzeniem bramy. Aby uzyskać więcej informacji, zobacz [łączenie urządzenia podrzędnego z bramą Azure IoT Edge](how-to-connect-downstream-device.md).


Aby urządzenie działało jako brama, musi być w stanie bezpiecznie połączyć się z jego urządzeniami podrzędnymi. Azure IoT Edge umożliwia konfigurowanie bezpiecznych połączeń między urządzeniami przy użyciu infrastruktury kluczy publicznych (PKI). W takim przypadku zezwalamy urządzeniu podrzędnemu na łączenie się z urządzeniem IoT Edge działającym jako niejawna brama. Aby zachować uzasadnione zabezpieczenia, urządzenie podrzędne powinno potwierdzić tożsamość urządzenia bramy. To sprawdzenie tożsamości uniemożliwia urządzeniom łączenie się z potencjalnie złośliwymi bramami.

W przypadku niejawnego scenariusza bramy może być dowolna aplikacja lub platforma, która ma tożsamość utworzoną za pomocą usługi [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub) w chmurze. W wielu przypadkach te aplikacje używają [zestawu SDK urządzenia usługi Azure IoT](../iot-hub/iot-hub-devguide-sdks.md). Ze względów praktycznych urządzenie podrzędne może nawet być aplikacją działającą na samym urządzeniu bramy IoT Edge. Jednak urządzenie IoT Edge nie może być niższe niż Brama IoT Edge. 

Można utworzyć dowolną infrastrukturę certyfikatów, która umożliwia zaufanie wymagane dla topologii bramy urządzeń. W tym artykule przyjęto założenie, że ta sama konfiguracja certyfikatu zostanie użyta do włączenia [zabezpieczeń urzędu certyfikacji x. 509](../iot-hub/iot-hub-x509ca-overview.md) w IoT Hub, co obejmuje certyfikat certyfikatu x. 509 skojarzony z określonym Centrum IoT Hub (główny urząd certyfikacji usługi IoT Hub), szereg certyfikatów podpisanych za pomocą tego urzędu certyfikacji oraz Urząd certyfikacji dla IoT Edge urządzenia.

![Konfiguracja certyfikatu bramy](./media/how-to-create-transparent-gateway/gateway-setup.png)

>[!NOTE]
>Termin "główny urząd certyfikacji" używany w tym artykule odnosi się do certyfikatu publicznego urzędu certyfikacji PKI, a nie musi być certyfikatem głównym urzędu certyfikacji. W wielu przypadkach jest to pośredni certyfikat publiczny urzędu certyfikacji. 

Podczas inicjowania połączenia Brama przedstawia certyfikat urzędu certyfikacji urządzenia IoT Edge do urządzenia podrzędnego. Urządzenie podrzędne sprawdza, czy certyfikat urzędu certyfikacji urządzenia IoT Edge jest podpisany przez certyfikat głównego urzędu certyfikacji. Ten proces umożliwia urządzeniu podrzędnemu potwierdzenie, że brama pochodzi z zaufanego źródła.

Poniższe kroki przeprowadzą Cię przez proces tworzenia certyfikatów i instalowania ich w odpowiednich miejscach na bramie. Możesz użyć dowolnej maszyny do wygenerowania certyfikatów, a następnie skopiować je na urządzenie IoT Edge. 

## <a name="prerequisites"></a>Wymagania wstępne

* Komputer deweloperski do tworzenia certyfikatów. 
* Urządzenie Azure IoT Edge, aby skonfigurować jako bramę. Wykonaj kroki instalacji IoT Edge jednego z następujących systemów operacyjnych:
  * [Windows](how-to-install-iot-edge-windows.md)
  * [Linux](how-to-install-iot-edge-linux.md)

## <a name="generate-certificates-with-windows"></a>Generowanie certyfikatów w systemie Windows

Wykonaj kroki opisane w tej sekcji, aby wygenerować certyfikaty testowe w systemie Windows. Za pomocą komputera z systemem Windows można generować certyfikaty, a następnie kopiować je do dowolnego IoT Edge urządzenia uruchomionego w dowolnym obsługiwanym systemie operacyjnym. 

Certyfikaty wygenerowane w tej sekcji są przeznaczone tylko do celów testowych. 

### <a name="install-openssl"></a>Zainstaluj OpenSSL

Zainstaluj program OpenSSL dla systemu Windows na komputerze, którego używasz do generowania certyfikatów. Jeśli masz już zainstalowaną OpenSSL na urządzeniu z systemem Windows, możesz pominąć ten krok, ale upewnij się, że OpenSSL. exe jest dostępny w zmiennej środowiskowej PATH. 

Istnieje kilka sposobów instalacji programu OpenSSL, w tym:

* **Łatwiejsze:** Pobierz i zainstaluj wszystkie [pliki binarne OpenSSL innych](https://wiki.openssl.org/index.php/Binaries)firm, na przykład z [OpenSSL na sourceforge](https://sourceforge.net/projects/openssl/). Dodaj pełną ścieżkę do OpenSSL. exe do zmiennej środowiskowej PATH. 
   
* **Zalecane:** Pobierz kod źródłowy OpenSSL i Utwórz pliki binarne na komputerze przez siebie lub za pośrednictwem [vcpkg](https://github.com/Microsoft/vcpkg). Poniższe instrukcje wykorzystują vcpkg do pobierania kodu źródłowego, kompilowania i instalowania OpenSSL na komputerze z systemem Windows z prostymi krokami.

   1. Przejdź do katalogu, w którym chcesz zainstalować vcpkg. Odwołujemy się do tego katalogu jako *\<VCPKGDIR >* . Postępuj zgodnie z instrukcjami, aby pobrać i zainstalować [vcpkg](https://github.com/Microsoft/vcpkg).
   
   2. Po zainstalowaniu vcpkg Uruchom następujące polecenie w wierszu polecenia programu PowerShell, aby zainstalować pakiet OpenSSL dla systemu Windows x64. Instalacja zazwyczaj trwa około 5 minut.

      ```powershell
      .\vcpkg install openssl:x64-windows
      ```
   3. Dodaj `<VCPKGDIR>\installed\x64-windows\tools\openssl` do zmiennej środowiskowej PATH, tak aby plik OpenSSL. exe był dostępny do wywołania.

### <a name="prepare-creation-scripts"></a>Przygotuj skrypty tworzenia

Repozytorium Azure IoT Edge git zawiera skrypty, których można użyć do generowania certyfikatów testowych. W tej sekcji można sklonować repozytorium IoT Edge i wykonać skrypty. 

1. Otwórz okno programu PowerShell w trybie administratora. 

2. Klonowanie repozytorium git zawierającego skrypty do generowania certyfikatów nieprodukcyjnych. Te skrypty ułatwiają tworzenie niezbędnych certyfikatów w celu skonfigurowania niejawnej bramy. Użyj polecenia `git clone` lub [Pobierz plik zip](https://github.com/Azure/iotedge/archive/master.zip). 

   ```powershell
   git clone https://github.com/Azure/iotedge.git
   ```

3. Przejdź do katalogu, w którym chcesz korzystać. W tym artykule wywołamy ten katalog *\<WRKDIR >* . Wszystkie certyfikaty i klucze zostaną utworzone w tym katalogu roboczym.

4. Skopiuj pliki konfiguracji i skryptów ze sklonowanego repozytorium do katalogu roboczego. 

   ```powershell
   copy <path>\iotedge\tools\CACertificates\*.cnf .
   copy <path>\iotedge\tools\CACertificates\ca-certs.ps1 .
   ```

   Jeśli repozytorium zostało pobrane jako plik ZIP, nazwa folderu jest `iotedge-master`, a reszta ścieżki jest taka sama. 
<!--
5. Set environment variable OPENSSL_CONF to use the openssl_root_ca.cnf configuration file.

    ```powershell
    $env:OPENSSL_CONF = "$PWD\openssl_root_ca.cnf"
    ```
-->
5. Włącz program PowerShell, aby uruchamiał skrypty.

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser
   ```

7. Przenieś funkcje używane przez skrypty do globalnej przestrzeni nazw programu PowerShell.
   
   ```powershell
   . .\ca-certs.ps1
   ```

   W oknie programu PowerShell zostanie wyświetlone ostrzeżenie informujące o tym, że certyfikaty wygenerowane przez ten skrypt są przeznaczone tylko do celów testowych i nie powinny być używane w scenariuszach produkcyjnych.

8. Sprawdź, czy program OpenSSL został prawidłowo zainstalowany i upewnij się, że nie nastąpiły kolizje nazw z istniejącymi certyfikatami. Jeśli występują problemy, skrypt powinien opisać, jak naprawić je w systemie.

   ```powershell
   Test-CACertsPrerequisites
   ```

### <a name="create-certificates"></a>Tworzenie certyfikatów

W tej sekcji utworzysz trzy certyfikaty, a następnie nawiążesz połączenie z nimi w łańcuchu. Umieszczenie certyfikatów w pliku łańcucha pozwala łatwo instalować je na urządzeniu bramy IoT Edge i na wszystkich urządzeniach podrzędnych.  

1. Utwórz certyfikat głównego urzędu certyfikacji i podpisz jeden certyfikat pośredni. Wszystkie certyfikaty są umieszczane w katalogu roboczym.

   ```powershell
   New-CACertsCertChain rsa
   ```

   To polecenie skryptu tworzy kilka certyfikatów i plików kluczy, ale odwołuje się do jednego w dalszej części tego artykułu:
   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

2. Utwórz certyfikat i klucz prywatny urzędu certyfikacji urządzenia IoT Edge przy użyciu następującego polecenia. Podaj nazwę certyfikatu urzędu certyfikacji, na przykład **MyEdgeDeviceCA**. Nazwa jest używana do nazwy plików i podczas generowania certyfikatu. 

   ```powershell
   New-CACertsEdgeDeviceCA "MyEdgeDeviceCA"
   ```

   To polecenie skryptu tworzy kilka certyfikatów i plików kluczy, w tym dwie, do których odwołuje się w dalszej części tego artykułu:
   * `<WRKDIR>\certs\iot-edge-device-ca-MyEdgeDeviceCA-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-edge-device-ca-MyEdgeDeviceCA.key.pem`

   >[!TIP]
   >Jeśli podano nazwę inną niż **MyEdgeDeviceCA**, wówczas certyfikaty i klucze utworzone przez to polecenie zostaną odzwierciedlone w tej nazwie. 

Teraz, gdy masz certyfikaty, przejdź z wyprzedzeniem, aby [zainstalować certyfikaty na bramie](#install-certificates-on-the-gateway)

## <a name="generate-certificates-with-linux"></a>Generowanie certyfikatów przy użyciu systemu Linux

Wykonaj kroki opisane w tej sekcji, aby wygenerować certyfikaty testowe w systemie Linux. Za pomocą komputera z systemem Linux można generować certyfikaty, a następnie kopiować je do dowolnego IoT Edge urządzenia uruchomionego w dowolnym obsługiwanym systemie operacyjnym. 

Certyfikaty wygenerowane w tej sekcji są przeznaczone tylko do celów testowych. 

### <a name="prepare-creation-scripts"></a>Przygotuj skrypty tworzenia

Repozytorium Azure IoT Edge git zawiera skrypty, których można użyć do generowania certyfikatów testowych. W tej sekcji można sklonować repozytorium IoT Edge i wykonać skrypty. 

1. Klonowanie repozytorium git zawierającego skrypty do generowania certyfikatów nieprodukcyjnych. Te skrypty ułatwiają tworzenie niezbędnych certyfikatów w celu skonfigurowania niejawnej bramy. 

   ```bash
   git clone https://github.com/Azure/iotedge.git
   ```

2. Przejdź do katalogu, w którym chcesz korzystać. Odwołujemy się do tego katalogu w artykule jako *\<WRKDIR >* . Wszystkie pliki certyfikatów i kluczy zostaną utworzone w tym katalogu.
  
3. Skopiuj pliki konfiguracji i skryptów ze sklonowanego repozytorium IoT Edge do katalogu roboczego.

   ```bash
   cp <path>/iotedge/tools/CACertificates/*.cnf .
   cp <path>/iotedge/tools/CACertificates/certGen.sh .
   ```

<!--
4. Configure OpenSSL to generate certificates using the provided script. 

   ```bash
   chmod 700 certGen.sh 
   ```
-->

### <a name="create-certificates"></a>Tworzenie certyfikatów

W tej sekcji utworzysz trzy certyfikaty, a następnie nawiążesz połączenie z nimi w łańcuchu. Umieszczenie certyfikatów w pliku łańcucha pozwala łatwo instalować je na urządzeniu bramy IoT Edge i na wszystkich urządzeniach podrzędnych.  

1. Utwórz certyfikat głównego urzędu certyfikacji i jeden certyfikat pośredni. Te certyfikaty są umieszczane w *\<WRKDIR >* .

   Jeśli certyfikaty główne i pośrednie zostały już utworzone w tym katalogu roboczym, nie uruchamiaj ponownie tego skryptu. Uruchomienie tego skryptu spowoduje zastąpienie istniejących certyfikatów. Zamiast tego przejdź do następnego kroku. 

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   Skrypt tworzy kilka certyfikatów i kluczy. Zwróć uwagę na to, do której odwołuje się w następnej sekcji:
   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`

2. Utwórz certyfikat i klucz prywatny urzędu certyfikacji urządzenia IoT Edge przy użyciu następującego polecenia. Podaj nazwę certyfikatu urzędu certyfikacji, na przykład **MyEdgeDeviceCA**. Nazwa jest używana do nazwy plików i podczas generowania certyfikatu. 

   ```bash
   ./certGen.sh create_edge_device_ca_certificate "MyEdgeDeviceCA"
   ```

   Skrypt tworzy kilka certyfikatów i kluczy. Zanotuj dwa, do których odnosimy się w następnej sekcji: 
   * `<WRKDIR>/certs/iot-edge-device-ca-MyEdgeDeviceCA-full-chain.cert.pem`
   * `<WRKDIR>/private/iot-edge-device-ca-MyEdgeDeviceCA.key.pem`

   >[!TIP]
   >Jeśli podano nazwę inną niż **MyEdgeDeviceCA**, wówczas certyfikaty i klucze utworzone przez to polecenie zostaną odzwierciedlone w tej nazwie. 

## <a name="install-certificates-on-the-gateway"></a>Instalowanie certyfikatów na bramie

Po utworzeniu łańcucha certyfikatów należy go zainstalować na urządzeniu bramy IoT Edge i skonfigurować środowisko uruchomieniowe IoT Edge w celu odwoływania się do nowych certyfikatów. 

1. Skopiuj następujące pliki z *\<WRKDIR >* . Zapisz je w dowolnym miejscu na urządzeniu IoT Edge. Odwołujemy się do katalogu docelowego na urządzeniu IoT Edge jako *\<CERTDIR >* . 

   * Certyfikat urzędu certyfikacji urządzenia — `<WRKDIR>\certs\iot-edge-device-ca-MyEdgeDeviceCA-full-chain.cert.pem`
   * Klucz prywatny urzędu certyfikacji urządzenia — `<WRKDIR>\private\iot-edge-device-ca-MyEdgeDeviceCA.key.pem`
   * Główny urząd certyfikacji — `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

   Do przenoszenia plików certyfikatów można użyć usługi, takiej jak [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) lub funkcja, taka jak [Secure Copy Protocol](https://www.ssh.com/ssh/scp/) .  Jeśli certyfikaty zostały wygenerowane na urządzeniu IoT Edge, możesz pominąć ten krok i użyć ścieżki do katalogu roboczego.

2. Otwórz plik konfiguracji demona Security IoT Edge. 

   * Windows: `C:\ProgramData\iotedge\config.yaml`
   * Linux: `/etc/iotedge/config.yaml`

3. Ustaw właściwości **certyfikatu** w pliku config. YAML na pełną ścieżkę do certyfikatu i plików kluczy na urządzeniu IoT Edge. Usuń znak `#` przed właściwościami certyfikatu, aby usunąć komentarz z czterech wierszy. Należy pamiętać, że wcięcia w YAML to dwie spacje.

   * W systemie Windows:

      ```yaml
      certificates:
        device_ca_cert: "<CERTDIR>\\certs\\iot-edge-device-ca-MyEdgeDeviceCA-full-chain.cert.pem"
        device_ca_pk: "<CERTDIR>\\private\\iot-edge-device-ca-MyEdgeDeviceCA.key.pem"
        trusted_ca_certs: "<CERTDIR>\\certs\\azure-iot-test-only.root.ca.cert.pem"
      ```
   
   * W systemie Linux: 
      ```yaml
      certificates:
        device_ca_cert: "<CERTDIR>/certs/iot-edge-device-ca-MyEdgeDeviceCA-full-chain.cert.pem"
        device_ca_pk: "<CERTDIR>/private/iot-edge-device-ca-MyEdgeDeviceCA.key.pem"
        trusted_ca_certs: "<CERTDIR>/certs/azure-iot-test-only.root.ca.cert.pem"
      ```

4. Na urządzeniach z systemem Linux upewnij się, że użytkownik **iotedge** ma uprawnienia do odczytu w katalogu zawierającym certyfikaty. 

## <a name="deploy-edgehub-to-the-gateway"></a>Wdróż EdgeHub na bramie

Podczas pierwszej instalacji IoT Edge na urządzeniu zostanie automatycznie uruchomiony tylko jeden moduł systemowy: Agent IoT Edge. Aby urządzenie działało jako brama, potrzebne są oba moduły systemowe. Jeśli nie wdrożono żadnych modułów na urządzeniu bramy wcześniej, należy utworzyć początkowe wdrożenie dla urządzenia, aby uruchomić drugi moduł systemu, IoT Edge centrum. Wdrożenie będzie wyglądało puste, ponieważ nie dodasz żadnych modułów w kreatorze, ale upewni się, że oba moduły systemowe są uruchomione. 

Możesz sprawdzić, które moduły są uruchomione na urządzeniu za pomocą polecenia `iotedge list`. Jeśli lista zwróci tylko moduł **edgeAgent** bez **edgeHub**, wykonaj następujące czynności:

1. W witrynie Azure Portal przejdź do centrum IoT Hub.

2. Przejdź do **IoT Edge** i wybierz urządzenie IoT Edge, które ma być używane jako brama.

3. Wybierz pozycję **Ustaw moduły**.

4. Wybierz opcję **Dalej**.

5. Na stronie **Określanie tras** powinna istnieć trasa domyślna, która wysyła wszystkie komunikaty ze wszystkich modułów do IoT Hub. Jeśli tak nie jest, dodaj następujący kod, a następnie wybierz przycisk **Dalej**.

   ```JSON
   {
       "routes": {
           "route": "FROM /* INTO $upstream"
       }
   }
   ```

6. Na stronie **Przegląd szablonu** wybierz pozycję **Prześlij**.

## <a name="open-ports-on-gateway-device"></a>Otwórz porty na urządzeniu bramy

Standardowe urządzenia IoT Edge nie potrzebują żadnej łączności przychodzącej z funkcją, ponieważ cała komunikacja z IoT Hub odbywa się za pośrednictwem połączeń wychodzących. Urządzenia bramy różnią się, ponieważ muszą odbierać komunikaty z urządzeń podrzędnych. Jeśli Zapora jest między urządzeniami podrzędnymi a urządzeniem bramy, komunikacja musi być również możliwa za pomocą zapory.

Aby scenariusz bramy działał prawidłowo, należy otworzyć co najmniej jeden z obsługiwanych protokołów IoT Edge Hub dla ruchu przychodzącego z urządzeń podrzędnych. Obsługiwane protokoły to MQTT, AMQP, HTTPS, MQTT za pośrednictwem obiektów WebSockets oraz AMQP za pośrednictwem obiektów WebSockets. 

| Port | Protocol (Protokół) |
| ---- | -------- |
| 8883 | MQTT |
| 5671 | AMQP |
| 443 | HTTPS <br> MQTT + WS <br> AMQP + WS | 

## <a name="route-messages-from-downstream-devices"></a>Kierowanie komunikatów z urządzeń podrzędnych
Środowisko uruchomieniowe IoT Edge może kierować komunikaty wysyłane z urządzeń podrzędnych podobnie jak komunikaty wysyłane przez moduły. Ta funkcja umożliwia wykonywanie analiz w module uruchomionym w bramie przed wysłaniem danych do chmury. 

Obecnie sposób rozsyłania komunikatów wysyłanych przez urządzenia podrzędne polega na rozróżnieniu ich od komunikatów wysyłanych przez moduły. Komunikaty wysyłane przez moduły zawierają Właściwość systemową o nazwie **connectionModuleId** , ale komunikaty wysyłane przez urządzenia podrzędne nie są obsługiwane. Można użyć klauzuli WHERE trasy, aby wykluczyć wszystkie komunikaty zawierające tę właściwość System. 

Poniższa trasa to przykład, który wyśle komunikaty z dowolnego urządzenia podrzędnego do modułu o nazwie `ai_insights`, a następnie z `ai_insights` do IoT Hub.

```json
{
    "routes":{
        "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")", 
        "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream" 
    } 
}
```

Aby uzyskać więcej informacji na temat routingu wiadomości, zobacz [wdrażanie modułów i ustanawianie tras](./module-composition.md#declare-routes).


## <a name="enable-extended-offline-operation"></a>Włącz rozszerzoną operację offline

Począwszy od [wersji v 1.0.4](https://github.com/Azure/azure-iotedge/releases/tag/1.0.4) środowiska uruchomieniowego IoT Edge, urządzenie bramy i urządzenia podrzędne łączące się z nim można skonfigurować do przedłużonej operacji w trybie offline. 

Dzięki tej możliwości lokalne moduły lub urządzenia podrzędne mogą ponownie uwierzytelniać się przy użyciu urządzenia z systemem IoT Edge i komunikować się ze sobą za pomocą komunikatów i metod nawet po rozłączeniu z Centrum IoT Hub. Aby uzyskać więcej informacji, zobacz informacje o [rozszerzonych możliwościach trybu offline dla urządzeń IoT Edge, modułów i urządzeń podrzędnych](offline-capabilities.md).

Aby włączyć rozszerzone możliwości trybu offline, należy ustanowić relację nadrzędny-podrzędny między urządzeniem bramy IoT Edge a urządzeniami podrzędnymi, które będą się z nim połączyć. Te kroki zostały omówione bardziej szczegółowo w temacie [uwierzytelnianie urządzenia podrzędnego w usłudze Azure IoT Hub](how-to-authenticate-downstream-device.md).

## <a name="next-steps"></a>Następne kroki

Teraz, gdy urządzenie IoT Edge działa jako przezroczysta brama, należy skonfigurować urządzenia podrzędne, aby ufać bramie i wysyłać do niej komunikaty. Kontynuuj, aby [uwierzytelnić urządzenie podrzędne w usłudze Azure IoT Hub](how-to-authenticate-downstream-device.md) w celu wykonania następnych kroków w celu skonfigurowania niejawnego scenariusza bramy. 
