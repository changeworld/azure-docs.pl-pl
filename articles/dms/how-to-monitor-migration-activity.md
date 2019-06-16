---
title: Azure Database Migration Service umożliwia monitorowanie działania migracji | Dokumentacja firmy Microsoft
description: Dowiedz się, na potrzeby monitorowania działania migracji usługi Azure Database Migration Service.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 03/12/2019
ms.openlocfilehash: 325bbee3f3d5ad5097f710cb56fe03baff97388a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60532806"
---
# <a name="monitor-migration-activity"></a>Monitorowanie działań migracji
W tym artykule dowiesz się, jak monitorować postęp migracji zarówno w poziomie bazy danych, jak i w poziomie tabeli.

## <a name="monitor-at-the-database-level"></a>Monitorowanie na poziomie bazy danych
Aby monitorować aktywność na poziomie bazy danych, należy wyświetlić blok poziomu bazy danych:

![Blok poziomu bazy danych](media/how-to-monitor-migration-activity/dms-database-level-blade.png)

> [!NOTE]
> Zaznaczanie hiperlinku bazy danych zostanie wyświetlona lista tabel i ich postęp migracji.

Poniższa tabela zawiera listę pól, w bloku bazy danych na poziomie i w tym artykule opisano różne wartości stanu związany z każdą.

<table id='overview' class='overview'>
  <thead>
    <tr>
      <th class="x-hidden-focus"><strong>Nazwa pola</strong></th>
      <th><strong>Podstan pola</strong></th>
      <th><strong>Opis</strong></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td rowspan="3" class="ActivityStatus"><strong>Stan działania</strong></td>
      <td>Działanie</td>
      <td>Uruchomiono działanie migracji.</td>
    </tr>
    <tr>
      <td>Powodzenie</td>
      <td>Działania migracji zakończyła się pomyślnie bez problemów.</td>
    </tr>
    <tr>
      <td>Wystąpił błąd</td>
      <td>Migracja nie powiodła się. Wybierz link "Patrz szczegóły błędu" w obszarze Szczegóły migracji pełną komunikatu o błędzie.</td>
    </tr>
    <tr>
      <td rowspan="4" class="Status"><strong>Stan</strong></td>
      <td>Inicjowanie</td>
      <td>Usługa DMS to Konfigurowanie procesu migracji.</td>
    </tr>
    <tr>
      <td>Działanie</td>
      <td>Usługa DMS potoku jest uruchomiona i przeprowadzanie migracji.</td>
    </tr>
    <tr>
      <td>Zakończ</td>
      <td>Migracja została zakończona.</td>
    </tr>
    <tr>
      <td>Niepowodzenie</td>
      <td>Migracja nie powiodła się. Kliknij przycisk Szczegóły migracji, aby wyświetlić błędy migracji.</td>
    </tr>
    <tr>
      <td rowspan="5" class="migration-details"><strong>Szczegóły migracji</strong></td>
      <td>Inicjowanie potoku migracji</td>
      <td>Usługa DMS to Konfigurowanie procesu migracji.</td>
    </tr>
    <tr>
      <td>Pełne ładowanie danych w toku</td>
      <td>Usługa DMS wykonuje ładowania początkowego.</td>
    </tr>
    <tr>
      <td>Gotowe do uruchomienia produkcyjnego</td>
      <td>Po zakończeniu ładowania początkowego DMS spowoduje oznaczenie bazy danych jako gotowe do uruchomienia produkcyjnego. Należy sprawdzić, jeśli dane złapał ciągłej synchronizacji.</td>
    </tr>
    <tr>
      <td>Wszystkie zmiany zastosowane</td>
      <td>Zakończeniu ładowania początkowego i ciągłej synchronizacji. Ten stan występuje także po pomyślnym baza danych jest uruchomienie produkcyjne.</td>
    </tr>
    <tr>
      <td>Zobacz szczegóły błędu</td>
      <td>Kliknij link, aby wyświetlić szczegóły błędu.</td>
    </tr>
    <tr>
      <td rowspan="1" class="duration"><strong>Czas trwania</strong></td>
      <td>ND</td>
      <td>Łączny czas działania migracji inicjowany do migracji lub ustawienia komunikacji niezawodnej migracji.</td>
    </tr>
     </tbody>
</table>

