---
title: Przykłady interfejsu API raport aktywności logowania w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Jak rozpocząć pracę z usługi Azure Active Directory raportowania interfejsu API
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: c41c1489-726b-4d3f-81d6-83beb932df9c
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 05/31/2018
ms.author: dhanyahk;rolyon
ms.reviewer: dhanyahk
ms.openlocfilehash: 466755d7d1cc7fbf4006826ac849b74ba306bae9
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34698581"
---
# <a name="azure-active-directory-sign-in-activity-report-api-samples"></a>Przykłady interfejsu API raport aktywności logowania w usłudze Azure Active Directory
W tym artykule jest częścią kolekcję artykułów na temat usługi Azure Active Directory raportowania interfejsu API.  
Raportowanie na platformie Azure AD zapewnia interfejs API, który umożliwia dostęp do danych działań logowania za pomocą kodu lub narzędzia pokrewne.  
Zakres tego artykułu jest zapewnienie przykładowy kod **logowania działanie interfejsu API**.

Zobacz:

* [Dzienniki inspekcji](active-directory-reporting-azure-portal.md#activity-reports) Aby uzyskać więcej informacji o pojęciach
* [Wprowadzenie do usługi Azure Active Directory interfejsu API raportowania](active-directory-reporting-api-getting-started.md) Aby uzyskać więcej informacji na temat raportowania interfejsu API.


## <a name="prerequisites"></a>Wymagania wstępne
Zanim użyjesz przykłady w tym artykule, należy wykonać [wymagania wstępne dotyczące raportowania interfejsu API usługi Azure AD dostęp](active-directory-reporting-api-prerequisites.md).  

## <a name="powershell-script"></a>Skrypt programu PowerShell

```powershell

# This script will require the Web Application and permissions setup in Azure Active Directory
$clientID       = "<appid>"             # ApplicationId
$clientSecret   = "<key>"         # Should be a ~44 character string insert your info here
$loginURL       = "https://login.windows.net/"
$tenantdomain   = "<domain>"            # For example, contoso.onmicrosoft.com
$msgraphEndpoint = "https://graph.microsoft.com"
$countOfSignInDocsToBeSavedInAFile = 2000
    
# Get an Oauth 2 access token based on client id, secret and tenant domain
$body       = @{grant_type="client_credentials";resource=$msgraphEndpoint;client_id=$clientID;client_secret=$clientSecret}
$oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body
    
if ($oauth.access_token -ne $null) {
    $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}
    
    $url = "$msgraphEndpoint/beta/auditLogs/signIns"
    Write-Output "Fetching data using Uri: $url"
    $i=0
    $docCount=0
    Do{
        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
        $jsonReport = ($myReport.Content | ConvertFrom-Json).value
        $fetchedRecordCount = $jsonReport.Count
        $docCount = $docCount + $fetchedRecordCount
        $totalFetchedRecordCount = $totalFetchedRecordCount + $fetchedRecordCount
        Write-Output "Fetched $fetchedRecordCount records and saved into SignIns$i.json"
        if($docCount -le $countOfSignInDocsToBeSavedInAFile)
        {
            $myReport.Content | Out-File -FilePath SignIns$i.json -append  -Force       
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




## <a name="executing-the-script"></a>Wykonanie skryptu
Zwracany jest po Zakończ edycję skryptu, uruchom go i sprawdź, czy oczekiwane dane z przy logowaniu rejestruje raportu.

Skrypt zwraca dane wyjściowe z raportu logowania w formacie JSON. Tworzy również `SignIns.json` plik o tych samych danych wyjściowych. Możesz eksperymentować, modyfikując skryptu, aby zwrócić dane z innych raportów ujmij w komentarz formatów wyjściowych, które nie ma potrzeby.

## <a name="next-steps"></a>Następne kroki
* Czy chcesz dostosować przykłady w tym artykule? Zapoznaj się z [działania usługi Azure Active Directory logowania dokumentacja interfejsu API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin). 
* Jeśli chcesz zobaczyć pełny przegląd przy użyciu usługi Azure Active Directory interfejsem API raportowania, zobacz [wprowadzenie do korzystania z usługi Azure Active Directory interfejsem API raportowania](active-directory-reporting-api-getting-started.md).
* Jeśli chcesz dowiedzieć się więcej o usłudze Azure Active Directory, zobacz [Azure Active Directory Przewodnik po raportach](active-directory-reporting-guide.md).  

