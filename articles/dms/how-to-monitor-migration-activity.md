---
title: Monitorowanie aktywności migracji — usługa migracji bazy danych Azure
description: Dowiedz się, jak używać usługi migracji bazy danych platformy Azure do monitorowania aktywności związanej z migracją.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: 31b49cdd9e0e5569981b2a0b0c6efcab7239e019
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77648516"
---
# <a name="monitor-migration-activity-using-the-azure-database-migration-service"></a>Monitorowanie aktywności migracji przy użyciu usługi migracji bazy danych azure
W tym artykule dowiesz się, jak monitorować postęp migracji zarówno na poziomie bazy danych, jak i na poziomie tabeli.

## <a name="monitor-at-the-database-level"></a>Monitoruj na poziomie bazy danych
Aby monitorować aktywność na poziomie bazy danych, wyświetl blok na poziomie bazy danych:

![Blok na poziomie bazy danych](media/how-to-monitor-migration-activity/dms-database-level-blade.png)

> [!NOTE]
> Wybranie hiperłącza bazy danych spowoduje wyświetlenie listy tabel i ich postępu migracji.

W poniższej tabeli wymieniono pola w bloku na poziomie bazy danych i opisano różne wartości stanu skojarzone z każdym z nich.

<table id='overview' class='overview'>
  <thead>
    <tr>
      <th class="x-hidden-focus"><strong>Nazwa pola</strong></th>
      <th><strong>Podstanek pola</strong></th>
      <th><strong>Opis</strong></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td rowspan="3" class="ActivityStatus"><strong>Stan działania</strong></td>
      <td>Działanie</td>
      <td>Działanie migracji jest uruchomione.</td>
    </tr>
    <tr>
      <td>Powodzenie</td>
      <td>Działanie związane z migracją zakończyło się sukcesem bez problemów.</td>
    </tr>
    <tr>
      <td>Faulted</td>
      <td>Migracja nie powiodła się. Wybierz łącze "Zobacz szczegóły błędu" w obszarze Szczegóły migracji dla pełnego komunikatu o błędzie.</td>
    </tr>
    <tr>
      <td rowspan="4" class="Status"><strong>Stan</strong></td>
      <td>Inicjowanie</td>
      <td>DMS konfiguruje potok migracji.</td>
    </tr>
    <tr>
      <td>Działanie</td>
      <td>Potok DMS jest uruchomiony i przeprowadza migrację.</td>
    </tr>
    <tr>
      <td>Complete</td>
      <td>Migracja zakończona.</td>
    </tr>
    <tr>
      <td>Niepowodzenie</td>
      <td>Migracja nie powiodła się. Kliknij szczegóły migracji, aby zobaczyć błędy migracji.</td>
    </tr>
    <tr>
      <td rowspan="5" class="migration-details"><strong>Szczegóły dotyczące migracji</strong></td>
      <td>Inicjowanie potoku migracji</td>
      <td>DMS konfiguruje potok migracji.</td>
    </tr>
    <tr>
      <td>Pełne ładowanie danych w toku</td>
      <td>DMS wykonuje obciążenie początkowe.</td>
    </tr>
    <tr>
      <td>Gotowy na cutover</td>
      <td>Po zakończeniu początkowego ładowania DMS oznaczy bazę danych jako gotową do przejścia. Użytkownik powinien sprawdzić, czy dane zostały przechwycone podczas ciągłej synchronizacji.</td>
    </tr>
    <tr>
      <td>Wszystkie zastosowane zmiany</td>
      <td>Początkowe obciążenie i synchronizacja ciągła są zakończone. Ten stan występuje również po pomyślnym przecięciu bazy danych.</td>
    </tr>
    <tr>
      <td>Zobacz szczegóły błędu</td>
      <td>Kliknij łącze, aby wyświetlić szczegóły błędu.</td>
    </tr>
    <tr>
      <td rowspan="1" class="duration"><strong>Czas trwania</strong></td>
      <td>Nie dotyczy</td>
      <td>Całkowity czas od zainicjowania aktywności migracji do zakończonej migracji lub błędu migracji.</td>
    </tr>
     </tbody>
</table>

