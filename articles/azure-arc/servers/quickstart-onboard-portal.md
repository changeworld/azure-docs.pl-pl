---
title: Łączenie maszyn hybrydowych z platformą Azure z poziomu Azure Portal
description: W tym artykule dowiesz się, jak zainstalować agenta i połączyć maszyny z platformą Azure przy użyciu usługi Azure ARC dla serwerów (wersja zapoznawcza) z Azure Portal.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 01/29/2020
ms.custom: mvc
ms.topic: quickstart
ms.openlocfilehash: 165ed8d0aad7011f1ea71ff870ee4629f1f12613
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76898606"
---
# <a name="connect-hybrid-machines-to-azure-from-the-azure-portal"></a>Łączenie maszyn hybrydowych z platformą Azure z poziomu Azure Portal

Możesz włączyć usługę Azure ARC dla serwerów (wersja zapoznawcza) dla jednej lub małej liczby maszyn z systemem Windows lub Linux w środowisku, wykonując ręcznie zestaw kroków lub korzystając z zautomatyzowanej metody przez uruchomienie skryptu szablonu, który udostępniamy. Ten skrypt automatyzuje pobieranie i Instalowanie obu agentów.

Ta metoda instalacji wymaga uprawnień administratora na komputerze, aby zainstalować i skonfigurować agenta. W systemie Linux przy użyciu konta głównego i w systemie Windows należysz do lokalnej grupy administratorów.

