---
title: Mainframe rehosting na maszynach wirtualnych platformy Azure
description: Ponowne hostowanie obciążeń mainframe, takie jak systemu IBM Z używaniem maszyn wirtualnych (VM) w systemie Microsoft Azure.
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: c1d7b52bdce77ca108781a999a8a85b3e3fca0b8
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "61487486"
---
# <a name="mainframe-rehosting-on-azure-virtual-machines"></a>Mainframe rehosting na maszynach wirtualnych platformy Azure

Migrowanie obciążeń z komputera mainframe środowisk w chmurze umożliwia modernizuj swoją infrastrukturę i często zaoszczędzić na kosztach. Wiele obciążeń można przenieść tylko drobnych zmian w kodzie, takie jak aktualizowanie nazwy baz danych na platformie Azure.

Ogólnie rzecz biorąc, określenie *mainframe* oznacza, że system komputerowy dużych. W szczególności większość obecnie w użyciu są serwery Z systemem IBM lub IBM plug-compatible komputerach MVS, DOS, VSE, system operacyjny/390 lub z/OS.

Maszyny wirtualnej (VM) platformy Azure służy do izolowania i zarządzanie zasobami dla określonej aplikacji w pojedynczym wystąpieniu. Komputery mainframe firmy IBM z/OS np. użycia partycje logiczne (LPARS), w tym celu. Mainframe korzystać LPAR jeden region CICS z skojarzone z nim programy COBOL i oddzielnych LPAR dla bazy danych programu IBM Db2. Typowa [aplikacji n warstwowej na platformie Azure](/azure/architecture/reference-architectures/n-tier/n-tier-sql-server) służy do wdrażania maszyn wirtualnych platformy Azure w sieci wirtualnej, którą można podzielić na podsieci dla każdej warstwy.

Maszyny wirtualne platformy Azure można uruchomić środowiska emulacji mainframe i kompilatory, które obsługują scenariusze lift-and-shift. Projektowanie i testowanie są często wśród pierwszych obciążeń do migracji z komputera mainframe do środowiska usługi Azure dev/test. Typowe składniki serwera, które może emulować obejmują przetwarzania transakcji online (OLTP), partii i systemy pozyskiwania danych, jak przedstawiono na poniższym rysunku.

![Emulacja środowisk na platformie Azure umożliwiają systemami systemu z/OS.](media/01-overview.png)

