---
title: Często zadawane pytania i znane problemy z tożsamość usługi zarządzanej (MSI) dla usługi Azure Active Directory
description: Znane problemy związane z tożsamości usługi zarządzanej dla usługi Azure Active Directory.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.assetid: 2097381a-a7ec-4e3b-b4ff-5d2fb17403b6
ms.service: active-directory
ms.component: msi
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 12/12/2017
ms.author: daveba
ms.openlocfilehash: 05096050dfc29aebd2859b298eef884dcd9a1111
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2018
ms.locfileid: "37908077"
---
# <a name="faqs-and-known-issues-with-managed-service-identity-msi-for-azure-active-directory"></a>Często zadawane pytania i znane problemy z tożsamość usługi zarządzanej (MSI) dla usługi Azure Active Directory

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

## <a name="frequently-asked-questions-faqs"></a>Często zadawane pytania

### <a name="does-msi-work-with-azure-cloud-services"></a>MSI działa z usługami w chmurze platformy Azure?

Nie, nie ma żadnych planów, do obsługi tożsamości usługi Zarządzanej w usłudze Azure Cloud Services.

### <a name="does-msi-work-with-the-active-directory-authentication-library-adal-or-the-microsoft-authentication-library-msal"></a>MSI działa przy użyciu biblioteki uwierzytelniania usługi Active Directory (ADAL) lub biblioteka Microsoft Authentication Library (MSAL)?

Nie, MSI nie jest jeszcze zintegrowany z biblioteki ADAL lub biblioteki MSAL. Aby uzyskać szczegółowe informacje na temat pozyskiwania tokenu MSI przy użyciu punktu końcowego MSI REST, zobacz [jak używać usługi Azure VM tożsamość usługi zarządzanej (MSI) dla tokenu](how-to-use-vm-token.md).

### <a name="what-is-the-security-boundary-of-a-managed-service-identity"></a>Co to jest granicy zabezpieczeń tożsamości usługi zarządzanej?

Granicy zabezpieczeń tożsamości jest zasób, do której jest dołączony do. Na przykład granicy zabezpieczeń z tożsamości usługi Zarządzanej maszyny wirtualnej, to maszyna wirtualna. Każdy kod uruchomiony na tej maszynie Wirtualnej jest w stanie wywołać punkt końcowy MSI i żądać tokenów. Jest podobnie środowisko z innymi zasobami, które obsługują MSI.

### <a name="should-i-use-the-msi-vm-imds-endpoint-or-the-msi-vm-extension-endpoint"></a>Należy używać punktu końcowego IMDS maszyny Wirtualnej MSI lub Instalatora MSI rozszerzenia punktu końcowego maszyny Wirtualnej?

Korzystając z pliku MSI z maszynami wirtualnymi, firma Microsoft zachęca przy użyciu punktu końcowego MSI IMDS. Azure Instance Metadata Service jest punkt końcowy REST dostępne dla wszystkich maszyn wirtualnych IaaS utworzone za pomocą usługi Azure Resource Manager. Korzyści z używania tożsamości usługi Zarządzanej za pośrednictwem IMDS, należą:

1. Wszystkie systemy operacyjne obsługiwane modelu IaaS platformy Azure mogą używać tożsamości usługi Zarządzanej za pośrednictwem IMDS. 
2. Nie są już potrzebne, można zainstalować rozszerzenia na maszynie Wirtualnej, aby włączyć tożsamości usługi Zarządzanej. 
3. Certyfikaty używane przez Instalatora MSI nie są już dostępne na maszynie wirtualnej. 
4. Punkt końcowy IMDS jest dobrze znanego nierutowalny adresu IP, dostępne tylko z poziomu maszyny Wirtualnej. 

Rozszerzenie maszyny Wirtualnej MSI jest nadal dostępne do użycia oprogramowania; jednak pory firma Microsoft będzie domyślnie przy użyciu punktu końcowego IMDS. Rozszerzenie maszyny Wirtualnej MSI wkrótce się rozpocznie w ramach planu obsługi. 

Aby uzyskać więcej informacji na temat usługi Metada wystąpienia platformy Azure, zobacz [IMDS dokumentacji](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)

### <a name="what-are-the-supported-linux-distributions"></a>Co to są obsługiwane dystrybucje systemu Linux?

Wszystkie dystrybucje systemu Linux obsługiwane przez IaaS platformy Azure może służyć za pomocą pliku MSI za pośrednictwem punktu końcowego IMDS. 

Uwaga: MSI rozszerzenia maszyny Wirtualnej obsługuje tylko poniższe dystrybucje systemu Linux:
- Stabilny systemu CoreOS
- CentOS 7.1
- Red Hat 7.2
- Ubuntu 15.04
- Ubuntu 16.04

