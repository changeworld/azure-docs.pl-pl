---
title: Rozwiązywanie typowych błędów wdrażania platformy Azure | Microsoft Docs
description: Opisuje sposób rozwiązywania typowych błędów podczas wdrażania zasobów na platformie Azure przy użyciu Azure Resource Manager.
tags: top-support-issue
author: tfitzmac
keywords: Błąd wdrażania, wdrażanie platformy Azure, wdrażanie na platformie Azure
ms.service: azure-resource-manager
ms.topic: troubleshooting
ms.date: 10/04/2019
ms.author: tomfitz
ms.openlocfilehash: 185570992ad0308b500da30bca212a0495bcb0fa
ms.sourcegitcommit: be344deef6b37661e2c496f75a6cf14f805d7381
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/07/2019
ms.locfileid: "72001638"
---
# <a name="troubleshoot-common-azure-deployment-errors-with-azure-resource-manager"></a>Rozwiązywanie typowych błędów wdrażania platformy Azure za pomocą Azure Resource Manager

W tym artykule opisano niektóre typowe błędy wdrażania platformy Azure i przedstawiono informacje umożliwiające rozwiązanie tych błędów. Jeśli nie możesz znaleźć kodu błędu dla błędu wdrożenia, zobacz [Znajdź kod błędu](#find-error-code).

Jeśli szukasz informacji o kodzie błędu i te informacje nie zostały podane w tym artykule, powiadom nas o tym. W dolnej części tej strony możesz opuścić opinię. Opinie są śledzone za pomocą usługi GitHub.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="error-codes"></a>Kody błędów

| Kod błędu | Ograniczenie | Więcej informacji |
| ---------- | ---------- | ---------------- |
| AccountNameInvalid | Postępuj zgodnie z ograniczeniami nazw dla kont magazynu. | [Rozpoznawanie nazwy konta magazynu](resource-manager-storage-account-name-errors.md) |
| AccountPropertyCannotBeSet | Sprawdź dostępne właściwości konta magazynu. | [storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |
| AllocationFailed | W klastrze lub regionie nie ma dostępnych zasobów lub nie można obsłużyć żądanego rozmiaru maszyny wirtualnej. Ponów próbę żądania w późniejszym czasie lub zażądaj innego rozmiaru maszyny wirtualnej. | [Problemy z obsługą i alokacją dla](../virtual-machines/linux/troubleshoot-deployment-new-vm.md)problemów z systemem Linux, [aprowizacji i alokacji dla systemu Windows](../virtual-machines/windows/troubleshoot-deployment-new-vm.md) i [Rozwiązywanie problemów z błędami alokacji](../virtual-machines/troubleshooting/allocation-failure.md)|
| AnotherOperationInProgress | Poczekaj na zakończenie operacji współbieżności. | |
| AuthorizationFailed | Twoje konto lub jednostka usługi nie ma wystarczających uprawnień, aby ukończyć wdrażanie. Sprawdź rolę, do której należy konto, i dostęp do zakresu wdrożenia.<br><br>Ten błąd może pojawić się, jeśli nie zarejestrowano wymaganego dostawcy zasobów. | [Access Control oparte na rolach na platformie Azure](../role-based-access-control/role-assignments-portal.md)<br><br>[Rozwiązywanie rejestracji](resource-manager-register-provider-errors.md) |
| Nieprawidłowego żądania | Wartości wdrożeń są wysyłane, które nie są zgodne z oczekiwaniami Menedżer zasobów. Sprawdź wewnętrzny komunikat o stanie, aby uzyskać pomoc dotyczącą rozwiązywania problemów. | [Odwołania do szablonu](/azure/templates/) i [obsługiwane lokalizacje](resource-location.md) |
| Kolizj | Żądasz operacji, która nie jest dozwolona w bieżącym stanie zasobu. Na przykład zmiany rozmiarów dysków są dozwolone tylko w przypadku tworzenia maszyny wirtualnej lub po cofnięciu przydziału maszyny wirtualnej. | |
| DeploymentActiveAndUneditable | Poczekaj na ukończenie współbieżnego wdrażania tej grupy zasobów. | |
| DeploymentNameInvalidCharacters | Nazwa wdrożenia może zawierać tylko litery, cyfry, znaki "-", "." lub "_". | |
| DeploymentNameLengthLimitExceeded | Nazwy wdrożeń są ograniczone do 64 znaków.  | |
| DeploymentFailed | Błąd DeploymentFailed jest ogólnym błędem, który nie zawiera szczegółów potrzebnych do rozwiązania błędu. Aby uzyskać więcej informacji, zobacz szczegóły błędu dotyczące kodu błędu. | [Znajdź kod błędu](#find-error-code) |
| DeploymentQuotaExceeded | Jeśli osiągnięto limit 800 wdrożeń dla każdej grupy zasobów, należy usunąć wdrożenia z historii, które nie są już potrzebne. | [Usuń błąd, gdy liczba wdrożeń przekracza 800](deployment-quota-exceeded.md) |
| DnsRecordInUse | Nazwa rekordu DNS musi być unikatowa. Wprowadź inną nazwę. | |
| ImageNotFound | Sprawdź ustawienia obrazu maszyny wirtualnej. |  |
| InUseSubnetCannotBeDeleted | Ten błąd może wystąpić podczas próby zaktualizowania zasobu, a żądanie jest przetwarzane przez usunięcie i utworzenie zasobu. Upewnij się, że określono wszystkie niezmienione wartości. | [Aktualizuj zasób](/azure/architecture/building-blocks/extending-templates/update-resource) |
| InvalidAuthenticationTokenTenant | Uzyskaj token dostępu dla odpowiedniej dzierżawy. Możesz uzyskać tylko token z dzierżawy, do której należy Twoje konto. | |
| InvalidContentLink | Najprawdopodobniej podjęto próbę połączenia z zagnieżdżonym szablonem, który nie jest dostępny. Sprawdź dokładnie identyfikator URI podany dla szablonu zagnieżdżonego. Jeśli szablon istnieje na koncie magazynu, upewnij się, że identyfikator URI jest dostępny. Może być konieczne przekazanie tokenu SAS. Obecnie nie można połączyć się z szablonem znajdującym się na koncie magazynu za [zaporą usługi Azure Storage](../storage/common/storage-network-security.md). Rozważ przeniesienie szablonu do innego repozytorium, takiego jak GitHub. | [Połączone szablony](resource-group-linked-templates.md) |
| InvalidDeploymentLocation | Podczas wdrażania na poziomie subskrypcji podano inną lokalizację dla wcześniej używanej nazwy wdrożenia. | [Wdrożenia na poziomie subskrypcji](deploy-to-subscription.md) |
| Nieprawidłowy parametr | Jedna z wartości podanych dla zasobu jest niezgodna z oczekiwaną wartością. Ten błąd może wynikać z wielu różnych warunków. Na przykład hasło może być niewystarczające lub nazwa obiektu BLOB może być niepoprawna. Komunikat o błędzie powinien wskazywać, którą wartość należy poprawić. | |
| InvalidRequestContent | Wartości wdrożenia obejmują wartości, które nie zostały rozpoznane lub brakuje wymaganych wartości. Potwierdź wartości dla typu zasobu. | [Dokumentacja szablonu](/azure/templates/) |
| InvalidRequestFormat | Włącz rejestrowanie debugowania podczas uruchamiania wdrożenia i sprawdź zawartość żądania. | [Rejestrowanie debugowania](#enable-debug-logging) |
| InvalidResourceNamespace | Sprawdź przestrzeń nazw zasobów określoną we właściwości **Typ** . | [Dokumentacja szablonu](/azure/templates/) |
| InvalidResourceReference | Zasób jeszcze nie istnieje lub jest nieprawidłowo przywoływany. Sprawdź, czy musisz dodać zależność. Sprawdź, czy użycie funkcji **Reference** obejmuje parametry wymagane w danym scenariuszu. | [Rozwiązywanie zależności](resource-manager-not-found-errors.md) |
| InvalidResourceType | Sprawdź typ zasobu określony we właściwości **Typ** . | [Dokumentacja szablonu](/azure/templates/) |
| InvalidSubscriptionRegistrationState | Zarejestruj swoją subskrypcję u dostawcy zasobów. | [Rozwiązywanie rejestracji](resource-manager-register-provider-errors.md) |
| InvalidTemplate | Sprawdź składnię szablonu pod kątem błędów. | [Rozwiąż nieprawidłowy szablon](resource-manager-invalid-template-errors.md) |
| InvalidTemplateCircularDependency | Usuń niepotrzebne zależności. | [Rozwiązywanie zależności cyklicznych](resource-manager-invalid-template-errors.md#circular-dependency) |
| LinkedAuthorizationFailed | Sprawdź, czy Twoje konto należy do tej samej dzierżawy, w której znajduje się grupa zasobów, w której jest wdrażana. | |
| LinkedInvalidPropertyId | Identyfikator zasobu dla zasobu nie jest rozpoznawany prawidłowo. Upewnij się, że podano wszystkie wymagane wartości dla identyfikatora zasobu, w tym identyfikator subskrypcji, nazwę grupy zasobów, typ zasobu, nadrzędną nazwę zasobu (w razie potrzeby) i nazwę zasobu. | |
| LocationRequired | Podaj lokalizację zasobu. | [Ustaw lokalizację](resource-location.md) |
| MismatchingResourceSegments | Upewnij się, że zagnieżdżony zasób ma poprawną liczbę segmentów w nazwie i typie. | [Rozpoznaj segmenty zasobów](resource-manager-invalid-template-errors.md#incorrect-segment-lengths)
| MissingRegistrationForLocation | Sprawdź stan rejestracji dostawcy zasobów i obsługiwane lokalizacje. | [Rozwiązywanie rejestracji](resource-manager-register-provider-errors.md) |
| MissingSubscriptionRegistration | Zarejestruj swoją subskrypcję u dostawcy zasobów. | [Rozwiązywanie rejestracji](resource-manager-register-provider-errors.md) |
| NoRegisteredProviderFound | Sprawdź stan rejestracji dostawcy zasobów. | [Rozwiązywanie rejestracji](resource-manager-register-provider-errors.md) |
| NotFound | Być może podjęto próbę wdrożenia zasobu zależnego równolegle z zasobem nadrzędnym. Sprawdź, czy musisz dodać zależność. | [Rozwiązywanie zależności](resource-manager-not-found-errors.md) |
| OperationNotAllowed | Wdrożenie próbuje wykonać operację, która przekracza limit przydziału dla subskrypcji, grupy zasobów lub regionu. Jeśli to możliwe, Popraw wdrożenie, aby zachować jego limit. W przeciwnym razie Rozważ zażądanie zmiany limitów przydziału. | [Rozpoznaj limity przydziału](resource-manager-quota-errors.md) |
| ParentResourceNotFound | Przed utworzeniem zasobów podrzędnych upewnij się, że istnieje zasób nadrzędny. | [Rozwiąż zasób nadrzędny](resource-manager-parent-resource-errors.md) |
| PasswordTooLong | Być może wybrano zbyt wiele znaków hasła lub przekonwertowano wartość hasła na bezpieczny ciąg przed przekazaniem go jako parametru. Jeśli szablon zawiera parametr **Secure String** , nie trzeba konwertować wartości na bezpieczny ciąg. Podaj wartość hasła jako tekst. |  |
| PrivateIPAddressInReservedRange | Określony adres IP zawiera zakres adresów wymagany przez platformę Azure. Zmień adres IP, aby uniknąć zarezerwowanego zakresu. | [Adresy IP](../virtual-network/virtual-network-ip-addresses-overview-arm.md) |
| PrivateIPAddressNotInSubnet | Określony adres IP znajduje się poza zakresem podsieci. Zmień adres IP, aby mieścił się w zakresie podsieci. | [Adresy IP](../virtual-network/virtual-network-ip-addresses-overview-arm.md) |
| PropertyChangeNotAllowed | Niektórych właściwości nie można zmienić we wdrożonym zasobie. Podczas aktualizowania zasobu należy ograniczyć zmiany do dozwolonych właściwości. | [Aktualizuj zasób](/azure/architecture/building-blocks/extending-templates/update-resource) |
| RequestDisallowedByPolicy | Twoja subskrypcja obejmuje zasady zasobów, które uniemożliwiają wykonanie akcji podczas wdrażania. Znajdź zasady blokujące akcję. Jeśli to możliwe, Zmień wdrożenie, aby spełniało ograniczenia z zasad. | [Rozwiązywanie zasad](resource-manager-policy-requestdisallowedbypolicy-error.md) |
| ReservedResourceName | Podaj nazwę zasobu, która nie zawiera zastrzeżonej nazwy. | [Zarezerwowane nazwy zasobów](resource-manager-reserved-resource-name.md) |
| ResourceGroupBeingDeleted | Poczekaj na zakończenie usuwania. | |
| ResourceGroupNotFound | Sprawdź nazwę docelowej grupy zasobów dla wdrożenia. Docelowa Grupa zasobów musi już istnieć w Twojej subskrypcji. Sprawdź kontekst subskrypcji. | [PowerShell](/powershell/module/Az.Accounts/Set-AzContext) [interfejsu wiersza polecenia platformy Azure](/cli/azure/account?#az-account-set) |
| ResourceNotFound | Wdrożenie odwołuje się do zasobu, którego nie można rozpoznać. Sprawdź, czy użycie funkcji **Reference** obejmuje parametry wymagane w danym scenariuszu. | [Rozpoznaj odwołania](resource-manager-not-found-errors.md) |
| ResourceQuotaExceeded | Wdrożenie próbuje utworzyć zasoby, które przekraczają limit przydziału dla subskrypcji, grupy zasobów lub regionu. Jeśli to możliwe, Popraw infrastrukturę, aby zachować te limity. W przeciwnym razie Rozważ zażądanie zmiany limitów przydziału. | [Rozpoznaj limity przydziału](resource-manager-quota-errors.md) |
| SkuNotAvailable | Wybierz jednostkę SKU (na przykład rozmiar maszyny wirtualnej), która jest dostępna dla wybranej lokalizacji. | [Rozpoznaj jednostkę SKU](resource-manager-sku-not-available-errors.md) |
| StorageAccountAlreadyExists | Podaj unikatową nazwę konta magazynu. | [Rozpoznawanie nazwy konta magazynu](resource-manager-storage-account-name-errors.md)  |
| StorageAccountAlreadyTaken | Podaj unikatową nazwę konta magazynu. | [Rozpoznawanie nazwy konta magazynu](resource-manager-storage-account-name-errors.md) |
| StorageAccountNotFound | Sprawdź subskrypcję, grupę zasobów i nazwę konta magazynu, którego próbujesz użyć. | |
| SubnetsNotInSameVnet | Maszyna wirtualna może mieć tylko jedną sieć wirtualną. Podczas wdrażania kilku kart sieciowych upewnij się, że należą one do tej samej sieci wirtualnej. | [Wiele kart sieciowych](../virtual-machines/windows/multiple-nics.md) |
| TemplateResourceCircularDependency | Usuń niepotrzebne zależności. | [Rozwiązywanie zależności cyklicznych](resource-manager-invalid-template-errors.md#circular-dependency) |
| TooManyTargetResourceGroups | Zmniejsz liczbę grup zasobów dla jednego wdrożenia. | [Wdrożenie między grupami zasobów](resource-manager-cross-resource-group-deployment.md) |

## <a name="find-error-code"></a>Znajdź kod błędu

Istnieją dwa typy błędów, które można odbierać:

* błędy walidacji
* błędy wdrożenia

Błędy walidacji powstają w scenariuszach, które można ustalić przed wdrożeniem. Zawierają one błędy składniowe w szablonie lub próbują wdrożyć zasoby, które przekraczają limity przydziału subskrypcji. Błędy wdrażania powstają na podstawie warunków występujących w procesie wdrażania. Obejmują one próby dostępu do zasobu, który jest wdrażany równolegle.

Oba typy błędów zwracają kod błędu, który jest używany do rozwiązywania problemów z wdrożeniem. Oba typy błędów pojawiają się w [dzienniku aktywności](resource-group-audit.md). Błędy sprawdzania poprawności nie są jednak wyświetlane w historii wdrożenia, ponieważ wdrożenie nigdy nie zostało uruchomione.

### <a name="validation-errors"></a>Błędy walidacji

Podczas wdrażania za pomocą portalu po przesłaniu wartości zostanie wyświetlony komunikat o błędzie walidacji.

![Pokaż błąd walidacji portalu](./media/resource-manager-common-deployment-errors/validation-error.png)

Wybierz komunikat, aby uzyskać więcej szczegółów. Na poniższej ilustracji zostanie wyświetlony błąd **InvalidTemplateDeployment** i komunikat wskazujący, że wdrożenie zasad zostało zablokowane.

![Pokaż szczegóły walidacji](./media/resource-manager-common-deployment-errors/validation-details.png)

### <a name="deployment-errors"></a>Błędy wdrożenia

Gdy operacja przejdzie do walidacji, ale podczas wdrażania nie powiedzie się, zostanie wyświetlony błąd wdrażania.

Aby wyświetlić kody błędów wdrażania i komunikaty przy użyciu programu PowerShell, użyj polecenia:

```azurepowershell-interactive
(Get-AzResourceGroupDeploymentOperation -DeploymentName exampledeployment -ResourceGroupName examplegroup).Properties.statusMessage
```

Aby wyświetlić kody błędów wdrażania i komunikaty za pomocą interfejsu wiersza polecenia platformy Azure, użyj polecenia:

```azurecli-interactive
az group deployment operation list --name exampledeployment -g examplegroup --query "[*].properties.statusMessage"
```

W portalu wybierz powiadomienie.

![błąd powiadomienia](./media/resource-manager-common-deployment-errors/notification.png)

Zobaczysz więcej szczegółowych informacji o wdrożeniu. Wybierz opcję, aby znaleźć więcej informacji o błędzie.

![wdrożenie nie powiodło się](./media/resource-manager-common-deployment-errors/deployment-failed.png)

Zobaczysz komunikat o błędzie i kody błędów. Zwróć uwagę na to, że istnieją dwa kody błędów. Pierwszy kod błędu (**DeploymentFailed**) jest ogólnym błędem, który nie zawiera szczegółów potrzebnych do rozwiązania błędu. Drugi kod błędu (**StorageAccountNotFound**) zawiera szczegółowe informacje, które są potrzebne.

![Szczegóły błędu](./media/resource-manager-common-deployment-errors/error-details.png)

## <a name="enable-debug-logging"></a>Włącz rejestrowanie debugowania

Czasami potrzebujesz więcej informacji na temat żądania i odpowiedzi, aby dowiedzieć się, co poszło źle. Podczas wdrażania możesz poprosić o zarejestrowanie dodatkowych informacji podczas wdrażania.

### <a name="powershell"></a>PowerShell

W programie PowerShell ustaw parametr **DeploymentDebugLogLevel** na wartość All, ResponseContent lub RequestContent.

```powershell
New-AzResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName examplegroup `
  -TemplateFile c:\Azure\Templates\storage.json `
  -DeploymentDebugLogLevel All
```

Przejrzyj żądanie zawartości za pomocą następującego polecenia cmdlet:

```powershell
(Get-AzResourceGroupDeploymentOperation `
-DeploymentName exampledeployment `
-ResourceGroupName examplegroup).Properties.request `
| ConvertTo-Json
```

Lub zawartość odpowiedzi z:

```powershell
(Get-AzResourceGroupDeploymentOperation `
-DeploymentName exampledeployment `
-ResourceGroupName examplegroup).Properties.response `
| ConvertTo-Json
```

Te informacje mogą pomóc w ustaleniu, czy wartość w szablonie jest nieprawidłowo ustawiona.

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Obecnie interfejs wiersza polecenia platformy Azure nie obsługuje włączania rejestrowania debugowania, ale można pobrać funkcję rejestrowania debugowania.

Przejrzyj operacje wdrażania przy użyciu następującego polecenia:

```azurecli
az group deployment operation list \
  --resource-group examplegroup \
  --name exampledeployment
```

Przeanalizuj zawartość żądania przy użyciu następującego polecenia:

```azurecli
az group deployment operation list \
  --name exampledeployment \
  -g examplegroup \
  --query [].properties.request
```

Zapoznaj się z zawartością odpowiedzi przy użyciu następującego polecenia:

```azurecli
az group deployment operation list \
  --name exampledeployment \
  -g examplegroup \
  --query [].properties.response
```

### <a name="nested-template"></a>Szablon zagnieżdżony

Aby rejestrować informacje debugowania dla szablonu zagnieżdżonego, należy użyć elementu **debugSetting** .

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

W niektórych przypadkach Najprostszym sposobem rozwiązywania problemów z szablonem jest przetestowanie jego części. Można utworzyć uproszczony szablon, który umożliwia skoncentrowanie się na części, która jest przyczyną błędu. Na przykład załóżmy, że podczas odwoływania się do zasobu pojawia się błąd. Zamiast korzystać z całego szablonu, Utwórz szablon, który zwraca część, która może być przyczyną problemu. Może pomóc w ustaleniu, czy przekazujesz odpowiednie parametry, używając poprawnie funkcji szablonu i uzyskać oczekiwany zasób.

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

Lub Załóżmy, że występują błędy wdrażania, które są związane z nieprawidłowo ustawionymi zależnościami. Przetestuj szablon, dzieląc go na uproszczone szablony. Najpierw Utwórz szablon, który wdraża tylko jeden zasób (taki jak SQL Server). Jeśli masz pewność, że ten zasób jest prawidłowo zdefiniowany, Dodaj do niego zależny zasób (taki jak SQL Database). W przypadku poprawnego zdefiniowania tych dwóch zasobów należy dodać inne zależne zasoby (takie jak zasady inspekcji). W ramach każdego wdrożenia testu Usuń grupę zasobów, aby upewnić się, że są odpowiednie testy zależności.


## <a name="next-steps"></a>Następne kroki

* Aby przejść przez samouczek rozwiązywania problemów, zobacz [Samouczek: Rozwiązywanie problemów z wdrożeniami szablonów Menedżer zasobów](./resource-manager-tutorial-troubleshoot.md)
* Aby dowiedzieć się więcej o akcjach inspekcji, zobacz [Inspekcja operacji przy użyciu Menedżer zasobów](resource-group-audit.md).
* Aby dowiedzieć się więcej o akcjach dotyczących określania błędów podczas wdrażania, zobacz [Wyświetlanie operacji wdrażania](resource-manager-deployment-operations.md).
