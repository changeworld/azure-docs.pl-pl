---
title: Korzystanie z parametrów do tworzenia dynamicznych plany
description: Więcej informacji na temat parametrów statyczne i dynamiczne i jak za ich pomocą tworzy schematy dynamiczne.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/12/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 5c1bb1f959f920ea9bce23082ec531dc83d873ad
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66356980"
---
# <a name="creating-dynamic-blueprints-through-parameters"></a>Tworzenie dynamicznych schematy za pomocą parametrów

W pełni zdefiniowana planu z różnych artefaktami (np. grupy zasobów, Menedżer zasobów szablonów, zasad lub przypisania ról) oferuje szybkie tworzenie i spójne Tworzenie obiektów w obrębie platformy Azure. Aby umożliwić użycie elastycznych te wzorce projektowe wielokrotnego użytku i kontenerów, schematy Azure obsługuje parametry. Parametr tworzy elastyczność zarówno podczas definicji i przypisania, aby zmienić właściwości artefaktów wdrożone przez planu.

Prostym przykładem jest artefaktu grupy zasobów. Po utworzeniu grupy zasobów ma dwa wymagane wartości, które należy podać: Nazwa i lokalizacja. Jeśli podczas dodawania grupy zasobów do Twojego planu parametrów nie istnieje, należy zdefiniować tej nazwy i lokalizacji co do użytku z planu. Powtórzenie spowoduje, że każdy korzystanie z planem, aby utworzyć artefakty w tej samej grupie zasobów. Zasoby znajdujące się w tej grupie zasobów może stać się zduplikowane i powodować konflikt.

> [!NOTE]
> Nie jest to problem dla dwóch różnych planów obejmujący grupę zasobów o takiej samej nazwie.
> Jeśli istnieje już uwzględnione w planie grupę zasobów, planu w dalszym ciągu tworzenie powiązanych artefaktów w danej grupie zasobów. Może to spowodować konflikt jako dwa zasoby o takiej samej nazwie, a typ zasobu nie mogą znajdować się w ramach subskrypcji.

Rozwiązanie tego problemu jest parametrów. Schematy umożliwia definiowanie wartości dla każdej właściwości artefaktu podczas przypisywania do subskrypcji. Parametr umożliwia ponowne użycie planu, który tworzy grupę zasobów i innych zasobów w ramach jednej subskrypcji bez konfliktów.

## <a name="blueprint-parameters"></a>Parametry strategii

