---
title: Reguły członkostwa opartych na atrybutach grupę dynamiczną w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Tworzenie zaawansowanych reguł członkostwa w grupie dynamicznej, w tym obsługiwane wyrażenie operatorami reguł i parametry.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 07/24/2018
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: e49da237584a48c01e72552abae01da2514da3c1
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/25/2018
ms.locfileid: "39248893"
---
# <a name="create-dynamic-groups-with-attribute-based-membership-in-azure-active-directory"></a>Tworzenie grupy dynamiczne oparte na atrybutach uzyskanie członkostwa na poziomie w usłudze Azure Active Directory

W usłudze Azure Active Directory (Azure AD) można utworzyć złożone zasady oparte na atrybutach, aby włączyć dynamiczne zarządzanie członkostwem w grupach. Ten artykuł szczegółowo opisuje atrybuty i składnię tworzenia reguły członkostwa dynamicznego dla użytkowników lub urządzeń. Możesz skonfigurować reguły dynamicznego zarządzania członkostwem w grupach zabezpieczeń lub w grupach usługi Office 365.

Po zmianie dowolnych atrybutów użytkownika lub urządzenia, system ocenia wszystkie reguły dynamicznego w grupach w katalogu, aby sprawdzić, czy zmiany będą wyzwalać żadnej grupy dodaje lub usuwa. Jeśli użytkownik lub urządzenie spełnia wymagania zasad grupy, dodawane jest członkiem tej grupy. Jeśli już nie spełniają zasady, są usuwane.

> [!NOTE]
> Ta funkcja wymaga licencji usługi Azure AD Premium P1 dla każdy unikatowy użytkownik, który jest członkiem jednej lub kilku grup dynamicznych. Nie trzeba przypisać licencje do użytkowników dla nich, aby były członkami grup dynamicznych, ale musi mieć minimalną liczbę licencji w ramach dzierżawy, aby objęły one wszystkich takich użytkowników. Na przykład jeśli masz łącznie 1000 unikatowych użytkowników we wszystkich grupach dynamicznych w Twojej dzierżawie, będziesz potrzebować co najmniej 1000 licencji dla usługi Azure AD Premium P1 spełnić wymagania licencyjne.
>
> Można utworzyć grupę dynamiczną w przypadku urządzeń lub użytkowników, ale nie można utworzyć regułę, która zawiera użytkowników i urządzeń.
> 
> W tej chwili nie jest możliwe, Utwórz grupę urządzeń, na podstawie atrybutów użytkownika będącego właścicielem. Reguły członkostwa urządzenie może odwoływać się tylko bezpośredniego atrybutów obiektów urządzeń w katalogu.

## <a name="to-create-an-advanced-rule"></a>Aby utworzyć zaawansowaną regułę

1. Zaloguj się do [Centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com) przy użyciu konta administratora globalnego lub administratora konta użytkownika.
2. Wybierz pozycję **Użytkownicy i grupy**.
3. Wybierz **wszystkich grup**i wybierz **nową grupę**.

   ![Dodaj nową grupę](./media/groups-dynamic-membership/new-group-creation.png)

4. Na **grupy** bloku, wprowadź nazwę i opis dla nowej grupy. Wybierz **Typ członkostwa** albo **użytkownik dynamiczny** lub **urządzenie dynamiczne**, w zależności od tego, czy chcesz utworzyć regułę dla użytkowników lub urządzeń, a następnie wybierz **Dodaj zapytanie dynamiczne**. Można użyć konstruktora reguły do tworzenia prostej reguły lub samodzielnie zapisujesz zaawansowanej reguły. Ten artykuł zawiera więcej informacji na temat dostępnych atrybutów użytkowników i urządzeń, a także przykłady reguł zaawansowanych.

   ![Dodaj dynamiczną regułę członkostwa](./media/groups-dynamic-membership/add-dynamic-group-rule.png)

5. Po utworzeniu reguły, wybierz **Dodaj zapytanie** w dolnej części bloku.
6. Wybierz **Utwórz** na **grupy** bloku, aby utworzyć grupę.

