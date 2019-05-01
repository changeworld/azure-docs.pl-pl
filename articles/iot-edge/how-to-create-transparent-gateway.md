---
title: Tworzenie przezroczystej bramy urządzenia — usługi Azure IoT Edge | Dokumentacja firmy Microsoft
description: Używanie usługi Azure IoT Edge urządzenia rolę przezroczystej bramy, która może przetwarzać informacje z urządzeń podrzędne
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/23/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 722ee6197b467454818026c960e1ce0e5b39efb4
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64717199"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>Konfigurowanie urządzenia usługi IoT Edge, aby pełnić rolę przezroczystej bramy

Ten artykuł zawiera szczegółowe instrukcje dotyczące konfigurowania urządzenia usługi IoT Edge działa jak przezroczystej bramy w przypadku innych urządzeń do komunikowania się z usługą IoT Hub. W tym artykule określenie *brama usługi IoT Edge* odwołuje się do urządzenia usługi IoT Edge używany jako przezroczystej bramy. Aby uzyskać więcej informacji, zobacz [jak IoT Edge urządzenia mogą być używane jako brama](./iot-edge-as-gateway.md).

>[!NOTE]
>Obecnie:
> * Jeśli brama zostanie odłączony od usługi IoT Hub, podrzędnym urządzenia nie mogą uwierzytelniać za pomocą bramy.
> * Urządzenia z włączoną obsługą usługi Edge nie można nawiązać połączenia bramy usługi IoT Edge. 
> * Podrzędne urządzenia nie mogą używać przekazywania plików.

W przypadku urządzeń do działania jako brama musi mieć możliwość nawiązania bezpiecznego połączenia podrzędnego urządzeń. Usługa Azure IoT Edge umożliwia użycie infrastruktury kluczy publicznych (PKI) do skonfigurowania bezpiecznych połączeń między urządzeniami. W tym przypadku możemy zezwolenie podrzędnym urządzenia połączyć się z urządzenia usługi IoT Edge, działając jako przezroczystej bramy. Aby zachować bezpieczeństwo uzasadnione, podrzędnym urządzenia powinny potwierdzenia tożsamości urządzenia usługi IoT Edge. Chcesz, aby urządzenia, na nawiązywanie połączeń tylko bram, nie wszystkie bramy mogą okazać się złośliwe.

Podrzędne urządzenie może pozostawać w dowolnej aplikacji lub platformy, który został utworzony za pomocą tożsamości [usługi Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub) usługi w chmurze. W wielu przypadkach te aplikacje korzystają [zestaw SDK urządzeń Azure IoT](../iot-hub/iot-hub-devguide-sdks.md). Praktycznego podrzędne urządzenia można nawet aplikację działającą na samym urządzeniu bramy usługi IoT Edge. 

Można utworzyć żadnej infrastruktury certyfikatów, umożliwiająca zaufania wymagane dla topologii urządzenia bramy. W tym artykule przyjęto założenie, że tę samą konfigurację certyfikatu, który zostanie wykorzystany do włączenia [zabezpieczeń urzędu certyfikacji X.509](../iot-hub/iot-hub-x509ca-overview.md) w usłudze IoT Hub, który obejmuje certyfikat X.509 urzędu certyfikacji, powiązanych z określonej usługi IoT hub (IoT hub właściciel urzędu certyfikacji) i serii certyfikatów, zarejestrowana za pomocą tego urzędu certyfikacji i urzędu certyfikacji do urządzenia usługi IoT Edge.

![Konfiguracja certyfikatu bramy](./media/how-to-create-transparent-gateway/gateway-setup.png)

Brama przedstawia informacje o jego usługi IoT Edge urządzenia urzędu certyfikacji, aby podrzędnym urządzenia podczas inicjowania połączenia. Podrzędne urządzenie sprawdza upewnij się, że certyfikat urzędu certyfikacji urządzenia usługi IoT Edge jest podpisany przez właściciela certyfikatu urzędu certyfikacji. Ten proces umożliwia podrzędnym urządzenia upewnić się, że brama pochodzi z zaufanego źródła.

W poniższych krokach objaśniono proces tworzenia certyfikatów i instalowania ich w odpowiednich miejscach.

## <a name="prerequisites"></a>Wymagania wstępne

