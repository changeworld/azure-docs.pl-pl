---
title: Punkty końcowe zabezpieczeń w usłudze inicjowania obsługi administracyjnej urządzeń IoT | Dokumenty firmy Microsoft
description: Pojęcia — jak kontrolować dostęp do usługi inicjowania obsługi administracyjnej urządzeń IoT (DPS) dla aplikacji zaplecza. Zawiera informacje o tokenach zabezpieczających.
author: wesmc7777
manager: philmea
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: wesmc
ms.openlocfilehash: 2a7e0932d226b1533c039b8529c2c11de06cf525
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79285151"
---
# <a name="control-access-to-azure-iot-hub-device-provisioning-service"></a>Kontrolowanie dostępu do usługi inicjowania obsługi administracyjnej urządzeń usługi Usługi Azure IoT Hub

W tym artykule opisano opcje zabezpieczania usługi inicjowania obsługi administracyjnej urządzeń IoT. Usługa inicjowania obsługi administracyjnej używa *uprawnień* do udzielania dostępu do każdego punktu końcowego. Uprawnienia ograniczają dostęp do wystąpienia usługi na podstawie funkcji.

W tym artykule opisano:

* Różne uprawnienia, które można udzielić do wewnętrznej bazy danych aplikacji, aby uzyskać dostęp do usługi inicjowania obsługi administracyjnej.
* Proces uwierzytelniania i tokeny używane do weryfikacji uprawnień.

### <a name="when-to-use"></a>Kiedy stosować

Musisz mieć odpowiednie uprawnienia, aby uzyskać dostęp do dowolnego punktu końcowego usługi inicjowania obsługi administracyjnej. Na przykład aplikacja wewnętrznej bazy danych musi zawierać token zawierający poświadczenia zabezpieczeń wraz z każdą wiadomością, którą wysyła do usługi.

## <a name="access-control-and-permissions"></a>Kontrola dostępu i uprawnienia

