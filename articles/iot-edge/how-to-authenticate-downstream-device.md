---
title: Uwierzytelnianie urządzeń podrzędnych — Azure IoT Edge | Microsoft Docs
description: Jak uwierzytelniać urządzenia podrzędne lub urządzenia typu liść w celu IoT Hub i kierować ich połączenia za poorednictwem urządzeń bram Azure IoT Edge Gateway.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 09/23/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 922654a6947a21eeee945762100abe086c552ad7
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457223"
---
# <a name="authenticate-a-downstream-device-to-azure-iot-hub"></a>Uwierzytelnianie urządzenia podrzędnego w usłudze Azure IoT Hub

W przypadku niejawnego scenariusza bramy urządzenia podrzędne (nazywane czasami urządzeniami typu liść lub urządzeniami podrzędnymi) potrzebują tożsamości w IoT Hub jak każde inne urządzenie. W tym artykule opisano opcje uwierzytelniania urządzenia podrzędnego w IoT Hub, a następnie pokazano, jak zadeklarować połączenie bramy.

Należy wykonać trzy ogólne kroki, aby skonfigurować pomyślne, przezroczyste połączenie bramy. W tym artykule omówiono drugi krok:

1. Urządzenie bramy musi być w stanie bezpiecznie łączyć się z urządzeniami podrzędnymi, odbierać komunikaty z urządzeń podrzędnych i kierować komunikaty do odpowiednich miejsc docelowych. Aby uzyskać więcej informacji, zobacz [Konfigurowanie urządzenia IoT Edge do działania jako nieprzezroczyste bramy](how-to-create-transparent-gateway.md).
2. **Urządzenie podrzędne musi mieć tożsamość urządzenia, aby można było uwierzytelnić się przy użyciu IoT Hub i wiedzieć o komunikacji za pomocą swojego urządzenia bramy.**
3. Urządzenie podrzędne musi mieć możliwość bezpiecznego łączenia się z urządzeniem bramy. Aby uzyskać więcej informacji, zobacz [łączenie urządzenia podrzędnego z bramą Azure IoT Edge](how-to-connect-downstream-device.md).

Urządzenia podrzędne mogą uwierzytelniać się za pomocą IoT Hub przy użyciu jednej z trzech metod: klucze symetryczne (czasami określane jako klucze dostępu współdzielonego), certyfikaty z podpisem własnym X. 509 lub certyfikaty z certyfikatem X. 509 (CA). Kroki uwierzytelniania są podobne do kroków użytych w celu skonfigurowania dowolnego urządzenia niezwiązanego z usługą IoT-Edge z IoT Hub z małymi różnicami w celu zadeklarować relacji bramy.

W krokach przedstawionych w tym artykule przedstawiono Ręczne inicjowanie obsługi urządzeń, a nie automatyczne Inicjowanie obsługi przy użyciu IoT Hub Device Provisioning Service platformy Azure. 

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj kroki opisane w [sekcji Konfigurowanie urządzenia IoT Edge do działania jako nieprzezroczyste bramy](how-to-create-transparent-gateway.md). Jeśli używasz uwierzytelniania X. 509 dla urządzenia podrzędnego, musisz użyć tego samego skryptu generującego ten sam certyfikat, który został skonfigurowany w przezroczystym artykule bramy. 

Ten artykuł odnosi się do *nazwy hosta bramy* w kilku punktach. Nazwa hosta bramy jest zadeklarowana w parametrze **hostname** pliku config. YAML na urządzeniu bramy IoT Edge. Służy do tworzenia certyfikatów w tym artykule i jest określany w parametrach połączenia urządzeń podrzędnych. Nazwa hosta bramy musi być rozpoznawalna na adres IP przy użyciu systemu DNS lub wpisu pliku hosta.

## <a name="symmetric-key-authentication"></a>Uwierzytelnianie klucza symetrycznego

