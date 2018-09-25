---
title: Tworzenie dynamicznych schematy za pomocą parametrów w plany usługi Azure
description: Więcej informacji na temat parametrów statyczne i dynamiczne i jak za ich pomocą tworzy schematy dynamiczne.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: e1a1d736eb9b22cd444a75b94d112abfe3fbe5af
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46993582"
---
# <a name="creating-dynamic-blueprints-through-parameters"></a>Tworzenie dynamicznych schematy za pomocą parametrów

W pełni zdefiniowana planu z różnych artefaktami (np. grupy zasobów, Menedżer zasobów szablonów, zasad lub przypisania ról) oferuje szybkie tworzenie i spójne udostępnianie obiektów w obrębie platformy Azure. Aby umożliwić użycie elastycznych te wzorce projektowe wielokrotnego użytku i kontenerów, schematy Azure obsługuje parametry. Parametr tworzy elastyczność zarówno podczas definicji i przypisania, aby zmienić właściwości artefaktów wdrożone przez planu.

Prostym przykładem jest artefaktu grupy zasobów. Po utworzeniu grupy zasobów ma dwa wymagane wartości, które należy podać: Nazwa i lokalizacja. Jeśli podczas dodawania grupy zasobów do Twojego planu parametrów nie istnieje, należy zdefiniować tej nazwy i lokalizacji co do użytku z planu. To spowoduje, że każdy korzystanie z planem, aby utworzyć artefakty w tej samej grupie zasobów. Gdy nie jest problemem z samej zasoby grupy zasobów w tej grupie zasobów może stać się zduplikowane i powodować konflikt.

> [!NOTE]
> Nie jest to problem dla dwóch różnych planów obejmujący grupę zasobów o takiej samej nazwie.
> Jeśli istnieje już uwzględnione w planie grupę zasobów, planu w dalszym ciągu tworzenie powiązanych artefaktów w danej grupie zasobów. Może to spowodować konflikt jako dwa zasoby o takiej samej nazwie, a typ zasobu nie mogą znajdować się w ramach subskrypcji.

Jest to, gdzie parametry mieści się w. Wartości tych właściwości, w przypadku grupy zasobów, nazwę i właściwości location, schematy umożliwia ich nie definiują podczas definicji planu, ale zamiast tego zdefiniuj ich wartości podczas przypisywania do subskrypcji. Umożliwia to ponowne użycie planu, który tworzy grupę zasobów i innych zasobów w ramach jednej subskrypcji bez konfliktów.

## <a name="blueprint-parameters"></a>Parametry planu

