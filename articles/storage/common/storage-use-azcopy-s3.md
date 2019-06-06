---
title: Przesyłanie danych do usługi Azure Storage z zasobników Amazon S3, za pomocą narzędzia AzCopy v10 | Dokumentacja firmy Microsoft
description: Transferowanie danych za pomocą narzędzia AzCopy i Amazon S3 przedziałów
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 04/23/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: b18c4c039b615c7c88268b6e668df9f7fec9fabf
ms.sourcegitcommit: 6932af4f4222786476fdf62e1e0bf09295d723a1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2019
ms.locfileid: "66687914"
---
# <a name="copy-data-from-amazon-s3-buckets-by-using-azcopy"></a>Skopiuj dane z zasobników Amazon S3 za pomocą narzędzia AzCopy

AzCopy to narzędzie wiersza polecenia używanej do kopiowania obiektów blob lub plików, do lub z konta magazynu. Ten artykuł pomoże Ci skopiowania obiektów, katalogów i zasobników S3 usługi Amazon Web Services (AWS) do usługi Azure blob storage za pomocą narzędzia AzCopy.

## <a name="choose-how-youll-provide-authorization-credentials"></a>Wybierz, jak zapewnisz poświadczenia autoryzacji

* Aby autoryzować z usługą Azure Storage, użyj usługi Azure Active Directory (AD) lub tokenu sygnatury dostępu współdzielonego (SAS).

* Do autoryzacji w usłudze AWS S3, użyj klawisza dostępu AWS i klucz dostępu do kluczy tajnych.

### <a name="authorize-with-azure-storage"></a>Autoryzuj przy użyciu usługi Azure Storage

Zobacz [Rozpoczynanie pracy za pomocą narzędzia AzCopy](storage-use-azcopy-v10.md) artykuł, aby pobrać narzędzia AzCopy, a następnie wybierz, jak zapewnisz poświadczenia autoryzacji do usługi storage.

> [!NOTE]
> Przykłady w niniejszym artykule przyjęto założenie, że uwierzytelniono swoją tożsamość za pomocą `AzCopy login` polecenia. Narzędzie AzCopy następnie używa konta usługi Azure AD do autoryzowania dostępu do danych w magazynie obiektów Blob.
>
> Jeśli zostanie wykorzystany raczej tokenu sygnatury dostępu Współdzielonego do autoryzowania dostępu do danych obiektów blob, można dołączyć ten token do adresu URL zasobu w każdym poleceniu narzędzia AzCopy.
>
> Na przykład: `https://mystorageaccount.blob.core.windows.net/mycontainer?<SAS-token>`.

### <a name="authorize-with-aws-s3"></a>Autoryzować w usłudze AWS S3

Zbierz swój klucz dostępu AWS i klucz dostępu do kluczy tajnych, a następnie ustaw te zmienne środowiskowe:

| System operacyjny | Polecenie  |
|--------|-----------|
| **Windows** | `set AWS_ACCESS_KEY_ID=<access-key>`<br>`set AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **Linux** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **MacOS** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>`|

## <a name="copy-objects-directories-and-buckets"></a>Kopiuje obiekty, katalogów i przedziałów

Korzysta z narzędzia AzCopy [umieścić blok z adresu URL](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) interfejsu API, dzięki czemu dane są kopiowane bezpośrednio między AWS S3, a serwery pamięci masowej. Przepustowość sieci na komputerze nie należy używać tych operacji kopiowania.

### <a name="copy-an-object"></a>Kopiowanie obiektu

|    |     |
|--------|-----------|
| **Składnia** | `azcopy cp "https://s3.amazonaws.com/<bucket-name>/<object-name>" "https://<storage-account-name>.blob.core.windows.net/<container-name>/<blob-name>"` |
| **Przykład** | `azcopy cp "https://s3.amazonaws.com/mybucket/myobject" "https://mystorageaccount.blob.core.windows.net/mycontainer/myblob"` |

