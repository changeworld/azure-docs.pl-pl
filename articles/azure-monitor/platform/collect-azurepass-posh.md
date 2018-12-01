---
title: Zbieranie metryk zasobów PaaS platformy Azure z usługą Log Analytics | Dokumentacja firmy Microsoft
description: Dowiedz się, jak włączyć kolekcję metryk zasobów PaaS platformy Azure przy użyciu programu PowerShell do przechowywania i analizy w usłudze Log Analytics.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: 75ec3cf41a70722001ec51185d83aded0a69efff
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2018
ms.locfileid: "52684586"
---
# <a name="configure-collection-of-azure-paas-resource-metrics-with-log-analytics"></a>Konfiguruj zbieranie metryk zasobów PaaS platformy Azure z usługą Log Analytics

Platforma Azure jako zasobów usługi (PaaS), takich jak Azure SQL i witryn sieci Web (aplikacje sieci Web), może emitować danych metryk wydajności natywnie w usłudze Log Analytics. Ten skrypt umożliwia użytkownikom włączyć rejestrowanie dla zasobów PaaS wdrożone w określonej grupie zasobów lub dla całej subskrypcji. 

Obecnie nie ma możliwości, aby włączyć rejestrowanie dla usług PaaS zasobów za pomocą witryny Azure portal. W związku z tym musisz użyć skryptu programu PowerShell. Ta funkcja rejestrowania natywnych metryk wraz z monitorowania usługi Log Analytics, umożliwiają monitorowanie zasobów platformy Azure na dużą skalę. 

## <a name="prerequisites"></a>Wymagania wstępne
Sprawdź, czy masz następujące moduły usługi Azure Resource Manager zainstalowana na komputerze przed kontynuowaniem:

- AzureRM.Insights
- AzureRM.OperationalInsights
- AzureRM.Resources
- AzureRM.profile

>[!NOTE]
>Zaleca się, że wszystkie moduły usługi Azure Resource Manager mają taką samą wersję, aby zapewnić zgodność, po uruchomieniu polecenia usługi Azure Resource Manager za pomocą programu PowerShell.
>
Aby zainstalować najnowszą wersję moduły usługi Azure Resource Manager na komputerze, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.4.1#update-azps).  

