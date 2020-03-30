---
title: SSL — usługa Azure Database for PostgreSQL — pojedynczy serwer
description: Instrukcje i informacje dotyczące konfigurowania łączności SSL dla usługi Azure Database dla postgreSql — single server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 303da4dcb68a79e69254f6610afc0003bf0aa22c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477004"
---
# <a name="configure-ssl-connectivity-in-azure-database-for-postgresql---single-server"></a>Konfigurowanie łączności SSL w bazie danych azure dla postgreSQL — pojedynczy serwer

Usługa Azure Database for PostgreSQL preferuje łączenie aplikacji klienckich z usługą PostgreSQL przy użyciu warstwy SSL (Secure Sockets Layer). Wymuszanie połączeń SSL między serwerem bazy danych a aplikacjami klienckimi pomaga chronić przed atakami typu "man-in-the-middle", szyfrując strumień danych między serwerem a aplikacją.

Domyślnie usługa bazy danych PostgreSQL jest skonfigurowana tak, aby wymagała połączenia SSL. Można wyłączyć wymaganie SSL, jeśli aplikacja kliencka nie obsługuje łączności SSL.

## <a name="enforcing-ssl-connections"></a>Wymuszanie połączeń SSL

W przypadku wszystkich serwerów usługi Azure Database for PostgreSQL aprowidyzwanych za pośrednictwem portalu Azure i interfejsu wiersza polecenia wymuszanie połączeń SSL jest domyślnie włączone. 

Podobnie parametry połączenia, które są wstępnie zdefiniowane w ustawieniach "Parametry połączenia" w obszarze serwera w witrynie Azure portal zawierają parametry wymagane dla języków wspólnych, aby połączyć się z serwerem bazy danych przy użyciu SSL. Parametr SSL różni się w zależności od złącza, na przykład "ssl=true" lub "sslmode=require" lub "sslmode=required" i innych odmian.

## <a name="configure-enforcement-of-ssl"></a>Konfigurowanie wymuszania ssl

Opcjonalnie można wyłączyć wymuszanie łączności SSL. Platforma Microsoft Azure zaleca, aby zawsze włączać ustawienie **wymuszanie połączenia SSL** w celu zwiększenia bezpieczeństwa.

### <a name="using-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Odwiedź swoją usługę Azure Database dla serwera PostgreSQL i kliknij pozycję **Zabezpieczenia połączenia**. Użyj przycisku przełączania, aby włączyć lub wyłączyć ustawienie **Wymuszanie połączenia SSL.** Następnie kliknij przycisk **Zapisz**.

![Zabezpieczenia połączenia — wyłącz wymuszanie SSL](./media/concepts-ssl-connection-security/1-disable-ssl.png)

Możesz potwierdzić to ustawienie, przeglądając **stronę,** aby wyświetlić wskaźnik **wymuszania stanu SSL.**

### <a name="using-azure-cli"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure

Można włączyć lub wyłączyć parametr **ssl-enforcement** przy użyciu `Enabled` lub `Disabled` wartości odpowiednio w usłudze Azure CLI.

```azurecli
az postgres server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Enabled
```

## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>Upewnij się, że twoja aplikacja lub struktura obsługuje połączenia SSL

Niektóre struktury aplikacji, które używają PostgreSQL dla swoich usług bazy danych nie włączyć SSL domyślnie podczas instalacji. Jeśli serwer PostgreSQL wymusza połączenia SSL, ale aplikacja nie jest skonfigurowana dla SSL, aplikacja może nie łączyć się z serwerem bazy danych. Zapoznaj się z dokumentacją aplikacji, aby dowiedzieć się, jak włączyć połączenia SSL.

## <a name="applications-that-require-certificate-verification-for-ssl-connectivity"></a>Aplikacje wymagające weryfikacji certyfikatów dla łączności SSL

W niektórych przypadkach aplikacje wymagają bezpiecznego połączenia lokalnego pliku certyfikatu wygenerowanego z pliku certyfikatu zaufanego urzędu certyfikacji (cer). Certyfikat do łączenia się z serwerem usługi Azure https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pemDatabase for PostgreSQL znajduje się pod adresem . Pobierz plik certyfikatu i zapisz go w preferowanej lokalizacji.

### <a name="connect-using-psql"></a>Połącz się za pomocą psql

W poniższym przykładzie pokazano, jak połączyć się z serwerem PostgreSQL za pomocą narzędzia wiersza polecenia psql. Użyj `sslmode=verify-full` ustawienia parametry połączenia, aby wymusić weryfikację certyfikatu SSL. Przekaż ścieżkę pliku certyfikatu lokalnego do parametru. `sslrootcert`

Następujące polecenie jest przykładem ciągu połączenia psql:

```shell
psql "sslmode=verify-full sslrootcert=BaltimoreCyberTrustRoot.crt host=mydemoserver.postgres.database.azure.com dbname=postgres user=myusern@mydemoserver"
```

> [!TIP]
> Upewnij się, że `sslrootcert` wartość przekazana do zgodnej ze ścieżką pliku dla zapisanego certyfikatu.

## <a name="next-steps"></a>Następne kroki

Przejrzyj różne opcje łączności aplikacji w [bibliotekach połączeń dla usługi Azure Database dla postgreSQL](concepts-connection-libraries.md).
