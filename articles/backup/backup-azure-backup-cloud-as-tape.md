---
title: Użycie usługi Azure Backup do zastąpienia infrastruktury taśmy
description: Dowiedz się, jak Azure Backup zapewnia semantykę podobną do taśmy, która umożliwia tworzenie kopii zapasowych i przywracanie danych na platformie Azure
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 04/30/2017
ms.author: dacurwin
ms.openlocfilehash: 2850fab3a5639673008a7433db48911ced832401
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74091108"
---
# <a name="move-your-long-term-storage-from-tape-to-the-azure-cloud"></a>Przenoszenie długoterminowego magazynu z taśmy do chmury platformy Azure

Klienci Azure Backup i System Center Data Protection Manager mogą:

* Utwórz kopię zapasową danych w harmonogramach, które najlepiej odpowiadają potrzebom organizacji.
* Przechowuj dane kopii zapasowej przez dłuższe okresy
* Ustaw platformę Azure jako część długoterminowych potrzeb przechowywania (zamiast taśm).

W tym artykule wyjaśniono, jak klienci mogą włączyć zasady tworzenia kopii zapasowych i przechowywania danych. Klienci, którzy wykorzystują taśmy do rozwiązywania długoterminowych potrzeb związanych z przechowywaniem, mają teraz zaawansowaną i wydajną alternatywę w zakresie dostępności tej funkcji. Ta funkcja jest włączona w najnowszej wersji Azure Backup (która jest dostępna w [tym miejscu](https://aka.ms/azurebackup_agent)). Przed rozpoczęciem korzystania z programu DPM z usługą Azure Backup klienci programu System Center DPM muszą zaktualizować program do wersji co najmniej programu DPM 2012 R2 UR5.

## <a name="what-is-the-backup-schedule"></a>Co to jest harmonogram tworzenia kopii zapasowych?

Harmonogram tworzenia kopii zapasowych wskazuje częstotliwość operacji tworzenia kopii zapasowej. Na przykład ustawienia na poniższym ekranie wskazują, że kopie zapasowe są wykonywane codziennie o godzinie 18:00 i północy.

![Dzienny harmonogram](./media/backup-azure-backup-cloud-as-tape/dailybackupschedule.png)

Klienci mogą również zaplanować cotygodniowe wykonywanie kopii zapasowych. Na przykład ustawienia na poniższym ekranie wskazują, że kopie zapasowe są pobierane dla każdej alternatywnej niedzieli & środę o godzinie 9:10:30 i 1:10:00.

![Harmonogram tygodniowy](./media/backup-azure-backup-cloud-as-tape/weeklybackupschedule.png)

## <a name="what-is-the-retention-policy"></a>Co to są zasady przechowywania?

Zasady przechowywania określają czas przechowywania kopii zapasowej. Zamiast określania "zasad płaskich" dla wszystkich punktów kopii zapasowych, klienci mogą określić różne zasady przechowywania na podstawie czasu wykonywania kopii zapasowej. Na przykład punkt kopii zapasowej dziennie, który służy jako punkt odzyskiwania, jest zachowywany przez 90 dni. Punkt kopii zapasowej wykonany na końcu każdego kwartału na potrzeby inspekcji jest zachowywany przez dłuższy czas.

![Zasady przechowywania](./media/backup-azure-backup-cloud-as-tape/retentionpolicy.png)

Łączna liczba "punktów przechowywania" określonych w tych zasadach to 90 (dzienne punkty) + 40 (jeden kwartał przez 10 lat) = 130.

## <a name="example--putting-both-together"></a>Przykład — umieszczenie obu jednocześnie

![Przykładowy ekran](./media/backup-azure-backup-cloud-as-tape/samplescreen.png)

1. **Dzienne zasady przechowywania**: kopie zapasowe wykonywane codziennie są przechowywane przez siedem dni.
2. **Zasady przechowywania tygodniowego**: kopie zapasowe wykonywane codziennie o północy i 18:00 soboty są zachowywane przez cztery tygodnie
3. **Zasady przechowywania miesięcznie**: kopie zapasowe wykonane o północy i 18:00 w ostatniej sobotę każdego miesiąca są zachowywane przez 12 miesięcy.
4. **Zasady przechowywania rocznego**: kopie zapasowe wykonane o północy w ostatniej sobotę każdej marca są zachowywane przez 10 lat

Całkowita liczba "punktów przechowywania" (punkty, z których klient może przywrócić dane) na powyższym diagramie jest obliczana w następujący sposób:

* dwa punkty na dzień przez siedem dni = 14 punktów odzyskiwania
* dwa punkty na tydzień przez cztery tygodnie = 8 punktów odzyskiwania
* dwa punkty miesięcznie przez 12 miesięcy = 24 punkty odzyskiwania
* jeden punkt na rok na 10 lat = 10 punktów odzyskiwania

Łączna liczba punktów odzyskiwania to 56.

> [!NOTE]
> Za pomocą Azure Backup można utworzyć maksymalnie 9999 punktów odzyskiwania dla każdego chronionego wystąpienia. Chronione wystąpienie jest komputerem, serwerem (fizycznym lub wirtualnym) lub obciążeniem, które tworzy kopię zapasową na platformie Azure.
>

## <a name="advanced-configuration"></a>Konfiguracja zaawansowana

Klikając przycisk **Modyfikuj** na poprzednim ekranie, klienci mają większą elastyczność w określaniu harmonogramów przechowywania.

![Modyfikuj](./media/backup-azure-backup-cloud-as-tape/modify.png)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat Azure Backup, zobacz:

* [Wprowadzenie do usługi Azure Backup](backup-introduction-to-azure-backup.md)
* [Wypróbuj Azure Backup](backup-try-azure-backup-in-10-mins.md)
