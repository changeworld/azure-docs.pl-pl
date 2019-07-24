---
title: Diagnozowanie i rozwiązywanie problemów podczas korzystania z wyzwalacza Azure Cosmos DB w programie Azure Functions
description: Typowe problemy, obejścia i kroki diagnostyczne podczas korzystania z wyzwalacza Azure Cosmos DB z Azure Functions
author: ealsur
ms.service: cosmos-db
ms.date: 05/23/2019
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 9c728a735e56e461e49dd3f594186c9c0192a3f0
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/16/2019
ms.locfileid: "68250012"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-cosmos-db-trigger-in-azure-functions"></a>Diagnozowanie i rozwiązywanie problemów podczas korzystania z wyzwalacza Azure Cosmos DB w programie Azure Functions

W tym artykule opisano typowe problemy, obejścia i kroki diagnostyczne, podczas korzystania z [wyzwalacza Azure Cosmos DB](change-feed-functions.md) z Azure Functions.

## <a name="dependencies"></a>Zależności

Wyzwalacz Azure Cosmos DB i powiązania są zależne od pakietów rozszerzeń przez podstawowy Azure Functions środowiska uruchomieniowego. Należy zawsze aktualizować te pakiety, ponieważ mogą one zawierać poprawki i nowe funkcje, które mogą rozwiązać ewentualne potencjalne problemy, które mogą wystąpić:

* W przypadku Azure Functions v2, zobacz [Microsoft. Azure. WebJobs. Extensions. CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB).
* Aby uzyskać Azure Functions V1, zobacz [Microsoft. Azure. WebJobs. Extensions. DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB).

Ten artykuł będzie zawsze odnosił się do Azure Functions v2, gdy środowisko uruchomieniowe zostanie wymienione, chyba że zostanie jawnie określony.

## <a name="consume-the-azure-cosmos-db-sdk-independently"></a>Korzystaj z zestawu SDK Azure Cosmos DB niezależnie

Kluczową funkcją pakietu rozszerzenia jest zapewnienie obsługi wyzwalacza Azure Cosmos DB i powiązań. Zawiera również [zestaw Azure Cosmos DB .NET SDK](sql-api-sdk-dotnet-core.md), który jest przydatny, jeśli chcesz korzystać z Azure Cosmos DB programowo bez użycia wyzwalacza i powiązań.

Jeśli chcesz użyć zestawu SDK Azure Cosmos DB, upewnij się, że nie dodajesz do projektu innego odwołania do pakietu NuGet. Zamiast tego **pozwól, aby odwołanie do zestawu SDK było rozpoznawane za pomocą pakietu rozszerzenia Azure Functions "** . Korzystanie z zestawu SDK Azure Cosmos DB niezależnie od wyzwalacza i powiązań

