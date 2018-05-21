---
title: Zrozumienie zabezpieczeń Centrum IoT Azure | Dokumentacja firmy Microsoft
description: Przewodnik dewelopera — kontrola dostępu do Centrum IoT dla aplikacji zaplecza i aplikacji dla urządzeń. Zawiera informacje na temat tokeny zabezpieczające i pomoc techniczna dla certyfikatów X.509.
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 45631e70-865b-4e06-bb1d-aae1175a52ba
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/12/2018
ms.author: dobett
ms.openlocfilehash: 25a1c05dc3e72d14482ee6bd1b26a8355cfc7dd9
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
---
# <a name="control-access-to-iot-hub"></a>Kontrola dostępu do centrum IoT Hub

W tym artykule opisano opcje zabezpieczanie Centrum IoT. Centrum IoT używa *uprawnienia* uzyskać dostęp do każdego punktu końcowego Centrum IoT. Uprawnienia ograniczyć dostęp do Centrum IoT, w oparciu o funkcje.

W tym artykule przedstawiono:

* Inne uprawnienia można przyznać do urządzenia lub wewnętrznych aplikacji na dostęp do Centrum IoT.
* Proces uwierzytelniania i tokenów używanych do Sprawdź uprawnienia.
* Jak zakres poświadczenia, aby ograniczyć dostęp do określonych zasobów.
* Obsługa Centrum IoT certyfikatów X.509.
* Urządzeń niestandardowych mechanizmów uwierzytelniania, korzystających z istniejących rejestrów tożsamość urządzenia lub schematy uwierzytelniania.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Musi mieć odpowiednie uprawnienia do uzyskania dostępu punkty końcowe Centrum IoT. Na przykład urządzenie musi zawierać token zawierający poświadczenia zabezpieczeń, wraz z każdej wiadomości wysyłanych do Centrum IoT.

## <a name="access-control-and-permissions"></a>Kontrola dostępu i uprawnienia

