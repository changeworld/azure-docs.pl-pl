---
title: Obróć klucze tajne w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wymienić klucze tajne w usłudze Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: cc7b1b9e96e32b090c0ec9ec9ab029588e5ec4ce
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/12/2018
ms.locfileid: "49166971"
---
# <a name="rotate-secrets-in-azure-stack"></a>Obróć klucze tajne w usłudze Azure Stack

*Te instrukcje dotyczą tylko usługi Azure Stack zintegrowanych systemów wersji 1803 i nowsze. Nie należy podejmować wpisu tajnego obrotu pre 1802 Azure Stack wersji*

Usługa Azure Stack używa różnych kluczy tajnych do obsługi bezpiecznej komunikacji między zasobami infrastruktury Azure Stack i usługami.

- **Wewnętrzny wpisów tajnych**  
Wszystkie certyfikaty, hasła, bezpieczne ciągów i kluczy używanych przez infrastrukturę usługi Azure Stack bez udziału operatora usługi Azure Stack. 

- **Zewnętrzne wpisy tajne**  
Certyfikaty usług infrastruktury usługi dołączonej do Internetu, które są dostarczane przez podmiot stosu usługi Azure. W tym certyfikaty dla następujących usług: 
    - Portal administratora 
    - Publiczny 
    - Administrator usługi Azure Resource Manager 
    - Globalny usługi Azure Resource Manager 
    - Administrator usługi Keyvault 
    - Keyvault 
    - Usługi ACS (w tym obiektów blob, tabela i queue storage) 
    - ADFS<sup>*</sup>
    - Wykres<sup>*</sup>

   <sup>*</sup> Dotyczy tylko w przypadku dostawcy tożsamości środowiska Active Directory federacyjnego Services (AD FS).

> [!NOTE]
> Wszystkie inne bezpieczeństwo kluczy i ciągów, w tym kontrolera zarządzania płytą GŁÓWNĄ i przełączać hasła, hasła do kont użytkowników i administratorów są nadal ręcznie aktualizowane przez administratora. 

W celu zachowania integralności infrastruktury Azure Stack, Operatorzy będą potrzebowali możliwości okresowo Obróć klucze tajne swoją infrastrukturę, częstotliwości, które są zgodne z wymaganiami dotyczącymi zabezpieczeń w organizacji.

### <a name="rotating-secrets-with-external-certificates-from-a-new-certificate-authority"></a>Obracanie wpisów tajnych za pomocą certyfikatów zewnętrznych z nowego urzędu certyfikacji

Usługa Azure Stack obsługuje obrotu wpisu tajnego za pomocą certyfikatów zewnętrznych z nowego certyfikatu urzędu certyfikacji w następujących kontekstów się:

|Zainstalowany certyfikat urzędu certyfikacji.|Obróć do urzędu certyfikacji|Obsługiwane|Obsługiwane wersje usługi Azure Stack|
|-----|-----|-----|-----|
|Z podpisem własnym|Do wersji Enterprise|Nieobsługiwane||
|Z podpisem własnym|Aby z podpisem własnym|Nieobsługiwane||
|Z podpisem własnym|Na wartość publiczne<sup>*</sup>|Obsługiwane|1803 i nowsze|
|Dla przedsiębiorstw|Do wersji Enterprise|Obsługiwane tak długo, jak klienci używają tego samego przedsiębiorstwa urzędu certyfikacji, ponieważ używany we wdrożeniu|1803 i nowsze|
|Dla przedsiębiorstw|Aby z podpisem własnym|Nieobsługiwane||
|Dla przedsiębiorstw|Na wartość publiczne<sup>*</sup>|Obsługiwane|1803 i nowsze|
|Z publicznego<sup>*</sup>|Do wersji Enterprise|Nieobsługiwane|1803 i nowsze|
|Z publicznego<sup>*</sup>|Aby z podpisem własnym|Nieobsługiwane||
|Z publicznego<sup>*</sup>|Na wartość publiczne<sup>*</sup>|Obsługiwane|1803 i nowsze|

<sup>*</sup> W tym miejscu publiczne urzędy certyfikacji są te, które są dostępne w ramach programu Windows zaufanych głównych. Pełną listę można znaleźć [programu zaufanych certyfikatów głównych firmy Microsoft: uczestników (począwszy od 27 czerwca 2017 r.)](https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca).

## <a name="alert-remediation"></a>Korygowanie alertu

W przypadku wpisów tajnych w ciągu 30 dni wygaśnięcia, następujące alerty są generowane w portalu administratora: 

- Wygaśnięcie hasła konta usługi oczekującej 
- Wygaśnięcie certyfikatu wewnętrznego oczekujące 
- Wygaśnięcie certyfikatu zewnętrznego oczekujące 

