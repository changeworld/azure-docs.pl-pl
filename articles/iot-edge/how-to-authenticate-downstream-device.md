---
title: Uwierzytelnianie urządzeń podrzędne — usługi Azure IoT Edge | Dokumentacja firmy Microsoft
description: Jak uwierzytelnić podrzędnego urządzeń lub urządzeń typu liść do usługi IoT Hub i kierowanie ich połączenie za pośrednictwem urządzenia bramy usługi Azure IoT Edge.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/07/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 5785b0260474bd0eb861236a0bd78066475baacd
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67082393"
---
# <a name="authenticate-a-downstream-device-to-azure-iot-hub"></a>Uwierzytelnienia podrzędnym urządzenia do usługi Azure IoT Hub

W scenariuszu przezroczystej bramy podrzędnym urządzenia (czasami nazywany urządzenia liścia lub podrzędnej) muszą tożsamości w usłudze IoT Hub, podobnie jak inne urządzenia. W tym artykule przedstawiono opcje dla uwierzytelniania podrzędnym urządzenia do usługi IoT Hub, a następnie pokazuje sposób deklarowania połączenia bramy.

Istnieją trzy ogólne kroki, aby skonfigurować połączenie pomyślne przezroczystej bramy. W tym artykule opisano drugi krok:

1. Urządzenie bramy musi być w stanie bezpieczne łączenie się z urządzeniami podrzędnego otrzymywać informacje z urządzeń podrzędne i kierowanie komunikatów w postaci do odpowiedniego miejsca docelowego. Aby uzyskać więcej informacji, zobacz [Konfigurowanie urządzenia usługi IoT Edge, aby pełnić rolę przezroczystej bramy](how-to-create-transparent-gateway.md).
2. **Podrzędne urządzenie musi mieć tożsamości urządzenia, aby można było uwierzytelnianie za pomocą usługi IoT Hub i wiedzieć, aby komunikować się za pośrednictwem urządzenia bramy.**
3. Podrzędne urządzenie musi mieć możliwość nawiązania bezpiecznego połączenia urządzenia bramy. Aby uzyskać więcej informacji, zobacz [połączyć podrzędny urządzenie bramy usługi Azure IoT Edge](how-to-connect-downstream-device.md).

Podrzędne urządzeń mogą uwierzytelniać za pomocą usługi IoT Hub przy użyciu jednej z trzech metod: klucze symetryczne (czasami określane jako klucze dostępu współdzielonego), certyfikaty X.509 z podpisem własnym lub certyfikatu X.509 urzędu certyfikacji podpisanego certyfikaty. Kroki uwierzytelniania są podobne do kroków można skonfigurować wszystkie urządzenia bez IoT-Edge z usługą IoT Hub przy użyciu niewielkie różnice, aby zadeklarować relacji bramy.

Kroki opisane w tym artykule Pokaż urządzenia ręcznego inicjowania obsługi administracyjnej, nie automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure IoT Hub Device Provisioning Service. 

## <a name="symmetric-key-authentication"></a>Uwierzytelnianie za pomocą klucza symetrycznego

Uwierzytelnianie za pomocą klucza symetrycznego lub uwierzytelniania klucza dostępu współdzielonego, jest najprostszym sposobem uwierzytelniania za pomocą usługi IoT Hub. Przy użyciu uwierzytelniania klucza symetrycznego powiązanego z Identyfikatorem urządzenia IoT w usłudze IoT Hub jest kluczem base64. Dodasz tego klucza w aplikacji IoT, aby urządzenie przedstawić go podczas łączenia z usługą IoT Hub. 

### <a name="create-the-device-identity"></a>Tworzenie tożsamości urządzenia 

Dodaj nowe urządzenie IoT w usłudze IoT hub przy użyciu witryny Azure portal, interfejsu wiersza polecenia platformy Azure lub rozszerzenia IoT dla programu Visual Studio Code. Należy pamiętać, że podrzędne urządzenia muszą zostać zidentyfikowany w usłudze IoT Hub jako regularne urządzenia IoT, nie dotyczy urządzeń usługi IoT Edge. 

Kiedy tworzysz nową tożsamość urządzenia, należy podać następujące informacje: 

* Utwórz identyfikator urządzenia.

* Wybierz **klucz symetryczny** jako typ uwierzytelniania. 

