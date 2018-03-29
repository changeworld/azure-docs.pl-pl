---
title: Wprowadzenie do platformy Azure rozwiązania Cosmos DB Cassandra interfejsu API | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać bazy danych Azure rozwiązania Cosmos do istniejących aplikacji "przyrostu i shift" i tworzyć nowe aplikacje przy użyciu interfejsu API Cassandra sterowniki Cassandra i CQL znasz już.
services: cosmos-db
author: govindk
manager: jhubbard
documentationcenter: ''
ms.assetid: 73839abf-5af5-4ae0-a852-0f4159bc00a0
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/20/2017
ms.author: govindk
ms.openlocfilehash: 88364cecc1fa1ad7318cb28c9708a42e6a807347
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2018
---
# <a name="introduction-to-azure-cosmos-db-apache-cassandra-api"></a>Wprowadzenie do platformy Azure rozwiązania Cosmos DB: Apache Cassandra interfejsu API

Azure DB rozwiązania Cosmos udostępnia interfejs API Cassandra (wersja zapoznawcza) dla aplikacji, które są przeznaczone dla Apache Cassandra czy wymagają możliwości premium, takich jak:

* [Rozmiar magazynu skalowalne i przepływność](partition-data.md).
* [Globalne dystrybucji gotowe](distribute-data-globally.md)
* Opóźnienie rzędu kilku milisekund na poziomie 99. percentyla.
* [Pięć dobrze zdefiniowane poziomy spójności](consistency-levels.md)
* [Automatyczne indeksowanie danych](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) bez konieczności postępowania w przypadku zarządzania schematu i indeksu. 
* Zagwarantować wysokiej dostępności, wszystkie kopie przez [branży umowy SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/)

## <a name="what-is-the-azure-cosmos-db-apache-cassandra-api"></a>Co to jest interfejs API Azure rozwiązania Cosmos bazy danych Apache Cassandra?

