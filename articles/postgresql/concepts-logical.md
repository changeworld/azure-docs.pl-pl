---
title: Dekodowanie logiczne — usługa Azure Database for PostgreSQL — pojedynczy serwer
description: W tym artykule opisano dekodowanie logiczne i wal2json do przechwytywania danych zmian w usłudze Azure Database dla postgreSQL — pojedynczy serwer
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: 1213b38f2b67e8fed179cfda4308943808893e1b
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80522149"
---
# <a name="logical-decoding"></a>Dekodowanie logiczne
 
[Dekodowanie logiczne w PostgreSQL](https://www.postgresql.org/docs/current/logicaldecoding.html) umożliwia strumieniowe przesyłanie zmian danych do zewnętrznych konsumentów. Dekodowanie logiczne jest powszechnie używane do przesyłania strumieniowego zdarzeń i zmiany scenariuszy przechwytywania danych.

Dekodowanie logiczne używa wtyczki wyjściowej do konwersji dziennika zapisu postgres z wyprzedzeniem (WAL) do czytelnego formatu. Usługa Azure Database for PostgreSQL udostępnia dwie wtyczki wyjściowe: [test_decoding](https://www.postgresql.org/docs/current/test-decoding.html) i [wal2json](https://github.com/eulerto/wal2json).
 

> [!NOTE]
> Dekodowanie logiczne jest w publicznej wersji zapoznawczej w usłudze Azure Database dla PostgreSQL — single server.


## <a name="set-up-your-server"></a>Konfigurowanie serwera
Aby rozpocząć korzystanie z dekodowania logicznego, włącz serwer do zapisywania i przesyłania strumieniowego WAL. 

1. Ustaw azure.replication_support przy `logical` użyciu interfejsu wiersza polecenia platformy Azure. 
   ```
   az postgres server configuration set --resource-group mygroup --server-name myserver --name azure.replication_support --value logical
   ```

   > [!NOTE]
   > Jeśli używasz replik odczytu, azure.replication_support `logical` ustawić również umożliwia uruchamianie replik. Jeśli przestaniesz używać dekodowania `replica`logicznego, zmień ustawienie z powrotem na . 


2. Uruchom ponownie serwer, aby zastosować zmiany.
   ```
   az postgres server restart --resource-group mygroup --name myserver
   ```

## <a name="start-logical-decoding"></a>Rozpocznij dekodowanie logiczne

Dekodowanie logiczne może być używane za pośrednictwem protokołu przesyłania strumieniowego lub interfejsu SQL. Obie metody używają [gniazd replikacji](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html#LOGICALDECODING-REPLICATION-SLOTS). Gniazdo reprezentuje strumień zmian z jednej bazy danych.

Użycie gniazda replikacji wymaga uprawnień replikacji postgres. W tej chwili uprawnienie replikacji jest dostępne tylko dla administratora serwera. 

### <a name="streaming-protocol"></a>Protokół przesyłania strumieniowego
Często zaleca się stosowanie zmian przy użyciu protokołu przesyłania strumieniowego. Możesz utworzyć własne konsumenta / złącze, lub użyć narzędzia takiego jak [Debezium](https://debezium.io/). 

Odwiedź dokumentację wal2json na [przykład przy użyciu protokołu przesyłania strumieniowego z pg_recvlogical](https://github.com/eulerto/wal2json#pg_recvlogical).


### <a name="sql-interface"></a>Interfejs SQL
W poniższym przykładzie używamy interfejsu SQL z wtyczką wal2json.
 
1. Utwórz miejsce.
   ```SQL
   SELECT * FROM pg_create_logical_replication_slot('test_slot', 'wal2json');
   ```
 
2. Wydawanie poleceń SQL. Przykład:
   ```SQL
   CREATE TABLE a_table (
      id varchar(40) NOT NULL,
      item varchar(40),
      PRIMARY KEY (id)
   );
   
   INSERT INTO a_table (id, item) VALUES ('id1', 'item1');
   DELETE FROM a_table WHERE id='id1';
   ```

3. Zużywają zmiany.
   ```SQL
   SELECT data FROM pg_logical_slot_get_changes('test_slot', NULL, NULL, 'pretty-print', '1');
   ```

   Dane wyjściowe będą wyglądać następująco:
   ```
   {
         "change": [
         ]
   }
   {
         "change": [
                  {
                           "kind": "insert",
                           "schema": "public",
                           "table": "a_table",
                           "columnnames": ["id", "item"],
                           "columntypes": ["character varying(40)", "character varying(40)"],
                           "columnvalues": ["id1", "item1"]
                  }
         ]
   }
   {
         "change": [
                  {
                           "kind": "delete",
                           "schema": "public",
                           "table": "a_table",
                           "oldkeys": {
                                 "keynames": ["id"],
                                 "keytypes": ["character varying(40)"],
                                 "keyvalues": ["id1"]
                           }
                  }
         ]
   }
   ```

4. Upuść gniazdo po zakończeniu korzystania z niego.
   ```SQL
   SELECT pg_drop_replication_slot('test_slot'); 
   ```


## <a name="monitoring-slots"></a>Gniazda monitorujące

Należy monitorować dekodowanie logiczne. Wszelkie nieużywane gniazdo replikacji muszą zostać usunięte. Sloty przytrzymują dzienniki Postgres WAL i odpowiednie katalogi systemowe, dopóki zmiany nie zostaną odczytane przez konsumenta. Jeśli konsument zawiedzie lub nie został poprawnie skonfigurowany, nieskonsumowane dzienniki będą piętrzą się i wypełnią magazyn. Ponadto nieskonsumowane dzienniki zwiększają ryzyko zawijania identyfikatora transakcji. Obie sytuacje mogą spowodować, że serwer stanie się niedostępny. W związku z tym jest bardzo ważne, że gniazda replikacji logicznej są używane w sposób ciągły. Jeśli gniazdo replikacji logicznej nie jest już używane, upuść go natychmiast.

Kolumna "aktywna" w widoku pg_replication_slots wskazuje, czy do gniazda jest podłączony konsument.
```SQL
SELECT * FROM pg_replication_slots;
```

[Ustaw alerty](howto-alert-on-metric.md) na *używane magazynu* i *max opóźnienia w danych replik,* aby powiadomić, gdy wartości zwiększyć powyżej normalnych progów. 

> [!IMPORTANT]
> Należy upuścić nieużywane gniazda replikacji. Niezastosowanie się do tego może prowadzić do niedostępności serwera.

## <a name="how-to-drop-a-slot"></a>Jak upuścić szczelinę
Jeśli nie są aktywnie zużywa gniazda replikacji należy go upuścić.

Aby upuścić gniazdo `test_slot` replikacji wywoływane przy użyciu języka SQL:
```SQL
SELECT pg_drop_replication_slot('test_slot');
```

> [!IMPORTANT]
> Jeśli przestaniesz używać dekodowania logicznego, `replica` zmień `off`plik azure.replication_support z powrotem do lub . Szczegóły WAL zachowane `logical` przez są bardziej pełne i powinny być wyłączone, gdy dekodowanie logiczne nie jest używany. 

 
## <a name="next-steps"></a>Następne kroki

* Odwiedź dokumentację Postgres, aby [dowiedzieć się więcej o dekodowaniu logicznym](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html).
* Jeśli masz pytania dotyczące dekodowania logicznego, skontaktuj się z [naszym zespołem.](mailto:AskAzureDBforPostgreSQL@service.microsoft.com)
* Dowiedz się więcej o [replikach odczytu](concepts-read-replicas.md).

