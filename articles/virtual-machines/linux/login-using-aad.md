---
title: Zaloguj się do maszyny Wirtualnej systemu Linux przy użyciu poświadczeń usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: W tym Porada można dowiedzieć się, jak utworzyć i skonfigurować maszyny Wirtualnej systemu Linux, aby używać uwierzytelniania usługi Azure Active Directory dla danych logowania użytkownika
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/08/2018
ms.author: iainfou
ms.openlocfilehash: 652f9867b7423ce4307dba1c77e8f38fcd596c67
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="log-in-to-a-linux-virtual-machine-in-azure-using-azure-active-directory-authentication-preview"></a>Zaloguj się do maszyny wirtualnej systemu Linux na platformie Azure przy użyciu uwierzytelniania usługi Azure Active Directory (wersja zapoznawcza)

W celu zwiększenia bezpieczeństwa maszyn wirtualnych systemu Linux (VM) na platformie Azure, można je zintegrować z uwierzytelnianiem usługi Azure Active Directory (AD). Gdy używasz uwierzytelniania usługi Azure AD dla maszyn wirtualnych systemu Linux można centralnie kontrolować i egzekwowanie zasad, które zezwolić lub odmówić dostępu do maszyn wirtualnych. W tym artykule przedstawiono sposób tworzenia i konfigurowania maszyny Wirtualnej systemu Linux, aby używać uwierzytelniania usługi Azure AD.

> [!NOTE]
> Ta funkcja jest w wersji zapoznawczej i nie jest zalecane w przypadku maszyn wirtualnych w środowisku produkcyjnym lub obciążeń. Użyj tej funkcji na testowej maszyny wirtualnej, który będzie odrzucenie po zakończeniu testowania.

Istnieje wiele zalet przy użyciu uwierzytelniania usługi Azure AD do logowania do maszyn wirtualnych systemu Linux na platformie Azure, w tym:

- **Ulepszone zabezpieczenia:**
  - Swoje poświadczenia firmowe, AD służy do logowania do maszyn wirtualnych systemu Linux platformy Azure. Nie istnieje potrzeba Tworzenie konta administratora lokalnego i zarządzanie nimi istnienia poświadczeń.
  - Dzięki zmniejszeniu sieci zależy od konta administratora lokalnego, nie trzeba martwić się o utracie/kradzieżą poświadczeń, użytkownicy konfigurowania poświadczeń słabe itp.
  - Złożoność hasła, a hasło okres istnienia zasady skonfigurowane dla katalogu usługi Azure AD pomocy bezpiecznego maszyn wirtualnych systemu Linux również.
  - Do dalszego bezpiecznego logowania do maszyn wirtualnych platformy Azure można skonfigurować usługę Multi-Factor authentication.

- **Bezproblemowe współpracy:** With Role-Based kontroli dostępu (RBAC), można określić kto może Zaloguj się do danej maszyny Wirtualnej przez użytkowników lub z uprawnieniami administratora. Gdy użytkownicy join lub pozostaw zespołu, należy zaktualizować zasady RBAC dla maszyny Wirtualnej, aby udzielić dostępu, zależnie od potrzeb. To środowisko jest znacznie łatwiejsze niż w przypadku konieczności wyczyść maszyn wirtualnych, aby usunąć niepotrzebne klucze publiczne SSH. Gdy pracownicy opuszczają organizację, a ich konta użytkownika zostało wyłączone lub usunięte z usługi Azure AD, nie będzie mieć dostęp do zasobów.

### <a name="supported-azure-regions-and-linux-distributions"></a>Obsługiwane regiony platformy Azure i dystrybucje systemu Linux

Następujące dystrybucje systemu Linux są obecnie obsługiwane w wersji zapoznawczej tej funkcji:

| Dystrybucja | Wersja |
| --- | --- |
| CentOS | CentOS 6,9 i CentOS 7.4 |
| RedHat Enterprise Linux | RHEL 7 | 
| Ubuntu Server | Ubuntu 14.04 LTS, Ubuntu Server 16.04 i Ubuntu Server 17.10 |

Obecnie obsługiwane są następujące regiony platformy Azure w wersji zapoznawczej tej funkcji:

- Wszystkie publiczne regiony platformy Azure

>[!IMPORTANT]
> Aby użyć tej funkcji w wersji zapoznawczej, wdrażać tylko w obsługiwanych distro systemu Linux i w obsługiwanym regionie Azure. Funkcja nie jest obsługiwana w Azure dla instytucji rządowych lub suwerennych chmury.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli wybierzesz do zainstalowania i używania interfejsu wiersza polecenia lokalnie, w tym samouczku wymaga używasz interfejsu wiersza polecenia Azure w wersji 2.0.31 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0]( /cli/azure/install-azure-cli).

## <a name="create-a-linux-virtual-machine"></a>Utwórz maszynę wirtualną z systemem Linux

