---
title: Korzystanie z Eksploratora usługi Azure Storage w usłudze Azure Data Storage Gen2
description: Za pomocą Eksploratora usługi Azure Storage można zarządzać katalogami oraz listami kontroli dostępu do plików i katalogów (ACL) na kontach magazynu z włączoną hierarchiczną przestrzenią nazw (HNS).
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: fca9fa8a964c6c9d69ffbb3036bd4774e0d1bd34
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255550"
---
# <a name="use-azure-storage-explorer-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Używanie Eksploratora usługi Azure Storage do zarządzania katalogami, plikami i listami AL w usłudze Azure Data Lake Storage Gen2

W tym artykule pokazano, jak używać [Eksploratora usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/) do tworzenia katalogów, plików i uprawnień na kontach magazynu z włączoną hierarchiczną przestrzenią nazw (HNS) i zarządzanie nimi.

## <a name="prerequisites"></a>Wymagania wstępne

> [!div class="checklist"]
> * Subskrypcja platformy Azure. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Konto magazynu z włączoną hierarchiczną przestrzenią nazw (HNS). Postępuj zgodnie z [tymi](data-lake-storage-quickstart-create-account.md) instrukcjami, aby je utworzyć.
> * Eksplorator usługi Azure Storage zainstalowany na komputerze lokalnym. Aby zainstalować Eksplorator usługi Azure Storage dla systemu Windows, Macintosh lub Linux, zobacz [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/).

## <a name="sign-in-to-storage-explorer"></a>Logowanie się do Eksploratora magazynu

Przy pierwszym uruchomieniu Eksploratora usługi Storage jest wyświetlane okno **Eksplorator usługi Microsoft Azure Storage — nawiązywanie połączenia**. Chociaż Eksplorator usługi Storage oferuje kilka sposobów łączenia się z kontem magazynu, tylko jeden ze sposobów jest obecnie obsługiwany na potrzeby zarządzania listami kontroli dostępu.

|Zadanie|Przeznaczenie|
|---|---|
|Dodawanie konta platformy Azure | Przekierowuje użytkownika do strony logowania w organizacji w celu uwierzytelnienia użytkownika na platformie Azure. Obecnie jest to jedyna obsługiwana metoda uwierzytelniania, jeśli chcesz ustawić listy kontroli dostępu i zarządzać nimi.|
|Używanie parametrów połączenia lub identyfikatora URI sygnatury dostępu współdzielonego | Umożliwia bezpośredni dostęp do kontenera lub konta magazynu za pomocą tokenu sygnatury dostępu współdzielonego lub udostępnionych parametrów połączenia. |
|Używanie nazwy i klucza konta magazynu| Użyj nazwy i klucza konta magazynu do nawiązania połączenia z magazynem Azure Storage.|

Wybierz **pozycję Dodaj konto platformy Azure** i kliknij pozycję Zaloguj **się..**. Postępuj zgodnie z monitami wyświetlanymi na ekranie, aby zalogować się na swoje konto platformy Azure.

![Okno Eksplorator usługi Microsoft Azure Storage — nawiązywanie połączenia](media/storage-quickstart-blobs-storage-explorer/connect.png)

