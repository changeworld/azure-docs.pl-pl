---
title: Skonfiguruj własne środowisko Integration Runtime jako serwer proxy dla usług SSIS w Azure Data Factory | Microsoft Docs
description: Dowiedz się, jak skonfigurować samoobsługowe Integration Runtime jako serwer proxy dla usług Azure-SSIS Integration Runtime.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/01/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 2f65303cd5cda50a95c3563422c059b985ecf28a
ms.sourcegitcommit: c662440cf854139b72c998f854a0b9adcd7158bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2019
ms.locfileid: "68737565"
---
# <a name="configure-self-hosted-ir-as-a-proxy-for-azure-ssis-ir-in-adf"></a>Skonfiguruj własne środowisko IR jako serwer proxy dla środowiska Azure-SSIS IR w podajniku APD
W tym artykule opisano sposób uruchamiania pakietów SQL Server Integration Services (SSIS) na platformie Azure-SSIS Integration Runtime (IR) w Azure Data Factory (ADF) przy użyciu samodzielnego środowiska IR skonfigurowanego jako serwer proxy.  Ta funkcja umożliwia dostęp do danych lokalnych bez dołączania [środowiska Azure-SSIS IR do sieci wirtualnej](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).  Jest to przydatne, gdy sieć firmowa ma nadmiernie złożone zasady konfiguracji/restrykcyjne umożliwiające wprowadzanie w niej środowiska Azure-SSIS IR.

Ta funkcja podzieli pakiet zawierający zadanie przepływu danych z lokalnym źródłem danych na dwa zadania przejściowe: pierwszy uruchomiony na samoobsługowym środowisku IR najpierw przenosi dane z lokalnego źródła danych do obszaru przejściowego na platformie Azure Blob Storage, podczas gdy drugi, uruchomiony w środowisku Azure-SSIS IR, następnie przenosi dane z obszaru przejściowego do planowanego miejsca docelowego danych.

Ta funkcja udostępnia również inne korzyści/możliwości, które umożliwiają udostępnienie własnego środowiska IR w regionach, które nie są jeszcze obsługiwane przez środowisko Azure-SSIS IR, pozwalają na publiczny statyczny adres IP samodzielnego środowiska IR na zaporze źródeł danych itp.

## <a name="prepare-self-hosted-ir"></a>Przygotuj własne środowisko IR
Aby móc korzystać z tej funkcji, najpierw musisz utworzyć ADF i udostępnić w niej środowisko Azure-SSIS IR, jeśli jeszcze tego nie zrobiono, wykonując [instrukcje dotyczące udostępniania artykułu Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure) .

Następnie musisz zainicjować obsługę własnego środowiska IR w tym samym PODAJNIKu, w którym Zainicjowano obsługę środowiska Azure-SSIS IR, postępując zgodnie z [tematem jak utworzyć własny artykuł IR](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime) .

