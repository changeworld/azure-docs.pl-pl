---
title: Jak skonfigurować OpenSSL dla systemu Linux
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
ms.openlocfilehash: cadf31dede8ee81323076013d00b9431f597bda6
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/16/2020
ms.locfileid: "76156492"
---
# <a name="configure-openssl-for-linux"></a>Konfigurowanie OpenSSL dla systemu Linux

W przypadku korzystania z dowolnej wersji zestawu Speech SDK przed 1.9.0, [OpenSSL](https://www.openssl.org) jest konfigurowana dynamicznie z wersją systemu hosta. W nowszych wersjach zestawu Speech SDK OpenSSL (wersja [1.1.1 b](https://mta.openssl.org/pipermail/openssl-announce/2019-February/000147.html)) jest statycznie połączony z podstawową biblioteką zestawu Speech SDK.

## <a name="troubleshoot-connectivity"></a>Rozwiązywanie problemów z łącznością

Jeśli wystąpiły błędy połączeń podczas korzystania z wersji 1.9.0 zestawu Speech SDK, upewnij się, że katalog `ssl/certs` istnieje w `/usr/lib` Directory, który znajduje się w systemie plików Linux. Jeśli katalog `ssl/certs` *nie istnieje*, sprawdź, gdzie w systemie jest zainstalowany program OpenSSL, przy użyciu następującego polecenia:

```bash
which openssl
```

Następnie zlokalizuj katalog OpenSSL `certs` i skopiuj zawartość tego katalogu do katalogu `/usr/lib/ssl/certs`. Następnie spróbuj ponownie sprawdzić, czy problemy z połączeniem zostały rozwiązane.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Informacje o zestawie SDK mowy](speech-sdk.md)