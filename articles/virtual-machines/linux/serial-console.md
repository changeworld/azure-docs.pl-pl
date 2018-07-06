---
title: Konsola szeregowa maszyny wirtualnej platformy Azure | Dokumentacja firmy Microsoft
description: Dwukierunkowa konsoli szeregowej maszyn wirtualnych platformy Azure.
services: virtual-machines-linux
documentationcenter: ''
author: harijay
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/05/2018
ms.author: harijay
ms.openlocfilehash: 00a776131321500386b507f45ea84817b08147f7
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/06/2018
ms.locfileid: "37867865"
---
# <a name="virtual-machine-serial-console-preview"></a>Konsola szeregowa maszyny wirtualnej (wersja zapoznawcza) 


Konsola szeregowa maszyny wirtualnej na platformie Azure zapewnia dostęp do konsoli usługi oparte na tekście dla maszyn wirtualnych systemu Linux i Windows. To połączenie szeregowe jest port szeregowy COM1 maszyny wirtualnej i zapewnia dostęp do maszyny wirtualnej i nie są powiązane z siecią maszyny wirtualnej / stan systemu operacyjnego. Dostęp do konsoli szeregowej maszyny wirtualnej można zrobić tylko przy użyciu witryny Azure portal aktualnie i dozwolone tylko dla tych użytkowników, którzy mają Współautor maszyny Wirtualnej lub wyższym dostęp do maszyny wirtualnej. 

