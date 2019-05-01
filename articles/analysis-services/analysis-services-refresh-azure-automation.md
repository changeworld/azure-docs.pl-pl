---
title: Odśwież modeli usług Azure Analysis Services przy użyciu usługi Azure Automation | Dokumentacja firmy Microsoft
description: Dowiedz się, jak napisać odświeżania modelu przy użyciu usługi Azure Automation.
author: chrislound
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: chlound
ms.openlocfilehash: 1897193f0ae781029a7303c42ca8eeaa51389892
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64920580"
---
# <a name="refresh-with-azure-automation"></a>Odświeżanie za pomocą usługi Azure Automation

Za pomocą usługi Azure Automation i elementy Runbook programu PowerShell, można wykonać operacji odświeżania danych automatycznych w modelach tabelarycznych usługi Azure Analysis.  

W przykładzie w tym artykule użyto [modułów programu PowerShell SqlServer](https://docs.microsoft.com/powershell/module/sqlserver/?view=sqlserver-ps).

Przykładowy element Runbook programu PowerShell, który pokazuje, odświeżanie modelu znajduje się w dalszej części tego artykułu.  

## <a name="authentication"></a>Authentication

Wszystkie wywołania musi zostać uwierzytelniony przy użyciu prawidłowego tokenu usługi Azure Active Directory (OAuth 2).  W przykładzie w tym artykule użyje nazwy głównej usługi (SPN) do uwierzytelniania w usługach Azure Analysis Services.

Aby dowiedzieć się więcej na temat tworzenia jednostki usługi, zobacz]

## <a name="prerequisites"></a>Wymagania wstępne

> [!IMPORTANT]
> W poniższym przykładzie założono, że Zapora usług Azure Analysis Services jest wyłączona. Jeśli Zapora jest włączona, następnie publiczny adres IP inicjatora żądania należy do listy dozwolonych w zaporze.

### <a name="install-sqlserver-modules-from-powershell-gallery"></a>Zainstaluj moduły SqlServer z galerii programu PowerShell.

1. Konta usługi Azure Automation kliknij **modułów**, następnie **Przeglądaj galerię**.

2. Na pasku wyszukiwania, wyszukaj **SqlServer**.

    ![Wyszukaj moduły](./media/analysis-services-refresh-azure-automation/1.png)

3. Wybierz SqlServer, a następnie kliknij przycisk **importu**.
 
    ![Importowanie modułu](./media/analysis-services-refresh-azure-automation/2.png)

4. Kliknij przycisk **OK**.
 
### <a name="create-a-service-principal-spn"></a>Tworzenie jednostki usługi (SPN)

Aby dowiedzieć się więcej na temat tworzenia jednostki usługi, zobacz [utworzyć nazwę główną usługi za pomocą witryny Azure portal](../active-directory/develop/howto-create-service-principal-portal.md).

### <a name="configure-permissions-in-azure-analysis-services"></a>Skonfiguruj uprawnienia w usługach Azure Analysis Services
 
Możesz utworzyć jednostkę usługi, musi mieć uprawnienia administratora serwera, na serwerze. Aby dowiedzieć się więcej, zobacz [Dodawanie jednostki usługi do roli administratora serwera](analysis-services-addservprinc-admins.md).

## <a name="design-the-azure-automation-runbook"></a>Projektowanie elementu Runbook usługi Azure Automation

1. Na koncie usługi Automation, tworzenie **poświadczenia** zasobu, który będzie używany do bezpiecznego przechowywania nazwy głównej usługi.

    ![Tworzenie poświadczeń](./media/analysis-services-refresh-azure-automation/6.png)

2. Wprowadź szczegóły dla poświadczenia.  Dla **nazwy użytkownika**, wprowadź **SPN ClientId**, dla **hasło**, wprowadź **klucz tajny SPN**.

    ![Tworzenie poświadczeń](./media/analysis-services-refresh-azure-automation/7.png)