Można przyznać [uprawnienia](#iot-hub-permissions) w następujący sposób:

* **Zasady dostępu do udostępnionego Centrum IoT na poziomie**. Zasady dostępu współdzielonego, można przyznać dowolną kombinację [uprawnienia](#iot-hub-permissions). Można zdefiniować zasady w [portalu Azure][lnk-management-portal], lub programowo przy użyciu [interfejsy API REST dostawcy zasobów Centrum IoT][lnk-resource-provider-apis]. Nowo utworzone Centrum IoT ma następujące zasady domyślne:

  * **iothubowner**: zasady z wszystkie uprawnienia.
  * **Usługa**: zasady z **ServiceConnect** uprawnienia.
  * **urządzenie**: zasady z **DeviceConnect** uprawnienia.
  * **registryRead**: zasady z **RegistryRead** uprawnienia.
  * **registryReadWrite**: zasady z **RegistryRead** i RegistryWrite uprawnienia.
  * **Poświadczenia zabezpieczeń na urządzenie**. Zawiera każdego centrum IoT [rejestru tożsamości][lnk-identity-registry]. Dla każdego urządzenia w rejestrze tej tożsamości można skonfigurować poświadczenia zabezpieczeń, które przyznać **DeviceConnect** uprawnień ograniczone do odpowiednich punktów końcowych urządzenia.

Na przykład w typowej rozwiązania IoT:

* Używa składnika zarządzania urządzeniami *registryReadWrite* zasad.
* Używa składnika procesora zdarzeń *usługi* zasad.
* Składnik logiki biznesowej urządzenia środowiska wykonawczego używa *usługi* zasad.
* Poszczególne urządzenia łączą, przy użyciu poświadczeń przechowywanych w rejestrze tożsamości Centrum IoT.

> [!NOTE]
> Zobacz [uprawnienia](#iot-hub-permissions) Aby uzyskać szczegółowe informacje.

## <a name="authentication"></a>Authentication

Centrum IoT Azure udziela dostępu do punktów końcowych, sprawdzając token przed zasady dostępu współdzielonego i poświadczenia zabezpieczeń rejestru tożsamości.

Poświadczenia zabezpieczeń, takie jak klucze symetryczne, nigdy nie są przesyłane przez sieć.

> [!NOTE]
> Centrum IoT Azure dostawcy zasobów jest chronione przy użyciu subskrypcji platformy Azure są wszystkich dostawców w [usługi Azure Resource Manager][lnk-azure-resource-manager].

Aby uzyskać więcej informacji dotyczących sposobu tworzenia i używania tokenów zabezpieczających, zobacz [tokeny zabezpieczające Centrum IoT][lnk-sas-tokens].

### <a name="protocol-specifics"></a>Szczegóły protokołu

Każdego obsługiwanych protokołów, takich jak MQTT, AMQP i HTTPS, transport tokenów na różne sposoby.

Korzystając z MQTT, pakiet CONNECT ma deviceId jako ClientId, `{iothubhostname}/{deviceId}` w polu nazwy użytkownika i tokenu sygnatury dostępu Współdzielonego, w polu hasła. `{iothubhostname}` powinna być pełną CName z Centrum IoT (na przykład devices.net contoso.azure).

Korzystając z [AMQP][lnk-amqp], obsługuje Centrum IoT [zwykły SASL] [ lnk-sasl-plain] i [AMQP oświadczenia na podstawie-zabezpieczeń] [ lnk-cbs].

Jeśli używasz protokołu AMQP oświadczenia na podstawie — zabezpieczeń, standardowego określa sposób przesyłania tych tokenów.

Dla zwykłego SASL **username** może być:

* `{policyName}@sas.root.{iothubName}` Jeśli przy użyciu tokenów poziomie Centrum IoT.
* `{deviceId}@sas.{iothubname}` Jeśli przy użyciu tokenów zakres urządzeń.

W obu przypadkach pole hasło zawiera token, zgodnie z opisem w [tokeny zabezpieczające Centrum IoT][lnk-sas-tokens].

HTTPS wykonuje uwierzytelnianie przy tym nieprawidłowy token w **autoryzacji** nagłówek żądania.

#### <a name="example"></a>Przykład

Nazwa użytkownika (DeviceId jest rozróżniana wielkość liter): `iothubname.azure-devices.net/DeviceId`

Hasło (Generowanie sygnatury dostępu Współdzielonego token z [explorer urządzenia] [ lnk-device-explorer] narzędzie): `SharedAccessSignature sr=iothubname.azure-devices.net%2fdevices%2fDeviceId&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501`

> [!NOTE]
> [Azure IoT SDK] [ lnk-sdks] automatycznie generować tokeny podczas nawiązywania połączenia z usługą. W niektórych przypadkach zestawy SDK usługi Azure IoT nie obsługują wszystkie protokoły lub wszystkich metod uwierzytelniania.

### <a name="special-considerations-for-sasl-plain"></a>Uwagi dotyczące SASL zwykły

Korzystając z protokołu AMQP zwykły SASL, klient nawiązywanie połączeń z Centrum IoT można użyć jednego tokenu dla każdego połączenia TCP. Po wygaśnięciu tokenu połączenie TCP zakończy połączenie z usługą i wyzwala ponowne połączenie. To zachowanie, gdy nie powodować problemy dla aplikacji zaplecza, jest uszkodzenia aplikacji dla urządzeń z następujących powodów:

* Bramy ze łączyć imieniu wiele urządzeń. Przy użyciu zwykłego SASL, mają można utworzyć różne połączenia TCP dla każdego urządzenia nawiązywanie połączeń z Centrum IoT. W tym scenariuszu znacznie zwiększa zużycia energii i zasobów sieciowych i zwiększa opóźnienie połączenia każdego urządzenia.
* Ograniczone zasobów urządzeń dotkną zwiększenie wykorzystania zasobów połączyć się ponownie po każdym wygaśnięcia tokenu.

## <a name="scope-iot-hub-level-credentials"></a>Zakres poświadczeń na poziomie Centrum IoT

Zakres zasad zabezpieczeń na poziomie Centrum IoT można określić, tworząc tokenów z ograniczeniami identyfikator URI zasobu. Na przykład punkt końcowy do wysyłania wiadomości urządzenia do chmury przy użyciu urządzenia jest **/devices/ {deviceId} / wiadomości/zdarzenia**. Można także użyć zasady dostępu współdzielonego z poziomu Centrum IoT z **DeviceConnect** uprawnienia do podpisywania tokenu, w których resourceURI jest **/devices/ {deviceId}**. Ta metoda tworzy token, którego można używać do wysyłania wiadomości w imieniu urządzenia tylko **deviceId**.

Mechanizm ten jest podobny do [zasad wydawcy usługi Event Hubs][lnk-event-hubs-publisher-policy]i umożliwia wdrożenie niestandardowe metody uwierzytelniania.

## <a name="security-tokens"></a>Tokeny zabezpieczające

Centrum IoT korzysta z tokenów zabezpieczających do uwierzytelniania urządzeń i usług unikać wysyłania kluczy w sieci. Ponadto tokeny zabezpieczające są ograniczone w czas ważności i zakres. [Zestawy Azure SDK IoT] [ lnk-sdks] automatycznie generować tokeny bez konieczności żadnej specjalnej konfiguracji. Niektóre scenariusze wymagają Generowanie i używanie tokeny zabezpieczające bezpośrednio. Takie scenariusze obejmują:

* Bezpośredniego użycia powierzchni MQTT, protokołu AMQP lub HTTPS.
* Implementacja wzorca usługi tokenu, zgodnie z objaśnieniem w [uwierzytelnianie urządzenia niestandardowe][lnk-custom-auth].

Centrum IoT umożliwia także urządzeń do uwierzytelniania za pomocą Centrum IoT przy użyciu [certyfikatów X.509][lnk-x509].

### <a name="security-token-structure"></a>Struktura tokenu zabezpieczeń

Aby przyznać ograniczony czas dostępu do urządzeń i usług do określonych funkcji w Centrum IoT można użyć tokenów zabezpieczających. Aby uzyskać zezwolenie na łączenie z Centrum IoT, urządzeń i usług musi wysyłanie tokenów zabezpieczających podpisany dostępu współdzielonego lub klucza symetrycznego. Klucze te są przechowywane za pomocą tożsamości urządzenia w rejestrze tożsamości.

Podpisany token dostępu współdzielonego klucza zapewnia dostęp do wszystkich funkcji, które są skojarzone z uprawnieniami zasady dostępu współdzielonego. Podpisany token urządzenia tożsamości symetrycznego klucza tylko przyznaje **DeviceConnect** uprawnienia dla tożsamości skojarzone urządzenie.

Token zabezpieczający ma następujący format:

`SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

Poniżej przedstawiono oczekiwanych wartości:

| Wartość | Opis |
| --- | --- |
| {sygnatury} |Podpis HMAC SHA256 ciąg w formie: `{URL-encoded-resourceURI} + "\n" + expiry`. **Ważne**: klucz jest zdekodować z formatu base64 i używany jako klucz do wykonywania obliczeń HMAC SHA256. |
| {resourceURI} |Prefiks identyfikatora URI (według segmentu) punktów końcowych, które mogą być udostępniane z tym tokenem, począwszy od nazwy hosta Centrum IoT (nie protocol). Na przykład: `myHub.azure-devices.net/devices/device1` |
| {wygaśnięcia} |Ciągi UTF8 liczbę sekund od czasu UTC epoki 00:00:00 w dniu 1 stycznia rokiem 1970. |
| {URL-zakodowane resourceURI} |Małe przypadek kodowania adresów URL identyfikator URI zasobu małe litery |
| {policyName} |Nazwa zasady dostępu współdzielonego, do którego odwołuje się ten token. Brak Jeśli token odwołuje się do poświadczeń w rejestrze urządzeń. |

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

    // Construct autorization string
    var token = "SharedAccessSignature sr=" + resourceUri + "&sig="
    + base64UriEncoded + "&se=" + expires;
    if (policyName) token += "&skn="+policyName;
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
        'se' : str(int(ttl))
    }

    if policy_name is not None:
        rawtoken['skn'] = policy_name

    return 'SharedAccessSignature ' + urlencode(rawtoken)
```

Funkcje języka C# do generowania tokenu zabezpieczającego jest:

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
> Ponieważ czas ważności tokenu jest weryfikowane na komputerach Centrum IoT, odejście zegara komputera, który generuje token musi być minimalny.

### <a name="use-sas-tokens-in-a-device-app"></a>Używanie tokeny sygnatury dostępu Współdzielonego w aplikacji urządzeń

Istnieją dwa sposoby uzyskania **DeviceConnect** uprawnienia z Centrum IoT z tokenów zabezpieczających: Użyj [klucza symetrycznego urządzenia z rejestru tożsamości](#use-a-symmetric-key-in-the-identity-registry), lub użyj [udostępniony klucz dostępu](#use-a-shared-access-policy).

Należy pamiętać, że wszystkie funkcje dostępne z urządzeń jest udostępniany przez projekt w punktach końcowych z prefiksem `/devices/{deviceId}`.

> [!IMPORTANT]
> Jedynym sposobem, aby Centrum IoT uwierzytelnia określonego urządzenia jest przy użyciu klucza symetrycznego tożsamości urządzenia. W przypadkach, gdy zasady dostępu współdzielonego jest używany do korzystania z funkcji urządzenia rozwiązania należy wziąć pod uwagę wystawiania tokenu zabezpieczającego jako zaufany podskładnika składnika.

Punkty końcowe skierowane do urządzenia są (niezależnie od protocol):

| Endpoint | Funkcjonalność |
| --- | --- |
| `{iot hub host name}/devices/{deviceId}/messages/events` |Wysyłanie wiadomości urządzenia do chmury. |
| `{iot hub host name}/devices/{deviceId}/messages/devicebound` |Komunikaty chmury do urządzenia. |

### <a name="use-a-symmetric-key-in-the-identity-registry"></a>Użyj klucza symetrycznego w rejestrze tożsamości

Korzystając z klucza symetrycznego tożsamość urządzenia do wygenerowania tokena Nazwa_zasady (`skn`) zostanie pominięty element tokenu.

Na przykład token utworzony na dostęp do wszystkich funkcji urządzenia powinny mieć następujące parametry:

* Identyfikator URI zasobu: `{IoT hub name}.azure-devices.net/devices/{device id}`,
* Klucz podpisujący: dowolnego klucza symetrycznego dla `{device id}` tożsamości,
* Brak nazwy zasad
* wszelkie czas wygaśnięcia.

Przykładem przy użyciu powyższej funkcji Node.js może być:

```nodejs
var endpoint ="myhub.azure-devices.net/devices/device1";
var deviceKey ="...";

var token = generateSasToken(endpoint, deviceKey, null, 60);
```

Wynik, która udziela dostępu do wszystkich funkcji dla device1, będzie:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697`

> [!NOTE]
> Istnieje możliwość wygenerowania tokenu sygnatury dostępu Współdzielonego, za pomocą programu .NET [explorer urządzenia] [ lnk-device-explorer] narzędzia lub i platform, na podstawie języka Python [rozszerzenia IoT Azure CLI 2.0] [ lnk-IoT-extension-CLI-2.0] narzędzia wiersza polecenia.

### <a name="use-a-shared-access-policy"></a>Użyj zasad dostępu współdzielonego

Podczas tworzenia tokenu z zasad dostępu współdzielonego, ustaw `skn` na nazwę zasady. Ta zasada musi udzielić **DeviceConnect** uprawnienia.

Są dwa główne scenariusze korzystania z funkcji urządzenia za pomocą zasad dostępu współdzielonego:

* [chmury bramy protokołu][lnk-endpoints],
* [token usługi] [ lnk-custom-auth] używaną do zaimplementowania schematy uwierzytelniania niestandardowego.

Ponieważ zasady dostępu współdzielonego potencjalnie mogą udzielać dostępu do Połącz się jako dowolnego urządzenia, należy użyć poprawny identyfikator URI zasobu, podczas tworzenia tokenów zabezpieczających. To ustawienie jest szczególnie ważne dla tokenu usługi, które zakres token do danego urządzenia przy użyciu identyfikatora URI zasobu. Ten punkt jest mniej istotne dla bramy protokołu, ponieważ są one już pośredniczących ruchu dla wszystkich urządzeń.

Na przykład usługi tokenu przy użyciu wstępnie utworzonej udostępnionych zasady dostępu o nazwie **urządzenia** utworzyć token z następującymi parametrami:

* Identyfikator URI zasobu: `{IoT hub name}.azure-devices.net/devices/{device id}`,
* Klucz podpisujący: jeden z kluczy `device` zasad,
* Nazwa zasad: `device`,
* wszelkie czas wygaśnięcia.

Przykładem przy użyciu powyższej funkcji Node.js może być:

```nodejs
var endpoint ="myhub.azure-devices.net/devices/device1";
var policyName = 'device';
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

Wynik, która udziela dostępu do wszystkich funkcji dla device1, będzie:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697&skn=device`

Brama protokołu można użyć tego samego tokenu dla wszystkich urządzeń, wystarczy wybrać ustawienie identyfikator URI zasobu do `myhub.azure-devices.net/devices`.

### <a name="use-security-tokens-from-service-components"></a>Używaj tokenów zabezpieczeń z składniki usługi

Składniki usługi można generować tylko tokeny zabezpieczające, za pomocą zasad dostępu współdzielonego przyznanie odpowiednich uprawnień, jak opisano wcześniej.

Poniżej przedstawiono funkcje usługi udostępniane w punktach końcowych:

| Endpoint | Funkcjonalność |
| --- | --- |
| `{iot hub host name}/devices` |Tworzenie, aktualizowanie, pobrać i usuwania tożsamości urządzenia. |
| `{iot hub host name}/messages/events` |Komunikaty urządzenia do chmury. |
| `{iot hub host name}/servicebound/feedback` |Otrzymasz opinię komunikatów chmury do urządzenia. |
| `{iot hub host name}/devicebound` |Wysyłanie wiadomości chmury do urządzenia. |

Na przykład usługi generowania przy użyciu wstępnie utworzone zasady dostępu o nazwie udostępnionego **registryRead** utworzyć token z następującymi parametrami:

* Identyfikator URI zasobu: `{IoT hub name}.azure-devices.net/devices`,
* Klucz podpisujący: jeden z kluczy `registryRead` zasad,
* Nazwa zasad: `registryRead`,
* wszelkie czas wygaśnięcia.

```nodejs
var endpoint ="myhub.azure-devices.net/devices";
var policyName = 'device';
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

Wynik, którym przyznano dostęp do odczytu wszystkich tożsamości urządzenia, będzie:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=registryRead`

## <a name="supported-x509-certificates"></a>Obsługiwane certyfikaty X.509

Każdy certyfikat X.509 służy do uwierzytelniania urządzenia z Centrum IoT, przekazując odcisku palca certyfikatu lub urząd certyfikacji (CA) do Centrum IoT Azure. Uwierzytelnianie przy użyciu tylko odciski palców certyfikatów sprawdza, czy przedstawioną odcisk palca odpowiada skonfigurowanym odciskiem palca. Uwierzytelnianie przy użyciu urzędu certyfikacji sprawdza łańcuch certyfikatów. 

Certyfikaty obsługiwanych obejmują:

* **Istniejący certyfikat X.509**. Urządzenie może już być skojarzone z nim certyfikatu X.509. Urządzenie może używać tego certyfikatu do uwierzytelniania za pomocą Centrum IoT. Współdziała z odciskiem palca lub uwierzytelniania urzędu certyfikacji. 
* **Podpisany przez urząd certyfikacji certyfikatu X.509**. Aby zidentyfikować urządzenia i uwierzytelniania go z Centrum IoT, można użyć certyfikatu X.509 wygenerowany i podpisany przez urząd certyfikacji (CA). Współdziała z odciskiem palca lub uwierzytelniania urzędu certyfikacji.
* **A własnym wygenerowany i X-509 certyfikat z podpisem własnym**. Producenta urządzenia lub wewnętrznych narzędzia wdrażania można wygenerować tych certyfikatów i przechowywać odpowiadające im klucze prywatne (i certyfikatu) na urządzeniu. Można użyć narzędzia, takie jak [OpenSSL] [ lnk-openssl] i [Windows SelfSignedCertificate] [ lnk-selfsigned] narzędzie do tego celu. Działa tylko z odciskiem palca uwierzytelniania. 

Urządzenie może użyć certyfikatu X.509 lub tokenu zabezpieczającego dla uwierzytelniania, ale nie oba.

Aby uzyskać więcej informacji na temat uwierzytelniania przy użyciu urzędu certyfikacji, zobacz [pojęć związanych z certyfikatami X.509 urzędu certyfikacji](iot-hub-x509ca-concept.md).

### <a name="register-an-x509-certificate-for-a-device"></a>Zarejestruj certyfikat X.509 dla urządzenia

[Azure IoT usługi SDK dla języka C#] [ lnk-service-sdk] (wersja 1.0.8+) obsługuje rejestrowanie urządzenia, który używa certyfikatu X.509 do uwierzytelniania. Innych interfejsów API, takich jak importu/eksportu urządzeń obsługuje także certyfikaty X.509.

### <a name="c-support"></a>C\# pomocy technicznej

**RegistryManager** klasy zapewnia programowy sposób zarejestrować urządzenie. W szczególności **AddDeviceAsync** i **UpdateDeviceAsync** metody umożliwiają rejestrowanie i aktualizowanie urządzenia w rejestrze tożsamości Centrum IoT. Te dwie metody przyjmują **urządzenia** wystąpienia jako dane wejściowe. **Urządzenia** klasa zawiera **uwierzytelniania** właściwość, która pozwala na określenie odciski palców certyfikatów X.509 podstawowego i pomocniczego. Odcisk palca reprezentuje skrót SHA256 certyfikatu X.509 (przechowywane przy użyciu kodowania binarnego DER). Istnieje możliwość określenia podstawowego odcisk palca lub dodatkowej odcisk palca lub oba. Podstawowe i pomocnicze odciski palców są obsługiwane do obsługi scenariuszy przerzucania certyfikatu.

Poniżej przedstawiono przykładowe C\# fragment kodu do zarejestrowania urządzenia przy użyciu odcisku palca certyfikatu X.509:

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

### <a name="use-an-x509-certificate-during-run-time-operations"></a>Użyj certyfikatu X.509 podczas operacji w czasie wykonywania

[Urządzenia Azure IoT SDK dla platformy .NET] [ lnk-client-sdk] (wersja 1.0.11+) obsługuje korzystanie z certyfikatów X.509.

### <a name="c-support"></a>C\# pomocy technicznej

Klasa **DeviceAuthenticationWithX509Certificate** obsługuje tworzenie **DeviceClient** wystąpienia za pomocą certyfikatu X.509. Certyfikat X.509 musi być w formacie PFX (nazywany również PKCS #12), który zawiera klucz prywatny.

Oto fragment kodu próbki:

```csharp
var authMethod = new DeviceAuthenticationWithX509Certificate("<device id>", x509Certificate);

var deviceClient = DeviceClient.Create("<IotHub DNS HostName>", authMethod);
```

## <a name="custom-device-and-module-authentication"></a>Niestandardowe uwierzytelnianie urządzenia i modułu

Można użyć Centrum IoT [rejestru tożsamości] [ lnk-identity-registry] skonfigurować poświadczenia na urządzeniu/modułowe zabezpieczeń i uzyskiwać dostęp za pomocą formantu [tokenów] [ lnk-sas-tokens]. Jeśli rozwiązania IoT już schemat rejestru i/lub uwierzytelnianie tożsamości niestandardowej, należy rozważyć utworzenie *token usługi* tej infrastruktury jest integrowana z Centrum IoT. W ten sposób można użyć innych funkcji IoT w rozwiązaniu.

Token usługi to usługa w chmurze niestandardowych. Używa Centrum IoT *udostępnionych zasad dostępu* z **DeviceConnect** lub **ModuleConnect** uprawnienia do tworzenia *zakres urządzeń* lub *zakres modułu* tokenów. Tokeny te włączyć urządzenia i moduł nawiązać połączenia z Centrum IoT.

![Kroki wzorca usługi tokenu][img-tokenservice]

Oto główne kroki wzorzec usługi tokenu:

1. Tworzenie zasad dostępu do udostępnionego Centrum IoT z **DeviceConnect** lub **ModuleConnect** uprawnienia Centrum IoT. Można utworzyć te zasady w [portalu Azure] [ lnk-management-portal] lub programowo. Usługa tokenu używa tej zasady do podpisywania tokenów, który tworzy.
1. Gdy urządzenie/modułu ma dostęp do Centrum IoT, żądań podpisany token z usługą tokenu. Urządzenia mogą uwierzytelniać za pomocą schematem rejestru/uwierzytelniania tożsamość niestandardowa, można określić tożsamości urządzenia/modułu, token używany przez usługę do utworzenia tokenu.
1. Usługa tokenu zwraca token. Token jest tworzona przy użyciu `/devices/{deviceId}` lub `/devices/{deviceId}/module/{moduleId}` jako `resourceURI`, z `deviceId` jako urządzenie uwierzytelnianego lub `moduleId` jako moduł jest uwierzytelniane. Token usługi używane są zasady dostępu współdzielonego do utworzenia tokenu.
1. Urządzenie/modułu używa tokenu bezpośrednio z Centrum IoT.

> [!NOTE]
> Można użyć klasy .NET [SharedAccessSignatureBuilder] [ lnk-dotnet-sas] lub Klasa Java [IotHubServiceSasToken] [ lnk-java-sas] do utworzenia tokenu w sieci Usługa tokenu.

Usługa tokenu można ustawić wygaśnięcia tokenu zgodnie z potrzebami. Po wygaśnięciu tokenu Centrum IoT serwery połączenia urządzenia/modułu. Następnie urządzenia/modułu należy zażądać nowego tokenu z usługi tokenu. Czas wygaśnięcia krótkich spowoduje zwiększenie obciążenia na urządzeniu/modułu i usługi tokenu.

Dla urządzenia/modułu do nawiązania połączenia z koncentratorem, należy nadal go dodać w rejestrze tożsamości Centrum IoT — mimo że it jest używany do nawiązywania połączenia token, a nie kluczem. W związku z tym można nadal używać kontroli dostępu na urządzenie/na — moduł przez włączenie lub wyłączenie tożsamości urządzenia/modułu w [rejestru tożsamości][lnk-identity-registry]. Takie podejście zmniejsza ryzyko użycia tokeny wraz z czasem wygaśnięcia długi.

### <a name="comparison-with-a-custom-gateway"></a>Porównanie z bramą niestandardowych

Wzorzec usługi tokenu jest zalecanym sposobem implementować schemat rejestru/uwierzytelniania tożsamość niestandardowa z Centrum IoT. Ten wzorzec jest zalecane, ponieważ nadal obsługuje większość ruchu rozwiązania IoT Hub. Jednak jeśli schemat uwierzytelniania niestandardowego więc jest sprzężony z protokołem, mogą wymagać *bram* do przetwarzania ruchu sieciowego. Przykładem takiej sytuacji jest przy użyciu[zabezpieczeń TLS (Transport Layer) i kluczy wstępnych (PSKs)][lnk-tls-psk]. Aby uzyskać więcej informacji, zobacz [bramy protokołu] [ lnk-protocols] artykułu.

## <a name="reference-topics"></a>Tematy odwołań:

Następujące tematy dokumentacji dostarczają więcej informacji na temat kontrolowania dostępu do Centrum IoT.

## <a name="iot-hub-permissions"></a>Centrum IoT uprawnień

W poniższej tabeli wymieniono uprawnienia, których można użyć do kontrolowania dostępu do Centrum IoT.

| Uprawnienie | Uwagi |
| --- | --- |
| **RegistryRead** |Przyznaje dostęp do odczytu w rejestrze tożsamości. Aby uzyskać więcej informacji, zobacz [rejestru tożsamości][lnk-identity-registry]. <br/>To uprawnienie jest używany przez usługi w chmurze zaplecza. |
| **RegistryReadWrite** |Przyznaje uprawnienia odczytu i zapisu w rejestrze tożsamości. Aby uzyskać więcej informacji, zobacz [rejestru tożsamości][lnk-identity-registry]. <br/>To uprawnienie jest używany przez usługi w chmurze zaplecza. |
| **ServiceConnect** |Udziela dostępu do komunikacji i monitorowania punktów końcowych połączonej usługi w chmurze. <br/>Udziela uprawnień do odbierania komunikatów urządzenia do chmury, wysyłać chmury do urządzenia i pobrać odpowiedni potwierdzeń dostarczenia. <br/>Przyznaje uprawnienia do pobierania potwierdzeń dostarczenia pliku operacji przekazywania. <br/>Udziela uprawnień do dostępu twins zaktualizować znaczniki i odpowiednie właściwości pobierania właściwości zgłoszone i uruchamianie zapytań. <br/>To uprawnienie jest używany przez usługi w chmurze zaplecza. |
| **DeviceConnect** |Zapewnia dostęp do urządzeń połączonych z punktów końcowych. <br/>Udziela uprawnień do urządzenia do chmury wysyłać i odbierać wiadomości chmury do urządzenia. <br/>Przyznaje uprawnienia do wykonywania przekazywania pliku z urządzenia. <br/>Udziela uprawnień, aby otrzymywać powiadomienia o właściwości dwie żądanego urządzenia i zaktualizować dwie urządzenia zgłoszone właściwości. <br/>Przyznaje uprawnienia do wykonywania pliku operacji przekazywania. <br/>To uprawnienie jest używany przez urządzenia. |

## <a name="additional-reference-material"></a>Odwołanie dodatkowe materiały

Inne tematy referencyjne w Podręczniku dewelopera Centrum IoT obejmują:

* [Punkty końcowe Centrum IoT] [ lnk-endpoints] opisano różne punkty końcowe, które udostępnia każdego centrum IoT dla operacji zarządzania i środowiska wykonawczego.
* [Ograniczenia przepustowości i przydziały] [ lnk-quotas] opisano przydziały i ograniczenia przepustowości zachowania, które dotyczą usługi Centrum IoT.
* [Zestawy Azure IoT urządzenia i usługi SDK] [ lnk-sdks] wymieniono języka różnych zestawów SDK, można użyć podczas opracowywania aplikacji usług i urządzeń, które współdziałają z Centrum IoT.
* [Język zapytań Centrum IoT] [ lnk-query] opisuje język kwerendy można pobrać z Centrum IoT informacji o twins urządzenia i zadania.
* [Obsługa MQTT Centrum IoT] [ lnk-devguide-mqtt] zapewnia więcej informacji na temat Centrum IoT obsługi protokołu MQTT.

## <a name="next-steps"></a>Kolejne kroki

Teraz, kiedy znasz sterowanie dostępem Centrum IoT, mogą być zainteresowane w następujących tematach przewodnik dewelopera Centrum IoT:

* [Użyj twins urządzenia, aby zsynchronizować stanu i konfiguracji][lnk-devguide-device-twins]
* [Wywoływanie metody bezpośrednio na urządzeniu][lnk-devguide-directmethods]
* [Planowanie zadań na wielu urządzeniach][lnk-devguide-jobs]

Jeśli chcesz wypróbować niektóre pojęcia opisane w tym artykule, zobacz następujące samouczki Centrum IoT:

* [Rozpoczynanie pracy z Centrum IoT Azure][lnk-getstarted-tutorial]
* [Jak wysyłać wiadomości chmury do urządzenia z Centrum IoT][lnk-c2d-tutorial]
* [Jak komunikaty procesu Centrum IoT urządzenia do chmury][lnk-d2c-tutorial]

<!-- links and images -->

[img-tokenservice]: ./media/iot-hub-devguide-security/tokenservice.png
[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-openssl]: https://www.openssl.org/
[lnk-selfsigned]: https://technet.microsoft.com/library/hh848633

[lnk-resource-provider-apis]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-sas-tokens]: iot-hub-devguide-security.md#security-tokens
[lnk-amqp]: https://www.amqp.org/
[lnk-azure-resource-manager]: ../azure-resource-manager/resource-group-overview.md
[lnk-cbs]: https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc
[lnk-event-hubs-publisher-policy]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab
[lnk-management-portal]: https://portal.azure.com
[lnk-sasl-plain]: http://tools.ietf.org/html/rfc4616
[lnk-identity-registry]: iot-hub-devguide-identity-registry.md
[lnk-dotnet-sas]: https://msdn.microsoft.com/library/microsoft.azure.devices.common.security.sharedaccesssignaturebuilder.aspx
[lnk-java-sas]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service.auth._iot_hub_service_sas_token
[lnk-tls-psk]: https://tools.ietf.org/html/rfc4279
[lnk-protocols]: iot-hub-protocol-gateway.md
[lnk-custom-auth]: iot-hub-devguide-security.md#custom-device-and-module-authentication
[lnk-x509]: iot-hub-devguide-security.md#supported-x509-certificates
[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-service-sdk]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/service
[lnk-client-sdk]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/device
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer
[lnk-IoT-extension-CLI-2.0]: https://github.com/Azure/azure-iot-cli-extension

[lnk-getstarted-tutorial]: iot-hub-csharp-csharp-getstarted.md
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md
[lnk-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
