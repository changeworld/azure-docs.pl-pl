---
title: Obróć kluczy tajnych w stosie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak Obróć tajnych w stosie Azure.
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
ms.date: 05/15/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: 8ac151a70a81f78dab5ed1f30df51a1121a42cbd
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37029020"
---
# <a name="rotate-secrets-in-azure-stack"></a>Obróć kluczy tajnych w stosie Azure

*Te instrukcje dotyczą tylko 1803 wersji systemów zintegrowanego stosu Azure i później. Nie należy podejmować tajny obrotu w wersji pre-1802 Azure stosu wersjach*

Stos Azure korzysta z różnych kluczy tajnych do obsługi bezpiecznej komunikacji między zasobami infrastruktury stosu Azure i usługi.

- **Wewnętrzny kluczy tajnych**  
Wszystkie certyfikaty, hasła bezpiecznego ciągów i kluczy używanych przez infrastrukturę stosu Azure bez interwencji operatora stosu Azure. 

- **Klucze tajne zewnętrznych**  
Infrastruktura certyfikatów usługi dla usługi dołączonej do Internetu, które są udostępniane przez podmiot stosu Azure. Obejmuje to certyfikaty dla następujących usług: 
    - Portal administratora 
    - Publiczny 
    - Administrator usługi Azure Resource Manager 
    - Globalny usługi Azure Resource Manager 
    - Keyvault administratora 
    - Keyvault 
    - Usługi ACS (w tym obiektów blob, tabel i magazyn kolejek) 
    - ADFS<sup>*</sup>
    - Wykres<sup>*</sup>

    > <sup>*</sup> Dotyczy tylko w przypadku dostawcy tożsamości w środowisku usług Active Directory federacyjnych (AD FS).

> [!NOTE]
> Wszystkich innych bezpieczeństwo kluczy i ciągi, łącznie z BMC i przełączyć hasła, haseł kont użytkowników i administrator nadal ręcznie zostały zaktualizowane przez administratora. 

W celu zachowania integralności infrastruktury Azure stosu, Operatorzy będą potrzebowali możliwości okresowo Obróć kluczy tajnych ich infrastruktury częstotliwością, które są zgodne z wymaganiami zabezpieczeń używanych w organizacji.

### <a name="rotating-secrets-with-external-certificates-from-a-new-certificate-authority"></a>Obracanie kluczy tajnych z certyfikaty zewnętrzne od nowego urzędu certyfikacji

Stos Azure obsługuje tajny Obrót o certyfikaty zewnętrzne z nowego certyfikatu urzędu certyfikacji w następujących sytuacjach:

|Zainstalowany certyfikat urzędu certyfikacji|Obróć do urzędu certyfikacji|Obsługiwane|Obsługiwane wersje Azure stosu|
|-----|-----|-----|-----|-----|
|Z podpisem własnym|Do wersji Enterprise|Nieobsługiwane||
|Z podpisem własnym|Aby z podpisem własnym|Nieobsługiwane||
|Z podpisem własnym|Na publiczne<sup>*</sup>|Obsługiwane|1803 & później|
|Enterprise|Do wersji Enterprise|Obsługiwane tak długo, jak używać tego samego przedsiębiorstwa urzędu certyfikacji jako używane w wdrożenia|1803 & później|
|Enterprise|Aby z podpisem własnym|Nieobsługiwane||
|Enterprise|Na publiczne<sup>*</sup>|Obsługiwane|1803 & później|
|Z publicznego<sup>*</sup>|Do wersji Enterprise|Nieobsługiwane|1803 & później|
|Z publicznego<sup>*</sup>|Aby z podpisem własnym|Nieobsługiwane||
|Z publicznego<sup>*</sup>|Na publiczne<sup>*</sup>|Obsługiwane|1803 & później|

<sup>*</sup> W tym miejscu publicznych urzędów certyfikacji są tymi, które są częścią programu zaufanych głównych systemu Windows. Pełną listę można znaleźć [programu zaufanych certyfikatów głównych firmy Microsoft: uczestników (począwszy od 27 czerwca 2017)](https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca).

## <a name="alert-remediation"></a>Korygowanie alertu

Gdy w ciągu 30 dni wygaśnięcia hasła, następujące alerty są generowane w portalu administratora: 

- Wygaśnięcie hasła konta usługi oczekującej 
- Wygaśnięcie certyfikatu wewnętrznego oczekujące 
- Wygaśnięcie certyfikatu zewnętrznego oczekujące 

Uruchomiona tajny obrotu z instrukcjami poniżej będzie skorygować te alerty.

## <a name="pre-steps-for-secret-rotation"></a>Kroki przed tajny obrotu

