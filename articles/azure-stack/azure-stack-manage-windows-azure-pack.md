---
title: Zarządzanie maszynami wirtualnymi systemu Windows Azure Pack ze stosu Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zarządzać maszynami wirtualnymi systemu Windows Azure Pack (map) z aplikacji portal użytkowników w stosie usługi Azure.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 213c2792-d404-4b44-8340-235adf3f8f0b
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2018
ms.author: mabrigg
ms.openlocfilehash: a7e4896c84938b392a86f4d9609c4932324c785d
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2018
ms.locfileid: "29735828"
---
# <a name="manage-windows-azure-pack-virtual-machines-from-azure-stack"></a>Zarządzanie maszynami wirtualnymi systemu Windows Azure Pack ze stosu Azure

*Dotyczy: Azure stosu Development Kit*

W zestawie Azure stosu Development Kit można włączyć dostęp z portalu Azure stosu użytkownika dla dzierżawy maszyn wirtualnych uruchomionych w systemie Windows Azure Pack. Użytkownicy mogą korzystać z portalu Azure stosu, do zarządzania ich istniejących maszyn wirtualnych IaaS i sieci wirtualnych. Te zasoby są udostępniane w systemie Windows Azure Pack za pomocą komponentów platformy Service Provider Foundation (SPF) i Virtual Machine Manager (VMM). Użytkownicy mogą w szczególności:

* Przeglądanie zasobów.
* Sprawdź wartości konfiguracji
* Zatrzymanie lub uruchomienie maszyny wirtualnej
* Połącz z maszyną wirtualną za pomocą protokołu RDP (Remote Desktop)
* Utwórz i Zarządzaj punktami kontrolnymi
* Usuń maszyny wirtualne i wirtualne sieci

Ta funkcja jest dostępna przez łącznik usługi Windows Azure Pack dla stosu Azure (wersja zapoznawcza). W tym artykule przedstawiono sposób konfigurowania łącznika środowiska Azure stosu z jednym węzłem.

Dla tej wersji zapoznawczej łącznika należy pamiętać o następujących czynności:
* Łącznik usługi Windows Azure Pack należy używać tylko w środowiskach testowych (dla stosu Azure i pakietu Windows Azure Pack), a nie we wdrożeniach produkcyjnych.
* Musi mieć systemu Windows Azure Pack aktualizacji WYKONANA 9.1 lub nowszy i System Center SPF i VMM pakietem zbiorczym aktualizacji 9 lub nowszy.
* Składniki programu VMM i SPF można System Center 2012 R2 lub System Center 2016.
* Na obu stosu Azure i pakietu Windows Azure Pack, należy wykonać czynności konfiguracyjne.
* Instrukcje dotyczą środowiskach innych niż - Cloud Platform System CPS.
* Aby przejrzeć znanych problemów, zobacz [Microsoft Azure stosu Rozwiązywanie problemów z](azure-stack-troubleshooting.md).


## <a name="architecture"></a>Architektura
Na poniższym diagramie przedstawiono główne składniki łącznik usługi Windows Azure Pack.

![Składniki łącznik usługi Windows Azure Pack](media/azure-stack-manage-wap/image1.png)

Zwróć uwagę następujące informacje:
* Portal użytkowników usługi Azure stosu uzyskuje dostęp do informacji o zasobie z obu chmury (Azure stosu i pakietu Windows Azure Pack).
* Użytkownik musi mieć konto, który jest prawidłowy w obu środowiskach.
* Portalu Azure stosu użytkownika musi mieć dostęp do składników, w systemie Windows Azure Pack.
* W **WAP** sekcji diagramu, można zobaczyć nowych modułów łącznik usługi Windows Azure Pack (rozszerzenie WAP i łącznika) i istniejące API systemu Windows Azure pakiet dzierżawy ze składnikami SPF i VMM.


## <a name="identity-management"></a>Zarządzanie tożsamościami
Interfejs API systemu Windows Azure Pack dzierżawcy muszą ufać usługi tokenu zabezpieczającego stosu Azure (STS).

Gdy użytkownik wykona akcję za pośrednictwem portalu Azure stosu, którego element docelowy zasobów systemu Windows Azure Pack, portal używa interfejsu API systemu Windows Azure Pack dzierżawy. W związku z tym token uwierzytelniania podany użytkownik musi pochodzić z zaufanego STS. Zobacz poniższy diagram:

![Diagram uwierzytelniania łącznika usługi Windows Azure Pack](media/azure-stack-manage-wap/image2.png)

W środowisku programistycznym zestawu Windows Azure Pack i stosu Azure mają dostawców niezależnych tożsamości. W obu dostawców tożsamości, użytkowników, którzy korzystają z portalu Azure stosu obu środowiskach musi mieć tej samej nazwy głównej nazwy (UPN) użytkownika. Na przykład konto  *azurestackadmin@azurestack.local*  również powinny istnieć w STS dla pakietu systemu Windows Azure. W przypadku, gdy usługi AD FS nie jest ustawiona do obsługi relacje zaufania wychodzącego, zostanie ustanowiona relacja zaufania ze składników pakietu Windows Azure Pack (interfejs API dzierżawcy) do stosu Azure wystąpienia usług AD FS.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="download-the-windows-azure-pack-connector"></a>Pobierz łącznik usługi Windows Azure Pack
Na [Microsoft Download Center](https://aka.ms/wapconnectorazurestackdlc), Pobierz plik .exe i wyodrębnij go na komputerze lokalnym. Później skopiuj zawartość do komputera, który można uzyskać dostępu do środowiska systemu Windows Azure Pack.

### <a name="deployment-option-requirement"></a>Wymagania opcji wdrożenia
Aby zintegrować z dodatkiem Service Pack systemu Windows Azure, można wdrożyć stosu Azure za pomocą usług AD FS opcja lub Azure Active Directory.

### <a name="connectivity-requirements"></a>Wymagania dotyczące łączności
1. Na komputerze, na którym masz dostęp do portalu użytkownika stosu Azure upewnij się, że masz dostęp do portalu dzierżawcy pakietu Windows Azure Pack za pośrednictwem przeglądarki sieci web.
2. AzS WASP01 maszyny wirtualnej na stosie Azure musi mieć możliwość łączenia się z komputerem portalu dzierżawcy pakietu Windows Azure Pack. Użyj Ping.exe, aby sprawdzić łączność sieciową. 
3.  Musi mieć prawidłowe certyfikaty dla nowej usługi łącznika. Te certyfikaty SSL musi być wystawiony przez zaufany urząd certyfikacji (CA). Nie można używać certyfikatów z podpisem własnym. Certyfikaty SSL musi być zaufany przez stos Azure (w szczególności AzS WASP01 maszyny Wirtualnej) i dowolnym innym komputerze dzierżawcy mogą uzyskiwać dostęp do portalu użytkownika stosu Azure.
 
    >[!NOTE]
    Ponieważ AzS WASP01 systemem Windows Server z opcją instalacji Server Core, można użyć narzędzia wiersza polecenia, takich jak Certutil.ext, aby zaimportować certyfikat. Na przykład można skopiować plik .cer c:\temp na AzS WASP01, a następnie uruchom polecenie **"CA" "c:\temp\certname.cer" certutil - addstore**.

4.  Nawiązać połączenie RDP do maszyn wirtualnych dzierżawcy pakietu Windows Azure Pack za pośrednictwem portalu Azure stosu środowiska systemu Windows Azure Pack muszą zezwalać na ruch pulpitu zdalnego do maszyny wirtualne dzierżawców.
5.  Dla łączności między maszynami wirtualnymi dzierżawy pakietu Windows Azure Pack i maszyn wirtualnych dzierżawców Azure stosu ich zewnętrzne adresy IP muszą być routingu między dwoma środowiskami. Połączeń może również obejmować kojarzenia serwera DNS do rozpoznawania nazw maszyny wirtualnej między środowiskami.

### <a name="iis-requirements"></a>Wymagania dotyczące usług IIS
Komputer, który obsługuje portal dzierżawcy pakietu Windows Azure Pack (które mogą być fizycznego hosta, maszyny wirtualnej lub wiele maszyn wirtualnych) musi mieć rozszerzenie ponowne zapisywanie adresów URL w usługach IIS zainstalowane. Jeśli nie jest już zainstalowany, możesz pobrać go z [tutaj](https://www.iis.net/downloads/microsoft/url-rewrite). Jeśli wiele maszyn wirtualnych hostowania portalu dzierżawcy, należy zainstalować rozszerzenie na każdej maszynie wirtualnej.

## <a name="configure-azure-stack"></a>Konfigurowanie usługi Azure stosu
Aby skonfigurować łącznik usługi Windows Azure Pack, należy włączyć tryb usługi chmury w portalu Azure stosu użytkownika. Ten tryb umożliwia użytkownikom z chmury, która umożliwia dostęp do zasobów.

Aby włączyć tryb usługi chmury, musisz Uruchom skrypt AzurePackConnector.ps1 Dodaj po wdrożeniu stosu Azure. W poniższej tabeli opisano parametry skryptu.


|  Parametr | Opis | Przykład |   
| -------- | ------------- | ------- |  
| AzurePackClouds | Identyfikatorów URI systemu Windows Azure Pack łączników. Te identyfikatory URI powinna odpowiadać portali dzierżawy pakietu Windows Azure Pack. | @{CloudName = "AzurePack1"; CloudEndpoint = "https://waptenantportal1:40005"},@{CloudName = "AzurePack2"; CloudEndpoint = "https://waptenantportal2:40005"}<br><br>  (Domyślnie wartość portu jest 40005). |  
| AzureStackCloudName | Etykieta do reprezentowania lokalnego chmury Azure stosu.| "AzureStack" |
| DisableMultiCloud | Przełącz, aby wyłączyć tryb usługi chmury.| ND |
| | |

Można uruchomić skryptu AzurePackConnector.ps1 Dodaj natychmiast po wdrożeniu lub nowszej. Aby uruchomić skrypt natychmiast po wdrożeniu, należy użyć tej samej sesji programu Windows PowerShell, gdzie wdrożenia stosu Azure ukończone. W przeciwnym razie można otworzyć sesję programu Windows PowerShell jako administrator (zalogowany jako konto azurestackadmin).

1. Uruchom skrypt AzurePackConnector.ps1 Dodaj przy użyciu następujących poleceń (z wartościami specjalnie dla własnego środowiska). Zwróć uwagę, że AzurePackConnector Dodaj skrypt umożliwia dodanie więcej niż jeden łącznik usługi Windows Azure Pack punktu końcowego.
 
 ```powershell
    $cred = New-Object System.Management.Automation.PSCredential("cloudadmin@azurestack.local", `
    (ConvertTo-SecureString -String "<password>" -AsPlainText -Force))
    $session = New-PSSession -ComputerName 'azs-ercs01' `
     -Credential $cred `
     -ConfigurationName PrivilegedEndpoint `
     -Authentication Credssp

    # Enable Multicloud
    Invoke-Command -Session $session -ScriptBlock { Add-AzurePackConnector -AzurePackClouds `
    @{CloudName = "AzurePack_1"; CloudEndpoint = "https://waptenantportal1:40005"},`
    @{CloudName = "AzurePack_2"; CloudEndpoint = "https://waptenantportal2:40005" } `
    -AzureStackCloudName "AzureStack" }

 ```
> [!NOTE]
> W bieżącej kompilacji występuje problem w przypadku, gdy po zakończeniu skryptu Dodaj AzurePackConnector, pozostaje w pętli sondowania przez dłuższy czas (kilka minut) do momentu zakończenia. Po wyświetleniu komunikatu **pełne: "Konfigurowanie pakietu łącznika usługi Azure" krok stanu: 'Success'**, można zatrzymać skrypt lub poczekaj, aż zatrzymuje się samodzielnie. Nie będzie rozsądne różnice, ponieważ konfiguracja już zakończyła się pomyślnie.

2. Zwróć uwagę na pliki wyjściowe, które są tworzone przez ten skrypt, po jednej dla każdego środowiska systemu Windows Azure Pack określony. Pliki znajdują się w: \\\su1fileserver\SU1_Infrastructure_1\AzurePackConnectorOutput. Te pliki zawierają informacje, które jest wymagane do skonfigurowania środowiska systemu Windows Azure Pack docelowej. Ten plik jako parametr są przekazywane do skryptu później w tej instrukcji. Ten plik zawiera następujące informacje:

    * **AzurePackConnectorEndpoint**: zawiera punkt końcowy do usługi łącznika usługi Windows Azure Pack.
    * **AuthenticationIdentityProviderPartner**: zawiera następującą parą wartości:
        * Interfejs API dzierżawcy systemu Windows Azure Pack musi ufać do akceptowania połączeń z rozszerzenia portalu Azure stosu certyfikat podpisywania tokenu uwierzytelniania.

        * "Obszar" skojarzonego z certyfikatem podpisywania. For example: https://adfs.local.azurestack.global.external/adfs/c1d72562-534e-4aa5-92aa-d65df289a107/.

3.  Przejdź do folderu, który zawiera pliki wyjściowe (\\su1fileserver\SU1_Infrastructure_1\AzurePackConnectorOutput) i skopiuj pliki na komputerze lokalnym. Pliki będą wyglądać podobnie do poniższego: AzurePack-06-27 — 15-50.txt.

4.  Przetestuj konfigurację.

    a. Otwórz przeglądarkę i zaloguj się do portalu użytkowników usługi Azure stosu (https://portal.local.azurestack.external/).
    
    b. Po zalogowaniu się jako dzierżawcy i obciążenia portalu, zostanie wyświetlone błędy dotyczące nie będą mogli pobrać subskrypcji lub rozszerzeń z chmury Azure Pack. Kliknij przycisk **OK** zamknąć te komunikaty. (Te komunikaty o błędach zniknie po skonfigurowaniu pakietu Windows Azure Pack.)

    c. Powiadomienie **chmury** listy rozwijanej w lewym górnym rogu portalu.

    ![Selektor chmury Azure stosu interfejsu użytkownika](media/azure-stack-manage-wap/image3.png)

## <a name="configure-windows-azure-pack"></a>Konfigurowanie systemu Windows Azure Pack
Dla tego łącznika wersji zapoznawczej tylko pakietu Windows Azure Pack wymaga ręcznej konfiguracji.

>[!IMPORTANT]
W tej wersji zapoznawczej Użyj łącznik usługi Windows Azure Pack tylko w środowisku testowym, a nie we wdrożeniach produkcyjnych.

1.  Instalowanie plików MSI łącznika dzierżawy na portalu maszyny wirtualnej systemu Windows Azure Pack i instalowania certyfikatów. (Jeśli masz wiele maszyn wirtualnych z portalu dzierżawcy, należy wykonać ten krok na każdej maszynie wirtualnej.)

    a. W Eksploratorze plików, skopiuj **WAPConnector** folderu (co zostały pobrane wcześniej) do folderu o nazwie **c:\temp** na maszynie wirtualnej portalu dzierżawcy.

    b. Otwórz połączenie RDP lub konsoli maszyny wirtualnej z portalu dzierżawcy.

    c. Przejdź do **c:\temp\wapconnector\setup\scripts**i uruchom **Connector.ps1 instalacji** skrypt, aby zainstalować trzy pliki MSI. Nie są wymagane parametry.

     ```powershell
    cd C:\temp\wapconnector\setup\scripts\

    .\Install-Connector.ps1
    ```
     d. Przejdź do **c:\inetpub** i sprawdź, czy są zainstalowane trzy nowe witryny:

       * MgmtSvc-Connector

       * MgmtSvc-ConnectorExtension

       * MgmtSvc-ConnectorController

    e. Z tej samej **c:\temp\wapconnector\setup\scripts** folderu Uruchom **Certificates.ps1 Konfiguruj** skrypt w celu zainstalowania certyfikatów. Domyślnie użyje sam certyfikat, który jest dostępny dla witryny portalu dzierżawcy w systemie Windows Azure Pack. Upewnij się, że jest to prawidłowy certyfikat (zaufany dla maszyny wirtualnej Azure stosu AzS-WASP01 i dowolnego komputera klienckiego, który uzyskuje dostęp do portalu Azure stosu). W przeciwnym razie nie będzie działać komunikacji. (Można również jawnie przekazywanej odcisku palca certyfikatu jako parametru za pomocą parametru - odcisk palca.)

     ```powershell
        cd C:\temp\wapconnector\setup\scripts\

        .\Configure-Certificates.ps1
    ```

    f. Aby zakończyć konfigurację te trzy usługi, uruchom **WapConnector.ps1 Konfiguruj** skrypt w celu zaktualizowania parametry pliku Web.config.

    |  Parametr | Opis | Przykład |   
    | -------- | ------------- | ------- |  
    | TenantPortalFQDN | Portal dzierżawcy pakietu Windows Azure Pack nazwy FQDN. | tenant.contoso.com | 
    | TenantAPIFQDN | Windows Azure Pack dzierżawcy interfejsu API nazwę FQDN. | tenantapi.contoso.com  |
    | AzureStackPortalFQDN | Portal użytkowników stosu Azure nazwy FQDN. | portal.local.azurestack.external |
    | | |
    
     ```powershell
    .\Configure-WapConnector.ps1 -TenantPortalFQDN "tenant.contoso.com" `
         -TenantAPIFQDN "tenantapi.contoso.com" `
         -AzureStackPortalFQDN "portal.local.azurestack.external"
    ```
    g. Jeśli masz wiele maszyn wirtualnych z portalu dzierżawcy, powtórz krok 1 dla każdego z tych maszyn wirtualnych.

2. Instalowanie nowego pliku MSI interfejsu API dzierżawcy na każdej maszynie wirtualnej Windows Azure Pack — interfejs API dzierżawcy.

    a. Moduł równoważenia obciążenia jest używany, można oznaczyć maszyny wirtualnej w trybie offline.

    b. W Eksploratorze plików, skopiuj **WAPConnector** folderu do folderu o nazwie **c:\temp** na każdej maszynie, interfejs API dzierżawcy.

    c. Skopiuj plik AzurePackConnectorOutput.txt zapisany wcześniej, aby **c:\temp\WAPConnector**.

    d. Otwórz połączenie RDP lub konsoli do maszyny Wirtualnej dzierżawcy interfejsu API kopiowane pliki.
    
    e. Przejdź do **c:\temp\wapconnector\setup\scripts**i uruchom **TenantAPI.ps1 aktualizacji**. Nowa wersja interfejsu API dzierżawcy WAP zawiera zmiany, aby włączyć relację zaufania nie tylko z STS bieżący, ale z wystąpieniem usług AD FS w stosie Azure.

     ```powershell
    cd C:\temp\wapconnector\setup\packages\

    .\Update-TenantAPI.ps1
    ```

    f.  Powtórz krok 2 na żadną inną maszynę wirtualną, uruchomiony interfejs API dzierżawcy.
3. Z **tylko jeden** maszyn wirtualnych interfejsu API dzierżawcy, uruchom skrypt TrustAzureStack.ps1 Konfiguruj, aby dodać relację zaufania między interfejs API dzierżawcy i wystąpienia usług AD FS na stosie Azure. Należy użyć konta z dostępu administratora systemu w bazie danych Microsoft.MgmtSvc.Store. Ten skrypt ma następujące parametry:

    | Parametr | Opis | Przykład |
    | --------- | ------------| ------- |
   | SqlServer | Nazwa programu SQL Server, który zawiera bazę danych Microsoft.MgmtSvc.Store. Ten parametr jest wymagany. | SQLServer | 
   | Pliku danych | Pliku wyjściowego, który został wygenerowany podczas konfigurowania tryb wielu chmury Azure stosu wcześniej. Ten parametr jest wymagany. | AzurePack-06-27-15-50.txt | 
   | PromptForSqlCredential | Wskazuje, czy skrypt powinien wyświetlenie monitu interakcyjnego dla poświadczenia uwierzytelniania SQL do użycia podczas połączenia z wystąpieniem programu SQL Server. Podane poświadczenie musi mieć wystarczające uprawnienia do odinstalowania baz danych, schematów i Usuń identyfikatory logowania użytkownika. Jeśli żaden nie jest podany, skrypt przyjęto założenie, że ten bieżącego kontekstu użytkownika ma dostęp. | Wartość nie jest potrzebna. |
   |  |  |

    Jeśli nie znasz programu SQL Server do użycia, może go odnaleźć. Połącz się z komputerem interfejs API dzierżawcy, użyj polecenia Unprotect-MgmtSvc Wyłącz ochronę pliku Web.config interfejsu API dzierżawcy i wyszukaj nazwę serwera w parametrach połączenia. Pamiętaj, aby uruchomić Protect-MgmtSvc ponownie, aby chronić plik Web.config interfejsu API dzierżawcy.

  ```powershell
  cd C:\temp\wapconnector\setup\scripts\

 .\Configure-TrustAzureStack.ps1 -SqlServer "SQLServer" `
       -DataFile "C:\temp\wapconnector\AzurePackConnectorOutput.txt"
  ```

