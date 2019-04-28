---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 08/14/2018
ms.author: cynthn;kareni
ms.custom: include file
ms.openlocfilehash: cbd86571cbdcd600ef3acdea3833568a34657931
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60337961"
---
**Ostatnie dokumentowanie aktualizacji**: 14 sierpnia 2018 r. 10:00 czasu PST.

Ujawnienie [nową klasę luk w zabezpieczeniach procesora CPU](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002) nazywane ataków kanału po stronie wykonywania spekulacyjnego spowodowało pytania klientów poszukujących bardziej przejrzysty.  

Microsoft wdrożono środki zaradcze dla naszych usług w chmurze. Infrastrukturę, która działa na platformie Azure i izoluje obciążeń klientów od siebie nawzajem jest chroniony. Oznacza to, że potencjalnym osobom atakującym przy użyciu tej samej infrastruktury nie ataki swoją aplikację przy użyciu tych luk w zabezpieczeniach.

Używa usługi Azure [pamięci zachowywanie konserwacji](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates#maintenance-not-requiring-a-reboot) zawsze, gdy jest to możliwe zminimalizować wpływ na klientów i wyeliminować potrzebę ponownego uruchomienia. Platforma Azure będzie kontynuować korzystanie z tych metod, podczas wprowadzania aktualizacji ogólnosystemowe do hosta i ochrony jej klientów.

Więcej informacji na temat sposobu zabezpieczenia są zintegrowane w każdy aspekt platformy Azure jest dostępna na [dokumentacja zabezpieczeń platformy Azure](https://docs.microsoft.com/azure/security/) lokacji. 

> [!NOTE] 
> Ponieważ w tym dokumencie najpierw została opublikowana, wielu wariantów tej klasy luk w zabezpieczeniach zostały ujawnione. Firma Microsoft nadal można w dużym stopniu poświęcił w celu ochrony klientów i zapewnianie doradztwa w zakresie. Ta strona będzie aktualizowana w miarę dalszego poprawki wydania. 
> 
> 14 sierpnia 2018 r. branży lukę w zabezpieczeniach nowe związanego z wykonywaniem spekulatywnym po stronie kanału znane jako [błędów terminalu L1](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180018) (L1TF) który został przypisany wielu CVEs ([CVE-2018-3615, CVE-2018-3620 i CVE-2018-3646](https://www.intel.com/content/www/us/en/security-center/advisory/intel-sa-00161.html)). Tę lukę w zabezpieczeniach dotyczy procesorów Intel Core® i procesorów Intel® Xeon®. Microsoft wdrożono środki zaradcze dla naszych usług w chmurze, które wzmocnienia izolacji między klientami. Przeczytaj poniżej, aby uzyskać dodatkowe wskazówki zapewnić ochronę przed L1TF i poprzedniej luki w zabezpieczeniach ([krokami zaradczymi dla luki wariantu 2 CVE-2017-5715 i Meltdown Variant 3 CVE-2017-5754](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution)).
>  






## <a name="keeping-your-operating-systems-up-to-date"></a>Aktualizowanie przez systemy operacyjne

Podczas aktualizacji systemu operacyjnego nie jest wymagany do izolowania aplikacji działających na platformie Azure z innych klientów platformy Azure, zawsze jest najlepszym rozwiązaniem jest, aby zapewnić aktualność oprogramowania. Najnowsze zabezpieczenia pakietów zbiorczych dla Windows zawiera środki zaradcze dla kilku związanego z wykonywaniem spekulatywnym po stronie kanału luk w zabezpieczeniach. Podobnie dystrybucje systemu Linux zostały wydane wiele aktualizacji, aby rozwiązać te luki w zabezpieczeniach. Oto nasze zalecane akcje do aktualizacji systemu operacyjnego:

| Oferty | Zalecana akcja  |
|----------|---------------------|
| usług Azure Cloud Services  | Włącz [automatyczne aktualizowanie](https://docs.microsoft.com/azure/cloud-services/cloud-services-how-to-configure-portal) lub upewnij się, są uruchomione najnowszych systemu operacyjnego gościa. |
| Azure Linux Virtual Machines | Zainstaluj aktualizacje za pośrednictwem swojego usługodawcy systemu operacyjnego. Aby uzyskać więcej informacji, zobacz [Linux](#linux) w dalszej części tego dokumentu. |
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

Można włączyć dodatkowe funkcje zabezpieczeń w swojej maszyny Wirtualnej lub usługi w chmurze.

### <a name="windows"></a>Windows 

Aktualne, aby włączyć te dodatkowe funkcje zabezpieczeń muszą być docelowego systemu operacyjnego. Chociaż wiele z wykonywaniem spekulatywnym po stronie kanału środki zaradcze są domyślnie włączone, dodatkowe funkcje, które są opisane w tym miejscu musi być włączone ręczne i może spowodować, że wpływ na wydajność. 

**Krok 1**: [Skontaktuj się z działem pomocy technicznej systemu Azure](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) udostępniają aktualizacji oprogramowania układowego (mikrokodu) do maszyn wirtualnych. 

**Krok 2**: Włącz obsługę jądra wirtualnego adresu przesłanianie (KVAS) i systemu operacyjnego w gałęzi docelowej iniekcji (WIT). Postępuj zgodnie z instrukcjami w [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) Aby włączyć ochronę za pomocą `Session Manager` kluczy rejestru. Wymagany jest ponowny rozruch. 

**Krok 3**: W przypadku wdrożeń, które korzystają z [wirtualizacji zagnieżdżonej](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization) (D3 i tylko E3): Te instrukcje mają zastosowanie wewnątrz maszyny Wirtualnej, której używasz jako hosta funkcji Hyper-V. 

1. Postępuj zgodnie z instrukcjami w [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) Aby włączyć ochronę za pomocą `MinVmVersionForCpuBasedMitigations` kluczy rejestru.  
 
1. Ustaw typ harmonogramu funkcji hypervisor **Core** zgodnie z instrukcjami [tutaj](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-scheduler-types). 

**Krok 4**: Postępuj zgodnie z instrukcjami w [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) Aby sprawdzić, zabezpieczenia są włączone, za pomocą [SpeculationControl](https://aka.ms/SpeculationControlPS) modułu programu PowerShell. 

> [!NOTE]
> Jeśli ten moduł został wcześniej pobrany, należy zainstalować najnowszą wersję.
>

Wszystkie maszyny wirtualne powinny pokazywać:

```
branch target injection mitigation is enabled: True

kernel VA shadow is enabled: True  

L1TFWindowsSupportEnabled: True
```


### <a name="linux"></a>Linux

<a name="linux"></a>Włączanie zestaw dodatkowych funkcji zabezpieczeń wewnątrz wymaga docelowy system operacyjny aktualne. Niektóre środki zaradcze zostaną włączone domyślnie. W poniższej sekcji opisano funkcje, które są wyłączone, domyślnie i/lub na architekturze na pomoc techniczna dotycząca sprzętu (mikrokodu). Włączenie tych funkcji może spowodować negatywny wpływ na wydajność. Aby uzyskać dalsze instrukcje dokumentacji dostawcy systemu operacyjnego
 
**Krok 1**: [Skontaktuj się z działem pomocy technicznej systemu Azure](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) udostępniają aktualizacji oprogramowania układowego (mikrokodu) do maszyn wirtualnych.
 
**Krok 2**: Włącz obsługę gałęzi docelowej iniekcji (WIT) systemu operacyjnego złagodzić CVE-2017-5715 (krokami zaradczymi dla luki wariantu 2), postępując zgodnie z dokumentacją dostawcy systemu operacyjnego. 
 
**Krok 3**: Włącz jądra strony tabeli izolacji (KPTI) aby uniknąć CVE-2017-5754 (Meltdown Variant 3), postępując zgodnie z dokumentacją dostawcy systemu operacyjnego. 
 
Więcej informacji znajduje się z dostawcą systemu operacyjnego:  
 
- [RedHat i CentOS](https://access.redhat.com/security/vulnerabilities/speculativeexecution) 
- [SUSE](https://www.suse.com/support/kb/doc/?id=7022512) 
- [Ubuntu](https://wiki.ubuntu.com/SecurityTeam/KnowledgeBase/SpectreAndMeltdown) 


## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej, zobacz [klientów zabezpieczania platformy Azure przed luką w zabezpieczeniach procesora CPU](https://azure.microsoft.com/blog/securing-azure-customers-from-cpu-vulnerability/).