Niektórych obciążeń mainframe można migrować na platformę Azure przy zachowaniu, podczas gdy inne osoby mogą rehosted na platformie Azure przy użyciu rozwiązania partnerskiego. Aby uzyskać szczegółowe wskazówki dotyczące wybierania rozwiązaniem partnera [Centrum migracji komputera Mainframe Azure](https://azure.microsoft.com/migration/mainframe/) może pomóc.

## <a name="mainframe-migration"></a>Migracja komputera mainframe

Rehost, ponownie skompilować, Zamień lub wycofać? IaaS lub PaaS? Aby określić strategii migracji odpowiednie dla twojej aplikacji mainframe, zobacz [migracji komputera Mainframe](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview) przewodnik w Centrum architektury platformy Azure.

## <a name="micro-focus-rehosting-platform"></a>Platforma wczesnych rehosting koncentracji uwagi

Micro fokus Enterprise Server jest jednym z największych komputera mainframe rehosting dostępnych platform. Umożliwia uruchamianie obciążeń z/OS na mniej kosztowne x86 platform na platformie Azure.

Aby rozpocząć pracę:

- [Instalowanie serwera Enterprise i Enterprise Developer na platformie Azure](./microfocus/set-up-micro-focus-azure.md)
- [Konfigurowanie CICS BankDemo dla dewelopera przedsiębiorstwa na platformie Azure](./microfocus/demo.md)
- [Uruchamianie serwera przedsiębiorstwa w kontenerze platformy Docker na platformie Azure](./microfocus/run-enterprise-server-container.md)


## <a name="tmaxsoft-openframe-on-azure"></a>TmaxSoft OpenFrame na platformie Azure

TmaxSoft OpenFrame jest rozwiązaniem rehosting popularnych mainframe używane w scenariuszach lift-and-shift. Środowisko OpenFrame na platformie Azure nadaje się do rozwoju, pokazów, testów lub obciążeń produkcyjnych.

Aby rozpocząć pracę:

- [Rozpoczynanie pracy z usługą TmaxSoft OpenFrame](./tmaxsoft/get-started.md)
- [Pobierz książkę elektroniczną](https://azure.microsoft.com/resources/install-tmaxsoft-openframe-on-azure/)

## <a name="ibm-zdt-120"></a>IBM zD&T 12.0

Programowanie Z IBM i środowiska testowego (IBM zD & T) konfiguruje poza środowiskiem produkcyjnym na platformie Azure, który służy do tworzenia, testowania i demonstracji aplikacji opartych na z/OS.

Emulacja środowiska na platformie Azure można hostować różne rodzaje Z wystąpień za pośrednictwem dystrybucji kontrolowane przez deweloperów aplikacji (ADCDs). Możesz uruchomić zD & T w wersji osobistej, zD & T Sysplex równoległych i zD & T Enterprise Edition na platformie Azure i usługi Azure Stack.

Aby rozpocząć pracę:

- [Konfigurowanie IBM zD & T 12.0 na platformie Azure](./ibm/install-ibm-z-environment.md)
- [Konfigurowanie ADCD na zD & T](./ibm/demo.md)

## <a name="ibm-db2-purescale-on-azure"></a>IBM DB2 pureScale na platformie Azure

Środowisko pureScale IBM DB2 zapewnia klaster bazy danych na platformie Azure. Nie jest taka sama jak oryginalne środowisko, ale zapewnia podobne dostępności i skalowalności jako IBM DB2 w przypadku uruchamiania w Instalatorze Sysplex równolegle z/OS.

Aby rozpocząć pracę, zobacz [pureScale IBM DB2 na platformie Azure](/azure/virtual-machines/linux/ibm-db2-purescale-azure).

## <a name="considerations"></a>Zagadnienia do rozważenia

Podczas migracji komputera mainframe obciążeń do infrastruktury platformy Azure jako usługa (IaaS), można wybrać spośród kilku typów na żądanie, skalowalnych zasobów obliczeniowych, w tym maszyn wirtualnych platformy Azure. Platforma Azure oferuje szeroką gamę [Linux](/azure/virtual-machines/linux/overview) i [Windows](/azure/virtual-machines/windows/overview) maszyn wirtualnych.

### <a name="compute"></a>Wystąpienia obliczeniowe

Pojemność mainframe favorably porównuje moc obliczeniową platformy Azure. Jeśli zamierzasz przenoszenie obciążenia komputera mainframe na platformie Azure, porównywać metryki mainframe instrukcji jeden milion na sekundę (MIPS) na procesory wirtualne. 

Dowiedz się, jak [przenieść mainframe obliczeniowych na platformę Azure](./concepts/mainframe-compute-azure.md).

### <a name="high-availability-and-failover"></a>Wysoka dostępność i tryb failover

Platforma Azure oferuje na podstawie zobowiązania umowy poziomu usług (SLA). Dostępność wielu nines jest ustawieniem domyślnym i umów SLA można zoptymalizować za pomocą replikacji lokalne lub oparte na lokalizacji geograficznej usług. Pełna treść [umowy SLA dotyczącej usługi Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) wyjaśnia w całości kwestię gwarantowanej dostępności platformy Azure.

Za pomocą usługi Azure IaaS, np. maszyna wirtualna, funkcje właściwe dla systemu zapewniają obsługę trybu failover — na przykład klastra trybu failover wystąpienia i [zestawy dostępności](/azure/virtual-machines/windows/regions-and-availability#availability-sets). Korzystając z platformy Azure jako zasoby usługi (PaaS), platforma automatycznie obsługuje tryb failover. Przykłady obejmują [usługi Azure SQL Database](/azure/sql-database/sql-database-technical-overview) i [usługi Azure Cosmos DB](/azure/cosmos-db/introduction).

### <a name="scalability"></a>Skalowalność

Komputery mainframe zazwyczaj skalowanie w górę, podczas skalowania środowiska chmury w poziomie. Platforma Azure oferuje szeroką gamę [Linux](/azure/virtual-machines/linux/sizes) i [Windows](/azure/virtual-machines/windows/sizes) rozmiarów do własnych potrzeb. Chmura również skalowany w górę lub w dół do specyfikacji użytkownika dokładnego dopasowania. Obliczenia mocy obliczeniowej, magazynu i usług [skalowania](/azure/architecture/best-practices/auto-scaling) na żądanie w ramach modelu rozliczania użycia.

### <a name="storage"></a>Magazyn

W chmurze masz kilka opcji magazynu elastyczną i skalowalną, i płacisz tylko za potrzebne. [Usługa Azure Storage](/azure/storage/common/storage-introduction) oferuje wysoce skalowalny magazyn obiektów dla obiektów danych, usługę systemu plików w chmurze, niezawodny Magazyn obsługi komunikatów i magazyn danych NoSQL. W przypadku maszyn wirtualnych dyski zarządzane i niezarządzane zapewniają trwały, bezpieczny magazyn dyskowy.

Dowiedz się, jak [przenieść magazyn komputera mainframe na platformie Azure](./concepts/mainframe-storage-azure.md).

### <a name="backup-and-recovery"></a>Tworzenie i przywracanie kopii zapasowych

Utrzymywania własnej lokacji odzyskiwania po awarii może być kosztowne oferty. Platforma Azure oferuje łatwe Implementowanie i ekonomiczne opcje [kopii zapasowej](/azure/backup/backup-introduction-to-azure-backup), [odzyskiwania](/azure/site-recovery/site-recovery-overview), i [nadmiarowości](/azure/storage/common/storage-redundancy) poziomie lokalnych i regionalnych, lub za pośrednictwem nadmiarowości geograficznej.

## <a name="azure-government-for-mainframe-migrations"></a>Platforma Azure Government dla migracji komputera mainframe

Jednostki sektora publicznego wiele chcielibyśmy Przenieś swoje aplikacje mainframe na bardziej nowoczesny, elastyczną platformę. Microsoft Azure dla instytucji rządowych jest fizycznie oddzielone wystąpienie globalnej platformy Microsoft Azure — spakowane do systemów rządowych Federalnej, stanowej i lokalnej. Zapewnia światowej klasy zabezpieczenia, ochronę i zgodnością, mające specjalnie dla agencji rządowych Stanów Zjednoczonych i partnerów tych instytucji.

Platforma Azure Government zdobytych certyfikację Provisional Authority to Operate (P-ATO) o dużym znaczeniu FedRAMP dla systemów, które potrzebują tego typu środowiska.

Aby rozpocząć pracę, Pobierz [chmura platformy Microsoft Azure Government dla aplikacji mainframe](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/en-us/).

## <a name="next-steps"></a>Kolejne kroki

Poproś naszych [partnerów](partner-workloads.md) aby pomóc Ci przy migracji lub ponowne hostowanie Twoich aplikacji mainframe. Aby uzyskać szczegółowe wskazówki dotyczące wybierania rozwiązania partnerskiego, zobacz [Alliance modernizacji platformy](https://www.platformmodernization.org/pages/mainframe.aspx) witryny sieci Web.

Zobacz też:

- [Oficjalne dokumenty dotyczące zagadnień związanych z komputera mainframe](mainframe-white-papers.md)
- [Migracja komputera mainframe](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Rozwiązywanie problemów](/azure/virtual-machines/troubleshooting/)
- [Demystifying mainframe do migracji na platformę Azure](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: /ibm/install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md
