---
title: Tworzenie zasad i zarządzanie nimi w celu wymuszania zgodności
description: Usługa Azure Policy umożliwia wymuszanie standardów, zachowanie zgodności z przepisami, spełnianie wymagań inspekcji, kontrolowanie kosztów, zapewnianie spójności wydajności i zabezpieczeń oraz narzucanie zasad projektowania dla całego przedsiębiorstwa.
author: DCtheGeek
ms.author: dacoulte
ms.date: 02/04/2019
ms.topic: tutorial
ms.service: azure-policy
ms.openlocfilehash: 43d39039d01b22e7d918755e9557d6a1030ef7a8
ms.sourcegitcommit: bd4198a3f2a028f0ce0a63e5f479242f6a98cc04
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/14/2019
ms.locfileid: "72302859"
---
# <a name="tutorial-create-and-manage-policies-to-enforce-compliance"></a>Samouczek: Tworzenie zasad i zarządzanie nimi w celu wymuszenia zgodności

Poznanie sposobu tworzenia zasad i zarządzania nimi na platformie Azure jest ważne w celu zachowania zgodności ze standardami firmy i umowami dotyczącymi poziomu usług. Z tego samouczka dowiesz się, jak za pomocą usługi Azure Policy wykonywać niektóre bardziej typowe zadania związane z tworzeniem i przypisywaniem zasad oraz zarządzaniem nimi w całej organizacji, na przykład:

> [!div class="checklist"]
> - Przypisywanie zasad w celu wymuszania warunku dla zasobów tworzonych w przyszłości
> - Tworzenie i przypisywanie definicji inicjatywy w celu śledzenia zgodności dla wielu zasobów
> - Rozwiązywanie problemu w przypadku niezgodnego lub odrzuconego zasobu
> - Implementowanie nowych zasad w całej organizacji

Przejrzyj artykuły Szybki start, aby dowiedzieć się, jak przypisać zasady w celu identyfikowania bieżącego stanu zgodności istniejących zasobów. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="assign-a-policy"></a>Przypisywanie zasad

Pierwszym krokiem w celu wymuszenia zgodności za pomocą usługi Azure Policy jest przypisanie definicji zasad. Definicja zasad określa, w jakim przypadku zasady zostaną wymuszone oraz jaki efekt przyniosą. W tym przykładzie przypiszesz wbudowaną definicję zasad o nazwie *Require SQL Server Version 12.0* (Wymagaj programu SQL Server w wersji 12.0) w celu wymuszenia warunku określającego, że wszystkie bazy danych programu SQL Server muszą być w wersji 12.0, aby były zgodne.

1. Uruchom usługę Azure Policy w witrynie Azure Portal, klikając pozycję **Wszystkie usługi**, a następnie wyszukując i wybierając opcję **Zasady**.

   ![Wyszukaj zasady w obszarze wszystkie usługi](../media/create-and-manage/search-policy.png)

1. Wybierz pozycję **Przypisania** w lewej części strony usługi Azure Policy. Przypisanie to zasady, które zostały przypisane do określonego zakresu.

   ![Strona przegląd wybierania przypisań z zasad](../media/create-and-manage/select-assignments.png)

1. Wybierz pozycję **Przypisz zasady** w górnej części strony **Zasady — Przypisania**.

   ![Przypisywanie definicji zasad ze strony przypisań](../media/create-and-manage/select-assign-policy.png)

1. Na stronie **Przypisywanie zasad** wybierz **Zakres**, klikając wielokropek i wybierając grupę zarządzania lub subskrypcję. Opcjonalnie możesz wybrać grupę zasobów. Zakres określa, jakie zasoby lub grupy zasobów są wymuszane w ramach przypisania zasad. Następnie kliknij przycisk **Wybierz** w dolnej części strony **Zakres**.

   W tym przykładzie użyto subskrypcji **Contoso**. Twoja subskrypcja będzie inna.

1. Zasoby można wykluczyć na podstawie opcji **Zakres**. **Wykluczenia** są uruchamiane o jeden poziom niżej od poziomu opcji **Zakres**. Pole **Wykluczenia** jest opcjonalne, więc na razie można je pozostawić puste.

1. W polu **Definicja zasad** wybierz wielokropek, aby otworzyć listę dostępnych definicji. Definicje zasad możesz filtrować, wybierając w polu **Typ** wartość *Wbudowany*, aby wyświetlić wszystkie definicje i przeczytać ich opisy.

