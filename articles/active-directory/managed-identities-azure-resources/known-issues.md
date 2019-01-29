---
title: Często zadawane pytania i znane problemy związane z zarządzanych tożsamości dla zasobów platformy Azure
description: Znane problemy związane z zarządzanych tożsamości dla zasobów platformy Azure.
services: active-directory
documentationcenter: ''
author: priyamohanram
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
ms.author: priyamo
ms.openlocfilehash: 45bf76696269e1224250b834b67acb2a68c10d7c
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55189463"
---
# <a name="faqs-and-known-issues-with-managed-identities-for-azure-resources"></a>Często zadawane pytania i znane problemy związane z zarządzanych tożsamości dla zasobów platformy Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

## <a name="frequently-asked-questions-faqs"></a>Często zadawane pytania

> [!NOTE]
> Tożsamości zarządzane dla zasobów platformy Azure to nowa nazwa usługi znanej wcześniej jako Tożsamość usługi zarządzanej (MSI).

### <a name="does-managed-identities-for-azure-resources-work-with-azure-cloud-services"></a>Zarządzanych tożsamości dla zasobów platformy Azure działa z usługami w chmurze platformy Azure?

Nie, nie ma żadnych planów, aby obsługiwać zarządzanych tożsamości na potrzeby zasobów platformy Azure w usługach Azure Cloud Services.

### <a name="does-managed-identities-for-azure-resources-work-with-the-active-directory-authentication-library-adal-or-the-microsoft-authentication-library-msal"></a>Zarządzanych tożsamości dla zasobów platformy Azure działa z Active Directory Authentication Library (ADAL) lub biblioteka Microsoft Authentication Library (MSAL)?

Dla zasobów platformy Azure nie jest jeszcze zintegrowana z biblioteką ADAL lub biblioteki MSAL, nie, zarządzanych tożsamości. Aby uzyskać więcej informacji na temat pozyskiwania token dla zarządzanych tożsamości dla zasobów platformy Azure przy użyciu punktu końcowego REST, zobacz [jak uzyskiwanie tokenu dostępu, za pomocą tożsamości zarządzanych zasobów platformy Azure na Maszynie wirtualnej platformy Azure ](how-to-use-vm-token.md).

### <a name="what-is-the-security-boundary-of-managed-identities-for-azure-resources"></a>Co to jest granicy zabezpieczeń z zarządzanych tożsamości dla zasobów platformy Azure?

Granicy zabezpieczeń tożsamości jest zasób, do której jest dołączony do. Na przykład granice zabezpieczeń dla maszyny wirtualnej z zarządzanych tożsamości dla zasobów platformy Azure jest włączone, maszyna wirtualna. Każdy kod uruchomiony na tej maszynie Wirtualnej jest w stanie wywołać zarządzanych tożsamości dla zasobów platformy Azure z punktu końcowego i żądania tokenów. Jest podobnie środowisko z innymi zasobami, które obsługują zarządzanych tożsamości dla zasobów platformy Azure.

### <a name="what-identity-will-imds-default-to-if-dont-specify-the-identity-in-the-request"></a>Jakie tożsamości zostanie domyślnie IMDS if określać tożsamość w żądaniu?

- Jeśli włączono przypisanej tożsamość zarządzaną w systemie, a nie tożsamości jest określony w żądaniu, domyślnie zostanie IMDS przypisanego tożsamości zarządzanej przez system.
- Jeśli system przypisane tożsamość zarządzana nie jest włączony, a istnieje tylko jeden użytkownik przypisany tożsamości zarządzanej, IMDS będą domyślnie do tego pojedynczego zarządzanych tożsamości przypisanych przez użytkownika. 
- Jeśli system przypisane tożsamość zarządzana nie jest włączony, a istnieje wielu użytkowników zarządzanych tożsamości przypisanych przez, następnie podając tożsamość zarządzaną w żądaniu jest wymagana.

### <a name="should-i-use-the-managed-identities-for-azure-resources-vm-imds-endpoint-or-the-vm-extension-endpoint"></a>Dla punktu końcowego maszyny Wirtualnej IMDS zasobów platformy Azure lub punktu końcowego z rozszerzenia maszyny Wirtualnej należy używać zarządzanych tożsamości?

