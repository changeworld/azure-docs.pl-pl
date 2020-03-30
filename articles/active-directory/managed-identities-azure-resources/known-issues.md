---
title: Często zadawane pytania i znane problemy z tożsamościami zarządzanymi — Usługa Azure AD
description: Znane problemy z tożsamościami zarządzanymi dla zasobów platformy Azure.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 2097381a-a7ec-4e3b-b4ff-5d2fb17403b6
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 12/12/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0f0c678f2426d9de58d2ab337c56243394b4d0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266535"
---
# <a name="faqs-and-known-issues-with-managed-identities-for-azure-resources"></a>Często zadawane pytania i znane problemy z tożsamościami zarządzanymi dla zasobów platformy Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

## <a name="frequently-asked-questions-faqs"></a>Często zadawane pytania (często zadawane pytania)

> [!NOTE]
> Tożsamości zarządzane dla zasobów platformy Azure to nowa nazwa usługi znanej wcześniej jako Tożsamość usługi zarządzanej (MSI).

### <a name="does-managed-identities-for-azure-resources-work-with-azure-cloud-services"></a>Czy tożsamości zarządzane dla zasobów platformy Azure działają z usługami Azure Cloud Services?

Nie, nie ma planów obsługi zarządzanych tożsamości zasobów platformy Azure w usługach w chmurze azure.

### <a name="does-managed-identities-for-azure-resources-work-with-the-active-directory-authentication-library-adal-or-the-microsoft-authentication-library-msal"></a>Czy tożsamości zarządzane zasobów platformy Azure działają z biblioteką uwierzytelniania usługi Active Directory (ADAL) lub biblioteką uwierzytelniania firmy Microsoft (MSAL)?

Nie, tożsamości zarządzane dla zasobów platformy Azure nie są jeszcze zintegrowane z usługą ADAL lub MSAL. Aby uzyskać szczegółowe informacje na temat uzyskiwania tokenu dla tożsamości zarządzanych dla zasobów platformy Azure przy użyciu punktu końcowego REST, zobacz [Jak używać zarządzanych tożsamości dla zasobów platformy Azure na maszynie Wirtualnej platformy Azure w celu uzyskania tokenu dostępu.](how-to-use-vm-token.md)

### <a name="what-is-the-security-boundary-of-managed-identities-for-azure-resources"></a>Jaka jest granica zabezpieczeń zarządzanych tożsamości zasobów platformy Azure?

Granica zabezpieczeń tożsamości jest zasobem, do którego jest dołączony. Na przykład granica zabezpieczeń maszyny wirtualnej z tożsamościami zarządzanymi dla zasobów platformy Azure jest maszyną wirtualną. Każdy kod uruchomiony na tej maszynie wirtualnej, jest w stanie wywołać tożsamości zarządzane dla punktu końcowego zasobów platformy Azure i tokeny żądania. Jest to podobne środowisko z innymi zasobami, które obsługują tożsamości zarządzane dla zasobów platformy Azure.

### <a name="what-identity-will-imds-default-to-if-dont-specify-the-identity-in-the-request"></a>Jaka tożsamość będzie domyślnie imds, jeśli nie określić tożsamość w żądaniu?

- Jeśli przypisana przez system tożsamość zarządzana jest włączona i w żądaniu nie określono tożsamości, usługa IMDS domyślnie będzie przypisywana do tożsamości zarządzanej przypisanej przez system.
- Jeśli przypisana do systemu tożsamość zarządzana nie jest włączona i istnieje tylko jeden użytkownik przypisany do tożsamości zarządzanej, imds domyślnie do tego pojedynczego użytkownika przypisanego tożsamości zarządzanej. 
- Jeśli przypisana przez system tożsamość zarządzana nie jest włączona i istnieje wiele tożsamości zarządzanych przypisanych przez wielu użytkowników, wymagane jest określenie tożsamości zarządzanej w żądaniu.

### <a name="should-i-use-the-managed-identities-for-azure-resources-imds-endpoint-or-the-vm-extension-endpoint"></a>Czy należy używać tożsamości zarządzanych dla punktu końcowego IMDS zasobów platformy Azure lub punktu końcowego rozszerzenia maszyny Wirtualnej?

Podczas korzystania z tożsamości zarządzanych dla zasobów platformy Azure z maszynami wirtualnymi zaleca się użycie punktu końcowego IMDS. Usługa metadanych wystąpienia platformy Azure jest punktem końcowym REST dostępnym dla wszystkich maszyn wirtualnych IaaS utworzonych za pośrednictwem usługi Azure Resource Manager. 

Niektóre z zalet korzystania z zarządzanych tożsamości dla zasobów platformy Azure za pośrednictwem imds są:
- Wszystkie systemy operacyjne obsługiwane przez usługę Azure IaaS mogą używać tożsamości zarządzanych dla zasobów platformy Azure za pomocą usługi IMDS.
- Nie trzeba już instalować rozszerzenia na maszynie Wirtualnej, aby włączyć zarządzane tożsamości dla zasobów platformy Azure. 
- Certyfikaty używane przez tożsamości zarządzane dla zasobów platformy Azure nie są już obecne na maszynie Wirtualnej.
- Punkt końcowy IMDS jest dobrze znany nie rutable adres IP, tylko dostępne z wewnątrz maszyny Wirtualnej.
- 1000 tożsamości zarządzanych przypisanych przez użytkownika można przypisać do jednej maszyny Wirtualnej. 

