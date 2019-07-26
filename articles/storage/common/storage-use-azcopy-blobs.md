---
title: Transferowanie danych do lub z usługi Azure Blob Storage za pomocą AzCopy v10 | Microsoft Docs
description: Ten artykuł zawiera kolekcję przykładowych poleceń AzCopy, które pomagają w tworzeniu kontenerów, kopiowaniu plików i synchronizowaniu katalogów między lokalnymi systemami plików i kontenerami.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: d1bb0dc0e5c9cca0a9570e9074a294afdeb99455
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/25/2019
ms.locfileid: "68501379"
---
# <a name="transfer-data-with-azcopy-and-blob-storage"></a>Transferowanie danych za pomocą AzCopy i magazynu obiektów BLOB

AzCopy to narzędzie wiersza polecenia, za pomocą którego można kopiować dane do, z lub między kontami magazynu. Ten artykuł zawiera przykładowe polecenia, które działają z usługą BLOB Storage.

## <a name="get-started"></a>Wprowadzenie

Zapoznaj się z artykułem [wprowadzenie do AzCopy](storage-use-azcopy-v10.md) w celu pobrania AzCopy i Dowiedz się więcej na temat sposobów dostarczania poświadczeń autoryzacji do usługi magazynu.

> [!NOTE]
> W przykładach w tym artykule przyjęto założenie, że tożsamość została uwierzytelniona `AzCopy login` przy użyciu polecenia. AzCopy następnie używa konta usługi Azure AD do autoryzacji dostępu do danych w usłudze BLOB Storage.
>
> Jeśli wolisz używać tokenu SAS do autoryzacji dostępu do danych obiektów blob, możesz dołączyć ten token do adresu URL zasobu w każdym poleceniu AzCopy.
>
> Na przykład: `https://<storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>"`.

## <a name="create-a-container"></a>Tworzenie kontenera

Aby utworzyć kontener, można `make` użyć polecenia AzCopy. Przykłady w tej sekcji tworzą kontener o nazwie `mycontainer`.

|    |     |
|--------|-----------|
| **Składnia** | `azcopy make "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>` |
| **Przykład** | `azcopy make "https://mystorageaccount.blob.core.windows.net/mycontainer"` |
| **Przykład** (hierarchiczna przestrzeń nazw) | `azcopy make "https://mystorageaccount.dfs.core.windows.net/mycontainer"` |

## <a name="upload-files"></a>Przekazywanie plików

Aby przekazać pliki i katalogi `copy` z komputera lokalnego, można użyć polecenia AzCopy.

Ta sekcja zawiera następujące przykłady:

> [!div class="checklist"]
> * Przekaż plik
> * Przekaż katalog
> * Przekazywanie plików przy użyciu symboli wieloznacznych

> [!NOTE]
> AzCopy nie oblicza automatycznie ani nie zapisuje kodu skrótu MD5 pliku. Jeśli chcesz, aby AzCopy to zrobić, Dodaj `--put-md5` flagę do poszczególnych poleceń kopiowania. Dzięki temu po pobraniu obiektu BLOB AzCopy oblicza skrót MD5 dla pobranych danych i sprawdza, czy skrót MD5 przechowywany we `Content-md5` właściwości obiektu BLOB jest zgodny z obliczonym skrótem.

### <a name="upload-a-file"></a>Przekaż plik

|    |     |
|--------|-----------|
| **Składnia** | `azcopy cp "<local-file-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>"` |
| **Przykład** | `azcopy copy "C:\myDirectory\myTextFile.txt" "https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt"` |
| **Przykład** (hierarchiczna przestrzeń nazw) | `azcopy copy "C:\myDirectory\myTextFile.txt" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt"` |

> [!NOTE]
> AzCopy domyślnie przekazuje dane do blokowych obiektów BLOB. Aby przekazać pliki jako Dołącz obiekty blob lub stronicowe obiekty blob, `--blob-type=[BlockBlob|PageBlob|AppendBlob]`Użyj flagi.

### <a name="upload-a-directory"></a>Przekaż katalog

Ten przykład kopiuje katalog (i wszystkie pliki w tym katalogu) do kontenera obiektów BLOB. Wynik jest katalogiem w kontenerze o tej samej nazwie.

|    |     |
|--------|-----------|
| **Składnia** | `azcopy copy "<local-directory-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive` |
| **Przykład** | `azcopy copy "C:\myDirectory" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive` |
| **Przykład** (hierarchiczna przestrzeń nazw) | `azcopy copy "C:\myDirectory" "https://mystorageaccount.dfs.core.windows.net/mycontainer" --recursive` |

Aby skopiować do katalogu w kontenerze, należy po prostu określić nazwę tego katalogu w ciągu polecenia.

|    |     |
|--------|-----------|
| **Przykład** | `azcopy copy "C:\myDirectory" "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory" --recursive` |
| **Przykład** (hierarchiczna przestrzeń nazw) | `azcopy copy "C:\myDirectory" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory" --recursive` |

