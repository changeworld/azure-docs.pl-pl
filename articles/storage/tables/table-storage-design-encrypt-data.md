---
title: Szyfrowanie danych tabeli magazynu platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się więcej o szyfrowaniu danych tabeli w magazynie platformy Azure.
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: article
ms.date: 04/11/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: f56946702011968a0fcb31f6fbecbaacdc89ea42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60326007"
---
# <a name="encrypt-table-data"></a>Szyfruj dane tabeli
Biblioteka klienta usługi .NET Azure Storage obsługuje szyfrowanie właściwości jednostki ciągu dla operacji wstawiania i zamieniania. Zaszyfrowane ciągi są przechowywane w usłudze jako właściwości binarne i są konwertowane z powrotem na ciągi po odszyfrowaniu.    

W przypadku tabel, oprócz zasad szyfrowania, użytkownicy muszą określić właściwości, które mają być szyfrowane. Można to zrobić, określając atrybut [EncryptProperty] (dla jednostek POCO, które pochodzą z TableEntity) lub program rozpoznawania szyfrowania w opcjach żądań. Program rozpoznawania nazw szyfrowania jest pełnomocnikiem, który przyjmuje klucz partycji, klucz wiersza i nazwę właściwości i zwraca wartość logiczną, która wskazuje, czy ta właściwość powinna być szyfrowana. Podczas szyfrowania biblioteka klienta używa tych informacji, aby zdecydować, czy do szyfrowania właściwości podczas zapisywania do sieci. Delegat zapewnia również możliwość logiki wokół jak właściwości są szyfrowane. (Na przykład, jeśli X, a następnie zaszyfrować właściwość A; w przeciwnym razie szyfrować właściwości A i B.) Nie jest konieczne podanie tych informacji podczas odczytywania lub wykonywania zapytań o jednostki.

## <a name="merge-support"></a>Obsługa scalania

Scalanie nie jest obecnie obsługiwane. Ponieważ podzbiór właściwości może być zaszyfrowany wcześniej przy użyciu innego klucza, po prostu scalanie nowych właściwości i aktualizowanie metadanych powoduje utratę danych. Scalanie wymaga wykonywania dodatkowych wywołań usługi, aby odczytać wcześniej istniejącej jednostki z usługi lub przy użyciu nowego klucza na właściwość, z których oba nie są odpowiednie ze względu na wydajność.     

Aby uzyskać informacje dotyczące szyfrowania danych tabeli, zobacz [Szyfrowanie po stronie klienta i Usługa Azure Key Vault dla usługi Microsoft Azure Storage](../common/storage-client-side-encryption.md).  

## <a name="next-steps"></a>Następne kroki

- [Wzorce projektowania tabel](table-storage-design-patterns.md)
- [Modelowanie relacji](table-storage-design-modeling.md)
- [Modelowanie relacji](table-storage-design-modeling.md)
- [Projektowanie pod kątem modyfikacji danych](table-storage-design-for-modification.md)
