---
title: kopia AzCopy | Microsoft Docs
description: Ten artykuł zawiera informacje referencyjne dotyczące polecenia copy AzCopy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: c15d188e333bea5e74fa65d2bbdf38ae7fadc246
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195739"
---
# <a name="azcopy-copy"></a>kopia AzCopy

Kopiuje dane źródłowe do lokalizacji docelowej.

## <a name="synopsis"></a>Streszczenie

Obsługiwane są następujące wskazówki:

- < lokalnego — > obiektu blob platformy Azure (uwierzytelnianie SAS lub OAuth)
- < lokalnego — > plik platformy Azure (uwierzytelnianie za pomocą uwierzytelniania SAS/katalogu)
- Local <-> ADLS Gen 2 (uwierzytelnianie SAS, OAuth lub SharedKey)
- Obiekt blob platformy Azure (SAS lub publiczny) <-> obiekt blob platformy Azure (uwierzytelnianie SAS lub OAuth)
- Azure File (SAS) — > blokowych obiektów blob platformy Azure (uwierzytelnianie SAS lub OAuth)
- AWS S3 (klucz dostępu) — > blokowych obiektów blob platformy Azure (uwierzytelnianie SAS lub OAuth)

Aby uzyskać więcej informacji, zapoznaj się z przykładami.

### <a name="advanced"></a>Zaawansowane

AzCopy automatycznie wykrywa typ zawartości plików podczas przekazywania z dysku lokalnego na podstawie rozszerzenia lub zawartości pliku (jeśli nie określono rozszerzenia).

Wbudowana tabela wyszukiwania jest mała, ale w systemie UNIX jest rozszerzana przez system lokalny. typy plików MIME. Types, jeśli są dostępne w co najmniej jednej z tych nazw:

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

W systemie Windows typy MIME są wyodrębniane z rejestru. Tę funkcję można wyłączyć za pomocą pomocy flagi. Zapoznaj się z sekcją flagi.

> [!IMPORTANT]
> W przypadku ustawienia zmiennej środowiskowej przy użyciu wiersza polecenia ta zmienna zostanie odczytana w historii wiersza polecenia. Rozważ wyczyszczenie zmiennych, które zawierają poświadczenia z historii wiersza polecenia. Aby zachować zmienne w historii, można użyć skryptu w celu wyświetlenia monitu o podanie poświadczeń użytkownika i ustawienia zmiennej środowiskowej.

```azcopy
azcopy copy [source] [destination] [flags]
```

## <a name="examples"></a>Przykłady

Przekaż pojedynczy plik przy użyciu uwierzytelniania OAuth.

Jeśli jeszcze nie zalogowano się w usłudze AzCopy, użyj `azcopy login` polecenia przed uruchomieniem następującego polecenia.

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

Analogicznie jak powyżej, ale ten czas również oblicza skrót MD5 zawartości pliku i zapisuje go jako właściwość Content-MD5 obiektu BLOB:

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5
```

Przekaż pojedynczy plik z sygnaturą dostępu współdzielonego:

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Przekaż pojedynczy plik z sygnaturą dostępu współdzielonego za pomocą instalacji rurowej (tylko blokowe obiekty blob):

```azcopy
cat "/path/to/file.txt" | azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Przekaż cały katalog przy użyciu sygnatury dostępu współdzielonego:

```azcopy
azcopy cp "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

lub

```azcopy
azcopy cp "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --put-md5
```

Przekazywanie zestawu plików za pomocą sygnatury dostępu współdzielonego przy użyciu symboli wieloznacznych:

```azcopy
azcopy cp "/path/*foo/*bar/*.pdf" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

Przekazywanie plików i katalogów za pomocą sygnatury dostępu współdzielonego przy użyciu symboli wieloznacznych:

```azcopy
azcopy cp "/path/*foo/*bar*" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Pobierz pojedynczy plik z uwierzytelnianiem OAuth.

Jeśli jeszcze nie zalogowano się w usłudze AzCopy, użyj `azcopy login` polecenia przed uruchomieniem następującego polecenia.

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]" "/path/to/file.txt"
```

Pobierz pojedynczy plik z sygnaturą dostępu współdzielonego:

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "/path/to/file.txt"
```

Pobieranie pojedynczego pliku za pomocą sygnatury dostępu współdzielonego (tylko blokowe obiekty blob):

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" > "/path/to/file.txt"
```

Pobierz cały katalog z sygnaturą dostępu współdzielonego:

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" "/path/to/dir" --recursive=true
```

Pobierz zestaw plików z sygnaturą dostępu współdzielonego przy użyciu symboli wieloznacznych:

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/foo*?[SAS]" "/path/to/dir"
```

