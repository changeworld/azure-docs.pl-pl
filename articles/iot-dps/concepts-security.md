---
title: Pojęcia dotyczące zabezpieczeń w usłudze Azure IoT Hub Device Provisioning Service | Dokumentacja firmy Microsoft
description: W tym artykule opisano specyficzne dla urządzeń z usługą Device Provisioning i IoT Hub koncepcje dotyczące aprowizacji zabezpieczeń
author: nberdy
ms.author: nberdy
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: e35330874c647eba2cddde694563c8a1d9e83df5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60775121"
---
# <a name="iot-hub-device-provisioning-service-security-concepts"></a>Pojęcia dotyczące zabezpieczeń IoT Hub Device Provisioning Service 

IoT Hub Device Provisioning Service to usługa pomocnika usługi IoT Hub, możesz użyć do skonfigurowania urządzenia i bezobsługowa aprowizacja w określonej usługi IoT hub. Przy użyciu usługi Device Provisioning, można [automatycznej obsługi](concepts-auto-provisioning.md) milionów urządzeń w bezpieczny i skalowalny sposób. Ten artykuł zawiera omówienie *zabezpieczeń* pojęcia zaangażowane w Inicjowanie obsługi administracyjnej urządzeń. Ten artykuł dotyczy wszystkich osób zajmujących się przygotowywanie do wdrożenia na urządzeniu.

## <a name="attestation-mechanism"></a>Mechanizm zaświadczania

Mechanizm zaświadczania jest metoda stosowana do potwierdzania tożsamości urządzenia. Mechanizm zaświadczania ma również zastosowanie listy rejestracji, która informuje usługę aprowizacji metody poświadczenie do użycia z danego urządzenia.

> [!NOTE]
> Usługa IoT Hub używa "schemat uwierzytelniania" podobne pojęcie w tej usłudze.

