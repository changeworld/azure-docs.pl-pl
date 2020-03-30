---
title: Co to jest StorSimple Snapshot Manager? | Microsoft Docs
description: W tym artykule opisano StorSimple Snapshot Manager, jego architektury i jego funkcji.
services: storsimple
documentationcenter: NA
author: twooley
manager: timlt
editor: ''
ms.assetid: 6094c31e-e2d9-4592-8a15-76bdcf60a754
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: twooley
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e58e2d929dd1e4db16ce495ad54045e9dc3a6fb1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267419"
---
# <a name="an-introduction-to-storsimple-snapshot-manager"></a>Wprowadzenie do Menedżera migawek StorSimple

## <a name="overview"></a>Omówienie
StorSimple Snapshot Manager to przystawka programu Microsoft Management Console (MMC), która upraszcza ochronę danych i zarządzanie kopiami zapasowymi w środowisku Microsoft Azure StorSimple. Dzięki StorSimple Snapshot Manager możesz zarządzać danymi usługi Microsoft Azure StorSimple w centrum danych i w chmurze jako pojedyncze zintegrowane rozwiązanie pamięci masowej, upraszczając w ten sposób procesy tworzenia kopii zapasowych i obniżając koszty.

W tym omówieniu przedstawiono Menedżera migawek StorSimple, opisano jego funkcje i wyjaśniono jego rolę w microsoft azure storproste. 

Aby zapoznać się z omówieniem całego systemu Microsoft Azure StorSimple, w tym urządzenia StorSimple, usługi StorSimple Manager, Menedżera migawek StorSimple i karty StorSimple dla programu SharePoint, zobacz [Seria StorSimple 8000: rozwiązanie do przechowywania w chmurze hybrydowej](storsimple-overview.md). 

> [!NOTE]
> * Nie można używać Menedżera migawek StorSimple do zarządzania macierzami wirtualnymi usługi Microsoft Azure StorSimple (nazywanymi również lokalnymi urządzeniami wirtualnymi StorSimple).
> * Jeśli planujesz zainstalować storsimple update 2 na urządzeniu StorSimple, należy pobrać najnowszą wersję StorSimple Snapshot Manager i zainstalować go **przed zainstalowaniem StorSimple Update 2**. Najnowsza wersja StorSimple Snapshot Manager jest wstecznie kompatybilny i współpracuje ze wszystkimi wydanymi wersjami Microsoft Azure StorSimple. Jeśli używasz poprzedniej wersji StorSimple Snapshot Manager, należy go zaktualizować (nie trzeba odinstalowywać poprzedniej wersji przed zainstalowaniem nowej wersji).
> 
> 

## <a name="storsimple-snapshot-manager-purpose-and-architecture"></a>StorSimple Snapshot Manager — przeznaczenie i architektura
StorSimple Snapshot Manager udostępnia centralną konsolę zarządzania, której można używać do tworzenia spójnych kopii zapasowych danych lokalnych i danych w chmurze w czasie. Na przykład można użyć konsoli do:

* Konfigurowanie woluminów, ich utworzenie kopii zapasowej i usuwanie.
* Skonfiguruj grupy woluminów, aby upewnić się, że kopie zapasowe danych są spójne z aplikacjami.
* Zarządzanie zasadami tworzenia kopii zapasowych, tak aby kopia zapasowa danych była archiwiza w określonym harmonogramie.
* Tworzenie migawek lokalnych i chmur, które mogą być przechowywane w chmurze i używane do odzyskiwania po awarii.

Menedżer migawek StorSimple pobiera listę aplikacji zarejestrowanych u dostawcy usługi VSS na hoście. Następnie, aby utworzyć kopie zapasowe spójne z aplikacjami, sprawdza woluminy używane przez aplikację i sugeruje grupy woluminów do skonfigurowania. StorSimple Snapshot Manager używa tych grup woluminów do generowania kopii zapasowych, które są spójne z aplikacją. (Spójność aplikacji istnieje, gdy wszystkie powiązane pliki i bazy danych są synchronizowane i reprezentują prawdziwy stan aplikacji w określonym momencie w czasie.) 

