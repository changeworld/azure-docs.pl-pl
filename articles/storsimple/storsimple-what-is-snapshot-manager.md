---
title: Co to jest StorSimple Snapshot Manager? | Microsoft Docs
description: Zawiera opis programu StorSimple Snapshot Manager, jego architektury i jego funkcje.
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: ''
ms.assetid: 6094c31e-e2d9-4592-8a15-76bdcf60a754
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: v-sharos
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 38c197c7bc57110b29b1d8cb789d5b7310823da2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23876831"
---
# <a name="an-introduction-to-storsimple-snapshot-manager"></a>Wprowadzenie do programu StorSimple Snapshot Manager

## <a name="overview"></a>Omówienie
StorSimple Snapshot Manager jest przystawką Microsoft Management Console (MMC), która ułatwia ochronę danych i zarządzania kopiami zapasowymi w środowisku Microsoft Azure StorSimple. StorSimple Snapshot Manager można zarządzać danych Microsoft Azure StorSimple w centrum danych i w chmurze jako pojedynczy magazyn zintegrowanego rozwiązania, w związku z tym uproszczeniu procesów tworzenia kopii zapasowej i zmniejszenie kosztów.

To omówienie wprowadza StorSimple Snapshot Manager, opisano jego funkcji i wyjaśniono swoją rolę w Microsoft Azure StorSimple. 

Omówienie całego systemu Microsoft Azure StorSimple, w tym urządzenia StorSimple, usługi Menedżer StorSimple, StorSimple Snapshot Manager i karty StorSimple dla programu SharePoint, zobacz [z serii StorSimple 8000: chmura hybrydowa rozwiązanie magazynowania](storsimple-overview.md). 

> [!NOTE]
> * Nie można użyć programu StorSimple Snapshot Manager do zarządzania Microsoft Azure StorSimple wirtualnego tablic (znanej także jako StorSimple lokalnego urządzenia wirtualnego).
> * Jeśli planujesz zainstalować StorSimple Update 2 na urządzeniu StorSimple, pamiętaj pobrać najnowszą wersję programu StorSimple Snapshot Manager i zainstalować ją **przed zainstalowaniem StorSimple Update 2**. Najnowszą wersję programu StorSimple Snapshot Manager jest zgodne z poprzednimi wersjami i działa w przypadku wszystkich wersji wydanej programu Microsoft Azure StorSimple. Jeśli używasz poprzedniej wersji programu StorSimple Snapshot Manager, należy zaktualizować je (nie należy odinstalować poprzednią wersję, przed zainstalowaniem nowej wersji).
> 
> 

## <a name="storsimple-snapshot-manager-purpose-and-architecture"></a>Cel StorSimple Snapshot Manager i architektura
StorSimple Snapshot Manager udostępnia centralną konsolę zarządzania używanej do utworzenia spójnych, kopie zapasowe w momencie lokalnych i danych w chmurze. Na przykład można użyć konsoli:

* Konfigurowanie, tworzenie kopii zapasowej i usuwanie woluminów.
* Konfigurowanie grup woluminu, aby upewnić się, którego kopię zapasową danych jest spójne z aplikacjami.
* Zarządzanie zasad tworzenia kopii zapasowych, dzięki czemu jest wykonywana kopia zapasowa danych na uprzednio określonym harmonogramem.
* Utwórz lokalne i migawki, które mogą być przechowywane w chmurze lub używanych na potrzeby odzyskiwania po awarii w chmurze.

StorSimple Snapshot Manager pobiera listę aplikacji zarejestrowana u dostawcy usługi VSS na hoście. Następnie Aby utworzyć kopie zapasowe spójnych z aplikacją, go sprawdza woluminów używanych przez aplikację, a także sugeruje grup woluminu, aby skonfigurować. StorSimple Snapshot Manager korzysta z tych grup woluminu do generowania kopie zapasowe, które są spójne z aplikacjami. (Spójności aplikacji występuje, gdy wszystkie powiązane pliki i baz danych są zsynchronizowane i reprezentuje rzeczywisty stan aplikacji w określonym punkcie w czasie.) 

