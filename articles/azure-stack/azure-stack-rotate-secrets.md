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
ms.date: 12/18/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.lastreviewed: 12/18/2018
ms.openlocfilehash: 55bb83015cee6476a10424f32b51eb9eb29db0da
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2019
ms.locfileid: "59050258"
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
- Administrator usługi KeyVault
- KeyVault
- Host rozszerzenia administratora
- Usługi ACS (w tym obiektów blob, tabela i queue storage)
- ADFS *
- Wykres *

\* Dotyczy tylko w przypadku dostawcy tożsamości środowiska Active Directory federacyjnego Services (AD FS).

> [!Note]
> Wszystkie inne bezpieczeństwo kluczy i ciągów, w tym kontrolera zarządzania płytą GŁÓWNĄ i przełączać hasła, hasła do kont użytkowników i administratorów są nadal ręcznie aktualizowane przez administratora.

> [!Important]
> Począwszy od wersji 1811 usługi Azure Stack, klucza tajnego obrotu został rozdzielony certyfikatów wewnętrznych i zewnętrznych.

W celu zachowania integralności infrastruktury Azure Stack, Operatorzy będą potrzebowali możliwości okresowo Obróć klucze tajne swoją infrastrukturę, częstotliwości, które są zgodne z wymaganiami dotyczącymi zabezpieczeń w organizacji.

### <a name="rotating-secrets-with-external-certificates-from-a-new-certificate-authority"></a>Obracanie wpisów tajnych za pomocą certyfikatów zewnętrznych z nowego urzędu certyfikacji

Usługa Azure Stack obsługuje obrotu wpisu tajnego za pomocą certyfikatów zewnętrznych z nowego certyfikatu urzędu certyfikacji w następujących kontekstów się:

|Zainstalowany certyfikat urzędu certyfikacji.|Obróć do urzędu certyfikacji|Obsługiwane|Obsługiwane wersje usługi Azure Stack|
|-----|-----|-----|-----|
|Z podpisem własnym|To Enterprise|Obsługiwane|1903 i nowsze|
|Z podpisem własnym|Aby z podpisem własnym|Nieobsługiwane||
|Z podpisem własnym|Na wartość publiczne<sup>*</sup>|Obsługiwane|1803 i nowsze|
|Dla przedsiębiorstw|To Enterprise|Obsługiwane. Od wersji 1803 1903: obsługiwane tak długo, jak klienci używają tego samego przedsiębiorstwa urzędu certyfikacji, ponieważ używany we wdrożeniu|1803 i nowsze|
|Dla przedsiębiorstw|Aby z podpisem własnym|Nieobsługiwane||
|Dla przedsiębiorstw|Na wartość publiczne<sup>*</sup>|Obsługiwane|1803 i nowsze|
|Z publicznego<sup>*</sup>|To Enterprise|Obsługiwane|1903 i nowsze|
|Z publicznego<sup>*</sup>|Aby z podpisem własnym|Nieobsługiwane||
|Z publicznego<sup>*</sup>|Na wartość publiczne<sup>*</sup>|Obsługiwane|1803 i nowsze|

<sup>*</sup>Wskazuje, że publiczne urzędy certyfikacji są te, które są dostępne w ramach programu Windows zaufanych głównych. Pełną listę można znaleźć w artykule [programu zaufanych certyfikatów głównych firmy Microsoft: Uczestnicy (począwszy od 27 czerwca 2017 r.)](https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca).

## <a name="alert-remediation"></a>Korygowanie alertu

W przypadku wpisów tajnych w ciągu 30 dni wygaśnięcia, następujące alerty są generowane w portalu administratora:

- Wygaśnięcie hasła konta usługi oczekującej
- Wygaśnięcie certyfikatu wewnętrznego oczekujące
- Wygaśnięcie certyfikatu zewnętrznego oczekujące

Obrót wpisu tajnego wykonując poniższe instrukcje uruchamiania będą skorygować te alerty.

