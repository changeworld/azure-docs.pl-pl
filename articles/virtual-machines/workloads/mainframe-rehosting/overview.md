---
title: Mainframe rehosting na maszynach wirtualnych platformy Azure | Dokumentacja firmy Microsoft
description: Ponowne hostowanie obciążeń mainframe, takie jak systemu IBM Z używaniem maszyn wirtualnych (VM) w systemie Microsoft Azure.
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
tags: ''
keywords: ''
ms.openlocfilehash: 8be0ebc486739f8826e8a1d5a5307a219ba71b6f
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/01/2019
ms.locfileid: "57192721"
---
# <a name="mainframe-rehosting-on-azure-virtual-machines"></a>Mainframe rehosting na maszynach wirtualnych platformy Azure

Migrowanie obciążeń z komputera mainframe środowisk w chmurze umożliwia modernizuj swoją infrastrukturę i często zaoszczędzić na kosztach. Wiele obciążeń można przenieść tylko drobnych zmian w kodzie, takie jak aktualizowanie nazwy baz danych na platformie Azure.

Termin *mainframe* zazwyczaj odnosi się do systemu komputerowego dużych, ale większość Trwa wdrażanie serwerów Z systemem IBM lub IBM plug-compatible komputerów z systemami MVS, DOS, VSE, system operacyjny/390 lub z/OS.

Maszyny wirtualnej (VM) platformy Azure służy do izolowania i zarządzanie zasobami dla określonej aplikacji w pojedynczym wystąpieniu. Komputery mainframe firmy IBM z/OS np. użycia partycje logiczne (LPARS), w tym celu. Mainframe korzystać LPAR jeden region CICS z skojarzone z nim programy COBOL i oddzielnych LPAR dla bazy danych programu IBM Db2. Typowa [aplikacji n warstwowej na platformie Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/n-tier-sql-server) służy do wdrażania maszyn wirtualnych platformy Azure w sieci wirtualnej, którą można podzielić na podsieci dla każdej warstwy.

Maszyny wirtualne platformy Azure można uruchomić środowiska emulacji mainframe i kompilatory, które obsługują scenariusze lift-and-shift. Projektowanie i testowanie są często wśród pierwszych obciążeń do migracji z komputera mainframe do środowiska usługi Azure dev/test. Typowe składniki serwera, które może emulować obejmują przetwarzania transakcji online (OLTP), partii i systemy pozyskiwania danych, jak przedstawiono na poniższym rysunku.

![Emulacja środowisk na platformie Azure umożliwiają systemami systemu z/OS.](media/01-overview.png)

