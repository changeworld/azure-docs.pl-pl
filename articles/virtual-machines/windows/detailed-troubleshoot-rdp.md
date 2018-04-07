---
title: Szczegółowy Pulpit zdalny, rozwiązywanie problemów z platformy Azure | Dokumentacja firmy Microsoft
description: Przeglądanie szczegółowe kroki rozwiązywania problemów dla zdalnego pulpitu błędy, których nie do maszyn wirtualnych systemu Windows na platformie Azure
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: jeconnoc
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
keywords: Nie można połączyć z pulpitem zdalnym, rozwiązywanie problemów z pulpitu zdalnego, Pulpit zdalny nie może połączyć błędy usług pulpitu zdalnego, rozwiązywania problemów pulpitu zdalnego, problemy z pulpitu zdalnego
ms.assetid: 9da36f3d-30dd-44af-824b-8ce5ef07e5e0
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/06/2017
ms.author: genli
ms.openlocfilehash: 1485bc5ac7ae47df9a1a36c8b88d6515b5624360
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="detailed-troubleshooting-steps-for-remote-desktop-connection-issues-to-windows-vms-in-azure"></a>Szczegółowe kroki rozwiązywania problemów dotyczących problemów połączeń usług pulpitu zdalnego do maszyn wirtualnych systemu Windows na platformie Azure
Ten artykuł zawiera szczegółowe kroki rozwiązywania problemów w celu zdiagnozowania i rozwiązania złożonych błędy pulpitu zdalnego dla komputerów z systemem Windows Azure maszyny wirtualne.

> [!IMPORTANT]
> Aby wyeliminować błędy częściej pulpitu zdalnego, upewnij się, że odczytu [podstawowe artykuł dotyczący rozwiązywania problemów dla pulpitu zdalnego](troubleshoot-rdp-connection.md) przed kontynuowaniem.