Utwórz nową grupę zasobów o [Tworzenie grupy az](/cli/azure/group#az-group-create), następnie utwórz maszynę Wirtualną za pomocą [tworzenia maszyny wirtualnej az](/cli/azure/vm#az-vm-create) za pomocą obsługiwanych distro w obsługiwanym regionie. Poniższy przykład wdraża maszynę Wirtualną o nazwie *myVM* używającą *Ubuntu 16.04 LTS* w grupie zasobów o nazwie *myResourceGroup* w *southcentralus*  regionu. W poniższych przykładach musisz podać własne grupy zasobów i nazwy maszyny Wirtualnej, zgodnie z potrzebami.

```azurecli-interactive
az group create --name myResourceGroup --location southcentralus

az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Trwa kilka minut na utworzenie maszyny Wirtualnej i dodatkowe zasoby.

## <a name="install-the-azure-ad-login-vm-extension"></a>Zainstaluj usługi Azure AD logowania rozszerzenia maszyny Wirtualnej

Aby zalogować się do maszyny Wirtualnej systemu Linux przy użyciu poświadczeń usługi Azure AD, należy zainstalować dziennika usługi Azure Active Directory w rozszerzenia maszyny Wirtualnej. Rozszerzenia maszyny Wirtualnej są małe aplikacji, które mają po wdrożeniu i automatyzację zadań na maszynach wirtualnych Azure. Użyj [zestaw rozszerzenia maszyny wirtualnej az](/cli/azure/vm/extension#az-vm-extension-set) do zainstalowania *AADLoginForLinux* rozszerzenia na Maszynie wirtualnej o nazwie *myVM* w *myResourceGroup* zasobów Grupa:

```azurecli-interactive
az vm extension set \
    --publisher Microsoft.Azure.ActiveDirectory.LinuxSSH \
    --name AADLoginForLinux \
    --resource-group myResourceGroup \
    --vm-name myVM
```

*ProvisioningState* z *zakończyło się pomyślnie* jest wyświetlany po zainstalowaniu rozszerzenia na maszynie Wirtualnej.

## <a name="configure-role-assignments-for-the-vm"></a>Konfigurowanie przypisania roli dla maszyny Wirtualnej

Zasady kontroli dostępu opartej na rolach (RBAC) Azure Określa, kto może zalogować się do maszyny Wirtualnej. Do autoryzacji logowania maszyny Wirtualnej, używane są dwie role RBAC:

- **Identyfikator logowania administratora maszyny wirtualnej**: użytkownicy z tą rolą przypisane zalogować się do maszyny wirtualnej platformy Azure z uprawnieniami użytkownika głównego administratora systemu Windows lub Linux.
- **Dane logowania użytkownika maszyny wirtualnej**: użytkownicy z tą rolą przypisane zalogować się do maszyny wirtualnej platformy Azure z uprawnienia zwykłych użytkowników.

> [!NOTE]
> Aby zezwolić użytkownikowi na logowanie się do maszyny Wirtualnej za pomocą protokołu SSH, należy przypisać jedną *logowania administratora programu Virtual Machine* lub *dane logowania użytkownika maszyny wirtualnej* roli. Użytkownik Azure z *właściciela* lub *współautora* ról przypisanych do maszyny Wirtualnej automatycznie nie mieć uprawnień do logowania do maszyny Wirtualnej za pomocą protokołu SSH.

W poniższym przykładzie użyto [utworzenia przypisania roli az](/cli/azure/role/assignment#az-role-assignment-create) można przypisać *logowania administratora programu Virtual Machine* roli maszyny wirtualnej dla bieżącego użytkownika platformy Azure. Nazwa użytkownika konta usługi Azure active są uzyskiwane z [Pokaż konto az](/cli/azure/account#az-account-show)i *zakres* jest ustawiona dla maszyny Wirtualnej utworzone w poprzednim kroku z [az maszyny wirtualnej pokazu](/cli/azure/vm#az-vm-show). Na poziomie grupy lub subskrypcji zasobów można także przypisać zakres, a następnie Zastosuj normalnymi uprawnieniami dziedziczenia RBAC. Aby uzyskać więcej informacji, zobacz [kontroli dostępu opartej na rolach](../../azure-resource-manager/resource-group-overview.md#access-control)

```azurecli-interactive
username=$(az account show --query user.name --output tsv)
vm=$(az vm show --resource-group myResourceGroup --name myVM --query id -o tsv)

az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee $username \
    --scope $vm
```

Aby uzyskać więcej informacji na temat sposobu użycie funkcji RBAC, aby zarządzać dostępem do zasobów subskrypcji platformy Azure, zobacz przy użyciu [Azure CLI 2.0](../../role-based-access-control/role-assignments-cli.md), [portalu Azure](../../role-based-access-control/role-assignments-portal.md), lub [programu Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

Można również skonfigurować usługi Azure AD, aby wymusić uwierzytelnianie wieloskładnikowe dla określonego użytkownika do logowania do maszyny wirtualnej systemu Linux. Aby uzyskać więcej informacji, zobacz [wprowadzenie do usługi Azure Multi-Factor Authentication w chmurze](../../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md).

## <a name="log-in-to-the-linux-virtual-machine"></a>Zaloguj się do maszyny wirtualnej systemu Linux

Najpierw należy wyświetlić publicznego adresu IP maszyny Wirtualnej z [az maszyny wirtualnej pokazu](/cli/azure/vm#az-vm-show):

```azurecli-interactive
az vm show --resource-group myResourceGroup --name myVM -d --query publicIps -o tsv
```

Zaloguj się do maszyny wirtualnej systemu Linux platformy Azure przy użyciu poświadczeń usługi Azure AD. `-l` Parametr umożliwia określenie własnych adres konta usługi Azure AD. Określ publiczny adres IP maszyny Wirtualnej jako dane wyjściowe w poprzednim poleceniu:

```azurecli-interactive
ssh -l azureuser@contoso.onmicrosoft.com publicIps
```

Zostanie wyświetlony monit o Zaloguj się do usługi Azure AD przy użyciu kodu jednorazowego użytku na [ https://microsoft.com/devicelogin ](https://microsoft.com/devicelogin). Skopiuj i Wklej kod jednorazowego użytku do strony logowania urządzenia, jak pokazano w poniższym przykładzie:

```bash
~$ ssh -l azureuser@contoso.onmicrosoft.com 13.65.237.247
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code FJS3K6X4D to authenticate. Press ENTER when ready.
```

Po wyświetleniu monitu wprowadź poświadczenia logowania usługi Azure AD na stronę logowania. Zostanie wyświetlony następujący komunikat w przeglądarce sieci web, po pomyślnym uwierzytelnieniu:

    You have signed in to the Microsoft Azure Linux Virtual Machine Sign-In application on your device.

Zamknij okno przeglądarki, wróć do SSH Monituj i naciśnij klawisz **Enter** klucza. Obecnie zalogowano do maszyny wirtualnej systemu Linux platformy Azure przy użyciu uprawnień roli przypisany, takich jak *użytkownika maszyny Wirtualnej* lub *administratora maszyny Wirtualnej*. Jeśli Twoje konto użytkownika zostanie przypisany *logowania administratora programu Virtual Machine* roli, można użyć `sudo` na potrzeby uruchamiania poleceń, które wymagają uprawnień głównego.

## <a name="troubleshoot-sign-in-issues"></a>Rozwiązywanie problemów logowania

Niektóre typowe błędy przy próbie SSH przy użyciu poświadczeń usługi Azure AD obejmują ma przypisane do ról RBAC i powtarzany monitami, aby zarejestrować. Poniższe sekcje umożliwiają rozwiązać te problemy.

### <a name="access-denied-rbac-role-not-assigned"></a>Odmowa dostępu: nie jest przypisana rola RBAC

Jeśli zostanie wyświetlony następujący błąd w wierszu użytkownika SSH, sprawdź, czy masz [skonfigurowane zasady RBAC](#configure-rbac-policy-for-the-virtual-machine) dla maszyny Wirtualnej, która udziela użytkownikowi albo *logowania administratora programu Virtual Machine* lub *wirtualnej Komputer logowania użytkownika* roli:

```bash
login as: azureuser@contoso.onmicrosoft.com
Using keyboard-interactive authentication.
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code FJX327AXD to authenticate. Press ENTER when ready.
Using keyboard-interactive authentication.
Access denied:  to sign-in you be assigned a role with action 'Microsoft.Compute/virtualMachines/login/action', for example 'Virtual Machine User Login'
Access denied
```

### <a name="continued-ssh-sign-in-prompts"></a>Kontynuacja monity logowania SSH

Jeśli pomyślnie wykonać krok uwierzytelniania w przeglądarce sieci web, może być natychmiast monit o ponownie zaloguj się za pomocą nowego kodu. Niezgodność nazwy logowania, określona w wierszu polecenia SSH konta, na którym jest zalogowany do usługi Azure AD z zazwyczaj przyczyną tego błędu. Aby rozwiązać ten problem:

- Sprawdź poprawność nazwy logowania, określona w wierszu polecenia SSH. Literówka w nazwie logowania może spowodować niezgodność nazwy logowania, określona w wierszu polecenia programu SSH i konta, na którym jest zalogowany do usługi Azure AD z. Na przykład możesz wpisać *azuresuer@contoso.onmicrosoft.com* zamiast *azureuser@contoso.onmicrosoft.com*.
- Jeśli masz wiele kont użytkowników, upewnij się, że nie udostępniają innego konta użytkownika w oknie przeglądarki, po zalogowaniu się do usługi Azure AD.
- Linux jest rozróżniana wielkość liter systemu operacyjnego. Ma różnicy między "Azureuser@contoso.onmicrosoft.com"i"azureuser@contoso.onmicrosoft.com", co może spowodować niezgodność. Upewnij się, określ nazwę UPN z poprawną rozróżnianiem wielkości liter w wierszu polecenia SSH.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji o usłudze Azure Active Directory, zobacz [co to jest usługa Azure Active Directory](../../active-directory/active-directory-whatis.md) i [jak rozpocząć pracę z usługą Azure Active Directory](../../active-directory/get-started-azure-ad.md)
