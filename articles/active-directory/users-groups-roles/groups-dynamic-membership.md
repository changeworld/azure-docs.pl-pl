---
title: Reguły członkostwa grup dynamicznie zaludnionych — Usługa Azure AD | Dokumenty firmy Microsoft
description: Jak utworzyć reguły członkostwa, aby automatycznie wypełniać grupy i odwołanie do reguły.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/27/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d6f8237ac13744e56baa8551f8cced12b2785a48
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81114739"
---
# <a name="dynamic-membership-rules-for-groups-in-azure-active-directory"></a>Dynamiczne reguły członkostwa dla grup w usłudze Azure Active Directory

W usłudze Azure Active Directory (Azure AD) można tworzyć złożone reguły oparte na atrybutach, aby włączyć dynamiczne członkostwo dla grup. Członkostwo w grupach dynamicznych zmniejsza obciążenie administracyjne związane z dodawaniem i usuwaniem użytkowników. W tym artykule opisano właściwości i składnię tworzenia dynamicznych reguł członkostwa dla użytkowników lub urządzeń. Możesz skonfigurować reguły dynamicznego zarządzania członkostwem w grupach zabezpieczeń lub w grupach usługi Office 365.

Po zmianie jakichkolwiek atrybutów użytkownika lub urządzenia system ocenia wszystkie reguły grupy dynamicznej w katalogu, aby sprawdzić, czy zmiana wyzwoliłaby dowolne grupy dodaje lub usuwa. Jeśli użytkownik lub urządzenie spełnia regułę w grupie, są dodawane jako członek tej grupy. Jeśli nie spełniają już reguły, zostaną usunięte. Nie można ręcznie dodać ani usunąć członka grupy dynamicznej.

- Można utworzyć grupę dynamiczną dla urządzeń lub użytkowników, ale nie można utworzyć reguły, która zawiera zarówno użytkowników, jak i urządzenia.
- Nie można utworzyć grupy urządzeń na podstawie atrybutów właścicieli urządzeń. Reguły członkostwa urządzenia mogą odwoływać się tylko do atrybutów urządzenia.

> [!NOTE]
> Ta funkcja wymaga licencji Usługi Azure AD Premium P1 dla każdego unikatowego użytkownika, który jest członkiem jednej lub więcej grup dynamicznych. Nie trzeba przypisywać licencji użytkownikom, aby byli członkami grup dynamicznych, ale musisz mieć minimalną liczbę licencji w dzierżawie, aby objąć wszystkich takich użytkowników. Na przykład jeśli masz łącznie 1000 unikatowych użytkowników we wszystkich grupach dynamicznych w dzierżawie, trzeba będzie co najmniej 1000 licencji dla usługi Azure AD Premium P1, aby spełnić wymagania licencyjne.
> Licencja nie jest wymagana dla urządzeń, które są członkami dynamicznej grupy urządzeń.

## <a name="rule-builder-in-the-azure-portal"></a>Konstruktor reguł w witrynie Azure portal

Usługa Azure AD udostępnia konstruktora reguł, aby szybciej tworzyć i aktualizować ważne reguły. Konstruktor reguł obsługuje konstrukcję do pięciu wyrażeń. Konstruktor reguł ułatwia tworzenie reguły za pomocą kilku prostych wyrażeń, jednak nie można jej odtworzyć do odtworzenia każdej reguły. Jeśli konstruktor reguł nie obsługuje reguły, którą chcesz utworzyć, możesz użyć tego pola tekstowego.

Oto kilka przykładów zaawansowanych reguł lub składni, dla których zaleca się konstruowanie przy użyciu pola tekstowego:

- Reguła z więcej niż pięcioma wyrażeniami
- Reguła Raporty bezpośrednie
- Ustawianie [pierwszeństwa operatora](groups-dynamic-membership.md#operator-precedence)
- [Reguły ze złożonymi wyrażeniami;](groups-dynamic-membership.md#rules-with-complex-expressions) na przykład`(user.proxyAddresses -any (_ -contains "contoso"))`

> [!NOTE]
> Konstruktor reguł może nie być w stanie wyświetlić niektórych reguł skonstruowanych w polu tekstowym. Może zostać wyświetlony komunikat, gdy konstruktor reguł nie jest w stanie wyświetlić reguły. Konstruktor reguł nie zmienia w żaden sposób obsługiwanej składni, sprawdzania poprawności ani przetwarzania reguł grupy dynamicznej.

Aby uzyskać więcej instrukcji krok po kroku, zobacz [Tworzenie lub aktualizowanie grupy dynamicznej](groups-create-rule.md).

![Dodawanie reguły członkostwa dla grupy dynamicznej](./media/groups-dynamic-membership/update-dynamic-group-rule.png)

### <a name="rule-syntax-for-a-single-expression"></a>Składnia reguły dla pojedynczego wyrażenia

Pojedyncze wyrażenie jest najprostszą formą reguły członkostwa i ma tylko trzy części wymienione powyżej. Reguła z pojedynczym wyrażeniem `Property Operator Value`wygląda podobnie do tej: , gdzie składnią właściwości jest nazwa object.property.

Poniżej przedstawiono przykład prawidłowo skonstruowanej reguły członkostwa za pomocą jednego wyrażenia:

```
user.department -eq "Sales"
```

Nawiasy są opcjonalne dla pojedynczego wyrażenia. Całkowita długość treści reguły członkostwa nie może przekraczać 2048 znaków.

## <a name="constructing-the-body-of-a-membership-rule"></a>Konstruowanie treści reguły członkostwa

Reguła członkostwa, która automatycznie wypełnia grupę użytkownikami lub urządzeniami, jest wyrażeniem binarnym, które powoduje wynik true lub false. Trzy części prostej zasady to:

- Właściwość
- Operator
- Wartość

Kolejność części w wyrażeniu są ważne, aby uniknąć błędów składni.

## <a name="supported-properties"></a>Obsługiwane właściwości

Istnieją trzy typy właściwości, które mogą służyć do konstruowania reguły członkostwa.

- Wartość logiczna
- Ciąg
- Kolekcja ciągów

Poniżej przedstawiono właściwości użytkownika, których można użyć do utworzenia pojedynczego wyrażenia.

### <a name="properties-of-type-boolean"></a>Właściwości typu logicznego

| Właściwości | Dozwolone wartości | Sposób użycia |
| --- | --- | --- |
| kontoWłasny |prawdziwe fałdy |user.accountEnabled -eq prawda |
| dirSyncEnabled |prawdziwe fałdy |user.dirSyncEnabled -eq prawda |

### <a name="properties-of-type-string"></a>Właściwości ciągu tekstowego

| Właściwości | Dozwolone wartości | Sposób użycia |
| --- | --- | --- |
| city |Dowolna wartość ciągu lub *wartość null* |(user.city -eq "wartość") |
| country |Dowolna wartość ciągu lub *wartość null* |(user.country -eq "wartość") |
| Companyname | Dowolna wartość ciągu lub *wartość null* | (user.companyName -eq "wartość") |
| department |Dowolna wartość ciągu lub *wartość null* |(user.department -eq "wartość") |
| displayName |Dowolna wartość ciągu |(user.displayName -eq "wartość") |
| Idpracownika |Dowolna wartość ciągu |(user.employeeId -eq "wartość")<br>(user.employeeId -ne *null*) |
| numer telefonu faksymiiltelephoneNumber |Dowolna wartość ciągu lub *wartość null* |(user.facsimileTelephoneNumber -eq "wartość") |
| givenName |Dowolna wartość ciągu lub *wartość null* |(user.givenName -eq "wartość") |
| jobTitle (Tytuł pracy) |Dowolna wartość ciągu lub *wartość null* |(user.jobTitle -eq "wartość") |
| mail (poczta) |Dowolna wartość ciągu lub *wartość null* (adres SMTP użytkownika) |(user.mail -eq "wartość") |
| mailNickName |Dowolna wartość ciągu (alias poczty użytkownika) |(user.mailNickName -eq "wartość") |
| telefon komórkowy |Dowolna wartość ciągu lub *wartość null* |(user.mobile -eq "wartość") |
| Objectid |Identyfikator GUID obiektu użytkownika |(user.objectId -eq "11111111-1111-1111-1111-1111111111111111111111111" |
| onPremisesSecurityIdentifier | Lokalny identyfikator zabezpieczeń (SID) dla użytkowników, którzy zostali zsynchronizowani z lokalnego do chmury. |(user.onPremisesSecurityIdentifier -eq "S-1-1-11-111111111111-111111111111-1111111111-11111111") |
| hasłoPolicja |Brak DisableStrongPassword DisablePasswordExpiration DisablePasswordExpiration, DisableStrongPassword |(user.passwordPolicies -eq "DisableStrongPassword") |
| physicalDeliveryOfficeName |Dowolna wartość ciągu lub *wartość null* |(user.physicalDeliveryOfficeName -eq "value") |
| Postalcode |Dowolna wartość ciągu lub *wartość null* |(user.postalKodek -eq "wartość") |
| preferowany Język |Kod ISO 639-1 |(user.preferredLanguage -eq "en-US") |
| sipProxyAddress |Dowolna wartość ciągu lub *wartość null* |(user.sipProxyAddress -eq "wartość") |
| state |Dowolna wartość ciągu lub *wartość null* |(user.state -eq "value") |
| Streetaddress |Dowolna wartość ciągu lub *wartość null* |(user.streetAddress -eq "wartość") |
| surname |Dowolna wartość ciągu lub *wartość null* |(user.surname -eq "value") |
| Telephonenumber |Dowolna wartość ciągu lub *wartość null* |(user.phoneNumber -eq "wartość") |
| usageLocation (korzystanieLokalizacja) |Kod kraju z dwoma literami |(user.usageLokalizacja -eq "USA") |
| userPrincipalName |Dowolna wartość ciągu |(user.userPrincipalName -eqalias@domain" ") |
| Usertype |element członkowski *null* |(user.userType -eq "Członek") |

### <a name="properties-of-type-string-collection"></a>Właściwości kolekcji ciągów typu

| Właściwości | Dozwolone wartości | Sposób użycia |
| --- | --- | --- |
| inneMaile |Dowolna wartość ciągu |(user.otherMails -zawieraalias@domain" ") |
| proxyAddresses |SMTP: alias@domain smtp:alias@domain |(user.proxyAddresses -contains "SMTP: alias@domain") |

Właściwości używane dla reguł urządzeń można znaleźć w [opisie reguł dla urządzeń](#rules-for-devices).

## <a name="supported-expression-operators"></a>Obsługiwane operatory wyrażeń

W poniższej tabeli wymieniono wszystkie obsługiwane operatory i ich składnię dla pojedynczego wyrażenia. Operatory mogą być używane z prefiksem łącznika (-) lub bez niego.

| Operator | Składnia |
| --- | --- |
| Nie równa się |-ne |
| Równa się |-eq |
| Nie zaczyna się od |-notStartsWith |
| Zaczyna się od |-startsWith |
| Nie zawiera |-notContains |
| Contains |-zawiera |
| Nie pasuje |-notMatch |
| Dopasowanie |-mecz |
| W | -w |
| Nie w | -notIn |

### <a name="using-the--in-and--notin-operators"></a>Korzystanie z operatorów -in i -notIn

Jeśli chcesz porównać wartość atrybutu użytkownika z wieloma różnymi wartościami, możesz użyć operatorów -in lub -notIn. Użyj symboli nawiasu "[" i "]", aby rozpocząć i zakończyć listę wartości.

 W poniższym przykładzie wyrażenie ma wartość true, jeśli wartość user.department jest równa dowolnej z wartości na liście:

```
   user.department -in ["50001","50002","50003","50005","50006","50007","50008","50016","50020","50024","50038","50039","51100"]
```


### <a name="using-the--match-operator"></a>Korzystanie z operatora -match 
Operator **-match** służy do dopasowywania dowolnego wyrażenia regularnego. Przykłady:

```
user.displayName -match "Da.*"   
```
Da, Dav, David ocenić na prawdziwe, aDa ocenia fałszywe.

```
user.displayName -match ".*vid"
```
David ocenia do prawdziwego, Da ocenia fałszywe.

## <a name="supported-values"></a>Obsługiwane wartości

Wartości używane w wyrażeniu mogą składać się z kilku typów, w tym:

* Ciągi
* Wartość logiczna – prawda, fałsz
* Numery
* Tablice – tablica numerów, tablica ciągów

Podczas określania wartości w wyrażeniu należy użyć poprawnej składni, aby uniknąć błędów. Niektóre wskazówki dotyczące składni to:

* Cudzysłowy są opcjonalne, chyba że wartość jest ciągiem.
* W operacjach ciągów i znaków wzbijowych nie rozróżnia się wielkość liter.
* Gdy wartość ciągu zawiera cudzysłowy podwójne, \` oba cudzysłowy powinny być \`zmienione\`przy użyciu znaku, na przykład user.department -eq "Sprzedaż" jest właściwą składnią, gdy "Sprzedaż" jest wartością.
* Można również wykonać kontrole null, używając wartości null `user.department -eq null`jako wartości, na przykład .

### <a name="use-of-null-values"></a>Używanie wartości null

Aby określić wartość null w regule, można użyć wartości *null.* 

* Użyj -eq lub -ne podczas porównywania wartości *null* w wyrażeniu.
* Cytaty można używać wokół słowa *null* tylko wtedy, gdy ma być interpretowane jako wartość ciągu literału.
* Operator -not nie może być używany jako operator porównawczy dla wartości null. Jeśli go używasz, pojawia się błąd, czy używasz null lub $null.

Prawidłowy sposób odwoływania się do wartości null jest następujący:

```
   user.mail –ne null
```

## <a name="rules-with-multiple-expressions"></a>Reguły z wieloma wyrażeniami

Reguła członkostwa w grupie może składać się z więcej niż jednego wyrażenia połączonego operatorami logicznymi -i, -lub i -not. Operatory logiczne mogą być również używane w połączeniu. 

Poniżej przedstawiono przykłady poprawnie skonstruowanych reguł członkostwa z wieloma wyrażeniami:

```
(user.department -eq "Sales") -or (user.department -eq "Marketing")
(user.department -eq "Sales") -and -not (user.jobTitle -contains "SDE")
```

### <a name="operator-precedence"></a>Pierwszeństwo operatorów

Wszystkie operatory są wymienione poniżej w kolejności pierwszeństwa od najwyższego do najniższego. Operatory w tym samym wierszu mają jednakowy priorytet:

```
-eq -ne -startsWith -notStartsWith -contains -notContains -match –notMatch -in -notIn
-not
-and
-or
-any -all
```

Poniżej przedstawiono przykład pierwszeństwa operatora, gdzie dwa wyrażenia są oceniane dla użytkownika:

```
   user.department –eq "Marketing" –and user.country –eq "US"
```

Nawiasy są potrzebne tylko wtedy, gdy pierwszeństwo nie spełnia wymagań. Na przykład jeśli chcesz, aby dział był oceniany jako pierwszy, poniżej przedstawiono, jak nawiasy mogą być używane do określania kolejności:

```
   user.country –eq "US" –and (user.department –eq "Marketing" –or user.department –eq "Sales")
```

## <a name="rules-with-complex-expressions"></a>Reguły ze złożonymi wyrażeniami

Reguła członkostwa może składać się ze złożonych wyrażeń, w których właściwości, operatory i wartości przyjmują bardziej złożone formy. Wyrażenia są uważane za złożone, gdy spełnione są następujące wyrażenia:

* Właściwość składa się z kolekcji wartości; w szczególności właściwości wielowartościowe
* Wyrażenia używają operatorów -any i -all
* Wartość wyrażenia może być jednym lub kilkoma wyrażeniami

## <a name="multi-value-properties"></a>Właściwości wielowartymi

Właściwości o wielu wartościach są kolekcjami obiektów tego samego typu. Mogą one służyć do tworzenia reguł członkostwa przy użyciu -any i -all operatorów logicznych.

| Właściwości | Wartości | Sposób użycia |
| --- | --- | --- |
| przypisanePlany | Każdy obiekt w kolekcji udostępnia następujące właściwości ciągu: capabilityStatus, service, servicePlanId |user.assignedPlans -any (assignedPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -and assignedPlan.capabilityStatus -eq "Enabled") |
| proxyAddresses| SMTP: alias@domain smtp:alias@domain | (user.proxyAddresses -any\_ ( -contains "contoso")) |

### <a name="using-the--any-and--all-operators"></a>Korzystanie z operatorów -any i -all

Można użyć -any i -all operatorów, aby zastosować warunek do jednego lub wszystkich elementów w kolekcji, odpowiednio.

* -any (spełnione, gdy co najmniej jeden element w kolekcji pasuje do warunku)
* -all (spełnione, gdy wszystkie elementy w kolekcji odpowiadają warunkowi)

#### <a name="example-1"></a>Przykład 1

assignedPlans jest wielowartowiową właściwością, która wyświetla listę wszystkich planów usług przypisanych do użytkownika. Następujące wyrażenie wybiera użytkowników, którzy mają plan usługi Exchange Online (Plan 2) (jako wartość GUID), który jest również w stanie Włączone:

```
user.assignedPlans -any (assignedPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -and assignedPlan.capabilityStatus -eq "Enabled")
```

Reguła taka jak ta może służyć do grupowania wszystkich użytkowników, dla których jest włączona usługa Office 365 (lub inna usługa Microsoft Online Service). Następnie można zastosować z zestawem zasad do grupy.

#### <a name="example-2"></a>Przykład 2

Następujące wyrażenie wybiera wszystkich użytkowników, którzy mają dowolny plan usług skojarzony z usługą Intune (identyfikowany przez nazwę usługi "SCO"):

```
user.assignedPlans -any (assignedPlan.service -eq "SCO" -and assignedPlan.capabilityStatus -eq "Enabled")
```

### <a name="using-the-underscore-_-syntax"></a>Korzystanie ze\_składni podkreślenia ( )

Składnia\_podkreślenia ( ) dopasowuje wystąpienia określonej wartości w jednej z właściwości kolekcji ciągów wielowartościowych, aby dodać użytkowników lub urządzenia do grupy dynamicznej. Jest on używany z -any lub -all operatorów.

Oto przykład użycia podkreślenia\_( ) w regule, aby dodać członków na podstawie user.proxyAddress (działa tak samo dla user.otherMails). Ta reguła dodaje do grupy dowolnego użytkownika z adresem serwera proxy zawierającego "contoso".

```
(user.proxyAddresses -any (_ -contains "contoso"))
```

## <a name="other-properties-and-common-rules"></a>Inne właściwości i wspólne zasady

### <a name="create-a-direct-reports-rule"></a>Tworzenie reguły "Raporty bezpośrednie"

Można utworzyć grupę zawierającą wszystkie bezpośrednie raporty menedżera. Gdy bezpośrednie raporty menedżera zmienią się w przyszłości, członkostwo grupy jest dostosowywane automatycznie.

Reguła raportów bezpośrednich jest konstruowana przy użyciu następującej składni:

```
Direct Reports for "{objectID_of_manager}"
```

Oto przykład prawidłowej reguły, w której "62e19b97-8b3d-4d4a-a106-4ce66896a863" jest obiektemid menedżera:

```
Direct Reports for "62e19b97-8b3d-4d4a-a106-4ce66896a863"
```

Poniższe wskazówki mogą pomóc w prawidłowym użyciu reguły.

- **Identyfikator menedżera** jest identyfikatorem obiektu menedżera. Można go znaleźć w **profilu**menedżera .
- Aby reguła działała, upewnij się, że właściwość **managera** jest ustawiona poprawnie dla użytkowników w dzierżawie. Bieżącą wartość można sprawdzić w **profilu**użytkownika .
- Ta reguła obsługuje tylko bezpośrednie raporty menedżera. Innymi słowy nie można utworzyć grupy z bezpośrednimi raportami menedżera *i* ich raportami.
- Tej reguły nie można łączyć z innymi regułami członkostwa.

### <a name="create-an-all-users-rule"></a>Tworzenie reguły "Wszyscy użytkownicy"

Można utworzyć grupę zawierającą wszystkich użytkowników w dzierżawie przy użyciu reguły członkostwa. Gdy użytkownicy są dodawane lub usuwane z dzierżawy w przyszłości, członkostwo grupy jest dostosowywane automatycznie.

Reguła "Wszyscy użytkownicy" jest skonstruowana przy użyciu pojedynczego wyrażenia przy użyciu operatora -ne i wartości null. Ta reguła dodaje b2b użytkowników-gości, a także użytkowników członkowskich do grupy.

```
user.objectId -ne null
```
Jeśli chcesz, aby grupa wykluczała użytkowników-gości i obejmowała tylko członków dzierżawy, możesz użyć następującej składni:

```
(user.objectId -ne null) -and (user.userType -eq "Member")
```

### <a name="create-an-all-devices-rule"></a>Tworzenie reguły "Wszystkie urządzenia"

Można utworzyć grupę zawierającą wszystkie urządzenia w dzierżawie przy użyciu reguły członkostwa. Gdy urządzenia są dodawane lub usuwane z dzierżawy w przyszłości, członkostwo grupy jest dostosowywane automatycznie.

Reguła "Wszystkie urządzenia" jest skonstruowana przy użyciu pojedynczego wyrażenia przy użyciu operatora -ne i wartości null:

```
device.objectId -ne null
```

## <a name="extension-properties-and-custom-extension-properties"></a>Właściwości rozszerzenia i niestandardowe właściwości rozszerzenia

Atrybuty rozszerzenia i niestandardowe właściwości rozszerzenia są obsługiwane jako właściwości ciągu w dynamicznych regułach członkostwa. [Atrybuty rozszerzenia](https://docs.microsoft.com/graph/api/resources/onpremisesextensionattributes?view=graph-rest-1.0) są synchronizowane z lokalnego serwera okna usługi AD i przyjmują format "ExtensionAttributeX", gdzie X jest równa 1 - 15. Oto przykład reguły, która używa atrybutu rozszerzenia jako właściwości:

```
(user.extensionAttribute15 -eq "Marketing")
```

[Niestandardowe właściwości rozszerzeń](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-feature-directory-extensions) są synchronizowane z lokalnego systemu Windows Server AD lub `user.extension_[GUID]_[Attribute]`z podłączonej aplikacji SaaS i mają format , gdzie:

* [GUID] to unikatowy identyfikator w usłudze Azure AD dla aplikacji, która utworzyła właściwość w usłudze Azure AD
* [Atrybut] to nazwa właściwości, która została utworzona

Przykładem reguły korzystającej z niestandardowej właściwości rozszerzenia jest:

```
user.extension_c272a57b722d4eb29bfe327874ae79cb_OfficeNumber -eq "123"
```

Nazwę właściwości niestandardowej można znaleźć w katalogu, przeprowadzając kwerendę właściwości użytkownika za pomocą Eksploratora wykresu i wyszukując nazwę właściwości. Ponadto można teraz wybrać **łącze Pobierz niestandardowe właściwości rozszerzenia** w konstruktorze reguł dynamicznej grupy użytkowników, aby wprowadzić unikatowy identyfikator aplikacji i otrzymać pełną listę niestandardowych właściwości rozszerzenia, które będą używane podczas tworzenia dynamicznej reguły członkostwa. Tę listę można również odświeżyć, aby uzyskać nowe niestandardowe właściwości rozszerzenia dla tej aplikacji.

## <a name="rules-for-devices"></a>Reguły dla urządzeń

Można również utworzyć regułę, która wybiera obiekty urządzenia do członkostwa w grupie. Nie możesz mieć zarówno użytkowników, jak i urządzeń jako członków grupy. 

> [!NOTE]
> Atrybut **organizationalUnit** nie jest już wyświetlany i nie powinien być używany. Ten ciąg jest ustawiany przez usługę Intune w określonych przypadkach, ale nie jest rozpoznawany przez usługę Azure AD, więc żadne urządzenia nie są dodawane do grup na podstawie tego atrybutu.

> [!NOTE]
> systemlabels jest atrybutem tylko do odczytu, który nie może być ustawiony w usłudze Intune.
>
> W systemie Windows 10 prawidłowy format atrybutu deviceOSVersion jest następujący: (device.deviceOSVersion -eq "10.0.17763"). Formatowanie można sprawdzić za pomocą polecenia cmdlet Programu Get-MsolDevice PowerShell.

Można użyć następujących atrybutów urządzenia.

 Atrybut urządzenia  | Wartości | Przykład
 ----- | ----- | ----------------
 kontoWłasny | prawdziwe fałdy | (device.accountEnabled -eq true)
 displayName | dowolna wartość ciągu |(device.displayName -eq "Rob iPhone")
 deviceOSType | dowolna wartość ciągu | (device.deviceOSType -eq "iPad") -lub (device.deviceOSType -eq "iPhone")<br>(device.deviceOSType -zawiera "AndroidEnterprise")<br>(device.deviceOSType -eq "AndroidForWork")
 deviceOSVersion | dowolna wartość ciągu | (device.deviceOSVersion -eq "9.1")
 kategoria urządzenia | prawidłowa nazwa kategorii urządzenia | (device.device Kategoria -eq "BYOD")
 deviceManufacturer | dowolna wartość ciągu | (deviceProdukt -eq "Samsung")
 deviceModel | dowolna wartość ciągu | (device.deviceModel -eq "iPad Air")
 deviceOwnership (właściciel urządzenia) | Personal, Firma, Nieznany | (device.deviceOwnership -eq "Firma")
 nazwa profilu rejestracji | Profil rejestracji urządzeń Apple, rejestracja urządzenia — identyfikatory urządzeń firmowych (Android — kiosk) lub nazwa profilu programu Windows Autopilot | (device.enrollmentProfileName -eq "DEP iPhone")
 isRooted (IsRooted) | prawdziwe fałdy | (device.isRooted -eq true)
 managementType | MDM (dla urządzeń mobilnych)<br>PC (dla komputerów zarządzanych przez agenta komputera usługi Intune) | (device.managementType -eq "MDM")
 deviceId | prawidłowy identyfikator urządzenia usługi Azure AD | (device.deviceId -eq "d4fe7726-5966-431c-b3b8-cddc8fdb717d")
 Objectid | prawidłowy identyfikator obiektu usługi Azure AD |  (device.objectId -eq "76ad43c9-32c5-45e8-a272-7b58b58f596d")
 devicePhysicalIds (IdentyfikatoryphysicalIds) | dowolną wartość ciągu używaną przez autopilota, taką jak wszystkie urządzenia Autopilot, OrderID lub PurchaseOrderID  | (device.devicePhysicalIDs -any _ -contains "[ZTDId]") (device.devicePhysicalIds -any _ -eq "[OrderID]:179887111881") (device.devicePhysicalIds -any _ -eq "[PurchaseOrderId]:76222342342")
 systemLabels (SystemLabels) | dowolny ciąg pasujący do właściwości urządzenia usługi Intune do oznaczania urządzeń Modern Workplace | (device.systemLabels -zawiera "M365Managed")

> [!Note]  
> Dla deviceOwnership podczas tworzenia grup dynamicznych dla urządzeń należy ustawić wartość równą "Firma". W usłudze Intune własność urządzenia jest reprezentowana jako firma. Aby uzyskać więcej informacji, zapoznaj się z [ownertypes.](https://docs.microsoft.com/intune/reports-ref-devices#ownertypes) 

## <a name="next-steps"></a>Następne kroki

Te artykuły zawierają dodatkowe informacje na temat grup w usłudze Azure Active Directory.

- [Wyświetlanie istniejących grup](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Tworzenie nowej grupy i dodawanie członków](../fundamentals/active-directory-groups-create-azure-portal.md)
- [Zarządzanie ustawieniami grupy](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Zarządzanie członkostwem w grupie](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Zarządzanie regułami dynamicznymi dla użytkowników w grupie](groups-create-rule.md)
