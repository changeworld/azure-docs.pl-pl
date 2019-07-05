---
title: Dostosowywanie konfiguracji zabezpieczeń systemu operacyjnego w usłudze Azure Security Center (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: W tym artykule pokazano, jak dostosować oceny Centrum zabezpieczeń
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/20/2019
ms.author: rkarlin
ms.openlocfilehash: 7095992253fbbe5aafce1eab889965250f5d59a8
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2019
ms.locfileid: "67551364"
---
# <a name="customize-os-security-configurations-in-azure-security-center-preview"></a>Dostosowywanie konfiguracji zabezpieczeń systemu operacyjnego w usłudze Azure Security Center (wersja zapoznawcza)

W tym instruktażu pokazano, jak dostosować oceny konfiguracji zabezpieczeń systemu operacyjnego w usłudze Azure Security Center.

> [!NOTE]
> Możliwość dostosowywania konfiguracji zabezpieczeń systemu operacyjnego zostaną wycofane z dniem 31 lipca 2019 r. Aby uzyskać więcej informacji i alternatywne usług zobacz [wycofywania Centrum zabezpieczeń funkcji (2019 lipca)](security-center-features-retirement-july2019.md#menu_securityconfigurations).

## <a name="what-are-os-security-configurations"></a>Co to są konfiguracje zabezpieczeń systemu operacyjnego?

Usługa Azure Security Center monitoruje konfiguracje zabezpieczeń, stosując zestaw [ponad 150 rekomendowanych reguł](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) dotyczących wzmacniania ochrony systemu operacyjnego, w tym reguł dotyczących zapór, inspekcji, zasad dotyczących haseł i wiele więcej. Jeśli okaże się, że maszyna ma konfigurację podatną na zagrożenia, usługa Security Center wygeneruje rekomendację dotyczącą zabezpieczeń.

Dostosowując reguły, organizacje można kontrolować, które opcje konfiguracji są bardziej odpowiednie dla swojego środowiska. Można ustawić zasady niestandardowe oceny i zastosować je na wszystkich komputerach stosowane w ramach subskrypcji.

> [!NOTE]
> - Obecnie Dostosowywanie konfiguracji zabezpieczeń systemu operacyjnego jest dostępny dla systemów operacyjnych w wersji 2008, 2008 R2, 2012, 2012 R2 i 2016 serwera systemu Windows tylko.
> - Konfiguracja stosowana do wszystkich maszyn wirtualnych i komputerów, które są podłączone do wszystkich obszarów roboczych w ramach wybranej subskrypcji.
> - Dostosowywanie konfiguracji zabezpieczeń systemu operacyjnego jest dostępna tylko w warstwie standardowa usługi Security Center.
>
>

Włączanie i wyłączanie określonej reguły, zmieniając odpowiednie ustawienie do istniejącej reguły lub dodając nową regułę, która opiera się na obsługiwanych typów reguł (rejestr, zasady inspekcji i zasady zabezpieczeń), można dostosować reguły konfiguracji zabezpieczeń systemu operacyjnego. Obecnie odpowiednie ustawienie musi być dokładną wartość.

Nowe reguły należy w tym samym formacie, a struktura innymi istniejącymi zasadami tego samego typu.

> [!NOTE]
> Aby dostosować konfiguracji zabezpieczeń systemu operacyjnego, musi mieć przypisaną rolę *właściciela subskrypcji*, *współautora subskrypcji*, lub *Administrator zabezpieczeń*.
>
>

## <a name="customize-the-default-os-security-configuration"></a>Dostosowywanie domyślna konfiguracja zabezpieczeń systemu operacyjnego

Aby dostosować domyślna konfiguracja zabezpieczeń systemu operacyjnego w usłudze Security Center, wykonaj następujące czynności:

1.  Otwórz pulpit nawigacyjny usługi **Security Center**.

2.  W okienku po lewej stronie wybierz **ceny u & stawienia**.

    ![Lista zasad zabezpieczeń](media/security-center-customize-os-security-config/manual-provision.png)

4. Wybierz subskrypcję, zastosowanie, a następnie wybierz pozycję **edytowanie konfiguracji zabezpieczeń**.  

    ![W oknie "Edytowanie konfiguracji zabezpieczeń"](media/security-center-customize-os-security-config/blade.png)

5. Wykonaj kroki, aby pobrać, edytować i przekazać zmodyfikowany plik.

   > [!NOTE]
   > Domyślnie pobrany plik konfiguracji jest w *json* formatu. Aby uzyskać instrukcje dotyczące modyfikowania tego pliku, przejdź do [dostosować plik konfiguracji](#customize-the-configuration-file).
   >

6. Aby zatwierdzić zmiany, wybierz **Zapisz**. W przeciwnym razie zasady nie są przechowywane.

    ![Przycisk Zapisz](media/security-center-customize-os-security-config/save-successfully.png)

   Po plik został pomyślnie zapisany, konfiguracja zostanie zastosowana do wszystkich maszyn wirtualnych i komputerów, które są podłączone do obszarów roboczych w ramach subskrypcji. Ten proces zwykle zajmuje kilka minut, ale może trwać dłużej, w zależności od rozmiaru infrastruktury.

W dowolnym momencie możesz zresetować bieżącej konfiguracji zasad do stanu domyślnego. Aby to zrobić w **reguł konfiguracji zabezpieczeń Edycja systemu operacyjnego** wybierz **resetowania**. Potwierdź tę opcję, wybierając **tak** w wyskakującym oknie potwierdzenia.

![W oknie potwierdzenia resetowania](media/security-center-customize-os-security-config/edit-alert.png)

## <a name="customize-the-configuration-file"></a>Dostosowywanie pliku konfiguracji

W pliku dostosowania każdej obsługiwanej wersji systemu operacyjnego ma zestaw reguł lub zestaw reguł. Każdy zestaw reguł ma własną nazwę i unikatowy identyfikator, jak pokazano w poniższym przykładzie:

![Zestaw reguł nazwy i Identyfikatora](media/security-center-customize-os-security-config/custom-file.png)

> [!NOTE]
> Ten przykładowy plik był edytowany w programie Visual Studio, ale można również użyć programu Notatnik, jeśli masz przeglądarka JSON wtyczki zainstalowane.
>
>

Podczas edycji pliku dostosowywania, można zmodyfikować reguły jeden lub wszystkie z nich. Każdy zestaw reguł zawiera *reguły* sekcja, która jest podzielone na trzy kategorie: Rejestru zasad inspekcji i zasady zabezpieczeń, jak pokazano poniżej:

![Trzy kategorie reguł](media/security-center-customize-os-security-config/rules-section.png)

Każda kategoria ma swój własny zestaw atrybutów. Można zmienić następujące atrybuty:

- **expectedValue**: Typ danych pola tego atrybutu musi odpowiadać obsługiwane wartości dla każdego *typ reguły*, na przykład:

  - **baselineRegistryRules**: Wartość powinna być zgodna [regValueType](https://msdn.microsoft.com/library/windows/desktop/ms724884) zdefiniowanego w tej regule.

  - **baselineAuditPolicyRules**: Użyj jednej z następujących wartości ciągu:

    - *Sukcesów i niepowodzeń*

    - *Powodzenie*

  - **baselineSecurityPolicyRules**: Użyj jednej z następujących wartości ciągu:

    - *Nikt*

    - Lista dozwolonych grup użytkowników, na przykład: *Administratorzy*, *Operatorzy kopii zapasowych*

-   **Stan**: Ciąg może zawierać odpowiednie opcje *wyłączone* lub *włączone*. W tej wersji ciąg jest rozróżniana wielkość liter.

Są to tylko pola, które można skonfigurować. Jeśli możesz naruszyć format pliku lub rozmiaru, nie można zapisać zmiany. Zostanie wyświetlony błąd informujący, że należy przekazać prawidłowy plik konfiguracji JSON.

Aby uzyskać listę innych potencjalnych błędów, zobacz [kody błędów](#error-codes).

Trzy poniższe sekcje zawierają przykłady powyższych zasad. *ExpectedValue* i *stanu* atrybuty mogą być zmieniane.

**baselineRegistryRules**
```json
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
```json
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
```json
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

Niektóre reguły są duplikowane dla różnych typów systemów operacyjnych. Zduplikowane reguły mają taką samą *originalId* atrybutu.

## <a name="create-custom-rules"></a>Tworzenie reguł niestandardowych

Można również utworzyć nowe zasady. Przed utworzeniem nowej reguły, pamiętać o następujących ograniczeniach:

-   Wersja schematu *baselineId* i *baselineName* nie można jej zmienić.

-   Nie można usunąć zestawu reguł.

-   Nie można dodać zestawu reguł.

-   Maksymalna liczba dozwolonych (w tym reguły domyślne) reguł wynosi 1000.

Nowe reguły niestandardowe są oznaczone jako nowe źródło niestandardowe (! = "Microsoft"). *RuleId* pole może być zerowa lub pusta. Jeśli jest on pusty, Microsoft generuje go. Jeśli nie jest pusta, musi być prawidłowym identyfikatorem GUID, która jest unikatowa dla wszystkich reguł (domyślne i niestandardowe). Przejrzyj następujące ograniczenia dla pól podstawowych:

-   **originalId**: Może to być wartość null lub jest pusty. Jeśli *originalId* jest nie jest pusty, powinno być prawidłowym identyfikatorem GUID.

-   **cceId**: Może to być wartość null lub jest pusty. Jeśli *cceId* jest nie jest pusty, musi być unikatowa.

-   **Typ ruleType**: (wybierz jedną opcję) rejestru, AuditPolicy lub SecurityPolicy.

-   **Ważność**: (wybierz jedną opcję) nieznany, krytyczne, ostrzeżenie lub komunikat o charakterze informacyjnym.

-   **analyzeOperation**: Musi być *jest równa*.

-   **auditPolicyId**: Musi być prawidłowym identyfikatorem GUID.

-   **regValueType**: (wybierz jedną opcję) Int, Long, String lub MultipleString.

> [!NOTE]
> Gałąź musi być *LocalMachine*.
>
>

Przykład nową regułę niestandardową:

**Rejestr**:
```json
    {
    "hive": "LocalMachine",
    "regValueType": "Int",
    "keyPath":
    "System\\\\CurrentControlSet\\\\Services\\\\Netlogon\\\\MyKeyName",
    "valueName": "MyValueName",
    "originalId": "",
    "cceId": "",
    "ruleName": "My new registry rule", "baselineRuleType": "Registry",
    "expectedValue": "123", "severity": "Critical",
    "analyzeOperation": "Equals",
    "source": "MyCustomSource",
    "state": "Enabled"
    }
```
**Zasady zabezpieczeń**:
```json
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
```json
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

## <a name="file-upload-failures"></a>Błędy przekazywania plików

Jeśli plik konfiguracyjny przesłane jest nieprawidłowa z powodu błędów w wartości lub formatowania, błąd jest wyświetlany, takich jak **Zapisz akcja nie powiodła się**. Możesz pobrać raport CSV szczegółowe informacje o błędach w celu korygowania i naprawiać błędy, zanim zostanie ponownie prześlij plik konfiguracji poprawiony.

Przykład pliku błędu:

![Przykład pliku błędu](media/security-center-customize-os-security-config/errors-file.png)

## <a name="error-codes"></a>Kody błędów

Wszystkie potencjalne błędy są wymienione w poniższej tabeli:

| **Error**                                | **Opis**                                                                                                                              |
|------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------|
| BaselineConfigurationSchemaVersionError  | Właściwość *schemaVersion* znaleziono nieprawidłowy lub pusty. Wartość musi być równa *{0}* .                                                         |
| BaselineInvalidStringError               | Właściwość *{0}* nie może zawierać  *\\n*.                                                                                                         |
| BaselineNullRuleError                    | Lista reguł linii bazowej konfiguracji zawiera reguły z wartością *null*.                                                                         |
| BaselineRuleCceIdNotUniqueError          | Identyfikator CCE *{0}* nie jest unikatowa.                                                                                                                  |
| BaselineRuleEmptyProperty                | Właściwość *{0}* lub jest nieprawidłowy.                                                                                                       |
| BaselineRuleIdNotInDefault               | Reguła ma właściwości source *Microsoft* , ale nie został znaleziony w zestawie reguł domyślna firmy Microsoft.                                                   |
| BaselineRuleIdNotUniqueError             | Identyfikator reguły nie jest unikatowa.                                                                                                                       |
| BaselineRuleInvalidGuid                  | Właściwość *{0}* jest nieprawidłowy. Wartość nie jest prawidłowym identyfikatorem GUID.                                                                             |
| BaselineRuleInvalidHive                  | Gałąź musi być LocalMachine.                                                                                                                   |
| BaselineRuleNameNotUniqueError           | Nazwa reguły nie jest unikatowa.                                                                                                                 |
| BaselineRuleNotExistInConfigError        | Reguła nie została odnaleziona w nowej konfiguracji. Nie można usunąć reguły.                                                                     |
| BaselineRuleNotFoundError                | Reguła nie została odnaleziona w domyślnych reguł odniesienia.                                                                                        |
| BaselineRuleNotInPlace                   | Reguła domyślna reguła z typem {0} i wyświetlony na liście {1} listy.                                                                       |
| BaselineRulePropertyTooLong              | Właściwość *{0}* jest za długa. Maksymalna dozwolona długość: {1}.                                                                                        |
| BaselineRuleRegTypeInvalidError          | Oczekiwana wartość *{0}* jest niezgodny z typem wartości rejestru, który jest zdefiniowany.                                                              |
| BaselineRulesetAdded                     | Zestaw reguł z identyfikatorem *{0}* nie został znaleziony w konfiguracji domyślnej. Nie można dodać zestaw reguł.                                               |
| BaselineRulesetIdMustBeUnique            | Zestaw reguł jednej linii bazowej *{0}* muszą być unikatowe.                                                                                           |
| BaselineRulesetNotFound                  | Zestaw reguł z identyfikatorem *{0}* i nazwy *{1}* nie został znaleziony w danej konfiguracji. Nie można usunąć zestaw reguł.                                |
| BaselineRuleSourceNotMatch               | Reguła o identyfikatorze *{0}* jest już zdefiniowany.                                                                                                       |
| BaselineRuleTypeDoesntMatch              | Domyślny typ reguły to *{0}* .                                                                                                              |
| BaselineRuleTypeDoesntMatchError         | Rzeczywisty typ reguły to *{0}* , ale *typ ruleType* właściwość *{1}* .                                                                          |
| BaselineRuleUnpermittedChangesError      | Tylko *expectedValue* i *stanu* właściwości mogą zostać zmienione.                                                                       |
| BaselineTooManyRules                     | Maksymalna liczba dozwolonych niestandardowych reguł to {0} reguły. Zawiera daną konfigurację {1} reguł, {2} domyślne reguły, a {3} dostosowanej reguły. |
| ErrorNoConfigurationStatus               | Brak stanu konfiguracji został znaleziony. Stan konfiguracji żądanego stanu: *Domyślne* lub *niestandardowe*.                                    |
| ErrorNonEmptyRulesetOnDefault            | Stan konfiguracji jest ustawiona jako domyślna. *BaselineRulesets* listy musi być wartość null lub jest pusty.                                                          |
| ErrorNullRulesetsPropertyOnCustom        | Jest w stanie danej konfiguracji *niestandardowe* ale *baselineRulesets* właściwość ma wartość null lub jest pusty.                                             |
| ErrorParsingBaselineConfig               | Danej konfiguracji jest nieprawidłowy. Przynajmniej jedna z wartości zdefiniowanych ma wartość null lub nieprawidłowego typu.                                  |
| ErrorParsingIsDefaultProperty            | Dany *configurationStatus* wartość *{0}* jest nieprawidłowy. Wartość może być tylko *domyślne* lub *niestandardowe*.                                         |
| InCompatibleViewVersion                  | Wyświetlanie wersji *{0}* jest *nie* obsługiwane w przypadku tego typu obszaru roboczego.                                                                                   |
| InvalidBaselineConfigurationGeneralError | Konfiguracja jednej linii bazowej został znaleziony z co najmniej jeden błąd sprawdzania poprawności typu.                                                          |
| ViewConversionError                      | Widok jest w starszej wersji niż obsługuje obszaru roboczego. Wyświetl konwersja nie powiodła się: {0}.                                                                 |

Jeśli nie masz wystarczających uprawnień, może uzyskać ogólny błąd, jak pokazano poniżej:

!["Zapisz akcja nie powiodła się" komunikat o błędzie](media/security-center-customize-os-security-config/general-failure-error.png)

## <a name="next-steps"></a>Kolejne kroki
W tym artykule omówiono sposób dostosowywania oceny konfiguracji zabezpieczeń systemu operacyjnego w usłudze Security Center. Aby dowiedzieć się więcej na temat zasad konfiguracji i korygowania, zobacz:

- [Identyfikatory typowych konfiguracji Centrum zabezpieczeń i reguły linii bazowych](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335).
- Usługa Security Center używa typowych konfiguracji wyliczenie (CCE) do przypisywania unikatowych identyfikatorów dla reguły konfiguracji. Aby uzyskać więcej informacji, zobacz [CCE](https://nvd.nist.gov/config/cce/index).
- Aby rozwiązać luk w zabezpieczeniach, podczas konfiguracji systemu operacyjnego jest niezgodny z reguły konfiguracji zabezpieczeń, zobacz [skoryguj konfiguracje zabezpieczeń](security-center-remediate-os-vulnerabilities.md).
