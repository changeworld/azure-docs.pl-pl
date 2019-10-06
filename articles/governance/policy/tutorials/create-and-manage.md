---
title: Tworzenie zasad i zarządzanie nimi w celu wymuszenia zgodności
description: Użyj Azure Policy, aby wymusić standardy, spełniać wymagania dotyczące zgodności z przepisami i inspekcji, kontrolować koszty, obsługiwać zabezpieczenia i spójność wydajności oraz nakładać zasady projektowania całego przedsiębiorstwa.
author: DCtheGeek
ms.author: dacoulte
ms.date: 02/04/2019
ms.topic: tutorial
ms.service: azure-policy
ms.openlocfilehash: 82c56a01d7f14f1eeae26dfa784f3545552bf6b9
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2019
ms.locfileid: "71981386"
---
# <a name="tutorial-create-and-manage-policies-to-enforce-compliance"></a>Samouczek: Tworzenie zasad i zarządzanie nimi w celu wymuszenia zgodności

Zrozumienie, jak tworzyć zasady i zarządzać nimi na platformie Azure, jest ważne w celu zapewnienia zgodności ze standardami firmy i umowami dotyczącymi poziomu usług. W ramach tego samouczka nauczysz się używać Azure Policy do wykonywania niektórych bardziej typowych zadań związanych z tworzeniem, przypisywaniem i zarządzaniem zasadami w organizacji, takich jak:

> [!div class="checklist"]
> - Przypisywanie zasad w celu wymuszania warunku dla zasobów tworzonych w przyszłości
> - Tworzenie i przypisywanie definicji inicjatywy w celu śledzenia zgodności dla wielu zasobów
> - Rozwiązywanie niezgodnego lub odrzuconego zasobu
> - Implementowanie nowych zasad w całej organizacji

Jeśli chcesz przypisać zasady w celu zidentyfikowania bieżącego stanu zgodności istniejących zasobów, artykuły szybkiego startu dowiesz się, jak to zrobić. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/) .

## <a name="assign-a-policy"></a>Przypisywanie zasad

Pierwszym krokiem w wymuszaniu zgodności z Azure Policy jest przypisanie definicji zasad. Definicja zasad definiuje, w jaki sposób warunek jest wymuszany przez zasady i jaki ma wpływ. W tym przykładzie należy przypisać wbudowaną definicję zasad o nazwie *wymagaj SQL Server wersja 12,0*, aby wymusić, że wszystkie bazy danych SQL Server muszą mieć wartość v 12, aby były zgodne.

1. Uruchom usługę Azure Policy w Azure Portal, klikając pozycję **wszystkie usługi**, a następnie wyszukując i wybierając pozycję **zasady**.

   ![Wyszukaj zasady w obszarze wszystkie usługi](../media/create-and-manage/search-policy.png)

1. Wybierz pozycję **przypisania** w lewej części strony Azure Policy. Przypisanie to zasady, które zostały przypisane w określonym zakresie.

   ![Strona przegląd wybierania przypisań z zasad](../media/create-and-manage/select-assignments.png)

1. Wybierz pozycję **Przypisz zasady** w górnej części strony **przydziały zasad** .

   ![Przypisywanie definicji zasad ze strony przypisań](../media/create-and-manage/select-assign-policy.png)

1. Na stronie **przypisywanie zasad** wybierz **zakres** , klikając wielokropek i wybierając grupę zarządzania lub subskrypcję. Opcjonalnie wybierz grupę zasobów. Zakres określa, jakie zasoby lub grupy zasobów są wymuszane w ramach przypisania zasad. Następnie kliknij pozycję **Wybierz** w dolnej części strony **zakres** .

   W tym przykładzie zastosowano subskrypcję **contoso** . Twoja subskrypcja będzie się różnić.

1. Zasoby mogą być wykluczone w oparciu o **zakres**. **Wykluczenia** zaczynają się na poziomie niższym niż poziom **zakresu**. **Wykluczenia** są opcjonalne, więc pozostaw je puste.

1. Wybierz wielokropek **definicji zasad** , aby otworzyć listę dostępnych definicji. Możesz filtrować **Typ** definicji zasad, aby *wbudowany* , wyświetlić wszystkie i przeczytać ich opisy.

