---
title: Zaloguj się do maszyny wirtualnej systemu Windows na platformie Azure przy użyciu usługi Azure Active Directory (Wersja zapoznawcza)
description: Logowanie się usługi Azure AD do maszyny Wirtualnej platformy Azure z systemem Windows
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 88ae3c45126403161e35ec46e5ccc2666c3edb55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050075"
---
# <a name="sign-in-to-windows-virtual-machine-in-azure-using-azure-active-directory-authentication-preview"></a>Zaloguj się do maszyny wirtualnej systemu Windows na platformie Azure przy użyciu uwierzytelniania usługi Azure Active Directory (wersja zapoznawcza)

Organizacje mogą teraz korzystać z uwierzytelniania usługi Azure Active Directory (AD) dla swoich maszyn wirtualnych platformy Azure z systemem **Windows Server 2019 Datacenter edition** lub Windows **10 1809** i nowszymi. Za pomocą usługi Azure AD do uwierzytelniania maszyn wirtualnych zapewnia możliwość centralnego sterowania i egzekwowania zasad. Narzędzia, takie jak kontrola dostępu oparta na rolach platformy Azure (RBAC) i dostęp warunkowy usługi Azure AD umożliwiają kontrolowanie, kto może uzyskać dostęp do maszyny Wirtualnej. W tym artykule pokazano, jak utworzyć i skonfigurować maszynę wirtualną systemu Windows Server 2019 do używania uwierzytelniania usługi Azure AD.

|     |
| --- |
| Logowanie się usługi Azure AD dla maszyn wirtualnych systemu Windows platformy Azure jest publiczną funkcją w wersji zapoznawczej usługi Azure Active Directory. Aby uzyskać więcej informacji na temat wersji zapoznawców, zobacz [Dodatkowe warunki użytkowania w wersji Zapoznawczej platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Istnieje wiele korzyści z używania uwierzytelniania usługi Azure AD do logowania się do maszyn wirtualnych systemu Windows na platformie Azure, w tym:

- Korzystaj z tych samych poświadczeń federacyjne lub zarządzanych usługi Azure AD, których zwykle używasz.
- Nie trzeba już zarządzać kontami administratorów lokalnych.
- Usługa Azure RBAC umożliwia udzielenie odpowiedniego dostępu do maszyn wirtualnych na podstawie potrzeb i usunięcie go, gdy nie jest już potrzebne.
- Przed zezwoleniem na dostęp do maszyny Wirtualnej dostęp warunkowy usługi Azure AD może wymusić dodatkowe wymagania, takie jak: 
   - Uwierzytelnianie wieloskładnikowe
   - Sprawdzanie ryzyka logowania
- Automatyzuj i skaluj dołączanie do usługi Azure AD maszyn wirtualnych systemu Windows, które są częścią wdrożeń VDI.

> [!NOTE]
> Po włączeniu tej funkcji maszyny wirtualne z systemem Windows na platformie Azure zostaną połączone z usługą Azure AD. Nie można dołączyć go do innej domeny, takich jak lokalne usługi AD lub usługi Azure AD DS. Jeśli musisz to zrobić, należy odłączyć maszynę wirtualną od dzierżawy usługi Azure AD, odinstalowując rozszerzenie.

## <a name="requirements"></a>Wymagania

### <a name="supported-azure-regions-and-windows-distributions"></a>Obsługiwane regiony platformy Azure i dystrybucje systemu Windows

Podczas podglądu tej funkcji są obecnie obsługiwane następujące dystrybucje systemu Windows:

- Windows Server 2019 Datacenter
- Windows 10 1809 i nowsze

> [!IMPORTANT]
> Zdalne połączenie z maszynami wirtualnymi przyłączonymi do usługi Azure AD jest dozwolone tylko z komputerów z systemem Windows 10, które są przyłączone do usługi Azure AD lub hybrydowej usługi Azure AD przyłączone do **tego samego** katalogu co maszyna wirtualna. 

Następujące regiony platformy Azure są obecnie obsługiwane podczas podglądu tej funkcji:

- Wszystkie globalne regiony platformy Azure

> [!IMPORTANT]
> Aby korzystać z tej funkcji w wersji zapoznawczej, należy wdrożyć tylko obsługiwaną dystrybucję systemu Windows i w obsługiwanym regionie platformy Azure. Ta funkcja nie jest obecnie obsługiwana w usłudze Azure Government ani w chmurach suwerennych.

### <a name="network-requirements"></a>Wymagania dotyczące sieci

Aby włączyć uwierzytelnianie usługi Azure AD dla maszyn wirtualnych z systemem Windows na platformie Azure, należy upewnić się, że konfiguracja sieci maszyn wirtualnych zezwala na dostęp wychodzący do następujących punktów końcowych za pośrednictwem portu TCP 443:

- https:\//enterpriseregistration.windows.net
- https:\//login.microsoftonline.com
- https:\//device.login.microsoftonline.com
- https:\//pas.windows.net

## <a name="enabling-azure-ad-login-in-for-windows-vm-in-azure"></a>Włączanie logowania usługi Azure AD dla maszyny Wirtualnej systemu Windows na platformie Azure

Aby użyć logowania usługi Azure AD dla maszyny Wirtualnej systemu Windows na platformie Azure, należy najpierw włączyć opcję logowania usługi Azure AD dla maszyny Wirtualnej systemu Windows, a następnie należy skonfigurować przypisania ról RBAC dla użytkowników, którzy są upoważnieni do logowania się do maszyny Wirtualnej.
Istnieje wiele sposobów włączania logowania usługi Azure AD dla maszyny Wirtualnej systemu Windows:

- Korzystanie z usługi Azure portal podczas tworzenia maszyny Wirtualnej systemu Windows
- Korzystanie z usługi Azure Cloud Shell podczas tworzenia maszyny Wirtualnej systemu Windows **lub dla istniejącej maszyny Wirtualnej systemu Windows**

### <a name="using-azure-portal-create-vm-experience-to-enable-azure-ad-login"></a>Korzystanie z usługi Azure Portal tworzenie środowiska maszyny Wirtualnej, aby włączyć logowanie usługi Azure AD

Można włączyć logowanie usługi Azure AD dla centrów danych systemu Windows Server 2019 lub obrazów maszyn wirtualnych systemu Windows 10 1809 i nowszych. 

Aby utworzyć maszynę wirtualną centrum danych systemu Windows Server 2019 na platformie Azure przy za pomocą logowania usługi Azure AD: 

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com), z kontem, które ma dostęp do tworzenia maszyn wirtualnych i wybierz **+ Utwórz zasób**.
1. Wpisz **system Windows Server** w polu Wyszukaj pasek wyszukiwania w portalu Marketplace.
   1. Kliknij **pozycję Windows Server** i wybierz pozycję Centrum danych systemu Windows Server **2019** z listy rozwijanej Wybierz plan oprogramowania.
   1. Kliknij pozycję **Utwórz**.