Urządzenia z systemem Azure IoT Edge można skonfigurować jako bramę. Wykonaj kroki instalacji usługi IoT Edge dla następujących systemów operacyjnych:
* [Windows](./how-to-install-iot-edge-windows.md)
* [Linux x64](./how-to-install-iot-edge-linux.md)
* [ARM32 systemu Linux](./how-to-install-iot-edge-linux-arm.md)

Można użyć dowolnej maszyny do generowania certyfikatów, a następnie skopiuj je do urządzenia usługi IoT Edge.

>[!NOTE]
>"Nazwa bramy" służąca do tworzenia certyfikatów w tej instrukcji musi mieć taką samą nazwę jak używana jako nazwa hosta w pliku config.yaml usługi IoT Edge i GatewayHostName w parametrach połączenia podrzędnego urządzenia. "Nazwa bramy" musi być rozpoznawana jako adres IP, albo za pomocą DNS lub wpisu w pliku hostów. Komunikacja oparta na protokół używany (MQTTS:8883 / AMQPS:5671 / HTTPS:433) musi być możliwe między podrzędnymi urządzeniami i transparant usługi IoT Edge. Jeśli Zapora jest między, odpowiedni port musi być otwarty.

## <a name="generate-certificates-with-windows"></a>Generowanie certyfikatów z Windows

Wykonaj kroki w tej sekcji, aby wygenerować Certyfikaty testowe na urządzeniu z systemem Windows. Te kroki służą do generowania certyfikatów na urządzeniu Windows IoT Edge. Alternatywnie można wygenerować certyfikaty na komputerze deweloperskim Windows, a następnie skopiuj je na dowolne urządzenie usługi IoT Edge. 

Certyfikatów wygenerowanych w tej sekcji są przeznaczone tylko do celów testowych. 

### <a name="install-openssl"></a>Zainstalować protokół OpenSSL

Zainstaluj biblioteki OpenSSL dla Windows na komputerze, którego używasz w celu wygenerowania certyfikatów. Istnieje kilka sposobów, należy zainstalować protokół OpenSSL:

   >[!NOTE]
   >Jeśli masz już zainstalowane na urządzeniu z systemem Windows biblioteki OpenSSL, można pominąć ten krok, ale upewnij się, że ten openssl.exe jest dostępny w zmiennej środowiskowej PATH.