> [!Note]
> Środowiska platformy Azure Stack w wersji pre-1811 może zostać wyświetlony alerty oczekujące certyfikatu wewnętrznego lub wygaśnięcia klucza tajnego.
> Te alerty są niedokładne i należy ją ignorować bez konieczności uruchamiania wewnętrznego obrotu wpisu tajnego.
> Alerty niedokładne wygaśnięciu wpisu tajnego wewnętrzne są znany problem, który został rozwiązany w 1811 — wewnętrzne, że klucze tajne nie wygasają, chyba że środowiska był aktywny przez dwa lata.

## <a name="pre-steps-for-secret-rotation"></a>Kroki przed operacją dla wpisu tajnego obrotu

   > [!IMPORTANT]
   > Jeśli została już wykonana obrotu wpisu tajnego w środowisku usługi Azure Stack należy zaktualizować system do wersji 1811 lub nowszej, przed wykonaniem ponownie wpisu tajnego obrotu.
   > Obrót wpisu tajnego musi zostać wykonana przez za pośrednictwem [uprzywilejowanych punktu końcowego](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint) i wymaga poświadczeń usługi Azure Stack operatora.
   > Jeśli w środowisku usługi Azure Stack operatorem(-ami) nie wiadomo, czy uruchomiono obrotu wpisu tajnego w swoim środowisku, zaktualizuj 1811 przed wykonaniem ponownie wpisu tajnego obrotu.

1. Zdecydowanie zaleca się aktualizacji Twojego wystąpienia usługi Azure Stack do wersji 1811.

    > [!Note] 
    > W przypadku wersji pre-1811 nie trzeba wymienić wpisów tajnych, aby dodać rozszerzenie hosta certyfikaty. Należy wykonać instrukcje przedstawione w artykule [przygotować się do hosta rozszerzenia dla usługi Azure Stack](azure-stack-extension-host-prepare.md) Aby dodać rozszerzenie hosta certyfikaty.

2. Podczas rotacji wpisów tajnych usługi Azure Stack operatorzy mogą obserwować automatyczne otwieranie i zamykanie alertów.  To zachowanie jest przewidywane i alerty można zignorować.  Operatory można sprawdzić poprawność te alerty, uruchamiając **AzureStack testu**.  Dla operatorów do monitorowania za pomocą programu SCOM systemy usługi Azure Stack, wprowadzenie do systemu w trybie konserwacji uniemożliwi ich systemami ITSM osiągnięcie tych alertów, ale będzie w dalszym ciągu alert, jeśli system usługi Azure Stack staje się niedostępny.

3. Powiadom użytkowników, wszelkich operacji konserwacji. Planowanie okna obsługi normalnych, jak to możliwe, podczas poza godzinami. Operacje konserwacji może mieć wpływ na użytkownika obciążeń i operacje w portalu.

    > [!Note]
    > Następne kroki stosowane tylko wtedy, gdy obracanie zewnętrzne wpisy tajne w usłudze Azure Stack.

