---
title: Rozszerzona obsługa programu SQL Server 2008 i SQL Server 2008 R2 z platformą Azure
description: Dowiedz się, jak rozszerzenie obsługi programu SQL Server 2008 i SQL Server 2008 R2, migracja wystąpienia programu SQL Server na platformie Azure lub zakup rozszerzonej pomocy technicznej, aby zachować wystąpień w środowisku lokalnym.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/08/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 62261e46dc4744597acd10c32f0a835f4a597d4d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66243971"
---
# <a name="extend-support-for-sql-server-2008-and-sql-server-2008-r2-with-azure"></a>Rozszerzona obsługa programu SQL Server 2008 i SQL Server 2008 R2 z platformą Azure

Program SQL Server 2008 i SQL Server 2008 R2 zarówno zbliża się [koniec cykl życia pomocy technicznej (EOS)](https://www.microsoft.com/sql-server/sql-server-2008). Ponieważ nadal wielu naszych klientów korzystają obie wersje, zapewniamy kilka opcji, aby nadal korzystać z pomocy technicznej. Możesz migracji lokalnych wystąpień programu SQL Server w usłudze Azure virtual machines (VMs), migrację do usługi Azure SQL Database, lub pozostać w środowisku lokalnym i Kup aktualizacje zabezpieczeń.

W odróżnieniu od za pomocą wystąpienia zarządzanego, migracji na Maszynie wirtualnej platformy Azure nie wymaga recertifying aplikacji. I w przeciwieństwie do przy użyciu pozostając w środowisku lokalnym, zostanie wyświetlony poprawki rozszerzone zabezpieczenia przy użyciu funkcji migracji na Maszynie wirtualnej platformy Azure. 

W pozostałej części tego artykułu zawiera zagadnienia dotyczące migracji wystąpienia programu SQL Server na Maszynie wirtualnej platformy Azure. 

## <a name="provisioning"></a>Inicjowanie obsługi 

Płatność za rzeczywiste użycie jest `SQL Server 2008 R2 on Windows Server 2008 R2` obrazu, które są dostępne w witrynie Azure marketplace. 

Klienci, którzy są w programie SQL Server 2008 musisz albo samodzielnie instalacji lub uaktualnienia do programu SQL Server 2008 R2. Podobnie w systemie Windows Server 2008 albo konieczne będzie wdrożenie ich maszyny Wirtualnej z niestandardowego pliku VHD lub uaktualnienia do systemu Windows Server 2008 R2. 

Obrazy wdrożone za pośrednictwem portalu Marketplace są dostarczane z wstępnie zainstalowane rozszerzenie SQL IaaS. Rozszerzenie SQL IaaS jest wymagana dla automatyczne stosowanie poprawek i elastyczne Licencjonowanie. Wdrażanie maszyn wirtualnych z własnym zainstalowanych klientów, należy ręcznie zainstalować rozszerzenie SQL IaaS. Rozszerzenie SQL IaaS nie jest obsługiwane w Windows 2008. 

  > [!NOTE]
  > Podczas gdy program SQL Server `Create` i `Manage` bloków będą działać przy użyciu obrazu programu SQL Server 2008 R2 w witrynie Azure portal, są następujące funkcje _nieobsługiwane_: Automatyczne kopie zapasowe, integracji usługi Azure Key Vault, usługi języka R i konfiguracji magazynu.

## <a name="licensing"></a>Licencjonowanie
Płatność za rzeczywiste użycie programu SQL Server 2008R2 wdrożeń można przekonwertować na [korzyść użycia hybrydowego platformy Azure (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/).

Aby przekonwertować licencji Software Assurance (SA) na podstawie płatność za rzeczywiste użycie, klienci powinni się rejestrować przy maszyny Wirtualnej SQL [dostawcy zasobów](virtual-machines-windows-sql-ahb.md#register-sql-server-vm-with-sql-resource-provider). Po zarejestrowaniu dostawcy zasobów maszyny Wirtualnej SQL typu licencji SQL będzie wymienne między AHB i płatność za rzeczywiste użycie. 

Samodzielnie zainstalowanych wystąpień programu SQL Server 2008 lub SQL Server 2008 R2, na maszynie Wirtualnej platformy Azure można rejestrować za pomocą dostawcy zasobów bazy danych SQL i konwertowanie ich typ licencji na płatność za rzeczywiste użycie.

## <a name="migration"></a>Migracja
Można migrować wystąpień EOS SQL Server na Maszynie wirtualnej platformy Azure przy użyciu metod ręcznych/przywracania kopii zapasowej. jest to najbardziej typowa metoda migracji ze środowiska lokalnego na Maszynie wirtualnej platformy Azure.

### <a name="azure-site-recovery"></a>Azure Site Recovery

Zbiorcze migracji, zaleca się [usługi Azure Site Recovery](/azure/site-recovery/site-recovery-overview) usługi. Usługa Azure Site Recovery klientów można replikować całą maszynę Wirtualną, w tym programu SQL Server ze środowiska lokalnego na Maszynie wirtualnej platformy Azure.

Serwer SQL wymaga migawek spójności aplikacji usługi Azure Site Recovery w celu zagwarantowania odzyskiwania; i usługi Azure Site Recovery obsługuje migawek spójności aplikacji za pomocą minimalny interwał na 1 godzinę. Możliwe dla programu SQL Server za pomocą usługi Azure Site Recovery migracje cel punktu odzyskiwania minimalną jest 1 godzinę i czas RTO wynosi 2 godziny, a także czas odzyskiwania serwera SQL.

### <a name="database-migration-service"></a>Usługa migracji bazy danych

[Database Migration Service](/azure/dms/dms-overview) jest opcja dla klientów, jeśli migrowanie ze środowiska lokalnego na maszynie Wirtualnej platformy Azure po uaktualnieniu programu SQL Server do programu SQL Server 2012 lub nowszym.

## <a name="disaster-recovery"></a>Odzyskiwanie po awarii

Rozwiązania odzyskiwania po awarii dla EOS programu SQL Server na maszynie Wirtualnej platformy Azure są następujące:

- **Tworzenie kopii zapasowych programu SQL Server**: Tworzenie kopii zapasowych programu SQL Server może służyć do odzyskiwania programu SQL Server w przypadku regionalne lub awarie strefy. Ponieważ zarządzanych funkcji Kopia zapasowa nie jest obsługiwana dla EOS programu SQL Server, klienci będą musieli ręcznie wykonać kopie zapasowe.
- **Wysyłanie dziennika**: Replika wysyłania dziennika można utworzyć w innej strefie lub regionu platformy Azure za pomocą ciągłego przeprowadzać operacje przywracania, aby zmniejszyć czas RTO. Klienci będą musieli ręcznie skonfigurować wysyłania dziennika.
- **Azure Site Recovery**: Teraz można replikować maszyny Wirtualnej między strefami i regionami przy użyciu replikacji usługi Azure Site Recovery. Serwer SQL wymaga migawki spójne z aplikacji zagwarantować odzyskiwania na wypadek awarii. Usługa Azure Site Recovery zapewnia minimalne RPO 1-godzinnego i 2-godzinnego + czas odzyskiwania serwera SQL cel czasu odzyskiwania dla EOS programu SQL Server odzyskiwania po awarii.

## <a name="security-patching"></a>Stosowanie poprawek zabezpieczeń
Aktualizacje zabezpieczeń dla maszyn wirtualnych programu SQL Server będą dostarczane za pośrednictwem kanałów Microsoft Update po maszynę Wirtualną programu SQL Server został zarejestrowany przy użyciu języka SQL [dostawcy zasobów](virtual-machines-windows-sql-ahb.md#register-sql-server-vm-with-sql-resource-provider). Poprawki albo mogą być pobierane, ręcznie lub automatycznie. 

Opcja **Automatyczne stosowanie poprawek** jest domyślnie włączona. Automatyczne stosowanie poprawek umożliwia platformie Azure automatyczne stosowanie poprawek programu SQL Server i systemu operacyjnego. Jeśli zainstalowano rozszerzenie programu SQL IaaS, można określić dzień tygodnia, godzinę i czas trwania okna obsługi. Platforma Azure stosuje poprawki w tym oknie obsługi. Harmonogram okna obsługi korzysta z ustawień regionalnych godziny maszyny wirtualnej.  Aby uzyskać więcej informacji, zobacz [Automated Patching for SQL Server in Azure Virtual Machines](virtual-machines-windows-sql-automated-patching.md) (Automatyczne stosowanie poprawek programu SQL Server w usłudze Azure Virtual Machines).


## <a name="next-steps"></a>Kolejne kroki

Migrowanie maszyn wirtualnych SQL Server na platformę Azure

* [Migrowanie bazy danych programu SQL Server do programu SQL Server na maszynie wirtualnej platformy Azure](virtual-machines-windows-migrate-sql.md)

Wprowadzenie do programu SQL Server na maszynach wirtualnych platformy Azure:

* [Tworzenie maszyny wirtualnej z programem SQL Server w witrynie Azure Portal](quickstart-sql-vm-create-portal.md)

Uzyskaj odpowiedzi na często zadawane pytania dotyczące maszyn wirtualnych SQL:

* [Często zadawane pytania dotyczące programu SQL Server w usłudze Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-faq.md)