3. Importowanie elementu Runbook usługi Automation

    ![Importowanie elementu Runbook](./media/analysis-services-refresh-azure-automation/8.png)

4. Przeglądaj w poszukiwaniu **Model.ps1 odświeżania** pliku, podaj **nazwa** i **opis**, a następnie kliknij przycisk **Utwórz**.

    ![Importowanie elementu Runbook](./media/analysis-services-refresh-azure-automation/9.png)

5. Po utworzeniu elementu Runbook, zostanie automatycznie przejść do trybu edycji.  Wybierz pozycję **Publikuj**.

    ![Publikowanie elementu Runbook](./media/analysis-services-refresh-azure-automation/10.png)

    > [!NOTE]
    > Zasób poświadczeń, który został utworzony wcześniej jest pobierany przez element runbook za pomocą **Get-AutomationPSCredential** polecenia.  To polecenie jest następnie przekazywany do **Invoke ProcessASADatabase** polecenia programu PowerShell w celu przeprowadzenia uwierzytelniania do usług Azure Analysis Services.

6. Testowanie elementu runbook, klikając **Start**.

    ![Uruchamianie elementu Runbook](./media/analysis-services-refresh-azure-automation/11.png)

7. Wypełnij **DATABASENAME**, **ANALYSISSERVER**, i **REFRESHTYPE** parametrów, a następnie kliknij **OK**. **WEBHOOKDATA** parametr nie jest wymagany, gdy element Runbook jest uruchamiana ręcznie.

    ![Uruchamianie elementu Runbook](./media/analysis-services-refresh-azure-automation/12.png)

Jeśli element Runbook został wykonany pomyślnie, otrzymasz dane wyjściowe podobne do następujących:

![Pomyślny przebieg](./media/analysis-services-refresh-azure-automation/13.png)

## <a name="use-a-self-contained-azure-automation-runbook"></a>Za pomocą niezależna elementu Runbook automatyzacji Azure

Element Runbook można skonfigurować do wyzwalania odświeżania modelu usług Azure Analysis Services, zgodnie z harmonogramem.

Można to skonfigurować w następujący sposób:

1. W elemencie Runbook usługi Automation, kliknij przycisk **harmonogramy**, następnie **Dodaj harmonogram**.
 
    ![Utwórz harmonogram](./media/analysis-services-refresh-azure-automation/14.png)

2. Kliknij przycisk **harmonogram** > **Utwórz nowy harmonogram**, a następnie wypełnij szczegóły.

    ![Konfigurowanie harmonogramu](./media/analysis-services-refresh-azure-automation/15.png)

3. Kliknij pozycję **Utwórz**.

4. Podaj parametry dla harmonogramu. Zostaną one użyte każdorazowo wyzwala element Runbook. **WEBHOOKDATA** parametru powinna pozostać pusta podczas uruchamiania za pomocą harmonogramu.

    ![Konfigurowanie parametrów](./media/analysis-services-refresh-azure-automation/16.png)

5. Kliknij przycisk **OK**.

## <a name="consume-with-data-factory"></a>Używanie z usługą Data Factory

Aby korzystać z elementu runbook za pomocą usługi Azure Data Factory, należy najpierw utworzyć **elementu Webhook** dla elementu runbook. **Elementu Webhook** zapewni, że adres URL, który można wywoływać za pomocą usługi Azure Data Factory działanie sieci web.

> [!IMPORTANT]
> Aby utworzyć **elementu Webhook**, stan elementu Runbook musi być **opublikowano**.

1. W przypadku elementu Runbook usługi Automation kliknij **elementów Webhook**, a następnie kliknij przycisk **Dodaj element Webhook**.

   ![Dodaj element Webhook](./media/analysis-services-refresh-azure-automation/17.png)

