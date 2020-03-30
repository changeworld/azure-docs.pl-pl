---
title: Zmienianie biblioteki procesorów kanałów informacyjnych w usłudze Azure Cosmos DB
description: Dowiedz się, jak używać biblioteki procesorów danych strumieniowych usługi Azure Cosmos DB do odczytywania pliku danych o zmianach składników procesora pliku danych zmian
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 12/03/2019
ms.reviewer: sngun
ms.openlocfilehash: e71b2807595aebeb1f0c8682fde119f4e267e55d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273303"
---
# <a name="change-feed-processor-in-azure-cosmos-db"></a>Procesor zestawienia zmian w usłudze Azure Cosmos DB 

Procesor pliku danych zmian jest częścią [usługi Azure Cosmos DB SDK V3](https://github.com/Azure/azure-cosmos-dotnet-v3). Upraszcza proces odczytywania pliku danych o zmianach i skutecznie rozdziela przetwarzanie zdarzeń wśród wielu konsumentów.

Główną zaletą biblioteki procesora kanału informacyjnego zmian jest jego zachowanie odporne na uszkodzenia, które zapewnia "co najmniej raz" dostarczanie wszystkich zdarzeń w pliku danych o zmianach.

## <a name="components-of-the-change-feed-processor"></a>Składniki procesora podawania zmian

Istnieją cztery główne składniki wdrażania procesora podawania zmian: 

1. **Monitorowany pojemnik:** Monitorowany kontener zawiera dane, z których jest generowany kanał informacyjny zmian. Wszelkie wstawia i aktualizacje monitorowanego kontenera są odzwierciedlane w posuwu zmian kontenera.

1. **Kontener dzierżawy:** Kontener dzierżawy działa jako magazyn stanu i współrzędnych przetwarzania źródła danych zmian przez wielu pracowników. Kontener dzierżawy może być przechowywany na tym samym koncie co monitorowany kontener lub na osobnym koncie. 

1. **Gospodarz:** Host jest wystąpieniem aplikacji, które używa procesora pliku danych zmian do nasłuchiwać zmian. Wiele wystąpień z tą samą konfiguracją dzierżawy może działać równolegle, ale każde wystąpienie powinno mieć inną **nazwę wystąpienia**. 

1. **Delegat:** Pełnomocnik jest kod, który definiuje, co ty, deweloper, chcesz zrobić z każdej partii zmian, które odczytuje procesor pliku danych zmian. 

Aby lepiej zrozumieć, jak te cztery elementy procesora pliku danych zmian współpracują ze sobą, przyjrzyjmy się przykładowi na poniższym diagramie. Monitorowany kontener przechowuje dokumenty i używa "Miasta" jako klucza partycji. Widzimy, że wartości klucza partycji są dystrybuowane w zakresach, które zawierają elementy. Istnieją dwa wystąpienia hosta i procesor pliku danych zmian przypisuje różne zakresy wartości klucza partycji do każdego wystąpienia, aby zmaksymalizować dystrybucję obliczeń. Każdy zakres jest odczytywany równolegle, a jego postęp jest utrzymywany oddzielnie od innych zakresów w kontenerze dzierżawy.

![Zmienianie przykładu procesora danych](./media/change-feed-processor/changefeedprocessor.png)

## <a name="implementing-the-change-feed-processor"></a>Implementowanie procesora pliku danych zmian

Punktem wejścia jest zawsze monitorowany kontener `Container` z `GetChangeFeedProcessorBuilder`wywoływanego wystąpienia:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-change-feed-processor/src/Program.cs?name=DefineProcessor)]

Gdzie pierwszy parametr jest odrębną nazwą, która opisuje cel tego procesora, a druga nazwa jest implementacją delegata, która będzie obsługiwać zmiany. 

Przykładem delegata jest:


[!code-csharp[Main](~/samples-cosmosdb-dotnet-change-feed-processor/src/Program.cs?name=Delegate)]

Na koniec można zdefiniować nazwę `WithInstanceName` dla tego wystąpienia procesora z `WithLeaseContainer`i który jest kontenerem do utrzymania stanu dzierżawy z .

Wywołanie `Build` da Ci wystąpienie procesora, `StartAsync`które można rozpocząć od wywołania .

