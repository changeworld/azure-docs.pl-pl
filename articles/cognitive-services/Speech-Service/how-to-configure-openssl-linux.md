---
title: Jak skonfigurować bibliotekę OpenSSL dla systemu Linux
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak skonfigurować OpenSSL dla systemu Linux.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: jhakulin
ms.openlocfilehash: ff8772f7c3c3213c010b0bdbd0d0aa8897404bac
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/11/2020
ms.locfileid: "77119984"
---
# <a name="configure-openssl-for-linux"></a>Konfigurowanie biblioteki OpenSSL dla systemu Linux

W przypadku korzystania z dowolnej wersji zestawu Speech SDK przed 1.9.0, [OpenSSL](https://www.openssl.org) jest konfigurowana dynamicznie z wersją systemu hosta. W nowszych wersjach zestawu Speech SDK OpenSSL (wersja [1.1.1 b](https://mta.openssl.org/pipermail/openssl-announce/2019-February/000147.html)) jest statycznie połączony z podstawową biblioteką zestawu Speech SDK.

Aby zapewnić łączność, sprawdź, czy certyfikaty OpenSSL zostały zainstalowane w systemie. Uruchom polecenie:
```bash
openssl version -d
```

Dane wyjściowe w systemach opartych na systemie Ubuntu/Debian powinny być następujące:
```
OPENSSLDIR: "/usr/lib/ssl"
```

Sprawdź, czy `certs` podkatalogu w obszarze OPENSSLDIR. W powyższym przykładzie `/usr/lib/ssl/certs`.

* Jeśli `/usr/lib/ssl/certs` i zawiera wiele pojedynczych plików certyfikatów (z rozszerzeniem `.crt` lub `.pem`), nie ma potrzeby wykonywania dalszych akcji.

* Jeśli OPENSSLDIR jest coś innego niż `/usr/lib/ssl` i/lub istnieje pojedynczy plik pakietu certyfikatów zamiast wielu pojedynczych plików, należy ustawić odpowiednią zmienną środowiskową SSL, aby wskazać, gdzie można znaleźć certyfikaty.

## <a name="examples"></a>Przykłady

- OPENSSLDIR jest `/opt/ssl`. Istnieje `certs` podkatalog z wieloma plikami `.crt` lub `.pem`.
Ustaw zmienną środowiskową `SSL_CERT_DIR` na `/opt/ssl/certs` przed uruchomieniem programu korzystającego z zestawu Speech SDK. Na przykład:
```bash
SSL_CERT_DIR=/opt/ssl/certs ./helloworld
```

- OPENSSLDIR jest `/etc/pki/tls`. Istnieje plik pakietu certyfikatów, na przykład `ca-bundle.pem` lub `ca-bundle.crt`.
Ustaw zmienną środowiskową `SSL_CERT_FILE` na `/etc/pki/tls/ca-bundle.pem` przed uruchomieniem programu korzystającego z zestawu Speech SDK. Na przykład:
```bash
SSL_CERT_FILE=/etc/pki/tls/ca-bundle.pem ./helloworld
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Informacje o zestawie SDK mowy](speech-sdk.md)
