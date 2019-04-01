---
title: Połącz urządzenia podrzędne — usługi Azure IoT Edge | Dokumentacja firmy Microsoft
description: Jak skonfigurować podrzędne lub urządzeniami liścia nawiązać połączenie za pośrednictwem urządzenia bramy usługi Azure IoT Edge.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/01/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 5a05b8f0f9484ea49fbfb0bbe8818aa9cd0d66ee
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2019
ms.locfileid: "58757131"
---
# <a name="connect-a-downstream-device-to-an-azure-iot-edge-gateway"></a>Podłącz urządzenie z podrzędnych do bramy usługi Azure IoT Edge

Usługa Azure IoT Edge umożliwia obsługę scenariuszy przezroczystej bramy, w których co najmniej jedno urządzenie można przekazać swoje wiadomości za pomocą urządzenia jednej bramy, która utrzymuje połączenie z Centrum IoT Hub. Po utworzeniu skonfigurowane urządzenie bramy, musisz wiedzieć, jak bezpiecznie łączyć z podrzędnym urządzenia. 

W tym artykule identyfikuje typowe problemy z połączeniami podrzędnym urządzenia i umożliwia realizację procesu konfigurowania urządzenia transmisji przez: 

* Wyjaśnia transport layer security (TLS) i podstawowe informacje dotyczące certyfikatu. 
* Wyjaśniające, jak biblioteki TLS działają w różnych systemach operacyjnych i sposób obsługi każdego systemu operacyjnego za pomocą certyfikatów.
* Przykłady w kilku językach, które pomogą Ci rozpocząć zalet usługi Azure IoT pracę. 

W tym artykule terminy *bramy* i *brama usługi IoT Edge* odnoszą się do urządzenia usługi IoT Edge skonfigurowane jako przezroczystej bramy. 

## <a name="prerequisites"></a>Wymagania wstępne

Przed wykonaniem kroków opisanych w tym artykule, musisz mieć dwa urządzenia gotowe do użycia:

1. Urządzenia usługi IoT Edge, skonfiguruj rolę przezroczystej bramy. 
    [Konfigurowanie urządzenia usługi IoT Edge, aby pełnić rolę przezroczystej bramy](how-to-create-transparent-gateway.md)

    Gdy urządzenie bramy skonfigurowane, skopiuj **azure-iot-test-only.root.ca.cert.pem** certyfikat od bramą i jest dostępna wszędzie na urządzeniu z systemem podrzędnego. 

