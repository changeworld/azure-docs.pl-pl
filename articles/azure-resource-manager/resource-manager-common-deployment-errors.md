---
title: Rozwiąż typowe błędy wdrożenia usługi Azure | Dokumentacja firmy Microsoft
description: Opisuje sposób rozwiązania typowych błędów, podczas wdrażania zasobów na platformie Azure przy użyciu usługi Azure Resource Manager.
services: azure-resource-manager
documentationcenter: ''
tags: top-support-issue
author: tfitzmac
manager: timlt
editor: tysonn
keywords: Błąd wdrażania, wdrożenia usługi azure wdrażanie na platformie azure
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/08/2018
ms.author: tomfitz
ms.openlocfilehash: 3ecc1a9557c7854a0771decb3cc7f7597bcd87dd
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34360023"
---
# <a name="troubleshoot-common-azure-deployment-errors-with-azure-resource-manager"></a>Rozwiąż typowe błędy wdrożenia usługi Azure z usługą Azure Resource Manager

W tym artykule opisano niektóre typowe błędy wdrożenia usługi Azure może wystąpić, a także informacje umożliwiające rozwiązanie błędów. Jeśli nie możesz znaleźć kod błędu dla błędu wdrożenia, zobacz [znaleźć kodu błędu](#find-error-code).

## <a name="error-codes"></a>Kody błędów

| Kod błędu | Środki zaradcze | Więcej informacji |
| ---------- | ---------- | ---------------- |
| AccountNameInvalid | Wykonaj ograniczenia nazewnictwa dla kont magazynu. | [Rozpoznanie nazwy konta magazynu](resource-manager-storage-account-name-errors.md) |
| AccountPropertyCannotBeSet | Sprawdź właściwości konta magazynu dostępne. | [storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |
| AllocationFailed | Klaster lub regionie nie ma dostępu do zasobów lub nie może obsługiwać żądany rozmiar maszyny Wirtualnej. Ponów żądanie w późniejszym czasie, lub zwróć inny rozmiar maszyny Wirtualnej. | [Problemy z inicjowaniem obsługi administracyjnej i alokacji dla systemu Linux](../virtual-machines/linux/troubleshoot-deployment-new-vm.md), [problemy z inicjowaniem obsługi administracyjnej i alokacji dla systemu Windows](../virtual-machines/windows/troubleshoot-deployment-new-vm.md) i [Rozwiązywanie problemów z przydziałem](../virtual-machines/windows/allocation-failure.md)|
| AnotherOperationInProgress | Poczekaj na zakończenie operacji współbieżnych. | |
| AuthorizationFailed | Twoje konto lub nazwy głównej usługi nie ma wystarczające prawa dostępu do wdrażania. Sprawdź używane konto należy do roli, a jego dostępu dla zakresu wdrożenia. | [Kontrola dostępu oparta na rolach na platformie Azure](../role-based-access-control/role-assignments-portal.md) |
| BadRequest | Wysłano wartości wdrożenia, które nie są zgodne, czego oczekuje się za pomocą Menedżera zasobów. Sprawdź komunikat o stanie wewnętrzny Aby uzyskać pomoc dotyczącą rozwiązywania problemów. | [Odwołanie do szablonu](/azure/templates/) i [obsługiwane lokalizacje](resource-manager-templates-resources.md#location) |
| Konflikt | Zażądano operacji, które nie są dozwolone w bieżącym stanie zasobu. Na przykład zmiana rozmiaru dysku jest dozwolona tylko w przypadku tworzenia maszyny Wirtualnej lub po cofnięciu przydziału maszyny Wirtualnej. | |
| DeploymentActive | Poczekaj, aż równoczesnych wdrożenia do tej grupy zasobów, aby zakończyć. | |
| Niepowodzenia wdrożenia | Błąd niepowodzenia wdrożenia jest błąd ogólny, który nie zapewnia szczegółowe informacje, musisz poprawić błąd. Sprawdź szczegóły błędu dla kodu błędu, zawierający więcej informacji. | [Znajdź kod błędu:](#find-error-code) |
| DeploymentQuotaExceeded | Jeśli użytkownik osiągnie limit 800 wdrożeń dla grupy zasobów, usunięcie wdrożeń z historii, które nie są już potrzebne. Możesz usunąć pozycji z historii kodu za pomocą [usunąć wdrożenie grupy az](/cli/azure/group/deployment#az_group_deployment_delete) dla wiersza polecenia platformy Azure, lub [AzureRmResourceGroupDeployment Usuń](/powershell/module/azurerm.resources/remove-azurermresourcegroupdeployment) w programie PowerShell. Usuwanie wpisu z historii wdrożenia nie ma wpływu na zasoby wdrażania. | |
| DnsRecordInUse | Nazwa rekordu DNS musi być unikatowa. Podaj inną nazwę, albo zmodyfikować istniejący rekord. | |
| ImageNotFound | Sprawdź ustawienia obrazu maszyny Wirtualnej. |  |
| InUseSubnetCannotBeDeleted | Błąd może się pojawić podczas próby zaktualizowania zasobu, ale żądanie jest przetwarzane przez usunięcie i utworzenie zasobu. Upewnij się określić wszystkie wartości bez zmian. | [Aktualizowanie zasobu](/azure/architecture/building-blocks/extending-templates/update-resource) |
| InvalidAuthenticationTokenTenant | Uzyskaj token dostępu dla odpowiedniego dzierżawcy. Tokenu można uzyskać tylko konta należącego do dzierżawy. | |
| InvalidContentLink | Prawdopodobnie podjęto próbę połączyć zagnieżdżony szablon, który nie jest dostępny. Sprawdź identyfikator URI przewidzianych szablon zagnieżdżony. Jeśli szablon istnieje w ramach konta magazynu, upewnij się, że identyfikator URI jest dostępny. Należy przekazać tokenu sygnatury dostępu Współdzielonego. | [Szablony połączonego](resource-group-linked-templates.md) |
| InvalidParameter | Jedna z wartości podane dla zasobu nie jest zgodna z oczekiwaną wartością. Ten błąd może wynikać z wielu różnych warunkach. Na przykład hasła mogą być niewystarczające lub nazwa obiektu blob może być nieprawidłowa. Sprawdź komunikat o błędzie, aby określić wartość, która musi zostać usunięty. | |
| InvalidRequestContent | Wartości wdrożenia zawierają wartości, które nie powinny lub brakuje wymaganych wartości. Potwierdź wartości dla danego typu zasobu. | [Odwołanie do szablonu](/azure/templates/) |
| InvalidRequestFormat | Włączenie rejestrowania debugowania, podczas wykonywania wdrożenia i Sprawdź treść żądania. | [Funkcję rejestrowania debugowania](#enable-debug-logging) |
| InvalidResourceNamespace | Sprawdź przestrzeni nazw zasobu określony w **typu** właściwości. | [Odwołanie do szablonu](/azure/templates/) |
| InvalidResourceReference | Zasób jeszcze nie istnieje lub jest niepoprawnie odwołuje się do. Sprawdź, czy konieczne jest dodanie zależności. Upewnij się, że korzystanie z **odwołania** funkcja zawiera parametry wymagane dla danego scenariusza. | [Rozwiąż zależności](resource-manager-not-found-errors.md) |
| InvalidResourceType | Typ wyboru zasób określony w **typu** właściwości. | [Odwołanie do szablonu](/azure/templates/) |
| InvalidSubscriptionRegistrationState | Zarejestrować subskrypcji u dostawcy zasobów. | [Rozwiąż rejestracji](resource-manager-register-provider-errors.md) |
| InvalidTemplate | Sprawdź składnię szablonu błędów. | [Nieprawidłowy szablon rozwiązania](resource-manager-invalid-template-errors.md) |
| InvalidTemplateCircularDependency | Usuń zbędne zależności. | [Rozwiązanie zależności cykliczne](resource-manager-invalid-template-errors.md#circular-dependency) |
| LinkedAuthorizationFailed | Sprawdź, czy używane konto należy do tej samej dzierżawy jako grupa zasobów, które wdrażasz. | |
| LinkedInvalidPropertyId | Identyfikator zasobu dla zasobu nie jest poprawnie rozpoznawania. Sprawdź, podaj wartości wszystkich wymaganych identyfikatorów zasobów, w tym identyfikator subskrypcji, nazwa grupy zasobów, typ zasobu, nazwa zasobu nadrzędnego (Jeśli to konieczne) i nazwa zasobu. | |
| LocationRequired | Podaj lokalizację dla zasobu. | [Ustawianie lokalizacji](resource-manager-templates-resources.md#location) |
| MismatchingResourceSegments | Upewnij się, że zasób ma nieprawidłową liczbę segmentów w nazwę i typ zagnieżdżony. | [Rozwiąż segmenty zasobu](resource-manager-invalid-template-errors.md#incorrect-segment-lengths)
| MissingRegistrationForLocation | Sprawdź stan rejestracji dostawcy zasobów i obsługiwane lokalizacje. | [Rozwiąż rejestracji](resource-manager-register-provider-errors.md) |
| MissingSubscriptionRegistration | Zarejestrować subskrypcji u dostawcy zasobów. | [Rozwiąż rejestracji](resource-manager-register-provider-errors.md) |
| NoRegisteredProviderFound | Sprawdzaj stan rejestracji dostawcy zasobów. | [Rozwiąż rejestracji](resource-manager-register-provider-errors.md) |
| Nie odnaleziono | Może być próby wdrożenia zasób zależny równolegle z zasobu nadrzędnego. Sprawdź, czy konieczne jest dodanie zależności. | [Rozwiąż zależności](resource-manager-not-found-errors.md) |
| OperationNotAllowed | Wdrożenie próbuje operację, która przekracza limit przydziału dla subskrypcji, grupy zasobów lub regionu. Jeśli to możliwe Popraw danego wdrożenia, aby pozostać w przydziałów. W przeciwnym razie należy wziąć pod uwagę żądania zmiany przydziałami. | [Rozwiąż przydziałów](resource-manager-quota-errors.md) |
| ParentResourceNotFound | Upewnij się, że istnieje zasób nadrzędnego przed utworzeniem zasoby podrzędne. | [Rozwiąż zasobu nadrzędnego](resource-manager-parent-resource-errors.md) |
| PrivateIPAddressInReservedRange | Określony adres IP zawiera zakres adresów wymagane przez platformę Azure. Zmienianie adresu IP, aby uniknąć zarezerwowany zakres. | [Adresy IP](../virtual-network/virtual-network-ip-addresses-overview-arm.md) |
| PrivateIPAddressNotInSubnet | Określony adres IP jest spoza zakresu podsieci. Zmienianie adresu IP, aby przypadał zakresu podsieci. | [Adresy IP](../virtual-network/virtual-network-ip-addresses-overview-arm.md) |
| PropertyChangeNotAllowed | Niektóre właściwości nie można zmienić na wdrożonych zasobów. Podczas aktualizowania zasobu, należy ograniczyć zmiany właściwości dozwolone. | [Aktualizowanie zasobu](/azure/architecture/building-blocks/extending-templates/update-resource) |
| RequestDisallowedByPolicy | Twoja subskrypcja obejmuje zasadę zasobu, która uniemożliwia akcję, którą próbujesz wykonać podczas wdrażania. Znajdź zasad, która blokuje akcji. Jeśli to możliwe zmodyfikuj wdrożenie do spełnienia ograniczenia z zasad. | [Zasady rozwiązania](resource-manager-policy-requestdisallowedbypolicy-error.md) |
| ReservedResourceName | Podaj nazwę zasobu, która nie zawiera zastrzeżoną nazwą. | [Nazw zarezerwowanych zasobów](resource-manager-reserved-resource-name.md) |
| ResourceGroupBeingDeleted | Poczekaj do usunięcia zakończyć. | |
| ResourceGroupNotFound | Sprawdź nazwę grupy zasobów docelowych wdrożenia. Musi już istnieć w ramach subskrypcji. Sprawdź kontekst subskrypcji. | [Azure CLI](/cli/azure/account?#az_account_set) [programu PowerShell](/powershell/module/azurerm.profile/set-azurermcontext) |
| ResourceNotFound | Wdrożenie odwołuje się do zasobu, którego nie można rozwiązać. Upewnij się, że korzystanie z **odwołania** funkcja zawiera parametrów wymaganych dla danego scenariusza. | [Rozpoznawania odwołań](resource-manager-not-found-errors.md) |
| ResourceQuotaExceeded | Wdrożenie próbuje utworzyć zasoby, które przekraczają limit przydziału dla subskrypcji, grupy zasobów lub regionu. Jeśli to możliwe Popraw infrastruktury ma pozostać w ramach przydziałów. W przeciwnym razie należy wziąć pod uwagę żądania zmiany przydziałami. | [Rozwiąż przydziałów](resource-manager-quota-errors.md) |
| SkuNotAvailable | Wybierz jednostki SKU (takich jak rozmiar maszyny Wirtualnej) dostępnej dla wybranej lokalizacji. | [Rozwiąż jednostki SKU](resource-manager-sku-not-available-errors.md) |
| StorageAccountAlreadyExists | Podaj unikatową nazwę konta magazynu. | [Rozpoznanie nazwy konta magazynu](resource-manager-storage-account-name-errors.md)  |
| StorageAccountAlreadyTaken | Podaj unikatową nazwę konta magazynu. | [Rozpoznanie nazwy konta magazynu](resource-manager-storage-account-name-errors.md) |
| StorageAccountNotFound | Sprawdź subskrypcji, grupy zasobów i nazwy konta magazynu, którego próbujesz użyć. | |
| SubnetsNotInSameVnet | Maszyna wirtualna może mieć tylko jedną sieć wirtualną. W przypadku wdrażania wielu kart sieciowych, upewnij się, że należą one do tej samej sieci wirtualnej. | [Wiele kart sieciowych](../virtual-machines/windows/multiple-nics.md) |
| TemplateResourceCircularDependency | Usuń zbędne zależności. | [Rozwiązanie zależności cykliczne](resource-manager-invalid-template-errors.md#circular-dependency) |
| TooManyTargetResourceGroups | Zmniejsz liczbę grup zasobów dla pojedynczego wdrożenia. | [Wdrażanie krzyżowe w grupach zasobów](resource-manager-cross-resource-group-deployment.md) |

## <a name="find-error-code"></a>Znajdź kod błędu:

Istnieją dwa typy błędów, które mogą odbierać:

* Błędy sprawdzania poprawności
* Błędy wdrożenia

Błędy sprawdzania poprawności wynikają z scenariusze, które można określić przed przystąpieniem do wdrożenia. Obejmują one błędy składniowe w szablonie lub w trakcie wdrażania zasobów, które może spowodować przekroczenie przydziałami subskrypcji. Błędy wdrożenia wynikają z warunków, które występują w trakcie procesu wdrażania. Obejmują one próby uzyskania dostępu do zasobu, który jest wdrażany jednocześnie.

Oba typy błędów zwraca kod błędu, który umożliwia rozwiązywanie problemów z wdrażaniem. Oba typy błędy są wyświetlane w [dziennik aktywności](resource-group-audit.md). Błędy sprawdzania poprawności nie pojawiają się jednak w historii wdrożenia, ponieważ wdrożenie nigdy nie jest uruchomiony.

### <a name="validation-errors"></a>Błędy weryfikacji

W przypadku wdrażania za pośrednictwem portalu, zobaczysz błąd sprawdzania poprawności po przesłaniu wartości.

![Pokaż błąd sprawdzania poprawności portalu](./media/resource-manager-common-deployment-errors/validation-error.png)

Zaznacz wiadomość, aby uzyskać więcej informacji. Na poniższej ilustracji, zobacz **InvalidTemplateDeployment** błąd i komunikat informujący zasad zablokowane wdrożenia.

![Pokaż szczegóły sprawdzania poprawności](./media/resource-manager-common-deployment-errors/validation-details.png)

### <a name="deployment-errors"></a>Błędy wdrożenia

Podczas operacji pozytywnej weryfikacji, ale nie powiodło się podczas wdrażania, zostanie wyświetlony błąd w powiadomieniach. Wybierz powiadomienie.

![Błąd powiadomienia](./media/resource-manager-common-deployment-errors/notification.png)

Możesz wyświetlić więcej szczegółów dotyczących wdrożenia. Wybierz opcję, aby uzyskać więcej informacji o tym błędzie.

![Wdrażanie nie powiodło się](./media/resource-manager-common-deployment-errors/deployment-failed.png)

Zostanie wyświetlony komunikat o błędzie i kody błędów. Należy zauważyć, że istnieją dwa kody błędów. Pierwszy kod błędu (**niepowodzenia wdrożenia**) jest błąd ogólny, który nie zapewnia szczegółowe informacje, musisz poprawić błąd. Drugi kod błędu (**StorageAccountNotFound**) zawiera szczegółowe informacje, należy. 

![szczegóły błędu](./media/resource-manager-common-deployment-errors/error-details.png)

## <a name="enable-debug-logging"></a>Włączenie rejestrowania debugowania

Czasami możesz uzyskać więcej informacji dotyczących żądania i odpowiedzi, aby dowiedzieć się, co poszło źle. Przy użyciu programu PowerShell lub interfejsu wiersza polecenia Azure, mogą żądać, aby uzyskać dodatkowe informacje jest rejestrowane podczas wdrażania.

- PowerShell

   W programie PowerShell, należy ustawić **DeploymentDebugLogLevel** parametr do wszystkich, obiektu ResponseContent lub RequestContent.

  ```powershell
  New-AzureRmResourceGroupDeployment -ResourceGroupName examplegroup -TemplateFile c:\Azure\Templates\storage.json -DeploymentDebugLogLevel All
  ```

   Sprawdź żądanie zawartości za pomocą następującego polecenia cmdlet:

  ```powershell
  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName storageonly -ResourceGroupName startgroup).Properties.request | ConvertTo-Json
  ```

   Lub zawartości z odpowiedzi:

  ```powershell
  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName storageonly -ResourceGroupName startgroup).Properties.response | ConvertTo-Json
  ```

   Te informacje mogą pomóc w określeniu, czy wartość w szablonie jest są ustawione niepoprawnie.

- Interfejs wiersza polecenia platformy Azure

   Sprawdź operacje wdrażania przy użyciu następującego polecenia:

  ```azurecli
  az group deployment operation list --resource-group ExampleGroup --name vmlinux
  ```

- Szablon zagnieżdżony

   Rejestrowanie informacji debugowania dla szablonu zagnieżdżonego, należy użyć **debugSetting** elementu.

  ```json
  {
      "apiVersion": "2016-09-01",
      "name": "nestedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
          "mode": "Incremental",
          "templateLink": {
              "uri": "{template-uri}",
              "contentVersion": "1.0.0.0"
          },
          "debugSetting": {
             "detailLevel": "requestContent, responseContent"
          }
      }
  }
  ```

## <a name="create-a-troubleshooting-template"></a>Tworzenie szablonu rozwiązywania problemów

W pewnych sytuacjach Najprostszym sposobem Rozwiązywanie problemów z szablonu jest przetestowanie jej części. Uproszczona można utworzyć szablonu, który pozwala skupić się na części podejrzenie, że przyczyną jest błąd. Na przykład załóżmy, że otrzymujesz błąd podczas odwoływania się do zasobu. Zamiast dotyczącą całą szablonu, utworzyć szablon, który zwraca składnik, który może być przyczyną tego problemu. Możesz określić, czy jest przekazywany w parametrach prawo poprawnie, przy użyciu funkcji szablonu i pobieranie zasobu oczekiwać może pomóc.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageName": {
        "type": "string"
    },
    "storageResourceGroup": {
        "type": "string"
    }
  },
  "variables": {},
  "resources": [],
  "outputs": {
    "exampleOutput": {
        "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageName')), '2016-05-01')]",
        "type" : "object"
    }
  }
}
```

Lub Załóżmy, że pojawiły się błędy wdrożenia, które Twoim zdaniem są związane z niepoprawnie ustawione zależności. Przetestuj szablonu przez podzielenie go na uproszczonej szablonów. Najpierw Utwórz szablon, który wdraża jednego zasobu (np. programu SQL Server). Podczas pracy upewnić się, że ten zasób zdefiniowany prawidłowo, Dodaj zasób zależy od niego (na przykład baza danych SQL). Jeśli te dwa zasoby zdefiniowany prawidłowo, należy dodać inne zasoby zależne (na przykład zasady inspekcji). Between każdego wdrożenia testu Usuń grupę zasobów, aby mieć pewność, że odpowiednio testowania zależności.


## <a name="next-steps"></a>Kolejne kroki
* Aby dowiedzieć się więcej o inspekcji akcji, zobacz [inspekcji operacji za pomocą Menedżera zasobów](resource-group-audit.md).
* Aby dowiedzieć się więcej o akcjach, aby określić błędy podczas wdrażania, zobacz [wyświetlić operacje wdrażania](resource-manager-deployment-operations.md).
