---
title: Rozwiązywanie typowych błędów związanych z wdrażaniem
description: W tym artykule opisano sposób rozwiązywania typowych błędów podczas wdrażania zasobów na platformie Azure przy użyciu usługi Azure Resource Manager.
tags: top-support-issue
ms.topic: troubleshooting
ms.date: 10/04/2019
ms.openlocfilehash: bc1568c53cdb5518f694d77a2f28f3cf77296ee2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460385"
---
# <a name="troubleshoot-common-azure-deployment-errors-with-azure-resource-manager"></a>Usuwanie typowych błędów wdrożeń na platformie Azure przy użyciu usługi Azure Resource Manager

W tym artykule opisano niektóre typowe błędy wdrażania platformy Azure i zawiera informacje, aby rozwiązać błędy. Jeśli nie możesz znaleźć kodu błędu wdrożenia, zobacz [Znajdowanie kodu błędu](#find-error-code).

Jeśli szukasz informacji o kodzie błędu, a te informacje nie są podane w tym artykule, pouczaj nas o tym. Na dole tej strony możesz zostawić opinię. Opinie są śledzone za pomocą problemów z githubem.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="error-codes"></a>Kody błędów

| Kod błędu | Środki zaradcze | Więcej informacji |
| ---------- | ---------- | ---------------- |
| AccountNameInvalid | Postępuj zgodnie z ograniczeniami nazewnictwa dla kont magazynu. | [Rozwiązywanie problemu z nazwą konta magazynu](error-storage-account-name.md) |
| AccountPropertyCannotBeSet | Sprawdź dostępne właściwości konta magazynu. | [konta przechowywania](/azure/templates/microsoft.storage/storageaccounts) |
| Alokacja Nieugięta | Klaster lub region nie ma dostępnych zasobów lub nie może obsługiwać żądanego rozmiaru maszyny Wirtualnej. Ponów próbę żądania w późniejszym czasie lub zażądaj innego rozmiaru maszyny Wirtualnej. | [Problemy z inicjowaniem obsługi administracyjnej i alokacji dla systemu Linux,](../../virtual-machines/linux/troubleshoot-deployment-new-vm.md) [obsługi administracyjnej i alokacji dla systemu Windows](../../virtual-machines/windows/troubleshoot-deployment-new-vm.md) i [rozwiązywania problemów z błędami alokacji](../../virtual-machines/troubleshooting/allocation-failure.md)|
| InnyOperationInProgress | Poczekaj na zakończenie równoczesnych operacji. | |
| AuthorizationFailed (Nieuwzruszony) | Twoje konto lub jednostki usługi nie ma wystarczającego dostępu do ukończenia wdrożenia. Sprawdź rolę, do której należy twoje konto, i jego dostęp do zakresu wdrożenia.<br><br>Ten błąd może wystąpić, gdy dostawca wymaganych zasobów nie jest zarejestrowany. | [Kontrola dostępu oparta na rolach na platformie Azure](../../role-based-access-control/role-assignments-portal.md)<br><br>[Rozwiązywanie problemów z rejestracją](error-register-resource-provider.md) |
| BadRequest | Wysłano wartości wdrożenia, które nie są zgodne z oczekiwaniami Menedżera zasobów. Sprawdź wewnętrzny komunikat o stanie, aby uzyskać pomoc dotyczącą rozwiązywania problemów. | [Odwołanie do szablonu](/azure/templates/) i [obsługiwane lokalizacje](resource-location.md) |
| Konflikt | Żądasz operacji, która nie jest dozwolona w bieżącym stanie zasobu. Na przykład zmiana rozmiaru dysku jest dozwolona tylko podczas tworzenia maszyny Wirtualnej lub gdy maszyna wirtualna jest cofnięta. | |
| WdrażanieActiveAndUneditable | Poczekaj na zakończenie równoczesnego wdrożenia w tej grupie zasobów. | |
| DeploymentFailedCleanUp (WdrażanieFailedCleanUp) | Podczas wdrażania w trybie kompletnym wszystkie zasoby, które nie znajdują się w szablonie, są usuwane. Ten błąd jest taki, gdy nie masz odpowiednich uprawnień do usuwania wszystkich zasobów, których nie ma w szablonie. Aby uniknąć błędu, zmień tryb wdrażania na przyrostowy. | [Tryby wdrażania usługi Azure Resource Manager](deployment-modes.md) |
| DeploymentNameInvalidCharacters | Nazwa wdrożenia może zawierać tylko literę, cyfrę, '-', '.' lub '_'. | |
| Nazwa wdrożeniaLengthLimitExceeded | Nazwy wdrożenia są ograniczone do 64 znaków.  | |
| WdrożenieNiewięksłe | Błąd DeploymentFailed to ogólny błąd, który nie zawiera szczegółów potrzebnych do rozwiązania błędu. Poszukaj szczegółów błędu, aby uzyskać kod błędu, który zawiera więcej informacji. | [Znajdź kod błędu](#find-error-code) |
| DeploymentQuotaExceeded | Jeśli osiągniesz limit 800 wdrożeń na grupę zasobów, usuń wdrożenia z historii, które nie są już potrzebne. | [Rozwiązywanie problemów, gdy liczba wdrożeń przekracza 800](deployment-quota-exceeded.md) |
| DnsRecordInUżywanie | Nazwa rekordu DNS musi być unikatowa. Wprowadź inną nazwę. | |
| ImageNotFound (Nie można odnaleźć | Sprawdź ustawienia obrazu maszyny Wirtualnej. |  |
| InUseSubnetCannotBeDeleted | Ten błąd może wystąpić podczas próby zaktualizowania zasobu, a żądanie jest przetwarzane przez usunięcie i utworzenie zasobu. Upewnij się, że wszystkie niezmienione wartości. | [Aktualizowanie zasobu](/azure/architecture/building-blocks/extending-templates/update-resource) |
| InvalidAuthenticationTokenTenant | Pobierz token dostępu dla odpowiedniej dzierżawy. Token można uzyskać tylko od dzierżawy, do której należy Twoje konto. | |
| InvalidContentLink | Najprawdopodobniej podjęto próbę połączenia z zagnieżdżonym szablonem, który nie jest dostępny. Sprawdź identyfikator URI podany dla szablonu zagnieżdżonego. Jeśli szablon istnieje na koncie magazynu, upewnij się, że identyfikator URI jest dostępny. Może być konieczne przekazanie tokenu sygnatury dostępu Współdzielonego. Obecnie nie można połączyć się z szablonem, który znajduje się na koncie magazynu za [zaporą usługi Azure Storage](../../storage/common/storage-network-security.md). Rozważ przeniesienie szablonu do innego repozytorium, takiego jak GitHub. | [Połączone szablony](linked-templates.md) |
| InvalidDeploymentLokacja | Podczas wdrażania na poziomie subskrypcji podano inną lokalizację dla wcześniej używanej nazwy wdrożenia. | [Wdrożenia na poziomie subskrypcji](deploy-to-subscription.md) |
| Nieprawidłowyparametr | Jedna z wartości podanych dla zasobu nie jest zgodna z oczekiwaną wartością. Ten błąd może wynikać z wielu różnych warunków. Na przykład hasło może być niewystarczające lub nazwa obiektu blob może być niepoprawna. Komunikat o błędzie powinien wskazywać, która wartość musi zostać poprawiona. | |
| Nieprawidłowy Zapis | Wartości wdrożenia zawierają wartości, które nie są rozpoznawane lub brakuje wymaganych wartości. Potwierdź wartości dla typu zasobu. | [Dokumentacja dotycząca szablonów](/azure/templates/) |
| InvalidRequestFormat (InvalidRequestFormat) | Włącz rejestrowanie debugowania podczas uruchamiania wdrożenia i sprawdź zawartość żądania. | [Rejestrowanie debugowania](#enable-debug-logging) |
| InvalidResourceNamespace | Sprawdź obszar nazw zasobu określony we właściwości **typu.** | [Dokumentacja dotycząca szablonów](/azure/templates/) |
| InvalidResourceReference (Wniosek osource) | Zasób albo jeszcze nie istnieje lub jest niepoprawnie odwołuje. Sprawdź, czy musisz dodać zależność. Sprawdź, czy użycie funkcji **odwołania** zawiera wymagane parametry dla scenariusza. | [Rozwiązywanie zależności](error-not-found.md) |
| Typ nieprawidłowego źródła | Sprawdź typ zasobu określony we właściwości **type.** | [Dokumentacja dotycząca szablonów](/azure/templates/) |
| InvalidSubscriptionRegistrationState | Zarejestruj subskrypcję u dostawcy zasobów. | [Rozwiązywanie problemów z rejestracją](error-register-resource-provider.md) |
| InvalidTemplate | Sprawdź składnię szablonu pod kątem błędów. | [Rozwiązywanie problemów z nieprawidłowym szablonem](error-invalid-template.md) |
| InvalidTemplateCircularDependency | Usuń niepotrzebne zależności. | [Rozwiązywanie zależności cyklicznych](error-invalid-template.md#circular-dependency) |
| LinkedAuthorizationPowiń | Sprawdź, czy Twoje konto należy do tej samej dzierżawy co grupa zasobów, w której wdrażasz. | |
| LinkedInvalidPropertyId | Identyfikator zasobu nie jest poprawnie rozpoznawany. Sprawdź, czy podasz wszystkie wymagane wartości dla identyfikatora zasobu, w tym identyfikator subskrypcji, nazwę grupy zasobów, typ zasobu, nazwę zasobu nadrzędnego (w razie potrzeby) i nazwę zasobu. | |
| Wymagana lokalizacja | Podaj lokalizację zasobu. | [Ustawianie lokalizacji](resource-location.md) |
| NiezgodnośćResourceSegments | Upewnij się, że zagnieżdżony zasób ma poprawną liczbę segmentów w nazwie i typie. | [Rozwiązywanie problemów z segmentami zasobów](error-invalid-template.md#incorrect-segment-lengths)
| BrakrejestracjiZalokacja | Sprawdź stan rejestracji dostawcy zasobów i obsługiwane lokalizacje. | [Rozwiązywanie problemów z rejestracją](error-register-resource-provider.md) |
| BrakReregistrationsubscription | Zarejestruj subskrypcję u dostawcy zasobów. | [Rozwiązywanie problemów z rejestracją](error-register-resource-provider.md) |
| NoRegisteredProviderFound | Sprawdź stan rejestracji dostawcy zasobów. | [Rozwiązywanie problemów z rejestracją](error-register-resource-provider.md) |
| NotFound | Być może próbujesz wdrożyć zasób zależny równolegle z zasobem nadrzędnym. Sprawdź, czy musisz dodać zależność. | [Rozwiązywanie zależności](error-not-found.md) |
| Operacja Nieuwolna | Wdrożenie próbuje operacji, która przekracza przydział dla subskrypcji, grupy zasobów lub regionu. Jeśli to możliwe, zmień swoje wdrożenie, aby pozostać w ramach przydziałów. W przeciwnym razie należy rozważyć żądanie zmiany przydziałów. | [Rozwiązywanie kwot](error-resource-quota.md) |
| ParentResourceNotFound | Upewnij się, że zasób nadrzędny istnieje przed utworzeniem zasobów podrzędnych. | [Rozwiązywanie problemów z zasobem nadrzędnym](error-parent-resource.md) |
| PasswordTooLong (Hasło Dołek) | Być może wybrano hasło ze zbyt dużą liczoną liczoną literą lub przekonwertowano wartość hasła na bezpieczny ciąg przed przekazaniem go jako parametru. Jeśli szablon zawiera parametr **bezpiecznego ciągu,** nie trzeba konwertować wartości na bezpieczny ciąg. Podaj wartość hasła jako tekst. |  |
| PrivateIPAddressInReservedRange (PrivateIPAddressInReservedRange) | Określony adres IP zawiera zakres adresów wymaganych przez platformę Azure. Zmień adres IP, aby uniknąć zastrzeżonego zakresu. | [Adresy IP](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) |
| PrivateIPAddressNotInSubnet | Określony adres IP znajduje się poza zakresem podsieci. Zmień adres IP, aby mieszczą się w zakresie podsieci. | [Adresy IP](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) |
| PropertyChangeNotallowed | Niektórych właściwości nie można zmienić w zasób wdrożony. Podczas aktualizowania zasobu należy ograniczyć zmiany do dozwolonych właściwości. | [Aktualizowanie zasobu](/azure/architecture/building-blocks/extending-templates/update-resource) |
| RequestDisallowedByPolicy | Subskrypcja zawiera zasady zasobów, które zapobiega akcji, którą próbujesz wykonać podczas wdrażania. Znajdź zasady, które blokują akcję. Jeśli to możliwe, zmień wdrożenie, aby spełnić ograniczenia z zasad. | [Rozwiązywanie zasad](error-policy-requestdisallowedbypolicy.md) |
| ReservedResourceName | Podaj nazwę zasobu, która nie zawiera nazwy zastrzeżonej. | [Nazwy zasobów zarezerwowanych](error-reserved-resource-name.md) |
| ZasiewGrupaZajednała | Poczekaj na usunięcie do zakończenia. | |
| ResourceGroupNotFound | Sprawdź nazwę docelowej grupy zasobów dla wdrożenia. Docelowa grupa zasobów musi już istnieć w ramach subskrypcji. Sprawdź kontekst subskrypcji. | [Program Azure CLI](/cli/azure/account?#az-account-set) [PowerShell](/powershell/module/Az.Accounts/Set-AzContext) |
| ResourceNotFound | Wdrożenie odwołuje się do zasobu, którego nie można rozpoznać. Sprawdź, czy użycie funkcji **odwołania** zawiera parametry wymagane dla scenariusza. | [Rozwiązywanie odwołań](error-not-found.md) |
| ResourceQuotaExceeded | Wdrożenie próbuje utworzyć zasoby, które przekraczają przydział dla subskrypcji, grupy zasobów lub regionu. Jeśli to możliwe, zmień infrastrukturę, aby pozostać w granicach przydziałów. W przeciwnym razie należy rozważyć żądanie zmiany przydziałów. | [Rozwiązywanie kwot](error-resource-quota.md) |
| SkuNotAvailable | Wybierz jednostkę SKU (na przykład rozmiar maszyny Wirtualnej), która jest dostępna dla wybranej lokalizacji. | [Rozwiąż sku](error-sku-not-available.md) |
| StorageAccountAlreadyExists | Podaj unikatową nazwę konta magazynu. | [Rozwiązywanie problemu z nazwą konta magazynu](error-storage-account-name.md)  |
| Konto magazynuNajażneWyjęty | Podaj unikatową nazwę konta magazynu. | [Rozwiązywanie problemu z nazwą konta magazynu](error-storage-account-name.md) |
| StorageAccountNotFound | Sprawdź subskrypcję, grupę zasobów i nazwę konta magazynu, którego próbujesz użyć. | |
| PodsieciNotInSameVnet | Maszyna wirtualna może mieć tylko jedną sieć wirtualną. Podczas wdrażania kilku kart sieciowych upewnij się, że należą one do tej samej sieci wirtualnej. | [Wiele kart sieciowych](../../virtual-machines/windows/multiple-nics.md) |
| SubskrypcjaNiezarejestrowane | Podczas wdrażania zasobów sieciowych dostawca zasobów microsoft.network jest automatycznie rejestrowany w subskrypcji. Czasami automatyczna rejestracja nie kończy się na czas. Aby uniknąć tego sporadycznego błędu, zarejestruj dostawcę zasobów microsoft.network przed wdrożeniem. | [Rozwiązywanie problemów z rejestracją](error-register-resource-provider.md) |
| SzablonResourceCircularDependency | Usuń niepotrzebne zależności. | [Rozwiązywanie zależności cyklicznych](error-invalid-template.md#circular-dependency) |
| TooManyTargetResourceGroups | Zmniejsz liczbę grup zasobów dla pojedynczego wdrożenia. | [Wdrażanie krzyżowe w grupach zasobów](cross-resource-group-deployment.md) |

## <a name="find-error-code"></a>Znajdź kod błędu

Istnieją dwa typy błędów, które mogą wystąpić:

* błędy weryfikacji
* błędy wdrażania

Błędy weryfikacji wynikają z sytuacji, które można rozpoznać przed przystąpieniem do wdrożenia. Są to na przykład błędy składniowe w szablonie lub próby wdrożenia zasobów, które przekraczają limity przydziału w ramach subskrypcji. Błędy wdrażania wynikają z warunków, które mają miejsce podczas procesu wdrażania. Obejmują one próby uzyskania dostępu do zasobu, który jest wdrażany równolegle.

Oba rodzaje błędów zwracają kod błędu, którego należy użyć do rozwiązania problemów z wdrożeniem. Oba typy błędów są wyświetlane w [dzienniku aktywności](../management/view-activity-logs.md). Błędy weryfikacji nie są jednak wyświetlane w historii wdrażania, ponieważ wdrożenie nie jest w takim przypadku rozpoczynane.

### <a name="validation-errors"></a>Błędy sprawdzania poprawności

Podczas wdrażania za pośrednictwem portalu zobaczysz błąd weryfikacji po przesłaniu własnych wartości.

![pokaż błąd sprawdzania poprawności portalu](./media/common-deployment-errors/validation-error.png)

Wybierz komunikat, aby uzyskać więcej informacji. Na poniższej ilustracji zostanie wyświetlony błąd **InvalidTemplateDeployment** i komunikat wskazujący wdrożenie zablokowane przez zasady.

![pokaż szczegóły sprawdzania poprawności](./media/common-deployment-errors/validation-details.png)

### <a name="deployment-errors"></a>Błędy wdrażania

Jeśli operacja przeszła weryfikację, ale kończy się niepowodzeniem podczas wdrażania, otrzymasz błąd wdrażania.

Aby wyświetlić kody błędów wdrażania i komunikaty za pomocą programu PowerShell, użyj następującego polecenia:

```azurepowershell-interactive
(Get-AzResourceGroupDeploymentOperation -DeploymentName exampledeployment -ResourceGroupName examplegroup).Properties.statusMessage
```

Aby wyświetlić kody błędów wdrażania i komunikaty za pomocą wiersza polecenia platformy Azure, użyj następującego polecenia:

```azurecli-interactive
az deployment group operation list --name exampledeployment -g examplegroup --query "[*].properties.statusMessage"
```

W portalu wybierz powiadomienie.

![błąd powiadomień](./media/common-deployment-errors/notification.png)

Zobaczysz więcej szczegółów na temat wdrożenia. Wybierz opcję, aby uzyskać więcej informacji o błędzie.

![wdrożenie nie powiodło się](./media/common-deployment-errors/deployment-failed.png)

Zostanie wyświetlony komunikat o błędzie i kody błędu. Zauważ, że są tam podane dwa kody błędu. Pierwszy kod błędu (**DeploymentFailed**) identyfikuje błąd ogólny, który nie zapewnia szczegółów niezbędnych do rozwiązania problemu. Drugi kod błędu (**StorageAccountNotFound**) udostępnia szczegółowe informacje, których potrzebujesz.

![szczegóły błędu](./media/common-deployment-errors/error-details.png)

## <a name="enable-debug-logging"></a>Włączanie rejestrowania debugowania

Czasami potrzebujesz więcej informacji na temat żądania i odpowiedzi, aby dowiedzieć się, co poszło nie tak. Podczas wdrażania można zażądać, aby dodatkowe informacje były rejestrowane podczas wdrażania.

### <a name="powershell"></a>PowerShell

W programie PowerShell ustaw parametr **DeploymentDebugLogLevel** na All, ResponseContent lub RequestContent.

```powershell
New-AzResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName examplegroup `
  -TemplateFile c:\Azure\Templates\storage.json `
  -DeploymentDebugLogLevel All
```

Sprawdź zawartość żądania za pomocą następującego polecenia cmdlet:

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

Te informacje mogą pomóc w określeniu, czy wartość w szablonie jest niepoprawnie ustawiona.

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Obecnie narzędzie interfejsu wiersza polecenia platformy Azure nie obsługuje włączania rejestrowania debugowania, ale można pobrać rejestrowanie debugowania.

Sprawdź operacje wdrażania za pomocą następującego polecenia:

```azurecli
az deployment group operation list \
  --resource-group examplegroup \
  --name exampledeployment
```

Sprawdź zawartość żądania za pomocą następującego polecenia:

```azurecli
az deployment group operation list \
  --name exampledeployment \
  -g examplegroup \
  --query [].properties.request
```

Sprawdź zawartość odpowiedzi za pomocą następującego polecenia:

```azurecli
az deployment group operation list \
  --name exampledeployment \
  -g examplegroup \
  --query [].properties.response
```

### <a name="nested-template"></a>Szablon zagnieżdżony

Aby rejestrować informacje debugowania dla szablonu zagnieżdżonego, użyj elementu **debugSetting.**

```json
{
  "type": "Microsoft.Resources/deployments",
  "apiVersion": "2016-09-01",
  "name": "nestedTemplate",
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

W niektórych przypadkach najprostszym sposobem rozwiązywania problemów z szablonem jest przetestowanie jego części. Można utworzyć uproszczony szablon, który umożliwia skupienie się na części, która uważasz, że jest przyczyną błędu. Załóżmy na przykład, że podczas odwoływania się do zasobu pojawia się błąd. Zamiast zajmować się całym szablonem, utwórz szablon, który zwraca część, która może być przyczyną problemu. Może pomóc określić, czy przekazujesz w odpowiednich parametrów, przy użyciu funkcji szablonu poprawnie i uzyskanie zasobów, których oczekujesz.

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

Lub załóżmy, że otrzymujesz błędy wdrażania, które uważasz, że są związane z niepoprawnie ustawić zależności. Przetestuj szablon, rozbijając go na uproszczone szablony. Najpierw utwórz szablon, który wdraża tylko jeden zasób (np. sql server). Jeśli masz pewność, że ten zasób został poprawnie zdefiniowany, dodaj zasób, który jest od niego zależny (np. baza danych SQL). Jeśli te dwa zasoby są poprawnie zdefiniowane, dodaj inne zasoby zależne (takie jak zasady inspekcji). W między każdym wdrożeniem testowym usuń grupę zasobów, aby upewnić się, że odpowiednio testujesz zależności.

## <a name="next-steps"></a>Następne kroki

* Aby przejść do samouczka rozwiązywania problemów, zobacz [Samouczek: Rozwiązywanie problemów z wdrożeniami szablonów Menedżera zasobów](template-tutorial-troubleshoot.md)
* Aby dowiedzieć się więcej o akcjach inspekcji, zobacz [Inspekcja operacji za pomocą Menedżera zasobów](../management/view-activity-logs.md).
* Aby dowiedzieć się więcej o akcjach w celu określenia błędów podczas wdrażania, zobacz [Wyświetlanie operacji wdrażania](deployment-history.md).
