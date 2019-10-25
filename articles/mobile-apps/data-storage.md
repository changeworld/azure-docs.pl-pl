---
title: Przechowywanie i utrwalanie danych aplikacji w chmurze oraz zarządzanie nimi za pomocą usług Visual Studio App Center i Azure
description: Dowiedz się więcej na temat usług, takich jak App Center, które umożliwiają przechowywanie i utrwalanie danych aplikacji mobilnych w chmurze oraz zarządzanie nimi.
author: elamalani
ms.assetid: 12344321-0123-4678-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: b7043c7e9bd944bfb3633397edfa3c35609bd8ec
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795577"
---
# <a name="store-sync-and-query-mobile-application-data-from-the-cloud"></a>Przechowuj, Synchronizuj i badaj dane aplikacji mobilnych z chmury
Niezależnie od tego, jakiego rodzaju aplikacja jest tworzona, prawdopodobnie będziesz generować i przetwarzać dane. Oczekiwania użytkowników są duże i chcą, aby aplikacja działała szybko i bezproblemowo. Większość aplikacji pracuje na wielu urządzeniach, dlatego podczas uzyskiwania dostępu do aplikacji można uzyskać do niej dostęp z poziomu pulpitu lub urządzenia przenośnego. Wielu użytkowników może korzystać z aplikacji w tym samym czasie i udostępniać dane przy użyciu oczekiwań, aby uzyskiwać dostęp do danych w czasie rzeczywistym i na bieżąco.

W tym przypadku użytkownicy aplikacji nie zawsze będą mieć łączność z Internetem. aplikacje są zaprojektowane i powinny działać z połączeniem internetowym lub bez niego. Ze względu na wszystkie te rosnące złożoności, deweloperzy mogą wybrać odpowiednie rozwiązanie do przechowywania i synchronizowania danych w chmurze w celu zapewnienia doskonałego środowiska klienta dla swojej aplikacji.

Firma Microsoft oferuje różne usługi, które eliminują potrzebę zwiększania liczby serwerów, wybierania bazy danych lub przejmowania się skalowaniem lub zabezpieczeniami w celu zapewnienia jak największej możliwości. Te usługi zapewniają wspaniałe środowisko programistyczne, które umożliwia przechowywanie danych aplikacji w chmurze przy użyciu interfejsów API SQL lub NoSQL, synchronizowanie danych na wszystkich urządzeniach i umożliwienie aplikacji pracy z połączeniem sieciowym lub bez niego, aby ułatwić tworzenie skalowalnych i niezawodnych zastosowania.

Użyj następujących usług, aby zarządzać danymi aplikacji mobilnych i przechowywać je w chmurze.

## <a name="visual-studio-app-center"></a>Centrum aplikacji programu Visual Studio
[App Center dane](/appcenter/data/) to usługa zarządzania danymi, która umożliwia aplikacjom zarządzanie, utrwalanie i synchronizowanie danych aplikacji w chmurze na różnych urządzeniach i w scenariuszach w trybie online i offline. Usługa **oparta na usłudze Cosmos DB** , która skaluje się w miarę rozwoju użytkownika i liczby aplikacji, zapewnia małe opóźnienia, wysoką dostępność i wysoką skalowalność dla wszystkich danych.

**Najważniejsze funkcje**
- Łatwa **Obsługa nowej bazy danych Cosmos DB** lub **nawiązywanie połączenia z istniejącą bazą danych Cosmos DB** z poziomu portalu App Center.
- **Obsługa bazy danych NoSQL** umożliwia łatwe przechowywanie, synchronizowanie i wykonywanie zapytań dotyczących danych aplikacji.
- **Oparta na Cosmos DB**, ta usługa dziedziczy większość najważniejszych funkcji oferowanych przez Azure Cosmos DB i może **skalować się globalnie** , aby zaspokoić potrzeby biznesowe.
- Możliwości **synchronizacji w trybie online i offline** w celu synchronizowania danych między urządzeniami.
- **Zestawy SDK klienta** mobilnego, które umożliwiają łatwe zarządzanie jednocześnie prywatnymi danymi aplikacji.
- **Obsługa platform** — iOS, Android, Xamarin, reaguje na natywne.

**Wołują**
- [Zarejestruj się w usłudze App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Wprowadzenie do danych App Center](/appcenter/data/getting-started)

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) to globalnie dystrybuowana, wielomodelowa usługa bazy danych firmy Microsoft, która ułatwia tworzenie aplikacji w skali globalnej. Kliknięcie Cosmos DB przycisku umożliwia elastyczne i niezależne skalowanie przepływności i magazynu w dowolnej liczbie regionów świadczenia usługi Azure na całym świecie. Można elastycznie skalować przepływność i magazyn oraz korzystać z szybkiego, jednocyfrowego dostępu do danych za pomocą ulubionych powierzchni interfejsu API, w tym SQL, MongoDB, Cassandra, tabel lub Gremlin. Cosmos DB jednoznacznie zapewnia kompleksowe umowy dotyczące poziomu usług (umowy SLA) na potrzeby przepływności, opóźnień, dostępności i spójności.

