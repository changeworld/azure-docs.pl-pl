---
title: Reguły członkostwa grupy dynamicznej automatyczne — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Jak utworzyć reguły członkostwa, aby automatycznie wypełnić grupy i odwołanie do reguły.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5a0e0508babdd9ae703e38d58b079ab5fa16f68c
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66397880"
---
# <a name="dynamic-membership-rules-for-groups-in-azure-active-directory"></a>Reguły członkostwa dynamicznego dla grup w usłudze Azure Active Directory

W usłudze Azure Active Directory (Azure AD) można utworzyć złożone zasady oparte na atrybutach, aby włączyć dynamiczne zarządzanie członkostwem w grupach. Dynamiczne członkostwo w grupie zmniejsza narzuty administracyjne, dodawania i usuwania użytkowników. Ten artykuł szczegółowo opisuje właściwości i składnię tworzenia reguły członkostwa dynamicznego dla użytkowników lub urządzeń. Możesz skonfigurować reguły dynamicznego zarządzania członkostwem w grupach zabezpieczeń lub w grupach usługi Office 365.

Po zmianie dowolnych atrybutów użytkownika lub urządzenia, system ocenia wszystkie reguły dynamicznego w grupach w katalogu, aby sprawdzić, czy zmiany będą wyzwalać żadnej grupy dodaje lub usuwa. Jeśli użytkownik lub urządzenie spełnia wymagania zasad grupy, dodawane jest członkiem tej grupy. Jeśli już nie spełniają zasady, są usuwane. Nie można ręcznie dodać lub usunąć element członkowski grupy dynamicznej.

* Można utworzyć grupę dynamiczną w przypadku urządzeń lub użytkowników, ale nie można utworzyć regułę, która zawiera użytkowników i urządzeń.
* Nie można utworzyć grupę urządzeń, na podstawie atrybutów właścicieli urządzeń. Reguły członkostwa urządzenie może odwoływać się tylko atrybuty urządzenia.

> [!NOTE]
> Ta funkcja wymaga licencji usługi Azure AD Premium P1 dla każdy unikatowy użytkownik, który jest członkiem jednej lub kilku grup dynamicznych. Nie trzeba przypisać licencje do użytkowników dla nich, aby były członkami grup dynamicznych, ale musi mieć minimalną liczbę licencji w ramach dzierżawy, aby objęły one wszystkich takich użytkowników. Na przykład jeśli masz łącznie 1000 unikatowych użytkowników we wszystkich grupach dynamicznych w Twojej dzierżawie, będziesz potrzebować co najmniej 1000 licencji dla usługi Azure AD Premium P1 spełnić wymagania licencyjne.
>

## <a name="constructing-the-body-of-a-membership-rule"></a>Konstruowanie treści reguły członkostwa

Reguły członkostwa, który automatycznie wypełnia grupę użytkowników lub urządzeń jest wyrażenia binarnego, powstałego w wyniku wartość PRAWDA lub FAŁSZ. Dostępne są następujące trzy części prostej reguły:

* Właściwość
* Operator
* Wartość

Kolejność elementów w obrębie wyrażenia są ważne, aby uniknąć błędów składniowych.

### <a name="rules-with-a-single-expression"></a>Reguły z pojedynczego wyrażenia

Pojedyncze wyrażenie jest to najprostsza forma reguły członkostwa i tylko ma trzy części, o których wspomniano powyżej. Reguła o jedno wyrażenie wygląda podobnie do następującej: `Property Operator Value`, gdzie składni dla właściwości jest nazwa obiekt.właściwość.

Oto przykład zastosowania reguły członkowskiej poprawnie skonstruowany przy użyciu pojedynczego wyrażenia:

```
user.department -eq "Sales"
```

Nawiasy są opcjonalne dla pojedynczego wyrażenia. Całkowita długość treści reguły członkostwa, nie może przekraczać 2048 znaków.

## <a name="supported-properties"></a>Obsługiwanych właściwości

Istnieją trzy typy, właściwości, które mogą służyć do tworzenia reguły członkostwa.

* Boolean
* String
* Kolekcji ciągów

Poniżej przedstawiono właściwości użytkownika, które można użyć do utworzenia pojedynczego wyrażenia.

