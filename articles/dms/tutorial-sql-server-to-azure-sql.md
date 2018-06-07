---
title: Migrowanie serwera SQL z bazą danych SQL Azure za pomocą usługi migracji bazy danych Azure | Dokumentacja firmy Microsoft
description: Dowiedz się przeprowadzić migrację z lokalnej instalacji programu SQL Server z bazą danych SQL Azure za pomocą usługi Azure migracji bazy danych.
services: dms
author: edmacauley
ms.author: edmaca
manager: craigg
ms.reviewer: ''
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 06/06/2018
ms.openlocfilehash: 823f785bf33fd4d227fbdbb0f3c6b5eec914e08c
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2018
ms.locfileid: "34808013"
---
# <a name="migrate-sql-server-to-azure-sql-database-using-dms"></a>Migrowanie serwera SQL z bazą danych SQL Azure przy użyciu DMS
Usługa Azure bazy danych migracji umożliwia migrację bazy danych z lokalnego wystąpienia programu SQL Server do [bazy danych SQL Azure](https://docs.microsoft.com/en-us/azure/sql-database/). W tym samouczku, wykonywana jest migracja **Adventureworks2012** przywrócone do lokalnego wystąpienia programu SQL Server 2016 (lub nowsza) bazy danych do bazy danych SQL Azure za pomocą usługi Azure migracji bazy danych.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Oceń lokalnej bazy danych przy użyciu Asystenta migracji danych.
> * Migrowanie schematu próbki za pomocą Asystenta migracji danych.
> * Utwórz wystąpienie usługi migracji bazy danych Azure.
> * Utwórz projekt migracji za pomocą usługi Azure migracji bazy danych.
> * Uruchom migrację.
> * Monitoruj migrację.
> * Pobierz raport migracji.

## <a name="prerequisites"></a>Wymagania wstępne
Do ukończenia tego samouczka, musisz:

- Pobierz i zainstaluj [programu SQL Server 2016 lub nowszy](https://www.microsoft.com/sql-server/sql-server-downloads) (dowolna wersja).
- Włącz protokół TCP/IP, które jest domyślnie wyłączone podczas instalacji programu SQL Server Express przez postępując zgodnie z instrukcjami w artykule [włączyć lub wyłączyć protokół sieciowy serwera](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
- Utwórz wystąpienie wystąpienia bazy danych SQL Azure, co zrobić, wykonując szczegółowo w artykule [tworzenie bazy danych Azure SQL w portalu Azure](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal).
- Pobierz i zainstaluj [Asystenta migracji danych](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 lub nowszym.
- Tworzenie sieci Wirtualnej dla usługi Azure migracji bazy danych przy użyciu modelu wdrażania usługi Azure Resource Manager, który zapewnia połączenie lokacja lokacja z serwerami lokalnymi źródła przy użyciu [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) lub [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Upewnij się, że Twoje Azure (VNET) sieciowej grupy zabezpieczeń sieci wirtualnej reguły blok następujący komunikat porty 443, 53, 9354, 445, 12000. Aby uzyskać więcej szczegółów na filtrowanie ruchu NSG sieci Wirtualnej Azure, zobacz artykuł [filtrowania ruchu sieciowego z grup zabezpieczeń sieci](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- Konfigurowanie sieci [zapory systemu Windows dla dostępu aparatu bazy danych](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Otwórz Zaporę systemu Windows, aby umożliwić usłudze migracji bazy danych Azure dostępu do źródła SQL Server, która domyślnie jest TCP port 1433.
- Jeśli używasz wielu wystąpień programu SQL Server o nazwie przy użyciu portów dynamicznych, możesz włączyć usługę Przeglądarka SQL i umożliwiają dostęp do portu UDP 1434 na zaporach, dzięki czemu usługa migracji bazy danych Azure mogą łączyć się nazwanego wystąpienia w źródle serwer.
- Podczas korzystania z urządzenia zapory przed baz danych z źródła, może być konieczne dodanie reguły zapory, aby umożliwić dostęp do bazy danych źródłowego do migracji z usługi migracji bazy danych Azure.
- Tworzenie z poziomu serwera [reguły zapory](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) dla serwera bazy danych SQL Azure, aby umożliwić dostęp z migracji bazy danych Azure do docelowymi bazami danych. Podaj zakres podsieci sieci wirtualnej używane przez usługę Azure migracji bazy danych.
- Upewnij się, że poświadczenia użyte do nawiązania połączenia źródła wystąpienia programu SQL Server [serwera kontroli](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql) uprawnienia.
- Sprawdź, czy poświadczenia użyte do nawiązania połączenia docelowego wystąpienia bazy danych SQL Azure mają uprawnienia bazy danych kontroli na docelowymi bazami danych Azure SQL.

## <a name="assess-your-on-premises-database"></a>Oceny lokalnej bazy danych
Przed przeprowadzeniem migracji danych z lokalnego wystąpienia programu SQL Server z bazą danych SQL Azure, należy dokonać oceny problemy blokujące, które mogą uniemożliwić migracji bazy danych SQL Server. Za pomocą Asystenta migracji danych v3.3 lub później, wykonaj czynności opisane w artykule [wykonywania oceny migracji programu SQL Server](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem) przeprowadzenie lokalnej bazy danych do oceny. Podsumowanie kroki wymagane są następujące:
1.  W Asystencie migracji danych wybierz ikonę Nowy (+), a następnie wybierz **oceny** typu projektu.
2.  Określ nazwę projektu w **typ serwera źródła** pole tekstowe, wybierz **programu SQL Server**w **typ serwer docelowy** pole tekstowe, wybierz opcję **bazy danych SQL Azure**, a następnie wybierz **Utwórz** Aby utworzyć projekt.

    Oceniając źródłowej bazy danych programu SQL Server, migracja do bazy danych SQL Azure, możesz wybrać jedną lub obie następujące typy raportów oceny:
    - Sprawdzanie zgodności bazy danych
    - Sprawdzanie równoważności funkcji

    Domyślnie są zaznaczone obu typów raportów.

3.  W Asystencie migracji danych na **opcje** ekranu wybierz **dalej**.
4.  Na **Wybierz źródła** ekranu w **Połącz z serwerem** okno dialogowe, podaj szczegóły połączenia z serwerem SQL, a następnie wybierz **Connect**.
5.  W **Dodaj źródła** okno dialogowe, wybierz opcję **AdventureWorks2012**, wybierz pozycję **Dodaj**, a następnie wybierz **Start oceny**.

    Po zakończeniu oceny wyniki są wyświetlane jak pokazano na poniższym rysunku:

    ![Oceny migracji danych](media\tutorial-sql-server-to-azure-sql\dma-assessments.png)

    Bazy danych SQL Azure oceny identyfikowanie problemów z parzystością funkcji i problemy z blokowaniem migracji.

    - **Parzystość funkcji programu SQL Server** kategorii zapewnia rozbudowany zestaw zaleceń, dostępnej na platformie Azure, alternatywnych metod i zmniejszenia kroki, aby ułatwić planowanie nakład pracy w projektach migracji.
    - **Problemy ze zgodnością** kategorii identyfikuje częściowo obsługiwane i nieobsługiwane funkcje, które odzwierciedlać problemy ze zgodnością może uniemożliwić Migrowanie baz danych programu SQL Server lokalną bazą danych SQL Azure. Podawane są również zalecenia ułatwiające rozwiązanie tych problemów.

6.  Przejrzyj wyniki oceny do rozwiązywania problemów parzystość funkcji i problemy z blokowaniem migracji wybranie określonych opcji.

## <a name="migrate-the-sample-schema"></a>Migrowanie schematu próbki
Po wykonaniu potrafisz oceny i stwierdzeniu, że wybrana baza danych działało kandydatem do migracji do usługi Azure SQL Database, korzystanie z Asystenta migracji danych do migracji schematu do bazy danych SQL Azure.

> [!NOTE]
> Przed utworzeniem projektu migracji w Asystenta migracji danych, należy się upewnić, że już zapewnieniu bazy danych Azure SQL określone w wymaganiach wstępnych. Do celów tego samouczka, nazwa bazy danych SQL Azure zakłada się, że **AdventureWorksAzure**, ale udostępniają nazwa ma.

Aby przeprowadzić migrację **AdventureWorks2012** schematu z bazą danych SQL Azure, wykonaj następujące czynności:

1.  W Asystencie migracji danych wybierz ikonę Nowy (+), a następnie w obszarze **typ projektu**, wybierz pozycję **migracji**.
2.  Określić nazwę projektu w **typ serwera źródła** pole tekstowe, wybierz **programu SQL Server**, a następnie w **typ serwer docelowy** pole tekstowe, wybierz **Azure SQL Baza danych**.
3.  W obszarze **zakresu migracji**, wybierz pozycję **tylko schemat**.

    Po wykonaniu poprzednich kroków, interfejs Asystenta migracji danych powinna być taka jak pokazano na poniższym rysunku:
    
    ![Utwórz projekt Asystenta migracji danych](media\tutorial-sql-server-to-azure-sql\dma-create-project.png)

4.  Wybierz **Utwórz** Aby utworzyć projekt.
5.  W Asystencie migracji danych Określ szczegóły połączenia źródła dla programu SQL Server, wybierz **Connect**, a następnie wybierz **AdventureWorks2012** bazy danych.

    ![Szczegóły połączenia źródła Asystenta migracji danych](media\tutorial-sql-server-to-azure-sql\dma-source-connect.png)

6.  Wybierz **dalej**w obszarze **Połącz z serwerem docelowym**, określ szczegóły połączenia docelowej bazy danych Azure SQL, wybierz **Connect**, a następnie wybierz **AdventureWorksAzure** bazy danych można było wstępnie tworzyć w bazie danych Azure SQL.

    ![Szczegóły połączenia z docelowym Asystenta migracji danych](media\tutorial-sql-server-to-azure-sql\dma-target-connect.png)

7.  Wybierz **dalej** aby przejść do **wybierz obiekty** ekran, na którym można określić obiektów schematu w **AdventureWorks2012** bazy danych, które muszą zostać wdrożone na platformie Azure Baza danych SQL.

    Domyślnie są zaznaczone wszystkie obiekty.

    ![Generowanie skryptów SQL](media\tutorial-sql-server-to-azure-sql\dma-assessment-source.png)

8.  Wybierz **skryptu SQL Generowanie** można utworzyć skrypty SQL, a następnie przejrzyj skrypty występują błędy.

    ![Skrypt schematu](media\tutorial-sql-server-to-azure-sql\dma-schema-script.png)

9.  Wybierz **schema wdrażania** można wdrożyć schematu bazy danych SQL Azure, a następnie po wdrożeniu schematu Sprawdź serwera docelowego dla wszelkich nieprawidłowości.

    ![Wdrażanie schematu](media\tutorial-sql-server-to-azure-sql\dma-schema-deploy.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Rejestrowanie dostawcy zasobów Microsoft.DataMigration
1. Zaloguj się do portalu Azure, wybierz opcję **wszystkie usługi**, a następnie wybierz **subskrypcje**.
 
   ![Pokaż portalu subskrypcji](media\tutorial-sql-server-to-azure-sql\portal-select-subscription1.png)
       
2. Wybierz subskrypcję, w której chcesz utworzyć wystąpienie usługi Azure Database Migration Service, a następnie wybierz pozycję **Dostawcy zasobów**.
 
    ![Pokaż dostawców zasobów](media\tutorial-sql-server-to-azure-sql\portal-select-resource-provider.png)
    
3.  Wyszukiwanie do migracji, a następnie po prawej stronie **Microsoft.DataMigration**, wybierz pozycję **zarejestrować**.
 
    ![Rejestrowanie dostawcy zasobów](media\tutorial-sql-server-to-azure-sql\portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Tworzenie wystąpienia
1.  W portalu Azure wybierz + **Utwórz zasób**, wyszukaj usługi migracji bazy danych Azure, a następnie wybierz **usługi migracji bazy danych Azure** z listy rozwijanej.

    ![Azure Marketplace](media\tutorial-sql-server-to-azure-sql\portal-marketplace.png)

2.  Na **usługi migracji bazy danych Azure** ekranu wybierz **Utwórz**.
 
    ![Utwórz wystąpienie usługi migracji bazy danych Azure](media\tutorial-sql-server-to-azure-sql\dms-create1.png)
  
3.  Na **Utwórz usługę migracji** ekranu, określ nazwę usługi, subskrypcji i grupy nowy lub istniejący zasób.

4. Wybierz istniejącą sieć wirtualną (VNET) lub Utwórz nową.

    Sieć wirtualna zawiera usługi migracji bazy danych Azure dostęp do źródła programu SQL Server i docelowego wystąpienia bazy danych SQL Azure.

    Aby uzyskać więcej informacji dotyczących sposobu tworzenia sieci Wirtualnej w portalu Azure, zobacz artykuł [utworzyć sieć wirtualną przy użyciu portalu Azure](https://aka.ms/DMSVnet).

5. Wybierz warstwę cenową.

    Aby uzyskać więcej informacji na kosztów i warstwy cenowej, zobacz [cennikiem](https://aka.ms/dms-pricing).

    Jeśli potrzebujesz pomocy w wyborze prawo warstwy usługi migracji bazy danych Azure, zapoznaj się z zaleceniami w Księgowanie [tutaj](https://go.microsoft.com/fwlink/?linkid=861067).  

     ![Skonfiguruj ustawienia wystąpienia usługi migracji bazy danych Azure](media\tutorial-sql-server-to-azure-sql\dms-settings1.png)

6.  Wybierz **Utwórz** można utworzyć usługi.

## <a name="create-a-migration-project"></a>Tworzenie projektu migracji
Po utworzeniu usługi znalezienie go w portalu Azure, otwórz go, a następnie utwórz nowy projekt migracji.

1. W portalu Azure wybierz **wszystkie usługi**, wyszukaj usługi migracji bazy danych Azure, a następnie wybierz **usługi migracji bazy danych Azure**.
 
      ![Znajdź wszystkie wystąpienia usługi migracji bazy danych Azure](media\tutorial-sql-server-to-azure-sql\dms-search.png)

2. Na **usługi migracji bazy danych Azure** ekranu, wyszukaj nazwę usługi migracji bazy danych Azure wystąpienia utworzony, a następnie wybierz wystąpienie.
 
     ![Zlokalizuj Twoje wystąpienie usługi migracji bazy danych Azure](media\tutorial-sql-server-to-azure-sql\dms-instance-search.png)
 
3. Wybierz + **nowy projekt migracji**.
4. Na **nowy projekt migracji** ekranu, należy określić nazwę projektu, w **typ serwera źródła** pole tekstowe, wybierz opcję **programu SQL Server**, a następnie w **docelowego Typ serwera** pole tekstowe, wybierz opcję **bazy danych SQL Azure**.

    ![Tworzenie projektu usługi bazy danych migracji](media\tutorial-sql-server-to-azure-sql\dms-create-project1.png)

5.  Wybierz **Utwórz** Aby utworzyć projekt.

## <a name="specify-source-details"></a>Określ szczegóły źródła
1. Na **źródła szczegóły** ekranu, określ szczegóły połączenia dla źródła wystąpienia programu SQL Server.
 
    Upewnij się użyć pełni kwalifikowanej domeny nazwę (FQDN) dla nazwy wystąpienia programu SQL Server źródła. Adres IP umożliwia także w sytuacji, w których DNS rozpoznawania nazw nie jest możliwe.

2. Jeśli nie zainstalowano zaufanego certyfikatu na serwerze źródłowym, wybierz **zaufania certyfikatów serwera** pole wyboru.

    Zaufanego certyfikatu nie jest zainstalowany, programu SQL Server generuje certyfikat z podpisem własnym, po uruchomieniu tego wystąpienia. Ten certyfikat służy do szyfrowania poświadczeń dla połączeń klienta.

    > [!CAUTION]
    > Połączeń SSL, które są encyopted przy użyciu certyfikatu z podpisem własnym nie zapewnia dużego bezpieczeństwa. Są one narażony na ataki man-in--middle. Nie należy polegać na protokołu SSL przy użyciu certyfikatów z podpisem własnym w środowisku produkcyjnym lub na serwerach, które są połączone z Internetem.

   ![Szczegóły źródła](media\tutorial-sql-server-to-azure-sql\dms-source-details1.png)
  
2. Wybierz **zapisać**, a następnie wybierz **AdventureWorks2012** bazy danych do migracji.

    ![Wybierz źródło bazy danych](media\tutorial-sql-server-to-azure-sql\dms-select-source-db1.png)

## <a name="specify-target-details"></a>Określ szczegóły docelowego
1. Wybierz **zapisać**, a następnie na **docelowe szczegóły** ekranu, określ szczegóły połączenia dla elementu docelowego serwera bazy danych SQL Azure, który jest wstępnie przygotowany bazy danych SQL Azure z którym **AdventureWorks2012** schematu została wdrożona przy użyciu Asystenta migracji danych.

    ![Wybierz element docelowy](media\tutorial-sql-server-to-azure-sql\dms-select-target1.png)

2. Wybierz **zapisać** zapisać projektu.

3. Na **podsumowanie projektu** ekranu, przejrzyj i sprawdź szczegóły skojarzone z projektem migracji.

    ![Podsumowanie DMS](media\tutorial-sql-server-to-azure-sql\dms-summary1.png)

4. Wybierz pozycję **Zapisz**.

## <a name="run-the-migration"></a>Uruchom migrację
1.  Wybierz ostatnio zapisany projekt, wybierz pozycję + **nowe działanie**, a następnie wybierz **uruchamiania migracji**.

    ![Nowe działanie](media\tutorial-sql-server-to-azure-sql\dms-new-activity1.png)

2.  Po wyświetleniu monitu wprowadź poświadczenia dla źródła i serwerów docelowych, a następnie wybierz **zapisać**.

3.  Na **mapy w celu docelowymi bazami danych** ekranu, mapy źródłowej i docelowej bazy danych do migracji.

    Jeśli docelowa baza danych zawiera nazwę bazy danych jako źródłowej bazy danych, Azure DMS domyślnie wybiera docelowej bazy danych.

    ![Mapuj do docelowych baz danych](media\tutorial-sql-server-to-azure-sql\dms-map-targets-activity1.png)

4. Wybierz **zapisać**na **wybierz tabele** ekranu, rozwiń tabelę z listą, a następnie przejrzyj listę odpowiednich pól.

    Należy pamiętać, że usługa migracji bazy danych Azure automatycznie wybiera wszystkie tabele pusty źródło istnieje w docelowym wystąpieniu bazy danych SQL Azure. Jeśli chcesz ponownie migrować tabel, które już zawierają dane, musisz jawnie wybrać tabele w tym bloku.

    ![Wybór tabel](media\tutorial-sql-server-to-azure-sql\dms-configure-setting-activity1.png)

5.  Wybierz **zapisać**na **podsumowanie migracji** ekranu w **Nazwa działania** tekst Określ nazwę działania migracji.

6. Rozwiń węzeł **opcję sprawdzania poprawności** sekcji, aby wyświetlić **wybierz opcję sprawdzania poprawności** ekranu, określ, czy można sprawdzić poprawności migrowanych bazy danych dla **Porównanie schematu**, **Spójność danych**, i **zapytania poprawności**.
    
    ![Wybierz opcję weryfikacji](media\tutorial-sql-server-to-azure-sql\dms-configuration1.png)

6.  Wybierz **zapisać**, zapoznaj się z podsumowaniem, aby upewnić się, że szczegóły źródłowa i docelowa są zgodne co wcześniej określona.

    ![Podsumowanie migracji](media\tutorial-sql-server-to-azure-sql\dms-run-migration1.png)

7.  Wybierz **uruchamiania migracji**.

    Zostanie wyświetlone okno działania migracji oraz **stan** działania jest **oczekujące**.

    ![Stan aktywności](media\tutorial-sql-server-to-azure-sql\dms-activity-status1.png)

## <a name="monitor-the-migration"></a>Monitoruj migrację
1. Na ekranie działania migracji wybierz **Odśwież** Aby zaktualizować wyświetlane do momentu **stan** migracji jest pokazywana jako **Ukończono**.

    ![Stan działania ukończone](media\tutorial-sql-server-to-azure-sql\dms-completed-activity1.png)

2. Po ukończeniu migracji, zaznacz **Pobierz raport** uzyskać raport zawierający listę szczegółów skojarzonych z procesem migracji.

3. Sprawdź docelowej bazy danych na serwerze docelowym, baza danych SQL Azure.

### <a name="additional-resources"></a>Zasoby dodatkowe

 * [Migracja SQL przy użyciu usługi migracji danych Azure (DMS)](https://www.microsoft.com/handsonlabs/SelfPacedLabs/?storyGuid=3b671509-c3cd-4495-8e8f-354acfa09587) laboratorium praktycznego.