---
title: Tworzenie przezroczystej bramy urządzenia — usługi Azure IoT Edge | Dokumentacja firmy Microsoft
description: Używanie usługi Azure IoT Edge urządzenia rolę przezroczystej bramy, która może przetwarzać informacje z urządzeń podrzędne
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/07/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 5881adb7e2fc0d52cc2037d3d4a9e986b3e29d74
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67058354"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>Konfigurowanie urządzenia usługi IoT Edge, aby pełnić rolę przezroczystej bramy

Ten artykuł zawiera szczegółowe instrukcje dotyczące konfigurowania urządzenia usługi IoT Edge działa jak przezroczystej bramy w przypadku innych urządzeń do komunikowania się z usługą IoT Hub. W tym artykule określenie *brama usługi IoT Edge* odwołuje się do urządzenia usługi IoT Edge używany jako przezroczystej bramy. Aby uzyskać więcej informacji, zobacz [jak IoT Edge urządzenia mogą być używane jako brama](./iot-edge-as-gateway.md).

>[!NOTE]
>Obecnie:
> * Urządzenia z włączoną obsługą usługi Edge nie można nawiązać połączenia bramy usługi IoT Edge. 
> * Podrzędne urządzenia nie mogą używać przekazywania plików.

Istnieją trzy ogólne kroki, aby skonfigurować połączenie pomyślne przezroczystej bramy. W tym artykule opisano pierwszy krok:

1. **Urządzenie bramy musi być w stanie bezpieczne łączenie się z urządzeniami podrzędnego otrzymywać informacje z urządzeń podrzędne i kierowanie komunikatów w postaci do odpowiedniego miejsca docelowego.**
2. Podrzędne urządzenie musi mieć tożsamości urządzenia, aby można było uwierzytelnianie za pomocą usługi IoT Hub i wiedzieć, aby komunikować się za pośrednictwem urządzenia bramy. Aby uzyskać więcej informacji, zobacz [uwierzytelnienia podrzędnym urządzenia do usługi Azure IoT Hub](how-to-authenticate-downstream-device.md).
3. Podrzędne urządzenie musi mieć możliwość nawiązania bezpiecznego połączenia urządzenia bramy. Aby uzyskać więcej informacji, zobacz [połączyć podrzędny urządzenie bramy usługi Azure IoT Edge](how-to-connect-downstream-device.md).


W przypadku urządzeń do działania jako brama musi mieć możliwość nawiązania bezpiecznego połączenia ich podrzędnym urządzenia. Usługa Azure IoT Edge umożliwia użycie infrastruktury kluczy publicznych (PKI) do skonfigurowania bezpiecznych połączeń między urządzeniami. W tym przypadku możemy zezwolenie podrzędnym urządzenia połączyć się z urządzenia usługi IoT Edge, działając jako przezroczystej bramy. Aby zachować bezpieczeństwo uzasadnione, podrzędne urządzenia powinny potwierdzenia tożsamości urządzenia bramy. To sprawdzenie tożsamości uniemożliwia nawiązywania połączenia z bramami potencjalnie złośliwych urządzeń.

Podrzędne urządzenie może pozostawać w dowolnej aplikacji lub platformy, który został utworzony za pomocą tożsamości [usługi Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub) usługi w chmurze. W wielu przypadkach te aplikacje korzystają [zestaw SDK urządzeń Azure IoT](../iot-hub/iot-hub-devguide-sdks.md). Praktycznego podrzędne urządzenia można nawet aplikację działającą na samym urządzeniu bramy usługi IoT Edge. 

Można utworzyć żadnej infrastruktury certyfikatów, umożliwiająca zaufania wymagane dla topologii urządzenia bramy. W tym artykule przyjęto założenie, że tę samą konfigurację certyfikatu, który zostanie wykorzystany do włączenia [zabezpieczeń urzędu certyfikacji X.509](../iot-hub/iot-hub-x509ca-overview.md) w usłudze IoT Hub, który obejmuje certyfikat X.509 urzędu certyfikacji, powiązanych z określonej usługi IoT hub (IoT hub głównego urzędu certyfikacji), szereg certyfikaty z podpisem z tego urzędu certyfikacji i urzędu certyfikacji dla urządzenia usługi IoT Edge.

