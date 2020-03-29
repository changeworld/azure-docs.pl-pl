---
title: Obsługiwane wersje — usługa Azure Database for PostgreSQL — single server
description: W tym artykule opisano obsługiwane wersje główne i pomocnicze postgres w usłudze Azure Database dla postgreSQL — single server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 12/03/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 61dd98028b7342290984615ea19b561b48aaeadb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792239"
---
# <a name="supported-postgresql-major-versions"></a>Obsługiwane główne wersje PostgreSQL
Firma Microsoft ma na celu obsługę n-2 wersji aparatu PostgreSQL w usłudze Azure Database for PostgreSQL — Single Server. Wersje będą bieżącą wersją główną na platformie Azure (n) i dwiema wcześniejszymi wersjami głównymi (-2).

Usługa Azure Database for PostgreSQL obsługuje obecnie następujące wersje główne:

## <a name="postgresql-version-11"></a>PostgreSQL wersja 11
Bieżąca wersja pomocnicza to 11.5. Zapoznaj się z [dokumentacją PostgreSQL,](https://www.postgresql.org/docs/11/static/release-11-5.html) aby dowiedzieć się więcej na temat ulepszeń i poprawek w tej wersji pomocniczej.

## <a name="postgresql-version-10"></a>PostgreSQL wersja 10
Bieżąca wersja pomocnicza to 10.10. Zapoznaj się z [dokumentacją PostgreSQL,](https://www.postgresql.org/docs/10/static/release-10-10.html) aby dowiedzieć się więcej na temat ulepszeń i poprawek w tej wersji pomocniczej.

## <a name="postgresql-version-96"></a>PostgreSQL wersja 9.6
Aktualna wersja pomocnicza to 9.6.15. Zapoznaj się z [dokumentacją PostgreSQL,](https://www.postgresql.org/docs/9.6/static/release-9-6-15.html) aby dowiedzieć się więcej na temat ulepszeń i poprawek w tej wersji pomocniczej.

## <a name="postgresql-version-95"></a>PostgreSQL wersja 9.5
Obecna wersja pomocnicza to 9.5.19. Zapoznaj się z [dokumentacją PostgreSQL,](https://www.postgresql.org/docs/9.5/static/release-9-5-19.html) aby dowiedzieć się więcej o ulepszeniach i poprawkach w tej wersji pomocniczej.

## <a name="managing-upgrades"></a>Zarządzanie uaktualnieniami
Projekt PostgreSQL regularnie wydaje drobne wersje, aby naprawić zgłoszone błędy. Usługa Azure Database for PostgreSQL automatycznie poprawia serwery za pomocą wersji pomocniczych podczas miesięcznych wdrożeń usługi. 

Automatyczne uaktualnianie wersji głównej nie jest obsługiwane. Na przykład nie jest możliwe automatyczne uaktualnienie bazy danych PostgreSQL 9.5 do wersji 9.6. Aby przeprowadzić uaktualnienie do następnej wersji głównej, wykonaj [zrzut bazy danych i przywróć go](./howto-migrate-using-dump-and-restore.md) na serwer, który został utworzony przy użyciu nowej wersji aparatu.

### <a name="version-syntax"></a>Składnia wersji
Przed PostgreSQL w wersji [10, PostgreSQL zasady przechowywania wersji](https://www.postgresql.org/support/versioning/) za główne uaktualnienie _wersji_ do zwiększenia w pierwszym _lub_ drugim numerze. Na przykład 9.5 do 9.6 został uznany za aktualizację wersji _głównej._ W wersji 10 tylko zmiana pierwszego numeru jest uważana za aktualizację wersji głównej. Na przykład 10.0 do 10.1 jest _uaktualnienie wersji pomocniczej._ Wersja 10 do 11 jest _główną_ aktualizacją wersji.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać informacje na temat obsługiwanych rozszerzeń PostgreSQL, zobacz [dokument rozszerzeń](concepts-extensions.md).