Inne dystrybucje systemu Linux nie są obecnie obsługiwane, a rozszerzenie może zakończyć się niepowodzeniem w nieobsługiwanych dystrybucjach.

To rozszerzenie działa na 6,9 CentOS. Jednak ze względu na brak obsługi systemu w 6,9, rozszerzenie nie automatycznie ponownego uruchomienia jeśli wystąpiła awaria lub zatrzymana. Ponownego uruchomienia po ponownym uruchomieniu maszyny Wirtualnej. Aby ręcznie uruchomić ponownie rozszerzenie, zobacz [jak jest ponownym rozszerzenia MSI?](#how-do-you-restart-the-msi-extension)

### <a name="how-do-you-restart-the-msi-extension"></a>Jak ponownym rozszerzenia MSI?
W systemie Windows i niektóre wersje systemu Linux Jeśli rozszerzenie zostanie zatrzymana, następujące polecenie cmdlet może służyć do ręcznie uruchomić ponownie:

```powershell
Set-AzureRmVMExtension -Name <extension name>  -Type <extension Type>  -Location <location> -Publisher Microsoft.ManagedIdentity -VMName <vm name> -ResourceGroupName <resource group name> -ForceRerun <Any string different from any last value used>
```

Gdzie: 
- Rozszerzenie nazwy i typu dla Windows: ManagedIdentityExtensionForWindows
- Rozszerzenie nazwy i typu dla systemu Linux jest: ManagedIdentityExtensionForLinux

## <a name="known-issues"></a>Znane problemy

### <a name="automation-script-fails-when-attempting-schema-export-for-msi-extension"></a>"Skrypt automatyzacji" kończy się niepowodzeniem podczas próby eksportu schematu dla rozszerzenia pliku MSI

Po włączeniu tożsamości usługi zarządzanej maszyny wirtualnej, podczas próby korzystania z funkcji "Skrypt automatyzacji" dla maszyny Wirtualnej lub jej grupy zasobów jest wyświetlany następujący błąd:

![Błąd eksportowania skryptów automatyzacji Instalatora MSI](../media/msi-known-issues/automation-script-export-error.png)

Zarządzane usługi tożsamości rozszerzenia maszyny Wirtualnej nie obsługuje obecnie możliwość eksportowania jego schematu do szablonu grupy zasobów. W rezultacie w wygenerowany szablon nie są wyświetlane parametry konfiguracji, aby włączyć tożsamości usługi zarządzanej dla zasobu. Poniższe sekcje mogą być dodawane ręcznie, wykonując na potrzeby przykładów w [Konfigurowanie tożsamości usługi zarządzanej maszyny Wirtualnej przy użyciu szablonu](qs-configure-template-windows-vm.md).

Gdy funkcja eksportu schematu staje się dostępna dla rozszerzenia maszyny Wirtualnej MSI, będzie ono wyświetlane w [eksportowanie grupy zasobów zawierające rozszerzeń maszyn wirtualnych](../../virtual-machines/extensions/export-templates.md#supported-virtual-machine-extensions).

### <a name="configuration-blade-does-not-appear-in-the-azure-portal"></a>Blok konfiguracji nie są wyświetlane w witrynie Azure portal

Jeśli nie ma bloku konfiguracji maszyny Wirtualnej na maszynie Wirtualnej, następnie MSI nie zostało włączone w portalu w Twoim regionie jeszcze.  Sprawdź ponownie później.  Można również włączyć tożsamości usługi Zarządzanej dla maszyny Wirtualnej przy użyciu [PowerShell](qs-configure-powershell-windows-vm.md) lub [wiersza polecenia platformy Azure](qs-configure-cli-windows-vm.md).

### <a name="cannot-assign-access-to-virtual-machines-in-the-access-control-iam-blade"></a>Nie można przypisać dostępu do maszyn wirtualnych w bloku kontrola dostępu (IAM)

Jeśli **maszyny wirtualnej** nie jest wyświetlany w witrynie Azure portal jako wyborem dla **Przypisz dostęp do** w **kontrola dostępu (IAM)** > **Dodaj uprawnienia**, a następnie tożsamości usługi zarządzanej nie został włączony w portalu w Twoim regionie jeszcze. Sprawdź ponownie później.  Nadal możesz wybrać tożsamości usługi zarządzanej, przypisania roli przez wyszukiwanie dla jednostki usługi MSI.  Wprowadź nazwę maszyny Wirtualnej w **wybierz** pola i nazwę główną usługi, zostanie wyświetlony w wynikach wyszukiwania.

### <a name="vm-fails-to-start-after-being-moved-from-resource-group-or-subscription"></a>Maszyna wirtualna nie została uruchomiona po jest przenoszony z grupy zasobów lub subskrypcji

Jeśli przenosisz Maszynę wirtualną w stanie uruchomienia, nadal działa podczas przenoszenia. Jednak po przeniesieniu, jeśli maszyna wirtualna zostanie zatrzymana i uruchomiona ponownie, zakończy się niepowodzeniem do uruchomienia. Ten problem występuje, ponieważ maszyna wirtualna nie aktualizuje odwołanie do tożsamości MSI i wskaż w starej grupy zasobów w dalszym ciągu.

**Obejście problemu** 
 
Aby go uzyskać prawidłowe wartości dla pliku MSI, uruchomić aktualizację na maszynie Wirtualnej. Możesz tworzyć zmiany właściwości maszyny Wirtualnej, można zaktualizować odwołania do tożsamości MSI. Na przykład można ustawić nową wartość tagu na maszynie Wirtualnej za pomocą następującego polecenia:

```azurecli-interactive
 az  vm update -n <VM Name> -g <Resource Group> --set tags.fixVM=1
```
 
To polecenie ustawia nowy tag "fixVM" o wartości 1 na maszynie Wirtualnej. 
 
Przez ustawienie tej właściwości, maszyna wirtualna zostaje zaktualizowana o poprawny identyfikator URI zasobu MSI, a następnie powinno być możliwe do uruchomienia maszyny Wirtualnej. 
 
Gdy maszyna wirtualna jest uruchomiona, można usunąć tagu za pomocą następującego polecenia:

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --remove tags.fixVM
```

## <a name="known-issues-with-user-assigned-identities"></a>Znane problemy związane z tożsamościami przypisanych użytkowników

- Przypisania przypisane do tożsamości użytkownika są tylko dostępne dla maszyny Wirtualnej i zestawu skalowania maszyn wirtualnych. Ważne: Przypisania tożsamości przypisany użytkownik zmieni się w ciągu najbliższych miesięcy.
- Zduplikowane przypisane tożsamości użytkowników na tym samym VM/VMSS spowoduje, że VM/VMSS nie powiedzie się. Dotyczy to również tożsamości, które są dodawane z inną wielkością liter. np. MyUserAssignedIdentity i myuserassignedidentity. 
- Inicjowanie obsługi rozszerzenia maszyny Wirtualnej do maszyny Wirtualnej może zakończyć się niepowodzeniem z powodu błędów wyszukiwania DNS. Uruchom ponownie maszynę Wirtualną i spróbuj ponownie. 
- Dodawanie tożsamości przypisanych przez użytkownika "nieistniejącej" spowoduje, że maszyna wirtualna może się nie powieść. 
- Tworzenie użytkownika przypisanego tożsamości przy użyciu znaków specjalnych (np. podkreślenie) w nazwie, nie jest obsługiwane.
- Nazwy tożsamości przypisanych przez użytkownika są ograniczone do 24 znaków w scenariuszu typu end to end. Tożsamości przypisanych użytkowników z nazwami dłuższe niż 24 znaki zakończy się niepowodzeniem do przypisania.
- W przypadku korzystania z tożsamości zarządzanej rozszerzenia maszyny wirtualnej obsługiwany limit jest 32 użytkownik przypisany zarządzanych tożsamości. Bez rozszerzenia tożsamości zarządzanej maszyny wirtualnej i obsługiwany limit to 512.  
- Po dodaniu drugiego użytkownika tożsamości przypisanej wartości clientID mogą być niedostępne do żądania tokenów dla rozszerzenia maszyny Wirtualnej. Środki zaradcze Uruchom ponownie rozszerzenie tożsamości usługi Zarządzanej maszyny Wirtualnej za pomocą następujących poleceń powłoki bash dwa:
 - `sudo bash -c "/var/lib/waagent/Microsoft.ManagedIdentity.ManagedIdentityExtensionForLinux-1.0.0.8/msi-extension-handler disable"`
 - `sudo bash -c "/var/lib/waagent/Microsoft.ManagedIdentity.ManagedIdentityExtensionForLinux-1.0.0.8/msi-extension-handler enable"`
- Gdy maszyna wirtualna ma tożsamości przypisanych przez użytkownika, ale nie systemu tożsamości przypisanej, portalu interfejsu użytkownika zostanie wyświetlone MSI jako wyłączone. Aby włączyć system tożsamości przypisanej, należy użyć szablonu usługi Azure Resource Manager, interfejsu wiersza polecenia platformy Azure lub zestawu SDK.
