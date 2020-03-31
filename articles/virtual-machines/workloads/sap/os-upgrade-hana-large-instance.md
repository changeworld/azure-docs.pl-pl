---
title: Uaktualnienie systemu operacyjnego dla sap HANA na platformie Azure (duże wystąpienia)| Dokumenty firmy Microsoft
description: Wykonywanie uaktualnienia systemu operacyjnego dla sap HANA na platformie Azure (duże wystąpienia)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/04/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3a0a5d39a7cb2162186291ea534a623ef45c40d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78675630"
---
# <a name="operating-system-upgrade"></a>Uaktualnienie systemu operacyjnego
W tym dokumencie opisano szczegółowe informacje na temat uaktualnień systemu operacyjnego w dużych wystąpieniach HANA.

>[!NOTE]
>Uaktualnienie systemu operacyjnego jest obowiązkiem klienta, pomoc techniczna firmy Microsoft może poprowadzić Cię do kluczowych obszarów, na które należy zwrócić uwagę podczas uaktualniania. Przed zaplanowaniem uaktualnienia należy skonsultować się z dostawcą systemu operacyjnego.

Podczas inicjowania obsługi administracyjnej jednostek HLI zespół operacyjny firmy Microsoft instaluje system operacyjny.
W miarę czasu wymagane jest utrzymanie systemu operacyjnego (przykład: Łatanie, strojenie, uaktualnianie itp.) na urządzeniu HLI.

Przed wprowadzeniem istotnych zmian w systemie operacyjnym (na przykład uaktualnienie dodatku SP1 do dodatku SP2) należy skontaktować się z zespołem microsoft operations, otwierając bilet pomocy technicznej, aby skonsultować się z nim.

Podaj w swoim bilecie:

* Identyfikator subskrypcji HLI.
* Nazwa serwera.
* Poziom poprawki, który planujesz zastosować.
* Data, w której planujesz tę zmianę. 

Zaleca się otwarcie tego biletu co najmniej tydzień przed pożądaną datą uaktualnienia ze względu na sprawdzenie przez zespół operacji, czy uaktualnienie oprogramowania układowego będzie konieczne w bloku serwera.


