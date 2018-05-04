---
title: Utwórz urządzenie bramy przezroczysty Azure IoT krawędzi | Dokumentacja firmy Microsoft
description: Użyj Azure IoT Edge, aby utworzyć urządzenie bramy przezroczysty, który może przetwarzać informacje dla wielu urządzeń
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 12/04/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 0378cb2964a496a2bfe5a0bc08296cbab462a409
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="create-an-iot-edge-device-that-acts-as-a-transparent-gateway---preview"></a>Utwórz urządzenie brzegowe IoT, który działa jako brama przezroczysty — w wersji preview

Ten artykuł zawiera szczegółowe instrukcje dotyczące używania urządzenia IoT jako przezroczysty bramy. W pozostałej części niniejszego artykułu termin *brama brzegowa IoT* odwołuje się do urządzenia IoT używany jako przezroczysty bramy. Aby uzyskać szczegółowe informacje, zobacz [jak IoT urządzenia może służyć jako brama][lnk-edge-as-gateway], co daje omówienie pojęć. 

>[!NOTE]
>Obecnie:
> * Jeśli brama jest odłączona od centrum IoT, podrzędne urządzeń nie można uwierzytelnić z bramą.
> * Urządzenia brzegowe IoT nie może połączyć się bram granicznych IoT.

## <a name="understand-the-azure-iot-device-sdk"></a>Zrozumienie urządzenia Azure IoT SDK


Koncentratora krawędzi, który jest zainstalowany na wszystkich urządzeniach krawędzi IoT udostępnia następujące podstawowych urządzeniom podrzędne:

* komunikaty o stanie urządzenia do chmury i chmury do urządzenia
* bezpośrednie metody
* operacje dwie urządzenia

Obecnie podrzędne urządzenia nie będą mogły używać przekazywania pliku, podczas łączenia za pośrednictwem bramy IoT krawędzi.

Po podłączeniu urządzenia do bramy usługi IoT krawędzi przy użyciu zestawu SDK urządzenia Azure IoT należy:

* Instalowanie podrzędne urządzenia przy użyciu parametrów połączenia, odwołujący się do nazwy hosta urządzenia bramy; i
* Upewnij się, że urządzenie podrzędne ufa certyfikat używany do akceptowania połączeń przez urządzenie bramy.

Po zainstalowaniu środowisko uruchomieniowe krawędzi IoT Azure przy użyciu skryptu sterowania Centrum krawędzi utworzyć certyfikatu, tak jak w samouczku [zainstalować krawędź IoT symulowane urządzenie w systemie Windows] [ lnk-tutorial1-win] i [Linux][lnk-tutorial1-lin]. Ten certyfikat jest używany przez Centrum krawędzi do akceptowania przychodzących połączeń TLS, a musi być uważany za zaufany przez urządzenia podrzędne podczas nawiązywania połączenia z urządzeniem bramy.

Można utworzyć dowolnej infrastruktury certyfikatów, umożliwiającą zaufania wymagane dla topologii urządzenia bramy. W tym artykule przyjęto założenie, tę samą konfigurację certyfikatu, który ma zostać użyty do włączenia [zabezpieczeń urzędu certyfikacji X.509] [ lnk-iothub-x509] w Centrum IoT, które obejmuje certyfikat X.509 urzędu certyfikacji skojarzonego z określonym Centrum IoT ( *Właściciela Centrum IoT urzędu certyfikacji*) oraz serii certyfikatów, podpisanych tego urzędu certyfikacji, zainstalowane urządzenia IoT krawędzi.

>[!IMPORTANT]
>Obecnie krawędzi IoT i podrzędne urządzenia można używać tylko [tokeny sygnatury dostępu Współdzielonego] [ lnk-iothub-tokens] do uwierzytelniania za pomocą Centrum IoT. Certyfikaty będą używane tylko do zweryfikowania połączenia TLS między urządzeniem liścia i bramy.

Używa naszego konfiguracji **właściciela Centrum IoT urzędu certyfikacji** jednocześnie jako:
* Certyfikat podpisywania dla ustawień środowiska uruchomieniowego krawędzi IoT na wszystkie urządzenia IoT krawędzi; i
* Certyfikat klucza publicznego zainstalowany na urządzeniach podrzędne.

Powoduje to rozwiązanie umożliwiający wszystkich urządzeniach można używać dowolnego urządzenia IoT krawędź jako bramy, pod warunkiem, że są one połączone z tym samym Centrum IoT.

## <a name="create-the-certificates-for-test-scenarios"></a>Tworzenie certyfikatów dla scenariuszy testowania

