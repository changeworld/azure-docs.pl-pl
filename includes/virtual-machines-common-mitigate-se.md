---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/12/2019
ms.author: cynthn;kareni
ms.custom: include file
ms.openlocfilehash: 6668d9753d0b93ab907d37cdeff8315f488cff7a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73935884"
---
**Ostatnia aktualizacja dokumentu**: 12 listopada 2019 10:00 PST.

Ujawnienie nowej [klasy luk w zabezpieczeniach procesora,](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002) znanej jako ataki kanału po stronie spekulacyjnego wykonania, spowodowało pytania klientów poszukujących większej jasności.  

Firma Microsoft wdrożyła środki zaradcze we wszystkich naszych usługach w chmurze. Infrastruktura, która uruchamia platformę Azure i izoluje obciążenia klientów od siebie nawzajem jest chroniona. Oznacza to, że potencjalna osoba atakująca korzystająca z tej samej infrastruktury nie może zaatakować aplikacji przy użyciu tych luk.

Platforma Azure używa [konserwacji zachowania pamięci,](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates#maintenance-that-doesnt-require-a-reboot) gdy tylko jest to możliwe, aby zminimalizować wpływ na klienta i wyeliminować potrzebę ponownego uruchamiania. Platforma Azure będzie nadal korzystać z tych metod podczas dokonywania aktualizacji ogólnosystemowych do hosta i chronić naszych klientów.

Więcej informacji na temat integracji zabezpieczeń z każdym aspektem platformy Azure jest dostępna w [witrynie dokumentacji zabezpieczeń platformy Azure.](https://docs.microsoft.com/azure/security/) 

> [!NOTE] 
> Od czasu opublikowania tego dokumentu ujawniono wiele wariantów tej klasy luk w zabezpieczeniach. Firma Microsoft nadal intensywnie inwestuje w ochronę naszych klientów i udzielanie wskazówek. Ta strona będzie aktualizowana w miarę wydawania kolejnych poprawek. 
> 
> 12 listopada 2019 [r. firma Intel opublikowała](https://software.intel.com/security-software-guidance/insights/deep-dive-intel-transactional-synchronization-extensions-intel-tsx-asynchronous-abort) poradnik techniczny dotyczący luki w zabezpieczeniach Intel® Transactional Sync Extensions (Intel® TSX) Transakcji Asynchroniczne Przerwanie (TAA), która jest przypisana [do CVE-2019-11135.](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2019-11135) Luka ta dotyczy procesorów Intel® Core® i procesorów Intel® Xeon®.  Platforma Microsoft Azure wydała aktualizacje systemu operacyjnego i wdraża nowy mikrokod, który jest udostępniany przez firmę Intel w całej naszej flocie, aby chronić naszych klientów przed tymi nowymi lukami w zabezpieczeniach.   Platforma Azure ściśle współpracuje z firmą Intel w celu przetestowania i zweryfikowanego nowego mikrokodu przed jego oficjalną wersją na platformie. 
>
> **Klienci, którzy korzystają z niezaufanego kodu w ramach maszyny Wirtualnej,** muszą podjąć działania w celu ochrony przed tymi lukami, czytając poniżej, aby uzyskać dodatkowe wskazówki dotyczące wszystkich luk w zabezpieczeniach kanału po stronie wykonywania spekulacyjnych (Microsoft Advisories ADV [180002](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002), [180018](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/adv180018)i [190013](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV190013)).
>
> Inni klienci powinni ocenić te luki w zabezpieczeniach z perspektywy obrony w głębi i wziąć pod uwagę wpływ na bezpieczeństwo i wydajność wybranej konfiguracji.
> 



## <a name="keeping-your-operating-systems-up-to-date"></a>Aktualizowanie systemów operacyjnych

Chociaż aktualizacja systemu operacyjnego nie jest wymagana do izolowania aplikacji uruchomionych na platformie Azure od innych klientów platformy Azure, zawsze jest najlepszym rozwiązaniem, aby oprogramowanie było aktualne. Najnowsze zestawienia zabezpieczeń dla systemu Windows zawierają środki zaradcze dla kilku luk w zabezpieczeniach kanału po stronie wykonywania spekulacyjnych. Podobnie dystrybucje linuksa wydały wiele aktualizacji w celu wyeliminowania tych luk. Oto nasze zalecane działania mające na celu aktualizację systemu operacyjnego:

| Oferta | Zalecana akcja  |
|----------|---------------------|
| usług Azure Cloud Services  | Włącz [automatyczną aktualizację](https://docs.microsoft.com/azure/cloud-services/cloud-services-how-to-configure-portal) lub upewnij się, że używasz najnowszego systemu operacyjnego gościa. |
| Maszyny wirtualne systemu Azure Linux | Zainstaluj aktualizacje od dostawcy systemu operacyjnego. Aby uzyskać więcej informacji, zobacz [Linux](#linux) w dalszej części tego dokumentu. |
| Maszyny wirtualne systemu Azure z systemem Windows  | Zainstaluj najnowszy pakiet zbiorczy zabezpieczeń.
| Inne usługi PaaS platformy Azure | Nie ma żadnych działań potrzebnych dla klientów korzystających z tych usług. Platforma Azure automatycznie aktualizuje wersje systemu operacyjnego. |

## <a name="additional-guidance-if-you-are-running-untrusted-code"></a>Dodatkowe wskazówki, jeśli korzystasz z niezaufanego kodu 

Klienci, którzy zezwalają niezaufanym użytkownikom na wykonywanie dowolnego kodu, mogą chcieć zaimplementować niektóre dodatkowe funkcje zabezpieczeń wewnątrz swoich maszyn wirtualnych platformy Azure lub usług w chmurze. Te funkcje chronią przed wektorami ujawniania informacji wewnątrz procesu, które opisują kilka luk w zabezpieczeniach związanych z wykonywaniem spekulacyjnych.

Przykładowe scenariusze, w których zalecane są dodatkowe funkcje zabezpieczeń:

- Możesz zezwolić na kod, który nie ufasz, aby uruchomić wewnątrz maszyny Wirtualnej.  
    - *Na przykład zezwalasz jednemu z klientów na przekazywanie pliku binarnego lub skryptu, który następnie wykonano w aplikacji.* 
- Możesz zezwolić użytkownikom, którym nie ufasz, na zalogowanie się do maszyny Wirtualnej przy użyciu kont o niskich uprawnieniach.   
    - *Na przykład zezwalasz użytkownikowi o niskich uprawnieniach na logowanie się do jednej z maszyn wirtualnych przy użyciu pulpitu zdalnego lub SSH*.  
- Zezwalasz niezaufanym użytkownikom dostęp do maszyn wirtualnych zaimplementowanych za pośrednictwem zagnieżdżonej wirtualizacji.  
    - *Na przykład można kontrolować hosta funkcji Hyper-V, ale przydzielić maszyny wirtualne do niezaufanych użytkowników*. 

Klienci, którzy nie implementują scenariusza obejmującego niezaufany kod, nie muszą włączać tych dodatkowych funkcji zabezpieczeń. 

## <a name="enabling-additional-security"></a>Włączanie dodatkowych zabezpieczeń 

Jeśli korzystasz z niezaufanego kodu, można włączyć dodatkowe funkcje zabezpieczeń wewnątrz maszyny Wirtualnej lub usługi w chmurze. Jednocześnie upewnij się, że system operacyjny jest aktualny, aby włączyć funkcje zabezpieczeń wewnątrz maszyny Wirtualnej lub usługi w chmurze

### <a name="windows"></a>Windows 

Docelowy system operacyjny musi być aktualny, aby włączyć te dodatkowe funkcje zabezpieczeń. Podczas gdy wiele spekulacyjnych wykonywania po stronie kanału ograniczenia są włączone domyślnie, dodatkowe funkcje opisane w tym miejscu musi być włączony ręcznie i może powodować wpływ na wydajność. 


**Krok 1: Wyłącz hiperwątkowe na maszynie Wirtualnej** — klienci z niezaufanym kodem na maszynie wirtualnej hiperwątkowej będą musieli wyłączyć hiperwątkowe lub przenieść do niewątkowego rozmiaru maszyny Wirtualnej. Odwołaj się do [tego doc](https://docs.microsoft.com/azure/virtual-machines/windows/acu) dla listy hiperwątkowych rozmiarów maszyn wirtualnych (gdzie stosunek procesora wirtualnego do rdzenia wynosi 2:1). Aby sprawdzić, czy maszyna wirtualna ma włączone hiperwątkowe, zapoznaj się z poniższym skryptem przy użyciu wiersza polecenia systemu Windows z poziomu maszyny Wirtualnej.

Wpisz, `wmic` aby wejść do interfejsu interaktywnego. Następnie wpisz poniżej, aby wyświetlić ilość procesorów fizycznych i logicznych na maszynie wirtualnej.

```console
CPU Get NumberOfCores,NumberOfLogicalProcessors /Format:List
```

Jeśli liczba procesorów logicznych jest większa niż procesory fizyczne (rdzenie), włączona jest hiperwątkowa.  Jeśli korzystasz z maszyny Wirtualnej z hiperwątkami, [skontaktuj się z pomocą techniczną platformy Azure,](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) aby wyłączyć hiperwątkowanie.  Po wyłączeniu hiperwątkowości **obsługa będzie wymagać pełnego ponownego uruchomienia maszyny Wirtualnej.** Zapoznaj się [z liczbą rdzeni,](#core-count) aby zrozumieć, dlaczego liczba rdzeni maszyny Wirtualnej zmniejszyła się.


**Krok 2:** Równolegle do kroku 1, postępuj zgodnie z instrukcjami w [KB4072698,](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) aby sprawdzić, czy zabezpieczenia są włączone przy użyciu modułu [SpeculationControl](https://aka.ms/SpeculationControlPS) PowerShell.

> [!NOTE]
> Jeśli wcześniej pobrano ten moduł, musisz zainstalować najnowszą wersję.
>


Dane wyjściowe skryptu programu PowerShell powinny mieć następujące wartości, aby sprawdzić poprawność włączonych zabezpieczeń przed tymi lukami w zabezpieczeniach:

```
Windows OS support for branch target injection mitigation is enabled: True
Windows OS support for kernel VA shadow is enabled: True
Windows OS support for speculative store bypass disable is enabled system-wide: False
Windows OS support for L1 terminal fault mitigation is enabled: True
Windows OS support for MDS mitigation is enabled: True
Windows OS support for TAA mitigation is enabled: True
```

Jeśli dane `MDS mitigation is enabled: False`wyjściowe pokazuje , skontaktuj [się z pomocą techniczną platformy Azure, aby](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) uzyskać dostępne opcje łagodzenia.



**Krok 3:** Aby włączyć obsługę systemu operacyjnego Kernel Virtual Address Shadowing (KVAS) i Branch Target Injection (BTI), postępuj `Session Manager` zgodnie z instrukcjami w [aktualizacji KB4072698,](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) aby włączyć zabezpieczenia przy użyciu kluczy rejestru. Wymagany jest ponowny rozruch.


**Krok 4:** W przypadku wdrożeń, które używają [wirtualizacji zagnieżdżonej](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization) (tylko D3 i E3): Te instrukcje mają zastosowanie wewnątrz maszyny Wirtualnej, której używasz jako hosta funkcji Hyper-V.

1.  Postępuj zgodnie z instrukcjami zawartymi w [aktualizacji KB4072698,](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) aby włączyć zabezpieczenia przy użyciu kluczy `MinVmVersionForCpuBasedMitigations` rejestru.
2.  Ustaw typ harmonogramu funkcji `Core` hypervisor, postępując zgodnie z instrukcjami [w tym miejscu](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-scheduler-types).


### <a name="linux"></a>Linux

<a name="linux"></a>Włączenie zestawu dodatkowych funkcji zabezpieczeń wewnątrz wymaga, aby docelowy system operacyjny był w pełni aktualny. Niektóre środki zaradcze będą domyślnie włączone. W poniższej sekcji opisano funkcje, które są domyślnie wyłączone i/lub zależne od obsługi sprzętu (mikrokod). Włączenie tych funkcji może spowodować wpływ na wydajność. Aby uzyskać dalsze instrukcje, należy zapoznać się z dokumentacją dostawcy systemu operacyjnego


**Krok 1: Wyłącz hiperwątkowe na maszynie Wirtualnej** — klienci z niezaufanym kodem na maszynie wirtualnej hiperwątkowej będą musieli wyłączyć hiperwątkowe lub przenieść do maszyny Wirtualnej bez hiperwątkowy.  Odwołaj się do [tego doc](https://docs.microsoft.com/azure/virtual-machines/linux/acu) dla listy hiperwątkowych rozmiarów maszyn wirtualnych (gdzie stosunek procesora wirtualnego do rdzenia wynosi 2:1). Aby sprawdzić, czy jest uruchomiona maszyna wirtualna `lscpu` z hiperwątkową, uruchom polecenie na maszynie Wirtualnej systemu Linux. 

Jeśli `Thread(s) per core = 2`, to hiper-wątki została włączona. 

Jeśli `Thread(s) per core = 1`, hiper-wątki został wyłączony. 

 
Przykładowe dane wyjściowe dla maszyny Wirtualnej z włączoną hiperwątkową obsługą: 

```console
CPU Architecture:      x86_64
CPU op-mode(s):        32-bit, 64-bit
Byte Order:            Little Endian
CPU(s):                8
On-line CPU(s) list:   0-7
Thread(s) per core:    2
Core(s) per socket:    4
Socket(s):             1
NUMA node(s):          1

```

Jeśli korzystasz z maszyny Wirtualnej z hiperwątkami, [skontaktuj się z pomocą techniczną platformy Azure,](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) aby wyłączyć hiperwątkowanie.  Po wyłączeniu hiperwątkowości **obsługa będzie wymagać pełnego ponownego uruchomienia maszyny Wirtualnej.** Zapoznaj się [z liczbą rdzeni,](#core-count) aby zrozumieć, dlaczego liczba rdzeni maszyny Wirtualnej zmniejszyła się.



**Krok 2:** Aby ograniczyć luki w zabezpieczeniach kanału po stronie wykonywania spekulacyjnych, zapoznaj się z dokumentacją dostawcy systemu operacyjnego:   
 
- [Redhat i CentOS](https://access.redhat.com/security/vulnerabilities) 
- [SUSE](https://www.suse.com/support/kb/?doctype%5B%5D=DT_SUSESDB_PSDB_1_1&startIndex=1&maxIndex=0) 
- [Ubuntu](https://wiki.ubuntu.com/SecurityTeam/KnowledgeBase/) 


### <a name="core-count"></a>Liczba rdzeni

Po utworzeniu maszyny wirtualnej z hiperwątkową platformą Azure przydziela 2 wątki na rdzeń — są one nazywane procesorami wirtualnymi. Gdy hiperwątkowe jest wyłączone, platforma Azure usuwa wątek i powierzchnie rdzeni jednowątkowych (rdzenie fizyczne). Stosunek procesora wirtualnego do procesora CPU wynosi 2:1, więc po wyłączeniu hiperwątkowości liczba procesorów w maszynie Wirtualnej zmniejszyła się o połowę. Na przykład maszyna wirtualna D8_v3 jest hiperwątkową maszyną wirtualną działającą na 8 procesorach wirtualnych (2 wątki na rdzeń x 4 rdzenie).  Gdy hiperwątkowe jest wyłączone, procesory spadnie do 4 rdzeni fizycznych z 1 wątku na rdzeń. 

## <a name="next-steps"></a>Następne kroki

Ten artykuł zawiera wskazówki dotyczące poniższych ataków kanału po stronie wykonywania spekulacyjnych, które wpływają na wiele nowoczesnych procesorów:

[Spectre Meltdown](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV180002):
- CVE-2017-5715 - Wtrysk docelowy oddziału (WIT)  
- CVE-2017-5754 - Izolacja tabeli stron jądra (KPTI)
- CVE-2018-3639 – Spekulacyjne obejście sklepu (KPTI) 
- [CVE-2019-1125](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2019-1125) – Informacje o jądrze systemu Windows – wariant wariantu Spectre 1
 
[Awaria zacisku L1 (L1TF)](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV180018):
- CVE-2018-3615 — rozszerzenia ochrony oprogramowania Firmy Intel (Intel SGX)
- CVE-2018-3620 - System operacyjny (OS) i tryb zarządzania systemem (SMM)
- CVE-2018-3646 – wpływa na menedżera maszyn wirtualnych (VMM)

[Pobieranie próbek danych mikroarchitekturowych:](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190013) 
- CVE-2019-11091 - Mikroarchitekturowe pobieranie próbek pamięci nieskładkowej (MDSUM)
- CVE-2018-12126 - Pobieranie próbek danych buforu magazynu mikroarchitekturowego (MSBDS)
- CVE-2018-12127 - Pobieranie próbek danych portu obciążenia mikroarchitekturowego (MLPDS)
- CVE-2018-12130 - Pobieranie próbek danych buforu napełniania mikroarchitekturowego (MFBDS)

Rozszerzenia synchronizacji transakcyjnej (Intel® TSX) Asynchroniczne przerwanie transakcji:  
- [CVE-2019-11135](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2019-11135) – Asynchroniczne przerwanie transakcji TSX (TAA)








