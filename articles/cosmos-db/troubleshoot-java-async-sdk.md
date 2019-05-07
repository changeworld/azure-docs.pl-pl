---
title: Diagnozowanie i rozwiązywanie problemów z zestawu SDK usługi Azure Cosmos DB Java Async
description: Używanie funkcji, takich jak rejestrowania po stronie klienta i inne narzędzia innych firm do identyfikacji, diagnozowanie i rozwiązywanie problemów w usłudze Azure Cosmos DB.
author: moderakh
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 04/30/2019
ms.author: moderakh
ms.devlang: java
ms.subservice: cosmosdb-sql
ms.reviewer: sngun
ms.openlocfilehash: f0dc45f104e05fde083489604865aaae8282d6a2
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65146205"
---
# <a name="troubleshoot-issues-when-you-use-the-java-async-sdk-with-azure-cosmos-db-sql-api-accounts"></a>Rozwiązywanie problemów, gdy używasz zestawu SDK Java Async z kontami interfejsu API SQL usługi Azure Cosmos DB
W tym artykule omówiono typowe problemy, rozwiązania, czynności diagnostycznych i narzędzia, gdy używasz [zestawu SDK Java Async](sql-api-sdk-async-java.md) z kontami interfejsu API SQL usługi Azure Cosmos DB.
Zestaw Java Async SDK udostępnia logiczna reprezentacja po stronie klienta do dostępu do interfejsu API SQL usługi Azure Cosmos DB. W tym artykule opisano narzędzia i podejścia, aby pomóc, jeśli napotkasz problemy.

Uruchom za pomocą tej listy:

