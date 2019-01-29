---
title: 'Samouczek:  Integracja z pojedynczym lasem usługi AD na platformie Azure przy użyciu synchronizacji skrótów haseł (wersji) | Dokumentacja firmy Microsoft'
description: Pokazuje, jak skonfigurować środowisko hybrydowe tożsamości za pomocą synchronizacji skrótów haseł.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2018
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: 11ba10921f806776a0bdf17c370feb45bd962bdb
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55168893"
---
# <a name="tutorial--integrate-a-single-ad-forest-using-password-hash-sync-phs"></a>Samouczek:  Integrowanie pojedynczego lasu usługi AD za pomocą synchronizacji skrótów haseł

![Przycisk Utwórz](media/tutorial-password-hash-sync/diagram.png)

Następującego samouczka opisano proces tworzenia środowiska tożsamości hybrydowej, przy użyciu synchronizacji skrótów haseł.  Następnie można użyć tego środowiska, do testowania lub w celu uzyskania bardziej zapoznać się z działaniem tożsamości hybrydowej.

## <a name="prerequisites"></a>Wymagania wstępne
Poniżej przedstawiono wymagania wstępne dotyczące wykonanie kroków tego samouczka
- Komputer z [funkcji Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/hyper-v-technology-overview) zainstalowane.  Zalecane jest aby to zrobić w dowolnym [systemu Windows 10](https://docs.microsoft.com/virtualization/hyper-v-on-windows/about/supported-guest-os) lub [systemu Windows Server 2016](https://docs.microsoft.com/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows) komputera.
- [Zewnętrzna karta sieciowa](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/connect-to-network) zezwalająca na maszynę wirtualną do komunikacji z Internetem.
- [Subskrypcja platformy Azure](https://azure.microsoft.com/free)
- Kopia systemu Windows Server 2016

> [!NOTE]
> Ten samouczek używa skryptów programu PowerShell, aby samouczka środowiska można tworzyć w najszybszym ilość czasu.  Każda z skryptów używa zmiennych, które są zadeklarowane na początku skryptów.  Można i należy zmienić zmienne z własnego środowiska.
>
>Skrypty używane utworzyć ogólne środowisko usługi Active Directory przed zainstalowaniem programu Azure AD Connect.  Są one odpowiednie dla wszystkich samouczków.
>
> Kopiuje skryptów programu PowerShell, które są używane w tym samouczku są dostępne w serwisie GitHub [tutaj](https://github.com/billmath/tutorial-phs).

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej
Pierwszą rzeczą, potrzebujemy, aby zrobić, aby można było rozpocząć naszego środowiska tożsamości hybrydowej i działa, utwórz maszynę wirtualną, która będzie służyć jako serwer usługi Active Directory naszego środowiska lokalnego.  Wykonaj następujące czynności:

1. Otwórz program PowerShell ISE jako Administrator.
2. Uruchom poniższy skrypt.

```powershell
#Declare variables
$VMName = 'DC1'
$Switch = 'External'
$InstallMedia = 'D:\ISO\en_windows_server_2016_updated_feb_2018_x64_dvd_11636692.iso'
$Path = 'D:\VM'
$VHDPath = 'D:\VM\DC1\DC1.vhdx'
$VHDSize = '64424509440'

#Create New Virtual Machine
New-VM -Name $VMName -MemoryStartupBytes 16GB -BootDevice VHD -Path $Path -NewVHDPath $VHDPath -NewVHDSizeBytes $VHDSize  -Generation 2 -Switch $Switch  

#Set the memory to be non-dynamic
Set-VMMemory $VMName -DynamicMemoryEnabled $false

#Add DVD Drive to Virtual Machine
Add-VMDvdDrive -VMName $VMName -ControllerNumber 0 -ControllerLocation 1 -Path $InstallMedia

#Mount Installation Media
$DVDDrive = Get-VMDvdDrive -VMName $VMName

#Configure Virtual Machine to Boot from DVD
Set-VMFirmware -VMName $VMName -FirstBootDevice $DVDDrive 
```

## <a name="complete-the-operating-system-deployment"></a>Wykonaj wdrożenie systemu operacyjnego
Aby zakończyć tworzenie maszyny wirtualnej, należy na zakończenie instalacji systemu operacyjnego.

1. Menedżer funkcji Hyper-V, kliknij dwukrotnie plik na maszynie wirtualnej
2. Kliknij przycisk Start.
3.  Zostanie wyświetlony monit "Naciśnij dowolny klawisz, aby rozruch następował z dysku CD lub DVD". Przejdź dalej i to zrobić.
4. W menu start systemu Windows Server ekran w górę wybierz swój język i kliknij przycisk **dalej**.
5. Kliknij przycisk **Zainstaluj teraz**.
6. Wprowadź klucz licencji i kliknij przycisk **dalej**.
7. Sprawdź ** I zaakceptuj postanowienia licencyjne i kliknij przycisk **dalej**.
8. Wybierz **niestandardowe:  Zainstaluj tylko Windows (zaawansowane)**
9. Kliknij przycisk **Dalej**
10. Po zakończeniu instalacji uruchom ponownie maszynę wirtualną, rejestrowania i wykonywania aktualizacji Windows i upewnij się, że maszyna wirtualna jest najbardziej aktualne.  Zainstaluj najnowsze aktualizacje.

## <a name="install-active-directory-prerequisites"></a>Instalowanie wstępnie wymaganych składników usługi Active Directory
Skoro mamy już maszynę wirtualną w, należy wykonać kilka czynności przed zainstalowaniem usługi Active Directory.  Oznacza to należy zmienić nazwę maszyny wirtualnej, ustaw statyczny adres IP i informacje dotyczące systemu DNS i zainstalować narzędzia administracji zdalnej serwera.   Wykonaj następujące czynności:

1. Otwórz program PowerShell ISE jako Administrator.
2. Uruchom poniższy skrypt.

```powershell
#Declare variables
$ipaddress = "10.0.1.117" 
$ipprefix = "24" 
$ipgw = "10.0.1.1" 
$ipdns = "10.0.1.117"
$ipdns2 = "8.8.8.8" 
$ipif = (Get-NetAdapter).ifIndex 
$featureLogPath = "c:\poshlog\featurelog.txt" 
$newname = "DC1"
$addsTools = "RSAT-AD-Tools" 

#Set static IP address
New-NetIPAddress -IPAddress $ipaddress -PrefixLength $ipprefix -InterfaceIndex $ipif -DefaultGateway $ipgw 

# Set the DNS servers
Set-DnsClientServerAddress -InterfaceIndex $ipif -ServerAddresses ($ipdns, $ipdns2)

#Rename the computer 
Rename-Computer -NewName $newname -force 

#Install features 
New-Item $featureLogPath -ItemType file -Force 
Add-WindowsFeature $addsTools 
Get-WindowsFeature | Where installed >>$featureLogPath 

#Restart the computer 
Restart-Computer
```

## <a name="create-a-windows-server-ad-environment"></a>Utwórz środowisko systemu Windows Server AD
Skoro mamy utworzona maszyna wirtualna i jej nazwa została zmieniona, a ma statyczny adres IP, możemy teraz i zainstaluj i skonfiguruj usługi Active Directory.  Wykonaj następujące czynności:

1. Otwórz program PowerShell ISE jako Administrator.
2. Uruchom poniższy skrypt.

```powershell 
#Declare variables
$DatabasePath = "c:\windows\NTDS"
$DomainMode = "WinThreshold"
$DomainName = "contoso.com"
$DomaninNetBIOSName = "CONTOSO"
$ForestMode = "WinThreshold"
$LogPath = "c:\windows\NTDS"
$SysVolPath = "c:\windows\SYSVOL"
$featureLogPath = "c:\poshlog\featurelog.txt" 
$Password = "Pass1w0rd"

#Install AD DS, DNS and GPMC 
start-job -Name addFeature -ScriptBlock { 
Add-WindowsFeature -Name "ad-domain-services" -IncludeAllSubFeature -IncludeManagementTools 
Add-WindowsFeature -Name "dns" -IncludeAllSubFeature -IncludeManagementTools 
Add-WindowsFeature -Name "gpmc" -IncludeAllSubFeature -IncludeManagementTools } 
Wait-Job -Name addFeature 
Get-WindowsFeature | Where installed >>$featureLogPath

#Create New AD Forest
Install-ADDSForest -CreateDnsDelegation:$false -DatabasePath $DatabasePath -DomainMode $DomainMode -DomainName $DomainName -SafeModeAdministratorPassword $Password -DomainNetbiosName $DomainNetBIOSName -ForestMode $ForestMode -InstallDns:$true -LogPath $LogPath -NoRebootOnCompletion:$false -SysvolPath $SysVolPath -Force:$true
```

## <a name="create-a-windows-server-ad-user"></a>Tworzenie użytkownika systemu Windows Server AD
Teraz, gdy mamy już naszego środowiska usługi Active Directory, musimy konta testowego.  To konto zostanie utworzony w naszym w środowisku lokalnym środowisku usługi AD i następnie zsynchronizowanych z usługą Azure AD.  Wykonaj następujące czynności:

1. Otwórz program PowerShell ISE jako Administrator.
2. Uruchom poniższy skrypt.

```powershell 
#Declare variables
$Givenname = "Allie"
$Surname = "McCray"
$Displayname = "Allie McCray"
$Name = "amccray"
$Password = "Pass1w0rd"
$Identity = "CN=ammccray,CN=Users,DC=contoso,DC=com"
$SecureString = ConvertTo-SecureString $Password -AsPlainText -Force


#Create the user
New-ADUser -Name $Name -GivenName $Givenname -Surname $Surname -DisplayName $Displayname -AccountPassword $SecureString

#Set the password to never expire
Set-ADUser -Identity $Identity -PasswordNeverExpires $true -ChangePasswordAtLogon $false -Enabled $true
```

## <a name="create-an-azure-ad-tenant"></a>Tworzenie dzierżawy usługi Azure AD
Teraz musimy utworzyć dzierżawę usługi Azure AD, dzięki czemu firma Microsoft można synchronizować użytkowników w chmurze.  Aby utworzyć nowe usługi Azure AD dzierżawy, wykonaj następujące czynności.

1. Przejdź do [witryny Azure portal](https://portal.azure.com) i zaloguj się przy użyciu konta które ma subskrypcję platformy Azure.
2. Wybierz **ikonę plusa (+)** i wyszukaj **usługi Azure Active Directory**.
3. Wybierz **usługi Azure Active Directory** w wynikach wyszukiwania.
4. Wybierz pozycję **Utwórz**.</br>
![Tworzenie](media/tutorial-password-hash-sync/create1.png)</br>
5. Podaj **nazwę organizacji** wraz z **początkowa nazwa domeny**. Następnie wybierz przycisk **Utwórz**. Spowoduje to utworzenie katalogu.
6. Po zakończeniu, kliknij przycisk **tutaj** łącze, aby zarządzać katalogiem.

## <a name="create-a-global-administrator-in-azure-ad"></a>Utwórz administratora globalnego w usłudze Azure AD
Teraz, gdy mamy już dzierżawę usługi Azure AD, utworzymy konto administratora globalnego.  To konto jest używane do utworzenia konta łącznika usługi Azure AD podczas instalacji programu Azure AD Connect.  Konta łącznika usługi Azure AD umożliwia zapisywanie informacji o usłudze Azure AD.   Aby utworzyć administratora globalnego konto, wykonaj następujące czynności.

1.  W obszarze **Zarządzaj** wybierz pozycję **Użytkownicy**.</br>
![Tworzenie](media/tutorial-password-hash-sync/gadmin1.png)</br>
2.  Wybierz **wszyscy użytkownicy** , a następnie wybierz **+ nowy użytkownik**.
3.  Podaj nazwę i nazwę użytkownika dla tego użytkownika. To będzie administratorem globalnym dla dzierżawy. Można również zmienić **roli w katalogu** do **administratora globalnego.** Można również wyświetlić hasło tymczasowe. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz**.</br>
![Tworzenie](media/tutorial-password-hash-sync/gadmin2.png)</br>
4. Po zakończeniu, Otwórz w nowym oknie przeglądarki sieci web i zaloguj się do myapps.microsoft.com przy użyciu nowego konta administratora globalnego i hasło tymczasowe.
5. Zmień hasło dla administratora globalnego do zasobu, który będzie pamiętać.

## <a name="download-and-install-azure-ad-connect"></a>Pobierz i zainstaluj program Azure AD Connect
Teraz nadszedł czas, aby pobrać i zainstalować program Azure AD Connect.  Po zainstalowaniu go uruchomimy za pomocą instalacji ekspresowej.  Wykonaj następujące czynności:

1. Pobierz [programu Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)
2. Przejdź do pozycji **AzureADConnect.msi** i kliknij ją dwukrotnie.
3. Na ekranie powitalnym zaznacz pole wyrażenia zgody na warunki licencji i kliknij pozycję **Kontynuuj**.  
4. Na ekranie Ustawienia ekspresowe kliknij polecenie **Użyj ustawień ekspresowych**.</br>  
![Tworzenie](media/tutorial-password-hash-sync/express1.png)</br>
5. Na ekranie łączenie z usługą Azure AD wprowadź nazwę użytkownika i hasło administratora globalnego dla usługi Azure AD. Kliknij przycisk **Dalej**.  
6. Na ekranie Łączenie z usługami AD DS wprowadź nazwę użytkownika i hasło konta administratora przedsiębiorstwa. Kliknij przycisk **Dalej**.  
7. Na ekranie Wszystko gotowe do skonfigurowania kliknij pozycję **Zainstaluj**.
8. Po zakończeniu instalacji kliknij przycisk **Zakończ**.
9. Po zakończeniu instalacji Wyloguj się i zaloguj się ponownie przed użyciem narzędzia Synchronization Service Manager lub Synchronization Rule Editor.


## <a name="verify-users-are-created-and-synchronization-is-occurring"></a>Sprawdź, użytkownicy są utworzeni i synchronizacja jest wykonywana
Teraz zostanie zweryfikowane, że użytkownicy, którzy mieliśmy w naszym katalogiem lokalnym zostały zsynchronizowane i istnieje teraz w poziomie dzierżawy usługi Azure AD.  Należy pamiętać, że może to potrwać kilka godzin.  Aby sprawdzić, użytkownicy są synchronizowani wykonaj następujące czynności.


1. Przejdź do [witryny Azure portal](https://portal.azure.com) i zaloguj się przy użyciu konta które ma subskrypcję platformy Azure.
2. Po lewej stronie, wybierz **usługi Azure Active Directory**
3. W obszarze **Zarządzaj** wybierz pozycję **Użytkownicy**.
4. Sprawdź, czy widzisz nowych użytkowników w dzierżawie nasze</br>
![Synchronizacja](media/tutorial-password-hash-sync/synch1.png)</br>

## <a name="test-signing-in-with-one-of-our-users"></a>Testowanie logujesz się przy użyciu jednego z naszych użytkowników

1.  Przejdź do [https://myapps.microsoft.com](httpss://myapps.microsoft.com)
2. Zaloguj się przy użyciu konta użytkownika, który został utworzony w naszym nowym dzierżawcą.  Konieczne będzie zalogowanie się w następującym formacie: (user@domain.onmicrosoft.com). Użyj tego samego hasła, których użytkownik używa do logowania lokalnego.</br>
![Sprawdź](media/tutorial-password-hash-sync/verify1.png)</br>

Masz teraz pomyślnie instalacji w środowisku hybrydowym tożsamości używanej do testowania i zapoznać się z platformy Azure ma do zaoferowania.

## <a name="next-steps"></a>Następne kroki


- [Sprzęt i wymagania wstępne](how-to-connect-install-prerequisites.md) 
- [Ustawienia ekspresowe](how-to-connect-install-express.md)
- [Synchronizacja skrótów haseł](how-to-connect-password-hash-synchronization.md)|
