---
title: Szyfrowanie danych z tabeli magazynu systemu Azure | Dokumentacja firmy Microsoft
description: Więcej informacji o szyfrowaniu danych tabeli w magazynie Azure.
services: storage
documentationcenter: na
author: MarkMcGeeAtAquent
manager: kfile
ms.assetid: 8e228b0c-2998-4462-8101-9f16517393ca
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/11/2018
ms.author: sngun
ms.openlocfilehash: 082e8a54cc8625a4bbdea2157f73874dbc86fde2
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34660896"
---
# <a name="encrypt-table-data"></a>Szyfrowanie danych tabeli
Biblioteka klienta magazynu Azure .NET obsługuje szyfrowanie właściwości jednostki parametrów w operacjach wstawiania i zamienianie operacji. Zaszyfrowane ciągi są przechowywane w usłudze jako właściwości binarnych i są one konwertowana z powrotem do ciągów po odszyfrowywania.    

W przypadku tabel, oprócz zasad szyfrowania użytkownicy muszą określić właściwości, które mają być szyfrowane. Można to zrobić, określając atrybutu [EncryptProperty] (dla jednostki POCO, które pochodzą z TableEntity) lub szyfrowania, program rozpoznawania nazw w opcjach żądania. Rozwiązywanie problemów z szyfrowania jest delegata, który ma klucz partycji, klucz wiersza i nazwy właściwości i zwraca wartość Boolean wskazującą, czy ma być szyfrowana tej właściwości. Podczas szyfrowania biblioteki klienta używa tych informacji do określenia, czy można zaszyfrować właściwości podczas zapisywania do przesyłania. Delegat także możliwość logiki wokół jak właściwości są szyfrowane. (Na przykład, jeśli X, następnie szyfrować właściwości A; w przeciwnym razie szyfrowania właściwości A i B.) Nie jest niezbędne do zapewnienia tych informacji podczas odczytywania lub zapytanie jednostki.

## <a name="merge-support"></a>Scal pomocy technicznej

Scalanie nie jest obecnie obsługiwane. Ponieważ podzbiór właściwości może być zaszyfrowany wcześniej za pomocą innego klucza, po prostu scalanie nowych właściwości i aktualizowania metadanych powoduje utratę danych. Scalanie albo wymaga wywołań usługi dodatkowe odczytać istniejące jednostki z usługi lub przy użyciu nowego klucza dla właściwości, które nie są odpowiednie ze względu na wydajność.     

Informacje o szyfrowaniu danych z tabeli, zobacz [szyfrowania po stronie klienta i usługi Azure Key Vault dla magazynu Microsoft Azure](../common/storage-client-side-encryption.md).  

## <a name="next-steps"></a>Kolejne kroki

- [Wzorce projektowe tabeli](table-storage-design-patterns.md)
- [Relacje modelowania](table-storage-design-modeling.md)
- [Relacje modelowania](table-storage-design-modeling.md)
- [Projekt do modyfikacji danych](table-storage-design-for-modification.md)