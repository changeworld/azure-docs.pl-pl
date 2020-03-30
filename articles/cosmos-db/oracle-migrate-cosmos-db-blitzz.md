---
title: Migrowanie danych z oracle do interfejsu API Cassandra usługi Azure Cosmos DB przy użyciu blitzz
description: Dowiedz się, jak migrować dane z bazy danych Oracle do interfejsu API Cassandra usługi Azure Cosmos DB przy użyciu aplikacji Blitzz.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/21/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: fe132ee6ab90fdae99463f11ecf46f352690b810
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "69984357"
---
# <a name="migrate-data-from-oracle-to-azure-cosmos-db-cassandra-api-account-using-blitzz"></a>Migrowanie danych z oracle do konta API Usługi Azure Cosmos DB Cassandra przy użyciu blitzz

Interfejs API Cassandra w usłudze Azure Cosmos DB stał się doskonałym wyborem dla obciążeń korporacyjnych, które są uruchomione w oracle z różnych powodów, takich jak:

* **Lepsza skalowalność i dostępność:** Eliminuje pojedyncze punkty awarii, lepszą skalowalność i dostępność aplikacji.

* **Znaczne oszczędności:** Możesz zaoszczędzić koszty dzięki usłudze Azure Cosmos DB, która obejmuje koszt maszyn wirtualnych, przepustowość i wszelkie odpowiednie licencje Oracle. Ponadto nie musisz zarządzać centrami danych, serwerami, magazynem SSD, siecią i kosztami energii elektrycznej.

* **Brak kosztów zarządzania i monitorowania:** Jako w pełni zarządzana usługa w chmurze usługa Azure Cosmos DB usuwa obciążenie związane z zarządzaniem i monitorowaniem niezliczonych ustawień.

