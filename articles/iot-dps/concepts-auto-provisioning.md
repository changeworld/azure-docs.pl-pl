---
title: Centrum IoT urządzenia inicjowania obsługi usługi — pojęcia automatycznego inicjowania obsługi administracyjnej
description: Ten artykuł zawiera omówienie pojęć dotyczących fazy urządzenia automatycznego inicjowania obsługi, za pomocą usługi udostępniania urządzenia IoT, Centrum IoT i zestawów SDK klienta.
services: iot-dps
keywords: ''
author: BryanLa
ms.author: bryanla
ms.date: 03/27/2018
ms.topic: conceptual
ms.service: iot-dps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: ''
ms.openlocfilehash: cd458b1f6d26fbd5f5821a04cd01be5c3a4e4514
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="auto-provisioning-concepts"></a>Pojęcia automatycznego inicjowania obsługi administracyjnej

Zgodnie z opisem w [omówienie](about-iot-dps.md), usługa inicjowania obsługi urządzeń jest usługę pomocnika w czasie inicjowania obsługi urządzeń Centrum IoT bez udziału człowieka. Po pomyślnym zainicjowaniu obsługi administracyjnej, urządzenia łączą się bezpośrednio z ich wyznaczonego Centrum IoT. Ten proces jest nazywany automatycznego inicjowania obsługi administracyjnej i zapewnia poza pole rejestracji i konfiguracji początkowej dla urządzeń.

## <a name="overview"></a>Przegląd

Azure IoT automatycznego inicjowania obsługi może być dzielony na trzech etapów:

1. **Konfiguracja usługi** -jednorazowej konfiguracji wystąpienia usługi Azure IoT Hub i inicjowania obsługi usługi IoT Hub urządzeń nawiązywania z nimi oraz tworzenie połączenia między nimi.

   > [!NOTE]
   > Niezależnie od rozmiaru rozwiązania IoT, nawet jeśli planuje się obsługiwać miliony urządzeń, to **jednorazowej konfiguracji**.

