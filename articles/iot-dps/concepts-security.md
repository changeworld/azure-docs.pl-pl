---
title: Pojęcia dotyczące zabezpieczeń w usłudze Azure IoT Hub urządzenia inicjowania obsługi administracyjnej | Dokumentacja firmy Microsoft
description: Opisuje zabezpieczenia, inicjowanie obsługi administracyjnej pojęcia specyficzne dla urządzeń z usługi udostępniania urządzenia i Centrum IoT
services: iot-dps
keywords: ''
author: nberdy
ms.author: nberdy
ms.date: 03/30/2018
ms.topic: article
ms.service: iot-dps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: f6410aa3ab21e7c50ec6918930f31b9e1455c464
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="iot-hub-device-provisioning-service-security-concepts"></a>Pojęcia dotyczące zabezpieczeń inicjowania obsługi usługi IoT Hub urządzeń 

Usługa inicjowania obsługi urządzeń Centrum IoT to usługa pomocnika do Centrum IoT, które są używane do konfigurowania urządzenia bezobsługową alokacji dla określonego Centrum IoT. Za pomocą usługi udostępniania urządzenia można [auto-provision](concepts-auto-provisioning.md) milionów urządzeń w sposób bezpieczny i skalowalności. Ten artykuł zawiera omówienie *zabezpieczeń* pojęcia związane z w Inicjowanie obsługi administracyjnej urządzeń. W tym artykule ma zastosowanie do wszystkich osoby objętego przygotowanie do wdrożenia urządzenia.

## <a name="attestation-mechanism"></a>Mechanizm zaświadczania

Mechanizm zaświadczania jest metoda stosowana do potwierdzenia tożsamości urządzenia. Mechanizm zaświadczania ma również zastosowanie do listy rejestracji, co informuje inicjowania obsługi usługi, którego metoda zaświadczania do użycia z danym urządzeniem.

> [!NOTE]
> Centrum IoT używa "schemat uwierzytelniania" podobne koncepcji w tej usłudze.

Usługa inicjowania obsługi urządzeń obsługuje dwa rodzaje poświadczenie:
* **Certyfikaty X.509** oparte na przepływie standardowe uwierzytelnianie certyfikatu X.509.
* **Zaufane TPM (Trusted Platform Module)** oparte na żądanie nonce, przy użyciu standardowego modułu TPM kluczy do prezentowania podpisany token dostępu sygnatury dostępu Współdzielonego. Ta sytuacja nie wymaga fizycznej modułu TPM na urządzeniu, ale usługa oczekuje, że poświadczenia przy użyciu klucza poręczenia na [specyfikacji modułu TPM](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/).

## <a name="hardware-security-module"></a>Sprzętowy moduł zabezpieczeń

Sprzętowego modułu zabezpieczeń lub modułu HSM, służy do bezpieczny, sprzętowy magazyn kluczy tajnych urządzenia, a jest to najbezpieczniejsze formą tajny magazynu. Certyfikatów X.509 i tokeny sygnatury dostępu Współdzielonego mogą być przechowywane w module HSM. Moduły HSM mogą być używane z obu mechanizmów poświadczenie obsługuje inicjowania obsługi administracyjnej.

> [!TIP]
> Zdecydowanie zaleca się użycie modułu HSM z urządzeniami bezpiecznie przechowywać kluczy tajnych na urządzeniach.

Klucze tajne urządzenia mogą być również przechowywane w oprogramowaniu (pamięci), ale jest mniej bezpieczne formularz magazynu niż modułu HSM.

## <a name="trusted-platform-module"></a>Moduł TPM

Moduł TPM może odwoływać się do standardowego bezpiecznie przechowywane klucze służące do uwierzytelniania platformy lub może się odnosić do interfejsu we/wy służy do interakcji z modułami implementowania standardowego. Moduły TPM może istnieć jako odrębny sprzętu, sprzęt zintegrowane, oparte na oprogramowania sprzętowego lub programowego. Dowiedz się więcej o [moduły TPM i modułu TPM zaświadczania](/windows-server/identity/ad-ds/manage/component-updates/tpm-key-attestation). Usługa inicjowania obsługi urządzeń obsługuje tylko modułu TPM 2.0.

Zaświadczenie modułu TPM jest oparta na żądanie nonce używający poręczenia i przechowywanie kluczy głównych do prezentowania podpisany token dostępu sygnatury dostępu Współdzielonego.

### <a name="endorsement-key"></a>Klucz poręczenia

