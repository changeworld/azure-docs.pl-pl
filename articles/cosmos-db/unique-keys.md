---
title: Użyj unikatowe klucze w usłudze Azure Cosmos DB
description: Dowiedz się, jak za pomocą unikatowych kluczy bazy danych Azure Cosmos DB
author: aliuy
ms.author: andrl
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: 6dcbfcc4773befc291bd360dd14ff4c943d485bf
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2018
ms.locfileid: "52966606"
---
# <a name="unique-keys-in-azure-cosmos-db"></a>Unikatowe klucze w usłudze Azure Cosmos DB

Unikatowe klucze umożliwiają dodanie warstwy integralności danych do kontenera Cosmos. Możesz utworzyć zasady unikatowych kluczy podczas tworzenia kontenera Cosmos. Z unikatowymi kluczami, można zapewnić unikatowość co najmniej jednej wartości w ramach partycji logicznej (unikatowość na można zagwarantować [klucza partycji](partition-data.md)). Po utworzeniu kontenera za pomocą zasady unikatowych kluczy, uniemożliwia tworzenie wszelkich nowych (lub zaktualizowanych) zduplikowane elementy w ramach partycji logicznej określony przez unikatowe ograniczenie klucza. Klucz partycji w połączeniu z unikatowości unikatowe gwarancje klucza elementu w zakresie kontenera.

Na przykład, należy wziąć pod uwagę Cosmos kontenera za pomocą adresu e-mail jako unikatowego ograniczenia klucza i `CompanyID` jako klucza partycji. Konfigurując Unikatowy klucz adres e-mail użytkownika, upewnij się, każdy element ma unikatowego adresu e-mail w ramach danego `CompanyID`. Nie można utworzyć dwa elementy, z zduplikowane adresy e-mail oraz z taką samą wartość klucza partycji.  

Jeśli chcesz zapewnić użytkownikom możliwość tworzenia wielu elementów przy użyciu tego samego adresu e-mail, ale nie takiej samej nazwie pierwszy, ostatni nazwy i adresu e-mail, można dodać dodatkowe ścieżki do zasady unikatowych kluczy. Zamiast tworzenia unikatowego klucza na podstawie adresu e-mail, można również utworzyć unikatowy klucz przy użyciu kombinacji imię, nazwisko i (Unikatowy klucz złożony) adres e-mail. W takim przypadku każda unikatowa kombinacja tych trzech wartości w danym `CompanyID` jest dozwolone. Na przykład kontener może zawierać elementy z następującymi wartościami, gdzie każdy element jest zapewniane unikatowe ograniczenie klucza.

|CompanyID|Imię|Nazwisko|Adres e-mail|
|---|---|---|---|
|Contoso|Gaby|Duperre|gaby@contoso.com |
|Contoso|Gaby|Duperre|gaby@fabrikam.com|
|Firma Fabrikam|Gaby|Duperre|gaby@fabrikam.com|
|Firma Fabrikam|Ivanowi|Duperre|gaby@fabrikam.com|
|Fabrkam|   |Duperre|gaby@fabraikam.com|
|Fabrkam|   |   |gaby@fabraikam.com|

Jeśli użytkownik podejmie próbę Wstaw element innego za pomocą kombinacji wymienionych w powyższej tabeli, otrzymasz komunikat o błędzie informujący, unikatowe ograniczenie klucza nie zostało spełnione. Zostanie wyświetlony, albo "zasobu o określonym identyfikatorze lub nazwie już istnieje" lub "Zasobu o określonym identyfikatorze, nazwy lub unikatowego indeksu już istnieje" jako zwracany komunikat.  

## <a name="defining-a-unique-key"></a>Definiowanie Unikatowy klucz

Unikatowe klucze można zdefiniować tylko wtedy, gdy trwa tworzenie kontenera Cosmos. Unikatowy klucz, który obejmuje logicznej partycji. W poprzednim przykładzie Jeśli po utworzeniu partycji kontenera, w oparciu o kod pocztowy, prowadzi to do umożliwienia mających zduplikowane elementy w każdej partycji logicznej. Podczas tworzenia unikatowych kluczy, należy wziąć pod uwagę następujące właściwości:

* Nie można zaktualizować istniejącego kontenera, aby użyć innego unikatowego klucza. Innymi słowy gdy kontener jest tworzony przy użyciu zasady unikatowych kluczy, nie można zmienić zasady.

* Aby ustawić Unikatowy klucz dla istniejącego kontenera, musisz utworzyć nowy kontener przy użyciu unikatowych ograniczenie klucza i użyć narzędzia migracji danych do przenoszenia danych z istniejącego kontenera do nowego kontenera. W przypadku kontenerów SQL, użyj [narzędzia migracji danych](import-data.md) do przenoszenia danych. Kontenery bazy danych MongoDB, można użyć [mongoimport.exe lub mongorestore.exe](mongodb-migrate.md) do przenoszenia danych.

* Zasady unikatowych kluczy może mieć maksymalnie 16 wartościami ścieżki (na przykład: /firstName, /lastName, / address/zipCode). Każdej zasady unikatowych kluczy może zawierać maksymalnie 10 unikatowych ograniczeń klucza lub kombinacji i połączone ścieżki dla każdego indeksu unikatowego ograniczenia nie powinna przekraczać 60 bajtów. W poprzednim przykładzie imię, nazwisko i adres e-mail ze sobą są tylko jedno ograniczenie, i wykorzystuje trzy z 16 możliwe ścieżki.

* Jeśli kontener zawiera zasady unikatowych kluczy, opłat za jednostki (RU) na żądanie do tworzenia, aktualizacji i usuwania elementu są nieco większe.

* Rozrzedzony unikatowe klucze nie są obsługiwane. Jeśli brakuje niektórych wartości unikatową ścieżkę, będą one traktowane jako wartości null, które biorą udział w ograniczenie unikatowości. Dzięki temu może istnieć tylko pojedynczy element z wartością null do spełnienia tego ograniczenia.

* Unikatowe nazwy kluczy uwzględniają wielkość liter. Na przykład należy wziąć pod uwagę kontenera za pomocą klucza ograniczenia unique równa /address/zipcode. Jeśli dane mają pola o nazwie kod pocztowy, Cosmos DB wstawia "null", jako unikatowy klucz ponieważ "Kod pocztowy" nie jest taka sama jak "Kod pocztowy". Ze względu na to wielkość liter nie można wstawić wszystkie rekordy z kod pocztowy, ponieważ duplikat "null" będzie narusza unikatowe ograniczenie klucza.

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [partycjami logicznymi](partition-data.md)
