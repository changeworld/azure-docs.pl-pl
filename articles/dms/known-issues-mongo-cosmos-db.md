---
title: 'Znane problemy: migracja z mongodb do usługi Azure CosmosDB'
titleSuffix: Azure Database Migration Service
description: Dowiedz się więcej o znanych problemach i ograniczeniach migracji z usługi MongoDB do usługi Azure Cosmos DB przy użyciu usługi migracji bazy danych azure.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/27/2020
ms.openlocfilehash: ae5d5f2d282c546f5172ca1c8cb0e420d3b6e96b
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878055"
---
# <a name="known-issuesmigration-limitations-with-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>Znane problemy/ograniczenia migracji z migracji z mongodb do interfejsu API usługi Azure Cosmos DB dla mongodb

Znane problemy i ograniczenia związane z migracjami z MongoDB do interfejsu API usługi Cosmos DB dla mongodb są opisane w poniższych sekcjach.

## <a name="migration-fails-as-a-result-of-using-the-incorrect-ssl-cert"></a>Migracja kończy się niepowodzeniem w wyniku użycia niepoprawnego certyfikatu SSL

* **Symptom:** Ten problem jest widoczny, gdy użytkownik nie może połączyć się z serwerem źródłowym MongoDB. Pomimo otwarcia wszystkich portów zapory, użytkownik nadal nie może się połączyć.

| Przyczyna         | Rozwiązanie |
| ------------- | ------------- |
| Przy użyciu certyfikatu z podpisem własnym w usłudze migracji bazy danych platformy Azure może prowadzić do niepowodzenia migracji z powodu niepoprawnego certyfikatu SSL. Komunikat o błędzie może zawierać komunikat "Certyfikat zdalny jest nieprawidłowy zgodnie z procedurą sprawdzania poprawności". | Użyj oryginalnego certyfikatu urzędu certyfikacji.  Certyfikaty z podpisem własnym są zwykle używane tylko w testach wewnętrznych. Po zainstalowaniu oryginalnego certyfikatu z urzędu certyfikacji można użyć ssl w usłudze migracji usługi Azure Database bez problemu (połączenia z usługą Cosmos DB używają SSL za pomocą interfejsu MONGO API).<br><br> |

## <a name="unable-to-get-the-list-of-databases-to-map-in-dms"></a>Nie można uzyskać listy baz danych do mapowania w DMS

* **Symptom:** Nie można uzyskać listy DB w bloku **ustawienia bazy danych** podczas korzystania z trybu usługi Azure **Storage** w bloku **Select source.**

| Przyczyna         | Rozwiązanie |
| ------------- | ------------- |
| Brakuje ciągu połączenia konta magazynu i w związku z tym nie można uwierzytelnić. | Utwórz sygnatury dostępu Współdzielonego w kontenerze obiektów blob w Eksploratorze magazynu i użyj adresu URL z informacjami o kontenerze sygnatury dostępu Współdzielonego jako źródłowego ciągu połączenia szczegółów.<br><br> |

## <a name="using-an-unsupported-version-of-the-database"></a>Korzystanie z nieobsługiwałej wersji bazy danych

* **Symptom**: Migracja kończy się niepowodzeniem.

| Przyczyna         | Rozwiązanie |
| ------------- | ------------- |
| Próba migracji do usługi Azure Cosmos DB z nieobsługiwałej wersji mongodb. | W miarę zwalniania nowych wersji bazy danych MongoDB są one testowane w celu zapewnienia zgodności z usługą migracji bazy danych Azure, a usługa jest okresowo aktualizowana w celu zaakceptowania najnowszych wersji. Jeśli istnieje natychmiastowa potrzeba migracji, jako obejście można wyeksportować bazy danych/kolekcje do usługi Azure Storage, a następnie wskazać źródło na wynikowy zrzut. Utwórz sygnatury dostępu Współdzielonego w kontenerze obiektów blob w Eksploratorze magazynu, a następnie użyj adresu URL z informacjami o kontenerze sygnatury dostępu Współdzielonego jako źródłowego ciągu połączenia szczegółów.<br><br> |

## <a name="next-steps"></a>Następne kroki

* Zobacz samouczek [Migracja mongodb do interfejsu API usługi Azure Cosmos DB dla mongodb online przy użyciu DMS](tutorial-mongodb-cosmos-db-online.md).
* Zobacz samouczek [Migracja mongodb do interfejsu API usługi Azure Cosmos DB dla mongodb w trybie offline przy użyciu DMS](tutorial-mongodb-cosmos-db.md).