W przypadku określenia nazwy katalogu, który nie istnieje w kontenerze, AzCopy tworzy nowy katalog o tej nazwie.

### <a name="upload-the-contents-of-a-directory"></a>Przekaż zawartość katalogu

Zawartość katalogu można przekazać bez kopiowania samego katalogu zawierającego symbol wieloznaczny (*).

|    |     |
|--------|-----------|
| **Składnia** | `azcopy copy "<local-directory-path>\*" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>` |
| **Przykład** | `azcopy copy "C:\myDirectory\*" "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory"` |
| **Przykład** (hierarchiczna przestrzeń nazw) | `azcopy copy "C:\myDirectory\*" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory"` |

> [!NOTE]
> `--recursive` Dołącz flagę do przekazywania plików we wszystkich podkatalogach.

## <a name="download-files"></a>Pobieranie plików

Można użyć polecenia AzCopy `copy` , aby pobrać obiekty blob, katalogi i kontenery na komputer lokalny.

Ta sekcja zawiera następujące przykłady:

> [!div class="checklist"]
> * Pobierz plik
> * Pobierz katalog
> * Pobieranie plików przy użyciu symboli wieloznacznych

> [!NOTE]
> Jeśli wartość `Content-md5` właściwości obiektu BLOB zawiera skrót, AzCopy oblicza skrót MD5 dla pobranych danych i sprawdza, czy skrót MD5 przechowywany we właściwości obiektu BLOB jest zgodny z obliczonym skrótem. `Content-md5` Jeśli te wartości nie są zgodne, pobieranie nie powiedzie się, o ile nie zastąpisz tego zachowania przez dołączenie `--check-md5=NoCheck` lub `--check-md5=LogOnly` do polecenia copy.

### <a name="download-a-file"></a>Pobierz plik

|    |     |
|--------|-----------|
| **Składnia** | `azcopy copy "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>" "<local-file-path>"` |
| **Przykład** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt" "C:\myDirectory\myTextFile.txt"` |
| **Przykład** (hierarchiczna przestrzeń nazw) | `azcopy copy "https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt" "C:\myDirectory\myTextFile.txt"` |

### <a name="download-a-directory"></a>Pobierz katalog

|    |     |
|--------|-----------|
| **Składnia** | `azcopy copy "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>" "<local-directory-path>" --recursive` |
| **Przykład** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory "C:\myDirectory"  --recursive` |
| **Przykład** (hierarchiczna przestrzeń nazw) | `azcopy copy "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory "C:\myDirectory"  --recursive` |

Ten przykład powoduje, że katalog o `C:\myDirectory\myBlobDirectory` nazwie zawiera wszystkie pobrane pliki.

### <a name="download-the-contents-of-a-directory"></a>Pobierz zawartość katalogu

Zawartość katalogu można pobrać bez kopiowania samego katalogu zawierającego symbol wieloznaczny (*).

> [!NOTE]
> Obecnie ten scenariusz jest obsługiwany tylko w przypadku kont, które nie mają hierarchicznej przestrzeni nazw.