## <a name="processing-life-cycle"></a>Cykl życia przetwarzania

Normalny cykl życia wystąpienia hosta jest:

1. Przeczytaj kanał zmian.
1. Jeśli nie ma żadnych zmian, uśpij przez wstępnie zdefiniowaną `WithPollInterval` ilość czasu (można dostosowywać za pomocą w Konstruktorze) i przejdź do #1.
1. Jeśli są zmiany, wyślij je do **pełnomocnika**.
1. Po pomyślnym zakończeniu przetwarzania **zmian**przez pełnomocnika zaktualizuj magazyn dzierżawy o ostatni przetworzony punkt w czasie i przejdź do #1.

## <a name="error-handling"></a>Obsługa błędów

Procesor pliku danych zmian jest odporny na błędy kodu użytkownika. Oznacza to, że jeśli implementacja delegata ma nieobsługiwany wyjątek (krok #4), przetwarzanie wątku, że określona partia zmian zostanie zatrzymana i zostanie utworzony nowy wątek. Nowy wątek sprawdzi, który był ostatni punkt w czasie magazynu dzierżawy ma dla tego zakresu wartości klucza partycji i uruchom ponownie stamtąd, skutecznie wysyłając tę samą partię zmian do delegata. To zachowanie będzie kontynuowane, dopóki delegat nie przetwarza zmian poprawnie i jest to powód, dla którego procesor pliku danych zmian ma gwarancję "co najmniej raz", ponieważ jeśli błądzami kodu delegata, ponowi próbę tej partii.

## <a name="dynamic-scaling"></a>Dynamiczne skalowanie

Jak wspomniano podczas wprowadzenia, procesor pliku danych zmian może automatycznie dystrybuować obliczenia między wieloma wystąpieniami. Można wdrożyć wiele wystąpień aplikacji przy użyciu procesora pliku danych zmian i skorzystać z niego, tylko kluczowe wymagania to:

1. Wszystkie wystąpienia powinny mieć taką samą konfigurację kontenera dzierżawy.
1. Wszystkie wystąpienia powinny mieć taką samą nazwę przepływu pracy.
1. Każde wystąpienie musi mieć inną`WithInstanceName`nazwę wystąpienia ( ).

Jeśli te trzy warunki mają zastosowanie, procesor pliku danych zmiany, przy użyciu algorytmu równej dystrybucji, rozprowadza wszystkie dzierżawy w kontenerze dzierżawy we wszystkich uruchomionych wystąpieniach i zrówna się z obliczeniami. Jedna dzierżawa może być własnością tylko jednego wystąpienia w danym czasie, więc maksymalna liczba wystąpień jest równa liczbie dzierżaw.

Liczba wystąpień może rosnąć i zmniejszać się, a procesor pliku danych zmian będzie dynamicznie dostosowywać obciążenie przez odpowiednie redystrybucja.

Ponadto procesor źródła danych zmian może dynamicznie dostosowywać się do skalowania kontenerów ze względu na zwiększenie przepustowości lub magazynu. Gdy kontener rośnie, procesor pliku danych zmian w sposób przejrzysty obsługuje te scenariusze, dynamicznie zwiększając dzierżawy i rozprowadzając nowe dzierżawy między istniejącymi wystąpieniami.

## <a name="change-feed-and-provisioned-throughput"></a>Zmienianie źródła danych i aprowizowana przepływność

Opłaty są naliczane za używane procesory RU, ponieważ przenoszenie danych do i z kontenerów usługi Cosmos zawsze zużywa procesory RU. Opłaty są naliczane za opłaty za użycie przez kontener dzierżawy.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [Przykłady użycia w usłudze GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [Dodatkowe przykłady w usłudze GitHub](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>Następne kroki

Teraz możesz dowiedzieć się więcej o procesorze pliku danych zmian w następujących artykułach:

* [Omówienie kanału informacyjnego zmian](change-feed.md)
* [Jak przeprowadzić migrację z biblioteki procesora kanału informacyjnego zmian](how-to-migrate-from-change-feed-library.md)
* [Korzystanie z estymatora zestawienia zmian](how-to-use-change-feed-estimator.md)
* [Czas rozpoczęcia procesora zestawienia zmian](how-to-configure-change-feed-start-time.md)
