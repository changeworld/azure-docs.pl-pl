---
title: Uwierzytelnij urządzenia podrzędne — Usługa Azure IoT Edge | Dokumenty firmy Microsoft
description: Jak uwierzytelnić urządzenia podrzędne lub urządzenia typu liść do usługi IoT Hub i rozsyłać ich połączenie za pośrednictwem urządzeń bramy usługi Azure IoT Edge.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/13/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: b8db3fedc5886e86d5f49739b87b26535665bdbc
ms.sourcegitcommit: 0553a8b2f255184d544ab231b231f45caf7bbbb0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2020
ms.locfileid: "80389328"
---
# <a name="authenticate-a-downstream-device-to-azure-iot-hub"></a>Uwierzytelnianie urządzenia podrzędnego w usłudze Azure IoT Hub

W scenariuszu bramy przezroczystej urządzenia podrzędne (czasami nazywane urządzeniami typu liść lub urządzenia podrzędne) potrzebują tożsamości w Centrum IoT, jak każde inne urządzenie. W tym artykule przedstawiono opcje uwierzytelniania urządzenia podrzędnego do usługi IoT Hub, a następnie pokazano, jak zadeklarować połączenie bramy.

Istnieją trzy ogólne kroki konfigurowania pomyślnego połączenia bramy przezroczystej. Ten artykuł obejmuje drugi krok:

1. Urządzenie bramy musi mieć możliwość bezpiecznego łączenia się z urządzeniami podrzędnymi, odbierania komunikacji z urządzeń podrzędnych i kierowania wiadomości do właściwego miejsca docelowego. Aby uzyskać więcej informacji, zobacz [Konfigurowanie urządzenia usługi IoT Edge do działania jako przezroczysta brama](how-to-create-transparent-gateway.md).
2. **Urządzenie podrzędne musi mieć tożsamość urządzenia, aby móc uwierzytelniać się za pomocą usługi IoT Hub i wiedzieć, aby komunikować się za pośrednictwem urządzenia bramy.**
3. Urządzenie podrzędne musi bezpiecznie połączyć się z urządzeniem bramy. Aby uzyskać więcej informacji, zobacz [Łączenie urządzenia podrzędnego z bramą usługi Azure IoT Edge](how-to-connect-downstream-device.md).

Urządzenia podrzędne mogą uwierzytelniać się za pomocą usługi IoT Hub przy użyciu jednej z trzech metod: kluczy symetrycznych (czasami nazywanych udostępnionymi kluczami dostępu), certyfikatów z podpisem X.509 lub podpisanych certyfikatów urzędu certyfikacji X.509 .Downstream devices can authenticate with IoT Hub using one of three methods: symetryczny (czasami referred bycony), X.509 self-signed certificates, or X.509 certificate authority (CA) signed certificates. Kroki uwierzytelniania są podobne do kroków używanych do konfigurowania dowolnego urządzenia innego niż IoT-Edge z Centrum IoT, z niewielkimi różnicami w celu zadeklarowania relacji bramy.

Kroki opisane w tym artykule pokazują ręczne inicjowanie obsługi administracyjnej urządzeń, a nie automatyczne inicjowanie obsługi administracyjnej za pomocą usługi inicjowania obsługi administracyjnej usługi azure ioT Hub Device Provisioning Service (DPS). Inicjowanie obsługi administracyjnej urządzeń podrzędnych z DPS nie jest obsługiwane.

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj kroki opisane w [temacie Konfigurowanie urządzenia usługi IoT Edge, aby działało jako przezroczysta brama.](how-to-create-transparent-gateway.md) Jeśli używasz uwierzytelniania X.509 dla urządzenia podrzędnego, musisz użyć tego samego skryptu generującego certyfikat, który został skonfigurowany w artykule bramy przezroczystej.

Ten artykuł odnosi się do *nazwy hosta bramy* w kilku punktach. Nazwa hosta bramy jest zadeklarowana w parametrze **nazwy hosta** pliku config.yaml na urządzeniu bramy usługi IoT Edge. Jest to określone w ciągu połączenia urządzenia podrzędnego. Nazwa hosta bramy musi być rozpoznawana na adres IP przy użyciu dns lub wpisu pliku hosta.

