---
title: Co to jest StorSimple Snapshot Manager? | Microsoft Docs
description: Zawiera opis programu StorSimple Snapshot Manager, jego architekturę i jej funkcji.
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
ms.openlocfilehash: 3f7436bb63f52c9c2b697c8e7031922ce89d786b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60789618"
---
# <a name="an-introduction-to-storsimple-snapshot-manager"></a>Wprowadzenie do programu StorSimple Snapshot Manager

## <a name="overview"></a>Omówienie
Przystawki StorSimple Snapshot Manager jest przystawką Microsoft Management Console (MMC), która ułatwia ochronę danych i zarządzania kopiami zapasowymi w środowisku Microsoft Azure StorSimple. StorSimple Snapshot Manager możesz zarządzać danych Microsoft Azure StorSimple w centrum danych i w chmurze jako pojedynczy zintegrowane rozwiązanie do magazynowania, tym samym upraszczanie procesów tworzenia kopii zapasowych i obniżając koszty.

W tym omówieniu wprowadza programu StorSimple Snapshot Manager, w tym artykule opisano jej funkcje oraz informacje o swojej roli w systemie Microsoft Azure StorSimple. 

Aby uzyskać przegląd całego systemu Microsoft Azure StorSimple, w tym urządzenia StorSimple, usługi StorSimple Manager, programu StorSimple Snapshot Manager i adaptera StorSimple dla programu SharePoint, zobacz [serii StorSimple 8000: chmura hybrydowa rozwiązanie do magazynowania](storsimple-overview.md). 

> [!NOTE]
> * Nie można użyć przystawki StorSimple Snapshot Manager do zarządzania Microsoft Azure macierze wirtualne StorSimple (znany także jako StorSimple w środowisku lokalnym urządzeń wirtualnych).
> * Jeśli planujesz zainstalować storsimple z aktualizacją Update 2 na urządzeniu StorSimple, pamiętaj pobrać najnowszą wersję programu StorSimple Snapshot Manager i zainstaluj go **przed zainstalowaniem storsimple z aktualizacją Update 2**. Najnowszą wersję programu StorSimple Snapshot Manager jest zgodny z poprzednimi wersjami i działa ze wszystkimi wersjami wydanej programu Microsoft Azure StorSimple. Jeśli używasz poprzedniej wersji programu StorSimple Snapshot Manager musisz zaktualizować go (nie musisz odinstalować poprzednią wersję przed zainstalowaniem nowej wersji).
> 
> 

## <a name="storsimple-snapshot-manager-purpose-and-architecture"></a>Architektura i celem programu StorSimple Snapshot Manager
Przystawki StorSimple Snapshot Manager udostępnia centralną konsolę zarządzania używanego w celu utworzenia spójnych, kopie zapasowe w momencie lokalnych i danych w chmurze. Na przykład można użyć konsoli:

* Konfigurowanie, tworzenie kopii zapasowej i Usuń woluminy.
* Konfigurowanie grup woluminu, aby upewnić się, którego kopię zapasową danych jest spójnych z aplikacją.
* Zarządzanie zasadami kopii zapasowych, dzięki czemu jest wykonywana kopia zapasowa danych w uprzednio określonym harmonogramem.
* Utwórz lokalne i migawki, które mogą być przechowywane w chmurze lub używanej na potrzeby odzyskiwania po awarii w chmurze.

StorSimple Snapshot Manager pobiera listę aplikacji, zarejestrowanego dostawcy usługi VSS na hoście. Następnie do tworzenia kopii zapasowych spójnych z aplikacją, sprawdza, czy woluminów używanych przez aplikację i sugeruje grupami woluminów do skonfigurowania. Przystawki StorSimple Snapshot Manager korzysta z tych grup woluminu do generowania kopie zapasowe, które są spójne z aplikacjami. (Spójność aplikacji istnieje, gdy wszystkie powiązane pliki i bazy danych są synchronizowane i reprezentuje rzeczywisty stan aplikacji w określonym punkcie w czasie.) 

Tworzenie kopii zapasowych programu StorSimple Snapshot Manager formę migawek przyrostowych, które umożliwiają przechwytywanie tylko zmiany od ostatniej kopii zapasowej. W rezultacie kopie zapasowe wymagają mniejszej ilości pamięci i można tworzyć i przywracane szybko. Przystawki StorSimple Snapshot Manager używa Windows woluminów w tle kopii Service (VSS), aby upewnić się, że migawki przechwytują dane spójne z aplikacjami. (Aby uzyskać więcej informacji, przejdź do integracji z usługą kopiowania woluminów w tle Windows sekcji.) Z programu StorSimple Snapshot Manager, można utworzyć harmonogramów tworzenia kopii zapasowych lub korzystać z natychmiastowej kopii zapasowych, zgodnie z potrzebami. Jeśli potrzebujesz przywrócić dane z kopii zapasowej, umożliwia programu StorSimple Snapshot Manager, należy wybrać z katalogu lokalnego lub migawki w chmurze. Usługa StorSimple przywraca tylko dane, które jest wymagany, ponieważ jest to potrzebne, która zapobiega opóźnieniom dostępność danych, podczas operacji przywracania).

![Architektura programu StorSimple Snapshot Manager](./media/storsimple-what-is-snapshot-manager/HCS_SSM_Overview.png)

**Architektura programu StorSimple Snapshot Manager** 

## <a name="support-for-multiple-volume-types"></a>Obsługa wielu typów woluminów
StorSimple Snapshot Manager służy do konfigurowania i utworzyć kopię zapasową następujących typów woluminów: 

* **Woluminy podstawowe** — wolumin podstawowy jest jednej partycji na dysku podstawowym. 
* **Woluminy proste** — wolumin prosty jest dynamiczny wolumin, który zawiera miejsca na dysku na pojedynczym dysku dynamicznego. Wolumin prosty składa się z jednego regionu na dysku lub w wielu regionach, które są połączone ze sobą na tym samym dysku. (Woluminy proste można tworzyć tylko w przypadku dysków dynamicznych.) Nie są odporne na uszkodzenia.
* **Woluminy dynamiczne** — wolumin dynamiczny jest wolumin utworzony na dysku dynamicznym. Dyski dynamiczne używają bazy danych do śledzenia informacji o woluminach, które są zawarte w przypadku dysków dynamicznych w komputerze. 
* **Woluminy dynamiczne za pomocą funkcji dublowania** — woluminy dynamiczne za pomocą funkcji dublowania są oparte na architekturze macierzy RAID 1. Za pomocą macierzy RAID 1 identyczne dane są zapisywane na dysku co najmniej dwóch produkujących dublowany zestaw. Żądania odczytu mogą być następnie obsługiwane przez każdego dysku, który zawiera żądane dane.
* **Udostępnione woluminy klastra** — w przypadku udostępnione woluminy klastra (CSV), wiele węzłów w trybu failover klastra mogą jednocześnie Odczyt lub zapis do tego samego dysku. Tryb failover z jednego węzła do innego węzła może wystąpić, szybko, bez konieczności zmiany własności dysku lub instalowania, odinstalowywania i usunięcie woluminu. 

> [!IMPORTANT]
> Nie należy mieszać udostępnionych woluminów klastra i innego niż CSV w tę samą migawkę. Mieszanie udostępnionych woluminów klastra i innego niż CSV migawki nie jest obsługiwane. 
> 
> 

Przywróć cały wolumin grup lub sklonować jeden z woluminów, i odzyskiwanie poszczególnych plików, można użyć przystawki StorSimple Snapshot Manager.