Możesz użyć przykładu środowiska Powershell i skrypty powłoki systemowej opisanych w [Zarządzanie przykładowy certyfikat urzędu certyfikacji] [ lnk-ca-scripts] do wygenerowania z podpisem własnym **właściciela Centrum IoT urzędu certyfikacji** i certyfikaty urządzeń podpisany z nim.

>[!IMPORTANT]
>Ten przykład jest przeznaczony tylko do celów testowych. Scenariusze produkcji, można znaleźć w temacie [zabezpieczania wdrożenia IoT] [ lnk-iothub-secure-deployment] dla Azure IoT wytyczne dotyczące bezpiecznego rozwiązania IoT i odpowiednio obsługi administracyjnej certyfikatu.


1. Klonowanie zestawów SDK programu Microsoft Azure IoT i bibliotek dla języka C z usługi GitHub:

   ```cmd/sh
   git clone -b modules-preview https://github.com/Azure/azure-iot-sdk-c.git 
   ```

2. Aby zainstalować certyfikat skrypty, postępuj zgodnie z instrukcjami **krok 1 — konfiguracji początkowej** z [Zarządzanie przykładowy certyfikat urzędu certyfikacji][lnk-ca-scripts]. 
3. Aby wygenerować **właściciela Centrum IoT urzędu certyfikacji**, postępuj zgodnie z instrukcjami **krok 2 — Tworzenie łańcucha certyfikatów**. Ten plik jest używany przez urządzenia podrzędne można zweryfikować połączenia.
4. Aby wygenerować certyfikat dla urządzenie bramy, użyj instrukcji Bash lub programu PowerShell:

### <a name="bash"></a>Bash

Utwórz nowy certyfikat urządzenia.  **NIE** nazwa `myGatewayCAName` być taka sama jak nazwa hosta bramy.  W ten sposób spowoduje certyfikatu klienta przed te certyfikaty, aby zakończyć się niepowodzeniem.

   ```bash
   ./certGen.sh create_edge_device_certificate myGatewayCAName
   ```

Są tworzone nowe pliki:.\certs\new-edge-device.* zawiera klucz publiczny i PFX, a.\private\new-edge-device.key.pem klucza prywatnego na urządzeniu.
 
W `certs` katalogu, uruchom następujące polecenie, aby uzyskać pełny łańcuch klucza publicznego urządzenia:

   ```bash
   cd ./certs
   cat ./new-edge-device.cert.pem ./azure-iot-test-only.intermediate.cert.pem ./azure-iot-test-only.root.ca.cert.pem > ./new-edge-device-full-chain.cert.pem
   ```

### <a name="powershell"></a>PowerShell

Utwórz nowy certyfikat urządzenia: 
   ```powershell
   New-CACertsEdgeDevice myGateway
   ```

Są tworzone nowe pliki myEdgeDevice *, które zawierają klucz publiczny, klucz prywatny i PFX tego certyfikatu. 

Po wyświetleniu monitu o wprowadzenie hasła podczas podpisywania, wprowadź "1234".

## <a name="configure-a-gateway-device"></a>Skonfiguruj urządzenie bramy

Aby skonfigurować urządzenia IoT krawędź jako bramy wystarczy skonfigurować do używania certyfikatu urządzenia utworzony w poprzedniej sekcji.

Przyjęto założenie, następujące nazwy plików z powyższych przykładowe skrypty:

| Dane wyjściowe | Nazwa pliku |
| ------ | --------- |
| Certyfikat urządzenia | `certs/new-edge-device.cert.pem` |
| Klucza prywatnego na urządzeniu | `private/new-edge-device.cert.pem` |
| Łańcuch certyfikatów urządzenia | `certs/new-edge-device-full-chain.cert.pem` |
| Właściciel Centrum IoT urzędu certyfikacji | `certs/azure-iot-test-only.root.ca.cert.pem`  |

Podaj informacje urządzenia i certyfikatu do środowiska wykonawczego IoT krawędzi. 
 
W systemie Linux przy użyciu Bash dane wyjściowe:

   ```bash
   sudo iotedgectl setup --connection-string {device connection string} \
        --edge-hostname {gateway hostname, e.g. mygateway.contoso.com} \
        --device-ca-cert-file {full path}/certs/new-edge-device.cert.pem \
        --device-ca-chain-cert-file {full path}/certs/new-edge-device-full-chain.cert.pem \
        --device-ca-private-key-file {full path}/private/new-edge-device.key.pem \
        --owner-ca-cert-file {full path}/certs/azure-iot-test-only.root.ca.cert.pem
   ```

