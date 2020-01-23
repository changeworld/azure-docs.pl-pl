---
title: Szyfrowanie w spoczynku w usłudze Azure Cosmos DB
description: Dowiedz się, jak usługa Azure Cosmos DB udostępnia szyfrowanie nieużywanych danych i jak jest zaimplementowana.
author: markjbrown
ms.author: sngun
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.custom: seodec18
ms.openlocfilehash: 366a8cc2d8b08c9508053eaeb8bf70622fd870cf
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76548105"
---
# <a name="data-encryption-in-azure-cosmos-db"></a>Szyfrowanie danych w Azure Cosmos DB 

Szyfrowanie w spoczynku jest frazę, która często odnosi się do szyfrowania danych na urządzeniach nieulotnej pamięci masowej, takich jak dyski półprzewodnikowe (SSD) i dysków twardych (HDD). Usługa cosmos DB przechowuje jego podstawowych baz danych na dyskach SSD. Jego załączniki nośników i kopii zapasowych są przechowywane w usłudze Azure Blob storage, zazwyczaj kopia zapasowa jest tworzona dysków twardych. Wraz z wydaniem szyfrowanie danych magazynowanych na potrzeby usługi Cosmos DB wszystkie bazy danych, załączniki nośników i kopii zapasowych są szyfrowane. Dane są teraz szyfrowane podczas przesyłania (za pośrednictwem sieci) i magazynowanych (składnik pamięci nieulotnej), umożliwiając end-to-end szyfrowania.

Ponieważ usługa PaaS, usługa Cosmos DB jest bardzo łatwa w użyciu. Ponieważ wszystkie dane użytkownika przechowywane w usłudze Cosmos DB są szyfrowane podczas przechowywania i podczas transportu, nie trzeba podejmować żadnych działań. Należy umieścić następujące polecenia w inny sposób jest to, że szyfrowanie danych magazynowanych jest "włączone" domyślnie. Nie istnieją żadne formantów, aby go włączyć lub wyłączyć. Azure Cosmos DB używa szyfrowania AES-256 we wszystkich regionach, w których konto jest uruchomione. Firma Microsoft zapewnia tę funkcję, podczas gdy my będziemy w celu spełnienia naszych [dostępność i wydajność umowy SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db).

## <a name="implementation-of-encryption-at-rest-for-azure-cosmos-db"></a>Implementacja szyfrowania danych magazynowanych na potrzeby usługi Azure Cosmos DB

Szyfrowanie w spoczynku jest implementowany przy użyciu wielu technologii zabezpieczeń, w tym systemów bezpiecznego magazynu kluczy, sieci szyfrowane i interfejsów API usług kryptograficznych. Systemy, odszyfrowywania i przetwarzania danych, które mają do komunikacji z systemami zarządzania kluczami. Na diagramie przedstawiono, jak przechowywanie zaszyfrowanych danych i zarządzanie kluczami jest oddzielony. 

![Diagram projektu](./media/database-encryption-at-rest/design-diagram.png)

Podstawowy przepływ żądania użytkownika jest następująca:
- Konto bazy danych użytkownika staje się gotowy i klucze magazynu są pobierane za pośrednictwem żądania na potrzeby dostawcy zasobów usługi zarządzania.
- Użytkownik tworzy połączenie za pośrednictwem protokołu HTTPS/zabezpieczenia transportu usługi Cosmos DB. (Zestawy SDK abstrakcyjna szczegóły).
- Użytkownik wysyła dokument JSON, który ma być przechowywany w utworzonej wcześniej bezpiecznego połączenia.
- Dokument JSON jest indeksowana, chyba że użytkownik wyłączył indeksowania.
- Zarówno dokumentu i indeks dane JSON są zapisywane do bezpiecznego magazynu.
- Okresowo dane są odczytywane bezpiecznego magazynu i kopie zapasowe Store obiektu Blob zaszyfrowanego Azure.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="q-how-much-more-does-azure-storage-cost-if-storage-service-encryption-is-enabled"></a>P: jak dużo bardziej kosztuje się usługa Azure Storage, jeśli włączono szyfrowanie usługi Storage?
Odp.: nie ma żadnych dodatkowych kosztów.

### <a name="q-who-manages-the-encryption-keys"></a>P: kto zarządza kluczami szyfrowania?
Odp.: klucze są zarządzane przez firmę Microsoft.

### <a name="q-how-often-are-encryption-keys-rotated"></a>P: jak często są obracane klucze szyfrowania
Odp.: Firma Microsoft ma zbiór wewnętrzne wytyczne dla wymiany kluczy szyfrowania, który jest zgodny z usługi Cosmos DB. Konkretne wskazówki nie są publikowane. Publikowanie Microsoft [cykl projektowania zabezpieczeń (SDL)](https://www.microsoft.com/sdl/default.aspx), który jest widoczny jako część wewnętrzne wskazówki i zawiera przydatne wskazówki dla deweloperów.

### <a name="q-can-i-use-my-own-encryption-keys"></a>P: czy można użyć własnych kluczy szyfrowania?
Odp.: tak teraz ta funkcja jest dostępna dla nowych kont usługi Cosmos. należy to zrobić w czasie tworzenia konta. Aby uzyskać więcej informacji, przejdź do dokumentu z [kluczami zarządzanymi przez klienta](https://docs.microsoft.com/azure/cosmos-db/how-to-setup-cmk) .

### <a name="q-what-regions-have-encryption-turned-on"></a>Pytanie: jakie regiony z włączonym szyfrowaniem?
Odp.: wszystkie regiony usługi Azure Cosmos DB ma szyfrowania włączone dla wszystkich danych użytkownika.

### <a name="q-does-encryption-affect-the-performance-latency-and-throughput-slas"></a>P: szyfrowania wpływa na wydajność opóźnienia i przepływności umów SLA?
Odp.: nie ma wpływu na lub zmiany wydajności umowy SLA, skoro szyfrowanie w spoczynku jest włączona dla wszystkich istniejących i nowych kont. Więcej informacji o [umowa SLA dla usługi Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db) strony, aby zobaczyć najnowsze gwarancji.

### <a name="q-does-the-local-emulator-support-encryption-at-rest"></a>P: czy lokalnego emulatora obsługuje szyfrowanie w spoczynku?
Odp.: emulator to autonomiczne narzędzie tworzenia i testowania i nie używa usługi zarządzania kluczami, które używa zarządzanej usługi Cosmos DB. Nasze zalecenie, jest włączenie funkcji BitLocker na dyskach, gdzie będą przechowywane dane testowe poufnych emulatora. [Emulator obsługuje zmiana domyślnego katalogu danych](local-emulator.md) oraz przy użyciu dobrze znanej lokalizacji.

## <a name="next-steps"></a>Następne kroki

Omówienie zabezpieczeń Cosmos DB i najnowszych ulepszeń można znaleźć w temacie [zabezpieczenia usługi Azure Cosmos Database](database-security.md).
Aby uzyskać więcej informacji o certyfikaty firmy Microsoft, zobacz [Centrum zaufania systemu Azure](https://azure.microsoft.com/support/trust-center/).
