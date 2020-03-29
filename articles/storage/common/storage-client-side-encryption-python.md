---
title: Szyfrowanie po stronie klienta za pomocą języka Python
titleSuffix: Azure Storage
description: Biblioteka klienta usługi Azure Storage dla języka Python obsługuje szyfrowanie po stronie klienta w celu uzyskania maksymalnego bezpieczeństwa aplikacji usługi Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.devlang: python
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 16e66cd762b86b27dc6703542ca7261b2300a33b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74895366"
---
# <a name="client-side-encryption-with-python"></a>Szyfrowanie po stronie klienta za pomocą języka Python

[!INCLUDE [storage-selector-client-side-encryption-include](../../../includes/storage-selector-client-side-encryption-include.md)]

## <a name="overview"></a>Omówienie
[Biblioteka klienta usługi Azure Storage dla języka Python](https://pypi.python.org/pypi/azure-storage) obsługuje szyfrowanie danych w aplikacjach klienckich przed przekazaniem do usługi Azure Storage i odszyfrowywanie danych podczas pobierania do klienta.

> [!NOTE]
> Biblioteka usługi Azure Storage Python jest w wersji zapoznawczej.
> 
> 

## <a name="encryption-and-decryption-via-the-envelope-technique"></a>Szyfrowanie i odszyfrowywanie za pomocą techniki koperty
Procesy szyfrowania i odszyfrowywania są zgodne z techniką koperty.

### <a name="encryption-via-the-envelope-technique"></a>Szyfrowanie za pomocą techniki koperty
Szyfrowanie za pomocą techniki koperty działa w następujący sposób:

1. Biblioteka klienta magazynu platformy Azure generuje klucz szyfrowania zawartości (CEK), który jest kluczem symetrycznym jednorazowego użycia.
2. Dane użytkownika są szyfrowane przy użyciu tego CEK.
3. CEK jest następnie zawijany (zaszyfrowany) przy użyciu klucza szyfrowania klucza (KEK). KEK jest identyfikowany za pomocą identyfikatora klucza i może być asymetryczną parą kluczy lub kluczem symetrycznym, który jest zarządzany lokalnie.
   Sama biblioteka klienta magazynu nigdy nie ma dostępu do KEK. Biblioteka wywołuje algorytm zawijania kluczy, który jest dostarczany przez KEK. Użytkownicy mogą wybrać użycie niestandardowych dostawców do zawijania/rozpakowywania kluczy w razie potrzeby.
4. Zaszyfrowane dane są następnie przekazywane do usługi Azure Storage. Zawinięty klucz wraz z niektórymi dodatkowymi metadanymi szyfrowania jest przechowywany jako metadane (na obiektach blob) lub interpolowany z zaszyfrowanymi danymi (wiadomości kolejki i jednostki tabeli).

### <a name="decryption-via-the-envelope-technique"></a>Odszyfrowywanie za pomocą techniki koperty
Odszyfrowywanie za pomocą techniki koperty działa w następujący sposób:

1. Biblioteka klienta zakłada, że użytkownik zarządza kluczem szyfrowania klucza (KEK) lokalnie. Użytkownik nie musi znać określonego klucza, który był używany do szyfrowania. Zamiast tego można skonfigurować i używać rozpoznawania kluczy, który rozpoznaje różne identyfikatory kluczy do kluczy.
2. Biblioteka klienta pobiera zaszyfrowane dane wraz z dowolnym materiałem szyfrowania, który jest przechowywany w usłudze.
3. Klucz szyfrowania opakowanej zawartości (CEK) jest następnie rozpakowany (odszyfrowany) przy użyciu klucza szyfrowania klucza (KEK). W tym miejscu ponownie biblioteka klienta nie ma dostępu do KEK. Po prostu wywołuje algorytm rozpakowywania dostawcy niestandardowego.
4. Klucz szyfrowania zawartości (CEK) jest następnie używany do odszyfrowywania zaszyfrowanych danych użytkownika.

## <a name="encryption-mechanism"></a>Mechanizm szyfrowania
Biblioteka klienta magazynu używa [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) w celu szyfrowania danych użytkownika. W szczególności [tryb łańcucha bloków szyfrowania (CBC)](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher-block_chaining_.28CBC.29) z AES. Każda usługa działa nieco inaczej, więc omówimy każdy z nich tutaj.

### <a name="blobs"></a>Obiekty blob
Biblioteka klienta obsługuje obecnie szyfrowanie tylko całych obiektów blob. W szczególności szyfrowanie jest obsługiwane, gdy użytkownicy używają metod **create***. W przypadku pobierania obsługiwane są zarówno pobieranie kompletne, jak i zakresowe, a równoległość zarówno przekazywania, jak i pobierania jest dostępna.

Podczas szyfrowania biblioteka klienta wygeneruje losowy wektor inicjowania (IV) o 16 bajtach, wraz z kluczem szyfrowania zawartości losowej (CEK) 32 bajtów i wykona szyfrowanie kopert danych obiektów blob przy użyciu tych informacji. Opakowane CEK i niektóre dodatkowe metadane szyfrowania są następnie przechowywane jako metadane obiektów blob wraz z zaszyfrowanym obiektem blob w usłudze.

> [!WARNING]
> Jeśli edytujesz lub przekazujesz własne metadane obiektu blob, musisz upewnić się, że te metadane są zachowywane. Jeśli przekażesz nowe metadane bez tych metadanych, opakowane CEK, IV i inne metadane zostaną utracone, a zawartość obiektu blob nigdy nie będzie można pobrać ponownie.
> 
> 

Pobieranie zaszyfrowanego obiektu blob polega na pobraniu zawartości całego obiektu blob przy użyciu metod **wygody get***. Opakowane CEK jest rozpakowany i używane razem z IV (przechowywane jako metadane obiektu blob w tym przypadku) do zwracania odszyfrowanych danych do użytkowników.

Pobieranie dowolnego zakresu **(get*** metody z parametrami zakresu przekazywane w) w zaszyfrowanym obiektu blob polega na dostosowaniu zakresu dostarczonego przez użytkowników w celu uzyskania niewielkiej ilości dodatkowych danych, które mogą być wykorzystane do pomyślnego odszyfrowania żądanego zakresu.

Blok obiektów blob i tylko obiekty blob strony mogą być szyfrowane/odszyfrowywane przy użyciu tego schematu. Obecnie nie ma obsługi szyfrowania dołączania obiektów blob.

### <a name="queues"></a>Kolejki
Ponieważ wiadomości kolejki mogą mieć dowolny format, biblioteka klienta definiuje format niestandardowy, który zawiera wektor inicjowania (IV) i zaszyfrowany klucz szyfrowania zawartości (CEK) w tekście wiadomości.

Podczas szyfrowania biblioteka klienta generuje losowe IV 16 bajtów wraz z losowym CEK 32 bajtów i wykonuje szyfrowanie koperty tekstu wiadomości kolejki przy użyciu tych informacji. Zawinięty CEK i niektóre dodatkowe metadane szyfrowania są następnie dodawane do zaszyfrowanego komunikatu kolejki. Ten zmodyfikowany komunikat (pokazany poniżej) jest przechowywany w usłudze.

```
<MessageText>{"EncryptedMessageContents":"6kOu8Rq1C3+M1QO4alKLmWthWXSmHV3mEfxBAgP9QGTU++MKn2uPq3t2UjF1DO6w","EncryptionData":{…}}</MessageText>
```

Podczas odszyfrowywania opakowany klucz jest wyodrębniany z komunikatu kolejki i rozpakowany. IV jest również wyodrębniany z wiadomości kolejki i używany wraz z kluczem rozpakowanym do odszyfrowania danych wiadomości kolejki. Należy zauważyć, że metadane szyfrowania jest mały (poniżej 500 bajtów), więc podczas gdy nie wlicza się do limitu 64 KB dla wiadomości kolejki, wpływ powinien być w zarządzaniu.

### <a name="tables"></a>Tabele
Biblioteka klienta obsługuje szyfrowanie właściwości jednostki dla operacji wstawiania i zamieniania.

> [!NOTE]
> Scalanie nie jest obecnie obsługiwane. Ponieważ podzbiór właściwości może być zaszyfrowany wcześniej przy użyciu innego klucza, po prostu scalanie nowych właściwości i aktualizowanie metadanych spowoduje utratę danych. Scalanie wymaga wykonywania dodatkowych wywołań usługi, aby odczytać wcześniej istniejącej jednostki z usługi lub przy użyciu nowego klucza na właściwość, z których oba nie są odpowiednie ze względu na wydajność.
> 
> 

Szyfrowanie danych tabeli działa w następujący sposób:

1. Użytkownicy określają właściwości, które mają być szyfrowane.
2. Biblioteka klienta generuje losowe Inicjowanie Vector (IV) z 16 bajtów wraz z kluczem szyfrowania zawartości losowej (CEK) 32 bajtów dla każdej jednostki i wykonuje szyfrowanie koperty na poszczególnych właściwości, które mają być zaszyfrowane przez wyprowadzanie nowego IV na właściwość. Szyfrowana właściwość jest przechowywana jako dane binarne.
3. Opakowane CEK i niektóre dodatkowe metadane szyfrowania są następnie przechowywane jako dwie dodatkowe właściwości zarezerwowane. Pierwsza zastrzeżona\_właściwość (ClientEncryptionMetadata1) jest właściwość ciągu, który przechowuje informacje o IV, wersja i klucz opakowane. Druga zastrzeżona\_właściwość (ClientEncryptionMetadata2) jest właściwością binarną, która przechowuje informacje o właściwościach, które są szyfrowane. Informacje w tej drugiej\_właściwości (ClientEncryptionMetadata2) jest sama zaszyfrowana.
4. Ze względu na te dodatkowe właściwości zarezerwowane wymagane do szyfrowania użytkownicy mogą mieć teraz tylko 250 właściwości niestandardowych zamiast 252. Całkowity rozmiar jednostki musi być mniejszy niż 1 MB.

   Należy zauważyć, że tylko właściwości ciągu mogą być szyfrowane. Jeśli inne typy właściwości mają być szyfrowane, muszą być konwertowane na ciągi. Zaszyfrowane ciągi są przechowywane w usłudze jako właściwości binarne i są konwertowane z powrotem na ciągi (ciągi nieprzetworzone, a nie właściwości EntityProperties z typem EdmType.STRING) po odszyfrowaniu.

   W przypadku tabel, oprócz zasad szyfrowania, użytkownicy muszą określić właściwości, które mają być szyfrowane. Można to zrobić, przechowując te właściwości w TableEntity obiektów z typem ustawionym na EdmType.STRING i szyfrowanie ustawione na true lub ustawienie encryption_resolver_function na tableservice obiektu. Program rozpoznawania nazw szyfrowania jest funkcją, która przyjmuje klucz partycji, klucz wiersza i nazwę właściwości i zwraca wartość logiczną, która wskazuje, czy ta właściwość powinna być szyfrowana. Podczas szyfrowania biblioteka klienta użyje tych informacji, aby zdecydować, czy właściwość powinna być szyfrowana podczas zapisywania do sieci. Delegat zapewnia również możliwość logiki wokół jak właściwości są szyfrowane. (Na przykład, jeśli X, a następnie zaszyfrować właściwość A; w przeciwnym razie szyfrować właściwości A i B.) Należy zauważyć, że nie jest konieczne podanie tych informacji podczas odczytywania lub wykonywania zapytań jednostek.

### <a name="batch-operations"></a>Operacje wsadowe
Jedna zasada szyfrowania ma zastosowanie do wszystkich wierszy w partii. Biblioteka klienta wewnętrznie wygeneruje nowy losowy IV i losowy CEK na wiersz w partii. Użytkownicy mogą również szyfrować różne właściwości dla każdej operacji w partii, definiując to zachowanie w programze rozpoznawania szyfrowania.
Jeśli partia jest tworzona jako menedżer kontekstu za pomocą metody tableservice batch(), zasady szyfrowania usługi tableservice zostaną automatycznie zastosowane do partii. Jeśli partia jest tworzony jawnie przez wywołanie konstruktora, zasady szyfrowania muszą być przekazywane jako parametr i pozostawić niezmodyfikowane przez okres istnienia partii.
Należy zauważyć, że jednostki są szyfrowane, ponieważ są wstawiane do partii przy użyciu zasad szyfrowania partii (jednostki NIE są szyfrowane w czasie zatwierdzania partii przy użyciu zasad szyfrowania usługi tableservice).

### <a name="queries"></a>Zapytania
> [!NOTE]
> Ponieważ jednostki są szyfrowane, nie można uruchamiać kwerend, które filtrują na właściwości zaszyfrowanej.  Jeśli spróbujesz, wyniki będą niepoprawne, ponieważ usługa będzie próbować porównać zaszyfrowane dane z niezaszyfrowanymi danymi.
> 
> 
> Aby wykonać operacje kwerendy, należy określić program rozpoznawania kluczy, który jest w stanie rozpoznać wszystkie klucze w zestawie wyników. Jeśli jednostka zawarta w wyniku kwerendy nie może zostać rozpoznana dla dostawcy, biblioteka klienta zrzuci błąd. Dla każdej kwerendy, która wykonuje projekcje po stronie serwera, biblioteka\_klienta domyślnie doda \_do wybranych kolumn specjalne właściwości metadanych szyfrowania (ClientEncryptionMetadata1 i ClientEncryptionMetadata2).
> 
> [!IMPORTANT]
> Należy pamiętać o tych ważnych punktach podczas korzystania z szyfrowania po stronie klienta:
> 
> * Podczas odczytywania lub zapisywania do zaszyfrowanego obiektu blob należy używać poleceń przekazywania całych obiektów blob i poleceń pobierania zakresu/całego obiektu blob. Unikaj zapisywania do zaszyfrowanego obiektu blob przy użyciu operacji protokołu, takich jak Put Block, Put Block List, Write Pages lub Clear Pages; w przeciwnym razie możesz uszkodzić zaszyfrowany obiekt blob i uczynić go nieczytelnym.
> * W przypadku tabel istnieje podobne ograniczenie. Należy uważać, aby nie aktualizować zaszyfrowanych właściwości bez aktualizowania metadanych szyfrowania.
> * Jeśli ustawisz metadane na zaszyfrowanym obiekcie blob, możesz zastąpić metadane związane z szyfrowaniem wymagane do odszyfrowywania, ponieważ ustawienie metadanych nie jest addytywne. Dotyczy to również migawek; uniknąć określania metadanych podczas tworzenia migawki zaszyfrowanego obiektu blob. Jeśli metadane muszą być ustawione, należy najpierw wywołać **metodę get_blob_metadata,** aby uzyskać bieżące metadane szyfrowania i uniknąć równoczesnych zapisów podczas ustawiania metadanych.
> * Włącz flagę **require_encryption** w obiekcie usługi dla użytkowników, którzy powinni pracować tylko z zaszyfrowanymi danymi. Zobacz poniżej, aby uzyskać więcej informacji.

Biblioteka klienta magazynu oczekuje, że podany KEK i program rozpoznawania kluczy zaimplementują następujący interfejs. [Obsługa usługi Azure Key Vault](https://azure.microsoft.com/services/key-vault/) dla zarządzania KEK języka Python jest w toku i zostanie zintegrowana z tą biblioteką po zakończeniu.

## <a name="client-api--interface"></a>Interfejs / interfejs klienta
Po utworzeniu obiektu usługi magazynu (tj. blockblobservice) użytkownik może przypisać wartości do pól, które stanowią zasady szyfrowania: key_encryption_key, key_resolver_function i require_encryption. Użytkownicy mogą podać tylko KEK, tylko program rozpoznawania nazw lub oba. key_encryption_key jest podstawowym typem klucza, który jest identyfikowany przy użyciu identyfikatora klucza i który zapewnia logikę zawijania/rozpakowywania. key_resolver_function służy do rozpoznawania klucza podczas procesu odszyfrowywania. Zwraca prawidłowy KEK o podany identyfikator klucza. Dzięki temu użytkownicy mogą wybierać między wieloma kluczami, które są zarządzane w wielu lokalizacjach.

KEK musi zaimplementować następujące metody, aby pomyślnie zaszyfrować dane:

* wrap_key(cek): Zawija określony CEK (bajty) przy użyciu algorytmu wybranego przez użytkownika. Zwraca zawinięty klucz.
* get_key_wrap_algorithm(): Zwraca algorytm używany do zawijania kluczy.
* get_kid(): Zwraca identyfikator klucza ciągu dla tego KEK.
  KEK musi implementować następujące metody, aby pomyślnie odszyfrować dane:
* unwrap_key(cek, algorytm): Zwraca rozpakowany formularz określonego CEK przy użyciu algorytmu określonego ciągu.
* get_kid(): Zwraca identyfikator klucza ciągu dla tego KEK.

Program rozpoznawania kluczy musi przynajmniej implementować metodę, która, biorąc pod uwagę identyfikator klucza, zwraca odpowiednie KEK implementujące interfejs powyżej. Tylko ta metoda ma być przypisany do właściwości key_resolver_function na obiekcie usługi.

* W przypadku szyfrowania klucz jest zawsze używany, a brak klucza spowoduje błąd.
* Do odszyfrowywania:

  * Program rozpoznawania kluczy jest wywoływany, jeśli określono, aby uzyskać klucz. Jeśli program rozpoznawania nazw jest określony, ale nie ma mapowania dla identyfikatora klucza, zgłaszany jest błąd.
  * Jeśli program rozpoznawania nazw nie jest określony, ale klucz jest określony, klucz jest używany, jeśli jego identyfikator jest zgodny z wymaganym identyfikatorem klucza. Jeśli identyfikator nie jest zgodny, zgłaszany jest błąd.

    Przykłady szyfrowania w witrynie azure.storage.samples pokazują bardziej szczegółowy scenariusz end-to-end dla obiektów blob, kolejek i tabel.
      Przykładowe implementacje KEK i program rozpoznawania kluczy znajdują się w przykładowych plikach odpowiednio jako KeyWrapper i KeyResolver.

### <a name="requireencryption-mode"></a>Tryb szyfrowania requireEncryption
Użytkownicy mogą opcjonalnie włączyć tryb działania, w którym wszystkie przesyłanie i pobieranie musi być zaszyfrowane. W tym trybie próby przekazania danych bez zasad szyfrowania lub danych pobierania, które nie są szyfrowane w usłudze, zakończy się niepowodzeniem na kliencie. Flaga **require_encryption** na obiekcie usługi kontroluje to zachowanie.

### <a name="blob-service-encryption"></a>Szyfrowanie usługi obiektów Blob
Ustaw pola zasad szyfrowania na obiekcie blockblobservice. Wszystko inne będzie obsługiwane przez bibliotekę klienta wewnętrznie.

```python
# Create the KEK used for encryption.
# KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
kek = KeyWrapper('local:key1')  # Key identifier

# Create the key resolver used for decryption.
# KeyResolver is the provided sample implementation, but the user may use whatever implementation they choose so long as the function set on the service object behaves appropriately.
key_resolver = KeyResolver()
key_resolver.put_key(kek)

# Set the KEK and key resolver on the service object.
my_block_blob_service.key_encryption_key = kek
my_block_blob_service.key_resolver_funcion = key_resolver.resolve_key

# Upload the encrypted contents to the blob.
my_block_blob_service.create_blob_from_stream(
    container_name, blob_name, stream)

# Download and decrypt the encrypted contents from the blob.
blob = my_block_blob_service.get_blob_to_bytes(container_name, blob_name)
```

### <a name="queue-service-encryption"></a>Szyfrowanie usługi kolejek
Ustaw pola zasad szyfrowania obiektu usługi kolejkowania. Wszystko inne będzie obsługiwane przez bibliotekę klienta wewnętrznie.

```python
# Create the KEK used for encryption.
# KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
kek = KeyWrapper('local:key1')  # Key identifier

# Create the key resolver used for decryption.
# KeyResolver is the provided sample implementation, but the user may use whatever implementation they choose so long as the function set on the service object behaves appropriately.
key_resolver = KeyResolver()
key_resolver.put_key(kek)

# Set the KEK and key resolver on the service object.
my_queue_service.key_encryption_key = kek
my_queue_service.key_resolver_funcion = key_resolver.resolve_key

# Add message
my_queue_service.put_message(queue_name, content)

# Retrieve message
retrieved_message_list = my_queue_service.get_messages(queue_name)
```

### <a name="table-service-encryption"></a>Szyfrowanie usługi tabel
Oprócz tworzenia zasad szyfrowania i ustawiania jej na opcje żądania, należy określić **encryption_resolver_function** w **tableservice**lub ustawić atrybut szyfrowania na EntityProperty.

### <a name="using-the-resolver"></a>Korzystanie z programu rozpoznawania nazw

```python
# Create the KEK used for encryption.
# KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
kek = KeyWrapper('local:key1')  # Key identifier

# Create the key resolver used for decryption.
# KeyResolver is the provided sample implementation, but the user may use whatever implementation they choose so long as the function set on the service object behaves appropriately.
key_resolver = KeyResolver()
key_resolver.put_key(kek)

# Define the encryption resolver_function.


def my_encryption_resolver(pk, rk, property_name):
    if property_name == 'foo':
        return True
    return False


# Set the KEK and key resolver on the service object.
my_table_service.key_encryption_key = kek
my_table_service.key_resolver_funcion = key_resolver.resolve_key
my_table_service.encryption_resolver_function = my_encryption_resolver

# Insert Entity
my_table_service.insert_entity(table_name, entity)

# Retrieve Entity
# Note: No need to specify an encryption resolver for retrieve, but it is harmless to leave the property set.
my_table_service.get_entity(
    table_name, entity['PartitionKey'], entity['RowKey'])
```

### <a name="using-attributes"></a>Korzystanie z atrybutów
Jak wspomniano powyżej, właściwość może być oznaczona do szyfrowania przez przechowywanie go w obiekcie EntityProperty i ustawienie pola szyfrowania.

```python
encrypted_property_1 = EntityProperty(EdmType.STRING, value, encrypt=True)
```

## <a name="encryption-and-performance"></a>Szyfrowanie i wydajność
Należy zauważyć, że szyfrowanie danych magazynu powoduje dodatkowe obciążenie wydajności. Klucz zawartości i IV muszą być generowane, sama zawartość musi być zaszyfrowana, a dodatkowe metadane muszą być sformatowane i przekazane. To obciążenie będzie się różnić w zależności od ilości zaszyfrowanych danych. Zaleca się, aby klienci zawsze testowali swoje aplikacje pod kątem wydajności podczas tworzenia.

## <a name="next-steps"></a>Następne kroki
* Pobierz [pakiet Biblioteka klienta usługi Azure Storage dla środowiska Java PyPi](https://pypi.python.org/pypi/azure-storage)
* Pobierz [bibliotekę klienta usługi Azure Storage dla kodu źródłowego języka Python z usługi GitHub](https://github.com/Azure/azure-storage-python)
