---
title: Automatyzacji zadań usług Azure Analysis Services z nazwy główne usług | Dokumentacja firmy Microsoft
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 931a45fdbb04d15f3080ee0a2c0546ab6e924b59
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34595720"
---
# <a name="automation-with-service-principals"></a>Automatyzacja przy użyciu nazwy główne usług

Nazwy główne usług są zasobami aplikacji usługi Azure Active Directory utwórz w ramach dzierżawy do przeprowadzenia instalacji nienadzorowanej zasobów oraz operacji poziomu usługi. Są one unikatowe typu *tożsamości użytkownika* przy użyciu Identyfikatora aplikacji i hasło lub certyfikat. Nazwy głównej usługi ma tylko uprawnienia niezbędne do wykonywania zadań określone przez role i uprawnienia, do których jest przypisany. 

W usługach Analysis Services nazwy główne usług są używane przez usługi Automatyzacja Azure, programu PowerShell w trybie nienadzorowanym, aplikacje klienckie niestandardowe i aplikacje sieci web w celu automatyzacji typowych zadań. Na przykład serwery inicjowania obsługi administracyjnej, wdrażanie modeli, odświeżanie danych, skalowania w górę lub w dół, a wstrzymanie/wznowienie wszystkie można zautomatyzować za pomocą nazwy główne usług. Uprawnienia są przypisane do nazwy główne usług za pośrednictwem członkostwo roli, podobnie jak w regularnych kont usługi Azure AD głównej nazwy użytkownika.

## <a name="create-service-principals"></a>Tworzenie jednostek usługi
 
Nazwy główne usług mogą być tworzone w portalu Azure lub za pomocą programu PowerShell. Aby dowiedzieć się więcej, zobacz:

[Tworzenie nazwy głównej - usługi portalu Azure](../azure-resource-manager/resource-group-create-service-principal-portal.md)   
[Tworzenie jednostki usługi — PowerShell](../azure-resource-manager/resource-group-authenticate-service-principal.md)

## <a name="store-credential-and-certificate-assets-in-azure-automation"></a>Przechowywanie zasobów poświadczeń i certyfikatów w usłudze Automatyzacja Azure

Poświadczenia główne i certyfikaty mogą być przechowywane bezpiecznie w automatyzacji Azure w operacjach elementu runbook. Aby dowiedzieć się więcej, zobacz:

[Zasoby poświadczeń usługi Automatyzacja Azure](../automation/automation-credentials.md)   
[Zasoby certyfikatu usługi Automatyzacja Azure](../automation/automation-certificates.md)

## <a name="add-service-principals-to-server-admin-role"></a>Dodaj jednostki usługi do roli administratora serwera

Zanim użyjesz nazwy głównej usługi dla operacji zarządzania na serwerze usług Analysis Services, należy go dodać do roli administratora serwera. Aby dowiedzieć się więcej, zobacz [dodać nazwy głównej usługi do roli administratora serwera](analysis-services-addservprinc-admins.md).

## <a name="service-principals-in-connection-strings"></a>Nazwy główne usług w parametrach połączenia

Identyfikator aplikacji głównej usługi i hasło lub certyfikat mogą być używane w znacznie taki sam, jak nazwy UPN parametry połączenia.

### <a name="powershell"></a>PowerShell

W przypadku używania nazwy głównej usługi dla operacji zarządzania zasobów z [AzureRM.AnalysisServices](https://www.powershellgallery.com/packages/AzureRM.AnalysisServices) modułu, użyj `Login-AzureRmAccount` polecenia cmdlet. W przypadku używania nazwy głównej usługi dla operacji serwera z [SQLServer](https://www.powershellgallery.com/packages/SqlServer) modułu, użyj `Add-AzureAnalysisServicesAccount` polecenia cmdlet. 

W poniższym przykładzie appID i hasło są używane do wykonywania operacji odświeżania modelu bazy danych:

```PowerShell
Param (

        [Parameter(Mandatory=$true)] [String] $AppId,
        [Parameter(Mandatory=$true)] [String] $PlainPWord,
        [Parameter(Mandatory=$true)] [String] $TenantId
       )
$PWord = ConvertTo-SecureString -String $PlainPWord -AsPlainText -Force

$Credential = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList $AppId, $PWord

Add-AzureAnalysisServicesAccount -Credential $Credential -ServicePrincipal -TenantId $TenantId -RolloutEnvironment "westcentralus.asazure.windows.net"

Invoke-ProcessTable -Server "asazure://westcentralus.asazure.windows.net/myserver" -TableName "MyTable" -Database "MyDb" -RefreshType "Full"
```

### <a name="amo-and-adomd"></a>AMO i ADOMD 

Podczas nawiązywania połączenia z aplikacjami klienckimi i aplikacji sieci web, [AMO i ADOMD bibliotek klienckich](analysis-services-data-providers.md) wersji 15.0.2 i nowszej można zainstalować pakiety NuGet obsługuje nazwy główne usług w parametrach połączenia, używając następującej składni: `app:AppID` i hasło lub `cert:thumbprint`. 

W poniższym przykładzie `appID` i `password` są używane do wykonywania operacji odświeżania modelu bazy danych:

```C#
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

## <a name="next-steps"></a>Kolejne kroki
[Zaloguj się przy użyciu programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps)   
[Dodaj nazwy głównej usługi do roli administratora serwera](analysis-services-addservprinc-admins.md)   