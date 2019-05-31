---
title: Efektywne zarządzanie kosztami dla programu SQL Server na maszynach wirtualnych platformy Azure | Dokumentacja firmy Microsoft
description: Przedstawiono najlepsze rozwiązania dotyczące wybierania prawo modelu cen maszyny wirtualnej w SQL Server.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/09/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: ce07c6c19c19f134cc322309bb338b94ef11ea85
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393852"
---
# <a name="pricing-guidance-for-sql-server-azure-vms"></a>Cennik wskazówki dotyczące maszyn wirtualnych programu SQL Server platformy Azure

Ten artykuł zawiera wskazówki cen dla [maszyny wirtualnej programu SQL Server](virtual-machines-windows-sql-server-iaas-overview.md) na platformie Azure. Dostępnych jest kilka opcji, które mają wpływ na koszt i ważne jest, aby wybrać właściwy obraz, który koszty od wymagań biznesowych.

> [!TIP]
> Jeśli musisz sprawdzić szacowania kosztów dla określonej kombinacji wersji programu SQL Server i rozmiar maszyny wirtualnej, zobacz stronę cen dla [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows) lub [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux). Wybierz platformę i wersji programu SQL Server z **system operacyjny/oprogramowanie** listy.
>
> ![Interfejs użytkownika na stronie cennika maszyny Wirtualnej](./media/virtual-machines-windows-sql-server-pricing-guidance/virtual-machines-pricing-ui.png)
>
> Lub użyj [Kalkulator cen](https://azure.microsoft.com/pricing/#explore-cost) Dodawanie i konfigurowanie maszyny wirtualnej. 

## <a name="free-licensed-sql-server-editions"></a>Bezpłatnie licencjonowane wersje programu SQL Server

Jeśli chcesz tworzenia, testowania i kompilacji, weryfikacji koncepcji, użyj bezpłatnie licencjonowane **programu SQL Server Developer edition**. Ta wersja zawiera wszystkie funkcje programu SQL Server Enterprise edition, co umożliwia tworzenie i testowanie aplikacji dowolnego typu. Jednak nie można uruchomić Developer edition w środowisku produkcyjnym. Wersja programu SQL Server Developer maszyny Wirtualnej tylko opłaty naliczane koszt maszyny Wirtualnej, ponieważ brak skojarzonego serwera SQL, koszty licencjonowania.

Jeśli chcesz uruchomić uproszczone obciążenia w środowisku produkcyjnym (< 4 rdzenie, < 1 GB pamięci, < 10 GB/bazy danych), używać swobodne licencjonowane **programu SQL Server Express edition**. Programu SQL Server Express edition maszyny Wirtualnej tylko spowoduje naliczenie opłaty za koszt maszyny Wirtualnej.

Dla tych projektowania i testowania oraz lekkich obciążeń produkcyjnych umożliwia zaoszczędzenie pieniędzy, wybierając mniejszego rozmiaru maszyny Wirtualnej, zgodną z tych obciążeń. DS1v2 może być dobrym wyborem w niektórych scenariuszach.

Aby utworzyć maszynę Wirtualną programu SQL Server 2017 Azure przy użyciu jednego z tych obrazów, zobacz następujące linki:

| Platforma | Bezpłatnie licencjonowane obrazy |
|---|---|
| Windows Server 2016 | [SQL Server 2017 Developer — maszyn wirtualnych platformy Azure](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonWindowsServer2016)<br/>[SQL Server 2017 Express maszyny Wirtualnej platformy Azure](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonWindowsServer2016) |
| Red Hat Enterprise Linux | [SQL Server 2017 Developer — maszyn wirtualnych platformy Azure](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonRedHatEnterpriseLinux74)<br/>[SQL Server 2017 Express maszyny Wirtualnej platformy Azure](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonRedHatEnterpriseLinux74) |
| SUSE Linux Enterprise Server | [SQL Server 2017 Developer — maszyn wirtualnych platformy Azure](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonSLES12SP2)<br/>[SQL Server 2017 Express maszyny Wirtualnej platformy Azure](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonSLES12SP2) |
| Ubuntu | [SQL Server 2017 Developer — maszyn wirtualnych platformy Azure](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonUbuntuServer1604LTS)<br/>[SQL Server 2017 Express maszyny Wirtualnej platformy Azure](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonUbuntuServer1604LTS) |

## <a name="paid-sql-server-editions"></a>Płatnymi wersjami programu SQL Server

W przypadku obciążeń produkcyjnych bez uproszczonych, użyj jednej z następujących wersji programu SQL Server:

| Wersja programu SQL Server | Obciążenie |
|-----|-----|
| sieć Web | Małych witryn sieci web |
| Standardowa (Standard) | Małych i średnich obciążeń |
| Enterprise | Obciążeń dużych lub o kluczowym znaczeniu|

Dostępne są dwie opcje do zapłacenia za licencjonowania w przypadku tych wersji programu SQL Server: *płacić za użycie* lub *model dostarczania własnej licencji (BYOL)* .

## <a name="pay-per-usage"></a>Płatność za użycie

**Płacenia za użycie licencji programu SQL Server** oznacza, że koszt na sekundę maszyny Wirtualnej platformy Azure obejmuje koszt licencji programu SQL Server. Widoczne ceny różnych wersjach programu SQL Server (Web, Standard i Enterprise) na maszynie Wirtualnej platformy Azure, cennik dla [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows) lub [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux).

Koszt jest taka sama dla wszystkich wersji programu SQL Server (2012 SP3 2017). Koszt licencji na sekundę zależy od liczby procesorów wirtualnych maszyny Wirtualnej.

Zwracając programu SQL Server licencjonowania za użycie jest zalecane w przypadku:

- **Tymczasowy lub okresowe obciążeń**. Na przykład aplikacja, która musi obsługiwać zdarzenia z kilku miesięcy co roku lub analizy biznesowe w poniedziałek.

- **Obciążenia za pomocą skalowania lub nieznany okres istnienia**. Na przykład aplikacja, która może nie być wymagane w ciągu kilku miesięcy lub, co może wymagać więcej lub mniej mocy obliczeniowej, w zależności od zapotrzebowania.

Aby utworzyć maszynę Wirtualną programu SQL Server 2017 Azure przy użyciu jednego z tych obrazów płatność za użycie, zobacz następujące linki:

| Platforma | Licencjonowane obrazy |
|---|---|
| Windows Server 2016 | [SQL Server 2017 sieci Web Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonWindowsServer2016)<br/>[SQL Server 2017 standardowa maszyna wirtualna platformy Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonWindowsServer2016)<br/>[SQL Server 2017 Enterprise — maszyn wirtualnych platformy Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseWindowsServer2016) |
| Red Hat Enterprise Linux | [SQL Server 2017 sieci Web Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonRedHatEnterpriseLinux74)<br/>[SQL Server 2017 standardowa maszyna wirtualna platformy Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonRedHatEnterpriseLinux74)<br/>[SQL Server 2017 Enterprise — maszyn wirtualnych platformy Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonRedHatEnterpriseLinux74) |
| SUSE Linux Enterprise Server | [SQL Server 2017 sieci Web Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonSLES12SP2)<br/>[SQL Server 2017 standardowa maszyna wirtualna platformy Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonSLES12SP2)<br/>[SQL Server 2017 Enterprise — maszyn wirtualnych platformy Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonSLES12SP2) |
| Ubuntu | [SQL Server 2017 sieci Web Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonUbuntuServer1604LTS)<br/>[SQL Server 2017 standardowa maszyna wirtualna platformy Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonUbuntuServer1604LTS)<br/>[SQL Server 2017 Enterprise — maszyn wirtualnych platformy Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonUbuntuServer1604LTS) |

> [!IMPORTANT]
> Po utworzeniu maszyny wirtualnej programu SQL Server w portalu, **wybierz rozmiar** okno pokazuje szacowany koszt. Należy pamiętać, że te dane szacunkowe jest tylko koszty obliczeń dotyczących uruchamiania maszyny Wirtualnej wraz z kosztami (Windows lub systemy operacyjne Linux innych firm) licencji na system operacyjny.
>
> ![Wybierz blok rozmiar maszyny Wirtualnej](./media/virtual-machines-windows-sql-server-pricing-guidance/sql-vm-choose-size-pricing-estimate.png)
>
>Nie ma dodatkowych licencjonowania programu SQL Server koszty Web, Standard i Enterprise. Aby uzyskać najbardziej dokładne szacowania cen, wybierz używanego systemu operacyjnego i wersji programu SQL Server, na stronie cennika dla [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) lub [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

> [!NOTE]
> Teraz jest możliwa zmiana modelu licencjonowania z płatność za użycie na model dostarczania własnej licencji (BYOL) i wykonać ich kopię. Aby uzyskać więcej informacji, zobacz [Jak zmienić model licencjonowania dla maszyny wirtualnej SQL](virtual-machines-windows-sql-ahb.md). 

## <a id="byol"></a> Model dostarczania własnej licencji (BYOL)

**Użycie własnej licencji programu SQL Server, dzięki funkcji przenośności licencji**, nazywany również **BYOL**, oznacza, że przy użyciu istniejącej licencji woluminu programu SQL Server z pakietem Software Assurance w Maszynie wirtualnej platformy Azure. SQL Server maszyny Wirtualnej przy użyciu modelu BYOL opłaty tylko kosztów obsługi maszyny Wirtualnej, a nie licencji programu SQL Server, biorąc pod uwagę, że masz już nabyte za pośrednictwem programu licencjonowania zbiorowego licencji i pakietu Software Assurance.

> [!IMPORTANT]
> Obrazy BYOL wymaga umowy Enterprise Agreement z pakietem Software Assurance. Nie są one dostępne w ramach usługi Azure Cloud Solution Partner (CSP) w tej chwili. Dostawcy usług Kryptograficznych mogą oni własnej licencji przez wdrożenie obrazu zgodnie z rzeczywistym użyciem, a następnie włączać [korzyść użycia hybrydowego platformy Azure](virtual-machines-windows-sql-ahb.md).

> [!NOTE]
> Obrazy BYOL są obecnie dostępne tylko dla maszyn wirtualnych Windows. Można jednak ręcznie zainstalować programu SQL Server na maszynie Wirtualnej tylko do systemu Linux. Zapoznaj się z instrukcjami w [często zadawane pytania dotyczące systemu Linux SQL VM](../../linux/sql/sql-server-linux-faq.md).

Wprowadzenie własnych SQL licencjonowania dzięki funkcji przenośności licencji jest zalecane w przypadku:

- **Ciągłe obciążeń**. Na przykład aplikacja, która musi obsługiwać operacje biznesowe 24 x 7.

- **Obciążenia za pomocą znanych okres istnienia i skalowania**. Na przykład aplikacja, która jest wymagana przez cały rok i którym żądanie zostało prognozowanych.

Aby użyć BYOL z maszyny Wirtualnej programu SQL Server, musi mieć licencję programu SQL Server Standard lub Enterprise i [pakietu Software Assurance](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-default.aspx#tab=1), która opcja jest wymagana przez niektóre programy licencjonowania zbiorowego i opcjonalnie zakupu w innym osobom. Poziom cen, dostępne za pośrednictwem programów licencjonowania zbiorowego różni się w zależności od typu umowy oraz ilość i lub zobowiązania do programu SQL Server. Jednak jako ogólną regułę można przyjąć, użycie własnej licencji w przypadku ciągłych obciążeń produkcyjnych ma następujące zalety:

| Korzyści w ramach opcji BYOL | Opis |
|-----|-----|
| **Oszczędności kosztów** | Użycie własnej licencji programu SQL Server jest bardziej opłacalna niż płacenia za użycie, jeśli obciążenie działa w sposób ciągły programu SQL Server Standard lub Enterprise dla *więcej niż 10 miesięcy*. |
| **Długoterminowe oszczędności** | Średnio jest *30% tańsze rocznie* kupowania lub odnowić licencję programu SQL Server przez pierwsze 3 lata. Ponadto po 3 lata, nie trzeba już odnowić licencję, po prostu płacić za pakiet Software Assurance. W tym momencie jest *200% tańsze*. |
| **Bezpłatne pasywnym replika pomocnicza** | Inną zaletą użycie własnej licencji jest [bezpłatnej licencji na program jedna replika pomocnicza pasywnym](https://azure.microsoft.com/pricing/licensing-faq/) dla programu SQL Server, aby zapewnić wysoką dostępność. To skraca w wysokości równej połowie koszt licencji jest wysoko dostępne wdrożenie programu SQL Server (na przykład przy użyciu zawsze włączonych grup dostępności). Prawa do uruchamiania pasywną są dostarczane za pośrednictwem korzyści z pracy awaryjnej serwery z pakietem Software Assurance. |

Aby utworzyć maszynę Wirtualną programu SQL Server 2017 Azure przy użyciu jednego z tych obrazów bring-your-own-license, zobacz maszyn wirtualnych z prefiksem "{BYOL}":

- [SQL Server 2017 Enterprise — maszyn wirtualnych platformy Azure](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017EnterpriseWindowsServer2016)
- [SQL Server 2017 standardowa maszyna wirtualna platformy Azure](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017StandardonWindowsServer2016)

> [!IMPORTANT]
> Powiedz, co w ciągu 10 dni ile licencji programu SQL Server, którego używasz na platformie Azure. Łącza do poprzedniego obrazów mają instrukcje, jak to zrobić.

> [!NOTE]
> Teraz jest możliwa zmiana modelu licencjonowania z płatność za użycie na model dostarczania własnej licencji (BYOL) i wykonać ich kopię. Aby uzyskać więcej informacji, zobacz [Jak zmienić model licencjonowania dla maszyny wirtualnej SQL](virtual-machines-windows-sql-ahb.md). 



## <a name="reduce-costs"></a>Obniżenie kosztów

Aby uniknąć niepotrzebnych kosztów, wybierz rozmiar optymalne maszyny wirtualnej, a następnie należy wziąć pod uwagę sporadyczne zamykania dla obciążeń — ciągły.

### <a id="machinesize"></a> Poprawnie rozmiar maszyny Wirtualnej

Koszt licencji programu SQL Server jest bezpośrednio związana liczba procesorów wirtualnych. Wybierz rozmiar maszyny Wirtualnej, który pasuje do oczekiwanego potrzeby dotyczące procesora CPU, pamięć, Magazyn i przepustowość operacji We/Wy. Aby uzyskać pełną listę opcji rozmiaru maszyny, zobacz [rozmiarów maszyn wirtualnych Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) i [rozmiarów maszyn wirtualnych systemu Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Istnieją nowe rozmiary maszyn, które działają prawidłowo w przypadku niektórych rodzajów obciążeń programu SQL Server. Te rozmiary maszyn utrzymywać wysokie poziomy pamięć, Magazyn i przepustowość operacji We/Wy, ale mają niższe liczby rdzeni zwirtualizowanych. Na przykład rozważmy następujący przykład:

| Rozmiar maszyny wirtualnej | procesorów wirtualnych | Memory (Pamięć) | Maksymalna liczba dysków | Maksymalna przepływność operacji We/Wy | Koszty licencjonowania programu SQL | Łączne koszty (obliczeń + licencjonowania) |
|---|---|---|---|---|---|---|
| **Standard_DS14v2** | 16 | 112 GB | 32 | 51,200 operacje We/Wy lub 768 MB/s | | |
| **Standard_DS14 4v2** | 4 | 112 GB | 32 | 51,200 operacje We/Wy lub 768 MB/s | 75% niższy | 57% niższy |

> [!IMPORTANT]
> To jest przykład punktu w czasie. Najnowszej specyfikacji, można znaleźć w artykułach rozmiarów maszyny i na platformie Azure, cennik dla [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) i [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

W poprzednim przykładzie możesz zobaczyć, że specyfikacje **Standard_DS14v2** i **Standard_DS14 4v2** są identyczne, z wyjątkiem procesorów wirtualnych. Sufiks **-4v2** na końcu **Standard_DS14 4v2** rozmiar maszyny wskazuje liczbę procesorów wirtualnych active. Ponieważ koszty licencjonowania programu SQL Server są powiązane z liczbą procesorów wirtualnych Vcpu, pozwala to znacznie ograniczyć koszty maszyn wirtualnych w scenariuszach, w których nie są wymagane dodatkowe procesorów wirtualnych. Jest to przykład. Ponadto dostępnych jest wiele różnych rozmiarów maszyny z procesory, które są identyfikowane za pomocą tego wzorca sufiks. Aby uzyskać więcej informacji, zobacz wpis w blogu [Przedstawiamy nowe rozmiary maszyn wirtualnych platformy Azure, więcej pracy ekonomiczne bazy danych](https://azure.microsoft.com/blog/announcing-new-azure-vm-sizes-for-more-cost-effective-database-workloads/).

### <a name="shut-down-your-vm-when-possible"></a>Zamknij maszynę Wirtualną, gdy jest to możliwe

Jeśli używasz dowolnego obciążenia, które nie są uruchamiane w sposób ciągły, należy wziąć pod uwagę zamykanie maszyny wirtualnej w okresach nieaktywne. Płaci się wyłącznie za użyte zasoby.

Na przykład jeśli po prostu sprawdzasz programu SQL Server na Maszynie wirtualnej platformy Azure, nie chcesz naliczenia opłat, którzy odchodzą przypadkowo będzie działać w ciągu tygodni. Jednym rozwiązaniem jest użycie [funkcji automatycznego zamykania](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/).

![Autoshutdown maszyny Wirtualnej SQL](./media/virtual-machines-windows-sql-server-pricing-guidance/sql-vm-auto-shutdown.png)

Automatyczne zamknięcie jest częścią większy zbiór podobne funkcje oferowane przez [usługi Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab).

W przypadku innych przepływów pracy, należy wziąć pod uwagę zamykanie i ponowne uruchomienie maszyn wirtualnych platformy Azure przy użyciu rozwiązanie do obsługi skryptów, takich jak automatycznie [usługi Azure Automation](https://azure.microsoft.com/services/automation/).

> [!IMPORTANT]
> Zamykanie i cofanie przydziału maszyny Wirtualnej jest jedynym sposobem, aby uniknąć naliczania opłat. Po prostu zatrzymanie lub za pomocą opcji zasilania w celu zamknięcia maszyny Wirtualnej nadal spowoduje naliczenie opłaty za użycie.

## <a name="next-steps"></a>Kolejne kroki

Na platformie Azure ogólne cenach wskazówki, zobacz [zapobieganie powstawaniu nieoczekiwanych kosztów za pomocą rozliczeń platformy Azure i zarządzania kosztami](../../../billing/billing-getting-started.md). Najnowsze maszyn wirtualnych cen, w tym program SQL Server, zobacz Azure maszyny Wirtualnej platformy Azure, cennik dla [maszyn wirtualnych Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) i [maszyn wirtualnych systemu Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

Omówienie programu SQL Server uruchomionym na maszynach wirtualnych platformy Azure zobacz następujące artykuły:

- [Omówienie programu SQL Server na maszynach wirtualnych Windows](virtual-machines-windows-sql-server-iaas-overview.md)
- [Omówienie programu SQL Server na maszynach wirtualnych z systemem Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
