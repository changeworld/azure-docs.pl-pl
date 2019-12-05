---
title: Usuń protokoły TLS 1,0 i 1,1 z używania z usługą Azure cache for Redis
description: Dowiedz się, jak usunąć protokoły TLS 1,0 i 1,1 z aplikacji podczas komunikowania się z usługą Azure cache for Redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: yegu
ms.openlocfilehash: 74fcce412b2673a3ec9e4809cef018f1afbc3530
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2019
ms.locfileid: "74812843"
---
# <a name="remove-tls-10-and-11-from-use-with-azure-cache-for-redis"></a>Usuń protokoły TLS 1,0 i 1,1 z używania z usługą Azure cache for Redis

Na wyłączne korzystanie z Transport Layer Security (TLS) w wersji 1,2 lub nowszej jest na całym branżą. Protokoły TLS w wersji 1,0 i 1,1 są podatne na ataki, takie jak BEAST i POODLE, a także zawierają inne słabe luki w zabezpieczeniach i zagrożenia (CVE). Nie obsługują one również nowoczesnych metod szyfrowania i mechanizmów szyfrowania zalecanych przez standardy zgodności z kartą płatniczą (PCI). Ten [Blog dotyczący zabezpieczeń protokołu TLS](https://www.acunetix.com/blog/articles/tls-vulnerabilities-attacks-final-part/) wyjaśnia niektóre z tych luk w bardziej szczegółowy sposób.

Chociaż żadne z tych kwestii nie powoduje natychmiastowego problemu, zalecamy natychmiastowe zaprzestanie korzystania z protokołu TLS 1,0 i 1,1. Usługa Azure cache for Redis zatrzyma obsługę tych wersji protokołu TLS 31 marca 2020. Po tej dacie aplikacja będzie musiała korzystać z protokołu TLS 1,2 lub nowszego do komunikowania się z pamięcią podręczną.

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

Klienci Java Redis używają protokołu TLS 1,0 w wersji 6 lub starszej. Jedis, sałata i Radisson nie mogą połączyć się z pamięcią podręczną platformy Azure dla Redis, jeśli protokół TLS 1,0 jest wyłączony w pamięci podręcznej. Obecnie nie ma żadnego znanego obejścia.

W programie Java 7 lub nowszym klienci Redis domyślnie nie używają protokołu TLS 1,2, ale można go skonfigurować dla niego. Sałata i Radisson nie obsługują tej konfiguracji teraz. Zostaną one przerwane, jeśli pamięć podręczna akceptuje tylko połączenia TLS 1,2. Jedis pozwala określić podstawowe ustawienia protokołu TLS przy użyciu następującego fragmentu kodu:

``` Java
SSLSocketFactory sslSocketFactory = (SSLSocketFactory) SSLSocketFactory.getDefault();
SSLParameters sslParameters = new SSLParameters();
sslParameters.setEndpointIdentificationAlgorithm("HTTPS");
sslParameters.setProtocols(new String[]{"TLSv1", "TLSv1.1", "TLSv1.2"});
 
URI uri = URI.create("rediss://host:port");
JedisShardInfo shardInfo = new JedisShardInfo(uri, sslSocketFactory, sslParameters, null);
 
shardInfo.setPassword("cachePassword");
 
Jedis jedis = new Jedis(shardInfo);
```

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
