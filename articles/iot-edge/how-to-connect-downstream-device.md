---
title: Połącz urządzenia podrzędne — usługi Azure IoT Edge | Dokumentacja firmy Microsoft
description: Jak skonfigurować podrzędne lub mają łączyć się urządzenia bramy usługi Azure IoT Edge urządzenia liścia.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/07/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 7a66355ca1a0c9c2c144f04cd944efe22467d3ae
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67058514"
---
# <a name="connect-a-downstream-device-to-an-azure-iot-edge-gateway"></a>Podłącz urządzenie z podrzędnych do bramy usługi Azure IoT Edge

Ten artykuł zawiera instrukcje dotyczące ustanawiania zaufanych połączeń między podrzędnymi urządzeniami i przezroczyste bramy usługi IoT Edge. W scenariuszu przezroczystej bramy co najmniej jedno urządzenie można przekazać swoje wiadomości za pomocą urządzenia jednej bramy, która utrzymuje połączenie z Centrum IoT Hub. Podrzędne urządzenie może pozostawać w dowolnej aplikacji lub platformy, który został utworzony za pomocą tożsamości [usługi Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub) usługi w chmurze. W wielu przypadkach te aplikacje korzystają [zestaw SDK urządzeń Azure IoT](../iot-hub/iot-hub-devguide-sdks.md). Podrzędne urządzenie może być nawet aplikację działającą na samym urządzeniu bramy usługi IoT Edge. 

Istnieją trzy ogólne kroki, aby skonfigurować połączenie pomyślne przezroczystej bramy. W tym artykule opisano trzeci krok:

1. Urządzenie bramy musi bezpieczne łączenie się z urządzeniami podrzędnego otrzymywać informacje z urządzeń podrzędne i kierowanie komunikatów w postaci do odpowiedniego miejsca docelowego. Aby uzyskać więcej informacji, zobacz [Konfigurowanie urządzenia usługi IoT Edge, aby pełnić rolę przezroczystej bramy](how-to-create-transparent-gateway.md).
2. Podrzędne urządzenie wymaga tożsamości urządzenia, aby można było uwierzytelnianie za pomocą usługi IoT Hub i wiedzieć, aby komunikować się za pośrednictwem urządzenia bramy. Aby uzyskać więcej informacji, zobacz [uwierzytelnienia podrzędnym urządzenia do usługi Azure IoT Hub](how-to-authenticate-downstream-device.md).
3. **Podrzędne urządzenie musi mieć możliwość nawiązania bezpiecznego połączenia urządzenia bramy.**

W tym artykule identyfikuje typowe problemy z połączeniami podrzędnym urządzenia i umożliwia realizację procesu konfigurowania urządzenia transmisji przez: 

* Wyjaśnia transport layer security (TLS) i podstawowe informacje dotyczące certyfikatu. 
* Wyjaśniające, jak biblioteki TLS działają w różnych systemach operacyjnych i sposób obsługi każdego systemu operacyjnego za pomocą certyfikatów.
* Przykłady w kilku językach, które pomogą Ci rozpocząć zalet usługi Azure IoT pracę. 

W tym artykule terminy *bramy* i *brama usługi IoT Edge* odnoszą się do urządzenia usługi IoT Edge skonfigurowane jako przezroczystej bramy. 

## <a name="prepare-a-downstream-device"></a>Przygotuj urządzenie podrzędne

Podrzędne urządzenie może pozostawać w dowolnej aplikacji lub platformy, który został utworzony za pomocą tożsamości [usługi Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub) usługi w chmurze. W wielu przypadkach te aplikacje korzystają [zestaw SDK urządzeń Azure IoT](../iot-hub/iot-hub-devguide-sdks.md). Podrzędne urządzenie może być nawet aplikację działającą na samym urządzeniu bramy usługi IoT Edge. 

Aby połączyć podrzędny urządzenia bramy usługi IoT Edge, niezbędne są dwa elementy:

* Urządzenie lub aplikacja, która jest skonfigurowana przy użyciu parametrów połączenia urządzenia usługi IoT Hub dołączane informacje w celu nawiązywania połączenia z bramą. 

    Ten krok jest szczegółowo [uwierzytelnienia podrzędnym urządzenia do usługi Azure IoT Hub](how-to-authenticate-downstream-device.md).

* Urządzenie lub aplikacja musi ufać bramy **główny urząd certyfikacji** certyfikatu do weryfikowania połączeń TLS z urządzeniem bramy. 

    Ten krok jest omówiona szczegółowo w dalszej części tego artykułu. Ten krok może być wykonywane jeden z dwóch sposobów: przez zainstalowanie certyfikatu urzędu certyfikacji w magazynie certyfikatów systemu operacyjnego lub (w przypadku niektórych języków), odwołując się do certyfikatu w aplikacjach za pomocą zestawów SDK IoT Azure.

