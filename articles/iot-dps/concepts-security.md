---
title: Usługa inicjowania obsługi administracyjnej urządzeń usługi Azure IoT Hub — pojęcia dotyczące zabezpieczeń
description: Zawiera opis koncepcji inicjowania obsługi administracyjnej zabezpieczeń specyficznych dla urządzeń z usługą inicjowania obsługi administracyjnej urządzeń (DPS) i centrum IoT Hub
author: nberdy
ms.author: nberdy
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 3191e9886604af9b2a26b71a89cee699197585c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271566"
---
# <a name="iot-hub-device-provisioning-service-security-concepts"></a>Pojęcia zabezpieczeń usługi inicjowania obsługi administracyjnej urządzeń usługi IoT Hub 

Usługa inicjowania obsługi administracyjnej urządzeń w centrum IoT hub jest usługą pomocniczą dla usługi IoT Hub, której używasz do konfigurowania obsługi administracyjnej urządzeń bezdotykowych do określonego centrum IoT Hub. Dzięki usłudze aprowizacji urządzeń można [automatycznie aprowizować](concepts-auto-provisioning.md) miliony urządzeń w bezpieczny i skalowalny sposób. W tym artykule przedstawiono omówienie pojęć *zabezpieczeń* związanych z inicjowania obsługi administracyjnej urządzeń. Ten artykuł jest odpowiedni dla wszystkich personas zaangażowanych w przygotowanie urządzenia do wdrożenia.

## <a name="attestation-mechanism"></a>Mechanizm zaświadczania

Mechanizm zaświadczania jest metodą używaną do potwierdzania tożsamości urządzenia. Mechanizm zaświadczania jest również odpowiedni dla listy rejestracji, która informuje usługę inicjowania obsługi administracyjnej, która metoda zaświadczania ma być używana z danym urządzeniem.

> [!NOTE]
> Usługa IoT Hub używa "schematu uwierzytelniania" dla podobnej koncepcji w tej usłudze.