Azure DB rozwiązania Cosmos może służyć do przechowywania danych dla aplikacji napisanych dla [Apache Cassandra](https://cassandra.apache.org/), korzystając z interfejsu API Cassandra Apache. Oznacza to, że przy użyciu istniejących [Apache licencjonowane sterowniki zgodne z CQLv4](https://cassandra.apache.org/doc/latest/getting_started/drivers.html?highlight=driver), tworzonych na potrzeby Cassandra mogą teraz komunikować się z interfejsu API Azure rozwiązania Cosmos DB Cassandra aplikacji. W wielu przypadkach można przełączyć się z pomocą Apache Cassandra przy użyciu interfejsu API Cassandra Apache Azure rozwiązania Cosmos DB, zmieniając po prostu ciąg połączenia. Z tej funkcji, umożliwia łatwe tworzenie i uruchamianie aplikacji bazy danych Cassandra interfejsu API na platformie Azure w chmurze z dystrybucji globalne DB rozwiązania Cosmos Azure i [kompleksowe SLA branży](https://azure.microsoft.com/support/legal/sla/cosmos-db), przy użyciu znanych umiejętności i narzędzia Cassandra interfejsu API.

![Interfejs Azure Cosmos DB Cassandra API](./media/cassandra-introduction/cosmosdb-cassandra.png)

Interfejs API Cassandra umożliwia interakcję z danymi przechowywanymi w usłudze Azure DB rozwiązania Cosmos za pomocą narzędzi Cassandra język zapytań na podstawie (na przykład CQLSH) i sterowników klienta Cassandra znasz już. Dowiedz się więcej informacji na ten temat to Microsoft Mechanics wideo z Kirill Gavrylyuk główny Menedżer zespołu inżynieryjnego.

> [!VIDEO https://www.youtube.com/embed/1Sf4McGN1AQ]
>

## <a name="what-is-the-benefit-of-using-apache-cassandra-api-for-azure-cosmos-db"></a>Co to jest użycie interfejsu API Cassandra Apache dla bazy danych rozwiązania Cosmos Azure?

**Nie operacji zarządzania**: jako wartość PRAWDA w pełni zarządzane usługi, bazy danych Azure rozwiązania Cosmos gwarantuje, że administratorzy Cassandra interfejsu API nie martwiąc się o zarządzanie i monitorowanie wiele ustawień na pliki systemu operacyjnego, JVM i yaml programu oraz ich wzajemne oddziaływanie. Azure DB rozwiązania Cosmos zapewnia monitorowanie przepływności, opóźnienia, magazynu i dostępności i można skonfigurować alerty. 

**Zarządzanie wydajnością**: bazy danych rozwiązania Cosmos Azure udostępnia odczyty małych opóźnieniach umowy SLA dla kopii i zapisuje do 99-ty percentyl. Użytkownicy nie muszą się martwić o partii nakłady operacyjne, aby zapewnić dobrą odczytu i zapisu umów SLA. Obejmują one zazwyczaj planowania kompaktowanie zarządzania reliktów, filtry rozwiniętej ustawienie i odchyłki repliki. Azure DB rozwiązania Cosmos przyjmuje optymalizacji obaw związanych z zarządzaniem te problemy i pozwala skupić się na materiałów aplikacji.

**Automatyczne indeksowanie**: bazy danych rozwiązania Cosmos Azure automatycznie indeksuje wszystkie kolumny tabeli w bazie danych Cassandra interfejsu API. Azure DB rozwiązania Cosmos nie ma konieczności tworzenia indeksów pomocniczych w celu przyspieszenia zapytania. Go zapewnia małe opóźnienia odczytu i zapisu podczas wykonywania spójne automatycznego indeksowania. 

**Możliwość używania istniejącego kodu i narzędzia**: bazy danych rozwiązania Cosmos Azure udostępnia podczas transmisji protokołu poziom zgodności z istniejących zestawy SDK i narzędzia. Zapewnia zgodność z tym służy istniejącą kodu Cassandra interfejsu API Azure rozwiązania Cosmos DB trivial zmiany.

**Elastyczność przepływność i magazyn**: platformy Azure rozwiązania Cosmos zapewnia elastyczność gwarantowanej przepustowości w regionach za pośrednictwem portalu proste, programu PowerShell lub interfejsu wiersza polecenia. Można też elastycznie skalować tabele bazy danych rozwiązania Cosmos Azure z przewidywalną wydajnością bezproblemowo wraz z rozwojem aplikacji. Azure DB rozwiązania Cosmos obsługuje tabel Cassandra interfejsu API, które można skalować do niemal nieograniczonego rozmiaru magazynu. 

**Globalne dystrybucji i dostępności**: bazy danych rozwiązania Cosmos Azure zapewnia możliwość dystrybucji danych w regionach platformy Azure, aby umożliwić użytkownikom przy użyciu środowiska małe opóźnienia przy jednoczesnym zapewnieniu dostępności. Azure DB rozwiązania Cosmos zapewnia dostępność 99,99% w obrębie regionu i 99,999% odczytu dostępności w regionach z całkowitej operacji. Azure DB rozwiązania Cosmos jest dostępna w 30 + [regiony platformy Azure](https://azure.microsoft.com/regions/services/). Dowiedz się więcej w [globalnie dystrybucji danych](distribute-data-globally.md). 

**Wybór spójności**: bazy danych rozwiązania Cosmos Azure zapewnia możliwość osiągnięcia optymalnego kompromisu między wydajnością a spójnością pięć dobrze zdefiniowane poziomy spójności. Te poziomy spójności są sesji silne, nieaktualność, prefiks spójne i "ostateczna". Te poziomy spójności szczegółowe, dokładnie zdefiniowane Zezwalaj developer dokonanie dźwięku kompromis między spójności, dostępnością i opóźnieniem. Aby dowiedzieć się więcej, zobacz [Maksymalizowanie dostępności i wydajności za pomocą poziomów spójności](consistency-levels.md). 

**Klasy Enterprise**: bezpieczne i zgodne domyślnie — Azure rozwiązania cosmos DB dostarcza [certyfikaty zgodności](https://www.microsoft.com/trustcenter) aby upewnić się, użytkownicy mogą korzystać z platformy, nie martwiąc się o problemach ze zgodnością. Azure DB rozwiązania Cosmos także szyfrowanie magazynowanych i ruchu, IP zapory i dzienniki inspekcji dla działania płaszczyzny sterowania.  

<a id="sign-up-now"></a>
## <a name="sign-up-now"></a>Utwórz konto teraz 

Jeśli masz już subskrypcję platformy Azure, możesz je założyć wziąć udział w programie Cassandra interfejsu API (wersja zapoznawcza) [portalu Azure](https://aka.ms/cosmosdb-cassandra-signup).  Jeśli jesteś nowym użytkownikiem usługi Azure, należy zarejestrować się w celu [bezpłatnej wersji próbnej](https://azure.microsoft.com/free) gdzie uzyskać 12 miesięcy bezpłatny dostęp do bazy danych Azure rozwiązania Cosmos. Wykonaj poniższe kroki, aby zażądać dostępu do programu Cassandra interfejsu API (wersja zapoznawcza).

1. W [portalu Azure](https://portal.azure.com), kliknij przycisk **Utwórz zasób** > **baz danych** > **bazy danych Azure rozwiązania Cosmos**. 

2. Na stronie nowe konto, wybierz **Cassandra** w polu interfejsu API. 

3. W **subskrypcji** Wybierz subskrypcję platformy Azure, który ma być używany dla tego konta.

4. Kliknij przycisk **Zarejestruj się, aby wyświetlić podgląd dzisiaj**.

    ![Interfejs Azure Cosmos DB Cassandra API](./media/cassandra-introduction/cassandra-sign-up.png)

3. W rejestracji do podglądu dziś okienku, kliknij przycisk **OK**. 

    Po przesłaniu żądania stan zmienia się na **oczekują na zatwierdzenie** w okienku konta. 

Po przesłaniu żądania, poczekaj na powiadomienie e-mail, żądanie zostanie zatwierdzone. Ze względu na dużą liczbę żądań otrzymasz powiadomienie w ciągu tygodnia. Nie jest konieczne tworzenie biletu pomocy technicznej, aby ukończyć żądanie. Żądania zostanie zweryfikowana w kolejności, w jakiej zostały odebrane. 

## <a name="how-to-get-started"></a>Jak zacząć
Po przyłączeniu program w wersji zapoznawczej, wykonaj quickstarts Cassandra interfejsu API, aby utworzyć aplikację przy użyciu interfejsu API Cassandra:

* [Szybki Start: Tworzenie Cassandra aplikacji sieci web Node.js i bazy danych Azure rozwiązania Cosmos](create-cassandra-nodejs.md)
* [Szybki Start: Tworzenie Cassandra aplikacja sieci web za pomocą języka Java i bazy danych Azure rozwiązania Cosmos](create-cassandra-java.md)
* [Szybki Start: Tworzenie Cassandra aplikacji sieci web z usług .NET i bazy danych Azure rozwiązania Cosmos](create-cassandra-dotnet.md)
* [Szybki Start: Tworzenie Cassandra aplikacja sieci web za pomocą języka Python i bazy danych Azure rozwiązania Cosmos](create-cassandra-python.md)

## <a name="next-steps"></a>Kolejne kroki

Informacje o interfejsie API Cassandra DB rozwiązania Cosmos Azure została zintegrowana z ogólną dokumentację bazy danych Azure rozwiązania Cosmos, ale poniżej przedstawiono kilka wskazówek, które ułatwią rozpoczęcie pracy:

* Postępuj zgodnie z [Quickstarts](create-cassandra-nodejs.md) do utworzenia konta i nową aplikację przy użyciu przykładowych Git
* Postępuj zgodnie z [samouczek](tutorial-develop-cassandra-java.md) programowo utworzyć nową aplikację.
* Postępuj zgodnie z [Cassandra importowania danych samouczek](cassandra-import-data.md) do importowania istniejących danych do bazy danych Azure rozwiązania Cosmos.
* Odczyt [— często zadawane pytania](faq.md#cassandra).
