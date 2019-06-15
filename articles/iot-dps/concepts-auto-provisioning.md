---
title: Usługa IoT Hub do aprowizacji urządzenia — automatycznej aprowizacji pojęcia
description: Ten artykuł zawiera omówienie pojęć dotyczących fazy urządzenia automatycznej aprowizacji przy użyciu IoT Device Provisioning Service, usługi IoT Hub i zestawy SDK klientów.
author: wesmc7777
ms.author: wesmc
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: 0df4eb664accd828c47d834fb0014d0d60f57458
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60746011"
---
# <a name="auto-provisioning-concepts"></a>Pojęcia automatycznej aprowizacji

Zgodnie z opisem w [Przegląd](about-iot-dps.md), usługi Device Provisioning Service to usługa pomocnika, która umożliwia just-in-time Inicjowanie obsługi urządzeń do usługi IoT hub, nie wymagając interwencji człowieka. Po pomyślnej aprowizacji urządzenia łączą się bezpośrednio z ich wyznaczonego Centrum IoT Hub. Ten proces jest nazywany automatycznej aprowizacji i zapewnia out-of--box rejestracji i konfiguracji początkowej środowisko dla urządzeń.

## <a name="overview"></a>Omówienie

Usługa Azure IoT auto-provisioning może można podzielić na trzy etapy:

1. **Konfiguracja usługi** -jednorazowej konfiguracji wystąpienia usługi Azure IoT Hub i IoT Hub Device Provisioning Service ustanawiania je i tworzenie połączenia między nimi.

   > [!NOTE]
   > Bez względu na rozmiar rozwiązania IoT, nawet jeśli planuje się obsługiwać miliony urządzeń to **jednorazowej konfiguracji**.

