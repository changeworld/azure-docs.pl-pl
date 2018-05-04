---
title: Tworzenie zasad i zarządzanie nimi za pomocą usługi Azure Policy w celu wymuszania zgodności organizacyjnej | Microsoft Docs
description: Usługa Azure Policy umożliwia wymuszanie standardów, zachowanie zgodności z przepisami, spełnianie wymagań inspekcji, kontrolowanie kosztów, zapewnianie spójności wydajności i zabezpieczeń oraz narzucanie zasad projektowania dla całego przedsiębiorstwa.
services: azure-policy
keywords: ''
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/19/2018
ms.topic: tutorial
ms.service: azure-policy
ms.custom: mvc
ms.openlocfilehash: 0b6e77d080ce4e4483709b9a5e47dca21c22e1d9
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="create-and-manage-policies-to-enforce-compliance"></a>Tworzenie zasad i zarządzanie nimi w celu wymuszania zgodności

Poznanie sposobu tworzenia zasad i zarządzania nimi na platformie Azure jest ważne w celu zachowania zgodności ze standardami firmy i umowami dotyczącymi poziomu usług. W ramach tego samouczka dowiesz się, jak za pomocą usługi Azure Policy wykonywać niektóre bardziej typowe zadania związane z tworzeniem i przypisywaniem zasad oraz zarządzaniem nimi w całej organizacji, np.:

> [!div class="checklist"]
> * Przypisywanie zasad w celu wymuszania warunku dla zasobów tworzonych w przyszłości
> * Tworzenie i przypisywanie definicji inicjatywy w celu śledzenia zgodności dla wielu zasobów
> * Rozwiązywanie problemu w przypadku niezgodnego lub odrzuconego zasobu
> * Implementowanie nowych zasad w całej organizacji

Przejrzyj artykuły Szybki start, aby dowiedzieć się, jak przypisać zasady w celu identyfikowania bieżącego stanu zgodności istniejących zasobów. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="assign-a-policy"></a>Przypisywanie zasad

Pierwszym krokiem w celu wymuszenia zgodności za pomocą usługi Azure Policy jest przypisanie definicji zasad. Definicja zasad określa, w jakim przypadku zasady zostaną wymuszone oraz jaka akcja zostanie podjęta. W tym przykładzie przypiszesz wbudowaną definicję zasad o nazwie *Require SQL Server Version 12.0* (Wymagaj programu SQL Server w wersji 12.0) w celu wymuszenia warunku określającego, że wszystkie bazy danych programu SQL Server muszą być w wersji 12.0, aby były zgodne.

1. Uruchom usługę Azure Policy w witrynie Azure Portal, wyszukując ciąg **Policy** w okienku po lewej stronie i wybierając odpowiednią pozycję.

   ![Wyszukiwanie zasad](media/assign-policy-definition/search-policy.png)

2. Wybierz pozycję **Przypisania** w lewym okienku na stronie Azure Policy. Przypisanie to zasady, które zostały przypisane do określonego zakresu.
3. Wybierz pozycję **Przypisz zasady** w górnej części okienka **Przypisania**.

   ![Przypisywanie definicji zasad](media/create-manage-policy/select-assign-policy.png)

4. Na stronie **Przypisz zasady** kliknij ![przycisk definicji zasad](media/assign-policy-definition/definitions-button.png) obok pola **Zasady**, aby otworzyć listę dostępnych definicji. Definicje zasad możesz filtrować, wybierając w polu **Typ** wartość *BuiltIn*, aby wyświetlić wszystkie definicje i przeczytać ich opisy.

   ![Otwieranie dostępnych definicji zasad](media/create-manage-policy/open-policy-definitions.png)

5. Wybierz definicję zasad **Require SQL Server Version 12.0** (Wymagaj programu SQL Server w wersji 12.0). Jeśli nie możesz znaleźć jej od razu, wpisz ciąg **Require SQL Server Version 12.0** w polu wyszukiwania, a następnie naciśnij klawisz ENTER.

   ![Lokalizowanie zasad](media/create-manage-policy/select-available-definition.png)