## <a name="monitor-at-table-level--quick-summary"></a>Monitor na poziomie tabeli – szybkie podsumowanie
Aby monitorować aktywność na poziomie tabeli, wyświetl blok na poziomie tabeli. Górna część bloku pokazuje szczegółową liczbę wierszy migrowanych w pełnej aktualizacji obciążenia i przyrostowych. 

W dolnej części bloku znajduje się lista tabel i krótkie podsumowanie postępu migracji.

![Ostrze na poziomie stołu - krótkie podsumowanie](media/how-to-monitor-migration-activity/dms-table-level-blade-summary.png)

W poniższej tabeli opisano pola wyświetlane w szczegółach na poziomie tabeli.

| Nazwa pola        | Opis       |
| ------------- | ------------- |
| **Pełne obciążenie zakończone**      | Liczba tabel ukończonych pełnego ładowania danych. |
| **Pełne ładowanie w kolejce**      | Liczba tabel w kolejce do pełnego obciążenia.      |
| **Pełne obciążenie** | Liczba tabel nie powiodła się.      |
| **Aktualizacje przyrostowe**      | Liczba aktualizacji przechwytywania danych zmian (CDC) w wierszach zastosowanych do obiektu docelowego. |
| **Wstawienia przyrostowe**      | Liczba wstawia CDC w wierszach zastosowanych do obiektu docelowego.      |
| **Usuwanie przyrostowe** | Liczba usuń CDC w wierszach zastosowanych do obiektu docelowego.      |
| **Oczekujące zmiany**      | Liczba CDC w wierszach, które nadal czekają, aby uzyskać zastosowanie do obiektu docelowego. |
| **Zastosowane zmiany**      | Suma aktualizacji CDC, wstawia i usuwa w wierszach stosowanych do obiektu docelowego.      |
| **Tabele w stanie błędu** | Liczba tabel, które są w stanie "błąd" podczas migracji. Niektóre przykłady, że tabele mogą przejść do stanu błędu są, gdy istnieją duplikaty zidentyfikowane w docelowych lub dane nie jest zgodne ładowania w tabeli docelowej.      |

## <a name="monitor-at-table-level--detailed-summary"></a>Monitor na poziomie tabeli – Szczegółowe podsumowanie
Istnieją dwie karty, które pokazują postęp migracji w pełne ładowanie i przyrostowe synchronizacji danych.
    
![Karta pełne obciążenie](media/how-to-monitor-migration-activity/dms-full-load-tab.png)

![Karta Przyrostowa synchronizacja danych](media/how-to-monitor-migration-activity/dms-incremental-data-sync-tab.png)

W poniższej tabeli opisano pola wyświetlane w postępie migracji na poziomie tabeli.

| Nazwa pola        | Opis       |
| ------------- | ------------- |
| **Status - Synchronizacja**      | Synchronizacja ciągła jest uruchomiona. |
| **Wstawić**      | Liczba wstawia CDC w wierszach zastosowanych do obiektu docelowego.      |
| **Aktualizacja** | Liczba aktualizacji CDC w wierszach zastosowanych do obiektu docelowego.      |
| **Usuwanie**      | Liczba usuń CDC w wierszach zastosowanych do obiektu docelowego. |
| **Całkowita liczba zastosowanych**      | Suma aktualizacji CDC, wstawia i usuwa w wierszach stosowanych do obiektu docelowego. |
| **Błędy danych** | Liczba błędów danych wystąpiła w tej tabeli. Niektóre przykłady błędów to *511: Nie można utworzyć wiersza o rozmiarze %d, który jest większy niż dopuszczalny maksymalny rozmiar wiersza %d, 8114: Błąd konwertowania typu danych %ls na %ls.*  Klient powinien zbadać z tabeli dms_apply_exceptions w witrynie docelowej platformy Azure, aby wyświetlić szczegóły błędu.    |

> [!NOTE]
> Wartości CDC wstawiania, aktualizowania i usuwania oraz całkowitego zastosowania mogą się zmniejszać, gdy baza danych jest przecięta lub migracja zostanie ponownie uruchomiona.

## <a name="next-steps"></a>Następne kroki
- Zapoznaj się ze wskazówkami dotyczącymi migracji w [Przewodniku migracji bazy danych firmy](https://datamigration.microsoft.com/)Microsoft .