1. Na karcie "Zarządzanie" włącz opcję **Logowania przy użyciu poświadczeń usługi AAD (Wersja zapoznawcza)** w sekcji Usługa Azure Active Directory od **wyłączonego**do włączone .
1. Upewnij się, że **system przypisany tożsamości zarządzanej** w sekcji Tożsamość jest ustawiona **na Włączone**. Ta akcja powinna nastąpić automatycznie po włączeniu logowania przy użyciu poświadczeń usługi Azure AD.
1. Przejdź przez resztę doświadczenia tworzenia maszyny wirtualnej. Podczas tej wersji zapoznawczej należy utworzyć nazwę użytkownika administratora i hasło dla maszyny Wirtualnej.

![Logowanie przy użyciu poświadczeń usługi Azure AD umożliwia utworzenie maszyny Wirtualnej](./media/howto-vm-sign-in-azure-ad-windows/azure-portal-login-with-azure-ad.png)

> [!NOTE]
> Aby zalogować się do maszyny Wirtualnej przy użyciu poświadczeń usługi Azure AD, należy najpierw skonfigurować przypisania ról dla maszyny Wirtualnej, zgodnie z opisem w jednej z poniższych sekcji.

### <a name="using-the-azure-cloud-shell-experience-to-enable-azure-ad-login"></a>Korzystanie z usługi Azure Cloud Shell w celu umożliwienia logowania usługi Azure AD

Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Typowe narzędzia platformy Azure są wstępnie zainstalowane i skonfigurowane w usłudze Cloud Shell na potrzeby użycia z poziomu konta. Po prostu wybierz przycisk Kopiuj, aby skopiować kod, wklej go do usługi Cloud Shell, a następnie naciśnij klawisz Enter, aby go uruchomić. Usługę Cloud Shell można otworzyć na kilka sposobów:

Wybierz pozycję Wypróbuj w prawym górnym rogu bloku kodu.
Otwórz usługę Cloud Shell w swojej przeglądarce.
Wybierz przycisk Cloud Shell w menu w prawym górnym rogu witryny [Azure Portal](https://portal.azure.com).

Jeśli zdecydujesz się zainstalować i używać interfejsu wiersza polecenia lokalnie, ten artykuł wymaga, aby uruchomić interfejsu wiersza polecenia platformy Azure w wersji 2.0.31 lub nowszej. Aby odnaleźć wersję, uruchom polecenie az --version. Jeśli chcesz zainstalować lub uaktualnić, zobacz artykuł [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

1. Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az-group-create). 
1. Utwórz maszynę wirtualną z [az vm utworzyć](/cli/azure/vm#az-vm-create) przy użyciu obsługiwanej dystrybucji w obsługiwanym regionie. 
1. Zainstaluj rozszerzenie maszyny Wirtualnej logowania usługi Azure AD. 

Poniższy przykład wdraża maszynę wirtualną o nazwie myVM, która używa Win2019Datacenter, do grupy zasobów o nazwie myResourceGroup, w regionie southcentralus. W poniższych przykładach można podać własną grupę zasobów i nazwy maszyn wirtualnych w razie potrzeby.

```AzureCLI
az group create --name myResourceGroup --location southcentralus

az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image Win2019Datacenter \
    --assign-identity \
    --admin-username azureuser \
    --admin-password yourpassword
```

> [!NOTE]
> Przed zainstalowaniem rozszerzenia maszyny wirtualnej logowania usługi Azure AD należy włączyć tożsamość zarządzaną przypisaną przez system na maszynie wirtualnej.

Utworzenie maszyny wirtualnej i zasobów pomocniczych potrwa kilka minut.

Na koniec zainstaluj rozszerzenie maszyny Wirtualnej logowania usługi Azure AD, aby włączyć logowanie usługi Azure AD dla maszyny Wirtualnej systemu Windows. Rozszerzenia maszyn wirtualnych to małe aplikacje, które zapewniają konfigurację po wdrożeniu i zadania automatyzacji na maszynach wirtualnych platformy Azure. Użyj zestawu [rozszerzeń maszyn wirtualnych az,](/cli/azure/vm/extension#az-vm-extension-set) aby zainstalować rozszerzenie AADLoginForWindows na maszynie Wirtualnej o nazwie myVM w grupie zasobów myResourceGroup:

> [!NOTE]
> Rozszerzenie AADLoginForWindows można zainstalować na istniejącym systemie Windows Server 2019 lub Windows 10 1809 i nowszej maszynie wirtualnej, aby włączyć je do uwierzytelniania usługi Azure AD. Poniżej przedstawiono przykład interfejsu wiersza polecenia AZ.

```AzureCLI
az vm extension set \
    --publisher Microsoft.Azure.ActiveDirectory \
    --name AADLoginForWindows \
    --resource-group myResourceGroup \
    --vm-name myVM
```

Of `provisioningState` `Succeeded` jest wyświetlany, gdy rozszerzenie jest zainstalowany na maszynie Wirtualnej.

## <a name="configure-role-assignments-for-the-vm"></a>Konfigurowanie przypisań ról dla maszyny Wirtualnej

Teraz, gdy utworzono maszynę wirtualną, należy skonfigurować zasady RBAC platformy Azure, aby określić, kto może zalogować się do maszyny Wirtualnej. Dwie role RBAC są używane do autoryzowania logowania maszyn wirtualnych:

- **Logowanie administratora maszyny wirtualnej:** użytkownicy z przypisaną tą rolą mogą logować się do maszyny wirtualnej platformy Azure z uprawnieniami administratora.
- **Login użytkownika maszyny wirtualnej:** użytkownicy z przypisaną tą rolą mogą logować się do maszyny wirtualnej platformy Azure z uprawnieniami zwykłych użytkowników.

> [!NOTE]
> Aby umożliwić użytkownikowi zalogowanie się do maszyny wirtualnej za pomocą protokołu RDP, należy przypisać rolę logowania administratora maszyny wirtualnej lub logowania użytkownika maszyny wirtualnej. Użytkownik platformy Azure z rolami właściciela lub współautora przypisanymi do maszyny Wirtualnej nie ma automatycznie uprawnień do logowania się do maszyny Wirtualnej za pomocą protokołu RDP. Ma to na celu zapewnienie skontrolowanego oddzielenia między zestawem osób, które kontrolują maszyny wirtualne, a zestawem osób, które mogą uzyskiwać dostęp do maszyn wirtualnych.

Istnieje wiele sposobów konfigurowania przypisań ról dla maszyny Wirtualnej:

- Korzystanie z usługi Azure AD Portal
- Korzystanie z usługi Azure Cloud Shell

### <a name="using-azure-ad-portal-experience"></a>Korzystanie z usługi Azure AD Portal

Aby skonfigurować przypisania ról dla maszyn wirtualnych z włączonym systemem Windows Server 2019 w systemie Windows Server 2019:

1. Przejdź do strony przeglądu określonej maszyny wirtualnej
1. Wybierz **kontrolka dostępu (IAM)** z opcji menu
1. Wybierz **pozycję Dodaj**, Dodaj **przypisanie roli,** aby otworzyć okienko Dodaj przypisanie roli.
1. Z listy rozwijanej **Rola** wybierz rolę, taką jak **Logowanie administratora maszyny wirtualnej** lub **Logowanie użytkownika maszyny wirtualnej**.
1. W polu **Wybierz** wybierz użytkownika, grupę, jednostkę usługi lub tożsamość zarządzaną. Jeśli nie widzisz podmiotu zabezpieczeń na liście, możesz wpisać tekst w polu **Wybierz**, aby wyszukać w katalogu nazwy wyświetlane, adresy e-mail i identyfikatory obiektów.
1. Wybierz **pozycję Zapisz**, aby przypisać rolę.

Po kilku chwilach podmiot zabezpieczeń otrzymuje przypisaną rolę w wybranym zakresie.

![Przypisywanie ról użytkownikom, którzy będą uzyskiwać dostęp do maszyny Wirtualnej](./media/howto-vm-sign-in-azure-ad-windows/azure-portal-access-control-assign-role.png)

### <a name="using-the-azure-cloud-shell-experience"></a>Korzystanie z usługi Azure Cloud Shell

W poniższym przykładzie użyto [az przypisanie roli utworzyć](/cli/azure/role/assignment#az-role-assignment-create) przypisać rolę logowania administratora maszyny wirtualnej do maszyny wirtualnej dla bieżącego użytkownika platformy Azure. Nazwa użytkownika aktywnego konta platformy Azure jest uzyskiwana za pomocą [programu AZ Account Show,](/cli/azure/account#az-account-show)a zakres jest ustawiony na maszynę wirtualną utworzoną w poprzednim kroku z [programem AZ VM Show](/cli/azure/vm#az-vm-show). Zakres można również przypisać na poziomie grupy zasobów lub subskrypcji i stosuje się normalne uprawnienia dziedziczenia RBAC. Aby uzyskać więcej informacji, zobacz [Formanty dostępu oparte na rolach](../../virtual-machines/linux/login-using-aad.md).

```   AzureCLI
username=$(az account show --query user.name --output tsv)
vm=$(az vm show --resource-group myResourceGroup --name myVM --query id -o tsv)

az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee $username \
    --scope $vm
```

> [!NOTE]
> Jeśli domena AAD i domena nazwy użytkownika logowania nie są zgodne, należy `--assignee-object-id`określić identyfikator `--assignee`obiektu konta użytkownika za pomocą , a nie tylko nazwy użytkownika dla . Identyfikator obiektu dla konta użytkownika można uzyskać za pomocą [listy użytkowników reklam az](/cli/azure/ad/user#az-ad-user-list).

Aby uzyskać więcej informacji na temat używania funkcji RBAC do zarządzania dostępem do zasobów subskrypcji platformy Azure, zobacz następujące artykuły:

- [Zarządzanie dostępem do zasobów platformy Azure przy użyciu funkcji RBAC i interfejsu wiersza polecenia platformy Azure](/azure/role-based-access-control/role-assignments-cli)
- [Zarządzanie dostępem do zasobów platformy Azure za pomocą kontroli dostępu opartej na rolach i witryny Azure Portal](/azure/role-based-access-control/role-assignments-portal)
- [Zarządzanie dostępem do zasobów platformy Azure przy użyciu funkcji RBAC i azure powershell](/azure/role-based-access-control/role-assignments-powershell).

## <a name="using-conditional-access"></a>Korzystanie z dostępu warunkowego

Przed autoryzowaniem dostępu do maszyn wirtualnych systemu Windows na platformie Azure, które są włączone za pomocą logowania usługi Azure AD, można wymusić zasady dostępu warunkowego, takie jak uwierzytelnianie wieloskładnikowe lub sprawdzanie ryzyka logowania użytkownika. Aby zastosować zasady dostępu warunkowego, należy wybrać aplikację "Azure Windows VM Sign-In" z opcji przypisywania aplikacji w chmurze lub akcji, a następnie użyć ryzyka logowania jako warunku i/lub wymagać uwierzytelniania wieloskładnikowego jako kontroli dostępu do dotacji. 

> [!NOTE]
> Jeśli używasz "Wymagaj uwierzytelniania wieloskładnikowego" jako kontroli dostępu do dotacji dla żądania dostępu do aplikacji "Azure Windows VM Sign-In", musisz podać oświadczenie uwierzytelniania wieloskładnikowego jako część klienta, który inicjuje sesję RDP do docelowego systemu Windows Maszyna wirtualna na platformie Azure. Jedynym sposobem osiągnięcia tego celu na kliencie systemu Windows 10 jest użycie numeru PIN windows hello dla firm lub authenication biometrycznego z klientem RDP. Obsługa uwierzytelniania biometrycznego została dodana do klienta RDP w systemie Windows 10 w wersji 1809. Pulpit zdalny przy użyciu uwierzytelniania Windows Hello dla firm jest dostępny tylko dla wdrożeń korzystających z modelu zaufania certyfikatu i obecnie nie jest dostępny dla modelu zaufania kluczy.

## <a name="log-in-using-azure-ad-credentials-to-a-windows-vm"></a>Zaloguj się przy użyciu poświadczeń usługi Azure AD na maszynie Wirtualnej systemu Windows

> [!IMPORTANT]
> Zdalne połączenie z maszynami wirtualnymi przyłączonymi do usługi Azure AD jest dozwolone tylko z komputerów z systemem Windows 10, które są przyłączone do usługi Azure AD lub hybrydowej usługi Azure AD przyłączone do **tego samego** katalogu co maszyna wirtualna. Ponadto do protokołu RDP przy użyciu poświadczeń usługi Azure AD użytkownik musi należeć do jednej z dwóch ról RBAC, logowania administratora maszyny wirtualnej lub logowania użytkownika maszyny wirtualnej. W tej chwili usługi Azure Bastion nie można używać do logowania przy użyciu uwierzytelniania usługi Azure Active Directory za pomocą rozszerzenia AADLoginForWindows. Obsługiwany jest tylko bezpośredni prow.

Aby zalogować się do maszyny wirtualnej systemu Windows Server 2019 przy użyciu usługi Azure AD: 

1. Przejdź do strony przeglądu maszyny wirtualnej, która została włączona przy logowaniu usługi Azure AD.
1. Wybierz **opcję Połącz,** aby otworzyć blok Połącz z maszyną wirtualną.
1. Wybierz opcję **Pobierz plik RDP**.
1. Wybierz **pozycję Otwórz,** aby uruchomić klienta połączenia pulpitu zdalnego.
1. Wybierz **pozycję Połącz,** aby uruchomić okno dialogowe logowania systemu Windows.
1. Logowanie przy użyciu poświadczeń usługi Azure AD.

Jesteś teraz zalogowany do maszyny wirtualnej platformy Azure systemu Windows Server 2019 z przypisanymi uprawnieniami roli, takimi jak użytkownik maszyny wirtualnej lub administrator maszyny wirtualnej. 

> [!NOTE]
> Można zapisać plik . Plik RDP lokalnie na komputerze, aby uruchomić przyszłe połączenia pulpitu zdalnego z maszyną wirtualną zamiast przechodzenia do strony przeglądu maszyny wirtualnej w witrynie Azure portal i przy użyciu opcji łączenia.

## <a name="troubleshoot"></a>Rozwiązywanie problemów

### <a name="troubleshoot-deployment-issues"></a>Rozwiązywanie problemów dotyczących wdrożenia

Rozszerzenie AADLoginForWindows musi zostać pomyślnie zainstalowane, aby maszyna wirtualna zakończyła proces dołączania usługi Azure AD. Wykonaj następujące kroki, jeśli rozszerzenie maszyny Wirtualnej nie może poprawnie zainstalować.

1. RDP do maszyny Wirtualnej przy użyciu konta administratora lokalnego i sprawdź CommandExecuti'n.log w obszarze  
   
   C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.ActiveDirectory.AADLoginForWindows\0.3.1.0. 

   > [!NOTE]
   > Jeśli rozszerzenie zostanie ponownie uruchomione po początkowym niepowodzeniu, dziennik z błędem wdrożenia zostanie zapisany jako CommandExecution_YYYYMMDDHHMMSSSSS.log. "
1. Otwórz wiersz polecenia na maszynie Wirtualnej i sprawdź te zapytania względem punktu końcowego usługi imds (ImdS) wystąpienia uruchomionego na hoście platformy Azure zwraca:

   | Polecenie do uruchomienia | Oczekiwane dane wyjściowe |
   | --- | --- |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/instance?api-version=2017-08-01"` | Poprawne informacje o maszynie Wirtualnej platformy Azure |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/identity/info?api-version=2018-02-01"` | Prawidłowy identyfikator dzierżawy skojarzony z subskrypcją platformy Azure |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/identity/oauth2/token?resource=urn:ms-drs:enterpriseregistration.windows.net&api-version=2018-02-01"` | Prawidłowy token dostępu wystawiony przez usługę Azure Active Directory dla tożsamości zarządzanej przypisanej do tej maszyny Wirtualnej |

   > [!NOTE]
   > Token dostępu można dekodować za [http://calebb.net/](http://calebb.net/)pomocą narzędzia, takiego jak . Sprawdź "appid" w tokenie dostępu pasuje do tożsamości zarządzanej przypisanej do maszyny Wirtualnej.

1. Upewnij się, że wymagane punkty końcowe są dostępne z maszyny Wirtualnej za pomocą wiersza polecenia:
   
   - curl https:\//login.microsoftonline.com/ -D –
   - curl https:\//login.microsoftonline.com/`<TenantID>`/ -D –

   > [!NOTE]
   > Zamień `<TenantID>` identyfikator dzierżawy usługi Azure AD skojarzony z subskrypcją platformy Azure.

   - curl https:\//enterpriseregistration.windows.net/ -D -
   - curl https:\//device.login.microsoftonline.com/ -D -
   - curl https:\//pas.windows.net/ -D -

1. Stan urządzenia można wyświetlić, `dsregcmd /status`uruchamiając plik . Celem jest, aby stan `AzureAdJoined : YES`urządzenia był pokazywał jako .

   > [!NOTE]
   > Działanie sprzężenia usługi Azure AD jest przechwytywane w Podglądzie zdarzeń w dzienniku Rejestracja urządzenia użytkownika\Administrator.

Jeśli rozszerzenie AADLoginForWindows nie powiedzie się z pewnym kodem błędu, można wykonać następujące kroki:

#### <a name="issue-1-aadloginforwindows-extension-fails-to-install-with-terminal-error-code-1007-and-exit-code--2145648574"></a>Problem 1: Rozszerzenie AADLoginForWindows nie można zainstalować z kodem błędu terminalu "1007" i kodem zakończenia: -2145648574.

Ten kod zakończenia przekłada się na DSREG_E_MSI_TENANTID_UNAVAILABLE, ponieważ rozszerzenie nie może zbadać informacji o dzierżawie usługi Azure AD.

1. Sprawdź, czy maszyna wirtualna platformy Azure może pobrać identyfikator dzierżawcy z usługi metadanych wystąpienia.

   - RDP do maszyny Wirtualnej jako administrator lokalny i sprawdź, czy punkt końcowy zwraca prawidłowy identyfikator dzierżawy, uruchamiając to polecenie z wiersza polecenia z podwyższonym poziomem uprawnień na maszynie wirtualnej:
      
      - curl -H Metadane:truehttp://169.254.169.254/metadata/identity/info?api-version=2018-02-01

1. Administrator maszyny Wirtualnej próbuje zainstalować rozszerzenie AADLoginForWindows, ale system przypisany tożsamości zarządzanej nie włączył maszyny Wirtualnej po raz pierwszy. Przejdź do bloku tożsamości maszyny Wirtualnej. Na karcie Przypisany system sprawdź, czy stan jest włączony.

#### <a name="issue-2-aadloginforwindows-extension-fails-to-install-with-exit-code--2145648607"></a>Problem 2: Rozszerzenie AADLoginForWindows nie można zainstalować z kodem zakończenia: -2145648607

Ten kod zakończenia przekłada się na DSREG_AUTOJOIN_DISC_FAILED, ponieważ rozszerzenie `https://enterpriseregistration.windows.net` nie jest w stanie dotrzeć do punktu końcowego.

1. Sprawdź, czy wymagane punkty końcowe są dostępne z maszyny Wirtualnej za pomocą wiersza polecenia:

   - curl https:\//login.microsoftonline.com/ -D –
   - curl https:\//login.microsoftonline.com/`<TenantID>`/ -D –
   
   > [!NOTE]
   > Zamień `<TenantID>` identyfikator dzierżawy usługi Azure AD skojarzony z subskrypcją platformy Azure. Jeśli chcesz znaleźć identyfikator dzierżawy, możesz najechać kursorem na nazwę konta, aby uzyskać identyfikator katalogu/ dzierżawy, lub wybierz pozycję Azure Active Directory > Properties > Directory ID w witrynie Azure portal.

   - curl https:\//enterpriseregistration.windows.net/ -D -
   - curl https:\//device.login.microsoftonline.com/ -D -
   - curl https:\//pas.windows.net/ -D -

1. Jeśli którekolwiek z poleceń nie powiedzie się z "Nie można rozpoznać hosta", `<URL>`spróbuj uruchomić to polecenie, aby określić serwer DNS, który jest używany przez maszynę wirtualną.
   
   `nslookup <URL>`

   > [!NOTE] 
   > Zamień `<URL>` na w pełni kwalifikowane nazwy domen używane przez punkty końcowe, takie jak "login.microsoftonline.com".

1. Następnie sprawdź, czy określenie publicznego serwera DNS umożliwia pomyślne wykonanie polecenia:

   `nslookup <URL> 208.67.222.222`

1. W razie potrzeby zmień serwer DNS przypisany do sieciowej grupy zabezpieczeń, do której należy maszyna wirtualna platformy Azure.

#### <a name="issue-3-aadloginforwindows-extension-fails-to-install-with-exit-code-51"></a>Problem 3: Rozszerzenie AADLoginForWindows nie można zainstalować z kodem zakończenia: 51

Kod zakończenia 51 tłumaczy się na "To rozszerzenie nie jest obsługiwane w systemie operacyjnym maszyny Wirtualnej".

W publicznej wersji zapoznawczej rozszerzenie AADLoginForWindows jest przeznaczone tylko do zainstalowania w systemie Windows Server 2019 lub Windows 10 (kompilacja 1809 lub nowsza). Upewnij się, że wersja systemu Windows jest obsługiwana. Jeśli kompilacja systemu Windows nie jest obsługiwana, odinstaluj rozszerzenie maszyny Wirtualnej.

### <a name="troubleshoot-sign-in-issues"></a>Rozwiązywanie problemów z logowaniem

Niektóre typowe błędy podczas próby protokołu RDP przy użyciu poświadczeń usługi Azure AD obejmują nie przypisane role RBAC, nieautoryzowanego klienta lub metody logowania 2FA wymagane. Aby rozwiązać te problemy, użyj następujących informacji.

Urządzenie i stan rejestracji przy rejestracji w `dsregcmd /status`tym miejscu można wyświetlić po uruchomieniu pliku . Celem jest, aby stan `AzureAdJoined : YES` urządzenia `SSO State` był `AzureAdPrt : YES`pokazywał jako i pokazywał .

Ponadto logowanie protokołu RDP przy użyciu kont usługi Azure AD jest przechwytywane w Podglądzie zdarzeń w dziennikach zdarzeń usługi AAD\Operational.

#### <a name="rbac-role-not-assigned"></a>Rola RBAC nie została przypisana

Jeśli podczas inicjowania połączenia pulpitu zdalnego z maszyną wirtualną jest wyświetlany następujący komunikat o błędzie: 

- Twoje konto jest skonfigurowane tak, aby uniemożliwić korzystanie z tego urządzenia. Aby uzyskać więcej informacji, skontaktuj się z administratorem systemu

![Twoje konto jest skonfigurowane tak, aby uniemożliwić korzystanie z tego urządzenia.](./media/howto-vm-sign-in-azure-ad-windows/rbac-role-not-assigned.png)

Sprawdź, czy [skonfigurowano zasady RBAC](../../virtual-machines/linux/login-using-aad.md) dla maszyny Wirtualnej, która przyznaje użytkownikowi rolę Logowania administratora maszyny wirtualnej lub logowania użytkownika maszyny wirtualnej:
 
#### <a name="unauthorized-client"></a>Nieautoryzowany klient

Jeśli podczas inicjowania połączenia pulpitu zdalnego z maszyną wirtualną jest wyświetlany następujący komunikat o błędzie: 

- Twoje poświadczenia nie działały

![Twoje poświadczenia nie działały](./media/howto-vm-sign-in-azure-ad-windows/your-credentials-did-not-work.png)

Sprawdź, czy komputer z systemem Windows 10 używany do inicjowania połączenia pulpitu zdalnego jest połączony z usługą Azure AD lub hybrydową usługą Azure AD przyłączony do tego samego katalogu usługi Azure AD, do którego jest przyłączona maszyna wirtualna. Aby uzyskać więcej informacji na temat tożsamości urządzenia, zobacz artykuł [Co to jest tożsamość urządzenia](/azure/active-directory/devices/overview).

> [!NOTE]
> Windows 10 20H1, doda obsługę komputera zarejestrowanego w usłudze Azure AD, aby zainicjować połączenie pulpitu zdalnego z maszyną wirtualną. Dołącz do niejawnego programu testów systemu Windows, aby wypróbować ten problem i zapoznać się z nowymi funkcjami systemu Windows 10.

Ponadto sprawdź, czy rozszerzenie AADLoginForWindows nie zostało odinstalowane po zakończeniu dołączania usługi Azure AD.
 
#### <a name="mfa-sign-in-method-required"></a>Wymagana metoda logowania usługi MFA

Jeśli podczas inicjowania połączenia pulpitu zdalnego z maszyną wirtualną jest wyświetlany następujący komunikat o błędzie: 

- Metoda logowania, której próbujesz użyć, jest niedozwolona. Spróbuj użyć innej metody logowania lub skontaktuj się z administratorem systemu.

![Metoda logowania, której próbujesz użyć, jest niedozwolona.](./media/howto-vm-sign-in-azure-ad-windows/mfa-sign-in-method-required.png)

Jeśli przed dostępem do zasobu skonfigurowano zasady dostępu warunkowego, które wymagają uwierzytelniania wieloskładnikowego, należy upewnić się, że komputer z systemem Windows 10 inicjujący połączenie pulpitu zdalnego z maszyną wirtualną loguje się przy użyciu silnego metody uwierzytelniania, takiej jak Windows Hello. Jeśli nie używasz metody silnego uwierzytelniania dla połączenia pulpitu zdalnego, zostanie wyświetlony poprzedni błąd.

Jeśli nie wdrożono systemu Windows Hello dla firm i jeśli nie jest to możliwe, można wykluczyć wymaganie usługi MFA, konfigurując zasady dostępu warunkowego, która wyklucza aplikację "Azure Windows VM Sign-In" z listy aplikacji w chmurze, które wymagają usługi MFA. Aby dowiedzieć się więcej o usłudze Windows Hello dla firm, zobacz [Omówienie funkcji Windows Hello dla firm](/windows/security/identity-protection/hello-for-business/hello-identity-verification).

> [!NOTE]
> Uwierzytelnianie numeru PIN windows hello dla firm za pomocą protokołu RDP jest obsługiwane przez system Windows 10 dla kilku wersji, jednak obsługa uwierzytelniania biometrycznego za pomocą protokołu RDP została dodana w systemie Windows 10 w wersji 1809. Korzystanie z funkcji Windows Hello dla firm podczas protokołu RDP jest dostępne tylko dla wdrożeń korzystających z modelu zaufania certyfikatu i obecnie nie jest dostępne dla modelu zaufania kluczy.
 
## <a name="preview-feedback"></a>Opinie dotyczące wersji zapoznawczej

Podziel się swoją opinią na temat tej funkcji w wersji zapoznawczej lub zgłoś problemy z używaniem jej na [forum opinii usługi Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat usługi Azure Active Directory, zobacz [Co to jest usługa Azure Active Directory](/azure/active-directory/fundamentals/active-directory-whatis)
