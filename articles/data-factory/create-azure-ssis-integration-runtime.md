---
title: Tworzenie środowiska Azure-SSIS Integration Runtime w Azure Data Factory
description: Dowiedz się, jak utworzyć środowisko Azure-SSIS Integration Runtime w Azure Data Factory, aby móc wdrażać i uruchamiać pakiety usług SSIS na platformie Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/15/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: ce688248a205981f4a4c60ad01231c0b8f6bae3d
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73677354"
---
# <a name="create-an-azure-ssis-integration-runtime-in-azure-data-factory"></a>Tworzenie środowiska Azure-SSIS Integration Runtime w Azure Data Factory

W tym artykule przedstawiono procedurę aprowizacji środowiska Integration Runtime (IR) platformy Azure — SQL Server Integration Services (SSIS) w programie Azure Data Factory. Azure-SSIS IR obsługuje:

- Uruchamianie pakietów wdrożonych w wykazie usług SSIS (SSISDB) hostowanych przez serwer Azure SQL Database lub wystąpienie zarządzane (model wdrażania projektu).
- Uruchomione pakiety wdrożone w systemach plików, udziałach plików lub Azure Files (model wdrażania pakietu). 

Po zainicjowaniu Azure-SSIS IR można używać dobrze znanych narzędzi do wdrażania i uruchamiania pakietów na platformie Azure. Te narzędzia obejmują SQL Server narzędzia danych, SQL Server Management Studio i narzędzia wiersza polecenia, takie jak `dtinstall`, `dtutil`i `dtexec`.

W samouczku [Azure-SSIS IR aprowizacji](tutorial-create-azure-ssis-runtime-portal.md) przedstawiono sposób tworzenia Azure-SSIS IR za pomocą aplikacji Azure Portal lub Data Factory. W tym samouczku pokazano również, jak opcjonalnie użyć serwera Azure SQL Database lub wystąpienia zarządzanego do hostowania SSISDB. Ten artykuł rozszerza się w samouczku i opisuje, jak wykonać te opcjonalne zadania:

- Użyj serwera Azure SQL Database z punktami końcowymi usługi sieci wirtualnej lub wystąpieniem zarządzanym z prywatnym punktem końcowym do hostowania SSISDB. Jako wymaganie wstępne należy skonfigurować uprawnienia i ustawienia sieci wirtualnej dla Azure-SSIS IR, aby dołączyć do sieci wirtualnej.

- Użyj uwierzytelniania Azure Active Directory (Azure AD) z zarządzaną tożsamością dla fabryki danych, aby nawiązać połączenie z serwerem Azure SQL Database lub wystąpieniem zarządzanym. Jako warunek wstępny należy dodać tożsamość zarządzaną dla fabryki danych jako użytkownika bazy danych, który może utworzyć wystąpienie SSISDB.

- Dołącz do Azure-SSIS IR do sieci wirtualnej lub skonfiguruj własne środowisko IR jako serwer proxy dla Azure-SSIS IR dostępu do danych lokalnych.