Możesz napotkać komunikat o błędzie, który wygląda inaczej, wszelkie komunikaty o błędach specyficznych objęte pulpitu zdalnego [podstawowy przewodnik rozwiązywania problemów z pulpitu zdalnego](troubleshoot-rdp-connection.md). Wykonaj następujące kroki, aby określić, dlaczego nie można nawiązać połączenia z usługą RDP na maszynie Wirtualnej platformy Azure jest klienta usług pulpitu zdalnego (RDP).

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Jeśli potrzebujesz więcej pomocy w dowolnym momencie, w tym artykule, możesz skontaktować się ekspertów platformy Azure na [MSDN Azure i fora przepełnienie stosu](https://azure.microsoft.com/support/forums/). Alternatywnie można również pliku zdarzenia pomocy technicznej platformy Azure. Przejdź do [witrynę pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i kliknij przycisk **Get Support**. Aby uzyskać informacje o korzystaniu z pomocą techniczną platformy Azure, przeczytaj [często zadawane pytania dotyczące programu Microsoft Azure Obsługa](https://azure.microsoft.com/support/faq/).

## <a name="components-of-a-remote-desktop-connection"></a>Składniki połączenia pulpitu zdalnego
Połączenie RDP obejmuje następujące składniki:

![](./media/detailed-troubleshoot-rdp/tshootrdp_0.png)

Przed kontynuowaniem, warto umysłowo Przejrzyj zmiany od momentu ostatniego pomyślnego połączenia pulpitu zdalnego do maszyny Wirtualnej. Na przykład:

* Publiczny adres IP maszyny Wirtualnej lub usługi w chmurze zawierających maszyny Wirtualnej (nazywane również wirtualnego adresu IP [VIP](https://en.wikipedia.org/wiki/Virtual_IP_address)) została zmieniona. Niepowodzenie RDP mogło być, ponieważ pamięć podręczna klienta DNS nadal zawiera *stary adres IP* zarejestrowane nazwy DNS. Opróżnienia pamięci podręcznej klienta DNS, a następnie spróbuj ponownie nawiązać połączenie z maszyną Wirtualną. Lub spróbuj połączyć się bezpośrednio przy użyciu nowego adresu VIP.
* Używasz aplikacji innych firm do zarządzania połączeniami pulpitu zdalnego zamiast przy użyciu połączenia wygenerowane w portalu Azure. Upewnij się, że konfiguracja aplikacji zawiera poprawny port TCP dla ruchu pulpitu zdalnego. Możesz sprawdzić tego portu klasycznych maszyn wirtualnych w [portalu Azure](https://portal.azure.com), klikając ustawienia maszyny Wirtualnej > punktów końcowych.

## <a name="preliminary-steps"></a>Czynności wstępne
Przed kontynuowaniem szczegółowe Rozwiązywanie problemów

* Sprawdź stan maszyny wirtualnej w portalu Azure w poszukiwaniu problemów oczywiste.
* Postępuj zgodnie z [kroków szybkiej poprawki dla typowych błędów protokołu RDP w podręczniku rozwiązywania problemów podstawowe](troubleshoot-rdp-connection.md#quick-troubleshooting-steps).
* Dla niestandardowych obrazów upewnij się, że dysk VHD jest odpowiednio przygotować przed przekazaniem. Aby uzyskać więcej informacji, zobacz [Przygotowywanie wirtualnego dysku twardego Windows lub VHDX można przekazać do usługi Azure](prepare-for-upload-vhd-image.md).


Spróbuj ponownie połączyć się z maszyną Wirtualną za pośrednictwem pulpitu zdalnego po wykonaniu tych kroków.

## <a name="detailed-troubleshooting-steps"></a>Szczegółowe kroki rozwiązywania problemów
Klient usług pulpitu zdalnego nie może być dostęp do usług pulpitu zdalnego na maszynie Wirtualnej Azure z powodu problemów w następujących źródeł:

* [Komputer klienta usług pulpitu zdalnego](#source-1-remote-desktop-client-computer)
* [Urządzenie brzegowe intranetowych w organizacji](#source-2-organization-intranet-edge-device)
* [Punkt końcowy usługi w chmurze i uzyskiwać dostęp do listy kontroli (ACL)](#source-3-cloud-service-endpoint-and-acl)
* [Sieciowe grupy zabezpieczeń](#source-4-network-security-groups)
* [Oparte na systemie Windows Azure maszyny Wirtualnej](#source-5-windows-based-azure-vm)

## <a name="source-1-remote-desktop-client-computer"></a>Źródło 1: Komputer klienta usług pulpitu zdalnego
Upewnij się, że komputer może nawiązywać połączenia pulpitu zdalnego do innego lokalnego, komputer z systemem Windows.

![](./media/detailed-troubleshoot-rdp/tshootrdp_1.png)

Jeśli nie, sprawdź następujące ustawienia na komputerze:

* Ustawienia zapory lokalnej, która blokuje ruch pulpitu zdalnego.
* Lokalnie zainstalowane oprogramowanie serwera proxy klienta, który uniemożliwia połączeń pulpitu zdalnego.
* Lokalnie zainstalować oprogramowania, który uniemożliwia połączeń pulpitu zdalnego do monitorowania sieci.
* Inne rodzaje oprogramowania zabezpieczającego które monitorowania ruchu lub Zezwalaj/nie zezwalaj na określone typy ruchu, który uniemożliwia połączeń pulpitu zdalnego.

W takich przypadkach tymczasowo wyłączone i spróbuj połączyć się z komputerem lokalnym za pośrednictwem pulpitu zdalnego. Jeśli przyczynę rzeczywiste można znaleźć w ten sposób, współpracować z administratorem sieci, aby rozwiązać oprogramowanie ustawienia połączeń usług pulpitu zdalnego.

## <a name="source-2-organization-intranet-edge-device"></a>Źródło 2: Urządzenie brzegowe intranetu organizacji
Upewnij się, że komputer bezpośrednio połączony z Internetem mogą nawiązywać połączenia pulpitu zdalnego do maszyny wirtualnej platformy Azure.

![](./media/detailed-troubleshoot-rdp/tshootrdp_2.png)

Jeśli nie masz komputera, który jest bezpośrednio połączony z Internetem, tworzenie i testowanie z nowej maszyny wirtualnej platformy Azure w usłudze chmury lub grupy zasobów. Aby uzyskać więcej informacji, zobacz [Utwórz maszynę wirtualną z systemem Windows na platformie Azure](../virtual-machines-windows-hero-tutorial.md). Maszyna wirtualna i grupy zasobów lub usługi w chmurze, można usunąć po testu.

Jeśli tworzysz Podłączanie pulpitu zdalnego z komputerem bezpośrednio połączone z Internetem, sprawdź do urządzenia brzegowe intranetu organizacji:

* Wewnętrzny Zapora blokuje połączenia HTTPS z Internetem.
* Serwer proxy uniemożliwia połączeń pulpitu zdalnego.
* Włamań lub oprogramowanie na urządzeniach w sieci krawędzi, która uniemożliwia połączeń pulpitu zdalnego do monitorowania sieci.

Skontaktować się z administratorem sieci, aby poprawić ustawień urządzenia brzegowe intranet organizacji, aby zezwolić oparty na protokole HTTPS pulpitu zdalnego połączenia z Internetem.

## <a name="source-3-cloud-service-endpoint-and-acl"></a>Źródło 3: Punkt końcowy usługi w chmurze i listy ACL
Dla maszyn wirtualnych utworzonych przy użyciu klasycznego modelu wdrożenia Sprawdź, czy innej maszyny Wirtualnej platformy Azure, który znajduje się w tej samej usługi w chmurze lub sieci wirtualnej mogą wysyłać połączeń pulpitu zdalnego do maszyny Wirtualnej Azure.

![](./media/detailed-troubleshoot-rdp/tshootrdp_3.png)

> [!NOTE]
> Dla maszyn wirtualnych utworzonych w Menedżerze zasobów, przejdź do [źródła 4: grup zabezpieczeń sieci](#source-4-network-security-groups).

Jeśli nie masz innej maszyny wirtualnej w tej samej usługi w chmurze lub sieci wirtualnej, utwórz je. Postępuj zgodnie z instrukcjami [Utwórz maszynę wirtualną z systemem Windows na platformie Azure](../virtual-machines-windows-hero-tutorial.md). Po zakończeniu testu, należy usunąć testowej maszyny wirtualnej.

Jeśli można połączyć za pośrednictwem pulpitu zdalnego do maszyny wirtualnej w tej samej usługi w chmurze lub sieci wirtualnej, sprawdź następujące ustawienia:

* Konfiguracja punktu końcowego dla ruchu pulpitu zdalnego w systemie docelowym maszyny Wirtualnej: port TCP prywatny punktu końcowego musi być zgodny z portem TCP, na którym nasłuchuje usługa Pulpit zdalny maszyny Wirtualnej (wartość domyślna to 3389).
* Listy ACL punktu końcowego ruchu pulpitu zdalnego w systemie docelowym maszyny Wirtualnej: listy kontroli dostępu zezwala na określanie dozwolony lub niedozwolony ruch przychodzący z Internetu, na podstawie jego adresu IP źródłowego. Nieprawidłowo skonfigurowane listy kontroli dostępu może uniemożliwić ruchu przychodzącego usług pulpitu zdalnego do punktu końcowego. Sprawdź z listy kontroli dostępu, aby upewnić się, że ruch przychodzący z publiczne adresy IP serwera proxy lub inny serwer krawędzi jest dozwolone. Aby uzyskać więcej informacji, zobacz [co to jest sieć listy kontroli dostępu (ACL)?](../../virtual-network/virtual-networks-acl.md)

Aby sprawdzić, czy punkt końcowy jest źródłem problemu, Usuń bieżący punkt końcowy i utworzyć nową, wybierając losowego portu z zakresu od 49152 – 65535 numeru portu zewnętrznego. Aby uzyskać więcej informacji, zobacz [sposobu konfigurowania punktów końcowych do maszyny wirtualnej](classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="source-4-network-security-groups"></a>Źródła 4: Grupy zabezpieczeń sieci
Grupy zabezpieczeń sieci umożliwiają większą kontrolę nad dozwolonego ruchu przychodzącego i wychodzącego. Można utworzyć zasady obejmujące podsieci i usług w sieci wirtualnej platformy Azure w chmurze.

Użyj funkcji [weryfikacji przepływu adresu IP](../../network-watcher/network-watcher-check-ip-flow-verify-portal.md) w celu potwierdzenia, że reguła w sieciowej grupie zabezpieczeń blokuje ruch do lub z maszyny wirtualnej. Można również przejrzeć efektywnym elementem systemu zabezpieczeń zasady grupy w celu zapewnienia ruchu przychodzącego "Zezwalaj" NSG reguły istnieje i jest priorytety dla portu protokołu RDP (ustawienie domyślne 3389). Aby uzyskać więcej informacji, zobacz [przepływu ruchu przy użyciu skuteczne reguły zabezpieczeń rozwiązywać problemy z maszyny Wirtualnej](../../virtual-network/virtual-network-nsg-troubleshoot-portal.md#using-effective-security-rules-to-troubleshoot-vm-traffic-flow).

## <a name="source-5-windows-based-azure-vm"></a>Źródło 5: Opartych na systemie Windows Azure maszyny Wirtualnej
![](./media/detailed-troubleshoot-rdp/tshootrdp_5.png)

Postępuj zgodnie z instrukcjami [w tym artykule](reset-rdp.md). W tym artykule resetuje usługi pulpitu zdalnego na maszynie wirtualnej:

* Włącz regułę domyślną "Pulpit zdalny" Zapora systemu Windows (port 3389 protokołu TCP).
* Włączenie połączeń pulpitu zdalnego za pomocą ustawienia wartości rejestru HKLM\System\CurrentControlSet\Control\Terminal Server\fDenyTSConnections na 0.

Ponów próbę połączenia z komputera. Jeśli nadal nie można nawiązać za pośrednictwem pulpitu zdalnego, sprawdź następujące możliwe problemy:

* Usługi pulpitu zdalnego nie działa w systemie docelowym maszyny Wirtualnej.
* Usługa Pulpit zdalny nie nasłuchuje portu 3389 protokołu TCP.
* Zapora systemu Windows lub innej zapory lokalnej ma wychodzącą regułę, która uniemożliwia ruchu pulpitu zdalnego.
* Włamań lub monitorowania oprogramowania uruchomionego na maszynie wirtualnej Azure sieci uniemożliwia połączeń pulpitu zdalnego.

Dla maszyn wirtualnych utworzonych przy użyciu klasycznego modelu wdrażania można użyć sesję zdalną programu Azure PowerShell do maszyny wirtualnej platformy Azure. Najpierw należy zainstalować certyfikat dla maszyny wirtualnej usługi hostingu w chmurze. Przejdź do [skonfigurować bezpiecznego środowiska PowerShell dostępu zdalnego do maszyny wirtualnej platformy Azure](http://gallery.technet.microsoft.com/scriptcenter/Configures-Secure-Remote-b137f2fe) i Pobierz **InstallWinRMCertAzureVM.ps1** plik skryptu na komputerze lokalnym.

Następnie zainstaluj programu Azure PowerShell, jeśli nie jest jeszcze. Zobacz artykuł [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview).

Następnie otwórz wiersz polecenia programu PowerShell systemu Azure i zmień bieżący folder na lokalizację **InstallWinRMCertAzureVM.ps1** pliku skryptu. Aby uruchomić skrypt programu Azure PowerShell, należy ustawić zasady wykonywania poprawne. Uruchom **Get-ExecutionPolicy** polecenie, aby określić bieżący poziom zasad. Informacje o ustawianiu odpowiedni poziom, zobacz [Set-ExecutionPolicy](https://technet.microsoft.com/library/hh849812.aspx).

Następnie wypełnij nazwę subskrypcji platformy Azure, nazwa usługi w chmurze i nazwę maszyny wirtualnej (usuwanie < i > znaków), a następnie uruchom następujące polecenia.

```powershell
$subscr="<Name of your Azure subscription>"
$serviceName="<Name of the cloud service that contains the target virtual machine>"
$vmName="<Name of the target virtual machine>"
.\InstallWinRMCertAzureVM.ps1 -SubscriptionName $subscr -ServiceName $serviceName -Name $vmName
```

Można uzyskać nazwy poprawną subskrypcję z *Nazwa subskrypcji* właściwości wyświetlania **Get-AzureSubscription** polecenia. Nazwa usługi w chmurze maszyny wirtualnej z uzyskaniem *ServiceName* kolumny na wyświetlaczu **Get-AzureVM** polecenia.

Sprawdź, czy istnieje nowy certyfikat. Otwórz przystawkę Certyfikaty dla bieżącego użytkownika i Znajdź **zaufany główny Certification Authorities\Certificates** folderu. Powinny pojawić się certyfikatu z nazwą DNS w kolumnie wystawiony dla usługi w chmurze (przykład: cloudservice4testing.cloudapp.net).

Następnie zainicjować sesję zdalną programu Azure PowerShell przy użyciu tych poleceń.

```powershell
$uri = Get-AzureWinRMUri -ServiceName $serviceName -Name $vmName
$creds = Get-Credential
Enter-PSSession -ConnectionUri $uri -Credential $creds
```

Po wprowadzeniu poświadczeń administratora prawidłowe, powinny zostać wyświetlone informacje podobne do następującego wiersza polecenia programu Azure PowerShell:

```powershell
[cloudservice4testing.cloudapp.net]: PS C:\Users\User1\Documents>
```

Pierwsza część ten monit jest nazwę usługi chmury, która zawiera docelową maszynę Wirtualną, która może być inny niż "cloudservice4testing.cloudapp.net". Obecnie można przeprowadzić programu Azure PowerShell polecenia dla tej usługi w chmurze do badania problemów wymienionych i popraw konfigurację.

### <a name="to-manually-correct-the-remote-desktop-services-listening-tcp-port"></a>Aby ręcznie rozwiązać nasłuchiwanie na porcie TCP usług pulpitu zdalnego
W wierszu zdalnej sesji programu Azure PowerShell Uruchom to polecenie.

```powershell
Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"
```

Właściwość PortNumber pokazuje bieżący numer portu. W razie potrzeby zmień pulpitu zdalnego portu numer wstecz do wartości domyślnej (3389) za pomocą tego polecenia.

```powershell
Set-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber" -Value 3389
```

Sprawdź, czy port został zmieniony na 3389 za pomocą tego polecenia.

```powershell
Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"
```

Zakończenia sesji zdalnej programu Azure PowerShell za pomocą tego polecenia.

```powershell
Exit-PSSession
```

Sprawdź, czy punkt końcowy pulpitu zdalnego dla maszyny Wirtualnej Azure również używa portu TCP 3398 jako wewnętrznego portu. Ponowne uruchomienie maszyny Wirtualnej Azure, a następnie spróbuj ponownie za pomocą połączenia pulpitu zdalnego.

## <a name="additional-resources"></a>Zasoby dodatkowe
[Jak można zresetować hasła lub usługi pulpitu zdalnego dla maszyn wirtualnych systemu Windows](reset-rdp.md)

[Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview)

[Rozwiązywanie problemów z połączeniami Secure Shell (SSH) do maszyny wirtualnej opartych na systemie Linux platformy Azure](../linux/troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Rozwiązywanie problemów z dostępem do aplikacji uruchomionej na maszynie wirtualnej platformy Azure](../linux/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

