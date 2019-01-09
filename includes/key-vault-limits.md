---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 9a39abf77a7396302f93e5a423271402b7c3edb3
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2019
ms.locfileid: "54084008"
---
Klucz transakcji (maksymalna liczba transakcji w ciągu 10 sekund, dozwolone magazynu na region<sup>1</sup>):

|Typ klucza|HSM-key<br>Utwórz klucz|HSM-key<br>Wszystkie inne transakcje|Klucz oprogramowania<br>Utwórz klucz|Klucz oprogramowania<br>Wszystkie inne transakcje|
|:---|---:|---:|---:|---:|
|RSA 2048-bit|5|1000|10|2000|
|RSA 3072-bit|5|250|10|500|
|RSA 4096-bit|5|125|10|250|
|ECC P-256|5|1000|10|2000|
|P-384 ECC|5|1000|10|2000|
|ECC P-521|5|1000|10|2000|
|ECC SECP256K1|5|1000|10|2000|
|

> [!NOTE]
> Powyżej wartości progowe są ważona i wymuszania znajduje się w ich suma. Możesz tworzyć 125 operacje RSA - przez moduł HSM — 4k i 0 RSA - przez moduł HSM - 2, lub 124 RSA - przez moduł HSM — 4k i 16 RSA - przez moduł HSM — 2 tys. Później w tym samym interwale 10 sekund innych operacji spowoduje, że wyjątek AKV klienta.

> [!NOTE]
> Jeśli przyjrzymy się w poniższej tabeli, możesz zobaczyć, że dla kluczy opartych na oprogramowanie zezwalamy na 2000 transakcji na 10 sekund, a przez moduł HSM kopii klucze zezwalamy na 1000 transakcji na 10 sekund. Współczynnik kopii oprogramowania następującą liczbę transakcji: klucze 3072 klucze 2048 to 500/2000 lub Update 0.4. Oznacza to, że jeśli klient ma 500 3072 transakcje klucza w ciągu 10 sekund, osiągną swój limit maksymalny wynoszący i nie można wykonać żadnych innych operacji klucza. 
   
|Typ klucza  | Klucz programowy |HSM-key  |
|---------|---------|---------|
|RSA 2048-bit     |    2000     |   1000    |
|RSA 3072-bit     |     500    |    250     |
|RSA 4096-bit     |    125     |    250     |
|ECC P-256     |    2000     |  1000     |
|P-384 ECC     |    2000     |  1000     |
|ECC P-521     |    2000     |  1000     |
|ECC SECP256K1     |    2000     |  1000     |


Wpisy tajne, zarządzanych kluczy konta magazynu i transakcje magazynu:
| Typ transakcji | Maksymalna liczba transakcji w ciągu 10 sekund, dozwolone magazynu na region<sup>1</sup> |
| --- | --- |
| Wszystkie transakcje |2000 |
|

Zobacz [wskazówki dotyczące ograniczania usługi Azure Key Vault](../articles/key-vault/key-vault-ovw-throttling.md) informacji na temat sposobu obsługi ograniczania, gdy te limity zostaną przekroczone.

<sup>1</sup> jest ograniczona do całej subskrypcji, dla wszystkich typów transakcji, 5 x limit magazynu kluczy na. Na przykład przez moduł HSM — inne transakcje w każdej subskrypcji mogą zawierać maksymalnie 5000 transakcji w ciągu 10 sekund na subskrypcję.
