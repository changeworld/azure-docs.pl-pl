---
title: Dzienniki serwera w bazie danych Azure database for PostgreSQL
description: W tym artykule opisano, jak usługi Azure Database dla PostgreSQL generuje dzienników zapytań i błąd i jak rejestrować przechowywania jest skonfigurowany.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: bcca8ce8d11482dd8517992297b7e8a5b94ac8b1
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2018
ms.locfileid: "37435494"
---
# <a name="server-logs-in-azure-database-for-postgresql"></a>Dzienniki serwera w bazie danych Azure database for PostgreSQL 
Usługa Azure Database for postgresql w warstwie generuje zapytania i błąd dzienników. Dostęp do dzienników transakcji nie jest obsługiwane. Dziennikami zapytań, błędów i może służyć do identyfikowania, rozwiązywanie problemów i naprawić błędy konfiguracji i nieoptymalne wydajności. Aby uzyskać więcej informacji, zobacz [raportowanie błędów i rejestrowanie](https://www.postgresql.org/docs/9.6/static/runtime-config-logging.html).

## <a name="access-server-logs"></a>Dzienniki dostępu serwera
Możesz wyświetlić listę i pobrać dzienniki błędów serwera Azure PostgreSQL za pomocą witryny Azure portal [wiersza polecenia platformy Azure](howto-configure-server-logs-using-cli.md)i interfejsów API REST platformy Azure.

## <a name="log-retention"></a>Przechowywanie dzienników
Można ustawić okresu przechowywania, system dzienników przy użyciu **dziennika\_przechowywania\_okres** parametru skojarzonego z serwerem. Jednostki dla tego parametru jest dni. Wartość domyślna to 3 dni. Wartość maksymalna to 7 dni. Serwer musi mieć wystarczające przydzielonej pamięci ma zawierać pliki dziennika zachowane.
Obróć pliki dziennika, co godzinę lub rozmiarze 100 MB, osiągnięta jako pierwsza.

## <a name="configure-logging-for-azure-postgresql-server"></a>Konfigurowanie rejestrowania dla serwera Azure PostgreSQL
Na serwerze, można włączyć rejestrowanie zapytań i rejestrowania błędów. Dzienniki błędów mogą zawierać informacje odkurzający auto, połączenia i punkty kontrolne.

Rejestrowanie zapytań można włączyć dla wystąpienia bazy danych postgresql w warstwie, ustawiając dwa parametry serwera: `log_statement` i `log_min_duration_statement`.

**Dziennika\_instrukcji** parametr określa, które instrukcje SQL są rejestrowane. Zaleca się ustawienie tego parametru na ***wszystkich*** do rejestrowania wszystkich instrukcji; wartość domyślna to Brak.

**Dziennika\_min\_czas trwania\_instrukcji** parametr ustawia limit (w milisekundach) w instrukcji, które mają być rejestrowane. Wszystkie instrukcje SQL, które działają dłużej niż ustawienie parametru są rejestrowane. Ten parametr jest wyłączona, domyślnie ustawiany na minus 1 (-1). Włączenie tego parametru można pomóc w określeniu zapytania sposób niezoptymalizowany w swoich aplikacjach.

**Dziennika\_min\_wiadomości** umożliwia kontrolę, której komunikatu poziomy są zapisywane w dzienniku serwera. Wartość domyślna to ostrzeżenie. 

Aby uzyskać więcej informacji na temat tych ustawień, zobacz [raportowanie błędów i rejestrowanie](https://www.postgresql.org/docs/9.6/static/runtime-config-logging.html) dokumentacji. Szczególnie konfigurowania usługi Azure Database dla parametrów serwera PostgreSQL, zobacz [Dostosowywanie parametrów konfiguracji serwera przy użyciu wiersza polecenia platformy Azure](howto-configure-server-parameters-using-cli.md).

## <a name="next-steps"></a>Kolejne kroki
- Aby uzyskać dostęp do dzienników przy użyciu interfejsu wiersza polecenia z wiersza polecenia platformy Azure, zobacz [Konfiguruj i dostęp do dzienników serwera przy użyciu wiersza polecenia platformy Azure](howto-configure-server-logs-using-cli.md).
- Aby uzyskać więcej informacji na temat parametrów serwera, zobacz [Dostosowywanie parametrów konfiguracji serwera przy użyciu wiersza polecenia platformy Azure](howto-configure-server-parameters-using-cli.md).
