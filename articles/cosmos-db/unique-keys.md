---
title: Używanie unikatowych kluczy w usłudze Azure Cosmos DB
description: Dowiedz się, jak definiować i używać unikatowych kluczy dla bazy danych usługi Azure Cosmos. W tym artykule opisano również, jak unikatowe klucze dodać warstwę integralności danych.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.reviewer: sngun
ms.openlocfilehash: f234579c6fb2b6f1bc0cd518b87ea69fae30093a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74869837"
---
# <a name="unique-key-constraints-in-azure-cosmos-db"></a>Unikatowe ograniczenia klucza w usłudze Azure Cosmos DB

Unikatowe klucze dodać warstwę integralności danych do kontenera usługi Azure Cosmos. Podczas tworzenia kontenera usługi Azure Cosmos można utworzyć unikatową zasadę klucza. Za pomocą unikatowych kluczy upewnij się, że jedna lub więcej wartości w partycji logicznej jest unikatowa. Można również zagwarantować unikatowość na [klucz partycji](partition-data.md).

Po utworzeniu kontenera z unikatową zasadą klucza, tworzenie nowego lub aktualizacji istniejącego elementu w wyniku duplikatu w ramach partycji logicznej jest zapobiegana, zgodnie z określonymi przez ograniczenie klucza unikatowego. Klucz partycji w połączeniu z kluczem unikatowym gwarantuje unikatowość elementu w zakresie kontenera.

Na przykład należy wziąć pod uwagę kontener usługi Azure `CompanyID` Cosmos z adresem e-mail jako ograniczenie klucza unikatowy i jako klucz partycji. Po skonfigurowaniu adresu e-mail użytkownika za pomocą unikatowego klucza każdy `CompanyID`element ma unikatowy adres e-mail w danym pliku . Nie można utworzyć dwóch elementów przy zduplikowanych adresach e-mail i tej samej wartości klucza partycji. 

Aby utworzyć elementy o tym samym adresie e-mail, ale nie o tym samym imieniem, nazwisku i adresie e-mail, dodaj więcej ścieżek do unikatowych zasad klucza. Zamiast tworzyć unikatowy klucz oparty tylko na adresie e-mail, można również utworzyć unikatowy klucz z kombinacją imienia, nazwiska i adresu e-mail. Ten klucz jest znany jako złożony unikatowy klucz. W takim przypadku każda unikatowa kombinacja `CompanyID` trzech wartości w danym jest dozwolona. 

Na przykład kontener może zawierać elementy z następującymi wartościami, gdzie każdy element honoruje unikatowe ograniczenie klucza.

|FirmID (ida firmy)|Imię|Nazwisko|Adres e-mail|
|---|---|---|---|
|Contoso|Gaby|Duperre|gaby@contoso.com |
|Contoso|Gaby|Duperre|gaby@fabrikam.com|
|Fabrikam|Gaby|Duperre|gaby@fabrikam.com|
|Fabrikam|Ivan|Duperre|gaby@fabrikam.com|
|Fabrkam ( Fabrkam )|   |Duperre|gaby@fabraikam.com|
|Fabrkam ( Fabrkam )|   |   |gaby@fabraikam.com|

Jeśli spróbujesz wstawić inny element z kombinacjami wymienionymi w poprzedniej tabeli, zostanie wyświetlony błąd. Błąd wskazuje, że ograniczenie klucza unikatowego nie zostało spełnione. Otrzymasz albo `Resource with specified ID or name already exists` `Resource with specified ID, name, or unique index already exists` lub jako wiadomość zwrotną. 

## <a name="define-a-unique-key"></a>Definiowanie unikatowego klucza

Unikatowe klucze można zdefiniować tylko podczas tworzenia kontenera usługi Azure Cosmos. Unikatowy klucz jest objęty zakresem do partycji logicznej. W poprzednim przykładzie, jeśli partycji kontenera na podstawie kodu pocztowego, skończyć z zduplikowanych elementów w każdej partycji logicznej. Podczas tworzenia unikatowych kluczy należy wziąć pod uwagę następujące właściwości:

* Nie można zaktualizować istniejącego kontenera, aby użyć innego klucza unikatowego. Innymi słowy po utworzeniu kontenera z unikatową zasadą klucza zasady nie można zmienić.

* Aby ustawić unikatowy klucz dla istniejącego kontenera, utwórz nowy kontener z unikatowym ograniczeniem klucza. Użyj odpowiedniego narzędzia migracji danych, aby przenieść dane z istniejącego kontenera do nowego kontenera. W przypadku kontenerów SQL użyj [narzędzia Migracji danych,](import-data.md) aby przenieść dane. W przypadku kontenerów MongoDB należy przenosić dane za pomocą [pliku mongoimport.exe lub mongorestore.exe.](mongodb-migrate.md)

* Unikatowa zasada klucza może mieć maksymalnie 16 wartości ścieżki. Na przykład wartości mogą `/firstName` `/lastName`być `/address/zipCode`, i . Każda unikatowa zasada klucza może mieć maksymalnie 10 unikatowych ograniczeń lub kombinacji klawiszy. Połączone ścieżki dla każdego unikatowego ograniczenia indeksu nie mogą przekraczać 60 bajtów. W poprzednim przykładzie imię, nazwisko i adres e-mail razem są jednym ograniczeniem. To ograniczenie używa 3 z 16 możliwych ścieżek.

* Gdy kontener ma unikatowe zasady klucza, [żądania jednostki (RU)](request-units.md) opłaty za tworzenie, aktualizowanie i usuwanie elementu są nieco wyższe.

* Rozrzedzone unikatowe klucze nie są obsługiwane. Jeśli brakuje niektórych unikatowych wartości ścieżki, są one traktowane jako wartości null, które biorą udział w ograniczeniu unikatowości. Z tego powodu może istnieć tylko jeden element o wartości null, aby spełnić to ograniczenie.

* W unikatowych nazwach kluczy rozróżniana jest wielkość liter. Rozważmy na przykład kontener z unikatowym `/address/zipcode`ograniczeniem klucza ustawionym na . Jeśli dane mają pole `ZipCode`o nazwie, usługa Azure Cosmos DB `zipcode` wstawia "null" jako unikatowy klucz, ponieważ nie jest taka sama jak `ZipCode`. Z powodu tej czułości wielkości liter nie można wstawić wszystkich innych rekordów z kodem zip, ponieważ zduplikowane "null" narusza ograniczenie klucza unikatowego.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [partycjach logicznych](partition-data.md)
* Dowiedz [się, jak zdefiniować unikatowe klucze](how-to-define-unique-keys.md) podczas tworzenia kontenera
