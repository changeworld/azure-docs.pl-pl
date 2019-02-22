---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: a8979edf94c0dd0271293feb28c18530faeba09c
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2019
ms.locfileid: "56660304"
---
## <a name="key-transactions-max-transactions-allowed-in-10-seconds-per-vault-per-regionsup1sup"></a>Klucz transakcji (maksymalna liczba transakcji w ciągu 10 sekund, dozwolone magazynu na region<sup>1</sup>):

|Typ klucza|HSM-key<br>Utwórz klucz|HSM-key<br>Wszystkie inne transakcje|Klucz oprogramowania<br>Utwórz klucz|Klucz oprogramowania<br>Wszystkie inne transakcje|
|:---|---:|---:|---:|---:|
|RSA 2048-bit|5|1000|10|2000|
|RSA 3072-bit|5|250|10|500|
|RSA 4096-bit|5|125|10|250|
|ECC P-256|5|1000|10|2000|
|P-384 ECC|5|1000|10|2000|
|ECC P-521|5|1000|10|2000|
|ECC SECP256K1|5|1000|10|2000|

> [!NOTE]
> W powyższej tabeli widzimy, że szyfrowania RSA 2048-bitowe oprogramowanie-kluczy, firma Microsoft Zezwalaj 2000 transakcji GET na 10 sekund i czy — klucze szyfrowania RSA 2048-bitowych HSM, firma Microsoft Zezwalaj 1000 transakcji GET na 10 sekund.
>
> Należy pamiętać, że są ważona wartościach progowych ograniczania przepustowości i wymuszania znajduje się w ich suma. Na przykład w powyższej tabeli widać, że podczas wykonywania operacji GET dla kluczy RSA przez moduł HSM jest 8-krotnością droższe do używania kluczy 4096-bitowe, w porównaniu do 2048-bitowe klucze (od 1000/125 = 8). W związku z tym, w danym interwale 10 sekund, klient AKV zrobić dokładnie jeden z następujących czynności przed zapoznaniem się `429` ograniczania kod stanu HTTP:
> - 2000 szyfrowania RSA 2048-bitowy klucz oprogramowania GET transakcji **lub**
> - 1000 transakcji GET klucza HSM szyfrowania RSA 2048-bitowych, **lub**
> - 125 transakcji GET klucza HSM kluczy RSA 4096-bitowe, **lub**
> - 124 GET klucza HSM kluczy RSA 4096-bitowe transakcje i 8 szyfrowania RSA 2048-bitowego klucza HSM GET.

## <a name="secrets-managed-storage-account-keys-and-vault-transactions"></a>Wpisy tajne, zarządzanych kluczy konta magazynu i transakcje magazynu:
| Typ transakcji | Maksymalna liczba transakcji w ciągu 10 sekund, dozwolone magazynu na region<sup>1</sup> |
| --- | --- |
| Wszystkie transakcje |2000 |

Zobacz [wskazówki dotyczące ograniczania usługi Azure Key Vault](../articles/key-vault/key-vault-ovw-throttling.md) informacji na temat sposobu obsługi ograniczania, gdy te limity zostaną przekroczone.

<sup>1</sup> jest ograniczona do całej subskrypcji, dla wszystkich typów transakcji, 5 x limit magazynu kluczy na. Na przykład przez moduł HSM — inne transakcje w każdej subskrypcji mogą zawierać maksymalnie 5000 transakcji w ciągu 10 sekund na subskrypcję.
