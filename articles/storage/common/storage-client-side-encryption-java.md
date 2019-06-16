---
title: Szyfrowanie po stronie klienta za pomocą języka Java dla usługi Microsoft Azure Storage | Dokumentacja firmy Microsoft
description: Biblioteki klienta usługi Azure Storage dla języka Java obsługuje szyfrowanie po stronie klienta i integracji z usługą Azure Key Vault dla zapewnienia maksymalnego poziomu bezpieczeństwa dla aplikacji usługi Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.devlang: java
ms.topic: article
ms.date: 05/11/2017
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 058dc97054aad310135ccc1f51d765f0af3f571b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65147028"
---
# <a name="client-side-encryption-and-azure-key-vault-with-java-for-microsoft-azure-storage"></a>Szyfrowanie po stronie klienta i usługi Azure Key Vault przy użyciu języka Java dla usługi Microsoft Azure Storage
[!INCLUDE [storage-selector-client-side-encryption-include](../../../includes/storage-selector-client-side-encryption-include.md)]

## <a name="overview"></a>Omówienie
[Biblioteki klienta usługi Azure Storage dla języka Java](https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage) obsługuje szyfrowanie danych w aplikacjach klienckich przed przekazaniem do usługi Azure Storage oraz odszyfrowywanie danych pobraniem do klienta. Biblioteka obsługuje również integrację z usługą [usługi Azure Key Vault](https://azure.microsoft.com/services/key-vault/) do zarządzania kluczami konta magazynu.

## <a name="encryption-and-decryption-via-the-envelope-technique"></a>Szyfrowanie i odszyfrowywanie za pomocą techniki obwiedni
Procesy szyfrowania i odszyfrowywania wykonaj technika koperty.  

### <a name="encryption-via-the-envelope-technique"></a>Szyfrowanie za pomocą techniki obwiedni
Szyfrowanie za pomocą techniki koperty działa w następujący sposób:  

1. Biblioteka klienta usługi Azure storage generuje klucz szyfrowania zawartości (CEK), czyli jeden jednorazowej Użyj klucza symetrycznego.  
2. Dane użytkownika są szyfrowane przy użyciu tego CEK.   
3. CEK jest następnie opakowywany (zaszyfrowane) przy użyciu klucza szyfrowania klucza (KEK). Klucza KEK jest identyfikowana przez identyfikator klucza i można asymetryczną parę kluczy lub klucz symetryczny i mogą być zarządzane lokalnie lub przechowywane w usłudze Azure Key Vault.  
   Sama biblioteka klienta magazynu nigdy nie ma dostępu do klucza KEK. Biblioteka wywołuje algorytmu zawijanie kluczy, który znajduje się za pomocą usługi Key Vault. Użytkownicy mogą wybierać niestandardowych dostawców dla klucza zawijania/rozpakowanie w razie potrzeby.  
4. Zaszyfrowane dane są następnie przekazywane do usługi Azure Storage. Opakowana klucz wraz z metadanymi pewne dodatkowe szyfrowanie jest przechowywane w postaci metadanych (na obiekcie blob) albo interpolowane z zaszyfrowanych danych (wiadomości w kolejce i jednostki z tabeli).

### <a name="decryption-via-the-envelope-technique"></a>Odszyfrowywanie za pomocą techniki obwiedni
Odszyfrowywanie za pomocą techniki koperty działa w następujący sposób:  

1. Biblioteka klienta zakłada, że użytkownik zarządza klucz szyfrowania klucza (KEK), lokalnie lub w usłudze Azure Key Vault. Użytkownik nie musi wiedzieć, określony klucz, który został użyty do szyfrowania. Zamiast tego klucza programu rozpoznawania nazw, który jest rozpoznawany jako różne identyfikatorach klucza kluczy można skonfigurować i używane.  
2. Biblioteka klienta pobierze zaszyfrowanych danych wraz z materiału szyfrowania, który jest przechowywany w usłudze.  
3. Klucz opakowana szyfrowania zawartości (CEK) jest nieopakowanych (odszyfrowany) przy użyciu klucza szyfrowania klucza (KEK). W tym miejscu ponownie, Biblioteka klienta nie ma dostępu do klucza KEK. Wywołuje po prostu niestandardowej lub dostawcy usługi Key Vault odszyfrowania algorytmu.  
4. Klucz szyfrowania zawartości (CEK) jest następnie używany do odszyfrowywania danych zaszyfrowanych użytkownika.

## <a name="encryption-mechanism"></a>Mechanizm szyfrowania
Korzysta z biblioteki klienta usługi storage [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) w celu zaszyfrowania danych użytkownika. W szczególności [Cipher Block Chaining (CBC)](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher-block_chaining_.28CBC.29) trybu przy użyciu standardu AES. Każdy usługi działa nieco inaczej, omówimy każdy z nich w tym miejscu.

### <a name="blobs"></a>Obiekty blob
Biblioteka klienta obsługuje obecnie szyfrowanie tylko całe obiektów blob. Ściślej mówiąc, szyfrowanie jest obsługiwane, gdy użytkownicy używają **przekazywanie*** metody lub **openOutputStream** metody. Pliki do pobrania, zarówno pełne i pobrania zakresu są obsługiwane.  

Podczas szyfrowania biblioteki klienta wygeneruje losowy inicjowania wektora (IV) 16-bajtowy, wraz z kluczem szyfrowania zawartości (CEK), 32-bitowej i wykonać koperty szyfrowanie danych obiektów blob, korzystając z tych informacji. Opakowana CEK i niektóre metadane dodatkowego szyfrowania są następnie zapisywane jako metadane, wraz z zaszyfrowanego obiektu blob w usłudze obiektu blob.

> [!WARNING]
> W przypadku edytowania lub przekazywanie metadane obiektu blob, należy upewnić się, że te metadane są zachowywane. Jeśli załadujesz nowymi metadanymi bez takich metadanych, CEK zawinięty, IV i inne metadane zostaną utracone, a zawartość obiektu blob nigdy nie być możliwe do pobierania.
> 
> 

Pobieranie obiektu blob zaszyfrowanego polega na pobraniu zawartości całego obiektu blob przy użyciu **Pobierz**/**openInputStream** wygodne metody. Opakowana CEK jest nieopakowanych i używany razem z IV (przechowywanych jako metadane obiektu blob w tym przypadku), aby zwrócić odszyfrowane dane do użytkowników.

Pobranie dowolnego zakresu (**downloadRange** metody) w obiekcie blob zaszyfrowanego obejmuje, dostosowując zakres dostarczone przez użytkowników w celu uzyskania niewielką ilość dodatkowych danych, które można pomyślnie odszyfrować żądany zakres.  

Wszystkie typy w obiektów blob (blokowe obiekty BLOB, stronicowe obiekty BLOB i uzupełnialnych obiektów blob) można zaszyfrowane/odszyfrować za pomocą tego programu.

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
3. Opakowana CEK i niektóre metadane dodatkowego szyfrowania są następnie zapisywane jako dwa dodatkowe właściwości zastrzeżonych. Pierwszą właściwością zarezerwowane (_ClientEncryptionMetadata1) jest właściwość ciągu, która przechowuje informacje dotyczące IV, wersji i klucz opakowana. Po drugie zastrzeżonych właściwości (_ClientEncryptionMetadata2) jest binarny właściwość, która przechowuje informacje dotyczące właściwości, które są szyfrowane. Informacje przedstawione w tej drugiej właściwości (_ClientEncryptionMetadata2) jest zaszyfrowany.  
4. Ze względu na te dodatkowe właściwości zastrzeżonych wymagana na potrzeby szyfrowania użytkownicy mogą teraz mieć tylko 250 właściwości niestandardowych zamiast 252. Całkowity rozmiar jednostki musi być mniejszy niż 1MB.  
   
   Należy zauważyć, że tylko ciąg właściwości mogą być szyfrowane. Inne typy właściwości mają być szyfrowane, musi zostać przekonwertowana na ciągi. Zaszyfrowane ciągi są przechowywane w usłudze jako właściwości binarnych i są konwertowane do ciągów po odszyfrowywania.
   
   W przypadku tabel, oprócz zasad szyfrowania użytkownicy muszą określić właściwości, które mają być szyfrowane. Można to zrobić, albo określając atrybutu [Szyfruj] (dla jednostki POCO, które wynikają z TableEntity) lub szyfrowania mechanizmu rozwiązywania konfliktów w opcji żądania. Mechanizm rozpoznawania szyfrowania jest delegat, który przyjmuje klucza partycji, klucz wiersza i nazwę właściwości i zwraca wartość boolean wskazującą, czy mają być szyfrowane tej właściwości. Podczas szyfrowania biblioteki klienckiej użyje tych informacji do określania, czy właściwości mają być szyfrowane podczas zapisu w sieci. Delegat udostępnia także możliwości logiki wokół jak zaszyfrowane właściwości. (Na przykład, jeśli X, następnie szyfrować właściwość A; w przeciwnym razie szyfrowanie właściwości, A i B.) Należy pamiętać, że nie jest konieczne może przekazać tę informację podczas odczytywania lub podczas badania jednostki.

### <a name="batch-operations"></a>Operacji wsadowych
W operacji wsadowych tego samego klucza KEK zostaną użyte, we wszystkich wierszach w tej operacji wsadowych ponieważ biblioteka klienta umożliwia tylko jeden obiekt opcje (i dlatego jeden zasad/KEK) dla operacji zbiorczej. Jednak biblioteka klienta wewnętrznie wygeneruje nowy IV losowych i losowe CEK poszczególnych wierszy w partii. Użytkowników można również szyfrowania różnych właściwości dla każdej operacji w zadaniu wsadowym, definiując to zachowanie w szyfrowania programu rozpoznawania nazw.

### <a name="queries"></a>Zapytania
> [!NOTE]
> Ponieważ obiekty są szyfrowane, nie można uruchomić zapytania, które filtrują na właściwością szyfrowaną.  Przy próbie wyniki będą nieprawidłowe, ponieważ usługa może podjąć próbę porównać dane zaszyfrowane przy użyciu nieszyfrowanego danych.
> 
> 
> Aby wykonać operacje zapytań, należy określić klucza programu rozpoznawania nazw, który jest w stanie rozpoznać wszystkich kluczy w zestawie wyników. Biblioteka klienta będzie sygnalizować błąd, jeśli nie można rozpoznać jednostki zawarte w wyniku zapytania do dostawcy. Dla dowolnego zapytania, który wykonuje projekcje po stronie serwera biblioteki klienckiej doda właściwości metadanych szyfrowania specjalne (_ClientEncryptionMetadata1 i _ClientEncryptionMetadata2) domyślnie do wybranych kolumn.

## <a name="azure-key-vault"></a>W usłudze Azure Key Vault
Usługa Azure Key Vault ułatwia ochronę kluczy kryptograficznych i kluczy tajnych używanych przez aplikacje i usługi w chmurze. Za pomocą usługi Azure Key Vault, użytkownicy mogą szyfrować klucze i wpisy tajne (takie jak klucze uwierzytelniania, klucze konta magazynu, klucze szyfrowania danych. Pliki PFX oraz hasła) przy użyciu kluczy chronionych przez sprzętowe moduły zabezpieczeń (HSM). Aby uzyskać więcej informacji, zobacz [co to jest usługa Azure Key Vault?](../../key-vault/key-vault-whatis.md).

Biblioteka klienta magazynu używa podstawowej biblioteki usługi Key Vault w celu zapewnienia wspólną platformę Azure do zarządzania kluczami. Użytkownicy otrzymują również dodatkową korzyść: za pomocą biblioteki rozszerzenia usługi Key Vault. Biblioteka rozszerzeń zapewnia przydatnych funkcji wokół łatwego i szybkiego Symmetric/RSA lokalnych i klucza dostawcy usług w chmurze, a także za pomocą agregacji i buforowania.

### <a name="interface-and-dependencies"></a>Interfejs i zależności
Istnieją trzy pakiety usługi Key Vault:  

* Azure keyvault procesory zawiera klucz Instrumentacji i IKeyResolver. To niewielki pakiet bez zależności. Biblioteki klienta usługi storage dla języka Java definiuje ją jako zależność.
* magazyn kluczy Azure zawiera klienta Key Vault REST.  
* rozszerzenia w przypadku magazynu kluczy Azure zawiera kod rozszerzenia, która zawiera implementacje algorytmów kryptograficznych i RSAKey SymmetricKey. On jest zależna od przestrzeni nazw Core i magazyn kluczy i oferuje funkcje, aby zdefiniować agregacji rozpoznawania nazw (gdy użytkownicy chcą korzystać z wielu dostawców kluczy) oraz buforowania rozpoznawania nazw kluczy. Mimo że biblioteki klienta usługi storage bezpośrednio niezależnie od tego pakietu, jeśli użytkownicy chcą korzystać z usługi Azure Key Vault do przechowywania swoich kluczy lub używanie lokalnej oraz dostawców usług kryptograficznych w chmurze za pomocą rozszerzenia usługi Key Vault, potrzebują tego pakietu.  
  
  Key Vault zaprojektowano z myślą o wysokiej wartości kluczy głównych i limity ograniczania przepustowości dla usługi Key Vault zaprojektowano z to na uwadze. Podczas wykonywania szyfrowania po stronie klienta za pomocą usługi Key Vault, model preferowany jest używać kluczy symetrycznych wzorzec przechowywane jako wpisy tajne w usłudze Key Vault i buforowane lokalnie. Użytkownicy, wykonaj następujące czynności:  

1. Utwórz klucz tajny w trybie offline i przekaż go do usługi Key Vault.  
2. Klucz tajny podstawowego identyfikatora można użyć jako parametru, aby Rozwiąż bieżącą wersję wpisu tajnego szyfrowania, a te informacje lokalnie w pamięci podręcznej. Użyj CachingKeyResolver do buforowania; Użytkownicy nie powinny implementować własnej logiki buforowania.  
3. Użyj buforowania rozpoznawania nazw jako dane wejściowe podczas tworzenia zasady szyfrowania.
   Więcej informacji na temat użycia usługi Key Vault można znaleźć w przykładach kodu szyfrowania.

## <a name="best-practices"></a>Najlepsze praktyki
Obsługa szyfrowania jest dostępna tylko w bibliotece klienta usługi storage dla języka Java.

> [!IMPORTANT]
> Należy pamiętać o te ważne punkty, gdy za pomocą szyfrowania po stronie klienta:
> 
> * Podczas odczytywania ze zmiennej czy zapisujemy do obiektu blob zaszyfrowanego, użyj całego obiektu blob przekazywania oraz poleceń pobierania obiektów blob zakresu/całości. Należy unikać zapisywanie obiektu blob zaszyfrowanego przy użyciu protokołu operacje, takie jak umieszczania bloku, umieść zablokowanych, zapis stron, wyczyść strony lub Dołącz bloku; w przeciwnym razie może uszkodzić zaszyfrowany obiekt blob i ułatwiają nie można go odczytać.
> * W przypadku tabel istnieje ograniczenie podobne. Uważaj nie aktualizować właściwości zaszyfrowane bez aktualizowania metadanych szyfrowania.  
> * Jeśli ustawisz metadane w obiekcie blob zaszyfrowanego może zastąpić metadane związane z szyfrowaniem, wymaganego do odszyfrowywania, ponieważ ustawienie metadanych nie jest dodatek. Dotyczy to również migawki Unikaj określania metadanych podczas tworzenia migawki obiektu blob zaszyfrowany. Jeśli należy ustawić metadane, należy wywołać **downloadAttributes** metodą najpierw uzyskać bieżące metadane szyfrowania i uniknąć współbieżne operacje podczas ustawiania metadanych.  
> * Włącz **requireEncryption** flagi w opcjach domyślny żądania dla użytkowników, którzy powinien działać wyłącznie z zaszyfrowanych danych. Aby uzyskać więcej informacji, zobacz poniżej.  
> 
> 

## <a name="client-api--interface"></a>Interfejs API klienta / interfejs
Podczas tworzenia obiektu EncryptionPolicy, użytkownicy mogą podać tylko klucz (Implementowanie IKey), tylko program rozpoznawania nazw (Implementowanie IKeyResolver) lub obu. Klucz Instrumentacji jest podstawowego typu klucza, który jest identyfikowany za pomocą identyfikatora klucza i zapewniający logikę zawijania/rozpakowanie. IKeyResolver jest używany do rozpoznawania klucza podczas odszyfrowywania. Definiuje metodę ResolveKey, która zwraca klucz IKey podany identyfikator klucza. Zapewnia to użytkownikom wybór między wiele kluczy, które są zarządzane w wielu lokalizacjach.

* Dla celów szyfrowania jest on używany zawsze i Brak klucza spowoduje wystąpienie błędu.  
* Do odszyfrowywania:  
  
  * Mechanizm rozpoznawania klucza jest wywoływana, jeśli określony, aby pobrać klucz. Jeśli program rozpoznawania nazw jest określony, ale nie ma mapowania dla identyfikatora klucza, zostanie zgłoszony błąd.  
  * Jeśli program rozpoznawania nazw nie jest określony, ale klucz jest określony, klucz jest używany, jeśli pasuje do identyfikatora wymaganego identyfikatora klucza. Jeśli identyfikator nie jest zgodny, zostanie zgłoszony błąd.  
    
    [Przykłady szyfrowania](https://github.com/Azure/azure-storage-net/tree/master/Samples/GettingStarted/EncryptionSamples) pokazują bardziej szczegółowe scenariusz end-to-end dla obiektów blob, kolejek i tabel, wraz z integracją usługi Key Vault.

### <a name="requireencryption-mode"></a>Tryb RequireEncryption
Użytkownicy mogą włączyć opcjonalnie tryb działania, w której muszą być szyfrowane wszystkie przekazywanie i pobieranie. W tym trybie próby przekazania danych bez zasady szyfrowania lub pobrać dane, które nie są szyfrowane w usłudze zakończy się niepowodzeniem na komputerze klienckim. **RequireEncryption** flagi obiekt opcji żądania określa to zachowanie. Jeśli aplikacja spowoduje zaszyfrowanie wszystkich obiektów przechowywanych w usłudze Azure Storage, a następnie można ustawić **requireEncryption** właściwości domyślne opcje żądanie dotyczące obiektu klienta usługi.   

Na przykład użyć **CloudBlobClient.getDefaultRequestOptions().setRequireEncryption(true)** do wymagania szyfrowania danych dla wszystkich obiektów blob operacji wykonywanych za pośrednictwem tego obiektu klienta.

### <a name="blob-service-encryption"></a>Szyfrowanie usługi obiektów blob
Tworzenie **BlobEncryptionPolicy** obiektu i ustaw ją za pomocą opcji żądania (na interfejs API lub na poziomie klienta przy użyciu **DefaultRequestOptions**). Cała reszta będzie obsługiwany przez bibliotekę klienta wewnętrznie.

```java
// Create the IKey used for encryption.
RsaKey key = new RsaKey("private:key1" /* key identifier */);