Tożsamości zarządzane dla rozszerzenia maszyny Wirtualnej zasobów platformy Azure jest nadal dostępna; jednak nie opracowujemy już na nim nowych funkcji. Zaleca się przełączanie do korzystania z punktu końcowego IMDS. 

Niektóre ograniczenia dotyczące korzystania z punktu końcowego rozszerzenia maszyny Wirtualnej to:
- Ograniczone wsparcie dla dystrybucji Linuksa: CoreOS Stable, CentOS 7.1, Red Hat 7.2, Ubuntu 15.04, Ubuntu 16.04
- Do maszyny Wirtualnej można przypisać tylko 32 tożsamości zarządzane przypisane przez użytkownika.


Uwaga: W styczniu 2019 r. rozszerzenia maszyny Wirtualnej zarządzanych tożsamości zasobów platformy Azure będzie brakować pomocy technicznej. 

Aby uzyskać więcej informacji na temat usługi metadanych wystąpienia platformy Azure, zobacz [dokumentację imds](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)

### <a name="will-managed-identities-be-recreated-automatically-if-i-move-a-subscription-to-another-directory"></a>Czy tożsamości zarządzane zostaną automatycznie odtworzone, jeśli przeniosę subskrypcję do innego katalogu?

Nie. Jeśli przeniesiesz subskrypcję do innego katalogu, trzeba będzie ręcznie ponownie utworzyć je i ponownie przyznać przypisania roli RBAC platformy Azure.
- W przypadku tożsamości zarządzanych przypisanych do systemu: wyłącz i włącz ponownie. 
- W przypadku przypisanych przez użytkownika tożsamości zarządzanych: usuń, ponownie utwórz je i dołącz do niezbędnych zasobów (np. maszyn wirtualnych)

### <a name="can-i-use-a-managed-identity-to-access-a-resource-in-a-different-directorytenant"></a>Czy można użyć tożsamości zarządzanej, aby uzyskać dostęp do zasobu w innym katalogu/dzierżawie?

Nie. Tożsamości zarządzane nie obsługują obecnie scenariuszy między katalogami. 

### <a name="what-azure-rbac-permissions-are-required-to-managed-identity-on-a-resource"></a>Jakie uprawnienia usługi Azure RBAC są wymagane do tożsamości zarządzanej w zasobie? 

