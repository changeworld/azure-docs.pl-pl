---
title: Usługa SQL Database (PaaS) a program SQL Server w chmurze na maszynach wirtualnych (IaaS) | Microsoft Docs
description: 'Dowiedz się, która opcja programu SQL Server w chmurze jest odpowiednia do używanej aplikacji: usługa Azure SQL Database (PaaS) czy program SQL Server w chmurze na maszynach wirtualnych platformy Azure.'
services: sql-database, virtual-machines
keywords: chmura usługi SQL Server, SQL Server w chmurze, baza danych PaaS, chmura SQL Server, DBaaS
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: article
ms.date: 04/09/2018
ms.author: carlrab
ms.openlocfilehash: c42bdaf932c29d9e4505266099960dafb48be937
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
# <a name="choose-a-cloud-sql-server-option-azure-sql-paas-database-or-sql-server-on-azure-vms-iaas"></a>Wybieranie opcji programu SQL Server w chmurze: usługa Azure SQL Database (PaaS) lub program SQL Server na maszynach wirtualnych Azure (IaaS)
Platforma Azure ma dwie opcje do obsługi obciążeń programu SQL Server na platformie Microsoft Azure:

* [Azure SQL Database](https://azure.microsoft.com/services/sql-database/): baza danych SQL natywna względem chmury, zwana również bazą danych typu platforma jako usługa (PaaS) lub bazą danych jako usługa (DBaaS), zoptymalizowana pod kątem tworzenia aplikacji typu oprogramowanie jako usługa (SaaS). Zapewnia zgodność z większością funkcji programu SQL Server. Aby uzyskać więcej informacji o usłudze PaaS, zobacz [Co to jest PaaS](https://azure.microsoft.com/overview/what-is-paas/).
* [Program SQL Server w usłudze Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/sql-server/): program SQL Server zainstalowany i hostowany w chmurze na maszynach wirtualnych systemu Windows Server (VM) na platformie Azure, znany również jako infrastruktura jako usługa (IaaS).
  Program SQL Server na maszynach wirtualnych platformy Azure jest tego samego oprogramowania SQL Server, które można zainstalować na serwerze lokalnego — jedyna różnica polega na ten serwer jest teraz maszyn wirtualnych hostowanych w systemie Azure. SQL Server na platformie Azure jest zoptymalizowany do migrowania istniejących aplikacji SQL Server i wszystkich wersji i wydań programu SQL Server są dostępne. Umożliwia hostowanie wielu baz danych, w razie potrzeby obsługuje wykonywanie transakcji między bazami danych i zapewnia pełną kontrolę wystąpienia programu SQL Server i systemu operacyjnego Windows.

Dowiedz się, jak poszczególne opcje wpisują się w platformę danych firmy Microsoft i uzyskaj pomoc przy dopasowaniu odpowiedniej opcji do wymagań działalności biznesowej. Niezależnie od tego, czy priorytetem jest oszczędność, czy ograniczenie administracji do minimum, artykuł ten może pomóc zdecydować, które rozwiązanie pozwoli spełnić wymagania związane z działalnością biznesową, na których zależy Ci najbardziej.

## <a name="microsofts-data-platform"></a>Platforma danych firmy Microsoft
Podczas każdej dyskusji dotyczącej baz danych Azure w zestawieniu z lokalnymi bazami danych programu SQL Server należy przede wszystkim pamiętać o tym, że można używać ich wszystkich. Platforma danych firmy Microsoft korzysta z technologii programu SQL Server i udostępnia ją na fizycznych lokalnymi maszynami, środowiskach chmur prywatnych środowiskach innej firmy hostowaną chmurę prywatną i firmy Microsoft w chmurze publicznej. Program SQL Server w usłudze Azure Virtual Machines umożliwia spełnienie unikatowych i zróżnicowanych potrzeb związanych z prowadzeniem działalności biznesowej poprzez kombinację wdrożeń lokalnych i hostowanych w chmurze, z równoczesnym użyciem w tych środowiskach tego samego zestawu produktów serwerowych, narzędzi projektowych i zakresu wiedzy.

   ![Opcje programu SQL Server w chmurze: program SQL Server w usłudze IaaS lub baza danych SQL w usłudze SaaS w chmurze.](./media/sql-database-paas-vs-sql-server-iaas/SQLIAAS_SQL_Server_Cloud_Continuum.png)

Jak widać to na diagramie, każde rozwiązanie można scharakteryzować według posiadanego poziomu administracji względem infrastruktury (na osi X) oraz według stopnia opłacalności wynikającej z konsolidacji poziomu bazy danych i automatyzacji (na osi Y).

Podczas projektowania aplikacji są dostępne cztery podstawowe opcje obsługi części aplikacji programu SQL Server:

* program SQL Server na niezwirtualizowanych komputerach fizycznych,
* program SQL Server na lokalnych komputerach zwirtualizowanych (w chmurze prywatnej),
* program SQL Server na maszynie wirtualnej platformy Azure (w chmurze publicznej firmy Microsoft),
* usługa Azure SQL Database (w chmurze publicznej firmy Microsoft).

Poniższe sekcje zawierają informacje na temat programu SQL Server w chmurze publicznej firmy Microsoft: usługi Azure SQL Database i programu SQL Server na maszynach wirtualnych platformy Azure. Ponadto przeanalizujemy typowe czynniki motywujące związane z działalnością biznesową, aby ustalić, która opcja jest najlepsza dla aplikacji.

## <a name="a-closer-look-at-azure-sql-database-and-sql-server-on-azure-vms"></a>Bliższe spojrzenie na usługę Azure SQL Database i program SQL Server na maszynach wirtualnych platformy Azure
**Azure SQL Database** to relacyjna bazą danych dostarczana jako usługa (DBaaS), hostowana w chmurze Azure. Można ją zaliczyć do następujących kategorii branżowych: *oprogramowanie jako usługa (SaaS)* i *platforma jako usługa (PaaS)*. [Baza danych SQL](sql-database-technical-overview.md) działa na ustandaryzowanym sprzęcie i oprogramowaniu, które stanowi własność firmy Microsoft, jest przez nią hostowane i konserwowane. W przypadku usługi SQL Database można programować bezpośrednio w usłudze przy użyciu wbudowanych funkcji i funkcjonalności. Płatność za korzystanie z usługi SQL Database odbywa się zgodnie z rzeczywistym użyciem, a opcje pozwalają na skalowanie w górę lub w poziomie, dzięki czemu klienci mają do dyspozycji większe możliwości bez najmniejszych zakłóceń.

**Program SQL Server w usłudze Azure Virtual Machines** należy do branżowej kategorii *infrastruktura jako usługa (IaaS)* i umożliwia uruchamianie programu SQL Server na maszynie wirtualnej w chmurze. Podobnie jak SQL Database, również ta usługa działa na zgodnym ze standardami sprzęcie, który jest własnością firmy Microsoft, jest przez nią hostowany i konserwowany. Program SQL Server na maszynie wirtualnej umożliwia płatność zgodnie z rzeczywistym użyciem za licencję programu SQL Server dołączoną do obrazu programu SQL Server lub łatwe użycie istniejącej licencji. Zapewnia również możliwość łatwego skalowania w górę lub w dół oraz wstrzymywania/wznawiania działania maszyny wirtualnej zgodnie z zapotrzebowaniem.

Ogólnie rzecz biorąc, te dwie opcje są zoptymalizowane do różnych celów:

* **Usługa Azure SQL Database** jest zoptymalizowana pod kątem ograniczenia do minimum całkowitych kosztów związanych z aprowizacją wielu baz danych i zarządzaniem nimi. Obniża bieżące koszty administracyjne, ponieważ nie jest konieczne zarządzanie maszynami wirtualnymi, systemem operacyjnym ani oprogramowaniem bazy danych. Nie wymaga zarządzania uaktualnieniami, wysoką dostępnością ani [kopiami zapasowymi](sql-database-automated-backups.md). Ogólnie rzecz biorąc, usługa Azure SQL Database może znacząco zwiększyć liczbę baz danych zarządzanych przez pojedynczy zasób informatyczny lub projektowy.
* **Program SQL Server w usłudze Azure Virtual Machines** jest zoptymalizowany pod kątem migrowania istniejących aplikacji do platformy Azure lub rozszerzania istniejących aplikacji lokalnych do chmury we wdrożeniach hybrydowych. Ponadto przy użyciu programu SQL Server na maszynie wirtualnej można tworzyć i testować tradycyjne aplikacje programu SQL Server. W przypadku programu SQL Server na maszynach wirtualnych platformy Azure masz pełne uprawnienia administracyjne do dedykowanego wystąpienia programu SQL Server oraz maszyny wirtualnej w chmurze. To doskonałe rozwiązanie, gdy firma ma już dostępne zasoby informatyczne do obsługi maszyn wirtualnych. Te funkcje umożliwiają stworzenie doskonale dopasowanego systemu, który spełni określone wymagania aplikacji związane z wydajnością i dostępnością.

Poniższa tabela zawiera podsumowanie głównych cech usługi SQL Database i programu SQL Server na maszynach wirtualnych platformy Azure:

| **Najlepsze dla:** | **Azure SQL Database** | **SQL Server na maszynie wirtualnej platformy Azure** |
| --- | --- | --- |
|  |Nowe aplikacje zaprojektowane dla chmury, które mają ograniczenia czasowe w zakresie projektowania i marketingu. |Istniejące aplikacje, które wymagają szybkiej migracji do chmury przy minimalnych zmianach. Scenariusze szybkiego tworzenia i testowania, gdy nie chcesz kupować lokalnego nieprodukcyjnego sprzętu dla programu SQL Server. |
|  | Zespoły, które mają firmy Microsoft, aby zarządzać uaktualnienia bazy danych, wysokiej dostępności i odzyskiwania po awarii. |Zespoły, które mogą konfigurować wysoką dostępność, odzyskiwanie po awarii i poprawki dla programu SQL Server oraz zarządzać nimi. Niektóre dostępne funkcje automatyczne znacznie to upraszczają. | |
|  | Zespoły, które nie chcą zarządzać podstawowymi ustawieniami systemu operacyjnego i konfiguracji. |Potrzebne jest dostosowane środowisko z pełnymi prawami administracyjnymi. | |
|  | Baz danych do 4 TB lub większych baz danych, które mogą być [poziomo czy pionowo podzielona na partycje](sql-database-elastic-scale-introduction.md#horizontal-and-vertical-scaling) przy użyciu wzorca skalowalnego w poziomie. |Wystąpienia programu SQL Server z maksymalnie 64 TB przestrzeni dyskowej. Wystąpienie może obsługiwać dowolną liczbę baz danych zależnie od potrzeb. | |
|  | | |
| **Zasoby:** |Nie chcesz używać zasobów IT do konfiguracji podstawowej infrastruktury i zarządzania nią, ale chcesz skupić się na warstwie aplikacji. |Masz niektóre zasoby informatyczne do konfiguracji i zarządzania. Niektóre dostępne funkcje automatyczne znacznie to upraszczają. |
| **Całkowity koszt posiadania:** |Eliminuje koszty sprzętu i ogranicza koszty administracyjne. |Eliminuje koszty sprzętu. |
| **Ciągłość działalności biznesowej:** |Oprócz możliwości infrastruktury z wbudowanych usterek uszkodzenia bazy danych SQL Azure udostępnia funkcje, takie jak [automatycznego tworzenia kopii zapasowych](sql-database-automated-backups.md), [w momencie przywracania](sql-database-recovery-using-backups.md#point-in-time-restore), [geograficzne](sql-database-recovery-using-backups.md#geo-restore), i [aktywna replikacja geograficzna](sql-database-geo-replication-overview.md) Aby zwiększyć ciągłość prowadzenia działalności biznesowej. Więcej informacji znajduje się w temacie [Omówienie ciągłości działalności biznesowej usługi SQL Database](sql-database-business-continuity.md). |Program SQL Server na maszynach wirtualnych platformy Azure umożliwia skonfigurowanie rozwiązania wysokiej dostępności i odzyskiwania po awarii w zależności od określonych potrzeb bazy danych. Można mieć zatem system w znacznym stopniu zoptymalizowany dla aplikacji. Jeśli wystąpi taka potrzeba, tryb failover można przetestować i uruchomić samodzielnie. Więcej informacji znajduje się w temacie [Wysoka dostępność i odzyskiwanie po awarii dla programu SQL Server w usłudze Azure Virtual Machines](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md). |
| **Chmura hybrydowa:** |Aplikacja lokalna może uzyskiwać dostęp do danych w usłudze Azure SQL Database. |Program SQL Server na maszynach wirtualnych platformy Azure może obejmować aplikacje, które działają częściowo w chmurze i częściowo lokalnie. Można na przykład rozszerzyć sieć lokalną i domenę usługi Active Directory do chmury za pośrednictwem usługi [Azure Virtual Network](../virtual-network/virtual-networks-overview.md). Ponadto można przechowywać lokalne pliki danych w usłudze Azure Storage przy użyciu [plików danych programu SQL Server na platformie Azure](http://msdn.microsoft.com/library/dn385720.aspx). Więcej informacji znajduje się w temacie [Wprowadzenie do chmury hybrydowej programu SQL Server 2014](http://msdn.microsoft.com/library/dn606154.aspx). |
|  | Obsługuje [replikację transakcyjną programu SQL Server](https://msdn.microsoft.com/library/mt589530.aspx) jako subskrybent replikowanych danych. |W pełni obsługuje [replikację transakcyjną programu SQL Server](https://msdn.microsoft.com/library/mt589530.aspx), [grupy dostępności AlwaysOn](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md), usługi integracji i wysyłanie dzienników do replikacji danych. Ponadto w pełni obsługiwane są tradycyjne kopie zapasowe programu SQL Server. | |
|  | | |

## <a name="business-motivations-for-choosing-azure-sql-database-or-sql-server-on-azure-vms"></a>Względy biznesowe przemawiające za wyborem usługi Azure SQL Database lub programu SQL Server na maszynach wirtualnych Azure
### <a name="cost"></a>Koszty
Ograniczone fundusze są często podstawowym czynnikiem decydującym o wyborze sposobu hostowania baz danych — niezależnie od tego, czy Twoja firma to start-up o ograniczonych środkach finansowych, czy należysz do zespołu firmowego, który musi zmieścić się w zaplanowanym budżecie. W tej sekcji poznasz informacje dotyczące rozliczeń i licencjonowania platformy Azure w odniesieniu do dwóch opcji obsługi relacyjnych baz danych: usługi SQL Database i programu SQL Server na maszynach wirtualnych platformy Azure. Poznasz również sposób obliczania całkowitego kosztu aplikacji.

#### <a name="billing-and-licensing-basics"></a>Podstawowe informacje dotyczące rozliczeń i licencjonowania
**Rozwiązanie SQL Database** jest sprzedawane klientom jako usługa, a nie z licencją.  [Program SQL Server w usłudze Azure Virtual Machines](../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md) jest sprzedawany z dołączoną licencją płatną według stawki minutowej. Możesz również użyć istniejącej licencji, jeśli ją masz.  

Obecnie usługa **SQL Database** jest dostępna w kilku warstwach, z których wszystkie są rozliczane godzinowo według stałej stawki ustalanej na podstawie warstwy usługi i wybranego poziomu wydajności. Rozliczany jest również internetowy ruch wychodzący po zwykłych [stawkach transferu danych](https://azure.microsoft.com/pricing/details/data-transfers/). Warstwy usług Basic, Standard, Premium, ogólnego przeznaczenia i krytycznym mają na celu zapewnienie przewidywalnej wydajności wiele poziomów wydajności, aby dopasować szczytowe wymagania Twojej aplikacji. Warstwy usług i poziomy wydajności można zmieniać, aby odpowiadały one zróżnicowanym potrzebom aplikacji związanym z przepływnością. Najnowsze informacje dotyczące bieżących obsługiwanych warstw usług można znaleźć w temacie [Warstwy usług w usłudze Azure SQL Database](sql-database-service-tiers.md). Można również utworzyć [pule elastyczne](sql-database-elastic-pool.md) do udostępniania zasobów wydajności między wystąpieniami bazy danych.

> [!IMPORTANT]
> Jeśli baza danych zawiera dużą liczbę transakcji i musi obsługiwać wielu użytkowników równocześnie, zalecamy warstwy usług Premium lub krytycznym. Aby zminimalizować opóźnienie pomiędzy aplikacją i bazy danych SQL, zlokalizuj aplikacji w tym samym regionie co w bazie danych, a następnie sprawdź wydajność — zwiększenie poziomu wydajności i warstwy usług, zgodnie z potrzebami.

W przypadku usługi **SQL Database** oprogramowanie bazy danych jest automatycznie konfigurowane, poprawiane i uaktualniane przez firmę Microsoft, co obniża koszty administracyjne. Ponadto [wbudowana funkcja tworzenia kopii zapasowych](sql-database-automated-backups.md) pozwala osiągnąć znaczne oszczędności kosztów, zwłaszcza w przypadku dużej liczby baz danych.

Dzięki **programowi SQL Server na maszynach wirtualnych platformy Azure** można używać udostępnianego na platformie obrazu programu SQL Server (który obejmuje licencję) lub przenieść swoją licencję programu SQL Server. Wszystkie obsługiwane wersje programu SQL Server (2008 R2, 2012, 2014, 2016, 2017) i dostępne są wersje (Developer Express, sieci Web, Standard, Enterprise). Ponadto są dostępne wersje bring-your-own-license (BYOL) obrazów. W przypadku użycia obrazów dostarczanych przez platformę Azure koszty operacyjne zależą od rozmiaru maszyny wirtualnej, a także od wybranej wersji programu SQL Server. Niezależnie od rozmiaru maszyny wirtualnej lub wersji programu SQL Server co minutę naliczana jest opłata licencyjna za program SQL Server i system Windows Server, wraz z kosztami korzystania z usługi Azure Storage i dysków maszyny wirtualnej. Opcja rozliczania co minutę pozwala używać programu SQL Server tak długo, jak jest to konieczne bez wykupywania dodatkowych licencji programu SQL Server. W przypadku przeniesienia licencji programu SQL Server na platformę Azure naliczane są tylko opłaty za korzystanie z systemu Windows Server i magazynu. Więcej informacji na temat przenoszenia własnej licencji można znaleźć w temacie [Przenośność licencji za pośrednictwem programu Software Assurance w systemie Azure](https://azure.microsoft.com/pricing/license-mobility/).

#### <a name="calculating-the-total-application-cost"></a>Obliczanie całkowitego kosztu aplikacji
Po rozpoczęciu pracy z platformą w chmurze koszt obsługi aplikacji obejmuje koszty programowania i administracji wraz z kosztami usługi publicznej platformy w chmurze.

Oto szczegółowe obliczenie kosztów obsługi aplikacji uruchomionej w usłudze SQL Database i programie SQL Server na maszynach wirtualnych platformy Azure:

**W przypadku korzystania z usługi Azure SQL Database:**

*Całkowity koszt aplikacji = znacznie ograniczone koszty administracyjne + koszty rozwoju oprogramowania + koszty usługi SQL Database*

**W przypadku korzystania z programu SQL Server na maszynach wirtualnych platformy Azure:**

*Całkowity koszt aplikacji = ograniczone do minimum koszty tworzenia oprogramowania + koszty administracyjne + koszty licencjonowania programu SQL Server i systemu Windows Server + koszty usługi Azure Storage*

Więcej informacji na temat cen zawierają następujące zasoby:

* [Cennik usługi SQL Database](https://azure.microsoft.com/pricing/details/sql-database/)
* [Maszyny wirtualne — cennik](https://azure.microsoft.com/pricing/details/virtual-machines/) ([SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#sql) i [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#windows))
* [Kalkulator cen platformy Azure](https://azure.microsoft.com/pricing/calculator/)

> [!NOTE]
> W programie SQL Server istnieje niewielki podzbiór funkcji, które nie są używane w usłudze SQL Database lub nie są w niej dostępne. Więcej informacji znajduje się w tematach [SQL Database Features](sql-database-features.md) (Funkcje usługi SQL Database) i [SQL Database Transact-SQL information](sql-database-transact-sql-information.md) (Informacje o programie Transact-SQL w usłudze SQL Database). W przypadku przenoszenia istniejącego rozwiązania programu SQL Server do chmury zobacz temat [Migrating a SQL Server database to Azure SQL Database](sql-database-cloud-migrate.md) (Migrowanie bazy danych programu SQL Server do usługi Azure SQL Database). Podczas migracji istniejącej lokalnej aplikacji SQL Server do usługi SQL Database należy wziąć pod uwagę aktualizację aplikacji, co pozwoli wykorzystać możliwości, które oferują usługi w chmurze. Można na przykład rozważyć użycie usługi [Azure Web App Service](https://azure.microsoft.com/services/app-service/web/) lub [Azure Cloud Services](https://azure.microsoft.com/services/cloud-services/) do obsługi warstwy aplikacji i zwiększenia oszczędności.
> 
> 

### <a name="administration"></a>Administracja
Dla wielu firm decyzja o przeniesieniu usług do usługi w chmurze wiąże się zarówno z ograniczeniem złożoności w zakresie administracji, jak i obniżeniem kosztów. Firma Microsoft administruje sprzętem podstawowym usługi **SQL Database**. Firma Microsoft automatycznie replikuje wszystkie dane w celu zapewnienia dużej dostępności, konfiguruje i uaktualnia oprogramowanie bazy danych, zarządza równoważeniem obciążenia, a w przypadku awarii serwera w nieodczuwalny dla klienta sposób obsługuje tryb failover. Możesz samodzielnie administrować swoją bazą danych, ale nie musisz zarządzać aparatem bazy danych, systemem operacyjnym serwera ani sprzętem.  Przykładowe elementy, którymi można dalej zarządzać to bazy danych, identyfikatory logowania, dostrajanie indeksów i zapytań oraz inspekcja i zabezpieczenia.

**Program SQL Server na maszynach wirtualnych platformy Azure** daje pełną kontrolę nad konfiguracją systemu operacyjnego i wystąpienia programu SQL Server. W przypadku korzystania z maszyny wirtualnej decydujesz o tym, kiedy należy zaktualizować/uaktualnić system operacyjny i oprogramowanie bazy danych i kiedy zainstalować dodatkowe oprogramowanie, np. oprogramowanie antywirusowe. Dostępnych jest kilka funkcji automatycznych, które znacznie upraszczają instalowanie poprawek, tworzenie kopii zapasowej i zapewnianie wysokiej dostępności. Ponadto możesz kontrolować rozmiar maszyny wirtualnej, liczbę dysków oraz ich konfiguracje magazynu. Platforma Azure umożliwia zmianę rozmiaru maszyny wirtualnej zgodnie z zapotrzebowaniem. Więcej informacji można znaleźć w temacie [Virtual Machine and Cloud Service Sizes for Azure](../virtual-machines/windows/sizes.md) (Rozmiary maszyn wirtualnych i usług w chmurze na platformie Azure). 

### <a name="service-level-agreement-sla"></a>Umowa dotycząca poziomu usług (SLA)
Dla wielu działów IT wypełnienie zobowiązań wynikających z umowy dotyczącej poziomu usług (SLA) ma najwyższy priorytet. W tej sekcji opisano, jakie warunki umowy SLA stosuje się do poszczególnych opcji obsługi bazy danych.

Aby uzyskać **bazy danych SQL** Basic, Standard, Premium, ogólnego przeznaczenia i krytycznym warstwy usług, firma Microsoft zapewnia dostępność 99,99%. Najnowsze informacje można znaleźć w artykule [SQL Database — umowa SLA](https://azure.microsoft.com/support/legal/sla/sql-database/). Najnowsze informacje dotyczące warstw usługi SQL Database oraz obsługiwanych planów ciągłości działalności biznesowej znajdują się w temacie [Warstwy usług](sql-database-service-tiers.md).

W przypadku **programu SQL Server uruchomionego na maszynach wirtualnych platformy Azure** firma Microsoft zapewnia dostępność na poziomie 99,95%, która obejmuje tylko maszyny wirtualne. Umowa SLA nie obejmuje procesów (np. programu SQL Server) uruchomionych na maszynie wirtualnej i wymaga obsługi przynajmniej dwóch wystąpień maszyny wirtualnej w zbiorze dostępności. Najnowsze informacje znajdują się w artykule [Maszyny wirtualne — umowa SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Aby uzyskać wysoką dostępność bazy danych w maszynach wirtualnych, należy skonfigurować jedną z obsługiwanych opcji wysokiej dostępności w programie SQL Server, np. [Zawsze włączone grupy dostępności](http://blogs.technet.com/b/dataplatforminsider/archive/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery.aspx). Użycie obsługiwanej opcji wysokiej dostępności nie zapewnia dodatkowej umowy SLA, ale umożliwia osiągnięcie dostępności bazy danych na poziomie > 99,99%.

### <a name="market"></a>Czas wprowadzenia na rynek
Usługa **SQL Database** to odpowiednie rozwiązanie do obsługi aplikacji przeznaczonych do chmury, gdy wydajność deweloperów i krótki czas wprowadzania na rynek mają decydujące znaczenie. Dzięki funkcjonalności przypominającej model DBA jest doskonała dla architektów i deweloperów chmury, ponieważ zmniejsza potrzebę zarządzania bazowym systemem operacyjnym i bazą danych. Na przykład można użyć [interfejsu API REST](http://msdn.microsoft.com/library/azure/dn505719.aspx) i [poleceń cmdlet programu PowerShell](http://msdn.microsoft.com/library/mt740629.aspx) do automatyzacji operacji administracyjnych dla tysięcy baz danych oraz zarządzania nimi. Funkcje, takie jak [pule elastyczne](sql-database-elastic-pool.md), pozwalają skupić się na warstwie aplikacji i szybciej wprowadzić rozwiązanie na rynek.

**Program SQL Server uruchomiony na maszynach wirtualnych platformy Azure** jest doskonałym rozwiązaniem, jeśli istniejące lub nowe aplikacje wymagają dużych baz danych, powiązanych baz danych lub dostępu do wszystkich funkcji programu SQL Server lub systemu Windows. Jest również odpowiedni, jeśli chcesz migrować istniejące lokalne aplikacje i bazy danych do platformy Azure w niezmienionej postaci. Ponieważ nie musisz wprowadzać zmian w prezentacji, aplikacji i warstwach danych, jeśli rezygnujesz z ponownego projektowania istniejącego rozwiązania, oszczędzasz czas i pieniądze. Zamiast tego możesz skoncentrować się na migracji wszystkich rozwiązań do platformy Azure i przeprowadzeniu optymalizacji wydajności, których może wymagać platforma Azure. Więcej informacje zawiera artykuł [Performance Best Practices for SQL Server on Azure Virtual Machines](../virtual-machines/windows/sql/virtual-machines-windows-sql-performance.md) (Najlepsze praktyki dotyczące wydajności dla programu SQL Server w usłudze Azure Virtual Machines).

## <a name="summary"></a>Podsumowanie
W tym artykule omówiono usługę SQL Database i program SQL Server w usłudze Azure Virtual Machines oraz względy biznesowe, które mogą mieć wpływ na wybór jednego z tych rozwiązań. Poniżej znajduje się podsumowanie sugestii, które należy wziąć pod uwagę:

Wybierz usługę **Azure SQL Database**, jeśli:

* Tworzysz nowe aplikacje oparte na chmurze, aby skorzystać z oszczędności kosztów i optymalizacji wydajności, które zapewniają usługi w chmurze. Takie rozwiązanie zapewnia korzyści w pełni zarządzanej usługi w chmurze, pomaga skrócić początkowy czas wprowadzenia na rynek i pozwala uzyskać długoterminową optymalizację kosztów.
* Chcesz, aby firma Microsoft wykonywała zwyczajowe operacje zarządzania na bazach danych i potrzebujesz umów SLA gwarantujących wyższą dostępność baz danych.

Wybierz rozwiązanie **SQL Server na maszynach wirtualnych Azure**, jeśli:

* Masz istniejące aplikacje lokalne, które chcesz przeprowadzić migrację lub rozszerzyć do chmury, lub jeśli chcesz tworzyć aplikacje przedsiębiorstwa większych niż 4 TB. To podejście pozwala korzystać z zalet 100% zgodności, dużej pojemności bazy danych, pełnej kontroli nad programem SQL Server i systemem Windows oraz bezpiecznego tunelowania do środowiska lokalnego. To podejście minimalizuje koszty tworzenia i modyfikowania istniejących aplikacji.
* Masz istniejące zasoby IT i możesz ostatecznie samodzielnie zarządzać poprawkami, kopiami zapasowymi i wysoką dostępnością bazy danych. Należy zauważyć, że niektóre funkcje automatyczne umożliwiają znaczne uproszczenie tych operacji. 

## <a name="next-steps"></a>Kolejne kroki
* Aby rozpocząć pracę z usługą SQL Database, zobacz [Your first Azure SQL Database](sql-database-get-started-portal.md) (Twoja pierwsza baza danych Azure SQL Database).
* Zobacz [Cennik usługi SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).
* Aby rozpocząć pracę z programem SQL Server na maszynach wirtualnych platformy Azure, zobacz temat [Aprowizowanie maszyny wirtualnej programu SQL Server w Portalu Azure](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision.md)

