---
title: Włącz rejestrowanie diagnostyczne w usłudze Azure Traffic Manager
description: Dowiedz się, jak włączyć rejestrowanie diagnostyczne dla swojego profilu usługi Traffic Manager i dostępu do plików dziennika, które są tworzone w wyniku.
services: traffic-manager
author: KumudD
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/25/2019
ms.author: kumud
ms.openlocfilehash: a7d6893c42028790ec565961f2a2cb54035aefa1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60204067"
---
# <a name="enable-diagnostic-logging-in-azure-traffic-manager"></a>Włącz rejestrowanie diagnostyczne w usłudze Azure Traffic Manager

W tym artykule opisano sposób włączania rejestrowania i dostęp do dzienników danych diagnostycznych dla profilu usługi Traffic Manager.

Dzienniki diagnostyczne platformy Azure Traffic Manager zapewniają wgląd w zachowanie zasobów profilu usługi Traffic Manager. Na przykład można użyć danych dziennika profil, który Aby ustalić, dlaczego poszczególnych sondy przekroczyło limit czasu dla punktu końcowego.

## <a name="enable-diagnostic-logging"></a>Włączanie rejestrowania diagnostycznego

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Możesz uruchamiać polecenia, które należy wykonać w [usługi Azure Cloud Shell](https://shell.azure.com/powershell), lub korzystając z polecenia programu PowerShell na komputerze. Azure Cloud Shell to bezpłatna interaktywna powłoka. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. Po uruchomieniu programu PowerShell z komputera, należy moduł Azure PowerShell 1.0.0 lub nowszym. Możesz uruchomić `Get-Module -ListAvailable Az` można odnaleźć zainstalowanej wersji. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, trzeba będzie również uruchomić `Login-AzAccount` zalogować się do platformy Azure.

1. **Pobierz profil usługi Traffic Manager:**

    Aby włączyć rejestrowanie diagnostyczne, potrzebny jest identyfikator profilu usługi Traffic Manager. Pobieranie profilu usługi Traffic Manager, który chcesz włączyć diagnostyki rejestrowanie dla za pomocą [Get AzTrafficManagerProfile](/powershell/module/az.TrafficManager/Get-azTrafficManagerProfile). Dane wyjściowe obejmują informacje o identyfikatorze profilu usługi Traffic Manager.

    ```azurepowershell-interactive
    Get-AzTrafficManagerProfile -Name <TrafficManagerprofilename> -ResourceGroupName <resourcegroupname>
    ```

2. **Włączanie rejestrowania diagnostycznego dla profilu usługi Traffic Manager:**

    Włączanie rejestrowania diagnostycznego dla profilu usługi Traffic Manager przy użyciu Identyfikatora uzyskanego w poprzednim kroku przy użyciu [AzDiagnosticSetting zestaw](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting?view=latest). Poniższe polecenie zapisuje pełne dzienniki profilu usługi Traffic Manager, aby określone konto usługi Azure Storage. 

      ```azurepowershell-interactive
    Set-AzDiagnosticSetting -ResourceId <TrafficManagerprofileResourceId> -StorageAccountId <storageAccountId> -Enabled $true
      ``` 
3. **Sprawdź ustawienia diagnostyczne:**

      Sprawdź ustawienia diagnostyczne dla profilu usługi Traffic Manager za pomocą [Get AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/get-azdiagnosticsetting?view=latest). Następujące polecenie wyświetla kategorie, które są rejestrowane dla zasobu.

     ```azurepowershell-interactive
     Get-AzDiagnosticSetting -ResourceId <TrafficManagerprofileResourceId>
     ```  
      Upewnij się, że wszystkie kategorie skojarzone z wyświetlaniem zasobów profilu usługi Traffic Manager jako włączone dziennika. Sprawdź także, że konto magazynu jest poprawnie ustawiona.

## <a name="access-log-files"></a>Dostęp do plików dziennika
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 
1. Przejdź do konta usługi Azure Storage w portalu.
2. Na **Przegląd** w obszarze strony usługi Azure storage account **usług** wybierz **obiektów blob**.
3. Dla **kontenery**, wybierz opcję **insights — dzienniki probehealthstatusevents**i przejdź do pliku PT1H.json i kliknij przycisk **Pobierz** pobrać i zapisać kopię tego dziennika plik.

    ![Dostęp do plików dziennika profilu usługi Traffic Manager z magazynu obiektów blob](./media/traffic-manager-logs/traffic-manager-logs.png)


## <a name="traffic-manager-log-schema"></a>Schemat dziennika usługi Traffic Manager

Wszystkie dzienniki diagnostyczne, dostępne za pośrednictwem usługi Azure Monitor udostępniać wspólny schemat najwyższego poziomu, elastyczność dla każdej usługi emitować unikatowe właściwości dla ich własnych zdarzeń. Dla schematu najwyższego poziomu dzienników diagnostycznych, zobacz [obsługiwane usługi, schematów i kategorie dla dzienników diagnostycznych usługi Azure](../azure-monitor/platform/tutorial-dashboards.md).

Poniższa tabela zawiera schemat dzienniki specyficzne dla zasobów profilu usługi Azure Traffic Manager.

|||||
|----|----|---|---|
|**Nazwa pola**|**Typ pola**|**Definicja**|**Przykład**|
|EndpointName|String|Nazwa punktu końcowego usługi Traffic Manager, których stan kondycji jest rejestrowany.|*myPrimaryEndpoint*|
|Stan|String|Stan kondycji punktu końcowego usługi Traffic Manager, który został sondowany. Stan może być **się** lub **dół**.|**W górę**|
|||||

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [monitorowania usługi Traffic Manager](traffic-manager-monitoring.md)

