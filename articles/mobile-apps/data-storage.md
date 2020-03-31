---
title: Przechowywanie, zarządzanie i utrwalanie danych aplikacji w chmurze za pomocą visual studio App Center i usług platformy Azure
description: Dowiedz się więcej o usługach, takich jak Visual Studio App Center, które umożliwiają przechowywanie danych aplikacji mobilnych w chmurze, zarządzanie nimi i utrwalanie ich.
author: codemillmatt
ms.assetid: 12344321-0123-4678-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 166847325fa9094136f1c2a143f1751420f05f66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240910"
---
# <a name="store-sync-and-query-mobile-application-data-from-the-cloud"></a>Przechowywanie, synchronizowanie i wykonywanie zapytań o dane aplikacji mobilnych z chmury
Bez względu na to, jaki rodzaj aplikacji tworzysz, prawdopodobnie będziesz generować i przetwarzać dane. Użytkownicy aplikacji mają wysokie oczekiwania. Chcą, aby aplikacja działała szybko i bezproblemowo, w każdych okolicznościach. Większość aplikacji działa również na wielu urządzeniach. Możesz uzyskać dostęp do aplikacji z komputera lub urządzenia przenośnego. Wielu użytkowników może korzystać z aplikacji w tym samym czasie i udostępniać dane z oczekiwaniem uzyskania natychmiastowego i rzeczywistego dostępu do danych.

Użytkownicy aplikacji nie zawsze będą mieli połączenie z Internetem. Aplikacje są zaprojektowane i oczekuje się, że będą działać z lub bez połączenia z Internetem. Deweloperzy muszą wybrać odpowiednie rozwiązanie do przechowywania i synchronizowania danych z chmurą, aby zapewnić doskonałe środowisko dla ich aplikacji.

Firma Microsoft oferuje szereg usług, które eliminują potrzebę rozkręcenia serwerów, pobrania bazy danych lub martwienia się o skalę lub zabezpieczenia, aby zapewnić jak najasformniejsze środowisko. Te usługi zapewniają doskonałe środowisko deweloperskie, które umożliwia przechowywanie danych aplikacji w chmurze przy użyciu interfejsów API SQL lub NoSQL. Można również synchronizować dane na wszystkich urządzeniach i włączyć aplikację do pracy z lub bez połączenia sieciowego, aby pomóc w tworzeniu skalowalnych i niezawodnych aplikacji.

Skorzystaj z następujących usług do zarządzania danymi aplikacji mobilnych i przechowywania ich w chmurze.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[Usługa App Center Data](/appcenter/data/) to usługa zarządzania danymi. Umożliwia aplikacjom zarządzanie, utrwalanie i synchronizowanie danych aplikacji w chmurze na różnych urządzeniach i platformach w scenariuszach online i offline. Oparte na bazie usługi Azure Cosmos DB dane usługi App Center są skalowane w miarę rozwoju bazy użytkowników i liczby aplikacji. Zapewnia małe opóźnienia, wysoką dostępność i wysoką skalowalność wszystkich danych.

**Najważniejsze funkcje**
- Łatwe inicjowanie obsługi administracyjnej nowej bazy danych usługi Azure Cosmos DB lub istniejącej bazy danych usługi Azure Cosmos DB z portalu centrum aplikacji programu Visual Studio.
- Obsługa bazy danych NoSQL do łatwego przechowywania, synchronizowania i wyszukiwania danych aplikacji.
- Ta usługa, oparta na usłudze Azure Cosmos DB, dziedziczy większość kluczowych funkcji oferowanych przez usługę Azure Cosmos DB i może być skalowana globalnie w celu zaspokojenia potrzeb biznesowych.
- Funkcje synchronizacji online i offline do synchronizowania danych między urządzeniami.
- Zestawy SDK klientów mobilnych, których można używać do łatwego zarządzania danymi aplikacji prywatnych.
- Obsługa platformy dla systemów iOS, Android, Xamarin i React Native.

**Odwołania**
- [Zarejestruj się w programie Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Wprowadzenie do usługi Dane usługi App Center](/appcenter/data/getting-started)

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[Usługa Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) to globalnie rozproszona usługa bazy danych z wieloma modelami. Można go używać do tworzenia aplikacji na skalę planety. Dzięki usłudze Azure Cosmos DB można elastycznie i niezależnie skalować przepływność i magazyn w dowolnej liczbie regionów platformy Azure na całym świecie. Możesz korzystać z szybkiego, jednocyfrowego dostępu do danych w milisekundach przy użyciu ulubionych powierzchni interfejsu API. Powierzchnie te obejmują SQL, MongoDB, Cassandra, Tabele lub Gremlin. Usługa Azure Cosmos DB w unikatowy sposób zapewnia kompleksowe umowy dotyczące poziomu usług (SLA) dotyczące przepływności, opóźnienia, dostępności i spójności.

