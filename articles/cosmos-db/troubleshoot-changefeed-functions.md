---
title: Rozwiązywanie problemów podczas korzystania z wyzwalacza usługi Azure Functions dla usługi Cosmos DB
description: Typowe problemy, obejścia i kroki diagnostyczne podczas korzystania z wyzwalacza usługi Azure Functions dla usługi Cosmos DB
author: ealsur
ms.service: cosmos-db
ms.date: 03/13/2020
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 7bf7d418e3f2680b32f61e42cffc76c921068508
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79365512"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-functions-trigger-for-cosmos-db"></a>Diagnozowanie i rozwiązywanie problemów podczas korzystania z wyzwalacza usługi Azure Functions dla usługi Cosmos DB

W tym artykule omówiono typowe problemy, obejścia i kroki diagnostyczne podczas korzystania z [wyzwalacza usługi Azure Functions dla usługi Cosmos DB](change-feed-functions.md).

## <a name="dependencies"></a>Zależności

Wyzwalacz usług Azure Functions i powiązania dla usługi Cosmos DB zależą od pakietów rozszerzeń za pomocą podstawowego środowiska uruchomieniowego usługi Azure Functions. Zawsze aktualizuj te pakiety, ponieważ mogą one zawierać poprawki i nowe funkcje, które mogą rozwiązać potencjalne problemy, które mogą wystąpić:

* W przypadku usług Azure Functions V2 zobacz [Microsoft.Azure.WebJobs.Extensions.CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB).
* W przypadku usług Azure Functions V1 zobacz [Microsoft.Azure.WebJobs.Extensions.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB).

Ten artykuł będzie zawsze odwoływać się do usługi Azure Functions V2, gdy środowisko uruchomieniowe jest wymieniony, chyba że jawnie określono.

## <a name="consume-the-azure-cosmos-db-sdk-independently"></a>Korzystanie z niezależnego korzystania z zestawu SDK usługi Azure Cosmos DB

Kluczową funkcjonalnością pakietu rozszerzenia jest zapewnienie obsługi wyzwalacza usług Azure Functions i powiązań dla usługi Cosmos DB. Zawiera również [azure cosmos DB .NET SDK](sql-api-sdk-dotnet-core.md), co jest przydatne, jeśli chcesz współdziałać z usługi Azure Cosmos DB programowo bez użycia wyzwalacza i powiązań.

Jeśli chcesz użyć zestawu SDK usługi Azure Cosmos DB, upewnij się, że nie dodajesz do projektu innego odwołania do pakietu NuGet. Zamiast tego **pozwól, aby odwołanie do zestawu SDK było rozpoznawane za pośrednictwem pakietu rozszerzenia usługi Azure Functions.** Korzystanie z sdk usługi Azure Cosmos DB oddzielnie od wyzwalacza i powiązań