> [!Note] 
> Wersje zapoznawcze są udostępniane pod warunkiem, że wyrażasz zgodę na warunki użytkowania. Aby uzyskać więcej informacji zobacz [Microsoft Azure dodatkowym warunkom użytkowania wersji zapoznawczych platformy Microsoft.] (https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Obecnie ta usługa jest w **publicznej wersji zapoznawczej** i dostęp do konsoli szeregowej dla maszyn wirtualnych jest dostępny dla globalnych regionów platformy Azure. W tym momencie konsoli szeregowej nie jest dostępna chmury Azure Government, Azure (Niemcy) i Azure (Chiny).


## <a name="prerequisites"></a>Wymagania wstępne 

* Należy używać modelu wdrażania usługi resource management. W przypadku wdrożeń klasycznych nie są obsługiwane. 
* Maszyna wirtualna musi mieć [diagnostykę rozruchu](boot-diagnostics.md) włączone 
* Konto, za pomocą konsoli szeregowej musi mieć [rola "Współautor"](../../role-based-access-control/built-in-roles.md) dla maszyny Wirtualnej i [diagnostykę rozruchu](boot-diagnostics.md) konta magazynu. 
* Aby uzyskać ustawienia właściwe dla dystrybucja systemu Linux, zobacz [uzyskiwania dostępu do konsoli szeregowej dla systemu Linux](#accessing-serial-console-for-linux)


## <a name="open-the-serial-console"></a>Otwieranie konsoli szeregowej
Konsola szeregowa dla maszyn wirtualnych jest dostępny za pośrednictwem tylko [witryny Azure portal](https://portal.azure.com). Poniżej przedstawiono kroki umożliwiające dostęp do konsoli szeregowej dla maszyn wirtualnych za pomocą portalu 

  1. Otwórz witrynę Azure portal
  2. W menu po lewej stronie wybierz maszyny wirtualne.
  3. Kliknij maszynę Wirtualną na liście. Zostanie otwarta strona Przegląd dla maszyny Wirtualnej.
  4. Przewiń w dół, pomoc techniczna i rozwiązywanie problemów z sekcji, a następnie kliknij opcję Konsola szeregowa (wersja zapoznawcza). Nowe okienko z konsolą szeregową otworzy się i rozpocząć połączenie.

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-connect.gif)


> [!NOTE] 
> Konsoli szeregowej wymaga użytkownika lokalnego przy użyciu hasła skonfigurowane. W tej chwili maszyny wirtualne tylko przy użyciu klucza publicznego SSH nie będzie dostępu do konsoli szeregowej. Aby utworzyć użytkownika lokalnego przy użyciu hasła, postępuj zgodnie z [rozszerzenia dostępu do maszyny Wirtualnej](https://docs.microsoft.com/azure/virtual-machines/linux/using-vmaccess-extension) i Utwórz użytkownika lokalnego przy użyciu hasła.

### <a name="disable-feature"></a>Wyłączanie funkcji
Funkcje konsoli szeregowej będzie możliwa ich dezaktywacja dla określonych maszyn wirtualnych przez wyłączenie ustawienia diagnostyki rozruchu dla tej maszyny Wirtualnej.

## <a name="serial-console-security"></a>Zabezpieczenia konsoli szeregowej 

### <a name="access-security"></a>Zabezpieczenia dostępu 
Dostęp do konsoli szeregowej jest ograniczony do użytkowników, którzy mają [współautorzy maszyny Wirtualnej](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) lub powyżej dostęp do maszyny wirtualnej. Jeśli dzierżawa usługi AAD wymaga uwierzytelniania wieloskładnikowego, a następnie dostęp do konsoli szeregowej muszą także MFA się jego dostęp za pośrednictwem [witryny Azure portal](https://portal.azure.com).

### <a name="channel-security"></a>Zabezpieczenia kanału
Wszystkie dane są wysyłane ponownie i do przodu, są szyfrowane w sieci.

### <a name="audit-logs"></a>Dzienniki inspekcji
Dostęp do konsoli szeregowej jest aktualnie zalogowany [diagnostykę rozruchu](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) dzienniki maszyny wirtualnej. Dostęp do tych dzienników są własnością i kontrolowane przez administratora maszyny wirtualnej platformy Azure.  

>[!CAUTION] 
Po zalogowaniu się żadne hasła dostępu do konsoli, czy polecenia uruchamiane w ramach konsoli programu zawiera dane wyjściowe haseł, kluczy tajnych, nazwy użytkowników lub inne formy z osobiście identyfikowalne dane osobowe, te zostaną zapisane Diagnostyka rozruchu maszyny wirtualnej rejestruje oraz wszystkie inne teksty widoczne, jako część wykonania funkcji scrollback konsoli szeregowej. Te dzienniki są cykliczne i tylko osoby z uprawnieniami do odczytu do konta magazynu diagnostyki mają do nich dostępu, jednak zalecamy następujące najlepsze rozwiązanie polegające na przy użyciu konsoli SSH dla wszystkich elementów, które mogą obejmować wpisów tajnych i/lub dane osobowe. 

### <a name="concurrent-usage"></a>Współbieżne użycie
Jeśli użytkownik jest połączony z konsoli szeregowej i inny użytkownik pomyślnie żąda dostępu do tej samej maszyny wirtualnej, zostaną rozłączone pierwszego użytkownika, a drugi użytkownik nawiązał połączenie w sposób, podobnie pierwszy użytkownik, stały i pozostawienie niezmienionej konsoli fizycznej i nowy Użytkownik, znajdują się w dół.

>[!CAUTION] 
Oznacza to, że użytkownik, który pobiera odłączony nie będą rejestrowane! Możliwość wymuszenia wylogowania po rozłączenia (za pośrednictwem SIGHUP lub podobny mechanizm) nadal działa w planach. Dla Windows ma automatycznego limitu czasu w SAC włączone jednak dla systemu Linux można skonfigurować ustawienia limitu czasu w terminalu. Aby zrobić to po prostu Dodaj `export TMOUT=600` .bash_profile lub podstawową dla użytkownika logowania w konsoli, przekroczenie limitu czasu sesji, po upływie 10 minut.

### <a name="disable-feature"></a>Wyłączanie funkcji
Funkcje konsoli szeregowej będzie możliwa ich dezaktywacja dla określonych maszyn wirtualnych przez wyłączenie ustawienia diagnostyki rozruchu dla tej maszyny Wirtualnej.

## <a name="common-scenarios-for-accessing-serial-console"></a>Typowe scenariusze dotyczące uzyskiwania dostępu do konsoli szeregowej 
Scenariusz          | Akcje w konsoli szeregowej                |  Zastosowanie systemu operacyjnego 
:------------------|:-----------------------------------------|:------------------
Uszkodzony plik FSTAB | Wprowadź klucz, aby kontynuować, a następnie usuń plik fstab za pomocą edytora tekstów. Zobacz [Rozwiązywanie problemów z fstab](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors) | Linux 
Reguły zapory niepoprawne | Dostęp do konsoli szeregowej i napraw iptables lub reguły zapory Windows | Linux/Windows 
System plików uszkodzenie/wyboru | Dostęp do konsoli szeregowej i odzyskiwanie systemu plików | Linux/Windows 
Problemy z konfiguracją protokołu RDP/SSH | Dostęp do konsoli szeregowej i zmienianie ustawień | Linux/Windows 
Blokowanie sieci w systemie| Dostęp do konsoli szeregowej za pośrednictwem portalu zarządzania systemem | Linux/Windows 
Interakcja z programu inicjującego | Dostęp do programu GRUB/BCD za pośrednictwem konsoli szeregowej | Linux/Windows 

## <a name="accessing-serial-console-for-linux"></a>Uzyskiwanie dostępu do konsoli szeregowej dla systemu Linux
Aby konsoli szeregowej działała poprawnie system operacyjny gościa, należy określić do odczytywania i zapisywania komunikatów konsoli do portu szeregowego. Większość [dystrybucji systemu Linux zalecanych dla platformy Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) zostały skonfigurowane domyślnie konsoli szeregowej. Wystarczy kliknąć w portalu w sekcji konsoli szeregowej zapewni dostęp do konsoli. 

### <a name="access-for-red-hat"></a>Dostęp do Red Hat 
Red Hat obrazów dostępnych na platformie Azure mają domyślnie dostęp do konsoli. Trybie jednego użytkownika w systemie Red Hat wymaga głównego użytkownika można włączyć, która jest domyślnie wyłączona. Jeśli trzeba włączyć tryb jednego użytkownika, użyj poniższych instrukcji:

1. Zaloguj się w systemie Red Hat za pośrednictwem protokołu SSH
2. Włącz hasło dla użytkownika głównego 
 * `passwd root` (Ustaw hasło główne silne)
3. Upewnij się, że użytkownik root tylko zalogować się za pośrednictwem ttyS0
 * `edit /etc/ssh/sshd_config` i upewnij się, że PermitRootLog w jest ustawiona na nie
 * `edit /etc/securetty file` Aby zezwolić tylko na logowanie za pomocą ttyS0 

Teraz, gdy system jest uruchamiany w trybie jednego użytkownika możesz zalogować się przy użyciu hasła użytkownika root.

Alternatywnie dla RHEL 7.4 + lub 6,9 + można włączyć tryb jednego użytkownika w konfigurację programu GRUB monity, zobacz instrukcje [tutaj](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/5/html/installation_guide/s1-rescuemode-booting-single)

### <a name="access-for-ubuntu"></a>Dostęp do systemu Ubuntu 
Ubuntu obrazów dostępnych na platformie Azure mają domyślnie dostęp do konsoli. Jeśli system jest uruchamiany w trybie jednego użytkownika możesz uzyskać dostęp bez dodatkowych poświadczeń. 

### <a name="access-for-coreos"></a>Dostęp do systemu CoreOS
CoreOS obrazów dostępnych na platformie Azure mają domyślnie dostęp do konsoli. Jeśli konieczne systemu można uruchomić w tryb jednego użytkownika za pośrednictwem zmiana parametrów CHODNIKÓW i dodawanie `coreos.autologin=ttyS0` umożliwiłby core użytkownikowi dziennika i dostępny w konsoli szeregowej. 

### <a name="access-for-suse"></a>Dostęp do SUSE
SLES obrazów dostępnych na platformie Azure mają domyślnie dostęp do konsoli. Jeśli używasz starszej wersji w systemie SLES na platformie Azure, postępuj zgodnie z [artykuł bazy wiedzy](https://www.novell.com/support/kb/doc.php?id=3456486) umożliwiające konsoli szeregowej. Nowsze obrazów z SLES 12 z dodatkiem SP3 + umożliwia także dostęp za pośrednictwem konsoli szeregowej w przypadku, gdy system jest uruchamiany w trybie awaryjnym.

### <a name="access-for-centos"></a>Dostęp do CentOS
CentOS obrazów dostępnych na platformie Azure mają domyślnie dostęp do konsoli. Dla tryb jednego użytkownika należy wykonać instrukcje podobne do obrazów Red Hat powyżej. 

### <a name="access-for-oracle-linux"></a>Dostęp do oprogramowania Oracle w systemie Linux
Obrazy oprogramowania Oracle Linux dostępnych na platformie Azure mają domyślnie dostęp do konsoli. Dla tryb jednego użytkownika należy wykonać instrukcje podobne do obrazów Red Hat powyżej.

### <a name="access-for-custom-linux-image"></a>Dostęp do niestandardowych obrazów systemu Linux
Aby włączyć konsoli szeregowej niestandardowego obrazu maszyny Wirtualnej systemu Linux, należy włączyć dostęp do konsoli w /etc/inittab systemem terminalu ttyS0. Poniżej znajduje się przykład, aby dodać to w pliku inittab 

`S0:12345:respawn:/sbin/agetty -L 115200 console vt102` 

## <a name="errors"></a>Błędy
Większość błędów są przejściowe w rodzaju i ponawianie próby adres połączenia tych. Poniższa tabela zawiera listę błędów i środki zaradcze 

Błąd                            |   Środki zaradcze 
:---------------------------------|:--------------------------------------------|
Nie można pobrać ustawień diagnostyki rozruchu "<VMNAME>". Aby korzystać z konsoli szeregowej, upewnij się, że Diagnostyka rozruchu jest włączona dla tej maszyny Wirtualnej. | Upewnij się, że maszyna wirtualna ma [diagnostykę rozruchu](boot-diagnostics.md) włączone. 
Maszyna wirtualna jest w stanie zatrzymania, przydział zostanie cofnięty. Uruchom maszynę Wirtualną i spróbuj ponownie nawiązać połączenie konsoli szeregowej. | Maszyna wirtualna musi być w stanie uruchomionym uzyskać dostęp do konsoli szeregowej
Nie masz wymaganych uprawnień, aby użyć tej konsoli szeregowej maszyny Wirtualnej. Upewnij się, że co najmniej uprawnienia roli Współautor maszyny Wirtualnej.| Dostęp do konsoli szeregowej wymaga określonych uprawnień dostępu. Zobacz [wymaganiami dotyczącymi dostępu](#prerequisites) Aby uzyskać szczegółowe informacje
Nie można określić grupy zasobów dla konta magazynu diagnostyki rozruchu "<STORAGEACCOUNTNAME>". Sprawdź, czy Diagnostyka rozruchu jest włączona dla tej maszyny Wirtualnej, i czy masz dostęp do tego konta magazynu. | Dostęp do konsoli szeregowej wymaga określonych uprawnień dostępu. Zobacz [wymaganiami dotyczącymi dostępu](#prerequisites) Aby uzyskać szczegółowe informacje

## <a name="known-issues"></a>Znane problemy 
Jak My nadal w fazie (wersja zapoznawcza), aby uzyskać dostęp do konsoli szeregowej, będziemy działają przez niektóre znane problemy, poniżej przedstawiono listę o możliwe obejścia 

Problem                           |   Środki zaradcze 
:---------------------------------|:--------------------------------------------|
Nie ma opcji za pomocą konsoli szeregowej wystąpienia zestawu skalowania maszyn wirtualnych |  W okresie obowiązywania wersji zapoznawczej dostęp do konsoli szeregowej dla wystąpień zestawu skalowania maszyn wirtualnych nie jest obsługiwana.
Naciśnięcie wprowadź po transparent połączenia nie są wyświetlane dziennika w wierszu polecenia | [Wprowadź osiągnięcia, nic nie robi](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md)


## <a name="frequently-asked-questions"></a>Często zadawane pytania 
**PYTANIA I ODPOWIEDZI. Jak wysłać opinię?**

A. Przekazać opinię jako problem, przechodząc do https://aka.ms/serialconsolefeedback. Alternatywnie mniej (preferowane) wysyłanie opinii za pośrednictwem azserialhelp@microsoft.com lub w kategorii maszyny wirtualnej http://feedback.azure.com

**Q.I wyświetlony komunikat o błędzie "istniejąca konsola ma typ systemu operacyjnego powodujące konflikt"Windows"z żądanym typem systemu operacyjnego z systemem Linux?**

A. Jest to znany problem można rozwiązać ten problem, po prostu otwórz [usługi Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) w trybie powłoki bash i spróbuj ponownie.

**PYTANIA I ODPOWIEDZI. Nie mogę uzyskać dostęp do konsoli szeregowej, gdzie plik zgłoszenia do pomocy technicznej?**

A. Tę funkcję wersji zapoznawczej jest objęte za pośrednictwem dodatkowym postanowieniom dotyczącym wersji platformy Azure. Obsługa tej najlepiej odbywa się za pośrednictwem kanałów, o których wspomniano powyżej. 

## <a name="next-steps"></a>Kolejne kroki
* Jest również dostępny dla konsoli szeregowej [Windows](../windows/serial-console.md) maszyn wirtualnych
* Dowiedz się więcej o [bootdiagnostics](boot-diagnostics.md)