Uwierzytelnianie przy użyciu klucza symetrycznego lub uwierzytelnianie klucza dostępu współdzielonego jest najprostszym sposobem na uwierzytelnianie za pomocą IoT Hub. W przypadku uwierzytelniania przy użyciu klucza symetrycznego klucz Base64 jest skojarzony z IDENTYFIKATORem urządzenia IoT w IoT Hub. Ten klucz jest dołączany do aplikacji IoT, aby urządzenie mógł je przedstawić podczas łączenia się z IoT Hub. 

### <a name="create-the-device-identity"></a>Tworzenie tożsamości urządzenia 

Dodaj nowe urządzenie IoT w centrum IoT Hub przy użyciu Azure Portal, interfejsu wiersza polecenia platformy Azure lub rozszerzenia IoT dla Visual Studio Code. Należy pamiętać, że urządzenia podrzędne muszą być identyfikowane w IoT Hub jako zwykłe urządzenie IoT, a nie IoT Edge urządzeń. 

Podczas tworzenia nowej tożsamości urządzenia podaj następujące informacje: 

* Utwórz identyfikator urządzenia.

* Wybierz **klucz symetryczny** jako typ uwierzytelniania. 

* Opcjonalnie wybierz **ustawienie urządzenie nadrzędne** i wybierz urządzenie bramy IoT Edge, za pomocą którego będzie nawiązywane połączenie. Ten krok jest opcjonalny w przypadku uwierzytelniania przy użyciu klucza symetrycznego, ale jest zalecane, ponieważ ustawienie urządzenia nadrzędnego umożliwia korzystanie z [funkcji offline](offline-capabilities.md) dla urządzenia podrzędnego. Możesz zawsze aktualizować szczegóły urządzenia, aby później dodać lub zmienić element nadrzędny. 

   ![Tworzenie identyfikatora urządzenia z uwierzytelnianiem przy użyciu klucza symetrycznego w portalu](./media/how-to-authenticate-downstream-device/symmetric-key-portal.png)

