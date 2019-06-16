---
title: Użycie usługi Azure Backup do zastąpienia infrastruktury taśmy
description: Dowiedz się, jak usługa Azure Backup udostępnia semantykę przypominającej taśm, co pozwala na wykonywanie kopii zapasowych i przywracania danych na platformie Azure
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 04/30/2017
ms.author: dacurwin
ms.openlocfilehash: d768f0fae9487a555f6ace12303f8a4bd7cb8bd1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65146020"
---
# <a name="move-your-long-term-storage-from-tape-to-the-azure-cloud"></a>Przenieś swoje długoterminowego z taśmy do chmury platformy Azure
Klienci platformy Azure i kopii zapasowych programu System Center Data Protection Manager wykonywać następujące czynności:

* Utwórz kopię zapasową danych w harmonogramach, które najlepiej odpowiadają potrzebom organizacji.
* Zachowaj dane kopii zapasowej przez dłuższy czas
* Należy ich długoterminowego przechowywania danych wymagających (zamiast taśm) platformy Azure.

W tym artykule wyjaśniono, jak klienci można włączyć zasady tworzenia kopii zapasowej i przechowywania. Klienci, którzy taśmy były używane w celu ich długo-długoterminową zachowywania musi już zaawansowane i możliwego do użycia zamiast dzięki dostępności tej funkcji. Ta funkcja jest włączona w najnowszej wersji usługi Azure Backup (który jest dostępny [tutaj](https://aka.ms/azurebackup_agent)). System Center DPM klienci muszą przeprowadzić aktualizację do, co najmniej programu DPM 2012 R2 pakiet zbiorczy aktualizacji 5 przed rozpoczęciem korzystania z programu DPM przy użyciu usługi Azure Backup.

## <a name="what-is-the-backup-schedule"></a>Co to jest harmonogram tworzenia kopii zapasowych?
Harmonogram tworzenia kopii zapasowych wskazuje częstotliwość operacji tworzenia kopii zapasowej. Na przykład ustawienia na poniższym ekranie wskazują podjęcie kopie zapasowe codziennie o godzinie 18: 00 i o północy.

![Dzienny harmonogram](./media/backup-azure-backup-cloud-as-tape/dailybackupschedule.png)

Klientów można także zaplanować cotygodniowej kopii zapasowej. Na przykład ustawienia na poniższym ekranie wskazują kopie zapasowe podjęcie każdej alternatywny niedziela & środa 9:30:00 i 1:00:00.

![Harmonogram tygodniowy](./media/backup-azure-backup-cloud-as-tape/weeklybackupschedule.png)

## <a name="what-is-the-retention-policy"></a>Co to są zasady przechowywania?
Zasady przechowywania określają czas trwania, dla której muszą być przechowywane kopii zapasowej. Zamiast określać "wspólne zasady" dla wszystkich punktów kopii zapasowej, klienci mogą określać różne zasady przechowywania na podstawie, gdy jest wykonywana kopia zapasowa. Na przykład punktu kopii zapasowej być pobierane raz dziennie, która służy jako punkt odzyskiwania operacyjnego, są zachowywane przez 90 dni. Punktu kopii zapasowej, wykonywane na końcu każdego kwartału w celach związanych z inspekcją są zachowywane przez dłuższy czas.

![Zasady przechowywania](./media/backup-azure-backup-cloud-as-tape/retentionpolicy.png)

Łączna liczba "punkty przechowywania" określone w tej zasadzie to 90 (punkty dzienny) + 40 (jeden kwartał przez okres 10 lat) = 130.

## <a name="example--putting-both-together"></a>Przykład — zarówno zestawiania
![Przykładowy ekran](./media/backup-azure-backup-cloud-as-tape/samplescreen.png)

1. **Zasady przechowywania codziennych**: Wykonywane codzienne kopie zapasowe są przechowywane przez siedem dni.
2. **Sady przechowywania**: Kopie zapasowe wykonywane codziennie o północy i 18: 00 Sobota są zachowywane przez 4 tygodnie
3. **Zasady przechowywania miesięczne**: Kopii zapasowych wykonanych o północy i 18: 00 w sobotę ostatniego dnia każdego miesiąca są zachowywane przez 12 miesięcy
4. **Roczne zasady przechowywania**: Kopii zapasowych wykonanych o północy w ostatnią sobotę co marca są zachowywane przez okres 10 lat

Całkowita liczba "punkty przechowywania" (punkty, w których klient może przywrócić dane) na powyższym diagramie jest obliczana w następujący sposób:

* dwa punkty dziennie w przypadku siedmiu dni = 14 punktów odzyskiwania
* dwa punkty tygodniowo cztery tygodnie = 8 punktów odzyskiwania
* dwa punkty miesięcznie przez 12 miesięcy = 24 punktów odzyskiwania
* wskazuje jeden punkt rocznie na 10 lat = 10 odzyskiwania

Całkowita liczba punktów odzyskiwania jest 56.

> [!NOTE]
> Za pomocą usługi Azure Backup można utworzyć maksymalnie 9999 punktów odzyskiwania na chronione wystąpienie. Chronione wystąpienie to komputer, serwer (fizyczny lub wirtualny) lub obciążenia, który tworzy kopie zapasowe na platformie Azure.
>

## <a name="advanced-configuration"></a>Konfiguracja zaawansowana
Klikając **Modyfikuj** na poprzednim ekranie klienci mają bardziej elastyczną określenie harmonogramu przechowywania.

![Modyfikuj](./media/backup-azure-backup-cloud-as-tape/modify.png)

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat usługi Azure Backup zobacz:

* [Wprowadzenie do usługi Azure Backup](backup-introduction-to-azure-backup.md)
* [Wypróbuj usługę Azure Backup](backup-try-azure-backup-in-10-mins.md)
