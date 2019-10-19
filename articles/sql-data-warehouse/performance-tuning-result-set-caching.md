---
title: Dostrajanie wydajności z buforowaniem zestawu wyników | Microsoft Docs
description: Omówienie funkcji
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 10/10/2019
ms.author: xiaoyul
ms.reviewer: nidejaco;
ms.openlocfilehash: 3e6af57840cf60516aba994a6b5728bfb7b35f09
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72553530"
---
# <a name="performance-tuning-with-result-set-caching"></a>Dostrajanie wydajności z buforowaniem zestawu wyników  
Gdy buforowanie zestawu wyników jest włączone, Azure SQL Data Warehouse automatycznie buforuje wyniki zapytania w bazie danych użytkownika do powtarzanego użycia.  Dzięki temu kolejne wykonania zapytania będą uzyskiwać wyniki bezpośrednio z utrwalonej pamięci podręcznej, więc ponowne obliczenie nie jest konieczne.   Buforowanie zestawu wyników zwiększa wydajność zapytań i zmniejsza użycie zasobów obliczeniowych.  Ponadto zapytania korzystające z zbuforowanego zestawu wyników nie używają żadnych miejsc współbieżności, więc nie są wliczane do istniejących limitów współbieżności. W celu zapewnienia bezpieczeństwa użytkownicy mogą uzyskiwać dostęp do buforowanych wyników tylko wtedy, gdy mają one takie same uprawnienia dostępu do danych, jak użytkownicy tworzący buforowane wyniki.  

## <a name="key-commands"></a>Polecenia kluczowe
[Włącz/Wyłącz buforowanie zestawu wyników dla bazy danych użytkownika](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azure-sqldw-latest)

[Włącz/Wyłącz buforowanie zestawu wyników dla sesji](https://docs.microsoft.com/sql/t-sql/statements/set-result-set-caching-transact-sql?view=azure-sqldw-latest)

[Sprawdź rozmiar buforowanego zestawu wyników](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-showresultcachespaceused-transact-sql?view=azure-sqldw-latest)  

[Czyszczenie pamięci podręcznej](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-dropresultsetcache-transact-sql?view=azure-sqldw-latest)

## <a name="whats-not-cached"></a>Co nie jest buforowane  

Po włączeniu buforowania zestawu wyników dla bazy danych wyniki są buforowane dla wszystkich zapytań do momentu zapełnienia pamięci podręcznej, z wyjątkiem tych zapytań:
- Zapytania korzystające z funkcji niedeterministycznych, takich jak DateTime. Now ()
- Zapytania korzystające z funkcji zdefiniowanych przez użytkownika
- Zapytania korzystające z tabel z włączonymi zabezpieczeniami na poziomie wierszy lub zabezpieczeniami
- Zapytania zwracające dane z rozmiarem wiersza większym niż 64 KB

Zapytania z dużymi zestawami wyników (na przykład > 1 000 000 wierszy) mogą spowodować wolniejszą wydajność podczas pierwszego uruchomienia podczas tworzenia pamięci podręcznej wyników.

## <a name="when-cached-results-are-used"></a>Gdy są używane buforowane wyniki

Buforowany zestaw wyników jest ponownie używany w przypadku zapytania, jeśli wszystkie poniższe wymagania zostały spełnione:
- Użytkownik, który uruchamia zapytanie, ma dostęp do wszystkich tabel, do których odwołuje się zapytanie.
- Istnieje dokładne dopasowanie między nowym zapytaniem i poprzednim zapytaniem, które spowodowało wygenerowanie pamięci podręcznej zestawu wyników.
- Brak danych lub schematu w tabelach, w których Wygenerowano buforowany zestaw wyników.

Uruchom to polecenie, aby sprawdzić, czy zapytanie zostało wykonane z trafieniem lub chybień pamięci podręcznej wyników. W przypadku trafienia pamięci podręcznej result_cache_hit będzie zwracać 1.

```sql
SELECT request_id, command, result_cache_hit FROM sys.pdw_exec_requests 
WHERE request_id = <'Your_Query_Request_ID'>
```

## <a name="manage-cached-results"></a>Zarządzanie wynikami buforowanymi 

Maksymalny rozmiar pamięci podręcznej zestawu wyników wynosi 1 TB na bazę danych.  Buforowane wyniki zostaną automatycznie unieważnione po zmianie źródłowych danych zapytania.  

Wykluczanie pamięci podręcznej jest zarządzane przez Azure SQL Data Warehouse automatycznie zgodnie z tym harmonogramem: 
- Co 48 godzin, jeśli zestaw wyników nie został użyty lub został unieważniony. 
- Gdy pamięć podręczna zestawu wyników zbliża się do maksymalnego rozmiaru.

Użytkownicy mogą ręcznie opróżnić całą pamięć podręczną zestawu wyników, korzystając z jednej z następujących opcji: 
- Wyłącz funkcję pamięci podręcznej zestawu wyników dla bazy danych 
- Uruchom polecenie DBCC DROPRESULTSETCACHE podczas połączenia z bazą danych

Wstrzymywanie bazy danych nie będzie pustym zestawem wyników w pamięci podręcznej.  

## <a name="next-steps"></a>Następne kroki
Więcej porad programistycznych znajdziesz w artykule [Omówienie programowania w usłudze SQL Data Warehouse](sql-data-warehouse-overview-develop.md). 
