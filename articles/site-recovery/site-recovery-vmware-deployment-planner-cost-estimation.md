---
title: Przejrzyj raport szacowania kosztów w planista wdrażania usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób przejrzeć raport szacowania kosztów w planista wdrażania usługi Azure Site Recovery dla oprogramowania VMware do odzyskiwania po awarii platformy Azure.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 3/14/2019
ms.author: mayg
ms.openlocfilehash: 8a36a80903a47bb4163666baf86ed8dac13a00de
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61471131"
---
# <a name="review-the-cost-estimation-report-in-the-site-recovery-deployment-planner-for-vmware-disaster-recovery-to-azure"></a>Przejrzyj raport szacowania kosztów w planista wdrażania usługi Site Recovery dla odzyskiwania po awarii programu VMware na platformę Azure

Raport planisty wdrożenia zawiera podsumowanie szacowania kosztów w arkuszach [Zalecenia](site-recovery-vmware-deployment-planner-analyze-report.md#recommendations) i szczegółową analizę kosztów w arkuszu szacowania kosztów. Szczegółowa analiza kosztów jest przygotowywana dla poszczególnych maszyn wirtualnych. 

>[!Note]
>Bieżącą wersję narzędzia planisty wdrażania nie udostępnia Szacowanie kosztów dla maszyn wirtualnych replikowanych do usługi Managed Disks.
>* Szacowane koszty odzyskiwania po awarii są takie same dla konta magazynu i dyski zarządzane, gdy parametr "Użyj dysków zarządzanych" ma wartość "Yes" w bloku "Obliczenia i sieć".
>* Aby uzyskać przybliżoną roczne szacowania kosztów dla replikacji, wprowadź następujące ustawienia tymczasowego na **szacowania kosztów** arkusza:
>    * Ustaw dla parametru "Czas trwania dla kosztu" **ustawienia** tabeli, aby "Year"
>    * W **szczegółowe analiza kosztów** tabeli, do 12 należy ustawić kolumny, "Liczba awarii w roku" i "czas trwania każdej operacji testowania odzyskiwania po awarii (dni)"-30 
>    * Koszt replikacji będą wyglądać mniej więcej kosztów, w kolumnie Koszt usługi storage "R", czyli testowania odzyskiwania po awarii w danym roku **roczny koszt rocznie** podsekcji.

### <a name="cost-estimation-summary"></a>Podsumowanie szacowania kosztów 
Na wykresie przedstawiono podsumowanie szacowanych łącznych kosztów odzyskiwania po awarii (DR) na platformie Azure w wybranym regionie docelowym w walucie określonej na potrzeby generowania raportu.
Podsumowanie szacowania kosztów

![Podsumowanie szacowania kosztów](media/site-recovery-vmware-deployment-planner-analyze-report/cost-estimation-summary-v2a.png)

Podsumowanie pomaga zrozumieć ponoszone koszty magazynowania, obliczania, użycia sieci i licencji w przypadku ochrony wszystkich zgodnych maszyn wirtualnych na platformie Azure przy użyciu usługi Azure Site Recovery. Koszt jest obliczany dla zgodnych maszyn wirtualnych, a nie dla wszystkich profilowanych maszyn wirtualnych.  
 
Koszt można wyświetlić w rozliczeniu miesięcznym lub rocznym. Dowiedz się więcej o [obsługiwanych regionach docelowych](./site-recovery-vmware-deployment-planner-cost-estimation.md#supported-target-regions) i [obsługiwanych walutach](./site-recovery-vmware-deployment-planner-cost-estimation.md#supported-currencies).

**Koszt według składników** łączny koszt odzyskiwania po awarii jest dzielony na cztery składniki: Koszt licencji usługi obliczeniowe, Magazyn, sieci i usługi Azure Site Recovery. Koszt jest obliczany na podstawie wykorzystania powstałego podczas replikacji oraz w czasie testowania odzyskiwania po awarii dla mocy obliczeniowych, magazynu (warstwa Premium i Standardowa), usługi ExpressRoute/sieci VPN skonfigurowanej między lokacją lokalną i platformą Azure oraz dla licencji usługi Azure Site Recovery.

**Koszt według stanów**: łączny koszt odzyskiwania po awarii (DR) jest dzielony na kategorie na podstawie dwóch różnych stanów — replikacji i testowania odzyskiwania po awarii. 

**Koszt replikacji**:  Koszt, który zostanie naliczony podczas replikacji. Obejmuje on koszt magazynu, użycia sieci i licencji usługi Azure Site Recovery. 

**Koszt testowania odzyskiwania po awarii**: Koszt, który zostanie naliczony podczas testów pracy w trybie Failover. Usługa Azure Site Recovery uruchamia maszyny wirtualne podczas testu pracy w trybie failover. Koszt testowania odzyskiwania po awarii obejmuje koszt magazynu i mocy obliczeniowej działających maszyn wirtualnych. 

**Koszt magazynu platformy Azure na miesiąc/rok**: obejmuje łączny koszt magazynu, który zostanie naliczony dla magazynów w warstwie Premium i Standardowa podczas replikacji i testowania odzyskiwania po awarii.

## <a name="detailed-cost-analysis"></a>Szczegółowa analiza kosztów
Ceny platformy Azure dotyczące obliczeń, magazynu, sieci itp. różnią się w poszczególnych regionach platformy Azure. Możesz wygenerować raport szacowania kosztów z najnowszymi cenami platformy Azure w oparciu o subskrypcję, ofertę skojarzoną z subskrypcją i dla określonego docelowego regionu świadczenia usługi Azure w konkretnej walucie. Domyślnie narzędzie używa regionu platformy Azure Zachodnie stany USA 2 i waluty USD (dolar amerykański). Jeśli użyto innego regionu i waluty, podczas następnego generowania raportu bez identyfikatora subskrypcji, identyfikatora oferty, regionu docelowego i waluty do szacowania kosztów zostaną zastosowane ceny z ostatniego użytego regionu docelowego i w ostatniej użytej walucie.
W tej sekcji przedstawiono identyfikator subskrypcji i identyfikator oferty, których użyto do generowania raportów.  Jeśli nie zostały użyte, jest ona pusta.

W całym raporcie komórki oznaczone kolorem szarym są przeznaczone tylko do odczytu. Komórki białe można modyfikować zgodnie z wymaganiami.

![Podsumowanie szacowania kosztów 1](media/site-recovery-hyper-v-deployment-planner-cost-estimation/cost-estimation1-h2a.png)

### <a name="overall-dr-cost-by-components"></a>Ogólny koszt odzyskiwania po awarii według składników
W pierwszej sekcji przedstawiono ogólny koszt odzyskiwania według składników i koszt odzyskiwania po awarii według stanów. 

**Obliczenia**: Koszt maszyn wirtualnych IaaS uruchamianych na platformie Azure na potrzeby odzyskiwania po awarii. Obejmuje on maszyny wirtualne tworzone przez usługę Azure Site Recovery podczas testowania odzyskiwania po awarii (testowanie pracy w trybie failover) oraz maszyny wirtualne działające na platformie Azure, takie jak program SQL Server z zawsze włączonymi grupami dostępności i kontrolerami domeny/serwerami nazw domen.

**Magazyn**: Koszt użycia magazynu platformy Azure na potrzeby odzyskiwania po awarii wymaga. Obejmuje on użycie magazynu na potrzeby replikacji oraz podczas testowania odzyskiwania po awarii.
Network: Usługi ExpressRoute i sieci VPN między lokacjami, koszt na potrzeby odzyskiwania po awarii. 

**Licencja usługi ASR**: Koszt licencji usługi Azure Site Recovery dla wszystkich zgodnych maszyn wirtualnych. Jeśli w tabeli szczegółowej analizy kosztów ręcznie wprowadzono maszynę wirtualną, koszt licencji usługi Azure Site Recovery jest również uwzględniany w przypadku tej maszyny wirtualnej.

### <a name="overall-dr-cost-by-states"></a>Ogólny koszt odzyskiwania po awarii według stanów
Łączny koszt odzyskiwania po awarii jest dzielony na kategorie na podstawie dwóch różnych stanów — replikacji i testowania odzyskiwania po awarii.

**Koszt replikacji**: Koszt generowany podczas replikacji. Obejmuje on koszt magazynu, użycia sieci i licencji usługi Azure Site Recovery. 

**Koszt testowania odzyskiwania po awarii**: Koszt generowany podczas testowania odzyskiwania po awarii. Usługa Azure Site Recovery uruchamia maszyny wirtualne podczas testowania odzyskiwania po awarii. Koszt testowania odzyskiwania po awarii obejmuje koszt magazynu i mocy obliczeniowej działających maszyn wirtualnych.
Łączny czas trwania testowania odzyskiwania po awarii = Liczba operacji testowania odzyskiwania po awarii x Czas trwania każdej operacji testowania odzyskiwania po awarii (w dniach). Średni koszt operacji testowania odzyskiwania po awarii (na miesiąc) = Łączny koszt testowania odzyskiwania operacji/12

### <a name="storage-cost-table"></a>Tabela kosztów magazynu:
W tej tabeli przedstawiono koszt magazynu w warstwie Premium i Standardowa ponoszony podczas replikacji i testowania odzyskiwania po awarii.

### <a name="site-to-azure-network"></a>Sieć między lokacją i platformą Azure
Wybierz odpowiednie ustawienia zgodnie z wymaganiami. 

**ExpressRoute**: Domyślnie narzędzie wybiera najbliższy plan usługi ExpressRoute, który odpowiada wymagana przepustowość sieci dla replikacji różnicowej. Plan można zmienić zgodnie z potrzebami.

**VPN Gateway**: Wybierz bramę sieci VPN, jeśli w danym środowisku. Wartość domyślna to Nie dotyczy.

**Region docelowy**: Określony region platformy Azure do odzyskiwania po awarii. Cena użyta w raporcie na potrzeby obliczania, magazynu, sieci i licencji opiera się na cenniku platformy Azure dla tego regionu. 

### <a name="vm-running-on-azure"></a>Maszyna wirtualna działająca na platformie Azure
Jeśli na potrzeby odzyskiwania po awarii na platformie Azure działa maszyna wirtualna kontrolera domeny lub systemu DNS albo maszyna wirtualna programu SQL Server z zawsze włączonymi grupami dostępności, musisz podać liczbę i rozmiar maszyn wirtualnych w celu uwzględnienia ich kosztu obliczeń w łącznym koszcie odzyskiwania po awarii. 

### <a name="apply-overall-discount-if-applicable"></a>Stosowanie rabatu ogólnego (jeśli ma zastosowanie)
Jeśli jesteś partnerem platformy Azure lub klientem i masz uprawnienia do dowolnego rabatu powiązanego z ogólnymi cenami platformy Azure, możesz użyć tego pola. Narzędzie stosuje rabat (w %) do wszystkich składników.

### <a name="number-of-virtual-machines-type-and-compute-cost-per-year"></a>Liczba maszyn wirtualnych — typ i koszt obliczeń (na rok)
W tej tabeli przedstawiono liczbę maszyn wirtualnych z systemem Windows i maszyn wirtualnych z systemem innym niż Windows oraz ich koszt obliczeń na potrzeby testowania odzyskiwania po awarii.

### <a name="settings"></a>Ustawienia 
**Użycie dysku zarządzanego**: Określa, czy dysk zarządzany jest używany w czasie testowania odzyskiwania po awarii. Wartość domyślna to Tak. Jeśli opcja -UseManagedDisks zostanie ustawiona na Nie, do obliczenia kosztów narzędzie użyje ceny dysku niezarządzanego.

**Waluta**: Waluta, w którym jest generowany raport. Czas trwania kosztów:  Można wyświetlić wszystkie koszty dla miesiąca lub całego roku. 

## <a name="detailed-cost-analysis-table"></a>Tabela szczegółowej analizy kosztów
![Szczegółowa analiza kosztów](media/site-recovery-hyper-v-deployment-planner-cost-estimation/detailed-cost-analysis-h2a.png) W tabeli przedstawiono podział kosztów dla każdej zgodnej maszyny wirtualnej. Przy użyciu tej tabeli można również zidentyfikować szacowany koszt odzyskiwania po awarii na platformie Azure dla nieprofilowanych maszyn wirtualnych, ręcznie dodając maszyny wirtualne. Jest to przydatne, jeśli trzeba oszacować koszty platformy Azure na potrzeby nowego wdrożenia odzyskiwania po awarii bez przeprowadzania szczegółowego profilowania.
Aby ręcznie dodać maszyny wirtualne: 
1.  Kliknij przycisk „Wstaw wiersz”, aby wstawić nowy wiersz między wierszem początkowym i końcowym.

2.  Wypełnij następujące kolumny w oparciu o przybliżony rozmiar maszyny wirtualnej i liczbę maszyn wirtualnych, które odpowiadają tej konfiguracji: 

* Liczba maszyn wirtualnych, rozmiar IaaS (wybór)
* Typ magazynu (Standardowa/Premium)
* Łączny rozmiar magazynu maszyny wirtualnej (GB)
* Liczba operacji testowania odzyskiwania po awarii w roku 
* Czas trwania każdej operacji testowania odzyskiwania po awarii (dni) 
* Typ systemu operacyjnego
* Nadmiarowość danych 
* Korzyść użycia hybrydowego platformy Azure

1. Tę samą wartość można zastosować do wszystkich maszyn wirtualnych w tabeli, klikając przycisk „Zastosuj do wszystkich” dla kolumn Liczba operacji testowania odzyskiwania po awarii w roku, Czas trwania każdej operacji testowania odzyskiwania po awarii (dni), Nadmiarowość danych i Korzyść użycia hybrydowego platformy Azure.

1. Kliknij pozycję „Oblicz ponownie koszt”, aby zaktualizować koszt.

**Nazwa maszyny Wirtualnej**: Nazwa maszyny Wirtualnej.

**Liczba maszyn wirtualnych**: Liczba maszyn wirtualnych, które zgodna z konfiguracją. Możesz zaktualizować liczbę istniejących maszyn wirtualnych, jeśli maszyny wirtualne o podobnej konfiguracji nie są profilowane, ale będą chronione.

**Rozmiar IaaS (zalecenie)**: Jest to rozmiar roli zgodnej maszyny wirtualnej zalecany przez narzędzie. 

**Rozmiar IaaS (wybór)**: Domyślnie jest taki sam jak zalecany rozmiar roli maszyny Wirtualnej. Rolę można zmienić zgodnie z wymaganiami. Koszt obliczeń opiera się na wybranym rozmiarze roli maszyny wirtualnej.

**Typ magazynu**: Typ magazynu, który jest używany przez maszynę Wirtualną. Jest to magazyn w warstwie Standardowa lub Premium.

**Maszyna wirtualna łączny rozmiar magazynu (GB)**: Całkowita wielkość magazynu maszyny Wirtualnej.

**Liczba awarii w roku**: Liczba wykonań testowania odzyskiwania po awarii w roku. Wartość domyślna to 4 razy w roku. Można zmodyfikować okres dla wybranych maszyn wirtualnych lub zastosować nową wartość do wszystkich maszyn wirtualnych, wprowadzając nową wartość w górnym wierszu i klikając przycisk „Zastosuj do wszystkich”. Liczba operacji testowania odzyskiwania po awarii w ciągu roku i czasu trwania poszczególnych okresów testowania odzyskiwania po awarii jest obliczany łączny koszt odzyskiwania po awarii.  

**Czas trwania każdej operacji testowania odzyskiwania po awarii (dni)**: Czas trwania każdej operacji testowania odzyskiwania po awarii. Wartość domyślna to 7 dni co 90 dni zgodnie z [korzyścią odzyskiwania po awarii w pakiecie Software Assurance](https://azure.microsoft.com/pricing/details/site-recovery). Można zmodyfikować okres dla wybranych maszyn wirtualnych lub zastosować nową wartość do wszystkich maszyn wirtualnych, wprowadzając nową wartość w górnym wierszu i klikając przycisk „Zastosuj do wszystkich”. Łączny koszt odzyskiwania po awarii jest obliczany w oparciu o liczbę operacji testowania odzyskiwania po awarii w ciągu roku i czasu trwania poszczególnych okresów testowania odzyskiwania po awarii.
  
**Typ systemu operacyjnego**: Typ systemu operacyjnego maszyny Wirtualnej. Może być to system Windows lub Linux. Jeśli typ systemu operacyjnego to Windows, do tej maszyny wirtualnej można zastosować korzyść użycia hybrydowego platformy Azure. 

**Nadmiarowość danych**: Może to być jedna z następujących wartości — magazyn lokalnie nadmiarowy (LRS), Magazyn geograficznie nadmiarowy (GRS) lub magazyn geograficznie nadmiarowy dostęp do odczytu (RA-GRS). Wartość domyślna to LRS. Można zmienić typ w oparciu o konto magazynu dla określonych maszyn wirtualnych lub zastosować nowy typ do wszystkich maszyn wirtualnych, klikając przycisk „Zastosuj do wszystkich”.  Koszt magazynu na potrzeby replikacji jest obliczany na podstawie ceny wybranej nadmiarowości danych. 

**Korzyść użycia hybrydowego platformy Azure**: Korzyść użycia hybrydowego platformy Azure można zastosować do maszyn wirtualnych Windows, jeśli ma to zastosowanie.  Wartość domyślna to Yes. Można zmienić ustawienie dla określonych maszyn wirtualnych lub zaktualizować wszystkie maszyny wirtualne, klikając przycisk „Zastosuj do wszystkich”.

**Łączne użycie platformy Azure**: Danym odzyskiwaniem po awarii obejmuje koszt licencji usługi Azure Site Recovery obliczeń, magazynu i. W oparciu o wybraną opcję jest przedstawiany koszt miesięczny lub roczny.

**Koszt replikacji w stanie stabilnym**: Obejmuje on koszt magazynu na potrzeby replikacji.

**Łączny koszt testowania odzyskiwania po awarii (średni)**: Zawiera zasoby obliczeniowe i Magazyn koszt testowania odzyskiwania po awarii.

**Koszt licencji usługi ASR**: Koszt licencji usługi Azure Site Recovery.

## <a name="supported-target-regions"></a>Obsługiwane regiony docelowe
Planista wdrożenia usługi Azure Site Recovery udostępnia szacowanie kosztów dla poniższych regionów platformy Azure. Jeśli ta lista nie zawiera Twojego regionu, możesz użyć dowolnego spośród następujących regionów, którego cena jest najbardziej zbliżona do Twojego.

eastus, eastus2, westus, centralus, northcentralus, southcentralus, northeurope, westeurope, eastasia, southeastasia, japaneast, japanwest, australiaeast, australiasoutheast, brazilsouth, southindia, centralindia, westindia, canadacentral, canadaeast, westus2, westcentralus, uksouth, ukwest, koreacentral, koreasouth 

## <a name="supported-currencies"></a>Obsługiwane waluty
Planista wdrażania usługi Azure Site Recovery może wygenerować raport kosztów z użyciem dowolnego spośród poniższych walut.

|Waluta|Name (Nazwa)||Waluta|Name (Nazwa)||Waluta|Name (Nazwa)|
|---|---|---|---|---|---|---|---|
|ARS|Peso argentyńskie ($)||AUD|Dolar australijski ($)||BRL|Real brazylijski (R$)|
|CAD|Dolar kanadyjski ($)||CHF|Frank szwajcarski (chf)||DKK|Korona duńska (kr)|
|EUR|Euro (€)||GBP|Funt brytyjski (£)||HKD|Dolar hongkoński (HK$)|
|IDR|Rupia indonezyjska (Rp)||INR|Rupia indyjska (₹)||JPY|Jen japoński (¥)|
|KRW|Won koreański (₩)||MXN|Peso meksykańskie (MXN$)||MYR|Ringgit malezyjski (RM$)|
|NOK|Korona norweska (kr)||NZD|Dolar nowozelandzki ($)||RUB|Rubel rosyjski (руб)|
|SAR|Rial saudyjski (SR)||SEK|Korona szwedzka (kr)||TWD|Dolar tajwański (NT$)|
|TRY|Lira turecka (TL)||USD| Dolar amerykański ($)||ZAR|Rand RPA (R)|

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej na temat ochrony [maszyn wirtualnych VMware na platformie Azure przy użyciu usługi Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/tutorial-vmware-to-azure).