Przed rozpoczęciem należy zapoznać się z wymaganiami [wstępnymi](overview.md#prerequisites) i upewnić się, że Twoja subskrypcja i zasoby spełniają wymagania.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="generate-install-script-from-the-azure-portal"></a>Generuj skrypt instalacji na podstawie Azure Portal

W Azure Portal jest dostępny skrypt służący do automatyzowania pobierania, instalacji i ustanawiania połączenia z usługą Azure Arc. W poniższych krokach opisano sposób wykonania tego procesu.

1. W przeglądarce Uruchom [https://aka.ms/hybridmachineportal](https://aka.ms/hybridmachineportal).

2. Na stronie **automaty na platformie Azure** wybierz pozycję **+ Dodaj** w lewym górnym rogu lub wybierz opcję **Utwórz Machine-Azure Arc** w dolnej części środkowego okienka. 

3. Na stronie **Wybierz metodę** wybierz pozycję z kafelka **Dodawanie maszyn przy użyciu interakcyjnego skryptu** **Generuj skrypt**.

4. Na stronie **Generowanie skryptu** wybierz subskrypcję i grupę zasobów, w której maszyna ma być zarządzana na platformie Azure. Wybierz lokalizację platformy Azure, w której będą przechowywane metadane maszynowe.

    >[!NOTE]
    >Usługa Azure ARC dla serwerów (wersja zapoznawcza) obsługuje tylko następujące regiony:
    >- WestUS2
    >- WestEurope
    >- WestAsia
    >

5. Na stronie **Generuj skrypt** na liście rozwijanej **system operacyjny** wybierz odpowiedni system operacyjny, na którym będzie uruchamiany skrypt.

6. Jeśli komputer komunikuje się za pomocą serwera proxy w celu nawiązania połączenia z Internetem, wybierz opcję **Dalej: serwer proxy >** . Na karcie **serwer proxy** Określ adres IP lub nazwę i numer portu serwera proxy, który będzie używany przez maszynę do komunikacji z serwerem proxy. Wprowadź wartość w formacie `http://<proxyURL>:<proxyport>`. Po zakończeniu wybierz pozycję **Przegląd + generowanie**.  W przeciwnym razie wybierz pozycję **Przegląd + Generuj** , aby wykonać kroki.

7. Na karcie **Recenzja + generowanie** Przejrzyj informacje podsumowujące, a następnie wybierz pozycję **Pobierz**. W przeciwnym razie, jeśli musisz wprowadzić zmiany, możesz wybrać pozycję **poprzedni**.

## <a name="install-and-validate-the-agent-on-windows"></a>Instalowanie i weryfikowanie agenta w systemie Windows

### <a name="install-manually"></a>Instalowanie ręczne

Agenta połączonego maszyny można zainstalować ręcznie, uruchamiając pakiet instalacyjny Instalator Windows `AzureConnectedMachineAgent.msi` po jego pobraniu i skopiowaniu do folderu na serwerze docelowym lub z udostępnionego folderu sieciowego. Jeśli zostanie uruchomiony pakiet Instalatora bez żadnych opcji, zostanie uruchomiony Kreator instalacji, który można wykonać, aby zainstalować agenta interaktywnie.

>[!NOTE]
>*Administrator* uprawnienia są wymagane do zainstalowania lub odinstalowania agenta.

Jeśli komputer musi komunikować się z usługą za pomocą serwera proxy, po zainstalowaniu agenta należy uruchomić polecenie opisane w poniższej sekcji, aby ustawić zmienną środowiskową systemu serwera proxy `https_proxy`.

W poniższej tabeli wymieniono parametry, które są obsługiwane przez Instalatora agenta z wiersza polecenia.

| Parametr | Opis |
|:--|:--|
| /? | Zwraca listę opcji wiersza polecenia. |
| /S | Wykonanie dyskretnej instalacji bez interakcji użytkownika. |

Na przykład, aby uruchomić program instalacyjny z parametrem `/?`, wprowadź `msiexec.exe /i AzureConnectedMachineAgent.msi /?`.

Pliki dla agenta połączonej maszyny są domyślnie instalowane w *katalogu C:\Program Files\AzureConnectedMachineAgent* . Jeśli uruchomienie agenta nie powiedzie się po zakończeniu instalacji, zapoznaj się z dziennikami, aby uzyskać szczegółowe informacje o błędzie. Katalog dziennika to *%ProgramFiles%\AzureConnectedMachineAgentAgent\logs*.

### <a name="install-using-scripted-method"></a>Zainstaluj przy użyciu metody skryptowej

1. Zaloguj się na serwerze.

2. Otwórz wiersz polecenia programu PowerShell z podwyższonym poziomem uprawnień.

3. Przejdź do folderu lub udziału, do którego skopiowano skrypt, i wykonaj go na serwerze, uruchamiając polecenie `./OnboardingScript.ps1`.

### <a name="configure-agent-proxy-setting"></a>Skonfiguruj ustawienie serwera proxy agenta

Uruchom następujące polecenie, aby ustawić zmienną środowiskową serwera proxy.

```powershell
# If a proxy server is needed, execute these commands with proxy URL and port
[Environment]::SetEnvironmentVariable("https_proxy", "http://{proxy-url}:{proxy-port}", "Machine")
$env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
# The agent service needs to be restarted after the proxy environment variable is set in order for the changes to take effect.
Restart-Service -Name himds
```

>[!NOTE]
>Agent nie obsługuje ustawiania uwierzytelniania serwera proxy w tej wersji zapoznawczej.
>

### <a name="configure-agent-communication"></a>Konfigurowanie komunikacji agenta

Po zainstalowaniu agenta należy skonfigurować agenta do komunikowania się z usługą Azure ARC, uruchamiając następujące polecenie:

`%ProgramFiles%\AzureConnectedMachineAgent\azcmagent.exe" connect --resource-group "<resourceGroupName>" --tenant-id "<tenantID>" --location "<regionName>" --subscription-id "<subscriptionID>"`

## <a name="install-and-validate-the-agent-on-linux"></a>Instalowanie i weryfikowanie agenta w systemie Linux

Agent połączonej maszyny dla systemu Linux jest dostępny w preferowanym formacie pakietu dla dystrybucji (. RPM lub. DEB) hostowana w [repozytorium pakietu](https://packages.microsoft.com/)firmy Microsoft. Pakiet skryptu powłoki `Install_linux_azcmagent.sh` znajdujący się w [https://aka.ms/azcmagent](https://aka.ms/azcmagent) wykonuje następujące czynności:

- Konfiguruje maszynę hosta do pobrania pakietu agenta z packages.microsoft.com.
- Instaluje pakiet hybrydowego dostawcy zasobów.
- Opcjonalnie można skonfigurować agenta przy użyciu informacji o serwerze proxy, dołączając parametr `--proxy "{proxy-url}:{proxy-port}"`.

Skrypt zawiera również logikę umożliwiającą identyfikację obsługiwanych i nieobsługiwanych dystrybucji oraz weryfikowanie wymaganych uprawnień do przeprowadzenia instalacji. 

Poniższy przykład pobiera agenta i instaluje go bez przeprowadzania żadnych kontroli warunkowej.

```bash
# Download the installation package
wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

# Install the connected machine agent. 
bash ~/Install_linux_azcmagent.sh
```

Aby pobrać i zainstalować agenta, w tym `--proxy` parametr służący do konfigurowania agenta do komunikowania się za pomocą serwera proxy, uruchom następujące polecenia:

```bash
# Download the installation package
wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

# Install the connected machine agent. 
bash ~/Install_linux_azcmagent.sh --proxy "{proxy-url}:{proxy-port}"
```

### <a name="configure-agent-communication"></a>Konfigurowanie komunikacji agenta

Po zainstalowaniu agenta należy skonfigurować agenta do komunikowania się z usługą Azure ARC, uruchamiając następujące polecenie:

`/opt/azcmagent/bin/azcmagent.exe" connect --resource-group "<resourceGroupName>" --tenant-id "<tenantID>" --location "<regionName>" --subscription-id "<subscriptionID>"`

## <a name="verify-connection-with-azure-arc"></a>Weryfikowanie połączenia z usługą Azure Arc

Po wykonaniu kroków w celu zainstalowania agenta i skonfigurowania go w celu nawiązania połączenia z usługą Azure ARC dla serwerów (wersja zapoznawcza) przejdź do Azure Portal, aby sprawdzić, czy serwer został pomyślnie połączony. Możesz wyświetlić maszyny w Azure Portal, odwiedzając [https://aka.ms/hybridmachineportal](https://aka.ms/hybridmachineportal).

![Pomyślne dołączanie](./media/quickstart-onboard/arc-for-servers-successful-onboard.png)

## <a name="clean-up"></a>Czyszczenie

Aby rozłączyć maszynę z usługi Azure ARC dla serwerów (wersja zapoznawcza), należy wykonać następujące czynności.

1. Otwórz usługę Azure ARC dla serwerów (wersja zapoznawcza), odwiedzając [https://aka.ms/hybridmachineportal](https://aka.ms/hybridmachineportal).

2. Wybierz maszynę z listy, kliknij wielokropek (`...`) i wybierz pozycję **Usuń**.

3. Aby odinstalować agenta systemu Windows z komputera, wykonaj następujące czynności:

    1. Zaloguj się na komputerze przy użyciu konta z uprawnieniami administracyjnymi.

    2. W **Panelu sterowania**wybierz **aplet Programy i funkcje**.

    3. W obszarze **programy i funkcje**wybierz pozycję **Agent połączonej maszyny Azure**, wybierz pozycję **Odinstaluj**, a następnie wybierz pozycję **tak**.

        >[!NOTE]
        >Kreatora instalacji agenta można również uruchomić, klikając dwukrotnie pakiet Instalatora **AzureConnectedMachineAgent. msi** .

    Jeśli chcesz, aby skrypt został odinstalowany, możesz użyć poniższego przykładu, który pobierze kod produktu i odinstaluje agenta za pomocą wiersza polecenia msiexec. exe `msiexec /x {Product Code}`. Otwórz Edytor rejestru i poszukaj w kluczu rejestru `HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Uninstall` i Znajdź identyfikator GUID kodu produktu. Następnie można odinstalować agenta przy użyciu polecenia msiexec.

   W poniższym przykładzie przedstawiono Odinstalowywanie agenta.

   ```powershell
   Get-ChildItem -Path HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall | `
   Get-ItemProperty | `
   Where-Object {$_.DisplayName -eq "Azure Connected Machine Agent"} | `
   ForEach-Object {MsiExec.exe /x "$($_.PsChildName)" /qn}
   ```

4. Aby odinstalować agenta systemu Linux, wykonaj następujące polecenie w celu odinstalowania agenta.

   ```bash
   sudo apt purge hybridagent
   ```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Przypisywanie zasad do połączonych maszyn](../../governance/policy/assign-policy-portal.md)