![Konfiguracja certyfikatu bramy](./media/how-to-create-transparent-gateway/gateway-setup.png)

>[!NOTE]
>Termin "głównego urzędu certyfikacji" używany w tym artykule dotyczy certyfikat publiczny urzędu najwyższego poziomu łańcucha certyfikatów PKI, a nie musi to być katalog główny certyfikat urzędu certyfikacji syndykowany. W wielu przypadkach jest faktycznie certyfikat pośredniego urzędu certyfikacji publicznych. 

Brama przedstawia informacje o jego usługi IoT Edge urządzenia urzędu certyfikacji, aby podrzędnym urządzenia podczas inicjowania połączenia. Podrzędne urządzenie sprawdza upewnij się, że certyfikat urzędu certyfikacji urządzenia usługi IoT Edge jest podpisany przez certyfikat głównego urzędu certyfikacji. Ten proces umożliwia podrzędnym urządzenia upewnić się, że brama pochodzi z zaufanego źródła.

W poniższych krokach objaśniono proces tworzenia certyfikatów i instalowania ich w odpowiednich miejscach w bramie. Można użyć dowolnej maszyny do generowania certyfikatów, a następnie skopiuj je do urządzenia usługi IoT Edge. 

## <a name="prerequisites"></a>Wymagania wstępne

Urządzenia z systemem Azure IoT Edge można skonfigurować jako bramę. Wykonaj kroki instalacji usługi IoT Edge dla jednego z następujących systemów operacyjnych:
  * [Windows](./how-to-install-iot-edge-windows.md)
  * [Linux x64](./how-to-install-iot-edge-linux.md)
  * [ARM32 systemu Linux](./how-to-install-iot-edge-linux-arm.md)

Ten artykuł dotyczy *hostname bramy* w kilku miejscach. Nazwa hosta bramy jest zadeklarowana w **hostname** parametru pliku config.yaml z urządzeniem bramy usługi IoT Edge. Służy do tworzenia certyfikatów, w tym artykule i jest określone w parametrach połączenia podrzędnego urządzeń. Nazwa bramy hosta musi być rozpoznawana jako adres IP, albo za pomocą DNS lub wpisu w pliku hostów.

## <a name="generate-certificates-with-windows"></a>Generowanie certyfikatów z Windows

Wykonaj kroki w tej sekcji, aby wygenerować Certyfikaty testowe na Windows. Możesz użyć maszyny Windows w celu wygenerowania certyfikatów i skopiować je za pośrednictwem dowolnego urządzenia IoT Edge, uruchomione na dowolnym obsługiwanym systemie operacyjnym. 

Certyfikatów wygenerowanych w tej sekcji są przeznaczone tylko do celów testowych. 

### <a name="install-openssl"></a>Zainstalować protokół OpenSSL

Zainstaluj biblioteki OpenSSL dla Windows na komputerze, którego używasz w celu wygenerowania certyfikatów. Jeśli masz już zainstalowane na urządzeniu z systemem Windows biblioteki OpenSSL, można pominąć ten krok, ale upewnij się, że ten openssl.exe jest dostępny w zmiennej środowiskowej PATH. 

Istnieje kilka sposobów, należy zainstalować protokół OpenSSL:

* **Łatwiejsze:** Pobierz i zainstaluj dowolne [plików binarnych biblioteki OpenSSL firm](https://wiki.openssl.org/index.php/Binaries), na przykład z [OpenSSL na SourceForge](https://sourceforge.net/projects/openssl/). Dodanie pełnej ścieżki do openssl.exe do zmiennej środowiskowej PATH. 
   
* **Zalecane:** Pobierz kod źródłowy biblioteki OpenSSL i kompilowania plików binarnych na komputerze, samodzielnie lub za pośrednictwem [vcpkg](https://github.com/Microsoft/vcpkg). Zgodnie z instrukcjami podanymi poniżej umożliwia vcpkg pobierania kodu źródłowego, kompilacji i zainstaluj OpenSSL na komputerze Windows za pomocą prostych krokach.

   1. Przejdź do katalogu, w którym chcesz zainstalować vcpkg. Będziemy odnosić się do tego katalogu jako  *\<VCPKGDIR >* . Postępuj zgodnie z instrukcjami, aby pobrać i zainstalować [vcpkg](https://github.com/Microsoft/vcpkg).
   
   2. Gdy vcpkg jest zainstalowany, uruchom następujące polecenie w wierszu polecenia programu powershell, aby zainstalować pakiet biblioteki OpenSSL dla Windows x64. Instalacja trwa zwykle około 5 minut.

      ```powershell
      .\vcpkg install openssl:x64-windows
      ```
   3. Dodaj `<VCPKGDIR>\installed\x64-windows\tools\openssl` do zmiennej środowiskowej PATH, aby plik openssl.exe jest dostępny do wywołania.

### <a name="prepare-creation-scripts"></a>Przygotowanie skryptów tworzenia

Repozytorium git usługi Azure IoT Edge zawiera skrypty używane do generowania certyfikatów testu. W tej sekcji możesz sklonować repozytorium usługi IoT Edge i uruchomienie skryptów. 

1. Otwórz okno programu PowerShell w trybie administratora. 

2. Sklonuj repozytorium git, zawierający skrypty w celu wygenerowania certyfikatów nieprodukcyjnych. Te skrypty pomocne podczas tworzenia wymagane certyfikaty, aby skonfigurować przezroczystej bramy. Użyj `git clone` polecenia lub [Pobierz plik ZIP](https://github.com/Azure/iotedge/archive/master.zip). 

   ```powershell
   git clone https://github.com/Azure/iotedge.git
   ```

3. Przejdź do katalogu, w którym chcesz pracować. W tym artykule Zadzwonimy do tego katalogu  *\<WRKDIR >* . Wszystkie certyfikaty i klucze, zostanie utworzony w tym katalogu roboczego.

4. Skopiuj pliki konfiguracji i skryptów z poziomu sklonowanego repozytorium do katalogu roboczego. 

   ```powershell
   copy <path>\iotedge\tools\CACertificates\*.cnf .
   copy <path>\iotedge\tools\CACertificates\ca-certs.ps1 .
   ```

   Jeśli pobrano repozytorium w formie pliku ZIP, a następnie nazwa folderu jest `iotedge-master` i pozostałą część ścieżki jest taka sama. 
<!--
5. Set environment variable OPENSSL_CONF to use the openssl_root_ca.cnf configuration file.

    ```powershell
    $env:OPENSSL_CONF = "$PWD\openssl_root_ca.cnf"
    ```
-->
5. Włącz program PowerShell w celu uruchamiania skryptów.

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser
   ```

7. Przenieś funkcje używane przez skrypty w globalnej przestrzeni nazw programu PowerShell.
   
   ```powershell
   . .\ca-certs.ps1
   ```

   W oknie programu PowerShell wyświetli ostrzeżenie, że certyfikatów wygenerowanych przy użyciu tego skryptu są tylko do celów testowych i nie należy używać w scenariuszach produkcyjnych.

8. Sprawdź, czy biblioteki OpenSSL został poprawnie zainstalowany i upewnij się, że nie będzie konfliktów nazw z istniejących certyfikatów. Jeśli wystąpią problemy, skrypt powinna opisywać sposób rozwiązać problemy w systemie.

   ```powershell
   Test-CACertsPrerequisites
   ```

### <a name="create-certificates"></a>Tworzenie certyfikatów

W tej sekcji utworzysz trzy certyfikaty i połączyć je w łańcuch. Umieszczenie certyfikatów w łańcuchu pliku pozwala łatwo zainstalować je na urządzenie bramy usługi IoT Edge i wszystkie podrzędne urządzenia.  

1. Utwórz certyfikat głównego urzędu certyfikacji i jego zarejestrować jeden certyfikat pośredniego. Certyfikaty są umieszczane w katalogu roboczym.

   ```powershell
   New-CACertsCertChain rsa
   ```

   To polecenie skrypt umożliwia utworzenie kilku certyfikatów i plików kluczy, ale zamierzamy dotyczą w szczególności w dalszej części tego artykułu:
   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

2. Tworzenie usługi IoT Edge urządzenia certyfikatu urzędu certyfikacji i klucza prywatnego za pomocą następującego polecenia. Podaj nazwę hosta bramy, który można znaleźć w pliku iotedge\config.yaml z urządzeniem bramy. Nazwa hosta bramy jest używany do nazywania plików i podczas generowania certyfikatów. 

   ```powershell
   New-CACertsEdgeDevice "<gateway hostname>"
   ```

   To polecenie skrypt tworzy kilka certyfikatów i kluczy pliki, w tym dwa, której będziemy się w dalszej części tego artykułu:
   * `<WRKDIR>\certs\iot-edge-device-<gateway hostname>-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-edge-device-<gateway hostname>.key.pem`

Teraz, gdy masz certyfikatów, przejdź do sekcji [instalowanie certyfikatów na bramę](#install-certificates-on-the-gateway)

## <a name="generate-certificates-with-linux"></a>Generowanie certyfikatów z systemem Linux

Wykonaj kroki w tej sekcji, aby wygenerować Certyfikaty testowe w systemie Linux. Możesz użyć maszyny z systemem Linux w celu wygenerowania certyfikatów i skopiować je za pośrednictwem dowolnego urządzenia IoT Edge, uruchomione na dowolnym obsługiwanym systemie operacyjnym. 

Certyfikatów wygenerowanych w tej sekcji są przeznaczone tylko do celów testowych. 

### <a name="prepare-creation-scripts"></a>Przygotowanie skryptów tworzenia

Repozytorium git usługi Azure IoT Edge zawiera skrypty używane do generowania certyfikatów testu. W tej sekcji możesz sklonować repozytorium usługi IoT Edge i uruchomienie skryptów. 

1. Sklonuj repozytorium git, zawierający skrypty w celu wygenerowania certyfikatów nieprodukcyjnych. Te skrypty pomocne podczas tworzenia wymagane certyfikaty, aby skonfigurować przezroczystej bramy. 

   ```bash
   git clone https://github.com/Azure/iotedge.git
   ```

2. Przejdź do katalogu, w którym chcesz pracować. Będziemy odnosić się do tego katalogu w artykule jako  *\<WRKDIR >* . Wszystkie pliki certyfikat i klucz zostanie utworzony w tym katalogu.
  
3. Skopiuj pliki konfiguracji i skryptów z poziomu sklonowanego repozytorium usługi IoT Edge w katalogu roboczym.

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

W tej sekcji utworzysz trzy certyfikaty i połączyć je w łańcuch. Umieszczenie certyfikatów w łańcuchu pliku pozwala łatwo zainstalować je na urządzenie bramy usługi IoT Edge i wszystkie podrzędne urządzenia.  

1. Tworzenie certyfikatu głównego urzędu certyfikacji i jeden certyfikat pośredniego. Te certyfikaty są umieszczane w  *\<WRKDIR >* .

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   Skrypt tworzy kilka certyfikatów i kluczy. Zanotuj, z których pierwszy będzie nazywamy w następnej sekcji:
   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`

2. Tworzenie usługi IoT Edge urządzenia certyfikatu urzędu certyfikacji i klucza prywatnego za pomocą następującego polecenia. Podaj nazwę hosta bramy, który można znaleźć w pliku iotedge/config.yaml z urządzeniem bramy. Nazwa hosta bramy jest używany do nazywania plików i podczas generowania certyfikatów. 

   ```bash
   ./certGen.sh create_edge_device_certificate "<gateway hostname>"
   ```

   Skrypt tworzy kilka certyfikatów i kluczy. Zanotuj dwa, który będziemy się odwoływać się w następnej sekcji: 
   * `<WRKDIR>/certs/iot-edge-device-<gateway hostname>-full-chain.cert.pem`
   * `<WRKDIR>/private/iot-edge-device-<gateway hostname>.key.pem`

## <a name="install-certificates-on-the-gateway"></a>Instalowanie certyfikatów na bramę

Po utworzeniu łańcucha certyfikatów, należy go zainstalować na urządzeniu bramy usługi IoT Edge i konfigurowanie środowiska uruchomieniowego usługi IoT Edge, aby odwoływać się do nowych certyfikatów. 

1. Skopiuj następujące pliki z  *\<WRKDIR >* . Zapisz je w dowolnym miejscu na urządzeniu usługi IoT Edge. Będziemy odnosić się do katalogu docelowego na twoim urządzeniu usługi IoT Edge jako  *\<CERTDIR >* . 

   * Certyfikat dostępu Warunkowego do urządzeń —  `<WRKDIR>\certs\iot-edge-device-<gateway hostname>-full-chain.cert.pem`
   * Klucz prywatny urzędu certyfikacji urządzenia — `<WRKDIR>\private\iot-edge-device-<gateway hostname>.key.pem`
   * Główny urząd certyfikacji — `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

   Można użyć usługi takiej jak [usługi Azure Key Vault](https://docs.microsoft.com/azure/key-vault) lub funkcji, takich jak [kopiowania bezpiecznego protokołu](https://www.ssh.com/ssh/scp/) umożliwiającego przeniesienie plików certyfikatów.  Jeśli certyfikaty na samym urządzeniu usługi IoT Edge jest generowany, możesz pominąć ten krok i użyć ścieżki do katalogu roboczego.

2. Otwórz plik konfiguracji demona zabezpieczeń usługi IoT Edge. 

   * Windows: `C:\ProgramData\iotedge\config.yaml`
   * Linux: `/etc/iotedge/config.yaml`

3. Ustaw **certyfikatu** właściwości w pliku config.yaml na pełną ścieżkę do plików certyfikatu i klucza na urządzeniu usługi IoT Edge. Usuń `#` znak przed właściwości certyfikatu i usuń znaczniki komentarza cztery wiersze. Należy pamiętać, że wcięcia w yaml są dwie spacje.

   * W systemie Windows:

      ```yaml
      certificates:
        device_ca_cert: "<CERTDIR>\\certs\\iot-edge-device-<gateway hostname>-full-chain.cert.pem"
        device_ca_pk: "<CERTDIR>\\private\\iot-edge-device-<gateway hostname>.key.pem"
        trusted_ca_certs: "<CERTDIR>\\certs\\azure-iot-test-only.root.ca.cert.pem"
      ```
   
   * W systemie Linux: 
      ```yaml
      certificates:
        device_ca_cert: "<CERTDIR>/certs/iot-edge-device-<gateway hostname>-full-chain.cert.pem"
        device_ca_pk: "<CERTDIR>/private/iot-edge-device-<gateway hostname>.key.pem"
        trusted_ca_certs: "<CERTDIR>/certs/azure-iot-test-only.root.ca.cert.pem"
      ```

4. Na urządzeniach z systemem Linux, upewnij się, że użytkownik **iotedge** ma uprawnienia odczytu wobec katalogu zawierający certyfikaty. 

## <a name="deploy-edgehub-to-the-gateway"></a>Wdrażanie EdgeHub do bramy

Podczas pierwszej instalacji usługi IoT Edge na urządzeniu, moduł tylko jeden system jest uruchamiany automatycznie: agenta usługi IoT Edge. Twoje urządzenie z miejscem pracy jako bramy należy oba moduły systemu. Jeśli wszystkie moduły nie zostały wdrożone na urządzeniu bramy przed, należy utworzyć początkowe wdrożenie dla Twojego urządzenia, które można uruchomić modułu systemu z drugiego Centrum usługi IoT Edge. Wdrożenie będzie wyglądać pusty, ponieważ wszystkie moduły nie dodawaj kreatora, ale będzie upewnij się, że działają zarówno moduły systemu. 

Możesz sprawdzić, które moduły są uruchomione na urządzeniu za pomocą polecenia `iotedge list`. Jeśli listy zwraca tylko moduł **edgeAgent** bez **edgeHub**, wykonaj następujące czynności:

1. W witrynie Azure Portal przejdź do centrum IoT Hub.

2. Przejdź do **usługi IoT Edge** i wybierz urządzenia usługi IoT Edge, która ma być używany jako brama.

3. Wybierz pozycję **Ustaw moduły**.

4. Wybierz opcję **Dalej**.

5. W **określić trasy** strony powinny mieć domyślną trasę, która przesyła wszystkie komunikaty z wszystkich modułów do usługi IoT Hub. Jeśli tak nie jest, dodaj następujący kod, a następnie wybierz przycisk **Dalej**.

   ```JSON
   {
       "routes": {
           "route": "FROM /* INTO $upstream"
       }
   }
   ```

6. W **szablon przeglądu** wybierz opcję **przesyłania**.

## <a name="open-ports-on-gateway-device"></a>Otwieranie portów w urządzeniu bramy

Standardowa urządzenia usługi IoT Edge nie jest konieczne połączenie przychodzące do funkcji, ponieważ cała komunikacja z usługą IoT Hub odbywa się za pośrednictwem połączeń wychodzących. Urządzenia bramy są różne, gdyż wymagają one odbierać komunikaty z urządzeń podrzędnego. Jeśli Zapora jest między podrzędnymi urządzeniami i urządzenie bramy, komunikacji musi być możliwy za pośrednictwem zapory, jak również.

Scenariusz bramy do pracy co najmniej jeden z obsługiwanych protokołów Centrum IoT Edge musi być otwarty dla ruchu przychodzącego z podrzędnym urządzeń. Obsługiwane protokoły to MQTT, AMQP i HTTPS. 

| Port | Protocol |
| ---- | -------- |
| 8883 | MQTT |
| 5671 | AMQP |
| 443 | HTTPS <br> MQTT+WS <br> AMQP+WS | 

## <a name="route-messages-from-downstream-devices"></a>Routing komunikatów z urządzeń podrzędne
Komunikaty wysyłane z urządzeń podrzędne, podobnie jak komunikaty wysyłane przez moduły można kierować do środowiska uruchomieniowego usługi IoT Edge. Ta funkcja umożliwia przeprowadzanie analiz w module uruchomiona na bramie przed wysłaniem danych do chmury. 

Obecnie sposobem, w który trasy wiadomości wysyłanych przez urządzenia podrzędnego jest różnicując je na wiadomości wysłane przez moduły. Komunikaty wysyłane przez wszystkie moduły zawierają właściwość systemu o nazwie **connectionModuleId** , ale nie obsługują komunikaty wysyłane przez urządzenia podrzędnego. Aby wykluczyć wszystkie komunikaty, które zawierają tę właściwość systemu, można użyć klauzuli WHERE trasy. 

Poniżej trasy znajduje się przykład, która będzie wysyłać komunikaty z dowolnego urządzenia podrzędnego do modułu o nazwie `ai_insights`, a następnie z `ai_insights` do usługi IoT Hub.

```json
{
    "routes":{
        "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")", 
        "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream" 
    } 
}
```

Aby uzyskać więcej informacji na temat routingu wiadomości zobacz [wdrażać moduły i trasy ustanowić](./module-composition.md#declare-routes).


## <a name="enable-extended-offline-operation"></a>Włącz rozszerzone operacji w trybie offline

Począwszy od [wersja v1.0.4](https://github.com/Azure/azure-iotedge/releases/tag/1.0.4) środowiska uruchomieniowego usługi IoT Edge, można skonfigurować urządzenie bramy i podrzędnym urządzenia nawiązania z nim rozszerzonej operacji w trybie offline. 

Dzięki tej możliwości moduły lokalne lub podrzędnego urządzeń można ponownie uwierzytelnić urządzenia usługi IoT Edge zgodnie z potrzebami i komunikować się ze sobą przy użyciu wiadomości i metod, nawet wtedy, gdy jest odłączony od usługi IoT hub. Aby uzyskać więcej informacji, zobacz [opis rozszerzony możliwości w trybie offline dla usługi IoT Edge, urządzeń, moduły i urządzeń podrzędnych](offline-capabilities.md).

Aby włączyć rozszerzone możliwości w trybie offline, możesz ustanowić relację nadrzędny podrzędny między urządzenia bramy usługi IoT Edge i podrzędnym urządzenia, które będzie z nim połączyć. Te kroki są szczegółowo opisane bardziej szczegółowo w [uwierzytelnienia podrzędnym urządzenia do usługi Azure IoT Hub](how-to-authenticate-downstream-device.md).

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy masz urządzenia usługi IoT Edge działa jako przezroczystej bramy, należy skonfigurować podrzędne urządzenia, aby ufać bramy i wysyłanie komunikatów do niego. Aby uzyskać więcej informacji, zobacz [połączyć podrzędny urządzenie bramy usługi Azure IoT Edge](how-to-connect-downstream-device.md) i [uwierzytelnienia podrzędnym urządzenia do usługi Azure IoT Hub](how-to-authenticate-downstream-device.md).
