---
title: Ponowne hostowanie komputera mainframe na maszynach wirtualnych platformy Azure
description: Hostuj ponownie obciążenia komputerów mainframe, takie jak systemy oparte na ibm z wykorzystaniem maszyn wirtualnych (VM) na platformie Microsoft Azure.
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: ccdeefabeedfca4959239696361ccce0bc6c1c78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76289802"
---
# <a name="mainframe-rehosting-on-azure-virtual-machines"></a>Ponowne hostowanie komputera mainframe na maszynach wirtualnych platformy Azure

Migracja obciążeń ze środowisk mainframe do chmury umożliwia modernizację infrastruktury i często pozwala zaoszczędzić na kosztach. Wiele obciążeń można przenieść na platformę Azure z uwzględnieniem tylko drobnych zmian kodu, takich jak aktualizacja nazw baz danych.

Ogólnie rzecz biorąc, termin *mainframe* oznacza duży system komputerowy. W szczególności zdecydowana większość obecnie używanych to serwery IBM System Z lub systemy ibm plug-compatible, które uruchamiają systemy MVS, DOS, VSE, OS/390 lub z/OS.

Maszyna wirtualna platformy Azure (VM) służy do izolowania zasobów dla określonej aplikacji i zarządzania nimi w jednym wystąpieniu. Komputery mainframe, takie jak IBM z/OS, używają w tym celu partycji logicznych (LPARS). Komputer typu mainframe może używać jednego LPAR dla regionu CICS ze skojarzonymi programami COBOL oraz oddzielnego lparu dla bazy danych IBM Db2. Typowa [aplikacja n-warstwowa na platformie Azure](/azure/architecture/reference-architectures/n-tier/n-tier-sql-server) wdraża maszyny wirtualne platformy Azure w sieci wirtualnej, która może być podzielona na podsieci dla każdej warstwy.

Maszyny wirtualne platformy Azure można uruchomić środowiska emulacji mainframe i kompilatory, które obsługują scenariusze lift-and-shift. Programowanie i testowanie są często jednymi z pierwszych obciążeń do migracji z mainframe do środowiska deweloper/test platformy Azure. Typowe składniki serwera, które można emulować obejmują proces transakcji online (OLTP), wsadowe i systemy pozyskiwania danych, jak pokazano na poniższym rysunku.

![Środowiska emulacji na platformie Azure umożliwiają uruchamianie systemów opartych na z/OS.](media/01-overview.png)

