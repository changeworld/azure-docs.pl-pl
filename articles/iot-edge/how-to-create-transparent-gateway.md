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
ms.openlocfilehash: a91860e9ec8d503a01d079925466093d19bbbccf
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2019
ms.locfileid: "68698604"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>Konfigurowanie urządzenia usługi IoT Edge, aby pełnić rolę przezroczystej bramy

Ten artykuł zawiera szczegółowe instrukcje dotyczące konfigurowania urządzenia IoT Edge jako przezroczystej bramy dla innych urządzeń w celu komunikowania się z IoT Hub. W tym artykule określenie *brama usługi IoT Edge* odwołuje się do urządzenia usługi IoT Edge używany jako przezroczystej bramy. Aby uzyskać więcej informacji, zobacz [jak urządzenie IoT Edge może być używane jako brama](./iot-edge-as-gateway.md).

>[!NOTE]
>Obecnie:
> * Urządzenia z włączoną obsługą usługi Edge nie można nawiązać połączenia bramy usługi IoT Edge. 
> * Podrzędne urządzenia nie mogą używać przekazywania plików.

Należy wykonać trzy ogólne kroki, aby skonfigurować pomyślne, przezroczyste połączenie bramy. W tym artykule omówiono pierwszy krok:

1. **Urządzenie bramy musi być w stanie bezpiecznie łączyć się z urządzeniami podrzędnymi, odbierać komunikaty z urządzeń podrzędnych i kierować komunikaty do odpowiednich miejsc docelowych.**
2. Urządzenie podrzędne musi mieć tożsamość urządzenia, aby można było uwierzytelnić się przy użyciu IoT Hub i wiedzieć o komunikacji za pomocą swojego urządzenia bramy. Aby uzyskać więcej informacji, zobacz [uwierzytelnianie urządzenia podrzędnego w usłudze Azure IoT Hub](how-to-authenticate-downstream-device.md).
3. Urządzenie podrzędne musi mieć możliwość bezpiecznego łączenia się z urządzeniem bramy. Aby uzyskać więcej informacji, zobacz [połączyć podrzędny urządzenie bramy usługi Azure IoT Edge](how-to-connect-downstream-device.md).


Aby urządzenie działało jako brama, musi być w stanie bezpiecznie połączyć się z jego urządzeniami podrzędnymi. Usługa Azure IoT Edge umożliwia użycie infrastruktury kluczy publicznych (PKI) do skonfigurowania bezpiecznych połączeń między urządzeniami. W tym przypadku możemy zezwolenie podrzędnym urządzenia połączyć się z urządzenia usługi IoT Edge, działając jako przezroczystej bramy. Aby zachować uzasadnione zabezpieczenia, urządzenie podrzędne powinno potwierdzić tożsamość urządzenia bramy. To sprawdzenie tożsamości uniemożliwia urządzeniom łączenie się z potencjalnie złośliwymi bramami.

Podrzędne urządzenie może pozostawać w dowolnej aplikacji lub platformy, który został utworzony za pomocą tożsamości [usługi Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub) usługi w chmurze. W wielu przypadkach te aplikacje korzystają [zestaw SDK urządzeń Azure IoT](../iot-hub/iot-hub-devguide-sdks.md). Praktycznego podrzędne urządzenia można nawet aplikację działającą na samym urządzeniu bramy usługi IoT Edge. 

Można utworzyć żadnej infrastruktury certyfikatów, umożliwiająca zaufania wymagane dla topologii urządzenia bramy. W tym artykule przyjęto założenie, że ta sama konfiguracja certyfikatu zostanie użyta w celu włączenia [zabezpieczeń urzędu certyfikacji x. 509](../iot-hub/iot-hub-x509ca-overview.md) w IoT Hub, co obejmuje certyfikat dla urzędu certyfikacji x. 509 skojarzony z określonym Centrum IoT Hub (główny urząd certyfikacji Centrum IoT Hub), Seria certyfikatów podpisanych za pomocą tego urzędu certyfikacji. i urząd certyfikacji dla urządzenia IoT Edge.

![Konfiguracja certyfikatu bramy](./media/how-to-create-transparent-gateway/gateway-setup.png)

>[!NOTE]
>Termin "główny urząd certyfikacji" używany w tym artykule odnosi się do certyfikatu publicznego urzędu certyfikacji PKI, a nie musi być certyfikatem głównym urzędu certyfikacji. W wielu przypadkach jest to pośredni certyfikat publiczny urzędu certyfikacji. 

