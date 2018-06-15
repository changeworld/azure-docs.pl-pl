---
title: Efektywne zarządzanie kosztami dla programu SQL Server na maszynach wirtualnych Azure | Dokumentacja firmy Microsoft
description: Zawiera najlepsze rozwiązania dotyczące wybierania model cenowy prawo maszyny wirtualnej programu SQL Server.
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/02/2018
ms.author: jroth
ms.openlocfilehash: 71c86af9d4dcdf1026b4f539574b9932ef1cfc89
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
ms.locfileid: "32767804"
---
# <a name="pricing-guidance-for-sql-server-azure-vms"></a>Wskazówek dotyczących ceny dla maszyn wirtualnych Azure, programu SQL Server

Ten artykuł zawiera cenową wskazówki dotyczące [maszyn wirtualnych programu SQL Server](virtual-machines-windows-sql-server-iaas-overview.md) na platformie Azure. Dostępnych jest kilka opcji, które mają wpływ na koszty i ważne jest, aby wybrać prawego obrazu, który równoważy koszt z wymaganiami biznesowymi.

> [!TIP]
> Jeśli musisz sprawdzić szacowania kosztów dla określonej kombinacji wersji programu SQL Server, a rozmiar maszyny wirtualnej, zobacz stronie dotyczącej cen [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows) lub [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux). Wybierz platformy i wersji programu SQL Server z **systemu operacyjnego/oprogramowania** listy.
>
> ![Interfejs użytkownika na stronie cennika maszyny Wirtualnej](./media/virtual-machines-windows-sql-server-pricing-guidance/virtual-machines-pricing-ui.png)
>
> Lub użyj [Kalkulator cen](https://azure.microsoft.com/pricing/#explore-cost) Aby dodać i skonfigurować maszynę wirtualną. 

## <a name="free-licensed-sql-server-editions"></a>Zwolnij licencjonowane wersje programu SQL Server

Testowanie, lub kompilacji Weryfikacja koncepcji, następnie należy użyć za darmo licencjonowane **programu SQL Server Developer edition**. Ta wersja zawiera wszystkie funkcje programu SQL Server Enterprise edition, umożliwia tworzenie i testowanie dowolnego typu aplikacji. Nie można jednak uruchomić Developer edition w środowisku produkcyjnym. Wersja programu SQL Server Developer wirtualna tylko zostaną naliczone opłaty powiązane kosztów maszyny Wirtualnej, ponieważ nie ma skojarzonego serwera SQL koszty licencjonowania.

Jeśli chcesz uruchomić lekkie obciążenia w środowisku produkcyjnym (< 4 rdzenie < 1 GB pamięci RAM, < 10 GB/bazy danych), użyj w pełni licencjonowanej **programu SQL Server Express edition**. SQL Server Express edition wirtualna tylko zostaną naliczone opłaty powiązane kosztów maszyny Wirtualnej.

Dla tych programowanie i testowanie i obciążeń produkcyjnych lekkie można także zapisać pieniędzy, wybierając mniejszego rozmiaru maszyny Wirtualnej, odpowiadający tych obciążeń. DS1v2 może być dobrym rozwiązaniem w niektórych scenariuszach.

Aby utworzyć Maszynę wirtualną Azure 2017 serwera SQL z jednym z tych obrazów, zobacz następujące linki:

| Platforma | Za darmo licencjonowane obrazów |
|---|---|
| Windows Server 2016 | [SQL Server 2017 Developer Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonWindowsServer2016)<br/>[SQL Server 2017 Express maszyna wirtualna platformy Azure](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonWindowsServer2016) |
| Red Hat Enterprise Linux | [SQL Server 2017 Developer Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonRedHatEnterpriseLinux74)<br/>[SQL Server 2017 Express maszyna wirtualna platformy Azure](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonRedHatEnterpriseLinux74) |
| SUSE Linux Enterprise Server | [SQL Server 2017 Developer Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonSLES12SP2)<br/>[SQL Server 2017 Express maszyna wirtualna platformy Azure](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonSLES12SP2) |
| Ubuntu | [SQL Server 2017 Developer Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonUbuntuServer1604LTS)<br/>[SQL Server 2017 Express maszyna wirtualna platformy Azure](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonUbuntuServer1604LTS) |

## <a name="paid-sql-server-editions"></a>Płatną wersjach programu SQL Server

Jeśli obciążenie z systemem innym niż lightweight produkcji, użyj jednej z następujących wersji programu SQL Server:

| Wersja programu SQL Server | Obciążenie |
|-----|-----|
| Sieć Web | Mała witryn sieci web |
| Standardowa (Standard) | Mała liczba godzin w przypadku obciążeń średnia |
| Enterprise | Obciążeń dużych lub krytycznym|

Dostępne są dwie opcje w celu opłacenia licencjonowania programu SQL Server dla następujących wersji: *płatności za użycie* lub *użycie własnej licencji (BYOL)*.

## <a name="pay-per-usage"></a>Należy zwrócić na użycie

**Płatności licencji programu SQL Server na użycie** oznacza, że koszt na sekundę maszyny Wirtualnej Azure zawiera koszt licencji programu SQL Server. Widać cennik o różnych wersjach programu SQL Server (sieci Web, Standard i Enterprise) w Maszynie wirtualnej Azure cennikiem dla [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows) lub [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux).

Koszt jest taka sama dla wszystkich wersji programu SQL Server (2012 z dodatkiem SP3 2017). Kosztów licencjonowania na sekundę zależy od liczby rdzeni maszyny Wirtualnej, który jest standardem wszystkich licencjonowania programu SQL Server.

Płatności programu SQL Server licencjonowania na użycie jest zalecane w przypadku:

- **Tymczasowe lub okresowe obciążeń**. Na przykład aplikacja, która musi obsługiwać zdarzenia z kilku miesięcy, co rok lub analizy biznesowej od poniedziałku.

- **Obciążeń z istnienia nieznany lub skalę**. Na przykład aplikacja, co może nie być wymagane w ciągu kilku miesięcy lub co może wymagać więcej lub mniej moc obliczeniową, w zależności od zapotrzebowania.

Aby utworzyć Maszynę wirtualną Azure 2017 serwera SQL z jednym z tych obrazów płatności za użycie, zobacz następujące linki:

| Platforma | Licencjonowane obrazów |
|---|---|
| Windows Server 2016 | [SQL Server 2017 sieci Web Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonWindowsServer2016)<br/>[SQL Server 2017 standardowa maszyna wirtualna platformy Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonWindowsServer2016)<br/>[SQL Server 2017 Enterprise Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseWindowsServer2016) |
| Red Hat Enterprise Linux | [SQL Server 2017 sieci Web Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonRedHatEnterpriseLinux74)<br/>[SQL Server 2017 standardowa maszyna wirtualna platformy Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonRedHatEnterpriseLinux74)<br/>[SQL Server 2017 Enterprise Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonRedHatEnterpriseLinux74) |
| SUSE Linux Enterprise Server | [SQL Server 2017 sieci Web Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonSLES12SP2)<br/>[SQL Server 2017 standardowa maszyna wirtualna platformy Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonSLES12SP2)<br/>[SQL Server 2017 Enterprise Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonSLES12SP2) |
| Ubuntu | [SQL Server 2017 sieci Web Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonUbuntuServer1604LTS)<br/>[SQL Server 2017 standardowa maszyna wirtualna platformy Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonUbuntuServer1604LTS)<br/>[SQL Server 2017 Enterprise Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonUbuntuServer1604LTS) |

> [!IMPORTANT]
> Podczas tworzenia maszyny wirtualnej programu SQL Server w portalu, **wybierz rozmiar** okna pokazuje szacowany koszt. Należy pamiętać, że ta szacowana jest tylko kosztów obliczeń do uruchamiania maszyn wirtualnych wraz z dowolnego systemu operacyjnego koszty (Windows lub systemów operacyjnych Linux innych firm) licencjonowania.
>
> ![Wybierz bloku rozmiar maszyny Wirtualnej](./media/virtual-machines-windows-sql-server-pricing-guidance/sql-vm-choose-size-pricing-estimate.png)
>
>Nie ma dodatkowych licencjonowania koszty sieci Web, wersje Standard i Enterprise programu SQL Server. Aby uzyskać najbardziej dokładnego oszacowania cenową, wybierz system operacyjny i wersji programu SQL Server na stronie dotyczącej cen [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) lub [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

## <a name="bring-your-own-license-byol"></a>Model dostarczania własnej licencji (BYOL)

**Przywracanie licencję programu SQL Server za pośrednictwem przenośność licencji**, nazywany również **BYOL**, środków z Software Assurance w maszynie Wirtualnej platformy Azure przy użyciu istniejącego programu SQL Server z licencją zbiorczą. Maszyna wirtualna SQL Server przy użyciu BYOL opłat tylko kosztów uruchamiania maszyny Wirtualnej, a nie dla licencjonowania programu SQL Server, biorąc pod uwagę, że ma już nabytych licencji i Software Assurance za pośrednictwem programu licencjonowania zbiorowego.

> [!NOTE]
> Obrazy BYOL są obecnie dostępne tylko dla maszyn wirtualnych systemu Windows. Można jednak ręcznie zainstalować program SQL Server na maszynie Wirtualnej systemu Linux — tylko do. Zapoznaj się z instrukcjami w [Linux SQL maszyny Wirtualnej — często zadawane pytania](../../linux/sql/sql-server-linux-faq.md).

Dostarczają własne SQL licencjonowania za pośrednictwem przenośność licencji jest zalecane w przypadku:

- **Ciągłe obciążeń**. Na przykład aplikacja, która musi obsługiwać operacje biznesowe 24 x 7.

- **Obciążeń przy użyciu znanych okres istnienia i skali**. Na przykład aplikację, która jest wymagana dla całego roku i popytu zostały prognozowanych.

Aby używać BYOL z maszyny Wirtualnej programu SQL Server, musi mieć licencję na program SQL Server Standard lub Enterprise i [Software Assurance](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-default.aspx#tab=1), która jest wymagana przez niektóre programy licencjonowania zbiorczego i opcjonalnie zakupu z innymi osobami. Poziom cenową realizowane za pośrednictwem programów licencjonowania zbiorczego zależy od typu umowy oraz ilość i lub zobowiązań do programu SQL Server. Jednak jako zasadą, przywracanie własnej licencji w przypadku obciążeń produkcyjnych ciągłego ma następujące zalety:

| BYOL korzyści | Opis |
|-----|-----|
| **Oszczędności** | Przywracanie licencję programu SQL Server jest tańsze niż płatności za użycie, jeśli obciążenie działa w sposób ciągły SQL Server Standard lub Enterprise dla *więcej niż 10 miesięcy*. |
| **Długoterminowe oszczędności** | Na ogół jest *30% rocznie tańsze* lub odnowić licencji programu SQL Server przez pierwsze 3 lata. Ponadto po 3 lata, nie trzeba już odnowić licencji, po prostu opłacać Software Assurance. W tym momencie jest *200% tańsze*. |
| **Bezpłatne pasywnym repliki pomocniczej** | Inną zaletą dostarczają własnej licencji jest [wolne Licencjonowanie jedna replika pomocnicza pasywnym](https://azure.microsoft.com/pricing/licensing-faq/) dla programu SQL Server na potrzeby wysokiej dostępności. Wytnij to w połowie licencjonowania koszt wdrożenia programu SQL Server o wysokiej dostępności (na przykład przy użyciu zawsze włączonych grup dostępności). Prawa do uruchamiania pomocniczej pasywnym są udostępniane za pośrednictwem korzyści awaryjnej serwerów Software Assurance. |

Aby utworzyć Maszynę wirtualną Azure 2017 serwera SQL z jednym z tych obrazów Przełącz your właścicielem licencji, zobacz maszyn wirtualnych i jest poprzedzony prefiksem "{BYOL}":

- [SQL Server 2017 Enterprise Azure VM](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017EnterpriseWindowsServer2016)
- [SQL Server 2017 standardowa maszyna wirtualna platformy Azure](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017StandardonWindowsServer2016)

> [!IMPORTANT]
> Trafić w ciągu 10 dni liczbę licencji programu SQL Server używane na platformie Azure. Łącza do poprzedniego obrazy mają instrukcje jak to zrobić.

> [!NOTE]
> Nie jest możliwa zmiana licencjonowania model płatności na sekundę maszyny Wirtualnej serwera SQL, aby użyć własnej licencji. W takim przypadku należy utworzyć nową maszynę wirtualną w modelu BYOL i zmigrować bazy danych do tej nowej maszyny wirtualnej.

## <a name="reduce-costs"></a>Obniżenie kosztów

Aby uniknąć niepotrzebnych kosztów, wybierz rozmiar maszyny wirtualnej optymalne i rozważ sporadyczne zamknięcia nieciągły obciążeń.

### <a id="machinesize"></a> Poprawnie rozmiaru maszyny Wirtualnej

Liczba rdzeni bezpośrednio dotyczy koszty licencjonowania programu SQL Server. Wybierz rozmiar maszyny Wirtualnej, który odpowiada Twoim potrzebom oczekiwany dla Procesora, pamięci, magazynu i przepustowości we/wy. Aby uzyskać pełną listę opcji rozmiaru maszyny, zobacz [rozmiarów maszyn wirtualnych systemu Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) i [rozmiarów maszyn wirtualnych systemu Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Istnieją nowe rozmiary maszyny, które działają prawidłowo w przypadku niektórych typów obciążeń programu SQL Server. Rozmiary maszyn Obsługa wysokiego poziomu pamięci, magazynu i przepustowości we/wy, ale mają niższe liczby rdzeni zwirtualizowanych. Rozważmy na przykład następująco:

| Rozmiar maszyny wirtualnej | Procesory wirtualne vCPU | Memory (Pamięć) | Maksymalna liczba dysków | Maksymalna przepustowość we/wy | Koszty licencjonowania programu SQL | Łączne koszty (obliczeń + licencjonowania) |
|---|---|---|---|---|---|---|
| **Standard_DS14v2** | 16 | 112 GB | 32 | 51,200 IOPS lub 768 MB/s | | |
| **Standard_DS14 4v2** | 4 | 112 GB | 32 | 51,200 IOPS lub 768 MB/s | 75% niższa | 57% niższa |

> [!IMPORTANT]
> To jest przykład punktu w czasie. Najnowsze specyfikacji, można znaleźć maszyny artykuły rozmiary i Azure cennikiem dla [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) i [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

W poprzednim przykładzie, można stwierdzić, że specyfikacje **Standard_DS14v2** i **Standard_DS14 4v2** są identyczne z wyjątkiem Vcpu. Sufiks **-4v2** na końcu **Standard_DS14 4v2** rozmiaru maszyny wskazuje liczbę aktywnych Vcpu. Ponieważ koszty licencjonowania programu SQL Server są powiązane z liczby rdzeni, to znacznie ograniczyć koszty maszyny Wirtualnej w scenariuszach, w którym nie są wymagane dodatkowe Vcpu. Jest to przykład i istnieje wiele rozmiarów maszyn z ograniczeniami Vcpu, które są oznaczone symbolem tego wzorca sufiks. Aby uzyskać więcej informacji, zobacz w blogu [o nowe rozmiary maszyny Wirtualnej Azure więcej pracy ekonomicznego bazy danych](https://azure.microsoft.com/blog/announcing-new-azure-vm-sizes-for-more-cost-effective-database-workloads/).

### <a name="shut-down-your-vm-when-possible"></a>Zamknij maszyny Wirtualnej, jeśli to możliwe

Jeśli korzystasz z dowolnych zadań, które nie są uruchamiane w sposób ciągły, należy wziąć pod uwagę zamykanie maszyny wirtualnej w okresach nieaktywne. Płaci się wyłącznie za użyte zasoby.

Na przykład jeśli próbujesz po prostu limit programu SQL Server na maszynie Wirtualnej platformy Azure, czy chcesz naliczenie opłat przypadkowo pozostawienie systemem tygodni. Jeden rozwiązaniem jest użycie [funkcji automatycznego zamykania](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/).

![Autoshutdown maszyny Wirtualnej SQL](./media/virtual-machines-windows-sql-server-pricing-guidance/sql-vm-auto-shutdown.png)

Automatyczne zamknięcie jest częścią większy zestaw podobne funkcje oferowane przez [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab).

Dla innych przepływów pracy, należy wziąć pod uwagę automatycznie zamykanie i ponowne uruchamianie maszyn wirtualnych platformy Azure z rozwiązaniem do obsługi skryptów, takich jak [usługi Automatyzacja Azure](https://azure.microsoft.com/services/automation/).

> [!IMPORTANT]
> Zamykanie i cofanie przydziału maszyny Wirtualnej jest jedynym sposobem uniknięcia naliczania opłat. Po prostu zatrzymanie lub zamykania maszyny Wirtualnej za pomocą apletu Opcje zasilania nadal użycie zostaną naliczone opłaty powiązane.

## <a name="next-steps"></a>Kolejne kroki

Ogólne Azure cennik wskazówki, zobacz [uniknąć kosztów nieoczekiwany rozliczenia Azure i kosztów zarządzania](../../../billing/billing-getting-started.md). Najnowsze maszyn wirtualnych cennika, w tym program SQL Server, zobacz Azure maszyny Wirtualnej Azure cennikiem dla [maszyn wirtualnych systemu Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) i [maszyn wirtualnych systemu Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

Omówienie programu SQL Server uruchomionego na maszynach wirtualnych platformy Azure zobacz następujące artykuły:

- [Omówienie programu SQL Server na maszynach wirtualnych systemu Windows](virtual-machines-windows-sql-server-iaas-overview.md)
- [Omówienie programu SQL Server na maszynach wirtualnych systemu Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md)