Obrót wpisu tajnego wykonując poniższe instrukcje uruchamiania będą skorygować te alerty.

## <a name="pre-steps-for-secret-rotation"></a>Kroki przed operacją dla wpisu tajnego obrotu

   > [!IMPORTANT]  
   > Upewnij się, że obrotu tajne nie zostało pomyślnie wykonany w swoim środowisku. Jeśli została już wykonana obrotu wpisu tajnego, należy zaktualizować usługi Azure Stack do wersji 1807 lub nowszej, przed wykonaniem wpisu tajnego obrotu. 
1.  Operatorzy mogą zauważyć alerty otwierających i zamykających automatycznie w trakcie rotacji kluczy tajnych w usłudze Azure Stack.  To zachowanie jest przewidywane i alerty można zignorować.  Operatory można sprawdzić poprawność te alerty, uruchamiając AzureStack testu.  Dla operatorów do monitorowania za pomocą programu SCOM systemy usługi Azure Stack, wprowadzenie do systemu w trybie konserwacji uniemożliwi ich systemami ITSM osiągnięcie tych alertów, ale będzie w dalszym ciągu alert, jeśli system usługi Azure Stack staje się niedostępny. 
2. Powiadom użytkowników, wszelkich operacji konserwacji. Planowanie okna obsługi normalnych, jak to możliwe, podczas poza godzinami. Operacje konserwacji może mieć wpływ na użytkownika obciążeń i operacje w portalu.
    > [!note]  
    > Następne kroki stosowane tylko wtedy, gdy obracanie zewnętrzne wpisy tajne w usłudze Azure Stack.
