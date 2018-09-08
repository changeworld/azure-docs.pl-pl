---
title: Wdrażanie lokalnego agenta i testowania obrazów maszyn wirtualnych w usłudze Azure Stack weryfikacji jako usługa | Dokumentacja firmy Microsoft
description: Wdrażanie lokalnego agenta i testowania obrazów maszyn wirtualnych w celu weryfikacji usługi Azure Stack jako usługa.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 78136ab00dcba2f8a99df36ba99d384b49995882
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/07/2018
ms.locfileid: "44159218"
---
# <a name="deploy-the-local-agent-and-test-virtual-machines"></a>Wdrażanie lokalnego maszyn wirtualnych agenta i testowania

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Dowiedz się, jak za pomocą weryfikacji jako lokalny agent usługi (VaaS) sprawdź, czy sprzęt. Agent lokalny należy wdrożyć rozwiązania usługi Azure Stack, weryfikowany przed uruchomieniem testów sprawdzania poprawności.

> [!Note]  
> Upewnij się, czy maszyny, w którym działa agent lokalny nie utracą dostęp do Internetu. Komputer musi być dostępna dla użytkowników, którzy są autoryzowane do używania usługi Azure Stack VaaS.

Aby przetestować maszyn wirtualnych:

1. Zainstaluj lokalnego agenta
2. Wstrzykiwanie błędów w systemie
3. Uruchom agenta lokalnego

## <a name="download-and-start-the-local-agent"></a>Pobierz i zacznij agent lokalny

Pobierz agenta na komputerze, który spełnia wymagania wstępne w Twoim centrum danych, który nie jest częścią systemu Azure Stack, ale taki, który ma dostęp do wszystkich punktów końcowych usługi Azure Stack.

### <a name="machine-prerequisites"></a>Wymagania wstępne dotyczące maszyny

Sprawdź, że Twoja maszyna spełnia następujące kryteria:

- Dostęp do wszystkich punktów końcowych usługi Azure Stack
- .NET 4.6 i PowerShell 5.0 zainstalowany
- Co najmniej 8 GB pamięci RAM
- Co najmniej 8 rdzeniami
- Minimalne 200 GB miejsce na dysku
- Czasu trwania stanu stabilnego łączności sieciowej z Internetem

Usługa Azure Stack jest systemie poddawanym testowi. Komputer nie powinien być częścią usługi Azure Stack lub hostowanych w chmurze Azure Stack.

### <a name="download-and-install-the-agent"></a>Pobierz i zainstaluj agenta

1. Otwórz program Windows PowerShell w wierszu podwyższonym poziomem uprawnień na komputerze, którego używasz do uruchamiania testów.
2. Uruchom następujące polecenie, aby pobrać agenta lokalnego:

    ```PowerShell  
        Invoke-WebRequest -Uri "https://storage.azurestackvalidation.com/packages/Microsoft.VaaSOnPrem.TaskEngineHost.3.2.0.nupkg" -outfile "OnPremAgent.zip"
        Expand-Archive -Path ".\OnPremAgent.zip" -DestinationPath VaaSOnPremAgent.3.2.0 -Force
        Set-Location VaaSOnPremAgent.3.2.0\lib\net46
    ````

3. Uruchom następujące polecenie, aby zainstalować zależności agent lokalny:

    ```PowerShell  
        $ServiceAdminCreds = New-Object System.Management.Automation.PSCredential "<aadServiceAdminUser>", (ConvertTo-SecureString "<aadServiceAdminPassword>" -AsPlainText -Force)
        Import-Module .\VaaSPreReqs.psm1 -Force
        Install-VaaSPrerequisites -AadTenantId <AadTenantId> `
        -ServiceAdminCreds <ServiceAdminCreds> `
        -ArmEndpoint https://adminmanagement.<ExternalFqdn> `
        -Region <Region>
    ````

    **Parametry**

    | Parametr | Opis |
    | --- | --- |
    | aadServiceAdminUser | Użytkownik Administrator globalny dla dzierżawy usługi Azure AD. Na przykład może być, vaasadmin@contoso.onmicrosoft.com. |
    | aadServiceAdminPassword | Hasło administratora globalnego. |
    | AadTenantId | Zarejestrowany identyfikator dzierżawy usługi Azure AD dla konta platformy Azure ze sprawdzaniem poprawności jako usługa. |
    | ExternalFqdn | Możesz uzyskać w pełni kwalifikowaną nazwę domeny, z pliku konfiguracji. Aby uzyskać instrukcje, zobacz [parametry do weryfikacji jako usługi Azure Stack testu](azure-stack-vaas-parameters-test.md). |
    | Region | Region dzierżawy usługi Azure AD. |