Na koniec należy pobrać i zainstalować najnowszą wersję własnego środowiska IR, a także dodatkowe sterowniki i środowisko uruchomieniowe na maszynie lokalnej/maszynie wirtualnej platformy Azure (VM) w następujący sposób:
- Pobierz i zainstaluj najnowszą wersję własnego środowiska IR z tego [miejsca](https://www.microsoft.com/download/details.aspx?id=39717).
- Jeśli używasz łączników OLEDB w pakietach, Pobierz i zainstaluj odpowiednie sterowniki OLEDB na tym samym komputerze, na którym zainstalowano samoobsługowe środowisko IR, jeśli jeszcze tego nie zrobiono.  Jeśli używasz starszej wersji sterownika OLEDB dla SQL Server (SQLNCLI), możesz pobrać wersję 64-bitową z tego [miejsca](https://www.microsoft.com/download/details.aspx?id=50402).  Jeśli używasz najnowszej wersji sterownika OLEDB dla SQL Server (MSOLEDBSQL), możesz pobrać 64-bitową wersję z tego [miejsca](https://www.microsoft.com/download/details.aspx?id=56730).  Jeśli używasz sterowników OLEDB dla innych systemów baz danych, takich jak PostgreSQL, MySQL, Oracle itp., możesz pobrać 64-bitową wersję z odpowiednich witryn sieci Web.
- Pobierz i zainstaluj środowisko uruchomieniowe programu Visual C++ (VC) na tym samym komputerze, na którym zainstalowano samoobsługowy aparat IR, jeśli jeszcze tego nie zrobiono.  W [tym miejscu](https://www.microsoft.com/download/details.aspx?id=40784)możesz pobrać wersję 64-bitową.

## <a name="prepare-azure-blob-storage-linked-service-for-staging"></a>Przygotowywanie usługi połączonej Blob Storage Azure na potrzeby przemieszczania
Utwórz połączoną usługę Azure Blob Storage w ramach tego samego PODAJNIKa danych, w którym Zainicjowano obsługę środowiska Azure-SSIS IR, jeśli jeszcze tego nie zrobiono, wykonując artykuł [jak utworzyć połączoną usługę ADF](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-linked-service) .  Upewnij się, że:
- Wybrano **BLOB Storage platformy Azure** do **przechowywania danych**
- Wybrano **AutoResolveIntegrationRuntime** do **połączenia za pośrednictwem środowiska Integration Runtime**
- Wybrano jednostkę**usługi** **URI**/sygnatury dostępu współdzielonego **klucza**/konta dla **metody uwierzytelniania**

![Przygotowywanie usługi połączonej Blob Storage Azure na potrzeby przemieszczania](media/self-hosted-integration-runtime-proxy-ssis/shir-azure-blob-storage-linked-service.png)

## <a name="configure-azure-ssis-ir-with-self-hosted-ir-as-a-proxy"></a>Konfigurowanie środowiska Azure-SSIS IR przy użyciu samodzielnego środowiska IR jako serwera proxy
Po przygotowaniu samodzielnego środowiska IR i połączonej usługi Azure Blob Storage na potrzeby przemieszczania możesz teraz skonfigurować nowe/istniejące środowisko Azure-SSIS IR przy użyciu samodzielnego środowiska IR jako serwera proxy w portalu i aplikacji ADF.  Jeśli Twoje istniejące środowisko Azure-SSIS IR jest uruchomione, Zatrzymaj je przed wykonaniem tej czynności, a następnie uruchom je ponownie.

Na stronie **Ustawienia zaawansowane** zaznacz pole wyboru Skonfiguruj samoobsługowe **Integration Runtime jako serwer proxy dla środowiska Azure-SSIS Integration Runtime** , wybierz swoją usługę powiązaną z własnym obsługą środowiska IR i platformy Azure Blob Storage do przemieszczania, a następnie określ obiekt BLOB nazwa kontenera dla **ścieżki tymczasowej** , jeśli chcesz.

![Konfigurowanie środowiska Azure-SSIS IR przy użyciu samodzielnego środowiska IR jako serwera proxy](media/self-hosted-integration-runtime-proxy-ssis/shir-advanced-settings-ssisir.png)

## <a name="enable-ssis-packages-to-connect-by-proxy"></a>Zezwól na łączenie pakietów SSIS z serwerem proxy
Korzystając z najnowszej SSDT z rozszerzeniem projektów SSIS dla programu Visual Studio, które można pobrać z tego [miejsca](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects) lub jako autonomicznego Instalatora, który można pobrać z tego [miejsca](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer), można znaleźć nową właściwość **ConnectByProxy** , która została dodana w OLEDB/ Menedżerów prostego połączenia plików.  

Podczas projektowania nowych pakietów zawierających zadania przepływu danych ze źródłami plików OLEDB/Flat do uzyskiwania dostępu do baz danych i plików lokalnie można włączyć tę właściwość, ustawiając dla niej **wartość true** w panelu Właściwości odpowiednich menedżerów połączeń.

![Włącz Właściwość ConnectByProxy](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-manager-properties.png)

Możesz również włączyć tę właściwość podczas uruchamiania istniejących pakietów bez konieczności ręcznej zmiany ich po jednym.  Dostępne są dwie opcje:
- Otwieranie, ponowna kompilacja i ponowne wdrażanie projektu zawierającego te pakiety przy użyciu najnowszych SSDT do uruchamiania na platformie Azure-SSIS IR: Właściwość można następnie włączyć, ustawiając jej **wartość na true** dla odpowiednich menedżerów połączeń, które są wyświetlane na karcie **Menedżer połączeń** w oknie podręcznym wykonaj pakiet w przypadku uruchamiania pakietów z programu SSMS.

  ![Włącz ConnectByProxy Property2](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssms.png)

  Właściwość można również włączyć, ustawiając jej **wartość na true** dla odpowiednich menedżerów połączeń, które są wyświetlane na karcie **menedżerowie połączeń** w [działaniu pakiet SSIS](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) w przypadku uruchamiania pakietów w potokach ADF.
  
  ![Włącz ConnectByProxy Property3](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssis-activity.png)

- Ponowne wdrażanie projektu zawierającego te pakiety do uruchomienia na urządzeniu SSIS IR: Właściwość można następnie włączyć, podając jej ścieżkę właściwości, `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`i ustawiając ją na **wartość true** jako przesłonięcie właściwości na karcie **Zaawansowane** w oknie podręcznym wykonywania pakietu podczas uruchamiania pakietów z programu SSMS.

  ![Włącz ConnectByProxy Property4](media/self-hosted-integration-runtime-proxy-ssis/shir-advanced-tab-ssms.png)

  Właściwość można również włączyć, podając `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`jej ścieżkę właściwości, i ustawiając ją na **wartość true** w ramach przesłonięcia właściwości na karcie przesłonięcie [działania pakietu SSIS](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) w przypadku uruchamiania pakietów w potokach ADF.
  
  ![Włącz ConnectByProxy Property5](media/self-hosted-integration-runtime-proxy-ssis/shir-property-overrides-tab-ssis-activity.png)

## <a name="debug-the-first-and-second-staging-tasks"></a>Debugowanie pierwszego i drugiego zadania przemieszczania
Na własnym hostowanym środowisku IR można znaleźć dzienniki środowiska uruchomieniowego w `C:\ProgramData\SSISTelemetry` folderze i dzienniki wykonywania pierwszego zadania przemieszczania w `C:\ProgramData\SSISTelemetry\ExecutionLog` folderze.  Dzienniki wykonywania drugiego zadania przemieszczania znajdują się w SSISDB lub określonych ścieżkach rejestrowania, w zależności od tego, czy pakiety są przechowywane odpowiednio w SSISDB, czy w systemie plików/udziałach plików/Azure Files.  Unikatowe identyfikatory pierwszych zadań przemieszczania można także znaleźć w dziennikach wykonywania drugiego zadania przemieszczania, np. 

![Unikatowy identyfikator pierwszego zadania przemieszczania](media/self-hosted-integration-runtime-proxy-ssis/shir-first-staging-task-guid.png)

## <a name="billing-for-the-first-and-second-staging-tasks"></a>Rozliczenia dotyczące pierwszego i drugiego zadania przemieszczania
Pierwsze zadania przemieszczania uruchomione na samoobsługowym środowisku IR będą rozliczane osobno w taki sam sposób, jak wszystkie działania związane z przenoszeniem danych działające w ramach własnego środowiska IR są rozliczane zgodnie z opisem w artykule [Cennik potoku danych ADF](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) .

Pozostałe zadania przemieszczania uruchomione na platformie Azure-SSIS IR nie będą rozliczane oddzielnie, ale w przypadku uruchamiania środowiska Azure-SSIS IR zostanie naliczona wartość określona w artykule dotyczącym [usługi Azure-SSIS IR](https://azure.microsoft.com/pricing/details/data-factory/ssis/) .

## <a name="current-limitations"></a>Bieżące ograniczenia
- Obecnie są obsługiwane tylko menedżerów połączeń plików OLEDB/Flat i źródła plików OLEDB/Flat.
- Obecnie są obsługiwane tylko połączone usługi platformy Azure Blob Storage skonfigurowane z uwierzytelnianiem podstawowym **klucza**/konta**SAS**/**usługi** .
- Obecnie jest obsługiwane tylko własne środowisko IR obsługiwane w ramach tego samego PODAJNIKa danych, w którym zainicjowano środowisko Azure-SSIS IR.

## <a name="next-steps"></a>Następne kroki
Po skonfigurowaniu własnego środowiska IR jako serwera proxy dla środowiska Azure-SSIS IR możesz wdrażać i uruchamiać pakiety, aby uzyskiwać dostęp do danych lokalnych jako działania pakietu SSIS w potokach ADF, zobacz [uruchamianie pakietów SSIS jako działania pakietu SSIS w potokach ADF ](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).