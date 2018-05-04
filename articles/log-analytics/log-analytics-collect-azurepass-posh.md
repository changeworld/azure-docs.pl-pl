---
title: Zbieranie metryk zasobów Azure PaaS z Log Analytics | Dokumentacja firmy Microsoft
description: Dowiedz się, jak włączyć kolekcję metryki zasobów Azure PaaS do przechowywania i analiz w analizy dzienników przy użyciu programu PowerShell.
services: log-analytics
documentationcenter: log-analytics
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: magoedte
ms.openlocfilehash: 8a2c04c2f79f310b7e70e7add7a8d5f318f056d2
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="configure-collection-of-azure-paas-resource-metrics-with-log-analytics"></a>Konfigurowanie zbierania metryk zasobów Azure PaaS z analizy dzienników

Platformy Azure jako zasobów usługa (PaaS), na przykład Azure SQL i witryn sieci Web (aplikacje sieci Web), można wysyłać dane metryk wydajności natywnie do analizy dzienników. Ten skrypt umożliwia użytkownikom włączyć metryki rejestrowania dla zasobów PaaS już wdrożona w danej grupy zasobów lub całej subskrypcji. 

Jest obecnie w żaden sposób, aby włączyć rejestrowanie dla PaaS zasobów za pośrednictwem portalu Azure metryki. W związku z tym należy użyć skryptu programu PowerShell. Ta funkcja rejestrowania natywnego metryki wraz z analizy dzienników monitorowania, umożliwiają monitorowanie zasobów platformy Azure na dużą skalę. 

## <a name="prerequisites"></a>Wymagania wstępne
Sprawdź, czy masz następujących modułów usługi Azure Resource Manager zainstalowana na komputerze przed kontynuowaniem:

- AzureRM.Insights
- AzureRM.OperationalInsights
- AzureRM.Resources
- AzureRM.profile

>[!NOTE]
>Zaleca się, że wszystkich modułów usługi Azure Resource Manager mają taką samą wersję, aby zapewnić zgodność, po uruchomieniu polecenia usługi Azure Resource Manager z programu PowerShell.
>
Aby zainstalować najnowszą wersję modułów usługi Azure Resource Manager na komputerze, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.4.1#update-azps).  

