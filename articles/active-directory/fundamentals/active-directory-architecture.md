---
title: Omówienie architektury — usługa Azure Active Directory | Dokumenty firmy Microsoft
description: Dowiedz się, czym jest dzierżawa usługi Azure Active Directory i jak zarządzać platformą Azure przy użyciu usługi Azure Active Directory.
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
ms.openlocfilehash: 854fb4649f8c1113f20abe5807dd0ce473ba6ee3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77368068"
---
# <a name="what-is-the-azure-active-directory-architecture"></a>Co to jest architektura usługi Azure Active Directory?

Usługa Azure Active Directory (Azure AD) umożliwia bezpieczne zarządzanie dostępem do usług i zasobów platformy Azure dla użytkowników. W ramach usługi Azure AD można skorzystać z pełnego zestawu możliwości zarządzania tożsamościami. Aby uzyskać więcej informacji na temat funkcji usługi Azure AD, zobacz [Co to jest usługa Azure Active Directory?](active-directory-whatis.md)

Za pomocą usługi Azure AD możesz tworzyć użytkowników i grupy oraz zarządzać nimi, a także zezwalać na dostęp do zasobów organizacji i odmawiać go przy użyciu uprawnień. Aby uzyskać informacje dotyczące zarządzania tożsamościami, zobacz [The fundamentals of Azure identity management](active-directory-whatis.md) (Podstawy zarządzania tożsamościami platformy Azure).

## <a name="azure-ad-architecture"></a>Architektura usługi Azure AD

Geograficzna architektura usługi Azure AD łączy w sobie rozbudowane funkcje monitorowania, automatycznego przekierowywania, pracy awaryjnej i odzyskiwania, które zapewniają klientom dostępność i wydajność w całej firmie.

W tym artykule omówione są następujące elementy architektury:

*   Projekt architektury usługi
*   Skalowalność
*   Ciągła dostępność
*   Centra danych

### <a name="service-architecture-design"></a>Projekt architektury usługi

Najczęstszym sposobem tworzenia dostępnego i użytecznego, bogatego w dane systemu jest niezależne bloki konstrukcyjne lub jednostki skalowania. W przypadku warstwy danych usługi Azure AD jednostki skalowania są nazywane *partycjami*.

Warstwa danych ma kilka usług frontonu, które zapewniają możliwość odczytu i zapisu. Na poniższym diagramie pokazano, jak składniki partycji z jednym katalogiem są dostarczane w geograficznie rozproszonych centrach danych.

  ![Diagram partycji z jednym katalogem](./media/active-directory-architecture/active-directory-architecture.png)

Składniki architektury usługi Azure AD obejmują repliki podstawowe i pomocnicze.

#### <a name="primary-replica"></a>Replika podstawowa

*Replika podstawowa* odbiera wszystkie operacje *zapisu* dla partycji, do której należy. Wszystkie operacje zapisu są natychmiast replikowane do repliki pomocniczej znajdującej się w innym centrum danych przed zwróceniem do obiektu wywołującego informacji o powodzeniu, co pozwala na zapewnienie nadmiarowej geograficznie trwałości zapisów.

#### <a name="secondary-replicas"></a>Repliki pomocnicze

Wszystkie *odczyty katalogów* są obsługiwane z *replik pomocniczych,* które znajdują się w centrach danych, które fizycznie znajdują się w różnych lokalizacjach geograficznych. Istnieje wiele replik pomocniczych, ponieważ dane są replikowane w sposób asynchroniczny. Odczyty katalogu, takie jak żądania uwierzytelniania, są obsługiwane z centrów danych, które znajdują się blisko klientów. Repliki pomocnicze są odpowiedzialne za skalowalność odczytu.

### <a name="scalability"></a>Skalowalność

Skalowalność to możliwość rozszerzania działania usługi w związku z rosnącymi wymaganiami dotyczącymi wydajności. Skalowalność zapisu jest osiągana przez partycjonowanie danych. Skalowalność odczytu uzyskuje się przez replikację danych z jednej partycji do wielu replik pomocniczych rozproszonych na całym świecie.

Żądania z aplikacji katalogowych są kierowane do centrum danych, które są fizycznie najbliżej. Zapisy są w sposób niewidoczny dla użytkownika przekierowywane do repliki podstawowej w celu zapewnienia spójności odczytu i zapisu. Repliki pomocnicze znacznie rozszerzają skalę partycji, ponieważ katalogi zazwyczaj przez większość czasu obsługują operacje odczytu.

