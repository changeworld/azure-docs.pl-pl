---
title: Raport aktywności logowania w usłudze Azure usługi Active Directory dokumentacja interfejsu API | Dokumentacja firmy Microsoft
description: Odwołanie do API raport aktywności logowania usługi Azure Active Directory
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: ddcd9ae0-f6b7-4f13-a5e1-6cbf51a25634
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/08/2018
ms.author: dhanyahk;markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: dbb95b5910def55437f05837986e850824fbe741
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="azure-active-directory-sign-in-activity-report-api-reference"></a>Raport aktywności logowania w usłudze Azure usługi Active Directory dokumentacja interfejsu API

> [!TIP] 
> Zapoznaj się z nowego interfejsu API programu Microsoft Graph, dla [raportowania](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit), która zastąpi ostatecznie tego interfejsu API. 

W tym artykule jest częścią kolekcję artykułów na temat usługi Azure Active Directory (Azure AD) raportowania interfejsu API. Raportowanie na platformie Azure AD zapewnia interfejs API, który umożliwia dostęp do danych inspekcji za pomocą kodu lub narzędzia pokrewne.
Zakres tego artykułu jest dostarczają informacje na temat **inspekcji interfejsu API**.

Zobacz:

* [Działania logowania](active-directory-reporting-azure-portal.md#activity-reports) Aby uzyskać więcej informacji o pojęciach
* [Wprowadzenie do usługi Azure Active Directory interfejsu API raportowania](active-directory-reporting-api-getting-started.md) Aby uzyskać więcej informacji na temat raportowania interfejsu API.


## <a name="who-can-access-the-api-data"></a>Kto ma dostęp do danych interfejsu API?
* Użytkownicy i nazwy główne usług w roli administratora zabezpieczeń lub czytnika zabezpieczeń
* Administratorzy globalni
* Dowolną aplikację, która ma zezwolenie na dostęp do interfejsu API (autoryzacji aplikacji można skonfigurować tylko na podstawie uprawnienia administratora globalnego)

Konfigurowanie dostępu dla aplikacji, aby uzyskać dostępu do interfejsów API zabezpieczeń, takich jak zdarzenia logowania, należy użyć do dodawania aplikacji nazwy głównej usługi do roli zabezpieczeń czytnika następujące programu PowerShell

```PowerShell
Connect-MsolService
$servicePrincipal = Get-MsolServicePrincipal -AppPrincipalId "<app client id>"
$role = Get-MsolRole | ? Name -eq "Security Reader"
Add-MsolRoleMember -RoleObjectId $role.ObjectId -RoleMemberType ServicePrincipal -RoleMemberObjectId $servicePrincipal.ObjectId
```

## <a name="prerequisites"></a>Wymagania wstępne
Aby uzyskać dostęp do tego raportu za pomocą interfejsu API raportowania, musi być:

* [Edition usługi Azure Active Directory Premium P1 lub P2](active-directory-editions.md)
* Ukończono [wymagania wstępne dotyczące raportowania interfejsu API usługi Azure AD dostęp](active-directory-reporting-api-prerequisites.md). 

## <a name="accessing-the-api"></a>Uzyskiwanie dostępu do interfejsu API
Albo dostęp do tego interfejsu API za pomocą [Explorer wykres](https://graphexplorer2.cloudapp.net) albo programowo z użyciem, na przykład środowiska PowerShell. Użyj backtick (alias: akcent) znaku "ucieczki" znak $, aby upewnić się, że PowerShell może interpretować składnia filtru OData używanym w wywołaniach REST Graph usługi AAD. Znak backtick służy jako [znak ucieczki dla środowiska PowerShell](https://technet.microsoft.com/library/hh847755.aspx), umożliwiając PowerShell literału interpretacja znak $, i uniknąć skomplikowana go jako nazwę zmiennej środowiska PowerShell (na przykład $filter).

Ten temat koncentruje się w Eksploratorze wykresu. Na przykład środowiska PowerShell, zobacz [skrypt programu PowerShell](active-directory-reporting-api-sign-in-activity-samples.md#powershell-script).

## <a name="api-endpoint"></a>Punkt końcowy interfejsu API
Aby dostęp do tego interfejsu API przy użyciu następujących podstawowy identyfikator URI:  

    https://graph.windows.net/contoso.com/activities/signinEvents?api-version=beta  



Ze względu na ilość danych ten interfejs API ma limit 1 000 000 rekordów zwróconych. 

To wywołanie zwraca dane w partiach. Każdej z partii może zawierać maksymalnie 1000 rekordów. Aby uzyskać kolejną partię rekordów, użyj łącze do następnej. Pobierz [skiptoken](https://msdn.microsoft.com/library/dd942121.aspx) informacji z pierwszego zestawu zwróconych rekordów. Pomiń token zostanie na końcu wynik ustawiona.  

    https://graph.windows.net/$tenantdomain/activities/signinEvents?api-version=beta&%24skiptoken=-1339686058


## <a name="supported-filters"></a>Filtry obsługiwane
Liczba rekordów, które są zwracane przez interfejs API można zawęzić wywołaj w formularzu filtru.  
Dane dotyczące interfejsu API logowania obsługiwane są następujące filtry:

* **$top =\<liczba rekordów, które ma zostać zwrócona\>**  — Aby ograniczyć liczbę zwróconych rekordów. Jest to kosztowna operacja. Nie używaj tego filtru, aby zwrócić tysiące obiektów.  
* **$filter =\<instrukcji filtru\>**  — Aby określić, na podstawie pól filtr obsługiwanych typu rekordów są dla Ciebie ważne

## <a name="supported-filter-fields-and-operators"></a>Operatory i pól filtr obsługiwane
Aby określić typ rekordów, które są dla Ciebie ważne, można utworzyć filtr instrukcję, która może zawierać jeden lub kombinację następujących pól Filtr:

* [signinDateTime](#signindatetime) — określa datę lub zakres dat
* [Identyfikator userId](#userid) — definiuje określonego użytkownika na podstawie identyfikatora użytkownika.
* [userPrincipalName](#userprincipalname) — definiuje określonego użytkownika na podstawie użytkownika nazwa główna użytkownika (UPN)
* [Identyfikator appId](#appid) — definiuje określonej aplikacji na podstawie Identyfikatora aplikacji
* [appDisplayName](#appdisplayname) — definiuje określonej aplikacji na podstawie nazwy wyświetlanej aplikacji
* [stanu logowania](#loginStatus) — definiuje stan logowania (Powodzenie / błąd)

> [!NOTE]
> Podczas używania Eksploratora wykresu, trzeba mieć poprawną wielkość dla każdej litery jest pól filtr.
> 
> 

Aby zawęzić zakres zwrócone dane, można tworzyć kombinacje obsługiwanych filtry i pola filtru. Na przykład następująca instrukcja zwraca top 10 rekordów między 1 lipca 2016 r i lipca 2016 6:

    https://graph.windows.net/contoso.com/activities/signinEvents?api-version=beta&$top=10&$filter=signinDateTime+ge+2016-07-01T17:05:21Z+and+signinDateTime+le+2016-07-07T00:00:00Z


- - -
### <a name="signindatetime"></a>signinDateTime
**Operatory obsługiwane**: eq, ge, le, gt, lt

**Przykład**:

Przy użyciu określonej daty

    $filter=signinDateTime+eq+2016-04-25T23:59:00Z    



Przy użyciu zakresu dat    

    $filter=signinDateTime+ge+2016-07-01T17:05:21Z+and+signinDateTime+le+2016-07-07T17:05:21Z


**Uwagi dotyczące**:

Parametr daty/godziny, powinna być w formacie UTC 

- - -
### <a name="userid"></a>userId
**Operatory obsługiwane**: eq

**Przykład**:

    $filter=userId+eq+’00000000-0000-0000-0000-000000000000’

**Uwagi dotyczące**:

Wartość Nazwa użytkownika jest wartość ciągu

- - -
### <a name="userprincipalname"></a>userPrincipalName
**Operatory obsługiwane**: eq

**Przykład**:

    $filter=userPrincipalName+eq+'audrey.oliver@wingtiptoysonline.com' 


**Uwagi dotyczące**:

Wartość userPrincipalName jest wartość ciągu

- - -
### <a name="appid"></a>appId
**Operatory obsługiwane**: eq

**Przykład**:

    $filter=appId+eq+’00000000-0000-0000-0000-000000000000’



**Uwagi dotyczące**:

Wartość Identyfikator appId jest wartość ciągu

- - -
### <a name="appdisplayname"></a>appDisplayName
**Operatory obsługiwane**: eq

**Przykład**:

    $filter=appDisplayName+eq+'Azure+Portal' 


**Uwagi dotyczące**:

Wartość ciągu jest wartością appDisplayName

- - -
### <a name="loginstatus"></a>stanu logowania
**Operatory obsługiwane**: eq

**Przykład**:

    $filter=loginStatus+eq+'1'  


**Uwagi dotyczące**:

Dostępne są dwie opcje dla stanu logowania: 0 - Sukces, 1 — błąd

- - -
## <a name="next-steps"></a>Kolejne kroki
* Czy chcesz wyświetlić przykłady filtrowane działań logowania? Zapoznaj się z [przykłady raportu interfejsu API działania usługi Azure Active Directory](active-directory-reporting-api-sign-in-activity-samples.md).
* Czy chcesz dowiedzieć się więcej na temat raportowania interfejsu API usługi Azure AD? Zobacz [wprowadzenie do usługi Azure Active Directory interfejsu API raportowania](active-directory-reporting-api-getting-started.md).

