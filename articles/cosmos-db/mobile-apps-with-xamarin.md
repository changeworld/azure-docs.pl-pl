---
title: Tworzenie aplikacji dla urządzeń przenośnych dzięki platformie Xamarin i usłudze Azure DB rozwiązania Cosmos | Dokumentacja firmy Microsoft
description: Samouczek, który tworzy Xamarin iOS, Android lub formularzy aplikacji za pomocą bazy danych Azure rozwiązania Cosmos. Azure DB rozwiązania Cosmos jest szybkie, planety skali, bazy danych chmury dla aplikacji mobilnych.
services: cosmos-db
documentationcenter: .net
author: arramac
manager: monicar
editor: ''
ms.assetid: ff97881a-b41a-499d-b7ab-4f394df0e153
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 11/15/2017
ms.author: arramac
ms.openlocfilehash: 0cbe9a8463e8b471e3e4b0b4403c3a5d01ed59a0
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="build-mobile-applications-with-xamarin-and-azure-cosmos-db"></a>Tworzenie aplikacji dla urządzeń przenośnych dzięki platformie Xamarin i usłudze Azure DB rozwiązania Cosmos

Aplikacje mobilne najbardziej potrzebne do przechowywania danych w chmurze i bazy danych Azure rozwiązania Cosmos jest bazą danych chmury dla aplikacji mobilnych. Zawiera wszystko, co jest wymagane przez dewelopera przenośnych. Pełni zarządzana baza danych jest jako usługa, która może obsłużyć na żądanie. Uruchamiać danych do aplikacji w sposób przezroczysty, wszędzie tam, gdzie użytkownicy znajdują się na całym świecie. Za pomocą [Azure rozwiązania Cosmos DB .NET Core SDK](sql-api-sdk-dotnet-core.md), można włączyć Xamarin aplikacji mobilnych na bezpośrednią interakcję z bazy danych z rozwiązania Cosmos platformy Azure, bez warstwy środkowej.

Ten artykuł zawiera samouczek tworzenia aplikacji mobilnych dzięki platformie Xamarin i usłudze Azure DB rozwiązania Cosmos. Kod źródłowy pełną można znaleźć w samouczku w [Xamarin i usłudze Azure DB rozwiązania Cosmos w serwisie GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin), łącznie ze sposobem zarządzania użytkownikami i uprawnienia.

## <a name="azure-cosmos-db-capabilities-for-mobile-apps"></a>Możliwości rozwiązania Cosmos bazy danych platformy Azure dla aplikacji mobilnych
Azure DB rozwiązania Cosmos oferuje następujące kluczowe funkcje dla deweloperów aplikacji mobilnych:

![Możliwości rozwiązania Cosmos bazy danych platformy Azure dla aplikacji mobilnych](media/mobile-apps-with-xamarin/documentdb-for-mobile.png)

