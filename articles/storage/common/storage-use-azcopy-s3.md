---
title: Kopiowanie danych z amazon S3 do usługi Azure Storage przy użyciu AzCopy | Dokumenty firmy Microsoft
description: Przesyłanie danych za pomocą wiader AzCopy i Amazon S3
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: a3180593eaf8c01c772fd761d88b5f5b9f7657ee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75941504"
---
# <a name="copy-data-from-amazon-s3-to-azure-storage-by-using-azcopy"></a>Kopiowanie danych z usługi Amazon S3 do usługi Azure Storage przy użyciu programu AzCopy

AzCopy to narzędzie wiersza polecenia, za pomocą którego można kopiować obiekty BLOB lub pliki do lub z konta magazynu. Ten artykuł ułatwia kopiowanie obiektów, katalogów i zasobników z amazon web services (AWS) S3 do magazynu obiektów blob platformy Azure przy użyciu programu AzCopy.

## <a name="choose-how-youll-provide-authorization-credentials"></a>Wybieranie sposobu podawania poświadczeń autoryzacji

* Aby autoryzować za pomocą usługi Azure Storage, użyj usługi Azure Active Directory (AD) lub tokenu sygnatury dostępu współdzielonego (SAS).

* Aby autoryzować za pomocą AWS S3, użyj klucza dostępu AWS i tajnego klucza dostępu.

### <a name="authorize-with-azure-storage"></a>Autoryzuj za pomocą usługi Azure Storage

Zobacz [wprowadzenie do AzCopy](storage-use-azcopy-v10.md) artykuł, aby pobrać AzCopy i wybierz sposób dostarczania poświadczeń autoryzacji do usługi magazynu.

> [!NOTE]
> Przykłady w tym artykule zakładają, że uwierzytelniłeś `AzCopy login` swoją tożsamość za pomocą polecenia. AzCopy następnie używa konta usługi Azure AD do autoryzowania dostępu do danych w magazynie obiektów Blob.
>
> Jeśli wolisz używać tokenu Sygnatury dostępu Współdzielonego do autoryzowania dostępu do danych obiektów blob, możesz dołączyć ten token do adresu URL zasobu w każdym poleceniu AzCopy.
>
> Na przykład: `https://mystorageaccount.blob.core.windows.net/mycontainer?<SAS-token>`.

### <a name="authorize-with-aws-s3"></a>Autoryzuj z AWS S3

Zbierz klucz dostępu AWS i tajny klucz dostępu, a następnie ustaw następujące zmienne środowiskowe:

| System operacyjny | Polecenie  |
|--------|-----------|
| **Windows** | `set AWS_ACCESS_KEY_ID=<access-key>`<br>`set AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **Linux** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **Macos** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>`|

## <a name="copy-objects-directories-and-buckets"></a>Kopiowanie obiektów, katalogów i zasobników

AzCopy używa interfejsu API [adresu URL Put Block From,](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) dzięki czemu dane są kopiowane bezpośrednio między AWS S3 a serwerami magazynu. Te operacje kopiowania nie używają przepustowości sieci komputera.

> [!IMPORTANT]
> Ta funkcja jest obecnie dostępna w wersji zapoznawczej. Jeśli zdecydujesz się usunąć dane z zasobników S3 po operacji kopiowania, przed usunięciem danych upewnij się, że dane zostały poprawnie skopiowane na konto magazynu.

