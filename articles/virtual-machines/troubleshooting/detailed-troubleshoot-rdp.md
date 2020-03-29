---
title: Szczegółowe rozwiązywanie problemów z pulpitem zdalnym na platformie Azure | Dokumenty firmy Microsoft
description: Zapoznaj się ze szczegółowymi krokami rozwiązywania problemów z błędami pulpitu zdalnego, w których nie można obsługiwać maszyn wirtualnych systemu Windows na platformie Azure
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
keywords: nie można połączyć się z pulpitem zdalnym, rozwiązywać problemy z pulpitem zdalnym, pulpitem zdalnym, nie można połączyć się z komputerami zdalnymi, rozwiązywaniem problemów z pulpitem zdalnym
ms.assetid: 9da36f3d-30dd-44af-824b-8ce5ef07e5e0
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: ea448b87f9e6954abecead2934bfb7f4ed04a9c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920148"
---
# <a name="detailed-troubleshooting-steps-for-remote-desktop-connection-issues-to-windows-vms-in-azure"></a>Szczegółowe procedury rozwiązywania problemów z połączeniem pulpitu zdalnego z maszynami wirtualnymi systemu Windows na platformie Azure
Ten artykuł zawiera szczegółowe kroki rozwiązywania problemów w celu diagnozowania i rozwiązywania złożonych błędów pulpitu zdalnego dla maszyn wirtualnych platformy Azure opartych na systemie Windows.

> [!IMPORTANT]
> Aby wyeliminować częstsze błędy pulpitu zdalnego, przed kontynuowaniem przeczytaj [podstawowy artykuł dotyczący rozwiązywania problemów z pulpitem zdalnym.](troubleshoot-rdp-connection.md)

Może wystąpić komunikat o błędzie pulpitu zdalnego, który nie przypomina żadnego z określonych komunikatów o błędach ujętych w [podstawowym przewodniku rozwiązywania problemów z pulpitem zdalnym](troubleshoot-rdp-connection.md). Wykonaj następujące kroki, aby ustalić, dlaczego klient pulpitu zdalnego (RDP) nie może połączyć się z usługą RDP na maszynie Wirtualnej platformy Azure.