[Uprawnienia](#device-provisioning-service-permissions) można przyznać w następujący sposób:

* **Zasady autoryzacji dostępu współdzielonego**. Zasady dostępu współdzielonego mogą przyznawać dowolną [kombinację uprawnień](#device-provisioning-service-permissions). Zasady można definiować w [witrynie Azure portal][lnk-management-portal]lub programowo przy użyciu [interfejsów API REST usługi inicjowania obsługi administracyjnej urządzeń][lnk-resource-provider-apis]. Nowo utworzona usługa inicjowania obsługi administracyjnej ma następujące zasady domyślne:

* **inicjująca usługi właściciel:** Zasady ze wszystkimi uprawnieniami.

> [!NOTE]
> Szczegółowe informacje można znaleźć w [uprawnieniach.](#device-provisioning-service-permissions)

## <a name="authentication"></a>Uwierzytelnianie

Usługa inicjowania obsługi administracyjnej urządzeń usługi Azure IoT Hub udziela dostępu do punktów końcowych, weryfikując token względem zasad dostępu udostępnionego. Poświadczenia zabezpieczeń, takie jak klucze symetryczne, nigdy nie są wysyłane za cały okres.

> [!NOTE]
> Dostawca zasobów usługi inicjowania obsługi urządzeń jest zabezpieczony za pośrednictwem subskrypcji platformy Azure, podobnie jak wszyscy dostawcy w [usłudze Azure Resource Manager][lnk-azure-resource-manager].

Aby uzyskać więcej informacji na temat konstruowania i używania tokenów zabezpieczających, zobacz następną sekcję.

PROTOKÓŁ HTTP jest jedynym obsługiwanym protokołem i implementuje uwierzytelnianie przez dołączenie prawidłowego tokenu w nagłówku żądania **autoryzacji.**

#### <a name="example"></a>Przykład
```csharp
SharedAccessSignature sr = 
   mydps.azure-devices-provisioning.net&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501&skn=provisioningserviceowner`\
```

> [!NOTE]
> ZestawY [SDK usługi inicjowania obsługi administracyjnej urządzeń Azure IoT][lnk-sdks] automatycznie generują tokeny podczas łączenia się z usługą.

## <a name="security-tokens"></a>Tokeny zabezpieczające

Usługa inicjowania obsługi administracyjnej urządzeń używa tokenów zabezpieczających do uwierzytelniania usług, aby uniknąć wysyłania kluczy w sieci. Ponadto tokeny zabezpieczające są ograniczone w czasie ważności i zakresu. [Zestaw SDK usługi inicjowania obsługi administracyjnej urządzeń Azure IoT][lnk-sdks] automatycznie generuje tokeny bez konieczności konieczności specjalnej konfiguracji. Niektóre scenariusze wymagają bezpośredniego generowania i używania tokenów zabezpieczających. Takie scenariusze obejmują bezpośrednie wykorzystanie powierzchni HTTP.

### <a name="security-token-structure"></a>Struktura tokenów zabezpieczających

Tokeny zabezpieczające są używane do udzielania ograniczonego czasowo dostępu do usług do określonych funkcji usługi inicjowania obsługi administracyjnej urządzeń IoT. Aby uzyskać autoryzację do łączenia się z usługą inicjowania obsługi administracyjnej, usługi muszą wysyłać tokeny zabezpieczające podpisane przy pomocy klucza udostępnionego lub symetrycznego.

Token podpisany przy za pomocą klucza dostępu udostępnionego udziela dostępu do wszystkich funkcji skojarzonych z uprawnieniami zasad dostępu współdzielonego. 

Token zabezpieczający ma następujący format:

`SharedAccessSignature sig={signature}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

Oto oczekiwane wartości:

| Wartość | Opis |
| --- | --- |
| {podpis} |Ciąg podpisu HMAC-SHA256 formularza: `{URL-encoded-resourceURI} + "\n" + expiry`. **Ważne:** Klucz jest dekodowany z base64 i używany jako klucz do wykonywania obliczeń HMAC-SHA256.|
| {wygaśnięcie} |Ciągi UTF8 przez kilka sekund od 00:00:00 UTC w dniu 1 stycznia 1970. |
| {IdentyfikatorURI zakodowany w adresie URL} | Małe wielkości kodowania adresów URL mniejszego identyfikatora URI zasobu wielkości liter. Prefiks identyfikatora URI (według segmentu) punktów końcowych, do których można uzyskać dostęp za pomocą tego tokenu, począwszy od nazwy hosta usługi inicjowania obsługi administracyjnej urządzeń IoT (bez protokołu). Na przykład `mydps.azure-devices-provisioning.net`. |
| {nazwa_polityki} |Nazwa zasady dostępu współdzielonego, do której odnosi się ten token. |

**Uwaga dotycząca prefiksu:** Prefiks identyfikatora URI jest obliczany według segmentu, a nie według znaku. Na `/a/b` przykład jest prefiksem dla `/a/b/c` , ale nie dla `/a/bc`.

Poniższy fragment kodu Node.js pokazuje funkcję o nazwie **generateSasToken,** która `resourceUri, signingKey, policyName, expiresInMins`oblicza token z danych wejściowych . W następnych sekcjach szczegółowo opisano, jak zainicjować różne dane wejściowe dla różnych przypadków użycia tokenu.

```javascript
var generateSasToken = function(resourceUri, signingKey, policyName, expiresInMins) {
    resourceUri = encodeURIComponent(resourceUri);

    // Set expiration in seconds
    var expires = (Date.now() / 1000) + expiresInMins * 60;
    expires = Math.ceil(expires);
    var toSign = resourceUri + '\n' + expires;

    // Use crypto
    var hmac = crypto.createHmac('sha256', new Buffer(signingKey, 'base64'));
    hmac.update(toSign);
    var base64UriEncoded = encodeURIComponent(hmac.digest('base64'));

    // Construct authorization string
    var token = "SharedAccessSignature sr=" + resourceUri + "&sig="
    + base64UriEncoded + "&se=" + expires + "&skn="+ policyName;
    return token;
};
```

Dla porównania, równoważny kod języka Python do generowania tokenu zabezpieczającego jest:

```python
from base64 import b64encode, b64decode
from hashlib import sha256
from time import time
from urllib import quote_plus, urlencode
from hmac import HMAC

def generate_sas_token(uri, key, policy_name, expiry=3600):
    ttl = time() + expiry
    sign_key = "%s\n%d" % ((quote_plus(uri)), int(ttl))
    print sign_key
    signature = b64encode(HMAC(b64decode(key), sign_key, sha256).digest())

    rawtoken = {
        'sr' :  uri,
        'sig': signature,
        'se' : str(int(ttl)),
        'skn' : policy_name
    }

    return 'SharedAccessSignature ' + urlencode(rawtoken)
```

> [!NOTE]
> Ponieważ ważność czasu tokenu jest sprawdzana na komputerach usługi inicjowania obsługi administracyjnej urządzeń IoT, dryf na zegarze komputera, który generuje token musi być minimalny.

### <a name="use-security-tokens-from-service-components"></a>Używanie tokenów zabezpieczających ze składników usługi

Składniki usługi można wygenerować tylko tokeny zabezpieczające przy użyciu zasad dostępu współdzielonego, przyznając odpowiednie uprawnienia, jak wyjaśniono wcześniej.

Oto funkcje usługi udostępniane w punktach końcowych:

| Endpoint | Funkcjonalność |
| --- | --- |
| `{your-service}.azure-devices-provisioning.net/enrollments` |Udostępnia operacje rejestracji urządzeń za pomocą usługi inicjowania obsługi administracyjnej urządzeń. |
| `{your-service}.azure-devices-provisioning.net/enrollmentGroups` |Zapewnia operacje zarządzania grupami rejestracji urządzeń. |
| `{your-service}.azure-devices-provisioning.net/registrations/{id}` |Zapewnia operacje pobierania i zarządzania stanem rejestracji urządzeń. |


Na przykład usługa generowana przy użyciu wstępnie utworzonej zasady dostępu współdzielonego o nazwie **enrollmentread** utworzy token z następującymi parametrami:

* identyfikator URI `{mydps}.azure-devices-provisioning.net`zasobu: ,
* klucz do podpisywania: `enrollmentread` jeden z kluczy polityki,
* nazwa zasad: `enrollmentread`,
* dowolny czas wygaśnięcia.backn

![Tworzenie zasad dostępu współdzielonego dla wystąpienia usługi inicjowania obsługi administracyjnej urządzeń w portalu][img-add-shared-access-policy]

```javascript
var endpoint ="mydps.azure-devices-provisioning.net";
var policyName = 'enrollmentread'; 
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

Wynik, który zapewni dostęp do odczytu wszystkich rekordów rejestracji, będzie:

`SharedAccessSignature sr=mydps.azure-devices-provisioning.net&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=enrollmentread`

## <a name="reference-topics"></a>Tematy referencyjne:

Poniższe tematy referencyjne zawierają więcej informacji na temat kontrolowania dostępu do usługi inicjowania obsługi administracyjnej urządzeń IoT.

### <a name="device-provisioning-service-permissions"></a>Uprawnienia usługi inicjowania obsługi administracyjnej urządzeń

W poniższej tabeli wymieniono uprawnienia, za pomocą których można kontrolować dostęp do usługi inicjowania obsługi administracyjnej urządzeń IoT.

| Uprawnienie | Uwagi |
| --- | --- |
| **Serviceconfig** |Udziela dostępu do zmiany konfiguracji usługi. <br/>To uprawnienie jest używane przez usługi w chmurze wewnętrznej bazy danych. |
| **EnrollmentRead (Odczyt rejestracji)** |Udziela dostępu do odczytu do rejestracji urządzeń i grup rejestracji. <br/>To uprawnienie jest używane przez usługi w chmurze wewnętrznej bazy danych. |
| **RejestracjaWrite** |Udziela dostępu do zapisu do rejestracji urządzeń i grup rejestracji. <br/>To uprawnienie jest używane przez usługi w chmurze wewnętrznej bazy danych. |
| **RegistrationStatusCzysz** |Udziela dostępu do odczytu do stanu rejestracji urządzenia. <br/>To uprawnienie jest używane przez usługi w chmurze wewnętrznej bazy danych. |
| **RejestracjaStatusWrite**  |Udziela dostępu do usuwania stanu rejestracji urządzenia. <br/>To uprawnienie jest używane przez usługi w chmurze wewnętrznej bazy danych. |

<!-- links and images -->

[img-add-shared-access-policy]: ./media/how-to-control-access/how-to-add-shared-access-policy.PNG
[lnk-sdks]: ../iot-hub/iot-hub-devguide-sdks.md
[lnk-management-portal]: https://portal.azure.com
[lnk-azure-resource-manager]: ../azure-resource-manager/management/overview.md
[lnk-resource-provider-apis]: https://docs.microsoft.com/rest/api/iot-dps/
