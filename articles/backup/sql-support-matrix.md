---
title: Macierz obsługi kopii zapasowej usługi Azure dla programu SQL Server Backup na maszynach wirtualnych platformy Azure
description: Zawiera podsumowanie ustawień pomocy technicznej i ograniczeń podczas wykonywania kopii zapasowej programu SQL Server na maszynach wirtualnych platformy Azure za pomocą usługi Azure Backup.
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: 79a7e30ab9240c489a66b547ff85bea7887131b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79410001"
---
# <a name="support-matrix-for-sql-server-backup-in-azure-vms"></a>Macierz pomocy technicznej dla programu SQL Server Backup na maszynach wirtualnych platformy Azure

Za pomocą usługi Azure Backup można wykonać kopię zapasową baz danych programu SQL Server na maszynach wirtualnych platformy Azure hostowanych na platformie chmurowej platformy Microsoft Azure. W tym artykule podsumowano ogólne ustawienia pomocy technicznej i ograniczenia dotyczące scenariuszy i wdrożeń programu SQL Server Backup na maszynach wirtualnych platformy Azure.

## <a name="scenario-support"></a>Obsługa scenariuszy

**Pomoc techniczna** | **Szczegóły**
--- | ---
**Obsługiwane wdrożenia** | Obsługiwane są maszyny wirtualne SQL Marketplace Azure i maszyny wirtualne spoza witryny Marketplace (z ręcznie instalowanym programem SQL Server).
**Obsługiwane obszary geograficzne** | Australia Południowo-Wschodnia (ASE), Australia Wschodnia (AE), Australia Central (AC), Australia Centrala 2 (AC) <br> Brazylia Południowa (BRS)<br> Kanada Central (CNC), Kanada Wschód (CE)<br> Azja Południowo-Wschodnia (SEA), Azja Wschodnia (EA) <br> Wschodnie stany USA (EUS), Wschodnie Stany Zjednoczone 2 (EUS2), Środkowe Stany Zjednoczone (WCUS), Zachodnie Stany Zjednoczone (WUS); Zachodnie stany USA 2 (WUS 2) Północno-Środkowe Stany Zjednoczone (NCUS) Środkowe Stany Zjednoczone (CUS) Południowo-Środkowe Stany Zjednoczone (SCUS) <br> Indie Środkowe (INC), Indie Południowe (INS), Indie Zachód <br> Japonia Wschodnia (JPE), Japonia Zachodnia (JPW) <br> Korea Centralna (KRC), Korea Południowa (KRS) <br> Europa Północna (NE), Europa Zachodnia <br> Wielka Brytania Południowa (UKS), Wielka Brytania Zachodnia (UKW) <br> US Gov Arizona, US Gov Virginia, US Gov Texas, US DoD Central, US DoD East <br> Niemcy Północne, Niemcy Zachodnio-Środkowe <br> Szwajcaria Północ, Szwajcaria Zachód <br> Francja Środkowa <br> Chiny Wschodnie, Chiny Wschód 2, Chiny Północne, Chiny Północne 2
**Obsługiwane systemy operacyjne** | Windows Server 2019, Windows Server 2016, Windows Server 2012, Windows Server 2008 R2 zw. <br/><br/> System Linux nie jest obecnie obsługiwany.
**Obsługiwane wersje programu SQL Server** | SQL Server 2019, SQL Server 2017 jako wyszczególnione na [stronie Cyklu życia produktu wyszukiwania,](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202017)SQL Server 2016 i SPs, jak opisano na [stronie cyklu życia produktu wyszukiwania,](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202016%20service%20pack)SQL Server 2014, SQL Server 2012, SQL Server 2008 R2, SQL Server 2008 <br/><br/> Enterprise, Standard, Web, Developer, Express.
**Obsługiwane wersje platformy .NET** | .NET Framework 4.5.2 lub nowsza zainstalowana na maszynie wirtualnej

## <a name="feature-consideration-and-limitations"></a>Uwzględnienie funkcji i ograniczenia

