---
title: SSL — hiperskala (Citus) — usługa Azure Database for PostgreSQL
description: Instrukcje i informacje dotyczące konfigurowania usługi Azure Database dla postgreSql — hiperskali (Citus) i skojarzonych aplikacji do prawidłowego korzystania z połączeń SSL.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/17/2019
ms.openlocfilehash: 3e4ef5d2d6db3a3d4f8923f47079f2484639a751
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74973989"
---
# <a name="configure-ssl-in-azure-database-for-postgresql---hyperscale-citus"></a>Konfigurowanie ssl w bazie danych platformy Azure dla postgreSql — hiperskali (Citus)
Połączenia aplikacji klienckich z węzłem koordynatora hiperskali (Citus) wymagają warstwy SSL (Secure Sockets Layer). Wymuszanie połączeń SSL między serwerem bazy danych a aplikacjami klienckimi pomaga chronić przed atakami typu "man-in-the-middle", szyfrując strumień danych między serwerem a aplikacją.

## <a name="enforcing-ssl-connections"></a>Wymuszanie połączeń SSL
W przypadku wszystkich serwerów usługi Azure Database for PostgreSQL aprowidzonych za pośrednictwem witryny Azure portal wymuszanie połączeń SSL jest domyślnie włączone. 

Podobnie parametry połączenia, które są wstępnie zdefiniowane w ustawieniach "Parametry połączenia" w obszarze serwera w witrynie Azure portal zawierają parametry wymagane dla języków wspólnych, aby połączyć się z serwerem bazy danych przy użyciu SSL. Parametr SSL różni się w zależności od złącza, na przykład "ssl=true" lub "sslmode=require" lub "sslmode=required" i innych odmian.

## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>Upewnij się, że twoja aplikacja lub struktura obsługuje połączenia SSL
Niektóre struktury aplikacji, które używają PostgreSQL dla swoich usług bazy danych nie włączyć SSL domyślnie podczas instalacji. Jeśli serwer PostgreSQL wymusza połączenia SSL, ale aplikacja nie jest skonfigurowana dla SSL, aplikacja może nie łączyć się z serwerem bazy danych. Zapoznaj się z dokumentacją aplikacji, aby dowiedzieć się, jak włączyć połączenia SSL.

## <a name="applications-that-require-certificate-verification-for-ssl-connectivity"></a>Aplikacje wymagające weryfikacji certyfikatów dla łączności SSL
W niektórych przypadkach aplikacje wymagają bezpiecznego połączenia lokalnego pliku certyfikatu wygenerowanego z pliku certyfikatu zaufanego urzędu certyfikacji (cer). Certyfikat do łączenia się z usługą Azure Database for PostgreSQL — Hyperscale (Citus) znajduje się pod adresem https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem. Pobierz plik certyfikatu i zapisz go w preferowanej lokalizacji.

### <a name="connect-using-psql"></a>Połącz się za pomocą psql
W poniższym przykładzie pokazano, jak połączyć się z węzłem koordynatora hiperskali (Citus) za pomocą narzędzia wiersza polecenia psql. Użyj `sslmode=verify-full` ustawienia parametry połączenia, aby wymusić weryfikację certyfikatu SSL. Przekaż ścieżkę pliku certyfikatu lokalnego do parametru. `sslrootcert`

Poniżej znajduje się przykład ciągu połączenia psql:
```
psql "sslmode=verify-full sslrootcert=DigiCertGlobalRootCA.crt.pem host=mydemoserver.postgres.database.azure.com dbname=citus user=citus password=your_pass"
```
> [!TIP]
> Upewnij się, że `sslrootcert` wartość przekazana do zgodnej ze ścieżką pliku dla zapisanego certyfikatu.

## <a name="next-steps"></a>Następne kroki
Zwiększ bezpieczeństwo dzięki [regułom zapory w usłudze Azure Database for PostgreSQL — Hyperscale (Citus)](concepts-hyperscale-firewall-rules.md).