* Opcjonalnie można wybrać **Ustaw urządzenia nadrzędnego** i wybierz urządzenie bramy usługi IoT Edge, które to podrzędne urządzenie połączy się za pośrednictwem. Ten krok jest opcjonalny w przypadku uwierzytelniania za pomocą klucza symetrycznego, ale jest zalecane, ponieważ ustawienie umożliwia urządzeniu nadrzędnego [możliwości w trybie offline](offline-capabilities.md) podrzędnym urządzenia. Zawsze możesz zaktualizować szczegóły dotyczące danego urządzenia, aby dodać lub zmienić element nadrzędny później. 

   ![Utwórz identyfikator urządzenia za pomocą symetrycznego klucza uwierzytelniania w portalu](./media/how-to-authenticate-downstream-device/symmetric-key-portal.png)

Możesz użyć [rozszerzenia IoT dla interfejsu wiersza polecenia platformy Azure](https://github.com/Azure/azure-iot-cli-extension) można ukończyć tej samej operacji. Poniższy przykład tworzy nowe urządzenie IoT przy użyciu uwierzytelniania za pomocą klucza symetrycznego i przypisuje urządzenia nadrzędnego: 

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID}
```

Aby uzyskać więcej informacji na temat polecenia wiersza polecenia platformy Azure dla tworzenia urządzenia i zarządzanie elementami nadrzędnymi i podrzędnymi, zobacz zawartość informacyjna dotycząca [az iot hub — tożsamość urządzenia](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) poleceń.

### <a name="connect-to-iot-hub-through-a-gateway"></a>Połącz się z IoT Hub przy użyciu bramy

Ten sam proces jest używany do uwierzytelniania regularne IoT urządzeń w usłudze IoT Hub przy użyciu kluczy symetrycznych dotyczą również urządzeń podrzędnego. Jedyna różnica polega na tym, że musisz dodać wskaźnik do urządzenia bramy, aby skierować połączenie lub w przypadku scenariuszy w trybie offline, aby obsługiwać uwierzytelnianie w imieniu usługi IoT Hub. 

Uwierzytelnianie klucza symetrycznego jest żadne dodatkowe kroki, które należy wykonać na urządzeniu, aby mogła uwierzytelnić za pomocą usługi IoT Hub. Nadal potrzebujesz certyfikaty w miejscu, aby Twoje podrzędne urządzenie może połączyć się jego urządzenie bramy zgodnie z opisem w [połączyć podrzędny urządzenie bramy usługi Azure IoT Edge](how-to-connect-downstream-device.md).

Po utworzeniu tożsamości urządzeń IoT w portalu, możesz pobrać swoje klucze podstawowy lub pomocniczy. Jeden z tych kluczy musi być uwzględniony w parametrach połączenia, które obejmują w dowolnej aplikacji, która komunikuje się z usługą IoT Hub. W przypadku uwierzytelniania klucza symetrycznego IoT Hub udostępnia parametry połączenia w pełni ukształtowane w szczegółowych informacji o urządzeniu dla Twojej wygody. Musisz dodać dodatkowe informacje o urządzeniu bramy do parametrów połączenia. 

Następujące składniki są wymagane parametry połączenia klucza symetrycznego dla podrzędnego urządzeń: 

* Usługa IoT hub, która łączy się z urządzeniem: `Hostname={iothub name}.azure-devices.net`
* Zarejestrowane w Centrum identyfikator urządzenia: `DeviceID={device ID}`
* Podstawowy lub pomocniczy klucza: `SharedAccessKey={key}`
* Urządzenie bramy, które urządzenie nawiązuje połączenie za pośrednictwem. Podaj **hostname** wartości z pliku config.yaml urządzenia bramy usługi IoT Edge: `GatewayHostName={gateway hostname}`

Wszystko ze sobą pełne parametry połączenia wyglądają następująco:

``` 
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz;GatewayHostName=myGatewayDevice
```

Jeśli można ustanowić relacji nadrzędny/podrzędny, dla tego urządzenia podrzędnego, można uprościć parametry połączenia, wywołując bramy bezpośrednio jako host połączenia. Na przykład: 

```
HostName=myGatewayDevice;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz
```

## <a name="x509-authentication"></a>Uwierzytelniania X.509 

Istnieją dwa sposoby uwierzytelniania urządzeń IoT za pomocą certyfikatów X.509. Niezależnie od tego wybierzesz uwierzytelnianie, instrukcje Podłącz urządzenie do Centrum IoT Hub są takie same. Wybierz z podpisem własnym albo podpisanych przez urząd certyfikacji certyfikatów do uwierzytelniania, a następnie Dowiedz się, jak połączyć się z Centrum IoT Hub w dalszym ciągu. 

Aby uzyskać więcej informacji o używaniu uwierzytelniania X.509 w Centrum IoT Hub zobacz następujące artykuły: 
* [Uwierzytelnianie urządzeń przy użyciu certyfikatów X.509 urzędu certyfikacji](../iot-hub/iot-hub-x509ca-overview.md)
* [Pojęć związanych z certyfikatów X.509 urzędu certyfikacji w branży IoT](../iot-hub/iot-hub-x509ca-concept.md)

### <a name="create-the-device-identity-with-x509-self-signed-certificates"></a>Tworzenie tożsamości urządzenia za pomocą certyfikatów X.509 z podpisem własnym

Do uwierzytelniania podpisem własnym X.509, czasami nazywane uwierzytelnianie odciskiem palca musisz utworzyć nowe certyfikaty do umieszczenia na urządzeniu IoT. Te certyfikaty muszą odcisku palca w nich udostępnianymi przez usługę IoT Hub do uwierzytelniania. 

Najprostszym sposobem przetestowania tego scenariusza jest użycie na tym samym komputerze, który został użyty do utworzenia certyfikatów w [Konfigurowanie urządzenia usługi IoT Edge, aby pełnić rolę przezroczystej bramy](how-to-create-transparent-gateway.md). Maszyny powinny już być skonfigurowana za pomocą właściwych narzędzi, certyfikat głównego urzędu certyfikacji i certyfikat pośredniego urzędu certyfikacji do tworzenia certyfikatów urządzeń IoT. Można skopiować końcowej certyfikatów i kluczy prywatnych za pośrednictwem urządzenie podrzędne później. Czynności opisane w artykule na temat bramy możesz skonfigurować openssl na maszynie, a następnie sklonować repozytorium usługi IoT Edge, aby skryptów tworzenia certyfikatu dostępu. Następnie dokonywane katalog roboczy, który nazywamy  **\<WRKDIR >** do przechowywania certyfikatów. Certyfikaty domyślne są przeznaczone do celów tworzenia i testowania, więc tylko ostatnich 30 dni. Należy utworzyć certyfikat głównego urzędu certyfikacji i pośredniego certyfikatu. 

1. Przejdź do katalogu roboczego w powłoki bash lub w oknie programu PowerShell. 

2. Utwórz dwa certyfikaty (podstawowe i pomocnicze) dla podrzędnego urządzenia. Podaj nazwę urządzenia, a następnie etykiety podstawowy lub pomocniczy. Te informacje są używane nazwy plików, aby śledzić certyfikaty dla wielu urządzeń. 

   ```PowerShell
   New-CACertsDevice "<device name>-primary"
   New-CACertsDevice "<device name>-secondary"
   ```

   ```bash
   ./certGen.sh create_device_certificate "<device name>-primary"
   ./certGen.sh create_device_certificate "<device name>-secondary"
   ```

3. Pobierz odcisk palca SHA1 (nazywane odcisku palca w interfejsie usługi IoT Hub) z każdego certyfikatu, który jest ciągiem 40 znaków szesnastkowych. Użyj następującego polecenia biblioteki openssl, aby wyświetlić certyfikat i znaleźć odcisk palca:

   ```PowerShell/bash
   openssl x509 -in <WORKDIR>/certs/iot-device-<device name>-primary.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

