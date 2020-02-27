---
title: Uaktualnienie systemu operacyjnego dla SAP HANA na platformie Azure (duże wystąpienia) | Microsoft Docs
description: Wykonaj uaktualnienie systemu operacyjnego SAP HANA na platformie Azure (duże wystąpienia)
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
ms.openlocfilehash: 83b86b9bc5c09cc9f97a320e3019ddea68434309
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/26/2020
ms.locfileid: "77616457"
---
# <a name="operating-system-upgrade"></a>Uaktualnienie systemu operacyjnego
W tym dokumencie opisano szczegółowe informacje dotyczące uaktualnień systemu operacyjnego w dużych wystąpieniach platformy HANA.

>[!NOTE]
>Uaktualnienie systemu operacyjnego jest odpowiedzialne za klienta, pomoc techniczna firmy Microsoft może poprowadzić Cię do kluczowych obszarów, które należy obejrzeć podczas uaktualniania. Przed zaplanowaniem uaktualnienia należy skonsultować się z dostawcą systemu operacyjnego.

Podczas inicjowania obsługi administracyjnej jednostki w usłudze HLI zespół operacyjny firmy Microsoft instaluje ten system.
W danym czasie wymagane jest zachowanie systemu operacyjnego (przykład: Poprawka, dostrajanie, uaktualnianie itp.) w jednostce.

Przed wprowadzeniem istotnych zmian w systemie operacyjnym (na przykład uaktualnienia programu SP1 do wersji SP2) należy skontaktować się z zespołem ds. operacyjnych, otwierając bilet pomocy technicznej w celu konsultacji.

Dołącz do biletu:

* Identyfikator subskrypcji pakietu.
* Nazwa serwera.
* Poziom poprawki, który ma zostać zastosowany.
* Data, w której planowana jest ta zmiana. 

Zalecamy otworzenie tego biletu z co najmniej jednego tygodnia przed pożądanym terminem uaktualnienia, z powodu sprawdzania zespołu operacji, jeśli w bloku serwera będzie wymagane uaktualnienie oprogramowania układowego.