Aby ukończyć tę samą operację, można użyć [rozszerzenia IoT dla interfejsu wiersza polecenia platformy Azure](https://github.com/Azure/azure-iot-cli-extension) . Poniższy przykład tworzy nowe urządzenie IoT z uwierzytelnianiem przy użyciu klucza symetrycznego i przypisuje urządzenie nadrzędne: 

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID}
```

Aby uzyskać więcej informacji na temat poleceń interfejsu wiersza polecenia platformy Azure służących do tworzenia i zarządzania urządzeniami nadrzędnymi/podrzędnymi, zobacz zawartość referencyjną polecenia [AZ IoT Hub Device-Identity](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) .

### <a name="connect-to-iot-hub-through-a-gateway"></a>Nawiązywanie połączenia z IoT Hub za pomocą bramy

Ten sam proces służy do uwierzytelniania zwykłych urządzeń IoT w celu IoT Hub z użyciem kluczy symetrycznych również ma zastosowanie do urządzeń podrzędnych. Jedyną różnicą jest to, że należy dodać wskaźnik do urządzenia bramy, aby skierować połączenie, lub w scenariuszach w trybie offline, aby obsłużyć uwierzytelnianie w imieniu IoT Hub. 

W przypadku uwierzytelniania przy użyciu klucza symetrycznego nie ma dodatkowych kroków, które należy wykonać na urządzeniu, aby można było uwierzytelnić się w usłudze IoT Hub. Nadal są wymagane certyfikaty, aby urządzenie podrzędne mogły połączyć się ze swoim urządzeniem bramy, zgodnie z opisem w temacie [łączenie urządzenia podrzędnego z bramą Azure IoT Edge](how-to-connect-downstream-device.md).

Po utworzeniu tożsamości urządzenia IoT w portalu można pobrać jej klucze podstawowe lub pomocnicze. Jeden z tych kluczy musi być uwzględniony w parametrach połączenia, które znajdują się w dowolnej aplikacji, która komunikuje się z IoT Hub. W przypadku uwierzytelniania przy użyciu klucza symetrycznego IoT Hub zapewnia w pełni uformowane parametry połączenia w szczegółach urządzenia dla wygody użytkownika. Należy dodać dodatkowe informacje o urządzeniu bramy do parametrów połączenia. 

Parametry połączenia klucza symetrycznego dla urządzeń podrzędnych muszą mieć następujące składniki: 

* Centrum IoT, z którym łączy się urządzenie: `Hostname={iothub name}.azure-devices.net`
* Identyfikator urządzenia zarejestrowany w centrum: `DeviceID={device ID}`
* Klucz podstawowy lub pomocniczy: `SharedAccessKey={key}`
* Urządzenie bramy, za pomocą którego urządzenie nawiązuje połączenie. Podaj wartość **hostname** z pliku config. YAML urządzenia bramy IoT Edge: `GatewayHostName={gateway hostname}`

Wszystkie razem, kompletne parametry połączenia wyglądają następująco:

``` 
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz;GatewayHostName=myGatewayDevice
```

Jeśli została ustanowiona relacja nadrzędny/podrzędny dla tego urządzenia podrzędnego, można uprościć parametry połączenia przez wywołanie bramy bezpośrednio jako hosta połączenia. Na przykład: 

```
HostName=myGatewayDevice;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz
```

## <a name="x509-authentication"></a>Uwierzytelnianie X. 509 

Istnieją dwa sposoby uwierzytelniania urządzenia IoT przy użyciu certyfikatów X. 509. W zależności od sposobu uwierzytelniania należy wykonać kroki, aby połączyć urządzenie z IoT Hub są takie same. Wybierz certyfikaty z podpisem własnym lub certyfikat podpisany przez urząd certyfikacji na potrzeby uwierzytelniania, a następnie przejdź do programu, aby dowiedzieć się, jak nawiązać połączenie z usługą IoT Hub. 

Aby uzyskać więcej informacji na temat sposobu używania uwierzytelniania X. 509 przez IoT Hub, zobacz następujące artykuły: 
* [Uwierzytelnianie urządzenia za pomocą certyfikatów X. 509 urzędu certyfikacji](../iot-hub/iot-hub-x509ca-overview.md)
* [Omówienie pojęć związanych z certyfikatami urzędu certyfikacji X. 509 w branży IoT](../iot-hub/iot-hub-x509ca-concept.md)

### <a name="create-the-device-identity-with-x509-self-signed-certificates"></a>Tworzenie tożsamości urządzenia przy użyciu certyfikatów z podpisem własnym X. 509

W przypadku uwierzytelniania z podpisem własnym X. 509, czasami określanego jako Uwierzytelnianie odciskiem palca, należy utworzyć nowe certyfikaty do umieszczenia na urządzeniu IoT. Te certyfikaty mają odcisk palca, które udostępniają IoT Hub na potrzeby uwierzytelniania. 

Najprostszym sposobem na przetestowanie tego scenariusza jest użycie tego samego komputera, który został użyty do utworzenia certyfikatów w programie [Konfigurowanie urządzenia IoT Edge jako niejawnej bramy](how-to-create-transparent-gateway.md). Ten komputer powinien być już skonfigurowany przy użyciu odpowiedniego narzędzia, certyfikatu głównego urzędu certyfikacji i certyfikatu pośredniego urzędu certyfikacji w celu utworzenia certyfikatów urządzeń IoT. Możesz później skopiować certyfikaty końcowe i ich klucze prywatne do urządzenia podrzędnego. Postępując zgodnie z instrukcjami w artykule dotyczącym bramy, skonfigurujesz OpenSSL na maszynie, a następnie Sklonowano repozytorium IoT Edge, aby uzyskać dostęp do skryptów tworzenia certyfikatów. Następnie został utworzony katalog roboczy, który wywoła **\<WRKDIR >** do przechowywania certyfikatów. Domyślne certyfikaty są przeznaczone do programowania i testowania, więc tylko ostatnie 30 dni. Należy utworzyć certyfikat głównego urzędu certyfikacji i certyfikat pośredni. 

1. Przejdź do katalogu roboczego w oknie bash lub PowerShell. 

2. Utwórz dwa certyfikaty (podstawowe i pomocnicze) dla urządzenia podrzędnego. Podaj nazwę urządzenia, a następnie główną lub pomocniczą etykietę. Te informacje służą do nazwy plików, dzięki czemu można śledzić certyfikaty dla wielu urządzeń. 

   ```PowerShell
   New-CACertsDevice "<device name>-primary"
   New-CACertsDevice "<device name>-secondary"
   ```

   ```bash
   ./certGen.sh create_device_certificate "<device name>-primary"
   ./certGen.sh create_device_certificate "<device name>-secondary"
   ```

3. Pobierz odcisk palca SHA1 (nazywany odciskiem palca w interfejsie IoT Hub) z każdego certyfikatu, który jest 40m ciągiem znaków w formacie szesnastkowym. Użyj następującego polecenia OpenSSL, aby wyświetlić certyfikat i znaleźć odcisk palca:

   ```PowerShell/bash
   openssl x509 -in <WORKDIR>/certs/iot-device-<device name>-primary.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