4. Przejdź do Centrum IoT hub w witrynie Azure portal i Utwórz nową tożsamość urządzenia IoT z następującymi wartościami: 

   * Wybierz **X.509 z podpisem własnym** jako typ uwierzytelniania.
   * Wklej ciągów szesnastkowych, które został skopiowany z głównego i dodatkowego certyfikatu na urządzeniu.
   * Wybierz **Ustaw urządzenia nadrzędnego** i wybierz urządzenie bramy, które to podrzędne urządzenie połączy się za pośrednictwem usługi IoT Edge. Urządzenie nadrzędne jest wymagany do uwierzytelniania X.509 podrzędnym urządzenia. 

   ![Utwórz identyfikator urządzenia za pomocą uwierzytelniania podpisem własnym X.509 w portalu](./media/how-to-authenticate-downstream-device/x509-self-signed-portal.png)

5. Skopiuj następujące pliki do dowolnego katalogu na urządzeniu z systemem podrzędne:

   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>*.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>*.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device name>*-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-device-<device name>*.key.pem`

   Będziesz odwoływać się do tych plików w aplikacjach urządzenia liścia, łączących się z Centrum IoT Hub. Można użyć usługi takiej jak [usługi Azure Key Vault](https://docs.microsoft.com/azure/key-vault) lub funkcji, takich jak [protoco bezpiecznego kopiowania](https://www.ssh.com/ssh/scp/) umożliwiającego przeniesienie plików certyfikatów.

Możesz użyć [rozszerzenia IoT dla interfejsu wiersza polecenia platformy Azure](https://github.com/Azure/azure-iot-cli-extension) można ukończyć operacji tworzenia tego samego urządzenia. Poniższy przykład tworzy nowego urządzenia IoT z podpisem własnym uwierzytelnianiem X.509 i przypisuje urządzenia nadrzędnego: 

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_thumbprint --ptp {primary thumbprint} --stp {secondary thumbprint}
```

