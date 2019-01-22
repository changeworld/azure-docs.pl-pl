---
title: Poziomy spójności i interfejsy API usługi Azure Cosmos DB
description: Zrozumienie poziomów spójności między interfejsami API w usłudze Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2018
ms.reviewer: sngun
ms.openlocfilehash: a506c696cdb9ca6c6221b54c63d2446b7cb86a69
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2019
ms.locfileid: "54430570"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>Poziomy spójności i interfejsy API usługi Azure Cosmos DB

Pięć modeli spójności oferowanych przez usługę Azure Cosmos DB są natywnie obsługiwane przez interfejs API SQL usługi Azure Cosmos DB. Gdy używasz usługi Azure Cosmos DB, interfejs API SQL jest ustawieniem domyślnym. 

Usługa Azure Cosmos DB również zapewnia natywną obsługę sieci zgodnego z protokołem interfejsów API na potrzeby popularnych baz danych. Bazy danych obejmują bazy danych MongoDB, Apache Cassandra, Gremlin i Azure Table storage. Te bazy danych nie ma możliwości dokładnie zdefiniowanych modeli spójności lub objętym umową SLA gwarancje dotyczące poziomów spójności. Zwykle zapewniają tylko podzbiór pięcioma modelami spójności oferowanych przez usługę Azure Cosmos DB. Dla interfejsu API SQL, interfejs API Gremlin i interfejsu API tabel jest używany domyślny poziom spójności skonfigurowany na koncie usługi Azure Cosmos DB. 

W poniższych sekcjach przedstawiono mapowanie między spójności danych wymagany przez sterownik klienta OSS dla bazy danych Apache Cassandra 4.x i MongoDB 3.4. Ten dokument zawiera również odpowiadającymi im poziomami spójności usługi Azure Cosmos DB dla bazy danych Apache Cassandra i bazy danych MongoDB.

## <a id="cassandra-mapping"></a>Mapowanie między poziomami spójności bazy danych Apache Cassandra usługi Azure Cosmos DB

W poniższej tabeli przedstawiono mapowania spójności bazy danych Apache Cassandra oraz poziomów spójności w usłudze Azure Cosmos DB. Dla każdej bazy danych Cassandra odczytu i zapisu poziomów spójności, odpowiedni poziom spójności bazy danych Cosmos zapewnia silniejsza, czyli bardziej rygorystyczne gwarancji.


<table>
<tr> 
  <th rowspan="2">Poziom spójności bazy danych Cassandra</th> 
  <th rowspan="2">Poziom spójności usługi cosmos DB</th> 
  <th colspan="3">Mapowanie spójności zapisu</th> 
  <th colspan="3">Mapowanie spójności odczytu</th> 
