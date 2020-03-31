---
title: Usługa inicjowania obsługi administracyjnej urządzeń usługi IoT Hub — pojęcia autoobsługiwania obsługi administracyjnej
description: Ten artykuł zawiera omówienie koncepcyjne fazy automatycznego inicjowania obsługi administracyjnej urządzeń przy użyciu usługi inicjowania obsługi administracyjnej urządzeń IoT (DPS), Centrum IoT hub i sdk klientów.
author: wesmc7777
ms.author: wesmc
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: c94fa6b851dfc9923628a738a15f7c245204f73f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975333"
---
# <a name="auto-provisioning-concepts"></a>Pojęcia automatycznego inicjowania obsługi administracyjnej

Jak opisano w [przeglądzie,](about-iot-dps.md)usługa inicjowania obsługi urządzeń jest usługą pomocniczą, która umożliwia aprowizacji urządzeń na czas do centrum IoT hub, bez konieczności interwencji człowieka. Po pomyślnym inicjowaniu obsługi administracyjnej urządzenia łączą się bezpośrednio z wyznaczonym centrum IoT Hub. Ten proces jest określany jako automatyczna inicjowanie obsługi administracyjnej i zapewnia gotowe środowisko rejestracji i konfiguracji początkowej dla urządzeń.

## <a name="overview"></a>Omówienie

Automatyczne inicjowanie obsługi administracyjnej usługi Azure IoT można podzielić na trzy fazy:

1. **Konfiguracja usługi** — jednorazowa konfiguracja wystąpień usługi inicjowania obsługi administracyjnej usługi Azure IoT Hub i usługi inicjowania obsługi urządzeń usługi, ustanawianie ich i tworzenie powiązania między nimi.

   > [!NOTE]
   > Niezależnie od wielkości rozwiązania IoT, nawet jeśli planujesz obsługiwać miliony urządzeń, jest to **konfiguracja jednorazowa.**

