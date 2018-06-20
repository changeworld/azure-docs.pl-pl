---
title: Migracja bazy danych SQL Server do usługi Azure SQL Database przy użyciu usługi DMA | Microsoft Docs
description: Dowiedz się, jak przeprowadzić migrację bazy danych SQL Server do usługi Azure SQL Database przy użyciu usługi DMA.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: mvc,migrate
ms.topic: tutorial
ms.date: 05/22/2018
ms.author: carlrab
ms.openlocfilehash: be22f2b45262b144c87a0e97f1c78e0167b0e24d
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34648102"
---
# <a name="migrate-your-sql-server-database-to-azure-sql-database-using-dma"></a>Migracja bazy danych SQL Server do usługi Azure SQL Database przy użyciu usługi DMA

Przeniesienie bazy danych SQL Server do pojedynczej bazy danych usługi Azure SQL Database jest proste i polega na utworzeniu pustej bazy danych SQL na platformie Azure, a następnie zaimportowaniu odpowiedniej bazy danych do platformy Azure przy użyciu programu [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) (DMA). Dodatkowe opcje migracji zostały przedstawione w temacie [Migrate your database to Azure SQL Database (Migracja bazy danych do usługi Azure SQL Database)](sql-database-cloud-migrate.md).

> [!IMPORTANT]
> Aby przeprowadzić migrację do wystąpienia zarządzanego usługi Azure SQL Database, zobacz [Migrate from SQL Server to a Managed Instance (Migracja z programu SQL Server do wystąpienia zarządzanego)](sql-database-managed-instance-migrate.md)

Z tego samouczka dowiesz się, jak wykonać następujące czynności:

> [!div class="checklist"]
> * Tworzenie pustej bazy danych usługi Azure SQL Database w witrynie Azure Portal (przy użyciu nowego lub istniejącego serwera usługi Azure SQL Database)
> * Tworzenie reguły zapory na poziomie serwera w witrynie Azure Portal (jeśli nie została utworzona wcześniej)
> * Importowanie bazy danych programu SQL Server do pustej bazy danych usługi Azure SQL Database za pomocą programu [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) (DMA) 
> * Zmienianie właściwości bazy danych za pomocą programu [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania zadań opisanych w tym samouczku niezbędne jest spełnienie następujących wymagań wstępnych:

- Zainstalowanie najnowszej wersji programu [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).  
- Zainstalowanie najnowszej wersji programu [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) (DMA).
- Określenie i uzyskanie dostępu do bazy danych, która ma zostać zmigrowana. W tym samouczku używana jest [baza danych SQL Server 2008R2 AdventureWorks OLTP](https://msftdbprodsamples.codeplex.com/releases/view/59211) w wystąpieniu programu SQL Server 2008R2 lub nowszego, ale można użyć dowolnej bazy danych. Aby rozwiązać problemy ze zgodnością, użyj narzędzi [SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)

## <a name="log-in-to-the-azure-portal"></a>Logowanie do witryny Azure Portal

Zaloguj się do witryny [Azure Portal](https://portal.azure.com/).

## <a name="create-a-blank-sql-database"></a>Tworzenie pustej bazy danych SQL

Baza danych Azure SQL jest tworzona ze zdefiniowanym zestawem [zasobów obliczeniowych i przechowywania](sql-database-service-tiers-dtu.md). Baza danych jest tworzona w [grupie zasobów platformy Azure](../azure-resource-manager/resource-group-overview.md) oraz na [serwerze logicznym bazy danych Azure SQL Database](sql-database-features.md). 

Wykonaj poniższe czynności, aby utworzyć pustą bazę danych SQL. 

1. W lewym górnym rogu witryny Azure Portal kliknij przycisk **Utwórz zasób**.

2. Na stronie **Nowy** wybierz pozycję **Bazy danych**, a następnie na stronie **Nowy** w obszarze **SQL Database** wybierz pozycję **Utwórz**.

   ![tworzenie pustej bazy danych](./media/sql-database-design-first-database/create-empty-database.png)

3. Wypełnij formularz Baza danych SQL w sposób pokazany na wcześniejszej ilustracji, używając następujących informacji:   

   | Ustawienie       | Sugerowana wartość | Opis | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **Nazwa bazy danych** | mySampleDatabase | Prawidłowe nazwy baz danych opisano w artykule [Database Identifiers](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers) (Identyfikatory baz danych). | 
   | **Subskrypcja** | Twoja subskrypcja  | Aby uzyskać szczegółowe informacje o subskrypcjach, zobacz [Subskrypcje](https://account.windowsazure.com/Subscriptions). |
   | **Grupa zasobów** | myResourceGroup | Prawidłowe nazwy grup zasobów opisano w artykule [Naming rules and restrictions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Reguły i ograniczenia nazewnictwa). |
   | **Wybierz źródło** | Pusta baza danych | Określa, że ma zostać utworzona pusta baza danych. |

4. Kliknij pozycję **Serwer**, aby utworzyć i skonfigurować nowy serwer dla nowej bazy danych. Wypełnij **formularz nowego serwera**, używając następujących informacji: 

   | Ustawienie       | Sugerowana wartość | Opis | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **Nazwa serwera** | Dowolna nazwa unikatowa w skali globalnej | Prawidłowe nazwy serwera opisano w artykule [Naming rules and restrictions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Reguły i ograniczenia nazewnictwa). | 
   | **Identyfikator logowania administratora serwera** | Dowolna prawidłowa nazwa | Prawidłowe nazwy identyfikatorów logowania opisano w artykule [Database Identifiers](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers) (Identyfikatory baz danych).|
   | **Hasło** | Dowolne prawidłowe hasło | Hasło musi mieć co najmniej osiem znaków i musi zawierać znaki z trzech z następujących kategorii: wielkie litery, małe litery, cyfry i znaki inne niż alfanumeryczne. |
   | **Lokalizacja** | Dowolna prawidłowa lokalizacja | Aby uzyskać informacje na temat regionów, zobacz temat [Regiony systemu Azure](https://azure.microsoft.com/regions/). |

   ![tworzenie serwera bazy danych](./media/sql-database-design-first-database/create-database-server.png)

5. Kliknij pozycję **Wybierz**.

6. Kliknij pozycję **Warstwa cenowa**, aby określić warstwę usługi, liczbę jednostek DTU i ilość miejsca do magazynowania. Przejrzyj opcje liczby jednostek DTU i miejsca do magazynowania dostępne dla poszczególnych warstw usługi. 

7. Na potrzeby tego samouczka wybierz warstwę usługi **Standardowa**, a następnie wybierz za pomocą suwaka **100 jednostek DTU (S3)** i **400** GB miejsca do magazynowania.

   ![tworzenie bazy danych s1](./media/sql-database-design-first-database/create-empty-database-pricing-tier.png)

8. Zaakceptuj warunki wersji zapoznawczej, aby użyć opcji **dodatkowego magazynu**. 

   > [!IMPORTANT]
   > - Rozmiary magazynu większe niż uwzględniona ilość miejsca do magazynowania są dostępne w wersji zapoznawczej i za dodatkową opłatą. Szczegóły można znaleźć w [cenniku usługi SQL Database](https://azure.microsoft.com/pricing/details/sql-database/). 
   >
   > - Więcej niż 1 TB pamięci w warstwie Premium jest dostępne we wszystkich regionach z wyjątkiem poniższych: Północne Zjednoczone Królestwo, Zachodnio-środkowe stany USA, Południowe Zjednoczone Królestwo2, Chiny Wschodnie, USDoDCentral, Niemcy Środkowe, USDoDEast, Południowo-Zachodnie Stany USA US Gov, Południowo-środkowe stany USA US Gov, Niemcy Północno-Wschodnie, Chiny Północne, Wschodnie stany USA US Gov. Zaplanowane jest zwiększenie dostępności. W pozostałych regionach maksymalna wielkość pamięci w warstwie Premium jest ograniczona do 1 TB. Więcej informacji można znaleźć na stronie [bieżących ograniczeń poziomów P11–P15](sql-database-dtu-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  
   > 

9. Po wybraniu warstwy serwera, liczby jednostek DTU i ilości miejsca do magazynowania kliknij przycisk **Zastosuj**.  

10. Wybierz **sortowanie** dla pustej bazy danych (na potrzeby tego samouczka użyj wartości domyślnej). Aby uzyskać więcej informacji na temat sortowań, zobacz [Sortowania](https://docs.microsoft.com/sql/t-sql/statements/collations)

11. Teraz po uzupełnieniu formularza SQL Database kliknij przycisk **Utwórz**, aby aprowizować bazę danych. Aprowizacja zajmuje kilka minut. 

12. Na pasku narzędzi kliknij pozycję **Powiadomienia**, aby monitorować proces wdrażania.
    
     ![powiadomienie](./media/sql-database-get-started-portal/notification.png)

## <a name="create-a-server-level-firewall-rule"></a>Tworzenie reguły zapory na poziomie serwera

Usługa SQL Database tworzy zaporę na poziomie serwera, która uniemożliwia zewnętrznym aplikacjom i narzędziom łączenie się z serwerem i wszelkimi bazami danych na tym serwerze, chyba że zostanie utworzona reguła zapory otwierająca zaporę dla konkretnych adresów IP. Wykonaj następujące kroki, aby utworzyć [regułę zapory na poziomie serwera usługi SQL Database](sql-database-firewall-configure.md) dla podanego adresu IP klienta i włączyć zewnętrzną łączność przez zaporę usługi SQL Database wyłącznie dla konkretnego adresu IP. 

> [!NOTE]
> Usługa SQL Database nawiązuje komunikację na porcie 1433. Jeśli próbujesz nawiązać połączenie z sieci firmowej, ruch wychodzący na porcie 1433 może być zablokowany przez firmową zaporę. Jeśli nastąpi taka sytuacja, nie będzie można nawiązać połączenia z serwerem usługi Azure SQL Database, chyba że dział IT otworzy port 1433.
>

1. Po ukończeniu wdrażania kliknij pozycję **Bazy danych SQL** w menu po lewej stronie i kliknij bazę danych **mySampleDatabase** na stronie **Bazy danych SQL**. Zostanie otwarta strona przeglądu bazy danych zawierająca w pełni kwalifikowaną nazwę serwera (na przykład **mynewserver-20170824.database.windows.net**) i opcje dalszej konfiguracji. 

2. Skopiuj tę w pełni kwalifikowaną nazwę serwera w celu nawiązania połączenia z serwerem i jego bazami danych w kolejnych przewodnikach Szybki start. 

   ![nazwa serwera](./media/sql-database-get-started-portal/server-name.png) 

3. Kliknij pozycję **Ustaw zaporę serwera** na pasku narzędzi. Zostanie otwarta strona **Ustawienia zapory** dla serwera SQL Database. 

   ![reguła zapory serwera](./media/sql-database-get-started-portal/server-firewall-rule.png) 

4. Kliknij pozycję **Dodaj adres IP klienta** na pasku narzędzi, aby dodać bieżący adres IP do nowej reguły zapory. Reguła zapory może otworzyć port 1433 dla pojedynczego adresu IP lub zakresu adresów IP.

5. Kliknij pozycję **Zapisz**. Dla bieżącego adresu IP zostanie utworzona reguła zapory na poziomie serwera otwierająca port 1433 na serwerze logicznym.

6. Kliknij przycisk **OK**, a następnie zamknij stronę **Ustawienia zapory**.

Teraz możesz nawiązać połączenie z serwerem usługi SQL Database i jego bazami danych, korzystając z programu SQL Server Management Studio, Data Migration Assistant lub innego wybranego narzędzia, z tego adresu IP za pomocą konta administratora serwera utworzonego w poprzedniej procedurze.

> [!IMPORTANT]
> Domyślnie dostęp za pośrednictwem zapory usługi SQL Database jest włączony dla wszystkich usług platformy Azure. Kliknij przycisk **WYŁ.** na tej stronie, aby wyłączyć tę opcję dla wszystkich usług platformy Azure.

## <a name="sql-server-connection-information"></a>Informacje o połączeniu z serwerem SQL

Pobierz w pełni kwalifikowaną nazwę serwera dla serwera Azure SQL Database w witrynie Azure Portal. W pełni kwalifikowana nazwa serwera służy do nawiązywania połączenia z serwerem usługi Azure SQL Database przy użyciu narzędzi klienckich, takich jak program Data Migration Assistant lub SQL Server Management Studio.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/).
2. Wybierz opcję **Bazy danych SQL** z menu po lewej stronie, a następnie kliknij bazę danych na stronie **Bazy danych SQL**. 
3. W okienku **Essentials** na stronie bazy danych w witrynie Azure Portal zlokalizuj i skopiuj **nazwę serwera**.

   ![informacje o połączeniu](./media/sql-database-get-started-portal/server-name.png)

## <a name="migrate-your-database"></a>Migrowanie bazy danych

Wykonaj następujące kroki, aby za pomocą programu **[Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595)** ocenić gotowość swojej bazy danych do migracji do usługi Azure SQL Database i przeprowadzić migrację.

1. Otwórz program **Data Migration Assistant**. Program Data Migration Assistant można uruchomić na dowolnym komputerze połączonym z wystąpieniem programu SQL Server zawierającym bazę danych, która ma zostać zmigrowana, i połączonym z Internetem. Nie trzeba go instalować na komputerze hostującym wystąpienie programu SQL Server, które ma być migrowane. Reguła zapory utworzona w poprzedniej procedurze musi obowiązywać na komputerze, na którym jest uruchamiany program Data Migration Assistant.

     ![otwieranie programu Data Migration Assistant](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-open.png)

2. W menu po lewej stronie kliknij pozycję **+ New** (+ Nowy), aby utworzyć projekt **Assessment** (Ocena). Wypełnij wymagane wartości, a następnie kliknij pozycję **Create** (Utwórz):

   | Ustawienie      | Sugerowana wartość | Opis | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | Project type (Typ projektu) | Migracja | Wybierz ocenę bazy danych pod kątem migracji lub ocenę i migrację w ramach tego samego przepływu pracy |
   |Project name (Nazwa projektu)|Migration tutorial (Samouczek migracji)| Nazwa opisowa |
   |Source server type (Typ serwera źródłowego)| Oprogramowanie SQL Server | Jest to jedyne obecnie obsługiwane źródło |
   |Target server type (Typ serwera docelowego)| Azure SQL Database| Dostępne są następujące opcje do wyboru: Azure SQL Database, SQL Server, SQL Server on Azure virtual machines (Program SQL Server na maszynach wirtualnych platformy Azure) |
   |Migration Scope (Zakres migracji)| Schema and data (Schemat i dane)| Dostępne są następujące opcje do wyboru: Schema and data (Schemat i dane), Schema only (Tylko schemat), Data only (Tylko dane) |
   
   ![nowy projekt programu Data Migration Assistant](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-new-project.png)

3.  Na stronie **Select source** (Wybieranie źródła) wypełnij wymagane wartości, a następnie kliknij przycisk **Connect** (Połącz):

    | Ustawienie      | Sugerowana wartość | Opis | 
    | ------------ | ------------------ | ------------------------------------------------- | 
    | Nazwa serwera | Nazwa serwera lub adres IP | Nazwa serwera lub adres IP |
    | Typ uwierzytelniania | Preferowany typ uwierzytelniania| Opcje do wyboru: Windows Authentication (Uwierzytelnianie systemu Windows), SQL Server Authentication (Uwierzytelnianie programu SQL Server), Active Directory Integrated Authentication (Zintegrowane uwierzytelnianie usługi Active Directory), Active Directory Password Authentication (Uwierzytelnianie hasłem usługi Active Directory) |
    | Nazwa użytkownika | Twoja nazwa logowania | Nazwa logowania musi mieć uprawnienia **CONTROL SERVER** (KONTROLA SERWERA) |
    | Hasło| Twoje hasło | Twoje hasło |
    | Connection properties (Właściwości połączenia)| Wybierz pozycje **Encrypt connection** (Szyfruj połączenie) i **Trust server certificate** (Certyfikat serwera zaufania) odpowiednie dla danego środowiska. | Wybierz właściwości odpowiednie dla połączenia z danym serwerem |

    ![wybieranie źródła nowej migracji danych](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-source.png)

5. Wybierz jedną bazę danych z serwera źródłowego, aby przeprowadzić jej migrację do usługi Azure SQL Database, a następnie kliknij przycisk **Next** (Dalej). W tym samouczku jest używana tylko jedna baza danych.

6. Na stronie **Select target** (Wybieranie celu) wypełnij wymagane wartości, a następnie kliknij przycisk **Connect** (Połącz):

    | Ustawienie      | Sugerowana wartość | Opis | 
    | ------------ | ------------------ | ------------------------------------------------- | 
    | Nazwa serwera | W pełni kwalifikowana nazwa serwera usługi Azure Database | W pełni kwalifikowana nazwa serwera usługi Azure Database z poprzedniej procedury |
    | Typ uwierzytelniania | Uwierzytelnianie programu SQL Server | W chwili pisania tego samouczka jedyną dostępna opcją jest uwierzytelnianie programu SQL Server, ale usługa Azure SQL Database obsługuje także zintegrowane uwierzytelnianie usługi Active Directory i uwierzytelnianie hasłem usługi Active Directory |
    | Nazwa użytkownika | Twoja nazwa logowania | Nazwa logowania musi mieć uprawnienia **CONTROL DATABASE** (KONTROLA BAZY DANYCH) do źródłowej bazy danych |
    | Hasło| Twoje hasło | Twoje hasło |
    | Connection properties (Właściwości połączenia)| Wybierz pozycje **Encrypt connection** (Szyfruj połączenie) i **Trust server certificate** (Certyfikat serwera zaufania) odpowiednie dla danego środowiska. | Wybierz właściwości odpowiednie dla połączenia z danym serwerem |

    ![wybieranie lokalizacji docelowej dla nowej migracji danych](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-target.png)

7. Wybierz bazę danych z serwera docelowego utworzonego w poprzedniej procedurze, a następnie kliknij przycisk **Next** (Dalej), aby rozpocząć proces oceny schematu źródłowej bazy danych. W tym samouczku jest używana tylko jedna baza danych. Zwróć uwagę, że poziom zgodności dla tej bazy danych ustawiono na wartość 140. Jest to domyślny poziom zgodności dla wszystkich nowych baz danych w usłudze Azure SQL Database.

   > [!IMPORTANT] 
   > Po przeprowadzeniu migracji bazy danych do usługi Azure SQL Database możesz korzystać z tej bazy danych na określonym poziomie zgodności w celu zapewnienia zgodności z poprzednimi wersjami. Aby uzyskać więcej informacji o implikacjach i opcjach związanych z używaniem bazy danych na określonym poziomie zgodności, zobacz [ALTER DATABASE Compatibility Level](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level) (Instrukcja ALTER DATABASE — poziom zgodności). Zapoznaj się też z tematem [ALTER DATABASE SCOPED CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql), aby uzyskać informacje o dodatkowych ustawieniach na poziomie bazy danych związanych z poziomem zgodności.
   >

8. Na stronie **Select objects** (Wybieranie obiektów), po zakończeniu procesu oceny schematu źródłowej bazy danych, przejrzyj obiekty wybrane do migracji i przejrzyj obiekty zawierające problemy. Na przykład przyjrzyj się obiektowi **dbo.uspSearchCandidateResumes** pod kątem zmian zachowania **SERVERPROPERTY('LCID')** oraz obiektowi **HumanResourcesJobCandidate** pod kątem zmian wyszukiwania pełnotekstowego. 

   > [!IMPORTANT] 
   > W zależności od projektu bazy danych i projektu aplikacji w przypadku migrowania źródłowej bazy danych może być konieczne zmodyfikowanie bazy danych lub aplikacji bądź obydwu tych elementów po migracji (a w niektórych przypadkach przed migracją). Informacje o różnicach języka Transact-SQL, które mogą mieć wpływ na migrację, można znaleźć w temacie [Rozwiązywanie różnic w języku Transact-SQL podczas migracji do usługi SQL Database](sql-database-transact-sql-information.md).

     ![wybieranie obiektów i ocena nowej migracji danych](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-assessment-results.png)

9. Kliknij pozycję **Generate SQL script** (Generuj skrypt SQL), aby utworzyć skrypt obiektów schematu w źródłowej bazie danych. 
10. Przejrzyj wygenerowany skrypt, a następnie kliknij pozycję **Next issue** (Następny problem), jeśli będzie taka potrzeba, aby zapoznać się z wykrytymi problemami dotyczącymi oceny oraz z zaleceniami. Na przykład w przypadku wyszukiwania pełnotekstowego podczas uaktualniania zalecane jest przetestowanie aplikacji z wykorzystaniem funkcji pełnego tekstu. Jeśli chcesz, możesz zapisać lub skopiować skrypt.

     ![skrypt wygenerowany dla nowej migracji danych](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-generated-script.png)

11. Kliknij przycisk **Deploy schema** (Wdróż schemat) i obserwuj proces migracji schematu.

     ![migracja schematu nowej migracji danych](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-schema-migration.png)

12. Po zakończeniu migracji schematu przejrzyj wyniki w poszukiwaniu błędów, a następnie, przy założeniu, że błędów nie ma, kliknij przycisk **Migrate data** (Przeprowadź migrację danych).
13. Na stronie **Select tables** (Wybieranie tabel) przejrzyj tabele wybrane do migracji, a następnie kliknij pozycję **Start data migration** (Rozpocznij migrację danych).

     ![migracja danych nowej migracji danych](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-data-migration.png)

14. Obserwuj proces migracji.

     ![proces migracji danych nowej migracji danych](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-data-migration-process.png)

## <a name="connect-to-the-database-with-ssms"></a>Nawiązywanie połączenia z bazą danych za pomocą programu SSMS

Nawiąż połączenie z serwerem Azure SQL Database za pomocą programu [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms).

1. Otwórz program SQL Server Management Studio.

2. W oknie dialogowym **Połącz z serwerem** wprowadź następujące informacje:

   | Ustawienie       | Sugerowana wartość | Opis | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | Typ serwera | Aparat bazy danych | Ta wartość jest wymagana |
   | Nazwa serwera | W pełni kwalifikowana nazwa serwera | Nazwa może mieć taką formę: **mynewserver20170824.database.windows.net**. |
   | Authentication | Uwierzytelnianie programu SQL Server | Uwierzytelnianie SQL to jedyny typ uwierzytelniania skonfigurowany w tym samouczku. |
   | Login | Konto administratora serwera | To konto określono podczas tworzenia serwera. |
   | Hasło | Hasło konta administratora serwera | To hasło określono podczas tworzenia serwera. |

   ![łączenie z serwerem](./media/sql-database-connect-query-ssms/connect.png)

3. Kliknij przycisk **Opcje** w oknie dialogowym **Połącz z serwerem**. W sekcji **Nawiązywanie połączenia z bazą danych** wprowadź ciąg **mySampleDatabase**, aby nawiązać połączenie z tą bazą danych.

   ![nawiązywanie połączenia z bazą danych na serwerze](./media/sql-database-connect-query-ssms/options-connect-to-db.png)  

4. Kliknij przycisk **Połącz**. W programie SSMS zostanie otwarte okno Eksplorator obiektów. 

5. W Eksploratorze obiektów rozwiń pozycję **Bazy danych**, a następnie rozwiń pozycję **mySampleDatabase**, aby wyświetlić obiekty w przykładowej bazie danych.

   ![obiekty bazy danych](./media/sql-database-connect-query-ssms/connected.png)  

## <a name="change-database-properties"></a>Zmienianie właściwości bazy danych

Warstwę usługi, poziom wydajności i poziom zgodności można zmienić przy użyciu programu SQL Server Management Studio. Podczas fazy importu zalecamy importowanie do bazy danych na wyższej warstwie wydajności, co pozwoli uzyskać najlepszą wydajność, a po zakończeniu importowania zalecamy przeprowadzenie skalowania w dół w celu zaoszczędzenia pieniędzy, dopóki wszystko nie będzie gotowe do aktywnego używania zaimportowanej bazy danych. Zmiana poziomu zgodności może dać w wyniku lepszą wydajność i dostęp do najnowszych funkcji usługi Azure SQL Database. Podczas migracji starszej bazy danych jej poziom zgodności jest utrzymywany na najniższym obsługiwanym poziomie, który jest zgodny z importowaną bazą danych. Aby uzyskać więcej informacji, zobacz [Improved query performance with compatibility Level 130 in Azure SQL Database](sql-database-compatibility-level-query-performance-130.md) (Zwiększenie wydajności zapytań za pomocą poziomu zgodności 130 w usłudze Azure SQL Database).

1. W Eksploratorze obiektów kliknij prawym przyciskiem myszy pozycję **mySampleDatabase**, a następnie kliknij polecenie **Nowe zapytanie**. Zostanie otwarte okno zapytania połączone z Twoją bazą danych.

2. Wykonaj następujące polecenie, aby ustawić warstwę usług **Standardowa** i poziom wydajności **S1**.

    ```sql
    ALTER DATABASE mySampleDatabase 
    MODIFY 
        (
        EDITION = 'Standard'
        , MAXSIZE = 250 GB
        , SERVICE_OBJECTIVE = 'S1'
    );
    ```

## <a name="next-steps"></a>Następne kroki 
W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> * Tworzenie pustej bazy danych usługi Azure SQL Database w witrynie Azure Portal 
> * Tworzenie zapory na poziomie serwera w witrynie Azure Portal 
> * Importowanie bazy danych programu SQL Server do pustej bazy danych usługi Azure SQL Database za pomocą programu [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) (DMA) 
> * Zmienianie właściwości bazy danych za pomocą programu [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

Przejdź do następnego samouczka, aby dowiedzieć się, jak zabezpieczyć bazę danych.

> [!div class="nextstepaction"]
> [Zabezpieczanie usługi Azure SQL Database](sql-database-security-tutorial.md).


