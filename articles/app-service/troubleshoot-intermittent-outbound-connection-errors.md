---
title: Rozwiązywanie problemów z sporadyznymi błędami połączenia wychodzącego w usłudze Azure App Service
description: Rozwiązywanie sporadyczne błędy połączenia i związane z nimi problemy z wydajnością w usłudze Azure App Service
author: v-miegge
manager: barbkess
ms.topic: troubleshooting
ms.date: 03/24/2020
ms.author: ramakoni
ms.custom: security-recommendations
ms.openlocfilehash: 028ddccdb989d35710e387081b08a3b973d75bdc
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80367553"
---
# <a name="troubleshooting-intermittent-outbound-connection-errors-in-azure-app-service"></a>Rozwiązywanie problemów z sporadyznymi błędami połączenia wychodzącego w usłudze Azure App Service

Ten artykuł ułatwia rozwiązywanie sporadyczne błędy połączenia i związane z nimi problemy z wydajnością w [usłudze Azure App Service](https://docs.microsoft.com/azure/app-service/overview). W tym temacie znajdziesz więcej informacji na temat i metod rozwiązywania problemów z wyczerpaniem portów translacji sieciowej adresów źródłowych (SNAT). Jeśli potrzebujesz więcej pomocy w dowolnym momencie tego artykułu, skontaktuj się z ekspertami platformy Azure na [platformie MSDN Azure i forach przepełnienia stosu](https://azure.microsoft.com/support/forums/). Alternatywnie złóż błąd pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję Uzyskaj pomoc **techniczną**.

## <a name="symptoms"></a>Objawy

Aplikacje i funkcje hostowane w usłudze Azure App może wykazywać co najmniej jeden z następujących symptomów:

* Powolne czasy odpowiedzi na wszystkich lub niektórych wystąpień w planie usługi.
* Przerywane błędy 5xx lub **Bad Gateway**
* Komunikaty o błędach limitu czasu
* Nie można połączyć się z zewnętrznymi punktami końcowymi (takimi jak SQLDB, usługa fabric, inne usługi aplikacji itp.)

## <a name="cause"></a>Przyczyna

Główną przyczyną tych objawów jest to, że wystąpienie aplikacji nie jest w stanie otworzyć nowe połączenie z zewnętrznym punktem końcowym, ponieważ osiągnął jeden z następujących limitów:

* Połączenia TCP: Istnieje limit liczby połączeń wychodzących, które mogą być wykonane. Jest to skojarzone z rozmiarem używanego pracownika.
* Porty SNAT: Jak omówiono [w połączeniach wychodzących na platformie Azure,](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections)platforma Azure używa źródłowego tłumaczenia adresów sieciowych (SNAT) i modułu równoważenia obciążenia (nieuprzejmego się z klientami) do komunikowania się z punktami końcowymi poza platformą Azure w publicznej przestrzeni adresowej IP. Każde wystąpienie w usłudze Azure App początkowo otrzymuje wstępnie przydzieloną liczbę **128** portów SNAT. Ten limit wpływa na otwieranie połączeń z tą samą kombinacją hosta i portu. Jeśli aplikacja tworzy połączenia z kombinacją kombinacji adresów i portów, nie będzie używać portów SNAT. Porty SNAT są używane, gdy masz powtarzające się wywołania tej samej kombinacji adresu i portu. Po zwolnieniu portu port jest dostępny do ponownego użycia w razie potrzeby. Moduł równoważenia obciążenia usługi Azure Network odzyskuje port SNAT z zamkniętych połączeń tylko po odczekaniu 4 minut.

Gdy aplikacje lub funkcje szybko otwierają nowe połączenie, mogą szybko wyczerpać wstępnie przydzielony przydział 128 portów. Są one następnie blokowane, dopóki nowy port SNAT nie stanie się dostępny, albo poprzez dynamiczne przydzielanie dodatkowych portów SNAT, albo poprzez ponowne użycie odzyskanego portu SNAT. Aplikacje lub funkcje, które są zablokowane z powodu tej niezdolności do tworzenia nowych połączeń rozpocznie się wystąpienie co najmniej jeden z problemów opisanych w **symptomy** sekcji tego artykułu.

## <a name="avoiding-the-problem"></a>Unikanie problemu

Unikanie problemu z portem SNAT oznacza unikanie tworzenia nowych połączeń powtarzalnie z tym samym hostem i portem.

Ogólne strategie łagodzenia wyczerpania portów SNAT zostały omówione w [sekcji Rozwiązywanie problemów](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#problemsolving) w dokumentacji **połączeń wychodzących platformy Azure.** Z tych strategii, poniżej mają zastosowanie do aplikacji i funkcji hostowanych w usłudze Azure App.

### <a name="modify-the-application-to-use-connection-pooling"></a>Modyfikowanie aplikacji w celu użycia buforowania połączeń

* Aby uzyskać połączenie z protokołem HTTP, zapoznaj się [z pulą połączeń HTTP za pomocą protokołu HttpClientFactory](https://docs.microsoft.com/aspnet/core/performance/performance-best-practices#pool-http-connections-with-httpclientfactory).
* Aby uzyskać informacje na temat buforowania połączeń programu SQL Server, zapoznaj się z [programem SQL Server Connection Pooling (ADO.NET)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling).
* Aby zaimplementować buforowanie z aplikacjami struktury jednostek, przejrzyj [buforowanie DbContext](https://docs.microsoft.com/ef/core/what-is-new/ef-core-2.0#dbcontext-pooling).

Oto zbiór łączy do implementowania buforowania połączeń przez stos różnych rozwiązań.

#### <a name="node"></a>Węzeł

Domyślnie połączenia dla NodeJS nie są utrzymywane przy życiu. Poniżej znajdują się popularne bazy danych i pakiety do buforowania połączeń, które zawierają przykłady sposobu ich implementacji.

* [Mysql](https://github.com/mysqljs/mysql#pooling-connections)
* [Mongodb](https://blog.mlab.com/2017/05/mongodb-connection-pooling-for-express-applications/)
* [PostgreSQL](https://node-postgres.com/features/pooling)
* [SQL Server](https://github.com/tediousjs/node-mssql#connection-pools)

HTTP Keep-alive

* [agentkeepalive](https://www.npmjs.com/package/agentkeepalive)
* [Dokumentacja node.js v13.9.0](https://nodejs.org/api/http.html)

#### <a name="java"></a>Java

Poniżej znajdują się popularne biblioteki używane do buforowania połączeń JDBC, które zawierają przykłady sposobu ich implementacji: buforowanie połączeń JDBC.

* [Tomcat 8 (Tomcat) 8](https://tomcat.apache.org/tomcat-8.0-doc/jdbc-pool.html)
* [C3p0](https://github.com/swaldman/c3p0)
* [HikariCP (hikaricp)](https://github.com/brettwooldridge/HikariCP)
* [Apache DBCP](https://commons.apache.org/proper/commons-dbcp/)

Buforowanie połączeń HTTP

* [Zarządzanie połączeniami Apache](https://hc.apache.org/httpcomponents-client-ga/tutorial/html/connmgmt.html)
* [Pula zajęćHttpClientConnectionManager](http://hc.apache.org/httpcomponents-client-ga/httpclient/apidocs/org/apache/http/impl/conn/PoolingHttpClientConnectionManager.html)

#### <a name="php"></a>PHP

Chociaż PHP nie obsługuje buforowania połączeń, można spróbować użyć trwałych połączeń z bazą danych z serwerem zaplecza.
 
* Serwer MySQL

   * [Połączenia MySQLi dla nowszych](https://www.php.net/manual/mysqli.quickstart.connections.php) wersji
   * [mysql_pconnect](https://www.php.net/manual/function.mysql-pconnect.php) dla starszych wersji PHP

* Inne źródła danych

   * [Zarządzanie połączeniami PHP](https://www.php.net/manual/en/pdo.connections.php)

#### <a name="python"></a>Python

* [Mysql](https://github.com/mysqljs/mysql#pooling-connections)
* [Mongodb](https://blog.mlab.com/2017/05/mongodb-connection-pooling-for-express-applications/)
* [PostgreSQL](https://node-postgres.com/features/pooling)
* [SQL Server](https://github.com/tediousjs/node-mssql#connection-pools) (UWAGA: SQLAlchemy może być używany z innymi bazami danych oprócz MicrosoftSQL Server)
* [HTTP Keep-alive](https://requests.readthedocs.io/en/master/user/advanced/#keep-alive)(Keep-Alive jest automatyczne podczas korzystania z sesji [sesji session-objects](https://requests.readthedocs.io/en/master/user/advanced/#keep-alive)).

W przypadku innych środowisk przejrzyj dostawców lub dokumenty specyficzne dla sterownika w celu zaimplementowania buforowania połączeń w aplikacjach.

### <a name="modify-the-application-to-reuse-connections"></a>Modyfikowanie aplikacji w celu ponownego użycia połączeń

*  Aby uzyskać dodatkowe wskaźniki i przykłady dotyczące zarządzania połączeniami w funkcjach platformy Azure, zapoznaj [się z zarządzaniem połączeniami w usłudze Azure Functions](https://docs.microsoft.com/azure/azure-functions/manage-connections).

### <a name="modify-the-application-to-use-less-aggressive-retry-logic"></a>Modyfikowanie aplikacji w celu użycia mniej agresywnej logiki ponawiania prób

* Aby uzyskać dodatkowe wskazówki i przykłady, przejrzyj [wzorzec ponawiania próby](https://docs.microsoft.com/azure/architecture/patterns/retry).

### <a name="use-keepalives-to-reset-the-outbound-idle-timeout"></a>Użyj keepalives, aby zresetować limit czasu bezczynny ruchu wychodzącego

* Aby zaimplementować keepalives dla aplikacji Node.js, przejrzyj [Moją aplikację węzła jest wykonywanie nadmiernych połączeń wychodzących](https://docs.microsoft.com/azure/app-service/app-service-web-nodejs-best-practices-and-troubleshoot-guide#my-node-application-is-making-excessive-outbound-calls).

### <a name="additional-guidance-specific-to-app-service"></a>Dodatkowe wskazówki dotyczące usługi App Service:

* [Test obciążenia](https://docs.microsoft.com/azure/devops/test/load-test/app-service-web-app-performance-test) powinien symulować rzeczywiste dane w stałej prędkości podawania. Testowanie aplikacji i funkcji w warunkach stresu w świecie rzeczywistym może z wyprzedzeniem identyfikować i rozwiązywać problemy z wyczerpaniem portów SNAT.
* Upewnij się, że usługi zaplecza mogą szybko zwracać odpowiedzi. Aby rozwiązać problemy z wydajnością w bazie danych SQL platformy Azure, zapoznaj [się z problemami z wydajnością usługi Azure SQL Database za pomocą aplikacji Intelligent Insights](https://docs.microsoft.com/azure/sql-database/sql-database-intelligent-insights-troubleshoot-performance#recommended-troubleshooting-flow).
* Skalowanie w poziomie planu usługi app service do większej liczby wystąpień. Aby uzyskać więcej informacji na temat skalowania, zobacz [Skalowanie aplikacji w usłudze Azure App Service](https://docs.microsoft.com/azure/app-service/manage-scale-up). Każde wystąpienie procesu roboczego w planie usługi aplikacji jest przydzielane szereg portów SNAT. Jeśli rozłożysz użycie na więcej wystąpień, możesz uzyskać użycie portu SNAT na wystąpienie poniżej zalecanego limitu 100 połączeń wychodzących na unikatowy zdalny punkt końcowy.
* Należy rozważyć przejście do [środowiska usługi app service (ASE),](https://docs.microsoft.com/azure/app-service/environment/using-an-ase)gdzie są przydzielane jeden wychodzący adres IP, a limity dla połączeń i portów SNAT są znacznie wyższe.

Unikanie wychodzących limitów TCP jest łatwiejsze do rozwiązania, ponieważ limity są ustawiane przez rozmiar pracownika. Możesz zobaczyć limity w [limitach numerycznych między piaskownicami — połączenia TCP](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits)

|Nazwa limitu|Opis|Mały (A1)|Średni (A2)|Duży (A3)|Warstwa izolowana (ASE)|
|---|---|---|---|---|---|
|Połączenia|Liczba połączeń na całej maszynie Wirtualnej|1920|3968|8064|16 000|

Aby uniknąć wychodzących limitów TCP, można zwiększyć rozmiar pracowników lub skalować w poziomie.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Znajomość dwóch typów limitów połączeń wychodzących i tego, co robi aplikacja, powinna ułatwić rozwiązywanie problemów. Jeśli wiesz, że aplikacja wykonuje wiele wywołań do tego samego konta magazynu, można podejrzewać limit SNAT. Jeśli aplikacja tworzy wiele wywołań do punktów końcowych w całym Internecie, można podejrzewać, że osiągasz limit maszyny Wirtualnej.

Jeśli nie znasz zachowania aplikacji na tyle, aby szybko ustalić przyczynę, istnieją pewne narzędzia i techniki dostępne w usłudze App Service, aby pomóc w tym określeniem.

### <a name="find-snat-port-allocation-information"></a>Znajdowanie informacji o alokacji portów SNAT

[Diagnostyka usługi app service](https://docs.microsoft.com/azure/app-service/overview-diagnostics) służy do znajdowania informacji o alokacji portów SNAT i obserwować metrykę alokacji portów SNAT w witrynie usługi app service. Aby znaleźć informacje o alokacji portów SNAT, wykonaj następujące kroki:

1. Aby uzyskać dostęp do diagnostyki usługi App Service, przejdź do aplikacji sieci Web usługi App Service lub środowiska usługi app service w [witrynie Azure portal](https://portal.azure.com/). W lewej nawigacji wybierz pozycję **Diagnozuj i rozwiązuj problemy**.
2. Wybierz kategorię dostępności i wydajności
3. Wybierz kafelek SNAT Port Exhaustion na liście dostępnych kafelków w kategorii. Praktyka jest utrzymanie go poniżej 128.
Jeśli tego potrzebujesz, nadal możesz otworzyć bilet pomocy technicznej, a inżynier pomocy technicznej otrzyma dla Ciebie dane z zaplecza.

Należy zauważyć, że ponieważ użycie portu SNAT nie jest dostępne jako metryka, nie jest możliwe skalowanie automatyczne na podstawie użycia portu SNAT lub skonfigurowanie skalowania automatycznego na podstawie metryki alokacji portów SNAT.

### <a name="tcp-connections-and-snat-ports"></a>Połączenia TCP i porty SNAT

Połączenia TCP i porty SNAT nie są bezpośrednio powiązane. Detektor użycia połączeń TCP znajduje się w bloku Diagnozowanie i rozwiązywanie problemów dowolnej witryny usługi App Service. Wyszukaj frazę "Połączenia TCP", aby ją znaleźć.

* Porty SNAT są używane tylko dla zewnętrznych przepływów sieciowych, podczas gdy całkowita liczba połączeń TCP obejmuje lokalne połączenia sprzężenia zwrotnego.
* Port SNAT może być współużytkowane przez różne przepływy, jeśli przepływy są różne w protokole, adresie IP lub porcie. Metryka Połączenia TCP zlicza każde połączenie TCP.
* Limit połączeń TCP odbywa się na poziomie wystąpienia procesu roboczego. Równoważenie obciążenia wychodzącego usługi Azure Network nie używa metryki Połączenia TCP do ograniczania portów SNAT.
* Limity połączeń TCP są opisane w [limitach numerycznych między maszynami wirtualnymi piaskownicy — połączenia TCP](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits)

|Nazwa limitu|Opis|Mały (A1)|Średni (A2)|Duży (A3)|Warstwa izolowana (ASE)|
|---|---|---|---|---|---|
|Połączenia|Liczba połączeń na całej maszynie Wirtualnej|1920|3968|8064|16 000|

### <a name="webjobs-and-database-connections"></a>Połączenia WebJobs i Database
 
Jeśli porty SNAT są wyczerpane, gdzie WebJobs nie mogą połączyć się z bazą danych SQL platformy Azure, nie ma żadnych metryk, aby pokazać, ile połączeń są otwierane przez każdy proces aplikacji sieci web. Aby znaleźć problematyczne WebJob, przenieść kilka WebJobs się do innego planu usługi app service, aby sprawdzić, czy sytuacja się poprawia lub jeśli problem pozostaje w jednym z planów. Powtarzaj ten proces, aż znajdziesz problematyczną robotę WebJob.

### <a name="using-snat-ports-sooner"></a>Wcześniejsze korzystanie z portów SNAT

Nie można zmienić żadnych ustawień platformy Azure, aby szybciej zwolnić używane porty SNAT, ponieważ wszystkie porty SNAT zostaną zwolnione zgodnie z poniższymi warunkami, a zachowanie jest projektowane.
 
* Jeśli serwer lub klient wyśle FINACK, [port SNAT zostanie zwolniony](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#tcp-snat-port-release) po 240 sekundach.
* Jeśli zostanie widoczna rst, port SNAT zostanie zwolniony po 15 sekundach.
* Jeśli limit czasu bezczynnego został osiągnięty, port jest zwalniany.
 
## <a name="additional-information"></a>Dodatkowe informacje

* [SNAT z usługą app service](https://4lowtherabbit.github.io/blogs/2019/10/SNAT/)
* [Rozwiązywanie problemów z powolną wydajnością aplikacji w usłudze Azure App Service](https://docs.microsoft.com/azure/app-service/troubleshoot-performance-degradation)