Korzystając z zarządzanych tożsamości dla zasobów platformy Azure z maszynami wirtualnymi, firma Microsoft zachęca do endpoint IMDS zasobów platformy Azure przy użyciu zarządzanych tożsamości. Azure Instance Metadata Service jest punkt końcowy REST dostępne dla wszystkich maszyn wirtualnych IaaS utworzone za pomocą usługi Azure Resource Manager. Korzyści z używania zarządzanych tożsamości dla zasobów platformy Azure za pośrednictwem IMDS, należą:
    - Wszystkie systemy operacyjne obsługiwane modelu IaaS platformy Azure mogą używać zarządzanych tożsamości dla zasobów platformy Azure za pośrednictwem IMDS.
    - Nie musisz zainstalować rozszerzenie na maszynie Wirtualnej, aby umożliwić zarządzanych tożsamości dla zasobów platformy Azure. 
    - Certyfikaty używane przez zarządzanych tożsamości dla zasobów platformy Azure nie są już dostępne na maszynie wirtualnej.
    - Punkt końcowy IMDS jest dobrze znanego nierutowalny adresu IP, dostępne tylko z poziomu maszyny Wirtualnej.

Zarządzanych tożsamości dla rozszerzenia maszyny Wirtualnej zasoby platformy Azure jest nadal dostępne do użycia oprogramowania; jednak pory firma Microsoft będzie domyślnie przy użyciu punktu końcowego IMDS. Zarządzanych tożsamości dla rozszerzenia maszyny Wirtualnej zasoby platformy Azure zostaną wycofane w styczniu 2019 r. 

Aby uzyskać więcej informacji na temat usługi Azure Instance Metadata Service, zobacz [IMDS dokumentacji](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)

### <a name="will-managed-identities-be-recreated-automatically-if-i-move-a-subscription-to-another-directory"></a>Zostaną zarządzanych tożsamości odtworzone automatycznie Jeśli przenieść subskrypcję do innego katalogu?

Nie. Jeśli przeniesiesz subskrypcję do innego katalogu, trzeba będzie ponownie je utworzyć ręcznie i ponownie przyznawanie przypisania ról RBAC platformy Azure.
    - Dla zarządzanych tożsamości przypisanych przez system: Wyłącz i ponownie włączyć.
    - Dla zarządzanych tożsamości przypisanych przez użytkownika: Usuń i ponownie utwórz ponownie podłączyć do wymaganych zasobów (np. maszyn wirtualnych)

### <a name="can-i-use-a-managed-identity-to-access-a-resource-in-a-different-directorytenant"></a>Dostęp do zasobów w innej katalogu/dzierżawy mogą używać tożsamości zarządzanej?

Nie. Zarządzanych tożsamości nie obsługują obecnie katalogu dla wielu scenariuszy. 

### <a name="what-are-the-supported-linux-distributions"></a>Co to są obsługiwane dystrybucje systemu Linux?

Wszystkie dystrybucje systemu Linux obsługiwane przez IaaS platformy Azure może służyć z zarządzanych tożsamości dla zasobów platformy Azure za pośrednictwem punktu końcowego IMDS. 

Zarządzanych tożsamości dla zasobów platformy Azure rozszerzenia maszyny Wirtualnej (zaplanowane do wycofania z użycia w styczniu 2019) obsługuje tylko poniższe dystrybucje systemu Linux:
- Stabilny systemu CoreOS
- CentOS 7.1
- Red Hat 7.2
- Ubuntu 15.04
- Ubuntu 16.04

Inne dystrybucje systemu Linux nie są obecnie obsługiwane, a rozszerzenie może zakończyć się niepowodzeniem w nieobsługiwanych dystrybucjach.

