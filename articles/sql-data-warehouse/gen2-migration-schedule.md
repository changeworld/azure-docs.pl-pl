---
title: Migrowanie magazynu danych do Gen2
description: Instrukcje dotyczące migrowania istniejącego magazynu danych do Gen2 i harmonogramu migracji według regionów.
services: sql-data-warehouse
author: mlee3gsd
ms.author: anjangsh
ms.reviewer: jrasnick
manager: craigg
ms.assetid: 04b05dea-c066-44a0-9751-0774eb84c689
ms.service: sql-data-warehouse
ms.topic: article
ms.date: 07/22/2019
ms.custom: seo-lt-2019
ms.openlocfilehash: 888f50d645c9b3babf95335e434db65423108ccb
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73693041"
---
# <a name="upgrade-your-data-warehouse-to-gen2"></a>Uaktualnij magazyn danych do Gen2

Firma Microsoft pomaga w zwiększeniu kosztów wejścia/wyjścia magazynu danych na niższym poziomie.  Niższe warstwy obliczeniowe, które mogą obsługiwać wymagające zapytania, są teraz dostępne dla Azure SQL Data Warehouse. Przeczytaj pełną obsługę usługi anonsowanie [niższej warstwy obliczeniowej dla Gen2](https://azure.microsoft.com/blog/azure-sql-data-warehouse-gen2-now-supports-lower-compute-tiers/). Nowa oferta jest dostępna w regionach zanotowanych w poniższej tabeli. W przypadku obsługiwanych regionów istniejące magazyny danych Gen1 można uaktualnić do Gen2 przez:

- **Proces uaktualniania automatycznego:** Automatyczne uaktualnienia nie są uruchamiane zaraz po udostępnieniu usługi w regionie.  Po rozpoczęciu automatycznych uaktualnień w określonym regionie, uaktualnienia poszczególnych magazynów będą odbywać się w ramach wybranego harmonogramu konserwacji.
- [**Samodzielne uaktualnienie do Gen2:** ](#self-upgrade-to-gen2) Możesz określić, kiedy należy przeprowadzić uaktualnienie, wykonując samodzielne uaktualnienie do Gen2. Jeśli region nie jest jeszcze obsługiwany, można przywrócić z punktu przywracania bezpośrednio do wystąpienia Gen2 w obsługiwanym regionie.

## <a name="automated-schedule-and-region-availability-table"></a>Automatyczne planowanie i tabela dostępności regionów

Poniższa tabela podsumowuje według regionu, gdy dolna warstwa obliczeniowa Gen2 będzie dostępna i kiedy rozpocznie się automatyczne uaktualnienia. Daty mogą ulec zmianie. Sprawdź ponownie, aby zobaczyć, kiedy region będzie dostępny.

\* wskazuje, że określony harmonogram dla regionu jest obecnie niedostępny.

| **Region** | **Dolna Gen2 dostępna** | **Rozpoczęcie uaktualniania automatycznego** |
|:--- |:--- |:--- |
| Australia Wschodnia |Dostępne |Zakończ |
| Australia Południowo-Wschodnia |Dostępne |Zakończ |
| Brazylia Południowa |Dostępne |Zakończ |
| Kanada Środkowa |Dostępne |Zakończ |
| Kanada Wschodnia |1 czerwca 2020 |1 lipca 2020 |
| Środkowe stany USA |Dostępne |Zakończ |
| Chiny Wschodnie |\* |\* |
| Chiny Wschodnie 2 |Dostępne |Zakończ |
| Chiny Północne |\* |\* |
| Chiny Północne 2 |Dostępne |Zakończ |
| Azja Wschodnia |Dostępne |Zakończ |
| Wschodnie stany USA |Dostępne |Zakończ |
| Wschodnie stany USA 2 |Dostępne |Zakończ |
| Francja Środkowa |Dostępne |W toku |
| Niemcy Środkowe |\* |\* |
| Niemcy Środkowo-Zachodnie |1 września 2019|1 października 2019 |
| Indie Środkowe |Dostępne |Zakończ |
| Indie Południowe |Dostępne |Zakończ |
| Indie Zachodnie |1 lipca 2019 |W toku |
| Japonia Wschodnia |Dostępne |Zakończ |
| Japonia Zachodnia |Dostępne |Zakończ |
| Korea Środkowa |Dostępne |Zakończ |
| Korea Południowa |Dostępne |Zakończ |
| Środkowo-północne stany USA |Dostępne |Zakończ |
| Europa Północna |Dostępne |Zakończ |
| Północna Republika Południowej Afryki |12 lipca 2019 |Zakończ |
| Środkowo-południowe stany USA |Dostępne |Zakończ |
| Azja Południowo-Wschodnia |Dostępne |Zakończ |
| Północne Zjednoczone Emiraty Arabskie |20 lipca 2019 |Zakończ |
| Południowe Zjednoczone Królestwo |Dostępne |W toku |
| Zachodnie Zjednoczone Królestwo |Dostępne |W toku |
| Środkowo-zachodnie stany USA |1 listopada 2019 |1 grudnia 2019|
| Europa Zachodnia |Dostępne |Zakończ |
| Zachodnie stany USA |Dostępne |Zakończ |
| Zachodnie stany USA 2 |Dostępne |Zakończ |

## <a name="automatic-upgrade-process"></a>Proces automatycznego uaktualniania

Zgodnie z powyższym wykresem dostępności będziemy planować zautomatyzowane uaktualnienia dla wystąpień Gen1. Aby uniknąć nieoczekiwanych przerw w dostępności magazynu danych, zautomatyzowane uaktualnienia zostaną zaplanowane podczas harmonogramu konserwacji. Możliwość tworzenia nowego wystąpienia Gen1 zostanie wyłączona w regionach, które przechodzą na funkcję autouaktualniania do Gen2. Gen1 będzie przestarzałe po zakończeniu automatycznych uaktualnień. Aby uzyskać więcej informacji na temat harmonogramów, zobacz [Wyświetlanie harmonogramu konserwacji](viewing-maintenance-schedule.md)

Proces uaktualniania obejmuje krótki spadek łączności (około 5 minut), co spowoduje ponowne uruchomienie magazynu danych.  Po ponownym uruchomieniu magazynu danych będzie on w pełni dostępny do użycia. Jednak może wystąpić spadek wydajności, podczas gdy proces uaktualniania kontynuuje uaktualnianie plików danych w tle. Łączny okres obniżonej wydajności zależy od rozmiaru plików danych.

Proces uaktualniania pliku danych można również przyspieszyć, uruchamiając polecenie [ALTER index Rebuild](sql-data-warehouse-tables-index.md) na wszystkich podstawowych tabelach magazynu kolumn przy użyciu większego poziomu SLO i klasy zasobów po ponownym uruchomieniu.

> [!NOTE]
> Instrukcja ALTER index Rebuild jest operacją w trybie offline, a tabele nie będą dostępne do momentu zakończenia odbudowy.

## <a name="self-upgrade-to-gen2"></a>Samodzielne uaktualnienie do Gen2

Możesz wybrać opcję samouaktualnienia, wykonując następujące kroki w istniejącym magazynie danych Gen1. W przypadku wybrania opcji samodzielnego uaktualniania należy ją ukończyć przed rozpoczęciem procesu uaktualniania automatycznego w Twoim regionie. Dzięki temu można uniknąć ryzyka, że automatyczne uaktualnienia powodują konflikt.

Podczas przeprowadzania samodzielnego uaktualniania dostępne są dwie opcje.  Możesz uaktualnić bieżący magazyn danych w miejscu lub można przywrócić magazyn danych Gen1 do wystąpienia Gen2.

- [Uaktualnij w miejscu](upgrade-to-latest-generation.md) — ta opcja spowoduje uaktualnienie istniejącego magazynu danych Gen1 do Gen2. Proces uaktualniania obejmuje krótki spadek łączności (około 5 minut), co spowoduje ponowne uruchomienie magazynu danych.  Po ponownym uruchomieniu magazynu danych będzie on w pełni dostępny do użycia. Jeśli wystąpią problemy podczas uaktualniania, Otwórz [żądanie obsługi](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket) i odwołuje się do "Gen2 upgrade" jako możliwej przyczyny.
- [Uaktualnienie z punktu przywracania](sql-data-warehouse-restore.md) — Utwórz punkt przywracania zdefiniowany przez użytkownika w bieżącym magazynie danych Gen1, a następnie Przywróć bezpośrednio do wystąpienia Gen2. Istniejący magazyn danych Gen1 pozostanie na miejscu. Po zakończeniu przywracania magazyn danych Gen2 będzie w pełni dostępny do użycia.  Po uruchomieniu wszystkich procesów testowania i walidacji w przywracanym wystąpieniu Gen2 można usunąć oryginalne wystąpienie Gen1.

   - Krok 1. z Azure Portal [Utwórz punkt przywracania zdefiniowany przez użytkownika](sql-data-warehouse-restore-active-paused-dw.md#restore-an-existing-data-warehouse-through-the-azure-portal).
   - Krok 2. podczas przywracania z punktu przywracania zdefiniowanego przez użytkownika Ustaw poziom wydajności na preferowaną warstwę Gen2.

Może nastąpić okresowe pogorszenie wydajności, gdy w tle będzie odbywać się uaktualnianie plików danych. Łączny okres obniżonej wydajności zależy od rozmiaru plików danych.

Aby przyspieszyć proces migracji danych w tle, można natychmiast wymusić przenoszenie danych przez uruchomienie [polecenia ALTER index Rebuild](sql-data-warehouse-tables-index.md) na wszystkich głównych tabelach magazynu kolumn, które zostaną zbadane przy użyciu większego poziomu SLO i klasy zasobów.

> [!NOTE]
> Instrukcja ALTER index Rebuild jest operacją w trybie offline, a tabele nie będą dostępne do momentu zakończenia odbudowy.

Jeśli wystąpią problemy z magazynem danych, Utwórz [żądanie pomocy technicznej](sql-data-warehouse-get-started-create-support-ticket.md) i odwołuje się do "Gen2 upgrade" jako możliwej przyczyny.

Aby uzyskać więcej informacji, zobacz [uaktualnianie do Gen2](upgrade-to-latest-generation.md).

## <a name="migration-frequently-asked-questions"></a>Często zadawane pytania dotyczące migracji

**P: czy Gen2 koszt jest taki sam jak Gen1?**

- Odp. Tak.

**P: w jaki sposób uaktualnienia będą mieć wpływ na moje skrypty automatyzacji?**

- Odp.: każdy skrypt automatyzacji, który odwołuje się do celu poziomu usługi, powinien zostać zmieniony w taki sposób, aby odpowiadał Gen2 równoważnej.  Szczegóły można znaleźć [tutaj](upgrade-to-latest-generation.md#sign-in-to-the-azure-portal).

**P: jak długo trwa samodzielne uaktualnianie?**

- Odp.: można uaktualnić miejsce lub uaktualnić z punktu przywracania.  
   - Uaktualnienie w miejscu spowoduje chwilowe wstrzymanie i wznowienie działania hurtowni danych.  Proces w tle będzie kontynuowany, gdy magazyn danych jest w trybie online.  
   - Trwa to dłużej, Jeśli uaktualniasz punkt przywracania, ponieważ uaktualnienie przejdzie przez proces pełnego przywracania.

**P: jak długo trwa Autouaktualnianie?**

- Odp.: rzeczywisty czas przestoju w przypadku uaktualnienia to przedział czasu potrzebny do wstrzymania i wznowienia działania usługi, która wynosi od 5 do 10 minut. Po krótkim czasie przestoju proces w tle uruchomi migrację magazynu. Czas działania procesu w tle zależy od rozmiaru magazynu danych.

**P: Kiedy to automatyczne uaktualnienie zostanie wykonane?**

- Odp.: w harmonogramie konserwacji. Korzystanie z wybranego harmonogramu konserwacji pozwoli zminimalizować zakłócenia Twojej firmy.

**P: co należy zrobić, jeśli mój proces uaktualniania w tle wydaje się być zablokowany?**

 - Odp.: należy uruchomić ponowne indeksowanie tabel magazynu kolumn. Należy pamiętać, że ponowne indeksowanie tabeli będzie w trybie offline podczas tej operacji.

**P: co, jeśli Gen2 nie ma celu poziomu usług, mam na Gen1?**
- Odp.: Jeśli używasz wartości DW600 lub DW1200 na Gen1, zaleca się użycie odpowiednio DW500c lub DW1000c, ponieważ Gen2 oferuje więcej pamięci, zasobów i wyższą wydajność niż Gen1.

**P: Czy można wyłączyć geograficzną kopię zapasową?**
- Odp.: nie. Geograficzna kopia zapasowa to funkcja korporacyjna, która pozwala zachować dostępność magazynu danych w przypadku, gdy region staną się niedostępne. Otwórz [żądanie pomocy technicznej](sql-data-warehouse-get-started-create-support-ticket.md) , jeśli chcesz uzyskać więcej problemów.

**P: czy istnieje różnica w składni T-SQL między Gen1 i Gen2?**

- Odp.: nie wprowadzono zmian w składni języka T-SQL od Gen1 do Gen2.

**P: czy Gen2 obsługuje okna obsługi?**

- Odp. Tak.

**P: czy będzie można utworzyć nowe wystąpienie Gen1 po uaktualnieniu mojego regionu?**

- Odp.: nie. Po uaktualnieniu regionu tworzenie nowych wystąpień Gen1 zostanie wyłączone.

## <a name="next-steps"></a>Następne kroki

- [Kroki uaktualniania](upgrade-to-latest-generation.md)
- [Okna obsługi](maintenance-scheduling.md)
- [Monitor kondycji zasobów](https://docs.microsoft.com/azure/service-health/resource-health-overview)
- [Przejrzyj przed rozpoczęciem migracji](upgrade-to-latest-generation.md#before-you-begin)
- [Uaktualnij w miejscu i Uaktualnij z punktu przywracania](upgrade-to-latest-generation.md)
- [Utwórz punkt przywracania zdefiniowany przez użytkownika](sql-data-warehouse-restore-points.md)
- [Dowiedz się, jak przywrócić do Gen2](sql-data-warehouse-restore-active-paused-dw.md#restore-an-existing-data-warehouse-through-the-azure-portal)
- [Otwórz żądanie obsługi SQL Data Warehouse](https://go.microsoft.com/fwlink/?linkid=857950)