1.  Powiadom użytkowników żadnych operacji konserwacji. Planowanie okna obsługi normalnych, jak to możliwe, podczas poza godzinami pracy. Operacje konserwacji może mieć wpływ na zarówno obciążeń użytkownika, jak i działania portalu.

    > [!note]  
    > Następne kroki stosowane tylko wtedy, gdy obracanie kluczy tajnych zewnętrznego stosu Azure.

2. Upewnij się, że tajne obrotu nie został pomyślnie wykonano w środowisku w ciągu ostatniego miesiąca. W tym momencie stosu Azure obsługuje tylko tajny obrotu raz w miesiącu. 
3. Przygotuj nowy zestaw wymiany certyfikatów zewnętrznych. Nowy zestaw odpowiada specyfikacji certyfikatu opisane w temacie [wymagania dotyczące certyfikatu PKI stosu Azure](https://docs.microsoft.com/azure/azure-stack/azure-stack-pki-certs).
4.  Przechowywanie kopii zapasowej certyfikaty używane do obrotu w bezpiecznej lokalizacji kopii zapasowej. Jeśli Twoje obrotu uruchomiona, a następnie kończy się niepowodzeniem, Zastąp certyfikaty w udziale plików kopii zapasowych przed ponownym uruchomieniem obrót. Należy pamiętać, wykonywania kopii zapasowych w bezpiecznej lokalizacji.
5.  Tworzenie udziału plików, których masz dostęp z ERCS maszyn wirtualnych. Udział plików musi być zdatny do odczytu i zapisu dla **CloudAdmin** tożsamości.
6.  Otwórz konsolę programu PowerShell ISE z komputera, gdzie masz dostęp do udziału plików. Przejdź do Twojej udziału plików. 
7.  Uruchom **[CertDirectoryMaker.ps1](http://www.aka.ms/azssecretrotationhelper)** można utworzyć katalogów wymaganych do używania certyfikatów zewnętrznych.

## <a name="rotating-external-and-internal-secrets"></a>Obracanie kluczy tajnych wewnętrznych i zewnętrznych

Aby zmienić zarówno zewnętrzne wewnętrzny klucz tajny:

1. W nowo utworzony **/certyfikaty** Katalog utworzony w kroku przed, umieść nowy zestaw certyfikaty zewnętrzne zastąpienie w strukturze katalogów zgodnie z formatem opisane w sekcji wymagane certyfikaty z [wymagania dotyczące certyfikatu PKI stosu Azure](https://docs.microsoft.com/azure/azure-stack/azure-stack-pki-certs#mandatory-certificates).
2. Tworzenie sesji programu PowerShell z [uprzywilejowanych punktu końcowego](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint) przy użyciu **CloudAdmin** konta i przechowywać sesje jako zmienną. Ta zmienna będzie używać jako parametru w następnym kroku.

    > [!IMPORTANT]  
    > Wprowadź sesji, nie sklepu sesji jako zmienną.
    
3. Uruchom  **[invoke-command](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/invoke-command?view=powershell-5.1)**. Przekaż zmiennej sesji użytkownika uprzywilejowanego punktu końcowego programu PowerShell jako **sesji** parametru. 
4. Uruchom **Start SecretRotation** z następującymi parametrami:
    - **PfxFilesPath**  
    Określ ścieżkę sieciową do utworzonego wcześniej katalogu certyfikatów.  
    - **PathAccessCredential**  
    Obiekt PSCredential poświadczeń do udziału. 
    - **CertificatePassword**  
    Bezpieczny ciąg hasło używane wszystkie pliki certyfikatów pfx utworzony.
4. Zaczekaj, aż Obróć tajnych.  
Po pomyślnym ukończeniu rotacji tajne, konsola wyświetli **ogólny stan akcji: Sukces**. 
5. Po pomyślnym ukończeniu obrotu tajne Usuń certyfikaty z udziału utworzonego w kroku przed i zapisywanie ich w bezpiecznej lokalizacji kopii zapasowej. 

## <a name="walkthrough-of-secret-rotation"></a>Wskazówki tajny obrotu

W poniższym przykładzie programu PowerShell pokazano poleceń cmdlet i parametrów do uruchomienia w celu obracania tajnych.

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
## <a name="rotating-only-internal-secrets"></a>Obracanie tylko wewnętrzny kluczy tajnych

Obracanie tylko Azure stosu wewnętrznego kluczy tajnych:

1. Tworzenie sesji programu PowerShell z [uprzywilejowanych punktu końcowego](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint).
2. W sesji uprzywilejowanych punktu końcowego, uruchom **Start SecretRotation** bez argumentów.

## <a name="start-secretrotation-reference"></a>Odwołanie do rozpoczęcia SecretRotation

Obraca kluczy tajnych stosu systemu Azure. Wykonywać tylko względem punktu końcowego uprzywilejowanych stosu Azure.

### <a name="syntax"></a>Składnia

Ścieżka (ustawienie domyślne)

```powershell
Start-SecretRotation [-PfxFilesPath <string>] [-PathAccessCredential] <PSCredential> [-CertificatePassword <SecureString>]  
```

### <a name="description"></a>Opis

Polecenie cmdlet Start-SecretRotation obraca kluczy tajnych infrastruktury systemu Azure stosu. Domyślnie obraca się wszystkich kluczy tajnych udostępniany w sieci wewnętrznej infrastruktury przy użyciu danych wejściowych użytkownika także o certyfikaty wszystkie punkty końcowe infrastruktury sieci zewnętrznej. Gdy obracanie punkty końcowe infrastruktury sieci zewnętrznej, Start SecretRotation powinny być wykonywane za pomocą bloku skryptu Invoke-Command z sesji uprzywilejowanych punktu końcowego środowiska Azure stosu przekazany jako parametr sesji.
 
### <a name="parameters"></a>Parametry

| Parametr | Typ | Wymagane | Pozycja | Domyślne | Opis |
| -- | -- | -- | -- | -- | -- |
| PfxFilesPath | Ciąg  | False  | O nazwie  | Brak  | Ścieżka udziału plików do **\Certificates** katalog zawierający wszystkie zewnętrzne sieci certyfikaty punktu końcowego. Wymagany tylko w przypadku obracanie kluczy tajnych wewnętrznych i zewnętrznych. Katalog end musi być **\Certificates**. |
| CertificatePassword | SecureString | False  | O nazwie  | Brak  | Hasło dla wszystkich certyfikatów w PfXFilesPath —. Wymagane wartości, jeśli PfxFilesPath jest udostępniane po wewnętrznych i zewnętrznych kluczy tajnych są obracane. |
|

### <a name="examples"></a>Przykłady
 
**Obróć tylko wewnętrzna infrastruktura kluczy tajnych**

```powershell  
PS C:\> Start-SecretRotation  
```

To polecenie obraca wszystkich kluczy tajnych infrastruktury narażonych na stosie Azure sieci wewnętrznej. Start SecretRotation obraca wszystkich kluczy tajnych generowany stos, ale ponieważ nie ma żadnych certyfikatów podany, certyfikaty zewnętrznego punktu końcowego nie można obracać.  

**Obróć kluczy tajnych infrastruktury wewnętrznych i zewnętrznych**
  
```powershell
PS C:\> Invoke-Command -session $PEPSession -ScriptBlock { 
Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCred -CertificatePassword $using:CertPassword } 
Remove-PSSession -Session $PEPSession
```

To polecenie obraca wszystkich kluczy tajnych infrastruktury narażonych na stosie Azure sieci wewnętrznej, a także certyfikaty protokołu TLS, używane dla punktów końcowych infrastruktury sieci zewnętrznej Azure stosu. Start SecretRotation obraca wszystkich kluczy tajnych generowany stos, a ponieważ podano w nim certyfikaty zewnętrznego punktu końcowego certyfikatów również można obracać.  


## <a name="update-the-baseboard-management-controller-bmc-password"></a>Zaktualizuj hasło kontrolera zarządzania płytą główną

Kontroler zarządzania płytą główną (BMC) monitoruje stan fizycznych serwerów. Specyfikacje i instrukcje na temat aktualizowania hasła bmc różnić w zależności od producenta komputera producenta sprzętu (OEM). Należy zaktualizować hasła dla składników Azure stosu w regularnych okresach.

1. Zaktualizuj BMC na serwerach fizycznych stosu Azure zgodnie z instrukcjami producenta OEM. Hasło dla każdego kontrolera BMC w danym środowisku muszą być takie same.
2. Otwórz uprzywilejowanych punktu końcowego w sesjach stosu Azure. Aby uzyskać instrukcje, zobacz [przy użyciu punktu końcowego uprzywilejowanych w stosie Azure](azure-stack-privileged-endpoint.md).
3. Po programu PowerShell wiersz został zmieniony na **[adres IP lub wirtualna ERCS name]: PS >** lub **[azs ercs01]: PS >** w zależności od środowiska uruchamiania `Set-BmcPassword` uruchamiając `invoke-command`. Przekaż zmiennej sesji użytkownika uprzywilejowanego punktu końcowego jako parametr. Na przykład:

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
    
    Za pomocą statycznego wersja programu PowerShell i hasła jako wierszy kodu:
    
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

[Dowiedz się więcej o zabezpieczeniach stosu Azure](azure-stack-security-foundations.md)
