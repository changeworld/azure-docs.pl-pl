---
title: Na podstawie atrybutów dynamiczne członkostwo w grupie w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Tworzenie zaawansowanych reguł w tym członkostwa grupy dynamicznej obsługiwane wyrażenie operatorami reguł i parametry.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.assetid: fb434cc2-9a91-4ebf-9753-dd81e289787e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 03/30/2018
ms.author: curtand
ms.reviewer: piotrci
ms.custom: H1Hack27Feb2017;it-pro
ms.openlocfilehash: a4ed9ddabe19406fa694992f29cf529b491438c0
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="create-attribute-based-rules-for-dynamic-group-membership-in-azure-active-directory"></a>Utwórz zasady na podstawie atrybutów dynamiczne członkostwo w grupie w usłudze Azure Active Directory
W usłudze Azure Active Directory (Azure AD) można utworzyć reguł zaawansowanych, aby włączyć złożonych opartych na atrybutach dynamiczne zarządzanie członkostwem w grupach. W tym artykule szczegółowo atrybuty i składni, aby utworzyć reguły członkostwa dynamicznych dla użytkowników lub urządzeń. Możesz skonfigurować reguły dynamicznego zarządzania członkostwem w grupach zabezpieczeń lub w grupach usługi Office 365.

Po zmianie wszystkie atrybuty użytkowników lub urządzeń, system ocenia wszystkie reguły dynamicznej grupy w katalogu, aby sprawdzić, czy spowoduje zmianę żadnej grupy dodaje lub usuwa. Jeśli użytkownik lub urządzenie spełnia zasady grupy, zostaną dodane jako członkiem tej grupy. Jeśli nie spełniają reguły, zostaną usunięte.

> [!NOTE]
> Ta funkcja wymaga licencji usługi Azure AD Premium P1 dla każdego użytkownika został dodany do co najmniej jednej grupy dynamicznej. Nie jest to konieczne, aby rzeczywiście przypisać licencje do użytkowników dla nich jako członków grup dynamicznych, ale musisz mieć minimalną liczbę licencji w dzierżawie dotyczyć wszystkich takich użytkowników. Na przykład: Jeśli masz łączną liczbę unikatowych użytkowników 1000 we wszystkich grupach dynamicznych w dzierżawie, musisz mieć co najmniej 1000 licencji dla programu Azure AD Premium P1 lub nowszej, do wersji spełniającej wymagania licencyjne.
>
> Można utworzyć grupy dynamicznej dla urządzeń lub użytkowników, ale nie można utworzyć regułę, która zawiera zarówno obiektów użytkowników i urządzeń.
> 
> W tej chwili nie jest możliwe tworzenie grupy urządzeń, na podstawie atrybutów użytkownika będący właścicielem. Reguły członkostwa urządzenie może odwoływać się tylko natychmiastowe atrybutów obiektów urządzeń w katalogu.

## <a name="to-create-an-advanced-rule"></a>Aby utworzyć regułę zaawansowane
1. Zaloguj się do [Centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com) przy użyciu konta, które jest administratorem globalnym lub administratorem konta użytkownika.
2. Wybierz **użytkowników i grup**.
3. Wybierz **wszystkich grup**i wybierz **nową grupę**.

   ![Dodaj nową grupę](./media/active-directory-groups-dynamic-membership-azure-portal/new-group-creation.png)

4. Na **grupy** bloku, wprowadź nazwę i opis dla nowej grupy. Wybierz **Typ członkostwa** albo **dynamiki** lub **dynamiczne urządzenia**, w zależności od tego, czy chcesz utworzyć regułę dla użytkowników lub urządzeń, a następnie wybierz **Zapytania dynamiczne dodawanie**. Prosta reguła kompilacji za pomocą konstruktora reguły lub zapisać reguły zaawansowanej samodzielnie. Ten artykuł zawiera więcej informacji na temat dostępnych atrybutów użytkowników i urządzeń, a także przykłady reguł zaawansowanych.

   ![Dodaj dynamiczną regułę członkostwa](./media/active-directory-groups-dynamic-membership-azure-portal/add-dynamic-group-rule.png)

