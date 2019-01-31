---
title: Jak używać usługi Azure Cosmos DB zmiany źródła danych za pomocą usługi Azure Functions
description: Użyj usługi Azure Cosmos DB zmiany źródła danych za pomocą usługi Azure Functions
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 93cd93b40c142d504c52f08f9005d082fb5a2a20
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2019
ms.locfileid: "55469485"
---
# <a name="trigger-azure-functions-from-azure-cosmos-db"></a>Wyzwalanie usługi Azure Functions z usługi Azure Cosmos DB

Jeśli używasz usługi Azure Functions Najprostszym sposobem, aby nawiązać połączenie zestawienia zmian jest dodanie [wyzwalacza usługi Azure Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger) do aplikacji usługi Azure Functions. Tworząc wyzwalacz usługi Cosmos DB w aplikacji usługi Azure Functions, wybierz kontener Cosmos, aby nawiązać połączenie i funkcja jest wyzwalana przy każdej zmianie coś w kontenerze.

Wyzwalacze mogą być tworzone w portalu usługi Azure Functions lub w portalu usługi Azure Cosmos DB lub programowo. Aby uzyskać więcej informacji, zobacz [obliczeniowych bez użycia serwera bazy danych, przy użyciu usługi Azure Cosmos DB i Azure Functions](serverless-computing-database.md).

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="how-can-i-configure-azure-functions-to-read-from-a-particular-region"></a>Jak można skonfigurować usługi Azure functions do odczytu w danym regionie?

