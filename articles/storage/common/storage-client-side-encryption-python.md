---
title: Szyfrowanie po stronie klienta za pomocą języka Python dla usługi Microsoft Azure Storage | Dokumentacja firmy Microsoft
description: Biblioteki klienta usługi Azure Storage dla języka Python obsługuje szyfrowanie po stronie klienta dla zapewnienia maksymalnego poziomu bezpieczeństwa dla aplikacji usługi Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.devlang: python
ms.topic: article
ms.date: 05/11/2017
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: d04c1e137a190b01554106c041853aa2fd6786d7
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65146912"
---
# <a name="client-side-encryption-with-python-for-microsoft-azure-storage"></a>Szyfrowanie po stronie klienta za pomocą języka Python dla usługi Microsoft Azure Storage
[!INCLUDE [storage-selector-client-side-encryption-include](../../../includes/storage-selector-client-side-encryption-include.md)]

## <a name="overview"></a>Omówienie
[Biblioteki klienta usługi Azure Storage dla języka Python](https://pypi.python.org/pypi/azure-storage) obsługuje szyfrowanie danych w aplikacjach klienckich przed przekazaniem do usługi Azure Storage oraz odszyfrowywanie danych pobraniem do klienta.

> [!NOTE]
> Biblioteki Azure Storage dla środowiska Python jest dostępna w wersji zapoznawczej.
> 
> 

## <a name="encryption-and-decryption-via-the-envelope-technique"></a>Szyfrowanie i odszyfrowywanie za pomocą techniki obwiedni
Procesy szyfrowania i odszyfrowywania wykonaj technika koperty.

### <a name="encryption-via-the-envelope-technique"></a>Szyfrowanie za pomocą techniki obwiedni
Szyfrowanie za pomocą techniki koperty działa w następujący sposób:

1. Biblioteka klienta usługi Azure storage generuje klucz szyfrowania zawartości (CEK), czyli jeden jednorazowej Użyj klucza symetrycznego.
2. Dane użytkownika są szyfrowane przy użyciu tego CEK.
3. CEK jest następnie opakowywany (zaszyfrowane) przy użyciu klucza szyfrowania klucza (KEK). Klucza KEK jest identyfikowany przez identyfikator klucza i może być asymetryczną parę kluczy lub klucz symetryczny odbywa się lokalnie.
   Sama biblioteka klienta magazynu nigdy nie ma dostępu do klucza KEK. Biblioteka wywołuje algorytmu zawijanie kluczy, który jest dostarczany przez klucza KEK. Użytkownicy mogą wybierać niestandardowych dostawców dla klucza zawijania/rozpakowanie w razie potrzeby.
4. Zaszyfrowane dane są następnie przekazywane do usługi Azure Storage. Opakowana klucz wraz z metadanymi pewne dodatkowe szyfrowanie jest przechowywane w postaci metadanych (na obiekcie blob) albo interpolowane z zaszyfrowanych danych (wiadomości w kolejce i jednostki z tabeli).

### <a name="decryption-via-the-envelope-technique"></a>Odszyfrowywanie za pomocą techniki obwiedni
Odszyfrowywanie za pomocą techniki koperty działa w następujący sposób:

1. Biblioteka klienta zakłada, że użytkownik jest zarządzany lokalnie klucz szyfrowania klucza (KEK). Użytkownik nie musi wiedzieć, określony klucz, który został użyty do szyfrowania. Zamiast tego klucza rozpoznawania nazw, który jest rozpoznawany jako różne identyfikatorach klucza kluczy, można skonfigurować i używane.
2. Biblioteka klienta pobierze zaszyfrowanych danych wraz z materiału szyfrowania, który jest przechowywany w usłudze.
3. Klucz opakowana szyfrowania zawartości (CEK) jest nieopakowanych (odszyfrowany) przy użyciu klucza szyfrowania klucza (KEK). W tym miejscu ponownie, Biblioteka klienta nie ma dostępu do klucza KEK. Po prostu wywołuje niestandardowego dostawcy odszyfrowania algorytmu.
4. Klucz szyfrowania zawartości (CEK) jest następnie używany do odszyfrowywania danych zaszyfrowanych użytkownika.

## <a name="encryption-mechanism"></a>Mechanizm szyfrowania
Korzysta z biblioteki klienta usługi storage [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) w celu zaszyfrowania danych użytkownika. W szczególności [Cipher Block Chaining (CBC)](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher-block_chaining_.28CBC.29) trybu przy użyciu standardu AES. Każdy usługi działa nieco inaczej, omówimy każdy z nich w tym miejscu.

### <a name="blobs"></a>Obiekty blob
Biblioteka klienta obsługuje obecnie szyfrowanie tylko całe obiektów blob. Ściślej mówiąc, szyfrowanie jest obsługiwane, gdy użytkownicy używają **tworzenie*** metody. Pliki do pobrania, zarówno pełne i pobrania zakresu są obsługiwane i równoległe przekazywanie i pobieranie jest dostępna.

Podczas szyfrowania biblioteki klienta wygeneruje losowy inicjowania wektora (IV) 16-bajtowy, wraz z kluczem szyfrowania zawartości (CEK), 32-bitowej i wykonać koperty szyfrowanie danych obiektów blob, korzystając z tych informacji. Opakowana CEK i niektóre metadane dodatkowego szyfrowania są następnie zapisywane jako metadane, wraz z zaszyfrowanego obiektu blob w usłudze obiektu blob.

> [!WARNING]
> W przypadku edytowania lub przekazywanie metadane obiektu blob, należy upewnić się, że te metadane są zachowywane. Jeśli załadujesz nowymi metadanymi bez takich metadanych, CEK zawinięty, IV i inne metadane zostaną utracone, a zawartość obiektu blob nigdy nie być możliwe do pobierania.
> 
> 

Pobieranie obiektu blob zaszyfrowanego polega na pobraniu zawartości całego obiektu blob przy użyciu **uzyskać*** wygodne metody. Opakowana CEK jest nieopakowanych i używany razem z IV (przechowywanych jako metadane obiektu blob w tym przypadku), aby zwrócić odszyfrowane dane do użytkowników.

Pobranie dowolnego zakresu (**uzyskać*** przekazanej metody z parametrami zakres) w obiekcie blob zaszyfrowanego obejmuje, dostosowując zakres dostarczone przez użytkowników w celu uzyskania niewielką ilość dodatkowych danych, który może służyć do pomyślnie odszyfrowywanie żądany zakres.

Blokowe obiekty BLOB i stronicowe obiekty BLOB tylko może być zaszyfrowany/odszyfrować za pomocą tego schematu. Obecnie nie jest obsługiwane w przypadku encrypting uzupełnialnych obiektów blob.

### <a name="queues"></a>Kolejki
Ponieważ kolejka komunikatów może być dowolnym formacie, Biblioteka klienta definiuje niestandardowego formatu, który zawiera wektor inicjowania (IV) i klucza zaszyfrowanego szyfrowania zawartości (CEK) w treści wiadomości.

Podczas szyfrowania biblioteki klienckiej generuje losowe IV 16-bajtowy wraz z losowe CEK 32-bitowej i wykonuje szyfrowanie koperty tekst komunikatu kolejki, korzystając z tych informacji. Opakowana CEK i niektóre metadane dodatkowego szyfrowania jest następnie dodawana do komunikatu w kolejce zaszyfrowane. Ten komunikat zmodyfikowany (pokazana poniżej) są przechowywane w usłudze.

```
<MessageText>{"EncryptedMessageContents":"6kOu8Rq1C3+M1QO4alKLmWthWXSmHV3mEfxBAgP9QGTU++MKn2uPq3t2UjF1DO6w","EncryptionData":{…}}</MessageText>
```

Podczas odszyfrowywania opakowaną klucza jest wyodrębniony z komunikatu w kolejce i nieopakowanych. IV jest również wyodrębniony z komunikatu w kolejce i używany wraz z kluczem odkodowany do odszyfrowania danych komunikatu kolejki. Należy pamiętać, że metadane szyfrowania małe (w bajtach 500), dlatego podczas jego wliczają się do limitu 64KB dla komunikatu w kolejce, wpływ powinny być łatwe w zarządzaniu.

### <a name="tables"></a>Tabele
Biblioteka klienta obsługuje szyfrowanie właściwości obiektu do wstawiania i zamienianie operacji.

> [!NOTE]
> Scalanie nie jest obecnie obsługiwane. Ponieważ podzbiór właściwości może być zaszyfrowany wcześniej przy użyciu innego klucza, po prostu scalanie nowych właściwości i aktualizowanie metadanych spowoduje utratę danych. Scalanie albo wymaga wywołań usługi dodatkowe odczytać wstępnie istniejącej jednostki usługi lub przy użyciu nowego klucza dla właściwości, które nie są odpowiednie dla ze względu na wydajność.
> 
> 

Szyfrowanie danych tabeli działa w następujący sposób:

1. Użytkownicy określić właściwości, które mają być szyfrowane.
2. Biblioteka klienta generuje losowe inicjowania wektora (IV) 16-bajtowy wraz z kluczem szyfrowania zawartości (CEK) 32-bitowej dla każdej jednostki i wykonuje szyfrowanie koperty na poszczególnych właściwości w celu zaszyfrowania przez wyprowadzanie nowy IV dla właściwości. Zaszyfrowane właściwości jest przechowywana jako dane binarne.
3. Opakowana CEK i niektóre metadane dodatkowego szyfrowania są następnie zapisywane jako dwa dodatkowe właściwości zastrzeżonych. Pierwsze zastrzeżone właściwości (\_ClientEncryptionMetadata1) jest właściwością ciągu, która przechowuje informacje dotyczące IV, wersji i klucz opakowana. Drugi zastrzeżone właściwości (\_ClientEncryptionMetadata2) jest binarny właściwość, która przechowuje informacje dotyczące właściwości, które są szyfrowane. Informacje przedstawione w tej drugiej właściwości (\_ClientEncryptionMetadata2) jest zaszyfrowany.
4. Ze względu na te dodatkowe właściwości zastrzeżonych wymagana na potrzeby szyfrowania użytkownicy mogą teraz mieć tylko 250 właściwości niestandardowych zamiast 252. Całkowity rozmiar jednostki musi być mniejszy niż 1MB.

   Należy zauważyć, że tylko ciąg właściwości mogą być szyfrowane. Inne typy właściwości mają być szyfrowane, musi zostać przekonwertowana na ciągi. Zaszyfrowane ciągi są przechowywane w usłudze jako właściwości binarnych i są konwertowane do ciągi (nieprzetworzone ciągi, nie EntityProperties z typem EdmType.STRING) po odszyfrowywania.

   W przypadku tabel, oprócz zasad szyfrowania użytkownicy muszą określić właściwości, które mają być szyfrowane. To jest możliwe dzięki przechowywaniu tych właściwości w obiektach TableEntity z wartością typu EdmType.STRING i szyfrowania ma wartość true lub ustawienie encryption_resolver_function obiektu tableservice. Mechanizm rozpoznawania szyfrowania jest funkcją, która przyjmuje klucza partycji, klucz wiersza i nazwę właściwości i zwraca wartość boolean wskazującą, czy mają być szyfrowane tej właściwości. Podczas szyfrowania biblioteki klienckiej użyje tych informacji do określania, czy właściwości mają być szyfrowane podczas zapisu w sieci. Delegat udostępnia także możliwości logiki wokół jak zaszyfrowane właściwości. (Na przykład, jeśli X, następnie szyfrować właściwość A; w przeciwnym razie szyfrowanie właściwości, A i B.) Należy pamiętać, że nie jest konieczne może przekazać tę informację podczas odczytywania lub podczas badania jednostki.

### <a name="batch-operations"></a>Operacji wsadowych
Zasady szyfrowania ma zastosowanie do wszystkich wierszy w partii. Biblioteki klienta wewnętrznie wygeneruje nowy IV losowych i losowe CEK poszczególnych wierszy w partii. Użytkowników można również szyfrowania różnych właściwości dla każdej operacji w zadaniu wsadowym, definiując to zachowanie w szyfrowania programu rozpoznawania nazw.
Jeśli usługi batch zostało utworzone jako menedżera kontekstowego za pośrednictwem metody batch() tableservice, zasady szyfrowania tableservice zostaną automatycznie zastosowane do danego wsadu. Partii jest jawnie tworzony przez wywołanie konstruktora, zasady szyfrowania należy przekazać jako parametru i lewej w niezmienionej postaci okres istnienia zadania wsadowego.
Należy pamiętać, że jednostki są szyfrowane, ponieważ są one wstawiane do usługi batch za pomocą zasad szyfrowania usługi batch (jednostki nie są szyfrowane w czasie zatwierdzania usługi batch za pomocą zasad szyfrowania tableservice).

### <a name="queries"></a>Zapytania
> [!NOTE]
> Ponieważ obiekty są szyfrowane, nie można uruchomić zapytania, które filtrują na właściwością szyfrowaną.  Przy próbie wyniki będą nieprawidłowe, ponieważ usługa może podjąć próbę porównać dane zaszyfrowane przy użyciu nieszyfrowanego danych.
> 
> 
> Aby wykonać operacje zapytań, należy określić klucza programu rozpoznawania nazw, który jest w stanie rozpoznać wszystkich kluczy w zestawie wyników. Biblioteka klienta będzie sygnalizować błąd, jeśli nie można rozpoznać jednostki zawarte w wyniku zapytania do dostawcy. Dla dowolnego zapytania, który wykonuje projekcje po stronie serwera biblioteki klienckiej spowoduje dodanie właściwości metadanych szyfrowania specjalne (\_ClientEncryptionMetadata1 i \_ClientEncryptionMetadata2), aby domyślnie zaznaczonych kolumnach.
> 
> [!IMPORTANT]
> Należy pamiętać o te ważne punkty, gdy za pomocą szyfrowania po stronie klienta:
> 
> * Podczas odczytywania ze zmiennej czy zapisujemy do obiektu blob zaszyfrowanego, użyj całego obiektu blob przekazywania oraz poleceń pobierania obiektów blob zakresu/całości. Należy unikać zapisywanie obiektu blob zaszyfrowanego przy użyciu protokołu operacje, takie jak umieścić blok, umieść zablokowanych, strony zapisu lub wyczyść stron. w przeciwnym razie może uszkodzić zaszyfrowany obiekt blob i ułatwiają nie można go odczytać.
> * W przypadku tabel istnieje ograniczenie podobne. Uważaj nie aktualizować właściwości zaszyfrowane bez aktualizowania metadanych szyfrowania.
> * Jeśli ustawisz metadane w obiekcie blob zaszyfrowanego może zastąpić metadane związane z szyfrowaniem, wymaganego do odszyfrowywania, ponieważ ustawienie metadanych nie jest dodatek. Dotyczy to również migawki Unikaj określania metadanych podczas tworzenia migawki obiektu blob zaszyfrowany. Jeśli należy ustawić metadane, należy wywołać **get_blob_metadata** metodą najpierw uzyskać bieżące metadane szyfrowania i uniknąć współbieżne operacje podczas ustawiania metadanych.
> * Włącz **require_encryption** flagi na obiekt usługi dla użytkowników, którzy powinien działać wyłącznie z zaszyfrowanych danych. Aby uzyskać więcej informacji, zobacz poniżej.

Biblioteka klienta magazynu oczekuje, że podana KEK oraz rozpoznawania klucza implementacji interfejsu. [Usługa Azure Key Vault](https://azure.microsoft.com/services/key-vault/) pomoc techniczna dotycząca zarządzania KEK języka Python jest w stanie oczekiwania i zostanie zintegrowana z tej biblioteki, po zakończeniu.

## <a name="client-api--interface"></a>Interfejs API klienta / interfejs
Po utworzeniu obiektu usługi storage (tj. blockblobservice), użytkownik może przypisać wartości do pola, które stanowią zasady szyfrowania: key_encryption_key, key_resolver_function i require_encryption. Użytkownicy mogą podać tylko KEK tylko program rozpoznawania nazw, lub obu. key_encryption_key jest podstawowego typu klucza, który jest identyfikowany za pomocą identyfikatora klucza i zapewniający logikę zawijania/rozpakowanie. key_resolver_function jest używany do rozpoznawania klucza podczas odszyfrowywania. Zwraca prawidłową KEK, biorąc pod uwagę identyfikatora klucza. Zapewnia to użytkownikom wybór między wiele kluczy, które są zarządzane w wielu lokalizacjach.

Klucza KEK należy zaimplementować następujące metody umożliwiające pomyślnie szyfrowania danych:

* wrap_key(cek): Opakowuje określony CEK (w bajtach), korzystając z algorytmu wybrany przez użytkownika. Zwraca klucz opakowana.
* get_key_wrap_algorithm(): Zwraca algorytm stosowanego do opakowywania kluczy.
* get_kid(): Zwraca identyfikator klucza ciągu dla tego klucza KEK.
  Klucza KEK należy zaimplementować następujące metody umożliwiające pomyślnie odszyfrować danych:
* unwrap_key (cek, algorytm): Zwraca nieopakowane formularza CEK określony, przy użyciu algorytmu określony ciąg.
* get_kid(): Zwraca ciąg identyfikatora klucza dla tego klucza KEK.

Program rozpoznawania nazw kluczy co najmniej musi implementować metodę zwracającą, biorąc pod uwagę identyfikator klucza, odpowiedniego klucza KEK implementującej interfejs powyżej. Jest tylko ta metoda ma być przypisane do właściwości key_resolver_function obiektu usługi.

* Dla celów szyfrowania jest on używany zawsze i Brak klucza spowoduje wystąpienie błędu.
* Do odszyfrowywania:

  * Mechanizm rozpoznawania klucza jest wywoływana, jeśli określony, aby pobrać klucz. Jeśli program rozpoznawania nazw jest określony, ale nie ma mapowania dla identyfikatora klucza, zostanie zgłoszony błąd.
  * Jeśli program rozpoznawania nazw nie jest określony, ale klucz jest określony, klucz jest używany, jeśli pasuje do identyfikatora wymaganego identyfikatora klucza. Jeśli identyfikator nie jest zgodny, zostanie zgłoszony błąd.

    Przykłady szyfrowania w azure.storage.samples pokazują bardziej szczegółowe scenariusz end-to-end dla obiektów blob, kolejek i tabel.
      Przykładowe implementacje KEK i rozpoznawania nazw kluczy są udostępniane w przykładowych plików oraz KeyWrapper KeyResolver odpowiednio.

### <a name="requireencryption-mode"></a>Tryb RequireEncryption
Użytkownicy mogą włączyć opcjonalnie tryb działania, w której muszą być szyfrowane wszystkie przekazywanie i pobieranie. W tym trybie próby przekazania danych bez zasady szyfrowania lub pobrać dane, które nie są szyfrowane w usłudze zakończy się niepowodzeniem na komputerze klienckim. **Require_encryption** flagi na obiekt usługi kontroluje to zachowanie.

### <a name="blob-service-encryption"></a>Szyfrowanie usługi obiektów blob
Nastavit szyfrowania zasad pól obiektu blockblobservice. Cała reszta będzie obsługiwany przez bibliotekę klienta wewnętrznie.

```python
# Create the KEK used for encryption.
# KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
kek = KeyWrapper('local:key1') # Key identifier

# Create the key resolver used for decryption.
# KeyResolver is the provided sample implementation, but the user may use whatever implementation they choose so long as the function set on the service object behaves appropriately.
key_resolver = KeyResolver()
key_resolver.put_key(kek)

# Set the KEK and key resolver on the service object.
my_block_blob_service.key_encryption_key = kek
my_block_blob_service.key_resolver_funcion = key_resolver.resolve_key

# Upload the encrypted contents to the blob.
my_block_blob_service.create_blob_from_stream(container_name, blob_name, stream)

# Download and decrypt the encrypted contents from the blob.
blob = my_block_blob_service.get_blob_to_bytes(container_name, blob_name)
```

### <a name="queue-service-encryption"></a>Szyfrowanie usługi kolejki
Ustawić szyfrowania zasad pól queueservice obiektu. Cała reszta będzie obsługiwany przez bibliotekę klienta wewnętrznie.

```python
# Create the KEK used for encryption.
# KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
kek = KeyWrapper('local:key1') # Key identifier

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

### <a name="table-service-encryption"></a>Szyfrowanie usługi tabeli
Oprócz tworzenia zasady szyfrowania i ustawienie opcji żądania, należy określić **encryption_resolver_function** na **tableservice**, lub ustaw atrybut Szyfruj w EntityProperty.

### <a name="using-the-resolver"></a>Przy użyciu rozpoznawania nazw

```python
# Create the KEK used for encryption.
# KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
kek = KeyWrapper('local:key1') # Key identifier

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
my_table_service.get_entity(table_name, entity['PartitionKey'], entity['RowKey'])
```

### <a name="using-attributes"></a>Przy użyciu atrybutów
Jak wspomniano powyżej, może być oznaczony właściwością szyfrowania dzięki przechowywaniu go w obiekcie EntityProperty i ustawienie pola Szyfruj.

```python
encrypted_property_1 = EntityProperty(EdmType.STRING, value, encrypt=True)
```

## <a name="encryption-and-performance"></a>Szyfrowanie i wydajności
Należy pamiętać, że szyfrowanie usługi magazynu danych spowoduje zmniejszenie wydajności. Klucz zawartości i IV musi zostać wygenerowany samej zawartości, które muszą być szyfrowane i musi być sformatowany i przekazać dodatkowe metadane. Ten narzut różnią się zależnie od ilości danych, są szyfrowane. Firma Microsoft zaleca, aby klienci zawsze testują wydajności podczas tworzenia.

## <a name="next-steps"></a>Kolejne kroki
* Pobierz [biblioteki klienta usługi Azure Storage dla języka Java PyPi pakietu](https://pypi.python.org/pypi/azure-storage)
* Pobierz [biblioteki klienta usługi Azure Storage dla języka Python źródła kodu z repozytorium GitHub](https://github.com/Azure/azure-storage-python)
