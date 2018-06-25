---
title: Przykłady interfejsu API raportowania działań logowania usługi Azure Active Directory | Microsoft Docs
description: Jak rozpocząć pracę z interfejsem API raportowania usługi Azure Active Directory
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: c41c1489-726b-4d3f-81d6-83beb932df9c
ms.service: active-directory
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 05/31/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 52d49770014a5fb6a5eec644868e702c8a8d9ef3
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36224902"
---
# <a name="azure-active-directory-sign-in-activity-report-api-samples"></a>Przykłady interfejsu API raportów działań logowania usługi Azure Active Directory
Ten artykuł jest częścią kolekcji artykułów na temat interfejsu API raportowania usługi Azure Active Directory.  
Funkcja raportowania usługi Azure AD udostępnia interfejs API, który umożliwia dostęp do danych działań logowania za pomocą kodu lub narzędzi pokrewnych.  
Zakresem tego artykułu jest przedstawienie przykładowego kodu **interfejsu API działań logowania**.

Zobacz:

* [Dzienniki inspekcji](active-directory-reporting-azure-portal.md#activity-reports), aby uzyskać więcej informacji koncepcyjnych
* [Wprowadzenie do interfejsu API raportowania usługi Azure Active Directory](active-directory-reporting-api-getting-started.md), aby uzyskać więcej informacji na temat interfejsu API raportowania.


## <a name="prerequisites"></a>Wymagania wstępne
Zanim będzie możliwe skorzystanie z przykładów w tym artykule, trzeba spełnić [warunki wstępne uzyskiwania dostępu do interfejsu API raportowania usługi Azure AD](active-directory-reporting-api-prerequisites.md).  

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




## <a name="executing-the-script"></a>Wykonywanie skryptu
Po zakończeniu edytowania skryptu uruchom go i sprawdź, czy zwrócone zostały oczekiwane dane z raportu dzienników logowania.

Skrypt zwraca dane wyjściowe z raportu logowania w formacie JSON. Tworzy również plik `SignIns.json` z tymi samymi danymi wyjściowymi. Możesz poeksperymentować, modyfikując skrypt tak, aby zwracał dane z innych raportów, i wykomentowując niepotrzebne formaty danych wyjściowych.

## <a name="next-steps"></a>Następne kroki
* Chcesz dostosować przykłady w tym artykule? Zapoznaj się z [dokumentacją interfejsu API działań logowania usługi Azure Active Directory](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin). 
* Jeśli chcesz zobaczyć pełny przegląd korzystania z interfejsu API raportowania usługi Azure Active Directory, zobacz [Wprowadzenie do interfejsu API raportowania usługi Azure Active Directory](active-directory-reporting-api-getting-started.md).
* Aby dowiedzieć się więcej na temat raportowania w usłudze Azure Active Directory, zobacz [Azure Active Directory Reporting Guide](active-directory-reporting-guide.md) (Przewodnik po raportowaniu w usłudze Azure Active Directory).  