## <a name="register-device-symmetric-key"></a>Zarejestruj urządzenie (klucz symetryczny)

Uwierzytelnianie za pomocą klucza symetrycznego lub uwierzytelnianie klucza dostępu współdzielonego jest najprostszym sposobem uwierzytelniania za pomocą usługi IoT Hub. W połączeniu z uwierzytelnianiem kluczem symetrycznym klucz base64 jest skojarzony z identyfikatorem urządzenia IoT w centrum IoT Hub. Ten klucz należy dołączyć do aplikacji IoT, aby urządzenie można było prezentować po podłączeniu do usługi IoT Hub.

### <a name="create-the-device-identity"></a>Tworzenie tożsamości urządzenia

Dodaj nowe urządzenie IoT w centrum IoT hub, używając witryny Azure portal, interfejsu wiersza polecenia platformy Azure lub rozszerzenia IoT dla programu Visual Studio Code. Należy pamiętać, że urządzenia podrzędne muszą być identyfikowane w Uorie IoT Hub jako zwykłe urządzenia IoT, a nie urządzenia usługi IoT Edge.

Podczas tworzenia nowej tożsamości urządzenia podaj następujące informacje:

* Utwórz identyfikator urządzenia.

* Wybierz **klucz symetryczny** jako typ uwierzytelniania.

* Opcjonalnie wybierz **pozycję Ustaw urządzenie nadrzędne** i wybierz urządzenie bramy usługi IoT Edge, za pomocą którego będzie łączyć się to urządzenie podrzędne. Ten krok jest opcjonalny dla uwierzytelniania za pomocą klucza symetrycznego, ale jest zalecane, ponieważ ustawienie urządzenia nadrzędnego umożliwia [możliwości trybu offline](offline-capabilities.md) dla urządzenia podrzędnego. Zawsze możesz zaktualizować szczegóły urządzenia, aby dodać lub zmienić element nadrzędny później.

   ![Tworzenie identyfikatora urządzenia z symetrycznym kluczem erowym w portalu](./media/how-to-authenticate-downstream-device/symmetric-key-portal.png)

