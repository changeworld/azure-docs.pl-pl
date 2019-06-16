---
title: Aktualizacje usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: Omówienie usługi aktualizacji i sposobu uaktualniania składniki używane w usłudze Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/25/2019
ms.author: rajanaki
ms.openlocfilehash: dc15c8935cd3c20037bbed01f0f24cd2b8642045
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65540927"
---
# <a name="service-updates-in-azure-site-recovery"></a>Aktualizacje usługi w usłudze Azure Site Recovery
Jako organizacja niezbędne jest ustalenie, jak zamierzasz zapewnić bezpieczeństwo danych i aplikacji/obciążeń podczas planowanych i nieplanowanych przestojów. Usługa Azure Site Recovery wspiera strategię BCDR przez zapewnienie niezawodnego działania aplikacji uruchomionych na maszynach wirtualnych i serwerów fizycznych, które są dostępne w przypadku wyłączenia witryny. Usługa Site Recovery replikuje obciążenia uruchomione na maszynach wirtualnych i serwerach fizycznych, dzięki czemu są one nadal dostępne w lokalizacji dodatkowej, jeśli lokacja główna jest niedostępna. Gdy lokacja główna zostanie ponowne uruchomiona, obciążenia zostaną odzyskane w tej lokacji.

Usługa Site Recovery może zarządzać replikacją dla:

- [Maszyny wirtualne platformy Azure replikowanych między regionami platformy Azure](azure-to-azure-tutorial-dr-drill.md).
- Lokalnych maszyn wirtualnych i serwerów fizycznych replikowanych do platformy Azure lub lokacji dodatkowej.
Aby dowiedzieć się więcej, zapoznaj się z dokumentacją [tutaj](https://docs.microsoft.com/azure/site-recovery) .

Usługa Azure Site Recovery publikuje aktualizacje usług na bieżąco — w tym dodawanie nowych funkcji, ulepszeń w macierzy obsługi i poprawki błędów, jeśli istnieje. Aby pozostać bieżącego skorzystaj z zalet wszystkie najnowsze funkcje i ulepszenia i poprawki ewentualnej, użytkownicy doradza się zawsze aktualizację do najnowszej wersje składników usługi Azure SIte Recovery. 
 
## <a name="support-statement-for-azure-site-recovery"></a>Oświadczenie pomocy technicznej dla usługi Azure Site Recovery 

> [!IMPORTANT]
> **Przy każdej nowej wersji "n" z usługi Azure Site Recovery składnik, który jest zwalniana, wszystkie wersje poniżej n-4 "jest uważany za okres wsparcia technicznego upływa**. Dlatego zawsze zalecane jest uaktualnienie do najnowszej wersji dostępna.

> [!IMPORTANT]
> Oficjalna Obsługa uaktualnień jest z > N-4 do wersji N (N jest najnowsza wersja). Jeśli użytkownik pracuje na N-6, musisz najpierw przeprowadzić uaktualnienie do N-4, a następnie do N.

### <a name="upgrading-when-the-difference-between-current-version-and-latest-released-version-is-greater-than-4"></a>Uaktualnianie, gdy różnicę między bieżącą wersję oraz najnowszą wersję oficjalną jest większy niż 4

1. Pierwszym krokiem uaktualnienie aktualnie zainstalowanych składników z wersji say N N + 4, a następnie przejdź do następnego zgodnej wersji. Załóżmy, że bieżąca wersja to 9,24 i znajdują się na 9.16, najpierw uaktualnić 9,20, a następnie 9,24.
2. Postępuj zgodnie z tym samym procesie dla wszystkich składników w zależności od scenariusza.

### <a name="support-for-latest-oskernel-versions"></a>Obsługa najnowszej wersji systemu operacyjnego/jądra

> [!NOTE]
> Jeśli masz zaplanowane okno obsługi, i ponowne uruchomienie jest częścią tego samego, zalecamy najpierw uaktualnić składników usługi Site Recovery i wykonać pozostałe zaplanowane działania.

1. Przed uaktualnieniem usługi jądra/wersji systemu operacyjnego, należy najpierw sprawdzić, czy docelowa wersja jest obsługiwana przez usługę Azure Site Recovery. Informacje można znaleźć w naszej dokumentacji maszyn wirtualnych platformy Azure, [maszyny wirtualne VMware](vmware-physical-azure-support-matrix.md) & maszyn wirtualnych funkcji Hyper-V na platformie
2. Zobacz nasze [aktualizacji usługi](https://azure.microsoft.com/updates/?product=site-recovery) Aby dowiedzieć się, która wersja programu Site Recovery składników obsługi określonej wersji, chcą przeprowadzić uaktualnienie do.
3. Najpierw należy uaktualnić do najnowszej wersji lokacji odzyskiwania.
4. Teraz można uaktualnić system operacyjny/jądra do żądanej wersji.
5. Wykonaj ponowny rozruch.
6. Pozwoli to zagwarantować uaktualniania wersji systemu operacyjnego/jądra na maszynach do najnowszej wersji, a najnowsze zmiany Site Recovery, które są wymagane do obsługi nowej wersji, również są ładowane na maszynie źródłowej.



## <a name="azure-vm-disaster-recovery-to-azure"></a>Odzyskiwanie po awarii maszyn wirtualnych platformy Azure na platformę Azure
W tym scenariuszu zaleca możesz [Włącz](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-autoupdate) aktualizacje automatyczne. Można wybrać umożliwić odzyskiwanie lokacji do zarządzania aktualizacjami w następujący sposób:

- W ramach kroku Włączanie replikacji
- Przełącz rozszerzenie aktualizowanie ustawień w magazynie

W przypadku, gdy wybrano ręczne zarządzanie aktualizacjami, wykonaj następujące kroki:

1. Przejdź do witryny Azure portal, a następnie przejdź do "magazyn usługi Recovery services."
2. Przejdź do okienka "Zreplikowane elementy" w witrynie Azure portal, w którym są "magazyn usługi Recovery services."
3. Kliknij następujące powiadomienie w górnej części ekranu:
    
    *Dostępna jest nowa aktualizacja agenta replikacji usługa Site Recovery*
    
    *Kliknij, aby zainstalować ->*

4. Wybierz maszyny wirtualne, które chcesz zastosować aktualizacji, a następnie kliknij przycisk **OK**.

## <a name="between-two-on-premises-vmm-sites"></a>Między dwiema lokalnymi lokacjami VMM
1. Pobierz najnowszą aktualizację zbiorczy dostawcy Microsoft Azure Site Recovery.
2. Najpierw zainstaluj zbiorczy pakiet aktualizacji na serwerze programu VMM w środowisku lokalnym, który zarządza lokacji odzyskiwania.
3. Po zaktualizowaniu lokacji, zainstaluj zbiorczy pakiet aktualizacji na serwerze programu VMM, który jest zarządzany z lokacji głównej.

> [!NOTE]
> Jeśli program VMM o wysokiej dostępności program VMM (klastra VMM), upewnij się, zainstaluj uaktualnienia we wszystkich węzłach klastra, na którym zainstalowano usługę programu VMM.

## <a name="between-an-on-premises-vmm-site-and-azure"></a>Między lokalną lokacją VMM i platformą Azure
1. Pobierz pakiet zbiorczy aktualizacji dla dostawcy Microsoft Azure Site Recovery.
2. Zainstaluj zbiorczy pakiet aktualizacji na serwerze programu VMM w środowisku lokalnym.
3. Zainstaluj najnowszą wersję agenta usług MARS agent na wszystkich hostach funkcji Hyper-V.

> [!NOTE]
> Jeśli usługi programu VMM o wysokiej dostępności program VMM (klastra VMM), upewnij się, zainstaluj uaktualnienia we wszystkich węzłach klastra, na którym zainstalowano usługę programu VMM.

## <a name="between-an-on-premises-hyper-v-site-and-azure"></a>Między lokalną lokacją funkcji Hyper-V i platformą Azure

1. Pobierz pakiet zbiorczy aktualizacji dla dostawcy Microsoft Azure Site Recovery.
2. Zainstaluj dostawcę w każdym węźle serwerów funkcji Hyper-V, które zostały zarejestrowane w usłudze Azure Site Recovery.

> [!NOTE]
> W przypadku funkcji Hyper-V jest serwerem hosta klastra funkcji Hyper-V, upewnij się, zainstaluj uaktualnienia we wszystkich węzłach klastra

## <a name="between-an-on-premises-vmware-or-physical-site-to-azure"></a>Między lokalnych zasobów programu VMware lub fizyczne witryny na platformie Azure

Przed kontynuowaniem aktualizacji, zapoznaj się [instrukcji obsługi Site Recovery](#support-statement-for-azure-site-recovery) zrozumienie ścieżki uaktualnienia.

1. Oparte na swoim wyciągu bieżącej wersji i pomoc techniczna podanej powyżej, zainstalować aktualizacji na serwerze zarządzania w środowisku lokalnym postępując zgodnie z wytycznymi podanymi [tutaj](vmware-azure-deploy-configuration-server.md#upgrade-the-configuration-server). Jest to serwer z serwerem konfiguracji i role serwera przetwarzania.
2. W przypadku skalowania w poziomie serwerów przetwarzania, zaktualizować je dalej, zgodnie z wytycznymi podanymi [tutaj](vmware-azure-manage-process-server.md#upgrade-a-process-server).
3. Następnie, aby zaktualizować agenta mobilności na poszczególnych chronionych elementów, przejdź do witryny Azure portal, a następnie przejdź do **chronione elementy** > **zreplikowane elementy** strony. Wybierz maszynę Wirtualną na tej stronie. Wybierz **usługi Windows Update Agent** znajdujący się w dolnej części strony dla każdej maszyny Wirtualnej. Spowoduje to zaktualizowanie agenta usługi mobilności na wszystkich chronionych maszyn wirtualnych.

### <a name="reboot-of-source-machine-after-mobility-agent-upgrade"></a>Ponowne uruchomienie maszyny źródłowej, po uaktualnieniu agenta mobilności

Ponowne uruchomienie jest zalecane po każdym uaktualnieniu agenta mobilności upewnij się, że wszystkie najnowsze zmiany są ładowane na maszynie źródłowej. Jest jednak **nie jest to konieczne**. Jeśli różnica między wersją agenta podczas ostatniego ponownego uruchomienia komputera i bieżącej wersji jest większa niż 4, ponowne uruchomienie jest obowiązkowy. Można znaleźć w poniższej tabeli przedstawiono szczegółowy opis.

|**Wersja agenta podczas ostatniego ponownego uruchomienia komputera.** | **Uaktualnianie do** | **Jest ponowny rozruch obowiązkowe?**|
|---------|---------|---------|
|9.16 |  9.18 | Nie jest to konieczne|
|9.16 | 9.19 | Nie jest to konieczne|
| 9.16 | 9.20 | Nie jest to konieczne
 | 9.16 | 9.21 | Tak, najpierw przeprowadzić uaktualnienie do 9,20, a następnie wykonaj ponowny rozruch przed uaktualnieniem do 9.21 jako różnica między wersjami (9.16, w którym wykonano ostatniego ponownego uruchomienia i wersji docelowej 9.21) jest > 4

## <a name="links-to-currently-supported-update-rollups"></a>Linki do pakietów zbiorczych aktualizacji obecnie obsługiwane

|Pakiet zbiorczy aktualizacji  |Dostawca  |Ujednoliconej konfiguracji| OVF  |MARS|
|---------|---------|---------|---------|--------|
|[Pakiet zbiorczy aktualizacji 36](https://support.microsoft.com/en-in/help/4503156)     |   5.1.4150.0  |  9.24.5211.1   |  5.1.4150.0  | 2.0.9160.0
|[Pakiet zbiorczy aktualizacji 35](https://support.microsoft.com/en-us/help/4494485/update-rollup-35-for-azure-site-recovery)     |   5.1.4000.0  |  9.23.5163.1   |  5.1.4000.0  | 2.0.9156.0
|[Aktualizuj pakiet zbiorczy 34](https://support.microsoft.com/en-us/help/4490016/update-rollup-34-for-azure-site-recovery) — poprawki     |   5.1.3950.0  |  9.22.5142.1   |  5.1.3950.0  | 2.0.9155.0
|[Pakiet zbiorczy aktualizacji 33](https://support.microsoft.com/en-us/help/4489582/update-rollup-33-for-azure-site-recovery)     |   5.1.3900.0  |  9.22.5109.1   |  5.1.3900.0  | 2.0.9155.0
|[Pakiet zbiorczy aktualizacji 32](https://support.microsoft.com/en-us/help/4485985/update-rollup-32-for-azure-site-recovery)     |   5.1.3800.0  |  9.21.5091.1   |  5.1.3800.0  |2.0.9144.0
|[Pakiet zbiorczy aktualizacji 31](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery)     |     5.1.3700.0      |   9.20.5051.1      |     5.1.3700.0    |2.0.9144.0

## <a name="previous-update-rollups"></a>Poprzednie pakiety zbiorcze aktualizacji

- [Pakiet zbiorczy aktualizacji 30](https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30)
- [Pakiet zbiorczy aktualizacji 29](https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery)
- [Pakiet zbiorczy aktualizacji 28](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery)
- [Pakiet zbiorczy aktualizacji 27](https://support.microsoft.com/help/4055712/update-rollup-27-for-azure-site-recovery)
- [Pakiet zbiorczy aktualizacji 26](https://support.microsoft.com/help/4344054/update-rollup-26-for-azure-site-recovery)  
- [Pakiet zbiorczy aktualizacji 25](https://support.microsoft.com/help/4278275/update-rollup-25-for-azure-site-recovery) 
- [Pakiet zbiorczy aktualizacji 23](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery) 
- [Pakiet zbiorczy aktualizacji 22](https://support.microsoft.com/help/4072852/update-rollup-22-for-azure-site-recovery) 
- [Pakiet zbiorczy aktualizacji 21](https://support.microsoft.com/help/4051380/update-rollup-21-for-azure-site-recovery) 
- [Pakiet zbiorczy aktualizacji 20](https://support.microsoft.com/help/4041105/update-rollup-20-for-azure-site-recovery) 
- [Pakiet zbiorczy aktualizacji 19](https://support.microsoft.com/help/4034599/update-rollup-19-for-azure-site-recovery) 