1. Wybierz pozycję **wymagaj SQL Server wersja 12,0**. Jeśli nie możesz znaleźć go od razu, wpisz w polu wyszukiwania pozycję **Wymagaj programu SQL Server** , a następnie naciśnij klawisz ENTER lub kliknij pole wyszukiwania. Po znalezieniu i wybraniu definicji zasad kliknij pozycję **Wybierz** w dolnej części strony **dostępne definicje** .

   ![Korzystanie z filtru wyszukiwania do lokalizowania zasad](../media/create-and-manage/select-available-definition.png)

1. **Nazwa przypisania** zostanie automatycznie wypełniona przy użyciu wybranej nazwy zasad, ale można ją zmienić. Na potrzeby tego przykładu pozostaw *wymagane SQL Server wersja 12,0*. Możesz również dodać opcjonalny **Opis**. Opis zawiera szczegółowe informacje o tym przypisaniu zasad.
   **Przypisane przez** jest automatycznie wypełniane na podstawie tego, kto jest zalogowany. To pole jest opcjonalne, dlatego można wprowadzać wartości niestandardowe.

1. Nie zaznaczaj opcji Utwórz niesprawdzoną **tożsamość zarządzaną** . To pole _należy_ zaznaczyć, gdy zasada lub przypisana inicjatywa zawiera zasady z efektem [deployIfNotExists](../concepts/effects.md#deployifnotexists) . Ponieważ zasady użyte w tym samouczku nie są, pozostaw puste. Aby uzyskać więcej informacji, zobacz [zarządzane tożsamości](../../../active-directory/managed-identities-azure-resources/overview.md) i [sposób działania zabezpieczeń korygowania](../how-to/remediate-resources.md#how-remediation-security-works).

1. Kliknij przycisk **Przypisz**.

## <a name="implement-a-new-custom-policy"></a>Implementowanie nowych zasad niestandardowych

Teraz, po przypisaniu wbudowanej definicji zasad, możesz wykonać więcej czynności w Azure Policy. Następnie utwórz nowe zasady niestandardowe, aby zmniejszyć koszty, sprawdzając, czy maszyny wirtualne utworzone w środowisku nie mogą znajdować się w serii G. W ten sposób, za każdym razem, gdy użytkownik w organizacji próbuje utworzyć maszynę wirtualną w serii G, żądanie zostanie odrzucone.

1. Wybierz pozycję **definicje** w obszarze **Tworzenie** po lewej stronie Azure Policy.

   ![Strona definicji w obszarze Grupa autorstwa](../media/create-and-manage/definition-under-authoring.png)

1. Wybierz pozycję **+ Definicja zasad** w górnej części strony. Ten przycisk zostanie otwarty na stronie **definicji zasad** .

1. Wprowadź następujące informacje:

   - Grupa zarządzania lub subskrypcja, w której zapisano definicję zasad. Wybierz przy użyciu wielokropka w **lokalizacji definicji**.

     > [!NOTE]
     > Jeśli zamierzasz zastosować tę definicję zasad do wielu subskrypcji, lokalizacja musi być grupą zarządzania, która zawiera subskrypcje, do których przypisano zasady. Ta sama wartość dotyczy definicji inicjatywy.

   - Nazwa definicji zasad — *wymagane jednostki SKU maszyny wirtualnej są mniejsze niż Seria G*
   - Opis definicji zasad przeznaczonych do wykonania — *Ta definicja zasad wymusza, że wszystkie maszyny wirtualne utworzone w tym zakresie mają jednostki SKU mniejsze niż Seria G, aby zmniejszyć koszty.*
   - Wybierz spośród istniejących opcji (takich jak _obliczenia_) lub Utwórz nową kategorię dla tej definicji zasad.
   - Skopiuj poniższy kod JSON, a następnie zaktualizuj go do swoich potrzeb przy użyciu:
      - Parametry zasad.
      - Reguły/warunki zasad, w tym przypadku — rozmiar jednostki SKU maszyny wirtualnej równy serii G
      - Efekt zasad, w tym przypadku — **Odmów**.

   Oto, jak powinien wyglądać kod JSON. Wklej zmieniony kod do Azure Portal.

   ```json
   {
       "policyRule": {
           "if": {
               "allOf": [{
                       "field": "type",
                       "equals": "Microsoft.Compute/virtualMachines"
                   },
                   {
                       "field": "Microsoft.Compute/virtualMachines/sku.name",
                       "like": "Standard_G*"
                   }
               ]
           },
           "then": {
               "effect": "deny"
           }
       }
   }
   ```

   Właściwość *Field* w regule zasad musi mieć jedną z następujących wartości: Name, Type, Location, Tags lub alias. Przykładem aliasu może być `"Microsoft.Compute/VirtualMachines/Size"`.

   Aby wyświetlić więcej przykładów dotyczących zasad platformy Azure, zobacz [Azure Policy Samples](../samples/index.md).

1. Wybierz pozycję **Zapisz**.

## <a name="create-a-policy-definition-with-rest-api"></a>Tworzenie definicji zasad za pomocą interfejsu API REST

Można utworzyć zasady za pomocą interfejsu API REST dla definicji Azure Policy. Interfejs API REST umożliwia tworzenie i usuwanie definicji zasad oraz uzyskiwanie informacji o istniejących definicjach. Aby utworzyć definicję zasad, należy użyć następującego przykładu:

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.authorization/policydefinitions/{policyDefinitionName}?api-version={api-version}
```

Uwzględnij treść żądania podobną do poniższego przykładu:

```json
{
    "properties": {
        "parameters": {
            "allowedLocations": {
                "type": "array",
                "metadata": {
                    "description": "The list of locations that can be specified when deploying resources",
                    "strongType": "location",
                    "displayName": "Allowed locations"
                }
            }
        },
        "displayName": "Allowed locations",
        "description": "This policy enables you to restrict the locations your organization can specify when deploying resources.",
        "policyRule": {
            "if": {
                "not": {
                    "field": "location",
                    "in": "[parameters('allowedLocations')]"
                }
            },
            "then": {
                "effect": "deny"
            }
        }
    }
}
```

## <a name="create-a-policy-definition-with-powershell"></a>Tworzenie definicji zasad za pomocą programu PowerShell

Przed kontynuowaniem pracy z przykładem programu PowerShell upewnij się, że zainstalowano najnowszą wersję Azure PowerShell AZ module.

Definicję zasad można utworzyć za pomocą polecenia cmdlet `New-AzPolicyDefinition`.

Aby utworzyć definicję zasad z pliku, Przekaż ścieżkę do pliku. W przypadku pliku zewnętrznego należy użyć następującego przykładu:

```azurepowershell-interactive
$definition = New-AzPolicyDefinition `
    -Name 'denyCoolTiering' `
    -DisplayName 'Deny cool access tiering for storage' `
    -Policy 'https://raw.githubusercontent.com/Azure/azure-policy-samples/master/samples/Storage/storage-account-access-tier/azurepolicy.rules.json'
```

W przypadku lokalnego użycia plików należy użyć następującego przykładu:

```azurepowershell-interactive
$definition = New-AzPolicyDefinition `
    -Name 'denyCoolTiering' `
    -Description 'Deny cool access tiering for storage' `
    -Policy 'c:\policies\coolAccessTier.json'
```

Aby utworzyć definicję zasad z wbudowaną regułą, użyj poniższego przykładu:

```azurepowershell-interactive
$definition = New-AzPolicyDefinition -Name 'denyCoolTiering' -Description 'Deny cool access tiering for storage' -Policy '{
    "if": {
        "allOf": [{
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
            },
            {
                "field": "kind",
                "equals": "BlobStorage"
            },
            {
                "field": "Microsoft.Storage/storageAccounts/accessTier",
                "equals": "cool"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}'
```

Dane wyjściowe są przechowywane w obiekcie `$definition`, który jest używany podczas przypisywania zasad. Poniższy przykład tworzy definicję zasad, która zawiera parametry:

```azurepowershell-interactive
$policy = '{
    "if": {
        "allOf": [{
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
            },
            {
                "not": {
                    "field": "location",
                    "in": "[parameters(''allowedLocations'')]"
                }
            }
        ]
    },
    "then": {
        "effect": "Deny"
    }
}'

$parameters = '{
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of locations that can be specified when deploying storage accounts.",
            "strongType": "location",
            "displayName": "Allowed locations"
        }
    }
}'