Za pomocą interfejsu API REST parametry mogą być tworzone na plan oprócz każdej z obsługiwanych artefaktów. Po utworzeniu parametru na planu może służyć przez artefaktów, w tym planie. Przykładem może być prefiks nazwy grupy zasobów. Artefakt następnie można użyć parametru planu utworzyć parametr "przede wszystkim dynamiczny", jako parametr, nadal można zdefiniować podczas przypisywania, ale będzie miał spójności, które mogą stosować zasady nazewnictwa organizacji. Aby uzyskać instrukcje, zobacz [ustawienie statycznych parametrów - planu parametru poziomu](#blueprint-level-parameter).

### <a name="using-securestring-and-secureobject-parameters"></a>Za pomocą parametrów ciągu secureString i secureObject

Podczas gdy szablon usługi Resource Manager _artefaktu_ obsługuje parametry **secureString** i **secureObject** typów, plany platformy Azure wymaga każdego połączenia z usługi Azure Key Vault.
To uniemożliwia niebezpieczne praktyki przechowywania wpisów tajnych wraz z planu i zachęca zatrudnienia bezpieczne wzorce. Plany usługi Azure ułatwia to dzięki wykrywaniu włączenia albo parametru secure w szablonie usługi Resource Manager _artefaktu_ monitowania użytkownika podczas przypisywania planu dla poniższej usługi Key Vault właściwości dla poszczególnych wykryte bezpieczne parametr:

- Identyfikator zasobu usługi Key Vault
- Nazwa wpisu tajnego usługi Key Vault
- Wersja wpisu tajnego usługi Key Vault

Musi istnieć w tej samej subskrypcji, ponieważ planu jest przypisany do odwołania usługi Key Vault i również musi mieć **włączyć dostęp do usługi Azure Resource Manager dla wdrożenia szablonu** skonfigurowane na usługi Key Vault **dostępu zasady** strony. Aby uzyskać wskazówki na temat włączania tej funkcji, zobacz [usługi Key Vault — wdrożenie szablonu Włącz](../../../managed-applications/key-vault-access.md#enable-template-deployment).
Aby uzyskać więcej informacji na temat usługi Azure Key Vault, zobacz [Key Vault Przegląd](../../../key-vault/key-vault-overview.md).

## <a name="parameter-types"></a>Typy parametrów

### <a name="static-parameters"></a>Parametry statyczne

Wartość parametru określone w definicji planu jest nazywany **parametru statycznego**. Jest to spowodowane co użycie planu wdroży artefaktów przy użyciu tej wartości statycznej. W tym przykładzie grupa zasobów podczas to nie miałoby sensu nazwy grupy zasobów może być uzasadnione dla lokalizacji. Następnie każdy przypisanie planu może utworzyć grupę zasobów, niezależnie od rodzaju jest wywoływana podczas przypisywania w tej samej lokalizacji. Dzięki temu można zachować ostrożność w jakie możesz zdefiniować jako wymagane vs co można zmienić podczas przypisywania.

#### <a name="setting-static-parameters-in-the-portal"></a>Parametry statyczne ustawienia w portalu

1. Uruchom usługę Azure schematy w witrynie Azure portal, klikając **wszystkich usług** wyszukiwanie i wybieranie **zasad** w okienku po lewej stronie. Na **zasad** kliknij na **plany**.

1. Wybierz **definicje planów** ze strony po lewej stronie.

1. Kliknij istniejący plan, a następnie kliknij przycisk **edytować planu** lub kliknij przycisk **+ Utwórz plan** i Wypełnij informacje w **podstawy** kartę.

1. Kliknij przycisk **Następny: artefakty** lub kliknąć **artefaktów** kartę.

1. Artefakty dodane do planu, które mają parametr opcje wyświetlania **parametry X z Y wypełnione** w **parametry** kolumny. Kliknij wiersz artefaktu, aby edytować parametry artefaktu.

   ![Parametry planu](../media/parameters/parameter-column.png)

1. **Edytuj artefaktu** strony Wyświetla opcje wartość odpowiednią do artefaktu kliknięty. Każdy parametr na artefaktu ma tytuł, pole wartości i pola wyboru. Ustaw pole niezaznaczone, aby stał się **parametru statycznego**. W przykładzie poniżej, tylko _lokalizacji_ jest **parametru statycznego** się niezaznaczone i _nazwy grupy zasobów_ jest zaznaczone.

   ![Parametry statyczne dla planu](../media/parameters/static-parameter.png)

#### <a name="setting-static-parameters-from-rest-api"></a>Parametry statyczne ustawienia z interfejsu API REST

Każdy identyfikator URI interfejsu API REST istnieją zmienne, które są używane, należy zastąpić własnymi wartościami:

- `{YourMG}` -Zamień na nazwę grupy zarządzania
- `{subscriptionId}` -Zastąp identyfikator subskrypcji

##### <a name="blueprint-level-parameter"></a>Parametr poziomu planu

Podczas tworzenia planu za pośrednictwem interfejsu API REST, jest możliwe utworzenie [planu parametry](#blueprint-parameters). Aby to zrobić, użyj następującego formatu identyfikatora URI interfejsu API REST i treści:

- IDENTYFIKATOR URI INTERFEJSU API REST

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint?api-version=2017-11-11-preview
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

- IDENTYFIKATOR URI INTERFEJSU API REST

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/roleOwner?api-version=2017-11-11-preview
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

W tym przykładzie **principalIds** właściwości wprowadzone użytkowania **właścicieli** planu parametru poziomu, podając wartość `[parameters('owners')]`. Ustawienie parametru na artefakt za pomocą parametru poziomu planu nadal znajduje się przykład **parametru statycznego** nie można ustawić podczas przypisywania planu i będzie to wartość dla każdego przydziału.

##### <a name="artifact-level-parameter"></a>Parametru poziomu artefaktów

Tworzenie **parametry statyczne** na artefakt jest podobny, ale przyjmuje wartość bezpośrednio zamiast przy użyciu `parameters()` funkcji. Poniższy przykład tworzy dwa parametry statyczne, **tagName** i **tagValue**. Wartość na każdym bezpośrednio pod warunkiem i nie używa wywołania funkcji.

- IDENTYFIKATOR URI INTERFEJSU API REST

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/policyStorageTags?api-version=2017-11-11-preview
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

Jest przeciwieństwem statyczne parametr **parametru dynamicznego**. Jest to parametr, który nie został zdefiniowany w planu, ale zamiast tego jest zdefiniowany podczas każdego przydziału planu. W tym przykładzie grupa zasobów to sens nazwy grupy zasobów, podając inną nazwę dla każdego przydziału planu.

#### <a name="setting-dynamic-parameters-in-the-portal"></a>Ustawianie parametrów dynamicznych w portalu

1. Uruchom usługę Azure schematy w witrynie Azure portal, klikając **wszystkich usług** wyszukiwanie i wybieranie **zasad** w okienku po lewej stronie. Na **zasad** kliknij na **plany**.

1. Wybierz **definicje planów** ze strony po lewej stronie.

1. Kliknij prawym przyciskiem myszy na planu, który chcesz przypisać, a następnie wybierz pozycję **przypisać planu** lub kliknąć planu, którą chcesz przypisać, a następnie kliknij przycisk **przypisać planu** przycisku.

1. Na **przypisać planu** strony, Znajdź **parametry artefaktu** sekcji. Każdego artefaktu z co najmniej jednym **parametru dynamicznego** Wyświetla artefaktu i opcje konfiguracji. Podaj wymagane wartości parametrów, zanim przypisze planu. W poniższym przykładzie _nazwa_ jest **parametru dynamicznego** musi być zdefiniowany można ukończyć ich przypisywania planu.

   ![Parametr dynamiczny plan](../media/parameters/dynamic-parameter.png)

#### <a name="setting-dynamic-parameters-from-rest-api"></a>Ustawianie parametrów dynamicznych z interfejsu API REST

Ustawienie **parametrów dynamicznych** przypisanie jest wykonywana, zapewniając bezpośrednio na żądaną wartość. Zamiast korzystać z funkcji, takich jak `parameters()`, podana wartość jest odpowiedni ciąg. Artefakty dla grupy zasobów są definiowane za pomocą "Nazwa szablonu", a **nazwa** i **lokalizacji** właściwości. Wszystkie inne parametry dla każdego artefaktu dołączony jest zdefiniowane w obszarze **parametry** z **\<nazwa\>** i **wartość** pary kluczy. Planu jest skonfigurowany dla parametrów dynamicznych, które nie są oferowane w trakcie przypisywania, przypisanie zakończy się niepowodzeniem.

- IDENTYFIKATOR URI INTERFEJSU API REST

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Blueprint/blueprintAssignments/assignMyBlueprint?api-version=2017-11-11-preview
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

- Dowiedz się więcej o [planu cyklu życia](lifecycle.md)
- Dowiedz się, jak dostosować [planu sekwencjonowania](sequencing-order.md)
- Dowiedz się, jak używać [planu blokowanie zasobów](resource-locking.md)
- Dowiedz się, jak [zaktualizować istniejące przypisania](../how-to/update-existing-assignments.md)
- Rozwiązywanie problemów podczas przypisywania planu z [Ogólne rozwiązywanie problemów](../troubleshoot/general.md)