Kopie zapasowe programu StorSimple Snapshot Manager formę przyrostowe migawki, które umożliwiają przechwytywanie tylko zmiany od ostatniej kopii zapasowej. W związku z tym kopie zapasowe wymagają mniejszej ilości pamięci i można tworzyć i szybko przywrócić. StorSimple Snapshot Manager używa Windows woluminów w tle kopii Service (VSS), aby upewnić się, że migawki przechwytywania danych spójnych z aplikacją. (Aby uzyskać więcej informacji, przejdź do integracji z sekcji usługi kopiowania woluminów w tle systemu Windows.) StorSimple Snapshot Manager można utworzyć harmonogramy tworzenia kopii zapasowej lub korzystać z bezpośrednim kopii zapasowych zgodnie z potrzebami. Jeśli potrzebujesz przywrócić dane z kopii zapasowej, umożliwia StorSimple Snapshot Manager wybierz z katalogu lokalnego lub migawki w chmurze. Azure StorSimple przywraca tylko dane, które jest wymagany, ponieważ jest to potrzebne, która zapobiega opóźnieniom danych są dostępne podczas operacji przywracania).

![Architektura programu StorSimple Snapshot Manager](./media/storsimple-what-is-snapshot-manager/HCS_SSM_Overview.png)

**Architektura programu StorSimple Snapshot Manager** 

## <a name="support-for-multiple-volume-types"></a>Obsługa wielu typów woluminu
StorSimple Snapshot Manager można użyć do konfigurowania i utworzyć kopię zapasową woluminów następujących typów: 

* **Woluminy podstawowe** — woluminu podstawowego jest jednej partycji na dysku podstawowym. 
* **Woluminów prostych** — prosty wolumin jest dynamiczny wolumin, który zawiera miejsca na dysku na pojedynczym dysku dynamicznego. Wolumin prosty składa się z jednego regionu na dysku lub w wielu regionach, które są połączone ze sobą na tym samym dysku. (Woluminy proste można tworzyć tylko na dyskach dynamicznych.) Nie są odporne na uszkodzenia.
* **Woluminy dynamiczne** — wolumin dynamiczny jest wolumin utworzony na dysku dynamicznym. Dyski dynamiczne używają bazy danych do śledzenia informacji o woluminach, które znajdują się na dyski dynamiczne w komputerze. 
* **Woluminy dynamiczne dublowanie** — woluminy dynamiczne dublowanie są tworzone na komputerze o architekturze RAID 1. Z macierzy RAID 1 identyczne dane są zapisywane na dysku co najmniej dwa produkujących dublowany zestaw. Żądanie odczytu następnie są obsługiwane przez każdego dysku, który zawiera żądanych danych.
* **Udostępnione woluminy klastra** — w przypadku udostępnione woluminy klastra (CSV), wiele węzłów może klastra pracy awaryjnej jednocześnie odczytu lub zapisu na tym samym dysku. Tryb failover z jednego węzła do innego węzła może wystąpić szybko, bez konieczności zmiany własności dysku lub instalowanie, odinstalowywanie i usuwanie woluminu. 

> [!IMPORTANT]
> Nie należy ich łączyć udostępnionych woluminów klastra i z systemem innym niż CSV w tym samym migawki. Mieszanie udostępnionych woluminów klastra i z systemem innym niż CSV migawki nie jest obsługiwane. 
> 
> 

StorSimple Snapshot Manager służy do przywracania grup całego woluminu lub sklonować poszczególnych woluminów i odzyskanie poszczególnych plików.