* **Łatwiejsze:** Pobierz i zainstaluj dowolne [plików binarnych biblioteki OpenSSL firm](https://wiki.openssl.org/index.php/Binaries), na przykład z [tego projektu na SourceForge](https://sourceforge.net/projects/openssl/). Dodanie pełnej ścieżki do openssl.exe do zmiennej środowiskowej PATH. 
   
* **Zalecane:** Pobierz kod źródłowy biblioteki OpenSSL i kompilowania plików binarnych na komputerze, samodzielnie lub za pośrednictwem [vcpkg](https://github.com/Microsoft/vcpkg). Zgodnie z instrukcjami podanymi poniżej umożliwia vcpkg pobierania kodu źródłowego, kompilacji i zainstaluj OpenSSL na komputerze Windows za pomocą prostych krokach.

   1. Przejdź do katalogu, w którym chcesz zainstalować vcpkg. Będziemy odnosić się do tego katalogu jako  *\<VCPKGDIR >*. Postępuj zgodnie z instrukcjami, aby pobrać i zainstalować [vcpkg](https://github.com/Microsoft/vcpkg).
   
   2. Po zainstalowaniu vcpkg w wierszu polecenia programu powershell uruchom następujące polecenie, aby zainstalować pakiet biblioteki OpenSSL dla Windows x64. Instalacja trwa zwykle około 5 minut.

      ```powershell
      .\vcpkg install openssl:x64-windows
      ```
   3. Dodaj `<VCPKGDIR>\installed\x64-windows\tools\openssl` do zmiennej środowiskowej PATH, aby plik openssl.exe jest dostępny do wywołania.

### <a name="prepare-creation-scripts"></a>Przygotowanie skryptów tworzenia

Zestaw SDK urządzeń Azure IoT dla języka C zawiera skrypty używane do generowania certyfikatów testu. W tej sekcji możesz sklonować zestaw SDK i konfigurowanie programu PowerShell.

1. Otwórz okno programu PowerShell w trybie administratora. 

2. Sklonuj repozytorium git, zawierający skrypty w celu wygenerowania certyfikatów nieprodukcyjnych. Te skrypty pomocne podczas tworzenia wymagane certyfikaty, aby skonfigurować przezroczystej bramy. Użyj `git clone` polecenia lub [Pobierz plik ZIP](https://github.com/Azure/azure-iot-sdk-c/archive/master.zip). 

   ```powershell
   git clone https://github.com/Azure/azure-iot-sdk-c.git
   ```

3. Przejdź do katalogu, w którym chcesz pracować. Będziemy odnosić się do tego katalogu jako  *\<WRKDIR >*.  Wszystkie pliki zostaną utworzone w tym katalogu.

4. Skopiuj pliki konfiguracji i skrypt w katalogu roboczym. 

   ```powershell
   copy <path>\azure-iot-sdk-c\tools\CACertificates\*.cnf .
   copy <path>\azure-iot-sdk-c\tools\CACertificates\ca-certs.ps1 .
   ```

   Jeśli pobrano zestaw SDK w formie pliku ZIP, a następnie nazwa folderu jest `azure-iot-sdk-c-master` i pozostałą część ścieżki jest taka sama. 

5. Ustaw zmienną środowiskową OPENSSL_CONF przy użyciu pliku konfiguracji openssl_root_ca.cnf.

    ```powershell
    $env:OPENSSL_CONF = "$PWD\openssl_root_ca.cnf"
    ```

6. Włącz program PowerShell w celu uruchamiania skryptów.

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser
   ```

7. Przenieś funkcje używane przez skrypty w globalnej przestrzeni nazw programu PowerShell.
   
   ```powershell
   . .\ca-certs.ps1
   ```

8. Sprawdź, czy biblioteki OpenSSL został poprawnie zainstalowany i upewnij się, że nie będzie konfliktów nazw z istniejących certyfikatów. Jeśli wystąpią problemy, skrypt powinna opisywać sposób rozwiązać problemy w systemie.

   ```powershell
   Test-CACertsPrerequisites
   ```

### <a name="create-certificates"></a>Tworzenie certyfikatów

W tej sekcji utworzysz trzy certyfikaty i połączyć je w łańcuch. Umieszczenie certyfikatów w łańcuchu pliku pozwala łatwo zainstalować je na urządzenie bramy usługi IoT Edge i wszystkie podrzędne urządzenia.  

1. Utwórz certyfikat z urzędu certyfikacji właścicielem i go zarejestrować jeden certyfikat pośredniego. Certyfikaty są umieszczane w  *\<WRKDIR >*.

      ```powershell
      New-CACertsCertChain rsa
      ```

2. Tworzenie usługi IoT Edge urządzenia certyfikatu urzędu certyfikacji i klucza prywatnego za pomocą następującego polecenia. Podaj nazwę dla urządzenia bramy, która będzie służyć do nazwy plików i podczas generowania certyfikatów. 

   ```powershell
   New-CACertsEdgeDevice "<gateway name>"
   ```

3. Tworzenie łańcucha certyfikatów od właściciela certyfikatu urzędu certyfikacji, pośredniego certyfikatu i certyfikat urzędu certyfikacji urządzenia usługi IoT Edge przy użyciu następującego polecenia. 

   ```powershell
   Write-CACertsCertificatesForEdgeDevice "<gateway name>"
   ```

   Skrypt tworzy następujące certyfikaty i klucza:
   * `<WRKDIR>\certs\new-edge-device.*`
   * `<WRKDIR>\private\new-edge-device.key.pem`
   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

## <a name="generate-certificates-with-linux"></a>Generowanie certyfikatów z systemem Linux

Wykonaj kroki w tej sekcji, aby wygenerować Certyfikaty testowe na urządzeniu z systemem Linux. Można wygenerować certyfikaty na urządzeniu usługi IoT Edge, lub użyć osobnej maszyny i skopiować końcowej certyfikaty na dowolne urządzenie usługi IoT Edge, w dowolnym obsługiwanym systemem operacyjnym. 

### <a name="prepare-creation-scripts"></a>Przygotowanie skryptów tworzenia

1. Sklonuj repozytorium git, zawierający skrypty w celu wygenerowania certyfikatów nieprodukcyjnych. Te skrypty pomocne podczas tworzenia wymagane certyfikaty, aby skonfigurować przezroczystej bramy. 

   ```bash
   git clone https://github.com/Azure/azure-iot-sdk-c.git
   ```

2. Przejdź do katalogu, w którym chcesz pracować. Będziemy odnosić się do tego katalogu jako  *\<WRKDIR >*.  Wszystkie pliki zostaną utworzone w tym katalogu.
  
3. Skopiuj pliki konfiguracji i skrypt w katalogu roboczym.

   ```bash
   cp <path>/azure-iot-sdk-c/tools/CACertificates/*.cnf .
   cp <path>/azure-iot-sdk-c/tools/CACertificates/certGen.sh .
   ```

4. Konfigurowanie biblioteki OpenSSL w celu wygenerowania certyfikatów przy użyciu dostarczonego skryptu. 

   ```bash
   chmod 700 certGen.sh 
   ```

### <a name="create-certificates"></a>Tworzenie certyfikatów

W tej sekcji utworzysz trzy certyfikaty i połączyć je w łańcuch. Umieszczenie certyfikatów w łańcuchu pliku pozwala łatwo zainstalować je na urządzenie bramy usługi IoT Edge i wszystkie podrzędne urządzenia.  

1. Utwórz certyfikat urzędu certyfikacji właściciela i jeden certyfikat pośredniego. Te certyfikaty są umieszczane w  *\<WRKDIR >*.

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   Skrypt tworzy następujące certyfikaty i klucze:
   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`
   * `<WRKDIR>/certs/azure-iot-test-only.intermediate.cert.pem`
   * `<WRKDIR>/private/azure-iot-test-only.root.ca.key.pem`
   * `<WRKDIR>/private/azure-iot-test-only.intermediate.key.pem`

2. Tworzenie usługi IoT Edge urządzenia certyfikatu urzędu certyfikacji i klucza prywatnego za pomocą następującego polecenia. Podaj nazwę dla urządzenia bramy, która będzie służyć do nazwy plików i podczas generowania certyfikatów. 

   ```bash
   ./certGen.sh create_edge_device_certificate "<gateway name>"
   ```

   Skrypt tworzy następujące certyfikaty i klucza:
   * `<WRKDIR>/certs/new-edge-device.*`
   * `<WRKDIR>/private/new-edge-device.key.pem`

3. Tworzenie łańcucha certyfikatów, o nazwie **nowy edge — urządzenia — pełny — chain.cert.pem** od właściciela urzędu certyfikacji certyfikat pośredniego certyfikatu i certyfikat urzędu certyfikacji urządzenia usługi IoT Edge.

   ```bash
   cat ./certs/new-edge-device.cert.pem ./certs/azure-iot-test-only.intermediate.cert.pem ./certs/azure-iot-test-only.root.ca.cert.pem > ./certs/new-edge-device-full-chain.cert.pem
   ```

## <a name="install-certificates-on-the-gateway"></a>Instalowanie certyfikatów na bramę

Po utworzeniu łańcucha certyfikatów, należy go zainstalować na urządzeniu bramy usługi IoT Edge i konfigurowanie środowiska uruchomieniowego usługi IoT Edge, aby odwoływać się do nowych certyfikatów. 

1. Skopiuj następujące pliki z  *\<WRKDIR >*. Zapisz je w dowolnym miejscu na urządzeniu usługi IoT Edge. Będziemy odnosić się do katalogu docelowego na twoim urządzeniu usługi IoT Edge jako  *\<CERTDIR >*. 

   Jeśli certyfikaty na samym urządzeniu usługi IoT Edge jest generowany, możesz pominąć ten krok i użyć ścieżki do katalogu roboczego.

   * Certyfikat dostępu Warunkowego do urządzeń —  `<WRKDIR>\certs\new-edge-device-full-chain.cert.pem`
   * Klucz prywatny urzędu certyfikacji urządzenia — `<WRKDIR>\private\new-edge-device.key.pem`
   * Właściciel CA- `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

2. Otwórz plik konfiguracji demona zabezpieczeń usługi IoT Edge. 

   * Windows: `C:\ProgramData\iotedge\config.yaml`
   * Linux: `/etc/iotedge/config.yaml`

3. Ustaw **certyfikatu** właściwości w pliku config.yaml do ścieżki, w którym została umieszczona certyfikat i klucz plików na urządzeniu usługi IoT Edge.

   * W systemie Windows:

      ```yaml
      certificates:
        device_ca_cert: "<CERTDIR>\\certs\\new-edge-device-full-chain.cert.pem"
        device_ca_pk: "<CERTDIR>\\private\\new-edge-device.key.pem"
        trusted_ca_certs: "<CERTDIR>\\certs\\azure-iot-test-only.root.ca.cert.pem"
      ```
   
   * W systemie Linux: 
      ```yaml
      certificates:
        device_ca_cert: "<CERTDIR>/certs/new-edge-device-full-chain.cert.pem"
        device_ca_pk: "<CERTDIR>/private/new-edge-device.key.pem"
        trusted_ca_certs: "<CERTDIR>/certs/azure-iot-test-only.root.ca.cert.pem"
      ```

4. Na urządzeniach z systemem Linux, upewnij się, że użytkownik **iotedge** ma uprawnienia odczytu wobec katalogu zawierający certyfikaty. 

## <a name="deploy-edgehub-to-the-gateway"></a>Wdrażanie EdgeHub do bramy

Podczas pierwszej instalacji usługi IoT Edge na urządzeniu, moduł tylko jeden system jest uruchamiany automatycznie: agenta usługi IoT Edge. Twoje urządzenie z miejscem pracy jako bramy należy oba moduły systemu. Jeśli wszystkie moduły nie zostały wdrożone na urządzeniu bramy przed, należy utworzyć wdrożenia dla Twojego urządzenia, które można uruchomić modułu systemu z drugiego Centrum usługi IoT Edge. Wdrożenie będzie wyglądać pusty, ponieważ wszystkie moduły nie dodawaj kreatora, ale zostanie wdrożona, oba moduły systemu. 

Możesz sprawdzić, które moduły są uruchomione na urządzeniu za pomocą polecenia `iotedge list`.

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

Standardowa urządzenia usługi IoT Edge nie jest konieczne połączenie przychodzące do funkcji, ponieważ cała komunikacja z usługą IoT Hub odbywa się za pośrednictwem połączeń wychodzących. Jednak urządzenia bramy są różne, ponieważ muszą być w stanie odbierać komunikaty z urządzeń podrzędnego.

Scenariusz bramy do pracy co najmniej jeden z obsługiwanych protokołów Centrum IoT Edge musi być otwarty dla ruchu przychodzącego z podrzędnym urządzeń. Obsługiwane protokoły to MQTT, AMQP i HTTPS.

| Port | Protokół |
| ---- | -------- |
| 8883 | MQTT |
| 5671 | AMQP |
| 443 | HTTPS <br> MQTT+WS <br> AMQP+WS | 

## <a name="route-messages-from-downstream-devices"></a>Routing komunikatów z urządzeń podrzędne
Komunikaty wysyłane z urządzeń podrzędne, podobnie jak komunikaty wysyłane przez moduły można kierować do środowiska uruchomieniowego usługi IoT Edge. Ta funkcja umożliwia przeprowadzanie analiz w module uruchomiona na bramie przed wysłaniem danych do chmury. 

Obecnie sposobem, w który trasy wiadomości wysyłanych przez urządzenia podrzędnego jest różnicując je na wiadomości wysłane przez moduły. Komunikaty wysyłane przez wszystkie moduły zawierają właściwość systemu o nazwie **connectionModuleId** , ale nie obsługują komunikaty wysyłane przez urządzenia podrzędnego. Aby wykluczyć wszystkie komunikaty, które zawierają tę właściwość systemu, można użyć klauzuli WHERE trasy. 

Poniżej trasy będzie służyć do wysyłania wiadomości z dowolnego urządzenia podrzędnego do nazwy modułu `ai_insights`.

```json
{
    "routes":{
        "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")", 
        "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream" 
    } 
}
```

Aby uzyskać więcej informacji na temat routingu wiadomości zobacz [wdrażać moduły i trasy ustanowić](./module-composition.md#declare-routes).

[!INCLUDE [iot-edge-extended-ofline-preview](../../includes/iot-edge-extended-offline-preview.md)]

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy masz urządzenia usługi IoT Edge działa jako przezroczystej bramy, należy skonfigurować podrzędne urządzenia, aby ufać bramy i wysyłanie komunikatów do niego. Aby uzyskać więcej informacji, zobacz [połączyć podrzędny urządzenie bramy usługi Azure IoT Edge](how-to-connect-downstream-device.md).
