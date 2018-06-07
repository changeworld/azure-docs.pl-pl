---
title: Raportowanie usługi Active Directory na platformie Azure inspekcji przykłady interfejsu API | Dokumentacja firmy Microsoft
description: Jak rozpocząć pracę z usługi Azure Active Directory raportowania interfejsu API
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: de8b8ec3-49b3-4aa8-93fb-e38f52c99743
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 05/30/2018
ms.author: dhanyahk;rolyon
ms.reviewer: dhanyahk
ms.openlocfilehash: 77ecb1f0c4b3614c9692715edae21a09f261b277
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34588168"
---
# <a name="azure-active-directory-reporting-audit-api-samples"></a>Usługa Azure Active Directory raportowania przykłady inspekcji interfejsu API
W tym artykule jest częścią kolekcję artykułów na temat usługi Azure Active Directory raportowania interfejsu API.  
Raportowanie na platformie Azure AD zapewnia interfejs API, który umożliwia dostęp do danych inspekcji za pomocą kodu lub narzędzia pokrewne.
Zakres tego artykułu jest zapewnienie przykładowy kod **inspekcji interfejsu API**.

Zobacz:

* [Dzienniki inspekcji](active-directory-reporting-azure-portal.md#activity-reports) Aby uzyskać więcej informacji o pojęciach
* [Wprowadzenie do usługi Azure Active Directory interfejsu API raportowania](active-directory-reporting-api-getting-started.md) Aby uzyskać więcej informacji na temat raportowania interfejsu API.

Pytania, problemy lub opinie, skontaktuj się z [Pomoc raportowania usługi AAD](mailto:aadreportinghelp@microsoft.com).


## <a name="prerequisites"></a>Wymagania wstępne
Zanim użyjesz przykłady w tym artykule, należy wykonać [wymagania wstępne dotyczące raportowania interfejsu API usługi Azure AD dostęp](active-directory-reporting-api-prerequisites.md).  

## <a name="known-issue"></a>Znany problem
Uwierzytelniania aplikacji nie będzie działać, jeśli dzierżawy znajduje się w regionie UE. Użyj uwierzytelniania użytkowników do uzyskiwania dostępu do interfejsu API inspekcji jako rozwiązanie alternatywne do czasu firma Microsoft rozwiązać problem. 

## <a name="powershell-script"></a>Skrypt programu PowerShell


```powershell

# This script will require the Web Application and permissions setup in Azure Active Directory
$clientID       = "<appid>"             # ApplicationId
$clientSecret   = "<key>"         # Should be a ~44 character string insert your info here
$loginURL       = "https://login.windows.net/"
$tenantdomain   = "<domain>"            # For example, contoso.onmicrosoft.com
$msgraphEndpoint = "https://graph.microsoft.com"
$countOfAuditDocsToBeSavedInAFile = 2000
    
# Get an Oauth 2 access token based on client id, secret and tenant domain
$body       = @{grant_type="client_credentials";resource=$msgraphEndpoint;client_id=$clientID;client_secret=$clientSecret}
$oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body
    
if ($oauth.access_token -ne $null) {
    $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}
    
    $url = "$msgraphEndpoint/beta/auditLogs/directoryAudits"
    Write-Output "Fetching data using Uri: $url"
    $i=0
    $docCount=0
    Do{
        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
        $jsonReport = ($myReport.Content | ConvertFrom-Json).value
        $fetchedRecordCount = $jsonReport.Count
        $docCount = $docCount + $fetchedRecordCount
        $totalFetchedRecordCount = $totalFetchedRecordCount + $fetchedRecordCount
        Write-Output "Fetched $fetchedRecordCount records and saved into Audits$i.json"
        if($docCount -le $countOfAuditDocsToBeSavedInAFile)
        {
            $myReport.Content | Out-File -FilePath Audits$i.json -append  -Force        
        }
        else
        {           
            $docCount=0
            $i = $i+1
        }
            
        #Get url from next link
        $url = ($myReport.Content | ConvertFrom-Json).'@odata.nextLink'         
    }while($url -ne $null)
    Write-Output "Total Fetched record count is : $totalFetchedRecordCount"
                
} else {
    Write-Host "ERROR: No Access Token"
}

```

### <a name="executing-the-powershell-script"></a>Wykonywanie skryptu programu PowerShell
Zwracany jest po Zakończ edycję skryptu, uruchom go i sprawdź, czy oczekiwane dane z inspekcji logowania raportu.

Skrypt zwraca dane wyjściowe z raportu inspekcji w formacie JSON. Tworzy również `Audits.json` plik o tych samych danych wyjściowych. Możesz eksperymentować, modyfikując skryptu, aby zwrócić dane z innych raportów ujmij w komentarz formatów wyjściowych, które nie ma potrzeby.





## <a name="next-steps"></a>Kolejne kroki
* Czy chcesz dostosować przykłady w tym artykule? Zapoznaj się z [inspekcji usługi Azure Active Directory dokumentacja interfejsu API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit). 
* Jeśli chcesz zobaczyć pełny przegląd przy użyciu usługi Azure Active Directory interfejsem API raportowania, zobacz [wprowadzenie do korzystania z usługi Azure Active Directory interfejsem API raportowania](active-directory-reporting-api-getting-started.md).
* Jeśli chcesz dowiedzieć się więcej o usłudze Azure Active Directory, zobacz [Azure Active Directory Przewodnik po raportach](active-directory-reporting-guide.md).  

