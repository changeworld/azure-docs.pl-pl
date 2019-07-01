---
title: Aprowizowanie środowiska Azure-SSIS Integration Runtime | Microsoft Docs
description: Dowiedz się, jak aprowizować środowisko Azure SSIS Integration Runtime w usłudze Azure Data Factory w celu wdrażania i uruchamiania pakietów SSIS na platformie Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: tutorial
ms.date: 06/26/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: bd2c055d2f7f1e90918cb160cfdebfe88f7f8ea4
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/01/2019
ms.locfileid: "67484785"
---
# <a name="provision-the-azure-ssis-integration-runtime-in-azure-data-factory"></a>Aprowizowanie środowiska Azure SSIS Integration Runtime w usłudze Azure Data Factory

Ten samouczek zawiera instrukcje dotyczące korzystania z witryny Azure portal do aprowizowania usługi Azure SQL Server Integration Services (SSIS) Integration Runtime (IR) w usłudze Azure Data Factory (ADF). Azure-SSIS IR obsługuje uruchamianie pakietów wdrażana w katalogu usług SSIS (SSISDB) hostowanych przez usługi Azure SQL Database server/zarządzanego wystąpienia (Model wdrażania projektu), a te wdrożone do systemów/plik udziałów/usługi Azure Files (Model wdrażania pakietu). Po aprowizacji środowiska Azure-SSIS IR możesz następnie użyć znanych narzędzi, takich jak SQL Server Data Tools (SSDT) / SQL Server Management Studio (SSMS), a polecenia narzędzia wiersza, takich jak `dtinstall` / `dtutil` / `dtexec`, wdrażanie i uruchamianie pakietów na platformie Azure. Aby uzyskać informacje koncepcyjne dotyczące środowisk Azure SSIS IR, zobacz [Omówienie środowiska Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime).

W tym samouczku wykonasz następujące czynności:

> [!div class="checklist"]
> * Tworzenie fabryki danych.
> * Aprowizowanie środowiska Azure-SSIS Integration Runtime.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Subskrypcja platformy Azure**. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).
- **Serwer bazy danych SQL Azure (opcjonalnie)** . Jeśli nie masz już serwer bazy danych, utworzyć w witrynie Azure portal przed rozpoczęciem pracy. ADF z kolei spowoduje utworzenie bazy danych SSISDB na tym serwerze bazy danych. Zaleca się utworzenie serwera bazy danych w tym samym regionie platformy Azure, co środowisko Integration Runtime. Ta konfiguracja pozwala środowiska integration runtime zapisywać dzienniki wykonywania do bazy danych SSISDB bez wykraczania poza granice regionów świadczenia usługi Azure. 
    - W zależności od wybranego serwera bazy danych baza danych SSISDB może zostać utworzona w Twoim imieniu jako pojedyncza baza danych, jako część elastycznej puli lub w ramach wystąpienia zarządzanego i może być dostępna w sieci publicznej lub przez dołączenie do sieci wirtualnej. Aby uzyskać wskazówki dotyczące wybranie typu serwera bazy danych do hostowania bazy SSISDB, zobacz [porównanie usługi Azure SQL Database pojedynczej bazy danych/elastycznej puli/zarządzanego wystąpienia](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance). Jeśli używasz serwera Azure SQL Database przy użyciu sieci wirtualnej usługi punktów końcowych/zarządzanego wystąpienia w sieci wirtualnej do hostowania bazy SSISDB, lub wymagać dostępu do danych lokalnych, musisz dołączyć środowiska Azure-SSIS IR do sieci wirtualnej, zobacz [tworzenie środowiska Azure-SSIS IR w sieci wirtualnej](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).
    - Upewnij się, że ustawienie **Zezwalaj na dostęp do usług platformy Azure** jest włączone dla serwera bazy danych. To nie jest stosowane podczas korzystania z serwera Azure SQL Database z sieci wirtualnej usługi punktów końcowych/zarządzanego wystąpienia w sieci wirtualnej do hostowania bazy SSISDB. Aby uzyskać więcej informacji, zobacz artykuł [Secure your Azure SQL database (Zabezpieczenia bazy danych Azure SQL Database)](../sql-database/sql-database-security-tutorial.md#create-firewall-rules). Aby włączyć to ustawienie przy użyciu programu PowerShell, zobacz [New AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule).
    - Dodaj adres IP maszyny klienta lub zakres adresów IP, które zawiera adres IP maszyny klienta, listy adresów IP klienta w ustawieniach zapory serwera bazy danych. Aby uzyskać więcej informacji, zobacz [Reguły zapory na poziomie serwera i na poziomie bazy danych usługi Azure SQL Database](../sql-database/sql-database-firewall-configure.md).
    - Można nawiązać połączenia z serwerem bazy danych przy użyciu uwierzytelniania SQL przy użyciu poświadczeń administratora serwera lub uwierzytelniania usługi Azure Active Directory (AAD) za pomocą tożsamości zarządzanej dla usługi ADF. W przypadku drugiego rozwiązania musisz dodać tożsamość zarządzaną usługi Azure Data Factory do grupy usługi AAD z uprawnieniami dostępu do serwera bazy danych. Aby uzyskać więcej informacji na ten temat, zobacz [Tworzenie środowiska Azure-SSIS IR przy użyciu uwierzytelniania usługi AAD](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).
    - Upewnij się, że serwer bazy danych nie ma już SSISDB. Aprowizacja środowiska IR Azure-SSIS nie obsługuje przy użyciu istniejącej bazy danych SSISDB.

> [!NOTE]
> - Aby uzyskać listę regionów świadczenia usługi Azure, w których są obecnie dostępne ADF i Azure-SSIS IR, zobacz [ADF + SSIS IR Dostępność wg regionu](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all). 

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

1. Uruchom przeglądarkę internetową **Microsoft Edge** lub **Google Chrome**. Obecnie interfejs użytkownika usługi Data Factory jest obsługiwany tylko przez przeglądarki internetowe Microsoft Edge i Google Chrome. 
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/). 
1. Wybierz pozycję **Nowy** w menu po lewej stronie, wybierz pozycję **Dane + analiza**, a następnie wybierz pozycję **Data Factory**. 

   ![Wybór usługi Data Factory w okienku „Nowy”](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)

1. Na stronie **Nowa fabryka danych** wprowadź wartość **MyAzureSsisDataFactory** w polu **Nazwa**. 

   ![Strona „Nowa fabryka danych”](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)

   Nazwa fabryki danych platformy Azure musi być *globalnie unikatowa*. Jeśli wystąpi poniższy błąd, zmień nazwę fabryki danych (np. na **&lt;twojanazwa&gt;MyAzureSsisDataFactory**) i spróbuj utworzyć ją ponownie. Artykuł [Usługa Data Factory — reguły nazewnictwa](naming-rules.md) zawiera reguły nazewnictwa artefaktów usługi Data Factory. 

   `Data factory name “MyAzureSsisDataFactory” is not available`

1. W obszarze **Subskrypcja** wybierz subskrypcję platformy Azure, w której chcesz utworzyć fabrykę danych. 
1. W obszarze **Grupa zasobów** wykonaj jedną z następujących czynności: 

   - Wybierz pozycję **Użyj istniejącej**, a następnie wybierz istniejącą grupę zasobów z listy. 
   - Wybierz pozycję **Utwórz nową**, a następnie wprowadź nazwę grupy zasobów. 

   Informacje na temat grup zasobów znajdują się w artykule [Using resource groups to manage your Azure resources](../azure-resource-manager/resource-group-overview.md) (Używanie grup zasobów do zarządzania zasobami platformy Azure). 
1. W obszarze **Wersja** wybierz pozycję **V2 (wersja zapoznawcza)** . 
1. W obszarze **Lokalizacja** wybierz lokalizację fabryki danych. Na liście są wyświetlane tylko lokalizacje obsługiwane na potrzeby tworzenia fabryk danych. 
1. Wybierz opcję **Przypnij do pulpitu nawigacyjnego**. 
1. Wybierz pozycję **Utwórz**. 
1. Na pulpicie nawigacyjnym jest widoczny następujący kafelek ze stanem **Wdrażanie fabryki danych**: 

   ![Kafelek „Wdrażanie fabryki danych”](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)

1. Po zakończeniu tworzenia zostanie wyświetlona strona **Fabryka danych**. 

   ![Strona główna fabryki danych](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)

1. Wybierz pozycję **Tworzenie i monitorowanie**, aby uruchomić interfejs użytkownika usługi Data Factory na osobnej karcie. 

## <a name="create-an-azure-ssis-integration-runtime"></a>Tworzenie środowiska Azure SSIS Integration Runtime

### <a name="from-the-data-factory-overview"></a>Z poziomu omówienia usługi Data Factory

1. Na stronie **Wprowadzenie** wybierz kafelek **Konfigurowanie środowiska SSIS Integration Runtime**. 

   ![Kafelek „Konfigurowanie środowiska SSIS Integration Runtime”](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

1. Aby poznać pozostałe kroki konfigurowania środowiska Azure-SSIS IR, zobacz sekcję [Aprowizowanie środowiska Azure SSIS Integration Runtime](#provision-an-azure-ssis-integration-runtime). 

### <a name="from-the-authoring-ui"></a>Z poziomu interfejsu użytkownika tworzenia

1. W interfejsie użytkownika usługi Azure Data Factory przejdź na kartę **Edycja**, wybierz pozycję **Połączenia**, a następnie przejdź na kartę **Środowiska Integration Runtime**, aby wyświetlić istniejące środowiska Integration Runtime w fabryce danych. 

   ![Opcje wyboru dotyczące wyświetlania istniejących środowisk IR](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. Wybierz pozycję **Nowe**, aby utworzyć środowisko Azure-SSIS IR. 

   ![Środowisko Integration Runtime za pomocą menu](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. W oknie **Konfiguracja środowiska Integration Runtime** wybierz pozycję **Migruj metodą „lift-and-shift” istniejące pakiety usług SSIS do wykonywania na platformie Azure**, a następnie wybierz przycisk **Dalej**. 

   ![Określanie typu środowiska Integration Runtime](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. Aby poznać pozostałe kroki konfigurowania środowiska Azure-SSIS IR, zobacz sekcję [Aprowizowanie środowiska Azure SSIS Integration Runtime](#provision-an-azure-ssis-integration-runtime). 

## <a name="provision-an-azure-ssis-integration-runtime"></a>Aprowizowanie środowiska Azure-SSIS Integration Runtime

1. Na stronie **Ustawienia ogólne** kreatora **konfiguracji środowiska Integration Runtime** wykonaj następujące czynności: 

   ![Ustawienia ogólne](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   a. W polu **Nazwa** wprowadź nazwę środowiska Integration Runtime. 

   b. W polu **Opis** wprowadź opis środowiska Integration Runtime. 

   d. W polu **Lokalizacja** wybierz lokalizację środowiska Integration Runtime. Wyświetlane są tylko obsługiwane lokalizacje. Zalecamy wybranie tej samej lokalizacji do hostowania bazy danych SSISDB co w przypadku serwera bazy danych. 

   d. W polu **Rozmiar węzła** wybierz rozmiar węzła w klastrze środowiska Integration Runtime. Wyświetlane są tylko obsługiwane rozmiary węzłów. Wybierz duży rozmiar węzła (skalowania w górę), jeśli chcesz uruchamiać wiele pakietów o znacznym wykorzystaniu zasób obliczeniowych lub pamięci. 

   e. W polu **Liczba węzłów** wybierz liczbę węzłów w klastrze środowiska Integration Runtime. Wyświetlane są tylko obsługiwane liczby węzłów. Wybierz duży klaster z wieloma węzłami (skalowania w poziomie), jeśli chcesz uruchamiać wiele pakietów równolegle. 

   f. W polu **Wydanie/licencja** wybierz wydanie lub licencję programu SQL Server na potrzeby środowiska Integration Runtime: Standard lub Enterprise. Wybierz wartość Enterprise, jeśli chcesz używać funkcji zaawansowanych lub Premium w swoim środowisku Integration Runtime. 

   g. W obszarze **Oszczędność pieniędzy** wybierz opcję Korzyść użycia hybrydowego platformy Azure (AHB) dla używanego środowiska Integration Runtime: Tak lub Nie. Wybierz opcję Tak, jeśli chcesz użyć własnej licencji programu SQL Server z pakietem Software Assurance, aby móc ograniczyć koszty dzięki użyciu hybrydowemu. 

   h. Kliknij przycisk **Dalej**. 

1. Na stronie **Ustawienia SQL** wykonaj następujące czynności: 

   ![Ustawienia SQL](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

   a. Na **wykazu usług SSIS tworzenie...**  pole wyboru, wybierz model wdrożenia na potrzeby pakietów do uruchamiania na środowiska IR Azure-SSIS: Pliki projektu modelu wdrażania, których pakietów są wdrażane w danych SSISDB hostowaną przez serwer bazy danych lub modelu wdrażania pakietu, gdy pakiety są wdrażane w pliku systemów/plik udziałów/platformy Azure. Możesz sprawdzić, należy przełączyć serwer bazy danych do hostowania bazy SSISDB, które firma Microsoft będzie tworzył i zarządzał w Twoim imieniu.
   
   b. W polu **Subskrypcja** określ subskrypcję platformy Azure, z której korzysta Twój serwer bazy danych hostujący bazę danych SSISDB. 

   c. W polu **Lokalizacja** określ lokalizację serwera bazy danych hostującego bazę danych SSISDB. Zalecamy wybranie tej samej lokalizacji co w przypadku środowiska Integration Runtime. 

   d. W polu **Punkt końcowy serwera bazy danych wykazu** wybierz punkt końcowy serwera bazy danych hostującego bazę danych SSISDB. W zależności od wybranego serwera bazy danych baza danych SSISDB może zostać utworzona w Twoim imieniu jako pojedyncza baza danych, jako część elastycznej puli lub w ramach wystąpienia zarządzanego i może być dostępna w sieci publicznej lub przez dołączenie do sieci wirtualnej. Aby uzyskać wskazówki dotyczące wybranie typu serwera bazy danych do hostowania bazy SSISDB, zobacz [porównanie usługi Azure SQL Database pojedynczej bazy danych/elastycznej puli/zarządzanego wystąpienia](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance). Jeśli wybierzesz serwer Azure SQL Database za pomocą punktów końcowych usługi sieci wirtualnej/zarządzanego wystąpienia w sieci wirtualnej do hostowania bazy SSISDB, lub wymagać dostępu do danych lokalnych, musisz dołączyć środowiska Azure-SSIS IR do sieci wirtualnej, zobacz [tworzenie środowiska Azure-SSIS Środowisko IR w sieci wirtualnej](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 

   e. Pole wyboru **Użyj uwierzytelniania usługi AAD...** służy do wyboru metody uwierzytelniania na potrzeby serwera bazy danych hostującego bazę danych SSISDB: Uwierzytelnianie SQL lub uwierzytelniania w usłudze AAD z zarządzaną tożsamością dla usługi ADF. Jeśli możesz sprawdzić, należy dodać tożsamości zarządzanej dla usługi ADF do grupy usługi AAD ma uprawnienia dostępu do serwera bazy danych, zobacz [utworzyć środowisko IR Azure SSIS z uwierzytelnianiem usługi AAD](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 

   f. W polu **Nazwa administratora** wprowadź nazwę użytkownika uwierzytelnienia SQL dla serwera bazy danych hostującego bazę danych SSISDB. 

   g. W polu **Hasło administratora** wprowadź hasło uwierzytelnienia SQL dla serwera bazy danych hostującego bazę danych SSISDB. 

   h. W polu **Warstwa serwera bazy danych katalogu** wybierz warstwę usługi dla serwera bazy hostującego bazę danych SSISDB: warstwa Podstawowa/Standardowa/Premium lub nazwa puli elastycznej. 

   i. Kliknij pozycję **Testuj połączenie**. Jeśli test zakończył się pomyślnie, kliknij przycisk **Dalej**. 

1. Na stronie **Ustawienia zaawansowane** wykonaj następujące czynności: 

   ![Ustawienia zaawansowane](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   a. W polu **Maksymalna liczba równoległych wykonań na węzeł** wybierz maksymalną liczbę pakietów, które mogą być wykonywane jednocześnie na węzeł w klastrze środowiska Integration Runtime. Wyświetlane są tylko obsługiwane liczby pakietów. Wybierz mała liczbę, jeśli chcesz użyć więcej niż jednego rdzenia do uruchamiania pojedynczego pakietu o znacznym wykorzystaniu zasobów obliczeniowych lub pamięci. Wybierz dużą liczbę, jeśli chcesz uruchamiać małe pakiety w ramach jednego rdzenia. 

   b. Aby uzyskać **identyfikator URI sygnatury dostępu współdzielonego kontenera instalacji niestandardowej** opcjonalnie wprowadź identyfikator URI sygnatury dostępu współdzielonego kontenera magazynu Azure Storage Blob, gdzie przechowywany jest skrypt instalacji i skojarzone z nim pliki. Aby uzyskać więcej informacji, zobacz [Instalacja niestandardowa dla środowiska Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup). 

   d. Za pomocą pola wyboru **Wybierz sieć wirtualną...** określ, czy chcesz dołączyć środowisko Integration Runtime do sieci wirtualnej. Należy sprawdzić jej serwera Azure SQL Database za pomocą sieci wirtualnej usługi punktów końcowych/zarządzanego wystąpienia w sieci wirtualnej do hostowania bazy SSISDB, lub wymagać dostępu do danych lokalnych, zobacz [utworzyć środowisko IR Azure SSIS w sieci wirtualnej](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 

1. Kliknij pozycję **Zakończ**, aby rozpocząć tworzenie środowiska Integration Runtime. 

   > [!NOTE]
   > Z wyjątkiem ilekroć instalacja niestandardowa ten proces należy wykonać w ciągu 5 minut.
   >
   > Jeśli używasz bazy danych SSISDB, usługi ADF połączy się serwer bazy danych w celu przygotowania bazy danych SSISDB. 
   > 
   > Podczas aprowizowania środowiska IR Azure-SSIS instalowane są również dostęp do dystrybucji i pakiety Azure Feature Pack dla usług SSIS. Te składniki zapewniają łączność z plikami programu Excel i Access i różnych źródeł danych na platformie Azure, oprócz źródeł danych, które już obsługiwane przez wbudowane składniki. Można także zainstalować dodatkowe składniki, zobacz [niestandardowa konfiguracja środowiska Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md).

1. Na karcie **Połączenia** w razie potrzeby przełącz na opcję **Środowiska Integration Runtime**. Wybierz pozycję **Odśwież**, aby odświeżyć stan. 

   ![Stan tworzenia i przycisk „Odśwież”](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

1. Za pomocą linków w kolumnie **Akcje** możesz zatrzymać/uruchomić, edytować lub usunąć środowisko Integration Runtime. Użyj ostatniego linku, aby wyświetlić kod JSON dla środowiska Integration Runtime. Przyciski edytowania i usuwania są włączone tylko wtedy, gdy środowisko IR jest zatrzymane. 

   ![Linki w kolumnie „Akcje”](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png) 

## <a name="deploy-ssis-packages"></a>Wdrażanie pakietów usług SSIS

Jeśli używasz bazy danych SSISDB, można wdrażać pakiety do niego i ich uruchamianie w usłudze Azure-SSIS IR, za pomocą narzędzi SSDT/SSMS, łączących się z serwerem bazy danych za pośrednictwem punktu końcowego serwera. W przypadku usługi Azure SQL Database server/wystąpienia zarządzanego z publicznym punktem końcowym, format punktu końcowego serwera jest `<server name>.database.windows.net` / `<server name>.public.<dns prefix>.database.windows.net,3342`, odpowiednio. Jeśli nie zostanie użyta baza SSISDB, można wdrożyć pakietów do pliku systemów/plik udziałów/Azure plików i uruchamiaj je na temat używania środowiska Azure-SSIS IR `dtinstall` / `dtutil` / `dtexec` narzędzia wiersza polecenia. Aby uzyskać więcej informacji, zobacz [pakietów SSIS wdrażanie](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server). W obu przypadkach można również uruchomić wdrożonych pakietów na środowisko IR Azure-SSIS przy użyciu działania wykonywania pakietów SSIS w potokach ADF, zobacz [wykonywania jako najwyższej jakości działanie usługi ADF pakietów SSIS wywołania](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity). 

Zobacz też następujące artykuły wchodzące w skład dokumentacji usług SSIS: 

- [Wdrażanie, uruchamianie i monitorowanie pakietów usług SSIS na platformie Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial) 
- [Nawiązać połączenie z bazy danych SSISDB na platformie Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database) 
- [Planowanie wykonywania pakietów na platformie Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages) 
- [Connect to on-premises data sources with Windows authentication (Łączenie z lokalnymi źródłami danych przy użyciu uwierzytelniania systemu Windows)](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności: 

> [!div class="checklist"]
> * Tworzenie fabryki danych.
> * Aprowizowanie środowiska Azure-SSIS Integration Runtime.
> * Wdrażanie pakietów usług SSIS

Aby dowiedzieć się więcej o dostosowywaniu środowiska Azure-SSIS Integration Runtime, przejdź do następującego artykułu: 

> [!div class="nextstepaction"]
> [Customize Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup) (Dostosowywanie środowiska Azure-SSIS IR)