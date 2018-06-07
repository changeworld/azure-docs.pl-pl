---
title: Punkty końcowe zabezpieczeń w usłudze udostępniania urządzenia IoT | Dokumentacja firmy Microsoft
description: Pojęcia — kontrola dostępu do usługi udostępniania urządzenia IoT dla aplikacji zaplecza. Zawiera informacje na temat tokenów zabezpieczających.
author: dsk-2015
manager: timlt
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 09/28/2017
ms.author: dkshir
ms.openlocfilehash: e33f6b61f757c9d7f6a773141ad0deea363be2b7
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34629392"
---
# <a name="control-access-to-azure-iot-hub-device-provisioning-service"></a>Kontrola dostępu do usługi inicjowania obsługi urządzeń Centrum IoT Azure

W tym artykule opisano opcje dotyczące zabezpieczania usługi inicjowania obsługi administracyjnej urządzeniu IoT. Używane przez usługę inicjowania obsługi administracyjnej *uprawnienia* uzyskać dostęp do każdego punktu końcowego. Uprawnienia ograniczyć dostęp do wystąpienia usługi, w oparciu o funkcje.

W tym artykule opisano:

* Inne uprawnienia można przyznać aplikacji zaplecza, do uzyskania dostępu do inicjowania obsługi usługi.
* Proces uwierzytelniania i tokenów używanych do Sprawdź uprawnienia.

### <a name="when-to-use"></a>Kiedy stosować

Musi mieć odpowiednie uprawnienia do uzyskania dostępu udostępniania punktów końcowych usługi. Na przykład aplikacji zaplecza musi zawierać token zawierający poświadczenia zabezpieczeń, wraz z każdej wiadomości wysyłanych do usługi.

## <a name="access-control-and-permissions"></a>Kontrola dostępu i uprawnienia