## <a name="enable-azure-diagnostics"></a>Włącz diagnostykę platformy Azure  
Konfigurowanie diagnostyki Azure do zasobów PaaS odbywa się przez wykonywanie skryptu, **AzureRMDiagnostics.ps1 Włącz**, który jest dostępny z [galerii programu PowerShell](https://www.powershellgallery.com/packages/Enable-AzureRMDiagnostics/2.52).  Skrypt obsługuje następujące scenariusze:
  
* Określenie zasobu związane z co najmniej jedną grupę zasobów w ramach subskrypcji  
* Określenie zasobu związane z danej grupy zasobów w ramach subskrypcji  
* Ponownie skonfiguruj zasób, aby przekazywać je do innego obszaru roboczego

Obsługiwane są tylko zasoby, które obsługują zbieranie metryk z diagnostyki platformy Azure i wysyłać bezpośrednio do usługi Log Analytics.  Aby uzyskać szczegółową listę, przejrzyj [zbieranie dzienników platformy Azure usługi i metryk do użycia w usłudze Log Analytics](collect-azure-metrics-logs.md) 

Wykonaj poniższe kroki, aby pobrać i uruchomić skrypt.

1.  Na ekranie startowym Windows wpisz **PowerShell** i kliknij prawym przyciskiem myszy program PowerShell z poziomu wyników wyszukiwania.  Wybierz z menu **Uruchom jako Administrator**.   
2. Zapisz **AzureRMDiagnostics.ps1 Włącz** pliku skryptu lokalnie, uruchamiając następujące polecenie i podając ścieżkę do przechowywania skryptu.    

    ```
    PS C:\> save-script -Name Enable-AzureRMDiagnostics -Path "C:\users\<username>\desktop\temp"
    ```

3. Uruchom polecenie `Connect-AzureRmAccount`, aby utworzyć połączenia z platformą Azure.   
4. Uruchom poniższy skrypt `.\Enable-AzureRmDiagnostics.ps1` bez żadnych parametrów, aby włączyć zbieranie danych z określonego zasobu, w ramach subskrypcji lub za pomocą parametru `-ResourceGroup <myResourceGroup>` określenie zasobu w określonej grupie zasobów.   
5. Wybierz odpowiednią subskrypcję z listy, jeśli masz więcej niż jeden, wprowadzając prawidłową wartość.<br><br> ![Wybierz subskrypcję zwróconych przez skrypt](./media/collect-azurepass-posh/script-select-subscription.png)<br> W przeciwnym razie powoduje automatyczne wybranie jednego dostępnej subskrypcji.
6. Następnie skrypt zwraca listę obszarów roboczych usługi Log Analytics, zarejestrowany w ramach subskrypcji.  Wybierz jeden z nich z listy.<br><br> ![Wybierz obszar roboczy zwróconych przez skrypt](./media/collect-azurepass-posh/script-select-workspace.png)<br> 
7. Wybierz zasób platformy Azure, który chcesz włączyć zbieranie z. Na przykład jeśli wpiszesz 5, Włącz zbieranie danych dla baz danych Azure SQL Database.<br><br> ![Wybierz zasób typu zwróconych przez skrypt](./media/collect-azurepass-posh/script-select-resource.png)<br>
   Można wybrać tylko te zasoby, które obsługują zbieranie metryk za pomocą diagnostyki Azure i wysyłanie bezpośrednio do usługi Log Analytics.  Skrypt wyświetli wartość **True** w obszarze **metryki** kolumny na liście zasobów odnajduje w określonej grupie zasobów lub subskrypcji.    
8. Monit o potwierdzenie wyboru.  Wprowadź **Y** Aby włączyć rejestrowanie metryki dla wszystkich wybrane zasoby dla zakresu zdefiniowane, będące w naszym przykładzie wszystkich baz danych SQL w subskrypcji.  

Skrypt uruchamiać każdy zasób spełniających wybrane kryteria, a następnie włączyć zbieranie metryk dla nich. Po zakończeniu zostanie wyświetlony komunikat informujący, że konfiguracja została ukończona.  

Zaraz po zakończeniu będzie wyświetlać dane z zasobów PaaS platformy Azure w swoim repozytorium usługi Log Analytics.  Rekord o typie `AzureMetrics` jest tworzony i analizując te rekordy są obsługiwane przez [usługi Azure SQL Analytics](../../log-analytics/log-analytics-azure-sql.md) i [Azure Web Apps Analytics](../../log-analytics/log-analytics-azure-web-apps-analytics.md) rozwiązań do zarządzania.   

## <a name="update-a-resource-to-send-data-to-another-workspace"></a>Aktualizowanie zasobu do wysyłania danych do innego obszaru roboczego
Jeśli masz zasób, który już jest wysyłanie danych do obszaru roboczego usługi Log Analytics, a później zdecydujesz się ponownie go skonfigurować do odwoływać się do innego obszaru roboczego, możesz uruchomić skrypt o `-Update` parametru.  

**Przykład:** 
`PS C:\users\<username>\Desktop\temp> .\Enable-AzureRMDiagnostics.ps1 -Update`

Zostanie wyświetlony monit odpowiedzieć na te same informacje co uruchomiono skrypt umożliwiający przeprowadzenie konfiguracji początkowej.  

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [dziennikach](../../log-analytics/log-analytics-queries.md) analizować dane zbierane z innych źródeł danych i rozwiązań. 

* Użyj [pól niestandardowych](../../log-analytics/log-analytics-custom-fields.md)(w celu analizowania rekordów zdarzeń do poszczególnych pól.

* Przegląd [tworzenia niestandardowego pulpitu nawigacyjnego do użycia w usłudze Log Analytics](../../azure-monitor/platform/dashboards.md) zrozumienie, jak wizualizować dziennik wyszukiwania w przejrzysty sposób dla organizacji.
