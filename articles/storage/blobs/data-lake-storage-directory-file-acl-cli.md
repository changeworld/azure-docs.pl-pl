---
title: Użyj interfejsu wiersza polecenia platformy Azure dla plików & list ACl w usłudze Azure Data Lake Storage Gen2 (wersja zapoznawcza)
description: Użyj interfejsu wiersza polecenia platformy Azure do zarządzania katalogami oraz listami kontroli dostępu do plików i katalogów (ACL) na kontach magazynu, które mają hierarchiczny obszar nazw.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: conceptual
ms.date: 11/24/2019
ms.author: normesta
ms.reviewer: prishet
ms.openlocfilehash: ce2b4200496938e6cffb935207df8c7027eaf37a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77486138"
---
# <a name="use-azure-cli-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2-preview"></a>Używanie interfejsu wiersza polecenia platformy Azure do zarządzania katalogami, plikami i listami ACl w usłudze Azure Data Lake Storage Gen2 (wersja zapoznawcza)

W tym artykule pokazano, jak używać [interfejsu wiersza polecenia platformy Azure (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) do tworzenia katalogów, plików i uprawnień na kontach magazynu, które mają hierarchiczną przestrzeń nazw i zarządzanie nimi. 

> [!IMPORTANT]
> Rozszerzenie, `storage-preview` które jest opisywany w tym artykule jest obecnie w publicznej wersji zapoznawczej.

[Przykładowe](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#adls-gen2-support) |  | [mapowanie Gen1 do Gen2](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#mapping-from-adls-gen1-to-adls-gen2)[Przekaż opinię](https://github.com/Azure/azure-cli-extensions/issues)
## <a name="prerequisites"></a>Wymagania wstępne

> [!div class="checklist"]
> * Subskrypcja platformy Azure. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Konto magazynu z włączoną hierarchiczną przestrzenią nazw (HNS). Postępuj zgodnie z [tymi](data-lake-storage-quickstart-create-account.md) instrukcjami, aby je utworzyć.
> * Wersja `2.0.67` interfejsu WIERSZA POLECENIA platformy Azure lub nowsza.

## <a name="install-the-storage-cli-extension"></a>Instalowanie rozszerzenia interfejsu wiersza polecenia magazynu

1. Otwórz [usługę Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)lub jeśli [zainstalowano](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) interfejs wiersza polecenia platformy Azure lokalnie, otwórz aplikację konsoli poleceń, taką jak Windows PowerShell.

2. Sprawdź, czy zainstalowana wersja interfejsu `2.0.67` wiersza polecenia platformy Azure jest lub wyższa za pomocą następującego polecenia.

   ```azurecli
    az --version
   ```
   Jeśli twoja wersja interfejsu wiersza polecenia platformy Azure jest niższa niż `2.0.67`, a następnie zainstaluj nowszą wersję. Zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

3. Należy zainstalować rozszerzenie `storage-preview`.

   ```azurecli
   az extension add -n storage-preview
   ```

## <a name="connect-to-the-account"></a>Łączenie się z kontem

1. Jeśli używasz interfejsu wiersza polecenia platformy Azure lokalnie, uruchom polecenie logowania.

   ```azurecli
   az login
   ```

   Jeśli interfejs wiersza polecenia można otworzyć domyślną przeglądarkę, zrobi to i załadować stronę logowania platformy Azure.

   W przeciwnym razie otwórz [https://aka.ms/devicelogin](https://aka.ms/devicelogin) stronę przeglądarki i wprowadź kod autoryzacji wyświetlany w terminalu. Następnie zaloguj się przy użyciu poświadczeń konta w przeglądarce.

   Aby dowiedzieć się więcej na temat różnych metod uwierzytelniania, zobacz Logowanie się za pomocą interfejsu wiersza polecenia platformy Azure.

2. Jeśli Twoja tożsamość jest skojarzona z więcej niż jedną subskrypcją, ustaw aktywną subskrypcję na subskrypcję konta magazynu, które będzie hostować statyczną witrynę sieci Web.

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   Zastąp wartość symbolu `<subscription-id>` zastępczego identyfikatorem subskrypcji.

## <a name="create-a-file-system"></a>Tworzenie systemu plików

System plików działa jako kontener dla plików. Można go utworzyć za `az storage container create` pomocą polecenia. 

W tym przykładzie `my-file-system`tworzy system plików o nazwie .

```azurecli
az storage container create --name my-file-system --account-name mystorageaccount
```

## <a name="create-a-directory"></a>Tworzenie katalogu

Utwórz odwołanie do katalogu `az storage blob directory create` za pomocą polecenia. 

W tym przykładzie `my-directory` dodaje katalog o `my-file-system` nazwie do systemu `mystorageaccount`plików o nazwie, który znajduje się na koncie o nazwie .

```azurecli
az storage blob directory create -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="show-directory-properties"></a>Pokaż właściwości katalogu

Właściwości katalogu można wydrukować na konsoli za pomocą `az storage blob show` polecenia.

```azurecli
az storage blob directory show -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="rename-or-move-a-directory"></a>Zmienianie nazwy lub przenoszenie katalogu

Zmień nazwę lub przenieś katalog `az storage blob directory move` za pomocą polecenia.

W tym przykładzie zmienia nazwę `my-directory` katalogu `my-new-directory`z nazwy na nazwę .

```azurecli
az storage blob directory move -c my-file-system -d my-new-directory -s my-directory --account-name mystorageaccount
```

## <a name="delete-a-directory"></a>Usuwanie katalogu

Usuń katalog za pomocą `az storage blob directory delete` polecenia.

W tym przykładzie usuwa `my-directory`katalog o nazwie . 

```azurecli
az storage blob directory delete -c my-file-system -d my-directory --account-name mystorageaccount 
```

## <a name="check-if-a-directory-exists"></a>Sprawdzanie, czy istnieje katalog

Określ, czy określony katalog istnieje w systemie `az storage blob directory exist` plików za pomocą polecenia.

W tym przykładzie pokazano, czy `my-file-system` w systemie plików istnieje nazwany `my-directory` katalog. 

```azurecli
az storage blob directory exists -c my-file-system -d my-directory --account-name mystorageaccount 
```

## <a name="download-from-a-directory"></a>Pobieranie z katalogu

Pobierz plik z katalogu za `az storage blob directory download` pomocą polecenia.

W tym przykładzie `upload.txt` pobrano plik `my-directory`o nazwie z katalogu o nazwie . 

```azurecli
az storage blob directory download -c my-file-system --account-name mystorageaccount -s "my-directory/upload.txt" -d "C:\mylocalfolder\download.txt"
```

W tym przykładzie pobiera cały katalog.

```azurecli
az storage blob directory download -c my-file-system --account-name mystorageaccount -s "my-directory/" -d "C:\mylocalfolder" --recursive
```

## <a name="list-directory-contents"></a>Wyświetlanie zawartości katalogu

Wyekslij zawartość katalogu `az storage blob directory list` za pomocą polecenia.

W tym przykładzie wymieniono `my-directory` zawartość katalogu o `my-file-system` nazwie, który znajduje `mystorageaccount`się w systemie plików konta magazynu o nazwie . 

```azurecli
az storage blob directory list -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="upload-a-file-to-a-directory"></a>Przekazywanie pliku do katalogu

Przekaż plik do katalogu za `az storage blob directory upload` pomocą polecenia.

W tym przykładzie `upload.txt` przekazuje plik o `my-directory`nazwie do katalogu o nazwie . 

```azurecli
az storage blob directory upload -c my-file-system --account-name mystorageaccount -s "C:\mylocaldirectory\upload.txt" -d my-directory
```

W tym przykładzie przekazuje cały katalog.

```azurecli
az storage blob directory upload -c my-file-system --account-name mystorageaccount -s "C:\mylocaldirectory\" -d my-directory --recursive 
```

## <a name="show-file-properties"></a>Pokaż właściwości pliku

Właściwości pliku można wydrukować na konsoli za `az storage blob show` pomocą polecenia.

```azurecli
az storage blob show -c my-file-system -b my-file.txt --account-name mystorageaccount
```

## <a name="rename-or-move-a-file"></a>Zmienianie nazwy lub przenoszenie pliku

Zmień nazwę lub przenieś plik `az storage blob move` za pomocą polecenia.

W tym przykładzie zmienia `my-file.txt` nazwę pliku `my-file-renamed.txt`z nazwy na nazwę .

```azurecli
az storage blob move -c my-file-system -d my-file-renamed.txt -s my-file.txt --account-name mystorageaccount
```

## <a name="delete-a-file"></a>Usuwanie pliku

Usuń plik za `az storage blob delete` pomocą polecenia.

W tym przykładzie usuwa plik o nazwie`my-file.txt`

```azurecli
az storage blob delete -c my-file-system -b my-file.txt --account-name mystorageaccount 
```

## <a name="manage-permissions"></a>Zarządzaj uprawnieniami

Można uzyskać, ustawić i zaktualizować uprawnienia dostępu do katalogów i plików.

> [!NOTE]
> Jeśli do autoryzowania poleceń używasz usługi Azure Active Directory (Azure AD), upewnij się, że podmiot zabezpieczeń został przypisany [do roli Właściciel danych obiektu Blob magazynu.](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner) Aby dowiedzieć się więcej o sposobie stosowania uprawnień listy ACL i skutkach ich zmiany, zobacz [Kontrola dostępu w usłudze Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

### <a name="get-directory-and-file-permissions"></a>Uzyskaj uprawnienia do katalogów i plików

Pobierz ACL **katalogu** za pomocą `az storage blob directory access show` polecenia.

W tym przykładzie pobiera listy ACL katalogu, a następnie drukuje listy ACL do konsoli.

```azurecli
az storage blob directory access show -d my-directory -c my-file-system --account-name mystorageaccount
```

Uzyskaj uprawnienia dostępu do **pliku** za `az storage blob access show` pomocą polecenia. 

W tym przykładzie pobiera ACL pliku, a następnie drukuje ACL do konsoli.

```azurecli
az storage blob access show -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```

Na poniższej ilustracji przedstawiono dane wyjściowe po uzyskaniu listy ACL katalogu.

![Uzyskaj wyjście ACL](./media/data-lake-storage-directory-file-acl-cli/get-acl.png)

W tym przykładzie użytkownik posiadający uprawnienia do odczytu, zapisu i wykonywania. Grupa właścicielka ma tylko uprawnienia do odczytu i wykonywania. Aby uzyskać więcej informacji na temat list kontroli dostępu, zobacz [Kontrola dostępu w usłudze Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="set-directory-and-file-permissions"></a>Ustawianie uprawnień do katalogów i plików

Użyj `az storage blob directory access set` polecenia, aby ustawić ACL **katalogu**. 

W tym przykładzie ustawia listy ACL w katalogu dla użytkownika posiadającego, grupy właścicielowej lub innych użytkowników, a następnie drukuje listy ACL do konsoli.

```azurecli
az storage blob directory access set -a "user::rw-,group::rw-,other::-wx" -d my-directory -c my-file-system --account-name mystorageaccount
```

W tym przykładzie ustawia *domyślną* listy ACL w katalogu dla użytkownika posiadającego, grupę posiadającą lub innych użytkowników, a następnie drukuje listy ACL na konsoli.

```azurecli
az storage blob directory access set -a "default:user::rw-,group::rw-,other::-wx" -d my-directory -c my-file-system --account-name mystorageaccount
```

Użyj `az storage blob access set` polecenia, aby ustawić acl **pliku**. 

W tym przykładzie ustawia ACL w pliku dla użytkownika posiadającego, grupy lub innych użytkowników, a następnie drukuje listy ACL do konsoli.

```azurecli
az storage blob access set -a "user::rw-,group::rw-,other::-wx" -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```
Na poniższej ilustracji przedstawiono dane wyjściowe po ustawieniu listy ACL pliku.

![Uzyskaj wyjście ACL](./media/data-lake-storage-directory-file-acl-cli/set-acl-file.png)

W tym przykładzie użytkownik-właściciel i grupa posiadająca mają tylko uprawnienia do odczytu i zapisu. Wszyscy inni użytkownicy mają uprawnienia do zapisu i wykonywania. Aby uzyskać więcej informacji na temat list kontroli dostępu, zobacz [Kontrola dostępu w usłudze Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="update-directory-and-file-permissions"></a>Aktualizowanie uprawnień do katalogu i plików

Innym sposobem ustawienia tego uprawnienia `az storage blob directory access update` jest `az storage blob access update` użycie polecenia lub polecenia. 

Zaktualizuj acl katalogu lub `-permissions` pliku, ustawiając parametr na krótki formularz listy ACL.

W tym przykładzie zaktualizowano listy ACL **katalogu**.

```azurecli
az storage blob directory access update --permissions "rwxrwxrwx" -d my-directory -c my-file-system --account-name mystorageaccount
```

W tym przykładzie zaktualizowano listy ACL **pliku**.

```azurecli
az storage blob access update --permissions "rwxrwxrwx" -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```

Użytkownik i grupa posiadanych katalogów lub plików można również `--owner` `group` zaktualizować, ustawiając parametry na identyfikator jednostki lub nazwę głównej użytkownika (UPN) użytkownika. 

W tym przykładzie zmienia właściciela katalogu. 

```azurecli
az storage blob directory access update --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -d my-directory -c my-file-system --account-name mystorageaccount
```

W tym przykładzie zmienia właściciela pliku. 

```azurecli
az storage blob access update --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```
## <a name="manage-user-defined-metadata"></a>Zarządzanie metadanymi zdefiniowanymi przez użytkownika

Metadane zdefiniowane przez użytkownika można dodać do `az storage blob directory metadata update` pliku lub katalogu za pomocą polecenia z co najmniej jedną parą nazwa-wartość.

W tym przykładzie dodano metadane `my-directory` zdefiniowane przez użytkownika dla katalogu o nazwie katalog.

```azurecli
az storage blob directory metadata update --metadata tag1=value1 tag2=value2 -c my-file-system -d my-directory --account-name mystorageaccount
```

W tym przykładzie przedstawiono wszystkie metadane zdefiniowane przez użytkownika dla katalogu o nazwie `my-directory`.

```azurecli
az storage blob directory metadata show -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="see-also"></a>Zobacz też

* [Przykładowe](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview)
* [Mapowanie gen1 do gen2](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#mapping-from-adls-gen1-to-adls-gen2)
* [Przesyłanie opinii](https://github.com/Azure/azure-cli-extensions/issues)
* [Znane problemy](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Kod źródłowy](https://github.com/Azure/azure-cli-extensions/tree/master/src)

