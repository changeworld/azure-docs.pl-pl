---
title: Wdrażanie pakietów usług SSIS na platformie Azure | Microsoft Docs
description: W tym artykule opisano sposób wdrażania pakietów usług SSIS do platformy Azure oraz tworzenia środowiska Azure SSIS Integration Runtime przy użyciu usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: hero-article
ms.date: 04/13/2018
ms.author: douglasl
ms.openlocfilehash: cc0c26d83794cfb0b398e668ae89e268901df345
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="deploy-sql-server-integration-services-packages-to-azure"></a>Wdrażanie pakietów usług SQL Server Integration Services na platformie Azure
Ten samouczek zawiera instrukcje aprowizacji środowiska Azure SSIS Integration Runtime (IR) w usłudze Azure Data Factory przy użyciu witryny Azure Portal. Następnie możesz za pomocą narzędzi SQL Server Data Tools lub programu SQL Server Management Studio wdrożyć pakiety usług SQL Server Integration Services (SSIS) w tym środowisku uruchomieniowym na platformie Azure. Aby uzyskać informacje koncepcyjne dotyczące środowisk Azure SSIS IR, zobacz [Omówienie środowiska Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime).

W tym samouczku wykonasz następujące czynności:

> [!div class="checklist"]
> * Tworzenie fabryki danych.
> * Aprowizowanie środowiska Azure-SSIS Integration Runtime.

> [!NOTE]
> Ten artykuł dotyczy wersji 2 usługi Data Factory, która jest obecnie dostępna w wersji zapoznawczej. Jeśli używasz ogólnie dostępnej wersji 1 usługi Data Factory, zobacz [dokumentację dotyczącą usługi Data Factory w wersji 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).


## <a name="prerequisites"></a>Wymagania wstępne
- **Subskrypcja platformy Azure**. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/). 
- **Serwer usługi Azure SQL Database**. Jeśli nie masz jeszcze serwera bazy danych, utwórz go w witrynie Azure Portal, zanim zaczniesz. Usługa Azure Data Factory tworzy katalog usług SSIS (bazę danych SSISDB) na tym serwerze bazy danych. Zaleca się utworzenie serwera bazy danych w tym samym regionie platformy Azure, co środowisko Integration Runtime. Ta konfiguracja pozwala środowisku Integration Runtime zapisywać dzienniki wykonywania w bazie danych SSISDB bez wykraczania poza granice regionów świadczenia usług platformy Azure. 
- Upewnij się, że ustawienie **Zezwalaj na dostęp do usług platformy Azure** jest włączone dla serwera bazy danych. Aby uzyskać więcej informacji, zobacz artykuł [Secure your Azure SQL database (Zabezpieczenia bazy danych Azure SQL)](../sql-database/sql-database-security-tutorial.md#create-a-server-level-firewall-rule-in-the-azure-portal). Aby włączyć to ustawienie za pomocą programu PowerShell, zobacz polecenie [New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule?view=azurermps-4.4.1).
- Do listy adresów IP klienta w ustawieniach zapory dla serwera bazy danych dodaj adres IP maszyny klienta lub zakres adresów IP, który zawiera adres IP maszyny klienta. Aby uzyskać więcej informacji, zobacz [Azure SQL Database server-level and database-level firewall rules (Reguły zapory na poziomie serwera i na poziomie bazy danych usługi Azure SQL Database)](../sql-database/sql-database-firewall-configure.md).
- Upewnij się, że serwer Azure SQL Database nie ma katalogu usług SSIS (baza danych SSISDB). Aprowizacja środowiska Azure-SSIS IR nie obsługuje istniejącego katalogu usług SSIS.

> [!NOTE]
> - Fabrykę danych w wersji 2 możesz utworzyć w następujących regionach: Wschodnie stany USA, Wschodnie stany USA 2, Azja Południowo-Wschodnia i Europa Zachodnia. 
> - Środowisko Azure-SSIS IR możesz utworzyć w następujących regionach: Wschodnie stany USA, Wschodnie stany USA 2, Środkowe stany USA, Zachodnie stany USA 2, Europa Północna, Europa Zachodnia, Południowe Zjednoczone Królestwo i Australia Wschodnia. 

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

1. Uruchom przeglądarkę internetową **Microsoft Edge** lub **Google Chrome**. Obecnie interfejs użytkownika usługi Data Factory jest obsługiwany tylko przez przeglądarki internetowe Microsoft Edge i Google Chrome.
2. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).    
3. Wybierz pozycję **Nowy** w menu po lewej stronie, wybierz pozycję **Dane + analiza**, a następnie wybierz pozycję **Data Factory**. 
   
   ![Wybór usługi Data Factory w okienku „Nowy”](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)
3. Na stronie **Nowa fabryka danych** wprowadź wartość **MyAzureSsisDataFactory** w polu **Nazwa**. 
      
   ![Strona „Nowa fabryka danych”](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)
 
   Nazwa fabryki danych platformy Azure musi być *globalnie unikatowa*. Jeśli wystąpi poniższy błąd, zmień nazwę fabryki danych (np. na **&lt;twojanazwa&gt;MyAzureSsisDataFactory**) i spróbuj utworzyć ją ponownie. Artykuł [Usługa Data Factory — reguły nazewnictwa](naming-rules.md) zawiera reguły nazewnictwa artefaktów usługi Data Factory.
  
   `Data factory name “MyAzureSsisDataFactory” is not available`
4. W obszarze **Subskrypcja** wybierz subskrypcję platformy Azure, w której chcesz utworzyć fabrykę danych. 
5. W obszarze **Grupa zasobów** wykonaj jedną z następujących czynności:
     
   - Wybierz pozycję **Użyj istniejącej**, a następnie wybierz istniejącą grupę zasobów z listy. 
   - Wybierz pozycję **Utwórz nową**, a następnie wprowadź nazwę grupy zasobów.   
         
   Informacje na temat grup zasobów znajdują się w artykule [Using resource groups to manage your Azure resources](../azure-resource-manager/resource-group-overview.md) (Używanie grup zasobów do zarządzania zasobami platformy Azure).  
6. W obszarze **Wersja** wybierz pozycję **V2 (wersja zapoznawcza)**.
7. W obszarze **Lokalizacja** wybierz lokalizację fabryki danych. Na liście są wyświetlane tylko lokalizacje obsługiwane na potrzeby tworzenia fabryk danych.
8. Wybierz opcję **Przypnij do pulpitu nawigacyjnego**.     
9. Wybierz pozycję **Utwórz**.
10. Na pulpicie nawigacyjnym jest widoczny następujący kafelek ze stanem **Wdrażanie fabryki danych**: 

   ![Kafelek „Wdrażanie fabryki danych”](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)
11. Po zakończeniu tworzenia zostanie wyświetlona strona **Fabryka danych**.
   
   ![Strona główna fabryki danych](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)
12. Wybierz pozycję **Tworzenie i monitorowanie**, aby uruchomić interfejs użytkownika usługi Data Factory na osobnej karcie. 

## <a name="provision-an-azure-ssis-integration-runtime"></a>Aprowizowanie środowiska Azure-SSIS Integration Runtime

1. Na stronie **Wprowadzenie** wybierz kafelek **Konfigurowanie środowiska SSIS Integration Runtime**. 

   ![Kafelek „Konfigurowanie środowiska SSIS Integration Runtime”](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)
2. Na stronie **Ustawienia ogólne** kreatora **konfiguracji środowiska Integration Runtime** wykonaj następujące czynności: 

   ![Ustawienia ogólne](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   a. W polu **Nazwa** określ nazwę środowiska Integration Runtime.

   b. W polu **Lokalizacja** wybierz lokalizację środowiska Integration Runtime. Wyświetlane są tylko obsługiwane lokalizacje.

   d. W polu **Rozmiar węzła** wybierz rozmiar węzła, który ma zostać skonfigurowany ze środowiskiem uruchomieniowym usług SSIS.

   d. W polu **Liczba węzłów** określ liczbę węzłów w klastrze.
   
   e. Wybierz opcję **Dalej**. 
3. Na stronie **Ustawienia SQL** wykonaj następujące czynności: 

   ![Ustawienia SQL](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

   a. W polu **Subskrypcja** określ subskrypcję platformy Azure, która ma serwer bazy danych platformy Azure.

   b. W polu **Punkt końcowy serwera bazy danych katalogu** wybierz serwer bazy danych platformy Azure.

   d. W polu **Nazwa użytkownika administratora** wprowadź nazwę użytkownika administratora.

   d. W polu **Hasło administratora** wprowadź hasło dla administratora.

   e. W polu **Warstwa serwera bazy danych katalogu** wybierz warstwę usługi dla serwera bazy danych katalogu. Wartość domyślna to **Podstawowa**.

   f. Wybierz opcję **Dalej**. 
4. Na stronie **Ustawienia zaawansowane** wybierz wartość dla ustawienia **Maksymalna liczba równoległych wykonań na węzeł**.   

   ![Ustawienia zaawansowane](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)    
5. Ten krok jest *opcjonalny*. Jeśli masz sieć wirtualną (utworzoną za pośrednictwem klasycznego modelu wdrażania lub za pośrednictwem usługi Azure Resource Manager), do której chcesz dołączyć środowisko Integration Runtime, zaznacz pole wyboru **Wybierz sieć wirtualną dla środowiska Azure-SSIS Integration Runtime do dołączenia i zezwalaj usługom platformy Azure na konfigurowanie uprawnień/ustawień sieci wirtualnej**. Następnie wykonaj następujące czynności: 

   ![Ustawienia zaawansowane sieci wirtualnej](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)    

   a. W polu **Subskrypcja** określ subskrypcję, która ma sieć wirtualną.

   b. W polu **Nazwa sieci wirtualnej** wybierz nazwę sieci wirtualnej.

   d. W polu **Nazwa podsieci** wybierz nazwę podsieci w sieci wirtualnej. 
6. Wybierz pozycję **Zakończ**, aby rozpocząć tworzenie środowiska Azure-SSIS Integration Runtime. 

   > [!IMPORTANT]
   > Ukończenie tego procesu zajmuje około 20 minut.
   >
   > Usługa Data Factory łączy się z bazą danych SQL Azure w celu przygotowania katalogu usług SSIS (bazy danych SSISDB). Skrypt konfiguruje także uprawnienia i ustawienia sieci wirtualnej, jeśli je określono. Ponadto przyłącza nowe wystąpienie środowiska Azure SSIS Integration Runtime do sieci wirtualnej.
   > 
   > Podczas aprowizowania wystąpienia środowiska Azure-SSIS IR instalowany jest również pakiet Azure Feature Pack dla usług SSIS i pakiet redystrybucyjny programu Access. Te składniki zapewniają łączność z plikami programów Excel i Access oraz z różnymi źródłami danych platformy Azure (oprócz źródeł danych obsługiwanych przez wbudowane składniki). Możesz też zainstalować dodatkowe składniki. Aby uzyskać więcej informacji, zobacz [Niestandardowa konfiguracja środowiska Azure SSIS Integration Runtime](how-to-configure-azure-ssis-ir-custom-setup.md).

7. Na karcie **Połączenia** w razie potrzeby przełącz na opcję **Środowiska Integration Runtime**. Wybierz pozycję **Odśwież**, aby odświeżyć stan. 

   ![Stan tworzenia i przycisk „Odśwież”](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)
8. Za pomocą linków w kolumnie **Akcje** możesz zatrzymać/uruchomić, edytować lub usunąć środowisko Integration Runtime. Użyj ostatniego linku, aby wyświetlić kod JSON dla środowiska Integration Runtime. Przyciski edytowania i usuwania są włączone tylko wtedy, gdy środowisko IR jest zatrzymane. 

   ![Linki w kolumnie „Akcje”](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)        

## <a name="create-an-azure-ssis-integration-runtime"></a>Tworzenie środowiska Azure SSIS Integration Runtime

1. W interfejsie użytkownika usługi Azure Data Factory przejdź na kartę **Edycja**, wybierz pozycję **Połączenia**, a następnie przejdź na kartę **Środowiska Integration Runtime**, aby wyświetlić istniejące środowiska Integration Runtime w fabryce danych. 
   ![Opcje wyboru dotyczące wyświetlania istniejących środowisk IR](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)
2. Wybierz pozycję **Nowe**, aby utworzyć środowisko Azure-SSIS IR. 

   ![Środowisko Integration Runtime za pomocą menu](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)
3. W oknie **Konfiguracja środowiska Integration Runtime** wybierz pozycję **Migruj metodą „lift-and-shift” istniejące pakiety usług SSIS do wykonywania na platformie Azure**, a następnie wybierz przycisk **Dalej**.

   ![Określanie typu środowiska Integration Runtime](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)
4. Aby poznać pozostałe kroki konfigurowania środowiska Azure-SSIS IR, zobacz sekcję [Aprowizowanie środowiska Azure SSIS Integration Runtime](#provision-an-azure-ssis-integration-runtime). 

 
## <a name="deploy-ssis-packages"></a>Wdrażanie pakietów usług SSIS
Teraz za pomocą narzędzi SQL Server Data Tools lub programu SQL Server Management Studio wdróż pakiety usług SSIS na platformie Azure. Nawiąż połączenie z serwerem bazy danych platformy Azure, który hostuje katalog usług SSIS (bazę danych SSISDB). Nazwa serwera bazy danych platformy Azure ma format `<servername>.database.windows.net` (dla usługi Azure SQL Database). 

Zobacz następujące artykuły wchodzące w skład dokumentacji usług SSIS: 

- [Deploy, run, and monitor an SSIS package on Azure (Wdrażanie, uruchamianie i monitorowanie pakietu usług SSIS na platformie Azure)](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Connect to SSIS catalog on Azure (Łączenie z katalogiem usług SSIS na platformie Azure)](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Schedule package execution on Azure (Planowanie wykonywania pakietów na platformie Azure)](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [Connect to on-premises data sources with Windows authentication (Łączenie z lokalnymi źródłami danych przy użyciu uwierzytelniania systemu Windows)](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="next-steps"></a>Następne kroki
W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności: 

> [!div class="checklist"]
> * Tworzenie fabryki danych.
> * Aprowizowanie środowiska Azure-SSIS Integration Runtime.

Aby dowiedzieć się więcej o kopiowaniu danych ze środowiska lokalnego do chmury, przejdź do następującego samouczka: 

> [!div class="nextstepaction"]
> [Kopiowanie danych w chmurze](tutorial-copy-data-portal.md)
