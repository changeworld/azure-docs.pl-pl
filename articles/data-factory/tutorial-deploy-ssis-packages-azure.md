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
ms.date: 09/15/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: b3b180fe4b465f3e0c7de888043326fd1a43cf23
ms.sourcegitcommit: a819209a7c293078ff5377dee266fa76fd20902c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/16/2019
ms.locfileid: "71009658"
---
# <a name="provision-the-azure-ssis-integration-runtime-in-azure-data-factory"></a>Aprowizowanie środowiska Azure SSIS Integration Runtime w usłudze Azure Data Factory

Ten samouczek zawiera instrukcje dotyczące korzystania z Azure Portal w celu aprowizacji Integration Runtime platformy Azure — SQL Server Integration Services (SSIS) w Azure Data Factory (ADF). Azure-SSIS IR obsługuje uruchamianie pakietów wdrożonych w katalogu usług SSIS (SSISDB) hostowanych przez Azure SQL Database Server/wystąpienie zarządzane (model wdrażania projektu) i te wdrożone w systemach plików/udziałach plików/Azure Files (model wdrażania pakietu). Po zainicjowaniu obsługi administracyjnej Azure-SSIS IR można korzystać ze znanych narzędzi, takich jak SQL Server narzędzia Data Tools (SSDT)/SQL Server Management Studio (SSMS), a także narzędzi wiersza polecenia, takich `dtinstall`jak / / `dtutil` `dtexec`, do Wdróż i uruchom pakiety na platformie Azure. Aby uzyskać informacje koncepcyjne dotyczące środowisk Azure SSIS IR, zobacz [Omówienie środowiska Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime).

W tym samouczku wykonasz następujące czynności:

> [!div class="checklist"]
> * Tworzenie fabryki danych.
> * Aprowizowanie środowiska Azure-SSIS Integration Runtime.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Subskrypcja platformy Azure**. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).
- **Serwer Azure SQL Database (opcjonalnie)** . Jeśli nie masz jeszcze serwera bazy danych, utwórz go w Azure Portal przed rozpoczęciem pracy. ADF utworzy SSISDB na tym serwerze bazy danych. Zaleca się utworzenie serwera bazy danych w tym samym regionie platformy Azure, co środowisko Integration Runtime. Ta konfiguracja umożliwia wykonywanie dzienników wykonywania zapisu środowiska Integration Runtime w usłudze SSISDB bez przekroczenia regionów świadczenia usługi Azure. 
    - W zależności od wybranego serwera bazy danych baza danych SSISDB może zostać utworzona w Twoim imieniu jako pojedyncza baza danych, jako część elastycznej puli lub w ramach wystąpienia zarządzanego i może być dostępna w sieci publicznej lub przez dołączenie do sieci wirtualnej. Aby uzyskać wskazówki dotyczące wybierania typu serwera bazy danych do hostowania SSISDB, zobacz [porównanie Azure SQL Database pojedynczej bazie danych/elastycznej puli/wystąpienia zarządzanego](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance). Jeśli używasz serwera Azure SQL Database z punktami końcowymi usługi sieci wirtualnej/wystąpieniem zarządzanym z prywatnym punktem końcowym do hostowania SSISDB lub Wymagaj dostępu do danych lokalnych bez konfigurowania samoobsługowego środowiska IR, musisz dołączyć Azure-SSIS IR do sieci wirtualnej, Zobacz [tworzenie Azure-SSIS IR w sieci wirtualnej](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).
    - Upewnij się, że ustawienie **Zezwalaj na dostęp do usług platformy Azure** jest włączone dla serwera bazy danych. Nie ma to zastosowania w przypadku używania serwera Azure SQL Database z punktami końcowymi usługi sieci wirtualnej/wystąpieniem zarządzanym z prywatnym punktem końcowym w celu hostowania SSISDB. Aby uzyskać więcej informacji, zobacz artykuł [Secure your Azure SQL database (Zabezpieczenia bazy danych Azure SQL Database)](../sql-database/sql-database-security-tutorial.md#create-firewall-rules). Aby włączyć to ustawienie przy użyciu programu PowerShell, zobacz polecenie [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule).
    - Dodaj adres IP komputera klienckiego lub zakres adresów IP, który zawiera adres IP komputera klienckiego, na listę adres IP klienta w ustawieniach zapory dla serwera bazy danych. Aby uzyskać więcej informacji, zobacz [Reguły zapory na poziomie serwera i na poziomie bazy danych usługi Azure SQL Database](../sql-database/sql-database-firewall-configure.md).
    - Można nawiązać połączenie z serwerem bazy danych przy użyciu uwierzytelniania SQL z poświadczeniami administratora serwera lub uwierzytelnianiem Azure Active Directory (AAD) z tożsamością zarządzaną dla Twojego ADF. W przypadku drugiego rozwiązania musisz dodać tożsamość zarządzaną usługi Azure Data Factory do grupy usługi AAD z uprawnieniami dostępu do serwera bazy danych. Aby uzyskać więcej informacji na ten temat, zobacz [Tworzenie środowiska Azure-SSIS IR przy użyciu uwierzytelniania usługi AAD](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).
    - Upewnij się, że serwer bazy danych nie ma już SSISDB. Inicjowanie obsługi Azure-SSIS IR nie obsługuje korzystania z istniejącej SSISDB.

> [!NOTE]
> - Aby zapoznać się z listą regionów świadczenia usługi Azure, w których obecnie są dostępne ADF i Azure-SSIS IR, zapoznaj się z tematem obsługa systemu APD i usług [SSIS IR](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all) 

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

Aby utworzyć ADF za pośrednictwem Azure Portal, postępuj zgodnie z instrukcjami krok po kroku w artykule [Tworzenie ADF za pośrednictwem interfejsu użytkownika](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory) , a następnie wybierz pozycję **Przypnij do pulpitu nawigacyjnego** , aby umożliwić szybki dostęp po utworzeniu. 

Po utworzeniu podajnika APD Otwórz **jego stronę przeglądu** na Azure Portal i kliknij kafelek **Monitoruj & Monitor** , aby uruchomić stronę wprowadzenie na oddzielnej karcie, w której możesz kontynuować tworzenie Azure-SSIS IR.   

## <a name="create-an-azure-ssis-integration-runtime"></a>Tworzenie środowiska Azure SSIS Integration Runtime

### <a name="from-the-data-factory-overview"></a>Z poziomu omówienia usługi Data Factory

1. **Na stronie Wprowadzenie** kliknij kafelek Konfigurowanie usług **SSIS Integration Runtime** . 

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

   a. Na stronie **Tworzenie wykazu usług SSIS...** wybierz model wdrażania dla pakietów do uruchomienia na Azure-SSIS IR: Model wdrażania projektu, w którym pakiety są wdrażane do SSISDB hostowanych przez serwer bazy danych lub model wdrażania pakietu, w którym pakiety są wdrażane w systemach plików/udziałach plików/Azure Files. W przypadku zaznaczenia tego pola wyboru należy przenieść własny serwer bazy danych na host SSISDB, który zostanie utworzony w Twoim imieniu i w nim zarządzamy.
   
   b. W polu **Subskrypcja** określ subskrypcję platformy Azure, z której korzysta Twój serwer bazy danych hostujący bazę danych SSISDB. 

   c. W polu **Lokalizacja** określ lokalizację serwera bazy danych hostującego bazę danych SSISDB. Zalecamy wybranie tej samej lokalizacji co w przypadku środowiska Integration Runtime.

   d. W polu **Punkt końcowy serwera bazy danych wykazu** wybierz punkt końcowy serwera bazy danych hostującego bazę danych SSISDB. W zależności od wybranego serwera bazy danych baza danych SSISDB może zostać utworzona w Twoim imieniu jako pojedyncza baza danych, jako część elastycznej puli lub w ramach wystąpienia zarządzanego i może być dostępna w sieci publicznej lub przez dołączenie do sieci wirtualnej. Aby uzyskać wskazówki dotyczące wybierania typu serwera bazy danych do hostowania SSISDB, zobacz [porównanie Azure SQL Database pojedynczej bazie danych/elastycznej puli/wystąpienia zarządzanego](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance). W przypadku wybrania Azure SQL Database serwera z punktami końcowymi usługi sieci wirtualnej/wystąpieniem zarządzanym z prywatnym punktem końcowym w celu hostowania SSISDB lub wymagania dostępu do danych lokalnych bez konfigurowania samoobsługowego środowiska IR należy dołączyć Azure-SSIS IR do sieci wirtualnej , zobacz [tworzenie Azure-SSIS IR w sieci wirtualnej](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 

   e. Pole wyboru **Użyj uwierzytelniania usługi AAD...** służy do wyboru metody uwierzytelniania na potrzeby serwera bazy danych hostującego bazę danych SSISDB: Uwierzytelnianie SQL lub uwierzytelnianie w usłudze AAD przy użyciu tożsamości zarządzanej dla Twojego ADF. W przypadku zaznaczenia tej opcji należy dodać tożsamość zarządzaną dla Twojego PODAJNIKa do grupy usługi AAD z uprawnieniami dostępu do serwera bazy danych, zobacz [tworzenie Azure-SSIS IR z uwierzytelnianiem w usłudze AAD](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 

   f. W polu **Nazwa administratora** wprowadź nazwę użytkownika uwierzytelnienia SQL dla serwera bazy danych hostującego bazę danych SSISDB. 

   g. W polu **Hasło administratora** wprowadź hasło uwierzytelnienia SQL dla serwera bazy danych hostującego bazę danych SSISDB. 

   h. W polu **Warstwa serwera bazy danych katalogu** wybierz warstwę usługi dla serwera bazy hostującego bazę danych SSISDB: warstwa Podstawowa/Standardowa/Premium lub nazwa puli elastycznej.

   i. Kliknij pozycję **Testuj połączenie**. Jeśli test zakończył się pomyślnie, kliknij przycisk **Dalej**. 

1. Na stronie **Ustawienia zaawansowane** wykonaj następujące czynności: 

   ![Ustawienia zaawansowane](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   a. W polu **Maksymalna liczba równoległych wykonań na węzeł** wybierz maksymalną liczbę pakietów, które mogą być wykonywane jednocześnie na węzeł w klastrze środowiska Integration Runtime. Wyświetlane są tylko obsługiwane liczby pakietów. Wybierz mała liczbę, jeśli chcesz użyć więcej niż jednego rdzenia do uruchamiania pojedynczego pakietu o znacznym wykorzystaniu zasobów obliczeniowych lub pamięci. Wybierz dużą liczbę, jeśli chcesz uruchamiać małe pakiety w ramach jednego rdzenia. 

   b. Aby uzyskać **identyfikator URI sygnatury dostępu współdzielonego kontenera instalacji niestandardowej** opcjonalnie wprowadź identyfikator URI sygnatury dostępu współdzielonego kontenera magazynu Azure Storage Blob, gdzie przechowywany jest skrypt instalacji i skojarzone z nim pliki. Aby uzyskać więcej informacji, zobacz [Instalacja niestandardowa dla środowiska Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup). 

   d. Za pomocą pola wyboru **Wybierz sieć wirtualną...** określ, czy chcesz dołączyć środowisko Integration Runtime do sieci wirtualnej. Należy ją sprawdzić w przypadku używania serwera Azure SQL Database z punktami końcowymi usługi sieci wirtualnej/wystąpieniem zarządzanym z prywatnym punktem końcowym w celu hostowania SSISDB lub wymagania dostępu do danych lokalnych bez konfigurowania samodzielnego środowiska IR, zobacz [tworzenie Azure-SSIS IR w Sieć wirtualna](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 

   d. Na **skonfigurować własne** pole wyboru, wybierz, czy chcesz skonfigurować własne środowisko IR jako serwer proxy dla Azure-SSIS IR, zobacz Konfigurowanie samoobsługowego środowiska [IR jako serwera proxy](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis). 

1. Kliknij przycisk **Dalej**. 

1. Na stronie **Podsumowanie** Przejrzyj wszystkie ustawienia aprowizacji, zakładkę zalecane linki do dokumentacji, a następnie kliknij przycisk **Zakończ** , aby rozpocząć tworzenie środowiska Integration Runtime. 

   > [!NOTE]
   > Bez czasu instalacji niestandardowej ten proces powinien zostać zakończony w ciągu 5 minut.
   >
   > Jeśli używasz SSISDB, usługa ADF będzie łączyć się z serwerem bazy danych w celu przygotowania SSISDB. 
   > 
   > Podczas aprowizacji Azure-SSIS IR są również instalowane narzędzia do redystrybucyjny i pakiet Azure Feature Pack dla usług SSIS. Te składniki zapewniają łączność z plikami programu Excel/Access i różnymi źródłami danych platformy Azure, a także ze źródłami danych, które są już obsługiwane przez wbudowane składniki. Możesz również zainstalować dodatkowe składniki, zobacz [Konfiguracja niestandardowa dla Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md).

1. Na karcie **Połączenia** w razie potrzeby przełącz na opcję **Środowiska Integration Runtime**. Wybierz pozycję **Odśwież**, aby odświeżyć stan. 

   ![Stan tworzenia i przycisk „Odśwież”](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

1. Za pomocą linków w kolumnie **Akcje** możesz zatrzymać/uruchomić, edytować lub usunąć środowisko Integration Runtime. Użyj ostatniego linku, aby wyświetlić kod JSON dla środowiska Integration Runtime. Przyciski edytowania i usuwania są włączone tylko wtedy, gdy środowisko IR jest zatrzymane. 

   ![Linki w kolumnie „Akcje”](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png) 

## <a name="deploy-ssis-packages"></a>Wdrażanie pakietów usług SSIS

Jeśli używasz SSISDB, możesz wdrożyć w nim pakiety i uruchamiać je na Azure-SSIS IR przy użyciu narzędzi SSDT/SSMS, które łączą się z serwerem bazy danych za pośrednictwem swojego punktu końcowego serwera. W przypadku Azure SQL Database serwera/wystąpienia zarządzanego z publicznym punktem końcowym format punktu końcowego serwera `<server name>.database.windows.net`jest / `<server name>.public.<dns prefix>.database.windows.net,3342`odpowiednio. Jeśli nie korzystasz z programu SSISDB, możesz wdrożyć pakiety w systemach plików/udziałach plików/Azure Files i uruchamiać je na Azure-SSIS IR przy `dtinstall` użyciu / / `dtutil` `dtexec` narzędzi wiersza polecenia. Aby uzyskać więcej informacji, zobacz [wdrażanie pakietów SSIS](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server). W obu przypadkach wdrożone pakiety można również uruchomić na Azure-SSIS IR przy użyciu działania wykonywania pakietu SSIS w potokach ADF, zobacz [wywołania wykonywania pakietu SSIS jako pierwszej klasy działania ADF](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity). 

Zobacz również następujące artykuły z dokumentacji usług SSIS: 

- [Wdrażanie, uruchamianie i monitorowanie pakietów SSIS na platformie Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial) 
- [Łączenie się z usługą SSISDB na platformie Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database) 
- [Zaplanuj wykonywanie pakietów na platformie Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages) 
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