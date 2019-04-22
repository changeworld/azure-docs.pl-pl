---
title: Punkty końcowe zabezpieczeń, w IoT Device Provisioning Service | Dokumentacja firmy Microsoft
description: Pojęcia — jak kontrolować dostęp do usługi IoT Device Provisioning Service dla aplikacji zaplecza. Zawiera informacje na temat tokenów zabezpieczających.
author: wesmc7777
manager: philmea
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: wesmc
ms.openlocfilehash: 7ff622ceac9c49eda7ba6bca1a8bb3aaabccb816
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59495434"
---
# <a name="control-access-to-azure-iot-hub-device-provisioning-service"></a>Kontrola dostępu do usługi Azure IoT Hub Device Provisioning Service

W tym artykule opisano opcje dotyczące zabezpieczania usługi IoT Device Provisioning. Używane przez usługę aprowizacji *uprawnienia* do udzielania dostępu do każdego punktu końcowego. Uprawnienia ograniczają dostęp do wystąpienia usługi, w oparciu o funkcjonalność.

W tym artykule opisano:

* Różne uprawnienia, można udzielić aplikacji zaplecza, uzyskiwać dostęp do usługi aprowizacji.
* Proces uwierzytelniania i tokeny, których ona używa, aby sprawdzić uprawnienia.

### <a name="when-to-use"></a>Kiedy stosować

Musi mieć odpowiednie uprawnienia dostępu do żadnego inicjowania obsługi administracyjnej punktów końcowych usługi. Na przykład aplikacja wewnętrznej bazy danych musi zawierać token zawierający poświadczenia zabezpieczeń wraz z każdej wiadomości wysyłanych do usługi.

## <a name="access-control-and-permissions"></a>Kontrola dostępu i uprawnień

