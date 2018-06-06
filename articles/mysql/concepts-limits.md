---
title: Ograniczenia dotyczące bazy danych platformy Azure dla programu MySQL
description: W tym artykule opisano ograniczenia w bazie danych Azure dla programu MySQL, takie jak liczba połączeń i opcje aparatu magazynu.
services: mysql
author: kamathsun
ms.author: sukamat
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 06/04/2018
ms.openlocfilehash: 7d05d3aa94fffb13a96fc9074d55d8fa67cadb04
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763123"
---
# <a name="limitations-in-azure-database-for-mysql"></a>Ograniczenia dotyczące bazy danych platformy Azure dla programu MySQL
W poniższych sekcjach opisano pojemności, magazynu aparat obsługę, uprawnień, obsługi instrukcji manipulacji danych oraz limity funkcjonalności usługi bazy danych. Zobacz też [ogólne ograniczenia](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.6/en/limits.html) mające zastosowanie do aparatu bazy danych MySQL.

## <a name="maximum-connections"></a>Maksymalna liczba połączeń
Maksymalna liczba połączeń na warstwa cenowa i vCores są następujące: 

|**Warstwa cenowa**|**vCore(s)**| **Maksymalna liczba połączeń**|
|---|---|---|
|Podstawowa| 1| 50|
|Podstawowa| 2| 100|
|Ogólne zastosowanie| 2| 300|
|Ogólne zastosowanie| 4| 625|
|Ogólne zastosowanie| 8| 1250|
|Ogólne zastosowanie| 16| 2500|
|Ogólne zastosowanie| 32| 5000|
|Pamięć| 2| 600|
|Pamięć| 4| 1250|
|Pamięć| 8| 2500|
|Pamięć| 16| 5000|

Gdy połączenia przekracza limit, może zostać wyświetlony następujący błąd:
> Błąd 1040 (08004): Zbyt wiele połączeń

## <a name="storage-engine-support"></a>Obsługa aparatu magazynu

### <a name="supported"></a>Obsługiwane
- [InnoDB](https://dev.mysql.com/doc/refman/5.7/en/innodb-introduction.html)
- [MEMORY](https://dev.mysql.com/doc/refman/5.7/en/memory-storage-engine.html)

### <a name="unsupported"></a>Nieobsługiwane
- [MyISAM](https://dev.mysql.com/doc/refman/5.7/en/myisam-storage-engine.html)
- [NALEŻĄCY](https://dev.mysql.com/doc/refman/5.7/en/blackhole-storage-engine.html)
- [ARCHIWUM](https://dev.mysql.com/doc/refman/5.7/en/archive-storage-engine.html)
- [FEDERACYJNA](https://dev.mysql.com/doc/refman/5.7/en/federated-storage-engine.html)

## <a name="privilege-support"></a>Obsługa uprawnień

### <a name="unsupported"></a>Nieobsługiwane
- Administrator roli: wiele parametrów serwera i ustawienia mogą przypadkowo obniżają wydajność serwerów lub odwrócić ACID właściwości systemu DBMS. Tak, aby zachować integralności usługi i umowy SLA na poziomie produktu, tego usługa nie uwidacznia roli Administrator. Domyślne konto użytkownika, który jest tworzony podczas tworzenia nowego wystąpienia bazy danych, umożliwia użytkownikowi wykonują większość instrukcji DDL i DML w wystąpieniu bazy danych zarządzanych. 
- Uprawnienie administratora: Podobnie [SUPER uprawnień](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_super) również jest ograniczony.

## <a name="data-manipulation-statement-support"></a>Obsługa instrukcji manipulacji danych

### <a name="supported"></a>Obsługiwane
- PLIKWEJŚCIOWY danych obciążenia — obsługiwane, ale należy określić parametr [LOCAL], który jest przekierowywany do ścieżki UNC (zainstalowane za pomocą XSMB magazynu Azure).

### <a name="unsupported"></a>Nieobsługiwane
- WYBIERZ... DO PLIKWYJŚCIOWY

## <a name="functional-limitations"></a>Ograniczenia funkcjonalności

### <a name="scale-operations"></a>Operacje skalowania
- Dynamiczne skalowanie serwerów między warstwa cenowa nie jest obecnie obsługiwane. Oznacza to przełączaniu między Basic, ogólnego przeznaczenia i zoptymalizowanych pod kątem pamięci, warstw cenowych.
- Zmniejszenie rozmiaru magazynu serwera nie jest obsługiwana.

### <a name="server-version-upgrades"></a>Uaktualniania wersji
- Automatycznej migracji między wersjami aparatu bazy danych głównych nie jest obecnie obsługiwane.

### <a name="point-in-time-restore"></a>Przywracanie do punktu w czasie
- Przywracanie do warstwy z innej usługi i/lub jednostki obliczeniowe i rozmiaru magazynu nie jest dozwolone.
- Przywracanie usuniętych serwera nie jest obsługiwane.

### <a name="subscription-management"></a>Zarządzanie subskrypcjami
- Dynamicznie przenoszenie serwerów wstępnie utworzone w subskrypcji i grupy zasobów nie jest obecnie obsługiwane.

## <a name="current-known-issues"></a>Obecnie znane problemy
- Wystąpienie serwera MySQL Wyświetla wersję niewłaściwy serwer, po nawiązaniu połączenia. Aby uzyskać z właściwym serwerem versioning wystąpienia, należy użyć wybierz version(); polecenie w wierszu polecenia programu MySQL.

## <a name="next-steps"></a>Kolejne kroki
- [Co to jest dostępne w poszczególnych warstwach usług](concepts-pricing-tiers.md)
- [Obsługiwane wersje bazy danych MySQL](concepts-supported-versions.md)
