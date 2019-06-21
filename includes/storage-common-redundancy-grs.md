---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/26/2018
ms.author: jeking
ms.custom: include file
ms.openlocfilehash: efa593d0ff0043d81574b67192deed30933e1e40
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183842"
---
Magazyn geograficznie nadmiarowy (GRS) pozwala uzyskać co najmniej 99,99999999999999% (16 9) trwałości obiektów w danym roku przez replikowanie danych do regionu pomocniczego, który znajduje się setki odległości od regionu podstawowego. Jeśli Twoje konto magazynu ma GRS włączone, dane są trwałe nawet w przypadku pełną, regionalnej niedostępności lub awarii, w którym regionie podstawowym nie jest możliwe do odzyskania.

Jeśli postanowisz magazynu grs, masz dwie powiązane opcje do wyboru:

* GRS replikuje dane do innego centrum danych w regionie pomocniczym, ale dane są dostępne do odczytu tylko jeśli firma Microsoft zainicjuje tryb failover z serwera podstawowego do regionu pomocniczego. 
* Dostęp do odczytu magazynu geograficznie nadmiarowego (RA-GRS) jest oparty na GRS. RA-GRS replikuje dane do innego centrum danych w regionie pomocniczym, a także zapewnia możliwość odczytu z regionu pomocniczego. W RA-GRS mogą odczytywać z regionu pomocniczego, niezależnie od tego, czy firma Microsoft zainicjuje tryb failover z serwera podstawowego do regionu pomocniczego. 

Dla konta magazynu przy użyciu GRS lub RA-GRS jest włączone wszystkie dane są najpierw replikowane z magazynem lokalnie nadmiarowym (LRS). Aktualizacja została zatwierdzona do lokalizacji głównej i replikowane z wykorzystaniem LRS. Aktualizacja jest następnie replikowane asynchronicznie w regionie pomocniczym, przy użyciu GRS. Dane są zapisywane do lokalizacji dodatkowej, jest również replikowana w tej lokalizacji przy użyciu magazynu LRS. 

Region podstawowy i pomocniczy zarządzania replik w oddzielnych domenach błędów i uaktualnień w jednostce skalowania magazynu. Jednostki skali magazynu jest jednostką podstawowej replikacji w centrum danych. Replikacja na tym poziomie jest świadczona przez Magazyn LRS; Aby uzyskać więcej informacji, zobacz [magazyn lokalnie nadmiarowy (LRS): Low-cost data redundancy for Azure Storage](../articles/storage/common/storage-redundancy-lrs.md) (Magazyn lokalnie nadmiarowy: nadmiarowość danych o niskich kosztach dla usługi Azure Storage).

Podczas wybierania opcji replikacji, należy pamiętać o następujących punktów:

* Magazyn strefowo nadmiarowy (ZRS) oferuje o wysokiej dostępności przy użyciu replikacji synchronicznej i może być lepszym rozwiązaniem w przypadku niektórych scenariuszy niż GRS lub RA-GRS. Aby uzyskać więcej informacji na temat magazynu ZRS, zobacz [ZRS](../articles/storage/common/storage-redundancy-zrs.md).
* Replikacja asynchroniczna obejmuje opóźnieniem od czasu, które dane są zapisywane do regionu podstawowego, gdy są replikowane do regionu pomocniczego. W przypadku regionalnej awarii usługa zmiany, które nie zostały jeszcze zreplikowane do regionu pomocniczego mogą zostać utracone, jeśli nie można odzyskać dane z regionu podstawowego.
* W przypadku magazynu GRS replika nie jest dostępna dla odczytu lub zapisu, chyba że firma Microsoft zainicjuje tryb failover do regionu pomocniczego. W przypadku pracy w trybie failover będzie zapoznaniu się i do zapisu danych po pracy w trybie failover zostało ukończone. Aby uzyskać więcej informacji, zobacz [wskazówki dotyczące odzyskiwania po awarii](../articles/storage/common/storage-disaster-recovery-guidance.md).
* Jeśli Twoja aplikacja potrzebuje do odczytu z regionu pomocniczego, należy włączyć RA-GRS.