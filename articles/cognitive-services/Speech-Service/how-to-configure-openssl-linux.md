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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78331148"
---
# <a name="configure-openssl-for-linux"></a>Konfigurowanie biblioteki OpenSSL dla systemu Linux

W przypadku korzystania z dowolnej wersji SDK mowy przed 1.9.0, [OpenSSL](https://www.openssl.org) jest dynamicznie skonfigurowany do wersji systemu hosta. W nowszych wersjach SDK mowy OpenSSL (wersja [1.1.1b)](https://mta.openssl.org/pipermail/openssl-announce/2019-February/000147.html)jest statycznie połączony z podstawową biblioteką SDK mowy.

Aby zapewnić łączność, sprawdź, czy certyfikaty OpenSSL zostały zainstalowane w systemie. Uruchom polecenie:
```bash
openssl version -d
```

Dane wyjściowe w systemach opartych na Ubuntu/Debianie powinny być następujące:
```
OPENSSLDIR: "/usr/lib/ssl"
```

Sprawdź, czy `certs` w obszarze OPENSSLDIR nie ma podkatalogu. W powyższym przykładzie `/usr/lib/ssl/certs`będzie .

* Jeśli istnieje `/usr/lib/ssl/certs` i zawiera wiele pojedynczych `.crt` `.pem` plików certyfikatów (z lub rozszerzeniem), nie ma potrzeby dalszych działań.

* Jeśli OPENSSLDIR jest `/usr/lib/ssl` czymś innym niż i/lub zamiast wielu pojedynczych plików znajduje się plik pakietu pojedynczego certyfikatu, należy ustawić odpowiednią zmienną środowiskową SSL, aby wskazać, gdzie można znaleźć certyfikaty.

## <a name="examples"></a>Przykłady

- OPENSSLDIR `/opt/ssl`jest . Istnieje `certs` podkatalog `.crt` z `.pem` wieloma plikami lub plikami.
Ustaw zmienną `SSL_CERT_DIR` środowiskową, `/opt/ssl/certs` aby wskazać przed uruchomieniem programu, który używa zestawu SDK mowy. Przykład:
```bash
export SSL_CERT_DIR=/opt/ssl/certs
```

- OPENSSLDIR `/etc/pki/tls` jest (podobnie jak w systemach opartych na RHEL/CentOS). Istnieje `certs` podkatalog z plikiem pakietu `ca-bundle.crt`certyfikatów, na przykład .
Przed uruchomieniem programu korzystającego z zestawu SDK mowy należy ustawić zmienną `SSL_CERT_FILE` środowiskową, aby wskazywała ten plik. Przykład:
```bash
export SSL_CERT_FILE=/etc/pki/tls/certs/ca-bundle.crt
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Informacje o zestawie SDK usługi Mowa](speech-sdk.md)
