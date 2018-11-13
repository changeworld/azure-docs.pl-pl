---
title: Zrozumienie certyfikaty usługi Azure IoT Edge | Dokumentacja firmy Microsoft
description: Dowiedz się więcej na temat usługi Azure IoT Edge certyfikatów i sposobie ich używania.
author: stevebus
manager: philmea
ms.author: stevebus
ms.date: 09/13/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 5ff85f2c9ef708c7cbac9be4933541f063a6c95b
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2018
ms.locfileid: "51568594"
---
# <a name="azure-iot-edge-certificate-usage-detail"></a>Szczegóły użycia certyfikatów w usłudze Azure IoT Edge

Usługi IoT Edge certyfikaty są używane do modułów i podrzędne urządzeń IoT do weryfikowania tożsamości i legalności [Centrum usługi Edge](iot-edge-runtime.md#iot-edge-hub) moduł środowiska wykonawczego, w którym jest nawiązywane połączenie. Tych weryfikacji włączenia protokołu TLS (transport layer zabezpieczeń) bezpiecznego połączenia między środowiska uruchomieniowego, moduły i urządzeń IoT. Takich jak usługi IoT Hub sam IoT Edge wymaga bezpiecznego i szyfrowane połączenie z IoT podrzędne (lub liści) urządzeń i moduły usługi IoT Edge. Do nawiązania bezpiecznego połączenia TLS, moduł Centrum usługi Edge przedstawia informacje o serwerze łańcuch certyfikatów do łączenia z klientami w kolejności ich do zweryfikowania jego tożsamości.

Aby usunąć niezgodności dotyczących używania usługi IoT Edge certyfikatów, w tym artykule wyjaśniono, jak certyfikaty usługi IoT Edge mogą współdziałać w scenariuszach na dużą skalę, a także scenariuszy tworzenia i testowania. Skrypty są różne (program Powershell i bash), koncepcje są takie same, między systemami Linux i Windows.

## <a name="iot-edge-certificates"></a>Certyfikaty usługi IoT Edge

W większości przypadków producenci są jednostek niezależnych od użytkowników końcowych z urządzenia usługi Edge. Czasami tylko relacji między producenta i operator jest zakup dostępne na rynku urządzeń brzegowych. W innych sytuacjach producenta pracującego w ramach umowy, tworzenie urządzenia usługi Edge imieniu operatora. Projekt certyfikatu usługi IoT Edge próbuje uwzględnić oba scenariusze.

Na poniższym rysunku przedstawiono użycie usługi IoT Edge certyfikatów. Może to być wartość zero, jeden lub wiele podpisywania certyfikatów pośrednich między certyfikat głównego urzędu certyfikacji i certyfikat urzędu certyfikacji urządzenia. W zależności od liczby jednostek biorących udział w tym miejscu przedstawiono jeden przypadek.

![Diagram przedstawiający relacje typowe certyfikatu](./media/iot-edge-certs/edgeCerts-general.png)

### <a name="certificate-authority"></a>Urząd certyfikacji

Urząd certyfikacji, lub "CA", w skrócie, jest jednostką, która wystawia certyfikaty cyfrowe. Urząd certyfikacji działa jako zaufanych stronę trzecią od właściciela i odbiorca certyfikatu. Cyfrowy certyfikat poświadcza własności przez odbiornik certyfikatu klucza publicznego. Łańcuch zaufania certyfikatów działa przez początkowo wystawienie certyfikatu głównego, który stanowi podstawę zaufania do wszystkich certyfikatów wystawionych przez urząd. Po tym dniu właściciela można użyć certyfikatu głównego do wystawiania certyfikatów pośrednich dodatkowe (certyfikaty "liścia").

### <a name="root-ca-certificate"></a>Certyfikat głównego urzędu certyfikacji

Certyfikat głównego urzędu certyfikacji jest głównym zaufania cały proces. W scenariuszach produkcyjnych to zwykle będzie to certyfikat urzędu certyfikacji zakupionego od zaufanego certyfikatu komercyjnego urzędu takich jak Baltimore Verisign, DigiCert itp. Powinna mieć pełną kontrolę nad urządzeniami nawiązywania połączenia z Twoich urządzeń brzegowych, jest możliwe używanie urzędu certyfikacji poziomu firmowych. W obu przypadkach całego łańcucha certyfikatów z Centrum usługi Edge się rzutuje, więc liścia urządzeń IoT muszą ufać certyfikatowi głównego. Zapisywanie certyfikatu głównego urzędu certyfikacji, albo w magazynie zaufanych głównych certyfikatów urzędu lub podaj katalog główny szczegóły certyfikatu urzędu certyfikacji w kodzie, gdy klient IoT nawiązuje połączenie z usługi IoT Edge.

### <a name="intermediate-certificates"></a>Certyfikaty pośrednie

W procesie produkcyjnym typowe do tworzenia bezpiecznych urządzeń certyfikaty głównego urzędu certyfikacji są rzadko używane bezpośrednio, przede wszystkim ze względu na ryzyko wycieku lub ujawnieniem. Ogólnie rzecz biorąc jeden lub więcej certyfikatów pośrednich urzędów certyfikacji są tworzone i podpisany cyfrowo przez główny urząd certyfikacji. Może istnieć tylko jeden lub może być łańcuch te certyfikaty pośrednie. Scenariusze, które wymagałyby łańcuch certyfikatów pośrednich obejmują:

* Hierarchia działów producenta.

* Wiele firm zajmujących się szeregowo produkcji urządzenia.

* Klient może dostosować zakupem głównego urzędu certyfikacji oraz certyfikat podpisywania dla producenta pochodząca zarejestrować urządzenia dokonają w imieniu klienta.

W każdym przypadku producenta używa certyfikat pośredniego urzędu certyfikacji na końcu tego łańcucha do podpisywania certyfikatu urzędu certyfikacji urządzenia umieszczone na urządzeniu zakończenia. Ogólnie rzecz biorąc te certyfikaty pośrednie są ściśle chronionych w zakładzie produkcyjnym. Przechodzą procesy, zarówno fizyczne, jak i elektroniczne ich użycia.

### <a name="device-ca-certificate"></a>Certyfikat dostępu Warunkowego do urządzeń

Certyfikat urzędu certyfikacji urządzenia jest generowany na podstawie i podpisem ostatecznego certyfikat pośredniego urzędu certyfikacji w procesie. Ten certyfikat jest zainstalowany na urządzeniu usługi Edge, najlepiej z bezpiecznego magazynu, takich jak sprzętowego modułu zabezpieczeń (HSM). Ponadto certyfikat urzędu certyfikacji urządzenia jednoznacznie identyfikuje urządzenia usługi IoT Edge. Dla usługi IoT Edge urządzenia certyfikatu urzędu certyfikacji jest może wystawiać certyfikaty. Na przykład certyfikat urządzeń urzędu certyfikacji wystawia liścia certyfikaty urządzeń, które są używane do uwierzytelniania urządzeń na [usługi Azure IoT Device Provisioning](..\iot-dps\about-iot-dps.md).

### <a name="iot-edge-workload-ca"></a>Obciążenie usługi IoT Edge urzędu certyfikacji

Ten certyfikat, pierwszy na stronie "operator" procesu, jest generowany przez [Menedżera zabezpieczeń usługi IoT Edge](iot-edge-security-manager.md) po pierwszym uruchomieniu usługi IoT Edge. Ten certyfikat jest generowany na podstawie i podpisany przez "certyfikat urzędu certyfikacji urządzenia". Ten certyfikat, który jest po prostu kolejne pośredniego certyfikatu podpisywania, jest używany do generowania i podpisywania innych certyfikatów używane przez środowisko uruchomieniowe usługi IoT Edge. Już dziś, który przede wszystkim certyfikatu serwera Centrum usługi Edge opisanych w poniższej sekcji, ale w przyszłości może zawierać innych certyfikatów do uwierzytelniania składniki usługi IoT Edge.

### <a name="edge-hub-server-certificate"></a>Certyfikat serwera Centrum usługi Edge

Certyfikat serwera Centrum usługi Edge jest rzeczywista certyfikat przedstawiony urządzeniami liścia a modułów na potrzeby weryfikacji tożsamości podczas ustanawiania połączenia TLS między wymaganych przez usługi IoT Edge. Ten certyfikat przedstawia całego łańcucha certyfikatów podpisywania używanych do jego wygenerowania maksymalnie certyfikat głównego urzędu certyfikacji, który musi mieć relację zaufania z urządzenia IoT typu liść. Gdy generowane przez IoT Edge Security Manager, nazwa pospolita (CN), to Centrum usługi Edge certyfikatu został ustawiony na Nazwa podana przez właściwość "hostname" w pliku config.yaml po konwersji na małe litery. Jest to wspólne źródło popełnienia błędu za pomocą usługi IoT Edge.

## <a name="production-implications"></a>Implikacje produkcji

Może być uzasadnione pytanie "Dlaczego usługi IoT Edge wymaga"obciążenia urzędu certyfikacji"dodatkowy certyfikat? Nie można użyć certyfikatu urządzenia urzędu certyfikacji bezpośrednio wygenerować certyfikat serwera Centrum usługi Edge? ". Technicznie rzecz biorąc można. Jednak tego certyfikatu pośredniego "obciążenie" ma na celu oddzielnych uwagi od producenta urządzenia i operator urządzenia. Wyobraź sobie scenariusz, w którym sprzedawane lub przeniesione z jednego klienta do innego urządzenia usługi IoT Edge. Może być pożądane urządzenia certyfikatu urzędu certyfikacji, dostarczone przez producenta być niezmienialne. Jednak specyficzne dla działania urządzenia certyfikatów "obciążenie" powinien być wyczyszczone i utworzone ponownie dla nowego wdrożenia.

Ponieważ procesy producenta i operator są rozdzielone, istnieje kilka konsekwencje, które należy wziąć pod uwagę związane z wdrażanie usługi IoT Edge dla urządzeń w środowisku produkcyjnym.

* Dzięki procesowi opartego na certyfikatach certyfikat głównego urzędu certyfikacji i wszystkich certyfikatów pośrednich urzędów certyfikacji należy chronione i monitorowane przez cały proces wdrażania urządzenia usługi IoT Edge. Producent urządzenia usługi IoT Edge należy dysponować stosowania mocnych procesów dla odpowiedniego magazynu i użycie ich certyfikaty pośrednie. Ponadto certyfikat urzędu certyfikacji urządzenia powinny być przechowywane w jako bezpieczny magazyn możliwie na samym urządzeniu, najlepiej sprzętowego modułu zabezpieczeń.

* Ponieważ certyfikat serwera Centrum usługi Edge, zostanie ona wyświetlona przez Centrum usługi Edge łączącego się urządzenia klienta i moduły, nazwa Pospolita certyfikatu urzędu certyfikacji urządzenia **nie może być** taka sama jak "hostname" używany w config.yaml w usłudze IoT Edge urządzenie. Nazwa używana przez klientów, nawiązać połączenia z usługi IoT Edge (np. za pomocą parametru GatewayHostName parametrów połączenia lub polecenie CONNECT w MQTT) **nie może być** taka sama jak nazwa pospolita używana w certyfikacie urządzeń urzędu certyfikacji. Jest to spowodowane Centrum usługi Edge przedstawia informacje o jego całego łańcucha certyfikatów do weryfikacji przez klientów. Jeśli certyfikat serwera Centrum usługi Edge i certyfikatu urządzenia urzędu certyfikacji, że mają ten sam CN w pętli weryfikacji i spowoduje unieważnienie certyfikatu.

* Ponieważ urządzenia certyfikatu urzędu certyfikacji jest używany przez demona usługi IoT Edge Security w celu wygenerowania ostatecznego certyfikatów usługi IoT Edge, musi sam być certyfikat podpisywania, co oznacza, że ma on możliwości podpisania certyfikatu. Aby to certyfikat podpisywania urzędu certyfikacji, polega na zastosowaniu "V3 podstawowych warunków ograniczających CA:True" do certyfikatu urzędu certyfikacji urządzenie automatycznie skonfiguruje właściwości wymagane użycie klucza.

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
./certGen.sh create_edge_device_certificate "<gateway device name>"
```

```Powershell
New-CACertsEdgeDevice "<gateway device name>"
```

* **\<Nazwy urządzenia bramy\>** przekazywana do tych skryptów **nie** być taka sama jak parametr "Nazwa hosta" w config.yaml. Jak wspomniano wcześniej, skrypty pomóc uniknąć problemów, dodając ciąg ".ca" **\<nazwy urządzenia bramy\>** Aby uniknąć kolizji nazw w przypadku, gdy użytkownik konfiguruje krawędzi przy użyciu tej samej nazwie w obu miejscach jednak jest dobrą praktyką, aby unikać używania tej samej nazwie.

>[!Tip]
> Aby połączyć urządzenia IoT "liścia" urządzeniami i aplikacje, które używają nasz zestaw SDK urządzeń IoT za pomocą usługi IoT Edge, należy dodać opcjonalny parametr GatewayHostName się na końcu parametry połączenia urządzenia. Po wygenerowaniu certyfikatu serwera Centrum usługi Edge jest on oparty na nazwę hosta z config.yaml wersję dolnym — z uwzględnieniem wielkości liter, w związku z tym, nazw do dopasowania i weryfikację certyfikatu TLS powiodło się, które należy wprowadzić parametr GatewayHostName w małymi literami.

## <a name="example-of-iot-edge-certificate-hierarchy"></a>Przykład hierarchii certyfikatów usługi IoT Edge

Aby zilustrować przykładem ścieżka do tego certyfikatu, poniższy zrzut ekranu pochodzi z pracy urządzenia usługi IoT Edge, skonfiguruj rolę przezroczystej bramy. Biblioteki OpenSSL służy do nawiązać połączenie z Centrum usługi Edge, weryfikacji i zrzutu limit certyfikatów.

![Zrzut ekranu przedstawiający hierarchii certyfikatów na każdym poziomie](./media/iot-edge-certs/iotedge-cert-chain.png)

Możesz zobaczyć hierarchię głębokość certyfikatu reprezentowane na zrzucie ekranu:

| Certyfikat głównego urzędu certyfikacji         | Usługa Azure IoT Hub urzędu certyfikacji certyfikatu tylko do testów                                                                           |
|-----------------------------|-----------------------------------------------------------------------------------------------------------|
| Certyfikat pośredniego urzędu certyfikacji | Usługa Azure IoT Hub pośredniego certyfikatu tylko testowanie                                                                 |
| Certyfikat dostępu Warunkowego do urządzeń       | iotgateway.CA ("iotgateway" została przekazana jako < nazwa hosta bramy > do skryptów wygody)      |
| Certyfikat urzędu certyfikacji obciążenia     | iotedge obciążenia z urzędu certyfikacji                                                                                       |
| Certyfikat serwera Centrum usługi Edge | iotedgegw.Local (odpowiada nazwę hosta z config.yaml)                                                |

## <a name="next-steps"></a>Kolejne kroki

[Omówienie modułów usługi Azure IoT Edge](iot-edge-modules.md)

[Konfigurowanie urządzenia usługi IoT Edge, aby pełnić rolę przezroczystej bramy](how-to-create-transparent-gateway.md)