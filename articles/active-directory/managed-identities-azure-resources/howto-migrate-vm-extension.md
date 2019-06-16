---
title: Zaprzestać korzystania z tożsamości zarządzanej rozszerzenia maszyny Wirtualnej i rozpocząć korzystanie z punktu końcowego usługi Azure Instance Metadata Service
description: Krok po kroku instrukcje, aby uniemożliwić korzystanie z rozszerzenia maszyny Wirtualnej i rozpocząć korzystanie z platformy Azure wystąpienie metadanych usługi (IMDS) do uwierzytelniania.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/25/2018
ms.author: markvi
ms.openlocfilehash: 6ee8891eae108256875660cc3f2256b65703a1aa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65406786"
---
# <a name="how-to-stop-using-the-virtual-machine-managed-identities-extension-and-start-using-the-azure-instance-metadata-service"></a>Jak zatrzymać za pomocą maszyny wirtualnej zarządzany rozszerzenie tożsamości i Rozpocznij korzystanie z usługi Azure Instance Metadata Service

## <a name="virtual-machine-extension-for-managed-identities"></a>Rozszerzenie maszyny wirtualnej dla zarządzanych tożsamości

Rozszerzenie maszyny wirtualnej dla zarządzanych tożsamości jest używany do żądania tokenów na potrzeby tożsamości zarządzanej maszyny wirtualnej. Przepływ pracy składa się z następujących czynności:

1. Po pierwsze, obciążenie w ramach zasobu wywołuje lokalny punkt końcowy `http://localhost/oauth2/token` do wysłania żądania tokenu dostępu.
2. Rozszerzenie maszyny wirtualnej używa poświadczenia tożsamości zarządzanej do żądania tokenu dostępu z usługi Azure AD... 
3. Token dostępu jest zwracany do obiektu wywołującego i może służyć do uwierzytelniania do usług, które obsługują uwierzytelnianie usługi Azure AD, takich jak usługi Azure Key Vault lub Azure Storage.

Wynikać z kilku ograniczeń, opisane w następnej sekcji została zastąpiona tożsamość zarządzaną rozszerzenia maszyny Wirtualnej przy użyciu równoważne punktu końcowego usługi metadanych wystąpienia (IMDS) platformy Azure

### <a name="provision-the-extension"></a>Aprowizacja rozszerzenia 

Podczas konfigurowania maszyny wirtualnej lub maszyny wirtualnej zestawu skalowania w celu mają tożsamość zarządzaną, użytkownik może opcjonalnie obsługi administracyjnej zarządzanych tożsamości dla rozszerzenie maszyny Wirtualnej zasoby platformy Azure przy użyciu polecenia `-Type` parametru [ Zestaw AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension) polecenia cmdlet. Można przekazać `ManagedIdentityExtensionForWindows` lub `ManagedIdentityExtensionForLinux`, w zależności od typu maszyny wirtualnej i nadaj mu za pomocą `-Name` parametru. `-Settings` Parametr określa port używany przez punkt końcowy tokenu OAuth dla tokenu:

```powershell
   $settings = @{ "port" = 50342 }
   Set-AzVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
```

Można również użyć Szablon wdrożenia usługi Azure Resource Manager do aprowizacji rozszerzenia maszyny Wirtualnej, dodając następujący kod JSON do `resources` sekcji do szablonu (Użyj `ManagedIdentityExtensionForLinux` nazwę i typ elementów dla wersji systemu Linux).

    ```json
    {
        "type": "Microsoft.Compute/virtualMachines/extensions",
        "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
        "apiVersion": "2018-06-01",
        "location": "[resourceGroup().location]",
        "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
        ],
        "properties": {
            "publisher": "Microsoft.ManagedIdentity",
            "type": "ManagedIdentityExtensionForWindows",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "port": 50342
            }
        }
    }
    ```
    
    
