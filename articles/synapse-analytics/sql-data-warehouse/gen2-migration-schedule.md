---
title: Migrowanie puli SQL do gen2
description: Instrukcje dotyczące migracji istniejącej puli SQL do gen2 i harmonogram migracji według regionu.
services: synapse-analytics
author: mlee3gsd
ms.author: anjangsh
ms.reviewer: jrasnick
manager: craigg
ms.assetid: 04b05dea-c066-44a0-9751-0774eb84c689
ms.service: synapse-analytics
ms.topic: article
ms.date: 01/21/2020
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 6f2af826473bfd13f8100796a540d41cbedbb037
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631566"
---
# <a name="upgrade-your-sql-pool-to-gen2"></a>Uaktualnianie puli SQL do gen2

Firma Microsoft pomaga zmniejszyć podstawowy koszt uruchomienia puli SQL.  Niższe warstwy obliczeniowe umożliwiające obsługę wymagających zapytań są teraz dostępne dla puli SQL. Przeczytaj pełne ogłoszenie [Niższa obsługa warstwy obliczeniowej dla gen2](https://azure.microsoft.com/blog/azure-sql-data-warehouse-gen2-now-supports-lower-compute-tiers/). Nowa oferta jest dostępna w regionach wymienionych w poniższej tabeli. W obsługiwanych regionach istniejące pule SQL gen1 można uaktualnić do gen2 za pomocą:

- **Proces automatycznej aktualizacji:** Automatyczne uaktualnienia nie uruchamiają się, gdy tylko usługa będzie dostępna w regionie.  Po uruchomieniu automatycznych uaktualnień w określonym regionie, indywidualne uaktualnienia magazynu danych będą miały miejsce podczas wybranego harmonogramu konserwacji.
- [**Samodzielne uaktualnienie do Gen2:**](#self-upgrade-to-gen2) Można kontrolować, kiedy uaktualnić, wykonując samodzielne uaktualnienie do Gen2. Jeśli region nie jest jeszcze obsługiwany, można przywrócić z punktu przywracania bezpośrednio do wystąpienia Gen2 w obsługiwanym regionie.

## <a name="automated-schedule-and-region-availability-table"></a>Tabela automatycznego harmonogramu i dostępności regionu

W poniższej tabeli podsumowano według regionu, kiedy warstwa obliczeniowa Dolna Generacja Gen2 będzie dostępna i kiedy rozpocznie się automatyczne uaktualnianie. Daty mogą ulec zmianie. Sprawdź, kiedy twój region stanie się dostępny.

\*wskazuje, że określony harmonogram dla regionu jest obecnie niedostępny.

| **Region** | **Dostępna dolna generacja 2** | **Rozpoczynają się automatyczne uaktualnienia** |
|:--- |:--- |:--- |
| Kanada Wschodnia |1 czerwca 2020 r. |1 lipca 2020 r. |
| Chiny Wschodnie |\* |\* |
| Chiny Północne |\* |\* |
| Niemcy Środkowe |\* |\* |
| Niemcy Zachodnio-Środkowe |Dostępne |1 maja 2020 r. |
| Indie Zachodnie |Dostępne |1 maja 2020 r.  |

## <a name="automatic-upgrade-process"></a>Proces automatycznej aktualizacji

Na podstawie powyższego wykresu dostępności będziemy planować automatyczne uaktualnienia dla wystąpień Gen1. Aby uniknąć nieoczekiwanych przerw w dostępności puli SQL, automatyczne uaktualnienia zostaną zaplanowane w harmonogramie konserwacji. Możliwość utworzenia nowego wystąpienia Gen1 zostanie wyłączona w regionach poddawanych automatycznemu uaktualnieniu do Gen2. Gen1 zostanie przestarzały po zakończeniu automatycznych uaktualnień. Aby uzyskać więcej informacji na temat harmonogramów, zobacz [Wyświetlanie harmonogramu konserwacji](maintenance-scheduling.md#view-a-maintenance-schedule)

Proces uaktualniania będzie obejmować krótki spadek łączności (około 5 min) podczas ponownego uruchamiania puli SQL.  Po ponownym uruchomieniu puli SQL będzie w pełni dostępna do użycia. Jednak może wystąpić spadek wydajności podczas procesu uaktualniania w dalszym ciągu uaktualniać pliki danych w tle. Łączny okres obniżonej wydajności zależy od rozmiaru plików danych.

Można również przyspieszyć proces uaktualniania pliku danych, uruchamiając [alter index odbudować](sql-data-warehouse-tables-index.md) na wszystkich tabelach magazynu kolumn podstawowych przy użyciu większej SLO i klasy zasobów po ponownym uruchomieniu.

> [!NOTE]
> Alter Index rebuild jest operacją w trybie offline i tabele nie będą dostępne, dopóki przebudowa nie zostanie zakończona.

## <a name="self-upgrade-to-gen2"></a>Samodzielne uaktualnienie do Gen2

Można wybrać do samodzielnego uaktualniania, wykonując następujące kroki w istniejącej puli SQL Gen1. Jeśli zdecydujesz się na samodzielne uaktualnienie, musisz go ukończyć przed rozpoczęciem procesu automatycznego uaktualniania w Twoim regionie. Dzięki temu można uniknąć ryzyka automatycznych uaktualnień powodujących konflikt.

Istnieją dwie opcje podczas samodzielnego uaktualniania.  Można uaktualnić bieżącą pulę SQL w miejscu lub można przywrócić gen1 puli SQL do wystąpienia Gen2.

- [Uaktualnij w miejscu](upgrade-to-latest-generation.md) — ta opcja spowoduje uaktualnienie istniejącej puli SQL Gen1 do Gen2. Proces uaktualniania będzie obejmować krótki spadek łączności (około 5 min) podczas ponownego uruchamiania puli SQL.  Po ponownym uruchomieniu puli SQL będzie w pełni dostępna do użycia. Jeśli wystąpią problemy podczas uaktualniania, otwórz [żądanie pomocy technicznej](sql-data-warehouse-get-started-create-support-ticket.md) i odwołanie "Uaktualnienie Gen2" jako możliwą przyczynę.
- [Uaktualnij z punktu przywracania](sql-data-warehouse-restore-points.md) — utwórz zdefiniowany przez użytkownika punkt przywracania w bieżącej puli SQL gen1, a następnie przywróć bezpośrednio do wystąpienia Gen2. Istniejąca pula JĘZYKA SQL Gen1 pozostanie na swoim miejscu. Po zakończeniu przywracania pula języka SQL gen2 będzie w pełni dostępna do użycia.  Po uruchomieniu wszystkich procesów testowania i sprawdzania poprawności na przywróconej gen2 wystąpienie, oryginalne wystąpienie Gen1 można usunąć.

  - Krok 1: W witrynie Azure portal [utwórz zdefiniowany przez użytkownika punkt przywracania](sql-data-warehouse-restore-active-paused-dw.md).
  - Krok 2: Podczas przywracania z punktu przywracania zdefiniowanego przez użytkownika ustaw "poziom wydajności" na preferowaną warstwę Gen2.

Może nastąpić okresowe pogorszenie wydajności, gdy w tle będzie odbywać się uaktualnianie plików danych. Łączny okres obniżonej wydajności zależy od rozmiaru plików danych.

Aby przyspieszyć proces migracji danych w tle, można natychmiast wymusić przenoszenie danych, uruchamiając [alter index odbudować](sql-data-warehouse-tables-index.md) na wszystkich tabelach magazynu kolumn podstawowych, które będą kwerendy w większej SLO i klasy zasobów.

> [!NOTE]
> Alter Index rebuild jest operacją w trybie offline i tabele nie będą dostępne, dopóki przebudowa nie zostanie zakończona.

Jeśli wystąpią jakiekolwiek problemy z puli SQL, utwórz [żądanie pomocy technicznej](sql-data-warehouse-get-started-create-support-ticket.md) i odwołanie "Gen2 upgrade" jako możliwą przyczynę.

Aby uzyskać więcej informacji, zobacz [Uaktualnienie do gen2](upgrade-to-latest-generation.md).

## <a name="migration-frequently-asked-questions"></a>Często zadawane pytania dotyczące migracji

**P: Czy Gen2 kosztuje tyle samo co Gen1?**

- Odp. Tak.

**Pyt.: Jak uaktualnienia wpłyną na moje skrypty automatyzacji?**

- Odp.: Każdy skrypt automatyzacji, który odwołuje się do celu poziomu usług, należy zmienić na odpowiadający równoważniek Gen2.  Zobacz szczegóły [tutaj](upgrade-to-latest-generation.md#upgrade-in-a-supported-region-using-the-azure-portal).

**P: Jak długo trwa samomodernak?**

- Odp.: Można uaktualnić w miejscu lub uaktualnić z punktu przywracania.

  - Uaktualnianie w miejscu spowoduje, że pula SQL na chwilę wstrzymać i wznowić.  Proces w tle będzie kontynuowany, gdy pula SQL jest w trybie online.  
  - Trwa dłużej, jeśli uaktualniasz za pośrednictwem punktu przywracania, ponieważ uaktualnienie przejdzie przez cały proces przywracania.

**P: Jak długo potrwa automatyczne uaktualnienie?**

- Odp.: Rzeczywisty przestój uaktualnienia to tylko czas potrzebny do wstrzymania i wznowienia usługi, który wynosi od 5 do 10 minut. Po krótkim czasie przestoju proces w tle uruchomi migrację magazynu. Czas trwania procesu w tle zależy od rozmiaru puli SQL.

**P: Kiedy nastąpi to automatyczne uaktualnienie?**

- O: Podczas planowania konserwacji. Wykorzystanie wybranego harmonogramu konserwacji zminimalizuje zakłócenia w twojej firmie.

**P: Co należy zrobić, jeśli proces uaktualniania w tle wydaje się być zablokowany?**

- O: Rozpocznij ponowne indeksy tabel w magazynie kolumn. Należy zauważyć, że ponowne indeksowanie tabeli będzie w trybie offline podczas tej operacji.

**P: Co zrobić, jeśli Gen2 nie ma celu poziomu usług, który mam w gen1?**

- Odp.: Jeśli używasz DW600 lub DW1200 w gen1, zaleca się użycie dw500c lub DW1000c odpowiednio, ponieważ Gen2 zapewnia więcej pamięci, zasobów i wyższą wydajność niż Gen1.

**P: Czy można wyłączyć tworzenie kopii zapasowych geograficznych?**

- Odpowiedź: nie. Tworzenie kopii zapasowych geograficznych to funkcja przedsiębiorstwa, która pozwala zachować dostępność puli SQL w przypadku, gdy region stanie się niedostępny. Otwórz [prośbę o pomoc techniczną,](sql-data-warehouse-get-started-create-support-ticket.md) jeśli masz dalsze wątpliwości.

**Pyt.: Czy istnieje różnica w składni T-SQL między Gen1 i Gen2?**

- Odp.: Nie ma żadnych zmian w składni języka T-SQL z Gen1 do Gen2.

**P: Czy Gen2 obsługuje system Windows konserwacji?**

- Odp. Tak.

**Pyt.: Czy po uaktualnieniu mojego regionu będzie można utworzyć nowe wystąpienie gen1?**

- Odpowiedź: nie. Po uaktualnieniu regionu tworzenie nowych wystąpień Gen1 zostanie wyłączone.

## <a name="next-steps"></a>Następne kroki

- [Kroki uaktualnienia](upgrade-to-latest-generation.md)
- [Okna konserwacyjne](maintenance-scheduling.md)
- [Monitor kondycji zasobów](../../service-health/resource-health-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
- [Przeglądanie przed rozpoczęciem migracji](upgrade-to-latest-generation.md#before-you-begin)
- [Uaktualnianie w miejscu i uaktualnianie z punktu przywracania](upgrade-to-latest-generation.md)
- [Tworzenie punktu przywracania zdefiniowanego przez użytkownika](sql-data-warehouse-restore-points.md)
- [Dowiedz się, jak przywrócić do gen2](sql-data-warehouse-restore-active-paused-dw.md)
- [Otwieranie żądania pomocy technicznej usługi SQL Data Warehouse](https://go.microsoft.com/fwlink/?linkid=857950)