</tr> 


 
 <tr> 
  <th>Cassandra</th> 
  <th>Cosmos DB</th> 
  <th>Gwarancja</th> 
  <th>Z bazy danych Cassandra</th> 
  <th>To Cosmos DB</th> 
  <th>Gwarancja</th> 
 </tr> 
 
  <tr> 
  <td rowspan="6">WSZYSTKIE</td> 
  <td rowspan="6">Silna</td> 
  <td>WSZYSTKIE</td> 
  <td>Silna</td> 
  <td>Operacje atomowe</td> 
  <td>WSZYSTKIE KWORUM, SERYJNY LOCAL_QUORUM, LOCAL_SERIAL, 3, DWÓCH, JEDEN, LOCAL_ONE</td> 
  <td>Silna</td> 
  <td>Operacje atomowe</td> 
 </tr> 
 
 <tr> 
  <td rowspan="2">EACH_QUORUM</td> 
  <td rowspan="2">Silna</td> 
  <td rowspan="2">Operacje atomowe</td> 
  <td>WSZYSTKIE KWORUM, SERYJNY LOCAL_QUORUM, LOCAL_SERIAL, TRZY, DWA</td> 
  <td>Silna</td> 
  <td >Operacje atomowe</td> 
 </tr> 
 
 <tr>
 <td>LOCAL_ONE, PO JEDNYM</td>
  <td>Spójny prefiks</td>
   <td>Globalne spójny prefiks</td>
 </tr>
 

 <tr> 
  <td rowspan="2">KWORUM SZEREGOWEJ</td> 
  <td rowspan="2">Silna</td> 
  <td rowspan="2">Operacje atomowe</td> 
  <td>WSZYSTKIM KWORUM, SERYJNY</td> 
  <td>Silna</td> 
  <td >Operacje atomowe</td> 
 </tr> 

 <tr>
   <td>LOCAL_ONE, JEDNYM, LOCAL_QUORUM LOCAL_SERIAL, DWÓCH, TRZECH</td>
   <td>Spójny prefiks</td>
   <td>Globalne spójny prefiks</td>
 </tr>
 
 
 <tr> 
 <td>LOCAL_QUORUM, 3, DWÓCH, JEDEN, LOCAL_ONE, <b>ANY</b></td> 
  <td>Spójny prefiks</td> 
  <td>Globalne spójny prefiks</td> 
  <td>LOCAL_ONE, JEDNEGO, DWÓCH, TRZECH LOCAL_QUORUM, KWORUM</td> 
  <td>Spójny prefiks</td> 
  <td>Globalne spójny prefiks</td>
 </tr> 
 
 
  <tr> 
  <td rowspan="6">EACH_QUORUM</td> 
  <td rowspan="6">Silna</td> 
  <td rowspan="2">EACH_QUORUM</td> 
  <td rowspan="2">Silna</td> 
  <td rowspan="2">Operacje atomowe</td> 
  <td>WSZYSTKIE KWORUM, SERYJNY LOCAL_QUORUM, LOCAL_SERIAL, TRZY, DWA</td> 
  <td>Silna</td> 
  <td>Operacje atomowe</td> 
 </tr> 
 
 <tr>
 <td>LOCAL_ONE, PO JEDNYM</td>
  <td>Spójny prefiks</td>
   <td>Globalne spójny prefiks</td>
 </tr>
 
 
 
 <tr> 
  <td rowspan="2">KWORUM SZEREGOWEJ</td> 
  <td rowspan="2">Silna</td> 
  <td rowspan="2">Operacje atomowe</td> 
  <td>WSZYSTKIM KWORUM, SERYJNY</td> 
  <td>Silna</td> 
  <td>Operacje atomowe</td> 
 </tr> 
 
 <tr>
 <td>LOCAL_ONE, JEDNYM, LOCAL_QUORUM LOCAL_SERIAL, DWÓCH, TRZECH</td>
  <td>Spójny prefiks</td>
   <td>Globalne spójny prefiks</td>
 </tr>
 
 
  <tr> 
  <td rowspan="2">LOCAL_QUORUM, 3, DWÓCH, JEDEN, LOCAL_ONE, WSZYSTKIE</td> 
  <td rowspan="2">Spójny prefiks</td> 
  <td rowspan="2">Globalne spójny prefiks</td> 
  <td>WSZYSTKIE</td> 
  <td>Silna</td> 
  <td>Operacje atomowe</td> 
 </tr> 
 
 <tr>
 <td>LOCAL_ONE, JEDNEGO, DWÓCH, TRZECH LOCAL_QUORUM, KWORUM</td>
  <td>Spójny prefiks</td>
   <td>Globalne spójny prefiks</td>
 </tr>


  <tr> 
  <td rowspan="4">KWORUM</td> 
  <td rowspan="4">Silna</td> 
  <td rowspan="2">KWORUM SZEREGOWEJ</td> 
  <td rowspan="2">Silna</td> 
  <td rowspan="2">Operacje atomowe</td> 
  <td>WSZYSTKIM KWORUM, SERYJNY</td> 
  <td>Silna</td> 
  <td>Operacje atomowe</td> 
 </tr> 
 
 <tr>
 <td>LOCAL_ONE, JEDNYM, LOCAL_QUORUM LOCAL_SERIAL, DWÓCH, TRZECH</td>
  <td>Spójny prefiks</td>
   <td>Globalne spójny prefiks</td>
 </tr>
 
 
 <tr> 
  <td rowspan="2">LOCAL_QUORUM, 3, DWÓCH, JEDEN, LOCAL_ONE, WSZYSTKIE</td> 
  <td rowspan="2">Spójny prefiks </td> 
  <td rowspan="2">Globalne spójny prefiks </td> 
  <td>WSZYSTKIE</td> 
  <td>Silna</td> 
  <td>Operacje atomowe</td> 
 </tr> 
 
 <tr>
 <td>LOCAL_ONE, JEDNEGO, DWÓCH, TRZECH LOCAL_QUORUM, KWORUM</td>
  <td>Spójny prefiks</td>
   <td>Globalne spójny prefiks</td>
 </tr>
 
 <tr> 
  <td rowspan="4">LOCAL_QUORUM, THREE, TWO</td> 
  <td rowspan="4">Powiązana nieaktualność</td> 
  <td rowspan="2">LOCAL_QUORUM, LOCAL_SERIAL, DWÓCH, TRZECH</td> 
  <td rowspan="2">Powiązana nieaktualność</td> 
  <td rowspan="2">Powiązana nieaktualność.<br/>
