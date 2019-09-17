---
title: Utwórz Azure-SSIS Integration Runtime w Azure Data Factory | Microsoft Docs
description: Dowiedz się, jak utworzyć Azure-SSIS Integration Runtime w Azure Data Factory, aby można było wdrażać i uruchamiać pakiety usług SSIS na platformie Azure.
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
ms.openlocfilehash: 36cb4d306cd74dcde09fa55fc582a043bc324f65
ms.sourcegitcommit: a819209a7c293078ff5377dee266fa76fd20902c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/16/2019
ms.locfileid: "71010023"
---
# <a name="create-azure-ssis-integration-runtime-in-azure-data-factory"></a>Utwórz Azure-SSIS Integration Runtime w Azure Data Factory

Ten samouczek zawiera kroki umożliwiające zainicjowanie obsługi Integration Runtime (IR) na platformie SQL Server Integration Services Azure w Azure Data Factory (ADF). Azure-SSIS IR obsługuje uruchamianie pakietów wdrożonych w katalogu usług SSIS (SSISDB) hostowanych przez Azure SQL Database Server/wystąpienie zarządzane (model wdrażania projektu) i te wdrożone w systemach plików/udziałach plików/Azure Files (model wdrażania pakietu). Po zainicjowaniu obsługi administracyjnej Azure-SSIS IR można korzystać ze znanych narzędzi, takich jak SQL Server narzędzia Data Tools (SSDT)/SQL Server Management Studio (SSMS), a także narzędzi wiersza polecenia, takich `dtinstall`jak / / `dtutil` `dtexec`, do Wdróż i uruchom pakiety na platformie Azure.

[Samouczek: Informacje o aprowizacji Azure-SSIS IR](tutorial-create-azure-ssis-runtime-portal.md) pokazują, jak utworzyć Azure-SSIS IR za pośrednictwem aplikacji Azure Portal/ADF i opcjonalnie użyć Azure SQL Database serwera/wystąpienia zarządzanego do hostowania SSISDB. Ten artykuł rozszerza się w samouczku i pokazuje, jak wykonać następujące czynności:

- Opcjonalnie można użyć serwera Azure SQL Database z punktami końcowymi usługi sieci wirtualnej/wystąpieniem zarządzanym z prywatnym punktem końcowym w celu hostowania SSISDB. Jako wymaganie wstępne należy skonfigurować uprawnienia/ustawienia sieci wirtualnej dla Azure-SSIS IR, aby dołączyć do sieci wirtualnej.

- Opcjonalnie można użyć uwierzytelniania Azure Active Directory (AAD) z zarządzaną tożsamością dla Twojego ADF, aby połączyć się z Azure SQL Database serwerem/wystąpieniem zarządzanym. Jako warunek wstępny należy dodać tożsamość zarządzaną dla Twojego ADF jako użytkownika bazy danych, która może utworzyć SSISDB.

- Opcjonalnie Przyłącz Azure-SSIS IR do sieci wirtualnej/Skonfiguruj własne środowisko IR jako serwer proxy dla Azure-SSIS IR, aby uzyskać dostęp do danych lokalnych.

## <a name="overview"></a>Omówienie

W tym artykule przedstawiono różne sposoby aprowizacji Azure-SSIS IR:

