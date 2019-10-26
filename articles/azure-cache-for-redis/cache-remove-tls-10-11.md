---
title: Usuń użycie protokołów TLS 1,0 i 1,1 w usłudze Azure cache for Redis | Microsoft Docs
description: Dowiedz się, jak usunąć protokoły TLS 1,0 i 1,1 z aplikacji podczas komunikowania się z usługą Azure cache for Redis
services: cache
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 10/22/2019
ms.author: yegu
ms.openlocfilehash: d1d44467d310b87d306ea7401e66784d684a1bf3
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901900"
---
# <a name="remove-use-of-tls-10-and-11-with-azure-cache-for-redis"></a>Usuń użycie protokołów TLS 1,0 i 1,1 w usłudze Azure cache for Redis

Istnieje szeroka branżowa wypychanie do korzystania z protokołów TLS 1,2 i wyższych. Protokoły TLS 1,0 i 1,1 są podatne na ataki, takie jak BEAST i POODLE, a także zawierają inne słabe luki w zabezpieczeniach i zagrożenia (CVE). Nie obsługują one również nowoczesnych metod szyfrowania i mechanizmów szyfrowania zalecanych przez standardy zgodności PCI. W tym blogu dotyczącym [zabezpieczeń protokołu TLS](https://www.acunetix.com/blog/articles/tls-vulnerabilities-attacks-final-part/) objaśniono niektóre z tych luk w zabezpieczeniach.

Chociaż żadne z tych elementów nie powoduje natychmiastowych problemów, należy rozważyć przejście od używania protokołów TLS 1,0 i 1,1 jak najszybciej. Pamięć podręczna platformy Azure dla usługi Redis przestanie obsługiwać te wersje protokołu TLS od 31 marca 2020. Aby można było komunikować się z pamięcią podręczną po tej dacie, aplikacja będzie musiała korzystać z co najmniej protokołu TLS 1,2.

Ten artykuł zawiera ogólne wskazówki dotyczące wykrywania i usuwania tych zależności z aplikacji.

## <a name="check-if-your-application-is-already-compliant"></a>Sprawdź, czy aplikacja jest już zgodna

Najprostszym sposobem na ustalenie, czy aplikacja będzie działała z protokołem TLS 1,2, jest ustawienie minimalnej wersji protokołu TLS w przypadku testu lub tymczasowej pamięci podręcznej używanej do protokołu TLS 1,2. Ustawienie minimalnej wersji protokołu TLS można znaleźć w obszarze [Ustawienia zaawansowane](cache-configure.md#advanced-settings) wystąpienia pamięci podręcznej w Azure Portal. Jeśli aplikacja będzie działać zgodnie z oczekiwaniami po tej zmianie, najprawdopodobniej będzie to zgodne. Niektóre biblioteki klienckie Redis używane przez aplikację muszą zostać skonfigurowane w celu włączenia protokołu TLS 1,2 w celu nawiązania połączenia z usługą Azure cache dla Redis za pośrednictwem tego protokołu zabezpieczeń.

## <a name="configure-your-application-to-use-tls-12"></a>Konfigurowanie aplikacji do korzystania z protokołu TLS 1,2

Większość aplikacji korzysta z bibliotek klienckich Redis do obsługi komunikacji z pamięciami podręcznymi. Poniżej znajdują się instrukcje dotyczące konfigurowania niektórych popularnych bibliotek klienckich w różnych językach programowania i strukturach w celu użycia protokołu TLS 1,2.

### <a name="net-framework"></a>.NET Framework

Klienci platformy Redis .NET domyślnie używają najniższej wersji protokołu TLS w .NET Framework 4.5.2 lub nowszym oraz najwyższej wersji protokołu TLS w 4,6 lub nowszej. Jeśli używasz starszej wersji .NET Framework, można ręcznie włączyć protokół TLS 1,2:

* StackExchange. Redis: Ustaw `ssl=true` i `sslprotocls=tls12` w parametrach połączenia.
* ServiceStack. Redis: Postępuj zgodnie z [tymi instrukcjami](https://github.com/ServiceStack/ServiceStack.Redis/pull/247).

### <a name="net-core"></a>.NET Core

Klienci programu Redis .NET Core domyślnie korzystają z największej wersji protokołu TLS.

### <a name="java"></a>Java

Klienci Java Redis używają protokołu TLS 1,0 w języku Java w wersji 6 lub starszej. Jedis, sałata i Radisson nie będą w stanie połączyć się z usługą Azure cache for Redis, jeśli protokół TLS 1,0 jest wyłączony w pamięci podręcznej. Obecnie nie ma żadnego znanego obejścia.

W przypadku środowiska Java 7 lub nowszego klienci Redis domyślnie nie używają protokołu TLS 1,2, ale mogą być dla niego skonfigurowane. Sałata i Radisson nie są obecnie obsługiwane. Zostaną one przerwane, jeśli pamięć podręczna akceptuje tylko połączenia TLS 1,2. Jedis pozwala określić podstawowe ustawienia protokołu TLS przy użyciu następującego fragmentu kodu:

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

Predis w języku PHP 7 nie będzie działał, ponieważ drugie obsługuje tylko protokół TLS 1,0. W przypadku środowiska PHP 7.2.1 lub niższa Predis domyślnie używa protokołu TLS 1,0 lub 1,1. Podczas tworzenia wystąpienia klienta można określić protokół TLS 1,2:

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