Pobieranie plików i katalogów za pomocą sygnatury dostępu współdzielonego przy użyciu symboli wieloznacznych:

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/foo*?[SAS]" "/path/to/dir" --recursive=true
```

Skopiuj pojedynczy obiekt BLOB z sygnaturą dostępu współdzielonego do innego obiektu BLOB z SYGNATURą

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Skopiuj pojedynczy obiekt BLOB z sygnaturą dostępu współdzielonego do innego obiektu BLOB z tokenem OAuth.

Jeśli jeszcze nie zalogowano się w usłudze AzCopy, użyj `azcopy login` polecenia przed uruchomieniem następującego polecenia. Token OAuth jest używany w celu uzyskania dostępu do docelowego konta magazynu.

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]"
```

Skopiuj cały katalog z katalogu wirtualnego obiektów BLOB do innego katalogu wirtualnego obiektów blob z sygnaturą dostępu współdzielonego:

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Skopiuj całe dane konta z konta BLOB z sygnaturą dostępu współdzielonego na inne konto BLOB z sygnaturą dostępu współdzielonego:

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net?[SAS]" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive=true
```

Skopiuj pojedynczy obiekt z poziomu S3 z kluczem dostępu do obiektu BLOB ze SAS:

Ustaw zmienną środowiskową AWS_ACCESS_KEY_ID i AWS_SECRET_ACCESS_KEY dla źródła S3.

```azcopy
azcopy cp "https://s3.amazonaws.com/[bucket]/[object]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Skopiuj cały katalog z warstwy S3 z kluczem dostępu do katalogu wirtualnego obiektów blob z sygnaturą SAS:

```azcopy
azcopy cp "https://s3.amazonaws.com/[bucket]/[folder]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Zobacz https://docs.aws.amazon.com/AmazonS3/latest/user-guide/using-folders.html , aby dowiedzieć się, co to jest [folder] oznacza dla S3. Ustaw zmienną środowiskową AWS_ACCESS_KEY_ID i AWS_SECRET_ACCESS_KEY dla źródła S3.

Skopiuj wszystkie przedziały w usłudze S3 przy użyciu klucza dostępu do konta obiektu BLOB z sygnaturą SAS:

Ustaw zmienną środowiskową AWS_ACCESS_KEY_ID i AWS_SECRET_ACCESS_KEY dla źródła S3.

```azcopy
azcopy cp "https://s3.amazonaws.com/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive=true
```

Kopiuj wszystkie przedziały w regionie S3 z kluczem dostępu do konta obiektu BLOB za pomocą SAS:

```azcopy
azcopy cp "https://s3-[region].amazonaws.com/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive=true
```

Ustaw zmienną środowiskową AWS_ACCESS_KEY_ID i AWS_SECRET_ACCESS_KEY dla źródła S3.

## <a name="options"></a>Opcje

|Opcja|Opis|
|--|--|
|--ciąg typu BLOB|Definiuje typ obiektu BLOB w miejscu docelowym. Służy do przekazywania obiektów blob i kopiowania między kontami (domyślnie "none").|
|--Block-ciąg warstwy|Przekaż blokowy obiekt BLOB do usługi Azure Storage przy użyciu tej warstwy obiektów BLOB. (domyślnie "none").|
|--rozmiar bloku-MB zmiennoprzecinkowego |Użyj tego rozmiaru bloku (określonego w MiB) podczas przekazywania do usługi Azure Storage i pobierania z usługi Azure Storage. Wartość domyślna jest obliczana automatycznie na podstawie rozmiaru pliku. Dozwolone są ułamki dziesiętne (na przykład: 0,25).|
|--ciąg kontroli pamięci podręcznej|Ustaw nagłówek Cache-Control. Zwrócony przy pobieraniu.|
|--Check-MD5 — ciąg|Określa, jak ściśle skróty MD5 mają być sprawdzane podczas pobierania. Dostępne tylko podczas pobierania. Dostępne opcje: NOCHECK, LogOn, FailIfDifferent, FailIfDifferentOrMissing. (domyślnie "FailIfDifferent")|
|--ciąg dyspozycji zawartości|Ustaw nagłówek Content-Dyspozycja. Zwrócony przy pobieraniu.|
|--ciąg kodowania zawartości|Ustaw nagłówek Content-Encoding. Zwrócony przy pobieraniu.|
|--ciąg języka zawartości|Ustaw nagłówek Content-Language. Zwrócony przy pobieraniu.|
|--ciąg typu zawartości |Określa typ zawartości pliku. Oznacza brak typu MIME-Type. Zwrócony przy pobieraniu.|
|--Wyklucz ciąg|Wyklucz te pliki podczas kopiowania. Obsługa użycia *.|
|--Exclude-BLOB-Type String|Opcjonalnie określa typ obiektu BLOB (BlockBlob/PageBlob/AppendBlob) do wykluczenia podczas kopiowania obiektów blob z kontenera lub konta. Użycie tej flagi nie ma zastosowania do kopiowania danych z usług spoza usługi Azure do usługi. Więcej niż jeden obiekt BLOB powinien być oddzielony znakiem ";".|
|--Obserwuj-linków symbolicznych|Śledź linki symboliczne podczas przekazywania z lokalnego systemu plików.|
|--ciąg od do do|Opcjonalnie określa kombinację źródłowej lokalizacji docelowej. Na przykład: LocalBlob, BlobLocal, LocalBlobFS.|
|-h,--pomoc|Wyświetla zawartość pomocy dla polecenia copy. |
|--ciąg poziomu dziennika|Zdefiniuj poziom szczegółowości dziennika dla pliku dziennika, dostępne poziomy: INFORMACJE (wszystkie żądania/odpowiedzi), ostrzeżenie (wolne odpowiedzi), błąd (tylko Nieudane żądania) i brak (bez dzienników wyjściowych). (wartość domyślna to "INFO").|
|--ciąg metadanych|Przekaż do usługi Azure Storage za pomocą tych par klucz-wartość jako metadanych.|
|--No-zgadywanie-MIME-Type|Uniemożliwia programowi AzCopy wykrywanie typu zawartości na podstawie rozszerzenia lub zawartości pliku.|
|--overwrite|Zastąp pliki lub obiekty blob powodujące konflikt w miejscu docelowym, jeśli ta flaga ma wartość true. (wartość domyślna to true).|
|--Strona-ciąg warstwy obiektu BLOB |Przekazanie stronicowego obiektu BLOB do usługi Azure Storage przy użyciu tej warstwy obiektów BLOB. (domyślnie "none").|
|--Preserve-Last-Modified-Time|Dostępne tylko wtedy, gdy miejscem docelowym jest system plików.|
|--Put-MD5|Utwórz skrót MD5 każdego pliku i Zapisz skrót jako właściwość Content-MD5 docelowego obiektu BLOB lub pliku. (Domyślnie skrót nie jest tworzony). Dostępne tylko podczas przekazywania.|
|--rekursywnie|Poszukaj podkatalogów cyklicznie podczas przekazywania z lokalnego systemu plików.|
|--S2S-Detect-Source-zmiana|Sprawdź, czy po wyliczyć źródła uległo zmianie. W przypadku kopiowania danych S2S, jako źródło, jest zasobem zdalnym, sprawdzając, czy zmiana źródła wymaga dodatkowych kosztów żądania.|
|--S2S-Handle-nieprawidłowy ciąg metadanych |Określa sposób obsługi nieprawidłowych kluczy metadanych. Dostępne opcje: ExcludeIfInvalid, FailIfInvalid, RenameIfInvalid. (domyślnie "ExcludeIfInvalid").|
|--S2S-Preserve-dostęp-warstwa|Zachowaj warstwę dostępu podczas kopiowania usługi do usługi. Zapoznaj się z [usługą Azure Blob Storage: warstwami dostępu gorąca, chłodna i archiwalna](../blobs/storage-blob-storage-tiers.md) , aby upewnić się, że docelowe konto magazynu obsługuje ustawienie Warstwa dostępu. W przypadkach, gdy ustawienie warstwy dostępu nie jest obsługiwane, użyj s2sPreserveAccessTier = false, aby obejść kopiowanie warstwy dostępu.  (wartość domyślna to true).|
|--S2S-Preserve-Properties|Zachowaj pełne właściwości podczas kopiowania usługi do usługi. W przypadku S3 i plików platformy Azure, które nie są pojedynczym źródłem plików, jako operacja listy nie zwracają pełnych właściwości obiektów i plików, aby zachować pełne właściwości AzCopy musi wysyłać jedno dodatkowe żądanie dla każdego obiektu i pliku. (wartość domyślna to true).|

## <a name="options-inherited-from-parent-commands"></a>Opcje dziedziczone z poleceń nadrzędnych

|Opcja|Opis|
|---|---|
|--Cap-MB/s UInt32|Szybkość transferu w megabitach na sekundę. Przepływność czasu na chwilę może się nieco różnić od końca. Jeśli ta opcja jest ustawiona na zero lub zostanie pominięta, przepływność nie zostanie ograniczona.|
|--ciąg typu wyjściowego|Format danych wyjściowych polecenia. Dostępne opcje to: text, JSON. Wartość domyślna to "text".|

## <a name="see-also"></a>Zobacz także

- [AzCopy](storage-ref-azcopy.md)