Ponadto, jeśli ręcznie tworzysz własne wystąpienie [klienta Azure Cosmos DB SDK](./sql-api-sdk-dotnet-core.md), należy postępować zgodnie ze wzorcem, który ma tylko jedno wystąpienie klienta [przy użyciu pojedynczego podejścia do wzorca](../azure-functions/manage-connections.md#documentclient-code-example-c). Ten proces pozwoli uniknąć potencjalnych problemów z gniazdem w operacjach.

## <a name="common-scenarios-and-workarounds"></a>Typowe scenariusze i obejścia

### <a name="azure-function-fails-with-error-message-collection-doesnt-exist"></a>Funkcja platformy Azure kończy się niepowodzeniem i nie istnieje kolekcja komunikatów o błędach

Funkcja platformy Azure kończy się niepowodzeniem z komunikatem o błędzie "Nazwa kolekcji źródłowej" (w bazie danych "database name") lub kolekcja dzierżaw "Collection2-Name" (w bazie danych "Database2-Name") nie istnieje. Obie kolekcje muszą istnieć przed rozpoczęciem odbiorniku. Aby automatycznie utworzyć kolekcję dzierżawy, ustaw dla opcji "CreateLeaseCollectionIfNotExists" wartość "true".

Oznacza to, że jeden lub oba kontenery usługi Azure Cosmos wymagane do działania wyzwalacza nie istnieją lub nie są dostępne dla funkcji platformy Azure. Sam błąd informuje o tym, **które usługi Azure Cosmos Database i kontenerów są wywoływane** na podstawie konfiguracji.

1. Sprawdź, czy  atrybuticzyodwołujesiędoustawienia,któreistniejewaplikacjafunkcjiplatformy`ConnectionStringSetting` Azure. Wartość w tym atrybucie nie powinna być ciągiem połączenia, ale nazwą ustawienia konfiguracji.
2. Sprawdź, czy `databaseName` usługa `collectionName` i istnieje na koncie usługi Azure Cosmos. Jeśli używasz automatycznej wymiany wartości (przy użyciu `%settingName%` wzorców), upewnij się, że nazwa ustawienia istnieje w aplikacja funkcji platformy Azure.
3. Jeśli nie określisz `LeaseCollectionName/leaseCollectionName`, wartość domyślna to "dzierżawy". Sprawdź, czy kontener istnieje. Opcjonalnie można ustawić `CreateLeaseCollectionIfNotExists` atrybut w wyzwalaczu na `true` , aby automatycznie go utworzyć.
4. Sprawdź [konfigurację zapory konta usługi Azure Cosmos](how-to-configure-firewall.md) , aby zobaczyć, że nie blokuje ona funkcji platformy Azure.

### <a name="azure-function-fails-to-start-with-shared-throughput-collection-should-have-a-partition-key"></a>Nie można uruchomić funkcji platformy Azure z opcją "udostępniona kolekcja przepływności powinna mieć klucz partycji"

Poprzednie wersje rozszerzenia Azure Cosmos DB nie obsługiwały kontenera dzierżaw, który został utworzony w ramach [udostępnionej bazy danych przepływności](./set-throughput.md#set-throughput-on-a-database). Aby rozwiązać ten problem, zaktualizuj rozszerzenie [Microsoft. Azure. WebJobs. Extensions. CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB) , aby uzyskać najnowszą wersję.

### <a name="azure-function-fails-to-start-with-the-lease-collection-if-partitioned-must-have-partition-key-equal-to-id"></a>Nie można uruchomić funkcji platformy Azure z "kolekcją dzierżawy, jeśli partycjonowany, musi mieć klucz partycji równy identyfikatorowi".

Ten błąd oznacza, że bieżący kontener dzierżawy jest podzielony na partycje, ale Ścieżka klucza partycji nie `/id`jest. Aby rozwiązać ten problem, należy ponownie utworzyć kontener dzierżawy za pomocą `/id` klucza partycji.

### <a name="you-see-a-value-cannot-be-null-parameter-name-o-in-your-azure-functions-logs-when-you-try-to-run-the-trigger"></a>Zobaczysz "wartość nie może być równa null. Nazwa parametru: o "w dziennikach Azure Functions podczas próby uruchomienia wyzwalacza

Ten problem pojawia się, jeśli używasz Azure Portal i próbujesz wybrać przycisk **Uruchom** na ekranie podczas inspekcji funkcji platformy Azure, która używa wyzwalacza. Wyzwalacz nie wymaga wybrania opcji Uruchom do uruchomienia, zostanie automatycznie uruchomiony po wdrożeniu funkcji platformy Azure. Jeśli chcesz sprawdzić strumień dzienników funkcji platformy Azure na Azure Portal, po prostu przejdź do monitorowanego kontenera i Wstaw nowe elementy, zostanie automatycznie wyświetlony wyzwalacz.

### <a name="my-changes-take-too-long-be-received"></a>Odbieranie zmian trwa zbyt długo

Ten scenariusz może mieć wiele przyczyn i należy sprawdzić wszystkie z nich:

1. Czy funkcja platformy Azure została wdrożona w tym samym regionie, w którym znajduje się konto usługi Azure Cosmos? W celu uzyskania optymalnego opóźnienia sieci zarówno funkcja platformy Azure, jak i konto usługi Azure Cosmos powinny znajdować się w tym samym regionie świadczenia usługi Azure.
2. Czy zmiany są wykonywane w kontenerze usługi Azure Cosmos w sposób ciągły lub sporadyczny?
Jeśli jest to ostatnie, może wystąpić pewne opóźnienie między zapisanymi zmianami a funkcją platformy Azure. Wynika to z faktu, że wewnętrznie, gdy wyzwalacz sprawdza zmiany w kontenerze usługi Azure Cosmos i nie oczekuje na odczytanie, będzie w stanie uśpienia przez konfigurowalny czas (domyślnie 5 sekund) przed sprawdzeniem nowych zmian (w celu uniknięcia użycia wysokiego poziomu RU). Można skonfigurować ten czas uśpienia za pomocą `FeedPollDelay/feedPollDelay` ustawienia w [konfiguracji](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration) wyzwalacza (wartość powinna być w milisekundach).
3. Kontener usługi Azure Cosmos może być [oceniany proporcjonalnie](./request-units.md).
4. Możesz użyć `PreferredLocations` atrybutu w wyzwalaczu, aby określić rozdzieloną przecinkami listę regionów świadczenia usługi Azure, aby zdefiniować niestandardową kolejność połączeń.

### <a name="some-changes-are-missing-in-my-trigger"></a>W moim wyzwalaczu brakuje niektórych zmian

Jeśli okaże się, że niektóre zmiany, które wystąpiły w kontenerze usługi Azure Cosmos, nie są wybierane przez funkcję systemu Azure, istnieje etap wstępnego badania, który musi zostać przeprowadzona.

Gdy funkcja platformy Azure otrzymuje zmiany, często przetwarza je i może opcjonalnie wysłać wynik do innego miejsca docelowego. Podczas badania brakujących zmian upewnij się, że **miary są odbierane w punkcie** pozyskiwania (gdy funkcja platformy Azure zostanie uruchomiona), a nie w miejscu docelowym.

Jeśli w miejscu docelowym brakuje niektórych zmian, może to oznaczać, że wystąpił błąd podczas wykonywania funkcji platformy Azure po odebraniu zmian.

W tym scenariuszu najlepszym sposobem działania jest dodanie `try/catch blocks` w kodzie i wewnątrz pętli, które mogą przetwarzać zmiany, wykrycie wszelkich błędów dla określonego podzestawu elementów i ich odpowiednie obsłużenie (wysłanie ich do innego magazynu Analiza lub Ponów próbę). 

> [!NOTE]
> Domyślnie wyzwalacz Azure Cosmos DB nie będzie ponawiać próby wykonania partii zmian, jeśli wystąpił nieobsługiwany wyjątek podczas wykonywania kodu. Oznacza to, że powodem, że zmiany nie dotarły do lokalizacji docelowej, jest to, że nie można ich przetworzyć.

Jeśli okaże się, że niektóre zmiany nie zostały odebrane w ogóle przez wyzwalacz, najbardziej typowym scenariuszem jest **uruchomienie innej funkcji platformy Azure**. Może to być inna funkcja platformy Azure wdrożona na platformie Azure lub funkcja platformy Azure działająca lokalnie na komputerze dewelopera, który ma **dokładnie taką samą konfigurację** (te same kontenery monitorowane i dzierżawy), a ta funkcja platformy Azure służy do kradzieży podzbioru zmian, które oczekuje, że funkcja platformy Azure będzie przetwarzać.

Ponadto można sprawdzić poprawność scenariusza, Jeśli wiesz, ile wystąpień aplikacja funkcji platformy Azure jest uruchomionych. Jeśli sprawdzisz kontener dzierżaw i policzesz liczbę elementów dzierżawy w ramach, różne wartości `Owner` właściwości w nich powinny być równe liczbie wystąpień aplikacja funkcji. Jeśli istnieje więcej właścicieli niż znane wystąpienia usługi Azure aplikacja funkcji, oznacza to, że Ci dodatkowi właściciele są "kradzieżą" zmian.

Aby obejść ten problem, należy zastosować `LeaseCollectionPrefix/leaseCollectionPrefix` do funkcji nową/inną wartość lub alternatywnie test z nowym kontenerem dzierżawy.

### <a name="binding-can-only-be-done-with-ireadonlylistdocument-or-jarray"></a>Powiązanie można wykonać tylko przy użyciu dokumentu\<IReadOnlyList > lub JArray

Ten błąd występuje, jeśli projekt Azure Functions (lub dowolny projekt, do którego istnieje odwołanie) zawiera ręczne odwołanie NuGet do zestawu Azure Cosmos DB SDK z inną wersją niż podana przez [rozszerzenie Azure Functions Cosmos DB](./troubleshoot-changefeed-functions.md#dependencies).

Aby obejść tę sytuację, Usuń ręczne odwołanie do narzędzia NuGet, które zostało dodane i pozwól, aby Azure Cosmos DB odwołanie do zestawu SDK przez pakiet rozszerzenia Cosmos DB Azure Functions.

## <a name="next-steps"></a>Następne kroki

* [Włącz monitorowanie dla Azure Functions](../azure-functions/functions-monitoring.md)
* [Rozwiązywanie problemów z Azure Cosmos DB .NET SDK](./troubleshoot-dot-net-sdk.md)