**Najważniejsze funkcje**
- Obsługuje **szeroką gamę interfejsów** API, w tym interfejs API SQL (rdzeń), interfejs API Cassandra, interfejs API MongoDB, interfejs API Gremlin i interfejs API tabel.
- **Gotowe Global Distribution** replikuje dane wszędzie tam, gdzie się znajdują użytkownicy, dzięki czemu użytkownicy będą mogli korzystać z repliki danych znajdujących się najbliżej nich.
- **Brak zarządzania schematem lub indeksem** , ponieważ aparat bazy danych jest w pełni niezależny od.
- Powszechna **obecność regionalnej obecności** jako Cosmos DB jest dostępna we wszystkich regionach świadczenia usługi Azure na całym świecie, w tym w odniesieniu do 54 regionów w chmurze publicznej.
- **Precyzyjnie zdefiniowana, wiele opcji spójności** jako protokołu wieloskładnikowej replikacji Cosmos DB jest starannie zaprojektowana w celu zaoferowania pięciu dobrze zdefiniowanych opcji spójności — silnej, ograniczonej nieodświeżoności, sesji, spójnego prefiksu i ostatecznej.
- **dostępność na 99,999%** dla operacji odczytu i zapisu.
- **Programowo (lub za pośrednictwem portalu) Wywołaj regionalną pracę w trybie failover** dla konta Cosmos, aby upewnić się, że aplikacja została zaprojektowana pod kątem regionalnej awarii.
- **Gwarantowane małe opóźnienia w 99 percentylu na**całym świecie.

**Wołują**
- [Azure Portal](https://portal.azure.com) 
- [Łączoną](/azure/cosmos-db/introduction)   

## <a name="azure-sql-database"></a>Azure SQL Database
 [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) to usługa zarządzana relacyjnej bazy danych ogólnego przeznaczenia, która umożliwia tworzenie warstwy magazynu danych o wysokiej dostępności dla aplikacji i rozwiązań w chmurze platformy Azure.

**Najważniejsze funkcje**
- **Elastyczne modele baz danych i narzędzia** — Elastyczna baza danych zapewnia deweloperom możliwość puli zasobów do użycia między grupami baz danych w celu skalowania, które można następnie zarządzać administracyjnie, przesyłając skrypt jako zadanie, a SQL Database wykonuje skrypt między bazami danych.
- **Wysoka wydajność** — aplikacje o wysokiej przepływności mogą korzystać z najnowszej wersji, która zapewnia 25% więcej mocy bazy danych w warstwie Premium.
- **Kopie zapasowe, replikacja i wysoka dostępność** — wbudowana replikacja i Umowa SLA firmy Microsoft na poziomie bazy danych zapewniają ciągłość działania aplikacji oraz ochronę przed katastrofami. Aktywna replikacja geograficzna umożliwia skonfigurowanie trybu failover i samoobsługowego przywracania, które zapewnia pełną kontrolę nad "Niestety odzyskiwaniem" (Przywracanie danych z dostępnych kopii zapasowych danych przez maksymalnie 35 dni).
- **Obsługa niemal zero** — automatyczne oprogramowanie jest częścią usługi, a wbudowane repliki systemu pomagają zapewnić wewnętrzną ochronę danych, nieprzerwaną pracę bazy danych i stabilność systemu. Repliki systemu są automatycznie przenoszone na nowe komputery, które są dynamicznie uaktywniane z chwilą awarii starych komputerów.
- **Zabezpieczenia** — SQL Database oferuje portfolio funkcji zabezpieczeń, które są zgodne z obowiązującymi zasadami zgodności organizacyjnych lub branżowych
    - Inspekcja zapewnia deweloperom możliwość wykonywania zadań związanych z zgodnością i uzyskiwania informacji o działaniach.
    - Deweloperzy i mogą wdrożyć zasady na poziomie bazy danych, aby ograniczyć dostęp do poufnych danych z zabezpieczeniami na poziomie wierszy, dynamicznym maską danych i przezroczystym szyfrowaniem danych dla Azure SQL Database.
    - SQL Database jest weryfikowana przez najważniejszych audytorów chmury w ramach zakresu najważniejszych certyfikatów i zatwierdzeń zgodności platformy Azure, takich jak HIPAA BAA, ISO/IEC 27001:2005, FedRAMP i klauzule modelowe UE.

**Wołują**
- [Azure Portal](https://portal.azure.com) 
- [Łączoną](/azure/sql-database/)
   