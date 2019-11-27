---
title: Omówienie architektury — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się, jakie dzierżawy usługi Azure Active Directory i jak nią zarządzać za pomocą usługi Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7d0511f008a3d5bc39a0fb2d9406d33b72dbede6
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2019
ms.locfileid: "74532940"
---
# <a name="what-is-the-azure-active-directory-architecture"></a>Co to jest architektura usługi Azure Active Directory?

Usługa Azure Active Directory (Azure AD) umożliwia bezpieczne zarządzanie dostępem do usług i zasobów platformy Azure dla użytkowników. W ramach usługi Azure AD można skorzystać z pełnego zestawu możliwości zarządzania tożsamościami. Aby uzyskać więcej informacji na temat funkcji usługi Azure AD, zobacz [Co to jest usługa Azure Active Directory?](active-directory-whatis.md)

Za pomocą usługi Azure AD możesz tworzyć użytkowników i grupy oraz zarządzać nimi, a także zezwalać na dostęp do zasobów organizacji i odmawiać go przy użyciu uprawnień. Aby uzyskać informacje dotyczące zarządzania tożsamościami, zobacz [The fundamentals of Azure identity management](active-directory-whatis.md) (Podstawy zarządzania tożsamościami platformy Azure).

## <a name="azure-ad-architecture"></a>Architektura usługi Azure AD

Rozproszona geograficznie Architektura usługi Azure AD łączy rozbudowane monitorowanie, automatyczne przekierowywanie, pracy awaryjnej i funkcje odzyskiwania, które dostarczają firmie dostępność i wydajność dla klientów.

W tym artykule omówione są następujące elementy architektury:

*   Projekt architektury usługi
*   Skalowalność
*   Ciągła dostępność
*   Centra danych

### <a name="service-architecture-design"></a>Projekt architektury usługi

Najbardziej typowym sposobem na skompilowanie dostępnego i możliwego do użycia systemu danych jest użycie niezależnych bloków konstrukcyjnych lub jednostek skalowania. W przypadku warstwy danych usługi Azure AD jednostki skalowania są nazywane *partycjami*.

Warstwa danych ma kilka usług frontonu, które zapewniają możliwość odczytu i zapisu. Na poniższym diagramie przedstawiono sposób, w jaki składniki partycji pojedynczego katalogu są dostarczane w obrębie rozproszonych geograficznie centrów danych.

  ![Diagram partycji z jednym katalogiem](./media/active-directory-architecture/active-directory-architecture.png)

Składniki architektury usługi Azure AD obejmują repliki podstawowe i pomocnicze.

#### <a name="primary-replica"></a>Replika podstawowa

*Replika podstawowa* odbiera wszystkie operacje *zapisu* dla partycji, do której należy. Wszystkie operacje zapisu są natychmiast replikowane do repliki pomocniczej znajdującej się w innym centrum danych przed zwróceniem do obiektu wywołującego informacji o powodzeniu, co pozwala na zapewnienie nadmiarowej geograficznie trwałości zapisów.

#### <a name="secondary-replicas"></a>Repliki pomocnicze

Wszystkie *odczyty* katalogów są obsługiwane z *replik pomocniczych*, które znajdują się w centrach danych, które fizycznie znajdują się na różnych lokalizacje geograficzne. Istnieje wiele replik pomocniczych, ponieważ dane są replikowane w sposób asynchroniczny. Odczyty katalogów, takie jak żądania uwierzytelniania, są serwisowane z centrów danych, które znajdują się blisko klientów. Repliki pomocnicze są odpowiedzialne za skalowalność odczytu.

### <a name="scalability"></a>Skalowalność

Skalowalność to możliwość rozszerzania działania usługi w związku z rosnącymi wymaganiami dotyczącymi wydajności. Skalowalność zapisu jest osiągana przez partycjonowanie danych. Skalowalność odczytu uzyskuje się przez replikację danych z jednej partycji do wielu replik pomocniczych rozproszonych na całym świecie.

Żądania z katalogu aplikacji są kierowane do centrum danych, które jest im fizycznie najbliższe. Zapisy są w sposób niewidoczny dla użytkownika przekierowywane do repliki podstawowej w celu zapewnienia spójności odczytu i zapisu. Repliki pomocnicze znacznie rozszerzają skalę partycji, ponieważ katalogi zazwyczaj przez większość czasu obsługują operacje odczytu.