4. Uruchom **[AzureStack testu](https://docs.microsoft.com/azure/azure-stack/azure-stack-diagnostic-test)** i upewnij się, wszystkie dane wyjściowe testu są w dobrej kondycji przed rotacji kluczy tajnych.
5. Przygotuj nowy zestaw zastępczy certyfikatów zewnętrznych. Nowy zestaw pasuje do specyfikacji certyfikatu, opisane w temacie [wymagania dotyczące certyfikatów infrastruktury kluczy publicznych do usługi Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-pki-certs). Można wygenerować certyfikat podpisywania żądania (CSR) do zakupu lub tworzenia nowych certyfikatów wykonując kroki opisane w [generowanie certyfikatów PKI](https://docs.microsoft.com/azure/azure-stack/azure-stack-get-pki-certs) i przygotować je do użytku w środowisku usługi Azure Stack, korzystając z procedury [ Przygotowywanie certyfikatów PKI w usłudze Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-prepare-pki-certs). Pamiętaj sprawdzić poprawność certyfikatów, należy przygotować wykonując kroki opisane w [sprawdzania poprawności certyfikatów PKI](https://docs.microsoft.com/azure/azure-stack/azure-stack-validate-pki-certs).
6. Store wykonywanie kopii zapasowej certyfikatów używanych dla obrotu w bezpiecznej lokalizacji kopii zapasowej. Jeśli Twoje obrotu uruchomiona, a następnie kończy się niepowodzeniem, Zamień certyfikaty w udziale plików kopii zapasowych przed ponownym uruchomieniem obrót. Należy pamiętać, wykonywania kopii zapasowych w bezpiecznej lokalizacji kopii zapasowej.
7. Utwórz udział plików, w której będziesz mieć dostęp z ERCS maszyn wirtualnych. Udział plików musi być czytelny i zapisywalny dla **CloudAdmin** tożsamości.
8. Otwórz konsolę programu PowerShell ISE z komputera, na których mają dostęp do udziału plików. Przejdź do Twojego udziału plików.
9. Uruchom **[CertDirectoryMaker.ps1](https://www.aka.ms/azssecretrotationhelper)** utworzyć wymagane katalogi zewnętrzne certyfikaty.

> [!IMPORTANT]
> Skrypt CertDirectoryMaker utworzy strukturę folderów, które będą spełniać wymagania:
>
> **.\Certificates\AAD** lub ***.\Certificates\ADFS*** w zależności od dostawcy tożsamości używane na potrzeby usługi Azure Stack
>
> Jest priorytetowe znaczenie, który kończy się Twoja struktura folderów **AAD** lub **usług AD FS** folderów i wszystkich podkatalogach znajdują się w tej struktury; w przeciwnym razie **Start-SecretRotation**rozpocznie pracę przy użyciu:
> ```powershell
> Cannot bind argument to parameter 'Path' because it is null.
> + CategoryInfo          : InvalidData: (:) [Test-Certificate], ParameterBindingValidationException
> + FullyQualifiedErrorId : ParameterArgumentValidationErrorNullNotAllowed,Test-Certificate
> + PSComputerName        : xxx.xxx.xxx.xxx
> ```
>
> Jak widać Masaż błąd wskazują, że występuje problem z dostępem do Twojego udziału plików, ale w rzeczywistości jest strukturę folderów, która jest wymuszana w tym miejscu.
> Więcej informacji można znaleźć w module sprawdzania gotowości AzureStack Microsoft - [PublicCertHelper modułu](https://www.powershellgallery.com/packages/Microsoft.AzureStack.ReadinessChecker/1.1811.1101.1/Content/CertificateValidation%5CPublicCertHelper.psm1)
>
> Jest również ważne, czy Twoja struktura folderów udziału plików zaczyna się od **certyfikaty** folder, w przeciwnym razie również zakończą się podczas weryfikacji.
> Instalowanie udziału plików powinna wyglądać **\\ \\ \<Adres_ip >\\\<nazwa_udziału >\\** i powinna zawierać folderu  **Certificates\AAD** lub **Certificates\ADFS** wewnątrz.
>
> Na przykład:
> - Udział plików =  **\\ \\ \<Adres_ip >\\\<nazwa_udziału >\\**
> - CertFolder = **Certificates\AAD**
> - FullPath =  **\\ \\ \<Adres_ip >\\\<nazwa_udziału > \Certificates\AAD**

## <a name="rotating-external-secrets"></a>Obracanie zewnętrznych wpisów tajnych

Aby obrócić zewnętrzne wpisy tajne:

1. W nowo utworzonej **\Certificates\\\<IdentityProvider >** Katalog utworzony w krokach wstępne umieścić nowy zestaw zastępczy certyfikaty zewnętrzne w strukturze katalogów zgodnie z opisem w Format opisany w sekcji wymagane certyfikaty [wymagania dotyczące certyfikatów infrastruktury kluczy publicznych do usługi Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-pki-certs#mandatory-certificates).

    Przykład struktura folderów dla dostawcy tożsamości usługi AAD:
    ```powershell
        <ShareName>
        │   │
        │   ├───Certificates
        │   └───AAD
        │       ├───ACSBlob
        │       │       <CertName>.pfx
        │       │
        │       ├───ACSQueue
        │       │       <CertName>.pfx
        │       │
        │       ├───ACSTable
        │       │       <CertName>.pfx
        │       │
        │       ├───Admin Extension Host
        │       │       <CertName>.pfx
        │       │
        │       ├───Admin Portal
        │       │       <CertName>.pfx
        │       │
        │       ├───ARM Admin
        │       │       <CertName>.pfx
        │       │
        │       ├───ARM Public
        │       │       <CertName>.pfx
        │       │
        │       ├───KeyVault
        │       │       <CertName>.pfx
        │       │
        │       ├───KeyVaultInternal
        │       │       <CertName>.pfx
        │       │
        │       ├───Public Extension Host
        │       │       <CertName>.pfx
        │       │
        │       └───Public Portal
        │               <CertName>.pfx

    ```

2. Tworzenie sesji programu PowerShell przy użyciu [uprzywilejowanych punktu końcowego](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint) przy użyciu **CloudAdmin** konta, a także przechowywać sesji jako zmienną. Ta zmienna będzie używany jako parametr w następnym kroku.

    > [!IMPORTANT]  
    > Wprowadź sesję, nie przechowują sesji jako zmienną.

3. Uruchom  **[Invoke-Command](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/Invoke-Command?view=powershell-5.1)**. Przekaż zmiennej sesji użytkownika uprzywilejowanego punktu końcowego programu PowerShell jako **sesji** parametru.

4. Uruchom **Start SecretRotation** z następującymi parametrami:
    - **PfxFilesPath**  
    Określ ścieżkę sieciową do utworzonego wcześniej katalogu certyfikatów.  
    - **PathAccessCredential**  
    Obiekt PSCredential poświadczeń do udziału.
    - **CertificatePassword**  
    Bezpieczny ciąg hasła używane dla wszystkich utworzony plik certyfikatu pfx.

5. Zaczekaj, aż Obróć klucze tajne. Obrót tajny zewnętrznego zwykle trwa około godzinę.

    Po pomyślnym ukończeniu rotacji wpisu tajnego, zostanie wyświetlona konsola **ogólny stan akcji: Powodzenie**.

    > [!Note]
    > Jeśli wpisu tajnego obrotu zakończy się niepowodzeniem, postępuj zgodnie z instrukcjami w komunikacie o błędzie i uruchom ponownie **Start SecretRotation** z **-Uruchom ponownie** parametru.

    ```powershell
    Start-SecretRotation -ReRun
    ```
    Się z pomocą techniczną, jeśli wystąpią powtórzone błędy klucza tajnego obrotu.

6. Po pomyślnym zakończeniu obrotu wpisu tajnego usunąć certyfikaty z udziału utworzonego w kroku wstępnego i zapisanie ich w bezpiecznej lokalizacji kopii zapasowej.

## <a name="walkthrough-of-secret-rotation"></a>Przewodnik dotyczący rotacji wpisu tajnego

W poniższym przykładzie programu PowerShell pokazuje, poleceń cmdlet i parametrów do uruchomienia w celu obracania wpisów tajnych.

```powershell
# Create a PEP Session
winrm s winrm/config/client '@{TrustedHosts= "<IpOfERCSMachine>"}'
$PEPCreds = Get-Credential
$PEPSession = New-PSSession -ComputerName <IpOfERCSMachine> -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

# Run Secret Rotation
$CertPassword = ConvertTo-SecureString "<CertPasswordHere>" -AsPlainText -Force
$CertShareCreds = Get-Credential
$CertSharePath = "<NetworkPathOfCertShare>"
Invoke-Command -Session $PEPSession -ScriptBlock {
    Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCreds -CertificatePassword $using:CertPassword
}
Remove-PSSession -Session $PEPSession
```

## <a name="rotating-only-internal-secrets"></a>Obracanie tylko wewnętrzne wpisów tajnych

> [!Note]
> Wewnętrzny obrotu klucz tajny powinno mieć miejsce tylko, jeśli podejrzewasz, że wewnętrzny klucz tajny przejął zamierzających lub otrzymane alertu (kompilacja 1811 lub nowszej) wskazująca, że certyfikaty wewnętrzne wkrótce wygasną.
> Środowiska platformy Azure Stack w wersji pre-1811 może zostać wyświetlony alerty oczekujące certyfikatu wewnętrznego lub wygaśnięcia klucza tajnego.
> Te alerty są niedokładne i należy ją ignorować bez konieczności uruchamiania wewnętrznego obrotu wpisu tajnego.
> Alerty niedokładne wygaśnięciu wpisu tajnego wewnętrzne są znany problem, który został rozwiązany w 1811 — wewnętrzne, że klucze tajne nie wygasają, chyba że środowiska był aktywny przez dwa lata.

1. Tworzenie sesji programu PowerShell przy użyciu [uprzywilejowanych punktu końcowego](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint).
2. W sesji uprzywilejowanych punktu końcowego Uruchom **Start SecretRotation-wewnętrznego**.

    > [!Note]
    > Nie wymaga środowiska platformy Azure Stack w wersji pre-1811 **— wewnętrzne** flagi. **Start-SecretRotation** dyżury tylko wewnętrzne wpisów tajnych.

3. Zaczekaj, aż Obróć klucze tajne.

Po pomyślnym ukończeniu rotacji wpisu tajnego, zostanie wyświetlona konsola **ogólny stan akcji: Powodzenie**.
    > [!Note]
    > If secret rotation fails, follow the instructions in the error message and rerun **Start-SecretRotation** with the  **–Internal** and **-ReRun** parameters.  

```powershell
Start-SecretRotation -Internal -ReRun
```

Się z pomocą techniczną, jeśli wystąpią powtórzone błędy klucza tajnego obrotu.

## <a name="start-secretrotation-reference"></a>Odwołanie do rozpoczęcia SecretRotation

Obraca się wpisy tajne w systemie usługi Azure Stack. Wykonać tylko względem usługi Azure Stack punktu końcowego w uprzywilejowanych.

### <a name="syntax"></a>Składnia

#### <a name="for-external-secret-rotation"></a>Dla zewnętrznych obrotu wpisu tajnego

```powershell
Start-SecretRotation [-PfxFilesPath <string>] [-PathAccessCredential <PSCredential>] [-CertificatePassword <SecureString>]  
```

#### <a name="for-internal-secret-rotation"></a>Dla wewnętrznej obrotu wpisu tajnego

```powershell
Start-SecretRotation [-Internal]  
```

#### <a name="for-external-secret-rotation-rerun"></a>Dla zewnętrznych obrotu wpisu tajnego, uruchom ponownie

```powershell
Start-SecretRotation [-ReRun]
```

#### <a name="for-internal-secret-rotation-rerun"></a>Dla wewnętrznej obrotu wpisu tajnego, uruchom ponownie

```powershell
Start-SecretRotation [-ReRun] [-Internal]
```

### <a name="description"></a>Opis

**Start SecretRotation** polecenia cmdlet obraca się wpisy tajne infrastruktury systemu Azure Stack. Domyślnie obraca się tylko certyfikaty wszystkich punktów końcowych infrastruktury sieci zewnętrznej. Z flagi wewnętrznej wewnętrznej infrastruktury kluczy tajnych zostaną obrócone. W przypadku rotacji punktów końcowych infrastruktury sieci zewnętrznej, **Start SecretRotation** powinno być uruchamiane z **Invoke-Command** blok skryptu ze środowiskiem usługi Azure Stack użytkownika uprzywilejowanego sesji punktu końcowego przekazana jako **sesji** parametru.

### <a name="parameters"></a>Parametry

| Parametr | Type | Wymagane | Pozycja | Domyślne | Opis |
| -- | -- | -- | -- | -- | -- |
| PfxFilesPath | String  | False  | o nazwie  | Brak  | Ścieżka udziału plików do **\Certificates** katalogu zawierającego wszystkich zewnętrznych sieci certyfikaty punktu końcowego. Wymagany tylko w przypadku rotacji zewnętrznych wpisów tajnych. Katalog end musi być **\Certificates**. |
| CertificatePassword | SecureString | False  | o nazwie  | Brak  | Hasło dla wszystkich certyfikatów w PfXFilesPath —. Wymagane wartości, jeśli PfxFilesPath jest udostępniany, gdy są obracane zewnętrznych wpisów tajnych. |
| Wewnętrzny | String | False | o nazwie | Brak | Flagi wewnętrznej musi być używane w dowolnym momencie operatorów usługi Azure Stack chce wymienić wewnętrznej infrastruktury kluczy tajnych. |
| PathAccessCredential | PSCredential | False  | o nazwie  | Brak  | Poświadczenie programu PowerShell dla udziału plików z **\Certificates** katalogu zawierającego wszystkich zewnętrznych sieci certyfikaty punktu końcowego. Wymagany tylko w przypadku rotacji zewnętrznych wpisów tajnych.  |
| Uruchom ponownie | SwitchParameter | False  | o nazwie  | Brak  | Uruchom ponownie muszą być używane w dowolnym momencie obrotu wpisu tajnego jest ponowieniu po nieudanej próbie. |

### <a name="examples"></a>Przykłady

#### <a name="rotate-only-internal-infrastructure-secrets"></a>Obróć tylko wpisy tajne wewnętrznej infrastruktury

Musi to być uruchamiane za pomocą usługi Azure Stack [środowiska użytkownika uprzywilejowanego punktu końcowego](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint).

```powershell
PS C:\> Start-SecretRotation -Internal
```

To polecenie obraca wszystkich wpisów tajnych infrastruktury, połączenie z sieci wewnętrznej usługi Azure Stack.

#### <a name="rotate-only-external-infrastructure-secrets"></a>Obróć tylko wpisy tajne zewnętrznych infrastruktury  

```powershell
# Create a PEP Session
winrm s winrm/config/client '@{TrustedHosts= "<IpOfERCSMachine>"}'
$PEPCreds = Get-Credential
$PEPSession = New-PSSession -ComputerName <IpOfERCSMachine> -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

# Create Credentials for the fileshare
$CertPassword = ConvertTo-SecureString "<CertPasswordHere>" -AsPlainText -Force
$CertShareCreds = Get-Credential
$CertSharePath = "<NetworkPathOfCertShare>"
# Run Secret Rotation
Invoke-Command -Session $PEPSession -ScriptBlock {  
    Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCreds -CertificatePassword $using:CertPassword
}
Remove-PSSession -Session $PEPSession
```

To polecenie obraca certyfikaty protokołu TLS, używane dla punktów końcowych infrastruktury sieci zewnętrznej usługi Azure Stack.

#### <a name="rotate-internal-and-external-infrastructure-secrets-pre-1811-only"></a>Obróć klucze tajne wewnętrznych i zewnętrznych infrastruktury (**pre 1811** tylko)

> [!IMPORTANT]
> To polecenie dotyczy tylko usługi Azure Stack **pre 1811** jako obrót podzielonego dla wewnętrznych i zewnętrznych certyfikatów.
>
> **Z *1811 +* nie można obrócić, zarówno wewnętrzne i zewnętrzne certyfikatów kolejne!!!**

```powershell
# Create a PEP Session
winrm s winrm/config/client '@{TrustedHosts= "<IpOfERCSMachine>"}'
$PEPCreds = Get-Credential
$PEPSession = New-PSSession -ComputerName <IpOfERCSMachine> -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

# Create Credentials for the fileshare
$CertPassword = ConvertTo-SecureString "<CertPasswordHere>" -AsPlainText -Force
$CertShareCreds = Get-Credential
$CertSharePath = "<NetworkPathOfCertShare>"
# Run Secret Rotation
Invoke-Command -Session $PEPSession -ScriptBlock {
    Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCreds -CertificatePassword $using:CertPassword
}
Remove-PSSession -Session $PEPSession
```

To polecenie obraca wszystkich wpisów tajnych infrastruktury, połączenie z sieci wewnętrznej usługi Azure Stack, a także certyfikaty protokołu TLS, używane dla punktów końcowych infrastruktury sieci zewnętrznej usługi Azure Stack. Start-SecretRotation obraca wszystkich wpisów tajnych, które są generowane przez stos, a ponieważ podano certyfikatów oraz certyfikatów jest zewnętrzny punkt końcowy również zostaną obrócone.  

## <a name="update-the-baseboard-management-controller-bmc-credential"></a>Zaktualizuj poświadczenia kontrolera zarządzania płytą główną

Kontroler zarządzania płytą główną (BMC) monitoruje stan fizycznych serwerów. Instrukcje na temat aktualizowania nazwę konta użytkownika i hasło kontrolera BMC i specyfikacje różnią się zależnie od dostawcy sprzętu producenta sprzętu (OEM). Należy zaktualizować hasła dla składników usługi Azure Stack w regularnych odstępach czasu.

1. Aktualizacja kontrolera zarządzania płytą GŁÓWNĄ na serwerach fizycznych w usłudze Azure Stack, postępując zgodnie z instrukcjami podanymi przez producenta OEM. Nazwa użytkownika i hasło dla każdego kontrolera zarządzania płytą GŁÓWNĄ w środowisku musi być taka sama. Należy pamiętać, że kontrolera zarządzania płytą GŁÓWNĄ nazwy użytkowników nie może przekraczać 16 znaków.
2. Otwórz punkt końcowy uprzywilejowanego w sesji usługi Azure Stack. Aby uzyskać instrukcje, zobacz [przy użyciu uprzywilejowanych punktu końcowego w usłudze Azure Stack](azure-stack-privileged-endpoint.md).
3. Po PowerShell wiersz został zmieniony na **[adres IP lub maszyny Wirtualnej ERCS name]: PS >** lub **[azs-ercs01]: PS >**, w zależności od środowiska, uruchom `Set-BmcCredential` , uruchamiając `Invoke-Command`. Przekaż zmiennej sesji użytkownika uprzywilejowanego punktu końcowego, jako parametr. Na przykład:

    ```powershell
    # Interactive Version
    $PEPIp = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PEPCreds = Get-Credential "<Domain>\CloudAdmin" -Message "PEP Credentials"
    $NewBmcPwd = Read-Host -Prompt "Enter New BMC password" -AsSecureString
    $NewBmcUser = Read-Host -Prompt "Enter New BMC user name"

    $PEPSession = New-PSSession -ComputerName $PEPIp -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

    Invoke-Command -Session $PEPSession -ScriptBlock {
        # Parameter BmcPassword is mandatory, while the BmcUser parameter is optional.
        Set-BmcCredential -BmcPassword $using:NewBmcPwd -BmcUser $using:NewBmcUser
    }
    Remove-PSSession -Session $PEPSession
    ```

    Za pomocą statycznych wersja programu PowerShell i hasła jako wiersze kodu:

    ```powershell
    # Static Version
    $PEPIp = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PEPUser = "<Privileged Endpoint user for example Domain\CloudAdmin>"
    $PEPPwd = ConvertTo-SecureString "<Privileged Endpoint Password>" -AsPlainText -Force
    $PEPCreds = New-Object System.Management.Automation.PSCredential ($PEPUser, $PEPPwd)
    $NewBmcPwd = ConvertTo-SecureString "<New BMC Password>" -AsPlainText -Force
    $NewBmcUser = "<New BMC User name>"

    $PEPSession = New-PSSession -ComputerName $PEPIp -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

    Invoke-Command -Session $PEPSession -ScriptBlock {
        # Parameter BmcPassword is mandatory, while the BmcUser parameter is optional.
        Set-BmcCredential -BmcPassword $using:NewBmcPwd -BmcUser $using:NewBmcUser
    }
    Remove-PSSession -Session $PEPSession
    ```

## <a name="next-steps"></a>Kolejne kroki

[Dowiedz się więcej na temat zabezpieczeń usługi Azure Stack](azure-stack-security-foundations.md)
