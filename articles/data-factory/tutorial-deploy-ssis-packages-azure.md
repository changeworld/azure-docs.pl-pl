---
title: Inicjowanie obsługi platformy Azure-SSIS Integration Runtime | Microsoft Docs
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
ms.openlocfilehash: 62b11fbf19c803d54c699d3a59454677ac3423f1
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72326078"
---
# <a name="provision-the-azure-ssis-integration-runtime-in-azure-data-factory"></a>Inicjowanie obsługi platformy Azure-SSIS Integration Runtime w Azure Data Factory

Ten samouczek zawiera instrukcje dotyczące korzystania z Azure Portal w celu aprowizacji środowiska Integration Runtime (IR) SQL Server Integration Services platformy Azure w Azure Data Factory. Azure-SSIS IR obsługuje:

- Uruchamianie pakietów wdrożonych w wykazie usług SSIS (SSISDB) hostowanych przez serwer Azure SQL Database lub wystąpienie zarządzane (model wdrażania projektu).
- Uruchomione pakiety wdrożone w systemach plików, udziałach plików lub Azure Files (model wdrażania pakietu). 

Po zainicjowaniu Azure-SSIS IR można używać dobrze znanych narzędzi do wdrażania i uruchamiania pakietów na platformie Azure. Te narzędzia obejmują SQL Server narzędzia danych, SQL Server Management Studio i narzędzia wiersza polecenia, takie jak `dtinstall`, `dtutil` i `dtexec`.

Aby uzyskać informacje koncepcyjne dotyczące środowisk Azure SSIS IR, zobacz [Omówienie środowiska Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime).

W tym samouczku wykonasz następujące czynności:

> [!div class="checklist"]
> * Tworzenie fabryki danych.
> * Aprowizowanie środowiska Azure-SSIS Integration Runtime.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Subskrypcja platformy Azure**. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).
- **Serwer Azure SQL Database (opcjonalnie)** . Jeśli nie masz jeszcze serwera bazy danych, utwórz go w witrynie Azure Portal, zanim zaczniesz. Data Factory spowoduje utworzenie wystąpienia SSISDB na tym serwerze bazy danych. 

  Zaleca się utworzenie serwera bazy danych w tym samym regionie platformy Azure, co środowisko Integration Runtime. Ta konfiguracja umożliwia wykonywanie dzienników wykonywania zapisu środowiska Integration Runtime w usłudze SSISDB bez przekroczenia regionów świadczenia usługi Azure.

  Należy pamiętać o następujących kwestiach:

  - Na podstawie wybranego serwera bazy danych wystąpienie SSISDB można utworzyć w Twoim imieniu jako pojedynczą bazę danych w ramach puli elastycznej lub w wystąpieniu zarządzanym. Może być dostępna w sieci publicznej lub przez dołączenie do sieci wirtualnej. Aby uzyskać wskazówki dotyczące wybierania typu serwera bazy danych do hostowania SSISDB, zobacz [porównanie Azure SQL Database pojedynczej bazy danych, elastycznej puli i wystąpienia zarządzanego](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance). 
  
    W przypadku używania serwera Azure SQL Database z punktami końcowymi usługi sieci wirtualnej lub wystąpieniem zarządzanym z prywatnym punktem końcowym w celu hostowania SSISDB lub gdy wymagany jest dostęp do danych lokalnych bez konfigurowania samoobsługowego środowiska IR, należy dołączyć Azure-SSIS IR do wirtualnego NFS. Aby uzyskać więcej informacji, zobacz [tworzenie Azure-SSIS IR w sieci wirtualnej](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).
  - Upewnij się, że ustawienie **Zezwalaj na dostęp do usług platformy Azure** jest włączone dla serwera bazy danych. To ustawienie nie ma zastosowania w przypadku używania serwera Azure SQL Database z punktami końcowymi usługi sieci wirtualnej lub wystąpieniem zarządzanym z prywatnym punktem końcowym do hostowania SSISDB. Aby uzyskać więcej informacji, zobacz artykuł [Secure your Azure SQL database (Zabezpieczenia bazy danych Azure SQL Database)](../sql-database/sql-database-security-tutorial.md#create-firewall-rules). Aby włączyć to ustawienie przy użyciu programu PowerShell, zobacz polecenie [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule).
  - Dodaj adres IP komputera klienckiego lub zakres adresów IP, który zawiera adres IP komputera klienckiego, do listy adres IP klienta w ustawieniach zapory dla serwera bazy danych. Aby uzyskać więcej informacji, zobacz [Reguły zapory na poziomie serwera i na poziomie bazy danych usługi Azure SQL Database](../sql-database/sql-database-firewall-configure.md).
  - Można nawiązać połączenie z serwerem bazy danych przy użyciu uwierzytelniania SQL z poświadczeniami administratora serwera lub przy użyciu uwierzytelniania usługi Azure AD z zarządzaną tożsamością dla fabryki danych. Dla tych ostatnich należy dodać tożsamość zarządzaną dla fabryki danych do grupy usługi Azure AD z uprawnieniami dostępu do serwera bazy danych. Aby uzyskać więcej informacji, zobacz [tworzenie Azure-SSIS IR przy użyciu uwierzytelniania usługi Azure AD](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).
  - Upewnij się, że serwer bazy danych nie ma już wystąpienia SSISDB. Inicjowanie obsługi Azure-SSIS IR nie obsługuje korzystania z istniejącego wystąpienia SSISDB.


> [!NOTE]
> Listę regionów świadczenia usługi Azure, w których Data Factory i Azure-SSIS IR są obecnie dostępne, można znaleźć w temacie [Data Factory i SSIS IR dostępność według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all). 

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

Aby utworzyć fabrykę danych za pomocą Azure Portal, wykonaj instrukcje krok po kroku w temacie [Tworzenie fabryki danych za pomocą interfejsu użytkownika](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory). Wybierz pozycję **Przypnij do pulpitu nawigacyjnego** , aby umożliwić szybki dostęp po utworzeniu. 

Po utworzeniu fabryki danych Otwórz jej stronę przeglądu w Azure Portal. Wybierz kafelek **tworzenie & monitor** **, aby otworzyć stronę wprowadzenie** na osobnej karcie. W tym miejscu możesz kontynuować tworzenie Azure-SSIS IR.

## <a name="create-an-azure-ssis-integration-runtime"></a>Tworzenie środowiska Azure SSIS Integration Runtime

### <a name="from-the-data-factory-overview"></a>Z poziomu omówienia usługi Data Factory

1. Na stronie **Wprowadzenie** wybierz kafelek **Konfigurowanie środowiska SSIS Integration Runtime**. 

   ![Kafelek „Konfigurowanie środowiska SSIS Integration Runtime”](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

1. Aby poznać pozostałe kroki konfigurowania środowiska Azure-SSIS IR, zobacz sekcję [Aprowizowanie środowiska Azure SSIS Integration Runtime](#provision-an-azure-ssis-integration-runtime). 

### <a name="from-the-authoring-ui"></a>Z interfejsu użytkownika tworzenia

1. W interfejsie użytkownika Azure Data Factory przejdź do karty **Edycja** i wybierz pozycję **połączenia**. Następnie przejdź na kartę **Integration Runtime** , aby wyświetlić istniejące środowiska Integration Runtime w fabryce danych. 

   ![Opcje wyboru dotyczące wyświetlania istniejących środowisk IR](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. Wybierz pozycję **Nowe**, aby utworzyć środowisko Azure-SSIS IR. 

   ![Środowisko Integration Runtime za pomocą menu](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. W oknie **Konfiguracja środowiska Integration Runtime** wybierz pozycję **Migruj metodą „lift-and-shift” istniejące pakiety usług SSIS do wykonywania na platformie Azure**, a następnie wybierz przycisk **Dalej**. 

   ![Określanie typu środowiska Integration Runtime](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. Aby poznać pozostałe kroki konfigurowania środowiska Azure-SSIS IR, zobacz sekcję [Aprowizowanie środowiska Azure SSIS Integration Runtime](#provision-an-azure-ssis-integration-runtime). 

## <a name="provision-an-azure-ssis-integration-runtime"></a>Aprowizowanie środowiska Azure-SSIS Integration Runtime

1. Na stronie **Ustawienia ogólne** **konfiguracji Integration Runtime**wykonaj następujące czynności. 

   ![Ustawienia ogólne](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   a. W polu **Nazwa** wprowadź nazwę środowiska Integration Runtime. 

   b. W polu **Opis** wprowadź opis środowiska Integration Runtime. 

   d. W polu **Lokalizacja** wybierz lokalizację środowiska Integration Runtime. Wyświetlane są tylko obsługiwane lokalizacje. Zalecamy wybranie tej samej lokalizacji do hostowania bazy danych SSISDB co w przypadku serwera bazy danych. 

   d. W polu **Rozmiar węzła** wybierz rozmiar węzła w klastrze środowiska Integration Runtime. Wyświetlane są tylko obsługiwane rozmiary węzłów. Wybierz duży rozmiar węzła (skalowanie w górę), jeśli chcesz uruchomić wiele pakietów intensywnie korzystających z mocy obliczeniowej lub intensywnie korzystających z pamięci. 

   e. W polu **Liczba węzłów** wybierz liczbę węzłów w klastrze środowiska Integration Runtime. Wyświetlane są tylko obsługiwane liczby węzłów. Wybierz duży klaster z wieloma węzłami (skalowanie w poziomie), jeśli chcesz uruchomić wiele pakietów równolegle. 

   f. W polu **wydanie/licencja**wybierz wersję SQL Server dla środowiska Integration Runtime: Standard lub Enterprise. Wybierz pozycję Enterprise (przedsiębiorstwo), jeśli chcesz korzystać z funkcji zaawansowanych w środowisku Integration Runtime. 

   g. W obszarze **oszczędność pieniędzy**wybierz opcję korzyść użycia hybrydowego platformy Azure dla środowiska Integration Runtime: **tak** lub **nie**. Wybierz pozycję **tak** , jeśli chcesz uzyskać własną licencję SQL Server z programem Software Assurance, aby korzystać z oszczędności związanych z użyciem hybrydowej. 

   h. Wybierz opcję **Dalej**. 

1. Na stronie **Ustawienia SQL** wykonaj następujące czynności. 

   ![Ustawienia SQL](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

   a. Zaznacz pole wyboru **Utwórz katalog usług SSIS...** , aby wybrać model wdrażania pakietów do uruchomienia na Azure-SSIS IR. Wybierz model wdrażania projektu, w którym pakiety są wdrażane w usłudze SSISDB hostowanej przez serwer bazy danych, lub model wdrażania pakietu, w którym pakiety są wdrażane w systemach plików, udziałach plików lub Azure Files.
   
   W przypadku zaznaczenia tego pola wyboru należy przenieść własny serwer baz danych do SSISDB, który zostanie utworzony w Twoim imieniu i będzie można nim zarządzać.
   
   b. W polu **Subskrypcja** określ subskrypcję platformy Azure, z której korzysta Twój serwer bazy danych hostujący bazę danych SSISDB. 

   d. W polu **Lokalizacja** określ lokalizację serwera bazy danych hostującego bazę danych SSISDB. Zalecamy wybranie tej samej lokalizacji co w przypadku środowiska Integration Runtime.

   d. W polu **Punkt końcowy serwera bazy danych wykazu** wybierz punkt końcowy serwera bazy danych hostującego bazę danych SSISDB. 
   
   Na podstawie wybranego serwera bazy danych wystąpienie SSISDB można utworzyć w Twoim imieniu jako pojedynczą bazę danych w ramach puli elastycznej lub w wystąpieniu zarządzanym. Może być dostępna w sieci publicznej lub przez dołączenie do sieci wirtualnej. Aby uzyskać wskazówki dotyczące wybierania typu serwera bazy danych do hostowania SSISDB, zobacz [porównanie Azure SQL Database pojedynczej bazy danych, elastycznej puli i wystąpienia zarządzanego](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance).   

   W przypadku wybrania serwera Azure SQL Database z punktami końcowymi usługi sieci wirtualnej lub wystąpieniem zarządzanym z prywatnym punktem końcowym do hostowania SSISDB lub gdy wymagany jest dostęp do danych lokalnych bez konfigurowania samoobsługowego środowiska IR, należy dołączyć Azure-SSIS IR do Sieć wirtualna. Aby uzyskać więcej informacji, zobacz [tworzenie Azure-SSIS IR w sieci wirtualnej](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).

   e. Zaznacz pole wyboru **Użyj uwierzytelniania usługi AAD z zarządzaną tożsamością dla usługi ADF,** aby wybrać metodę uwierzytelniania dla serwera bazy danych na potrzeby hostowania SSISDB. Wybierz opcję uwierzytelnianie SQL lub uwierzytelnianie usługi Azure AD z zarządzaną tożsamością dla fabryki danych.

   W przypadku zaznaczenia tego pola wyboru należy dodać tożsamość zarządzaną dla fabryki danych do grupy usługi Azure AD z uprawnieniami dostępu do serwera bazy danych. Aby uzyskać więcej informacji, zobacz [tworzenie Azure-SSIS IR przy użyciu uwierzytelniania usługi Azure AD](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).
   
   f. W polu **Nazwa użytkownika administratora**wprowadź nazwę użytkownika uwierzytelniania SQL dla serwera bazy danych, który będzie hostować SSISDB. 

   g. W polu **hasło administratora**wprowadź hasło uwierzytelniania SQL dla serwera bazy danych, aby hostować SSISDB. 

   h. W polu **warstwa usługi bazy danych wykazu**wybierz warstwę usług dla serwera bazy danych, która będzie hostować SSISDB. Wybierz warstwę podstawowa, standardowa lub Premium lub wybierz nazwę puli elastycznej.

   i. Wybierz pozycję **Testuj połączenie**. Jeśli test zakończy się pomyślnie, wybierz pozycję **dalej**. 

1. Na stronie **Ustawienia zaawansowane** wykonaj następujące czynności. 

   ![Ustawienia zaawansowane](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   a. Dla opcji **Maksymalna liczba równoległych wykonań na węzeł**wybierz maksymalną liczbę pakietów do uruchomienia współbieżnie na węzeł w klastrze środowiska Integration Runtime. Wyświetlane są tylko obsługiwane liczby pakietów. Wybierz niską liczbę, jeśli chcesz użyć więcej niż jednego rdzenia do uruchamiania pojedynczego dużego pakietu, który jest obliczeniowy lub intensywnie korzystający z pamięci. Wybierz dużą liczbę, jeśli chcesz uruchomić co najmniej jeden niewielki pakiet w jednym rdzeniu. 

   b. W przypadku **identyfikatora URI sygnatury**dostępu współdzielonego niestandardowego kontenera konfiguracji opcjonalnie wprowadź identyfikator URI sygnatury usługi Azure Blob Storage, do którego są przechowywane skrypty instalacji i skojarzone pliki. Aby uzyskać więcej informacji, zobacz [Konfiguracja niestandardowa dla Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup).
   
   d. Zaznacz pole wyboru **Wybierz sieć wirtualną dla Azure-SSIS Integration Runtime, aby dołączyć i włączyć ADF do tworzenia niektórych zasobów sieciowych** , aby wybrać, czy chcesz dołączyć do środowiska Integration Runtime do sieci wirtualnej.

   Wybierz go, jeśli używasz serwera Azure SQL Database z punktami końcowymi usługi sieci wirtualnej lub wystąpieniem zarządzanym z prywatnym punktem końcowym do hostowania SSISDB lub jeśli potrzebujesz dostępu do danych lokalnych bez konfigurowania samoobsługowego środowiska IR. Aby uzyskać więcej informacji, zobacz [tworzenie Azure-SSIS IR w sieci wirtualnej](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 
   
   d. Zaznacz pole wyboru **Skonfiguruj Samoobsługowe Integration Runtime jako serwer proxy dla Azure-SSIS Integration Runtime** , aby określić, czy chcesz skonfigurować własne środowisko IR jako serwer proxy dla Azure-SSIS IR. Aby uzyskać więcej informacji, zobacz [Konfigurowanie własnego środowiska IR jako serwera proxy](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis).   

1. Wybierz opcję **Dalej**. 

1. Na stronie **Podsumowanie** Przejrzyj wszystkie ustawienia aprowizacji, zakładkę zalecane linki do dokumentacji i wybierz pozycję **Zakończ** , aby rozpocząć tworzenie środowiska Integration Runtime. 

   > [!NOTE]
   > Bez czasu instalacji niestandardowej ten proces powinien zakończyć się w ciągu 5 minut.
   >
   > Jeśli używasz SSISDB, Usługa Data Factory będzie łączyć się z serwerem bazy danych w celu przygotowania SSISDB. 
   > 
   > Podczas aprowizacji Azure-SSIS IR są również instalowane narzędzia do redystrybucyjny i pakiet Azure Feature Pack dla usług SSIS. Te składniki zapewniają łączność z plikami programu Excel, plikami dostępu i różnymi źródłami danych platformy Azure, a także ze źródłami danych, które już obsługują. Aby uzyskać informacje o innych składnikach, które można zainstalować, zobacz [Konfiguracja niestandardowa dla Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md).

1. Na karcie **Połączenia** w razie potrzeby przełącz na opcję **Środowiska Integration Runtime**. Wybierz pozycję **Odśwież**, aby odświeżyć stan. 

   ![Stan tworzenia i przycisk „Odśwież”](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

1. Za pomocą linków w kolumnie **Akcje** możesz zatrzymać/uruchomić, edytować lub usunąć środowisko Integration Runtime. Użyj ostatniego linku, aby wyświetlić kod JSON dla środowiska Integration Runtime. Przyciski edytowania i usuwania są włączone tylko wtedy, gdy środowisko IR jest zatrzymane. 

   ![Linki w kolumnie „Akcje”](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png) 

## <a name="deploy-ssis-packages"></a>Wdrażanie pakietów usług SSIS

Jeśli używasz SSISDB, możesz wdrożyć w nim pakiety i uruchamiać je na Azure-SSIS IR przy użyciu narzędzi danych SQL Server lub narzędzi SQL Server Management Studio. Narzędzia te łączą się z serwerem bazy danych za pośrednictwem swojego punktu końcowego serwera: 

- W przypadku serwera Azure SQL Database z prywatnym punktem końcowym format punktu końcowego serwera to `<server name>.database.windows.net`.
- W przypadku wystąpienia zarządzanego z prywatnym punktem końcowym format punktu końcowego serwera to `<server name>.<dns prefix>.database.windows.net`.
- W przypadku wystąpienia zarządzanego z publicznym punktem końcowym format punktu końcowego serwera to `<server name>.public.<dns prefix>.database.windows.net,3342`. 

Jeśli nie korzystasz z programu SSISDB, możesz wdrożyć pakiety w systemach plików, udziałach plików lub Azure Files. Następnie można uruchomić je na Azure-SSIS IR przy użyciu narzędzi wiersza polecenia `dtinstall`, `dtutil` i `dtexec`. Aby uzyskać więcej informacji, zobacz [wdrażanie pakietów SSIS](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server). 

W obu przypadkach wdrożone pakiety można również uruchomić na Azure-SSIS IR przy użyciu działania wykonaj pakiet SSIS w potokach Data Factory. Aby uzyskać więcej informacji, zobacz [wywołania wykonywania pakietu SSIS jako działania pierwszej klasy Data Factory](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).

Zobacz również następującą dokumentację usług SSIS: 

- [Wdrażanie, uruchamianie i monitorowanie pakietów SSIS na platformie Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial) 
- [Łączenie się z usługą SSISDB na platformie Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database) 
- [Zaplanuj wykonywanie pakietów na platformie Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages) 
- [Connect to on-premises data sources with Windows authentication (Łączenie z lokalnymi źródłami danych przy użyciu uwierzytelniania systemu Windows)](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o dostosowywaniu środowiska Azure-SSIS Integration Runtime, przejdź do następującego artykułu: 

> [!div class="nextstepaction"]
> [Dostosowywanie Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)