## <a name="enable-azure-diagnostics"></a>Włącz diagnostykę Azure  
Konfigurowanie diagnostyki Azure dla zasobów PaaS odbywa się przy wykonywania skryptu, **AzureRMDiagnostics.ps1 Włącz**, który jest dostępny z [galerii programu PowerShell](https://www.powershellgallery.com/packages/Enable-AzureRMDiagnostics/2.52/DisplayScript).  Skrypt obsługuje następujące scenariusze:
  
* Określenie zasobu powiązane z jedną lub więcej grup zasobów w subskrypcji  
* Określenie zasobu związane z danej grupy zasobów w subskrypcji  
* Skonfiguruj ponownie zasobów do przekazywania do innego obszaru roboczego

Obsługiwane są tylko zasoby, które obsługują zbieranie metryk ze diagnostyki Azure i wysyłać bezpośrednio do analizy dzienników.  Aby uzyskać szczegółową listę, przejrzyj [dzienniki usługi Azure zbieranie i metryk do użycia w analizy dzienników](log-analytics-azure-storage.md) 

Wykonaj poniższe kroki, aby pobrać i uruchom skrypt.

1.  Na ekranie startowym systemu Windows wpisz **PowerShell** i kliknij prawym przyciskiem myszy programu PowerShell w wynikach wyszukiwania.  Wybierz z menu **Uruchom jako Administrator**.   
2. Zapisz **AzureRMDiagnostics.ps1 Włącz** pliku skryptu lokalnie, uruchamiając następujące polecenie i podając ścieżkę do przechowywania skryptu.    

    ```
    PS C:\> save-script -Name Enable-AzureRMDiagnostics -Path "C:\users\<username>\desktop\temp"
    ```

3. Uruchom polecenie `Connect-AzureRmAccount`, aby utworzyć połączenia z platformą Azure.   
4. Uruchom poniższy skrypt `.\Enable-AzureRmDiagnostics.ps1` bez żadnych parametrów, aby włączyć zbieranie danych z określonego zasobu w ramach subskrypcji lub z parametrem `-ResourceGroup <myResourceGroup>` określenie zasobu w określonej grupy zasobów.   
5. Wybierz odpowiednią subskrypcję z listy, jeśli masz więcej niż jeden, wprowadzając prawidłową wartość.<br><br> ![Wybierz subskrypcję zwróconych przez skrypt](./media/log-analytics-collect-azurepass-posh/script-select-subscription.png)<br> W przeciwnym razie powoduje automatyczne wybranie jednego dostępnych subskrypcji.
6. Następnie skrypt zwraca listę obszarów roboczych usługi Analiza dzienników zarejestrowany w ramach subskrypcji.  Wybierz odpowiednie jedną, z listy.<br><br> ![Wybierz obszar roboczy zwróconych przez skrypt](./media/log-analytics-collect-azurepass-posh/script-select-workspace.png)<br> 
7. Wybierz zasobów platformy Azure, który chcesz włączyć zbieranie informacji z. Na przykład jeśli wpiszesz 5, włączyć funkcji zbierania danych dla bazy danych SQL Azure.<br><br> ![Wybierz zasób zwrócone przez skrypt](./media/log-analytics-collect-azurepass-posh/script-select-resource.png)<br>
   Można wybrać tylko zasobów, które obsługują zbieranie metryk ze diagnostyki Azure i wysyłanie bezpośrednio do analizy dzienników.  Skrypt wyświetli wartość **True** w obszarze **metryki** kolumnę do listy zasobów wykrytego w określonej grupy zasobów lub subskrypcji.    
8. Monit o potwierdzenie wyboru.  Wprowadź **Y** Aby włączyć rejestrowanie metryki dla wszystkich wybranych zasobów dla zakresu zdefiniowane, będące w naszym przykładzie wszystkich baz danych w ramach subskrypcji.  

Skrypt Uruchom względem poszczególnych zasobów spełniających wybrane kryteria, a następnie włączyć zbieranie metryk dla nich. Po zakończeniu, pojawi się komunikat wskazujący, że konfiguracja została ukończona.  

Wkrótce po zakończeniu rozpocznie wyświetlić dane z zasobów Azure PaaS w repozytorium analizy dzienników.  Rekord typu `AzureMetrics` jest tworzony i analizowanie te rekordy są obsługiwane przez [analiza SQL Azure](log-analytics-azure-sql.md) i [Analytics aplikacji sieci Web Azure](log-analytics-azure-web-apps-analytics.md) rozwiązania do zarządzania.   

## <a name="update-a-resource-to-send-data-to-another-workspace"></a>Zaktualizuj zasób do wysyłania danych do innego obszaru roboczego
Jeśli później zdecydujesz się ponownie go skonfigurować do odwołania obszaru roboczego innego zasobu, który jest już wysyłanie danych do obszaru roboczego analizy dzienników, można uruchomić skryptu `-Update` parametru.  

**Przykład:** 
`PS C:\users\<username>\Desktop\temp> .\Enable-AzureRMDiagnostics.ps1 -Update`

Pojawi się monit odpowiedzi na te same informacje co po uruchomieniu skryptu w celu przeprowadzenia konfiguracji początkowej.  

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [dziennika wyszukiwania](log-analytics-log-searches.md) analizować dane zebrane ze źródeł danych i rozwiązania. 

* Użyj [pola niestandardowe](log-analytics-custom-fields.md)(można przeanalizować rekordów zdarzeń do poszczególnych pól.

* Przegląd [utworzyć niestandardowy pulpit nawigacyjny do użycia w analizy dzienników](log-analytics-dashboards.md) zrozumienie, jak do wizualizacji dziennik wyszukiwania w sposób zrozumiały dla organizacji.