Aby wykonać tę samą operację, można użyć [rozszerzenia IoT dla interfejsu wiersza polecenia platformy Azure.](https://github.com/Azure/azure-iot-cli-extension) Poniższy przykład tworzy nowe urządzenie IoT z uwierzytelnianiem za pomocą klucza symetrycznego i przypisuje urządzenie nadrzędne:

```cli
az iot hub device-identity create -n {iothub name} -d {new device ID} --pd {existing gateway device ID}
```

Aby uzyskać więcej informacji na temat poleceń interfejsu wiersza polecenia platformy Azure do tworzenia urządzeń i zarządzania nadrzędnym/podrzędnym, zobacz zawartość referencyjną dla poleceń [tożsamości urządzenia az iot hub.](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest)


Następnie [pobierz i zmodyfikuj parametry połączenia,](#retrieve-and-modify-connection-string) aby urządzenie wiedziało, aby połączyć się za pośrednictwem bramy.

## <a name="register-device-x509-self-signed"></a>Urządzenie rejestrujące (X.509 z podpisem własnym)

W przypadku uwierzytelniania z podpisem własnym X.509, czasami nazywanego uwierzytelnianiem odcisków palców, należy utworzyć nowe certyfikaty do umieszczenia na urządzeniu IoT. Te certyfikaty mają odcisk palca w nich, które można udostępnić w Centrum IoT do uwierzytelniania.

Jeśli nie masz urzędu certyfikacji do tworzenia certyfikatów X.509, możesz [utworzyć certyfikaty demonstracyjne w celu przetestowania funkcji urządzenia IoT Edge.](how-to-create-test-certificates.md) Podczas generowania certyfikatów testowych dla urządzenia podrzędnego użyj tego samego certyfikatu głównego urzędu certyfikacji, który wygenerował certyfikaty dla urządzenia bramy.

1. Korzystając z certyfikatu urzędu certyfikacji, utwórz dwa certyfikaty urządzeń (podstawowe i pomocnicze) dla urządzenia podrzędnego.

   Certyfikat urządzenia musi mieć ustawioną nazwę podmiotu na identyfikator urządzenia, który będzie używany podczas rejestrowania urządzenia IoT w centrum Azure IoT Hub. To ustawienie jest wymagane do uwierzytelniania.

2. Pobierz odcisk palca SHA1 (nazywany odcisk palca w interfejsie Usługi IoT Hub) z każdego certyfikatu, który jest ciągiem znaków szesnastkowych 40. Użyj następującego polecenia openssl, aby wyświetlić certyfikat i znaleźć odcisk palca:

   ```PowerShell/bash
   openssl x509 -in <primary device certificate>.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

   Uruchom to polecenie dwa razy, raz dla certyfikatu podstawowego i raz dla certyfikatu pomocniczego. Odciski palców dla obu certyfikatów są poświęcene podczas rejestrowania nowego urządzenia IoT przy użyciu certyfikatów X.509 z podpisem własnym.

3. Przejdź do centrum IoT hub w witrynie Azure portal i utwórz nową tożsamość urządzenia IoT z następującymi wartościami:

   * Podaj **identyfikator urządzenia,** który pasuje do nazwy podmiotu certyfikatów urządzenia.
   * Wybierz **X.509 Podpis własny** jako typ uwierzytelniania.
   * Wklej ciągi szesnastkowe skopiowane z certyfikatów podstawowych i pomocniczych urządzenia.
   * Wybierz **pozycję Ustaw urządzenie nadrzędne** i wybierz urządzenie bramy usługi IoT Edge, za pomocą którego będzie łączyć się to urządzenie podrzędne. Urządzenie nadrzędne jest wymagane do uwierzytelniania X.509 urządzenia podrzędnego.

   ![Tworzenie identyfikatora urządzenia z emisą x.509 z podpisem własnym w portalu](./media/how-to-authenticate-downstream-device/x509-self-signed-portal.png)

4. Skopiuj certyfikat urządzenia i klucze do dowolnego miejsca na urządzeniu podrzędnym. Przenieś również kopię udostępnionego certyfikatu głównego urzędu certyfikacji, który wygenerował zarówno certyfikat urządzenia bramy, jak i certyfikaty urządzeń podrzędnych.

   Będziesz odwoływać się do tych plików w aplikacjach urządzenia typu liść, które łączą się z Centrum IoT Hub. Aby przenieść pliki certyfikatów, można użyć usługi takiej jak [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) lub funkcji, takiej jak secure copy [protocol.](https://www.ssh.com/ssh/scp/)

5. W zależności od preferowanego języka przejrzyj przykłady, w jaki sposób można odwoływać się do certyfikatów X.509 w aplikacjach IoT:

   * C#: [Konfigurowanie zabezpieczeń X.509 w centrum Usługi Azure IoT](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)
   * C: [iotedge_downstream_device_sample](https://github.com/Azure/azure-iot-sdk-c/tree/x509_edge_bugbash/iothub_client/samples/iotedge_downstream_device_sample)
   * Node.js: [simple_sample_device_x509.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * Java: [SendEventX509.java](https://github.com/Azure/azure-iot-sdk-python/blob/master/device/samples/iothub_client_sample_x509.py)
   * Python: [send_message_x509.py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/advanced-hub-scenarios/send_message_x509.py)

Rozszerzenie [IoT dla interfejsu wiersza polecenia platformy Azure](https://github.com/Azure/azure-iot-cli-extension) służy do wykonywania tej samej operacji tworzenia urządzenia. Poniższy przykład tworzy nowe urządzenie IoT z uwierzytelnianiem z podpisem własnym X.509 i przypisuje urządzenie nadrzędne:

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_thumbprint --ptp {primary thumbprint} --stp {secondary thumbprint}
```

Aby uzyskać więcej informacji na temat poleceń interfejsu wiersza polecenia platformy Azure do tworzenia urządzeń, generowania certyfikatów oraz zarządzania nadrzędnym i podrzędnym, zobacz zawartość referencyjną dla poleceń [tożsamości urządzenia az iot hub.](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest)

Następnie [pobierz i zmodyfikuj parametry połączenia,](#retrieve-and-modify-connection-string) aby urządzenie wiedziało, aby połączyć się za pośrednictwem bramy.

## <a name="register-device-x509-ca-signed"></a>Zarejestruj urządzenie (podpisane z certyfikatem X.509 CA)

W przypadku uwierzytelniania podpisanego przez urząd certyfikacji X.509 (CA) potrzebny jest certyfikat głównego urzędu certyfikacji zarejestrowany w Centrum IoT Hub, który służy do podpisywania certyfikatów dla urządzenia IoT. Każde urządzenie używające certyfikatu, który został wydany przez główny certyfikat urzędu certyfikacji lub którykolwiek z jego certyfikatów pośrednich, będzie mógł się uwierzytelnić.

Ta sekcja jest oparta na instrukcjach opisanych w artykule Centrum IoT [Konfigurowanie zabezpieczeń X.509 w centrum Azure IoT Hub.](../iot-hub/iot-hub-security-x509-get-started.md) Wykonaj kroki opisane w tej sekcji, aby dowiedzieć się, których wartości użyć do skonfigurowania urządzenia podrzędnego, które łączy się za pośrednictwem bramy.

Jeśli nie masz urzędu certyfikacji do tworzenia certyfikatów X.509, możesz [utworzyć certyfikaty demonstracyjne w celu przetestowania funkcji urządzenia IoT Edge.](how-to-create-test-certificates.md) Podczas generowania certyfikatów testowych dla urządzenia podrzędnego użyj tego samego certyfikatu głównego urzędu certyfikacji, który wygenerował certyfikaty dla urządzenia bramy.

1. Postępuj zgodnie z instrukcjami zawartymi w sekcji [Rejestrowanie certyfikatów urzędu certyfikacji X.509 w sekcji Centrum IoT](../iot-hub/iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub) w konfigurowaniu *zabezpieczeń X.509 w centrum Azure IoT Hub.* W tej sekcji należy wykonać następujące kroki:

   1. Przekazywanie głównego certyfikatu urzędu certyfikacji. Jeśli używasz certyfikatów demonstracyjnych, głównym urzędem ** \<certyfikacji jest ścieżka>/certs/azure-iot-test-only.root.ca.cert.pem**.

   2. Sprawdź, czy jesteś właścicielem tego głównego certyfikatu urzędu certyfikacji.

2. Postępuj zgodnie z instrukcjami zawartymi w sekcji [Tworzenie urządzenia X.509 dla centrum IoT w](../iot-hub/iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub) *witrynie Konfigurowanie zabezpieczeń X.509 w centrum Azure IoT Hub.* W tej sekcji należy wykonać następujące kroki:

   1. Dodaj nowe urządzenie. Podaj nazwę małych liter dla **identyfikatora urządzenia**i wybierz typ uwierzytelnienia **X.509 URZĄD CERTYFIKACJI Podpisany**.
   2. Ustawianie urządzenia nadrzędnego. W przypadku urządzeń podrzędnych wybierz pozycję **Ustaw urządzenie nadrzędne** i wybierz urządzenie bramy usługi IoT Edge, które zapewni połączenie z centrum IoT Hub.

3. Utwórz łańcuch certyfikatów dla urządzenia podrzędnego. Użyj tego samego głównego certyfikatu urzędu certyfikacji, który został przekazany do usługi IoT Hub, aby stworzyć ten łańcuch. Użyj tego samego identyfikatora urządzenia o małych literach, który został nadany tożsamości urządzenia w portalu.

4. Skopiuj certyfikat urządzenia i klucze do dowolnego miejsca na urządzeniu podrzędnym. Przenieś również kopię udostępnionego certyfikatu głównego urzędu certyfikacji, który wygenerował zarówno certyfikat urządzenia bramy, jak i certyfikaty urządzeń podrzędnych.

   Będziesz odwoływać się do tych plików w aplikacjach urządzenia typu liść, które łączą się z Centrum IoT Hub. Aby przenieść pliki certyfikatów, można użyć usługi takiej jak [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) lub funkcji, takiej jak secure copy [protocol.](https://www.ssh.com/ssh/scp/)

5. W zależności od preferowanego języka przejrzyj przykłady, w jaki sposób można odwoływać się do certyfikatów X.509 w aplikacjach IoT:

   * C#: [Konfigurowanie zabezpieczeń X.509 w centrum Usługi Azure IoT](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)
   * C: [iotedge_downstream_device_sample.c](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iotedge_downstream_device_sample)
   * Node.js: [simple_sample_device_x509.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * Java: [SendEventX509.java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/send-event-x509)
   * Python: [send_message_x509.py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/send_message_x509.py)

Rozszerzenie [IoT dla interfejsu wiersza polecenia platformy Azure](https://github.com/Azure/azure-iot-cli-extension) służy do wykonywania tej samej operacji tworzenia urządzenia. Poniższy przykład tworzy nowe urządzenie IoT z uwierzytelnianiem podpisanym przez urząd certyfikacji X.509 i przypisuje urządzenie nadrzędne:

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_ca
```

Aby uzyskać więcej informacji, zobacz zawartość referencyjną interfejsu wiersza polecenia platformy Azure dla poleceń [tożsamości urządzenia az iot hub.](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest)

Następnie [pobierz i zmodyfikuj parametry połączenia,](#retrieve-and-modify-connection-string) aby urządzenie wiedziało, aby połączyć się za pośrednictwem bramy.

## <a name="retrieve-and-modify-connection-string"></a>Pobieranie i modyfikowanie ciągu połączenia

Po utworzeniu tożsamości urządzenia IoT w portalu można pobrać jego klucze podstawowe lub pomocnicze. Jeden z tych kluczy musi być dołączony do ciągu połączenia używanego przez aplikacje do komunikowania się z centrum IoT Hub. W przypadku uwierzytelniania za pomocą klucza symetrycznego usługa IoT Hub udostępnia w pełni uformowany ciąg połączenia w szczegółach urządzenia dla Twojej wygody. Należy dodać dodatkowe informacje o urządzeniu bramy do ciągu połączenia.

Parametry połączenia dla urządzeń podrzędnych wymagają następujących składników:

* Centrum IoT, z które łączy urządzenie:`Hostname={iothub name}.azure-devices.net`
* Identyfikator urządzenia zarejestrowany w koncentratorze:`DeviceID={device ID}`
* Klucz podstawowy lub pomocniczy:`SharedAccessKey={key}`
* Urządzenie bramy, za pośrednictwem których urządzenie łączy się. Podaj wartość **nazwy hosta** z pliku config.yaml urządzenia bramy IoT Edge:`GatewayHostName={gateway hostname}`

Wszystko razem, pełny ciąg połączenia wygląda tak:

```
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz;GatewayHostName=myGatewayDevice
```

Jeśli nawiązano relację nadrzędny/podrzędny dla tego urządzenia podrzędnego, można uprościć parametry połączenia, wywołując bramę bezpośrednio jako host połączenia. Relacje nadrzędne i podrzędne są wymagane dla uwierzytelniania X.509, ale opcjonalne dla uwierzytelniania klucza symetrycznego. Przykład:

```
HostName=myGatewayDevice;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz
```

W tym momencie powinno być zarejestrowane i skonfigurowane jako brama urządzenia usługi IoT Edge. Masz również podrzędne urządzenie IoT zarejestrowane i wskazujące jego urządzenie bramy. Ostatnim krokiem jest umieszczenie certyfikatów na urządzeniu podrzędnym, aby można było bezpiecznie połączyć się z bramą.

Przejdź do następnego artykułu z serii bram, [Połącz urządzenie podrzędne z bramą usługi Azure IoT Edge](how-to-connect-downstream-device.md).

## <a name="next-steps"></a>Następne kroki

Po zakończeniu tego artykułu powinno być urządzenie usługi IoT Edge działające jako przezroczysta brama i urządzenie podrzędne zarejestrowane za pomocą centrum IoT hub. Następnie należy skonfigurować urządzenia podrzędne, aby ufać urządzeniu bramy i bezpiecznie się z nim połączyć. Aby uzyskać więcej informacji, zobacz [Łączenie urządzenia podrzędnego z bramą usługi Azure IoT Edge](how-to-connect-downstream-device.md).
