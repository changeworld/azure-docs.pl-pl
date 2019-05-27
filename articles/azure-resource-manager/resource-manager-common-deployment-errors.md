---
title: Rozwiąż typowe błędy wdrażania na platformie Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób rozwiązywania typowych problemów podczas wdrażania zasobów na platformie Azure przy użyciu usługi Azure Resource Manager.
services: azure-resource-manager
documentationcenter: ''
tags: top-support-issue
author: tfitzmac
manager: timlt
editor: tysonn
keywords: Błąd wdrażania, wdrażania platformy azure, wdrażanie na platformie azure
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/15/2019
ms.author: tomfitz
ms.openlocfilehash: f6ebeb1d9953311ad1cb85d8ab33c83d5e92d687
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "66128567"
---
# <a name="troubleshoot-common-azure-deployment-errors-with-azure-resource-manager"></a>Rozwiąż typowe błędy wdrażania na platformie Azure przy użyciu usługi Azure Resource Manager

W tym artykule opisano niektóre typowe błędy wdrażania na platformie Azure i zawiera informacje, aby naprawić błędy. Jeśli nie możesz znaleźć kod błędu dla błędu wdrożenia, zobacz [znaleźć kod błędu:](#find-error-code).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="error-codes"></a>Kody błędów

| Kod błędu | Ograniczanie ryzyka | Więcej informacji |
| ---------- | ---------- | ---------------- |
| AccountNameInvalid | Postępuj zgodnie z ograniczenia nazewnictwa dla kont magazynu. | [Rozpoznać nazwę konta magazynu](resource-manager-storage-account-name-errors.md) |
| AccountPropertyCannotBeSet | Sprawdź właściwości konta dostępnego magazynu. | [storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |
| AllocationFailed | Klaster lub regionie nie ma dostępu do zasobów lub nie obsługuje żądanego rozmiaru maszyny Wirtualnej. Ponów żądanie w późniejszym czasie, lub żądania innego rozmiaru maszyny Wirtualnej. | [Problemy z aprowizacji i alokacji dla systemu Linux](../virtual-machines/linux/troubleshoot-deployment-new-vm.md), [problemy z aprowizacji i alokacji w Windows](../virtual-machines/windows/troubleshoot-deployment-new-vm.md) i [Rozwiązywanie problemów z błędami alokacji](../virtual-machines/troubleshooting/allocation-failure.md)|
| AnotherOperationInProgress | Poczekaj na zakończenie operacji jednoczesnych. | |
| AuthorizationFailed | Twoje konto lub jednostki usługi nie ma wystarczające uprawnienia dostępu do ukończenia wdrażania. Sprawdź, Twoje konto należy do roli, a jego dostęp dla zakresu wdrożenia.<br><br>Ten błąd może występować, gdy dostawca wymagany zasób nie jest zarejestrowany. | [Kontrola dostępu oparta na rolach na platformie Azure](../role-based-access-control/role-assignments-portal.md)<br><br>[Rozwiąż rejestracji](resource-manager-register-provider-errors.md) |
| BadRequest | Wysłano wartości wdrożenia, które nie jest inna niż oczekiwana przez usługę Resource Manager. Sprawdź komunikat o stanie wewnętrzny, aby uzyskać pomoc dotyczącą rozwiązywania problemów. | [Dokumentacja dotycząca szablonów](/azure/templates/) i [obsługiwane lokalizacje](resource-group-authoring-templates.md#resource-location) |
| Konflikt | W przypadku żądania operacji, która nie jest dozwolona w bieżącym stanie zasobu. Na przykład zmiana rozmiaru dysku jest dozwolona tylko w przypadku tworzenia maszyny Wirtualnej lub po cofnięciu przydziału maszyny Wirtualnej. | |
| DeploymentActive | Poczekaj, aż współbieżnych wdrożenie do tej grupy zasobów, aby zakończyć. | |
| Niepowodzenia wdrożenia | Błąd niepowodzenia wdrożenia jest błąd ogólny, który nie zapewnia szczegółowe informacje, musisz rozwiązać błąd. Sprawdź szczegóły błędu dla kodu błędu, który zawiera więcej informacji. | [Znajdź kod błędu:](#find-error-code) |
| DeploymentQuotaExceeded | Jeśli przekroczysz limit 800 wdrożeń dla grupy zasobów, należy usunąć wdrożenia z historii, które nie są już potrzebne. Można usunąć wpisów z historii z [Usuń wdrożenie grupy az](/cli/azure/group/deployment#az-group-deployment-delete) wiersza polecenia platformy Azure lub [AzResourceGroupDeployment Usuń](/powershell/module/az.resources/remove-azresourcegroupdeployment) w programie PowerShell. Usuwanie wpisu z historii wdrożenia nie ma wpływu na zasoby wdrażania. | |
| DnsRecordInUse | Nazwa rekordu DNS musi być unikatowa. Podaj inną nazwę lub zmodyfikować istniejący rekord. | |
| ImageNotFound | Sprawdź ustawienia obrazu maszyny Wirtualnej. |  |
| InUseSubnetCannotBeDeleted | Błąd ten może wystąpić podczas próby zaktualizowania zasobu, ale żądanie jest przetwarzane przez usunięcie i utworzenie zasobu. Upewnij się określić wszystkie wartości bez zmian. | [Aktualizowanie zasobu](/azure/architecture/building-blocks/extending-templates/update-resource) |
| InvalidAuthenticationTokenTenant | Uzyskiwanie tokenu dostępu do odpowiedniego dzierżawy. Tokenu można pobierać tylko z której Twoje konto należy do dzierżawy. | |
| InvalidContentLink | Prawdopodobnie podjęto próbę połączyć zagnieżdżonych szablonów, które nie są dostępne. Sprawdź identyfikator URI podany dla zagnieżdżonych szablonów. Jeśli szablon istnieje w ramach konta magazynu, upewnij się, że identyfikator URI jest dostępny. Może być konieczne przekazać token sygnatury dostępu Współdzielonego. | [Połączone szablony](resource-group-linked-templates.md) |
| InvalidParameter | Jedna z wartości podanych dla zasobu nie jest zgodna z oczekiwaną wartością. Ten błąd może wynikać z wielu różnych warunków. Na przykład hasła mogą być niewystarczające lub nazwa obiektu blob może być nieprawidłowa. Sprawdź komunikat o błędzie, aby określić wartość, która musi zostać poprawione. | |
| InvalidRequestContent | Wartości wdrożenia zawierają wartości, które nie są oczekiwane lub brakuje wymaganych wartości. Upewnij się, wartości dla danego typu zasobu. | [Dokumentacja szablonu](/azure/templates/) |
| InvalidRequestFormat | Włączenie rejestrowania debugowania, podczas wykonywania wdrożenia, a następnie sprawdź treść żądania. | [Rejestrowanie debugowania](#enable-debug-logging) |
| InvalidResourceNamespace | Sprawdź przestrzeń nazw zasobów, które określiłeś w **typu** właściwości. | [Dokumentacja szablonu](/azure/templates/) |
| InvalidResourceReference | Zasób jeszcze nie istnieje lub jest niepoprawnie przywoływane. Sprawdź, czy należy dodać zależność. Upewnij się, że korzystanie z **odwołania** funkcja zawiera wymagane parametry dla danego scenariusza. | [Rozwiąż zależności](resource-manager-not-found-errors.md) |
| InvalidResourceType | Typ wyboru zasób określony w **typu** właściwości. | [Dokumentacja szablonu](/azure/templates/) |
| InvalidSubscriptionRegistrationState | Zarejestruj swoją subskrypcję dostawcy zasobów. | [Rozwiąż rejestracji](resource-manager-register-provider-errors.md) |
| InvalidTemplate | Sprawdź błędy składni szablonu. | [Nieprawidłowy szablon rozwiązania](resource-manager-invalid-template-errors.md) |
| InvalidTemplateCircularDependency | Usuń niepotrzebne zależności. | [Rozwiąż zależności cykliczne](resource-manager-invalid-template-errors.md#circular-dependency) |
| LinkedAuthorizationFailed | Sprawdź, czy Twoje konto należy do tej samej dzierżawie, co grupa zasobów, który jest wdrażany na. | |
| LinkedInvalidPropertyId | Identyfikator zasobu dla zasobu nie jest poprawnie rozpoznawania. Należy sprawdzić, możesz podać wartości wszystkich wymaganych dla Identyfikatora zasobu, w tym identyfikator subskrypcji, nazwę grupy zasobów, typ zasobu, nazwa zasobu nadrzędnego (jeśli jest to konieczne) i nazwę zasobu. | |
| LocationRequired | Podaj lokalizację zasobu bazy danych. | [Ustawianie lokalizacji](resource-group-authoring-templates.md#resource-location) |
| MismatchingResourceSegments | Upewnij się, że zagnieżdżone zasób ma poprawną liczbę segmentów w nazwie i typie. | [Rozwiąż segmenty zasobu](resource-manager-invalid-template-errors.md#incorrect-segment-lengths)
| MissingRegistrationForLocation | Sprawdź stan rejestracji dostawcy zasobów oraz obsługiwane lokalizacje. | [Rozwiąż rejestracji](resource-manager-register-provider-errors.md) |
| MissingSubscriptionRegistration | Zarejestruj swoją subskrypcję dostawcy zasobów. | [Rozwiąż rejestracji](resource-manager-register-provider-errors.md) |
| NoRegisteredProviderFound | Sprawdź stan rejestracji dostawcy zasobów. | [Rozwiąż rejestracji](resource-manager-register-provider-errors.md) |
| Nie odnaleziono | Może być próba wdrożenia zasób zależny równolegle z zasobu nadrzędnego. Sprawdź, czy wymagane można dodać zależności. | [Rozwiąż zależności](resource-manager-not-found-errors.md) |
| OperationNotAllowed | Wdrożenie próbuje operacji, która przekracza limit przydziału dla subskrypcji, grupy zasobów lub regionu. Jeśli to możliwe Sprawdź, czy wdrożenie w ramach limitów przydziału. W przeciwnym razie należy wziąć pod uwagę żądania zmiany limity przydziału. | [Rozwiąż przydziałów](resource-manager-quota-errors.md) |
| ParentResourceNotFound | Upewnij się, że zasób nadrzędny znajduje się przed utworzeniem elementu podrzędnego zasobów. | [Rozwiąż zasób nadrzędny](resource-manager-parent-resource-errors.md) |
| PasswordTooLong | Być może wybrano hasło zbyt wiele znaków lub mogą mieć przekonwertowane wartość hasła na bezpieczny ciąg przed przekazaniem go jako parametr. Jeśli szablon zawiera **bezpieczny ciąg** parametru, nie trzeba przekonwertować wartości na bezpieczny ciąg. Podaj hasło jako tekst. |  |
| PrivateIPAddressInReservedRange | Określony adres IP obejmuje zakres adresów, wymagane przez platformę Azure. Zmienianie adresu IP, aby uniknąć zarezerwowany zakres. | [Adresy IP](../virtual-network/virtual-network-ip-addresses-overview-arm.md) |
| PrivateIPAddressNotInSubnet | Określony adres IP jest spoza zakresu podsieci. Zmień adres IP, który wchodzi w zakres podsieci. | [Adresy IP](../virtual-network/virtual-network-ip-addresses-overview-arm.md) |
| PropertyChangeNotAllowed | Nie można zmienić niektóre właściwości wdrożony zasób. Podczas aktualizowania zasobu, należy ograniczyć zmiany właściwości dozwolone. | [Aktualizowanie zasobu](/azure/architecture/building-blocks/extending-templates/update-resource) |
| RequestDisallowedByPolicy | Twoja subskrypcja obejmuje zasady zasobów, które uniemożliwiają akcję, którą próbujesz wykonać podczas wdrażania. Znaleźć zasady, która blokuje akcji. Jeśli to możliwe zmodyfikuj wdrożenie spełnia ograniczenia z zasad. | [Rozwiąż zasad](resource-manager-policy-requestdisallowedbypolicy-error.md) |
| ReservedResourceName | Podaj nazwę zasobu, który nie zawiera nazwą zastrzeżoną. | [Nazw zarezerwowanych zasobów](resource-manager-reserved-resource-name.md) |
| ResourceGroupBeingDeleted | Oczekiwanie na usunięcie zakończyć. | |
| ResourceGroupNotFound | Sprawdź nazwę docelowej grupy zasobów dla wdrożenia. Musi już istnieć w subskrypcji. Sprawdź kontekst subskrypcji. | [Interfejs wiersza polecenia Azure](/cli/azure/account?#az-account-set) [programu PowerShell](/powershell/module/Az.Accounts/Set-AzContext) |
| ResourceNotFound | Wdrożenie odwołuje się do zasobu, którego nie można rozpoznać. Upewnij się, że korzystanie z **odwołania** funkcja zawiera parametrów wymaganych dla danego scenariusza. | [Rozpoznawania odwołań](resource-manager-not-found-errors.md) |
| ResourceQuotaExceeded | Wdrożenie próbuje utworzyć zasoby, które przekraczają limit przydziału dla subskrypcji, grupy zasobów lub regionu. Jeśli to możliwe poprawić swoją infrastrukturę, aby w ramach limitów przydziału. W przeciwnym razie należy wziąć pod uwagę żądania zmiany limity przydziału. | [Rozwiąż przydziałów](resource-manager-quota-errors.md) |
| SkuNotAvailable | Wybierz jednostkę SKU (np. rozmiar maszyny Wirtualnej), który jest dostępny dla lokalizacji, które wybrałeś. | [Rozwiąż jednostki SKU](resource-manager-sku-not-available-errors.md) |
| StorageAccountAlreadyExists | Podaj unikatową nazwę konta magazynu. | [Rozpoznać nazwę konta magazynu](resource-manager-storage-account-name-errors.md)  |
| StorageAccountAlreadyTaken | Podaj unikatową nazwę konta magazynu. | [Rozpoznać nazwę konta magazynu](resource-manager-storage-account-name-errors.md) |
| StorageAccountNotFound | Sprawdź subskrypcji, grupy zasobów i nazwy konta magazynu, do którego próbujesz użyć. | |
| SubnetsNotInSameVnet | Maszyna wirtualna może mieć tylko jedną sieć wirtualną. W przypadku wdrażania kilka kart sieciowych, upewnij się, że należą one do tej samej sieci wirtualnej. | [Multiple NICs](../virtual-machines/windows/multiple-nics.md) |
| TemplateResourceCircularDependency | Usuń niepotrzebne zależności. | [Rozwiąż zależności cykliczne](resource-manager-invalid-template-errors.md#circular-dependency) |
| TooManyTargetResourceGroups | Zmniejsz liczbę grup zasobów dla pojedynczego wdrożenia. | [Wdrażanie krzyżowe w grupach zasobów](resource-manager-cross-resource-group-deployment.md) |

## <a name="find-error-code"></a>Znajdź kod błędu:

Istnieją dwa typy błędów, które mogą odbierać:

* błędy sprawdzania poprawności
* błędy związane z wdrażaniem

Błędy sprawdzania poprawności wynikają z scenariusze, które można określić przed przystąpieniem do wdrożenia. Są to na przykład błędy składniowe w szablonie lub próby wdrożenia zasobów, które przekraczają limity przydziału w ramach subskrypcji. Błędy związane z wdrażaniem wynikają z warunków, które występują podczas procesu wdrażania. Obejmują one próby uzyskania dostępu do zasobu, który jest wdrażany równolegle.

Oba rodzaje błędów zwracają kod błędu, którego należy użyć do rozwiązania problemów z wdrożeniem. Oba rodzaje błędy są wyświetlane w [dziennika aktywności](resource-group-audit.md). Błędy weryfikacji nie są jednak wyświetlane w historii wdrażania, ponieważ wdrożenie nie jest w takim przypadku rozpoczynane.

### <a name="validation-errors"></a>Błędy weryfikacji

Podczas wdrażania za pośrednictwem portalu, zobaczysz błąd sprawdzania poprawności po przesłaniu własnymi wartościami.

![Pokaż błąd sprawdzania poprawności w portalu](./media/resource-manager-common-deployment-errors/validation-error.png)

Zaznacz wiadomość, aby uzyskać więcej informacji. Na poniższej ilustracji widać **InvalidTemplateDeployment** błąd i komunikat informujący, zasady jest blokowany wdrożenia.

![Pokaż szczegóły sprawdzania poprawności](./media/resource-manager-common-deployment-errors/validation-details.png)

### <a name="deployment-errors"></a>błędy związane z wdrażaniem

Podczas operacji pozytywnie zweryfikowane, ale nie powiedzie się podczas wdrażania, otrzymasz błąd wdrażania.

Aby wyświetlić kody błędów wdrażania i wiadomości za pomocą programu PowerShell, należy użyć:

```azurepowershell-interactive
(Get-AzResourceGroupDeploymentOperation -DeploymentName exampledeployment -ResourceGroupName examplegroup).Properties.statusMessage
```

Aby wyświetlić kody błędów wdrażania i komunikatów za pomocą wiersza polecenia platformy Azure, należy użyć:

```azurecli-interactive
az group deployment operation list --name exampledeployment -g examplegroup --query "[*].properties.statusMessage"
```

W portalu wybierz powiadomienie.

![Błąd powiadomienia](./media/resource-manager-common-deployment-errors/notification.png)

Zostanie wyświetlone więcej szczegółów dotyczących wdrożenia. Wybierz opcję, aby uzyskać więcej informacji o błędzie.

![Wdrażanie nie powiodło się](./media/resource-manager-common-deployment-errors/deployment-failed.png)

Zostanie wyświetlony komunikat o błędzie i kody błędów. Należy zauważyć, że istnieją dwa kody błędów. Pierwszy kod błędu: (**niepowodzenia wdrożenia**) jest błąd ogólny, który nie zapewnia szczegółowe informacje, musisz rozwiązać błąd. Drugi kod błędu: (**StorageAccountNotFound**) zawiera szczegółowe informacje, potrzebujesz. 

![szczegóły błędu](./media/resource-manager-common-deployment-errors/error-details.png)

## <a name="enable-debug-logging"></a>Włączenie rejestrowania debugowania

Czasami potrzebujesz więcej informacji na temat żądań i odpowiedzi, aby dowiedzieć się, co poszło źle. Podczas wdrażania możesz poprosić, że dodatkowe informacje są rejestrowane podczas wdrażania. 

### <a name="powershell"></a>PowerShell

W programie PowerShell, należy ustawić **DeploymentDebugLogLevel** parametr do wszystkich, obsah ResponseContent lub RequestContent.

```powershell
New-AzResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName examplegroup `
  -TemplateFile c:\Azure\Templates\storage.json `
  -DeploymentDebugLogLevel All
```

Sprawdź żądanie zawartości za pomocą następującego polecenia cmdlet:

```powershell
(Get-AzResourceGroupDeploymentOperation `
-DeploymentName exampledeployment `
-ResourceGroupName examplegroup).Properties.request `
| ConvertTo-Json
```

Lub zawartość odpowiedzi:

```powershell
(Get-AzResourceGroupDeploymentOperation `
-DeploymentName exampledeployment `
-ResourceGroupName examplegroup).Properties.response `
| ConvertTo-Json
```

Te informacje może pomóc w określeniu, czy wartość w szablonie ustawiono jest niepoprawnie.

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Obecnie nie obsługuje interfejsu wiersza polecenia platformy Azure, włączenie rejestrowania debugowania, ale można pobrać rejestrowania debugowania.

Sprawdź operacji wdrażania przy użyciu następującego polecenia:

```azurecli
az group deployment operation list \
  --resource-group examplegroup \
  --name exampledeployment
```

Sprawdź żądanie zawartości za pomocą następującego polecenia:

```azurecli
az group deployment operation list \
  --name exampledeployment \
  -g examplegroup \
  --query [].properties.request
```

Sprawdź odpowiedzi zawartości za pomocą następującego polecenia:

```azurecli
az group deployment operation list \
  --name exampledeployment \
  -g examplegroup \
  --query [].properties.response
```

### <a name="nested-template"></a>Zagnieżdżony szablon

Aby rejestrować informacje debugowania dla zagnieżdżonych szablonów, należy użyć **debugSetting** elementu.

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

W niektórych przypadkach Najprostszym sposobem rozwiązywania problemów z szablonu jest przetestowanie jej części. Możesz utworzyć uproszczona szablon, który pozwala skupić się na części, które Twoim zdaniem jest przyczyną błędu. Na przykład załóżmy, że otrzymujesz błąd podczas odwoływania się do zasobu. Zamiast radzenia sobie z szablonem całego, należy utworzyć szablon, który zwraca składnik, który może być przyczyną problemu. Możesz określić, czy przechodząc w parametrach prawo poprawnie, za pomocą funkcji szablonu i pobieranie zasobu oczekujesz, że może pomóc.

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

Możesz też Załóżmy, że występują błędy związane z wdrażaniem, które Twoim zdaniem są powiązane próbę ustawienia nieprawidłowej zależności. Testowanie szablonu przez podzielenie go na uproszczonej szablonów. Najpierw Utwórz szablon, który wdraża pojedynczego zasobu (np. SQL Server). Po upewnieniu się, że masz poprawnie zdefiniowana zasobu, Dodaj zasób, który zależy od niego (np. bazy danych SQL). Jeśli masz te dwa zasoby poprawnie zdefiniowana, należy dodać inne zasoby zależne (na przykład zasady inspekcji). Między każdym wdrożeniu testu Usuń grupę zasobów, aby upewnić się, że testy odpowiednio zależności.


## <a name="next-steps"></a>Kolejne kroki

* Aby przejść przez samouczek rozwiązywania problemów, zobacz [samouczka: Rozwiązywanie problemów z wdrożeniami szablonu usługi Resource Manager](./resource-manager-tutorial-troubleshoot.md)
* Aby dowiedzieć się więcej na temat inspekcji akcji, zobacz [inspekcji operacji przy użyciu usługi Resource Manager](resource-group-audit.md).
* Aby dowiedzieć się więcej o akcjach, aby określić błędy podczas wdrażania, zobacz [wyświetlanie operacji wdrażania](resource-manager-deployment-operations.md).
