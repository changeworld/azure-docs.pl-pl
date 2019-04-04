---
title: Za pomocą uprzywilejowanych punktu końcowego w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Pokazuje, jak używać uprzywilejowanego (program ten) dla punktu końcowego w usłudze Azure Stack (dla operatorów usługi Azure Stack).
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2019
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 01/25/2019
ms.openlocfilehash: ef75b161bcdb9e1b9658612b783dff46d1fa2502
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58484342"
---
# <a name="using-the-privileged-endpoint-in-azure-stack"></a>Za pomocą uprzywilejowanych punktu końcowego w usłudze Azure Stack

*Dotyczy: Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

Operatorzy usługi Azure Stack wykonują większość codziennych zadań zarządzania za pomocą portalu administratora, programu PowerShell lub interfejsów API usługi Azure Resource Manager. Jednak w przypadku niektórych mniej typowych operacji, należy użyć *uprzywilejowanych punktu końcowego* (program ten). Program ten jest wstępnie skonfigurowane zdalnego konsoli programu PowerShell, która zawiera wystarczający tylko funkcje, które ułatwiają wykonywanie wymaganych zadań. Punkt końcowy korzysta z [PowerShell JEA (Just Enough Administration)](https://docs.microsoft.com/powershell/jea/overview) do udostępnienia tylko ograniczony zestaw poleceń cmdlet. Program ten dostęp, i wywoływać ograniczony zestaw poleceń cmdlet, używane jest konto o niskich uprawnieniach. Żadnych kont administratora są wymagane. Dla dodatkowego bezpieczeństwa skryptów nie jest dozwolone.

Program ten służy do wykonywania następujących zadań:

- Aby wykonywać zadania niskiego poziomu, takie jak [zbierania dzienników diagnostycznych](https://docs.microsoft.com/azure/azure-stack/azure-stack-diagnostics#log-collection-tool).
- Aby wykonać wiele zadań związanych z integracją po wdrożeniu centrum danych w ramach zintegrowanych systemów, takich jak dodawanie usług przesyłania dalej systemu nazw domen (DNS) po wdrożeniu i konfigurowanie programu Microsoft Graph i integracji usługi Active Directory Federation Services (AD FS) Rotacja certyfikatu, itd.
- Aby pracować za pomocą techniczną, aby uzyskać tymczasowy wysokiego poziomu dostęp na potrzeby szczegółowego rozwiązywania problemów zintegrowanego systemu.

Program ten rejestruje wszystkie akcje (i jej odpowiednie dane wyjściowe), wykonujących w sesji programu PowerShell. Zapewnia to pełnej przejrzystości i inspekcji zakończenie operacji. Można zachować te pliki dziennika dla przyszłych inspekcji.

> [!NOTE]
> W usłudze Azure Stack Development Kit (ASDK), można uruchomić niektóre polecenia dostępne w program ten bezpośrednio w sesji programu PowerShell na hoście development kit. Jednak warto przetestować niektóre operacje za pomocą program ten, takich jak zbieranie dzienników, ponieważ jest jedyną metodą dostępne do wykonywania określonych operacji w środowisku zintegrowane systemy usługi.

## <a name="access-the-privileged-endpoint"></a>Dostęp uprzywilejowany punktu końcowego

Program ten jest dostępny za pośrednictwem zdalną sesję programu PowerShell na maszynie wirtualnej, który hostuje program ten. W ASDK, ta maszyna wirtualna o nazwie **AzS ERCS01**. Jeśli używasz zintegrowanego systemu, istnieją trzy wystąpienia program ten, każda uruchomiona na maszynie wirtualnej (*prefiksu*-ERCS01, *prefiksu*-ERCS02, lub *prefiksu*— ERCS03) na różnych hostach w celu zapewnienia odporności. 

Przed przystąpieniem do wykonywania tej procedury do zintegrowanego systemu, upewnij się, że można uzyskać dostęp program ten przy użyciu adresu IP lub za pośrednictwem systemu DNS. Po początkowym wdrożeniu usługi Azure Stack są dostępne program ten tylko przy użyciu adresu IP, ponieważ integracji usługi DNS nie jest jeszcze skonfigurowany. Z dostawcą sprzętu OEM przekaże Ci plik JSON o nazwie **AzureStackStampDeploymentInfo** zawierający adresy IP program ten.


> [!NOTE]
> Ze względów bezpieczeństwa wymagamy, że łączysz się program ten tylko z działającego ze wzmocnionymi zabezpieczeniami maszyny wirtualnej na podstawie sprzętu hosta cyklu życia lub dedykowane, bezpiecznego komputera, takich jak [stacji roboczej z dostępem uprzywilejowanym](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations). Oryginalną konfigurację sprzętu hosta cyklu życia nie mogą zostać zmodyfikowane z oryginalną konfiguracją, takie jak instalowanie nowego oprogramowania, ani nie stosuje się nawiązać połączenia z program ten.

1. Należy ustanowić relację zaufania.

    - W zintegrowanym systemie, uruchom następujące polecenie w sesji środowiska Windows PowerShell z podwyższonym poziomem uprawnień do dodania program ten jako zaufanego hosta na maszynie wirtualnej ze wzmocnionymi zabezpieczeniami, uruchomiona na hoście cyklu życia sprzętu lub uprzywilejowanego stacji roboczej z dostępem.

      ```powershell
        winrm s winrm/config/client '@{TrustedHosts="<IP Address of Privileged Endpoint>"}'
      ```
    - Jeśli korzystasz z ASDK, zaloguj się do rozwoju hosta zestawu.

2. Na maszynie wirtualnej ze wzmocnionymi zabezpieczeniami uruchomiona na hoście cyklu życia sprzętu lub uprzywilejowanego stacji roboczej z dostępem Otwórz sesję środowiska Windows PowerShell. Uruchom następujące polecenia, aby ustanowić sesję zdalną na maszynie wirtualnej, który hostuje program ten:
 
   - W zintegrowanym systemie:
     ```powershell
       $cred = Get-Credential

       Enter-PSSession -ComputerName <IP_address_of_ERCS> `
         -ConfigurationName PrivilegedEndpoint -Credential $cred
     ```
     `ComputerName` Parametr może być adres IP lub nazwa DNS jednej z maszyn wirtualnych, które obsługuje program ten. 
   - Jeśli korzystasz z ASDK:
     
     ```powershell
       $cred = Get-Credential

       Enter-PSSession -ComputerName azs-ercs01 `
         -ConfigurationName PrivilegedEndpoint -Credential $cred
     ``` 
     Po wyświetleniu monitu użyj następujących poświadczeń:

     - **Nazwa użytkownika**: Określ konto CloudAdmin w formacie  **&lt; *domeny usługi Azure Stack*&gt;\cloudadmin**. (ASDK, nazwa użytkownika jest **azurestack\cloudadmin**.)
     - **Hasło**: Wprowadź to samo hasło, które zostało podane podczas instalacji dla konta administratora domeny AzureStackAdmin.

     > [!NOTE]
     > Jeśli nie można nawiązać połączenia z punktem końcowym ERCS, spróbuj ponownie, podając adres IP ERCS maszyny Wirtualnej, do którego nie zostały już próbowano połączyć kroki 1 i 2.

3. Po nawiązaniu połączenia wiersz zmieni się na **[*nazwa adresu IP lub maszyny Wirtualnej ERCS*]: PS >** lub **[azs-ercs01]: PS >**, w zależności od środowiska. W tym miejscu, należy uruchomić `Get-Command` Aby wyświetlić listę dostępnych poleceń cmdlet.

   Wiele z tych poleceń cmdlet są przeznaczone tylko dla środowisk zintegrowanego systemu (na przykład polecenia cmdlet związane z integracją usługi Centrum danych). Następujące polecenia cmdlet zostały zweryfikowane w ASDK:

   - Wyczyść hosta
   - Close-PrivilegedEndpoint
   - PSSession zakończenia
   - Get-AzureStackLog
   - Get-AzureStackStampInformation
   - Get-Command
   - Get-FormatData
   - Get-Help
   - Get-ThirdPartyNotices
   - Obiekt miary
   - New-CloudAdminUser
   - Wyewidencjonowanie i domyślne
   - Remove-CloudAdminUser
   - Select-Object
   - Set-CloudAdminUserPassword
   - Test-AzureStack
   - Stop-AzureStack
   - Get-ClusterLog

## <a name="tips-for-using-the-privileged-endpoint"></a>Porady dotyczące korzystania z uprzywilejowanym punktu końcowego 

Jak wspomniano powyżej, program ten jest [PowerShell JEA](https://docs.microsoft.com/powershell/jea/overview) punktu końcowego. Zapewniając dodatkową warstwę zabezpieczeń silne, punktu końcowego JEA zmniejsza niektóre podstawowe możliwości programu PowerShell, takie jak ukończenia skryptów lub na karcie. Jeśli spróbujesz wszystkie typy operacji skryptu, kończy się niepowodzeniem z powodu błędu **ScriptsNotAllowed**. Jest to oczekiwane zachowanie.

Tak na przykład, aby uzyskać listę parametrów dla danego polecenia cmdlet, należy uruchom następujące polecenie:

```powershell
    Get-Command <cmdlet_name> -Syntax
```

Alternatywnie, można użyć [Import-PSSession](https://docs.microsoft.com/powershell/module/Microsoft.PowerShell.Utility/Import-PSSession?view=powershell-5.1) polecenia cmdlet, aby zaimportować wszystkie polecenia cmdlet program ten do bieżącej sesji na komputerze lokalnym. Dzięki temu wszystkie polecenia cmdlet i funkcje program ten są teraz dostępne na komputerze lokalnym, wraz z uzupełniania po naciśnięciu tabulatora oraz więcej ogólnie rzecz biorąc, wykonywanie skryptów. 

Aby zaimportować program ten sesji na komputerze lokalnym, wykonaj następujące czynności:

1. Należy ustanowić relację zaufania.

    -W zintegrowany system, uruchom następujące polecenie w sesji środowiska Windows PowerShell z podwyższonym poziomem uprawnień do dodania program ten jako zaufanego hosta na maszynie wirtualnej ze wzmocnionymi zabezpieczeniami, uruchomiona na hoście cyklu życia sprzętu lub uprzywilejowanego stacji roboczej z dostępem.

      ```powershell
        winrm s winrm/config/client '@{TrustedHosts="<IP Address of Privileged Endpoint>"}'
      ```
    - Jeśli korzystasz z ASDK, zaloguj się do rozwoju hosta zestawu.

2. Na maszynie wirtualnej ze wzmocnionymi zabezpieczeniami uruchomiona na hoście cyklu życia sprzętu lub uprzywilejowanego stacji roboczej z dostępem Otwórz sesję środowiska Windows PowerShell. Uruchom następujące polecenia, aby ustanowić sesję zdalną na maszynie wirtualnej, który hostuje program ten:
 
   - W zintegrowanym systemie:
     ```powershell
       $cred = Get-Credential

       $session = New-PSSession -ComputerName <IP_address_of_ERCS> `
         -ConfigurationName PrivilegedEndpoint -Credential $cred
     ```
     `ComputerName` Parametr może być adres IP lub nazwa DNS jednej z maszyn wirtualnych, które obsługuje program ten. 
   - Jeśli korzystasz z ASDK:
     
     ```powershell
      $cred = Get-Credential

      $session = New-PSSession -ComputerName azs-ercs01 `
         -ConfigurationName PrivilegedEndpoint -Credential $cred
     ``` 
     Po wyświetleniu monitu użyj następujących poświadczeń:

     - **Nazwa użytkownika**: Określ konto CloudAdmin w formacie  **&lt; *domeny usługi Azure Stack*&gt;\cloudadmin**. (ASDK, nazwa użytkownika jest **azurestack\cloudadmin**.)
     - **Hasło**: Wprowadź to samo hasło, które zostało podane podczas instalacji dla konta administratora domeny AzureStackAdmin.

3. Importuj sesję program ten, na komputerze lokalnym
    ```powershell 
        Import-PSSession $session
    ```
4. Teraz możesz użyć uzupełniania po naciśnięciu tabulatora i wykonać skryptów w zwykły sposób, w lokalnej sesji programu PowerShell przy użyciu wszystkich funkcji i poleceń cmdlet programu program ten, bez wpływu na stan zabezpieczeń usługi Azure Stack. Owocnej pracy.


## <a name="close-the-privileged-endpoint-session"></a>Zamknij sesję uprzywilejowanych punktu końcowego

 Jak wspomniano wcześniej, program ten rejestruje wszystkie akcje (i jej odpowiednie dane wyjściowe), wykonujących w sesji programu PowerShell. Należy zamknąć sesję przy użyciu `Close-PrivilegedEndpoint` polecenia cmdlet. To polecenie cmdlet poprawnie zamyka punktu końcowego i transferów plików dziennika do udziału zewnętrznego pliku do przechowywania danych.

Aby zamknąć sesję punktu końcowego:

1. Tworzenie udziału pliku zewnętrznego, który jest dostępny program ten. W środowisku deweloperskim zestaw może po prostu Utwórz udział plików na hoście development kit.
2. Uruchom `Close-PrivilegedEndpoint` polecenia cmdlet. 
3. Zostanie wyświetlony monit dla ścieżki, na którym będzie przechowywany plik dziennika transkrypcji. Określ udział plików, który został utworzony wcześniej, w formacie &#92; &#92; *servername*&#92;*sharename*. Jeśli nie określisz ścieżki, polecenie cmdlet nie powiedzie się i sesja pozostanie otwarta. 

    ![Dane wyjściowe polecenia cmdlet PrivilegedEndpoint Zamknij, który pokazuje, gdzie Określ ścieżkę docelową, transkrypcji](media/azure-stack-privileged-endpoint/closeendpoint.png)

Po transkrypcji pliki dziennika są pomyślnie przekazywane do udziału plików, są one automatycznie usunięte z program ten. 

> [!NOTE]
> Jeśli za pomocą polecenia cmdlet Zamknij sesję program ten `Exit-PSSession` lub `Exit`, lub po prostu zamknąć konsolę programu PowerShell, dzienniki transkrypcji nie są przenoszone do udziału plików. Pozostają one w program ten. Przy następnym uruchomieniu `Close-PrivilegedEndpoint` i obejmują udziału plików, dzienniki transkrypcji z poprzedniego musiały również zostaną przeniesione. Nie używaj `Exit-PSSession` lub `Exit` zamknąć sesję program ten; użyj `Close-PrivilegedEndpoint` zamiast tego.


## <a name="next-steps"></a>Kolejne kroki

[Narzędzia diagnostyczne platformy Azure Stack](azure-stack-diagnostics.md)
