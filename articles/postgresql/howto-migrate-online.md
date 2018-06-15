---
title: Migracja minimalnym czasem przestojów PostgreSQL bazą danych Azure
description: W tym artykule opisano, jak przeprowadzić migrację minimalnym czasem przestojów wyodrębniania bazy danych programu PostgreSQL w pliku zrzutu, przywracanie bazy danych programu PostgreSQL z pliku archiwum utworzone przez pg_dump w bazie danych Azure dla PostgreSQL i konfigurowania ładowania początkowego i Synchronizacja danych ciągłego źródłowej bazy danych do docelowej bazy danych przy użyciu replikacji Attunity Migrations firmy Microsoft.
services: postgresql
author: HJToland3
ms.author: jtoland
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 48cf460405ae3985553f9bff29f4fd7abb008196
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/28/2018
ms.locfileid: "29692093"
---
# <a name="minimal-downtime-migration-to-azure-database-for-postgresql"></a>Migracja minimalnym czasem przestojów PostgreSQL bazą danych Azure
Istniejącą bazę danych PostgreSQL można migrować do bazy danych platformy Azure dla PostgreSQL przy użyciu replikacji Attunity Migrations firmy Microsoft. Replikacja Attunity jest wspólnego z Attunity i firmy Microsoft. Wraz z usługi migracji bazy danych Azure jest uwzględniony bez ponoszenia dodatkowych kosztów do klientów firmy Microsoft. 

Replikacja Attunity minimalizuje czas przestoju podczas migracji bazy danych, a źródłowa baza danych zachowuje operacyjne całego procesu.

Replikacja Attunity to narzędzie replikacji danych, które umożliwia synchronizacji danych między z różnych źródeł i obiektów docelowych. Propaguje on skrypcie tworzenia schematu i dane skojarzone z każdej tabeli bazy danych. Replikacja Attunity nie są propagowane innych artefakty (na przykład SP, wyzwalacze, funkcje i tak dalej) lub konwersji, na przykład, kod PL/SQL, który znajduje się w takich artefaktów do T-SQL.

> [!NOTE]
> Mimo że replikacja Attunity obsługuje szeroką gamę scenariuszy migracji, koncentruje się na obsługę konkretnego podzestawu par źródłowy i docelowy.

Zawiera omówienie procesu migracji minimalnym czasem przestojów:

* **Migrowanie schematu źródła PostgreSQL** do bazy danych dla bazy danych PostgreSQL za pomocą usługi Azure [pg_dump](https://www.postgresql.org/docs/9.3/static/app-pgdump.html) polecenia z parametrem - n, a następnie za pomocą [pg_restore](https://www.postgresql.org/docs/9.3/static/app-pgrestore.html) polecenia.

* **Ustawienie ładowania początkowego i przeprowadź synchronizację z ciągłymi danymi ze źródłowej bazy danych do docelowej bazy danych** przy użyciu replikacji Attunity Migrations firmy Microsoft. Dzięki temu minimalizuje czas, który źródłowej bazy danych musi być ustawiona jako tylko do odczytu przygotowując się do aplikacji docelowej bazy danych PostgreSQL przełączyć się na platformie Azure.

Aby uzyskać więcej informacji o replikacji Attunity Migrations Microsoft oferty zobacz następujące zasoby:
 - Przejdź do [replikacja Attunity Migrations Microsoft](https://aka.ms/attunity-replicate) strony sieci Web.
 - Pobierz [Attunity replikacji dla migracji w programie Microsoft](http://discover.attunity.com/download-replicate-microsoft-lp6657.html).
 - Przejdź do [Attunity replikować społeczności](https://aka.ms/attunity-community/) Przewodnik Szybki Start, samouczki i pomocy technicznej.
 - Aby uzyskać wskazówki krok po kroku przy użyciu replikacji Attunity do migracji z PostgreSQL do bazy danych platformy Azure dla PostgreSQL, zobacz [Przewodnik po migracji bazy danych](https://datamigration.microsoft.com/scenario/postgresql-to-azurepostgresql).