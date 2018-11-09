---
title: Diagnozowanie i rozwiązywanie problemów z zestawu SDK usługi Azure Cosmos DB Java Async | Dokumentacja firmy Microsoft
description: Używanie funkcji, takich jak rejestrowania po stronie klienta i innych narzędzi innych firm do identyfikacji, diagnozowania i rozwiązywania problemów w usłudze Azure Cosmos DB.
services: cosmos-db
author: moderakh
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 10/28/2018
ms.author: moderakh
ms.devlang: java
ms.component: cosmosdb-sql
ms.openlocfilehash: 74813634aad95f163b06717521bb2c746ac3df6b
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51238833"
---
# <a name="troubleshooting-issues-when-using-java-async-sdk-with-azure-cosmos-db-sql-api-accounts"></a>Rozwiązywanie problemów, korzystając z zestawu SDK Java Async z kontami interfejsu API SQL usługi Azure Cosmos DB
W tym artykule omówiono typowe problemy, rozwiązania, kroki diagnostyki i narzędzi, korzystając z [Java Async ADK](sql-api-sdk-async-java.md) z kontami interfejsu API SQL usługi Azure Cosmos DB.
Zestaw SDK platformy Java Async zapewnia logiczna reprezentacja po stronie klienta do dostępu do interfejsu API SQL usługi Azure Cosmos DB. W tym artykule opisano narzędzia i podejścia, aby pomóc, jeśli napotkasz problemy.