* [Woluminy i grup woluminu](#volumes-and-volume-groups) 
* [Typy kopii zapasowych i zasady tworzenia kopii zapasowej](#backup-types-and-backup-policies) 

Aby uzyskać więcej informacji o funkcjach programu StorSimple Snapshot Manager i sposobu ich używania, zobacz [interfejsu użytkownika programu StorSimple Snapshot Manager](storsimple-use-snapshot-manager.md).

## <a name="volumes-and-volume-groups"></a>Woluminy i grup woluminu
StorSimple Snapshot Manager tworzyć woluminy i skonfigurować je w grupach woluminu. 

StorSimple Snapshot Manager korzysta z grup woluminu do tworzenia kopii zapasowych, które są spójne z aplikacjami. Spójności aplikacji występuje, gdy wszystkie powiązane pliki i baz danych są zsynchronizowane i reprezentuje rzeczywisty stan aplikacji w określonym punkcie w czasie. Grup woluminu (które są nazywane również *spójności grup*) stanowią podstawę kopię zapasową lub przywrócić zadania.

Grupy woluminu nie są takie same, jak kontenery woluminów. Kontener woluminów zawiera jeden lub więcej woluminów, które mają konta magazynu w chmurze i inne atrybuty, takie jak szyfrowanie oraz przepustowości łącza. Kontener pojedynczy wolumin może zawierać maksymalnie 256 alokowane elastycznie woluminów StorSimple. Aby uzyskać więcej informacji na temat kontenery woluminów, przejdź do [Zarządzanie kontenerów woluminu](storsimple-manage-volume-containers.md). Wolumin grupy są kolekcjami elementów woluminów, które skonfigurowano w celu ułatwienia operacji tworzenia kopii zapasowej. W przypadku wybrania dwa woluminy, które należą do kontenerów inny wolumin, umieść je w grupie pojedynczy wolumin, a następnie utwórz zasady tworzenia kopii zapasowej dla tej grupy woluminu każdego woluminu zostanie kopii zapasowej w kontenerze odpowiednim woluminie, przy użyciu konta magazynu odpowiednie.

> [!NOTE]
> Wszystkie woluminy w grupie woluminu musi pochodzić od dostawcy usług w chmurze pojedynczego.
> 
> 

## <a name="integration-with-windows-volume-shadow-copy-service"></a>Integracja z usługą kopiowania woluminów w tle systemu Windows
StorSimple Snapshot Manager używa Windows woluminów w tle kopii Service (VSS) do przechwycenia danych spójnych z aplikacją. Usługa VSS zapewnia spójność aplikacji komunikując się z aplikacji obsługujących usługę VSS do koordynowania tworzenia przyrostowej migawki. Usługa VSS zapewnia, że aplikacje są tymczasowo nieaktywne lub spoczynku, przy tworzeniu migawek woluminów. 

Implementacja StorSimple Snapshot Manager VSS współdziała z programu SQL Server i rodzajowy woluminów NTFS. Proces przebiega w następujący sposób: 

1. Obiekt żądający, który jest zazwyczaj zarządzanie danymi i rozwiązanie do ochrony (takie jak StorSimple Snapshot Manager) lub aplikacji do tworzenia kopii zapasowej, wywołuje usługi VSS i zapyta, aby zebrać informacje z oprogramowania zapisywania w aplikacji docelowej.
2. Usługa VSS kontaktuje się składnika zapisywania można pobrać opis danych. Moduł zapisujący zwraca opis danych do wykonania kopii zapasowej. 
3. Usługa VSS sygnalizuje zapisywania do przygotowania aplikacji do utworzenia kopii zapasowej. Moduł zapisujący przygotowuje danych do utworzenia kopii zapasowej, wykonując otwartych transakcji, aktualizowanie, dzienników transakcji i tak dalej, a następnie powiadamia VSS.
4. VSS powoduje, że składnik zapisywania, aby tymczasowo zatrzymać magazyny danych aplikacji i upewnij się, że żadne dane nie są zapisywane do woluminu, podczas tworzenia kopii w tle. Ten krok zapewnia spójność danych i trwa nie dłużej niż 60 sekund.
5. Usługa VSS nakazuje dostawcy w celu utworzenia kopii w tle. Dostawców, które mogą być oprogramowania - lub oparte na sprzęcie, zarządzanie woluminów, które są aktualnie uruchomione i utworzyć ich kopie w tle na żądanie. Dostawca tworzy kopię w tle i powiadamia VSS po jego ukończeniu.
6. Usługa VSS kontaktuje się twórcę powiadomić aplikację, którą można wznowić operacji We/Wy i upewnij się, że We/Wy została wstrzymana pomyślnie podczas tworzenia kopii w tle. 
7. Jeśli kopia zakończyło się pomyślnie, VSS zwraca lokalizacji kopiowania do żądającego. 
8. Jeśli dane zostały zapisane podczas tworzenia kopii w tle została utworzona, to kopia zapasowa będą niespójne. Usługa VSS usuwa kopii w tle i powiadamia żądającego. Obiekt żądający może automatycznie Powtórz proces tworzenia kopii zapasowej albo zawiadomić administratora, aby ponowić próbę wykonania w późniejszym czasie.

Zobacz poniższy rysunek.

![Proces usługi VSS](./media/storsimple-what-is-snapshot-manager/HCS_SSM_VSS_process.png)

**Proces usługi kopiowania woluminów w tle systemu Windows** 

## <a name="backup-types-and-backup-policies"></a>Typy kopii zapasowych i zasady tworzenia kopii zapasowej
StorSimple Snapshot Manager można tworzyć kopie zapasowe danych i zapisać go lokalnie i w chmurze. StorSimple Snapshot Manager można użyć, aby natychmiast wykonać kopię zapasową danych, lub można utworzyć harmonogram wykonywania kopii zapasowych automatycznie zasad tworzenia kopii zapasowej. Zasady tworzenia kopii zapasowych umożliwiają także określić, ile migawki zostaną zachowane. 

### <a name="backup-types"></a>Typy kopii zapasowych
StorSimple Snapshot Manager służy do tworzenia kopii zapasowych następujących typów:

* **Migawki lokalne** — lokalny migawki są w chwili kopii danych woluminu, które są przechowywane na urządzeniu StorSimple. Zazwyczaj ten typ kopii zapasowej można tworzyć i szybko przywrócić. Można użyć migawka lokalna, jak w przypadku lokalnej kopii zapasowej.
* **Migawki w chmurze** — migawki w chmurze są w chwili kopii danych woluminu, które są przechowywane w chmurze. Migawka w chmurze jest odpowiednikiem migawki replikowane w systemie magazynu innych, poza nim. Migawki w chmurze są szczególnie przydatne w scenariuszach odzyskiwania po awarii.

### <a name="on-demand-and-scheduled-backups"></a>Na żądanie i zaplanowanych kopii zapasowych
StorSimple Snapshot Manager można zainicjować jednorazowe kopia zapasowa ma zostać utworzony natychmiast lub zasad tworzenia kopii zapasowej można użyć do zaplanowania cyklicznych operacji tworzenia kopii zapasowej.

Zasady tworzenia kopii zapasowej jest zestaw reguł automatycznego, które służą do planowania regularnych kopii zapasowych. Zasady tworzenia kopii zapasowej można zdefiniować częstotliwość i parametry tworzenie migawek woluminu określonej grupy. Zasady można użyć do określenia daty rozpoczęcia i wygaśnięcia, godziny, częstotliwości i wymagania dotyczące przechowywania, lokalnego i w chmurze migawki. Zasady są stosowane natychmiast po zdefiniowaniu. 

Konfigurowanie lub ponownie skonfigurować zasady tworzenia kopii zapasowych, gdy jest to niezbędne, można użyć StorSimple Snapshot Manager. 

Należy skonfigurować dla każdej zasady tworzenia kopii zapasowej należy utworzyć następujące informacje:

* **Nazwa** — unikatowa nazwa wybranych zasad tworzenia kopii zapasowej.
* **Typ** — typ zasad tworzenia kopii zapasowej; migawka lokalna i migawka w chmurze.
* **Grupy woluminu** — grupy woluminu, do której przypisano wybranych zasad tworzenia kopii zapasowej.
* **Przechowywania** — liczba kopii do zachowania. Jeśli wybierzesz **wszystkie** okno, wszystkie kopie zapasowe są przechowywane, dopóki nie zostanie osiągnięta maksymalna liczba kopii zapasowych na każdym woluminie, w takim przypadku zasada zostanie zakończyć się niepowodzeniem i generuje komunikat o błędzie. Alternatywnie można określić liczbę kopii zapasowych do zachowania (od 1 do 64).
* **Data** — Data utworzenia zasad tworzenia kopii zapasowej.

Aby uzyskać informacje o konfigurowaniu zasad tworzenia kopii zapasowych, przejdź do [Użyj StorSimple Snapshot Manager do tworzenia i obsługi zasad tworzenia kopii zapasowych](storsimple-snapshot-manager-manage-backup-policies.md).

### <a name="backup-job-monitoring-and-management"></a>Zadanie tworzenia kopii zapasowej, monitorowanie i zarządzanie
StorSimple Snapshot Manager umożliwia monitorowanie i zarządzanie nimi nadchodzących, zaplanowane i zakończonych zadań tworzenia kopii zapasowej. Ponadto StorSimple Snapshot Manager udostępnia katalog maksymalnie 64 kopie zapasowe ukończone. Katalogu służy do znajdowania i Przywracanie woluminów lub pojedynczych plików. 

Aby uzyskać informacje o monitorowaniu zadań tworzenia kopii zapasowej, przejdź do [Użyj StorSimple Snapshot Manager można wyświetlać i zarządzać nimi zadania tworzenia kopii zapasowej](storsimple-snapshot-manager-manage-backup-jobs.md).

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [przy użyciu programu StorSimple Snapshot Manager do administrowania rozwiązania StorSimple](storsimple-snapshot-manager-admin.md).
* Pobierz [StorSimple Snapshot Manager](https://www.microsoft.com/download/details.aspx?id=44220).