6. Wyświetlana **Nazwa** zostanie wypełniona automatycznie, ale można ją zmienić. Na potrzeby tego przykładu podaj nazwę *Require SQL Server Version 12.0* (Wymagaj programu SQL Server w wersji 12.0). Można również dodać opcjonalny **Opis**. Opis zawiera szczegóły dotyczące sposobu, w jaki przypisanie zasad zapewnia, że wszystkie programy SQL Server tworzone w tym środowisku mają wersję 12.0.

7. Zmień warstwę cenową na **Standardowa**, aby upewnić się, że zasady zostaną zastosowane do istniejących zasobów.

   W ramach usługi Azure Policy dostępne są dwie warstwy cenowe — *Bezpłatna* i *Standardowa*. W warstwie Bezpłatna można wprowadzać zasady tylko dla przyszłych zasobów, podczas gdy w warstwie Standardowa można stosować je także do istniejących zasobów, co zapewnia lepsze zrozumienie stanu zgodności z przepisami. Usługa Azure Policy jest dostępna w wersji zapoznawczej, dlatego nie wprowadzono jeszcze modelu cenowego. W związku z tym nie otrzymasz rachunku za wybranie warstwy *Standardowa*. Aby dowiedzieć się więcej o cenach, zobacz: [Cennik usługi Azure Policy](https://azure.microsoft.com/pricing/details/azure-policy).

8. Wybierz **Zakres** — wcześniej zarejestrowaną subskrypcję (lub grupę zasobów). Zakres określa, jakie zasoby lub grupy zasobów są wymuszane w ramach przypisania zasad. Może obejmować zarówno subskrypcje, jak i grupy zasobów.

   W tym przykładzie użyto subskrypcji **Azure Analytics Capacity Dev**. Twoja subskrypcja będzie inna.

10. Wybierz opcję **Przypisz**.

## <a name="implement-a-new-custom-policy"></a>Implementowanie nowych zasad niestandardowych

Teraz, gdy wbudowana definicja zasad została przypisana, możesz wykonywać dalsze działania w ramach usługi Azure Policy. Utwórz nowe zasady niestandardowe w celu ograniczenia kosztów przez zapewnienie, że maszyny wirtualne tworzone w Twoim środowisku nie mogą korzystać z serii G. Dzięki temu za każdym razem, gdy użytkownik w Twojej organizacji spróbuje utworzyć maszynę wirtualną serii G, żądanie zostanie odrzucone.

1. W okienku po lewej stronie wybierz pozycję **Definicja** w obszarze **Tworzenie**.

   ![Pozycja Definicja w obszarze Tworzenie](media/create-manage-policy/definition-under-authoring.png)

2. Wybierz pozycję **+ Definicja zasad**.
3. Wprowadź następujące dane:

   - Nazwa definicji zasad — *Wymagaj jednostek SKU maszyn wirtualnych mniejszych niż seria G*
   - Opis wskazujący, jaki jest cel tej definicji zasad — Ta definicja zasad wymusza jednostki SKU mniejsze niż seria G dla wszystkich maszyn wirtualnych tworzonych w tym zakresie, aby zredukować koszt.
   - Subskrypcja, w której znajduje się definicja zasad. W tym przypadku definicja zasad znajduje się w subskrypcji **Advisor Analytics Capacity Dev**. Twoja lista subskrypcji będzie inna.
   - Wybierz jedną z istniejących opcji lub utwórz nową kategorię dla tej definicji zasad.
   - Skopiuj poniższy kod JSON, a następnie zaktualizuj go zgodnie ze swoimi potrzebami przy użyciu następujących danych:
      - Parametry zasad.
      - Warunki/reguły zasad, w tym przypadku jest to rozmiar jednostki SKU maszyny wirtualnej, który jest równy serii G
      - Efekt zasad, w tym przypadku **Deny**.

    Oto, jak powinien wyglądać kod JSON. Wklej poprawiony kod do witryny Azure Portal.

    ```json
{
    "policyRule": {
      "if": {
        "allOf": [
          {
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

    Wartością *właściwości field* w regule zasad musi być jedna z następujących wartości: Name, Type, Location, Tags lub alias. Na przykład `"Microsoft.Compute/VirtualMachines/Size"`.

    Aby zobaczyć więcej przykładów kodu JSON, przeczytaj artykuł [Szablony usługi Azure Policy](json-samples.md).

4. Wybierz pozycję **Zapisz**.

## <a name="create-a-policy-definition-with-rest-api"></a>Tworzenie definicji zasad za pomocą interfejsu API REST

Zasady można tworzyć przy użyciu interfejsu API REST dla definicji zasad. Interfejs API REST umożliwia tworzenie i usuwanie definicji zasad oraz uzyskiwanie informacji o istniejących definicjach.
Aby utworzyć definicję zasad, skorzystaj z następującego przykładu:

```
PUT https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.authorization/policydefinitions/{policyDefinitionName}?api-version={api-version}

```
Dołącz treść żądania podobną do poniższego przykładu:

```
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

Przed przejściem do przykładu programu PowerShell upewnij się, że zainstalowano najnowszą wersję programu Azure PowerShell. Parametry zasad zostały dodane w wersji 3.6.0. Jeśli masz starszą wersję, przykłady zwrócą błąd wskazujący, że nie można odnaleźć parametru.

Definicję zasad można utworzyć przy użyciu polecenia cmdlet `New-AzureRmPolicyDefinition`.

Aby utworzyć definicję zasad na podstawie pliku, przekaż ścieżkę do tego pliku. W przypadku pliku zewnętrznego skorzystaj z następującego przykładu:

```
$definition = New-AzureRmPolicyDefinition `
    -Name denyCoolTiering `
    -DisplayName "Deny cool access tiering for storage" `
    -Policy 'https://raw.githubusercontent.com/Azure/azure-policy-samples/master/samples/Storage/storage-account-access-tier/azurepolicy.rules.json'
```

W przypadku pliku lokalnego skorzystaj z następującego przykładu:

```
$definition = New-AzureRmPolicyDefinition `
    -Name denyCoolTiering `
    -Description "Deny cool access tiering for storage" `
    -Policy "c:\policies\coolAccessTier.json"
```

Aby utworzyć definicję zasad z wbudowaną regułą, skorzystaj z następującego przykładu:

```
$definition = New-AzureRmPolicyDefinition -Name denyCoolTiering -Description "Deny cool access tiering for storage" -Policy '{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "field": "kind",
        "equals": "BlobStorage"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/accessTier",
          "equals": "cool"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}'
```

Dane wyjściowe są zapisywane w obiekcie `$definition`, który jest używany podczas przypisywania zasad.
Poniższy przykład tworzy definicję zasad, która obejmuje parametry:

```
$policy = '{
    "if": {
        "allOf": [
            {
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

$definition = New-AzureRmPolicyDefinition -Name storageLocations -Description "Policy to specify locations for storage accounts." -Policy $policy -Parameter $parameters
```

## <a name="view-policy-definitions"></a>Wyświetlanie definicji zasad

Aby wyświetlić wszystkie definicje zasad w ramach subskrypcji, użyj następującego polecenia:

```
Get-AzureRmPolicyDefinition
```

Zwraca ono wszystkie dostępne definicje zasad, w tym wbudowane zasady. Poszczególne zasady są zwracane w następującym formacie:

```
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

Definicję zasad możesz utworzyć przy użyciu interfejsu wiersza polecenia platformy Azure, korzystając z polecenia policy definition.
Aby utworzyć definicję zasad z wbudowaną regułą, skorzystaj z następującego przykładu:

```
az policy definition create --name denyCoolTiering --description "Deny cool access tiering for storage" --rules '{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "field": "kind",
        "equals": "BlobStorage"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/accessTier",
          "equals": "cool"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}'
