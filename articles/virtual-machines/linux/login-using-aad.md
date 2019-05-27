---
title: Zaloguj się do maszyny Wirtualnej systemu Linux przy użyciu poświadczeń usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: W tym Porada dowiesz się, jak utworzyć i skonfigurować Maszynę wirtualną systemu Linux do użycia uwierzytelnianie usługi Azure Active Directory dla danych logowania użytkownika
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/17/2018
ms.author: cynthn
ms.openlocfilehash: d1db228f4c73cc00cd32ca6ae5b86056db68f05b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "66155957"
---
# <a name="log-in-to-a-linux-virtual-machine-in-azure-using-azure-active-directory-authentication-preview"></a>Zaloguj się do maszyny wirtualnej z systemem Linux na platformie Azure przy użyciu uwierzytelniania usługi Azure Active Directory (wersja zapoznawcza)

Aby zwiększyć bezpieczeństwo maszyn wirtualnych systemu Linux (VM) na platformie Azure, możesz zintegrować przy użyciu uwierzytelniania usługi Azure Active Directory (AD). Gdy używasz uwierzytelniania usługi Azure AD dla maszyn wirtualnych z systemem Linux możesz centralnie kontrolować i egzekwowanie zasad, które blokują lub zezwalają na dostęp do maszyn wirtualnych. W tym artykule pokazano, jak utworzyć i skonfigurować Maszynę wirtualną systemu Linux do użycia uwierzytelnianie usługi Azure AD.

> [!NOTE]
> Ta funkcja jest dostępna w wersji zapoznawczej i nie jest zalecane do użytku z maszyn wirtualnych w środowisku produkcyjnym lub obciążeń. Użyj tej funkcji na testowej maszyny wirtualnej, który zamierzasz odrzucić po zakończeniu testowania.

Istnieje wiele korzyści, zaloguj się do maszyn wirtualnych systemu Linux na platformie Azure przy użyciu uwierzytelniania usługi Azure AD w tym:

- **Ulepszone zabezpieczenia:**
  - Zaloguj się do maszyn wirtualnych systemu Linux platformy Azure, można użyć poświadczeń firmowych usługi AD. Nie ma potrzeby tworzenia konta administratora lokalnego i zarządzania nimi okres istnienia poświadczeń.
  - Dzięki zmniejszeniu swoje poleganie na lokalne konta administratorów, nie trzeba się martwić o utratę/kradzieży poświadczeń, użytkownicy konfigurowania poświadczeń słabe itp.
  - Złożoność hasła i zasady okres istnienia hasła skonfigurowane dla katalogu usługi Azure AD pomóc bezpieczne maszyn wirtualnych systemu Linux oraz.
  - Aby dodatkowo zabezpieczyć logowanie do maszyn wirtualnych platformy Azure można skonfigurować uwierzytelnianie wieloskładnikowe.
  - Możliwość zalogowania się do maszyn wirtualnych systemu Linux w usłudze Azure Active Directory działa również w przypadku klientów używających [usług federacyjnych](../../active-directory/hybrid/how-to-connect-fed-whatis.md).

- **Bezproblemową współpracę:** Za pomocą opartej na rolach kontrola dostępu (RBAC), można określić, kto może logować do danej maszyny Wirtualnej jako zwykli użytkownicy lub z uprawnieniami administratora. Gdy użytkownicy dołączyć lub opuścić zespół, można zaktualizować zasad RBAC dla maszyny Wirtualnej udzielić dostępu, zgodnie z potrzebami. To środowisko jest znacznie prostsze niż w przypadku czyszczenie maszyn wirtualnych, aby usunąć niepotrzebne kluczy publicznych SSH. Gdy pracownik odejdzie z organizacji ich konta użytkownika zostało wyłączone lub usunięte z usługi Azure AD, nie będzie mieć dostęp do zasobów.

## <a name="supported-azure-regions-and-linux-distributions"></a>Obsługiwane regiony platformy Azure i dystrybucje systemu Linux

Poniższe dystrybucje systemu Linux są obecnie obsługiwane w okresie zapoznawczym tę funkcję:

