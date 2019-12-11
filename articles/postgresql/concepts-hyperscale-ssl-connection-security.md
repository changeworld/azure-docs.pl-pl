---
title: SSL-Citus) — Azure Database for PostgreSQL
description: Instrukcje i informacje dotyczące konfigurowania Azure Database for PostgreSQL-Citus i skojarzonych aplikacji, aby prawidłowo używać połączeń SSL.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/17/2019
ms.openlocfilehash: 3e4ef5d2d6db3a3d4f8923f47079f2484639a751
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74973989"
---
# <a name="configure-ssl-in-azure-database-for-postgresql---hyperscale-citus"></a>Konfigurowanie protokołu SSL w Azure Database for PostgreSQL-Citus
Połączenia aplikacji klienckich z węzłem koordynatora Citus () wymagają SSL (SSL). Wymuszanie połączeń SSL między serwerem bazy danych i aplikacjami klienckimi pomaga chronić przed atakami typu man-in-the-Middle przez szyfrowanie strumienia danych między serwerem a aplikacją.

## <a name="enforcing-ssl-connections"></a>Wymuszanie połączeń SSL
W przypadku wszystkich serwerów Azure Database for PostgreSQL, które są obsługiwane za pośrednictwem Azure Portal, wymuszanie połączeń SSL jest domyślnie włączone. 

Analogicznie, parametry połączenia, które są wstępnie zdefiniowane w ustawieniach "parametry połączenia" w ramach serwera w Azure Portal zawierają wymagane parametry dla wspólnych języków do łączenia się z serwerem bazy danych przy użyciu protokołu SSL. Parametr SSL zależy od łącznika, na przykład "SSL = true" lub "sslmode = wymagaj" lub "sslmode = Required" i innych wariantów.

## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>Upewnij się, że aplikacja lub struktura obsługuje połączenia SSL
Niektóre struktury aplikacji korzystające z PostgreSQL dla usług baz danych nie domyślnie włączają protokół SSL podczas instalacji. Jeśli serwer PostgreSQL wymusza połączenia SSL, ale aplikacja nie jest skonfigurowana dla protokołu SSL, nawiązanie połączenia z serwerem bazy danych przez aplikację może zakończyć się niepowodzeniem. Zapoznaj się z dokumentacją aplikacji, aby dowiedzieć się, jak włączyć połączenia SSL.

## <a name="applications-that-require-certificate-verification-for-ssl-connectivity"></a>Aplikacje, które wymagają weryfikacji certyfikatu na potrzeby łączności SSL
W niektórych przypadkach aplikacje wymagają lokalnego pliku certyfikatu wygenerowanego na podstawie pliku certyfikatu zaufanego urzędu certyfikacji (. cer), aby bezpiecznie nawiązać połączenie. Certyfikat do połączenia z Azure Database for PostgreSQL-Citus) znajduje się w https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem. Pobierz plik certyfikatu i Zapisz go w preferowanej lokalizacji.

### <a name="connect-using-psql"></a>Nawiązywanie połączenia przy użyciu PSQL
W poniższym przykładzie pokazano, jak nawiązać połączenie z węzłem koordynatora Citus (PSQL) za pomocą narzędzia wiersza polecenia. Użyj ustawienia parametrów połączenia `sslmode=verify-full`, aby wymusić weryfikację certyfikatu SSL. Przekaż ścieżkę pliku certyfikatu lokalnego do parametru `sslrootcert`.

Poniżej znajduje się przykład parametrów połączenia PSQL:
```
psql "sslmode=verify-full sslrootcert=DigiCertGlobalRootCA.crt.pem host=mydemoserver.postgres.database.azure.com dbname=citus user=citus password=your_pass"
```
> [!TIP]
> Upewnij się, że wartość przeniesiona do `sslrootcert` jest zgodna ze ścieżką pliku dla zapisywanych certyfikatów.

## <a name="next-steps"></a>Następne kroki
Zwiększ bezpieczeństwo, korzystając z [reguł zapory w Azure Database for PostgreSQL-Citus](concepts-hyperscale-firewall-rules.md).
