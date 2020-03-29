---
title: Włączanie rejestrowania diagnostycznego w usłudze Azure Traffic Manager
description: Dowiedz się, jak włączyć rejestrowanie diagnostyczne profilu usługi Traffic Manager i uzyskać dostęp do plików dziennika, które zostały utworzone w wyniku.
services: traffic-manager
author: rohinkoul
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/25/2019
ms.author: rohink
ms.openlocfilehash: 0ed2ecef86795f62aa3fe5798dcd0d07adbaf9cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938671"
---
# <a name="enable-diagnostic-logging-in-azure-traffic-manager"></a>Włączanie rejestrowania diagnostycznego w usłudze Azure Traffic Manager

W tym artykule opisano sposób włączania rejestrowania diagnostycznego i dostępu do danych dziennika dla profilu usługi Traffic Manager.

Dzienniki diagnostyczne usługi Azure Traffic Manager mogą zapewniać wgląd w zachowanie zasobu profilu usługi Traffic Manager. Na przykład można użyć danych dziennika profilu, aby ustalić, dlaczego poszczególne sondy zostały przesunął limit czasu względem punktu końcowego.

## <a name="enable-diagnostic-logging"></a>Włączanie rejestrowania diagnostycznego

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Można uruchomić polecenia, które należy wykonać w [usłudze Azure Cloud Shell](https://shell.azure.com/powershell)lub uruchamiając program PowerShell z komputera. Usługa Azure Cloud Shell to bezpłatna powłoka interaktywna. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. Jeśli program PowerShell jest uruchamiany z komputera, potrzebny jest moduł programu Azure PowerShell, 1.0.0 lub nowsze. Można uruchomić, `Get-Module -ListAvailable Az` aby znaleźć zainstalowaną wersję. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli korzystasz z programu PowerShell lokalnie, `Login-AzAccount` musisz również uruchomić, aby zalogować się na platformie Azure.

1. **Pobierz profil Usługi Traffic Manager:**

    Aby włączyć rejestrowanie diagnostyczne, potrzebny jest identyfikator profilu usługi Traffic Manager. Pobierz profil usługi Traffic Manager, który ma zostać włączyny w rejestrowaniu diagnostycznym za pomocą [programu Get-AzTrafficManagerProfile](/powershell/module/az.TrafficManager/Get-azTrafficManagerProfile). Dane wyjściowe zawierają informacje o identyfikatorze profilu usługi Traffic Manager.

    ```azurepowershell-interactive
    Get-AzTrafficManagerProfile -Name <TrafficManagerprofilename> -ResourceGroupName <resourcegroupname>
    ```

2. **Włącz rejestrowanie diagnostyczne dla profilu usługi Traffic Manager:**

    Włącz rejestrowanie diagnostyczne profilu usługi Traffic Manager przy użyciu identyfikatora uzyskanego w poprzednim kroku za pomocą [funkcji Set-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting?view=latest). Następujące polecenie przechowuje pełne dzienniki profilu usługi Traffic Manager do określonego konta usługi Azure Storage. 

      ```azurepowershell-interactive
    Set-AzDiagnosticSetting -ResourceId <TrafficManagerprofileResourceId> -StorageAccountId <storageAccountId> -Enabled $true
      ``` 
3. **Sprawdź ustawienia diagnostyczne:**

      Sprawdź ustawienia diagnostyczne profilu Usługi Traffic Manager za pomocą [polecenia Get-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/get-azdiagnosticsetting?view=latest). Następujące polecenie wyświetla kategorie, które są rejestrowane dla zasobu.

     ```azurepowershell-interactive
     Get-AzDiagnosticSetting -ResourceId <TrafficManagerprofileResourceId>
     ```  
      Upewnij się, że wszystkie kategorie dzienników skojarzone z zasobem profilu usługi Traffic Manager są wyświetlane jako włączone. Ponadto sprawdź, czy konto magazynu jest poprawnie ustawione.

## <a name="access-log-files"></a>Dostęp do plików dziennika
1. Zaloguj się do [Portalu Azure](https://portal.azure.com). 
1. Przejdź do konta usługi Azure Storage w portalu.
2. Na stronie **Przegląd** konta magazynu platformy Azure w obszarze **Usługi** wybierz pozycję **Obiekty blob**.
3. W przypadku **kontenerów**wybierz **insights-logs-probehealthstatusevents**i przejdź w dół do pliku PT1H.json i kliknij przycisk **Pobierz,** aby pobrać i zapisać kopię tego pliku dziennika.

    ![Uzyskiwanie dostępu do plików dziennika profilu usługi Traffic Manager z magazynu obiektów blob](./media/traffic-manager-logs/traffic-manager-logs.png)


## <a name="traffic-manager-log-schema"></a>Schemat dziennika usługi Traffic Manager

Wszystkie dzienniki diagnostyczne dostępne za pośrednictwem usługi Azure Monitor współużytkują wspólny schemat najwyższego poziomu, z elastycznością dla każdej usługi do emitowania unikatowych właściwości dla własnych zdarzeń. Aby uzyskać schemat dzienników diagnostycznych najwyższego poziomu, zobacz [Obsługiwane usługi, schematy i kategorie dzienników diagnostycznych platformy Azure.](../azure-monitor/platform/tutorial-dashboards.md)

Poniższa tabela zawiera schemat dzienników specyficzny dla zasobu profilu usługi Azure Traffic Manager.

|||||
|----|----|---|---|
|**Nazwa pola**|**Typ pola**|**Definicja**|**Przykład**|
|Nazwa punktu końcowego|Ciąg|Nazwa punktu końcowego programu Traffic Manager, którego stan kondycji jest rejestrowany.|*myPrimaryEndpoint*|
|Stan|Ciąg|Stan kondycji punktu końcowego programu Traffic Manager, który został sondowany. Stan może być w **górę** lub **w dół**.|**W górę**|
|||||

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [monitorowaniu usługi Traffic Manager](traffic-manager-monitoring.md)

