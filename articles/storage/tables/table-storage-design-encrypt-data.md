---
title: Szyfrowanie danych tabeli usługi Azure storage | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o szyfrowaniu danych tabeli w usłudze Azure storage.
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: article
ms.date: 04/11/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: f56946702011968a0fcb31f6fbecbaacdc89ea42
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60326007"
---
# <a name="encrypt-table-data"></a>Szyfrowanie danych w tabelach
Biblioteka klienta .NET Azure Storage obsługuje szyfrowanie właściwości jednostki ciągu wstawiania i zamienianie operacji. Zaszyfrowane ciągi są przechowywane w usłudze jako właściwości binarnych i są konwertowane do ciągów po odszyfrowywania.    

W przypadku tabel, oprócz zasad szyfrowania użytkownicy muszą określić właściwości, które mają być szyfrowane. Można to zrobić, albo określając atrybutu [EncryptProperty] (dla jednostki POCO, które wynikają z TableEntity) lub szyfrowania mechanizmu rozwiązywania konfliktów w opcji żądania. Mechanizm rozpoznawania szyfrowania jest delegat, który przyjmuje klucza partycji, klucz wiersza i nazwę właściwości i zwraca wartość Boolean wskazującą, czy mają być szyfrowane tej właściwości. Podczas szyfrowania Biblioteka klienta używa tych informacji do określania, czy należy szyfrować właściwości podczas zapisu w sieci. Delegat udostępnia także możliwości logiki wokół jak zaszyfrowane właściwości. (Na przykład, jeśli X, następnie szyfrować właściwość A; w przeciwnym razie szyfrowanie właściwości, A i B.) Nie jest konieczne może przekazać tę informację podczas odczytywania lub podczas badania jednostki.

## <a name="merge-support"></a>Scal pomocy technicznej

Scalanie nie jest obecnie obsługiwane. Ponieważ podzbiór właściwości może być zaszyfrowany wcześniej przy użyciu innego klucza, po prostu scalanie nowych właściwości i aktualizowanie metadanych powoduje utratę danych. Scalanie albo wymaga wywołań usługi dodatkowe odczytać wstępnie istniejącej jednostki usługi lub przy użyciu nowego klucza dla właściwości, które nie są odpowiednie dla ze względu na wydajność.     

Aby uzyskać informacje o szyfrowaniu danych w tabeli, zobacz [szyfrowanie po stronie klienta i usługi Azure Key Vault dla usługi Microsoft Azure Storage](../common/storage-client-side-encryption.md).  

## <a name="next-steps"></a>Kolejne kroki

- [Wzorce projektowe oparte na tabelę](table-storage-design-patterns.md)
- [Modelowanie relacji](table-storage-design-modeling.md)
- [Modelowanie relacji](table-storage-design-modeling.md)
- [Projektowanie pod kątem modyfikacji danych](table-storage-design-for-modification.md)
