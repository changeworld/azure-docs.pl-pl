---
title: Jak wymienić infrastrukturę taśmową
description: Dowiedz się, jak usługa Azure Backup udostępnia semantykę podobną do taśmy, która umożliwia tworzenie kopii zapasowych i przywracanie danych na platformie Azure
ms.topic: conceptual
ms.date: 04/30/2017
ms.openlocfilehash: aeda1cefc84d425855c40b793f8334936541e63f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77425106"
---
# <a name="move-your-long-term-storage-from-tape-to-the-azure-cloud"></a>Przenoszenie długoterminowego magazynu z taśmy do chmury platformy Azure

Klienci usługi Azure Backup and System Center Data Protection Manager mogą:

* Śmij zapasy danych w harmonogramach, które najlepiej odpowiadają potrzebom organizacyjnym.
* Zachowaj dane kopii zapasowej przez dłuższy czas.
* Spraw, aby platforma Azure była częścią ich długoterminowych potrzeb dotyczących przechowywania (zamiast taśmy).

W tym artykule wyjaśniono, jak klienci mogą włączyć zasady tworzenia kopii zapasowych i przechowywania. Klienci, którzy używają taśm do zaspokojenia swoich długoterminowych potrzeb retencji mają teraz potężną i realną alternatywę z dostępnością tej funkcji. Funkcja jest włączona w najnowszej wersji usługi Azure Backup (która jest dostępna [tutaj).](https://aka.ms/azurebackup_agent) Klienci programu System Center DPM muszą zaktualizować do co najmniej programu DPM 2012 R2 UR5 przed użyciem programu DPM z usługą Azure Backup.

## <a name="what-is-the-backup-schedule"></a>Co to jest harmonogram tworzenia kopii zapasowych?

Harmonogram tworzenia kopii zapasowych wskazuje częstotliwość operacji tworzenia kopii zapasowej. Na przykład ustawienia na poniższym ekranie wskazują, że kopie zapasowe są pobierane codziennie o godzinie 18:00 i o północy.

![Harmonogram dzienny](./media/backup-azure-backup-cloud-as-tape/dailybackupschedule.png)

Klienci mogą również zaplanować cotygodniową kopię zapasową. Na przykład ustawienia na poniższym ekranie wskazują, że kopie zapasowe są pobierane w każdą alternatywną niedzielę & środę o 9:30 i 1:00.

![Harmonogram tygodniowy](./media/backup-azure-backup-cloud-as-tape/weeklybackupschedule.png)

## <a name="what-is-the-retention-policy"></a>Co to jest zasady przechowywania?

Zasady przechowywania określa czas trwania, dla którego kopia zapasowa musi być przechowywana. Zamiast po prostu określać "zasady płaskie" dla wszystkich punktów kopii zapasowej, klienci mogą określić różne zasady przechowywania na podstawie momentu wykonywania kopii zapasowej. Na przykład punkt kopii zapasowej przyjmowany codziennie, który służy jako operacyjny punkt odzyskiwania, jest zachowywany przez 90 dni. Punkt kopii zapasowej wykonany na koniec każdego kwartału do celów inspekcji jest zachowywany przez dłuższy czas.

![Zasady przechowywania](./media/backup-azure-backup-cloud-as-tape/retentionpolicy.png)

Całkowita liczba "punktów retencji" określonych w tej zasadie wynosi 90 (punkty dzienne) + 40 (po jednym kwartale przez 10 lat) = 130.

## <a name="example--putting-both-together"></a>Przykład – Połączenie obu tych

![Przykładowy ekran](./media/backup-azure-backup-cloud-as-tape/samplescreen.png)

1. **Zasady dziennego przechowywania:** Kopie zapasowe pobierane codziennie są przechowywane przez siedem dni.
2. **Tygodniowe zasady przechowywania:** Kopie zapasowe wykonane o północy i 18:00 w sobotę są zachowywane przez cztery tygodnie.
3. **Miesięczne zasady przechowywania:** Kopie zapasowe wykonane o północy i 18:00 w ostatnią sobotę każdego miesiąca są zachowywane przez 12 miesięcy.
4. **Roczna polityka retencji**: Kopie zapasowe wykonane o północy w ostatnią sobotę każdego marca są zachowywane przez 10 lat.

Całkowita liczba "punktów przechowywania" (punktów, z których klient może przywrócić dane) na poprzednim diagramie jest obliczany w następujący sposób:

* dwa punkty dziennie przez siedem dni = 14 punktów odzyskiwania
* dwa punkty tygodniowo przez cztery tygodnie = 8 punktów odzysku
* dwa punkty miesięcznie przez 12 miesięcy = 24 punkty odzysku
* jeden punkt rocznie na 10 lat = 10 punktów odzysku

Całkowita liczba punktów odzyskiwania wynosi 56.

> [!NOTE]
> Za pomocą usługi Azure Backup można utworzyć maksymalnie 9999 punktów odzyskiwania na chronione wystąpienie. Chronione wystąpienie to komputer, serwer (fizyczny lub wirtualny) lub obciążenie, którego kopia zapasowa jest wykonywana na platformie Azure.
>

## <a name="advanced-configuration"></a>Konfiguracja zaawansowana

Klikając **przycisk Modyfikuj** na poprzednim ekranie, klienci mają większą elastyczność w określaniu harmonogramów przechowywania.

![Modyfikowanie](./media/backup-azure-backup-cloud-as-tape/modify.png)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat usługi Azure Backup, zobacz:

* [Wprowadzenie do usługi Azure Backup](backup-introduction-to-azure-backup.md)
* [Wypróbuj usługę Azure Backup](backup-try-azure-backup-in-10-mins.md)