- Tożsamość zarządzana przypisana do systemu: potrzebne są uprawnienia do zapisu za zasób. Na przykład w przypadku maszyn wirtualnych potrzebne jest uprawnienie Microsoft.Compute/virtualMachines/write. Ta akcja jest uwzględniona w wbudowanych rolach specyficznych dla zasobów, takich jak [współautor maszyny wirtualnej.](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)
- Tożsamość zarządzana przypisana przez użytkownika: należy mieć uprawnienia do zapisu za zasób. Na przykład w przypadku maszyn wirtualnych potrzebne jest uprawnienie Microsoft.Compute/virtualMachines/write. Oprócz przypisania roli [operatora tożsamości zarządzanej](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#managed-identity-operator) za pomocą tożsamości zarządzanej.

### <a name="how-do-you-restart-the-managed-identities-for-azure-resources-extension"></a>Jak ponownie uruchomić tożsamości zarządzane dla rozszerzenia zasobów platformy Azure?
W systemie Windows i niektórych wersjach systemu Linux, jeśli rozszerzenie zostanie zatrzymane, do ręcznego ponownego uruchomienia może być użyte następujące polecenie cmdlet:

```powershell
Set-AzVMExtension -Name <extension name>  -Type <extension Type>  -Location <location> -Publisher Microsoft.ManagedIdentity -VMName <vm name> -ResourceGroupName <resource group name> -ForceRerun <Any string different from any last value used>
```

Gdzie: 
- Nazwa i typ rozszerzenia dla systemu Windows to: ManagedIdentityExtensionForWindows
- Nazwa i typ rozszerzenia dla systemu Linux to: ManagedIdentityExtensionForLinux

## <a name="known-issues"></a>Znane problemy

### <a name="automation-script-fails-when-attempting-schema-export-for-managed-identities-for-azure-resources-extension"></a>"Skrypt automatyzacji" kończy się niepowodzeniem podczas próby wyeksportowania schematu dla tożsamości zarządzanych dla rozszerzenia zasobów platformy Azure

Gdy zarządzane tożsamości zasobów platformy Azure jest włączona na maszynie Wirtualnej, następujący błąd jest wyświetlany podczas próby użycia funkcji "Skrypt automatyzacji" dla maszyny Wirtualnej lub jej grupy zasobów:

![Identyfikatory zarządzane dla błędu eksportu skryptu automatyzacji zasobów platformy Azure](./media/msi-known-issues/automation-script-export-error.png)

Tożsamości zarządzane dla rozszerzenia maszyny Wirtualnej zasobów platformy Azure (planowane do wycofania w styczniu 2019 r.) obecnie nie obsługuje możliwości eksportowania jego schematu do szablonu grupy zasobów. W rezultacie wygenerowany szablon nie pokazuje parametrów konfiguracji, aby włączyć zarządzane tożsamości zasobów platformy Azure w zasobie. Te sekcje można dodać ręcznie, postępając zgodnie z przykładami w [Konfigurowanie tożsamości zarządzanych dla zasobów platformy Azure na maszynie Wirtualnej platformy Azure przy użyciu szablonów.](qs-configure-template-windows-vm.md)

Gdy funkcja eksportu schematu stanie się dostępna dla tożsamości zarządzanych dla rozszerzenia maszyny Wirtualnej zasobów platformy Azure (planowanego do wycofania w styczniu 2019 r.), zostanie ona wyświetlona w [obszarze Eksportowanie grup zasobów zawierających rozszerzenia maszyn wirtualnych.](../../virtual-machines/extensions/export-templates.md#supported-virtual-machine-extensions)

### <a name="vm-fails-to-start-after-being-moved-from-resource-group-or-subscription"></a>Maszyna wirtualna nie może się uruchomić po przeniesieniu z grupy zasobów lub subskrypcji

Jeśli przeniesiesz maszynę wirtualną w stanie uruchomionym, będzie ona nadal działać podczas przenoszenia. Jednak po przeniesieniu, jeśli maszyna wirtualna zostanie zatrzymana i ponownie uruchomiona, nie można uruchomić. Ten problem występuje, ponieważ maszyna wirtualna nie aktualizuje odwołania do tożsamości zarządzanych dla tożsamości zasobów platformy Azure i nadal wskazuje na nią w starej grupie zasobów.

**Obejście** 
 
Wyzwalanie aktualizacji na maszynie wirtualnej, dzięki czemu można uzyskać poprawne wartości dla tożsamości zarządzanych dla zasobów platformy Azure. Można wykonać zmianę właściwości maszyny Wirtualnej, aby zaktualizować odwołanie do tożsamości zarządzanych dla tożsamości zasobów platformy Azure. Na przykład można ustawić nową wartość znacznika na maszynie wirtualnej za pomocą następującego polecenia:

```azurecli-interactive
 az  vm update -n <VM Name> -g <Resource Group> --set tags.fixVM=1
```
 
To polecenie ustawia nowy tag "fixVM" o wartości 1 na maszynie wirtualnej. 
 
Po ustawieniu tej właściwości maszyna wirtualna aktualizuje się z poprawnymi tożsamościami zarządzanymi dla identyfikatora URI zasobów zasobów platformy Azure, a następnie powinieneś mieć możliwość uruchomienia maszyny Wirtualnej. 
 
Po uruchomieniu maszyny Wirtualnej znacznik można usunąć za pomocą następującego polecenia:

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --remove tags.fixVM
```

### <a name="vm-extension-provisioning-fails"></a>Inicjowanie obsługi administracyjnej rozszerzenia maszyny Wirtualnej kończy się niepowodzeniem

Inicjowanie obsługi administracyjnej rozszerzenia maszyny Wirtualnej może zakończyć się niepowodzeniem z powodu błędów wyszukiwania DNS. Uruchom ponownie maszynę wirtualną i spróbuj ponownie.
 
> [!NOTE]
> Rozszerzenie maszyny Wirtualnej planowane jest do wycofania do stycznia 2019. Zaleca się przejście do korzystania z punktu końcowego IMDS.

### <a name="transferring-a-subscription-between-azure-ad-directories"></a>Przenoszenie subskrypcji między katalogami usługi Azure AD

Tożsamości zarządzane nie są aktualizowane, gdy subskrypcja jest przenoszona/przenoszona do innego katalogu. W rezultacie wszelkie istniejące tożsamości zarządzane przypisane do systemu lub przypisane przez użytkownika zostaną uszkodzone. 

Obejście tożsamości zarządzanych w subskrypcji, która została przeniesiona do innego katalogu:

 - W przypadku tożsamości zarządzanych przypisanych do systemu: wyłącz i włącz ponownie. 
 - W przypadku przypisanych przez użytkownika tożsamości zarządzanych: usuń, ponownie utwórz je i dołącz do niezbędnych zasobów (np. maszyn wirtualnych)

### <a name="moving-a-user-assigned-managed-identity-to-a-different-resource-groupsubscription"></a>Przenoszenie tożsamości zarządzanej przypisanej przez użytkownika do innej grupy/subskrypcji zasobów

Przeniesienie tożsamości zarządzanej przypisanej przez użytkownika do innej grupy zasobów spowoduje przerwanie tożsamości. W rezultacie zasoby (np. maszyna wirtualna) przy użyciu tej tożsamości nie będą mogły żądać tokenów dla niego. 