Istnieje możliwość definiowania [PreferredLocations](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations?view=azure-dotnet#Microsoft_Azure_Documents_Client_ConnectionPolicy_PreferredLocations) podczas używania wyzwalacza usługi Azure Cosmos DB, aby określić listę regionów. Wartość jest taka sama jak dostosować ConnectionPolicy, aby wyzwalacz odczytywać swoje preferowane regiony. Najlepiej, jeśli chcesz odczytać z najbliższego regionu, w której wdrażana jest usługi Azure Functions.

### <a name="what-is-the-default-size-of-batches-in-azure-functions"></a>Co to jest domyślny rozmiar partii w usłudze Azure Functions?

Rozmiar domyślny wynosi 100 elementów dla każdego wywołania usługi Azure Functions. Jednak ta liczba jest konfigurowane w pliku function.json. Oto kompletny [listę opcji konfiguracji](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration). Jeśli tworzysz lokalnie, należy zaktualizować ustawienia aplikacji w pliku local.settings.json.

### <a name="i-am-monitoring-a-container-and-reading-its-change-feed-however-i-dont-get-all-the-inserted-documents-some-items-are-missing"></a>Monitoruję kontenera i odczytanie jego kanał informacyjny zmian, jednak nie pojawia się wstawiane dokumenty, niektóre elementy brakuje?

Upewnij się, że nie ma żadnych innych funkcji platformy Azure, odczytywanie z tego samego kontenera, z tym samym kontenerze dzierżawy. Brak dokumenty są przetwarzane przez inne usługi Azure Functions, również przy użyciu tej samej dzierżawy.

W związku z tym Jeśli tworzysz wiele funkcji platformy Azure do odczytu, że takie same zestawienia zmian muszą używać kontenerów do innej dzierżawy lub udostępnianie tego samego kontenera przy użyciu konfiguracji "leasePrefix". Jednak korzystając z biblioteką procesora zestawienia zmian można uruchomić wiele wystąpień funkcji platformy Azure i zestaw SDK dzieli dokumentów między różne wystąpienia automatycznie dla Ciebie.

### <a name="azure-cosmos-item-is-updated-every-second-and-i-dont-get-all-the-changes-in-azure-functions-listening-to-change-feed"></a>Element usługi Azure Cosmos jest aktualizowany co sekundę, a nie pojawia się wszystkie zmiany w usłudze Azure Functions nasłuchiwanie zestawienia zmian?

Usługa Azure Functions sonduje zmiany kanału informacyjnego zmian w sposób ciągły, z opóźnieniem, Domyślna maksymalna 5 sekund. Czy istnieją oczekujących zmian do odczytu, czy istnieją oczekujące zmiany, po zastosowaniu wyzwalacz, funkcja będzie je odczytać natychmiast. Jednak jeśli nie istnieją żadne zmiany oczekujące, funkcja poczekaj 5 sekund i sondowania pod kątem zmian.

Jeśli dokument odbierze wiele zmian w tym samym interwale, który miał wyzwalacza do sondowania pod kątem nowych zmian, może pojawić się najnowszą wersję dokumentu a nie do pośredniego.

Jeśli chcesz sondować zmiany źródła danych dla mniej niż 5 sekund, na przykład, przez co sekundę, można skonfigurować godziny sondowania "feedPollDelay", zobacz [kompletna Konfiguracja](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations?view=azure-dotnet#Microsoft_Azure_Documents_Client_ConnectionPolicy_PreferredLocations). Jest on zdefiniowany w milisekundach z domyślną 5000. Sondowanie, możliwe jest mniejsza niż 1 sekundę, ale nie jest to zalecane, ponieważ zostanie uruchomiona, przy użyciu większej ilości pamięci procesora CPU.

### <a name="can-multiple-azure-functions-read-one-containers-change-feed"></a>Wiele funkcji Azure przeczytasz Kanał informacyjny zmian jednego kontenera?

Tak. Wiele funkcji platformy Azure może odczytywać źródło danych zmiany tego samego kontenera. Jednak usługi Azure Functions, musi mieć osobne "leaseCollectionPrefix" zdefiniowany.

### <a name="if-i-am-processing-change-feed-by-using-azure-functions-in-a-batch-of-10-documents-and-i-get-an-error-at-seventh-document-in-that-case-the-last-three-documents-are-not-processed-how-can-i-start-processing-from-the-failed-document-ie-seventh-document-in-my-next-feed"></a>Jeśli jestem przetwarzania zmian źródła danych za pomocą usługi Azure Functions w zadaniu wsadowym dokumentów 10 i pojawia się błąd w siódmej dokumentu. W takim przypadku w ostatnie trzy dokumenty jak mogę zacząć przetwarzania dokumentu nie powiodło się (tj, siódmy dokumentu) w mojej następne źródła danych nie są przetwarzane?

Do obsługi błędu, zalecany wzorzec jest zawijany kodu za pomocą bloku try / catch, a jeśli są Iterowanie listy dokumentów, zawijanie każdej iteracji w jego własnej bloku try / catch. CATCH błąd i umieść tego dokumentu w kolejce (utraconych wiadomości), a następnie zdefiniować logikę radzenia sobie z dokumentów, które spowodowało błąd. Przy użyciu tej metody w przypadku partii 200 dokumentu i tylko jeden dokument nie powiodło się, nie trzeba Pozbywać się całą partię.

W przypadku wystąpienia błędu, należy nie rewind punktu sprawdzania powrót do początku else będzie można nadal jest wyświetlany te dokumenty z kanału informacyjnego zmian. Należy pamiętać, że przechowuje kanału informacyjnego zmian ostatniego zrzut końcowego przystawki dokumentów, w związku z tym możesz utracić poprzednią migawkę do dokumentu. Kanał informacyjny zmian zapewnia tylko jedna wersja ostatniej części dokumentu, a między inne procesy mogą pochodzić i zmian w dokumencie.

Jak zachować naprawianie kodu, wkrótce znajdziesz żadnych dokumentów na kolejki utraconych wiadomości. Usługa Azure Functions jest wywoływana automatycznie przez system kanału informacyjnego zmian i wyboru punktu wewnętrznie obsługiwany przez funkcję platformy Azure. Jeśli chcesz wycofać w punkcie wyboru i kontrolowanie wszystkich aspektów, należy rozważyć przy użyciu zmian źródła danych zestawu SDK procesora.

### <a name="are-there-any-extra-costs-for-using-the-azure-cosmos-db-trigger"></a>Czy istnieją żadnych dodatkowych kosztów dla przy użyciu wyzwalacza usługi Azure Cosmos DB?

Wyzwalacz usługi Azure Cosmos DB wewnętrznie korzysta z biblioteką procesora zestawienia zmian. W efekcie wymaga dodatkowego kolekcję o nazwie kolekcję dzierżaw, aby zachować stan i częściowe punkty kontrolne. Pozwala mieć możliwość dynamicznego skalowania i Kontynuuj w przypadku, gdy chcesz zatrzymać usługi Azure Functions i kontynuować przetwarzanie w późniejszym czasie na potrzeby tego stanu zarządzania. Aby dowiedzieć się więcej, zobacz [sposób pracy ze zmianą kanału informacyjnego w bibliotece procesora](change-feed-processor.md).

## <a name="next-steps"></a>Kolejne kroki

Można teraz kontynuować, aby dowiedzieć się więcej na temat zmiany źródła danych w następujących artykułach:

* [Omówienie Kanał informacyjny zmian](change-feed.md)
* [Sposoby na odczytywanie zestawienia zmian](read-change-feed.md)
* [Za pomocą zmian źródła danych z biblioteką procesora](change-feed-processor.md)
* [Jak pracować z zmiany źródła danych w bibliotece procesora](change-feed-processor.md)
* [Bezserwerowa baza danych obliczeń z użyciem usługi Azure Cosmos DB i Azure Functions](serverless-computing-database.md)