```

## <a name="view-policy-definitions"></a>Wyświetlanie definicji zasad

Aby wyświetlić wszystkie definicje zasad w ramach subskrypcji, użyj następującego polecenia:

```
az policy definition list
```

Zwraca ono wszystkie dostępne definicje zasad, w tym wbudowane zasady. Poszczególne zasady są zwracane w następującym formacie:

```
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

Za pomocą definicji inicjatywy możesz grupować kilka definicji zasad w celu osiągnięcia jednego ogólnego celu. Tworząc definicję inicjatywy, zapewniasz zachowanie zgodności zasobów w zakresie definicji z definicjami zasad składającymi się na definicję inicjatywy.  Aby uzyskać więcej informacji na temat definicji inicjatyw, zobacz [Omówienie usługi Azure Policy](./azure-policy-introduction.md).

### <a name="create-an-initiative-definition"></a>Tworzenie definicji inicjatywy

1. W okienku po lewej stronie wybierz pozycję **Definicje** w obszarze **Tworzenie**.

   ![Wybieranie pozycji Definicje](media/create-manage-policy/select-definitions.png)

2. W górnej części strony wybierz pozycję **Definicja inicjatywy**. Spowoduje to przejście do formularza **Definicja inicjatywy**.
3. Wprowadź nazwę i opis inicjatywy.

   W tym przykładzie zapewniasz, że zasoby są zgodne z definicjami zasad w zakresie uzyskiwania bezpieczeństwa. Dlatego dla tej inicjatywy podaj nazwę **Uzyskiwanie bezpieczeństwa** i opis: **Ta inicjatywa została utworzona w celu obsługi wszystkich definicji zasad skojarzonych z zabezpieczaniem zasobów**.

   ![Definicja inicjatywy](media/create-manage-policy/initiative-definition.png)

