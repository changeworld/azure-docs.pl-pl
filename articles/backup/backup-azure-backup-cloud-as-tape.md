---
title: Użycie usługi Azure Backup do zastąpienia infrastruktury taśmy
description: Dowiedz się, jak Kopia zapasowa Azure udostępnia semantyki przypominającej taśm, co pozwala na wykonywanie kopii zapasowych i przywracania danych na platformie Azure
services: backup
author: trinadhk
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 1/10/2017
ms.author: saurse
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ff30dd0e4c7cadabddbeddc38c28a773db68d8ff
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34606498"
---
# <a name="move-your-long-term-storage-from-tape-to-the-azure-cloud"></a>Przenieś magazyn długoterminowy z taśmy w chmurze platformy Azure
Azure Backup i System Center Data Protection Manager klienci mogą:

* Utwórz kopię zapasową danych w harmonogramy, które najlepiej odpowiadają potrzebom organizacji.
* Przechowywanie danych kopii zapasowej przez dłuższy czas
* Wprowadź Azure ich przechowywania długoterminowego wymagających (zamiast taśma).

W tym artykule opisano, jak włączyć zasady tworzenia kopii zapasowej i przechowywania klientów. Klienci, którzy korzystają z taśmy w celu rozwiązania ich długo-długoterminowego — przechowywania musi już wydajny i realną alternatywę dostępności tej funkcji. Ta funkcja jest włączona w najnowszym wydaniu usługi Azure Backup (który jest dostępny [tutaj](http://aka.ms/azurebackup_agent)). Użytkownicy programu System Center DPM należy zaktualizować, co najmniej programu DPM 2012 R2 pakiet zbiorczy aktualizacji 5 przed użyciem programu DPM w usłudze Kopia zapasowa Azure.

## <a name="what-is-the-backup-schedule"></a>Co to jest harmonogram tworzenia kopii zapasowej?
Harmonogram tworzenia kopii zapasowych wskazuje częstotliwość tworzenia kopii zapasowej. Na przykład ustawienia na poniższym ekranie wskazują podjęcie kopii zapasowych codziennie o 18: 00 i o północy.

![Harmonogram dzienny](./media/backup-azure-backup-cloud-as-tape/dailybackupschedule.png)

Klientów można również zaplanować kopia zapasowa co tydzień. Na przykład ustawienia na poniższym ekranie wskazują kopii zapasowych podjęcie co alternatywny niedziela & środa na 9:30 a 1:00:00.

![Harmonogram tygodniowy](./media/backup-azure-backup-cloud-as-tape/weeklybackupschedule.png)

## <a name="what-is-the-retention-policy"></a>Co to jest zasady przechowywania?
Zasady przechowywania określają czas trwania, dla której muszą być przechowywane kopii zapasowej. Zamiast określać "wspólne zasady" dla wszystkich punktów kopii zapasowej, klientów można określić różne zasady przechowywania na podstawie czasu tworzenia kopii zapasowej. Na przykład punktu kopii zapasowej pobierane raz dziennie, która służy jako punkt odzyskiwania operacyjne, jest zachowywana przez 90 dni. Punktu kopii zapasowej są pod koniec każdego kwartału dla celów inspekcji jest zachowywana przez dłuższy czas.

![Zasady przechowywania](./media/backup-azure-backup-cloud-as-tape/retentionpolicy.png)

Całkowita liczba "punkty przechowywania" określone w tej zasadzie wynosi 90 (codzienne punkty) + 40 (każdy kwartał 10 lat) = 130.

## <a name="example--putting-both-together"></a>Przykład — zarówno zestawienie
![Przykład ekranu](./media/backup-azure-backup-cloud-as-tape/samplescreen.png)

1. **Zasady przechowywania codziennych**: kopii zapasowych wykonanych codziennie są przechowywane na siedem dni.
2. **Co tydzień zasady przechowywania**: wykonaną codziennie o północy i 18: 00 Sobota kopie zapasowe są zachowywane przez 4 tygodnie
3. **Miesięczne zasady przechowywania**: kopii zapasowych wykonanych na północ i 18: 00 w sobotę ostatniego dnia każdego miesiąca są zachowywane przez 12 miesięcy
4. **Zasady przechowywania corocznych**: kopii zapasowych wykonanych o północy w ostatnią sobotę co marca zostaną zachowane 10 lat

Całkowita liczba "punkty przechowywania" (punkty, z których klient może przywrócić dane) na powyższym diagramie jest obliczana w następujący sposób:

* dwa punkty dziennie siedem dni = 14 punktów odzyskiwania
* dwa punkty tygodniowo dla cztery tygodnie = 8 punktów odzyskiwania
* dwa punkty USD miesięcznie przez 12 miesięcy = 24 punktów odzyskiwania
* wskazuje jeden punkt rocznie na 10 lat = 10 odzyskiwania

Całkowita liczba punktów odzyskiwania jest w 56.

> [!NOTE]
> Kopia zapasowa Azure nie ma ograniczenie liczby punktów odzyskiwania.
>
>

## <a name="advanced-configuration"></a>Konfiguracja zaawansowana
Klikając **Modyfikuj** na ekranie poprzedniego klienci mają bardziej elastyczną określenie harmonogramu przechowywania.

![Modyfikuj](./media/backup-azure-backup-cloud-as-tape/modify.png)

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji o usłudze Azure Backup zobacz:

* [Wprowadzenie do usługi Azure Backup](backup-introduction-to-azure-backup.md)
* [Spróbuj kopia zapasowa Azure](backup-try-azure-backup-in-10-mins.md)
