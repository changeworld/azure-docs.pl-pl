---
title: Obsługiwane wersje w Azure Database for PostgreSQL — pojedynczy serwer
description: W tym artykule opisano obsługiwane wersje Azure Database for PostgreSQL-jednego serwera.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/06/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: b77fd082be43b8cbdedf7cbe5875a8931eb0474a
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68837896"
---
# <a name="supported-postgresql-database-versions"></a>Obsługiwane wersje bazy danych PostgreSQL
Firma Microsoft dąży do obsługi wersji n-2 aparatu PostgreSQL w Azure Database for PostgreSQL-pojedynczym serwerze. Wersje byłyby w bieżącej wersji głównej na platformie Azure (n) i dwóch wcześniejszych wersjach głównych (-2).

Azure Database for PostgreSQL obecnie obsługuje następujące wersje główne:

## <a name="postgresql-version-11"></a>PostgreSQL, wersja 11
Bieżąca wersja pomocnicza to 11,4. Zapoznaj się z [dokumentacją PostgreSQL](https://www.postgresql.org/docs/11/static/release-11-4.html) , aby dowiedzieć się więcej na temat ulepszeń i poprawek w tej wersji pomocniczej.

## <a name="postgresql-version-10"></a>PostgreSQL wersja 10
Bieżąca wersja pomocnicza to 10,9. Zapoznaj się z [dokumentacją PostgreSQL](https://www.postgresql.org/docs/10/static/release-10-9.html) , aby dowiedzieć się więcej na temat ulepszeń i poprawek w tej wersji pomocniczej.

## <a name="postgresql-version-96"></a>PostgreSQL w wersji 9,6
Bieżąca wersja pomocnicza to 9.6.14. Zapoznaj się z [dokumentacją PostgreSQL](https://www.postgresql.org/docs/9.6/static/release-9-6-14.html) , aby dowiedzieć się więcej na temat ulepszeń i poprawek w tej wersji pomocniczej.

## <a name="postgresql-version-95"></a>PostgreSQL w wersji 9,5
Bieżąca wersja pomocnicza to 9.5.18. Zapoznaj się z [dokumentacją PostgreSQL](https://www.postgresql.org/docs/9.5/static/release-9-5-18.html) , aby dowiedzieć się więcej na temat ulepszeń i poprawek w tej wersji pomocniczej.

## <a name="managing-upgrades"></a>Zarządzanie uaktualnieniami
Azure Database for PostgreSQL automatycznie zarządza uaktualnieniami wersji pomocniczych. 

Automatyczne uaktualnianie wersji głównej nie jest obsługiwane. Na przykład nie istnieje automatyczne uaktualnienie z PostgreSQL 9,5 do PostgreSQL 9,6. Aby uaktualnić do następnej wersji głównej, Utwórz [zrzut bazy danych i Przywróć](./howto-migrate-using-dump-and-restore.md) serwer, który został utworzony przy użyciu nowej wersji aparatu.

### <a name="version-syntax"></a>Składnia wersji
Przed PostgreSQL w wersji 10 [zasady dotyczące wersji PostgreSQL](https://www.postgresql.org/support/versioning/) uznawane za uaktualnienie _wersji głównej_ o zwiększenie liczby pierwszej _lub_ drugiej. Na przykład 9,5 do 9,6 zostało uznane za uaktualnienie wersji _głównej_ . Począwszy od wersji 10, tylko zmiana pierwszego numeru jest uznawana za uaktualnienie wersji głównej. Na przykład 10,0 do 10,1 jest uaktualnieniem wersji pomocniczej. Wersja 10 do 11 to uaktualnienie wersji _głównej_ .

## <a name="next-steps"></a>Kolejne kroki
Informacje na temat obsługiwanych rozszerzeń PostgreSQL można znaleźć [w dokumencie rozszerzenia](concepts-extensions.md).
