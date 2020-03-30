---
title: Odświeżanie modeli usług Azure Analysis Services za pomocą usługi Azure Automation | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób kodowania odświeżania modelu dla usług Azure Analysis Services przy użyciu usługi Azure Automation.
author: chrislound
ms.service: analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: chlound
ms.openlocfilehash: a79123d57f80474e1871ef68f9a92ea9417089ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73572352"
---
# <a name="refresh-with-azure-automation"></a>Odświeżanie za pomocą usługi Azure Automation

Za pomocą usługi Azure Automation i PowerShell Runbooks, można wykonać automatyczne operacje odświeżania danych na modelach tabelaryczne usługi Azure Analysis.  

W tym artykule użyto [modułów programu SqlServer programu PowerShell](https://docs.microsoft.com/powershell/module/sqlserver/?view=sqlserver-ps).

Przykładowy podręcznik runbook programu PowerShell, który demonstruje odświeżanie modelu znajduje się w dalszej części tego artykułu.  

## <a name="authentication"></a>Uwierzytelnianie

Wszystkie wywołania muszą być uwierzytelnione przy użyciu prawidłowego tokenu usługi Azure Active Directory (OAuth 2).  W tym artykule użyje jednostki usługi (SPN) do uwierzytelniania w usługach Analizy platformy Azure.

Aby dowiedzieć się więcej na temat tworzenia jednostki usługi, zobacz [Tworzenie jednostki usługi przy użyciu witryny Azure portal.](../active-directory/develop/howto-create-service-principal-portal.md)

## <a name="prerequisites"></a>Wymagania wstępne

> [!IMPORTANT]
> W poniższym przykładzie przyjęto założenie, że zapora usług Azure Analysis Services jest wyłączona. Jeśli zapora jest włączona, publiczny adres IP inicjatora żądania musi znajdować się na białej liście w zaporze.

### <a name="install-sqlserver-modules-from-powershell-gallery"></a>Zainstaluj moduły programu SqlServer z galerii programu PowerShell.

1. Na koncie usługi Azure Automation kliknij pozycję **Moduły,** a następnie **przejrzyj galerię**.

2. Na pasku wyszukiwania wyszukaj pozycję **SqlServer**.

    ![Moduły wyszukiwania](./media/analysis-services-refresh-azure-automation/1.png)

3. Wybierz pozycję SqlServer, a następnie kliknij przycisk **Importuj**.
 
    ![Moduł importu](./media/analysis-services-refresh-azure-automation/2.png)

4. Kliknij przycisk **OK**.
 
### <a name="create-a-service-principal-spn"></a>Tworzenie jednostki usługi (SPN)

Aby dowiedzieć się więcej o tworzeniu jednostki usługi, zobacz [Tworzenie jednostki usługi przy użyciu witryny Azure portal.](../active-directory/develop/howto-create-service-principal-portal.md)

### <a name="configure-permissions-in-azure-analysis-services"></a>Konfigurowanie uprawnień w usługach Azure Analysis Services
 
Utworzona jednostka usługi musi mieć uprawnienia administratora serwera na serwerze. Aby dowiedzieć się więcej, zobacz [Dodawanie jednostki usługi do roli administratora serwera](analysis-services-addservprinc-admins.md).

## <a name="design-the-azure-automation-runbook"></a>Projektowanie systemu stroju automatyzacji platformy Azure

1. Na koncie automatyzacji utwórz **zasób poświadczeń,** który będzie używany do bezpiecznego przechowywania jednostki usługi.

    ![Tworzenie poświadczeń](./media/analysis-services-refresh-azure-automation/6.png)

2. Wprowadź szczegóły poświadczenia.  W przypadku **nazwy użytkownika**wprowadź identyfikator **klienta SPN**, hasło **,** wprowadź klucz **tajny SPN**.

    ![Tworzenie poświadczeń](./media/analysis-services-refresh-azure-automation/7.png)

3. Importowanie uruchomieniu systemu automatyzacji

    ![Importowanie chyłkania](./media/analysis-services-refresh-azure-automation/8.png)

4. Wyszukaj plik **Refresh-Model.ps1,** podaj **nazwę** i **opis**, a następnie kliknij przycisk **Utwórz**.

    ![Importowanie chyłkania](./media/analysis-services-refresh-azure-automation/9.png)

5. Po utworzeniu eksmisji automatycznie przejdzie on w tryb edycji.  Wybierz pozycję **Publikuj**.

    ![Publikowanie eks-u](./media/analysis-services-refresh-azure-automation/10.png)

    > [!NOTE]
    > Zasób poświadczeń, który został utworzony wcześniej, jest pobierany przez grupę runbook za pomocą polecenia **Get-AutomationPSCredential.**  To polecenie jest następnie przekazywane do polecenia **Invoke-ProcessASADatabase** PowerShell w celu wykonania uwierzytelniania w usługach Azure Analysis Services.

6. Przetestuj projekt runbooka, klikając przycisk **Start**.

    ![Uruchamianie eksmisji](./media/analysis-services-refresh-azure-automation/11.png)

7. Wypełnij parametry **DATABASENAME**, **ANALYSISSERVER**i **REFRESHTYPE,** a następnie kliknij przycisk **OK**. Parametr **WEBHOOKDATA** nie jest wymagany, gdy element runbook jest uruchamiany ręcznie.

    ![Uruchamianie eksmisji](./media/analysis-services-refresh-azure-automation/12.png)

Jeśli element runbook został pomyślnie wykonany, otrzymasz dane wyjściowe następujące:

![Udany bieg](./media/analysis-services-refresh-azure-automation/13.png)

## <a name="use-a-self-contained-azure-automation-runbook"></a>Korzystanie z niezależnego systemu uruchamiania automatyzacji usługi Azure

Runbook można skonfigurować do wyzwalania odświeżania modelu usług Azure Analysis Services zgodnie z harmonogramem.

Można to skonfigurować w następujący sposób:

1. W bielicie uruchomieniu automatyzacji kliknij pozycję **Harmonogramy**, a następnie **dodaj harmonogram**.
 
    ![Tworzenie harmonogramu](./media/analysis-services-refresh-azure-automation/14.png)

2. Kliknij **pozycję Zaplanuj** > **utwórz nowy harmonogram,** a następnie wprowadź szczegóły.

    ![Konfigurowanie harmonogramu](./media/analysis-services-refresh-azure-automation/15.png)

3. Kliknij przycisk **Utwórz**.

4. Wypełnij parametry harmonogramu. Będą one używane za każdym razem, gdy uruchamia się runbook. **Parametr WEBHOOKDATA** powinien pozostać pusty podczas uruchamiania za pomocą harmonogramu.

    ![Konfigurowanie parametrów](./media/analysis-services-refresh-azure-automation/16.png)

5. Kliknij przycisk **OK**.

## <a name="consume-with-data-factory"></a>Korzystaj z fabryki danych

Aby korzystać z elementu runbook przy użyciu usługi Azure Data Factory, należy najpierw utworzyć **element webhook** dla elementu runbook. **Element Webhook** zapewni adres URL, który można wywołać za pośrednictwem działania sieci Web usługi Azure Data Factory.

> [!IMPORTANT]
> Aby utworzyć element **Webhook,** stan elementu runbook musi być **opublikowany**.

1. W yklętym elementów systemu automatyzacji kliknij pozycję **Elementy webhook**, a następnie kliknij pozycję **Dodaj element Webhook**.

   ![Dodawanie elementu Webhook](./media/analysis-services-refresh-azure-automation/17.png)

2. Nadaj elementowi Webhook nazwę i wygaśnięcie.  Nazwa identyfikuje tylko element Webhook wewnątrz elementu runbook automatyzacji, nie stanowi część adresu URL.

   >[!CAUTION]
   >Upewnij się, że kopiujesz adres URL przed zamknięciem kreatora, ponieważ nie można go odzyskać po zamknięciu.
    
   ![Konfigurowanie elementu Webhook](./media/analysis-services-refresh-azure-automation/18.png)

    Parametry elementu webhook mogą pozostać puste.  Podczas konfigurowania działania sieci Web usługi Azure Data Factory parametry mogą być przekazywane do treści wywołania sieci web.

3. W obszarze Fabryka danych skonfiguruj **aktywność w sieci Web**

### <a name="example"></a>Przykład

   ![Przykładowa aktywność w sieci Web](./media/analysis-services-refresh-azure-automation/19.png)

**Adres URL** to adres URL utworzony na podstawie elementu Webhook.

**Treść** jest dokumentem JSON, który powinien zawierać następujące właściwości:


|Właściwość  |Wartość  |
|---------|---------|
|**Baza danych AnalysisServicesDatabase**     |Nazwa bazy danych usług Azure Analysis Services <br/> Przykład: AdventureWorksDB         |
|**Serwer AnalysisServicesServer**     |Nazwa serwera usług Azure Analysis Services. <br/> Przykład: https:\//westus.asazure.windows.net/servers/myserver/models/AdventureWorks/         |
|**Typ danych**     |Typ odświeżania do wykonania. <br/> Przykład: Pełna         |

Przykładowa treść JSON:

```json
{
    "AnalysisServicesDatabaseName": "AdventureWorksDB",
    "AnalysisServicesServer": "asazure://westeurope.asazure.windows.net/MyAnalysisServer",
    "DatabaseRefreshType": "Full"
}
```

Parametry te są zdefiniowane w skrypcie programu PowerShell programu Runbook.  Po wykonaniu działania sieci Web, ładunek JSON przekazywane jest WEBHOOKDATA.

Jest to deserialized i przechowywane jako parametry programu PowerShell, które są następnie używane przez invoke-ProcesASDatabase PowerShell polecenia.

![Deserializowany element webhook](./media/analysis-services-refresh-azure-automation/20.png)

## <a name="use-a-hybrid-worker-with-azure-analysis-services"></a>Używanie procesu roboczego hybrydowego z usługami Azure Analysis Services

Maszyna wirtualna platformy Azure ze statycznym publicznym adresem IP może służyć jako proces roboczy hybrydowy usługi Azure Automation.  Ten publiczny adres IP można następnie dodać do zapory usług Azure Analysis Services.

> [!IMPORTANT]
> Upewnij się, że publiczny adres IP maszyny wirtualnej jest skonfigurowany jako statyczny.
>
>Aby dowiedzieć się więcej na temat konfigurowania hybrydowych procesów pracy usługi Azure Automation, zobacz [Automatyzowanie zasobów w centrum danych lub w chmurze przy użyciu hybrydowego procesu roboczego żyjącego.](../automation/automation-hybrid-runbook-worker.md#install-a-hybrid-runbook-worker)

Po skonfigurowaniu procesu roboczego hybrydowego utwórz element Webhook zgodnie z opisem w sekcji [Zużywają z fabryką danych](#consume-with-data-factory).  Jedyną różnicą w tym miejscu jest wybranie opcji **Uruchom na** > **hybrydowym udaniem procesu roboczego** podczas konfigurowania elementu Webhook.

Przykład elementu webhook przy użyciu hybrydowego procesu roboczego:

![Przykładowy element Webhook procesu roboczego hybrydowego](./media/analysis-services-refresh-azure-automation/21.png)

## <a name="sample-powershell-runbook"></a>Przykładowy program PowerShell Runbook

Poniższy fragment kodu jest przykładem sposobu wykonywania odświeżania modelu usługi Azure Analysis Services przy użyciu podręcznika runbook programu PowerShell.

```powershell
param
(
    [Parameter (Mandatory = $false)]
    [object] $WebhookData,

    [Parameter (Mandatory = $false)]
    [String] $DatabaseName,
    [Parameter (Mandatory = $false)]
    [String] $AnalysisServer,
    [Parameter (Mandatory = $false)]
    [String] $RefreshType
)

$_Credential = Get-AutomationPSCredential -Name "ServicePrincipal"

# If runbook was called from Webhook, WebhookData will not be null.
if ($WebhookData)
{ 
    # Retrieve AAS details from Webhook request body
    $atmParameters = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)
    Write-Output "CredentialName: $($atmParameters.CredentialName)"
    Write-Output "AnalysisServicesDatabaseName: $($atmParameters.AnalysisServicesDatabaseName)"
    Write-Output "AnalysisServicesServer: $($atmParameters.AnalysisServicesServer)"
    Write-Output "DatabaseRefreshType: $($atmParameters.DatabaseRefreshType)"
    
    $_databaseName = $atmParameters.AnalysisServicesDatabaseName
    $_analysisServer = $atmParameters.AnalysisServicesServer
    $_refreshType = $atmParameters.DatabaseRefreshType
 
    Invoke-ProcessASDatabase -DatabaseName $_databaseName -RefreshType $_refreshType -Server $_analysisServer -ServicePrincipal -Credential $_credential
}
else 
{
    Invoke-ProcessASDatabase -DatabaseName $DatabaseName -RefreshType $RefreshType -Server $AnalysisServer -ServicePrincipal -Credential $_Credential
}
```


## <a name="next-steps"></a>Następne kroki

[Próbki](analysis-services-samples.md)  
[INTERFEJS API ODPOCZYNKU](https://docs.microsoft.com/rest/api/analysisservices/servers)