Aby uzyskać macierz pomocy technicznej dla różnych wersji SAP HANA z różnymi wersjami systemu Linux, zobacz [uwagi dotyczące oprogramowania SAP #2235581](https://launchpad.support.sap.com/#/notes/2235581).


## <a name="known-issues"></a>Znane problemy

Poniżej przedstawiono kilka typowych znanych problemów podczas uaktualniania:
- W jednostkach SKU klasy SKU typu II, oprogramowanie oprogramowania Software Foundation (SFS) zostanie usunięte po uaktualnieniu systemu operacyjnego. Po uaktualnieniu systemu operacyjnego należy ponownie zainstalować zgodną SFS.
- Sterowniki kart Ethernet (ENIC i FNIC) zostały przywrócone do starszej wersji. Po uaktualnieniu należy ponownie zainstalować zgodną wersję sterowników.

## <a name="sap-hana-large-instance-type-i-recommended-configuration"></a>SAP HANA zalecana konfiguracja: duże wystąpienie (typ I)

Konfiguracja systemu operacyjnego może przekroczyć zalecane ustawienia w miarę upływu czasu w związku z poprawkami, uaktualnieniami systemu i zmianami wprowadzonymi przez klientów. Ponadto firma Microsoft identyfikuje aktualizacje, które są konieczne dla istniejących systemów, aby upewnić się, że są one optymalnie skonfigurowane pod kątem najlepszej wydajności i odporności. Poniższe instrukcje przedstawiają zalecenia dotyczące wydajności sieci, stabilności systemu i optymalnej wydajności platformy HANA.

### <a name="compatible-enicfnic-driver-versions"></a>Zgodne wersje sterowników eNIC/fNIC
  W celu zapewnienia prawidłowej wydajności sieci i stabilności systemu zaleca się zagwarantowanie, że dla systemu operacyjnego odpowiednie wersje sterowników eNIC i fNIC są zainstalowane zgodnie z poniższą tabelą zgodności. Serwery są dostarczane do klientów ze zgodnymi wersjami. Należy pamiętać, że w niektórych przypadkach podczas stosowania poprawek jądra systemu operacyjnego sterowniki mogą zostać przywrócone do domyślnych wersji sterowników. Upewnij się, że w odpowiedniej wersji sterownika działają operacje publikowania poprawek dla systemu operacyjnego/jądra.
       
      
  |  Dostawca systemu operacyjnego    |  Wersja pakietu systemu operacyjnego     |  Sterownik eNIC  |  Sterownik fNIC |
  |---------------|-------------------------|---------------|--------------|
  |   Szło        |  SLES 12 SP2            |   2.3.0.40    |   1.6.0.34   |
  |   Szło        |  SLES 12 Z DODATKIEM SP3            |   2.3.0.44    |   1.6.0.36   |
  |   Red Hat     |  RHEL 7,2               |   2.3.0.39    |   1.6.0.34   |
 

### <a name="commands-for-driver-upgrade-and-to-clean-old-rpm-packages"></a>Polecenia uaktualniania sterowników i czyszczenia starych pakietów rpm
```
rpm -U driverpackage.rpm
rpm -e olddriverpackage.rpm
```

#### <a name="commands-to-confirm"></a>Polecenia do potwierdzenia
```
modinfo enic
modinfo fnic
```

### <a name="suse-hlis-grub-update-failure"></a>Niepowodzenie aktualizacji SuSE HLIs GRUB
Usługa SAP w dużych wystąpieniach platformy Azure Hana (typ I) może być w stanie rozruchowym po uaktualnieniu. Poniższa procedura rozwiązuje ten problem.
#### <a name="execution-steps"></a>Kroki wykonywania


*   Wykonaj `multipath -ll` polecenie.
*   Pobierz identyfikator LUN o rozmiarze około 50G lub użyj polecenia: `fdisk -l | grep mapper`
*   Zaktualizuj plik `/etc/default/grub_installdevice` przy użyciu `/dev/mapper/<LUN ID>`wiersza. Przykład:/dev/mapper/3600a09803830372f483f495242534a56
>[!NOTE]
>Identyfikator LUN różni się od serwera do serwera.


### <a name="disable-edac"></a>Wyłącz EDAC 
   Moduł wykrywanie błędów i Korekcja (EDAC) pomaga w wykrywaniu i poprawianiu błędów pamięci. Jednak podstawowy sprzęt dla Oprogramowanie SAP HANA na platformie Azure — duże wystąpienia (typ I) już wykonuje tę samą funkcję. Ta sama funkcja włączona na poziomach sprzętu i systemu operacyjnego może powodować konflikty i może prowadzić do okazjonalnych, nieplanowanych zamknięć serwera. W związku z tym zalecane jest wyłączenie modułu z systemu operacyjnego.

#### <a name="execution-steps"></a>Kroki wykonywania

* Sprawdź, czy moduł EDAC jest włączony. Jeśli dane wyjściowe są zwracane w poniższym poleceniu, oznacza to, że moduł jest włączony. 
```
lsmod | grep -i edac 
```
* Wyłącz moduły, dołączając następujące wiersze do pliku `/etc/modprobe.d/blacklist.conf`
```
blacklist sb_edac
blacklist edac_core
```
Aby zmiany zostały wprowadzone, wymagany jest ponowny rozruch. Wykonaj polecenie `lsmod` i sprawdź, czy moduł nie jest obecny w danych wyjściowych.


### <a name="kernel-parameters"></a>Parametry jądra
   Upewnij się, że są stosowane poprawne ustawienia dla `transparent_hugepage`, `numa_balancing`, `processor.max_cstate`, `ignore_ce` i `intel_idle.max_cstate`.

* intel_idle. max_cstate = 1
* procesor. max_cstate = 1
* transparent_hugepage = nigdy
* numa_balancing = Wyłącz
* MCE = ignore_ce


#### <a name="execution-steps"></a>Kroki wykonywania

* Dodaj te parametry do wiersza `GRB_CMDLINE_LINUX` w pliku `/etc/default/grub`
```
intel_idle.max_cstate=1 processor.max_cstate=1 transparent_hugepage=never numa_balancing=disable mce=ignore_ce
```
* Utwórz nowy plik grub.
```
grub2-mkconfig -o /boot/grub2/grub.cfg
```
* Uruchom ponownie system.


## <a name="next-steps"></a>Następne kroki
- Odwołaj się do [kopii zapasowych i przywracania](hana-overview-high-availability-disaster-recovery.md) dla klasy SKU typu kopia zapasowa systemu operacyjnego.
- Zapoznaj się [z kopią zapasową systemu operacyjnego dla jednostek SKU typu II poprawki 3 sygnatury](os-backup-type-ii-skus.md) dla klasy SKU typu II.
