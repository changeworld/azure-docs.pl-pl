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
ms.openlocfilehash: 350c2bf3c4d0fc0a16f1b393e7c8d8a372679797
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78331148"
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
export SSL_CERT_DIR=/opt/ssl/certs
```

- OPENSSLDIR jest `/etc/pki/tls` (podobnie jak w systemach opartych na RHEL/CentOS). `certs` podkatalog z plikiem pakietu certyfikatów, na przykład `ca-bundle.crt`.
Ustaw zmienną środowiskową `SSL_CERT_FILE`, aby wskazywała ten plik przed uruchomieniem programu korzystającego z zestawu Speech SDK. Na przykład:
```bash
export SSL_CERT_FILE=/etc/pki/tls/certs/ca-bundle.crt
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Informacje o zestawie SDK mowy](speech-sdk.md)