5. Po utworzeniu reguły, wybierz **kwerendy dodaj** w dolnej części bloku.
6. Wybierz **Utwórz** na **grupy** bloku, aby utworzyć grupę.

> [!TIP]
> Tworzenie grupy może zakończyć się niepowodzeniem, jeśli reguły zaawansowanej, wprowadzony jest niepoprawne. Powiadomienie będzie wyświetlane w prawym górnym rogu portalu; Zawiera wyjaśnienie, dlaczego reguły nie może zostać zaakceptowane przez system. Przeczytaj uważnie, aby zrozumieć, jak należy skorygować reguły, aby była nieprawidłowa.

## <a name="constructing-the-body-of-an-advanced-rule"></a>Konstruowanie treści zaawansowanej reguły
Zaawansowane zasady, która można tworzyć dla dynamiczne zarządzanie członkostwem w grupach jest zasadniczo wyrażenie binarne, która składa się z trzech części i otrzymywania wyników w wyniku wartość true lub false. Dostępne są następujące trzy części:

* Lewy parametr
* Operator binarny.
* Stała prawo

Zakończenie reguły zaawansowanej wygląda podobnie do następującej: (leftParameter binaryOperator "RightConstant"), gdzie otwierające i zamykające są opcjonalne dla całego wyrażenia binarne, podwójne cudzysłowy są opcjonalne również tylko wymagane przez prawo stała gdy jest ciąg i user.property jest składnia lewy parametr. Reguły zaawansowanej może zawierać więcej niż jednego wyrażenia binarne oddzielone i, -, lub i - operatorów logicznych nie.