4. Przejrzyj listę **Dostępne definicje** i wybierz definicje zasad, które chcesz dodać do tej inicjatywy. W przypadku naszej inicjatywy **Uzyskiwanie bezpieczeństwa** **dodaj** następujące wbudowane definicje zasad:
   - Require SQL Server Version 12.0 (Wymagaj programu SQL Server w wersji 12.0)
   - Monitor unprotected web applications in Security Center (Monitoruj niechronione aplikacje internetowe w usłudze Security Center)
   - Monitor permissive network across in Security Center (Monitoruj sieć z mniejszymi ograniczeniami w usłudze Security Center)
   - Monitor possible app Whitelisting in Security Center (Monitoruj możliwe umieszczanie aplikacji na białej liście w usłudze Security Center)
   - Monitor unencrypted VM Disks in Security Center (Monitoruj nieszyfrowane dyski maszyn wirtualnych w usłudze Security Center)

   ![Definicje inicjatyw](media/create-manage-policy/initiative-definition-2.png)

   Po wybraniu definicji zasad z listy zobaczysz je w obszarze **Zasady i parametry**, jak pokazano na powyższej ilustracji.

5. Za pomocą opcji **Lokalizacja definicji** wybierz subskrypcję, w której definicja ma być przechowywana. Wybierz pozycję **Zapisz**.

### <a name="assign-an-initiative-definition"></a>Przypisywanie definicji inicjatywy

1. Przejdź na kartę **Definicje** w obszarze **Tworzenie**.
2. Wyszukaj utworzoną definicję inicjatywy **Uzyskiwanie bezpieczeństwa**.
3. Wybierz definicję inicjatywy, a następnie wybierz przycisk **Przypisz**.

   ![Przypisywanie definicji](media/create-manage-policy/assign-definition.png)

