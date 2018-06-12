---
title: Unikatowy kluczy w usłudze Azure DB rozwiązania Cosmos | Dokumentacja firmy Microsoft
description: Dowiedz się, jak za pomocą unikatowy kluczy Azure DB rozwiązania Cosmos bazy danych.
services: cosmos-db
keywords: ograniczenia klucza UNIQUE, naruszenie ograniczenia klucza unique
author: rafats
manager: kfile
editor: monicar
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 03/21/2018
ms.author: rafats
ms.openlocfilehash: d12109efbb157b1e0c15b1a4c0d005fa98c44858
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35261104"
---
# <a name="unique-keys-in-azure-cosmos-db"></a>Unikatowy kluczy w usłudze Azure DB rozwiązania Cosmos

Unikatowy kluczy dostarczyć deweloperom możliwość dodawania warstwy integralność danych do ich bazy danych. Tworząc unikatowe zasady klucza po utworzeniu kontenera, zapewnienia unikatowości co najmniej jedna wartość na [klucza partycji](partition-data.md). Po utworzeniu kontenera z zasadą unikatowy kluczy uniemożliwia tworzenie nowych lub zaktualizowanych elementów z wartościami, które zduplikowane wartości określonego przez ograniczenia klucza unique.   

> [!NOTE]
> Unikatowe klucze są obsługiwane przez najnowsze wersje [.NET](sql-api-sdk-dotnet.md) i [.NET Core](sql-api-sdk-dotnet-core.md) SDK SQL i [API bazy danych MongoDB](mongodb-feature-support.md#unique-indexes). Tabela interfejsu API i interfejsu API programu Graph nie obsługują unikatowy kluczy w tej chwili. 
> 
>

## <a name="use-case"></a>Przypadek użycia

Na przykład Przyjrzyjmy się jak bazy danych użytkownika skojarzonego z [społecznościowych aplikacji](use-cases.md#web-and-mobile-applications) mogą korzystać ze miały unikatowe zasady klucza na adresy e-mail. Tworząc Unikatowy klucz adres e-mail użytkownika, upewnij się, każdy rekord ma unikatowy adres e-mail, a żadne nowe rekordy można tworzyć za pomocą adresów e-mail zduplikowane. 

Jeśli chcesz użytkownikom można było utworzyć wiele rekordów o takiej samej wiadomości e-mail adres, ale nie sam imię, nazwisko i adres e-mail, można dodać inne ścieżki do unikatowe zasady kluczy. Dlatego zamiast tworzenia Unikatowy klucz na podstawie adresu e-mail, można utworzyć unikatowy klucz jest kombinacją imię, nazwisko i adres e-mail. W takim przypadku każdy unikatowy kombinację trzech ścieżek jest dozwolone, więc baza danych może zawierać elementy, które mają następujące wartości ścieżki. Każdy z tych rekordów przejdzie unikatowy kluczy zasad.  

**Dozwolone wartości Unikatowy klucz firstName, lastName i poczty e-mail**

|Imię|Nazwisko|Adres e-mail|
|---|---|---|
|Gaby|Duperre|gaby@contoso.com |
|Gaby|Duperre|gaby@fabrikam.com|
|Ivanowi|Duperre|gaby@fabrikam.com|
|    |Duperre|gaby@fabrikam.com|
|    |       |gaby@fabraikam.com|

Przy próbie wstawienia inny rekord o dowolnej kombinacji wymienione w powyższej tabeli, użytkownik otrzyma komunikat o błędzie informujący, że ograniczenia klucza unique nie zostało spełnione. Kod błędu zwrócony z bazy danych Azure rozwiązania Cosmos jest "Zasób o określonym identyfikatorze lub nazwie już istnieje." lub "Zasób o określonym identyfikatorze, nazwy lub unikatowego indeksu już istnieje." 

## <a name="using-unique-keys"></a>Przy użyciu unikatowy kluczy

Unikatowy kluczy musi być zdefiniowana, jeśli ten kontener jest tworzony i zakres to unikatowy klucz klucza partycji. Aby utworzyć na poprzedniego przykładu, jeśli partycji na podstawie kodu pocztowego, może mieć rekordy z tabeli zduplikowany w każdej partycji.

Nie można zaktualizować istniejącego kontenera, aby używać unikatowy kluczy.

Po utworzeniu kontenera z zasadą unikatowy kluczy zasady nie można zmienić, o ile nie zostanie ponownie utworzona kontenera. Jeśli masz istniejące dane, które chcesz zaimplementować unikatowy kluczy, Utwórz nowy kontener, a następnie użyć narzędzia migracji danych do przenoszenia danych do nowego kontenera. Dla kontenerów SQL, użyj [narzędzia migracji danych](import-data.md). Dla bazy danych MongoDB kontenery, użyj [mongoimport.exe lub mongorestore.exe](mongodb-migrate.md).

Maksymalnie 16 wartościami ścieżki (na przykład /firstName, /lastName, /address/zipCode itp.) mogą zostać włączone w każdym Unikatowy klucz. 

Każdy unikatowy kluczy zasad mogą mieć maksymalnie 10 ograniczeń klucza unique lub kombinacji i połączone ścieżki dla wszystkich właściwości unikatowego indeksu nie może przekraczać 60 znaków. Tak poprzedniego przykładu, która używa imienia, nazwiska i adres e-mail jest tylko jedno ograniczenie i używa trzech 16 ścieżek możliwe dostępne. 

Żądanie jednostki opłaty za tworzenie, aktualizowanie, i usunięciu elementu są nieco większe, gdy istnieje unikatowe zasady klucza w kontenerze. 

Rozrzedzony unikatowy kluczy nie są obsługiwane. Brak wartości dla niektórych unikatowy ścieżki są traktowane jako specjalne wartość null, która uczestniczy w ograniczenie unikatowości.

## <a name="sql-api-sample"></a>Przykładowy interfejs API SQL

Poniższy przykładowy kod przedstawia sposób tworzenia nowego kontenera SQL z dwóch ograniczeń klucza unique. Pierwszy ograniczenie jest firstName, lastName, poczty e-mail ograniczenia opisane w poprzedniego przykładu. Drugi ograniczeniem jest to adres/kod pocztowy użytkowników. Przykładowy plik JSON używa ścieżki tych unikatowy kluczy zasad następuje przykładów kodu. 

```csharp
// Create a collection with two separate UniqueKeys, one compound key for /firstName, /lastName,
// and /email, and another for /address/zipCode.
private static async Task CreateCollectionIfNotExistsAsync(string dataBase, string collection)
{
    try
    {
        await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(dataBase, collection));
    }
    catch (DocumentClientException e)
    {
        if (e.StatusCode == System.Net.HttpStatusCode.NotFound)
        {
            DocumentCollection myCollection = new DocumentCollection();
            myCollection.Id = collection;
            myCollection.PartitionKey.Paths.Add("/pk");
            myCollection.UniqueKeyPolicy = new UniqueKeyPolicy
            {
                UniqueKeys =
                new Collection<UniqueKey>
                {
                    new UniqueKey { Paths = new Collection<string> { "/firstName" , "/lastName" , "/email" }}
                    new UniqueKey { Paths = new Collection<string> { "/address/zipcode" } },
          }
            };
            await client.CreateDocumentCollectionAsync(
                UriFactory.CreateDatabaseUri(dataBase),
                myCollection,
                new RequestOptions { OfferThroughput = 2500 });
        }
        else
        {
            throw;
        }
    }
```

Przykładowy dokument JSON.

```json
{
    "id": "1",
    "pk": "1234",
    "firstName": "Gaby",
    "lastName": "Duperre",
    "email": "gaby@contoso.com",
    "address": 
        {            
            "line1": "100 Some Street",
            "line2": "Unit 1",
            "city": "Seattle",
            "state": "WA",
            "zipcode": 98012
        }
    
}
```
> [!NOTE]
> Sprawdź notatki unikatowa nazwa klucza jest rozróżniana wielkość liter. Jak pokazano powyżej przykład, dla /address/zipcode ustawiono unikatową nazwę. Jeśli dane będzie kod pocztowy, następnie zostaną wstawione "null" w kluczu unikatowy kod pocztowy nie jest taki sam, jak kod pocztowy. I ze względu na to wielkości liter wszystkich rekordów z kod pocztowy nie będzie można ma zostać wstawiony jako zduplikowane wartości "null" spowodują naruszenie ograniczenia klucza unique.

## <a name="mongodb-api-sample"></a>Przykładowy interfejs API bazy danych MongoDB

W poniższym przykładzie polecenie pokazuje, jak można utworzyć indeksu unikatowego na imię, nazwisko i adres e-mail pola kolekcji użytkowników dla interfejsu API bazy danych MongoDB. Dzięki temu unikatowości dla kombinacji wszystkie trzy pola dla wszystkich dokumentów w kolekcji. Kolekcje API bazy danych MongoDB unikatowy indeks jest tworzony po utworzeniu kolekcji, ale przed kolekcję.

```
db.users.createIndex( { firstName: 1, lastName: 1, email: 1 }, { unique: true } )
```
## <a name="configure-unique-keys-by-using-azure-portal"></a>Skonfigurować unikatowy kluczy przy użyciu portalu Azure

W sekcjach powyżej, które znajdują się przykłady kodu, które będą widoczne, jak można zdefiniować ograniczenia klucza unique, gdy kolekcja jest tworzony przy użyciu interfejsu API SQL lub bazy danych MongoDB interfejsu API. Ale jest również możliwe określenie unikatowy kluczy podczas tworzenia kolekcji za pomocą interfejsu użytkownika sieci web w portalu Azure. 

- Przejdź do **Eksploratora danych** na koncie DB rozwiązania Cosmos
- Kliknij przycisk **nowej kolekcji**
- W sekcji unikatowy kluczy ** można dodać żądanego ograniczeń klucza unique, klikając **Dodaj Unikatowy klucz.**

![Zdefiniuj unikatowe klucze w Eksploratorze danych](./media/unique-keys/unique-keys-azure-portal.png)

- Jeśli chcesz utworzyć unikatowego ograniczenia klucza w ścieżce lastName, Dodaj `/lastName`.
- Jeśli chcesz utworzyć unikatowego ograniczenia klucza dla kombinacji firstName lastName, Dodaj `/lastName,/firstName`

Po zakończeniu kliknij przycisk **OK** do tworzenia kolekcji.

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób tworzenia unikatowy kluczy dla elementów w bazie danych. W przypadku tworzenia kontenera po raz pierwszy, przejrzyj [partycjonowanie danych w usłudze Azure DB rozwiązania Cosmos](partition-data.md) jako unikatowy kluczy i kluczy partycji zależne od siebie nawzajem. 


