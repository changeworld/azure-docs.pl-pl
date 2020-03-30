---
title: Informacje o wersji Eksploratora usługi Microsoft Azure Storage
description: Informacje o wersji dla Eksploratora magazynu platformy Microsoft Azure
services: storage
documentationcenter: na
author: cawaMS
manager: paulyuk
editor: ''
ms.assetid: ''
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/12/2018
ms.author: cawa
ms.openlocfilehash: 986da8980a569583ef454833957ace85dd1bfbb6
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351059"
---
# <a name="microsoft-azure-storage-explorer-release-notes"></a>Informacje o wersji Eksploratora usługi Microsoft Azure Storage

Ten artykuł zawiera najnowsze informacje o wersji dla Eksploratora usługi Azure Storage, a także informacje o wersji dla poprzednich wersji. 

[Microsoft Azure Storage Explorer](./vs-azure-tools-storage-manage-with-storage-explorer.md) to autonomiczna aplikacja, która umożliwia łatwą pracę z danymi usługi Azure Storage w systemach Windows, macOS i Linux.

Aby pobrać poprzednie wersje Eksploratora magazynu, możesz odwiedzić [stronę Wersje](https://github.com/microsoft/AzureStorageExplorer/releases) naszego repozytorium GitHub.

## <a name="version-1110"></a>Wersja 1.11.0
11/4/2019

### <a name="new"></a>Nowa
* Operacje dla obiektów blob, ADLS Gen2 i dysków zarządzanych używają zintegrowanego AzCopy. W szczególności następujące operacje są wykonywane przy użyciu AzCopy:
   * Obiekty blob
      * Otwórz do edycji + Prześlij
      * Prześlij, łącznie z przeciągania & upuszczania
      * Pobierz
      * Kopiowanie & #1249 wklejać
      * Usuń
   * Obiekty blob ADLS Gen2
      * Prześlij, łącznie z przeciągania & upuszczania
      * Pobierz
      * Kopiowanie & wklej
      * Usuń, w tym usunięcie folderu
   * Dyski zarządzane
      * Upload
      * Pobierz
      * Kopiowanie & wklej

   Ponadto do zintegrowanego środowiska AzCopy dodano kilka często żądanych funkcji:
   * Rozwiązywanie konfliktów — zostanie wyświetlony monit podczas transferów w celu rozwiązania konfliktów. #1455
   * Przekaż jako stronicowe obiekty blob - możesz wybrać, czy AzCopy przesyła pliki .vhd i .vhdx jako stronicowe obiekty blob. #1164 i #1601
   * Konfigurowalne parametry AzCopy — dodano kilka ustawień, aby dostroić wydajność i użycie zasobów AzCopy. Zobacz więcej szczegółów poniżej.

* Aby umożliwić dostęp do wielu protokołów ADLS Gen2 i obiektów blob oraz dodatkowo ulepszyć środowisko ADLS Gen2, dodaliśmy następujące funkcje dla kont ADLS Gen2:
   * Wyszukiwanie przy użyciu przyjaznych nazw w celu ustawienia uprawnień listy ACL
   * Wyświetlanie ukrytych kontenerów, takich jak $logs i $web
   * Nabywanie i przerywanie dzierżawy kontenerów
   * Nabywanie i przerywanie #848 dzierżawy obiektów Blob
   * Zarządzanie zasadami dostępu do kontenerów
   * Konfigurowanie warstw dostępu obiektów Blob
   * Kopiowanie & wklejają obiekty blob

* W tej wersji wyświetlamy podgląd 17 dodatkowych języków. Możesz przełączyć się na wybrany język na stronie ustawień w sekcji "Aplikacja" → "Ustawienia regionalne" → "Język (podgląd)". Wciąż ciężko pracujemy nad tłumaczeniem dodatkowych strun i poprawą jakości tłumaczenia. Jeśli masz jakieś opinie dotyczące tłumaczenia lub zauważysz ciąg, który nie został jeszcze przetłumaczony, [otwórz problem na GitHub](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=%F0%9F%8C%90%20localization&template=bug-report.md&title=).
* W każdej wersji staramy się wbudowane kilka ustawień, aby włączyć precyzyjne toczenia Eksploratora magazynu. W tej wersji dodaliśmy ustawienia, aby dodatkowo skonfigurować AzCopy, a także ukryć węzły usługi:
   * Limit przepustowości AzCopy - pomaga kontrolować, ile sieci AzCopy używa. To ustawienie można znaleźć na "Transfery" → "AzCopy" → "Maksymalna szybkość transferu". #1099
   * AzCopy MD5 check - pozwala skonfigurować, czy i jak ściśle AzCopy sprawdza skróty MD5 przy pobieraniu. To ustawienie można znaleźć na stronie "Transfery" → "AzCopy" → "Check MD5".
   * Współbieżność AzCopy i rozmiar buforu pamięci — domyślnie AzCopy przeanalizuje komputer w celu określenia rozsądnych wartości domyślnych dla tych ustawień. Ale jeśli napotkasz problemy z wydajnością, te zaawansowane ustawienia mogą być wykorzystane do dalszego dostosowania sposobu działania AzCopy na komputerze. Ustawienia te można znaleźć w sekcji "Transfery" → "AzCopy". #994
   * Wyświetlanie i ukrywanie węzłów usługi — te ustawienia umożliwiają wyświetlanie lub ukrywanie dowolnej usługi platformy Azure obsługiwanej przez Eksploratora usługi. Ustawienia te można znaleźć w sekcji "Usługi". #1877

* Podczas tworzenia migawki dysku zarządzanego jest teraz podana domyślna nazwa. #1847
* Podczas dołączania z usługą Azure AD, jeśli dołączysz kontener obiektów blob ADLS Gen2, obok węzła zostanie wyświetlony znak "(ADLS Gen2)". #1861

### <a name="fixes"></a>Poprawki
* Podczas kopiowania, przekazywania lub pobierania dużych dysków Eksplorator magazynu czasami nie może odwołać dostępu do dysków zaangażowanych w operację. Ten problem został rozwiązany. #2048
* Statystyki tabeli nie powiodły się podczas wyświetlania kwerendy klucza partycji. Ten problem został rozwiązany. #1886

### <a name="known-issues"></a>Znane problemy
* Explorer magazynu 1.11.0 wymaga teraz punktu końcowego systemu plików DFS (na przykład "myaccount.dfs.core.windows.net") do dołączenia do kontenerów ADLS Gen2. Poprzednie wersje Eksploratora magazynu umożliwiały używanie punktu końcowego obiektu blob. Te załączniki mogą przestać działać po uaktualnieniu do 1.11.0. Jeśli wystąpi ten problem, dołącz ponownie przy użyciu punktu końcowego systemu plików DFS.
* Ustawienia numeryczne nie są sprawdzane pod kątem tego, czy znajdują się w prawidłowym zakresie.#2140
* Kopiowanie kontenerów obiektów blob z jednego konta magazynu do innego w widoku drzewa może zakończyć się niepowodzeniem. Badamy problem.#2124
* Ustawienie Automatyczne odświeżanie nie ma jeszcze wpływu na wszystkie operacje w Eksploratorze obiektów blob.
* Funkcje dysku zarządzanego nie są obsługiwane w usłudze Azure Stack.
* Jeśli przekazywanie lub wklejanie dysku nie powiedzie się, a nowy dysk został utworzony przed awarią, Eksplorator magazynu nie usunie dysku.
* W zależności od tego, kiedy anulujesz przekazywanie lub wklejanie dysku, można pozostawić nowy dysk w stanie uszkodzonym. W takim przypadku należy usunąć nowy dysk lub ręcznie wywołać interfejsy API dysku, aby zastąpić zawartość dysku w taki sposób, że nie jest już uszkodzony.
* W przypadku korzystania z funkcji RBAC Eksplorator magazynu wymaga pewnych uprawnień warstwy zarządzania w celu uzyskania dostępu do zasobów magazynu. Więcej informacji można znaleźć w [przewodniku dotyczącym rozwiązywania problemów.](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)
* Odłączanie od zasobu dołączonego za pośrednictwem identyfikatora URI sygnatury dostępu Współdzielonego, takiego jak kontener obiektów blob, może spowodować błąd, który uniemożliwia poprawne wyświetlanie innych załączników. Aby obejść ten problem, wystarczy odświeżyć węzeł grupy. Zobacz #537, aby uzyskać więcej informacji.
* Jeśli używasz vs dla komputerów Mac i kiedykolwiek utworzono niestandardową konfigurację usługi AAD, może być nie można zalogować. Aby obejść ten problem, usuń zawartość ~/. IdentityService/AadConfigurations. Jeśli tak nie odblokuje Cię, wyśmiaj tę kwestię.
* Azurite nie zaimplementował jeszcze w pełni wszystkich interfejsów API magazynu. Z tego powodu mogą wystąpić nieoczekiwane błędy lub zachowanie podczas korzystania z Azurite do przechowywania programów deweloperskich.
* W rzadkich przypadkach fokus drzewa może utknąć na szybki dostęp. Aby odkleić fokus, możesz odświeżyć wszystko.
* Przekazywanie z folderu usługi OneDrive nie działa z powodu błędu w NodeJS. Błąd został naprawiony, ale nie został jeszcze zintegrowany z Electronem. Aby obejść ten problem podczas przekazywania lub pobierania z kontenera obiektów blob, można użyć eksperymentalnej funkcji AzCopy.
* Podczas kierowania usługi Azure Stack przekazywanie niektórych plików jako dołączanie obiektów blob może zakończyć się niepowodzeniem.
* Po kliknięciu przycisku "Anuluj" zadania anulowanie tego zadania może chwilę potrwać. Dzieje się tak, ponieważ używamy pracy filtru anulowania opisanego tutaj.
* Jeśli wybierzesz niewłaściwy certyfikat PIN/Karta inteligentna, musisz ponownie uruchomić komputer, aby Eksplorator magazynu zapomniał o tej decyzji.
* Zmiana nazwy obiektów blob (indywidualnie lub wewnątrz kontenera obiektów blob o zmienionej nazwie) nie zachowuje migawek. Wszystkie inne właściwości i metadane obiektów blob, plików i jednostek są zachowywane podczas zmiany nazwy.
* Usługa Azure Stack nie obsługuje następujących funkcji. Próba użycia tych funkcji podczas pracy z zasobami usługi Azure Stack może spowodować nieoczekiwane błędy.
   * Udziały plików
   * Poziomy dostępu
   * Usuwanie nietrwałe
   * ADLS Gen2
   * Dyski zarządzane
* Powłoka elektronu używana przez Eksploratora pamięci masowej ma problemy z akceleracją sprzętową niektórych procesorów graficznych (procesora graficznego). Jeśli Eksplorator magazynu wyświetla puste (puste) okno główne, możesz spróbować uruchomić Eksploratora magazynu `--disable-gpu` z wiersza polecenia i wyłączyć przyspieszenie procesora GPU, dodając przełącznik:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Uruchamianie Eksploratora magazynu w systemie Linux wymaga najpierw zainstalowania pewnych zależności. Więcej informacji można znaleźć w [przewodniku dotyczącym rozwiązywania problemów z Eksploratorem](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) magazynu.

## <a name="previous-releases"></a>Poprzednie wersje

* [Wersja 1.10.1](#version-1101)
* [Wersja 1.10.0](#version-1100)
* [Wersja 1.9.0](#version-190)
* [Wersja 1.8.1](#version-181)
* [Wersja 1.8.0](#version-180)
* [Wersja 1.7.0](#version-170)
* [Wersja 1.6.2](#version-162)
* [Wersja 1.6.1](#version-161)
* [Wersja 1.6.0](#version-160)
* [Wersja 1.5.0](#version-150)
* [Wersja 1.4.4](#version-144)
* [Wersja 1.4.3](#version-143)
* [Wersja 1.4.2](#version-142)
* [Wersja 1.4.1](#version-141)
* [Wersja 1.3.0](#version-130)
* [Wersja 1.2.0](#version-120)
* [Wersja 1.1.0](#version-110)
* [Wersja 1.0.0](#version-100)
* [Wersja 0.9.6](#version-096)
* [Wersja 0.9.5](#version-095)
* [Wersja 0.9.4 i 0.9.3](#version-094-and-093)
* [Wersja 0.9.2](#version-092)
* [Wersja 0.9.1 i 0.9.0](#version-091-and-090)
* [Wersja 0.8.16](#version-0816)
* [Wersja 0.8.14](#version-0814)
* [Wersja 0.8.13](#version-0813)
* [Wersja 0.8.12 i 0.8.11 i 0.8.10](#version-0812-and-0811-and-0810)
* [Wersja 0.8.9 i 0.8.8](#version-089-and-088)
* [Wersja 0.8.7](#version-087)
* [Wersja 0.8.6](#version-086)
* [Wersja 0.8.5](#version-085)
* [Wersja 0.8.4](#version-084)
* [Wersja 0.8.3](#version-083)
* [Wersja 0.8.2](#version-082)
* [Wersja 0.8.0](#version-080)
* [Wersja 0.7.20160509.0](#version-07201605090)
* [Wersja 0.7.20160325.0](#version-07201603250)
* [Wersja 0.7.20160129.1](#version-07201601291)
* [Wersja 0.7.20160105.0](#version-07201601050)
* [Wersja 0.7.20151116.0](#version-07201511160)

## <a name="version-1101"></a>Wersja 1.10.1
9/19/2019

### <a name="hotfix"></a>Poprawek
* Niektórzy użytkownicy napotkali błąd w 1.10.0 podczas próby wyświetlenia swoich danych na swoich kontach ADLS Gen 1. Ten błąd uniemożliwił poprawne renderowanie panelu eksploratora. Ten problem został rozwiązany. #1853 #1865

### <a name="new"></a>Nowa
* Eksplorator magazynu ma teraz dedykowany interfejs użytkownika ustawień. Dostęp do niego można uzyskać z sekcji Edytuj → Ustawienia lub klikając ikonę Ustawienia (bieg) na lewym pionowym pasku narzędzi. Ta funkcja jest pierwszym krokiem, który podejmujemy w kierunku zapewnienia różnych [ustawień żądanych przez użytkownika.](https://github.com/microsoft/AzureStorageExplorer/labels/%3Abulb%3A%20setting%20candidate) Począwszy od tej wersji obsługiwane są następujące ustawienia:
  * motyw
  * Serwer proxy
  * Wyloguj się przy wyjściu #6
  * Włączanie logowania przepływu kodu urządzenia
  * Automatyczne odświeżanie #1526
  * Włącz AzCopy
  * Czas trwania Sygnatury dostępu Współdzielonego Jeśli istnieją inne ustawienia, które chcesz zobaczyć dodane, [otwórz problem na GitHub](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=%3Abulb%3A%20setting%20candidate&template=feature_request.md&title=) opisujący ustawienie, które chcesz zobaczyć.
* Eksplorator magazynu obsługuje teraz dyski zarządzane. Możesz:
  * Przekazywanie lokalnego dysku VHD na nowy dysk
  * Pobieranie dysku
  * Kopiowanie i wklejanie dysków między grupami i regionami zasobów
  * Usuwanie dysków
  * Tworzenie migawki dysku

Przesyłanie, pobieranie i kopiowanie dysków w różnych regionach jest obsługiwane przez AzCopy v10.
* Eksploratora magazynu można teraz zainstalować za pośrednictwem sklepu Snap w systemie Linux. Po zainstalowaniu za pośrednictwem sklepu Snap, wszystkie zależności są instalowane dla Ciebie, w tym .NET Core! Obecnie sprawdziliśmy, że Eksplorator pamięci masowej działa dobrze na Ubuntu i CentOS. Jeśli napotkasz problemy z instalacją ze sklepu Snap w innych dystrybucjach Linuksa, [otwórz problem na GitHub](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=snaps&template=bug-report.md&title=). Aby dowiedzieć się więcej o instalacji ze sklepu Snap, zapoznaj się z naszym [przewodnikiem po rozpoczęciu](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=linux)pracy. #68
* Wprowadzono dwie główne zmiany w dołączanym usłudze Azure Active Directory (Azure AD), które mają sprawić, że funkcja będzie bardziej przydatna dla użytkowników ADLS Gen2:
  * Teraz wybierz dzierżawę, w której znajduje się zasób, do którego dołączasz. Oznacza to, że nie trzeba już mieć dostęp RBAC do subskrypcji zasobu.
  * Jeśli dołączasz kontener obiektów blob ADLS Gen2, można teraz dołączyć do określonej ścieżki w kontenerze.
* Podczas zarządzania listami ADL dla plików i folderów ADLS Gen2 Eksplorator magazynu będzie teraz wyświetlał przyjazne nazwy encji w liście ACL. #957
* Podczas dodawania za pośrednictwem identyfikatora OID do listy ADLS Gen2 ACL, Eksplorator magazynu będzie teraz sprawdzać, czy identyfikator należy do prawidłowej jednostki w dzierżawie. #1603
* Skróty klawiaturowe do nawigowania między kartami używają teraz bardziej standardowych kombinacji klawiszy. #1018
* Kliknięcie środka karty spowoduje jej teraz zamknięcie. #1348
* Jeśli transfer AzCopy zawiera przeskakiwanie i nie ma błędów, Eksplorator magazynu wyświetli teraz ikonę ostrzeżenia, aby podświetlić, że pominięto wystąpił. #1490
* Zintegrowany AzCopy został zaktualizowany do wersji 10.2.1. Ponadto można teraz wyświetlić wersję programu AzCopy zainstalowaną w oknie dialogowym Informacje. #1343

### <a name="fixes"></a>Poprawki
* Wielu użytkowników napotkało różne błędy "nie można odczytać wersji niezdefiniowanej" lub "nie można odczytać połączenia niezdefiniowanych" błędów podczas pracy z dołączonymi kontami magazynu. Mimo że nadal badamy główną przyczynę tego problemu, w 1.10.0 poprawiliśmy obsługę błędów wokół ładowania dołączonych kont magazynu. #1626, #985 i #1532
* Było możliwe dla drzewa eksploratora (po lewej stronie), aby dostać się do stanu, w którym fokus będzie przeskakiwać do górnego węzła wielokrotnie. Ten problem został rozwiązany. #1596
* Podczas zarządzania migawek obiektu blob, czytniki ekranu nie będzie odczytywać sygnatury czasowej skojarzone z migawką. Ten problem został rozwiązany. #1202
* Ustawienie serwera proxy w systemie macOS nie było ustawiane w czasie, aby proces uwierzytelniania z nich korzystał. Ten problem został rozwiązany. #1567
* Jeśli konto magazynu w suwerennej chmurze zostało dołączone przy użyciu nazwy i klucza, AzCopy nie będzie działać. Ten problem został rozwiązany. #1544
* Podczas dołączania za pomocą ciągu połączenia Eksplorator magazynu usunie teraz końcowe spacje. #1387

### <a name="known-issues"></a>Znane problemy
* Ustawienie Automatyczne odświeżanie nie ma jeszcze wpływu na wszystkie operacje w Eksploratorze obiektów blob.
* Funkcje dysku zarządzanego nie są obsługiwane w usłudze Azure Stack.
* Jeśli przekazywanie lub wklejanie dysku nie powiedzie się, a nowy dysk został utworzony przed awarią, Eksplorator magazynu nie usunie dysku.
* W zależności od tego, kiedy anulujesz przekazywanie lub wklejanie dysku, można pozostawić nowy dysk w stanie uszkodzonym. W takim przypadku należy usunąć nowy dysk lub ręcznie wywołać interfejsy API dysku, aby zastąpić zawartość dysku w taki sposób, że nie jest już uszkodzony.
* W zależności od tego, kiedy anulujesz przekazywanie lub wklejanie dysku, można pozostawić nowy dysk w stanie uszkodzonym. W takim przypadku należy usunąć nowy dysk lub ręcznie wywołać interfejsy API dysku, aby zastąpić zawartość dysku w taki sposób, że nie jest już uszkodzony.
* Podczas wykonywania pobierania obiektów blob innych niż AzCopy, MD5 dla dużych plików nie jest weryfikowany. Jest to spowodowane błędem w SDK magazynu. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* W przypadku korzystania z funkcji RBAC Eksplorator magazynu wymaga pewnych uprawnień warstwy zarządzania w celu uzyskania dostępu do zasobów magazynu. Więcej informacji można znaleźć w [przewodniku dotyczącym rozwiązywania problemów.](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)
* Odłączanie od zasobu dołączonego za pośrednictwem identyfikatora URI sygnatury dostępu Współdzielonego, takiego jak kontener obiektów blob, może spowodować błąd, który uniemożliwia poprawne wyświetlanie innych załączników. Aby obejść ten problem, wystarczy odświeżyć węzeł grupy. Zobacz #537, aby uzyskać więcej informacji.
* Jeśli używasz vs dla komputerów Mac i kiedykolwiek utworzono niestandardową konfigurację usługi AAD, może być nie można zalogować. Aby obejść ten problem, usuń zawartość ~/. IdentityService/AadConfigurations. Jeśli tak nie odblokuje Cię, wyśmiaj tę kwestię.
* Azurite nie zaimplementował jeszcze w pełni wszystkich interfejsów API magazynu. Z tego powodu mogą wystąpić nieoczekiwane błędy lub zachowanie podczas korzystania z Azurite do przechowywania programów deweloperskich.
* W rzadkich przypadkach fokus drzewa może utknąć na szybki dostęp. Aby odkleić fokus, możesz odświeżyć wszystko.
* Przekazywanie z folderu usługi OneDrive nie działa z powodu błędu w NodeJS. Błąd został naprawiony, ale nie został jeszcze zintegrowany z Electronem. Aby obejść ten problem podczas przekazywania lub pobierania z kontenera obiektów blob, można użyć eksperymentalnej funkcji AzCopy.
* Podczas kierowania usługi Azure Stack przekazywanie niektórych plików jako dołączanie obiektów blob może zakończyć się niepowodzeniem.
* Po kliknięciu przycisku "Anuluj" zadania anulowanie tego zadania może chwilę potrwać. Dzieje się tak, ponieważ używamy pracy filtru anulowania opisanego tutaj.
* Jeśli wybierzesz niewłaściwy certyfikat PIN/Karta inteligentna, musisz ponownie uruchomić komputer, aby Eksplorator magazynu zapomniał o tej decyzji.
* Zmiana nazwy obiektów blob (indywidualnie lub wewnątrz kontenera obiektów blob o zmienionej nazwie) nie zachowuje migawek. Wszystkie inne właściwości i metadane obiektów blob, plików i jednostek są zachowywane podczas zmiany nazwy.
* Usługa Azure Stack nie obsługuje następujących funkcji. Próba użycia tych funkcji podczas pracy z zasobami usługi Azure Stack może spowodować nieoczekiwane błędy.
   * Udziały plików
   * Poziomy dostępu
   * Usuwanie nietrwałe
   * ADLS Gen2
   * Dyski zarządzane
* Powłoka elektronu używana przez Eksploratora pamięci masowej ma problemy z akceleracją sprzętową niektórych procesorów graficznych (procesora graficznego). Jeśli Eksplorator magazynu wyświetla puste (puste) okno główne, możesz spróbować uruchomić Eksploratora magazynu `--disable-gpu` z wiersza polecenia i wyłączyć przyspieszenie procesora GPU, dodając przełącznik:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Uruchamianie Eksploratora magazynu w systemie Linux wymaga najpierw zainstalowania pewnych zależności. Więcej informacji można znaleźć w [przewodniku dotyczącym rozwiązywania problemów z Eksploratorem](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) magazynu.


## <a name="version-1100"></a>Wersja 1.10.0
9/12/2019

### <a name="new"></a>Nowa

* Eksplorator magazynu ma teraz dedykowany interfejs użytkownika ustawień. Dostęp do niego można uzyskać z sekcji Edytuj → Ustawienia lub klikając ikonę Ustawienia (bieg) na lewym pionowym pasku narzędzi. Ta funkcja jest pierwszym krokiem, który podejmujemy w kierunku zapewnienia różnych [ustawień żądanych przez użytkownika.](https://github.com/microsoft/AzureStorageExplorer/labels/%3Abulb%3A%20setting%20candidate) Począwszy od tej wersji obsługiwane są następujące ustawienia:
    * motyw
    * Serwer proxy
    * Wyloguj się przy [wyjściu #6](https://www.github.com/Microsoft/AzureStorageExplorer/issues/6)
    * Włączanie logowania przepływu kodu urządzenia
    * Automatyczne odświeżanie [#1526](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1526)
    * Włącz AzCopy
    * AzCopy SAS czas trwania

    Jeśli istnieją inne ustawienia, które chcesz zobaczyć dodane, [otwórz problem na GitHub opisujący ustawienie, które chcesz zobaczyć](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=%3Abulb%3A%20setting%20candidate&template=feature_request.md&title=).
* Eksplorator magazynu obsługuje teraz dyski zarządzane. Możesz:
    * Przekazywanie lokalnego dysku VHD na nowy dysk
    * Pobieranie dysku
    * Kopiowanie i wklejanie dysków między grupami i regionami zasobów
    * Usuwanie dysków
    * Tworzenie migawki dysku

    Przesyłanie, pobieranie i kopiowanie dysków w różnych regionach jest obsługiwane przez AzCopy v10.
* Eksploratora magazynu można teraz zainstalować za pośrednictwem sklepu Snap w systemie Linux. Po zainstalowaniu za pośrednictwem sklepu Snap, wszystkie zależności są instalowane dla Ciebie, w tym .NET Core! Obecnie sprawdziliśmy, że Eksplorator pamięci masowej działa dobrze na Ubuntu i CentOS. Jeśli napotkasz problemy z instalacją ze sklepu Snap w innych dystrybucjach Linuksa, [otwórz problem na GitHub](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=snaps&template=bug-report.md&title=). Aby dowiedzieć się więcej o instalacji ze sklepu Snap, zapoznaj się z naszym [przewodnikiem po rozpoczęciu](https://aka.ms/storageexplorer/snapinformation)pracy. [#68](https://www.github.com/Microsoft/AzureStorageExplorer/issues/68)
* Wprowadzono dwie główne zmiany w celu dołączenia do usługi Azure Active Directory (Azure AD), które mają na celu uczynienie tej funkcji bardziej użyteczną dla użytkowników ADLS Gen2: * Teraz wybierz dzierżawę, w której znajduje się dołączony zasób. Oznacza to, że nie trzeba już mieć dostęp RBAC do subskrypcji zasobu.
        * Jeśli dołączasz kontener obiektów blob ADLS Gen2, możesz teraz dołączyć do określonej ścieżki w kontenerze.
* Podczas zarządzania listami ADL dla plików i folderów ADLS Gen2 Eksplorator magazynu będzie teraz wyświetlał przyjazne nazwy encji w liście ACL. [#957](https://www.github.com/Microsoft/AzureStorageExplorer/issues/957)
* Podczas dodawania za pośrednictwem identyfikatora OID do listy ADLS Gen2 ACL, Eksplorator magazynu będzie teraz sprawdzać, czy identyfikator należy do prawidłowej jednostki w dzierżawie. [#1603](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1603)
* Skróty klawiaturowe do nawigowania między kartami używają teraz bardziej standardowych kombinacji klawiszy. [#1018](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1018)
* Kliknięcie środka karty spowoduje jej teraz zamknięcie. [#1348](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1348)
* Jeśli transfer AzCopy zawiera przeskakiwanie i nie ma błędów, Eksplorator magazynu wyświetli teraz ikonę ostrzeżenia, aby podświetlić, że pominięto wystąpił. [#1490](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1490)
* Zintegrowany AzCopy został zaktualizowany do wersji 10.2.1. Ponadto można teraz wyświetlić wersję programu AzCopy zainstalowaną w oknie dialogowym Informacje. [#1343](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1343)

### <a name="fixes"></a>Poprawki

* Wielu użytkowników napotkało różne błędy "nie można odczytać wersji niezdefiniowanej" lub "nie można odczytać połączenia niezdefiniowanych" błędów podczas pracy z dołączonymi kontami magazynu. Mimo że nadal badamy główną przyczynę tego problemu, w 1.10.0 poprawiliśmy obsługę błędów wokół ładowania dołączonych kont magazynu. [#1626,](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1626) [#985](https://www.github.com/Microsoft/AzureStorageExplorer/issues/985)i [#1532](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1532)
* Było możliwe dla drzewa eksploratora (po lewej stronie), aby dostać się do stanu, w którym fokus będzie przeskakiwać do górnego węzła wielokrotnie. Ten problem został rozwiązany. [#1596](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1596)
* Podczas zarządzania migawek obiektu blob, czytniki ekranu nie będzie odczytywać sygnatury czasowej skojarzone z migawką. Ten problem został rozwiązany. [#1202](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1202)
* Ustawienie serwera proxy w systemie macOS nie było ustawiane w czasie, aby proces uwierzytelniania z nich korzystał. Ten problem został rozwiązany. [#1567](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1567)
* Jeśli konto magazynu w suwerennej chmurze zostało dołączone przy użyciu nazwy i klucza, AzCopy nie będzie działać. Ten problem został rozwiązany. [#1544](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1544)
* Podczas dołączania za pomocą ciągu połączenia Eksplorator magazynu usunie teraz końcowe spacje. [#1387](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1387)

### <a name="known-issues"></a>Znane problemy

* Ustawienie Automatyczne odświeżanie nie ma jeszcze wpływu na wszystkie operacje w Eksploratorze obiektów blob.
* Funkcje dysku zarządzanego nie są obsługiwane w usłudze Azure Stack.
* Jeśli przekazywanie lub wklejanie dysku nie powiedzie się, a nowy dysk został utworzony przed awarią, Eksplorator magazynu nie usunie dysku.
* W zależności od tego, kiedy anulujesz przekazywanie lub wklejanie dysku, można pozostawić nowy dysk w stanie uszkodzonym. W takim przypadku należy usunąć nowy dysk lub ręcznie wywołać interfejsy API dysku, aby zastąpić zawartość dysku w taki sposób, że nie jest już uszkodzony.
* Podczas wykonywania pobierania obiektów blob innych niż AzCopy, MD5 dla dużych plików nie jest weryfikowany. Jest to spowodowane błędem w SDK magazynu. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* W przypadku korzystania z funkcji RBAC Eksplorator magazynu wymaga pewnych uprawnień warstwy zarządzania w celu uzyskania dostępu do zasobów magazynu. Więcej informacji można znaleźć w [przewodniku dotyczącym rozwiązywania problemów.](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)
* Odłączanie od zasobu dołączonego za pośrednictwem identyfikatora URI sygnatury dostępu Współdzielonego, takiego jak kontener obiektów blob, może spowodować błąd, który uniemożliwia poprawne wyświetlanie innych załączników. Aby obejść ten problem, wystarczy odświeżyć węzeł grupy. Zobacz #537, aby uzyskać więcej informacji.
* Jeśli używasz vs dla komputerów Mac i kiedykolwiek utworzono niestandardową konfigurację usługi AAD, może być nie można zalogować. Aby obejść ten problem, usuń zawartość ~/. IdentityService/AadConfigurations. Jeśli tak nie odblokuje Cię, wyśmiaj tę kwestię.
* Azurite nie zaimplementował jeszcze w pełni wszystkich interfejsów API magazynu. Z tego powodu mogą wystąpić nieoczekiwane błędy lub zachowanie podczas korzystania z Azurite do przechowywania programów deweloperskich.
* W rzadkich przypadkach fokus drzewa może utknąć na szybki dostęp. Aby odkleić fokus, możesz odświeżyć wszystko.
* Przekazywanie z folderu usługi OneDrive nie działa z powodu błędu w NodeJS. Błąd został naprawiony, ale nie został jeszcze zintegrowany z Electronem. Aby obejść ten problem podczas przekazywania lub pobierania z kontenera obiektów blob, można użyć eksperymentalnej funkcji AzCopy.
* Podczas kierowania usługi Azure Stack przekazywanie niektórych plików jako dołączanie obiektów blob może zakończyć się niepowodzeniem.
* Po kliknięciu przycisku "Anuluj" zadania anulowanie tego zadania może chwilę potrwać. Dzieje się tak, ponieważ używamy pracy filtru anulowania opisanego tutaj.
* Jeśli wybierzesz niewłaściwy certyfikat PIN/Karta inteligentna, musisz ponownie uruchomić komputer, aby Eksplorator magazynu zapomniał o tej decyzji.
* Zmiana nazwy obiektów blob (indywidualnie lub wewnątrz kontenera obiektów blob o zmienionej nazwie) nie zachowuje migawek. Wszystkie inne właściwości i metadane obiektów blob, plików i jednostek są zachowywane podczas zmiany nazwy.
* Usługa Azure Stack nie obsługuje następujących funkcji. Próba użycia tych funkcji podczas pracy z zasobami usługi Azure Stack może spowodować nieoczekiwane błędy.
   * Udziały plików
   * Poziomy dostępu
   * Usuwanie nietrwałe
   * ADLS Gen2
   * Dyski zarządzane
* Powłoka elektronu używana przez Eksploratora pamięci masowej ma problemy z akceleracją sprzętową niektórych procesorów graficznych (procesora graficznego). Jeśli Eksplorator magazynu wyświetla puste (puste) okno główne, możesz spróbować uruchomić Eksploratora magazynu `--disable-gpu` z wiersza polecenia i wyłączyć przyspieszenie procesora GPU, dodając przełącznik:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Uruchamianie Eksploratora magazynu w systemie Linux wymaga najpierw zainstalowania pewnych zależności. Więcej informacji można znaleźć w [przewodniku dotyczącym rozwiązywania problemów z Eksploratorem](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) magazynu.

## <a name="version-190"></a>Wersja 1.9.0
7/1/2019

### <a name="download-azure-storage-explorer-190"></a>Pobierz za darmo Azure Storage Explorer 1.9.0
- [Eksplorator usługi Azure Storage 1.9.0 dla systemu Windows](https://go.microsoft.com/fwlink/?LinkId=708343)
- [Usługa Azure Storage Explorer 1.9.0 dla komputerów Mac](https://go.microsoft.com/fwlink/?LinkId=708342)
- [Usługa Azure Storage Explorer 1.9.0 dla systemu Linux](https://go.microsoft.com/fwlink/?LinkId=722418)

### <a name="new"></a>Nowa

* Teraz można dołączyć kontenery obiektów blob za pośrednictwem usługi Azure AD (uprawnienia RBAC lub ACL). Ta funkcja ma na celu pomóc użytkownikom, którzy mają dostęp do kontenerów, ale nie kont magazynu, w którym znajdują się kontenery. Więcej informacji na temat tej funkcji można znaleźć w naszym przewodniku wprowadzenie.
* Nabywanie i zerwanie dzierżawy teraz współpracuje z RBAC. [#1354](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1354)
* Zarządzanie zasadami dostępu i ustawianie poziomu dostępu publicznego działa teraz z RBAC. [#1355](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1355)
* Usuwanie folderów obiektów blob działa teraz z rbac. [#1450](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1450)
* Zmiana warstwy dostępu obiektów blob teraz działa z RBAC. [#1446](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1446)
* Teraz możesz szybko zresetować szybki dostęp za pomocą "Pomoc" → "Reset". [#1327](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1327)

### <a name="preview-features"></a>Funkcje w wersji zapoznawczej

* Logowanie przepływu kodu urządzenia jest teraz dostępne do podglądu. Aby ją włączyć, przejdź do "Podgląd" → "Użyj logowania przepływu kodu urządzenia". Zachęcamy wszystkich użytkowników, którzy mieli problemy z pustymi oknami logowania, do wypróbowania tej funkcji, ponieważ może okazać się bardziej niezawodną formą logowania.
* Eksplorator magazynu zintegrowany z AzCopy jest obecnie dostępny do podglądu. Aby go włączyć, przejdź do "Podgląd" → "Użyj AzCopy dla ulepszonego przekazywania i pobierania obiektów blob". Transfery obiektów blob zakończone za pomocą AzCopy powinny być szybsze i bardziej wydajne.

### <a name="fixes"></a>Poprawki

* Naprawiono niemożność załadowania więcej niż 50 subskrypcji dla jednego konta. [#1416](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1416)
* Naprawiono przycisk "Zaloguj się" nie działa na pasku informacyjnym, który pojawia się, gdy bezpośrednie połączenie nie powiedzie się. [#1358](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1358)
* Naprawiono błąd, który nie powodował przekazywania plików aplikacji w systemie macOS. [#1119](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1119)
* Naprawiono "Ponów próbę wszystkich" nie działa dla nieudanej zmiany nazwy obiektu blob. [#992](https://www.github.com/Microsoft/AzureStorageExplorer/issues/992)
* Naprawiono błąd "Anuluj" niedziała podczas otwierania obiektu blob. [#1464](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1464)
* Naprawiono wiele problemów z pisownią i etykietką narzędzi w całym produkcie. Wielkie dzięki dla wszystkich, którzy zgłosili te problemy! [#1303](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1303), [#1328,](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1328) [#1329,](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1329) [#1331,](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1331) [#1336,](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1336) [#1352,](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1352) [#1368,](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1368) [#1395](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1395)

### <a name="known-issues"></a>Znane problemy

* Podczas wykonywania pobierania obiektów blob innych niż AzCopy, MD5 dla dużych plików nie jest weryfikowany. Jest to spowodowane błędem w SDK magazynu. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* W przypadku korzystania z funkcji RBAC Eksplorator magazynu wymaga pewnych uprawnień warstwy zarządzania w celu uzyskania dostępu do zasobów magazynu. Więcej informacji można znaleźć w [przewodniku dotyczącym rozwiązywania problemów.](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)
* Próba uzyskania dostępu do obiektów blob ADLS Gen2, gdy za serwerem proxy może zakończyć się niepowodzeniem.
* Odłączanie od zasobu dołączonego za pośrednictwem identyfikatora URI sygnatury dostępu Współdzielonego, takiego jak kontener obiektów blob, może spowodować błąd, który uniemożliwia poprawne wyświetlanie innych załączników. Aby obejść ten problem, wystarczy odświeżyć węzeł grupy. Zobacz #537, aby uzyskać więcej informacji.
* Jeśli używasz vs dla komputerów Mac i kiedykolwiek utworzono niestandardową konfigurację usługi AAD, może być nie można zalogować. Aby obejść ten problem, usuń zawartość ~/. IdentityService/AadConfigurations. Jeśli tak nie odblokuje Cię, wyśmiaj tę kwestię.
* Azurite nie zaimplementował jeszcze w pełni wszystkich interfejsów API magazynu. Z tego powodu mogą wystąpić nieoczekiwane błędy lub zachowanie podczas korzystania z Azurite do przechowywania programów deweloperskich.
* W rzadkich przypadkach fokus drzewa może utknąć na szybki dostęp. Aby odkleić fokus, możesz odświeżyć wszystko.
* Przekazywanie z folderu usługi OneDrive nie działa z powodu błędu w NodeJS. Błąd został naprawiony, ale nie został jeszcze zintegrowany z Electronem. Aby obejść ten problem podczas przekazywania lub pobierania z kontenera obiektów blob, można użyć eksperymentalnej funkcji AzCopy.
* Podczas kierowania usługi Azure Stack przekazywanie niektórych plików jako dołączanie obiektów blob może zakończyć się niepowodzeniem.
* Po kliknięciu przycisku "Anuluj" zadania anulowanie tego zadania może chwilę potrwać. Dzieje się tak, ponieważ używamy pracy filtru anulowania opisanego tutaj.
* Jeśli wybierzesz niewłaściwy certyfikat PIN/Karta inteligentna, musisz ponownie uruchomić komputer, aby Eksplorator magazynu zapomniał o tej decyzji.
* Zmiana nazwy obiektów blob (indywidualnie lub wewnątrz kontenera obiektów blob o zmienionej nazwie) nie zachowuje migawek. Wszystkie inne właściwości i metadane obiektów blob, plików i jednostek są zachowywane podczas zmiany nazwy.
* Usługa Azure Stack nie obsługuje następujących funkcji. Próba użycia tych funkcji podczas pracy z zasobami usługi Azure Stack może spowodować nieoczekiwane błędy.
   * Udziały plików
   * Poziomy dostępu
   * Usuwanie nietrwałe
   * ADLS Gen2
* Powłoka elektronu używana przez Eksploratora pamięci masowej ma problemy z akceleracją sprzętową niektórych procesorów graficznych (procesora graficznego). Jeśli Eksplorator magazynu wyświetla puste (puste) okno główne, możesz spróbować uruchomić Eksploratora magazynu `--disable-gpu` z wiersza polecenia i wyłączyć przyspieszenie procesora GPU, dodając przełącznik:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Uruchamianie Eksploratora magazynu w systemie Linux wymaga najpierw zainstalowania pewnych zależności. Więcej informacji można znaleźć w [przewodniku dotyczącym rozwiązywania problemów z Eksploratorem](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) magazynu.

## <a name="version-181"></a>Wersja 1.8.1
5/13/2019

### <a name="hotfixes"></a>Poprawki
* W niektórych przypadkach kliknięcie przycisku "Załaduj więcej" na poziomie zasobu nie zwróci następnej strony zasobów. Ten problem został rozwiązany. [#1359](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1359)
* W systemie Windows pobieranie AzCopy zakończy się niepowodzeniem, jeśli jeden plik lub folder był pobierany, a nazwa pliku lub folderu miała nieprawidłowy znak dla ścieżki systemu Windows. Ten problem został rozwiązany. [#1350](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1350)
* W bardzo rzadkich przypadkach podczas wykonywania zmiany nazwy udziału plików lub zmiany nazwy w udziale plików, jeśli kopie zmiany nazwy nie powiodły się lub jeśli Eksplorowanie magazynu nie może potwierdzić powodzenia kopii za pomocą platformy Azure, istnieje możliwość usunięcia przez Eksploratora magazynu oryginalnych plików przed zakończeniem kopiowania. Ten problem został rozwiązany.

### <a name="new"></a>Nowa

* Zintegrowana wersja AzCopy została zaktualizowana do wersji 10.1.0.
* Ctrl/Cmd+R można teraz odświeżyć aktualnie skoncentrowany edytor. [#1097](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1097)
* Wersja interfejsu API usługi Azure Stack Storage została zmieniona na 2017-04-17.
* Okno dialogowe Zarządzanie dostępem dla usługi ADLS Gen2 będzie teraz synchronizować maskę w sposób podobny do innych narzędzi uprawnień POSIX. Interfejs użytkownika będzie również ostrzegać, jeśli zostanie wniesiona zmiana, która powoduje, że uprawnienia użytkownika lub grupy, aby przekroczyć granice Maska. [#1253](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1253)
* W przypadku przekazywania AzCopy flaga do obliczania i ustawiania skrótu MD5 jest teraz włączona. [#1223](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1223)


### <a name="preview-features"></a>Funkcje w wersji zapoznawczej

* Logowanie przepływu kodu urządzenia jest teraz dostępne do podglądu. Aby ją włączyć, przejdź do "Podgląd" → "Użyj logowania przepływu kodu urządzenia". Zachęcamy wszystkich użytkowników, którzy mieli problemy z pustymi oknami logowania, do wypróbowania tej funkcji, ponieważ może okazać się bardziej niezawodną formą logowania.
* Eksplorator magazynu zintegrowany z AzCopy jest obecnie dostępny do podglądu. Aby go włączyć, przejdź do "Podgląd" → "Użyj AzCopy dla ulepszonego przekazywania i pobierania obiektów blob". Transfery obiektów blob zakończone za pomocą AzCopy powinny być szybsze i bardziej wydajne.

### <a name="fixes"></a>Poprawki

* W oknie dialogowym Zasady dostępu nie będzie już ustawiać daty wygaśnięcia zasad dostępu do magazynu, które nie mają wygaśnięcia. [#764](https://www.github.com/Microsoft/AzureStorageExplorer/issues/764)
* Wprowadzono pewne zmiany w oknie dialogowym Generowanie sygnatury dostępu współdzielonego, aby upewnić się, że zasady dostępu przechowywane są używane poprawnie podczas generowania sygnatury dostępu Współdzielonego. [#1269](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1269)
* Podczas próby przekazania pliku wyrównanego bajtów nie 512 do strony obiektu Blob Eksplorator magazynu będzie teraz uwidaczniać bardziej odpowiedni błąd. [#1050](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1050)
* Kopiowanie kontenera obiektów blob, który wykorzystywał nazwę wyświetlaną, zakończy się niepowodzeniem. Teraz używana jest rzeczywista nazwa kontenera obiektów Blob. [#1166](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1166)
* Próba wykonania pewnych akcji w folderze ADLS Gen2, w którym w nazwie są znaki unicode, zakończy się niepowodzeniem. Wszystkie działania powinny teraz działać. [#980](https://www.github.com/Microsoft/AzureStorageExplorer/issues/980)

### <a name="known-issues"></a>Znane problemy

* Podczas wykonywania pobierania obiektów blob innych niż AzCopy, MD5 dla dużych plików nie jest weryfikowany. Jest to spowodowane błędem w SDK magazynu. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* W przypadku korzystania z funkcji RBAC Eksplorator magazynu wymaga pewnych uprawnień warstwy zarządzania w celu uzyskania dostępu do zasobów magazynu. Więcej informacji można znaleźć w [przewodniku dotyczącym rozwiązywania problemów.](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)
* Próba uzyskania dostępu do obiektów blob ADLS Gen2, gdy za serwerem proxy może zakończyć się niepowodzeniem.
* Odłączanie od zasobu dołączonego za pośrednictwem identyfikatora URI sygnatury dostępu Współdzielonego, takiego jak kontener obiektów blob, może spowodować błąd, który uniemożliwia poprawne wyświetlanie innych załączników. Aby obejść ten problem, wystarczy odświeżyć węzeł grupy. Zobacz #537, aby uzyskać więcej informacji.
* Jeśli używasz vs dla komputerów Mac i kiedykolwiek utworzono niestandardową konfigurację usługi AAD, może być nie można zalogować. Aby obejść ten problem, usuń zawartość ~/. IdentityService/AadConfigurations. Jeśli tak nie odblokuje Cię, wyśmiaj tę kwestię.
* Azurite nie zaimplementował jeszcze w pełni wszystkich interfejsów API magazynu. Z tego powodu mogą wystąpić nieoczekiwane błędy lub zachowanie podczas korzystania z Azurite do przechowywania programów deweloperskich.
* W rzadkich przypadkach fokus drzewa może utknąć na szybki dostęp. Aby odkleić fokus, możesz odświeżyć wszystko.
* Przekazywanie z folderu usługi OneDrive nie działa z powodu błędu w NodeJS. Błąd został naprawiony, ale nie został jeszcze zintegrowany z Electronem. Aby obejść ten problem podczas przekazywania lub pobierania z kontenera obiektów blob, można użyć eksperymentalnej funkcji AzCopy.
* Podczas kierowania usługi Azure Stack przekazywanie niektórych plików jako dołączanie obiektów blob może zakończyć się niepowodzeniem.
* Po kliknięciu przycisku "Anuluj" zadania anulowanie tego zadania może chwilę potrwać. Dzieje się tak, ponieważ używamy pracy filtru anulowania opisanego tutaj.
* Jeśli wybierzesz niewłaściwy certyfikat PIN/Karta inteligentna, musisz ponownie uruchomić komputer, aby Eksplorator magazynu zapomniał o tej decyzji.
* Zmiana nazwy obiektów blob (indywidualnie lub wewnątrz kontenera obiektów blob o zmienionej nazwie) nie zachowuje migawek. Wszystkie inne właściwości i metadane obiektów blob, plików i jednostek są zachowywane podczas zmiany nazwy.
* Usługa Azure Stack nie obsługuje następujących funkcji. Próba użycia tych funkcji podczas pracy z zasobami usługi Azure Stack może spowodować nieoczekiwane błędy.
   * Udziały plików
   * Poziomy dostępu
   * Usuwanie nietrwałe
   * ADLS Gen2
* Powłoka elektronu używana przez Eksploratora pamięci masowej ma problemy z akceleracją sprzętową niektórych procesorów graficznych (procesora graficznego). Jeśli Eksplorator magazynu wyświetla puste (puste) okno główne, możesz spróbować uruchomić Eksploratora magazynu `--disable-gpu` z wiersza polecenia i wyłączyć przyspieszenie procesora GPU, dodając przełącznik:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Uruchamianie Eksploratora magazynu w systemie Linux wymaga najpierw zainstalowania pewnych zależności. Więcej informacji można znaleźć w [przewodniku dotyczącym rozwiązywania problemów z Eksploratorem](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) magazynu.

## <a name="version-180"></a>Wersja 1.8.0
1.05.2019

### <a name="new"></a>Nowa

* Zintegrowana wersja AzCopy została zaktualizowana do wersji 10.1.0.
* Ctrl/Cmd+R można teraz odświeżyć aktualnie skoncentrowany edytor. [#1097](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1097)
* Wersja interfejsu API usługi Azure Stack Storage została zmieniona na 2017-04-17.
* Okno dialogowe Zarządzanie dostępem dla usługi ADLS Gen2 będzie teraz synchronizować maskę w sposób podobny do innych narzędzi uprawnień POSIX. Interfejs użytkownika będzie również ostrzegać, jeśli zostanie wniesiona zmiana, która powoduje, że uprawnienia użytkownika lub grupy, aby przekroczyć granice Maska. [#1253](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1253)
* W przypadku przekazywania AzCopy flaga do obliczania i ustawiania skrótu MD5 jest teraz włączona. [#1223](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1223)


### <a name="preview-features"></a>Funkcje w wersji zapoznawczej

* Logowanie przepływu kodu urządzenia jest teraz dostępne do podglądu. Aby ją włączyć, przejdź do "Podgląd" → "Użyj logowania przepływu kodu urządzenia". Zachęcamy wszystkich użytkowników, którzy mieli problemy z pustymi oknami logowania, do wypróbowania tej funkcji, ponieważ może okazać się bardziej niezawodną formą logowania.
* Eksplorator magazynu zintegrowany z AzCopy jest obecnie dostępny do podglądu. Aby go włączyć, przejdź do "Podgląd" → "Użyj AzCopy dla ulepszonego przekazywania i pobierania obiektów blob". Transfery obiektów blob zakończone za pomocą AzCopy powinny być szybsze i bardziej wydajne.

### <a name="fixes"></a>Poprawki

* W oknie dialogowym Zasady dostępu nie będzie już ustawiać daty wygaśnięcia zasad dostępu do magazynu, które nie mają wygaśnięcia. [#764](https://www.github.com/Microsoft/AzureStorageExplorer/issues/764)
* Wprowadzono pewne zmiany w oknie dialogowym Generowanie sygnatury dostępu współdzielonego, aby upewnić się, że zasady dostępu przechowywane są używane poprawnie podczas generowania sygnatury dostępu Współdzielonego. [#1269](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1269)
* Podczas próby przekazania pliku wyrównanego bajtów nie 512 do strony obiektu Blob Eksplorator magazynu będzie teraz uwidaczniać bardziej odpowiedni błąd. [#1050](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1050)
* Kopiowanie kontenera obiektów blob, który wykorzystywał nazwę wyświetlaną, zakończy się niepowodzeniem. Teraz używana jest rzeczywista nazwa kontenera obiektów Blob. [#1166](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1166)
* Próba wykonania pewnych akcji w folderze ADLS Gen2, w którym w nazwie są znaki unicode, zakończy się niepowodzeniem. Wszystkie działania powinny teraz działać. [#980](https://www.github.com/Microsoft/AzureStorageExplorer/issues/980)

### <a name="known-issues"></a>Znane problemy

* Podczas wykonywania pobierania obiektów blob innych niż AzCopy, MD5 dla dużych plików nie jest weryfikowany. Jest to spowodowane błędem w SDK magazynu. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* W przypadku korzystania z funkcji RBAC Eksplorator magazynu wymaga pewnych uprawnień warstwy zarządzania w celu uzyskania dostępu do zasobów magazynu. Więcej informacji można znaleźć w [przewodniku dotyczącym rozwiązywania problemów.](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)
* Próba uzyskania dostępu do obiektów blob ADLS Gen2, gdy za serwerem proxy może zakończyć się niepowodzeniem.
* Odłączanie od zasobu dołączonego za pośrednictwem identyfikatora URI sygnatury dostępu Współdzielonego, takiego jak kontener obiektów blob, może spowodować błąd, który uniemożliwia poprawne wyświetlanie innych załączników. Aby obejść ten problem, wystarczy odświeżyć węzeł grupy. Zobacz #537, aby uzyskać więcej informacji.
* Jeśli używasz vs dla komputerów Mac i kiedykolwiek utworzono niestandardową konfigurację usługi AAD, może być nie można zalogować. Aby obejść ten problem, usuń zawartość ~/. IdentityService/AadConfigurations. Jeśli tak nie odblokuje Cię, wyśmiaj tę kwestię.
* Azurite nie zaimplementował jeszcze w pełni wszystkich interfejsów API magazynu. Z tego powodu mogą wystąpić nieoczekiwane błędy lub zachowanie podczas korzystania z Azurite do przechowywania programów deweloperskich.
* W rzadkich przypadkach fokus drzewa może utknąć na szybki dostęp. Aby odkleić fokus, możesz odświeżyć wszystko.
* Przekazywanie z folderu usługi OneDrive nie działa z powodu błędu w NodeJS. Błąd został naprawiony, ale nie został jeszcze zintegrowany z Electronem. Aby obejść ten problem podczas przekazywania lub pobierania z kontenera obiektów blob, można użyć eksperymentalnej funkcji AzCopy.
* Podczas kierowania usługi Azure Stack przekazywanie niektórych plików jako dołączanie obiektów blob może zakończyć się niepowodzeniem.
* Po kliknięciu przycisku "Anuluj" zadania anulowanie tego zadania może chwilę potrwać. Dzieje się tak, ponieważ używamy pracy filtru anulowania opisanego tutaj.
* Jeśli wybierzesz niewłaściwy certyfikat PIN/Karta inteligentna, musisz ponownie uruchomić komputer, aby Eksplorator magazynu zapomniał o tej decyzji.
* Zmiana nazwy obiektów blob (indywidualnie lub wewnątrz kontenera obiektów blob o zmienionej nazwie) nie zachowuje migawek. Wszystkie inne właściwości i metadane obiektów blob, plików i jednostek są zachowywane podczas zmiany nazwy.
* Usługa Azure Stack nie obsługuje następujących funkcji. Próba użycia tych funkcji podczas pracy z zasobami usługi Azure Stack może spowodować nieoczekiwane błędy.
   * Udziały plików
   * Poziomy dostępu
   * Usuwanie nietrwałe
   * ADLS Gen2
* Powłoka elektronu używana przez Eksploratora pamięci masowej ma problemy z akceleracją sprzętową niektórych procesorów graficznych (procesora graficznego). Jeśli Eksplorator magazynu wyświetla puste (puste) okno główne, możesz spróbować uruchomić Eksploratora magazynu `--disable-gpu` z wiersza polecenia i wyłączyć przyspieszenie procesora GPU, dodając przełącznik:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Uruchamianie Eksploratora magazynu w systemie Linux wymaga najpierw zainstalowania pewnych zależności. Więcej informacji można znaleźć w [przewodniku dotyczącym rozwiązywania problemów z Eksploratorem](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) magazynu.

## <a name="version-170"></a>Wersja 1.7.0
3/5/2019

### <a name="download-azure-storage-explorer-170"></a>Pobierz usługę Azure Storage Explorer 1.7.0
- [Eksplorator usługi Azure Storage 1.7.0 dla systemu Windows](https://go.microsoft.com/fwlink/?LinkId=708343)
- [Usługa Azure Storage Explorer 1.7.0 dla komputerów Mac](https://go.microsoft.com/fwlink/?LinkId=708342)
- [Usługa Azure Storage Explorer 1.7.0 dla systemu Linux](https://go.microsoft.com/fwlink/?LinkId=722418)

### <a name="new"></a>Nowa

* Teraz można zmienić właściciela i grupę będącą właścicielem podczas zarządzania dostępem do kontenera, pliku lub folderu ADLS Gen2.
* W systemie Windows aktualizowanie Eksploratora magazynu z poziomu produktu jest teraz instalacją przyrostową. Powinno to spowodować szybsze środowisko aktualizacji. Jeśli wolisz czystą instalację, możesz pobrać [instalator](https://azure.microsoft.com/features/storage-explorer/) samodzielnie, a następnie zainstalować ręcznie. #1089

### <a name="preview-features"></a>Funkcje w wersji zapoznawczej

* Logowanie przepływu kodu urządzenia jest teraz dostępne do podglądu. Aby ją włączyć, przejdź do "Podgląd" → "Użyj logowania przepływu kodu urządzenia". Zachęcamy wszystkich użytkowników, którzy mieli problemy z pustymi oknami logowania, do wypróbowania tej funkcji, ponieważ może okazać się bardziej niezawodną formą logowania. #938
* Eksplorator magazynu zintegrowany z AzCopy jest obecnie dostępny do podglądu. Aby go włączyć, przejdź do "Podgląd" → "Użyj AzCopy dla ulepszonego przekazywania i pobierania obiektów blob". Transfery obiektów blob zakończone za pomocą AzCopy powinny być szybsze i bardziej wydajne.

### <a name="fixes"></a>Poprawki

* Teraz możesz wybrać typ obiektu blob, który chcesz przekazać, tak jak po włączeniu AzCopy. #1111
* Wcześniej, jeśli włączono statyczne witryny sieci Web dla konta magazynu ADLS Gen2, a następnie dołączono go z nazwą i kluczem, Eksplorator magazynu nie wykryłby, że hierarchiczna przestrzeń nazw została włączona. Ten problem został rozwiązany. #1081
* W edytorze obiektów blob sortowanie według pozostałych dni przechowywania lub stanu zostało przerwane. Ten problem został rozwiązany. #1106
* Po wersji 1.5.0 Eksplorator magazynu nie czekał już na zakończenie kopii po stronie serwera przed zgłoszeniem sukcesu podczas zmiany nazwy lub kopiowania & wklejanie. Ten problem został rozwiązany. #976
* Podczas korzystania z eksperymentalnej funkcji AzCopy polecenie skopiowane po kliknięciu przycisku "Kopiuj polecenie do schowka" nie zawsze było możliwe do samodzielnego uruchomienia. Teraz wszystkie polecenia potrzebne do ręcznego uruchomienia transferu zostaną skopiowane. #1079
* Wcześniej obiekty blob ADLS Gen2 nie były dostępne, jeśli byłeś za serwerem proxy. Było to spowodowane błędem w nowej bibliotece sieciowej używanej przez SDK magazynu. W 1.7.0 podjęto próbę złagodzenia tego problemu, ale niektórzy ludzie mogą nadal dostrzegać problemy. Pełna poprawka zostanie wydana w przyszłej aktualizacji. #1090
* W 1.7.0 okno dialogowe zapisywania pliku poprawnie zapamiętuje ostatnią lokalizację, w której zapisano plik. #16
* W panelu właściwości warstwa jednostki SKU konta magazynu była wyświetlana jako rodzaj konta. Ten problem został rozwiązany. #654
* Czasami nie można było przerwać dzierżawy obiektu blob, nawet jeśli nazwa obiektu blob została poprawnie wprowadzona. Ten problem został rozwiązany. #1070

### <a name="known-issues"></a>Znane problemy

* W przypadku korzystania z funkcji RBAC Eksplorator magazynu wymaga pewnych uprawnień warstwy zarządzania w celu uzyskania dostępu do zasobów magazynu. Więcej informacji można znaleźć w [przewodniku dotyczącym rozwiązywania problemów.](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)
* Próba uzyskania dostępu do obiektów blob ADLS Gen2, gdy za serwerem proxy może zakończyć się niepowodzeniem.
* Odłączanie od zasobu dołączonego za pośrednictwem identyfikatora URI sygnatury dostępu Współdzielonego, takiego jak kontener obiektów blob, może spowodować błąd, który uniemożliwia poprawne wyświetlanie innych załączników. Aby obejść ten problem, wystarczy odświeżyć węzeł grupy. Zobacz #537, aby uzyskać więcej informacji.
* Odłączanie od zasobu dołączonego za pośrednictwem identyfikatora URI sygnatury dostępu Współdzielonego, takiego jak kontener obiektów blob, może spowodować błąd, który uniemożliwia poprawne wyświetlanie innych załączników. Aby obejść ten problem, wystarczy odświeżyć węzeł grupy. Aby uzyskać więcej informacji, zobacz #537.
* Jeśli używasz vs dla komputerów Mac i kiedykolwiek utworzono niestandardową konfigurację usługi AAD, może być nie można zalogować. Aby obejść ten problem, usuń zawartość ~/. IdentityService/AadConfigurations. Jeśli tak nie odblokuje Cię, wyśmiaj tę kwestię.
* Azurite nie zaimplementował jeszcze w pełni wszystkich interfejsów API magazynu. Z tego powodu mogą wystąpić nieoczekiwane błędy lub zachowanie podczas korzystania z Azurite do przechowywania programów deweloperskich.
* W rzadkich przypadkach fokus drzewa może utknąć na szybki dostęp. Aby odkleić fokus, możesz odświeżyć wszystko.
* Przekazywanie z folderu usługi OneDrive nie działa z powodu błędu w NodeJS. Błąd został naprawiony, ale nie został jeszcze zintegrowany z Electronem. Aby obejść ten problem podczas przekazywania lub pobierania z kontenera obiektów blob, można użyć eksperymentalnej funkcji AzCopy.
* Podczas kierowania usługi Azure Stack przekazywanie niektórych plików jako dołączanie obiektów blob może zakończyć się niepowodzeniem.
* Po kliknięciu przycisku "Anuluj" zadania anulowanie tego zadania może chwilę potrwać. Dzieje się tak, ponieważ używamy pracy filtru anulowania opisanego tutaj.
* Jeśli wybierzesz niewłaściwy certyfikat PIN/Karta inteligentna, musisz ponownie uruchomić komputer, aby Eksplorator magazynu zapomniał o tej decyzji.
* Zmiana nazwy obiektów blob (indywidualnie lub wewnątrz kontenera obiektów blob o zmienionej nazwie) nie zachowuje migawek. Wszystkie inne właściwości i metadane obiektów blob, plików i jednostek są zachowywane podczas zmiany nazwy.
* Usługa Azure Stack nie obsługuje następujących funkcji. Próba użycia tych funkcji podczas pracy z zasobami usługi Azure Stack może spowodować nieoczekiwane błędy.
   * Udziały plików
   * Poziomy dostępu
   * Usuwanie nietrwałe
* Powłoka elektronu używana przez Eksploratora pamięci masowej ma problemy z akceleracją sprzętową niektórych procesorów graficznych (procesora graficznego). Jeśli Eksplorator magazynu wyświetla puste (puste) okno główne, możesz spróbować uruchomić Eksploratora magazynu `--disable-gpu` z wiersza polecenia i wyłączyć przyspieszenie procesora GPU, dodając przełącznik:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Dla użytkowników Linuksa, trzeba będzie zainstalować [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Dla użytkowników na Ubuntu 14.04, trzeba będzie upewnić się, GCC jest na bieżąco - można to zrobić, uruchamiając następujące polecenia, a następnie ponowne uruchomienie komputera:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Dla użytkowników na Ubuntu 17.04, trzeba będzie zainstalować GConf - można to zrobić, uruchamiając następujące polecenia, a następnie ponowne uruchomienie komputera:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-162"></a>Wersja 1.6.2
1/9/2019

### <a name="hotfixes"></a>Poprawki
* W 1.6.1 jednostki dodane do list ACL ADLS Gen2 przez ObjectId, które nie były użytkownikami, były zawsze dodawane jako grupy. Teraz tylko grupy są dodawane jako grupy, a jednostki, takie jak aplikacje przedsiębiorstwa i podmioty usługi są dodawane jako użytkownicy. [#1049](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1049)
* Jeśli konto magazynu ADLS Gen2 nie miało kontenerów i zostało dołączone z nazwą i kluczem, Eksplorator magazynu nie wykryłby, że kontem magazynu jest ADLS Gen2. Ten problem został rozwiązany. [#1048](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1048)
* W wersji 1.6.0 konflikty podczas kopiowania i wklejania nie monitują o rozwiązanie. Zamiast tego skonfliktowana kopia po prostu zakończy się niepowodzeniem. Teraz, w pierwszym konflikcie, zostaniesz zapytany, jak chcesz go rozwiązać. [#1014](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1014)
* Ze względu na ograniczenia interfejsu API wszystkie sprawdzanie poprawności ObjectIds w oknie dialogowym Zarządzanie dostępem zostały wyłączone. Sprawdzanie poprawności będzie teraz występować tylko dla sieci UPN użytkowników. [#954](https://www.github.com/Microsoft/AzureStorageExplorer/issues/954)
* W oknie dialogowym Zarządzanie programem ADLS Gen2 nie można zmodyfikować uprawnień dla grupy. Ten problem został rozwiązany. [#958](https://www.github.com/Microsoft/AzureStorageExplorer/issues/958)
* Dodano obsługę przeciągnij i upuść do edytora ADLS Gen2. [#953](https://www.github.com/Microsoft/AzureStorageExplorer/issues/953)
* Właściwość adresu URL w oknie dialogowym właściwości plików i folderów ADLS Gen2 czasami brakowało '/'. Ten problem został rozwiązany. [#960](https://www.github.com/Microsoft/AzureStorageExplorer/issues/960)
* Jeśli uzyskanie bieżących uprawnień do kontenera, pliku lub folderu ADLS Gen2 nie powiedzie się, błąd jest teraz odpowiednio wyświetlany w dzienniku aktywności. [#965](https://www.github.com/Microsoft/AzureStorageExplorer/issues/965)
* Tymczasowa ścieżka utworzona do otwierania plików została skrócona, aby zmniejszyć ryzyko utworzenia ścieżki, która jest dłuższa niż MAX_PATH w systemie Windows. [#93](https://www.github.com/Microsoft/AzureStorageExplorer/issues/93)
* Okno dialogowe Połącz teraz poprawnie pojawia się, gdy nie ma zalogowanych użytkowników i nie zostały dołączone żadne zasoby. [#944](https://www.github.com/Microsoft/AzureStorageExplorer/issues/944)
* W 1.6.0 zapisywanie właściwości dla obiektów blob i plików innych niż HNS zakoduje wartość każdej właściwości. Spowodowało to niepotrzebne kodowanie wartości, które zawierały tylko znaki ASCII. Teraz wartości będą kodowane tylko wtedy, gdy zawierają znaki inne niż ASCII. [#986](https://www.github.com/Microsoft/AzureStorageExplorer/issues/986)
* Przekazywanie folderu do kontenera obiektów blob innych niż HNS zakończy się niepowodzeniem, jeśli został użyty sygnatura dostępu Współdzielonego, a sygnatura dostępu Współdzielonego dostępu Współdzielonego nie miała uprawnień do odczytu. Ten problem został rozwiązany. [#970](https://www.github.com/Microsoft/AzureStorageExplorer/issues/970)
* Anulowanie transferu AzCopy nie zadziałało. Ten problem został rozwiązany. [#943](https://www.github.com/Microsoft/AzureStorageExplorer/issues/943)
* AzCopy nie powiedzie się podczas próby pobrania folderu z kontenera obiektów blob ADLS Gen2, jeśli folder miał spacje w jego nazwie. Ten problem został rozwiązany. [#990](https://www.github.com/Microsoft/AzureStorageExplorer/issues/990)
* Edytor CosmosDB został uszkodzony w 1.6.0. Teraz został naprawiony. [#950](https://www.github.com/Microsoft/AzureStorageExplorer/issues/950)
        
### <a name="new"></a>Nowa

* Teraz można użyć Eksploratora magazynu, aby uzyskać dostęp do danych obiektów blob za pośrednictwem [pliku RBAC](https://go.microsoft.com/fwlink/?linkid=2045904&clcid=0x409). Jeśli użytkownik jest zalogowany i Eksplorator magazynu nie może pobrać kluczy dla konta magazynu, token OAuth będzie używany do uwierzytelniania podczas interakcji z danymi.
* Eksplorator magazynu obsługuje teraz konta magazynu ADLS Gen2. Gdy Eksplorator magazynu wykryje, że hierarchiczna przestrzeń nazw jest włączona dla konta magazynu, obok nazwy konta magazynu zostanie wyświetlony napis "(ADLS Gen2 Preview)". Eksplorator magazynu jest w stanie wykryć, czy hierarchiczna przestrzeń nazw jest włączona po zalogowaniu się lub czy konto magazynu zostało dołączone z nazwą i kluczem. W przypadku kont magazynu ADLS Gen2 można użyć Eksploratora magazynu, aby:
  * Tworzenie i usuwanie kontenerów
  * Zarządzanie właściwościami i uprawnieniami kontenera (po lewej stronie)
  * Wyświetlanie i nawigowanie po danych wewnątrz kontenerów
  * Tworzenie nowych folderów
  * Przesyłanie, pobieranie, zmienianie nazw i usuwanie plików i folderów
  * Zarządzanie właściwościami i uprawnieniami plików i folderów (po prawej stronie).
    
    Inne typowe funkcje obiektów blob, takie jak usuwanie nietrwałe i migawki, nie są obecnie dostępne. Zarządzanie uprawnieniami jest również dostępne tylko po zalogowaniu. Ponadto podczas pracy na koncie magazynu ADLS Gen2 Eksplorator magazynu użyje AzCopy dla wszystkich przekazywania i pobierania oraz domyślnie używa nazwy i poświadczeń klucza dla wszystkich operacji, jeśli są dostępne.
* Po silne opinie użytkowników, break lease może ponownie służyć do przerwania dzierżawy na wiele obiektów blob naraz.

### <a name="known-issues"></a>Znane problemy

* Podczas pobierania z konta ADLS Gen2 Storage, jeśli jeden z przesyłanych plików już istnieje, a następnie AzCopy czasami upaść. Zostanie to naprawione w nadchodzącej poprawce.
* Odłączanie od zasobu dołączonego za pośrednictwem identyfikatora URI sygnatury dostępu Współdzielonego, takiego jak kontener obiektów blob, może spowodować błąd, który uniemożliwia poprawne wyświetlanie innych załączników. Aby obejść ten problem, wystarczy odświeżyć węzeł grupy. Aby uzyskać więcej informacji, zobacz #537.
* Jeśli używasz vs dla komputerów Mac i kiedykolwiek utworzono niestandardową konfigurację usługi AAD, może być nie można zalogować. Aby obejść ten problem, usuń zawartość ~/. IdentityService/AadConfigurations. Jeśli tak nie odblokuje Cię, wyśmiaj tę kwestię.
* Azurite nie zaimplementował jeszcze w pełni wszystkich interfejsów API magazynu. Z tego powodu mogą wystąpić nieoczekiwane błędy lub zachowanie podczas korzystania z Azurite do przechowywania programów deweloperskich.
* W rzadkich przypadkach fokus drzewa może utknąć na szybki dostęp. Aby odkleić fokus, możesz odświeżyć wszystko.
* Przekazywanie z folderu usługi OneDrive nie działa z powodu błędu w NodeJS. Błąd został naprawiony, ale nie został jeszcze zintegrowany z Electronem. Aby obejść ten problem podczas przekazywania lub pobierania z kontenera obiektów blob, można użyć eksperymentalnej funkcji AzCopy.
* Podczas kierowania usługi Azure Stack przekazywanie niektórych plików jako dołączanie obiektów blob może zakończyć się niepowodzeniem.
* Po kliknięciu przycisku "Anuluj" zadania anulowanie tego zadania może chwilę potrwać. Dzieje się tak, ponieważ używamy pracy filtru anulowania opisanego tutaj.
* Jeśli wybierzesz niewłaściwy certyfikat PIN/Karta inteligentna, musisz ponownie uruchomić komputer, aby Eksplorator magazynu zapomniał o tej decyzji.
* Zmiana nazwy obiektów blob (indywidualnie lub wewnątrz kontenera obiektów blob o zmienionej nazwie) nie zachowuje migawek. Wszystkie inne właściwości i metadane obiektów blob, plików i jednostek są zachowywane podczas zmiany nazwy.
* Usługa Azure Stack nie obsługuje następujących funkcji. Próba użycia tych funkcji podczas pracy z zasobami usługi Azure Stack może spowodować nieoczekiwane błędy.
   * Udziały plików
   * Poziomy dostępu
   * Usuwanie nietrwałe
* Powłoka elektronu używana przez Eksploratora pamięci masowej ma problemy z akceleracją sprzętową niektórych procesorów graficznych (procesora graficznego). Jeśli Eksplorator magazynu wyświetla puste (puste) okno główne, możesz spróbować uruchomić Eksploratora magazynu `--disable-gpu` z wiersza polecenia i wyłączyć przyspieszenie procesora GPU, dodając przełącznik:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Dla użytkowników Linuksa, trzeba będzie zainstalować [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Dla użytkowników na Ubuntu 14.04, trzeba będzie upewnić się, GCC jest na bieżąco - można to zrobić, uruchamiając następujące polecenia, a następnie ponowne uruchomienie komputera:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Dla użytkowników na Ubuntu 17.04, trzeba będzie zainstalować GConf - można to zrobić, uruchamiając następujące polecenia, a następnie ponowne uruchomienie komputera:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-161"></a>Wersja 1.6.1
12/18/2018

### <a name="hotfixes"></a>Poprawki
* Ze względu na ograniczenia interfejsu API wszystkie sprawdzanie poprawności ObjectIds w oknie dialogowym Zarządzanie dostępem zostały wyłączone. Sprawdzanie poprawności będzie teraz występować tylko dla sieci UPN użytkowników. [#954](https://www.github.com/Microsoft/AzureStorageExplorer/issues/954)
* W oknie dialogowym Zarządzanie programem ADLS Gen2 nie można zmodyfikować uprawnień dla grupy. Ten problem został rozwiązany. [#958](https://www.github.com/Microsoft/AzureStorageExplorer/issues/958)
* Dodano obsługę przeciągnij i upuść do edytora ADLS Gen2. [#953](https://www.github.com/Microsoft/AzureStorageExplorer/issues/953)
* Właściwość adresu URL w oknie dialogowym właściwości plików i folderów ADLS Gen2 czasami brakowało '/'. Ten problem został rozwiązany. [#960](https://www.github.com/Microsoft/AzureStorageExplorer/issues/960)
* Jeśli uzyskanie bieżących uprawnień do kontenera, pliku lub folderu ADLS Gen2 nie powiedzie się, błąd jest teraz odpowiednio wyświetlany w dzienniku aktywności. [#965](https://www.github.com/Microsoft/AzureStorageExplorer/issues/965)
* Tymczasowa ścieżka utworzona do otwierania plików została skrócona, aby zmniejszyć ryzyko utworzenia ścieżki, która jest dłuższa niż MAX_PATH w systemie Windows. [#93](https://www.github.com/Microsoft/AzureStorageExplorer/issues/93)
* Okno dialogowe Połącz teraz poprawnie pojawia się, gdy nie ma zalogowanych użytkowników i nie zostały dołączone żadne zasoby. [#944](https://www.github.com/Microsoft/AzureStorageExplorer/issues/944)
* W 1.6.0 zapisywanie właściwości dla obiektów blob i plików innych niż HNS zakoduje wartość każdej właściwości. Spowodowało to niepotrzebne kodowanie wartości, które zawierały tylko znaki ASCII. Teraz wartości będą kodowane tylko wtedy, gdy zawierają znaki inne niż ASCII. [#986](https://www.github.com/Microsoft/AzureStorageExplorer/issues/986)
* Przekazywanie folderu do kontenera obiektów blob innych niż HNS zakończy się niepowodzeniem, jeśli został użyty sygnatura dostępu Współdzielonego, a sygnatura dostępu Współdzielonego dostępu Współdzielonego nie miała uprawnień do odczytu. Ten problem został rozwiązany. [#970](https://www.github.com/Microsoft/AzureStorageExplorer/issues/970)
* Anulowanie transferu AzCopy nie zadziałało. Ten problem został rozwiązany. [#943](https://www.github.com/Microsoft/AzureStorageExplorer/issues/943)
* AzCopy nie powiedzie się podczas próby pobrania folderu z kontenera obiektów blob ADLS Gen2, jeśli folder miał spacje w jego nazwie. Ten problem został rozwiązany. [#990](https://www.github.com/Microsoft/AzureStorageExplorer/issues/990)
* Edytor CosmosDB został uszkodzony w 1.6.0. Teraz został naprawiony. [#950](https://www.github.com/Microsoft/AzureStorageExplorer/issues/950)
        
### <a name="new"></a>Nowa

* Teraz można użyć Eksploratora magazynu, aby uzyskać dostęp do danych obiektów blob za pośrednictwem [pliku RBAC](https://go.microsoft.com/fwlink/?linkid=2045904&clcid=0x409). Jeśli użytkownik jest zalogowany i Eksplorator magazynu nie może pobrać kluczy dla konta magazynu, token OAuth będzie używany do uwierzytelniania podczas interakcji z danymi.
* Eksplorator magazynu obsługuje teraz konta magazynu ADLS Gen2. Gdy Eksplorator magazynu wykryje, że hierarchiczna przestrzeń nazw jest włączona dla konta magazynu, obok nazwy konta magazynu zostanie wyświetlony napis "(ADLS Gen2 Preview)". Eksplorator magazynu jest w stanie wykryć, czy hierarchiczna przestrzeń nazw jest włączona po zalogowaniu się lub czy konto magazynu zostało dołączone z nazwą i kluczem. W przypadku kont magazynu ADLS Gen2 można użyć Eksploratora magazynu, aby:
  * Tworzenie i usuwanie kontenerów
  * Zarządzanie właściwościami i uprawnieniami kontenera (po lewej stronie)
  * Wyświetlanie i nawigowanie po danych wewnątrz kontenerów
  * Tworzenie nowych folderów
  * Przesyłanie, pobieranie, zmienianie nazw i usuwanie plików i folderów
  * Zarządzanie właściwościami i uprawnieniami plików i folderów (po prawej stronie).
    
    Inne typowe funkcje obiektów blob, takie jak usuwanie nietrwałe i migawki, nie są obecnie dostępne. Zarządzanie uprawnieniami jest również dostępne tylko po zalogowaniu. Ponadto podczas pracy na koncie magazynu ADLS Gen2 Eksplorator magazynu użyje AzCopy dla wszystkich przekazywania i pobierania oraz domyślnie używa nazwy i poświadczeń klucza dla wszystkich operacji, jeśli są dostępne.
* Po silne opinie użytkowników, break lease może ponownie służyć do przerwania dzierżawy na wiele obiektów blob naraz.

### <a name="known-issues"></a>Znane problemy

* Podczas pobierania z konta ADLS Gen2 Storage, jeśli jeden z przesyłanych plików już istnieje, a następnie AzCopy czasami upaść. Zostanie to naprawione w nadchodzącej poprawce.
* Odłączanie od zasobu dołączonego za pośrednictwem identyfikatora URI sygnatury dostępu Współdzielonego, takiego jak kontener obiektów blob, może spowodować błąd, który uniemożliwia poprawne wyświetlanie innych załączników. Aby obejść ten problem, wystarczy odświeżyć węzeł grupy. Aby uzyskać więcej informacji, zobacz #537.
* Jeśli używasz vs dla komputerów Mac i kiedykolwiek utworzono niestandardową konfigurację usługi AAD, może być nie można zalogować. Aby obejść ten problem, usuń zawartość ~/. IdentityService/AadConfigurations. Jeśli tak nie odblokuje Cię, wyśmiaj tę kwestię.
* Azurite nie zaimplementował jeszcze w pełni wszystkich interfejsów API magazynu. Z tego powodu mogą wystąpić nieoczekiwane błędy lub zachowanie podczas korzystania z Azurite do przechowywania programów deweloperskich.
* W rzadkich przypadkach fokus drzewa może utknąć na szybki dostęp. Aby odkleić fokus, możesz odświeżyć wszystko.
* Przekazywanie z folderu usługi OneDrive nie działa z powodu błędu w NodeJS. Błąd został naprawiony, ale nie został jeszcze zintegrowany z Electronem. Aby obejść ten problem podczas przekazywania lub pobierania z kontenera obiektów blob, można użyć eksperymentalnej funkcji AzCopy.
* Podczas kierowania usługi Azure Stack przekazywanie niektórych plików jako dołączanie obiektów blob może zakończyć się niepowodzeniem.
* Po kliknięciu przycisku "Anuluj" zadania anulowanie tego zadania może chwilę potrwać. Dzieje się tak, ponieważ używamy pracy filtru anulowania opisanego tutaj.
* Jeśli wybierzesz niewłaściwy certyfikat PIN/Karta inteligentna, musisz ponownie uruchomić komputer, aby Eksplorator magazynu zapomniał o tej decyzji.
* Zmiana nazwy obiektów blob (indywidualnie lub wewnątrz kontenera obiektów blob o zmienionej nazwie) nie zachowuje migawek. Wszystkie inne właściwości i metadane obiektów blob, plików i jednostek są zachowywane podczas zmiany nazwy.
* Usługa Azure Stack nie obsługuje następujących funkcji. Próba użycia tych funkcji podczas pracy z zasobami usługi Azure Stack może spowodować nieoczekiwane błędy.
   * Udziały plików
   * Poziomy dostępu
   * Usuwanie nietrwałe
* Powłoka elektronu używana przez Eksploratora pamięci masowej ma problemy z akceleracją sprzętową niektórych procesorów graficznych (procesora graficznego). Jeśli Eksplorator magazynu wyświetla puste (puste) okno główne, możesz spróbować uruchomić Eksploratora magazynu `--disable-gpu` z wiersza polecenia i wyłączyć przyspieszenie procesora GPU, dodając przełącznik:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Dla użytkowników Linuksa, trzeba będzie zainstalować [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Dla użytkowników na Ubuntu 14.04, trzeba będzie upewnić się, GCC jest na bieżąco - można to zrobić, uruchamiając następujące polecenia, a następnie ponowne uruchomienie komputera:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Dla użytkowników na Ubuntu 17.04, trzeba będzie zainstalować GConf - można to zrobić, uruchamiając następujące polecenia, a następnie ponowne uruchomienie komputera:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-160"></a>Wersja 1.6.0
2018-12-05

### <a name="new"></a>Nowa

* Teraz można użyć Eksploratora magazynu, aby uzyskać dostęp do danych obiektów blob za pośrednictwem [pliku RBAC](https://go.microsoft.com/fwlink/?linkid=2045904&clcid=0x409). Jeśli użytkownik jest zalogowany i Eksplorator magazynu nie może pobrać kluczy dla konta magazynu, token OAuth będzie używany do uwierzytelniania podczas interakcji z danymi.
* Eksplorator magazynu obsługuje teraz konta magazynu ADLS Gen2. Gdy Eksplorator magazynu wykryje, że hierarchiczna przestrzeń nazw jest włączona dla konta magazynu, obok nazwy konta magazynu zostanie wyświetlony napis "(ADLS Gen2 Preview)". Eksplorator magazynu jest w stanie wykryć, czy hierarchiczna przestrzeń nazw jest włączona po zalogowaniu się lub czy konto magazynu zostało dołączone z nazwą i kluczem. W przypadku kont magazynu ADLS Gen2 można użyć Eksploratora magazynu, aby:
  * Tworzenie i usuwanie kontenerów
  * Zarządzanie właściwościami i uprawnieniami kontenera (po lewej stronie)
  * Wyświetlanie i nawigowanie po danych wewnątrz kontenerów
  * Tworzenie nowych folderów
  * Przesyłanie, pobieranie, zmienianie nazw i usuwanie plików i folderów
  * Zarządzanie właściwościami i uprawnieniami plików i folderów (po prawej stronie).
    
    Inne typowe funkcje obiektów blob, takie jak usuwanie nietrwałe i migawki, nie są obecnie dostępne. Zarządzanie uprawnieniami jest również dostępne tylko po zalogowaniu. Ponadto podczas pracy na koncie magazynu ADLS Gen2 Eksplorator magazynu użyje AzCopy dla wszystkich przekazywania i pobierania oraz domyślnie używa nazwy i poświadczeń klucza dla wszystkich operacji, jeśli są dostępne.
* Po silne opinie użytkowników, break lease może ponownie służyć do przerwania dzierżawy na wiele obiektów blob naraz.

### <a name="known-issues"></a>Znane problemy

* Podczas pobierania z konta ADLS Gen2 Storage, jeśli jeden z przesyłanych plików już istnieje, a następnie AzCopy czasami upaść. Zostanie to naprawione w nadchodzącej poprawce.
* Odłączanie od zasobu dołączonego za pośrednictwem identyfikatora URI sygnatury dostępu Współdzielonego, takiego jak kontener obiektów blob, może spowodować błąd, który uniemożliwia poprawne wyświetlanie innych załączników. Aby obejść ten problem, wystarczy odświeżyć węzeł grupy. Aby uzyskać więcej informacji, zobacz #537.
* Jeśli używasz vs dla komputerów Mac i kiedykolwiek utworzono niestandardową konfigurację usługi AAD, może być nie można zalogować. Aby obejść ten problem, usuń zawartość ~/. IdentityService/AadConfigurations. Jeśli tak nie odblokuje Cię, wyśmiaj tę kwestię.
* Azurite nie zaimplementował jeszcze w pełni wszystkich interfejsów API magazynu. Z tego powodu mogą wystąpić nieoczekiwane błędy lub zachowanie podczas korzystania z Azurite do przechowywania programów deweloperskich.
* W rzadkich przypadkach fokus drzewa może utknąć na szybki dostęp. Aby odkleić fokus, możesz odświeżyć wszystko.
* Przekazywanie z folderu usługi OneDrive nie działa z powodu błędu w NodeJS. Błąd został naprawiony, ale nie został jeszcze zintegrowany z Electronem. Aby obejść ten problem podczas przekazywania lub pobierania z kontenera obiektów blob, można użyć eksperymentalnej funkcji AzCopy.
* Podczas kierowania usługi Azure Stack przekazywanie niektórych plików jako dołączanie obiektów blob może zakończyć się niepowodzeniem.
* Po kliknięciu przycisku "Anuluj" zadania anulowanie tego zadania może chwilę potrwać. Dzieje się tak, ponieważ używamy pracy filtru anulowania opisanego tutaj.
* Jeśli wybierzesz niewłaściwy certyfikat PIN/Karta inteligentna, musisz ponownie uruchomić komputer, aby Eksplorator magazynu zapomniał o tej decyzji.
* Zmiana nazwy obiektów blob (indywidualnie lub wewnątrz kontenera obiektów blob o zmienionej nazwie) nie zachowuje migawek. Wszystkie inne właściwości i metadane obiektów blob, plików i jednostek są zachowywane podczas zmiany nazwy.
* Usługa Azure Stack nie obsługuje następujących funkcji. Próba użycia tych funkcji podczas pracy z zasobami usługi Azure Stack może spowodować nieoczekiwane błędy.
   * Udziały plików
   * Poziomy dostępu
   * Usuwanie nietrwałe
* Powłoka elektronu używana przez Eksploratora pamięci masowej ma problemy z akceleracją sprzętową niektórych procesorów graficznych (procesora graficznego). Jeśli Eksplorator magazynu wyświetla puste (puste) okno główne, możesz spróbować uruchomić Eksploratora magazynu `--disable-gpu` z wiersza polecenia i wyłączyć przyspieszenie procesora GPU, dodając przełącznik:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Dla użytkowników Linuksa, trzeba będzie zainstalować [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Dla użytkowników na Ubuntu 14.04, trzeba będzie upewnić się, GCC jest na bieżąco - można to zrobić, uruchamiając następujące polecenia, a następnie ponowne uruchomienie komputera:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Dla użytkowników na Ubuntu 17.04, trzeba będzie zainstalować GConf - można to zrobić, uruchamiając następujące polecenia, a następnie ponowne uruchomienie komputera:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-150"></a>Wersja 1.5.0
10/29/2018

### <a name="new"></a>Nowa

* Teraz można użyć [AzCopy v10 (Podgląd)](https://github.com/Azure/azure-storage-azcopy) do przekazywania i pobierania obiektów blob. Aby włączyć tę funkcję, przejdź do menu "Eksperymentalne", a następnie kliknij przycisk "Użyj AzCopy dla ulepszonego przekazywania i pobierania obiektów blob". Po włączeniu AzCopy będzie używany w następujących scenariuszach:
   * Przekazywanie folderów i plików do kontenerów obiektów blob za pośrednictwem paska narzędzi lub przeciągania i upuszczania.
   * Pobieranie folderów i plików za pośrednictwem paska narzędzi lub menu kontekstowego.

* Dodatkowo, podczas korzystania z AzCopy:
   * Można skopiować polecenie AzCopy używane do wykonania transferu do schowka. Po prostu kliknij "Kopiuj polecenie AzCopy do Schowka" w dzienniku aktywności.
   * Po przesłaniu należy ręcznie odświeżyć edytor obiektów blob.
   * Przekazywanie plików do dołączania obiektów blob nie jest obsługiwane, a pliki vhd zostaną przekazane jako obiekty blob stron, a wszystkie inne pliki zostaną przekazane jako blokowe obiekty blob.
   * Błędy i konflikty występujące podczas przekazywania lub pobierania zostaną ujrzę dopiero po zakończeniu przekazywania lub pobierania.

Wreszcie, wsparcie dla korzystania z AzCopy z udziałami plików pojawi się w przyszłości.
* Eksplorator magazynu korzysta teraz z wersji Electron 2.0.11.
* Zerwanie dzierżawy można teraz wykonywać tylko na jednym obiekcie blob naraz. Ponadto należy wprowadzić nazwę obiektu blob, którego dzierżawa jest łamanie. Ta zmiana została wniesienia w celu zmniejszenia prawdopodobieństwa przypadkowego złamania dzierżawy, szczególnie dla maszyn wirtualnych. #394
* Jeśli kiedykolwiek wystąpią problemy z logowaniem, możesz teraz spróbować zresetować uwierzytelnianie. Przejdź do menu "Pomoc" i kliknij przycisk "Reset", aby uzyskać dostęp do tej funkcji. #419

### <a name="fix"></a>Poprawka

* Po silnej opinii użytkowników domyślny węzeł emulatora został ponownie włączony. Nadal można dodać dodatkowe połączenia emulatora za pośrednictwem okna dialogowego Connect, ale jeśli emulator jest skonfigurowany do używania portów domyślnych, można również użyć węzła "Emulator * domyślne porty" w obszarze "Lokalne & dołączane/konta magazynu". #669
* Eksplorator magazynu nie będzie już umożliwiał ustawiania wartości metadanych obiektów blob, które mają interweniujący lub końcowy odstęp. #760
* Przycisk "Zaloguj się" był zawsze włączony na tych samych stronach okna dialogowego Połącz. W razie potrzeby jest wyłączona. #761
* Szybki dostęp nie będzie już wygenerował błędu w konsoli, gdy nie dodano żadnych elementów szybkiego dostępu.

### <a name="known-issues"></a>Znane problemy

* Odłączanie od zasobu dołączonego za pośrednictwem identyfikatora URI sygnatury dostępu Współdzielonego, takiego jak kontener obiektów blob, może spowodować błąd, który uniemożliwia poprawne wyświetlanie innych załączników. Aby obejść ten problem, wystarczy odświeżyć węzeł grupy. Aby uzyskać więcej informacji, zobacz #537.
* Jeśli używasz vs dla komputerów Mac i kiedykolwiek utworzono niestandardową konfigurację usługi AAD, może być nie można zalogować. Aby obejść ten problem, usuń zawartość ~/. IdentityService/AadConfigurations. Jeśli tak nie odblokuje Cię, wyśmiaj tę kwestię.
* Azurite nie zaimplementował jeszcze w pełni wszystkich interfejsów API magazynu. Z tego powodu mogą wystąpić nieoczekiwane błędy lub zachowanie podczas korzystania z Azurite do przechowywania programów deweloperskich.
* W rzadkich przypadkach fokus drzewa może utknąć na szybki dostęp. Aby odkleić fokus, możesz odświeżyć wszystko.
* Przekazywanie z folderu usługi OneDrive nie działa z powodu błędu w NodeJS. Błąd został naprawiony, ale nie został jeszcze zintegrowany z Electronem. Aby obejść ten problem podczas przekazywania lub pobierania z kontenera obiektów blob, można użyć eksperymentalnej funkcji AzCopy.
* Podczas kierowania usługi Azure Stack przekazywanie niektórych plików jako dołączanie obiektów blob może zakończyć się niepowodzeniem.
* Po kliknięciu przycisku "Anuluj" zadania anulowanie tego zadania może chwilę potrwać. Dzieje się tak, ponieważ używamy pracy filtru anulowania opisanego tutaj.
* Jeśli wybierzesz niewłaściwy certyfikat PIN/Karta inteligentna, musisz ponownie uruchomić komputer, aby Eksplorator magazynu zapomniał o tej decyzji.
* Zmiana nazwy obiektów blob (indywidualnie lub wewnątrz kontenera obiektów blob o zmienionej nazwie) nie zachowuje migawek. Wszystkie inne właściwości i metadane obiektów blob, plików i jednostek są zachowywane podczas zmiany nazwy.
* Usługa Azure Stack nie obsługuje następujących funkcji. Próba użycia tych funkcji podczas pracy z zasobami usługi Azure Stack może spowodować nieoczekiwane błędy.
   * Udziały plików
   * Poziomy dostępu
   * Usuwanie nietrwałe
* Powłoka elektronu używana przez Eksploratora pamięci masowej ma problemy z akceleracją sprzętową niektórych procesorów graficznych (procesora graficznego). Jeśli Eksplorator magazynu wyświetla puste (puste) okno główne, możesz spróbować uruchomić Eksploratora magazynu `--disable-gpu` z wiersza polecenia i wyłączyć przyspieszenie procesora GPU, dodając przełącznik:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Dla użytkowników Linuksa, trzeba będzie zainstalować [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Dla użytkowników na Ubuntu 14.04, trzeba będzie upewnić się, GCC jest na bieżąco - można to zrobić, uruchamiając następujące polecenia, a następnie ponowne uruchomienie komputera:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Dla użytkowników na Ubuntu 17.04, trzeba będzie zainstalować GConf - można to zrobić, uruchamiając następujące polecenia, a następnie ponowne uruchomienie komputera:

    ```
    sudo apt-get install libgconf-2-4
    ```


## <a name="version-144"></a>Wersja 1.4.4
10/15/2018

### <a name="hotfixes"></a>Poprawki
* Wersja interfejsu API zarządzania zasobami platformy Azure została wycofana, aby odblokować użytkowników platformy Azure us government. [#696](https://github.com/Microsoft/AzureStorageExplorer/issues/696)
* Ładowanie pokrętła są teraz za pomocą animacji CSS, aby zmniejszyć ilość gpu używane przez Eksploratora magazynu. [#653](https://github.com/Microsoft/AzureStorageExplorer/issues/653)

### <a name="new"></a>Nowa
* Załączniki zasobów zewnętrznych, takie jak dla połączeń i emulatorów sygnatury dostępu Współdzielonego, zostały znacznie ulepszone. Co możesz teraz zrobić:
   * Dostosuj nazwę wyświetlaną dołączonego zasobu. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Dołącz do wielu emulatorów lokalnych przy użyciu różnych portów. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Dodaj dołączone zasoby do szybkiego dostępu. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* Eksplorator magazynu obsługuje teraz usuwanie trędowak. Możesz:
   * Skonfiguruj zasady usuwania nietrwałego, klikając prawym przyciskiem myszy węzeł Kontenery obiektów blob dla konta magazynu.
   * Wyświetl nietrowe usunięte obiekty blob w Edytorze obiektów blob, wybierając "Aktywne i usunięte obiekty blob" na rozwijaniu obok paska nawigacyjnego.
   * Cofanie usuwania nieumarłych usuniętych obiektów blob.

### <a name="fixes"></a>Poprawki
* Akcja "Konfigurowanie ustawień CORS" nie jest już dostępna na kontach magazynu w wersji Premium, ponieważ konta magazynu w wersji Premium nie obsługują usługi CORS. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Obecnie istnieje właściwość podpisu dostępu współdzielonego dla usług dołączonych do sas. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* Akcja "Ustaw domyślną warstwę dostępu" jest teraz dostępna dla kont magazynu obiektów Blob i GPV2, które zostały przypięte do szybkiego dostępu. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* Czasami Eksplorator magazynu nie może wyświetlić kont magazynu klasycznego. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Znane problemy
* Podczas korzystania z emulatorów, takich jak Azure Storage Emulator lub Azurite, należy je nasłuchiwać połączeń na ich portów domyślnych. W przeciwnym razie Eksplorator magazynu nie będzie mógł się z nimi połączyć.
* Jeśli używasz vs dla komputerów Mac i kiedykolwiek utworzono niestandardową konfigurację usługi AAD, może być nie można zalogować. Aby obejść ten problem, usuń zawartość ~/. IdentityService/AadConfigurations. Jeśli to nie odblokuje Cię, proszę skomentować [tę kwestię](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite nie zaimplementował jeszcze w pełni wszystkich interfejsów API magazynu. Z tego powodu mogą wystąpić nieoczekiwane błędy lub zachowanie podczas korzystania z Azurite do przechowywania programów deweloperskich.
* W rzadkich przypadkach fokus drzewa może utknąć na szybki dostęp. Aby odkleić fokus, możesz odświeżyć wszystko.
* Przekazywanie z folderu usługi OneDrive nie działa z powodu błędu w NodeJS. Błąd został naprawiony, ale nie został jeszcze zintegrowany z Electronem.
* Podczas kierowania usługi Azure Stack przekazywanie niektórych plików jako dołączanie obiektów blob może zakończyć się niepowodzeniem.
* Po kliknięciu przycisku "Anuluj" zadania anulowanie tego zadania może chwilę potrwać. Dzieje się tak dlatego, że używamy filtru anulowania opisanego [tutaj.](https://github.com/Azure/azure-storage-node/issues/317)
* Jeśli wybierzesz niewłaściwy certyfikat PIN/Karta inteligentna, musisz ponownie uruchomić komputer, aby Eksplorator magazynu zapomniał o tej decyzji.
* Zmiana nazwy obiektów blob (indywidualnie lub wewnątrz kontenera obiektów blob o zmienionej nazwie) nie zachowuje migawek. Wszystkie inne właściwości i metadane obiektów blob, plików i jednostek są zachowywane podczas zmiany nazwy.
* Mimo że usługa Azure Stack obecnie nie obsługuje udziałów plików, węzeł udziałów plików nadal jest wyświetlany w ramach dołączonego konta usługi Azure Stack storage.
* Powłoka elektronu używana przez Eksploratora pamięci masowej ma problemy z akceleracją sprzętową niektórych procesorów graficznych (procesora graficznego). Jeśli Eksplorator magazynu wyświetla puste (puste) okno główne, możesz spróbować uruchomić Eksploratora magazynu `--disable-gpu` z wiersza polecenia i wyłączyć przyspieszenie procesora GPU, dodając przełącznik:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Dla użytkowników Linuksa, trzeba będzie zainstalować [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Dla użytkowników na Ubuntu 14.04, trzeba będzie upewnić się, GCC jest na bieżąco - można to zrobić, uruchamiając następujące polecenia, a następnie ponowne uruchomienie komputera:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Dla użytkowników na Ubuntu 17.04, trzeba będzie zainstalować GConf - można to zrobić, uruchamiając następujące polecenia, a następnie ponowne uruchomienie komputera:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-143"></a>Wersja 1.4.3
10/11/2018

### <a name="hotfixes"></a>Poprawki
* Wersja interfejsu API zarządzania zasobami platformy Azure została wycofana, aby odblokować użytkowników platformy Azure us government. [#696](https://github.com/Microsoft/AzureStorageExplorer/issues/696)
* Ładowanie pokrętła są teraz za pomocą animacji CSS, aby zmniejszyć ilość gpu używane przez Eksploratora magazynu. [#653](https://github.com/Microsoft/AzureStorageExplorer/issues/653)

### <a name="new"></a>Nowa
* Załączniki zasobów zewnętrznych, takie jak dla połączeń i emulatorów sygnatury dostępu Współdzielonego, zostały znacznie ulepszone. Co możesz teraz zrobić:
   * Dostosuj nazwę wyświetlaną dołączonego zasobu. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Dołącz do wielu emulatorów lokalnych przy użyciu różnych portów. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Dodaj dołączone zasoby do szybkiego dostępu. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* Eksplorator magazynu obsługuje teraz usuwanie trędowak. Możesz:
   * Skonfiguruj zasady usuwania nietrwałego, klikając prawym przyciskiem myszy węzeł Kontenery obiektów blob dla konta magazynu.
   * Wyświetl nietrowe usunięte obiekty blob w Edytorze obiektów blob, wybierając "Aktywne i usunięte obiekty blob" na rozwijaniu obok paska nawigacyjnego.
   * Cofanie usuwania nieumarłych usuniętych obiektów blob.

### <a name="fixes"></a>Poprawki
* Akcja "Konfigurowanie ustawień CORS" nie jest już dostępna na kontach magazynu w wersji Premium, ponieważ konta magazynu w wersji Premium nie obsługują usługi CORS. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Obecnie istnieje właściwość podpisu dostępu współdzielonego dla usług dołączonych do sas. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* Akcja "Ustaw domyślną warstwę dostępu" jest teraz dostępna dla kont magazynu obiektów Blob i GPV2, które zostały przypięte do szybkiego dostępu. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* Czasami Eksplorator magazynu nie może wyświetlić kont magazynu klasycznego. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Znane problemy
* Podczas korzystania z emulatorów, takich jak Azure Storage Emulator lub Azurite, należy je nasłuchiwać połączeń na ich portów domyślnych. W przeciwnym razie Eksplorator magazynu nie będzie mógł się z nimi połączyć.
* Jeśli używasz vs dla komputerów Mac i kiedykolwiek utworzono niestandardową konfigurację usługi AAD, może być nie można zalogować. Aby obejść ten problem, usuń zawartość ~/. IdentityService/AadConfigurations. Jeśli to nie odblokuje Cię, proszę skomentować [tę kwestię](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite nie zaimplementował jeszcze w pełni wszystkich interfejsów API magazynu. Z tego powodu mogą wystąpić nieoczekiwane błędy lub zachowanie podczas korzystania z Azurite do przechowywania programów deweloperskich.
* W rzadkich przypadkach fokus drzewa może utknąć na szybki dostęp. Aby odkleić fokus, możesz odświeżyć wszystko.
* Przekazywanie z folderu usługi OneDrive nie działa z powodu błędu w NodeJS. Błąd został naprawiony, ale nie został jeszcze zintegrowany z Electronem.
* Podczas kierowania usługi Azure Stack przekazywanie niektórych plików jako dołączanie obiektów blob może zakończyć się niepowodzeniem.
* Po kliknięciu przycisku "Anuluj" zadania anulowanie tego zadania może chwilę potrwać. Dzieje się tak dlatego, że używamy filtru anulowania opisanego [tutaj.](https://github.com/Azure/azure-storage-node/issues/317)
* Jeśli wybierzesz niewłaściwy certyfikat PIN/Karta inteligentna, musisz ponownie uruchomić komputer, aby Eksplorator magazynu zapomniał o tej decyzji.
* Zmiana nazwy obiektów blob (indywidualnie lub wewnątrz kontenera obiektów blob o zmienionej nazwie) nie zachowuje migawek. Wszystkie inne właściwości i metadane obiektów blob, plików i jednostek są zachowywane podczas zmiany nazwy.
* Mimo że usługa Azure Stack obecnie nie obsługuje udziałów plików, węzeł udziałów plików nadal jest wyświetlany w ramach dołączonego konta usługi Azure Stack storage.
* Powłoka elektronu używana przez Eksploratora pamięci masowej ma problemy z akceleracją sprzętową niektórych procesorów graficznych (procesora graficznego). Jeśli Eksplorator magazynu wyświetla puste (puste) okno główne, możesz spróbować uruchomić Eksploratora magazynu `--disable-gpu` z wiersza polecenia i wyłączyć przyspieszenie procesora GPU, dodając przełącznik:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Dla użytkowników Linuksa, trzeba będzie zainstalować [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Dla użytkowników na Ubuntu 14.04, trzeba będzie upewnić się, GCC jest na bieżąco - można to zrobić, uruchamiając następujące polecenia, a następnie ponowne uruchomienie komputera:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Dla użytkowników na Ubuntu 17.04, trzeba będzie zainstalować GConf - można to zrobić, uruchamiając następujące polecenia, a następnie ponowne uruchomienie komputera:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-142"></a>Wersja 1.4.2
09/24/2018

### <a name="hotfixes"></a>Poprawki
* Zaktualizuj wersję interfejsu API zarządzania zasobami platformy Azure do wersji 2018-07-01, aby dodać obsługę nowych rodzajów kont usługi Azure Storage. [#652](https://github.com/Microsoft/AzureStorageExplorer/issues/652)

### <a name="new"></a>Nowa
* Załączniki zasobów zewnętrznych, takie jak dla połączeń i emulatorów sygnatury dostępu Współdzielonego, zostały znacznie ulepszone. Co możesz teraz zrobić:
   * Dostosuj nazwę wyświetlaną dołączonego zasobu. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Dołącz do wielu emulatorów lokalnych przy użyciu różnych portów. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Dodaj dołączone zasoby do szybkiego dostępu. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* Eksplorator magazynu obsługuje teraz usuwanie trędowak. Możesz:
   * Skonfiguruj zasady usuwania nietrwałego, klikając prawym przyciskiem myszy węzeł Kontenery obiektów blob dla konta magazynu.
   * Wyświetl nietrowe usunięte obiekty blob w Edytorze obiektów blob, wybierając "Aktywne i usunięte obiekty blob" na rozwijaniu obok paska nawigacyjnego.
   * Cofanie usuwania nieumarłych usuniętych obiektów blob.

### <a name="fixes"></a>Poprawki
* Akcja "Konfigurowanie ustawień CORS" nie jest już dostępna na kontach magazynu w wersji Premium, ponieważ konta magazynu w wersji Premium nie obsługują usługi CORS. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Obecnie istnieje właściwość podpisu dostępu współdzielonego dla usług dołączonych do sas. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* Akcja "Ustaw domyślną warstwę dostępu" jest teraz dostępna dla kont magazynu obiektów Blob i GPV2, które zostały przypięte do szybkiego dostępu. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* Czasami Eksplorator magazynu nie może wyświetlić kont magazynu klasycznego. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Znane problemy
* Podczas korzystania z emulatorów, takich jak Azure Storage Emulator lub Azurite, należy je nasłuchiwać połączeń na ich portów domyślnych. W przeciwnym razie Eksplorator magazynu nie będzie mógł się z nimi połączyć.
* Jeśli używasz vs dla komputerów Mac i kiedykolwiek utworzono niestandardową konfigurację usługi AAD, może być nie można zalogować. Aby obejść ten problem, usuń zawartość ~/. IdentityService/AadConfigurations. Jeśli to nie odblokuje Cię, proszę skomentować [tę kwestię](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite nie zaimplementował jeszcze w pełni wszystkich interfejsów API magazynu. Z tego powodu mogą wystąpić nieoczekiwane błędy lub zachowanie podczas korzystania z Azurite do przechowywania programów deweloperskich.
* W rzadkich przypadkach fokus drzewa może utknąć na szybki dostęp. Aby odkleić fokus, możesz odświeżyć wszystko.
* Przekazywanie z folderu usługi OneDrive nie działa z powodu błędu w NodeJS. Błąd został naprawiony, ale nie został jeszcze zintegrowany z Electronem.
* Podczas kierowania usługi Azure Stack przekazywanie niektórych plików jako dołączanie obiektów blob może zakończyć się niepowodzeniem.
* Po kliknięciu przycisku "Anuluj" zadania anulowanie tego zadania może chwilę potrwać. Dzieje się tak dlatego, że używamy filtru anulowania opisanego [tutaj.](https://github.com/Azure/azure-storage-node/issues/317)
* Jeśli wybierzesz niewłaściwy certyfikat PIN/Karta inteligentna, musisz ponownie uruchomić komputer, aby Eksplorator magazynu zapomniał o tej decyzji.
* Zmiana nazwy obiektów blob (indywidualnie lub wewnątrz kontenera obiektów blob o zmienionej nazwie) nie zachowuje migawek. Wszystkie inne właściwości i metadane obiektów blob, plików i jednostek są zachowywane podczas zmiany nazwy.
* Mimo że usługa Azure Stack obecnie nie obsługuje udziałów plików, węzeł udziałów plików nadal jest wyświetlany w ramach dołączonego konta usługi Azure Stack storage.
* Powłoka elektronu używana przez Eksploratora pamięci masowej ma problemy z akceleracją sprzętową niektórych procesorów graficznych (procesora graficznego). Jeśli Eksplorator magazynu wyświetla puste (puste) okno główne, możesz spróbować uruchomić Eksploratora magazynu `--disable-gpu` z wiersza polecenia i wyłączyć przyspieszenie procesora GPU, dodając przełącznik:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Dla użytkowników Linuksa, trzeba będzie zainstalować [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Dla użytkowników na Ubuntu 14.04, trzeba będzie upewnić się, GCC jest na bieżąco - można to zrobić, uruchamiając następujące polecenia, a następnie ponowne uruchomienie komputera:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Dla użytkowników na Ubuntu 17.04, trzeba będzie zainstalować GConf - można to zrobić, uruchamiając następujące polecenia, a następnie ponowne uruchomienie komputera:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-141"></a>Wersja 1.4.1
08/28/2018

### <a name="hotfixes"></a>Poprawki
* Przy pierwszym uruchomieniu Eksplorator magazynu nie może wygenerować klucza używanego do szyfrowania poufnych danych. Spowodowałoby to problemy podczas korzystania z szybkiego dostępu i dołączania zasobów. [#535](https://github.com/Microsoft/AzureStorageExplorer/issues/535)
* Jeśli twoje konto nie wymaga usługi MFA dla dzierżawy domowej, ale nie dla niektórych innych dzierżaw, Eksplorator magazynu nie będzie w stanie wyświetlić subskrypcji. Teraz, po zalogowaniu się przy użyciu takiego konta, Eksplorator magazynu poprosi o ponowne wniesienie poświadczeń i wykonanie usługi MFA. [#74](https://github.com/Microsoft/AzureStorageExplorer/issues/74)
* Eksplorator magazynu nie może dołączyć zasobów z platformy Azure w Niemczech i usłudze Azure US Government. [#572](https://github.com/Microsoft/AzureStorageExplorer/issues/572)
* Jeśli zalogujesz się na dwa konta, które miały ten sam adres e-mail, Eksplorator magazynu czasami nie wyświetla zasobów w widoku drzewa. [#580](https://github.com/Microsoft/AzureStorageExplorer/issues/580)
* Na wolniejszych komputerach z systemem Windows ekran powitalny czasami zajmuje dużo czasu. [#586](https://github.com/Microsoft/AzureStorageExplorer/issues/586)
* Okno dialogowe connect pojawi się nawet wtedy, gdy były dołączone konta lub usługi. [#588](https://github.com/Microsoft/AzureStorageExplorer/issues/588)

### <a name="new"></a>Nowa
* Załączniki zasobów zewnętrznych, takie jak dla połączeń i emulatorów sygnatury dostępu Współdzielonego, zostały znacznie ulepszone. Co możesz teraz zrobić:
   * Dostosuj nazwę wyświetlaną dołączonego zasobu. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Dołącz do wielu emulatorów lokalnych przy użyciu różnych portów. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Dodaj dołączone zasoby do szybkiego dostępu. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* Eksplorator magazynu obsługuje teraz usuwanie trędowak. Możesz:
   * Skonfiguruj zasady usuwania nietrwałego, klikając prawym przyciskiem myszy węzeł Kontenery obiektów blob dla konta magazynu.
   * Wyświetl nietrowe usunięte obiekty blob w Edytorze obiektów blob, wybierając "Aktywne i usunięte obiekty blob" na rozwijaniu obok paska nawigacyjnego.
   * Cofanie usuwania nieumarłych usuniętych obiektów blob.

### <a name="fixes"></a>Poprawki
* Akcja "Konfigurowanie ustawień CORS" nie jest już dostępna na kontach magazynu w wersji Premium, ponieważ konta magazynu w wersji Premium nie obsługują usługi CORS. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Obecnie istnieje właściwość podpisu dostępu współdzielonego dla usług dołączonych do sas. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* Akcja "Ustaw domyślną warstwę dostępu" jest teraz dostępna dla kont magazynu obiektów Blob i GPV2, które zostały przypięte do szybkiego dostępu. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* Czasami Eksplorator magazynu nie może wyświetlić kont magazynu klasycznego. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Znane problemy
* Podczas korzystania z emulatorów, takich jak Azure Storage Emulator lub Azurite, należy je nasłuchiwać połączeń na ich portów domyślnych. W przeciwnym razie Eksplorator magazynu nie będzie mógł się z nimi połączyć.
* Jeśli używasz vs dla komputerów Mac i kiedykolwiek utworzono niestandardową konfigurację usługi AAD, może być nie można zalogować. Aby obejść ten problem, usuń zawartość ~/. IdentityService/AadConfigurations. Jeśli to nie odblokuje Cię, proszę skomentować [tę kwestię](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite nie zaimplementował jeszcze w pełni wszystkich interfejsów API magazynu. Z tego powodu mogą wystąpić nieoczekiwane błędy lub zachowanie podczas korzystania z Azurite do przechowywania programów deweloperskich.
* W rzadkich przypadkach fokus drzewa może utknąć na szybki dostęp. Aby odkleić fokus, możesz odświeżyć wszystko.
* Przekazywanie z folderu usługi OneDrive nie działa z powodu błędu w NodeJS. Błąd został naprawiony, ale nie został jeszcze zintegrowany z Electronem.
* Podczas kierowania usługi Azure Stack przekazywanie niektórych plików jako dołączanie obiektów blob może zakończyć się niepowodzeniem.
* Po kliknięciu przycisku "Anuluj" zadania anulowanie tego zadania może chwilę potrwać. Dzieje się tak dlatego, że używamy filtru anulowania opisanego [tutaj.](https://github.com/Azure/azure-storage-node/issues/317)
* Jeśli wybierzesz niewłaściwy certyfikat PIN/Karta inteligentna, musisz ponownie uruchomić komputer, aby Eksplorator magazynu zapomniał o tej decyzji.
* Zmiana nazwy obiektów blob (indywidualnie lub wewnątrz kontenera obiektów blob o zmienionej nazwie) nie zachowuje migawek. Wszystkie inne właściwości i metadane obiektów blob, plików i jednostek są zachowywane podczas zmiany nazwy.
* Mimo że usługa Azure Stack obecnie nie obsługuje udziałów plików, węzeł udziałów plików nadal jest wyświetlany w ramach dołączonego konta usługi Azure Stack storage.
* Powłoka elektronu używana przez Eksploratora pamięci masowej ma problemy z akceleracją sprzętową niektórych procesorów graficznych (procesora graficznego). Jeśli Eksplorator magazynu wyświetla puste (puste) okno główne, możesz spróbować uruchomić Eksploratora magazynu `--disable-gpu` z wiersza polecenia i wyłączyć przyspieszenie procesora GPU, dodając przełącznik:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Dla użytkowników Linuksa, trzeba będzie zainstalować [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Dla użytkowników na Ubuntu 14.04, trzeba będzie upewnić się, GCC jest na bieżąco - można to zrobić, uruchamiając następujące polecenia, a następnie ponowne uruchomienie komputera:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Dla użytkowników na Ubuntu 17.04, trzeba będzie zainstalować GConf - można to zrobić, uruchamiając następujące polecenia, a następnie ponowne uruchomienie komputera:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-130"></a>Wersja 1.3.0
07/09/2018

### <a name="new"></a>Nowa
* Dostęp do kontenerów $web używanych przez statyczne witryny sieci Web jest teraz obsługiwany. Dzięki temu można łatwo przesyłać pliki i foldery używane przez witrynę oraz zarządzać nimi. [#223](https://github.com/Microsoft/AzureStorageExplorer/issues/223)
* Pasek aplikacji w systemie macOS został zreorganizowany. Zmiany obejmują menu Plik, niektóre zmiany klawiszy skrótów i kilka nowych poleceń w menu aplikacji. [#99](https://github.com/Microsoft/AzureStorageExplorer/issues/99)
* Punkt końcowy urzędu logowania do usługi Azure US Government został zmieniony nahttps://login.microsoftonline.us/
* Ułatwienia dostępu: Gdy czytnik ekranu jest aktywny, nawigacja za pomocą klawiatury działa teraz z tabelami używanymi do wyświetlania elementów po prawej stronie. Za pomocą klawiszy strzałek można poruszać się po wierszach i kolumnach, klawisz Enter do wywoływania akcji domyślnych, klawisz menu kontekstowego, aby otworzyć menu kontekstowe elementu, oraz Shift lub Control do wielokrotnego zaznaczania. [#103](https://github.com/Microsoft/AzureStorageExplorer/issues/103)

### <a name="fixes"></a>Poprawki
*  Na niektórych maszynach procesy dziecięce trwały długo. Kiedy to nastąpi, pojawi się błąd "proces podrzędny nie można uruchomić w odpowiednim czasie". Czas przeznaczony na rozpoczęcie procesu podrzędnego został teraz wydłużony z 20 do 90 sekund. Jeśli ten problem nadal dotyczy, prosimy o komentarz w sprawie powiązanego problemu z witryną GitHub. [#281](https://github.com/Microsoft/AzureStorageExplorer/issues/281)
* Podczas korzystania z sygnatury dostępu Współdzielonego, który nie ma uprawnień do odczytu, nie było możliwe przekazanie dużego obiektu blob. Logika przekazywania została zmodyfikowana do pracy w tym scenariuszu. [#305](https://github.com/Microsoft/AzureStorageExplorer/issues/305)
* Ustawienie poziomu dostępu publicznego dla kontenera spowoduje usunięcie wszystkich zasad dostępu i odwrotnie. Teraz poziom dostępu publicznego i zasady dostępu są zachowywane podczas ustawiania jednego z dwóch. [#197](https://github.com/Microsoft/AzureStorageExplorer/issues/197)
* "AccessTierChangeTime" został obcięty w oknie dialogowym Właściwości. Ten problem został rozwiązany. [#145](https://github.com/Microsoft/AzureStorageExplorer/issues/145)
* W oknie dialogowym Tworzenie nowego katalogu brakowało prefiksu "Microsoft Azure Storage Explorer -". Ten problem został rozwiązany. [#299](https://github.com/Microsoft/AzureStorageExplorer/issues/299)
* Ułatwienia dostępu: Okno dialogowe Dodaj encję było trudne do nawigowania podczas korzystania z funkcji VoiceOver. Wprowadzono ulepszenia. [#206](https://github.com/Microsoft/AzureStorageExplorer/issues/206)
* Ułatwienia dostępu: kolor tła przycisku zwijanie/rozwijanie w okienku Akcje i właściwości był niezgodny z podobnymi kontrolkami interfejsu użytkownika w motywie Czarny kontrast wysoki kontrast. Kolor został zmieniony. [#123](https://github.com/Microsoft/AzureStorageExplorer/issues/123)
* Dostępność: W kompozycji Czarny kontrast wysoki kontrast styl ustawiania ostrości dla przycisku "X" w oknie dialogowym Właściwości nie był widoczny. Ten problem został rozwiązany. [#243](https://github.com/Microsoft/AzureStorageExplorer/issues/243)
* Dostępność: na kartach Akcje i właściwości brakowało kilku wartości aria, co spowodowało środowisko czytnika ekranu podrzędnego. Brakujące wartości aria zostały dodane. [#316](https://github.com/Microsoft/AzureStorageExplorer/issues/316)
* Dostępność: Zwinięte węzły drzewa po lewej stronie nie otrzymały wartości false rozszerzonej aria. Ten problem został rozwiązany. [#352](https://github.com/Microsoft/AzureStorageExplorer/issues/352)

### <a name="known-issues"></a>Znane problemy
* Odłączanie od zasobu dołączonego za pośrednictwem identyfikatora URI sygnatury dostępu Współdzielonego, takiego jak kontener obiektów blob, może spowodować błąd, który uniemożliwia poprawne wyświetlanie innych załączników. Aby obejść ten problem, wystarczy odświeżyć węzeł grupy. Zobacz [ten problem,](https://github.com/Microsoft/AzureStorageExplorer/issues/537) aby uzyskać więcej informacji.
* Jeśli używasz vs dla komputerów Mac i kiedykolwiek utworzono niestandardową konfigurację usługi AAD, może być nie można zalogować. Aby obejść ten problem, usuń zawartość ~/. IdentityService/AadConfigurations. Jeśli to nie odblokuje Cię, proszę skomentować [tę kwestię](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite nie zaimplementował jeszcze w pełni wszystkich interfejsów API magazynu. Z tego powodu mogą wystąpić nieoczekiwane błędy lub zachowanie podczas korzystania z Azurite do przechowywania programów deweloperskich.
* W rzadkich przypadkach fokus drzewa może utknąć na szybki dostęp. Aby odkleić fokus, możesz odświeżyć wszystko.
* Przekazywanie z folderu usługi OneDrive nie działa z powodu błędu w NodeJS. Błąd został naprawiony, ale nie został jeszcze zintegrowany z Electronem.
* Podczas kierowania usługi Azure Stack przekazywanie niektórych plików jako dołączanie obiektów blob może zakończyć się niepowodzeniem.
* Po kliknięciu przycisku "Anuluj" zadania anulowanie tego zadania może chwilę potrwać. Dzieje się tak dlatego, że używamy filtru anulowania opisanego [tutaj.](https://github.com/Azure/azure-storage-node/issues/317)
* Jeśli wybierzesz niewłaściwy certyfikat PIN/Karta inteligentna, musisz ponownie uruchomić komputer, aby Eksplorator magazynu zapomniał o tej decyzji.
* Zmiana nazwy obiektów blob (indywidualnie lub wewnątrz kontenera obiektów blob o zmienionej nazwie) nie zachowuje migawek. Wszystkie inne właściwości i metadane obiektów blob, plików i jednostek są zachowywane podczas zmiany nazwy.
* Usługa Azure Stack nie obsługuje następujących funkcji, a próba użycia ich podczas pracy z usługą Azure Stack może spowodować nieoczekiwane błędy:
   * Udziały plików
   * Poziomy dostępu
   * Usuwanie nietrwałe
* Powłoka elektronu używana przez Eksploratora pamięci masowej ma problemy z akceleracją sprzętową niektórych procesorów graficznych (procesora graficznego). Jeśli Eksplorator magazynu wyświetla puste (puste) okno główne, możesz spróbować uruchomić Eksploratora magazynu `--disable-gpu` z wiersza polecenia i wyłączyć przyspieszenie procesora GPU, dodając przełącznik:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Dla użytkowników Linuksa, trzeba będzie zainstalować [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Dla użytkowników na Ubuntu 14.04, trzeba będzie upewnić się, GCC jest na bieżąco - można to zrobić, uruchamiając następujące polecenia, a następnie ponowne uruchomienie komputera:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Dla użytkowników na Ubuntu 17.04, trzeba będzie zainstalować GConf - można to zrobić, uruchamiając następujące polecenia, a następnie ponowne uruchomienie komputera:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-120"></a>Wersja 1.2.0
06/12/2018

### <a name="new"></a>Nowa
* Jeśli Eksplorator magazynu nie może załadować subskrypcji tylko z podzbioru dzierżawców, wszystkie pomyślnie załadowane subskrypcje będą wyświetlane wraz z komunikatem o błędzie specjalnie dla dzierżawców, które nie powiodły się. [#159](https://github.com/Microsoft/AzureStorageExplorer/issues/159)
* W systemie Windows, gdy aktualizacja jest dostępna, można teraz wybrać opcję "Aktualizuj przy zamykaniu". Po wybraniu tej opcji instalator aktualizacji zostanie uruchomiony po zamknięciu Eksploratora magazynu. [#21](https://github.com/Microsoft/AzureStorageExplorer/issues/21)
* Przywróć migawkę został dodany do menu kontekstowego edytora udziałów plików podczas przeglądania migawki udziału plików. [#131](https://github.com/Microsoft/AzureStorageExplorer/issues/131)
* Przycisk Wyczyść kolejkę jest teraz zawsze włączony. [#135](https://github.com/Microsoft/AzureStorageExplorer/issues/135)
* Obsługa logowania się do usługi ADFS Azure Stack została ponownie włączona. Usługa Azure Stack w wersji 1804 lub wyższej jest wymagana. [#150](https://github.com/Microsoft/AzureStorageExplorer/issues/150)

### <a name="fixes"></a>Poprawki
* Jeśli oglądano migawki dla udziału plików, którego nazwa była prefiksem innego udziału plików na tym samym koncie magazynu, migawki dla innego udziału plików również zostaną wyświetlone. Ten problem został rozwiązany. [#255](https://github.com/Microsoft/AzureStorageExplorer/issues/255)
* Po dołączeniu za pośrednictwem sygnatury dostępu Współdzielonego przywrócenie pliku z migawki udziału plików spowodowałoby błąd. Ten problem został rozwiązany. [#211](https://github.com/Microsoft/AzureStorageExplorer/issues/211)
* Podczas wyświetlania migawek dla obiektu blob akcja Promuj migawkę została włączona po wybraniu podstawowego obiektu blob i pojedynczej migawki. Akcja jest teraz włączona tylko wtedy, gdy wybrano pojedynczą migawkę. [#230](https://github.com/Microsoft/AzureStorageExplorer/issues/230)
* Jeśli jedno zadanie (na przykład pobieranie obiektu blob) zostało uruchomione, a później nie powiodło się, nie będzie automatycznie ponawiać próby, dopóki nie uruchomisz innego zadania tego samego typu. Wszystkie zadania powinny teraz automatycznie ponowić próbę, niezależnie od liczby zadań, które zostały umieszczone w kolejce.
* Edytory otwarte dla nowo utworzonych kontenerów obiektów blob na kontach magazynu obiektów GPV2 i obiektów blob nie miały kolumny warstwy dostępu. Ten problem został rozwiązany. [#109](https://github.com/Microsoft/AzureStorageExplorer/issues/109)
* Kolumna warstwy dostępu czasami nie pojawia się, gdy konto magazynu lub kontener obiektów blob został dołączony za pośrednictwem sygnatury dostępu Współdzielonego. Kolumna będzie teraz zawsze wyświetlana, ale z pustą wartością, jeśli nie ma zestawu warstwy dostępu. [#160](https://github.com/Microsoft/AzureStorageExplorer/issues/160)
* Ustawienie warstwy dostępu nowo przekazanego obiektu blob bloku zostało wyłączone. Ten problem został rozwiązany. [#171](https://github.com/Microsoft/AzureStorageExplorer/issues/171)
* Jeśli przycisk "Keep Tab Open" został wywołany za pomocą klawiatury, fokus klawiatury zostanie utracony. Teraz fokus przejdzie na kartę, która była otwarta. [#163](https://github.com/Microsoft/AzureStorageExplorer/issues/163)
* W przypadku kwerendy w Konstruktorze zapytań VoiceOver nie podaje użytecznego opisu bieżącego operatora. Jest teraz bardziej opisowy. [#207](https://github.com/Microsoft/AzureStorageExplorer/issues/207)
* Łącza do stron internetowych dla różnych redaktorów nie były opisowe. Zostały one zmienione na bardziej opisowe. [#205](https://github.com/Microsoft/AzureStorageExplorer/issues/205)
* W oknie dialogowym Dodaj encję VoiceOver nie ogłaszał, w jakiej kolumnie był częścią elementu wejściowego. Nazwa bieżącej kolumny jest teraz uwzględniona w opisie elementu. [#206](https://github.com/Microsoft/AzureStorageExplorer/issues/206)
* Przyciski radiowe i pola wyboru nie miały widocznego obramowania podczas ustawiania ostrości. Ten problem został rozwiązany. [#237](https://github.com/Microsoft/AzureStorageExplorer/issues/237)

### <a name="known-issues"></a>Znane problemy
* Podczas korzystania z emulatorów, takich jak Azure Storage Emulator lub Azurite, należy je nasłuchiwać połączeń na ich portów domyślnych. W przeciwnym razie Eksplorator magazynu nie będzie mógł się z nimi połączyć.
* Jeśli używasz vs dla komputerów Mac i kiedykolwiek utworzono niestandardową konfigurację usługi AAD, może być nie można zalogować. Aby obejść ten problem, usuń zawartość ~/. IdentityService/AadConfigurations. Jeśli to nie odblokuje Cię, proszę skomentować [tę kwestię](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite nie zaimplementował jeszcze w pełni wszystkich interfejsów API magazynu. Z tego powodu mogą wystąpić nieoczekiwane błędy lub zachowanie podczas korzystania z Azurite do przechowywania programów deweloperskich.
* W rzadkich przypadkach fokus drzewa może utknąć na szybki dostęp. Aby odkleić fokus, możesz odświeżyć wszystko.
* Przekazywanie z folderu usługi OneDrive nie działa z powodu błędu w NodeJS. Błąd został naprawiony, ale nie został jeszcze zintegrowany z Electronem.
* Podczas kierowania usługi Azure Stack przekazywanie niektórych plików jako dołączanie obiektów blob może zakończyć się niepowodzeniem.
* Po kliknięciu przycisku "Anuluj" zadania anulowanie tego zadania może chwilę potrwać. Dzieje się tak dlatego, że używamy filtru anulowania opisanego [tutaj.](https://github.com/Azure/azure-storage-node/issues/317)
* Jeśli wybierzesz niewłaściwy certyfikat PIN/Karta inteligentna, musisz ponownie uruchomić komputer, aby Eksplorator magazynu zapomniał o tej decyzji.
* Zmiana nazwy obiektów blob (indywidualnie lub wewnątrz kontenera obiektów blob o zmienionej nazwie) nie zachowuje migawek. Wszystkie inne właściwości i metadane obiektów blob, plików i jednostek są zachowywane podczas zmiany nazwy.
* Mimo że usługa Azure Stack obecnie nie obsługuje udziałów plików, węzeł udziałów plików nadal jest wyświetlany w ramach dołączonego konta usługi Azure Stack storage.
* Powłoka elektronu używana przez Eksploratora pamięci masowej ma problemy z akceleracją sprzętową niektórych procesorów graficznych (procesora graficznego). Jeśli Eksplorator magazynu wyświetla puste (puste) okno główne, możesz spróbować uruchomić Eksploratora magazynu `--disable-gpu` z wiersza polecenia i wyłączyć przyspieszenie procesora GPU, dodając przełącznik:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Dla użytkowników Linuksa, trzeba będzie zainstalować [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Dla użytkowników na Ubuntu 14.04, trzeba będzie upewnić się, GCC jest na bieżąco - można to zrobić, uruchamiając następujące polecenia, a następnie ponowne uruchomienie komputera:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Dla użytkowników na Ubuntu 17.04, trzeba będzie zainstalować GConf - można to zrobić, uruchamiając następujące polecenia, a następnie ponowne uruchomienie komputera:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-110"></a>Wersja 1.1.0
05/09/2018

### <a name="new"></a>Nowa
* Eksplorator magazynu obsługuje teraz korzystanie z Azurite. Uwaga: połączenie z Azurite jest zakodowane na stałe z domyślnymi punktami końcowymi rozwoju.
* Eksplorator magazynu obsługuje teraz warstwy dostępu dla kont tylko obiektów blob i gpv2 magazynu. Dowiedz się więcej o warstwach dostępu [tutaj](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers).
* Czas rozpoczęcia nie jest już wymagane podczas generowania sygnatury dostępu Współdzielonego.

### <a name="fixes"></a>Poprawki
* Pobieranie subskrypcji dla kont instytucji rządowych i samorządowych USA zostało przerwane. Ten problem został rozwiązany. [#61](https://github.com/Microsoft/AzureStorageExplorer/issues/61)
* Czas wygaśnięcia zasad dostępu nie został poprawnie zapisany. Ten problem został rozwiązany. [#50](https://github.com/Microsoft/AzureStorageExplorer/issues/50)
* Podczas generowania adresu URL sygnatury dostępu Współdzielonego dla elementu w kontenerze nazwa elementu nie była dołączana do adresu URL. Ten problem został rozwiązany. [#44](https://github.com/Microsoft/AzureStorageExplorer/issues/44)
* Podczas tworzenia sygnatury dostępu Współdzielonego, czas wygaśnięcia, które są w przeszłości czasami będzie wartością domyślną. Wynikało to z Eksploratora magazynu przy użyciu ostatnio używanego czasu rozpoczęcia i wygaśnięcia jako wartości domyślnych. Teraz za każdym razem, gdy otwierasz okno dialogowe Sygnatury dostępu Współdzielonego, generowany jest nowy zestaw wartości domyślnych. [#35](https://github.com/Microsoft/AzureStorageExplorer/issues/35)
* Podczas kopiowania między kontami magazynu generowany jest 24-godzinny sygnatury dostępu Współdzielonego. Jeśli kopia trwała dłużej niż 24 godziny, kopia zakończy się niepowodzeniem. Zwiększyliśmy sas do ostatniego 1 tygodnia, aby zmniejszyć ryzyko kopii nie z powodu wygasłego SAS. [#62](https://github.com/Microsoft/AzureStorageExplorer/issues/62)
* W przypadku niektórych działań kliknięcie na "Anuluj" nie zawsze będzie działać. Ten problem został rozwiązany. [#125](https://github.com/Microsoft/AzureStorageExplorer/issues/125)
* W przypadku niektórych działań prędkość transferu była nieprawidłowa. Ten problem został rozwiązany. [#124](https://github.com/Microsoft/AzureStorageExplorer/issues/124)
* Pisownia "Poprzednia" w menu Widok była błędna. Jest teraz poprawnie napisane. [#71](https://github.com/Microsoft/AzureStorageExplorer/issues/71)
* Ostatnia strona instalatora Windows miała przycisk "Dalej". Został on zmieniony na przycisk "Zakończ". [#70](https://github.com/Microsoft/AzureStorageExplorer/issues/70)
* Fokus tabulatora nie był widoczny dla przycisków w oknach dialogowych podczas korzystania z motywu HC Black. Jest teraz widoczny. [#64](https://github.com/Microsoft/AzureStorageExplorer/issues/64)
* Wielkość liter "Auto-Resolve" dla akcji w dzienniku aktywności była nieprawidłowa. Teraz jest poprawna. [#51](https://github.com/Microsoft/AzureStorageExplorer/issues/51)
* Podczas usuwania encji z tabeli w oknie dialogowym z prośbą o potwierdzenie wyświetlana jest ikona błędu. Okno dialogowe używa teraz ikony ostrzeżenia. [#148](https://github.com/Microsoft/AzureStorageExplorer/issues/148)

### <a name="known-issues"></a>Znane problemy
* Jeśli używasz vs dla komputerów Mac i kiedykolwiek utworzono niestandardową konfigurację usługi AAD, może być nie można zalogować. Aby obejść ten problem, usuń zawartość ~/. IdentityService/AadConfigurations. Jeśli to nie odblokuje Cię, proszę skomentować [tę kwestię](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite nie zaimplementował jeszcze w pełni wszystkich interfejsów API magazynu. Z tego powodu mogą wystąpić nieoczekiwane błędy lub zachowanie podczas korzystania z Azurite do przechowywania programów deweloperskich.
* W rzadkich przypadkach fokus drzewa może utknąć na szybki dostęp. Aby odkleić fokus, możesz odświeżyć wszystko.
* Przekazywanie z folderu usługi OneDrive nie działa z powodu błędu w NodeJS. Błąd został naprawiony, ale nie został jeszcze zintegrowany z Electronem.
* Podczas kierowania usługi Azure Stack przekazywanie niektórych plików jako dołączanie obiektów blob może zakończyć się niepowodzeniem.
* Po kliknięciu przycisku "Anuluj" zadania anulowanie tego zadania może chwilę potrwać. Dzieje się tak dlatego, że używamy filtru anulowania opisanego [tutaj.](https://github.com/Azure/azure-storage-node/issues/317)
* Jeśli wybierzesz niewłaściwy certyfikat PIN/Karta inteligentna, musisz ponownie uruchomić komputer, aby Eksplorator magazynu zapomniał o tej decyzji.
* Zmiana nazwy obiektów blob (indywidualnie lub wewnątrz kontenera obiektów blob o zmienionej nazwie) nie zachowuje migawek. Wszystkie inne właściwości i metadane obiektów blob, plików i jednostek są zachowywane podczas zmiany nazwy.
* Mimo że usługa Azure Stack obecnie nie obsługuje udziałów plików, węzeł udziałów plików nadal jest wyświetlany w ramach dołączonego konta usługi Azure Stack storage.
* Powłoka elektronu używana przez Eksploratora pamięci masowej ma problemy z akceleracją sprzętową niektórych procesorów graficznych (procesora graficznego). Jeśli Eksplorator magazynu wyświetla puste (puste) okno główne, możesz spróbować uruchomić Eksploratora magazynu `--disable-gpu` z wiersza polecenia i wyłączyć przyspieszenie procesora GPU, dodając przełącznik:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Dla użytkowników Linuksa, trzeba będzie zainstalować [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Dla użytkowników na Ubuntu 14.04, trzeba będzie upewnić się, GCC jest na bieżąco - można to zrobić, uruchamiając następujące polecenia, a następnie ponowne uruchomienie komputera:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Dla użytkowników na Ubuntu 17.04, trzeba będzie zainstalować GConf - można to zrobić, uruchamiając następujące polecenia, a następnie ponowne uruchomienie komputera:

    ```
    sudo apt-get install libgconf-2-4
    ```


## <a name="version-100"></a>Wersja 1.0.0
04/16/2018

### <a name="new"></a>Nowa
* Rozszerzone uwierzytelnianie, które umożliwia Eksploratorowi magazynu używanie tego samego magazynu kont co program Visual Studio 2017. Aby korzystać z tej funkcji, musisz ponownie zalogować się na swoje konta i ponownie ustawić filtrowane subskrypcje.
* W przypadku kont usługi Azure Stack wspieranych przez usługę AAD Eksplorator usługi Storage będzie teraz pobierał subskrypcje usługi Azure Stack po włączeniu funkcji "Docelowy stos azure stack". Nie trzeba już tworzyć niestandardowego środowiska logowania.
* Dodano kilka skrótów umożliwiających szybszą nawigację. Należą do nich przełączanie różnych paneli i przenoszenie między edytorami. Zobacz menu Widok, aby uzyskać więcej informacji.
* Opinia badacza pamięci masowego dostępna jest teraz w usłudze GitHub. Możesz dotrzeć do naszej strony problemy, klikając przycisk Opinie [https://github.com/Microsoft/AzureStorageExplorer/issues](https://github.com/Microsoft/AzureStorageExplorer/issues)w lewym dolnym rogu lub przechodząc do . Zachęcamy do przedstawiania sugestii, zgłaszania problemów, zadawania pytań lub pozostawiania innych form opinii.
* Jeśli korzystasz z problemów z certyfikatem TLS/SSL i nie możesz znaleźć certyfikatu naruszającego, `--ignore-certificate-errors` możesz teraz uruchomić Eksploratora magazynu z wiersza polecenia z flagą. Po uruchomieniu z tą flagą Eksplorator magazynu zignoruje błędy certyfikatów TLS/SSL.
* W menu kontekstowym dla elementów obiektów blob i plików istnieje teraz opcja "Pobierz".
* Ulepszona obsługa ułatwień dostępu i czytnika ekranu. Jeśli korzystasz z funkcji ułatwień dostępu, zapoznaj się z naszą [dokumentacją ułatwień dostępu, aby](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-accessibility) uzyskać więcej informacji.
* Eksplorator pamięci masowej używa teraz Electron 1.8.3

### <a name="breaking-changes"></a>Zmiany powodujące niezgodność
* Eksplorator magazynu przełączył się na nową bibliotekę uwierzytelniania. W ramach przejścia do biblioteki musisz ponownie zalogować się na swoje konta i ponownie ustawić filtrowane subskrypcje
* Zmieniono metodę używaną do szyfrowania poufnych danych. Może to spowodować, że niektóre elementy szybkiego dostępu wymagają ponownego dodania i/lub niektóre z was dołączone zasoby, które wymagają ponownego dołączenia.

### <a name="fixes"></a>Poprawki
* Niektórzy użytkownicy za serwerami proxy mieliby grupowe przekazywanie obiektów blob lub pobieranie przerywane komunikatem o błędzie "Nie można go rozpoznać". Ten problem został rozwiązany.
* Jeśli logowanie było potrzebne podczas korzystania z bezpośredniego łącza, kliknięcie monitu "Zaloguj się" spowoduje wyświetlenie pustego okna dialogowego. Ten problem został rozwiązany.
* W systemie Linux, jeśli Eksplorator pamięci masowej nie może uruchomić z powodu awarii procesu GPU, zostaniesz poinformowany o awarii, zostaniesz poinformowany o wyłączeniu przełącznika '--disable-gpu', a Eksplorator pamięci masowej automatycznie uruchomi się ponownie po włączeniu przełącznika.
* Nieprawidłowe zasady dostępu były trudne do tożsamości w oknie dialogowym Zasady dostępu. Nieprawidłowe identyfikatory zasad dostępu są teraz opisane na czerwono, aby uzyskać większą widoczność.
* Dziennik aktywności czasami ma duże obszary odstępów między różnymi częściami działania. Ten problem został rozwiązany.
* W edytorze kwerend tabeli, jeśli pozostawiono klauzulę sygnatury czasowej w nieprawidłowym stanie, a następnie podjęto próbę zmodyfikowania innej klauzuli, edytor zostanie zamroził. Edytor będzie teraz przywrócić klauzulę sygnatury czasowej do ostatniego prawidłowego stanu po wykryciu zmiany w innej klauzuli.
* Jeśli zostanie wstrzymana podczas wpisywania zapytania wyszukiwania w widoku drzewa, wyszukiwanie rozpocznie się, a fokus zostanie skradziony z pola tekstowego. Teraz musisz jawnie rozpocząć wyszukiwanie, naciskając klawisz "Enter" lub klikając przycisk start search.
* Polecenie "Pobierz podpis dostępu współdzielonego" czasami jest wyłączone po kliknięciu prawym przyciskiem myszy pliku w udziale plików. Ten problem został rozwiązany.
* Jeśli węzeł drzewa zasobów z fokusem został odfiltrowany podczas wyszukiwania, nie można włączyć karty do drzewa zasobów i użyć klawiszy strzałek do poruszania się po drzewie zasobów. Teraz, jeśli węzeł drzewa zasobów koncentruje się jest ukryty, pierwszy węzeł w drzewie zasobów będzie automatycznie koncentruje.
* Dodatkowy separator czasami będzie widoczny na pasku narzędzi edytora. Ten problem został rozwiązany.
* Pole tekstowe bułki tartej czasami przepełniało się. Ten problem został rozwiązany.
* Edytory obiektów Blob i Udostępnianie plików czasami stale odświeżałyby się podczas przesyłania wielu plików jednocześnie. Ten problem został rozwiązany.
* Funkcja "Statystyka folderów" nie miała żadnego celu w widoku Zarządzanie migawkami udziałów plików. Teraz został wyłączony.
* W systemie Linux menu Plik nie było wyświetlane. Ten problem został rozwiązany.
* Podczas przekazywania folderu do udziału plików domyślnie przekazano tylko zawartość folderu. Teraz domyślnym zachowaniem jest przekazanie zawartości folderu do pasującego folderu w udziale plików.
* Kolejność przycisków w kilku oknach dialogowych została odwrócona. Ten problem został rozwiązany.
* Różne poprawki związane z zabezpieczeniami.

### <a name="known-issues"></a>Znane problemy
* W rzadkich przypadkach fokus drzewa może utknąć na szybki dostęp. Aby odkleić fokus, możesz odświeżyć wszystko.
* Podczas kierowania usługi Azure Stack przekazywanie niektórych plików jako dołączanie obiektów blob może zakończyć się niepowodzeniem.
* Po kliknięciu przycisku "Anuluj" zadania anulowanie tego zadania może chwilę potrwać. Dzieje się tak, ponieważ używamy pracy filtru anulowania opisanego tutaj.
* Jeśli wybierzesz niewłaściwy certyfikat PIN/Karta inteligentna, musisz ponownie uruchomić komputer, aby Eksplorator magazynu zapomniał o tej decyzji.
* Zmiana nazwy obiektów blob (indywidualnie lub wewnątrz kontenera obiektów blob o zmienionej nazwie) nie zachowuje migawek. Wszystkie inne właściwości i metadane obiektów blob, plików i jednostek są zachowywane podczas zmiany nazwy.
* Mimo że usługa Azure Stack obecnie nie obsługuje udziałów plików, węzeł udziałów plików nadal jest wyświetlany w ramach dołączonego konta usługi Azure Stack storage.
* Powłoka elektronu używana przez Eksploratora pamięci masowej ma problemy z akceleracją sprzętową niektórych procesorów graficznych (procesora graficznego). Jeśli Eksplorator magazynu wyświetla puste (puste) okno główne, możesz spróbować uruchomić Eksploratora magazynu `--disable-gpu` z wiersza polecenia i wyłączyć przyspieszenie procesora GPU, dodając przełącznik:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Dla użytkowników Linuksa, trzeba będzie zainstalować [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* Dla użytkowników na Ubuntu 14.04, trzeba będzie upewnić się, GCC jest na bieżąco - można to zrobić, uruchamiając następujące polecenia, a następnie ponowne uruchomienie komputera:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Dla użytkowników na Ubuntu 17.04, trzeba będzie zainstalować GConf - można to zrobić, uruchamiając następujące polecenia, a następnie ponowne uruchomienie komputera:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-096"></a>Wersja 0.9.6
02/28/2018

### <a name="fixes"></a>Poprawki
* Problem uniemożliwił wyświetlenia oczekiwanych obiektów blob/plików w edytorze. Ten problem został rozwiązany.
* Problem spowodował przełączanie między widokami migawki, aby wyświetlić elementy niepoprawnie. Ten problem został rozwiązany.

### <a name="known-issues"></a>Znane problemy
* Eksplorator magazynu nie obsługuje kont usługi ADFS.
* Podczas kierowania usługi Azure Stack przekazywanie niektórych plików jako dołączanie obiektów blob może zakończyć się niepowodzeniem.
* Po kliknięciu przycisku "Anuluj" zadania anulowanie tego zadania może chwilę potrwać. Dzieje się tak dlatego, że używamy filtru anulowania opisanego [tutaj.](https://github.com/Azure/azure-storage-node/issues/317)
* Jeśli wybierzesz niewłaściwy certyfikat PIN/Karta inteligentna, musisz ponownie uruchomić komputer, aby Eksplorator magazynu zapomniał o tej decyzji.
* Panel ustawień konta może pokazać, że musisz ponownie wdawać poświadczenia, aby filtrować subskrypcje.
* Zmiana nazwy obiektów blob (indywidualnie lub wewnątrz kontenera obiektów blob o zmienionej nazwie) nie zachowuje migawek. Wszystkie inne właściwości i metadane obiektów blob, plików i jednostek są zachowywane podczas zmiany nazwy.
* Mimo że usługa Azure Stack obecnie nie obsługuje udziałów plików, węzeł udziałów plików nadal jest wyświetlany w ramach dołączonego konta usługi Azure Stack storage.
* Powłoka elektronu używana przez Eksploratora pamięci masowej ma problemy z akceleracją sprzętową niektórych procesorów graficznych (procesora graficznego). Jeśli Eksplorator magazynu wyświetla puste (puste) okno główne, możesz spróbować uruchomić Eksploratora magazynu `--disable-gpu` z wiersza polecenia i wyłączyć przyspieszenie procesora GPU, dodając przełącznik:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Dla użytkowników na Ubuntu 14.04, trzeba będzie upewnić się, GCC jest na bieżąco - można to zrobić, uruchamiając następujące polecenia, a następnie ponowne uruchomienie komputera:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Dla użytkowników na Ubuntu 17.04, trzeba będzie zainstalować GConf - można to zrobić, uruchamiając następujące polecenia, a następnie ponowne uruchomienie komputera:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-095"></a>Wersja 0.9.5
02/06/2018

### <a name="new"></a>Nowa

* Obsługa migawek udziałów plików:
    * Tworzenie migawek dla udziałów plików i zarządzanie nimi.
    * Podczas eksplorowania można łatwo przełączać widoki między migawkami udziałów plików.
    * Przywróć poprzednie wersje plików.
* Obsługa wersji zapoznawczej dla usługi Azure Data Lake Store:
    * Połącz się z zasobami ADLS na wielu kontach.
    * Łączenie się z zasobami ADLS i udostępnianie ich przy użyciu identyfikatorów URI ADL.
    * Cyklicznie przeprowadzaj podstawowe operacje plików/folderów.
    * Przypnij poszczególne foldery do szybkiego dostępu.
    * Wyświetlanie statystyk folderów.

### <a name="fixes"></a>Poprawki
* Poprawa wydajności uruchamiania.
* Różne poprawki błędów.

### <a name="known-issues"></a>Znane problemy
* Eksplorator magazynu nie obsługuje kont usługi ADFS.
* Podczas kierowania usługi Azure Stack przekazywanie niektórych plików jako dołączanie obiektów blob może zakończyć się niepowodzeniem.
* Po kliknięciu przycisku "Anuluj" zadania anulowanie tego zadania może chwilę potrwać. Dzieje się tak, ponieważ używamy pracy filtru anulowania opisanego tutaj.
* Jeśli wybierzesz niewłaściwy certyfikat PIN/Karta inteligentna, musisz ponownie uruchomić komputer, aby Eksplorator magazynu zapomniał o tej decyzji.
* Panel ustawień konta może pokazać, że musisz ponownie wdawać poświadczenia, aby filtrować subskrypcje.
* Zmiana nazwy obiektów blob (indywidualnie lub wewnątrz kontenera obiektów blob o zmienionej nazwie) nie zachowuje migawek. Wszystkie inne właściwości i metadane obiektów blob, plików i jednostek są zachowywane podczas zmiany nazwy.
* Mimo że usługa Azure Stack obecnie nie obsługuje udziałów plików, węzeł udziałów plików nadal jest wyświetlany w ramach dołączonego konta usługi Azure Stack storage.
* Powłoka elektronu używana przez Eksploratora pamięci masowej ma problemy z akceleracją sprzętową niektórych procesorów graficznych (procesora graficznego). Jeśli Eksplorator magazynu wyświetla puste (puste) okno główne, możesz spróbować uruchomić Eksploratora magazynu `--disable-gpu` z wiersza polecenia i wyłączyć przyspieszenie procesora GPU, dodając przełącznik:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Dla użytkowników na Ubuntu 14.04, trzeba będzie upewnić się, GCC jest na bieżąco - można to zrobić, uruchamiając następujące polecenia, a następnie ponowne uruchomienie komputera:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Dla użytkowników na Ubuntu 17.04, trzeba będzie zainstalować GConf - można to zrobić, uruchamiając następujące polecenia, a następnie ponowne uruchomienie komputera:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-094-and-093"></a>Wersja 0.9.4 i 0.9.3
01/21/2018

### <a name="new"></a>Nowa
* Istniejące okno Eksploratora magazynu zostanie ponownie użyte, gdy:
    * Otwieranie łączy bezpośrednich generowanych w Eksploratorze magazynu.
    * Otwieranie Eksploratora magazynu z portalu.
    * Otwieranie Eksploratora magazynu z rozszerzenia kodu usługi Azure Storage VS (wkrótce).
* Dodano możliwość otwarcia nowego okna Eksploratora magazynu z poziomu Eksploratora magazynu.
    * W systemie Windows w menu plików i w menu kontekstowym paska zadań znajduje się opcja "Nowe okno".
    * W przypadku komputerów Mac w menu aplikacji znajduje się opcja "Nowe okno".

### <a name="fixes"></a>Poprawki
* Naprawiono błąd bezpieczeństwa. Proszę uaktualnić do 0.9.4 w najbliższym czasie.
* Stare zajęcia nie były odpowiednio sprzątane. Wpłynęło to na wydajność długotrwałych zadań. Są one teraz czyszczone poprawnie.
* Akcje obejmujące dużą liczbę plików i katalogów czasami powodują zamrożenie Eksploratora magazynu. Żądania do platformy Azure dla udziałów plików są teraz ograniczane, aby ograniczyć użycie zasobów systemowych.

### <a name="known-issues"></a>Znane problemy
* Eksplorator magazynu nie obsługuje kont usługi ADFS.
* Klawisze skrótów dla "View Explorer" i "View Account Management" powinny być ctrl/cmd+Shift+E i Ctrl/Cmd+Shift+A odpowiednio.
* Podczas kierowania usługi Azure Stack przekazywanie niektórych plików jako dołączanie obiektów blob może zakończyć się niepowodzeniem.
* Po kliknięciu przycisku "Anuluj" zadania anulowanie tego zadania może chwilę potrwać. Dzieje się tak, ponieważ używamy pracy filtru anulowania opisanego tutaj.
* Jeśli wybierzesz niewłaściwy certyfikat PIN/Karta inteligentna, musisz ponownie uruchomić komputer, aby Eksplorator magazynu zapomniał o tej decyzji.
* Panel ustawień konta może pokazać, że musisz ponownie wdawać poświadczenia, aby filtrować subskrypcje.
* Zmiana nazwy obiektów blob (indywidualnie lub wewnątrz kontenera obiektów blob o zmienionej nazwie) nie zachowuje migawek. Wszystkie inne właściwości i metadane obiektów blob, plików i jednostek są zachowywane podczas zmiany nazwy.
* Mimo że usługa Azure Stack obecnie nie obsługuje udziałów plików, węzeł udziałów plików nadal jest wyświetlany w ramach dołączonego konta usługi Azure Stack storage.
* Powłoka elektronu używana przez Eksploratora pamięci masowej ma problemy z akceleracją sprzętową niektórych procesorów graficznych (procesora graficznego). Jeśli Eksplorator magazynu wyświetla puste (puste) okno główne, możesz spróbować uruchomić Eksploratora magazynu `--disable-gpu` z wiersza polecenia i wyłączyć przyspieszenie procesora GPU, dodając przełącznik:

    ```
    ./StorageExplorer --disable-gpu
    ```

* Dla użytkowników na Ubuntu 14.04, trzeba będzie upewnić się, GCC jest na bieżąco - można to zrobić, uruchamiając następujące polecenia, a następnie ponowne uruchomienie komputera:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Dla użytkowników na Ubuntu 17.04, trzeba będzie zainstalować GConf - można to zrobić, uruchamiając następujące polecenia, a następnie ponowne uruchomienie komputera:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-092"></a>Wersja 0.9.2
11/01/2017

### <a name="hotfixes"></a>Poprawki
* Nieoczekiwane zmiany danych były możliwe podczas edytowania wartości Edm.DateTime dla jednostek tabeli w zależności od lokalnej strefy czasowej. Edytor używa teraz zwykłego pola tekstowego, zapewniając precyzyjną, spójną kontrolę nad wartościami Edm.DateTime.
* Przekazywanie/pobieranie grupy obiektów blob po dołączeniu z nazwą i kluczem nie rozpocznie się. Ten problem został rozwiązany.
* Wcześniej Eksplorator magazynu monitował o ponowne uwierzytelnienie nieaktualnego konta tylko wtedy, gdy wybrano jedną lub więcej subskrypcji konta. Teraz Eksplorator magazynu wyświetli monit, nawet jeśli konto jest całkowicie odfiltrowane.
* Domena punktów końcowych dla platformy Azure us government była błędna. Został on naprawiony.
* Czasami trudno było kliknąć przycisk Zastosuj w panelu Zarządzanie kontami. To już nie powinno się zdarzyć.

### <a name="new"></a>Nowa
* Obsługa podglądu usługi Azure Cosmos DB:
    * [Dokumentacja online](./cosmos-db/storage-explorer.md)
    * Tworzenie baz danych i kolekcji
    * Manipulowanie danymi
    * Wykonywanie zapytań, tworzenie lub usuwanie dokumentów
    * Aktualizowanie procedur przechowywanych, funkcji zdefiniowanych przez użytkownika lub wyzwalaczy
    * Łączenie się z bazami danych i zarządzanie nimi
* Poprawiono wydajność przesyłania/pobierania wielu małych obiektów blob.
* Dodano akcję "Ponów próbę wszystkich", jeśli występują błędy w grupie pobierania przekazywania obiektów blob lub obiektów blob.
* Eksplorator magazynu wstrzyma teraz iterację podczas przekazywania/pobierania obiektu blob, jeśli wykryje utratę połączenia sieciowego. Następnie można wznowić iterację po ponownym nawiązaniu połączenia sieciowego.
* Dodano możliwość zakładek "Zamknij wszystko", "Zamknij innych" i "Zamknij" za pomocą menu kontekstowego.
* Eksplorator magazynu używa teraz natywnych okien dialogowych i natywnych menu kontekstowych.
* Eksplorator magazynu jest teraz bardziej dostępny. Ulepszenia obejmują:
    * Ulepszona obsługa czytnika ekranu dla NVDA w systemie Windows i VoiceOver na macu
    * Ulepszone motywy o wysokim kontraście
    * Klawiatura tabulator i klawiatury poprawki fokusu

### <a name="fixes"></a>Poprawki
* Jeśli próbowano otworzyć lub pobrać obiekt blob z nieprawidłową nazwą pliku systemu Windows, operacja zakończy się niepowodzeniem. Eksplorator magazynu wykryje teraz, czy nazwa obiektu blob jest nieprawidłowa i zapyta, czy chcesz ją zakodować, czy pominąć obiekt blob. Eksplorator magazynu wykryje również, czy nazwa pliku wydaje się być zakodowana i zapyta, czy chcesz ją zdekodować przed przekazaniem.
* Podczas przekazywania obiektów blob edytor docelowego kontenera obiektów blob czasami nie zostanie poprawnie odświeżony. Ten problem został rozwiązany.
* Obsługa kilku form ciągów połączeń i identyfikatorów URI sygnatury dostępu Współdzielonego ponownie. Rozwiązaliśmy wszystkie znane problemy, ale prosimy o przesłanie opinii w przypadku wystąpienia dalszych problemów.
* Powiadomienie o aktualizacji zostało przerwane dla niektórych użytkowników w 0.9.0. Ten problem został rozwiązany, a dla osób dotkniętych błędem, można ręcznie pobrać najnowszą wersję Eksploratora magazynu [tutaj](https://azure.microsoft.com/features/storage-explorer/).

### <a name="known-issues"></a>Znane problemy
* Eksplorator magazynu nie obsługuje kont usługi ADFS.
* Klawisze skrótów dla "View Explorer" i "View Account Management" powinny być ctrl/cmd+Shift+E i Ctrl/Cmd+Shift+A odpowiednio.
* Podczas kierowania usługi Azure Stack przekazywanie niektórych plików jako dołączanie obiektów blob może zakończyć się niepowodzeniem.
* Po kliknięciu przycisku "Anuluj" zadania anulowanie tego zadania może chwilę potrwać. Dzieje się tak, ponieważ używamy pracy filtru anulowania opisanego tutaj.
* Jeśli wybierzesz niewłaściwy certyfikat PIN/Karta inteligentna, musisz ponownie uruchomić komputer, aby Eksplorator magazynu zapomniał o tej decyzji.
* Panel ustawień konta może pokazać, że musisz ponownie wdawać poświadczenia, aby filtrować subskrypcje.
* Zmiana nazwy obiektów blob (indywidualnie lub wewnątrz kontenera obiektów blob o zmienionej nazwie) nie zachowuje migawek. Wszystkie inne właściwości i metadane obiektów blob, plików i jednostek są zachowywane podczas zmiany nazwy.
* Mimo że usługa Azure Stack obecnie nie obsługuje udziałów plików, węzeł udziałów plików nadal jest wyświetlany w ramach dołączonego konta usługi Azure Stack storage.
* Powłoka elektronu używana przez Eksploratora pamięci masowej ma problemy z akceleracją sprzętową niektórych procesorów graficznych (procesora graficznego). Jeśli Eksplorator magazynu wyświetla puste (puste) okno główne, możesz spróbować uruchomić Eksploratora magazynu `--disable-gpu` z wiersza polecenia i wyłączyć przyspieszenie procesora GPU, dodając przełącznik:

    ```
    ./StorageExplorer --disable-gpu
    ```

* Dla użytkowników na Ubuntu 14.04, trzeba będzie upewnić się, GCC jest na bieżąco - można to zrobić, uruchamiając następujące polecenia, a następnie ponowne uruchomienie komputera:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Dla użytkowników na Ubuntu 17.04, trzeba będzie zainstalować GConf - można to zrobić, uruchamiając następujące polecenia, a następnie ponowne uruchomienie komputera:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-091-and-090"></a>Wersja 0.9.1 i 0.9.0
10/20/2017
### <a name="new"></a>Nowa
* Obsługa podglądu usługi Azure Cosmos DB:
    * [Dokumentacja online](./cosmos-db/storage-explorer.md)
    * Tworzenie baz danych i kolekcji
    * Manipulowanie danymi
    * Wykonywanie zapytań, tworzenie lub usuwanie dokumentów
    * Aktualizowanie procedur przechowywanych, funkcji zdefiniowanych przez użytkownika lub wyzwalaczy
    * Łączenie się z bazami danych i zarządzanie nimi
* Poprawiono wydajność przesyłania/pobierania wielu małych obiektów blob.
* Dodano akcję "Ponów próbę wszystkich", jeśli występują błędy w grupie pobierania przekazywania obiektów blob lub obiektów blob.
* Eksplorator magazynu wstrzyma teraz iterację podczas przekazywania/pobierania obiektu blob, jeśli wykryje utratę połączenia sieciowego. Następnie można wznowić iterację po ponownym nawiązaniu połączenia sieciowego.
* Dodano możliwość zakładek "Zamknij wszystko", "Zamknij innych" i "Zamknij" za pomocą menu kontekstowego.
* Eksplorator magazynu używa teraz natywnych okien dialogowych i natywnych menu kontekstowych.
* Eksplorator magazynu jest teraz bardziej dostępny. Ulepszenia obejmują:
    * Ulepszona obsługa czytnika ekranu dla NVDA w systemie Windows i VoiceOver na macu
    * Ulepszone motywy o wysokim kontraście
    * Klawiatura tabulator i klawiatury poprawki fokusu

### <a name="fixes"></a>Poprawki
* Jeśli próbowano otworzyć lub pobrać obiekt blob z nieprawidłową nazwą pliku systemu Windows, operacja zakończy się niepowodzeniem. Eksplorator magazynu wykryje teraz, czy nazwa obiektu blob jest nieprawidłowa i zapyta, czy chcesz ją zakodować, czy pominąć obiekt blob. Eksplorator magazynu wykryje również, czy nazwa pliku wydaje się być zakodowana i zapyta, czy chcesz ją zdekodować przed przekazaniem.
* Podczas przekazywania obiektów blob edytor docelowego kontenera obiektów blob czasami nie zostanie poprawnie odświeżony. Ten problem został rozwiązany.
* Obsługa kilku form ciągów połączeń i identyfikatorów URI sygnatury dostępu Współdzielonego ponownie. Rozwiązaliśmy wszystkie znane problemy, ale prosimy o przesłanie opinii w przypadku wystąpienia dalszych problemów.
* Powiadomienie o aktualizacji zostało przerwane dla niektórych użytkowników w 0.9.0. Ten problem został rozwiązany, a dla osób dotkniętych błędem, można ręcznie pobrać najnowszą wersję Eksploratora magazynu [tutaj](https://azure.microsoft.com/features/storage-explorer/)

### <a name="known-issues"></a>Znane problemy
* Eksplorator magazynu nie obsługuje kont usługi ADFS.
* Klawisze skrótów dla "View Explorer" i "View Account Management" powinny być ctrl/cmd+Shift+E i Ctrl/Cmd+Shift+A odpowiednio.
* Podczas kierowania usługi Azure Stack przekazywanie niektórych plików jako dołączanie obiektów blob może zakończyć się niepowodzeniem.
* Po kliknięciu przycisku "Anuluj" zadania anulowanie tego zadania może chwilę potrwać. Dzieje się tak, ponieważ używamy pracy filtru anulowania opisanego tutaj.
* Jeśli wybierzesz niewłaściwy certyfikat PIN/Karta inteligentna, musisz ponownie uruchomić komputer, aby Eksplorator magazynu zapomniał o tej decyzji.
* Panel ustawień konta może pokazać, że musisz ponownie wdawać poświadczenia, aby filtrować subskrypcje.
* Zmiana nazwy obiektów blob (indywidualnie lub wewnątrz kontenera obiektów blob o zmienionej nazwie) nie zachowuje migawek. Wszystkie inne właściwości i metadane obiektów blob, plików i jednostek są zachowywane podczas zmiany nazwy.
* Mimo że usługa Azure Stack obecnie nie obsługuje udziałów plików, węzeł udziałów plików nadal jest wyświetlany w ramach dołączonego konta usługi Azure Stack storage.
* Powłoka elektronu używana przez Eksploratora pamięci masowej ma problemy z akceleracją sprzętową niektórych procesorów graficznych (procesora graficznego). Jeśli Eksplorator magazynu wyświetla puste (puste) okno główne, możesz spróbować uruchomić Eksploratora magazynu `--disable-gpu` z wiersza polecenia i wyłączyć przyspieszenie procesora GPU, dodając przełącznik:

    ```
    ./StorageExplorer --disable-gpu
    ```

* Dla użytkowników na Ubuntu 14.04, trzeba będzie upewnić się, GCC jest na bieżąco - można to zrobić, uruchamiając następujące polecenia, a następnie ponowne uruchomienie komputera:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Dla użytkowników na Ubuntu 17.04, trzeba będzie zainstalować GConf - można to zrobić, uruchamiając następujące polecenia, a następnie ponowne uruchomienie komputera:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-0816"></a>Wersja 0.8.16
8/21/2017

### <a name="new"></a>Nowa
* Po otwarciu obiektu blob Eksplorator magazynu wyświetli monit o przekazanie pobranego pliku w przypadku wykrycia zmiany
* Ulepszone środowisko logowania usługi Azure Stack
* Poprawiono wydajność przesyłania/pobierania wielu małych plików w tym samym czasie


### <a name="fixes"></a>Poprawki
* W przypadku niektórych typów obiektów blob wybranie opcji "zastąp" podczas konfliktu przekazywania czasami powodowałoby ponowne uruchomienie przekazywania.
* W wersji 0.8.15 przesyłanie czasami przeskakuje na poziomie 99%.
* Podczas przesyłania plików do udziału plików, jeśli zdecydujesz się przekazać do katalogu, który jeszcze nie istnieje, przekazywanie zakończy się niepowodzeniem.
* Eksplorator magazynu niepoprawnie wygenerował sygnatury czasowe dla podpisów dostępu współdzielonego i zapytań tabel.


### <a name="known-issues"></a>Znane problemy
* Przy użyciu nazwy i klucza połączenia ciąg nie działa obecnie. Zostanie on naprawiony w następnym wydaniu. Do tego czasu można użyć dołączyć z nazwą i kluczem.
* Jeśli spróbujesz otworzyć plik o nieprawidłowej nazwie pliku systemu Windows, pobieranie spowoduje błąd nie znaleziono pliku.
* Po kliknięciu przycisku "Anuluj" zadania anulowanie tego zadania może chwilę potrwać. Jest to ograniczenie biblioteki węzła usługi Azure Storage.
* Po zakończeniu przekazywania obiektu blob karta, która zainicjowała przekazywanie, zostanie odświeżona. Jest to zmiana w stosunku do poprzedniego zachowania, a także spowoduje, że zostaniesz zabrany z powrotem do katalogu głównego kontenera, w którego się znajdujesz.
* Jeśli wybierzesz niewłaściwy certyfikat PIN/Karta inteligentna, musisz ponownie uruchomić komputer, aby Eksplorator magazynu zapomniał o tej decyzji.
* Panel ustawień konta może pokazać, że musisz ponownie wdawać poświadczenia, aby filtrować subskrypcje.
* Zmiana nazwy obiektów blob (indywidualnie lub wewnątrz kontenera obiektów blob o zmienionej nazwie) nie zachowuje migawek. Wszystkie inne właściwości i metadane obiektów blob, plików i jednostek są zachowywane podczas zmiany nazwy.
* Mimo że usługa Azure Stack obecnie nie obsługuje udziałów plików, węzeł udziałów plików nadal jest wyświetlany w ramach dołączonego konta usługi Azure Stack storage.
* Dla użytkowników na Ubuntu 14.04, trzeba będzie upewnić się, GCC jest na bieżąco - można to zrobić, uruchamiając następujące polecenia, a następnie ponowne uruchomienie komputera:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Dla użytkowników na Ubuntu 17.04, trzeba będzie zainstalować GConf - można to zrobić, uruchamiając następujące polecenia, a następnie ponowne uruchomienie komputera:

    ```
    sudo apt-get install libgconf-2-4
    ```

### <a name="version-0814"></a>Wersja 0.8.14
06/22/2017

### <a name="new"></a>Nowa

* Zaktualizowano wersję Electron do wersji 1.7.2, aby skorzystać z kilku krytycznych aktualizacji zabezpieczeń
* Teraz możesz szybko uzyskać dostęp do przewodnika rozwiązywania problemów online z menu pomocy
* [Przewodnik po][2] rozwiązywaniu problemów z Eksploratorem magazynu
* [Instrukcje dotyczące][3] łączenia się z subskrypcją usługi Azure Stack

### <a name="known-issues"></a>Znane problemy

* Przyciski w oknie dialogowym potwierdzenia folderu usuwania nie rejestrują się za pomocą kliknięć myszą w systemie Linux. obejść jest użycie klawisza Enter
* Jeśli wybierzesz niewłaściwy certyfikat PIN/Karta inteligentna, musisz ponownie uruchomić komputer, aby Eksplorator pamięci masowej zapomniał o decyzji
* Posiadanie więcej niż 3 grup obiektów blob lub plików przesyłanych w tym samym czasie może spowodować błędy
* Panel ustawień konta może oznaczać konieczność ponownego wprowadzenia poświadczeń w celu filtrowania subskrypcji
* Zmiana nazwy obiektów blob (indywidualnie lub wewnątrz kontenera obiektów blob o zmienionej nazwie) nie zachowuje migawek. Wszystkie inne właściwości i metadane obiektów blob, plików i jednostek są zachowywane podczas zmiany nazwy.
* Mimo że usługa Azure Stack obecnie nie obsługuje udziałów plików, węzeł udziałów plików nadal jest wyświetlany w ramach dołączonego konta usługi Azure Stack storage.
* Ubuntu 14.04 zainstalować potrzebuje wersji GCC zaktualizowane lub uaktualnione - kroki, aby uaktualnić są poniżej:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

### <a name="version-0813"></a>Wersja 0.8.13
05/12/2017

#### <a name="new"></a>Nowa

* [Przewodnik po][2] rozwiązywaniu problemów z Eksploratorem magazynu
* [Instrukcje dotyczące][3] łączenia się z subskrypcją usługi Azure Stack

#### <a name="fixes"></a>Poprawki

* Naprawiono: Przesyłanie plików miało duże szanse na spowodowanie błędu braku pamięci
* Naprawiono: Możesz teraz zalogować się za pomocą kodu PIN/karty inteligentnej
* Poprawiono: Otwórz w portalu współpracuje teraz z platformą Azure China 21Vianet, Azure Germany, Azure US Government i Azure Stack
* Naprawiono: Podczas przesyłania folderu do kontenera obiektu blob czasami pojawia się błąd "Niedozwolona operacja"
* Poprawiono: Zaznaczenie wszystkich zostało wyłączone podczas zarządzania migawkami
* Naprawiono: Metadane podstawowego obiektu blob mogą zostać zastąpione po wyświetleniu właściwości jego migawek

#### <a name="known-issues"></a>Znane problemy

* Jeśli wybierzesz niewłaściwy certyfikat PIN/Karta inteligentna, musisz ponownie uruchomić komputer, aby Eksplorator pamięci masowej zapomniał o decyzji
* Powiększeniu lub pomniejszeniu poziom powiększenia może chwilowo
* Posiadanie więcej niż 3 grup obiektów blob lub plików przesyłanych w tym samym czasie może spowodować błędy
* Panel ustawień konta może oznaczać konieczność ponownego wprowadzenia poświadczeń w celu filtrowania subskrypcji
* Zmiana nazwy obiektów blob (indywidualnie lub wewnątrz kontenera obiektów blob o zmienionej nazwie) nie zachowuje migawek. Wszystkie inne właściwości i metadane obiektów blob, plików i jednostek są zachowywane podczas zmiany nazwy.
* Mimo że usługa Azure Stack obecnie nie obsługuje udziałów plików, węzeł udziałów plików nadal jest wyświetlany w ramach dołączonego konta usługi Azure Stack storage.
* Ubuntu 14.04 zainstalować potrzebuje wersji GCC zaktualizowane lub uaktualnione - kroki, aby uaktualnić są poniżej:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```


### <a name="version-0812-and-0811-and-0810"></a>Wersja 0.8.12 i 0.8.11 i 0.8.10
04/07/2017

#### <a name="new"></a>Nowa

* Eksplorator magazynu zostanie automatycznie zamknięty po zainstalowaniu aktualizacji z powiadomienia o aktualizacji
* Szybki dostęp w miejscu zapewnia lepsze środowisko pracy z często używanymi zasobami
* W edytorze kontenera obiektów blob można teraz zobaczyć, do której maszyny wirtualnej należy dzierżawiony obiekt blob
* Teraz można zwinąć lewy panel boczny
* Odnajdowanie działa teraz w tym samym czasie co pobieranie
* Użyj statystyk w edytorach kontenera obiektów blob, udziału plików i tabel, aby zobaczyć rozmiar zasobu lub zaznaczenia
* Teraz możesz zalogować się do kont usługi Azure Stack opartych na usłudze Azure (Azure Directory) platformy Azure.
* Teraz możesz przesyłać pliki archiwum o przemieszać ponad 32 MB na konta magazynu Premium
* Ulepszona obsługa ułatwień dostępu
* Teraz można dodać zaufane certyfikaty X.509 zakodowane w bazie podstawowej X.509 TLS/SSL, przechodząc do edit -&gt; SSL Certificates -&gt; Import Certificates

#### <a name="fixes"></a>Poprawki

* Naprawiono: po odświeżeniu poświadczeń konta widok drzewa czasami nie odświeża się automatycznie
* Naprawiono: wygenerowanie sygnatury dostępu Współdzielonego dla kolejek i tabel emulatora spowodowałoby nieprawidłowy adres URL
* Naprawiono: konta magazynu w premium można teraz rozszerzać, gdy serwer proxy jest włączony
* Naprawiono: przycisk Zastosuj na stronie zarządzania kontami nie działałby, gdyby zaznaczono 1 lub 0 kont
* Naprawiono: przesyłanie obiektów blob wymagających rozwiązywania konfliktów może zakończyć się niepowodzeniem — poprawiono w 0.8.11
* Naprawiono: wysyłanie opinii zostało przerwane w 0.8.11 - poprawiono w 0.8.12

#### <a name="known-issues"></a>Znane problemy

* Po uaktualnieniu do 0.8.10 należy odświeżyć wszystkie poświadczenia.
* Powiększeniu lub pomniejszeniu poziom powiększenia może chwilowo zresetować do poziomu domyślnego.
* Posiadanie więcej niż 3 grup obiektów blob lub plików przekazywania w tym samym czasie może spowodować błędy.
* Panel ustawień konta może pokazać, że musisz ponownie wdawać poświadczenia, aby filtrować subskrypcje.
* Zmiana nazwy obiektów blob (indywidualnie lub wewnątrz kontenera obiektów blob o zmienionej nazwie) nie zachowuje migawek. Wszystkie inne właściwości i metadane obiektów blob, plików i jednostek są zachowywane podczas zmiany nazwy.
* Mimo że usługa Azure Stack obecnie nie obsługuje udziałów plików, węzeł udziałów plików nadal jest wyświetlany w ramach dołączonego konta usługi Azure Stack storage.
* Ubuntu 14.04 zainstalować potrzebuje wersji GCC zaktualizowane lub uaktualnione - kroki, aby uaktualnić są poniżej:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```


### <a name="version-089-and-088"></a>Wersja 0.8.9 i 0.8.8
02/23/2017

>[!VIDEO https://www.youtube.com/embed/R6gonK3cYAc?ecver=1]

>[!VIDEO https://www.youtube.com/embed/SrRPCm94mfE?ecver=1]


#### <a name="new"></a>Nowa

* Program Storage Explorer 0.8.9 automatycznie pobierze najnowszą wersję aktualizacji.
* Poprawka: przy użyciu identyfikatora URI generowanego przez portal do dołączania konta magazynu spowodowałoby błąd.
* Teraz można tworzyć migawki obiektów blob, zarządzać nimi i promować je.
* Teraz możesz zalogować się na konta azure China 21Vianet, Azure Germany i Azure US Government.
* Teraz można zmienić poziom powiększenia. Użyj opcji w menu Widok, aby powiększyć, pomniejszyć i zresetować powiększenie.
* Znaki Unicode są teraz obsługiwane w metadanych użytkownika dla obiektów blob i plików.
* Ulepszenia ułatwień dostępu.
* Informacje o wersji następnej wersji można wyświetlić z powiadomienia o aktualizacji. Bieżące informacje o wersji można również wyświetlić z menu Pomoc.

#### <a name="fixes"></a>Poprawki

* Poprawiono: numer wersji jest teraz poprawnie wyświetlany w Panelu sterowania w systemie Windows
* Naprawiono: wyszukiwanie nie jest już ograniczone do 50 000 węzłów
* Naprawiono: przesyłanie do udziału plików obróciło się na zawsze, jeśli katalog docelowy jeszcze nie istniał
* Poprawiono: poprawiono stabilność podczas długich wgrywania i pobierania

#### <a name="known-issues"></a>Znane problemy

* Powiększeniu lub pomniejszeniu poziom powiększenia może chwilowo zresetować do poziomu domyślnego.
* Szybki dostęp działa tylko z elementami opartymi na subskrypcji. Zasoby lokalne lub zasoby dołączone za pośrednictwem klucza lub tokenu Sygnatury dostępu Współdzielonego nie są obsługiwane w tej wersji.
* Przejście do zasobu docelowego może potrwać kilka sekund, w zależności od liczby posiadań zasobów.
* Posiadanie więcej niż 3 grup obiektów blob lub plików przekazywania w tym samym czasie może spowodować błędy.

12/16/2016
### <a name="version-087"></a>Wersja 0.8.7

>[!VIDEO https://www.youtube.com/embed/Me4Y4jxoer8?ecver=1]

#### <a name="new"></a>Nowa

* Możesz wybrać sposób rozwiązywania konfliktów na początku aktualizacji, pobierania lub kopiowania sesji w oknie Działania
* Umieść wskaźnik myszy na karcie, aby wyświetlić pełną ścieżkę zasobu magazynu
* Po kliknięciu karty synchronizuje się ją z jej lokalizacją w lewym okienku nawigacji po lewej stronie

#### <a name="fixes"></a>Poprawki

* Naprawiono: Eksplorator pamięci masowej jest teraz zaufaną aplikacją na komputerze Mac
* Naprawiono: Ubuntu 14.04 jest ponownie obsługiwany
* Naprawiono: Czasami interfejs użytkownika konta dodawania miga podczas ładowania subskrypcji
* Naprawiono: Czasami nie wszystkie zasoby magazynu były wymienione w lewym okienku nawigacji po lewej stronie
* Naprawiono: W okienku akcji czasami wyświetlane były puste akcje
* Naprawiono: Rozmiar okna z ostatniej zamkniętej sesji jest teraz zachowywany
* Naprawiono: Za pomocą menu kontekstowego można otworzyć wiele kart dla tego samego zasobu.

#### <a name="known-issues"></a>Znane problemy

* Szybki dostęp działa tylko z elementami opartymi na subskrypcji. Zasoby lokalne lub zasoby dołączone za pośrednictwem klucza lub tokenu Sygnatury dostępu Współdzielonego nie są obsługiwane w tej wersji
* Przejście do zasobu docelowego może potrwać kilka sekund, w zależności od liczby posiadanego zasobu
* Posiadanie więcej niż 3 grup obiektów blob lub plików przesyłanych w tym samym czasie może spowodować błędy
* Uchwyty wyszukiwania przeszukują około 50 000 węzłów - po tym, wydajność może mieć wpływ lub może powodować nieobsługiwanie się wyjątku
* Po raz pierwszy przy użyciu Eksploratora magazynu w systemie macOS może zostać wyświetlonych wiele monitów z prośbą o pozwolenie użytkownika na dostęp do pęku kluczy. Zalecamy wybranie opcji Zawsze zezwalaj, aby monit nie był wyświetlany ponownie

11/18/2016
### <a name="version-086"></a>Wersja 0.8.6

#### <a name="new"></a>Nowa

* Teraz można przypiąć najczęściej używane usługi do szybkiego dostępu, aby ułatwić nawigację
* Teraz można otworzyć wiele edytorów w różnych kartach. Pojedyncze kliknięcie, aby otworzyć tymczasową kartę; kliknij dwukrotnie, aby otworzyć stałą kartę. Możesz również kliknąć kartę tymczasową, aby stała zakładka
* Dokonaliśmy zauważalnej wydajności i stabilności w przypadku przesyłania i pobierania, szczególnie w przypadku dużych plików na szybkich komputerach
* Puste "wirtualne" foldery można teraz tworzyć w kontenerach obiektów blob
* Ponownie wprowadziliśmy wyszukiwanie o zasięgu dzięki naszemu nowemu ulepszonemu wyszukiwaniu podciągów, więc masz teraz dwie opcje wyszukiwania:
    * Wyszukiwanie globalne - wystarczy wpisać wyszukiwany termin w polu tekstowym wyszukiwania
    * Wyszukiwanie o określonym zakresie - kliknij ikonę lupy obok węzła, a następnie dodaj wyszukiwany termin na końcu ścieżki lub kliknij prawym przyciskiem myszy i wybierz "Szukaj tutaj"
* Dodaliśmy różne motywy: Jasny (domyślnie), Ciemny, Czarny o wysokim kontraście i Biały o wysokim kontraście. Przejdź do&gt; pozycji Edytuj — motywy, aby zmienić preferencje dotyczące motywów
* Można modyfikować właściwości obiektów Blob i plików
* Obsługujemy teraz zakodowane (base64) i niezakodowane wiadomości kolejki
* W systemie Linux wymagany jest teraz 64-bitowy system operacyjny. W tej wersji obsługujemy tylko 64-bitowy Ubuntu 16.04.1 LTS
* Zaktualizowaliśmy nasze logo!

#### <a name="fixes"></a>Poprawki

* Naprawiono: Problemy z zamrażaniem ekranu
* Naprawiono: Zwiększone bezpieczeństwo
* Naprawiono: Czasami mogą pojawić się zduplikowane dołączone konta
* Poprawiono: Obiekt blob z niezdefiniowanym typem zawartości może wygenerować wyjątek
* Naprawiono: Otwarcie Panelu zapytań przy pustej tabeli nie było możliwe
* Naprawiono: Zmienia się błąd w wyszukiwaniu
* Naprawiono: Zwiększono liczbę załadowanych zasobów z 50 do 100 po kliknięciu przycisku "Załaduj więcej"
* Naprawiono: Po pierwszym uruchomieniu, jeśli konto jest zalogowane, domyślnie wybieramy wszystkie subskrypcje dla tego konta.

#### <a name="known-issues"></a>Znane problemy

* Ta wersja Eksploratora magazynu nie działa na Ubuntu 14.04
* Aby otworzyć wiele kart dla tego samego zasobu, nie klikaj stale tego samego zasobu. Kliknij inny zasób, a następnie wróć, a następnie kliknij oryginalny zasób, aby otworzyć go ponownie w innej karcie
* Szybki dostęp działa tylko z elementami opartymi na subskrypcji. Zasoby lokalne lub zasoby dołączone za pośrednictwem klucza lub tokenu Sygnatury dostępu Współdzielonego nie są obsługiwane w tej wersji
* Przejście do zasobu docelowego może potrwać kilka sekund, w zależności od liczby posiadanego zasobu
* Posiadanie więcej niż 3 grup obiektów blob lub plików przesyłanych w tym samym czasie może spowodować błędy
* Uchwyty wyszukiwania przeszukują około 50 000 węzłów - po tym, wydajność może mieć wpływ lub może powodować nieobsługiwanie się wyjątku

03.10.2016
### <a name="version-085"></a>Wersja 0.8.5

#### <a name="new"></a>Nowa

* Teraz można używać kluczy SYGNATURY DOSTĘPU WSPÓŁDZIELONEGO generowanego przez portal do dołączania do kont magazynu i zasobów

#### <a name="fixes"></a>Poprawki

* Naprawiono: Stan wyścigu podczas wyszukiwania czasami powodował, że węzły stały się nierozszerwalne
* Naprawiono: "Korzystanie z protokołu HTTP" nie działa podczas łączenia się z kontami magazynu z nazwą konta i kluczem
* Naprawiono: Klawisze SAS (specjalnie wygenerowane przez portal) zwracają błąd "końcowego ukośnika".
* Naprawiono: Problemy z importem tabeli
    * Czasami klucz partycji i klucz wiersza zostały odwrócone
    * Nie można odczytać "null" klucze partycji

#### <a name="known-issues"></a>Znane problemy

* Uchwyty wyszukiwania przeszukują około 50 000 węzłów - po tym, wydajność może mieć wpływ
* Usługa Azure Stack obecnie nie obsługuje plików, więc próba rozwiń Pliki spowoduje wyświetlenie błędu

09/12/2016
### <a name="version-084"></a>Wersja 0.8.4

>[!VIDEO https://www.youtube.com/embed/cr5tOGyGrIQ?ecver=1]

#### <a name="new"></a>Nowa

* Generowanie bezpośrednich łączy do kont magazynu, kontenerów, kolejek, tabel lub udziałów plików w celu udostępniania i łatwego dostępu do zasobów — obsługa systemu Windows i Mac OS
* Wyszukiwanie kontenerów obiektów blob, tabel, kolejek, udziałów plików lub kont magazynu w polu wyszukiwania
* Teraz można grupować klauzule w konstruktorze zapytań tabeli
* Zmienianie nazw kontenerów obiektów blob i kopiowanie/wklejanie ich, udziałów plików, tabel, obiektów blob, folderów obiektów blob, plików i katalogów z kont i kontenerów dołączonych do sas
* Zmiana nazwy i kopiowanie kontenerów obiektów blob i udziałów plików zachowuje teraz właściwości i metadane

#### <a name="fixes"></a>Poprawki

* Naprawiono: nie można edytować elementów tabeli, jeśli zawierają one właściwości logiczne lub binarne

#### <a name="known-issues"></a>Znane problemy

* Uchwyty wyszukiwania przeszukują około 50 000 węzłów - po tym, wydajność może mieć wpływ

08/03/2016
### <a name="version-083"></a>Wersja 0.8.3

>[!VIDEO https://www.youtube.com/embed/HeGW-jkSd9Y?ecver=1]

#### <a name="new"></a>Nowa

* Zmienianie nazw kontenerów, tabel, udziałów plików
* Ulepszone środowisko konstruktora zapytań
* Możliwość zapisywania i ładowania zapytań
* Bezpośrednie łącza do kont magazynu lub kontenerów, kolejek, tabel lub udziałów plików w celu udostępniania i łatwego uzyskiwania dostępu do zasobów (obsługa tylko dla systemu Windows — macOS już wkrótce!)
* Możliwość zarządzania i konfigurowania reguł CORS

#### <a name="fixes"></a>Poprawki

* Naprawiono: Konta Microsoft wymagają ponownego uwierzytelnienia co 8-12 godzin

#### <a name="known-issues"></a>Znane problemy

* Czasami interfejs użytkownika może wydawać się zablokowany - maksymalizacja okna pomaga rozwiązać ten problem
* Instalacja systemu macOS może wymagać uprawnień z podwyższonym poziomem uprawnień
* Panel Ustawień konta może oznaczać konieczność ponownego wprowadzenia poświadczeń w celu filtrowania subskrypcji
* Zmiana nazwy udziałów plików, kontenerów obiektów blob i tabel nie zachowuje metadanych ani innych właściwości w kontenerze, takich jak przydział udziału plików, publiczny poziom dostępu lub zasady dostępu
* Zmiana nazwy obiektów blob (indywidualnie lub wewnątrz kontenera obiektów blob o zmienionej nazwie) nie zachowuje migawek. Wszystkie inne właściwości i metadane obiektów blob, plików i encji są zachowywane podczas zmiany nazwy
* Kopiowanie lub zmienianie nazw zasobów nie działa na kontach dołączonych do usługi SAS

07/07/2016
### <a name="version-082"></a>Wersja 0.8.2

>[!VIDEO https://www.youtube.com/embed/nYgKbRUNYZA?ecver=1]

#### <a name="new"></a>Nowa

* Konta magazynu są pogrupowane według subskrypcji; magazyn deweloperów i zasoby dołączone za pośrednictwem klucza lub sygnatury dostępu Współdzielonego są wyświetlane w węźle (Lokalny i Dołączony)
* Wylogowywanie się z kont w panelu "Ustawienia konta platformy Azure"
* Konfigurowanie ustawień serwera proxy w celu włączania logowania i zarządzania nim
* Tworzenie i przerywanie dzierżaw obiektów blob
* Otwieranie kontenerów obiektów blob, kolejek, tabel i plików za pomocą jednego kliknięcia

#### <a name="fixes"></a>Poprawki

* Naprawiono: wiadomości kolejek wstawianych za pomocą bibliotek .NET lub Java nie są poprawnie dekodowane z base64
* Poprawiono: tabele $metrics nie są wyświetlane dla kont magazynu obiektów blob
* Naprawiono: węzeł tabel nie działa w magazynie lokalnym (rozwojowym)

#### <a name="known-issues"></a>Znane problemy

* Instalacja systemu macOS może wymagać uprawnień z podwyższonym poziomem uprawnień

06/15/2016
### <a name="version-080"></a>Wersja 0.8.0

>[!VIDEO https://www.youtube.com/embed/ycfQhKztSIY?ecver=1]

>[!VIDEO https://www.youtube.com/embed/k4_kOUCZ0WA?ecver=1]

>[!VIDEO https://www.youtube.com/embed/3zEXJcGdl_k?ecver=1]

#### <a name="new"></a>Nowa

* Obsługa udziału plików: przeglądanie, przesyłanie, pobieranie, kopiowanie plików i katalogów, identyfikatory URI sygnatury dostępu Współdzielonego (tworzenie i łączenie)
* Ulepszone środowisko użytkownika w zakresie łączenia się z pamięcią Masowego dostępu z kluczami URI lub kluczami kont
* Eksportowanie wyników kwerendy tabeli
* Ponowne kolejność i dostosowywanie kolumn tabeli
* Wyświetlanie kontenerów obiektów blob $logs i tabel $metrics dla kont magazynu z włączonymi metrykami
* Ulepszone zachowanie eksportu i importu, teraz zawiera typ wartości właściwości

#### <a name="fixes"></a>Poprawki

* Poprawiono: przesyłanie lub pobieranie dużych obiektów blob może spowodować niekompletne przesyłanie/pobieranie
* Naprawiono: edycja, dodawanie lub importowanie encji z wartością ciągu numerycznego ("1") spowoduje przekonwertowanie jej na podwójną
* Naprawiono: Nie można rozwinąć węzła tabeli w lokalnym środowisku programistycznym

#### <a name="known-issues"></a>Znane problemy

* tabele $metrics nie są widoczne dla kont magazynu obiektów Blob
* Komunikaty kolejki dodane programowo mogą nie być wyświetlane poprawnie, jeśli wiadomości są zakodowane przy użyciu kodowania Base64

05/17/2016
### <a name="version-07201605090"></a>Wersja 0.7.20160509.0

#### <a name="new"></a>Nowa

* Lepsza obsługa błędów w przypadku awarii aplikacji

#### <a name="fixes"></a>Poprawki

* Naprawiono błąd, który powodował, że wiadomości paska informacyjnego czasami nie pojawiały się, gdy wymagane były poświadczenia logowania

#### <a name="known-issues"></a>Znane problemy

* Tabele: Dodawanie, edytowanie lub importowanie encji, która ma właściwość o niejednoznacznej wartości liczbowej, takiej jak "1" lub "1.0", a użytkownik próbuje wysłać ją jako `Edm.String`, wartość powróci za pośrednictwem interfejsu API klienta jako Edm.Double

03/31/2016

### <a name="version-07201603250"></a>Wersja 0.7.20160325.0

>[!VIDEO https://www.youtube.com/embed/imbgBRHX65A?ecver=1]

>[!VIDEO https://www.youtube.com/embed/ceX-P8XZ-s8?ecver=1]

#### <a name="new"></a>Nowa

* Obsługa tabel: wyświetlanie, wykonywanie zapytań, eksportowanie, importowanie i operacje CRUD dla jednostek
* Obsługa kolejki: wyświetlanie, dodawanie, usuwanie kolejek
* Generowanie identyfikatorów URI sygnatury dostępu Współdzielonego dla kont magazynu
* Łączenie się z kontami magazynu za pomocą identyfikatorów URI sygnatury dostępu Współdzielonego
* Aktualizowanie powiadomień o przyszłych aktualizacjach Eksploratora magazynu
* Zaktualizowany wygląd i działanie

#### <a name="fixes"></a>Poprawki

* Ulepszenia wydajności i niezawodności

### <a name="known-issues-amp-mitigations"></a>Znane &amp; problemy zaradcze

* Pobieranie dużych plików blob nie działa poprawnie - zalecamy użycie AzCopy podczas rozwiązywania tego problemu
* Poświadczenia konta nie zostaną pobrane ani zapisane w pamięci podręcznej, jeśli nie można odnaleźć folderu macierzystego lub nie można ich zapisać
* Jeśli dodajemy, edytujemy lub importujemy encję, która ma właściwość o niejednoznacznej wartości liczbowej, takiej jak "1" lub "1.0", a użytkownik spróbuje wysłać ją jako `Edm.String`, wartość powróci za pośrednictwem interfejsu API klienta jako Edm.Double
* Podczas importowania plików CSV z rekordami wielowierszowymi dane mogą być posiekane lub

02/03/2016

### <a name="version-07201601291"></a>Wersja 0.7.20160129.1

#### <a name="fixes"></a>Poprawki

* Zwiększona ogólna wydajność podczas przesyłania, pobierania i kopiowania obiektów blob

01/14/2016

### <a name="version-07201601050"></a>Wersja 0.7.20160105.0

#### <a name="new"></a>Nowa

* Obsługa Linuksa (funkcje parzystości z OSX)
* Dodawanie kontenerów obiektów blob z kluczem Sygnatury dostępu współdzielonego (SAS)
* Dodawanie kont magazynu dla platformy Azure China 21Vianet
* Dodawanie kont magazynu z niestandardowymi punktami końcowymi
* Otwieranie i wyświetlanie tekstu zawartości i obiektów blob obrazów
* Wyświetlanie i edytowanie właściwości obiektów blob i metadanych

#### <a name="fixes"></a>Poprawki

* Naprawiono: przesyłanie lub pobieranie dużej liczby obiektów blob (500+) może czasami powodować, że aplikacja ma biały ekran
* Naprawiono: podczas ustawiania poziomu dostępu publicznego kontenera obiektów blob nowa wartość nie jest aktualizowana, dopóki nie ustawisz ponownie fokusu w kontenerze. Ponadto okno dialogowe zawsze domyślnie "Brak dostępu publicznego", a nie rzeczywista bieżąca wartość.
* Lepsza ogólna obsługa klawiatury/dostępności i interfejsu użytkownika
* Breadcrumbs historia tekst owija, gdy jest długa z białymi spacjami
* Okno dialogowe SAS obsługuje sprawdzanie poprawności danych wejściowych
* Magazyn lokalny jest nadal dostępny, nawet jeśli poświadczenia użytkownika wygasły
* Po usunięciu otwartego kontenera obiektów blob eksplorator obiektów blob po prawej stronie jest zamknięty

#### <a name="known-issues"></a>Znane problemy

* Instalacja Linuksa wymaga wersji GCC zaktualizowanej lub uaktualnionej - kroki uaktualnienia są poniżej:
    * `sudo add-apt-repository ppa:ubuntu-toolchain-r/test`
    * `sudo apt-get update`
    * `sudo apt-get upgrade`
    * `sudo apt-get dist-upgrade`

11/18/2015
### <a name="version-07201511160"></a>Wersja 0.7.20151116.0

#### <a name="new"></a>Nowa

* Wersje systemów macOS i Windows
* Zaloguj się, aby wyświetlić swoje konta magazynu — użyj konta organizacyjnego, konta Microsoft, 2FA itp.
* Lokalna pamięć masowa (użyj emulatora magazynu, tylko dla systemu Windows)
* Usługa Azure Resource Manager i klasyczna obsługa zasobów
* Tworzenie i usuwanie obiektów blob, kolejek lub tabel
* Wyszukiwanie określonych obiektów blob, kolejek lub tabel
* Eksplorowanie zawartości kontenerów obiektów blob
* Wyświetlanie katalogów i poruszanie się po ich
* Przekazywanie, pobieranie i usuwanie obiektów blob i folderów
* Wyświetlanie i edytowanie właściwości obiektów blob i metadanych
* Generowanie kluczy sygnatury dostępu Współ
* Zarządzanie zasadami dostępu przechowywane (SAP) i tworzenie ich
* Wyszukiwanie obiektów blob według prefiksu
* Przeciągnij 'n upuść pliki, aby przesłać lub pobrać

#### <a name="known-issues"></a>Znane problemy

* Podczas ustawiania poziomu dostępu publicznego kontenera obiektów blob nowa wartość nie jest aktualizowana, dopóki nie zostanie ponownie ustawiona fokus na kontenerze
* Po otwarciu okna dialogowego w celu ustawienia poziomu dostępu publicznego zawsze jest wyświetlany "Brak dostępu publicznego" jako wartość domyślna, a nie rzeczywista bieżąca wartość
* Nie można zmienić nazwy pobranych obiektów blob
* Wpisy dziennika aktywności czasami zostają "utknięte" w stanie w toku, gdy wystąpi błąd, a błąd nie jest wyświetlany
* Czasami ulega awarii lub staje się całkowicie biały podczas próby przesłania lub pobrania dużej liczby obiektów blob
* Czasami anulowanie operacji kopiowania nie działa
* Podczas tworzenia kontenera (obiekt blob/queue/table), jeśli wprowadzona zostanie nieprawidłowa nazwa i przystąpisz do utworzenia innego w ramach innego typu kontenera, nie można ustawić fokusu na nowy typ
* Nie można utworzyć nowego folderu ani zmienić nazwy folderu




[2]: https://support.microsoft.com/en-us/help/4021389/storage-explorer-troubleshooting-guide
[3]: vs-azure-tools-storage-manage-with-storage-explorer.md