* Tworzenie kopii zapasowej programu SQL Server można skonfigurować w portalu Azure lub programie **PowerShell**. Nie obsługujemy interfejsu wiersza polecenia.
* Rozwiązanie jest obsługiwane w obu [rodzajach wdrożeń — maszynach wirtualnych](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model) usługi Azure Resource Manager i klasycznych maszynach wirtualnych.
* Maszyna wirtualna z systemem SQL Server wymaga łączności z Internetem, aby uzyskać dostęp do publicznych adresów IP platformy Azure.
* **Wystąpienie klastra trybu failover programu** SQL Server (FCI) nie jest obsługiwane.
* Tworzenie kopii zapasowych i przywracanie operacji dublowania baz danych i migawek bazy danych nie są obsługiwane.
* Użycie więcej niż jednego rozwiązania do tworzenia kopii zapasowych w celu utworzenia kopii zapasowej autonomicznego wystąpienia programu SQL Server lub grupy SQL Always on availability może prowadzić do awarii kopii zapasowej; powstrzymać się od tego.
* Tworzenie kopii zapasowej dwóch węzłów grupy dostępności indywidualnie z tych samych lub różnych rozwiązań, może również prowadzić do awarii kopii zapasowej.
* Usługa Azure Backup obsługuje tylko pełne i tylko do kopiowania pełne typy kopii zapasowych dla baz danych tylko do **odczytu**
* Nie można chronić baz danych z dużą liczbą plików. Maksymalna liczba obsługiwanych plików to **~1000**.  
* W przechowalni można wywrzeć zapasy maksymalnie **~2000** baz danych programu SQL Server. Można utworzyć wiele magazynów w przypadku większej liczby baz danych.
* Można skonfigurować kopię zapasową dla maksymalnie **50** baz danych za jednym zamachem; to ograniczenie pomaga zoptymalizować obciążenia kopii zapasowych.
* Obsługujemy bazy danych o rozmiarze do **2 TB;** dla rozmiarów większych niż mogą pojawić się problemy z wydajnością.
* Aby mieć poczucie, ile baz danych może być chronionych na serwer, należy wziąć pod uwagę czynniki, takie jak przepustowość, rozmiar maszyny Wirtualnej, częstotliwość tworzenia kopii zapasowych, rozmiar bazy danych i tak dalej. [Pobierz](https://download.microsoft.com/download/A/B/5/AB5D86F0-DCB7-4DC3-9872-6155C96DE500/SQL%20Server%20in%20Azure%20VM%20Backup%20Scale%20Calculator.xlsx) planer zasobów, aby obliczyć przybliżoną liczbę baz danych, które mogą mieć na serwerze na podstawie zasobów maszyny Wirtualnej i zasad tworzenia kopii zapasowych.
* Po skonfigurowaniu grup dostępności kopie zapasowe są pobierane z różnych węzłów na podstawie kilku czynników. Zachowanie kopii zapasowej dla grupy dostępności podsumowano poniżej.

### <a name="back-up-behavior-with-always-on-availability-groups"></a>3.00.

Zaleca się, że kopia zapasowa jest skonfigurowana tylko w jednym węźle grupy dostępności (AG). Zawsze konfigurować tworzenie kopii zapasowych w tym samym regionie co węzeł podstawowy. Innymi słowy zawsze trzeba węzeł podstawowy, aby być obecny w regionie, w którym konfigurujesz kopię zapasową. Jeśli wszystkie węzły AG znajdują się w tym samym regionie, w którym jest skonfigurowana kopia zapasowa, nie ma żadnych obaw.

#### <a name="for-cross-region-ag"></a>Dla międzyregionów AG

* Niezależnie od preferencji tworzenia kopii zapasowej kopie zapasowe będą uruchamiane tylko z węzłów, które znajdują się w tym samym regionie, w którym jest skonfigurowana kopia zapasowa. Jest tak, ponieważ kopie zapasowe między regionami nie są obsługiwane. Jeśli masz tylko dwa węzły, a węzeł pomocniczy znajduje się w innym regionie, kopie zapasowe będą nadal uruchamiane z węzła podstawowego (chyba że preferencja tworzenia kopii zapasowej jest "tylko pomocnicza").
* Jeśli węzeł działa awaryjnie w regionie innym niż ten, w którym jest skonfigurowana kopia zapasowa, kopie zapasowe nie powiedzie się w węzłach w regionie awaryjnym.

W zależności od preferencji tworzenia kopii zapasowych i typów kopii zapasowych (pełne/różnicowe/log/copy-only full), kopie zapasowe są pobierane z określonego węzła (podstawowy/pomocniczy).

#### <a name="backup-preference-primary"></a>Preferencja tworzenia kopii zapasowej: Podstawowa

**Typ kopii zapasowej** | **Node**
--- | ---
Pełne | Podstawowy
Różnicowe | Podstawowy
Log |  Podstawowy
Pełna tylko do kopiowania |  Podstawowy

#### <a name="backup-preference-secondary-only"></a>Preferencja tworzenia kopii zapasowej: tylko pomocnicze

**Typ kopii zapasowej** | **Node**
--- | ---
Pełne | Podstawowy
Różnicowe | Podstawowy
Log |  Pomocniczy
Pełna tylko do kopiowania |  Pomocniczy

#### <a name="backup-preference-secondary"></a>Preferencja tworzenia kopii zapasowej: Pomocnicza

**Typ kopii zapasowej** | **Node**
--- | ---
Pełne | Podstawowy
Różnicowe | Podstawowy
Log |  Pomocniczy
Pełna tylko do kopiowania |  Pomocniczy

#### <a name="no-backup-preference"></a>Brak preferencji Kopia zapasowa

**Typ kopii zapasowej** | **Node**
--- | ---
Pełne | Podstawowy
Różnicowe | Podstawowy
Log |  Pomocniczy
Pełna tylko do kopiowania |  Pomocniczy

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak zrobić [zdjętą z bazy danych programu SQL Server,](backup-azure-sql-database.md) która jest uruchomiona na maszynie Wirtualnej platformy Azure.