StorSimple Snapshot Manager kopie zapasowe przybierają formę migawek przyrostowych, które przechwytują tylko zmiany od ostatniej kopii zapasowej. W rezultacie kopie zapasowe wymagają mniej miejsca i mogą być tworzone i przywracane szybko. StorSimple Snapshot Manager używa usługi kopiowania woluminów w tle systemu Windows (VSS), aby upewnić się, że migawki przechwytują dane spójne z aplikacją. (Aby uzyskać więcej informacji, przejdź do sekcji Integracja z usługą kopiowania woluminów systemu Windows). Za pomocą StorSimple Snapshot Manager można tworzyć harmonogramy tworzenia kopii zapasowych lub w razie potrzeby do wykonywania natychmiastowych kopii zapasowych. Jeśli chcesz przywrócić dane z kopii zapasowej, StorSimple Snapshot Manager umożliwia wybranie z katalogu migawek lokalnych lub w chmurze. Usługa Azure StorSimple przywraca tylko dane, które są potrzebne, co zapobiega opóźnieniom w dostępności danych podczas operacji przywracania.)

![Architektura Menedżera migawek StorSimple](./media/storsimple-what-is-snapshot-manager/HCS_SSM_Overview.png)

**Architektura Menedżera migawek StorSimple** 

## <a name="support-for-multiple-volume-types"></a>Obsługa wielu typów woluminów
Za pomocą Menedżera migawek StorSimple można skonfigurować i wyw. 

* **Woluminy podstawowe** — wolumin podstawowy to pojedyncza partycja na dysku podstawowym. 
* **Woluminy proste** — wolumin prosty to wolumin dynamiczny, który zawiera miejsce na dysku z jednego dysku dynamicznego. Wolumin prosty składa się z pojedynczego regionu na dysku lub wielu regionów połączonych ze sobą na tym samym dysku. (Woluminy proste można tworzyć tylko na dyskach dynamicznych). Woluminy proste nie są odporne na uszkodzenia.
* **Woluminy dynamiczne** — wolumin dynamiczny to wolumin utworzony na dysku dynamicznym. Dyski dynamiczne używają bazy danych do śledzenia informacji o woluminach, które znajdują się na dyskach dynamicznych w komputerze. 
* **Woluminy dynamiczne z dublowaniem** — woluminy dynamiczne z dublowanie są oparte na architekturze RAID 1. W macierzy RAID 1 identyczne dane są zapisywane na dwóch lub więcej dyskach, tworząc zestaw dublowany. Żądanie odczytu może być następnie obsługiwane przez dowolny dysk, który zawiera żądane dane.
* **Woluminy udostępnione przez klaster** — dzięki woluminom współużytkowym klastra (CSV) wiele węzłów w klastrze trybu failover może jednocześnie odczytywać lub zapisywać na tym samym dysku. Przełączenie awaryjne z jednego węzła do innego węzła może nastąpić szybko, bez konieczności zmiany własności dysku lub montażu, demontażu i usuwania woluminu. 

> [!IMPORTANT]
> Nie mieszaj csv i innych niż CSV w tej samej migawce. Mieszanie csv i innych niż CSV w migawce nie jest obsługiwane. 
> 
> 

Za pomocą StorSimple Snapshot Manager można przywrócić całe grupy woluminów lub sklonować poszczególne woluminy i odzyskać poszczególne pliki.