Jeśli potrzebujesz więcej pomocy w dowolnym momencie tego artykułu, możesz skontaktować się z ekspertami platformy Azure na [platformie MSDN Azure i forach przepełnienia stosu](https://azure.microsoft.com/support/forums/). Alternatywnie można również zgłosić zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i kliknij pozycję Uzyskaj pomoc **techniczną**. Aby uzyskać informacje dotyczące korzystania z pomocy technicznej platformy Azure, przeczytaj [często zadawane pytania dotyczące pomocy technicznej platformy Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="components-of-a-remote-desktop-connection"></a>Składniki połączenia pulpitu zdalnego
Następujące składniki są zaangażowane w połączenie RDP:

![](./media/detailed-troubleshoot-rdp/tshootrdp_0.png)

Przed kontynuowaniem może pomóc psychicznie przejrzeć, co zmieniło się od ostatniego pomyślnego połączenia pulpitu zdalnego z maszyną wirtualną. Przykład:

* Publiczny adres IP maszyny Wirtualnej lub usługi w chmurze zawierającej maszynę wirtualną (nazywaną również wirtualnym adresem IP [VIP)](https://en.wikipedia.org/wiki/Virtual_IP_address)uległ zmianie. Błąd protokołu RDP może wynikać z tego, że pamięć podręczna klienta DNS nadal ma *stary adres IP* zarejestrowany dla nazwy DNS. Opróżnij pamięć podręczną klienta DNS i spróbuj ponownie połączyć maszynę wirtualną. Lub spróbuj połączyć się bezpośrednio z nowym VIP.
* Używasz aplikacji innej firmy do zarządzania połączeniami pulpitu zdalnego zamiast połączenia generowanego przez witrynę Azure portal. Sprawdź, czy konfiguracja aplikacji zawiera poprawny port TCP dla ruchu pulpitu zdalnego. Możesz sprawdzić ten port dla klasycznej maszyny wirtualnej w [witrynie Azure portal,](https://portal.azure.com)klikając ustawienia maszyny wirtualnej > punktami końcowymi.

## <a name="preliminary-steps"></a>Kroki wstępne
Przed przystąpieniem do szczegółowego rozwiązywania problemów

* Sprawdź stan maszyny wirtualnej w witrynie Azure portal dla wszystkich oczywistych problemów.
* Postępuj zgodnie z [instrukcjami szybkiego rozwiązywania typowych błędów RDP w podstawowym przewodniku rozwiązywania problemów](troubleshoot-rdp-connection.md#quick-troubleshooting-steps).
* W przypadku obrazów niestandardowych upewnij się, że dysk VHD jest odpowiednio przygotowany przed przesłaniem. Aby uzyskać więcej informacji, zobacz [Przygotowywanie dysku VHD lub VHDX systemu Windows do przekazania na platformę Azure](../windows/prepare-for-upload-vhd-image.md).


Po tych krokach spróbuj ponownie połączyć się z maszyną wirtualną za pośrednictwem pulpitu zdalnego.

## <a name="detailed-troubleshooting-steps"></a>Szczegółowe kroki rozwiązywania problemów
Klient pulpitu zdalnego może nie być w stanie uzyskać dostępu do usługi pulpitu zdalnego na maszynie Wirtualnej platformy Azure z powodu problemów z następującymi źródłami:

* [Komputer kliencki pulpitu zdalnego](#source-1-remote-desktop-client-computer)
* [Organizacyjne urządzenie brzegowe intranetu](#source-2-organization-intranet-edge-device)
* [Lista punktów końcowych usług w chmurze i kontroli dostępu (ACL)](#source-3-cloud-service-endpoint-and-acl)
* [Grupy zabezpieczeń sieci](#source-4-network-security-groups)
* [Maszyna wirtualna platformy Azure oparta na systemie Windows](#source-5-windows-based-azure-vm)

## <a name="source-1-remote-desktop-client-computer"></a>Źródło 1: Komputer kliencki pulpitu zdalnego
Sprawdź, czy komputer może nawiązywać połączenia pulpitu zdalnego z innym lokalnym komputerem z systemem Windows.

![](./media/detailed-troubleshoot-rdp/tshootrdp_1.png)

Jeśli nie możesz, sprawdź następujące ustawienia na komputerze:

* Ustawienie zapory lokalnej, która blokuje ruch pulpitu zdalnego.
* Lokalnie zainstalowane oprogramowanie proxy klienta, które uniemożliwia połączenia pulpitu zdalnego.
* Lokalnie zainstalowane oprogramowanie do monitorowania sieci, które uniemożliwia połączenia pulpitu zdalnego.
* Inne typy oprogramowania zabezpieczającego, które monitorują ruch lub zezwalają/nie zezwalają na określone typy ruchu, które uniemożliwiają połączenia pulpitu zdalnego.

We wszystkich tych przypadkach tymczasowo wyłącz oprogramowanie i spróbuj połączyć się z komputerem lokalnym za pośrednictwem pulpitu zdalnego. Jeśli w ten sposób możesz dowiedzieć się, jaka jest rzeczywista przyczyna, skontaktuj się z administratorem sieci, aby poprawić ustawienia oprogramowania, aby zezwolić na połączenia pulpitu zdalnego.

## <a name="source-2-organization-intranet-edge-device"></a>Źródło 2: Urządzenie brzegowe intranetu organizacji
Sprawdź, czy komputer bezpośrednio połączony z Internetem może nawiązywać połączenia pulpitu zdalnego z maszyną wirtualną platformy Azure.

![](./media/detailed-troubleshoot-rdp/tshootrdp_2.png)

Jeśli nie masz komputera, który jest bezpośrednio połączony z Internetem, utwórz i przetestuj za pomocą nowej maszyny wirtualnej platformy Azure w grupie zasobów lub usłudze w chmurze. Aby uzyskać więcej informacji, zobacz [Tworzenie maszyny wirtualnej z systemem Windows na platformie Azure](../virtual-machines-windows-hero-tutorial.md). Po teście można usunąć maszynę wirtualną i grupę zasobów lub usługę w chmurze.

Jeśli można utworzyć połączenie pulpitu zdalnego z komputerem podłączonym bezpośrednio do Internetu, sprawdź, czy w urządzeniu brzegowym intranetu organizacji:

* Wewnętrzna zapora blokująca połączenia HTTPS z Internetem.
* Serwer proxy uniemożliwiający połączenia pulpitu zdalnego.
* Oprogramowanie do wykrywania włamań lub monitorowania sieci uruchomione na urządzeniach w sieci brzegowej, które uniemożliwia połączenia pulpitu zdalnego.

Współpracuj z administratorem sieci, aby poprawić ustawienia urządzenia brzegowego intranetu organizacji, aby zezwolić na połączenia pulpitu zdalnego z Internetem oparte na protokoszy z protokołem HTTPS.

## <a name="source-3-cloud-service-endpoint-and-acl"></a>Źródło 3: Punkt końcowy usługi w chmurze i ACL

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

W przypadku maszyn wirtualnych utworzonych przy użyciu klasycznego modelu wdrażania sprawdź, czy inna maszyna wirtualna platformy Azure, która znajduje się w tej samej usłudze w chmurze lub w sieci wirtualnej, może nawiązać połączenia pulpitu zdalnego z maszyną wirtualną platformy Azure.

![](./media/detailed-troubleshoot-rdp/tshootrdp_3.png)

> [!NOTE]
> W przypadku maszyn wirtualnych utworzonych w Menedżerze zasobów przejdź do [źródła 4: Sieciowe grupy zabezpieczeń](#source-4-network-security-groups).

Jeśli nie masz innej maszyny wirtualnej w tej samej usłudze w chmurze lub w sieci wirtualnej, utwórz jedną. Wykonaj kroki opisane w [obszarze Tworzenie maszyny wirtualnej z systemem Windows na platformie Azure](../virtual-machines-windows-hero-tutorial.md). Usuń testową maszynę wirtualną po zakończeniu testu.

Jeśli można połączyć się za pośrednictwem pulpitu zdalnego z maszyną wirtualną w tej samej usłudze w chmurze lub w sieci wirtualnej, sprawdź następujące ustawienia:

* Konfiguracja punktu końcowego dla ruchu pulpitu zdalnego na docelowej maszynie Wirtualnej: Prywatny port TCP punktu końcowego musi być zgodny z portem TCP, na którym nasłuchuje usługa pulpitu zdalnego maszyny Wirtualnej (wartość domyślna to 3389).
* Listna ACL dla punktu końcowego ruchu usług pulpitu zdalnego na docelowej maszynie Wirtualnej: Listy ACL umożliwiają określenie dozwolonego lub odrzuconego ruchu przychodzącego z Internetu na podstawie jego źródłowego adresu IP. Nieprawidłowo skonfigurowane listy ACL mogą uniemożliwić przychodzący ruch pulpitu zdalnego do punktu końcowego. Sprawdź listy ACL, aby upewnić się, że ruch przychodzący z publicznych adresów IP serwera proxy lub innego serwera brzegowego jest dozwolony. Aby uzyskać więcej informacji, zobacz [Co to jest lista kontroli dostępu do sieci (ACL)?](../../virtual-network/virtual-networks-acl.md)

Aby sprawdzić, czy źródłem problemu jest punkt końcowy, usuń bieżący punkt końcowy i utwórz nowy, wybierając losowy port w zakresie 49152–65535 dla numeru portu zewnętrznego. Aby uzyskać więcej informacji, zobacz [Jak skonfigurować punkty końcowe na maszynie wirtualnej](../windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="source-4-network-security-groups"></a>Źródło 4: Sieciowe grupy zabezpieczeń
Sieciowe grupy zabezpieczeń umożliwiają bardziej szczegółową kontrolę dozwolonego ruchu przychodzącego i wychodzącego. Można tworzyć reguły obejmujące podsieci i usługi w chmurze w sieci wirtualnej platformy Azure.

Użyj funkcji [weryfikacji przepływu adresu IP](../../network-watcher/network-watcher-check-ip-flow-verify-portal.md) w celu potwierdzenia, że reguła w sieciowej grupie zabezpieczeń blokuje ruch do lub z maszyny wirtualnej. Można również przejrzeć reguły skutecznej grupy zabezpieczeń, aby upewnić się, że przychodząca reguła sieciowej grupy zabezpieczeń istnieje i jest priorytetowa dla portu RDP (domyślnie 3389). Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z przepływem ruchu maszyn wirtualnych przy użyciu reguł efektywnego zabezpieczeń](../../virtual-network/diagnose-network-traffic-filter-problem.md).

## <a name="source-5-windows-based-azure-vm"></a>Źródło 5: Maszyna wirtualna platformy Azure oparta na systemie Windows
![](./media/detailed-troubleshoot-rdp/tshootrdp_5.png)

Postępuj zgodnie z instrukcjami w [tym artykule](../windows/reset-rdp.md). W tym artykule resetuje usługę pulpitu zdalnego na maszynie wirtualnej:

* Włącz domyślną regułę Zapory systemu Windows "Pulpit zdalny" (port TCP 3389).
* Włącz połączenia pulpitu zdalnego, ustawiając wartość rejestru HKLM\System\CurrentControlSet\Control\Terminal Server\fDenyTSConnections na wartość 0.

Spróbuj ponownie nawiązać połączenie z komputerem. Jeśli połączenie za pośrednictwem pulpitu zdalnego nadal nie jest możliwe, sprawdź, czy nie występują następujące problemy:

* Usługa Pulpit zdalny nie jest uruchomiona na docelowej maszynie wirtualnej.
* Usługa pulpitu zdalnego nie nasłuchuje na porcie TCP 3389.
* Zapora systemu Windows lub inna zapora lokalna ma regułę ruchu wychodzącego, która uniemożliwia ruch pulpitu zdalnego.
* Wykrywanie włamań lub oprogramowanie do monitorowania sieci uruchomione na maszynie wirtualnej platformy Azure uniemożliwia połączenia pulpitu zdalnego.

W przypadku maszyn wirtualnych utworzonych przy użyciu klasycznego modelu wdrażania można użyć zdalnej sesji programu Azure PowerShell na maszynie wirtualnej platformy Azure. Najpierw musisz zainstalować certyfikat dla usługi hostingowej maszyny wirtualnej w chmurze. Przejdź do [konfigurowania bezpiecznego zdalnego dostępu programu PowerShell do maszyn wirtualnych platformy Azure](https://gallery.technet.microsoft.com/scriptcenter/Configures-Secure-Remote-b137f2fe) i pobierz plik skryptu **InstallWinRMCertAzureVM.ps1** na komputer lokalny.

Następnie zainstaluj program Azure PowerShell, jeśli jeszcze tego nie zrobiłeś. Zobacz artykuł [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview).

Następnie otwórz wiersz polecenia programu Azure PowerShell i zmień bieżący folder na lokalizację pliku skryptu **InstallWinRMCertAzureVM.ps1.** Aby uruchomić skrypt programu Azure PowerShell, należy ustawić poprawne zasady wykonywania. Uruchom polecenie **Get-ExecutionPolicy,** aby określić bieżący poziom zasad. Aby uzyskać informacje na temat ustawiania odpowiedniego poziomu, zobacz [Ustawianie-WykonaniePolicy](https://technet.microsoft.com/library/hh849812.aspx).

Następnie wypełnij nazwę subskrypcji platformy Azure, nazwę usługi w chmurze i nazwę maszyny wirtualnej (usuwając < i > znaków), a następnie uruchom te polecenia.

```powershell
$subscr="<Name of your Azure subscription>"
$serviceName="<Name of the cloud service that contains the target virtual machine>"
$vmName="<Name of the target virtual machine>"
.\InstallWinRMCertAzureVM.ps1 -SubscriptionName $subscr -ServiceName $serviceName -Name $vmName
```

Możesz uzyskać poprawną nazwę subskrypcji z *SubscriptionName* właściwości wyświetlania **get-AzureSubscription** polecenia. Nazwę usługi w chmurze dla maszyny wirtualnej można uzyskać z kolumny *ServiceName* na wyświetlaczu polecenia **Get-AzureVM.**

Sprawdź, czy masz nowy certyfikat. Otwórz przystawkę Certyfikaty dla bieżącego użytkownika i poszukaj w folderze **Zaufane główne urzędy certyfikacji\Certyfikaty.** W kolumnie Wystawione do powinno być widoczne certyfikat o nazwie DNS usługi w chmurze (np. cloudservice4testing.cloudapp.net).

Następnie zainicjuj zdalną sesję programu Azure PowerShell przy użyciu tych poleceń.

```powershell
$uri = Get-AzureWinRMUri -ServiceName $serviceName -Name $vmName
$creds = Get-Credential
Enter-PSSession -ConnectionUri $uri -Credential $creds
```

Po wprowadzeniu prawidłowych poświadczeń administratora powinien zostać wyświetlony monit podobny do następującego monitu programu Azure PowerShell:

```powershell
[cloudservice4testing.cloudapp.net]: PS C:\Users\User1\Documents>
```

Pierwsza część tego monitu jest nazwa usługi w chmurze, która zawiera docelową maszynę wirtualną, która może się różnić od "cloudservice4testing.cloudapp.net". Teraz można wydawać polecenia programu Azure PowerShell dla tej usługi w chmurze, aby zbadać wymienione problemy i poprawić konfigurację.

### <a name="to-manually-correct-the-remote-desktop-services-listening-tcp-port"></a>Aby ręcznie poprawić port TCP usług pulpitu zdalnego
W zdalnym wierszu sesji programu Azure PowerShell uruchom to polecenie.

```powershell
Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"
```

Właściwość PortNumber pokazuje bieżący numer portu. W razie potrzeby zmień numer portu pulpitu zdalnego z powrotem na wartość domyślną (3389) za pomocą tego polecenia.

```powershell
Set-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber" -Value 3389
```

Sprawdź, czy port został zmieniony na 3389 za pomocą tego polecenia.

```powershell
Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"
```

Zamknij zdalną sesję programu Azure PowerShell za pomocą tego polecenia.

```powershell
Exit-PSSession
```

Sprawdź, czy punkt końcowy pulpitu zdalnego dla maszyny Wirtualnej platformy Azure również używa portu TCP 3398 jako portu wewnętrznego. Uruchom ponownie maszynę wirtualną platformy Azure i spróbuj ponownie użyć połączenia pulpitu zdalnego.

## <a name="additional-resources"></a>Zasoby dodatkowe
[Jak zresetować hasło lub usługę pulpitu zdalnego dla maszyn wirtualnych z systemem Windows](../windows/reset-rdp.md)

[How to install and configure Azure PowerShell](/powershell/azure/overview)

[Rozwiązywanie problemów z połączeniami secure shell (SSH) z maszyną wirtualną platformy Azure opartą na systemie Linux](../linux/troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Rozwiązywanie problemów z dostępem do aplikacji uruchomionej na maszynie wirtualnej platformy Azure](../linux/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

