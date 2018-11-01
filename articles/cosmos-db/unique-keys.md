---
title: Unikatowe klucze w usłudze Azure Cosmos DB | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać unikatowe klucze w bazie danych Azure Cosmos DB.
services: cosmos-db
keywords: unikatowe ograniczenie klucza naruszenia unikatowego ograniczenia klucza
author: rafats
manager: kfile
editor: monicar
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 08/08/2018
ms.author: rafats
ms.openlocfilehash: ff432de59e5a5fdfeaad4c3a5361554ee32e21b0
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/01/2018
ms.locfileid: "50740012"
---
# <a name="unique-keys-in-azure-cosmos-db"></a>Unikatowe klucze w usłudze Azure Cosmos DB

Unikatowe klucze umożliwiają deweloperom dodanie warstwy integralności danych do ich bazy danych. Tworząc zasady unikatowych kluczy podczas tworzenia kontenera, możesz zapewnić unikatowość co najmniej jedną wartość na [klucza partycji](partition-data.md). Po utworzeniu kontenera za pomocą zasady unikatowych kluczy uniemożliwia tworzenie nowych lub zaktualizowanych elementów z wartościami, które duplikują wartości określonych przez unikatowe ograniczenie klucza.   

> [!NOTE]
> Unikatowe klucze są obsługiwane przez najnowsze wersje [.NET](sql-api-sdk-dotnet.md) i [platformy .NET Core](sql-api-sdk-dotnet-core.md) zestawy SQL SDK i [interfejsu API usługi MongoDB](mongodb-feature-support.md#unique-indexes). Interfejs API tabel i interfejs API Gremlin nie obsługują unikatowe klucze w tej chwili. 
> 
>

## <a name="use-case"></a>Przypadek użycia

Na przykład Przyjrzyjmy się jak bazy danych użytkownika skojarzony z [aplikacji społecznościowych](use-cases.md#web-and-mobile-applications) mogą odnieść korzyści z mających zasady unikatowych kluczy na adresy e-mail. Podejmując Unikatowy klucz adres e-mail użytkownika, upewnij się, każdy rekord zawiera unikatowego adresu e-mail, a nie nowe rekordy mogą być tworzone za pomocą zduplikowane adresy e-mail. 

Jeśli użytkowników, aby można było utworzyć wiele rekordów o takiej samej adresu e-mail, ale nie sam imię, nazwisko i adres e-mail, można dodać inne ścieżki do zasady unikatowych kluczy. Dlatego zamiast tworzenia unikatowego klucza na podstawie adresu e-mail, można utworzyć unikatowy klucz, który składa się z pól Imię, nazwisko i adres e-mail. W takim przypadku każda unikatowa kombinacja trzech ścieżek jest dozwolone, więc baza danych może zawierać elementy, które mają następujące wartości ścieżek. Każda z tych rekordów przejdzie zasady unikatowych kluczy.  

**Dozwolone wartości Unikatowy klucz firstName, lastName i email**

|Imię|Nazwisko|Email address (Adres e-mail)|
|---|---|---|
|Gaby|Duperre|gaby@contoso.com |
|Gaby|Duperre|gaby@fabrikam.com|
|Ivanowi|Duperre|gaby@fabrikam.com|
|    |Duperre|gaby@fabrikam.com|
|    |       |gaby@fabraikam.com|

Jeśli podjęto próbę wstawienia innego rekordu przy użyciu dowolnej kombinacji wymienionych w powyższej tabeli, otrzyma komunikat o błędzie informujący, unikatowe ograniczenie klucza nie zostało spełnione. Błąd usługi Azure Cosmos DB, zwracany jest "Zasób o określonym identyfikatorze lub nazwie już istnieje." lub "Zasobu o określonym identyfikatorze, nazwy lub unikatowego indeksu już istnieje". 

## <a name="using-unique-keys"></a>Za pomocą unikatowe klucze

Unikatowe klucze musi być zdefiniowany ten kontener jest tworzony, gdy Unikatowy klucz jest ograniczone do klucza partycji. Aby skompilować na wcześniejszym przykładzie, jeśli po utworzeniu partycji w oparciu o kod pocztowy, może mieć rekordy z tabeli zduplikowane w każdej partycji.

Nie można zaktualizować istniejącego kontenera na potrzeby używania unikatowe klucze.

Po utworzeniu kontenera za pomocą zasady unikatowych kluczy, zasady nie można zmienić, chyba że ponownego tworzenia kontenera. W przypadku istniejących danych, które chcesz zaimplementować unikatowe klucze na tworzenie nowego kontenera, a następnie użyć narzędzia migracji danych do przenoszenia danych do nowego kontenera. W przypadku kontenerów SQL, użyj [narzędzia migracji danych](import-data.md). Kontenery bazy danych MongoDB, można użyć [mongoimport.exe lub mongorestore.exe](mongodb-migrate.md).

Maksymalnie 16 wartościami ścieżki (na przykład /firstName, /lastName, /address/zipCode itp.) mogą być dołączane w każdy unikatowy klucz. 

Każdej zasady unikatowych kluczy może zawierać maksymalnie 10 unikatowych ograniczeń klucza lub kombinacji i połączone ścieżki dla wszystkich właściwości indeksu unique nie powinna przekraczać 60 znaków. Więc poprzedniego przykładu, który używa imienia, nazwiska, adresu e-mail jest tylko jedno ograniczenie i używa trzech 16 możliwe ścieżki, dostępne. 

Żądań jednostki opłaty za tworzenie, aktualizowanie i usuwanie elementu są nieco większe, gdy zasady unikatowych kluczy w kontenerze. 

Rozrzedzony unikatowe klucze nie są obsługiwane. Jeśli brakuje wartości dla niektórych unikatowych ścieżek, będą one traktowane jako specjalne wartość null, które bierze udział w ograniczenie unikatowości.

## <a name="sql-api-sample"></a>Przykład interfejsu API SQL

Poniższy przykład kodu pokazuje, jak utworzyć nowy kontener SQL za pomocą dwóch unikatowych ograniczeń klucza. Pierwszy ograniczenie jest firstName, lastName, poczty e-mail ograniczenie opisanych we wcześniejszym przykładzie. Drugi ograniczenie polega na adres/kod pocztowy użytkowników. Przykładowy plik JSON, który używa ścieżki w zasady unikatowych kluczy następuje w przykładzie kodu. 

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
> Proszę Uwaga unikatowa nazwa klucza jest uwzględniana wielkość liter. Jak pokazano powyżej przykład, unikatową nazwę ustawiono /address/zipcode. Jeśli Twoje dane będą mieć kod pocztowy, następnie zostaną wstawione "null" w kluczu unikatowy kod pocztowy nie jest równa kod pocztowy. I w związku z tym rozróżnianie wielkości liter wszystkich rekordów z kod pocztowy nie będzie ma zostać wstawiony jako zduplikowane wartości "null" będzie narusza unikatowe ograniczenie klucza.

## <a name="mongodb-api-sample"></a>Przykład interfejsu API usługi MongoDB

Następujące przykładowe polecenie pokazuje, jak utworzyć indeksu unique w firstName, lastName i pola wiadomości e-mail w kolekcji użytkowników dla interfejsu API usługi MongoDB. Dzięki temu unikatowości dla kombinacji wszystkie trzy pola dla wszystkich dokumentów w kolekcji. Dla kolekcji interfejs API usługi MongoDB unikatowy indeks jest tworzony po utworzeniu kolekcji, ale przed kolekcję.

> [!NOTE]
> Unikatowy format klucza interfejsu API usługi MongoDB kont różni się od z interfejsu API SQL kont, których nie trzeba określić znak ukośnika (/) przed nazwą pola. 

```
db.users.createIndex( { firstName: 1, lastName: 1, email: 1 }, { unique: true } )
```
## <a name="configure-unique-keys-by-using-azure-portal"></a>Skonfigurować unikatowe klucze za pomocą witryny Azure Portal

W sekcji powyżej, które znajdują się przykłady kodu, pokazujące, jak zdefiniować ograniczenia klucza unikatowe podczas tworzenia kolekcji za pomocą interfejsu API SQL lub interfejsu API usługi MongoDB. Ale istnieje również możliwość zdefiniowania unikatowych kluczy podczas tworzenia kolekcji za pomocą interfejsu użytkownika sieci web w witrynie Azure portal. 

- Przejdź do **Eksplorator danych** w ramach konta usługi Cosmos DB
- Kliknij przycisk **nowej kolekcji**
- W sekcji unikatowe klucze ** odpowiednią unikatowych ograniczeń klucza można dodać, klikając **Dodaj Unikatowy klucz.**

![Zdefiniuj unikatowe klucze w Eksploratorze danych](./media/unique-keys/unique-keys-azure-portal.png)

- Jeśli chcesz utworzyć unikatowego ograniczenia klucza na ścieżce lastName, Dodaj `/lastName`.
- Jeśli chcesz utworzyć unikatowego ograniczenia klucza dla kombinacji firstName lastName, Dodaj `/lastName,/firstName`

Po zakończeniu kliknij przycisk **OK** do utworzenia kolekcji.

## <a name="next-steps"></a>Kolejne kroki

W tym artykule przedstawiono sposób tworzenia unikatowe klucze dla elementów w bazie danych. Jeśli kontener jest tworzony po raz pierwszy, zapoznaj się z [partycjonowanie danych w usłudze Azure Cosmos DB](partition-data.md) jako unikatowe klucze i klucze partycji zależą od siebie nawzajem. 