- [Azure Portal](#azure-portal)
- [Azure PowerShell](#azure-powershell)
- [Szablon usługi Azure Resource Manager](#azure-resource-manager-template)

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Subskrypcja platformy Azure**. Jeśli jeszcze nie masz subskrypcji, możesz utworzyć [bezpłatne konto wersji próbnej](https://azure.microsoft.com/pricing/free-trial/) .
- **Azure SQL Database serwera/wystąpienia zarządzanego (opcjonalnie)** . Jeśli nie masz jeszcze serwera bazy danych, utwórz go w Azure Portal przed rozpoczęciem pracy. ADF utworzy SSISDB na tym serwerze bazy danych. Zaleca się utworzenie serwera bazy danych w tym samym regionie platformy Azure, co środowisko Integration Runtime. Ta konfiguracja umożliwia wykonywanie dzienników wykonywania zapisu środowiska Integration Runtime w usłudze SSISDB bez przekroczenia regionów świadczenia usługi Azure. 
    - W zależności od wybranego serwera bazy danych baza danych SSISDB może zostać utworzona w Twoim imieniu jako pojedyncza baza danych, jako część elastycznej puli lub w ramach wystąpienia zarządzanego i może być dostępna w sieci publicznej lub przez dołączenie do sieci wirtualnej. Aby uzyskać wskazówki dotyczące wybierania typu serwera bazy danych do hostowania SSISDB, zobacz [porównanie Azure SQL Database pojedynczej bazie danych/elastycznej puli/wystąpienia zarządzanego](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance). Jeśli używasz serwera Azure SQL Database z punktami końcowymi usługi sieci wirtualnej/wystąpieniem zarządzanym z prywatnym punktem końcowym do hostowania SSISDB lub Wymagaj dostępu do danych lokalnych bez konfigurowania samoobsługowego środowiska IR, musisz dołączyć Azure-SSIS IR do sieci wirtualnej, Zobacz [Przyłączanie Azure-SSIS IR do sieci wirtualnej](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).
    - Upewnij się, że ustawienie **Zezwalaj na dostęp do usług platformy Azure** jest włączone dla serwera bazy danych. Nie ma to zastosowania w przypadku używania serwera Azure SQL Database z punktami końcowymi usługi sieci wirtualnej/wystąpieniem zarządzanym z prywatnym punktem końcowym w celu hostowania SSISDB. Aby uzyskać więcej informacji, zobacz artykuł [Secure your Azure SQL database (Zabezpieczenia bazy danych Azure SQL Database)](../sql-database/sql-database-security-tutorial.md#create-firewall-rules). Aby włączyć to ustawienie przy użyciu programu PowerShell, zobacz polecenie [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule).
    - Dodaj adres IP komputera klienckiego lub zakres adresów IP, który zawiera adres IP komputera klienckiego, na listę adres IP klienta w ustawieniach zapory dla serwera bazy danych. Aby uzyskać więcej informacji, zobacz [Reguły zapory na poziomie serwera i na poziomie bazy danych usługi Azure SQL Database](../sql-database/sql-database-firewall-configure.md).
    - Można nawiązać połączenie z serwerem bazy danych przy użyciu uwierzytelniania SQL z poświadczeniami administratora serwera lub uwierzytelnianiem Azure Active Directory (AAD) z tożsamością zarządzaną dla Twojego ADF.  Dla tych ostatnich należy dodać tożsamość zarządzaną dla Twojego PODAJNIKa do grupy usługi AAD z uprawnieniami dostępu do serwera bazy danych, zobacz [Włączanie uwierzytelniania usługi AAD dla Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir).
    - Upewnij się, że serwer bazy danych nie ma już SSISDB. Inicjowanie obsługi Azure-SSIS IR nie obsługuje korzystania z istniejącej SSISDB.
- **Azure Resource Manager sieci wirtualnej (opcjonalnie)** . Należy mieć Azure Resource Manager sieci wirtualnej, jeśli co najmniej jeden z następujących warunków jest spełniony:
    - Hosty SSISDB na serwerze Azure SQL Database z punktami końcowymi usługi sieci wirtualnej/wystąpieniem zarządzanym z prywatnym punktem końcowym.
    - Chcesz połączyć się z lokalnymi magazynami danych z pakietów SSIS uruchomionych na Azure-SSIS IR bez konfigurowania własnego środowiska IR.
- **Azure PowerShell (opcjonalnie)** . Postępuj zgodnie z instrukcjami dotyczącymi [instalowania i konfigurowania Azure PowerShell](/powershell/azure/install-az-ps), jeśli chcesz uruchomić skrypt programu PowerShell w celu aprowizacji Azure-SSIS IR.

### <a name="region-support"></a>Obsługa regionów

Aby zapoznać się z listą regionów świadczenia usługi Azure, w których obecnie są dostępne ADF i Azure-SSIS IR, zapoznaj się z tematem obsługa systemu APD i usług [SSIS IR według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all).

### <a name="compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance"></a>Porównanie SQL Database pojedynczej bazy danych/elastycznej puli i SQL Database wystąpienia zarządzanego

Poniższa tabela zawiera porównanie niektórych funkcji programu Azure SQL Database Server i wystąpienia zarządzanego, które są powiązane z usługą Azure-SSIR IR:

| Cecha | pojedyncza baza danych/Pula elastyczna| Wystąpienie zarządzane |
|---------|--------------|------------------|
| **Harmonogram** | Agent SQL Server jest niedostępny.<br/><br/>Zobacz [Planowanie wykonywania pakietu w potoku ADF](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages?view=sql-server-2017#activity).| Agent wystąpienia zarządzanego jest dostępny. |
| **Uwierzytelnianie** | Można utworzyć SSISDB z użytkownikiem zawartej bazy danych reprezentującym dowolną grupę usługi AAD z zarządzaną tożsamością Twojego ADF jako członka w roli **db_owner** .<br/><br/>Zobacz [Włączanie uwierzytelniania usługi Azure AD, aby utworzyć SSISDB na serwerze Azure SQL Database](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database). | Można utworzyć SSISDB z użytkownikiem zawartej bazy danych reprezentującym zarządzaną tożsamość Twojego ADF. <br/><br/>Zobacz [Włączanie uwierzytelniania usługi Azure AD, aby utworzyć SSISDB w wystąpieniu zarządzanym Azure SQL Database](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database-managed-instance). |
| **Warstwa usług** | Podczas tworzenia Azure-SSIS IR z serwerem Azure SQL Database można wybrać warstwę usług dla SSISDB. Istnieje wiele warstw usług. | Po utworzeniu Azure-SSIS IR z wystąpieniem zarządzanym nie można wybrać warstwy usług dla SSISDB. Wszystkie bazy danych w wystąpieniu zarządzanym współdzielą ten sam zasób przydzielony do tego wystąpienia. |
| **Sieć wirtualna** | Program obsługuje tylko Azure Resource Manager sieci wirtualnych Azure-SSIS IR do przyłączenia, jeśli używasz serwera Azure SQL Database z punktami końcowymi usługi sieci wirtualnej lub potrzebujesz dostępu do lokalnych magazynów danych bez konfigurowania własnego środowiska IR. | Obsługuje tylko Azure Resource Manager sieci wirtualnych, dla których Azure-SSIS IR można przyłączyć. Sieć wirtualna jest wymagana, gdy nie zostanie włączony publiczny punkt końcowy dla wystąpienia zarządzanego.<br/><br/>Jeśli dołączysz Azure-SSIS IR do tej samej sieci wirtualnej co wystąpienie zarządzane, upewnij się, że Azure-SSIS IR znajduje się w innej podsieci niż wystąpienie zarządzane. Jeśli dołączysz Azure-SSIS IR do innej sieci wirtualnej niż wystąpienie zarządzane, zalecamy połączenie komunikacji równorzędnej sieci wirtualnej lub sieci wirtualnej z połączeniem sieci wirtualnej. Zobacz [łączenie aplikacji z Azure SQL Database wystąpieniem zarządzanym](../sql-database/sql-database-managed-instance-connect-app.md). |
| **Transakcje rozproszone** | Obsługiwane przez transakcje elastyczne. Transakcje Microsoft Distributed Transaction Coordinator (MSDTC) nie są obsługiwane. Jeśli pakiety SSIS używają usługi MSDTC do koordynowania transakcji rozproszonych, rozważ migrację do transakcji elastycznych dla Azure SQL Database. Aby uzyskać więcej informacji, zobacz [transakcje rozproszone w bazach danych w chmurze](../sql-database/sql-database-elastic-transactions-overview.md). | Nieobsługiwane. |
| | | |

## <a name="azure-portal"></a>Azure Portal

W tej sekcji do tworzenia Azure-SSIS IR służy Azure Portal, w tym interfejs użytkownika (UI)/App.

### <a name="create-a-data-factory"></a>Tworzenie fabryki danych

Aby utworzyć ADF za pośrednictwem Azure Portal, postępuj zgodnie z instrukcjami krok po kroku w artykule [Tworzenie ADF za pośrednictwem interfejsu użytkownika](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory) , a następnie wybierz pozycję **Przypnij do pulpitu nawigacyjnego** , aby umożliwić szybki dostęp po utworzeniu. 

Po utworzeniu podajnika APD Otwórz **jego stronę przeglądu** na Azure Portal i kliknij kafelek **Monitoruj & Monitor** , aby uruchomić stronę wprowadzenie na oddzielnej karcie, w której możesz kontynuować tworzenie Azure-SSIS IR.   

### <a name="provision-an-azure-ssis-integration-runtime"></a>Aprowizowanie środowiska Azure SSIS Integration Runtime

1. **Na stronie Wprowadzenie** kliknij kafelek Konfigurowanie usług **SSIS Integration Runtime** .

   ![Kafelek Konfigurowanie środowiska SSIS Integration Runtime](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

2. Na stronie **Ustawienia ogólne** kreatora **konfiguracji środowiska Integration Runtime** wykonaj następujące czynności:

   ![Ustawienia ogólne](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

    a. W polu **Nazwa** wprowadź nazwę środowiska Integration Runtime.

    b. W polu **Opis** wprowadź opis środowiska Integration Runtime.

    d. W polu **Lokalizacja** wybierz lokalizację środowiska Integration Runtime. Wyświetlane są tylko obsługiwane lokalizacje. Zalecamy wybranie tej samej lokalizacji do hostowania bazy danych SSISDB co w przypadku serwera bazy danych.

    d. W polu **Rozmiar węzła** wybierz rozmiar węzła w klastrze środowiska Integration Runtime. Wyświetlane są tylko obsługiwane rozmiary węzłów. Wybierz duży rozmiar węzła (skalowania w górę), jeśli chcesz uruchamiać wiele pakietów o znacznym wykorzystaniu zasób obliczeniowych lub pamięci.

    e. W polu **Liczba węzłów** wybierz liczbę węzłów w klastrze środowiska Integration Runtime. Wyświetlane są tylko obsługiwane liczby węzłów. Wybierz duży klaster z wieloma węzłami (skalowania w poziomie), jeśli chcesz uruchamiać wiele pakietów równolegle.

    f. W polu **Wydanie/licencja** wybierz wydanie lub licencję programu SQL Server na potrzeby środowiska Integration Runtime: Standard lub Enterprise. Wybierz wartość Enterprise, jeśli chcesz używać funkcji zaawansowanych lub Premium w swoim środowisku Integration Runtime.

    g. W obszarze **Oszczędność pieniędzy** wybierz opcję Korzyść użycia hybrydowego platformy Azure (AHB) dla używanego środowiska Integration Runtime: Tak lub Nie. Wybierz opcję Tak, jeśli chcesz użyć własnej licencji programu SQL Server z pakietem Software Assurance, aby móc ograniczyć koszty dzięki użyciu hybrydowemu.

    h. Kliknij przycisk **Dalej**.

3. Na stronie **Ustawienia SQL** wykonaj następujące czynności:

   ![Ustawienia SQL](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

    a. Na stronie **Tworzenie wykazu usług SSIS...** wybierz model wdrażania dla pakietów do uruchomienia na Azure-SSIS IR: Model wdrażania projektu, w którym pakiety są wdrażane do SSISDB hostowanych przez serwer bazy danych lub model wdrażania pakietu, w którym pakiety są wdrażane w systemach plików/udziałach plików/Azure Files. W przypadku zaznaczenia tego pola wyboru należy przenieść własny serwer bazy danych na host SSISDB, który zostanie utworzony w Twoim imieniu i w nim zarządzamy.
   
    b. W polu **Subskrypcja** określ subskrypcję platformy Azure, z której korzysta Twój serwer bazy danych hostujący bazę danych SSISDB. 

    c. W polu **Lokalizacja** określ lokalizację serwera bazy danych hostującego bazę danych SSISDB. Zalecamy wybranie tej samej lokalizacji co w przypadku środowiska Integration Runtime. 

    d. W polu **Punkt końcowy serwera bazy danych wykazu** wybierz punkt końcowy serwera bazy danych hostującego bazę danych SSISDB. W zależności od wybranego serwera bazy danych baza danych SSISDB może zostać utworzona w Twoim imieniu jako pojedyncza baza danych, jako część elastycznej puli lub w ramach wystąpienia zarządzanego i może być dostępna w sieci publicznej lub przez dołączenie do sieci wirtualnej. Aby uzyskać wskazówki dotyczące wybierania typu serwera bazy danych do hostowania SSISDB, zobacz [porównanie Azure SQL Database pojedynczej bazie danych/elastycznej puli/wystąpienia zarządzanego](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance). W przypadku wybrania Azure SQL Database serwera z punktami końcowymi usługi sieci wirtualnej/wystąpieniem zarządzanym z prywatnym punktem końcowym w celu hostowania SSISDB lub wymagania dostępu do danych lokalnych bez konfigurowania samoobsługowego środowiska IR należy dołączyć Azure-SSIS IR do sieci wirtualnej , zobacz [Dołączanie Azure-SSIS IR do sieci wirtualnej](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). 

    e. Pole wyboru **Użyj uwierzytelniania usługi AAD...** służy do wyboru metody uwierzytelniania na potrzeby serwera bazy danych hostującego bazę danych SSISDB: Uwierzytelnianie SQL lub uwierzytelnianie w usłudze AAD przy użyciu tożsamości zarządzanej dla Twojego ADF. W przypadku zaznaczenia tej opcji należy dodać tożsamość zarządzaną dla Twojego PODAJNIKa do grupy usługi AAD z uprawnieniami dostępu do serwera bazy danych, zobacz [Włączanie uwierzytelniania usługi AAD dla Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir). 

    f. W polu **Nazwa administratora** wprowadź nazwę użytkownika uwierzytelnienia SQL dla serwera bazy danych hostującego bazę danych SSISDB. 

    g. W polu **Hasło administratora** wprowadź hasło uwierzytelnienia SQL dla serwera bazy danych hostującego bazę danych SSISDB. 

    h. W polu **Warstwa serwera bazy danych katalogu** wybierz warstwę usługi dla serwera bazy hostującego bazę danych SSISDB: warstwa Podstawowa/Standardowa/Premium lub nazwa puli elastycznej. 

    i. Kliknij pozycję **Testuj połączenie**. Jeśli test zakończył się pomyślnie, kliknij przycisk **Dalej**. 

4. Na stronie **Ustawienia zaawansowane** wykonaj następujące czynności:

    ![Ustawienia zaawansowane](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

    a. W polu **Maksymalna liczba równoległych wykonań na węzeł** wybierz maksymalną liczbę pakietów, które mogą być wykonywane jednocześnie na węzeł w klastrze środowiska Integration Runtime. Wyświetlane są tylko obsługiwane liczby pakietów. Wybierz niską liczbę, jeśli chcesz użyć więcej niż jednego rdzenia do uruchamiania pojedynczego dużego/ciężkiego pakietu, który jest intensywnie wykorzystujący operacje obliczeniowe i pamięci. Wybierz dużą liczbę, jeśli chcesz uruchamiać małe pakiety w ramach jednego rdzenia.

    b. Aby uzyskać **identyfikator URI sygnatury dostępu współdzielonego kontenera instalacji niestandardowej** opcjonalnie wprowadź identyfikator URI sygnatury dostępu współdzielonego kontenera magazynu Azure Storage Blob, gdzie przechowywany jest skrypt instalacji i skojarzone z nim pliki. Aby uzyskać więcej informacji, zobacz [Instalacja niestandardowa dla środowiska Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup).

5. W obszarze **Wybierz sieć wirtualną...** zaznacz pole wyboru, czy chcesz dołączyć do środowiska Integration Runtime do sieci wirtualnej. Sprawdź, czy używasz serwera Azure SQL Database z punktami końcowymi usługi sieci wirtualnej/wystąpieniem zarządzanym z prywatnym punktem końcowym, aby hostować SSISDB lub wymagać dostępu do danych lokalnych, tj. lokalnych źródeł danych/miejsc docelowych w pakietach SSIS, bez Konfigurowanie samoobsługowego środowiska IR, zobacz [Dołączanie Azure-SSIS IR do sieci wirtualnej](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Jeśli go sprawdzisz, wykonaj następujące czynności:

   ![Ustawienia zaawansowane z siecią wirtualną](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

    a. W obszarze **subskrypcja**wybierz subskrypcję platformy Azure, która ma swoją sieć wirtualną.

    b. W polu **Lokalizacja**jest wybierana ta sama lokalizacja środowiska Integration Runtime.

    c. W **polu Typ**wybierz typ sieci wirtualnej: Klasyczne lub Azure Resource Manager. Zalecamy wybranie Azure Resource Manager sieci wirtualnej, ponieważ klasyczna Sieć wirtualna zostanie wkrótce wycofana.

    d. W polu **Nazwa**sieci wirtualnej wybierz nazwę swojej usługi. Ta sieć wirtualna powinna być taka sama, jak w przypadku serwera Azure SQL Database z punktami końcowymi usługi sieci wirtualnej/wystąpieniem zarządzanym w sieci wirtualnej w celu hostowania SSISDB lub połączenia z siecią lokalną.

    e. W polu **Nazwa podsieci**wybierz nazwę podsieci dla sieci wirtualnej. Powinna to być inna podsieć niż używana dla wystąpienia zarządzanego w sieci wirtualnej do hostowania SSISDB.

6. Na **skonfigurować własne** pole wyboru, wybierz, czy chcesz skonfigurować własne środowisko IR jako serwer proxy dla Azure-SSIS IR, zobacz Konfigurowanie samoobsługowego środowiska [IR jako serwera proxy](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis). Jeśli go sprawdzisz, wykonaj następujące czynności:

   ![Ustawienia zaawansowane przy użyciu samodzielnego środowiska IR](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

    a. W przypadku **Samodzielnej Integration Runtime**Wybierz istniejące własne środowisko IR jako serwer proxy dla Azure-SSIS IR.

    b. W przypadku **usługi połączonej z magazynem przemieszczania**wybierz istniejącą połączoną usługę Azure Blob Storage lub Utwórz nową na potrzeby przemieszczania.

    c. W polu **ścieżka tymczasowa**Określ kontener obiektów BLOB w wybranym BLOB Storage platformy Azure lub pozostaw to pole puste, aby użyć domyślnego ustawienia do przemieszczania.

7. Kliknij pozycję **Walidacja sieci wirtualnej** i lub **dalej**. 

8. Na stronie **Podsumowanie** Przejrzyj wszystkie ustawienia aprowizacji, zakładkę zalecane linki do dokumentacji, a następnie kliknij przycisk **Zakończ** , aby rozpocząć tworzenie środowiska Integration Runtime.

    > [!NOTE]
    > Bez czasu instalacji niestandardowej ten proces powinien zostać zakończony w ciągu 5 minut, ale może potrwać około 20-30 minut w przypadku Azure-SSIS IR dołączenia do sieci wirtualnej.
    >
    > Jeśli używasz SSISDB, usługa ADF będzie łączyć się z serwerem bazy danych w celu przygotowania SSISDB. Konfiguruje również uprawnienia i ustawienia dla sieci wirtualnej, jeśli określono, i przyłączy Azure-SSIS IR do sieci wirtualnej.
    > 
    > Podczas aprowizacji Azure-SSIS IR są również instalowane narzędzia do redystrybucyjny i pakiet Azure Feature Pack dla usług SSIS. Te składniki zapewniają łączność z plikami programu Excel/Access i różnymi źródłami danych platformy Azure, a także ze źródłami danych, które są już obsługiwane przez wbudowane składniki. Możesz również zainstalować dodatkowe składniki, zobacz [Konfiguracja niestandardowa dla Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md).

7. W oknie **Połączenia** w razie potrzeby przełącz do pozycji **Produkty Integration Runtime**. Kliknij przycisk **Odśwież**, aby odświeżyć status.

   ![Stan tworzenia](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

8. Użyj linków w kolumnie **Akcje** , aby zatrzymać/uruchomić, edytować lub usunąć środowisko Integration Runtime. Użyj ostatniego linku, aby wyświetlić kod JSON dla środowiska Integration Runtime. Przyciski edytowania i usuwania są włączone tylko wtedy, gdy środowisko IR jest zatrzymane.

   ![Środowisko IR Azure SSIS — akcje](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)

### <a name="azure-ssis-integration-runtimes-in-the-portal"></a>Środowiska Azure SSIS Integration Runtime w portalu

1. W interfejsie użytkownika usługi Azure Data Factory przejdź na kartę **Edycja**, kliknij pozycję **Połączenia**, a następnie przejdź na kartę **Środowiska Integration Runtime**, aby wyświetlić istniejące środowiska Integration Runtime w fabryce danych.

   ![Wyświetl istniejący urząd skarbowy](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

2. Kliknij pozycję **Nowe**, aby utworzyć środowisko IR Azure-SSIS.

   ![Środowisko Integration Runtime za pomocą menu](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

3. Aby utworzyć środowisko Azure-SSIS Integration Runtime, kliknij przycisk **Nowe**, jak pokazano na ilustracji.

4. W oknie Integration Runtime Setup (Konfiguracja środowiska Integration Runtime) wybierz pozycję **Lift-and-shift existing SSIS packages to execute in Azure** (Migruj metodą „lift-and-shift” istniejące pakiety usług SSIS do wykonywania na platformie Azure), a następnie kliknij przycisk **Dalej**.

   ![Określanie typu środowiska Integration Runtime](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

5. Zobacz sekcję [Aprowizowanie środowiska Azure SSIS Integration Runtime](#provision-an-azure-ssis-integration-runtime), zawierającą pozostałe kroki konfigurowania środowiska IR Azure-SSIS.

## <a name="azure-powershell"></a>Azure PowerShell

W tej sekcji utworzysz Azure-SSIS IR za pomocą Azure PowerShell.

### <a name="create-variables"></a>Tworzenie zmiennych

Skopiuj i wklej następujący skrypt — Określ wartości dla zmiennych. 

```powershell
### Azure Data Factory information
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - Must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime information - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2
# Azure-SSIS IR edition/license info: Standard or Enterprise
$AzureSSISEdition = "Standard" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported, but for other nodes, up to (2 x number of cores) are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database server with virtual network service endpoints/Managed Instance with a private endpoint/on-premises data without configuring Self-Hosted IR, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Please use the same subnet as the one used for your Azure SQL Database server with virtual network service endpoints or a different subnet than the one used for your Managed Instance with a private endpoint

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or Managed Instance name.DNS prefix.database.windows.net or Managed Instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, please ensure that there is no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure Active Directory (AAD)
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for AAD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for AAD authentication]"
# For the basic pricing tier, specify "Basic", not "B" - For standard/premium/elastic pool tiers, specify "S0", "S1", "S2", "S3", etc., see https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for Managed Instance]"
```

### <a name="sign-in-and-select-subscription"></a>Logowanie i wybieranie subskrypcji

Dodaj następujący kod skryptu, aby się zalogować, i wybierz subskrypcję platformy Azure:

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName
```

### <a name="validate-the-connection-to-database-server"></a>Weryfikowanie połączenia z serwerem bazy danych

Dodaj następujący skrypt, aby sprawdzić poprawność serwera Azure SQL Database/wystąpienia zarządzanego.

```powershell
# Validate only if you use SSISDB and do not use VNet or AAD authentication
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

### <a name="configure-virtual-network"></a>Konfigurowanie sieci wirtualnej

Dodaj następujący skrypt, aby automatycznie skonfigurować uprawnienia/ustawienia sieci wirtualnej do przyłączenia do Twojego środowiska Azure SSIS Integration Runtime.

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

Jeśli grupa zasobów już istnieje, nie Kopiuj tego kodu do skryptu. 

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

Jeśli nie używasz SSISDB, możesz pominąć parametry CatalogServerEndpoint, CatalogPricingTier i CatalogAdminCredential.

Jeśli nie używasz serwera Azure SQL Database z punktami końcowymi usługi sieci wirtualnej/wystąpieniem zarządzanym z prywatnym punktem końcowym do hostowania SSISDB lub Wymagaj dostępu do danych lokalnych, możesz pominąć parametry VNetId i Subnet lub przekazać dla nich puste wartości. Można je również pominąć, jeśli skonfigurujesz własne środowisko IR jako serwer proxy dla Azure-SSIS IR dostępu do danych lokalnych. W przeciwnym razie nie można ich pominąć i należy przekazać prawidłowe wartości z konfiguracji sieci wirtualnej, zobacz [sprzęganie Azure-SSIS IR z siecią wirtualną](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

Jeśli używasz wystąpienia zarządzanego do hostowania SSISDB, możesz pominąć parametr CatalogPricingTier lub przekazać pustą wartość dla tego elementu. W przeciwnym razie nie można go pominąć i należy przekazać prawidłową wartość z listy obsługiwanych warstw cenowych dla Azure SQL Database, zapoznaj się z tematem [SQL Database limitów zasobów](../sql-database/sql-database-resource-limits.md).

Jeśli używasz uwierzytelniania przy użyciu usługi Azure Active Directory (AAD) z tożsamością zarządzaną do łączenia się z serwerem bazy danych, możesz pominąć parametr CatalogAdminCredential, ale należy dodać tożsamość zarządzaną dla tego PODAJNIKa do grupy usługi AAD z dostępem uprawnienia do serwera bazy danych, zobacz [Włączanie uwierzytelniania usługi AAD dla Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir). W przeciwnym razie nie można go pominąć i musi upłynąć prawidłowy obiekt utworzony z nazwy użytkownika i hasła administratora serwera na potrzeby uwierzytelniania SQL.

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
       
# Add CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -CatalogServerEndpoint $SSISDBServerEndpoint `
                                               -CatalogPricingTier $SSISDBPricingTier

    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword)) # Add CatalogAdminCredential parameter if you do not use AAD authentication
    {
        $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
        $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                                   -DataFactoryName $DataFactoryName `
                                                   -Name $AzureSSISName `
                                                   -CatalogAdminCredential $serverCreds
    }
}

# Add SetupScriptContainerSasUri parameter if you use custom setup
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}
```

### <a name="start-integration-runtime"></a>Uruchamianie środowiska Integration Runtime

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
> Bez czasu instalacji niestandardowej ten proces powinien zostać zakończony w ciągu 5 minut, ale może potrwać około 20-30 minut w przypadku Azure-SSIS IR dołączenia do sieci wirtualnej.
>
> Jeśli używasz SSISDB, usługa ADF będzie łączyć się z serwerem bazy danych w celu przygotowania SSISDB. Konfiguruje również uprawnienia i ustawienia dla sieci wirtualnej, jeśli określono, i przyłączy Azure-SSIS IR do sieci wirtualnej.
> 
> Podczas aprowizacji Azure-SSIS IR są również instalowane narzędzia do redystrybucyjny i pakiet Azure Feature Pack dla usług SSIS. Te składniki zapewniają łączność z plikami programu Excel/Access i różnymi źródłami danych platformy Azure, a także ze źródłami danych, które są już obsługiwane przez wbudowane składniki. Możesz również zainstalować dodatkowe składniki, zobacz [Konfiguracja niestandardowa dla Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="full-script"></a>Pełny skrypt

Oto pełny skrypt, który tworzy środowisko Azure-SSIS Integration Runtime.

```powershell
### Azure Data Factory information
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - Must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime information - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2
# Azure-SSIS IR edition/license info: Standard or Enterprise
$AzureSSISEdition = "Standard" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported, but for other nodes, up to (2 x number of cores) are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database server with virtual network service endpoints/Managed Instance with a private endpoint/on-premises data without configuring Self-Hosted IR, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Please use the same subnet as the one used with your Azure SQL Database server with virtual network service endpoints or a different subnet than the one used for your Managed Instance with a private endpoint

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or Managed Instance name.DNS prefix.database.windows.net or Managed Instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, please ensure that there is no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure Active Directory (AAD)
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for AAD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for AAD authentication]"
# For the basic pricing tier, specify "Basic", not "B" - For standard/premium/elastic pool tiers, specify "S0", "S1", "S2", "S3", etc., see https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for Managed Instance]"

### Sign in and select subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName

### Validate the connection to database server
# Validate only if you use SSISDB and do not use VNet or AAD authentication
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

### Configure virtual network
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

    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword)) # Add CatalogAdminCredential parameter if you do not use AAD authentication
    {
        $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
        $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                                   -DataFactoryName $DataFactoryName `
                                                   -Name $AzureSSISName `
                                                   -CatalogAdminCredential $serverCreds
    }
}

# Add SetupScriptContainerSasUri parameter when you use custom setup
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}

### Start integration runtime
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

## <a name="azure-resource-manager-template"></a>Szablon usługi Azure Resource Manager

W tej sekcji użyjesz szablonu Azure Resource Manager, aby utworzyć środowisko Azure-SSIS Integration Runtime. Oto przykład przewodnika:

1. Utwórz plik JSON z następującym szablonem Azure Resource Manager. Zastąp wartości w nawiasach ostrych (symbolach) własnymi wartościami.

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

2. Aby wdrożyć szablon Azure Resource Manager, uruchom polecenie New-AzResourceGroupDeployment, jak pokazano w poniższym przykładzie, gdzie ADFTutorialResourceGroup to nazwa grupy zasobów, a ADFTutorialARM. JSON to plik, który zawiera definicję JSON dla Twoja Fabryka danych i Azure-SSIS IR.

    ```powershell
    New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    To polecenie tworzy fabrykę danych i Azure-SSIS IR w niej, ale nie uruchamia środowiska IR.

3. Aby rozpocząć Azure-SSIS IR, uruchom polecenie Start-AzDataFactoryV2IntegrationRuntime:

    ```powershell
    Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "<Resource Group Name>" `
                                                 -DataFactoryName "<Data Factory Name>" `
                                                 -Name "<Azure SSIS IR Name>" `
                                                 -Force
    ```

> [!NOTE]
> Bez czasu instalacji niestandardowej ten proces powinien zostać zakończony w ciągu 5 minut, ale może potrwać około 20-30 minut w przypadku Azure-SSIS IR dołączenia do sieci wirtualnej.
>
> Jeśli używasz SSISDB, usługa ADF będzie łączyć się z serwerem bazy danych w celu przygotowania SSISDB. Konfiguruje również uprawnienia i ustawienia dla sieci wirtualnej, jeśli określono, i przyłączy Azure-SSIS IR do sieci wirtualnej.
> 
> Podczas aprowizacji Azure-SSIS IR są również instalowane narzędzia do redystrybucyjny i pakiet Azure Feature Pack dla usług SSIS. Te składniki zapewniają łączność z plikami programu Excel/Access i różnymi źródłami danych platformy Azure, a także ze źródłami danych, które są już obsługiwane przez wbudowane składniki. Możesz również zainstalować dodatkowe składniki, zobacz [Konfiguracja niestandardowa dla Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="deploy-ssis-packages"></a>Wdrażanie pakietów usług SSIS

Jeśli używasz SSISDB, możesz wdrożyć w nim pakiety i uruchamiać je na Azure-SSIS IR przy użyciu narzędzi SSDT/SSMS, które łączą się z serwerem bazy danych za pośrednictwem swojego punktu końcowego serwera.  W przypadku serwera Azure SQL Database/wystąpienia zarządzanego w programie z prywatnym punktem końcowym/zarządzanym z publicznym punktem końcowym format punktu `<server name>.database.windows.net`końcowego serwera jest / / `<server name>.<dns prefix>.database.windows.net` `<server name>.public.<dns prefix>.database.windows.net,3342`odpowiednio. Jeśli nie korzystasz z programu SSISDB, możesz wdrożyć pakiety w systemach plików/udziałach plików/Azure Files i uruchamiać je na Azure-SSIS IR przy `dtinstall` użyciu / / `dtutil` `dtexec` narzędzi wiersza polecenia. Aby uzyskać więcej informacji, zobacz [wdrażanie pakietów SSIS](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server). W obu przypadkach wdrożone pakiety można również uruchomić na Azure-SSIS IR przy użyciu działania wykonywania pakietu SSIS w potokach ADF, zobacz [wywołania wykonywania pakietu SSIS jako pierwszej klasy działania ADF](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).

## <a name="next-steps"></a>Następne kroki

Zobacz również inne tematy Azure-SSIS IR w tej dokumentacji:

- [Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime). Ten artykuł zawiera ogólne informacje na temat środowiska Integration Runtime, w tym Azure-SSIS IR.
- [Monitoruj Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime). W tym artykule pokazano, jak pobrać i zrozumieć informacje o Azure-SSIS IR.
- [Zarządzaj Azure-SSIS IR](manage-azure-ssis-integration-runtime.md). W tym artykule przedstawiono sposób zatrzymywania, uruchamiania lub usuwania Azure-SSIS IR — przedstawiono w nim również sposób skalowania Azure-SSIS IR przez dodanie kolejnych węzłów.
- [Dołącz Azure-SSIS IR do sieci wirtualnej](join-azure-ssis-integration-runtime-virtual-network.md). Ten artykuł zawiera informacje dotyczące przyłączania Azure-SSIS IR do sieci wirtualnej.