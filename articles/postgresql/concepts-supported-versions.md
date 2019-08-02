---
title: Obsługiwane wersje w Azure Database for PostgreSQL — pojedynczy serwer
description: W tym artykule opisano obsługiwane wersje Azure Database for PostgreSQL-jednego serwera.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/26/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: fae8dfb2e2cc532f4aed17f83f13bbee31d29113
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68551359"
---
# <a name="supported-postgresql-database-versions"></a>Obsługiwane wersje bazy danych PostgreSQL
Firma Microsoft dąży do obsługi wersji n-2 aparatu PostgreSQL w Azure Database for PostgreSQL-pojedynczym serwerze. Wersje byłyby w bieżącej wersji głównej na platformie Azure (n) i dwóch wcześniejszych wersjach głównych (-2).

Azure Database for PostgreSQL obecnie obsługuje następujące wersje:

## <a name="postgresql-version-112"></a>PostgreSQL w wersji 11,2
Zapoznaj się z [dokumentacją PostgreSQL](https://www.postgresql.org/docs/11/static/release-11-2.html) , aby dowiedzieć się więcej na temat ulepszeń i poprawek w tej wersji pomocniczej.

## <a name="postgresql-version-107"></a>PostgreSQL w wersji 10,7
Zapoznaj się z [dokumentacją PostgreSQL](https://www.postgresql.org/docs/10/static/release-10-7.html) , aby dowiedzieć się więcej na temat ulepszeń i poprawek w tej wersji pomocniczej.

## <a name="postgresql-version-9612"></a>PostgreSQL wersja 9.6.12
Zapoznaj się z [dokumentacją PostgreSQL](https://www.postgresql.org/docs/9.6/static/release-9-6-12.html) , aby dowiedzieć się więcej na temat ulepszeń i poprawek w tej wersji pomocniczej.

## <a name="postgresql-version-9516"></a>PostgreSQL wersja 9.5.16
Zapoznaj się z [dokumentacją PostgreSQL](https://www.postgresql.org/docs/9.5/static/release-9-5-16.html) , aby dowiedzieć się więcej na temat ulepszeń i poprawek w tej wersji pomocniczej.

## <a name="managing-updates-and-upgrades"></a>Zarządzanie aktualizacjami i uaktualnieniami
Azure Database for PostgreSQL automatycznie zarządza poprawkami wersji pomocniczych. Obecnie uaktualnienie wersji głównej nie jest obsługiwane. Na przykład uaktualnienie z PostgreSQL 9,5 do PostgreSQL 9,6 nie jest obsługiwane. Jeśli chcesz uaktualnić do następnej wersji głównej, Utwórz zrzut bazy danych [i Przywróć](./howto-migrate-using-dump-and-restore.md) go na serwerze, który został utworzony przy użyciu nowej wersji aparatu.

> Należy pamiętać, że przed PostgreSQL wersja 10 [zasady dotyczące wersji PostgreSQL](https://www.postgresql.org/support/versioning/) uznawane za uaktualnienie _wersji głównej_ o zwiększenie liczby pierwszej _lub_ drugiej (na przykład 9,5 do 9,6 zostało uznane za uaktualnienie wersji _głównej_ ).
> Począwszy od wersji 10, tylko zmiana pierwszego numeru jest uznawana za uaktualnienie wersji głównej (na przykład 10,0 do 10,1 jest uaktualnieniem wersji _pomocniczej_ , a 10 do 11 jest uaktualnieniem _głównym_ wersji).

## <a name="next-steps"></a>Następne kroki
Aby uzyskać informacje o obsłudze różnych rozszerzeń PostgreSQL, zobacz [rozszerzenia PostgreSQL](concepts-extensions.md).