1. Wybierz definicję zasad **Require SQL Server Version 12.0** (Wymagaj programu SQL Server w wersji 12.0). Jeśli nie możesz znaleźć jej od razu, wpisz ciąg **require sql server** w polu wyszukiwania, a następnie naciśnij klawisz ENTER. Po znalezieniu i wybraniu definicji zasad kliknij przycisk **Wybierz** w dolnej części strony **Dostępne definicje**.

   ![Korzystanie z filtru wyszukiwania do lokalizowania zasad](../media/create-and-manage/select-available-definition.png)

1. Pole **Nazwa przypisania** jest automatycznie wypełniane przy użyciu nazwy wybranych zasad, ale można ją zmienić. Na potrzeby tego przykładu pozostaw nazwę *Require SQL Server Version 12.0* (Wymagaj programu SQL Server w wersji 12.0). Można również dodać opcjonalny **Opis**. Opis zawiera szczegóły dotyczące danego przypisania zasad.
   Pole **Przypisane przez** jest wypełniane automatycznie w zależności od tego, kto jest zalogowany. To pole jest opcjonalne, dzięki czemu można wprowadzić wartości niestandardowe.

1. Pozostaw pole **Utwórz tożsamość zarządzaną** niezaznaczone. To pole _musi_ być zaznaczone, gdy przypisywane zasady lub inicjatywa obejmują zasady z efektem [deployIfNotExists](../concepts/effects.md#deployifnotexists). Ponieważ w przypadku zasad stosowanych na potrzeby tego samouczka tak nie jest, pozostaw to pole puste. Aby uzyskać więcej informacji, zobacz tematy dotyczące [tożsamości zarządzanych](../../../active-directory/managed-identities-azure-resources/overview.md) i [sposobu działania zabezpieczeń w zakresie korygowania](../how-to/remediate-resources.md#how-remediation-security-works).

1. Kliknij przycisk **Przypisz**.

## <a name="implement-a-new-custom-policy"></a>Implementowanie nowych zasad niestandardowych

Teraz, gdy wbudowana definicja zasad została przypisana, możesz wykonywać dalsze działania w ramach usługi Azure Policy. Utwórz nowe zasady niestandardowe w celu ograniczenia kosztów przez zapewnienie, że maszyny wirtualne tworzone w Twoim środowisku nie mogą korzystać z serii G. Dzięki temu za każdym razem, gdy użytkownik w Twojej organizacji spróbuje utworzyć maszynę wirtualną serii G, żądanie zostanie odrzucone.

1. W lewej części strony usługi Azure Policy wybierz opcję **Definicje** w obszarze **Tworzenie**.

   ![Strona definicji w obszarze Grupa autorstwa](../media/create-and-manage/definition-under-authoring.png)

1. Wybierz **+ Definicja zasad** w górnej części strony. Ten przycisk powoduje otwarcie strony **Definicja zasad**.

1. Wprowadź następujące informacje:

   - Grupa zarządzania lub subskrypcji, w której zapisano definicję zasad. Wybierz, używając wielokropka w polu **Lokalizacja definicji**.

     > [!NOTE]
     > Jeśli planujesz zastosowanie tej definicji zasad w wielu subskrypcjach, lokalizacja musi być grupą zarządzania zawierającą subskrypcje, do których zostaną przypisane zasady. To samo dotyczy definicji inicjatywy.

   - Nazwa definicji zasad — *Wymagaj jednostek SKU maszyn wirtualnych mniejszych niż seria G*
   - Opis wskazujący, jaki jest cel tej definicji zasad — *Ta definicja zasad wymusza jednostki SKU mniejsze niż seria G dla wszystkich maszyn wirtualnych tworzonych w tym zakresie, aby ograniczyć koszty.*
   - Wybierz jedną z istniejących opcji (np. _Compute_) lub utwórz nową kategorię dla tej definicji zasad.
   - Skopiuj poniższy kod JSON, a następnie zaktualizuj go zgodnie ze swoimi potrzebami przy użyciu następujących danych:
      - Parametry zasad.
      - Warunki/reguły zasad, w tym przypadku jest to rozmiar jednostki SKU maszyny wirtualnej, który jest równy serii G
      - Efekt zasad, w tym przypadku **Deny**.

   Oto, jak powinien wyglądać kod JSON. Wklej poprawiony kod do witryny Azure Portal.

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

   Aby wyświetlić więcej przykładowych zasad Azure, zobacz [Przykłady dla usługi Azure Policy](../samples/index.md).

1. Wybierz pozycję **Zapisz**.

## <a name="create-a-policy-definition-with-rest-api"></a>Tworzenie definicji zasad za pomocą interfejsu API REST

Można utworzyć zasady za pomocą interfejsu API REST dla definicji Azure Policy. Interfejs API REST umożliwia tworzenie i usuwanie definicji zasad oraz uzyskiwanie informacji o istniejących definicjach. Aby utworzyć definicję zasad, skorzystaj z następującego przykładu:

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.authorization/policydefinitions/{policyDefinitionName}?api-version={api-version}
```

Dołącz treść żądania podobną do poniższego przykładu:

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

Definicję zasad można utworzyć przy użyciu polecenia cmdlet `New-AzPolicyDefinition`.

Aby utworzyć definicję zasad na podstawie pliku, przekaż ścieżkę do tego pliku. W przypadku pliku zewnętrznego skorzystaj z następującego przykładu:

```azurepowershell-interactive
$definition = New-AzPolicyDefinition `
    -Name 'denyCoolTiering' `
    -DisplayName 'Deny cool access tiering for storage' `
    -Policy 'https://raw.githubusercontent.com/Azure/azure-policy-samples/master/samples/Storage/storage-account-access-tier/azurepolicy.rules.json'
```

W przypadku pliku lokalnego skorzystaj z następującego przykładu:

```azurepowershell-interactive
$definition = New-AzPolicyDefinition `
    -Name 'denyCoolTiering' `
    -Description 'Deny cool access tiering for storage' `
    -Policy 'c:\policies\coolAccessTier.json'
```

Aby utworzyć definicję zasad z wbudowaną regułą, skorzystaj z następującego przykładu:

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

Dane wyjściowe są zapisywane w obiekcie `$definition`, który jest używany podczas przypisywania zasad. Poniższy przykład tworzy definicję zasad, która obejmuje parametry:

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

### <a name="view-policy-definitions-with-powershell"></a>Wyświetlanie definicji zasad przy użyciu programu PowerShell

Aby wyświetlić wszystkie definicje zasad w ramach subskrypcji, użyj następującego polecenia:

```azurepowershell-interactive
Get-AzPolicyDefinition
```

Zwraca ono wszystkie dostępne definicje zasad, w tym wbudowane zasady. Poszczególne zasady są zwracane w następującym formacie:

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

## <a name="create-a-policy-definition-with-azure-cli"></a>Tworzenie definicji zasad za pomocą wiersza polecenia platformy Azure

Definicję zasad możesz utworzyć przy użyciu interfejsu wiersza polecenia platformy Azure, korzystając z polecenia policy definition. Aby utworzyć definicję zasad z wbudowaną regułą, skorzystaj z następującego przykładu:

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

### <a name="view-policy-definitions-with-azure-cli"></a>Wyświetlanie definicji zasad przy użyciu interfejsu wiersza polecenia platformy Azure

Aby wyświetlić wszystkie definicje zasad w ramach subskrypcji, użyj następującego polecenia:

```azurecli-interactive
az policy definition list
```

Zwraca ono wszystkie dostępne definicje zasad, w tym wbudowane zasady. Poszczególne zasady są zwracane w następującym formacie:

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

Za pomocą definicji inicjatywy możesz grupować kilka definicji zasad w celu osiągnięcia jednego ogólnego celu. Inicjatywa ocenia zasoby w zakresie przypisania pod kątem zgodności z uwzględnionymi zasadami. Aby uzyskać więcej informacji na temat definicji inicjatyw, zobacz [Omówienie usługi Azure Policy](../overview.md).

### <a name="create-an-initiative-definition"></a>Tworzenie definicji inicjatywy

1. W lewej części strony usługi Azure Policy wybierz opcję **Definicje** w obszarze **Tworzenie**.

   ![Wybierz definicję ze strony definicje](../media/create-and-manage/definition-under-authoring.png)

1. W górnej części strony wybierz pozycję **+ Definicja inicjatywy**. Spowoduje to przejście do formularza **Definicja inicjatywy**.

   ![Strona przeglądu definicji inicjatywy](../media/create-and-manage/initiative-definition.png)

1. Za pomocą wielokropka przy opcji **Lokalizacja definicji** wybierz grupę zarządzania lub subskrypcję, w której definicja ma być przechowywana. Jeśli poprzednia strona dotyczyła tylko jednej grupy zarządzania lub subskrypcji, pole **Lokalizacja definicji** zostanie wypełnione automatycznie.

1. Wprowadź wartości w polach **Nazwa** i **Opis** inicjatywy.

   W tym przykładzie zapewniasz, że zasoby są zgodne z definicjami zasad w zakresie uzyskiwania bezpieczeństwa. Dla tej inicjatywy podaj nazwę **Uzyskiwanie bezpieczeństwa** i opis: **Ta inicjatywa została utworzona w celu obsługi wszystkich definicji zasad skojarzonych z zabezpieczaniem zasobów**.

1. W polu **Kategoria** wybierz jedną z istniejących opcji lub utwórz nową kategorię.

1. Przejrzyj listę **Dostępne definicje** (w lewej części strony**Definicja inicjatywy**) i wybierz definicje zasad, które chcesz dodać do tej inicjatywy. W przypadku inicjatywy **Uzyskiwanie bezpieczeństwa** dodaj następujące definicje wbudowanych zasad, klikając **+** obok informacji o definicji zasad lub klikając wiersz definicji zasad, a następnie opcję **+ Dodaj** na stronie szczegółów:

   - Require SQL Server Version 12.0 (Wymagaj programu SQL Server w wersji 12.0)
   - [Preview]: Monitor unprotected web applications in Security Center.
   - [Preview]: Monitor permissive network across in Security Center.
   - [Preview]: Monitor possible app Whitelisting in Security Center.
   - [Preview]: Monitor unencrypted VM Disks in Security Center.

   Po wybraniu definicji zasad z listy zostanie ona dodana w obszarze **Zasady i parametry**.

   ![Przejrzyj parametry definicji inicjatywy](../media/create-and-manage/initiative-definition-2.png)

1. Jeśli definicja zasad dodawana do inicjatywy zawiera parametry, są one wyświetlane pod nazwą zasad w obszarze **Zasady i parametry**. Pole _Wartość_ może mieć ustawienie „Ustaw wartość” (wartość będzie zakodowana dla wszystkich przypisań tej inicjatywy) lub „Użyj parametru inicjatywy” (wartość będzie ustawiana w trakcie każdego przypisania inicjatywy). Jeśli wybrano pozycję „Ustaw wartość”, menu rozwijane z prawej strony pozycji _Wartości_ umożliwia wprowadzenie lub wybranie odpowiednich wartości. Jeśli wybrano pozycję „Użyj parametru inicjatywy”, zostanie wyświetlona nowa sekcja **Parametry inicjatywy**, umożliwiająca zdefiniowane parametru, który zostanie ustawiony podczas przypisania inicjatywy. Dozwolone wartości tego parametru inicjatywy mogą dodatkowo ograniczać wartości możliwe do ustawienia podczas przypisania inicjatywy.

   ![Zmień parametry definicji inicjatywy z dozwolonych wartości](../media/create-and-manage/initiative-definition-3.png)

   > [!NOTE]
   > W przypadku niektórych parametrów `strongType` listy wartości nie można określić automatycznie. W takich przypadkach z prawej strony wiersza parametru jest wyświetlany symbol wielokropka. Kliknięcie wielokropka spowoduje otwarcie strony „Zakres parametru (&lt;nazwa parametru&gt;)”. Na tej stronie wybierz subskrypcję, która ma zostać użyta do podania opcji wartości. Ten zakres parametru jest używany wyłącznie w trakcie tworzenia definicji inicjatywy i nie ma żadnego wpływu na ocenę zasad lub zakres inicjatywy podczas przypisania.

1. Kliknij przycisk **Save** (Zapisz).

### <a name="assign-an-initiative-definition"></a>Przypisywanie definicji inicjatywy

1. W lewej części strony usługi Azure Policy wybierz opcję **Definicje** w obszarze **Tworzenie**.

1. Zlokalizuj utworzoną wcześniej inicjatywę **Uzyskiwanie bezpieczeństwa** i kliknij ją. Wybierz polecenie **Przypisz** w górnej części strony, aby otworzyć stronę **Uzyskiwanie bezpieczeństwa: Przypisz inicjatywę**.

   ![Przypisz definicję ze strony definicji inicjatywy](../media/create-and-manage/assign-definition.png)

   Możesz również kliknąć prawym przyciskiem myszy wybrany wiersz lub lewym przyciskiem myszy wielokropek na końcu wiersza, aby wyświetlić menu kontekstowe. Następnie wybierz opcję **Przypisz**.

   ![Alternatywne opcje dla inicjatywy](../media/create-and-manage/select-right-click.png)

1. Wypełnij stronę **Uzyskiwanie bezpieczeństwa: Przypisz inicjatywę**, wprowadzając następujące przykładowe informacje. Możesz podać własne informacje.

   - Zakres: grupa zarządzania lub subskrypcja, dla której wcześniej została zapisana inicjatywa, staje się wartością domyślną.
     Można zmienić zakres, aby przypisać inicjatywę do subskrypcji lub grupy zasobów w lokalizacji zapisywania.
   - Wyjątki: skonfiguruj wszystkie zasoby w zakresie, aby zapobiec zastosowaniu wobec nich przypisania inicjatywy.
   - Definicja inicjatywy i Nazwa przypisania: „Uzyskiwanie bezpieczeństwa” (wypełniane wstępnie jako nazwa przypisywanej inicjatywy).
   - Opis: to przypisanie inicjatywy jest dostosowane w celu wymuszenia tej grupy definicji zasad.
   - Przypisane przez: jest wypełniane automatycznie w zależności od tego, kto jest zalogowany. To pole jest opcjonalne, dzięki czemu można wprowadzić wartości niestandardowe.

1. Pozostaw pole **Utwórz tożsamość zarządzaną** niezaznaczone. To pole _musi_ być zaznaczone, gdy przypisywane zasady lub inicjatywa obejmują zasady z efektem [deployIfNotExists](../concepts/effects.md#deployifnotexists). Ponieważ w przypadku zasad stosowanych na potrzeby tego samouczka tak nie jest, pozostaw to pole puste. Aby uzyskać więcej informacji, zobacz tematy dotyczące [tożsamości zarządzanych](../../../active-directory/managed-identities-azure-resources/overview.md) i [sposobu działania zabezpieczeń w zakresie korygowania](../how-to/remediate-resources.md#how-remediation-security-works).

1. Kliknij przycisk **Przypisz**.

## <a name="check-initial-compliance"></a>Sprawdzanie zgodności początkowej

1. Wybierz pozycję **Zgodność** w lewej części strony usługi Azure Policy.

1. Znajdź inicjatywę **Get Secure** . Jej _Stan zgodności_ prawdopodobnie nadal ma wartość **Nie uruchomiono**.
   Kliknij inicjatywę, aby uzyskać szczegółowe informacje na temat postępu przypisania.

   ![Strona zgodności z inicjatywą — nie uruchomiono ocen](../media/create-and-manage/compliance-status-not-started.png)

1. Po ukończeniu przypisania inicjatywy strona zgodności jest aktualizowana — _Stan zgodności_ zmienia wartość na **Zgodne**.

   ![Strona zgodności z inicjatywą — zgodne zasoby](../media/create-and-manage/compliance-status-compliant.png)

1. Kliknięcie dowolnych zasad na stronie zgodności inicjatywy spowoduje otwarcie strony ze szczegółami zgodności z tymi zasadami. Ta strona zawiera szczegółowe informacje dotyczące zgodności na poziomie zasobów.

## <a name="exempt-a-non-compliant-or-denied-resource-using-exclusion"></a>Dopuszczanie niezgodnego lub odrzuconego zasobu przy użyciu wykluczenia

W powyższym przykładzie po przypisaniu definicji zasad wymagającej programu SQL Server w wersji 12.0 próba utworzenia programu SQL Server w innej wersji niż 12.0 zostałaby odrzucona. W tej sekcji opisano rozwiązywanie problemu z odmową żądania utworzenia serwera SQL przez utworzenie wykluczenia pojedynczej grupy zasobów. To wykluczenie zapobiega wymuszeniu zasady (lub inicjatywy) dla danego zasobu.
W poniższym przykładzie dopuszczalna jest dowolna wersja programu SQL Server w pojedynczej grupie zasobów. Wykluczenie można zastosować do subskrypcji, grupy zasobów lub zawęzić je do pojedynczych zasobów.

Wdrożenie uniemożliwione ze względu na przypisane zasady lub inicjatywę można przeglądać w dwóch miejscach:

- W grupie zasobów, do której należy wdrożenie: wybierz pozycję **wdrożenia** w lewej części strony, a następnie kliknij **nazwę wdrożenia** , którego wdrożenie nie powiodło się. Zasób, do którego odmówiono dostępu, jest wyświetlany jako _Zabroniony_. Aby określić zasady lub inicjatywę i przypisanie, które odrzuciły zasób, kliknij przycisk **nie powiodło się. Kliknij tutaj, aby uzyskać szczegółowe informacje — >** na stronie Przegląd wdrożenia. W prawej części strony wyświetli się okno z informacjami o błędzie. W obszarze **Szczegóły błędu** są widoczne identyfikatory GUID powiązanych obiektów zasad.

  ![Odmowa wdrożenia spowodowana przypisaniem zasad](../media/create-and-manage/rg-deployment-denied.png)

- Na stronie Azure Policy: wybierz opcję **Zgodność** w lewej części strony i kliknij zasadę **Require SQL Server Version 12.0** (Wymagaj programu SQL Server w wersji 12.0). Zostanie otwarta strona, na której wartość licznika **Odmów** jest zwiększona. Na karcie **Zdarzenia** można również zobaczyć, kto rozpoczął wdrożenie, które zostało odrzucone przez zasady.

  ![Omówienie zgodności przypisanych zasad](../media/create-and-manage/compliance-overview.png)

W tym przykładzie Trent Baker, jeden z doświadczonych specjalistów firmy Contoso w dziedzinie wirtualizacji, wykonywał swoją pracę. Należy przyznać mu wyjątek, ale nie chcemy, aby serwery SQL w wersji innej niż 12.0 znajdowały się w dowolnej grupie zasobów. Utworzyliśmy nową grupę zasobów, **SQLServers_Excluded** i teraz przypiszemy jej wyjątek od przypisanych zasad.

### <a name="update-assignment-with-exclusion"></a>Aktualizacja przypisania z wykluczeniem

1. W lewej części strony usługi Azure Policy wybierz opcję **Przypisania** w obszarze **Tworzenie**.

1. Przejrzyj wszystkie przypisania zasad i otwórz przypisanie *Require SQL Server Version 12.0* (Wymagaj programu SQL Server w wersji 12.0).

1. Ustaw opcję **Wykluczenie**, klikając wielokropek i wybierając grupę zasobów przeznaczoną do wykluczenia (w tym przykładzie jest to *SQLServers_Excluded*).

   ![Dodawanie wykluczonej grupy zasobów do przypisania zasad](../media/create-and-manage/request-exclusion.png)

   > [!NOTE]
   > W zależności od zasad i ich wpływu wykluczenie może dotyczyć określonych zasobów w grupie zasobów w zakresie przypisania. Ponieważ w tym samouczku użyto funkcji **Odmów**, więc ustawienie wykluczenia dla określonego, istniejącego już zasobu nie miałoby sensu.

1. Kliknij przycisk **Wybierz**, a następnie kliknij pozycję **Zapisz**.

W tej sekcji rozwiązano problem z odmową żądania przez utworzenie wykluczenia pojedynczej grupy zasobów.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie planujesz dalszej pracy z zasobami utworzonymi w tym samouczku, wykonaj poniższe kroki, aby usunąć wszystkie utworzone powyżej przypisania lub definicje:

1. Wybierz pozycję **Definicje** (lub **Przypisania**, jeśli próbujesz usunąć przypisanie) w obszarze **Tworzenie** w lewej części strony usługi Azure Policy.

1. Wyszukaj nowo utworzoną definicję inicjatywy lub zasad (albo przypisanie), którą chcesz usunąć.

1. Kliknij prawym przyciskiem myszy wiersz albo wybierz wielokropek na końcu definicji lub przypisania, a następnie wybierz pozycję **Usuń definicję** (lub **Usuń przypisanie**).

## <a name="next-steps"></a>Następne kroki

W tym samouczku pomyślnie wykonano następujące czynności:

> [!div class="checklist"]
> - Przypisano zasady w celu wymuszania warunku dla zasobów tworzonych w przyszłości
> - Utworzono i przypisano definicję inicjatywy w celu śledzenia zgodności dla wielu zasobów
> - Rozwiązano problem w przypadku niezgodnego lub odrzuconego zasobu
> - Zaimplementowano nowe zasady w całej organizacji

Aby dowiedzieć się więcej o strukturach definicji zasad, zapoznaj się z artykułem:

> [!div class="nextstepaction"]
> [Struktura definicji usługi Azure Policy](../concepts/definition-structure.md)