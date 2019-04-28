---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: bc3590e90cacfa4966f0d1f64aa1c8d49483cb1b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61485464"
---
W tym artykule zostały podane odpowiedzi na niektóre często zadawane przez użytkowników pytania dotyczące maszyn wirtualnych na platformie Azure utworzonych za pomocą klasycznego modelu wdrażania.

## <a name="can-i-migrate-my-vm-created-in-the-classic-deployment-model-to-the-new-resource-manager-model"></a>Czy mogę zmigrować swoje maszyny wirtualne utworzone w klasycznym modelu wdrażania do nowego modelu menedżera zasobów?
Tak. Aby uzyskać instrukcje dotyczące sposobu przeprowadzania migracji, zobacz:

* [Migracja z wersji klasycznej do usługi Azure Resource Manager przy użyciu programu Azure PowerShell](../articles/virtual-machines/windows/migration-classic-resource-manager-ps.md)
* [Migracja z wersji klasycznej do usługi Azure Resource Manager przy użyciu interfejsu wiersza polecenia platformy Azure](../articles/virtual-machines/virtual-machines-linux-cli-migration-classic-resource-manager.md)

## <a name="what-can-i-run-on-an-azure-vm"></a>Co mogę uruchomić na maszynie wirtualnej platformy Azure?
Wszyscy subskrybenci mogą uruchomić oprogramowanie serwerowe na maszynie wirtualnej platformy Azure. Możesz uruchomić nowe wersje systemu Windows Server oraz różne dystrybucje systemu Linux. Aby uzyskać szczegółowe informacje na temat pomocy technicznej, zobacz:

