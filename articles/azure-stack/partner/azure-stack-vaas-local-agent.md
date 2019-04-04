---
title: Wdrażanie lokalnego agenta | Dokumentacja firmy Microsoft
description: Wdrażanie lokalnego agenta usługi Azure Stack weryfikacji jako usługa.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: cfea454b20b010148eba063ec724e55134944ac3
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58482933"
---
# <a name="deploy-the-local-agent"></a>Wdrażanie lokalnego agenta

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Dowiedz się, jak używać weryfikacji jako lokalny agent usługi (VaaS) do uruchomienia testów walidacyjnych. Agent lokalny należy wdrożyć przed uruchomieniem testów sprawdzania poprawności.

> [!Note]  
> Upewnij się, komputera, na którym jest uruchomiony agent lokalny nie utracić dostęp ruchu wychodzącego z Internetem. Ta maszyna powinny być dostępne tylko dla użytkowników, którzy są autoryzowane do używania VaaS w imieniu swojej dzierżawy.

Wdrażanie lokalnego agenta:

1. Zainstaluj lokalnego agenta.
2. Wykonaj testy z poprawnością.
3. Uruchom agenta lokalnego.

## <a name="download-and-start-the-local-agent"></a>Pobierz i zacznij agent lokalny

Pobierz agenta na komputerze, który spełnia wymagania wstępne w centrum danych i ma dostęp do wszystkich punktów końcowych usługi Azure Stack. Ta maszyna nie powinna być częścią systemu Azure Stack lub hostowanych w chmurze Azure Stack.

### <a name="machine-prerequisites"></a>Wymagania wstępne dotyczące maszyny

Sprawdź, że Twoja maszyna spełnia następujące kryteria:

- Dostęp do wszystkich punktów końcowych usługi Azure Stack
- .NET 4.6 i PowerShell 5.0 zainstalowany
- Co najmniej 8 GB pamięci RAM
- Co najmniej 8 rdzeniami
- Minimalne 200 GB miejsce na dysku
- Czasu trwania stanu stabilnego łączności sieciowej z Internetem

### <a name="download-and-install-the-agent"></a>Pobierz i zainstaluj agenta

1. Otwórz program Windows PowerShell w wierszu podwyższonym poziomem uprawnień na komputerze, którego używasz do uruchamiania testów.
2. Uruchom następujące polecenie, aby pobrać agenta lokalnego:

    ```powershell
    Invoke-WebRequest -Uri "https://storage.azurestackvalidation.com/packages/Microsoft.VaaSOnPrem.TaskEngineHost.latest.nupkg" -outfile "OnPremAgent.zip"
    Expand-Archive -Path ".\OnPremAgent.zip" -DestinationPath VaaSOnPremAgent -Force
    Set-Location VaaSOnPremAgent\lib\net46
    ```

