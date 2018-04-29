---
title: Przy użyciu punktu końcowego uprzywilejowanych w stosie Azure | Dokumentacja firmy Microsoft
description: Przedstawia sposób użycia uprzywilejowanego punktu końcowego (program ten) w stosie Azure (dla operatora stosu Azure).
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: e94775d5-d473-4c03-9f4e-ae2eada67c6c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2018
ms.author: mabrigg
ms.reviewer: fiseraci
ms.openlocfilehash: 9fb928b7cb8e1a83734b64a8b9c19bc3cf3203ba
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="using-the-privileged-endpoint-in-azure-stack"></a>Przy użyciu punktu końcowego uprzywilejowanych w stosie Azure

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

Jako operator stosu Azure należy użyć portalu administratora programu PowerShell lub interfejsów API usługi Azure Resource Manager dla najbardziej codziennych zadań zarządzania. Jednak w przypadku niektórych mniej typowych operacji, należy użyć *uprzywilejowanych punktu końcowego* (program ten). Program ten jest wstępnie skonfigurowane zdalnej konsoli programu PowerShell, która dostarcza wystarczającego możliwości, które ułatwiają wykonywanie wymaganych zadań. Punkt końcowy używa [JEA środowiska PowerShell (tylko tyle Administracja)](https://docs.microsoft.com/powershell/jea/overview) do udostępnienia tylko ograniczony zestaw poleceń cmdlet. Aby dostęp program ten i wywołać ograniczony zestaw poleceń cmdlet, należy użyć konta z niskimi uprawnieniami. Żadnych kont administratora są wymagane. Aby dodatkowo zwiększyć bezpieczeństwo skryptów jest niedozwolone.

Program ten służy do wykonywania następujących zadań:

- Aby wykonywać zadania niskiego poziomu, takie jak [zbierania dzienników diagnostycznych](https://docs.microsoft.com/azure/azure-stack/azure-stack-diagnostics#log-collection-tool).
- Do wykonywania wielu zadań integracji datacenter po wdrożeniu zintegrowane systemów, takich jak dodawanie usług przesyłania dalej systemu nazw domen (DNS) po wdrożeniu konfigurowania integracji programu Microsoft Graph, integracji usługi Active Directory Federation Services (AD FS) certyfikat obracanie itp.
- Aby pracować z obsługą przy uzyskiwaniu dostępu do tymczasowego, wysokiego poziomu do rozwiązywania problemów szczegółowe zintegrowanego systemu.

Program ten rejestruje każda akcja (i jej odpowiednie dane wyjściowe), które można wykonywać w sesji programu PowerShell. Zapewnia to pełna przezroczystość i inspekcji zakończenie operacji. Można zachować te pliki dziennika dla przyszłych inspekcji.

> [!NOTE]
> W usłudze Azure stosu Development Kit (ASDK), można uruchomić niektórych poleceń, które są dostępne w program ten bezpośrednio z sesji programu PowerShell na hoście development kit. Jednak można przetestować niektóre operacje przy użyciu program ten, takich jak zbieranie dzienników, ponieważ jest jedyną wykonywania pewnych operacji w środowisku zintegrowanych systemów.

## <a name="access-the-privileged-endpoint"></a>Dostęp uprzywilejowany punktu końcowego

Program ten jest dostępny za pośrednictwem sesji zdalnej programu PowerShell na maszynie wirtualnej, który hostuje program ten. W ASDK tej maszyny wirtualnej o nazwie **AzS ERCS01**. Jeśli używasz zintegrowanego systemu, istnieją trzy wystąpienia program ten, każda uruchomiona wewnątrz maszyny wirtualnej (*prefiksu*-ERCS01, *prefiksu*-ERCS02, lub *prefiksu*- ERCS03) na różnych hostach odporność na awarie. 

Przed rozpoczęciem tej procedury dla zintegrowany system, upewnij się, że można uzyskać dostępu do program ten, za pomocą adresu IP lub przy użyciu systemu DNS. Po początkowym wdrożeniu stosu Azure program ten są dostępne tylko za pomocą adresu IP ponieważ integracja protokołu DNS nie jest jeszcze skonfigurowany. Dostawca sprzętu OEM zapewnia plik JSON o nazwie **AzureStackStampDeploymentInfo** zawierający adresy IP program ten.


> [!NOTE]
> Ze względów bezpieczeństwa wymagamy, że łączysz się program ten tylko z uruchomionej maszyny wirtualnej ze wzmocnionymi zabezpieczeniami na hoście cyklu życia sprzętu lub dedykowanych, bezpieczne komputera, takich jak [uprzywilejowanego dostępu do stacji roboczej](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations). Nie można modyfikować oryginalną konfigurację sprzętu hosta cyklu życia z oryginalną konfiguracją, takie jak instalowanie nowego oprogramowania, nie powinny być używane do nawiązania połączenia program ten.

1. Ustanowienie relacji zaufania.

    - W systemie zintegrowanego, uruchom następujące polecenie w sesji środowiska Windows PowerShell z podwyższonym poziomem uprawnień do dodania program ten jako zaufanego hosta na uruchomiona na hoście cyklu życia sprzętu lub uprzywilejowanego dostępu do stacji roboczej ze wzmocnionymi zabezpieczeniami maszyny wirtualnej.

      ````PowerShell
        winrm s winrm/config/client '@{TrustedHosts="<IP Address of Privileged Endpoint>"}'
      ````
    - Jeśli korzystasz z ADSK, zaloguj się do rozwoju hosta zestawu.

2. Na uruchomiona na hoście cyklu życia sprzętu lub uprzywilejowanego dostępu do stacji roboczej ze wzmocnionymi zabezpieczeniami maszynie wirtualnej Otwórz sesję programu Windows PowerShell. Uruchom następujące polecenia, aby ustanowić sesję zdalną na maszynie wirtualnej, który hostuje program ten:
 
    - Na zintegrowany system:
      ````PowerShell
        $cred = Get-Credential

        Enter-PSSession -ComputerName <IP_address_of_ERCS> `
          -ConfigurationName PrivilegedEndpoint -Credential $cred
      ````
      `ComputerName` Parametr może być adres IP lub nazwę DNS w jednej z maszyn wirtualnych, które obsługuje program ten. 
    - Jeśli używasz ADSK:
     
      ````PowerShell
        $cred = Get-Credential

        Enter-PSSession -ComputerName azs-ercs01 `
          -ConfigurationName PrivilegedEndpoint -Credential $cred
      ```` 
   Po wyświetleniu monitu użyj następujących poświadczeń:

      - **Nazwa użytkownika**: Określ konto CloudAdmin w formacie  **&lt; *domeny stosu Azure*&gt;\cloudadmin**. (ASDK, nazwa użytkownika jest **azurestack\cloudadmin**.)
      - **Hasło**: wprowadź to samo hasło, które zostało podane podczas instalacji dla konta administratora domeny AzureStackAdmin.

    > [!NOTE]
    > Jeśli nie można nawiązać połączenia z punktem końcowym ERCS, spróbuj wykonać kroki 1 i 2 ponownie przy użyciu adresu IP ERCS maszyny wirtualnej, do którego nie można już próbujesz się połączyć.

3.  Po nawiązaniu połączenia wiersza zmieni się na **[*nazwa adresu IP lub wirtualna ERCS*]: PS >** lub **[azs ercs01]: PS >**, w zależności od środowiska. W tym miejscu, należy uruchomić `Get-Command` Aby wyświetlić listę dostępnych poleceń cmdlet.

    Wiele z tych poleceń cmdlet są przeznaczone tylko dla środowisk zintegrowany system (takich jak polecenia cmdlet związane z integracji centrum danych). W ASDK zostały zatwierdzone następujące polecenia cmdlet:

    - Wyczyść hosta
    - Zamknij PrivilegedEndpoint
    - Zakończ PSSession
    - Get-AzureStackLog
    - Get-AzureStackStampInformation
    - Polecenie GET
    - Get-FormatData
    - Get-Help
    - Get-ThirdPartyNotices
    - Obiekt miary
    - New-CloudAdminUser
    - Domyślna wyjściowego
    - Remove-CloudAdminUser
    - Select-Object
    - Set-CloudAdminUserPassword
    - Test-AzureStack
    - Stop-AzureStack
    - Get-ClusterLog

## <a name="tips-for-using-the-privileged-endpoint"></a>Porady dotyczące używania uprzywilejowanego punktu końcowego 

Jak wspomniano powyżej, program ten jest [PowerShell JEA](https://docs.microsoft.com/powershell/jea/overview) punktu końcowego. Zapewniając warstwy silne zabezpieczenie punktu końcowego JEA zmniejsza niektórych podstawowych funkcji programu PowerShell, takie jak uzupełnianie skryptów lub na karcie. Jeśli spróbujesz dowolnego typu działania skryptu kończy się niepowodzeniem z powodu błędu **ScriptsNotAllowed**. Jest to oczekiwane zachowanie.

Tak na przykład, aby uzyskać listę parametrów dla danego polecenia cmdlet, możesz uruchom następujące polecenie:

```PowerShell
    Get-Command <cmdlet_name> -Syntax
```

Alternatywnie można użyć [Import-PSSession](https://docs.microsoft.com/powershell/module/Microsoft.PowerShell.Utility/Import-PSSession?view=powershell-5.1) polecenia cmdlet, aby zaimportować wszystkie polecenia cmdlet program ten do bieżącej sesji na komputerze lokalnym. W ten sposób wszystkie polecenia cmdlet i funkcje program ten są teraz dostępne na komputerze lokalnym, wraz z uzupełniania po naciśnięciu tabulatora i, inne ogólnie rzecz biorąc, wykonywanie skryptów. 

Aby zaimportować program ten sesji na komputerze lokalnym, wykonaj następujące czynności:

1. Ustanowienie relacji zaufania.

    — W systemie zintegrowane, uruchom następujące polecenie w sesji środowiska Windows PowerShell z podwyższonym poziomem uprawnień do dodania program ten jako zaufanego hosta na uruchomiona na hoście cyklu życia sprzętu lub uprzywilejowanego dostępu do stacji roboczej ze wzmocnionymi zabezpieczeniami maszyny wirtualnej.

      ````PowerShell
        winrm s winrm/config/client '@{TrustedHosts="<IP Address of Privileged Endpoint>"}'
      ````
    - Jeśli korzystasz z ADSK, zaloguj się do rozwoju hosta zestawu.

2. Na uruchomiona na hoście cyklu życia sprzętu lub uprzywilejowanego dostępu do stacji roboczej ze wzmocnionymi zabezpieczeniami maszynie wirtualnej Otwórz sesję programu Windows PowerShell. Uruchom następujące polecenia, aby ustanowić sesję zdalną na maszynie wirtualnej, który hostuje program ten:
 
    - Na zintegrowany system:
      ````PowerShell
        $cred = Get-Credential

        $session = New-PSSession -ComputerName <IP_address_of_ERCS> `
          -ConfigurationName PrivilegedEndpoint -Credential $cred
      ````
      `ComputerName` Parametr może być adres IP lub nazwę DNS w jednej z maszyn wirtualnych, które obsługuje program ten. 
    - Jeśli używasz ADSK:
     
      ````PowerShell
       $cred = Get-Credential

       $session = New-PSSession -ComputerName azs-ercs01 `
          -ConfigurationName PrivilegedEndpoint -Credential $cred
      ```` 
   Po wyświetleniu monitu użyj następujących poświadczeń:

      - **Nazwa użytkownika**: Określ konto CloudAdmin w formacie  **&lt; *domeny stosu Azure*&gt;\cloudadmin**. (ASDK, nazwa użytkownika jest **azurestack\cloudadmin**.)
      - **Hasło**: wprowadź to samo hasło, które zostało podane podczas instalacji dla konta administratora domeny AzureStackAdmin.

3. Zaimportuj program ten sesji do komputera lokalnego
    ````PowerShell 
        Import-PSSession $session
    ````
4. Teraz można użyć uzupełniania po naciśnięciu tabulatora i czy skryptów w zwykły sposób, w sesji programu PowerShell lokalnej z wszystkich funkcji i poleceń cmdlet programu program ten, bez wpływu na stan zabezpieczeń stosu Azure. Owocnej pracy.


## <a name="close-the-privileged-endpoint-session"></a>Zamknij sesję uprzywilejowanych punktu końcowego

 Jak wspomniano wcześniej, program ten rejestruje każda akcja (i jej odpowiednie dane wyjściowe), które można wykonywać w sesji programu PowerShell. Należy zamknąć sesję przy użyciu `Close-PrivilegedEndpoint` polecenia cmdlet. To polecenie cmdlet poprawnie zamyka punktu końcowego i przesyła pliki dziennika w udziale plików zewnętrznych dla przechowywania.

Aby zamknąć sesji punktu końcowego:

1. Tworzenie udziału plików zewnętrznego, który jest dostępny program ten. W środowisku development kit można po prostu utworzyć udział plików na hoście development kit.
2. Uruchom `Close-PrivilegedEndpoint` polecenia cmdlet. 
3. Zostanie wyświetlony monit o ścieżkę do przechowywania plików dziennika zapis. Określ udział plików, który został utworzony wcześniej, w formacie &#92; &#92; *servername*&#92;*sharename*. Jeśli ścieżka nie jest określony, polecenie cmdlet nie powiedzie się i sesja pozostanie otwarta. 

    ![Dane wyjściowe polecenia cmdlet PrivilegedEndpoint Zamknij pokazujący, gdzie Określ ścieżkę docelową, zapis](media/azure-stack-privileged-endpoint/closeendpoint.png)

Po pomyślnie przesyłania plików dziennika zapis do udziału plików, są one automatycznie usunięte z program ten. 

> [!NOTE]
> Jeśli za pomocą poleceń cmdlet programu Zamknij sesję program ten `Exit-PSSession` lub `Exit`, lub zamknij konsolę programu PowerShell, dzienniki wykaz nie transferu do udziału plików. Pozostają one w program ten. Przy następnym uruchomieniu `Close-PrivilegedEndpoint` i obejmują udziału plików, dzienniki zapis z poprzedniej sesji również zostaną przeniesione. Nie używaj `Exit-PSSession` lub `Exit` zamknąć sesję program ten; użyj `Close-PrivilegedEndpoint` zamiast tego.


## <a name="next-steps"></a>Kolejne kroki
[Azure stosu narzędzia diagnostyczne](azure-stack-diagnostics.md)