|    |     |
|--------|-----------|
| **Składnia** | `azcopy copy "https://<storage-account-name>.blob.core.windows.net/<container-name>/*" "<local-directory-path>/"` |
| **Przykład** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory/*" "C:\myDirectory"` |

> [!NOTE]
> `--recursive` Dołącz flagę do pobierania plików we wszystkich podkatalogach.

## <a name="copy-blobs-between-storage-accounts"></a>Kopiowanie obiektów BLOB między kontami magazynu

Możesz użyć AzCopy, aby skopiować obiekty blob do innych kont magazynu. Operacja kopiowania jest synchroniczna, dlatego gdy polecenie zwróci wartość, która wskazuje, że wszystkie pliki zostały skopiowane.

> [!NOTE]
> Obecnie ten scenariusz jest obsługiwany tylko w przypadku kont, które nie mają hierarchicznej przestrzeni nazw. 

AzCopy używa [interfejsów API](https://docs.microsoft.com/en-us/rest/api/storageservices/put-page-from-url) [serwer-serwer](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) , aby dane były kopiowane bezpośrednio między serwerami magazynu. Te operacje kopiowania nie korzystają z przepustowości sieci komputera.

Ta sekcja zawiera następujące przykłady:

> [!div class="checklist"]
> * Kopiowanie obiektu BLOB do innego konta magazynu
> * Kopiowanie katalogu do innego konta magazynu
> * Kopiowanie kontenerów na inne konto magazynu
> * Kopiuj wszystkie kontenery, katalogi i pliki na inne konto magazynu

> [!NOTE]
> W bieżącej wersji należy dołączyć token sygnatury dostępu współdzielonego do każdego źródłowego adresu URL. W przypadku podania poświadczeń autoryzacji za pomocą Azure Active Directory (AD) można pominąć token sygnatury dostępu współdzielonego tylko z docelowego adresu URL. 

### <a name="copy-a-blob-to-another-storage-account"></a>Kopiowanie obiektu BLOB do innego konta magazynu

|    |     |
|--------|-----------|
| **Składnia** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>?<SAS-token>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>"` |
| **Przykład** | `azcopy cp "https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D" "https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt"` |

### <a name="copy-a-directory-to-another-storage-account"></a>Kopiowanie katalogu do innego konta magazynu

|    |     |
|--------|-----------|
| **Składnia** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path>?<SAS-token>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path>" --recursive` |
| **Przykład** | `azcopy cp "https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D" "https://mydestinationaccount.blob.core.windows.net/mycontainer/myBlobDirectory" --recursive` |

### <a name="copy-a-containers-to-another-storage-account"></a>Kopiowanie kontenerów na inne konto magazynu

|    |     |
|--------|-----------|
| **Składnia** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>" --recursive` |
| **Przykład** | `azcopy cp "https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D" "https://mydestinationaccount.blob.core.windows.net/mycontainer" --recursive` |

### <a name="copy-all-containers-directories-and-files-to-another-storage-account"></a>Kopiuj wszystkie kontenery, katalogi i pliki na inne konto magazynu

|    |     |
|--------|-----------|
| **Składnia** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/?<SAS-token>" "https://<destination-storage-account-name>.blob.core.windows.net/" --recursive"` |
| **Przykład** | `azcopy cp "https://mysourceaccount.blob.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D" "https://mydestinationaccount.blob.core.windows.net" --recursive` |

## <a name="synchronize-files"></a>Synchronizuj pliki

Zawartość lokalnego systemu plików można zsynchronizować z kontenerem obiektów BLOB. Synchronizacja jest jednokierunkowa. Innymi słowy, możesz wybrać, które z tych dwóch punktów końcowych są źródłem, a które są lokalizacją docelową.

> [!NOTE]
> Obecnie ten scenariusz jest obsługiwany tylko w przypadku kont, które nie mają hierarchicznej przestrzeni nazw. Bieżąca wersja AzCopy nie jest synchronizowana między innymi źródłami i miejscami docelowymi (na przykład: Zasobniki magazynu plików lub Amazon Web Services (AWS) S3).

`sync` Polecenie porównuje nazwy plików i ostatnio modyfikowane sygnatury czasowe. Ustaw opcjonalną flagę na `true` wartość lub `prompt` , aby usunąć pliki w katalogu docelowym, jeśli te pliki nie znajdują się już w katalogu źródłowym. `--delete-destination`

Jeśli ustawisz `--delete-destination` flagę na `true` AzCopy usuwa pliki bez wyświetlania monitu. Jeśli chcesz, aby monit pojawił się zanim AzCopy usunie plik, ustaw `--delete-destination` flagę na. `prompt`

> [!NOTE]
> Aby zapobiec przypadkowemu usunięciu, upewnij się, że funkcja [usuwania](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) nietrwałego została włączona `--delete-destination=prompt|true` przed użyciem flagi.

### <a name="update-a-container-with-changes-to-a-local-file-system"></a>Aktualizowanie kontenera ze zmianami w lokalnym systemie plików

W takim przypadku kontener jest miejscem docelowym, a lokalny system plików jest źródłem.

|    |     |
|--------|-----------|
| **Składnia** | `azcopy sync "<local-directory-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive` |
| **Przykład** | `azcopy sync "C:\myDirectory" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive` |

### <a name="update-a-local-file-system-with-changes-to-a-container"></a>Aktualizowanie lokalnego systemu plików przy użyciu zmian w kontenerze

W takim przypadku lokalny system plików jest miejscem docelowym, a kontener jest źródłem.

|    |     |
|--------|-----------|
| **Składnia** | `azcopy sync "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" "C:\myDirectory" --recursive` |
| **Przykład** | `azcopy sync "https://mystorageaccount.blob.core.windows.net/mycontainer" "C:\myDirectory" --recursive` |
|

## <a name="next-steps"></a>Następne kroki

Więcej przykładów znajdziesz w jednym z następujących artykułów:

- [Wprowadzenie do AzCopy](storage-use-azcopy-v10.md)

- [Samouczek: Migrowanie danych lokalnych do magazynu w chmurze za pomocą AzCopy](storage-use-azcopy-migrate-on-premises-data.md)

- [Transferowanie danych za pomocą AzCopy i magazynu plików](storage-use-azcopy-files.md)

- [Transferowanie danych za pomocą zasobników AzCopy i Amazon S3](storage-use-azcopy-s3.md)

- [Konfigurowanie, optymalizowanie i rozwiązywanie problemów z AzCopy](storage-use-azcopy-configure.md)
