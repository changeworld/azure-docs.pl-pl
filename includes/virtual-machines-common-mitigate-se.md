---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 06/04/2019
ms.author: cynthn;kareni
ms.custom: include file
ms.openlocfilehash: 169e25aeb8503a11f768a2a3062022eef51a76b8
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2019
ms.locfileid: "67659867"
---
**Ostatnie dokumentowanie aktualizacji**: 4 czerwca 2019 r 3:00 PM czasu Pacyficznego.

Ujawnienie [nową klasę luk w zabezpieczeniach procesora CPU](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002) nazywane ataków kanału po stronie wykonywania spekulacyjnego spowodowało pytania klientów poszukujących bardziej przejrzysty.  

Microsoft wdrożono środki zaradcze dla naszych usług w chmurze. Infrastrukturę, która działa na platformie Azure i izoluje obciążeń klientów od siebie nawzajem jest chroniony. Oznacza to, że potencjalnym osobom atakującym przy użyciu tej samej infrastruktury nie ataki swoją aplikację przy użyciu tych luk w zabezpieczeniach.

Używa usługi Azure [pamięci zachowywanie konserwacji](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates#maintenance-that-doesnt-require-a-reboot) zawsze, gdy jest to możliwe zminimalizować wpływ na klientów i wyeliminować potrzebę ponownego uruchomienia. Platforma Azure będzie kontynuować korzystanie z tych metod, podczas wprowadzania aktualizacji ogólnosystemowe do hosta i ochrony jej klientów.

Więcej informacji na temat sposobu zabezpieczenia są zintegrowane w każdy aspekt platformy Azure jest dostępna na [dokumentacja zabezpieczeń platformy Azure](https://docs.microsoft.com/azure/security/) lokacji. 

> [!NOTE] 
> Ponieważ w tym dokumencie najpierw została opublikowana, wielu wariantów tej klasy luk w zabezpieczeniach zostały ujawnione. Firma Microsoft nadal można w dużym stopniu poświęcił w celu ochrony klientów i zapewnianie doradztwa w zakresie. Ta strona będzie aktualizowana w miarę dalszego poprawki wydania. 
> 
> Na 14 maja 2019 r [Intel ujawnione](https://www.intel.com/content/www/us/en/security-center/advisory/intel-sa-00233.html) nowego zestawu z wykonywaniem spekulatywnym po stronie kanału luki w zabezpieczeniach znane jako próbkowanie danych mikroarchitektury (MDS, zobacz wskazówki dotyczące zabezpieczeń firmy Microsoft [ADV190013](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV190013)), który przypisano wiele CVEs: 
> - CVE-2019-11091 - mikroarchitektury dane próbkowania pamięci Uncacheable (MDSUM)
> - CVE-2018-12126 - Store mikroarchitektury buforowania danych próbkowania (MSBDS) 
> - CVE-2018-12127 — Port obciążenia mikroarchitektury danych próbkowania (MLPDS)
> - CVE-2018-12130 - wypełnienia mikroarchitektury buforowania danych próbkowania (MFBDS)
>
> Tę lukę w zabezpieczeniach dotyczy procesorów Intel Core® i procesorów Intel® Xeon®.  Microsoft Azure został wydany aktualizacji systemu operacyjnego i wdraża nowy mikrokodu, jak staje się dostępna przez firmę Intel, w całej naszej floty do naszych zapewnienia klientom ochrony przed tych nowych luk w zabezpieczeniach.   Zespół Azure ściśle współpracuje z Intel, testować i weryfikować nowe mikrokodu przed jej oficjalnym wydaniem, na platformie. 
>
> **Klienci, którzy są uruchomione niezaufanego kodu w ramach ich maszyn wirtualnych** należy podjąć działania w celu ochrony przed te luki w zabezpieczeniach, czytając poniżej, aby uzyskać dodatkowe wskazówki na wszystkich z wykonywaniem spekulatywnym kanału po stronie luk w zabezpieczeniach (Microsoft biuletyny Zaawansowana [180002](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002), [180018](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/adv180018), i [190013](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV190013)).
>
> Pozostali klienci powinni należy ocenić te luki w zabezpieczeniach z obrony w perspektywie głębokość i uwzględnić jej wpływ bezpieczeństwo i wydajność swoich wybranej konfiguracji.



## <a name="keeping-your-operating-systems-up-to-date"></a>Aktualizowanie przez systemy operacyjne

Podczas aktualizacji systemu operacyjnego nie jest wymagany do izolowania aplikacji działających na platformie Azure z innych klientów platformy Azure, zawsze jest najlepszym rozwiązaniem jest, aby zapewnić aktualność oprogramowania. Najnowsze zabezpieczenia pakietów zbiorczych dla Windows zawiera środki zaradcze dla kilku związanego z wykonywaniem spekulatywnym po stronie kanału luk w zabezpieczeniach. Podobnie dystrybucje systemu Linux zostały wydane wiele aktualizacji, aby rozwiązać te luki w zabezpieczeniach. Oto nasze zalecane akcje do aktualizacji systemu operacyjnego:

| Oferty | Zalecana akcja  |
|----------|---------------------|
| Usług Azure Cloud Services  | Włącz [automatyczne aktualizowanie](https://docs.microsoft.com/azure/cloud-services/cloud-services-how-to-configure-portal) lub upewnij się, są uruchomione najnowszych systemu operacyjnego gościa. |
| Maszyny wirtualne systemu Linux platformy Azure | Zainstaluj aktualizacje za pośrednictwem swojego usługodawcy systemu operacyjnego. Aby uzyskać więcej informacji, zobacz [Linux](#linux) w dalszej części tego dokumentu. |
| Maszyn wirtualnych Windows Azure  | Zainstaluj najnowszy pakiet zbiorczy zabezpieczeń.
| Inne usługi PaaS platformy Azure | Nie ma akcji wymagane dla klientów korzystających z tych usług. Azure automatycznie aktualizuje swoje wersje systemów operacyjnych aktualne. |

## <a name="additional-guidance-if-you-are-running-untrusted-code"></a>Dodatkowe wskazówki, jeśli korzystasz z niezaufanego kodu 

Klienci, którzy zezwalać niezaufanym użytkownikom na wykonanie dowolnego kodu pragną we wdrażaniu niektórych funkcji zabezpieczeń w ich maszyn wirtualnych platformy Azure lub usług w chmurze. Te funkcje ochrony przed wektorów wewnątrzprocesową ujawnienie, których opisano kilka usterek z wykonywaniem spekulatywnym.

Przykładowe scenariusze, w którym są zalecane dodatkowe funkcje zabezpieczeń:

- Możesz zezwolić kod, który nie ufasz do uruchomienia wewnątrz maszyny Wirtualnej.  
    - *Na przykład jeden z klientami w celu przekazywania pliku binarnego lub skrypt, a następnie wykonaj w aplikacji pozwalają*. 
- Użytkownicy, których nie masz zaufania do logowania się do maszyny Wirtualnej za pomocą niski kont uprzywilejowanych.   
    - *Na przykład zezwolisz na niskim poziomem uprawnień użytkownika do logowania się do jednej z maszyn wirtualnych przy użyciu pulpitu zdalnego lub SSH*.  
- Zezwalaj niezaufanym użytkownikom dostęp do maszyn wirtualnych, wdrożone za pośrednictwem wirtualizacji zagnieżdżonej.  
    - *Na przykład kontrolować hosta funkcji Hyper-V, ale przydzielania maszyn wirtualnych do niezaufanym użytkownikom*. 

Klienci, którzy nie należy implementować scenariusza obejmujące niezaufanego kodu jest konieczne te dodatkowe funkcje zabezpieczeń. 

## <a name="enabling-additional-security"></a>Włączanie dodatkowych zabezpieczeń 

Jeśli korzystasz z niezaufanego kodu, można włączyć dodatkowe funkcje zabezpieczeń w swojej maszyny Wirtualnej lub usługi w chmurze. Równolegle upewnij się, że Twój system operacyjny jest aktualny, aby włączyć funkcje zabezpieczeń w swojej maszyny Wirtualnej lub usługi w chmurze

### <a name="windows"></a>Windows 

Aktualne, aby włączyć te dodatkowe funkcje zabezpieczeń muszą być docelowego systemu operacyjnego. Chociaż wiele z wykonywaniem spekulatywnym po stronie kanału środki zaradcze są domyślnie włączone, dodatkowe funkcje, które są opisane w tym miejscu musi być włączone ręczne i może spowodować, że wpływ na wydajność. 


**Krok 1. Wyłącz funkcję hyper threading na maszynie Wirtualnej** — klienci korzystający z niezaufanego kodu na maszynie Wirtualnej funkcji hyper Threading należy wyłączyć funkcję hyper threading lub przenieść do innego niż hyper Threading rozmiar maszyny Wirtualnej. Odwołanie [tego dokumentu](https://docs.microsoft.com/azure/virtual-machines/windows/acu) listę rozmiarów maszyn wirtualnych funkcji hyper Threading (gdzie stosunek procesorów wirtualnych na rdzeń to 2:1). Aby sprawdzić, czy maszyna wirtualna ma funkcję hyper threading włączone, można znaleźć poniżej skrypt przy użyciu wiersza polecenia Windows z poziomu maszyny Wirtualnej.

Typ `wmic` wprowadzenia interaktywny interfejs. Następnie wpisz poniżej, aby wyświetlić ilość fizycznych i logicznych procesorów na maszynie Wirtualnej.

```console
CPU Get NumberOfCores,NumberOfLogicalProcessors /Format:List
```

Jeśli liczba procesorów logicznych jest większa niż procesorów fizycznych (rdzenie), następnie funkcję hyper threading jest włączona.  Jeśli używasz maszyny Wirtualnej funkcji hyper Threading [skontaktuj się z działem pomocy technicznej systemu Azure](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) uzyskać funkcji hyper-threading wyłączony.  Po wyłączeniu funkcji hyper-threading **pomoc techniczna będzie wymagać pełne ponowne uruchomienie maszyny Wirtualnej**. Zapoznaj się [podstawowe liczba](#core-count) Aby zrozumieć, dlaczego zmniejszyła się liczba rdzeni Twojej maszyny Wirtualnej.


**Krok 2**: Równolegle do kroku 1, postępuj zgodnie z instrukcjami [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) Aby sprawdzić, zabezpieczenia są włączone, za pomocą [SpeculationControl](https://aka.ms/SpeculationControlPS) modułu programu PowerShell.

> [!NOTE]
> Jeśli ten moduł został wcześniej pobrany, należy zainstalować najnowszą wersję.
>


Dane wyjściowe skryptu programu PowerShell powinna mieć poniższych wartości, aby sprawdzić poprawność włączono ochronę przed te luki w zabezpieczeniach:

```
Windows OS support for branch target injection mitigation is enabled: True
Windows OS support for kernel VA shadow is enabled: True
Windows OS support for speculative store bypass disable is enabled system-wide: False
Windows OS support for L1 terminal fault mitigation is enabled: True
Windows OS support for MDS mitigation is enabled: True
```

Jeśli dane wyjściowe pokazują `MDS mitigation is enabled: False`, [skontaktuj się z działem pomocy technicznej systemu Azure](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) środki zaradcze dostępne opcje.



**Krok 3**: Aby włączyć jądra wirtualnego adresu przesłanianie (KVAS) i pomoc techniczna w gałęzi docelowej iniekcji (WIT) systemu operacyjnego, postępuj zgodnie z instrukcjami [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) Aby włączyć ochronę za pomocą `Session Manager` kluczy rejestru. Wymagany jest ponowny rozruch.


**Krok 4**: W przypadku wdrożeń, które korzystają z [wirtualizacji zagnieżdżonej](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization) (D3 i tylko E3): Te instrukcje mają zastosowanie wewnątrz maszyny Wirtualnej, której używasz jako hosta funkcji Hyper-V.

1.  Postępuj zgodnie z instrukcjami w [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) Aby włączyć ochronę za pomocą `MinVmVersionForCpuBasedMitigations` kluczy rejestru.
2.  Ustaw typ harmonogramu funkcji hypervisor `Core` zgodnie z instrukcjami [tutaj](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-scheduler-types).


### <a name="linux"></a>Linux

<a name="linux"></a>Włączanie zestaw dodatkowych funkcji zabezpieczeń wewnątrz wymaga docelowy system operacyjny aktualne. Niektóre środki zaradcze zostaną włączone domyślnie. W poniższej sekcji opisano funkcje, które są wyłączone, domyślnie i/lub na architekturze na pomoc techniczna dotycząca sprzętu (mikrokodu). Włączenie tych funkcji może spowodować negatywny wpływ na wydajność. Aby uzyskać dalsze instrukcje dokumentacji dostawcy systemu operacyjnego


**Krok 1. Wyłącz funkcję hyper threading na maszynie Wirtualnej** — klienci korzystający z niezaufanego kodu na maszynie Wirtualnej funkcji hyper Threading należy przenieść do maszyny Wirtualnej bez hyper Threading lub wyłączyć funkcję hyper threading.  Odwołanie [tego dokumentu](https://docs.microsoft.com/azure/virtual-machines/linux/acu) listę rozmiarów maszyn wirtualnych funkcji hyper Threading (gdzie stosunek procesorów wirtualnych na rdzeń to 2:1). Aby sprawdzić, czy używasz maszyny Wirtualnej funkcji hyper Threading, uruchom `lscpu` polecenia na maszynie Wirtualnej systemu Linux. 

Jeśli `Thread(s) per core = 2`, następnie funkcję hyper threading został włączony. 

Jeśli `Thread(s) per core = 1`, następnie funkcję hyper threading została wyłączona. 

 
Przykładowe dane wyjściowe dla maszyny Wirtualnej za pomocą funkcji hyper-threading włączone: 

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

Jeśli używasz maszyny Wirtualnej funkcji hyper Threading [skontaktuj się z działem pomocy technicznej systemu Azure](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) uzyskać funkcji hyper-threading wyłączony.  Po wyłączeniu funkcji hyper-threading **pomoc techniczna będzie wymagać pełne ponowne uruchomienie maszyny Wirtualnej**. Zapoznaj się [podstawowe liczba](#core-count) Aby zrozumieć, dlaczego zmniejszyła się liczba rdzeni Twojej maszyny Wirtualnej.



**Krok 2**: Aby ograniczyć ryzyko ze znajdujących się poniżej związanego z wykonywaniem spekulatywnym kanału po stronie luk w zabezpieczeniach można znaleźć w dokumentacji dostawcy systemu operacyjnego:   
 
- [RedHat i CentOS](https://access.redhat.com/security/vulnerabilities) 
- [SUSE](https://www.suse.com/support/kb/?doctype%5B%5D=DT_SUSESDB_PSDB_1_1&startIndex=1&maxIndex=0) 
- [Ubuntu](https://wiki.ubuntu.com/SecurityTeam/KnowledgeBase/) 


### <a name="core-count"></a>Liczba rdzeni

Po utworzeniu maszyny Wirtualnej funkcji hyper Threading platforma Azure przydziela 2 wątków na każdy rdzeń — są to tak zwane procesorów wirtualnych. Po wyłączeniu funkcji hyper-threading Azure usuwa wątku i ujawniają się jednym wątków rdzeni (fizycznych). Stosunek procesorów wirtualnych do procesora CPU jest 2:1, więc jeden raz — Hiperwątkowość jest wyłączona, Procesor zliczania na maszynie wirtualnej zostanie wyświetlony jako spadły o połowę. Na przykład D8_v3 maszyny Wirtualnej jest maszyny Wirtualnej funkcji hyper Threading działającej na 8 wirtualnych procesorów CPU (2 wątków na podstawowe x 4 rdzeni).  Po wyłączeniu funkcji hyper-threading procesorów będzie się zmniejszać do 4 rdzeni fizycznych z 1 wątek na każdy rdzeń. 

## <a name="next-steps"></a>Następne kroki

Ten artykuł zawiera wskazówki dotyczące poniżej związanego z wykonywaniem spekulatywnym kanału po stronie ataków, które wpływają na wiele procesorów nowoczesnych:

[Meltdown krokami zaradczymi dla luki](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV180002):
- CVE-2017-5715 - gałęzi docelowej iniekcji (WIT)  
- CVE-2017-5754 - izolacji tabeli stron jądra (KPTI)
- CVE-2018-3639 — obejście spekulacyjnego Store (KPTI) 
 
[L1 Terminalu błędów (L1TF)](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV180018):
- CVE-2018-3615 — rozszerzenia Guard oprogramowania firmy Intel (Intel SGX)
- CVE-2018-3620 — systemy operacyjne (OS) i tryb zarządzania systemem (SMM)
- CVE-2018-3646 — Virtual Machine Manager (VMM) na środowisko

[Próbkowanie danych mikroarchitektury](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190013): 
- CVE-2019-11091 - mikroarchitektury dane próbkowania pamięci Uncacheable (MDSUM)
- CVE-2018-12126 - Store mikroarchitektury buforowania danych próbkowania (MSBDS)
- CVE-2018-12127 — Port obciążenia mikroarchitektury danych próbkowania (MLPDS)
- CVE-2018-12130 - wypełnienia mikroarchitektury buforowania danych próbkowania (MFBDS)








