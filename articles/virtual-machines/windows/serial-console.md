---
title: Konsoli szeregowej maszyny wirtualnej platformy Azure | Dokumentacja firmy Microsoft
description: Konsola serial dwukierunkowych maszyn wirtualnych platformy Azure.
services: virtual-machines-windows
documentationcenter: ''
author: harijay
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/05/2018
ms.author: harijay
ms.openlocfilehash: 9c16114b4f8d335dc750b1beef1fb6204ae20e0f
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

## <a name="open-the-serial-console"></a>Otwieranie konsoli szeregowej
tylko jest dostępny za pośrednictwem konsoli szeregowej dla maszyn wirtualnych [portalu Azure](https://portal.azure.com). Poniżej przedstawiono kroki, aby uzyskać dostępu do konsoli szeregowej dla maszyn wirtualnych za pomocą portalu 

  1. Otwórz Azure portal
  2. W menu po lewej stronie wybierz maszyny wirtualne.
  3. Kliknij maszynę Wirtualną na liście. Zostanie otwarta strona — omówienie dla maszyny Wirtualnej.
  4. Przewiń w dół do obsługi i rozwiązywania problemów z sekcji, a następnie kliknij opcję konsoli szeregowej (wersja zapoznawcza). Nowe okienko z konsoli szeregowej będą otwierane i uruchomić połączenia.

![](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

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
Gdy żadne hasła dostępu do konsoli usługi nie powodują rejestrowania, jeśli polecenia uruchamiane w ramach konsoli zawierają lub output hasła, klucze tajne, nazwy użytkowników lub inne formy z osobiście informacji osobowych, te będą zapisywane diagnostyki rozruchu maszyny wirtualnej rejestruje oraz wszystkich innych tekst widoczne, jako część implementacji funkcji scrollback konsoli szeregowej. Te dzienniki są cykliczne, a tylko osoby, które mają uprawnienia do odczytu do konta magazynu diagnostyki mają do nich dostępu, jednak zaleca się następujące najlepsze rozwiązanie polegające na przy użyciu pulpitu zdalnego dla wszystkich elementów, które może się wiązać kluczy tajnych i/lub dane osobowe. 

### <a name="concurrent-usage"></a>Współbieżne użycie
Jeśli użytkownik jest połączony z konsolą szeregową i inny użytkownik pomyślnie żąda dostępu do tej samej maszyny wirtualnej, pierwszy użytkownik zostanie odłączony, a drugi użytkownik połączonych w sposób podobnie pierwszy użytkownik, stały i opuszczania konsoli fizycznej i nowy Użytkownik obecne w dół.

>[!CAUTION] 
Oznacza to, że użytkownik, który zostanie rozłączona nie będą rejestrowane! Możliwość wymuszenia wylogowania po rozłączeniu (za pośrednictwem SIGHUP lub podobny mechanizm) jest nadal mapy drogowej. W systemie Windows jest automatyczne limitu czasu w jej włączone jednak dla systemu Linux można skonfigurować ustawienia terminali limitu czasu. 


## <a name="accessing-serial-console-for-windows"></a>Uzyskiwanie dostępu do konsoli szeregowej dla systemu Windows 
Nowsze obrazów systemu Windows Server na platformie Azure mają [specjalnej konsoli administracyjnej](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) domyślnie włączone (jej). JEJ jest obsługiwany w wersjach systemu Windows server, ale nie jest dostępne w wersjach klientów (np. Windows 10, Windows 8 lub Windows 7). Aby włączyć konsoli szeregowej utworzone za pomocą przy użyciu Feb2018 lub niższym obrazów maszyn wirtualnych systemu Windows, zapoznaj się z następujących czynności: 

1. Połącz się z maszyną wirtualną z systemem Windows za pośrednictwem pulpitu zdalnego
2. Z wiersza polecenia z uprawnieniami administracyjnymi, uruchom następujące polecenia 
* `bcdedit /ems {current} on`
* `bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200`
3. Wykonaj ponowny rozruch systemu dla konsoli jej włączenia

![](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

Jeśli jej uruchomienie można włączyć w trybie offline, również 

1. Dołączenie dysku systemu windows ma jej skonfigurowany dla jako dysku danych do istniejącej maszyny Wirtualnej. 
2. Z wiersza polecenia z uprawnieniami administracyjnymi, uruchom następujące polecenia 
* `bcdedit /store <mountedvolume>\boot\bcd /ems {default} on`
* `bcdedit /store <mountedvolume>\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

### <a name="how-do-i-know-if-sac-is-enabled-or-not"></a>Jak sprawdzić, czy jej jest włączony, czy nie 

Jeśli [jej] (https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) nie włączono konsoli szeregowej nie będzie wyświetlany monit o jej. Go może wyświetlać informacje o kondycji maszyny Wirtualnej w niektórych przypadkach lub może być pusta.  

### <a name="enabling-boot-menu-to-show-in-the-serial-console"></a>Włączanie menu rozruchu do wyświetlenia w konsoli szeregowej 

Jeśli musisz włączyć moduł ładujący rozruchu systemu Windows monituje o do wyświetlenia w konsoli szeregowej, możesz dodać następujące dodatkowe opcje, aby moduł ładujący rozruchu systemu Windows.

1. Połącz się z maszyną wirtualną z systemem Windows za pośrednictwem pulpitu zdalnego
2. Z wiersza polecenia z uprawnieniami administracyjnymi, uruchom następujące polecenia 
* `bcdedit /set {bootmgr} displaybootmenu yes`
* `bcdedit /set {bootmgr} timeout 5`
* `bcdedit /set {bootmgr} bootems yes`
3. Wykonaj ponowny rozruch systemu do włączenia menu rozruchu

> [!NOTE] 
> W tym punktu obsługę funkcji kluczy nie jest włączona, jeśli potrzebujesz zaawansowanych opcji rozruchu w systemie bcdedit/set {current} onetimeadvancedoptions, zobacz [bcdedit](https://docs.microsoft.com/windows-hardware/drivers/devtest/bcdedit--set) więcej szczegółów

## <a name="common-scenarios-for-accessing-windows-serial-console"></a>Typowe scenariusze dotyczące uzyskiwania dostępu do konsoli szeregowej systemu Windows 
Scenariusz          | Akcje w konsoli szeregowej                
:------------------|:----------------------------------------
Reguły zapory niepoprawne | Dostęp do konsoli szeregowej i napraw iptables lub reguły zapory systemu Windows 
System plików uszkodzenie/wyboru | Dostęp do konsoli szeregowej i odzyskiwanie po zalogowaniu się do jej CMD systemu plików
Problemy z konfiguracją protokołu RDP | Dostęp do konsoli szeregowej i zaloguj się do cmd kanału. Sprawdź kondycję usług terminalowych i uruchom ponownie w razie potrzeby.
Blokowanie sieci systemu| Dostęp do konsoli szeregowej i zaloguj się do cmd kanału. Sprawdź stan zapory przez [netsh](https://docs.microsoft.com/windows-server/networking/technologies/netsh/netsh-contexts) wiersza polecenia. 

## <a name="errors"></a>Błędy
Przejściowego charakter i ponowienie próby adresu połączenia te są większość błędów. Poniższej tabeli przedstawiono listę błędów i środki zaradcze 

Błąd                            |   Środki zaradcze 
:---------------------------------|:--------------------------------------------|
Nie można pobrać ustawień diagnostyki rozruchu dla "<VMNAME>". Aby używać konsoli szeregowej, upewnij się, że diagnostyki rozruchu jest włączone dla tej maszyny Wirtualnej. | Upewnij się, że maszyna wirtualna ma [diagnostyki rozruchu](boot-diagnostics.md) włączone. 
Maszyna wirtualna jest w stanie zatrzymania, deallocated. Uruchom maszynę Wirtualną i ponów próbę połączenia konsoli szeregowej. | Maszyna wirtualna musi być w stanie uruchomionym, dostęp do konsoli szeregowej
Nie masz uprawnień wymaganych do używania konsoli szeregowej maszyny Wirtualnej. Upewnij się, musisz mieć co najmniej uprawnienia roli współautora maszyny Wirtualnej.| Dostęp do konsoli szeregowej wymaga określonych uprawnień dostępu. Zobacz [wymagania związane z dostępem](#prerequisites) Aby uzyskać więcej informacji
Nie można określić grupy zasobów dla konta magazynu diagnostyki rozruchu "<STORAGEACCOUNTNAME>". Sprawdź, czy włączono diagnostyki rozruchu dla tej maszyny Wirtualnej i czy masz dostęp do tego konta magazynu. | Dostęp do konsoli szeregowej wymaga określonych uprawnień dostępu. Zobacz [wymagania związane z dostępem](#prerequisites) Aby uzyskać więcej informacji

## <a name="known-issues"></a>Znane problemy 
Firma Microsoft są nadal w fazie Podgląd dostępu do konsoli szeregowej, firma Microsoft pracy za pośrednictwem znane problemy, poniżej znajdują się na liście z możliwe obejścia 

Problem                           |   Środki zaradcze 
:---------------------------------|:--------------------------------------------|
Nie jest dostępna opcja z konsoli szeregowej wystąpienia zestawu skali maszyny wirtualnej | W czasie w wersji zapoznawczej dostęp do konsoli szeregowej dla wystąpień zestawu skali maszyny wirtualnej nie jest obsługiwane.
Naciśnięcie klawisza enter po transparent połączenia nie są wyświetlane dziennika w wierszu | [Naciśnięcie wprowadź nie działa](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md)
Tylko informacje o kondycji jest wyświetlany podczas nawiązywania połączenia z maszyną wirtualną systemu Windows| [Sygnały kondycji systemu Windows](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Windows_Health_Info.md)

## <a name="frequently-asked-questions"></a>Często zadawane pytania 
**Q. Jak można wysłać opinię?**

A. Wyrazić swoją opinię jako rozwiązaniem problemu, przechodząc do https://aka.ms/serialconsolefeedback. Możesz też mniej (preferowane) Prześlij opinię za pośrednictwem azserialhelp@microsoft.com lub w kategorii maszyny wirtualnej http://feedback.azure.com 

**Q. Otrzymuję komunikat o błędzie "powodujące konflikt typu systemu operacyjnego"Windows"żądanego typu systemu operacyjnego Linux ma istniejące konsoli?**

A. Jest to znany problem można rozwiązać ten problem, po prostu otworzyć [powłoki chmury Azure](https://docs.microsoft.com/azure/cloud-shell/overview) w trybie bash i ponów próbę.

**Q. Nie mogę uzyskać dostępu do konsoli szeregowej, w którym plik sprawy pomocy technicznej?**

A. Ta funkcja podglądu jest objęte za pośrednictwem warunki dotyczące usługi Azure. Obsługa tej powinni obsłużyć kanałami wymienionych powyżej. 

## <a name="next-steps"></a>Kolejne kroki
* Jest również dostępny do konsoli szeregowej [Linux](../linux/serial-console.md) maszyny wirtualne
* Dowiedz się więcej o [bootdiagnostics](boot-diagnostics.md)