| Dystrybucja | Wersja |
| --- | --- |
| CentOS | CentOS 6, CentOS 7 |
| Debian | Debian 9 |
| openSUSE | openSUSE Leap 42.3 |
| RedHat Enterprise Linux | RHEL 6, RHEL 7 | 
| SUSE Linux Enterprise Server | SLES 12 |
| Ubuntu Server | Ubuntu 14.04 LTS, Ubuntu Server 16.04 i Ubuntu Server 18.04 |


Poniższych regionów platformy Azure są obecnie obsługiwane w okresie zapoznawczym tę funkcję:

- Globalnych regionów platformy Azure

>[!IMPORTANT]
> Aby użyć tej funkcji w wersji zapoznawczej, wdrażać tylko w obsługiwanych dystrybucji systemu Linux w obsługiwanym regionie platformy Azure. Funkcja nie jest obsługiwana w Azure dla instytucji rządowych lub w chmurach suwerennych.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować i korzystać z interfejsu wiersza polecenia lokalnie, ten samouczek wymaga, czy korzystasz z wiersza polecenia platformy Azure w wersji 2.0.31 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-linux-virtual-machine"></a>Tworzenie maszyny wirtualnej z systemem Linux

Utwórz grupę zasobów za pomocą [Tworzenie grupy az](/cli/azure/group#az-group-create), następnie utwórz Maszynę wirtualną za pomocą [tworzenie az vm](/cli/azure/vm#az-vm-create) przy użyciu obsługiwanych Dystrybucja w obsługiwanym regionie. Poniższy przykład wdraża maszynę Wirtualną o nazwie *myVM* , który używa *Ubuntu 16.04 LTS* w grupie zasobów o nazwie *myResourceGroup* w *southcentralus*  regionu. W poniższych przykładach można podać własną grupę zasobów i nazwy maszyn wirtualnych, zgodnie z potrzebami.

```azurecli-interactive
az group create --name myResourceGroup --location southcentralus

az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Utworzenie maszyny wirtualnej i zasobów pomocniczych potrwa kilka minut.

## <a name="install-the-azure-ad-login-vm-extension"></a>Instalowanie rozszerzenia maszyny Wirtualnej logowania usługi Azure AD

Aby zalogować się do maszyny Wirtualnej systemu Linux przy użyciu poświadczeń usługi Azure AD, należy zainstalować logowania usługi Azure Active Directory rozszerzenia maszyny Wirtualnej. Rozszerzenia maszyn wirtualnych są małych aplikacji, które zapewniają po wdrożeniu konfiguracji oraz zadania automatyzacji na maszynach wirtualnych platformy Azure. Użyj [zestaw rozszerzeń maszyn wirtualnych az](/cli/azure/vm/extension#az-vm-extension-set) zainstalował *AADLoginForLinux* rozszerzenia na maszynie Wirtualnej o nazwie *myVM* w *myResourceGroup* zasobów Grupa:

```azurecli-interactive
az vm extension set \
    --publisher Microsoft.Azure.ActiveDirectory.LinuxSSH \
    --name AADLoginForLinux \
    --resource-group myResourceGroup \
    --vm-name myVM
```

*ProvisioningState* z *Powodzenie* jest wyświetlany, gdy rozszerzenie jest zainstalowane na maszynie Wirtualnej.

## <a name="configure-role-assignments-for-the-vm"></a>Konfigurowanie przypisań ról maszyny wirtualnej

Usługa Azure policy kontroli dostępu opartej na rolach (RBAC) określa, kto może zalogować się do maszyny Wirtualnej. Do autoryzacji logowania na maszynie Wirtualnej używane są dwie role RBAC:

- **Logowanie administratora maszyny wirtualnej**: Użytkownicy z tą rolą przypisane zalogować się do maszyny wirtualnej platformy Azure za pomocą administratora Windows lub Linux głównego użytkownikowi uprawnienia.
- **Logowanie użytkownika maszyny wirtualnej**: Użytkownicy z tą rolą przypisane zalogować się do maszyny wirtualnej platformy Azure z uprawnieniami zwykłego użytkownika.

> [!NOTE]
> Aby umożliwić użytkownikowi logowanie się do maszyny Wirtualnej za pomocą protokołu SSH, należy przypisać jedną *Logowanie administratora maszyny wirtualnej* lub *logowanie użytkownika maszyny wirtualnej* roli. Użytkownikiem platformy Azure z *właściciela* lub *Współautor* ról przypisanych do maszyny Wirtualnej automatycznie nie mają uprawnień do logowania się do maszyny Wirtualnej za pomocą protokołu SSH.

W poniższym przykładzie użyto [utworzenia przypisania roli az](/cli/azure/role/assignment#az-role-assignment-create) można przypisać *Logowanie administratora maszyny wirtualnej* roli maszyny Wirtualnej dla bieżącego użytkownika platformy Azure. Nazwa aktywnego konta platformy Azure są uzyskiwane z [Pokaż konta az](/cli/azure/account#az-account-show)i *zakres* ustawiono maszyny Wirtualnej utworzonej w poprzednim kroku przy użyciu [az vm show](/cli/azure/vm#az-vm-show). Na poziomie grupy lub subskrypcji zasobów można także przypisać zakres i stosować normalny RBAC dziedziczenie uprawnień. Aby uzyskać więcej informacji, zobacz [kontroli dostępu opartej na rolach](../../role-based-access-control/overview.md)

```azurecli-interactive
username=$(az account show --query user.name --output tsv)
vm=$(az vm show --resource-group myResourceGroup --name myVM --query id -o tsv)

az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee $username \
    --scope $vm
```

> [!NOTE]
> Jeśli Twoja domena usługi AAD i domena nazwa użytkownika logowania nie są zgodne, należy określić identyfikator obiektu konta użytkownika z *— assignee-object-id*, nie tylko nazwy użytkownika dla *— assignee*. Możesz uzyskać identyfikator obiektu dla tego konta użytkownika, za pomocą [listy użytkowników usługi ad az](/cli/azure/ad/user#az-ad-user-list).

Aby uzyskać więcej informacji na temat zarządzania dostępem do zasobów subskrypcji platformy Azure przy użyciu kontroli RBAC, zobacz Korzystanie z [wiersza polecenia platformy Azure](../../role-based-access-control/role-assignments-cli.md), [witryny Azure portal](../../role-based-access-control/role-assignments-portal.md), lub [programu Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

Można również skonfigurować usługi Azure AD, aby wymagać uwierzytelniania wieloskładnikowego dla określonego użytkownika zalogować się do maszyny wirtualnej systemu Linux. Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy z usługą Azure Multi-Factor Authentication w chmurze](../../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md).

## <a name="log-in-to-the-linux-virtual-machine"></a>Zaloguj się do maszyny wirtualnej systemu Linux

Po pierwsze, Wyświetl publiczny adres IP swojej maszyny wirtualnej za pomocą [az vm show](/cli/azure/vm#az-vm-show):

```azurecli-interactive
az vm show --resource-group myResourceGroup --name myVM -d --query publicIps -o tsv
```

Zaloguj się do maszyny wirtualnej systemu Linux platformy Azure przy użyciu poświadczeń usługi Azure AD. `-l` Parametr umożliwia określenie własny adres konta usługi Azure AD. Adresy konta powinny być wprowadzane w same małe litery. Użyj publicznego adresu IP maszyny wirtualnej z poprzedniego polecenia:

```azurecli-interactive
ssh -l azureuser@contoso.onmicrosoft.com publicIps
```

Zostanie wyświetlony monit o Zaloguj się do usługi Azure AD przy użyciu kodu jednorazowego użytku w [ https://microsoft.com/devicelogin ](https://microsoft.com/devicelogin). Skopiuj i Wklej kod jednorazowego użytku urządzenia strony logowania, jak pokazano w poniższym przykładzie:

```bash
~$ ssh -l azureuser@contoso.onmicrosoft.com 13.65.237.247
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code FJS3K6X4D to authenticate. Press ENTER when ready.
```

Po wyświetleniu monitu wprowadź poświadczenia logowania do usługi Azure AD na stronie logowania. Poniższy komunikat jest wyświetlony w przeglądarce sieci web, po pomyślnym uwierzytelnieniu:

    You have signed in to the Microsoft Azure Linux Virtual Machine Sign-In application on your device.

Zamknij okno przeglądarki, wróć do protokołu SSH Monituj i naciśnij klawisz **Enter** klucza. Teraz zalogowaniu się do maszyny wirtualnej systemu Linux platformy Azure przy użyciu uprawnień roli przypisaniu, takich jak *użytkownika maszyny Wirtualnej* lub *Administrator maszyny Wirtualnej*. Jeśli Twoje konto użytkownika nie zostanie przypisana *Logowanie administratora maszyny wirtualnej* roli, można użyć `sudo` do uruchamiania poleceń, które wymagają uprawnień użytkownika root.

## <a name="sudo-and-aad-login"></a>Identyfikator logowania "sudo" i usługi AAD

Przy pierwszym uruchomieniu "sudo", będzie zostać poproszona o uwierzytelnienie po raz drugi. Jeśli nie chcesz uwierzytelnić się ponownie do uruchomienia "sudo", można edytować plik sudo `/etc/sudoers.d/aad_admins` i Zastąp ten wiersz:

```bash
%aad_admins ALL=(ALL) ALL
```
w tym:

```bash
%aad_admins ALL=(ALL) NOPASSWD:ALL
```


## <a name="troubleshoot-sign-in-issues"></a>Rozwiązywanie problemów z rejestracją

Niektóre typowe błędy przy próbie SSH przy użyciu poświadczeń usługi Azure AD nie obejmują żadnych przypisanych ról RBAC i powtórzyć monitami, aby zalogować. Następujące sekcje zawierają rozwiązania tych problemów.

### <a name="access-denied-rbac-role-not-assigned"></a>Odmowa dostępu: Nie jest przypisana rola RBAC

Jeśli zobaczysz następujący błąd w wierszu polecenia usługi SSH, sprawdź, czy skonfigurowano zasady RBAC dla maszyny Wirtualnej, która udziela użytkownikowi albo *Logowanie administratora maszyny wirtualnej* lub *logowanie użytkownika maszyny wirtualnej*roli:

```bash
login as: azureuser@contoso.onmicrosoft.com
Using keyboard-interactive authentication.
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code FJX327AXD to authenticate. Press ENTER when ready.
Using keyboard-interactive authentication.
Access denied:  to sign-in you be assigned a role with action 'Microsoft.Compute/virtualMachines/login/action', for example 'Virtual Machine User Login'
Access denied
```

### <a name="continued-ssh-sign-in-prompts"></a>Dalsze SSH monity o logowanie

Jeśli pomyślnym ukończeniu kroku uwierzytelniania w przeglądarce sieci web, można od razu monit może się ponownie zalogować się przy użyciu kodu od nowa. Ten błąd jest zazwyczaj spowodowany przez niezgodność między podana w wierszu SSH nazwa logowania i konta którego zalogowano się do usługi Azure AD za pomocą. Aby rozwiązać ten problem:

- Sprawdź, czy określona w wierszu SSH nazwa logowania jest poprawna. Błąd pisowni w nazwie logowania może spowodować niezgodność między podana w wierszu SSH nazwa logowania i konta którego zalogowano się do usługi Azure AD za pomocą. Na przykład wpisać *azuresuer\@contoso.onmicrosoft.com* zamiast *azureuser\@contoso.onmicrosoft.com*.
- Jeśli masz wiele kont użytkowników, upewnij się, że podczas logowania się do usługi Azure AD nie podasz innego konta użytkownika w oknie przeglądarki.
- Linux jest rozróżniana wielkość liter systemu operacyjnego. Istnieje różnica pomiędzy "Azureuser@contoso.onmicrosoft.com"i"azureuser@contoso.onmicrosoft.com", co może spowodować niezgodność. Upewnij się, określ nazwę UPN z poprawną uwzględnianie wielkości liter w wierszu SSH.

## <a name="preview-feedback"></a>Opinii dotyczących wersji zapoznawczej

Podziel się swoją opinią na temat tej wersji zapoznawczej funkcji lub raport problemów z korzystania z niego na [forum opinii w usłudze Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat usługi Azure Active Directory, zobacz [co to jest Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md)
