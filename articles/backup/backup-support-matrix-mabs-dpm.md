---
title: Mabs & system center dpm wsparcie matrycy
description: W tym artykule podsumowano obsługę usługi Azure Backup podczas korzystania z programu Microsoft Azure Backup Server (MABS) lub Programu DPM programu System Center do tworzenia kopii zapasowych w środowisku lokalnym i zasobów maszyny wirtualnej platformy Azure.
ms.date: 02/17/2019
ms.topic: conceptual
ms.openlocfilehash: 6664f7b226b75b364fd1c83f2abc56b5a275eff9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77582657"
---
# <a name="support-matrix-for-backup-with-microsoft-azure-backup-server-or-system-center-dpm"></a>Macierz obsługi kopii zapasowej za pomocą programu Microsoft Azure Backup Server lub Programu DPM programu System Center

Za pomocą [usługi Azure Backup](backup-overview.md) można wykonać kopię zapasową maszyn i obciążeń lokalnych oraz maszyn wirtualnych platformy Azure. W tym artykule podsumowano ustawienia i ograniczenia pomocy technicznej dotyczące tworzenia kopii zapasowych maszyn przy użyciu programu Microsoft Azure Backup Server (MABS) lub Programu Menedżer ochrony danych (System Center Data Protection Manager) i usługi Azure Backup.

## <a name="about-dpmmabs"></a>Informacje o programach DPM/MABS