Aby uzyskać więcej informacji na temat polecenia wiersza polecenia platformy Azure dla tworzenia urządzenia, generowania certyfikatów i zarządzanie nadrzędnymi i podrzędnymi, zobacz zawartość informacyjna dotycząca [az iot hub — tożsamość urządzenia](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) poleceń.

### <a name="create-the-device-identity-with-x509-ca-signed-certificates"></a>Utwórz urządzenie tożsamość za pomocą X.509 urzędu certyfikacji podpisanego certyfikaty

Certyfikat X.509 urzędu certyfikacji uwierzytelniania podpisem konieczne jest certyfikat głównego urzędu certyfikacji, rejestrowany w Centrum IoT, używanego do podpisywania certyfikatów dla urządzenia IoT. Dowolne urządzenie, używa certyfikatu, który był problemy według certyfikat głównego urzędu certyfikacji lub dowolnego z jego certyfikaty pośrednie będzie możliwe do uwierzytelniania. 

W tej sekcji jest zgodnie z instrukcjami podanymi w artykule usługi IoT Hub [konfigurowania zabezpieczeń X.509 w usłudze Azure IoT hub](../iot-hub/iot-hub-security-x509-get-started.md). Wykonaj kroki opisane w tej sekcji, aby dowiedzieć się wartości, które będą służy do ustawiania podrzędnym urządzenia, które nawiązuje połączenie za pośrednictwem bramy. 

Najprostszym sposobem przetestowania tego scenariusza jest użycie na tym samym komputerze, który został użyty do utworzenia certyfikatów w [Konfigurowanie urządzenia usługi IoT Edge, aby pełnić rolę przezroczystej bramy](how-to-create-transparent-gateway.md). Maszyny powinny już być skonfigurowana za pomocą właściwych narzędzi, certyfikat głównego urzędu certyfikacji i certyfikat pośredniego urzędu certyfikacji do tworzenia certyfikatów urządzeń IoT. Można skopiować końcowej certyfikatów i kluczy prywatnych za pośrednictwem urządzenie podrzędne później. Czynności opisane w artykule na temat bramy możesz skonfigurować openssl na maszynie, a następnie sklonować repozytorium usługi IoT Edge, aby skryptów tworzenia certyfikatu dostępu. Następnie dokonywane katalog roboczy, który nazywamy  **\<WRKDIR >** do przechowywania certyfikatów. Certyfikaty domyślne są przeznaczone do celów tworzenia i testowania, więc tylko ostatnich 30 dni. Należy utworzyć certyfikat głównego urzędu certyfikacji i pośredniego certyfikatu. 

1. Postępuj zgodnie z instrukcjami w [rejestrowanie certyfikatów X.509 urzędu certyfikacji do usługi IoT hub](../iot-hub/iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub) części *konfigurowania zabezpieczeń X.509 w usłudze Azure IoT hub*. W tej sekcji należy wykonać następujące czynności: 

   1. Przekaż certyfikat głównego urzędu certyfikacji. Jeśli używasz certyfikatów, które zostały utworzone w artykule przezroczystej bramy, Przekaż  **\<WRKDIR > /certs/azure-iot-test-only.root.ca.cert.pem** jako plik certyfikatu głównego. 
   2. Sprawdź, czy jesteś właścicielem tego certyfikatu głównego urzędu certyfikacji. Możesz sprawdzić posiadania za pomocą narzędzi certyfikatu w \<WRKDIR >. 

      ```powershell
      New-CACertsVerificationCert "<verification code from Azure portal>"
      ```

      ```bash
      ./certGen.sh create_verification_certificate <verification code from Azure portal>"
      ```

