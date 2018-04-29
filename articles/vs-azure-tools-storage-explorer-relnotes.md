---
title: Informacje o wersji Eksploratora magazynu Microsoft Azure
description: Informacje o wersji dla Eksploratora magazynu Microsoft Azure
services: storage
documentationcenter: na
author: cawa
manager: paulyuk
editor: ''
ms.assetid: ''
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/31/2017
ms.author: cawa
ms.openlocfilehash: 8ec74f69d2de7b167fcc66d0e2499d052f0bf18e
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
---
# <a name="microsoft-azure-storage-explorer-release-notes"></a>Informacje o wersji Eksploratora magazynu Microsoft Azure

Ten artykuł zawiera zlecenia, które uwagi 0.9.6 Eksploratora usługi Azure Storage (wersja zapoznawcza) wersji, a także informacje o wersji w poprzednich wersjach.

[Eksplorator magazynu Microsoft Azure](./vs-azure-tools-storage-manage-with-storage-explorer.md) jest aplikacją autonomiczną, która pozwala łatwo pracować z danymi usługi Azure Storage w systemie Windows, macOS i Linux.

## <a name="version-096"></a>Wersja 0.9.6
02/28/2018

### <a name="download-azure-storage-explorer-096-preview"></a>Pobierz Eksploratora usługi Azure Storage 0.9.6 (wersja zapoznawcza)
- [Eksplorator usługi Azure Storage 0.9.6 (wersja zapoznawcza) dla systemu Windows](https://go.microsoft.com/fwlink/?LinkId=708343)
- [Eksplorator usługi Azure Storage 0.9.6 (wersja zapoznawcza) dla komputerów Mac](https://go.microsoft.com/fwlink/?LinkId=708342)
- [Eksplorator usługi Azure Storage 0.9.6 (wersja zapoznawcza) dla systemu Linux](https://go.microsoft.com/fwlink/?LinkId=722418)

### <a name="fixes"></a>Poprawki
* Wystąpił problem uniemożliwił obiekty BLOB oczekiwanego/pliki są wymienione w edytorze. Problem został rozwiązany.
* Przełączanie między widokami migawki do wyświetlania elementów niepoprawnie przyczyną problemu. Problem został rozwiązany.

### <a name="known-issues"></a>Znane problemy
* Eksplorator usługi Storage nie obsługuje kont usług AD FS.
* Celem Azure stosu, przekazywanie pewne pliki jako uzupełnialnych obiektów blob może zakończyć się niepowodzeniem.
* Po kliknięciu przycisku "Anuluj" dla zadania, może upłynąć trochę czasu dla tego zadania anulować. Jest to spowodowane używamy rozwiązanie filtru Anuluj opisane [tutaj](https://github.com/Azure/azure-storage-node/issues/317).
* Jeśli wybierzesz nieprawidłowy certyfikat kodu PIN/karty inteligentnej, będzie konieczne ponowne uruchomienie w celu Eksploratora usługi Storage zapomnij tej decyzji.
* Panel ustawień konta mogą być wyświetlane, należy ponownie wprowadzić poświadczenia, aby filtrować subskrypcji.
* Zmiana nazwy obiektów blob (indywidualnie lub wewnątrz kontenera obiektów blob zmienionej nazwie) nie zostaną zachowane migawki. Wszystkie inne właściwości i metadanych dla obiektów blob, plików i jednostek są zachowywane podczas zmiany nazwy.
* Mimo że stosu Azure aktualnie nie obsługuje udziałów plików, węzła udziałów plików jest nadal wyświetlana na koncie dołączone magazynu Azure stosu.
* Powłoka elektronów wykorzystywane przez Eksploratora magazynu ma problemy z niektórych przyspieszanie sprzętowe procesora GPU (jednostka przetwarzania grafiki). Jeśli Eksploratora usługi Storage są wyświetlane puste okno główne (pusta), można spróbować uruchamianie Eksploratora usługi Storage z poziomu wiersza polecenia i wyłączanie przyspieszenie procesora GPU, dodając `--disable-gpu` przełącznika:

```
./StorageExplorer.exe --disable-gpu
```

* Dla użytkowników Ubuntu 14.04, konieczne będzie zapewnienia GCC jest aktualny — można to zrobić, uruchamiając następujące polecenia i ponownym uruchomieniu komputera:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Dla użytkowników Ubuntu 17.04 będą musieli zainstalować GConf — można to zrobić, uruchamiając następujące polecenia i ponownym uruchomieniu komputera:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="previous-releases"></a>Poprzednie wersje

* [Wersja 0.9.5](#version-095)
* [Wersja 0.9.4 i 0.9.3](#version-094-and-093)
* [Wersja 0.9.2](#version-092)
* [W wersji od 0.9.1 i 0.9.0](#version-091-and-090)
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

## <a name="version-095"></a>Wersja 0.9.5
2018-02/06

### <a name="download-azure-storage-explorer-095-preview"></a>Pobierz Eksploratora usługi Azure Storage 0.9.5 (wersja zapoznawcza)
- [Eksplorator usługi Azure Storage 0.9.5 (wersja zapoznawcza) dla systemu Windows](https://go.microsoft.com/fwlink/?LinkId=708343)
- [Eksplorator usługi Azure Storage 0.9.5 (wersja zapoznawcza) dla komputerów Mac](https://go.microsoft.com/fwlink/?LinkId=708342)
- [Eksplorator usługi Azure Storage 0.9.5 (wersja zapoznawcza) dla systemu Linux](https://go.microsoft.com/fwlink/?LinkId=722418)

### <a name="new"></a>Nowa

* Obsługa migawki w udziałach plików.
    * Utwórz i Zarządzaj migawek dla Twojego udziałów plików.
    * Łatwo przełączać widoki między migawki z udziałów plików, Ci poznać platformę.
    * Przywróć poprzednie wersje plików.
* Obsługa wersji zapoznawczej usługi Azure Data Lake Store:
    * Połącz do zasobów ADLS przez wiele kont.
    * Nawiązywanie połączenia i udostępnianie zasobów ADLS przy użyciu identyfikatorów URI ADL.
    * Wykonywanie cyklicznie operacji podstawowy plik lub folder.
    * PIN poszczególnych folderów do szybkiego dostępu.
    * Wyświetlanie statystyk folderu.

### <a name="fixes"></a>Poprawki
* Ulepszenia wydajności uruchamiania.
* Różne poprawki błędów.

### <a name="known-issues"></a>Znane problemy
* Eksplorator usługi Storage nie obsługuje kont usług AD FS.
* Celem Azure stosu, przekazywanie pewne pliki jako uzupełnialnych obiektów blob może zakończyć się niepowodzeniem.
* Po kliknięciu przycisku "Anuluj" dla zadania, może upłynąć trochę czasu dla tego zadania anulować. Jest to spowodowane używamy rozwiązanie filtru Anuluj opisane w tym miejscu.
* Jeśli wybierzesz nieprawidłowy certyfikat kodu PIN/karty inteligentnej, będzie konieczne ponowne uruchomienie w celu Eksploratora usługi Storage zapomnij tej decyzji.
* Panel ustawień konta mogą być wyświetlane, należy ponownie wprowadzić poświadczenia, aby filtrować subskrypcji.
* Zmiana nazwy obiektów blob (indywidualnie lub wewnątrz kontenera obiektów blob zmienionej nazwie) nie zostaną zachowane migawki. Wszystkie inne właściwości i metadanych dla obiektów blob, plików i jednostek są zachowywane podczas zmiany nazwy.
* Mimo że stosu Azure aktualnie nie obsługuje udziałów plików, węzła udziałów plików jest nadal wyświetlana na koncie dołączone magazynu Azure stosu.
* Powłoka elektronów wykorzystywane przez Eksploratora magazynu ma problemy z niektórych przyspieszanie sprzętowe procesora GPU (jednostka przetwarzania grafiki). Jeśli Eksploratora usługi Storage są wyświetlane puste okno główne (pusta), można spróbować uruchamianie Eksploratora usługi Storage z poziomu wiersza polecenia i wyłączanie przyspieszenie procesora GPU, dodając `--disable-gpu` przełącznika:

```
./StorageExplorer.exe --disable-gpu
```

* Dla użytkowników Ubuntu 14.04, konieczne będzie zapewnienia GCC jest aktualny — można to zrobić, uruchamiając następujące polecenia i ponownym uruchomieniu komputera:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Dla użytkowników Ubuntu 17.04 będą musieli zainstalować GConf — można to zrobić, uruchamiając następujące polecenia i ponownym uruchomieniu komputera:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-094-and-093"></a>Wersja 0.9.4 i 0.9.3
2018-01/21

### <a name="download-azure-storage-explorer-094-preview"></a>Pobierz Eksploratora usługi Azure Storage 0.9.4 (wersja zapoznawcza)
* [Pobierz Eksploratora usługi Azure Storage 0.9.4 (wersja zapoznawcza) dla systemu Windows](https://go.microsoft.com/fwlink/?LinkId=809306)
* [Pobierz Eksploratora usługi Azure Storage 0.9.4 (wersja zapoznawcza) dla komputerów Mac](https://go.microsoft.com/fwlink/?LinkId=809307)
* [Pobierz Eksploratora usługi Azure Storage 0.9.4 (wersja zapoznawcza) dla systemu Linux](https://go.microsoft.com/fwlink/?LinkId=809308)

### <a name="new"></a>Nowa
* Twoje istniejące okno Eksploratora magazynu będzie ponownie użyte podczas:
    * Otwieranie linki bezpośrednie generowane w Eksploratorze usługi Storage.
    * Otwieranie Eksploratora magazynu z portalu.
    * Otwarcie Eksploratora usługi Storage z rozszerzeniem kodzie VS magazynu Azure (wkrótce).
* Dodano możliwość Otwórz nowe okno Eksploratora usługi Storage z wewnątrz Eksploratora usługi Storage.
    * W systemie Windows jest opcja "Nowe okno", w Menu Plik i w menu kontekstowym paska zadań.
    * Dla komputerów Mac jest to opcja "Nowe okno" w Menu aplikacji.

### <a name="fixes"></a>Poprawki
* Rozwiązany problem z zabezpieczeniami. Uaktualnij do 0.9.4 najwcześniejszą wygodne.
* Starego działania zostały nie odpowiednio są czyszczone. To wpływ na wydajność długotrwałych zadań. One są teraz czyszczony poprawnie.
* Akcje związane z dużą liczbą plików i katalogów spowoduje, że od czasu do czasu Eksploratora magazynu zablokować. Żądania do platformy Azure i udziały plików są teraz trottled ograniczenie użycia zasobów systemowych.

### <a name="known-issues"></a>Znane problemy
* Eksplorator usługi Storage nie obsługuje kont usług AD FS.
* Skróty klawiaturowe "Zarządzanie kontem widok" i "Widok Eksploratora" powinna być Ctrl / Cmd + Shift + E i Ctrl / Cmd + Shift + A odpowiednio.
* Celem Azure stosu, przekazywanie pewne pliki jako uzupełnialnych obiektów blob może zakończyć się niepowodzeniem.
* Po kliknięciu przycisku "Anuluj" dla zadania, może upłynąć trochę czasu dla tego zadania anulować. Jest to spowodowane używamy rozwiązanie filtru Anuluj opisane w tym miejscu.
* Jeśli wybierzesz nieprawidłowy certyfikat kodu PIN/karty inteligentnej, będzie konieczne ponowne uruchomienie w celu Eksploratora usługi Storage zapomnij tej decyzji.
* Panel ustawień konta mogą być wyświetlane, należy ponownie wprowadzić poświadczenia, aby filtrować subskrypcji.
* Zmiana nazwy obiektów blob (indywidualnie lub wewnątrz kontenera obiektów blob zmienionej nazwie) nie zostaną zachowane migawki. Wszystkie inne właściwości i metadanych dla obiektów blob, plików i jednostek są zachowywane podczas zmiany nazwy.
* Mimo że stosu Azure aktualnie nie obsługuje udziałów plików, węzła udziałów plików jest nadal wyświetlana na koncie dołączone magazynu Azure stosu.
* Powłoka elektronów wykorzystywane przez Eksploratora magazynu ma problemy z niektórych przyspieszanie sprzętowe procesora GPU (jednostka przetwarzania grafiki). Jeśli Eksploratora usługi Storage są wyświetlane puste okno główne (pusta), można spróbować uruchamianie Eksploratora usługi Storage z poziomu wiersza polecenia i wyłączanie przyspieszenie procesora GPU, dodając `--disable-gpu` przełącznika:
```
./StorageExplorer --disable-gpu
```
* Dla użytkowników Ubuntu 14.04, konieczne będzie zapewnienia GCC jest aktualny — można to zrobić, uruchamiając następujące polecenia i ponownym uruchomieniu komputera:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Dla użytkowników Ubuntu 17.04 będą musieli zainstalować GConf — można to zrobić, uruchamiając następujące polecenia i ponownym uruchomieniu komputera:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-092"></a>Wersja 0.9.2
11/01/2017

### <a name="hotfixes"></a>Poprawki
* Nieoczekiwane dane zmiany były możliwe podczas edycji wartości Edm.DateTime dla jednostek tabeli w zależności od tego, w lokalnej strefie czasowej. Edytor używa teraz pola tekstowego, dając dokładne, spójne kontrolę nad Edm.DateTime wartości.
* Przekazywanie/pobieranie grupy obiektów blob, gdy podłączonych do nazwy i klucza nie może uruchomić. Problem został rozwiązany.
* Wcześniej Eksploratora usługi Storage czy tylko monitować ponownego uwierzytelnienia stare konto, jeśli jeden lub więcej subskrypcji konta została wybrana. Teraz Eksploratora usługi Storage spowoduje wyświetlenie monitu nawet wtedy, gdy konto pełni odfiltrowane.
* Błędna domeny punktów końcowych dla usługi Azure instytucji rządowych Stanów Zjednoczonych. Został rozwiązany.
* Przycisk Zastosuj w panelu Zarządzanie kontami jest czasami trudno kliknij przycisk. Nie powinno to nastąpić.

### <a name="new"></a>Nowa
* Obsługa podglądu bazy danych Azure rozwiązania Cosmos.
    * [Dokumentację w trybie online](./cosmos-db/storage-explorer.md)
    * Tworzenie baz danych i kolekcji
    * Manipulowanie danymi
    * Zapytanie, tworzenie lub usuwanie dokumentów
    * Zaktualizuj wyzwalaczy, funkcji zdefiniowanych przez użytkownika lub procedury składowane
    * Aby nawiązać połączenie i zarządzanie bazami danych użyj parametrów połączenia
* Zwiększona wydajność przekazywania/pobieranie wiele małych obiektów blob.
* Dodać akcję "Wykonaj ponownie wszystkie", jeśli występują błędy w grupie przekazywanie obiektu blob lub grupa pobierania obiektu blob.
* Eksploratora usługi Storage zatrzyma teraz iteracji podczas pobierania/wysyłania obiektów blob, jeśli wykryje, że połączenie sieciowe zostało utracone. Następnie można wznowić iteracji, po ponownym nawiązaniu połączenia sieciowego.
* Dodano możliwość do "Zamknij", "Zamknij inne" i "Zamknij" karty za pomocą menu kontekstowego.
* Eksploratora usługi Storage obecnie korzysta natywnych okien dialogowych i menu kontekstowe macierzystego.
* Eksplorator usługi Storage jest teraz łatwiej dostępne. Ulepszenia obejmują:
    * Obsługę czytnika ulepszone ekranu, NVDA w systemie Windows oraz VoiceOver dla komputerów Mac
    * Tworzenie motywów ulepszone duży kontrast
    * Poprawki fokus klawiatury klawisza TAB i klawiatury

### <a name="fixes"></a>Poprawki
* Jeśli próbowano otworzyć lub pobieranie obiektu blob o nieprawidłowej nazwie pliku systemu Windows, operacja nie powiedzie się. Eksplorator usługi Storage teraz wykrywania, czy nazwa obiektu blob jest nieprawidłowy i zapytaj, jeśli chcesz kodować je albo pominąć obiektu blob. Eksplorator usługi Storage wykryje również, jeśli nazwa pliku wydaje się być kodowany i pytanie, jeśli chcesz odkodować go przed przekazaniem.
* Podczas przekazywania obiektów blob edytor dla kontenera obiektów blob docelowego będą czasami nie prawidłowo odświeżenia. Problem został rozwiązany.
* Uwzględniona obsługę kilku formularzy Parametry połączenia i identyfikatory URI sygnatury dostępu Współdzielonego. Możemy usunąć wszystkie znane problemy, ale jeśli dalsze wystąpią problemy dotyczące Wyślij opinię.
* Powiadomienie o aktualizacji został przerwany dla niektórych użytkowników w 0.9.0. Ten problem został rozwiązany i tych dotyczy ten błąd, należy ręcznie pobrać najnowszą wersję Eksploratora usługi Storage [tutaj](https://azure.microsoft.com/en-us/features/storage-explorer/).

### <a name="known-issues"></a>Znane problemy
* Eksplorator usługi Storage nie obsługuje kont usług AD FS.
* Skróty klawiaturowe "Zarządzanie kontem widok" i "Widok Eksploratora" powinna być Ctrl / Cmd + Shift + E i Ctrl / Cmd + Shift + A odpowiednio.
* Celem Azure stosu, przekazywanie pewne pliki jako uzupełnialnych obiektów blob może zakończyć się niepowodzeniem.
* Po kliknięciu przycisku "Anuluj" dla zadania, może upłynąć trochę czasu dla tego zadania anulować. Jest to spowodowane używamy rozwiązanie filtru Anuluj opisane w tym miejscu.
* Jeśli wybierzesz nieprawidłowy certyfikat kodu PIN/karty inteligentnej, będzie konieczne ponowne uruchomienie w celu Eksploratora usługi Storage zapomnij tej decyzji.
* Panel ustawień konta mogą być wyświetlane, należy ponownie wprowadzić poświadczenia, aby filtrować subskrypcji.
* Zmiana nazwy obiektów blob (indywidualnie lub wewnątrz kontenera obiektów blob zmienionej nazwie) nie zostaną zachowane migawki. Wszystkie inne właściwości i metadanych dla obiektów blob, plików i jednostek są zachowywane podczas zmiany nazwy.
* Mimo że stosu Azure aktualnie nie obsługuje udziałów plików, węzła udziałów plików jest nadal wyświetlana na koncie dołączone magazynu Azure stosu.
* Powłoka elektronów wykorzystywane przez Eksploratora magazynu ma problemy z niektórych przyspieszanie sprzętowe procesora GPU (jednostka przetwarzania grafiki). Jeśli Eksploratora usługi Storage są wyświetlane puste okno główne (pusta), można spróbować uruchamianie Eksploratora usługi Storage z poziomu wiersza polecenia i wyłączanie przyspieszenie procesora GPU, dodając `--disable-gpu` przełącznika:
```
./StorageExplorer --disable-gpu
```
* Dla użytkowników Ubuntu 14.04, konieczne będzie zapewnienia GCC jest aktualny — można to zrobić, uruchamiając następujące polecenia i ponownym uruchomieniu komputera:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Dla użytkowników Ubuntu 17.04 będą musieli zainstalować GConf — można to zrobić, uruchamiając następujące polecenia i ponownym uruchomieniu komputera:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-091-and-090"></a>W wersji od 0.9.1 i 0.9.0
10/20/2017
### <a name="new"></a>Nowa
* Obsługa podglądu bazy danych Azure rozwiązania Cosmos.
    * [Dokumentację w trybie online](./cosmos-db/storage-explorer.md)
    * Tworzenie baz danych i kolekcji
    * Manipulowanie danymi
    * Zapytanie, tworzenie lub usuwanie dokumentów
    * Zaktualizuj wyzwalaczy, funkcji zdefiniowanych przez użytkownika lub procedury składowane
    * Aby nawiązać połączenie i zarządzanie bazami danych użyj parametrów połączenia
* Zwiększona wydajność przekazywania/pobieranie wiele małych obiektów blob.
* Dodać akcję "Wykonaj ponownie wszystkie", jeśli występują błędy w grupie przekazywanie obiektu blob lub grupa pobierania obiektu blob.
* Eksploratora usługi Storage zatrzyma teraz iteracji podczas pobierania/wysyłania obiektów blob, jeśli wykryje, że połączenie sieciowe zostało utracone. Następnie można wznowić iteracji, po ponownym nawiązaniu połączenia sieciowego.
* Dodano możliwość do "Zamknij", "Zamknij inne" i "Zamknij" karty za pomocą menu kontekstowego.
* Eksploratora usługi Storage obecnie korzysta natywnych okien dialogowych i menu kontekstowe macierzystego.
* Eksplorator usługi Storage jest teraz łatwiej dostępne. Ulepszenia obejmują:
    * Obsługę czytnika ulepszone ekranu, NVDA w systemie Windows oraz VoiceOver dla komputerów Mac
    * Tworzenie motywów ulepszone duży kontrast
    * Poprawki fokus klawiatury klawisza TAB i klawiatury

### <a name="fixes"></a>Poprawki
* Jeśli próbowano otworzyć lub pobieranie obiektu blob o nieprawidłowej nazwie pliku systemu Windows, operacja nie powiedzie się. Eksplorator usługi Storage teraz wykrywania, czy nazwa obiektu blob jest nieprawidłowy i zapytaj, jeśli chcesz kodować je albo pominąć obiektu blob. Eksplorator usługi Storage wykryje również, jeśli nazwa pliku wydaje się być kodowany i pytanie, jeśli chcesz odkodować go przed przekazaniem.
* Podczas przekazywania obiektów blob edytor dla kontenera obiektów blob docelowego będą czasami nie prawidłowo odświeżenia. Problem został rozwiązany.
* Uwzględniona obsługę kilku formularzy Parametry połączenia i identyfikatory URI sygnatury dostępu Współdzielonego. Możemy usunąć wszystkie znane problemy, ale jeśli dalsze wystąpią problemy dotyczące Wyślij opinię.
* Powiadomienie o aktualizacji został przerwany dla niektórych użytkowników w 0.9.0. Ten problem został rozwiązany i tych dotyczy ten błąd, należy ręcznie pobrać najnowszą wersję Eksploratora usługi Storage [tutaj](https://azure.microsoft.com/en-us/features/storage-explorer/)

### <a name="known-issues"></a>Znane problemy
* Eksplorator usługi Storage nie obsługuje kont usług AD FS.
* Skróty klawiaturowe "Zarządzanie kontem widok" i "Widok Eksploratora" powinna być Ctrl / Cmd + Shift + E i Ctrl / Cmd + Shift + A odpowiednio.
* Celem Azure stosu, przekazywanie pewne pliki jako uzupełnialnych obiektów blob może zakończyć się niepowodzeniem.
* Po kliknięciu przycisku "Anuluj" dla zadania, może upłynąć trochę czasu dla tego zadania anulować. Jest to spowodowane używamy rozwiązanie filtru Anuluj opisane w tym miejscu.
* Jeśli wybierzesz nieprawidłowy certyfikat kodu PIN/karty inteligentnej, będzie konieczne ponowne uruchomienie w celu Eksploratora usługi Storage zapomnij tej decyzji.
* Panel ustawień konta mogą być wyświetlane, należy ponownie wprowadzić poświadczenia, aby filtrować subskrypcji.
* Zmiana nazwy obiektów blob (indywidualnie lub wewnątrz kontenera obiektów blob zmienionej nazwie) nie zostaną zachowane migawki. Wszystkie inne właściwości i metadanych dla obiektów blob, plików i jednostek są zachowywane podczas zmiany nazwy.
* Mimo że stosu Azure aktualnie nie obsługuje udziałów plików, węzła udziałów plików jest nadal wyświetlana na koncie dołączone magazynu Azure stosu.
* Powłoka elektronów wykorzystywane przez Eksploratora magazynu ma problemy z niektórych przyspieszanie sprzętowe procesora GPU (jednostka przetwarzania grafiki). Jeśli Eksploratora usługi Storage są wyświetlane puste okno główne (pusta), można spróbować uruchamianie Eksploratora usługi Storage z poziomu wiersza polecenia i wyłączanie przyspieszenie procesora GPU, dodając `--disable-gpu` przełącznika:
```
./StorageExplorer --disable-gpu
```
* Dla użytkowników Ubuntu 14.04, konieczne będzie zapewnienia GCC jest aktualny — można to zrobić, uruchamiając następujące polecenia i ponownym uruchomieniu komputera:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Dla użytkowników Ubuntu 17.04 będą musieli zainstalować GConf — można to zrobić, uruchamiając następujące polecenia i ponownym uruchomieniu komputera:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-0816"></a>Wersja 0.8.16
8/21/2017

### <a name="new"></a>Nowa
* Po otwarciu obiektu blob Eksploratora usługi Storage spowoduje wyświetlenie monitu o Przekaż pobrany plik, jeśli zmiana zostaje wykryta
* Rozszerzony stos Azure logowania
* Zwiększono wydajność przekazywania/pobieranie wiele małych plików w tym samym czasie


### <a name="fixes"></a>Poprawki
* Dla niektórych typów obiektów blob wybierając pozycję "replace" podczas przekazywania konflikt czasami spowoduje przekazywania uruchamiany ponownie.
* W wersji 0.8.15 przekazywania czasami spowoduje zatrzymania 99%.
* Przekazywanie plików do udziału plików, jeśli chcesz przekazać do katalogu, które nie zostały jeszcze istnieje, przekazania może zakończyć się niepowodzeniem.
* Eksploratora usługi Storage został niepoprawnie generowania sygnatury czasowe dla sygnatur dostępu współdzielonego i tabeli zapytania.


### <a name="known-issues"></a>Znane problemy
* Przy użyciu nazwy i parametry połączenia klucza aktualnie nie działa. Zostanie on rozwiązany w następnej wersji. Do tego czasu można dołączyć z nazwy i klucza.
* Próba otwarcia pliku o nieprawidłowej nazwie pliku systemu Windows, pobierania spowoduje błąd: nie odnaleziono pliku.
* Po kliknięciu przycisku "Anuluj" dla zadania, może upłynąć trochę czasu dla tego zadania anulować. Jest to ograniczenie biblioteki węzła magazynu Azure.
* Po zakończeniu przekazywania obiektów blob, jest odświeżany kartę, który zainicjował przekazywania. Została zmieniona z poprzedniego działania, a także spowoduje przyjmowana z powrotem do głównego kontenera, które znajdują się w.
* Jeśli wybierzesz nieprawidłowy certyfikat kodu PIN/karty inteligentnej, będzie konieczne ponowne uruchomienie w celu Eksploratora usługi Storage zapomnij tej decyzji.
* Panel ustawień konta mogą być wyświetlane, należy ponownie wprowadzić poświadczenia, aby filtrować subskrypcji.
* Zmiana nazwy obiektów blob (indywidualnie lub wewnątrz kontenera obiektów blob zmienionej nazwie) nie zostaną zachowane migawki. Wszystkie inne właściwości i metadanych dla obiektów blob, plików i jednostek są zachowywane podczas zmiany nazwy.
* Mimo że stosu Azure aktualnie nie obsługuje udziałów plików, węzła udziałów plików jest nadal wyświetlana na koncie dołączone magazynu Azure stosu.
* Dla użytkowników Ubuntu 14.04, konieczne będzie zapewnienia GCC jest aktualny — można to zrobić, uruchamiając następujące polecenia i ponownym uruchomieniu komputera:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Dla użytkowników Ubuntu 17.04 będą musieli zainstalować GConf — można to zrobić, uruchamiając następujące polecenia i ponownym uruchomieniu komputera:

    ```
    sudo apt-get install libgconf-2-4
    ```

### <a name="version-0814"></a>Wersja 0.8.14
06/22/2017

### <a name="new"></a>Nowa

* Zaktualizowana wersja elektronów do 1.7.2, aby można było skorzystać z kilku aktualizacji krytycznych
* Możesz teraz szybko uzyskiwać dostęp online przewodnik rozwiązywania problemów z menu Pomoc
* Rozwiązywanie problemów z Eksploratora usługi Storage [przewodnik][2]
* [Instrukcje] [ 3] na nawiązywanie połączenia z subskrypcją platformy Azure stosu

### <a name="known-issues"></a>Znane problemy

* Przyciski w oknie dialogowym potwierdzenia usunięcia folderu nie zarejestrować kliknięć myszą w systemie Linux. Obejście tego problemu jest użycie klawisza Enter
* Po wybraniu zły certyfikat karty inteligentnej na kodu PIN/następnie konieczne będzie ponowne uruchomienie w celu Eksploratora usługi Storage zapomnij decyzji
* Mając więcej niż 3 grup obiektów blob lub przekazywania w tym samym czasie plików może powodować błędy
* Panel ustawień konta mogą być wyświetlane konieczne ponowne wprowadzenie poświadczeń, aby filtrować subskrypcji
* Zmiana nazwy obiektów blob (indywidualnie lub wewnątrz kontenera obiektów blob zmienionej nazwie) nie zostaną zachowane migawki. Wszystkie inne właściwości i metadanych dla obiektów blob, plików i jednostek są zachowywane podczas zmiany nazwy.
* Mimo że stosu Azure aktualnie nie obsługuje udziałów plików, węzła udziałów plików jest nadal wyświetlana na koncie dołączone magazynu Azure stosu.
* Ubuntu 14.04 instalacji wymaga wersji gcc zaktualizowane lub uaktualnione — kroki niezbędne do uaktualnienia jest poniżej:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

### <a name="version-0813"></a>Wersja 0.8.13
05/12/2017

#### <a name="new"></a>Nowa

* Rozwiązywanie problemów z Eksploratora usługi Storage [przewodnik][2]
* [Instrukcje] [ 3] na nawiązywanie połączenia z subskrypcją platformy Azure stosu

#### <a name="fixes"></a>Poprawki

* Stała: Przekazywanie pliku wypróbowana wysokiej spowodować błąd braku pamięci
* Stały: Można teraz zalogować się przy użyciu kodu PIN/za pomocą kart inteligentnych
* Stałe: Otwórz w portalu teraz współpracuje z Chińskiej wersji platformy Azure, platformy Azure w Niemczech Azure instytucji rządowych Stanów Zjednoczonych i stosu Azure
* Stała: Podczas przekazywania folder do kontenera obiektów blob, błąd "Niedozwolonej operacji" czasami wystąpiłyby
* Stałej: Zaznacz wszystko zostało wyłączone podczas zarządzania migawki
* Stały: Metadane obiektu blob podstawowej może spowodować zastąpienie po wyświetleniu właściwości jego migawek.

#### <a name="known-issues"></a>Znane problemy

* Po wybraniu zły certyfikat karty inteligentnej na kodu PIN/następnie konieczne będzie ponowne uruchomienie w celu Eksploratora usługi Storage zapomnij decyzji
* Gdy jest powiększony przychodzący lub wychodzący, poziom powiększenia na chwilę może zresetować domyślnego poziomu
* Mając więcej niż 3 grup obiektów blob lub przekazywania w tym samym czasie plików może powodować błędy
* Panel ustawień konta mogą być wyświetlane konieczne ponowne wprowadzenie poświadczeń, aby filtrować subskrypcji
* Zmiana nazwy obiektów blob (indywidualnie lub wewnątrz kontenera obiektów blob zmienionej nazwie) nie zostaną zachowane migawki. Wszystkie inne właściwości i metadanych dla obiektów blob, plików i jednostek są zachowywane podczas zmiany nazwy.
* Mimo że stosu Azure aktualnie nie obsługuje udziałów plików, węzła udziałów plików jest nadal wyświetlana na koncie dołączone magazynu Azure stosu.
* Ubuntu 14.04 instalacji wymaga wersji gcc zaktualizowane lub uaktualnione — kroki niezbędne do uaktualnienia jest poniżej:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```


### <a name="version-0812-and-0811-and-0810"></a>Wersja 0.8.12 i 0.8.11 i 0.8.10
04/07/2017

#### <a name="new"></a>Nowa

* Eksplorator usługi Storage teraz zostanie automatycznie zamknięte podczas instalacji aktualizacji z powiadomienie o aktualizacji
* Szybki dostęp w miejscu udostępnia udoskonalone środowisko do pracy z często używanych zasobów
* W edytorze kontenera obiektów Blob można przeglądać maszyny wirtualnej, które dzierżawionych obiektu blob należy do
* Teraz można zwinąć panelu po lewej stronie
* Odnajdywanie teraz odbywa się w tym samym czasie jako plik do pobrania
* Użyj statystyk w edytorach kontenera obiektów Blob, udziału plików i tabeli, aby wyświetlić rozmiar zasobu lub zaznaczenia
* Możesz teraz logowania do usługi Azure Active Directory (AAD) na podstawie konta Azure stosu.
* Możesz teraz przekazać pliki archiwum przekracza 32MB do kont magazynu w warstwie Premium
* Udoskonalona obsługa ułatwień dostępu
* Można teraz dodawać zaufanych Base-64 zakodowane certyfikatów X.509 SSL, przechodząc do edycji —&gt; certyfikaty SSL -&gt; importu certyfikatów

#### <a name="fixes"></a>Poprawki

* Stała: po odświeżeniu poświadczenia konta, widok drzewa czy czasami nie automatycznie odświeżenia
* Stała: Generowanie sygnatury dostępu Współdzielonego dla kolejki emulatora i tabele dadzą w wyniku nieprawidłowy adres URL
* Stały: konta premium magazynu teraz można rozszerzyć, gdy włączone jest serwer proxy
* Stałe: przycisk Zastosuj, na stronie Zarządzanie kont nie będzie działać gdyby 1 lub 0 konta zaznaczone
* Stały: przekazywanie obiektów blob, które wymagają rozwiązania konfliktu może zakończyć się niepowodzeniem — stała 0.8.11
* Stałe: wysyłanie opinii został przerwany w 0.8.11 — stała 0.8.12

#### <a name="known-issues"></a>Znane problemy

* Po uaktualnieniu do wersji 0.8.10, należy odświeżyć wszystkie swoje poświadczenia.
* Gdy jest powiększony przychodzący lub wychodzący, poziom powiększenia na chwilę może zresetować domyślnego poziomu.
* Mając więcej niż 3 grup obiektów blob lub przekazywania w tym samym czasie plików może powodować błędy.
* Panel ustawień konta mogą być wyświetlane, należy ponownie wprowadzić poświadczenia, aby filtrować subskrypcji.
* Zmiana nazwy obiektów blob (indywidualnie lub wewnątrz kontenera obiektów blob zmienionej nazwie) nie zostaną zachowane migawki. Wszystkie inne właściwości i metadanych dla obiektów blob, plików i jednostek są zachowywane podczas zmiany nazwy.
* Mimo że stosu Azure aktualnie nie obsługuje udziałów plików, węzła udziałów plików jest nadal wyświetlana na koncie dołączone magazynu Azure stosu.
* Ubuntu 14.04 instalacji wymaga wersji gcc zaktualizowane lub uaktualnione — kroki niezbędne do uaktualnienia jest poniżej:

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

* Eksplorator usługi Storage 0.8.9 automatycznie pobierze najnowszą wersję aktualizacji.
* Poprawka: przy użyciu portalu wygenerowanego identyfikatora URI połączenia SAS do podłączenia konta magazynu spowoduje błąd.
* Teraz można tworzyć, zarządzanie i wspierania migawki obiektu blob.
* Można teraz logowania się do konta chińskiej wersji platformy Azure, platformy Azure w Niemczech i Azure instytucji rządowych Stanów Zjednoczonych.
* Można teraz zmienić poziom powiększenia. Opcje w menu Widok powiększanie, zmniejszanie i zresetować powiększenie.
* Znaki Unicode są teraz obsługiwane w metadanych użytkownika do obiektów blob i plików.
* Zwiększona dostępność.
* Z powiadomienie o aktualizacji można wyświetlić informacje o wersji w następnej wersji. Można również wyświetlić bieżące informacje o wersji w menu Pomoc.

#### <a name="fixes"></a>Poprawki

* Stały: numer wersji jest teraz prawidłowo wyświetlany w Panelu sterowania w systemie Windows
* Stały: wyszukiwanie nie jest już ograniczona do 50 000 węzłów
* Stały: Przekaż do udziału plików zawsze wykonana, jeśli katalog docelowy już nie istnieje
* Stały: lepszą stabilność dla długich przekazywania i pobierania

#### <a name="known-issues"></a>Znane problemy

* Gdy jest powiększony przychodzący lub wychodzący, poziom powiększenia na chwilę może zresetować domyślnego poziomu.
* Szybki dostęp działa tylko z elementami subskrypcji. Zasobów lokalnych lub zasobów dołączonych za pomocą klucza lub tokenu sygnatury dostępu Współdzielonego nie są obsługiwane w tej wersji.
* Może upłynąć w kilka sekund, aby przejść do zasobu docelowego, w zależności od liczby zasobów, należy mieć szybki dostęp.
* Mając więcej niż 3 grup obiektów blob lub przekazywania w tym samym czasie plików może powodować błędy.

12/16/2016
### <a name="version-087"></a>Wersja 0.8.7

>[!VIDEO https://www.youtube.com/embed/Me4Y4jxoer8?ecver=1]

#### <a name="new"></a>Nowa

* Można wybrać sposób rozwiązywania konfliktów na początku sesji aktualizacji, pobieranie lub kopiowanie w oknie działań
* Umieść kursor nad kartę, aby wyświetlić pełną ścieżkę zasobów pamięci masowej
* Po kliknięciu na karcie synchronizacji z lokalizacji, w okienku nawigacji po lewej stronie

#### <a name="fixes"></a>Poprawki

* Stały: Eksploratora usługi Storage jest teraz zaufanych aplikacji dla komputerów Mac
* Stały: Ubuntu 14.04 jest ponownie obsługiwane
* Stały: Czasami Dodaj konto interfejsu użytkownika miga podczas ładowania subskrypcji
* Stały: Czasami nie wszystkie zasoby magazynu zostały wymienione w okienku nawigacji po lewej stronie
* Stałe: Okienka akcji czasami wyświetlane akcje pusty
* Stały: Rozmiar okna z ostatniej sesji zamkniętego są teraz przechowywane
* Stały: Możesz otworzyć wiele kart dla tego samego zasobu za pomocą menu kontekstowe

#### <a name="known-issues"></a>Znane problemy

* Szybki dostęp działa tylko z elementami subskrypcji. Zasobów lokalnych lub dołączonych za pomocą klucza lub tokenu sygnatury dostępu Współdzielonego zasobów nie są obsługiwane w tej wersji
* Może zająć w kilka sekund, aby przejść do zasobu docelowego, w zależności od liczby zasobów, musisz mieć szybki dostęp
* Mając więcej niż 3 grup obiektów blob lub przekazywania w tym samym czasie plików może powodować błędy
* Dojść wyszukiwania wyszukiwanie w węzłach około 50 000 — po to, może to mieć wpływ na wydajność lub może spowodować nieobsługiwany wyjątek
* Po raz pierwszy na macOS przy użyciu Eksploratora magazynu może pojawić się wiele monitów pytania o uprawnienia użytkownika do uzyskania dostępu łańcucha kluczy. Zalecamy użycie wybierz zawsze Zezwalaj, monit nie będą widoczne ponownie

11/18/2016
### <a name="version-086"></a>Wersja 0.8.6

#### <a name="new"></a>Nowa

* Można teraz numer pin najczęściej używane usług szybki dostęp do ułatwienia nawigacji
* Teraz możesz otworzyć wiele edytorów na różnych kartach. Jednego kliknięcia, aby otworzyć kartę tymczasowego; Kliknij dwukrotnie, aby otworzyć kartę trwałych. Możesz również kliknąć na karcie tymczasowy, aby stał się na karcie stałe
* Wprowadziliśmy zauważalna zmiana wydajności i stabilności ulepszenia przekazywania i pobierania, zwłaszcza w przypadku dużych plików na maszynach fast
* Teraz można tworzyć foldery "wirtualne" pusta w kontenerów obiektów blob
* Ma ponownie wprowadzono ukierunkowane wyszukiwanie z naszego nowego wyszukiwania podciągu rozszerzonego, teraz są dwie opcje wyszukiwania:
    * Globalne wyszukiwanie — w polu tekstowym wyszukiwania wpisz wyszukiwany termin
    * Wyszukiwania zakresowego — kliknij ikonę lupy obok węzła, a następnie dodaj wyszukiwany termin do końca ścieżki, lub kliknij prawym przyciskiem myszy i wybierz polecenie "Wyszukiwania z tutaj"
* Dodano różne kompozycje: jasny (ustawienie domyślne), ciemny, wysoki kontrast czarne i wysoki kontrast biały. Przejdź do edycji -&gt; kompozycje, aby zmienić preferencje motywów
* Można zmodyfikować właściwości obiektów Blob i plików
* Firma Microsoft obsługuje teraz zakodowanego (base64) i niekodowany kolejki komunikatów
* W systemie Linux, 64-bitowego systemu operacyjnego jest teraz wymagane. W tej wersji obsługiwany jest tylko 64-bitowych Ubuntu 16.04.1 LTS
* Zaktualizowaliśmy naszych logo!

#### <a name="fixes"></a>Poprawki

* Stała: Ekranu zamrożenia problemów
* Stały: Zwiększonych zabezpieczeń
* Stałe: Może być czasami zduplikowane dołączonego konta
* Stały: Obiektów blob z niezdefiniowanego typu zawartości można wygenerować wyjątek
* Stała: Otwieranie panelu zapytania na pusta tabela nie było możliwe
* Stały: Zmienia się usterki podczas wyszukiwania.
* Zwiększona stałym: Liczba zasobów załadowane z 50 do 100 po kliknięciu przycisku "Obciążenia więcej"
* Stałe: Przy pierwszym uruchomieniu, jeśli konto jest podpisana, możemy teraz wybrać wszystkie subskrypcje dla tego konta domyślnie

#### <a name="known-issues"></a>Znane problemy

* Ta wersja Eksploratora magazynu nie działa w Ubuntu 14.04
* Aby otworzyć wiele kart dla tego samego zasobu, nie stale klikaj tego samego zasobu. Kliknij na inny zasób, a następnie wrócić do poprzedniej strony, a następnie kliknij polecenie pierwotnego zasobu, aby otworzyć go ponownie w innej karty
* Szybki dostęp działa tylko z elementami subskrypcji. Zasobów lokalnych lub dołączonych za pomocą klucza lub tokenu sygnatury dostępu Współdzielonego zasobów nie są obsługiwane w tej wersji
* Może zająć w kilka sekund, aby przejść do zasobu docelowego, w zależności od liczby zasobów, musisz mieć szybki dostęp
* Mając więcej niż 3 grup obiektów blob lub przekazywania w tym samym czasie plików może powodować błędy
* Dojść wyszukiwania wyszukiwanie w węzłach około 50 000 — po to, może to mieć wpływ na wydajność lub może spowodować nieobsługiwany wyjątek

10/03/2016
### <a name="version-085"></a>Wersja 0.8.5

#### <a name="new"></a>Nowa

* Klucze generowane przez Portal SAS mogą teraz używać do dołączenia do przechowywania kont i zasobów

#### <a name="fixes"></a>Poprawki

* Stała: wyścigu podczas wyszukiwania czasami spowodował węzłów stanie nie można rozwijać się
* Stałych: "Użyj protokołu HTTP" nie działa, podczas nawiązywania połączenia z nazwą konta i klucza konta magazynu
* Stały: Klucze SAS (wygenerowany specjalnie portalu te) zwracają błąd "ukośnikiem na końcu"
* Stały: Importowanie tabeli problemów
    * Czasami klucz partycji i klucz wiersza zostały cofnięte
    * Nie można odczytać "wartości null" kluczy partycji

#### <a name="known-issues"></a>Znane problemy

* Dojść wyszukiwania wyszukiwanie w węzłach około 50 000 -, wydajność może mieć wpływ na
* Stos Azure aktualnie nie obsługuje plików, rozwiń pliki w trakcie wyświetli błąd

09/12/2016
### <a name="version-084"></a>Wersja 0.8.4

>[!VIDEO https://www.youtube.com/embed/cr5tOGyGrIQ?ecver=1]

#### <a name="new"></a>Nowa

* Generowanie linki bezpośrednie do kont magazynu, kontenerów, kolejek, tabel lub udziały plików do udostępniania i obsługuje łatwy dostęp do zasobów — Windows i Mac OS
* Wyszukaj kontenerów obiektów blob, tabel, kolejek, udziałów plików lub kont magazynu w polu wyszukiwania
* Teraz można pogrupować klauzul w tabeli konstruktora zapytań
* Zmień nazwę i kopiowania/wklejania kontenerów obiektów blob, udziałów plików, tabel, obiektów blob, obiektów blob folderów, plików i katalogów z wewnątrz dołączone sygnatury dostępu Współdzielonego konta i kontenerów
* Zmiana nazwy i kopiowania obiektu blob kontenery i udziały plików teraz zachować właściwości i metadane

#### <a name="fixes"></a>Poprawki

* Stały: nie można edytować jednostek tabeli, gdy właściwości boolean lub binarne

#### <a name="known-issues"></a>Znane problemy

* Dojść wyszukiwania wyszukiwanie w węzłach około 50 000 -, wydajność może mieć wpływ na

08/03/2016
### <a name="version-083"></a>Wersja 0.8.3

>[!VIDEO https://www.youtube.com/embed/HeGW-jkSd9Y?ecver=1]

#### <a name="new"></a>Nowa

* Zmień nazwę kontenerów, tabel, udziałów plików
* Udoskonalone środowisko konstruktora zapytań
* Umożliwia zapisywanie i ładowanie zapytań
* Bezpośrednie łącza do przechowywania kont lub kontenery, kolejek, tabel lub udziały do udostępniania i łatwe uzyskiwanie dostępu do zasobów plików (tylko Windows - macOS obsługuje wkrótce!)
* Możliwość zarządzania i skonfigurować zasady CORS

#### <a name="fixes"></a>Poprawki

* Stały: Accounts Microsoft wymagają ponowne uwierzytelnianie co 8 – 12 godzin

#### <a name="known-issues"></a>Znane problemy

* Czasami interfejsu użytkownika mogą sprawiać wrażenie — maksymalizacja okna pomaga rozwiązać ten problem
* System macOS instalacji może wymagać podniesionego poziomu uprawnień
* Panel ustawień konta mogą być wyświetlane konieczne ponowne wprowadzenie poświadczeń, aby filtrować subskrypcji
* Zmiana nazwy udziałów plików, kontenerów obiektów blob i tabele nie zachowuje metadane lub innych właściwości w kontenerze, takie jak przydziału udziału plików, poziom dostępu publicznego lub zasad dostępu
* Zmiana nazwy obiektów blob (indywidualnie lub wewnątrz kontenera obiektów blob zmienionej nazwie) nie zostaną zachowane migawki. Wszystkie inne właściwości i metadanych dla obiektów blob, plików i jednostek są zachowywane podczas zmiany nazwy
* Kopiowanie lub zmiana nazwy zasobów nie działa w ramach konta dołączone sygnatury dostępu Współdzielonego

07/07/2016
### <a name="version-082"></a>Wersja 0.8.2

>[!VIDEO https://www.youtube.com/embed/nYgKbRUNYZA?ecver=1]

#### <a name="new"></a>Nowa

* Konta magazynu są pogrupowane według subskrypcji; Programowanie magazynu i zasobów dołączonych za pomocą klucza lub SAS są wyświetlane w węźle (lokalny i załączone)
* Wyloguj z konta w panelu "Ustawienia konta Azure"
* Skonfiguruj ustawienia serwera proxy, aby włączyć i zarządzać nimi logowania
* Tworzenie i przerwać dzierżawy obiektów blob
* Kontenery Otwórz obiektów blob, kolejek, tabel i pliki z jednym kliknięciem

#### <a name="fixes"></a>Poprawki

* Stała: dodaje z bibliotekami .NET lub Java wiadomości w kolejce są nie prawidłowo zdekodować z formatu base64
* Stały: $metrics tabele nie są wyświetlane w przypadku kont magazynu obiektów Blob
* Stały: węzeł tabele nie działa dla lokalnej pamięci masowej (Programowanie)

#### <a name="known-issues"></a>Znane problemy

* System macOS instalacji może wymagać podniesionego poziomu uprawnień

06/15/2016
### <a name="version-080"></a>Wersja 0.8.0

>[!VIDEO https://www.youtube.com/embed/ycfQhKztSIY?ecver=1]

>[!VIDEO https://www.youtube.com/embed/k4_kOUCZ0WA?ecver=1]

>[!VIDEO https://www.youtube.com/embed/3zEXJcGdl_k?ecver=1]

#### <a name="new"></a>Nowa

* Obsługa udziału plików: wyświetlanie, przekazywanie, pobieranie, kopiowania plików i katalogów, identyfikatory URI SAS (Tworzenie i łączenie)
* Udoskonalona obsługa połączenie z magazynem z kluczami identyfikatory URI SAS lub konta
* Eksportowanie tabeli wyników zapytania
* Zmienianie kolejności kolumn w tabeli i dostosowywania
* Wyświetlanie $logs kontenerów obiektów blob i tabelach $metrics dla konta magazynu o metryki włączone
* Ulepszone eksportowania i importowania zachowanie, zawiera teraz typ wartości właściwości

#### <a name="fixes"></a>Poprawki

* Stały: przekazanie lub pobranie dużych obiektów blob może spowodować niekompletne przekazywania/pobieranie
* Stały: Edycja, dodanie lub zaimportowanie jednostki z wartością liczbową ciągu ("1") przekonwertuje go podwójne
* Stała: Nie można rozwinąć węzła tabeli w środowisku programistycznym lokalnego

#### <a name="known-issues"></a>Znane problemy

* $metrics tabele nie są widoczne dla kont magazynu obiektów Blob
* Wiadomości w kolejce dodać programistycznie mogą nie być wyświetlane prawidłowo, jeśli komunikaty są zakodowane przy użyciu kodowania Base64

05/17/2016
### <a name="version-07201605090"></a>Wersja 0.7.20160509.0

#### <a name="new"></a>Nowa

* Awarie lepszą obsługę błędów dla aplikacji

#### <a name="fixes"></a>Poprawki

* Usunięte usterki gdzie komunikaty paska informacyjnego czasami nie są wyświetlane podczas logowania były wymagane

#### <a name="known-issues"></a>Znane problemy

* Tabele: Dodawanie, edytowanie lub importowanie jednostki, która ma właściwość z wartość ambiguously liczbowego, na przykład "1" lub "1.0", a użytkownik próbuje wysłać go jako `Edm.String`, wartość będzie Wróć za pomocą klienta interfejsu API jako Edm.Double

03/31/2016

### <a name="version-07201603250"></a>Wersja 0.7.20160325.0

>[!VIDEO https://www.youtube.com/embed/imbgBRHX65A?ecver=1]

>[!VIDEO https://www.youtube.com/embed/ceX-P8XZ-s8?ecver=1]

#### <a name="new"></a>Nowa

* Tabela wsparcia: przeglądanie, wyszukiwanie, eksportu, importowania i operacji CRUD dla jednostek
* Kolejka pomocy technicznej: przeglądanie, dodawanie dequeueing wiadomości
* Generowanie identyfikatorów URI sygnatury dostępu Współdzielonego dla konta magazynu
* Łączenie z kont magazynu identyfikatory URI sygnatury dostępu Współdzielonego
* Powiadomienia o aktualizacji dla przyszłych aktualizacji do Eksploratora usługi Storage
* Zaktualizowano wygląd i działanie

#### <a name="fixes"></a>Poprawki

* Ulepszenia wydajności i niezawodności

### <a name="known-issues-amp-mitigations"></a>Znane problemy &amp; środki zaradcze

* Pobieranie plików dużych obiektów blob nie działa prawidłowo — zaleca się używanie AzCopy, podczas gdy ten problem można rozwiązać
* Poświadczenia konta nie zostaną pobrane ani Jeśli folder macierzysty nie można odnaleźć lub nie można zapisać w pamięci podręcznej
* Jeśli firma Microsoft Dodawanie, edytowanie lub importowanie jednostki, która ma właściwość z wartością ambiguously liczbowego, takie jak "1" lub "1.0", a użytkownik próbuje wysłać go jako `Edm.String`, wartość będzie Wróć za pomocą klienta interfejsu API jako Edm.Double
* Podczas importowania plików CSV z rekordami wielowierszowe, dane mogą uzyskać kostki lub zaszyfrowane

02/03/2016

### <a name="version-07201601291"></a>Wersja 0.7.20160129.1

#### <a name="fixes"></a>Poprawki

* Poprawia ogólną wydajność podczas przekazywanie, pobieranie i kopiowania obiektów blob

01/14/2016

### <a name="version-07201601050"></a>Wersja 0.7.20160105.0

#### <a name="new"></a>Nowa

* Obsługa systemu Linux (parzystość funkcji do systemu OS x)
* Dodaj kontenerów obiektów blob z kluczem dostępu sygnatur dostępu Współdzielonego
* Dodawanie konta magazynu dla Chin Azure
* Dodawanie konta magazynu z niestandardowe punkty końcowe
* Otwieranie i wyświetlanie obiektów blob tekst i obraz zawartości
* Wyświetlanie i edytowanie właściwości obiektów blob i metadanych

#### <a name="fixes"></a>Poprawki

* Stała: przekazywanie i pobieranie dużą liczbę obiektów blob (500 +) może wywoływać aplikacji na ekranie białe
* Stały: podczas ustawiania poziomu dostępu publicznego kontenera obiektów blob, nowa wartość nie jest aktualizowane do momentu ponownego Ustaw fokus w kontenerze. Ponadto okno dialogowe zawsze domyślnie "Brak dostępu publicznego", a nie rzeczywiste bieżącą wartość.
* Obsługa lepszej ogólnej klawiatury/ułatwień dostępu i interfejsu użytkownika
* Obszar nawigacji historii zawijany, gdy jest długa z białą przestrzenią
* Okno dialogowe SAS obsługuje sprawdzania poprawności danych wejściowych
* Lokalny magazyn nadal będzie dostępna, nawet jeśli wygasły poświadczenia użytkownika
* Usunięcie kontenera obiektów blob otwarty Eksplorator obiektów blob po prawej stronie jest zamknięty.

#### <a name="known-issues"></a>Znane problemy

* Instalacja systemu Linux wymaga wersji gcc zaktualizowane lub uaktualnione — kroki niezbędne do uaktualnienia jest poniżej:
    * `sudo add-apt-repository ppa:ubuntu-toolchain-r/test`
    * `sudo apt-get update`
    * `sudo apt-get upgrade`
    * `sudo apt-get dist-upgrade`

11/18/2015
### <a name="version-07201511160"></a>Wersja 0.7.20151116.0

#### <a name="new"></a>Nowa

* System macOS i wersji systemu Windows
* Zaloguj się do wyświetlania kont magazynu — użyć konta organizacji, Account firmy Microsoft, 2FA itp.
* Lokalne działania projektowe magazynu (użyć emulatora magazynu systemu Windows)
* Usługa Azure Resource Manager i Model Klasyczny zasobów pomocy technicznej
* Tworzenie i usuwanie obiektów blob, kolejek i tabel
* Wyszukiwanie określonych obiektów blob, kolejek i tabel
* Przejrzyj zawartość kontenerów obiektów blob
* Wyświetlanie i przeglądanie katalogów
* Przekazywanie, pobieranie i usuwanie obiektów blob i folderów
* Wyświetlanie i edytowanie właściwości obiektów blob i metadanych
* Generuj klucze SAS
* Zarządzanie i utworzyć przechowywane zasad dostępu (SAP)
* Wyszukaj obiekty BLOB według prefiksu
* Przeciągnij n porzucić przekazywanie lub pobieranie plików

#### <a name="known-issues"></a>Znane problemy

* Po ustawienie poziomu dostępu publicznego kontenera obiektów blob, nowa wartość nie jest aktualizowany, dopóki nie zostanie ponownie ustawiony fokus w kontenerze
* Po otwarciu okna dialogowego, aby ustawić poziom dostępu publicznego będzie zawsze wyświetlana "Brak dostępu publicznego" jako domyślne, a nie rzeczywiste bieżącą wartość
* Nie można zmienić nazwy pobrany obiektów blob
* Wpisy dziennika aktywności będą czasami "zatrzymywane" w toku stanu, gdy wystąpi błąd i nie jest wyświetlany błąd
* Czasami awarii lub włącza całkowicie biały podczas próby przekazywanie lub pobieranie dużą liczbę obiektów blob
* Czasami anulowanie operacji kopiowania nie działa
* Podczas tworzenia kontenera (tabeli-obiekt blob/kolejki), jeśli dane wejściowe nieprawidłową nazwę i przejść do tworzenia drugiego, w ramach typu innego kontenera nie można ustawić fokusu na nowy typ
* Nie można utworzyć nowego folderu, lub zmień nazwę folderu




[2]: https://support.microsoft.com/en-us/help/4021389/storage-explorer-troubleshooting-guide
[3]: vs-azure-tools-storage-manage-with-storage-explorer.md
