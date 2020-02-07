---
title: Skonfiguruj własne środowisko Integration Runtime jako serwer proxy dla usług SSIS
description: Dowiedz się, jak skonfigurować Integration Runtime samodzielne jako serwer proxy dla Azure-SSIS Integration Runtime.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 02/06/2020
ms.openlocfilehash: b20a615691d95c04574e2909f69b5a83a97f9d14
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/06/2020
ms.locfileid: "77048949"
---
# <a name="configure-self-hosted-ir-as-a-proxy-for-azure-ssis-ir-in-adf"></a>Skonfiguruj własne środowisko IR jako serwer proxy dla Azure-SSIS IR w usłudze ADF

W tym artykule opisano sposób uruchamiania pakietów SQL Server Integration Services (SSIS) na Azure-SSIS Integration Runtime (IR) w Azure Data Factory (ADF) z własnym hostowanym systemem IR skonfigurowanym jako serwer proxy.  Ta funkcja umożliwia dostęp do danych lokalnych bez [przyłączania Azure-SSIS IR do sieci wirtualnej](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).  Jest to przydatne, gdy sieć firmowa ma nadmiernie złożone zasady konfiguracji/restrykcyjne umożliwiające wprowadzanie w niej Azure-SSIS IR.

Ta funkcja podzieli pakiet zawierający zadanie przepływu danych z lokalnym źródłem danych na dwa zadania przejściowe: pierwszy uruchomiony na samoobsługowym środowisku IR najpierw przenosi dane z lokalnego źródła danych do obszaru przejściowego na platformie Azure Blob Storage, podczas gdy Druga z nich uruchomiona w Azure-SSIS IR następnie przenosi dane z obszaru tymczasowego do planowanego miejsca docelowego danych.

Ta funkcja udostępnia również inne zalety/możliwości, które umożliwiają udostępnienie samodzielnego środowiska IR w regionach, które nie są jeszcze obsługiwane przez Azure-SSIS IR, umożliwiają publiczny statyczny adres IP Twojego własnego środowiska IR na zaporze źródeł danych itp.

## <a name="prepare-self-hosted-ir"></a>Przygotuj własne środowisko IR

Aby móc korzystać z tej funkcji, najpierw musisz utworzyć ADF i udostępnić w niej Azure-SSIS IR, jeśli nie zostało to jeszcze zrobione, zgodnie z artykułem [jak zainicjować obsługę administracyjną Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure) .

Następnie musisz zainicjować obsługę własnego środowiska IR w tym samym PODAJNIKu, w którym Zainicjowano obsługę Azure-SSIS IR, postępując zgodnie z [tematem Tworzenie własnego artykułu IR](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime) .

