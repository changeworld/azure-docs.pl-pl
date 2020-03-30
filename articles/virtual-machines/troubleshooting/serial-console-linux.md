---
title: Konsola szeregowa platformy Azure dla systemu Linux | Dokumenty firmy Microsoft
description: Dwukierunkowa konsola szeregowa dla maszyn wirtualnych platformy Azure i zestawów skalowania maszyn wirtualnych.
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 5/1/2019
ms.author: alsin
ms.openlocfilehash: b1f7708c9bd213e201ba4eb8837a191dca68ca9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77167016"
---
# <a name="azure-serial-console-for-linux"></a>Konsola szeregowa platformy Azure dla systemu Linux

Konsola szeregowa w witrynie Azure portal zapewnia dostęp do konsoli tekstowej dla maszyn wirtualnych systemu Linux (VMs) i wystąpień zestawu skalowania maszyny wirtualnej. To połączenie szeregowe łączy się z portem szeregowym ttys0 wystąpienia zestawu skalowania maszyny wirtualnej lub maszyny wirtualnej, zapewniając dostęp do niego niezależnie od stanu sieci lub systemu operacyjnego. Dostęp do konsoli szeregowej można uzyskać tylko za pomocą witryny Azure portal i jest dozwolony tylko dla tych użytkowników, którzy mają rolę dostępu współautora lub wyższego do zestawu skalowania maszyny wirtualnej lub maszyny wirtualnej.

Konsola szeregowa działa w ten sam sposób dla maszyn wirtualnych i wystąpień zestawu skalowania maszyny wirtualnej. W tym doc wszystkie wzmianki do maszyn wirtualnych niejawnie będzie zawierać wystąpienia zestawu skalowania maszyny wirtualnej, chyba że określono inaczej.

Aby zapoznać się z dokumentacją konsoli szeregowej dla systemu Windows, zobacz [Konsola szeregowa dla systemu Windows](../windows/serial-console.md).

> [!NOTE]
> Konsola szeregowa jest ogólnie dostępna w globalnych regionach platformy Azure i w publicznej wersji zapoznawczej w usłudze Azure Dla Instytucji Rządowych. Nie jest jeszcze dostępna w chmurze Azure China.


## <a name="prerequisites"></a>Wymagania wstępne

- Wystąpienie zestawu skalowania maszyny wirtualnej lub maszyny wirtualnej musi używać modelu wdrażania zarządzania zasobami. Wdrożenia klasyczne nie są obsługiwane.

- Twoje konto korzystające z konsoli szeregowej musi mieć [rolę współautora maszyny wirtualnej](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) dla maszyny wirtualnej i konta magazynu [diagnostyki rozruchu](boot-diagnostics.md)

- Wystąpienie zestawu skalowania maszyny wirtualnej lub maszyny wirtualnej musi mieć użytkownika opartego na hasłach. Można go utworzyć z funkcją [resetowania hasła](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) rozszerzenia dostępu do maszyny Wirtualnej. Wybierz **pozycję Resetuj hasło** z sekcji Pomoc **techniczna + rozwiązywanie problemów.**

