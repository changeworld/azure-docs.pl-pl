---
title: Praca z zmiany źródła danych z biblioteką procesora w usłudze Azure Cosmos DB
description: Za pomocą zmian usługi Azure Cosmos DB kanału informacyjnego bibliotece procesora.
author: rimman
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 9cf9e1aabc0898ef025c7c2f517e631a812e67d7
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2019
ms.locfileid: "65969025"
---
# <a name="change-feed-processor-in-azure-cosmos-db"></a>Procesor kanału informacyjnego zmian w usłudze Azure Cosmos DB 

[Zmian usługi Azure Cosmos DB kanału informacyjnego w bibliotece procesora](sql-api-sdk-dotnet-changefeed.md) ułatwia dystrybucję przetwarzanie zdarzeń w wielu odbiorców. Ta biblioteka upraszcza zmiany odczytywania różnych partycji i wiele wątków działających równolegle.

Główną zaletą biblioteką procesora zestawienia zmian jest nie trzeba zarządzać każdej partycji i token kontynuacji i nie trzeba ręcznie sondować każdego kontenera.

Biblioteką procesora zestawienia zmian upraszcza zmiany odczytu w partycji i wiele wątków działających równolegle. Automatycznie zarządza odczytu zmiany na partycje przy użyciu mechanizmu dzierżawy. Jak widać na poniższej ilustracji, jeśli zaczniesz dwóch klientów, którzy korzystają z zestawienia bibliotece procesora zmian, ich podzielić pracę między sobą. W miarę postępu w celu zwiększenia liczby klientów mogą zachować podzielenie pracy między sobą.

![Za pomocą usługi Azure Cosmos DB zmiany źródła danych z biblioteką procesora](./media/change-feed-processor/change-feed-output.png)

Po lewej stronie klient został uruchomiony w pierwszej i jego uruchomienia, monitorowania, które wszystkie partycje, a następnie drugiego klienta została uruchomiona, a następnie pierwszy porzuciła niektóre dzierżawy do drugiego klienta. Jest to wydajny sposób rozpraszających między różnych komputerów i klientów.

Jeśli masz dwie funkcje platformy Azure bez serwera monitorowania tego samego kontenera i przy użyciu tej samej dzierżawy, te dwie funkcje może zostać różnymi dokumentami, w zależności od sposobu bibliotece procesora decyduje się na przetwarzanie partycji.

## <a name="implementing-the-change-feed-processor-library"></a>Wprowadzanie zmian źródła danych z biblioteką procesora

Istnieją cztery główne składniki wdrażania zestawienia bibliotece procesora zmian: 

1. **Monitorowane kontener:** Monitorowane kontener ma danych, z którego jest generowany zestawienia zmian. Wszystkie operacje wstawiania i zmiany do monitorowanych kontenera są odzwierciedlane w zestawienia zmian w kontenerze.

1. **Kontener dzierżawy:** Współrzędne kontenera dzierżawy przetwarzania zestawienia zmian na wielu procesów roboczych. Oddzielny kontener jest używany do przechowywania dzierżaw przy użyciu jednej dzierżawy dla każdej partycji. Jest lepiej jest przechowywać ten kontener dzierżawy na innym koncie z regionem zapisu bliżej na którym działa zestawienia procesora zmian. Obiekt dzierżawy zawiera następujące atrybuty:

   * Właściciel: Określa host, który jest właścicielem dzierżawy.

   * Kontynuacja: Określa położenie (token kontynuacji) Zmień źródło danych dla określonej partycji.

   * Sygnatura czasowa: Czas ostatniego dzierżawy została zaktualizowana; Sygnatura czasowa może służyć do sprawdzania, czy dzierżawa jest uznawany za wygasły.

1. **Host procesora:** Każdy host Określa, ile partycje do przetworzenia na podstawie liczby wystąpień hostów mają aktywne dzierżawy.

   * Podczas uruchamiania hosta, uzyskuje dzierżawę, aby zrównoważyć obciążenie na wszystkich hostach. Host okresowo odnowieniu dzierżawy, więc dzierżawy pozostają aktywne.

   * Punkty kontrolne hosta ostatni token kontynuacji do dzierżawy dla każdego do odczytu. Aby zapewnić bezpieczeństwo współbieżności, hosta sprawdza, czy element ETag dla każdej aktualizacji dzierżawy. Obsługiwane są również inne strategie punktu kontrolnego.

   * Podczas zamykania systemu host zwalnia wszystkie dzierżawy, ale zachowuje informacji kontynuacji, dzięki czemu może wznowić działanie czytania od przechowywanej punktu kontrolnego później.

   Obecnie liczby hostów nie może być większa niż liczba partycji (dzierżawy).

1. **Odbiorcy:** Użytkowników lub pracowników, są wątki, które wykonują przetwarzania zestawienia zmian inicjowane przez każdego hosta. Każdy host procesor może mieć wielu odbiorców. Każdy odbiorca odczytuje zmiany źródła danych z partycji, który jest przypisany do powiadamia o jego hosta zmiany i Wygasłe dzierżawy.

Aby jeszcze lepiej zrozumieć sposób tych czterech elementów kanału informacyjnego zmian pracy procesora ze sobą, Spójrzmy na przykład na poniższym diagramie. Monitorowane kolekcja przechowuje dokumenty i używa "Miejscowość" jako klucza partycji. Widzimy, że niebieski partycja zawiera dokumenty z polem "Miejscowość" od "A-E" i tak dalej. Istnieją dwa hosty, każdy z dwóch odbiorców podczas odczytywania z cztery partycje równolegle. Strzałki oznaczają odbiorców podczas odczytywania z określonego miejsca w zestawienia zmian. W pierwszej partycji niebieskim ciemniejsze reprezentuje nieprzeczytane zmiany, gdy jasnoniebieski reprezentuje zmiany już odczytu na kanał informacyjny zmian. Hosty używają kolekcję dzierżaw, do przechowywania wartości "kontynuacja", aby śledzić bieżącą pozycję odczytu dla każdego użytkownika.

![Przykład procesora zestawienia zmian](./media/change-feed-processor/changefeedprocessor.png)

### <a name="change-feed-and-provisioned-throughput"></a>Kanał informacyjny zmian i aprowizowanej przepływności

Opłaty są naliczane za RU, gdyż przenoszenie danych do i z kontenerów Cosmos zawsze wykorzystuje jednostki zarezerwowane. Opłaty są naliczane za ru zużywanych przez kontener dzierżawy.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Biblioteką procesora zestawienia zmian w usłudze Azure Cosmos DB](sql-api-sdk-dotnet-changefeed.md)
* [Pakiet nugGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)
* [Dodatkowe przykłady w witrynie GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/ChangeFeedProcessor)

## <a name="next-steps"></a>Kolejne kroki

Można teraz kontynuować, aby dowiedzieć się więcej na temat zmiany źródła danych w następujących artykułach:

* [Omówienie Kanał informacyjny zmian](change-feed.md)
* [Sposoby na odczytywanie zestawienia zmian](read-change-feed.md)
* [Za pomocą zmian źródła danych za pomocą usługi Azure Functions](change-feed-functions.md)