> [!NOTE]
> Przykłady w niniejszym artykule korzystać styl ścieżki adresów URL, dla przedziałów AWS S3 (na przykład: `http://s3.amazonaws.com/<bucket-name>`). 
>
> Możesz również użyć wirtualnych także hostowanych styl adresów URL. (na przykład: `http://bucket.s3.amazonaws.com`). 
>
> Aby dowiedzieć się więcej o hostingu wirtualnego zasobników, zobacz [wirtualnego hostingu z zasobników]] (https://docs.aws.amazon.com/AmazonS3/latest/dev/VirtualHosting.html).

### <a name="copy-a-directory"></a>Kopiowanie katalogu

|    |     |
|--------|-----------|
| **Składnia** | `azcopy cp "https://s3.amazonaws.com/<bucket-name>/<directory-name>" "https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>" --recursive=true` |
| **Przykład** | `azcopy cp "https://s3.amazonaws.com/mybucket/mydirectory" "https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory" --recursive=true` |

### <a name="copy-a-bucket"></a>Skopiuj koszyka

|    |     |
|--------|-----------|
| **Składnia** | `azcopy cp "https://s3.amazonaws.com/<bucket-name>" "https://<storage-account-name>.blob.core.windows.net/<container-name>" --recursive=true` |
| **Przykład** | `azcopy cp "https://s3.amazonaws.com/mybucket" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive=true` |

### <a name="copy-all-buckets-in-all-regions"></a>Skopiuj wszystkie przedziały we wszystkich regionach

|    |     |
|--------|-----------|
| **Składnia** | `azcopy cp "https://s3.amazonaws.com/" "https://<storage-account-name>.blob.core.windows.net" --recursive=true` |
| **Przykład** | `azcopy cp "https://s3.amazonaws.com" "https://mystorageaccount.blob.core.windows.net" --recursive=true` |

### <a name="copy-all-buckets-in-a-specific-s3-region"></a>Skopiuj wszystkie przedziały w określonym regionie S3

|    |     |
|--------|-----------|
| **Składnia** | `azcopy cp "https://s3-<region-name>.amazonaws.com/" "https://<storage-account-name>.blob.core.windows.net" --recursive=true` |
| **Przykład** | `azcopy cp "https://s3-rds.eu-north-1.amazonaws.com" "https://mystorageaccount.blob.core.windows.net" --recursive=true` |

## <a name="handle-differences-in-object-naming-rules"></a>Obsługa różnice w obiekcie reguły nazewnictwa

AWS S3 ma inny zestaw konwencje nazewnictwa dla nazwy zasobnika w porównaniu do kontenerów obiektów blob platformy Azure. Informacje o ich [tutaj](https://docs.aws.amazon.com/AmazonS3/latest/dev/BucketRestrictions.html#bucketnamingrules). Jeśli chcesz skopiować grupę zasobników do konta usługi Azure storage, operacja kopiowania może zakończyć się niepowodzeniem ze względu na różnice nazewnictwa.

Narzędzie AzCopy obsługuje dwie najbardziej typowe problemy, które mogą wystąpić; pakiety, które zawierają kropki i pakiety, które może zawierać następujących po sobie łączników. Nazwy zasobnika AWS S3 mogą zawierać kropki i następujących po sobie łączników, ale nie kontener na platformie Azure. Narzędzie AzCopy zastępuje kropki łączniki i następujących po sobie łączników numerem, który reprezentuje liczbę występujących po sobie łączników (na przykład: zasobnik, o nazwie `my----bucket` staje się `my-4-bucket`. 

Ponadto zgodnie z narzędzia AzCopy kopiuje pliki, sprawdza kolizji nazw i spróbuje je rozwiązać. Na przykład, jeśli pakiety o nazwie `bucket-name` i `bucket.name`, narzędzia AzCopy jest rozpoznawana jako przedział, o nazwie `bucket.name` pierwszego do `bucket-name` a `bucket-name-2`.

## <a name="handle-differences-in-object-metadata"></a>Obsługa różnice w metadane obiektu

AWS S3 i na platformie Azure umożliwiają różnych zestawów znaków w nazwach kluczy obiektu. Możesz przeczytać temat znaków, AWS S3 używa [tutaj](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingMetadata.html#object-keys). Na stronie platformy Azure, klucze obiektów blob stosować się do reguł nazewnictwa [ C# identyfikatory](https://docs.microsoft.com/dotnet/csharp/language-reference/).

W ramach programu AzCopy `copy` polecenia, można podać wartości opcjonalne `s2s-invalid-metadata-handle` flagę, która określa sposób obsługi plików, gdy metadane pliku zawiera niezgodne nazwy kluczy. W poniższej tabeli opisano każdą wartość flagi.

| Wartość flagi | Opis  |
|--------|-----------|
| **ExcludeIfInvalid** | (Opcja domyślna) Metadane nie jest zawarty w obiekcie przeniesione. Narzędzie AzCopy rejestruje ostrzeżenie. |
| **FailIfInvalid** | Obiekty nie są kopiowane. Narzędzie AzCopy rejestruje błąd i dołącza ten błąd w liczba niepowodzeń, który pojawia się w podsumowaniu transferu.  |
| **RenameIfInvalid**  | Narzędzie AzCopy jest rozpoznawana jako klucz nieprawidłowe metadane i kopiuje obiekt na platformie Azure przy użyciu metadanych rozwiązania parę klucz-wartość. Dokładnie poznać kroki, jakie ma narzędzia AzCopy można zmienić nazwy kluczy obiektu można znaleźć [jak AzCopy zmienia nazwę obiektu klucze](#rename-logic) poniższej sekcji. Jeśli narzędzie AzCopy jest nie można zmienić nazwy klucza, nie można skopiować obiektu. |

<a id="rename-logic" />

### <a name="how-azcopy-renames-object-keys"></a>Jak narzędzia AzCopy zmienia nazwę klucze obiektów

Narzędzie AzCopy wykonuje następujące czynności:

1. Zamienia nieprawidłowe znaki znakiem "_".

2. Dodaje ciąg `rename_` na początku nowego prawidłowego klucza.

   Ten klucz będzie używany do zapisania odpowiednich oryginalnych metadanych **wartość**.

3. Dodaje ciąg `rename_key_` na początku nowego prawidłowego klucza.
   Ten klucz będzie używany do zapisania oryginalnych metadanych jest nieprawidłowy **klucz**.
   Odzyskiwanie metadanych w stronie platformy Azure, ponieważ klucza metadanych są zachowywane jako wartości na usługi Blob storage i spróbuj, można użyć tego klucza.

## <a name="next-steps"></a>Kolejne kroki

Znajdź więcej przykładów w dowolnym z następujących artykułów:

- [Rozpoczynanie pracy za pomocą narzędzia AzCopy](storage-use-azcopy-v10.md)

- [Transferowanie danych za pomocą narzędzia AzCopy i blob storage](storage-use-azcopy-blobs.md)

- [Transferowanie danych za pomocą narzędzia AzCopy oraz plików magazynu](storage-use-azcopy-files.md)

- [Konfigurowanie optymalizacji i rozwiązywanie problemów z narzędzia AzCopy](storage-use-azcopy-configure.md)