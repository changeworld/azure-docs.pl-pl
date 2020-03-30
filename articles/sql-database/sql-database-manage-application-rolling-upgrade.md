---
title: Stopniowe uaktualnienia aplikacji
description: Dowiedz się, jak używać replikacji geograficznej usługi Azure SQL Database do obsługi uaktualnień online aplikacji w chmurze.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 02/13/2019
ms.openlocfilehash: 9c627c3e597fdcd3859ce02ea208fc7a8b5d612b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73822853"
---
# <a name="manage-rolling-upgrades-of-cloud-applications-by-using-sql-database-active-geo-replication"></a>Zarządzanie uaktualnianiem stopniowym aplikacji w chmurze przy użyciu aktywnej replikacji geograficznej bazy danych SQL

Dowiedz się, jak używać [aktywnej replikacji geograficznej](sql-database-auto-failover-group.md) w usłudze Azure SQL Database, aby umożliwić stopniowe uaktualnianie aplikacji w chmurze. Ponieważ uaktualnienia są destrukcyjne operacje, powinny one być częścią planowania ciągłości biznesowej i projektowania. W tym artykule przyjrzymy się dwóm różnym metodom organizowania procesu uaktualniania i omówimy korzyści i kompromisy każdej opcji. Na potrzeby tego artykułu odnosimy się do aplikacji, która składa się z witryny sieci Web, która jest połączona z pojedynczej bazy danych jako jego warstwy danych. Naszym celem jest uaktualnienie wersji 1 (V1) aplikacji do wersji 2 (V2) bez znaczącego wpływu na środowisko użytkownika.

Oceniając opcje uaktualnienia, należy wziąć pod uwagę następujące czynniki:

* Wpływ na dostępność aplikacji podczas uaktualniania, na przykład jak długo funkcje aplikacji mogą być ograniczone lub ulec pogorszeniu.
* Możliwość wycofania, jeśli uaktualnienie nie powiedzie się.
* Luka w zabezpieczeniach aplikacji w przypadku wystąpienia niepowiązanego, katastrofalnego błędu podczas uaktualniania.
* Całkowity koszt w dolarach. Współczynnik ten obejmuje dodatkowe nadmiarowość bazy danych i przyrostowe koszty składników tymczasowych używanych przez proces uaktualniania.

## <a name="upgrade-applications-that-rely-on-database-backups-for-disaster-recovery"></a>Uaktualnianie aplikacji, które opierają się na kopiach zapasowych bazy danych w celu odzyskiwania po awarii

Jeśli aplikacja opiera się na automatycznych kopii zapasowych bazy danych i używa przywracania geograficznego do odzyskiwania po awarii, jest wdrażany w jednym regionie platformy Azure. Aby zminimalizować zakłócenia użytkownika, należy utworzyć środowisko przejściowe w tym regionie ze wszystkimi składnikami aplikacji zaangażowanymi w uaktualnienie. Pierwszy diagram ilustruje środowisko operacyjne przed procesem uaktualniania. Punkt końcowy `contoso.azurewebsites.net` reprezentuje środowisko produkcyjne aplikacji sieci web. Aby można było wycofać uaktualnienie, należy utworzyć środowisko przejściowe z w pełni zsynchronizowanymi kopiami bazy danych. Wykonaj następujące kroki, aby utworzyć środowisko przejściowe dla uaktualnienia:

1. Utwórz pomocniczą bazę danych w tym samym regionie platformy Azure. Monitorowanie pomocniczego, aby sprawdzić, czy proces wysiewu został zakończony (1).
2. Utwórz nowe środowisko dla aplikacji sieci web i nazwij je "Przejściowe". Zostanie on zarejestrowany w usłudze `contoso-staging.azurewebsites.net` Azure DNS z adresem URL (2).

> [!NOTE]
> Te kroki przygotowania nie będą miały wpływu na środowisko produkcyjne, które może działać w trybie pełnego dostępu.

![Konfiguracja replikacji geograficznej bazy danych SQL do odzyskiwania po awarii w chmurze.](media/sql-database-manage-application-rolling-upgrade/option1-1.png)

Po zakończeniu etapów przygotowania aplikacja jest gotowa do rzeczywistego uaktualnienia. Następny diagram ilustruje kroki związane z procesem uaktualniania:

1. Ustaw podstawową bazę danych w trybie tylko do odczytu (3). Ten tryb gwarantuje, że środowisko produkcyjne aplikacji sieci web (V1) pozostaje tylko do odczytu podczas uaktualniania, zapobiegając w ten sposób rozbieżności danych między wystąpieniami bazy danych V1 i V2.
2. Odłącz pomocniczą bazę danych przy użyciu planowanego trybu zakończenia (4). Ta akcja tworzy w pełni zsynchronizowany, niezależna kopia podstawowej bazy danych. Ta baza danych zostanie uaktualniona.
3. Obróć pomocniczą bazę danych w tryb odczytu i zapisu i uruchom skrypt uaktualnienia (5).

![Konfiguracja replikacji geograficznej bazy danych SQL do odzyskiwania po awarii w chmurze.](media/sql-database-manage-application-rolling-upgrade/option1-2.png)

Jeśli uaktualnienie zakończy się pomyślnie, teraz można przystąpić do przełączania użytkowników do uaktualnionej kopii aplikacji, która staje się środowiskiem produkcyjnym. Przełączanie obejmuje kilka kroków, jak pokazano na następnym diagramie:

1. Uaktywnianie operacji wymiany między środowiskami produkcyjnymi i przejściowymi aplikacji sieci web (6). Ta operacja przełącza adresy URL obu środowisk. Teraz `contoso.azurewebsites.net` wskazuje wersję V2 witryny sieci web i bazy danych (środowisko produkcyjne). 
2. Jeśli nie potrzebujesz już wersji V1, która stała się kopia tymczasowa po zamianie, można zlikwidować środowisko przejściowe (7).

![Konfiguracja replikacji geograficznej bazy danych SQL do odzyskiwania po awarii w chmurze.](media/sql-database-manage-application-rolling-upgrade/option1-3.png)

Jeśli proces uaktualniania nie powiedzie się (na przykład z powodu błędu w skrypcie uaktualnienia), należy wziąć pod uwagę środowisko przejściowe, które zostały naruszone. Aby przywrócić aplikację do stanu sprzed uaktualnienia, przywróć aplikację w środowisku produkcyjnym do pełnego dostępu. Następny diagram przedstawia kroki powrotu:

1. Ustaw kopię bazy danych w tryb odczytu i zapisu (8). Ta akcja przywraca pełną funkcjonalność V1 kopii produkcyjnej.
2. Wykonaj analizę głównej przyczyny i likwiduj środowisko przejściowe (9).

W tym momencie aplikacja jest w pełni funkcjonalna i można powtórzyć kroki uaktualnienia.

> [!NOTE]
> Wycofywanie nie wymaga zmian DNS, ponieważ nie wykonasz jeszcze operacji wymiany.

![Konfiguracja replikacji geograficznej bazy danych SQL do odzyskiwania po awarii w chmurze.](media/sql-database-manage-application-rolling-upgrade/option1-4.png)

Kluczową zaletą tej opcji jest to, że można uaktualnić aplikację w jednym regionie, wykonując zestaw prostych kroków. Koszt aktualizacji w dolarach jest stosunkowo niski. 

Głównym kompromisem jest to, że jeśli wystąpi katastrofalny błąd podczas uaktualniania, odzyskiwanie do stanu sprzed uaktualnienia obejmuje ponowne wdrożenie aplikacji w innym regionie i przywracanie bazy danych z kopii zapasowej przy użyciu przywracania geograficznego. Ten proces powoduje znaczne przestoje.

## <a name="upgrade-applications-that-rely-on-database-geo-replication-for-disaster-recovery"></a>Uaktualnianie aplikacji, które opierają się na replikacji geograficznej bazy danych w celu odzyskiwania po awarii

Jeśli aplikacja używa aktywnej replikacji geograficznej lub grup automatycznego trybu failover dla ciągłości biznesowej, jest wdrażana w co najmniej dwóch różnych regionach. Istnieje aktywna, podstawowa baza danych w regionie podstawowym i pomocnicza baza danych tylko do odczytu w regionie kopii zapasowej. Wraz z czynnikami wymienionymi na początku tego artykułu, proces aktualizacji musi również zagwarantować, że:

* Aplikacja pozostaje chroniona przed katastrofalnymi awariami przez cały czas podczas procesu uaktualniania.
* Składniki geograficznie nadmiarowe aplikacji są uaktualniane równolegle z aktywnymi składnikami.

Aby osiągnąć te cele, oprócz korzystania ze środowisk aplikacji sieci Web, będziesz korzystać z usługi Azure Traffic Manager przy użyciu profilu trybu failover z jednego aktywnego punktu końcowego i jeden punkt końcowy kopii zapasowej. Następny diagram ilustruje środowisko operacyjne przed procesem uaktualniania. Witryn sieci `contoso-1.azurewebsites.net` `contoso-dr.azurewebsites.net` web i reprezentują środowisko produkcyjne aplikacji z pełną nadmiarowości geograficznej. Środowisko produkcyjne obejmuje następujące składniki:

* Środowisko produkcyjne aplikacji `contoso-1.azurewebsites.net` sieci web w regionie podstawowym (1)
* Podstawowa baza danych w regionie podstawowym (2)
* Wystąpienie wstrzymania aplikacji sieci web w regionie kopii zapasowej (3)
* Replikowana geograficznie pomocnicza baza danych w regionie kopii zapasowej (4)
* Profil wydajności usługi Traffic Manager z `contoso-1.azurewebsites.net` wywoływanym punktem końcowym online i punktem końcowym offline o nazwie`contoso-dr.azurewebsites.net`

Aby umożliwić wycofanie uaktualnienia, należy utworzyć środowisko przejściowe z w pełni zsynchronizowanymi kopiami aplikacji. Ponieważ należy upewnić się, że aplikacja może szybko odzyskać w przypadku wystąpienia katastrofalnej awarii podczas procesu uaktualniania, środowisko przejściowe musi być również geobalemalna. Aby utworzyć środowisko przejściowe dla uaktualnienia, wymagane są następujące kroki:

1. Wdrażanie środowiska przejściowego aplikacji sieci web w regionie podstawowym (6).
2. Utwórz pomocniczą bazę danych w podstawowym regionie platformy Azure (7). Skonfiguruj środowisko przejściowe aplikacji sieci web, aby się z nim połączyć. 
3. Utwórz inną geobbędową pomocniczą bazę danych w regionie kopii zapasowej, replikując pomocniczą bazę danych w regionie podstawowym. (Ta metoda jest *nazywana replikacją geograficzną łańcuchową.)* (8).
4. Wdrażanie środowiska przejściowego wystąpienia aplikacji sieci web w regionie kopii zapasowej (9) i konfigurowanie go do łączenia pomocniczej bazy danych geograficznie nadmiarowej utworzonej w (8).

> [!NOTE]
> Te kroki przygotowania nie wpłynie na aplikację w środowisku produkcyjnym. Pozostanie w pełni funkcjonalny w trybie odczytu i zapisu.

![Konfiguracja replikacji geograficznej bazy danych SQL do odzyskiwania po awarii w chmurze.](media/sql-database-manage-application-rolling-upgrade/option2-1.png)

Po zakończeniu etapów przygotowania środowisko przejściowe jest gotowe do uaktualnienia. Następny diagram ilustruje następujące kroki uaktualniania:

1. Ustaw podstawową bazę danych w środowisku produkcyjnym na tryb tylko do odczytu (10). Ten tryb gwarantuje, że produkcyjna baza danych (V1) nie zmieni się podczas uaktualniania, zapobiegając w ten sposób rozbieżności danych między wystąpieniami bazy danych V1 i V2.

```sql
-- Set the production database to read-only mode
ALTER DATABASE <Prod_DB>
SET (ALLOW_CONNECTIONS = NO)
```

2. Zakończ replikację geograficzną, odłączając pomocniczą (11). Ta akcja tworzy niezależną, ale w pełni zsynchronizowana kopię produkcyjnej bazy danych. Ta baza danych zostanie uaktualniona. W poniższym przykładzie użyto transact-SQL, ale [program PowerShell](/powershell/module/az.sql/remove-azsqldatabasesecondary?view=azps-1.5.0) jest również dostępny. 

```sql
-- Disconnect the secondary, terminating geo-replication
ALTER DATABASE <Prod_DB>
REMOVE SECONDARY ON SERVER <Partner-Server>
```

3. Uruchom skrypt uaktualnienia względem `contoso-1-staging.azurewebsites.net`programu , `contoso-dr-staging.azurewebsites.net`i podstawowego bazy danych przemieszczania (12). Zmiany bazy danych zostaną automatycznie zreplikowane do pomocniczego przemieszczania.

![Konfiguracja replikacji geograficznej bazy danych SQL do odzyskiwania po awarii w chmurze.](media/sql-database-manage-application-rolling-upgrade/option2-2.png)

Jeśli uaktualnienie zakończy się pomyślnie, możesz teraz przystąpić do przełączania użytkowników do wersji V2 aplikacji. Następny diagram ilustruje kroki:

1. Uaktywnianie operacji wymiany między środowiskami produkcyjnymi i przejściowymi aplikacji sieci web w regionie podstawowym (13) i w regionie kopii zapasowej (14). Wersja 2 aplikacji staje się teraz środowiskiem produkcyjnym z nadmiarową kopią w regionie kopii zapasowej.
2. Jeśli nie jest już potrzebna aplikacja V1 (15 i 16), można zlikwidować środowisko przejściowe.

![Konfiguracja replikacji geograficznej bazy danych SQL do odzyskiwania po awarii w chmurze.](media/sql-database-manage-application-rolling-upgrade/option2-3.png)

Jeśli proces uaktualniania nie powiedzie się (na przykład z powodu błędu w skrypcie uaktualnienia), należy wziąć pod uwagę środowiska przejściowego w niespójnym stanie. Aby przywrócić aplikację do stanu sprzed uaktualnienia, wróć do używania wersji 1 aplikacji w środowisku produkcyjnym. Wymagane kroki są wyświetlane na następnym diagramie:

1. Ustaw podstawową kopię bazy danych w środowisku produkcyjnym na tryb odczytu i zapisu (17). Ta akcja przywraca pełną funkcjonalność V1 w środowisku produkcyjnym.
2. Wykonaj analizę przyczyn źródłowych i napraw lub usuń środowisko przejściowe (18 i 19).

W tym momencie aplikacja jest w pełni funkcjonalna i można powtórzyć kroki uaktualnienia.

> [!NOTE]
> Wycofywanie nie wymaga zmian DNS, ponieważ nie wykonasz operacji wymiany.

![Konfiguracja replikacji geograficznej bazy danych SQL do odzyskiwania po awarii w chmurze.](media/sql-database-manage-application-rolling-upgrade/option2-4.png)

Kluczową zaletą tej opcji jest to, że można uaktualnić zarówno aplikację, jak i jej kopię geob nadmiarową równolegle bez uszczerbku dla ciągłości biznesowej podczas uaktualniania.

Głównym kompromisem jest to, że wymaga podwójnej redundancji każdego składnika aplikacji, a zatem wiąże się z wyższym kosztem dolara. Obejmuje to również bardziej skomplikowany przepływ pracy.

## <a name="summary"></a>Podsumowanie

Dwie metody uaktualniania opisane w artykule różnią się złożonością i kosztem dolara, ale obie koncentrują się na minimalizowaniu czasu, przez jaki użytkownik jest ograniczony do operacji tylko do odczytu. Ten czas jest bezpośrednio zdefiniowany przez czas trwania skryptu uaktualnienia. Nie zależy to od rozmiaru bazy danych, wybranej warstwy usług, konfiguracji witryny sieci Web ani innych czynników, których nie można łatwo kontrolować. Wszystkie kroki przygotowania są oddzielone od kroków uaktualnienia i nie mają wpływu na aplikację produkcyjną. Wydajność skryptu uaktualniania jest kluczowym czynnikiem, który określa środowisko użytkownika podczas uaktualniania. Tak, najlepszym sposobem, aby poprawić to doświadczenie jest skupić swoje wysiłki na podejmowaniu skrypt uaktualnienia tak wydajne, jak to możliwe.

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z omówieniem ciągłości biznesowej i scenariuszami, zobacz [Omówienie ciągłości biznesowej](sql-database-business-continuity.md).
* Aby dowiedzieć się więcej o aktywnej replikacji geograficznej usługi Azure SQL Database, zobacz [Tworzenie czytelnych pomocniczych baz danych przy użyciu aktywnej replikacji geograficznej](sql-database-active-geo-replication.md).
* Aby dowiedzieć się więcej o grupach automatycznego trybu failover usługi Azure SQL Database, zobacz [Używanie grup automatycznego trybu failover w celu włączenia przezroczystego i skoordynowanego trybu failover wielu baz danych.](sql-database-auto-failover-group.md)
* Aby dowiedzieć się więcej o środowiskach przejściowych w usłudze Azure App Service, zobacz [Konfigurowanie środowisk przejściowych w usłudze Azure App Service.](../app-service/deploy-staging-slots.md)
* Aby dowiedzieć się więcej o profilach usługi Azure Traffic Manager, zobacz [Zarządzanie profilem usługi Azure Traffic Manager](../traffic-manager/traffic-manager-manage-profiles.md).