Niektórych obciążeń mainframe można migrować na platformę Azure przy zachowaniu, podczas gdy inne osoby mogą rehosted na platformie Azure przy użyciu rozwiązania partnerskiego. Aby uzyskać szczegółowe wskazówki dotyczące wybierania rozwiązaniem partnera [Centrum migracji komputera Mainframe Azure](https://azure.microsoft.com/migration/mainframe/) może pomóc.

## <a name="set-up-devtest-environment-using-a-micro-focus-rehosting-platform"></a>Konfigurowanie środowiska tworzenia i testowania za pomocą platformy rehosting Micro fokus

Micro fokus Enterprise Server jest jednym z największych komputera mainframe rehosting dostępnych platform. Umożliwia uruchamianie obciążeń z/OS na mniej kosztowne x86 platform na platformie Azure.

Aby rozpocząć pracę, zobacz następujące artykuły:

- [Instalowanie Micro Focus Enterprise Server w wersji 4.0 i Enterprise, Developer 4.0 na platformie Azure](./microfocus/set-up-micro-focus-on-azure.md)
- [Konfigurowanie Micro fokus CICS BankDemo Micro fokus Enterprise Developer 4.0 na platformie Azure](./microfocus/demo.md)

## <a name="tmaxsoft-openframe-on-azure"></a>TmaxSoft OpenFrame na platformie Azure

TmaxSoft OpenFrame to popularne komputera mainframe, rehosting rozwiązanie, które można łatwo posiadane zasoby komputera mainframe lift- and -shift je na platformie Azure. Środowisko OpenFrame na platformie Azure nadaje się do rozwoju, pokazów, testów lub obciążeń produkcyjnych.

Aby rozpocząć pracę, Pobierz [zainstalować TmaxSoft OpenFrame na platformie Azure](https://azure.microsoft.com/resources/install-tmaxsoft-openframe-on-azure/) książki elektronicznej.

## <a name="set-up-a-devtest-environment-using-ibm-z-devtest-120"></a>Konfigurowanie środowiska tworzenia i testowania przy użyciu systemu IBM Z tworzenia i testowania 12.0

Programowanie Z IBM i środowiska testowego (IBM zD & T) konfiguruje poza środowiskiem produkcyjnym na platformie Azure, który służy do tworzenia, testowania i demonstracji aplikacji opartych na z/OS.

Emulacja środowiska na platformie Azure umożliwia hostowanie wielu wystąpień Z za pośrednictwem dystrybucji kontrolowane przez deweloperów aplikacji (ADCDs). Możesz uruchomić zD & T w wersji osobistej, zD & T Sysplex równoległych i zD & T Enterprise Edition na platformie Azure i usługi Azure Stack.

Aby rozpocząć pracę, zobacz następujące artykuły:

-   [Konfigurowanie IBM zD & T 12.0 na platformie Azure](./ibm/install-ibm-z-environment.md)
-   [Konfigurowanie ADCD na zD & T](./ibm/demo.md)

## <a name="migrate-ibm-db2-purescale-to-azure"></a>Migrowanie programu IBM DB2 pureScale na platformę Azure

Środowisko pureScale IBM DB2 zapewnia klaster bazy danych dla platformy Azure o wysokiej dostępności i skalowalności w systemach operacyjnych Linux. Chociaż nie są identyczne do oryginalnego środowiska, pureScale IBM DB2 w systemie Linux udostępnia podobne wysokiej dostępności i skalowalności jako IBM DB2 dla działającej w konfiguracji równoległej Sysplex na mainframe z/OS.

Aby rozpocząć pracę, zobacz [pureScale IBM DB2 na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/linux/ibm-db2-purescale-azure).

## <a name="considerations"></a>Zagadnienia do rozważenia

Podczas migracji komputera mainframe obciążeń do infrastruktury platformy Azure jako usługa (IaaS), można wybrać spośród kilku typów na żądanie, skalowalnych zasobów obliczeniowych, w tym maszyn wirtualnych platformy Azure. Platforma Azure oferuje szeroką gamę [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/overview) i [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/overview) maszyn wirtualnych.

### <a name="high-availability-and-failover"></a>Wysoka dostępność i tryb failover

Platforma Azure oferuje na podstawie zobowiązania umowy poziomu usług (SLA), gdzie wiele 9s dostępności jest zachowaniem domyślnym, zoptymalizowanej przy użyciu replikacji lokalne lub oparte na lokalizacji geograficznej usług. Pełna treść [umowy SLA dotyczącej usługi Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) wyjaśnia w całości kwestię gwarantowanej dostępności platformy Azure.

Dla usługi Azure IaaS, takie jak maszyny wirtualne, pracy awaryjnej opiera się na funkcje właściwe dla systemu, takie jak wystąpienia klastra trybu failover i [zestawy dostępności](https://docs.microsoft.com/azure/virtual-machines/windows/regions-and-availability#availability-sets). Kiedy używasz platformy Azure jako usługa (PaaS) zasoby, takie jak [usługi Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview) i [usługi Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/introduction), platforma automatycznie obsługuje tryb failover.

### <a name="scalability"></a>Skalowalność

Komputery mainframe zazwyczaj skalowanie w górę, podczas skalowania środowiska chmury w poziomie. Platforma Azure oferuje szeroką gamę [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes) i [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) rozmiarów do własnych potrzeb. Chmura również skalowany w górę lub w dół do specyfikacji użytkownika dokładnego dopasowania. Obliczenia mocy obliczeniowej, magazynu i usług [skalowania](https://docs.microsoft.com/azure/architecture/best-practices/auto-scaling) na żądanie w ramach modelu rozliczania użycia.

### <a name="storage"></a>Magazyn

W chmurze masz kilka opcji magazynu elastyczną i skalowalną, i płacisz tylko za potrzebne. [Usługa Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-introduction) oferuje wysoce skalowalny magazyn obiektów dla obiektów danych, usługę systemu plików w chmurze, niezawodny Magazyn obsługi komunikatów i magazyn danych NoSQL. W przypadku maszyn wirtualnych dyski zarządzane i niezarządzane zapewniają trwały, bezpieczny magazyn dyskowy.

### <a name="backup-and-recovery"></a>Tworzenie i przywracanie kopii zapasowych

Utrzymywania własnej lokacji odzyskiwania po awarii może być kosztowne oferty. Platforma Azure oferuje łatwe Implementowanie i ekonomiczne opcje [kopii zapasowej](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup), [odzyskiwania](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview), i [nadmiarowości](https://docs.microsoft.com/azure/storage/common/storage-redundancy) poziomie lokalnych i regionalnych, lub za pośrednictwem nadmiarowości geograficznej.

## <a name="azure-government-for-mainframe-migrations"></a>Platforma Azure Government dla migracji komputera mainframe

Jednostki sektora publicznego wiele chcielibyśmy Przenieś swoje aplikacje mainframe na bardziej nowoczesny, elastyczną platformę. Microsoft Azure dla instytucji rządowych jest fizycznie oddzielone wystąpienie usług cloud services, oparte na globalnej platformy Microsoft Azure, ale spakowane do systemów rządowych Federalnej, stanowej i lokalnej. Zapewnia światowej klasy zabezpieczenia, ochronę i zgodnością, mające specjalnie dla agencji rządowych Stanów Zjednoczonych i partnerów tych instytucji.

Platforma Azure Government zdobytych certyfikację Provisional Authority to Operate (P-ATO) o dużym znaczeniu FedRAMP dla systemów, które potrzebują tego typu środowiska. 

Aby rozpocząć pracę, Pobierz [chmura platformy Microsoft Azure Government dla aplikacji mainframe](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/en-us/).

## <a name="learn-more"></a>Dowiedz się więcej

Jeśli rozważasz migracji komputera mainframe, nasz rozbudowany [partnera](partner-workloads.md) ekosystem to może pomóc Ci. Aby uzyskać szczegółowe wskazówki dotyczące wybierania rozwiązania partnerskiego, zapoznaj się [Alliance modernizacji platformy](https://www.platformmodernization.org/pages/mainframe.aspx).

Zobacz też:

-   [Migracja komputera mainframe](https://docs.microsoft.com/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
-   [Rozwiązywanie problemów](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/)
-   [Demystifying mainframe do migracji na platformę Azure](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/en-us/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-on-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: /ibm/install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md
