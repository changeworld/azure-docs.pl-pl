---
title: Łączenie maszyn hybrydowych z platformą Azure za pomocą witryny Azure portal
description: W tym artykule dowiesz się, jak zainstalować agenta i połączyć maszyny z platformą Azure przy użyciu usługi Azure Arc dla serwerów (w wersji zapoznawczej) z witryny Azure portal.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 03/24/2020
ms.topic: conceptual
ms.openlocfilehash: 40885e1de4ff4c16d2a50399c654d8596396ab53
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366365"
---
# <a name="connect-hybrid-machines-to-azure-from-the-azure-portal"></a>Łączenie maszyn hybrydowych z platformą Azure za pomocą witryny Azure portal

Można włączyć usługę Azure Arc dla serwerów (wersja zapoznawcza) dla jednego lub niewielkiej liczby komputerów z systemem Windows lub Linux w twoim środowisku, wykonując zestaw kroków ręcznie. Możesz też użyć metody zautomatyzowanej, uruchamiając skrypt szablonu, który udostępniamy. Ten skrypt automatyzuje pobieranie i instalowanie obu agentów.

Ta metoda wymaga, aby masz uprawnienia administratora na komputerze, aby zainstalować i skonfigurować agenta. W systemie Linux, korzystając z konta głównego, a w systemie Windows należysz do grupy Administratorzy lokalni.

