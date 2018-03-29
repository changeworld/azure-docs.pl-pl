Klucz transakcji (transakcji maksymalna dozwolona za 10 sekund na magazynu na region<sup>1</sup>):

|Typ klucza|HSM-Key<br>Utwórz klucz|Klucz HSM<br>Wszystkie inne transakcje|Klucz oprogramowania<br>Utwórz klucz|Klucz oprogramowania<br>Wszystkie inne transakcje|
|:---|---:|---:|---:|---:|
|RSA 2048-bit|5|1000|10|2000|
|RSA 3072-bit|5|250|10|500|
|RSA 4096-bit|5|125|10|250|
|ECC P-256|5|1000|10|2000|
|ECC P-384|5|1000|10|2000|
|ECC P-521|5|1000|10|2000|
|ECC SECP256K1|5|1000|10|2000|
|

Klucze tajne, zarządzane klucze konta magazynu i transakcji magazynu:
| Typ transakcji | Maksymalna liczba transakcji za 10 sekund dozwolony magazynu na region<sup>1</sup> |
| --- | --- |
| Wszystkie transakcje |2000 |
|

Zobacz [usługi Azure Key Vault ograniczania wskazówki](../articles/key-vault/key-vault-ovw-throttling.md) informacji o sposobie obsługi ograniczania przepustowości, gdy zostaną przekroczone następujące limity.

<sup>1</sup> jest ograniczona do całej subskrypcji, dla wszystkich typów transakcji, które 5 x limitu magazynu kluczy. Na przykład HSM - inne transakcje dla subskrypcji są ograniczone do 5000 transakcji za 10 sekund na subskrypcję.