> [!TIP]
> Przykłady w tej sekcji ujęć argumenty ścieżki z pojedynczymi cudzysłowami (''). Użyj pojedynczych cudzysłowów we wszystkich powłokach poleceń z wyjątkiem powłoki poleceń systemu Windows (cmd.exe). Jeśli używasz powłoki poleceń systemu Windows (cmd.exe), należy ująć argumenty ścieżki z podwójnymi cudzysłowami ("") zamiast pojedynczych cudzysłowów (').

 Te przykłady działają również z kontami, które mają hierarchiczną przestrzeń nazw. [Dostęp do wielu protokołów w magazynie usługi Data Lake](../blobs/data-lake-storage-multi-protocol-access.md) `blob.core.windows.net`umożliwia użycie tej samej składni adresu URL ( ) na tych kontach. 

### <a name="copy-an-object"></a>Kopiowanie obiektu

Użyj tej samej składni adresu URL (`blob.core.windows.net`) dla kont, które mają hierarchiczną przestrzeń nazw.

|    |     |
|--------|-----------|
| **Składni** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>/<object-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<blob-name>'` |
| **Przykład** | `azcopy copy 'https://s3.amazonaws.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'` |
| **Przykład** (hierarchiczna przestrzeń nazw) | `azcopy copy 'https://s3.amazonaws.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'` |

> [!NOTE]
> Przykłady w tym artykule używają adresów URL w stylu ścieżki `http://s3.amazonaws.com/<bucket-name>`dla zasobników AWS S3 (na przykład: ). 
>
> Można również użyć wirtualnych adresów URL w stylu `http://bucket.s3.amazonaws.com`hostowanym (na przykład: ). 
>
> Aby dowiedzieć się więcej o wirtualnym hostingu wiader, zobacz [Virtual Hosting wiader]](https://docs.aws.amazon.com/AmazonS3/latest/dev/VirtualHosting.html).

### <a name="copy-a-directory"></a>Kopiowanie katalogu

Użyj tej samej składni adresu URL (`blob.core.windows.net`) dla kont, które mają hierarchiczną przestrzeń nazw.

|    |     |
|--------|-----------|
| **Składni** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>/<directory-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true` |
| **Przykład** | `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |
| **Przykład** (hierarchiczna przestrzeń nazw)| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

### <a name="copy-a-bucket"></a>Kopiowanie zasobnika

Użyj tej samej składni adresu URL (`blob.core.windows.net`) dla kont, które mają hierarchiczną przestrzeń nazw.

|    |     |
|--------|-----------|
| **Składni** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive=true` |
| **Przykład** | `azcopy copy 'https://s3.amazonaws.com/mybucket' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive=true` |
| **Przykład** (hierarchiczna przestrzeń nazw)| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

### <a name="copy-all-buckets-in-all-regions"></a>Kopiowanie wszystkich zasobników we wszystkich regionach

Użyj tej samej składni adresu URL (`blob.core.windows.net`) dla kont, które mają hierarchiczną przestrzeń nazw.

|    |     |
|--------|-----------|
| **Składni** | `azcopy copy 'https://s3.amazonaws.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true` |
| **Przykład** | `azcopy copy 'https://s3.amazonaws.com' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |
| **Przykład** (hierarchiczna przestrzeń nazw)| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

### <a name="copy-all-buckets-in-a-specific-s3-region"></a>Kopiowanie wszystkich zasobników w określonym regionie S3

Użyj tej samej składni adresu URL (`blob.core.windows.net`) dla kont, które mają hierarchiczną przestrzeń nazw.

|    |     |
|--------|-----------|
| **Składni** | `azcopy copy 'https://s3-<region-name>.amazonaws.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true` |
| **Przykład** | `azcopy copy 'https://s3-rds.eu-north-1.amazonaws.com' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |
| **Przykład** (hierarchiczna przestrzeń nazw)| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

## <a name="handle-differences-in-object-naming-rules"></a>Obsługa różnic w regułach nazewnictwa obiektów

Usługa AWS S3 ma inny zestaw konwencji nazewnictwa nazw zasobników w porównaniu z kontenerami obiektów blob platformy Azure. Możesz przeczytać o nich [tutaj](https://docs.aws.amazon.com/AmazonS3/latest/dev/BucketRestrictions.html#bucketnamingrules). Jeśli zdecydujesz się skopiować grupę zasobników do konta magazynu platformy Azure, operacja kopiowania może zakończyć się niepowodzeniem z powodu różnic nazewnictwa.

AzCopy obsługuje dwa z najczęstszych problemów, które mogą wystąpić; zasobników zawierających kropki i zasobniki zawierające kolejne łączniki. Nazwy zasobników usługi AWS S3 mogą zawierać okresy i kolejne łączniki, ale kontener na platformie Azure nie może. AzCopy zastępuje kropki myślnikami i kolejnymi myślnikami liczbą reprezentującą liczbę kolejnych łączników (Na przykład: wiadro o nazwie `my----bucket` staje się `my-4-bucket`. 

Ponadto, jak AzCopy kopiuje pliki, sprawdza, czy kolizje nazewnictwa i próbuje je rozwiązać. Na przykład, jeśli istnieją zasobniki `bucket-name` `bucket.name`o nazwie i , AzCopy `bucket-name` rozpoznaje `bucket-name-2`zasobnik o nazwie `bucket.name` najpierw do, a następnie do .

## <a name="handle-differences-in-object-metadata"></a>Obsługa różnic w metadanych obiektów

AWS S3 i Azure zezwalają na różne zestawy znaków w nazwach kluczy obiektów. Możesz przeczytać o znakach, które AWS S3 używa [tutaj](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingMetadata.html#object-keys). On the Azure side, blob object keys adhere to the naming rules for [C# identifiers](https://docs.microsoft.com/dotnet/csharp/language-reference/).

W ramach polecenia AzCopy `copy` można podać wartość opcjonalną `s2s-invalid-metadata-handle` flagę, która określa sposób obsługi plików, w których metadane pliku zawierają niezgodne nazwy kluczy. W poniższej tabeli opisano każdą wartość flagi.

| Wartość flagi | Opis  |
|--------|-----------|
| **ExcludeIfInvalid (ExcludeIfInvalid)** | (Opcja domyślna) Metadane nie są uwzględniane w przesłanym obiekcie. AzCopy rejestruje ostrzeżenie. |
| **FailIfInvalid (FailIfInvalid)** | Obiekty nie są kopiowane. AzCopy rejestruje błąd i zawiera ten błąd w liczniku nie powiodło się, który pojawia się w podsumowaniu transferu.  |
| **RenameIfInvalid**  | AzCopy rozwiązuje nieprawidłowy klucz metadanych i kopiuje obiekt na platformę Azure przy użyciu pary wartości klucza metadanych rozwiązanych. Aby dowiedzieć się dokładnie, jakie kroki wykonuje AzCopy, aby zmienić nazwę kluczy obiektów, zobacz [sekcję Jak AzCopy zmienia nazwy klawiszy obiektów](#rename-logic) poniżej. Jeśli AzCopy nie może zmienić nazwy klucza, obiekt nie zostanie skopiowany. |

<a id="rename-logic" />

### <a name="how-azcopy-renames-object-keys"></a>Jak AzCopy zmienia nazwę kluczy obiektów

AzCopy wykonuje następujące kroki:

1. Zastępuje nieprawidłowe znaki '_'.

2. Dodaje ciąg `rename_` na początku nowego prawidłowego klucza.

   Ten klucz będzie używany do zapisywania oryginalnej **wartości**metadanych .

3. Dodaje ciąg `rename_key_` na początku nowego prawidłowego klucza.
   Ten klucz będzie używany do zapisywania oryginalnych metadanych nieprawidłowy **klucz**.
   Ten klucz służy do próby odzyskania metadanych po stronie platformy Azure, ponieważ klucz metadanych jest zachowywany jako wartość w usłudze magazynu obiektów Blob.

## <a name="next-steps"></a>Następne kroki

Znajdź więcej przykładów w każdym z tych artykułów:

- [Wprowadzenie do narzędzia AzCopy](storage-use-azcopy-v10.md)

- [Przesyłanie danych za pomocą pamięci masowej AzCopy i obiektów blob](storage-use-azcopy-blobs.md)

- [Przesyłanie danych za pomocą AzCopy i przechowywania plików](storage-use-azcopy-files.md)

- [Konfigurowanie, optymalizowanie i rozwiązywanie problemów z programem AzCopy](storage-use-azcopy-configure.md)