- Wystąpienie zestawu skalowania maszyny wirtualnej lub maszyny wirtualnej musi mieć włączoną [diagnostykę rozruchu.](boot-diagnostics.md)

    ![Ustawienia diagnostyki rozruchu](./media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

- Aby uzyskać ustawienia specyficzne dla dystrybucji linuksa, zobacz [Dostępność dystrybucji konsoli szeregowych linuksa](#serial-console-linux-distribution-availability).

- Wystąpienie zestawu skalowania maszyny wirtualnej lub maszyny `ttys0`wirtualnej musi być skonfigurowane dla danych wyjściowych szeregowych w programie . Jest to ustawienie domyślne dla obrazów platformy Azure, ale należy dokładnie sprawdzić to na obrazach niestandardowych. Szczegóły [poniżej](#custom-linux-images).


> [!NOTE]
> Konsola szeregowa wymaga użytkownika lokalnego ze skonfigurowanym hasłem. Maszyny wirtualne lub zestawy skalowania maszyny wirtualnej skonfigurowane tylko przy za pomocą klucza publicznego SSH nie będą mogły zalogować się do konsoli szeregowej. Aby utworzyć użytkownika lokalnego z hasłem, należy użyć [rozszerzenia VMAccess](https://docs.microsoft.com/azure/virtual-machines/linux/using-vmaccess-extension), który jest dostępny w portalu, wybierając **resetowanie hasła** w witrynie Azure portal i utworzyć użytkownika lokalnego z hasłem.
> Hasło administratora można również zresetować na swoim koncie [za pomocą GRUB do rozruchu w trybie pojedynczego użytkownika](./serial-console-grub-single-user-mode.md).

## <a name="serial-console-linux-distribution-availability"></a>Dostępność dystrybucji linuksa konsoli szeregowych
Aby konsola szeregowa działała poprawnie, system operacyjny gościa musi być skonfigurowany do odczytu i zapisu komunikatów konsoli na porcie szeregowym. Większość [zatwierdzonych dystrybucji systemu Azure Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) ma domyślnie skonfigurowany konsoli szeregowej. Wybranie **konsoli szeregowej** w sekcji **Pomoc techniczna + rozwiązywanie problemów** w witrynie Azure Portal zapewnia dostęp do konsoli szeregowej.

> [!NOTE]
> Jeśli nie widzisz niczego w konsoli szeregowej, upewnij się, że diagnostyka rozruchu jest włączona na maszynie Wirtualnej. Naciśnięcie **klawisza Enter** często rozwiązuje problemy, w których nic nie pojawia się w konsoli szeregowej.

Dystrybucja      | Dostęp do konsoli szeregowej
:-----------|:---------------------
Red Hat Enterprise Linux    | Domyślnie włączony jest dostęp do konsoli szeregowego.
CentOS      | Domyślnie włączony jest dostęp do konsoli szeregowego.
Debian      | Domyślnie włączony jest dostęp do konsoli szeregowego.
Ubuntu      | Domyślnie włączony jest dostęp do konsoli szeregowego.
CoreOS      | Domyślnie włączony jest dostęp do konsoli szeregowego.
SUSE        | Nowsze obrazy SLES dostępne na platformie Azure mają domyślnie włączony dostęp do konsoli szeregowej. Jeśli używasz starszych wersji (10 lub starszych) SLES na platformie Azure, zobacz [artykuł KB,](https://www.novell.com/support/kb/doc.php?id=3456486) aby włączyć konsolę szeregową.
Oracle Linux        | Domyślnie włączony jest dostęp do konsoli szeregowego.

### <a name="custom-linux-images"></a>Niestandardowe obrazy linuksa
Aby włączyć konsolę szeregową dla niestandardowego obrazu maszyny Wirtualnej systemu Linux, włącz dostęp `ttyS0`do konsoli w pliku */etc/inittab,* aby uruchomić terminal na . Na przykład: `S0:12345:respawn:/sbin/agetty -L 115200 console vt102`. Może być również konieczne tarło getty na ttyS0. Można to zrobić `systemctl start serial-getty@ttyS0.service`za pomocą .

Należy również dodać ttys0 jako miejsce docelowe dla danych wyjściowych szeregowych. Aby uzyskać więcej informacji na temat konfigurowania obrazu niestandardowego do pracy z konsolą szeregową, zobacz ogólne wymagania systemowe w [temacie Tworzenie i przekazywanie dysku VHD systemu Linux](https://aka.ms/createuploadvhd#general-linux-system-requirements)na platformie Azure .

Jeśli budujesz niestandardowe jądro, rozważ włączenie tych `CONFIG_SERIAL_8250=y` flag `CONFIG_MAGIC_SYSRQ_SERIAL=y`jądra: i . Plik konfiguracyjny zazwyczaj znajduje się w */boot/* path.

## <a name="common-scenarios-for-accessing-the-serial-console"></a>Typowe scenariusze uzyskiwania dostępu do konsoli szeregowej

Scenariusz          | Akcje w konsoli szeregowej
:------------------|:-----------------------------------------
Uszkodzony plik *FSTAB* | Naciśnij klawisz **Enter,** aby kontynuować, a następnie użyć edytora tekstu, aby naprawić plik *FSTAB.* Aby to zrobić, może być konieczne korzystanie z trybu pojedynczego użytkownika. Aby uzyskać więcej informacji, zobacz sekcję konsoli szeregowej [jak rozwiązać problemy z fstab](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors) i Użyj konsoli [szeregowej, aby uzyskać dostęp do GRUB i trybu pojedynczego użytkownika](serial-console-grub-single-user-mode.md).
Nieprawidłowe reguły zapory |  Jeśli skonfigurowano iptables do blokowania łączności SSH, można użyć konsoli szeregowej do interakcji z maszyną wirtualną bez konieczności SSH. Więcej szczegółów można znaleźć na [stronie człowieka iptables](https://linux.die.net/man/8/iptables).<br>Podobnie jeśli zapora blokuje dostęp SSH, można uzyskać dostęp do maszyny Wirtualnej za pośrednictwem konsoli szeregowej i ponownie skonfigurować zaporę. Więcej szczegółów można znaleźć w [dokumentacji zapory](https://firewalld.org/documentation/).
Uszkodzenie/sprawdzanie systemu plików | Zobacz sekcję konsoli szeregowej [maszyny Wirtualnej systemu Azure Linux nie można uruchomić z powodu błędów systemu plików, aby](https://support.microsoft.com/en-us/help/3213321/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck) uzyskać więcej informacji na temat rozwiązywania problemów z uszkodzonymi systemami plików przy użyciu konsoli szeregowej.
Problemy z konfiguracją SSH | Przejdź do konsoli szeregowej i zmień ustawienia. Konsoli szeregowej można używać niezależnie od konfiguracji SSH maszyny wirtualnej, ponieważ nie wymaga ona, aby maszyna wirtualna miała łączność sieciową do pracy. Przewodnik rozwiązywania problemów jest dostępny w [rozwiązywaniu problemów z połączeniami SSH z maszyną wirtualną systemu Azure Linux, która kończy się niepowodzeniem, błędy lub jest odrzucana.](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-ssh-connection) Więcej informacji można znaleźć na [stronie Szczegółowe kroki rozwiązywania problemów z rozwiązywaniem problemów związanych z łączeniem się z maszyną wirtualną z systemem Linux na platformie Azure](./detailed-troubleshoot-ssh-connection.md)
Interakcja z bootloaderem | Uruchom ponownie maszynę wirtualną z poziomu bloku konsoli szeregowej, aby uzyskać dostęp do GRUB na maszynie Wirtualnej systemu Linux. Aby uzyskać więcej informacji i informacji specyficznych dla dystrybucji, zobacz [Dostęp do grub i trybu pojedynczego użytkownika za pomocą konsoli szeregowej.](serial-console-grub-single-user-mode.md)

## <a name="disable-the-serial-console"></a>Wyłączanie konsoli szeregowej

Domyślnie wszystkie subskrypcje mają włączony dostęp do konsoli szeregowego. Można wyłączyć konsolę szeregową na poziomie subskrypcji lub na poziomie zestawu skalowania maszyny wirtualnej/maszyny wirtualnej. Aby uzyskać szczegółowe instrukcje, odwiedź stronę [Włącz i wyłącz konsolę szeregową platformy Azure](./serial-console-enable-disable.md).

## <a name="serial-console-security"></a>Zabezpieczenia konsoli szeregowych

### <a name="access-security"></a>Zabezpieczenia dostępu
Dostęp do konsoli szeregowej jest ograniczony do użytkowników, którzy mają rolę dostępu [współautora maszyny wirtualnej](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) lub wyższej do maszyny wirtualnej. Jeśli dzierżawa usługi Azure Active Directory wymaga uwierzytelniania wieloskładnikowego (MFA), dostęp do konsoli szeregowej będzie również wymagał uwierzytelnienia wieloskładnikowego, ponieważ dostęp konsoli szeregowej odbywa się za pośrednictwem [portalu Azure.](https://portal.azure.com)

### <a name="channel-security"></a>Zabezpieczenia kanałów
Wszystkie dane wysyłane tam iz powrotem są szyfrowane w sieci.

### <a name="audit-logs"></a>Dzienniki inspekcji
Cały dostęp do konsoli szeregowej jest obecnie rejestrowany w [dziennikach diagnostyki rozruchu](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) maszyny wirtualnej. Dostęp do tych dzienników są własnością i są kontrolowane przez administratora maszyny wirtualnej platformy Azure.

> [!CAUTION]
> Nie są rejestrowane żadne hasła dostępu do konsoli. Jeśli jednak polecenia uruchamiane w konsoli zawierają lub wyprowadzają hasła, wpisy tajne, nazwy użytkowników lub inną formę informacji umożliwiających identyfikację użytkownika, zostaną one zapisane w dziennikach diagnostyki rozruchowej maszyny Wirtualnej. Zostaną one napisane wraz z wszystkimi innymi widocznymi tekstami, w ramach implementacji funkcji przewijania konsoli szeregowej. Te dzienniki są okrężne i tylko osoby z uprawnieniami do odczytu do konta magazynu diagnostyki mają do nich dostęp. Jeśli wprowadzasz polecenia danychlub, które zawierają wpisy tajne lub informacje umożliwiające identyfikację użytkownika, zaleca się użycie SSH, chyba że konsola szeregowa jest absolutnie konieczne.

### <a name="concurrent-usage"></a>Jednoczesne użycie
Jeśli użytkownik jest połączony z konsolą szeregową, a inny użytkownik pomyślnie zażąda dostępu do tej samej maszyny wirtualnej, pierwszy użytkownik zostanie rozłączony, a drugi użytkownik połączony z tą samą sesją.

> [!CAUTION]
> Oznacza to, że użytkownik, który jest rozłączony, nie zostanie wylogowany. Możliwość wymuszenia wylogowania po rozłączeniu (przy użyciu SIGHUP lub podobnego mechanizmu) jest nadal na mapie drogowej. W systemie Windows jest włączony automatyczny limit czasu w specjalnej konsoli administracyjnej (SAC); jednak dla Linuksa można skonfigurować ustawienie limitu czasu terminalu. Aby to zrobić, dodaj `export TMOUT=600` plik *.bash_profile* lub *.profile* dla użytkownika, którego używasz do logowania się do konsoli. To ustawienie spowoduje przesuń czas sesji po 10 minutach.

## <a name="accessibility"></a>Ułatwienia dostępu
Ułatwienia dostępu jest głównym celem dla konsoli szeregowej platformy Azure. W tym celu zadbaliśmy o to, aby konsola szeregowa była w pełni dostępna.

### <a name="keyboard-navigation"></a>Nawigacja przy użyciu klawiatury
Użyj **klawisza Tab** na klawiaturze, aby poruszać się w interfejsie konsoli szeregowej z witryny Azure portal. Twoja lokalizacja zostanie podświetlona na ekranie. Aby pozostawić fokus okna konsoli szeregowej, naciśnij klawisz **Ctrl**+**F6** na klawiaturze.

### <a name="use-serial-console-with-a-screen-reader"></a>Używanie konsoli szeregowej z czytnikiem zawartości ekranu
Konsola szeregowa ma wbudowaną obsługę czytnika ekranu. Poruszanie się z włączonym czytnikiem ekranu umożliwi odczytanie tekstu alternatywnego aktualnie wybranego przycisku na głos przez czytnik ekranu.

## <a name="known-issues"></a>Znane problemy
Zdajemy sobie sprawę z niektórych problemów z konsolą szeregową i systemem operacyjnym maszyny Wirtualnej. Oto lista tych problemów i kroki łagodzenia dla maszyn wirtualnych z systemem Linux. Te problemy i środki zaradcze dotyczą zarówno maszyn wirtualnych, jak i wystąpień zestawu skalowania maszyny wirtualnej. Jeśli te błędy nie są zgodne z wyświetlonym błędem, zobacz typowe błędy usługi konsoli szeregowej w [przypadku błędów typowej konsoli szeregowej](./serial-console-errors.md).

Problem                           |   Środki zaradcze
:---------------------------------|:--------------------------------------------|
Naciśnięcie **klawisza Enter** po zakończeniu baneru połączenia nie powoduje wyświetlenia monitu o logowanie. | GRUB może nie być poprawnie skonfigurowany. Uruchom następujące polecenia: `grub2-mkconfig -o /etc/grub2-efi.cfg` i/lub `grub2-mkconfig -o /etc/grub2.cfg`. Aby uzyskać więcej informacji, zobacz [Uderzanie enter nic nie robi](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md). Ten problem może wystąpić, jeśli korzystasz z niestandardowej maszyny Wirtualnej, utwardzonego urządzenia lub konfiguracji GRUB, która powoduje, że linux nie może połączyć się z portem szeregowym.
Tekst konsoli szeregowej zajmuje tylko część rozmiaru ekranu (często po użyciu edytora tekstu). | Konsole szeregowe nie obsługują negocjacji dotyczących rozmiaru okna[(RFC 1073),](https://www.ietf.org/rfc/rfc1073.txt)co oznacza, że nie będzie sygnału SIGWINCH wysyłanego do aktualizacji rozmiaru ekranu, a maszyna wirtualna nie będzie miała wiedzy o rozmiarze terminala. Zainstaluj xterm lub podobne narzędzie, `resize` aby zapewnić ci `resize`polecenie, a następnie uruchom program .
Wklejanie długich ciągów nie działa. | Konsola szeregowa ogranicza długość ciągów wklejonych do terminala do 2048 znaków, aby zapobiec przeciążeniu przepustowości portu szeregowego.
Nieregularne wprowadzanie klawiatury w obrazach SLES BYOS. Wejście klawiatury jest rozpoznawane tylko sporadycznie. | Jest to problem z pakietem Plymouth. Plymouth nie powinny być uruchamiane na platformie Azure, ponieważ nie potrzebujesz ekranu powitalnego, a Plymouth zakłóca możliwość korzystania z platformy konsoli szeregowej. Usuń Plymouth `sudo zypper remove plymouth` z, a następnie uruchom ponownie. Możesz też zmodyfikować linię jądra konfiudu `plymouth.enable=0` GRUB, dołączając ją na końcu wiersza. Można to zrobić, [edytując wpis rozruchu w czasie rozruchu](https://aka.ms/serialconsolegrub#single-user-mode-in-suse-sles)lub `/etc/default/grub`edytując wiersz `grub2-mkconfig -o /boot/grub2/grub.cfg`GRUB_CMDLINE_LINUX w , przebudowując GRUB za pomocą , a następnie ponownie uruchamiając ponownie.


## <a name="frequently-asked-questions"></a>Często zadawane pytania

**P. Jak mogę wysłać opinię?**

A. Przekaż opinię, tworząc problem z https://aka.ms/serialconsolefeedbackgithubem w pliku . Alternatywnie (mniej preferowane), można azserialhelp@microsoft.com wysłać opinię za https://feedback.azure.compośrednictwem lub w kategorii maszyny wirtualnej .

**P. Czy konsola szeregowa obsługuje kopiowanie/wklejanie?**

A. Tak. Użyj **klawiszy Ctrl**+**Shift**+**C** i **Ctrl**+**Shift**+**V,** aby skopiować i wkleić do terminala.

**P. Czy można używać konsoli szeregowej zamiast połączenia SSH?**

A. Chociaż to użycie może wydawać się technicznie możliwe, konsola szeregowa ma być używana głównie jako narzędzie do rozwiązywania problemów w sytuacjach, gdy łączność za pośrednictwem protokołu SSH nie jest możliwa. Firma Microsoft zaleca, aby nie używać konsoli szeregowej jako zamiennika SSH z następujących powodów:

- Konsola szeregowa nie ma tak dużej przepustowości jak SSH. Ponieważ jest to połączenie tylko do tekstu, więcej interakcji gui-heavy są trudne.
- Dostęp do konsoli szeregowej jest obecnie możliwy tylko przy użyciu nazwy użytkownika i hasła. Ponieważ klucze SSH są znacznie bezpieczniejsze niż kombinacje nazwy użytkownika/hasła, z punktu widzenia zabezpieczeń logowania, zalecamy SSH za pomocą konsoli szeregowej.

**P. Kto może włączyć lub wyłączyć konsolę szeregową dla mojej subskrypcji?**

A. Aby włączyć lub wyłączyć konsolę szeregową na poziomie całej subskrypcji, musisz mieć uprawnienia do zapisu do subskrypcji. Role, które mają uprawnienia do zapisu obejmują role administratora lub właściciela. Role niestandardowe mogą mieć również uprawnienia do zapisu.

**P. Kto może uzyskać dostęp do konsoli szeregowej dla mojego zestawu skalowania maszyny wirtualnej/maszyny wirtualnej?**

A. Aby uzyskać dostęp do konsoli szeregowej, musisz mieć rolę współautora maszyny wirtualnej lub wyższą.

**P. Moja konsola szeregowa niczego nie wyświetla, co mam zrobić?**

A. Obraz jest prawdopodobnie nieprawidłowo skonfigurowany w celu uzyskania dostępu do konsoli szeregowego. Aby uzyskać informacje dotyczące konfigurowania obrazu w celu włączenia konsoli szeregowej, zobacz [Dostępność dystrybucji konsoli szeregowej systemu Linux](#serial-console-linux-distribution-availability).

**Pyt.: Czy konsola szeregowa jest dostępna dla zestawów skalowania maszyny wirtualnej?**

A. Tak! Zobacz [Konsola szeregowa dla zestawów skalowania maszyny wirtualnej](serial-console-overview.md#serial-console-for-virtual-machine-scale-sets)

**P. Jeśli skonfigurowano skalę maszyny wirtualnej lub maszyny wirtualnej ustawioną przy użyciu tylko uwierzytelniania za pomocą klucza SSH, czy nadal mogę używać konsoli szeregowej do łączenia się z wystąpieniem zestawu skalowania maszyny wirtualnej/maszyny wirtualnej?**

A. Tak. Ponieważ konsola szeregowa nie wymaga kluczy SSH, wystarczy skonfigurować kombinację nazwy użytkownika i hasła. Można to zrobić, wybierając **resetowanie hasła** w witrynie Azure portal i przy użyciu tych poświadczeń, aby zalogować się do konsoli szeregowej.

## <a name="next-steps"></a>Następne kroki
* Użyj konsoli szeregowej, aby [uzyskać dostęp do GRUB i trybu pojedynczego użytkownika](serial-console-grub-single-user-mode.md).
* Użyj konsoli szeregowej dla [połączeń NMI i SysRq](serial-console-nmi-sysrq.md).
* Dowiedz się, jak używać konsoli szeregowej do [włączania GRUB w różnych dystrybucjach](serial-console-grub-proactive-configuration.md)
* Konsola szeregowa jest również dostępna dla [maszyn wirtualnych z systemem Windows](../windows/serial-console.md).
* Dowiedz się więcej o [diagnostyce rozruchu](boot-diagnostics.md).

