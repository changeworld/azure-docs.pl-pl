---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: ed0c387f9785336fbf18b3fd3c0cd9a7b09df633
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2018
ms.locfileid: "52279826"
---
Klucz transakcji (maksymalna liczba transakcji w ciągu 10 sekund, dozwolone magazynu na region<sup>1</sup>):

|Typ klucza|HSM-Key<br>Utwórz klucz|HSM-key<br>Wszystkie inne transakcje|Klucz oprogramowania<br>Utwórz klucz|Klucz oprogramowania<br>Wszystkie inne transakcje|
|:---|---:|---:|---:|---:|
|RSA 2048-bit|5|1000|10|2000|
|RSA 3072-bit|5|250|10|500|
|RSA 4096-bit|5|125|10|250|
|ECC P-256|5|1000|10|2000|
|P-384 ECC|5|1000|10|2000|
|ECC P-521|5|1000|10|2000|
|ECC SECP256K1|5|1000|10|2000|
|

Wpisy tajne, zarządzanych kluczy konta magazynu i transakcje magazynu:
| Typ transakcji | Maksymalna liczba transakcji w ciągu 10 sekund, dozwolone magazynu na region<sup>1</sup> |
| --- | --- |
| Wszystkie transakcje |2000 |
|

Zobacz [wskazówki dotyczące ograniczania usługi Azure Key Vault](../articles/key-vault/key-vault-ovw-throttling.md) informacji na temat sposobu obsługi ograniczania, gdy te limity zostaną przekroczone.

<sup>1</sup> jest ograniczona do całej subskrypcji, dla wszystkich typów transakcji, 5 x limit magazynu kluczy na. Na przykład przez moduł HSM — inne transakcje w każdej subskrypcji mogą zawierać maksymalnie 5000 transakcji w ciągu 10 sekund na subskrypcję.