To rozszerzenie działa na 6,9 CentOS. Jednak ze względu na brak obsługi systemu w 6,9, rozszerzenie nie automatycznie ponownego uruchomienia jeśli wystąpiła awaria lub zatrzymana. Ponownego uruchomienia po ponownym uruchomieniu maszyny Wirtualnej. Aby ręcznie uruchomić ponownie rozszerzenie, zobacz [jak jest ponownym zarządzanych tożsamości dla rozszerzenia zasobów platformy Azure?](#how-do-you-restart-the-managed-identities-for-Azure-resources-extension)

### <a name="how-do-you-restart-the-managed-identities-for-azure-resources-extension"></a>Jak ponownym zarządzanych tożsamości dla rozszerzenia zasobów platformy Azure?
W systemie Windows i niektóre wersje systemu Linux Jeśli rozszerzenie zostanie zatrzymana, następujące polecenie cmdlet może służyć do ręcznie uruchomić ponownie:

```powershell
Set-AzVMExtension -Name <extension name>  -Type <extension Type>  -Location <location> -Publisher Microsoft.ManagedIdentity -VMName <vm name> -ResourceGroupName <resource group name> -ForceRerun <Any string different from any last value used>
```

Gdzie: 
- Rozszerzenie nazwy i typu dla Windows to: ManagedIdentityExtensionForWindows
- Rozszerzenie nazwy i typu dla systemu Linux jest: ManagedIdentityExtensionForLinux

## <a name="known-issues"></a>Znane problemy

### <a name="automation-script-fails-when-attempting-schema-export-for-managed-identities-for-azure-resources-extension"></a>"Skrypt automatyzacji" kończy się niepowodzeniem podczas próby eksport schematu dla zarządzanych tożsamości dla rozszerzenia zasobów platformy Azure

Po włączeniu zarządzanych tożsamości dla zasobów platformy Azure na maszynie Wirtualnej następujący błąd jest wyświetlany, gdy próba użycia funkcji "Skrypt automatyzacji" dla maszyny Wirtualnej lub jej grupy zasobów:

![Błąd eksportowania zarządzanych tożsamości dla zasobów platformy Azure, skrypt automatyzacji](./media/msi-known-issues/automation-script-export-error.png)

Zarządzanych tożsamości dla zasobów platformy Azure, które rozszerzenia maszyny Wirtualnej (zaplanowane do wycofania z użycia w styczniu 2019) jest obecnie nie obsługuje możliwość eksportowania jego schematu do szablonu grupy zasobów. W rezultacie w wygenerowany szablon nie są wyświetlane parametry konfiguracji, aby umożliwić zarządzanych tożsamości dla zasobów platformy Azure w zasobie. Poniższe sekcje mogą być dodawane ręcznie, wykonując na potrzeby przykładów w [Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na Maszynie wirtualnej platformy Azure przy użyciu szablonów](qs-configure-template-windows-vm.md).

Gdy funkcja eksportu schematu stają się dostępne dla zarządzanych tożsamości dla rozszerzenia maszyny Wirtualnej zasoby platformy Azure (planowana do wycofania z użycia w styczniu 2019), będzie ono wyświetlane w [eksportowanie grupy zasobów zawierające rozszerzeń maszyn wirtualnych](../../virtual-machines/extensions/export-templates.md#supported-virtual-machine-extensions).

### <a name="configuration-blade-does-not-appear-in-the-azure-portal"></a>Blok konfiguracji nie są wyświetlane w witrynie Azure portal

Jeśli nie ma bloku konfiguracji maszyny Wirtualnej na maszynie Wirtualnej, następnie zarządzanych tożsamości dla zasobów platformy Azure nie zostało włączone w portalu w Twoim regionie jeszcze.  Sprawdź ponownie później.  Można również włączyć zarządzanych tożsamości dla zasobów platformy Azure dla maszyny Wirtualnej przy użyciu [PowerShell](qs-configure-powershell-windows-vm.md) lub [wiersza polecenia platformy Azure](qs-configure-cli-windows-vm.md).

### <a name="cannot-assign-access-to-virtual-machines-in-the-access-control-iam-blade"></a>Nie można przypisać dostępu do maszyn wirtualnych w bloku kontrola (IAM) dostępu

Jeśli **maszyny wirtualnej** nie jest wyświetlany w witrynie Azure portal jako wyborem dla **Przypisz dostęp do** w **kontrola dostępu (IAM)** > **Dodaj rolę Przypisanie**, a następnie zarządzanych tożsamości dla zasobów platformy Azure nie został włączony w portalu w Twoim regionie jeszcze. Sprawdź ponownie później.  Nadal można wybrać tożsamości dla maszyny Wirtualnej na potrzeby przypisania roli przez wyszukiwanie zarządzanych tożsamości dla zasobów platformy Azure nazwy głównej usługi.  Wprowadź nazwę maszyny Wirtualnej w **wybierz** pola i nazwę główną usługi, zostanie wyświetlony w wynikach wyszukiwania.

### <a name="vm-fails-to-start-after-being-moved-from-resource-group-or-subscription"></a>Maszyna wirtualna nie została uruchomiona po jest przenoszony z grupy zasobów lub subskrypcji

Jeśli przenosisz Maszynę wirtualną w stanie uruchomienia, nadal działa podczas przenoszenia. Jednak po przeniesieniu, jeśli maszyna wirtualna zostanie zatrzymana i uruchomiona ponownie, zakończy się niepowodzeniem do uruchomienia. Ten problem występuje, ponieważ maszyna wirtualna nie aktualizuje odwołanie do zarządzanych tożsamości dla tożsamości zasobów platformy Azure i wskaż w starej grupy zasobów w dalszym ciągu.

**Obejście problemu** 
 
Wyzwalanie aktualizacji na maszynie Wirtualnej, dzięki czemu go uzyskać prawidłowe wartości dla zarządzanych tożsamości dla zasobów platformy Azure. Możesz tworzyć zmiany właściwości maszyny Wirtualnej, można zaktualizować odwołania do zarządzanych tożsamości dla tożsamości zasobów platformy Azure. Na przykład można ustawić nową wartość tagu na maszynie Wirtualnej za pomocą następującego polecenia:

```azurecli-interactive
 az  vm update -n <VM Name> -g <Resource Group> --set tags.fixVM=1
```
 
To polecenie ustawia nowy tag "fixVM" o wartości 1 na maszynie Wirtualnej. 
 
Przez ustawienie tej właściwości, maszyna wirtualna zostaje zaktualizowana o poprawne zarządzanych tożsamości dla identyfikatora URI zasobu zasobów platformy Azure, a następnie powinno być możliwe do uruchomienia maszyny Wirtualnej. 
 
Gdy maszyna wirtualna jest uruchomiona, można usunąć tagu za pomocą następującego polecenia:

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --remove tags.fixVM
```

### <a name="vm-extension-provisioning-fails"></a>Rozszerzenie maszyny Wirtualnej, inicjowanie obsługi administracyjnej kończy się niepowodzeniem

Inicjowanie obsługi rozszerzenia maszyny Wirtualnej może zakończyć się niepowodzeniem z powodu błędów wyszukiwania DNS. Uruchom ponownie maszynę Wirtualną i spróbuj ponownie.
 
> [!NOTE]
> Rozszerzenie maszyny Wirtualnej jest planowana do wycofania z użycia stycznia 2019 r. Zaleca się, że przeniesiesz przy użyciu punktu końcowego IMDS.

### <a name="transferring-a-subscription-between-azure-ad-directories"></a>Transferowania subskrypcji między katalogami usługi Azure AD

Zarządzanych tożsamości nie zostać zaktualizowana, gdy subskrypcja jest przeniesiony/przeniesione do innego katalogu. Co w efekcie wszelkie istnieje przypisana przez system lub zarządzanych tożsamości przypisanych przez użytkownika zostaną przerwane. 

Jako obejście po przeniesieniu subskrypcji można wyłączyć przypisany systemowo zarządzanych tożsamości i ponowne ich włączenie. Podobnie możesz usunąć i ponownie utworzyć wszelkie przypisane do użytkowników zarządzanych tożsamości. 

## <a name="known-issues-with-user-assigned-managed-identities"></a>Znane problemy związane z zarządzanych tożsamości przypisanych przez użytkownika

- Tworzenie tożsamości zarządzanej przypisanych przez użytkownika przy użyciu znaków specjalnych (np. podkreślenie) w nazwie, nie jest obsługiwane.
- Nazwy tożsamości przypisanych przez użytkownika są ograniczone do 24 znaków. Jeśli nazwa jest dłuższa niż 24 znaki, tożsamość zakończy się niepowodzeniem, ma być przypisane do zasobów (np. maszyna wirtualna.)
- W przypadku korzystania z tożsamości zarządzanej rozszerzenia maszyny wirtualnej (zaplanowane do wycofania z użycia w styczniu 2019) obsługiwany limit jest 32 przypisanych do użytkowników zarządzanych tożsamości. Bez rozszerzenia tożsamości zarządzanej maszyny wirtualnej i obsługiwany limit to 512.  
- Przenoszenie tożsamości zarządzanej użytkownik przypisany do innej grupy zasobów spowoduje, że tożsamości można przerwać. W rezultacie nie będzie do żądania tokenów dla tej tożsamości. 
- Przeniesienie subskrypcji do innego katalogu spowoduje przerwanie wszelkich istniejących tożsamości zarządzanej przypisanych przez użytkownika. 