$definition = New-AzPolicyDefinition -Name 'storageLocations' -Description 'Policy to specify locations for storage accounts.' -Policy $policy -Parameter $parameters
```

### <a name="view-policy-definitions-with-powershell"></a>Wyświetlanie definicji zasad za pomocą programu PowerShell

Aby wyświetlić wszystkie definicje zasad w ramach subskrypcji, użyj następującego polecenia:

```azurepowershell-interactive
Get-AzPolicyDefinition
```

Zwraca wszystkie dostępne definicje zasad, w tym wbudowane zasady. Każda zasada jest zwracana w następującym formacie:

```output
Name               : e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceId         : /providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceName       : e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceType       : Microsoft.Authorization/policyDefinitions
Properties         : @{displayName=Allowed locations; policyType=BuiltIn; description=This policy enables you to
                     restrict the locations your organization can specify when deploying resources. Use to enforce
                     your geo-compliance requirements.; parameters=; policyRule=}
PolicyDefinitionId : /providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c
```

## <a name="create-a-policy-definition-with-azure-cli"></a>Tworzenie definicji zasad za pomocą interfejsu wiersza polecenia platformy Azure

Definicję zasad można utworzyć przy użyciu interfejsu wiersza polecenia platformy Azure z definicją zasad. Aby utworzyć definicję zasad z wbudowaną regułą, użyj poniższego przykładu:

```azurecli-interactive
az policy definition create --name 'denyCoolTiering' --description 'Deny cool access tiering for storage' --rules '{
    "if": {
        "allOf": [{
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
            },
            {
                "field": "kind",
                "equals": "BlobStorage"
            },
            {
                "field": "Microsoft.Storage/storageAccounts/accessTier",
                "equals": "cool"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}'
```

### <a name="view-policy-definitions-with-azure-cli"></a>Wyświetlanie definicji zasad za pomocą interfejsu wiersza polecenia platformy Azure

Aby wyświetlić wszystkie definicje zasad w ramach subskrypcji, użyj następującego polecenia:

```azurecli-interactive
az policy definition list
```

Zwraca wszystkie dostępne definicje zasad, w tym wbudowane zasady. Każda zasada jest zwracana w następującym formacie:

```json
{
    "description": "This policy enables you to restrict the locations your organization can specify when deploying resources. Use to enforce your geo-compliance requirements.",
    "displayName": "Allowed locations",
    "id": "/providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c",
    "name": "e56962a6-4747-49cd-b67b-bf8b01975c4c",
    "policyRule": {
        "if": {
            "not": {
                "field": "location",
                "in": "[parameters('listOfAllowedLocations')]"
            }
        },
        "then": {
            "effect": "Deny"
        }
    },
    "policyType": "BuiltIn"
}
```

## <a name="create-and-assign-an-initiative-definition"></a>Tworzenie i przypisywanie definicji inicjatywy

Za pomocą definicji inicjatywy można grupować kilka definicji zasad w celu osiągnięcia jednego przełożonego celu. Inicjatywa szacuje zasoby w zakresie przypisania pod kątem zgodności z uwzględnionymi zasadami. Aby uzyskać więcej informacji na temat definicji inicjatyw, zobacz [Azure Policy Omówienie](../overview.md).

### <a name="create-an-initiative-definition"></a>Tworzenie definicji inicjatywy

1. Wybierz pozycję **definicje** w obszarze **Tworzenie** po lewej stronie Azure Policy.

   ![Wybierz definicję ze strony definicje](../media/create-and-manage/definition-under-authoring.png)

1. Wybierz pozycję **+ Definicja inicjatywy** w górnej części strony, aby otworzyć stronę **definicji inicjatywy** .

   ![Strona przeglądu definicji inicjatywy](../media/create-and-manage/initiative-definition.png)

1. Użyj wielokropka **lokalizacji definicji** , aby wybrać grupę zarządzania lub subskrypcję do przechowywania definicji. Jeśli poprzednia strona została objęta zakresem pojedynczej grupy zarządzania lub subskrypcji, **Lokalizacja definicji** jest automatycznie wypełniana.

1. Wprowadź **nazwę** i **Opis** inicjatywy.

   Ten przykład sprawdza, czy zasoby są zgodne z definicjami zasad dotyczącymi uzyskiwania bezpiecznego. Nadaj mu nazwę **Initiative i** Ustaw opis: **Ta inicjatywa została utworzona w celu obsługi wszystkich definicji zasad skojarzonych z zabezpieczaniem zasobów**.

1. W obszarze **Kategoria**wybierz opcję z istniejących opcji lub Utwórz nową kategorię.

1. Przejrzyj listę **dostępnych definicji** (prawym połowa strony **definicji inicjatywy** ) i wybierz definicje zasad, które chcesz dodać do tej inicjatywy. W przypadku inicjatywy **Get Secure** należy dodać następujące wbudowane definicje zasad, klikając **+** obok informacji o definicji zasad lub klikając wiersz definicji zasad, a następnie opcję **+ Dodaj** na stronie szczegółów:

   - Wymagaj SQL Server w wersji 12,0
   - [Preview]: Monitor unprotected web applications in Security Center.
   - [Preview]: Monitor permissive network across in Security Center.
   - [Preview]: Monitor possible app Whitelisting in Security Center.
   - [Preview]: Monitor unencrypted VM Disks in Security Center.

   Po wybraniu definicji zasad z listy zostanie ona dodana w obszarze **zasady i parametry**.

   ![Przejrzyj parametry definicji inicjatywy](../media/create-and-manage/initiative-definition-2.png)

1. Jeśli definicja zasad dodawana do inicjatywy ma parametry, są one wyświetlane pod nazwą zasad w obszarze **zasady i parametry** . Można ustawić _wartość_ "Ustaw wartość" (stałe kodowane dla wszystkich przypisań tej inicjatywy) lub parametr "Use Initiative" (ustawiana podczas każdego przypisania inicjatywy). Jeśli wybrano opcję "Ustaw wartość", lista rozwijana z prawej strony _wartości_ umożliwia wprowadzanie lub wybieranie wartości. Jeśli wybrano opcję Użyj parametru inicjatywy, zostanie wyświetlona nowa sekcja **parametrów inicjatywy** umożliwiająca zdefiniowanie parametru, który jest ustawiany podczas przypisywania inicjatywy. Dozwolone wartości w tym parametrze inicjatywy mogą bardziej ograniczyć to, co można ustawić podczas przypisywania inicjatywy.

   ![Zmień parametry definicji inicjatywy z dozwolonych wartości](../media/create-and-manage/initiative-definition-3.png)

   > [!NOTE]
   > W przypadku niektórych parametrów `strongType` nie można automatycznie ustalić listy wartości. W takich przypadkach wielokropek pojawia się po prawej stronie wiersza parametru. Kliknięcie spowoduje otwarcie strony "zakres parametrów (&lt;parameter Name @ no__t-1)". Na tej stronie wybierz subskrypcję, która ma być używana do udostępniania opcji wartości. Ten zakres parametrów jest używany tylko podczas tworzenia definicji inicjatywy i nie ma wpływu na ocenę zasad ani zakres tej inicjatywy po przypisaniu.

1. Kliknij przycisk **Zapisz**.

### <a name="assign-an-initiative-definition"></a>Przypisywanie definicji inicjatywy

1. Wybierz pozycję **definicje** w obszarze **Tworzenie** po lewej stronie Azure Policy.

1. Znajdź utworzoną wcześniej definicję z inicjatywy **Get Secure** Initiative i kliknij ją. Wybierz pozycję **Przypisz** w górnej części strony, aby otworzyć stronę na stronie **Uzyskaj bezpieczną: przypisywanie inicjatywy** .

   ![Przypisz definicję ze strony definicji inicjatywy](../media/create-and-manage/assign-definition.png)

   Możesz również kliknąć prawym przyciskiem myszy wybrany wiersz lub kliknąć lewym przyciskiem na końcu wiersza w menu kontekstowym. Następnie wybierz pozycję **Przypisz**.

   ![Alternatywne opcje dla inicjatywy](../media/create-and-manage/select-right-click.png)

1. Wypełnij stronę **Uzyskaj bezpieczną: przypisywanie inicjatywy** , wprowadzając poniższe informacje. Możesz użyć własnych informacji.

   - Zakres: Grupa zarządzania lub subskrypcja, której inicjatywa została zapisana, ma stać się wartością domyślną.
     Można zmienić zakres, aby przypisać inicjatywę do subskrypcji lub grupy zasobów w lokalizacji zapisywania.
   - Wykluczenia: Skonfiguruj wszelkie zasoby w zakresie, aby zapobiec zastosowaniu do nich przypisania inicjatywy.
   - Definicja inicjatywy i nazwa przypisania: Uzyskaj Secure (wstępnie wypełniony jako nazwa przypisanej inicjatywy).
   - Opis: to przypisanie inicjatywy jest dostosowane do wymuszania tej grupy definicji zasad.
   - Przypisane przez: automatycznie wypełniane na podstawie tego, kto jest zalogowany. To pole jest opcjonalne, dlatego można wprowadzać wartości niestandardowe.

1. Nie zaznaczaj opcji Utwórz niesprawdzoną **tożsamość zarządzaną** . To pole _należy_ zaznaczyć, gdy zasada lub przypisana inicjatywa zawiera zasady z efektem [deployIfNotExists](../concepts/effects.md#deployifnotexists) . Ponieważ zasady użyte w tym samouczku nie są, pozostaw puste. Aby uzyskać więcej informacji, zobacz [zarządzane tożsamości](../../../active-directory/managed-identities-azure-resources/overview.md) i [sposób działania zabezpieczeń korygowania](../how-to/remediate-resources.md#how-remediation-security-works).

1. Kliknij przycisk **Przypisz**.

## <a name="check-initial-compliance"></a>Sprawdź wstępną zgodność

1. Wybierz pozycję **zgodność** w lewej części strony Azure Policy.

1. Znajdź inicjatywę **Pobierz Źródło** . Prawdopodobnie nadal jest w _stanie zgodności_ **nie został uruchomiony**.
   Kliknij inicjatywę, aby uzyskać pełne szczegóły postępu przypisywania.

   ![Strona zgodności z inicjatywą — nie uruchomiono ocen](../media/create-and-manage/compliance-status-not-started.png)

1. Po zakończeniu przypisania inicjatywy Strona zgodność zostanie zaktualizowana o _stan zgodności_ **zgodny**.

   ![Strona zgodności z inicjatywą — zgodne zasoby](../media/create-and-manage/compliance-status-compliant.png)

1. Kliknięcie jakichkolwiek zasad na stronie Zgodność z inicjatywą powoduje otwarcie strony Szczegóły zgodności dla zasad. Ta strona zawiera szczegółowe informacje na temat poziomu zasobów pod kątem zgodności.

## <a name="exempt-a-non-compliant-or-denied-resource-using-exclusion"></a>Zwolnij niezgodny lub zabroniony zasób przy użyciu wykluczenia

Zgodnie z powyższym przykładem, po przypisaniu definicji zasad w celu wymagania programu SQL Server w wersji 12,0, zostanie odmowa dostępu do programu SQL Server utworzonego przy użyciu dowolnej wersji 12,0. W tej sekcji opisano sposób rozwiązywania odrzuconego żądania utworzenia programu SQL Server przez utworzenie wykluczenia dla pojedynczej grupy zasobów. Wykluczenie uniemożliwia wymuszanie zasad (lub inicjatywy) dla tego zasobu.
W poniższym przykładzie dowolna wersja programu SQL Server jest dozwolona w jednej grupie zasobów. Wykluczenie może dotyczyć subskrypcji, grupy zasobów lub można zawęzić wykluczenia do poszczególnych zasobów.

Wdrożenie uniemożliwiło przypisaną zasadę lub inicjatywę można wyświetlić w dwóch lokalizacjach:

- W grupie zasobów, do której należy wdrożenie: wybierz pozycję **wdrożenia** w lewej części strony, a następnie kliknij **nazwę wdrożenia** , którego wdrożenie nie powiodło się. Odmowa zasobu jest wyświetlana z _niedozwolonym_stanem. Aby określić zasady lub inicjatywę i przypisanie, które odrzuciły zasób, kliknij przycisk **nie powiodło się. Kliknij tutaj, aby uzyskać szczegółowe informacje — >** na stronie Przegląd wdrożenia. Po prawej stronie strony zostanie otwarte okno z informacjami o błędzie. W obszarze **szczegóły błędu** są identyfikatory GUID powiązanych obiektów zasad.

  ![Wdrożenie odrzucone przez przypisanie zasad](../media/create-and-manage/rg-deployment-denied.png)

- Na stronie Azure Policy: wybierz pozycję **zgodność** po lewej stronie i kliknij pozycję **Wymagaj zasad SQL Server w wersji 12,0** . Na stronie, która zostanie otwarta, zobaczysz wzrost liczby **Odmów** . Na karcie **zdarzenia** zobaczysz również, kto próbował wykonać wdrożenie odrzucone przez zasady.

  ![Przegląd zgodności przypisanych zasad](../media/create-and-manage/compliance-overview.png)

W tym przykładzie Trent Baker, jedna z specjalistów wirtualizacji SR firmy Contoso. Musimy udzielić Trent wyjątku, ale nie chcemy, aby serwery SQL w wersji 12,0 nie były w tej samej grupie zasobów. Utworzyliśmy nową grupę zasobów **SQLServers_Excluded** i przyznamy jej wyjątek dla tego przypisania zasad.

### <a name="update-assignment-with-exclusion"></a>Aktualizowanie przypisania z wykluczeniem

1. Wybierz pozycję **przypisania** w obszarze **Tworzenie** po lewej stronie Azure Policy.

1. Przejrzyj wszystkie przypisania zasad i Otwórz przypisanie *wymagaj SQL Server w wersji 12,0* .

1. Aby określić **wykluczenie** , kliknij wielokropek i wybierz grupę zasobów do wykluczenia, *SQLServers_Excluded* w tym przykładzie.

   ![Dodawanie wykluczonej grupy zasobów do przypisania zasad](../media/create-and-manage/request-exclusion.png)

   > [!NOTE]
   > W zależności od zasad i jego działania wykluczenia można także udzielić określonym zasobom w grupie zasobów w ramach zakresu przydziału. Ponieważ w tym samouczku użyto efektu **odmowy** , nie ma sensu ustawienia wykluczenia dla określonego zasobu, który już istnieje.

1. Kliknij przycisk **Wybierz** , a następnie kliknij przycisk **Zapisz**.

W tej sekcji rozwiązano żądanie odrzucone przez utworzenie wykluczenia dla pojedynczej grupy zasobów.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli wykonujesz pracę z zasobami z tego samouczka, wykonaj następujące kroki, aby usunąć wszystkie utworzone powyżej przypisania lub definicje:

1. Wybierz pozycję **definicje** (lub **przypisania** , jeśli próbujesz usunąć przypisanie) w obszarze **tworzenie** w lewej części strony Azure Policy.

1. Wyszukaj nową inicjatywę lub definicję zasad (lub przypisanie), które chcesz usunąć.

1. Kliknij prawym przyciskiem myszy wiersz lub wybierz wielokropek na końcu definicji (lub przypisania), a następnie wybierz pozycję **Usuń definicję** (lub **Usuń przypisanie**).

## <a name="next-steps"></a>Następne kroki

W tym samouczku pomyślnie wykonano następujące zadania:

> [!div class="checklist"]
> - Przypisano zasady wymuszania warunku dla zasobów tworzonych w przyszłości
> - Tworzenie i przypisywanie definicji inicjatywy w celu śledzenia zgodności dla wielu zasobów
> - Rozwiązano niezgodny lub zabroniony zasób
> - Wdrożono nowe zasady w całej organizacji

Aby dowiedzieć się więcej o strukturach definicji zasad, zapoznaj się z tym artykułem:

> [!div class="nextstepaction"]
> [Struktura definicji Azure Policy](../concepts/definition-structure.md)