[System Center DPM](https://docs.microsoft.com/system-center/dpm/dpm-overview?view=sc-dpm-1807) to rozwiązanie dla przedsiębiorstw, które konfiguruje, ułatwia i zarządza tworzeniem kopii zapasowych i odzyskiwaniem maszyn i danych przedsiębiorstwa. Jest to część pakietu [produktów System Center.](https://www.microsoft.com/cloud-platform/system-center-pricing)

MABS to produkt serwerowy, który może służyć do utworzenia kopii zapasowej lokalnych serwerów fizycznych, maszyn wirtualnych i aplikacji działających na nich.

MABS jest oparty na System Center DPM i zapewnia podobną funkcjonalność z kilkoma różnicami:

- Do uruchomienia mabs nie jest wymagana żadna licencja system center.
- Dla mabs i programu DPM azure zapewnia długoterminowe przechowywanie kopii zapasowych. Ponadto program DPM umożliwia utworzenie kopii zapasowej danych w celu długoterminowego przechowywania na taśmie. MABS nie udostępnia tej funkcji.
- Można wywkprzeć podstawowy serwer programu DPM za pomocą pomocniczego serwera programu DPM. Serwer pomocniczy będzie chronić bazę danych serwera podstawowego i repliki źródła danych przechowywane na serwerze podstawowym. W razie awarii serwera podstawowego serwer pomocniczy może nadal chronić obciążenia chronione przez serwer podstawowy do czasu, aż serwer podstawowy stanie się ponownie dostępny.  MABS nie udostępnia tej funkcji.

System MABS jest pobierany z [Centrum pobierania Microsoft](https://www.microsoft.com/download/details.aspx?id=57520). Można go uruchomić lokalnie lub na maszynie Wirtualnej platformy Azure.

Program DPM i MABS obsługują tworzenie kopii zapasowych wielu różnych aplikacji oraz systemów operacyjnych serwerów i klientów. Zapewniają one wiele scenariuszy tworzenia kopii zapasowych:

- Można wykonać kopię zapasową na poziomie komputera za pomocą kopii zapasowej w stanie systemu lub bez systemu.
- Można wywrzeć kopie zapasowe określonych woluminów, udziałów, folderów i plików.
- Możesz zrobić na niej kopii zapasowej określone aplikacje, korzystając ze zoptymalizowanych ustawień obsługujących aplikacje.

## <a name="dpmmabs-backup"></a>Kopia zapasowa programu DPM/MABS

Kopia zapasowa przy użyciu programu DPM/MABS i usługi Azure Backup działa w następujący sposób:

1. Na każdym komputerze, na który zostanie utworzona kopia zapasowa, jest zainstalowany agent ochrony programu DPM/MABS.
1. Kopie zapasowe maszyn i aplikacji są archiwizowane w pamięci lokalnej w programie DPM/MABS.
1. Agent usług odzyskiwania platformy Microsoft Azure (MARS) jest zainstalowany na serwerze programu DPM/mabs.
1. Agent MARS kopie zapasowe dysków programu DPM/MABS do magazynu usług odzyskiwania kopii zapasowej na platformie Azure przy użyciu usługi Azure Backup.

Więcej informacji:

- [Dowiedz się więcej](backup-architecture.md#architecture-back-up-to-dpmmabs) o architekturze MABS.
- [Sprawdź, co jest obsługiwane](backup-support-matrix-mars-agent.md) dla agenta MARS.

## <a name="supported-scenarios"></a>Obsługiwane scenariusze

**Scenariusz** | **Agent** | **Lokalizacja**
--- | --- | ---
**3.0.2019** | Agent ochrony programu DPM/MABS działa na komputerach, których chcesz poprzeć.<br/><br/> Agent MARS na serwerze DPM/MABS.<br/> Minimalna wersja agenta usług odzyskiwania platformy Microsoft Azure lub agenta usługi Azure Backup wymagana do włączenia tej funkcji to 2.0.8719.0.  | Program DPM/MABS musi działać lokalnie.

## <a name="supported-deployments"></a>Obsługiwane wdrożenia

Program DPM/MABS można wdrożyć w sposób podsumowany w poniższej tabeli.

**wdrażania** | **Pomoc techniczna** | **Szczegóły**
--- | --- | ---
**Wdrożony lokalnie** | Serwer fizyczny<br/><br/>Maszyna wirtualna funkcji Hyper-V<br/><br/> Maszyna wirtualna VMware | Jeśli program DPM/MABS jest zainstalowany jako maszyna wirtualna VMware, jest on akcyzowy tylko maszyny wirtualne vmware i obciążeń, które są uruchomione na tych maszynach wirtualnych.
**Wdrożony jako maszyna wirtualna usługi Azure Stack** | Tylko mabs | Programu DPM nie można użyć do utworzenia kopii zapasowej maszyn wirtualnych usługi Azure Stack.
**Wdrożony jako maszyna wirtualna platformy Azure** | Chroni maszyny wirtualne i obciążenia platformy Azure, które są uruchomione na tych maszynach wirtualnych | Program DPM/MABS działający na platformie Azure nie może wyw.

## <a name="supported-mabs-and-dpm-operating-systems"></a>Obsługiwane systemy operacyjne MABS i DPM

Usługa Azure Backup może wykonać kopię zapasową wystąpień programu DPM/MABS, w których uruchomiony jest dowolny z następujących systemów operacyjnych. W systemach operacyjnych powinny być uruchomione najnowsze dodatki Service Pack i aktualizacje.

**Scenariusz** | **DPM/MABS**
--- | ---
**Usługa MABS na maszynie wirtualnej platformy Azure** |  Centrum danych systemu Windows 2016.<br/><br/> Centrum danych systemu Windows 2019.<br/><br/> Zalecamy rozpoczęcie od obrazu z rynku.<br/><br/> Minimalna Standard_A4_v2 z czterema rdzeniami i 8 GB pamięci RAM.
**Program DPM na maszynie wirtualnej platformy Azure** | System Center 2012 R2 z aktualizacją 3 lub nowszą.<br/><br/> System operacyjny Windows [zgodnie z wymaganiami programu System Center](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1807#dpm-server).<br/><br/> Zalecamy rozpoczęcie od obrazu z rynku.<br/><br/> Minimalna Standard_A4_v2 z czterema rdzeniami i 8 GB pamięci RAM.
**MABS lokalnie** |  MABS w wersji 3 i nowszej: Windows Server 2016 lub Windows Server 2019
**Program DPM lokalnie** | Serwer fizyczny/maszyna wirtualna funkcji Hyper-V: system Center 2012 z dodatku SP1 lub nowszym.<br/><br/> VMware VM: System Center 2012 R2 z aktualizacją 5 lub nowszą.

>[!NOTE]
>Instalowanie serwera kopii zapasowej platformy Azure nie jest obsługiwane w systemie Windows Server Core ani Microsoft Hyper-V Server.

## <a name="management-support"></a>Wsparcie zarządzania

**Problem** | **Szczegóły**
--- | ---
**Instalacji** | Zainstaluj program DPM/MABS na komputerze jednozadaniowym.<br/><br/> Nie należy instalować programu DPM/MABS na kontrolerze domeny, na komputerze z instalacją roli Serwer aplikacji, na komputerze z uruchomionym programem Microsoft Exchange Server lub Menedżerem operacyjnym programu System Center ani na węźle klastra.<br/><br/> [Przejrzyj wszystkie wymagania systemowe programu DPM](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1807#dpm-server).
**Domain** | Program DPM/MABS powinien zostać przyłączony do domeny. Najpierw zainstaluj, a następnie dołącz program DPM/MABS do domeny. Przenoszenie programu DPM/MABS do nowej domeny po wdrożeniu nie jest obsługiwane.
**Magazyn** | Nowoczesna pamięć masowa kopii zapasowych (MBS) jest obsługiwana z programu DPM 2016/MABS v2 i nowszych. Nie jest dostępny dla mabs v1.
**Uaktualnienie mabs** | Można bezpośrednio zainstalować mabs v3 lub uaktualnić do MABS v3 z MABS v2. [Dowiedz się więcej](backup-azure-microsoft-azure-backup.md#upgrade-mabs).
**Przenoszenie mabs** | Przenoszenie usługi MABS na nowy serwer przy zachowaniu magazynu jest obsługiwane, jeśli używasz mbs.<br/><br/> Serwer musi mieć taką samą nazwę jak oryginał. Nie można zmienić nazwy, jeśli chcesz zachować tę samą pulę magazynu i używać tej samej bazy danych MABS do przechowywania punktów odzyskiwania danych.<br/><br/> Będziesz potrzebować kopii zapasowej bazy danych MABS, ponieważ musisz ją przywrócić.

## <a name="mabs-support-on-azure-stack"></a>Obsługa mabs w usłudze Azure Stack

Usługę MABS można wdrożyć na maszynie wirtualnej usługi Azure Stack, dzięki czemu można zarządzać tworzeniem kopii zapasowych maszyn wirtualnych i obciążeń usługi Azure Stack z jednej lokalizacji.

**Składnik** | **Szczegóły**
--- | ---
**MABS na maszynie wirtualnej usługi Azure Stack** | Co najmniej rozmiar A2. Zalecamy rozpoczęcie pracy z obrazem systemu Windows Server 2012 R2 lub Windows Server 2016 z portalu Azure Marketplace.<br/><br/> Nie instaluj niczego innego na maszynie wirtualnej MABS.
**Pamięć masowa MABS** | Użyj osobnego konta magazynu dla maszyny Wirtualnej MABS. Agent MARS działający w usłudze MABS wymaga tymczasowego magazynu dla lokalizacji pamięci podręcznej i przechowywania danych przywróconych z chmury.
**Pula pamięci masowej MABS** | Rozmiar puli magazynu MABS zależy od liczby i rozmiaru dysków dołączonych do maszyny Wirtualnej MABS. Każdy rozmiar maszyny Wirtualnej usługi Azure Stack ma maksymalną liczbę dysków. Na przykład A2 to cztery dyski.
**Retencja MABS** | Nie przechowuj kopii zapasowej danych na lokalnych dyskach MABS przez więcej niż pięć dni.
**Skala MABS w górę** | Aby zwiększyć rozmiar wdrożenia, można zwiększyć rozmiar maszyny Wirtualnej MABS. Na przykład można zmienić z serii A na D.<br/><br/> Można również upewnić się, że regularnie odciążasz dane z tworzeniem kopii zapasowej na platformie Azure. W razie potrzeby można wdrożyć dodatkowe serwery MABS.
**.NET Framework na mabs** | Maszyna wirtualna MABS wymaga zainstalowanego na nim dodatku SP1 programu .NET Framework 3.3 lub nowszego.
**Domena MABS** | Maszyna wirtualna MABS musi być przyłączona do domeny. Użytkownik domeny z uprawnieniami administratora musi zainstalować usługę MABS na maszynie wirtualnej.
**Kopia zapasowa danych maszyny wirtualnej usługi Azure Stack** | Można wywrzeć kopie zapasowe plików, folderów i aplikacji.
**Obsługiwana kopia zapasowa** | Te systemy operacyjne są obsługiwane dla maszyn wirtualnych, które chcesz wyw.<br/><br/> Kanał półroczny systemu Windows Server (centrum danych, przedsiębiorstwo, standard)<br/><br/> Windows Server 2016, Windows Server 2012 R2, Windows Server 2008 R2
**Obsługa programu SQL Server dla maszyn wirtualnych usługi Azure Stack** | Zapasowy kopii zapasowej dodatku SP1 dla programu SQL Server 2016, SQL Server 2014, SQL Server 2012.<br/><br/> Tworzenie kopii zapasowych i odzyskiwanie bazy danych.
**Obsługa programu SharePoint dla maszyn wirtualnych usługi Azure Stack** | Program SharePoint 2016, SharePoint 2013, SharePoint 2010.<br/><br/> Tworzenie kopii zapasowych i odzyskiwanie farmy, bazy danych, fronto-ów i serwera sieci web.
**Wymagania sieciowe dotyczące kopii zapasowej maszyn wirtualnych** | Wszystkie maszyny wirtualne w obciążeniu usługi Azure Stack muszą należeć do tej samej sieci wirtualnej i należeć do tej samej subskrypcji.

## <a name="dpmmabs-networking-support"></a>Obsługa sieci DPM/MABS

### <a name="url-access"></a>Dostęp do adresu URL

Serwer programu DPM/mass potrzebuje dostępu do następujących adresów URL:

- `http://www.msftncsi.com/ncsi.txt`
- *.Microsoft.com
- *.WindowsAzure.com
- *.microsoftonline.com
- *.windows.net

### <a name="azure-expressroute-support"></a>Pomoc techniczna usługi Azure ExpressRoute

Można wywrzeć zapas danych za pomocą usługi Azure ExpressRoute za pomocą publicznej komunikacji równorzędnej (dostępnej dla starych obwodów) i komunikacji równorzędnej firmy Microsoft. Tworzenie kopii zapasowych za pomocą prywatnej komunikacji równorzędnej nie jest obsługiwane.

Komunikacja równorzędna publiczna: zapewnij dostęp do następujących domen/adresów:

* `http://www.msftncsi.com/ncsi.txt`
* `microsoft.com`
* `.WindowsAzure.com`
* `.microsoftonline.com`
* `.windows.net`

W komunikacji równorzędnej firmy Microsoft wybierz następujące usługi/regiony i odpowiednie wartości społeczności:

* Usługa Azure Active Directory (12076:5060)
* Region Platformy Microsoft Azure (w zależności od lokalizacji magazynu usług odzyskiwania)
* Usługa Azure Storage (w zależności od lokalizacji magazynu usług odzyskiwania)

Aby uzyskać więcej informacji, zobacz [wymagania dotyczące routingu usługi ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-routing).

>[!NOTE]
>Publiczne peering jest przestarzałe dla nowych obwodów.

### <a name="dpmmabs-connectivity-to-azure-backup"></a>Łączność programu DPM/MABS z usługą Azure Backup

Łączność z usługą Azure Backup jest wymagana do prawidłowego działania kopii zapasowych, a subskrypcja platformy Azure powinna być aktywna. W poniższej tabeli przedstawiono zachowanie, jeśli te dwie rzeczy nie występują.

**MABS na platformę Azure** | **Subskrypcja** | **Tworzenie/przywracanie kopii zapasowych**
--- | --- | ---
Połączone | Aktywne | Zapasowy kopii zapasowej na dysku DPM/MABS.<br/><br/> Aby przejść do platformy Azure, owa użytkownicy zapasowi.<br/><br/> Przywróć z dysku.<br/><br/> Przywracanie z platformy Azure.
Połączone | Wygasłe/anulowane | Brak kopii zapasowej na dysku lub platformie Azure.<br/><br/> Jeśli subskrypcja wygasła, można przywrócić z dysku lub platformy Azure.<br/><br/> Jeśli subskrypcja zostanie wycofana, nie można przywrócić z dysku lub platformy Azure. Punkty odzyskiwania platformy Azure są usuwane.
Brak łączności przez ponad 15 dni | Aktywne | Brak kopii zapasowej na dysku lub platformie Azure.<br/><br/> Można przywrócić z dysku lub platformy Azure.
Brak łączności przez ponad 15 dni | Wygasłe/anulowane | Brak kopii zapasowej na dysku lub platformie Azure.<br/><br/> Jeśli subskrypcja wygasła, można przywrócić z dysku lub platformy Azure.<br/><br/> Jeśli subskrypcja zostanie wycofana, nie można przywrócić z dysku lub platformy Azure. Punkty odzyskiwania platformy Azure są usuwane.

## <a name="dpmmabs-storage-support"></a>Obsługa pamięci masowej DPM/MABS

Dane, których kopia zapasowa jest w programie DPM/MABS, są przechowywane w magazynie dysku lokalnego.

**Magazyn** | **Szczegóły**
--- | ---
**Mbs** | Nowoczesna pamięć masowa kopii zapasowych (MBS) jest obsługiwana z programu DPM 2016/MABS v2 i nowszych. Nie jest dostępny dla mabs v1.
**Magazyn MABS na maszynie Wirtualnej platformy Azure** | Dane są przechowywane na dyskach platformy Azure, które są dołączone do maszyny Wirtualnej programu DPM/MABS i które są zarządzane w programie DPM/MABS. Liczba dysków, które mogą być używane dla puli magazynu programu DPM/MABS jest ograniczona przez rozmiar maszyny Wirtualnej.<br/><br/> Maszyna wirtualna A2: 4 dyski; Maszyna wirtualna A3: 8 dysków; Maszyna wirtualna A4: 16 dysków o maksymalnym rozmiarze 1 TB dla każdego dysku. Określa całkowitą pulę magazynu kopii zapasowych, która jest dostępna.<br/><br/> Ilość danych, które można uzupełnić, zależy od liczby i rozmiaru dołączonych dysków.
**Przechowywanie danych mabs na maszynie Wirtualnej platformy Azure** | Zaleca się przechowywanie danych przez jeden dzień na dysku platformy Azure programu DPM/MABS i wykonanie kopii zapasowej z programu DPM/MABS w magazynie w celu dłuższego przechowywania. W ten sposób można chronić większą ilość danych, odciążając je do usługi Azure Backup.

### <a name="modern-backup-storage-mbs"></a>Nowoczesna pamięć masowa kopii zapasowych (MBS)

Program DPM 2016/MABS v2 (uruchomiony w systemie Windows Server 2016) i nowszych można korzystać z nowoczesnego magazynu kopii zapasowych (MBS).

- Kopie zapasowe MBS są przechowywane na dysku programu Resilient File System (ReFS).
- Mbs wykorzystuje klonowanie bloków plików ReFS w celu szybszego tworzenia kopii zapasowych i bardziej efektywnego wykorzystania miejsca do magazynowania.
- Podczas dodawania woluminów do lokalnej puli magazynu programu DPM/MABS można je skonfigurować za pomocą liter dysków. Następnie można skonfigurować magazyn obciążenia na różnych woluminach.
- Podczas tworzenia grup ochrony do tworzenia kopii zapasowych danych do programu DPM/MABS, należy wybrać dysk, którego chcesz użyć. Na przykład można przechowywać kopie zapasowe dla SQL lub innych obciążeń operacji We/Wy o wysokiej wydajności na dysku o wysokiej wydajności i przechowywać obciążenia, które są rzadziej archiwizowane na dysku o niższej wydajności.

## <a name="supported-backups-to-mabs"></a>Obsługiwane kopie zapasowe do mabs

Aby uzyskać informacje na temat różnych serwerów i obciążeń, które można chronić za pomocą serwera kopii zapasowych platformy Azure, zobacz [Macierz ochrony serwera kopii zapasowych platformy Azure](https://docs.microsoft.com/azure/backup/backup-mabs-protection-matrix#protection-support-matrix).

## <a name="supported-backups-to-dpm"></a>Obsługiwane kopie zapasowe programu DPM

Aby uzyskać informacje na temat różnych serwerów i obciążeń, które można chronić za pomocą Menedżera ochrony danych, zapoznaj się z artykułem [Co można uzyskać kopii zapasowej programu DPM?](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-2019).

- Obciążenia klastrowane wspierane przez program DPM/MABS powinny znajdować się w tej samej domenie co program DPM/MABS lub w domenie podrzędnej/zaufanej.
- Za pomocą uwierzytelnienia NTLM/certyfikatu można użyć do utworzenia kopii zapasowej danych w niezaufanych domenach lub grupach roboczych.

## <a name="next-steps"></a>Następne kroki

- [Dowiedz się więcej](backup-architecture.md#architecture-back-up-to-dpmmabs) o architekturze MABS.
- [Sprawdź,](backup-support-matrix-mars-agent.md) co jest obsługiwane dla agenta MARS.
- [Konfigurowanie](backup-azure-microsoft-azure-backup.md) serwera MABS.
- [Skonfiguruj program DPM](https://docs.microsoft.com/system-center/dpm/install-dpm?view=sc-dpm-180).