Podczas inicjowania połączenia Brama przedstawia certyfikat urzędu certyfikacji urządzenia IoT Edge do urządzenia podrzędnego. Urządzenie podrzędne sprawdza, czy certyfikat urzędu certyfikacji urządzenia IoT Edge jest podpisany przez certyfikat głównego urzędu certyfikacji. Ten proces umożliwia urządzeniu podrzędnemu potwierdzenie, że brama pochodzi z zaufanego źródła.

Poniższe kroki przeprowadzą Cię przez proces tworzenia certyfikatów i instalowania ich w odpowiednich miejscach na bramie. Można użyć dowolnej maszyny do generowania certyfikatów, a następnie skopiuj je do urządzenia usługi IoT Edge. 

## <a name="prerequisites"></a>Wymagania wstępne

Urządzenia z systemem Azure IoT Edge można skonfigurować jako bramę. Wykonaj kroki instalacji IoT Edge jednego z następujących systemów operacyjnych:
  * [Windows](how-to-install-iot-edge-windows.md)
  * [Linux](how-to-install-iot-edge-linux.md)

Ten artykuł odnosi się do *nazwy hosta bramy* w kilku punktach. Nazwa hosta bramy jest zadeklarowana w parametrze **hostname** pliku config. YAML na urządzeniu bramy IoT Edge. Służy do tworzenia certyfikatów w tym artykule i jest określany w parametrach połączenia urządzeń podrzędnych. Nazwa hosta bramy musi być rozpoznawalna na adres IP przy użyciu systemu DNS lub wpisu pliku hosta.

## <a name="generate-certificates-with-windows"></a>Generowanie certyfikatów z Windows

Wykonaj kroki opisane w tej sekcji, aby wygenerować certyfikaty testowe w systemie Windows. Za pomocą komputera z systemem Windows można generować certyfikaty, a następnie kopiować je do dowolnego IoT Edge urządzenia uruchomionego w dowolnym obsługiwanym systemie operacyjnym. 

Certyfikatów wygenerowanych w tej sekcji są przeznaczone tylko do celów testowych. 

### <a name="install-openssl"></a>Zainstalować protokół OpenSSL

Zainstaluj biblioteki OpenSSL dla Windows na komputerze, którego używasz w celu wygenerowania certyfikatów. Jeśli masz już zainstalowaną OpenSSL na urządzeniu z systemem Windows, możesz pominąć ten krok, ale upewnij się, że OpenSSL. exe jest dostępny w zmiennej środowiskowej PATH. 

Istnieje kilka sposobów, należy zainstalować protokół OpenSSL:

* **Czytelnooci** Pobierz i zainstaluj wszystkie [pliki binarne OpenSSL innych](https://wiki.openssl.org/index.php/Binaries)firm, na przykład z [OpenSSL na sourceforge](https://sourceforge.net/projects/openssl/). Dodanie pełnej ścieżki do openssl.exe do zmiennej środowiskowej PATH. 
   
* **Rekomendowane** Pobierz kod źródłowy OpenSSL i Utwórz pliki binarne na komputerze przez siebie lub za pośrednictwem [vcpkg](https://github.com/Microsoft/vcpkg). Zgodnie z instrukcjami podanymi poniżej umożliwia vcpkg pobierania kodu źródłowego, kompilacji i zainstaluj OpenSSL na komputerze Windows za pomocą prostych krokach.

   1. Przejdź do katalogu, w którym chcesz zainstalować vcpkg. Będziemy odnosić się do tego katalogu jako  *\<VCPKGDIR >* . Postępuj zgodnie z instrukcjami, aby pobrać i zainstalować [vcpkg](https://github.com/Microsoft/vcpkg).
   
   2. Po zainstalowaniu vcpkg Uruchom następujące polecenie w wierszu polecenia programu PowerShell, aby zainstalować pakiet OpenSSL dla systemu Windows x64. Instalacja trwa zwykle około 5 minut.

      ```powershell
      .\vcpkg install openssl:x64-windows
      ```
   3. Dodaj `<VCPKGDIR>\installed\x64-windows\tools\openssl` do zmiennej środowiskowej PATH, aby plik openssl.exe jest dostępny do wywołania.

### <a name="prepare-creation-scripts"></a>Przygotowanie skryptów tworzenia

Repozytorium Azure IoT Edge git zawiera skrypty, których można użyć do generowania certyfikatów testowych. W tej sekcji można sklonować repozytorium IoT Edge i wykonać skrypty. 

1. Otwórz okno programu PowerShell w trybie administratora. 

2. Sklonuj repozytorium git, zawierający skrypty w celu wygenerowania certyfikatów nieprodukcyjnych. Te skrypty pomocne podczas tworzenia wymagane certyfikaty, aby skonfigurować przezroczystej bramy. Użyj `git clone` polecenia lub [Pobierz plik ZIP](https://github.com/Azure/iotedge/archive/master.zip). 

   ```powershell
   git clone https://github.com/Azure/iotedge.git
   ```

3. Przejdź do katalogu, w którym chcesz pracować. W tym artykule wywołamy ten katalog  *\<WRKDIR >* . Wszystkie certyfikaty i klucze zostaną utworzone w tym katalogu roboczym.

4. Skopiuj pliki konfiguracji i skryptów ze sklonowanego repozytorium do katalogu roboczego. 

   ```powershell
   copy <path>\iotedge\tools\CACertificates\*.cnf .
   copy <path>\iotedge\tools\CACertificates\ca-certs.ps1 .
   ```

   Jeśli repozytorium zostało pobrane jako plik zip, nazwa folderu to `iotedge-master` , a reszta ścieżki jest taka sama. 
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

7. Przenieś funkcje używane przez skrypty do globalnej przestrzeni nazw programu PowerShell.
   
   ```powershell
   . .\ca-certs.ps1
   ```

   W oknie programu PowerShell zostanie wyświetlone ostrzeżenie informujące o tym, że certyfikaty wygenerowane przez ten skrypt są przeznaczone tylko do celów testowych i nie powinny być używane w scenariuszach produkcyjnych.

8. Sprawdź, czy program OpenSSL został prawidłowo zainstalowany i upewnij się, że nie nastąpiły kolizje nazw z istniejącymi certyfikatami. Jeśli wystąpią problemy, skrypt powinna opisywać sposób rozwiązać problemy w systemie.

   ```powershell
   Test-CACertsPrerequisites
   ```

### <a name="create-certificates"></a>Tworzenie certyfikatów

W tej sekcji utworzysz trzy certyfikaty i połączyć je w łańcuch. Umieszczenie certyfikatów w łańcuchu pliku pozwala łatwo zainstalować je na urządzenie bramy usługi IoT Edge i wszystkie podrzędne urządzenia.  

1. Utwórz certyfikat głównego urzędu certyfikacji i podpisz jeden certyfikat pośredni. Wszystkie certyfikaty są umieszczane w katalogu roboczym.

   ```powershell
   New-CACertsCertChain rsa
   ```

   To polecenie skryptu tworzy kilka certyfikatów i plików kluczy, ale odwołuje się do jednego w dalszej części tego artykułu:
   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

2. Utwórz certyfikat i klucz prywatny urzędu certyfikacji urządzenia IoT Edge przy użyciu następującego polecenia. Podaj nazwę hosta bramy, którą można znaleźć w pliku iotedge\config.YAML na urządzeniu bramy. Nazwa hosta bramy służy do nazwy plików i podczas generowania certyfikatu. 

   ```powershell
   New-CACertsEdgeDevice "<gateway hostname>"
   ```

   To polecenie skryptu tworzy kilka certyfikatów i plików kluczy, w tym dwie, do których odwołuje się w dalszej części tego artykułu:
   * `<WRKDIR>\certs\iot-edge-device-<gateway hostname>-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-edge-device-<gateway hostname>.key.pem`

Teraz, gdy masz certyfikaty, przejdź z wyprzedzeniem, aby [zainstalować certyfikaty na bramie](#install-certificates-on-the-gateway)

## <a name="generate-certificates-with-linux"></a>Generowanie certyfikatów z systemem Linux

Wykonaj kroki opisane w tej sekcji, aby wygenerować certyfikaty testowe w systemie Linux. Za pomocą komputera z systemem Linux można generować certyfikaty, a następnie kopiować je do dowolnego IoT Edge urządzenia uruchomionego w dowolnym obsługiwanym systemie operacyjnym. 

Certyfikatów wygenerowanych w tej sekcji są przeznaczone tylko do celów testowych. 

### <a name="prepare-creation-scripts"></a>Przygotowanie skryptów tworzenia

Repozytorium Azure IoT Edge git zawiera skrypty, których można użyć do generowania certyfikatów testowych. W tej sekcji można sklonować repozytorium IoT Edge i wykonać skrypty. 

1. Sklonuj repozytorium git, zawierający skrypty w celu wygenerowania certyfikatów nieprodukcyjnych. Te skrypty pomocne podczas tworzenia wymagane certyfikaty, aby skonfigurować przezroczystej bramy. 

   ```bash
   git clone https://github.com/Azure/iotedge.git
   ```

2. Przejdź do katalogu, w którym chcesz pracować. Odwołujemy się do tego katalogu w artykule jako  *\<WRKDIR >* . Wszystkie pliki certyfikatów i kluczy zostaną utworzone w tym katalogu.
  
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

W tej sekcji utworzysz trzy certyfikaty i połączyć je w łańcuch. Umieszczenie certyfikatów w łańcuchu pliku pozwala łatwo zainstalować je na urządzenie bramy usługi IoT Edge i wszystkie podrzędne urządzenia.  

1. Utwórz certyfikat głównego urzędu certyfikacji i jeden certyfikat pośredni. Te certyfikaty są umieszczane w  *\<WRKDIR >* .

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   Skrypt tworzy kilka certyfikatów i kluczy. Zwróć uwagę na to, do której odwołuje się w następnej sekcji:
   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`

2. Utwórz certyfikat i klucz prywatny urzędu certyfikacji urządzenia IoT Edge przy użyciu następującego polecenia. Podaj nazwę hosta bramy, którą można znaleźć w pliku iotedge/config. YAML na urządzeniu bramy. Nazwa hosta bramy służy do nazwy plików i podczas generowania certyfikatu. 

   ```bash
   ./certGen.sh create_edge_device_certificate "<gateway hostname>"
   ```

   Skrypt tworzy kilka certyfikatów i kluczy. Zanotuj dwa, do których odnosimy się w następnej sekcji: 
   * `<WRKDIR>/certs/iot-edge-device-<gateway hostname>-full-chain.cert.pem`
   * `<WRKDIR>/private/iot-edge-device-<gateway hostname>.key.pem`

## <a name="install-certificates-on-the-gateway"></a>Instalowanie certyfikatów na bramę

Po utworzeniu łańcucha certyfikatów, należy go zainstalować na urządzeniu bramy usługi IoT Edge i konfigurowanie środowiska uruchomieniowego usługi IoT Edge, aby odwoływać się do nowych certyfikatów. 

1. Skopiuj następujące pliki z  *\<WRKDIR >* . Zapisz je w dowolnym miejscu na urządzeniu usługi IoT Edge. Będziemy odnosić się do katalogu docelowego na twoim urządzeniu usługi IoT Edge jako  *\<CERTDIR >* . 

   * Certyfikat dostępu Warunkowego do urządzeń —  `<WRKDIR>\certs\iot-edge-device-<gateway hostname>-full-chain.cert.pem`
   * Klucz prywatny urzędu certyfikacji urządzenia — `<WRKDIR>\private\iot-edge-device-<gateway hostname>.key.pem`
   * Główny urząd certyfikacji —`<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

   Do przenoszenia plików certyfikatów można użyć usługi, takiej jak [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) lub funkcja, taka jak [Secure Copy Protocol](https://www.ssh.com/ssh/scp/) .  Jeśli certyfikaty zostały wygenerowane na urządzeniu IoT Edge, możesz pominąć ten krok i użyć ścieżki do katalogu roboczego.

2. Otwórz plik konfiguracji demona zabezpieczeń usługi IoT Edge. 

   * Windows: `C:\ProgramData\iotedge\config.yaml`
   * Linux: `/etc/iotedge/config.yaml`

3. Ustaw właściwości **certyfikatu** w pliku config. YAML na pełną ścieżkę do certyfikatu i plików kluczy na urządzeniu IoT Edge. `#` Usuń znak przed właściwościami certyfikatu, aby usunąć komentarz z czterech wierszy. Należy pamiętać, że wcięcia w YAML to dwie spacje.

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

4. Na urządzeniach z systemem Linux upewnij się, że użytkownik **iotedge** ma uprawnienia do odczytu w katalogu zawierającym certyfikaty. 

## <a name="deploy-edgehub-to-the-gateway"></a>Wdrażanie EdgeHub do bramy

Podczas pierwszej instalacji IoT Edge na urządzeniu zostanie automatycznie uruchomiony tylko jeden moduł systemowy: Agent IoT Edge. Twoje urządzenie z miejscem pracy jako bramy należy oba moduły systemu. Jeśli nie wdrożono żadnych modułów na urządzeniu bramy wcześniej, należy utworzyć początkowe wdrożenie dla urządzenia, aby uruchomić drugi moduł systemu, IoT Edge centrum. Wdrożenie będzie wyglądało puste, ponieważ nie dodasz żadnych modułów w kreatorze, ale upewni się, że oba moduły systemowe są uruchomione. 

Możesz sprawdzić, które moduły są uruchomione na urządzeniu za pomocą polecenia `iotedge list`. Jeśli lista zwróci tylko moduł **edgeAgent** bez **edgeHub**, wykonaj następujące czynności:

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

## <a name="open-ports-on-gateway-device"></a>Otwórz porty na urządzeniu bramy

Standardowe urządzenia IoT Edge nie potrzebują żadnej łączności przychodzącej z funkcją, ponieważ cała komunikacja z IoT Hub odbywa się za pośrednictwem połączeń wychodzących. Urządzenia bramy różnią się, ponieważ muszą odbierać komunikaty z urządzeń podrzędnych. Jeśli Zapora jest między urządzeniami podrzędnymi a urządzeniem bramy, komunikacja musi być również możliwa za pomocą zapory.

Aby scenariusz bramy działał prawidłowo, należy otworzyć co najmniej jeden z obsługiwanych protokołów IoT Edge Hub dla ruchu przychodzącego z urządzeń podrzędnych. Obsługiwane protokoły to MQTT, AMQP i HTTPS. 

| Port | Protocol |
| ---- | -------- |
| 8883 | MQTT |
| 5671 | AMQP |
| 443 | HTTPS <br> MQTT + WS <br> AMQP+WS | 

## <a name="route-messages-from-downstream-devices"></a>Routing komunikatów z urządzeń podrzędne
Komunikaty wysyłane z urządzeń podrzędne, podobnie jak komunikaty wysyłane przez moduły można kierować do środowiska uruchomieniowego usługi IoT Edge. Ta funkcja umożliwia wykonywanie analiz w module uruchomionym w bramie przed wysłaniem danych do chmury. 

Obecnie sposobem, w który trasy wiadomości wysyłanych przez urządzenia podrzędnego jest różnicując je na wiadomości wysłane przez moduły. Komunikaty wysyłane przez wszystkie moduły zawierają właściwość systemu o nazwie **connectionModuleId** , ale nie obsługują komunikaty wysyłane przez urządzenia podrzędnego. Aby wykluczyć wszystkie komunikaty, które zawierają tę właściwość systemu, można użyć klauzuli WHERE trasy. 

Poniższa trasa to przykład, który wyśle komunikaty z dowolnego urządzenia podrzędnego do modułu o `ai_insights`nazwie, a następnie `ai_insights` z do IoT Hub.

```json
{
    "routes":{
        "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")", 
        "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream" 
    } 
}
```

Aby uzyskać więcej informacji na temat routingu wiadomości zobacz [wdrażać moduły i trasy ustanowić](./module-composition.md#declare-routes).


## <a name="enable-extended-offline-operation"></a>Włącz rozszerzoną operację offline

Począwszy od [wersji v 1.0.4](https://github.com/Azure/azure-iotedge/releases/tag/1.0.4) środowiska uruchomieniowego IoT Edge, urządzenie bramy i urządzenia podrzędne łączące się z nim można skonfigurować do przedłużonej operacji w trybie offline. 

Dzięki tej możliwości lokalne moduły lub urządzenia podrzędne mogą ponownie uwierzytelniać się przy użyciu urządzenia z systemem IoT Edge i komunikować się ze sobą za pomocą komunikatów i metod nawet po rozłączeniu z Centrum IoT Hub. Aby uzyskać więcej informacji, zobacz [opis rozszerzony możliwości w trybie offline dla usługi IoT Edge, urządzeń, moduły i urządzeń podrzędnych](offline-capabilities.md).

Aby włączyć rozszerzone możliwości trybu offline, należy ustanowić relację nadrzędny-podrzędny między urządzeniem bramy IoT Edge a urządzeniami podrzędnymi, które będą się z nim połączyć. Te kroki zostały omówione bardziej szczegółowo w temacie [uwierzytelnianie urządzenia podrzędnego w usłudze Azure IoT Hub](how-to-authenticate-downstream-device.md).

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy masz urządzenia usługi IoT Edge działa jako przezroczystej bramy, należy skonfigurować podrzędne urządzenia, aby ufać bramy i wysyłanie komunikatów do niego. Aby uzyskać więcej informacji, zobacz [łączenie urządzenia podrzędnego z bramą Azure IoT Edge](how-to-connect-downstream-device.md) i [uwierzytelnianie urządzenia podrzędnego w usłudze Azure IoT Hub](how-to-authenticate-downstream-device.md).
