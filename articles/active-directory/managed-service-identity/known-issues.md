---
title: Często zadawane pytania i znane problemy z zarządzania usługi tożsamości (MSI) dla usługi Azure Active Directory
description: Znane problemy z zarządzanych tożsamości usługi dla usługi Azure Active Directory.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.assetid: 2097381a-a7ec-4e3b-b4ff-5d2fb17403b6
ms.service: active-directory
ms.component: msi
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 12/12/2017
ms.author: daveba
ms.openlocfilehash: 552f9e7cae4d7f46ea1548cfe7d9482bff79e5bc
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
ms.locfileid: "33930990"
---
# <a name="faqs-and-known-issues-with-managed-service-identity-msi-for-azure-active-directory"></a>Często zadawane pytania i znane problemy z zarządzania usługi tożsamości (MSI) dla usługi Azure Active Directory

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

## <a name="frequently-asked-questions-faqs"></a>Często zadawane pytania

### <a name="does-msi-work-with-azure-cloud-services"></a>MSI działa z usługami w chmurze Azure?

Nie, nie ma żadnych planów obsługi MSI w usługi w chmurze Azure.

### <a name="does-msi-work-with-the-active-directory-authentication-library-adal-or-the-microsoft-authentication-library-msal"></a>MSI działa z biblioteki uwierzytelniania usługi Active Directory (ADAL) lub biblioteki uwierzytelniania firmy Microsoft (MSAL)?

Nie, MSI nie jest jeszcze zintegrowana z biblioteką ADAL lub MSAL. Aby uzyskać więcej informacji na temat pozyskiwania token MSI przy użyciu punktu końcowego MSI REST, zobacz [sposobu użycia Azure VM zarządzane usługi tożsamości (MSI) do przejęcia tokenu](how-to-use-vm-token.md).

### <a name="what-is-the-security-boundary-of-a-managed-service-identity"></a>Co to jest granicy zabezpieczeń tożsamości usługi zarządzania?

Granica bezpieczeństwa tożsamości jest zasobów, do której jest dołączona do. Na przykład granicy zabezpieczeń MSI maszyny wirtualnej, jest maszyny wirtualnej. Dowolnego kodu uruchomionego na danej maszynie Wirtualnej jest w stanie połączenia punktu końcowego MSI i żądań tokenów. Jest podobne możliwości z innych zasobów, które obsługują MSI.

### <a name="should-i-use-the-msi-vm-imds-endpoint-or-the-msi-vm-extension-endpoint"></a>Należy użyć punktu końcowego MSI IMDS maszyny Wirtualnej lub punktu końcowego rozszerzenia maszyny Wirtualnej MSI?

Korzystając z pliku MSI z maszynami wirtualnymi, zaleca się przy użyciu punktu końcowego MSI IMDS. Usługa Azure wystąpienie metadanych jest punkt końcowy REST jest dostępny dla wszystkich maszyn wirtualnych IaaS utworzone za pomocą Menedżera zasobów Azure. Korzyści wynikające ze stosowania MSI za pośrednictwem IMDS należą:

1. Wszystkie systemy operacyjne obsługiwane IaaS platformy Azure można użyć MSI za pośrednictwem IMDS. 
2. Nie trzeba zainstalować rozszerzenie na maszynie Wirtualnej, aby włączyć MSI. 
3. Certyfikaty używane przez Instalatora MSI nie są już obecny w maszynie Wirtualnej. 
4. Punkt końcowy IMDS jest dobrze znanego nierutowalny adresu IP, dostępne tylko z poziomu maszyny Wirtualnej. 

Rozszerzenia maszyny Wirtualnej MSI jest nadal dostępne do użycia oprogramowania; jednak następnych firma Microsoft będzie domyślnie korzysta z punktu końcowego IMDS. Rozszerzenia maszyny Wirtualnej MSI rozpocznie planu amortyzacja wkrótce. 

Aby uzyskać więcej informacji w usłudze Metada wystąpienia platformy Azure, zobacz [IMDS dokumentacji](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)

### <a name="what-are-the-supported-linux-distributions"></a>Co to są obsługiwane dystrybucje systemu Linux?

Wszystkie dystrybucje systemu Linux obsługiwane przez IaaS platformy Azure może służyć msi za pośrednictwem punktu końcowego IMDS. 

