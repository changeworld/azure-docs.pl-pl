---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 0b9d87fd7929607da8407ae5bbfb2f6dd6d69dab
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183636"
---
#### <a name="key-transactions-maximum-transactions-allowed-in-10-seconds-per-vault-per-regionsup1sup"></a>Klucz transakcji (maksymalna liczba transakcji w ciągu 10 sekund, dozwolone magazynu na region<sup>1</sup>):

|Typ klucza|Klucz HSM<br>Utwórz klucz|Klucz HSM<br>Wszystkie inne transakcje|Klucz programowy<br>Utwórz klucz|Klucz programowy<br>Wszystkie inne transakcje|
|:---|---:|---:|---:|---:|
|RSA 2048 bitowych|5|1000|10|2000|
|3072 bitowe RSA|5|250|10|500|
|RSA 4096 bitowe|5|125|10|250|
|ECC P-256|5|1000|10|2000|
|P-384 ECC|5|1000|10|2000|
|ECC P-521|5|1000|10|2000|
|ECC SECP256K1|5|1000|10|2000|

> [!NOTE]
> W poprzedniej tabeli widzimy, że RSA 2048-bitowych kluczy oprogramowania, 2000 transakcji GET na 10 sekund są dozwolone. Klucze RSA 2048-bitowych HSM są dozwolone 1000 transakcji GET na 10 sekund.
>
> Ważona są wartościach progowych ograniczania przepustowości i wymuszania znajduje się w ich suma. Na przykład jak pokazano w poprzedniej tabeli, podczas wykonywania operacji GET na klucze RSA przez moduł HSM, jest osiem godzin droższe do używania kluczy 4096 bitowe, w porównaniu z kluczami 2048 bitowych. To dlatego, że 1000/125 = 8.
>
> W danym interwale 10 sekund, przez klienta usługi Azure Key Vault można zrobić *tylko jeden* z następujących czynności przed napotka `429` ograniczania kod stanu HTTP:
> - 2000 transakcji GET oprogramowania kluczy RSA 2048-bitowych
> - 1000 transakcji GET klucza HSM RSA 2048-bitowych
> - 125 transakcji RSA 4096-bitowego klucza HSM GET
> - 124 transakcje GET klucza HSM RSA 4096-bitowe i 8 GET klucza HSM RSA 2048-bitowych

#### <a name="secrets-managed-storage-account-keys-and-vault-transactions"></a>Wpisy tajne, kluczy zarządzanego konta magazynu i transakcje magazynu:
| Typ transakcji | Maksymalna liczba transakcji w ciągu 10 sekund, dozwolone magazynu na region<sup>1</sup> |
| --- | --- |
| Wszystkie transakcje |2000 |

Aby uzyskać informacje na temat sposobu obsługi ograniczania, gdy te limity zostaną przekroczone, zobacz [wskazówki dotyczące ograniczania usługi Azure Key Vault](../articles/key-vault/key-vault-ovw-throttling.md).

<sup>1</sup> limit na poziomie subskrypcji dla wszystkich typów transakcji jest pięć razy na limit magazynu kluczy. Na przykład przez moduł HSM inne transakcje na subskrypcję są maksymalnie 5000 transakcji w ciągu 10 sekund na subskrypcję.
