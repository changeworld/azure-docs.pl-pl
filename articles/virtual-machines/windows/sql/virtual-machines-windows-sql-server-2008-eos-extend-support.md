---
title: Rozszerzanie obsługi programu SQL Server 2008 & 2008 R2
description: Rozszerz obsługę programów SQL Server 2008 i SQL Server 2008 R2, migrując wystąpienie programu SQL Server na platformę Azure lub kupując rozszerzoną pomoc techniczną w celu utrzymania wystąpień w środowisku lokalnym.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/08/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: faaff9e9ad5982efac6409f2284158a3d0711331
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548408"
---
# <a name="extend-support-for-sql-server-2008-and-sql-server-2008-r2-with-azure"></a>Rozszerzanie obsługi programów SQL Server 2008 i SQL Server 2008 R2 za pomocą platformy Azure

Sql Server 2008 i SQL Server 2008 R2 osiągnęły [koniec cyklu życia wsparcia (EOS).](https://www.microsoft.com/sql-server/sql-server-2008) Ponieważ wielu klientów nadal korzysta z obu wersji, udostępniamy kilka opcji, aby nadal utrzymywać pomoc techniczną. Lokalne wystąpienia programu SQL Server można migrować na maszyny wirtualne platformy Azure,migrować do bazy danych SQL azure lub pozostawać w środowisku lokalnym i kupować rozszerzone aktualizacje zabezpieczeń.

W przeciwieństwie do wystąpienia zarządzanego migracja do maszyny Wirtualnej platformy Azure nie wymaga ponownej certyfikacji aplikacji. W przeciwieństwie do pobytów lokalnych, otrzymasz bezpłatne rozszerzone poprawki zabezpieczeń, migrując do maszyny Wirtualnej platformy Azure.

W dalszej części tego artykułu przedstawiono zagadnienia dotyczące migracji wystąpienia programu SQL Server do maszyny Wirtualnej platformy Azure.

Aby uzyskać więcej informacji na temat zakończenia opcji pomocy technicznej, zobacz [Koniec pomocy technicznej](/sql/sql-server/end-of-support/sql-server-end-of-life-overview).

## <a name="provisioning"></a>Inicjowanie obsługi

W witrynie Azure Marketplace dostępny jest obraz **programu SQL Server 2008 R2 w systemie Windows Server 2008 R2.**

Klienci korzystający z programu SQL Server 2008 będą musieli samodzielnie zainstalować program SQL Server 2008 R2 lub uaktualnić go do programu SQL Server 2008 R2. Podobnie klienci systemu Windows Server 2008 będą musieli wdrożyć maszynę wirtualną z niestandardowej sieci VHD lub uaktualnić do systemu Windows Server 2008 R2.

Obrazy wdrożone za pośrednictwem portalu Azure Marketplace są fabrycznie zainstalowane z fabrycznie zainstalowanym rozszerzeniem IaaS. Rozszerzenie SQL IaaS jest wymagane dla elastycznego licencjonowania i automatycznego instalowania poprawek. Klienci, którzy wdrażają samodzielnie zainstalowane maszyny wirtualne, będą musieli ręcznie zainstalować rozszerzenie IaaS SQL. Rozszerzenie SQL IaaS nie jest obsługiwane w systemie Windows Server 2008.

> [!NOTE]
> Mimo że sql server **tworzenie** i **zarządzanie** bloków będzie współpracować z sql server 2008 R2 obrazu w portalu azure, następujące funkcje nie są _obsługiwane:_ Automatyczne kopie zapasowe, integracja usługi Azure Key Vault, usługi R i konfiguracji magazynu.

## <a name="licensing"></a>Licencjonowanie
Wdrożenia programu SQL Server 2008 R2 zgodnie z rzeczywistym wdrożeniem można przekonwertować na [korzyść hybrydową platformy Azure.](https://azure.microsoft.com/pricing/hybrid-benefit/)

Aby przekonwertować licencję opartą na programie Software Assurance (SA) na płatność zgodnie z rzeczywistym użyciem, klienci powinni zarejestrować się u [dostawcy zasobów maszyny](virtual-machines-windows-sql-register-with-resource-provider.md)Wirtualnej SQL. Po tej rejestracji typ licencji SQL będzie wymienny między korzyścią hybrydową platformy Azure a płatnością zgodnie z rzeczywistymi instrukcjami.

Samodzielnie zainstalowane wystąpienia programu SQL Server 2008 lub SQL Server 2008 R2 na maszynie Wirtualnej platformy Azure mogą rejestrować się u dostawcy zasobów maszyn wirtualnych SQL i konwertować ich typ licencji na płatność zgodnie z rzeczywistym użyciem.

## <a name="migration"></a>Migracja
Wystąpienia programu EOS SQL Server można migrować do maszyny Wirtualnej platformy Azure za pomocą metod ręcznego tworzenia kopii zapasowych/przywracania. Jest to najbardziej najpopularniejsza metoda migracji z lokalnego do maszyny Wirtualnej platformy Azure.

### <a name="azure-site-recovery"></a>Azure Site Recovery

W przypadku migracji zbiorczej zaleca się usługę [Azure Site Recovery.](/azure/site-recovery/site-recovery-overview) Dzięki usłudze Azure Site Recovery klienci mogą replikować całą maszynę wirtualną, w tym program SQL Server z lokalnego do maszyny Wirtualnej platformy Azure.

Sql Server wymaga spójnych z aplikacjami migawek usługi Azure Site Recovery, aby zagwarantować odzyskiwanie. Usługa Azure Site Recovery obsługuje migawki spójne z aplikacjami z co najmniej 1-godzinnym interwałem. Minimalny cel punktu odzyskiwania (RPO) możliwy dla programu SQL Server z migracjami usługi Azure Site Recovery wynosi 1 godzinę. Cel czasu odzyskiwania (RTO) to 2 godziny plus czas odzyskiwania programu SQL Server.

### <a name="database-migration-service"></a>Usługa migracji bazy danych

[Usługa migracji bazy danych](/azure/dms/dms-overview) jest opcją dla klientów, którzy migrują z lokalnego do maszyny Wirtualnej platformy Azure przez uaktualnienie programu SQL Server do wersji 2012 lub nowszej.

## <a name="disaster-recovery"></a>Odzyskiwanie po awarii

Rozwiązania do odzyskiwania po awarii dla programu EOS SQL Server na maszynie Wirtualnej platformy Azure są następujące:

- **Kopie zapasowe programu SQL Server:** Użyj usługi Azure Backup, aby chronić system EOS SQL Server 2008 i 2008 R2 przed oprogramowaniem wymuszającym okup, przypadkowym usunięciem i uszkodzeniem dzięki 15-minutowej funkcji RPO i odzyskiwania w czasie. Aby uzyskać więcej informacji, zobacz [ten artykuł](https://docs.microsoft.com/azure/backup/sql-support-matrix#scenario-support).
- **Wysyłka dziennika:** Można utworzyć replikę wysyłki dziennika w innej strefie lub regionie platformy Azure z ciągłymi przywracania, aby zmniejszyć rto. Należy ręcznie skonfigurować wysyłkę dziennika.
- **Usługa Azure Site Recovery:** Można replikować maszynę wirtualną między strefami i regionami za pośrednictwem replikacji usługi Azure Site Recovery. SQL Server wymaga migawek spójnych z aplikacjami, aby zagwarantować odzyskiwanie w przypadku awarii. Usługa Azure Site Recovery oferuje co najmniej 1 godzinę obiektu RPO i 2-godzinny (plus czas odzyskiwania programu SQL Server) dla odzyskiwania po awarii programu EOS SQL Server.

## <a name="security-patching"></a>Łatanie zabezpieczeń
Rozszerzone aktualizacje zabezpieczeń dla maszyn wirtualnych programu SQL Server są dostarczane za pośrednictwem kanałów Microsoft Update po zarejestrowaniu maszyny Wirtualnej programu SQL Server u [dostawcy zasobów maszyny Wirtualnej](virtual-machines-windows-sql-register-with-resource-provider.md)SQL. Poprawki można pobrać ręcznie lub automatycznie.

Opcja *Automatyczne stosowanie poprawek* jest domyślnie włączona. Automatyczne stosowanie poprawek umożliwia platformie Azure automatyczne stosowanie poprawek programu SQL Server i systemu operacyjnego. Można określić dzień tygodnia, czas i czas trwania okna konserwacji, jeśli jest zainstalowane rozszerzenie IaaS programu SQL Server. Platforma Azure stosuje poprawki w tym oknie obsługi. Harmonogram okna obsługi korzysta z ustawień regionalnych godziny maszyny wirtualnej.  Aby uzyskać więcej informacji, zobacz [Automatyczne instalowanie poprawek dla programu SQL Server na maszynach wirtualnych platformy Azure](virtual-machines-windows-sql-automated-patching.md).


## <a name="next-steps"></a>Następne kroki

Migrowanie maszyny Wirtualnej programu SQL Server na platformę Azure:

* [Migrowanie bazy danych programu SQL Server do programu SQL Server na maszynie wirtualnej platformy Azure](virtual-machines-windows-migrate-sql.md)

Wprowadzenie do programu SQL Server na maszynach wirtualnych platformy Azure:

* [Tworzenie maszyny wirtualnej z programem SQL Server w witrynie Azure Portal](quickstart-sql-vm-create-portal.md)

Uzyskaj odpowiedzi na często zadawane pytania dotyczące maszyn wirtualnych programu SQL Server:

* [Często zadawane pytania dotyczące programu SQL Server na maszynach wirtualnych platformy Azure](virtual-machines-windows-sql-server-iaas-faq.md)

Dowiedz się więcej o zakończeniu opcji pomocy technicznej i rozszerzonych aktualizacjach zabezpieczeń:

* [Koniec pomocy technicznej](/sql/sql-server/end-of-support/sql-server-end-of-life-overview) & [Rozszerzone aktualizacje zabezpieczeń](/sql/sql-server/end-of-support/sql-server-extended-security-updates)