> [!TIP]
> Tworzenie grupy kończy się niepowodzeniem, jeśli reguła wprowadzony został niepoprawnie sformułowany lub nieprawidłowy. W prawym górnym rogu portalu, zawierające wyjaśnienie, dlaczego nie można przetworzyć reguły zostanie wyświetlone powiadomienie. Przeczytaj uważnie, aby zrozumieć, jak musisz dostosować reguły, aby stał się nieprawidłowy.

## <a name="status-of-the-dynamic-rule"></a>Stan reguły dynamicznego

Możesz zobaczyć członkostwa, takich jak przetwarzanie stanu i Data ostatniej aktualizacji na stronie Przegląd dla grupy dynamicznej.
  
  ![Wyświetlanie stanu grupy dynamicznej](./media/groups-dynamic-membership/group-status.png)


Następujące komunikaty o stanie mogą być wyświetlane dla **przetwarzania członkostwa** stanu:

* **Ocena**: Zmiana grupy zostało odebrane i aktualizacji są oceniane.
* **Przetwarzanie**: aktualizacje są przetwarzane.
* **Ukończono aktualizację**: przetwarzanie zostało ukończone i wszystkie odpowiednie aktualizacje zostały wprowadzone.
* **Błąd przetwarzania**: Napotkano błąd podczas obliczania reguły członkostwa i nie można ukończyć przetwarzanie.
* **Aktualizowanie wstrzymane**: dynamiczną regułę członkostwa aktualizacji została wstrzymana przez administratora. MembershipRuleProcessingState jest ustawiona na "Wstrzymana".

Następujące komunikaty o stanie mogą być wyświetlane dla **członkostwa Ostatnia aktualizacja:** stanu:

* &lt;**Data i godzina**&gt;: czas ostatniego członkostwo zostało zaktualizowane.
* **Trwającą**: aktualizacje są obecnie w toku.
* **Nieznany**: nie można pobrać czas ostatniej aktualizacji. Może to być spowodowane nowo tworzonej grupy.

Jeśli wystąpi błąd podczas przetwarzania reguły członkostwa dla określonej grupy, alert jest wyświetlany w górnej części **strony Przegląd** dla grupy. Jeśli nie oczekujące członkostwo dynamiczne aktualizacje mogą być przetwarzane dla wszystkich grup w ramach dzierżawy więcej następnie 24 godziny, alert jest wyświetlany w górnej części **wszystkich grup**.

![komunikat o błędzie przetwarzania](./media/groups-dynamic-membership/processing-error.png)

## <a name="constructing-the-body-of-an-advanced-rule"></a>Konstruowanie treść zaawansowanej reguły

Zaawansowanej reguły, które można tworzyć na dynamiczne zarządzanie członkostwem w grupach jest zasadniczo wyrażenia binarnego, składa się z trzech części, która powstaje w wyniku wartość PRAWDA lub FAŁSZ. Dostępne są następujące trzy części:

* Lewy parametr
* Operator binarny
* Prawy — stała

Pełne zaawansowaną regułę wygląda podobnie do następującej: (leftParameter binaryOperator "RightConstant"), gdzie otwierającym i zamykającym są opcjonalne dla całego wyrażenia binarnego, podwójne cudzysłowy są opcjonalne, tylko wymagane dla prawej — stała gdy jest ciąg, a składnia parametru po lewej stronie jest user.property. Zaawansowanej reguły może zawierać więcej niż jednego wyrażenia binarne rozdzielone i, -, lub i — operatory logiczne nie.