Poniżej przedstawiono przykłady prawidłowo skonstruowanej reguł zaawansowanych:
```
(user.department -eq "Sales") -or (user.department -eq "Marketing")
(user.department -eq "Sales") -and -not (user.jobTitle -contains "SDE")
```
Pełna lista obsługiwanych parametrów i operatorami reguł wyrażenia znajduje się w sekcjach poniżej. Dla atrybutów używana dla reguł urządzenia, zobacz [do tworzenia reguł obiekty urządzeń przy użyciu atrybutów](#using-attributes-to-create-rules-for-device-objects).

Całkowita długość treści zaawansowane reguły nie może przekraczać 2048 znaków.

> [!NOTE]
> Operacje ciągu i wyrażeń regularnych nie jest uwzględniana. Można także przeprowadzić sprawdzenia wartości Null przy użyciu *null* jako stała, na przykład user.department - eq *null*.
> Ciągi zawierające oferty "powinny być zmienione znaczenie przy użyciu ' znaków, na przykład user.department - eq \`"Sprzedaż".

## <a name="supported-expression-rule-operators"></a>Obsługiwane wyrażenie operatorami reguł
W poniższej tabeli wymieniono wszystkie operatory obsługiwane wyrażenie reguły i ich składni, które mają być użyte w treści zaawansowanych reguł:

| Operator | Składnia |
| --- | --- |
| Nie równa się |-ne |
| Równa się |-eq |
| Nie rozpoczyna się od |-notStartsWith |
| Rozpoczyna się od |-startsWith |
| Nie zawiera |-notContains |
| Contains |-zawiera |
| Nie pasują do siebie |-notMatch |
| dopasowanie |-zgodne |
| W | -in |
| Nie w | -notIn |

## <a name="operator-precedence"></a>Kolejność wykonywania działań

Poniżej przedstawiono wszystkie operatory na pierwszeństwo z niższym wyższy. Operatory w tym samym wierszu znajdują się w taki sam priorytet:
````
-any -all
-or
-and
-not
-eq -ne -startsWith -notStartsWith -contains -notContains -match –notMatch -in -notIn
````
Wszystkich operatorów można używać z lub bez prefiksu łącznik. Nawiasy są wymagane tylko wtedy, gdy priorytet nie spełnia wymagań.
Na przykład:
```
   user.department –eq "Marketing" –and user.country –eq "US"
```
odpowiada to:
```
   (user.department –eq "Marketing") –and (user.country –eq "US")
```
## <a name="using-the--in-and--notin-operators"></a>Przy użyciu w i notIn — operatory

Jeśli chcesz porównać wartości atrybutu użytkownika przed szereg różnych wartości można użyć w lub operatory - notIn. Oto przykład przy użyciu w operatorze:
```
    user.department -In [ "50001", "50002", "50003", “50005”, “50006”, “50007”, “50008”, “50016”, “50020”, “50024”, “50038”, “50039”, “51100” ]
```
Zwróć uwagę na użycie "[" i "]" na początku i na końcu listy wartości. Ten warunek ma wartość True, wartość równa user.department jednej z wartości na liście.


## <a name="query-error-remediation"></a>Korygowanie błąd zapytania
Poniższa tabela zawiera listę typowych błędów i rozwiązania ich

| Błąd analizy zapytania | Błąd użycia | Poprawiony użycia |
| --- | --- | --- |
| Błąd: Atrybut nie jest obsługiwane. |(user.invalidProperty - eq "Value") |(user.department - eq "value")<br/><br/>Upewnij się, że atrybut jest [obsługiwane listy właściwości](#supported-properties). |
| Błąd: Operator nie jest obsługiwany w atrybucie. |(user.accountEnabled — zawiera wartość true) |(user.accountEnabled - eq true)<br/><br/>Operator używany jest nieobsługiwana dla typu właściwości (w tym przykładzie-zawiera nie można używać dla typu boolean). Operatory prawidłowa dla typu właściwości. |
| Błąd: Błąd kompilacji zapytania. |1. (user.department - eq "Sprzedaż") (user.department - eq "Marketing")<br/><br/>2. (user.userPrincipalName-odpowiada "*@domain.ext") |1. Brak operatora. Użyj - i - lub połącz dwa predykatów<br/><br/>(user.department - eq "Sprzedaż")- lub (user.department - eq "Marketing")<br/><br/>2. Błąd w wyrażeniu regularnym używane z - zgodne<br/><br/>(user.userPrincipalName-odpowiada ". *@domain.ext"), można również: (user.userPrincipalName-odpowiada "@domain.ext$")|

## <a name="supported-properties"></a>Obsługiwanych właściwości
Wszystkie właściwości użytkownika, które można używać w zaawansowanych reguły są następujące:

### <a name="properties-of-type-boolean"></a>Właściwości typu boolean
Dozwolonych operatorów

* -eq
* -ne

| Właściwości | Dozwolone wartości | Sposób użycia |
| --- | --- | --- |
| accountEnabled |wartość true, false |true - eq user.accountEnabled |
| dirSyncEnabled |wartość true, false |true - eq user.dirSyncEnabled |

### <a name="properties-of-type-string"></a>Właściwości typu String
Dozwolonych operatorów

* -eq
* -ne
* -notStartsWith
* -StartsWith
* -zawiera
* -notContains
* -zgodne
* -notMatch
* -in
* -notIn

| Właściwości | Dozwolone wartości | Sposób użycia |
| --- | --- | --- |
| city |Dowolną wartość ciągu lub *wartości null* |(user.city - eq "value") |
| Kraju |Dowolną wartość ciągu lub *wartości null* |(user.country - eq "value") |
| companyName | Dowolną wartość ciągu lub *wartości null* | (user.companyName - eq "value") |
| Dział |Dowolną wartość ciągu lub *wartości null* |(user.department - eq "value") |
| Nazwa wyświetlana |Dowolną wartością ciągu |(user.displayName - eq "value") |
| Identyfikator pracownika |Dowolną wartością ciągu |(user.employeeId - eq "value")<br>(user.employeeId - ne *null*) |
| facsimileTelephoneNumber |Dowolną wartość ciągu lub *wartości null* |(user.facsimileTelephoneNumber - eq "value") |
| givenName |Dowolną wartość ciągu lub *wartości null* |(user.givenName - eq "value") |
| jobTitle |Dowolną wartość ciągu lub *wartości null* |(user.jobTitle - eq "value") |
| Poczty |Dowolną wartość ciągu lub *null* (adresu SMTP użytkownika) |(user.mail - eq "value") |
| mailNickName |Dowolną wartość ciągu (poczty alias użytkownika) |(user.mailNickName - eq "value") |
| Telefon komórkowy |Dowolną wartość ciągu lub *wartości null* |(user.mobile - eq "value") |
| Identyfikator obiektu |Identyfikator GUID obiektu użytkownika |(user.objectId - eq "1111111-1111-1111-1111-111111111111") |
| onPremisesSecurityIdentifier | Lokalny identyfikator zabezpieczeń (SID) dla użytkowników, którzy zostały zsynchronizowane z lokalnymi do chmury. |(user.onPremisesSecurityIdentifier - eq "S-1-1-11-1111111111-1111111111-1111111111-1111111") |
| passwordPolicies |Brak DisableStrongPassword DisablePasswordExpiration DisablePasswordExpiration, DisableStrongPassword |(user.passwordPolicies - eq "DisableStrongPassword") |
| physicalDeliveryOfficeName |Dowolną wartość ciągu lub *wartości null* |(user.physicalDeliveryOfficeName - eq "value") |
| postalCode |Dowolną wartość ciągu lub *wartości null* |(user.postalCode - eq "value") |
| preferredLanguage |Kod ISO 639-1 |(user.preferredLanguage - eq "pl pl") |
| sipProxyAddress |Dowolną wartość ciągu lub *wartości null* |(user.sipProxyAddress - eq "value") |
| state |Dowolną wartość ciągu lub *wartości null* |(user.state - eq "value") |
| streetAddress |Dowolną wartość ciągu lub *wartości null* |(user.streetAddress - eq "value") |
| surname |Dowolną wartość ciągu lub *wartości null* |(user.surname - eq "value") |
| TelephoneNumber |Dowolną wartość ciągu lub *wartości null* |(user.telephoneNumber - eq "value") |
| usageLocation |Kod kraju własną literą dwóch |(user.usageLocation - eq "PL") |
| userPrincipalName |Dowolną wartością ciągu |(user.userPrincipalName - eq "alias@domain") |
| UserType |element członkowski gościa *wartości null* |(user.userType - eq "Elementu członkowskiego") |

### <a name="properties-of-type-string-collection"></a>Właściwości typu kolekcji ciągów
Dozwolonych operatorów

* -zawiera
* -notContains

| Właściwości | Dozwolone wartości | Sposób użycia |
| --- | --- | --- |
| otherMails |Dowolną wartością ciągu |(user.otherMails — zawiera "alias@domain") |
| proxyAddresses |SMTP: alias@domain smtp: alias@domain |(user.proxyAddresses — zawiera "SMTP: alias@domain") |

## <a name="multi-value-properties"></a>Właściwości wielu wartości
Dozwolonych operatorów

* -wszelkie (spełnione po co najmniej jeden element w kolekcji odpowiada warunek)
* -wszystkie (spełnione, kiedy wszystkie elementy w kolekcji zgodne z warunkiem)

| Właściwości | Wartości | Sposób użycia |
| --- | --- | --- |
| assignedPlans |Każdy obiekt w kolekcji udostępnia następujące właściwości ciągu: capabilityStatus, usługi, servicePlanId |user.assignedPlans — wszystkie (assignedPlan.servicePlanId - eq "efb87545-963c-4e0d-99df-69c6916d9eb0"- i assignedPlan.capabilityStatus - eq "Włączone") |

Właściwości wielowartościowe są kolekcje obiektów tego samego typu. Można użyć — wszystkie — wszystkie operatory i do zastosowania warunku, odpowiednio do jednego lub wszystkich elementów w kolekcji. Na przykład:

assignedPlans jest właściwością wielu wartościach, która wyświetla wszystkie plany usługi przypisane do użytkownika. Poniżej wyrażenie będzie Wybierz użytkowników, którzy mają usługi Exchange Online (Plan 2) planu obsługi, który jest również w stanie włączone:

```
user.assignedPlans -any (assignedPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -and assignedPlan.capabilityStatus -eq "Enabled")
```

(Identyfikator Guid identyfikuje planie usługi Exchange Online (Plan 2)).

> [!NOTE]
> Jest to przydatne, jeśli chcesz zidentyfikować wszystkich użytkowników, dla którego usługi Office 365 (lub innych usług Microsoft Online) funkcja została włączona, na przykład, do których zestaw zasad.

Poniższe wyrażenie będzie wybierz wszystkich użytkowników, którzy mają wszystkie planu usług, który jest skojarzony z usługą Intune (określanego przez nazwę usługi "SCO"):
```
user.assignedPlans -any (assignedPlan.service -eq "SCO" -and assignedPlan.capabilityStatus -eq "Enabled")
```

## <a name="use-of-null-values"></a>Użyj wartości Null

Aby określić wartości null w regule, można użyć *null* wartość. Nie można więc użyć cudzysłowów wokół wyraz *null* — Jeśli to zrobisz, zostanie potraktowany jako wartość literału ciągu. Nie nie można używać operatora jako operatora porównawczych dla wartości null. Jeśli używasz tego błąd pojawia się przy użyciu wartości null lub $null. W zamian użyj - eq lub - ne. Prawidłowy sposób odwoływać się do wartości null, jest następujący:
```
   user.mail –ne $null
```

## <a name="extension-attributes-and-custom-attributes"></a>Atrybuty rozszerzenia oraz atrybuty niestandardowe
Atrybuty rozszerzenia oraz atrybuty niestandardowe są obsługiwane w regułach członkostwo dynamiczne.

Atrybuty rozszerzenia są synchronizowane z lokalną okna serwera AD i mieć format "ExtensionAttributeX", gdzie X jest równa 1 – 15.
Oto przykład regułę, która używa atrybutu rozszerzenia
```
(user.extensionAttribute15 -eq "Marketing")
```
Atrybuty niestandardowe są synchronizowane z lokalną AD systemu Windows Server lub połączonych aplikacji SaaS i format "user.extension_[GUID]\__ [Attribute]", gdzie [identyfikator GUID] jest unikatowy identyfikator aplikacji, która utworzyła w usłudze AAD atrybut w usłudze AAD i [Attribute] jest nazwa atrybutu, ponieważ został on utworzony.
Na przykład regułę, która korzysta z atrybutem niestandardowym
```
user.extension_c272a57b722d4eb29bfe327874ae79cb__OfficeNumber  
```
Nazwa atrybutu niestandardowego można znaleźć w katalogu, badając użytkowników atrybutu za pomocą Eksploratora wykres i wyszukiwania dla nazwy atrybutu.

## <a name="direct-reports-rule"></a>Reguła "Bezpośrednich podwładnych"
Można utworzyć grupę wszystkich bezpośrednich podwładnych menedżera. Menedżera bezpośrednich podwładnych zmiany w przyszłości, członkostwo w grupie zostanie automatycznie dostosowana.

> [!NOTE]
> 1. Aby reguła mogła działać, upewnij się, że **identyfikator menedżera** właściwość została poprawnie ustawiona dla użytkowników w dzierżawie. Bieżąca wartość dla użytkownika można sprawdzić na ich **karta Profil**.
> 2. Ta reguła obsługuje tylko **bezpośredniego** raportów. Obecnie nie jest możliwe utworzyć grupę dla zagnieżdżonej hierarchii; na przykład grupy, która zawiera bezpośrednich podwładnych i ich raporty.
> 3. Ta zasada nie można łączyć z innymi regułami zaawansowane.

**Aby skonfigurować grupę**

1. Wykonaj kroki 1 – 5 z sekcji [do tworzenia zaawansowanych reguł](#to-create-the-advanced-rule)i wybierz **Typ członkostwa** z **dynamiczne użytkownika**.
2. Na **członkostwo dynamiczne reguły** bloku, wprowadź regułę przy użyciu następującej składni:

    *Bezpośrednich podwładnych dla "{objectID_of_manager}"*

    Przykład prawidłowy reguły:
```
                    Direct Reports for "62e19b97-8b3d-4d4a-a106-4ce66896a863"
```
    where “62e19b97-8b3d-4d4a-a106-4ce66896a863” is the objectID of the manager. The object ID can be found on manager's **Profile tab**.
3. Po zapisaniu reguły, wszyscy użytkownicy z określoną wartością Identyfikator menedżera zostanie dodany do grupy.

## <a name="using-attributes-to-create-rules-for-device-objects"></a>Aby utworzyć zasady obiekty urządzeń przy użyciu atrybutów
Można również utworzyć regułę, która wybiera obiekty urządzeń do członkostwa w grupie. Następujące atrybuty urządzenia mogą być używane.

 Atrybut urządzenia  | Wartości | Przykład
 ----- | ----- | ----------------
 accountEnabled | wartość true, false | (device.accountEnabled - eq true)
 Nazwa wyświetlana | Dowolną wartością ciągu |(device.displayName - eq "Tomasz Iphone")
 deviceOSType | Dowolną wartością ciągu | (device.deviceOSType - eq "iPad")- lub (device.deviceOSType - eq "iPhone")
 DeviceOSVersion | Dowolną wartością ciągu | (urządzenia. OSVersion - eq "9.1")
 deviceCategory | Nazwa kategorii prawidłowe urządzenie | (device.deviceCategory - eq "BYOD")
 deviceManufacturer | Dowolną wartością ciągu | (device.deviceManufacturer - eq "Samsung")
 deviceModel | Dowolną wartością ciągu | (device.deviceModel - eq "iPad lotniczego")
 deviceOwnership | Osobiste, firma, nieznane | (device.deviceOwnership - eq "Firmy")
 domainName | Dowolną wartością ciągu | (device.domainName - eq "contoso.com")
 enrollmentProfileName | Nazwa profilu rejestracji urządzeń firmy Apple | (device.enrollmentProfileName - eq "DEP iPhone")
 isRooted | wartość true, false | (device.isRooted - eq true)
 managementType | Zarządzanie urządzeniami Przenośnymi (dla urządzeń przenośnych)<br>Komputer (w przypadku komputerów zarządzanych przez agenta Komputerami z usługą Intune) | (device.managementType - eq "MDM")
 organizationalUnit | dowolną wartością ciągu pasującego do nazwy jednostki organizacyjnej, ustawione przez lokalnej usługi Active Directory | (device.organizationalUnit -eq "US PCs")
 deviceId | prawidłowy identyfikator urządzenia usługi Azure AD | (device.deviceId - eq "d4fe7726-5966-431c-b3b8-cddc8fdb717d")
 Identyfikator obiektu | Identyfikator obiektu: nieprawidłowy usługi Azure AD |  (device.objectId -eq 76ad43c9-32c5-45e8-a272-7b58b58f596d")



## <a name="changing-dynamic-membership-to-static-and-vice-versa"></a>Zmiana członkostwo dynamiczne statyczne i na odwrót
Użytkownik może zmienić sposób zarządzania członkostwa w grupie. Jest to przydatne, gdy chcesz zachować taką samą nazwę grupy i identyfikator w systemie, więc wszystkie istniejące odwołania do grupy są nadal ważne. Tworzenie nowej grupy wymagają aktualizacji tych odwołań.

Zaktualizowaliśmy Centrum usługi Azure AD administratora, aby dodać obsługę tej funkcji. Teraz klientów można przekonwertować istniejących grup z członkostwo dynamiczne przypisanych członkostwa i na odwrót za pośrednictwem Centrum administracyjnego usługi AD platformy Azure lub poleceń cmdlet programu PowerShell, jak pokazano poniżej.

> [!WARNING]
> Podczas zmiany istniejącej grupy statyczne Dynamiczna grupa, wszystkie istniejące elementy członkowskie zostaną usunięte z grupy, a następnie przetworzenie reguły członkostwa można dodać nowe elementy członkowskie. Jeśli grupa jest używana do kontrolowania dostępu do aplikacji lub zasobów, oryginalnego elementy Członkowskie mogą stracić dostęp do momentu reguły członkostwa jest w pełni przetworzony.
>
> Firma Microsoft zaleca test wcześniej nowe reguły członkostwa, aby upewnić się, że nowe członkostwo w grupie zgodnie z oczekiwaniami.

### <a name="using-azure-ad-admin-center-to-change-membership-management-on-a-group"></a>Aby zmienić zarządzanie członkostwem w grupie za pomocą Centrum administracyjnego usługi Azure AD 

1. Zaloguj się do [Centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com) przy użyciu konta, które jest administratorem globalnym lub administratorem konta użytkowników w dzierżawie.
2. Wybierz **grup**.
3. Z **wszystkich grup** listy, otwórz grupę, którą chcesz zmienić.
4. Wybierz **właściwości**.
5. Na **właściwości** stronie dla grupy, wybierz opcję **Typ członkostwa** przypisane (statyczny), dynamiczne użytkownika lub urządzenia dynamicznej, w zależności od typu żądanego członkostwa. Dynamicznego zarządzania członkostwem można użyć konstruktora reguły wybierz opcje Prosta reguła lub zapisać reguły zaawansowanej samodzielnie. 

Poniższe kroki są przykładem zmiany grupy ze statycznego na dynamiczny członkostwa dla grupy użytkowników. 

1. Na **właściwości** strony dla wybranej grupy, wybierz opcję **Typ członkostwa** z **dynamiczne użytkownika**, następnie wybierz opcję Tak w oknie dialogowym wyjaśniający zmiany do grupy członkostwo, aby kontynuować. 
  
   ![Wybierz typ członkostwa użytkownika dynamiczne](./media/active-directory-groups-dynamic-membership-azure-portal/select-group-to-convert.png)
  
2. Wybierz **zapytania dynamiczne dodawanie**, a następnie podaj reguły.
  
   ![Wprowadź reguły](./media/active-directory-groups-dynamic-membership-azure-portal/enter-rule.png)
  
3. Po utworzeniu reguły, wybierz **kwerendy dodaj** w dolnej części strony.
4. Wybierz **zapisać** na **właściwości** stronie dla grupy zapisać zmiany. **Typ członkostwa** grupy natychmiast zaktualizowany na liście grupy.

> [!TIP]
> Konwersja grupy może zakończyć się niepowodzeniem, jeśli reguły zaawansowanej, wprowadzony jest niepoprawne. W prawym górnym rogu portalu, który zawiera wyjaśnienie, dlaczego reguły nie może zostać zaakceptowana przez system zostanie wyświetlone powiadomienie. Przeczytaj uważnie, aby zrozumieć, jak można dostosować reguły, aby była nieprawidłowa.

### <a name="using-powershell-to-change-membership-management-on-a-group"></a>Przy użyciu programu PowerShell, aby zmienić zarządzanie członkostwem w grupie

> [!NOTE]
> Aby zmienić właściwości dynamicznych grupy będą musieli używać poleceń cmdlet z **wersję zapoznawczą** [programu Azure AD PowerShell w wersji 2](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0). Można zainstalować podglądu [galerii programu PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).

Oto przykład funkcji, które przełączają zarządzanie członkostwem w istniejącej grupy. W tym przykładzie są dokładnie dokumentowane poprawnie modyfikowania właściwości GroupTypes i zachowania wartości, które są związane z członkostwo dynamiczne.

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
Aby umożliwić statycznych grupy:
```
ConvertDynamicGroupToStatic "a58913b2-eee4-44f9-beb2-e381c375058f"
```
Aby grupy dynamicznej:
```
ConvertStaticGroupToDynamic "a58913b2-eee4-44f9-beb2-e381c375058f" "user.displayName -startsWith ""Peter"""
```
## <a name="next-steps"></a>Kolejne kroki
Te artykuły zawierają dodatkowe informacje o grupach w usłudze Azure Active Directory.

* [Zobacz istniejących grup](active-directory-groups-view-azure-portal.md)
* [Utwórz nową grupę i dodawanie członków](active-directory-groups-create-azure-portal.md)
* [Zarządzanie ustawieniami grupy](active-directory-groups-settings-azure-portal.md)
* [Zarządzanie członkostwami grup](active-directory-groups-membership-azure-portal.md)
* [Dynamiczne reguły dla użytkowników w grupie zarządzania](active-directory-groups-dynamic-membership-azure-portal.md)
