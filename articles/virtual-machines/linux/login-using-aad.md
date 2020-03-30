---
title: Zaloguj się do maszyny Wirtualnej systemu Linux przy użyciu poświadczeń usługi Azure Active Directory
description: Dowiedz się, jak utworzyć i skonfigurować maszynę wirtualną z systemem Linux do logowania się przy użyciu uwierzytelniania usługi Azure Active Directory.
author: iainfoulds
ms.service: virtual-machines-linux
ms.topic: article
ms.workload: infrastructure
ms.date: 08/29/2019
ms.author: iainfou
ms.openlocfilehash: 2731693667d2129a72da72455c6bbdd74c277697
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366484"
---
# <a name="preview-log-in-to-a-linux-virtual-machine-in-azure-using-azure-active-directory-authentication"></a>Wersja zapoznawcza: logowanie się do maszyny wirtualnej systemu Linux na platformie Azure przy użyciu uwierzytelniania usługi Azure Active Directory

Aby zwiększyć bezpieczeństwo maszyn wirtualnych systemu Linux (VM) na platformie Azure, można zintegrować z uwierzytelnianiem usługi Azure Active Directory (AD). Korzystając z uwierzytelniania usługi Azure AD dla maszyn wirtualnych z systemem Linux, można centralnie kontrolować i wymuszać zasady, które zezwalają lub odmawiają dostępu do maszyn wirtualnych. W tym artykule pokazano, jak utworzyć i skonfigurować maszynę wirtualną z systemem Linux do używania uwierzytelniania usługi Azure AD.


> [!IMPORTANT]
> Uwierzytelnianie usługi Azure Active Directory jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
> Użyj tej funkcji na testowej maszynie wirtualnej, która ma być odrzucona po przetestowaniu.
>


Istnieje wiele korzyści z używania uwierzytelniania usługi Azure AD do logowania się do maszyn wirtualnych z systemem Linux na platformie Azure, w tym:

- **Większe bezpieczeństwo:**
  - Za pomocą firmowych poświadczeń usługi AD można zalogować się do maszyn wirtualnych systemu Azure z systemem Linux. Nie ma potrzeby tworzenia kont administratora lokalnego i zarządzania okresem istnienia poświadczeń.
  - Zmniejszając zależność od kont administratora lokalnego, nie musisz się martwić o utratę poświadczeń / kradzieży, użytkowników konfigurujących słabe poświadczenia itp.
  - Złożoność haseł i zasady okresu istnienia hasła skonfigurowane dla katalogu usługi Azure AD pomagają również zabezpieczyć maszyny wirtualne z systemem Linux.
  - Aby dodatkowo zabezpieczyć logowanie do maszyn wirtualnych platformy Azure, można skonfigurować uwierzytelnianie wieloskładnikowe.
  - Możliwość logowania się do maszyn wirtualnych z systemem Linux za pomocą usługi Azure Active Directory działa również dla klientów korzystających z [usług federacyjnych.](../../active-directory/hybrid/how-to-connect-fed-whatis.md)

- **Bezproblemowa współpraca:** Za pomocą kontroli dostępu opartej na rolach (RBAC) można określić, kto może logować się do danej maszyny Wirtualnej jako zwykły użytkownik lub z uprawnieniami administratora. Gdy użytkownicy dołączyć lub opuścić zespół, można zaktualizować zasady RBAC dla maszyny Wirtualnej, aby udzielić dostępu w razie potrzeby. To środowisko jest znacznie prostsze niż konieczności szorowania maszyn wirtualnych, aby usunąć niepotrzebne klucze publiczne SSH. Gdy pracownicy opuszczają organizację, a ich konto użytkownika jest wyłączone lub usunięte z usługi Azure AD, nie mają już dostępu do zasobów.

## <a name="supported-azure-regions-and-linux-distributions"></a>Obsługiwane regiony platformy Azure i dystrybucje systemu Linux

Następujące dystrybucje Linuksa są obecnie obsługiwane podczas podglądu tej funkcji:

| Dystrybucja | Wersja |
| --- | --- |
| CentOS | CentOS 6, CentOS 7 |
| Debian | Debian 9 |
| openSUSE | openSUSE Leap 42.3 |
| RedHat Enterprise Linux | RHEL 6, RHEL 7 | 
| SUSE Linux Enterprise Server | SLES 12 |
| Ubuntu Server | Ubuntu 14.04 LTS, Ubuntu Server 16.04 i Ubuntu Server 18.04 |


Następujące regiony platformy Azure są obecnie obsługiwane podczas podglądu tej funkcji:

