---
title: Dostosowywanie konfiguracji zabezpieczeń systemu operacyjnego w Azure Security Center (wersja zapoznawcza) | Microsoft Docs
description: W tym artykule pokazano, jak dostosować oceny w usłudze Security Center
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
ms.date: 08/15/2019
ms.author: v-mohabe
ms.openlocfilehash: df4f51f97798048b28c0193dbc61e07fc55c9adc
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/16/2019
ms.locfileid: "69535075"
---
# <a name="customize-os-security-configurations-in-azure-security-center-retired"></a>Dostosowywanie konfiguracji zabezpieczeń systemu operacyjnego w Azure Security Center (wycofane)

W tym instruktażu pokazano, jak dostosować oceny konfiguracji zabezpieczeń systemu operacyjnego (wersja zapoznawcza) w Azure Security Center.

> [!NOTE]
> Możliwość dostosowywania konfiguracji zabezpieczeń systemu operacyjnego (funkcja w wersji zapoznawczej) została wycofana 31 lipca 2019. Aby uzyskać więcej informacji i alternatywnych usług, zobacz wycofywanie [funkcji Security Center (lipiec 2019)](security-center-features-retirement-july2019.md#menu_securityconfigurations).

## <a name="what-are-os-security-configurations"></a>Co to są konfiguracje zabezpieczeń systemu operacyjnego?

Usługa Azure Security Center monitoruje konfiguracje zabezpieczeń, stosując zestaw [ponad 150 rekomendowanych reguł](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) dotyczących wzmacniania ochrony systemu operacyjnego, w tym reguł dotyczących zapór, inspekcji, zasad dotyczących haseł i wiele więcej. Jeśli okaże się, że maszyna ma konfigurację podatną na zagrożenia, usługa Security Center wygeneruje rekomendację dotyczącą zabezpieczeń.

Dostosowując reguły, organizacje mogą kontrolować, które opcje konfiguracji są bardziej odpowiednie dla swojego środowiska. Można ustawić dostosowane zasady oceny, a następnie zastosować je na wszystkich odpowiednich maszynach w subskrypcji.

> [!NOTE]
> - Obecnie dostosowanie konfiguracji zabezpieczeń systemu operacyjnego jest dostępne tylko dla systemów operacyjnych Windows Server w wersji 2008, 2008 R2, 2012, 2012 R2 i 2016.
> - Konfiguracja dotyczy wszystkich maszyn wirtualnych i komputerów, które są połączone ze wszystkimi obszarami roboczymi w ramach wybranej subskrypcji.
> - Dostosowanie konfiguracji zabezpieczeń systemu operacyjnego jest dostępne tylko w warstwie Standardowa Security Center.
>
>

Reguły konfiguracji zabezpieczeń systemu operacyjnego można dostosować, włączając i wyłączając określoną regułę, zmieniając odpowiednie ustawienia dla istniejącej reguły lub dodając nową regułę opartą na obsługiwanych typach reguł (rejestr, zasady inspekcji i zasady zabezpieczeń). Obecnie wymagane ustawienie musi być dokładną wartością.

Nowe reguły muszą mieć taki sam format i strukturę jak inne istniejące reguły tego samego typu.

> [!NOTE]
> Aby dostosować konfiguracje zabezpieczeń systemu operacyjnego, musisz mieć przypisaną rolę *właściciela subskrypcji*, współautora *subskrypcji*lub *administratora zabezpieczeń*.
>
>

## <a name="customize-the-default-os-security-configuration"></a>Dostosuj domyślną konfigurację zabezpieczeń systemu operacyjnego

Aby dostosować domyślną konfigurację zabezpieczeń systemu operacyjnego w Security Center, wykonaj następujące czynności:

1.  Otwórz pulpit nawigacyjny usługi **Security Center**.

2.  W okienku po lewej stronie wybierz pozycję **Cennik ustawienia &** .

    ![Lista zasad zabezpieczeń](media/security-center-customize-os-security-config/manual-provision.png)

4. Wybierz odpowiednią subskrypcję i wybierz pozycję **Edytuj konfiguracje zabezpieczeń**.  

    ![Okno "Edytowanie konfiguracji zabezpieczeń"](media/security-center-customize-os-security-config/blade.png)

5. Postępuj zgodnie z instrukcjami, aby pobrać, edytować i przekazać zmodyfikowany plik.

   > [!NOTE]
   > Domyślnie pobierany plik konfiguracji ma format *JSON* . Aby uzyskać instrukcje dotyczące modyfikowania tego pliku, przejdź do [obszaru Dostosowywanie pliku konfiguracji](#customize-the-configuration-file).
   >

6. Aby zatwierdzić zmianę, wybierz pozycję **Zapisz**. W przeciwnym razie zasady nie są przechowywane.

    ![Przycisk Zapisz](media/security-center-customize-os-security-config/save-successfully.png)

   Po pomyślnym zapisaniu pliku konfiguracja zostanie zastosowana do wszystkich maszyn wirtualnych i komputerów, które są połączone z obszarami roboczymi w ramach subskrypcji. Proces zazwyczaj trwa kilka minut, ale może trwać dłużej, w zależności od rozmiaru infrastruktury.

W dowolnym momencie możesz zresetować bieżącą konfigurację zasad do stanu domyślnego. Aby to zrobić, w oknie **Edytowanie reguł konfiguracji zabezpieczeń systemu operacyjnego** wybierz pozycję **Zresetuj**. Potwierdź tę opcję, wybierając pozycję **tak** w oknie podręcznym potwierdzenia.

![Okno potwierdzenia resetowania](media/security-center-customize-os-security-config/edit-alert.png)

## <a name="customize-the-configuration-file"></a>Dostosuj plik konfiguracji

W pliku dostosowania każda obsługiwana wersja systemu operacyjnego ma zestaw reguł, lub reguł. Każdy zestaw reguł ma własną nazwę i unikatowy identyfikator, jak pokazano w następującym przykładzie:

![Nazwa i identyfikator zestawu reguł](media/security-center-customize-os-security-config/custom-file.png)

> [!NOTE]
> Ten przykładowy plik był edytowany w programie Visual Studio, ale można również użyć notatnika, jeśli zainstalowano wtyczkę przeglądarki JSON.
>
>

Podczas edytowania pliku dostosowania można zmodyfikować jedną regułę lub wszystkie z nich. Każdy zestaw reguł zawiera sekcję *reguły* , która jest oddzielona na trzy kategorie: Rejestr, zasady inspekcji i zasady zabezpieczeń, jak pokazano poniżej:

![Trzy kategorie zestawu reguł](media/security-center-customize-os-security-config/rules-section.png)

Każda kategoria ma swój własny zestaw atrybutów. Można zmienić następujące atrybuty:

- **ExpectedValue**: Typ danych pola tego atrybutu musi być zgodny z obsługiwanymi wartościami dla danego *typu reguły*, na przykład:

  - **baselineRegistryRules**: Wartość powinna być zgodna z [regValueType](https://msdn.microsoft.com/library/windows/desktop/ms724884) , który jest zdefiniowany w tej regule.

  - **baselineAuditPolicyRules**: Użyj jednej z następujących wartości ciągu:

    - *Sukces i niepowodzenie*

    - *Prawnego*

  - **baselineSecurityPolicyRules**: Użyj jednej z następujących wartości ciągu:

    - *Nie ma*

    - Lista dozwolonych grup użytkowników, na przykład: *Administratorzy*, *Operatorzy kopii zapasowych*

-   **stan**: Ciąg może zawierać opcje *wyłączone* lub *włączone*. W tej wersji ciąg jest rozróżniana wielkość liter.

Są to jedyne pola, które można skonfigurować. W przypadku naruszenia formatu lub rozmiaru pliku nie będzie można zapisać zmiany. Zostanie wyświetlony komunikat o błędzie z informacją, że należy przekazać prawidłowy plik konfiguracji JSON.

Listę innych potencjalnych błędów można znaleźć w temacie [kody błędów](#error-codes).

Poniższe trzy sekcje zawierają przykłady powyższych reguł. Atrybuty *ExpectedValue* i *State* można zmienić.

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

Niektóre reguły są zduplikowane dla różnych typów systemów operacyjnych. Zduplikowane reguły mają ten sam atrybut *originalId* .

## <a name="create-custom-rules"></a>Tworzenie reguł niestandardowych

Możesz również utworzyć nowe reguły. Przed utworzeniem nowej reguły należy pamiętać o następujących ograniczeniach:

-   Nie można zmienić wersji schematu, *baselineId* i *linii bazowejname* .

-   Nie można usunąć zestawu reguł.

-   Nie można dodać zestawu reguł.

-   Maksymalna dozwolona liczba reguł (łącznie z regułami domyślnymi) to 1000.

Nowe reguły niestandardowe są oznaczone nowym źródłem niestandardowym (! = "Microsoft"). Pole *ruleId* może mieć wartość null lub być puste. Jeśli jest pusty, firma Microsoft generuje ten element. Jeśli nie jest pusty, musi mieć prawidłowy identyfikator GUID, który jest unikatowy dla wszystkich reguł (domyślny i niestandardowy). Przejrzyj następujące ograniczenia dotyczące podstawowych pól:

-   **originalId**: Może mieć wartość null lub być pusty. Jeśli *originalId* nie jest pusty, powinien być PRAWIDŁOWYm identyfikatorem GUID.

-   **cceId**: Może mieć wartość null lub być pusty. Jeśli *cceId* nie jest pusty, musi być unikatowy.

-   **ruletype**: (Wybierz jeden) Registry, AuditPolicy lub SecurityPolicy.

-   **Ważność**: (Wybierz jeden) nieznany, krytyczny, ostrzeżenie lub informacyjny.

-   **analyzeOperation**: Musi być *równa*.

-   **auditPolicyId**: Musi być prawidłowym identyfikatorem GUID.

-   **regValueType**: (Wybierz jeden) int, Long, String lub MultipleString.

> [!NOTE]
> Gałąź musi być *LocalMachine*.
>
>

Przykład nowej reguły niestandardowej:

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

Jeśli przesłany plik konfiguracji jest nieprawidłowy z powodu błędów w wartościach lub formatowaniu, zostanie wyświetlony błąd błędu, na przykład **Akcja zapisywania nie powiodła się**. Możesz pobrać szczegółowy raport błędy. csv, aby skorygować i naprawić błędy przed ponownym przesłaniem poprawionego pliku konfiguracji.

Przykładowy plik błędu:

![Przykład pliku błędu](media/security-center-customize-os-security-config/errors-file.png)

## <a name="error-codes"></a>Kody błędów

Wszystkie potencjalne błędy są wymienione w poniższej tabeli:

| **Error**                                | **Opis**                                                                                                                              |
|------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------|
| BaselineConfigurationSchemaVersionError  | Właściwość *schemaVersion* została znaleziona nieprawidłowa lub pusta. Wartość musi być ustawiona na *{0}* .                                                         |
| BaselineInvalidStringError               | Właściwość *{0}* nie może zawierać znaków  *\\n*.                                                                                                         |
| BaselineNullRuleError                    | Lista reguł konfiguracji linii bazowej zawiera regułę z wartością *null*.                                                                         |
| BaselineRuleCceIdNotUniqueError          | Identyfikator CCE-ID *{0}* nie jest unikatowy.                                                                                                                  |
| BaselineRuleEmptyProperty                | Brak właściwości *{0}* lub jest ona nieprawidłowa.                                                                                                       |
| BaselineRuleIdNotInDefault               | Reguła ma właściwość source *firmy Microsoft* , ale nie została znaleziona w domyślnym zestawie reguł firmy Microsoft.                                                   |
| BaselineRuleIdNotUniqueError             | Identyfikator reguły nie jest unikatowy.                                                                                                                       |
| BaselineRuleInvalidGuid                  | Właściwość *{0}* została znaleziona nieprawidłowa. Wartość nie jest prawidłowym identyfikatorem GUID.                                                                             |
| BaselineRuleInvalidHive                  | Gałąź musi być LocalMachine.                                                                                                                   |
| BaselineRuleNameNotUniqueError           | Nazwa reguły nie jest unikatowa.                                                                                                                 |
| BaselineRuleNotExistInConfigError        | Nie znaleziono reguły w nowej konfiguracji. Nie można usunąć reguły.                                                                     |
| BaselineRuleNotFoundError                | Nie znaleziono reguły w domyślnym zestawie reguł linii bazowej.                                                                                        |
| BaselineRuleNotInPlace                   | Reguła jest zgodna z regułą domyślną z {0} typem i jest wymieniona w {1} liście.                                                                       |
| BaselineRulePropertyTooLong              | Właściwość *{0}* jest za długa. Maksymalna dozwolona Długość: {1}.                                                                                        |
| BaselineRuleRegTypeInvalidError          | Oczekiwana wartość *{0}* jest niezgodna z typem wartości rejestru, który jest zdefiniowany.                                                              |
| BaselineRulesetAdded                     | Zestaw reguł z identyfikatorem *{0}* nie został odnaleziony w konfiguracji domyślnej. Nie można dodać zestawu reguł.                                               |
| BaselineRulesetIdMustBeUnique            | Określony zestaw reguł *{0}* linii bazowej musi być unikatowy.                                                                                           |
| BaselineRulesetNotFound                  | Zestaw reguł o identyfikatorze *{0}* i nazwie *{1}* nie został odnaleziony w podaną konfigurację. Nie można usunąć zestawu reguł.                                |
| BaselineRuleSourceNotMatch               | Reguła o identyfikatorze *{0}* jest już zdefiniowana.                                                                                                       |
| BaselineRuleTypeDoesntMatch              | Domyślny typ reguły to *{0}* .                                                                                                              |
| BaselineRuleTypeDoesntMatchError         | Rzeczywisty typ reguły to *{0}* , ale właściwość *ruletype* ma *{1}* wartość.                                                                          |
| BaselineRuleUnpermittedChangesError      | Tylko właściwości *ExpectedValue* i *State* mogą być zmieniane.                                                                       |
| BaselineTooManyRules                     | Maksymalna liczba dozwolonych reguł dostosowanych {0} to reguły. Podana konfiguracja zawiera {1} reguły, {2} reguły domyślne i {3} dostosowane reguły. |
| ErrorNoConfigurationStatus               | Nie znaleziono stanu konfiguracji. Stan żądanego stanu konfiguracji: *Domyślny* lub *niestandardowy*.                                    |
| ErrorNonEmptyRulesetOnDefault            | Stan konfiguracji to default. Lista *BaselineRulesets* musi mieć wartość null lub być pusta.                                                          |
| ErrorNullRulesetsPropertyOnCustom        | Dany stan konfiguracji jest *niestandardowy* , ale właściwość *baselineRulesets* ma wartość null lub jest pusta.                                             |
| ErrorParsingBaselineConfig               | Dana konfiguracja jest nieprawidłowa. Co najmniej jedna ze zdefiniowanych wartości ma wartość null lub jest nieprawidłowym typem.                                  |
| ErrorParsingIsDefaultProperty            | Dana *{0}* wartość *configurationStatus* jest nieprawidłowa. Wartość może być tylko *Domyślna* lub niestandardowa.                                         |
| InCompatibleViewVersion                  | Wersja *{0}* widoku *nie* jest obsługiwana w tym typie obszaru roboczego.                                                                                   |
| InvalidBaselineConfigurationGeneralError | Dana konfiguracja linii bazowej została znaleziona z co najmniej jednym błędem walidacji typu.                                                          |
| ViewConversionError                      | Widok jest starszą wersją, która jest obsługiwana przez obszar roboczy. Konwersja widoku nie powiodła się: {0}.                                                                 |

Jeśli nie masz wystarczających uprawnień, może zostać wyświetlony ogólny błąd błędu, jak pokazano poniżej:

![Komunikat o błędzie "Zapisywanie akcji nie powiodło się"](media/security-center-customize-os-security-config/general-failure-error.png)

## <a name="next-steps"></a>Następne kroki
W tym artykule opisano sposób dostosowywania ocen konfiguracji zabezpieczeń systemu operacyjnego w programie Security Center. Aby dowiedzieć się więcej na temat reguł konfiguracji i korygowania, zobacz:

- [Security Center wspólnych identyfikatorów konfiguracji i reguł linii bazowej](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335).
- Security Center używa wyliczenia typowej konfiguracji (CCE) do przypisywania unikatowych identyfikatorów do reguł konfiguracji. Aby uzyskać więcej informacji, zobacz [CCE](https://nvd.nist.gov/config/cce/index).
- Aby rozwiązać luki w zabezpieczeniach, gdy konfiguracja systemu operacyjnego nie jest zgodna z zalecanymi regułami konfiguracji zabezpieczeń, zobacz [korygowanie konfiguracji zabezpieczeń](security-center-virtual-machine-protection.md).