## <a name="example"></a>Przykład
W poniższym przykładzie pokazano pełne wdrożenia łącznika usługi Windows Azure Pack w konfiguracji stosu Azure jednym węzłem i dwóch instalacji systemu Windows Azure Pack Express. (Każdej instalacji ekspresowej znajduje się na jednym komputerze, na przykład nazwy *wapcomputer1* i*wapcomputer2*.)

```powershell
# Run the following script on the Azure Stack host
$cred = New-Object System.Management.Automation.PSCredential("cloudadmin@azurestack.local",`
     (ConvertTo-SecureString -String "p@ssw0rd" -AsPlainText -Force))
$session = New-PSSession -ComputerName 'azs-ercs01' -Credential $cred `
     -ConfigurationName PrivilegedEndpoint -Authentication Credssp
 
# Enable Multicloud
invoke-command -Session $session -ScriptBlock { Add-AzurePackConnector -AzurePackClouds `
     @{CloudName = "AzurePack_1"; CloudEndpoint = "https://wapcomputer1.contoso.com:40005"},`
     @{CloudName = "AzurePack_2"; CloudEndpoint = "https://wapcomputer2.contoso.com:40005"}`
     -AzureStackCloudName "AzureStack" }  

```
Pobierz plik .exe z [Microsoft Download Center](https://aka.ms/wapconnectorazurestackdlc), wyodrębnij go i skopiuj WAPConnector folder **c:\temp** folderu na komputerze z systemem Windows Azure Pack. Skopiuj pliki, które zostały wygenerowane jako dane wyjściowe w poprzednim skryptu (znajdujący się w \\\su1fileserver\SU1_Infrastructure_1\AzurePackConnectorOutput) do **c:\temp\WAPConnector** folderu. (Pliki będą wygląda podobnie do poniższego: AzurePack-06-27 — 15-50.txt.) Następnie uruchom następujący skrypt, jeden raz dla każdego wystąpienia programu Microsoft Azure Pack:

 ```powershell