// Create the encryption policy to be used for upload and download.
BlobEncryptionPolicy policy = new BlobEncryptionPolicy(key, null);

// Set the encryption policy on the request options.
BlobRequestOptions options = new BlobRequestOptions();
options.setEncryptionPolicy(policy);

// Upload the encrypted contents to the blob.
blob.upload(stream, size, null, options, null);

// Download and decrypt the encrypted contents from the blob.
ByteArrayOutputStream outputStream = new ByteArrayOutputStream();
blob.download(outputStream, null, options, null);
```

### <a name="queue-service-encryption"></a>Szyfrowanie usługi kolejki
Tworzenie **QueueEncryptionPolicy** obiektu i ustaw ją za pomocą opcji żądania (na interfejs API lub na poziomie klienta przy użyciu **DefaultRequestOptions**). Cała reszta będzie obsługiwany przez bibliotekę klienta wewnętrznie.

```java
// Create the IKey used for encryption.
RsaKey key = new RsaKey("private:key1" /* key identifier */);

// Create the encryption policy to be used for upload and download.
QueueEncryptionPolicy policy = new QueueEncryptionPolicy(key, null);

// Add message
QueueRequestOptions options = new QueueRequestOptions();
options.setEncryptionPolicy(policy);

queue.addMessage(message, 0, 0, options, null);