Uwaga: MSI rozszerzenia maszyny Wirtualnej obsługuje tylko następujące dystrybucje systemu Linux:
- Stały CoreOS
- CentOS 7.1
- RedHat 7.2
- Ubuntu 15.04
- Ubuntu 16.04

Inne dystrybucje systemu Linux są obecnie nieobsługiwane i rozszerzenia może zakończyć się niepowodzeniem na nieobsługiwany dystrybucji.

Rozszerzenie działa na 6,9 CentOS. Jednak z powodu braku obsługi systemu w 6,9, rozszerzenia nie automatycznie uruchom ponownie Jeśli awaria lub zatrzymana. Jej ponownym uruchomieniu, po ponownym uruchomieniu maszyny Wirtualnej. Aby ręcznie ponownie uruchomić rozszerzenia, zobacz [jak jest ponownym z rozszerzeniem MSI?](#how-do-you-restart-the-msi-extension)

### <a name="how-do-you-restart-the-msi-extension"></a>Jak ponownym z rozszerzeniem MSI?
W systemach Windows i niektóre wersje systemu Linux Jeśli rozszerzenie zostanie zatrzymana, następującego polecenia cmdlet można ręcznie uruchomić ponownie:

```powershell
Set-AzureRmVMExtension -Name <extension name>  -Type <extension Type>  -Location <location> -Publisher Microsoft.ManagedIdentity -VMName <vm name> -ResourceGroupName <resource group name> -ForceRerun <Any string different from any last value used>
```

Gdzie: 
- Nazwa rozszerzenia i typu dla systemu Windows: ManagedIdentityExtensionForWindows
- Nazwa rozszerzenia i typu dla systemu Linux jest: ManagedIdentityExtensionForLinux

## <a name="known-issues"></a>Znane problemy

### <a name="automation-script-fails-when-attempting-schema-export-for-msi-extension"></a>"Skrypt automatyzacji" kończy się niepowodzeniem podczas próby eksportowania schematu dla rozszerzenia pliku MSI

Po włączeniu zarządzane tożsamości usługi na maszynie Wirtualnej następujący błąd jest wyświetlany podczas próby użycia funkcji "Skrypt automatyzacji" dla maszyny Wirtualnej lub jego grupa zasobów:

![Wystąpił błąd MSI automatyzacji skrypt eksportu](../media/msi-known-issues/automation-script-export-error.png)

Rozszerzenia zarządzane wirtualna tożsamość usługi nie obsługuje obecnie możliwość eksportowania schematem do szablonu grupy zasobów. W związku z tym wygenerowanego szablonu nie są wyświetlane parametry konfiguracji, Włącz zarządzane tożsamość usługi zasobu. Poniższe sekcje mogą być dodawane ręcznie, wykonując przykłady w [konfigurowania tożsamości usługi maszyn wirtualnych zarządzanych za pomocą szablonu](qs-configure-template-windows-vm.md).

Gdy funkcja eksportu schematu stają się dostępne dla rozszerzenia maszyny Wirtualnej MSI, będzie wyświetlane w [eksportowanie grupy zasobów, która zawiera rozszerzenia maszyny Wirtualnej](../../virtual-machines/extensions/export-templates.md#supported-virtual-machine-extensions).

### <a name="configuration-blade-does-not-appear-in-the-azure-portal"></a>Blok konfiguracji nie są wyświetlane w portalu Azure

Jeśli nie ma bloku konfiguracji maszyny Wirtualnej na maszynie Wirtualnej, następnie MSI nie zostało włączone w portalu w danym regionie jeszcze.  Sprawdź ponownie później.  Można również włączyć MSI do maszyny Wirtualnej przy użyciu [PowerShell](qs-configure-powershell-windows-vm.md) lub [interfejsu wiersza polecenia Azure](qs-configure-cli-windows-vm.md).

### <a name="cannot-assign-access-to-virtual-machines-in-the-access-control-iam-blade"></a>Nie można przypisać dostęp do maszyn wirtualnych w bloku kontroli dostępu (IAM)

Jeśli **maszyny wirtualnej** nie są wyświetlane w portalu Azure jako rozwiązaniem dla **przypisany dostęp** w **kontroli dostępu (IAM)** > **Dodaj uprawnienia**, a następnie zarządzane tożsamość usługi nie został włączony w portalu w danym regionie jeszcze. Sprawdź ponownie później.  Możesz również zaznaczyć tożsamość usługi zarządzania dla przypisania roli, wyszukując dla MSI nazwy głównej usługi.  Wprowadź nazwę maszyny Wirtualnej w ramach **wybierz** pola i nazwy głównej usługi pojawia się w wynikach wyszukiwania.

### <a name="vm-fails-to-start-after-being-moved-from-resource-group-or-subscription"></a>Maszyna wirtualna nie została uruchomiona po przeniesieniu z grupy zasobów lub subskrypcji

Jeśli przenosisz Maszynę wirtualną w stanie uruchomienia, nadal działa podczas przenoszenia. Jednak po przeniesieniu, jeśli maszyna wirtualna zostanie zatrzymana i ponownie uruchomione, nie będzie można uruchomić. Ten problem odbywa się, ponieważ maszyna wirtualna nie jest aktualizowana odwołanie do tożsamości MSI i wskaż w starym grupy zasobów w dalszym ciągu.

**Obejście problemu** 
 
Wyzwalają aktualizację na maszynie Wirtualnej, aby go pobrać poprawne wartości dla MSI. Możliwość zmiany właściwości maszyny Wirtualnej, aby zaktualizować odwołania do tożsamości MSI. Na przykład można ustawić nową wartość tagu na Maszynie wirtualnej za pomocą następującego polecenia:

```azurecli-interactive
 az  vm update -n <VM Name> -g <Resource Group> --set tags.fixVM=1
```
 
To polecenie ustawia nowy znacznik "fixVM" o wartości 1 na maszynie Wirtualnej. 
 
Przez ustawienie dla tej właściwości, maszyna wirtualna aktualizuje poprawny identyfikator URI zasobu MSI i następnie można uruchomić maszyny Wirtualnej. 
 
Po uruchomieniu maszyny Wirtualnej, można usunąć tagu za pomocą następujących poleceń:

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --remove tags.fixVM
```

## <a name="known-issues-with-user-assigned-identities"></a>Znane problemy z tożsamościami przypisany użytkownik

- Przypisania przypisane tożsamości użytkownika są tylko tym dostępną dla maszyny Wirtualnej i VMSS. Ważne: Przypisania tożsamości przypisany użytkownik zmieni się w ciągu przyszłych miesięcy.
- Zduplikowana przypisane tożsamości użytkowników w tej samej maszyny Wirtualnej/VMSS, spowoduje, że maszyna wirtualna/VMSS się niepowodzeniem. W tym tożsamości, które są dodawane z innej wielkości znaków. np. MyUserAssignedIdentity i myuserassignedidentity. 
- Inicjowanie obsługi rozszerzenia maszyny Wirtualnej do maszyny Wirtualnej może zakończyć się niepowodzeniem z powodu błędów wyszukiwania DNS. Uruchom ponownie maszynę Wirtualną, a następnie spróbuj ponownie. 
- Dodawanie tożsamości przypisane przez użytkownika "nieistniejącego" spowoduje, że maszyna wirtualna może się nie powieść. 
- Tworzenie użytkownika przypisanego tożsamości z znaki specjalne (np. podkreślenie) w nazwie, nie jest obsługiwane.
- Użytkownik, któremu przypisano nazwy tożsamości są ograniczone do 24 znaków dla scenariusza pełnego. Tożsamości przypisanych użytkowników o nazwach dłużej niż 24 znaki zakończy się niepowodzeniem do przypisania.  
- Po dodaniu drugiego użytkownika przypisaniu tożsamości, clientID może być niedostępne dla żądań tokenów dla rozszerzenia maszyny Wirtualnej. Jako ograniczenie Uruchom ponownie rozszerzenia MSI maszyny Wirtualnej za pomocą następujących poleceń dwóch bash:
 - `sudo bash -c "/var/lib/waagent/Microsoft.ManagedIdentity.ManagedIdentityExtensionForLinux-1.0.0.8/msi-extension-handler disable"`
 - `sudo bash -c "/var/lib/waagent/Microsoft.ManagedIdentity.ManagedIdentityExtensionForLinux-1.0.0.8/msi-extension-handler enable"`
- Gdy maszyna wirtualna ma przypisany tożsamości użytkownika, ale nie systemu przypisane tożsamości, portalu interfejsu użytkownika zostanie wyświetlone MSI jako wyłączone. Aby włączyć system przypisane tożsamości, należy użyć szablonu usługi Azure Resource Manager, Azure CLI lub zestawu SDK.