Możesz nadawać [uprawnienia](#device-provisioning-service-permissions) w następujący sposób:

* **Udostępnione zasady autoryzacji dostępu**. Zasady dostępu współdzielonego może nadać dowolną kombinację [uprawnienia](#device-provisioning-service-permissions). Można zdefiniować zasady w [witryny Azure portal][lnk-management-portal], lub programowo przy użyciu [interfejsów API REST usługi urządzenia aprowizacji][lnk-resource-provider-apis]. Nowo utworzonej usługi aprowizacji ma następujące domyślne zasady:

* **provisioningserviceowner**: Zasady z wszystkie uprawnienia.

> [!NOTE]
> Zobacz [uprawnienia](#device-provisioning-service-permissions) Aby uzyskać szczegółowe informacje.

## <a name="authentication"></a>Authentication

Usługa Azure IoT Hub Device Provisioning Service udziela dostępu do punktów końcowych, sprawdzając token względem zasad dostępu współdzielonego. Poświadczenia zabezpieczeń, takie jak klucze symetryczne, nigdy nie są przesyłane przez sieć.

> [!NOTE]
> Dostawcy zasobów usługi Device Provisioning jest zabezpieczony za pomocą subskrypcji platformy Azure, ponieważ wszyscy dostawcy w [usługi Azure Resource Manager][lnk-azure-resource-manager].

Aby uzyskać więcej informacji dotyczących sposobu tworzenia i używania tokenów zabezpieczających zobacz następną sekcję.

Protokół HTTP jest jedynym protokołem obsługiwanych i implementuje uwierzytelniania umieszczając prawidłowy token w **autoryzacji** nagłówek żądania.

#### <a name="example"></a>Przykład
```csharp
SharedAccessSignature sr = 
   mydps.azure-devices-provisioning.net&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501&skn=provisioningserviceowner`\
```

> [!NOTE]
> [Azure inicjowania obsługi usługi zestawy SDK urządzeń IoT] [ lnk-sdks] automatycznie generować tokeny podczas nawiązywania połączenia z usługą.

## <a name="security-tokens"></a>Tokeny zabezpieczające

Usługi Device Provisioning Service używa tokenów zabezpieczających do uwierzytelniania usługi, aby unikać wysyłania kluczy na potrzeby przesyłu. Ponadto tokeny zabezpieczające są ograniczony czas ważności i zakresu. [Usługa Azure inicjowania obsługi usługi zestawy SDK urządzeń IoT] [ lnk-sdks] automatycznego generowania tokenów bez konieczności żadnej specjalnej konfiguracji. Niektóre scenariusze wymagają do generowania i używania tokenów zabezpieczających bezpośrednio. Takie scenariusze obejmują bezpośredniemu wykorzystaniu powierzchni HTTP.

### <a name="security-token-structure"></a>Struktura tokenu zabezpieczeń

Użyjesz tokenów zabezpieczających do udzielania dostępu ograniczone czasowo dla usług do określonych funkcji w IoT Device Provisioning Service. Aby uzyskać zezwolenie na łączenie się z usługą aprowizacji, usługi wysłanie tokenów zabezpieczających podpisany przy użyciu dostępu współdzielonego lub klucza symetrycznego.

Token jest podpisany przy użyciu dostępu do klucza udziela dostępu współdzielonego do wszystkich funkcji, które są skojarzone z uprawnienia zasad dostępu współdzielonego. 

Token zabezpieczający ma następujący format:

`SharedAccessSignature sig={signature}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

Oto oczekiwane wartości:

| Wartość | Opis |
| --- | --- |
| {Sygnatura} |Ciąg sygnatury HMAC SHA256 w postaci: `{URL-encoded-resourceURI} + "\n" + expiry`. **Ważne**: Klucz jest zdekodować z formatu base64 i użyć go jako klucza do wykonywania obliczeń HMAC SHA256.|
| {expiry} |Ciągi UTF8 liczba sekund od epoki 00:00:00 czasu UTC na 1 stycznia 1970. |
| {URL-encoded-resourceURI} | Małe zamierzone, Zapisz kodowania adresu URL identyfikator URI zasobu małymi literami. Prefiks identyfikatora URI (według segmentu) punktów końcowych, które mogą być udostępniane z tym tokenem, rozpoczynając od nazwy hosta IoT Device Provisioning Service (nie protocol). Na przykład `mydps.azure-devices-provisioning.net`. |
| {policyName} |Nazwa zasad dostępu współdzielonego, do którego odwołuje się ten token. |

**Uwaga dotycząca prefiks**: Prefiks identyfikatora URI jest obliczany według segmentu i nie według znaków. Na przykład `/a/b` był prefiksem dla `/a/b/c` , ale nie dla `/a/bc`.

Poniższy fragment kodu Node.js zawiera funkcję o nazwie **generateSasToken** , oblicza token z danych wejściowych `resourceUri, signingKey, policyName, expiresInMins`. Kolejne sekcje zawierają szczegółowe instrukcje dotyczące inicjowanie różne dane wejściowe dla różnych zastosowań tokenu.

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

Porównanie jest równoważny kod języka Python w celu wygenerowania tokenu zabezpieczającego:

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
> Ponieważ czas ważności tokenu jest sprawdzana na maszynach IoT Device Provisioning Service, dryfu zegara komputera, która generuje token musi być minimalny.

### <a name="use-security-tokens-from-service-components"></a>Używanie tokenów zabezpieczeń ze składników usługi

Składniki usługi może generować jedynie tokeny zabezpieczające, za pomocą zasad dostępu współdzielonego przyznawanie odpowiednich uprawnień, jak wyjaśniono wcześniej.

Poniżej przedstawiono funkcje usługi dostępne w punktach końcowych:

| Endpoint | Funkcjonalność |
| --- | --- |
| `{your-service}.azure-devices-provisioning.net/enrollments` |Udostępnia operacje rejestracji urządzeń z usługą Device Provisioning. |
| `{your-service}.azure-devices-provisioning.net/enrollmentGroups` |Udostępnia operacje do zarządzania grupami rejestracji urządzenia. |
| `{your-service}.azure-devices-provisioning.net/registrations/{id}` |Udostępnia operacje do pobierania i zarządzania nimi stanu rejestracji urządzeń. |


Na przykład usługa wygenerowany za pomocą wstępnie utworzonych współużytkowane zasady dostępu o nazwie **enrollmentread** spowodowałoby utworzenie tokenu z następującymi parametrami:

* Identyfikator URI zasobu: `{mydps}.azure-devices-provisioning.net`,
* klucz podpisywania: jeden z kluczy `enrollmentread` zasad
* Nazwa zasady: `enrollmentread`,
* wszelkie time.backn wygaśnięcia

![Tworzenie zasad dostępu współdzielonego danego wystąpienia usługi Device Provisioning w witrynie portal][img-add-shared-access-policy]

```javascript
var endpoint ="mydps.azure-devices-provisioning.net";
var policyName = 'enrollmentread'; 
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

Wynik, którym przyznano dostęp do odczytu wszystkich rekordów rejestracji, będzie:

`SharedAccessSignature sr=mydps.azure-devices-provisioning.net&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=enrollmentread`

## <a name="reference-topics"></a>Tematy referencyjne:

W poniższych tematach odwołania udostępnić więcej informacji na temat kontrolowania dostępu do usługi IoT Device Provisioning Service.

### <a name="device-provisioning-service-permissions"></a>Uprawnienia usługi aprowizacji urządzeń

W poniższej tabeli wymieniono uprawnienia, których można użyć do kontrolowania dostępu do usługi IoT Device Provisioning Service.

| Uprawnienie | Uwagi |
| --- | --- |
| **ServiceConfig** |Przyznaje dostęp do zmiany konfiguracji usługi. <br/>To uprawnienie jest używany przez usług zaplecza w chmurze. |
| **EnrollmentRead** |Przyznaje dostęp do rejestracji urządzeń i grup rejestracji. <br/>To uprawnienie jest używany przez usług zaplecza w chmurze. |
| **EnrollmentWrite** |Przyznaje dostęp do zapisu są do rejestracji urządzeń i grup rejestracji. <br/>To uprawnienie jest używany przez usług zaplecza w chmurze. |
| **RegistrationStatusRead** |Przyznaje dostęp do stanu rejestracji urządzenia. <br/>To uprawnienie jest używany przez usług zaplecza w chmurze. |
| **RegistrationStatusWrite**  |Przyznaje usunąć dostęp do stanu rejestracji urządzenia. <br/>To uprawnienie jest używany przez usług zaplecza w chmurze. |

<!-- links and images -->

[img-add-shared-access-policy]: ./media/how-to-control-access/how-to-add-shared-access-policy.PNG
[lnk-sdks]: ../iot-hub/iot-hub-devguide-sdks.md
[lnk-management-portal]: https://portal.azure.com
[lnk-azure-resource-manager]: ../azure-resource-manager/resource-group-overview.md
[lnk-resource-provider-apis]: https://docs.microsoft.com/rest/api/iot-dps/