// Retrieve message
CloudQueueMessage retrMessage = queue.retrieveMessage(30, options, null);
```

### <a name="table-service-encryption"></a>Szyfrowanie usługi tabeli
Oprócz tworzenia zasady szyfrowania i ustawienie opcji żądania, należy określić **EncryptionResolver** w **TableRequestOptions**, lub ustawić atrybut [Szyfruj] dla jednostki metodę getter i setter.

### <a name="using-the-resolver"></a>Przy użyciu rozpoznawania nazw

```java
// Create the IKey used for encryption.
RsaKey key = new RsaKey("private:key1" /* key identifier */);

// Create the encryption policy to be used for upload and download.
TableEncryptionPolicy policy = new TableEncryptionPolicy(key, null);

TableRequestOptions options = new TableRequestOptions()
options.setEncryptionPolicy(policy);
options.setEncryptionResolver(new EncryptionResolver() {
    public boolean encryptionResolver(String pk, String rk, String key) {
        if (key == "foo")
        {
            return true;
        }
        return false;
    }
});

// Insert Entity
currentTable.execute(TableOperation.insert(ent), options, null);

// Retrieve Entity
// No need to specify an encryption resolver for retrieve
TableRequestOptions retrieveOptions = new TableRequestOptions()
retrieveOptions.setEncryptionPolicy(policy);

