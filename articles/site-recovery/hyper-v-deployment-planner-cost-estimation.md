---
title: Przejrzyj raport szacowania do odzyskiwania po awarii maszyn wirtualnych funkcji Hyper-V na platformę Azure koszty planista wdrażania usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób przeglądania koszt szacowania wygenerowany raport Planisty wdrożenia usługi Azure Site Recovery dla funkcji Hyper-V odzyskiwania po awarii na platformie Azure.
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/9/2019
ms.author: mayg
ms.openlocfilehash: bced6a9e6c59dc32657dbabef986e29e0447b28b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60947230"
---
# <a name="cost-estimation-report-by-azure-site-recovery-deployment-planner"></a>Raport szacowania kosztów przez Planistę wdrażania usługi Azure Site Recovery 

Raport Planisty wdrażania usługi Azure Site Recovery zawiera podsumowanie szacowania kosztów w arkuszach [Zalecenia](hyper-v-deployment-planner-analyze-report.md#recommendations) i szczegółową analizę kosztów w arkuszu szacowania kosztów. Szczegółowa analiza kosztów jest przygotowywana dla poszczególnych maszyn wirtualnych. 

### <a name="cost-estimation-summary"></a>Podsumowanie szacowania kosztów 
Na wykresie przedstawiono podsumowanie szacowanych łącznych kosztów odzyskiwania po awarii na platformie Azure w wybranym regionie docelowym w walucie określonej na potrzeby generowania raportu.

![Podsumowanie szacowania kosztów](media/hyper-v-azure-deployment-planner-cost-estimation/cost-estimation-summary-h2a.png)

Podsumowanie pomaga zrozumieć ponoszone koszty magazynowania, obliczania, użycia sieci i licencji w przypadku ochrony zgodnych maszyn wirtualnych przy użyciu usługi Azure Site Recovery. Koszt jest obliczany dla zgodnych maszyn wirtualnych, a nie dla wszystkich profilowanych maszyn wirtualnych. 
 
Koszt można wyświetlić w rozliczeniu miesięcznym lub rocznym. Dowiedz się więcej o [obsługiwanych regionach docelowych](./hyper-v-deployment-planner-cost-estimation.md#supported-target-regions) i [obsługiwanych walutach](./hyper-v-deployment-planner-cost-estimation.md#supported-currencies).

**Koszt według składników**: Łączny koszt odzyskiwania po awarii jest dzielony na cztery składniki: koszt licencji usługi obliczeniowe, Magazyn, sieci i Usługa Site Recovery. Koszt jest obliczany na podstawie wykorzystania powstałego podczas replikacji oraz w czasie testowania odzyskiwania po awarii. Do obliczeń są używane: moc obliczeniowa, magazyn (warstwa Premium i Standardowa), usługa ExpressRoute/sieć VPN skonfigurowana między lokacją lokalną i platformą Azure oraz licencja usługi Site Recovery.

**Koszt według stanów**: Kategoria kosztów odzyskiwania po całkowitej awarii opiera się na dwóch różnych stanów: replikacji i testowania odzyskiwania po awarii. 

**Koszt replikacji**: Koszt, który jest naliczany podczas replikacji. Obejmuje on koszt magazynu, użycia sieci i licencji usługi Site Recovery. 

**Koszt testowania odzyskiwania po awarii**: Koszt, który jest naliczany podczas testów pracy w trybie Failover. Usługa Site Recovery uruchamia maszyny wirtualne podczas testu pracy w trybie failover. Koszt testowania odzyskiwania po awarii obejmuje koszty magazynu i mocy obliczeniowej działających maszyn wirtualnych. 

**Koszt usługi Azure storage na miesiąc/rok**: Łączny koszt magazynu, który jest naliczany dla magazynu premium i standardowa podczas replikacji i testowania odzyskiwania po awarii.

## <a name="detailed-cost-analysis"></a>Szczegółowa analiza kosztów
Ceny platformy Azure dotyczące obliczeń, magazynu i sieci różnią się w poszczególnych regionach świadczenia usługi Azure. Możesz wygenerować raport szacowania kosztów z najnowszymi cenami platformy Azure w oparciu o subskrypcję, ofertę skojarzoną z subskrypcją i określony docelowy region świadczenia usługi Azure w konkretnej walucie. Domyślnie narzędzie używa regionu platformy Azure Zachodnie stany USA 2 i waluty USD (dolar amerykański). Jeśli używasz innego regionu i waluty, podczas następnego generowania raportu bez identyfikatora subskrypcji, identyfikatora oferty, regionu docelowego i waluty do szacowania kosztów narzędzie zastosuje ceny z ostatnio użytego regionu docelowego w ostatnio użytej walucie.

W tej sekcji przedstawiono identyfikator subskrypcji i identyfikator oferty, których użyto do generowania raportów. Jeśli nie są one używane, są puste.

W całym raporcie komórki oznaczone kolorem szarym są przeznaczone tylko do odczytu. Komórki białe można modyfikować zgodnie z wymaganiami.

![Szczegóły szacowania kosztów](media/hyper-v-azure-deployment-planner-cost-estimation/cost-estimation1-h2a.png)

### <a name="overall-dr-costs-by-components"></a>Ogólne koszty odzyskiwania po awarii według składników
W pierwszej sekcji przedstawiono ogólny koszt odzyskiwania według składników i koszt odzyskiwania po awarii według stanów. 

**Obliczenia**: Koszt maszyn wirtualnych IaaS uruchamianych na platformie Azure na potrzeby odzyskiwania po awarii. Obejmuje on maszyny wirtualne, które są tworzone przez usługę Site Recovery podczas testów odzyskiwania po awarii (testów trybu failover). Obejmuje również maszyny wirtualne uruchomione na platformie Azure, takie jak SQL Server z zawsze włączonymi grupami dostępności i kontrolerami domeny lub serwerami nazw domen.

**Magazyn**: Koszt użycia magazynu platformy Azure na potrzeby odzyskiwania po awarii. Obejmuje on użycie magazynu na potrzeby replikacji oraz podczas testowania odzyskiwania po awarii.

**Sieć**: Usługa ExpressRoute site-to-site VPN koszt i na potrzeby odzyskiwania po awarii. 

**Licencja na usługę Azure Site Recovery**: Koszt licencji Site Recovery dla wszystkich zgodnych maszyn wirtualnych. Jeśli w tabeli szczegółowej analizy kosztów ręcznie wprowadzono maszynę wirtualną, koszt licencji usługi Site Recovery również jest uwzględniany w przypadku tej maszyny wirtualnej.

### <a name="overall-dr-costs-by-states"></a>Ogólne koszty odzyskiwania po awarii według stanów
Łączny koszt odzyskiwania po awarii jest dzielony na kategorie na podstawie dwóch różnych stanów: replikacji i testowania odzyskiwania po awarii.

**Replikacja**: Koszt generowany podczas replikacji. Obejmuje on koszt magazynu, użycia sieci i licencji usługi Site Recovery. 

**DR-Drill**: Koszt generowany podczas testowania odzyskiwania po awarii. Usługa Site Recovery uruchamia maszyny wirtualne podczas testowania odzyskiwania po awarii. Koszt testowania odzyskiwania po awarii obejmuje koszt magazynu i mocy obliczeniowej działających maszyn wirtualnych.

* Łączny roczny czas trwania testowania odzyskiwania po awarii = Liczba operacji testowania odzyskiwania po awarii x Czas trwania każdej operacji testowania odzyskiwania po awarii (w dniach)
* Średni koszt operacji testowania odzyskiwania po awarii (na miesiąc) = Łączny koszt testowania odzyskiwania po awarii / 12

### <a name="storage-cost-table"></a>Tabela kosztów magazynu
W tej tabeli przedstawiono koszt magazynu w warstwie Premium i Standardowa ponoszony podczas replikacji i testowania odzyskiwania po awarii (z rabatami i bez).

### <a name="site-to-azure-network"></a>Sieć między lokacją i platformą Azure
Wybierz odpowiednie ustawienie zgodnie z wymaganiami. 

**ExpressRoute**: Domyślnie narzędzie wybiera najbliższy plan usługi ExpressRoute, który odpowiada wymagana przepustowość sieci dla replikacji różnicowej. Plan można zmienić odpowiednio do potrzeb.

**Typ bramy VPN Gateway**: Wybierz bramę Azure VPN Gateway, jeśli w danym środowisku. Wartość domyślna to Nie dotyczy.

**Region docelowy**: Określony region platformy Azure do odzyskiwania po awarii. Cena użyta w raporcie na potrzeby obliczania, magazynu, sieci i licencji opiera się na cenniku platformy Azure dla tego regionu. 

### <a name="vm-running-on-azure"></a>Maszyna wirtualna działająca na platformie Azure
Być może masz maszynę wirtualną z kontrolerem domeny lub systemem DNS bądź maszynę wirtualną z programem SQL Server z zawsze włączonymi grupami dostępności działającą na platformie Azure na potrzeby odzyskiwania po awarii. Możesz podać liczbę maszyn wirtualnych i rozmiar, aby uwzględnić koszty mocy obliczeniowej w łącznym koszcie odzyskiwania po awarii. 

### <a name="apply-overall-discount-if-applicable"></a>Stosowanie rabatu ogólnego (jeśli ma zastosowanie)
Jeśli jesteś klientem lub partnerem platformy Azure i masz uprawnienia do dowolnego rabatu w stosunku do ogólnych cen platformy Azure, możesz użyć tego pola. Narzędzie stosuje rabat (procentowy) do wszystkich składników.

### <a name="number-of-virtual-machines-type-and-compute-cost-per-year"></a>Liczba maszyn wirtualnych — typ i koszt obliczeń (na rok)
W tej tabeli przedstawiono liczbę maszyn wirtualnych z systemem Windows i maszyn wirtualnych z systemem innym niż Windows oraz ich koszt zasobów obliczeniowych na potrzeby testowania odzyskiwania po awarii.

### <a name="settings"></a>Ustawienia 
**Użycie dysku zarządzanego**: To ustawienie określa, czy dysk zarządzany jest używany w czasie testowania odzyskiwania po awarii. Wartość domyślna to **Tak**. Jeśli opcja **-UseManagedDisks** jest ustawiona na wartość**Nie**, do obliczenia kosztów służy cena dysku niezarządzanego.

**Waluta**: Waluta, w którym jest generowany raport.

**Czas trwania dla kosztu**: Można wyświetlić wszystkie koszty dla miesiąca lub całego roku. 

## <a name="detailed-cost-analysis-table"></a>Tabela szczegółowej analizy kosztów
![Szczegółowa analiza kosztów](media/hyper-v-azure-deployment-planner-cost-estimation/detailed-cost-analysis-h2a.png)

W tabeli przedstawiono podział kosztów dla każdej zgodnej maszyny wirtualnej. Przy użyciu tej tabeli można również zidentyfikować szacowany koszt odzyskiwania po awarii na platformie Azure dla nieprofilowanych maszyn wirtualnych, ręcznie dodając maszyny wirtualne. Te informacje są przydatne w sytuacji, gdy trzeba oszacować koszty platformy Azure na potrzeby nowego wdrożenia odzyskiwania po awarii bez przeprowadzania szczegółowego profilowania.

Aby ręcznie dodać maszyny wirtualne:

1. Wybierz pozycję **Wstaw wiersz**, aby wstawić nowy wiersz między wierszem **początkowym** i **końcowym**.

1. Wypełnij następujące kolumny w oparciu o przybliżony rozmiar maszyny wirtualnej i liczbę maszyn wirtualnych, które odpowiadają tej konfiguracji: 

    a. **Liczba maszyn wirtualnych**

    b. **Rozmiar IaaS (wybór)**

    c. **Typ magazynu (Standardowa/Premium)**

    d. **Łączny rozmiar magazynu maszyny wirtualnej (GB)**

    e. **Liczba operacji testowania odzyskiwania po awarii w roku**

    f. **Czas trwania każdej operacji testowania odzyskiwania po awarii (dni)**

    g. **Typ systemu operacyjnego**

    h. **Nadmiarowość danych**

    i. **Korzyść użycia hybrydowego platformy Azure**

1. Tę samą wartość można zastosować do wszystkich maszyn wirtualnych w tabeli, wybierając pozycję **Zastosuj do wszystkich** dla kolumn **Liczba operacji testowania odzyskiwania po awarii w roku**, **Czas trwania każdej operacji testowania odzyskiwania po awarii (dni)**, **Nadmiarowość danych** i **Korzyść użycia hybrydowego platformy Azure**.

1. Wybierz pozycję **Oblicz ponownie koszt**, aby zaktualizować koszt.

**Nazwa maszyny Wirtualnej**: Nazwa maszyny Wirtualnej.

**Liczba maszyn wirtualnych**: Liczba maszyn wirtualnych, które zgodna z konfiguracją. Możesz zaktualizować liczbę istniejących maszyn wirtualnych, jeśli maszyny wirtualne o podobnej konfiguracji nie są profilowane, ale chronione.

**Rozmiar IaaS (zalecenie)**: Rozmiar roli zgodnej maszyny Wirtualnej zalecany przez narzędzie. 

**Rozmiar IaaS (wybór)**: Domyślnie rozmiar jest taki sam jak zalecany rozmiar roli maszyny Wirtualnej. Rolę można zmienić zgodnie z wymaganiami. Koszt obliczeń opiera się na wybranym rozmiarze roli maszyny wirtualnej.

**Typ magazynu**: Typ magazynu, który jest używany przez maszynę Wirtualną. Jest to magazyn w warstwie Standardowa lub Premium.

**Maszyna wirtualna łączny rozmiar magazynu (GB)**: Całkowita wielkość magazynu maszyny Wirtualnej.

**Liczba awarii w roku**: Liczba wykonań testowania odzyskiwania po awarii w roku. Wartość domyślna to cztery razy w roku. Okres możesz zmodyfikować dla konkretnych maszyn wirtualnych lub zastosować nową wartość dla wszystkich maszyn wirtualnych. Wprowadź nową wartość w górnym wierszu, a następnie wybierz pozycję **Zastosuj do wszystkich**. Łączny koszt odzyskiwania po awarii jest obliczany na podstawie liczby operacji testowania odzyskiwania po awarii w ciągu roku i czasu trwania poszczególnych okresów testowania odzyskiwania po awarii. 

**Czas trwania każdej operacji testowania odzyskiwania po awarii (dni)**: Czas trwania poszczególnych operacji testowania odzyskiwania po awarii. Wartość domyślna to 7 dni co 90 dni zgodnie z [korzyścią odzyskiwania po awarii w pakiecie Software Assurance](https://azure.microsoft.com/pricing/details/site-recovery). Okres możesz zmodyfikować dla konkretnych maszyn wirtualnych lub zastosować nową wartość dla wszystkich maszyn wirtualnych. Wprowadź nową wartość w górnym wierszu, a następnie wybierz pozycję **Zastosuj do wszystkich**. Łączny koszt odzyskiwania po awarii jest obliczany w oparciu o liczbę operacji testowania odzyskiwania po awarii w ciągu roku i czas trwania poszczególnych okresów testowania odzyskiwania po awarii.
 
**Typ systemu operacyjnego**: Typ systemu operacyjnego (OS) maszyny Wirtualnej. Może to być system Windows lub Linux. Jeśli typ systemu operacyjnego to Windows, do tej maszyny wirtualnej można zastosować korzyść użycia hybrydowego platformy Azure. 

**Nadmiarowość danych**: Może być magazyn lokalnie nadmiarowy, Magazyn geograficznie nadmiarowy lub magazyn geograficznie nadmiarowy z dostępem do odczytu. Domyślnie jest to magazyn lokalnie nadmiarowy. Typ można zmienić w oparciu o konto magazynu dla określonych maszyn wirtualnych lub zastosować nowy typ do wszystkich maszyn wirtualnych. Zmień typ w górnym wierszu, a następnie wybierz pozycję **Zastosuj do wszystkich**. Koszt magazynu na potrzeby replikacji jest obliczany na podstawie ceny wybranej nadmiarowości danych. 

**Korzyści z używania hybrydowej platformy Azure**: Do maszyn wirtualnych Windows, można zastosować korzyść użycia hybrydowego platformy Azure, jeśli ma to zastosowanie. Wartość domyślna to **Tak**. Ustawienie możesz zmienić dla określonych maszyn wirtualnych lub zaktualizować wszystkie maszyny wirtualne. Wybierz pozycję **Zastosuj do wszystkich**.

**Łączne użycie platformy Azure**: Obliczeń, magazynu i koszt licencji usługi Site Recovery danym odzyskiwaniem po awarii. W oparciu o wybraną opcję jest przedstawiany koszt miesięczny lub roczny.

**Koszt replikacji w stanie stabilnym**: Koszt magazynu na potrzeby replikacji.

**Łączny koszt testowania odzyskiwania po awarii (średni)**: Zasoby obliczeniowe i Magazyn koszt testowania odzyskiwania po awarii.

**Koszt licencji usługi Azure Site Recovery**: Koszt licencji usługi Site Recovery.

## <a name="supported-target-regions"></a>Obsługiwane regiony docelowe
Planista wdrażania usługi Site Recovery udostępnia szacowanie kosztów dla poniższych regionów świadczenia usługi Azure. Jeśli ta lista nie zawiera Twojego regionu, możesz użyć dowolnego spośród następujących regionów, którego cena jest najbardziej zbliżona do Twojego:

eastus, eastus2, westus, centralus, northcentralus, southcentralus, northeurope, westeurope, eastasia, southeastasia, japaneast, japanwest, australiaeast, australiasoutheast, brazilsouth, southindia, centralindia, westindia, canadacentral, canadaeast, westus2, westcentralus, uksouth, ukwest, koreacentral, koreasouth 

## <a name="supported-currencies"></a>Obsługiwane waluty
Planista wdrażania usługi Site Recovery może wygenerować raport kosztów z użyciem dowolnej spośród poniższych walut.

|Waluta|Name (Nazwa)||Waluta|Name (Nazwa)||Waluta|Name (Nazwa)|
|---|---|---|---|---|---|---|---|
|ARS|Peso argentyńskie ($)||AUD|Dolar australijski ($)||BRL|Real brazylijski (R$)|
|CAD|Dolar kanadyjski ($)||CHF|Frank szwajcarski (chf)||DKK|Korona duńska (kr)|
|EUR|Euro (€)||GBP|Funt brytyjski (l)||HKD|Dolar hongkoński (HK$)|
|IDR|Rupia indonezyjska (Rp)||INR|Rupia indyjska (₹)||JPY|Jen japoński (y)|
|KRW|Won południowokoreański (₩)||MXN|Peso meksykańskie (MX$)||MYR|Ringgit malezyjski (RM$)|
|NOK|Korona norweska (kr)||NZD|Dolar nowozelandzki ($)||RUB|Rubel rosyjski (руб)|
|SAR|Rial saudyjski (SR)||SEK|Korona szwedzka (kr)||TWD|Dolar tajwański (NT$)|
|TRY|Lira turecka (TL)||USD| Dolar amerykański ($)||ZAR|Rand RPA (R)|

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej na temat sposobu ochrony [maszyn wirtualnych funkcji Hyper-V na platformie Azure przy użyciu usługi Site Recovery](hyper-v-azure-tutorial.md).