### <a name="properties-of-type-boolean"></a>Właściwości typu boolean

| Właściwości | Dozwolone wartości | Sposób użycia |
| --- | --- | --- |
| accountEnabled |wartość true, false |true - eq user.accountEnabled |
| dirSyncEnabled |wartość true, false |true - eq user.dirSyncEnabled |

### <a name="properties-of-type-string"></a>Właściwości typu ciąg

| Właściwości | Dozwolone wartości | Sposób użycia |
| --- | --- | --- |
| city |Dowolną wartość ciągu lub *o wartości null* |(user.city - eq "value") |
| Kraj |Dowolną wartość ciągu lub *o wartości null* |(user.country - eq "value") |
| companyName | Dowolną wartość ciągu lub *o wartości null* | (user.companyName - eq "value") |
| Dział |Dowolną wartość ciągu lub *o wartości null* |(user.department - eq "value") |
| displayName |dowolną wartość ciągu |(user.displayName - eq "value") |
| employeeId |dowolną wartość ciągu |(user.employeeId - eq "value")<br>(user.employeeId - ne *null*) |
| facsimileTelephoneNumber |Dowolną wartość ciągu lub *o wartości null* |(user.facsimileTelephoneNumber - eq "value") |
| givenName |Dowolną wartość ciągu lub *o wartości null* |(user.givenName - eq "value") |
| Stanowisko |Dowolną wartość ciągu lub *o wartości null* |(user.jobTitle - eq "value") |
| poczta |Dowolną wartość ciągu lub *null* (adresu SMTP użytkownika) |(user.mail - eq "value") |
| mailNickName |Dowolną wartość ciągu (alias poczty użytkownika) |(user.mailNickName - eq "value") |
| Telefon komórkowy |Dowolną wartość ciągu lub *o wartości null* |(user.mobile - eq "value") |
| Identyfikator obiektu |Identyfikator GUID obiektu użytkownika |(user.objectId - eq "11111111-1111-1111-1111-111111111111") |
| onPremisesSecurityIdentifier | Lokalny identyfikator zabezpieczeń (SID) dla użytkowników, którzy zostały zsynchronizowane ze środowiska lokalnego do chmury. |(user.onPremisesSecurityIdentifier - eq "S-1-1-11-1111111111-1111111111-1111111111-1111111") |
| passwordPolicies |Brak DisableStrongPassword DisablePasswordExpiration DisablePasswordExpiration, DisableStrongPassword |(user.passwordPolicies - eq "DisableStrongPassword") |
| physicalDeliveryOfficeName |Dowolną wartość ciągu lub *o wartości null* |(user.physicalDeliveryOfficeName - eq "value") |
| postalCode |Dowolną wartość ciągu lub *o wartości null* |(user.postalCode - eq "value") |
| preferredLanguage |ISO 639-1 kodu |(user.preferredLanguage - eq "en US") |
| sipProxyAddress |Dowolną wartość ciągu lub *o wartości null* |(user.sipProxyAddress - eq "value") |
| state |Dowolną wartość ciągu lub *o wartości null* |(user.state - eq "value") |
| Adres |Dowolną wartość ciągu lub *o wartości null* |(user.streetAddress - eq "value") |
| nazwisko |Dowolną wartość ciągu lub *o wartości null* |(user.surname - eq "value") |
| telephoneNumber |Dowolną wartość ciągu lub *o wartości null* |(user.telephoneNumber - eq "value") |
| usageLocation |Numer kierunkowy kraju własną literą dwa |(user.usageLocation - eq "PL") |
| userPrincipalName |dowolną wartość ciągu |(user.userPrincipalName - eq "alias@domain") |
| userType |element członkowski gościa *o wartości null* |(user.userType - eq "Członek") |

### <a name="properties-of-type-string-collection"></a>Właściwości typu kolekcji ciągów

| Właściwości | Dozwolone wartości | Sposób użycia |
| --- | --- | --- |
| otherMails |dowolną wartość ciągu |(user.otherMails — zawiera "alias@domain") |
| proxyAddresses |SMTP: alias@domain smtp: alias@domain |(user.proxyAddresses — zawiera "SMTP: alias@domain") |