- Wszystkie globalne regiony platformy Azure

>[!IMPORTANT]
> Aby korzystać z tej funkcji w wersji zapoznawczej, należy wdrożyć tylko obsługiwaną dystrybucję systemu Linux i w obsługiwanym regionie platformy Azure. Ta funkcja nie jest obsługiwana w usłudze Azure Government ani w chmurach suwerennych.
>
> Nie jest obsługiwana do korzystania z tego rozszerzenia w klastrach usługi Azure Kubernetes Service (AKS). Aby uzyskać więcej informacji, zobacz [Zasady pomocy technicznej dla usługi AKS](../../aks/support-policies.md).


Jeśli zdecydujesz się zainstalować i używać interfejsu wiersza polecenia lokalnie, ten samouczek wymaga, aby uruchomić interfejsu wiersza polecenia platformy Azure w wersji 2.0.31 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

## <a name="network-requirements"></a>Wymagania dotyczące sieci

Aby włączyć uwierzytelnianie usługi Azure AD dla maszyn wirtualnych z systemem Linux na platformie Azure, musisz upewnić się, że konfiguracja sieci maszyn wirtualnych zezwala na dostęp wychodzący do następujących punktów końcowych za pośrednictwem portu TCP 443:

* https:\//login.microsoftonline.com
* https:\//login.windows.net
* https:\//device.login.microsoftonline.com
* https:\//pas.windows.net
* https:\//management.azure.com
* https:\//packages.microsoft.com

> [!NOTE]
> Obecnie nie można skonfigurować grup zabezpieczeń sieci platformy Azure dla maszyn wirtualnych włączonych za pomocą uwierzytelniania usługi Azure AD.

## <a name="create-a-linux-virtual-machine"></a>Tworzenie maszyny wirtualnej z systemem Linux

