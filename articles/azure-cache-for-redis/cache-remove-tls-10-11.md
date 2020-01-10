---
title: Usuń protokoły TLS 1,0 i 1,1 z używania z usługą Azure cache for Redis
description: Dowiedz się, jak usunąć protokoły TLS 1,0 i 1,1 z aplikacji podczas komunikowania się z usługą Azure cache for Redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: yegu
ms.openlocfilehash: 2f6203deb5e06ba69a3b4d06297d5e702992c79d
ms.sourcegitcommit: f2149861c41eba7558649807bd662669574e9ce3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/07/2020
ms.locfileid: "75708060"
---
# <a name="remove-tls-10-and-11-from-use-with-azure-cache-for-redis"></a>Usuń protokoły TLS 1,0 i 1,1 z używania z usługą Azure cache for Redis

Na wyłączne korzystanie z Transport Layer Security (TLS) w wersji 1,2 lub nowszej jest na całym branżą. Protokoły TLS w wersji 1,0 i 1,1 są podatne na ataki, takie jak BEAST i POODLE, a także zawierają inne słabe luki w zabezpieczeniach i zagrożenia (CVE). Nie obsługują one również nowoczesnych metod szyfrowania i mechanizmów szyfrowania zalecanych przez standardy zgodności z kartą płatniczą (PCI). Ten [Blog dotyczący zabezpieczeń protokołu TLS](https://www.acunetix.com/blog/articles/tls-vulnerabilities-attacks-final-part/) wyjaśnia niektóre z tych luk w bardziej szczegółowy sposób.

W ramach tego wysiłku wprowadzamy następujące zmiany w usłudze Azure cache dla Redis:

* Od 13 stycznia 2020 skonfigurujemy domyślną minimalną wersję protokołu TLS równą 1,2 dla nowo utworzonych wystąpień pamięci podręcznej.  Istniejące wystąpienia pamięci podręcznej nie zostaną zaktualizowane w tym momencie.  W razie potrzeby będziesz mieć możliwość [zmiany minimalnej wersji protokołu TLS](cache-configure.md#access-ports) z powrotem do 1,0 lub 1,1 w celu zapewnienia zgodności z poprzednimi wersjami.  Tę zmianę można wykonać za pomocą Azure Portal lub innych interfejsów API zarządzania.
* Od 31 marca 2020 zaprzestanie obsługi protokołu TLS w wersji 1,0 i 1,1. Po tej zmianie aplikacja będzie musiała korzystać z protokołu TLS 1,2 lub nowszego do komunikowania się z pamięcią podręczną.

Ponadto w ramach tej zmiany zostanie usunięta pomoc techniczna dla starszych, niezabezpieczonych pakietów szyfr.  Nasze obsługiwane pakiety szyfr zostaną ograniczone do następujących, gdy pamięć podręczna zostanie skonfigurowana z minimalną wersją protokołu TLS 1,2.

* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P384
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256

Ten artykuł zawiera ogólne wskazówki dotyczące wykrywania zależności od wcześniejszych wersji protokołu TLS i usuwania ich z aplikacji.

## <a name="check-whether-your-application-is-already-compliant"></a>Sprawdź, czy aplikacja jest już zgodna

Najprostszym sposobem, aby dowiedzieć się, czy aplikacja będzie współdziałać z protokołem TLS 1,2, to ustawienie **minimalnej wartości wersji TLS** na TLS 1,2 na testowej lub tymczasowej pamięci podręcznej, z której korzysta. Ustawienie **minimalnej wersji protokołu TLS** znajduje się w [ustawieniach zaawansowanych](cache-configure.md#advanced-settings) wystąpienia pamięci podręcznej w Azure Portal. Jeśli aplikacja będzie działać zgodnie z oczekiwaniami po tej zmianie, prawdopodobnie jest zgodna. Może być konieczne skonfigurowanie niektórych bibliotek klienta Redis używanych przez aplikację w celu włączenia protokołu TLS 1,2, dzięki czemu mogą oni łączyć się z usługą Azure cache dla Redis za pośrednictwem tego protokołu zabezpieczeń.

## <a name="configure-your-application-to-use-tls-12"></a>Konfigurowanie aplikacji do korzystania z protokołu TLS 1,2

Większość aplikacji korzysta z bibliotek klienckich Redis do obsługi komunikacji z pamięciami podręcznymi. Poniżej znajdują się instrukcje dotyczące konfigurowania niektórych popularnych bibliotek klientów w różnych językach programowania i strukturach w celu użycia protokołu TLS 1,2.

### <a name="net-framework"></a>.NET Framework

Klienci platformy Redis .NET domyślnie używają najstarszej wersji protokołu TLS w .NET Framework 4.5.2 lub starszym i używają najnowszej wersji protokołu TLS na platformie .NET Framework 4,6 lub nowszej. Jeśli używasz starszej wersji .NET Framework, można ręcznie włączyć protokół TLS 1,2:

* **Stackexchange. Redis:** W parametrach połączenia należy ustawić `ssl=true` i `sslprotocols=tls12`.
* **ServiceStack. Redis:** Postępuj zgodnie z [instrukcjami dotyczącymi ServiceStack. Redis](https://github.com/ServiceStack/ServiceStack.Redis/pull/247).

### <a name="net-core"></a>.NET Core

Klienci programu Redis .NET Core domyślnie używają najnowszej wersji protokołu TLS.

### <a name="java"></a>Java

Klienci Java Redis używają protokołu TLS 1,0 w wersji 6 lub starszej. Jedis, sałata i Redisson nie mogą połączyć się z pamięcią podręczną platformy Azure dla Redis, jeśli protokół TLS 1,0 jest wyłączony w pamięci podręcznej. Uaktualnij strukturę języka Java, aby korzystać z nowych wersji protokołu TLS.

W przypadku języka Java 7 klienci Redis domyślnie nie używają protokołu TLS 1,2, ale można go skonfigurować dla niego. Jedis pozwala określić podstawowe ustawienia protokołu TLS przy użyciu następującego fragmentu kodu:

``` Java
SSLSocketFactory sslSocketFactory = (SSLSocketFactory) SSLSocketFactory.getDefault();
SSLParameters sslParameters = new SSLParameters();
sslParameters.setEndpointIdentificationAlgorithm("HTTPS");
sslParameters.setProtocols(new String[]{"TLSv1.2"});
 
URI uri = URI.create("rediss://host:port");
JedisShardInfo shardInfo = new JedisShardInfo(uri, sslSocketFactory, sslParameters, null);
 
shardInfo.setPassword("cachePassword");
 
Jedis jedis = new Jedis(shardInfo);
```

Klienci sałaty i Redisson nie obsługują jeszcze określania wersji protokołu TLS, więc będą przerywane, jeśli pamięć podręczna akceptuje tylko połączenia TLS 1,2. Poprawki dla tych klientów są przeglądane, więc sprawdź te pakiety pod kątem zaktualizowanej wersji za pomocą tej obsługi.

W języku Java 8 protokół TLS 1,2 jest domyślnie używany i nie należy w większości przypadków wymagać aktualizacji konfiguracji klienta. Aby można było bezpiecznie, Przetestuj aplikację.

### <a name="nodejs"></a>Node.js

Węzeł Redis i IORedis domyślnie używają protokołu TLS 1,2.

### <a name="php"></a>PHP

Predis w języku PHP 7 nie będzie działał, ponieważ PHP 7 obsługuje tylko protokół TLS 1,0. W przypadku środowiska PHP w wersji 7.2.1 lub starszej Predis domyślnie używa protokołu TLS 1,0 lub 1,1. Podczas tworzenia wystąpienia klienta można określić protokół TLS 1,2:

``` PHP
$redis=newPredis\Client([
    'scheme'=>'tls',
    'host'=>'host',
    'port'=>6380,
    'password'=>'password',
    'ssl'=>[
        'crypto_type'=>STREAM_CRYPTO_METHOD_TLSv1_2_CLIENT,
    ],
]);
```

W przypadku języka PHP 7,3 lub nowszego Predis używa najnowszej wersji protokołu TLS.

PhpRedis nie obsługuje protokołu TLS w żadnej wersji języka PHP.

### <a name="python"></a>Python

Redis-PR domyślnie używa protokołu TLS 1,2.

### <a name="go"></a>JĘZYK GO

Redigo domyślnie używa protokołu TLS 1,2.

## <a name="additional-information"></a>Dodatkowe informacje

- [Jak skonfigurować usługę Azure cache for Redis](cache-configure.md)
