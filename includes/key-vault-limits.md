---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 2d16febd4676ca7ba763eb7bc6dcecda4608ebb5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74224580"
---
#### <a name="key-transactions-maximum-transactions-allowed-in-10-seconds-per-vault-per-regionsup1sup"></a>Kluczowe transakcje (maksymalna dozwolona liczba transakcji w ciągu 10 sekund, na magazyn na region<sup>1):</sup>

|Typ klucza|Klucz HSM<br>KLUCZ CREATE|Klucz HSM<br>Wszystkie inne transakcje|Klucz oprogramowania<br>KLUCZ CREATE|Klucz oprogramowania<br>Wszystkie inne transakcje|
|:---|---:|---:|---:|---:|
|RSA 2048-bit|5|1000|10|2000|
|RSA 3072-bitowe|5|250|10|500|
|RSA 4096-bit|5|125|10|250|
|Eks P-256|5|1000|10|2000|
|EKC P-384|5|1000|10|2000|
|Eks P-521|5|1000|10|2000|
|ECC SECP256K1|5|1000|10|2000|

> [!NOTE]
> W poprzedniej tabeli widzimy, że dla kluczy oprogramowania RSA 2048-bitowych dozwolone są 2000 transakcji GET na 10 sekund. W przypadku 2048-bitowych kluczy HSM rsa dozwolone jest 1000 transakcji GET na 10 sekund.
>
> Progi ograniczania przepustowości są ważone, a wymuszanie jest na ich sumie. Na przykład, jak pokazano w poprzedniej tabeli, podczas wykonywania operacji GET na klucze HSM RSA, jest osiem razy droższe użycie kluczy 4096-bitowych w porównaniu do kluczy 2048-bitowych. To dlatego, że 1000/125 = 8.
>
> W danym interwale 10-sekundowym klient usługi Azure Key Vault może wykonać `429` tylko jedną *z* następujących operacji, zanim napotka kod stanu HTTP ograniczania przepustowości:
> - 2000 transakcji RSA 2048-bitowych transakcji GET z kluczem programowym
> - 1000 2048-bitowych transakcji GET klucza HSM RSA
> - 125 4096-bitowych transakcji GET klucza HSM RSA
> - 124 4096-bitowe transakcje GET klucza HSM rsa i 8 transakcji RSA 2048-bitowy klucz HSM GET

#### <a name="secrets-managed-storage-account-keys-and-vault-transactions"></a>Wpisy tajne, klucze zarządzanych kont magazynu i transakcje magazynu:
| Typ transakcji | Maksymalna dozwolona liczba transakcji w ciągu 10 sekund, na magazyn na region<sup>1</sup> |
| --- | --- |
| Wszystkie transakcje |2000 |

Aby uzyskać informacje na temat obsługi ograniczania przepustowości po przekroczeniu tych limitów, zobacz [Wskazówki dotyczące ograniczania przepustowości usługi Azure Key Vault.](../articles/key-vault/key-vault-ovw-throttling.md)

<sup>1</sup> Limit dla wszystkich typów transakcji dla wszystkich typów transakcji wynosi pięć razy na limit magazynu kluczy. Na przykład hsm innych transakcji na subskrypcję są ograniczone do 5000 transakcji w 10 sekund na subskrypcję.
