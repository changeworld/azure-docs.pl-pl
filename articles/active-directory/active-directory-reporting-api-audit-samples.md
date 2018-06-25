---
title: Przykłady interfejsu API inspekcji raportowania usługi Azure Active Directory | Microsoft Docs
description: Jak rozpocząć pracę z interfejsem API raportowania usługi Azure Active Directory
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: de8b8ec3-49b3-4aa8-93fb-e38f52c99743
ms.service: active-directory
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 05/30/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 240095b64f4b7b37a71f84ce8d7a3521e0ab7c03
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36223889"
---
# <a name="azure-active-directory-reporting-audit-api-samples"></a>Przykłady interfejsu API inspekcji raportowania usługi Azure Active Directory
Ten artykuł jest częścią kolekcji artykułów na temat interfejsu API raportowania usługi Azure Active Directory.  
Raportowanie usługi Azure AD udostępnia interfejs API, który umożliwia dostęp do danych inspekcji za pomocą kodu lub narzędzi pokrewnych.
Zakresem tego artykułu jest przedstawienie przykładowego kodu **interfejsu API inspekcji**.

Zobacz:

* [Dzienniki inspekcji](active-directory-reporting-azure-portal.md#activity-reports), aby uzyskać więcej informacji koncepcyjnych
* [Wprowadzenie do interfejsu API raportowania usługi Azure Active Directory](active-directory-reporting-api-getting-started.md), aby uzyskać więcej informacji na temat interfejsu API raportowania.

W przypadku pytań, problemów lub opinii należy kontaktować się z [pomocą dotyczącą raportowania w usłudze AAD](mailto:aadreportinghelp@microsoft.com).


## <a name="prerequisites"></a>Wymagania wstępne
Zanim będzie możliwe skorzystanie z przykładów w tym artykule, trzeba spełnić [warunki wstępne uzyskiwania dostępu do interfejsu API raportowania usługi Azure AD](active-directory-reporting-api-prerequisites.md).  

## <a name="known-issue"></a>Znany problem
Uwierzytelnianie aplikacji nie będzie działać, jeśli dzierżawa znajduje się w regionie UE. Do czasu rozwiązania tego problemu jako obejścia przy uzyskiwaniu dostępu do interfejsu API inspekcji należy używać uwierzytelniania użytkownika. 

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
Po zakończeniu edytowania skryptu uruchom go i sprawdź, czy zwrócone zostały oczekiwane dane z raportu dzienników inspekcji.

Skrypt zwraca dane wyjściowe z raportu inspekcji w formacie JSON. Tworzy również plik `Audits.json` z tymi samymi danymi wyjściowymi. Możesz poeksperymentować, modyfikując skrypt tak, aby zwracał dane z innych raportów, i wykomentowując niepotrzebne formaty danych wyjściowych.





## <a name="next-steps"></a>Następne kroki
* Chcesz dostosować przykłady w tym artykule? Zapoznaj się z [dokumentacją interfejsu API inspekcji usługi Azure Active Directory](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit). 
* Jeśli chcesz zobaczyć pełny przegląd korzystania z interfejsu API raportowania usługi Azure Active Directory, zobacz [Wprowadzenie do interfejsu API raportowania usługi Azure Active Directory](active-directory-reporting-api-getting-started.md).
* Aby dowiedzieć się więcej na temat raportowania w usłudze Azure Active Directory, zobacz [Azure Active Directory Reporting Guide](active-directory-reporting-guide.md) (Przewodnik po raportowaniu w usłudze Azure Active Directory).  