3. Uruchom następujące polecenie, aby zainstalować zależności agent lokalny:

    ```powershell
    $ServiceAdminCreds = New-Object System.Management.Automation.PSCredential "<aadServiceAdminUser>", (ConvertTo-SecureString "<aadServiceAdminPassword>" -AsPlainText -Force)
    Import-Module .\VaaSPreReqs.psm1 -Force
    Install-VaaSPrerequisites -AadTenantId $AadTenantId `
                              -ServiceAdminCreds $ServiceAdminCreds `
                              -ArmEndpoint https://adminmanagement.$ExternalFqdn `
                              -Region $Region
    ```

    **Parametry**

    | Parametr | Opis |
    | --- | --- |
    | aadServiceAdminUser | Użytkownik Administrator globalny dla dzierżawy usługi Azure AD. Na przykład może być, vaasadmin@contoso.onmicrosoft.com. |
    | aadServiceAdminPassword | Hasło administratora globalnego. |
    | AadTenantId | Zarejestrowany identyfikator dzierżawy usługi Azure AD dla konta platformy Azure ze sprawdzaniem poprawności jako usługa. |
    | ExternalFqdn | Możesz uzyskać w pełni kwalifikowaną nazwę domeny, z pliku konfiguracji. Aby uzyskać instrukcje, zobacz [wspólnych parametrów przepływów pracy w usługi Azure Stack weryfikacji jako usługa](azure-stack-vaas-parameters.md). |
    | Region | Region dzierżawy usługi Azure AD. |

Polecenie spowoduje pobranie obrazu publicznego repozytorium (PIR) obrazu (systemu operacyjnego. wirtualny dysk twardy) i skopiuj z usługi Azure blob storage do magazynu usługi Azure Stack.

![Pobierz wstępnie wymagane składniki](media/installingprereqs.png)

> [!Note]
> Jeśli występują zbyt wolnego połączenia sieciowego podczas pobierania tych obrazów, pobierz je oddzielnie do lokalnego udziału i określ parametr **- LocalPackagePath** *FileShareOrLocalPath*. Możesz znaleźć więcej wskazówek na pobieranie PIR w sekcji [połączenia powolnej sieci uchwyt](azure-stack-vaas-troubleshoot.md#handle-slow-network-connectivity) z [Rozwiązywanie problemów z weryfikacji jako usługa](azure-stack-vaas-troubleshoot.md).

## <a name="checks-before-starting-the-tests"></a>Sprawdzanie przed uruchomieniem testów

Testy uruchamiania operacji zdalnych. Komputer który uruchamia testy muszą mieć dostęp do punktów końcowych usługi Azure Stack, w przeciwnym razie testy nie będą działać. Jeśli używasz lokalnego agenta VaaS korzystać z maszyn, których będzie uruchamiany agent. Aby sprawdzić, czy komputer ma dostęp do punktów końcowych usługi Azure Stack, uruchamiając następujące testy:

1. Sprawdź, czy można połączyć podstawowy identyfikator URI. Otwórz wiersz polecenia lub powłokę bash, i uruchom następujące polecenie, zastępując `<EXTERNALFQDN>` nazwą FQDN zewnętrznego środowiska:

    ```bash
    nslookup adminmanagement.<EXTERNALFQDN>
    ```

2. Otwórz przeglądarkę internetową i przejdź do `https://adminportal.<EXTERNALFQDN>` w celu sprawdzenia, czy można połączyć z portalu MAS.

3. Zalogować się przy użyciu usługi Azure AD usługi administratora nazwy i hasła podanych wartości podczas tworzenia przebiegu testu.

4. Sprawdź kondycję systemu, uruchamiając **AzureStack testu** polecenia cmdlet programu PowerShell, zgodnie z opisem w [uruchomienie testu poprawności dla usługi Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-diagnostic-test). Rozwiąż wszystkie ostrzeżenia i błędy przed uruchomieniem wszystkie testy.

## <a name="run-the-agent"></a>Uruchom agenta

1. Otwórz program Windows PowerShell w wierszu podwyższonym poziomem uprawnień.

2. Uruchom następujące polecenie:

    ```powershell
    .\Microsoft.VaaSOnPrem.TaskEngineHost.exe -u <VaaSUserId> -t <VaaSTenantId>
    ```

      **Parametry**  

    | Parametr | Opis |
    | --- | --- |
    | VaaSUserId | Identyfikator użytkownika użyte do zalogowania się do portalu VaaS (na przykład nazwa użytkownika\@Contoso.com) |
    | VaaSTenantId | Zarejestrowany identyfikator dzierżawy usługi Azure AD dla konta platformy Azure ze sprawdzaniem poprawności jako usługa. |

    > [!Note]  
    > Po uruchomieniu agenta, bieżący katalog roboczy musi być lokalizacja pliku wykonywalnego, host aparatu zadań **Microsoft.VaaSOnPrem.TaskEngineHost.exe.**

Jeśli widzisz błędy raportowane następnie lokalnego agenta zakończyła się pomyślnie. W oknie konsoli zostanie wyświetlony następujący tekst przykładowy.

`Heartbeat Callback at 11/8/2016 4:45:38 PM`

![Uruchomiono agenta](media/startedagent.png)

Agent jest unikatowo identyfikowane przez nazwę. Domyślnie używa nazwę domeny w pełni kwalifikowaną nazwę (FQDN) komputer, z której zostało uruchomione. Należy zminimalizować okno, aby uniknąć wszelkich przypadkowym wybiera w oknie jako zmiana fokusu wstrzymuje wszystkie inne czynności.

## <a name="next-steps"></a>Kolejne kroki

- [Rozwiązywanie problemów z weryfikacji jako usługa](azure-stack-vaas-troubleshoot.md)
- [Sprawdzanie poprawności jako kluczowe pojęcia dotyczące usługi](azure-stack-vaas-key-concepts.md)
- [Szybki start: Użyj sprawdzania poprawności jako portal usługi, do zaplanowania Twojego pierwszego testu](azure-stack-vaas-schedule-test-pass.md)