Uruchom za pomocą tej listy:
    * Przyjrzyj się [Typowe problemy i rozwiązania] sekcję w tym artykule.
    * Nasz zestaw SDK jest [open source w serwisie github](https://github.com/Azure/azure-cosmosdb-java) mamy już [problemy z sekcji](https://github.com/Azure/azure-cosmosdb-java/issues) , firma Microsoft aktywnie monitoruje. Należy sprawdzić, możesz znaleźć wszystkie podobny problem z już zgłoszone obejście tego problemu.
    * Przegląd [porady dotyczące wydajności](performance-tips-async-java.md) i stosuj sugerowane rozwiązania.
    * Postępuj zgodnie z pozostałej części tego artykułu, jeśli nie możesz znaleźć rozwiązania, plik [problem w usłudze GitHub](https://github.com/Azure/azure-cosmosdb-java/issues).

## <a name="common-issues-workarounds"></a>Typowe problemy i rozwiązania

### <a name="network-issues-netty-read-timeout-failure-low-throughput-high-latency"></a>Problemy sieciowe Netty odczytu Błąd limitu czasu, niskiej przepustowości, duże opóźnienie

#### <a name="general-suggestions"></a>Ogólne sugestie
* Upewnij się, że aplikacja jest uruchomiona na tym samym regionie co konto usługi Cosmos DB. 
* Sprawdź użycie procesora CPU na hoście, na którym działa aplikacja. Jeśli użycie procesora CPU jest co najmniej 90%, Rozważ uruchomienie aplikacji na hoście przy użyciu nowszej konfiguracji lub rozłożenie obciążenia na maszynach więcej.

#### <a name="connection-throttling"></a>Ograniczanie przepustowości połączenia
Ograniczanie przepustowości połączenia może się zdarzyć z powodu albo [Limit połączeń na maszynie hosta], lub [Wyczerpanie portów w usłudze Azure SNAT (PAT)]:

##### <a name="connection-limit-on-host"></a>Limit połączeń na maszynie hosta
Niektóre systemy Linux (np. Red Hat) mają górnego limitu całkowitej liczby otwartych plików. Gniazda w systemie Linux są implementowane jako pliki, więc ten numer ogranicza całkowitą liczbę połączeń zbyt.
Uruchom następujące polecenie:

```bash
ulimit -a
```
Liczba otwartych plików ("nofile") musi być wystarczająco duży, (na co najmniej tak podwójna jak rozmiar puli połączeń). Przeczytaj więcej szczegółów w [porady dotyczące wydajności](performance-tips-async-java.md).

##### <a name="snat"></a>Wyczerpanie portów w usłudze Azure SNAT (PAT)

Jeśli Twoja aplikacja jest wdrożona na maszynie Wirtualnej platformy Azure bez publicznego adresu IP domyślnie [porty Azure SNAT](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports) służą do nawiązywania połączenia z dowolnego punktu końcowego poza maszyną Wirtualną. Liczba połączeń z maszyny Wirtualnej do punktu końcowego usługi Cosmos DB jest ograniczona przez [konfiguracji Azure SNAT](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports).

Porty SNAT platformy Azure są używane tylko wtedy, gdy maszyna wirtualna platformy Azure ma prywatny adres IP i próbuje nawiązać połączenie z publicznym adresem IP procesu z maszyny Wirtualnej. Tak istnieją dwa obejścia problemu, aby uniknąć ograniczenia SNAT platformy Azure:
    * Dodawanie punktu końcowego usługi Azure Cosmos DB do podsieci sieci Wirtualnej maszyny Wirtualnej platformy Azure, zgodnie z objaśnieniem w [Włączanie sieć Wirtualną punktu końcowego usługi](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview). Po włączeniu punktu końcowego usługi żądania nie są wysyłane z publicznego adresu IP do usługi cosmos DB zamiast tego sieci Wirtualnej i podsieci tożsamości są wysyłane. Ta zmiana może spowodować spadnie zapory, jeśli tylko publiczne adresy IP są dozwolone. Jeśli używasz zapory, podczas włączania punktu końcowego usługi, Dodaj podsieć do zapory przy użyciu [list ACL sieci Wirtualnej](https://docs.microsoft.com/azure/virtual-network/virtual-networks-acl).
    * Przypisz publiczny adres IP do maszyny Wirtualnej platformy Azure.

#### <a name="http-proxy"></a>Serwer proxy HTTP

Jeśli używasz HttpProxy, upewnij się, Twoje HttpProxy może obsługiwać liczbę połączeń skonfigurowanych w zestawie SDK `ConnectionPolicy`.
W przeciwnym razie napotkasz problemy z połączeniem.

#### <a name="invalid-coding-pattern-blocking-netty-io-thread"></a>Nieprawidłowe kodowanie wzorzec: blokowanie wątku Netty operacji We/Wy

Zestaw SDK używa [Netty](https://netty.io/) biblioteki we/wy do komunikacji z usługi usługi Azure Cosmos DB. Mamy Async interfejsu API i używamy nieblokującej na poziomie interfejsów API we/wy netty. Zestaw SDK we/wy praca jest wykonywana w netty wątków We/Wy. Liczba operacji We/Wy netty wątków jest skonfigurowany do być taka sama jak liczba rdzeni procesora CPU maszyny aplikacji. Netty wątków We/Wy są przeznaczone tylko do użytku z blokowaniem innych netty pracy we/wy. Zestaw SDK zwraca wynik wywołania interfejsu API w jednym z netty wątków We/Wy do kodu aplikacji. Jeśli aplikacji, po otrzymaniu wyników w wątku netty wykonuje operację długotrwałego netty wątku, który może spowodować zestaw SDK nie mieć wystarczającej liczby operacji We/Wy wątków do wykonywania wewnętrznego pracę we/wy. Kodowanie takich aplikacji może spowodować niskiej przepustowości, duże opóźnienie i `io.netty.handler.timeout.ReadTimeoutException` błędów. Obejście polega na Przełącz wątek, gdy wiesz, że operacja potrwa.

   Na przykład poniższy fragment kodu pokazuje, że długotrwałego pracy, który przyjmuje więcej niż kilka milisekund, wykonanie na wątku netty, po pewnym czasie można uzyskać w stanie, w którym nie netty wątków We/Wy jest obecny przetwarzanie zadań we/wy, a w efekcie uzyskasz ReadTimeou tException:
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
                        // time consuming work. For example:
                        // writing to a file, computationally heavy work, or just sleep
                        // basically anything which takes more than a few milliseconds
                        // doing such operation on the IO netty thread
                        // without a proper scheduler, will cause problems.
                        // The subscriber will get ReadTimeoutException failure.
                        TimeUnit.SECONDS.sleep(2 * requestTimeoutInSeconds);
                    } catch (Exception e) {
                    }
                },

                exception -> {
                    //will be io.netty.handler.timeout.ReadTimeoutException
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
   Obejście polega na zmiany wątku, na którym wykonywana jest wykonywanie pracy. Zdefiniuj pojedyncze wystąpienie harmonogramu dla aplikacji:
   ```java
// have a singleton instance of executor and scheduler
ExecutorService ex  = Executors.newFixedThreadPool(30);
Scheduler customScheduler = rx.schedulers.Schedulers.from(ex);
   ```
   Zawsze, gdy zachodzi potrzeba czas pracy podjęcie (na przykład duże wymaga pracy, blokowanie operacji We/Wy), Przełącz wątek do procesu roboczego dostarczone przez użytkownika `customScheduler` przy użyciu `.observeOn(customScheduler)` interfejsu API.
```java
Observable<ResourceResponse<Document>> createObservable = client
        .createDocument(getCollectionLink(), docDefinition, null, false);

createObservable
        .observeOn(customScheduler) // switches the thread.
        .subscribe(
            // ...
        );
```
Za pomocą `observeOn(customScheduler)`, zwolnij netty wątków We/Wy i przełącz się do własnego niestandardowego wątku, dostarczone przez customScheduler. Ta modyfikacja spowoduje rozwiązania problemu, a nie będą otrzymywać `io.netty.handler.timeout.ReadTimeoutException` już błędu.

### <a name="connection-pool-exhausted-issue"></a>Pula połączeń wyczerpana problem

`PoolExhaustedException` jest błąd po stronie klienta. Jeśli zostanie wyświetlony ten błąd często to wskazanie, że obciążenie aplikacji jest wyższa niż może służyć jakie puli połączeń zestawu SDK. Może pomóc zwiększyć rozmiar puli połączeń lub rozłożeniu obciążenia na wiele aplikacji.

### <a name="request-rate-too-large"></a>Zbyt duża liczba żądań
Ten błąd jest niepowodzenie po stronie serwera, informujący, że używane aprowizowanej przepływności i należy ponownie później. Jeśli ten błąd często, należy rozważyć zwiększenie przepływność kolekcji.

### <a name="failure-connecting-to-azure-cosmos-db-emulator"></a>Błąd nawiązywania połączenia z emulatora usługi Azure Cosmos DB

Cosmos DB emulator HTTPS certyfikat ma podpis własny. Dla zestawu SDK do pracy z emulatorem należy zaimportować certyfikat emulatora do TrustStore języka Java. Zgodnie z objaśnieniem [tutaj](local-emulator-export-ssl-certificates.md).


## <a name="enable-client-sice-logging"></a>Włącz rejestrowanie zestawu SDK klienta

Używa zestawu Java SDK async SLF4j jako fasady rejestrowania, który obsługuje logowanie do popularnych struktur rejestrowania, takich jak log4j i logback.

Na przykład jeśli chcesz używać mechanizmu log4j jako struktury rejestrowania, należy dodać następujące biblioteki w ścieżce klas języka Java:

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

Dodaj również konfiguracji mechanizmu log4j:
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

Przegląd [ręczne rejestrowanie sfl4j](https://www.slf4j.org/manual.html) Aby uzyskać więcej informacji.

## <a name="netstats"></a>Statystyki sieci systemu operacyjnego
Uruchom polecenie netstat, aby poznać liczbę połączeń znajdują się w `Established` stanu, `CLOSE_WAIT` stanu itp.

W systemie Linux należy uruchom następujące polecenie:
```bash
netstat -nap
```
Filtrować wynik, który ma tylko połączenia do punktu końcowego usługi Cosmos DB.

Najwyraźniej, liczbę połączeń do punktu końcowego usługi Cosmos DB `Established` stanu nie może być większy niż rozmiar puli skonfigurowane połączenie.

Jeśli ma wiele połączeń do punktu końcowego usługi Cosmos DB `CLOSE_WAIT` stanu dla przykładu, więcej niż 1000 połączeń, czyli wskazanie połączeń są opracowywane i rozłączona szybko, co może potencjalnie spowodować problemy. Przegląd [Typowe problemy i rozwiązania] sekcji, aby uzyskać więcej szczegółów.

 <!--Anchors-->
[Typowe problemy i rozwiązania]: #common-issues-workarounds
[Enable client SDK logging]: #enable-client-sice-logging
[Limit połączeń na maszynie hosta]: #connection-limit-on-host
[Wyczerpanie portów w usłudze Azure SNAT (PAT)]: #snat