4. Przejdź do centrum IoT Hub w Azure Portal i Utwórz nową tożsamość urządzenia IoT o następujących wartościach: 

   * Wybierz pozycję **X. 509 z podpisem własnym** jako typ uwierzytelniania.
   * Wklej ciągi szesnastkowe skopiowane z podstawowych i pomocniczych certyfikatów urządzenia.
   * Wybierz pozycję **Ustaw urządzenie nadrzędne** i wybierz urządzenie bramy IoT Edge, za pomocą którego będzie nawiązywane połączenie. Urządzenie nadrzędne jest wymagane do uwierzytelniania X. 509 urządzenia podrzędnego. 

   ![Tworzenie identyfikatora urządzenia z uwierzytelnianiem z podpisem własnym X. 509 w portalu](./media/how-to-authenticate-downstream-device/x509-self-signed-portal.png)

5. Skopiuj następujące pliki do dowolnego katalogu na urządzeniu podrzędnym:

   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>*.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>*.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device name>*-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-device-<device name>*.key.pem`

   Te pliki będą odwoływać się do tych plików w aplikacjach urządzeń typu liść, które łączą się z IoT Hub. Do przenoszenia plików certyfikatów można użyć usługi, takiej jak [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) lub funkcja, taka jak [Secure Copy Protocol](https://www.ssh.com/ssh/scp/) .

Możesz użyć [rozszerzenia IoT dla interfejsu wiersza polecenia platformy Azure](https://github.com/Azure/azure-iot-cli-extension) , aby ukończyć tę samą operację tworzenia urządzenia. Poniższy przykład tworzy nowe urządzenie IoT z uwierzytelnianiem z podpisem własnym X. 509 i przypisuje urządzenie nadrzędne: 

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_thumbprint --ptp {primary thumbprint} --stp {secondary thumbprint}
```

Aby uzyskać więcej informacji na temat poleceń interfejsu wiersza polecenia platformy Azure na potrzeby tworzenia urządzeń, generowania certyfikatów i zarządzania nadrzędnego i podrzędnego, zobacz zawartość referencyjną polecenia [AZ IoT Hub Device-Identity](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) .

### <a name="create-the-device-identity-with-x509-ca-signed-certificates"></a>Tworzenie tożsamości urządzenia przy użyciu certyfikatów X. 509 urzędu certyfikacji

