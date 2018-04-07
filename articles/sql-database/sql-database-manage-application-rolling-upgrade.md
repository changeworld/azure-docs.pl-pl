---
title: Wprowadzanie uaktualnień aplikacji — baza danych SQL Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać replikacja geograficzna bazy danych SQL Azure w celu obsługi online uaktualnień aplikacji w chmurze.
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: article
ms.date: 04/01/2018
ms.author: sashan
ms.openlocfilehash: d1799befb2b5f59f1794bbca2a1daa2a13990882
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="managing-rolling-upgrades-of-cloud-applications-using-sql-database-active-geo-replication"></a>Zarządzanie stopniowe aplikacji w chmurze przy użyciu aktywna replikacja geograficzna bazy danych SQL
> [!NOTE]
> [Aktywna replikacja geograficzna](sql-database-geo-replication-overview.md) jest teraz dostępna dla wszystkich baz danych w warstwach wszystkie.
> 

Dowiedz się, jak używać [— replikacja geograficzna](sql-database-geo-replication-overview.md) w bazie danych SQL umożliwia stopniowe aplikacji chmury. Ponieważ uaktualnienie jest dużo operacji, należy go części projektowania i planowania ciągłości biznesowej. W tym artykule możemy Spójrz na dwóch różnych metod organizowanie proces uaktualniania i omówimy korzyści i kompromisy każdej z nich. Do celów w tym artykule używamy prostą aplikację, która składa się z witryną sieci web podłączony do pojedynczej bazy danych jako jego warstwy danych. Naszym celem jest uaktualnienie wersji 1 aplikacji w wersji 2 bez jakiegokolwiek znaczącego wpływu na środowisko pracy użytkownika końcowego. 

Podczas obliczania opcji uaktualnienia należy uwzględnić następujące czynniki:

* Wpływ na dostępność aplikacji podczas uaktualniania. Jak długo funkcja aplikacji może być ograniczony lub nieprawidłowego działania.
* Możliwość wycofać w przypadku niepowodzenia uaktualnienia.
* Luki w zabezpieczeniach aplikacji po wystąpieniu błędu krytycznego niepowiązanych podczas uaktualniania.
* Dolar całkowity koszt.  Obejmuje to dodatkowa Redundancja i przyrostowe kosztów składników tymczasowy używany przez proces uaktualniania. 

## <a name="upgrading-applications-that-rely-on-database-backups-for-disaster-recovery"></a>Uaktualnianie aplikacji, które opierają się na kopie zapasowe bazy danych dla odzyskiwania po awarii
Jeśli aplikacji opiera się na automatyczne bazy danych kopii zapasowych i używa geograficzne do odzyskiwania po awarii, zazwyczaj jest wdrażany jeden region platformy Azure. W takim przypadku proces uaktualnienia obejmuje utworzenie kopii zapasowej wdrożenia wszystkich składników aplikacji objętego uaktualnienia. Aby zminimalizować zakłócenia użytkownika końcowego Menedżera ruchu Azure (WATM) będzie korzystać z profilem trybu failover.  Na poniższym diagramie przedstawiono środowisku operacyjnym przed procesu uaktualniania. Punkt końcowy <i>contoso 1.azurewebsites.net</i> reprezentuje gnieździe produkcyjnym aplikacji, która musi zostać uaktualniony. Aby włączyć możliwość wycofać uaktualnienie, ma potrzeby tworzenia miejsca etap z pełni zsynchronizowanej kopii aplikacji. Poniższe kroki są wymagane w celu przygotowania aplikacji do uaktualnienia:

1. Utwórz miejsce etapu dla uaktualnienia. Aby tworzenia pomocniczej bazy danych (1), a następnie wdrożona identyczne witryny sieci web, w tym samym regionie Azure. Monitorowanie lokacji dodatkowej, aby zobaczyć, jeśli proces rozmieszczania kończy się.
2. Utwórz profil trybu failover w WATM z <i>contoso 1.azurewebsites.net</i> jako punkt końcowy w trybie online i <i>contoso 2.azurewebsites.net</i> w trybie offline. 

> [!NOTE]
> Należy pamiętać, procedura przygotowująca nie będą miały wpływ aplikacji w miejscu produkcyjnym oraz mogą działać w trybie pełnego dostępu.
>  

![Konfiguracja replikacji Przejdź bazy danych SQL. Odzyskiwanie awaryjne chmury.](media/sql-database-manage-application-rolling-upgrade/Option1-1.png)

Po wykonaniu czynności przygotowujące aplikacja jest gotowa do rzeczywistego uaktualnienia. Na poniższym diagramie przedstawiono etapy procesu uaktualniania. 

1. Ustaw podstawowej bazy danych w tryb tylko do odczytu (3) do miejsca produkcji. Gwarantuje to, że produkcji wystąpienie aplikacji (wersja 1) pozostanie tylko do odczytu podczas uaktualniania, co uniemożliwia rozbieżności danych między V1 i V2 wystąpień bazy danych.  
2. Odłącz pomocniczej bazy danych przy użyciu trybu planowane zakończenie (4). Utworzy on pełni zsynchronizowanej niezależnych kopii podstawowej bazy danych. Ta baza danych zostanie uaktualniona.
3. Włącz podstawowej bazy danych w trybie odczytu i zapisu, a następnie uruchom skrypt uaktualnienia w miejscu etap [5].     

![Konfiguracja — replikacja geograficzna bazy danych SQL. Odzyskiwanie awaryjne chmury.](media/sql-database-manage-application-rolling-upgrade/Option1-2.png)

Jeśli uaktualnienie zakończone pomyślnie możesz teraz przystąpić do przełącznika użytkowników końcowych do przemieszczanego kopii aplikacji. Teraz będzie gotowa do miejsca produkcji aplikacji.  Ten proces obejmuje kilka kroków więcej, jak pokazano na poniższym diagramie.

1. W profilu WATM, aby zmienić online punktu końcowego <i>contoso 2.azurewebsites.net</i>, który wskazuje wersji V2 witryny sieci web (6). Staje się teraz gniazda produkcyjnego z aplikacją V2 i ruchu przez użytkownika końcowego jest kierowany do niego.  
2. Jeśli nie są już potrzebne składniki aplikacji V1, możesz bezpiecznie usunąć je (7).   

![Konfiguracja — replikacja geograficzna bazy danych SQL. Odzyskiwanie awaryjne chmury.](media/sql-database-manage-application-rolling-upgrade/Option1-3.png)

Jeśli proces uaktualniania zakończy się niepowodzeniem, na przykład z powodu błędu skryptu uaktualniania miejsca etapie należy traktować jako naruszenia zabezpieczeń. Aby wycofać aplikację do stanu sprzed uaktualnienia po prostu przywrócić aplikacji w miejscu produkcyjnym pełny dostęp. Czynności procedury są wyświetlane na diagramie dalej.    

1. Ustawić kopii bazy danych w trybie odczytu i zapisu (8). Spowoduje to przywrócenie pełne V1 funkcjonalnie w gnieździe produkcyjnym.
2. Wykonaj analiza głównych przyczyn i usunąć składniki ze złamanymi zabezpieczeniami w gnieździe etap (9). 

W tym momencie aplikacja jest w pełni funkcjonalny i można powtórzyć kroki uaktualniania.

> [!NOTE]
> Wycofywanie nie wymaga zmian w profilu WATM on już wskazuje <i>contoso 1.azurewebsites.net</i> jako aktywny punkt końcowy.
> 
> 

![Konfiguracja — replikacja geograficzna bazy danych SQL. Odzyskiwanie awaryjne chmury.](media/sql-database-manage-application-rolling-upgrade/Option1-4.png)

Klucz **korzyści** tej opcji jest uaktualnienie aplikacji w pojedynczym regionie przy użyciu zestawu prostych krokach. Koszt dolara uaktualnienia jest stosunkowo niska. Głównym **zależnościami** jest, że jeśli wystąpi błąd krytyczny podczas uaktualniania odzyskiwania do stanu sprzed uaktualnienia będzie obejmować ponownego wdrażania aplikacji w innym regionie i przywracanie bazy danych z kopii zapasowej za pomocą przywracaniem geograficznym. Ten proces spowoduje znaczne przestoju.   

## <a name="upgrading-applications-that-rely-on-database-geo-replication-for-disaster-recovery"></a>Uaktualnianie aplikacji, które zależą od bazy danych — replikacja geograficzna odzyskiwania po awarii
Jeśli aplikacja korzysta z georeplikacji ciągłość prowadzenia działalności biznesowej, jest wdrażana na co najmniej dwóch różnych regionach z aktywnym wdrożeniu w regionie podstawowym i rezerwy wdrożenia w regionie kopii zapasowej. Oprócz czynników, o których wspomniano wcześniej proces uaktualniania należy zagwarantować, że:

* Aplikacja pozostaje chroniona przed poważnej awarii przez cały czas w trakcie procesu uaktualniania
* Uaktualnienia równoległe ze składnikami programu active geograficznie nadmiarowego składniki aplikacji

Na osiągnięcie tych celów będzie korzystać przy użyciu profilu trybu failover z jedna aktualizacja aktywna i trzy punkty końcowe kopii zapasowej Azure ruchu Manager (WATM).  Na poniższym diagramie przedstawiono środowisku operacyjnym przed procesu uaktualniania. Witryny sieci web <i>contoso 1.azurewebsites.net</i> i <i>contoso dr.azurewebsites.net</i> reprezentują gnieździe produkcyjnym aplikacji z nadmiarowością pełnej geograficznych. Aby włączyć możliwość wycofać uaktualnienie, ma potrzeby tworzenia miejsca etap z pełni zsynchronizowanej kopii aplikacji. Ponieważ należy się upewnić, aplikacja szybko można odzyskać w przypadku, gdy wystąpi błąd krytyczny podczas procesu uaktualniania, gniazdo etapie należy geograficznie nadmiarowego również. Poniższe kroki są wymagane w celu przygotowania aplikacji do uaktualnienia:

1. Utwórz miejsce etapu dla uaktualnienia. Aby tworzenia pomocniczej bazy danych (1), a następnie wdrożona identyczne kopię witryny sieci web, w tym samym regionie Azure. Monitorowanie lokacji dodatkowej, aby zobaczyć, jeśli proces rozmieszczania kończy się.
2. Utwórz geograficznie nadmiarowego pomocniczej bazy danych w miejscu etap poprzez replikację geograficzną dodatkowej bazy danych do obszaru tworzenia kopii zapasowej (jest to nazywane "łańcuchowej — replikacja geograficzna"). Monitorowanie kopii zapasowej dodatkowej czy Proces rozmieszczania jest wypełniony (3).
3. Utwórz kopię rezerwy witryny sieci web w regionie kopii zapasowej i połączyć ją pomocniczej geograficznie nadmiarowego (4).  
4. Dodaj dodatkowe punkty końcowe <i>contoso 2.azurewebsites.net</i> i <i>contoso 3.azurewebsites.net</i> do profilu trybu failover w WATM jako punktów końcowych w trybie offline (5). 

> [!NOTE]
> Należy pamiętać, procedura przygotowująca nie będą miały wpływ aplikacji w miejscu produkcyjnym oraz mogą działać w trybie pełnego dostępu.
> 
> 

![Konfiguracja — replikacja geograficzna bazy danych SQL. Odzyskiwanie awaryjne chmury.](media/sql-database-manage-application-rolling-upgrade/Option2-1.png)

Po wykonaniu czynności przygotowujące miejsce etap jest gotowy do uaktualnienia. Na poniższym diagramie przedstawiono kroki uaktualniania.

1. Ustaw podstawowej bazy danych w gnieździe produkcyjnym do trybu tylko do odczytu (6). Gwarantuje to, że produkcji wystąpienie aplikacji (wersja 1) pozostanie tylko do odczytu podczas uaktualniania, co uniemożliwia rozbieżności danych między V1 i V2 wystąpień bazy danych.  
2. Odłącz dodatkowej bazy danych w tym samym regionie, w trybie planowane zakończenie (7). Utworzy on pełni zsynchronizowanej niezależnych kopii podstawowej bazy danych, którego podstawowy automatycznie stanie się po zakończeniu. Ta baza danych zostanie uaktualniona.
3. Włącz podstawowej bazy danych w miejscu etap w trybie odczytu i zapisu, a następnie uruchom skrypt uaktualnienia (8).    

![Konfiguracja — replikacja geograficzna bazy danych SQL. Odzyskiwanie awaryjne chmury.](media/sql-database-manage-application-rolling-upgrade/Option2-2.png)

Jeśli uaktualnienie zostało ukończone pomyślnie możesz teraz przystąpić do przełącznika użytkowników końcowych do wersji V2 aplikacji. Na poniższym diagramie przedstawiono etapy.

1. Przełączyć się aktywny punkt końcowy w profilu WATM <i>contoso 2.azurewebsites.net</i>, które teraz wskazuje wersji V2 witryny sieci web (9). Staje się teraz gnieździe produkcyjnym z aplikacją V2 i ruchu przez użytkownika końcowego jest kierowany do niego. 
2. Jeśli nie są już potrzebne aplikacji V1, możesz bezpiecznie usunąć go (10 i 11).  

![Konfiguracja — replikacja geograficzna bazy danych SQL. Odzyskiwanie awaryjne chmury.](media/sql-database-manage-application-rolling-upgrade/Option2-3.png)

Jeśli proces uaktualniania zakończy się niepowodzeniem, na przykład z powodu błędu skryptu uaktualniania miejsca etapie należy traktować jako naruszenia zabezpieczeń. Aby wycofać aplikację do stanu sprzed uaktualnienia można po prostu przywrócić w gnieździe produkcyjnym z pełnym dostępem przy użyciu aplikacji. Czynności procedury są wyświetlane na diagramie dalej.    

1. Ustaw kopii podstawowej bazy danych w trybie odczytu i zapisu (12) miejsca produkcji. Spowoduje to przywrócenie pełne V1 funkcjonalnie w gnieździe produkcyjnym.
2. Wykonaj analiza głównych przyczyn i usunąć składniki ze złamanymi zabezpieczeniami w gnieździe etap (13 i 14). 

W tym momencie aplikacja jest w pełni funkcjonalny i można powtórzyć kroki uaktualniania.

> [!NOTE]
> Wycofywanie nie wymaga zmian w profilu WATM on już wskazuje <i>contoso 1.azurewebsites.net</i> jako aktywny punkt końcowy.
> 
> 

![Konfiguracja — replikacja geograficzna bazy danych SQL. Odzyskiwanie awaryjne chmury.](media/sql-database-manage-application-rolling-upgrade/Option2-4.png)

Klucz **korzyści** tej opcji jest można uaktualnić zarówno aplikacji, jak i jego geograficznie nadmiarowego kopiowania równolegle w bez naruszania ciągłość prowadzenia działalności biznesowej podczas uaktualniania. Głównym **zależnościami** wymaga nadmiarowości dwa razy każdego składnika aplikacji i w związku z tym generuje wyższy koszt dolara ($). Obejmuje ona również bardziej skomplikowanych przepływów pracy. 

## <a name="summary"></a>Podsumowanie
Te dwie metody uaktualniania opisaną w artykule różnią się złożoność i dolara kosztów, ale ich obu skupić się na skrócenie czasu, gdy użytkownik końcowy jest ograniczona do operacji tylko do odczytu. Ta godzina bezpośrednio jest zdefiniowana przez czas trwania uaktualniania skryptów. Nie są zależne od rozmiaru bazy danych, wybranej warstwy usług, konfiguracji witryny sieci web i innych czynników, które nie można łatwo zarządzać. Jest to spowodowane wszystkie kroki przygotowania jest całkowicie niezależna od kroki uaktualniania i mogą być przeprowadzane bez wpływu na aplikacji produkcyjnej. Wydajność skryptów uaktualniania jest kluczowym czynnikiem, który określa środowisko użytkownika końcowego podczas uaktualniania. Dlatego najlepiej mogła ją udoskonalać jest koncentrujące się wysiłków na tworzenie jak najbardziej efektywne skrypt uaktualnienia skryptu.  

## <a name="next-steps"></a>Kolejne kroki
* Omówienie ciągłości działalności biznesowej i scenariuszy, zobacz [omówienie ciągłości działalności biznesowej](sql-database-business-continuity.md).
* Aby dowiedzieć się więcej na temat usługi Azure SQL bazy danych automatycznego tworzenia kopii zapasowych, zobacz [bazy danych SQL automatycznego tworzenia kopii zapasowych](sql-database-automated-backups.md).
* Aby dowiedzieć się więcej o używaniu kopie zapasowe automatycznego odzyskiwania, zobacz [przywrócić bazę danych z automatycznych kopii zapasowych](sql-database-recovery-using-backups.md).
* Informacje na temat opcji odzyskiwania szybsze, zobacz [aktywna replikacja geograficzna](sql-database-geo-replication-overview.md).