Niektóre obciążenia komputerów mainframe można migrować na platformę Azure ze względną łatwością, podczas gdy inne można ponownie hostować na platformie Azure przy użyciu rozwiązania partnerskiego. Aby uzyskać szczegółowe wskazówki dotyczące wybierania rozwiązania partnerskiego, [centrum migracji mainframe platformy Azure](https://azure.microsoft.com/migration/mainframe/) może ci pomóc.

## <a name="mainframe-migration"></a>Migracja komputera mainframe

Rehost, odbudować, wymienić, lub przejść na emeryturę? IaaS lub PaaS? Aby określić właściwą strategię migracji dla aplikacji mainframe, zobacz przewodnik [migracji mainframe](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview) w Centrum architektury platformy Azure.

## <a name="micro-focus-rehosting-platform"></a>Platforma micro Focus rehosting

Micro Focus Enterprise Server jest jedną z największych platform hostowania mainframe dostępnych. Można go używać uruchomić obciążeń z/OS na tańszej platformie x86 na platformie Azure.

Aby rozpocząć:

- [Instalowanie programu Enterprise Server i Enterprise Developer na platformie Azure](./microfocus/set-up-micro-focus-azure.md)
- [Konfigurowanie cics bankdemo dla deweloperów korporacyjnych na platformie Azure](./microfocus/demo.md)
- [Uruchamianie serwera enterprise server w kontenerze platformy Docker na platformie Azure](./microfocus/run-enterprise-server-container.md)


## <a name="tmaxsoft-openframe-on-azure"></a>TmaxSoft OpenFrame na platformie Azure

TmaxSoft OpenFrame to popularne rozwiązanie do rehostingu mainframe stosowane w scenariuszach lift-and-shift. Środowisko OpenFrame na platformie Azure jest odpowiednie do tworzenia, tworzenia, testowania lub obciążeń produkcyjnych.

Aby rozpocząć:

- [Wprowadzenie do TmaxSoft OpenFrame](./tmaxsoft/get-started.md)
- [Pobierz ebook](https://azure.microsoft.com/resources/install-tmaxsoft-openframe-on-azure/)

## <a name="ibm-zdt-120"></a>IBM zD&T 12.0

Środowisko programistyczne i testowe IBM Z (IBM zD&T) konfiguruje środowisko nieprodukcyjne na platformie Azure, którego można używać do tworzenia, testowania i demonstracji aplikacji opartych na z/OS.

Środowisko emulacji na platformie Azure może obsługiwać różne rodzaje wystąpień Z za pośrednictwem dystrybucji kontrolowanych przez deweloperów aplikacji (ADCDs). ZD&T Personal Edition, zD&T Parallel Sysplex i zD&T Enterprise Edition na platformie Azure i usłudze Azure Stack.

Aby rozpocząć:

- [Konfigurowanie programu IBM zD&T 12.0 na platformie Azure](./ibm/install-ibm-z-environment.md)
- [Konfigurowanie ADCD na zD&T](./ibm/demo.md)

## <a name="ibm-db2-purescale-on-azure"></a>IBM DB2 pureScale na platformie Azure

Środowisko IBM DB2 pureScale zapewnia klaster bazy danych dla platformy Azure. Nie jest identyczny z oryginalnym środowiskiem, ale zapewnia podobną dostępność i skalę jak IBM DB2 dla z/OS działający w konfiguracji Równoległego Sysplexa.

Aby rozpocząć, zobacz [IBM DB2 pureScale on Azure](/azure/virtual-machines/linux/ibm-db2-purescale-azure).

## <a name="considerations"></a>Zagadnienia do rozważenia

Podczas migracji obciążeń komputerów mainframe do infrastruktury platformy Azure jako usługi (IaaS) można wybierać spośród kilku typów skalowalnych zasobów obliczeniowych na żądanie, w tym maszyn wirtualnych platformy Azure. Platforma Azure oferuje szeroką gamę maszyn wirtualnych z [systemem Linux](/azure/virtual-machines/linux/overview) i [Windows.](/azure/virtual-machines/windows/overview)

### <a name="compute"></a>Wystąpienia obliczeniowe

Moc obliczeniowa platformy Azure porównuje się korzystnie do pojemności komputera mainframe. Jeśli myślisz o przeniesieniu obciążenia komputera mainframe na platformę Azure, porównaj metrykę komputera mainframe z milionem instrukcji na sekundę (MIPS) z procesorami wirtualnymi. 

Dowiedz się, jak [przenieść obliczenia mainframe na platformę Azure.](./concepts/mainframe-compute-azure.md)

### <a name="high-availability-and-failover"></a>Wysoka dostępność i przewijamy awaryjnie

Platforma Azure oferuje oparte na zobowiązaniach umowy dotyczące poziomu usług (SLA). Dostępność wielu dziewiątek jest domyślna, a ławy SLA można zoptymalizować za pomocą lokalnej lub opartej na geograficznej replikacji usług. Pełna treść [umowy SLA dotyczącej usługi Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) wyjaśnia w całości kwestię gwarantowanej dostępności platformy Azure.

Dzięki usłudze Azure IaaS, takiej jak maszyna wirtualna, określone funkcje systemowe zapewniają obsługę trybu failover — na przykład wystąpienia klastrowania trybu failover i zestawy dostępności. Podczas korzystania z platformy Azure jako usługi (PaaS) zasobów, platforma obsługuje pracy awaryjnej automatycznie. Przykłady obejmują [usługę Azure SQL Database](/azure/sql-database/sql-database-technical-overview) i usługę Azure [Cosmos DB](/azure/cosmos-db/introduction).

### <a name="scalability"></a>Skalowalność

Komputery mainframe zazwyczaj skalują się w górę, podczas gdy środowiska chmury są skalowane w poziomie. Platforma Azure oferuje szeroką gamę rozmiarów [systemów Linux](/azure/virtual-machines/linux/sizes) i [Windows,](/azure/virtual-machines/windows/sizes) aby spełnić Twoje potrzeby. Chmura również skaluje się w górę lub w dół, aby dopasować dokładne specyfikacje użytkownika. Moc obliczeniowa, pamięć masowa i usługi [są skalowane](/azure/architecture/best-practices/auto-scaling) na żądanie w modelu rozliczeniowym opartym na użyciu.

### <a name="storage"></a>Magazyn

W chmurze masz szereg elastycznych, skalowalnych opcji pamięci masowej i płacisz tylko za to, czego potrzebujesz. [Usługa Azure Storage](/azure/storage/common/storage-introduction) oferuje ogromnie skalowalny magazyn obiektów dla obiektów danych, usługę systemu plików dla chmury, niezawodny magazyn wiadomości i magazyn NoSQL. W przypadku maszyn wirtualnych dyski zarządzane i niezarządzane zapewniają trwały, bezpieczny magazyn dysku.

Dowiedz się, jak [przenieść magazyn mainframe na platformę Azure](./concepts/mainframe-storage-azure.md).

### <a name="backup-and-recovery"></a>Tworzenie i przywracanie kopii zapasowych

Utrzymanie własnej witryny odzyskiwania po awarii może być kosztowną propozycją. Platforma Azure oferuje łatwe do wdrożenia i ekonomiczne opcje [tworzenia kopii zapasowych,](/azure/backup/backup-introduction-to-azure-backup) [odzyskiwania](/azure/site-recovery/site-recovery-overview)i [nadmiarowości](/azure/storage/common/storage-redundancy) na poziomie lokalnym lub regionalnym lub za pomocą nadmiarowości geograficznej.

## <a name="azure-government-for-mainframe-migrations"></a>Platforma Azure Dla migracji mainframe

Wiele podmiotów sektora publicznego chciałoby przenieść swoje aplikacje mainframe na bardziej nowoczesną, elastyczną platformę. Microsoft Azure Government to fizycznie oddzielone wystąpienie globalnej platformy Microsoft Azure — przeznaczone dla federalnych, stanowych i lokalnych systemów rządowych. Zapewnia światowej klasy usługi w zakresie bezpieczeństwa, ochrony i zgodności specjalnie dla agencji rządowych Stanów Zjednoczonych i ich partnerów.

Platforma Azure Government uzyskała tymczasowy urząd do działania (P-ATO) dla FedRAMP High Impact dla systemów, które potrzebują tego typu środowiska.

Aby rozpocząć, pobierz [chmurę Microsoft Azure Dla Instytucji Rządowych dla aplikacji mainframe](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/en-us/).

## <a name="next-steps"></a>Następne kroki

Poproś naszych [partnerów](partner-workloads.md) o pomoc w migracji lub ponownym hostie aplikacji mainframe. 

Zobacz też:

- [Białe księgi na temat tematów mainframe](mainframe-white-papers.md)
- [Migracja komputera mainframe](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Rozwiązywanie problemów](/azure/virtual-machines/troubleshooting/)
- [Demistyfikacja elementu mainframe z migracją na platformę Azure](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: /ibm/install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md
