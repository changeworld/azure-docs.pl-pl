---
title: Szyfrowanie po stronie klienta z języka Python dla usługi Microsoft Azure Storage | Dokumentacja firmy Microsoft
description: Biblioteka klienta magazynu Azure dla języka Python obsługuje szyfrowanie po stronie klienta dla zapewnienia maksymalnego poziomu bezpieczeństwa dla aplikacji usługi Azure Storage.
services: storage
documentationcenter: python
author: lakasa
manager: jahogg
editor: tysonn
ms.assetid: f9bf7981-9948-4f83-8931-b15679a09b8a
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 05/11/2017
ms.author: lakasa
ms.openlocfilehash: c925b41d1654bd5c9b40438c4b6b9f402ec4bac2
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2018
ms.locfileid: "29742646"
---
# <a name="client-side-encryption-with-python-for-microsoft-azure-storage"></a>Szyfrowanie po stronie klienta z języka Python dla magazynu Microsoft Azure
[!INCLUDE [storage-selector-client-side-encryption-include](../../../includes/storage-selector-client-side-encryption-include.md)]

## <a name="overview"></a>Przegląd
[Biblioteki klienta usługi Azure Storage dla języka Python](https://pypi.python.org/pypi/azure-storage) obsługuje szyfrowanie danych w aplikacjach klienckich przed przekazaniem do usługi Azure Storage i odszyfrowywania danych podczas pobierania do klienta.

> [!NOTE]
> Biblioteka języka Python magazynu Azure znajduje się w wersji zapoznawczej.
> 
> 

## <a name="encryption-and-decryption-via-the-envelope-technique"></a>Szyfrowanie i odszyfrowywanie za pomocą techniki koperty
Procesy szyfrowania i odszyfrowywania wykonaj technika koperty.

### <a name="encryption-via-the-envelope-technique"></a>Szyfrowanie za pomocą techniki koperty
Szyfrowanie za pomocą techniki koperty działa w następujący sposób:

1. Biblioteka klienta magazynu Azure generuje klucz szyfrowania zawartości (CEK), jest jeden jednorazowej użycia klucza symetrycznego.
2. Dane użytkownika są szyfrowane przy użyciu tego CEK.
3. CEK jest następnie opakowane (zaszyfrowane) przy użyciu klucza szyfrowania klucza (KEK). KEK jest identyfikowany przez identyfikator klucza i może być parę klucza asymetrycznego lub klucza symetrycznego, które jest zarządzane lokalnie.
   Biblioteka klienta magazynu się nigdy nie ma dostęp do KEK. Biblioteka wywołuje Algorytm zawijania klucza dostarczonego przez KEK. Użytkownicy mogą wybierać dostawców niestandardowych dla klucza zawijania/odkodowywania w razie potrzeby.
4. Zaszyfrowane dane są następnie przekazywane do usługi Magazyn Azure. Opakowana klucz wraz ze niektóre metadane dodatkowego szyfrowania jest przechowywane jako metadanych (dla obiektu blob) albo interpolowane z zaszyfrowanych danych (wiadomości w kolejce i jednostek tabeli).

### <a name="decryption-via-the-envelope-technique"></a>Odszyfrowywanie przy użyciu techniki koperty
Odszyfrowywanie przy użyciu techniki koperty działa w następujący sposób:

1. Biblioteka klienta przyjęto założenie, że użytkownik zarządza klucz szyfrowania klucza (KEK) lokalnie. Użytkownik nie musi wiedzieć określonego klucza, który był używany do szyfrowania. Zamiast tego klucza rozpoznawania nazw, który jest rozpoznawany jako różne identyfikatory klucza klucze, można skonfigurować i używane.
2. Biblioteka klienta pobierze zaszyfrowanych danych wraz z materiał szyfrowania, który jest przechowywany w usłudze.
3. Klucz szyfrowania zawartości opakowana (CEK) znajduje się bez otoki (odszyfrowane) przy użyciu klucza szyfrowania klucza (KEK). W tym miejscu ponownie, Biblioteka klienta nie ma dostępu do KEK. Wywołuje po prostu niestandardowego dostawcy odszyfrowania algorytmu.
4. Klucz szyfrowania zawartości (CEK) jest następnie używany do odszyfrowywania danych zaszyfrowanych użytkownika.

## <a name="encryption-mechanism"></a>Mechanizm szyfrowania
Biblioteka klienta magazynu używa [AES](http://en.wikipedia.org/wiki/Advanced_Encryption_Standard) w celu zaszyfrowania danych użytkownika. W szczególności [Cipher Block Chaining (CBC)](http://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher-block_chaining_.28CBC.29) trybu z użyciem standardu AES. Każdy usługi działa nieco inaczej, dlatego omówimy każdego z nich w tym miejscu.

### <a name="blobs"></a>Obiekty blob
Biblioteka klienta obecnie obsługuje szyfrowanie całego obiektów blob tylko. W szczególności, szyfrowanie jest obsługiwane, gdy użytkownicy używają **utworzyć*** metody. Obsługiwane są pliki do pobrania, zarówno pełne i pobieranie zakresu, i paralelizacja przekazywania i pobierania jest dostępna.

Podczas szyfrowania biblioteki klienckiej generowania losowego inicjowania wektora (IV) 16 bajtów oraz klucz szyfrowania zawartości (CEK) 32 bajtów i wykonać koperty szyfrowanie danych obiektów blob, korzystając z tych informacji. Opakowana CEK i niektóre metadane szyfrowania dodatkowe następnie są przechowywane jako obiektów blob metadane wraz z zaszyfrowanego obiektu blob w usłudze.

> [!WARNING]
> W przypadku edytowania lub przekazywanie metadanych dla obiektu blob, należy upewnij się, że te metadane są zachowywane. Po wysłaniu nowej metadanych bez takich metadanych opakowana CEK, IV i inne metadane zostaną utracone i zawartości obiektu blob nigdy nie być pobieranie.
> 
> 

Pobieranie zaszyfrowanej blob polega na pobraniu zawartości przy użyciu całego obiektu blob **uzyskać*** podręczne metody. Opakowana CEK następnie rozpakować i używać razem z IV (przechowywane jako metadane obiektu blob w tym przypadku), aby przywrócić odszyfrowane dane użytkowników.

Pobieranie z dowolnego zakresu (**uzyskać*** przekazano metody z parametrami zakresu) w obiekcie blob zaszyfrowane obejmuje Dopasowywanie zakresu dostarczone przez użytkowników w celu pobrania niewielkie dodatkowych danych, który może służyć do odszyfrowywania pomyślnie żądany zakres.

Blokowe i stronicowe obiekty BLOB tylko może być szyfrowane/odszyfrować za pomocą tego systemu. Obecnie nie jest obsługiwane dla szyfrowania uzupełnialnych obiektów blob.

### <a name="queues"></a>Kolejki
Ponieważ wiadomości w kolejce może być dowolnym formacie, Biblioteka klienta definiuje formatu niestandardowego, który obejmuje wektor inicjowania (IV) oraz klucza zaszyfrowanego szyfrowania zawartości (CEK) w treści wiadomości.

Podczas szyfrowania biblioteki klienckiej generuje losowe IV 16 bajtów wraz z losowe CEK 32 bajtów i wykonuje koperty szyfrowania treści wiadomości kolejki, korzystając z tych informacji. Opakowana CEK i niektóre metadane dodatkowego szyfrowania jest następnie dodawana do komunikatu w kolejce zaszyfrowane. Ten komunikat modyfikacji (pokazana poniżej) są przechowywane w usłudze.

```
<MessageText>{"EncryptedMessageContents":"6kOu8Rq1C3+M1QO4alKLmWthWXSmHV3mEfxBAgP9QGTU++MKn2uPq3t2UjF1DO6w","EncryptionData":{…}}</MessageText>
```

Podczas odszyfrowywania opakowana klucz zostaje wyodrębniony z komunikatu w kolejce i nie opakowanych. IV jest również wyodrębniony z komunikatu w kolejce i używać razem z kluczem odkodowany do odszyfrowania danych kolejki wiadomości. Należy pamiętać, że metadane szyfrowania mały (w obszarze 500 bajtów), więc podczas jego wliczane limit 64KB komunikatu w kolejce, wpływ powinno być łatwe w zarządzaniu.

### <a name="tables"></a>Tabele
Biblioteka klienta obsługuje szyfrowanie właściwości obiektów w operacjach wstawiania i zamienianie operacji.

> [!NOTE]
> Scalanie nie jest obecnie obsługiwane. Ponieważ podzbiór właściwości może być zaszyfrowany wcześniej za pomocą innego klucza, po prostu scalanie nowych właściwości i aktualizowania metadanych spowoduje utratę danych. Scalanie albo wymaga wywołań usługi dodatkowe odczytać istniejące jednostki z usługi lub przy użyciu nowego klucza dla właściwości, które nie są odpowiednie ze względu na wydajność.
> 
> 

Szyfrowanie danych tabeli działa w następujący sposób:

1. Użytkownicy określić właściwości, które mają być szyfrowane.
2. Biblioteka klienta generuje losowe inicjowania wektora (IV) 16 bajtów oraz klucza szyfrowania zawartości (CEK) 32 bajtów dla każdej jednostki i wykonuje szyfrowanie koperty na poszczególnych właściwości w celu zaszyfrowania przez wyprowadzanie nowy IV dla właściwości. Zaszyfrowane właściwości są przechowywane jako dane binarne.
3. Opakowana CEK i niektóre metadane szyfrowania dodatkowe następnie są przechowywane jako dwa dodatkowe właściwości zastrzeżone. Właściwości zastrzeżone pierwszy (\_ClientEncryptionMetadata1) jest właściwością ciągu, która przechowuje informacje dotyczące IV, wersji i klucz opakowana. Właściwości zastrzeżone drugiego (\_ClientEncryptionMetadata2) jest właściwość binarną, która przechowuje informacje o właściwościach, które są szyfrowane. Informacje przedstawione w tej drugiej właściwości (\_ClientEncryptionMetadata2) jest zaszyfrowany.
4. Z powodu te dodatkowe właściwości zastrzeżone wymagana na potrzeby szyfrowania użytkownicy mogą teraz mieć tylko 250 właściwości niestandardowych zamiast 252. Całkowity rozmiar jednostki musi być mniejszy niż 1MB.
   
   Należy pamiętać, że tylko właściwości parametrów mogą być szyfrowane. Inne typy właściwości mają być szyfrowane, musi zostać przekonwertowana na ciągi. Zaszyfrowane ciągi są przechowywane w usłudze jako właściwości binarnych i są one konwertowana z powrotem do ciągi (nieprzetworzone ciągi, nie EntityProperties z typem EdmType.STRING) po odszyfrowywania.
   
   W przypadku tabel, oprócz zasad szyfrowania użytkownicy muszą określić właściwości, które mają być szyfrowane. To może odbywać się przez zapisanie tych właściwości w obiektach TableEntity z wartością typu EdmType.STRING i szyfrowania ma wartość true lub ustawienie encryption_resolver_function dla obiekt tableservice. Rozwiązywanie problemów z szyfrowania to funkcja, która przyjmuje klucza partycji, klucz wiersza i nazwy właściwości i zwraca wartość boolean wskazującą, czy ma być szyfrowana tej właściwości. Podczas szyfrowania biblioteki klienckiej użyje tych informacji do określania, czy właściwości powinny być szyfrowane podczas zapisywania można przewodowo. Delegat także możliwość logiki wokół jak właściwości są szyfrowane. (Na przykład, jeśli X, następnie szyfrować właściwości A; w przeciwnym razie szyfrowania właściwości A i B.) Należy pamiętać, że nie jest niezbędne do zapewnienia tych informacji podczas odczytywania lub zapytanie jednostki.

### <a name="batch-operations"></a>Operacje w trybie wsadowym
Jedne zasady szyfrowania ma zastosowanie do wszystkich wierszy w partii. Biblioteka klienta wewnętrznie wygeneruje nowy losowych IV i losowe CEK wierszu w partii. Użytkowników można również zaszyfrować inne właściwości dla każdej operacji w partii, definiując to zachowanie w szyfrowania programu rozpoznawania nazw.
Jeśli plik wsadowy jest tworzony jako menedżera kontekstu za pomocą metody batch() tableservice, zasady szyfrowania tableservice zostaną automatycznie zastosowane do wykonywania zadania wsadowego. Plik wsadowy jest jawnie utworzone przez wywołanie konstruktora, zasady szyfrowania należy przekazać jako parametru i lewej nie mają być modyfikowane przez czas ich istnienia partii.
Należy pamiętać, że jednostki są szyfrowane, ponieważ są one wstawiane do partii za pomocą zasad szyfrowania wykonywania zadania wsadowego (jednostek nie są szyfrowane w czasie zatwierdzania partii za pomocą zasad szyfrowania tableservice).

### <a name="queries"></a>Zapytania
> [!NOTE]
> Ponieważ jednostek są szyfrowane, nie można uruchomić zapytania, które filtrować we właściwości zaszyfrowanej.  Jeśli spróbujesz, wyniki będą niepoprawne, ponieważ usługa będzie próby porównania zaszyfrowane dane z niezaszyfrowanych danych.
> 
>
Aby wykonać operacje zapytań, należy określić klucza programu rozpoznawania nazw, która jest w stanie rozwiązać wszystkich kluczy w zestawie wyników. Jeśli nie można rozpoznać jednostki zawarty w wyniku zapytania do dostawcy, biblioteki klienckiej zgłosi błąd. Dla dowolnego zapytania, który wykonuje projekcje po stronie serwera, Biblioteka klienta spowoduje dodanie właściwości metadanych szyfrowania specjalne (\_ClientEncryptionMetadata1 i \_ClientEncryptionMetadata2), aby domyślnie wybranych kolumn.

> [!IMPORTANT]
> Należy pamiętać o tych punktów ważne przy użyciu szyfrowania po stronie klienta:
> 
> * Gdy odczyt lub zapis do obiektu blob zaszyfrowane, użyj całego obiektu blob przekazywania oraz poleceń pobieranie obiektu blob zakresu/całości. Należy unikać pisania do obiektu blob zaszyfrowane za pomocą protokołu operacji, takich jak Put bloku, umieść listy zablokowanych, zapisu stron lub wyczyść stron. w przeciwnym razie możesz uszkodzony zaszyfrowany obiekt blob i stał się niemożliwe do odczytania.
> * W przypadku tabel istnieje ograniczenie podobne. Należy zachować ostrożność nie aktualizować zaszyfrowane właściwości bez aktualizowania metadanych szyfrowania.
> * Jeśli ustawisz metadanych w obiekcie blob zaszyfrowane, mogą zastąpić metadane związane z szyfrowaniem wymaganych do odszyfrowania, ponieważ ustawienie metadanych nie jest dodatek. Dotyczy to również migawki; Unikaj określania metadanych podczas tworzenia migawki obiektu blob zaszyfrowane. Jeśli metadane muszą być ustawione, należy wywołać **get_blob_metadata** metodę najpierw uzyskać bieżące metadane szyfrowania i uniknąć równoczesnych zapisów podczas ustawiania metadanych.
> * Włącz **require_encryption** flagi na obiekt usługi dla użytkowników, którzy powinien działać wyłącznie z zaszyfrowanych danych. Aby uzyskać więcej informacji, zobacz poniżej.
> 
> 

Biblioteka klienta magazynu oczekuje podana KEK i rozpoznawania klucza do implementacji interfejsu. [Usługa Azure Key Vault](https://azure.microsoft.com/services/key-vault/) obsługę zarządzania Python KEK oczekuje i będzie można zintegrować z tej biblioteki, po zakończeniu.

## <a name="client-api--interface"></a>Interfejs API klienta / interfejsu
Po utworzeniu obiektu usługi Magazyn (tj. blockblobservice), użytkownik może przypisać wartości do pola, które tworzą zasadę szyfrowania: key_encryption_key, key_resolver_function i require_encryption. Użytkownicy mogą podać tylko KEK tylko program rozpoznawania nazw, lub obie. key_encryption_key jest podstawowy typ klucza, który jest identyfikowany przy użyciu identyfikatora klucza i zapewnia logiki zawijania/odkodowywania. key_resolver_function jest używany do rozpoznawania klucza podczas odszyfrowywania. Zwraca prawidłową KEK, podany identyfikator klucza. Zapewnia to użytkownikom wybór między wiele kluczy, które są zarządzane na wiele lokalizacji.

KEK musi implementować następujące metody umożliwiające pomyślnie szyfrowania danych:

* wrap_key(cek): Opakowuje CEK określonego (w bajtach) przy użyciu algorytmu wybrany przez użytkownika. Zwraca klucz opakowana.
* get_key_wrap_algorithm(): zwraca algorytm stosowanego do opakowywania kluczy.
* get_kid(): zwraca identyfikator klucza w ciągu dla tego KEK.
  KEK musi implementować następujących metod można pomyślnie odszyfrować danych:
* unwrap_key (cek, algorytm): zwraca bez otoki formę CEK określony przy użyciu algorytmu określony ciąg.
* get_kid(): zwraca ciąg identyfikatora klucza dla tego KEK.

Program rozpoznawania nazw kluczy co najmniej musi implementować metodę, która podany identyfikator klucza, zwraca odpowiedniego klucza KEK implementującej interfejs powyżej. Tylko ta metoda służy do przypisania do właściwości key_resolver_function na obiekt usługi.

* Dla celów szyfrowania jest on używany zawsze i Brak klucza spowoduje błąd.
* Do odszyfrowywania:
  
  * Mechanizm rozpoznawania klucza jest wywoływana, jeśli określono, aby pobrać klucz. Jeśli program rozpoznawania nazw jest określony, ale nie ma mapowania dla identyfikatora klucza, zostanie zgłoszony błąd.
  * Jeśli nie określono programu rozpoznawania nazw, ale nie określono klucza, klucz zostanie użyty, jeśli jego identyfikator odpowiada wymagany identyfikator klucza. Jeśli identyfikator nie jest zgodne, zostanie zgłoszony błąd.
    
    Przykłady szyfrowania w azure.storage.samples <fix URL>zaprezentowania bardziej szczegółowe scenariusza end-to-end dla obiektów blob, kolejek i tabel.
      Przykładowe implementacje KEK i klucza program rozpoznawania nazw są zawarte w przykładowych plików jako KeyWrapper i KeyResolver odpowiednio.

### <a name="requireencryption-mode"></a>Tryb RequireEncryption
Użytkownicy mogą opcjonalnie włączyć tryb działania, w której wszystkie przekazywania i pobierania muszą być szyfrowane. W tym trybie próby przekazania danych bez zasady szyfrowania lub pobrać dane, które nie są szyfrowane w usłudze zakończy się niepowodzeniem na kliencie. **Require_encryption** flagi na obiekt usługi określa to zachowanie.

### <a name="blob-service-encryption"></a>Szyfrowanie usługi blob
Ustawić szyfrowania pól zasad blockblobservice obiektu. Wszystkie inne będzie obsługiwany przez bibliotekę klienta wewnętrznie.

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
Ustawić szyfrowania pól zasad queueservice obiektu. Wszystkie inne będzie obsługiwany przez bibliotekę klienta wewnętrznie.

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

### <a name="table-service-encryption"></a>Szyfrowanie usługi tabel
Oprócz tworzenia zasady szyfrowania i ustawienie jej na żądanie opcji, należy określić **encryption_resolver_function** na **tableservice**, lub ustaw dla atrybutu Szyfruj EntityProperty.

### <a name="using-the-resolver"></a>Za pomocą programu rozpoznawania nazw

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
Jak wspomniano powyżej, właściwość może być oznaczony do szyfrowania przez zapisanie jej w obiekcie EntityProperty i ustawienie pola szyfrowania.

```python
encrypted_property_1 = EntityProperty(EdmType.STRING, value, encrypt=True)
```

## <a name="encryption-and-performance"></a>Szyfrowanie i wydajności
Należy pamiętać, że szyfrowania z magazynu danych spowoduje zmniejszenie wydajności. Musi zostać wygenerowany klucz zawartości i IV, muszą być szyfrowane samej zawartości i dodatkowe metadane muszą być sformatowane i przekazać. Ten narzut będą się różnić w zależności od ilości danych szyfrowany. Zaleca się, że klienci zawsze testują wydajności w czasie projektowania.

## <a name="next-steps"></a>Kolejne kroki
* Pobierz [biblioteki klienta usługi Azure Storage dla języka Java PyPi pakietu](https://pypi.python.org/pypi/azure-storage)
* Pobierz [biblioteki klienta magazynu Azure dla języka Python źródła kodu z usługi GitHub](https://github.com/Azure/azure-storage-python)
