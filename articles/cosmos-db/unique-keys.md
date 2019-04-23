---
title: Użyj unikatowe klucze w usłudze Azure Cosmos DB
description: Dowiedz się, jak za pomocą unikatowych kluczy bazy danych Azure Cosmos
author: rimman
ms.author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2019
ms.reviewer: sngun
ms.openlocfilehash: 3c5e8a2c85898175772dc353258e77fc8e0a74f2
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59799120"
---
# <a name="unique-key-constraints-in-azure-cosmos-db"></a>Unikatowe ograniczenia klucza w usłudze Azure Cosmos DB

Unikatowe klucze dodanie warstwy integralności danych do kontenera usługi Azure Cosmos. Możesz utworzyć zasady unikatowych kluczy podczas tworzenia kontenera usługi Azure Cosmos. Z unikatowymi kluczami, należy upewnić się, że co najmniej jednej wartości w ramach partycji logicznej jest unikatowa. Pozwala również zagwarantować unikatowości na [klucza partycji](partition-data.md). 

Po utworzeniu kontenera za pomocą zasady unikatowych kluczy, utworzenia nowego lub aktualizację istniejącego elementu skutkuje zduplikowane w ramach partycji logicznej jest blokowana, zgodnie z określonym unikatowe ograniczenie klucza. Klucz partycji, w połączeniu z unikatowego klucza gwarantuje unikatowość elementu w zakresie kontenera.

Na przykład, należy wziąć pod uwagę kontenera usługi Azure Cosmos za pomocą adresu e-mail jako unikatowe ograniczenie klucza i `CompanyID` jako klucza partycji. Po skonfigurowaniu przez użytkownika, adres e-mail z unikatowym kluczem, każdy element ma unikatowego adresu e-mail w ramach danego `CompanyID`. Nie można utworzyć dwa elementy, z zduplikowane adresy e-mail oraz z taką samą wartość klucza partycji. 

Tworzenie elementów przy użyciu tego samego adresu e-mail adres, ale nie sam imię, nazwisko i adres e-mail, należy dodać więcej ścieżek do zasady unikatowych kluczy. Zamiast tworzenia unikatowego klucza na podstawie tylko za pomocą adresu e-mail, możesz również utworzyć unikatowy klucz przy użyciu kombinacji imię, nazwisko i adres e-mail. Ten klucz jest określany jako unikatowego klucza złożonego. W takim przypadku każda unikatowa kombinacja tych trzech wartości w danym `CompanyID` jest dozwolone. 

Na przykład kontener może zawierać elementy z następującymi wartościami, gdzie każdy element honoruje ograniczenia unique dla klucza.

|CompanyID|Imię|Nazwisko|Adres e-mail|
|---|---|---|---|
|Contoso|Gaby|Duperre|gaby@contoso.com |
|Contoso|Gaby|Duperre|gaby@fabrikam.com|
|Fabrikam|Gaby|Duperre|gaby@fabrikam.com|
|Fabrikam|Ivan|Duperre|gaby@fabrikam.com|
|Fabrkam|   |Duperre|gaby@fabraikam.com|
|Fabrkam|   |   |gaby@fabraikam.com|

Jeśli użytkownik podejmie próbę Wstaw element innego za pomocą kombinacji wymienionych w powyższej tabeli, otrzymasz komunikat o błędzie. Błąd wskazuje, czy unikatowe ograniczenie klucza nie został spełniony. Pojawi się jeden `Resource with specified ID or name already exists` lub `Resource with specified ID, name, or unique index already exists` jako zwracany komunikat. 

## <a name="define-a-unique-key"></a>Zdefiniuj Unikatowy klucz

Unikatowe klucze można zdefiniować tylko wtedy, gdy należy utworzyć kontener usługi Azure Cosmos. Unikatowy klucz, który obejmuje logicznej partycji. W poprzednim przykładzie Jeśli po utworzeniu partycji w kontenerze, na podstawie kodu POCZTOWEGO na końcu zduplikowane elementy w każdej partycji logicznej. Podczas tworzenia unikatowych kluczy, należy wziąć pod uwagę następujące właściwości:

* Nie można zaktualizować istniejącego kontenera, aby użyć innego unikatowego klucza. Innymi słowy po utworzeniu kontenera za pomocą zasady unikatowych kluczy zasad nie można zmienić.

* Aby określić unikatowy klucz dla istniejącego kontenera, należy utworzyć nowy kontener przy użyciu unikatowych ograniczenie klucza. Aby przenieść dane z istniejącego kontenera do nowego kontenera, należy użyć narzędzia migracji danych. W przypadku kontenerów SQL, użyj [narzędzia migracji danych](import-data.md) do przenoszenia danych. Kontenery bazy danych MongoDB, można użyć [mongoimport.exe lub mongorestore.exe](mongodb-migrate.md) do przenoszenia danych.

* Zasady unikatowych kluczy może mieć maksymalnie 16 wartości ścieżek. Na przykład wartości mogą być `/firstName`, `/lastName`, i `/address/zipCode`. Każdej zasady unikatowych kluczy może mieć maksymalnie 10 unikatowych ograniczeń klucza lub kombinacji. Połączone ścieżki dla każdego z ograniczeń unikatowy indeks nie może przekraczać 60 bajtów. W poprzednim przykładzie imię, nazwisko i adres e-mail są ze sobą jedno ograniczenie. To ograniczenie używa 3 poza 16 możliwe ścieżki.

* Jeśli kontener zawiera zasady unikatowych kluczy [jednostek żądań (RU)](request-units.md) opłat, aby tworzyć, aktualizować i usuwać element są nieco większe.

* Rozrzedzony unikatowe klucze nie są obsługiwane. Jeśli brakuje niektórych wartości unikatową ścieżkę, są one traktowane jako wartości null, które biorą udział w ograniczenie unikatowości. Z tego powodu może być tylko pojedynczy element z wartością null, aby spełnić tego ograniczenia.

* Unikatowe nazwy kluczy uwzględniają wielkość liter. Na przykład, należy wziąć pod uwagę kontenera za pomocą klucza ograniczenia unique równa `/address/zipcode`. Jeśli dane mają pola o nazwie `ZipCode`, usługi Azure Cosmos DB wstawia "null", jako unikatowego klucza, ponieważ `zipcode` nie jest taka sama jak `ZipCode`. Ze względu na to wielkość liter nie można wstawić wszystkie rekordy z kod pocztowy, ponieważ duplikat "null" narusza unikatowe ograniczenie klucza.

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [partycjami logicznymi](partition-data.md).