Usługi Device Provisioning obsługuje następujące rodzaje zaświadczania:
* **Certyfikaty X.509** oparte na standardowych przepływ uwierzytelniania certyfikatu X.509.
* **Trusted Platform Module (TPM)** oparte na wezwanie nonce przy użyciu standardowego modułu TPM klucze do przedstawienia podpisany token sygnatury dostępu współdzielonego (SAS). Ta forma zaświadczania nie wymaga fizyczny moduł TPM na urządzeniu, ale usługa oczekuje, że poświadczenia przy użyciu klucza poręczenia na [specyfikacji TPM](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/).
* **Klucz symetryczny** oparte na sygnatury dostępu współdzielonego (SAS) [tokeny zabezpieczające](../iot-hub/iot-hub-devguide-security.md#security-tokens), które obejmują sygnatury skrótu i osadzone wygaśnięcia. Aby uzyskać więcej informacji, zobacz [zaświadczenie klucza symetrycznego](concepts-symmetric-key-attestation.md).


## <a name="hardware-security-module"></a>Sprzętowy moduł zabezpieczeń

Sprzętowy moduł zabezpieczeń lub modułu HSM, służy do bezpieczny, sprzętowy magazyn kluczy tajnych urządzenia, a jest to najbezpieczniejsze forma wpisu tajnego magazynu. Tokeny sygnatur dostępu Współdzielonego i certyfikaty X.509 mogą być przechowywane w ramach sprzętowego modułu zabezpieczeń. Moduły HSM mogą być używane przy użyciu zarówno mechanizmów zaświadczania obsługuje inicjowania obsługi administracyjnej.

> [!TIP]
> Zdecydowanie zaleca się bezpieczne przechowywanie wpisów tajnych na urządzeniach, przy użyciu modułu HSM z urządzeniami.

Wpisy tajne urządzenia mogą być również przechowywane w oprogramowaniu (pamięci), ale jest mniej bezpieczna opcja formularz magazynu niż moduł HSM.

## <a name="trusted-platform-module"></a>Moduł TPM

Moduł TPM może odwoływać się do warstwy standardowa w zakresie bezpiecznego magazynowania kluczy używanych do uwierzytelniania platformy lub może odnosić się do interfejsu operacji We/Wy, używane do interakcji z modułów, implementacja standardowej. Moduły TPM może istnieć jako osobne sprzętu, sprzęt zintegrowane, oparte na oprogramowania układowego lub opartych na oprogramowaniu. Dowiedz się więcej o [moduły TPM i modułu TPM zaświadczania](/windows-server/identity/ad-ds/manage/component-updates/tpm-key-attestation). Usługi Device Provisioning obsługuje tylko TPM 2.0.

Zaświadczenia modułu TPM opiera się na wezwanie nonce używa klucze główne poręczenia i magazynu do przedstawienia podpisany token sygnatury dostępu współdzielonego (SAS).

### <a name="endorsement-key"></a>Klucz poręczenia

Klucz poręczenia jest to klucz asymetryczny zawarte wewnątrz modułu TPM, który wewnętrznie generowane lub wprowadzonym w produkcji czasu i jest unikatowy dla każdego modułu TPM. Klucz poręczenia nie można zmienić ani usunąć. Prywatna część klucza poręczenia nigdy nie zwolnieniu poza modułu TPM, gdy publicznej części klucza poręczenia jest używane do rozpoznawania autentyczność modułu TPM. Dowiedz się więcej o [klucz poręczenia](https://technet.microsoft.com/library/cc770443(v=ws.11).aspx).

### <a name="storage-root-key"></a>Klucz głównego magazynu

Klucza głównego magazynowania jest przechowywany w module TPM i służy do ochrony kluczy TPM tworzonych przez aplikacje, dzięki czemu te klucze nie można używać bez modułu TPM. Klucz główny magazynowania jest generowany, gdy przejęcie na własność modułu TPM; Po usunięciu zaznaczenia modułu TPM, dzięki czemu nowy użytkownik może przejąć prawo własności, jest generowany nowy klucz główny magazynowania. Dowiedz się więcej o [klucza głównego magazynowania](https://technet.microsoft.com/library/cc753560(v=ws.11).aspx).

## <a name="x509-certificates"></a>Certyfikaty X.509

Za pomocą certyfikatów X.509 jako mechanizmu zaświadczania jest to doskonały sposób na skalowanie produkcji i uprościć Inicjowanie obsługi administracyjnej urządzeń. Certyfikaty X.509 zwykle są rozmieszczone w łańcuchu certyfikatów, w którym każdy z certyfikatów w łańcuchu jest podpisany przy użyciu klucza prywatnego z następnym wyższym certyfikat i tak dalej, kończenie certyfikatu głównego z podpisem własnym zaufania. Taki układ ustanawia delegowanego łańcucha zaufania od certyfikatu głównego, wygenerowanego przez zaufany główny urząd certyfikacji (CA) w dół za pomocą każdego pośredniego urzędu certyfikacji do certyfikatu "liścia" jednostek końcowych, które są zainstalowane na urządzeniu. Aby dowiedzieć się więcej, zobacz [uwierzytelnianie urządzeń przy użyciu certyfikatu X.509 urzędu certyfikacji](/azure/iot-hub/iot-hub-x509ca-overview). 

Często łańcucha certyfikatów przedstawia niektóre hierarchii logicznych lub fizycznych, skojarzone z urządzenia. Na przykład producent może:
- Wygeneruj certyfikat głównego urzędu certyfikacji z podpisem własnym
- użycie certyfikatu głównego, aby wygenerować unikatowy certyfikat pośredniego urzędu certyfikacji dla każdej fabryki
- Użyj certyfikatu każda fabryka można wygenerować unikatowy certyfikat pośredniego urzędu certyfikacji dla każdej linii produkcyjnej w zakładzie
- a na koniec posługiwać się certyfikatem linii produkcyjnej, aby wygenerować certyfikat unikatowych urządzeń (jednostki końcowej) dla każdego urządzenia wyprodukowane w wierszu. 

Aby dowiedzieć się więcej, zobacz [pojęć związanych z certyfikatów X.509 urzędu certyfikacji w branży IoT](/azure/iot-hub/iot-hub-x509ca-concept). 

### <a name="root-certificate"></a>Certyfikat główny

Certyfikat główny jest reprezentujących urząd certyfikacji (CA) certyfikatu z podpisem własnym X.509. Jest końca lub kotwicy zaufania, łańcucha certyfikatów. Certyfikaty główne można samodzielnie wydany przez organizację lub zakupione od głównego urzędu certyfikacji. Aby dowiedzieć się więcej, zobacz [certyfikatów urzędu certyfikacji X.509 Pobierz](/azure/iot-hub/iot-hub-security-x509-get-started#get-x509-ca-certificates). Certyfikat główny można również określane jako certyfikat głównego urzędu certyfikacji.

### <a name="intermediate-certificate"></a>Certyfikat pośredniego

Pośredniego certyfikatu jest certyfikat X.509, który został podpisany certyfikat główny (lub inny certyfikat pośredniego przy użyciu certyfikatu głównego w łańcuchu). Ostatni certyfikat pośredniego z łańcucha jest używany do podpisywania certyfikatu liścia. Pośredniego certyfikatu może być również określany jako pośredniego urzędu certyfikacji.

### <a name="end-entity-leaf-certificate"></a>Certyfikat "liścia" jednostki końcowej

Certyfikat liścia lub certyfikatów jednostek końcowych identyfikuje właściciela certyfikatu. Ma on certyfikat główny w łańcuchu certyfikatów, a także zero lub więcej certyfikatów pośrednich. Certyfikat liścia nie jest używany do podpisywania innych certyfikatów. Jednoznacznie identyfikuje urządzenie do usługi aprowizacji i jest czasami określane jako certyfikatu tego urządzenia. Podczas uwierzytelniania jest używany klucz prywatny skojarzony z tym certyfikatem odpowiadania na dowód posiadania żądania z usługi.

Certyfikaty używane z liści [rejestracji indywidualnej](./concepts-service.md#individual-enrollment) wpis ma wymagań dotyczących, **nazwy podmiotu** musi być ustawione na identyfikator rejestracji wpis rejestracji indywidualnej. Liści certyfikaty używane z [grupy rejestracji](./concepts-service.md#enrollment-group) wejścia powinny mieć **nazwy podmiotu** Ustaw identyfikator żądanego urządzenia, które zostaną wyświetlone w **rejestracji rekordów** dla uwierzytelnionego urządzenia w grupie rejestracji.

Aby dowiedzieć się więcej, zobacz [uwierzytelniając urządzenia podpisane za pomocą certyfikatów X.509 urzędu certyfikacji](/azure/iot-hub/iot-hub-x509ca-overview#authenticating-devices-signed-with-x509-ca-certificates).

## <a name="controlling-device-access-to-the-provisioning-service-with-x509-certificates"></a>Sterowanie dostępem urządzenia do usługi aprowizacji za pomocą certyfikatów X.509

Usługa aprowizowania udostępnia dwa typy wpis rejestracji, który służy do kontrolowania dostępu urządzeń, które korzystają z mechanizmu zaświadczania X.509:  

- [Rejestracja indywidualna](./concepts-service.md#individual-enrollment) wpisy są skonfigurowane przy użyciu certyfikatu urządzenia skojarzone z określonym urządzeniem. Te wpisy kontroli rejestracji dla określonych urządzeń.
- [Grupy rejestracji](./concepts-service.md#enrollment-group) wpisy są skojarzone z określonym pośredni lub certyfikatu głównego urzędu certyfikacji. Te wpisy kontroli rejestracji dla wszystkich urządzeń, które mają pośredniego lub głównego certyfikatu w swoim łańcuchu certyfikatów. 

Gdy urządzenie nawiązuje połączenie z usługą aprowizowania, usługa priorytetem dokładniejszą wpisów rejestracji za pośrednictwem mniej określonych wpisów rejestracji. Oznacza to jeśli istnieje rejestrację indywidualną dla urządzenia, usługę aprowizacji dotyczy ten wpis. Jeśli ma nie rejestrację indywidualną dla urządzenia, a grupę rejestracji dla pośredniego pierwszy certyfikat w łańcuchu certyfikatów urządzenia istnieje, usługa stosuje ten wpis i tak dalej, łańcuch z głównym. Usługa stosuje pierwszy wpis dotyczy, które znajdzie, tak, aby:

- Jeśli pierwszy wpis rejestracji znaleziono jest włączony, usługa aprowizuje dla urządzenia.
- Jeśli pierwszy wpis rejestracji znaleziono jest wyłączona, usługa nie umożliwia aprowizowania urządzenia.  
- Jeśli żaden wpis rejestracji zostanie znaleziony jakichkolwiek certyfikatów w łańcuchu certyfikatów urządzenia, usługa nie umożliwia aprowizowania urządzenia. 

Ten mechanizm i struktury hierarchiczne łańcuchów certyfikatów można swobodnie zaawansowany sposób kontrolowania dostępu dla poszczególnych urządzeń, a także jak w przypadku grup urządzeń. Na przykład Wyobraź sobie pięć urządzeń za pomocą następujących łańcuchów certyfikatów: 

- *Urządzenia 1*: certyfikat główny -> certyfikat, A -> certyfikat urządzenia 1
- *Urządzenie 2*: certyfikat główny -> certyfikat, A -> certyfikat urządzenia 2
- *Urządzenie 3*: certyfikat główny -> certyfikat, A -> certyfikat urządzenia 3
- *Urządzenia 4*: certyfikat główny -> certyfikat B -> certyfikat urządzenia 4
- *Urządzenie 5*: certyfikat główny -> certyfikat B -> certyfikat urządzenia 5

Początkowo można utworzyć wpis rejestracji pojedynczej grupy włączone dla certyfikatu głównego włączyć dostęp dla wszystkich pięciu urządzeń. Jeśli certyfikat B później zostanie naruszony, można utworzyć wpis grupy rejestracji wyłączone dla certyfikatu B zapobiec *4 urządzenia* i *5 urządzeń* rejestracji. Jeśli nadal nowsze *3 urządzenia* staje się naruszenia zabezpieczeń można utworzyć wpis rejestracji indywidualnej wyłączone dla swojego certyfikatu. To odwołuje dostęp do *3 urządzenia*, ale nadal umożliwia *1 urządzenia* i *2 urządzenia* do zarejestrowania.