Na koniec należy pobrać i zainstalować najnowszą wersję własnego środowiska IR, a także dodatkowe sterowniki i środowisko uruchomieniowe na maszynie lokalnej/maszynie wirtualnej platformy Azure (VM) w następujący sposób:
- Pobierz i zainstaluj najnowszą wersję własnego środowiska IR z [tej lokalizacji](https://www.microsoft.com/download/details.aspx?id=39717).
- Jeśli używasz łączników OLEDB w pakietach, Pobierz i zainstaluj odpowiednie sterowniki OLEDB na tym samym komputerze, na którym zainstalowano samoobsługowe środowisko IR, jeśli jeszcze tego nie zrobiono.  Jeśli używasz starszej wersji sterownika OLEDB dla SQL Server (SQLNCLI), możesz pobrać wersję 64-bitową z tego [miejsca](https://www.microsoft.com/download/details.aspx?id=50402).  Jeśli używasz najnowszej wersji sterownika OLEDB dla SQL Server (MSOLEDBSQL), możesz pobrać 64-bitową wersję z tego [miejsca](https://www.microsoft.com/download/details.aspx?id=56730).  Jeśli używasz sterowników OLEDB dla innych systemów baz danych, takich jak PostgreSQL, MySQL, Oracle itp., możesz pobrać 64-bitową wersję z odpowiednich witryn sieci Web.
- Pobierz i zainstaluj środowisko C++ uruchomieniowe programu Visual (VC) na tym samym komputerze, na którym jest zainstalowany samoobsługowy aparat IR, jeśli jeszcze tego nie zrobiono.  W [tym miejscu](https://www.microsoft.com/download/details.aspx?id=40784)możesz pobrać wersję 64-bitową.

## <a name="prepare-azure-blob-storage-linked-service-for-staging"></a>Przygotowywanie usługi połączonej Blob Storage Azure na potrzeby przemieszczania

Utwórz połączoną usługę Azure Blob Storage w ramach tego samego PODAJNIKa systemu, w którym zainicjowano Azure-SSIS IR, jeśli jeszcze tego nie zrobiono, wykonując czynności opisane w artykule [Tworzenie połączonej usługi ADF](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-linked-service) .  Upewnij się, że:
- Wybrano **BLOB Storage platformy Azure** do **przechowywania danych**
- Wybrano **AutoResolveIntegrationRuntime** do **połączenia za pośrednictwem środowiska Integration Runtime**
- Dla **metody uwierzytelniania** wybrano **Identyfikator URI sygnatury dostępu współdzielonego** dla **klucza konta**//jednostki **usługi**

>[!TIP]
>Po wybraniu  **głównej usługi** Udziel co najmniej **roli współautor danych obiektu blob magazynu**. Aby uzyskać więcej informacji, zapoznaj się z tematem [Azure Blob Storage Connector](connector-azure-blob-storage.md#linked-service-properties).

![Przygotowywanie usługi połączonej Blob Storage Azure na potrzeby przemieszczania](media/self-hosted-integration-runtime-proxy-ssis/shir-azure-blob-storage-linked-service.png)

## <a name="configure-azure-ssis-ir-with-self-hosted-ir-as-a-proxy"></a>Konfigurowanie Azure-SSIS IR przy użyciu samodzielnego środowiska IR jako serwera proxy

Po przygotowaniu samodzielnego środowiska IR i połączonej usługi Azure Blob Storage na potrzeby przemieszczania możesz teraz skonfigurować nowe/istniejące Azure-SSIS IR za pomocą samoobsługowego środowiska IR jako serwera proxy w portalu i aplikacji ADF.  Jeśli istniejąca Azure-SSIS IR jest uruchomiona, Zatrzymaj ją przed wykonaniem tej czynności, a następnie uruchom ją ponownie.

1. W panelu ustawień środowiska Integration Runtime przejdź do sekcji **Ustawienia ogólne** i **Ustawienia SQL** , wybierając przycisk **dalej** . 

1. W sekcji **Ustawienia zaawansowane** :

   1. Zaznacz pole wyboru **skonfiguruj Integration Runtime samodzielnego jako serwer proxy dla Azure-SSIS Integration Runtime** . 

   1. W przypadku **Samodzielnej Integration Runtime**Wybierz istniejące własne środowisko IR jako serwer proxy dla Azure-SSIS IR.

   1. W przypadku **usługi połączonej z magazynem przemieszczania**wybierz istniejącą połączoną usługę Azure Blob Storage lub Utwórz nową na potrzeby przemieszczania.

   1. W polu **ścieżka tymczasowa**Określ kontener obiektów BLOB na wybranym koncie usługi Azure Blob Storage lub pozostaw to pole puste, aby użyć domyślnego ustawienia do przemieszczania.

   1. Wybierz przycisk **Kontynuuj**.

   ![Ustawienia zaawansowane przy użyciu samodzielnego środowiska IR](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

Możesz również skonfigurować nowe/istniejące Azure-SSIS IR za pomocą samoobsługowego środowiska IR jako serwera proxy przy użyciu programu PowerShell.

```powershell
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
$AzureSSISName = "[your Azure-SSIS IR name]"
# Self-hosted integration runtime info - This can be configured as a proxy for on-premises data access 
$DataProxyIntegrationRuntimeName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingLinkedServiceName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingPath = "" # OPTIONAL to configure a proxy for on-premises data access 

# Add self-hosted integration runtime parameters if you configure a proxy for on-premises data accesss
if(![string]::IsNullOrEmpty($DataProxyIntegrationRuntimeName) -and ![string]::IsNullOrEmpty($DataProxyStagingLinkedServiceName))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -DataProxyIntegrationRuntimeName $DataProxyIntegrationRuntimeName `
        -DataProxyStagingLinkedServiceName $DataProxyStagingLinkedServiceName

    if(![string]::IsNullOrEmpty($DataProxyStagingPath))
    {
        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -DataProxyStagingPath $DataProxyStagingPath
    }
}
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force
```

## <a name="enable-ssis-packages-to-connect-by-proxy"></a>Zezwól na łączenie pakietów SSIS z serwerem proxy

Korzystając z najnowszych SSDT z rozszerzeniem projektów SSIS dla programu Visual Studio, które można pobrać z tego [miejsca](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects) lub jako autonomicznego Instalatora, który można pobrać z tego [miejsca](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer), można znaleźć nową właściwość **ConnectByProxy** , która została dodana w menedżerach połączeń plików OLEDB/Flat.  

Podczas projektowania nowych pakietów zawierających zadania przepływu danych ze źródłami plików OLEDB/Flat do uzyskiwania dostępu do baz danych i plików lokalnie można włączyć tę właściwość, ustawiając dla niej **wartość true** w panelu Właściwości odpowiednich menedżerów połączeń.

![Włącz Właściwość ConnectByProxy](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-manager-properties.png)

Możesz również włączyć tę właściwość podczas uruchamiania istniejących pakietów bez konieczności ręcznej zmiany ich po jednym.  Dostępne są dwie opcje:
- Otwieranie, ponowna kompilacja i ponowne wdrażanie projektu zawierającego te pakiety przy użyciu najnowszych SSDT do uruchomienia na Azure-SSIS IR: Właściwość można następnie włączyć, **ustawiając dla odpowiednich** menedżerów połączeń, które są wyświetlane na karcie **Menedżer połączeń** w oknie podręcznym wykonaj pakiet, podczas uruchamiania pakietów z programu SSMS.

  ![Włącz ConnectByProxy Property2](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssms.png)

  Właściwość można również włączyć, ustawiając jej **wartość na true** dla odpowiednich menedżerów połączeń, które są wyświetlane na karcie **menedżerowie połączeń** w [działaniu pakiet SSIS](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) w przypadku uruchamiania pakietów w potokach ADF.
  
  ![Włącz ConnectByProxy Property3](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssis-activity.png)

- Ponowne wdrażanie projektu zawierającego te pakiety do uruchomienia na urządzeniu IR usługi SSIS: Właściwość można następnie włączyć, podając jej ścieżkę właściwości, `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`i ustawiając ją na **wartość true** jako przesłonięcie właściwości na karcie **Zaawansowane** w oknie podręcznym wykonywania pakietu w przypadku uruchamiania pakietów z programu SSMS.

  ![Włącz ConnectByProxy Property4](media/self-hosted-integration-runtime-proxy-ssis/shir-advanced-tab-ssms.png)

  Właściwość można również włączyć, podając jej ścieżkę właściwości, `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`i ustawiając ją na **wartość true** w przypadku zastąpienia właściwości na karcie **przesłonięcie** [działania pakietu SSIS](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) w przypadku uruchamiania pakietów w potokach ADF.
  
  ![Włącz ConnectByProxy Property5](media/self-hosted-integration-runtime-proxy-ssis/shir-property-overrides-tab-ssis-activity.png)

## <a name="debug-the-first-and-second-staging-tasks"></a>Debugowanie pierwszego i drugiego zadania przemieszczania

Na własnym hostowanym środowisku IR możesz znaleźć dzienniki środowiska uruchomieniowego w folderze `C:\ProgramData\SSISTelemetry` i dzienniki wykonywania pierwszych zadań przemieszczania w folderze `C:\ProgramData\SSISTelemetry\ExecutionLog`.  Dzienniki wykonywania drugiego zadania przemieszczania znajdują się w SSISDB lub określonych ścieżkach rejestrowania, w zależności od tego, czy pakiety są przechowywane odpowiednio w SSISDB, czy w systemie plików/udziałach plików/Azure Files.  Unikatowe identyfikatory pierwszych zadań przemieszczania można także znaleźć w dziennikach wykonywania drugiego zadania przemieszczania, np. 

![Unikatowy identyfikator pierwszego zadania przemieszczania](media/self-hosted-integration-runtime-proxy-ssis/shir-first-staging-task-guid.png)

## <a name="using-windows-authentication-in-staging-tasks"></a>Korzystanie z uwierzytelniania systemu Windows w zadaniach przemieszczania

Jeśli zadania przemieszczania w samoobsługowym środowisku IR wymagają uwierzytelniania systemu Windows, musisz [skonfigurować pakiety usług SSIS tak, aby korzystały z tego samego uwierzytelniania systemu Windows](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth?view=sql-server-ver15). Zadania przemieszczania zostaną wywołane przy użyciu samodzielnego konta usługi IR (`NT SERVICE\DIAHostService` domyślnie), a Twoje magazyny danych będą dostępne przy użyciu konta uwierzytelniania systemu Windows. Oba konta wymagają przypisania pewnych zasad zabezpieczeń do nich. W związku z tym na samoobsługowym komputerze IR Otwórz `Local Security Policy` -> `Local Policies` -> `User Rights Assignment` i wykonaj następujące czynności.
- Przypisz **limity przydziałów pamięci dla procesu** i **Zastąp zasady tokenów na poziomie procesu** do samodzielnego konta usługi IR. Należy to zrobić automatycznie podczas instalacji własnego środowiska IR przy użyciu domyślnego konta usługi. W przypadku użycia innego konta usługi Przypisz do niego te same zasady.
- Przypisz **Logowanie jako zasady usługi** do konta uwierzytelniania systemu Windows.

## <a name="billing-for-the-first-and-second-staging-tasks"></a>Rozliczenia dotyczące pierwszego i drugiego zadania przemieszczania

Pierwsze zadania przemieszczania uruchomione na samoobsługowym środowisku IR będą rozliczane osobno w taki sam sposób, jak wszystkie działania związane z przenoszeniem danych działające w ramach własnego środowiska IR są rozliczane zgodnie z opisem w artykule [Cennik potoku danych ADF](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) .

Pozostałe zadania przemieszczania uruchomione w Azure-SSIS IR nie będą rozliczane oddzielnie, ale w przypadku uruchamiania Azure-SSIS IR zostanie naliczona wartość określona w artykule dotyczącym [cennika Azure-SSIS IR](https://azure.microsoft.com/pricing/details/data-factory/ssis/) .

## <a name="current-limitations"></a>Bieżące ograniczenia

- Obecnie obsługiwane są tylko zadania przepływu danych korzystające z menedżerów połączeń ODBC/OLEDB/prostych plików oraz ODBC/OLEDB/prostych źródeł plików lub lokalizacji docelowej OLEDB. 
- Obecnie są obsługiwane tylko połączone usługi platformy Azure Blob Storage skonfigurowane z **kluczem konta**/**URI SAS**/uwierzytelnianie **nazwy głównej usługi** .

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu własnego środowiska IR jako serwera proxy dla Azure-SSIS IR można wdrażać i uruchamiać pakiety, aby uzyskiwać dostęp do danych lokalnych jako działania pakietu SSIS w potokach ADF, zobacz [uruchamianie pakietów SSIS jako działania pakietu SSIS w potokach ADF](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).