W przypadku uwierzytelniania podpisanego za pomocą certyfikatu X. 509 urząd certyfikacji wymagany jest certyfikat głównego urzędu certyfikacji zarejestrowany w IoT Hub używany do podpisywania certyfikatów dla urządzenia IoT. Uwierzytelnianie będzie możliwe przy użyciu dowolnego urządzenia korzystającego z certyfikatu z certyfikatem głównego urzędu certyfikacji lub dowolnego z jego certyfikatów pośrednich. 

Ta sekcja jest oparta na instrukcji przedstawionych w IoT Hub artykule [Konfigurowanie zabezpieczeń X. 509 w usłudze Azure IoT Hub](../iot-hub/iot-hub-security-x509-get-started.md). Postępuj zgodnie z instrukcjami w tej sekcji, aby dowiedzieć się, które wartości należy użyć do skonfigurowania urządzenia podrzędnego, które nawiązuje połączenie za pomocą bramy. 

Najprostszym sposobem na przetestowanie tego scenariusza jest użycie tego samego komputera, który został użyty do utworzenia certyfikatów w programie [Konfigurowanie urządzenia IoT Edge jako niejawnej bramy](how-to-create-transparent-gateway.md). Ten komputer powinien być już skonfigurowany przy użyciu odpowiedniego narzędzia, certyfikatu głównego urzędu certyfikacji i certyfikatu pośredniego urzędu certyfikacji w celu utworzenia certyfikatów urządzeń IoT. Możesz później skopiować certyfikaty końcowe i ich klucze prywatne do urządzenia podrzędnego. Postępując zgodnie z instrukcjami w artykule dotyczącym bramy, skonfigurujesz OpenSSL na maszynie, a następnie Sklonowano repozytorium IoT Edge, aby uzyskać dostęp do skryptów tworzenia certyfikatów. Następnie został utworzony katalog roboczy, który wywoła **\<WRKDIR >** do przechowywania certyfikatów. Domyślne certyfikaty są przeznaczone do programowania i testowania, więc tylko ostatnie 30 dni. Należy utworzyć certyfikat głównego urzędu certyfikacji i certyfikat pośredni. 

1. Postępuj zgodnie z instrukcjami znajdującymi się w sekcji [Rejestrowanie certyfikatów urzędu certyfikacji x. 509 w usłudze IoT Hub](../iot-hub/iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub) tematu *Konfigurowanie zabezpieczeń X. 509 w usłudze Azure IoT Hub*. W tej sekcji wykonaj następujące czynności: 

   1. Przekaż certyfikat głównego urzędu certyfikacji. Jeśli używasz certyfikatów utworzonych w przezroczystym artykule bramy, Przekaż **\<WRKDIR >/certs/Azure-IoT-test-Only.root.ca.CERT.pem** jako plik certyfikatu głównego. 
   2. Sprawdź, czy jesteś własnym certyfikatem głównego urzędu certyfikacji. Można zweryfikować posiadanie narzędzi certyfikatów w \<WRKDIR >. 

      ```powershell
      New-CACertsVerificationCert "<verification code from Azure portal>"
      ```

      ```bash
      ./certGen.sh create_verification_certificate <verification code from Azure portal>"
      ```

2. Postępuj zgodnie z instrukcjami podanymi w sekcji [Tworzenie urządzenia x. 509 dla Centrum IoT](../iot-hub/iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub) *w temacie Konfigurowanie zabezpieczeń X. 509 w usłudze Azure IoT Hub*. W tej sekcji wykonaj następujące czynności: 

   1. Dodaj nowe urządzenie. Podaj nazwę z małymi literami dla **identyfikatora urządzenia**, a następnie wybierz pozycję typ uwierzytelniania **X. 509 podpisany przez urząd certyfikacji**. 
   2. Ustaw urządzenie nadrzędne. W przypadku urządzeń podrzędnych wybierz opcję **Ustaw urządzenie nadrzędne** i wybierz urządzenie bramy IoT Edge, które zapewni połączenie IoT Hub. 

