---
title: Tworzenie środowiska Azure-SSIS Integration Runtime w usłudze Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć środowisko Azure-SSIS Integration Runtime w usłudze Azure Data Factory umożliwiające wdrażanie i uruchamianie pakietów SSIS na platformie Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/20/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: d30ec0765627ec173f0027e49f44cb77f6b26ac6
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59361476"
---
# <a name="create-azure-ssis-integration-runtime-in-azure-data-factory"></a>Tworzenie środowiska Azure-SSIS Integration Runtime w usłudze Azure Data Factory

W tym artykule przedstawiono kroki do aprowizacji środowiska Azure-SSIS Integration Runtime (IR) w usłudze Azure Data Factory (ADF). Następnie można wdrażać i uruchamiać pakiety usług SQL Server Integration Services (SSIS) na tym środowisku integration runtime w systemie Azure można użyć programu SQL Server Data Tools (SSDT) lub SQL Server Management Studio (SSMS).

[Samouczka: Wdrażanie pakietów usług SSIS na platformie Azure](tutorial-create-azure-ssis-runtime-portal.md) dowiesz się, jak utworzyć środowisko IR Azure-SSIS przy użyciu serwera Azure SQL Database do hosta bazy danych wykazu usług SSIS (SSISDB). Ten artykuł stanowi rozszerzenie samouczka i pokazuje, jak wykonywać następujące czynności:

- Opcjonalnie można użyć serwera Azure SQL Database przy użyciu sieci wirtualnej usługi punktów końcowych/zarządzanego wystąpienia do hostowania bazy SSISDB. Aby uzyskać wskazówki dotyczące wyboru typu serwera baz danych do hostowania bazy danych SSISDB, zobacz [Porównanie pojedynczych baz danych/elastycznych pul usługi SQL Database i wystąpienia zarządzanego](create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance). Jako warunek wstępny należy dołączyć środowiska Azure-SSIS IR do sieci wirtualnej i konfigurowanie uprawnień/ustawień sieci wirtualnej zgodnie z potrzebami. Zobacz [Join Azure-SSIS IR do sieci wirtualnej](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

- Opcjonalnie użyć uwierzytelniania usługi Azure Active Directory (AAD) za pomocą tożsamości zarządzanej dla usługi ADF do łączenia się z serwerem bazy danych. Jako warunek wstępny, musisz dodać tożsamości zarządzanej dla usługi ADF jako użytkownika zawartej bazy danych, pozwalając w usługi Azure SQL Database server/zarządzanego wystąpienia bazy danych SSISDB, zobacz [Włącz uwierzytelnianie usługi AAD dla środowiska Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir).

## <a name="overview"></a>Omówienie

W tym artykule przedstawiono różne sposoby inicjowania obsługi administracyjnej Azure-SSIS IR:

- [Azure Portal](#azure-portal)
- [Azure PowerShell](#azure-powershell)
- [Szablon usługi Azure Resource Manager](#azure-resource-manager-template)

Podczas tworzenia środowiska Azure-SSIS IR usługi ADF łączy się z usługi Azure SQL Database server/zarządzanego wystąpienia w celu przygotowania bazy danych SSISDB. Ponadto umożliwia skonfigurowanie uprawnień/ustawień sieci wirtualnej, jeśli określona i dołączania środowiska Azure-SSIS IR do sieci wirtualnej.

Podczas aprowizowania środowiska IR Azure-SSIS instalowane są również pakiety Azure Feature Pack dla usług SSIS i Access Redistributable. Te składniki zapewniają łączność z plikami programu Excel i Access i różnych źródeł danych na platformie Azure, oprócz źródeł danych obsługiwanych przez wbudowane składniki. Możesz też zainstalować dodatkowe składniki. Aby uzyskać więcej informacji, zobacz [Niestandardowa konfiguracja środowiska Azure SSIS Integration Runtime](how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Subskrypcja platformy Azure**. Jeśli nie masz już subskrypcję, możesz utworzyć [bezpłatna wersja próbna](https://azure.microsoft.com/pricing/free-trial/) konta.

- **Azure serwer usługi SQL Database lub wystąpienia zarządzanego**. Jeśli nie masz już serwer bazy danych, można utworzyć w witrynie Azure portal przed rozpoczęciem pracy. Ten serwer będzie hostem bazy danych SSISDB. Firma Microsoft zaleca tworzenie serwera bazy danych w tym samym regionie platformy Azure jako środowiska integration runtime. Ta konfiguracja pozwala środowiska integration runtime zapisywać dzienniki wykonywania SSISDB bez wykraczania poza granice regionów świadczenia usługi Azure. Na podstawie wybranej bazy danych serwera, bazy danych SSISDB można utworzyć w Twoim imieniu jako pojedynczej bazy danych, część puli elastycznej, lub w wystąpieniu usługi zarządzane i dostępne w sieci publicznej lub po dołączeniu do sieci wirtualnej. Aby uzyskać listę obsługiwanych warstw cenowych usługi Azure SQL Database, zobacz [limity zasobów bazy danych SQL](../sql-database/sql-database-resource-limits.md).

    Upewnij się, że usługi Azure SQL Database server/zarządzanego wystąpienia nie istnieje już baza SSISDB. Aprowizacja środowiska Azure-SSIS IR nie obsługuje przy użyciu istniejącej bazy danych SSISDB.

- **Usługa Azure Resource Manager virtual network (opcjonalnie)**. Musi mieć z siecią wirtualną usługi Azure Resource Manager, jeśli co najmniej jeden z następujących warunków jest spełniony:

  - Bazy danych SSISDB są hostowane na serwerze Azure SQL Database za pomocą punktów końcowych usługi sieci wirtualnej lub w wystąpieniu zarządzanym, która znajduje się wewnątrz sieci wirtualnej.
  - Aby nawiązać połączenie z danymi lokalnymi magazynami z pakietów usług SSIS działającymi na usługi platformy Azure-SSIS IR.

- Zainstalowanie programu **Azure PowerShell**. Postępuj zgodnie z instrukcjami [jak zainstalować i skonfigurować program Azure PowerShell](/powershell/azure/install-az-ps), aby uruchomić skrypt programu PowerShell do aprowizacji środowiska Azure-SSIS IR.

### <a name="region-support"></a>Obsługa regionu

Aby uzyskać listę regionów świadczenia usługi Azure, w których ADF i Azure-SSIS IR są obecnie dostępne, zobacz [ADF + SSIS IR Dostępność wg regionu](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all).

### <a name="compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance"></a>Porównaj puli pojedynczej bazy danych/elastycznej bazy danych SQL i wystąpienie zarządzane usługi SQL Database

W poniższej tabeli porównano niektóre funkcje usługi Azure SQL Database serwer i wystąpienie zarządzane usługi w odniesieniu do środowiska IR Azure-SSIR:

| Cecha | pojedynczej bazy danych/elastycznej puli| Wystąpienie zarządzane |
|---------|--------------|------------------|
| **Planowanie** | SQL Server Agent jest niedostępna.<br/><br/>Zobacz [zaplanować wykonanie pakietu, w potoku usługi ADF](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages?view=sql-server-2017#activity).| Agentem zarządzanym — wystąpienie jest dostępna. |
| **Uwierzytelnianie** | Można utworzyć bazy danych SSISDB z użytkowników zawartej bazy danych, reprezentujący grupy usługi AAD za pomocą tożsamości zarządzanej usługi ADF jako element członkowski w **db_owner** roli.<br/><br/>Zobacz [Włączanie usługi Azure AD uwierzytelniania do utworzenia bazy danych SSISDB na serwerze Azure SQL Database](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database). | Można utworzyć bazy danych SSISDB z zarządzaną tożsamością usługi ADF reprezentujący użytkownikiem zawartej bazy danych. <br/><br/>Zobacz [Włączanie usługi Azure AD uwierzytelniania można utworzyć bazy danych SSISDB w wystąpieniu zarządzanym usługi Azure SQL Database](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database-managed-instance). |
| **Warstwa usług** | Po utworzeniu środowiska IR Azure-SSIS przy użyciu serwera usługi Azure SQL Database można wybrać warstwę usług dla bazy danych SSISDB. Istnieje wiele warstw usługi. | Po utworzeniu środowiska Azure-SSIS IR za pomocą wystąpienia zarządzanego nie można wybrać warstwę usług dla bazy danych SSISDB. Wszystkie bazy danych w wystąpieniu zarządzanemu udostępnianie tego samego zasobu przydzielony do tego wystąpienia. |
| **Sieć wirtualna** | Obsługuje tylko usługi Azure Resource Manager sieci wirtualnych na potrzeby środowiska IR Azure-SSIS do dołączenia do serwera Azure SQL Database za pomocą punktów końcowych usługi sieci wirtualnej lub wymagają dostępu do lokalnych magazynów danych. | Obsługuje tylko usługi Azure Resource Manager sieci wirtualnych na potrzeby środowiska IR Azure-SSIS do przyłączenia. Sieć wirtualna jest zawsze wymagany.<br/><br/>Jeśli dołączysz do środowiska Azure-SSIS IR do tej samej sieci wirtualnej jako wystąpienia zarządzanego, upewnij się, że środowiska IR Azure-SSIS znajduje się w innej podsieci niż wystąpienia zarządzanego. Jeśli dołączysz do programu środowiska IR Azure-SSIS do innej sieci wirtualnej niż wystąpienia zarządzanego, firma Microsoft zaleca komunikacji równorzędnej sieci wirtualnej lub sieci wirtualnej do połączenia sieci wirtualnej. Zobacz [połączyć aplikację z wystąpienia zarządzanego Azure SQL Database](../sql-database/sql-database-managed-instance-connect-app.md). |
| **Transakcje rozproszone** | Obsługiwane przez transakcje elastyczne. Transakcje rozproszone transakcji Koordynator MSDTC (Microsoft) nie są obsługiwane. Jeśli pakiety usług SSIS za pomocą usługi MSDTC koordynowanie transakcji rozproszonych, należy rozważyć Migrowanie do transakcje elastyczne usługi Azure SQL Database. Aby uzyskać więcej informacji, zobacz [transakcje rozproszone w bazach danych w chmurze](../sql-database/sql-database-elastic-transactions-overview.md). | Nieobsługiwane. |
| | | |

## <a name="azure-portal"></a>Azure Portal

W tej sekcji użyjesz witryny Azure portal, w szczególności ADF użytkownika Interfejsu / aplikacji, aby utworzyć Azure-SSIS IR.

### <a name="create-a-data-factory"></a>Tworzenie fabryki danych

1. Uruchom przeglądarkę internetową **Microsoft Edge** lub **Google Chrome**. Obecnie interfejs użytkownika usługi Data Factory jest obsługiwany tylko przez przeglądarki internetowe Microsoft Edge i Google Chrome.
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
1. Kliknij przycisk **Nowy** w lewym menu, kliknij pozycję **Dane + analiza**, a następnie kliknij pozycję **Data Factory**.

   ![Nowy-> Fabryka danych](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)

1. Na stronie **Nowa fabryka danych** wprowadź jako **nazwę** wartość **MyAzureSsisDataFactory**.

   ![Strona Nowa fabryka danych](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)

   Nazwa fabryki danych platformy Azure musi być **globalnie unikatowa**. Jeśli wystąpi poniższy błąd, zmień nazwę fabryki danych (np. twojanazwaMyAzureSsisDataFactory) i spróbuj utworzyć ją ponownie. Artykuł [Data Factory — Naming Rules (Usługa Data Factory — reguły nazewnictwa)](naming-rules.md) zawiera reguły nazewnictwa artefaktów usługi Data Factory.

   `Data factory name “MyAzureSsisDataFactory” is not available`

1. Wybierz **subskrypcję** Azure, w której chcesz utworzyć fabrykę danych.
1. Dla opcji **Grupa zasobów** wykonaj jedną z następujących czynności:

   - Wybierz pozycję **Użyj istniejącej**, a następnie wybierz istniejącą grupę zasobów z listy rozwijanej.
   - Wybierz pozycję **Utwórz nową**, a następnie wprowadź nazwę grupy zasobów.

   Informacje na temat grup zasobów znajdują się w artykule [Using resource groups to manage your Azure resources](../azure-resource-manager/resource-group-overview.md) (Używanie grup zasobów do zarządzania zasobami platformy Azure).

1. Wybierz opcję **V2** w obszarze **Wersja**.
1. Na liście **lokalizacja** wybierz lokalizację fabryki danych. Na liście są wyświetlane tylko lokalizacje obsługiwane na potrzeby tworzenia fabryk danych.
1. Wybierz opcję **Przypnij do pulpitu nawigacyjnego**.
1. Kliknij pozycję **Utwórz**.
1. Na pulpicie nawigacyjnym jest widoczny następujący kafelek ze stanem: **Wdrażanie fabryki danych**.

    ![kafelek Wdrażanie fabryki danych](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)

1. Po zakończeniu tworzenia zostanie wyświetlona strona **Fabryka danych**, jak pokazano na poniższej ilustracji.

    ![Strona główna fabryki danych](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)

1. Kliknij pozycję **Tworzenie i monitorowanie**, aby w osobnej karcie uruchomić interfejs użytkownika usługi Data Factory.

### <a name="provision-an-azure-ssis-integration-runtime"></a>Aprowizowanie środowiska Azure SSIS Integration Runtime

1. Na stronie wprowadzenia kliknij kafelek **Konfigurowanie środowiska SSIS Integration Runtime**.

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

    a. W polu **Subskrypcja** określ subskrypcję platformy Azure, z której korzysta Twój serwer bazy danych hostujący bazę danych SSISDB.

    b. W polu **Lokalizacja** określ lokalizację serwera bazy danych hostującego bazę danych SSISDB. Zalecamy wybranie tej samej lokalizacji co w przypadku środowiska Integration Runtime.

    d. W polu **Punkt końcowy serwera bazy danych wykazu** wybierz punkt końcowy serwera bazy danych hostującego bazę danych SSISDB. W zależności od wybranego serwera bazy danych baza danych SSISDB może zostać utworzona w Twoim imieniu jako pojedyncza baza danych, jako część elastycznej puli lub w ramach wystąpienia zarządzanego i może być dostępna w sieci publicznej lub przez dołączenie do sieci wirtualnej.

    d. Pole wyboru **Użyj uwierzytelniania usługi AAD...** służy do wyboru metody uwierzytelniania na potrzeby serwera bazy danych hostującego bazę danych SSISDB: Bazy danych SQL lub Azure Active Directory (AAD) za pomocą tożsamości zarządzanej dla usługi Azure Data Factory. Jeśli możesz sprawdzić, należy dodać tożsamości zarządzanej dla usługi ADF do grupy usługi AAD ma uprawnienia dostępu do serwera bazy danych, zobacz [Włącz uwierzytelnianie usługi AAD dla środowiska Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir).

    e. W polu **Nazwa administratora** wprowadź nazwę użytkownika uwierzytelnienia SQL dla serwera bazy danych hostującego bazę danych SSISDB.

    f. W polu **Hasło administratora** wprowadź hasło uwierzytelnienia SQL dla serwera bazy danych hostującego bazę danych SSISDB.

    g. W polu **Warstwa serwera bazy danych katalogu** wybierz warstwę usługi dla serwera bazy hostującego bazę danych SSISDB: warstwa Podstawowa/Standardowa/Premium lub nazwa puli elastycznej.

    h. Kliknij pozycję **Testuj połączenie**. Jeśli test zakończył się pomyślnie, kliknij przycisk **Dalej**.

4. Na stronie **Ustawienia zaawansowane** wykonaj następujące czynności:

    ![Ustawienia zaawansowane](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

    a. W polu **Maksymalna liczba równoległych wykonań na węzeł** wybierz maksymalną liczbę pakietów, które mogą być wykonywane jednocześnie na węzeł w klastrze środowiska Integration Runtime. Wyświetlane są tylko obsługiwane liczby pakietów. Wybierz niską wartość, jeśli chcesz użyć więcej niż jednego rdzenia, aby uruchomić jeden pakiet dużych/ciężki, który jest obliczeń/pamięci-o znacznym wykorzystaniu. Wybierz dużą liczbę, jeśli chcesz uruchamiać małe pakiety w ramach jednego rdzenia.

    b. Aby uzyskać **identyfikator URI sygnatury dostępu współdzielonego kontenera instalacji niestandardowej** opcjonalnie wprowadź identyfikator URI sygnatury dostępu współdzielonego kontenera magazynu Azure Storage Blob, gdzie przechowywany jest skrypt instalacji i skojarzone z nim pliki. Aby uzyskać więcej informacji, zobacz [Instalacja niestandardowa dla środowiska Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup).

5. Na **Wybieranie sieci wirtualnej...**  pole wyboru, wybierz, czy chcesz dołączyć środowisko integration runtime do sieci wirtualnej. Sprawdź, czy usługi Azure SQL Database za pomocą sieci wirtualnej usługi punktów końcowych/zarządzanego wystąpienia do hostowania bazy SSISDB wymagają dostępu do danych lokalnych. oznacza to, ma lokalne źródłami/miejscami docelowymi danych w pakiety usług SSIS, zobacz [Join Azure-SSIS IR do sieci wirtualnej](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Jeśli możesz sprawdzić, wykonaj następujące czynności:

   ![Ustawienia zaawansowane za pomocą sieci wirtualnej](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

    a. Aby uzyskać **subskrypcji**, wybierz subskrypcję platformy Azure, która ma sieci wirtualnej.

    b. Aby uzyskać **lokalizacji**, tej samej lokalizacji środowiska integration runtime jest zaznaczone.

    c. Aby uzyskać **typu**, wybierz typ sieci wirtualnej: Klasycznym lub na platformie Azure Resource Manager. Zaleca się, że wybierz sieć wirtualną usługi Azure Resource Manager, ponieważ wersja klasyczna sieć wirtualna wkrótce staną się przestarzałe.

    d. Aby uzyskać **nazwa sieci wirtualnej**, wybierz nazwę sieci wirtualnej. Ta sieć wirtualna powinna być tej samej sieci wirtualnej używane dla usługi Azure SQL Database za pomocą punktów końcowych usługi sieci wirtualnej/zarządzanego wystąpienia do hostowania bazy SSISDB i jednej podłączonej do sieci lokalnej.

    e. Aby uzyskać **Nazwa podsieci**, wybierz nazwę podsieci sieci wirtualnej. Powinna to być innej podsieci niż ten używany do wystąpienia zarządzanego do hostowania bazy SSISDB.

6. Kliknij przycisk **Weryfikacja sieci wirtualnej** i jeśli to się powiedzie, kliknij pozycję **Zakończ** aby rozpocząć tworzenie środowiska Azure-SSIS integration runtime.

    > [!IMPORTANT]
    > - Ten proces trwa około 20 – 30 minut
    > - Usługa Data Factory łączy się z usługą Azure SQL Database w celu przygotowania bazy danych wykazu usług SSIS (SSISDB). Ponadto umożliwia skonfigurowanie uprawnień i ustawień sieci wirtualnej, jeśli określony i dołącza nowe wystąpienie środowiska Azure-SSIS integration Runtime do sieci wirtualnej.

7. W oknie **Połączenia** w razie potrzeby przełącz do pozycji **Produkty Integration Runtime**. Kliknij przycisk **Odśwież**, aby odświeżyć status.

   ![Stan tworzenia](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

8. Użyj linków w kolumnie **akcje** kolumnę, aby zatrzymać/uruchomić, edytować lub usunąć środowisko integration runtime. Użyj ostatniego linku, aby wyświetlić kod JSON dla środowiska Integration Runtime. Przyciski edytowania i usuwania są włączone tylko wtedy, gdy środowisko IR jest zatrzymane.

   ![Środowisko IR Azure SSIS — akcje](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)

### <a name="azure-ssis-integration-runtimes-in-the-portal"></a>Środowiska Azure SSIS Integration Runtime w portalu

1. W interfejsie użytkownika usługi Azure Data Factory przejdź na kartę **Edycja**, kliknij pozycję **Połączenia**, a następnie przejdź na kartę **Środowiska Integration Runtime**, aby wyświetlić istniejące środowiska Integration Runtime w fabryce danych.

   ![Wyświetlanie istniejących środowisk IR](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

2. Kliknij pozycję **Nowe**, aby utworzyć środowisko IR Azure-SSIS.

   ![Środowisko Integration Runtime za pomocą menu](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

3. Aby utworzyć środowisko Azure-SSIS Integration Runtime, kliknij przycisk **Nowe**, jak pokazano na ilustracji.
4. W oknie Integration Runtime Setup (Konfiguracja środowiska Integration Runtime) wybierz pozycję **Lift-and-shift existing SSIS packages to execute in Azure** (Migruj metodą „lift-and-shift” istniejące pakiety usług SSIS do wykonywania na platformie Azure), a następnie kliknij przycisk **Dalej**.

   ![Określanie typu środowiska Integration Runtime](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

5. Zobacz sekcję [Aprowizowanie środowiska Azure SSIS Integration Runtime](#provision-an-azure-ssis-integration-runtime), zawierającą pozostałe kroki konfigurowania środowiska IR Azure-SSIS.

## <a name="azure-powershell"></a>Azure PowerShell

W tej sekcji użyjesz programu Azure PowerShell do utworzenia Azure-SSIS IR.

### <a name="create-variables"></a>Tworzenie zmiennych

Zdefiniuj zmienne do wykorzystania w skrypcie w tym samouczku:

```powershell
### Azure Data Factory information
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime information - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[specify a name for your Azure-SSIS IR]"
$AzureSSISDescription = "[specify a description for your Azure-SSIS IR]"
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
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database with virtual network service endpoints/Managed Instance/on-premises data, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Please use the same subnet as the one used with your Azure SQL Database with virtual network service endpoints or a different subnet than the one used for your Managed Instance

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name or Managed Instance name.DNS prefix].database.windows.net" # WARNING: Please ensure that there is no existing SSISDB, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure Active Directory (AAD)
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for AAD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for AAD authentication]"
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database or leave it empty for Managed Instance]"
```

### <a name="sign-in-and-select-subscription"></a>Zaloguj się i wybierz subskrypcję

Dodaj następujący kod skryptu, które mogą się zalogować, a następnie wybierz swoją subskrypcję platformy Azure:

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName
```

### <a name="validate-the-connection-to-database"></a>Weryfikowanie połączenia z bazą danych

Dodaj następujący skrypt, aby zweryfikować punktu końcowego serwera usługi Azure SQL Database.

```powershell
# Validate only when you do not use VNet nor AAD authentication
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

Tworzenie [grupy zasobów platformy Azure](../azure-resource-manager/resource-group-overview.md) przy użyciu [New AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) polecenia. Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi w formie grupy.

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

Uruchom następujące polecenia, aby utworzyć środowiska Azure-SSIS integration runtime, które uruchamia pakiety usług SSIS na platformie Azure.

Jeśli nie usługi Azure SQL Database za pomocą sieci wirtualnej usługi punktów końcowych/zarządzanego wystąpienia do hostowania bazy SSISDB ani wymagają dostępu do danych lokalnych, można pominąć parametrów VNetId i podsieci lub przekazać wartości puste je. W przeciwnym razie nie można pominąć je i musi przekazać prawidłowe wartości z konfiguracji sieci wirtualnej, zobacz [Join Azure-SSIS IR do sieci wirtualnej](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

Jeśli używasz wystąpienia zarządzanego do hostowania bazy SSISDB, można pominąć parametr CatalogPricingTier lub przekazać wartość pustą. W przeciwnym razie nie można go pominąć i musi przekazywać prawidłową wartość z listy obsługiwanych warstw cenowych usługi Azure SQL Database, zobacz [limity zasobów bazy danych SQL](../sql-database/sql-database-resource-limits.md).

Jeśli używasz uwierzytelniania usługi Azure Active Directory (AAD) za pomocą tożsamości zarządzanej dla usługi Azure Data Factory do łączenia się z serwerem bazy danych, można pominąć parametr CatalogAdminCredential, ale należy dodać tożsamości zarządzanej dla usługi ADF do grupy usługi AAD za pomocą uprawnienia dostępu do serwera bazy danych, zobacz [Włącz uwierzytelnianie usługi AAD dla środowiska Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir). W przeciwnym razie nie można go pominąć i należy przekazać prawidłowy obiekt tworzony na podstawie swoją nazwę administratora serwera i hasło dla uwierzytelniania SQL.

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
                                           -Subnet $SubnetName `
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogPricingTier $SSISDBPricingTier

# Add SetupScriptContainerSasUri parameter when you use custom setup
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}

# Add CatalogAdminCredential parameter when you do not use AAD authentication
if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
{
    $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
    $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -CatalogAdminCredential $serverCreds
}
```

### <a name="start-integration-runtime"></a>Uruchamianie środowiska Integration Runtime

Uruchom następujące polecenie, aby uruchomić środowisko Azure-SSIS Integration Runtime:

```powershell
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

Wykonanie tego polecenia trwa od **20 do 30 minut**.

### <a name="full-script"></a>Pełny skrypt

Oto pełny skrypt, który tworzy środowiska Azure-SSIS integration runtime.

```powershell
### Azure Data Factory information
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime information - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[specify a name for your Azure-SSIS IR]"
$AzureSSISDescription = "[specify a description for your Azure-SSIS IR]"
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
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database with virtual network service endpoints/Managed Instance/on-premises data, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Please use the same subnet as the one used with your Azure SQL Database with virtual network service endpoints or a different subnet than the one used for your Managed Instance

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name or Managed Instance name.DNS prefix].database.windows.net" # WARNING: Please ensure that there is no existing SSISDB, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure Active Directory (AAD)
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for AAD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for AAD authentication]"
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database or leave it empty for Managed Instance]"

### Log in and select subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName

### Validate the connection to database
# Validate only when you do not use VNet nor AAD authentication
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
                                           -Subnet $SubnetName `
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogPricingTier $SSISDBPricingTier

# Add SetupScriptContainerSasUri parameter when you use custom setup
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}

# Add CatalogAdminCredential parameter when you do not use AAD authentication
if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
{
    $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
    $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -CatalogAdminCredential $serverCreds
}

### Start integration runtime
write-host("##### Starting your Azure-SSIS integration runtime. This command takes 20 to 30 minutes to complete. #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

## <a name="azure-resource-manager-template"></a>Szablon usługi Azure Resource Manager

W tej sekcji możesz szablon usługi Azure Resource Manager do tworzenia środowiska Azure-SSIS integration runtime. Oto przykład krok po kroku:

1. Utwórz plik JSON przy użyciu następującego szablonu usługi Azure Resource Manager. Zastąp wartości w nawiasach (symbole zastępcze) przy użyciu własnych wartości.

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

2. Aby wdrożyć szablon usługi Azure Resource Manager, Uruchom polecenia New-AzResourceGroupDeployment, jak pokazano w poniższym przykładzie, gdzie ADFTutorialResourceGroup to nazwa grupy zasobów, a ADFTutorialARM.json to plik zawierający definicję JSON dla usługi data factory i Azure-SSIS IR.

    ```powershell
    New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    To polecenie umożliwia utworzenie usługi data factory i Azure-SSIS IR w nim, ale nie uruchamia IR.

3. Aby uruchomić środowiska IR Azure-SSIS, uruchom polecenie Start AzDataFactoryV2IntegrationRuntime:

    ```powershell
    Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "<Resource Group Name>" `
                                                 -DataFactoryName "<Data Factory Name>" `
                                                 -Name "<Azure SSIS IR Name>" `
                                                 -Force
    ```

## <a name="deploy-ssis-packages"></a>Wdrażanie pakietów usług SSIS

Teraz użyj programu SQL Server Data Tools (SSDT) lub SQL Server Management Studio (SSMS), aby wdrożyć pakiety usług SSIS na platformie Azure. Połącz z serwerem bazy danych, który hostuje katalog usług SSIS (SSISDB). Nazwa serwera bazy danych jest w formacie: &lt;Nazwa serwera usługi Azure SQL Database&gt;. database.windows.net lub &lt;nazwa wystąpienia zarządzanego. Prefiks DNS&gt;. database.windows.net. Zobacz artykuł [Deploy packages (Wdrażanie pakietów)](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server), aby uzyskać instrukcje.

## <a name="next-steps"></a>Kolejne kroki

Zobacz inne tematy Azure-SSIS IR w tej dokumentacji:

- [Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime). Ten artykuł zawiera informacje o pojęciach dotyczących środowiska integration Runtime, ogólnie rzecz biorąc w tym Azure-SSIS IR.
- [Samouczek: Wdrażanie pakietów usług SSIS na platformie Azure](tutorial-create-azure-ssis-runtime-portal.md). Ten artykuł zawiera szczegółowe instrukcje dotyczące tworzenia środowiska Azure-SSIS IR i używania bazy danych Azure SQL do hostowania wykazu usług SSIS.
- [Monitor an Azure-SSIS IR (Monitorowanie środowiska Azure-SSIS IR)](monitor-integration-runtime.md#azure-ssis-integration-runtime). W tym artykule przedstawiono sposób pobierania informacji o środowisku Azure-SSIS IR i opisów stanów w pobranych informacjach.
- [Manage an Azure-SSIS IR (Zarządzanie środowiskiem Azure-SSIS IR)](manage-azure-ssis-integration-runtime.md). W tym artykule przedstawiono sposób zatrzymywania, uruchamiania lub usuwania środowiska Azure-SSIS IR. Zawiera on również instrukcje skalowania środowiska Azure-SSIS IR do wewnątrz za pomocą dodawania do niego węzłów.
- [Join an Azure-SSIS IR to a virtual network](join-azure-ssis-integration-runtime-virtual-network.md) (Dołączanie środowiska IR Azure SSIS do sieci wirtualnej). Ten artykuł zawiera informacje o pojęciach dotyczących dołączania środowiska IR Azure-SSIS z siecią wirtualną platformy Azure. Opisano w nim kroki konfigurowania sieci wirtualnej za pomocą witryny Azure Portal tak, aby umożliwić dołączanie środowiska IR Azure-SSIS do sieci wirtualnej.