Można przyznać [uprawnienia](#device-provisioning-service-permissions) w następujący sposób:

* **Zasady autoryzacji dostępu do udostępnionych**. Zasady dostępu współdzielonego, można przyznać dowolną kombinację [uprawnienia](#device-provisioning-service-permissions). Można zdefiniować zasady w [portalu Azure][lnk-management-portal], lub programowo przy użyciu [interfejsów API REST usługi urządzenia inicjowania obsługi administracyjnej][lnk-resource-provider-apis]. Nowo utworzona usługa inicjowania obsługi administracyjnej ma następujące domyślne zasady:

  * **provisioningserviceowner**: zasady z wszystkie uprawnienia.

> [!NOTE]
> Zobacz [uprawnienia](#device-provisioning-service-permissions) Aby uzyskać szczegółowe informacje.

## <a name="authentication"></a>Authentication

Azure IoT Hub urządzenia usługi udostępniania udziela dostępu do punktów końcowych, upewniając token pod kątem zasad dostępu współdzielonego. Poświadczenia zabezpieczeń, takie jak klucze symetryczne, nigdy nie są przesyłane przez sieć.

> [!NOTE]
> Dostawcy zasobów usługi udostępniania urządzenie jest chronione przy użyciu subskrypcji platformy Azure są wszystkich dostawców w [usługi Azure Resource Manager][lnk-azure-resource-manager].

Aby uzyskać więcej informacji dotyczących sposobu tworzenia i używania tokenów zabezpieczających zobacz następną sekcję.

HTTP jest tylko obsługiwanych protokołów i implementuje uwierzytelnianie przez nieprawidłowy token w tym **autoryzacji** nagłówek żądania.

#### <a name="example"></a>Przykład
`SharedAccessSignature sr=mydps.azure-devices-provisioning.net&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501&skn=provisioningserviceowner`

> [!NOTE]
> [Azure inicjowania obsługi usługi zestawy SDK urządzenia IoT] [ lnk-sdks] automatycznie generować tokeny podczas nawiązywania połączenia z usługą.

## <a name="security-tokens"></a>Tokeny zabezpieczające
Usługa inicjowania obsługi urządzeń używa tokenów zabezpieczających do uwierzytelniania usługi unikać wysyłania kluczy w sieci. Ponadto tokeny zabezpieczające są ograniczone w czas ważności i zakres. [Azure inicjowania obsługi usługi zestawy SDK urządzenia IoT] [ lnk-sdks] automatycznie generować tokeny bez konieczności żadnej specjalnej konfiguracji. Niektóre scenariusze wymagają Generowanie i używanie tokeny zabezpieczające bezpośrednio. Takie scenariusze obejmują bezpośredniego użycia powierzchni HTTP.

### <a name="security-token-structure"></a>Struktura tokenu zabezpieczeń

Aby przyznać ograniczony czas dostępu dla usług do określonych funkcji w usłudze udostępniania urządzenia IoT można użyć tokenów zabezpieczających. Aby uzyskać zezwolenie na połączenia z usługą inicjowania obsługi administracyjnej, usług musi wysyłanie tokenów zabezpieczających, podpisany dostępu współdzielonego lub klucza symetrycznego.

Podpisany token dostępu współdzielonego klucza zapewnia dostęp do wszystkich funkcji, które są skojarzone z uprawnieniami zasady dostępu współdzielonego. 

Token zabezpieczający ma następujący format:

`SharedAccessSignature sig={signature}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

Poniżej przedstawiono oczekiwanych wartości:

| Wartość | Opis |
| --- | --- |
| {sygnatury} |Podpis HMAC SHA256 ciąg w formie: `{URL-encoded-resourceURI} + "\n" + expiry`. **Ważne**: klucz jest zdekodować z formatu base64 i używany jako klucz do wykonywania obliczeń HMAC SHA256.|
| {wygaśnięcia} |Ciągi UTF8 liczbę sekund od czasu UTC epoki 00:00:00 w dniu 1 stycznia rokiem 1970. |
| {URL-zakodowane resourceURI} | Małe sprawy kodowania adresów URL identyfikator URI zasobu małe litery. Prefiks identyfikatora URI (według segmentu) punktów końcowych, które mogą być udostępniane z tym tokenem, począwszy od nazwy hosta IoT urządzenia inicjowania obsługi usługi (nie protocol). Na przykład `mydps.azure-devices-provisioning.net`. |
| {policyName} |Nazwa zasady dostępu współdzielonego, do którego odwołuje się ten token. |

**Uwaga na prefiksie**: Prefiks URI jest obliczana przez segment, a nie przez znak. Na przykład `/a/b` był prefiksem dla `/a/b/c` , ale nie dla `/a/bc`.

Poniższy fragment kodu Node.js zawiera funkcji o nazwie **generateSasToken** który oblicza tokenu z wejść `resourceUri, signingKey, policyName, expiresInMins`. Następne sekcje szczegóły dotyczące inicjowania różnych komponentów dla różnych zastosowań tokenu.

```nodejs
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

Porównanie jest równoważne kodu języka Python do wygenerowania tokenu zabezpieczeń:

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
> Ponieważ czas ważności tokenu jest weryfikowane na maszynach usługi udostępniania urządzenia IoT, odejście zegara komputera, który generuje token musi być minimalny.


### <a name="use-security-tokens-from-service-components"></a>Używaj tokenów zabezpieczeń z składniki usługi

Składniki usługi można generować tylko tokeny zabezpieczające, za pomocą zasad dostępu współdzielonego przyznanie odpowiednich uprawnień, jak opisano wcześniej.

Poniżej przedstawiono funkcje usługi udostępniane w punktach końcowych:

| Endpoint | Funkcjonalność |
| --- | --- |
| `{your-service}.azure-devices-provisioning.net/enrollments` |Udostępnia operacje rejestracji urządzenia przy użyciu usługi inicjowania obsługi urządzeń. |
| `{your-service}.azure-devices-provisioning.net/enrollmentGroups` |Udostępnia operacje do zarządzania grupami rejestracji urządzenia. |
| `{your-service}.azure-devices-provisioning.net/registrations/{id}` |Udostępnia operacje pobierania i zarządzania nimi stan rejestracji urządzenia. |


Na przykład usługi wygenerowanych przy użyciu wstępnie utworzonej udostępnionych zasady dostępu o nazwie **enrollmentread** utworzyć token z następującymi parametrami:

* Identyfikator URI zasobu: `{mydps}.azure-devices-provisioning.net`,
* Klucz podpisujący: jeden z kluczy `enrollmentread` zasad,
* Nazwa zasad: `enrollmentread`,
* wszelkie czas wygaśnięcia.

![Tworzenie zasad dostępu współdzielonego dla swojego wystąpienia punktu dystrybucji w portalu][img-add-shared-access-policy]

```nodejs
var endpoint ="mydps.azure-devices-provisioning.net";
var policyName = 'enrollmentread'; 
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

Wynik, którym przyznano dostęp do odczytu wszystkich rekordów rejestracji, będzie:

`SharedAccessSignature sr=mydps.azure-devices-provisioning.net&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=enrollmentread`

## <a name="reference-topics"></a>Tematy odwołań:

Następujące tematy dokumentacji dostarczają więcej informacji na temat kontrolowania dostępu do usługi obsługi urządzenia IoT.

## <a name="device-provisioning-service-permissions"></a>Uprawnienia usługi inicjowania obsługi urządzeń

W poniższej tabeli wymieniono uprawnienia, których można użyć do kontrolowania dostępu do usługi obsługi urządzenia IoT.

| Uprawnienie | Uwagi |
| --- | --- |
| **ServiceConfig** |Przyznaje prawa do zmiany konfiguracji usługi. <br/>To uprawnienie jest używany przez usługi w chmurze zaplecza. |
| **EnrollmentRead** |Przyznaje dostęp do odczytu do rejestracji grup i rejestracji urządzeń. <br/>To uprawnienie jest używany przez usługi w chmurze zaplecza. |
| **EnrollmentWrite** |Przyznaje dostęp do zapisu do rejestracji urządzeń i grup rejestracji. <br/>To uprawnienie jest używany przez usługi w chmurze zaplecza. |
| **RegistrationStatusRead** |Przyznaje dostęp do odczytu stan rejestracji urządzenia. <br/>To uprawnienie jest używany przez usługi w chmurze zaplecza. |
| **RegistrationStatusWrite**  |Przyznaje usunąć dostęp do stanu rejestracji urządzenia. <br/>To uprawnienie jest używany przez usługi w chmurze zaplecza. |

<!-- links and images -->

[img-add-shared-access-policy]: ./media/how-to-control-access/how-to-add-shared-access-policy.PNG
[lnk-sdks]: ../iot-hub/iot-hub-devguide-sdks.md
[lnk-management-portal]: https://portal.azure.com
[lnk-azure-resource-manager]: ../azure-resource-manager/resource-group-overview.md
[lnk-resource-provider-apis]: https://docs.microsoft.com/rest/api/iot-dps/