3. Utwórz łańcuch certyfikatów dla urządzenia podrzędnego. Użyj tego samego certyfikatu głównego urzędu certyfikacji, który został przekazany do IoT Hub, aby utworzyć ten łańcuch. Użyj tego samego identyfikatora urządzenia małymi literami, który został przekazany do tożsamości urządzenia w portalu.

   ```powershell
   New-CACertsDevice "<device id>"
   ```

   ```bash
   ./certGen.sh create_device_certificate "<device id>"
   ```

4. Skopiuj następujące pliki do dowolnego katalogu na urządzeniu podrzędnym: 

   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>*.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>*.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device id>*-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-device-<device id>*.key.pem`

   Te pliki będą odwoływać się do tych plików w aplikacjach urządzeń typu liść, które łączą się z IoT Hub. Do przenoszenia plików certyfikatów można użyć usługi, takiej jak [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) lub funkcja, taka jak [Secure Copy Protocol](https://www.ssh.com/ssh/scp/) .

Możesz użyć [rozszerzenia IoT dla interfejsu wiersza polecenia platformy Azure](https://github.com/Azure/azure-iot-cli-extension) , aby ukończyć tę samą operację tworzenia urządzenia. Poniższy przykład tworzy nowe urządzenie IoT przy użyciu podpisanego uwierzytelniania urzędu certyfikacji X. 509 i przypisuje urządzenie nadrzędne: 

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_ca
```

Aby uzyskać więcej informacji na temat poleceń interfejsu wiersza polecenia platformy Azure służących do tworzenia i zarządzania urządzeniami nadrzędnymi/podrzędnymi, zobacz zawartość referencyjną polecenia [AZ IoT Hub Device-Identity](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) .


### <a name="connect-to-iot-hub-through-a-gateway"></a>Nawiązywanie połączenia z IoT Hub za pomocą bramy

Każdy zestaw Azure IoT SDK obsługuje uwierzytelnianie X. 509 nieco inaczej. Jednak ten sam proces służy do uwierzytelniania zwykłych urządzeń IoT w celu IoT Hub z certyfikatami X. 509 również dotyczy urządzeń podrzędnych. Jedyną różnicą jest to, że należy dodać wskaźnik do urządzenia bramy, aby skierować połączenie, lub w scenariuszach w trybie offline, aby obsłużyć uwierzytelnianie w imieniu IoT Hub. Ogólnie rzecz biorąc, można wykonać te same czynności uwierzytelniania X. 509 dla wszystkich urządzeń IoT Hub, a następnie po prostu zastąpić wartość **nazwy hosta** w parametrach połączenia jako nazwę hosta urządzenia bramy. 

W poniższych sekcjach przedstawiono kilka przykładów dla różnych języków zestawu SDK. 

>[!IMPORTANT]
>W poniższych przykładach pokazano, jak zestawy SDK IoT Hub używają certyfikatów do uwierzytelniania urządzeń. W przypadku wdrożenia produkcyjnego należy przechowywać wszystkie klucze tajne, takie jak klucze prywatne lub SAS, w bezpiecznym module sprzętowym (HSM). 

#### <a name="net"></a>.NET

Przykład C# programu uwierzytelniającego do IoT Hub za pomocą certyfikatów X. 509, zobacz [Konfigurowanie zabezpieczeń x. 509 w usłudze Azure IoT Hub](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates). Niektóre z najważniejszych wierszy tego przykładu są zawarte w tym miejscu, aby zademonstrować proces uwierzytelniania.

Podczas deklarowania nazwy hosta dla wystąpienia usługi DeviceClient należy użyć nazwy hosta urządzenia bramy IoT Edge. Nazwę hosta można znaleźć w pliku config. YAML urządzenia bramy. 

W przypadku korzystania z certyfikatów testowych dostarczonych przez repozytorium IoT Edge git klucz certyfikatów to **1234**.

