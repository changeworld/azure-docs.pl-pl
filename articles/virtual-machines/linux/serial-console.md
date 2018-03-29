---
title: Konsoli szeregowej maszyny wirtualnej platformy Azure | Dokumentacja firmy Microsoft
description: Konsola serial dwukierunkowych maszyn wirtualnych platformy Azure.
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
ms.openlocfilehash: b7d6e48a6f34472bc38947fd70e850b1c3bf6f8a
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="virtual-machine-serial-console-preview"></a>Maszyna wirtualna konsoli szeregowej (wersja zapoznawcza) 


Konsoli szeregowej maszyny wirtualnej na platformie Azure zapewnia dostęp do konsoli tekstowych dla maszyn wirtualnych systemu Linux i Windows. To połączenie szeregowe jest port szeregowy COM1 maszyny wirtualnej i zapewnia dostęp do maszyny wirtualnej i nie są powiązane z siecią maszyny wirtualnej / stan systemu operacyjnego. Dostęp do konsoli szeregowej dla maszyny wirtualnej można uzyskać tylko za pośrednictwem portalu Azure obecnie i dozwolone tylko dla tych użytkowników, którzy mają współautora maszyny Wirtualnej lub powyżej dostęp do maszyny wirtualnej. 

> [!Note] 
> Podglądy są udostępniane użytkownikowi, pod warunkiem że wyrażasz zgodę na warunki użytkowania. Aby uzyskać więcej informacji zobacz [Microsoft Azure uzupełniające warunki użytkowania dotyczącym wersji zapoznawczych platformy Microsoft Azure.] (https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Ta usługa jest obecnie w **publicznej wersji zapoznawczej** i dostęp do konsoli szeregowej dla maszyn wirtualnych jest dostępny dla globalnych regiony platformy Azure. W tym momencie konsoli szeregowej nie jest dostępne chmury Azure dla instytucji rządowych, platformy Azure w Niemczech i Chińskiej wersji platformy Azure.


## <a name="prerequisites"></a>Wymagania wstępne 

* Maszyna wirtualna musi mieć [diagnostyki rozruchu](boot-diagnostics.md) włączone 
* Konto, przy użyciu konsoli szeregowej musi mieć [roli współautora](../../active-directory/role-based-access-built-in-roles.md) dla maszyny Wirtualnej i [diagnostyki rozruchu](boot-diagnostics.md) konta magazynu. 
* Dla ustawienia właściwe dla systemu Linux distro, zobacz [podczas uzyskiwania dostępu do konsoli szeregowej dla systemu Linux](#accessing-serial-console-for-linux)


## <a name="open-the-serial-console"></a>Otwieranie konsoli szeregowej
tylko jest dostępny za pośrednictwem konsoli szeregowej dla maszyn wirtualnych [portalu Azure](https://portal.azure.com). Poniżej przedstawiono kroki, aby uzyskać dostępu do konsoli szeregowej dla maszyn wirtualnych za pomocą portalu 

  1. Otwórz Azure portal
  2. W menu po lewej stronie wybierz maszyny wirtualne.
  3. Kliknij maszynę Wirtualną na liście. Zostanie otwarta strona — omówienie dla maszyny Wirtualnej.
  4. Przewiń w dół do obsługi i rozwiązywania problemów z sekcji, a następnie kliknij opcję konsoli szeregowej (wersja zapoznawcza). Nowe okienko z konsoli szeregowej będą otwierane i uruchomić połączenia.

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-connect.gif)


> [!NOTE] 
> Konsoli szeregowej wymaga użytkownika lokalnego przy użyciu hasła skonfigurowane. W tej chwili tylko skonfigurowane z użyciem klucza publicznego SSH maszyny wirtualne nie mają dostęp do konsoli szeregowej. Aby utworzyć użytkownika lokalnego z hasłem, wykonaj [rozszerzenia dostępu do maszyny Wirtualnej](https://docs.microsoft.com/azure/virtual-machines/linux/using-vmaccess-extension) i utworzenie użytkownika lokalnego z hasłem.

### <a name="disable-feature"></a>Wyłącz funkcję
Wyłączenie ustawienia diagnostyki rozruchu tej maszyny Wirtualnej dla określonej maszyny wirtualne można dezaktywować funkcji konsoli szeregowej.

## <a name="serial-console-security"></a>Zabezpieczenia konsoli szeregowej 

### <a name="access-security"></a>Zabezpieczenia dostępu 
Dostęp do konsoli szeregowej jest ograniczony do użytkowników, którzy mają [współautorzy wirtualna](../../active-directory/role-based-access-built-in-roles.md#virtual-machine-contributor) lub powyżej dostęp do maszyny wirtualnej. Jeśli uwierzytelnianie wieloskładnikowe wymaga dzierżawę usługi AAD, dostęp do konsoli szeregowej będzie również konieczne MFA, zgodnie z jego dostęp odbywa się za pośrednictwem [portalu Azure](https://portal.azure.com).

### <a name="channel-security"></a>Zabezpieczenia kanału
Wszystkie dane są wysyłane Wstecz i określonymi zaszyfrowane w trakcie przesyłania.

### <a name="audit-logs"></a>Dzienniki inspekcji
Dostęp do konsoli szeregowej jest aktualnie zalogowany [diagnostyki rozruchu](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) dzienniki maszyny wirtualnej. Dostęp do tych dzienników są własnością i kontrolowane przez administratora maszyny wirtualnej platformy Azure.  

>[!CAUTION] 
Gdy żadne hasła dostępu do konsoli usługi nie powodują rejestrowania, jeśli polecenia uruchamiane w ramach konsoli zawierają lub output hasła, klucze tajne, nazwy użytkowników lub inne formy z osobiście informacji osobowych, te będą zapisywane diagnostyki rozruchu maszyny wirtualnej rejestruje oraz wszystkich innych tekst widoczne, jako część implementacji funkcji scrollback konsoli szeregowej. Te dzienniki są cykliczne, a tylko osoby, które mają uprawnienia do odczytu do konta magazynu diagnostyki mają do nich dostępu, jednak zaleca się następujące najlepsze rozwiązanie polegające na przy użyciu konsoli SSH dla wszystkich elementów, które może się wiązać kluczy tajnych i/lub dane osobowe. 

### <a name="concurrent-usage"></a>Współbieżne użycie
Jeśli użytkownik jest połączony z konsolą szeregową i inny użytkownik pomyślnie żąda dostępu do tej samej maszyny wirtualnej, pierwszy użytkownik zostanie odłączony, a drugi użytkownik połączonych w sposób podobnie pierwszy użytkownik, stały i opuszczania konsoli fizycznej i nowy Użytkownik obecne w dół.

>[!CAUTION] 
Oznacza to, że użytkownik, który zostanie rozłączona nie będą rejestrowane! Możliwość wymuszenia wylogowania po rozłączeniu (za pośrednictwem SIGHUP lub podobny mechanizm) jest nadal mapy drogowej. W systemie Windows jest automatyczne limitu czasu w jej włączone jednak dla systemu Linux można skonfigurować ustawienia terminali limitu czasu. Aby zrobić to po prostu Dodaj `export TMOUT=600` w .bash_profile lub profil użytkownika logowania w konsoli, aby limit czasu sesji po 10 minutach.

### <a name="disable-feature"></a>Wyłącz funkcję
Wyłączenie ustawienia diagnostyki rozruchu tej maszyny Wirtualnej dla określonej maszyny wirtualne można dezaktywować funkcji konsoli szeregowej.

## <a name="common-scenarios-for-accessing-serial-console"></a>Typowe scenariusze dotyczące uzyskiwania dostępu do konsoli szeregowej 
Scenariusz          | Akcje w konsoli szeregowej                |  Stosowanie systemu operacyjnego 
:------------------|:-----------------------------------------|:------------------
Uszkodzony plik FSTAB | Wprowadź klucz, aby kontynuować, a następnie napraw plik fstab za pomocą edytora tekstu. Zobacz [jak rozwiązać problemy z fstab](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors) | Linux 
Reguły zapory niepoprawne | Dostęp do konsoli szeregowej i napraw iptables lub reguły zapory systemu Windows | Linux/Windows 
System plików uszkodzenie/wyboru | Dostęp do konsoli szeregowej i odzyskiwanie systemu plików | Linux/Windows 
Problemy z konfiguracją protokołu RDP/SSH | Dostęp do konsoli szeregowej i zmienić ustawienia | Linux/Windows 
Blokowanie sieci systemu| Dostęp do konsoli szeregowej za pośrednictwem portalu do zarządzania systemem | Linux/Windows 
Interakcja z programu inicjującego | Dostęp CHODNIKÓW/BCD za pośrednictwem konsoli szeregowej | Linux/Windows 

## <a name="accessing-serial-console-for-linux"></a>Uzyskiwanie dostępu do konsoli szeregowej dla systemu Linux
Aby konsoli szeregowej działał poprawnie systemu operacyjnego gościa musi być skonfigurowana do do odczytu i zapisu wiadomości konsoli portu szeregowego. Większość [zatwierdzone dystrybucje systemu Linux Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) mają konsoli szeregowej konfiguracja domyślna. Klikając w portalu w sekcji konsoli szeregowej zapewni dostęp do konsoli. 

### <a name="access-for-redhat"></a>Dostęp do RedHat 
RedHat obrazów dostępnych na platformie Azure mają dostęp konsoli domyślnie włączone. Tryb pojedynczego użytkownika w Red Hat wymaga użytkownika głównego włączenia tej funkcji, które jest domyślnie wyłączone. Jeśli trzeba włączyć tryb pojedynczego użytkownika, użyj poniższych instrukcji:

1. Zaloguj się do systemu Red Hat za pośrednictwem SSH
2. Włącz hasła dla użytkownika głównego 
 * `passwd root` (Ustaw hasło główne silne)
3. Upewnij się, że użytkownik root tylko zalogować się za pośrednictwem ttyS0
 * `edit /etc/ssh/sshd_config` i upewnij się, że PermitRootLog w jest ustawiona na nie
 * `edit /etc/securetty file` Aby zezwolić tylko na logowanie za pomocą ttyS0 

Teraz, jeśli system jest uruchamiany w trybie jednego użytkownika możesz zalogować się przy użyciu hasła użytkownika root.

Można również uzyskać RHEL 7.4 + lub 6,9 + można włączyć tryb pojedynczego użytkownika w CHODNIKÓW monity, zobacz instrukcje [tutaj](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/5/html/installation_guide/s1-rescuemode-booting-single)

### <a name="access-for-ubuntu"></a>Dostęp dla Ubuntu 
Ubuntu obrazów dostępnych na platformie Azure mają dostęp konsoli domyślnie włączone. Jeśli system jest uruchamiany w trybie jednego użytkownika możesz uzyskać dostęp bez dodatkowych poświadczeń. 

### <a name="access-for-coreos"></a>Dostęp dla CoreOS
CoreOS obrazów dostępnych na platformie Azure mają dostęp konsoli domyślnie włączone. Jeśli konieczne systemu można uruchomić w tryb jednego użytkownika za pośrednictwem zmiana parametrów CHODNIKÓW i dodawanie `coreos.autologin=ttyS0` umożliwia użytkownikowi core dziennika i dostępny w konsoli szeregowej. 

### <a name="access-for-suse"></a>Dostęp do SUSE
SLES obrazów dostępnych na platformie Azure mają dostęp konsoli domyślnie włączone. Jeśli używasz starszej wersji SLES na platformie Azure, wykonaj [artykułu KB](https://www.novell.com/support/kb/doc.php?id=3456486) umożliwiające konsoli szeregowej. Nowsze obrazów systemu SLES 12 SP3 + umożliwia również dostęp za pośrednictwem konsoli szeregowej w przypadku, gdy system jest uruchamiany w trybie awaryjnym.

### <a name="access-for-centos"></a>Dostęp dla CentOS
CentOS obrazów dostępnych na platformie Azure mają dostęp konsoli domyślnie włączone. Dla tryb jednego użytkownika należy wykonać instrukcje podobne do Red Hat obrazów powyżej. 

### <a name="access-for-oracle-linux"></a>Dostęp dla Oracle Linux
Oracle Linux obrazów dostępnych na platformie Azure mają dostęp konsoli domyślnie włączone. Dla tryb jednego użytkownika należy wykonać instrukcje podobne do Red Hat obrazów powyżej.

### <a name="access-for-custom-linux-image"></a>Dostęp dla niestandardowego obrazu systemu Linux
Aby włączyć konsoli szeregowej dla niestandardowego obrazu maszyny Wirtualnej systemu Linux, Włącz dostęp do konsoli w /etc/inittab do uruchomienia terminal na ttyS0. Poniżej przedstawiono przykład, aby dodać to w pliku inittab 

`S0:12345:respawn:/sbin/agetty -L 115200 console vt102` 

## <a name="errors"></a>Błędy
Przejściowego charakter i ponowienie próby adresu połączenia te są większość błędów. Poniższej tabeli przedstawiono listę błędów i środki zaradcze 

Błąd                            |   Środki zaradcze 
:---------------------------------|:--------------------------------------------|
Nie można pobrać ustawień diagnostyki rozruchu dla "<VMNAME>". Aby używać konsoli szeregowej, upewnij się, że diagnostyki rozruchu jest włączone dla tej maszyny Wirtualnej. | Upewnij się, że maszyna wirtualna ma [diagnostyki rozruchu](boot-diagnostics.md) włączone. 
Maszyna wirtualna jest w stanie zatrzymania, deallocated. Uruchom maszynę Wirtualną i ponów próbę połączenia konsoli szeregowej. | Maszyna wirtualna musi być w stanie uruchomionym, dostęp do konsoli szeregowej
Nie masz wymaganych uprawnień, aby używać konsoli szeregowej tej maszyny Wirtualnej. Upewnij się, musisz mieć co najmniej uprawnienia roli współautora maszyny Wirtualnej.| Dostęp do konsoli szeregowej wymaga określonych uprawnień dostępu. Zobacz [wymagania związane z dostępem](#prerequisites) Aby uzyskać więcej informacji
Nie można określić grupy zasobów dla konta magazynu diagnostyki rozruchu "<STORAGEACCOUNTNAME>". Sprawdź, czy włączono diagnostyki rozruchu dla tej maszyny Wirtualnej i czy masz dostęp do tego konta magazynu. | Dostęp do konsoli szeregowej wymaga określonych uprawnień dostępu. Zobacz [wymagania związane z dostępem](#prerequisites) Aby uzyskać więcej informacji

## <a name="known-issues"></a>Znane problemy 
Firma Microsoft są nadal w fazie Podgląd dostępu do konsoli szeregowej, firma Microsoft pracy za pośrednictwem znane problemy, poniżej znajdują się na liście z możliwe obejścia 

Problem                           |   Środki zaradcze 
:---------------------------------|:--------------------------------------------|
Nie jest dostępna opcja z konsoli szeregowej wystąpienia zestawu skali maszyny wirtualnej |  W czasie w wersji zapoznawczej dostęp do konsoli szeregowej dla wystąpień zestawu skali maszyny wirtualnej nie jest obsługiwane.
Naciśnięcie klawisza enter po transparent połączenia nie są wyświetlane dziennika w wierszu | [Naciśnięcie wprowadź nie działa](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md)


## <a name="frequently-asked-questions"></a>Często zadawane pytania 
**Q. Jak można wysłać opinię?**

A. Wyrazić swoją opinię jako rozwiązaniem problemu, przechodząc do https://aka.ms/serialconsolefeedback. Możesz też mniej (preferowane) Prześlij opinię za pośrednictwem azserialhelp@microsoft.com lub w kategorii maszyny wirtualnej http://feedback.azure.com

**Q.I wystąpi błąd "powodujące konflikt typu systemu operacyjnego"Windows"żądanego typu systemu operacyjnego Linux ma istniejące konsoli?**

A. Jest to znany problem można rozwiązać ten problem, po prostu otworzyć [powłoki chmury Azure](https://docs.microsoft.com/azure/cloud-shell/overview) w trybie bash i ponów próbę.

**Q. Nie mogę uzyskać dostępu do konsoli szeregowej, w którym plik sprawy pomocy technicznej?**

A. Ta funkcja podglądu jest objęte za pośrednictwem warunki dotyczące usługi Azure. Obsługa tej powinni obsłużyć kanałami wymienionych powyżej. 

## <a name="next-steps"></a>Kolejne kroki
* Jest również dostępny do konsoli szeregowej [Windows](../windows/serial-console.md) maszyny wirtualne
* Dowiedz się więcej o [bootdiagnostics](boot-diagnostics.md)