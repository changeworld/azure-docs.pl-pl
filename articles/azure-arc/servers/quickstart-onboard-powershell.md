---
title: Szybki Start — łączenie maszyn z platformą Azure przy użyciu usługi Azure ARC dla serwerów — PowerShell
description: W tym przewodniku szybki start dowiesz się, jak łączyć maszyny z platformą Azure przy użyciu usługi Azure ARC dla serwerów przy użyciu programu PowerShell
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: bobbytreed
ms.author: robreed
keywords: Azure Automation, DSC, PowerShell, Konfiguracja żądanego stanu, zarządzanie aktualizacjami, śledzenie zmian, spis, elementy Runbook, Python, graficzne, hybrydowe i dołączane
ms.date: 11/04/2019
ms.custom: mvc
ms.topic: quickstart
ms.openlocfilehash: 7fb24d53876ab8c06fca4fbfe929c06a889335f3
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74786354"
---
# <a name="quickstart-connect-machines-to-azure-using-azure-arc-for-servers---powershell"></a>Szybki Start: łączenie maszyn z platformą Azure przy użyciu usługi Azure ARC dla serwerów — PowerShell

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Zapoznaj się z obsługiwanymi klientami i wymaganą konfiguracją sieci w [usłudze Azure ARC dla serwerów — Omówienie](overview.md).

## <a name="create-a-service-principal-for-onboarding-at-scale"></a>Tworzenie jednostki usługi na potrzeby dołączania na dużą skalę

Nazwa główna usługi jest specjalną ograniczoną tożsamością zarządzania, która ma przyznane tylko minimalne uprawnienia wymagane do łączenia maszyn z platformą Azure. Jest to bezpieczniejsze niż używanie bardziej zaawansowanego konta, takiego jak Administrator dzierżawy. Nazwa główna usługi jest używana tylko podczas dołączania. Po połączeniu żądanych serwerów można bezpiecznie usunąć jednostkę usługi.

> [!NOTE]
> Ten krok jest zalecany, ale nie jest wymagany.

### <a name="steps-to-create-the-service-principal"></a>Procedura tworzenia nazwy głównej usługi

W tym przykładzie będziemy używać [Azure PowerShell](/powershell/azure/install-az-ps) do tworzenia głównej nazwy usługi (SPN). Alternatywnie możesz wykonać kroki opisane w sekcji [Tworzenie nazwy głównej usługi przy użyciu Azure Portal](../../active-directory/develop/howto-create-service-principal-portal.md) dla tego zadania.

Rola `Azure Connected Machine Onboarding` zawiera tylko uprawnienia wymagane do dołączania. Można zdefiniować uprawnienia nazwy SPN, aby umożliwić jej zakresowi pokrycie grupy zasobów lub subskrypcji.

Należy przechowywać dane wyjściowe polecenia cmdlet [`New-AzADServicePrincipal`](/powershell/module/az.resources/new-azadserviceprincipal) lub nie będzie można pobrać hasła do użycia w późniejszym kroku.

```azurepowershell-interactive
$sp = New-AzADServicePrincipal -DisplayName "Arc-for-servers" -Role "Azure Connected Machine Onboarding"
$sp
```

```output
Secret                : System.Security.SecureString
ServicePrincipalNames : {ad9bcd79-be9c-45ab-abd8-80ca1654a7d1, https://Arc-for-servers}
ApplicationId         : ad9bcd79-be9c-45ab-abd8-80ca1654a7d1
ObjectType            : ServicePrincipal
DisplayName           : Hybrid-RP
Id                    : 5be92c87-01c4-42f5-bade-c1c10af87758
Type                  :
```

Teraz Pobierz hasło przy użyciu programu PowerShell.

```azurepowershell-interactive
$credential = New-Object pscredential -ArgumentList "temp", $sp.Secret
$credential.GetNetworkCredential().password
```

Na podstawie danych wyjściowych Skopiuj **hasło** i identyfikator **aplikacji** (z poprzedniego kroku) i Zapisz je później w bezpiecznym miejscu, na przykład w magazynie wpisów tajnych dla narzędzia do konfiguracji serwera. Jeśli zapomnisz lub utracisz hasło nazwy SPN, możesz zresetować ją za pomocą polecenia cmdlet [`New-AzADSpCredential`](/powershell/module/azurerm.resources/new-azurermadspcredential) .

W skrypcie instalacji dołączania agenta:

* Właściwość Identyfikator **aplikacji** jest używana dla parametru `--service-principal-id` używanego w agencie instalacji
* Właściwość **Password** jest używana dla parametru `--service-principal-secret` w agencie instalacji.