```csharp
try
{
    var cert = new X509Certificate2(@"<absolute-path-to-your-device-pfx-file>", "1234");
    var auth = new DeviceAuthenticationWithX509Certificate("<device-id>", cert);
    var deviceClient = DeviceClient.Create("<gateway hostname>", auth, TransportType.Amqp_Tcp_Only);

    if (deviceClient == null)
    {
        Console.WriteLine("Failed to create DeviceClient!");
    }
    else
    {
        Console.WriteLine("Successfully created DeviceClient!");
        SendEvent(deviceClient).Wait();
    }

    Console.WriteLine("Exiting...\n");
}
catch (Exception ex)
{
    Console.WriteLine("Error in sample: {0}", ex.Message);
}
```

#### <a name="c"></a>C

Przykład programu C uwierzytelniającego do IoT Hub za pomocą certyfikatów X. 509, zobacz przykład [iotedge_downstream_device_sample](https://github.com/Azure/azure-iot-sdk-c/tree/x509_edge_bugbash/iothub_client/samples/iotedge_downstream_device_sample) zestawu SDK usługi c IoT. Niektóre z najważniejszych wierszy tego przykładu są zawarte w tym miejscu, aby zademonstrować proces uwierzytelniania.

Podczas definiowania parametrów połączenia dla urządzenia podrzędnego Użyj nazwy hosta IoT Edge urządzenia bramy dla parametru **hostname** . Nazwę hosta można znaleźć w pliku config. YAML urządzenia bramy. 

```C
// If your downstream device uses X.509 authentication (self signed or X.509 CA) then
// resulting connection string should look like the following:
// "HostName=<gateway device hostname>;DeviceId=<device_id>;x509=true"
static const char* connectionString = "[Downstream device IoT Edge connection string]";

// Path to the Edge "owner" root CA certificate
static const char* edge_ca_cert_path = "[Path to root CA certificate]";

// When the downstream device uses X.509 authentication, a certificate and key 
// in PRM format must be provided.
static const char * x509_device_cert_path = "[Path to primary or secondary device cert]";
static const char * x509_device_key_path = "[Path to primary or secondary device key]";

int main(void)
{
    // Create the iothub handle here
    device_handle = IoTHubDeviceClient_CreateFromConnectionString(connectionString, protocol);

    // Provide the Azure IoT device client with the same root
    // X509 CA certificate that was used to set up the IoT Edge gateway runtime
    if (edge_ca_cert_path != NULL)
    {
        cert_string = obtain_edge_ca_certificate();
        (void)IoTHubDeviceClient_SetOption(device_handle, OPTION_TRUSTED_CERT, cert_string);
    }

    if ((x509_device_cert_path != NULL) && (x509_device_key_path != NULL))
    {
        const char *x509certificate = obtain_file_contents(x509_device_cert_path);
        const char *x509privatekey = obtain_file_contents(x509_device_key_path);
        if ((IoTHubDeviceClient_SetOption(device_handle, OPTION_X509_CERT, x509certificate) != IOTHUB_CLIENT_OK) ||
            (IoTHubDeviceClient_SetOption(device_handle, OPTION_X509_PRIVATE_KEY, x509privatekey) != IOTHUB_CLIENT_OK)
            )
        {
            printf("failure to set options for x509, aborting\r\n");
            exit(1);
        }
    }
}
```

#### <a name="nodejs"></a>Node.js

Przykład programu Node. js uwierzytelniającego do IoT Hub za pomocą certyfikatów X. 509, zobacz przykład [simple_sample_device_x509.](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js) js zestawu SDK środowiska Node. js. Niektóre z najważniejszych wierszy tego przykładu są zawarte w tym miejscu, aby zademonstrować proces uwierzytelniania.

Podczas definiowania parametrów połączenia dla urządzenia podrzędnego Użyj nazwy hosta IoT Edge urządzenia bramy dla parametru **hostname** . Nazwę hosta można znaleźć w pliku config. YAML urządzenia bramy. 

W przypadku korzystania z certyfikatów testowych dostarczonych przez repozytorium IoT Edge git klucz certyfikatów to **1234**.

```node
// String containing Hostname and Device Id in the following format:
//  "HostName=<gateway device hostname>;DeviceId=<device_id>;x509=true"
var connectionString = '<DEVICE CONNECTION STRING WITH x509=true>';
var certFile = '<PATH-TO-CERTIFICATE-FILE>';
var keyFile = '<PATH-TO-KEY-FILE>';
var passphrase = '<KEY PASSPHRASE IF ANY>';

// fromConnectionString must specify a transport constructor, coming from any transport package.
var client = Client.fromConnectionString(connectionString, Protocol);

var options = {
   cert : fs.readFileSync(certFile, 'utf-8').toString(),
   key : fs.readFileSync(keyFile, 'utf-8').toString(),
   passphrase: passphrase
 };

// Calling setOptions with the x509 certificate and key (and optionally, passphrase) will configure the client transport to use x509 when connecting to IoT Hub
client.setOptions(options);
```

#### <a name="python"></a>Python

Zestaw SDK języka Python jest obecnie obsługiwany tylko przy użyciu certyfikatów x509 i kluczy z plików, a nie zdefiniowanych w tekście. W poniższym przykładzie odpowiednie ścieżki są przechowywane w zmiennych środowiskowych.

Podczas definiowania nazwy hosta dla urządzenia podrzędnego Użyj nazwy hosta IoT Edge urządzenia bramy dla parametru **hostname** . Nazwę hosta można znaleźć w pliku config. YAML urządzenia bramy. 

```python
import os
from azure.iot.device import IoTHubDeviceClient, X509

HOSTNAME = "[IoT Edge Gateway Hostname]"
DEVICE_ID = "[Device ID]"

def iothub_client_init():
    x509 = X509(
        cert_file=os.getenv("X509_CERT_FILE"),
        key_file=os.getenv("X509_KEY_FILE")
    )

    client = IoTHubDeviceClient.create_from_x509_certificate(
        x509=x509,
        hostname=HOSTNAME,
        device_id=DEVICE_ID
    )
)

if __name__ == '__main__':
    iothub_client_init()
```

#### <a name="java"></a>Java

Przykład programu Java uwierzytelniającego do IoT Hub za pomocą certyfikatów X. 509, zobacz przykład [SendEventX509. Java](https://github.com/Azure/azure-iot-sdk-python/blob/master/device/samples/iothub_client_sample_x509.py) zestawu SDK usługi Java IoT. Niektóre z najważniejszych wierszy tego przykładu są zawarte w tym miejscu, aby zademonstrować proces uwierzytelniania.

Podczas definiowania parametrów połączenia dla urządzenia podrzędnego Użyj nazwy hosta IoT Edge urządzenia bramy dla parametru **hostname** . Nazwę hosta można znaleźć w pliku config. YAML urządzenia bramy. 

```java
//PEM encoded representation of the public key certificate
private static String publicKeyCertificateString =
    "-----BEGIN CERTIFICATE-----\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "-----END CERTIFICATE-----\n";

//PEM encoded representation of the private key
private static String privateKeyString =
    "-----BEGIN EC PRIVATE KEY-----\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "-----END EC PRIVATE KEY-----\n";

DeviceClient client = new DeviceClient(connectionString, protocol, publicKeyCertificateString, false, privateKeyString, false);
```

## <a name="next-steps"></a>Następne kroki

Wykonanie tego artykułu powinno spowodować, że urządzenie IoT Edge działa jako niewidoczna brama i urządzenie podrzędne zarejestrowane w usłudze IoT Hub. Następnie należy skonfigurować urządzenia podrzędne, aby ufać urządzeniu bramy i wysyłać do niego komunikaty. Aby uzyskać więcej informacji, zobacz [łączenie urządzenia podrzędnego z bramą Azure IoT Edge](how-to-connect-downstream-device.md).