**Najważniejsze funkcje**
- Obsługuje szeroką gamę interfejsów API, w tym interfejs API SQL (Core), interfejs API Cassandra, interfejs API mongodb, interfejs API Gremlin i interfejs API tabeli.
- Globalna dystrybucja "pod klucz" replikuje dane wszędzie tam, gdzie są użytkownicy. Użytkownicy mogą wchodzić w interakcje z repliką danych, które są im najbliższe.
- Brak zarządzania schematem lub indeksem, ponieważ aparat bazy danych jest w pełni niezależny od schematu.
- Wszechobecna obecność regionalna, ponieważ usługa Azure Cosmos DB jest dostępna we wszystkich regionach platformy Azure na całym świecie, która obejmuje ponad 54 regiony w chmurze publicznej.
- Precyzyjnie zdefiniowane, wiele opcji spójności, ponieważ protokół replikacji wielu wzorcowych usługi Azure Cosmos DB został starannie zaprojektowany, aby oferować pięć dobrze zdefiniowanych opcji spójności. Te pięć opcji są silne, ograniczone nieaktualność, sesja, spójny prefiks i ewentualne.
- Dostępność 99,999% zarówno dla odczytów, jak i zapisów.
- Programowo (lub za pośrednictwem witryny Azure portal) wywołać regionalnej pracy awaryjnej konta usługi Azure Cosmos DB, aby upewnić się, że aplikacja jest zaprojektowany, aby wytrzymać regionalną awarię.
- Gwarantowane małe opóźnienia na 99 percentylu na całym świecie.

**Odwołania**
- [Portal Azure](https://portal.azure.com) 
- [Dokumentacja usługi Azure Cosmos DB](/azure/cosmos-db/introduction)

## <a name="azure-sql-database"></a>Azure SQL Database
 [Usługa Azure SQL Database](https://azure.microsoft.com/services/sql-database/) to usługa zarządzana relacyjnej bazy danych ogólnego przeznaczenia. Można go użyć do utworzenia warstwy magazynu danych o wysokiej dostępności i wysokiej wydajności dla aplikacji i rozwiązań w chmurze platformy Azure.

**Najważniejsze funkcje**
- **Elastyczne modele i narzędzia baz danych:** Dzięki elastycznej bazie danych deweloperzy mogą łączyć zasoby między grupą baz danych do skalowania. Aby administracyjnie zarządzać tymi zasobami, należy przesłać skrypt jako zadanie. Baza danych SQL następnie wykonuje skrypt w bazach danych.
- **Wysoka wydajność:** Aplikacje o wysokiej przepływie mogą korzystać z najnowszej wersji. Zapewnia o 25% więcej mocy bazy danych premium.
- **Kopie zapasowe, replikacja i wysoka dostępność:** Wbudowana replikacja i usługa SLA wspierana przez firmę Microsoft na poziomie bazy danych zapewniają ciągłość aplikacji i ochronę przed katastrofalnymi zdarzeniami. Aktywna replikacja geograficzna umożliwia skonfigurowanie pracy awaryjnej i przywracania samoobsługowego, które zapewniają pełną kontrolę nad "odzyskiwaniem oops". Przywracanie danych jest dostępne z kopii zapasowych danych do 35 dni.
- **Konserwacja bliska zeru:** Automatyczne oprogramowanie jest częścią usługi. Wbudowane repliki systemu pomagają zapewnić nieodłączną ochronę danych, dyspozycyjność bazy danych i stabilność systemu. Repliki systemu są automatycznie przenoszone na nowe komputery. Są one aprowizacji w locie, jak stare nie.
- **Bezpieczeństwo:** Usługa Azure SQL Database oferuje portfolio funkcji zabezpieczeń spełnianych zasadami zgodności zgodnie z wymaganiami organizacji lub zasad zgodności zgodnie z wymaganiami branżowymi:
    - Inspekcja zapewnia deweloperom możliwość wykonywania zadań związanych z zgodnością i zdobywania wiedzy na temat działań.
    - Deweloperzy i IT można zaimplementować zasady na poziomie bazy danych, aby ograniczyć dostęp do poufnych danych z zabezpieczeniami na poziomie wiersza, dynamiczne maskowanie danych i przezroczyste szyfrowanie danych dla usługi Azure SQL Database.
    - Usługa Azure SQL Database jest weryfikowana przez kluczowych audytorów chmury w ramach zakresu kluczowych certyfikatów i zatwierdzeń zgodności platformy Azure, takich jak HIPAA BAA, ISO/IEC 27001:2005, FedRAMP i klauzule modelu UE.

**Odwołania**
- [Portal Azure](https://portal.azure.com) 
- [Dokumentacja bazy danych SQL usługi Azure](/azure/sql-database/) 