W systemie Windows przy użyciu dane wyjściowe programu PowerShell:

   ```powershell
   iotedgectl setup --connection-string {device connection string}
        --edge-hostname {gateway hostname, e.g. mygateway.contoso.com}
        --device-ca-cert-file {full path}/certs/new-edge-device.cert.pem
        --device-ca-chain-cert-file {full path}/certs/new-edge-device-full-chain.cert.pem
        --device-ca-private-key-file {full path}/private/new-edge-device.key.pem
        --owner-ca-cert-file {full path}/RootCA.pem
   ```

Domyślnie przykładowe skrypty nie ustawiono hasło klucza prywatnego na urządzeniu. Jeśli ustawisz hasło, Dodaj następujący parametr: `--device-ca-passphrase {passphrase}`.

Skrypt wyświetli monit o ustawić hasło dla certyfikatu agenta krawędzi. Po wykonaniu tego polecenia, należy ponownie uruchomić środowiska uruchomieniowego krawędzi IoT:

   ```cmd/sh
   iotedgectl restart
   ```

## <a name="configure-a-downstream-device"></a>Skonfiguruj urządzenie podrzędne

Podrzędne urządzenie może być dowolną aplikację przy użyciu [urządzenia Azure IoT SDK][lnk-devicesdk], takie jak opisano to proste w [Podłącz urządzenie do Centrum IoT przy użyciu platformy .NET] [ lnk-iothub-getstarted].

Najpierw aplikacji podrzędne urządzenie ma ufać **właściciela Centrum IoT urzędu certyfikacji** certyfikatu w celu weryfikowania połączeń TLS do urządzenia bramy. Ten krok, zwykle można przeprowadzić na dwa sposoby: na poziomie systemu operacyjnego lub (w przypadku niektórych języków) na poziomie aplikacji.

Na przykład aplikacji .NET można dodać następujący fragment kodu zaufania certyfikatu w formacie PEM składowanych w ścieżce `certPath`. W zależności od wersji programu można użyć skryptu, ścieżka odwołuje się do albo `certs/azure-iot-test-only.root.ca.cert.pem` (Bash) lub `RootCA.pem` (Powershell).

   ```csharp
   using System.Security.Cryptography.X509Certificates;
   
   ...

   X509Store store = new X509Store(StoreName.Root, StoreLocation.CurrentUser);
   store.Open(OpenFlags.ReadWrite);
   store.Add(new X509Certificate2(X509Certificate2.CreateFromCertFile(certPath)));
   store.Close();
   ```

Wykonanie tego kroku na poziomie systemu operacyjnego różni się od systemu Windows i między dystrybucje systemu Linux.

Drugim krokiem jest zainicjować sdk urządzenia IoT Hub parametrami połączenia odwołujących się do nazwy hosta z urządzeniem bramy.
Jest to realizowane przez dodanie `GatewayHostName` właściwości parametrów połączenia urządzenia. Na przykład poniżej przedstawiono przykładowe parametry połączenia urządzenia dla urządzeń, do którego możemy dołączany `GatewayHostName` właściwości:

   ```
   HostName=yourHub.azure-devices-int.net;DeviceId=yourDevice;SharedAccessKey=2BUaYca45uBS/O1AsawsuQslH4GX+SPkrytydWNdFxc=;GatewayHostName=mygateway.contoso.com
   ```

Następujące dwa kroki Włącz aplikacji urządzenia do nawiązania połączenia z urządzeniem bramy.

## <a name="next-steps"></a>Kolejne kroki
[Zrozumieć wymagania i narzędzi do tworzenia modułów krawędzi IoT][lnk-module-dev].

[lnk-devicesdk]: ../iot-hub/iot-hub-devguide-sdks.md
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
[lnk-edge-as-gateway]: ./iot-edge-as-gateway.md
[lnk-module-dev]: module-development.md
[lnk-iothub-getstarted]: ../iot-hub/iot-hub-csharp-csharp-getstarted.md
[lnk-iothub-x509]: ../iot-hub/iot-hub-x509ca-overview.md
[lnk-iothub-secure-deployment]: ../iot-hub/iot-hub-security-deployment.md
[lnk-iothub-tokens]: ../iot-hub/iot-hub-devguide-security.md#security-tokens 
[lnk-iothub-throttles-quotas]: ../iot-hub/iot-hub-devguide-quotas-throttling.md
[lnk-iothub-devicetwins]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-iothub-c2d]: ../iot-hub/iot-hub-devguide-messages-c2d.md
[lnk-ca-scripts]: https://github.com/Azure/azure-iot-sdk-c/blob/modules-preview/tools/CACertificates/CACertificateOverview.md
[lnk-modbus-module]: https://github.com/Azure/iot-edge-modbus
