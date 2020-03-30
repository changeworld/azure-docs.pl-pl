---
title: Szyfrowanie w stanie spoczynku w usłudze Azure Cosmos DB
description: Dowiedz się, jak usługa Azure Cosmos DB zapewnia szyfrowanie danych w spoczynku i jak są implementowane.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.custom: seodec18
ms.openlocfilehash: db20388b5277e000ffe7055e9840742d6af7788e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062596"
---
# <a name="data-encryption-in-azure-cosmos-db"></a>Szyfrowanie danych w usłudze Azure Cosmos DB 

Szyfrowanie w spoczynku to fraza, która często odnosi się do szyfrowania danych na nieulotnych urządzeniach pamięci masowej, takich jak dyski PÓŁPRZEWODNIKOWE (SSD) i dyski twarde (HDD). Usługa Cosmos DB przechowuje swoje podstawowe bazy danych na ssd. Jego załączniki multimediów i kopie zapasowe są przechowywane w magazynie obiektów Blob platformy Azure, który jest zazwyczaj wspierany przez dyski twarde. Wraz z wydaniem szyfrowania w spoczynku usługi Cosmos DB wszystkie bazy danych, załączniki multimediów i kopie zapasowe są szyfrowane. Twoje dane są teraz szyfrowane podczas przesyłania (przez sieć) i w spoczynku (magazyn nieulotny), co zapewnia szyfrowanie end-to-end.

Jako usługa PaaS, Cosmos DB jest bardzo łatwy w użyciu. Ponieważ wszystkie dane użytkownika przechowywane w usłudze Cosmos DB są szyfrowane w spoczynku i w transporcie, nie trzeba podejmować żadnych działań. Innym sposobem, aby umieścić to jest to, że szyfrowanie w spoczynku jest "na" domyślnie. Nie ma żadnych formantów, aby go wyłączyć lub włączyć. Usługa Azure Cosmos DB używa szyfrowania AES-256 we wszystkich regionach, w których konto jest uruchomione. Udostępniamy tę funkcję, podczas gdy nadal spełniamy nasze [łasce dostępności i wydajności.](https://azure.microsoft.com/support/legal/sla/cosmos-db)

## <a name="implementation-of-encryption-at-rest-for-azure-cosmos-db"></a>Implementacja szyfrowania w spoczynku dla usługi Azure Cosmos DB

Szyfrowanie w spoczynku jest implementowane przy użyciu wielu technologii zabezpieczeń, w tym bezpiecznych systemów przechowywania kluczy, zaszyfrowanych sieci i kryptograficznych interfejsów API. Systemy, które odszyfrowują i przetwarzają dane, muszą komunikować się z systemami, które zarządzają kluczami. Diagram pokazuje, jak magazyn zaszyfrowanych danych i zarządzanie kluczami jest oddzielona. 

![Diagram projektowy](./media/database-encryption-at-rest/design-diagram.png)

Podstawowy przepływ żądania użytkownika jest następujący:
- Konto bazy danych użytkownika jest gotowe, a klucze magazynu są pobierane za pośrednictwem żądania do dostawcy zasobów usługi zarządzania.
- Użytkownik tworzy połączenie z usługą Cosmos DB za pośrednictwem protokołu HTTPS/secure transport. (SDKs wyodrębnić szczegóły.)
- Użytkownik wysyła dokument JSON do przechowywania za poprzednie utworzone bezpieczne połączenie.
- Dokument JSON jest indeksowany, chyba że użytkownik wyłączył indeksowanie.
- Zarówno dokument JSON, jak i dane indeksu są zapisywane w celu bezpiecznego magazynu.
- Okresowo dane są odczytywane z bezpiecznego magazynu i archiwizowane w magazynie zaszyfrowanych obiektów blob platformy Azure.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="q-how-much-more-does-azure-storage-cost-if-storage-service-encryption-is-enabled"></a>Pyt.: Ile więcej kosztuje usługa Azure Storage, jeśli jest włączone szyfrowanie usługi storage?
O: Nie ma żadnych dodatkowych kosztów.

### <a name="q-who-manages-the-encryption-keys"></a>P: Kto zarządza kluczami szyfrowania?
Odp.: Klucze są zarządzane przez firmę Microsoft.

### <a name="q-how-often-are-encryption-keys-rotated"></a>P: Jak często obracane są klucze szyfrowania?
Odp.: Firma Microsoft ma zestaw wewnętrznych wytycznych dotyczących rotacji kluczy szyfrowania, które jest zgodne z programem Cosmos DB. Szczegółowe wytyczne nie są publikowane. Firma Microsoft publikuje [cykl życia rozwoju zabezpieczeń (SDL),](https://www.microsoft.com/sdl/default.aspx)który jest postrzegany jako podzbiór wewnętrznych wskazówek i ma przydatne najlepsze rozwiązania dla deweloperów.

### <a name="q-can-i-use-my-own-encryption-keys"></a>Pyt.: Czy mogę używać własnych kluczy szyfrowania?
Odp.: Tak, ta funkcja jest teraz dostępna dla nowych kont kosmosu i powinno to nastąpić w momencie tworzenia konta. Aby uzyskać więcej informacji, przejdź przez dokument [Klucze zarządzane](https://docs.microsoft.com/azure/cosmos-db/how-to-setup-cmk) przez klienta.

### <a name="q-what-regions-have-encryption-turned-on"></a>Pyt.: W jakich regionach jest włączone szyfrowanie?
Odp.: Wszystkie regiony usługi Azure Cosmos DB mają włączone szyfrowanie dla wszystkich danych użytkownika.

### <a name="q-does-encryption-affect-the-performance-latency-and-throughput-slas"></a>Pyt.: Czy szyfrowanie wpływa na opóźnienie wydajności i łasce przepływności?
Odp.: Nie ma wpływu ani zmian w slach wydajności teraz, gdy szyfrowanie w spoczynku jest włączone dla wszystkich istniejących i nowych kont. Możesz przeczytać więcej na stronie [Umowy SLA dla usługi Cosmos DB,](https://azure.microsoft.com/support/legal/sla/cosmos-db) aby wyświetlić najnowsze gwarancje.

### <a name="q-does-the-local-emulator-support-encryption-at-rest"></a>Pyt.: Czy lokalny emulator obsługuje szyfrowanie w spoczynku?
Odp.: Emulator jest autonomicznym narzędziem deweloperskim/testowym i nie używa usług zarządzania kluczami używanymi przez zarządzaną usługę Usługi Cosmos DB. Naszym zaleceniem jest włączenie funkcji BitLocker na dyskach, na których przechowujesz poufne dane testowe emulatora. [Emulator obsługuje zmianę domyślnego katalogu danych,](local-emulator.md) a także przy użyciu dobrze znanej lokalizacji.

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z omówieniem zabezpieczeń usługi Cosmos DB i najnowszymi ulepszeniami, zobacz [Zabezpieczenia bazy danych usługi Azure Cosmos](database-security.md).
Aby uzyskać więcej informacji na temat certyfikacji firmy [Microsoft,](https://azure.microsoft.com/support/trust-center/)zobacz Centrum zaufania platformy Azure .
