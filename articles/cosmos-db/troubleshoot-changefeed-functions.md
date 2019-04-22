---
title: Diagnozowanie i rozwiązywanie problemów w przypadku korzystania z usługi Azure Cosmos DB wyzwalacza w usłudze Azure Functions
description: Typowe problemy, rozwiązania i czynności diagnostycznych, korzystając z wyzwalacza usługi Azure Cosmos DB przy użyciu usługi Azure Functions
author: ealsur
ms.service: cosmos-db
ms.date: 04/16/2019
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 5416c576e6392b7c5714ef83f152453aeff8d964
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2019
ms.locfileid: "59685855"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-cosmos-db-trigger-in-azure-functions"></a>Diagnozowanie i rozwiązywanie problemów w przypadku korzystania z usługi Azure Cosmos DB wyzwalacza w usłudze Azure Functions

W tym artykule omówiono typowe problemy, rozwiązania i czynności diagnostycznych, gdy używasz [usługi Azure Cosmos DB wyzwalacza](change-feed-functions.md) za pomocą usługi Azure Functions.

## <a name="dependencies"></a>Zależności

Azure Cosmos DB wyzwalaczy i powiązań zależą od pakietów rozszerzeń za pośrednictwem podstawowego środowiska uruchomieniowego usługi Azure Functions. Zawsze Zachowuj te pakiety aktualizacji, ponieważ mogą one obejmować poprawki i nowe funkcje, które mogą rozwiązać potencjalne problemy, które mogą wystąpić:

* Dla usługi Azure Functions w wersji 2, zobacz [Microsoft.Azure.WebJobs.Extensions.CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB).
* Dla usługi Azure Functions w wersji 1, zobacz [Microsoft.Azure.WebJobs.Extensions.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB).

W tym artykule będzie zawsze odwoływać się do usługi Azure Functions w wersji 2 w każdym przypadku, gdy środowisko uruchomieniowe jest wymieniony, chyba że jawnie określony.

## <a name="consuming-the-cosmos-db-sdk-separately-from-the-trigger-and-bindings"></a>Korzystanie z zestawu SDK Cosmos DB niezależnie od wyzwalacz i powiązania

Kluczowe funkcje pakietu rozszerzenia jest w celu zapewnienia obsługi powiązań i wyzwalaczy usługi Azure Cosmos DB. Obejmuje również [zestawu .NET SDK usługi Azure Cosmos DB](sql-api-sdk-dotnet-core.md), co jest przydatne, jeśli chcesz interakcji programowej z usługą Azure Cosmos DB bez korzystania z powiązań i wyzwalaczy.

Jeśli do chcesz używać zestawu SDK usługi Azure Cosmos DB, upewnij się, że nie dodano do projektu innego odwołania do pakietu NuGet. Zamiast tego **umożliwiają rozwiązania za pośrednictwem pakietu rozszerzenia usługi Azure Functions odwołanie do zestawu SDK**.