Aplikacje katalogów nawiązują połączenie z najbliższymi centrami danych. To połączenie umożliwia zwiększenie wydajności i skalowania w poziomie jest możliwe. Ponieważ partycje katalogów mogą mieć wiele replik pomocniczych, repliki pomocnicze można umieścić bliżej klientów katalogu. Tylko wewnętrzne składniki usługi katalogu przeprowadzające wiele operacji odczytu bezpośrednio komunikują się z aktywną repliką podstawową.

### <a name="continuous-availability"></a>Ciągła dostępność

Dostępność (lub czas dostępności) definiuje możliwość wykonywania przez system nieprzerwanej pracy. Klucz do wysokiej dostępności w usłudze Azure AD polega na tym, że usługi mogą szybko przenoszony ruch na wiele rozproszonych geograficznie centrów danych. Każde centrum danych jest niezależne, co umożliwia usuwanie nieskorelowanych trybów awarii. W tym projekcie o wysokiej dostępności usługa Azure AD nie wymaga żadnych przestojów w przypadku działań konserwacyjnych.

Projekt partycji usługi Azure AD jest uproszczony w porównaniu do organizacyjnego projektu usługi AD, przy użyciu projekt pojedynczego elementu głównego, który obejmuje starannie zorganizowany i deterministyczny replikę podstawową procesu trybu failover.

#### <a name="fault-tolerance"></a>Odporność na uszkodzenia

System jest bardziej dostępny, jeśli jest odporny na błędy sprzętu, sieci i oprogramowania. Dla każdej partycji w katalogu istnieje replika główna o wysokiej dostępności — replika podstawowa. W ramach tej repliki są wykonywane tylko operacje zapisu. Ta replika jest stale i ściśle monitorowana, a operacje zapisu w przypadku wykrycia błędu mogą zostać natychmiast przełączone do innej repliki, która staje się wtedy nową repliką podstawową. Podczas pracy w trybie failover może to być związane z utratą dostępności do zapisu trwającą zwykle od 1 do 2 minut. Taka sytuacja nie ma wpływu na dostępność do odczytu.

Operacje odczytu (których liczba jest wiele rzędów wielkości większa od operacji zapisu) odbywają się tylko za pośrednictwem replik pomocniczych. Ponieważ repliki pomocnicze są idempotentne, utrata dowolnej repliki w danej partycji jest łatwo kompensowana przez przełączenie operacji odczytu do innej repliki, która zwykle znajduje się w tym samym centrum danych.

#### <a name="data-durability"></a>Trwałość danych

Zapis jest trwale zatwierdzany do co najmniej dwóch centrów danych przed potwierdzeniem. Jest to wykonywane przez pierwsze zatwierdzenie zapisu na serwerze podstawowym, a następnie natychmiastowe replikowanie zapisu do co najmniej jednego innego centrum danych. Ta akcja zapisu zapewnia, że potencjalna strata centrum danych, w którym znajduje się podstawowy, nie powoduje utraty.

