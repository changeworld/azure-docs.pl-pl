---
title: Dostosowywanie systemu operacyjnego konfiguracjach zabezpieczeń w Centrum zabezpieczeń Azure (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: W tym artykule przedstawiono sposób dostosowywania oceny Centrum zabezpieczeń
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/25/2018
ms.author: terrylan
ms.openlocfilehash: f12441a960db9f1c45bca2a5b95f3669923c7e3d
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2018
ms.locfileid: "28200014"
---
# <a name="customize-os-security-configurations-in-azure-security-center-preview"></a>Dostosowywanie systemu operacyjnego konfiguracjach zabezpieczeń w Centrum zabezpieczeń Azure (wersja zapoznawcza)

W tym przewodniku pokazano, jak dostosować oceny konfiguracji zabezpieczeń systemu operacyjnego w Centrum zabezpieczeń Azure.

## <a name="what-are-os-security-configurations"></a>Co to są konfiguracji zabezpieczeń systemu operacyjnego?

Centrum zabezpieczeń Azure monitoruje konfiguracji zabezpieczeń, stosując zestaw [zalecane reguły kodu ponad 150](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) do wzmacniania systemu operacyjnego, w tym reguły związane z zapory, inspekcji, zasad haseł i innych. Jeśli maszyna zostanie znaleziony konfiguracji narażony, Centrum zabezpieczeń generuje zalecana ze względów bezpieczeństwa.

Dostosowując zasady organizacji można kontrolować, które opcje konfiguracji są bardziej odpowiednie dla swojego środowiska. Można ustawić zasady niestandardowe oceny i zastosować je na wszystkich komputerach stosowane w ramach subskrypcji.

> [!NOTE]
> - Dostosowywanie konfiguracji zabezpieczeń systemu operacyjnego jest obecnie dostępne dla systemu Windows Server w wersji 2008, 2008 R2, 2012 i 2012 R2 w systemach operacyjnych.
> - Zastosowanie tej konfiguracji do wszystkich maszyn wirtualnych i komputerów, które są podłączone do wszystkich obszarów roboczych w ramach wybranej subskrypcji.
> - Dostosowywanie konfiguracji zabezpieczeń systemu operacyjnego jest dostępna tylko w warstwie standardowa Centrum zabezpieczeń.
>
>

Włączanie i wyłączanie określonej reguły, zmieniając odpowiednie ustawienie do istniejącej reguły lub dodając nową regułę, która jest oparta na obsługiwanych typów reguł (rejestru, zasady inspekcji i zasady zabezpieczeń), można dostosować zasady konfiguracji zabezpieczeń systemu operacyjnego. Obecnie odpowiednie ustawienie musi być dokładną wartość.

Nowe zasady musi być w tym samym formacie i struktury jako innych istniejących reguł tego samego typu.

> [!NOTE]
> Aby dostosować konfiguracji zabezpieczeń systemu operacyjnego, musi mieć przypisaną rolę z *właściciel subskrypcji*, *Współautor subskrypcji*, lub *Administrator zabezpieczeń*.
>
>

## <a name="customize-the-default-os-security-configuration"></a>Dostosowywanie domyślna konfiguracja zabezpieczeń systemu operacyjnego

Aby dostosować domyślna konfiguracja zabezpieczeń systemu operacyjnego w Centrum zabezpieczeń, wykonaj następujące czynności:

1.  Otwórz pulpit nawigacyjny usługi **Security Center**.

2.  W okienku po lewej stronie wybierz **zasady zabezpieczeń**.  
    **Centrum zabezpieczeń — zasady zabezpieczeń** zostanie otwarte okno.

    ![Lista zasad zabezpieczeń](media/security-center-customize-os-security-config/open-security-policy.png)

3.  Wybierz subskrypcję, którą chcesz wykonać dostosowania dla.

4. W obszarze **składniki zasad**, wybierz pozycję **Edycja konfiguracji zabezpieczeń**.  
    **Edycja konfiguracji zabezpieczeń** zostanie otwarte okno.

    ![Okno "Edytuj konfiguracje zabezpieczeń"](media/security-center-customize-os-security-config/blade.png)

5. W okienku po prawej stronie wykonaj kroki pobierania, edytowania i przekazywania zmodyfikowany plik.

   > [!NOTE]
   > Domyślnie pobrany plik konfiguracji jest w *json* format. Aby uzyskać instrukcje na temat modyfikowania tego pliku, przejdź do [dostosować plik konfiguracji](#customize-the-configuration-file).
   >

   Po plik został pomyślnie zapisany, konfiguracja zostanie zastosowana do wszystkich maszyn wirtualnych i komputerów, które są podłączone do wszystkich obszarów roboczych w ramach subskrypcji. Proces zazwyczaj zajmuje kilka minut, ale może trwać dłużej, w zależności od wielkości infrastruktury.

6. Aby zatwierdzić zmiany, wybierz **zapisać**. W przeciwnym razie wartość zasad nie są przechowywane.

    ![Przycisk Zapisz](media/security-center-customize-os-security-config/save-successfully.png)

W dowolnym momencie można zresetować bieżącej konfiguracji zasad do stanu domyślnego. Aby to zrobić, w **reguły konfiguracji zabezpieczeń edycji systemu operacyjnego** wybierz **zresetować**. Potwierdzić tę opcję, wybierając **tak** w wyskakującym oknie potwierdzenia.

![Resetuj okno potwierdzenia](media/security-center-customize-os-security-config/edit-alert.png)

## <a name="customize-the-configuration-file"></a>Dostosowywanie pliku konfiguracji

W pliku dostosowania wszystkie obsługiwane wersje systemu operacyjnego ma zestaw reguł lub zestaw reguł. Każdy zestaw reguł ma własną nazwę i unikatowy identyfikator, jak pokazano w poniższym przykładzie:

![Zestaw reguł nazwy i Identyfikatora](media/security-center-customize-os-security-config/custom-file.png)

> [!NOTE]
> Ten przykładowy plik był edytowany w programie Visual Studio, ale można również użyć programu Notatnik, jeśli masz Podgląd JSON wtyczki zainstalowane.
>
>

Podczas edytowania pliku dostosowania można zmodyfikować jedną regułę lub wszystkie z nich. Każdy zestaw reguł zawiera *reguły* sekcja, która jest podzielone na trzy kategorie: rejestru, zasady inspekcji i zasady zabezpieczeń, jak pokazano poniżej:

![Trzy kategorie zestaw reguł](media/security-center-customize-os-security-config/rules-section.png)

Każda kategoria ma swój własny zestaw atrybutów. Można zmienić następujące atrybuty:

- **expectedValue**: typ danych pola tego atrybutu musi być zgodna obsługiwane wartości na *typ reguły*, na przykład:

  - **baselineRegistryRules**: wartość powinna być zgodna [regValueType](https://msdn.microsoft.com/library/windows/desktop/ms724884) zdefiniowanego w tej regule.

  - **baselineAuditPolicyRules**: użyj jednej z następujących wartości ciągu:

    - *Sukces i niepowodzenie*

    - *Powodzenie*

  - **baselineSecurityPolicyRules**: użyj jednej z następujących wartości ciągu:

    - *Nikt nie*

    - Lista dozwolonych grup użytkowników, na przykład: *Administratorzy*, *Operatorzy kopii zapasowych*

-   **Stan**: ciąg może zawierać opcji *wyłączone* lub *włączone*. W tej wersji prywatnej wersji zapoznawczej ciąg jest rozróżniana wielkość liter.

Są to jedyne pola, które można skonfigurować. Jeśli użytkownik narusza format pliku lub rozmiar, nie można zapisać zmiany. Następujący komunikat o błędzie występuje, gdy nie można przetworzyć pliku:

![Komunikat o błędzie konfiguracji zabezpieczeń](media/security-center-customize-os-security-config/invalid-json.png)

Lista innych potencjalne błędy, zobacz [kody błędów](#error-codes).

Trzy poniższe sekcje zawierają przykłady powyższych zasad. *ExpectedValue* i *stanu* można zmienić atrybutów.

**baselineRegistryRules**
```
    {
    "hive": "LocalMachine",
    "regValueType": "Int",
    "keyPath":
    "System\\\\CurrentControlSet\\\\Services\\\\LanManServer\\\\Parameters",
    "valueName": "restrictnullsessaccess",
    "ruleId": "f9020046-6340-451d-9548-3c45d765d06d",
    "originalId": "0f319931-aa36-4313-9320-86311c0fa623",
    "cceId": "CCE-10940-5",
    "ruleName": "Network access: Restrict anonymous access to Named Pipes and
    Shares",
    "ruleType": "Registry",
    "expectedValue": "1",
    "severity": "Warning",
    "analyzeOperation": "Equals",
    "source": "Microsoft",
    "state": "Disabled"

    }
```

**baselineAuditPolicyRules**
```
    {
    "auditPolicyId": "0cce923a-69ae-11d9-bed3-505054503030",
    "ruleId": "37745508-95fb-44ec-ab0f-644ec0b16995",
    "originalId": "2ea0de1a-c71d-46c8-8350-a7dd4d447895",
    "cceId": "CCE-11001-5",
    "ruleName": "Audit Policy: Account Management: Other Account Management Events",
    "ruleType": "AuditPolicy",
    "expectedValue": "Success and Failure",
    "severity": "Critical",
    "analyzeOperation": "Equals",
    "source": "Microsoft",
    "state": "Enabled"
    }
```

**baselineSecurityPolicyRules**
```
    {
    "sectionName": "Privilege Rights",
    "settingName": "SeIncreaseWorkingSetPrivilege",
    "ruleId": "b0ec9d5e-916f-4356-83aa-c23522102b33",
    "originalId": "b61bd492-74b0-40f3-909d-36b9bf54e94c",
    "cceId": "CCE-10548-6",
    "ruleName": "Increase a process working set",
    "ruleType": "SecurityPolicy",
    "expectedValue": "Administrators, Local Service",
    "severity": "Warning",
    "analyzeOperation": "Equals",
    "source": "Microsoft",
    "state": "Enabled"
    }
```

Niektóre reguły są duplikowane dla różnych typów systemu operacyjnego. Zduplikowane reguły mają takie same *originalId* atrybutu.

## <a name="create-custom-rules"></a>Tworzenie niestandardowych reguł

Można również utworzyć nowe reguły. Przed utworzeniem nowej reguły, mieć na uwadze następujące ograniczenia:

-   Wersja schematu *baselineId* i *baselineName* nie można zmienić.

-   Nie można usunąć zestaw reguł.

-   Nie można dodać zestaw reguł.

-   Maksymalna liczba reguł niedozwolonych (w tym reguły domyślne) wynosi 1000.

Nowe reguły niestandardowe są oznaczone ikoną z nowego źródła niestandardowego (! = "Microsoft"). *RuleId* pole może być zerowa ani pusta. Jeśli jest pusty, Microsoft generuje go. Jeśli nie jest pusta, musi mieć prawidłowy identyfikator GUID, który jest unikatowy w całej wszystkie reguły (domyślne i niestandardowe). Przejrzyj następujące ograniczenia dla pól rdzeni:

-   **originalId**: może być zerowa ani pusta. Jeśli *originalId* jest nie jest pusty, powinno być prawidłowym identyfikatorem GUID.

-   **cceId**: może być zerowa ani pusta. Jeśli *cceId* jest nie jest pusty, musi być unikatowa.

-   **Typ**: (Wybierz jeden) rejestru, AuditPolicy lub SecurityPolicy.

-   **Ważność**: (Wybierz jeden) nieznany, krytyczne, ostrzeżenie lub komunikat informacyjny.

-   **analyzeOperation**: musi być *jest równe*.

-   **auditPolicyId**: musi być prawidłowym identyfikatorem GUID.

-   **regValueType**: (Wybierz jeden) Int, Long, ciągu lub MultipleString.

> [!NOTE]
> Gałąź musi być *LocalMachine*.
>
>

Przykład nową regułę niestandardową:

**Rejestru**:
```
    {
    "hive": "LocalMachine",
    "regValueType": "Int",
    "keyPath":
    "System\\\\CurrentControlSet\\\\Services\\\\Netlogon\\\\MyKeyName",
    "valueName": "MyValueName",
    "originalId": "",
    "cceId": "",
    "ruleName": "My new registry rule”, "baselineRuleType": "Registry",
    "expectedValue": "123", "severity": "Critical",
    "analyzeOperation": "Equals",
    "source": "MyCustomSource",
    "state": "Enabled"
    }
```
**Zasady zabezpieczeń**:
```
   {
   "sectionName": "Privilege Rights",
   "settingName": "SeDenyBatchLogonRight",
   "originalId": "",
   "cceId": "",
   "ruleName": "My new security policy rule", "baselineRuleType":
   "SecurityPolicy",
   "expectedValue": "Guests",
   "severity": "Critical",
   "analyzeOperation": "Equals", "source": " MyCustomSource ",
   "state": "Enabled"
   }
```
**Zasady inspekcji**:
```
   {
   "auditPolicyId": "0cce923a-69ae-11d9-bed3-505054503030",
   "originalId": "",
   "cceId": "",
   "ruleName": " My new audit policy rule ", "baselineRuleType": "AuditPolicy",
   "expectedValue": " Failure",
   "severity": "Critical",
   "analyzeOperation": "Equals", "source": " MyCustomSource ",
   "state": "Enabled"
   }
```

## <a name="file-upload-failures"></a>Plik operacji przekazywania zakończonych niepowodzeniem

Jeśli plik konfiguracji przesłanych jest nieprawidłowa z powodu błędów w wartości lub formatowania, zostanie wyświetlony błąd awarii. Można pobrać raportu CSV szczegóły błędów można skorygować i naprawić błędy, zanim zostanie ponownie prześlij plik konfiguracji poprawiony.

!["Zapisz akcja nie powiodła się" komunikat o błędzie](media/security-center-customize-os-security-config/invalid-configuration.png)

Przykład pliku błędu:

![Przykład pliku błędu](media/security-center-customize-os-security-config/errors-file.png)

## <a name="error-codes"></a>Kody błędów

Wszelkie potencjalne błędy są wymienione w poniższej tabeli:

| **Błąd**                                | **Opis**                                                                                                                              |
|------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------|
| BaselineConfiguratiohSchemaVersionError  | Właściwość *schemaVersion* znaleziono nieprawidłowy lub pusty. Musi mieć ustawioną wartość *{0}*.                                                         |
| BaselineInvalidStringError               | Właściwość *{0}* nie może zawierać  *\\ n* .                                                                                                         |
| BaselineNullRuleError                    | Listę reguł linii bazowej konfiguracji zawiera reguły z wartością *null*.                                                                         |
| BaselineRuleCceIdNotUniqueError          | Identyfikator CCE *{0}* nie jest unikatowa.                                                                                                                  |
| BaselineRuleEmptyProperty                | Właściwość *{0}* lub jest nieprawidłowy.                                                                                                       |
| BaselineRuleIdNotInDefault               | Reguła ma właściwości source *Microsoft* , ale nie został znaleziony w domyślny zestaw reguł firmy Microsoft.                                                   |
| BaselineRuleIdNotUniqueError             | Identyfikator reguły nie jest unikatowa.                                                                                                                       |
| BaselineRuleInvalidGuid                  | Właściwość *{0}* jest nieprawidłowy. Wartość nie jest prawidłowym identyfikatorem GUID.                                                                             |
| BaselineRuleInvalidHive                  | Gałąź musi być LocalMachine.                                                                                                                   |
| BaselineRuleNameNotUniqueError           | Nazwa reguły nie jest unikatowa.                                                                                                                 |
| BaselineRuleNotExistInConfigError        | Zasada nie została odnaleziona w nowej konfiguracji. Nie można usunąć reguły.                                                                     |
| BaselineRuleNotFoundError                | Reguły nie został znaleziony w domyślnym zestaw reguł linii bazowej.                                                                                        |
| BaselineRuleNotInPlace                   | Reguła zgodny z regułą domyślnego o typ {0} i wymienionej na liście {1}.                                                                       |
| BaselineRulePropertyTooLong              | Właściwość *{0}* jest zbyt długa. Maksymalna dozwolona długość: {1}.                                                                                        |
| BaselineRuleRegTypeInvalidError          | Oczekiwana wartość *{0}* nie pasuje do typu wartość rejestru, która jest zdefiniowana.                                                              |
| BaselineRulesetAdded                     | Zestaw reguł z Identyfikatorem *{0}* nie został znaleziony w konfiguracji domyślnej. Nie można dodać zestaw reguł.                                               |
| BaselineRulesetIdMustBeUnique            | Zestaw reguł jednej linii bazowej *{0}* muszą być unikatowe.                                                                                           |
| BaselineRulesetNotFound                  | Zestaw reguł o identyfikatorze *{0}* i nazwy *{1}* nie został znaleziony w danej konfiguracji. Nie można usunąć zestaw reguł.                                |
| BaselineRuleSourceNotMatch               | Reguła o identyfikatorze *{0}* jest już zdefiniowany.                                                                                                       |
| BaselineRuleTypeDoesntMatch              | Domyślny typ reguły jest *{0}*.                                                                                                              |
| BaselineRuleTypeDoesntMatchError         | Rzeczywisty typ reguły to *{0}*, ale *typ* właściwość jest *{1}*.                                                                          |
| BaselineRuleUnpermittedChangesError      | Tylko *expectedValue* i *stanu* właściwości mogą zostać zmienione.                                                                       |
| BaselineTooManyRules                     | Maksymalna liczba dozwolonych niestandardowych reguł jest reguły {0}. Danej konfiguracji zawiera reguły {1}, {2} domyślnych reguł i {3} dostosowanej reguły. |
| ErrorNoConfigurationStatus               | Stan konfiguracji, nie został znaleziony. Stan konfiguracji żądanego stanu: *domyślne* lub *niestandardowy*.                                    |
| ErrorNonEmptyRulesetOnDefault            | Stan konfiguracji jest ustawiony na wartość domyślną. *BaselineRulesets* listy musi być zerowa ani pusta.                                                          |
| ErrorNullRulesetsPropertyOnCustom        | Jest w stanie danej konfiguracji *niestandardowy* , ale *baselineRulesets* właściwość ma wartość null lub pusty.                                             |
| ErrorParsingBaselineConfig               | Danej konfiguracji jest nieprawidłowy. Przynajmniej jedna z wartości zdefiniowanych ma wartość null lub nieprawidłowego typu.                                  |
| ErrorParsingIsDefaultProperty            | Podany *configurationStatus* wartość *{0}* jest nieprawidłowy. Wartość może być tylko *domyślne* lub *niestandardowy*.                                         |
| InCompatibleViewVersion                  | Wyświetlanie wersji *{0}* jest *nie* obsługiwane w tym typie obszaru roboczego.                                                                                   |
| InvalidBaselineConfigurationGeneralError | Jednej linii bazowej konfiguracji znaleziono z co najmniej jeden błąd sprawdzania poprawności typu.                                                          |
| ViewConversionError                      | Widok jest w starszej wersji niż obszar roboczy obsługuje. Wyświetl konwersja nie powiodła się: {0}.                                                                 |

Jeśli nie masz wystarczających uprawnień, można otrzymać błąd ogólny błąd, jak pokazano poniżej:

!["Zapisz akcja nie powiodła się" komunikat o błędzie](media/security-center-customize-os-security-config/general-failure-error.png)

## <a name="next-steps"></a>Kolejne kroki
W tym artykule opisano sposób dostosowywania oceny konfiguracji zabezpieczeń systemu operacyjnego w Centrum zabezpieczeń. Aby dowiedzieć się więcej na temat zasad konfiguracji i korygowania, zobacz:

- [Wspólne identyfikatory konfiguracji Centrum zabezpieczeń i reguły linii bazowej](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335).
- Centrum zabezpieczeń używane typowych konfiguracji wyliczenie (CCE) do przypisywania unikatowych identyfikatorów do reguły konfiguracji. Aby uzyskać więcej informacji, zobacz [CCE](https://nvd.nist.gov/config/cce/index).
- Aby rozwiązać luk w zabezpieczeniach, podczas konfiguracji systemu operacyjnego jest niezgodny z reguły konfiguracji zabezpieczeń, zobacz [skorygować konfiguracjach zabezpieczeń](security-center-remediate-os-vulnerabilities.md).
