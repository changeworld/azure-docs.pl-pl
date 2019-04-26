---
title: Szczegółowy Pulpit zdalny, rozwiązywania problemów na platformie Azure | Dokumentacja firmy Microsoft
description: Przejrzyj szczegółowe kroki rozwiązywania problemów dla zdalnego pulpitu błędów, których nie można do maszyn wirtualnych Windows na platformie Azure
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: jeconnoc
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
keywords: Nie można nawiązać połączenia z pulpitem zdalnym, rozwiązywanie problemów z pulpitu zdalnego, Pulpit zdalny nie może połączyć błędy usług pulpitu zdalnego, rozwiązywania problemów usług pulpitu zdalnego, problemy z pulpitu zdalnego
ms.assetid: 9da36f3d-30dd-44af-824b-8ce5ef07e5e0
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 4b4d2e2099f0d49c7dd9a150ac659ffde62eaa21
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60506409"
---
# <a name="detailed-troubleshooting-steps-for-remote-desktop-connection-issues-to-windows-vms-in-azure"></a>Szczegółowe kroki rozwiązywania problemów połączeń usług pulpitu zdalnego do maszyn wirtualnych Windows na platformie Azure
Ten artykuł zawiera szczegółowe kroki rozwiązywania problemów, aby zdiagnozować i rozwiązać złożone błędy usług pulpitu zdalnego na podstawie Windows maszyn wirtualnych platformy Azure.

> [!IMPORTANT]
> Aby wyeliminować bardziej powszechne błędy usług pulpitu zdalnego, upewnij się, że odczytu [podstawowy artykuł dotyczący rozwiązywania problemów za pomocą pulpitu zdalnego](troubleshoot-rdp-connection.md) przed kontynuowaniem.

