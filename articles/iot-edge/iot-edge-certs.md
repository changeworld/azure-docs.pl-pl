---
title: Certyfikaty dla zabezpieczeń urządzenia — usługi Azure IoT Edge | Dokumentacja firmy Microsoft
description: Usługa Azure IoT Edge używa certyfikatu, aby zweryfikować, urządzeń, moduły i urządzeniami liścia i nawiązywać bezpieczne połączenia między nimi.
author: stevebus
manager: philmea
ms.author: stevebus
ms.date: 09/13/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 0aa70e591c7aac977fe13ed638f8ee56b88e4bd1
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69982909"
---
# <a name="azure-iot-edge-certificate-usage-detail"></a>Szczegóły użycia certyfikatów w usłudze Azure IoT Edge

Certyfikaty IoT Edge są używane w modułach i podrzędnych urządzeniach IoT do weryfikowania tożsamości i poprawności modułu środowiska uruchomieniowego [centrum IoT Edge](iot-edge-runtime.md#iot-edge-hub) , z którym się łączą. Tych weryfikacji włączenia protokołu TLS (transport layer zabezpieczeń) bezpiecznego połączenia między środowiska uruchomieniowego, moduły i urządzeń IoT. Takich jak usługi IoT Hub sam IoT Edge wymaga bezpiecznego i szyfrowane połączenie z IoT podrzędne (lub liści) urządzeń i moduły usługi IoT Edge. Aby nawiązać bezpieczne połączenie TLS, moduł IoT Edge Hub przedstawia łańcuch certyfikatów serwera do łączenia klientów, aby zweryfikować swoją tożsamość.

W tym artykule wyjaśniono, jak IoT Edge certyfikaty mogą funkcjonować w scenariuszach produkcyjnych, deweloperskich i testowych. Skrypty są różne (program Powershell i bash), koncepcje są takie same, między systemami Linux i Windows.

## <a name="iot-edge-certificates"></a>Certyfikaty usługi IoT Edge

Zwykle producenci nie są użytkownikami końcowymi urządzenia IoT Edge. Czasami jedyną relacją między nimi jest czas, w którym użytkownik końcowy lub operator nabywa urządzenie ogólne wykonane przez producenta. W innych przypadkach producent działa w ramach kontraktu, aby zbudować niestandardowe urządzenie w imieniu operatora. Projekt certyfikatu usługi IoT Edge próbuje uwzględnić oba scenariusze.

Na poniższym rysunku przedstawiono użycie usługi IoT Edge certyfikatów. Między certyfikatem głównego urzędu certyfikacji a certyfikatem urzędu certyfikacji urządzenia może istnieć zero, jeden lub wiele pośrednich certyfikatów podpisywania, w zależności od liczby powiązanych jednostek. W tym miejscu pokazujemy jeden przypadek.

![Diagram przedstawiający relacje typowe certyfikatu](./media/iot-edge-certs/edgeCerts-general.png)

### <a name="certificate-authority"></a>Urząd certyfikacji

Urząd certyfikacji, lub "CA", w skrócie, jest jednostką, która wystawia certyfikaty cyfrowe. Urząd certyfikacji działa jako zaufanych stronę trzecią od właściciela i odbiorca certyfikatu. Cyfrowy certyfikat poświadcza własności przez odbiornik certyfikatu klucza publicznego. Łańcuch zaufania certyfikatów działa przez początkowo wystawienie certyfikatu głównego, który stanowi podstawę zaufania do wszystkich certyfikatów wystawionych przez urząd. Po tym dniu właściciela można użyć certyfikatu głównego do wystawiania certyfikatów pośrednich dodatkowe (certyfikaty "liścia").

### <a name="root-ca-certificate"></a>Certyfikat głównego urzędu certyfikacji

Certyfikat głównego urzędu certyfikacji jest głównym zaufania cały proces. W scenariuszach produkcyjnych ten certyfikat urzędu certyfikacji jest zwykle kupowany z zaufanego komercyjnego urzędu certyfikacji, takiego jak Baltimore, VeriSign lub DigiCert. Jeśli masz pełną kontrolę nad urządzeniami łączącymi się z IoT Edge urządzeniami, możliwe jest korzystanie z urzędu certyfikacji na poziomie firmy. W każdym z tych zdarzeń cały łańcuch certyfikatów z Centrum IoT Edge, w którym się znajduje, a więc urządzenia IoT muszą ufać certyfikatowi głównemu. Certyfikat głównego urzędu certyfikacji można przechowywać w magazynie zaufanych głównych urzędów certyfikacji lub podawać szczegóły certyfikatu w kodzie aplikacji.

### <a name="intermediate-certificates"></a>Certyfikaty pośrednie

W procesie produkcyjnym typowe do tworzenia bezpiecznych urządzeń certyfikaty głównego urzędu certyfikacji są rzadko używane bezpośrednio, przede wszystkim ze względu na ryzyko wycieku lub ujawnieniem. Certyfikat głównego urzędu certyfikacji tworzy i podpisuje cyfrowo jeden lub więcej certyfikatów pośrednich urzędów certyfikacji. Może istnieć tylko jeden lub może być łańcuch te certyfikaty pośrednie. Scenariusze, które wymagałyby łańcuch certyfikatów pośrednich obejmują:

* Hierarchia działów producenta.

* Wiele firm zajmujących się szeregowo produkcji urządzenia.

* Klient może dostosować zakupem głównego urzędu certyfikacji oraz certyfikat podpisywania dla producenta pochodząca zarejestrować urządzenia dokonają w imieniu klienta.

W każdym przypadku producenta używa certyfikat pośredniego urzędu certyfikacji na końcu tego łańcucha do podpisywania certyfikatu urzędu certyfikacji urządzenia umieszczone na urządzeniu zakończenia. Ogólnie rzecz biorąc te certyfikaty pośrednie są ściśle chronionych w zakładzie produkcyjnym. Przechodzą procesy, zarówno fizyczne, jak i elektroniczne ich użycia.

### <a name="device-ca-certificate"></a>Certyfikat dostępu Warunkowego do urządzeń

Certyfikat urzędu certyfikacji urządzenia jest generowany na podstawie i podpisem ostatecznego certyfikat pośredniego urzędu certyfikacji w procesie. Ten certyfikat jest instalowany na urządzeniu IoT Edge, najlepiej w bezpiecznym magazynie, takim jak sprzętowy moduł zabezpieczeń (HSM). Ponadto certyfikat urzędu certyfikacji urządzenia jednoznacznie identyfikuje urządzenia usługi IoT Edge. W przypadku IoT Edge certyfikat urzędu certyfikacji urządzenia może wystawić inne certyfikaty. Na przykład certyfikat urzędu certyfikacji urządzenia wystawia certyfikaty urządzeń liściowych, które są używane do uwierzytelniania urządzeń w [usłudze Azure IoT Device](../iot-dps/about-iot-dps.md)Provisioning.

### <a name="iot-edge-workload-ca"></a>Obciążenie usługi IoT Edge urzędu certyfikacji

Program [IoT Edge Security Manager](iot-edge-security-manager.md) generuje certyfikat urzędu certyfikacji obciążenia, pierwszy na stronie "operator" procesu, IoT Edge po pierwszym uruchomieniu. Ten certyfikat jest generowany na podstawie i podpisany przez "certyfikat urzędu certyfikacji urządzenia". Ten certyfikat, który jest po prostu kolejne pośredniego certyfikatu podpisywania, jest używany do generowania i podpisywania innych certyfikatów używane przez środowisko uruchomieniowe usługi IoT Edge. Obecnie jest to przede wszystkim certyfikat serwera IoT Edge Hub omówiony w poniższej sekcji, ale w przyszłości może on obejmować inne certyfikaty służące do uwierzytelniania składników IoT Edge.

### <a name="iot-edge-hub-server-certificate"></a>Certyfikat serwera IoT Edge Hub

Certyfikat serwera Centrum IoT Edge jest rzeczywistym certyfikatem przedstawianym urządzeniom liściowym i modułom w celu weryfikacji tożsamości podczas ustanawiania połączenia TLS wymaganego przez IoT Edge. Ten certyfikat przedstawia całego łańcucha certyfikatów podpisywania używanych do jego wygenerowania maksymalnie certyfikat głównego urzędu certyfikacji, który musi mieć relację zaufania z urządzenia IoT typu liść. Po wygenerowaniu przez program IoT Edge Security Manager, nazwa pospolita (CN) tego certyfikatu Centrum IoT Edge jest ustawiona na Właściwość hostname w pliku config. YAML po konwersji na niższą literę. Jest to wspólne źródło popełnienia błędu za pomocą usługi IoT Edge.

## <a name="production-implications"></a>Implikacje produkcji

Może być uzasadnione pytanie "Dlaczego usługi IoT Edge wymaga"obciążenia urzędu certyfikacji"dodatkowy certyfikat? Nie można użyć certyfikatu urzędu certyfikacji urządzenia do bezpośredniego wygenerowania certyfikatu serwera IoT Edge Hub? ". Technicznie rzecz biorąc można. Jednak tego certyfikatu pośredniego "obciążenie" ma na celu oddzielnych uwagi od producenta urządzenia i operator urządzenia. Wyobraź sobie scenariusz, w którym sprzedawane lub przeniesione z jednego klienta do innego urządzenia usługi IoT Edge. Może być pożądane urządzenia certyfikatu urzędu certyfikacji, dostarczone przez producenta być niezmienialne. Jednak specyficzne dla działania urządzenia certyfikatów "obciążenie" powinien być wyczyszczone i utworzone ponownie dla nowego wdrożenia.

Ponieważ procesy produkcyjne i operacyjne są rozdzielone, podczas przygotowywania urządzeń produkcyjnych należy wziąć pod uwagę następujące konsekwencje:

* Dzięki procesowi opartego na certyfikatach certyfikat głównego urzędu certyfikacji i wszystkich certyfikatów pośrednich urzędów certyfikacji należy chronione i monitorowane przez cały proces wdrażania urządzenia usługi IoT Edge. Producent urządzenia usługi IoT Edge należy dysponować stosowania mocnych procesów dla odpowiedniego magazynu i użycie ich certyfikaty pośrednie. Ponadto certyfikat urzędu certyfikacji urządzenia powinny być przechowywane w jako bezpieczny magazyn możliwie na samym urządzeniu, najlepiej sprzętowego modułu zabezpieczeń.

* Certyfikat serwera Centrum IoT Edge jest przedstawiany przez Centrum IoT Edge do łączenia urządzeń i modułów klienckich. Nazwa pospolita (CN) certyfikatu urzędu certyfikacji urządzenia **nie może być** taka sama jak wartość "hostname", która będzie używana w pliku config. YAML na urządzeniu IoT Edge. Nazwa używana przez klientów do łączenia się z IoT Edge (na przykład za pomocą parametru GatewayHostName parametrów połączenia lub polecenia CONNECT w MQTT) **nie może być** taka sama jak nazwa pospolita użyta w certyfikacie certyfikatu urzędu certyfikacji. To ograniczenie wynika z faktu, że Centrum IoT Edge prezentuje cały łańcuch certyfikatów do weryfikacji przez klientów. Jeśli certyfikat serwera Centrum IoT Edge i certyfikat urzędu certyfikacji urządzenia mają ten sam CN, uzyskasz pętlę weryfikacyjną i unieważnia certyfikat.

* Ponieważ certyfikat urzędu certyfikacji urządzenia jest używany przez demona zabezpieczeń IoT Edge w celu wygenerowania końcowych certyfikatów IoT Edge, musi być to certyfikat podpisywania, co oznacza, że ma ona możliwości podpisywania certyfikatu. Zastosowanie do certyfikatu urzędu certyfikacji "v3 Basic Constraints CA: true" powoduje automatyczne skonfigurowanie wymaganych właściwości użycia klucza.

>[!Tip]
> Jeśli zostały już sklasyfikowane za pośrednictwem ustawień usługi IoT Edge rolę przezroczystej bramy w przypadku tworzenia i testowania za pomocą naszych "wygody skryptów" (patrz następny rozdział) i używać tej samej nazwy hosta, podczas tworzenia certyfikatu urządzenia urzędu certyfikacji, tak jak w przypadku nazwy hosta w config.yaml , możesz się zastanawiać Dlaczego zadziałała. W ramach działań zmierzających do uprościć środowisko dla deweloperów skryptów wygody dołącza ".ca" na końcu nazwę, którą można przekazać do skryptu. Tak, na przykład, jeśli "mygateway" jest używany zarówno nazwę urządzenia w skryptach i nazwy hosta w config.yaml, pierwsza przekonwertowany mygateway.ca przed używany jako nazwa Pospolita certyfikatu urządzenia urzędu certyfikacji.

## <a name="devtest-implications"></a>Tworzenie i testowanie skutków

Aby ułatwić sobie projektowanie i przetestować scenariusze, firma Microsoft udostępnia zestaw [skrypty wygody](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates) generowania nieprodukcyjnych certyfikatów odpowiednich dla usługi IoT Edge w scenariuszu przezroczystej bramy. Przykłady działania skryptów, zobacz [Konfigurowanie urządzenia usługi IoT Edge, aby pełnić rolę przezroczystej bramy](how-to-create-transparent-gateway.md).

Te skrypty generować certyfikaty, które należy wykonać strukturę łańcucha certyfikatów, które są opisane w tym artykule. Następujące polecenia generowania "certyfikat głównego urzędu certyfikacji" i jednym "certyfikat pośredniego urzędu certyfikacji".

```bash
./certGen.sh create_root_and_intermediate 
```

```Powershell
New-CACertsCertChain rsa 
```

Podobnie te polecenia Generuj "Certyfikat urzędu certyfikacji urządzenia".

```bash
./certGen.sh create_edge_device_ca_certificate "<gateway device name>"
```

```Powershell
New-CACertsEdgeDeviceCA "<gateway device name>"
```

* **Nazwaurządzenia\> bramy przeniesiona do tych skryptów nie powinna być taka sama jak parametr "hostname" w pliku config. YAML. \<** Skrypty te pomagają uniknąć wszelkich problemów, dołączając ciąg ". ca" do  **\<\> nazwy urządzenia bramy** , aby zapobiec kolizji nazw na wypadek, gdyby Użytkownik ustawił IoT Edge przy użyciu tej samej nazwy w obu miejscach. Jednak dobrym sposobem jest unikanie używania tej samej nazwy.

>[!Tip]
> Aby połączyć urządzenia IoT "liścia" urządzeniami i aplikacje, które używają nasz zestaw SDK urządzeń IoT za pomocą usługi IoT Edge, należy dodać opcjonalny parametr GatewayHostName się na końcu parametry połączenia urządzenia. Po wygenerowaniu certyfikatu serwera Centrum usługi Edge jest on oparty na nazwę hosta z config.yaml wersję dolnym — z uwzględnieniem wielkości liter, w związku z tym, nazw do dopasowania i weryfikację certyfikatu TLS powiodło się, które należy wprowadzić parametr GatewayHostName w małymi literami.

## <a name="example-of-iot-edge-certificate-hierarchy"></a>Przykład hierarchii certyfikatów usługi IoT Edge

Aby zilustrować przykładem ścieżka do tego certyfikatu, poniższy zrzut ekranu pochodzi z pracy urządzenia usługi IoT Edge, skonfiguruj rolę przezroczystej bramy. OpenSSL jest używany do nawiązywania połączenia z Centrum IoT Edge, weryfikowania i zrzucania certyfikatów.

![Zrzut ekranu przedstawiający hierarchii certyfikatów na każdym poziomie](./media/iot-edge-certs/iotedge-cert-chain.png)

Możesz zobaczyć hierarchię głębokość certyfikatu reprezentowane na zrzucie ekranu:

| Certyfikat głównego urzędu certyfikacji         | Usługa Azure IoT Hub urzędu certyfikacji certyfikatu tylko do testów                                                                           |
|-----------------------------|-----------------------------------------------------------------------------------------------------------|
| Certyfikat pośredniego urzędu certyfikacji | Usługa Azure IoT Hub pośredniego certyfikatu tylko testowanie                                                                 |
| Certyfikat dostępu Warunkowego do urządzeń       | iotgateway.CA ("iotgateway" została przekazana jako < nazwa hosta bramy > do skryptów wygody)      |
| Certyfikat urzędu certyfikacji obciążenia     | iotedge obciążenia z urzędu certyfikacji                                                                                       |
| Certyfikat serwera IoT Edge Hub | iotedgegw.Local (odpowiada nazwę hosta z config.yaml)                                                |

## <a name="next-steps"></a>Kolejne kroki

[Omówienie modułów usługi Azure IoT Edge](iot-edge-modules.md)

[Konfigurowanie urządzenia usługi IoT Edge, aby pełnić rolę przezroczystej bramy](how-to-create-transparent-gateway.md)