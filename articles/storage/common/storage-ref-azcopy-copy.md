---
title: kopia azcopy| Dokumenty firmy Microsoft
description: Ten artykuł zawiera informacje referencyjne dla polecenia kopiowania azcopy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 431372b930269c3dfa6bdc6e8b2fe4d291a8162e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78933790"
---
# <a name="azcopy-copy"></a>azcopy copy

Kopiuje dane źródłowe do lokalizacji docelowej.

## <a name="synopsis"></a>Streszczenie

Kopiuje dane źródłowe do lokalizacji docelowej. Obsługiwane kierunki to:

  - lokalny <-> azure blob (uwierzytelnianie Sygnatury dostępu Współdzielonego lub OAuth)
  - lokalne < > usługi Azure Files (uwierzytelnianie SAS udziału/katalogu)
  - lokalne <-> ADLS Gen 2 (uwierzytelnianie SAS, OAuth lub SharedKey)
  - Usługa Azure Blob (SAS lub public) -> Azure Blob (uwierzytelnianie SYGNATUR LUB OAuth)
  - Usługa Azure Blob (SAS lub public) -> Usługi Azure Files (SAS)
  - Usługi Azure Files (SAS) -> usługi Azure Files (SAS)
  - Usługi Azure Files (SAS) -> azure blob (uwierzytelnianie SAS lub OAuth)
  - AWS S3 (klucz dostępu) - > azure blok blob (uwierzytelnianie SYGNAUTH lub OAuth)

Więcej informacji można znaleźć w przykładach.

## <a name="related-conceptual-articles"></a>Powiązane artykuły koncepcyjne

- [Wprowadzenie do narzędzia AzCopy](storage-use-azcopy-v10.md)
- [Przesyłanie danych za pomocą pamięci masowej AzCopy i Blob](storage-use-azcopy-blobs.md)
- [Przesyłanie danych za pomocą AzCopy i przechowywania plików](storage-use-azcopy-files.md)
- [Konfigurowanie, optymalizowanie i rozwiązywanie problemów z programem AzCopy](storage-use-azcopy-configure.md)

## <a name="advanced"></a>Zaawansowane

AzCopy automatycznie wykrywa typ zawartości plików podczas przekazywania z dysku lokalnego na podstawie rozszerzenia pliku lub zawartości (jeśli nie określono rozszerzenia).

Wbudowana tabela odnośnictwa jest mała, ale w systemie Unix jest rozszerzana przez pliki mime.types systemu lokalnego, jeśli są dostępne pod jedną lub kilkoma z tych nazw:

- /etc/mime.typy
- /etc/apache2/mime.types
- /etc/apache/mime.types

W systemie Windows typy MIME są wyodrębniane z rejestru. Tę funkcję można wyłączyć za pomocą flagi. Zapoznaj się z sekcją flagi.

Jeśli ustawisz zmienną środowiskową przy użyciu wiersza polecenia, ta zmienna będzie czytelna w historii wiersza polecenia. Należy wziąć pod uwagę wyczyszczenie zmiennych, które zawierają poświadczenia z historii wiersza polecenia. Aby zachować zmienne z pojawiających się w historii, można użyć skryptu, aby monitować użytkownika o ich poświadczenia i ustawić zmienną środowiskową.

```
azcopy copy [source] [destination] [flags]
```

## <a name="examples"></a>Przykłady

Przekaż pojedynczy plik przy użyciu uwierzytelniania OAuth. Jeśli nie zalogowano się jeszcze do AzCopy, uruchom polecenie azcopy login przed uruchomieniem następującego polecenia.

- azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"

Tak samo jak powyżej, ale tym razem również obliczyć md5 skrót zawartości pliku i zapisać go jako właściwości Content-MD5 obiektu blob:

- azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5

Przekaż pojedynczy plik przy użyciu tokenu sygnatury dostępu Współdzielonego:

- azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]? [SAS]"