Za pomocą interfejsu API REST parametry mogą być tworzone na samego planu. Te parametry są inne niż parametrów dla każdej z obsługiwanych artefaktów. Po utworzeniu parametru na planu może służyć przez artefaktów, w tym planie. Przykładem może być prefiks nazwy grupy zasobów. Artefakt służy parametr planu do tworzenia parametru "przede wszystkim dynamicznego". Jak można również określić parametr podczas przypisywania, ten wzorzec umożliwia użycie w celu zachowania spójności, które mogą stosować zasady nazewnictwa. Aby uzyskać instrukcje, zobacz [ustawienie statycznych parametrów - planu parametru poziomu](#blueprint-level-parameter).

### <a name="using-securestring-and-secureobject-parameters"></a>Za pomocą parametrów ciągu secureString i secureObject

Podczas gdy szablon usługi Resource Manager _artefaktu_ obsługuje parametry **secureString** i **secureObject** typów, plany platformy Azure wymaga każdego połączenia z usługi Azure Key Vault.
To zabezpieczenie zapobiega niebezpieczne praktyki przechowywania wpisów tajnych wraz z planu i zachęca zatrudnienia bezpieczne wzorce. Plany usługi Azure obsługuje to zabezpieczenie, wykrywanie włączenia albo parametru secure w szablonie usługi Resource Manager _artefaktu_. Usługa, która jest następnie monituje podczas przypisywania następujących właściwości usługi Key Vault na wykryte parametru secure:

- Identyfikator zasobu usługi Key Vault
- Nazwa wpisu tajnego usługi Key Vault
- Wersja wpisu tajnego usługi Key Vault

Jeśli przypisanie planu używa **przypisany systemowo tożsamości zarządzanej**, odwołanie do usługi Key Vault _musi_ istnieje w tej samej subskrypcji, o których przypisano definicję planu.

Jeśli przypisanie planu używa **przypisanych przez użytkownika z tożsamości zarządzanej**, odwołanie do usługi Key Vault _może_ istnieje w subskrypcji scentralizowany. Tożsamość zarządzana musi otrzymać odpowiednie uprawnienia w usłudze Key Vault przed przypisanie planu.

> [!IMPORTANT]
> W obu przypadkach usługi Key Vault musi mieć **włączyć dostęp do usługi Azure Resource Manager dla wdrożenia szablonu** skonfigurowane na **zasady dostępu** strony. Aby uzyskać wskazówki na temat włączania tej funkcji, zobacz [usługi Key Vault — wdrożenie szablonu Włącz](../../../managed-applications/key-vault-access.md#enable-template-deployment).

Aby uzyskać więcej informacji na temat usługi Azure Key Vault, zobacz [Key Vault Przegląd](../../../key-vault/key-vault-overview.md).

## <a name="parameter-types"></a>Typy parametrów

### <a name="static-parameters"></a>Parametry statyczne

Wartość parametru określone w definicji planu jest nazywany **parametru statycznego**, ponieważ każdy Użyj planu wdroży artefaktów przy użyciu tej wartości statycznej. W tym przykładzie grupa zasobów, gdy nie ma sensu nazwy grupy zasobów może być uzasadnione dla lokalizacji. Następnie każdy przypisanie planu może utworzyć grupę zasobów, niezależnie od rodzaju jest wywoływana podczas przypisywania w tej samej lokalizacji. Ta elastyczność umożliwia selektywne w jakie możesz zdefiniować jako wymagane vs co można zmienić podczas przypisywania.

#### <a name="setting-static-parameters-in-the-portal"></a>Parametry statyczne ustawienia w portalu

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Wybierz pozycję **Definicje strategii** w lewej części strony.

1. Kliknij istniejący plan, a następnie kliknij przycisk **edycji planu** lub kliknij przycisk **+ Utwórz plan** i Wypełnij informacje w **podstawy** kartę.

1. Kliknij pozycję **Next: Artefakty** lub kliknąć **artefaktów** kartę.

1. Artefakty dodane do planu, które mają parametr opcje wyświetlania **parametry X z Y wypełnione** w **parametry** kolumny. Kliknij wiersz artefaktu, aby edytować parametry artefaktu.

   ![Parametry planu na definicji planu](../media/parameters/parameter-column.png)

1. **Edytuj artefaktu** strony Wyświetla opcje wartość odpowiednią do artefaktu kliknięty. Każdy parametr na artefaktu ma tytuł, pole wartości i pola wyboru. Ustaw pole niezaznaczone, aby stał się **parametru statycznego**. W przykładzie poniżej, tylko _lokalizacji_ jest **parametru statycznego** pozostał niezaznaczone i _nazwy grupy zasobów_ jest zaznaczone.

   ![Parametry statyczne planu artefaktu planu](../media/parameters/static-parameter.png)

#### <a name="setting-static-parameters-from-rest-api"></a>Parametry statyczne ustawienia z interfejsu API REST

Każdy identyfikator URI interfejsu API REST zawiera używane zmienne, które musisz zastąpić własnymi wartościami:

- `{YourMG}` — zastąp nazwą swojej grupy zarządzania
- `{subscriptionId}` — zastąp swoim identyfikatorem subskrypcji

##### <a name="blueprint-level-parameter"></a>Parametr poziomu planu

Podczas tworzenia planu za pośrednictwem interfejsu API REST, jest możliwe utworzenie [planu parametry](#blueprint-parameters). Aby to zrobić, użyj następującego formatu identyfikatora URI interfejsu API REST i treści:

- Identyfikator URI interfejsu API REST

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint?api-version=2018-11-01-preview
  ```

- Treść żądania

  ```json
  {
      "properties": {
          "description": "This blueprint has blueprint level parameters.",
          "targetScope": "subscription",
          "parameters": {
              "owners": {
                  "type": "array",
                  "metadata": {
                      "description": "List of AAD object IDs that is assigned Owner role at the resource group"
                  }
              }
          },
          "resourceGroups": {
              "storageRG": {
                  "description": "Contains the resource template deployment and a role assignment."
              }
          }
      }
  }
  ```

Po utworzeniu planu parametru poziomu może służyć w artefakty dodane do tego planu.
W poniższym przykładzie interfejs API REST tworzy artefaktu przypisania roli na planu i używa parametru poziomu planu.

- Identyfikator URI interfejsu API REST

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/roleOwner?api-version=2018-11-01-preview
  ```

- Treść żądania

  ```json
  {
      "kind": "roleAssignment",
      "properties": {
          "resourceGroup": "storageRG",
          "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
          "principalIds": "[parameters('owners')]"
      }
  }
  ```

W tym przykładzie **principalIds** używa właściwości **właścicieli** planu parametru poziomu przy użyciu wartości `[parameters('owners')]`. Ustawienie parametru na artefakt za pomocą parametru poziomu planu nadal znajduje się przykład **parametru statycznego**. Parametr poziomu planu nie można ustawić podczas przypisywania planu i będzie mieć taką samą wartość dla każdego przypisania.

##### <a name="artifact-level-parameter"></a>Parametru poziomu artefaktów

Tworzenie **parametry statyczne** na artefakt jest podobny, ale przyjmuje wartość bezpośrednio zamiast przy użyciu `parameters()` funkcji. Poniższy przykład tworzy dwa parametry statyczne, **tagName** i **tagValue**. Wartość na każdym bezpośrednio pod warunkiem i nie używa wywołania funkcji.

- Identyfikator URI interfejsu API REST

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/policyStorageTags?api-version=2018-11-01-preview
  ```

- Treść żądania

  ```json
  {
      "kind": "policyAssignment",
      "properties": {
          "description": "Apply storage tag and the parameter also used by the template to resource groups",
          "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71",
          "parameters": {
              "tagName": {
                  "value": "StorageType"
              },
              "tagValue": {
                  "value": "Premium_LRS"
              }
          }
      }
  }
  ```

### <a name="dynamic-parameters"></a>Parametry dynamiczne

Jest przeciwieństwem statyczne parametr **parametru dynamicznego**. Ten parametr nie jest zdefiniowany dla planu, ale zamiast tego jest zdefiniowany podczas każdego przydziału planu. W tym przykładzie grupa zasobów wykorzystania **parametru dynamicznego** pasujące do nazwy grupy zasobów. Zapewnia inną nazwę dla każdego przydziału planu. Aby uzyskać listę funkcji planu, zobacz [planu funkcje](../reference/blueprint-functions.md) odwołania.

#### <a name="setting-dynamic-parameters-in-the-portal"></a>Ustawianie parametrów dynamicznych w portalu

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Wybierz pozycję **Definicje strategii** w lewej części strony.

1. Kliknij prawym przyciskiem myszy na planu, który chcesz przypisać. Wybierz **planu Przypisz** lub kliknąć planu, którą chcesz przypisać, a następnie kliknij przycisk **planu Przypisz** przycisku.

1. Na **planu Przypisz** strony, Znajdź **parametry artefaktu** sekcji. Każdego artefaktu z co najmniej jednym **parametru dynamicznego** Wyświetla artefaktu i opcje konfiguracji. Podaj wymagane wartości parametrów, zanim przypisze planu. W poniższym przykładzie _nazwa_ jest **parametru dynamicznego** musi być zdefiniowany można ukończyć ich przypisywania planu.

   ![Parametr dynamiczny plan podczas przypisywania planu](../media/parameters/dynamic-parameter.png)

#### <a name="setting-dynamic-parameters-from-rest-api"></a>Ustawianie parametrów dynamicznych z interfejsu API REST

Ustawienie **parametrów dynamicznych** przypisanie jest wykonywana, wprowadzając wartość bezpośrednio. Zamiast korzystać z funkcji, takich jak [parameters()](../reference/blueprint-functions.md#parameters), podana wartość jest odpowiedni ciąg. Artefakty dla grupy zasobów są definiowane za pomocą "Nazwa szablonu," **nazwa**, i **lokalizacji** właściwości. Wszystkie inne parametry dla artefaktu uwzględniane są zdefiniowane w obszarze **parametry** z **\<nazwa\>** i **wartość** pary kluczy. Planu jest skonfigurowany dla parametrów dynamicznych, które nie są oferowane w trakcie przypisywania, przypisanie zakończy się niepowodzeniem.

- Identyfikator URI interfejsu API REST

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Blueprint/blueprintAssignments/assignMyBlueprint?api-version=2018-11-01-preview
  ```

- Treść żądania

  ```json
  {
      "properties": {
          "blueprintId": "/providers/Microsoft.Management/managementGroups/{YourMG}  /providers/Microsoft.Blueprint/blueprints/MyBlueprint",
          "resourceGroups": {
              "storageRG": {
                  "name": "StorageAccount",
                  "location": "eastus2"
              }
          },
          "parameters": {
              "storageAccountType": {
                  "value": "Standard_GRS"
              },
              "tagName": {
                  "value": "CostCenter"
              },
              "tagValue": {
                  "value": "ContosoIT"
              },
              "contributors": {
                  "value": [
                      "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
                      "38833b56-194d-420b-90ce-cff578296714"
                  ]
                },
              "owners": {
                  "value": [
                      "44254d2b-a0c7-405f-959c-f829ee31c2e7",
                      "316deb5f-7187-4512-9dd4-21e7798b0ef9"
                  ]
              }
          }
      },
      "identity": {
          "type": "systemAssigned"
      },
      "location": "westus"
  }
  ```

## <a name="next-steps"></a>Kolejne kroki

- Przejrzyj listę rzeczy, [planu funkcji](../reference/blueprint-functions.md).
- Uzyskaj informacje na temat [cyklu życia strategii](lifecycle.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../how-to/update-existing-assignments.md).
- Rozwiązywanie problemów podczas przypisywania strategii za pomocą [ogólnych procedur rozwiązywania problemów](../troubleshoot/general.md).