---
title: Co to jest Azure Backup?
description: Zawiera omówienie usługi Azure Backup i jak przyczynia się do ciągłości biznesowej i strategii odzyskiwania po awarii (BCDR).
ms.topic: overview
ms.date: 04/24/2019
ms.custom: mvc
ms.openlocfilehash: cf48090b2c32f0c3a1c8170873cb8d6a771fe21f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240296"
---
# <a name="what-is-the-azure-backup-service"></a>Co to jest usługa Azure Backup?

Usługa Azure Backup udostępnia proste, bezpieczne i ekonomiczne rozwiązania do wykonywania kopii zapasowych danych i odzyskiwania ich z chmury platformy Microsoft Azure.

> [!VIDEO https://www.youtube.com/embed/elODShatt-c]

## <a name="what-can-i-back-up"></a>Dla jakich danych mogę tworzyć kopię zapasową?

- **Lokalnie** — tworzenie kopii zapasowych plików, folderów, stanu systemu przy użyciu [agenta usług marsjańskich platformy Microsoft Azure](backup-support-matrix-mars-agent.md). Możesz też użyć agenta programu DPM lub usługi Azure Backup Server (MABS), aby chronić lokalne maszyny wirtualne[(Hyper-V](back-up-hyper-v-virtual-machines-mabs.md) i [VMWare)](backup-azure-backup-server-vmware.md)i inne [obciążenia lokalne](backup-mabs-protection-matrix.md)
- **Maszyny wirtualne** - platformy Azure[Wykonaj kopię zapasową całych maszyn wirtualnych systemu Windows/Linux](backup-azure-vms-introduction.md) (przy użyciu rozszerzeń kopii zapasowych) lub wykonaj kopię zapasową plików, folderów i stanu systemu za pomocą [agenta MARS](backup-azure-manage-mars.md).
- **Usługa Azure Files udostępnia na** - [koncie magazynu udostępnianie kopii zapasowej udziałów plików azure](backup-afs.md)
- **Sql Server na maszynach wirtualnych** -  platformy Azure[Tworzenie kopii zapasowych baz danych programu SQL Server uruchomionych na maszynach wirtualnych platformy Azure](backup-azure-sql-database.md)
- **Bazy danych SAP HANA w maszynach wirtualnych platformy Azure** - [kopiuje bazy danych SAP HANA uruchomione na maszynach wirtualnych platformy Azure](backup-azure-sap-hana-database.md)

![Omówienie tworzenia kopii zapasowych platformy Azure](./media/backup-overview/azure-backup-overview.png)

## <a name="why-use-azure-backup"></a>Dlaczego warto używać usługi Azure Backup?

Usługa Azure Backup oferuje następujące kluczowe korzyści:

- **Odciążanie lokalnej kopii zapasowej:** usługa Azure Backup oferuje proste rozwiązanie do tworzenia kopii zapasowych zasobów lokalnych w chmurze. Uzyskaj krótkoterminowe i długoterminowe kopie zapasowe bez konieczności wdrażania złożonych lokalnych rozwiązań do wykonywania kopii zapasowych.
- **Tworzenie kopii zapasowych maszyn wirtualnych usługi Azure IaaS:** usługa Azure Backup zapewnia niezależne i izolowane kopie zapasowe w celu ochrony przed przypadkowym zniszczeniem oryginalnych danych. Kopie zapasowe są przechowywane w magazynie usług odzyskiwania z wbudowanym zarządzaniem punktami odzyskiwania. Konfiguracja i skalowalność są proste, kopie zapasowe są zoptymalizowane i można łatwo przywrócić w razie potrzeby.
- **Łatwe skalowanie** — usługa Azure Backup używa dostępnej mocy i nieograniczonej skali chmury Azure do zapewniania wysokiej dostępności, bez narzutu na konserwację lub monitorowanie.
- **Uzyskaj nieograniczony transfer danych:** usługa Azure Backup nie ogranicza ilości przesyłanych danych przychodzących lub wychodzących ani nie pobiera opłat za przesyłane dane.
  - Dane wychodzące to dane transferowane z magazynu usługi Recovery Services podczas operacji przywracania.
  - Jeśli wykonasz początkową kopię zapasową w trybie offline przy użyciu usługi Azure Import/Export do importowania dużych ilości danych, istnieje koszt skojarzony z danymi przychodzącymi.  [Dowiedz się więcej](backup-azure-backup-import-export.md).
- **Bezpieczeństwo danych:** Usługa Azure Backup zapewnia rozwiązania do zabezpieczania danych [podczas przesyłania](backup-azure-security-feature.md) i [przechowywania.](backup-azure-security-feature-cloud.md)
- **Scentralizowane monitorowanie i zarządzanie:** usługa Azure Backup zapewnia [wbudowane funkcje monitorowania i alertów](backup-azure-monitoring-built-in-monitor.md) w magazynie usług odzyskiwania. Te możliwości są dostępne bez dodatkowej infrastruktury zarządzania. Możesz również zwiększyć skalę monitorowania i raportowania za [pomocą usługi Azure Monitor.](backup-azure-monitoring-use-azuremonitor.md)
- **Uzyskaj kopie zapasowe spójne z aplikacjami:** Kopia zapasowa zgodna z aplikacją oznacza, że punkt odzyskiwania ma wszystkie wymagane dane do przywrócenia kopii zapasowej. Usługa Azure Backup udostępnia kopie zapasowe spójne z aplikacjami, które zapewniają, że dodatkowe poprawki nie są wymagane do przywrócenia danych. Przywracanie danych spójnych na poziomie aplikacji skraca czas przywracania, co pozwala szybko powrócić do stanu roboczego.
- **Zachowaj dane krótko- i długoterminowe:** można używać [magazynów usług odzyskiwania](backup-azure-recovery-services-vault-overview.md) do długoterminowego i długoterminowego przechowywania danych.
- **Automatyczne zarządzanie magazynem** — hybrydowe środowiska często wymagają heterogenicznego magazynu — znajdującego się w części lokalnie, a w części w chmurze. W przypadku usługi Azure Backup korzystanie z lokalnych urządzeń magazynowych nie jest kosztowne. Usługa Azure Backup automatycznie przydziela pojemność i zarządza magazynem kopii zapasowych, przy czym użytkownik płaci tylko za faktyczne użycie. Więc płacisz tylko za magazyn, który zużywasz. [Dowiedz się więcej](https://azure.microsoft.com/pricing/details/backup) o cenach.
- **Wiele opcji magazynowania** — usługa Azure Backup oferuje dwa typy replikacji, aby zapewnić wysoką dostępność magazynu/danych.
  - [Magazyn lokalnie nadmiarowy (LRS)](../storage/common/storage-redundancy-lrs.md) replikuje dane trzy razy (tworzy trzy kopie danych) w jednostce skalowania magazynu w centrum danych. Wszystkie kopie danych istnieją w tym samym regionie. Magazyn LRS to ekonomiczna opcja ochrony danych przed awariami sprzętu lokalnego.
  - [Magazyn geograficznie nadmiarowy (GRS)](../storage/common/storage-redundancy-grs.md) jest ustawieniem domyślnym i zalecaną opcją replikacji. Magazyn GRS replikuje dane do regionu pomocniczego (setki kilometrów od lokalizacji głównej danych źródłowych). GRS kosztuje więcej niż LRS, ale GRS zapewnia wyższy poziom trwałości danych, nawet jeśli występuje awaria regionalna.

## <a name="next-steps"></a>Następne kroki

- [Przejrzyj](backup-architecture.md) architekturę i składniki w różnych scenariuszach wykonywania kopii zapasowych.
- [Sprawdź](backup-support-matrix.md) wymagania i ograniczenia dotyczące pomocy technicznej dotyczące tworzenia kopii zapasowych i [tworzenia kopii zapasowych maszyn wirtualnych platformy Azure.](backup-support-matrix-iaas.md)