Aplikacje katalogów nawiązują połączenie z najbliższymi centrami danych. To połączenie zwiększa wydajność i dlatego skalowanie w poziomie jest możliwe. Ponieważ partycje katalogów mogą mieć wiele replik pomocniczych, repliki pomocnicze można umieścić bliżej klientów katalogu. Tylko wewnętrzne składniki usługi katalogu przeprowadzające wiele operacji odczytu bezpośrednio komunikują się z aktywną repliką podstawową.

### <a name="continuous-availability"></a>Ciągła dostępność

Dostępność (lub czas dostępności) definiuje możliwość wykonywania przez system nieprzerwanej pracy. Kluczem do wysokiej dostępności usługi Azure AD jest to, że usługi mogą szybko przenosić ruch w wielu geograficznie rozproszonych centrach danych. Każde centrum danych jest niezależne, co umożliwia dekorelowane tryby awarii. Dzięki temu projektowi o wysokiej dostępności usługa Azure AD nie wymaga żadnych przestojów w działaniach konserwacyjnych.

Projekt partycji usługi Azure AD jest uproszczony w porównaniu do projektu usługi AD w przedsiębiorstwie przy użyciu projektu z jednym wzorem, który zawiera starannie zaaranżowany i deterministyczny proces pracy awaryjnej repliki podstawowej.

#### <a name="fault-tolerance"></a>Odporność na uszkodzenia

System jest bardziej dostępny, jeśli jest odporny na błędy sprzętu, sieci i oprogramowania. Dla każdej partycji w katalogu istnieje replika główna o wysokiej dostępności — replika podstawowa. W ramach tej repliki są wykonywane tylko operacje zapisu. Ta replika jest stale i ściśle monitorowana, a operacje zapisu w przypadku wykrycia błędu mogą zostać natychmiast przełączone do innej repliki, która staje się wtedy nową repliką podstawową. Podczas pracy w trybie failover może to być związane z utratą dostępności do zapisu trwającą zwykle od 1 do 2 minut. Taka sytuacja nie ma wpływu na dostępność do odczytu.

Operacje odczytu (których liczba jest wiele rzędów wielkości większa od operacji zapisu) odbywają się tylko za pośrednictwem replik pomocniczych. Ponieważ repliki pomocnicze są idempotentne, utrata dowolnej repliki w danej partycji jest łatwo kompensowana przez przełączenie operacji odczytu do innej repliki, która zwykle znajduje się w tym samym centrum danych.

#### <a name="data-durability"></a>Trwałość danych

Zapis jest trwale zobowiązana do co najmniej dwóch centrów danych przed jego potwierdzeniem. Dzieje się tak, najpierw zatwierdzając zapis na podstawowym, a następnie natychmiast replikując zapis do co najmniej jednego innego centrum danych. Ta akcja zapisu zapewnia, że potencjalna katastrofalna utrata centrum danych hostowania podstawowego nie powoduje utraty danych.

Usługa Azure AD utrzymuje zero [recovery time objective (RTO),](https://en.wikipedia.org/wiki/Recovery_time_objective) aby nie utracić danych w przypadku pracy awaryjnej. Obejmuje to:

* Odczyty i odczyty tokenów
* Zezwalanie tylko na około 5 minut RTO dla zapisów katalogu

### <a name="datacenters"></a>Centra danych

Repliki usługi Azure AD są przechowywane w centrach danych znajdujących się na całym świecie. Aby uzyskać więcej informacji, zobacz [infrastruktura globalna platformy Azure](https://azure.microsoft.com/global-infrastructure/).

Usługa Azure AD działa w centrach danych o następujących cechach:

* Uwierzytelnianie, wykres i inne usługi AD znajdują się za usługą Bramy. Brama zarządza równoważeniem obciążenia tych usług. Zostanie ona automatycznie przejęta w stan faila, jeśli wszystkie serwery w złej kondycji zostaną wykryte przy użyciu sond kondycji transakcyjnej. Na podstawie tych sond kondycji gateway dynamicznie kieruje ruch do zdrowych centrów danych.
* W przypadku *odczytów*katalog ma repliki pomocnicze i odpowiadające im usługi front-end w konfiguracji aktywny-aktywny działającej w wielu centrach danych. W przypadku awarii całego centrum danych ruch zostanie automatycznie przekierowany do innego centrum danych.
 * W przypadku *zapisów*katalog będzie awaryjnie nad repliką podstawową (wzorcową) w centrach danych za pośrednictwem planowanych (nowy podstawowy jest synchronizowany ze starymi podstawowymi) lub awaryjnymi procedurami pracy awaryjnej. Trwałość danych uzyskuje się przez replikowanie dowolnego zatwierdzenia w co najmniej dwóch centrach danych.

#### <a name="data-consistency"></a>Spójność danych

Model katalogu jest jednym z ewentualnych spójności. Typowym problemem z rozproszonymi systemami replikacji asynchronicznie jest to, że dane zwrócone z repliki "określonej" mogą nie być aktualne. 

Usługa Azure AD zapewnia spójność odczytu i zapisu dla aplikacji działających w ramach repliki pomocniczej, kierując jej operacje zapisu do repliki podstawowej i synchronicznie pobierając operacje zapisu do repliki pomocniczej.

Zapisy aplikacji przy użyciu interfejsu API programu Microsoft Graph usługi Azure AD są pobierane od zachowania koligacji do repliki katalogu dla spójności odczytu i zapisu. Usługa interfejsu API programu Microsoft Graph utrzymuje sesję logiczną, która ma koligację z repliką pomocniczą używaną do odczytów; koligacja jest przechwytywany w "token repliki", że usługa buforuje przy użyciu rozproszonej pamięci podręcznej w centrum danych repliki pomocniczej. Token ten jest następnie używany na potrzeby kolejnych operacji w ramach tej samej sesji logicznej. Aby kontynuować korzystanie z tej samej sesji logicznej, kolejne żądania muszą być kierowane do tego samego centrum danych usługi Azure AD. Nie jest możliwe kontynuowanie sesji logicznej, jeśli żądania klientów katalogu są kierowane do wielu centrów danych usługi Azure AD; jeśli tak się stanie, klient ma wiele sesji logicznych, które mają niezależne konsystencje odczytu i zapisu.

 >[!NOTE]
 >Operacje zapisu są natychmiast replikowane do repliki pomocniczej, dla której zostały utworzone operacje odczytu sesji logicznej.

#### <a name="backup-protection"></a>Ochrona kopii zapasowych

Katalog implementuje usuwanie elastyczne zamiast usuwania całkowitego w celu umożliwienia użytkownikom i dzierżawcom łatwego odzyskiwania w razie wykonania przypadkowego usunięcia przez klienta. Jeśli administrator dzierżawy przypadkowo usunie użytkowników, może łatwo cofnąć i przywrócić usuniętych użytkowników.

Usługa Azure AD implementuje codzienne tworzenie kopii zapasowych wszystkich danych i w związku z tym umożliwia autorytatywne przywrócenie danych w przypadku wykonania jakichkolwiek logicznych operacji usuwania lub uszkodzenia danych. Warstwa danych używa kodów korygujących błędy, dzięki czemu może sprawdzać błędy i automatycznie poprawiać określone typy błędów dysku.

#### <a name="metrics-and-monitors"></a>Metryki i monitory

Uruchamianie usługi o wysokiej dostępności wymaga światowej klasy metryk i możliwości monitorowania. Usługa Azure AD stale analizuje i raportuje kluczowe metryki kondycji usługi i kryteria powodzenia dla każdej ze swoich usług. Istnieje również ciągłe opracowywanie i dostrajanie metryk oraz monitorowania i alertów dla każdego scenariusza, w ramach każdej usługi Azure AD i we wszystkich usługach.

Jeśli jakakolwiek usługa Azure AD nie działa zgodnie z oczekiwaniami, natychmiast podejmuje się działania w celu przywrócenia funkcji tak szybko, jak to możliwe. Najważniejsze metryki usługi Azure AD śledzi jak szybko problemy z witryną na żywo można wykryć i złagodzić dla klientów. Ciężko pracujemy nad funkcjami monitorowania i alertów w celu zminimalizowania czasu wykrywania (docelowy czas wykrywania: poniżej 5 minut) oraz nad gotowością operacyjną w celu zminimalizowania czasu usuwania problemu (docelowy czas usuwania problemu: poniżej 30 minut).

#### <a name="secure-operations"></a>Zabezpieczanie operacji

Przy użyciu kontroli operacyjnych, takich jak uwierzytelnianie wieloskładnikowe (MFA) dla każdej operacji, a także inspekcji wszystkich operacji. Ponadto, przy użyciu systemu just-in-time elevation do udzielania niezbędnych tymczasowy dostęp dla wszelkich zadań operacyjnych na żądanie na bieżąco. Aby uzyskać więcej informacji, zobacz [Zaufana chmura](https://azure.microsoft.com/support/trust-center).

## <a name="next-steps"></a>Następne kroki

[Przewodnik dewelopera usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop)