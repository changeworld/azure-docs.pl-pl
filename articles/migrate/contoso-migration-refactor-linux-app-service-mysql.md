---
title: Refaktoryzuj Contoso Linux usługi app Service desk do usługi Azure App Service i Azure MySQL | Dokumentacja firmy Microsoft
description: Dowiedz się, jak Contoso refactors aplikacji systemu Linux w środowisku lokalnym przy użyciu funkcji migracji do usługi Azure App Service, korzystając z usługi GitHub dla warstwy internetowej i usługi Azure SQL Database.
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/04/2018
ms.author: raynew
ms.openlocfilehash: 585076b3fef56fe0e59828137674d7eb14528540
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/06/2018
ms.locfileid: "37872475"
---
# <a name="contoso-migration-refactor-a-contoso-linux-service-desk-app-to-the-azure-app-service-and-azure-mysql"></a>Migracja Contoso: Refaktoryzacja Contoso Linux usługi app Service desk do usługi Azure App Service i Azure MySQL

W tym artykule pokazano, jak Contoso refactors ich w środowisku lokalnym dwuwarstwowej Linux technicznej aplikacji service (osTicket) przy użyciu funkcji migracji jego Integracja z usługą GitHub usługi Azure App Service i Azure MySQL.

Ten dokument jest dziesiętnej serią artykułów, które pokazują, jak fikcyjnej firmy Contoso migruje swoje zasoby lokalne do chmury Microsoft Azure. Seria zawiera dodatkowe informacje i scenariusze, które ilustrują sposób konfigurowania infrastruktury migracji i uruchamiania różnych rodzajów migracji. Scenariusze zwiększanie się stopnia skomplikowania, a dodamy dodatkowe artykuły, wraz z upływem czasu.

**Artykuł** | **Szczegóły** | **Stan**
--- | --- | ---
[Artykuł 1: omówienie](contoso-migration-overview.md) | Zawiera omówienie strategii migracji firmy Contoso, serię artykułów i przykładowe aplikacje, używanych przez firmę Microsoft. | Dostępne
[Artykuł 2: Wdrażanie infrastruktury platformy Azure](contoso-migration-infrastructure.md) | W tym artykule opisano, jak Contoso przygotowuje jej w środowisku lokalnym i infrastruktury platformy Azure do migracji. Tej samej infrastruktury jest używana w przypadku wszystkich scenariuszy migracji Contoso. | Dostępne
[Artykuł 3: Ocena zasobów lokalnych](contoso-migration-assessment.md)  | Pokazuje, jak firmy Contoso jest wykonywany oceny lokalnej SmartHotel dwuwarstwowej aplikacji działających z oprogramowaniem VMware. Ich oceny maszyn wirtualnych aplikacji, za pomocą [usługi Azure Migrate](migrate-overview.md) usługi bazy danych oraz aplikacji programu SQL Server za pomocą [Asystenta migracji bazy danych usługi Azure](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Dostępne
[Artykuł 4: Rehost maszyn wirtualnych platformy Azure i wystąpienie zarządzane SQL](contoso-migration-rehost-vm-sql-managed-instance.md) | Pokazuje, jak Contoso migruje SmartHotel aplikacji na platformie Azure. Oni migrować do aplikacji sieci web maszyny Wirtualnej przy użyciu [usługi Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview)i przy użyciu bazy danych aplikacji [usługi Azure Database Migration](https://docs.microsoft.com/azure/dms/dms-overview) usługi, aby przeprowadzić migrację do wystąpienia zarządzanego SQL. | Dostępne
[Artykuł 5: Ponowne hostowanie dla maszyn wirtualnych platformy Azure](contoso-migration-rehost-vm.md) | Pokazuje, jak Contoso migrację SmartHotel ich do maszyn wirtualnych IaaS platformy Azure, przy użyciu usługi Site Recovery.
[Artykuł 6: Ponowne hostowanie dla maszyn wirtualnych platformy Azure i grup dostępności programu SQL Server](contoso-migration-rehost-vm-sql-ag.md) | Pokazuje, jak Contoso migruje SmartHotel aplikacji. Ich Usługa Site Recovery do migrowania aplikacji, maszyny wirtualne i usługi migracji bazy danych do migracji bazy danych aplikacji do grupy dostępności programu SQL Server. | Dostępne
[Artykuł 7: Ponowne hostowanie aplikacji systemu Linux na maszynach wirtualnych platformy Azure](contoso-migration-rehost-linux-vm.md) | Pokazuje, jak firmy Contoso jest migrowana ich aplikacji systemu Linux osTicket do maszyn wirtualnych IaaS platformy Azure przy użyciu usługi Azure Site Recovery.
[Artykuł 8: Ponowne hostowanie aplikacji systemu Linux na maszynach wirtualnych platformy Azure i serwer Azure MySQL](contoso-migration-rehost-linux-vm-mysql.md) | Pokazuje, jak Contoso migruje osTicket aplikacji systemu Linux. Używają do migracji do wystąpienia serwera MySQL w usłudze Azure Site Recovery na potrzeby migracji maszyny Wirtualnej i połączenia aplikacji MySQL Workbench. | Dostępne
Artykuł 9: Refaktoryzacja aplikacji do aplikacji sieci Web platformy Azure i usługi Azure SQL Database | Pokazuje, jak Contoso SmartHotel app jest migrowana do aplikacji internetowej platformy Azure opartych na kontenerach i bazy danych aplikacji jest migrowana do usługi Azure SQL Server. | Dostępne
Artykuł 10: Refaktoryzacja aplikacji systemu Linux w usłudze Azure App Service i Azure serwera MySQL | Pokazuje, jak firmy Contoso jest migrowana osTicket aplikacji systemu Linux do usługi Azure App Service za pomocą kontenera platformy Docker programu PHP 7.0. Baza kodów dla wdrożenia są migrowane do usługi GitHub. Baza danych aplikacji są migrowane do usługi Azure MySQL. | W tym artykule.


W tym artykule Contoso migruje dwuwarstwowej Linux Apache MySQL PHP (LAMP) aplikacji usługi, która działu (osTicket) na platformie Azure. Jeśli chcesz korzystać z tej aplikacji typu open source, możesz ją pobrać z [GitHub](https://github.com/osTicket/osTicket).



## <a name="business-drivers"></a>Czynniki biznesowe

Zespół kierowniczy IT ma ściśle współpracowała z ich partnerów biznesowych, aby zrozumieć, czego chcą osiągnąć:

- **Adres rozwój**: Contoso jest stałym wzbogacaniu i przeniesienie na nowe rynki. Muszą oni mieć agenci usługi dodatkowych klientów. 
- **Skala**: rozwiązanie powinny zostać skompilowane tak, aby Contoso można dodać więcej agentów usługi klienta jako skale biznesowych.
- **Zwiększyć odporność**: WE ostatnie problemy w systemie dotyczą tylko w przypadku użytkowników wewnętrznych. Przy użyciu ich nowym modelu biznesowym będzie mieć wpływ na użytkowników zewnętrznych, a potrzeby firmy Contoso app działanie przez cały czas.

## <a name="migration-goals"></a>Cele migracji

Zespół chmury firmy Contoso ma przypięte w dół do celów migracji, aby określić najlepszą metodę migracji:

- Aplikacja powinny być skalowane poza bieżącą pojemność w środowisku lokalnym i wydajności.  Firmy Contoso przechodzi aplikacji, aby skorzystać ze skalowania na żądanie platformy Azure.
- Contoso chce przenieść bazy kodu aplikacji do potok ciągłego dostarczania.  Ponieważ zmiany aplikacji zostaną wypchnięte do usługi GitHub, firma chce wdrożyć tych zmian bez zadania dla pracowników operacyjnych.
- Aplikacja musi być odporne na błędy z funkcjami dla rozwoju i trybu failover. Firma chce wdrożyć aplikację w dwóch różnych regionach platformy Azure i skonfiguruj go w celu automatycznego skalowania.
- Firma Contoso chce, aby zminimalizować zadania administratora bazy danych, po przeniesieniu aplikacji w chmurze.

## <a name="solution-design"></a>Projekt rozwiązania
Po przypięciu dół swoje cele i wymagania, Contoso projektuje i przejrzyj rozwiązanie wdrożenia i zidentyfikować proces migracji, w tym usług platformy Azure, które będą używały do migracji.


## <a name="current-architecture"></a>Bieżącej architektury

- Aplikacja jest rozmieszczone warstwowo na dwie maszyny wirtualne (OSTICKETWEB i OSTICKETMYSQL).
- Maszyny wirtualne znajdują się na hoście VMware ESXi **contosohost1.contoso.com** (w wersji 6.5).
- Środowisko VMware jest zarządzane przez program vCenter Server 6.5 (**vcenter.contoso.com**) uruchomionego na maszynie Wirtualnej.
- Firma Contoso ma lokalne centrum danych (contoso-datacenter), lokalnego kontrolera domeny (**contosodc1**).

![Bieżącej architektury](./media/contoso-migration-refactor-linux-app-service-mysql/current-architecture.png) 


## <a name="proposed-architecture"></a>Proponowana architektury

Po przypięciu dół ich bieżącej architektury, cele i wymagania dotyczące migracji, Contoso projektuje rozwiązanie wdrażania rozwiązania i identyfikuje proces migracji, w tym usług platformy Azure, które będą używały do migracji.

- Aplikacja sieci web warstwy na OSTICKETWEB zostaną poddane migracji, tworząc usługi Azure App Service w dwóch regionach platformy Azure. Usługa Azure App Service dla systemu Linux będą realizowane za pomocą kontenera platformy Docker programu PHP 7.0.
- Kod aplikacji, które zostaną przeniesione do usługi GitHub, a aplikacja internetowa platformy Azure zostanie skonfigurowana na potrzeby ciągłego dostarczania za pomocą usługi GitHub.
- Serwery aplikacji platformy Azure zostanie wdrożony w podstawowej (wschodnie stany USA 2) i regionu pomocniczego (środkowe stany USA).
- Usługa Traffic Manager zostanie skonfigurowana przed dwie aplikacje internetowe platformy Azure w obu regionach.
- Usługa Traffic Manager zostanie skonfigurowana w trybie priorytet w celu wymuszania ruchu za pośrednictwem wschodnie stany USA 2.
- Jeśli serwer aplikacji platformy Azure w regionie wschodnie stany USA 2 przejdzie do trybu offline, użytkownicy mogą uzyskiwać dostęp nie powiodło się nad aplikacją w środkowych stanach USA.
- Baza danych aplikacji zostaną poddane migracji do usługi Azure MySQL w usłudze PaaS przy użyciu narzędzia MySQL Workbench. Lokalna baza danych będzie kopii zapasowej lokalnie i przywrócić bezpośrednio do usługi Azure MySQL.
- Bazy danych będą znajdować się w regionie wschodnie stany USA 2 podstawowym w podsieci bazy danych (PROD-DB-EUS2) w sieci produkcyjnej (sieć wirtualna-PROD-EUS2):
- Ponieważ ich migrowania obciążeń produkcyjnych, zasobów platformy Azure dla aplikacji będą znajdować się w grupie zasobów w środowisku produkcyjnym **ContosoRG**.
- Zasób usługi Traffic Manager, które zostaną wdrożone w grupie zasobów infrastruktury firmy Contoso **ContosoInfraRG**.
- Lokalne maszyny wirtualne w centrum danych firmy Contoso zostanie zamknięty, po zakończeniu migracji.


![Architektura scenariusza](./media/contoso-migration-refactor-linux-app-service-mysql/proposed-architecture.png) 


## <a name="migration-process"></a>Proces migracji

Contoso zakończy proces migracji w następujący sposób:

1. Pierwszym krokiem Contoso konfiguruje infrastruktury platformy Azure, w tym inicjowania obsługi usługi Azure App Services, Konfigurowanie Menedżera ruchu i Inicjowanie obsługi administracyjnej wystąpienia usługi Azure MySQL.
2. Po przygotowaniu systemu Azure, mogą migrować bazy danych, przy użyciu aplikacji MySQL Workbench. 
3. Po uruchomieniu bazy danych na platformie Azure będzie Konfigurowanie prywatnego repozytorium GitHub dla usługi Azure App Service za pomocą ciągłego dostarczania i załaduj go z aplikacją osTicket.
4. W witrynie Azure portal są ładowane aplikacji z usługi GitHub do kontenera Docker, uruchomiony w usłudze Azure App Service. 
5. Dostosowanie ustawień systemu DNS i konfigurowanie skalowania automatycznego dla aplikacji.

![Proces migracji](./media/contoso-migration-refactor-linux-app-service-mysql/migration-process.png) 


### <a name="azure-services"></a>Usługi platformy Azure

**Usługa** | **Opis** | **Koszty**
--- | --- | ---
[Azure App Service](https://azure.microsoft.com/services/app-service/) | Usługa jest uruchamiana i skalowanie aplikacji przy użyciu usługi PaaS platformy Azure dla witryn sieci Web.  | Cennik zależy od rozmiaru wystąpienia i funkcje wymagane. [Dowiedz się więcej](https://azure.microsoft.com/pricing/details/app-service/windows/).
[Traffic Manager](https://azure.microsoft.com/services/traffic-manager/) | Moduł równoważenia obciążenia przy użyciu serwera DNS, udostępniając użytkownikom platformy Azure lub zewnętrznych witryn i usług. | Cennik zależy od liczby odebranych zapytań DNS, a liczba monitorowanych punktów końcowych. | [Dowiedz się więcej](https://azure.microsoft.com/pricing/details/traffic-manager/).
[Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/) | Baza danych jest oparta na aparacie serwer MySQL typu open source. Zapewnia społeczności w pełni zarządzana i gotowa do użycia w przedsiębiorstwie w przypadku bazy danych MySQL jako usługi w celu tworzenia i wdrażania aplikacji. | Cennik oparty na wystąpienia obliczeniowe, Magazyn i wymagań kopii zapasowej. [Dowiedz się więcej](https://azure.microsoft.com/pricing/details/mysql/).

 
## <a name="prerequisites"></a>Wymagania wstępne

Jeśli Ty (i firmy Contoso) mają być uruchamiane w tym scenariuszu, w tym miejscu jest powinny mieć.

**Wymagania** | **Szczegóły**
--- | ---
**Subskrypcja platformy Azure** | Należy utworzono już subskrypcję w okresie wcześniejsze artykuły w tej serii. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Jeśli bezpłatne konto właśnie zostało utworzone, jesteś administratorem subskrypcji i możesz wykonywać wszystkie akcje.<br/><br/> Jeśli używasz istniejącej subskrypcji i nie jesteś administratorem, musisz skontaktować się z administratorem w celu uprawnień właściciela lub współautora.<br/><br/> Jeśli potrzebujesz bardziej szczegółowych uprawnień, zapoznaj się z [w tym artykule](../site-recovery/site-recovery-role-based-linked-access-control.md). 
**Infrastruktura platformy Azure** | Konfigurowanie infrastruktury platformy Azure, zgodnie z opisem w contoso [infrastruktury platformy Azure na potrzeby migracji](contoso-migration-infrastructure.md).



## <a name="scenario-steps"></a>Kroki w scenariuszu

Poniżej przedstawiono, jak przeprowadzić migrację przez Azure:

> [!div class="checklist"]
> * **Krok 1: Świadczenia usługi Azure App Services**: Contoso przydzieli aplikacji sieci Web w regionach podstawowego i pomocniczego.
> * **Krok 2: Skonfigurować Traffic Manager**: ustawiają się usługi Traffic Manager przed aplikacji sieci Web, routingu i równoważenia obciążenia ruchu.
> * **Krok 3: Aprowizowanie MySQL**: na platformie Azure, firma Contoso ustanowienie wystąpienia bazy danych Azure MySQL.
> * **Krok 4: Migrację bazy danych**: migrowania bazy danych, przy użyciu aplikacji MySQL Workbench. 
> * **Krok 5: Skonfiguruj usługę GitHub**: Contoso konfiguruje lokalnego repozytorium GitHub na potrzeby witryn sieci web/kodu aplikacji.
> * **Krok 6. wdrażanie aplikacji sieci web**: Contoso służy do wdrażania aplikacji sieci web z witryny GitHub.




## <a name="step-1-provision-azure-app-services"></a>Krok 1: Aprowizowanie z usług Azure App Services

Firmy Contoso Inicjuje obsługę dwie aplikacje internetowe (po jednym w każdym regionie) przy użyciu usługi Azure App Services.

1. Tworzą zasób aplikacji sieci Web w regionie podstawowym wschodnie stany USA 2 (**osticket eus2**) w portalu Azure Marketplace.
2. Umieszczają zasobów w grupie zasobów w środowisku produkcyjnym **ContosoRG**.

    ![Aplikacja platformy Azure](./media/contoso-migration-refactor-linux-app-service-mysql/azure-app1.png) 

3. Tworzą nowy plan usługi App Service w regionie podstawowym (**EUS2-aplikacji-rozwiązań**), przy użyciu standardowego rozmiaru.

     ![Aplikacja platformy Azure](./media/contoso-migration-refactor-linux-app-service-mysql/azure-app2.png) 
    
4. Contoso wybiera systemu operacyjnego Linux, za pomocą stosu środowiska uruchomieniowego środowisko PHP 7.0, będąca kontenerem platformy Docker.

    ![Aplikacja platformy Azure](./media/contoso-migration-refactor-linux-app-service-mysql/azure-app3.png) 

5. Tworzą drugą aplikację internetową (**osticket cus**) i plan usługi App service w regionie środkowe stany USA.

    ![Aplikacja platformy Azure](./media/contoso-migration-refactor-linux-app-service-mysql/azure-app4.png) 


**Potrzebujesz dodatkowej pomocy?**

- Dowiedz się więcej o [aplikacji sieci Web w usłudze Azure App Service](https://docs.microsoft.com/azure/app-service/app-service-web-overview).
- Dowiedz się więcej o [usługa Azure App Service w systemie Linux](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-intro).


## <a name="step-2-set-up-traffic-manager"></a>Krok 2: Skonfigurować Traffic Manager

Contoso ustawia Konfigurowanie Menedżera ruchu przychodzącego ruchu internetowego żądania kierowane do aplikacji sieci Web uruchomionych na osTicket warstwa sieci web.

1. Contoso umożliwia utworzenie zasobu usługi Traffic Manager (**osticket.trafficmanager.net**) w portalu Azure Marketplace. Używają priorytet routingu, aby wschodnie stany USA 2 jest lokacją główną. Zasób mogą umieścić w swojej grupie zasobów infrastruktury (**ContosoInfraRG**). Należy pamiętać, że usługa Traffic Manager jest globalna i niepowiązana do określonej lokalizacji

    ![Traffic Manager](./media/contoso-migration-refactor-linux-app-service-mysql/traffic-manager1.png) 

2. Teraz contoso — Konfigurowanie usługi Traffic Manager z punktami końcowymi. Wschodnie stany USA 2 aplikacji sieci Web mogą dodać pełnić rolę podstawowej witryny (**osticket eus2**) i aplikacja środkowe stany USA, jako pomocniczy (**osticket cus**).

    ![Traffic Manager](./media/contoso-migration-refactor-linux-app-service-mysql/traffic-manager2.png) 

3. Po dodaniu punktów końcowych, mogą monitorować je.

    ![Traffic Manager](./media/contoso-migration-refactor-linux-app-service-mysql/traffic-manager3.png)

**Potrzebujesz dodatkowej pomocy?**

- Dowiedz się więcej o [usługi Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview).
- Dowiedz się więcej o [routingu ruchu do priorytetowego punktu końcowego](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-configure-priority-routing-method).
 
## <a name="step-3-provision-azure-database-for-mysql"></a>Krok 3: Inicjowanie obsługi administracyjnej Azure Database dla MySQL

Postanowienia firmy Contoso MySQL bazy danych wystąpienia w regionie wschodnie stany USA 2 podstawowym.

1. W witrynie Azure portal tworzą usługi Azure Database for MySQL zasobów. 

    ![MySQL](./media/contoso-migration-refactor-linux-app-service-mysql/mysql-1.png)

2. Dodaj ich nazwę **contosoosticket** dla bazy danych platformy Azure. Mogą dodać bazę danych do grupy zasobów w środowisku produkcyjnym **ContosoRG**, a następnie określ poświadczenia dla niego.
3. Lokalna baza danych MySQL jest wersji 5.7, więc wybierają tej wersji do zgodności. Używają domyślnym rozmiarze, odpowiadających ich wymagania dotyczące bazy danych.

     ![MySQL](./media/contoso-migration-refactor-linux-app-service-mysql/mysql-2.png)

4. Aby uzyskać **Opcje nadmiarowości kopii zapasowej**, Contoso wybiera się do użycia **magazynu geograficznie nadmiarowego**. Ta opcja służy do przywracania bazy danych w regionie środkowe stany USA, ich pomocniczym, jeśli wystąpi awaria. One można skonfigurować tylko tę opcję, gdy ich aprowizować bazę danych.

    ![Nadmiarowość](./media/contoso-migration-refactor-linux-app-service-mysql/db-redundancy.png)

4. Konfigurowanie połączenia zabezpieczeń firmy Contoso. W bazie danych > **zabezpieczenia połączeń**, ich konfigurowanie reguł zapory umożliwiające bazy danych na dostęp do usług platformy Azure.
5. Adres IP klienta lokalnej stacji roboczej co zwiększa początkowy i końcowy adres IP. Dzięki temu aplikacje sieci Web, dostępu do bazy danych MySQL wraz z klienta bazy danych, który przeprowadza migrację.

    ![MySQL](./media/contoso-migration-refactor-linux-app-service-mysql/mysql-3.png)



## <a name="step-4-migrate-the-database"></a>Krok 4: Migrację bazy danych

Contoso zostaną zmigrowane bazy danych przy użyciu kopii zapasowej i przywracania, przy użyciu narzędzia MySQL. Ich instalacja aplikacji MySQL Workbench, wykonaj kopię zapasową bazy danych z OSTICKETMYSQL, a następnie przywrócić do usługi Azure Database dla serwera MySQL.

### <a name="install-mysql-workbench"></a>Instalacja aplikacji MySQL Workbench

1. Sprawdzanie contoso [wymagań wstępnych oraz pliki do pobrania aplikacji MySQL Workbench](https://dev.mysql.com/downloads/workbench/?utm_source=tuicool).
2. Podczas instalacji aplikacji MySQL Workbench for Windows zgodnie z [instrukcje dotyczące instalacji](https://dev.mysql.com/doc/workbench/en/wb-installing.html). Maszyny, na którym instalacji muszą być dostępne dla maszyny Wirtualnej OSTICKETMYSQL i platformę Azure za pośrednictwem Internetu.
3. W aplikacji MySQL Workbench tworzą połączenia MySQL OSTICKETMYSQL. 

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench1.png)

4. Ich eksportowania bazy danych jako **osticket**, do lokalnego pliku niezależna.

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench2.png)

5. Po bazy danych ma lokalnie kopii zapasowej, tworzą połączenie usługi Azure Database for MySQL — wystąpienia.

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench3.png)

6. Teraz Contoso można zaimportować plik (przywracanie) bazy danych w wystąpieniu usługi Azure MySQL z niezależna. Nowy schemat (osticket) jest tworzony dla tego wystąpienia.

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench4.png)

7. Po przywróceniu danych można wykonywać zapytania przy użyciu aplikacji Workbench i pojawia się w witrynie Azure portal.

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench5.png)

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench6.png)

8. Na koniec firma Contoso potrzebuje zaktualizować informacje o bazie danych w usłudze web apps. W wystąpieniu programu MySQL otworzą **parametry połączenia**. 

     ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench7.png)

9. Na liście ciągów Znajdź ustawienia aplikacji sieci Web i kliknij, aby skopiować je.

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench8.png)

10. One Otwórz okno programu Notatnik i Wklej parametry do nowego pliku i zaktualizować je, aby dopasować osticket bazy danych, wystąpienia MySQL i ustawienia poświadczeń.

     ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench9.png)

11. Contoso można zweryfikować nazwy serwera i logowanie z **Przegląd** w wystąpieniu programu MySQL w witrynie Azure portal.

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench10.png)


## <a name="step-5-set-up-github"></a>Krok 5: Skonfiguruj usługę GitHub

Contoso tworzy nowy prywatnym repozytorium GitHub i konfiguruje połączenie z bazą danych osTicket w bazie danych MySQL w usłudze Azure. Następnie należy załadować aplikacji sieci Web platformy Azure przy użyciu aplikacji.  

1.  Przejdź do publicznego repozytorium GitHub OsTicket oprogramowania i rozwidlenie go do konta usługi GitHub firmy Contoso.

    ![GitHub](./media/contoso-migration-refactor-linux-app-service-mysql/github1.png)

2. Po rozwidlenia, przejdź do **obejmują** folder i Znajdź **ost config.php** pliku.

    ![GitHub](./media/contoso-migration-refactor-linux-app-service-mysql/github2.png)


3. Plik zostanie otwarty w przeglądarce i edytuj go.

    ![GitHub](./media/contoso-migration-refactor-linux-app-service-mysql/github3.png)

4. W edytorze Contoso aktualizuje Szczegóły bazy danych, w szczególności **DBHOST** i **DBUSER**. 

    ![GitHub](./media/contoso-migration-refactor-linux-app-service-mysql/github4.png)

5. Następnie Zatwierdź zmiany.

    ![GitHub](./media/contoso-migration-refactor-linux-app-service-mysql/github5.png)

6. Dla każdej aplikacji sieci web (**osticket eus2** i **osticket cus**), zmodyfikuj Contoso **ustawienia aplikacji** w witrynie Azure portal.

    ![GitHub](./media/contoso-migration-refactor-linux-app-service-mysql/github6.png)

7. Użytkownik podał parametry połączenia o nazwie **osticket**i skopiuj ciąg ze Schowka do **wartość obszaru**. Wybierają **MySQL** na liście rozwijanej obok ciągu, a następnie Zapisz ustawienia.

    ![GitHub](./media/contoso-migration-refactor-linux-app-service-mysql/github7.png)

## <a name="step-6-configure-the-web-apps"></a>Krok 6. Konfigurowanie aplikacji sieci Web

Ostatni krok w procesie migracji Contoso, należy skonfigurować aplikacje sieci web przy użyciu osTicket witryn sieci web.



1. W aplikacji internetowej głównej (**osticket eus2**) otwórz **opcji wdrożenia** i ustaw źródło **GitHub**.

    ![Konfigurowanie aplikacji](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app1.png)

2. Wybierają opcje wdrażania.

    ![Konfigurowanie aplikacji](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app2.png)

3. Po ustawieniu opcji przedstawiono konfigurację jako oczekujące w witrynie Azure portal.

    ![Konfigurowanie aplikacji](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app3.png)

4. Po zaktualizowaniu konfiguracji i aplikacji sieci web osTicket jest ładowany z usługi GitHub do kontenera Docket uruchamiania usługi Azure App Service, witryna jest wyświetlany jako aktywny.

    ![Konfigurowanie aplikacji](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app4.png)

5. Contoso następnie powtarza powyższe kroki dla aplikacji sieci web dodatkowej (**osticket cus**).
6. Po skonfigurowaniu lokacji jest dostępna za pośrednictwem profilu usługi Traffic Manager. Nazwa DNS jest nową lokalizację osTicket aplikacji. [Dowiedz się więcej](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain#map-a-cname-record).

    ![Konfigurowanie aplikacji](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app5.png)
    
7. Firma Contoso chce nazwę DNS, która jest łatwa do zapamiętania. One tworzenie rekordu aliasu (CNAME) **osticket.contoso.com** który wskazuje nazwę usługi Traffic Manager w systemie DNS na swoich kontrolerach domeny.

    ![Konfigurowanie aplikacji](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app6.png)

8. Skonfigurować zarówno **osticket eus2** i **osticket cus** aplikacji, aby umożliwić niestandardowe nazwy hostów sieci web.

    ![Konfigurowanie aplikacji](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app7.png)

### <a name="set-up-autoscaling"></a>Konfigurowanie skalowania automatycznego

Na koniec konfigurowania automatycznego skalowania dla aplikacji. Gwarantuje to, że jako agenci korzystanie z aplikacji, wystąpień aplikacji można zwiększać i zmniejszać zgodnie z potrzebami firmy. 

1. W usłudze App Service **EUS2-aplikacji-SRV**, otwórz Contoso **jednostki skalowania**.
2. Nowe ustawienie skalowania automatycznego mogą skonfigurować przy użyciu jednej reguły, które zwiększa o jeden liczbę wystąpień, gdy procent użycia procesora CPU dla bieżącego wystąpienia jest przekracza 70% przez 10 minut.

    ![Automatyczne skalowanie](./media/contoso-migration-refactor-linux-app-service-mysql/autoscale1.png)

3. To samo ustawienie ich konfigurowanie na **CUS-aplikacji-SRV** aby upewnić się, że takie samo zachowanie ma zastosowanie, jeśli aplikacja awaryjnie do regionu pomocniczego. Jedyna różnica polega na ich Ustaw limit wystąpienia 1, ponieważ są to przejścia w tryb failover tylko.

   ![Automatyczne skalowanie](./media/contoso-migration-refactor-linux-app-service-mysql/autoscale2.png)

##  <a name="clean-up-after-migration"></a>Wyczyść zasoby po ukończeniu migracji

Za pomocą po zakończeniu migracji aplikacji osTicket został zrefaktoryzowany do uruchomionego w aplikacji sieci Web platformy Azure za pomocą ciągłego dostarczania przy użyciu prywatnego repozytorium GitHub. Uruchamianie aplikacji w dwóch regionach w celu zwiększenia odporności na błędy. OsTicket bazy danych działa w usłudze Azure database for MySQL po migracji do korzystania z platformy PaaS.

Teraz Contoso musi wykonać następujące czynności: 
- Usuń maszyny wirtualne programu VMware z magazynu programu vCenter.
- Usuń lokalne maszyny wirtualne z lokalnego zadania tworzenia kopii zapasowej.
- Aktualizacja wewnętrznego dokumentach przedstawiono informacje na nowym lokalizacjom i adresów IP. 
- Przejrzyj wszystkie zasoby, które współdziałają z lokalnych maszyn wirtualnych i zaktualizuj wszelkie odpowiednie ustawienia lub dokumentacji, aby odzwierciedlić nową konfigurację.
- Zmień konfigurację monitorowania, aby wskazywała na adres URL osticket trafficmanager.net, aby śledzić, że aplikacja działa i uruchamiania.

## <a name="review-the-deployment"></a>Przegląd wdrożenia

Gdy aplikacja jest teraz uruchomiona Contoso muszą w pełni operacjonalizacji i zabezpieczanie ich nowej infrastruktury.

### <a name="security"></a>Bezpieczeństwo

Zespół ds. zabezpieczeń Contoso przeglądowi aplikacji, aby określić wszelkie problemy z zabezpieczeniami. One zidentyfikowane, że komunikacji między aplikacją osTicket i wystąpienie bazy danych MySQL nie jest skonfigurowany do obsługi protokołu SSL. Należy to zrobić, aby upewnić się, że ruchu bazy danych nie może stać się celem ataku. [Dowiedz się więcej](https://docs.microsoft.com/azure/mysql/howto-configure-ssl).

### <a name="backups"></a>Tworzenie kopii zapasowych

- Aplikacje sieci web osTicket nie zawierają dane o stanie i dzięki temu nie trzeba można utworzyć kopię zapasową.
- Nie muszą skonfigurować kopię zapasową bazy danych. Usługa Azure Database for MySQL — automatycznie tworzy kopie zapasowe serwera i przechowuje. Została wybrana opcja używania nadmiarowości geograficznej bazy danych, więc odpornej na błędy i gotowe do produkcji. Tworzenie kopii zapasowych można przywrócić serwer do punktu w czasie. [Dowiedz się więcej](https://docs.microsoft.com/azure/mysql/concepts-backup).


### <a name="licensing-and-cost-optimization"></a>Optymalizacja licencjonowania i kosztów

- Nie są problemy licencjonowania dla wdrożenia PaaS.
- Contoso — spowoduje to włączenie usługi Azure Cost Management licencjonowana przez firmę Cloudyn, podmiot zależny firmy Microsoft. To rozwiązanie do zarządzania kosztami wielu chmur, które ułatwia najwydajniejsze korzystanie z oraz zarządzania platformą Azure i innych zasobów w chmurze.  [Dowiedz się więcej](https://docs.microsoft.com/azure/cost-management/overview) o usłudze Azure Cost Management.



