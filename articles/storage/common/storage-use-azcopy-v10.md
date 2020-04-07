---
title: Kopiowanie lub przenoszenie danych do usługi Azure Storage przy użyciu programu AzCopy w wersji 10 | Dokumenty firmy Microsoft
description: AzCopy to narzędzie wiersza polecenia, za pomocą którego można kopiować dane do, z lub między kontami magazynu. Ten artykuł ułatwia pobieranie azcopy, łączenie się z kontem magazynu, a następnie przenoszenie plików.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 8701fe6857e95334a5e1d24bfe70feb130d5512c
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756033"
---
# <a name="get-started-with-azcopy"></a>Wprowadzenie do narzędzia AzCopy

AzCopy to narzędzie wiersza polecenia, za pomocą którego można kopiować obiekty BLOB lub pliki do lub z konta magazynu. Ten artykuł ułatwia pobieranie azcopy, łączenie się z kontem magazynu, a następnie przenoszenie plików.

> [!NOTE]
> AzCopy **V10** jest obecnie obsługiwaną wersją AzCopy.
>
> Jeśli chcesz użyć poprzedniej wersji programu AzCopy, zobacz sekcję [Użyj poprzedniej wersji programu AzCopy](#previous-version) w tym artykule.

<a id="download-and-install-azcopy" />

## <a name="download-azcopy"></a>Pobierz AzCopy

Najpierw pobierz plik wykonywalny AzCopy V10 do dowolnego katalogu na komputerze. AzCopy V10 to tylko plik wykonywalny, więc nie ma nic do zainstalowania.

- [Windows 64-bitowy](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Windows 32-bitowy](https://aka.ms/downloadazcopy-v10-windows-32bit) (zip)
- [Linux](https://aka.ms/downloadazcopy-v10-linux) (smoła)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

Pliki te są kompresowane jako plik zip (Windows i Mac) lub plik tar (Linux). Aby pobrać i rozpakować plik tar w systemie Linux, zapoznaj się z dokumentacją dystrybucji linuksa.

> [!NOTE]
> Jeśli chcesz skopiować dane do i z usługi [magazynu tabel platformy Azure,](https://docs.microsoft.com/azure/storage/tables/table-storage-overview) zainstaluj [AzCopy w wersji 7.3](https://aka.ms/downloadazcopynet).


## <a name="run-azcopy"></a>Uruchom AzCopy

Dla wygody należy rozważyć dodanie lokalizacji katalogu pliku wykonywalnego AzCopy do ścieżki systemowej w celu ułatwienia użycia. W ten sposób `azcopy` można wpisać z dowolnego katalogu w systemie.

Jeśli nie chcesz dodawać katalogu AzCopy do ścieżki, musisz zmienić katalogi na lokalizację pliku wykonywalnego AzCopy i wpisać `azcopy` lub `.\azcopy` w monitach poleceń programu Windows PowerShell.

Aby wyświetlić listę poleceń, wpisz, `azcopy -h` a następnie naciśnij klawisz ENTER.

Aby dowiedzieć się więcej o konkretnym poleceniu, `azcopy list -h`wystarczy podać nazwę polecenia (na przykład: ).

![Pomoc inline](media/storage-use-azcopy-v10/azcopy-inline-help.png)

Aby znaleźć szczegółową dokumentację referencyjną dla każdego parametru polecenia i polecenia, zobacz [azcopy](storage-ref-azcopy.md)

> [!NOTE] 
> Jako właściciel konta usługi Azure Storage nie są automatycznie przypisywane uprawnienia dostępu do danych. Zanim będzie można zrobić coś znaczącego z AzCopy, należy zdecydować, w jaki sposób będzie dostarczać poświadczenia autoryzacji do usługi magazynu. 

## <a name="choose-how-youll-provide-authorization-credentials"></a>Wybieranie sposobu podawania poświadczeń autoryzacji

Poświadczenia autoryzacji można podać przy użyciu usługi Azure Active Directory (AD) lub przy użyciu tokenu sygnatury dostępu współdzielonego (SAS).

Użyj tej tabeli jako przewodnika:

| Typ magazynu | Obecnie obsługiwana metoda autoryzacji |
|--|--|
|**Blob Storage** | Usługa Azure AD & sygnatury dostępu Współdzielonego usługi Azure |
|**Magazyn obiektów blob (hierarchiczna przestrzeń nazw)** | Usługa Azure AD & sygnatury dostępu Współdzielonego usługi Azure |
|**Przechowywanie plików** | Tylko SAS |

### <a name="option-1-use-azure-active-directory"></a>Opcja 1: Korzystanie z usługi Azure Active Directory

Za pomocą usługi Azure Active Directory, można podać poświadczenia raz zamiast dołączania tokenu sygnatury dostępu Współdzielonego do każdego polecenia.  

> [!NOTE]
> W bieżącej wersji, jeśli planujesz skopiować obiekty BLOC między kontami magazynu, musisz dołączyć token sygnatury dostępu Współdzielonego do każdego źródłowego adresu URL. Token sygnatury dostępu Współdzielonego można pominąć tylko z docelowego adresu URL. Przykłady można znaleźć [w obiekcie Kopiowanie obiektów blob między kontami magazynu](storage-use-azcopy-blobs.md).

Potrzebny poziom autoryzacji zależy od tego, czy zamierzasz przesyłać pliki, czy po prostu je pobierać.

Jeśli chcesz tylko pobrać pliki, sprawdź, czy [czytnik danych obiektów blob magazynu](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader) został przypisany do tożsamości użytkownika, tożsamości zarządzanej lub jednostki usługi.

> Tożsamości użytkowników, tożsamości zarządzane i jednostki usługi są każdy typ *jednostki zabezpieczeń,* więc użyjemy terminu *zabezpieczeń zabezpieczeń w* pozostałej części tego artykułu.

Jeśli chcesz przekazać pliki, sprawdź, czy jedna z tych ról została przypisana do podmiotu zabezpieczeń:

- [Współautor danych obiektów blob magazynu](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor)
- [Właściciel danych obiektu blob magazynu](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)

Te role można przypisać do podmiotu zabezpieczeń w dowolnym z tych zakresów:

- Kontener (system plików)
- Konto magazynu
- Grupa zasobów
- Subskrypcja

Aby dowiedzieć się, jak weryfikować i przypisywać role, zobacz [Udzielanie dostępu do obiektów blob platformy Azure i danych kolejek za pomocą funkcji RBAC w witrynie Azure portal](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

> [!NOTE]
> Należy pamiętać, że przypisania roli RBAC może potrwać do pięciu minut do propagacji.

Nie trzeba mieć jedną z tych ról przypisanych do podmiotu zabezpieczeń, jeśli podmiot zabezpieczeń jest dodawany do listy kontroli dostępu (ACL) kontenera docelowego lub katalogu. W acl, podmiot zabezpieczeń wymaga uprawnienia do zapisu w katalogu docelowym i wykonać uprawnienia do kontenera i każdego katalogu nadrzędnego.

Aby dowiedzieć się więcej, zobacz [Kontrola dostępu w usłudze Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

#### <a name="authenticate-a-user-identity"></a>Uwierzytelnij tożsamość użytkownika

Po sprawdzeniu, czy tożsamość użytkownika została podana na wymaganym poziomie autoryzacji, otwórz wiersz polecenia, wpisz następujące polecenie, a następnie naciśnij klawisz ENTER.

```azcopy
azcopy login
```

Jeśli należysz do więcej niż jednej organizacji, dołącz identyfikator dzierżawy organizacji, do której należy konto magazynu.

```azcopy
azcopy login --tenant-id=<tenant-id>
```

Zastąp `<tenant-id>` symbol zastępczy identyfikatorem dzierżawy organizacji, do której należy konto magazynu. Aby znaleźć identyfikator dzierżawy, wybierz pozycję **Azure Active Directory > Properties > Directory ID** w witrynie Azure portal.

To polecenie zwraca kod uwierzytelniania i adres URL witryny sieci Web. Otwórz witrynę sieci Web, podaj kod, a następnie wybierz przycisk **Dalej.**

![Tworzenie kontenera](media/storage-use-azcopy-v10/azcopy-login.png)

Pojawi się okno logowania. W tym oknie zaloguj się do konta platformy Azure przy użyciu poświadczeń konta platformy Azure. Po pomyślnym zalogowaniu można zamknąć okno przeglądarki i rozpocząć korzystanie z programu AzCopy.

<a id="service-principal" />

#### <a name="authenticate-a-service-principal"></a>Uwierzytelnij jednostkę usługi

Jest to świetna opcja, jeśli planujesz używać AzCopy wewnątrz skryptu, który działa bez interakcji z użytkownikiem, szczególnie podczas uruchamiania lokalnie. Jeśli planujesz uruchomić AzCopy na maszynach wirtualnych, które są uruchamiane na platformie Azure, tożsamość usługi zarządzanej jest łatwiejsze do administrowania. Aby dowiedzieć się więcej, zobacz [sekcję Uwierzytelnij tożsamość zarządzaną](#managed-identity) w tym artykule.

Przed uruchomieniem skryptu, należy zalogować się interaktywnie co najmniej jeden raz, dzięki czemu można podać AzCopy z poświadczenia jednostki usługi.  Te poświadczenia są przechowywane w zabezpieczonym i zaszyfrowanym pliku, dzięki czemu skrypt nie musi dostarczać tych poufnych informacji.

Możesz zalogować się na swoje konto przy użyciu klucza tajnego klienta lub przy użyciu hasła certyfikatu skojarzonego z rejestracją aplikacji jednostki usługi.

Aby dowiedzieć się więcej na temat tworzenia jednostki usługi, zobacz [Jak: Użyj portalu, aby utworzyć aplikację i jednostkę usługi Azure AD, która może uzyskiwać dostęp do zasobów.](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)

Aby dowiedzieć się więcej o jednostkach usługi w ogóle, zobacz [Obiekty głównej aplikacji i usługi w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)

##### <a name="using-a-client-secret"></a>Korzystanie z klucza tajnego klienta

Zacznij od `AZCOPY_SPA_CLIENT_SECRET` ustawienia zmiennej środowiskowej do klucza tajnego klienta rejestracji aplikacji jednostki usługi.

> [!NOTE]
> Upewnij się, że ta wartość jest ustawiana w wierszu polecenia, a nie w ustawieniach zmiennych środowiskowych systemu operacyjnego. W ten sposób wartość jest dostępna tylko dla bieżącej sesji.

W tym przykładzie pokazano, jak można to zrobić w programie PowerShell.

```azcopy
$env:AZCOPY_SPA_CLIENT_SECRET="$(Read-Host -prompt "Enter key")"
```

> [!NOTE]
> Należy rozważyć użycie monitu, jak pokazano w tym przykładzie. W ten sposób hasło nie pojawi się w historii poleceń konsoli.  

Następnie wpisz następujące polecenie, a następnie naciśnij klawisz ENTER.

```azcopy
azcopy login --service-principal --application-id <application-id> --tenant-id=<tenant-id>
```

Zastąp `<application-id>` symbol zastępczy identyfikatorem aplikacji rejestracji aplikacji jednostki usługi. Zastąp `<tenant-id>` symbol zastępczy identyfikatorem dzierżawy organizacji, do której należy konto magazynu. Aby znaleźć identyfikator dzierżawy, wybierz pozycję **Azure Active Directory > Properties > Directory ID** w witrynie Azure portal. 

##### <a name="using-a-certificate"></a>Korzystanie z certyfikatu

Jeśli wolisz używać własnych poświadczeń do autoryzacji, możesz przekazać certyfikat do rejestracji aplikacji, a następnie użyć tego certyfikatu do logowania.

Oprócz przekazywania certyfikatu do rejestracji aplikacji, musisz również mieć kopię certyfikatu zapisanego na komputerze lub maszynie wirtualnej, na której będzie uruchomiona aplikacja AzCopy. Ta kopia certyfikatu powinna znajdować się w pliku . PFX lub . pem i musi zawierać klucz prywatny. Klucz prywatny powinien być chroniony hasłem. Jeśli używasz systemu Windows, a certyfikat istnieje tylko w magazynie certyfikatów, należy wyeksportować ten certyfikat do pliku PFX (w tym klucza prywatnego). Aby uzyskać wskazówki, zobacz [Export-PfxCertificate](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate?view=win10-ps)

Następnie ustaw `AZCOPY_SPA_CERT_PASSWORD` zmienną środowiskową na hasło certyfikatu.

> [!NOTE]
> Upewnij się, że ta wartość jest ustawiana w wierszu polecenia, a nie w ustawieniach zmiennych środowiskowych systemu operacyjnego. W ten sposób wartość jest dostępna tylko dla bieżącej sesji.

W tym przykładzie pokazano, jak można wykonać to zadanie w programie PowerShell.

```azcopy
$env:AZCOPY_SPA_CERT_PASSWORD="$(Read-Host -prompt "Enter key")"
```

Następnie wpisz następujące polecenie, a następnie naciśnij klawisz ENTER.

```azcopy
azcopy login --service-principal --certificate-path <path-to-certificate-file> --tenant-id=<tenant-id>
```

Zastąp `<path-to-certificate-file>` symbol zastępczy względną lub w pełni kwalifikowaną ścieżką do pliku certyfikatu. AzCopy zapisuje ścieżkę do tego certyfikatu, ale nie zapisuje kopii certyfikatu, więc upewnij się, że ten certyfikat jest na swoim miejscu. Zastąp `<tenant-id>` symbol zastępczy identyfikatorem dzierżawy organizacji, do której należy konto magazynu. Aby znaleźć identyfikator dzierżawy, wybierz pozycję **Azure Active Directory > Properties > Directory ID** w witrynie Azure portal.

> [!NOTE]
> Należy rozważyć użycie monitu, jak pokazano w tym przykładzie. W ten sposób hasło nie pojawi się w historii poleceń konsoli. 

<a id="managed-identity" />

#### <a name="authenticate-a-managed-identity"></a>Uwierzytelnij tożsamość zarządzaną

Jest to świetna opcja, jeśli planujesz używać AzCopy wewnątrz skryptu, który działa bez interakcji z użytkownikiem, a skrypt jest uruchamiany z maszyny wirtualnej platformy Azure (VM). Korzystając z tej opcji, nie trzeba przechowywać żadnych poświadczeń na maszynie Wirtualnej.

Możesz zalogować się na swoje konto przy użyciu tożsamości zarządzanej w całym systemie, która została włączona na maszynie wirtualnej, lub przy użyciu identyfikatora klienta, identyfikatora obiektu lub identyfikatora zasobu tożsamości zarządzanej przypisanej przez użytkownika, która została przypisana do maszyny Wirtualnej.

Aby dowiedzieć się więcej o tym, jak włączyć tożsamość zarządzaną dla całego systemu lub utworzyć tożsamość zarządzaną przypisaną przez użytkownika, zobacz [Konfigurowanie tożsamości zarządzanych dla zasobów platformy Azure na maszynie Wirtualnej przy użyciu portalu Azure.](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm)

##### <a name="using-a-system-wide-managed-identity"></a>Korzystanie z tożsamości zarządzanej w całym systemie

Najpierw upewnij się, że włączono tożsamość zarządzaną dla całego systemu na maszynie wirtualnej. Zobacz [Tożsamość zarządzana przypisana do systemu](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity).

Następnie w konsoli poleceń wpisz następujące polecenie, a następnie naciśnij klawisz ENTER.

```azcopy
azcopy login --identity
```

##### <a name="using-a-user-assigned-managed-identity"></a>Korzystanie z tożsamości zarządzanej przypisanej przez użytkownika

Najpierw upewnij się, że włączono tożsamość zarządzaną przypisaną przez użytkownika na maszynie wirtualnej. Zobacz [Tożsamość zarządzana przypisana przez użytkownika](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#user-assigned-managed-identity).

Następnie w konsoli poleceń wpisz dowolne z następujących poleceń, a następnie naciśnij klawisz ENTER.

```azcopy
azcopy login --identity --identity-client-id "<client-id>"
```

Zastąp `<client-id>` symbol zastępczy identyfikatorem klienta tożsamości zarządzanej przypisanej przez użytkownika.

```azcopy
azcopy login --identity --identity-object-id "<object-id>"
```

Zastąp `<object-id>` symbol zastępczy identyfikatorem obiektu tożsamości zarządzanej przypisanej przez użytkownika.

```azcopy
azcopy login --identity --identity-resource-id "<resource-id>"
```

Zastąp `<resource-id>` symbol zastępczy identyfikatorem zasobu tożsamości zarządzanej przypisanej przez użytkownika.

### <a name="option-2-use-a-sas-token"></a>Opcja 2: Użyj tokenu sygnatury dostępu Współdzielonego

Token Sygnatury dostępu Współdzielonego można dołączyć do każdego źródłowego lub docelowego adresu URL, który jest używany w poleceniach AzCopy.

W tym przykładowym poleceniu cyklicznie kopiuje dane z katalogu lokalnego do kontenera obiektów blob. Fikcyjny token sygnatury dostępu Współdzielonego jest dołączany na końcu adresu URL kontenera.

```azcopy
azcopy copy "C:\local\path" "https://account.blob.core.windows.net/mycontainer1/?sv=2018-03-28&ss=bjqt&srt=sco&sp=rwddgcup&se=2019-05-01T05:01:17Z&st=2019-04-30T21:01:17Z&spr=https&sig=MGCXiyEzbtttkr3ewJIh2AR8KrghSy1DGM9ovN734bQF4%3D" --recursive=true
```

Aby dowiedzieć się więcej o tokenach sygnatury dostępu Współdzielonego i sposobie ich uzyskania, zobacz [Korzystanie z sygnatur dostępu współdzielonego (SYGNATURA DOSTĘPU WSPÓŁDZIELONEGO).](https://docs.microsoft.com/azure/storage/common/storage-sas-overview)

## <a name="transfer-files"></a>Transfer plików

Po uwierzytelnieniu tożsamości lub uzyskaniu tokenu Sygnatury dostępu Współdzielonego można rozpocząć przesyłanie plików.

Aby znaleźć przykładowe polecenia, zobacz dowolny z tych artykułów.

- [Przesyłanie danych za pomocą pamięci masowej AzCopy i obiektów blob](storage-use-azcopy-blobs.md)

- [Przesyłanie danych za pomocą AzCopy i przechowywania plików](storage-use-azcopy-files.md)

- [Przesyłanie danych za pomocą wiader AzCopy i Amazon S3](storage-use-azcopy-s3.md)

- [Przesyłanie danych za pomocą azcopy i usługi Azure Stack storage](https://docs.microsoft.com/azure-stack/user/azure-stack-storage-transfer#azcopy)

## <a name="use-azcopy-in-a-script"></a>Używanie azcopy w skrypcie

### <a name="obtain-a-static-download-link"></a>Uzyskiwanie statycznego łącza pobierania

Z biegiem [czasu,](#download-and-install-azcopy) AzCopy link do pobrania wskaże nowe wersje AzCopy. Jeśli skrypt zostanie pobrany azcopy, skrypt może przestać działać, jeśli nowsza wersja AzCopy modyfikuje funkcje, od których zależy skrypt.

Aby uniknąć tych problemów, należy uzyskać statyczne (nie zmienia) łącze do bieżącej wersji AzCopy. W ten sposób skrypt pobiera tę samą dokładną wersję AzCopy za każdym razem, gdy działa.

Aby uzyskać łącze, uruchom następujące polecenie:

| System operacyjny  | Polecenie |
|--------|-----------|
| **Linux** | `curl -v https://aka.ms/downloadazcopy-v10-linux` |
| **Windows** | `(curl https://aka.ms/downloadazcopy-v10-windows -MaximumRedirection 0 -ErrorAction silentlycontinue).RawContent` |

> [!NOTE]
> W przypadku `--strip-components=1` systemu `tar` Linux w poleceniu usuwa folder najwyższego poziomu zawierający nazwę wersji i zamiast tego wyodrębnia plik binarny bezpośrednio do bieżącego folderu. Dzięki temu skrypt może być aktualizowany `azcopy` o nową `wget` wersję tylko przez aktualizowanie adresu URL.

Adres URL pojawi się w danych wyjściowych tego polecenia. Skrypt można następnie pobrać AzCopy przy użyciu tego adresu URL.

| System operacyjny  | Polecenie |
|--------|-----------|
| **Linux** | `wget -O azcopy_v10.tar.gz https://aka.ms/downloadazcopy-v10-linux && tar -xf azcopy_v10.tar.gz --strip-components=1` |
| **Windows** | `Invoke-WebRequest https://azcopyvnext.azureedge.net/release20190517/azcopy_windows_amd64_10.1.2.zip -OutFile azcopyv10.zip <<Unzip here>>` |

### <a name="escape-special-characters-in-sas-tokens"></a>Wyuj znaki specjalne w tokenach Sygnatury dostępu Współdzielonego

W plikach wsadowych, `.cmd` które mają rozszerzenie, musisz uciec `%` znaków, które pojawiają się w tokenach sygnatury dostępu Współdzielonego. Można to zrobić, dodając `%` dodatkowy znak `%` obok istniejących znaków w ciągu tokenu sygnatury dostępu Współdzielonego.

### <a name="run-scripts-by-using-jenkins"></a>Uruchamianie skryptów przy użyciu usługi Jenkins

Jeśli planujesz używać [usługi Jenkins](https://jenkins.io/) do uruchamiania skryptów, upewnij się, że na początku skryptu należy umieścić następujące polecenie.

```
/usr/bin/keyctl new_session
```

## <a name="use-azcopy-in-azure-storage-explorer"></a>Używanie AzCopy w Eksploratorze usługi Azure Storage

[Eksplorator magazynu](https://azure.microsoft.com/features/storage-explorer/) używa AzCopy do wykonywania wszystkich operacji transferu danych. [Explorer magazynu](https://azure.microsoft.com/features/storage-explorer/) można użyć, jeśli chcesz wykorzystać zalety wydajności AzCopy, ale wolisz używać graficznego interfejsu użytkownika, a nie wiersza polecenia do interakcji z plikami.

Eksplorator magazynu używa klucza konta do wykonywania operacji, więc po zalogowaniu się do Eksploratora magazynu nie trzeba podawać dodatkowych poświadczeń autoryzacji.

<a id="previous-version" />

## <a name="use-the-previous-version-of-azcopy"></a>Użyj poprzedniej wersji programu AzCopy

Jeśli chcesz użyć poprzedniej wersji programu AzCopy, zobacz jeden z następujących łączy:

- [Narzędzie AzCopy w systemie Windows (wersja 8)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy)

- [AzCopy na Linuksie (v7)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy-linux)

## <a name="configure-optimize-and-troubleshoot-azcopy"></a>Konfigurowanie, optymalizowanie i rozwiązywanie problemów z programem AzCopy

Zobacz [Konfigurowanie, optymalizowanie i rozwiązywanie problemów z programem AzCopy](storage-use-azcopy-configure.md)

## <a name="next-steps"></a>Następne kroki

Jeśli masz pytania, problemy lub ogólne opinie, prześlij je na stronie [GitHub.](https://github.com/Azure/azure-storage-azcopy)