W tym artykule pokazano, jak zainicjować obsługę Azure-SSIS IR przy użyciu Azure Portal, Azure PowerShell i szablonu Azure Resource Manager.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Subskrypcja platformy Azure**. Jeśli nie masz jeszcze subskrypcji, możesz utworzyć [bezpłatne konto wersji próbnej](https://azure.microsoft.com/pricing/free-trial/) .
- **Serwer Azure SQL Database lub wystąpienie zarządzane (opcjonalnie)** . Jeśli nie masz jeszcze serwera bazy danych, utwórz go w witrynie Azure Portal, zanim zaczniesz. Data Factory spowoduje utworzenie wystąpienia SSISDB na tym serwerze bazy danych. 

  Zaleca się utworzenie serwera bazy danych w tym samym regionie platformy Azure, co środowisko Integration Runtime. Ta konfiguracja umożliwia wykonywanie dzienników wykonywania zapisu środowiska Integration Runtime w usłudze SSISDB bez przekroczenia regionów świadczenia usługi Azure.

  Należy pamiętać o następujących kwestiach:

  - Na podstawie wybranego serwera bazy danych wystąpienie SSISDB można utworzyć w Twoim imieniu jako pojedynczą bazę danych w ramach puli elastycznej lub w wystąpieniu zarządzanym. Może być dostępna w sieci publicznej lub przez dołączenie do sieci wirtualnej. Aby uzyskać wskazówki dotyczące wybierania typu serwera bazy danych do hostowania SSISDB, zapoznaj się z sekcją [porównanie Azure SQL Database pojedynczej bazy danych, elastycznej puli i wystąpienia zarządzanego](#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance) w tym artykule. 
  
    W przypadku używania serwera Azure SQL Database z punktami końcowymi usługi sieci wirtualnej lub wystąpieniem zarządzanym z prywatnym punktem końcowym w celu hostowania SSISDB lub gdy wymagany jest dostęp do danych lokalnych bez konfigurowania samoobsługowego środowiska IR, należy dołączyć Azure-SSIS IR do wirtualnego NFS. Aby uzyskać więcej informacji, zobacz [Dołączanie Azure-SSIS IR do sieci wirtualnej](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).
  - Upewnij się, że ustawienie **Zezwalaj na dostęp do usług platformy Azure** jest włączone dla serwera bazy danych. To ustawienie nie ma zastosowania w przypadku używania serwera Azure SQL Database z punktami końcowymi usługi sieci wirtualnej lub wystąpieniem zarządzanym z prywatnym punktem końcowym do hostowania SSISDB. Aby uzyskać więcej informacji, zobacz artykuł [Secure your Azure SQL database (Zabezpieczenia bazy danych Azure SQL Database)](../sql-database/sql-database-security-tutorial.md#create-firewall-rules). Aby włączyć to ustawienie przy użyciu programu PowerShell, zobacz polecenie [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule).
  - Dodaj adres IP komputera klienckiego lub zakres adresów IP, który zawiera adres IP komputera klienckiego, do listy adres IP klienta w ustawieniach zapory dla serwera bazy danych. Aby uzyskać więcej informacji, zobacz [Reguły zapory na poziomie serwera i na poziomie bazy danych usługi Azure SQL Database](../sql-database/sql-database-firewall-configure.md).
  - Można nawiązać połączenie z serwerem bazy danych przy użyciu uwierzytelniania SQL z poświadczeniami administratora serwera lub przy użyciu uwierzytelniania usługi Azure AD z zarządzaną tożsamością dla fabryki danych. Dla tych ostatnich należy dodać tożsamość zarządzaną dla fabryki danych do grupy usługi Azure AD z uprawnieniami dostępu do serwera bazy danych. Aby uzyskać więcej informacji, zobacz [Włączanie uwierzytelniania usługi Azure AD dla Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir).
  - Upewnij się, że serwer bazy danych nie ma już wystąpienia SSISDB. Inicjowanie obsługi Azure-SSIS IR nie obsługuje korzystania z istniejącego wystąpienia SSISDB.
- **Azure Resource Manager sieci wirtualnej (opcjonalnie)** . Należy mieć Azure Resource Manager sieci wirtualnej, jeśli co najmniej jeden z następujących warunków jest spełniony:
    - Hosting SSISDB na serwerze Azure SQL Database za pomocą punktów końcowych usługi sieci wirtualnej lub wystąpienia zarządzanego z prywatnym punktem końcowym.
    - Chcesz połączyć się z lokalnymi magazynami danych z pakietów SSIS uruchomionych na Azure-SSIS IR bez konfigurowania własnego środowiska IR.
- **Azure PowerShell (opcjonalnie)** . Postępuj zgodnie z instrukcjami w temacie [jak zainstalować i skonfigurować Azure PowerShell](/powershell/azure/install-az-ps), jeśli chcesz uruchomić skrypt programu PowerShell w celu aprowizacji Azure-SSIS IR.

### <a name="regional-support"></a>Obsługa regionalna

Aby uzyskać listę regionów świadczenia usługi Azure, w których Data Factory i Azure-SSIS IR są dostępne, zobacz [Data Factory i SSIS IR dostępność według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all).

### <a name="comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance"></a>Porównanie SQL Database pojedynczej bazy danych, elastycznej puli i wystąpienia zarządzanego

Poniższa tabela zawiera porównanie niektórych funkcji serwera Azure SQL Database i wystąpienia zarządzanego, które są powiązane z usługą Azure-SSIR IR:

| Funkcja | Pojedyncza baza danych/Pula elastyczna| Wystąpienie zarządzane |
|---------|--------------|------------------|
| **Harmonogram** | Agent SQL Server jest niedostępny.<br/><br/>Zobacz [Planowanie wykonywania pakietu w potoku Data Factory](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages?view=sql-server-2017#activity).| Agent wystąpienia zarządzanego jest dostępny. |
| **Uwierzytelnianie** | Można utworzyć wystąpienie SSISDB z użytkownikiem zawartej bazy danych, który reprezentuje dowolną grupę usługi Azure AD z zarządzaną tożsamością fabryki danych jako członkiem roli **db_owner** .<br/><br/>Zobacz [Włączanie uwierzytelniania usługi Azure AD, aby utworzyć wystąpienie SSISDB na serwerze Azure SQL Database](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database). | Można utworzyć wystąpienie SSISDB z użytkownikiem zawartej bazy danych, który reprezentuje zarządzaną tożsamość fabryki danych. <br/><br/>Zobacz [Włączanie uwierzytelniania usługi Azure AD, aby utworzyć wystąpienie SSISDB w wystąpieniu zarządzanym Azure SQL Database](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database-managed-instance). |
| **Warstwa usług** | Po utworzeniu Azure-SSIS IR z serwerem Azure SQL Database można wybrać warstwę usług dla SSISDB. Istnieje wiele warstw usług. | Po utworzeniu Azure-SSIS IR z wystąpieniem zarządzanym nie można wybrać warstwy usług dla SSISDB. Wszystkie bazy danych w wystąpieniu zarządzanym współdzielą ten sam zasób przydzielony do tego wystąpienia. |
| **Sieć wirtualna** | Azure-SSIS IR można dołączać do Azure Resource Manager sieci wirtualnych tylko wtedy, gdy używany jest serwer Azure SQL Database z punktami końcowymi usługi sieci wirtualnej lub jeśli wymagany jest dostęp do lokalnych magazynów danych bez konfigurowania samoobsługowego środowiska IR. | Azure-SSIS IR można przyłączyć do Azure Resource Manager sieci wirtualnych. Sieć wirtualna jest wymagana, gdy nie zostanie włączony publiczny punkt końcowy dla wystąpienia zarządzanego.<br/><br/>Jeśli dołączysz Azure-SSIS IR do tej samej sieci wirtualnej co wystąpienie zarządzane, upewnij się, że Azure-SSIS IR znajduje się w innej podsieci od wystąpienia zarządzanego. Jeśli dołączysz Azure-SSIS IR do innej sieci wirtualnej z wystąpienia zarządzanego, zalecamy komunikację równorzędną sieci wirtualnej lub połączenie sieciowe-sieciowe. Zobacz [łączenie aplikacji z wystąpieniem zarządzanym Azure SQL Database](../sql-database/sql-database-managed-instance-connect-app.md). |
| **Transakcje rozproszone** | Ta funkcja jest obsługiwana przez transakcje elastyczne. Transakcje Microsoft Distributed Transaction Coordinator (MSDTC) nie są obsługiwane. Jeśli pakiety SSIS używają usługi MSDTC do koordynowania transakcji rozproszonych, rozważ migrację do transakcji elastycznych dla Azure SQL Database. Aby uzyskać więcej informacji, zobacz [transakcje rozproszone w bazach danych w chmurze](../sql-database/sql-database-elastic-transactions-overview.md). | Nieobsługiwane. |
| | | |

## <a name="use-the-azure-portal-to-create-an-integration-runtime"></a>Tworzenie środowiska Integration Runtime za pomocą Azure Portal

W tej sekcji użyjesz Azure Portal, w tym interfejsu użytkownika Data Factory (UI) lub aplikacji, aby utworzyć Azure-SSIS IR.

### <a name="create-a-data-factory"></a>Tworzenie fabryki danych

Aby utworzyć fabrykę danych za pomocą Azure Portal, wykonaj instrukcje krok po kroku w temacie [Tworzenie fabryki danych za pomocą interfejsu użytkownika](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory). Wybierz pozycję **Przypnij do pulpitu nawigacyjnego** , aby umożliwić szybki dostęp po utworzeniu. 

Po utworzeniu fabryki danych Otwórz jej stronę przeglądu w Azure Portal. Wybierz kafelek **tworzenie & monitor** , aby **otworzyć stronę wprowadzenie** na osobnej karcie. W tym miejscu możesz kontynuować tworzenie Azure-SSIS IR.   

### <a name="provision-an-azure-ssis-integration-runtime"></a>Aprowizowanie środowiska Azure-SSIS Integration Runtime

1. Na stronie **Wprowadzenie** wybierz kafelek **Konfigurowanie środowiska SSIS Integration Runtime**.

   ![Kafelek Konfigurowanie środowiska SSIS Integration Runtime](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

2. Na stronie **Ustawienia ogólne** **konfiguracji Integration Runtime**wykonaj następujące czynności.

   ![Ustawienia ogólne](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

    a. W polu **Nazwa** wprowadź nazwę środowiska Integration Runtime.

    b. W polu **Opis** wprowadź opis środowiska Integration Runtime.

    d. W polu **Lokalizacja** wybierz lokalizację środowiska Integration Runtime. Wyświetlane są tylko obsługiwane lokalizacje. Zalecamy wybranie tej samej lokalizacji do hostowania bazy danych SSISDB co w przypadku serwera bazy danych.

    d. W polu **rozmiar węzła**wybierz rozmiar węzła w klastrze środowiska Integration Runtime. Wyświetlane są tylko obsługiwane rozmiary węzłów. Wybierz duży rozmiar węzła (skalowanie w górę), jeśli chcesz uruchomić wiele pakietów intensywnie korzystających z mocy obliczeniowej lub intensywnie korzystających z pamięci.

    e. W polu **Liczba węzłów** wybierz liczbę węzłów w klastrze środowiska Integration Runtime. Wyświetlane są tylko obsługiwane liczby węzłów. Wybierz duży klaster z wieloma węzłami (skalowanie w poziomie), jeśli chcesz uruchomić wiele pakietów równolegle.

    f. W polu **wydanie/licencja**wybierz wersję SQL Server dla środowiska Integration Runtime: Standard lub Enterprise. Wybierz pozycję Enterprise (przedsiębiorstwo), jeśli chcesz korzystać z funkcji zaawansowanych w środowisku Integration Runtime.

    g. W obszarze **oszczędność pieniędzy**wybierz opcję korzyść użycia hybrydowego platformy Azure dla środowiska Integration Runtime: **tak** lub **nie**. Wybierz pozycję **tak** , jeśli chcesz uzyskać własną licencję SQL Server z programem Software Assurance, aby korzystać z oszczędności związanych z użyciem hybrydowej.

    h. Wybierz opcję **Dalej**.

3. Na stronie **Ustawienia SQL** wykonaj następujące czynności.

   ![Ustawienia SQL](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

   a. Zaznacz pole wyboru **Utwórz katalog usług SSIS...** , aby wybrać model wdrażania pakietów do uruchomienia na Azure-SSIS IR. Wybierz model wdrażania projektu, w którym pakiety są wdrażane w usłudze SSISDB hostowanej przez serwer bazy danych, lub model wdrażania pakietu, w którym pakiety są wdrażane w systemach plików, udziałach plików lub Azure Files. 
    
   Jeśli zaznaczysz to pole wyboru, musisz przełączyć własny serwer bazy danych, aby obsługiwał wystąpienie SSISDB, które utworzymy i zarządzamy w Twoim imieniu.
   
   b. W polu **Subskrypcja** określ subskrypcję platformy Azure, z której korzysta Twój serwer bazy danych hostujący bazę danych SSISDB. 

   d. W polu **Lokalizacja** określ lokalizację serwera bazy danych hostującego bazę danych SSISDB. Zalecamy wybranie tej samej lokalizacji co w przypadku środowiska Integration Runtime. 

   d. W polu **Punkt końcowy serwera bazy danych wykazu** wybierz punkt końcowy serwera bazy danych hostującego bazę danych SSISDB. 
    
   Na podstawie wybranego serwera bazy danych wystąpienie SSISDB można utworzyć w Twoim imieniu jako pojedynczą bazę danych w ramach puli elastycznej lub w wystąpieniu zarządzanym. Może być dostępna w sieci publicznej lub przez dołączenie do sieci wirtualnej. Aby uzyskać wskazówki dotyczące wybierania typu serwera bazy danych do hostowania SSISDB, zapoznaj się z sekcją [porównanie Azure SQL Database pojedynczej bazy danych, elastycznej puli i wystąpienia zarządzanego](#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance) w tym artykule. 
    
   W przypadku wybrania serwera Azure SQL Database z punktami końcowymi usługi sieci wirtualnej lub wystąpieniem zarządzanym z prywatnym punktem końcowym do hostowania SSISDB lub gdy wymagany jest dostęp do danych lokalnych bez konfigurowania samoobsługowego środowiska IR, należy dołączyć Azure-SSIS IR do Sieć wirtualna. Aby uzyskać więcej informacji, zobacz [Dołączanie Azure-SSIS IR do sieci wirtualnej](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). 

   e. Zaznacz pole wyboru **Użyj uwierzytelniania usługi AAD z zarządzaną tożsamością dla usługi ADF,** aby wybrać metodę uwierzytelniania dla serwera bazy danych na potrzeby hostowania SSISDB. Wybierz opcję uwierzytelnianie SQL lub uwierzytelnianie usługi Azure AD z zarządzaną tożsamością dla fabryki danych. 
    
   W przypadku zaznaczenia tego pola wyboru należy dodać tożsamość zarządzaną dla fabryki danych do grupy usługi Azure AD z uprawnieniami dostępu do serwera bazy danych. Aby uzyskać więcej informacji, zobacz [Włączanie uwierzytelniania usługi Azure AD dla Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir). 

   f. W polu **Nazwa użytkownika administratora**wprowadź nazwę użytkownika uwierzytelniania SQL dla serwera bazy danych, który będzie hostować SSISDB. 

   g. W polu **hasło administratora**wprowadź hasło uwierzytelniania SQL dla serwera bazy danych, aby hostować SSISDB. 

   h. W polu **warstwa usługi bazy danych wykazu**wybierz warstwę usług dla serwera bazy danych, która będzie hostować SSISDB. Wybierz warstwę podstawowa, standardowa lub Premium lub wybierz nazwę puli elastycznej. 

   i. Wybierz pozycję **Testuj połączenie**. Jeśli test zakończy się pomyślnie, wybierz pozycję **dalej**. 

4. Na stronie **Ustawienia zaawansowane** wykonaj następujące czynności.

   ![Ustawienia zaawansowane](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   a. Dla opcji **Maksymalna liczba równoległych wykonań na węzeł**wybierz maksymalną liczbę pakietów do uruchomienia współbieżnie na węzeł w klastrze środowiska Integration Runtime. Wyświetlane są tylko obsługiwane liczby pakietów. Wybierz niską liczbę, jeśli chcesz użyć więcej niż jednego rdzenia do uruchamiania pojedynczego dużego pakietu, który jest obliczeniowy lub intensywnie korzystający z pamięci. Wybierz dużą liczbę, jeśli chcesz uruchomić co najmniej jeden niewielki pakiet w jednym rdzeniu.

   b. W przypadku **identyfikatora URI sygnatury**dostępu współdzielonego niestandardowego kontenera konfiguracji opcjonalnie wprowadź identyfikator URI sygnatury usługi Azure Blob Storage, do którego są przechowywane skrypty instalacji i skojarzone pliki. Aby uzyskać więcej informacji, zobacz [Konfiguracja niestandardowa dla Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup).

5. Zaznacz pole wyboru **Wybierz sieć wirtualną dla Azure-SSIS Integration Runtime, aby dołączyć i włączyć ADF do tworzenia niektórych zasobów sieciowych** , aby wybrać, czy chcesz dołączyć do środowiska Integration Runtime do sieci wirtualnej. 

   Wybierz go, jeśli używasz serwera Azure SQL Database z punktami końcowymi usługi sieci wirtualnej lub wystąpieniem zarządzanym z prywatnym punktem końcowym do hostowania SSISDB, lub jeśli potrzebujesz dostępu do danych lokalnych. (Oznacza to, że masz lokalne źródła danych lub miejsca docelowe w pakietach SSIS, bez konfigurowania własnego środowiska IR). Aby uzyskać więcej informacji, zobacz [Dołączanie Azure-SSIS IR do sieci wirtualnej](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). 

   Jeśli zaznaczysz pole wyboru, wykonaj następujące czynności.

   ![Ustawienia zaawansowane sieci wirtualnej](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

   a. W obszarze **subskrypcja**wybierz subskrypcję platformy Azure, która ma swoją sieć wirtualną.

   b. W polu **Lokalizacja**jest wybierana ta sama lokalizacja środowiska Integration Runtime.

   d. W polu **Typ**wybierz typ sieci wirtualnej: klasyczny lub Azure Resource Manager. Zalecamy wybranie Azure Resource Manager sieci wirtualnej, ponieważ klasyczne sieci wirtualne będą wkrótce przestarzałe.

   d. W polu **Nazwa**sieci wirtualnej wybierz nazwę swojej usługi. Ta sieć wirtualna powinna być taka sama, jak ta, która jest używana przez serwer Azure SQL Database z punktami końcowymi usługi sieci wirtualnej lub wystąpieniem zarządzanym w sieci wirtualnej do hostowania SSISDB. Lub ta sieć wirtualna powinna być taka sama jak ta, która jest połączona z siecią lokalną.

   e. W polu **Nazwa podsieci**wybierz nazwę podsieci dla sieci wirtualnej. Powinna to być inna podsieć z tej, która jest używana dla wystąpienia zarządzanego w sieci wirtualnej do hostowania SSISDB.

6. Zaznacz pole wyboru **Skonfiguruj Samoobsługowe Integration Runtime jako serwer proxy dla Azure-SSIS Integration Runtime** , aby określić, czy chcesz skonfigurować własne środowisko IR jako serwer proxy dla Azure-SSIS IR. Aby uzyskać więcej informacji, zobacz [Konfigurowanie własnego środowiska IR jako serwera proxy](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis). 

   Jeśli zaznaczysz pole wyboru, wykonaj następujące czynności.

   ![Ustawienia zaawansowane przy użyciu samodzielnego środowiska IR](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

   a. W przypadku **Samodzielnej Integration Runtime**Wybierz istniejące własne środowisko IR jako serwer proxy dla Azure-SSIS IR.

   b. W przypadku **usługi połączonej z magazynem przemieszczania**wybierz istniejącą połączoną usługę Azure Blob Storage. Lub Utwórz nowy element do przemieszczania.

   d. W polu **ścieżka tymczasowa**Określ kontener obiektów BLOB na wybranym koncie usługi Azure Blob Storage. Lub pozostaw to pole puste, aby użyć domyślnego ustawienia do przemieszczania.

7. Wybierz pozycję **weryfikacja sieci wirtualnej** > **dalej**. 

8. Na stronie **Podsumowanie** Przejrzyj wszystkie ustawienia aprowizacji, zakładkę zalecane linki do dokumentacji i wybierz pozycję **Zakończ** , aby rozpocząć tworzenie środowiska Integration Runtime.

    > [!NOTE]
    > Bez czasu instalacji niestandardowej ten proces powinien zakończyć się w ciągu 5 minut. Azure-SSIS IR można przyłączyć do sieci wirtualnej, ale może upłynąć do 20-30 minut.
    >
    > Jeśli używasz SSISDB, Usługa Data Factory będzie łączyć się z serwerem bazy danych w celu przygotowania SSISDB. Konfiguruje również uprawnienia i ustawienia dla sieci wirtualnej, jeśli określono, i przyłączy Azure-SSIS IR do sieci wirtualnej.
    > 
    > Podczas aprowizacji Azure-SSIS IR są również instalowane narzędzia do redystrybucyjny i pakiet Azure Feature Pack dla usług SSIS. Te składniki zapewniają łączność z plikami programu Excel, plikami dostępu i różnymi źródłami danych platformy Azure, a także ze źródłami danych, które już obsługują. Aby uzyskać informacje o innych składnikach, które można zainstalować, zobacz [Konfiguracja niestandardowa dla Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md).

7. Na karcie **Połączenia** w razie potrzeby przełącz na opcję **Środowiska Integration Runtime**. Wybierz pozycję **Odśwież**, aby odświeżyć stan.

   ![Stan tworzenia](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

8. Za pomocą linków w kolumnie **Akcje** możesz zatrzymać/uruchomić, edytować lub usunąć środowisko Integration Runtime. Użyj ostatniego linku, aby wyświetlić kod JSON dla środowiska Integration Runtime. Przyciski edytowania i usuwania są włączone tylko wtedy, gdy środowisko IR jest zatrzymane.

   ![Akcje środowiska IR platformy Azure SSIS](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)

### <a name="azure-ssis-integration-runtimes-in-the-portal"></a>Środowiska Azure SSIS Integration Runtime w portalu

1. W interfejsie użytkownika Azure Data Factory przejdź do karty **Edycja** i wybierz pozycję **połączenia**. Następnie przejdź na kartę **Integration Runtime** , aby wyświetlić istniejące środowiska Integration Runtime w fabryce danych.

   ![Wyświetl istniejący urząd skarbowy](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

2. Wybierz pozycję **Nowy** , aby utworzyć nowy Azure-SSIS IR.

   ![Środowisko Integration Runtime za pomocą menu](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

4. W oknie **Konfiguracja środowiska Integration Runtime** wybierz pozycję **Migruj metodą „lift-and-shift” istniejące pakiety usług SSIS do wykonywania na platformie Azure**, a następnie wybierz przycisk **Dalej**.

   ![Określanie typu środowiska Integration Runtime](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

5. Pozostałe kroki konfigurowania Azure-SSIS IR można znaleźć w sekcji [Inicjowanie obsługi platformy Azure SSIS Integration Runtime](#provision-an-azure-ssis-integration-runtime) .

## <a name="use-azure-powershell-to-create-an-integration-runtime"></a>Tworzenie środowiska Integration Runtime za pomocą Azure PowerShell

W tej sekcji utworzysz Azure-SSIS IR za pomocą Azure PowerShell.

### <a name="create-variables"></a>Tworzenie zmiennych

Skopiuj i wklej poniższy skrypt. Określ wartości zmiennych. 

```powershell
### Azure Data Factory information
# If your input contains a PSH special character like "$", precede it with the escape character "`" - for example, "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime information. This is a Data Factory compute resource for running SSIS packages.
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2
# Azure-SSIS IR edition/license info: Standard or Enterprise
$AzureSSISEdition = "Standard" # Standard by default, whereas Enterprise lets you use advanced features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, whereas BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported. For other nodes, up to (2 x number of cores) are currently supported.
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide the SAS URI of the blob container where your custom setup script and its associated files are stored
# Virtual network info: classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use an Azure SQL Database server with virtual network service endpoints or a managed instance with a private endpoint, or if you require on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for your Azure SQL Database server with virtual network service endpoints, or a different subnet from the one used for your managed instance with a private endpoint

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or managed instance name.DNS prefix.database.windows.net or managed instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, ensure that there's no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure AD
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for Azure AD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for Azure AD authentication]"
# For the basic pricing tier, specify "Basic," not "B." For standard, premium, and elastic pool tiers, specify "S0," "S1," "S2," "S3," etc. See https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server.
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for managed instance]"
```

### <a name="sign-in-and-select-a-subscription"></a>Zaloguj się i wybierz subskrypcję

Dodaj następujący skrypt, aby zalogować się i wybrać subskrypcję platformy Azure.

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName
```

### <a name="validate-the-connection-to-database-server"></a>Weryfikowanie połączenia z serwerem bazy danych

Dodaj następujący skrypt, aby sprawdzić poprawność serwera Azure SQL Database lub wystąpienia zarządzanego.

```powershell
# Validate only if you use SSISDB and you don't use virtual network or Azure AD authentication
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    if([string]::IsNullOrEmpty($VnetId) -and [string]::IsNullOrEmpty($SubnetName))
    {
        if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) -and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
        {
            $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword
            $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
            Try
            {
                $sqlConnection.Open();
            }
            Catch [System.Data.SqlClient.SqlException]
            {
                Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_";
                Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
                $yn = Read-Host
                if(!($yn -ieq "Y"))
                {
                    Return;
                }
            }
        }
    }
}
```

### <a name="configure-the-virtual-network"></a>Konfigurowanie sieci wirtualnej

Dodaj następujący skrypt, aby automatycznie skonfigurować uprawnienia i ustawienia sieci wirtualnej dla środowiska Azure-SSIS Integration Runtime do przyłączenia.

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz [grupę zasobów platformy Azure](../azure-resource-manager/resource-group-overview.md) za pomocą polecenia [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) . Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi w formie grupy.

Jeśli grupa zasobów już istnieje, nie kopiuj tego kodu do skryptu. 

```powershell
New-AzResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

### <a name="create-a-data-factory"></a>Tworzenie fabryki danych

Uruchom poniższe polecenie, aby utworzyć fabrykę danych.

```powershell
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName
```

### <a name="create-an-integration-runtime"></a>Tworzenie środowiska Integration Runtime

Uruchom następujące polecenia, aby utworzyć środowisko Azure-SSIS Integration Runtime, które uruchamia pakiety usług SSIS na platformie Azure.

Jeśli nie używasz SSISDB, możesz pominąć parametry `CatalogServerEndpoint`, `CatalogPricingTier`i `CatalogAdminCredential`.

Jeśli nie używasz serwera Azure SQL Database z punktami końcowymi usługi sieci wirtualnej lub wystąpieniem zarządzanym z prywatnym punktem końcowym do hostowania SSISDB lub potrzebujesz dostępu do danych lokalnych, możesz pominąć parametry `VNetId` i `Subnet` lub przekazać wartości puste dla niego. Można je również pominąć, jeśli skonfigurujesz własne środowisko IR jako serwer proxy dla Azure-SSIS IR dostępu do danych lokalnych. W przeciwnym razie nie można ich pominąć i musi upłynąć poprawne wartości z konfiguracji sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [Dołączanie Azure-SSIS IR do sieci wirtualnej](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

Jeśli używasz wystąpienia zarządzanego do hostowania SSISDB, możesz pominąć parametr `CatalogPricingTier` lub przekazać pustą wartość dla tego elementu. W przeciwnym razie nie można go pominąć i należy przekazać prawidłową wartość z listy obsługiwanych warstw cenowych dla Azure SQL Database. Aby uzyskać więcej informacji, zobacz [SQL Database limitów zasobów](../sql-database/sql-database-resource-limits.md).

Jeśli używasz uwierzytelniania usługi Azure AD z zarządzaną tożsamością dla fabryki danych do łączenia się z serwerem bazy danych, możesz pominąć parametr `CatalogAdminCredential`. Jednak należy dodać tożsamość zarządzaną dla fabryki danych do grupy usługi Azure AD z uprawnieniami dostępu do serwera bazy danych. Aby uzyskać więcej informacji, zobacz [Włączanie uwierzytelniania usługi Azure AD dla Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir). W przeciwnym razie nie można go pominąć i musi upłynąć prawidłowy obiekt utworzony z nazwy użytkownika i hasła administratora serwera na potrzeby uwierzytelniania SQL.

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -VnetId $VnetId `
                                           -Subnet $SubnetName
       
# Add the CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -CatalogServerEndpoint $SSISDBServerEndpoint `
                                               -CatalogPricingTier $SSISDBPricingTier

    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword)) # Add the CatalogAdminCredential parameter if you don't use Azure AD authentication
    {
        $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
        $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                                   -DataFactoryName $DataFactoryName `
                                                   -Name $AzureSSISName `
                                                   -CatalogAdminCredential $serverCreds
    }
}

# Add the SetupScriptContainerSasUri parameter if you use custom setup
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}
```

### <a name="start-the-integration-runtime"></a>Uruchom środowisko Integration Runtime

Uruchom następujące polecenia, aby uruchomić środowisko Azure-SSIS Integration Runtime.

```powershell
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

> [!NOTE]
> Bez czasu instalacji niestandardowej ten proces powinien zakończyć się w ciągu 5 minut. Azure-SSIS IR można przyłączyć do sieci wirtualnej, ale może upłynąć do 20-30 minut.
>
> Jeśli używasz SSISDB, Usługa Data Factory będzie łączyć się z serwerem bazy danych w celu przygotowania SSISDB. Konfiguruje również uprawnienia i ustawienia dla sieci wirtualnej, jeśli określono, i przyłączy Azure-SSIS IR do sieci wirtualnej.
> 
> Podczas aprowizacji Azure-SSIS IR są również instalowane narzędzia do redystrybucyjny i pakiet Azure Feature Pack dla usług SSIS. Te składniki zapewniają łączność z plikami programu Excel, plikami dostępu i różnymi źródłami danych platformy Azure, a także ze źródłami danych, które już obsługują. Aby uzyskać informacje o innych składnikach, które można zainstalować, zobacz [Konfiguracja niestandardowa dla Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="full-script"></a>Pełny skrypt

Oto pełny skrypt służący do tworzenia środowiska Azure-SSIS Integration Runtime.

```powershell
### Azure Data Factory information
# If your input contains a PSH special character like "$", precede it with the escape character "`" - for example, "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime information. This is a Data Factory compute resource for running SSIS packages.
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2
# Azure-SSIS IR edition/license info: Standard or Enterprise
$AzureSSISEdition = "Standard" # Standard by default, whereas Enterprise lets you use advanced features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, whereas BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from the Azure Hybrid Benefit option
# For a Standard_D1_v2 node, up to four parallel executions per node are supported. For other nodes, up to (2 x number of cores) are currently supported.
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide the SAS URI of the blob container where your custom setup script and its associated files are stored
# Virtual network info: classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use an Azure SQL Database server with virtual network service endpoints or a managed instance with a private endpoint, or if you require on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for your Azure SQL Database server with virtual network service endpoints, or a different subnet from the one used for your managed instance with a private endpoint

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or managed instance name.DNS prefix.database.windows.net or managed instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, ensure that there's no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure AD
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for Azure AD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for Azure AD authentication]"
# For the basic pricing tier, specify "Basic," not "B." For standard, premium, and elastic pool tiers, specify "S0," "S1," "S2," "S3," etc. See https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server.
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for managed instance]"

### Sign in and select a subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName

### Validate the connection to the database server
# Validate only if you use SSISDB and don't use a virtual network or Azure AD authentication
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    if([string]::IsNullOrEmpty($VnetId) -and [string]::IsNullOrEmpty($SubnetName))
    {
        if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) -and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
        {
            $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword
            $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
            Try
            {
                $sqlConnection.Open();
            }
            Catch [System.Data.SqlClient.SqlException]
            {
                Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_";
                Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
                $yn = Read-Host
                if(!($yn -ieq "Y"))
                {
                    Return;
                }
            }
        }
    }
}

### Configure a virtual network
# Make sure to run this script against the subscription to which the virtual network belongs
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}

### Create a data factory
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName

### Create an integration runtime
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -VnetId $VnetId `
                                           -Subnet $SubnetName
       
# Add CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -CatalogServerEndpoint $SSISDBServerEndpoint `
                                               -CatalogPricingTier $SSISDBPricingTier

    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword)) # Add the CatalogAdminCredential parameter if you don't use Azure AD authentication
    {
        $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
        $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                                   -DataFactoryName $DataFactoryName `
                                                   -Name $AzureSSISName `
                                                   -CatalogAdminCredential $serverCreds
    }
}

# Add the SetupScriptContainerSasUri parameter when you use custom setup
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}

### Start the integration runtime
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

## <a name="use-an-azure-resource-manager-template-to-create-an-integration-runtime"></a>Tworzenie środowiska Integration Runtime przy użyciu szablonu Azure Resource Manager

W tej sekcji użyjesz szablonu Azure Resource Manager, aby utworzyć środowisko Azure-SSIS Integration Runtime. Oto przykład przewodnika:

1. Utwórz plik JSON z następującym szablonem Azure Resource Manager. Zastąp wartości w nawiasach kątowych (symbolami zastępczymi) własnymi wartościami.

    ```json
    {
      "contentVersion": "1.0.0.0",
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": {},
        "variables": {},
        "resources": [{
            "name": "<Specify a name for your data factory>",
            "apiVersion": "2018-06-01",
            "type": "Microsoft.DataFactory/factories",
            "location": "East US",
            "properties": {},
            "resources": [{
                "type": "integrationruntimes",
                "name": "<Specify a name for your Azure-SSIS IR>",
                "dependsOn": [ "<The name of the data factory you specified at the beginning>" ],
                "apiVersion": "2018-06-01",
                "properties": {
                    "type": "Managed",
                    "typeProperties": {
                        "computeProperties": {
                            "location": "East US",
                            "nodeSize": "Standard_D8_v3",
                            "numberOfNodes": 1,
                            "maxParallelExecutionsPerNode": 8
                        },
                        "ssisProperties": {
                            "catalogInfo": {
                                "catalogServerEndpoint": "<Azure SQL Database server name>.database.windows.net",
                                "catalogAdminUserName": "<Azure SQL Database server admin username>",
                                "catalogAdminPassword": {
                                    "type": "SecureString",
                                    "value": "<Azure SQL Database server admin password>"
                                },
                                "catalogPricingTier": "Basic"
                            }
                        }
                    }
                }
            }]
        }]
    }
    ```

2. Aby wdrożyć szablon Azure Resource Manager, uruchom polecenie `New-AzResourceGroupDeployment`, jak pokazano w poniższym przykładzie. W przykładzie `ADFTutorialResourceGroup` jest nazwą grupy zasobów. `ADFTutorialARM.json` to plik zawierający definicję JSON dla fabryki danych i Azure-SSIS IR.

    ```powershell
    New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    To polecenie tworzy fabrykę danych i Azure-SSIS IR w niej, ale nie uruchamia środowiska IR.

3. Aby uruchomić Azure-SSIS IR, uruchom polecenie `Start-AzDataFactoryV2IntegrationRuntime`:

    ```powershell
    Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "<Resource Group Name>" `
                                                 -DataFactoryName "<Data Factory Name>" `
                                                 -Name "<Azure SSIS IR Name>" `
                                                 -Force
    ```

> [!NOTE]
> Bez czasu instalacji niestandardowej ten proces powinien zakończyć się w ciągu 5 minut. Azure-SSIS IR można przyłączyć do sieci wirtualnej, ale może upłynąć do 20-30 minut.
>
> Jeśli używasz SSISDB, Usługa Data Factory będzie łączyć się z serwerem bazy danych w celu przygotowania SSISDB. Konfiguruje również uprawnienia i ustawienia dla sieci wirtualnej, jeśli określono, i przyłączy Azure-SSIS IR do sieci wirtualnej.
> 
> Podczas aprowizacji Azure-SSIS IR są również instalowane narzędzia do redystrybucyjny i pakiet Azure Feature Pack dla usług SSIS. Te składniki zapewniają łączność z plikami programu Excel, plikami dostępu i różnymi źródłami danych platformy Azure, a także ze źródłami danych, które już obsługują. Aby uzyskać informacje o innych składnikach, które można zainstalować, zobacz [Konfiguracja niestandardowa dla Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="deploy-ssis-packages"></a>Wdrażanie pakietów usług SSIS

Jeśli używasz SSISDB, możesz wdrożyć w nim pakiety i uruchamiać je na Azure-SSIS IR przy użyciu narzędzi danych SQL Server lub narzędzi SQL Server Management Studio. Narzędzia te łączą się z serwerem bazy danych za pośrednictwem swojego punktu końcowego serwera: 

- W przypadku serwera Azure SQL Database z prywatnym punktem końcowym format punktu końcowego serwera jest `<server name>.database.windows.net`.
- W przypadku wystąpienia zarządzanego z prywatnym punktem końcowym format punktu końcowego serwera jest `<server name>.<dns prefix>.database.windows.net`.
- W przypadku wystąpienia zarządzanego z publicznym punktem końcowym format punktu końcowego serwera jest `<server name>.public.<dns prefix>.database.windows.net,3342`. 

Jeśli nie korzystasz z programu SSISDB, możesz wdrożyć pakiety w systemach plików, udziałach plików lub Azure Files. Następnie można uruchomić je na Azure-SSIS IR przy użyciu narzędzi wiersza polecenia `dtinstall`, `dtutil`i `dtexec`. Aby uzyskać więcej informacji, zobacz [wdrażanie pakietów SSIS](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server). 

W obu przypadkach wdrożone pakiety można również uruchomić na Azure-SSIS IR przy użyciu działania wykonaj pakiet SSIS w potokach Data Factory. Aby uzyskać więcej informacji, zobacz [wywołania wykonywania pakietu SSIS jako działania pierwszej klasy Data Factory](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).

## <a name="next-steps"></a>Następne kroki

Zobacz inne tematy Azure-SSIS IR w tej dokumentacji:

- [Środowisko Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime). Ten artykuł zawiera ogólne informacje na temat środowiska Integration Runtime, w tym Azure-SSIS IR.
- [Monitor an Azure-SSIS IR (Monitorowanie środowiska Azure-SSIS IR)](monitor-integration-runtime.md#azure-ssis-integration-runtime). W tym artykule pokazano, jak pobrać i zrozumieć informacje o Azure-SSIS IR.
- [Manage an Azure-SSIS IR (Zarządzanie środowiskiem Azure-SSIS IR)](manage-azure-ssis-integration-runtime.md). W tym artykule przedstawiono sposób zatrzymywania, uruchamiania lub usuwania Azure-SSIS IR. Przedstawiono w nim również sposób skalowania Azure-SSIS IR przez dodanie kolejnych węzłów.
- [Join an Azure-SSIS IR to a virtual network](join-azure-ssis-integration-runtime-virtual-network.md) (Dołączanie środowiska IR Azure SSIS do sieci wirtualnej). Ten artykuł zawiera informacje dotyczące przyłączania Azure-SSIS IR do sieci wirtualnej.
