---
title: 'Szyfrowanie bazy danych magazynowanych: bazy danych Azure rozwiązania Cosmos | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak bazy danych rozwiązania Cosmos Azure udostępnia domyślne szyfrowanie wszystkich danych.
services: cosmos-db
author: voellm
manager: kfile
documentationcenter: ''
ms.assetid: 99725c52-d7ca-4bfa-888b-19b1569754d3
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2017
ms.author: voellm
ms.openlocfilehash: 831fa2b6b9afb39bff715ef54cc41e11ee903e50
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="azure-cosmos-db-database-encryption-at-rest"></a>Azure DB rozwiązania Cosmos szyfrowania bazy danych w stanie spoczynku

Szyfrowanie rest jest frazę, które często odwołuje się do szyfrowania danych na urządzeniach nieulotnej pamięci masowej, takich jak półprzewodnikowe dyski (SSD) i dysków twardych (HDD). Rozwiązania cosmos bazy danych są przechowywane jego głównej bazy danych na dyskach SSD. Jego załączniki nośników i kopii zapasowych są przechowywane w magazynie obiektów Blob platformy Azure, które zazwyczaj została skopiowana przez użycie dysków twardych. Wraz z wydaniem szyfrowanie przechowywanych dla rozwiązania Cosmos DB wszystkich baz danych, załączniki nośników i kopii zapasowych są szyfrowane. Dane są teraz szyfrowane podczas przesyłania (za pośrednictwem sieci) i w stanie spoczynku (nieulotnej pamięci masowej), umożliwiając szyfrowanie na całej trasie.

Jak usługa PaaS, rozwiązania Cosmos bazy danych jest bardzo łatwa w użyciu. Ponieważ wszystkie dane użytkownika przechowywane w bazie danych rozwiązania Cosmos są szyfrowane podczas przechowywania i podczas transportu, nie trzeba podejmować żadnych działań. Jest to można umieścić w inny sposób, że szyfrowanie rest jest "na" domyślnie. Nie istnieją żadne formanty, aby go włączyć lub wyłączyć. Firma Microsoft udostępnia tę funkcję, podczas możemy w dalszym ciągu będą spełniać naszych [SLA dostępności i wydajności](https://azure.microsoft.com/support/legal/sla/cosmos-db).

## <a name="implementation-of-encryption-at-rest-for-azure-cosmos-db"></a>Implementacja szyfrowanie przechowywanych dla bazy danych Azure rozwiązania Cosmos

Szyfrowanie magazynowane jest implementowane za pomocą wielu technologii zabezpieczeń, takich jak systemy bezpiecznego magazynu kluczy, zaszyfrowanych sieci i interfejsów API usług kryptograficznych. Systemy odszyfrować i przetwarza dane mają do komunikowania się z systemów, które zarządzania kluczami. Na diagramie przedstawiono sposób przechowywania zaszyfrowanych danych i zarządzanie kluczami jest oddzielona. 

![Diagram projektu](./media/database-encryption-at-rest/design-diagram.png)

Podstawowy przepływ żądania użytkownika jest następujący:
- Konto bazy danych użytkownika jest gotów i magazynu kluczy są pobierane przy użyciu żądania do dostawcy zasobów usługi zarządzania.
- Użytkownik tworzy połączenie za pośrednictwem protokołu HTTPS/secure transportu DB rozwiązania Cosmos. (Zestawy SDK abstrakcyjnej szczegóły).
- Użytkownik wysyła dokument JSON mają być przechowywane w utworzonej wcześniej bezpiecznego połączenia.
- Dokument JSON jest indeksowany, chyba że użytkownik wyłączył indeksowania.
- Zarówno dokumentu i pod indeksem dane JSON są zapisywane do bezpiecznego magazynu.
- Okresowo dane są odczytywane bezpiecznego magazynu i kopii zapasowej w magazynie obiektów Blob Azure zaszyfrowane.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="q-how-much-more-does-azure-storage-cost-if-storage-service-encryption-is-enabled"></a>Pytanie: jak bardziej usługi Azure Storage koszt Jeśli włączono szyfrowanie usługi Magazyn?
Odpowiedź: nie istnieje bez dodatkowych kosztów.

### <a name="q-who-manages-the-encryption-keys"></a>Pytanie: zarządzająca klucze szyfrowania?
A: klucze są zarządzane przez firmę Microsoft.

### <a name="q-how-often-are-encryption-keys-rotated"></a>Pytanie: jak często są obracane klucze szyfrowania?
Odpowiedź: Microsoft ma zestaw wewnętrzne wytyczne dotyczące obrotu klucza szyfrowania, następującego rozwiązania Cosmos bazy danych. Szczegółowe wytyczne nie są publikowane. Publikowanie Microsoft [Security Development Lifecycle (SDL)](https://www.microsoft.com/sdl/default.aspx), który jest traktowany jako podzbiór wewnętrzny wskazówki i ma przydatne wskazówki dla deweloperów.

### <a name="q-can-i-use-my-own-encryption-keys"></a>Pytanie: czy można użyć własnych kluczy szyfrowania?
A: rozwiązania cosmos bazy danych jest usługą PaaS i firma Microsoft pracy niezbędne do zachowania usługi łatwy w użyciu. Zauważyliśmy, że to pytanie jest często zadawane jako serwer proxy pytanie najlepiej spełniającej wymagania zgodności, takie jak PCI-DSS. W ramach tworzenia tej funkcji firma Microsoft we współpracy z audytorów zgodności, aby upewnić się, że klienci korzystający z rozwiązania Cosmos DB wymagań ich bez konieczności zarządzania kluczami samodzielnie.

### <a name="q-what-regions-have-encryption-turned-on"></a>Pytanie: jakie regiony z włączonym szyfrowaniem?
Odpowiedź: rozwiązania Cosmos Azure DB regiony z szyfrowania włączona dla wszystkich danych użytkownika.

### <a name="q-does-encryption-affect-the-performance-latency-and-throughput-slas"></a>Pytanie: szyfrowania wpływa na czas oczekiwania na wydajność i przepływność umów SLA?
Odpowiedź: nie istnieje wpływ lub zmiany na wydajność SLA teraz, gdy jest włączone szyfrowanie magazynowane wszystkich istniejących i nowych kont. Możesz przeczytać dodatkowe na [umowy SLA dla rozwiązania Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db) stronę, aby zobaczyć najnowsze gwarancji.

### <a name="q-does-the-local-emulator-support-encryption-at-rest"></a>Pytanie: czy lokalnego emulatora obsługuje szyfrowanie magazynowane?
A: emulator jest to samodzielne narzędzie i testowania i nie używa usługi zarządzania kluczami, które używa zarządzanych usług DB rozwiązania Cosmos. Jest nasze zalecenie, aby włączyć funkcję BitLocker na dyskach, w której są przechowywane emulatora poufnych danych testowych. [Emulatora obsługuje zmiana domyślnego katalogu danych](local-emulator.md) oraz przy użyciu dobrze znanej lokalizacji.

## <a name="next-steps"></a>Kolejne kroki

Omówienie najnowsze ulepszenia i rozwiązania Cosmos DB zabezpieczeń, zobacz [zabezpieczeń bazy danych Azure DB rozwiązania Cosmos](database-security.md).
Aby uzyskać więcej informacji o certyfikatach firmy Microsoft, zobacz [Centrum zaufania Azure](https://azure.microsoft.com/support/trust-center/).