2. **Rejestracja urządzenia** — proces uświadamiania wystąpienia usługi inicjowania obsługi administracyjnej urządzeń urządzeń, które będą próbowały zarejestrować się w przyszłości. [Rejestracja](concepts-service.md#enrollment) odbywa się przez skonfigurowanie informacji o tożsamości urządzenia w usłudze inicjowania obsługi administracyjnej, jako "rejestracja indywidualna" dla jednego urządzenia lub "rejestracja grupowa" dla wielu urządzeń. Tożsamość opiera się na [mechanizmie zaświadczania,](concepts-security.md#attestation-mechanism) z którego urządzenie jest przeznaczone do użytku, co pozwala usłudze aprowizacji potwierdzić autentyczność urządzenia podczas rejestracji:

   - **Moduł TPM**: skonfigurowany jako "rejestracja indywidualna", tożsamość urządzenia jest oparta na identyfikatorze rejestracji modułu TPM i publicznym kluczu poręczenia. Biorąc pod uwagę, że moduł TPM jest [specyfikacją,](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/)usługa oczekuje tylko, że będzie testować według specyfikacji, niezależnie od implementacji modułu TPM (sprzętu lub oprogramowania). Zobacz [inicjowanie obsługi administracyjnej urządzeń: posuwanie tożsamości z modułem TPM,](https://azure.microsoft.com/blog/device-provisioning-identity-attestation-with-tpm/) aby uzyskać szczegółowe informacje na temat zaświadczania opartego na programie TPM. 

   - **X509**: skonfigurowany jako "rejestracja indywidualna" lub "rejestracja grupowa", tożsamość urządzenia jest oparta na certyfikatie cyfrowym X.509, który jest przekazytywał do rejestracji jako plik pem lub cer.

   > [!IMPORTANT]  
   > Chociaż nie jest to warunek wstępny korzystania z usług inicjowania obsługi administracyjnej urządzeń, zdecydowanie zaleca się, aby urządzenie używało sprzętowego modułu zabezpieczeń (HSM) do przechowywania poufnych informacji o tożsamości urządzenia, takich jak klucze i certyfikaty X.509.

3. **Rejestracja i konfiguracja urządzenia** — inicjowane po uruchomieniu przez oprogramowanie rejestracyjne, które jest budowane przy użyciu sDK klienta usługi inicjowania obsługi urządzeń odpowiedniego dla urządzenia i mechanizmu zaświadczania. Oprogramowanie ustanawia połączenie z usługą inicjowania obsługi administracyjnej w celu uwierzytelnienia urządzenia i późniejszej rejestracji w Centrum IoT Hub. Po pomyślnej rejestracji urządzenie jest dostarczane z unikatowym identyfikatorem urządzenia usługi IoT Hub i informacjami o połączeniu, co pozwala na pobranie początkowej konfiguracji i rozpoczęcie procesu telemetrii. W środowiskach produkcyjnych ta faza może wystąpić kilka tygodni lub miesięcy po poprzednich dwóch fazach.

## <a name="roles-and-operations"></a>Role i operacje

Fazy omówione w poprzedniej sekcji mogą obejmować tygodnie lub miesiące, ze względu na realia produkcyjne, takie jak czas produkcji, wysyłka, proces celny itp. Ponadto mogą one obejmować działania w wielu rolach, biorąc pod uwagę różne zaangażowane jednostki. W tej sekcji ma głębsze spojrzenie na różne role i operacje związane z każdej fazy, a następnie ilustruje przepływ w diagramie sekwencji. 

Automatyczne inicjowanie obsługi administracyjnej umieszcza również wymagania dla producenta urządzenia, specyficzne dla włączenia mechanizmu zaświadczania. Operacje produkcyjne mogą również odbywać się niezależnie od czasu faz automatycznego inicjowania obsługi administracyjnej, szczególnie w przypadkach, gdy nowe urządzenia są zamawiane po automatycznym inicjowaniu aprowizacji.

Seria przewodników Szybki start znajduje się w spisie treści po lewej stronie, aby ułatwić automatyczne inicjowanie obsługi administracyjnej za pomocą praktycznego środowiska. W celu ułatwienia/uproszczenia procesu uczenia się oprogramowanie służy do symulacji fizycznego urządzenia do rejestracji i rejestracji. Niektóre przewodniki Szybki start wymagają wykonania operacji dla wielu ról, w tym operacji dla nieistniejących ról, ze względu na symulowany charakter przewodnika Szybki start.

| Rola | Operacja | Opis |
|------| --------- | ------------|
| Producent | Kodowanie adresu URL tożsamości i rejestracji | Na podstawie zastosowanego mechanizmu zaświadczania producent jest odpowiedzialny za kodowanie informacji o tożsamości urządzenia i adresu URL rejestracji usługi inicjowania obsługi administracyjnej urządzenia.<br><br>**Szybkie starty:** ponieważ urządzenie jest symulowane, nie ma roli producenta. Zobacz rolę dewelopera, aby uzyskać szczegółowe informacje na temat sposobu uzyskania tych informacji, który jest używany do kodowania przykładowej aplikacji rejestracji. |
| | Podaj tożsamość urządzenia | Jako inicjator informacji o tożsamości urządzenia producent jest odpowiedzialny za przekazanie ich operatorowi (lub wyznaczonemu agentowi) lub bezpośrednie zarejestrowanie go w usłudze inicjowania obsługi administracyjnej urządzeń za pośrednictwem interfejsów API.<br><br>**Szybkie starty:** ponieważ urządzenie jest symulowane, nie ma roli producenta. Zobacz rolę Operatora, aby uzyskać szczegółowe informacje na temat sposobu uzyskania tożsamości urządzenia, który jest używany do rejestrowania symulowanego urządzenia w wystąpieniu usługi inicjowania obsługi administracyjnej urządzenia. |
| Operator | Konfigurowanie automatycznego inicjowania obsługi administracyjnej | Ta operacja odpowiada pierwszej fazie automatycznego inicjowania obsługi administracyjnej.<br><br>**Szybki start:** Pełnisz rolę Operator, konfigurując usługi inicjowania obsługi urządzeń i wystąpienia usługi IoT Hub w ramach subskrypcji platformy Azure. |
|  | Rejestrowanie tożsamości urządzenia | Ta operacja odpowiada drugiej fazie automatycznego inicjowania obsługi administracyjnej.<br><br>**Szybki start:** pełnisz rolę Operator, rejestrując symulowane urządzenie w wystąpieniu usługi inicjowania obsługi administracyjnej urządzeń. Tożsamość urządzenia jest określana przez metodę zaświadczania symulowanego w przewodniku Szybki start (TPM lub X.509). Zobacz rolę dewelopera, aby uzyskać szczegółowe informacje na temat zaświadczenia. |
| Usługa inicjowania obsługi urządzeń,<br>Usługa IoT Hub | \<wszystkie operacje\> | W przypadku implementacji produkcyjnej z urządzeniami fizycznymi i przewodników Szybki start z symulowanych urządzeń te role są spełniane za pośrednictwem usług IoT skonfigurowanych w ramach subskrypcji platformy Azure. Role/operacje działają dokładnie tak samo, jak usługi IoT są obojętne na inicjowanie obsługi administracyjnej urządzeń fizycznych i symulowanych. |
| Developer | Tworzenie/wdrażanie oprogramowania rejestracyjnego | Ta operacja odpowiada trzeciej fazie automatycznego inicjowania obsługi administracyjnej. Deweloper jest odpowiedzialny za tworzenie i wdrażanie oprogramowania rejestracyjnego na urządzeniu przy użyciu odpowiedniego zestawu SDK.<br><br>**Szybki start:** Przykładowa aplikacja rejestracji, którą tworzysz, symuluje rzeczywiste urządzenie dla wybranej platformy/języka, które działa na stacji roboczej (zamiast wdrażać ją na urządzeniu fizycznym). Aplikacja rejestracji wykonuje te same operacje, co ta wdrożona na urządzeniu fizycznym. Należy określić metodę zaświadczania (certyfikat TPM lub X.509) oraz adres URL rejestracji i "Zakres identyfikatorów" wystąpienia usługi inicjowania obsługi administracyjnej urządzenia. Tożsamość urządzenia jest określana przez logikę zaświadczania SDK w czasie wykonywania, na podstawie określonej metody: <ul><li>**TPM atest -** twoja stacja robocza rozwoju uruchamia [aplikację symulatora modułu TPM](how-to-use-sdk-tools.md#trusted-platform-module-tpm-simulator). Po uruchomieniu oddzielna aplikacja jest używana do wyodrębniania modułu TPM "Klucz poręczenia" i "Identyfikator rejestracji" do użycia w rejestrowaniu tożsamości urządzenia. Logika zaświadczania SDK używa również symulatora podczas rejestracji, aby przedstawić podpisany token Sygnatura dostępu Współdzielonego do weryfikacji uwierzytelniania i rejestracji.</li><li>**X509 zaświadczenie** - używasz narzędzia do [generowania certyfikatu](how-to-use-sdk-tools.md#x509-certificate-generator). Po wygenerowaniu plik certyfikatu jest wymagany do użycia podczas rejestracji. Logika zaświadczania SDK używa również certyfikatu podczas rejestracji, aby przedstawić do uwierzytelniania i weryfikacji rejestracji.</li></ul> |
| Urządzenie | Uruchamianie i rejestrowanie | Ta operacja odpowiada trzeciej fazie automatycznego inicjowania obsługi administracyjnej, spełnionej przez oprogramowanie do rejestracji urządzenia utworzone przez dewelopera. Zobacz rolę dewelopera, aby uzyskać szczegółowe informacje. Przy pierwszym uruchomieniu: <ol><li>Aplikacja łączy się z wystąpieniem usługi inicjowania obsługi administracyjnej urządzeń, według globalnego adresu URL i usługi "Zakres identyfikatora" określony podczas tworzenia.</li><li>Po podłączeniu urządzenie jest uwierzytelniane względem metody zaświadczania i tożsamości określonej podczas rejestracji.</li><li>Po uwierzytelnieniu urządzenie jest rejestrowane w wystąpieniu Usługi IoT Hub określonym przez wystąpienie usługi inicjowania obsługi administracyjnej.</li><li>Po pomyślnej rejestracji unikatowy identyfikator urządzenia i punkt końcowy usługi IoT Hub są zwracane do aplikacji rejestracji do komunikowania się z Centrum IoT.</li><li> Stamtąd urządzenie może ściągać w dół jego początkowy stan [bliźniaczej reprezentacji urządzenia](~/articles/iot-hub/iot-hub-devguide-device-twins.md) dla konfiguracji i rozpocząć proces raportowania danych telemetrycznych.</li></ol>**Szybki start:** ponieważ urządzenie jest symulowane, oprogramowanie do rejestracji działa na stacji roboczej rozwoju.|

Na poniższym diagramie podsumowano role i sekwencjonowanie operacji podczas automatycznego inicjowania obsługi administracyjnej urządzenia:
<br><br>
[![Sekwencja automatycznego inicjowania obsługi administracyjnej urządzenia](./media/concepts-auto-provisioning/sequence-auto-provision-device-vs.png)](./media/concepts-auto-provisioning/sequence-auto-provision-device-vs.png#lightbox) 

> [!NOTE]
> Opcjonalnie producent może również wykonać operację "Rejestrowanie tożsamości urządzenia" przy użyciu interfejsów API usługi inicjowania obsługi administracyjnej urządzeń (zamiast za pośrednictwem operatora). Aby uzyskać szczegółowe omówienie tego sekwencjonowania i nie tylko, zobacz [rejestrację urządzenia typu Zero touch za pomocą wideo Usługi Azure IoT](https://youtu.be/cSbDRNg72cU?t=2460) (począwszy od znacznika 41:00)

## <a name="roles-and-azure-accounts"></a>Role i konta platformy Azure

Jak każda rola jest mapowana na konto platformy Azure jest zależna od scenariusza i istnieje sporo scenariuszy, które mogą być zaangażowane. Typowe wzorce poniżej powinny pomóc zapewnić ogólne zrozumienie dotyczące jak role są zazwyczaj mapowane na konto platformy Azure.

#### <a name="chip-manufacturer-provides-security-services"></a>Producent chipów świadczy usługi bezpieczeństwa

W tym scenariuszu producent zarządza zabezpieczeniami dla klientów poziomu 1. Ten scenariusz może być preferowany przez tych klientów poziomu jeden, ponieważ nie muszą zarządzać szczegółowymi zabezpieczeniami. 

Producent wprowadza zabezpieczenia do sprzętowych modułów zabezpieczeń (HSM). Zabezpieczenia te mogą obejmować producenta uzyskującego klucze, certyfikaty itp. Producent może również wygenerować te informacje zabezpieczające dla swoich klientów.

W tym scenariuszu mogą być dwa konta platformy Azure zaangażowanych:

- **#1 konta:** Prawdopodobnie współużytkowane przez operatora i dewelopera role do pewnego stopnia. Strona ta może zakupić chipy HSM od producenta. Żetony te są wskazywały na wystąpienia DPS skojarzone z #1 konta. Dzięki rejestracji DPS ta strona może dzierżawić urządzenia wielu klientom poziomu dwóch, ponownie konfigurując ustawienia rejestracji urządzeń w dps. Ta strona może również mieć centra IoT przydzielone dla systemów wewnętrznej bazy danych użytkownika końcowego do interfejsu z w celu uzyskania dostępu do telemetrii urządzenia itp. W tym ostatnim przypadku drugie konto może nie być potrzebne.

- **#2 konta:** Użytkownicy końcowi, klienci poziomu drugiego mogą mieć własne centra IoT. Strona powiązana z kontem #1 tylko punkty dzierżawione urządzenia do właściwego centrum na tym koncie. Ta konfiguracja wymaga łączenia centrów DPS i IoT na kontach platformy Azure, co można wykonać za pomocą szablonów usługi Azure Resource Manager.

#### <a name="all-in-one-oem"></a>Wszechstronny OEM

Producent może być "All-in-one OEM", gdzie potrzebne byłoby tylko jedno konto producenta. Producent obsługuje zabezpieczenia i inicjowania obsługi administracyjnej od końca do końca.

Producent może udostępnić aplikację chmurową klientom, którzy kupują urządzenia. Ta aplikacja będzie współpracować z Centrum IoT przyznane przez producenta.

Automaty vendingowe lub zautomatyzowane ekspresy do kawy stanowią przykłady dla tego scenariusza.




## <a name="next-steps"></a>Następne kroki

Pomocne może okazać się dodaniem do zakładek w tym artykule jako punkt odniesienia, podczas pracy nad odpowiednimi programami Szybkiej obsługi administracyjnej. 

Zacznij od ukończenia szybkiego startu "Konfigurowanie automatycznego inicjowania obsługi administracyjnej", który najlepiej odpowiada preferencjom narzędzia do zarządzania, który przeprowadzi Cię przez fazę "Konfiguracja usługi":

- [Konfigurowanie automatycznego inicjowania obsługi administracyjnej przy użyciu interfejsu wiersza polecenia platformy Azure](quick-setup-auto-provision-cli.md)
- [Konfigurowanie automatycznego inicjowania obsługi administracyjnej przy użyciu portalu Azure](quick-setup-auto-provision.md)
- [Konfigurowanie automatycznego inicjowania obsługi administracyjnej przy użyciu szablonu Menedżera zasobów](quick-setup-auto-provision-rm.md)

Następnie kontynuuj tworzenie przewodnika Szybki start "Automatyczne inicjowanie obsługi administracyjnej symulowanego urządzenia", który odpowiada mechanizmowi zaświadczania urządzenia i preferencjom SDK/językiem usługi inicjowania obsługi urządzeń. W tym przewodniku Szybki start przechodzisz przez fazy "Rejestracja urządzenia" i "Rejestracja i konfiguracja urządzenia": 

|  | Symulowany mechanizm zaświadczania urządzenia | Pakiet SDK/język szybkiego startu |  |
|--|--|--|--|
|  | Moduł TPM | [C](quick-create-simulated-device.md)<br>[Java](quick-create-simulated-device-tpm-java.md)<br>[C #](quick-create-simulated-device-tpm-csharp.md)<br>[Python](quick-create-simulated-device-tpm-python.md) |  |
|  | Certyfikat X.509 | [C](quick-create-simulated-device-x509.md)<br>[Java](quick-create-simulated-device-x509-java.md)<br>[C #](quick-create-simulated-device-x509-csharp.md)<br>[Node.js](quick-create-simulated-device-x509-node.md)<br>[Python](quick-create-simulated-device-x509-python.md) |  |