## <a name="manually-install-the-agent-and-connect-to-azure"></a>Ręczne instalowanie agenta i nawiązywanie połączenia z platformą Azure

Poniższy przewodnik pozwala połączyć maszynę z platformą Azure, logując się do maszyny i wykonując czynności. Możesz również łączyć maszyny z platformą Azure [z poziomu portalu](quickstart-onboard-portal.md).

### <a name="download-and-install-the-agent"></a>Pobieranie i Instalowanie agenta

Zainstalowanie pakietu agenta wymaga dostępu głównego lub administratora lokalnego na serwerze docelowym, ale nie ma dostępu do platformy Azure.

#### <a name="linux"></a>Linux

W przypadku serwerów z **systemem Linux** Agent jest dystrybuowany za pośrednictwem [repozytorium pakietu firmy Microsoft](https://packages.microsoft.com) przy użyciu preferowanego formatu pakietu dla dystrybucji (. RPM lub. DEB).

> [!NOTE]
> W publicznej wersji zapoznawczej wydano tylko jeden pakiet, który jest odpowiedni dla Ubuntu 16,04 lub 18,04.

<!-- What about this aks? -->
Najprostszą opcją jest zarejestrowanie repozytorium pakietów, a następnie zainstalowanie pakietu przy użyciu Menedżera pakietów dystrybucji.
Skrypt bash znajdujący się w [https://aka.ms/azcmagent](https://aka.ms/azcmagent) wykonuje następujące czynności:

1. Konfiguruje maszynę hosta do pobrania z `packages.microsoft.com`.
2. Instaluje pakiet hybrydowego dostawcy zasobów.
3. Opcjonalnie można skonfigurować agenta dla operacji serwera proxy, jeśli określono `--proxy`.

Skrypt zawiera również sprawdzenia obsługiwane i nieobsługiwane dystrybucje oraz wykrywanie wymaganych uprawnień do instalacji.

Poniższy przykład pobiera agenta i instaluje go bez kontroli warunkowej.

```bash
# Download the installation package
wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

# Install the connected machine agent. Omit the '--proxy "{proxy-url}"' parameters if proxy is not needed
bash ~/Install_linux_azcmagent.sh--proxy "{proxy-url}"
```

> [!NOTE]
> Jeśli wolisz nie odwoływać się do repozytorium pakietów firmy Microsoft, możesz skopiować plik pakietu z tego miejsca do wewnętrznego repozytorium.

#### <a name="windows"></a>Windows

W przypadku **systemu Windows**Agent jest spakowany w pliku Instalator Windows (`.MSI`) i można go pobrać z [https://aka.ms/AzureConnectedMachineAgent](https://aka.ms/AzureConnectedMachineAgent), który jest hostowany w [https://download.microsoft.com](https://download.microsoft.com).

```powershell
# Download the package
Invoke-WebRequest -Uri https://aka.ms/AzureConnectedMachineAgent -OutFile AzureConnectedMachineAgent.msi

# Install the package
msiexec /i AzureConnectedMachineAgent.msi /l*v installationlog.txt /qn | Out-String
```

> [!NOTE]
> W systemie Linux ponowne uruchomienie skryptu instalacji spowoduje automatyczne uaktualnienie do najnowszej wersji. W systemie Windows przed ponownym uruchomieniem Instalatora należy odinstalować "agenta połączonej maszyny platformy Azure".

### <a name="connecting-to-azure"></a>Nawiązywanie połączenia z platformą Azure

Po zainstalowaniu można zarządzać agentem i konfigurować go przy użyciu narzędzia wiersza polecenia o nazwie `azcmagent.exe`. Agent znajduje się w obszarze `/opt/azcmagent/bin` w systemach Linux i `$env:programfiles\AzureConnectedMachineAgent` w systemie Windows.

W systemie Windows otwórz program PowerShell jako administrator w węźle docelowym i uruchom polecenie:

```powershell
& "$env:ProgramFiles\AzureConnectedMachineAgent\azcmagent.exe" connect `
  --service-principal-id "{your-spn-appid}" `
  --service-principal-secret "{your-spn-password}" `
  --resource-group "{your-resource-group-name}" `
  --tenant-id "{your-tenant-id}" `
  --location "{location-of-your-resource-group}" `
  --subscription-id "{your-subscription-id}"
```

W systemie Linux Otwórz powłokę i uruchom polecenie

<!-- Same command for linux?-->
```bash
azcmagent connect \
  --service-principal-id "{your-spn-appid}" \
  --service-principal-secret "{your-spn-password}" \
  --resource-group "{your-resource-group-name}" \
  --tenant-id "{your-tenant-id}" \
  --location "{location-of-your-resource-group}" \
  --subscription-id "{your-subscription-id}"
```

Parametry:

* `tenant-id`: identyfikator GUID dzierżawy. Można go znaleźć w Azure Portal, wybierając pozycję **Azure Active directory** -> **Właściwości** -> **Directory ID**.
* `subscription-id`: identyfikator GUID subskrypcji na platformie Azure, w której ma zostać nawiązane połączenie z maszyną.
* `resource-group`: Grupa zasobów, w której ma nawiązać połączenie z maszyną.
* `location`: zobacz [regiony i lokalizacje platformy Azure](https://azure.microsoft.com/global-infrastructure/regions/). Ta lokalizacja może być taka sama lub inna, jak lokalizacja grupy zasobów. W publicznej wersji zapoznawczej usługa jest obsługiwana w **WestUS2** i **Europa Zachodnia**.
* `resource-name`: (*Opcjonalnie*) używany do reprezentacji zasobów platformy Azure na komputerze lokalnym. Jeśli ta wartość nie zostanie określona, zostanie użyta nazwa hosta maszyny.

Więcej informacji na temat narzędzia "azcmagent" można znaleźć w temacie [Azcmagent Reference](azcmagent-reference.md).
<!-- Isn't this still needed to view machines? -->

Po pomyślnym zakończeniu komputer zostanie połączony z platformą Azure. Maszynę można wyświetlić w Azure Portal, odwiedzając [https://aka.ms/hybridmachineportal](https://aka.ms/hybridmachineportal).

![Pomyślne dołączanie](./media/quickstart-onboard/arc-for-servers-successful-onboard.png)

### <a name="proxy-server-configuration"></a>Konfiguracja serwera proxy

#### <a name="linux"></a>Linux

<!-- New proxy name? -->

W przypadku systemu **Linux**, jeśli serwer wymaga serwera proxy, można wykonać jedną z:

* Uruchom skrypt `install_linux_hybrid_agent.sh` w powyższej sekcji [Install the Agent](#download-and-install-the-agent) , z `--proxy`.
* Jeśli Agent został już zainstalowany, wykonaj polecenie `/opt/azcmagent/bin/hybridrp_proxy add http://{proxy-url}:{proxy-port}`, które konfiguruje serwer proxy i ponownie uruchamia agenta.

#### <a name="windows"></a>Windows

W przypadku **systemu Windows**, jeśli serwer proxy wymaga dostępu do zasobów internetowych, należy uruchomić poniższe polecenie, aby ustawić zmienną środowiskową serwera proxy. Dzięki temu Agent może korzystać z serwera proxy w celu uzyskania dostępu do Internetu.

```powershell
# If a proxy server is needed, execute these commands with actual proxy URL
[Environment]::SetEnvironmentVariable("https_proxy", "http://{proxy-url}:{proxy-port}", "Machine")
$env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
# The agent service needs to be restarted after the proxy environment variable is set in order for the changes to take effect.
Restart-Service -Name himds
```

> [!NOTE]
> Uwierzytelnione serwery proxy nie są obsługiwane w publicznej wersji zapoznawczej.

## <a name="clean-up"></a>Czyszczenie

Aby rozłączyć maszynę z usługi Azure ARC dla serwerów, należy wykonać dwa kroki.

1. Wybierz maszynę w [portalu](https://aka.ms/hybridmachineportal), kliknij wielokropek (`...`), a następnie wybierz pozycję **Usuń**.
1. Odinstaluj agenta z komputera.

   W systemie Windows możesz odinstalować agenta za pomocą panelu sterowania "Aplikacje & funkcje".
  
  ![Aplikacje & funkcje](./media/quickstart-onboard/apps-and-features.png)

   Jeśli chcesz, aby skrypt został odinstalowany, możesz użyć poniższego przykładu, który pobiera **PackageID** i odinstalowuje agenta przy użyciu `msiexec /X`.

   Poszukaj w kluczu rejestru `HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Uninstall` i Znajdź **PackageID**. Następnie można odinstalować agenta za pomocą `msiexec`.

   W poniższym przykładzie przedstawiono Odinstalowywanie agenta.

   ```powershell
   Get-ChildItem -Path HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall | `
   Get-ItemProperty | `
   Where-Object {$_.DisplayName -eq "Azure Connected Machine Agent"} | `
   ForEach-Object {MsiExec.exe /Quiet /X "$($_.PsChildName)"}
   ```

   W systemie Linux wykonaj następujące polecenie, aby odinstalować agenta.

   ```bash
   sudo apt purge hybridagent
   ```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Przypisywanie zasad do połączonych maszyn](../../governance/policy/assign-policy-portal.md)