2. Postępuj zgodnie z instrukcjami w [tworzenie urządzenia X.509 dla usługi IoT hub](../iot-hub/iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub) części *konfigurowania zabezpieczeń X.509 w usłudze Azure IoT hub*. W tej sekcji należy wykonać następujące czynności: 

   1. Dodaj nowe urządzenie. Podaj nazwę małe **identyfikator urządzenia**i wybierz typ uwierzytelniania **podpisany urzędu certyfikacji X.509**. 
   2. Ustaw urządzenia nadrzędnego. Dla urządzeń z podrzędnym, wybierz **Ustaw urządzenia nadrzędnego** i wybierz urządzenie bramy, które będzie dostarczać połączenie do usługi IoT Hub usługi IoT Edge. 

3. Tworzenie łańcucha certyfikatów podrzędnego urządzenia. Użyj tego samego certyfikatu głównego urzędu certyfikacji, który został przekazany do usługi IoT Hub, aby ten łańcuch. Użyj tego samego Identyfikatora urządzenia małe litery, który na Twoją tożsamość urządzenia w portalu.

   ```powershell
   New-CACertsDevice "<device id>"
   ```

   ```bash
   ./certGen.sh create_device_certificate "<device id>"
   ```

4. Skopiuj następujące pliki do dowolnego katalogu na urządzeniu z systemem podrzędne: 

   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>*.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>*.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device id>*-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-device-<device id>*.key.pem`

   Będziesz odwoływać się do tych plików w aplikacjach urządzenia liścia, łączących się z Centrum IoT Hub. Można użyć usługi takiej jak [usługi Azure Key Vault](https://docs.microsoft.com/azure/key-vault) lub funkcji, takich jak [protoco bezpiecznego kopiowania](https://www.ssh.com/ssh/scp/) umożliwiającego przeniesienie plików certyfikatów.

Możesz użyć [rozszerzenia IoT dla interfejsu wiersza polecenia platformy Azure](https://github.com/Azure/azure-iot-cli-extension) można ukończyć operacji tworzenia tego samego urządzenia. Poniższy przykład tworzy nowe urządzenie IoT przy użyciu podpisanego uwierzytelniania X.509 urzędu certyfikacji i przypisuje urządzenia nadrzędnego: 

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_ca
```

Aby uzyskać więcej informacji na temat polecenia wiersza polecenia platformy Azure dla tworzenia urządzenia i zarządzanie elementami nadrzędnymi i podrzędnymi, zobacz zawartość informacyjna dotycząca [az iot hub — tożsamość urządzenia](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) poleceń.


### <a name="connect-to-iot-hub-through-a-gateway"></a>Połącz się z IoT Hub przy użyciu bramy

Każdy zestaw SDK Azure IoT, które w sposób nieco różniący obsługuje uwierzytelnianie X.509. Jednak ten sam proces jest używany do uwierzytelniania regularne IoT urządzeń do usługi IoT Hub za pomocą certyfikatów X.509 dotyczą również urządzeń podrzędnego. Jedyna różnica polega na tym, że musisz dodać wskaźnik do urządzenia bramy, aby skierować połączenie lub w przypadku scenariuszy w trybie offline, aby obsługiwać uwierzytelnianie w imieniu usługi IoT Hub. Ogólnie rzecz biorąc, można wykonać te same czynności uwierzytelniania X.509 dla wszystkich urządzeń usługi IoT Hub, a następnie po prostu zastąpić wartość **Hostname** w parametrach połączenia, jako nazwę hosta urządzenie bramy. 

W poniższych sekcjach przedstawiono kilka przykładów dla różnych języków z zestawem SDK. 

>[!IMPORTANT]
>Poniższe przykłady pokazują, jak IoT Hub SDKs używają certyfikatów do uwierzytelniania urządzeń. W przypadku wdrożenia produkcyjnego należy przechowywać wszystkich wpisów tajnych, takich jak prywatny lub klucze sygnatur dostępu Współdzielonego w module bezpieczny sprzęt (HSM). 

#### <a name="net"></a>.NET

Na przykład C# program uwierzytelniania do usługi IoT Hub za pomocą certyfikatów X.509, zobacz [konfigurowania zabezpieczeń X.509 w usłudze Azure IoT hub](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates). Niektóre z kluczowych wierszy w tej próbce znajdują się tutaj do zademonstrowania procesu uwierzytelniania.

