---
title: Kopiowanie lub przenoszenie danych do usługi Azure Storage za pomocą narzędzia AzCopy v10 | Dokumentacja firmy Microsoft
description: AzCopy to narzędzie wiersza polecenia, który służy do kopiowania danych do z lub między kontami magazynu. Ten artykuł pomoże Ci pobrać narzędzia AzCopy, nawiąż połączenie z kontem magazynu i następnie transferu plików.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 722097f1a61a10cd45c0c330e998021cd1abf0c8
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147967"
---
# <a name="get-started-with-azcopy"></a>Wprowadzenie do narzędzia AzCopy

AzCopy to narzędzie wiersza polecenia używanej do kopiowania obiektów blob lub plików, do lub z konta magazynu. Ten artykuł pomoże Ci pobrać narzędzia AzCopy, nawiąż połączenie z kontem magazynu i następnie transferu plików.

> [!NOTE]
> Narzędzie AzCopy **V10** jest obecnie obsługiwana wersja programu AzCopy.
>
> Jeśli musisz użyć narzędzia AzCopy **v8.1**, zobacz [używać poprzedniej wersji programu AzCopy](#previous-version) dalszej części tego artykułu.

<a id="download-and-install-azcopy" />

## <a name="download-azcopy"></a>Pobierz narzędzia AzCopy

Najpierw Pobierz plik wykonywalny V10 narzędzia AzCopy do dowolnego katalogu na komputerze. 

- [Windows](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Linux](https://aka.ms/downloadazcopy-v10-linux) (docelowy)
- [System MacOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

> [!NOTE]
> Jeśli chcesz skopiować dane do i z usługi [usługi Azure Table storage](https://docs.microsoft.com/azure/storage/tables/table-storage-overview) usługi, a następnie zainstaluj [narzędzia AzCopy w wersji 7.3](https://aka.ms/downloadazcopynet).

## <a name="run-azcopy"></a>Uruchom narzędzie AzCopy

Dla wygody należy rozważyć dodanie lokalizację katalogu pliku wykonywalnego narzędzia AzCopy do ścieżki systemowej w celu ułatwienia. W ten sposób można wpisać `azcopy` z dowolnego katalogu, w tym systemie.

Jeśli nie chcesz dodać katalog narzędzia AzCopy do ścieżki, musisz Zmień katalogi na lokalizację pliku wykonywalnego narzędzia AzCopy i typ `azcopy` lub `.\azcopy` w wierszy polecenia programu Windows PowerShell.

Aby wyświetlić listę poleceń, wpisz `azcopy -h` i naciśnij klawisz ENTER.

Aby uzyskać informacje dotyczące określonego polecenia, po prostu Dołącz nazwę polecenia (na przykład: `azcopy list -h`).

![Wbudowanej pomocy](media/storage-use-azcopy-v10/azcopy-inline-help.png)

> [!NOTE] 
> Jako właściciel konta usługi Azure Storage nie są automatycznie przypisywane uprawnienia dostępu do danych. Przed wykonaniem jakichkolwiek, które są istotne, za pomocą narzędzia AzCopy, musisz zdecydować, jak zapewnisz poświadczenia autoryzacji do usługi storage. 

## <a name="choose-how-youll-provide-authorization-credentials"></a>Wybierz, jak zapewnisz poświadczenia autoryzacji

Możesz podać poświadczenia autoryzacji za pomocą usługi Azure Active Directory (AD) lub przy użyciu tokenu sygnatury dostępu współdzielonego (SAS).

Użyj tej tabeli jako przewodnika:

| Typ magazynu | Aktualnie obsługiwana metoda autoryzacji |
|--|--|
|**Blob Storage** | Azure AD & SAS |
|**Magazyn obiektów blob (hierarchicznych przestrzeni nazw)** | Tylko usługi Azure AD |
|**Usługa File storage** | Tylko sygnatury dostępu Współdzielonego |

### <a name="option-1-use-azure-ad"></a>Opcja 1: Używaj usługi Azure AD

Poziom autoryzacji, które należy zależy od tego, czy zamierzasz przekazać pliki lub po prostu pobrać je.

Jeśli chcesz pobrać pliki, następnie sprawdź, czy [czytnik danych obiektu Blob magazynu](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader) przypisane do tożsamości.

Jeśli chcesz przekazać pliki, a następnie sprawdź, czy jeden z tych ról ma przypisane do tożsamości:

- [Współautor danych obiektu Blob magazynu](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor)
- [Właściciel danych obiektu Blob magazynu](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)

Te role można przypisać do tożsamości w jednym z tych zakresów:

- Kontener (systemu plików)
- Konto magazynu
- Grupa zasobów
- Subskrypcja

Aby dowiedzieć się, jak sprawdzić i przypisz role, zobacz [udzielić dostępu do obiektów blob i kolejek danych Azure przy użyciu funkcji RBAC w witrynie Azure portal](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

Nie musisz mieć jedną z tych ról, przypisane do tożsamości, jeśli swoją tożsamość zostanie dodany do listy kontroli dostępu (ACL) kontenera docelowego lub katalogu. Na liście ACL swoją tożsamość musi uprawnienie do zapisu w katalogu docelowym, a także uprawnienia do wykonywania w kontenerze i każdy katalog nadrzędny.

Aby dowiedzieć się więcej, zobacz [kontrola dostępu w usługach Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

#### <a name="authenticate-your-identity"></a>Potwierdzenia Twojej tożsamości

Po zweryfikowaniu, że tożsamości ma autoryzację na odpowiednim poziomie, otwórz wiersz polecenia, wpisz następujące polecenie i naciśnij klawisz ENTER.

```azcopy
azcopy login
```

Jeśli użytkownik należy do więcej niż jednej z organizacji, należy uwzględnić identyfikator dzierżawy organizacji, do której należy konto magazynu.

```azcopy
azcopy login --tenant-id=<tenant-id>
```

Zastąp `<tenant-id>` symbol zastępczy identyfikator dzierżawy organizacji, do której należy konto magazynu. Aby znaleźć identyfikator dzierżawy, wybierz **usługi Azure Active Directory > Właściwości > identyfikator katalogu** w witrynie Azure portal.

To polecenie zwraca kod uwierzytelniania i adres URL witryny sieci Web. Otwórz witrynę sieci Web, należy podać kod, a następnie wybierz **dalej** przycisku.

![Tworzenie kontenera](media/storage-use-azcopy-v10/azcopy-login.png)

Zostanie wyświetlone okno logowania. W tym oknie należy zalogować się do konta platformy Azure przy użyciu poświadczeń konta platformy Azure. Po pomyślnym zalogowaniu, możesz zamknąć okno przeglądarki i rozpocząć korzystanie z narzędzia AzCopy.

### <a name="option-2-use-a-sas-token"></a>Opcja 2: Użyj tokenu sygnatury dostępu Współdzielonego

Token sygnatury dostępu Współdzielonego można dołączyć do każdego źródłowego lub docelowego adresu URL, używanego w poleceniach programu AzCopy.

Ten przykład polecenia rekursywnie kopiuje dane z katalogu lokalnego do kontenera obiektów blob. Fikcyjne tokenu sygnatury dostępu Współdzielonego jest dołączany na końcu adresu URL kontenera.

```azcopy
azcopy cp "C:\local\path" "https://account.blob.core.windows.net/mycontainer1/?sv=2018-03-28&ss=bjqt&srt=sco&sp=rwddgcup&se=2019-05-01T05:01:17Z&st=2019-04-30T21:01:17Z&spr=https&sig=MGCXiyEzbtttkr3ewJIh2AR8KrghSy1DGM9ovN734bQF4%3D" --recursive=true
```

Aby dowiedzieć się więcej na temat tokeny sygnatur dostępu Współdzielonego i jak uzyskać klucz, zobacz [Using shared access signatures (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1).

## <a name="transfer-files"></a>Transfer plików

Po uwierzytelniono Twoją tożsamość lub uzyskać tokenu sygnatury dostępu Współdzielonego, można rozpocząć transferu plików.

Aby uzyskać przykładowe polecenia, zobacz dowolnego z poniższych artykułów.

- [Transferowanie danych za pomocą narzędzia AzCopy i blob storage](storage-use-azcopy-blobs.md)

- [Transferowanie danych za pomocą narzędzia AzCopy oraz plików magazynu](storage-use-azcopy-files.md)

- [Transferowanie danych za pomocą narzędzia AzCopy i Amazon S3 przedziałów](storage-use-azcopy-s3.md)

## <a name="use-azcopy-in-a-script"></a>Narzędzia AzCopy można użyć w skrypcie

Wraz z upływem czasu, narzędzia AzCopy [link pobierania](#download-and-install-azcopy) wskaże nowe wersje narzędzia AzCopy. Jeśli skrypt pobiera narzędzia AzCopy, skrypt może przestać działać nowszą wersję programu AzCopy modyfikuje właściwości, które skryptu są zależne od pracy. 

Aby uniknąć tych problemów, należy uzyskać dołączanej (bez zmieniania) na bieżącą wersję narzędzia AzCopy. W ten sposób skrypt pobierze dokładnie tę samą wersję programu AzCopy za każdym razem, działa.

Aby uzyskać link, uruchom następujące polecenie:

| System operacyjny  | Polecenie |
|--------|-----------|
| **Linux** | `curl -v https://aka.ms/downloadazcopy-v10-linux` |
| **Windows** | `(curl https://aka.ms/downloadazcopy-v10-windows -MaximumRedirection 0 -ErrorAction silentlycontinue).RawContent` |

> [!NOTE]
> W przypadku systemu Linux `--strip-components=1` na `tar` polecenie usuwa folder najwyższego poziomu, który zawiera nazwy wersji, a zamiast tego wyodrębnia dane binarne bezpośrednio do bieżącego folderu. Umożliwia to skrypt, aby zostać zaktualizowane przy użyciu nowej wersji `azcopy` tylko aktualizując `wget` adresu URL.

Adres URL jest wyświetlany w danych wyjściowych tego polecenia. Skrypt następnie pobrać narzędzia AzCopy przy użyciu tego adresu URL.

| System operacyjny  | Polecenie |
|--------|-----------|
| **Linux** | `wget -O azcopyv10.tar https://azcopyvnext.azureedge.net/release20190301/azcopy_linux_amd64_10.0.8.tar.gz tar -xf azcopyv10.tar --strip-components=1 ./azcopy` |
| **Windows** | `Invoke-WebRequest https://azcopyvnext.azureedge.net/release20190517/azcopy_windows_amd64_10.1.2.zip -OutFile azcopyv10.zip <<Unzip here>>` |

## <a name="use-azcopy-in-storage-explorer"></a>Korzystanie z narzędzia AzCopy w Eksploratorze usługi Storage

Jeśli chcesz korzystać z korzyści związanych z wydajnością, narzędzia azcopy, ale wolisz użyć Eksploratora usługi Storage, a nie w wierszu polecenia do interakcji z plikami, Włącz narzędzie AzCopy w Eksploratorze usługi Storage. 

W Eksploratorze usługi Storage wybierz **Podgląd**->**Użyj narzędzia AzCopy, ulepszone przekazywanie obiektu Blob i pobieranie**.

![Włącz narzędzia AzCopy jako aparat transferu w Eksploratorze usługi Azure Storage](media/storage-use-azcopy-v10/enable-azcopy-storage-explorer.jpg)

> [!NOTE]
> Nie masz włączyć to ustawienie, jeśli włączono hierarchicznej przestrzeni nazw w ramach konta magazynu. To, ponieważ Eksplorator usługi Storage automatycznie korzysta z narzędzia AzCopy dla kont magazynu, które mają hierarchicznej przestrzeni nazw.  

Eksplorator usługi Storage używa Twój klucz konta do wykonywania operacji, więc po zalogowaniu się do programu Storage Explorer, nie musisz podać poświadczenia autoryzacji dodatkowe.

<a id="previous-version" />

## <a name="use-the-previous-version-of-azcopy"></a>Korzystanie z poprzedniej wersji programu AzCopy

Jeśli potrzebujesz poprzedniej wersji programu AzCopy (AzCopy v8.1), zobacz jedną z następujących linków:

- [Narzędzie AzCopy w Windows (v8)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy)

- [Narzędzie AzCopy w systemie Linux (v8)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy-linux)

## <a name="configure-optimize-and-troubleshoot-azcopy"></a>Konfigurowanie optymalizacji i rozwiązywanie problemów z narzędzia AzCopy

Zobacz [Konfiguruj, optymalizowanie i rozwiązywanie problemów z narzędzia AzCopy](storage-use-azcopy-configure.md)

## <a name="next-steps"></a>Kolejne kroki

Jeśli masz pytania, problemów lub ogólne opinie, zgłoś je [w serwisie GitHub](https://github.com/Azure/azure-storage-azcopy) strony.