Przekaż pojedynczy plik przy użyciu tokenu sygnatury dostępu Współdzielonego i rurociągów (tylko blokowe obiekty BLOB):
  
- kat "/path/to/file.txt" | azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]? [SAS]"

Przekaż cały katalog przy użyciu tokenu sygnatury dostępu Współdzielonego:
  
- azcopy cp "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/directory]? [SAS]" --rekurencyjna=true

lub

- azcopy cp "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/directory]? [SAS]" --rekurencyjna=true --put-md5

Przekaż zestaw plików przy użyciu tokenu Sygnatury dostępu Współdzielonego i symboli wieloznacznych (*):

- azcopy cp "/path/*foo/* bar/*.pdf" "https://[account].blob.core.windows.net/[container]/[path/to/directory]? [SAS]"

Przekazywanie plików i katalogów przy użyciu tokenu Sygnatury dostępu Współdzielonego i symboli wieloznacznych (*):

- azcopy cp "/path/*foo/* bar*" "https://[account].blob.core.windows.net/[container]/[path/to/directory]? [SAS]" --rekurencyjna=true

Pobierz pojedynczy plik przy użyciu uwierzytelniania OAuth. Jeśli nie zalogowano się jeszcze do AzCopy, uruchom polecenie azcopy login przed uruchomieniem następującego polecenia.

- azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]" "/path/to/file.txt"

Pobierz pojedynczy plik przy użyciu tokenu sygnatury dostępu Współdzielonego:

- azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]? [SAS]" "/path/to/file.txt"

Pobierz pojedynczy plik przy użyciu tokenu Sygnatury dostępu Współdzielonego, a następnie przesuń dane wyjściowe do pliku (tylko blokowe obiekty BLOB):
  
- azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]? [SAS]" > "/path/to/file.txt"

Pobierz cały katalog przy użyciu tokenu sygnatury dostępu Współdzielonego:
  
- azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/directory]? [SAS]" "/path/to/dir" --rekursywny=true

Uwaga dotycząca używania symbolu wieloznacznego (*) w adresach URL:

W adresie URL można używać tylko dwóch obsługiwanych sposobów używania symbolu wieloznacznego. 

- Możesz użyć go tuż po ostatnim ukośniku (/) adresu URL. Spowoduje to skopiowanie wszystkich plików w katalogu bezpośrednio do miejsca docelowego bez umieszczania ich w podkatalogu.

- Można również użyć jednego w nazwie kontenera, tak długo, jak adres URL odnosi się tylko do kontenera, a nie do obiektu blob. Za pomocą tej metody można uzyskać pliki z podzbioru kontenerów.

Pobierz zawartość katalogu bez kopiowania samego katalogu zawierającego.

- azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/folder]/*? [SAS]" "/path/to/dir"

Pobierz całe konto magazynu.

- azcopy cp "https://[srcaccount].blob.core.windows.net/" "/path/to/dir" --rekursywne

Pobierz podzbiór kontenerów w ramach konta magazynu przy użyciu symbolu wieloznacznego (*) w nazwie kontenera.

- azcopy cp "https://[srcaccount].blob.core.windows.net/[nazwa kontenera]" "/path/to/dir" --rekursywny

Skopiuj pojedynczy obiekt blob do innego obiektu blob przy użyciu tokenu sygnatury dostępu Współdzielonego.

- azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/blob]? [SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]? [SAS]"

Skopiuj pojedynczy obiekt blob do innego obiektu blob przy użyciu tokenu Sygnatury dostępu Współdzielonego i tokenu OAuth. Musisz użyć tokenu Sygnatury dostępu Współdzielonego na końcu adresu URL konta źródłowego, ale konto docelowe nie jest potrzebne, jeśli zalogujesz się do AzCopy za pomocą polecenia azcopy login. 

- azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/blob]? [SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]"

Skopiuj jeden katalog wirtualny obiektu blob do innego przy użyciu tokenu sygnatury dostępu Współdzielonego:

- azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/directory]? [SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]? [SAS]" --rekurencyjna=true

Skopiuj wszystkie kontenery obiektów blob, katalogi i obiekty blob z konta magazynu do innego przy użyciu tokenu sygnatury dostępu Współdzielonego:

- azcopy cp "https://[srcaccount].blob.core.windows.net? [SAS]" "https://[destaccount].blob.core.windows.net? [SAS]" --rekurencyjna=true

Skopiuj pojedynczy obiekt do magazynu obiektów Blob z amazon web services (AWS) S3 przy użyciu klucza dostępu i tokenu SYGNAŁÓW dostępu. Najpierw ustaw zmienną środowiskową AWS_ACCESS_KEY_ID i AWS_SECRET_ACCESS_KEY dla źródła AWS S3.
  
- azcopy cphttps://s3.amazonaws.com/" [bucket]/[object]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]? [SAS]"

Skopiuj cały katalog do magazynu obiektów Blob z usługi AWS S3 przy użyciu klucza dostępu i tokenu Sygnatury dostępu. Najpierw ustaw zmienną środowiskową AWS_ACCESS_KEY_ID i AWS_SECRET_ACCESS_KEY dla źródła AWS S3.

- azcopy cphttps://s3.amazonaws.com/" [bucket]/[folder]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]? [SAS]" --rekurencyjna=true

Zapoznaj się https://docs.aws.amazon.com/AmazonS3/latest/user-guide/using-folders.html z lepszym zrozumieniem symbolu zastępczego [folder].

Skopiuj wszystkie zasobniki do magazynu obiektów Blob z usług Amazon Web Services (AWS) przy użyciu klucza dostępu i tokenu Sygnatury dostępu Współdzielonego. Najpierw ustaw zmienną środowiskową AWS_ACCESS_KEY_ID i AWS_SECRET_ACCESS_KEY dla źródła AWS S3.

- azcopy cphttps://s3.amazonaws.com/" " "https://[destaccount].blob.core.windows.net? [SAS]" --rekurencyjna=true

Skopiuj wszystkie zasobniki do magazynu obiektów Blob z regionu Amazon Web Services (AWS) przy użyciu klucza dostępu i tokenu Sygnatury dostępu. Najpierw ustaw zmienną środowiskową AWS_ACCESS_KEY_ID i AWS_SECRET_ACCESS_KEY dla źródła AWS S3.

- azcopy cphttps://s3-" [region].amazonaws.com/" "https://[destaccount].blob.core.windows.net? [SAS]" --rekurencyjna=true

Skopiuj podzbiór zasobników przy użyciu symbolu wieloznacznego (*) w nazwie zasobnika. Podobnie jak w poprzednich przykładach, potrzebny jest klucz dostępu i token sygnatury dostępu współdzielonego. Upewnij się, że dla źródła AWS S3 ustawiono zmienną środowiskową AWS_ACCESS_KEY_ID i AWS_SECRET_ACCESS_KEY.

- azcopy cphttps://s3.amazonaws.com/" [bucket*name]/" "https://[destaccount].blob.core.windows.net? [SAS]" --rekurencyjna=true

## <a name="options"></a>Opcje

**Ciąg typu --blob** definiuje typ obiektu blob w miejscu docelowym. Służy do przekazywania obiektów blob i kopiowania między kontami (domyślnie "Detect"). Prawidłowe wartości to "Detect", "BlockBlob", "PageBlob" i "AppendBlob". Podczas kopiowania między kontami wartość "Detect" powoduje, że AzCopy używa typu źródłowego obiektu blob do określenia typu docelowego obiektu blob. Podczas przesyłania pliku "Detect" określa, czy plik jest VHD lub VHDX plik oparty na rozszerzeniu pliku. Jeśli plik jest eterem pliku VHD lub VHDX, AzCopy traktuje plik jako obiekt blob strony. (domyślnie "Detect")

**--block-blob-tier** string Przekaż blokowe obiekty blob bezpośrednio do [wybranej warstwy dostępu.](../blobs/storage-blob-storage-tiers.md) (domyślnie "Brak"). Prawidłowe wartości to "Brak", "Hot", "Cool" i "Archive". Jeśli zostanie przekazana warstwa "Brak" lub nie zostanie przekazana żadna warstwa, obiekt blob odziedziczy warstwę konta magazynu.

**--block-size-mb** float Użyj tego rozmiaru bloku (określonego w MiB) podczas przekazywania do usługi Azure Storage i pobierania z usługi Azure Storage. Wartość domyślna jest obliczana automatycznie na podstawie rozmiaru pliku. Frakcje dziesiętne są dozwolone (na przykład: 0,25).

**--cache-control** string Ustaw nagłówek kontroli pamięci podręcznej. Zwrócono po pobraniu.

**--check-length**                         Sprawdź długość pliku w miejscu docelowym po przeniesieniu. Jeśli występuje niezgodność między źródłem a miejscem docelowym, transfer jest oznaczony jako nie powiodło się. (domyślna wartość true)

**--check-md5** string Określa, jak ściśle skróty MD5 powinny być sprawdzane podczas pobierania. Dostępne tylko podczas pobierania. Dostępne opcje: NoCheck, LogOnly, FailIfDifferent, FailIfDifferentOrMissing. (domyślnie "FailIfDifferent")

**--content-disposition** string Ustaw nagłówek dyspozycji zawartości. Zwrócono po pobraniu.

**--content-kodowanie** ciągu Ustaw nagłówek kodowania zawartości. Zwrócono po pobraniu.

**--content-language** string Ustaw nagłówek języka zawartości. Zwrócono po pobraniu.

**Ciąg typu --content** Określa typ zawartości pliku. Oznacza no-guess-mime typu. Zwrócono po pobraniu.

**--dekompresja**                           Automatycznie dekompresuj pliki podczas pobierania, jeśli ich kodowanie zawartości wskazuje, że są skompresowane. Obsługiwane wartości kodowania zawartości to "gzip" i "deflate". Rozszerzenia plików '.gz'/'.gzip' lub '.zz' nie są konieczne, ale zostaną usunięte, jeśli są obecne.

**Ciąg --exclude-attributes** (tylko windows) Wyklucz pliki, których atrybuty pasują do listy atrybutów. Na przykład: A; S; R

**Ciąg typu --exclude-blob** Opcjonalnie określa typ obiektu blob (BlockBlob/ PageBlob/ AppendBlob), który ma być wykluczony podczas kopiowania obiektów blob z kontenera lub konta. Użycie tej flagi nie ma zastosowania do kopiowania danych z usługi nie azure do usługi. Więcej niż jeden obiekt blob powinien być oddzielony przez ';'.

**Ciąg --exclude-path** Wyklucz te ścieżki podczas kopiowania. Ta opcja nie obsługuje symboli wieloznacznych (*). Sprawdza względny prefiks ścieżki(Na przykład: mójFolder;mójFolder/subDirName/file.pdf). W połączeniu z przechodzeniem do konta ścieżki nie zawierają nazwy kontenera.

**Ciąg --exclude-pattern** Wyklucz te pliki podczas kopiowania. Ta opcja obsługuje symbole wieloznaczne (*)

**--follow-symlinks**                      Podczas przesyłania z lokalnego systemu plików należy obserwować łącza symboliczne.

**--od-do** ciągu Opcjonalnie określa kombinację docelową źródła. Na przykład: LocalBlob, BlobLocal, LocalBlobfs.

**-h, --help** pomoc dla kopiowania

**--include-attributes** string (tylko windows) Dołącz pliki, których atrybuty pasują do listy atrybutów. Na przykład: A; S; R

**--include-path** string Uwzględnij tylko te ścieżki podczas kopiowania. Ta opcja nie obsługuje symboli wieloznacznych (*). Sprawdza prefiks ścieżki względnej (na przykład: mójFolder;mójFolder/subDirName/file.pdf).

**--include-pattern** string Uwzględnij tylko te pliki podczas kopiowania. Ta opcja obsługuje symbole wieloznaczne (*). Oddzielaj pliki za pomocą ';'.

**--log-level** string Zdefiniuj szczegółowość dziennika dla pliku dziennika, dostępne poziomy: INFO (wszystkie żądania/odpowiedzi), OSTRZEŻENIE (powolne odpowiedzi), ERROR (tylko żądania nieudane) i NONE (brak dzienników wyjściowych). (domyślnie "INFO")

**--metadanych** ciąg Przekaż do usługi Azure Storage z tych par klucza wartości jako metadane.

**--no-guess-mime-type**                   Zapobiega wykrywaniu przez AzCopy typu zawartości na podstawie rozszerzenia lub zawartości pliku.

**--overwrite** string Zastąp pliki powodujące konflikt i obiekty BLOB w miejscu docelowym, jeśli ta flaga jest ustawiona na true. Możliwe wartości to "true", "false", "ifSourceNewer" i "prompt". (domyślnie "true")

**Ciąg -page-blob-tier** Przekaż obiekt blob strony do usługi Azure Storage przy użyciu tej warstwy obiektu blob. (domyślnie "Brak")

**--preserve-last-modified-time**          Dostępne tylko wtedy, gdy miejscem docelowym jest system plików.

**--put-md5**                             Utwórz skrót MD5 każdego pliku i zapisz skrót jako właściwość Content-MD5 docelowego obiektu blob lub pliku. (Domyślnie skrót NIE jest tworzony). Dostępne tylko podczas przesyłania.

**--rekursywny**                            Podczas przekazywania z lokalnego systemu plików zajrzyj do podkatarzy cyklicznie.

**--s2s-detect-source-zmieniono**           Sprawdź, czy źródło uległo zmianie po wyliczenia.

**--s2s-handle-invalid-metadata** string Określa sposób obsługi nieprawidłowych kluczy metadanych. Dostępne opcje: ExcludeIfInvalid, FailIfInvalid, RenameIfInvalid. (domyślnie "ExcludeIfInvalid")

**--s2s-preserve-access-tier**             Zachowaj warstwę dostępu podczas usługi do kopiowania usługi. Zapoznaj się z [usługi Azure Blob storage: hot, cool i archiwum warstwy dostępu,](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers) aby upewnić się, że konto magazynu docelowego obsługuje ustawienie warstwy dostępu. W przypadkach, gdy ustawienie warstwy dostępu nie jest obsługiwane, użyj s2sPreserveAccessTier=false, aby pominąć kopiowanie warstwy dostępu. (domyślna wartość true)

**--s2s-preserve-właściwości**              Zachowaj pełne właściwości podczas kopiowania usługi do serwisu. W przypadku aws S3 i usługi Azure File bez pojedynczego źródła plików operacja listy nie zwraca pełnych właściwości obiektów i plików. Aby zachować pełne właściwości, AzCopy musi wysłać jedno dodatkowe żądanie na obiekt lub plik. (domyślna wartość true)

## <a name="options-inherited-from-parent-commands"></a>Opcje dziedziczone z poleceń nadrzędnych

**--cap-mbps uint32**      Limity szybkości transferu w megabitach na sekundę. Przepływność moment po chwili może się nieznacznie różnić od limitu. Jeśli ta opcja jest ustawiona na zero lub jest pomijana, przepływność nie jest ograniczona.

**--output-type** string Format wyjścia polecenia. Do wyboru są: tekst, json. Wartością domyślną jest "text". (domyślny "tekst")

## <a name="see-also"></a>Zobacz też

- [azcopy](storage-ref-azcopy.md)