Podczas deklarowania nazwę hosta wystąpienia DeviceClient, użyj nazwy hosta usługi IoT Edge urządzenia bramy. Nazwa hosta można znaleźć w pliku config.yaml urządzenia bramy. 

Jeśli używasz Certyfikaty testowe, dostarczone przez repozytorium git usługi IoT Edge jest kluczem do certyfikatów **1234**.

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

Na przykład C program uwierzytelniania do usługi IoT Hub za pomocą certyfikatów X.509, zobacz C IoT SDK [iotedge_downstream_device_sample](https://github.com/Azure/azure-iot-sdk-c/tree/x509_edge_bugbash/iothub_client/samples/iotedge_downstream_device_sample) próbki. Niektóre z kluczowych wierszy w tej próbce znajdują się tutaj do zademonstrowania procesu uwierzytelniania.

Definiując parametry połączenia dla podrzędnego urządzenia, użyj nazwy hosta usługi IoT Edge urządzenia bramy **HostName** parametru. Nazwa hosta można znaleźć w pliku config.yaml urządzenia bramy. 

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

Na przykład Node.js program uwierzytelniania do usługi IoT Hub za pomocą certyfikatów X.509, zobacz Node.js IoT SDK [simple_sample_device_x509.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js) próbki. Niektóre z kluczowych wierszy w tej próbce znajdują się tutaj do zademonstrowania procesu uwierzytelniania.

Definiując parametry połączenia dla podrzędnego urządzenia, użyj nazwy hosta usługi IoT Edge urządzenia bramy **HostName** parametru. Nazwa hosta można znaleźć w pliku config.yaml urządzenia bramy. 

Jeśli używasz Certyfikaty testowe, dostarczone przez repozytorium git usługi IoT Edge jest kluczem do certyfikatów **1234**.

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

Przykład programu w języku Python uwierzytelniania do usługi IoT Hub za pomocą certyfikatów X.509, zobacz Java IoT SDK [iothub_client_sample_x509.py](https://github.com/Azure/azure-iot-sdk-python/blob/master/device/samples/iothub_client_sample_x509.py) próbki. Niektóre z kluczowych wierszy w tej próbce znajdują się tutaj do zademonstrowania procesu uwierzytelniania.

Definiując parametry połączenia dla podrzędnego urządzenia, użyj nazwy hosta usługi IoT Edge urządzenia bramy **HostName** parametru. Nazwa hosta można znaleźć w pliku config.yaml urządzenia bramy. 

```python
# String containing Hostname, Device Id in the format:
# "HostName=<gateway device hostname>;DeviceId=<device_id>;x509=true"
CONNECTION_STRING = "[Device Connection String]"

X509_CERTIFICATE = (
    "-----BEGIN CERTIFICATE-----""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "...""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "XXXXXXXXXXXX""\n"
    "-----END CERTIFICATE-----"
)

X509_PRIVATEKEY = (
    "-----BEGIN RSA PRIVATE KEY-----""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "...""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
    "-----END RSA PRIVATE KEY-----"
)

def iothub_client_init():
    # prepare iothub client
    client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

    # this brings in x509 privateKey and certificate
    client.set_option("x509certificate", X509_CERTIFICATE)
    client.set_option("x509privatekey", X509_PRIVATEKEY)

    return client
```

#### <a name="java"></a>Java

Przykład programu w języku Java uwierzytelniania do usługi IoT Hub za pomocą certyfikatów X.509, zobacz Java IoT SDK [SendEventX509.java](https://github.com/Azure/azure-iot-sdk-python/blob/master/device/samples/iothub_client_sample_x509.py) próbki. Niektóre z kluczowych wierszy w tej próbce znajdują się tutaj do zademonstrowania procesu uwierzytelniania.

Definiując parametry połączenia dla podrzędnego urządzenia, użyj nazwy hosta usługi IoT Edge urządzenia bramy **HostName** parametru. Nazwa hosta można znaleźć w pliku config.yaml urządzenia bramy. 

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

## <a name="next-steps"></a>Kolejne kroki

Wykonując w tym artykule, powinny mieć urządzenia usługi IoT Edge działa jako przezroczystej bramy i podrzędne urządzenie zarejestrowane w usłudze IoT hub. Następnie należy skonfigurować podrzędne urządzenia, aby ufać urządzenia bramy i wysyłanie komunikatów do niego. Aby uzyskać więcej informacji, zobacz [połączyć podrzędny urządzenie bramy usługi Azure IoT Edge](how-to-connect-downstream-device.md).