# Install Connector components
cd C:\temp\WAPConnector\Setup\Scripts
.\Install-Connector.ps1

# Configure Certificates for the new Connector services
.\Configure-Certificates.ps1

# Configure the Connector services
.\Configure-WapConnector.ps1 -TenantPortalFQDN "wapcomputer1.contoso.com" `
     -TenantAPIFQDN "wapcomputer1.contoso.com" `
     -AzureStackPortalFQDN "portal.local.azurestack.external"

# Install the updated TenantAPI
.\Update-TenantAPI.ps1

# Establish trust with the Azure Stack AD FS
.\Configure-TrustAzureStack.ps1 -SqlServer "wapcomputer1" `
     -DataFile "C:\temp\wapconnector\AzurePack-06-27-15-50.txt" 

```
## <a name="troubleshooting-tips"></a>Wskazówki dotyczące rozwiązywania problemów
1.  Upewnij się, że istnieje połączenie sieciowe między stosu Azure i pakietu Windows Azure Pack. Połączeń powinna wynosić od dowolnego komputera dzierżawy, który uzyskuje dostęp do portalu Azure stosu do dzierżawy na portalu maszyny wirtualnej systemu Windows Azure Pack, w którym są uruchomione nowe usługi łącznika.
2.  Upewnij się, że wszystkie określone czy nazw FQDN, które są prawidłowe.
3.  Upewnij się, że certyfikaty SSL używany w nowej usługi łączników są zaufane przez stos Azure (w szczególności AzS WASP01 maszyny Wirtualnej) i dowolnym innym komputerze dzierżawcy mogą uzyskiwać dostęp do portalu użytkownika stosu Azure.
4. Pod kątem znanych problemów, zobacz [Microsoft Azure stosu Rozwiązywanie problemów z](azure-stack-troubleshooting.md).


## <a name="next-steps"></a>Kolejne kroki
[Przy użyciu portali administratora i użytkownika w stosie Azure](azure-stack-manage-portals.md)