Polecenie spowoduje pobranie obrazu publicznego repozytorium (PIR) obrazu (systemu operacyjnego. wirtualny dysk twardy) i skopiuj z usługi Azure blob storage do magazynu usługi Azure Stack. 

![Pobierz wstępnie wymagane składniki](media/installingprereqs.png)

> [!Note]  
> Jeśli występują zbyt wolnego połączenia sieciowego podczas pobierania tych obrazów, pobierz je oddzielnie do lokalnego udziału i określ parametr **- LocalPackagePath** *FileShareOrLocalPath*. Możesz znaleźć więcej wskazówek na pobieranie PIR w sekcji [połączenia powolnej sieci uchwyt](azure-stack-vaas-troubleshoot.md#handle-slow-network-connectivity) z [Rozwiązywanie problemów z weryfikacji jako usługa](azure-stack-vaas-troubleshoot.md).

## <a name="fault-injection"></a>Technika wstrzykiwania błędów

Firma Microsoft zaprojektowała usługi Azure Stack, aby zapewnić elastyczność i tolerować wiele typów błędów sprzętu i oprogramowania. Technika wstrzykiwania błędów zwiększa się liczba błędów w systemie. Ten wzrost pomaga wcześniej odkrywanie problemów, aby zmniejszyć liczbę zdarzeń, które doprowadzić do wyłączenia systemu.

Uruchom następujące polecenia, aby wstawić błędów do systemu.

1. Otwórz program Windows PowerShell w wierszu podwyższonym poziomem uprawnień.

2. Uruchom następujące polecenie:

    ```PowerShell  
        Import-Module .\VaaSPreReqs.psm1 -Force
        Install-ServiceFabricSDK Install-ServiceFabricSDK
    ```

## <a name="run-the-agent"></a>Uruchom agenta

1. Otwórz program Windows PowerShell w wierszu podwyższonym poziomem uprawnień.

2. Uruchom następujące polecenie:

    ````PowerShell  
    .\Microsoft.VaaSOnPrem.TaskEngineHost.exe -u <VaaSUserId> -t <VaaSTenantId>
    ````

      **Parametry**  
    
    | Parametr | Opis |
    | --- | --- |
    | VaaSUserId | Identyfikator użytkownika użyte do zalogowania się do portalu VaaS (na przykład UserName@Contoso.com) |
    | VaaSTenantId | Zarejestrowany identyfikator dzierżawy usługi Azure AD dla konta platformy Azure ze sprawdzaniem poprawności jako usługa. |

    > [!Note]  
    > Po uruchomieniu agenta, bieżący katalog roboczy musi być lokalizacja pliku wykonywalnego, host aparatu zadań **Microsoft.VaaSOnPrem.TaskEngineHost.exe.**

Jeśli widzisz błędy raportowane następnie lokalnego agenta zakończyła się pomyślnie. W oknie konsoli zostanie wyświetlony następujący tekst przykładowy.

`Heartbeat Callback at 11/8/2016 4:45:38 PM`

![Uruchomiono agenta](media/startedagent.png)

Agent jest unikatowo identyfikowane przez nazwę. Domyślnie używa nazwę domeny w pełni kwalifikowaną nazwę (FQDN) komputer, z której zostało uruchomione. Należy zminimalizować okno, aby uniknąć wszelkich przypadkowym kliknięć w oknie jako zmiana fokusu wstrzymuje wszystkie inne czynności.

## <a name="next-steps"></a>Kolejne kroki

- [Weryfikowanie nowych rozwiązań usługi Azure Stack](azure-stack-vaas-validate-solution-new.md)  
- Jeśli masz powolne lub sporadycznych łączności z Internetem, możesz pobrać obraz PIR. Aby uzyskać więcej informacji, zobacz [połączenia powolnej sieci uchwyt](azure-stack-vaas-troubleshoot.md#handle-slow-network-connectivity).
- Aby dowiedzieć się więcej na temat [walidacji usługi Azure Stack jako usługa](https://docs.microsoft.com/azure/azure-stack/partner).