Po nawiązaniu połączenia Eksplorator usługi Azure Storage zostanie załadowany z wyświetloną kartą **Eksplorator**. Ten widok udostępnia szczegółowe informacje dotyczące wszystkich kont usługi Azure Storage, a także magazynu lokalnego skonfigurowanego za pomocą kont [emulatora magazynu Azure](../common/storage-use-emulator.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) lub bazy danych [Cosmos DB](../../cosmos-db/storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) bądź środowisk usługi [Azure Stack](/azure-stack/user/azure-stack-storage-connect-se?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

![Okno Eksplorator usługi Microsoft Azure Storage — nawiązywanie połączenia](media/storage-quickstart-blobs-storage-explorer/mainpage.png)

## <a name="create-a-container"></a>Tworzenie kontenera

Kontener przechowuje katalogi i pliki. Aby go utworzyć, rozwiń konto magazynu utworzone w kroku postępowania. Wybierz pozycję **Kontenery obiektów blob**, rozwiń ją, a następnie wybierz pozycję **Utwórz kontener obiektów blob**. Wprowadź nazwę kontenera. Zobacz [Tworzenie kontenera](storage-quickstart-blobs-dotnet.md#create-a-container) sekcji listy reguł i ograniczeń dotyczących nazewnictwa kontenerów. Po zakończeniu naciśnij klawisz **Enter,** aby utworzyć kontener. Po pomyślnym utworzeniu kontenera jest on wyświetlany w folderze **Kontenery obiektów blob** dla wybranego konta magazynu.

![Eksplorator magazynu platformy Microsoft Azure — tworzenie kontenera](media/data-lake-storage-explorer/creating-a-filesystem.png)

## <a name="create-a-directory"></a>Tworzenie katalogu

Aby utworzyć katalog, wybierz kontener utworzony w kroku postępowania. Na wstążce kontenera wybierz przycisk **Nowy folder.** Wprowadź nazwę katalogu. Po zakończeniu naciśnij klawisz **Enter,** aby utworzyć katalog. Po pomyślnym utworzeniu katalogu pojawia się on w oknie edytora.

![Eksplorator magazynu platformy Microsoft Azure — tworzenie katalogu](media/data-lake-storage-explorer/creating-a-directory.png)

## <a name="upload-blobs-to-the-directory"></a>Przekazywanie obiektów blob do katalogu

Na wstążce katalogu wybierz przycisk **Przekaż.** Ta operacja udostępnia opcję przekazania folderu lub pliku.

Wybierz pliki lub folder do przekazania.

![Eksplorator usługi Microsoft Azure Storage — przekazywanie obiektu blob](media/data-lake-storage-explorer/upload-file.png)

Po wybraniu przycisku **OK** zaznaczone pliki zostaną umieszczone w kolejce do przekazania, a następnie przekazane. Po zakończeniu przekazywania wyniki zostaną wyświetlone w oknie **Działania**.

## <a name="view-blobs-in-a-directory"></a>Wyświetlanie obiektów blob w katalogu

W aplikacji **Eksplorator usługi Azure Storage** wybierz katalog w ramach konta magazynu. W okienku głównym wyświetlana jest lista obiektów blob w wybranym katalogu.

![Eksplorator usługi Microsoft Azure Storage — wyświetlanie listy obiektów blob w katalogu](media/data-lake-storage-explorer/list-files.png)

## <a name="download-blobs"></a>Pobieranie obiektów blob

Aby pobrać pliki przy użyciu **Eksploratora usługi Azure Storage**z wybranym plikiem, wybierz **pozycję Pobierz** ze wstążki. Zostanie wyświetlone okno dialogowe, w którym możliwe jest wprowadzenie nazwy pliku. Wybierz **pozycję Zapisz,** aby rozpocząć pobieranie pliku do lokalizacji lokalnej.

## <a name="managing-access"></a>Zarządzanie dostępem

Uprawnienia można ustawić w katalogu głównym kontenera. Aby to zrobić, musisz być zalogowany do Usługi Azure Storage Explorer z indywidualnym kontem z uprawnieniami do tego (w przeciwieństwie do ciągu połączenia). Kliknij prawym przyciskiem myszy kontener i wybierz polecenie **Zarządzaj uprawnieniami,** powołując okno dialogowe **Zarządzanie uprawnieniami.**

![Eksplorator usługi Microsoft Azure Storage — zarządzanie dostępem do katalogów](media/storage-quickstart-blobs-storage-Explorer/manageperms.png)

Okno dialogowe **Zarządzanie uprawnieniami** służy do zarządzania uprawnieniami właściciela i grupy właściciela. Umożliwia również dodawanie do listy kontroli dostępu nowych użytkowników i grup, dla których można następnie zarządzać uprawnieniami.

Aby dodać do listy kontroli dostępu nowego użytkownika lub nową grupę, wybierz pole **Dodaj użytkownika lub grupę**.

Wprowadź odpowiedni wpis usługi Azure Active Directory (AAD), który chcesz dodać do listy, a następnie wybierz pozycję **Dodaj**.

Użytkownika lub grupę będzie teraz widać w polu **Użytkownicy i grupy:**, co umożliwi rozpoczęcie zarządzania ich uprawnieniami.

> [!NOTE]
> Najlepsze rozwiązanie i zalecenie jest takie, aby utworzyć grupę zabezpieczeń w usłudze AAD i obsługiwać uprawnienia dla grupy, a nie dla poszczególnych użytkowników. Szczegółowe informacje na temat tego zalecenia oraz inne najlepsze rozwiązania można znaleźć w [najlepszych rozwiązaniach dotyczących usługi Data Lake Storage Gen2](data-lake-storage-best-practices.md).

Istnieją dwie kategorie uprawnień, które można przypisać: listy ACL dostępu i domyślne listy ACL.

* **Dostęp**: Listy dostępu do list kontroli dostępu do obiektu. Pliki i katalogi mają osobne listy ACL dostępu.

* **Domyślnie**: Szablon list ACL skojarzonych z katalogiem określający listy ACL dostępu dla wszystkich elementów podrzędnych utworzonych w tym katalogu. Pliki nie mają domyślnych list ACL.

W obu tych kategoriach istnieją trzy uprawnienia, które można następnie przypisać do plików lub katalogów: **Odczyt**, **Zapis**i **Wykonaj**.

>[!NOTE]
> Dokonanie wyboru w tym miejscu nie spowoduje ustawienie uprawnień dla wszystkich elementów znajdujących się obecnie w katalogu. Jeśli dany plik już istnieje, należy przejść do poszczególnych elementów i ustawić uprawnienia ręcznie.

Można zarządzać uprawnieniami dla poszczególnych katalogów, a także dla pojedynczych plików, co umożliwia szczegółową kontrolę dostępu. Proces zarządzania uprawnieniami dla plików i katalogów jest taki sam, jak opisano powyżej. Kliknij prawym przyciskiem myszy plik lub katalog, dla którego chcesz zarządzać uprawnieniami, i wykonaj ten sam proces.

## <a name="next-steps"></a>Następne kroki

Poznaj listy kontroli dostępu w programie Data Lake Storage Gen2.

> [!div class="nextstepaction"]
> [Kontrola dostępu w usłudze Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)