3. Przygotuj nowy zestaw zastępczy certyfikatów zewnętrznych. Nowy zestaw pasuje do specyfikacji certyfikatu, opisane w temacie [wymagania dotyczące certyfikatów infrastruktury kluczy publicznych do usługi Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-pki-certs).
4.  Store wykonywanie kopii zapasowej certyfikatów używanych dla obrotu w bezpiecznej lokalizacji kopii zapasowej. Jeśli Twoje obrotu uruchomiona, a następnie kończy się niepowodzeniem, Zamień certyfikaty w udziale plików kopii zapasowych przed ponownym uruchomieniem obrót. Należy pamiętać, wykonywania kopii zapasowych w bezpiecznej lokalizacji kopii zapasowej.
5.  Utwórz udział plików, w której będziesz mieć dostęp z ERCS maszyn wirtualnych. Udział plików musi być czytelny i zapisywalny dla **CloudAdmin** tożsamości.
6.  Otwórz konsolę programu PowerShell ISE z komputera, na których mają dostęp do udziału plików. Przejdź do Twojego udziału plików. 
7.  Uruchom **[CertDirectoryMaker.ps1](http://www.aka.ms/azssecretrotationhelper)** utworzyć wymagane katalogi zewnętrzne certyfikaty.

## <a name="rotating-external-and-internal-secrets"></a>Obracanie wewnętrznych i zewnętrznych wpisów tajnych

Aby obrócić zewnętrznych wewnętrzny klucz tajny:

1. W nowo utworzonej **/certyfikaty** Katalog utworzony w krokach wstępne umieścić nowy zestaw zastępczy certyfikaty zewnętrzne w strukturze katalogów z formatem opisane w sekcji wymagane certyfikaty z [wymagania dotyczące certyfikatów infrastruktury kluczy publicznych do usługi Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-pki-certs#mandatory-certificates).
2. Tworzenie sesji programu PowerShell przy użyciu [uprzywilejowanych punktu końcowego](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint) przy użyciu **CloudAdmin** konta, a także przechowywać sesji jako zmienną. Ta zmienna będzie używany jako parametr w następnym kroku.

    > [!IMPORTANT]  
    > Wprowadź sesję, nie przechowują sesji jako zmienną.
    
3. Uruchom  **[invoke-command](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/invoke-command?view=powershell-5.1)**. Przekaż zmiennej sesji użytkownika uprzywilejowanego punktu końcowego programu PowerShell jako **sesji** parametru. 
4. Uruchom **Start SecretRotation** z następującymi parametrami:
    - **PfxFilesPath**  
    Określ ścieżkę sieciową do utworzonego wcześniej katalogu certyfikatów.  
    - **PathAccessCredential**  
    Obiekt PSCredential poświadczeń do udziału. 
    - **CertificatePassword**  
    Bezpieczny ciąg hasła używane dla wszystkich utworzony plik certyfikatu pfx.
4. Zaczekaj, aż Obróć klucze tajne.  
Po pomyślnym ukończeniu rotacji wpisu tajnego, zostanie wyświetlona konsola **ogólny stan akcji: Sukces**. 
    > [!note]  
    > Jeśli wpisu tajnego obrotu zakończy się niepowodzeniem, postępuj zgodnie z instrukcjami w komunikacie o błędzie i ponownie uruchom start-secretrotation z **-Uruchom ponownie** parametru. Się z pomocą techniczną, jeśli wystąpią powtórzone błędy klucza tajnego obrotu. 
5. Po pomyślnym zakończeniu obrotu wpisu tajnego usunąć certyfikaty z udziału utworzonego w kroku wstępnego i zapisanie ich w bezpiecznej lokalizacji kopii zapasowej. 

## <a name="walkthrough-of-secret-rotation"></a>Przewodnik dotyczący rotacji wpisu tajnego

W poniższym przykładzie programu PowerShell pokazuje, poleceń cmdlet i parametrów do uruchomienia w celu obracania wpisów tajnych.

```powershell
#Create a PEP Session
winrm s winrm/config/client '@{TrustedHosts= "<IPofERCSMachine>"}'
$PEPCreds = Get-Credential 
$PEPsession = New-PSSession -computername <IPofERCSMachine> -Credential $PEPCreds -ConfigurationName PrivilegedEndpoint 

#Run Secret Rotation
$CertPassword = ConvertTo-SecureString "Certpasswordhere" -AsPlainText -Force
$CertShareCred = Get-Credential 
$CertSharePath = "<NetworkPathofCertShare>"
Invoke-Command -session $PEPsession -ScriptBlock { 
Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCred -CertificatePassword $using:CertPassword }
Remove-PSSession -Session $PEPSession
```
## <a name="rotating-only-internal-secrets"></a>Obracanie tylko wewnętrzne wpisów tajnych

Aby obrócić tylko usługi Azure Stack wewnętrznego wpisów tajnych:

1. Tworzenie sesji programu PowerShell przy użyciu [uprzywilejowanych punktu końcowego](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint).
2. W sesji uprzywilejowanych punktu końcowego Uruchom **Start SecretRotation** bez argumentów.
3. Zaczekaj, aż Obróć klucze tajne.  
Po pomyślnym ukończeniu rotacji wpisu tajnego, zostanie wyświetlona konsola **ogólny stan akcji: Sukces**. 
    > [!note]  
    > W przypadku niepowodzenia wpisu tajnego obrotu postępuj zgodnie z instrukcjami w komunikacie o błędzie i ponownie uruchom start-secretrotation z **-Uruchom ponownie** parametru. Się z pomocą techniczną, jeśli wystąpią powtórzone błędy klucza tajnego obrotu. 

## <a name="start-secretrotation-reference"></a>Odwołanie do rozpoczęcia SecretRotation

Obraca się wpisy tajne w systemie usługi Azure Stack. Wykonać tylko względem usługi Azure Stack punktu końcowego w uprzywilejowanych.

### <a name="syntax"></a>Składnia

Ścieżka (ustawienie domyślne)

```powershell
Start-SecretRotation [-PfxFilesPath <string>] [-PathAccessCredential] <PSCredential> [-CertificatePassword <SecureString>]  
```

### <a name="description"></a>Opis

Polecenie cmdlet Start-SecretRotation obraca się wpisy tajne infrastruktury systemu Azure Stack. Domyślnie obraca się wszystkich wpisów tajnych ujawnione w sieci wewnętrznej infrastruktury z danymi wejściowymi użytkownika także o certyfikaty wszystkie punkty końcowe infrastruktury sieci zewnętrznej. W przypadku rotacji punktów końcowych infrastruktury sieci zewnętrznej, Start SecretRotation mają zostać wykonane za pomocą bloku skryptu Invoke-Command za pomocą środowiska usługi Azure Stack punktu końcowego uprzywilejowanych sesji przekazany jako parametr sesji.
 
### <a name="parameters"></a>Parametry

| Parametr | Typ | Wymagane | Pozycja | Domyślne | Opis |
| -- | -- | -- | -- | -- | -- |
| PfxFilesPath | Ciąg  | False  | o nazwie  | Brak  | Ścieżka udziału plików do **\Certificates** katalogu zawierającego wszystkich zewnętrznych sieci certyfikaty punktu końcowego. Wymagany tylko w przypadku rotacji kluczy tajnych zewnętrznych lub wszystkich wpisów tajnych. Katalog end musi być **\Certificates**. |
| CertificatePassword | SecureString | False  | o nazwie  | Brak  | Hasło dla wszystkich certyfikatów w PfXFilesPath —. Wymagane wartości, jeśli PfxFilesPath jest udostępniany, gdy są obracane wewnętrznych i zewnętrznych wpisów tajnych. |
| PathAccessCredential | PSCredential | False  | o nazwie  | Brak  | Poświadczenie programu PowerShell dla udziału plików z **\Certificates** katalogu zawierającego wszystkich zewnętrznych sieci certyfikaty punktu końcowego. Wymagany tylko w przypadku rotacji kluczy tajnych zewnętrznych lub wszystkich wpisów tajnych.  |
| Uruchom ponownie | SwitchParameter | False  | o nazwie  | Brak  | Uruchom ponownie muszą być używane w dowolnym momencie po nieudanej próbie jest ponowne próby obrót wpisu tajnego. |

### <a name="examples"></a>Przykłady
 
**Obróć tylko wpisy tajne wewnętrznej infrastruktury**

```powershell  
PS C:\> Start-SecretRotation  
```

To polecenie obraca wszystkich wpisów tajnych infrastruktury, połączenie z sieci wewnętrznej usługi Azure Stack. Start-SecretRotation obraca wszystkich wpisów tajnych, które są generowane przez stos, ale ponieważ nie ma żadnych certyfikatów podana, certyfikaty zewnętrzny punkt końcowy nie będzie obracana.  

**Obróć klucze tajne infrastruktury wewnętrznych i zewnętrznych**
  
```powershell
PS C:\> Invoke-Command -session $PEPSession -ScriptBlock { 
Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCred -CertificatePassword $using:CertPassword } 
Remove-PSSession -Session $PEPSession
```

To polecenie obraca wszystkich wpisów tajnych infrastruktury, połączenie z sieci wewnętrznej usługi Azure Stack, a także certyfikaty protokołu TLS, używane dla punktów końcowych infrastruktury sieci zewnętrznej usługi Azure Stack. Start-SecretRotation obraca wszystkich wpisów tajnych, które są generowane przez stos, a ponieważ podano certyfikatów oraz certyfikatów jest zewnętrzny punkt końcowy również zostaną obrócone.  


## <a name="update-the-baseboard-management-controller-bmc-password"></a>Zaktualizuj hasło kontrolera zarządzania płytą główną

Kontroler zarządzania płytą główną (BMC) monitoruje stan fizycznych serwerów. Instrukcje na temat aktualizowania hasła kontrolera bmc i specyfikacje różnią się zależnie od dostawcy sprzętu producenta sprzętu (OEM). Należy zaktualizować hasła dla składników usługi Azure Stack w regularnych cyklach.

1. Aktualizacja kontrolera zarządzania płytą GŁÓWNĄ na serwerach fizycznych z usługi Azure Stack, postępując zgodnie z instrukcjami podanymi przez producenta OEM. Hasło dla każdego kontrolera zarządzania płytą GŁÓWNĄ w środowisku musi być taka sama.
2. Otwórz punkt końcowy uprzywilejowanych w usłudze Azure Stack sesji. Aby uzyskać instrukcje, zobacz [przy użyciu uprzywilejowanych punktu końcowego w usłudze Azure Stack](azure-stack-privileged-endpoint.md).
3. Po PowerShell wiersz został zmieniony na **[adres IP lub maszyny Wirtualnej ERCS name]: PS >** lub **[azs-ercs01]: PS >**, w zależności od środowiska, uruchom `Set-BmcPassword` , uruchamiając `invoke-command`. Przekaż zmiennej sesji użytkownika uprzywilejowanego punktu końcowego, jako parametr. Na przykład:

    ```powershell
    # Interactive Version
    $PEip = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PECred = Get-Credential "<Domain>\CloudAdmin" -Message "PE Credentials" 
    $NewBMCpwd = Read-Host -Prompt "Enter New BMC password" -AsSecureString 

    $PEPSession = New-PSSession -ComputerName $PEip -Credential $PECred -ConfigurationName "PrivilegedEndpoint" 

    Invoke-Command -Session $PEPSession -ScriptBlock {
        Set-Bmcpassword -bmcpassword $using:NewBMCpwd
    }
    Remove-PSSession -Session $PEPSession
    ```
    
    Za pomocą statycznych wersja programu PowerShell i hasła jako wiersze kodu:
    
    ```powershell
    # Static Version
    $PEip = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PEUser = "<Privileged Endpoint user for exmaple Domain\CloudAdmin>"
    $PEpwd = ConvertTo-SecureString "<Privileged Endpoint Password>" -AsPlainText -Force
    $PECred = New-Object System.Management.Automation.PSCredential ($PEUser, $PEpwd) 
    $NewBMCpwd = ConvertTo-SecureString "<New BMC Password>" -AsPlainText -Force 

    $PEPSession = New-PSSession -ComputerName $PEip -Credential $PECred -ConfigurationName "PrivilegedEndpoint" 

    Invoke-Command -Session $PEPSession -ScriptBlock {
        Set-Bmcpassword -bmcpassword $using:NewBMCpwd
    }
    Remove-PSSession -Session $PEPSession
    ```

## <a name="next-steps"></a>Kolejne kroki

[Dowiedz się więcej na temat zabezpieczeń usługi Azure Stack](azure-stack-security-foundations.md)