Ponadto jeśli ręcznie tworzysz własne wystąpienie [klienta SDK usługi Azure Cosmos DB,](./sql-api-sdk-dotnet-core.md)należy postępować zgodnie ze wzorcem posiadania tylko jednego wystąpienia klienta [przy użyciu metody wzorca Singleton.](../azure-functions/manage-connections.md#documentclient-code-example-c) Ten proces pozwoli uniknąć potencjalnych problemów z gniazdem w operacjach.

## <a name="common-scenarios-and-workarounds"></a>Typowe scenariusze i obejścia

### <a name="azure-function-fails-with-error-message-collection-doesnt-exist"></a>Funkcja platformy Azure kończy się niepowodzeniem z zbieraniem komunikatów o błędach nie istnieje

Usługa Azure Function kończy się niepowodzeniem z komunikatem o błędzie "Kolekcja źródłowa 'collection-name' (w bazie danych 'database-name') lub kolekcja dzierżawy 'collection2-name' (w bazie danych 'database2-name') nie istnieje. Obie kolekcje muszą istnieć przed uruchomieniem odbiornika. Aby automatycznie utworzyć kolekcję dzierżawy, ustaw "CreateLeaseCollectionIfNotExists" na "true""

Oznacza to, że jeden lub oba kontenery usługi Azure Cosmos wymagane do uruchomienia wyzwalacza nie istnieją lub nie są osiągalne do funkcji platformy Azure. **Sam błąd powie, która baza danych i kontener usługi Azure Cosmos jest wyzwalaczem szukającym** na podstawie konfiguracji.

1. Sprawdź `ConnectionStringSetting` atrybut i czy odwołuje się **on do ustawienia, które istnieje w aplikacji funkcji platformy Azure**. Wartość tego atrybutu nie powinna być sama ciąg połączenia, ale nazwa ustawienia konfiguracji.
2. Sprawdź, `databaseName` czy `collectionName` i istnieje na koncie usługi Azure Cosmos. Jeśli używasz automatycznego zastępowania wartości (przy użyciu `%settingName%` wzorców), upewnij się, że nazwa ustawienia istnieje w aplikacji funkcji platformy Azure.
3. Jeśli nie określisz `LeaseCollectionName/leaseCollectionName`, domyślnie jest to "dzierżawy". Sprawdź, czy taki kontener istnieje. Opcjonalnie można ustawić `CreateLeaseCollectionIfNotExists` atrybut w wyzwalaczu, aby `true` automatycznie go utworzyć.
4. Sprawdź [konfigurację zapory konta usługi Azure Cosmos,](how-to-configure-firewall.md) aby zobaczyć, czy nie blokuje funkcji platformy Azure.

### <a name="azure-function-fails-to-start-with-shared-throughput-collection-should-have-a-partition-key"></a>Funkcja platformy Azure nie uruchamia się od "Zbieranie przepływności udostępnionej powinno mieć klucz partycji"

Poprzednie wersje rozszerzenia bazy danych usługi Azure Cosmos nie obsługiwały kontenera dzierżawy utworzonego w [bazie danych przepływności udostępnionej.](./set-throughput.md#set-throughput-on-a-database) Aby rozwiązać ten problem, zaktualizuj rozszerzenie [Microsoft.Azure.WebJobs.Extensions.CosmosDB,](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB) aby uzyskać najnowszą wersję.

### <a name="azure-function-fails-to-start-with-partitionkey-must-be-supplied-for-this-operation"></a>Funkcja platformy Azure nie może rozpocząć się od "PartitionKey musi być dostarczony dla tej operacji."

Ten błąd oznacza, że obecnie używasz kolekcji dzierżawy podzielonej na partycje ze starą [zależnością rozszerzenia](#dependencies). Uaktualnienie do najnowszej dostępnej wersji. Jeśli korzystasz obecnie z usługi Azure Functions V1, musisz uaktualnić do usługi Azure Functions V2.

### <a name="azure-function-fails-to-start-with-the-lease-collection-if-partitioned-must-have-partition-key-equal-to-id"></a>Funkcja platformy Azure nie uruchamia się od "Kolekcja dzierżawy, jeśli jest podzielona na partycje, musi mieć klucz partycji równy identyfikatorowi."

Ten błąd oznacza, że bieżący kontener dzierżaw jest podzielony `/id`na partycje, ale ścieżka klucza partycji nie jest . Aby rozwiązać ten problem, należy ponownie utworzyć kontener `/id` dzierżawy z kluczem partycji.

### <a name="you-see-a-value-cannot-be-null-parameter-name-o-in-your-azure-functions-logs-when-you-try-to-run-the-trigger"></a>Zostanie wyświetlony "Wartość nie może być null. Nazwa parametru: o" w dziennikach usługi Azure Functions podczas próby uruchomienia wyzwalacza

Ten problem pojawia się, jeśli używasz witryny Azure portal i spróbuj wybrać przycisk **Uruchom** na ekranie podczas sprawdzania funkcji platformy Azure, która używa wyzwalacza. Wyzwalacz nie wymaga, aby wybrać Uruchom, aby uruchomić, zostanie automatycznie uruchomiony po wdrożeniu funkcji platformy Azure. Jeśli chcesz sprawdzić strumień dziennika usługi Azure Function w witrynie Azure portal, po prostu przejdź do monitorowanego kontenera i wstaw niektóre nowe elementy, automatycznie zobaczysz wyzwalacz wykonywania.

### <a name="my-changes-take-too-long-to-be-received"></a>Moje zmiany potrwają zbyt długo, aby otrzymać

Ten scenariusz może mieć wiele przyczyn i wszystkie z nich powinny być sprawdzane:

1. Czy funkcja platformy Azure została wdrożona w tym samym regionie co konto usługi Azure Cosmos? W celu uzyskania optymalnego opóźnienia sieci zarówno funkcja platformy Azure, jak i konto usługi Azure Cosmos powinny znajdować się w tym samym regionie świadczenia usługi Azure.
2. Czy zmiany pojawiają się w kontenerze usługi Azure Cosmos w sposób ciągły czy sporadyczny?
Jeśli w ten drugi sposób, może wystąpić pewne opóźnienie między zapisywaniem zmian a ich pobieraniem przez funkcję platformy Azure. Wynika to z faktu, że wewnętrznie, gdy wyzwalacz sprawdza istnienie zmian w kontenerze usługi Azure Cosmos i nie znajduje żadnych oczekujących na odczytanie, będzie w stanie uśpienia przez konfigurowalny czas (domyślnie 5 sekund) przed sprawdzeniem istnienia nowych zmian (w celu uniknięcia wysokiego użycia jednostek żądań). Ten czas uśpienia można skonfigurować za pomocą ustawienia `FeedPollDelay/feedPollDelay` w [konfiguracji](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) wyzwalacza (oczekiwana jest wartość w milisekundach).
3. Twój kontener usługi Azure Cosmos może być [ograniczony stawką.](./request-units.md)
4. Za pomocą `PreferredLocations` atrybutu w wyzwalaczu można określić listę regionów platformy Azure oddzieloną przecinkami w celu zdefiniowania niestandardowej preferowanej kolejności połączeń.

### <a name="some-changes-are-repeated-in-my-trigger"></a>Niektóre zmiany są powtarzane w moim wyzwalaczu

Pojęcie "zmiany" jest operacją na dokumencie. Najbardziej typowe scenariusze, w których są odbierane zdarzenia dla tego samego dokumentu, to:
* Konto używa spójność ostateczna. Podczas korzystania z pliku danych o zmianie na poziomie spójność ostateczna, może istnieć zduplikowane zdarzenia między kolejnymi operacjami odczytu kanału informacyjnego zmiany (ostatnie zdarzenie jednej operacji odczytu pojawia się jako pierwsza z następnych).
* Dokument jest aktualizowany. Plik danych może zawierać wiele operacji dla tych samych dokumentów, jeśli ten dokument otrzymuje aktualizacje, może odebrać wiele zdarzeń (po jednym dla każdej aktualizacji). Jednym z łatwych sposobów rozróżniania różnych `_lsn` operacji dla tego samego dokumentu jest śledzenie [właściwości dla każdej zmiany.](change-feed.md#change-feed-and-_etag-_lsn-or-_ts) Jeśli nie są zgodne, są to różne zmiany w tym samym dokumencie.
* Jeśli identyfikujesz dokumenty tylko `id`przez , należy pamiętać, że `id` unikatowy identyfikator dokumentu jest i jego `id` klucz partycji (mogą istnieć dwa dokumenty z tym samym, ale inny klucz partycji).

### <a name="some-changes-are-missing-in-my-trigger"></a>Brakuje niektórych zmian w moim wyzwalaczu

Jeśli okaże się, że niektóre zmiany, które miały miejsce w kontenerze usługi Azure Cosmos nie są pobierane przez funkcję platformy Azure, istnieje początkowy krok dochodzenia, który musi mieć miejsce.

Gdy funkcja platformy Azure odbiera zmiany, często przetwarza je i opcjonalnie może wysłać wynik do innego miejsca docelowego. Podczas badania brakujących zmian, upewnij się, **że pomiar, które zmiany są odbierane w punkcie pozyskiwania** (po uruchomieniu funkcji platformy Azure), a nie w miejscu docelowym.

Jeśli brakuje niektórych zmian w miejscu docelowym, może to oznaczać, że występuje pewien błąd podczas wykonywania funkcji platformy Azure po odebraniu zmian.

W tym scenariuszu najlepszym sposobem działania `try/catch` jest dodanie bloków w kodzie i wewnątrz pętli, które mogą być przetwarzania zmian, aby wykryć wszelkie błędy dla określonego podzbioru elementów i obsługiwać je odpowiednio (wysłać je do innego magazynu do dalszej analizy lub ponowić próbę). 

> [!NOTE]
> Wyzwalacz usługi Azure Functions dla usługi Cosmos DB domyślnie nie będzie  ponawiać próby przetworzenia partii zmian, jeśli wystąpił nieobsługiwany wyjątek podczas wykonywania kodu. Oznacza to, że powodem, dla którego zmiany nie dotarły do miejsca docelowego, jest to, że nie można ich przetworzyć.

Jeśli okaże się, że niektóre zmiany nie zostały odebrane przez wyzwalacz, najbardziej typowym scenariuszem jest to, że istnieje **inna funkcja platformy Azure uruchomiona**. Może to być inna funkcja platformy Azure wdrożona na platformie Azure lub funkcja platformy Azure działająca lokalnie na komputerze dewelopera, który ma **dokładnie taką samą konfigurację** (te same kontenery monitorowane i dzierżawy), a ta funkcja platformy Azure kradnie podzbiór zmian, których można oczekiwać, że funkcja platformy Azure będzie przetwarzać.

Ponadto scenariusz może zostać zweryfikowany, jeśli wiesz, ile wystąpień aplikacji funkcji platformy Azure masz uruchomione. Jeśli inspekcji dzierżawy kontenera i zliczyć liczbę przedmiotów `Owner` dzierżawy w ramach, różne wartości właściwości w nich powinny być równe liczbie wystąpień aplikacji funkcji. Jeśli istnieje więcej właścicieli niż znane wystąpienia aplikacji funkcji platformy Azure, oznacza to, że ci dodatkowi właściciele to ci, którzy "kradną" zmiany.

Jednym z łatwych sposobów obejść tę `LeaseCollectionPrefix/leaseCollectionPrefix` sytuację, jest zastosowanie a do funkcji z nową/inną wartość lub, alternatywnie, test z nowym kontenerem dzierżawy.

### <a name="need-to-restart-and-reprocess-all-the-items-in-my-container-from-the-beginning"></a>Trzeba ponownie uruchomić i ponownie przetworzyć wszystkie elementy w moim kontenerze od początku 
Aby ponownie przetworzyć wszystkie elementy w kontenerze od początku:
1. Zatrzymaj swoją funkcję platformy Azure, jeśli jest aktualnie uruchomiona. 
1. Usuwanie dokumentów w kolekcji dzierżawy (lub usuwanie i ponowne tworzenie kolekcji dzierżawy, tak aby była pusta)
1. Ustaw [atrybut StartFromBeginning](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) CosmosDBTrigger w funkcji na true. 
1. Uruchom ponownie funkcję platformy Azure. Będzie teraz odczytywać i przetwarzać wszystkie zmiany od początku. 

Ustawienie [StartFromBeginning](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) do true powie funkcji platformy Azure, aby rozpocząć odczytywanie zmian od początku historii kolekcji zamiast bieżącego czasu. Działa to tylko wtedy, gdy nie ma już utworzonych dzierżaw (czyli dokumentów w kolekcji dzierżaw). Ustawienie tej właściwości na true, gdy istnieją dzierżawy już utworzone nie ma wpływu; w tym scenariuszu, gdy funkcja zostanie zatrzymana i ponownie uruchomiona, rozpocznie odczyt z ostatniego punktu kontrolnego, zgodnie z definicją w kolekcji dzierżaw. Aby ponownie przetworzyć od początku, wykonaj powyższe kroki 1-4.  

### <a name="binding-can-only-be-done-with-ireadonlylistdocument-or-jarray"></a>Powiązanie można wykonać tylko za\<pomocą dokumentu IReadOnlyList> lub JArray

Ten błąd występuje, jeśli projekt usługi Azure Functions (lub dowolny projekt, do którego istnieje odwołanie) zawiera ręczne odwołanie NuGet do sdk usługi Azure Cosmos DB w innej wersji niż ta dostarczona przez [rozszerzenie usługi Azure Functions Cosmos DB Extension.](./troubleshoot-changefeed-functions.md#dependencies)

Aby obejść tę sytuację, usuń ręczne odwołanie NuGet, które zostało dodane i pozwól odwołać się do zestawu SDK usługi Azure Cosmos DB za pośrednictwem pakietu rozszerzenia usługi Azure Functions Cosmos DB Extension.

### <a name="changing-azure-functions-polling-interval-for-the-detecting-changes"></a>Zmiana interwału sondowania funkcji platformy Azure w celu wykrycia zmian

Jak wyjaśniono wcześniej dla [Moje zmiany trwa zbyt długo, aby otrzymać,](./troubleshoot-changefeed-functions.md#my-changes-take-too-long-to-be-received)funkcja platformy Azure będzie w stanie uśpienia przez konfigurowalny czas (5 sekund, domyślnie) przed sprawdzeniem nowych zmian (w celu uniknięcia wysokiego zużycia RU). Ten czas uśpienia można skonfigurować za pomocą ustawienia `FeedPollDelay/feedPollDelay` w [konfiguracji](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) wyzwalacza (oczekiwana jest wartość w milisekundach).

## <a name="next-steps"></a>Następne kroki

* [Włącz monitorowanie funkcji platformy Azure](../azure-functions/functions-monitoring.md)
* [Rozwiązywanie problemów z zestawem SDK usługi Azure Cosmos DB .NET](./troubleshoot-dot-net-sdk.md)