## <a name="monitor-at-table-level--quick-summary"></a>Monitorowanie na poziomie tabeli — krótkie podsumowanie
Aby monitorować aktywność na poziomie tabeli, należy wyświetlić bloku na poziomie tabeli. Górna część bloku pokazuje szczegółowy liczbę wierszy migracji w pełne ładowanie i przyrostowych aktualizacji. 

Dolnej części bloku są wyświetlane tabele i przedstawiono krótkie podsumowanie postęp migracji.

![Blok tabeli — krótkie podsumowanie](media/how-to-monitor-migration-activity/dms-table-level-blade-summary.png)

W poniższej tabeli opisano pola wyświetlane w obszarze szczegółów na poziomie tabeli.

| Nazwa pola        | Opis       |
| ------------- | ------------- |
| **Ukończono pełne ładowanie**      | Liczba tabel ukończono pełne ładowanie danych. |
| **Pełne ładowanie w kolejce**      | Liczba tabel pełne ładowanie ich umieszczenia w kolejce.      |
| **Pełne ładowanie** | Liczba tabel nie powiodła się.      |
| **Aktualizacje przyrostowe**      | Liczba zmian danych przechwycenie aktualizacji (CDC) w wierszach zastosowany do obiektu docelowego. |
| **Wstawia przyrostowe**      | Wstawia liczbę przechwytywania zmian danych w wierszach zastosowany do obiektu docelowego.      |
| **Usuwa przyrostowe** | Usuwa liczba przechwytywania zmian danych w wierszach zastosowany do obiektu docelowego.      |
| **Oczekujące zmiany**      | Liczba przechwytywania zmian danych w wierszach, które wciąż oczekują na zastosowane do obiektu docelowego. |
| **Zastosowane zmiany**      | Łączna liczba przechwytywania zmian danych operacji wstawiania, aktualizacji i usuwa w wierszach zastosowany do obiektu docelowego.      |
| **Tabele w stanie Błąd** | Liczba tabel, które są w stanie "error" podczas migracji. Kilka przykładów, które tabele mogą być wprowadzanie do stanu błędu są, gdy znajdują się duplikaty określone w elemencie docelowym lub dane nie są zgodne, ładowanie w tabeli docelowej.      |

## <a name="monitor-at-table-level--detailed-summary"></a>Monitorowanie na poziomie tabeli — szczegółowe podsumowanie
Istnieją dwie karty, pokazujące postęp migracji w pełne ładowanie i Przyrostowa synchronizacja danych.
    
![Karta pełne ładowanie](media/how-to-monitor-migration-activity/dms-full-load-tab.png)

![Karta synchronizacji przyrostowej danych](media/how-to-monitor-migration-activity/dms-incremental-data-sync-tab.png)

W poniższej tabeli opisano pola wyświetlane w tabeli migracji poziomu postępu.

| Nazwa pola        | Opis       |
| ------------- | ------------- |
| **Stan — synchronizacja**      | Ciągłej synchronizacji jest uruchomiona. |
| **Wstaw**      | Wstawia liczbę przechwytywania zmian danych w wierszach zastosowany do obiektu docelowego.      |
| **Aktualizacja** | Liczba aktualizacji przechwytywania zmian danych w wierszach zastosowany do obiektu docelowego.      |
| **Usuwanie**      | Usuwa liczba przechwytywania zmian danych w wierszach zastosowany do obiektu docelowego. |
| **Całkowita liczba zastosowanych**      | Łączna liczba przechwytywania zmian danych operacji wstawiania, aktualizacji i usuwa w wierszach zastosowany do obiektu docelowego. |
| **Błędy danych** | Liczba błędów danych wydarzyło się w tej tabeli. Oto kilka przykładów błędów *511: Nie można utworzyć wiersza o wielkości %d, który jest większy niż dopuszczalny rozmiar maksymalny rozmiar wiersza % d, 8114: Błąd podczas konwertowania typu danych %ls % ls.*  Klient powinien zapytanie z tabeli dms_apply_exceptions w celu platformy Azure, aby wyświetlić szczegóły błędu.    |

> [!NOTE]
> Przechwytywanie zmian danych wartości Insert, Update i Delete i łączna liczba stosowane mogą się zmniejszyć, gdy baza danych jest uruchomienie produkcyjne lub ponownym uruchomieniu migracji.

## <a name="next-steps"></a>Kolejne kroki
- Przejrzyj wskazówki dotyczące migracji w programie Microsoft [Przewodnik po migracji bazy danych](https://datamigration.microsoft.com/).