Usługa Azure AD utrzymuje zerowy [cel czasu odzyskiwania (RTO)](https://en.wikipedia.org/wiki/Recovery_time_objective) , aby nie utracić danych w trybie failover. Obejmuje to:

* Wystawiania tokenów i operacje odczytu z katalogu
* Zezwalanie tylko około 5 minut cel czasu odzyskiwania do zapisu w katalogu

### <a name="datacenters"></a>Centra danych

Repliki usługi Azure AD są przechowywane w centrach danych znajdujących się na całym świecie. Aby uzyskać więcej informacji, zobacz [globalna infrastruktura platformy Azure](https://azure.microsoft.com/global-infrastructure/).

Usługa Azure AD działa w centrach danych o następujących cechach:

* Uwierzytelnianie, wykresu i inne usługi AD znajdują się za usługą bramy. Brama zarządza równoważeniem obciążenia tych usług. Zakończy się niepowodzeniem przez automatycznie wszystkie serwery w złej kondycji w przypadku wykrycia użycie sond kondycji transakcyjnych. W oparciu o te sondy kondycji Brama dynamicznie kieruje ruch do centrów danych w dobrej kondycji.
* W przypadku operacji *odczytu*katalog ma repliki pomocnicze i odpowiadające im usługi frontonu w konfiguracji aktywne-aktywne działające w wielu centrach danych. W przypadku awarii całego centrum danych ruch zostanie automatycznie przekierowany do innego centrum.
 \* W przypadku *operacji zapisu*katalog przejdzie do trybu failover (Master) repliki w centrach danych przez zaplanowaną (Nowa podstawowa jest synchronizowana z poprzednimi podstawowymi) lub awaryjnymi procedurami pracy awaryjnej. Trwałość danych jest uzyskiwana przez replikację wszelkich zatwierdzeń do co najmniej dwóch centrum danych.

#### <a name="data-consistency"></a>Spójność danych

Model katalogu to jeden z ostateczną wyborami. Jednym z typowych problemów z systemami rozproszonymi asynchronicznie replikowane jest, że z danymi zwróconymi z "konkretnej" repliki mogą nie być aktualne. 

Usługa Azure AD zapewnia spójność odczytu i zapisu dla aplikacji działających w ramach repliki pomocniczej, kierując jej operacje zapisu do repliki podstawowej i synchronicznie pobierając operacje zapisu do repliki pomocniczej.

Operacje zapisu aplikacji wykonywane za pomocą interfejsu API programu Graph usługi Azure AD są odseparowane od operacji zapewniania koligacji z repliką katalogu w celu zapewnienia spójności odczytu i zapisu. Usługa Azure AD Graph obsługuje sesję logiczną, która ma koligację z repliką pomocniczą używaną do odczytu; koligacja jest przechwytywana w "token repliki", który usługa grafuje pamięci podręcznej za pomocą rozproszonej pamięci podręcznej w pomocniczym centrum danych repliki. Token ten jest następnie używany na potrzeby kolejnych operacji w ramach tej samej sesji logicznej. Aby nadal używać tej samej sesji logicznej, kolejne żądania muszą być kierowane do tego samego centrum danych usługi Azure AD. Nie można kontynuować sesji logicznej, jeśli żądania klienta katalogu są kierowane do wielu centrów danych usługi Azure AD; w takim przypadku klient ma wiele sesji logicznych, które mają niezależne spójności odczytu i zapisu.

 >[!NOTE]
 >Operacje zapisu są natychmiast replikowane do repliki pomocniczej, dla której zostały utworzone operacje odczytu sesji logicznej.

#### <a name="backup-protection"></a>Ochrona kopii zapasowych

Katalog implementuje usuwanie elastyczne zamiast usuwania całkowitego w celu umożliwienia użytkownikom i dzierżawcom łatwego odzyskiwania w razie wykonania przypadkowego usunięcia przez klienta. Jeśli administrator dzierżawy przypadkowym usunie użytkowników, mogą łatwo cofnąć i przywrócić usuniętych użytkowników.

Usługa Azure AD implementuje codzienne tworzenie kopii zapasowych wszystkich danych i w związku z tym umożliwia autorytatywne przywrócenie danych w przypadku wykonania jakichkolwiek logicznych operacji usuwania lub uszkodzenia danych. Warstwy danych korzystają z kodu służącego do korygowania, dzięki czemu mogą sprawdzić błędy i automatyczne naprawienie określonych rodzajów błędów na dysku.

#### <a name="metrics-and-monitors"></a>Metryki i monitory

Uruchamianie usługi o wysokiej dostępności wymaga światowej klasy metryk i możliwości monitorowania. Usługa Azure AD stale analizuje i raportuje kluczowe metryki kondycji usługi i kryteria powodzenia dla każdej ze swoich usług. Dostępna jest również ciągłego rozwoju i dostrajanie metryk i monitorowania i alertów w każdym scenariuszu, w ramach każdej usługi Azure AD i wszystkich innych usług.

Jeśli z dowolnej usługi Azure AD nie działa zgodnie z oczekiwaniami, działania jest natychmiast podjęte w celu jak najszybszego przywrócenia jej funkcjonalności. Najważniejsze śledzi metryki usługi Azure AD jest jak szybko aktywnej witryny, problemy mogą być wykryte i skorygowane dla klientów. Ciężko pracujemy nad funkcjami monitorowania i alertów w celu zminimalizowania czasu wykrywania (docelowy czas wykrywania: poniżej 5 minut) oraz nad gotowością operacyjną w celu zminimalizowania czasu usuwania problemu (docelowy czas usuwania problemu: poniżej 30 minut).

#### <a name="secure-operations"></a>Bezpieczne operacje

Przy użyciu kontrole operacyjne, takie jak uwierzytelnianie wieloskładnikowe (MFA) dla każdej operacji, a także inspekcję wszystkich operacji. Ponadto przy użyciu systemu podniesienia uprawnień just-in-time udzielenia niezbędnego tymczasowego dostępu dla wszelkich operacyjnych zadań na żądanie w sposób ciągły. Aby uzyskać więcej informacji, zobacz [Zaufana chmura](https://azure.microsoft.com/support/trust-center).

## <a name="next-steps"></a>Następne kroki

[Przewodnik dewelopera usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop)