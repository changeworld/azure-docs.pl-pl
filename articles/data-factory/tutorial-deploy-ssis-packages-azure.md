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
ms.date: 10/28/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 26aa8b17917e92a0bcb2393ac3f5d69a70dceefe
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "66145082"
---
# <a name="provision-the-azure-ssis-integration-runtime-in-azure-data-factory"></a>Aprowizowanie środowiska Azure SSIS Integration Runtime w usłudze Azure Data Factory
Ten samouczek zawiera instrukcje aprowizacji środowiska Azure SSIS Integration Runtime (IR) w usłudze Azure Data Factory przy użyciu witryny Azure Portal. Następnie możesz użyć programu SQL Server Data Tools (SSDT) lub SQL Server Management Studio (SSMS) do wdrożenia pakietów usług SQL Server Integration Services (SSIS) w tym środowisku uruchomieniowym na platformie Azure. Aby uzyskać informacje koncepcyjne dotyczące środowisk Azure SSIS IR, zobacz [Omówienie środowiska Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime).

W tym samouczku wykonasz następujące czynności:

> [!div class="checklist"]
> * Tworzenie fabryki danych.
> * Aprowizowanie środowiska Azure-SSIS Integration Runtime.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Subskrypcja platformy Azure**. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/). 
- **Serwer usługi Azure SQL Database**. Jeśli nie masz jeszcze serwera bazy danych, utwórz go w witrynie Azure Portal, zanim zaczniesz. Usługa Azure Data Factory tworzy katalog usług SSIS (bazę danych SSISDB) na tym serwerze bazy danych. Zaleca się utworzenie serwera bazy danych w tym samym regionie platformy Azure, co środowisko Integration Runtime. Ta konfiguracja pozwala środowisku Integration Runtime zapisywać dzienniki wykonywania w bazie danych SSISDB bez wykraczania poza granice regionów świadczenia usług platformy Azure. 
- W zależności od wybranego serwera bazy danych baza danych SSISDB może zostać utworzona w Twoim imieniu jako pojedyncza baza danych, jako część elastycznej puli lub w ramach wystąpienia zarządzanego i może być dostępna w sieci publicznej lub przez dołączenie do sieci wirtualnej. Jeśli używasz bazy danych Azure SQL Database z punktami końcowymi usługi dla sieci wirtualnej lub wystąpieniem zarządzanym do hostowania bazy danych SSISDB lub wymagasz dostępu do danych lokalnych, należy dołączyć środowisko Azure-SSIS IR do sieci wirtualnej. Aby uzyskać więcej informacji na ten temat, zobacz [Tworzenie środowiska Azure-SSIS IR w ramach sieci wirtualnej](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 
- Upewnij się, że ustawienie **Zezwalaj na dostęp do usług platformy Azure** jest włączone dla serwera bazy danych. Nie ma to zastosowania w przypadku używania bazy danych Azure SQL Database z punktami końcowymi usługi dla sieci wirtualnej lub wystąpieniem zarządzanym do hostowania bazy danych SSISDB. Aby uzyskać więcej informacji, zobacz artykuł [Secure your Azure SQL database (Zabezpieczenia bazy danych Azure SQL Database)](../sql-database/sql-database-security-tutorial.md#create-firewall-rules). Aby włączyć to ustawienie przy użyciu programu PowerShell, zobacz [New AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule). 
- Do listy adresów IP klienta w ustawieniach zapory dla serwera bazy danych dodaj adres IP maszyny klienta lub zakres adresów IP, który zawiera adres IP maszyny klienta. Aby uzyskać więcej informacji, zobacz [Reguły zapory na poziomie serwera i na poziomie bazy danych usługi Azure SQL Database](../sql-database/sql-database-firewall-configure.md). 
- Połączenie z serwerem bazy danych możesz nawiązać za pomocą uwierzytelniania SQL wraz z poświadczeniami administratora serwera lub uwierzytelniania usługi Azure Active Directory (AAD) wraz z tożsamością zarządzaną dla usługi Azure Data Factory.  W przypadku drugiego rozwiązania musisz dodać tożsamość zarządzaną usługi Azure Data Factory do grupy usługi AAD z uprawnieniami dostępu do serwera bazy danych. Aby uzyskać więcej informacji na ten temat, zobacz [Tworzenie środowiska Azure-SSIS IR przy użyciu uwierzytelniania usługi AAD](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 
- Upewnij się, że serwer usługi Azure SQL Database nie ma katalogu usług SSIS (baza danych SSISDB). Aprowizacja środowiska Azure-SSIS IR nie obsługuje istniejącego katalogu usług SSIS. 

> [!NOTE]
> - Aby uzyskać listę regionów świadczenia usługi Azure, w których obecnie jest dostępna usługa Data Factory i środowisko Azure-SSIS Integration Runtime, zobacz [dostępność usługi ADF i środowiska SSIS IR według regionu](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all). 

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
1. W obszarze **Wersja** wybierz pozycję **V2 (wersja zapoznawcza)**. 
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

   a. W polu **Subskrypcja** określ subskrypcję platformy Azure, z której korzysta Twój serwer bazy danych hostujący bazę danych SSISDB. 

   b. W polu **Lokalizacja** określ lokalizację serwera bazy danych hostującego bazę danych SSISDB. Zalecamy wybranie tej samej lokalizacji co w przypadku środowiska Integration Runtime. 

   d. W polu **Punkt końcowy serwera bazy danych wykazu** wybierz punkt końcowy serwera bazy danych hostującego bazę danych SSISDB. W zależności od wybranego serwera bazy danych baza danych SSISDB może zostać utworzona w Twoim imieniu jako pojedyncza baza danych, jako część elastycznej puli lub w ramach wystąpienia zarządzanego i może być dostępna w sieci publicznej lub przez dołączenie do sieci wirtualnej. Aby uzyskać wskazówki dotyczące wyboru typu serwera baz danych do hostowania bazy danych SSISDB, zobacz [Porównanie pojedynczych baz danych/elastycznych pul usługi Azure SQL Database i wystąpienia zarządzanego](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance). Jeśli wybierzesz bazę danych Azure SQL Database z punktami końcowymi usługi dla sieci wirtualnej lub wystąpieniem zarządzanym do hostowania bazy danych SSISDB lub wymagasz dostępu do danych lokalnych, należy dołączyć środowisko Azure-SSIS IR do sieci wirtualnej. Zobacz [Tworzenie środowiska uruchomieniowego integracji usług SSIS w sieci wirtualnej](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 

   d. Pole wyboru **Użyj uwierzytelniania usługi AAD...** służy do wyboru metody uwierzytelniania na potrzeby serwera bazy danych hostującego bazę danych SSISDB: uwierzytelnianie SQL lub uwierzytelnianie usługi Azure Active Directory (AAD) wraz z tożsamością zarządzaną usługi Azure Data Factory (ADF). Jeśli je zaznaczysz, należy dodać tożsamość zarządzaną dla usługi ADF do grupy usługi AAD z uprawnieniami dostępu do serwera bazy danych. Zobacz [Tworzenie środowiska Azure-SSIS IR przy użyciu uwierzytelniania usługi AAD](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 

   e. W polu **Nazwa administratora** wprowadź nazwę użytkownika uwierzytelnienia SQL dla serwera bazy danych hostującego bazę danych SSISDB. 

   f. W polu **Hasło administratora** wprowadź hasło uwierzytelnienia SQL dla serwera bazy danych hostującego bazę danych SSISDB. 

   g. W polu **Warstwa serwera bazy danych katalogu** wybierz warstwę usługi dla serwera bazy hostującego bazę danych SSISDB: warstwa Podstawowa/Standardowa/Premium lub nazwa puli elastycznej. 

   h. Kliknij pozycję **Testuj połączenie**. Jeśli test zakończył się pomyślnie, kliknij przycisk **Dalej**. 

1. Na stronie **Ustawienia zaawansowane** wykonaj następujące czynności: 

   ![Ustawienia zaawansowane](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   a. W polu **Maksymalna liczba równoległych wykonań na węzeł** wybierz maksymalną liczbę pakietów, które mogą być wykonywane jednocześnie na węzeł w klastrze środowiska Integration Runtime. Wyświetlane są tylko obsługiwane liczby pakietów. Wybierz mała liczbę, jeśli chcesz użyć więcej niż jednego rdzenia do uruchamiania pojedynczego pakietu o znacznym wykorzystaniu zasobów obliczeniowych lub pamięci. Wybierz dużą liczbę, jeśli chcesz uruchamiać małe pakiety w ramach jednego rdzenia. 

   b. Aby uzyskać **identyfikator URI sygnatury dostępu współdzielonego kontenera instalacji niestandardowej** opcjonalnie wprowadź identyfikator URI sygnatury dostępu współdzielonego kontenera magazynu Azure Storage Blob, gdzie przechowywany jest skrypt instalacji i skojarzone z nim pliki. Aby uzyskać więcej informacji, zobacz [Instalacja niestandardowa dla środowiska Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup). 

   d. Za pomocą pola wyboru **Wybierz sieć wirtualną...** określ, czy chcesz dołączyć środowisko Integration Runtime do sieci wirtualnej. Należy je zaznaczyć, jeśli używasz bazy danych Azure SQL Database z punktami końcowymi usługi dla sieci wirtualnej lub wystąpieniem zarządzanym do hostowania bazy danych SSISDB lub wymagasz dostępu do danych lokalnych. Aby uzyskać więcej informacji na ten temat, zobacz [Tworzenie środowiska Azure-SSIS IR w ramach sieci wirtualnej](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 

1. Kliknij pozycję **Zakończ**, aby rozpocząć tworzenie środowiska Integration Runtime. 

   > [!IMPORTANT]
   > Ukończenie tego procesu zajmuje około 20–30 minut.
   >
   > Usługa Data Factory łączy się z serwerem usługi Azure SQL Database w celu przygotowania wykazu usług SSIS (baza danych SSISDB). 
   > 
   > Podczas aprowizowania wystąpienia środowiska Azure-SSIS IR instalowany jest również pakiet Azure Feature Pack dla usług SSIS i pakiet redystrybucyjny programu Access. Te składniki zapewniają łączność z plikami programów Excel i Access oraz z różnymi źródłami danych platformy Azure (oprócz źródeł danych obsługiwanych przez wbudowane składniki). Możesz też zainstalować dodatkowe składniki. Aby uzyskać więcej informacji, zobacz [Niestandardowa konfiguracja środowiska Azure SSIS Integration Runtime](how-to-configure-azure-ssis-ir-custom-setup.md). 

1. Na karcie **Połączenia** w razie potrzeby przełącz na opcję **Środowiska Integration Runtime**. Wybierz pozycję **Odśwież**, aby odświeżyć stan. 

   ![Stan tworzenia i przycisk „Odśwież”](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

1. Za pomocą linków w kolumnie **Akcje** możesz zatrzymać/uruchomić, edytować lub usunąć środowisko Integration Runtime. Użyj ostatniego linku, aby wyświetlić kod JSON dla środowiska Integration Runtime. Przyciski edytowania i usuwania są włączone tylko wtedy, gdy środowisko IR jest zatrzymane. 

   ![Linki w kolumnie „Akcje”](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png) 

## <a name="deploy-ssis-packages"></a>Wdrażanie pakietów usług SSIS
Teraz użyj programu SQL Server Data Tools (SSDT) lub SQL Server Management Studio (SSMS), aby wdrożyć pakiety usług SSIS na platformie Azure. Nawiąż połączenie z serwerem usługi Azure SQL Database, który hostuje wykaz usług SSIS (baza danych SSISDB). Nazwa serwera usługi Azure SQL Database ma następujący format: `<servername>.database.windows.net`. 

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

Aby dowiedzieć się więcej o dostosowywaniu środowiska Azure-SSIS Integration Runtime, przejdź do następującego artykułu: 

> [!div class="nextstepaction"]
> [Customize Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup) (Dostosowywanie środowiska Azure-SSIS IR)