Co najwyżej K wersji lub czasu (t) za zaporą.<br/>
Odczytaj najnowsze zatwierdzone wartość w regionie. 
</td> 
  
  <td>KWORUM, LOCAL_QUORUM, LOCAL_SERIAL, DWA, TRZY</td> 
  <td>Powiązana nieaktualność</td> 
  <td>Powiązana nieaktualność.<br/>
Co najwyżej K wersji lub czasu (t) za zaporą. <br/>
Odczytaj najnowsze zatwierdzone wartość w regionie. </td> 
 </tr> 
 
 <tr>
 <td>LOCAL_ONE, PO JEDNYM</td>
  <td>Spójny prefiks</td>
   <td>Spójny prefiks regionu</td>
 </tr>
 
 
 <tr> 
  <td>JEDEN, LOCAL_ONE, WSZYSTKIE</td> 
  <td>Spójny prefiks </td> 
  <td >Spójny prefiks regionu </td> 
  <td>LOCAL_ONE, JEDNEGO, DWÓCH, TRZECH LOCAL_QUORUM, KWORUM</td> 
  <td>Spójny prefiks</td> 
  <td>Spójny prefiks regionu</td> 
 </tr> 
</table>

## <a id="mongo-mapping"></a>Mapowanie między poziomami spójności bazy danych MongoDB 3.4 i Azure Cosmos DB

W poniższej tabeli przedstawiono "Przeczytaj uwagi" mapowanie między MongoDB 3.4 i domyślny poziom spójności w usłudze Azure Cosmos DB. W tabeli przedstawiono wdrożenia w wielu regionach i jednym regionie.

| **MongoDB 3.4** | **Usługa Azure Cosmos DB (wielu regionów)** | **Usługa Azure Cosmos DB (jednym regionie)** |
| - | - | - |
| Linearizable | Silna | Silna |
| Większość | Powiązana nieaktualność | Silna |
| Lokalna | Spójny prefiks | Spójny prefiks |

## <a name="next-steps"></a>Kolejne kroki

Przeczytaj więcej na temat poziomów spójności i zgodności usługi Azure Cosmos DB API za pomocą interfejsów API typu open source. Zobacz następujące artykuły:

* [Dostępność i wydajność kompromisy dla różnych poziomów spójności](consistency-levels-tradeoffs.md)
* [Funkcje bazy danych MongoDB, obsługiwane przez interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB](mongodb-feature-support.md)
* [Funkcje bazy danych Apache Cassandra obsługiwane przez interfejs API rozwiązania Cassandra usługi Azure Cosmos DB](cassandra-support.md)