TableOperation operation = TableOperation.retrieve(ent.PartitionKey, ent.RowKey, DynamicTableEntity.class);
TableResult result = currentTable.execute(operation, retrieveOptions, null);
```

### <a name="using-attributes"></a>Przy użyciu atrybutów
Jak wspomniano powyżej, jeśli jednostka implementuje TableEntity, następnie właściwości getter i setter może być dekorowane za pomocą atrybutu [Szyfruj] zamiast określania **EncryptionResolver**.

```java
private string encryptedProperty1;

@Encrypt
public String getEncryptedProperty1 () {
    return this.encryptedProperty1;
}

@Encrypt
public void setEncryptedProperty1(final String encryptedProperty1) {
    this.encryptedProperty1 = encryptedProperty1;
}
```

## <a name="encryption-and-performance"></a>Szyfrowanie i wydajności
Należy pamiętać, że szyfrowanie usługi magazynu danych spowoduje zmniejszenie wydajności. Klucz zawartości i IV musi zostać wygenerowany samej zawartości, które muszą być szyfrowane i musi być sformatowany i przekazać dodatkowe metadane. Ten narzut różnią się zależnie od ilości danych, są szyfrowane. Firma Microsoft zaleca, aby klienci zawsze testują wydajności podczas tworzenia.

## <a name="next-steps"></a>Kolejne kroki
* Pobierz [biblioteki klienta usługi Azure Storage dla języka Java Maven package](https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage)  
* Pobierz [biblioteki klienta usługi Azure Storage dla języka Java kodu źródłowego z repozytorium GitHub](https://github.com/Azure/azure-storage-java)   
* Pobierz biblioteki Azure klucza magazynu Maven pakietów Java Maven:
  * [Podstawowe](https://mvnrepository.com/artifact/com.microsoft.azure/azure-keyvault-core) pakietu
  * [Klient](https://mvnrepository.com/artifact/com.microsoft.azure/azure-keyvault) pakietu
* Odwiedź stronę [dokumentacji usługi Azure Key Vault](../../key-vault/key-vault-whatis.md)
