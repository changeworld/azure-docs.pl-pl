---
title: Azure lokacji odzyskiwania wdrożenia Planistę koszt szczegóły Szacowanie funkcji Hyper-V-do Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano szczegółów szacowania kosztów wygenerowanych przy użyciu funkcji Hyper-v do platformy Azure scenariusza Azure lokacji odzyskiwania wdrożenia Planistę raportu.
services: site-recovery
author: nsoneji
manager: garavd
ms.service: site-recovery
ms.topic: article
ms.date: 02/14/2018
ms.author: nisoneji
ms.openlocfilehash: 31461e70e81f0f48a8d67e31b98cfae2dd627a54
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/22/2018
ms.locfileid: "29467177"
---
# <a name="cost-estimation-report-by-azure-site-recovery-deployment-planner"></a>Szacowanie kosztów raport według Azure lokacji odzyskiwania wdrożenia Planistę 

Raport planowania wdrożenia Azure lokacji odzyskiwania zawiera podsumowania w szacowania kosztów [zalecenia](hyper-v-deployment-planner-analyze-report.md#recommendations) arkusze i analizy kosztów szczegółowe w arkuszu szacowania kosztów. Szczegółowa analiza kosztów jest przygotowywana dla poszczególnych maszyn wirtualnych. 

### <a name="cost-estimation-summary"></a>Podsumowanie szacowania kosztów 
Wykres przedstawia widok podsumowania koszt odzyskiwania (DR) szacowany po całkowitej awarii na platformie Azure w Twoim regionie wybranego celu i walut, określoną w celu wygenerowania raportu.

![Podsumowanie szacowania kosztów](media/hyper-v-azure-deployment-planner-cost-estimation/cost-estimation-summary-h2a.png)

Podsumowanie pomaga zrozumieć koszt, który trzeba płacić za magazyn, obliczeniowych, sieci i licencji zgodne maszyn wirtualnych w przypadku ochrony za pomocą usługi Azure Site Recovery. Koszt jest obliczany zgodne maszyn wirtualnych, a nie na wszystkich PROFILOWANEGO maszyn wirtualnych. 
 
Koszt można wyświetlić w rozliczeniu miesięcznym lub rocznym. Dowiedz się więcej o [obsługiwanych regionach docelowych](./hyper-v-deployment-planner-cost-estimation.md#supported-target-regions) i [obsługiwanych walutach](./hyper-v-deployment-planner-cost-estimation.md#supported-currencies).

**Koszt przez składniki**: całkowity koszt odzyskiwania po awarii jest podzielony na cztery składniki: obliczeń, magazynu, sieci i usługi Site Recovery licencji kosztów. Koszt jest obliczany na podstawie zużycia, która powstaje podczas replikacji i w czasie wyszczególniania odzyskiwania po awarii. Obliczeń, magazynu (premium i standardowa) ExpressRoute/VPN skonfigurowano między lokacją lokalną i platformy Azure i licencji usługi Site Recovery są używane do obliczeń.

**Koszt przez Państwa**: kategorii odzyskiwania (DR) po całkowitej awarii jest oparta na dwa różne stany: replikacji i awarii. 

**Koszt replikacji**: koszt powstaje podczas replikacji. Obejmuje ona kosztów licencji usługi Site Recovery, sieci i magazynu. 

**Przechodzenie do szczegółów DR koszt**: koszt powstaje podczas testu pracy w trybie Failover. Usługa Site Recovery obraca się maszyn wirtualnych podczas testowania trybu failover. Koszt wyszczególniania odzyskiwania po awarii obejmuje kosztów uruchomionych maszyn wirtualnych obliczeniowej i pamięci masowej. 

**Koszt magazynu Azure na miesiąc i rok**: koszt całkowita ilość miejsca, który poniesienia — wersja premium i standardowa magazynu dla replikacji i awarii.

## <a name="detailed-cost-analysis"></a>Szczegółowa analiza kosztów
Azure ceny dla zasobów obliczeniowych, magazynu i sieci różnią się w regionach platformy Azure. Można wygenerować raport szacowania kosztów z cen platformy Azure na podstawie Twojej subskrypcji, oferty skojarzone z subskrypcją, a określony docelowy element region platformy Azure w określonej walucie. Domyślnie narzędzie używa regionu platformy Azure Zachodnie stany USA 2 i waluty USD (dolar amerykański). Jeśli używasz innych regionu i walut, przy następnym Generowanie raportu bez Identyfikatora subskrypcji, identyfikator oferty region docelowy i walut, narzędzie używa ceny region docelowy ostatnio używane i oszacowanie kosztów waluty.

W tej sekcji przedstawiono identyfikator subskrypcji i identyfikator oferty, który został użyty do generowania raportów. Jeśli nie są używane, jest on pusty.

W raporcie całego oznaczone szare komórki są tylko do odczytu. Komórek w oficjalnym można zmodyfikować zgodnie z wymaganiami.

![Szczegóły oszacowanie kosztów](media/hyper-v-azure-deployment-planner-cost-estimation/cost-estimation1-h2a.png)

### <a name="overall-dr-costs-by-components"></a>Ogólne koszty DR przez składniki
W pierwszej sekcji przedstawiono ogólny koszt odzyskiwania według składników i koszt odzyskiwania po awarii według stanów. 

**Obliczenia bazy danych**: kosztów maszyn wirtualnych IaaS, które działają w Azure na potrzeby odzyskiwania po awarii. Obejmuje on maszyn wirtualnych, które są tworzone przez usługę Site Recovery podczas ćwiczeń DR (test trybu failover). Zawiera również maszyn wirtualnych na platformie Azure, takich jak SQL Server z zawsze włączonych grup dostępności i kontrolery domeny lub serwery nazw domen.

**Magazyn**: koszt zużycia magazynu Azure na potrzeby odzyskiwania po awarii. Obejmuje on użycie magazynu na potrzeby replikacji oraz podczas testowania odzyskiwania po awarii.

**Sieci**: ExpressRoute i sieci VPN typu lokacja lokacja koszt na potrzeby odzyskiwania po awarii. 

**Funkcja automatycznego odzyskiwania systemu licencji**: usługi Site Recovery kosztów licencji dla wszystkich maszyn wirtualnych zgodne. Jeśli wprowadzone ręcznie Maszynę wirtualną w tabeli analizy kosztów szczegółowe, licencji usługi Site Recovery koszt również jest dołączony do tej maszyny Wirtualnej.

### <a name="overall-dr-costs-by-states"></a>Ogólne koszty DR przez Państwa
Łączny koszt odzyskiwania po awarii jest skategoryzowany oparte na dwa różne stany: replikacji i awarii.

**Replikacja**: kosztów ponoszonych podczas replikacji. Obejmuje ona kosztów licencji usługi Site Recovery, sieci i magazynu. 

**Przechodzenie do szczegółów DR**: kosztów ponoszonych w czasie odzyskiwania po awarii ćwiczeń. Podczas odzyskiwania po awarii rozwija się maszyny wirtualne obraca usługi Site Recovery. Odzyskiwania po awarii — Przechodzenie do szczegółów kosztów koszt zasobów obliczeniowych i magazynowania obejmuje uruchomionych maszyn wirtualnych.

* Czas trwania Przechodzenie do szczegółów DR razem w roku = liczba ćwiczenia DR x każdego czas trwania wyszczególniania odzyskiwania po awarii (dni)
* Przechodzenie do szczegółów DR średni koszt (miesięcznie) = całkowity koszt wyszczególniania odzyskiwania po awarii / 12

### <a name="storage-cost-table"></a>Tabela magazynu koszt
W poniższej tabeli zamieszczono premium i koszty magazynowania standardowe dla replikacji i rozwija odzyskiwania po awarii z włączonymi i wyłączonymi rabatów.

### <a name="site-to-azure-network"></a>Sieć między lokacją i platformą Azure
Wybierz odpowiednie ustawienie zgodnie z wymaganiami. 

**ExpressRoute**: domyślnie narzędzie wybiera najbliższy plan usługi ExpressRoute, który odpowiada wymaganej przepustowości sieci na potrzeby replikacji różnicowej. Możesz zmienić plan zgodnie z wymaganiami.

**Brama sieci VPN typu**: Wybieranie bramy sieci VPN platformy Azure, jeśli w danym środowisku. Wartość domyślna to Nie dotyczy.

**Region docelowy**: regionu Azure określony do odzyskiwania po awarii. Cena użyta w raporcie na potrzeby obliczania, magazynu, sieci i licencji opiera się na cenniku platformy Azure dla tego regionu. 

### <a name="vm-running-on-azure"></a>Maszyna wirtualna działająca na platformie Azure
Być może masz kontrolera domeny lub DNS maszyny Wirtualnej lub maszyny Wirtualnej programu SQL Server z zawsze włączonych grup dostępności działających na platformie Azure do odzyskiwania po awarii. Można podać liczbę maszyn wirtualnych, a rozmiar należy wziąć pod uwagę kosztów obliczeniowych w całkowity koszt odzyskiwania po awarii. 

### <a name="apply-overall-discount-if-applicable"></a>Stosowanie rabatu ogólnego (jeśli ma zastosowanie)
Jeśli w przypadku klienta lub partnera Azure i są uprawnione do rabatów na cennik ogólna platformy Azure, można użyć tego pola. Narzędzie stosuje rabat (w procentach) dla wszystkich składników.

### <a name="number-of-virtual-machines-type-and-compute-cost-per-year"></a>Liczba maszyn wirtualnych — typ i koszt obliczeń (na rok)
Ta tabela pokazuje liczbę systemu Windows i VMs z systemem innym niż Windows i wyszczególniania odzyskiwania po awarii należy obliczyć koszt dla nich.

### <a name="settings"></a>Ustawienia 
**Przy użyciu dysku zarządzanego**: to ustawienie określa, czy używa dysków zarządzanych w czasie odzyskiwania po awarii ćwiczeń. Wartość domyślna to **tak**. Jeśli ustawisz **- UseManagedDisks** do **nr**, ceny niezarządzane dysku służy do obliczania kosztów.

**Waluta**: waluta, w którym jest generowany raport.

**Czas trwania koszt**: można wyświetlić wszystkie koszty dla miesiąca lub cały rok. 

## <a name="detailed-cost-analysis-table"></a>Tabela szczegółowej analizy kosztów
![Szczegółowa analiza kosztów](media/hyper-v-azure-deployment-planner-cost-estimation/detailed-cost-analysis-h2a.png)

W tabeli wymieniono podziału kosztów dla każdej zgodne maszyny Wirtualnej. Za pomocą tej tabeli można również uzyskać szacowany koszt Azure DR nonprofiled maszyn wirtualnych przez ręczne dodanie maszyn wirtualnych. Informacje te są przydatne w sytuacjach, w których należy oszacować Azure koszty nowego wdrożenia odzyskiwania po awarii bez szczegółowe profilowania.

Aby ręcznie dodać maszyny wirtualne:

1. Wybierz **Wstaw wiersz** Aby wstawić nowy wiersz między **Start** i **zakończenia** wierszy.

2. Wypełnij następujące kolumny na podstawie Przybliżony rozmiar maszyny Wirtualnej i liczbę maszyn wirtualnych, które odpowiada tej konfiguracji: 

    a. **Liczba maszyn wirtualnych**

    b. **Rozmiar IaaS (wybór)**

    c. **Typ magazynu Standard/Premium**

    d. **Maszyna wirtualna całkowitego rozmiaru magazynu (GB)**

    e. **Liczba ćwiczenia odzyskiwania po awarii w roku**

    f. **Czas trwania każdego wyszczególniania odzyskiwania po awarii (dni)**

    g. Typ systemu operacyjnego

    h. **Nadmiarowość danych**

    i. **Korzyści Użyj hybrydowe platformy Azure**

3. Na wszystkich maszynach wirtualnych w tabeli można zastosować tę samą wartość, wybierając **Zastosuj do wszystkich** dla **numer programu DR-ćwiczenia w roku**, **każdy wyszczególniania odzyskiwania po awarii, czas trwania (w dniach)**, **danych nadmiarowość**, i **Azure hybrydowego Użyj korzyści**.

4. Wybierz **ponownie obliczyć koszt** zaktualizować kosztów.

**Nazwa maszyny wirtualnej**: nazwa maszyny wirtualnej.

**Liczba maszyn wirtualnych**: liczba maszyn wirtualnych, które są zgodne z konfiguracją. Należy zaktualizować liczbę istniejących maszyn wirtualnych, jeśli podobnej konfiguracji maszyn wirtualnych nie jest profilowane, ale chronione.

**Rozmiar IaaS (zalecane)**: rozmiaru roli maszyny Wirtualnej zgodne maszyny wirtualnej zalecane przez narzędzie. 

**Rozmiar IaaS (wybór)**: domyślny rozmiar jest taka sama jak zalecane rozmiaru roli maszyny Wirtualnej. Rolę można zmienić zgodnie z wymaganiami. Koszt obliczeń opiera się na wybranym rozmiarze roli maszyny wirtualnej.

**Typ magazynu**: typ magazynu, który jest używany przez maszynę Wirtualną. Jest magazynu standard lub premium.

**Łączny rozmiar magazynu maszyny wirtualnej (GB)**: całkowita wielkość magazynu maszyny wirtualnej.

**Liczba ćwiczenia odzyskiwania po awarii w roku**: liczba wykonać odzyskiwania po awarii bardziej szczegółowy w roku. Domyślnie jest cztery razy w roku. Można zmodyfikować okresu dla określonych maszyn wirtualnych lub zastosować nową wartość do wszystkich maszyn wirtualnych. Wprowadź nową wartość w górnym wierszu, a następnie wybierz **Zastosuj do wszystkich**. Na podstawie liczby ćwiczenia odzyskiwania po awarii w roku i każdego wyszczególniania odzyskiwania po awarii okresie trwania, jest obliczana całkowity koszt wyszczególniania odzyskiwania po awarii. 

**Czas trwania każdego wyszczególniania odzyskiwania po awarii (dni)**: czas trwania każdego wyszczególniania odzyskiwania po awarii. Domyślnie jest 7 dni co 90 dni zgodnie z [korzyści awaryjnego odzyskiwania Software Assurance](https://azure.microsoft.com/en-in/pricing/details/site-recovery). Można zmodyfikować okresu dla określonych maszyn wirtualnych albo można zastosować nową wartość do wszystkich maszyn wirtualnych. Wprowadź nową wartość w górnym wierszu, a następnie wybierz **Zastosuj do wszystkich**. Łączny koszt wyszczególniania odzyskiwania po awarii jest obliczany na podstawie liczby ćwiczenia odzyskiwania po awarii w roku i każdego wyszczególniania odzyskiwania po awarii okresie trwania.
 
**Typ systemu operacyjnego**: typ systemu operacyjnego (OS) maszyny wirtualnej. Jest Windows lub Linux. Typ systemu operacyjnego w przypadku systemu Windows, korzyści Użyj hybrydowe usługi Azure może odnosić się do tej maszyny Wirtualnej. 

**Nadmiarowość danych**: może to być magazyn lokalnie nadmiarowy, magazynu geograficznie nadmiarowego lub magazynu geograficznie nadmiarowego dostęp do odczytu. Wartość domyślna to magazyn lokalnie nadmiarowy. Można zmienić typu oparte na koncie magazynu dla maszyn wirtualnych w określonej lub nowego typu można zastosować do wszystkich maszyn wirtualnych. Zmień typ górnym wierszu, a następnie wybierz **Zastosuj do wszystkich**. Koszt magazynowania replikacji jest obliczana na podstawie ceny nadmiarowość danych, które wybrano. 

**Azure korzyści Użyj hybrydowego**: korzyści Użyj hybrydowe platformy Azure można stosować do maszyn wirtualnych systemu Windows, jeśli ma to zastosowanie. Wartość domyślna to **tak**. Możesz zmienić ustawienie dla określonego maszyn wirtualnych lub zaktualizować wszystkich maszyn wirtualnych. Wybierz **Zastosuj do wszystkich**.

**Łączna liczba wykorzystania platformy Azure**: obliczeniowych, magazynu i kosztów licencji usługi Site Recovery dla Twojego odzyskiwania po awarii. W zależności od dokonanego, przedstawia on koszt miesięcznego lub rocznego.

**Koszt replikacji stanie stabilności**: koszt magazynowania dla replikacji.

**Całkowity koszt wyszczególniania odzyskiwania po awarii (średnia)**: koszt zasobów obliczeniowych i magazynu dla ćwiczenia odzyskiwania po awarii.

**Funkcja automatycznego odzyskiwania systemu kosztów licencji**: kosztów licencji usługi Site Recovery.

## <a name="supported-target-regions"></a>Obsługiwane regiony docelowe
Planowanie wdrożenia odzyskiwania lokacji zawiera szacowania kosztów dla następujących regionach platformy Azure. Jeśli obszar nie ma na liście w tym miejscu, można użyć dowolnego z następujących regionach, których ceny znajduje się najbliżej regionu:

eastus, eastus2, westus, centralus, northcentralus, southcentralus, northeurope, westeurope, eastasia, southeastasia, japaneast, japanwest, australiaeast, australiasoutheast, brazilsouth, southindia, centralindia, westindia, canadacentral, canadaeast, westus2, westcentralus, uksouth, ukwest, koreacentral, koreasouth 

## <a name="supported-currencies"></a>Obsługiwane waluty
Planowanie wdrożenia odzyskiwania lokacji można wygenerować raport koszt za pomocą dowolnego z następujących waluty.

|Waluta|Name (Nazwa)||Waluta|Name (Nazwa)||Waluta|Name (Nazwa)|
|---|---|---|---|---|---|---|---|
|ARS|Peso argentyńskie ($)||AUD|Australian dolara ($)||BRL|Brazylia rzeczywistym (R$)|
|CAD|Kanadyjscy dolara ($)||CHF|Frank Szwajcarii (chf)||DKK|Duńska (kr)|
|EUR|Euro (€)||GBP|Funt brytyjski (l)||HKD|Hongkong dolara (HK$)|
|IDR|Rupia indonezyjska (Rp)||INR|Rupia indyjskiego (₹)||JPY|Jen japoński (y)|
|KRW|Koreański won (₩)||MXN|Peso meksykański (MX$)||MYR|Ringgit malezyjski (RM$)|
|NOK|Norweska (kr)||NZD|Nowa Zelandia dolara ($)||RUB|Rubel rosyjski (руб)|
|SAR|Saudi riyal (SR)||SEK|Szwedzka (kr)||TWD|Tajwańskiej dolara (NT$)|
|TRY|Lir turecki (TL)||USD| Dolara ($)||ZAR|Rand Południowej Afryki (R)|

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej na temat sposobu ochrony [maszyn wirtualnych funkcji Hyper-V do platformy Azure przy użyciu usługi Site Recovery](hyper-v-azure-tutorial.md).
