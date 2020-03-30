---
title: Automatyzacja zadań usługi Azure Analysis Services z podmiotami świadczącymi usługi | Dokumenty firmy Microsoft
description: Dowiedz się, jak utworzyć jednostkę usługi do automatyzacji zadań administracyjnych usługi Azure Analysis Services.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: dc163de9a7fb46d62f4bc2983e040e68bbf9231c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266145"
---
# <a name="automation-with-service-principals"></a>Automatyzacja przy użyciu jednostek usługi

Jednostki usług to zasoby aplikacji usługi Azure Active Directory tworzone w ramach dzierżawy w celu przeprowadzania nienadzorowanych operacji na poziomie zasobu lub usługi. Są one unikatowym typem *tożsamości użytkownika* z identyfikatorem aplikacji i hasłem lub certyfikatem. Podmiot usługi ma tylko te uprawnienia niezbędne do wykonywania zadań zdefiniowanych przez role i uprawnienia, do których jest przypisany. 

W analysis services jednostki usługi są używane z usługą Azure Automation, trybem nienadzorowanym programu PowerShell, niestandardowymi aplikacjami klienckimi i aplikacjami sieci Web w celu automatyzacji typowych zadań. Na przykład inicjowania obsługi administracyjnej serwerów, wdrażania modeli, odświeżania danych, skalowania w górę/w dół i pause/resume można zautomatyzować przy użyciu podmiotów usługi. Uprawnienia są przypisywane do podmiotów nych usług za pośrednictwem członkostwa roli, podobnie jak zwykłe konta usługi AZURE AD UPN.

Analysis Services obsługuje również operacje wykonywane przez tożsamości zarządzane przy użyciu podmiotów zabezpieczeń usługi. Aby dowiedzieć się więcej, zobacz [Tożsamości zarządzane dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md) i usługi platformy Azure [obsługujące uwierzytelnianie usługi Azure AD.](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-analysis-services)  

## <a name="create-service-principals"></a>Tworzenie jednostek usługi
 
Jednostki usługi można utworzyć w witrynie Azure portal lub przy użyciu programu PowerShell. Aby dowiedzieć się więcej, zobacz:

[Tworzenie jednostki usługi — witryna Azure portal](../active-directory/develop/howto-create-service-principal-portal.md)   
[Tworzenie jednostki usługi — PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="store-credential-and-certificate-assets-in-azure-automation"></a>Przechowywanie poświadczeń i zasobów certyfikatów w usłudze Azure Automation

Poświadczenia jednostki usługi i certyfikaty mogą być bezpiecznie przechowywane w usłudze Azure Automation dla operacji uruchomieniu. Aby dowiedzieć się więcej, zobacz:

[Zasoby poświadczeń w usłudze Azure Automation](../automation/automation-credentials.md)   
[Zasoby certyfikatów w usłudze Azure Automation](../automation/automation-certificates.md)

## <a name="add-service-principals-to-server-admin-role"></a>Dodawanie podmiotów zabezpieczeń usługi do roli administratora serwera

Przed użyciem jednostki usługi do operacji zarządzania serwerem usług Analysis Services należy dodać go do roli administratorów serwera. Aby dowiedzieć się więcej, zobacz [Dodawanie jednostki usługi do roli administratora serwera](analysis-services-addservprinc-admins.md).

## <a name="service-principals-in-connection-strings"></a>Jednostki usługi w połączeniach

Jednostki usługi appID i hasło lub certyfikat może być używany w ciągu połączeń podobnie jak upn.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

#### <a name="using-azanalysisservices-module"></a><a name="azmodule" />Korzystanie z modułu Az.AnalysisServices

W przypadku korzystania z jednostki usługi dla operacji zarządzania `Connect-AzAccount` zasobami z modułem [Az.AnalysisServices](/powershell/module/az.analysisservices) należy użyć polecenia cmdlet. 

W poniższym przykładzie appID i hasło są używane do wykonywania operacji płaszczyzny sterowania do synchronizacji do replik tylko do odczytu i skalowania w górę/w górę i w poziomie:

```powershell
Param (
        [Parameter(Mandatory=$true)] [String] $AppId,
        [Parameter(Mandatory=$true)] [String] $PlainPWord,
        [Parameter(Mandatory=$true)] [String] $TenantId
       )
$PWord = ConvertTo-SecureString -String $PlainPWord -AsPlainText -Force
$Credential = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList $AppId, $PWord

# Connect using Az module
Connect-AzAccount -Credential $Credential -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx"

# Syncronize a database for query scale out
Sync-AzAnalysisServicesInstance -Instance "asazure://westus.asazure.windows.net/testsvr" -Database "testdb"

# Scale up the server to an S1, set 2 read-only replicas, and remove the primary from the query pool. The new replicas will hydrate from the synchronized data.
Set-AzAnalysisServicesServer -Name "testsvr" -ResourceGroupName "testRG" -Sku "S1" -ReadonlyReplicaCount 2 -DefaultConnectionMode Readonly
```

#### <a name="using-sqlserver-module"></a>Korzystanie z modułu SQLServer

W poniższym przykładzie appID i hasło są używane do wykonywania operacji odświeżania bazy danych modelu:

```powershell
Param (
        [Parameter(Mandatory=$true)] [String] $AppId,
        [Parameter(Mandatory=$true)] [String] $PlainPWord,
        [Parameter(Mandatory=$true)] [String] $TenantId
       )
$PWord = ConvertTo-SecureString -String $PlainPWord -AsPlainText -Force

$Credential = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList $AppId, $PWord

Invoke-ProcessTable -Server "asazure://westcentralus.asazure.windows.net/myserver" -TableName "MyTable" -Database "MyDb" -RefreshType "Full" -ServicePrincipal -ApplicationId $AppId -TenantId $TenantId -Credential $Credential
```

### <a name="amo-and-adomd"></a>AMO i ADOMD 

Podczas łączenia się z aplikacjami klienckimi i aplikacjami sieci Web [biblioteki klienckie AMO i ADOMD](analysis-services-data-providers.md) w wersji 15.0.2 i wyższej instalowalnej z podmiotów usługi pomocy technicznej NuGet w ciągach połączeń przy użyciu następującej składni: `app:AppID` i hasła lub `cert:thumbprint`. 

W poniższym `appID` przykładzie `password` i są używane do wykonywania operacji odświeżania bazy danych modelu:

```csharp
string appId = "xxx";
string authKey = "yyy";
string connString = $"Provider=MSOLAP;Data Source=asazure://westus.asazure.windows.net/<servername>;User ID=app:{appId};Password={authKey};";
Server server = new Server();
server.Connect(connString);
Database db = server.Databases.FindByName("adventureworks");
Table tbl = db.Model.Tables.Find("DimDate");
tbl.RequestRefresh(RefreshType.Full);
db.Model.SaveChanges();
```

## <a name="next-steps"></a>Następne kroki
[Logowanie się za pomocą programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps)   
[Dodawanie jednostki usługi do roli administratora serwera](analysis-services-addservprinc-admins.md)   