Jeśli pracujesz z zestawami skalowania maszyn wirtualnych, można również udostępniać zarządzanych tożsamości dla zestawu skalowania maszyny wirtualnej zasoby platformy Azure przy użyciu rozszerzenia [AzVmssExtension Dodaj](/powershell/module/az.compute/add-azvmssextension) polecenia cmdlet. Można przekazać `ManagedIdentityExtensionForWindows` lub `ManagedIdentityExtensionForLinux`, w zależności od typu skalowania maszyn wirtualnych zestawu i nazwij go za pomocą `-Name` parametru. `-Settings` Parametr określa port używany przez punkt końcowy tokenu OAuth dla tokenu:

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzVmss
   Add-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```
Aby aprowizować skalowania maszyn wirtualnych należy ustawić rozszerzenia za pomocą szablonu wdrożenia usługi Azure Resource Manager, Dodaj następujący kod JSON do `extensionpProfile` sekcji do szablonu (Użyj `ManagedIdentityExtensionForLinux` nazwę i typ elementów dla wersji systemu Linux).

    ```json
    "extensionProfile": {
        "extensions": [
            {
                "name": "ManagedIdentityWindowsExtension",
                "properties": {
                    "publisher": "Microsoft.ManagedIdentity",
                    "type": "ManagedIdentityExtensionForWindows",
                    "typeHandlerVersion": "1.0",
                    "autoUpgradeMinorVersion": true,
                    "settings": {
                        "port": 50342
                    },
                    "protectedSettings": {}
                }
            }
    ```

Inicjowanie obsługi rozszerzenia maszyny wirtualnej może zakończyć się niepowodzeniem z powodu błędów wyszukiwania DNS. W takim przypadku należy ponownie uruchomić maszynę wirtualną i spróbuj ponownie. 

### <a name="remove-the-extension"></a>Usuń rozszerzenie 
Aby usunąć rozszerzenie, należy użyć `-n ManagedIdentityExtensionForWindows` lub `-n ManagedIdentityExtensionForLinux` przełącznik (w zależności od typu maszyny wirtualnej) z [Usuń rozszerzenie maszyny wirtualnej az](https://docs.microsoft.com/cli/azure/vm/), lub [Usuń rozszerzenie zestawu skalowania maszyn wirtualnych az](https://docs.microsoft.com/cli/azure/vmss) dla skalowania maszyn wirtualnych Ustawia przy użyciu wiersza polecenia platformy Azure lub `Remove-AzVMExtension` środowiska PowerShell:

```azurecli-interactive
az vm identity --resource-group myResourceGroup --vm-name myVm -n ManagedIdentityExtensionForWindows
```

```azurecli-interactive
az vmss extension delete -n ManagedIdentityExtensionForWindows -g myResourceGroup -vmss-name myVMSS
```

```powershell
Remove-AzVMExtension -ResourceGroupName myResourceGroup -Name "ManagedIdentityExtensionForWindows" -VMName myVM
```

### <a name="acquire-a-token-using-the-virtual-machine-extension"></a>Uzyskiwanie tokenu przy użyciu rozszerzenia maszyny wirtualnej

Poniżej przedstawiono przykładowe żądanie przy użyciu zarządzanych tożsamości dla zasobów platformy Azure punktu końcowego rozszerzenia maszyny Wirtualnej:

```
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F HTTP/1.1
Metadata: true
```

| Element | Opis |
| ------- | ----------- |
| `GET` | Czasownik HTTP wskazująca, że chcesz pobrać dane z punktu końcowego. W tym przypadku token dostępu OAuth. | 
| `http://localhost:50342/oauth2/token` | Zarządzanych tożsamości dla punktu końcowego z zasobów platformy Azure, gdzie 50342 jest domyślnym portem i można konfigurować. |
| `resource` | Parametr ciągu zapytania, wskazującą aplikację identyfikator URI zasobu docelowego. Jest także wyświetlany w `aud` (odbiorcy) oświadczenia w wystawionych tokenów. W tym przykładzie żąda tokenu dostępu do usługi Azure Resource Manager, która zawiera identyfikator URI aplikacji z https://management.azure.com/. |
| `Metadata` | Pola nagłówka żądania HTTP, związanej z zarządzanych tożsamości dla zasobów platformy Azure jako ograniczenie przed atakiem serwera po stronie żądania Międzywitrynowego (SSRF). Ta wartość musi być równa "true", małymi literami.|
| `object_id` | (Opcjonalnie) Parametr ciągu zapytania, wskazujący object_id tożsamości zarządzanej, które Twoim zdaniem token dla. Wymagane, jeśli maszyna wirtualna ma wiele tożsamości zarządzanych przypisanych przez użytkownika.|
| `client_id` | (Opcjonalnie) Parametr ciągu zapytania, wskazujący client_id z tożsamości zarządzanej, które Twoim zdaniem tokenem. Wymagane, jeśli maszyna wirtualna ma wiele tożsamości zarządzanych przypisanych przez użytkownika.|


Przykładowa odpowiedź:

```
HTTP/1.1 200 OK
Content-Type: application/json
{
  "access_token": "eyJ0eXAi...",
  "refresh_token": "",
  "expires_in": "3599",
  "expires_on": "1506484173",
  "not_before": "1506480273",
  "resource": "https://management.azure.com/",
  "token_type": "Bearer"
}
```

| Element | Opis |
| ------- | ----------- |
| `access_token` | Token żądanego dostępu. Podczas wywoływania zabezpieczonego interfejsu API REST, token jest osadzony w `Authorization` pola nagłówka żądania jako token "bearer", dzięki czemu interfejs API do uwierzytelniania obiektu wywołującego. | 
| `refresh_token` | Nie używany przez zarządzanych tożsamości dla zasobów platformy Azure. |
| `expires_in` | Liczba sekund, przez które tokenu dostępu w dalszym ciągu jest prawidłowy, zanim wygaśnie od czasu wydania. Czas wystawienia można znaleźć w tokenie `iat` oświadczenia. |
| `expires_on` | Zakres czasu wygaśnięcia tokenu dostępu. Data jest reprezentowana jako liczba sekund od "1970-01-01T0:0:0Z UTC" (odnosi się do tokenu `exp` oświadczenia). |
| `not_before` | Timespan, gdy token dostępu staje się skuteczny i mogą być akceptowane. Data jest reprezentowana jako liczba sekund od "1970-01-01T0:0:0Z UTC" (odnosi się do tokenu `nbf` oświadczenia). |
| `resource` | Zasób tokenu dostępu zażądano, odpowiadający `resource` żądania parametr ciągu zapytania. |
| `token_type` | Typ tokenu, który jest "Bearer" tokenu dostępu, co oznacza, że zasób można zapewnić dostęp do elementu nośnego tego tokenu. |


### <a name="troubleshoot-the-virtual-machine-extension"></a>Rozwiązywanie problemów z rozszerzenia maszyny wirtualnej 

#### <a name="restart-the-virtual-machine-extension-after-a-failure"></a>Uruchomienie rozszerzenia maszyny wirtualnej po awarii

W systemie Windows i niektóre wersje systemu Linux Jeśli rozszerzenie zostanie zatrzymana, następujące polecenie cmdlet może służyć do ręcznie uruchomić ponownie:

```powershell
Set-AzVMExtension -Name <extension name>  -Type <extension Type>  -Location <location> -Publisher Microsoft.ManagedIdentity -VMName <vm name> -ResourceGroupName <resource group name> -ForceRerun <Any string different from any last value used>
```

Gdzie: 
- Rozszerzenie nazwy i typu dla Windows to: `ManagedIdentityExtensionForWindows`
- Rozszerzenie nazwy i typu dla systemu Linux jest: `ManagedIdentityExtensionForLinux`

#### <a name="automation-script-fails-when-attempting-schema-export-for-managed-identities-for-azure-resources-extension"></a>"Skrypt automatyzacji" kończy się niepowodzeniem podczas próby eksport schematu dla zarządzanych tożsamości dla rozszerzenia zasobów platformy Azure

Po włączeniu zarządzanych tożsamości dla zasobów platformy Azure na maszynie wirtualnej następujący błąd jest wyświetlany, gdy próba użycia funkcji "Skrypt automatyzacji" dla maszyny wirtualnej lub jej grupy zasobów:

![Błąd eksportowania zarządzanych tożsamości dla zasobów platformy Azure, skrypt automatyzacji](./media/howto-migrate-vm-extension/automation-script-export-error.png)

Zarządzanych tożsamości dla rozszerzenia maszyny wirtualnej zasoby platformy Azure nie obsługuje obecnie możliwość eksportowania jego schematu do szablonu grupy zasobów. W rezultacie w wygenerowany szablon nie są wyświetlane parametry konfiguracji, aby umożliwić zarządzanych tożsamości dla zasobów platformy Azure w zasobie. Poniższe sekcje mogą być dodawane ręcznie, wykonując na potrzeby przykładów w [Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na maszynie wirtualnej platformy Azure przy użyciu szablonów](qs-configure-template-windows-vm.md).

Gdy funkcja eksportu schematu stają się dostępne dla zarządzanych tożsamości dla rozszerzenia maszyny wirtualnej zasoby platformy Azure (planowana do wycofania z użycia w styczniu 2019), będzie ono wyświetlane w [eksportowanie grupy zasobów zawierające rozszerzeń maszyn wirtualnych ](../../virtual-machines/extensions/export-templates.md#supported-virtual-machine-extensions).

## <a name="limitations-of-the-virtual-machine-extension"></a>Ograniczenia rozszerzenie maszyny wirtualnej 

Istnieje kilka głównych ograniczeń dotyczących używania rozszerzenia maszyny wirtualnej. 

 * Najpoważniejsze ograniczeniem jest fakt, że poświadczenia używane do żądania tokenów są przechowywane na maszynie wirtualnej. Osoba atakująca pomyślnie breaches maszyny wirtualnej może wyprowadzać poświadczenia. 
 * Ponadto rozszerzenia maszyny wirtualnej nadal jest nieobsługiwany przez kilka dystrybucje systemu Linux, z ogromnego rozwoju, kosztów, aby zmodyfikować, skompilować i przetestować rozszerzenia na każdym z tych dystrybucji. Obecnie obsługiwane są tylko poniższe dystrybucje systemu Linux: 
    * Stabilny systemu CoreOS
    * CentOS 7.1 
    * Red Hat 7.2 
    * Ubuntu 15.04 
    * Ubuntu 16.04
 * Ma wpływ na wydajność do wdrażania maszyn wirtualnych za pomocą tożsamości zarządzanej, rozszerzenie maszyny wirtualnej również musi być aprowizowany. 
 * Na koniec rozszerzenie maszyny wirtualnej może obsługiwać tylko o 32 tożsamości przypisanych przez użytkownika zarządzanych na maszynę wirtualną. 

## <a name="azure-instance-metadata-service"></a>Wystąpienie usługi Azure Metadata Service

[Azure wystąpienie metadanych usługi (IMDS)](/azure/virtual-machines/windows/instance-metadata-service) jest punkt końcowy REST, który dostarcza informacji o uruchomionych wystąpień maszyn wirtualnych, które może służyć do zarządzania i konfigurowania maszyn wirtualnych. Punkt końcowy jest dostępny w dobrze znanego adresu IP bez obsługi routingu (`169.254.169.254`), są dostępne tylko z poziomu maszyny wirtualnej.

Ma kilka zalet za pomocą usługi Azure IMDS żądania tokenów. 

1. Usługa jest zewnętrzny do maszyny wirtualnej, dlatego poświadczenia używane przez zarządzanych tożsamości nie są już dostępne na maszynie wirtualnej. Zamiast tego są hostowane i zabezpieczane na komputerze hosta maszyny wirtualnej platformy Azure.   
2. Wszystkie Windows i Linux obsługiwane systemy operacyjne w usłudze Azure IaaS można użyć zarządzanych tożsamości.
3. Wdrożenie jest szybsze i prostsze, ponieważ rozszerzenie maszyny Wirtualnej nie jest już powinna być przygotowana.
4. Za pomocą IMDS punktu końcowego, maksymalnie 1000 przypisanych do użytkowników zarządzanych tożsamości można przypisać do jednej maszyny wirtualnej.
5. Nie ma znaczących zmian na żądania, a nie za pomocą rozszerzenia maszyny wirtualnej przy użyciu IMDS, dlatego jest dość prosty, port za pośrednictwem istniejących wdrożeń, które obecnie używają rozszerzenia maszyny wirtualnej.

Z tego względu usługa Azure IMDS będzie oczywisty sposób żądania tokenów, po rozszerzenie maszyny wirtualnej jest przestarzały. 


## <a name="next-steps"></a>Następne kroki

* [Jak używać zarządzanych tożsamości dla zasobów platformy Azure na maszynie wirtualnej platformy Azure w celu pobrania tokenu dostępu](how-to-use-vm-token.md)
* [Wystąpienie usługi Azure Metadata Service](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)
