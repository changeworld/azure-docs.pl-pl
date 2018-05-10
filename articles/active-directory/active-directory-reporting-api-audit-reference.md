---
title: Azure Active Directory audytu dokumentacja interfejsu API | Dokumentacja firmy Microsoft
description: Jak rozpocząć pracę z interfejsem API inspekcji usługi Azure Active Directory
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 44e46be8-09e5-4981-be2b-d474aaa92792
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/08/2018
ms.author: dhanyahk;markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 1bf86a9190039cdf0fe8dc435bdee4308b28cf29
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="azure-active-directory-audit-api-reference"></a>Azure Active Directory audytu dokumentacja interfejsu API

> [!TIP] 
> Zapoznaj się z nowego interfejsu API programu Microsoft Graph, dla [raportowania](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit), która zastąpi ostatecznie tego interfejsu API. 


W tym artykule jest częścią kolekcję artykułów na temat usługi Azure Active Directory (Azure AD) raportowania interfejsu API. Raportowanie na platformie Azure AD zapewnia interfejs API, który umożliwia dostęp do danych inspekcji za pomocą kodu lub narzędzia pokrewne.
Zakres tego artykułu jest dostarczają informacje na temat **inspekcji interfejsu API**.

Zobacz:

* [Dzienniki inspekcji](active-directory-reporting-azure-portal.md#activity-reports) Aby uzyskać więcej informacji o pojęciach

* [Wprowadzenie do usługi Azure Active Directory interfejsu API raportowania](active-directory-reporting-api-getting-started.md) Aby uzyskać więcej informacji na temat raportowania interfejsu API.


W przypadku:

- Przeczytaj często zadawane pytania, [— często zadawane pytania](active-directory-reporting-faq.md) 

- Problemy, [pliku biletu pomocy technicznej](active-directory-troubleshooting-support-howto.md) 


## <a name="who-can-access-the-data"></a>Kto ma dostęp do danych?
* Użytkownicy w roli administratora zabezpieczeń lub czytelnika zabezpieczeń
* Administratorzy globalni
* Dowolną aplikację, która ma zezwolenie na dostęp do interfejsu API (autoryzacji aplikacji można skonfigurować tylko na podstawie uprawnienia administratora globalnego)

## <a name="prerequisites"></a>Wymagania wstępne
Aby uzyskać dostęp do tego raportu za pomocą interfejsu API raportowania, musi być:

* [Lepsze wersji lub Azure Active Directory bezpłatna](active-directory-editions.md)
* Ukończono [wymagania wstępne dotyczące raportowania interfejsu API usługi Azure AD dostęp](active-directory-reporting-api-prerequisites.md). 

## <a name="accessing-the-api"></a>Uzyskiwanie dostępu do interfejsu API
Albo dostęp do tego interfejsu API za pomocą [Explorer wykres](https://graphexplorer2.cloudapp.net) albo programowo z użyciem, na przykład środowiska PowerShell. Użyj backtick (alias: akcent) znaku "ucieczki" znak $, aby upewnić się, że PowerShell może interpretować składnia filtru OData używanym w wywołaniach REST Graph usługi AAD. Znak backtick służy jako [znak ucieczki dla środowiska PowerShell](https://technet.microsoft.com/library/hh847755.aspx), umożliwiając PowerShell literału interpretacja znak $, i uniknąć skomplikowana go jako nazwę zmiennej środowiska PowerShell (na przykład $filter).

Ten artykuł koncentruje się na Explorer wykresu. Na przykład środowiska PowerShell, zobacz [skrypt programu PowerShell](active-directory-reporting-api-audit-samples.md#powershell-script).

## <a name="api-endpoint"></a>Punkt końcowy interfejsu API

Aby dostęp do tego interfejsu API przy użyciu następującego identyfikatora URI:  

    https://graph.windows.net/contoso.com/activities/audit?api-version=beta

Nie ma żadnego limitu liczby rekordów, które są zwracane przez interfejs API inspekcji usługi Azure AD (przy użyciu podział na strony OData). Limitów przechowywania danych raportowania, zobacz [zasad przechowywania dotyczących okresu raportowania](active-directory-reporting-retention.md).

Wywołanie zwraca dane w partiach. Każdej z partii może zawierać maksymalnie 1000 rekordów. Aby uzyskać kolejną partię rekordów, użyj **dalej** łącza. Uzyskiwanie informacji o tokenie pomijania z pierwszego zestawu zwróconych rekordów. Pomiń token zostanie na końcu wynik ustawiona.  

    https://graph.windows.net/contoso.com/activities/audit?api-version=beta&%24skiptoken=-1339686058




## <a name="supported-filters"></a>Filtry obsługiwane

Można zawęzić liczbę rekordów, które są zwracane przez wywołanie interfejsu API z filtrem.  
Dane dotyczące interfejsu API logowania obsługiwane są następujące filtry:

* **$top =\<liczba rekordów, które ma zostać zwrócona\>**  — Aby ograniczyć liczbę zwróconych rekordów. Jest to kosztowna operacja. Nie używaj tego filtru, aby zwrócić tysiące obiektów.     
* **$filter =\<instrukcji filtru\>**  — Aby określić, na podstawie pól filtr obsługiwanych typu rekordów są dla Ciebie ważne

## <a name="supported-filter-fields-and-operators"></a>Operatory i pól filtr obsługiwane
Aby określić typ rekordów, które są dla Ciebie ważne, można utworzyć filtr instrukcja zawierająca co najmniej kombinację następujących pól Filtr:

* [Daty](#activitydate) — określa datę lub zakres dat
* [Kategoria](#category) — definiuje chcesz filtrować według kategorii.
* [Element activityStatus](#activitystatus) — definiuje stan działania
* [Właściwość activityType](#activitytype) — definiuje typ działania
* [działanie](#activity) — definiuje działania w postaci ciągu  
* [nazwisko/aktora](#actorname) — definiuje aktora w formie nazwy aktora
* [aktora/objectid](#actorobjectid) — definiuje aktora w postaci aktora o identyfikatorze   
* [aktora/nazwa upn](#actorupn) — definiuje aktora w postaci nazwy zasady aktora użytkownika (UPN) 
* [Nazwadocelowego/](#targetname) — definiuje obiekt docelowy w formie nazwy aktora
* [docelowy/objectid](#targetobjectid) — definiuje obiekt docelowy w formie identyfikatora obiektu docelowego  
* [docelowy/nazwa upn](#targetupn) — definiuje aktora w postaci nazwy zasady aktora użytkownika (UPN)   

- - -
### <a name="activitydate"></a>Daty
**Operatory obsługiwane**: eq, ge, le, gt, lt

**Przykład**:

    $filter=tdomain + 'activities/audit?api-version=beta&`$filter=activityDate gt ' + $7daysago    

**Uwagi dotyczące**:

Data i godzina powinny być w formacie UTC

- - -
### <a name="category"></a>category

**Obsługiwane wartości**:

| Kategoria                         | Wartość     |
| :--                              | ---       |
| Katalog podstawowy                   | Katalog |
| Samoobsługowe zarządzanie hasłami | SSPR      |
| Samoobsługowe zarządzanie grupami    | SSGM      |
| Aprowizacja kont             | Sync      |
| Automatyczne przenoszenie haseł      | Automatyczne przenoszenie haseł |
| Identity Protection              | IdentityProtection |
| Zaproszeni użytkownicy                    | Zaproszeni użytkownicy |
| Usługa MIM                      | Usługa MIM |



**Operatory obsługiwane**: eq

**Przykład**:

    $filter=category eq 'SSPR'
- - -
### <a name="activitystatus"></a>Element ActivityStatus

**Obsługiwane wartości**:

| Stan aktywności | Wartość |
| :--             | ---   |
| Powodzenie         | 0     |
| Niepowodzenie         | - 1   |

**Operatory obsługiwane**: eq

**Przykład**:

    $filter=activityStatus eq -1    

---
### <a name="activitytype"></a>Właściwość activityType
**Operatory obsługiwane**: eq

**Przykład**:

    $filter=activityType eq 'User'    

**Uwagi dotyczące**:

uwzględniana wielkość liter

- - -
### <a name="activity"></a>działanie
**Operatory obsługiwane**: eq, zawiera startsWith

**Przykład**:

    $filter=activity eq 'Add application' or contains(activity, 'Application') or startsWith(activity, 'Add')    

**Uwagi dotyczące**:

uwzględniana wielkość liter

- - -
### <a name="actorname"></a>nazwisko/aktora
**Operatory obsługiwane**: eq, zawiera startsWith

**Przykład**:

    $filter=actor/name eq 'test' or contains(actor/name, 'test') or startswith(actor/name, 'test')    

**Uwagi dotyczące**:

Bez uwzględniania wielkości liter

- - -
### <a name="actorobjectid"></a>aktora/objectId.
**Operatory obsługiwane**: eq

**Przykład**:

    $filter=actor/objectId eq 'e8096343-86a2-4384-b43a-ebfdb17600ba'    


- - -
### <a name="targetname"></a>Nazwa docelowego /
**Operatory obsługiwane**: eq, zawiera startsWith

**Przykład**:

    $filter=targets/any(t: t/name eq 'some name')    

**Uwagi dotyczące**:

Bez uwzględniania wielkości liter

- - -
### <a name="targetupn"></a>docelowy/nazwa upn
**Operatory obsługiwane**: eq, startsWith

**Przykład**:

    $filter=targets/any(t: startswith(t/Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.TargetResourceUserEntity/userPrincipalName,'abc'))    

**Uwagi dotyczące**:

* Bez uwzględniania wielkości liter
* Dodaj pełną przestrzeń nazw podczas wykonywania zapytania Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.TargetResourceUserEntity

- - -
### <a name="targetobjectid"></a>docelowy/objectId.
**Operatory obsługiwane**: eq

**Przykład**:

    $filter=targets/any(t: t/objectId eq 'e8096343-86a2-4384-b43a-ebfdb17600ba')    

- - -
### <a name="actorupn"></a>aktora/nazwa upn
**Operatory obsługiwane**: eq, startsWith

**Przykład**:

    $filter=startswith(actor/Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.ActorUserEntity/userPrincipalName,'abc')    

**Uwagi dotyczące**:

* Bez uwzględniania wielkości liter 
* Dodaj pełną przestrzeń nazw podczas wykonywania zapytania Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.ActorUserEntity

- - -
## <a name="next-steps"></a>Następne kroki

- Czy chcesz wyświetlić przykłady działań filtrowane systemu? Zapoznaj się z [przykłady inspekcji interfejsu API usługi Azure Active Directory](active-directory-reporting-api-audit-samples.md).

- Czy chcesz dowiedzieć się więcej na temat raportowania interfejsu API usługi Azure AD? Zobacz [wprowadzenie do usługi Azure Active Directory interfejsu API raportowania](active-directory-reporting-api-getting-started.md).