2. Urządzenie podrzędne, które ma tożsamości urządzenia w usłudze IoT Hub. 
    Nie można użyć urządzenia usługi IoT Edge jako urządzenie podrzędne. Zamiast tego należy użyć urządzenia zarejestrowane jako regularne urządzeń IoT w usłudze IoT Hub. W portalu, możesz zarejestrować nowe urządzenie **urządzeń IoT** sekcji. Lub można użyć wiersza polecenia platformy Azure do [rejestrowanie urządzenia](../iot-hub/quickstart-send-telemetry-c.md#register-a-device). Skopiuj parametry połączenia i jest dostępny do użycia w kolejnych sekcjach. 

    Obecnie tylko podrzędnym urządzenia przy użyciu uwierzytelniania za pomocą klucza symetrycznego można połączyć za pośrednictwem bramy usługi IoT Edge. Urzędy certyfikacji X.509 i certyfikaty X.509 z podpisem własnym nie są obecnie obsługiwane.
    
> [!NOTE]
> "Bramy" używane w tym artykule musi być takie same nazwy jak używana jako nazwa hosta w pliku config.yaml usługi IoT Edge. Nazwa bramy musi być rozpoznawana jako adres IP, albo za pomocą DNS lub wpisu w pliku hostów. Komunikacja oparta na protokół używany (MQTTS:8883 / AMQPS:5671 / HTTPS:433) musi być możliwe między podrzędnymi urządzeniami i transparant usługi IoT Edge. Jeśli Zapora jest między, odpowiedni port musi być otwarty.

## <a name="prepare-a-downstream-device"></a>Przygotuj urządzenie podrzędne

Podrzędne urządzenie może pozostawać w dowolnej aplikacji lub platformy, który został utworzony za pomocą tożsamości [usługi Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub) usługi w chmurze. W wielu przypadkach te aplikacje korzystają [zestaw SDK urządzeń Azure IoT](../iot-hub/iot-hub-devguide-sdks.md). Praktycznego podrzędne urządzenia można nawet aplikację działającą na samym urządzeniu bramy usługi IoT Edge. 

Aby połączyć podrzędny urządzenia bramy usługi IoT Edge, niezbędne są dwa elementy:

1. Urządzenie lub aplikacja, która jest skonfigurowana przy użyciu parametrów połączenia urządzenia usługi IoT Hub dołączane informacje w celu nawiązywania połączenia z bramą. 

    Parametry połączenia są sformatowane następująco: `HostName=yourHub.azure-devices.net;DeviceId=yourDevice;SharedAccessKey=XXXYYYZZZ=;`. Dołącz **GatewayHostName** właściwości z nazwą hosta urządzenia bramy do końca ciągu połączenia. Wartość **GatewayHostName** powinna być zgodna wartość **hostname** w pliku config.yaml urządzenia bramy. 

    Końcowy ciąg wygląda następująco: `HostName=yourHub.azure-devices.net;DeviceId=yourDevice;SharedAccessKey=XXXYYYZZZ=;GatewayHostName=mygateway.contoso.com`.

2. Urządzenie lub aplikacja musi ufać bramy **główny urząd certyfikacji** lub **właściciela urzędu certyfikacji** certyfikatu do weryfikowania połączeń TLS z urządzeniem bramy. 

    W tym kroku bardziej skomplikowane jest omówiona szczegółowo w dalszej części tego artykułu. Ten krok może być wykonywane jeden z dwóch sposobów: przez zainstalowanie certyfikatu urzędu certyfikacji w magazynie certyfikatów systemu operacyjnego lub (w przypadku niektórych języków) odwołuje się do certyfikatu w aplikacjach za pomocą zestawów SDK IoT Azure.

## <a name="tls-and-certificate-fundamentals"></a>Podstawowe informacje dotyczące protokołów TLS i certyfikatów

Wyzwanie, bezpiecznie nawiązywanie połączeń podrzędnym urządzenia usługi IoT Edge jest podobnie jak inne komunikacji klienta/serwera zabezpieczeń, które odbywa się za pośrednictwem Internetu. Klient i serwer bezpiecznego komunikowania się za pośrednictwem Internetu, za pomocą [Transport layer security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security). TLS został skompilowany przy użyciu standardu [infrastruktury kluczy publicznych (PKI)](https://en.wikipedia.org/wiki/Public_key_infrastructure) konstrukcji o nazwie certyfikatów. Protokół TLS jest dość zaangażowane Specyfikacja i sprostać szeroką gamę tematów związanych z zabezpieczaniem dwa punkty końcowe, ale w poniższej sekcji opisano zwięźle, co jest potrzebne do nawiązania bezpiecznego połączenia urządzenia bramy usługi IoT Edge.

Gdy klient nawiąże połączenie z serwerem, serwer przedstawia łańcucha certyfikatów, o nazwie *łańcuchu certyfikatów serwera*. Łańcuch certyfikatów zwykle składa się z urzędu certyfikacji certyfikat główny, jeden lub więcej certyfikatów pośrednich urzędów certyfikacji, a na końcu sam certyfikat serwera. Klient ustanawia relację zaufania z serwerem, upewniając się, kryptograficznie łańcucha certyfikatów całego serwera. Nazywa się to sprawdzanie poprawności klienta łańcucha certyfikatów serwera *uwierzytelniania serwera*. Aby sprawdzić poprawność łańcucha certyfikatów serwera, klient musi kopię certyfikatu głównego urzędu certyfikacji, który został użyty do tworzenia (lub wystawiania) certyfikatu serwera. Zwykle podczas łączenia z witryn sieci Web, przeglądarce ma wstępnie skonfigurowany powszechnie używane certyfikaty urzędu certyfikacji, klient ma bezproblemowe procesu. 

Gdy urządzenie nawiąże połączenie z usługi Azure IoT Hub, urządzenie jest klient i usługa IoT Hub w chmurze jest serwer. Usługa IoT Hub w chmurze jest wspierana przez certyfikat głównego urzędu certyfikacji, nazywany **Baltimore CyberTrust Root**, który jest publicznie dostępny i powszechnie używanych. Ponieważ certyfikat usługi IoT Hub urzędu certyfikacji jest już zainstalowane na większości urządzeń, wiele implementacji protokołu TLS (biblioteki OpenSSL, dostawca Schannel, LibreSSL) automatycznie używać go podczas weryfikacji certyfikatu serwera. Urządzenia, które mogą nawiązywać połączeń z Centrum IoT Hub może mieć problemy, które podejmuje próbę nawiązania połączenia bramy usługi IoT Edge.

Gdy urządzenie nawiąże połączenie z bramy usługi IoT Edge, klient jest podrzędnym urządzenia i urządzenia bramy jest serwerem. Usługa Azure IoT Edge umożliwia operatorów (lub użytkownicy) do tworzenia łańcuchów certyfikatów bramy, jednak mogą korzystać zależnie od potrzeb. Operator może wybrać należy użyć certyfikatu publicznego urzędu certyfikacji, takich jak Baltimore, lub podpisem (lub wewnętrznych) główny certyfikat urzędu certyfikacji. Certyfikatów publicznych urzędów certyfikacji często żadnych opłat związanych z nimi, zwykle są używane w scenariuszach produkcyjnych. Certyfikaty urzędu certyfikacji z podpisem własnym są preferowane dla tworzenia i testowania aplikacji. Artykułach Instalatora przezroczystej bramy, wymienione w sekcji wymagania wstępne jest używane certyfikaty głównego urzędu certyfikacji z podpisem własnym. 

Korzystając z urzędu certyfikacji certyfikat główny z podpisem własnym w przypadku bramy usługi IoT Edge, musi być zainstalowany na lub udostępniane na wszystkich urządzeniach podrzędnego próby połączenia z bramą. 

Aby dowiedzieć się więcej o certyfikatach usługi IoT Edge i pewne skutki produkcyjnych, zobacz [szczegóły użycia certyfikatu usługi IoT Edge](iot-edge-certs.md).

## <a name="install-certificates-using-the-os"></a>Instalowanie certyfikatów przy użyciu systemu operacyjnego

W tym artykule wykorzystano *właściciela urzędu certyfikacji* do odwoływania się do certyfikatu głównego urzędu certyfikacji, ponieważ to termin używany przez skrypty w artykule na temat wymagań wstępnych bramy. 

Instalowanie certyfikatu urzędu certyfikacji właściciela w magazynie certyfikatów systemu operacyjnego ogólnie umożliwia większości aplikacji do korzystania z właścicielem certyfikatu urzędu certyfikacji. Istnieją pewne wyjątki, jak aplikacja NodeJS, która nie używał magazynu certyfikatów systemu operacyjnego, ale wolisz użyć magazynu certyfikatu wewnętrznego środowiska uruchomieniowego węzła. Nie można zainstalować certyfikat na poziomie systemu operacyjnego, można znaleźć przykłady specyficzny dla języka w dalszej części tego artykułu, certyfikat za pomocą zestawu SDK usługi Azure IoT w aplikacjach. 

### <a name="ubuntu"></a>Ubuntu

Następujące polecenia są przykładem zainstalować certyfikat urzędu certyfikacji na hoście systemu Ubuntu. W tym przykładzie przyjęto założenie, że używasz **azure-iot-test-only.root.ca.cert.pem** certyfikat z artykułów wymagań wstępnych i certyfikat zostały skopiowane do lokalizacji na urządzeniu podrzędnego.  

```bash
sudo cp <path>/azure-iot-test-only.root.ca.cert.pem /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
sudo update-ca-certificates
```

Powinien zostać wyświetlony komunikat z informacją, "aktualizowanie certyfikatów w /etc/ssl/certs... Dodano 1, 0 usunięte; Gotowe".

### <a name="windows"></a>Windows

Poniższe kroki są przykładem zainstalować certyfikat urzędu certyfikacji na hoście Windows. W tym przykładzie przyjęto założenie, że używasz **azure-iot-test-only.root.ca.cert.pem** certyfikat z artykułów wymagań wstępnych i certyfikat zostały skopiowane do lokalizacji na urządzeniu podrzędnego.  

1. W Start menu, wyszukaj i wybierz pozycję **zarządzania certyfikatami komputera**. Wywołuje narzędzie **certlm** zostanie otwarty.
2. Przejdź do **certyfikaty — komputer lokalny** > **zaufane główne urzędy certyfikacji**.
3. Kliknij prawym przyciskiem myszy **certyfikaty** i wybierz **wszystkie zadania** > **importu**. Należy uruchomić Kreatora importu certyfikatów. 
4. Postępuj zgodnie z instrukcjami, zgodnie z instrukcją i zaimportuj plik certyfikatu `<path>/azure-iot-test-only.root.ca.cert.pem`. Po zakończeniu wyświetlony komunikat "Pomyślnie zaimportowane". 

Można także zainstalować certyfikaty programowo przy użyciu interfejsów API platformy .NET, jak pokazano w przykładzie .NET w dalszej części tego artykułu. 

Zazwyczaj aplikacje używają Windows, pod warunkiem o nazwie stosu protokołu TLS [Schannel](https://docs.microsoft.com/windows/desktop/com/schannel) nawiązania bezpiecznego połączenia za pośrednictwem protokołu TLS. Dostawca Schannel *wymaga* wszelkich certyfikatów zainstalowania w magazynie certyfikatów Windows przed podjęciem próby nawiązania połączenia TLS.

## <a name="use-certificates-with-azure-iot-sdks"></a>Użycie certyfikatów wraz z zestawami SDK Azure IoT

Ten artykuł odnosi się do certyfikatu głównego urzędu certyfikacji jako *właściciela urzędu certyfikacji* ponieważ to termin używany przez skrypty, które generują certyfikatu z podpisem własnym w artykułach z wymagań wstępnych. 

W tej sekcji opisano, jak łączyć z zestawami SDK Azure IoT do urządzenia usługi IoT Edge przy użyciu prostych przykładowych aplikacji. Celem wszystkie próbki jest połączenia klienta urządzenia i wysyłać dane telemetryczne do bramy, a następnie zamknij połączenie i zakończenia. 

### <a name="common-concepts-across-all-azure-iot-sdks"></a>Typowe pojęcia we wszystkich zestawów SDK usługi Azure IoT

Masz dwie rzeczy gotowe przed użyciem przykładów dodatku poziomu aplikacji:

1. Parametry połączenia Centrum IoT Hub urządzenia podrzędnego modyfikowane w celu wskazania z urządzeniem bramy.

    Parametry połączenia są sformatowane następująco: `HostName=yourHub.azure-devices.net;DeviceId=yourDevice;SharedAccessKey=XXXYYYZZZ=;`. Dołącz **GatewayHostName** właściwości z nazwą hosta urządzenia bramy do końca ciągu połączenia. Wartość **GatewayHostName** powinna być zgodna wartość **hostname** w pliku config.yaml urządzenia bramy. 

    Końcowy ciąg wygląda następująco: `HostName=yourHub.azure-devices.net;DeviceId=yourDevice;SharedAccessKey=XXXYYYZZZ=;GatewayHostName=mygateway.contoso.com`.

2. Pełna ścieżka do certyfikatu głównego urzędu certyfikacji, który możesz skopiowane i zapisane w dowolne miejsce na urządzeniu z systemem podrzędnego.

    Na przykład `<path>/azure-iot-test-only.root.ca.cert.pem`. 

### <a name="nodejs"></a>NodeJS

Ta sekcja zawiera Przykładowa aplikacja do łączenia z klienta usługi Azure IoT NodeJS urządzenia do bramy usługi IoT Edge. W przypadku hostów z systemami Linux i Windows należy zainstalować certyfikat głównego urzędu certyfikacji na poziomie aplikacji jak pokazano poniżej, jak aplikacji NodeJS nie należy używać magazynu certyfikatów systemu. 

1. Pobierz przykład **edge_downstream_device.js** z [zestaw SDK urządzeń Azure IoT dla środowiska Node.js przykłady repozytorium](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples). 
2. Upewnij się, że wszystkie wstępnie wymagane składniki do uruchomienia przykładu, przeglądając **readme.md** pliku. 
3. W pliku edge_downstream_device.js zaktualizuj **connectionString** i **edge_ca_cert_path** zmiennych. 
4. Zapoznaj się z dokumentacją zestawu SDK, aby uzyskać instrukcje dotyczące sposobu uruchamiania przykładu na urządzeniu z systemem. 

Aby poznać przykład, której używasz, poniższy fragment kodu jest jak zestaw SDK klienta odczytuje plik certyfikatu i używa ich do nawiązania bezpiecznego połączenia TLS: 

```javascript
// Provide the Azure IoT device client via setOptions with the X509
// Edge root CA certificate that was used to setup the Edge runtime
var options = {
    ca : fs.readFileSync(edge_ca_cert_path, 'utf-8'),
};
```

### <a name="net"></a>.NET

W tej sekcji przedstawiono przykładową aplikację do łączenia z klienta usługi Azure IoT dla środowiska .NET urządzenia do bramy usługi IoT Edge. Jednak aplikacji platformy .NET mogą automatycznie używać wszystkich zainstalowanych certyfikatów w magazynie certyfikatów systemu na hostach z systemem Linux i Windows.

1. Pobierz przykład **EdgeDownstreamDevice** z [folderu przykładów IoT Edge .NET](https://github.com/Azure/iotedge/tree/master/samples/dotnet/EdgeDownstreamDevice). 
2. Upewnij się, że wszystkie wstępnie wymagane składniki do uruchomienia przykładu, przeglądając **readme.md** pliku. 
3. W **właściwości / launchSettings.json** pliku, zaktualizuj **DEVICE_CONNECTION_STRING** i **CA_CERTIFICATE_PATH** zmiennych. Aby użyć certyfikatu zainstalowanego w magazynie zaufanych certyfikatów w systemie hosta, ta zmienna pozostaw puste. 
4. Zapoznaj się z dokumentacją zestawu SDK, aby uzyskać instrukcje dotyczące sposobu uruchamiania przykładu na urządzeniu z systemem. 

Aby programowo zainstalować zaufanego certyfikatu w magazynie certyfikatów za pomocą aplikacji .NET, zapoznaj się **InstallCACert()** działa w programach **EdgeDownstreamDevice / Program.cs** pliku. Ta operacja jest idempotentna, dzięki czemu można uruchamiać wiele razy przy użyciu tych samych wartości bez dodatkowe zmiany. 

### <a name="c"></a>C

W tej sekcji przedstawiono przykładową aplikację do łączenia z klienta usługi Azure IoT C urządzenia do bramy usługi IoT Edge. Zestaw SDK C mogą pracować z wielu bibliotek TLS, takich jak biblioteki OpenSSL, WolfSSL i Schannel. Aby uzyskać więcej informacji, zobacz [zestawu SDK C usługi IoT Azure](https://github.com/Azure/azure-iot-sdk-c). 

1. Pobierz **iotedge_downstream_device_sample** na [zestaw SDK urządzeń Azure IoT dla przykłady w języku C](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples). 
2. Upewnij się, że wszystkie wstępnie wymagane składniki do uruchomienia przykładu, przeglądając **readme.md** pliku. 
3. W pliku iotedge_downstream_device_sample.c zaktualizuj **connectionString** i **edge_ca_cert_path** zmiennych. 
4. Zapoznaj się z dokumentacją zestawu SDK, aby uzyskać instrukcje dotyczące sposobu uruchamiania przykładu na urządzeniu z systemem. 

Zestaw SDK urządzeń Azure IoT dla języka C udostępnia opcję, aby zarejestrować certyfikat urzędu certyfikacji, podczas konfigurowania klienta. Tej operacji nie można zainstalować certyfikatu w dowolnym miejscu, ale raczej w formacie ciągu certyfikatu w pamięci. Zapisany certyfikat znajduje się do podstawowych stosu protokołu TLS, podczas nawiązywania połączenia. 

```C
(void)IoTHubDeviceClient_SetOption(device_handle, OPTION_TRUSTED_CERT, cert_string);
```

Na hostach Windows Jeśli nie używasz biblioteki OpenSSL lub innej biblioteki TLS, zestaw SDK domyślnie przy użyciu kanału Schannel. Dla dostawcy Schannel do pracy, należy zainstalować certyfikat głównego urzędu certyfikacji usługi IoT Edge w magazynie certyfikatów Windows nie zestawu przy użyciu `IoTHubDeviceClient_SetOption` operacji. 

### <a name="java"></a>Java

W tej sekcji przedstawiono przykładową aplikację do łączenia z klienta urządzenia Java usługi Azure IoT do bramy usługi IoT Edge. 

1. Pobierz przykład **Zdarzenie wysyłania** z [zestaw SDK urządzeń Azure IoT dla przykładów w języku Java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples). 
2. Upewnij się, że wszystkie wstępnie wymagane składniki do uruchomienia przykładu, przeglądając **readme.md** pliku. 
3. Zapoznaj się z dokumentacją zestawu SDK, aby uzyskać instrukcje dotyczące sposobu uruchamiania przykładu na urządzeniu z systemem.

### <a name="python"></a>Python

W tej sekcji przedstawiono przykładową aplikację do łączenia z klienta usługi Azure IoT dla środowiska Python urządzenia do bramy usługi IoT Edge. 

1. Pobierz przykład **edge_downstream_client** z [zestaw SDK urządzeń Azure IoT dla przykłady w języku Python](https://github.com/Azure/azure-iot-sdk-python/tree/master/device/samples). 
2. Upewnij się, że wszystkie wstępnie wymagane składniki do uruchomienia przykładu, przeglądając **readme.md** pliku. 
3. W pliku edge_downstream_client.py zaktualizuj **parametry_połączenia** i **TRUSTED_ROOT_CA_CERTIFICATE_PATH** zmiennych. 
4. Zapoznaj się z dokumentacją zestawu SDK, aby uzyskać instrukcje dotyczące sposobu uruchamiania przykładu na urządzeniu z systemem. 


## <a name="test-the-gateway-connection"></a>Testuj połączenie bramy

To przykładowe polecenie, które testy, które wszystko, co zostało skonfiguruj poprawnie. Wyświetlony komunikat z informacją "zweryfikowano OK".

```cmd/sh
openssl s_client -connect mygateway.contoso.com:8883 -CAfile <CERTDIR>/certs/azure-iot-test-only.root.ca.cert.pem -showcerts
```

## <a name="troubleshoot-the-gateway-connection"></a>Rozwiązywanie problemów z połączeniem bramy

Jeśli urządzenia liścia ma sporadyczne połączenia urządzenia bramy, spróbuj wykonać poniższe czynności, do rozpoznania. 

1. Czy nazwa bramy, dołączony do połączeń ciągu taka sama jak nazwa hosta w pliku config.yaml usługi IoT Edge na urządzeniu bramy?
2. Nazwa bramy jest rozpoznawana jako adres IP? Połączenia intenmittent można rozwiązać za pomocą DNS lub przez dodanie wpisu w pliku hostów na urządzeniu typu liść.
3. Otwartych portów komunikacyjnych w zaporze? Komunikacja oparta na protokół używany (MQTTS:8883 / AMQPS:5671 / HTTPS:433) musi być możliwe między podrzędnymi urządzeniami i transparant usługi IoT Edge.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak rozszerzyć usługi IoT Edge [możliwości w trybie offline](offline-capabilities.md) urządzeniom podrzędnego. 
