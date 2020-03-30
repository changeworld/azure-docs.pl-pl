---
title: Wskazówki dotyczące cen & zarządzanie kosztami
description: Zawiera najlepsze rozwiązania dotyczące wybierania odpowiedniego modelu cenowego maszyny wirtualnej programu SQL Server.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/09/2018
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: b7a8f7136d0899ab3afe04bce2d25bc2e7f8d3e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75981727"
---
# <a name="pricing-guidance-for-azure-sql-server-vms"></a>Wskazówki dotyczące cen maszyn wirtualnych platformy Azure z programem SQL Server

Ten artykuł zawiera wskazówki dotyczące cen [dla maszyn wirtualnych programu SQL Server](virtual-machines-windows-sql-server-iaas-overview.md) na platformie Azure. Istnieje kilka opcji, które wpływają na koszt i ważne jest, aby wybrać odpowiedni obraz, który równoważy koszty z wymaganiami biznesowymi.

> [!TIP]
> Jeśli chcesz tylko znaleźć oszacowanie kosztów dla określonej kombinacji wersji PROGRAMU SQL Server i rozmiaru maszyny wirtualnej, zobacz stronę z cennikiem dla [systemu Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows) lub [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux). Wybierz platformę i wersję PROGRAMU SQL Server z listy **OS/Oprogramowanie.**
>
> ![Interfejs użytkownika na stronie cennik maszyny Wirtualnej](./media/virtual-machines-windows-sql-server-pricing-guidance/virtual-machines-pricing-ui.png)
>
> Możesz też użyć [kalkulatora cen,](https://azure.microsoft.com/pricing/#explore-cost) aby dodać i skonfigurować maszynę wirtualną. 

## <a name="free-licensed-sql-server-editions"></a>Bezpłatne wersje programu SQL Server

Jeśli chcesz opracować, przetestować lub zbudować dowód koncepcji, użyj swobodnie licencjonowanej **wersji SQL Server Developer.** Ta wersja ma wszystkie funkcje programu SQL Server Enterprise edition, co pozwala na tworzenie i testowanie dowolnego typu aplikacji. Jednak nie można uruchomić wersji dewelopera w wersji produkcyjnej. Maszyna wirtualna w wersji sql server developer edition wiąże się tylko z opłatami za koszt maszyny Wirtualnej, ponieważ nie ma żadnych powiązanych kosztów licencjonowania programu SQL Server.

Jeśli chcesz uruchomić lekkie obciążenie w produkcji (<4 rdzenie, <pamięć 1 GB, <10 GB/baza danych), użyj swobodnie licencjonowanej **wersji SQL Server Express**. Maszyna wirtualna w wersji SQL Server Express również pobiera tylko opłaty za koszt maszyny Wirtualnej.

W przypadku tych obciążeń deweloperskich/testowych i lekkich obciążeń produkcyjnych można również zaoszczędzić pieniądze, wybierając mniejszy rozmiar maszyny Wirtualnej, który odpowiada tym obciążeniom. DS1v2 może być dobrym wyborem w niektórych scenariuszach.

Aby utworzyć maszynę wirtualną platformy Azure programu SQL Server 2017 z jednym z tych obrazów, zobacz następujące łącza:

| Platforma | Swobodnie licencjonowane obrazy |
|---|---|
| Windows Server 2016 | [SQL Server 2017 Developer Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonWindowsServer2016)<br/>[Maszyna wirtualna platformy Azure programu SQL Server 2017 Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonWindowsServer2016) |
| Red Hat Enterprise Linux | [SQL Server 2017 Developer Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonRedHatEnterpriseLinux74)<br/>[Maszyna wirtualna platformy Azure programu SQL Server 2017 Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonRedHatEnterpriseLinux74) |
| SUSE Linux Enterprise Server | [SQL Server 2017 Developer Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonSLES12SP2)<br/>[Maszyna wirtualna platformy Azure programu SQL Server 2017 Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonSLES12SP2) |
| Ubuntu | [SQL Server 2017 Developer Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonUbuntuServer1604LTS)<br/>[Maszyna wirtualna platformy Azure programu SQL Server 2017 Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonUbuntuServer1604LTS) |

## <a name="paid-sql-server-editions"></a>Płatne wersje programu SQL Server

Jeśli masz nielekkie obciążenie produkcyjne, użyj jednej z następujących wersji programu SQL Server:

| SQL Server Edition | Obciążenie |
|-----|-----|
| sieć Web | Małe strony internetowe |
| Standardowa | Małe i średnie obciążenia |
| Enterprise | Duże obciążenia o znaczeniu krytycznym|

Dostępne są dwie opcje płacenia za licencjonowanie programu SQL Server dla tych wersji: *płatność za użycie* lub *przywiezienie własnej licencji (BYOL).*

## <a name="pay-per-usage"></a>Zapłać za użycie

**Płacenie licencji programu SQL Server na użycie** (nazywane również **płatnością w drodze)** oznacza, że koszt na sekundę uruchomienia maszyny Wirtualnej platformy Azure obejmuje koszt licencji programu SQL Server. Ceny dla różnych wersji programu SQL Server (Web, Standard, Enterprise) można wyświetlić na stronie cenniku maszyny Wirtualnej platformy Azure dla [systemu Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows) lub [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux).

Koszt jest taki sam dla wszystkich wersji programu SQL Server (2012 SP3 do 2017). Koszt licencjonowania na sekundę zależy od liczby procesorów wirtualnych maszyn wirtualnych.

Płacenie licencji programu SQL Server na użycie jest zalecane dla:

- **Obciążenia tymczasowe lub okresowe**. Na przykład aplikacja, która musi obsługiwać wydarzenie przez kilka miesięcy każdego roku lub analiza biznesowa w poniedziałki.

- **Obciążenia o nieznanym okresie istnienia lub skali**. Na przykład aplikacja, która może nie być wymagana w ciągu kilku miesięcy lub która może wymagać więcej lub mniej mocy obliczeniowej, w zależności od zapotrzebowania.

Aby utworzyć maszynę wirtualną platformy Azure programu SQL Server 2017 z jednym z tych obrazów płatności zgodnie z rzeczywistymu poniżej, zobacz następujące łącza:

| Platforma | Licencjonowane obrazy |
|---|---|
| Windows Server 2016 | [Maszyna wirtualna platformy Web azure programu SQL Server 2017](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonWindowsServer2016)<br/>[Standardowa maszyna wirtualna platformy Azure w programie SQL Server 2017](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonWindowsServer2016)<br/>[Maszyna wirtualna platformy Azure dla programu SQL Server 2017](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseWindowsServer2016) |
| Red Hat Enterprise Linux | [Maszyna wirtualna platformy Web azure programu SQL Server 2017](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonRedHatEnterpriseLinux74)<br/>[Standardowa maszyna wirtualna platformy Azure w programie SQL Server 2017](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonRedHatEnterpriseLinux74)<br/>[Maszyna wirtualna platformy Azure dla programu SQL Server 2017](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonRedHatEnterpriseLinux74) |
| SUSE Linux Enterprise Server | [Maszyna wirtualna platformy Web azure programu SQL Server 2017](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonSLES12SP2)<br/>[Standardowa maszyna wirtualna platformy Azure w programie SQL Server 2017](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonSLES12SP2)<br/>[Maszyna wirtualna platformy Azure dla programu SQL Server 2017](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonSLES12SP2) |
| Ubuntu | [Maszyna wirtualna platformy Web azure programu SQL Server 2017](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonUbuntuServer1604LTS)<br/>[Standardowa maszyna wirtualna platformy Azure w programie SQL Server 2017](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonUbuntuServer1604LTS)<br/>[Maszyna wirtualna platformy Azure dla programu SQL Server 2017](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonUbuntuServer1604LTS) |

> [!IMPORTANT]
> Podczas tworzenia maszyny wirtualnej programu SQL Server w portalu okno **Wybierz rozmiar** zawiera szacowany koszt. Należy pamiętać, że to oszacowanie jest tylko koszty obliczeniowe dla uruchomienia maszyny Wirtualnej wraz z wszelkich kosztów licencjonowania systemu operacyjnego (Windows lub innych systemów operacyjnych Linux).
>
> ![Wybieranie bloku o rozmiarze maszyny Wirtualnej](./media/virtual-machines-windows-sql-server-pricing-guidance/sql-vm-choose-size-pricing-estimate.png)
>
>Nie obejmuje dodatkowych kosztów licencjonowania programu SQL Server dla wersji Sieci Web, Standard i Enterprise. Aby uzyskać najdokładniejsze oszacowanie cen, wybierz system operacyjny i wersję SQL Server na stronie z cennikiem [dla systemu Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) lub [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

> [!NOTE]
> Teraz można zmienić model licencjonowania z pay-per-usage, aby przynieść własną licencję (BYOL) i z powrotem. Aby uzyskać więcej informacji, zobacz [Jak zmienić model licencjonowania dla maszyny wirtualnej SQL](virtual-machines-windows-sql-ahb.md). 

## <a name="bring-your-own-license-byol"></a><a id="byol"></a>Przynieś własną licencję (BYOL)

**Wprowadzenie własnej licencji programu SQL Server za pośrednictwem mobilności licencji,** zwanej również **BYOL,** oznacza użycie istniejącej licencji zbiorczej programu SQL Server z pakietem Software Assurance na maszynie Wirtualnej platformy Azure. Maszyna wirtualna programu SQL Server używająca narzędzia BYOL pobiera opłaty tylko za koszt uruchomienia maszyny Wirtualnej, a nie za licencjonowanie programu SQL Server, biorąc pod uwagę, że licencje i pakiet Software Assurance zostały już nabyte za pośrednictwem programu licencjonowania zbiorowego.

> [!IMPORTANT]
> Obrazy BYOL wymagają umowy Enterprise Agreement z pakietem Software Assurance. Obecnie nie są one dostępne jako część programu Azure Cloud Solution Partner (CSP). Klienci usług CSP mogą przynieść własną licencję, wdrażając obraz płatności zgodnie z rzeczywistym użyciem, a następnie włączając [korzyść hybrydową platformy Azure.](virtual-machines-windows-sql-ahb.md)

> [!NOTE]
> Obrazy BYOL są obecnie dostępne tylko dla maszyn wirtualnych z systemem Windows. Można jednak ręcznie zainstalować program SQL Server na maszynie wirtualnej tylko dla systemu Linux. Zapoznaj się z wytycznymi w [często zadawanych pytaniach dotyczących maszyny wirtualnej języka Linux.](../../linux/sql/sql-server-linux-faq.md)

Wprowadzanie własnych licencji SQL za pośrednictwem mobilności licencji jest zalecane dla:

- **Ciągłe obciążenia**. Na przykład aplikacja, która musi obsługiwać operacje biznesowe 24x7.

- **Obciążenia o znanym okresie istnienia i skali**. Na przykład aplikacja, która jest wymagana przez cały rok i który popyt został prognozowany.

Aby używać narzędzia BYOL z maszyną wirtualną programu SQL Server, użytkownik musi mieć licencję na program SQL Server Standard lub Enterprise and [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default.aspx#tab=1), która jest wymaganą opcją za pośrednictwem niektórych programów licencjonowania zbiorowego i opcjonalnym zakupem u innych. Poziom cen zapewniany za pośrednictwem programów licencjonowania zbiorowego różni się w zależności od typu umowy oraz ilości i lub zaangażowania w program SQL Server. Ale z reguły wprowadzenie własnej licencji na ciągłe obciążenia produkcyjne ma następujące zalety:

| Korzyści BYOL | Opis |
|-----|-----|
| **Redukcja kosztów** | [Korzyść hybrydowa platformy Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) oferuje do 55% oszczędności. Aby uzyskać więcej informacji, zobacz [Przełączanie modelu licencjonowania](virtual-machines-windows-sql-ahb.md) |
| **Darmowa pasywna replika pomocnicza** | Inną zaletą wprowadzenia własnej licencji jest [bezpłatne licencjonowanie jednej pasywnej repliki pomocniczej](https://azure.microsoft.com/pricing/licensing-faq/) na program SQL Server w celu uzyskania wysokiej dostępności. Spowoduje to zmniejszenie o połowę kosztu licencjonowania wdrożenia programu SQL Server o wysokiej dostępności (na przykład przy użyciu grup zawsze na dostępności). Prawa do uruchamiania pasywnego pomocniczego są dostarczane za pośrednictwem korzyści software assurance serwerów awaryjnych. |

Aby utworzyć maszynę wirtualną platformy Azure programu SQL Server 2017 z jednym z tych obrazów przynieś własną licencję, zobacz maszyny wirtualne z prefiksem "{BYOL}":

- [Maszyna wirtualna platformy Azure dla programu SQL Server 2017](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017EnterpriseWindowsServer2016)
- [Standardowa maszyna wirtualna platformy Azure w programie SQL Server 2017](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017StandardonWindowsServer2016)

> [!IMPORTANT]
> Poinformuj nas o tym, ile licencji programu SQL Server jest używanych na platformie Azure. Linki do poprzednich obrazów mają instrukcje, jak to zrobić.

> [!NOTE]
> Teraz można zmienić model licencjonowania z pay-per-usage, aby przynieść własną licencję (BYOL) i z powrotem. Aby uzyskać więcej informacji, zobacz [Jak zmienić model licencjonowania dla maszyny wirtualnej SQL](virtual-machines-windows-sql-ahb.md). 



## <a name="reduce-costs"></a>Redukcja kosztów

Aby uniknąć niepotrzebnych kosztów, wybierz optymalny rozmiar maszyny wirtualnej i rozważ sporadyczne zamykanie dla obciążeń nieciągłych.

### <a name="correctly-size-your-vm"></a><a id="machinesize"></a>Prawidłowy rozmiar maszyny Wirtualnej

Koszt licencjonowania programu SQL Server jest bezpośrednio powiązany z liczbą procesorów wirtualnych. Wybierz rozmiar maszyny Wirtualnej, który odpowiada oczekiwanym potrzebom procesora, pamięci, pamięci i przepustowości we/wy. Aby uzyskać pełną listę opcji rozmiaru komputera, zobacz [Rozmiary maszyn wirtualnych z systemem Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) i [rozmiary maszyn wirtualnych z systemem Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Istnieją nowe rozmiary komputerów, które działają dobrze z niektórych typów obciążeń programu SQL Server. Te rozmiary maszyn zachowują wysoki poziom pamięci, pamięci masowej i przepustowości we/wy, ale mają mniejszą liczbę zwirtualizowanych rdzeni. Rozważmy na przykład następujący przykład:

| Rozmiar maszyny wirtualnej | Procesory wirtualne | Memory (Pamięć) | Maksymalna liczba dysków | Maksymalna przepustowość we/wy | Koszty licencjonowania SQL | Łączne koszty (obliczenia + licencjonowanie) |
|---|---|---|---|---|---|---|
| **Standard_DS14v2** | 16 | 112 GB | 32 | 51 200 operacji we/wy lub 768 MB/s | | |
| **Standardowa_DS14-4v2** | 4 | 112 GB | 32 | 51 200 operacji we/wy lub 768 MB/s | 75% niższy | 57% niższy |

> [!IMPORTANT]
> Jest to przykład punktu w czasie. Najnowsze specyfikacje można znaleźć w artykułach dotyczących rozmiarów maszyn oraz na stronie cennika platformy Azure dla [systemów Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) i [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

W poprzednim przykładzie widać, że specyfikacje **dla Standard_DS14v2** i **Standard_DS14-4v2** są identyczne z wyjątkiem procesorów wirtualnych. Sufiks **-4v2** na końcu **Standard_DS14-4v2** rozmiar maszyny wskazuje liczbę aktywnych procesorów wirtualnych. Ponieważ koszty licencjonowania programu SQL Server są powiązane z liczbą procesorów wirtualnych, znacznie zmniejsza to koszt maszyny Wirtualnej w scenariuszach, w których dodatkowe procesory wirtualne nie są potrzebne. Jest to jeden z przykładów i istnieje wiele rozmiarów maszyn z ograniczonymi procesorami wirtualnymi, które są identyfikowane za pomocą tego wzorca sufiksu. Aby uzyskać więcej informacji, zobacz wpis w blogu [Ogłaszanie nowych rozmiarów maszyn wirtualnych platformy Azure, aby uzyskać bardziej ekonomiczną pracę nad bazą danych.](https://azure.microsoft.com/blog/announcing-new-azure-vm-sizes-for-more-cost-effective-database-workloads/)

### <a name="shut-down-your-vm-when-possible"></a>Wyłącz maszynę wirtualną, jeśli to możliwe

Jeśli używasz żadnych obciążeń, które nie są uruchamiane w sposób ciągły, należy rozważyć zamknięcie maszyny wirtualnej w okresach nieaktywnych. Płaci się wyłącznie za użyte zasoby.

Na przykład jeśli po prostu wypróbowywać SQL Server na maszynie Wirtualnej platformy Azure, nie chcesz ponosić opłat przez przypadkowe pozostawienie go uruchomionego przez kilka tygodni. Jednym z rozwiązań jest użycie [funkcji automatycznego zamykania](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/).

![Automatyczne wycofywanie maszyn wirtualnych sql](./media/virtual-machines-windows-sql-server-pricing-guidance/sql-vm-auto-shutdown.png)

Automatyczne zamykanie jest częścią większego zestawu podobnych funkcji oferowanych przez [platformę Azure DevTest Labs.](https://azure.microsoft.com/services/devtest-lab)

W przypadku innych przepływów pracy należy rozważyć automatyczne zamykanie i ponowne uruchamianie maszyn wirtualnych platformy Azure za pomocą rozwiązania do skryptów, takiego jak [usługa Azure Automation.](https://azure.microsoft.com/services/automation/)

> [!IMPORTANT]
> Zamykanie i rozdzielanie maszyny wirtualnej jest jedynym sposobem uniknięcia opłat. Po prostu zatrzymanie lub użycie opcji zasilania, aby zamknąć maszynę wirtualną nadal wiąże się z opłatami za użycie.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać ogólne wskazówki dotyczące cen platformy Azure, zobacz [Zapobieganie nieoczekiwanym kosztom dzięki rozliczaniu i zarządzaniu kosztami platformy Azure.](../../../cost-management-billing/manage/getting-started.md) Aby uzyskać najnowsze ceny maszyn wirtualnych, w tym sql server, zobacz stronę cenowania maszyn wirtualnych platformy Azure dla [maszyn wirtualnych z systemem Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) i maszyn [wirtualnych z systemem Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

Aby zapoznać się z omówieniem programu SQL Server działającego na maszynach wirtualnych platformy Azure, zobacz następujące artykuły:

- [Omówienie programu SQL Server na maszynach wirtualnych z systemem Windows](virtual-machines-windows-sql-server-iaas-overview.md)
- [Omówienie programu SQL Server na maszynach wirtualnych z systemem Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