* Przyjrzyj się [Typowe problemy i rozwiązania] sekcję w tym artykule.
* Przyjrzyj się zestaw SDK, który jest dostępny [typu open source w serwisie GitHub](https://github.com/Azure/azure-cosmosdb-java). Ma ona [problemy z sekcji](https://github.com/Azure/azure-cosmosdb-java/issues) , jest aktywnie monitorowane. Sprawdź, jeśli już złożono wszelkie podobne problemy związane z obejście tego problemu.
* Przegląd [porady dotyczące wydajności](performance-tips-async-java.md)i postępuj zgodnie z sugerowane rozwiązania.
* Przeczytaj w pozostałej części tego artykułu, jeśli nie możesz znaleźć rozwiązania. Następnie plik [problem w usłudze GitHub](https://github.com/Azure/azure-cosmosdb-java/issues).

## <a name="common-issues-workarounds"></a>Typowe problemy i rozwiązania

### <a name="network-issues-netty-read-timeout-failure-low-throughput-high-latency"></a>Problemy sieciowe Netty odczytu Błąd limitu czasu, niskiej przepustowości, duże opóźnienie

#### <a name="general-suggestions"></a>Ogólne sugestie
* Upewnij się, że aplikacja jest uruchomiona na tym samym regionie co konto usługi Azure Cosmos DB. 
* Sprawdź użycie procesora CPU na hoście, na którym działa aplikacja. Gdy użycie procesora CPU przez 90 procent lub więcej, uruchom aplikację na hoście przy użyciu konfiguracji wyższego. Lub można rozdzielić obciążenia więcej maszyn.

#### <a name="connection-throttling"></a>Ograniczanie przepustowości połączenia
Ograniczanie przepustowości połączenia może się zdarzyć z powodu albo [Limit połączeń na komputerze hosta] lub [Wyczerpanie portów w usłudze Azure SNAT (PAT)].

##### <a name="connection-limit-on-host"></a>Limit połączeń na komputerze hosta
Niektóre systemy Linux, takie jak Red Hat mają górnego limitu całkowitej liczby otwartych plików. Gniazda w systemie Linux są implementowane jako pliki, więc ten numer ogranicza całkowitą liczbę połączeń, zbyt.
Uruchom następujące polecenie.

```bash
ulimit -a
```
Liczba maksymalny dozwolony otwartych plików, które są identyfikowane jako "nofile", musi być co najmniej dwukrotnie rozmiar puli połączeń. Aby uzyskać więcej informacji, zobacz [porady dotyczące wydajności](performance-tips-async-java.md).

##### <a name="snat"></a>Wyczerpanie portów w usłudze Azure SNAT (PAT)

Jeśli Twoja aplikacja jest wdrożona w usłudze Azure Virtual Machines bez publicznego adresu IP domyślnie [porty Azure SNAT](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports) nawiązywać połączenia z dowolnego punktu końcowego poza maszyną Wirtualną. Liczba połączeń z maszyny Wirtualnej do punktu końcowego usługi Azure Cosmos DB jest ograniczona przez [konfiguracji Azure SNAT](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports).

 Azure SNAT porty są używane tylko wtedy, gdy maszyna wirtualna ma prywatny adres IP i próbuje nawiązać połączenie z publicznym adresem IP procesu z maszyny Wirtualnej. Istnieją dwa obejścia problemu, aby uniknąć ograniczenia SNAT platformy Azure:

* Dodawanie punktu końcowego usługi Azure Cosmos DB do podsieci sieci wirtualnej usługi Azure Virtual Machines. Aby uzyskać więcej informacji, zobacz [punktów końcowych usługi Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview). 

    Po włączeniu punktu końcowego usługi żądania są już wysyłane z publicznego adresu IP do usługi Azure Cosmos DB. Zamiast tego wysyłane sieć wirtualną i podsieć tożsamości. Ta zmiana może spowodować spadnie zapory, jeśli tylko publiczne adresy IP są dozwolone. Jeśli używana jest zapora, po włączeniu punktu końcowego usługi, dodać podsieć do zapory przy użyciu [wirtualnego ACL sieci](https://docs.microsoft.com/azure/virtual-network/virtual-networks-acl).
* Przypisz publiczny adres IP do maszyny Wirtualnej platformy Azure.

##### <a name="cant-connect"></a>Nie można uzyskać dostęp do usługi — zapory
``ConnectTimeoutException`` Wskazuje, że zestaw SDK nie może uzyskać dostęp do usługi.
Podczas korzystania z trybu bezpośredniego awaria może nastąpić podobny do następującego:
```
GoneException{error=null, resourceAddress='https://cdb-ms-prod-westus-fd4.documents.azure.com:14940/apps/e41242a5-2d71-5acb-2e00-5e5f744b12de/services/d8aa21a5-340b-21d4-b1a2-4a5333e7ed8a/partitions/ed028254-b613-4c2a-bf3c-14bd5eb64500/replicas/131298754052060051p//', statusCode=410, message=Message: The requested resource is no longer available at the server., getCauseInfo=[class: class io.netty.channel.ConnectTimeoutException, message: connection timed out: cdb-ms-prod-westus-fd4.documents.azure.com/101.13.12.5:14940]
```

Jeśli zapora na komputerze aplikacji, otwórz zakres portów 10 000 do 20 000, które są używane w trybie bezpośredniego.
To samo wykonać również [limitu połączeń na komputerze hosta](#connection-limit-on-host).

#### <a name="http-proxy"></a>Serwer proxy HTTP

Jeśli używasz serwera proxy HTTP, upewnij się, może obsługiwać liczbę połączeń, skonfigurowanych w zestawie SDK `ConnectionPolicy`.
W przeciwnym razie napotkasz problemy z połączeniem.

#### <a name="invalid-coding-pattern-blocking-netty-io-thread"></a>Nieprawidłowy wzorzec kodowania: Blokowanie wątku Netty operacji We/Wy

Zestaw SDK używa [Netty](https://netty.io/) biblioteki we/wy nawiązać połączenia z usługą Azure Cosmos DB. Zestaw SDK ma asynchronicznych interfejsów API i korzysta z nieblokującej na poziomie interfejsów API Netty we/wy. Zestaw SDK we/wy praca jest wykonywana w wątkach Netty we/wy. Liczba operacji We/Wy Netty wątków jest skonfigurowany do być taka sama jak liczba rdzeni procesora CPU maszyny aplikacji. 

Wątki Netty operacji We/Wy są przeznaczone do użycia tylko w przypadku pracy bez blokowania Netty operacji We/Wy. Zestaw SDK zwraca wynik wywołania interfejsu API w jednym z wątków We/Wy Netty do kodu aplikacji. Aplikacja wykonuje to operacja długotrwała po otrzymaniu wyników w wątku Netty, zestaw SDK może nie mieć wystarczającej liczby wątków We/Wy do wykonania pracy wewnętrznej operacji We/Wy. Kodowanie takich aplikacji może prowadzić do niskiej przepustowości, duże opóźnienie i `io.netty.handler.timeout.ReadTimeoutException` błędów. Obejście polega na Przełącz wątek, gdy wiesz, że operacja jest czasochłonne.

Na przykład Przyjrzyj się poniższej wstawki kodu. Może wykonywać pracę długotrwałych, która zajmuje więcej niż kilku milisekund na Netty wątku. Jeśli tak, po pewnym czasie można uzyskać w stanie, w którym nie Netty operacji We/Wy wątek znajduje się przetwarzanie zadań we/wy. W rezultacie otrzymasz błąd ReadTimeoutException.
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
   Obejście polega na zmiany wątku, na którym wykonujesz pracy, która zajmuje trochę czasu. Zdefiniuj pojedyncze wystąpienie harmonogramu dla aplikacji.
   ```java
// Have a singleton instance of an executor and a scheduler.
ExecutorService ex  = Executors.newFixedThreadPool(30);
Scheduler customScheduler = rx.schedulers.Schedulers.from(ex);
   ```
   Może być konieczne działają w ten sposób, że zajmuje czas, na przykład pracy wymaga dużych lub blokowania We/Wy. W tym przypadku Przełącz wątek do procesu roboczego dostarczone przez użytkownika `customScheduler` przy użyciu `.observeOn(customScheduler)` interfejsu API.
```java
Observable<ResourceResponse<Document>> createObservable = client
        .createDocument(getCollectionLink(), docDefinition, null, false);

createObservable
        .observeOn(customScheduler) // Switches the thread.
        .subscribe(
            // ...
        );
```
Za pomocą `observeOn(customScheduler)`, zwolnij wątku Netty operacji We/Wy i przełącz się do własnego niestandardowego wątku, dostarczone przez niestandardowy harmonogram. Ta modyfikacja rozwiązuje problem. Nie otrzymasz `io.netty.handler.timeout.ReadTimeoutException` już błędu.

### <a name="connection-pool-exhausted-issue"></a>Pula połączeń wyczerpana problem

`PoolExhaustedException` jest błąd po stronie klienta. Ten błąd wskazuje, że obciążenie aplikacji jest wyższy niż co może służyć w puli połączeń zestawu SDK. Zwiększ rozmiar puli połączeń lub rozłożyć obciążenie na wiele aplikacji.

### <a name="request-rate-too-large"></a>Zbyt duża liczba żądań
Ten błąd jest niepowodzenie po stronie serwera. Oznacza to, że używane aprowizowanej przepływności. Spróbuj ponownie później. Jeśli ten błąd często, należy wziąć pod uwagę wzrost przepływność kolekcji.

### <a name="failure-connecting-to-azure-cosmos-db-emulator"></a>Błąd nawiązywania połączenia z emulatora usługi Azure Cosmos DB

Certyfikat HTTPS emulatora usługi Azure Cosmos DB, ma podpis własny. Dla zestawu SDK do pracy z emulatorem należy zaimportować certyfikat emulatora do TrustStore języka Java. Aby uzyskać więcej informacji, zobacz [eksportu usługi Azure Cosmos DB emulator certyfikaty](local-emulator-export-ssl-certificates.md).

### <a name="dependency-conflict-issues"></a>Problemy dotyczące konfliktu zależności

```console
Exception in thread "main" java.lang.NoSuchMethodError: rx.Observable.toSingle()Lrx/Single;
```

Powyżej wyjątek sugeruje, że mają zależności na starszą wersję RxJava lib (np. 1.2.2). Nasz zestaw SDK zależy od RxJava 1.3.8 ma nie jest dostępna w starszej wersji RxJava interfejsów API. 

Obejście dla takich issuses polega na określeniu które innych zależności wiąże RxJava 1.2.2 wykluczyć przechodnie zależność od RxJava 1.2.2 i umożliwić bazy danych cosmos DB zestawu SDK doprowadzić do nowszej wersji.

Aby zidentyfikować, biblioteki, który udostępnia w 1.2.2 RxJava, uruchom następujące polecenie, obok pliku pom.xml projektu:
```bash
mvn dependency:tree
```
Aby uzyskać więcej informacji, zobacz [przewodnik drzewo zależności maven](https://maven.apache.org/plugins/maven-dependency-plugin/examples/resolving-conflicts-using-the-dependency-tree.html).

Po zidentyfikowaniu RxJava 1.2.2 jest przechodnia które innych zależności projektu, możesz zmodyfikować zależności na zależność, która lib w pliku pom i Wyklucz RxJava przechodnie zależności:

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

Aby uzyskać więcej informacji, zobacz [wykluczyć przewodnik przechodnie zależności](https://maven.apache.org/guides/introduction/introduction-to-optional-and-excludes-dependencies.html).


## <a name="enable-client-sice-logging"></a>Włącz rejestrowanie zestawu SDK klienta

Używa zestawu SDK Java Async SLF4j jako fasady rejestrowania, który obsługuje logowanie do popularnych struktur rejestrowania, takich jak log4j i logback.

Na przykład jeśli chcesz używać mechanizmu log4j jako struktury rejestrowania, należy dodać następujące biblioteki w ścieżce klas języka Java.

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

Dodaj również konfiguracji mechanizmu log4j.
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

Aby uzyskać więcej informacji, zobacz [ręczne rejestrowanie sfl4j](https://www.slf4j.org/manual.html).

## <a name="netstats"></a>Statystyki sieci systemu operacyjnego
Uruchom polecenie netstat, aby poznać liczbę połączeń są takie jak Stany `ESTABLISHED` i `CLOSE_WAIT`.

W systemie Linux możesz uruchomić następujące polecenie.
```bash
netstat -nap
```
Filtrowanie wyników do tylko na połączenia z punktem końcowym usługi Azure Cosmos DB.

Liczba połączeń z punktu końcowego usługi Azure Cosmos DB w `ESTABLISHED` stanu nie może być większy niż rozmiar puli skonfigurowane połączenie.

Wiele połączeń do punktu końcowego usługi Azure Cosmos DB może znajdować się w `CLOSE_WAIT` stanu. Może być większa niż 1000. To duża liczba wskazuje, że połączenia są opracowywane i szybko rozłączona. Ta sytuacja potencjalnie powoduje występowanie problemów. Aby uzyskać więcej informacji, zobacz [Typowe problemy i rozwiązania] sekcji.

 <!--Anchors-->
[Typowe problemy i rozwiązania]: #common-issues-workarounds
[Enable client SDK logging]: #enable-client-sice-logging
[Limit połączeń na komputerze hosta]: #connection-limit-on-host
[Wyczerpanie portów w usłudze Azure SNAT (PAT)]: #snat


