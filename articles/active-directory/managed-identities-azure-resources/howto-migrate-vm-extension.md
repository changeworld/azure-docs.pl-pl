---
title: Zatrzymywać się przy użyciu rozszerzenia maszyny Wirtualnej tożsamości zarządzanej — usługa Azure AD
description: Instrukcje krok po kroku, aby zatrzymać przy użyciu rozszerzenia maszyny Wirtualnej i rozpocząć korzystanie z usługi metadanych wystąpienia platformy Azure (IMDS) do uwierzytelniania.
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
ms.openlocfilehash: 01b8e1dbc290bed86ccfc3c7016e8bd9168e427a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80049063"
---
# <a name="how-to-stop-using-the-virtual-machine-managed-identities-extension-and-start-using-the-azure-instance-metadata-service"></a>Jak przestać używać rozszerzenia tożsamości zarządzanych na maszynie wirtualnej i rozpocząć korzystanie z usługi metadanych wystąpienia platformy Azure

## <a name="virtual-machine-extension-for-managed-identities"></a>Rozszerzenie maszyny wirtualnej dla tożsamości zarządzanych

Rozszerzenie maszyny wirtualnej dla tożsamości zarządzanych jest używane do żądania tokenów dla tożsamości zarządzanej w maszynie wirtualnej. Przepływ pracy składa się z następujących kroków:

1. Najpierw obciążenie w zasobie wywołuje lokalnego punktu końcowego, `http://localhost/oauth2/token` aby zażądać tokenu dostępu.
2. Rozszerzenie maszyny wirtualnej następnie używa poświadczeń dla tożsamości zarządzanej, aby zażądać tokenu dostępu z usługi Azure AD.. 
3. Token dostępu jest zwracany do obiektu wywołującego i może służyć do uwierzytelniania do usług obsługujących uwierzytelnianie usługi Azure AD, takich jak Usługa Azure Key Vault lub Azure Storage.

Ze względu na kilka ograniczeń opisanych w następnej sekcji rozszerzenie maszyny Wirtualnej tożsamości zarządzanej zostało przestarzałe na rzecz korzystania z równoważnego punktu końcowego w usłudze metadanych wystąpienia platformy Azure (IMDS)

### <a name="provision-the-extension"></a>Rozszerzeń 

Podczas konfigurowania maszyny wirtualnej lub maszyny wirtualnej skalowania zestaw mieć tożsamość zarządzaną, można opcjonalnie wybrać `-Type` do aprowizowania tożsamości zarządzanych dla rozszerzenia maszyny wirtualnej zasobów platformy Azure przy użyciu parametru na polecenie cmdlet [Set-AzVMExtension.](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension) Można przekazać albo `ManagedIdentityExtensionForWindows` `ManagedIdentityExtensionForLinux`lub , w zależności od typu maszyny `-Name` wirtualnej i nazwać go za pomocą parametru. Parametr `-Settings` określa port używany przez punkt końcowy tokenu OAuth do pozyskiwania tokenów:

```powershell
   $settings = @{ "port" = 50342 }
   Set-AzVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
```