## <a name="tls-and-certificate-fundamentals"></a>Podstawowe informacje dotyczące protokołów TLS i certyfikatów

Wyzwanie, bezpiecznie nawiązywanie połączeń podrzędnym urządzenia usługi IoT Edge jest podobnie jak inne komunikacji klienta/serwera zabezpieczeń, które odbywa się za pośrednictwem Internetu. Klient i serwer bezpiecznego komunikowania się za pośrednictwem Internetu, za pomocą [Transport layer security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security). TLS został skompilowany przy użyciu standardu [infrastruktury kluczy publicznych (PKI)](https://en.wikipedia.org/wiki/Public_key_infrastructure) konstrukcji o nazwie certyfikatów. Protokół TLS jest dość zaangażowane Specyfikacja i sprostać szeroką gamę tematów związanych z zabezpieczaniem dwa punkty końcowe. Ta sekcja zawiera podsumowanie pojęcia istotne dla Ciebie do bezpiecznego łączenia urządzeń do bramy usługi IoT Edge.

Gdy klient nawiąże połączenie z serwerem, serwer przedstawia łańcucha certyfikatów, o nazwie *łańcuchu certyfikatów serwera*. Łańcuch certyfikatów zwykle składa się z urzędu certyfikacji certyfikat główny, jeden lub więcej certyfikatów pośrednich urzędów certyfikacji, a na końcu sam certyfikat serwera. Klient ustanawia relację zaufania z serwerem, upewniając się, kryptograficznie łańcucha certyfikatów całego serwera. Nazywa się to sprawdzanie poprawności klienta łańcucha certyfikatów serwera *weryfikacji łańcucha serwera*. Klient kryptograficznie wyzwań usługę, aby udowodnić, że posiadaniu klucza prywatnego skojarzonego z certyfikatem serwera w procesie nazywanym *dowód przesyłany*. Kombinacja weryfikacji łańcucha serwera i dowodu posiadania nosi nazwę *uwierzytelniania serwera*. Aby sprawdzić poprawność łańcucha certyfikatów serwera, klient musi kopię certyfikatu głównego urzędu certyfikacji, który został użyty do tworzenia (lub wystawiania) certyfikatu serwera. Zwykle podczas łączenia z witryn sieci Web, przeglądarce ma wstępnie skonfigurowany powszechnie używane certyfikaty urzędu certyfikacji, klient ma bezproblemowe procesu. 

Gdy urządzenie nawiąże połączenie z usługi Azure IoT Hub, urządzenie jest klient i usługa IoT Hub w chmurze jest serwer. Usługa IoT Hub w chmurze jest wspierana przez certyfikat głównego urzędu certyfikacji, nazywany **Baltimore CyberTrust Root**, który jest publicznie dostępny i powszechnie używanych. Ponieważ certyfikat usługi IoT Hub urzędu certyfikacji jest już zainstalowane na większości urządzeń, wiele implementacji protokołu TLS (biblioteki OpenSSL, dostawca Schannel, LibreSSL) automatycznie używać go podczas weryfikacji certyfikatu serwera. Urządzenia, które mogą nawiązywać połączeń z Centrum IoT Hub może mieć problemy, które podejmuje próbę nawiązania połączenia bramy usługi IoT Edge.

Gdy urządzenie nawiąże połączenie z bramy usługi IoT Edge, klient jest podrzędnym urządzenia i urządzenia bramy jest serwerem. Usługa Azure IoT Edge umożliwia operatorów (lub użytkownicy) do tworzenia łańcuchów certyfikatów bramy, jednak mogą korzystać zależnie od potrzeb. Operator może wybrać należy użyć certyfikatu publicznego urzędu certyfikacji, takich jak Baltimore, lub podpisem (lub wewnętrznych) główny certyfikat urzędu certyfikacji. Certyfikatów publicznych urzędów certyfikacji często żadnych opłat związanych z nimi, zwykle są używane w scenariuszach produkcyjnych. Certyfikaty urzędu certyfikacji z podpisem własnym są preferowane dla tworzenia i testowania aplikacji. Artykuły Instalatora przezroczystej bramy, które są wymienione we wstępie używają certyfikatów głównego urzędu certyfikacji z podpisem własnym. 

Korzystając z urzędu certyfikacji certyfikat główny z podpisem własnym w przypadku bramy usługi IoT Edge, musi być zainstalowany na lub udostępniane na wszystkich urządzeniach podrzędnego próby połączenia z bramą. 

![Konfiguracja certyfikatu bramy](./media/how-to-create-transparent-gateway/gateway-setup.png)

Aby dowiedzieć się więcej o certyfikatach usługi IoT Edge i pewne skutki produkcyjnych, zobacz [szczegóły użycia certyfikatu usługi IoT Edge](iot-edge-certs.md).

## <a name="provide-the-root-ca-certificate"></a>Podaj certyfikat głównego urzędu certyfikacji

Aby zweryfikować certyfikaty urządzenia bramy, podrzędne urządzenie wymaga własną kopię certyfikatu głównego urzędu certyfikacji. Jeśli podane w repozytorium git usługi IoT Edge skrypty został użyty do utworzenia Certyfikaty testowe, a następnie certyfikat głównego urzędu certyfikacji jest nazywany **azure-iot-test-only.root.ca.cert.pem**. Jeśli nie jest jeszcze w ramach innych kroków przygotowania podrzędnym urządzenia, należy przenieść ten plik certyfikatu do dowolnego katalogu na urządzeniu z systemem podrzędnego. Można użyć usługi takiej jak [usługi Azure Key Vault](https://docs.microsoft.com/azure/key-vault) lub funkcji, takich jak [kopiowania bezpieczny protokół](https://www.ssh.com/ssh/scp/) można przenieść pliku certyfikatu.

## <a name="install-certificates-in-the-os"></a>Instalowanie certyfikatów w systemie operacyjnym

Instalowanie certyfikatu głównego urzędu certyfikacji w magazynie certyfikatów systemu operacyjnego ogólnie umożliwia większości aplikacji do korzystania z certyfikatu głównego urzędu certyfikacji. Istnieje kilka wyjątków, takich jak NodeJS przechowują aplikacje, które nie używają certyfikatu systemu operacyjnego, ale wolą korzystać z magazynu certyfikatów wewnętrznego środowiska uruchomieniowego węzła. Jeśli nie można zainstalować certyfikat na poziomie systemu operacyjnego, przejdź do sekcji [użycie certyfikatów wraz z zestawami SDK Azure IoT](#use-certificates-with-azure-iot-sdks). 

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

W tej sekcji opisano, jak łączyć z zestawami SDK Azure IoT do urządzenia usługi IoT Edge przy użyciu prostych przykładowych aplikacji. Celem wszystkie próbki jest połączenia klienta urządzenia i wysyłać dane telemetryczne do bramy, a następnie zamknij połączenie i zakończenia. 

Masz dwie rzeczy gotowe przed użyciem przykładów dodatku poziomu aplikacji:

* Parametry połączenia Centrum IoT Hub urządzenia podrzędnego modyfikowane w celu wskazania z urządzeniem bramy, a wszystkie certyfikaty wymagane do uwierzytelnienia podrzędnym urządzenia do usługi IoT Hub. Aby uzyskać więcej informacji, zobacz [uwierzytelnienia podrzędnym urządzenia do usługi Azure IoT Hub](how-to-authenticate-downstream-device.md).

* Pełna ścieżka do certyfikatu głównego urzędu certyfikacji, który możesz skopiowane i zapisane w dowolne miejsce na urządzeniu z systemem podrzędnego.

    Na przykład `<path>/azure-iot-test-only.root.ca.cert.pem`. 

### <a name="nodejs"></a>NodeJS

Ta sekcja zawiera Przykładowa aplikacja do łączenia z klienta usługi Azure IoT NodeJS urządzenia do bramy usługi IoT Edge. W przypadku aplikacji NodeJS należy zainstalować certyfikat głównego urzędu certyfikacji na poziomie aplikacji, jak pokazano poniżej. Aplikacje NodeJS nie należy używać magazynu certyfikatów systemowych. 

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

To przykładowe polecenie, który umożliwia sprawdzenie, czy wszystko zostało skonfigurowane prawidłowo. Wyświetlony komunikat z informacją "zweryfikowano OK".

```cmd/sh
openssl s_client -connect mygateway.contoso.com:8883 -CAfile <CERTDIR>/certs/azure-iot-test-only.root.ca.cert.pem -showcerts
```

## <a name="troubleshoot-the-gateway-connection"></a>Rozwiązywanie problemów z połączeniem bramy

Jeśli urządzenia liścia ma sporadyczne połączenia urządzenia bramy, spróbuj wykonać poniższe czynności, do rozpoznania. 

1. Nazwa hosta bramy w parametrach połączenia jest taka sama jak wartość nazwy hosta w pliku config.yaml usługi IoT Edge na urządzeniu bramy?
2. Nazwa hosta bramy jest rozpoznawana jako adres IP? Sporadyczne połączenia można rozwiązać, używając usługi DNS lub przez dodanie wpisu w pliku hostów na urządzeniu typu liść.
3. Otwartych portów komunikacyjnych w zaporze? Komunikacja oparta na protokół używany (MQTTS:8883 / AMQPS:5671 / HTTPS:433) musi być możliwe między urządzeniem podrzędne i przejrzyste usługi IoT Edge.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak rozszerzyć usługi IoT Edge [możliwości w trybie offline](offline-capabilities.md) urządzeniom podrzędnego. 