Poniżej przedstawiono przykłady prawidłowo zaawansowanej reguły:
```
(user.department -eq "Sales") -or (user.department -eq "Marketing")
(user.department -eq "Sales") -and -not (user.jobTitle -contains "SDE")
```
Aby uzyskać pełną listę obsługiwanych parametrów operatorami reguł wyrażenia zobacz poniższe sekcje. Dla atrybutów używany dla reguły urządzeń, zobacz [Tworzenie reguły dla obiektów urządzeń przy użyciu atrybutów](#using-attributes-to-create-rules-for-device-objects).

Całkowita długość treść zaawansowanej reguły nie może przekraczać 2048 znaków.

> [!NOTE]
> Operacje na ciąg i wyrażeń regularnych są z uwzględnieniem wielkości liter. Można również wykonać sprawdzanie wartości Null przy użyciu *null* jako stała, na przykład user.department - eq *null*.
> Ciągi zawierające oferty "należy otaczać" znaków, na przykład user.department - eq \`"Sprzedaż".

## <a name="supported-expression-rule-operators"></a>Obsługiwane wyrażenie operatorami reguł

W poniższej tabeli wymieniono wszystkie operatory obsługiwane wyrażenie reguły i ich składni, która ma być używany w treści zaawansowanej reguły:

| Operator | Składnia |
| --- | --- |
| Nie równa się |-ne |
| Równa się |-eq |
| Nie zaczyna się od |-notStartsWith |
| Rozpoczyna się od |startsWith — |
| Nie zawiera |-notContains |
| Contains |-zawiera |
| Nie jest zgodne |-notMatch |
| Dopasowanie |-dopasowania |
| W | -w |
| Nie w | -notIn |

## <a name="operator-precedence"></a>Pierwszeństwo operatorów

Poniżej przedstawiono wszystkie operatory na pierwszeństwo od niższych do wyższych. Operatory w tym samym wierszu znajdują się w taki sam priorytet:

````
-any -all
-or
-and
-not
-eq -ne -startsWith -notStartsWith -contains -notContains -match –notMatch -in -notIn
````

Wszystkie operatory może służyć z lub bez prefiksu łącznik. Nawiasy są wymagane tylko wtedy, gdy pierwszeństwo nie spełnia wymagań.
Na przykład:

```
   user.department –eq "Marketing" –and user.country –eq "US"
```

jest równoważne:

```
   (user.department –eq "Marketing") –and (user.country –eq "US")
```

## <a name="using-the--in-and--notin-operators"></a>Przy użyciu w i notIn — operatory

Jeśli chcesz porównać wartości atrybutu użytkownika względem szereg różnych wartości możesz użyć w - notIn operatory lub. Oto przykład przy użyciu w operatorze:
```
   user.department -In ["50001","50002","50003",“50005”,“50006”,“50007”,“50008”,“50016”,“50020”,“50024”,“50038”,“50039”,“51100”]
```
Zwróć uwagę na użycie "[" i "]" na początku i na końcu listy wartości. Ten warunek ma wartość True, wartość jest równa user.department jedną z wartości na liście.


## <a name="query-error-remediation"></a>Korygowanie błąd zapytania

W poniższej tabeli wymieniono typowe błędy i ich rozwiązania

| Błąd analizowania zapytania | Błąd użycia | Poprawiony użycia |
| --- | --- | --- |
| Błąd: Atrybutu nie jest obsługiwane. |(user.invalidProperty - eq "Value") |(user.department - eq "value")<br/><br/>Upewnij się, że atrybut jest [obsługiwane listy właściwości](#supported-properties). |
| Błąd: Operator nie jest obsługiwany dla atrybutu. |(user.accountEnabled — zawiera wartość true) |(user.accountEnabled - eq true)<br/><br/>Operator używany nie jest obsługiwana dla typu właściwości (w tym przykładzie-zawiera nie można użyć typu boolean). Prawidłowe operatory na użytek typ właściwości. |
| Błąd: Błąd kompilacji zapytania. |1. (user.department - eq "Sprzedaż") (user.department - eq "Marketing")<br/><br/>2. (user.userPrincipalName-zgodny "*@domain.ext") |1. Brak operatora. Użyj - i - lub dołączyć dwa predykatów<br/><br/>(user.department - eq "Sprzedaż")- lub (user.department - eq "Marketing")<br/><br/>2. Błąd w wyrażeniu regularnym, w ramach - dopasowania<br/><br/>(user.userPrincipalName-zgodny ". *@domain.ext"), można również: (user.userPrincipalName-zgodny "\@domain.ext$")|

## <a name="supported-properties"></a>Obsługiwanych właściwości

Poniżej przedstawiono wszystkich właściwości użytkownika, które można używać w zaawansowanej reguły:

### <a name="properties-of-type-boolean"></a>Właściwości typu boolean

Dozwolonych operatorów

* -eq
* -ne

| Właściwości | Dozwolone wartości | Sposób użycia |
| --- | --- | --- |
| accountEnabled |wartość true, false |true - eq user.accountEnabled |
| dirSyncEnabled |wartość true, false |true - eq user.dirSyncEnabled |

### <a name="properties-of-type-string"></a>Właściwości typu ciąg

Dozwolonych operatorów

* -eq
* -ne
* -notStartsWith
* StartsWith —
* -zawiera
* -notContains
* -dopasowania
* -notMatch
* -w
* -notIn

| Właściwości | Dozwolone wartości | Sposób użycia |
| --- | --- | --- |
| city |Dowolną wartość ciągu lub *o wartości null* |(user.city - eq "value") |
| Kraj |Dowolną wartość ciągu lub *o wartości null* |(user.country - eq "value") |
| companyName | Dowolną wartość ciągu lub *o wartości null* | (user.companyName - eq "value") |
| Dział |Dowolną wartość ciągu lub *o wartości null* |(user.department - eq "value") |
| displayName |Dowolną wartość ciągu |(user.displayName - eq "value") |
| employeeId |Dowolną wartość ciągu |(user.employeeId - eq "value")<br>(user.employeeId - ne *null*) |
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
| Element usageLocation |Numer kierunkowy kraju własną literą dwa |(user.usageLocation - eq "PL") |
| userPrincipalName |Dowolną wartość ciągu |(user.userPrincipalName - eq "alias@domain") |
| UserType |element członkowski gościa *o wartości null* |(user.userType - eq "Członek") |

### <a name="properties-of-type-string-collection"></a>Właściwości typu kolekcji ciągów

Dozwolonych operatorów

* -zawiera
* -notContains

| Właściwości | Dozwolone wartości | Sposób użycia |
| --- | --- | --- |
| otherMails |Dowolną wartość ciągu |(user.otherMails — zawiera "alias@domain") |
| proxyAddresses |SMTP: alias@domain smtp: alias@domain |(user.proxyAddresses — zawiera "SMTP: alias@domain") |

## <a name="multi-value-properties"></a>Właściwości wielu wartości

Dozwolonych operatorów

* -dowolny (spełnione, gdy co najmniej jeden element w kolekcji dopasowuje warunek)
* — wszystkie (spełnione, gdy wszystkie elementy w kolekcji zgodne z warunkiem)

| Właściwości | Wartości | Sposób użycia |
| --- | --- | --- |
| assignedPlans |Każdy obiekt w kolekcji udostępnia następujące właściwości ciągu: capabilityStatus, usługi, servicePlanId |user.assignedPlans — wszystkie (assignedPlan.servicePlanId - eq "efb87545-963c-4e0d-99df-69c6916d9eb0"- a assignedPlan.capabilityStatus - eq "Enabled") |
| proxyAddresses| SMTP: alias@domain smtp: alias@domain | (user.proxyAddresses — wszystkie (\_ — zawiera "contoso")) |

Właściwości wielu wartości są kolekcjami obiektów tego samego typu. Możesz użyć - wszelkie - wszystkie operatory i do zastosowania warunku do jednego lub wszystkich elementów w kolekcji, odpowiednio. Na przykład:

assignedPlans jest właściwością wielu wartości, która zawiera listę wszystkich planach usługi przypisane do użytkownika. Poniższe wyrażenie wybierze użytkownicy, którzy posiadają plan usługi Exchange Online (Plan 2) jest w stanie włączone:

```
user.assignedPlans -any (assignedPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -and assignedPlan.capabilityStatus -eq "Enabled")
```

(Identyfikator GUID identyfikuje planu usługi Exchange Online (Plan 2)).

> [!NOTE]
> Jest to przydatne, jeśli chcesz zidentyfikować wszyscy użytkownicy, dla którego usługi Office 365 (lub innych usług Microsoft Online Services) funkcja została włączona, na przykład w celu określania elementów docelowych widoków przy użyciu zestawu zasad.

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

## <a name="use-of-null-values"></a>Użyj wartości Null

Aby określić wartość null w regule, można użyć *null* wartość. Należy zachować ostrożność nie należy używać cudzysłowów wokół wyraz *null* — Jeśli to zrobisz, będzie interpretowany jako wartość literału ciągu. Nie nie można używać operatora jako operatora porównawczych dla wartości null. Jeśli jest ona używana, wystąpi błąd, czy używać wartości null lub $null. Zamiast tego należy użyć - eq lub - ne. Poprawny sposób odwoływać się do wartości null, jest następujący:
```
   user.mail –ne $null
```

## <a name="extension-attributes-and-custom-attributes"></a>Rozszerzeń atrybuty oraz atrybuty niestandardowe
Rozszerzeń atrybuty oraz atrybuty niestandardowe są obsługiwane w regułach członkostwa dynamicznego.

Atrybuty rozszerzenia są synchronizowane z lokalną Windows Server AD i mieć format "ExtensionAttributeX", gdzie X jest równa 1 – 15.
Oto przykład regułę, która używa atrybutu rozszerzenia

```
(user.extensionAttribute15 -eq "Marketing")
```

Atrybutów niestandardowych, które są synchronizowane z lokalnej usługi Windows Server AD lub połączonych aplikacji SaaS i format "user.extension_[GUID]\__ [Attribute]", gdzie [identyfikator GUID] to unikatowy identyfikator aplikacji, która utworzyła w usłudze AAD atrybut w usłudze Azure AD i [Attribute] jest nazwa atrybutu, ponieważ został on utworzony. Na przykład regułę, która używa atrybutu niestandardowego

```
user.extension_c272a57b722d4eb29bfe327874ae79cb__OfficeNumber  
```

Nazwa atrybutu niestandardowego można znaleźć w katalogu, wysyłając zapytanie do atrybutu za pomocą Eksploratora programu Graph i wyszukując nazwę atrybutu.

## <a name="direct-reports-rule"></a>Reguła "Bezpośrednich podwładnych"
Można utworzyć grupy obejmującej wszystkich bezpośrednich podwładnych menedżera. Jeśli Menedżer bezpośrednich podwładnych zmienia się w przyszłości, członkostwo w grupie zostaną dostosowane automatycznie.

> [!NOTE]
> 1. Reguła działa, upewnij się, **identyfikator menedżera** poprawnie ustawić właściwości w użytkowników w dzierżawie. Bieżąca wartość dla użytkownika można sprawdzić ich **karta Profil**.
> 2. Ta reguła obsługuje tylko **bezpośredniego** raportów. Obecnie nie jest możliwe utworzyć grupę dla zagnieżdżonej hierarchii; na przykład grupa, która obejmuje bezpośrednich podwładnych oraz ich raporty.
> 3. Ta zasada nie można łączyć z innymi regułami zaawansowanymi.

**Aby skonfigurować grupę**

1. Wykonaj kroki od 1 do 5 z sekcji [do tworzenia zaawansowanej reguły](#to-create-the-advanced-rule)i wybierz **Typ członkostwa** z **użytkownik dynamiczny**.
2. Na **reguły członkostwa dynamicznego** bloku wprowadź reguła o następującej składni:

    *Raporty bezpośrednie dla "{objectID_of_manager}"*

    Przykład prawidłowy reguły:
```
                    Direct Reports for "62e19b97-8b3d-4d4a-a106-4ce66896a863"
```
    where “62e19b97-8b3d-4d4a-a106-4ce66896a863” is the objectID of the manager. The object ID can be found on manager's **Profile tab**.
3. Po zapisaniu reguły, wszyscy użytkownicy z określoną wartością Identyfikatora Menedżera zostaną dodane do grupy.

## <a name="using-attributes-to-create-rules-for-device-objects"></a>Tworzenie reguły dla obiektów urządzeń przy użyciu atrybutów
Można również utworzyć regułę, która wybiera obiekty urządzeń do członkostwa w grupie. Następujące atrybuty urządzenia może służyć.

 Atrybutu urządzenia  | Wartości | Przykład
 ----- | ----- | ----------------
 accountEnabled | wartość true, false | (device.accountEnabled - eq true)
 displayName | dowolną wartość ciągu |(device.displayName - eq "Rob Iphone")
 deviceOSType | dowolną wartość ciągu | (device.deviceOSType - eq "iPad")- lub (device.deviceOSType - eq "iPhone")
 deviceOSVersion | dowolną wartość ciągu | (urządzenie. OSVersion - eq "9.1")
 deviceCategory | Nazwa kategorii prawidłowe urządzenie | (device.deviceCategory - eq "BYOD")
 deviceManufacturer | dowolną wartość ciągu | (device.deviceManufacturer - eq "Samsung")
 deviceModel | dowolną wartość ciągu | (device.deviceModel - eq "iPad Air")
 deviceOwnership | Osobiste, firma, nieznany | (device.deviceOwnership - eq "Firma")
 Nazwa_domeny | dowolną wartość ciągu | (device.domainName - eq "contoso.com")
 enrollmentProfileName | Nazwa profilu profilu rejestracji urządzeń firmy Apple lub rozwiązania Windows Autopilot | (device.enrollmentProfileName - eq "IPhone DEP")
 isRooted | wartość true, false | (device.isRooted - eq true)
 managementType | Zarządzanie urządzeniami Przenośnymi (dla urządzeń przenośnych)<br>Komputer (w przypadku komputerów zarządzanych przez agenta PC usługi Intune) | (device.managementType - eq "MDM")
 Jednostka organizacyjna | dowolną wartość ciągu pasujące do nazwy jednostki organizacyjnej, ustawiane przez usługi Active Directory w środowisku lokalnym | (device.organizationalUnit -eq "US PCs")
 deviceId | Nieprawidłowy identyfikator urządzenia usługi Azure AD | (device.deviceId - eq "d4fe7726-5966-431c-b3b8-cddc8fdb717d")
 Identyfikator obiektu | Identyfikator obiektu prawidłowy, usługa Azure AD |  (device.objectId -eq 76ad43c9-32c5-45e8-a272-7b58b58f596d")



## <a name="changing-dynamic-membership-to-static-and-vice-versa"></a>Zmiana członkostwa dynamicznego na statyczne i na odwrót
Istnieje możliwość zmienić sposób zarządzania członkostwa w grupie. Jest to przydatne, gdy chcesz zachować taką samą nazwę grupy i identyfikator w systemie, dzięki czemu wszystkie istniejące odwołania do tej grupy są nadal ważne. Tworzenie nowej grupy wymaga aktualizacji te odwołania.

Zaktualizowaliśmy Centrum administratora usługi Azure AD, aby dodać obsługę tej funkcji. Teraz klienci mogą przekształcać istniejące grupy dynamiczne członkostwo przypisane członkostwo i na odwrót za pośrednictwem Centrum administracyjnego usługi Azure AD lub poleceń cmdlet programu PowerShell, jak pokazano poniżej.

> [!WARNING]
> Po zmianie istniejącej statycznych grupy do grupy dynamicznej, wszystkie istniejące członkowie zostaną usunięci z grupy, a następnie reguły członkostwa będą przetwarzane do dodawania nowych elementów członkowskich. Jeśli grupa jest używana do kontrolowania dostępu do aplikacji lub zasobów, oryginalnym elementy Członkowskie mogą stracić dostęp do momentu reguły członkostwa jest w pełni przetwarzany.
>
> Firma Microsoft zaleca test nową regułę członkostwa wcześniej, aby upewnić się, że nowego członkostwa w grupie zgodnie z oczekiwaniami.

### <a name="using-azure-ad-admin-center-to-change-membership-management-on-a-group"></a>Aby zmienić zarządzanie członkostwem w grupie przy użyciu Centrum administracyjnego usługi Azure AD 

1. Zaloguj się do [Centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com) przy użyciu konta administratora globalnego lub administratora konta użytkowników w dzierżawie.
2. Wybierz **grup**.
3. Z **wszystkich grup** listy, otwórz grupę, którą chcesz zmienić.
4. Wybierz **właściwości**.
5. Na **właściwości** stronie dla grupy, wybierz opcję **Typ członkostwa** przypisane (statyczny), dynamiczne użytkownika lub urządzenie dynamiczne, w zależności od typu żądanego członkostwa. Dynamicznego zarządzania członkostwem można użyć konstruktora reguły wybierz opcje dla prostej reguły lub samodzielnie zapisujesz zaawansowanej reguły. 

Poniższe kroki są przykładem Zmiana grupy ze statycznego na dynamiczne zarządzanie członkostwem w grupie użytkowników. 

1. Na **właściwości** strony dla wybranej grupy, wybierz opcję **Typ członkostwa** z **użytkownik dynamiczny**, następnie wybierz pozycję Tak, w oknie dialogowym wyjaśniających zmiany do grupy członkostwo, aby kontynuować. 
  
   ![Wybierz typ członkostwa dynamicznego użytkownika](./media/groups-dynamic-membership/select-group-to-convert.png)
  
2. Wybierz **Dodaj zapytanie dynamiczne**, a następnie podaj reguły.
  
   ![Wprowadź reguły](./media/groups-dynamic-membership/enter-rule.png)
  
3. Po utworzeniu reguły, wybierz **Dodaj zapytanie** w dolnej części strony.
4. Wybierz **Zapisz** na **właściwości** stronie dla grupy zapisać zmiany. **Typ członkostwa** grupy natychmiast zaktualizować na liście grup.

> [!TIP]
> Konwersja grup może zakończyć się niepowodzeniem, jeśli zaawansowaną regułę, wprowadzony była nieprawidłowa. Zostanie wyświetlone powiadomienie w prawym górnym rogu portalu który zawiera wyjaśnienie, dlaczego nie można zaakceptować zasady przez system. Przeczytaj uważnie, aby zrozumieć, jak można dostosować reguły, aby stał się nieprawidłowy.

### <a name="using-powershell-to-change-membership-management-on-a-group"></a>Aby zmienić zarządzanie członkostwem w grupie przy użyciu programu PowerShell

> [!NOTE]
> Aby zmienić właściwości grupy dynamicznej, musisz użyć polecenia cmdlet z **wersję zapoznawczą** [usługi Azure AD PowerShell w wersji 2](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0). Możesz zainstalować wersję zapoznawczą z [galerii programu PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).

Oto przykład funkcji, które Przełącz zarządzanie członkostwem w istniejącej grupy. W tym przykładzie jest uważać, aby poprawnie manipulowania właściwość GroupTypes przy zachowaniu dowolnych wartości, które są powiązane z członkostwa dynamicznego.

```
#The moniker for dynamic groups as used in the GroupTypes property of a group object
$dynamicGroupTypeString = "DynamicMembership"

function ConvertDynamicGroupToStatic
{
    Param([string]$groupId)

    #existing group types
    [System.Collections.ArrayList]$groupTypes = (Get-AzureAdMsGroup -Id $groupId).GroupTypes

    if($groupTypes -eq $null -or !$groupTypes.Contains($dynamicGroupTypeString))
    {
        throw "This group is already a static group. Aborting conversion.";
    }


    #remove the type for dynamic groups, but keep the other type values
    $groupTypes.Remove($dynamicGroupTypeString)

    #modify the group properties to make it a static group: i) change GroupTypes to remove the dynamic type, ii) pause execution of the current rule
    Set-AzureAdMsGroup -Id $groupId -GroupTypes $groupTypes.ToArray() -MembershipRuleProcessingState "Paused"
}

function ConvertStaticGroupToDynamic
{
    Param([string]$groupId, [string]$dynamicMembershipRule)

    #existing group types
    [System.Collections.ArrayList]$groupTypes = (Get-AzureAdMsGroup -Id $groupId).GroupTypes

    if($groupTypes -ne $null -and $groupTypes.Contains($dynamicGroupTypeString))
    {
        throw "This group is already a dynamic group. Aborting conversion.";
    }
    #add the dynamic group type to existing types
    $groupTypes.Add($dynamicGroupTypeString)

    #modify the group properties to make it a static group: i) change GroupTypes to add the dynamic type, ii) start execution of the rule, iii) set the rule
    Set-AzureAdMsGroup -Id $groupId -GroupTypes $groupTypes.ToArray() -MembershipRuleProcessingState "On" -MembershipRule $dynamicMembershipRule
}
```
Aby umożliwić grupy statyczne:
```
ConvertDynamicGroupToStatic "a58913b2-eee4-44f9-beb2-e381c375058f"
```
Aby utworzyć grupę dynamiczną:
```
ConvertStaticGroupToDynamic "a58913b2-eee4-44f9-beb2-e381c375058f" "user.displayName -startsWith ""Peter"""
```
## <a name="next-steps"></a>Kolejne kroki
Te artykuły zawierają dodatkowe informacje na temat grup w usłudze Azure Active Directory.

* [Wyświetlanie istniejących grup](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Tworzenie nowej grupy i dodawanie członków](../fundamentals/active-directory-groups-create-azure-portal.md)
* [Zarządzanie ustawieniami grupy](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [Zarządzanie członkostwem w grupie](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [Zarządzanie regułami dynamicznymi dla użytkowników w grupie](groups-dynamic-membership.md)