* [Woluminy i grupy woluminów](#volumes-and-volume-groups) 
* [Typy kopii zapasowych i zasady tworzenia kopii zapasowych](#backup-types-and-backup-policies) 

Aby uzyskać więcej informacji na temat funkcji Menedżera migawek StorSimple i sposobu ich używania, zobacz [Interfejs użytkownika StorSimple Snapshot Manager](storsimple-use-snapshot-manager.md).

## <a name="volumes-and-volume-groups"></a>Woluminy i grupy woluminów
Za pomocą StorSimple Snapshot Manager można utworzyć woluminy, a następnie skonfigurować je w grupach woluminów. 

StorSimple Snapshot Manager używa grup woluminów do tworzenia kopii zapasowych, które są spójne z aplikacjami. Spójność aplikacji istnieje, gdy wszystkie powiązane pliki i bazy danych są synchronizowane i reprezentują prawdziwy stan aplikacji w określonym momencie w czasie. Grupy woluminów (które są również nazywane *grupami spójności)* stanowią podstawę zadania tworzenia kopii zapasowej lub przywracania.

Grupy woluminów nie są takie same jak kontenery woluminów. Kontener woluminu zawiera jeden lub więcej woluminów współużytkujących konto magazynu w chmurze i inne atrybuty, takie jak szyfrowanie i zużycie przepustowości. Kontener pojedynczego woluminu może zawierać maksymalnie 256 woluminów storaprowizowanych w sposób cienki. Aby uzyskać więcej informacji na temat kontenerów woluminów, przejdź do [witryny Zarządzanie kontenerami woluminów](storsimple-manage-volume-containers.md). Grupy woluminów to kolekcje woluminów skonfigurowane w celu ułatwienia operacji tworzenia kopii zapasowych. Jeśli wybierzesz dwa woluminy, które należą do różnych kontenerów woluminów, umieść je w jednej grupie woluminów, a następnie utworzysz zasady tworzenia kopii zapasowych dla tej grupy woluminów, kopia zapasowa każdego woluminu zostanie utworzona w odpowiednim kontenerze woluminu przy użyciu odpowiedniego konta magazynu.

> [!NOTE]
> Wszystkie woluminy w grupie woluminów muszą pochodzić od jednego dostawcy usług w chmurze.
> 
> 

## <a name="integration-with-windows-volume-shadow-copy-service"></a>Integracja z usługą kopiowania woluminów w tle systemu Windows
StorSimple Snapshot Manager używa usługi kopiowania woluminów w tle systemu Windows (VSS) do przechwytywania danych spójnych z aplikacją. Usługa VSS ułatwia spójność aplikacji, komunikując się z aplikacjami obsługującymi usługę VSS w celu koordynowania tworzenia migawek przyrostowych. Usługa VSS zapewnia, że aplikacje są tymczasowo nieaktywne lub spoczynkowe, gdy migawki są pobierane. 

Implementacja Menedżera migawek StorSimple usługi VSS współpracuje z programem SQL Server i ogólnymi woluminami NTFS. Proces jest następujący: 

1. Żądacz, który jest zazwyczaj rozwiązanie do zarządzania danymi i ochrony (takie jak StorSimple Snapshot Manager) lub aplikacji kopii zapasowej, wywołuje usługi VSS i prosi o zebranie informacji z oprogramowania modułu zapisującego w aplikacji docelowej.
2. Usługa VSS kontaktuje się ze składnikiem modułu zapisującego w celu pobrania opisu danych. Moduł zapisujący zwraca opis danych, które mają być archiwizowane. 
3. Usługa VSS sygnalizuje moduł zapisujący, aby przygotować aplikację do tworzenia kopii zapasowych. Moduł zapisujący przygotowuje dane do tworzenia kopii zapasowych, wykonując otwarte transakcje, aktualizując dzienniki transakcji i tak dalej, a następnie powiadamia usługi VSS.
4. Usługa VSS nakazuje modułowi zapisującego tymczasowe zatrzymanie magazynów danych aplikacji i upewnienie się, że żadne dane nie są zapisywane na woluminie podczas tworzenia kopii w tle. Ten krok zapewnia spójność danych i trwa nie więcej niż 60 sekund.
5. Usługa VSS instruuje dostawcę, aby utworzyć kopię w tle. Dostawcy, którzy mogą być oparte na oprogramowaniu lub sprzęcie, zarządzają woluminami, które są aktualnie uruchomione, i tworzą ich kopie w tle na żądanie. Dostawca tworzy kopię w tle i powiadamia vss po jej zakończeniu.
6. Usługa VSS kontaktuje się z modułem zapisu, aby powiadomić aplikację, że operacje we/wy mogą zostać wznowione, a także potwierdzić, że operacje we/wy zostały pomyślnie wstrzymane podczas tworzenia kopii w tle. 
7. Jeśli kopia zakończyła się pomyślnie, usługa VSS zwraca lokalizację kopii żądaczowi. 
8. Jeśli dane zostały zapisane podczas tworzenia kopii w tle, kopia zapasowa będzie niespójna. Usługa VSS usuwa kopię w tle i powiadamia żądającego. Żądający może automatycznie powtórzyć proces tworzenia kopii zapasowej lub powiadomić administratora o ponowieniu próby w późniejszym czasie.

Zobacz poniższą ilustrację.

![Proces usługi VSS](./media/storsimple-what-is-snapshot-manager/HCS_SSM_VSS_process.png)

**Proces usługi kopiowania woluminów w tle systemu Windows** 

## <a name="backup-types-and-backup-policies"></a>Typy kopii zapasowych i zasady tworzenia kopii zapasowych
Za pomocą StorSimple Snapshot Manager można wyw. Za pomocą StorSimple Snapshot Manager można natychmiast wykonać kopię zapasową danych lub użyć zasad tworzenia kopii zapasowych, aby utworzyć harmonogram automatycznego wykonywania kopii zapasowych. Zasady tworzenia kopii zapasowych umożliwiają również określenie liczby migawek, które zostaną zachowane. 

### <a name="backup-types"></a>Typy kopii zapasowych
Za pomocą StorSimple Snapshot Manager można utworzyć następujące typy kopii zapasowych:

* **Migawki lokalne** — migawki lokalne są kopie w czasie danych woluminu, które są przechowywane na urządzeniu StorSimple. Zazwyczaj ten typ kopii zapasowej można szybko utworzyć i przywrócić. Można użyć migawki lokalnej, tak jak lokalnej kopii zapasowej.
* **Migawki chmury** — migawki chmury są kopie w czasie danych woluminów, które są przechowywane w chmurze. Migawka w chmurze jest odpowiednikiem migawki replikowanej w innym systemie magazynu poza siedzibą firmy. Migawki chmury są szczególnie przydatne w scenariuszach odzyskiwania po awarii.

### <a name="on-demand-and-scheduled-backups"></a>Kopie zapasowe na żądanie i zaplanowane
Za pomocą StorSimple Snapshot Manager można zainicjować jednorazową kopię zapasową, która ma zostać utworzona natychmiast, lub użyć zasad tworzenia kopii zapasowych, aby zaplanować operacje cyklicznego tworzenia kopii zapasowych.

Zasady tworzenia kopii zapasowych to zestaw reguł automatycznych, których można używać do planowania regularnych kopii zapasowych. Zasady tworzenia kopii zapasowych umożliwiają zdefiniowanie częstotliwości i parametrów do wykonywania migawek określonej grupy woluminów. Za pomocą zasad można określić daty rozpoczęcia i wygaśnięcia, godziny, częstotliwości i wymagania dotyczące przechowywania, zarówno dla migawek lokalnych, jak i w chmurze. Zasady są stosowane natychmiast po zdefiniowaniu. 

Za pomocą StorSimple Snapshot Manager można skonfigurować lub ponownie skonfigurować zasady tworzenia kopii zapasowych w razie potrzeby. 

Skonfiguruj następujące informacje dla każdej utworzonej zasady tworzenia kopii zapasowych:

* **Nazwa** — unikatowa nazwa wybranej zasady tworzenia kopii zapasowych.
* **Typ** — typ zasad tworzenia kopii zapasowych; migawka lokalna lub migawka w chmurze.
* **Grupa woluminów** — grupa woluminów, do której przypisano wybrane zasady tworzenia kopii zapasowej.
* **Przechowywanie** — liczba kopii zapasowych do zachowania. Jeśli zaznaczysz pole **Wszystkie,** wszystkie kopie zapasowe są zachowywane do momentu osiągnięcia maksymalnej liczby kopii zapasowych na woluminie, w którym to momencie zasady nie powiodą się i wygenerują komunikat o błędzie. Alternatywnie można określić liczbę kopii zapasowych do zachowania (od 1 do 64).
* **Data** — data utworzenia zasad tworzenia kopii zapasowej.

Aby uzyskać informacje dotyczące konfigurowania zasad tworzenia kopii zapasowych, przejdź do [tematu Tworzenie zasad tworzenia kopii zapasowych i zarządzanie nimi za pomocą Menedżera migawek StorSimple](storsimple-snapshot-manager-manage-backup-policies.md).

### <a name="backup-job-monitoring-and-management"></a>Monitorowanie zadań tworzenia kopii zapasowych i zarządzanie nimi
Menedżer migawek StorSimple służy do monitorowania nadchodzących, zaplanowanych i ukończonych zadań tworzenia kopii zapasowych oraz zarządzania nimi. Ponadto StorSimple Snapshot Manager udostępnia katalog do 64 ukończonych kopii zapasowych. Za pomocą katalogu można znaleźć i przywrócić woluminy lub pojedyncze pliki. 

Aby uzyskać informacje dotyczące monitorowania zadań tworzenia kopii zapasowych, przejdź do punktu [Użyj Menedżera migawek StorSimple, aby wyświetlić zadania tworzenia kopii zapasowych i zarządzać nimi.](storsimple-snapshot-manager-manage-backup-jobs.md)

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [administrowaniu rozwiązaniem StorSimple za pomocą Menedżera migawek StorSimple.](storsimple-snapshot-manager-admin.md)
* Pobierz [StorSimple Snapshot Manager](https://www.microsoft.com/download/details.aspx?id=44220).