Przed rozpoczęciem należy zapoznać się z [wymaganiami wstępnymi](overview.md#prerequisites) i sprawdzić, czy subskrypcja i zasoby spełniają wymagania.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="generate-the-installation-script-from-the-azure-portal"></a>Generowanie skryptu instalacyjnego z witryny Azure portal

Skrypt do automatyzacji pobierania i instalacji oraz do nawiązania połączenia z usługą Azure Arc jest dostępny w witrynie Azure portal. Aby zakończyć proces, wykonaj następujące czynności:

1. W przeglądarce przejdź do [witryny Azure portal](https://aka.ms/hybridmachineportal).

1. Na **maszyn — Azure Arc** strony, wybierz albo **Dodaj**, w lewym górnym rogu lub **Utwórz maszynę — Azure Arc** opcji w dolnej części środkowego okienka. 

1. Na stronie **Wybierz metodę** wybierz kafelek Dodaj maszyny przy **użyciu skryptu interaktywnego,** a następnie wybierz pozycję **Generuj skrypt**.

1. Na stronie **Generowanie skryptu** wybierz grupę subskrypcji i zasobów, w której chcesz, aby komputer był zarządzany na platformie Azure. Wybierz lokalizację platformy Azure, w której będą przechowywane metadane komputera.

    >[!NOTE]
    >Usługa Azure Arc dla serwerów (wersja zapoznawcza) obsługuje tylko następujące regiony:
    >- Okręg wyborczy WestUS2
    >- WestEurope
    >- WestAsia (WestAsia)
    >
    >Przejrzyj dodatkowe zagadnienia podczas wybierania regionu [w](overview.md#supported-regions) artykule Przegląd.

1. Na stronie **Generowanie skryptu** na liście rozwijanej **System operacyjny** wybierz system operacyjny, na który będzie uruchomiony skrypt.

1. Jeśli urządzenie komunikuje się za pośrednictwem serwera proxy w celu połączenia się z Internetem, wybierz **przycisk Dalej: Serwer proxy**. 
1. Na karcie **Serwer serwera proxy** określ adres IP serwera proxy lub nazwę i numer portu, który będzie używany do komunikowania się z serwerem proxy. Wprowadź wartość w `http://<proxyURL>:<proxyport>`formacie . 
1. Wybierz **pozycję Recenzja + wygeneruj**.

1. Na karcie **Recenzja + generowanie** przejrzyj informacje podsumowujące, a następnie wybierz pozycję **Pobierz**. Jeśli nadal musisz wprowadzić zmiany, **wybierz**poprzedni .

## <a name="install-and-validate-the-agent-on-windows"></a>Instalowanie i sprawdzanie poprawności agenta w systemie Windows

### <a name="install-manually"></a>Instalowanie ręczne

Agent połączonego komputera można zainstalować ręcznie, uruchamiając pakiet Instalatora Windows *AzureConnectedMachineAgent.msi*. 

> [!NOTE]
> * Aby zainstalować lub odinstalować agenta, musisz mieć uprawnienia *administratora.*
> * Najpierw należy pobrać i skopiować pakiet Instalatora do folderu na serwerze docelowym lub z udostępnionego folderu sieciowego. Jeśli pakiet Instalatora zostanie uruchomiony bez żadnych opcji, zostanie uruchomiony kreator instalacji, który można wykonać, aby zainstalować agenta interaktywnie.

Jeśli komputer musi komunikować się za pośrednictwem serwera proxy do usługi, po zainstalowaniu agenta należy uruchomić polecenie, które jest opisane w dalszej części artykułu. Spowoduje to ustawienie zmiennej `https_proxy`środowiskowej systemu serwera proxy .

Jeśli nie znasz opcji wiersza polecenia dla pakietów Instalatora Windows, zapoznaj się ze [standardowymi opcjami wiersza polecenia msiexec](https://docs.microsoft.com/windows/win32/msi/standard-installer-command-line-options) i [opcjami wiersza polecenia Msiexec](https://docs.microsoft.com/windows/win32/msi/command-line-options).

Na przykład uruchom program instalacyjny z parametrem, `/?` aby przejrzeć opcję pomocy i szybkiego odwołania. 

```dos
msiexec.exe /i AzureConnectedMachineAgent.msi /?
```

Aby zainstalować agenta w trybie dyskretnym i utworzyć plik dziennika instalacji w folderze, `C:\Support\Logs` uruchom następujące polecenie.

```dos
msiexec.exe /i AzureConnectedMachineAgent.msi /qn /l*v "C:\Support\Logs\Azcmagentsetup.log"
```

Pliki dla agenta Połączonego komputera są instalowane domyślnie w *C:\Program Files\AzureConnectedMachineAgent*. Jeśli agent nie uruchamia się po zakończeniu instalacji, sprawdź dzienniki, aby uzyskać szczegółowe informacje o błędzie. Katalog dziennika to *%Programfiles%\AzureConnectedMachineAgentAgent\logs*.

### <a name="install-with-the-scripted-method"></a>Instalacja za pomocą metody skryptowej

1. Zaloguj się do serwera.

1. Otwórz wiersz polecenia programu PowerShell z podwyższonym poziomem uprawnień.

1. Zmień folder lub udział, do którego skopiowano skrypt, i wykonaj `./OnboardingScript.ps1` go na serwerze, uruchamiając skrypt.

### <a name="configure-the-agent-proxy-setting"></a>Konfigurowanie ustawienia serwera proxy agenta

Aby ustawić zmienną środowiskową serwera proxy, uruchom następujące polecenie:

```powershell
# If a proxy server is needed, execute these commands with the proxy URL and port.
[Environment]::SetEnvironmentVariable("https_proxy", "http://{proxy-url}:{proxy-port}", "Machine")
$env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
# For the changes to take effect, the agent service needs to be restarted after the proxy environment variable is set.
Restart-Service -Name himds
```

>[!NOTE]
>Agent nie obsługuje ustawiania uwierzytelniania serwera proxy w tej wersji zapoznawczej.
>

### <a name="configure-agent-communication"></a>Konfigurowanie komunikacji z agentem

Po zainstalowaniu agenta należy skonfigurować agenta do komunikowania się z usługą Azure Arc, uruchamiając następujące polecenie:

`%ProgramFiles%\AzureConnectedMachineAgent\azcmagent.exe" connect --resource-group "<resourceGroupName>" --tenant-id "<tenantID>" --location "<regionName>" --subscription-id "<subscriptionID>"`

## <a name="install-and-validate-the-agent-on-linux"></a>Zainstaluj i sprawdź poprawność agenta w systemie Linux

Agent Connected Machine dla systemu Linux znajduje się w preferowanym formacie pakietu dla dystrybucji (. Rpm lub . DEB) hostowany w [repozytorium pakietów](https://packages.microsoft.com/)firmy Microsoft . [Pakiet `Install_linux_azcmagent.sh` skryptów powłoki](https://aka.ms/azcmagent) wykonuje następujące akcje:

- Konfiguruje komputer-host, aby pobrać pakiet agenta z packages.microsoft.com.
- Instaluje pakiet dostawcy zasobów hybrydowych.

Opcjonalnie można skonfigurować agenta z informacjami o `--proxy "{proxy-url}:{proxy-port}"` serwerze proxy, dołączając parametr.

Skrypt zawiera również logikę do identyfikowania obsługiwanych i nieobsługiwanych dystrybucji i weryfikuje uprawnienia, które są wymagane do wykonania instalacji. 

Poniższy przykład pobiera agenta i instaluje go:

```bash
# Download the installation package.
wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

# Install the connected machine agent. 
bash ~/Install_linux_azcmagent.sh
```

Aby pobrać i zainstalować agenta, w tym `--proxy` parametr konfigurowania agenta do komunikacji za pośrednictwem serwera proxy, uruchom następujące polecenia:

```bash
# Download the installation package.
wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

# Install the connected machine agent. 
bash ~/Install_linux_azcmagent.sh --proxy "{proxy-url}:{proxy-port}"
```

### <a name="configure-the-agent-communication"></a>Konfigurowanie komunikacji agenta

Po zainstalowaniu agenta skonfiguruj go do komunikowania się z usługą Azure Arc, uruchamiając następujące polecenie:

`/opt/azcmagent/bin/azcmagent.exe" connect --resource-group "<resourceGroupName>" --tenant-id "<tenantID>" --location "<regionName>" --subscription-id "<subscriptionID>"`

## <a name="verify-the-connection-with-azure-arc"></a>Weryfikowanie połączenia z programem Azure Arc

Po zainstalowaniu agenta i skonfigurowaniu go do łączenia się z programem Azure Arc dla serwerów (wersja zapoznawcza) przejdź do witryny Azure Portal, aby sprawdzić, czy serwer został pomyślnie połączony. Wyświetlanie maszyn w [witrynie Azure portal](https://aka.ms/hybridmachineportal).

![Pomyślne połączenie z serwerem](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak zarządzać komputerem przy użyciu [usługi Azure Policy](../../governance/policy/overview.md), dla takich rzeczy, jak [konfiguracja gościa](../../governance/policy/concepts/guest-configuration.md)maszyny wirtualnej, sprawdzanie, że maszyna zgłasza się do oczekiwanego obszaru roboczego usługi Log Analytics, włącz monitorowanie za pomocą [usługi Azure Monitor za pomocą maszyn wirtualnych](../../azure-monitor/insights/vminsights-enable-at-scale-policy.md)i wiele więcej.

- Dowiedz się więcej o [agencie usługi Log Analytics](../../azure-monitor/platform/log-analytics-agent.md). Agent analizy dzienników dla systemów Windows i Linux jest wymagany, gdy chcesz aktywnie monitorować system operacyjny i obciążenia uruchomione na komputerze, zarządzać nim przy użyciu umnień umowniczych automatyzacji lub rozwiązań, takich jak zarządzanie aktualizacjami, lub korzystać z innych usług platformy Azure Security [Center.](../../security-center/security-center-intro.md)