4. Wypełnij formularz **Przypisanie**, wprowadzając następujące przykładowe informacje. Możesz podać własne informacje.
   - Nazwa: Uzyskiwanie bezpieczeństwa — przypisanie
   - Opis: To przypisanie inicjatywy jest dostosowane do wymuszania tej grupy definicji zasad w ramach subskrypcji **Azure Advisor Capacity Dev**.
   - Warstwa cenowa: Standardowa
   - Zakres, w którym chcesz zastosować to przypisanie: **Azure Advisor Capacity Dev**. Możesz wybrać własną subskrypcję i grupę zasobów.

5. Wybierz opcję **Przypisz**.

## <a name="exempt-a-non-compliant-or-denied-resource-using-exclusion"></a>Dopuszczanie niezgodnego lub odrzuconego zasobu przy użyciu wykluczenia

W powyższym przykładzie po przypisaniu definicji zasad wymagającej programu SQL Server w wersji 12.0 próba utworzenia programu SQL Server w innej wersji niż 12.0 zostałaby odrzucona. W tej sekcji opisano rozwiązywanie problemu z odmową utworzenia programu SQL Server przez żądanie wykluczenia określonych zasobów. Wykluczenie zasadniczo zapobiega wymuszaniu zasad. W poniższym przykładzie można użyć dowolnej wersji programu SQL Server. Wykluczenie można zastosować do grupy zasobów lub zawęzić je do pojedynczych zasobów.

1. Wybierz pozycję **Przypisania** w lewym okienku.
2. Przejrzyj wszystkie przypisania zasad i otwórz przypisanie *Require SQL Server Version 12.0* (Wymagaj programu SQL Server w wersji 12.0).
3. **Wybierz** wykluczenie dla zasobów w grupach zasobów, w których chcesz utworzyć program SQL Server. W tym przykładzie wyklucz zasoby Microsoft.Sql/servers/databases: *azuremetrictest/testdb* i *azuremetrictest/testdb2*.

   ![Żądanie wykluczenia](media/create-manage-policy/request-exclusion.png)

   Inne metody rozwiązania problemu z odrzuceniem zasobu to: skontaktowanie się z osobą ds. kontaktu skojarzoną z zasadami, jeśli masz dobre uzasadnienie konieczności utworzenia programu SQL Server, i bezpośrednie edytowanie zasad, jeśli masz do nich dostęp.

4. Kliknij przycisk **Przypisz**.

W tej sekcji rozwiązano problem z odmową utworzenia programu SQL Server, żądając wykluczenia zasobów.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli planujesz kontynuować pracę z kolejnymi samouczkami, nie usuwaj zasobów utworzonych w tym przewodniku. Jeśli nie planujesz kontynuować pracy, wykonaj poniższe kroki, aby usunąć wszystkie utworzone powyżej przypisania lub definicje:

1. Wybierz pozycję **Definicje** (lub **Przypisania**, jeśli próbujesz usunąć przypisanie) w okienku po lewej stronie.
2. Wyszukaj nową, właśnie utworzoną definicję inicjatywy lub zasad (albo przypisanie).
3. Wybierz wielokropek na końcu definicji lub przypisania, a następnie wybierz pozycję **Usuń definicję** (lub **Usuń przypisanie**).

## <a name="next-steps"></a>Następne kroki

W tym samouczku pomyślnie wykonano następujące czynności:

> [!div class="checklist"]
> * Przypisano zasady w celu wymuszania warunku dla zasobów tworzonych w przyszłości
> * Utworzono i przypisano definicję inicjatywy w celu śledzenia zgodności dla wielu zasobów
> * Rozwiązano problem w przypadku niezgodnego lub odrzuconego zasobu
> * Zaimplementowano nowe zasady w całej organizacji

Aby dowiedzieć się więcej o strukturach definicji zasad, zapoznaj się z artykułem:

> [!div class="nextstepaction"]
> [Struktura definicji usługi Azure Policy](policy-definition.md)