Ponadto jeśli ręcznie utworzysz wystąpienie programu [klienta zestawu SDK usługi Azure Cosmos DB](./sql-api-sdk-dotnet-core.md), należy przestrzegać wzorzec masz tylko jedno wystąpienie klienta [przy użyciu metody wzorca Singleton](../azure-functions/manage-connections.md#documentclient-code-example-c) . Ten proces pozwoli uniknąć potencjalnych problemów gniazda w operacji.

## <a name="common-known-scenarios-and-workarounds"></a>Typowe scenariusze znane i obejścia

### <a name="azure-function-fails-with-error-message-either-the-source-collection-collection-name-in-database-database-name-or-the-lease-collection-collection2-name-in-database-database2-name-does-not-exist-both-collections-must-exist-before-the-listener-starts-to-automatically-create-the-lease-collection-set-createleasecollectionifnotexists-to-true"></a>Funkcja platformy Azure kończy się niepowodzeniem z komunikatem o błędzie "albo kolekcji źródłowej"kolekcji name"(w bazie danych"Nazwa bazy danych") lub kolekcję dzierżaw"kolekcji 2 name"(w bazie danych"bazy danych 2 name") nie istnieje. Obie kolekcje muszą istnieć przed uruchomieniem odbiornika. Aby automatycznie utworzyć kolekcję dzierżaw, ustaw "CreateLeaseCollectionIfNotExists" na "true" "

Oznacza to, co najmniej jednego z kontenerów usługi Azure Cosmos wymagane dla wyzwalacza do pracy nie istnieje lub nie są dostępne dla funkcji platformy Azure. **Błąd, sama poinformuje, której bazy danych Azure Cosmos i kontenerów jest wyzwalacza szukasz** zgodnie z konfiguracją.

1. Sprawdź `ConnectionStringSetting` atrybutu i jej **odwołuje się do ustawienia, która znajduje się w aplikacji funkcji platformy Azure**. Wartość dla tego atrybutu nie powinien być ciąg połączenia, ale nazwa ustawienia konfiguracji.
2. Upewnij się, że `databaseName` i `collectionName` istnieje na koncie usługi Azure Cosmos. Jeśli używasz Automatyczna wartość zastąpienia (przy użyciu `%settingName%` wzorce), sprawdź, czy nazwa ustawienia istnieje w aplikacji funkcji platformy Azure.
3. Jeśli nie określisz `LeaseCollectionName/leaseCollectionName`, wartością domyślną jest "dzierżawy". Sprawdź, czy istnieje taki kontener. Opcjonalnie możesz ustawić `CreateLeaseCollectionIfNotExists` atrybutu w wyzwalacza do `true` automatycznie go utworzyć.
4. Sprawdź swoje [konfiguracji zapory konto usługi Cosmos Azure](how-to-configure-firewall.md) aby zobaczyć, że nie jest nie blokuje funkcję platformy Azure.

### <a name="azure-function-fails-to-start-with-shared-throughput-collection-should-have-a-partition-key"></a>Funkcja platformy Azure nie powiedzie się zaczynać "Shared przepływność kolekcji powinien mieć klucza partycji"

Poprzednie wersje usługi Azure Cosmos DB Extension nie obsługuje korzystania z kontenera dzierżawy, który został utworzony w ramach [udostępnionej przepływności bazy danych](./set-throughput.md#set-throughput-on-a-database). Aby rozwiązać ten problem, zaktualizuj [Microsoft.Azure.WebJobs.Extensions.CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB) rozszerzenie, aby uzyskać najnowszą wersję.

### <a name="azure-function-fails-to-start-with-the-lease-collection-if-partitioned-must-have-partition-key-equal-to-id"></a>Funkcja platformy Azure nie powiedzie się zaczynać "kolekcję dzierżaw, jeśli podzielona na partycje, muszą mieć taki sam identyfikator klucza partycji."

Ten błąd oznacza, że bieżący kontener dzierżawy jest podzielona na partycje, ale ścieżka klucza partycji jest `/id`. Aby rozwiązać ten problem, należy ponownie utworzyć kontener dzierżawy za pomocą `/id` jako klucza partycji.

### <a name="you-see-a-value-cannot-be-null-parameter-name-o-in-your-azure-functions-logs-when-you-try-to-run-the-trigger"></a>Zostanie wyświetlony "nie może mieć wartości null. Nazwa parametru: o "dzienników usługi Azure Functions przy próbie uruchomienia wyzwalacza

Ten problem występuje, jeśli używasz witryny Azure portal, a następnie spróbuj wybrać **Uruchom** przycisk na ekranie podczas sprawdzania korzystającego z wyzwalacza funkcji platformy Azure. Wyzwalacz nie wymaga do wyboru Uruchom, aby rozpocząć, zostanie automatycznie uruchomiony po wdrożeniu funkcji platformy Azure. Jeśli chcesz sprawdzić strumień dziennika funkcji platformy Azure w witrynie Azure portal, po prostu przejdź do kontenera monitorowanych i wstawianie niektóre nowe elementy zostaną automatycznie wyświetlone wykonywania wyzwalacza.

### <a name="my-changes-take-too-long-be-received"></a>Moje zmiany w trwają zbyt długo odbioru

Ten scenariusz może mieć wiele przyczyn i wszystkie z nich powinna być sprawdzana:

1. W tym samym regionie co konto usługi Azure Cosmos jest wdrożony funkcji platformy Azure? Opóźnienia sieci optymalne zarówno w przypadku funkcji platformy Azure, jak i kontem usługi Azure Cosmos powinny wspólnie przechowywane w tym samym regionie platformy Azure.
2. Zmiany występują w kontenerze usługi Azure Cosmos ciągłej lub sporadyczne?
Po drugie, mogą wystąpić pewne opóźnienie między zmiany są przechowywane i pobieranie ich funkcji platformy Azure. Jest to spowodowane wewnętrznie, gdy wyzwalacz sprawdza obecność zmian w kontenerze usługi Azure Cosmos wykryje Brak oczekujących do odczytu, jej będzie stan uśpienia na skonfigurowanej ilości czasu (5 sekund, domyślnie) przed sprawdzania dostępności nowych zmian (w celu uniknięcia wysokiego użycia jednostek Zarezerwowanych). Można skonfigurować ten czas uśpienia za pośrednictwem `FeedPollDelay/feedPollDelay` w [konfiguracji](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration) wyzwalacza (jest oczekiwaną wartością w milisekundach).
3. Kontener usługi Azure Cosmos może być [limited współczynnik](./request-units.md).
4. Możesz użyć `PreferredLocations` atrybutu w wyzwalacza w taki sposób, aby określić listę rozdzielonych przecinkami regionów świadczenia usługi Azure, aby zdefiniować kolejność niestandardowego preferowanych połączenia.

### <a name="some-changes-are-missing-in-my-trigger"></a>Niektóre zmiany nie występują w mojej wyzwalacza

Jeśli okaże się, że niektóre zmiany, które wystąpiły w kontenerze usługi Azure Cosmos nie są są pobierane przez funkcję platformy Azure, ma kroku bada, który ma mieć miejsce.

Jeśli funkcji platformy Azure otrzyma zmian, jest często przetwarza je i może opcjonalnie, wysyłać wyniki do innego miejsca docelowego. Podczas badania jest brak zmiany, upewnij się, że **miar jakie zmiany są otrzymywane w momencie pozyskiwania** (po uruchomieniu funkcji platformy Azure), nie na komputerze docelowym.

Jeśli brakuje pewnych zmian w folderze docelowym, może to oznaczać, że to błąd pojawia się podczas wykonywania funkcji platformy Azure, po zmiany zostały odebrane.

W tym scenariuszu najlepszy plan działania jest dodanie `try/catch blocks` w kodzie, a także wewnątrz pętli, które może przetwarzać zmiany, aby wykrywać jakiekolwiek niepowodzenie dla konkretnego podzbioru elementów i odpowiednio je obsłużyć (wysyłać je do innego magazynu w celu dalszego Analiza lub ponownych prób). 

> **Wyzwalacza usługi Azure Cosmos DB domyślnie nie próbę partii zmian w przypadku, gdy wystąpił nieobsługiwany wyjątek** podczas wykonywanie kodu. Oznacza to, że przyczyna, że zmiany nie dostarczone w miejscu docelowym jest czy kończą się niepowodzeniem do ich przetworzenia.

Jeśli okaże się, że niektóre zmiany nie zostały w ogóle odebrane przez wyzwalacz, najbardziej typowym scenariuszem jest to, że istnieje **uruchomiona jest inna funkcja platformy Azure**. Może to być inna funkcja platformy Azure wdrożonych na platformie Azure lub funkcji platformy Azure lokalnie na uruchomiony, komputer dewelopera, który ma **dokładnie taką samą konfigurację** (takie same monitorowane i dzierżawy kontenerów), a ta funkcja platformy Azure jest kradzież podzbiór zmiany można oczekiwać do przetworzenia przez funkcję platformy Azure.

Ponadto można sprawdzić poprawności tego scenariusza, jeśli wiesz, ile wystąpień aplikacja funkcji platformy Azure masz uruchomiony. Jeśli sprawdzanie kontenera dzierżawy i liczby elementów dzierżawy w ramach odrębne wartości `Owner` właściwość w nich powinna być równa liczbie wystąpień aplikacji funkcji. W przypadku kolejnych właścicieli znanych wystąpień aplikacja funkcji platformy Azure, oznacza to, że tych dodatkowych właścicieli są jedną "kradzież" zmiany.

Jeden łatwy sposób obejścia problemu tej sytuacji jest stosowanie `LeaseCollectionPrefix/leaseCollectionPrefix` do funkcji z nową/inną wartość lub alternatywnie testowanie za pomocą nowego kontenera dzierżawy.

## <a name="next-steps"></a>Kolejne kroki

* [Włącz monitorowanie dla usługi Azure Functions](../azure-functions/functions-monitoring.md)
* [Azure Cosmos DB .NET SDK rozwiązywania problemów](./troubleshoot-dot-net-sdk.md)