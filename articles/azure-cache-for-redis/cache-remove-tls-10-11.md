---
title: Usuwanie protokołu TLS 1.0 i 1.1 z użycia za pomocą usługi Azure Cache for Redis
description: Dowiedz się, jak usunąć tls 1.0 i 1.1 z aplikacji podczas komunikowania się z pamięcią podręczną Azure dla redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: yegu
ms.openlocfilehash: 809fbe85a9783777d5dbef86357bd5a386bd6f81
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261247"
---
# <a name="remove-tls-10-and-11-from-use-with-azure-cache-for-redis"></a>Usuwanie protokołu TLS 1.0 i 1.1 z użycia za pomocą usługi Azure Cache for Redis

Istnieje ogólnobranżowy nacisk na wyłączne korzystanie z usługi TLS (Transport Layer Security) w wersji 1.2 lub nowszej. Wiadomo, że tls w wersjach 1.0 i 1.1 jest podatny na ataki, takie jak BEAST i PUDEL, oraz ma inne słabe punkty działania i słabości typowych luk w zabezpieczeniach (CVE). Nie obsługują również nowoczesnych metod szyfrowania i zestawów szyfrowania zalecanych przez standardy zgodności z kartami płatniczymi (PCI). Ten [blog o zabezpieczeniach TLS](https://www.acunetix.com/blog/articles/tls-vulnerabilities-attacks-final-part/) wyjaśnia bardziej szczegółowo niektóre z tych luk.

W ramach tych wysiłków będziemy wszłać następujące zmiany w pamięci podręcznej platformy Azure dla firmy Redis:

* **Faza 1:** Skonfigurujemy domyślną minimalną wersję TLS na 1.2 dla nowo utworzonych wystąpień pamięci podręcznej. (Kiedyś był to TLS 1.0.) Istniejące wystąpienia pamięci podręcznej nie zostaną zaktualizowane w tym momencie. W razie potrzeby możesz [zmienić minimalną wersję protokołu TLS](cache-configure.md#access-ports) na 1.0 lub 1.1 w celu zapewnienia zgodności z powrotem. Tę zmianę można wykonać za pomocą witryny Azure portal lub innych interfejsów API zarządzania.
* **Faza 2:** Przestaniemy obsługiwać TLS w wersjach 1.0 i 1.1. Po tej zmianie aplikacja będzie wymagana do używania protokołu TLS 1.2 lub nowszego do komunikowania się z pamięcią podręczną.

Ponadto w ramach tej zmiany usuniemy obsługę starszych, niezabezpieczonych pakietów cypher.  Nasze obsługiwane pakiety cypher będą ograniczone do następujących, gdy pamięć podręczna jest skonfigurowana z minimalną wersją TLS 1.2.

* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P384
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256

Ten artykuł zawiera ogólne wskazówki dotyczące sposobu wykrywania zależności od tych wcześniejszych wersji protokołu TLS i usuwania ich z aplikacji.

Daty, kiedy te zmiany wejdą w życie, to:

| Chmura               | Data rozpoczęcia fazy 1 | Faza 2 Data rozpoczęcia      |
|---------------------|--------------------|-------------------------|
| Platforma Azure (globalna)      |  13 stycznia 2020 r.  | 11 maja 2020 r. (rozszerzony) |
| Azure Government    |  13 marca 2020 r.    | 11 maja 2020 r.            |
| Azure (Niemcy)       |  13 marca 2020 r.    | 11 maja 2020 r.            |
| Azure China         |  13 marca 2020 r.    | 11 maja 2020 r.            |

## <a name="check-whether-your-application-is-already-compliant"></a>Sprawdź, czy aplikacja jest już zgodna

Najprostszym sposobem, aby dowiedzieć się, czy aplikacja będzie współpracować z TLS 1.2 jest ustawienie minimalnej wartości **wersji TLS** do TLS 1.2 w teście lub przemieszczania pamięci podręcznej, który używa. **Ustawienie minimalna wersja TLS** znajduje się w [ustawieniach zaawansowanych](cache-configure.md#advanced-settings) wystąpienia pamięci podręcznej w witrynie Azure portal. Jeśli aplikacja nadal działa zgodnie z oczekiwaniami po tej zmianie, prawdopodobnie jest zgodna. Może być konieczne skonfigurowanie niektórych bibliotek klienta Redis używanych przez aplikację specjalnie w celu włączenia protokołu TLS 1.2, aby mogły łączyć się z usługą Azure Cache for Redis za pomocą tego protokołu zabezpieczeń.

## <a name="configure-your-application-to-use-tls-12"></a>Konfigurowanie aplikacji do używania protokołu TLS 1.2

Większość aplikacji używa bibliotek klienta Redis do obsługi komunikacji z ich pamięci podręcznych. Poniżej przedstawiono instrukcje konfigurowania niektórych popularnych bibliotek klienckich w różnych językach programowania i ramach do używania protokołu TLS 1.2.

### <a name="net-framework"></a>.NET Framework

Klienci Redis .NET domyślnie używają najwcześniejszej wersji protokołu TLS w programie .NET Framework 4.5.2 lub starszym i używają najnowszej wersji protokołu TLS w programie .NET Framework 4.6 lub nowszym. Jeśli używasz starszej wersji programu .NET Framework, możesz ręcznie włączyć funkcję TLS 1.2:

* **StackExchange.Redis:** Ustaw `ssl=true` `sslprotocols=tls12` i w ciągu połączenia.
* **ServiceStack.Redis:** Postępuj zgodnie z instrukcjami [ServiceStack.Redis](https://github.com/ServiceStack/ServiceStack.Redis#servicestackredis-ssl-support) i wymaga ServiceStack.Redis v5.6 co najmniej.

### <a name="net-core"></a>.NET Core

Klienci Redis .NET Core domyślnie używają najnowszej wersji protokołu TLS.

### <a name="java"></a>Java

Klienci Redis Java używają protokołu TLS 1.0 w wersji Java 6 lub wcześniejszej. Jedis, Sałata i Redisson nie można połączyć się z pamięcią podręczną Azure dla redis, jeśli TLS 1.0 jest wyłączona w pamięci podręcznej. Uaktualnij platformę Java, aby używać nowych wersji TLS.

W przypadku oprogramowania Java 7 klienci Redis domyślnie nie używają protokołu TLS 1.2, ale można go skonfigurować. Jedis umożliwia określenie podstawowych ustawień TLS za pomocą następującego fragmentu kodu:

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

Klienci Lettuce i Redisson nie obsługują jeszcze określania wersji protokołu TLS, więc zostaną przerwane, jeśli pamięć podręczna akceptuje tylko połączenia TLS 1.2. Poprawki dla tych klientów są sprawdzane, więc skontaktuj się z tymi pakietami dla zaktualizowanej wersji z tej pomocy technicznej.

W języku Java 8 TLS 1.2 jest używany domyślnie i nie powinien wymagać aktualizacji konfiguracji klienta w większości przypadków. Aby być bezpiecznym, przetestuj aplikację.

### <a name="nodejs"></a>Node.js

Node Redis i IORedis domyślnie używają protokołu TLS 1.2.

### <a name="php"></a>PHP

#### <a name="predis"></a>Predy
 
* Wersje wcześniejsze niż PHP 7: Predis obsługuje tylko TLS 1.0. Te wersje nie działają z TLS 1.2; aby korzystać z protokołu TLS 1.2, należy uaktualnić.
 
* PHP 7.0 do PHP 7.2.1: Predis domyślnie używa tylko TLS 1.0 lub 1.1. Aby użyć protokołu TLS 1.2, można użyć następującego obejścia. Określ TLS 1.2 podczas tworzenia wystąpienia klienta:

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

* PHP 7.3 i nowsze wersje: Predis używa najnowszej wersji TLS.

#### <a name="phpredis"></a>PhpRedis ( PhpRedis )

PhpRedis nie obsługuje TLS w żadnej wersji PHP.

### <a name="python"></a>Python

Redis-py domyślnie używa protokołu TLS 1.2.

### <a name="go"></a>JĘZYK GO

Redigo domyślnie używa TLS 1.2.

## <a name="additional-information"></a>Dodatkowe informacje

- [Jak skonfigurować pamięć podręczną platformy Azure dla programu Redis](cache-configure.md)
