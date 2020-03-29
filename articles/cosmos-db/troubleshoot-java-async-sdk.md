---
title: Diagnozowanie i rozwiązywanie problemów z zestawem async usługi Azure Cosmos DB Java Async
description: Użyj funkcji, takich jak rejestrowanie po stronie klienta i innych narzędzi innych firm do identyfikowania, diagnozowania i rozwiązywania problemów z usługą Azure Cosmos DB.
author: moderakh
ms.service: cosmos-db
ms.date: 04/30/2019
ms.author: moderakh
ms.devlang: java
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 572139743c66546622450cef8f8a0fa264d24779
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "65519980"
---
# <a name="troubleshoot-issues-when-you-use-the-java-async-sdk-with-azure-cosmos-db-sql-api-accounts"></a>Rozwiązywanie problemów podczas korzystania z zestawu Java Async SDK z kontami interfejsu API SQL usługi Azure Cosmos DB
W tym artykule omówiono typowe problemy, obejścia, kroki diagnostyczne i narzędzia podczas korzystania z [zestawu Java Async SDK](sql-api-sdk-async-java.md) z kontami interfejsu API usługi Azure Cosmos DB SQL.
Zestaw Java Async SDK zapewnia reprezentację logiczną po stronie klienta służącą do uzyskania dostępu do interfejsu API SQL usługi Azure Cosmos DB. W tym artykule opisano narzędzia i podejścia pomocne w przypadku napotkania jakichkolwiek problemów.

Zacznij od tej listy:

* Zapoznaj się z [sekcją Typowe problemy i obejścia] w tym artykule.
* Spójrz na SDK, który jest dostępny [open source na GitHub](https://github.com/Azure/azure-cosmosdb-java). Ma [sekcję problemów,](https://github.com/Azure/azure-cosmosdb-java/issues) która jest aktywnie monitorowana. Sprawdź, czy podobny problem z obejściem jest już zgłoszony.
* Zapoznaj się ze [wskazówkami dotyczącymi skuteczności](performance-tips-async-java.md)i postępuj zgodnie z sugerowanymi praktykami.
* Przeczytaj resztę tego artykułu, jeśli nie znalazłeś rozwiązania. Następnie zgłaskaj problem z [githubem](https://github.com/Azure/azure-cosmosdb-java/issues).

## <a name="common-issues-and-workarounds"></a><a name="common-issues-workarounds"></a>Typowe problemy i ich rozwiązania

### <a name="network-issues-netty-read-timeout-failure-low-throughput-high-latency"></a>Problemy z siecią, awaria limitu czasu odczytu netty, niska przepływność, duże opóźnienia

#### <a name="general-suggestions"></a>Ogólne sugestie
* Upewnij się, że aplikacja jest uruchomiona w tym samym regionie co konto usługi Azure Cosmos DB. 
* Sprawdź użycie procesora CPU na hoście, na którym jest uruchomiona aplikacja. Jeśli użycie procesora CPU wynosi 90 procent lub więcej, uruchom aplikację na hoście z wyższą konfiguracją. Możesz też rozłożyć obciążenie na więcej maszyn.

#### <a name="connection-throttling"></a>Ograniczanie połączeń
Ograniczanie połączeń może się zdarzyć z powodu [limitu połączenia na komputerze-hoście] lub [wyczerpania portu Azure SNAT (PAT).]

##### <a name="connection-limit-on-a-host-machine"></a><a name="connection-limit-on-host"></a>Limit połączeń na komputerze-hoście
Niektóre systemy Linux, takie jak Red Hat, mają górny limit całkowitej liczby otwartych plików. Gniazda w systemie Linux są implementowane jako pliki, więc liczba ta ogranicza całkowitą liczbę połączeń.
Uruchom następujące polecenie.

```bash
ulimit -a
```
Liczba maksymalnych dozwolonych otwartych plików, które są identyfikowane jako "nofile", musi być co najmniej dwukrotnie większa niż rozmiar puli połączeń. Aby uzyskać więcej informacji, zobacz [Porady dotyczące wydajności](performance-tips-async-java.md).

##### <a name="azure-snat-pat-port-exhaustion"></a><a name="snat"></a>Wyczerpanie portu Azure SNAT (PAT)

Jeśli aplikacja jest wdrażana na maszynach wirtualnych platformy Azure bez publicznego adresu IP, domyślnie [porty SNAT platformy Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports) ustanawiają połączenia z dowolnym punktem końcowym poza maszyną wirtualną. Liczba połączeń dozwolonych z maszyny Wirtualnej do punktu końcowego usługi Azure Cosmos DB jest ograniczona przez [konfigurację usługi Azure SNAT](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports).

 Porty SNAT platformy Azure są używane tylko wtedy, gdy maszyna wirtualna ma prywatny adres IP, a proces z maszyny Wirtualnej próbuje połączyć się z publicznym adresem IP. Istnieją dwa obejścia, aby uniknąć ograniczenia usługi Azure SNAT:

* Dodaj punkt końcowy usługi Usługi Azure Cosmos DB do podsieci sieci wirtualnej maszyn wirtualnych platformy Azure. Aby uzyskać więcej informacji, zobacz [punkty końcowe usługi azure virtual network](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview). 

    Gdy punkt końcowy usługi jest włączony, żądania nie są już wysyłane z publicznego adresu IP do usługi Azure Cosmos DB. Zamiast tego są wysyłane tożsamości sieci wirtualnej i podsieci. Ta zmiana może spowodować spadek zapory, jeśli dozwolone są tylko publiczne serwery IP. Jeśli używasz zapory, po włączeniu punktu końcowego usługi, dodaj podsieć do zapory przy użyciu [list ACL sieci wirtualnej](https://docs.microsoft.com/azure/virtual-network/virtual-networks-acl).
* Przypisywanie publicznego adresu IP do maszyny Wirtualnej platformy Azure.

##### <a name="cant-reach-the-service---firewall"></a><a name="cant-connect"></a>Nie można dotrzeć do usługi — zapora
``ConnectTimeoutException``oznacza, że sdk nie może dotrzeć do usługi.
Podczas korzystania z trybu bezpośredniego może wystąpić błąd podobny do następującego:
```
GoneException{error=null, resourceAddress='https://cdb-ms-prod-westus-fd4.documents.azure.com:14940/apps/e41242a5-2d71-5acb-2e00-5e5f744b12de/services/d8aa21a5-340b-21d4-b1a2-4a5333e7ed8a/partitions/ed028254-b613-4c2a-bf3c-14bd5eb64500/replicas/131298754052060051p//', statusCode=410, message=Message: The requested resource is no longer available at the server., getCauseInfo=[class: class io.netty.channel.ConnectTimeoutException, message: connection timed out: cdb-ms-prod-westus-fd4.documents.azure.com/101.13.12.5:14940]
```

Jeśli masz zaporę uruchomiona na komputerze z aplikacją, zakres otwartych portów od 10 000 do 20 000, które są używane w trybie bezpośrednim.
Należy również przestrzegać [limitu połączenia na komputerze-hoście](#connection-limit-on-host).

#### <a name="http-proxy"></a>Serwer proxy HTTP

Jeśli używasz serwera proxy HTTP, upewnij się, że może on obsługiwać liczbę połączeń skonfigurowanych w pliku SDK `ConnectionPolicy`.
W przeciwnym razie występują problemy z połączeniem.

#### <a name="invalid-coding-pattern-blocking-netty-io-thread"></a>Nieprawidłowy wzorzec kodowania: blokowanie wątku we/wy Netty

Zestaw SDK używa biblioteki we/wy [Netty](https://netty.io/) do komunikowania się z usługą Azure Cosmos DB. SDK ma interfejsy API Async i używa nieblokujące interfejsy API IO netty. Praca we/wy SDK jest wykonywana na wątkach IO Netty. Liczba wątków IO Netty jest skonfigurowana tak, aby była taka sama jak liczba rdzeni procesora komputera aplikacji. 

Wątki We/Wy Netty są przeznaczone do użycia tylko do pracy netty we/wy bez blokowania. Zestaw SDK zwraca wynik wywołania interfejsu API w jednym z wątków we/wysłudze Netty do kodu aplikacji. Jeśli aplikacja wykonuje długotrwałą operację po otrzymaniu wyników w wątku Netty, SDK może nie mieć wystarczającej liczby wątków we/wy, aby wykonać swoją wewnętrzną pracę we/wy. Takie kodowanie aplikacji może spowodować niską przepływność, duże opóźnienia i `io.netty.handler.timeout.ReadTimeoutException` błędy. Obejście jest, aby przełączyć wątek, gdy wiesz, że operacja wymaga czasu.

Na przykład spójrz na poniższy fragment kodu. Może wykonać długotrwałą pracę, która trwa więcej niż kilka milisekund w wątku Netty. Jeśli tak, w końcu można dostać się do stanu, w którym nie netty wątku we/wy jest obecny do przetwarzania pracy we/wy. W rezultacie otrzymasz błąd ReadTimeoutException.
```java
@Test
public void badCodeWithReadTimeoutException() throws Exception {
    int requestTimeoutInSeconds = 10;

    ConnectionPolicy policy = new ConnectionPolicy();
    policy.setRequestTimeoutInMillis(requestTimeoutInSeconds * 1000);

    AsyncDocumentClient testClient = new AsyncDocumentClient.Builder()
            .withServiceEndpoint(TestConfigurations.HOST)
            .withMasterKeyOrResourceToken(TestConfigurations.MASTER_KEY)
            .withConnectionPolicy(policy)
            .build();

    int numberOfCpuCores = Runtime.getRuntime().availableProcessors();
    int numberOfConcurrentWork = numberOfCpuCores + 1;
    CountDownLatch latch = new CountDownLatch(numberOfConcurrentWork);
    AtomicInteger failureCount = new AtomicInteger();

    for (int i = 0; i < numberOfConcurrentWork; i++) {
        Document docDefinition = getDocumentDefinition();
        Observable<ResourceResponse<Document>> createObservable = testClient
                .createDocument(getCollectionLink(), docDefinition, null, false);
        createObservable.subscribe(r -> {
                    try {
                        // Time-consuming work is, for example,
                        // writing to a file, computationally heavy work, or just sleep.
                        // Basically, it's anything that takes more than a few milliseconds.
                        // Doing such operations on the IO Netty thread
                        // without a proper scheduler will cause problems.
                        // The subscriber will get a ReadTimeoutException failure.
                        TimeUnit.SECONDS.sleep(2 * requestTimeoutInSeconds);
                    } catch (Exception e) {
                    }
                },

                exception -> {
                    //It will be io.netty.handler.timeout.ReadTimeoutException.
                    exception.printStackTrace();
                    failureCount.incrementAndGet();
                    latch.countDown();
                },
                () -> {
                    latch.countDown();
                });
    }

    latch.await();
    assertThat(failureCount.get()).isGreaterThan(0);
}
```
   Obejście jest zmiana wątku, w którym wykonujesz pracę, która wymaga czasu. Zdefiniuj pojedyncze wystąpienie harmonogramu dla aplikacji.
   ```java
// Have a singleton instance of an executor and a scheduler.
ExecutorService ex  = Executors.newFixedThreadPool(30);
Scheduler customScheduler = rx.schedulers.Schedulers.from(ex);
   ```
   Może być konieczne wykonanie pracy, która wymaga czasu, na przykład, obliczeniowo ciężkiej pracy lub blokowania we/wy. W takim przypadku przełącz wątek `customScheduler` do procesu `.observeOn(customScheduler)` roboczego dostarczonego przez użytkownika przy użyciu interfejsu API.
```java
Observable<ResourceResponse<Document>> createObservable = client
        .createDocument(getCollectionLink(), docDefinition, null, false);

createObservable
        .observeOn(customScheduler) // Switches the thread.
        .subscribe(
            // ...
        );
```
Za `observeOn(customScheduler)`pomocą , zwolnić netty wątku we/wy i przełączyć się do własnego wątku niestandardowego dostarczonych przez harmonogram niestandardowy. Ta modyfikacja rozwiązuje problem. Nie dostaniesz już `io.netty.handler.timeout.ReadTimeoutException` awarii.

### <a name="connection-pool-exhausted-issue"></a>Wyczerpany problem z pulą połączeń

`PoolExhaustedException`jest awarią po stronie klienta. Ten błąd wskazuje, że obciążenie aplikacji jest wyższa niż to, co może obsługiwać pula połączeń SDK. Zwiększ rozmiar puli połączeń lub rozłóż obciążenie wielu aplikacji.

### <a name="request-rate-too-large"></a>Stawka żądania jest zbyt duża
Ten błąd jest awarią po stronie serwera. Oznacza to, że posunęła się aprowizowana przepływność. Ponów próbę później. Jeśli często pojawia się ten błąd, należy wziąć pod uwagę wzrost przepływności kolekcji.

### <a name="failure-connecting-to-azure-cosmos-db-emulator"></a>Niepowodzenie nawiązywania połączenia z emulatorem usługi Azure Cosmos DB

Certyfikat HTTPS emulatora usługi Azure Cosmos DB jest podpisany samodzielnie. Aby sdk działał z emulatorem, zaimportuj certyfikat emulatora do java truststore. Aby uzyskać więcej informacji, zobacz [Eksportowanie certyfikatów emulatora usługi Azure Cosmos DB](local-emulator-export-ssl-certificates.md).

### <a name="dependency-conflict-issues"></a>Problemy z konfliktem zależności

```console
Exception in thread "main" java.lang.NoSuchMethodError: rx.Observable.toSingle()Lrx/Single;
```

Powyższy wyjątek sugeruje, że masz zależność od starszej wersji RxJava lib (np. 1.2.2). Nasz SDK opiera się na RxJava 1.3.8, który ma interfejsy API nie są dostępne we wcześniejszej wersji RxJava. 

Obejście dla takich issuses jest określenie, które inne zależności przynosi w RxJava-1.2.2 i wykluczyć przechodnie zależności na RxJava-1.2.2 i zezwolić cosmosDB SDK przynieść nowszą wersję.

Aby określić, która biblioteka przynosi w programie RxJava-1.2.2, uruchom następujące polecenie obok pliku pom.xml projektu:
```bash
mvn dependency:tree
```
Aby uzyskać więcej informacji, zobacz [przewodnik po drzewie zależności maven](https://maven.apache.org/plugins/maven-dependency-plugin/examples/resolving-conflicts-using-the-dependency-tree.html).

Po zidentyfikowaniu RxJava-1.2.2 jest przechodnie zależności, które inne zależności projektu, można zmodyfikować zależność od tego lib w pliku pom i wykluczyć RxJava przechodnie zależności to:

```xml
<dependency>
  <groupId>${groupid-of-lib-which-brings-in-rxjava1.2.2}</groupId>
  <artifactId>${artifactId-of-lib-which-brings-in-rxjava1.2.2}</artifactId>
  <version>${version-of-lib-which-brings-in-rxjava1.2.2}</version>
  <exclusions>
    <exclusion>
      <groupId>io.reactivex</groupId>
      <artifactId>rxjava</artifactId>
    </exclusion>
  </exclusions>
</dependency>
```

Aby uzyskać więcej informacji, zobacz [przewodnik wykluczają zależności przechodnie](https://maven.apache.org/guides/introduction/introduction-to-optional-and-excludes-dependencies.html).


## <a name="enable-client-sdk-logging"></a><a name="enable-client-sice-logging"></a>Włączanie rejestrowania sdk klienta

Java Async SDK używa SLF4j jako fasady rejestrowania, która obsługuje rejestrowanie do popularnych platform rejestrowania, takich jak log4j i logback.

Na przykład, jeśli chcesz użyć log4j jako struktury rejestrowania, dodaj następujące libs w ścieżce klasy Java.

```xml
<dependency>
  <groupId>org.slf4j</groupId>
  <artifactId>slf4j-log4j12</artifactId>
  <version>${slf4j.version}</version>
</dependency>
<dependency>
  <groupId>log4j</groupId>
  <artifactId>log4j</artifactId>
  <version>${log4j.version}</version>
</dependency>
```

Dodam też log4j config.
```
# this is a sample log4j configuration

# Set root logger level to DEBUG and its only appender to A1.
log4j.rootLogger=INFO, A1

log4j.category.com.microsoft.azure.cosmosdb=DEBUG
#log4j.category.io.netty=INFO
#log4j.category.io.reactivex=INFO
# A1 is set to be a ConsoleAppender.
log4j.appender.A1=org.apache.log4j.ConsoleAppender

# A1 uses PatternLayout.
log4j.appender.A1.layout=org.apache.log4j.PatternLayout
log4j.appender.A1.layout.ConversionPattern=%d %5X{pid} [%t] %-5p %c - %m%n
```

Aby uzyskać więcej informacji, zobacz [instrukcję rejestrowania sfl4j](https://www.slf4j.org/manual.html).

## <a name="os-network-statistics"></a><a name="netstats"></a>Statystyki sieci systemu operacyjnego
Uruchom polecenie netstat, aby zorientować się, ile połączeń `ESTABLISHED` `CLOSE_WAIT`jest w stanach takich jak i .

W systemie Linux można uruchomić następujące polecenie.
```bash
netstat -nap
```
Filtruj wynik tylko do połączeń z punktem końcowym usługi Azure Cosmos DB.

Liczba połączeń z punktem końcowym usługi Azure Cosmos DB w `ESTABLISHED` stanie nie może być większa niż rozmiar puli skonfigurowanych połączeń.

Wiele połączeń z punktem końcowym usługi Azure Cosmos DB może być w `CLOSE_WAIT` stanie. Może być ich ponad 1000. Liczba, która jest wysoka, wskazuje, że połączenia są nawiązywały i szybko niszczone. Ta sytuacja może spowodować problemy. Aby uzyskać więcej informacji, zobacz [sekcję Typowe problemy i obejścia] problemu.

 <!--Anchors-->
[Typowe problemy i ich rozwiązania]: #common-issues-workarounds
[Enable client SDK logging]: #enable-client-sice-logging
[Limit połączeń na komputerze-hoście]: #connection-limit-on-host
[Wyczerpanie portu Azure SNAT (PAT)]: #snat