Istnieją różne sposoby migracji obciążeń bazy danych z jednej platformy do drugiej. [Blitzz](https://www.blitzz.io) to narzędzie, które oferuje bezpieczny i niezawodny sposób wykonywania migracji bez przestojów z różnych baz danych do usługi Azure Cosmos DB. W tym artykule opisano kroki wymagane do migracji danych z bazy danych Oracle do interfejsu API Cassandra usługi Azure Cosmos DB przy użyciu systemu Blitzz.

## <a name="benefits-using-blitzz-for-migration"></a>Korzyści z używania blitzz do migracji

Rozwiązanie do migracji blitzz jest zgodne z podejściem krok po kroku do migracji złożonych obciążeń operacyjnych. Oto niektóre z kluczowych aspektów planu migracji blitzz zero przestojów:

* Oferuje automatyczną migrację logiki biznesowej (tabele, indeksy, widoki) z bazy danych Oracle do usługi Azure Cosmos DB. Nie trzeba ręcznie tworzyć schematów.

* Blitzz oferuje dużą i równoległą replikację bazy danych. Umożliwia platformom źródłowym i docelowym synchronizację podczas migracji przy użyciu techniki o nazwie Change-Data-Capture (CDC). Za pomocą CDC, Blitzz stale pobiera strumień zmian ze źródłowej bazy danych(Oracle) i stosuje go do docelowej bazy danych(Azure Cosmos DB).

* Jest odporny na uszkodzenia i gwarantuje dokładnie raz dostarczanie danych nawet podczas awarii sprzętu lub oprogramowania w systemie.

* Zabezpiecza dane podczas przesyłania przy użyciu różnych metodologii zabezpieczeń, takich jak SSL, szyfrowanie.

* Oferuje usługi do konwersji złożonej logiki biznesowej napisanej w PL/SQL do równoważnej logiki biznesowej w usłudze Azure Cosmos DB.

## <a name="steps-to-migrate-data"></a>Kroki migracji danych

W tej sekcji opisano kroki wymagane do skonfigurowania systemu Blitzz i migracji danych z bazy danych Oracle do usługi Azure Cosmos DB.

1. Z komputera, na którym planujesz zainstalować replikatora Blitzz, dodaj certyfikat zabezpieczeń. Ten certyfikat jest wymagany przez replikatora Blitzz do ustanowienia połączenia SSL z określonym kontem usługi Azure Cosmos DB. Certyfikat można dodać, wykonując następujące czynności:

   ```bash
   wget https://cacert.omniroot.com/bc2025.crt
   mv bc2025.crt bc2025.cer
   keytool -keystore $JAVA_HOME/lib/security/cacerts -importcert -alias bc2025ca -file bc2025.cer
   ```

1. ou można uzyskać instalację Blitzz i pliki binarne albo poprzez żądanie demo na [stronie internetowej Blitzz](https://www.blitzz.io). Alternatywnie można również wysłać [wiadomość e-mail](mailto:success@blitzz.io) do zespołu.

   ![Blitzz replikator narzędzia do pobrania](./media/oracle-migrate-cosmos-db-blitzz/blitzz-replicant-download.png)

   ![Pliki replikatorów Blitzz](./media/oracle-migrate-cosmos-db-blitzz/replicant-files.png)

1. Z terminalu interfejsu wiersza polecenia skonfiguruj konfigurację źródłowej bazy danych. Otwórz plik konfiguracyjny za pomocą **`vi conf/conn/oracle.yml`** polecenia i dodaj oddzieloną przecinkami listę adresów IP węzłów oracle, numer portu, nazwę użytkownika, hasło i inne wymagane szczegóły. Poniższy kod przedstawia przykładowy plik konfiguracyjny:

   ```bash
   type: ORACLE

   host: localhost
   port: 53546

   service-name: IO

   username: '<Username of your Oracle database>'
   password: '<Password of your Oracle database>'

   conn-cnt: 30
   use-ssl: false
   ```

   ![Otwórz edytor połączeń Oracle](./media/oracle-migrate-cosmos-db-blitzz/open-connection-editor-oracle.png)

   ![Konfiguracja połączenia Oracle](./media/oracle-migrate-cosmos-db-blitzz/oracle-connection-configuration.png)

   Po wypełnieniu szczegółów konfiguracji zapisz i zamknij plik.

1. Opcjonalnie można skonfigurować plik filtru źródłowej bazy danych. Plik filtru określa, które schematy lub tabele mają być migrowane. Otwórz plik konfiguracyjny za pomocą **`vi filter/oracle_filter.yml`** polecenia i wprowadź następujące szczegóły konfiguracji:

   ```bash

   allow:
   -    schema: “io_blitzz”
   Types: [TABLE]
   ```
 
   Po wypełnieniu szczegółów filtru bazy danych zapisz i zamknij plik.

1. Następnie skonfigurujesz konfigurację docelowej bazy danych. Przed zdefiniowaniem konfiguracji [należy utworzyć konto interfejsu API usługi Azure Cosmos DB Cassandra](create-cassandra-dotnet.md#create-a-database-account). [Wybierz odpowiedni klucz partycji](partitioning-overview.md#choose-partitionkey) z danych, a następnie utwórz obszar klucza i tabelę do przechowywania zmigrowanych danych.

1. Przed migracją danych należy zwiększyć przepływność kontenera do ilości wymaganej do szybkiej migracji aplikacji. Na przykład można zwiększyć przepływność do 100000 rUs. Skalowanie przepływności przed rozpoczęciem migracji pomoże ci przeprowadzić migrację danych w krótszym czasie. 

   ![Skalowanie kontenera usługi Azure Cosmos w całym](./media/oracle-migrate-cosmos-db-blitzz/scale-throughput.png)

   Należy zmniejszyć przepływność po zakończeniu migracji. Na podstawie ilości przechowywanych danych i procesorów operacyjnych wymaganych dla każdej operacji można oszacować przepływność wymaganą po migracji danych. Aby dowiedzieć się więcej na temat szacowania wymaganych obiektów ru, zobacz [Aprowizuj przepływność kontenerów i baz danych](set-throughput.md) oraz [Oszacuj ru/s przy użyciu artykułów planowania pojemności usługi Azure Cosmos DB.](estimate-ru-with-capacity-planner.md)

1. Pobierz **punkt kontaktowy, port, nazwę użytkownika**i **hasło podstawowe** konta usługi Azure Cosmos z **okienka Parametry połączenia.** Te wartości będą używane w pliku konfiguracyjnym.

1. Z terminalu interfejsu wiersza polecenia skonfiguruj docelową konfigurację bazy danych. Otwórz plik konfiguracyjny za pomocą **`vi conf/conn/cosmosdb.yml`** polecenia i dodaj oddzieloną przecinkami listę identyfikatora URI hosta, numer portu, nazwę użytkownika, hasło i inne wymagane parametry. Oto przykład zawartości w pliku konfiguracyjnym:

   ```bash
   type: COSMOSDB

   host: `<Azure Cosmos account’s Contact point>`
   port: 10350

   username: 'blitzzdemo'
   password: `<Your Azure Cosmos account’s primary password>'

   max-connections: 30
   use-ssl: false
   ```

1. Następnie migruj dane za pomocą Blitzz. Replikatora Blizz można uruchomić w **trybie pełnym** lub **migawki:**

   * **Tryb pełny** — w tym trybie replikator nadal działa po migracji i nasłuchuje wszelkich zmian w źródłowym systemie Oracle. Jeśli wykryje żadnych zmian, są one replikowane na docelowym koncie usługi Azure Cosmos w czasie rzeczywistym.

   * **Tryb migawki** — w tym trybie można przeprowadzić migrację schematu i jednorazową replikację danych. Replikacja w czasie rzeczywistym nie jest obsługiwana za pomocą tej opcji.


   Za pomocą powyższych dwóch trybów, migracji można przeprowadzić przy zerowym przestoju.

1. Aby przeprowadzić migrację danych, z terminalu interfejsu wiersza polecenia replikatora Blitzz uruchom następujące polecenie:

   ```bash
   ./bin/replicant full conf/conn/oracle.yaml conf/conn/cosmosdb.yaml --filter filter/oracle_filter.yaml --replace-existing
   ```

   Interfejs użytkownika replikatora pokazuje postęp replikacji. Po zakończeniu migracji schematu i operacji migawki, postęp pokazuje 100%. Po zakończeniu migracji można sprawdzić poprawność danych w docelowej bazie danych usługi Azure Cosmos.

   ![Dane wyjściowe migracji danych Oracle](./media/oracle-migrate-cosmos-db-blitzz/oracle-data-migration-output.png)

1. Ponieważ użyto pełnego trybu migracji, można wykonywać operacje, takie jak wstawianie, aktualizowanie lub usuwanie danych ze źródłowej bazy danych Oracle. Później można sprawdzić, czy są one replikowane w czasie rzeczywistym w docelowej bazie danych usługi Azure Cosmos. Po migracji upewnij się, aby zmniejszyć przepływność skonfigurowany dla kontenera usługi Azure Cosmos.

1. Replikatora można zatrzymać w dowolnym punkcie i ponownie uruchomić za pomocą **przełącznika --resume.** Replikacja wznawia od punktu, który został zatrzymany bez uszczerbku dla spójności danych. Następujące polecenie pokazuje, jak używać przełącznika wznowienia.

   ```bash
   ./bin/replicant full conf/conn/oracle.yaml conf/conn/cosmosdb.yaml --filter filter/oracle_filter.yaml --replace-existing --resume
   ```

Aby dowiedzieć się więcej na temat migracji danych do miejsca docelowego, migracji w czasie rzeczywistym, zobacz [demo replikatora Blitzz](https://www.youtube.com/watch?v=y5ZeRK5A-MI).

## <a name="next-steps"></a>Następne kroki

* [Aprowizacja przepływności kontenerów i baz danych](set-throughput.md) 
* [Najważniejsze wskazówki dotyczące klucza partycji](partitioning-overview.md#choose-partitionkey)
* [Szacowanie usług RU/s przy użyciu artykułów planowania pojemności usługi Azure Cosmos DB](estimate-ru-with-capacity-planner.md)