Możesz napotkać pulpitu zdalnego, komunikat o błędzie, który wygląda inaczej, dowolny określone komunikaty o błędach omówione w [podstawowy przewodnik rozwiązywania problemów usług pulpitu zdalnego](troubleshoot-rdp-connection.md). Wykonaj następujące kroki, aby ustalić, dlaczego klienta Remote Desktop (RDP) nie jest w stanie połączyć się z usługą protokołu RDP na maszynie Wirtualnej platformy Azure.

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Jeśli potrzebujesz dodatkowej pomocy w dowolnym momencie, w tym artykule, możesz skontaktować się ze ekspertów platformy Azure na [MSDN Azure i Stack Overflow forów](https://azure.microsoft.com/support/forums/). Alternatywnie można również pliku zdarzenia pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/) i kliknij przycisk **Uzyskaj pomoc techniczną**. Aby uzyskać informacje o korzystaniu z pomocy technicznej systemu Azure, przeczytaj [Microsoft Azure pomocy technicznej — często zadawane pytania](https://azure.microsoft.com/support/faq/).

## <a name="components-of-a-remote-desktop-connection"></a>Składniki połączenia pulpitu zdalnego
Połączenia RDP obejmuje następujące składniki:

![](./media/detailed-troubleshoot-rdp/tshootrdp_0.png)

Zanim przejdziesz dalej, mogą pomóc umysłowo Przejrzyj, co zmieniło się od czasu ostatniego pomyślnego połączenia pulpitu zdalnego z maszyną wirtualną. Na przykład:

* Publiczny adres IP maszyny Wirtualnej lub usługi w chmurze zawierającej maszynę Wirtualną (nazywane również wirtualnego adresu IP [VIP](https://en.wikipedia.org/wiki/Virtual_IP_address)) została zmieniona. Błąd protokołu RDP może to być spowodowane pamięć podręczną klienta DNS nadal ma *stary adres IP* zarejestrowany dla nazwy DNS. Opróżnienia pamięci podręcznej klienta DNS, a następnie spróbuj ponownie nawiązać połączenie maszyny Wirtualnej. Lub spróbuj nawiązać połączenie bezpośrednio z nowego adresu VIP.
* Używasz aplikacji innej firmy do zarządzania połączeniami pulpitu zdalnego zamiast przy użyciu połączenia wygenerowanych przez portal systemu Azure. Upewnij się, że konfiguracja aplikacji zawiera prawidłowy port TCP dla ruchu pulpitu zdalnego. Można sprawdzić ten port klasycznej maszyny wirtualnej w [witryny Azure portal](https://portal.azure.com), klikając przycisk Ustawienia maszyny Wirtualnej > punkty końcowe.

## <a name="preliminary-steps"></a>Wstępne kroki
Przed przejściem do szczegółowe procedury rozwiązywania problemów,

* Sprawdź stan maszyny wirtualnej w witrynie Azure portal żadnych oczywistych problemów.
* Postępuj zgodnie z [szybkiej poprawki kroków dla typowych błędów protokołu RDP w podstawowych przewodnik rozwiązywania problemów](troubleshoot-rdp-connection.md#quick-troubleshooting-steps).
* Dla obrazów niestandardowych upewnij się, wirtualnego dysku twardego jest prawidłowo przygotowane przed jego przekazaniem. Aby uzyskać więcej informacji, zobacz [przygotowanie Windows dysku VHD lub VHDX można przekazać na platformę Azure](../windows/prepare-for-upload-vhd-image.md).


Spróbuj ponowne nawiązywanie połączenia z maszyną wirtualną za pośrednictwem pulpitu zdalnego, po wykonaniu tych kroków.

## <a name="detailed-troubleshooting-steps"></a>Szczegółowe kroki rozwiązywania problemów
Klient pulpitu zdalnego nie może być dostęp do usług pulpitu zdalnego na maszynie Wirtualnej platformy Azure, ze względu na problemy z następujących źródeł:

* [Klient usług pulpitu zdalnego komputera](#source-1-remote-desktop-client-computer)
* [Urządzenie brzegowe w intranecie organizacji](#source-2-organization-intranet-edge-device)
* [Punkt końcowy usługi w chmurze i dostęp do listy kontroli (ACL)](#source-3-cloud-service-endpoint-and-acl)
* [Sieciowe grupy zabezpieczeń](#source-4-network-security-groups)
* [Oparte na Windows maszyny Wirtualnej platformy Azure](#source-5-windows-based-azure-vm)

## <a name="source-1-remote-desktop-client-computer"></a>Źródło 1: Klient usług pulpitu zdalnego komputera
Upewnij się, że komputer może nawiązywać połączenia pulpitu zdalnego, do innego serwera lokalnego, komputerze z systemem Windows.

![](./media/detailed-troubleshoot-rdp/tshootrdp_1.png)

Jeśli z jakiegoś powodu, sprawdź, czy następujące ustawienia na komputerze:

* Ustawienie zapory lokalnej, która blokuje ruch pulpitu zdalnego.
* Lokalnie zainstalowane oprogramowanie serwera proxy klienta, co uniemożliwia połączeń pulpitu zdalnego.
* Lokalnie zainstalowane oprogramowanie, które uniemożliwiają wykonanie połączenia pulpitu zdalnego monitorowania sieci.
* Inne typy oprogramowania zabezpieczającego przeznaczonych do monitorowania ruchu lub umożliwić/nie zezwalaj na określone typy ruchu, zapobiegający wyrzuceniu połączeń pulpitu zdalnego.

W takich przypadkach tymczasowo wyłączone i spróbuj połączyć się na komputerze lokalnym za pośrednictwem pulpitu zdalnego. Jeśli znajdziesz rzeczywista Przyczyna w ten sposób, Współpracuj z administratorem sieci, aby poprawić ustawień oprogramowania, aby zezwolić na połączenia pulpitu zdalnego.

## <a name="source-2-organization-intranet-edge-device"></a>Źródło 2: Urządzenie brzegowe w intranecie organizacji
Upewnij się, że komputer bezpośrednio połączony z Internetem mogą przesłać połączeń pulpitu zdalnego z maszyną wirtualną platformy Azure.

![](./media/detailed-troubleshoot-rdp/tshootrdp_2.png)

Jeśli nie masz komputera, który jest bezpośrednio połączony z Internetem, tworzenie i testowanie za pomocą nowej maszyny wirtualnej platformy Azure w usłudze chmury lub grupy zasobów. Aby uzyskać więcej informacji, zobacz [tworzenie maszyny wirtualnej z systemem Windows na platformie Azure](../virtual-machines-windows-hero-tutorial.md). Po przeprowadzeniu testu, możesz usunąć maszynę wirtualną i grupę zasobów lub usługi w chmurze.

Jeśli tworzysz połączenie pulpitu zdalnego z komputerem podłączone bezpośrednio do Internetu, należy sprawdzić organizacji intranet edge na urządzeniu:

* Wewnętrzny Zapora blokuje połączenia HTTPS z Internetem.
* Serwer proxy, zapobiegając połączeń pulpitu zdalnego.
* Wykrywanie włamań lub oprogramowanie działające na urządzeniach w sieci krawędź, co uniemożliwia połączeń pulpitu zdalnego monitorowania sieci.

Praca z administratorem sieci, aby poprawić ustawienia urządzenie brzegowe w intranecie organizacji, aby zezwolić na połączenia pulpitu zdalnego oparty na protokole HTTPS z Internetem.

## <a name="source-3-cloud-service-endpoint-and-acl"></a>Źródło 3: Punkt końcowy usługi w chmurze i listy ACL
W przypadku maszyn wirtualnych utworzonych za pomocą klasycznego modelu wdrażania Sprawdź, czy innej maszyny Wirtualnej platformy Azure, która znajduje się w tej samej usługi w chmurze lub sieci wirtualnej ułatwia połączeń pulpitu zdalnego z maszyną wirtualną platformy Azure.

![](./media/detailed-troubleshoot-rdp/tshootrdp_3.png)

> [!NOTE]
> Maszyny wirtualne utworzone w usłudze Resource Manager, można pominąć [4 źródła: Sieciowe grupy zabezpieczeń](#source-4-network-security-groups).

Jeśli nie masz inną maszynę wirtualną w tej samej usługi w chmurze lub sieci wirtualnej, utworzyć. Postępuj zgodnie z instrukcjami w [tworzenie maszyny wirtualnej z systemem Windows na platformie Azure](../virtual-machines-windows-hero-tutorial.md). Po zakończeniu testu, Usuń testową maszynę wirtualną.

Jeśli można się połączyć za pośrednictwem pulpitu zdalnego na maszynę wirtualną w tej samej usługi w chmurze lub sieci wirtualnej, sprawdź, czy następujące ustawienia:

* Konfiguracja punktu końcowego ruchu pulpitu zdalnego na docelowej maszynie Wirtualnej: Port TCP prywatny punktu końcowego musi odpowiadać portu TCP, na którym nasłuchuje usługa pulpitu zdalnego maszyny Wirtualnej (wartość domyślna to 3389).
* Listy ACL dla punktu końcowego ruchu pulpitu zdalnego na docelowej maszynie Wirtualnej: Listy ACL pozwalają na określenie dozwolony lub niedozwolony ruch przychodzący z Internetu, w oparciu o jego źródłowy adres IP. Nieprawidłowo skonfigurowane listy ACL może uniemożliwić ruchu przychodzącego do usług pulpitu zdalnego do punktu końcowego. Sprawdź swoje listy ACL, aby upewnić się, że ruch przychodzący z publicznymi adresami IP serwera proxy lub inny serwer krawędzi jest dozwolony. Aby uzyskać więcej informacji, zobacz [co to jest sieć kontroli listy dostępu (ACL)?](../../virtual-network/virtual-networks-acl.md)

Aby sprawdzić, czy punkt końcowy jest źródłem problemu, Usuń bieżący punkt końcowy, a następnie utwórz nową, wybierając losowy port z zakresu od 49152 – 65535 numeru portu zewnętrznego. Aby uzyskać więcej informacji, zobacz [jak konfigurować punkty końcowe do maszyny wirtualnej](../windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="source-4-network-security-groups"></a>Źródło 4: Grupy zabezpieczeń sieci
Sieciowe grupy zabezpieczeń umożliwiają większą kontrolę nad dozwolonego ruchu przychodzącego i wychodzącego. Można utworzyć zasady obejmujące podsieci i usług w chmurze w sieci wirtualnej platformy Azure.

Użyj funkcji [weryfikacji przepływu adresu IP](../../network-watcher/network-watcher-check-ip-flow-verify-portal.md) w celu potwierdzenia, że reguła w sieciowej grupie zabezpieczeń blokuje ruch do lub z maszyny wirtualnej. Możesz również przejrzeć obowiązujące reguły grupy zabezpieczeń do zapewnienia ruchu przychodzącego "Zezwalaj" sieciowa grupa zabezpieczeń istnieje priorytetowa reguła dla portu RDP (domyślnie: 3389). Aby uzyskać więcej informacji, zobacz [przepływu ruchu przy użyciu efektywne reguły zabezpieczeń, aby rozwiązać maszyn wirtualnych](../../virtual-network/diagnose-network-traffic-filter-problem.md).

## <a name="source-5-windows-based-azure-vm"></a>Źródło 5: Oparte na Windows maszyny Wirtualnej platformy Azure
![](./media/detailed-troubleshoot-rdp/tshootrdp_5.png)

Postępuj zgodnie z instrukcjami w [w tym artykule](../windows/reset-rdp.md). W tym artykule resetuje usługi pulpitu zdalnego na maszynie wirtualnej:

* Włącz regułę domyślną "Pulpit zdalny" Windows zapory (TCP port 3389).
* Włączenie połączeń pulpitu zdalnego, ustawiając wartość rejestru HKLM\System\CurrentControlSet\Control\Terminal Server\fDenyTSConnections na 0.

Ponów próbę połączenia z komputera. Jeśli nadal nie można nawiązać połączenie za pośrednictwem usług pulpitu zdalnego, sprawdź następujące możliwe problemy:

* Usługa pulpitu zdalnego nie jest uruchomiona na docelowej maszynie Wirtualnej.
* Usługi Pulpit zdalny nie nasłuchuje na porcie TCP 3389.
* Zapora Windows lub inna zapora lokalnego ma regułę dla ruchu wychodzącego, co uniemożliwia ruchu pulpitu zdalnego.
* Wykrywanie włamań lub oprogramowania uruchamianego na maszynie wirtualnej platformy Azure do monitorowania sieci uniemożliwia połączeń pulpitu zdalnego.

W przypadku maszyn wirtualnych utworzonych za pomocą klasycznego modelu wdrażania można użyć sesji zdalnej programu Azure PowerShell do maszyny wirtualnej platformy Azure. Najpierw należy zainstalować certyfikat dla maszyny wirtualnej usługi hostingu w chmurze. Przejdź do [Konfigurowanie bezpiecznego środowiska PowerShell dostępu zdalnego do maszyn wirtualnych platformy Azure](https://gallery.technet.microsoft.com/scriptcenter/Configures-Secure-Remote-b137f2fe) i Pobierz **InstallWinRMCertAzureVM.ps1** plik skryptu na komputerze lokalnym.

Następnie zainstaluj program Azure PowerShell, jeśli jeszcze nie. Zobacz artykuł [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview).

Następnie otwórz wiersz polecenia programu Azure PowerShell i zmień bieżący folder do lokalizacji **InstallWinRMCertAzureVM.ps1** pliku skryptu. Aby uruchomić skrypt programu Azure PowerShell, należy ustawić zasady wykonywania poprawne. Uruchom **Get-ExecutionPolicy** polecenie, aby określić bieżący poziom zasad. Aby dowiedzieć się, jak ustawienia odpowiedniego poziomu, zobacz [Set-ExecutionPolicy](https://technet.microsoft.com/library/hh849812.aspx).

Następnie wprowadź nazwę subskrypcji platformy Azure, nazwa usługi w chmurze i nazwę maszyny wirtualnej (usuwanie < i > znaków), a następnie uruchom następujące polecenia.

```powershell
$subscr="<Name of your Azure subscription>"
$serviceName="<Name of the cloud service that contains the target virtual machine>"
$vmName="<Name of the target virtual machine>"
.\InstallWinRMCertAzureVM.ps1 -SubscriptionName $subscr -ServiceName $serviceName -Name $vmName
```

Można uzyskać nazwę poprawną subskrypcję z *SubscriptionName* właściwości wyświetlania **Get-AzureSubscription** polecenia. Nazwa usługi w chmurze dla maszyny wirtualnej można uzyskać *ServiceName* kolumny na wyświetlaczu **Get-AzureVM** polecenia.

Sprawdź, czy nowy certyfikat. Otwórz przystawkę Certyfikaty dla bieżącego użytkownika i Znajdź **zaufane główne urzędy certyfikacji\Certyfikaty** folderu. Powinien zostać wyświetlony certyfikat z nazwą DNS, w kolumnie wystawiony dla usługi w chmurze (przykład: cloudservice4testing.cloudapp.net).

Następnie można zainicjować sesji zdalnej programu Azure PowerShell za pomocą poniższych poleceń.

```powershell
$uri = Get-AzureWinRMUri -ServiceName $serviceName -Name $vmName
$creds = Get-Credential
Enter-PSSession -ConnectionUri $uri -Credential $creds
```

Po wprowadzeniu poświadczeń administratora prawidłowe, powinny zostać wyświetlone informacje podobne do następujących wiersza polecenia programu Azure PowerShell:

```powershell
[cloudservice4testing.cloudapp.net]: PS C:\Users\User1\Documents>
```

Pierwsza część tego monitu jest nazwę usługi w chmurze, która zawiera docelową maszynę Wirtualną, która może różnić się od "cloudservice4testing.cloudapp.net". Teraz można wydać programu Azure PowerShell polecenia dla tej usługi w chmurze do badania problemów wymienione i popraw konfigurację.

### <a name="to-manually-correct-the-remote-desktop-services-listening-tcp-port"></a>Aby ręcznie rozwiązać usług pulpitu zdalnego nasłuchiwania na porcie TCP
W wierszu zdalnej sesji programu Azure PowerShell Uruchom to polecenie.

```powershell
Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"
```

Właściwość numer_portu pokazuje bieżący numer portu. W razie potrzeby zmień Pulpit zdalny port numer wstecz do wartości domyślnej (3389) za pomocą tego polecenia.

```powershell
Set-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber" -Value 3389
```

Upewnij się, że zmieniono numer portu 3389 za pomocą tego polecenia.

```powershell
Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"
```

Zamknij sesję zdalną programu Azure PowerShell za pomocą tego polecenia.

```powershell
Exit-PSSession
```

Sprawdź, czy punkt końcowy pulpitu zdalnego dla maszyny Wirtualnej platformy Azure jest również za pomocą portu TCP 3398 jako wewnętrznego portu. Ponowne uruchomienie maszyny Wirtualnej platformy Azure, a następnie spróbuj ponownie za pomocą połączenia pulpitu zdalnego.

## <a name="additional-resources"></a>Dodatkowe zasoby
[Jak zresetować hasło lub usługę Pulpit zdalny dla maszyn wirtualnych Windows](../windows/reset-rdp.md)

[Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview)

[Rozwiązywanie problemów z połączeniami protokołu Secure Shell (SSH) opartą na systemie Linux maszyny wirtualnej platformy Azure](../linux/troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Rozwiązywanie problemów z dostępem do aplikacji uruchomionej na maszynie wirtualnej platformy Azure](../linux/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