Utwórz grupę zasobów z [tworzeniem grupy az,](/cli/azure/group#az-group-create)a następnie utwórz maszynę wirtualną z [az vm create](/cli/azure/vm#az-vm-create) przy użyciu obsługiwanej dystrybucji i w obsługiwanym regionie. Poniższy przykład wdraża maszynę wirtualną o nazwie *myVM,* która używa *Ubuntu 16.04 LTS* do grupy zasobów o nazwie *myResourceGroup* w regionie *southcentralus.* W poniższych przykładach można podać własną grupę zasobów i nazwy maszyn wirtualnych w razie potrzeby.

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

> [!NOTE]
> Jeśli wdrożenie tego rozszerzenia na wcześniej utworzonej maszynie wirtualnej upewnij się, że komputer ma co najmniej 1 GB pamięci przydzielonej w przeciwnym razie rozszerzenie nie zostanie zainstalowane

Aby zalogować się do maszyny Wirtualnej systemu Linux przy użyciu poświadczeń usługi Azure AD, zainstaluj rozszerzenie logowania do logowania usługi Azure Active Directory. Rozszerzenia maszyn wirtualnych to małe aplikacje, które zapewniają konfigurację po wdrożeniu i zadania automatyzacji na maszynach wirtualnych platformy Azure. Użyj [zestawu rozszerzeń az vm,](/cli/azure/vm/extension#az-vm-extension-set) aby zainstalować rozszerzenie *AADLoginForLinux* na maszynie wirtualnej o nazwie *myVM* w grupie zasobów *myResourceGroup:*

```azurecli-interactive
az vm extension set \
    --publisher Microsoft.Azure.ActiveDirectory.LinuxSSH \
    --name AADLoginForLinux \
    --resource-group myResourceGroup \
    --vm-name myVM
```

*Inicjowanie obsługi administracyjnejState* *pomyślnie* jest wyświetlany po pomyślnym zainstalowaniu rozszerzenia na maszynie Wirtualnej. Maszyna wirtualna potrzebuje uruchomionego agenta maszyny Wirtualnej, aby zainstalować rozszerzenie. Aby uzyskać więcej informacji, zobacz [Omówienie agenta maszyn wirtualnych](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows).

## <a name="configure-role-assignments-for-the-vm"></a>Konfigurowanie przypisań ról dla maszyny Wirtualnej

Zasady kontroli dostępu opartej na rolach platformy Azure (RBAC) określają, kto może zalogować się do maszyny Wirtualnej. Dwie role RBAC są używane do autoryzowania logowania maszyn wirtualnych:

- **Logowanie administratora maszyny wirtualnej:** Użytkownicy z przypisaną tą rolą mogą logować się do maszyny wirtualnej platformy Azure za pomocą uprawnień administratora systemu Windows lub użytkownika głównego systemu Linux.
- **Login użytkownika maszyny wirtualnej:** użytkownicy z przypisaną tą rolą mogą logować się do maszyny wirtualnej platformy Azure z uprawnieniami zwykłych użytkowników.

> [!NOTE]
> Aby umożliwić użytkownikowi zalogowanie się do maszyny Wirtualnej za pomocą funkcji SSH, należy przypisać rolę *logowania administratora maszyny wirtualnej* lub *logowania użytkownika maszyny wirtualnej.* Użytkownik platformy Azure z *ról właściciela* lub *współautora* przypisane do maszyny Wirtualnej nie mają automatycznie uprawnień do logowania się do maszyny Wirtualnej za pomocą SSH.

W poniższym przykładzie użyto [az przypisanie roli utworzyć](/cli/azure/role/assignment#az-role-assignment-create) przypisać rolę *logowania administratora maszyny wirtualnej* do maszyny wirtualnej dla bieżącego użytkownika platformy Azure. Nazwa użytkownika aktywnego konta platformy Azure jest uzyskiwana za pomocą [programu AZ Account Show,](/cli/azure/account#az-account-show)a *zakres* jest ustawiony na maszynę wirtualną utworzoną w poprzednim kroku z [programem AZ VM Show](/cli/azure/vm#az-vm-show). Zakres można również przypisać na poziomie grupy zasobów lub subskrypcji i stosuje się normalne uprawnienia dziedziczenia RBAC. Aby uzyskać więcej informacji, zobacz [Formanty dostępu oparte na rolach](../../role-based-access-control/overview.md)

```azurecli-interactive
username=$(az account show --query user.name --output tsv)
vm=$(az vm show --resource-group myResourceGroup --name myVM --query id -o tsv)

az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee $username \
    --scope $vm
```

> [!NOTE]
> Jeśli domena AAD i domena nazwy użytkownika logowania nie są zgodne, należy określić identyfikator obiektu konta użytkownika za pomocą *identyfikatora --assignee-object-id,* a nie tylko nazwy użytkownika *dla --assignee*. Identyfikator obiektu dla konta użytkownika można uzyskać za pomocą [listy użytkowników reklam az](/cli/azure/ad/user#az-ad-user-list).

Aby uzyskać więcej informacji na temat używania funkcji RBAC do zarządzania dostępem do zasobów subskrypcji platformy Azure, zobacz korzystanie z [interfejsu wiersza polecenia platformy Azure,](../../role-based-access-control/role-assignments-cli.md) [portalu Azure](../../role-based-access-control/role-assignments-portal.md)lub programu [Azure PowerShell.](../../role-based-access-control/role-assignments-powershell.md)

Można również skonfigurować usługę Azure AD, aby wymagać uwierzytelniania wieloskładnikowego dla określonego użytkownika, aby zalogować się do maszyny wirtualnej systemu Linux. Aby uzyskać więcej informacji, zobacz [Wprowadzenie do uwierzytelniania wieloskładnikowego platformy Azure w chmurze](../../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md).

## <a name="log-in-to-the-linux-virtual-machine"></a>Zaloguj się do maszyny wirtualnej Systemu Linux

Najpierw wyświetl publiczny adres IP maszyny Wirtualnej z [az vm show:](/cli/azure/vm#az-vm-show)

```azurecli-interactive
az vm show --resource-group myResourceGroup --name myVM -d --query publicIps -o tsv
```

Zaloguj się do maszyny wirtualnej systemu Azure Linux przy użyciu poświadczeń usługi Azure AD. Parametr `-l` umożliwia określenie własnego adresu konta usługi Azure AD. Zastąp przykładowe konto własnym. Adresy kont należy wprowadzić we wszystkich małych literach. Zastąp przykładowy adres IP publicznym adresem IP maszyny Wirtualnej z poprzedniego polecenia.

```console
ssh -l azureuser@contoso.onmicrosoft.com 10.11.123.456
```

Zostanie wyświetlony monit o zalogowanie się do usługi Azure [https://microsoft.com/devicelogin](https://microsoft.com/devicelogin)AD przy użyciu kodu jednorazowego użycia w pliku . Skopiuj i wklej jednorazowy kod do strony logowania urządzenia.

Po wyświetleniu monitu wprowadź poświadczenia logowania usługi Azure AD na stronie logowania. 

Po pomyślnym uwierzytelnieniu w przeglądarce sieci Web zostanie wyświetlony następujący komunikat:`You have signed in to the Microsoft Azure Linux Virtual Machine Sign-In application on your device.`

Zamknij okno przeglądarki, wróć do monitu SSH i naciśnij klawisz **Enter.** 

Jesteś teraz zalogowany do maszyny wirtualnej systemu Azure Linux z uprawnieniami roli przypisanymi, takimi jak *użytkownik maszyny wirtualnej* lub *administrator maszyny wirtualnej.* Jeśli do konta użytkownika przypisano rolę *Logowania administratora maszyny wirtualnej,* można użyć `sudo` do uruchamiania poleceń, które wymagają uprawnień głównych.

## <a name="sudo-and-aad-login"></a>Logowanie do Sudo i AAD

Przy pierwszym uruchomieniu sudo zostaniesz poproszony o uwierzytelnienie po raz drugi. Jeśli nie chcesz ponownie uwierzytelniać się, aby uruchomić sudo, możesz `/etc/sudoers.d/aad_admins` edytować plik sudoers i zastąpić ten wiersz:

```bash
%aad_admins ALL=(ALL) ALL
```

Z tą linią:

```bash
%aad_admins ALL=(ALL) NOPASSWD:ALL
```


## <a name="troubleshoot-sign-in-issues"></a>Rozwiązywanie problemów z logowaniem

Niektóre typowe błędy podczas próby SSH z poświadczeniami usługi Azure AD obejmują brak przypisanych ról RBAC i powtarzające się monity o zalogowanie się. Aby rozwiązać te problemy, należy użyć poniższych sekcji.

### <a name="access-denied-rbac-role-not-assigned"></a>Odmowa dostępu: rola RBAC nie została przypisana

Jeśli w wierszu monitu SSH zostanie wyświetlony następujący błąd, sprawdź, czy skonfigurowano zasady RBAC dla maszyny wirtualnej, która przyznaje użytkownikowi rolę *Logowania administratora maszyny wirtualnej* lub *logowania użytkownika maszyny wirtualnej:*

```output
login as: azureuser@contoso.onmicrosoft.com
Using keyboard-interactive authentication.
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code FJX327AXD to authenticate. Press ENTER when ready.
Using keyboard-interactive authentication.
Access denied:  to sign-in you be assigned a role with action 'Microsoft.Compute/virtualMachines/login/action', for example 'Virtual Machine User Login'
Access denied
```

### <a name="continued-ssh-sign-in-prompts"></a>Ciąg dalszy monitów logowania SSH

Jeśli pomyślnie zakończysz krok uwierzytelniania w przeglądarce sieci Web, może zostać natychmiast wyświetlony monit o ponowne zalogowanie się za pomocą nowego kodu. Ten błąd jest zazwyczaj spowodowane przez niezgodność między nazwą logowania określonym w wierszu SSH i konto, które zalogowano się do usługi Azure AD. Aby rozwiązać ten problem:

- Sprawdź, czy nazwa logowania określona w wierszu SSH jest poprawna. Literówka w nazwie logowania może spowodować niezgodność między nazwą logowania określoną w wierszu polecenia SSH a kontem, za pomocą którego zalogowano się do usługi Azure AD. Na przykład wpisano *\@azuresuer contoso.onmicrosoft.com* zamiast *azureuser\@contoso.onmicrosoft.com*.
- Jeśli masz wiele kont użytkowników, upewnij się, że nie podasz innego konta użytkownika w oknie przeglądarki podczas logowania się do usługi Azure AD.
- Linux to system operacyjny uwzględniający wielkość liter. Istnieje różnica międzyAzureuser@contoso.onmicrosoft.com' 'azureuser@contoso.onmicrosoft.comi ' ', co może spowodować niezgodność. Upewnij się, że w wierszu SSH określono nazwę UPN z poprawną czułością wielkości liter.

### <a name="other-limitations"></a>Inne ograniczenia

Użytkownicy, którzy dziedziczą prawa dostępu za pośrednictwem grup zagnieżdżonych lub przypisań ról, nie są obecnie obsługiwani. Użytkownikowi lub grupie należy bezpośrednio przypisać [wymagane przypisania ról](#configure-role-assignments-for-the-vm). Na przykład użycie grup zarządzania lub zagnieżdżonych przypisań ról grup nie zapewni prawidłowych uprawnień umożliwiających użytkownikowi zalogowanie się.

## <a name="preview-feedback"></a>Opinie dotyczące wersji zapoznawczej

Podziel się swoją opinią na temat tej funkcji w wersji zapoznawczej lub zgłoś problemy z używaniem jej na [forum opinii usługi Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat usługi Azure Active Directory, zobacz [Co to jest usługa Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md)