2. **Rejestrowanie urządzenia** — proces uświadamiania wystąpienie usługi Device Provisioning urządzeń, które będzie próbował zarejestrować się w przyszłości. [Rejestracja](concepts-service.md#enrollment) odbywa się przez skonfigurowanie informacji o tożsamości urządzenia w usłudze aprowizowania jako "rejestracji indywidualnej" na jednym urządzeniu lub "rejestrowanie grupy" dla wielu urządzeń. Tożsamość jest oparty na [mechanizmu zaświadczania](concepts-security.md#attestation-mechanism) urządzenia jest przeznaczony do stosowania, co umożliwia usługa aprowizowania recenzentom potwierdzenie, że autentyczności urządzeniem podczas rejestracji:

   - **Moduł TPM**: skonfigurowany jako "rejestracji indywidualnej", tożsamości urządzenia opiera się na identyfikator rejestracji modułu TPM i klucz publiczny adnotację. Biorąc pod uwagę, że moduł TPM jest [specyfikacji](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/), usługa tylko oczekuje, że poświadczenia na specyfikację, niezależnie od implementacji modułu TPM (sprzętowego lub programowego). Zobacz [Inicjowanie obsługi administracyjnej urządzeń: Poświadczania tożsamości z modułem TPM](https://azure.microsoft.com/blog/device-provisioning-identity-attestation-with-tpm/) szczegółowe informacje dotyczące zaświadczania opartych na modułach TPM. 

   - **X509**: skonfigurowany jako "rejestracji indywidualnej" lub "rejestrowanie grupy", urządzenia tożsamości opiera się na X.509 certyfikatu cyfrowego, który zostanie przekazany do rejestracji jako plik PEM lub cer.

   > [!IMPORTANT]  
   > Chociaż nie wymaganie wstępne przy użyciu usługi Device Provisioning, zdecydowanie zalecamy używania urządzenia sprzętowego modułu zabezpieczeń (HSM) do przechowywania informacji o tożsamości urządzenia poufne, takie jak klucze i certyfikaty X.509.

3. **Rejestracja urządzenia i konfiguracji** — zainicjowane po rozruchu przez oprogramowanie do rejestracji, który został skompilowany przy użyciu klienta usługi Device Provisioning SDK odpowiednie dla urządzenia i mechanizmu zaświadczania. Oprogramowanie ustanawia połączenie do inicjowania obsługi usługi na potrzeby uwierzytelniania urządzenia i późniejszej rejestracji w usłudze IoT Hub. Po pomyślnej rejestracji urządzenie otrzymuje jego usługi IoT Hub urządzenia Unikatowy identyfikator oraz informacje o połączeniu, co pozwala na ściąganie pierwotną konfigurację i rozpocząć proces telemetrii. W środowiskach produkcyjnych tej fazy może wystąpić kilka tygodni lub miesięcy po poprzednich dwóch faz.

## <a name="roles-and-operations"></a>Role i operacje

Fazy opisanych w poprzedniej sekcji może obejmować tygodni lub miesięcy, z powodu rzeczywistości produkcji takie jak czas produkcji wysyłania procesu celnymi itp. Ponadto mogą obejmować działań między wiele ról, biorąc pod uwagę różne jednostki biorące udział. W tej sekcji przyjmuje lepiej poznać różnymi rolami i operacje związane z każdej fazy, a następnie przedstawiono przepływ na diagramie sekwencji. 

Producent urządzenia określonego na włączenie mechanizmu zaświadczania również automatycznej aprowizacji powoduje wymagania. Czynności produkcyjnych może również wystąpić, niezależnie od chronometraż faz automatycznej aprowizacji, zwłaszcza w przypadkach, gdy nowych urządzeń są kupowane po automatycznej aprowizacji jest już ustanowione.

Seria przewodników Szybki Start znajdują się w spisie treści po lewej stronie, aby pomóc w wyjaśnieniu automatycznej aprowizacji za pośrednictwem praktyczne doświadczenie w pracy. W celu ułatwienia/uproszczenia procesu uczenia, oprogramowanie jest użyte do symulacji urządzenia fizycznego rejestracji i rejestracji. Niektóre przewodników Szybki Start wymaga wykonania operacji dla wielu ról, w tym operacje dla ról nie istnieje, ze względu na charakter symulowane Szybki Start.

| Rola | Operacja | Opis |
|------| --------- | ------------|
| Producent | Kodowanie adresu URL tożsamości i rejestrację | Oparte na mechanizmu zaświadczania używanego, producent jest odpowiedzialny za kodowanie informacje o tożsamości urządzenia i adres URL rejestracji usługi Device Provisioning.<br><br>**Przewodniki Szybki Start**: ponieważ jest symulowane urządzenie, nie istnieje żadna rola producenta. Zobacz jak uzyskać te informacje, który jest używany podczas programowania aplikacji do rejestracji próbki roli dla deweloperów, aby uzyskać szczegółowe informacje. |
| | Potwierdzenie tożsamości urządzenia | Twórcę informacje o tożsamości urządzenia producent jest odpowiedzialny za komunikację operatora (lub agenta wyznaczonego) lub bezpośredniego rejestrowania w usłudze Device Provisioning za pomocą interfejsów API.<br><br>**Przewodniki Szybki Start**: ponieważ jest symulowane urządzenie, nie istnieje żadna rola producenta. Zobacz jak się dostać tożsamości urządzenia, który służy do zarejestrować symulowane urządzenie w wystąpieniu usługi Device Provisioning Service roli operatora, aby uzyskać szczegółowe informacje. |
| Operator | Konfigurowanie automatycznego aprowizowania | Ta operacja odpowiada pierwszej fazy automatycznego aprowizowania.<br><br>**Przewodniki Szybki Start**: Możesz wykonać roli operatora, skonfigurowanie wystąpienia usługi Device Provisioning i Centrum IoT w subskrypcji platformy Azure. |
|  | Rejestrowanie tożsamości urządzenia | Ta operacja odpowiada drugiej fazy automatycznego aprowizowania.<br><br>**Przewodniki Szybki Start**: Wykonujesz roli operatora rejestrowania symulowanego urządzenia w wystąpieniu usługi Device Provisioning Service. Tożsamość urządzenia jest określana przez metodę zaświadczania jest symulowane w przewodniku Szybki Start (TPM lub X.509). Zobacz szczegóły zaświadczania rola dla deweloperów. |
| Usługa Device Provisioning,<br>Usługa IoT Hub | \<wszystkie operacje\> | Dla obu implementacji produkcyjnej z urządzeniami fizycznymi i przewodników Szybki Start z symulowanych urządzeń te role są spełnione za pośrednictwem usługi IoT, które należy skonfigurować w ramach subskrypcji platformy Azure. Role/operations działać dokładnie tak samo, jak Obojętność do inicjowania obsługi administracyjnej fizycznych a symulowane urządzenia usługi IoT. |
| Developer | Kompilacja/wdrażanie oprogramowania do rejestracji | Ta operacja odpowiada trzeci etap automatycznego aprowizowania. Deweloper jest odpowiedzialny za tworzenie i wdrażanie oprogramowania do rejestracji na urządzeniu przy użyciu odpowiedniego zestawu SDK.<br><br>**Przewodniki Szybki Start**: Przykładowa aplikacja rejestracji, które tworzysz symuluje rzeczywiste urządzenie, dla platform/wybranym języku, który jest uruchamiany na stacji roboczej (zamiast jej wdrożeniu na urządzeniu fizycznym). Aplikacji do rejestracji wykonuje te same operacje jako jeden wdrożony na urządzeniu fizycznym. Należy określić metodę zaświadczania (certyfikat modułu TPM lub X.509) oraz adres URL rejestracji i "Zakres identyfikatorów" wystąpienie usługi Device Provisioning Service. Tożsamość urządzenia jest określana przez logikę zaświadczania zestawu SDK w czasie wykonywania, na podstawie metody, które określisz: <ul><li>**Zaświadczenia modułu TPM** — uruchamia deweloperskiej stacji roboczej [aplikację symulatora modułu TPM](how-to-use-sdk-tools.md#trusted-platform-module-tpm-simulator). Po uruchomieniu oddzielną aplikację jest używany do wyodrębnienia "Klucz poręczenia" i "Identyfikator rejestracji" do użycia modułu TPM w rejestrowania tożsamości urządzenia. Logika zaświadczania zestawu SDK używa także symulator podczas rejestracji, aby przedstawić podpisany token sygnatury dostępu Współdzielonego dla uwierzytelniania i weryfikacji rejestracji.</li><li>**X509 zaświadczania** — za pomocą narzędzia do [wygenerować certyfikat](how-to-use-sdk-tools.md#x509-certificate-generator). Wygenerowany należy utworzyć plik certyfikatu, wymagany do użycia w rejestracji. Logika zaświadczania zestawu SDK również używa certyfikatu podczas rejestracji, aby przedstawić do uwierzytelniania i weryfikacji rejestracji.</li></ul> |
| Urządzenie | Rozruchu i rejestrowanie | Ta operacja odpowiada trzeci etap automatycznego aprowizowania spełnić utworzonych przez dewelopera oprogramowania do rejestracji urządzenia. Zobacz roli dla deweloperów, aby uzyskać szczegółowe informacje. Po pierwszym uruchomieniu: <ol><li>Aplikacja łączy się z wystąpieniem usługi Device Provisioning, globalnego adresu URL i usługi "Identyfikator zakresu" określone podczas tworzenia.</li><li>Po nawiązaniu połączenia, urządzenie jest uwierzytelniane względem metoda zaświadczania i tożsamość określona podczas rejestracji.</li><li>Po uwierzytelnieniu, urządzenie jest zarejestrowane w usłudze wystąpienie usługi IoT Hub, określony przez wystąpienie usługi aprowizacji.</li><li>Po pomyślnej rejestracji Unikatowy identyfikator i Centrum IoT Hub punkt końcowy urządzenia są zwracane do aplikacji rejestracji w celu komunikowania się z usługą IoT Hub.</li><li> W efekcie urządzenia można ściągnąć jego początkowego [bliźniaczej reprezentacji urządzenia](~/articles/iot-hub/iot-hub-devguide-device-twins.md) stan dla konfiguracji i rozpocząć proces raportowania danych telemetrycznych.</li></ol>**Przewodniki Szybki Start**: ponieważ jest symulowane urządzenie, oprogramowanie do rejestracji jest uruchamiany na deweloperskiej stacji roboczej.|

Poniższy diagram zawiera Podsumowanie ról i sekwencji operacji podczas aprowizacji automatycznej urządzenia:
<br><br>
[![Sekwencja automatycznej aprowizacji dla urządzenia](./media/concepts-auto-provisioning/sequence-auto-provision-device-vs.png)](./media/concepts-auto-provisioning/sequence-auto-provision-device-vs.png#lightbox) 

> [!NOTE]
> Opcjonalnie, producent można również wykonać operacji "Rejestrowanie tożsamości urządzenia", przy użyciu interfejsów API usługi aprowizacji urządzeń (zamiast przy użyciu operatora). Szczegółowe omówienie tej sekwencjonowania i nie tylko zobacz [Zero touch rejestracji urządzeń w usłudze wideo usługi Azure IoT](https://youtu.be/cSbDRNg72cU?t=2460) (rozpoczyna się od znacznika 41:00)

## <a name="roles-and-azure-accounts"></a>Role i kont platformy Azure

Jak każda rola jest mapowany na konto platformy Azure jest zależny od scenariusza, a istnieje kilka scenariuszy, które mogą być wykonywane. Typowe wzorce poniżej należy zapewnić ogólną wiedzą na temat jak role zazwyczaj są mapowane na konto platformy Azure.

#### <a name="chip-manufacturer-provides-security-services"></a>Producenta udostępnia usługi zabezpieczeń

W tym scenariuszu producenta zarządza zabezpieczeń dla klientów o jeden poziom. Ten scenariusz może preferowane przez tych klientów jeden poziom, nie ma do zarządzania zabezpieczeniami szczegółowe. 

Producent wprowadza zabezpieczeń do sprzętowych modułów zabezpieczeń (HSM). To zabezpieczeń mogą obejmować producenta, uzyskiwania kluczy, certyfikaty, itp. z potencjalnym klientom, którzy mają już wystąpień usługi DPS i konfiguracji grup rejestracji. Producent może również wygenerować informacji o zabezpieczeniach dla swoich klientów.

W tym scenariuszu mogą istnieć dwa konta platformy Azure zaangażowanych:

- **Konto #1**: Prawdopodobnie współużytkowane przez role operatora i dla deweloperów w pewnym stopniu. Ta strona zakupić od producenta mikroukładami modułu HSM. Te moduły są wskazywał wystąpienia usługi DPS skojarzone z kontem nr 1. Za pomocą rejestracji punktu dystrybucji ta strona może dzierżawić urządzenia do wielu klientów poziomu drugiego przez ponowne skonfigurowanie ustawień rejestracji urządzeń w usłudze DPS. Ta strona może być konieczne centra IoT Hub przydzielone dla użytkowników końcowych systemów zaplecza do połączenia interfejsem z w celu uzyskania dostępu do danych telemetrycznych z urządzenia itp. W tym drugim przypadku drugiego konta nie mogą być potrzebne.

- **Konto #2**: Użytkownicy końcowi poziom dwóch klientów może mieć własne centra IoT Hub. Strona skojarzonej z punktami tylko 1 konta dzierżawy urządzenia poprawne Centrum na tym koncie. Ta konfiguracja wymaga usługi DPS i IoT hubs zawierający linki między kontami platformy Azure, które może odbywać się przy użyciu szablonów usługi Azure Resource Manager.

#### <a name="all-in-one-oem"></a>W jednym OEM

Producent może być "OEM w jednym", gdy będą potrzebne na koncie jednego producenta. Producent obsługuje zabezpieczenia i Inicjowanie obsługi administracyjnej typu end to end.

Producent może dostarczyć oparte na chmurze aplikacji klientów, którzy zakupią urządzeń. Ta aplikacja będzie interfejs z usługą IoT Hub, przydzielany przez producenta.

Automaty lub zautomatyzowane kawy reprezentują przykłady w tym scenariuszu.




## <a name="next-steps"></a>Kolejne kroki

Może okazać się przydatne do zakładki w tym artykule jako punkt odniesienia, podczas pracy swój sposób za pomocą odpowiedniej automatycznej aprowizacji przewodników Szybki Start. 

Rozpocznij, wykonując "Konfigurowanie automatycznego aprowizowania" Szybki Start najlepiej odpowiadającą Preferencje narzędzia do zarządzania, przeprowadzi Cię przez fazy "Konfiguracja usługi":

- [Konfigurowanie automatycznego aprowizowania za pomocą wiersza polecenia platformy Azure](quick-setup-auto-provision-cli.md)
- [Konfigurowanie automatycznego aprowizowania za pomocą witryny Azure portal](quick-setup-auto-provision.md)
- [Konfigurowanie automatycznego aprowizowania za pomocą szablonu usługi Resource Manager](quick-setup-auto-provision-rm.md)

Następnie przejdź do przewodnika Szybki Start "Auto aprowizowanie symulowanego urządzenia", pasujące swoje mechanizm zaświadczania urządzenia i usługi Device Provisioning Service SDK/języka ustawionego preferencji. W tym przewodniku Szybki Start opisano fazy "Rejestrowanie urządzeń" i "urządzenia, Rejestracja i konfiguracja": 

|  | Mechanizm zaświadczania z symulowanego urządzenia | Zestaw SDK/języka ustawionego Szybki Start |  |
|--|--|--|--|
|  | Moduł zaufanej platformy (TPM) | [C](quick-create-simulated-device.md)<br>[Java](quick-create-simulated-device-tpm-java.md)<br>[C#](quick-create-simulated-device-tpm-csharp.md)<br>[Python](quick-create-simulated-device-tpm-python.md) |  |
|  | Certyfikat X.509 | [C](quick-create-simulated-device-x509.md)<br>[Java](quick-create-simulated-device-x509-java.md)<br>[C#](quick-create-simulated-device-x509-csharp.md)<br>[Node.js](quick-create-simulated-device-x509-node.md)<br>[Python](quick-create-simulated-device-x509-python.md) |  |