2. **Rejestracja urządzeń** — proces uświadamiania wystąpienia usługi inicjowania obsługi urządzeń urządzeń, które będzie podejmować próby zarejestrowania w przyszłości. Rejestracja odbywa się przez skonfigurowanie informacji o tożsamości urządzenia w usłudze inicjowania obsługi administracyjnej jako "poszczególnych rejestrowanie" pojedynczej lub "rejestrowanie grupy" dla wielu urządzeń. Tożsamość na podstawie [mechanizmu zaświadczania](concepts-security.md#attestation-mechanism) urządzenia jest przeznaczony do użycia, które umożliwia usłudze inicjowania obsługi administracyjnej potwierdzają autentyczności urządzenia podczas rejestracji:

   - **Moduł TPM**: skonfigurowany jako "Rejestrowanie poszczególnych", tożsamości urządzenia opiera się na identyfikator rejestracji modułu TPM i klucz publiczny poręczenia. Biorąc pod uwagę, że moduł TPM jest [specyfikacji]((https://trustedcomputinggroup.org/work-groups/trusted-platform-module/)), usługa oczekuje tylko poświadczanie według specyfikacji, niezależnie od implementacji modułu TPM (sprzętowego lub programowego). Zobacz [Inicjowanie obsługi administracyjnej urządzeń: zaświadczanie o tożsamości z modułem TPM](https://azure.microsoft.com/blog/device-provisioning-identity-attestation-with-tpm/) szczegółowe informacje dotyczące zaświadczania opartych na modułach TPM. 

   - **X509**: skonfigurowany jako "Rejestrowanie poszczególnych" lub "rejestrowanie grupy", tożsamości urządzenia jest oparta na cyfrowe certyfikatu X.509, który zostanie przekazany do rejestracji jako plik PEM lub cer.

   > [!IMPORTANT]  
   > Chociaż nie wymaganie wstępne dotyczące korzystania z urządzenia świadczenie usług, zdecydowanie zalecamy używanie urządzenia sprzętowego modułu zabezpieczeń (HSM) do przechowywania informacji o tożsamości urządzenia poufne, takie jak klucze i certyfikaty X.509.

3. **Rejestracja urządzenia i Konfiguracja** — inicjowane przy rozruchu przez oprogramowanie rejestracji jest utworzony przy użyciu klienta usługi inicjowania obsługi urządzeń SDK odpowiednie dla mechanizmu urządzenia i zaświadczania. Oprogramowanie ustanawia połączenie do inicjowania obsługi usługi do uwierzytelniania urządzeń i późniejszej rejestracji w Centrum IoT. Po pomyślnej rejestracji urządzenia jest dostarczany z jego Centrum IoT urządzenia Unikatowy identyfikator oraz informacje o połączeniu, dzięki któremu ściągnięcia jego początkową konfigurację i rozpocząć proces telemetrii. W środowiskach produkcyjnych w tej fazie może wystąpić, tygodnie lub miesiące po poprzednich dwóch faz.

## <a name="roles-and-operations"></a>Role i operacje

Fazy opisanych w poprzedniej sekcji może obejmować tygodnie lub miesiące, z powodu rzeczywistości produkcji jak czasu produkcji wysyłania procesu celne itp. Ponadto można obejmują one działania wielu ról podane różne jednostki biorące udział. W tej sekcji przedstawia bardziej różnych ról i operacje związane z każdej fazy, a następnie przedstawiono przepływ na diagramie sekwencji. 

Również automatycznego inicjowania obsługi administracyjnej umieszcza wymagania na producenta urządzenia, specyficzne dla włączenie mechanizmu zaświadczania. Czynności produkcyjnych może również wystąpić niezależne faz automatycznego inicjowania obsługi administracyjnej, szczególnie w przypadkach, gdy nowych urządzeń są kupowane po automatycznego inicjowania obsługi administracyjnej już została ustanowiona w czasie.

Szereg poradniki Szybki Start znajdują się w spis treści z lewej strony, w celu lepszego objaśnienia automatyczne — Obsługa administracyjna przy użyciu w praktyce. W celu ułatwienia/uprościć proces uczenia, oprogramowanie służy do symulowania rejestracji i rejestracji urządzenia fizycznego. Niektóre elementy szybkiego startu wymagają wykonania operacji dla wielu ról, w tym operacje dla nieistniejącego ról, z powodu symulowane rodzaj Przewodniki Szybki Start.

| Rola | Operacja | Opis | 
|------| --------- | ------------| 
| Producent | Kodowanie adresu URL tożsamości i rejestrację | Oparte na mechanizmie zaświadczania używany, producent jest odpowiedzialny za kodowanie informacje o tożsamości urządzenia i adres URL rejestracji urządzeń usługi udostępniania.<br><br>**Przewodniki Szybki Start**: ponieważ symulacji urządzenia nie jest Brak roli producenta. Zobacz roli deweloperów, aby uzyskać więcej informacji o jak uzyskać tę informację, która jest używana w przykładowej aplikacji rejestracji kodowania. |  
| | Potwierdzenie tożsamości urządzenia | Twórcę informacje o tożsamości urządzenia producent jest odpowiedzialny za komunikację operatora (lub wyznaczonym agenta) lub bezpośredniego rejestrowania usługi inicjowania obsługi urządzeń za pośrednictwem interfejsów API.<br><br>**Przewodniki Szybki Start**: ponieważ symulacji urządzenia nie jest Brak roli producenta. Zobacz roli operatora, aby uzyskać szczegółowe informacje o jak wprowadzasz tożsamości tego urządzenia, który jest używany do rejestrowania symulowane urządzenie w wystąpieniu usługi inicjowania obsługi urządzeń. | 
| Operator | Konfigurowanie automatycznego inicjowania obsługi administracyjnej | Tej operacji odpowiada pierwszą fazę automatycznego inicjowania obsługi administracyjnej.<br><br>**Przewodniki Szybki Start**: wykonywania roli operatora konfigurowania usług inicjowania obsługi urządzeń i wystąpień Centrum IoT w Twojej subskrypcji platformy Azure. | Ustawienie automatycznego inicjowania obsługi |
|  | Rejestrowanie tożsamości urządzenia | Tej operacji odpowiada na drugim etapie automatycznego inicjowania obsługi administracyjnej.<br><br>**Przewodniki Szybki Start**: wykonaj roli operatora rejestrowania symulowane urządzenie w wystąpieniu usługi inicjowania obsługi urządzeń. Tożsamość tego urządzenia jest określana przez metodę zaświadczania jest symulowane w szybki start (TPM lub X.509). Zobacz szczegółowe zaświadczania rola Developer. | 
| Usługa inicjowania obsługi urządzeń,<br>Usługa IoT Hub | \<wszystkie operacje\> | Dla obu implementacji produkcyjnej z urządzeń fizycznych i Przewodniki Szybki Start z symulowanego urządzenia te role są spełnione za pośrednictwem usługi IoT, które można skonfigurować w ramach subskrypcji platformy Azure. Role/operations działać dokładnie tak samo, usługi IoT są Obojętność do inicjowania obsługi administracyjnej a symulowanego urządzenia fizycznego. | 
| Developer | Kompilacji/wdrażanie oprogramowania rejestracji | Tej operacji odpowiada trzeci etap automatycznego inicjowania obsługi administracyjnej. Deweloper jest odpowiedzialny za tworzenie i wdrażanie oprogramowania rejestracji na urządzeniu przy użyciu odpowiedniego zestawu SDK.<br><br>**Przewodniki Szybki Start**: przykładowa aplikacja rejestracji tworzenia symuluje rzeczywistego urządzenia, dla Twojej platformy/języka wyboru, która działa na stacji roboczej (zamiast wdrożenia go na urządzeniu fizycznym). Aplikacji rejestracji wykonuje te same operacje jako jeden wdrożone do urządzenia fizycznego. Możesz określić metodę zaświadczania (TPM lub X.509 certyfikatu), oraz adresu URL rejestracji i 'Identyfikator Scope' wystąpienia usługi inicjowania obsługi urządzeń. Tożsamości tego urządzenia jest określana przez logikę zaświadczania zestawu SDK w czasie wykonywania na podstawie metody, które można określić: <ul><li>**Zaświadczenie modułu TPM** — uruchamia deweloperskiej stacji roboczej [modułu TPM symulatora aplikacji](how-to-use-sdk-tools.md#trusted-platform-module-tpm-simulator). Po uruchomieniu innej aplikacji służy do wyodrębniania "Klucz poręczenia" i "Identyfikator rejestracji" Użyj modułu TPM w rejestracji tożsamości tego urządzenia. Logika zaświadczania SDK używa również symulator podczas rejestracji, do prezentowania podpisany token sygnatury dostępu Współdzielonego dla uwierzytelniania i weryfikacji rejestracji.</li><li>**X509 zaświadczania** — za pomocą narzędzia do [wygenerowanie certyfikatu z](how-to-use-sdk-tools.md#x509-certificate-generator). Wygenerowany należy utworzyć plik certyfikatu wymagane do użycia w rejestracji. Logiki zaświadczania SDK używa także certyfikatu podczas rejestracji, do prezentowania uwierzytelniania i weryfikacji rejestracji.</li></ul> | 
| Urządzenie | Rozruchowego i rejestrowanie | Tej operacji odpowiada trzeci etap obsługi automatycznego spełnić utworzony przez dewelopera oprogramowania rejestracji urządzenia. Zobacz roli deweloperów, aby uzyskać więcej informacji. Po pierwszym uruchomieniu: <ol><li>Aplikacja nawiązuje połączenie z wystąpieniem usługi inicjowania obsługi urządzeń, na globalnego adresu URL i usługa 'Identyfikator Scope' określone podczas tworzenia.</li><li>Po połączeniu urządzenia jest uwierzytelniane względem metoda zaświadczania i tożsamość określona podczas rejestracji.</li><li>Po uwierzytelnieniu urządzenie jest zarejestrowane w usłudze określonego przez wystąpienie usługi inicjowania obsługi administracyjnej wystąpienia Centrum IoT.</li><li>Po pomyślnej rejestracji punktu końcowego Centrum IoT i identyfikator unikatowy urządzenia są zwracane do rejestracji aplikacji do komunikowania się z Centrum IoT.</li><li> Z tego miejsca, urządzenie może rozwiń jego początkowego [dwie urządzenia](~/articles/iot-hub/iot-hub-devguide-device-twins.md) stan dla konfiguracji i rozpocząć proces raportowania danych telemetrycznych.</li></ol>**Przewodniki Szybki Start**: ponieważ symulacji urządzenia oprogramowania rejestracji jest uruchamiany na deweloperskiej stacji roboczej.| 

Poniższy diagram przedstawia role i sekwencjonowania operacje podczas urządzenia automatycznego inicjowania obsługi:
<br><br>
![Automatyczne inicjowanie obsługi administracyjnej sekwencji dla urządzenia](./media/concepts-auto-provisioning/sequence-auto-provision-device-vs.png) 

> [!NOTE]
> Opcjonalnie producenta można również wykonać operację "Rejestrowanie urządzenia tożsamości" przy użyciu interfejsów API usługi inicjowania obsługi urządzeń (zamiast za pomocą operatora). Szczegółowe omówienie tej sekwencji i inne, zobacz [Zero touch rejestracji urządzenia Azure IoT wideo](https://myignite.microsoft.com/sessions/55087) (rozpoczyna się od znacznika 41:00)

## <a name="next-steps"></a>Kolejne kroki

Może być przydatne do zakładki w tym artykule jako punkt odniesienia, podczas pracy w drodze za pomocą odpowiednich skrócone podręczniki automatycznego inicjowania obsługi administracyjnej. 

Rozpocznij, wykonując "Konfigurowanie automatycznego inicjowania obsługi administracyjnej" szybkiego startu najlepiej pasujące do swoich preferencji narzędzia zarządzania, który przeprowadzi Cię przez kolejne fazy "Konfigurację usługi":

- [Konfigurowanie automatycznego inicjowania obsługi przy użyciu wiersza polecenia platformy Azure](quick-setup-auto-provision-cli.md)
- [Konfigurowanie automatycznego inicjowania obsługi przy użyciu portalu Azure](quick-setup-auto-provision.md)
- [Konfigurowanie automatycznego inicjowania obsługi przy użyciu szablonu usługi Resource Manager](quick-setup-auto-provision-rm.md)

Następnie kontynuuj Szybki Start "Auto-provision symulowane urządzenie" pasującą do Twojego urządzenia zaświadczania mechanizm i preferencji językowi zestawu SDK usługi inicjowania obsługi urządzeń. Ta opcja szybkiego startu służy do przeprowadzenie fazy "Rejestrowanie urządzeń" i "rejestracji urządzenia oraz konfiguracji": 

|  | Mechanizm zaświadczania symulowane urządzenie | Zestaw SDK językowi Szybki Start |  |
|--|--|--|--|
|  | Moduł TPM (TPM) | [C](quick-create-simulated-device.md)<br>[Java](quick-create-simulated-device-tpm-java.md)<br>[C#](quick-create-simulated-device-tpm-csharp.md)<br>[Python](quick-create-simulated-device-tpm-python.md) |  |
|  | Certyfikat X.509 | [C](quick-create-simulated-device-x509.md)<br>[Java](quick-create-simulated-device-x509-java.md)<br>[C#](quick-create-simulated-device-x509-csharp.md)<br>[Node.js](quick-create-simulated-device-x509-node.md)<br>[Python](quick-create-simulated-device-x509-python.md) |  |