Klucz poręczenia jest klucza asymetrycznego znajdująca się wewnątrz modułu TPM, który wewnętrznie wygenerowany lub wprowadzonym w produkcji czasu i jest unikatowy dla każdego modułu TPM. Klucz poręczenia nie można zmienić ani usunąć. Prywatny klucz poręczenia nigdy nie zwolnieniu poza modułu TPM, gdy publicznej części klucza poręczenia jest używane do rozpoznawania oryginalnego modułu TPM. Dowiedz się więcej o [klucz poręczenia](https://technet.microsoft.com/library/cc770443(v=ws.11).aspx).

### <a name="storage-root-key"></a>Klucz głównego magazynu

Klucza głównego magazynowania jest przechowywany w module TPM i jest używany do ochrony kluczy TPM tworzonych przez aplikacje, dzięki czemu te klucze nie można używać bez modułu TPM. Klucz główny magazynowania jest generowany, gdy własność modułu TPM; Wyczyszczenie modułu TPM, nowy użytkownik może przejąć na własność jest generowany nowy klucz główny magazynowania. Dowiedz się więcej o [klucza głównego magazynowania](https://technet.microsoft.com/library/cc753560(v=ws.11).aspx).

## <a name="x509-certificates"></a>Certyfikaty X.509

Za pomocą certyfikatów X.509 jako mechanizmu zaświadczania jest doskonałym sposobem skalowania produkcyjnych i uprościć Inicjowanie obsługi administracyjnej urządzeń. Certyfikaty X.509 zwykle są rozmieszczone w łańcuch certyfikatów zaufania, w którym każdy z certyfikatów w łańcuchu jest podpisany przy użyciu klucza prywatnego następnym wyższym certyfikatu i itd., przerywanie certyfikatu głównego z podpisem własnym. W ten sposób ustanawiany delegowanego łańcucha zaufania od certyfikatu głównego, generowane przez zaufany główny urząd certyfikacji (CA) w dół za pośrednictwem każdej pośredniego urzędu certyfikacji z certyfikatem "liścia" jednostki końcowej zainstalowana na urządzeniu. Aby dowiedzieć się więcej, zobacz [uwierzytelnianie urządzenia za pomocą certyfikatów X.509 urzędu certyfikacji](/azure/iot-hub/iot-hub-x509ca-overview). 

Często łańcucha certyfikatów reprezentuje niektórych hierarchii logiczną lub fizyczną skojarzone z urządzeń. Producent może na przykład:
- wystawiania certyfikatu z podpisem własnym głównego urzędu certyfikacji
- Generuj unikatowy certyfikat pośredniego urzędu certyfikacji dla każdego fabryki przy użyciu certyfikatu głównego
- Generuj unikatowy certyfikat pośredniego urzędu certyfikacji dla każdego wiersza produkcyjnym w zakładzie za pomocą certyfikatu każdej fabryki
- i na koniec Użyj certyfikatu wiersza produkcyjnym do wygenerowania certyfikatu unikatowych urządzeń (jednostek końcowych) dla każdego urządzenia wyprodukowany w wierszu. 

Aby dowiedzieć się więcej, zobacz [pojęć związanych z certyfikatami X.509 urzędu certyfikacji w branży IoT](/azure/iot-hub/iot-hub-x509ca-concept). 

### <a name="root-certificate"></a>Certyfikat główny

Certyfikat główny jest samopodpisany certyfikat X.509 reprezentujący urząd certyfikacji (CA). Jest to końca ani kotwicy zaufania łańcucha certyfikatów. Certyfikaty główne można samodzielnie wystawiony przez organizację lub zakupione od głównego urzędu certyfikacji. Aby dowiedzieć się więcej, zobacz [certyfikatów Pobierz urzędu certyfikacji X.509](/azure/iot-hub/iot-hub-security-x509-get-started#get-x509-ca-certificates). Certyfikat główny może także określane jako certyfikat głównego urzędu certyfikacji.

### <a name="intermediate-certificate"></a>Certyfikat pośredniego

Certyfikat pośredniego to certyfikat X.509, który został podpisany certyfikat główny (lub innego certyfikatu pośredniego certyfikat główny w łańcuchu). Ostatni pośredniego certyfikat w łańcuchu jest używany do podpisywania certyfikatu liścia. Certyfikatu pośredniego może być również określany jako certyfikat pośredniego urzędu certyfikacji.

### <a name="end-entity-leaf-certificate"></a>Certyfikat jednostki końcowej "liścia"

Certyfikat liścia lub certyfikatów jednostek końcowych identyfikuje właściciela certyfikatu. Ma on certyfikat główny w łańcuchu certyfikatów, a także certyfikaty pośrednie zero lub więcej. Certyfikat liścia nie jest używany do podpisywania innych certyfikatów. Unikatowo identyfikuje urządzenia do inicjowania obsługi usługi i czasami jest nazywany certyfikatu tego urządzenia. Podczas uwierzytelniania urządzenia przy użyciu klucza prywatnego skojarzonego z tym certyfikatem odpowiedzieć na potwierdzenie posiadania żądania z usługi. Aby dowiedzieć się więcej, zobacz [uwierzytelniania urządzenia podpisane za pomocą certyfikatów X.509 urzędu certyfikacji](/azure/iot-hub/iot-hub-x509ca-overview#authenticating-devices-signed-with-x509-ca-certificates).

## <a name="controlling-device-access-to-the-provisioning-service-with-x509-certificates"></a>Kontrolowanie dostępu do urządzenia do inicjowania obsługi usługi za pomocą certyfikatów X.509

Inicjowania obsługi usługi udostępnia dwa typy wpisu rejestracji, który służy do kontroli dostępu dla urządzeń, które korzystają z mechanizmu zaświadczania X.509:  

- [Poszczególne rejestracji](./concepts-service.md#individual-enrollment) wpisy są skonfigurowane przy użyciu certyfikatu urządzenia skojarzone z określonym urządzeniem. Te wpisy kontroli rejestracji dla określonych urządzeń.
- [Grupa rejestracji](./concepts-service.md#enrollment-group) wpisy są skojarzone z określonym pośredniego lub certyfikatu głównego urzędu certyfikacji. Te wpisy kontroli rejestracji dla wszystkich urządzeń mających pośredniego lub głównego certyfikatu w łańcuchu swoich certyfikatów. 

Gdy urządzenie łączy się z usługą inicjowania obsługi administracyjnej, usługa priorytetem bardziej szczegółowe rejestrowanie wpisów za pośrednictwem mniej określonych wpisów rejestracji. Oznacza to jeśli istnieje poszczególnych rejestracji dla urządzenia, inicjowania obsługi usługi dotyczy tego wpisu. Jeśli nie istnieje żadne poszczególnych rejestracji urządzenia i rejestracji dla pierwszego certyfikatu pośredniego w łańcuchu certyfikatów urządzenia istnieje, usługa dotyczy tego wpisu i tak dalej, zapasowej łańcucha do katalogu głównego. Usługa dotyczy pierwszego wpisu dotyczy, zostaną znalezione, tak, aby:

- Jeśli pierwszy znaleziono wpisu rejestracji jest włączone, usługa aprowizuje dla urządzenia.
- Jeśli pierwszy znaleziono wpisu rejestracji jest wyłączona, usługa nie udostępnić urządzenia.  
- Jeśli nie ma wpisu rejestracji zostanie znaleziony jakichkolwiek certyfikatów w łańcuchu certyfikatów urządzenia, usługi nie udostępnić urządzenia. 

Ten mechanizm i łańcuchów certyfikatów strukturę hierarchiczną zapewnia zaawansowane elastyczność w sposób można sterować dostępem dla poszczególnych urządzeń, a także jak w przypadku grup urządzeń. Załóżmy na przykład pięciu urządzeń z następujących łańcuchami certyfikatów: 

- *Urządzenia 1*: certyfikat, A -> certyfikatu urządzenia 1 -> certyfikat główny
- *Urządzenie 2*: certyfikat, A -> certyfikatu urządzenia 2 -> certyfikat główny
- *Urządzenie 3*: certyfikat, A -> certyfikatu urządzenia 3 -> certyfikat główny
- *Urządzenia 4*: certyfikat certyfikatu urządzenia 4 -> B -> certyfikat główny
- *Urządzenia, 5*: certyfikat certyfikatu urządzenia, 5 -> B -> certyfikat główny

Początkowo można utworzyć wpis rejestracji pojedynczej grupy włączone dla certyfikatu głównego włączyć dostęp do wszystkich pięciu urządzeń. Jeśli certyfikat B później zostanie naruszony, można utworzyć wpis grupy wyłączone rejestracji certyfikatu B zapobiec *4 urządzenia* i *urządzenia, 5* z rejestrowania. Jeśli nadal nowsze *3 urządzenia* staje się naruszony, można utworzyć wpis wyłączenia poszczególnych rejestracji dla swojego certyfikatu. To odwołanie dostępu dla *3 urządzenia*, ale nadal umożliwia *1 urządzenia* i *2 urządzenia* do zarejestrowania.