Można również użyć szablonu wdrażania usługi Azure Resource Manager do aprowizowania rozszerzenia maszyny Wirtualnej, dodając następujący JSON do `resources` sekcji do szablonu (użyj `ManagedIdentityExtensionForLinux` dla elementów nazwy i typu dla wersji systemu Linux).

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
    
    
Jeśli pracujesz z zestawami skalowania maszyny wirtualnej, można również aprowizować tożsamości zarządzane dla zasobów platformy Azure rozszerzenie zestawu skalowania maszyny wirtualnej przy użyciu polecenia cmdlet [Add-AzVmssExtension.](/powershell/module/az.compute/add-azvmssextension) Można przekazać albo `ManagedIdentityExtensionForWindows` `ManagedIdentityExtensionForLinux`lub , w zależności od typu zestawu skalowania `-Name` maszyny wirtualnej i nazwać go za pomocą parametru. Parametr `-Settings` określa port używany przez punkt końcowy tokenu OAuth do pozyskiwania tokenów:

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzVmss
   Add-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```
Aby aprowizować rozszerzenie zestawu skalowania maszyny wirtualnej za pomocą `extensionpProfile` szablonu wdrażania usługi `ManagedIdentityExtensionForLinux` Azure Resource Manager, dodaj następujący JSON do sekcji do szablonu (użyj elementów nazwy i typu dla wersji systemu Linux).

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

Inicjowanie obsługi administracyjnej rozszerzenia maszyny wirtualnej może zakończyć się niepowodzeniem z powodu błędów wyszukiwania DNS. W takim przypadku uruchom ponownie maszynę wirtualną i spróbuj ponownie. 

### <a name="remove-the-extension"></a>Usuwanie rozszerzenia 
Aby usunąć rozszerzenie, `-n ManagedIdentityExtensionForWindows` `-n ManagedIdentityExtensionForLinux` użyj lub przełącz (w zależności od typu maszyny wirtualnej) z [usunięciem rozszerzenia az vm](https://docs.microsoft.com/cli/azure/vm/)lub `Remove-AzVMExtension` [usunięciem rozszerzenia AZ VMSs](https://docs.microsoft.com/cli/azure/vmss) dla zestawów skalowania maszyny wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure lub programu Powershell:

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

Poniżej przedstawiono przykładowe żądanie przy użyciu tożsamości zarządzanych dla zasobów platformy Azure Punkt końcowy rozszerzenia maszyny Wirtualnej:

```
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F HTTP/1.1
Metadata: true
```

| Element | Opis |
| ------- | ----------- |
| `GET` | Zlecenie HTTP, wskazując, że chcesz pobrać dane z punktu końcowego. W takim przypadku token dostępu OAuth. | 
| `http://localhost:50342/oauth2/token` | Tożsamości zarządzane dla punktu końcowego zasobów platformy Azure, gdzie 50342 jest portem domyślnym i jest konfigurowalny. |
| `resource` | Parametr ciągu kwerendy wskazujący identyfikator URI identyfikatora aplikacji zasobu docelowego. Pojawia się również `aud` w (odbiorcy) roszczenia wydanego tokenu. W tym przykładzie żąda tokenu dostępu do usługi Azure `https://management.azure.com/`Resource Manager, który ma identyfikator URI identyfikatora aplikacji . |
| `Metadata` | Pole nagłówka żądania HTTP, wymagane przez tożsamości zarządzane dla zasobów platformy Azure jako ograniczenie ataków fałszerstwa żądań po stronie serwera (SSRF). Ta wartość musi być ustawiona na "true", we wszystkich przypadkach.|
| `object_id` | (Opcjonalnie) Parametr ciągu kwerendy, wskazujący object_id tożsamości zarządzanej, dla której chcesz token. Wymagane, jeśli maszyna wirtualna ma wiele tożsamości zarządzanych przypisanych przez użytkownika.|
| `client_id` | (Opcjonalnie) Parametr ciągu kwerendy, wskazujący client_id tożsamości zarządzanej, dla której chcesz token. Wymagane, jeśli maszyna wirtualna ma wiele tożsamości zarządzanych przypisanych przez użytkownika.|


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
| `access_token` | Żądany token dostępu. Podczas wywoływania zabezpieczonego interfejsu API REST `Authorization` token jest osadzony w polu nagłówka żądania jako token "nośnik", umożliwiając interfejsowi API uwierzytelnienie wywołującego. | 
| `refresh_token` | Nie używane przez tożsamości zarządzane dla zasobów platformy Azure. |
| `expires_in` | Liczba sekund token dostępu nadal jest prawidłowa, przed wygaśnięciem, od czasu wystawienia. Czas wystawienia można znaleźć w żądaniu `iat` tokenu. |
| `expires_on` | Czas po wygaśnięciu tokenu dostępu. Data jest reprezentowana jako liczba sekund od "1970-01-01T0:0:0Z UTC" (odpowiada `exp` żądaniu tokenu). |
| `not_before` | Czas, gdy token dostępu staje się skuteczne i mogą być akceptowane. Data jest reprezentowana jako liczba sekund od "1970-01-01T0:0:0Z UTC" (odpowiada `nbf` żądaniu tokenu). |
| `resource` | Zasób, dla którego zażądano tokenu dostępu, który odpowiada parametrowi `resource` ciągu zapytania żądania. |
| `token_type` | Typ tokenu, który jest token dostępu "Bearer", co oznacza, że zasób może dać dostęp do nośnika tego tokenu. |


### <a name="troubleshoot-the-virtual-machine-extension"></a>Rozwiązywanie problemów z rozszerzeniem maszyny wirtualnej 

#### <a name="restart-the-virtual-machine-extension-after-a-failure"></a>Uruchom ponownie rozszerzenie maszyny wirtualnej po awarii

W systemie Windows i niektórych wersjach systemu Linux, jeśli rozszerzenie zostanie zatrzymane, do ręcznego ponownego uruchomienia może być użyte następujące polecenie cmdlet:

```powershell
Set-AzVMExtension -Name <extension name>  -Type <extension Type>  -Location <location> -Publisher Microsoft.ManagedIdentity -VMName <vm name> -ResourceGroupName <resource group name> -ForceRerun <Any string different from any last value used>
```

Gdzie: 
- Nazwa rozszerzenia i typ dla systemu Windows to:`ManagedIdentityExtensionForWindows`
- Nazwa rozszerzenia i typ dla Linuksa to:`ManagedIdentityExtensionForLinux`

#### <a name="automation-script-fails-when-attempting-schema-export-for-managed-identities-for-azure-resources-extension"></a>"Skrypt automatyzacji" kończy się niepowodzeniem podczas próby wyeksportowania schematu dla tożsamości zarządzanych dla rozszerzenia zasobów platformy Azure

Gdy zarządzane tożsamości zasobów platformy Azure jest włączona na maszynie wirtualnej, następujący błąd jest wyświetlany podczas próby użycia funkcji "Skrypt automatyzacji" dla maszyny wirtualnej lub jej grupy zasobów:

![Identyfikatory zarządzane dla błędu eksportu skryptu automatyzacji zasobów platformy Azure](./media/howto-migrate-vm-extension/automation-script-export-error.png)

Tożsamości zarządzane dla zasobów platformy Azure rozszerzenie maszyny wirtualnej nie obsługuje obecnie możliwość eksportowania jego schematu do szablonu grupy zasobów. W rezultacie wygenerowany szablon nie pokazuje parametrów konfiguracji, aby włączyć zarządzane tożsamości zasobów platformy Azure w zasobie. Te sekcje można dodać ręcznie, postępując zgodnie z przykładami w [Konfigurowanie tożsamości zarządzanych dla zasobów platformy Azure na maszynie wirtualnej platformy Azure przy użyciu szablonów.](qs-configure-template-windows-vm.md)

Gdy funkcja eksportu schematu stanie się dostępna dla tożsamości zarządzanych dla rozszerzenia maszyny wirtualnej zasobów platformy Azure (planowana do wycofania w styczniu 2019 r.), zostanie ona wyświetlona w [obszarze Eksportowanie grup zasobów zawierających rozszerzenia maszyn wirtualnych.](../../virtual-machines/extensions/export-templates.md#supported-virtual-machine-extensions)

## <a name="limitations-of-the-virtual-machine-extension"></a>Ograniczenia rozszerzenia maszyny wirtualnej 

Istnieje kilka głównych ograniczeń przy użyciu rozszerzenia maszyny wirtualnej. 

 * Najpoważniejszym ograniczeniem jest fakt, że poświadczenia używane do żądania tokenów są przechowywane na maszynie wirtualnej. Osoba atakująca, która pomyślnie narusza maszynę wirtualną, może eksfiltrować poświadczenia. 
 * Ponadto rozszerzenie maszyny wirtualnej jest nadal nieobsługiwał przez kilka dystrybucji Linuksa, z ogromnym kosztem rozwoju, aby zmodyfikować, zbudować i przetestować rozszerzenie na każdej z tych dystrybucji. Obecnie obsługiwane są tylko następujące dystrybucje Linuksa: 
    * CoreOS Stabilny
    * CentOS 7,1 
    * Czerwony Kapelusz 7.2 
    * Ubuntu 15.04 
    * Ubuntu 16.04
 * Ma wpływ na wydajność wdrażania maszyn wirtualnych z tożsamościami zarządzanymi, ponieważ rozszerzenie maszyny wirtualnej również musi zostać zainicjowane. 
 * Na koniec rozszerzenie maszyny wirtualnej może obsługiwać tylko 32 tożsamości zarządzane przypisane przez użytkownika na maszynę wirtualną. 

## <a name="azure-instance-metadata-service"></a>Usługa metadanych wystąpienia platformy Azure

[Usługa metadanych wystąpienia platformy Azure (IMDS)](/azure/virtual-machines/windows/instance-metadata-service) jest punktem końcowym REST, który zawiera informacje o uruchamianiu wystąpień maszyny wirtualnej, które mogą służyć do zarządzania i konfigurowania maszyn wirtualnych. Punkt końcowy jest dostępny pod dobrze znanym nie rutowalnym adresem IP (`169.254.169.254`), do który można uzyskać dostęp tylko z poziomu maszyny wirtualnej.

Istnieje kilka zalet przy użyciu usługi Azure IMDS do żądania tokenów. 

1. Usługa jest zewnętrzna do maszyny wirtualnej, w związku z tym poświadczenia używane przez tożsamości zarządzane nie są już obecne na maszynie wirtualnej. Zamiast tego są one hostowane i zabezpieczone na maszynie hosta maszyny wirtualnej platformy Azure.   
2. Wszystkie systemy operacyjne Windows i Linux obsługiwane na platformie Azure IaaS mogą używać tożsamości zarządzanych.
3. Wdrażanie jest szybsze i łatwiejsze, ponieważ rozszerzenie maszyny Wirtualnej nie musi być już aprowizowana.
4. W punkcie końcowym IMDS do 1000 tożsamości zarządzanych przypisanych przez użytkownika można przypisać do jednej maszyny wirtualnej.
5. Nie ma żadnych istotnych zmian w żądaniach przy użyciu imds w przeciwieństwie do tych, przy użyciu rozszerzenia maszyny wirtualnej, w związku z tym jest dość proste do portu w istniejących wdrożeń, które obecnie używają rozszerzenia maszyny wirtualnej.

Z tych powodów usługa Azure IMDS będzie de facto sposobem żądania tokenów, gdy rozszerzenie maszyny wirtualnej zostanie przestarzałe. 


## <a name="next-steps"></a>Następne kroki

* [Jak używać zarządzanych tożsamości dla zasobów platformy Azure na maszynie wirtualnej platformy Azure w celu uzyskania tokenu dostępu](how-to-use-vm-token.md)
* [Usługa metadanych wystąpienia platformy Azure](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)