Dla właściwości, używany do reguły urządzeń, zobacz [reguły dla urządzeń](#rules-for-devices).

## <a name="supported-operators"></a>Operatory obsługiwane

W poniższej tabeli wymieniono operatory obsługiwane i ich składnię w jednym wyrażeniu. Można używać operatorów, z lub bez prefiksu łącznika (-).

| Operator | Składnia |
| --- | --- |
| Nie równa się |-ne |
| Równa się |-eq |
| Nie zaczyna się od |-notStartsWith |
| Rozpoczyna się od |startsWith — |
| Nie zawiera |-notContains |
| zawiera |-zawiera |
| Nie jest zgodne |-notMatch |
| Dopasowanie |-dopasowania |
| W | -w |
| Nie w | -notIn |

### <a name="using-the--in-and--notin-operators"></a>Przy użyciu w i notIn — operatory

Jeśli chcesz porównać wartości atrybutu użytkownika względem szereg różnych wartości możesz użyć w - notIn operatory lub. Użyj symboli nawiasu "[" i "]" na początku i końcu, na liście wartości.

 W poniższym przykładzie wyrażenie ma wartość true, jeśli wartość user.department jest równa wartości na liście:

```
   user.department -in ["50001","50002","50003","50005","50006","50007","50008","50016","50020","50024","50038","50039","51100"]
```


### <a name="using-the--match-operator"></a>Za pomocą operatora — dopasowanie 
**-Dopasowania** operator jest używany do dopasowywania dowolne wyrażenie regularne. Przykłady:

```
user.displayName -match "Da.*"   
```
David da, Dav, przyjmowało wartość true, aDa zwróci wartość false.

```
user.displayName -match ".*vid"
```
David zwraca wartość true, Da zwróci wartość false.

## <a name="supported-values"></a>Obsługiwane wartości

Wartość używana w wyrażeniu może składać się z kilku typów, w tym:

* Ciągi
* Boolean — wartość true, false
* Numery
* Tablice — tablicy liczb, tablica ciągów

Podczas określania wartości w wyrażeniu jest ważne, aby stosować prawidłową składnię, aby uniknąć błędów. Dostępne są następujące wskazówki dotyczące składni:

* Podwójne cudzysłowy są opcjonalne, chyba że wartość jest ciągiem.
* Operacje na ciąg i wyrażeń regularnych są z uwzględnieniem wielkości liter.
* Jeśli wartość ciągu zawiera podwójne cudzysłowy, zarówno oferty należy otaczać \` znak, na przykład user.department - eq \`"sprzedaż\`" jest poprawna składnia, gdy "Sprzedaż" jest wartością.
* Można również wykonać sprawdzanie wartości Null przy użyciu wartości null jako wartość, na przykład `user.department -eq null`.

### <a name="use-of-null-values"></a>Użyj wartości Null

Aby określić wartość null w regule, można użyć *null* wartość. 

* Użyj - eq lub - ne, podczas porównywania *null* wartości wyrażenia.
* Użyj słowa w cudzysłowie *null* tylko wtedy, gdy ma być interpretowany jako wartość literału ciągu.
* Nie nie można używać operatora jako operatora porównawczych dla wartości null. Jeśli jest ona używana, wystąpi błąd, czy używać wartości null lub $null.

Poprawny sposób odwoływać się do wartości null, jest następujący:

```
   user.mail –ne null
```

## <a name="rules-with-multiple-expressions"></a>Reguły z wielu wyrażeń

Reguły członkostwa grupy może zawierać więcej niż jedno wyrażenie pojedynczego połączone przez i, -, lub i — operatory logiczne nie. Operatory logiczne może również służyć w połączeniu. 

Poniżej przedstawiono przykłady reguł członkostwa poprawnie skonstruowany przy użyciu wielu wyrażeń:

```
(user.department -eq "Sales") -or (user.department -eq "Marketing")
(user.department -eq "Sales") -and -not (user.jobTitle -contains "SDE")
```

### <a name="operator-precedence"></a>Pierwszeństwo operatorów

Poniżej przedstawiono wszystkie operatory w kolejność pierwszeństwa od najwyższego do najniższego. Operatory w tym samym wierszu mają równy priorytet:

```
-eq -ne -startsWith -notStartsWith -contains -notContains -match –notMatch -in -notIn
-not
-and
-or
-any -all
```

Oto przykład pierwszeństwo operatorów gdzie dwóch wyrażeń są oceniane pod kątem użytkownika:

```
   user.department –eq "Marketing" –and user.country –eq "US"
```

Nawiasy są wymagane tylko wtedy, gdy pierwszeństwo nie spełnia wymagań. Na przykład jeśli chcesz, aby dział jest stosowana jako pierwsza, poniżej przedstawiono sposób nawiasów może służyć do określenia kolejności:

```
   user.country –eq "US" –and (user.department –eq "Marketing" –or user.department –eq "Sales")
```

## <a name="rules-with-complex-expressions"></a>Reguły z złożonych wyrażeń

Reguły członkostwa może składać się w przypadku gdy podjąć właściwości, operatory i wartości w formularzach bardziej złożonych wyrażeń złożonych. Wyrażenia są uważane za złożonych, gdy spełnione są następujące czynności:

* Właściwość składa się z kolekcji wartości. w szczególności wielowartościowe właściwości
* Użyj wyrażenia wszelkie - wszystkie operatory i
* Wartość wyrażenia może być co najmniej jednego wyrażenia

## <a name="multi-value-properties"></a>Właściwości wielu wartości

Właściwości wielu wartości są kolekcjami obiektów tego samego typu. Może służyć do tworzenia reguł członkostwa przy użyciu wszelkie i - wszystkich operatorów logicznych.

| Właściwości | Wartości | Sposób użycia |
| --- | --- | --- |
| assignedPlans | Każdy obiekt w kolekcji udostępnia następujące właściwości ciągu: capabilityStatus, usługi, servicePlanId |user.assignedPlans — wszystkie (assignedPlan.servicePlanId - eq "efb87545-963c-4e0d-99df-69c6916d9eb0"- a assignedPlan.capabilityStatus - eq "Enabled") |
| proxyAddresses| SMTP: alias@domain smtp: alias@domain | (user.proxyAddresses — wszystkie (\_ — zawiera "contoso")) |

### <a name="using-the--any-and--all-operators"></a>Przy użyciu wszelkie - wszystkie operatory i

Możesz użyć - wszelkie - wszystkie operatory i do zastosowania warunku do jednego lub wszystkich elementów w kolekcji, odpowiednio.

* -dowolny (spełnione, gdy co najmniej jeden element w kolekcji dopasowuje warunek)
* — wszystkie (spełnione, gdy wszystkie elementy w kolekcji zgodne z warunkiem)

#### <a name="example-1"></a>Przykład 1

assignedPlans jest właściwością wielu wartości, która zawiera listę wszystkich planach usługi przypisane do użytkownika. Poniższe wyrażenie wybiera użytkowników, którzy mają usługi Exchange Online (Plan 2) planu usługi (jako wartość identyfikatora GUID), który jest w stanie włączone:

```
user.assignedPlans -any (assignedPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -and assignedPlan.capabilityStatus -eq "Enabled")
```

Reguły, taką jak ta może służyć do grupowania wszystkich użytkowników, dla którego usługi Office 365 (lub innych usług Microsoft Online Services) możliwość jest włączona. Za pomocą zestawu zasad można następnie zastosować do grupy.

#### <a name="example-2"></a>Przykład 2

Poniższe wyrażenie wybiera wszystkich użytkowników, którzy mają wszystkie plan usługi, który jest skojarzony z usługą Intune (identyfikowanych na podstawie nazwy usługi "SCO"):

```
user.assignedPlans -any (assignedPlan.service -eq "SCO" -and assignedPlan.capabilityStatus -eq "Enabled")
```

### <a name="using-the-underscore--syntax"></a>Za pomocą znaku podkreślenia (\_) składni

Podkreślenie (\_) składni uwzględnia wystąpień określonej wartości w jednej z właściwości kolekcji parametrów wielowartościowych, aby dodać użytkowników lub urządzeń do grupy dynamicznej. Jest używane z parametrem lub - wszystkim operatorów.

Poniżej przedstawiono przykład użycia znaku podkreślenia (\_) w regule można dodać elementy członkowskie w oparciu o user.proxyAddress (działa tak samo dla user.otherMails). Ta reguła dodaje każdy użytkownik z adresem serwera proxy, który zawiera "contoso" do grupy.

```
(user.proxyAddresses -any (_ -contains "contoso"))
```

## <a name="other-properties-and-common-rules"></a>Inne właściwości i wspólnych zasad

### <a name="create-a-direct-reports-rule"></a>Utwórz regułę "Bezpośrednich podwładnych"

Można utworzyć grupy obejmującej wszystkich bezpośrednich podwładnych menedżera. Jeśli Menedżer bezpośrednich podwładnych zmienia się w przyszłości, członkostwo w grupie jest automatycznie dostosowywany.

Reguła bezpośrednich podwładnych jest konstruowany przy użyciu następującej składni:

```
Direct Reports for "{objectID_of_manager}"
```

Oto przykład prawidłowa reguła, gdzie "62e19b97-8b3d-4d4a-a106-4ce66896a863" to atrybut objectID Menedżera:

```
Direct Reports for "62e19b97-8b3d-4d4a-a106-4ce66896a863"
```

Poniższe porady mogą pomóc należy poprawnie używać reguły.

* **Identyfikator menedżera** jest identyfikator obiektu menedżera. Można je znaleźć w Menedżerze **profilu**.
* Reguła działa, upewnij się, **Menedżera** poprawnie ustawić właściwości dla użytkowników w dzierżawie. Możesz sprawdzić bieżącą wartość w użytkownika **profilu**.
* Ta reguła obsługuje tylko raporty bezpośrednie menedżera. Innymi słowy, nie można utworzyć grupy za pomocą Menedżera raportów bezpośrednich *i* swoich raportów.
* Ta zasada nie można łączyć z innymi regułami członkostwa.

### <a name="create-an-all-users-rule"></a>Utwórz regułę "Wszyscy użytkownicy"

Można utworzyć grupy obejmującej wszystkich użytkowników w ramach dzierżawy przy użyciu reguły członkostwa. Gdy użytkownicy są dodawani lub usunięte z dzierżawy w przyszłości, członkostwo w grupie jest automatycznie dostosowywany.

Reguła "All users" jest tworzony, przy użyciu pojedynczego wyrażenia przy użyciu operatora - ne i wartość null. Ta reguła dodaje użytkownicy-goście B2B, a także użytkowników-członków do grupy.

```
user.objectid -ne null
```

### <a name="create-an-all-devices-rule"></a>Utwórz regułę "Wszystkie urządzenia"

Można utworzyć grupę zawierającą wszystkie urządzenia w ramach dzierżawy przy użyciu reguły członkostwa. Gdy urządzenia są dodawane lub usuwane z dzierżawy w przyszłości, członkostwo w grupie jest automatycznie dostosowywany.

Reguła "Wszystkie urządzenia" jest konstruowany przy użyciu pojedynczego wyrażenia przy użyciu operatora - ne i wartość null:

```
device.objectid -ne null
```

## <a name="extension-properties-and-custom-extension-properties"></a>Właściwości rozszerzenia i właściwości niestandardowego rozszerzenia

Rozszerzeń atrybuty i właściwości niestandardowego rozszerzenia są obsługiwane jako parametry właściwości reguły członkostwa dynamicznego. Atrybuty rozszerzenia są synchronizowane z lokalną Windows Server AD i mieć format "ExtensionAttributeX", gdzie X jest równa 1 – 15. Oto przykład reguły używającej atrybutu rozszerzenia jako właściwość:

```
(user.extensionAttribute15 -eq "Marketing")
```

Właściwości niestandardowe rozszerzenia są synchronizowane z lokalnej usługi Windows Server AD lub z połączonych aplikacji SaaS i mają format `user.extension_[GUID]__[Attribute]`, gdzie:

* [Identyfikator GUID] jest unikatowym identyfikatorem w usłudze Azure AD dla aplikacji, który utworzył właściwość w usłudze Azure AD
* [Attribute] jest nazwa właściwości, która została utworzona

Jest przykładem regułę, która używa właściwości niestandardowego rozszerzenia:

```
user.extension_c272a57b722d4eb29bfe327874ae79cb__OfficeNumber -eq "123"
```

Nazwa właściwości niestandardowych można znaleźć w katalogu, wysyłając zapytanie do właściwości przy użyciu Eksploratora programu Graph i wyszukując nazwę właściwości. Ponadto można teraz wybrać **pobieranie właściwości niestandardowego rozszerzenia** łącze w Konstruktorze użytkownik dynamiczny grupy reguł, możesz wprowadzić identyfikator unikatowy aplikacji i otrzymywać pełną listę właściwości niestandardowego rozszerzenia do użycia podczas tworzenia reguły członkostwa dynamicznego. Tej listy można odświeżać w taki sposób, aby uzyskać nowe właściwości rozszerzenia niestandardowego dla danej aplikacji.

## <a name="rules-for-devices"></a>Reguły urządzeń

Można również utworzyć regułę, która wybiera obiekty urządzeń do członkostwa w grupie. Członkowie grupy nie może być użytkowników i urządzeń. **OrganizationalUnit** atrybut nie jest już znajduje się na liście i nie powinna być używana. Ten ciąg jest ustawiony przez usługę Intune w szczególnych przypadkach, ale nie został rozpoznany przez usługę Azure AD, dlatego żadne urządzenia są dodawane do grupy na podstawie tego atrybutu.

Następujące atrybuty urządzenia może służyć.

 Atrybutu urządzenia  | Wartości | Przykład
 ----- | ----- | ----------------
 accountEnabled | wartość true, false | (device.accountEnabled - eq true)
 displayName | dowolną wartość ciągu |(device.displayName - eq "Rob iPhone")
 deviceOSType | dowolną wartość ciągu | (device.deviceOSType - eq "iPad")- lub (device.deviceOSType - eq "iPhone")<br>(device.deviceOSType -contains "AndroidEnterprise")<br>(device.deviceOSType - eq "AndroidForWork")
 deviceOSVersion | dowolną wartość ciągu | (device.deviceOSVersion -eq "9.1")
 deviceCategory | Nazwa kategorii prawidłowe urządzenie | (device.deviceCategory - eq "BYOD")
 deviceManufacturer | dowolną wartość ciągu | (device.deviceManufacturer - eq "Samsung")
 deviceModel | dowolną wartość ciągu | (device.deviceModel - eq "iPad Air")
 deviceOwnership | Osobiste, firma, nieznany | (device.deviceOwnership - eq "Firma")
 enrollmentProfileName | Nazwa profilu profilu rejestracji urządzeń firmy Apple lub rozwiązania Windows Autopilot | (device.enrollmentProfileName - eq "IPhone DEP")
 isRooted | wartość true, false | (device.isRooted - eq true)
 managementType | Zarządzanie urządzeniami Przenośnymi (dla urządzeń przenośnych)<br>Komputer (w przypadku komputerów zarządzanych przez agenta PC usługi Intune) | (device.managementType - eq "MDM")
 deviceId | Nieprawidłowy identyfikator urządzenia usługi Azure AD | (device.deviceId -eq "d4fe7726-5966-431c-b3b8-cddc8fdb717d")
 Identyfikator obiektu | Identyfikator obiektu prawidłowy, usługa Azure AD |  (device.objectId -eq 76ad43c9-32c5-45e8-a272-7b58b58f596d")
 systemLabels | dowolny ciąg dopasowania właściwości urządzenia usługi Intune do znakowania nowoczesnym miejscu pracy urządzenia | (device.systemLabels — zawiera "M365Managed")

> [!Note]  
> Dla deviceOwnership podczas tworzenia grup dynamicznych w przypadku urządzeń, należy ustawić wartość równa "Firma". W usłudze Intune własność urządzenia jest zamiast tego reprezentowana jako należące do firmy. Zapoznaj się [OwnerTypes](https://docs.microsoft.com/intune/reports-ref-devices#ownertypes) Aby uzyskać więcej informacji. 

## <a name="next-steps"></a>Kolejne kroki

Te artykuły zawierają dodatkowe informacje na temat grup w usłudze Azure Active Directory.

* [Wyświetlanie istniejących grup](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Tworzenie nowej grupy i dodawanie członków](../fundamentals/active-directory-groups-create-azure-portal.md)
* [Zarządzanie ustawieniami grupy](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [Zarządzanie członkostwem w grupie](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [Zarządzanie regułami dynamicznymi dla użytkowników w grupie](groups-create-rule.md)
