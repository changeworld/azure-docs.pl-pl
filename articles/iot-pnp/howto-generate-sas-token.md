---
title: Generowanie tokenu zabezpieczającego w celu uzyskania dostępu do repozytorium Podglądu ioT Plug and Play | Dokumenty firmy Microsoft
description: Generowanie tokenu podpisu dostępu udostępnionego do użycia podczas programowania uzyskiwania dostępu do repozytorium modelu Podglądu IoT i odtworzenia.
author: Philmea
ms.author: philmea
ms.date: 12/27/2019
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: f008627317588467d731ccc03aec7738f58e46e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159204"
---
# <a name="generate-sas-token"></a>Generowanie tokenu Sygnatury dostępu Współ

W tym przewodniku przedstawiono sposób programowego generowania tokenu podpisu dostępu udostępnionego (SAS) do użycia z interfejsami API repozytorium iposytorium iodnowy IoT Preview.

## <a name="python"></a>Python

Poniższy fragment kodu pokazuje, jak wygenerować token sygnatury dostępu Współdzielonego przy użyciu języka Python:

```python
from base64 import b64decode, b64encode
from hashlib import sha256
from hmac import digest
from time import time
from urllib.parse import quote_plus, urlencode

def calculate_sas_token(hostname, repo_id, key_name, key, expiry_in_second):
    expire = int(time() + expiry_in_second)
    sign_value = "{0}\n{1}\n{2}".format(repo_id, quote_plus(hostname), expire)
    sig = b64encode(digest(b64decode(key), sign_value.encode("utf-8"), sha256))
    token = "SharedAccessSignature " + urlencode({
        "sr": hostname, 
        "sig": sig,
        "se": str(expire),
        "skn": key_name,
        "rid": repo_id
        })
    return token
```

## <a name="c"></a>C\#

Poniższy fragment kodu pokazuje, jak wygenerować\#token sygnatury dostępu Współdzielonego przy użyciu języka C:

```csharp
public static string generateSasToken(string hostName, string repoId, string key, string keyName, int expiryInSeconds = 3600)
{
    TimeSpan fromEpochStart = DateTime.UtcNow - new DateTime(1970, 1, 1);
    string expiry = Convert.ToString((int)fromEpochStart.TotalSeconds + expiryInSeconds);

    string stringToSign = repoId + "\n" + WebUtility.UrlEncode(hostName) + "\n" + expiry;

    HMACSHA256 hmac = new HMACSHA256(Convert.FromBase64String(key));
    string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));

    string token = String.Format(
        CultureInfo.InvariantCulture,
        "SharedAccessSignature sr={0}&sig={1}&se={2}&skn={3}&rid={4}",
        WebUtility.UrlEncode(hostName),
        WebUtility.UrlEncode(signature),
        expiry,
        keyName,
        repoId);

    return token;
}
```

## <a name="use-the-sas-token"></a>Użyj tokenu sygnatury dostępu Współdzielonego

Po wygenerowaniu tokenu sygnatury dostępu Współdzielonego można go użyć do wykonania żądania HTTP POST. Przykład:

```text
POST https:///models/{modelId}?repositoryId={repositoryId}&api-version=2019-07-01-preview
```

Jeśli przyznasz klientowi token sygnatury dostępu Współdzielonego, klient nie ma klucza podstawowego zasobu i nie może odwrócić skrótu, aby go uzyskać. Token sygnatury dostępu współdzielonego zapewnia kontrolę nad tym, do czego klient może uzyskać dostęp i jak długo. Po zmianie klucza podstawowego w zasadach wszystkie tokeny sygnatury dostępu Współdzielonego utworzone z niego są unieważnione.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy dowiedziałeś się o generowaniu tokenów zabezpieczających używanych do uzyskiwania dostępu do repozytoriów modeli IoT Plug and Play Preview, sugerowanym następnym krokiem jest dowiedzenie się więcej w [przewodniku dla deweloperów modelowania IoT Plug and Play Preview.](concepts-developer-guide.md)