Usługa inicjowania obsługi urządzeń obsługuje następujące formy zaświadczania:
* **Certyfikaty X.509** oparte na standardowym przepływie uwierzytelniania certyfikatów X.509.
* **Moduł TPM (Trusted Platform Module)** oparty na wyzwaniu typu nonce przy użyciu standardu modułu TPM dla kluczy do prezentowania podpisanego tokenu sygnatury dostępu współdzielonego (SAS). Ta forma zaświadczania nie wymaga fizycznego modułu TPM na urządzeniu, ale usługa oczekuje, że będzie testować przy użyciu klucza poręczenia zgodnie [ze specyfikacją modułu TPM.](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/)
* **Klucz symetryczny** oparty na [tokenach zabezpieczających](../iot-hub/iot-hub-devguide-security.md#security-tokens)podpisu dostępu współdzielonego (SAS), które zawierają podpis skrótowy i osadzony wygaśnięcie. Aby uzyskać więcej informacji, zobacz [zaświadczenie klucza symetrycznego](concepts-symmetric-key-attestation.md).


## <a name="hardware-security-module"></a>Sprzętowy moduł zabezpieczeń

Sprzętowy moduł zabezpieczeń (HSM) służy do bezpiecznego, sprzętowego przechowywania wpisów tajnych urządzeń i jest najbezpieczniejszą formą tajnego magazynu. Zarówno certyfikaty X.509, jak i tokeny sygnatury dostępu Współdzielonego mogą być przechowywane w modułie HSM. Moduły HSM mogą być używane z obu mechanizmów zaświadczania obsługi inicjowania obsługi administracyjnej.

> [!TIP]
> Zdecydowanie zaleca się używanie modułu HSM z urządzeniami do bezpiecznego przechowywania wpisów tajnych na urządzeniach.

Wpisy tajne urządzenia mogą być również przechowywane w oprogramowaniu (pamięci), ale jest to mniej bezpieczna forma magazynu niż moduł HSM.

## <a name="trusted-platform-module"></a>Moduł TPM

Moduł TPM może odwoływać się do standardu bezpiecznego przechowywania kluczy używanych do uwierzytelniania platformy lub może odwoływać się do interfejsu we/wy używanego do interakcji z modułami implementującymi standard. Moduły TPM mogą istnieć jako sprzęt dyskretny, zintegrowany sprzęt, oprogramowanie układowe lub oparte na oprogramowaniu. Dowiedz się więcej o [tpm i tpm zaświadczania](/windows-server/identity/ad-ds/manage/component-updates/tpm-key-attestation). Usługa inicjowania obsługi administracyjnej urządzeń obsługuje tylko moduł TPM 2.0.

Zaświadczanie modułu TPM jest oparte na wyzwaniu nonce, które używa kluczy głównych poręczenia i magazynu do przedstawienia podpisanego tokenu sygnatury dostępu współdzielonego (SAS).

### <a name="endorsement-key"></a>Klucz poparcia

Klucz poręczenia jest kluczem asymetrycznym zawartym wewnątrz modułu TPM, który został wygenerowany wewnętrznie lub wstrzyknięty w czasie produkcji i jest unikatowy dla każdego modułu TPM. Nie można zmienić ani usunąć klucza potwierdzenia. Prywatna część klucza poręczenia nigdy nie jest zwalniana poza modułem TPM, podczas gdy publiczna część klucza poręczenia jest używana do rozpoznawania oryginalnego modułu TPM. Dowiedz się więcej o [kluczu poparcia](https://technet.microsoft.com/library/cc770443(v=ws.11).aspx).

### <a name="storage-root-key"></a>Klucz główny magazynu

Klucz główny magazynu jest przechowywany w modułie TPM i służy do ochrony kluczy modułu TPM utworzonych przez aplikacje, dzięki czemu klucze te nie mogą być używane bez modułu TPM. Klucz główny magazynu jest generowany po przejęciu na własność modułu TPM; po wyczyszczeniu modułu TPM, aby nowy użytkownik mógł przejąć na własność nowy klucz główny magazynu. Dowiedz się więcej o [kluczu głównym magazynu](https://technet.microsoft.com/library/cc753560(v=ws.11).aspx).

## <a name="x509-certificates"></a>Certyfikaty X.509

Używanie certyfikatów X.509 jako mechanizmu zaświadczania jest doskonałym sposobem skalowania produkcji i upraszczania inicjowania obsługi administracyjnej urządzeń. Certyfikaty X.509 są zazwyczaj rozmieszczone w łańcuchu zaufania certyfikatów, w którym każdy certyfikat w łańcuchu jest podpisywana kluczem prywatnym następnego wyższego certyfikatu itd., kończąc certyfikat główny z podpisem własnym. To rozwiązanie ustanawia delegowany łańcuch zaufania z certyfikatu głównego wygenerowanego przez zaufany główny urząd certyfikacji (CA) w dół za pośrednictwem każdego pośredniego urzędu certyfikacji do certyfikatu "liścia" jednostki końcowej zainstalowanego na urządzeniu. Aby dowiedzieć się więcej, zobacz [Uwierzytelnianie urządzeń przy użyciu certyfikatów urzędu certyfikacji X.509](/azure/iot-hub/iot-hub-x509ca-overview). 

Często łańcuch certyfikatów reprezentuje pewną hierarchię logiczną lub fizyczną skojarzoną z urządzeniami. Na przykład producent może:
- wystawianie certyfikatu głównego urzędu certyfikacji z podpisem własnym
- użyj certyfikatu głównego do wygenerowania unikatowego pośredniego certyfikatu urzędu certyfikacji dla każdej fabryki
- użyj certyfikatu każdej fabryki do wygenerowania unikalnego pośredniego certyfikatu urzędu certyfikacji dla każdej linii produkcyjnej w zakładzie
- i na koniec użyj certyfikatu linii produkcyjnej, aby wygenerować unikatowy certyfikat urządzenia (jednostki końcowej) dla każdego urządzenia wyprodukowanego w wierszu. 

Aby dowiedzieć się więcej, zobacz [Koncepcyjne zrozumienie certyfikatów X.509 urzędu certyfikacji w branży IoT](/azure/iot-hub/iot-hub-x509ca-concept). 

### <a name="root-certificate"></a>Certyfikat główny

Certyfikat główny jest certyfikatem X.509 z podpisem własnym reprezentującym urząd certyfikacji(CA). Jest to punkt końcowy lub kotwica zaufania łańcucha certyfikatów. Certyfikaty główne mogą być wystawiane samodzielnie przez organizację lub kupowane w głównym urzędzie certyfikacji. Aby dowiedzieć się więcej, zobacz [Pobierz certyfikaty urzędu certyfikacji X.509](/azure/iot-hub/iot-hub-security-x509-get-started#get-x509-ca-certificates). Certyfikat główny można również nazwać certyfikatem głównego urzędu certyfikacji.

### <a name="intermediate-certificate"></a>Certyfikat pośredni

Certyfikat pośredni to certyfikat X.509, który został podpisany przez certyfikat główny (lub przez inny certyfikat pośredni z certyfikatem głównym w łańcuchu). Ostatni certyfikat pośredni w łańcuchu jest używany do podpisywania certyfikatu liścia. Certyfikat pośredni można również nazwać certyfikatem pośredniego urzędu certyfikacji.

### <a name="end-entity-leaf-certificate"></a>Certyfikat "liścia" jednostki końcowej

Certyfikat typu liść lub certyfikat jednostki końcowej identyfikuje posiadacza certyfikatu. Ma certyfikat główny w łańcuchu certyfikatów, a także zero lub więcej certyfikatów pośrednich. Certyfikat typu liść nie jest używany do podpisywania innych certyfikatów. Jednoznacznie identyfikuje urządzenie do usługi inicjowania obsługi administracyjnej i jest czasami określane jako certyfikat urządzenia. Podczas uwierzytelniania urządzenie używa klucza prywatnego skojarzonego z tym certyfikatem, aby odpowiedzieć na wyzwanie dowodu posiadania z usługi.

Certyfikaty typu liść używane z indywidualnym wpisem [rejestracji](./concepts-service.md#individual-enrollment) mają wymóg, aby **nazwa podmiotu** była ustawiona na identyfikator rejestracji wpisu rejestracji indywidualnej. Certyfikaty typu [Liść](./concepts-service.md#enrollment-group) używane z wpisem grupy Rejestracji powinny mieć **ustawioną nazwę podmiotu** na żądany identyfikator urządzenia, który będzie wyświetlany w **rekordach rejestracji** uwierzytelnionego urządzenia w grupie rejestracji.

Aby dowiedzieć się więcej, zobacz [Uwierzytelnianie urządzeń podpisanych certyfikatami urzędu certyfikacji X.509](/azure/iot-hub/iot-hub-x509ca-overview#authenticating-devices-signed-with-x509-ca-certificates).

## <a name="controlling-device-access-to-the-provisioning-service-with-x509-certificates"></a>Kontrolowanie dostępu urządzenia do usługi inicjowania obsługi administracyjnej za pomocą certyfikatów X.509

Usługa inicjowania obsługi administracyjnej udostępnia dwa typy wpisu rejestracji, których można użyć do kontrolowania dostępu dla urządzeń korzystających z mechanizmu zaświadczania X.509:  

- [Poszczególne](./concepts-service.md#individual-enrollment) wpisy rejestracji są konfigurowane z certyfikatem urządzenia skojarzonym z określonym urządzeniem. Te wpisy kontrolują rejestracje dla określonych urządzeń.
- [Wpisy grupy rejestracji](./concepts-service.md#enrollment-group) są skojarzone z określonym certyfikatem pośredniego lub głównego urzędu certyfikacji. Te wpisy kontrolują rejestracje dla wszystkich urządzeń, które mają ten certyfikat pośredni lub główny w łańcuchu certyfikatów. 

Gdy urządzenie łączy się z usługą inicjowania obsługi administracyjnej, usługa nadaje priorytet bardziej szczegółowym wpisom rejestracji w stosunku do mniej określonych wpisów rejestracji. Oznacza to, że jeśli istnieje rejestracja indywidualna dla urządzenia, usługa inicjowania obsługi administracyjnej stosuje ten wpis. Jeśli nie istnieje żadna indywidualna rejestracja dla urządzenia i grupy rejestracji dla pierwszego certyfikatu pośredniego w łańcuchu certyfikatów urządzenia, usługa stosuje ten wpis i tak dalej, w górę łańcucha do katalogu głównego. Usługa stosuje pierwszy obowiązujący wpis, który stwierdzi, w taki sposób, że:

- Jeśli pierwszy znaleziony wpis rejestracji jest włączony, usługa aprobuje urządzenie.
- Jeśli pierwszy znaleziony wpis rejestracji jest wyłączony, usługa nie aprowizuje urządzenia.  
- Jeśli nie zostanie znaleziony żaden wpis rejestracji dla któregokolwiek z certyfikatów w łańcuchu certyfikatów urządzenia, usługa nie aprowizuje urządzenia. 

Ten mechanizm i hierarchiczna struktura łańcuchów certyfikatów zapewnia wysoką elastyczność w kontrolowaniu dostępu dla poszczególnych urządzeń, a także dla grup urządzeń. Wyobraźmy sobie na przykład pięć urządzeń z następującymi łańcuchami certyfikatów: 

- *Urządzenie 1*: certyfikat główny -> certyfikat A -> certyfikat urządzenia 1
- *Urządzenie 2*: certyfikat główny -> certyfikat Certyfikat > urządzenia 2
- *Urządzenie 3*: certyfikat główny -> certyfikat A -> certyfikat urządzenia 3
- *Urządzenie 4*: certyfikat główny -> certyfikat B -> certyfikat urządzenia 4
- *Urządzenie 5*: certyfikat główny -> certyfikat B -> certyfikat urządzenia 5

Początkowo można utworzyć pojedynczy włączony wpis rejestracji grupy dla certyfikatu głównego, aby włączyć dostęp dla wszystkich pięciu urządzeń. Jeśli certyfikat B zostanie później naruszony, można utworzyć wyłączony wpis grupy rejestracji dla certyfikatu B, aby zapobiec rejestrowaniu *urządzeń 4* i *urządzenia 5.* Jeśli jeszcze później *urządzenie 3* zostanie naruszone, można utworzyć wyłączony indywidualny wpis rejestracji dla jego certyfikatu. Spowoduje to odwołanie dostępu do *urządzenia 3,* ale nadal umożliwia rejestrowanie *urządzeń 1* i *urządzenia 2.*