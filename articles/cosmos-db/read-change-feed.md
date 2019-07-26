---
title: Uzyskiwanie dostępu do źródła zmian w Azure Cosmos DB Azure Cosmos DB
description: W tym artykule opisano różne opcje dostępne do odczytu i dostępu do źródła zmian w Azure Cosmos DB Azure Cosmos DB.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: rimman
ms.openlocfilehash: 3d52ba1abc22aae6121ea6a36f943851dfcca7a0
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2019
ms.locfileid: "68467662"
---
# <a name="reading-azure-cosmos-db-change-feed"></a>Odczytywanie kanału informacyjnego zmiany Azure Cosmos DB

Ze źródłem zmian Azure Cosmos DB można korzystać z jednej z następujących opcji:

* Używanie Azure Functions
* Korzystanie z biblioteki procesora źródła zmian
* Korzystanie z zestawu SDK interfejsu API Azure Cosmos DB SQL

## <a name="using-azure-functions"></a>Używanie Azure Functions

Azure Functions jest najprostszą i zalecaną opcją. Po utworzeniu wyzwalacza Azure Functions dla Cosmos DB, można wybrać kontener do połączenia, a funkcja platformy Azure zostanie wyzwolona za każdym razem, gdy nastąpi zmiana do kontenera. Wyzwalacze można tworzyć przy użyciu portalu Azure Functions, portalu Azure Cosmos DB lub programowo przy użyciu zestawów SDK. Program Visual Studio i VS Code zapewniają pomoc techniczną do pisania Azure Functions i można nawet użyć interfejsu wiersza polecenia Azure Functions do tworzenia aplikacji dla wielu platform. Można napisać i debugować kod na pulpicie, a następnie wdrożyć funkcję jednym kliknięciem. Aby dowiedzieć się więcej, zobacz artykuł [Przetwarzanie baz danych bez serwera przy użyciu Azure Functions](serverless-computing-database.md) i [Używanie kanału informacyjnego zmian z Azure Functions](change-feed-functions.md)).

## <a name="using-the-change-feed-processor-library"></a>Korzystanie z biblioteki procesora źródła zmian

Biblioteka procesora kanału informacyjnego zmian powoduje ukrycie złożoności i zapewnia pełną kontrolę nad źródłem zmian. Biblioteka jest zgodna ze wzorcem obserwatora, gdzie funkcja przetwarzania jest wywoływana przez bibliotekę. W przypadku kanału informacyjnego o wysokiej przepływności można utworzyć wystąpienie wielu klientów w celu odczytania źródła zmian. Ze względu na to, że korzystasz z biblioteki procesora źródła zmian, będzie ona automatycznie dzielić obciążenie między różnymi klientami, bez konieczności implementowania tej logiki. Cała złożoność jest obsługiwana przez bibliotekę. Jeśli chcesz mieć własny moduł równoważenia obciążenia, możesz wdrożyć `IPartitionLoadBalancingStrategy` dla niestandardowej strategii partycji, aby przetwarzać Źródło zmian. Aby dowiedzieć się więcej, zobacz [Korzystanie z biblioteki procesora źródła zmian](change-feed-processor.md).

## <a name="using-the-azure-cosmos-db-sql-api-sdk"></a>Korzystanie z zestawu SDK interfejsu API Azure Cosmos DB SQL

Zestaw SDK otrzymuje kontrolę nad źródłem zmian. Punkt kontrolny można zarządzać, uzyskiwać dostęp do określonego klucza partycji logicznej itp. Jeśli masz wielu czytników, możesz użyć `ChangeFeedOptions` do dystrybucji wczytywania odczytu do różnych wątków lub różnych klientów. 

## <a name="next-steps"></a>Kolejne kroki

Można teraz kontynuować, aby dowiedzieć się więcej na temat zmiany źródła danych w następujących artykułach:

* [Przegląd źródła zmian](change-feed.md)
* [Za pomocą zmian źródła danych za pomocą usługi Azure Functions](change-feed-functions.md)
* [Za pomocą zmian źródła danych z biblioteką procesora](change-feed-processor.md)
