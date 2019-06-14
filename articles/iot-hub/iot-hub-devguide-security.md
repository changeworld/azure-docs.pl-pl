---
title: Omówienie zabezpieczeń usługi Azure IoT Hub | Dokumentacja firmy Microsoft
description: Przewodnik dla deweloperów — jak kontrolować dostęp do usługi IoT Hub dla aplikacji dla urządzeń i aplikacji zaplecza. Zawiera informacje na temat tokeny zabezpieczające i pomoc techniczna dla certyfikatów X.509.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/18/2018
ms.openlocfilehash: bb402a5a059fb6f2836bddbd951220271ca77ba3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60400618"
---
# <a name="control-access-to-iot-hub"></a>Kontrola dostępu do centrum IoT Hub

W tym artykule opisano opcje dotyczące zabezpieczania usługi IoT hub. IoT Hub używa *uprawnienia* do udzielania dostępu do każdego punktu końcowego Centrum IoT. Uprawnienia ograniczają dostęp do usługi IoT hub w oparciu o funkcjonalność.

W tym artykule przedstawiono:

* Różne uprawnienia można przyznać do urządzenia lub serwer zaplecza w aplikacji dostęp do Centrum IoT hub.
* Proces uwierzytelniania i tokeny, których ona używa, aby sprawdzić uprawnienia.
* Jak ograniczyć zakres poświadczenia, aby ograniczyć dostęp do określonych zasobów.
* Obsługa usługi IoT Hub certyfikaty X.509.
* Mechanizmy uwierzytelniania urządzeń niestandardowych, które używają istniejącej rejestrów tożsamości urządzeń lub schematów uwierzytelniania.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Musi mieć odpowiednie uprawnienia dostępu do żadnego z punktów końcowych usługi IoT Hub. Na przykład urządzenie musi zawierać token zawierający poświadczenia zabezpieczeń wraz z każdej wiadomości wysyłanych do usługi IoT Hub.

## <a name="access-control-and-permissions"></a>Kontrola dostępu i uprawnień