• Dla maszyn wirtualnych z systemem Windows — [pomoc techniczna dla oprogramowania serwerowego firmy Microsoft dla usługi Azure Virtual Machines](https://go.microsoft.com/fwlink/p/?LinkId=393550)

• Dla maszyn wirtualnych z systemem Linux — [dystrybucje systemu Linux zalecane dla platformy Azure](https://go.microsoft.com/fwlink/p/?LinkId=393551)

Dla obrazów klienta systemu Windows niektóre wersje systemów Windows 7 i Windows 8.1 są dostępne dla subskrybentów korzyści MSDN na platformie Azure i subskrybentów MSDN: tworzenie/testowanie — płatność zgodnie z rzeczywistym użyciem dla zadań tworzenia i testowania. Aby uzyskać szczegółowe informacje, łącznie z instrukcjami i ograniczeniami, zobacz [Windows Client images for MSDN subscribers](https://azure.microsoft.com/blog/2014/05/29/windows-client-images-on-azure/) (Obrazy klienta systemu Windows dla subskrybentów MSDN).

## <a name="why-are-affinity-groups-being-deprecated"></a>Dlaczego grupy koligacji stały się przestarzałe?
Grupy koligacji to starsza koncepcja geograficznego grupowania wdrożeń usługi w chmurze klienta i kont magazynu w ramach platformy Azure. Zostały one początkowo wprowadzone w celu zwiększenia wydajności sieci maszyn wirtualnych we wczesnych projektach sieci platformy Azure. Obsługiwały one też początkową wersję sieci wirtualnych (VNet), które były ograniczone do niewielkiego zbioru sprzętu w regionie.

Obecna sieć platformy Azure w obrębie regionu jest zaprojektowana tak, aby grupy koligacji nie były już wymagane. Sieci wirtualne znajdują się również w zakresie regionalnym, więc grupy koligacji nie są już wymagane, gdy korzystasz z sieci wirtualnej. Ze względu na te ulepszenia nie zalecamy już klientom używania grup koligacji, ponieważ może to stanowić ograniczenie w niektórych scenariuszach. Korzystanie z grup koligacji niepotrzebnie skojarzy Twoje maszyny wirtualne z określonym sprzętem, co ogranicza wybór dostępnych dla Ciebie rozmiarów maszyn wirtualnych. Może to również prowadzić do błędów związanych z pojemnością, gdy spróbujesz dodać nowe maszyny wirtualne, jeśli określony sprzęt skojarzony z grupą koligacji jest bliski osiągnięcia swojej pojemności.

Funkcje grupy koligacji są już przestarzałe w modelu wdrażania przy użyciu usługi Azure Resource Manager i w witrynie Azure Portal. Dla klasycznego portalu Azure wycofaliśmy obsługę tworzenia grup koligacji i tworzenia zasobów magazynu, które są przypięte do grupy koligacji. Nie jest potrzebne modyfikowanie istniejących usług w chmurze korzystających z grupy koligacji. Jednak nie należy używać grup koligacji dla nowych usług w chmurze, chyba że specjalista pomocy technicznej platformy Azure je zaleci.

## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>Ile pamięci masowej mogę użyć w maszynie wirtualnej?
Każdy dysk danych może mieć maksymalnie 1 TB. Liczba dysków danych, których możesz użyć, zależy od rozmiaru maszyny wirtualnej. Aby uzyskać szczegółowe informacje, zobacz [Sizes for virtual machines](../articles/virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Rozmiary maszyn wirtualnych).

Konto magazynu platformy Azure udostępnia pamięć masową dla dysku systemu operacyjnego i wszelkich dysków z danymi. Każdy dysk jest plikiem VHD przechowywanym jako stronicowy obiekt blob. Aby uzyskać szczegółowe informacje o cenach, zobacz [Szczegóły cennika magazynu](https://go.microsoft.com/fwlink/p/?LinkId=396819).

## <a name="which-virtual-hard-disk-types-can-i-use"></a>Jakich typów wirtualnego dysku twardego mogę używać?
Platforma Azure obsługuje tylko stałe wirtualne dyski twarde w formacie VHD. Jeśli masz dysk VHDX, którego chcesz używać na platformie Azure, musisz najpierw dokonać jego konwersji za pomocą menedżera funkcji Hyper-V lub polecenia cmdlet [convert-VHD](https://go.microsoft.com/fwlink/p/?LinkId=393656). Po wykonaniu tej czynności za pomocą polecenia cmdlet [Add-AzureVHD](https://msdn.microsoft.com/library/azure/dn495173.aspx) (w trybie zarządzania usługami) przekaż dysk VHD na konto magazynu na platformie Azure, aby móc go używać z maszynami wirtualnymi.

* Aby uzyskać instrukcje dla systemu Linux, zobacz [Creating and Uploading a Virtual Hard Disk that Contains the Linux Operating System](../articles/virtual-machines/linux/classic/create-upload-vhd-classic.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json) (Tworzenie wirtualnego dysku twardego zawierającego system operacyjny Linux i przekazywanie go).

## <a name="are-these-virtual-machines-the-same-as-hyper-v-virtual-machines"></a>Czy te maszyny wirtualne są takie same, jak maszyny wirtualne funkcji Hyper-V?
Pod wieloma względami są one podobne do maszyn wirtualnych „Generacji 1” funkcji Hyper-V, ale nie są dokładnie takie same. Oba typy zapewniają zwirtualizowany sprzęt, a wirtualne dyski twarde w formacie VHD są zgodne. Oznacza to, że możesz je przenosić między funkcją Hyper-V a platformą Azure. Trzema podstawowymi różnicami, które czasami zaskakują użytkowników funkcji Hyper-V, są:

* Platforma Azure nie zapewnia dostępu do konsoli maszyny wirtualnej. Nie istnieje sposób uzyskania dostępu do maszyny wirtualnej, dopóki nie zakończy ona rozruchu.
* Maszyny wirtualne platformy Azure w większości [rozmiarów](../articles/virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) mają tylko 1 wirtualną kartę sieciową, co oznacza, że mogą mieć również tylko 1 zewnętrzny adres IP. (Rozmiary A8 i A9 używają drugiej karty sieciowej do aplikacji komunikacyjnych między wystąpieniami w ograniczonych scenariuszach).
* Maszyny wirtualne platformy Azure nie obsługują funkcji maszyn wirtualnych 2. generacji funkcji Hyper-V. Aby uzyskać więcej informacji o tych funkcjach, zobacz [Virtual Machine Specifications for Hyper-V](https://technet.microsoft.com/library/dn592184.aspx) (Specyfikacje maszyny wirtualnej dla funkcji Hyper-V) i [Generation 2 Virtual Machine Overview](https://technet.microsoft.com/library/dn282285.aspx) (Maszyna wirtualna 2. generacji — omówienie).

## <a name="can-these-virtual-machines-use-my-existing-on-premises-networking-infrastructure"></a>Czy te maszyny wirtualne mogą korzystać z mojej istniejącej lokalnej infrastruktury sieci?
Dla maszyn wirtualnych utworzonych w klasycznym modelu wdrażania możesz użyć sieci wirtualnej platformy Azure, aby rozszerzyć swoją istniejącą infrastrukturę. Podejście to przypomina konfigurowanie biura oddziału. Możesz aprowizować wirtualne sieci prywatne (VPN) na platformie Azure i zarządzać nimi oraz bezpiecznie łączyć je z lokalną infrastrukturą IT. Aby uzyskać szczegółowe informacje, zobacz [Omówienie usługi Virtual Network](../articles/virtual-network/virtual-networks-overview.md).

Podczas tworzenia maszyny wirtualnej musisz określić sieć, do której ma należeć maszyna wirtualna. Nie możesz dołączyć istniejącej maszyny wirtualnej do sieci wirtualnej. Możesz jednak obejść ten problem, odłączając wirtualny dysk twardy od istniejącej maszyny wirtualnej, a następnie używając go do utworzenia nowej maszyny wirtualnej z żądaną konfiguracją sieci.

## <a name="how-can-i-access--my-virtual-machine"></a>Jak mogę uzyskać dostęp do mojej maszyny wirtualnej?
Należy ustanowić połączenia zdalnego do logowania się do maszyny wirtualnej przy użyciu usługi Podłączanie pulpitu zdalnego dla maszyny Wirtualnej z systemem Windows lub protokołu Secure Shell (SSH) dla maszyny Wirtualnej z systemem Linux. Aby uzyskać instrukcje, zobacz:

* [Jak logować się do maszyny wirtualnej z systemem Windows Server](../articles/virtual-machines/windows/classic/connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Obsługiwane są maksymalnie 2 równoczesne połączenia, chyba że serwer jest skonfigurowany jako host sesji usług pulpitu zdalnego.  
* [Jak logować się do maszyny wirtualnej z systemem Linux](../articles/virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Domyślnie protokół SSH umożliwia maksymalnie 10 równoczesnych połączeń. Możesz zwiększyć tę liczbę, edytując plik konfiguracji.

Jeśli masz problemy z pulpitem zdalnym lub protokołem SSH, zainstaluj rozszerzenie [VMAccess](../articles/virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) i użyj go w celu łatwiejszego rozwiązania problemu.

Dla maszyn wirtualnych z systemem Windows dostępne są następujące opcje dodatkowe:

* W portalu Azure Znajdź maszynę Wirtualną, a następnie kliknij przycisk **Resetuj dostęp zdalny** na pasku poleceń.
* Zapoznaj się z tematem [Rozwiązywanie problemów z połączeniami pulpitu zdalnego nawiązywanymi z maszyną wirtualną platformy Azure z systemem Windows](../articles/virtual-machines/windows/troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Użyj funkcji komunikacji zdalnej programu Windows PowerShell, aby połączyć się z maszyną wirtualną, lub utwórz dodatkowe punkty końcowe dla innych zasobów, aby nawiązać połączenie z maszyną wirtualną. Aby uzyskać szczegółowe informacje, zobacz [How to Set Up Endpoints to a Virtual Machine](../articles/virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) (Jak skonfigurować punkty końcowe do maszyny wirtualnej).

Jeśli znasz funkcję Hyper-V, możesz poszukać narzędzia podobnego do VMConnect. Platforma Azure nie oferuje podobnego narzędzia, ponieważ dostęp do konsoli maszyny wirtualnej nie jest obsługiwany.

## <a name="can-i-use-the-temporary-disk-the-d-drive-for-windows-or-devsdb1-for-linux-to-store-data"></a>Czy mogę użyć dysku tymczasowego (dysku D: dla systemu Windows lub /dev/sdb1 dla systemu Linux) do przechowywania danych?
Do przechowywania danych nie należy używać dysku tymczasowego (domyślnie dysku D: dla systemu Windows lub /dev/sdb1 dla systemu Linux). Są to wyłącznie magazyny tymczasowe, czyli istnieje ryzyko utraty danych, których nie będzie można odzyskać. Może to wystąpić, gdy maszyna wirtualna zostanie przeniesiona do innego hosta. Zmiana rozmiaru maszyny wirtualnej, aktualizowanie hosta lub awaria sprzętu na hoście to kilka przyczyn, które mogą spowodować przeniesienie maszyny wirtualnej.

## <a name="how-can-i-change-the-drive-letter-of-the-temporary-disk"></a>Jak mogę zmienić literę dysku tymczasowego?
W maszynie wirtualnej z systemem Windows możesz zmienić literę dysku, przenosząc plik stronicowania i ponowne przypisując literę dysku, ale musisz się upewnić, że czynności zostały wykonane w określonej kolejności. Aby uzyskać instrukcje, zobacz [Change the drive letter of the Windows temporary disk](../articles/virtual-machines/windows/change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) (Zmiana litery dysku tymczasowego w systemie Windows).

## <a name="how-can-i-upgrade-the-guest-operating-system"></a>Jak mogę uaktualnić system operacyjny gościa?
Termin uaktualnienie zazwyczaj oznacza przejście do nowszej wersji systemu operacyjnego, pozostając na tym samym urządzeniu. Dla maszyn wirtualnych platformy Azure proces przechodzenia do nowszej wersji różni się dla systemów Linux i Windows:

* Dla maszyn wirtualnych z systemem Linux użyj narzędzi i procedur do zarządzania pakietem odpowiednich dla dystrybucji.
* Dla maszyny wirtualnej z systemem Windows musisz przeprowadzić migrację serwera przy użyciu czegoś podobnego do narzędzi migracji systemu Windows Server. Nie podejmuj próby uaktualnienia systemu operacyjnego gościa, gdy znajduje się on na platformie Azure. Nie jest on obsługiwany ze względu na ryzyko utraty dostępu do maszyny wirtualnej. Jeśli problem wystąpi podczas uaktualniania, możesz utracić możliwość rozpoczynania sesji pulpitu zdalnego i nie będzie można rozwiązać problemów.

Aby uzyskać ogólne informacje dotyczące narzędzi i procesów migracji systemu Windows Server, zobacz [Migrowanie ról i funkcji do systemu Windows Server](https://go.microsoft.com/fwlink/p/?LinkId=396940).

## <a name="whats-the-default-user-name-and-password-on-the-virtual-machine"></a>Jaka jest domyślna nazwa użytkownika i hasło na maszynie wirtualnej?
Obrazy dostarczane przez platformę Azure nie mają wstępnie skonfigurowanej nazwy użytkownika ani hasła. Podczas tworzenia maszyny wirtualnej przy użyciu jednego z tych obrazów należy podać nazwę użytkownika i hasło, którego będziesz używać do logowania się do maszyny wirtualnej.

Jeśli nie pamiętasz nazwy użytkownika lub hasła, a został zainstalowany agent maszyny wirtualnej, możesz zainstalować rozszerzenie [VMAccess](../articles/virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) i użyć go w celu rozwiązania tego problemu.

Dodatkowe szczegóły:

* Jeśli używasz witryny Azure portal, dla obrazów systemu Linux "azureuser" jest podawana jako domyślna nazwa użytkownika, ale możesz to zmienić za pomocą "Z galerii" zamiast "Szybkie tworzenie" jako sposób do utworzenia maszyny wirtualnej. Użycie pozycji „Z galerii” umożliwia również podjęcie decyzji co do tego, czy do logowania użyć hasła, klucza SSH, czy też ich obu. Konto użytkownika jest kontem użytkownika bez uprawnień, który ma dostęp „sudo” do uruchamiania poleceń uprzywilejowanych. Konto użytkownika „root” jest wyłączone.
* W przypadku obrazów systemu Windows podczas tworzenia maszyny wirtualnej musisz podać nazwę użytkownika i hasło. Konto jest dodawane do grupy Administratorzy.

## <a name="can-azure-run-anti-virus-on-my-virtual-machines"></a>Czy platforma Azure może uruchamiać oprogramowanie antywirusowe na moich maszynach wirtualnych?
Platforma Azure oferuje kilka rozwiązań dla oprogramowania antywirusowego, ale zarządzanie nimi należy do Ciebie. Na przykład możesz potrzebować oddzielnej subskrypcji oprogramowania chroniącego przed złośliwym oprogramowaniem, a następnie musisz zdecydować, kiedy uruchamiać skanowania i instalować aktualizacje. Obsługę antywirusową możesz dodać za pomocą rozszerzenia maszyny wirtualnej dla usługi firmy Microsoft chroniącej przed złośliwym kodem, programu Symantec Endpoint Protection lub agenta TrendMicro Deep Security Agent podczas tworzenia maszyny wirtualnej systemu Windows lub w późniejszym czasie. Rozszerzenia firm Symantec i TrendMicro pozwalają na korzystanie z bezpłatnej subskrypcji próbnej ograniczonej czasowo lub istniejącej subskrypcji przedsiębiorstwa. Usługa firmy Microsoft chroniąca przed złośliwym kodem jest bezpłatna. Aby uzyskać szczegółowe informacje, zobacz:

* [Jak zainstalować i skonfigurować rozwiązanie Symantec Endpoint Protection na maszynie wirtualnej platformy Azure](https://go.microsoft.com/fwlink/p/?LinkId=404207)
* [Jak zainstalować i skonfigurować rozwiązanie Security as a Service o nazwie Trend Micro Deep na maszynie wirtualnej platformy Azure](https://go.microsoft.com/fwlink/p/?LinkId=404206)
* [Wdrażanie rozwiązań do ochrony przed złośliwym kodem na maszynach wirtualnych platformy Azure](https://azure.microsoft.com/blog/2014/05/13/deploying-antimalware-solutions-on-azure-virtual-machines/)

## <a name="what-are-my-options-for-backup-and-recovery"></a>Jakie są możliwości tworzenia kopii zapasowych i odzyskiwania?
Kopia zapasowa Azure jest dostępna w pewnych regionach jako wersja zapoznawcza. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie kopii zapasowych maszyn wirtualnych platformy Azure](../articles/backup/backup-azure-arm-vms.md). Inne rozwiązania są dostępne u certyfikowanych partnerów. Aby dowiedzieć się, co jest obecnie dostępne, przeszukaj witrynę Azure Marketplace.

Dodatkową opcją jest używanie możliwości migawki magazynu obiektów blob. Aby to zrobić, musisz wyłączyć maszynę wirtualną przed dowolną operacją, która zależy od migawki obiektu blob. Zapisuje to oczekujące zapisy danych i umieszcza system plików w spójnym stanie.

## <a name="how-does-azure-charge-for-my-vm"></a>Jak platforma Azure nalicza opłaty za moją maszynę wirtualną?
Na platformie Azure obowiązuje cena za godzinę, która jest określana na podstawie rozmiaru maszyny wirtualnej i jej systemu operacyjnego. Za kolejne rozpoczęte godziny na użytkowników platformy Azure są nakładane opłaty wyłącznie za wykorzystane minuty. Jeśli utworzysz maszynę wirtualną z obrazem maszyny wirtualnej zawierającym pewne wstępnie zainstalowane oprogramowanie, mogą mieć zastosowanie dodatkowe opłaty godzinowe za oprogramowanie. Platforma Azure nalicza opłaty oddzielnie za pamięć masową na system operacyjny maszyny wirtualnej i dyski danych. Tymczasowy magazyn dysków jest bezpłatny.

Opłaty są naliczane, gdy stan maszyny wirtualnej to Uruchomiona lub Zatrzymana, ale nie są naliczane, gdy stan maszyny wirtualnej to Zatrzymana (cofnięty przydział). Aby umieścić maszynę wirtualną w stanie Zatrzymana (cofnięty przydział), wykonaj jedną z następujących czynności:

* Wyłącz lub Usuń maszynę Wirtualną w witrynie Azure portal.
* Użyj polecenia cmdlet Stop-AzureVM dostępnego w module Azure PowerShell.
* Użyj operacji zamknięcia roli w interfejsie API REST zarządzania usługi i określ wartość StoppedDeallocated dla elementu PostShutdownAction.

Aby uzyskać więcej informacji, zobacz [Virtual Machines Pricing](https://azure.microsoft.com/pricing/details/virtual-machines/) (Cennik usługi Virtual Machines).

## <a name="will-azure-reboot-my-vm-for-maintenance"></a>Czy platforma Azure ponownie uruchomi moją maszynę wirtualną w celu konserwacji?
Platforma Azure czasami ponownie uruchamia Twoją maszynę wirtualną jako część regularnych, planowanych aktualizacji konserwacji w centrach danych platformy Azure.

Nieplanowane zdarzenia konserwacji mogą wystąpić, gdy platforma Azure wykryje poważny problem sprzętowy, który ma wpływ na Twoją maszynę wirtualną. W przypadku nieplanowanych zdarzeń platforma Azure automatycznie przeprowadza migrację maszyny wirtualnej na hosta w dobrej kondycji i ponownie uruchamia maszynę wirtualną.

Dla dowolnej autonomicznej maszyny wirtualnej (tzn. gdy maszyna wirtualna nie jest częścią zestawu dostępności) platforma Azure powiadamia administratora usługi subskrypcji za pośrednictwem poczty e-mail co najmniej tydzień przed zaplanowaną konserwacją, ponieważ maszyny wirtualne mogą zostać ponownie uruchomione podczas aktualizacji. Może nastąpić przestój w działaniu aplikacji uruchomionych na maszynach wirtualnych.

Możesz również można użyć witryny Azure portal lub programu Azure PowerShell Aby wyświetlić dzienniki ponownego uruchomienia, gdy ponowne uruchomienie nastąpiło z powodu planowanej konserwacji. Aby uzyskać szczegółowe informacje, zobacz [Wyświetlanie dzienników ponownego uruchamiania maszyny wirtualnej](https://azure.microsoft.com/blog/2015/04/01/viewing-vm-reboot-logs/).

Aby zapewnić nadmiarowość, umieść co najmniej dwie podobnie skonfigurowane maszyny wirtualne w tym samym zestawie dostępności. Pomaga to w zapewnieniu, że co najmniej jedna maszyna wirtualna jest dostępna podczas planowanej lub nieplanowanej konserwacji. Platforma Azure gwarantuje pewne poziomy dostępności maszyny wirtualnej dla tej konfiguracji. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie dostępnością maszyn wirtualnych](../articles/virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="additional-resources"></a>Dodatkowe zasoby
[Informacje o maszynach wirtualnych platformy Azure](../articles/virtual-machines/virtual-machines-linux-about.md)

[Tworzenie maszyn wirtualnych z systemem Linux i zarządzanie nimi za pomocą interfejsu wiersza polecenia platformy Azure](../articles/virtual-machines/linux/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Utwórz Windows maszyn wirtualnych i zarządzanie przy użyciu programu Azure PowerShell](../articles/virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