* Zaawansowana zapytania dotyczące danych schematu. Azure DB rozwiązania Cosmos przechowuje dane jako schematu dokumentów JSON w kolekcjach heterogenicznych. Zapewnia ona [zapytania bogaty i szybkie](sql-api-sql-query.md) bez konieczności martwić się o schematów lub indeksów.
* Szybkie przepływności. Trwa tylko kilka milisekund do odczytywania i zapisywania dokumentów z bazy danych Azure rozwiązania Cosmos. Deweloperzy można określić przepustowość, które są im potrzebne i bazy danych Azure rozwiązania Cosmos honoruje go z umowy SLA dostępności 99,99% dla wszystkich kont w pojedynczym regionie i wszystkich kont w przypadku z swobodna spójności i 99,999% odczytu dostępności na wszystkich kontach w przypadku bazy danych .
* Skala nieograniczona. Kolekcji bazy danych Azure rozwiązania Cosmos [rosnąć w miarę wzrastania aplikacji](partition-data.md). Można uruchomić z rozmiar danych w małych i przepływności setki żądań na sekundę. Kolekcji odpowiednio do petabajtów danych i dowolnie dużą przepływność setki miliony żądań na sekundę.
* Globalnie rozproszone. Użytkownicy aplikacji mobilnej są w podróży, często całym świecie. Azure DB rozwiązania Cosmos jest [globalnie rozproszoną bazę danych](distribute-data-globally.md). Kliknij mapę, aby dane były dostępne dla użytkowników.
* Wbudowane sformatowanego autoryzacji. Z bazy danych rozwiązania Cosmos platformy Azure, można łatwo zaimplementować popularnych wzorców, takich jak [danych użytkownika](https://aka.ms/documentdb-xamarin-todouser) lub wielodostępnego udostępnionych danych bez kodu złożonych autoryzacji niestandardowej.
* Dane geograficzne zapytania. Wiele aplikacji mobilnych zapewniają kontekstowe geograficznie środowiska dzisiaj. O najwyższej jakości pomoc techniczną dla [typy dane geograficzne](geospatial.md), sprawia, że bazy danych Azure rozwiązania Cosmos tworzenie łatwe do wykonania tych środowisk.
* Załączników binarnych. Dane aplikacji zawierają często binarnego obiektów blob. Natywna obsługa załączników ułatwia używać bazy danych Azure rozwiązania Cosmos jako zaspokaja dla danych aplikacji.

## <a name="azure-cosmos-db-and-xamarin-tutorial"></a>Samouczek usługi Azure DB rozwiązania Cosmos i Xamarin
Następujące samouczek przedstawia sposób tworzenia aplikacji mobilnych za pomocą platformy Xamarin i usłudze Azure DB rozwiązania Cosmos. Kod źródłowy pełną można znaleźć w samouczku w [Xamarin i usłudze Azure DB rozwiązania Cosmos w serwisie GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin).

### <a name="get-started"></a>Rozpoczęcie pracy
To proste rozpocząć korzystanie z bazy danych Azure rozwiązania Cosmos. Przejdź do portalu Azure i Utwórz nowe konto bazy danych Azure rozwiązania Cosmos. Kliknij przycisk **szybki start** kartę. Pobierz przykładowe listy zadań do wykonania Xamarin Forms, który jest już połączony z kontem usługi Azure DB rozwiązania Cosmos. 

![Azure DB rozwiązania Cosmos — szybki start dla aplikacji mobilnych](media/mobile-apps-with-xamarin/cosmos-db-quickstart.png)

Lub jeśli masz istniejącą aplikację platformy Xamarin, możesz dodać [pakietu NuGet DB rozwiązania Cosmos Azure](sql-api-sdk-dotnet-core.md). Azure DB rozwiązania Cosmos obsługuje Xamarin.IOS, Xamarin.Android, i biblioteki udostępnione Xamarin Forms.

### <a name="work-with-data"></a>Praca z danymi
Rekordy danych są przechowywane w usłudze Azure DB rozwiązania Cosmos jako schematu dokumentów JSON w kolekcjach heterogenicznych. Dokumenty z różnych struktur można przechowywać w tej samej kolekcji:

```cs
    var result = await client.CreateDocumentAsync(collectionLink, todoItem);
```

W projektach Xamarin można użyć zapytania o języku zintegrowanym nad danymi schematu:

```cs
    var query = await client.CreateDocumentQuery<ToDoItem>(collectionLink)
                    .Where(todoItem => todoItem.Complete == false)
                    .AsDocumentQuery();

    Items = new List<TodoItem>();
    while (query.HasMoreResults) {
        Items.AddRange(await query.ExecuteNextAsync<TodoItem>());
    }
```
### <a name="add-users"></a>Dodawanie użytkowników
Jak uzyskać wiele próbek wprowadzenie, przykładowej bazy danych Azure rozwiązania Cosmos, pobrany uwierzytelnia się w usłudze przy użyciu ustalony klucza głównego w kodzie aplikacji. To ustawienie domyślne nie jest dobrym rozwiązaniem dla aplikacji, które zamierzasz uruchomić dowolnego miejsca, z wyjątkiem sieci lokalnej emulatora. Jeśli nieautoryzowany użytkownik uzyskany klucz główny, wszystkie dane na Twoje konto bazy danych Azure rozwiązania Cosmos może być zagrożone. Zamiast tego chcesz aplikacji na dostęp tylko do rekordów dla zalogowanego użytkownika. Azure DB rozwiązania Cosmos umożliwia deweloperom Udziel aplikacji odczytu lub odczytu/zapisu uprawnienia do kolekcji, zestaw dokumentów pogrupowane według klucza partycji lub określonego dokumentu. 

Wykonaj następujące kroki, aby zmodyfikować aplikację listy zadań do wykonania w celu aplikacja z listą zadań do wykonania wielu użytkowników: 

  1. Dodawanie logowania do aplikacji za pomocą usługi Facebook, usługi Active Directory lub innego dostawcy.

  2. Utwórz kolekcję usługi Azure rozwiązania Cosmos DB UserItems z **/userId** jako klucza partycji. Określanie klucza partycji kolekcji umożliwia Azure DB rozwiązania Cosmos skalowania nieograniczonej wraz z rozwojem liczbę użytkowników aplikacji, pozostawiając oferują szybki zapytania.

  3. Dodaj Token brokera zasobów Azure rozwiązania Cosmos bazy danych. Ten prosty interfejs API sieci Web uwierzytelnia użytkowników z tokenami krótkim okresie problemów do zalogowanych użytkowników z dostępem tylko do dokumentów w ich partycji. W tym przykładzie brokera tokenu zasobów znajduje się w usłudze App Service.

  4. Zmodyfikować aplikację do uwierzytelniania zasobu brokera tokenu z usługą Facebook, a żądania tokenów zasobów dla użytkowników usługi Facebook zalogowany. Można następnie dostęp do danych w kolekcji UserItems.  

Możesz znaleźć tego wzorca na przykład kompletny kod [zasobu brokera tokenu w serwisie GitHub](http://aka.ms/documentdb-xamarin-todouser). Ten diagram przedstawia rozwiązania:

![Azure DB rozwiązania Cosmos uprawnienia użytkowników i broker](media/mobile-apps-with-xamarin/documentdb-resource-token-broker.png)

Chcąc dwóch użytkownikom dostęp do tej samej listy zadań do wykonania, można dodać dodatkowe uprawnienia do tokena dostępu w brokerze Token zasobu.

### <a name="scale-on-demand"></a>Skalowanie na żądanie
Azure DB rozwiązania Cosmos jest zarządzany bazy danych jako usługa. Wraz z rozwojem bazy użytkowników, nie musisz martwić się o inicjowania obsługi administracyjnej maszyn wirtualnych lub zwiększenie rdzeni. Należy określić bazy danych Azure rozwiązania Cosmos jest liczbę operacji na sekundę (przepływność) Twoja aplikacja powinna. Można określić przepustowość za pośrednictwem **skali** kartę za pomocą miara przepływności wywołuje jednostek żądania (RUs) na sekundę. Operacja odczytu dokumentu 1 KB wymaga na przykład 1 RU. Możesz także dodać alerty **przepływności** metrykę, aby monitorować wzrost ruchu i programowo zmienić przepływności jak alerty fire.

![Azure DB rozwiązania Cosmos skali przepływności na żądanie](media/mobile-apps-with-xamarin/cosmos-db-xamarin-scale.png)

### <a name="go-planet-scale"></a>Przejdź planety skali
Jako Twojej aplikacji popularne zysków użytkownicy mogą uzyskać na całym świecie. Lub może być na tyle nieprzewidzianych. Przejdź do portalu Azure, a następnie otwórz konto bazy danych Azure rozwiązania Cosmos. Kliknij mapę, aby dane replikacji ciągłej do dowolnej liczby regionów całym świecie. Ta funkcja udostępnia dane wszędzie tam, gdzie są użytkownicy. Można również dodać zasady trybu failover należy przygotować warunkowych.

![Azure skali DB rozwiązania Cosmos w regionach geograficznych](media/mobile-apps-with-xamarin/cosmos-db-xamarin-replicate.png)

Gratulacje. Ukończono rozwiązania i aplikacji mobilnej dzięki platformie Xamarin i usłudze Azure DB rozwiązania Cosmos. Należy wykonać podobne kroki w celu tworzenia aplikacji oprogramowania Cordova przy użyciu zestawu SDK usługi Azure rozwiązania Cosmos DB JavaScript i natywne aplikacje dla systemu iOS/Android przy użyciu interfejsów API REST usługi Azure rozwiązania Cosmos bazy danych.

## <a name="next-steps"></a>Kolejne kroki
* Wyświetl kod źródłowy [Xamarin i usłudze Azure DB rozwiązania Cosmos w serwisie GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin).
* Pobierz [rozwiązania Cosmos Azure DB .NET Core SDK](sql-api-sdk-dotnet-core.md).
* Znajdź więcej przykładów kodu dla [aplikacji .NET](sql-api-dotnet-samples.md).
* Dowiedz się więcej o [sformatowanego bazy danych Azure rozwiązania Cosmos zapytania możliwości](sql-api-sql-query.md).
* Dowiedz się więcej o [Obsługa dane geograficzne w usłudze Azure DB rozwiązania Cosmos](geospatial.md).