Możesz nadawać [uprawnienia](#iot-hub-permissions) w następujący sposób:

* **Centrum IoT na poziomie współużytkowane zasady dostępu**. Zasady dostępu współdzielonego może nadać dowolną kombinację [uprawnienia](#iot-hub-permissions). Można zdefiniować zasady w [witryny Azure portal](https://portal.azure.com), programowo przy użyciu [interfejsów API REST usługi IoT Hub zasobów](/rest/api/iothub/iothubresource), lub za pomocą [az iot hub zasad](/cli/azure/iot/hub/policy?view=azure-cli-latest) interfejsu wiersza polecenia. Nowo utworzone Centrum IoT ma następujące domyślne zasady:
  
  | Zasady dostępu współdzielonego | Uprawnienia |
  | -------------------- | ----------- |
  | iothubowner | Uprawnienie all |
  | usługa | **ServiceConnect** uprawnień |
  | urządzenia | **DeviceConnect** uprawnień |
  | registryRead | **RegistryRead** uprawnień |
  | registryReadWrite | **RegistryRead** i **RegistryWrite** uprawnień |

* **Poświadczenia zabezpieczeń na poziomie urządzenia**. Każde Centrum IoT Hub zawiera [rejestr tożsamości](iot-hub-devguide-identity-registry.md) dla każdego urządzenia w rejestrze tej tożsamości można skonfigurować poświadczenia zabezpieczeń, które udzielić **DeviceConnect** uprawnień ograniczone do odpowiedniego urządzenia punkty końcowe.

Na przykład w przypadku typowego rozwiązania IoT:

* Używa składnika zarządzania urządzeniami *registryReadWrite* zasad.
* Używa składnika procesora zdarzeń *usługi* zasad.
* Składnik logiki biznesowej urządzenia czasu wykonywania używa *usługi* zasad.
* Poszczególne urządzenia łączą, przy użyciu poświadczeń przechowywanych w rejestrze tożsamości Centrum IoT.

> [!NOTE]
> Zobacz [uprawnienia](#iot-hub-permissions) Aby uzyskać szczegółowe informacje.

## <a name="authentication"></a>Authentication

Usługa Azure IoT Hub udziela dostępu do punktów końcowych, sprawdzając token przed zasady dostępu współdzielonego i poświadczenia zabezpieczeń rejestru tożsamości.

Poświadczenia zabezpieczeń, takie jak klucze symetryczne, nigdy nie są przesyłane przez sieć.

> [!NOTE]
> Dostawcy zasobów usługi Azure IoT Hub jest zabezpieczony za pomocą subskrypcji platformy Azure, ponieważ wszyscy dostawcy w [usługi Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

Aby uzyskać więcej informacji dotyczących sposobu tworzenia i używania tokenów zabezpieczających, zobacz [tokeny zabezpieczeń usługi IoT Hub](iot-hub-devguide-security.md#security-tokens).

### <a name="protocol-specifics"></a>Szczegóły protokołu

Każdego obsługiwanego protokołu MQTT, AMQP i protokołu HTTPS, służy do transportu tokenów na różne sposoby.

Korzystając z protokołu MQTT, pakiet CONNECT nie ma deviceId jako ClientId, `{iothubhostname}/{deviceId}` w pole nazwy użytkownika i token sygnatury dostępu Współdzielonego, w polu hasła. `{iothubhostname}` powinna być pełną CName usługi IoT hub (na przykład contoso.azure-devices.net).

Korzystając z [AMQP](https://www.amqp.org/), Centrum IoT Hub obsługuje [zwykły SASL](https://tools.ietf.org/html/rfc4616) i [AMQP oświadczeń — zabezpieczenia na poziomie](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc).

Użycie połączeń AMQP oświadczeń — zabezpieczenia na poziomie standardowa Określa, jak przesyłać tych tokenów.

Dla zwykłego SASL **username** może być:

* `{policyName}@sas.root.{iothubName}` Jeśli przy użyciu tokenów poziom Centrum IoT.
* `{deviceId}@sas.{iothubname}` Jeśli przy użyciu tokenów urządzenia o określonym zakresie.

W obu przypadkach pole hasło zawiera token, zgodnie z opisem w [tokeny zabezpieczeń usługi IoT Hub](iot-hub-devguide-security.md#security-tokens).

Protokół HTTPS implementuje uwierzytelniania, umieszczając prawidłowy token w **autoryzacji** nagłówek żądania.

#### <a name="example"></a>Przykład

Nazwa użytkownika (identyfikatorze urządzenia rozróżniana jest wielkość liter): `iothubname.azure-devices.net/DeviceId`

Hasło (można wygenerować token sygnatury dostępu Współdzielonego za pomocą [Eksplorator urządzeń](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer) narzędzia polecenia interfejsu wiersza polecenia rozszerzenia [az iot hub Generowanie token sygnatury dostępu współdzielonego —](/cli/azure/ext/azure-cli-iot-ext/iot/hub?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-generate-sas-token), lub [narzędzia usługi Azure IoT dla programu Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)):

`SharedAccessSignature sr=iothubname.azure-devices.net%2fdevices%2fDeviceId&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501`

> [!NOTE]
> [Azure IoT SDKs](iot-hub-devguide-sdks.md) automatycznie generować tokeny podczas nawiązywania połączenia z usługą. W niektórych przypadkach zestawami SDK Azure IoT nie obsługują wszystkich protokołów lub wszystkich metod uwierzytelniania.

### <a name="special-considerations-for-sasl-plain"></a>Specjalne uwagi dotyczące zwykły SASL

Korzystając z mechanizmu SASL zwykły z obsługą protokołu AMQP, klienta nawiązującego połączenie do usługi IoT hub można użyć pojedynczy token dla każdego połączenia TCP. Po wygaśnięciu ważności tokenu połączenie TCP zamknie połączenie z usługi i wyzwala ponowne nawiązanie połączenia. To zachowanie, gdy nie kłopotliwy dla aplikacji zaplecza, jest uszkodzenia aplikacji urządzenia z następujących powodów:

* Bramy ze łączyć imieniu wiele urządzeń. Korzystając z mechanizmu SASL zwykły, muszą utworzyć różne połączenia protokołu TCP dla każdego urządzenia, na nawiązywanie połączeń z Centrum IoT hub. W tym scenariuszu znacznie zwiększa zużycia energii i zasoby sieciowe i zwiększa opóźnienia każdego połączenia urządzenia.

* Ograniczonych zasobach urządzeń dotkną zwiększenie wykorzystania zasobów do ponownego połączenia po każdym wygaśnięcia tokenu.

## <a name="scope-iot-hub-level-credentials"></a>Zakres poświadczeń na poziomie Centrum IoT

Można określić zakres zasad zabezpieczeń na poziomie Centrum IoT, tworząc tokenów z ograniczeniami identyfikator URI zasobu. Na przykład punkt końcowy, aby wysyłać komunikaty urządzenie chmura z urządzenia jest **/devices/ {deviceId} / komunikaty/zdarzenia**. Można również użyć zasad dostępu współdzielonego na poziomie Centrum IoT przy użyciu **DeviceConnect** uprawnień do podpisywania tokenu, którego identyfikator URI zasobu jest **/devices/ {deviceId}** . Ta metoda tworzy token, którego można używać do wysyłania wiadomości w imieniu urządzenia tylko **deviceId**.

Mechanizm ten jest podobny do [zasad wydawcy usługi Event Hubs](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab)i umożliwia Implementowanie niestandardowych metod uwierzytelniania.

## <a name="security-tokens"></a>Tokeny zabezpieczające

Usługi IoT Hub do uwierzytelniania urządzeń i usług, aby uniknąć przesyłanie kluczy na potrzeby przesyłu przy użyciu tokenów zabezpieczających. Ponadto tokeny zabezpieczające są ograniczony czas ważności i zakresu. [Usługa Azure IoT SDKs](iot-hub-devguide-sdks.md) automatycznego generowania tokenów bez konieczności żadnej specjalnej konfiguracji. Niektóre scenariusze wymagają do generowania i używania tokenów zabezpieczających bezpośrednio. Takie scenariusze obejmują:

* Bezpośredniemu wykorzystaniu powierzchnie MQTT, AMQP lub HTTPS.

* Implementacji wzorca usługi tokenu, jak wyjaśniono w [uwierzytelniania urządzeń niestandardowego](iot-hub-devguide-security.md#custom-device-and-module-authentication).

Usługa IoT Hub umożliwia również urządzeń do uwierzytelniania za pomocą usługi IoT Hub przy użyciu [certyfikaty X.509](iot-hub-devguide-security.md#supported-x509-certificates).

### <a name="security-token-structure"></a>Struktura tokenu zabezpieczeń

Tokeny zabezpieczające umożliwia przyznawanie ograniczone czasowo dostęp do urządzeń i usług do określonych funkcji w usłudze IoT Hub. Aby uzyskać autoryzacji do łączenia z usługą IoT Hub, urządzeń i usług musi wysyłanie tokenów zabezpieczających podpisany przy użyciu dostępu współdzielonego lub klucza symetrycznego. Te klucze są przechowywane przy użyciu tożsamości urządzenia w rejestrze tożsamości.

Token jest podpisany przy użyciu dostępu do klucza udziela dostępu współdzielonego do wszystkich funkcji, które są skojarzone z uprawnienia zasad dostępu współdzielonego. Token jest podpisany przy użyciu tożsamości urządzenia symetrycznego klucza tylko przyznaje **DeviceConnect** uprawnień dla tożsamości skojarzone urządzenia.

Token zabezpieczający ma następujący format:

`SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

Oto oczekiwane wartości:

| Wartość | Opis |
| --- | --- |
| {Sygnatura} |Ciąg sygnatury HMAC SHA256 w postaci: `{URL-encoded-resourceURI} + "\n" + expiry`. **Ważne**: Klucz jest zdekodować z formatu base64 i użyć go jako klucza do wykonywania obliczeń HMAC SHA256. |
| {resourceURI} |Prefiks identyfikatora URI (według segmentu) punktów końcowych, które mogą być udostępniane z tym tokenem, rozpoczynając od nazwy hosta usługi IoT hub (nie protocol). Na przykład: `myHub.azure-devices.net/devices/device1` |
| {expiry} |Ciągi UTF8 liczba sekund od epoki 00:00:00 czasu UTC na 1 stycznia 1970. |
| {URL-encoded-resourceURI} |Małe zamierzone, Zapisz kodowania adresu URL identyfikator URI zasobu małymi literami |
| {policyName} |Nazwa zasad dostępu współdzielonego, do którego odwołuje się ten token. Nieobecny, jeśli token odnosi się do rejestru urządzeń poświadczenia. |

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
    + base64UriEncoded + "&se=" + expires;
    if (policyName) token += "&skn="+policyName;
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
        'se' : str(int(ttl))
    }

    if policy_name is not None:
        rawtoken['skn'] = policy_name

    return 'SharedAccessSignature ' + urlencode(rawtoken)
```

Funkcje w języku C#, aby wygenerować token zabezpieczający jest:

```csharp
using System;
using System.Globalization;
using System.Net;
using System.Net.Http;
using System.Security.Cryptography;
using System.Text;

public static string generateSasToken(string resourceUri, string key, string policyName, int expiryInSeconds = 3600)
{
    TimeSpan fromEpochStart = DateTime.UtcNow - new DateTime(1970, 1, 1);
    string expiry = Convert.ToString((int)fromEpochStart.TotalSeconds + expiryInSeconds);

    string stringToSign = WebUtility.UrlEncode(resourceUri) + "\n" + expiry;

    HMACSHA256 hmac = new HMACSHA256(Convert.FromBase64String(key));
    string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));

    string token = String.Format(CultureInfo.InvariantCulture, "SharedAccessSignature sr={0}&sig={1}&se={2}", WebUtility.UrlEncode(resourceUri), WebUtility.UrlEncode(signature), expiry);

    if (!String.IsNullOrEmpty(policyName))
    {
        token += "&skn=" + policyName;
    }

    return token;
}

```


> [!NOTE]
> Ponieważ czas ważności tokenu jest sprawdzana na maszynach usługi IoT Hub, dryfu zegara komputera, która generuje token musi być minimalny.

### <a name="use-sas-tokens-in-a-device-app"></a>Używanie tokenów SAS w aplikacji urządzenia

Istnieją dwa sposoby uzyskania **DeviceConnect** uprawnień za pomocą usługi IoT Hub za pomocą tokenów zabezpieczających: Użyj [klucza symetrycznego urządzenia w rejestrze tożsamości](#use-a-symmetric-key-in-the-identity-registry), lub użyj [klucza dostępu współdzielonego](#use-a-shared-access-policy).

Należy pamiętać, że wszystkie funkcje dostępne z urządzenia jest udostępniona zgodnie z projektem w punktach końcowych z prefiksem `/devices/{deviceId}`.

> [!IMPORTANT]
> Jedynym sposobem IoT Hub uwierzytelnia się określonego urządzenia jest przy użyciu klucza symetrycznego tożsamości urządzenia. W przypadku stosowania zasad dostępu współdzielonego na dostęp do funkcji urządzenia rozwiązania należy wziąć pod uwagę składnika wystawiania tokenu zabezpieczeń jako zaufanego podskładnika.

Punkty końcowe przeznaczonych dla urządzeń są (niezależnie od tego, protocol):

| Endpoint | Funkcja |
| --- | --- |
| `{iot hub host name}/devices/{deviceId}/messages/events` |Wysyłanie komunikatów z urządzenia do chmury. |
| `{iot hub host name}/devices/{deviceId}/messages/devicebound` |Odbieranie komunikatów z chmury do urządzeń. |

### <a name="use-a-symmetric-key-in-the-identity-registry"></a>Użyj klucza symetrycznego w rejestrze tożsamości

W przypadku używania klucza symetrycznego tożsamości urządzenia do generowania tokenu, jednostki policyName (`skn`) elementu token zostanie pominięty.

Na przykład token utworzony na dostęp do wszystkich funkcji urządzeń mają następujące parametry:

* Identyfikator URI zasobu: `{IoT hub name}.azure-devices.net/devices/{device id}`,
* klucz podpisywania: dowolnego klucza symetrycznego dla `{device id}` tożsamości
* Brak nazwy zasad
* wszelkie czas wygaśnięcia.

Przykład za pomocą poprzedniej funkcji Node.js może być:

```javascript
var endpoint ="myhub.azure-devices.net/devices/device1";
var deviceKey ="...";

var token = generateSasToken(endpoint, deviceKey, null, 60);
```

Wynik, który przyznaje dostęp do wszystkich funkcji dla urządzenia 1, będzie:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697`

> [!NOTE]
> Istnieje możliwość wygenerowania tokenu sygnatury dostępu Współdzielonego, za pomocą [Eksplorator urządzeń](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer) narzędzia polecenia interfejsu wiersza polecenia rozszerzenia [az iot hub Generowanie token sygnatury dostępu współdzielonego —](/cli/azure/ext/azure-cli-iot-ext/iot/hub?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-generate-sas-token), lub [narzędzia usługi Azure IoT dla programu Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).

### <a name="use-a-shared-access-policy"></a>Użyj zasad dostępu współdzielonego

Podczas tworzenia tokenu z zasad dostępu współdzielonego, ustaw `skn` pole Nazwa zasad. Te zasady należy przyznać **DeviceConnect** uprawnień.

Są dwa główne scenariusze dotyczące korzystania z zasad dostępu współdzielonego na dostęp do funkcji urządzenia:

* [w chmurze bramy protokołów](iot-hub-devguide-endpoints.md),
* [token usługi](iot-hub-devguide-security.md#custom-device-and-module-authentication) używaną do zaimplementowania schematy uwierzytelniania niestandardowego.

Ponieważ zasady dostępu współdzielonego potencjalnie udzielić dostępu, aby połączyć się jako dowolne urządzenie, ważne jest używany prawidłowy identyfikator URI zasobu, do tworzenia tokenów zabezpieczających. To ustawienie jest szczególnie ważne w przypadku usług tokenów, które mają zakres token z konkretnym urządzeniem za pomocą identyfikatora URI zasobu. Ten punkt jest mniej istotne dla bramy protokołów, zgodnie z ich są już pośredniczących ruchu dla wszystkich urządzeń.

Na przykład usługi tokenu przy użyciu wstępnie utworzonego udostępnionego zasady dostępu o nazwie **urządzenia** spowodowałoby utworzenie tokenu z następującymi parametrami:

* Identyfikator URI zasobu: `{IoT hub name}.azure-devices.net/devices/{device id}`,
* klucz podpisywania: jeden z kluczy `device` zasad
* Nazwa zasady: `device`,
* wszelkie czas wygaśnięcia.

Przykład za pomocą poprzedniej funkcji Node.js może być:

```javascript
var endpoint ="myhub.azure-devices.net/devices/device1";
var policyName = 'device';
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

Wynik, który przyznaje dostęp do wszystkich funkcji dla urządzenia 1, będzie:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697&skn=device`

Brama protokołu można użyć tego samego tokenu dla wszystkich urządzeń, wystarczy ustawić identyfikator URI zasobu do `myhub.azure-devices.net/devices`.

### <a name="use-security-tokens-from-service-components"></a>Używanie tokenów zabezpieczeń ze składników usługi

Składniki usługi może generować jedynie tokeny zabezpieczające, za pomocą zasad dostępu współdzielonego przyznawanie odpowiednich uprawnień, jak wyjaśniono wcześniej.

Poniżej przedstawiono funkcje usługi dostępne w punktach końcowych:

| Endpoint | Funkcja |
| --- | --- |
| `{iot hub host name}/devices` |Tworzenie, aktualizowanie, pobieranie i usuwanie tożsamości urządzeń. |
| `{iot hub host name}/messages/events` |Odbieranie komunikatów z urządzenia do chmury. |
| `{iot hub host name}/servicebound/feedback` |Odbieranie opinii dla komunikatów z chmury do urządzeń. |
| `{iot hub host name}/devicebound` |Wysyłanie komunikatów z chmury do urządzeń. |

Na przykład usługa generowania, przy użyciu wstępnie utworzonego udostępnionego zasady dostępu o nazwie **registryRead** spowodowałoby utworzenie tokenu z następującymi parametrami:

* Identyfikator URI zasobu: `{IoT hub name}.azure-devices.net/devices`,
* klucz podpisywania: jeden z kluczy `registryRead` zasad
* Nazwa zasady: `registryRead`,
* wszelkie czas wygaśnięcia.

```javascript
var endpoint ="myhub.azure-devices.net/devices";
var policyName = 'registryRead';
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

Wynik, którym przyznano dostęp do odczytu wszystkich tożsamości urządzenia, będzie:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=registryRead`

## <a name="supported-x509-certificates"></a>Obsługiwane certyfikatów X.509

Każdy certyfikat X.509 służy do uwierzytelniania na urządzeniu z usługą IoT Hub, przekazując odcisk palca certyfikatu lub urzędu certyfikacji (CA) dla usługi Azure IoT Hub. Uwierzytelnianie przy użyciu tylko odciski palców certyfikatu sprawdza, czy odcisk palca prezentowane odpowiada skonfigurowanym odciskiem palca. Uwierzytelnianie przy użyciu urzędu certyfikacji sprawdza poprawność łańcucha certyfikatów. 

Obsługiwane certyfikaty obejmują:

* **Istniejący certyfikat X.509**. Urządzenie może już być skojarzone z nim certyfikatu X.509. Urządzenie może używać tego certyfikatu do uwierzytelniania za pomocą usługi IoT Hub. Działa z odciskiem palca lub uwierzytelniania urzędu certyfikacji. 

* **Certyfikat X.509 podpisany przez urząd certyfikacji**. Aby zidentyfikować urządzenia i go uwierzytelniać za pomocą usługi IoT Hub, można użyć certyfikatu X.509 generowane i podpisany przez urząd certyfikacji (CA). Działa z odciskiem palca lub uwierzytelniania urzędu certyfikacji.

* **A własnym wygenerowany i 509 certyfikat z podpisem własnym**. Producent urządzenia lub wewnętrznych wdrażania można generować te certyfikaty i przechowywać odpowiedniego klucza prywatnego (i certyfikatu) na urządzeniu. Użyj narzędzi takich jak [OpenSSL](https://www.openssl.org/) i [Windows SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) narzędzie do tego celu. Działa tylko w przypadku uwierzytelniania odciskiem palca. 

Urządzenie może użyć certyfikatu X.509 lub token zabezpieczający uwierzytelniania, ale nie obu.

Aby uzyskać więcej informacji na temat uwierzytelniania przy użyciu urzędu certyfikacji, zobacz [uwierzytelnianie urządzeń przy użyciu certyfikatu X.509 urzędu certyfikacji](iot-hub-x509ca-overview.md).

### <a name="register-an-x509-certificate-for-a-device"></a>Zarejestruj certyfikat X.509 dla urządzenia

[Usługa zestawu SDK Azure IoT dla języka C#](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/service) (wersja 1.0.8+) obsługuje rejestrowanie urządzeń, który używa certyfikatu X.509 do uwierzytelniania. Inne interfejsy API, takich jak importu/eksportu urządzeń obsługują także certyfikaty X.509.

Można również użyć polecenia interfejsu wiersza polecenia rozszerzenia [az iot hub — tożsamość urządzenia](/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) konfigurowania certyfikatów X.509 dla urządzenia.

### <a name="c-support"></a>C\# pomocy technicznej

**RegistryManager** klasy zapewnia programowy sposób, aby zarejestrować urządzenie. W szczególności **AddDeviceAsync** i **UpdateDeviceAsync** metody umożliwiają rejestrowanie i aktualizowanie urządzeń w rejestrze tożsamości Centrum IoT Hub. Te dwie metody przyjmują **urządzenia** wystąpienia jako dane wejściowe. **Urządzenia** klasa zawiera **uwierzytelniania** właściwość, która pozwala na określenie odciski palców certyfikatu X.509 podstawowego i pomocniczego. Odcisk palca reprezentuje skrót SHA256 certyfikat X.509 (przechowywane przy użyciu kodowania binarnego DER). Masz możliwość określenia podstawowy odcisk palca lub pomocniczy odcisk palca lub oba. Odciski palców podstawowe i pomocnicze są obsługiwane do obsługi scenariuszach przerzucania certyfikatu.

Poniżej znajduje się przykładowy C\# fragment kodu, aby zarejestrować urządzenia przy użyciu odcisku palca certyfikatu X.509:

```csharp
var device = new Device(deviceId)
{
  Authentication = new AuthenticationMechanism()
  {
    X509Thumbprint = new X509Thumbprint()
    {
      PrimaryThumbprint = "B4172AB44C28F3B9E117648C6F7294978A00CDCBA34A46A1B8588B3F7D82C4F1"
    }
  }
};
RegistryManager registryManager = RegistryManager.CreateFromConnectionString(deviceGatewayConnectionString);
await registryManager.AddDeviceAsync(device);
```

### <a name="use-an-x509-certificate-during-run-time-operations"></a>Użyj certyfikatu X.509 podczas wykonywania operacji w czasie wykonywania

[Zestaw SDK urządzeń Azure IoT dla platformy .NET](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/device) (wersja 1.0.11+) obsługuje korzystanie z certyfikatów X.509.

### <a name="c-support"></a>C\# pomocy technicznej

Klasa **DeviceAuthenticationWithX509Certificate** obsługuje tworzenie **DeviceClient** wystąpień przy użyciu certyfikatu X.509. Certyfikat X.509 musi być w formacie PFX (nazywany również PKCS #12), który zawiera klucz prywatny.

Oto przykład fragmentu kodu:

```csharp
var authMethod = new DeviceAuthenticationWithX509Certificate("<device id>", x509Certificate);

var deviceClient = DeviceClient.Create("<IotHub DNS HostName>", authMethod);
```

## <a name="custom-device-and-module-authentication"></a>Urządzenia i moduł uwierzytelniania niestandardowego

Można użyć usługi IoT Hub [rejestr tożsamości](iot-hub-devguide-identity-registry.md) do konfigurowania poświadczeń zabezpieczeń na urządzeniu/modułowe i uzyskiwać dostęp za pomocą kontrolki [tokenów](iot-hub-devguide-security.md#security-tokens). Jeśli rozwiązania IoT już schemat rejestru i/lub uwierzytelnianie tożsamość niestandardowa, należy rozważyć utworzenie *tokenu usługi* do tej infrastruktury można zintegrować z usługą IoT Hub. W ten sposób można użyć innych funkcji IoT w rozwiązaniu.

Usługa tokenu to usługa w chmurze niestandardowych. Używa usługi IoT Hub *współużytkowane zasady dostępu* z **DeviceConnect** lub **ModuleConnect** uprawnień do utworzenia *urządzenia o określonym zakresie* lub *zakresie modułu* tokenów. Te tokeny umożliwia deweloperom urządzeń i modułu, aby nawiązać połączenie z Centrum IoT hub.

![Kroki wzorzec usługi tokenu](./media/iot-hub-devguide-security/tokenservice.png)

Oto główne kroki wzorzec usługi tokenu:

1. Tworzenie zasad dostępu współdzielonego Centrum IoT przy użyciu **DeviceConnect** lub **ModuleConnect** uprawnienia dla usługi IoT hub. Można utworzyć te zasady w [witryny Azure portal](https://portal.azure.com) lub programowo. Usługa tokenu używa tych zasad do podpisywania tokenów, który on generuje.

2. Gdy urządzenie/modułu musi dostęp do Centrum IoT hub, żąda podpisany token z usługi tokenu. Urządzenia mogą uwierzytelniać za pomocą schemat rejestru lub uwierzytelniania tożsamości niestandardowej ustalenie tożsamości urządzenia/modułu, która korzysta z usługi tokenu do utworzenia tokenu.

3. Usługa tokenu zwraca token. Token jest tworzona przy użyciu `/devices/{deviceId}` lub `/devices/{deviceId}/module/{moduleId}` jako `resourceURI`, za pomocą `deviceId` jako urządzenie uwierzytelniane lub `moduleId` jako moduł uwierzytelniane. Usługa tokenu używa zasad dostępu współdzielonego do utworzenia tokenu.

4. Urządzenie/modułu używa tokenu bezpośrednio z usługi IoT hub.

> [!NOTE]
> Można użyć klasy .NET [SharedAccessSignatureBuilder](https://msdn.microsoft.com/library/microsoft.azure.devices.common.security.sharedaccesssignaturebuilder.aspx) lub klasy Java [IotHubServiceSasToken](/java/api/com.microsoft.azure.sdk.iot.service.auth.iothubservicesastoken) do utworzenia tokenu w usłudze tokenu.

Usługa tokenów można ustawić wygaśnięcie tokenu zgodnie z potrzebami. Po wygaśnięciu ważności tokenu usługi IoT hub serwery połączenia urządzenia/modułu. Następnie urządzenia/modułu, należy zażądać nowego tokenu z usługi tokenu. Czas wygaśnięcia krótki zwiększa to obciążenie urządzenia i/lub modułu i usługi tokenu.

Dla urządzenia/modułu, aby nawiązać połączenie z Centrum, należy nadal go dodać w rejestrze tożsamości Centrum IoT Hub — nawet jeśli korzysta ona z tokenu i nie klucza połączyć. W związku z tym, można kontynuować korzystanie z kontroli dostępu na urządzenie/na module przez włączenie lub wyłączenie tożsamości urządzenia/modułu w [rejestr tożsamości](iot-hub-devguide-identity-registry.md). Takie podejście zmniejsza ryzyko przy użyciu tokenów z czasem wygaśnięcia długie.

### <a name="comparison-with-a-custom-gateway"></a>Porównanie z bramą niestandardowe

Wzorzec usługi tokenu jest zalecany sposób wdrożenia schemat rejestru/uwierzytelniania tożsamości niestandardowej z usługą IoT Hub. Ten wzorzec jest zalecane, ponieważ usługa IoT Hub w dalszym ciągu obsługiwać większość ruchu sieciowego rozwiązania. Jednakże, jeśli schemat uwierzytelniania niestandardowego jest więc sprzężony z protokołem, mogą wymagać *bram* do przetwarzania ruchu sieciowego. Przykładem takiego scenariusza jest przy użyciu [zabezpieczeń TLS (Transport Layer) i klucze wstępne (PSKs)](https://tools.ietf.org/html/rfc4279). Aby uzyskać więcej informacji, zobacz [bramy protokołu](iot-hub-protocol-gateway.md) artykułu.

## <a name="reference-topics"></a>Tematy referencyjne:

W poniższych tematach odwołania udostępnić więcej informacji na temat kontrolowania dostępu do usługi IoT hub.

## <a name="iot-hub-permissions"></a>Uprawnienia usługi IoT Hub

W poniższej tabeli wymieniono uprawnienia, których można użyć do kontrolowania dostępu do usługi IoT hub.

| Uprawnienie | Uwagi |
| --- | --- |
| **RegistryRead** |Przyznaje dostęp do rejestru tożsamości. Aby uzyskać więcej informacji, zobacz [rejestr tożsamości](iot-hub-devguide-identity-registry.md). <br/>To uprawnienie jest używany przez usługi w chmurze wewnętrznej. |
| **RegistryReadWrite** |Przyznaje uprawnienia odczytu i zapisu w rejestrze tożsamości. Aby uzyskać więcej informacji, zobacz [rejestr tożsamości](iot-hub-devguide-identity-registry.md). <br/>To uprawnienie jest używany przez usługi w chmurze wewnętrznej. |
| **ServiceConnect** |Przyznaje dostęp do chmury usługi komunikacji i monitorowania punktów końcowych. <br/>Przyznaje uprawnienia do odbierania komunikatów z urządzenia do chmury, wysyłanie komunikatów z chmury do urządzeń i pobrać odpowiedni potwierdzeń dostarczenia. <br/>Przyznaje uprawnienia do pobierania potwierdzeń dostarczenia pliku służy do przekazywania. <br/>Udziela uprawnień do reprezentacji dostępu do urządzeń na potrzeby aktualizacji tagi i żądane właściwości, zgłaszanych właściwości do pobrania i uruchamianie zapytań. <br/>To uprawnienie jest używany przez usługi w chmurze wewnętrznej. |
| **DeviceConnect** |Zapewnia dostęp do punktów końcowych przeznaczonych dla urządzenia. <br/>Udziela uprawnień do wysyłania komunikatów z urządzenia do chmury i odbierania komunikatów z chmury do urządzeń. <br/>Przyznaje uprawnienia do wykonania przekazywania plików z urządzenia. <br/>Udziela uprawnień do odbierania powiadomień właściwość żądanego bliźniacza reprezentacja urządzenia i zaktualizować bliźniaczej reprezentacji urządzenia zgłoszonych właściwości. <br/>Przekazuje przyznaje uprawnienia do wykonywania pliku. <br/>To uprawnienie jest używany przez urządzenia. |

## <a name="additional-reference-material"></a>Dodatkowe materiały

Inne tematy referencyjne w przewodniku dla deweloperów usługi IoT Hub obejmują:

* [Punkty końcowe usługi IoT Hub](iot-hub-devguide-endpoints.md) w tym artykule opisano różne punkty końcowe, które każde Centrum IoT hub udostępnia dla operacji zarządzania i środowiska wykonawczego.

* [Przydziału i ograniczanie przepływności](iot-hub-devguide-quotas-throttling.md) opisano limity przydziału i ograniczanie zachowań, które są stosowane do usługi IoT Hub.

* [Usługa Azure IoT usługi zestawy SDK urządzeń i](iot-hub-devguide-sdks.md) Wyświetla język różnych zestawów SDK, można użyć podczas tworzenia aplikacji usług i urządzeń, które współdziałają z usługą IoT Hub.

* [Język zapytań usługi IoT Hub](iot-hub-devguide-query-language.md) opisuje język zapytań, można użyć, aby pobrać informacje z usługi IoT Hub o bliźniaczych reprezentacji urządzeń i zadań.

* [Obsługa protokołu MQTT Centrum IoT](iot-hub-mqtt-support.md) zawiera więcej informacji na temat obsługi usługi IoT Hub dla protokołu MQTT.

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy wiesz jak kontrolować dostęp do usługi IoT Hub, mogą być zainteresowane w następujących tematach przewodnik dla deweloperów usługi IoT Hub:

* [Przy użyciu bliźniaczych reprezentacji urządzeń do stanu i konfiguracji](iot-hub-devguide-device-twins.md)
* [Wywoływanie metody bezpośredniej o urządzeniu](iot-hub-devguide-direct-methods.md)
* [Planowanie zadań na wielu urządzeniach](iot-hub-devguide-jobs.md)

Jeśli chcesz wypróbować niektóre pojęcia opisane w tym artykule, zobacz następujące samouczki usługi IoT Hub:

* [Rozpoczynanie pracy z usługą Azure IoT Hub](quickstart-send-telemetry-node.md)
* [Jak wysyłać komunikaty z chmury do urządzenia z usługą IoT Hub](iot-hub-csharp-csharp-c2d.md)
* [Jak przetwarzać komunikaty z urządzenia do chmury usługi IoT Hub](tutorial-routing.md)