Aby uzyskać macierz obsługi różnych wersji SAP HANA z różnymi wersjami systemu Linux, zobacz [SAP Note #2235581](https://launchpad.support.sap.com/#/notes/2235581).


## <a name="known-issues"></a>Znane problemy

Poniżej przedstawiono kilka często znanych problemów podczas uaktualniania:
- W jednostce SKU klasy II oprogramowanie podstawy oprogramowania (SFS) jest usuwane po uaktualnieniu systemu operacyjnego. Po uaktualnieniu systemu operacyjnego należy ponownie zainstalować zgodny system SFS.
- Sterowniki kart Ethernet (ENIC i FNIC) cofnęły się do starszej wersji. Po uaktualnieniu należy ponownie zainstalować zgodną wersję sterowników.

## <a name="sap-hana-large-instance-type-i-recommended-configuration"></a>Zalecana konfiguracja wystąpienia dużego sap HANA (typ I)

Konfiguracja systemu operacyjnego może z czasem odejść od zalecanych ustawień z powodu poprawek, uaktualnień systemu i zmian wprowadzonych przez klientów. Ponadto firma Microsoft identyfikuje aktualizacje potrzebne dla istniejących systemów, aby upewnić się, że są one optymalnie skonfigurowane dla najlepszej wydajności i odporności. Poniższe instrukcje opis zaleceń, które odnoszą się do wydajności sieci, stabilności systemu i optymalnej wydajności HANA.

### <a name="compatible-enicfnic-driver-versions"></a>Kompatybilne wersje sterowników eNIC/fNIC
  W celu zapewnienia właściwej wydajności sieci i stabilności systemu zaleca się zapewnienie zainstalowania odpowiedniej wersji sterowników eNIC i fNIC specyficznych dla systemu operacyjnego, jak pokazano w poniższej tabeli zgodności. Serwery są dostarczane do klientów ze zgodnymi wersjami. Należy zauważyć, że w niektórych przypadkach podczas instalowania poprawek systemu operacyjnego/jądra sterowniki mogą zostać wycofane do domyślnych wersji sterowników. Upewnij się, że odpowiednia wersja sterownika jest uruchomiona po operacji poprawek systemu operacyjnego/jądra.
       
      
  |  Dostawca systemu operacyjnego    |  Wersja pakietu systemu operacyjnego     |  Wersja oprogramowania układowego  |  Sterownik eNIC |  Sterownik fNIC | 
  |---------------|-------------------------|--------------------|--------------|--------------|
  |   Suse        |  SLES 12 SP2            |   3.1.3h           |  2.3.0.40    |   1.6.0.34   |
  |   Suse        |  SLES 12 SP3            |   3.1.3h           |  2.3.0.44    |   1.6.0.36   |
  |   Red Hat     |  RHEL 7.2               |   3.1.3h           |  2.3.0.39    |   1.6.0.34   |
 

### <a name="commands-for-driver-upgrade-and-to-clean-old-rpm-packages"></a>Polecenia dotyczące uaktualniania sterowników i czyszczenia starych pakietów rpm
```
rpm -U driverpackage.rpm
rpm -e olddriverpackage.rpm
```

#### <a name="commands-to-confirm"></a>Polecenia potwierdzające
```
modinfo enic
modinfo fnic
```

### <a name="suse-hlis-grub-update-failure"></a>SuSE HLIs AWARIA AKTUALIZACJI GRUB
Sap na platformie Azure HANA dużych wystąpień (typu I) może być w stanie nie rozruchu po uaktualnieniu. Poniższa procedura rozwiązuje ten problem.
#### <a name="execution-steps"></a>Kroki wykonania


*   Wykonaj `multipath -ll` polecenie.
*   Pobierz identyfikator jednostki LUN, którego rozmiar wynosi około 50G, lub użyj polecenia:`fdisk -l | grep mapper`
*   Aktualizuj `/etc/default/grub_installdevice` plik `/dev/mapper/<LUN ID>`za pomocą wiersza . Przykład: /dev/mapper/3600a09803830372f483f495242534a56
>[!NOTE]
>Identyfikator jednostki LUN różni się w zależności od serwera.


### <a name="disable-edac"></a>Wyłącz EDAC 
   Moduł wykrywania i korekcji błędów (EDAC) pomaga w wykrywaniu i korygowaniu błędów pamięci. Jednak podstawowy sprzęt dla sap HANA na azure dużych wystąpień (typ I) jest już wykonywania tej samej funkcji. Włączenie tej samej funkcji na poziomie sprzętu i systemu operacyjnego (OS) może powodować konflikty i może prowadzić do sporadycznego, nieplanowanego zamknięcia serwera. W związku z tym zaleca się wyłączenie modułu z systemu operacyjnego.

#### <a name="execution-steps"></a>Kroki wykonania

* Sprawdź, czy moduł EDAC jest włączony. Jeśli dane wyjściowe są zwracane w poniżej polecenia, oznacza to, że moduł jest włączony. 
```
lsmod | grep -i edac 
```
* Wyłącz moduły, dołączając do pliku następujące wiersze:`/etc/modprobe.d/blacklist.conf`
```
blacklist sb_edac
blacklist edac_core
```
Do wprowadzenia zmian wymagany jest ponowny rozruch. Wykonaj `lsmod` polecenie i sprawdź, czy moduł nie jest obecny w danych wyjściowych.


### <a name="kernel-parameters"></a>Parametry jądra
   Upewnij się, że `transparent_hugepage` `numa_balancing`jest `processor.max_cstate` `ignore_ce` to `intel_idle.max_cstate` poprawne ustawienie dla , , i są stosowane.

* intel_idle.max_cstate=1
* procesor.max_cstate=1
* transparent_hugepage=nigdy
* numa_balancing=wyłącz
* mce=ignore_ce


#### <a name="execution-steps"></a>Kroki wykonania

* Dodawanie tych parametrów do `GRB_CMDLINE_LINUX` wiersza w pliku`/etc/default/grub`
```
intel_idle.max_cstate=1 processor.max_cstate=1 transparent_hugepage=never numa_balancing=disable mce=ignore_ce
```
* Utwórz nowy plik grub.
```
grub2-mkconfig -o /boot/grub2/grub.cfg
```
* Uruchom ponownie system.


## <a name="next-steps"></a>Następne kroki
- Zapoznaj się [z poleceniem tworzenia kopii zapasowych i przywracania](hana-overview-high-availability-disaster-recovery.md) dla klasy jednostki SKU kopii zapasowej systemu operacyjnego typu I.
- Zapoznaj się [z kopią zapasową systemu operacyjnego dla jednostek SKU typu II sygnatury wersji 3](os-backup-type-ii-skus.md) dla klasy jednostek SKU typu II.
