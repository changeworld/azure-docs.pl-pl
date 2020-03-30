---
title: Instalowanie agenta podłączonego komputera przy użyciu programu Windows PowerShell DSC
description: W tym artykule dowiesz się, jak połączyć maszyny z platformą Azure przy użyciu usługi Azure Arc dla serwerów (w wersji zapoznawczej) przy użyciu programu Windows PowerShell DSC.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 03/12/2020
ms.topic: conceptual
ms.openlocfilehash: 1fb64463b0372202adb04c2deb304c389c7773b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79164685"
---
# <a name="how-to-install-the-connected-machine-agent-using-windows-powershell-dsc"></a>Jak zainstalować agenta podłączonego komputera przy użyciu programu Windows PowerShell DSC

Korzystając z [konfiguracji żądanego stanu programu Windows PowerShell](https://docs.microsoft.com/powershell/scripting/dsc/getting-started/winGettingStarted?view=powershell-7) (DSC) można zautomatyzować instalację i konfigurację oprogramowania dla komputera z systemem Windows. W tym artykule opisano sposób instalowania programu Azure Arc dla serwerów Connected Machine agenta na hybrydowych komputerach z systemem Windows za pomocą dsc.

## <a name="requirements"></a>Wymagania

- Program Windows PowerShell w wersji 4.0 lub nowszej

- Moduł [DSC usługi AzureConnectedMachineDsc](https://www.powershellgallery.com/packages/AzureConnectedMachineDsc/1.0.1.0)

- Podmiot usługi do łączenia maszyn do usługi Azure Arc dla serwerów nieinterakcyjnie. Wykonaj kroki opisane w sekcji [Tworzenie jednostki usługi dla dołączania na dużą skalę,](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) jeśli nie utworzono już jednostki usługi dla arc dla serwerów.

## <a name="install-the-connectedmachine-dsc-module"></a>Instalowanie modułu ConnectedMachine DSC

1. Aby ręcznie zainstalować moduł, pobierz kod źródłowy i rozpakowywać zawartość `$env:ProgramFiles\WindowsPowerShell\Modules folder`katalogu projektu do pliku . Możesz też uruchomić następujące polecenie, aby zainstalować je z galerii programu PowerShell przy użyciu programu PowerShellGet (w programie PowerShell 5.0):

    ```powershell
    Find-Module -Name AzureConnectedMachineDsc -Repository PSGallery | Install-Module
    ```

2. Aby potwierdzić instalację, uruchom następujące polecenie i upewnij się, że są dostępne zasoby dsc podłączonego komputera platformy Azure.

    ```powershell
    Get-DscResource -Module AzureConnectedMachineDsc
    ```

   Na danych wyjściowych powinien być widoczny coś podobnego do następującego:

   ![Potwierdzenie przykładu instalacji modułu DSC podłączonej maszyny](./media/onboard-dsc/confirm-module-installation.png)

## <a name="install-the-agent-and-connect-to-azure"></a>Instalowanie agenta i łączenie się z platformą Azure

Zasoby w tym module są przeznaczone do zarządzania konfiguracją agenta połączonego komputera platformy Azure. W zestawie znajduje się również `AzureConnectedMachineAgent.ps1`skrypt programu `AzureConnectedMachineDsc\examples` PowerShell, który znajduje się w folderze. Używa zasobów społeczności do automatyzacji pobierania i instalacji oraz nawiązania połączenia z programem Azure Arc. Ten skrypt wykonuje podobne kroki opisane w connect maszyn hybrydowych do platformy Azure z artykułu [witryny Azure portal.](onboard-portal.md)

Jeśli komputer musi komunikować się za pośrednictwem serwera proxy do usługi, po zainstalowaniu agenta należy uruchomić polecenie, które jest opisane [w tym miejscu](onboard-portal.md#configure-the-agent-proxy-setting). Spowoduje to ustawienie zmiennej `https_proxy`środowiskowej systemu serwera proxy . Zamiast uruchamiać polecenie ręcznie, można wykonać ten krok za pomocą DSC przy użyciu modułu [ComputeManagementDsc.](https://www.powershellgallery.com/packages/ComputerManagementDsc/6.0.0.0)

>[!NOTE]
>Aby umożliwić uruchamianie dsc, system Windows musi być skonfigurowany do odbierania zdalnych poleceń programu PowerShell, nawet gdy używasz konfiguracji hosta lokalnego. Aby łatwo skonfigurować środowisko poprawnie, wystarczy uruchomić `Set-WsManQuickConfig -Force` w podwyższonym rozsyłaniu terminalu programu PowerShell.
>

Dokumenty konfiguracyjne (pliki MOF) można zastosować `Start-DscConfiguration` do urządzenia za pomocą polecenia cmdlet.

Poniżej przedstawiono parametry, które można przekazać do skryptu programu PowerShell do użycia.

- `TenantId`: Unikatowy identyfikator (GUID), który reprezentuje dedykowane wystąpienie usługi Azure AD.

- `SubscriptionId`: Identyfikator subskrypcji (GUID) subskrypcji platformy Azure, w której mają być na komputerze.

- `ResourceGroup`: Nazwa grupy zasobów, do której mają należeć podłączone komputery.

- `Location`: Zobacz [obsługiwane regiony platformy Azure](overview.md#supported-regions). Ta lokalizacja może być taka sama lub inna, jak lokalizacja grupy zasobów.

- `Tags`: Tablica ciągów tagów, które powinny być stosowane do podłączonego zasobu komputera.

- `Credential`: Obiekt poświadczeń programu PowerShell z **identyfikatorem applicationid** i **hasłem używanym** do rejestrowania maszyn na dużą skalę przy użyciu [jednostki usługi.](onboard-service-principal.md) 

1. W konsoli programu PowerShell przejdź do folderu, w `.ps1` którym został zapisany plik.

2. Uruchom następujące polecenia programu PowerShell w celu skompilowania dokumentu MOF (aby uzyskać informacje dotyczące kompilowania konfiguracji DSC, zobacz [Konfiguracje DSC:](https://docs.microsoft.com/powershell/scripting/dsc/configurations/configurations?view=powershell-7)

    ```powershell
    .\`AzureConnectedMachineAgent.ps1 -TenantId <TenantId GUID> -SubscriptionId <SubscriptionId GUID> -ResourceGroup '<ResourceGroupName>' -Location '<LocationName>' -Tags '<Tag>' -Credential <psCredential>
    ```

3. Spowoduje to `localhost.mof file` utworzenie w nowym `C:\dsc`folderze o nazwie .

Po zainstalowaniu agenta i skonfigurowaniu go do łączenia się z programem Azure Arc dla serwerów (wersja zapoznawcza) przejdź do witryny Azure Portal, aby sprawdzić, czy serwer został pomyślnie połączony. Wyświetlanie maszyn w [witrynie Azure portal](https://aka.ms/hybridmachineportal).

## <a name="adding-to-existing-configurations"></a>Dodawanie do istniejących konfiguracji

Ten zasób można dodać do istniejących konfiguracji DSC do reprezentowania konfiguracji end-to-end dla komputera. Na przykład można dodać ten zasób do konfiguracji, która ustawia bezpieczne ustawienia systemu operacyjnego.

[Moduł CompsiteResource](https://www.powershellgallery.com/packages/compositeresource/0.4.0) z galerii programu PowerShell może służyć do tworzenia [zasobu złożonego](https://docs.microsoft.com/powershell/scripting/dsc/resources/authoringResourceComposite?view=powershell-7) przykładowej konfiguracji, aby jeszcze bardziej uprościć łączenie konfiguracji.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak zarządzać komputerem przy użyciu [usługi Azure Policy](../../governance/policy/overview.md), dla takich rzeczy, jak [konfiguracja gościa](../../governance/policy/concepts/guest-configuration.md)maszyny wirtualnej, sprawdzanie, że maszyna zgłasza się do oczekiwanego obszaru roboczego usługi Log Analytics, włącz monitorowanie za pomocą [usługi Azure Monitor za pomocą maszyn wirtualnych](../../azure-monitor/insights/vminsights-enable-at-scale-policy.md)i wiele więcej.

- Dowiedz się więcej o [agencie usługi Log Analytics](../../azure-monitor/platform/log-analytics-agent.md). Agent analizy dzienników dla systemów Windows i Linux jest wymagany, gdy chcesz aktywnie monitorować system operacyjny i obciążenia uruchomione na komputerze, zarządzać nim przy użyciu umnień umowniczych automatyzacji lub rozwiązań, takich jak zarządzanie aktualizacjami, lub korzystać z innych usług platformy Azure Security [Center.](../../security-center/security-center-intro.md)