* [Woluminy i grupami woluminów](#volumes-and-volume-groups) 
* [Typy kopii zapasowych i zasady kopii zapasowych](#backup-types-and-backup-policies) 

Aby uzyskać więcej informacji o funkcjach programu StorSimple Snapshot Manager i sposobu ich używania, zobacz [interfejsu użytkownika programu StorSimple Snapshot Manager](storsimple-use-snapshot-manager.md).

## <a name="volumes-and-volume-groups"></a>Woluminy i grupami woluminów
StorSimple Snapshot Manager, możesz utworzyć woluminy i skonfigurować je w grupach woluminu. 

Przystawki StorSimple Snapshot Manager korzysta z grup woluminu do tworzenia kopii zapasowych, które są spójne z aplikacjami. Spójność aplikacji istnieje, gdy wszystkie powiązane pliki i bazy danych są synchronizowane i reprezentuje rzeczywisty stan aplikacji w określonym punkcie w czasie. Grupami woluminów (które są również nazywane *grupy spójności*) stanowią podstawę kopię zapasową lub przywrócić zadania.

Grupy woluminu nie są takie same jak kontenery woluminów. Kontener woluminów zawiera jeden lub więcej woluminów, które współużytkują konta magazynu w chmurze i innych atrybutów, takich jak szyfrowanie oraz przepustowości łącza. Kontener pojedynczy wolumin może zawierać maksymalnie 256 woluminów StorSimple alokowane elastycznie. Aby uzyskać więcej informacji na temat kontenerów woluminów, przejdź do [Zarządzaj kontenerami woluminów](storsimple-manage-volume-containers.md). Grupy woluminu są kolekcjami woluminów, które skonfigurować w celu ułatwienia operacje tworzenia kopii zapasowej. Jeśli wybierzesz dwa woluminy, które należą do innego woluminu kontenerów, umieść je w grupie pojedynczy wolumin, a następnie utwórz zasady tworzenia kopii zapasowych dla tej grupy woluminów, każdy wolumin zostanie skopiowana w kontenerze odpowiednim woluminie, przy użyciu konta magazynu odpowiednie.

> [!NOTE]
> Wszystkie woluminy w grupie woluminu musi pochodzić od dostawcy usług w chmurze.
> 
> 

## <a name="integration-with-windows-volume-shadow-copy-service"></a>Integracja z usługą kopiowania woluminów w tle Windows
Przystawki StorSimple Snapshot Manager używa Windows woluminów w tle kopii Service (VSS) do przechwytywania danych spójnych z aplikacją. Usługa VSS zapewnia spójność aplikacji, komunikując się z aplikacji rozpoznających usługę VSS. do zapewnienia koordynacji tworzenia migawek przyrostowych. Usługa VSS zapewnia, że aplikacje są tymczasowo nieaktywne lub spoczynku, przy tworzeniu migawek woluminów. 

Implementacja programu StorSimple Snapshot Manager VSS współpracuje z programu SQL Server i ogólny woluminów NTFS. Proces przebiega w następujący sposób: 

1. Obiekt żądający, który jest zazwyczaj zarządzania danymi i rozwiązanie do ochrony (takich jak przystawki StorSimple Snapshot Manager) lub aplikacji do tworzenia kopii zapasowej, wywołuje usługi VSS i pyta, aby zebrać informacje z oprogramowania składnika zapisywania w aplikacji docelowej.
2. Usługa VSS kontaktuje się składnika zapisywania, aby pobrać opis danych. Moduł zapisujący zwraca opis danych do wykonania kopii zapasowej. 
3. Usługa VSS sygnalizuje składnika zapisywania w celu przygotowania aplikacji do tworzenia kopii zapasowych. Moduł zapisujący przygotowuje dane do utworzenia kopii zapasowej, wykonując otwartych transakcji aktualizowanie dzienników transakcji i tak dalej, a następnie powiadamia użytkownika, programu VSS
4. Usługa VSS powoduje, że składnik zapisywania do tymczasowego zatrzymania magazyny danych aplikacji i upewnij się, że żadne dane nie są zapisywane do woluminu, podczas tworzenia kopii w tle. Ten krok zapewnia spójność danych i trwa nie dłużej niż 60 sekund.
5. Usługa VSS powoduje, że dostawcy w celu utworzenia kopii w tle. Dostawców, które mogą być oprogramowania — lub oparte na sprzęcie, zarządzanie woluminy, które są aktualnie uruchomione i utworzyć ich kopie w tle na żądanie. Dostawca jest tworzona kopia w tle i powiadomienia usługi VSS, po jego zakończeniu.
6. Usługa VSS kontaktuje się składnika zapisywania, by powiadomić aplikację, którą można wznowić operacji We/Wy, a także aby upewnić się, że operacje We/Wy pomyślnie Wstrzymano Podczas tworzenia kopii w tle. 
7. Jeśli kopiowanie zakończyło się pomyślnie, Usługa VSS Zwraca lokalizację kopii do osoby zgłaszającej żądanie. 
8. Jeśli dane zostały zapisane, podczas tworzenia kopii w tle została utworzona, to tworzenie kopii zapasowej będą niespójne. Usługa VSS usuwa kopii w tle i powiadamia obiekt żądający. Obiekt żądający, można automatycznie Powtórz proces tworzenia kopii zapasowej lub Powiadom administratora, aby ponowić próbę wykonania w późniejszym czasie.

Zobacz na poniższej ilustracji.

![Proces usługi VSS](./media/storsimple-what-is-snapshot-manager/HCS_SSM_VSS_process.png)

**Proces usługi kopiowania woluminów w tle Windows** 

## <a name="backup-types-and-backup-policies"></a>Typy kopii zapasowych i zasady kopii zapasowych
Za pomocą Menedżera migawek StorSimple można tworzyć kopie zapasowe danych i zapisz go lokalnie, jak i w chmurze. Przystawki StorSimple Snapshot Manager można użyć, aby natychmiast utworzyć kopię zapasową danych lub zasad tworzenia kopii zapasowej można użyć do tworzenia harmonogramu wykonywania kopii zapasowych automatycznie. Zasady tworzenia kopii zapasowych również umożliwiają określenie, ile migawki zostaną zachowane. 

### <a name="backup-types"></a>Typy kopii zapasowych
Aby utworzyć następujące typy kopii zapasowych, można użyć przystawki StorSimple Snapshot Manager:

* **Migawki lokalne** — migawki lokalne są kopiami w momencie woluminu danych, które są przechowywane na urządzeniu StorSimple. Zazwyczaj ten typ kopii zapasowej można tworzyć i przywracane szybko. Możesz użyć lokalnych migawek, jak w przypadku lokalnej kopii zapasowej.
* **Migawki w chmurze** — migawki w chmurze są kopiami w momencie woluminu danych, które są przechowywane w chmurze. Migawka w chmurze jest równoznaczne z migawki replikowane w systemie magazynu inną, poza siedzibą firmy. Migawki w chmurze są szczególnie przydatne w przypadku scenariuszy odzyskiwania po awarii.

### <a name="on-demand-and-scheduled-backups"></a>Na żądanie i zaplanowane kopie zapasowe
Za pomocą Menedżera migawek StorSimple można zainicjować jednorazowej kopii zapasowej od razu utworzyć lub zasad tworzenia kopii zapasowej można użyć do zaplanowania cyklicznych operacje tworzenia kopii zapasowej.

Zasady tworzenia kopii zapasowych to zestaw reguł automatycznych, które można użyć do zaplanowania regularnych kopii zapasowych. Zasady tworzenia kopii zapasowych umożliwia definiowanie częstotliwość i parametry tworzenie migawek grupę określonego woluminu. Można użyć zasad, aby określić daty rozpoczęcia i wygaśnięcia, godziny, częstotliwości i wymagania dotyczące przechowywania, lokalnego oraz migawki w chmurze. Zasady są stosowane natychmiast, po jego zdefiniowaniu. 

Aby skonfigurować lub zmienić konfigurację zasad tworzenia kopii zapasowych, jeśli zajdzie taka potrzeba, można użyć przystawki StorSimple Snapshot Manager. 

Możesz skonfigurować następujące informacje dotyczące poszczególnych zasad tworzenia kopii zapasowej:

* **Nazwa** — unikatowa nazwa wybrane zasady kopii zapasowych.
* **Typ** — typ zasad tworzenia kopii zapasowej; migawka lokalna lub migawkę w chmurze.
* **Grupa woluminów** — grupy woluminów, do której przypisany jest wybrane zasady kopii zapasowych.
* **Przechowywanie** — liczba kopii do przechowania. Jeśli zaznaczysz **wszystkich** pole, wszystkie kopie zapasowe są przechowywane, dopóki nie zostanie osiągnięta maksymalna liczba kopii zapasowych na wolumin, w tym momencie zasada zostanie zakończyć się niepowodzeniem i generować komunikat o błędzie. Alternatywnie można określić liczbę kopii zapasowych do zachowania (od 1 do 64).
* **Data** — Data utworzenia zasad tworzenia kopii zapasowej.

Aby uzyskać informacje o konfigurowaniu zasad tworzenia kopii zapasowych, przejdź do [Użyj StorSimple Snapshot Manager, aby utworzyć i zarządzać zasadami tworzenia kopii zapasowych](storsimple-snapshot-manager-manage-backup-policies.md).

### <a name="backup-job-monitoring-and-management"></a>Zadanie tworzenia kopii zapasowej, monitorowanie i zarządzanie nimi
Menedżer migawek StorSimple umożliwia monitorowanie i zarządzanie nimi nadchodzące zaplanowane i zakończonych zadań tworzenia kopii zapasowej. Ponadto programu StorSimple Snapshot Manager udostępnia katalog maksymalnie 64 ukończone kopii zapasowych. Wykazu służy do znajdowania i przywracania woluminów lub poszczególne pliki. 

Aby uzyskać informacje o monitorowaniu zadania tworzenia kopii zapasowej, przejdź do [Użyj StorSimple Snapshot Manager do wyświetlania i zarządzania zadania tworzenia kopii zapasowej](storsimple-snapshot-manager-manage-backup-jobs.md).

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [za pomocą Menedżera migawek StorSimple do administrowania rozwiązania StorSimple](storsimple-snapshot-manager-admin.md).
* Pobierz [programu StorSimple Snapshot Manager](https://www.microsoft.com/download/details.aspx?id=44220).