2. Element Webhook należy podać nazwę i wygaśnięcia.  Nazwa identyfikuje tylko element Webhook wewnątrz elementu Runbook usługi Automation, go nie stanowią część adresu URL.

   >[!CAUTION]
   >Upewnij się, że skopiuj adres URL przed zamknięciem kreatora, ponieważ nie można odzyskać ją po zamknięciu.
    
   ![Konfigurowanie elementu Webhook](./media/analysis-services-refresh-azure-automation/18.png)

    Parametry elementu webhook może pozostać puste.  Podczas konfigurowania usługi Azure Data Factory działanie internetowe, parametry mogą być przekazywane w treści wywołania sieci web.

3. W usłudze Data Factory, należy skonfigurować **działania w sieci web**

### <a name="example"></a>Przykład

   ![Przykład działania internetowego](./media/analysis-services-refresh-azure-automation/19.png)

**Adresu URL** jest adres URL utworzony na podstawie elementu Webhook.

**Treści** to dokument JSON, który powinien zawierać następujące właściwości:


|Właściwość  |Wartość  |
|---------|---------|
|**AnalysisServicesDatabase**     |Nazwa bazy danych usług Azure Analysis Services <br/> Przykład: AdventureWorksDB         |
|**AnalysisServicesServer**     |Nazwa serwera usług Azure Analysis Services. <br/> Przykład: https://westus.asazure.windows.net/servers/myserver/models/AdventureWorks/         |
|**DatabaseRefreshType**     |Typ odświeżania do wykonania. <br/> Przykład: Pełne         |

Przykład treść kodu JSON:

```json
{
    "AnalysisServicesDatabaseName": "AdventureWorksDB",
    "AnalysisServicesServer": "asazure://westeurope.asazure.windows.net/MyAnalysisServer",
    "DatabaseRefreshType": "Full"
}
```

Te parametry są definiowane w elemencie runbook skrypt programu PowerShell.  Po wykonaniu działania internetowego ładunek JSON przekazywane jest parametr WEBHOOKDATA.

Jest to zdeserializować i przechowywane jako parametry programu PowerShell, które są następnie używane przez polecenie Invoke-ProcesASDatabase programu PowerShell.

![Zdeserializowany element Webhook](./media/analysis-services-refresh-azure-automation/20.png)

## <a name="use-a-hybrid-worker-with-azure-analysis-services"></a>Hybrydowy proces roboczy za pomocą usługi Azure Analysis Services

Maszynę wirtualną platformy Azure przy użyciu statyczny publiczny adres IP może służyć jako hybrydowy proces roboczy usługi Azure Automation.  Następnie można dodać ten publiczny adres IP do zapory usługi Azure Analysis Services.

> [!IMPORTANT]
> Upewnij się, że publiczny adres IP maszyny wirtualnej jest skonfigurowany jako statyczny.
>
>Aby dowiedzieć się więcej na temat konfigurowania usługi Azure Automation hybrydowych procesów roboczych, zobacz [Automatyzuj zasobów w centrum danych lub w chmurze przy użyciu hybrydowego procesu roboczego Runbook](../automation/automation-hybrid-runbook-worker.md#install-a-hybrid-runbook-worker).

Po skonfigurowaniu hybrydowy proces roboczy tworzenia elementu Webhook, zgodnie z opisem w sekcji [wykorzystania z usługą Data Factory](#consume-with-data-factory).  Jedyną różnicą jest wybranie **systemem** > **hybrydowy proces roboczy** opcję podczas konfigurowania elementu Webhook.

Przykład elementu webhook przy użyciu hybrydowego procesu roboczego:

![Przykład hybrydowego procesu roboczego z elementu Webhook](./media/analysis-services-refresh-azure-automation/21.png)

## <a name="sample-powershell-runbook"></a>Przykładowy element Runbook programu PowerShell

Poniższy fragment kodu znajduje się przykład sposobu wykonania odświeżania modelu usług Azure Analysis Services przy użyciu elementu Runbook programu PowerShell.

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


## <a name="next-steps"></a>Kolejne kroki

[Przykłady](analysis-services-samples.md)  
[Interfejs API REST](https://docs.microsoft.com/rest/api/analysisservices/servers)
