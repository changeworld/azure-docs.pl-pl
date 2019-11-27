---
title: Informacje o wersji programu Microsoft Azure Storage Explorer
description: Informacje o wersji programu Microsoft Azure Storage Explorer
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
ms.openlocfilehash: 0b2ffc00b6c96f2c31a4b711f618e7b87b6f69e0
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2019
ms.locfileid: "74482122"
---
# <a name="microsoft-azure-storage-explorer-release-notes"></a>Informacje o wersji programu Microsoft Azure Storage Explorer

Ten artykuł zawiera najnowsze informacje o wersji dla programu Eksplorator usługi Azure Storage, a także informacje o wersji dla poprzednich wersji. 

[Eksplorator usługi Microsoft Azure Storage](./vs-azure-tools-storage-manage-with-storage-explorer.md) jest aplikacją autonomiczną, która umożliwia łatwe współdziałanie z danymi usługi Azure Storage w systemach Windows, MacOS i Linux.

Aby pobrać wcześniejsze wersje Eksplorator usługi Storage, odwiedź [stronę wydań](https://github.com/microsoft/AzureStorageExplorer/releases) w naszym repozytorium GitHub.

## <a name="version-1110"></a>1\.11.0 wersja
11/4/2019

### <a name="new"></a>Nowa
* Operacje dla obiektów blob, ADLS Gen2 i Managed Disks używają zintegrowanych AzCopy. Dokładniej mówiąc, następujące operacje są wykonywane przy użyciu AzCopy:
   * Obiekty blob
      * Otwórz do edycji i przekazywania
      * Przekaż, w tym przeciągnij & upuść
      * Do pobrania
      * Kopiuj & Wklej #1249
      * Usuwanie
   * ADLS Gen2 obiektów BLOB
      * Przekaż, w tym przeciągnij & upuść
      * Do pobrania
      * Kopiuj & Wklej
      * Usuń, łącznie z usunięciem folderu
   * Dyski zarządzane
      * Upload
      * Do pobrania
      * Kopiuj & Wklej

   Ponadto do zintegrowanego środowiska AzCopy dodano kilka często pożądanych funkcji:
   * Rozwiązywanie konfliktów — podczas transferów zostanie wyświetlony monit o rozwiązanie konfliktów. #1455
   * Przekaż jako stronicowe obiekty blob — możesz wybrać, czy AzCopy przekazuje pliki VHD i VHDX jako stronicowe obiekty blob. #1164 i #1601
   * Konfigurowalne parametry AzCopy — dodano kilka ustawień w celu dostrajania wydajności i użycia zasobów AzCopy. Zobacz więcej szczegółów poniżej.

* Aby włączyć dostęp do ADLS Gen2 i obiektów BLOB dla wielu protokołów i jeszcze bardziej ulepszyć ADLS Gen2 środowiska, dodaliśmy następujące funkcje dla kont ADLS Gen2:
   * Wyszukaj przy użyciu przyjaznych nazw, aby ustawić uprawnienia listy ACL
   * Wyświetlanie ukrytych kontenerów, takich jak $logs i $web
   * Pozyskiwanie i przerywanie dzierżawy kontenera
   * Pobieranie i przerywanie dzierżawy obiektów BLOB #848
   * Zarządzanie zasadami dostępu do kontenera
   * Konfigurowanie warstw dostępu do obiektów BLOB
   * Kopiuj & Wklej obiekty blob

* W tej wersji zapoznajemy 17 dodatkowych języków. Możesz przełączyć się do wybranego języka na stronie Ustawienia w obszarze "aplikacja" → "ustawienia regionalne" → "(wersja zapoznawcza)". Nadal pracujemy nad tłumaczeniem dodatkowych ciągów i ulepszaniem jakości tłumaczenia. Jeśli masz jakiekolwiek opinie dotyczące tłumaczenia lub jeśli zauważysz ciąg, który nie został jeszcze przetłumaczony, [Otwórz problem w usłudze GitHub](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=%F0%9F%8C%90%20localization&template=bug-report.md&title=).
* W każdej wersji próbujemy dołączyć kilka ustawień, aby umożliwić precyzyjne Eksplorator usługi Storage. W tej wersji dodaliśmy ustawienia w celu dodatkowego skonfigurowania AzCopy oraz ukrycia węzłów usługi:
   * Limit przepustowości AzCopy — pomaga kontrolować, ile sieci AzCopy używa. To ustawienie można znaleźć w "transfery" → "AzCopy" → "Maksymalna szybkość transferu". #1099
   * Sprawdzanie AzCopy MD5 — umożliwia skonfigurowanie, czy i jak ścisłe AzCopy sprawdzają skróty MD5 podczas pobierania. To ustawienie można znaleźć w "transfers" → "AzCopy" → "Check MD5".
   * AzCopy rozmiar buforu współbieżności i pamięci — domyślnie AzCopy przeanalizuje maszynę, aby określić rozsądne wartości domyślne dla tych ustawień. Jednak jeśli wystąpią problemy z wydajnością, te zaawansowane ustawienia mogą służyć do dokładniejszego dostosowywania sposobu działania AzCopy na komputerze. Te ustawienia można znaleźć w obszarze "transfery" → "AzCopy". #994
   * Wyświetlanie i ukrywanie węzłów usługi — te ustawienia umożliwiają wyświetlanie lub ukrywanie dowolnych usług platformy Azure obsługiwanych przez Eksplorator usługi Storage. Te ustawienia można znaleźć w sekcji "usługi". #1877

* Podczas tworzenia migawki dysku zarządzanego jest teraz dostępna nazwa domyślna. #1847
* W przypadku dołączania do usługi Azure AD, jeśli dołączysz kontener ADLS Gen2 obiektów blob, obok węzła zostanie wyświetlony komunikat "(ADLS Gen2)". #1861

### <a name="fixes"></a>Poprawki
* Podczas kopiowania, przekazywania lub pobierania dużych dysków Eksplorator usługi Storage czasami niepowodzeniem odwołać dostęp do dysków należących do tej operacji. Ten problem został rozwiązany. #2048
* Statystyka tabeli nie powiodła się podczas wyświetlania zapytania klucza partycji. Ten problem został rozwiązany. #1886

### <a name="known-issues"></a>Znane problemy
* Eksplorator usługi Storage 1.11.0 teraz wymaga punktu końcowego systemu plików DFS (takiego jak "myaccount.dfs.core.windows.net") w celu dołączenia do kontenerów ADLS Gen2. Poprzednie wersje Eksplorator usługi Storage mogą korzystać z punktu końcowego obiektu BLOB. Te załączniki mogą przestać działać po uaktualnieniu do 1.11.0. Jeśli wystąpi ten problem, dołącz go ponownie przy użyciu punktu końcowego systemu plików DFS.
* Ustawienia liczbowe nie są sprawdzane, czy znajdują się w prawidłowym zakresie. #2140
* Kopiowanie kontenerów obiektów blob z jednego konta magazynu do innego w widoku drzewa może zakończyć się niepowodzeniem. Badamy problem. #2124
* Ustawienie AutoRefresh nie ma jeszcze wpływu na wszystkie operacje w Eksploratorze obiektów BLOB.
* Funkcje dysków zarządzanych nie są obsługiwane w Azure Stack.
* Jeśli przekazanie lub wklejenie dysku zakończy się niepowodzeniem i przed awarią zostanie utworzony nowy dysk, Eksplorator usługi Storage nie spowoduje usunięcia dysku.
* W zależności od tego, kiedy anulujesz przekazywanie lub wklejanie dysku, można pozostawić nowy dysk w uszkodzonym stanie. W takim przypadku należy usunąć nowy dysk lub ręcznie wywołać interfejsy API dysku, aby zastąpić zawartość dysku, tak że nie jest już uszkodzona.
* W przypadku korzystania z funkcji RBAC Eksplorator usługi Storage wymaga pewnych uprawnień do warstwy zarządzania w celu uzyskania dostępu do zasobów magazynu. Więcej informacji można znaleźć w [przewodniku rozwiązywania problemów](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) .
* Odłączanie od zasobu dołączonego za pomocą identyfikatora URI SAS, takiego jak kontener obiektów blob, może spowodować błąd, który uniemożliwia poprawne wyświetlanie innych załączników. Aby obejść ten problem, wystarczy odświeżyć węzeł grupy. Zobacz 537 # Aby uzyskać więcej informacji.
* Jeśli używasz programu VS dla komputerów Mac i nigdy nie zostały utworzone niestandardowej konfiguracji usługi AAD, możesz nie mieć możliwości logowania. Aby obejść ten problem, usuń zawartość ~ /. IdentityService/AadConfigurations. Jeśli tak się nie odblokowuje, komentarz dotyczący tego problemu.
* Azurite nie została jeszcze w pełni zaimplementowana wszystkie interfejsy API usługi Storage. W związku z tym może występować nieoczekiwanych błędów lub zachowanie w przypadku używania Azurite dla magazynem projektowym.
* W rzadkich przypadkach fokus drzewa może zakończyć się zatrzymaniem na szybki dostęp. Aby odklej fokus, można na nim Odśwież wszystko.
* Przekazywanie z folderu usługi OneDrive nie działa z powodu błędów w środowisku NodeJS. Błąd został rozwiązany, ale nie są jeszcze zintegrowane z elektronów. Aby obejść ten problem podczas przekazywania lub pobierania z kontenera obiektów blob, można użyć funkcji eksperymentalnej AzCopy.
* Przeznaczone dla usługi Azure Stack, przekazywanie pewne pliki jako uzupełnialnych obiektów blob może zakończyć się niepowodzeniem.
* Po kliknięciu przycisku "Anuluj" do zadania, może upłynąć trochę czasu tego zadania anulować. Dzieje się tak dlatego, że używamy filtru anulowania obejścia tego problemu.
* Jeśli wybrano nieprawidłowy numer PIN/certyfikatu karty inteligentnej, należy uruchomić ponownie, aby mogła mieć Eksploratora usługi Storage zapomnij tej decyzji.
* Zmiana nazwy obiektów blob (pojedynczo lub w kontenerze obiektów blob zmieniono nazwę) nie zostaną zachowane migawki. Wszystkie właściwości i metadanych obiektów blob, plików oraz jednostki są zachowywane podczas zmiany nazwy.
* Usługa Azure Stack nie obsługuje następujących funkcji. Podjęto próbę użycia tych funkcji podczas pracy z usługą Azure Stack zasobów może spowodować nieoczekiwane błędy.
   * Udziały plików
   * Poziomy dostępu
   * Usuwanie nietrwałe
   * ADLS Gen2
   * Dyski zarządzane
* Powłoka elektronów wykorzystywane przez Eksploratora magazynu ma problemy z niektórych przyspieszania sprzętowego procesora GPU (jednostka przetwarzania grafiki). Jeśli Eksplorator usługi Storage wyświetla puste okno główne (puste), możesz spróbować uruchomić Eksplorator usługi Storage z wiersza polecenia i wyłączyć Przyspieszenie GPU, dodając przełącznik `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Uruchomienie Eksplorator usługi Storage w systemie Linux wymaga wcześniejszego zainstalowania niektórych zależności. Aby uzyskać więcej informacji, zapoznaj się z [przewodnikiem rozwiązywania problemów](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) Eksplorator usługi Storage.

## <a name="previous-releases"></a>Poprzednie wersje

* [1.10.1 wersja](#version-1101)
* [1.10.0 wersja](#version-1100)
* [1.9.0 wersja](#version-190)
* [1.8.1 wersja](#version-181)
* [1.8.0 wersja](#version-180)
* [1.7.0 wersja](#version-170)
* [Wersja 1.6.2](#version-162)
* [1.6.1 wersja](#version-161)
* [1.6.0 wersja](#version-160)
* [1.5.0 wersja](#version-150)
* [1.4.4 wersja](#version-144)
* [Wersja 1.4.3](#version-143)
* [Wersja 1.4.2](#version-142)
* [Wersja 1.4.1](#version-141)
* [1.3.0 wersja](#version-130)
* [1.2.0 wersja](#version-120)
* [1.1.0 wersja](#version-110)
* [1.0.0 wersja](#version-100)
* [0.9.6 wersja](#version-096)
* [0.9.5 wersja](#version-095)
* [Wersja 0.9.4 i 0.9.3](#version-094-and-093)
* [0.9.2 wersja](#version-092)
* [Wersja od 0.9.1 i 0.9.0](#version-091-and-090)
* [0.8.16 wersja](#version-0816)
* [0.8.14 wersja](#version-0814)
* [0.8.13 wersja](#version-0813)
* [Wersje 0.8.12 i 0.8.11 i 0.8.10](#version-0812-and-0811-and-0810)
* [Wersja 0.8.9 i 0.8.8](#version-089-and-088)
* [0.8.7 wersja](#version-087)
* [0.8.6 wersja](#version-086)
* [0.8.5 wersja](#version-085)
* [0.8.4 wersja](#version-084)
* [0.8.3 wersja](#version-083)
* [0.8.2 wersja](#version-082)
* [0.8.0 wersja](#version-080)
* [0.7.20160509.0 wersja](#version-07201605090)
* [0.7.20160325.0 wersja](#version-07201603250)
* [0.7.20160129.1 wersja](#version-07201601291)
* [0.7.20160105.0 wersja](#version-07201601050)
* [0.7.20151116.0 wersja](#version-07201511160)

## <a name="version-1101"></a>1\.10.1 wersja
9/19/2019

### <a name="hotfix"></a>Rozwiązującą
* Niektórzy użytkownicy napotkały błąd w 1.10.0 podczas próby wyświetlenia ich danych na kontach ADLS generacji 1. Ten błąd uniemożliwił prawidłowe renderowanie panelu Eksploratora. Ten problem został rozwiązany. #1853 #1865

### <a name="new"></a>Nowa
* Eksplorator usługi Storage ma teraz interfejs użytkownika dedykowanych ustawień. Możesz uzyskać do niego dostęp przy użyciu opcji Edytuj ustawienia → lub klikając ikonę Ustawienia (koła zębatego) na pasku narzędzi po lewej stronie. Ta funkcja jest pierwszą czynnością, którą należy wykonać, aby zapewnić różne [Ustawienia użytkownika](https://github.com/microsoft/AzureStorageExplorer/labels/%3Abulb%3A%20setting%20candidate). Począwszy od tej wersji, obsługiwane są następujące ustawienia:
  * Motyw
  * Serwer proxy
  * Wyloguj się #6 zakończenia
  * Włącz logowanie przy użyciu przepływu kodu urządzenia
  * #1526 AutoRefresh
  * Włącz AzCopy
  * AzCopy czas trwania SAS Jeśli istnieją inne ustawienia profilów, które chcesz zobaczyć, [Otwórz problem w witrynie GitHub](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=%3Abulb%3A%20setting%20candidate&template=feature_request.md&title=) opisujące ustawienia, które chcesz zobaczyć.
* Eksplorator usługi Storage teraz obsługuje Managed Disks. Możesz:
  * Przekazywanie wirtualnego dysku twardego Premium na nowy dysk
  * Pobieranie dysku
  * Kopiowanie i wklejanie dysków między grupami zasobów i regionami
  * Usuwanie dysków
  * Utwórz migawkę dysku, który umożliwia przekazywanie, pobieranie i kopiowanie w różnych regionach dysków jest obsługiwany przez AzCopy v10.
* Eksplorator usługi Storage można teraz zainstalować za pośrednictwem magazynu Snap w systemie Linux. Podczas instalacji za pomocą usługi Snap są instalowane wszystkie zależności, w tym .NET Core. Obecnie sprawdzono, że Eksplorator usługi Storage działa dobrze w Ubuntu i CentOS. Jeśli wystąpią problemy z instalacją z magazynu Snap w innym systemie Linux dystrybucje, należy [otworzyć problem w witrynie GitHub](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=snaps&template=bug-report.md&title=). Aby dowiedzieć się więcej o instalowaniu z magazynu Snap, zobacz nasz [Przewodnik wprowadzający](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=linux). #68
* Wprowadzono dwa istotne zmiany w celu dołączenia do Azure Active Directory (Azure AD), które mają być bardziej przydatne dla ADLS Gen2 użytkowników:
  * Teraz wybierz dzierżawcę, do którego jest dołączany zasób. Oznacza to, że nie trzeba już uzyskiwać dostępu RBAC do subskrypcji zasobu.
  * W przypadku dołączania kontenera ADLS Gen2 obiektów BLOB możesz teraz dołączyć do określonej ścieżki w kontenerze.
* Podczas zarządzania listami ACL dla ADLS Gen2 plików i folderów, Eksplorator usługi Storage będzie teraz pokazywał przyjazne nazwy dla jednostek na liście ACL. #957
* Po dodaniu za pomocą identyfikatora OID do ADLS Gen2 listy kontroli dostępu Eksplorator usługi Storage zostanie teraz zweryfikowana, że identyfikator OID należy do prawidłowej jednostki w dzierżawie. #1603
* Skróty klawiaturowe do nawigowania między kartami teraz używają bardziej standardowych kombinacji klawiszy. #1018
* Drugie kliknięcie karty spowoduje jej zamknięcie. #1348
* Jeśli transfer AzCopy zawiera pomijania i nie ma błędów, Eksplorator usługi Storage będzie teraz wyświetlał ikonę ostrzeżenia, aby wyróżnić pominięte. #1490
* Zintegrowana AzCopy została zaktualizowana do wersji 10.2.1. Ponadto można wyświetlić wersję programu AzCopy zainstalowaną w oknie dialogowym informacje. #1343

### <a name="fixes"></a>Poprawki
* Wielu użytkowników w różnych "nie można odczytać wersji niezdefiniowanej" lub "nie można odczytać połączenia niezdefiniowanych" podczas pracy z dołączonymi kontami magazynu. Mimo że nadal kontynuujemy badanie głównej przyczyny tego problemu, w 1.10.0 Ulepszono obsługę błędów podczas ładowania dołączonych kont magazynu. #1626, #985 i #1532
* Być może drzewo Eksploratora (po lewej stronie), aby przejść w stan, w którym fokus przechodzi wielokrotnie do górnego węzła. Ten problem został rozwiązany. #1596
* Podczas zarządzania migawkami obiektu BLOB screenreaders nie odczytuje sygnatury czasowej skojarzonej z migawką. Ten problem został rozwiązany. #1202
* Ustawienie serwera proxy w usłudze macOS nie zostało ustawione na czas na ich użycie przez proces uwierzytelniania. Ten problem został rozwiązany. #1567
* Jeśli konto magazynu w chmurze suwerennej zostało dołączone przy użyciu nazwy i klucza, AzCopy nie będzie działała. Ten problem został rozwiązany. #1544
* W przypadku dołączania za pomocą parametrów połączenia, Eksplorator usługi Storage usunie teraz spacje końcowe. #1387

### <a name="known-issues"></a>Znane problemy
* Ustawienie AutoRefresh nie ma jeszcze wpływu na wszystkie operacje w Eksploratorze obiektów BLOB.
* Funkcje dysków zarządzanych nie są obsługiwane w Azure Stack.
* Jeśli przekazanie lub wklejenie dysku zakończy się niepowodzeniem i przed awarią zostanie utworzony nowy dysk, Eksplorator usługi Storage nie spowoduje usunięcia dysku.
* W zależności od tego, kiedy anulujesz przekazywanie lub wklejanie dysku, można pozostawić nowy dysk w uszkodzonym stanie. W takim przypadku należy usunąć nowy dysk lub ręcznie wywołać interfejsy API dysku, aby zastąpić zawartość dysku, tak że nie jest już uszkodzona.
* W zależności od tego, kiedy anulujesz przekazywanie lub wklejanie dysku, można pozostawić nowy dysk w uszkodzonym stanie. W takim przypadku należy usunąć nowy dysk lub ręcznie wywołać interfejsy API dysku, aby zastąpić zawartość dysku, tak że nie jest już uszkodzona.
* Podczas pobierania obiektu BLOB, który nie jest AzCopy, MD5 dla dużych plików nie jest weryfikowany. Jest to spowodowane usterką w zestawie SDK magazynu. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* W przypadku korzystania z funkcji RBAC Eksplorator usługi Storage wymaga pewnych uprawnień do warstwy zarządzania w celu uzyskania dostępu do zasobów magazynu. Więcej informacji można znaleźć w [przewodniku rozwiązywania problemów](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) .
* Odłączanie od zasobu dołączonego za pomocą identyfikatora URI SAS, takiego jak kontener obiektów blob, może spowodować błąd, który uniemożliwia poprawne wyświetlanie innych załączników. Aby obejść ten problem, wystarczy odświeżyć węzeł grupy. Zobacz 537 # Aby uzyskać więcej informacji.
* Jeśli używasz programu VS dla komputerów Mac i nigdy nie zostały utworzone niestandardowej konfiguracji usługi AAD, możesz nie mieć możliwości logowania. Aby obejść ten problem, usuń zawartość ~ /. IdentityService/AadConfigurations. Jeśli tak się nie odblokowuje, komentarz dotyczący tego problemu.
* Azurite nie została jeszcze w pełni zaimplementowana wszystkie interfejsy API usługi Storage. W związku z tym może występować nieoczekiwanych błędów lub zachowanie w przypadku używania Azurite dla magazynem projektowym.
* W rzadkich przypadkach fokus drzewa może zakończyć się zatrzymaniem na szybki dostęp. Aby odklej fokus, można na nim Odśwież wszystko.
* Przekazywanie z folderu usługi OneDrive nie działa z powodu błędów w środowisku NodeJS. Błąd został rozwiązany, ale nie są jeszcze zintegrowane z elektronów. Aby obejść ten problem podczas przekazywania lub pobierania z kontenera obiektów blob, można użyć funkcji eksperymentalnej AzCopy.
* Przeznaczone dla usługi Azure Stack, przekazywanie pewne pliki jako uzupełnialnych obiektów blob może zakończyć się niepowodzeniem.
* Po kliknięciu przycisku "Anuluj" do zadania, może upłynąć trochę czasu tego zadania anulować. Dzieje się tak dlatego, że używamy filtru anulowania obejścia tego problemu.
* Jeśli wybrano nieprawidłowy numer PIN/certyfikatu karty inteligentnej, należy uruchomić ponownie, aby mogła mieć Eksploratora usługi Storage zapomnij tej decyzji.
* Zmiana nazwy obiektów blob (pojedynczo lub w kontenerze obiektów blob zmieniono nazwę) nie zostaną zachowane migawki. Wszystkie właściwości i metadanych obiektów blob, plików oraz jednostki są zachowywane podczas zmiany nazwy.
* Usługa Azure Stack nie obsługuje następujących funkcji. Podjęto próbę użycia tych funkcji podczas pracy z usługą Azure Stack zasobów może spowodować nieoczekiwane błędy.
   * Udziały plików
   * Poziomy dostępu
   * Usuwanie nietrwałe
   * ADLS Gen2
   * Dyski zarządzane
* Powłoka elektronów wykorzystywane przez Eksploratora magazynu ma problemy z niektórych przyspieszania sprzętowego procesora GPU (jednostka przetwarzania grafiki). Jeśli Eksplorator usługi Storage wyświetla puste okno główne (puste), możesz spróbować uruchomić Eksplorator usługi Storage z wiersza polecenia i wyłączyć Przyspieszenie GPU, dodając przełącznik `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Uruchomienie Eksplorator usługi Storage w systemie Linux wymaga wcześniejszego zainstalowania niektórych zależności. Aby uzyskać więcej informacji, zapoznaj się z [przewodnikiem rozwiązywania problemów](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) Eksplorator usługi Storage.


## <a name="version-1100"></a>1\.10.0 wersja
9/12/2019

### <a name="new"></a>Nowa

* Eksplorator usługi Storage ma teraz interfejs użytkownika dedykowanych ustawień. Możesz uzyskać do niego dostęp przy użyciu opcji Edytuj ustawienia → lub klikając ikonę Ustawienia (koła zębatego) na pasku narzędzi po lewej stronie. Ta funkcja jest pierwszą czynnością, którą należy wykonać, aby zapewnić różne [Ustawienia użytkownika](https://github.com/microsoft/AzureStorageExplorer/labels/%3Abulb%3A%20setting%20candidate). Począwszy od tej wersji, obsługiwane są następujące ustawienia:
    * Motyw
    * Serwer proxy
    * Wyloguj się [#6](https://www.github.com/Microsoft/AzureStorageExplorer/issues/6) zakończenia
    * Włącz logowanie przy użyciu przepływu kodu urządzenia
    * [#1526](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1526) AutoRefresh
    * Włącz AzCopy
    * Czas trwania AzCopy SAS

    Jeśli istnieją inne ustawienia profilów, które chcesz zobaczyć, [Otwórz problem w usłudze GitHub z opisem ustawienia, które chcesz zobaczyć](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=%3Abulb%3A%20setting%20candidate&template=feature_request.md&title=).
* Eksplorator usługi Storage teraz obsługuje Managed Disks. Możesz:
    * Przekazywanie wirtualnego dysku twardego Premium na nowy dysk
    * Pobieranie dysku
    * Kopiowanie i wklejanie dysków między grupami zasobów i regionami
    * Usuwanie dysków
    * Tworzenie migawki dysku

    Przekazywanie, pobieranie i między regionami kopiowanie dysków jest obsługiwane przez AzCopy v10.
* Eksplorator usługi Storage można teraz zainstalować za pośrednictwem magazynu Snap w systemie Linux. Podczas instalacji za pomocą usługi Snap są instalowane wszystkie zależności, w tym .NET Core. Obecnie sprawdzono, że Eksplorator usługi Storage działa dobrze w Ubuntu i CentOS. Jeśli wystąpią problemy z instalacją z magazynu Snap w innym systemie Linux dystrybucje, należy [otworzyć problem w witrynie GitHub](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=snaps&template=bug-report.md&title=). Aby dowiedzieć się więcej o instalowaniu z magazynu Snap, zobacz nasz [Przewodnik wprowadzający](https://aka.ms/storageexplorer/snapinformation). [#68](https://www.github.com/Microsoft/AzureStorageExplorer/issues/68)
* Wprowadzono dwa istotne zmiany w celu dołączenia do Azure Active Directory (Azure AD), które mają być bardziej przydatne dla ADLS Gen2 użytkowników: * teraz wybierz dzierżawę, do której jest dołączany zasób. Oznacza to, że nie trzeba już uzyskiwać dostępu RBAC do subskrypcji zasobu.
        * Jeśli dołączysz kontener ADLS Gen2 obiektów blob, możesz teraz dołączyć do określonej ścieżki w kontenerze.
* Podczas zarządzania listami ACL dla ADLS Gen2 plików i folderów, Eksplorator usługi Storage będzie teraz pokazywał przyjazne nazwy dla jednostek na liście ACL. [#957](https://www.github.com/Microsoft/AzureStorageExplorer/issues/957)
* Po dodaniu za pomocą identyfikatora OID do ADLS Gen2 listy kontroli dostępu Eksplorator usługi Storage zostanie teraz zweryfikowana, że identyfikator OID należy do prawidłowej jednostki w dzierżawie. [#1603](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1603)
* Skróty klawiaturowe do nawigowania między kartami teraz używają bardziej standardowych kombinacji klawiszy. [#1018](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1018)
* Drugie kliknięcie karty spowoduje jej zamknięcie. [#1348](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1348)
* Jeśli transfer AzCopy zawiera pomijania i nie ma błędów, Eksplorator usługi Storage będzie teraz wyświetlał ikonę ostrzeżenia, aby wyróżnić pominięte. [#1490](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1490)
* Zintegrowana AzCopy została zaktualizowana do wersji 10.2.1. Ponadto można wyświetlić wersję programu AzCopy zainstalowaną w oknie dialogowym informacje. [#1343](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1343)

### <a name="fixes"></a>Poprawki

* Wielu użytkowników w różnych "nie można odczytać wersji niezdefiniowanej" lub "nie można odczytać połączenia niezdefiniowanych" podczas pracy z dołączonymi kontami magazynu. Mimo że nadal kontynuujemy badanie głównej przyczyny tego problemu, w 1.10.0 Ulepszono obsługę błędów podczas ładowania dołączonych kont magazynu. [#1626](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1626), [#985](https://www.github.com/Microsoft/AzureStorageExplorer/issues/985)i [#1532](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1532)
* Być może drzewo Eksploratora (po lewej stronie), aby przejść w stan, w którym fokus przechodzi wielokrotnie do górnego węzła. Ten problem został rozwiązany. [#1596](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1596)
* Podczas zarządzania migawkami obiektu BLOB screenreaders nie odczytuje sygnatury czasowej skojarzonej z migawką. Ten problem został rozwiązany. [#1202](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1202)
* Ustawienie serwera proxy w usłudze macOS nie zostało ustawione na czas na ich użycie przez proces uwierzytelniania. Ten problem został rozwiązany. [#1567](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1567)
* Jeśli konto magazynu w chmurze suwerennej zostało dołączone przy użyciu nazwy i klucza, AzCopy nie będzie działała. Ten problem został rozwiązany. [#1544](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1544)
* W przypadku dołączania za pomocą parametrów połączenia, Eksplorator usługi Storage usunie teraz spacje końcowe. [#1387](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1387)

### <a name="known-issues"></a>Znane problemy

* Ustawienie AutoRefresh nie ma jeszcze wpływu na wszystkie operacje w Eksploratorze obiektów BLOB.
* Funkcje dysków zarządzanych nie są obsługiwane w Azure Stack.
* Jeśli przekazanie lub wklejenie dysku zakończy się niepowodzeniem i przed awarią zostanie utworzony nowy dysk, Eksplorator usługi Storage nie spowoduje usunięcia dysku.
* W zależności od tego, kiedy anulujesz przekazywanie lub wklejanie dysku, można pozostawić nowy dysk w uszkodzonym stanie. W takim przypadku należy usunąć nowy dysk lub ręcznie wywołać interfejsy API dysku, aby zastąpić zawartość dysku, tak że nie jest już uszkodzona.
* Podczas pobierania obiektu BLOB, który nie jest AzCopy, MD5 dla dużych plików nie jest weryfikowany. Jest to spowodowane usterką w zestawie SDK magazynu. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* W przypadku korzystania z funkcji RBAC Eksplorator usługi Storage wymaga pewnych uprawnień do warstwy zarządzania w celu uzyskania dostępu do zasobów magazynu. Więcej informacji można znaleźć w [przewodniku rozwiązywania problemów](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) .
* Odłączanie od zasobu dołączonego za pomocą identyfikatora URI SAS, takiego jak kontener obiektów blob, może spowodować błąd, który uniemożliwia poprawne wyświetlanie innych załączników. Aby obejść ten problem, wystarczy odświeżyć węzeł grupy. Zobacz 537 # Aby uzyskać więcej informacji.
* Jeśli używasz programu VS dla komputerów Mac i nigdy nie zostały utworzone niestandardowej konfiguracji usługi AAD, możesz nie mieć możliwości logowania. Aby obejść ten problem, usuń zawartość ~ /. IdentityService/AadConfigurations. Jeśli tak się nie odblokowuje, komentarz dotyczący tego problemu.
* Azurite nie została jeszcze w pełni zaimplementowana wszystkie interfejsy API usługi Storage. W związku z tym może występować nieoczekiwanych błędów lub zachowanie w przypadku używania Azurite dla magazynem projektowym.
* W rzadkich przypadkach fokus drzewa może zakończyć się zatrzymaniem na szybki dostęp. Aby odklej fokus, można na nim Odśwież wszystko.
* Przekazywanie z folderu usługi OneDrive nie działa z powodu błędów w środowisku NodeJS. Błąd został rozwiązany, ale nie są jeszcze zintegrowane z elektronów. Aby obejść ten problem podczas przekazywania lub pobierania z kontenera obiektów blob, można użyć funkcji eksperymentalnej AzCopy.
* Przeznaczone dla usługi Azure Stack, przekazywanie pewne pliki jako uzupełnialnych obiektów blob może zakończyć się niepowodzeniem.
* Po kliknięciu przycisku "Anuluj" do zadania, może upłynąć trochę czasu tego zadania anulować. Dzieje się tak dlatego, że używamy filtru anulowania obejścia tego problemu.
* Jeśli wybrano nieprawidłowy numer PIN/certyfikatu karty inteligentnej, należy uruchomić ponownie, aby mogła mieć Eksploratora usługi Storage zapomnij tej decyzji.
* Zmiana nazwy obiektów blob (pojedynczo lub w kontenerze obiektów blob zmieniono nazwę) nie zostaną zachowane migawki. Wszystkie właściwości i metadanych obiektów blob, plików oraz jednostki są zachowywane podczas zmiany nazwy.
* Usługa Azure Stack nie obsługuje następujących funkcji. Podjęto próbę użycia tych funkcji podczas pracy z usługą Azure Stack zasobów może spowodować nieoczekiwane błędy.
   * Udziały plików
   * Poziomy dostępu
   * Usuwanie nietrwałe
   * ADLS Gen2
   * Dyski zarządzane
* Powłoka elektronów wykorzystywane przez Eksploratora magazynu ma problemy z niektórych przyspieszania sprzętowego procesora GPU (jednostka przetwarzania grafiki). Jeśli Eksplorator usługi Storage wyświetla puste okno główne (puste), możesz spróbować uruchomić Eksplorator usługi Storage z wiersza polecenia i wyłączyć Przyspieszenie GPU, dodając przełącznik `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Uruchomienie Eksplorator usługi Storage w systemie Linux wymaga wcześniejszego zainstalowania niektórych zależności. Aby uzyskać więcej informacji, zapoznaj się z [przewodnikiem rozwiązywania problemów](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) Eksplorator usługi Storage.

## <a name="version-190"></a>1\.9.0 wersja
7/1/2019

### <a name="download-azure-storage-explorer-190"></a>Pobierz Eksplorator usługi Azure Storage 1.9.0
- [Eksplorator usługi Azure Storage 1.9.0 dla systemu Windows](https://go.microsoft.com/fwlink/?LinkId=708343)
- [Eksplorator usługi Azure Storage 1.9.0 dla komputerów Mac](https://go.microsoft.com/fwlink/?LinkId=708342)
- [Eksplorator usługi Azure Storage 1.9.0 dla systemu Linux](https://go.microsoft.com/fwlink/?LinkId=722418)

### <a name="new"></a>Nowa

* Teraz możesz dołączać kontenery obiektów BLOB za pośrednictwem usługi Azure AD (uprawnienia RBAC lub ACL). Ta funkcja ma pomóc użytkownikom mającym dostęp do kontenerów, ale nie kont magazynu, w których znajdują się kontenery. Aby uzyskać więcej informacji na temat tej funkcji, zobacz nasz przewodnik Wprowadzenie.
* Uzyskaj i Przerwij dzierżawę teraz pracują z RBAC. [#1354](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1354)
* Zarządzanie zasadami dostępu i ustawianie poziomu dostępu publicznego teraz działa z RBAC. [#1355](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1355)
* Usuwanie folderów obiektów BLOB działa teraz z RBAC. [#1450](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1450)
* Zmiana warstwy dostępu do obiektów BLOB teraz współpracuje z RBAC. [#1446](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1446)
* Teraz możesz szybko zresetować szybki dostęp poprzez "pomoc" → "reset". [#1327](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1327)

### <a name="preview-features"></a>Funkcje w wersji zapoznawczej

* Logowanie do przepływu kodu urządzenia jest teraz dostępne w wersji zapoznawczej. Aby ją włączyć, przejdź do pozycji "wersja zapoznawcza" → "Użyj logowania do przepływu kodu urządzenia". Zachęcamy użytkowników, którzy mieli problemy z pustymi oknami logowania, aby wypróbować tę funkcję, ponieważ może to być bardziej niezawodna forma logowania.
* Eksplorator usługi Storage zintegrowany z usługą AzCopy jest obecnie dostępny w wersji zapoznawczej. Aby ją włączyć, przejdź do pozycji "wersja zapoznawcza" → "Użyj AzCopy do ulepszonego przekazywania i pobierania obiektów BLOB". Transfery obiektów BLOB zakończonych za pomocą AzCopy powinny być szybsze i bardziej wydajne.

### <a name="fixes"></a>Poprawki

* Ustalono, że nie można załadować więcej niż 50 subskrypcji dla jednego konta. [#1416](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1416)
* Naprawiono przycisk "Zaloguj się" nie działa na pasku informacyjnym, który pojawia się, gdy łącze bezpośrednie nie powiedzie się. [#1358](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1358)
* Naprawiono brak przekazywania plików aplikacji w witrynie macOS. [#1119](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1119)
* Naprawiono "ponawianie wszystkich" nie działa dla nieudanej zmiany nazwy obiektu BLOB. [#992](https://www.github.com/Microsoft/AzureStorageExplorer/issues/992)
* Naprawiono "Cancel", podczas otwierania obiektu BLOB. [#1464](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1464)
* Naprawiono wiele problemów dotyczących pisowni i etykietek narzędzi w całym produkcie. Wiele podziękowania dla wszystkich użytkowników, którzy zgłosili te problemy! [#1303](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1303), [#1328](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1328), [#1329](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1329), [#1331](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1331), [#1336](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1336), [#1352](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1352), [#1368](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1368), [#1395](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1395)

### <a name="known-issues"></a>Znane problemy

* Podczas pobierania obiektu BLOB, który nie jest AzCopy, MD5 dla dużych plików nie jest weryfikowany. Jest to spowodowane usterką w zestawie SDK magazynu. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* W przypadku korzystania z funkcji RBAC Eksplorator usługi Storage wymaga pewnych uprawnień do warstwy zarządzania w celu uzyskania dostępu do zasobów magazynu. Więcej informacji można znaleźć w [przewodniku rozwiązywania problemów](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) .
* Próba uzyskania dostępu do ADLS Gen2 obiektów blob, gdy serwer proxy może zakończyć się niepowodzeniem.
* Odłączanie od zasobu dołączonego za pomocą identyfikatora URI SAS, takiego jak kontener obiektów blob, może spowodować błąd, który uniemożliwia poprawne wyświetlanie innych załączników. Aby obejść ten problem, wystarczy odświeżyć węzeł grupy. Zobacz 537 # Aby uzyskać więcej informacji.
* Jeśli używasz programu VS dla komputerów Mac i nigdy nie zostały utworzone niestandardowej konfiguracji usługi AAD, możesz nie mieć możliwości logowania. Aby obejść ten problem, usuń zawartość ~ /. IdentityService/AadConfigurations. Jeśli tak się nie odblokowuje, komentarz dotyczący tego problemu.
* Azurite nie została jeszcze w pełni zaimplementowana wszystkie interfejsy API usługi Storage. W związku z tym może występować nieoczekiwanych błędów lub zachowanie w przypadku używania Azurite dla magazynem projektowym.
* W rzadkich przypadkach fokus drzewa może zakończyć się zatrzymaniem na szybki dostęp. Aby odklej fokus, można na nim Odśwież wszystko.
* Przekazywanie z folderu usługi OneDrive nie działa z powodu błędów w środowisku NodeJS. Błąd został rozwiązany, ale nie są jeszcze zintegrowane z elektronów. Aby obejść ten problem podczas przekazywania lub pobierania z kontenera obiektów blob, można użyć funkcji eksperymentalnej AzCopy.
* Przeznaczone dla usługi Azure Stack, przekazywanie pewne pliki jako uzupełnialnych obiektów blob może zakończyć się niepowodzeniem.
* Po kliknięciu przycisku "Anuluj" do zadania, może upłynąć trochę czasu tego zadania anulować. Dzieje się tak dlatego, że używamy filtru anulowania obejścia tego problemu.
* Jeśli wybrano nieprawidłowy numer PIN/certyfikatu karty inteligentnej, należy uruchomić ponownie, aby mogła mieć Eksploratora usługi Storage zapomnij tej decyzji.
* Zmiana nazwy obiektów blob (pojedynczo lub w kontenerze obiektów blob zmieniono nazwę) nie zostaną zachowane migawki. Wszystkie właściwości i metadanych obiektów blob, plików oraz jednostki są zachowywane podczas zmiany nazwy.
* Usługa Azure Stack nie obsługuje następujących funkcji. Podjęto próbę użycia tych funkcji podczas pracy z usługą Azure Stack zasobów może spowodować nieoczekiwane błędy.
   * Udziały plików
   * Poziomy dostępu
   * Usuwanie nietrwałe
   * ADLS Gen2
* Powłoka elektronów wykorzystywane przez Eksploratora magazynu ma problemy z niektórych przyspieszania sprzętowego procesora GPU (jednostka przetwarzania grafiki). Jeśli Eksplorator usługi Storage wyświetla puste okno główne (puste), możesz spróbować uruchomić Eksplorator usługi Storage z wiersza polecenia i wyłączyć Przyspieszenie GPU, dodając przełącznik `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Uruchomienie Eksplorator usługi Storage w systemie Linux wymaga wcześniejszego zainstalowania niektórych zależności. Aby uzyskać więcej informacji, zapoznaj się z [przewodnikiem rozwiązywania problemów](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) Eksplorator usługi Storage.

## <a name="version-181"></a>1\.8.1 wersja
5/13/2019

### <a name="hotfixes"></a>Poprawki
* W niektórych przypadkach kliknięcie opcji "Załaduj więcej" na poziomie zasobu nie zwróci następnej strony zasobów. Ten problem został rozwiązany. [#1359](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1359)
* W systemie Windows pobieranie AzCopy kończy się niepowodzeniem w przypadku pobrania pojedynczego pliku lub folderu, a nazwa pliku lub folderu miała znak, który był nieprawidłowy dla ścieżki systemu Windows. Ten problem został rozwiązany. [#1350](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1350)
* W bardzo rzadkich przypadkach podczas przeprowadzania zmiany nazwy udziału plików lub zmiany nazwy w udziale plików, jeśli kopie dla zmiany nazwy nie powiodły się lub jeśli usługa Storage nie mogła potwierdzić sukcesu kopii na platformie Azure, istnieje możliwość, że Eksplorator usługi Storage usunąć Pliki riginal przed ukończeniem kopiowania. Ten problem został rozwiązany.

### <a name="new"></a>Nowa

* Zintegrowana wersja AzCopy została zaktualizowana do wersji 10.1.0.
* Za pomocą klawiszy Ctrl/CMD + R można teraz odświeżyć aktualnie fokusowy Edytor. [#1097](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1097)
* Wersja interfejsu API magazynu Azure Stack została zmieniona na 2017-04-17.
* Okno dialogowe Zarządzanie dostępem dla ADLS Gen2 będzie teraz przechowywać maskę w sposób podobny do innych narzędzi dostępu POSIX. Interfejs użytkownika wyświetli również ostrzeżenie, gdy zostanie wprowadzona zmiana, która powoduje, że uprawnienia użytkownika lub grupy przekraczają granice maski. [#1253](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1253)
* W przypadku operacji przekazywania AzCopy flaga obliczania i ustawiania skrótu MD5 jest teraz włączona. [#1223](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1223)


### <a name="preview-features"></a>Funkcje w wersji zapoznawczej

* Logowanie do przepływu kodu urządzenia jest teraz dostępne w wersji zapoznawczej. Aby ją włączyć, przejdź do pozycji "wersja zapoznawcza" → "Użyj logowania do przepływu kodu urządzenia". Zachęcamy użytkowników, którzy mieli problemy z pustymi oknami logowania, aby wypróbować tę funkcję, ponieważ może to być bardziej niezawodna forma logowania.
* Eksplorator usługi Storage zintegrowany z usługą AzCopy jest obecnie dostępny w wersji zapoznawczej. Aby ją włączyć, przejdź do pozycji "wersja zapoznawcza" → "Użyj AzCopy do ulepszonego przekazywania i pobierania obiektów BLOB". Transfery obiektów BLOB zakończonych za pomocą AzCopy powinny być szybsze i bardziej wydajne.

### <a name="fixes"></a>Poprawki

* W oknie dialogowym zasady dostępu nie będzie już ustawiana Data wygaśnięcia zasad dostępu do magazynu, które nie mają wygaśnięcia. [#764](https://www.github.com/Microsoft/AzureStorageExplorer/issues/764)
* Wprowadzono pewne zmiany w oknie dialogowym generowanie sygnatury dostępu współdzielonego, aby upewnić się, że są one używane prawidłowo podczas generowania SAS. [#1269](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1269)
* Podczas próby przekazania pliku wyrównanego bajtem o postaci innej niż 512 do obiektu BLOB stronicowania Eksplorator usługi Storage będzie teraz uwidaczniał bardziej istotny błąd. [#1050](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1050)
* Kopiowanie kontenera obiektów blob, które używało nazwy wyświetlanej, nie powiedzie się. Teraz jest używana rzeczywista nazwa kontenera obiektów BLOB. [#1166](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1166)
* Podjęto próbę wykonania pewnych akcji w folderze ADLS Gen2, w którym występują znaki Unicode w nazwie. Wszystkie akcje powinny teraz funkcjonować. [#980](https://www.github.com/Microsoft/AzureStorageExplorer/issues/980)

### <a name="known-issues"></a>Znane problemy

* Podczas pobierania obiektu BLOB, który nie jest AzCopy, MD5 dla dużych plików nie jest weryfikowany. Jest to spowodowane usterką w zestawie SDK magazynu. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* W przypadku korzystania z funkcji RBAC Eksplorator usługi Storage wymaga pewnych uprawnień do warstwy zarządzania w celu uzyskania dostępu do zasobów magazynu. Więcej informacji można znaleźć w [przewodniku rozwiązywania problemów](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) .
* Próba uzyskania dostępu do ADLS Gen2 obiektów blob, gdy serwer proxy może zakończyć się niepowodzeniem.
* Odłączanie od zasobu dołączonego za pomocą identyfikatora URI SAS, takiego jak kontener obiektów blob, może spowodować błąd, który uniemożliwia poprawne wyświetlanie innych załączników. Aby obejść ten problem, wystarczy odświeżyć węzeł grupy. Zobacz 537 # Aby uzyskać więcej informacji.
* Jeśli używasz programu VS dla komputerów Mac i nigdy nie zostały utworzone niestandardowej konfiguracji usługi AAD, możesz nie mieć możliwości logowania. Aby obejść ten problem, usuń zawartość ~ /. IdentityService/AadConfigurations. Jeśli tak się nie odblokowuje, komentarz dotyczący tego problemu.
* Azurite nie została jeszcze w pełni zaimplementowana wszystkie interfejsy API usługi Storage. W związku z tym może występować nieoczekiwanych błędów lub zachowanie w przypadku używania Azurite dla magazynem projektowym.
* W rzadkich przypadkach fokus drzewa może zakończyć się zatrzymaniem na szybki dostęp. Aby odklej fokus, można na nim Odśwież wszystko.
* Przekazywanie z folderu usługi OneDrive nie działa z powodu błędów w środowisku NodeJS. Błąd został rozwiązany, ale nie są jeszcze zintegrowane z elektronów. Aby obejść ten problem podczas przekazywania lub pobierania z kontenera obiektów blob, można użyć funkcji eksperymentalnej AzCopy.
* Przeznaczone dla usługi Azure Stack, przekazywanie pewne pliki jako uzupełnialnych obiektów blob może zakończyć się niepowodzeniem.
* Po kliknięciu przycisku "Anuluj" do zadania, może upłynąć trochę czasu tego zadania anulować. Dzieje się tak dlatego, że używamy filtru anulowania obejścia tego problemu.
* Jeśli wybrano nieprawidłowy numer PIN/certyfikatu karty inteligentnej, należy uruchomić ponownie, aby mogła mieć Eksploratora usługi Storage zapomnij tej decyzji.
* Zmiana nazwy obiektów blob (pojedynczo lub w kontenerze obiektów blob zmieniono nazwę) nie zostaną zachowane migawki. Wszystkie właściwości i metadanych obiektów blob, plików oraz jednostki są zachowywane podczas zmiany nazwy.
* Usługa Azure Stack nie obsługuje następujących funkcji. Podjęto próbę użycia tych funkcji podczas pracy z usługą Azure Stack zasobów może spowodować nieoczekiwane błędy.
   * Udziały plików
   * Poziomy dostępu
   * Usuwanie nietrwałe
   * ADLS Gen2
* Powłoka elektronów wykorzystywane przez Eksploratora magazynu ma problemy z niektórych przyspieszania sprzętowego procesora GPU (jednostka przetwarzania grafiki). Jeśli Eksplorator usługi Storage wyświetla puste okno główne (puste), możesz spróbować uruchomić Eksplorator usługi Storage z wiersza polecenia i wyłączyć Przyspieszenie GPU, dodając przełącznik `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Uruchomienie Eksplorator usługi Storage w systemie Linux wymaga wcześniejszego zainstalowania niektórych zależności. Aby uzyskać więcej informacji, zapoznaj się z [przewodnikiem rozwiązywania problemów](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) Eksplorator usługi Storage.

## <a name="version-180"></a>1\.8.0 wersja
5/1/2019

### <a name="new"></a>Nowa

* Zintegrowana wersja AzCopy została zaktualizowana do wersji 10.1.0.
* Za pomocą klawiszy Ctrl/CMD + R można teraz odświeżyć aktualnie fokusowy Edytor. [#1097](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1097)
* Wersja interfejsu API magazynu Azure Stack została zmieniona na 2017-04-17.
* Okno dialogowe Zarządzanie dostępem dla ADLS Gen2 będzie teraz przechowywać maskę w sposób podobny do innych narzędzi dostępu POSIX. Interfejs użytkownika wyświetli również ostrzeżenie, gdy zostanie wprowadzona zmiana, która powoduje, że uprawnienia użytkownika lub grupy przekraczają granice maski. [#1253](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1253)
* W przypadku operacji przekazywania AzCopy flaga obliczania i ustawiania skrótu MD5 jest teraz włączona. [#1223](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1223)


### <a name="preview-features"></a>Funkcje w wersji zapoznawczej

* Logowanie do przepływu kodu urządzenia jest teraz dostępne w wersji zapoznawczej. Aby ją włączyć, przejdź do pozycji "wersja zapoznawcza" → "Użyj logowania do przepływu kodu urządzenia". Zachęcamy użytkowników, którzy mieli problemy z pustymi oknami logowania, aby wypróbować tę funkcję, ponieważ może to być bardziej niezawodna forma logowania.
* Eksplorator usługi Storage zintegrowany z usługą AzCopy jest obecnie dostępny w wersji zapoznawczej. Aby ją włączyć, przejdź do pozycji "wersja zapoznawcza" → "Użyj AzCopy do ulepszonego przekazywania i pobierania obiektów BLOB". Transfery obiektów BLOB zakończonych za pomocą AzCopy powinny być szybsze i bardziej wydajne.

### <a name="fixes"></a>Poprawki

* W oknie dialogowym zasady dostępu nie będzie już ustawiana Data wygaśnięcia zasad dostępu do magazynu, które nie mają wygaśnięcia. [#764](https://www.github.com/Microsoft/AzureStorageExplorer/issues/764)
* Wprowadzono pewne zmiany w oknie dialogowym generowanie sygnatury dostępu współdzielonego, aby upewnić się, że są one używane prawidłowo podczas generowania SAS. [#1269](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1269)
* Podczas próby przekazania pliku wyrównanego bajtem o postaci innej niż 512 do obiektu BLOB stronicowania Eksplorator usługi Storage będzie teraz uwidaczniał bardziej istotny błąd. [#1050](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1050)
* Kopiowanie kontenera obiektów blob, które używało nazwy wyświetlanej, nie powiedzie się. Teraz jest używana rzeczywista nazwa kontenera obiektów BLOB. [#1166](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1166)
* Podjęto próbę wykonania pewnych akcji w folderze ADLS Gen2, w którym występują znaki Unicode w nazwie. Wszystkie akcje powinny teraz funkcjonować. [#980](https://www.github.com/Microsoft/AzureStorageExplorer/issues/980)

### <a name="known-issues"></a>Znane problemy

* Podczas pobierania obiektu BLOB, który nie jest AzCopy, MD5 dla dużych plików nie jest weryfikowany. Jest to spowodowane usterką w zestawie SDK magazynu. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* W przypadku korzystania z funkcji RBAC Eksplorator usługi Storage wymaga pewnych uprawnień do warstwy zarządzania w celu uzyskania dostępu do zasobów magazynu. Więcej informacji można znaleźć w [przewodniku rozwiązywania problemów](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) .
* Próba uzyskania dostępu do ADLS Gen2 obiektów blob, gdy serwer proxy może zakończyć się niepowodzeniem.
* Odłączanie od zasobu dołączonego za pomocą identyfikatora URI SAS, takiego jak kontener obiektów blob, może spowodować błąd, który uniemożliwia poprawne wyświetlanie innych załączników. Aby obejść ten problem, wystarczy odświeżyć węzeł grupy. Zobacz 537 # Aby uzyskać więcej informacji.
* Jeśli używasz programu VS dla komputerów Mac i nigdy nie zostały utworzone niestandardowej konfiguracji usługi AAD, możesz nie mieć możliwości logowania. Aby obejść ten problem, usuń zawartość ~ /. IdentityService/AadConfigurations. Jeśli tak się nie odblokowuje, komentarz dotyczący tego problemu.
* Azurite nie została jeszcze w pełni zaimplementowana wszystkie interfejsy API usługi Storage. W związku z tym może występować nieoczekiwanych błędów lub zachowanie w przypadku używania Azurite dla magazynem projektowym.
* W rzadkich przypadkach fokus drzewa może zakończyć się zatrzymaniem na szybki dostęp. Aby odklej fokus, można na nim Odśwież wszystko.
* Przekazywanie z folderu usługi OneDrive nie działa z powodu błędów w środowisku NodeJS. Błąd został rozwiązany, ale nie są jeszcze zintegrowane z elektronów. Aby obejść ten problem podczas przekazywania lub pobierania z kontenera obiektów blob, można użyć funkcji eksperymentalnej AzCopy.
* Przeznaczone dla usługi Azure Stack, przekazywanie pewne pliki jako uzupełnialnych obiektów blob może zakończyć się niepowodzeniem.
* Po kliknięciu przycisku "Anuluj" do zadania, może upłynąć trochę czasu tego zadania anulować. Dzieje się tak dlatego, że używamy filtru anulowania obejścia tego problemu.
* Jeśli wybrano nieprawidłowy numer PIN/certyfikatu karty inteligentnej, należy uruchomić ponownie, aby mogła mieć Eksploratora usługi Storage zapomnij tej decyzji.
* Zmiana nazwy obiektów blob (pojedynczo lub w kontenerze obiektów blob zmieniono nazwę) nie zostaną zachowane migawki. Wszystkie właściwości i metadanych obiektów blob, plików oraz jednostki są zachowywane podczas zmiany nazwy.
* Usługa Azure Stack nie obsługuje następujących funkcji. Podjęto próbę użycia tych funkcji podczas pracy z usługą Azure Stack zasobów może spowodować nieoczekiwane błędy.
   * Udziały plików
   * Poziomy dostępu
   * Usuwanie nietrwałe
   * ADLS Gen2
* Powłoka elektronów wykorzystywane przez Eksploratora magazynu ma problemy z niektórych przyspieszania sprzętowego procesora GPU (jednostka przetwarzania grafiki). Jeśli Eksplorator usługi Storage wyświetla puste okno główne (puste), możesz spróbować uruchomić Eksplorator usługi Storage z wiersza polecenia i wyłączyć Przyspieszenie GPU, dodając przełącznik `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Uruchomienie Eksplorator usługi Storage w systemie Linux wymaga wcześniejszego zainstalowania niektórych zależności. Aby uzyskać więcej informacji, zapoznaj się z [przewodnikiem rozwiązywania problemów](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) Eksplorator usługi Storage.

## <a name="version-170"></a>Wersja 1.7.0
3/5/2019

### <a name="download-azure-storage-explorer-170"></a>Pobierz Eksplorator usługi Azure Storage 1.7.0
- [Eksplorator usługi Azure Storage 1.7.0 dla systemu Windows](https://go.microsoft.com/fwlink/?LinkId=708343)
- [Eksplorator usługi Azure Storage 1.7.0 dla komputerów Mac](https://go.microsoft.com/fwlink/?LinkId=708342)
- [Eksplorator usługi Azure Storage 1.7.0 dla systemu Linux](https://go.microsoft.com/fwlink/?LinkId=722418)

### <a name="new"></a>Nowa

* Teraz można zmienić właściciela i grupę będącą właścicielem podczas zarządzania dostępem do kontenera ADLS Gen2, pliku lub folderu.
* W systemie Windows Aktualizowanie Eksplorator usługi Storage z poziomu produktu jest teraz instalacją przyrostową. Powinno to spowodować szybsze działanie aktualizacji. Jeśli wolisz czystą instalację, możesz pobrać [Instalatora](https://azure.microsoft.com/features/storage-explorer/) samodzielnie, a następnie zainstalować ręcznie. #1089

### <a name="preview-features"></a>Funkcje w wersji zapoznawczej

* Logowanie do przepływu kodu urządzenia jest teraz dostępne w wersji zapoznawczej. Aby ją włączyć, przejdź do pozycji "wersja zapoznawcza" → "Użyj logowania do przepływu kodu urządzenia". Zachęcamy użytkowników, którzy mieli problemy z pustymi oknami logowania, aby wypróbować tę funkcję, ponieważ może to być bardziej niezawodna forma logowania. #938
* Eksplorator usługi Storage zintegrowany z usługą AzCopy jest obecnie dostępny w wersji zapoznawczej. Aby ją włączyć, przejdź do pozycji "wersja zapoznawcza" → "Użyj AzCopy do ulepszonego przekazywania i pobierania obiektów BLOB". Transfery obiektów BLOB zakończonych za pomocą AzCopy powinny być szybsze i bardziej wydajne.

### <a name="fixes"></a>Poprawki

* Teraz można wybrać typ obiektu BLOB, który ma zostać przekazany, gdy AzCopy jest włączony. #1111
* Wcześniej, jeśli włączono statyczne witryny sieci Web dla konta magazynu ADLS Gen2, a następnie dołączono je przy użyciu nazwy i klucza, Eksplorator usługi Storage nie wykryła, że włączono hierarchiczną przestrzeń nazw. Ten problem został rozwiązany. #1081
* W Edytorze obiektów BLOB Sortuj według pozostałego okresu przechowywania lub jego stanu. Ten problem został rozwiązany. #1106
* Po 1.5.0, Eksplorator usługi Storage już nie czekać na zakończenie wykonywania kopii po stronie serwera przed zgłoszeniem sukcesu podczas zmiany nazwy lub kopiowania & wklejania. Ten problem został rozwiązany. #976
* W przypadku korzystania z funkcji eksperymentalnej AzCopy polecenie skopiowane po kliknięciu polecenia "Kopiuj polecenie do schowka" nigdy nie zawsze możliwy do uruchomienia. Teraz wszystkie polecenia, które są konieczne do ręcznego uruchomienia transferu, zostaną skopiowane. #1079
* Wcześniej ADLS Gen2 obiekty blob były niedostępne, jeśli jesteś za serwerem proxy. Zostało to spowodowane usterką w nowej bibliotece sieciowej używanej przez zestaw SDK magazynu. W programie 1.7.0 podjęto próbę wyeliminowania tego problemu, ale niektóre osoby mogą nadal widzieć problemy. Pełna poprawka zostanie wydana w przyszłej aktualizacji. #1090
* W 1.7.0, w oknie dialogowym Zapisz plik jest teraz poprawnie zapisywana ostatnio zapisana lokalizacja pliku. #16
* W panelu Właściwości warstwa SKU konta magazynu była wyświetlana jako rodzaj konta. Ten problem został rozwiązany. #654
* Czasami nie było możliwe przerwanie dzierżawy obiektu BLOB, nawet w przypadku poprawnego wprowadzenia nazwy obiektu BLOB. Ten problem został rozwiązany. #1070

### <a name="known-issues"></a>Znane problemy

* W przypadku korzystania z funkcji RBAC Eksplorator usługi Storage wymaga pewnych uprawnień do warstwy zarządzania w celu uzyskania dostępu do zasobów magazynu. Więcej informacji można znaleźć w [przewodniku rozwiązywania problemów](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) .
* Próba uzyskania dostępu do ADLS Gen2 obiektów blob, gdy serwer proxy może zakończyć się niepowodzeniem.
* Odłączanie od zasobu dołączonego za pomocą identyfikatora URI SAS, takiego jak kontener obiektów blob, może spowodować błąd, który uniemożliwia poprawne wyświetlanie innych załączników. Aby obejść ten problem, wystarczy odświeżyć węzeł grupy. Zobacz 537 # Aby uzyskać więcej informacji.
* Odłączanie od zasobu dołączonego za pomocą identyfikatora URI SAS, takiego jak kontener obiektów blob, może spowodować błąd, który uniemożliwia poprawne wyświetlanie innych załączników. Aby obejść ten problem, wystarczy odświeżyć węzeł grupy. Aby uzyskać więcej informacji, zobacz #537.
* Jeśli używasz programu VS dla komputerów Mac i nigdy nie zostały utworzone niestandardowej konfiguracji usługi AAD, możesz nie mieć możliwości logowania. Aby obejść ten problem, usuń zawartość ~ /. IdentityService/AadConfigurations. Jeśli tak się nie odblokowuje, komentarz dotyczący tego problemu.
* Azurite nie została jeszcze w pełni zaimplementowana wszystkie interfejsy API usługi Storage. W związku z tym może występować nieoczekiwanych błędów lub zachowanie w przypadku używania Azurite dla magazynem projektowym.
* W rzadkich przypadkach fokus drzewa może zakończyć się zatrzymaniem na szybki dostęp. Aby odklej fokus, można na nim Odśwież wszystko.
* Przekazywanie z folderu usługi OneDrive nie działa z powodu błędów w środowisku NodeJS. Błąd został rozwiązany, ale nie są jeszcze zintegrowane z elektronów. Aby obejść ten problem podczas przekazywania lub pobierania z kontenera obiektów blob, można użyć funkcji eksperymentalnej AzCopy.
* Przeznaczone dla usługi Azure Stack, przekazywanie pewne pliki jako uzupełnialnych obiektów blob może zakończyć się niepowodzeniem.
* Po kliknięciu przycisku "Anuluj" do zadania, może upłynąć trochę czasu tego zadania anulować. Dzieje się tak dlatego, że używamy filtru anulowania obejścia tego problemu.
* Jeśli wybrano nieprawidłowy numer PIN/certyfikatu karty inteligentnej, należy uruchomić ponownie, aby mogła mieć Eksploratora usługi Storage zapomnij tej decyzji.
* Zmiana nazwy obiektów blob (pojedynczo lub w kontenerze obiektów blob zmieniono nazwę) nie zostaną zachowane migawki. Wszystkie właściwości i metadanych obiektów blob, plików oraz jednostki są zachowywane podczas zmiany nazwy.
* Usługa Azure Stack nie obsługuje następujących funkcji. Podjęto próbę użycia tych funkcji podczas pracy z usługą Azure Stack zasobów może spowodować nieoczekiwane błędy.
   * Udziały plików
   * Poziomy dostępu
   * Usuwanie nietrwałe
* Powłoka elektronów wykorzystywane przez Eksploratora magazynu ma problemy z niektórych przyspieszania sprzętowego procesora GPU (jednostka przetwarzania grafiki). Jeśli Eksplorator usługi Storage wyświetla puste okno główne (puste), możesz spróbować uruchomić Eksplorator usługi Storage z wiersza polecenia i wyłączyć Przyspieszenie GPU, dodając przełącznik `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* W przypadku użytkowników systemu Linux należy zainstalować program [.NET Core 2,0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
* Dla użytkowników w systemie Ubuntu 14.04, konieczne będzie upewnij się, GCC jest aktualne — można to zrobić, uruchamiając następujące polecenia, a następnie ponownym uruchomieniu komputera:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Dla użytkowników w systemie Ubuntu 17.04 będą musieli zainstalować GConf — można to zrobić, uruchamiając następujące polecenia, a następnie ponownym uruchomieniu komputera:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-162"></a>Wersja 1.6.2
1/9/2019

### <a name="hotfixes"></a>Poprawki
* W 1.6.1 jednostki dodane do ADLS Gen2 list ACL według identyfikatora ObjectId, które nie były użytkownikami były zawsze dodawane jako grupy. Teraz tylko grupy są dodawane jako grupy, a jednostki, takie jak aplikacje przedsiębiorstwa andService podmioty zabezpieczeń, są dodawane jako użytkownicy. [#1049](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1049)
* Jeśli konto magazynu ADLS Gen2 nie zawierało kontenerów i zostało dołączone przy użyciu nazwy i klucza, Eksplorator usługi Storage nie wykryje, że konto magazynu zostało ADLS Gen2. Ten problem został rozwiązany. [#1048](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1048)
* W programie 1.6.0 konflikty podczas kopiowania i wklejania nie monitują o rozwiązanie. Zamiast tego kopia powodująca konflikt może po prostu kończyć się niepowodzeniem. Teraz podczas pierwszego konfliktu zostanie wyświetlony monit o jego rozwiązanie. [#1014](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1014)
* Ze względu na ograniczenia interfejsu API wszystkie walidacje identyfikatora ObjectId w oknie dialogowym Zarządzanie dostępem zostały wyłączone. Sprawdzanie poprawności będzie teraz odbywać się tylko dla UPN użytkownika. [#954](https://www.github.com/Microsoft/AzureStorageExplorer/issues/954)
* W oknie dialogowym ADLS Gen2 zarządzanie dostępem nie można zmodyfikować uprawnień dla grupy. Ten problem został rozwiązany. [#958](https://www.github.com/Microsoft/AzureStorageExplorer/issues/958)
* Dodano obsługę funkcji przeciągania i upuszczania w edytorze ADLS Gen2. [#953](https://www.github.com/Microsoft/AzureStorageExplorer/issues/953)
* Właściwość adresu URL w oknie dialogowym właściwości dla ADLS Gen2 plików i folderów czasami brakuje znaku "/". Ten problem został rozwiązany. [#960](https://www.github.com/Microsoft/AzureStorageExplorer/issues/960)
* Jeśli pobranie bieżących uprawnień do kontenera ADLS Gen2, pliku lub folderu nie powiedzie się, w dzienniku aktywności zostanie wyświetlony komunikat o błędzie. [#965](https://www.github.com/Microsoft/AzureStorageExplorer/issues/965)
* Ścieżka tymczasowa utworzona na potrzeby otwierania plików została skrócona, aby zmniejszyć prawdopodobieństwo utworzenia ścieżki, która jest dłuższa niż MAX_PATH w systemie Windows. [#93](https://www.github.com/Microsoft/AzureStorageExplorer/issues/93)
* Okno dialogowe Połącz jest teraz wyświetlane, gdy nie ma żadnych zalogowanych użytkowników, a żadne zasoby nie zostały dołączone. [#944](https://www.github.com/Microsoft/AzureStorageExplorer/issues/944)
* W 1.6.0, zapisywanie właściwości dla obiektów blob i plików innych niż SNS spowoduje zakodowanie wartości każdej właściwości. Spowodowało to niepotrzebne kodowanie wartości, które zawierają tylko znaki ASCII. Teraz wartości będą kodowane tylko wtedy, gdy zawierają znaki inne niż ASCII. [#986](https://www.github.com/Microsoft/AzureStorageExplorer/issues/986)
* Przekazanie folderu do kontenera obiektów blob, który nie jest SNS, zakończy się niepowodzeniem, jeśli użyto sygnatury dostępu współdzielonego i nie ma uprawnień do odczytu. Ten problem został rozwiązany. [#970](https://www.github.com/Microsoft/AzureStorageExplorer/issues/970)
* Anulowanie transferu AzCopy nie zadziałało. Ten problem został rozwiązany. [#943](https://www.github.com/Microsoft/AzureStorageExplorer/issues/943)
* AzCopy Wystąpił błąd podczas próby pobrania folderu z kontenera obiektów BLOB ADLS Gen2, jeśli jego nazwa zawiera spacje. Ten problem został rozwiązany. [#990](https://www.github.com/Microsoft/AzureStorageExplorer/issues/990)
* Edytor CosmosDB został przerwany w 1.6.0. Został on rozwiązany. [#950](https://www.github.com/Microsoft/AzureStorageExplorer/issues/950)
        
### <a name="new"></a>Nowa

* Teraz możesz używać Eksplorator usługi Storage, aby uzyskiwać dostęp do danych obiektów BLOB za pomocą [RBAC](https://go.microsoft.com/fwlink/?linkid=2045904&clcid=0x409). Jeśli użytkownik jest zalogowany i Eksplorator usługi Storage nie może pobrać kluczy dla konta magazynu, zostanie użyty token OAuth do uwierzytelniania podczas korzystania z danych.
* Eksplorator usługi Storage teraz obsługuje konta magazynu ADLS Gen2. Gdy Eksplorator usługi Storage wykryje, że dla konta magazynu jest włączona hierarchiczna przestrzeń nazw, zobaczysz "(ADLS Gen2 wersja zapoznawcza)" obok nazwy konta magazynu. Eksplorator usługi Storage jest w stanie wykryć, czy hierarchiczna przestrzeń nazw jest włączona, gdy użytkownik jest zalogowany, lub czy konto magazynu zostało dołączone przy użyciu nazwy i klucza. W przypadku kont usługi ADLS Gen2 Storage można użyć Eksplorator usługi Storage do:
  * Tworzenie i usuwanie kontenerów
  * Zarządzanie właściwościami i uprawnieniami kontenera (po lewej stronie)
  * Wyświetlanie danych i nawigowanie w kontenerach
  * Utwórz nowe foldery
  * Przekazywanie, pobieranie, zmiana nazwy i usuwanie plików i folderów
  * Zarządzanie właściwościami i uprawnieniami plików i folderów (po prawej stronie).
    
    Inne typowe funkcje obiektów blob, takie jak usuwanie nietrwałe i migawki, nie są obecnie dostępne. Zarządzanie uprawnieniami jest również dostępne tylko po zalogowaniu się. Ponadto podczas pracy na koncie magazynu ADLS Gen2 Eksplorator usługi Storage będzie używać AzCopy do wszystkich operacji przekazywania i pobierania oraz domyślnie do korzystania z poświadczeń nazw i kluczy dla wszystkie operacje, jeśli są dostępne.
* Po silnych opiniach użytkowników przerwanie dzierżawy może być ponownie używane do jednoczesnego przerwania dzierżaw w wielu obiektach Blob.

### <a name="known-issues"></a>Znane problemy

* W przypadku pobierania z konta magazynu ADLS Gen2, jeśli jeden z transferowanych plików już istnieje, AzCopy czasami ulegnie awarii. Ten problem zostanie rozwiązany w nadchodzącej poprawce.
* Odłączanie od zasobu dołączonego za pomocą identyfikatora URI SAS, takiego jak kontener obiektów blob, może spowodować błąd, który uniemożliwia poprawne wyświetlanie innych załączników. Aby obejść ten problem, wystarczy odświeżyć węzeł grupy. Aby uzyskać więcej informacji, zobacz #537.
* Jeśli używasz programu VS dla komputerów Mac i nigdy nie zostały utworzone niestandardowej konfiguracji usługi AAD, możesz nie mieć możliwości logowania. Aby obejść ten problem, usuń zawartość ~ /. IdentityService/AadConfigurations. Jeśli tak się nie odblokowuje, komentarz dotyczący tego problemu.
* Azurite nie została jeszcze w pełni zaimplementowana wszystkie interfejsy API usługi Storage. W związku z tym może występować nieoczekiwanych błędów lub zachowanie w przypadku używania Azurite dla magazynem projektowym.
* W rzadkich przypadkach fokus drzewa może zakończyć się zatrzymaniem na szybki dostęp. Aby odklej fokus, można na nim Odśwież wszystko.
* Przekazywanie z folderu usługi OneDrive nie działa z powodu błędów w środowisku NodeJS. Błąd został rozwiązany, ale nie są jeszcze zintegrowane z elektronów. Aby obejść ten problem podczas przekazywania lub pobierania z kontenera obiektów blob, można użyć funkcji eksperymentalnej AzCopy.
* Przeznaczone dla usługi Azure Stack, przekazywanie pewne pliki jako uzupełnialnych obiektów blob może zakończyć się niepowodzeniem.
* Po kliknięciu przycisku "Anuluj" do zadania, może upłynąć trochę czasu tego zadania anulować. Dzieje się tak dlatego, że używamy filtru anulowania obejścia tego problemu.
* Jeśli wybrano nieprawidłowy numer PIN/certyfikatu karty inteligentnej, należy uruchomić ponownie, aby mogła mieć Eksploratora usługi Storage zapomnij tej decyzji.
* Zmiana nazwy obiektów blob (pojedynczo lub w kontenerze obiektów blob zmieniono nazwę) nie zostaną zachowane migawki. Wszystkie właściwości i metadanych obiektów blob, plików oraz jednostki są zachowywane podczas zmiany nazwy.
* Usługa Azure Stack nie obsługuje następujących funkcji. Podjęto próbę użycia tych funkcji podczas pracy z usługą Azure Stack zasobów może spowodować nieoczekiwane błędy.
   * Udziały plików
   * Poziomy dostępu
   * Usuwanie nietrwałe
* Powłoka elektronów wykorzystywane przez Eksploratora magazynu ma problemy z niektórych przyspieszania sprzętowego procesora GPU (jednostka przetwarzania grafiki). Jeśli Eksplorator usługi Storage wyświetla puste okno główne (puste), możesz spróbować uruchomić Eksplorator usługi Storage z wiersza polecenia i wyłączyć Przyspieszenie GPU, dodając przełącznik `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* W przypadku użytkowników systemu Linux należy zainstalować program [.NET Core 2,0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
* Dla użytkowników w systemie Ubuntu 14.04, konieczne będzie upewnij się, GCC jest aktualne — można to zrobić, uruchamiając następujące polecenia, a następnie ponownym uruchomieniu komputera:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Dla użytkowników w systemie Ubuntu 17.04 będą musieli zainstalować GConf — można to zrobić, uruchamiając następujące polecenia, a następnie ponownym uruchomieniu komputera:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-161"></a>1\.6.1 wersja
12/18/2018

### <a name="hotfixes"></a>Poprawki
* Ze względu na ograniczenia interfejsu API wszystkie walidacje identyfikatora ObjectId w oknie dialogowym Zarządzanie dostępem zostały wyłączone. Sprawdzanie poprawności będzie teraz odbywać się tylko dla UPN użytkownika. [#954](https://www.github.com/Microsoft/AzureStorageExplorer/issues/954)
* W oknie dialogowym ADLS Gen2 zarządzanie dostępem nie można zmodyfikować uprawnień dla grupy. Ten problem został rozwiązany. [#958](https://www.github.com/Microsoft/AzureStorageExplorer/issues/958)
* Dodano obsługę funkcji przeciągania i upuszczania w edytorze ADLS Gen2. [#953](https://www.github.com/Microsoft/AzureStorageExplorer/issues/953)
* Właściwość adresu URL w oknie dialogowym właściwości dla ADLS Gen2 plików i folderów czasami brakuje znaku "/". Ten problem został rozwiązany. [#960](https://www.github.com/Microsoft/AzureStorageExplorer/issues/960)
* Jeśli pobranie bieżących uprawnień do kontenera ADLS Gen2, pliku lub folderu nie powiedzie się, w dzienniku aktywności zostanie wyświetlony komunikat o błędzie. [#965](https://www.github.com/Microsoft/AzureStorageExplorer/issues/965)
* Ścieżka tymczasowa utworzona na potrzeby otwierania plików została skrócona, aby zmniejszyć prawdopodobieństwo utworzenia ścieżki, która jest dłuższa niż MAX_PATH w systemie Windows. [#93](https://www.github.com/Microsoft/AzureStorageExplorer/issues/93)
* Okno dialogowe Połącz jest teraz wyświetlane, gdy nie ma żadnych zalogowanych użytkowników, a żadne zasoby nie zostały dołączone. [#944](https://www.github.com/Microsoft/AzureStorageExplorer/issues/944)
* W 1.6.0, zapisywanie właściwości dla obiektów blob i plików innych niż SNS spowoduje zakodowanie wartości każdej właściwości. Spowodowało to niepotrzebne kodowanie wartości, które zawierają tylko znaki ASCII. Teraz wartości będą kodowane tylko wtedy, gdy zawierają znaki inne niż ASCII. [#986](https://www.github.com/Microsoft/AzureStorageExplorer/issues/986)
* Przekazanie folderu do kontenera obiektów blob, który nie jest SNS, zakończy się niepowodzeniem, jeśli użyto sygnatury dostępu współdzielonego i nie ma uprawnień do odczytu. Ten problem został rozwiązany. [#970](https://www.github.com/Microsoft/AzureStorageExplorer/issues/970)
* Anulowanie transferu AzCopy nie zadziałało. Ten problem został rozwiązany. [#943](https://www.github.com/Microsoft/AzureStorageExplorer/issues/943)
* AzCopy Wystąpił błąd podczas próby pobrania folderu z kontenera obiektów BLOB ADLS Gen2, jeśli jego nazwa zawiera spacje. Ten problem został rozwiązany. [#990](https://www.github.com/Microsoft/AzureStorageExplorer/issues/990)
* Edytor CosmosDB został przerwany w 1.6.0. Został on rozwiązany. [#950](https://www.github.com/Microsoft/AzureStorageExplorer/issues/950)
        
### <a name="new"></a>Nowa

* Teraz możesz używać Eksplorator usługi Storage, aby uzyskiwać dostęp do danych obiektów BLOB za pomocą [RBAC](https://go.microsoft.com/fwlink/?linkid=2045904&clcid=0x409). Jeśli użytkownik jest zalogowany i Eksplorator usługi Storage nie może pobrać kluczy dla konta magazynu, zostanie użyty token OAuth do uwierzytelniania podczas korzystania z danych.
* Eksplorator usługi Storage teraz obsługuje konta magazynu ADLS Gen2. Gdy Eksplorator usługi Storage wykryje, że dla konta magazynu jest włączona hierarchiczna przestrzeń nazw, zobaczysz "(ADLS Gen2 wersja zapoznawcza)" obok nazwy konta magazynu. Eksplorator usługi Storage jest w stanie wykryć, czy hierarchiczna przestrzeń nazw jest włączona, gdy użytkownik jest zalogowany, lub czy konto magazynu zostało dołączone przy użyciu nazwy i klucza. W przypadku kont usługi ADLS Gen2 Storage można użyć Eksplorator usługi Storage do:
  * Tworzenie i usuwanie kontenerów
  * Zarządzanie właściwościami i uprawnieniami kontenera (po lewej stronie)
  * Wyświetlanie danych i nawigowanie w kontenerach
  * Utwórz nowe foldery
  * Przekazywanie, pobieranie, zmiana nazwy i usuwanie plików i folderów
  * Zarządzanie właściwościami i uprawnieniami plików i folderów (po prawej stronie).
    
    Inne typowe funkcje obiektów blob, takie jak usuwanie nietrwałe i migawki, nie są obecnie dostępne. Zarządzanie uprawnieniami jest również dostępne tylko po zalogowaniu się. Ponadto podczas pracy na koncie magazynu ADLS Gen2 Eksplorator usługi Storage będzie używać AzCopy do wszystkich operacji przekazywania i pobierania oraz domyślnie do korzystania z poświadczeń nazw i kluczy dla wszystkie operacje, jeśli są dostępne.
* Po silnych opiniach użytkowników przerwanie dzierżawy może być ponownie używane do jednoczesnego przerwania dzierżaw w wielu obiektach Blob.

### <a name="known-issues"></a>Znane problemy

* W przypadku pobierania z konta magazynu ADLS Gen2, jeśli jeden z transferowanych plików już istnieje, AzCopy czasami ulegnie awarii. Ten problem zostanie rozwiązany w nadchodzącej poprawce.
* Odłączanie od zasobu dołączonego za pomocą identyfikatora URI SAS, takiego jak kontener obiektów blob, może spowodować błąd, który uniemożliwia poprawne wyświetlanie innych załączników. Aby obejść ten problem, wystarczy odświeżyć węzeł grupy. Aby uzyskać więcej informacji, zobacz #537.
* Jeśli używasz programu VS dla komputerów Mac i nigdy nie zostały utworzone niestandardowej konfiguracji usługi AAD, możesz nie mieć możliwości logowania. Aby obejść ten problem, usuń zawartość ~ /. IdentityService/AadConfigurations. Jeśli tak się nie odblokowuje, komentarz dotyczący tego problemu.
* Azurite nie została jeszcze w pełni zaimplementowana wszystkie interfejsy API usługi Storage. W związku z tym może występować nieoczekiwanych błędów lub zachowanie w przypadku używania Azurite dla magazynem projektowym.
* W rzadkich przypadkach fokus drzewa może zakończyć się zatrzymaniem na szybki dostęp. Aby odklej fokus, można na nim Odśwież wszystko.
* Przekazywanie z folderu usługi OneDrive nie działa z powodu błędów w środowisku NodeJS. Błąd został rozwiązany, ale nie są jeszcze zintegrowane z elektronów. Aby obejść ten problem podczas przekazywania lub pobierania z kontenera obiektów blob, można użyć funkcji eksperymentalnej AzCopy.
* Przeznaczone dla usługi Azure Stack, przekazywanie pewne pliki jako uzupełnialnych obiektów blob może zakończyć się niepowodzeniem.
* Po kliknięciu przycisku "Anuluj" do zadania, może upłynąć trochę czasu tego zadania anulować. Dzieje się tak dlatego, że używamy filtru anulowania obejścia tego problemu.
* Jeśli wybrano nieprawidłowy numer PIN/certyfikatu karty inteligentnej, należy uruchomić ponownie, aby mogła mieć Eksploratora usługi Storage zapomnij tej decyzji.
* Zmiana nazwy obiektów blob (pojedynczo lub w kontenerze obiektów blob zmieniono nazwę) nie zostaną zachowane migawki. Wszystkie właściwości i metadanych obiektów blob, plików oraz jednostki są zachowywane podczas zmiany nazwy.
* Usługa Azure Stack nie obsługuje następujących funkcji. Podjęto próbę użycia tych funkcji podczas pracy z usługą Azure Stack zasobów może spowodować nieoczekiwane błędy.
   * Udziały plików
   * Poziomy dostępu
   * Usuwanie nietrwałe
* Powłoka elektronów wykorzystywane przez Eksploratora magazynu ma problemy z niektórych przyspieszania sprzętowego procesora GPU (jednostka przetwarzania grafiki). Jeśli Eksplorator usługi Storage wyświetla puste okno główne (puste), możesz spróbować uruchomić Eksplorator usługi Storage z wiersza polecenia i wyłączyć Przyspieszenie GPU, dodając przełącznik `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* W przypadku użytkowników systemu Linux należy zainstalować program [.NET Core 2,0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
* Dla użytkowników w systemie Ubuntu 14.04, konieczne będzie upewnij się, GCC jest aktualne — można to zrobić, uruchamiając następujące polecenia, a następnie ponownym uruchomieniu komputera:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Dla użytkowników w systemie Ubuntu 17.04 będą musieli zainstalować GConf — można to zrobić, uruchamiając następujące polecenia, a następnie ponownym uruchomieniu komputera:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-160"></a>1\.6.0 wersja
2018-12-05

### <a name="new"></a>Nowa

* Teraz możesz używać Eksplorator usługi Storage, aby uzyskiwać dostęp do danych obiektów BLOB za pomocą [RBAC](https://go.microsoft.com/fwlink/?linkid=2045904&clcid=0x409). Jeśli użytkownik jest zalogowany i Eksplorator usługi Storage nie może pobrać kluczy dla konta magazynu, zostanie użyty token OAuth do uwierzytelniania podczas korzystania z danych.
* Eksplorator usługi Storage teraz obsługuje konta magazynu ADLS Gen2. Gdy Eksplorator usługi Storage wykryje, że dla konta magazynu jest włączona hierarchiczna przestrzeń nazw, zobaczysz "(ADLS Gen2 wersja zapoznawcza)" obok nazwy konta magazynu. Eksplorator usługi Storage jest w stanie wykryć, czy hierarchiczna przestrzeń nazw jest włączona, gdy użytkownik jest zalogowany, lub czy konto magazynu zostało dołączone przy użyciu nazwy i klucza. W przypadku kont usługi ADLS Gen2 Storage można użyć Eksplorator usługi Storage do:
  * Tworzenie i usuwanie kontenerów
  * Zarządzanie właściwościami i uprawnieniami kontenera (po lewej stronie)
  * Wyświetlanie danych i nawigowanie w kontenerach
  * Utwórz nowe foldery
  * Przekazywanie, pobieranie, zmiana nazwy i usuwanie plików i folderów
  * Zarządzanie właściwościami i uprawnieniami plików i folderów (po prawej stronie).
    
    Inne typowe funkcje obiektów blob, takie jak usuwanie nietrwałe i migawki, nie są obecnie dostępne. Zarządzanie uprawnieniami jest również dostępne tylko po zalogowaniu się. Ponadto podczas pracy na koncie magazynu ADLS Gen2 Eksplorator usługi Storage będzie używać AzCopy do wszystkich operacji przekazywania i pobierania oraz domyślnie do korzystania z poświadczeń nazw i kluczy dla wszystkie operacje, jeśli są dostępne.
* Po silnych opiniach użytkowników przerwanie dzierżawy może być ponownie używane do jednoczesnego przerwania dzierżaw w wielu obiektach Blob.

### <a name="known-issues"></a>Znane problemy

* W przypadku pobierania z konta magazynu ADLS Gen2, jeśli jeden z transferowanych plików już istnieje, AzCopy czasami ulegnie awarii. Ten problem zostanie rozwiązany w nadchodzącej poprawce.
* Odłączanie od zasobu dołączonego za pomocą identyfikatora URI SAS, takiego jak kontener obiektów blob, może spowodować błąd, który uniemożliwia poprawne wyświetlanie innych załączników. Aby obejść ten problem, wystarczy odświeżyć węzeł grupy. Aby uzyskać więcej informacji, zobacz #537.
* Jeśli używasz programu VS dla komputerów Mac i nigdy nie zostały utworzone niestandardowej konfiguracji usługi AAD, możesz nie mieć możliwości logowania. Aby obejść ten problem, usuń zawartość ~ /. IdentityService/AadConfigurations. Jeśli tak się nie odblokowuje, komentarz dotyczący tego problemu.
* Azurite nie została jeszcze w pełni zaimplementowana wszystkie interfejsy API usługi Storage. W związku z tym może występować nieoczekiwanych błędów lub zachowanie w przypadku używania Azurite dla magazynem projektowym.
* W rzadkich przypadkach fokus drzewa może zakończyć się zatrzymaniem na szybki dostęp. Aby odklej fokus, można na nim Odśwież wszystko.
* Przekazywanie z folderu usługi OneDrive nie działa z powodu błędów w środowisku NodeJS. Błąd został rozwiązany, ale nie są jeszcze zintegrowane z elektronów. Aby obejść ten problem podczas przekazywania lub pobierania z kontenera obiektów blob, można użyć funkcji eksperymentalnej AzCopy.
* Przeznaczone dla usługi Azure Stack, przekazywanie pewne pliki jako uzupełnialnych obiektów blob może zakończyć się niepowodzeniem.
* Po kliknięciu przycisku "Anuluj" do zadania, może upłynąć trochę czasu tego zadania anulować. Dzieje się tak dlatego, że używamy filtru anulowania obejścia tego problemu.
* Jeśli wybrano nieprawidłowy numer PIN/certyfikatu karty inteligentnej, należy uruchomić ponownie, aby mogła mieć Eksploratora usługi Storage zapomnij tej decyzji.
* Zmiana nazwy obiektów blob (pojedynczo lub w kontenerze obiektów blob zmieniono nazwę) nie zostaną zachowane migawki. Wszystkie właściwości i metadanych obiektów blob, plików oraz jednostki są zachowywane podczas zmiany nazwy.
* Usługa Azure Stack nie obsługuje następujących funkcji. Podjęto próbę użycia tych funkcji podczas pracy z usługą Azure Stack zasobów może spowodować nieoczekiwane błędy.
   * Udziały plików
   * Poziomy dostępu
   * Usuwanie nietrwałe
* Powłoka elektronów wykorzystywane przez Eksploratora magazynu ma problemy z niektórych przyspieszania sprzętowego procesora GPU (jednostka przetwarzania grafiki). Jeśli Eksplorator usługi Storage wyświetla puste okno główne (puste), możesz spróbować uruchomić Eksplorator usługi Storage z wiersza polecenia i wyłączyć Przyspieszenie GPU, dodając przełącznik `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* W przypadku użytkowników systemu Linux należy zainstalować program [.NET Core 2,0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
* Dla użytkowników w systemie Ubuntu 14.04, konieczne będzie upewnij się, GCC jest aktualne — można to zrobić, uruchamiając następujące polecenia, a następnie ponownym uruchomieniu komputera:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Dla użytkowników w systemie Ubuntu 17.04 będą musieli zainstalować GConf — można to zrobić, uruchamiając następujące polecenia, a następnie ponownym uruchomieniu komputera:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-150"></a>Wersja 1.5.0
10 29 2018

### <a name="new"></a>Nowa

* Teraz można używać [AzCopy v10 (wersja zapoznawcza)](https://github.com/Azure/azure-storage-azcopy) do przekazywania i pobierania obiektów BLOB. Aby włączyć tę funkcję przejdź do menu "Eksperymentalne", a następnie kliknij przycisk "Użyj narzędzia AzCopy dla ulepszone obiektu Blob przekazywania i pobierania". Po włączeniu narzędzia AzCopy można używać w następujących scenariuszach:
   * Przekazywanie folderów i pliki do kontenerów obiektów blob za pomocą paska narzędzi lub przeciągnij i upuść.
   * Pobieranie folderów i plików, za pośrednictwem menu paska narzędzi lub kontekstu.

* Ponadto korzystając z narzędzia AzCopy:
   * Możesz skopiować polecenia narzędzia AzCopy, używany w celu wykonania transferu do Schowka. W dzienniku aktywności, po prostu kliknij przycisk "Kopiuj AzCopy polecenia do Schowka".
   * Należy ręcznie odświeżyć Edytor obiektów blob po przekazaniu.
   * Przekazywanie plików do dołączania obiektów BLOB nie jest obsługiwane, a pliki VHD zostaną przekazane jako stronicowe obiekty blob, a wszystkie inne pliki zostaną przekazane jako blokowe obiekty blob.
   * Błędy i konflikty występujące podczas przekazywania lub pobierania nie będą należeć do momentu zakończenia przekazywania lub pobrania.

Na koniec obsługi przy użyciu narzędzia AzCopy z udziałami plików zostanie udostępniona w przyszłości.
* Eksplorator usługi Storage jest teraz, używając elektronów wersji 2.0.11.
* Przerwanie dzierżawy teraz można wykonać tylko na jeden obiekt blob w danym momencie. Ponadto należy wprowadzić nazwę obiektu blob dzierżawy, którego są istotne. Ta zmiana została wprowadzona w celu zmniejszenia prawdopodobieństwa przypadkowego przerwania dzierżawy, szczególnie w przypadku maszyn wirtualnych. #394
* Jeśli nigdy nie występują problemy dotyczące logowania, użytkownik może teraz spróbuj zresetować uwierzytelniania. Przejdź do menu "Pomoc", a następnie kliknij przycisk "Resetuj", aby dostęp do tej funkcji. #419

### <a name="fix"></a>Napraw

* Po opinii silne użytkownika domyślnego emulatora węzła została ponownie włączona. Nadal możesz dodać dodatkowe emulatora połączeń za pośrednictwem okna dialogowego Connect, ale jeśli emulator usługi jest skonfigurowany do korzystania z portów domyślnych umożliwia także "Emulator * domyślnych portów" węźle "Lokalne i dołączone/Storage konta". #669
* Eksplorator usługi Storage będą już nie pozwalają na ustawienie wartości metadanych obiektu blob, które mają spacji wiodących i końcowych. #760
* Przycisk "Zaloguj" był zawsze włączona w tej samej strony okna dialogowego Connect. Teraz jest ona wyłączona, gdy jest to konieczne. #761
* Szybki dostęp nie jest już spowoduje wygenerowanie błędu w konsoli, gdy zostały dodane żadne elementy paska szybki dostęp.

### <a name="known-issues"></a>Znane problemy

* Odłączanie od zasobu dołączonego za pomocą identyfikatora URI SAS, takiego jak kontener obiektów blob, może spowodować błąd, który uniemożliwia poprawne wyświetlanie innych załączników. Aby obejść ten problem, wystarczy odświeżyć węzeł grupy. Aby uzyskać więcej informacji, zobacz #537.
* Jeśli używasz programu VS dla komputerów Mac i nigdy nie zostały utworzone niestandardowej konfiguracji usługi AAD, możesz nie mieć możliwości logowania. Aby obejść ten problem, usuń zawartość ~ /. IdentityService/AadConfigurations. Jeśli tak się nie odblokowuje, komentarz dotyczący tego problemu.
* Azurite nie została jeszcze w pełni zaimplementowana wszystkie interfejsy API usługi Storage. W związku z tym może występować nieoczekiwanych błędów lub zachowanie w przypadku używania Azurite dla magazynem projektowym.
* W rzadkich przypadkach fokus drzewa może zakończyć się zatrzymaniem na szybki dostęp. Aby odklej fokus, można na nim Odśwież wszystko.
* Przekazywanie z folderu usługi OneDrive nie działa z powodu błędów w środowisku NodeJS. Błąd został rozwiązany, ale nie są jeszcze zintegrowane z elektronów. Aby obejść ten problem podczas przekazywania lub pobierania z kontenera obiektów blob, można użyć funkcji eksperymentalnej AzCopy.
* Przeznaczone dla usługi Azure Stack, przekazywanie pewne pliki jako uzupełnialnych obiektów blob może zakończyć się niepowodzeniem.
* Po kliknięciu przycisku "Anuluj" do zadania, może upłynąć trochę czasu tego zadania anulować. Dzieje się tak dlatego, że używamy filtru anulowania obejścia tego problemu.
* Jeśli wybrano nieprawidłowy numer PIN/certyfikatu karty inteligentnej, należy uruchomić ponownie, aby mogła mieć Eksploratora usługi Storage zapomnij tej decyzji.
* Zmiana nazwy obiektów blob (pojedynczo lub w kontenerze obiektów blob zmieniono nazwę) nie zostaną zachowane migawki. Wszystkie właściwości i metadanych obiektów blob, plików oraz jednostki są zachowywane podczas zmiany nazwy.
* Usługa Azure Stack nie obsługuje następujących funkcji. Podjęto próbę użycia tych funkcji podczas pracy z usługą Azure Stack zasobów może spowodować nieoczekiwane błędy.
   * Udziały plików
   * Poziomy dostępu
   * Usuwanie nietrwałe
* Powłoka elektronów wykorzystywane przez Eksploratora magazynu ma problemy z niektórych przyspieszania sprzętowego procesora GPU (jednostka przetwarzania grafiki). Jeśli Eksplorator usługi Storage wyświetla puste okno główne (puste), możesz spróbować uruchomić Eksplorator usługi Storage z wiersza polecenia i wyłączyć Przyspieszenie GPU, dodając przełącznik `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* W przypadku użytkowników systemu Linux należy zainstalować program [.NET Core 2,0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
* Dla użytkowników w systemie Ubuntu 14.04, konieczne będzie upewnij się, GCC jest aktualne — można to zrobić, uruchamiając następujące polecenia, a następnie ponownym uruchomieniu komputera:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Dla użytkowników w systemie Ubuntu 17.04 będą musieli zainstalować GConf — można to zrobić, uruchamiając następujące polecenia, a następnie ponownym uruchomieniu komputera:

    ```
    sudo apt-get install libgconf-2-4
    ```


## <a name="version-144"></a>Wersja 1.4.4
10/15/2018 r.

### <a name="hotfixes"></a>Poprawki
* Wersja interfejsu API usługi Azure Resource Management została wycofana w celu odblokowania użytkowników programu Azure USA dla instytucji rządowych. [#696](https://github.com/Microsoft/AzureStorageExplorer/issues/696)
* Trwa ładowanie pokręteł teraz używają animacji CSS skrócenie GPU wykorzystywane przez Eksploratora magazynu. [#653](https://github.com/Microsoft/AzureStorageExplorer/issues/653)

### <a name="new"></a>Nowa
* Załączniki zewnętrznego zasobu, takie jak w przypadku połączenia SAS i emulatorów, została znacznie ulepszona. Teraz możesz wykonywać następujące czynności:
   * Dostosuj nazwę wyświetlaną zasób, który jest podłączany. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Dołącz do wielu emulatorów lokalnego przy użyciu różnych portów. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Dodaj zasoby dołączone do paska szybki dostęp. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* Eksplorator usługi Storage obsługuje teraz usuwanie nietrwałe. Możesz:
   * Skonfiguruj zasady usuwanie nietrwałe, klikając prawym przyciskiem myszy w węźle kontenery obiektów Blob konta magazynu.
   * Widok nietrwale usunięte obiekty BLOB w Edytorze obiektów Blob, wybierając pozycję "Active i usunięte obiekty BLOB" w menu rozwijanym obok paska nawigacji.
   * Usuniętych nietrwale usunięte obiekty BLOB.

### <a name="fixes"></a>Poprawki
* Akcja "Konfiguruj ustawienia specyfikacji CORS" nie jest już dostępna dla kont usługi Premium Storage ponieważ kont usługi Premium Storage nie obsługują mechanizmu CORS. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Teraz jest właściwością sygnatura dostępu współdzielonego dla usługi z dołączoną sygnatury dostępu Współdzielonego. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* Akcja "Ustaw domyślna Warstwa dostępu" jest teraz dostępne dla obiektów Blob i magazynu GPV2 konta, które zostały przypięte do paska szybki dostęp. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* Czasami Eksploratora usługi Storage zakończy się niepowodzeniem pokazać klasycznych kont magazynu. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Znane problemy
* Przy użyciu emulatorów, takich jak Emulator usługi Azure Storage lub Azurite, należy je nasłuchiwać połączeń na ich domyślne porty. W przeciwnym razie Eksploratora usługi Storage nie będzie można połączyć się z nimi.
* Jeśli używasz programu VS dla komputerów Mac i nigdy nie zostały utworzone niestandardowej konfiguracji usługi AAD, możesz nie mieć możliwości logowania. Aby obejść ten problem, usuń zawartość ~ /. IdentityService/AadConfigurations. Jeśli tak się nie odblokowuje, należy dodać komentarz dotyczący [tego problemu](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite nie została jeszcze w pełni zaimplementowana wszystkie interfejsy API usługi Storage. W związku z tym może występować nieoczekiwanych błędów lub zachowanie w przypadku używania Azurite dla magazynem projektowym.
* W rzadkich przypadkach fokus drzewa może zakończyć się zatrzymaniem na szybki dostęp. Aby odklej fokus, można na nim Odśwież wszystko.
* Przekazywanie z folderu usługi OneDrive nie działa z powodu błędów w środowisku NodeJS. Błąd został rozwiązany, ale nie są jeszcze zintegrowane z elektronów.
* Przeznaczone dla usługi Azure Stack, przekazywanie pewne pliki jako uzupełnialnych obiektów blob może zakończyć się niepowodzeniem.
* Po kliknięciu przycisku "Anuluj" do zadania, może upłynąć trochę czasu tego zadania anulować. Dzieje się tak dlatego, że używamy filtru anulowania obejścia tego [problemu.](https://github.com/Azure/azure-storage-node/issues/317)
* Jeśli wybrano nieprawidłowy numer PIN/certyfikatu karty inteligentnej, należy uruchomić ponownie, aby mogła mieć Eksploratora usługi Storage zapomnij tej decyzji.
* Zmiana nazwy obiektów blob (pojedynczo lub w kontenerze obiektów blob zmieniono nazwę) nie zostaną zachowane migawki. Wszystkie właściwości i metadanych obiektów blob, plików oraz jednostki są zachowywane podczas zmiany nazwy.
* Mimo że usługę Azure Stack, obecnie nie obsługuje udziałów plików, udziały plików nadal pojawia się pod węzłem dołączone konto magazynu usługi Azure Stack.
* Powłoka elektronów wykorzystywane przez Eksploratora magazynu ma problemy z niektórych przyspieszania sprzętowego procesora GPU (jednostka przetwarzania grafiki). Jeśli Eksplorator usługi Storage wyświetla puste okno główne (puste), możesz spróbować uruchomić Eksplorator usługi Storage z wiersza polecenia i wyłączyć Przyspieszenie GPU, dodając przełącznik `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* W przypadku użytkowników systemu Linux należy zainstalować program [.NET Core 2,0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
* Dla użytkowników w systemie Ubuntu 14.04, konieczne będzie upewnij się, GCC jest aktualne — można to zrobić, uruchamiając następujące polecenia, a następnie ponownym uruchomieniu komputera:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Dla użytkowników w systemie Ubuntu 17.04 będą musieli zainstalować GConf — można to zrobić, uruchamiając następujące polecenia, a następnie ponownym uruchomieniu komputera:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-143"></a>Wersja 1.4.3
10/11/2018 r.

### <a name="hotfixes"></a>Poprawki
* Wersja interfejsu API usługi Azure Resource Management została wycofana w celu odblokowania użytkowników programu Azure USA dla instytucji rządowych. [#696](https://github.com/Microsoft/AzureStorageExplorer/issues/696)
* Trwa ładowanie pokręteł teraz używają animacji CSS skrócenie GPU wykorzystywane przez Eksploratora magazynu. [#653](https://github.com/Microsoft/AzureStorageExplorer/issues/653)

### <a name="new"></a>Nowa
* Załączniki zewnętrznego zasobu, takie jak w przypadku połączenia SAS i emulatorów, została znacznie ulepszona. Teraz możesz wykonywać następujące czynności:
   * Dostosuj nazwę wyświetlaną zasób, który jest podłączany. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Dołącz do wielu emulatorów lokalnego przy użyciu różnych portów. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Dodaj zasoby dołączone do paska szybki dostęp. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* Eksplorator usługi Storage obsługuje teraz usuwanie nietrwałe. Możesz:
   * Skonfiguruj zasady usuwanie nietrwałe, klikając prawym przyciskiem myszy w węźle kontenery obiektów Blob konta magazynu.
   * Widok nietrwale usunięte obiekty BLOB w Edytorze obiektów Blob, wybierając pozycję "Active i usunięte obiekty BLOB" w menu rozwijanym obok paska nawigacji.
   * Usuniętych nietrwale usunięte obiekty BLOB.

### <a name="fixes"></a>Poprawki
* Akcja "Konfiguruj ustawienia specyfikacji CORS" nie jest już dostępna dla kont usługi Premium Storage ponieważ kont usługi Premium Storage nie obsługują mechanizmu CORS. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Teraz jest właściwością sygnatura dostępu współdzielonego dla usługi z dołączoną sygnatury dostępu Współdzielonego. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* Akcja "Ustaw domyślna Warstwa dostępu" jest teraz dostępne dla obiektów Blob i magazynu GPV2 konta, które zostały przypięte do paska szybki dostęp. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* Czasami Eksploratora usługi Storage zakończy się niepowodzeniem pokazać klasycznych kont magazynu. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Znane problemy
* Przy użyciu emulatorów, takich jak Emulator usługi Azure Storage lub Azurite, należy je nasłuchiwać połączeń na ich domyślne porty. W przeciwnym razie Eksploratora usługi Storage nie będzie można połączyć się z nimi.
* Jeśli używasz programu VS dla komputerów Mac i nigdy nie zostały utworzone niestandardowej konfiguracji usługi AAD, możesz nie mieć możliwości logowania. Aby obejść ten problem, usuń zawartość ~ /. IdentityService/AadConfigurations. Jeśli tak się nie odblokowuje, należy dodać komentarz dotyczący [tego problemu](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite nie została jeszcze w pełni zaimplementowana wszystkie interfejsy API usługi Storage. W związku z tym może występować nieoczekiwanych błędów lub zachowanie w przypadku używania Azurite dla magazynem projektowym.
* W rzadkich przypadkach fokus drzewa może zakończyć się zatrzymaniem na szybki dostęp. Aby odklej fokus, można na nim Odśwież wszystko.
* Przekazywanie z folderu usługi OneDrive nie działa z powodu błędów w środowisku NodeJS. Błąd został rozwiązany, ale nie są jeszcze zintegrowane z elektronów.
* Przeznaczone dla usługi Azure Stack, przekazywanie pewne pliki jako uzupełnialnych obiektów blob może zakończyć się niepowodzeniem.
* Po kliknięciu przycisku "Anuluj" do zadania, może upłynąć trochę czasu tego zadania anulować. Dzieje się tak dlatego, że używamy filtru anulowania obejścia tego [problemu.](https://github.com/Azure/azure-storage-node/issues/317)
* Jeśli wybrano nieprawidłowy numer PIN/certyfikatu karty inteligentnej, należy uruchomić ponownie, aby mogła mieć Eksploratora usługi Storage zapomnij tej decyzji.
* Zmiana nazwy obiektów blob (pojedynczo lub w kontenerze obiektów blob zmieniono nazwę) nie zostaną zachowane migawki. Wszystkie właściwości i metadanych obiektów blob, plików oraz jednostki są zachowywane podczas zmiany nazwy.
* Mimo że usługę Azure Stack, obecnie nie obsługuje udziałów plików, udziały plików nadal pojawia się pod węzłem dołączone konto magazynu usługi Azure Stack.
* Powłoka elektronów wykorzystywane przez Eksploratora magazynu ma problemy z niektórych przyspieszania sprzętowego procesora GPU (jednostka przetwarzania grafiki). Jeśli Eksplorator usługi Storage wyświetla puste okno główne (puste), możesz spróbować uruchomić Eksplorator usługi Storage z wiersza polecenia i wyłączyć Przyspieszenie GPU, dodając przełącznik `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* W przypadku użytkowników systemu Linux należy zainstalować program [.NET Core 2,0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
* Dla użytkowników w systemie Ubuntu 14.04, konieczne będzie upewnij się, GCC jest aktualne — można to zrobić, uruchamiając następujące polecenia, a następnie ponownym uruchomieniu komputera:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Dla użytkowników w systemie Ubuntu 17.04 będą musieli zainstalować GConf — można to zrobić, uruchamiając następujące polecenia, a następnie ponownym uruchomieniu komputera:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-142"></a>W wersji 1.4.2
09/24/2018 r.

### <a name="hotfixes"></a>Poprawki
* Zaktualizuj wersję interfejsu API usługi Azure Resource Management do wersji 2018-07-01 w celu dodania obsługi nowych rodzajów kont usługi Azure Storage. [#652](https://github.com/Microsoft/AzureStorageExplorer/issues/652)

### <a name="new"></a>Nowa
* Załączniki zewnętrznego zasobu, takie jak w przypadku połączenia SAS i emulatorów, została znacznie ulepszona. Teraz możesz wykonywać następujące czynności:
   * Dostosuj nazwę wyświetlaną zasób, który jest podłączany. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Dołącz do wielu emulatorów lokalnego przy użyciu różnych portów. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Dodaj zasoby dołączone do paska szybki dostęp. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* Eksplorator usługi Storage obsługuje teraz usuwanie nietrwałe. Możesz:
   * Skonfiguruj zasady usuwanie nietrwałe, klikając prawym przyciskiem myszy w węźle kontenery obiektów Blob konta magazynu.
   * Widok nietrwale usunięte obiekty BLOB w Edytorze obiektów Blob, wybierając pozycję "Active i usunięte obiekty BLOB" w menu rozwijanym obok paska nawigacji.
   * Usuniętych nietrwale usunięte obiekty BLOB.

### <a name="fixes"></a>Poprawki
* Akcja "Konfiguruj ustawienia specyfikacji CORS" nie jest już dostępna dla kont usługi Premium Storage ponieważ kont usługi Premium Storage nie obsługują mechanizmu CORS. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Teraz jest właściwością sygnatura dostępu współdzielonego dla usługi z dołączoną sygnatury dostępu Współdzielonego. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* Akcja "Ustaw domyślna Warstwa dostępu" jest teraz dostępne dla obiektów Blob i magazynu GPV2 konta, które zostały przypięte do paska szybki dostęp. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* Czasami Eksploratora usługi Storage zakończy się niepowodzeniem pokazać klasycznych kont magazynu. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Znane problemy
* Przy użyciu emulatorów, takich jak Emulator usługi Azure Storage lub Azurite, należy je nasłuchiwać połączeń na ich domyślne porty. W przeciwnym razie Eksploratora usługi Storage nie będzie można połączyć się z nimi.
* Jeśli używasz programu VS dla komputerów Mac i nigdy nie zostały utworzone niestandardowej konfiguracji usługi AAD, możesz nie mieć możliwości logowania. Aby obejść ten problem, usuń zawartość ~ /. IdentityService/AadConfigurations. Jeśli tak się nie odblokowuje, należy dodać komentarz dotyczący [tego problemu](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite nie została jeszcze w pełni zaimplementowana wszystkie interfejsy API usługi Storage. W związku z tym może występować nieoczekiwanych błędów lub zachowanie w przypadku używania Azurite dla magazynem projektowym.
* W rzadkich przypadkach fokus drzewa może zakończyć się zatrzymaniem na szybki dostęp. Aby odklej fokus, można na nim Odśwież wszystko.
* Przekazywanie z folderu usługi OneDrive nie działa z powodu błędów w środowisku NodeJS. Błąd został rozwiązany, ale nie są jeszcze zintegrowane z elektronów.
* Przeznaczone dla usługi Azure Stack, przekazywanie pewne pliki jako uzupełnialnych obiektów blob może zakończyć się niepowodzeniem.
* Po kliknięciu przycisku "Anuluj" do zadania, może upłynąć trochę czasu tego zadania anulować. Dzieje się tak dlatego, że używamy filtru anulowania obejścia tego [problemu.](https://github.com/Azure/azure-storage-node/issues/317)
* Jeśli wybrano nieprawidłowy numer PIN/certyfikatu karty inteligentnej, należy uruchomić ponownie, aby mogła mieć Eksploratora usługi Storage zapomnij tej decyzji.
* Zmiana nazwy obiektów blob (pojedynczo lub w kontenerze obiektów blob zmieniono nazwę) nie zostaną zachowane migawki. Wszystkie właściwości i metadanych obiektów blob, plików oraz jednostki są zachowywane podczas zmiany nazwy.
* Mimo że usługę Azure Stack, obecnie nie obsługuje udziałów plików, udziały plików nadal pojawia się pod węzłem dołączone konto magazynu usługi Azure Stack.
* Powłoka elektronów wykorzystywane przez Eksploratora magazynu ma problemy z niektórych przyspieszania sprzętowego procesora GPU (jednostka przetwarzania grafiki). Jeśli Eksplorator usługi Storage wyświetla puste okno główne (puste), możesz spróbować uruchomić Eksplorator usługi Storage z wiersza polecenia i wyłączyć Przyspieszenie GPU, dodając przełącznik `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* W przypadku użytkowników systemu Linux należy zainstalować program [.NET Core 2,0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
* Dla użytkowników w systemie Ubuntu 14.04, konieczne będzie upewnij się, GCC jest aktualne — można to zrobić, uruchamiając następujące polecenia, a następnie ponownym uruchomieniu komputera:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Dla użytkowników w systemie Ubuntu 17.04 będą musieli zainstalować GConf — można to zrobić, uruchamiając następujące polecenia, a następnie ponownym uruchomieniu komputera:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-141"></a>Wersja 1.4.1
08/28/2018 r.

### <a name="hotfixes"></a>Poprawki
* Przy pierwszym uruchomieniu programu Storage Explorer nie mógł wygenerować klucz używany do szyfrowania poufnych danych. Może to spowodować problemy, korzystając z paska szybki dostęp i dołączanie zasobów. [#535](https://github.com/Microsoft/AzureStorageExplorer/issues/535)
* Jeśli Twoje konto nie wymagało uwierzytelniania Wieloskładnikowego dla swojej dzierżawy głównej, ale dla niektórych innych dzierżaw, Eksploratora usługi Storage nie będzie można subskrypcji listy. Teraz po zarejestrowaniu się przy użyciu tego konta, Eksploratora usługi Storage wyświetli monit o ponownie wprowadzić swoje poświadczenia i wykonać uwierzytelnianie wieloskładnikowe. [#74](https://github.com/Microsoft/AzureStorageExplorer/issues/74)
* Eksplorator usługi Storage nie może dołączyć zasoby z platformy Azure (Niemcy) i Azure instytucji rządowych USA. [#572](https://github.com/Microsoft/AzureStorageExplorer/issues/572)
* Jeśli zalogowano się do dwóch kont, które miały ten sam adres e-mail Eksploratora usługi Storage czasami zakończy się niepowodzeniem do wyświetlenia Twoich zasobów w widoku drzewa. [#580](https://github.com/Microsoft/AzureStorageExplorer/issues/580)
* Na wolniejszych Windows maszynach na ekranie powitalnym zajęłoby czasami znaczną ilość czasu. [#586](https://github.com/Microsoft/AzureStorageExplorer/issues/586)
* Czy pojawi się okno dialogowe Połącz, nawet wtedy, gdy było dołączone konta lub usługi. [#588](https://github.com/Microsoft/AzureStorageExplorer/issues/588)

### <a name="new"></a>Nowa
* Załączniki zewnętrznego zasobu, takie jak w przypadku połączenia SAS i emulatorów, została znacznie ulepszona. Teraz możesz wykonywać następujące czynności:
   * Dostosuj nazwę wyświetlaną zasób, który jest podłączany. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Dołącz do wielu emulatorów lokalnego przy użyciu różnych portów. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Dodaj zasoby dołączone do paska szybki dostęp. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* Eksplorator usługi Storage obsługuje teraz usuwanie nietrwałe. Możesz:
   * Skonfiguruj zasady usuwanie nietrwałe, klikając prawym przyciskiem myszy w węźle kontenery obiektów Blob konta magazynu.
   * Widok nietrwale usunięte obiekty BLOB w Edytorze obiektów Blob, wybierając pozycję "Active i usunięte obiekty BLOB" w menu rozwijanym obok paska nawigacji.
   * Usuniętych nietrwale usunięte obiekty BLOB.

### <a name="fixes"></a>Poprawki
* Akcja "Konfiguruj ustawienia specyfikacji CORS" nie jest już dostępna dla kont usługi Premium Storage ponieważ kont usługi Premium Storage nie obsługują mechanizmu CORS. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Teraz jest właściwością sygnatura dostępu współdzielonego dla usługi z dołączoną sygnatury dostępu Współdzielonego. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* Akcja "Ustaw domyślna Warstwa dostępu" jest teraz dostępne dla obiektów Blob i magazynu GPV2 konta, które zostały przypięte do paska szybki dostęp. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* Czasami Eksploratora usługi Storage zakończy się niepowodzeniem pokazać klasycznych kont magazynu. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Znane problemy
* Przy użyciu emulatorów, takich jak Emulator usługi Azure Storage lub Azurite, należy je nasłuchiwać połączeń na ich domyślne porty. W przeciwnym razie Eksploratora usługi Storage nie będzie można połączyć się z nimi.
* Jeśli używasz programu VS dla komputerów Mac i nigdy nie zostały utworzone niestandardowej konfiguracji usługi AAD, możesz nie mieć możliwości logowania. Aby obejść ten problem, usuń zawartość ~ /. IdentityService/AadConfigurations. Jeśli tak się nie odblokowuje, należy dodać komentarz dotyczący [tego problemu](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite nie została jeszcze w pełni zaimplementowana wszystkie interfejsy API usługi Storage. W związku z tym może występować nieoczekiwanych błędów lub zachowanie w przypadku używania Azurite dla magazynem projektowym.
* W rzadkich przypadkach fokus drzewa może zakończyć się zatrzymaniem na szybki dostęp. Aby odklej fokus, można na nim Odśwież wszystko.
* Przekazywanie z folderu usługi OneDrive nie działa z powodu błędów w środowisku NodeJS. Błąd został rozwiązany, ale nie są jeszcze zintegrowane z elektronów.
* Przeznaczone dla usługi Azure Stack, przekazywanie pewne pliki jako uzupełnialnych obiektów blob może zakończyć się niepowodzeniem.
* Po kliknięciu przycisku "Anuluj" do zadania, może upłynąć trochę czasu tego zadania anulować. Dzieje się tak dlatego, że używamy filtru anulowania obejścia tego [problemu.](https://github.com/Azure/azure-storage-node/issues/317)
* Jeśli wybrano nieprawidłowy numer PIN/certyfikatu karty inteligentnej, należy uruchomić ponownie, aby mogła mieć Eksploratora usługi Storage zapomnij tej decyzji.
* Zmiana nazwy obiektów blob (pojedynczo lub w kontenerze obiektów blob zmieniono nazwę) nie zostaną zachowane migawki. Wszystkie właściwości i metadanych obiektów blob, plików oraz jednostki są zachowywane podczas zmiany nazwy.
* Mimo że usługę Azure Stack, obecnie nie obsługuje udziałów plików, udziały plików nadal pojawia się pod węzłem dołączone konto magazynu usługi Azure Stack.
* Powłoka elektronów wykorzystywane przez Eksploratora magazynu ma problemy z niektórych przyspieszania sprzętowego procesora GPU (jednostka przetwarzania grafiki). Jeśli Eksplorator usługi Storage wyświetla puste okno główne (puste), możesz spróbować uruchomić Eksplorator usługi Storage z wiersza polecenia i wyłączyć Przyspieszenie GPU, dodając przełącznik `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* W przypadku użytkowników systemu Linux należy zainstalować program [.NET Core 2,0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
* Dla użytkowników w systemie Ubuntu 14.04, konieczne będzie upewnij się, GCC jest aktualne — można to zrobić, uruchamiając następujące polecenia, a następnie ponownym uruchomieniu komputera:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Dla użytkowników w systemie Ubuntu 17.04 będą musieli zainstalować GConf — można to zrobić, uruchamiając następujące polecenia, a następnie ponownym uruchomieniu komputera:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-130"></a>Wersja 1.3.0
07/09/2018 r.

### <a name="new"></a>Nowa
* Uzyskiwanie dostępu do kontenerów $web używane przez statycznej witryny sieci Web jest teraz obsługiwane. Dzięki temu można łatwo przekazywać i zarządzania plikami i folderami używane przez witryny sieci Web. [#223](https://github.com/Microsoft/AzureStorageExplorer/issues/223)
* Na pasku aplikacji w systemie macOS została zreorganizowana. Zmiany obejmują menu Plik, niektóre zmiany klucza skrótu i kilka nowych poleceń w menu aplikacji. [#99](https://github.com/Microsoft/AzureStorageExplorer/issues/99)
* Punkt końcowy urzędu rejestrowania w usłudze Azure USA dla instytucji rządowych Stanów Zjednoczonych został zmieniony na https://login.microsoftonline.us/
* Ułatwienia dostępu: Włączeniu czytnika ekranu, nawigacji za pomocą klawiatury teraz współpracuje z tabel, służący do wyświetlania elementów po prawej stronie. Za pomocą klawiszy strzałek do nawigacji wierszy i kolumn, Enter, aby wywołać akcje domyślne klawisz menu kontekst, otwórz menu kontekstowe dla elementu i Shift lub kontrolować wybór wielokrotny. [#103](https://github.com/Microsoft/AzureStorageExplorer/issues/103)

### <a name="fixes"></a>Poprawki
*  Na niektórych komputerach procesy podrzędne zostały zajmuje dużo czasu do uruchomienia. Jeśli ta sytuacja może mieć miejsce, pojawią się błędem "proces podrzędny nie można uruchomić w odpowiednim czasie". Teraz został zwiększony czas przydzielony na wykonanie procesu podrzędnego, można uruchomić z 20 do 90 sekund. Jeśli są nadal objęte tym problemem, Skomentuj na połączonych problem w usłudze GitHub. [#281](https://github.com/Microsoft/AzureStorageExplorer/issues/281)
* Przy użyciu sygnatury dostępu Współdzielonego, który nie ma uprawnienia do odczytu, nie było możliwe przekazywanie dużych obiektów blob. Logiki w celu przekazania została zmodyfikowana, aby pracować w tym scenariuszu. [#305](https://github.com/Microsoft/AzureStorageExplorer/issues/305)
* Ustawienie poziom dostępu publicznego do kontenera spowoduje usunięcie wszystkich zasad dostępu i na odwrót. Teraz zasady i dostępu publicznego dostępu zostaną zachowane, ustawiając jedną z dwóch. [#197](https://github.com/Microsoft/AzureStorageExplorer/issues/197)
* "AccessTierChangeTime" został obcięty w oknie dialogowym właściwości. Ten problem został rozwiązany. [#145](https://github.com/Microsoft/AzureStorageExplorer/issues/145)
* "Microsoft Azure Storage Explorer —" prefiks brakowało okna dialogowego Tworzenie nowego katalogu. Ten problem został rozwiązany. [#299](https://github.com/Microsoft/AzureStorageExplorer/issues/299)
* Ułatwienia dostępu: Okno dialogowe Dodawanie jednostki trudno było je przejść, korzystając z VoiceOver. Wprowadzono ulepszenia. [#206](https://github.com/Microsoft/AzureStorageExplorer/issues/206)
* Ułatwienia dostępu: Kolor tła przycisk Zwiń/Rozwiń okienko akcji i właściwości była niespójna przy użyciu podobnych kontrolek interfejsu użytkownika w kompozycji wysoki kontrast — kolor czarny. Kolor został zmieniony. [#123](https://github.com/Microsoft/AzureStorageExplorer/issues/123)
* Ułatwienia dostępu: Motyw wysoki kontrast — kolor czarny fokus, ustawianie stylów dla przycisku "X" w oknie dialogowym właściwości nie były widoczne. Ten problem został rozwiązany. [#243](https://github.com/Microsoft/AzureStorageExplorer/issues/243)
* Ułatwienia dostępu: Karty akce a Vlastnosti brakuje kilku wartości aria, które zakończyły się środowisko czytnika ekranu niepoprawne. Brak wartości aria został dodany. [#316](https://github.com/Microsoft/AzureStorageExplorer/issues/316)
* Ułatwienia dostępu: Węzłów zwinięty drzewa po lewej stronie zostały nie zostało dostarczone rozwinięte aria musi mieć wartość false. Ten problem został rozwiązany. [#352](https://github.com/Microsoft/AzureStorageExplorer/issues/352)

### <a name="known-issues"></a>Znane problemy
* Odłączanie od zasobu dołączonego za pomocą identyfikatora URI SAS, takiego jak kontener obiektów blob, może spowodować błąd, który uniemożliwia poprawne wyświetlanie innych załączników. Aby obejść ten problem, wystarczy odświeżyć węzeł grupy. Aby uzyskać więcej informacji, zobacz [ten problem](https://github.com/Microsoft/AzureStorageExplorer/issues/537) .
* Jeśli używasz programu VS dla komputerów Mac i nigdy nie zostały utworzone niestandardowej konfiguracji usługi AAD, możesz nie mieć możliwości logowania. Aby obejść ten problem, usuń zawartość ~ /. IdentityService/AadConfigurations. Jeśli tak się nie odblokowuje, należy dodać komentarz dotyczący [tego problemu](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite nie została jeszcze w pełni zaimplementowana wszystkie interfejsy API usługi Storage. W związku z tym może występować nieoczekiwanych błędów lub zachowanie w przypadku używania Azurite dla magazynem projektowym.
* W rzadkich przypadkach fokus drzewa może zakończyć się zatrzymaniem na szybki dostęp. Aby odklej fokus, można na nim Odśwież wszystko.
* Przekazywanie z folderu usługi OneDrive nie działa z powodu błędów w środowisku NodeJS. Błąd został rozwiązany, ale nie są jeszcze zintegrowane z elektronów.
* Przeznaczone dla usługi Azure Stack, przekazywanie pewne pliki jako uzupełnialnych obiektów blob może zakończyć się niepowodzeniem.
* Po kliknięciu przycisku "Anuluj" do zadania, może upłynąć trochę czasu tego zadania anulować. Dzieje się tak dlatego, że używamy filtru anulowania obejścia tego [problemu.](https://github.com/Azure/azure-storage-node/issues/317)
* Jeśli wybrano nieprawidłowy numer PIN/certyfikatu karty inteligentnej, należy uruchomić ponownie, aby mogła mieć Eksploratora usługi Storage zapomnij tej decyzji.
* Zmiana nazwy obiektów blob (pojedynczo lub w kontenerze obiektów blob zmieniono nazwę) nie zostaną zachowane migawki. Wszystkie właściwości i metadanych obiektów blob, plików oraz jednostki są zachowywane podczas zmiany nazwy.
* Usługa Azure Stack nie obsługuje następujące funkcje i próby ich wykorzystania podczas pracy z usługą Azure Stack może spowodować nieoczekiwane błędy:
   * Udziały plików
   * Poziomy dostępu
   * Usuwanie nietrwałe
* Powłoka elektronów wykorzystywane przez Eksploratora magazynu ma problemy z niektórych przyspieszania sprzętowego procesora GPU (jednostka przetwarzania grafiki). Jeśli Eksplorator usługi Storage wyświetla puste okno główne (puste), możesz spróbować uruchomić Eksplorator usługi Storage z wiersza polecenia i wyłączyć Przyspieszenie GPU, dodając przełącznik `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* W przypadku użytkowników systemu Linux należy zainstalować program [.NET Core 2,0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
* Dla użytkowników w systemie Ubuntu 14.04, konieczne będzie upewnij się, GCC jest aktualne — można to zrobić, uruchamiając następujące polecenia, a następnie ponownym uruchomieniu komputera:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Dla użytkowników w systemie Ubuntu 17.04 będą musieli zainstalować GConf — można to zrobić, uruchamiając następujące polecenia, a następnie ponownym uruchomieniu komputera:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-120"></a>Wersji 1.2.0 lub nowszej
06/12/2018 r.

### <a name="new"></a>Nowa
* Jeśli Eksploratora usługi Storage nie można załadować subskrypcji z podzbiorem dzierżawcy, następnie wszystkich pomyślnie załadowany w subskrypcji będą wyświetlane wraz z komunikatu o błędzie specjalnie dla dzierżaw, które nie powiodło się. [#159](https://github.com/Microsoft/AzureStorageExplorer/issues/159)
* W Windows gdy aktualizacja jest dostępna, możesz teraz możliwość "Aktualizuj przy zamknięciu". Gdy ta opcja jest pobierana, Instalator aktualizacji zostanie uruchomiony po zamknięciu programu Storage Explorer. [#21](https://github.com/Microsoft/AzureStorageExplorer/issues/21)
* Migawka przywracania została dodana do menu kontekstowego edytora udziału plików podczas wyświetlania migawki udziału plików. [#131](https://github.com/Microsoft/AzureStorageExplorer/issues/131)
* Przycisk czyszczenia kolejki jest teraz zawsze włączony. [#135](https://github.com/Microsoft/AzureStorageExplorer/issues/135)
* Obsługa logowania do usług AD FS usługi Azure Stack została ponownie włączona. Usługa Azure Stack w wersji 1804 lub nowszej jest wymagana. [#150](https://github.com/Microsoft/AzureStorageExplorer/issues/150)

### <a name="fixes"></a>Poprawki
* Jeśli migawki dla udziału plików, którego nazwa została prefiksu innego udziału plików na tym samym koncie magazynu jest wyświetlane, następnie migawki dla udziału plików będą również wyświetlane. Ten problem został rozwiązany. [#255](https://github.com/Microsoft/AzureStorageExplorer/issues/255)
* Gdy dołączone, za pomocą sygnatury dostępu Współdzielonego, przywracanie pliku z migawki udziału plików spowoduje błąd. Ten problem został rozwiązany. [#211](https://github.com/Microsoft/AzureStorageExplorer/issues/211)
* Podczas przeglądania migawek obiektu blob, akcji podwyższanie poziomu migawki została włączona, gdy wybrano podstawowy obiekt blob i pojedynczej migawki. Akcja teraz jest włączony tylko jeśli wybrano opcję pojedynczej migawki. [#230](https://github.com/Microsoft/AzureStorageExplorer/issues/230)
* Jeśli pojedynczego zadania (takie jak pobieranie obiektu blob) zostało uruchomione i nowszym nie powiodło się, go czy nie automatycznego ponownego do momentu rozpoczęcia inne zadanie tego samego typu. Wszystkie zadania powinien teraz automatycznie ponownych prób, niezależnie od tego, jak wiele zadań można ustawić w kolejce.
* Edytory otwarty nowo kontenery obiektów blob utworzonych w konta GPV2 i kont usługi Blob Storage nie ma kolumny warstwy dostępu. Ten problem został rozwiązany. [#109](https://github.com/Microsoft/AzureStorageExplorer/issues/109)
* Kolumny warstwy dostępu może czasami są wyświetlane, gdy konto magazynu lub kontenera obiektów blob został dołączony przy użyciu sygnatury dostępu Współdzielonego. Kolumna będzie zawsze wyświetlana, ale z pustą wartość, jeśli nie ustawiono warstwy dostępu. [#160](https://github.com/Microsoft/AzureStorageExplorer/issues/160)
* Ustawienie warstwy dostępu do nowo przesłanym blokowy obiekt blob został wyłączony. Ten problem został rozwiązany. [#171](https://github.com/Microsoft/AzureStorageExplorer/issues/171)
* Jeśli przycisk "Zachowaj kartę Otwórz" została wywołana przy użyciu klawiatury, fokus klawiatury może zostać utracone. Teraz fokus zostanie przesunięty na kartę która została zachowana open. [#163](https://github.com/Microsoft/AzureStorageExplorer/issues/163)
* Zapytania w Konstruktorze zapytań VoiceOver nie był zawierający opis użyteczne bieżący operator. Jest teraz bardziej opisowe. [#207](https://github.com/Microsoft/AzureStorageExplorer/issues/207)
* Linki dzielenia na strony dla różnych edytorów nie jest opisowy. Zostały zmienione za bardziej opisowe. [#205](https://github.com/Microsoft/AzureStorageExplorer/issues/205)
* W oknie dialogowym Dodawanie jednostki VoiceOver został nie informuje o jakie kolumny elementu input było częścią. Nazwa bieżącej kolumny teraz znajduje się w opisie elementu. [#206](https://github.com/Microsoft/AzureStorageExplorer/issues/206)
* Przyciski radiowe i pola wyboru nie miał widoczne obramowanie z fokusem. Ten problem został rozwiązany. [#237](https://github.com/Microsoft/AzureStorageExplorer/issues/237)

### <a name="known-issues"></a>Znane problemy
* Przy użyciu emulatorów, takich jak Emulator usługi Azure Storage lub Azurite, należy je nasłuchiwać połączeń na ich domyślne porty. W przeciwnym razie Eksploratora usługi Storage nie będzie można połączyć się z nimi.
* Jeśli używasz programu VS dla komputerów Mac i nigdy nie zostały utworzone niestandardowej konfiguracji usługi AAD, możesz nie mieć możliwości logowania. Aby obejść ten problem, usuń zawartość ~ /. IdentityService/AadConfigurations. Jeśli tak się nie odblokowuje, należy dodać komentarz dotyczący [tego problemu](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite nie została jeszcze w pełni zaimplementowana wszystkie interfejsy API usługi Storage. W związku z tym może występować nieoczekiwanych błędów lub zachowanie w przypadku używania Azurite dla magazynem projektowym.
* W rzadkich przypadkach fokus drzewa może zakończyć się zatrzymaniem na szybki dostęp. Aby odklej fokus, można na nim Odśwież wszystko.
* Przekazywanie z folderu usługi OneDrive nie działa z powodu błędów w środowisku NodeJS. Błąd został rozwiązany, ale nie są jeszcze zintegrowane z elektronów.
* Przeznaczone dla usługi Azure Stack, przekazywanie pewne pliki jako uzupełnialnych obiektów blob może zakończyć się niepowodzeniem.
* Po kliknięciu przycisku "Anuluj" do zadania, może upłynąć trochę czasu tego zadania anulować. Dzieje się tak dlatego, że używamy filtru anulowania obejścia tego [problemu.](https://github.com/Azure/azure-storage-node/issues/317)
* Jeśli wybrano nieprawidłowy numer PIN/certyfikatu karty inteligentnej, należy uruchomić ponownie, aby mogła mieć Eksploratora usługi Storage zapomnij tej decyzji.
* Zmiana nazwy obiektów blob (pojedynczo lub w kontenerze obiektów blob zmieniono nazwę) nie zostaną zachowane migawki. Wszystkie właściwości i metadanych obiektów blob, plików oraz jednostki są zachowywane podczas zmiany nazwy.
* Mimo że usługę Azure Stack, obecnie nie obsługuje udziałów plików, udziały plików nadal pojawia się pod węzłem dołączone konto magazynu usługi Azure Stack.
* Powłoka elektronów wykorzystywane przez Eksploratora magazynu ma problemy z niektórych przyspieszania sprzętowego procesora GPU (jednostka przetwarzania grafiki). Jeśli Eksplorator usługi Storage wyświetla puste okno główne (puste), możesz spróbować uruchomić Eksplorator usługi Storage z wiersza polecenia i wyłączyć Przyspieszenie GPU, dodając przełącznik `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* W przypadku użytkowników systemu Linux należy zainstalować program [.NET Core 2,0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
* Dla użytkowników w systemie Ubuntu 14.04, konieczne będzie upewnij się, GCC jest aktualne — można to zrobić, uruchamiając następujące polecenia, a następnie ponownym uruchomieniu komputera:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Dla użytkowników w systemie Ubuntu 17.04 będą musieli zainstalować GConf — można to zrobić, uruchamiając następujące polecenia, a następnie ponownym uruchomieniu komputera:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-110"></a>Wersji 1.1.0
05/09/2018 r.

### <a name="new"></a>Nowa
* Eksplorator usługi Storage obsługuje teraz użycie Azurite. Uwaga: połączenie Azurite jest zakodowana w domyślnych punktów końcowych rozwoju.
* Eksplorator usługi Storage obsługuje teraz warstw dostępu tylko do obiektów Blob i konta magazynu GPV2. Więcej informacji na temat warstw dostępu [znajdziesz tutaj](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers).
* Czas rozpoczęcia nie jest już wymagany podczas generowania sygnatury dostępu Współdzielonego.

### <a name="fixes"></a>Poprawki
* Podczas pobierania subskrypcji dla instytucji rządowych USA kont zostało przerwane. Ten problem został rozwiązany. [#61](https://github.com/Microsoft/AzureStorageExplorer/issues/61)
* Poprawnie czas wygaśnięcia zasady dostępu nie został on zapisany. Ten problem został rozwiązany. [#50](https://github.com/Microsoft/AzureStorageExplorer/issues/50)
* Podczas generowania adresu URL sygnatury dostępu Współdzielonego dla elementu w kontenerze, nazwa elementu została nie są dołączane do adresu URL. Ten problem został rozwiązany. [#44](https://github.com/Microsoft/AzureStorageExplorer/issues/44)
* Podczas tworzenia sygnatury dostępu Współdzielonego, czas wygaśnięcia, znajdujących się w przeszłości czasami będzie wartością domyślną. Jest to spowodowane Eksploratora usługi Storage przy użyciu ostatnio używany czas rozpoczęcia i utraty ważności jako wartości domyślne. Teraz za każdym razem, gdy możesz otworzyć okno dialogowe sygnatury dostępu Współdzielonego, jest generowany nowy zestaw wartości domyślnych. [#35](https://github.com/Microsoft/AzureStorageExplorer/issues/35)
* Podczas kopiowania między kontami magazynu, 24-godzinny sygnatury dostępu Współdzielonego jest generowany. Jeśli kopia trwała dłużej niż 24 godziny, kopia może zakończyć się niepowodzeniem. Zwiększyliśmy sygnatury dostępu Współdzielonego w celu ostatni 1 tydzień Aby zmniejszyć prawdopodobieństwo kopię kończy się niepowodzeniem z powodu wygasły sygnatury dostępu Współdzielonego. [#62](https://github.com/Microsoft/AzureStorageExplorer/issues/62)
* W przypadku niektórych działań kliknij "Anuluj", nie będzie zawsze działać. Ten problem został rozwiązany. [#125](https://github.com/Microsoft/AzureStorageExplorer/issues/125)
* W przypadku niektórych działań szybkość transferu była nieprawidłowa. Ten problem został rozwiązany. [#124](https://github.com/Microsoft/AzureStorageExplorer/issues/124)
* Pisownię wyrazu "Wstecz", w menu Widok była nieprawidłowa. Teraz została poprawnie wpisana. [#71](https://github.com/Microsoft/AzureStorageExplorer/issues/71)
* Na ostatniej stronie Instalatora Windows ma przycisk "Dalej". Został on zmieniony na przycisku "Zakończ". [#70](https://github.com/Microsoft/AzureStorageExplorer/issues/70)
* Fokus na klawiszu TAB nie był widoczny dla przycisków w oknach dialogowych podczas korzystania z motywu czarny połączenia Hybrydowego. Jest ona teraz widoczna. [#64](https://github.com/Microsoft/AzureStorageExplorer/issues/64)
* Wielkość liter w wyrazie "Auto-Rozwiąż" dla akcji w dzienniku aktywności była nieprawidłowa. Teraz jest poprawna. [#51](https://github.com/Microsoft/AzureStorageExplorer/issues/51)
* Podczas usuwania jednostki z tabeli, okno dialogowe z prośbą o potwierdzenie wyświetlana ikona błędu. Okno dialogowe używa teraz ikonę ostrzeżenia. [#148](https://github.com/Microsoft/AzureStorageExplorer/issues/148)

### <a name="known-issues"></a>Znane problemy
* Jeśli używasz programu VS dla komputerów Mac i nigdy nie zostały utworzone niestandardowej konfiguracji usługi AAD, możesz nie mieć możliwości logowania. Aby obejść ten problem, usuń zawartość ~ /. IdentityService/AadConfigurations. Jeśli tak się nie odblokowuje, należy dodać komentarz dotyczący [tego problemu](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite nie została jeszcze w pełni zaimplementowana wszystkie interfejsy API usługi Storage. W związku z tym może występować nieoczekiwanych błędów lub zachowanie w przypadku używania Azurite dla magazynem projektowym.
* W rzadkich przypadkach fokus drzewa może zakończyć się zatrzymaniem na szybki dostęp. Aby odklej fokus, można na nim Odśwież wszystko.
* Przekazywanie z folderu usługi OneDrive nie działa z powodu błędów w środowisku NodeJS. Błąd został rozwiązany, ale nie są jeszcze zintegrowane z elektronów.
* Przeznaczone dla usługi Azure Stack, przekazywanie pewne pliki jako uzupełnialnych obiektów blob może zakończyć się niepowodzeniem.
* Po kliknięciu przycisku "Anuluj" do zadania, może upłynąć trochę czasu tego zadania anulować. Dzieje się tak dlatego, że używamy filtru anulowania obejścia tego [problemu.](https://github.com/Azure/azure-storage-node/issues/317)
* Jeśli wybrano nieprawidłowy numer PIN/certyfikatu karty inteligentnej, należy uruchomić ponownie, aby mogła mieć Eksploratora usługi Storage zapomnij tej decyzji.
* Zmiana nazwy obiektów blob (pojedynczo lub w kontenerze obiektów blob zmieniono nazwę) nie zostaną zachowane migawki. Wszystkie właściwości i metadanych obiektów blob, plików oraz jednostki są zachowywane podczas zmiany nazwy.
* Mimo że usługę Azure Stack, obecnie nie obsługuje udziałów plików, udziały plików nadal pojawia się pod węzłem dołączone konto magazynu usługi Azure Stack.
* Powłoka elektronów wykorzystywane przez Eksploratora magazynu ma problemy z niektórych przyspieszania sprzętowego procesora GPU (jednostka przetwarzania grafiki). Jeśli Eksplorator usługi Storage wyświetla puste okno główne (puste), możesz spróbować uruchomić Eksplorator usługi Storage z wiersza polecenia i wyłączyć Przyspieszenie GPU, dodając przełącznik `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* W przypadku użytkowników systemu Linux należy zainstalować program [.NET Core 2,0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
* Dla użytkowników w systemie Ubuntu 14.04, konieczne będzie upewnij się, GCC jest aktualne — można to zrobić, uruchamiając następujące polecenia, a następnie ponownym uruchomieniu komputera:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Dla użytkowników w systemie Ubuntu 17.04 będą musieli zainstalować GConf — można to zrobić, uruchamiając następujące polecenia, a następnie ponownym uruchomieniu komputera:

    ```
    sudo apt-get install libgconf-2-4
    ```


## <a name="version-100"></a>Wersja 1.0.0
16/04/2018

### <a name="new"></a>Nowa
* Rozszerzone uwierzytelnianie umożliwia Eksploratora usługi Storage do użycia tego samego konta magazynu jako Visual Studio 2017. Aby użyć tej funkcji, należy ponownie zaloguj się do kont i ponownie ustaw filtrowane subskrypcji.
* W przypadku kont usługi Azure Stack wspierana przez usługi AAD Eksploratora usługi Storage, pobiera subskrypcji usługi Azure Stack po włączeniu docelowej usługi Azure Stack. Nie jest już konieczne Utwórz środowisko niestandardowe nazwy logowania.
* Dodano kilka skróty umożliwiają szybsze nawigowanie. Obejmują one przełączanie różnych zespołów i przechodzenia między edytorów. Wyświetlić menu Widok, aby uzyskać więcej informacji.
* Opinie Eksploratora magazynu znajduje się teraz w witrynie GitHub. Możesz skontaktować się z naszą stroną problemów, klikając przycisk opinii w lewym dolnym rogu lub przechodząc do [https://github.com/Microsoft/AzureStorageExplorer/issues](https://github.com/Microsoft/AzureStorageExplorer/issues). Swobodnie sugestie, zgłaszania problemów, zadawać pytania lub pozostaw żaden inny rodzaj opinii.
* Jeśli używasz programu do problemów z certyfikatem SSL i nie można znaleźć poprawnego certyfikatu, możesz teraz uruchomić Eksplorator usługi Storage z wiersza polecenia z flagą `--ignore-certificate-errors`. Gdy uruchomiony przy użyciu tej flagi, Eksploratora usługi Storage będzie ignorować błędy certyfikatu SSL.
* Jest teraz opcję "Pobierz" w menu kontekstowym dla obiektów blob i plików elementów.
* Ulepszone ułatwienia dostępu i obsługę czytników zawartości ekranu. Jeśli korzystasz z funkcji ułatwień dostępu, zapoznaj się z [dokumentacją ułatwień dostępu](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-accessibility) , aby uzyskać więcej informacji.
* Eksplorator usługi Storage korzysta z elektronów 1.8.3

### <a name="breaking-changes"></a>Zmiany powodujące niezgodność
* Eksplorator usługi Storage zostało przełączone nową bibliotekę uwierzytelniania. W ramach przełącznika do biblioteki trzeba będzie ponownie zaloguj się do kont i ponownie ustaw filtrowane subskrypcji
* Metoda używana do szyfrowania poufnych danych został zmieniony. Może to spowodować niektórych elementów paska szybki dostęp bez potrzeby ponownego dodania i/lub niektórzy z was dołączone zasoby wymagające ponownie dołączyć.

### <a name="fixes"></a>Poprawki
* Niektórzy użytkownicy się za serwery proxy miałby przekazywaniu obiektów blob do grupy lub Pobieranie przerwane przez "Nie można rozpoznać" komunikat o błędzie. Ten problem został rozwiązany.
* W razie logowania zostało podczas przy użyciu linku bezpośredniego, klikając polecenie w wierszu polecenia "Sign-In" będzie wyskakujące puste okno dialogowe. Ten problem został rozwiązany.
* W systemie Linux, jeśli Eksplorator usługi Storage to nie można uruchomić z powodu awarii procesu procesora GPU, użytkownik będzie teraz informowany o awarii, informację, aby użyć "--procesora gpu disable" przełącznika i Eksploratora usługi Storage będą następnie automatycznie ponownie uruchom za pomocą przełącznika włączone.
* Nieprawidłowy dostęp zasady były trudne do tożsamości w oknie dialogowym zasad dostępu. Zasady dostępu nieprawidłowe identyfikatory podane są teraz w kolorze czerwonym, aby uzyskać lepszą widoczność.
* Dziennik aktywności, czasami musi duże obszary odstępów między poszczególnymi częściami działania. Ten problem został rozwiązany.
* W edytorze zapytań tabeli jeśli left klauzulę timestamp w nieprawidłowym stanie, a następnie podjęto próbę modyfikacji inną klauzulę zablokować edytora. Edytor przywróci klauzuli timestamp ostatni stan prawidłowy po wykryciu zmiany w klauzuli innego.
* Jeśli zostanie wstrzymana podczas wpisywania w zapytaniu wyszukiwania w widoku drzewa, wyszukiwanie będzie rozpocznie się i fokus, czy skradziony z pola tekstowego. Teraz należy jawnie rozpocząć wyszukiwanie, naciskając klawisz "Enter" lub klikając na przycisk start wyszukiwania.
* Polecenie "Uzyskaj sygnaturę dostępu współdzielonego" czasami zostałoby wyłączone, kliknięcie prawym przyciskiem myszy plik w udziale plików. Ten problem został rozwiązany.
* Jeśli węzeł drzewa zasób mający fokus został odfiltrowany podczas wyszukiwania, może nie kartę w drzewie zasobów i użyj klawiszy strzałek, aby nawigować drzewo zasobów. Teraz Jeśli węzeł drzewa wąsko zdefiniowany zasób jest ukryty, pierwszy węzeł w drzewie zasobów automatycznie skoncentruje się.
* To dodatkowe separator czasami będzie widoczny na pasku narzędzi edytora. Ten problem został rozwiązany.
* Pole tekstowe łączy do stron nadrzędnych czasami spowodowałoby przepełnienie. Ten problem został rozwiązany.
* Edytory obiektów Blob i udział plików będzie czasami stale Odśwież podczas przekazywania wielu plików jednocześnie. Ten problem został rozwiązany.
* Funkcja "Folder statystyki" miał bezcelowe w widoku Zarządzanie migawki udziału plików. Teraz została wyłączona.
* W systemie Linux w menu Plik nie były wyświetlane. Ten problem został rozwiązany.
* Podczas przekazywania folderu w udziale plików, domyślnie, tylko zawartość folderu zostały przekazane. Teraz zachowanie domyślne jest przekazywanie zawartości folderu do dopasowania folderu w udziale plików.
* Kolejność przycisków w oknach dialogowych kilka miał została wycofana. Ten problem został rozwiązany.
* Różne zabezpieczeń związane z poprawkami.

### <a name="known-issues"></a>Znane problemy
* W rzadkich przypadkach fokus drzewa może zakończyć się zatrzymaniem na szybki dostęp. Aby odklej fokus, można na nim Odśwież wszystko.
* Przeznaczone dla usługi Azure Stack, przekazywanie pewne pliki jako uzupełnialnych obiektów blob może zakończyć się niepowodzeniem.
* Po kliknięciu przycisku "Anuluj" do zadania, może upłynąć trochę czasu tego zadania anulować. Dzieje się tak dlatego, że używamy filtru anulowania obejścia tego problemu.
* Jeśli wybrano nieprawidłowy numer PIN/certyfikatu karty inteligentnej, należy uruchomić ponownie, aby mogła mieć Eksploratora usługi Storage zapomnij tej decyzji.
* Zmiana nazwy obiektów blob (pojedynczo lub w kontenerze obiektów blob zmieniono nazwę) nie zostaną zachowane migawki. Wszystkie właściwości i metadanych obiektów blob, plików oraz jednostki są zachowywane podczas zmiany nazwy.
* Mimo że usługę Azure Stack, obecnie nie obsługuje udziałów plików, udziały plików nadal pojawia się pod węzłem dołączone konto magazynu usługi Azure Stack.
* Powłoka elektronów wykorzystywane przez Eksploratora magazynu ma problemy z niektórych przyspieszania sprzętowego procesora GPU (jednostka przetwarzania grafiki). Jeśli Eksplorator usługi Storage wyświetla puste okno główne (puste), możesz spróbować uruchomić Eksplorator usługi Storage z wiersza polecenia i wyłączyć Przyspieszenie GPU, dodając przełącznik `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* W przypadku użytkowników systemu Linux należy zainstalować program [.NET Core 2,0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
* Dla użytkowników w systemie Ubuntu 14.04, konieczne będzie upewnij się, GCC jest aktualne — można to zrobić, uruchamiając następujące polecenia, a następnie ponownym uruchomieniu komputera:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Dla użytkowników w systemie Ubuntu 17.04 będą musieli zainstalować GConf — można to zrobić, uruchamiając następujące polecenia, a następnie ponownym uruchomieniu komputera:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-096"></a>Wersja 0.9.6
2018-02/28

### <a name="fixes"></a>Poprawki
* Wystąpił problem nie oczekiwano obiektów blob i pliki w edytorze. Ten problem został rozwiązany.
* Problem spowodował, że przełączanie widoków migawki niepoprawne wyświetlanie elementów. Ten problem został rozwiązany.

### <a name="known-issues"></a>Znane problemy
* Eksplorator usługi Storage nie obsługują kont usług AD FS.
* Przeznaczone dla usługi Azure Stack, przekazywanie pewne pliki jako uzupełnialnych obiektów blob może zakończyć się niepowodzeniem.
* Po kliknięciu przycisku "Anuluj" do zadania, może upłynąć trochę czasu tego zadania anulować. Dzieje się tak dlatego, że używamy filtru anulowania obejścia tego [problemu.](https://github.com/Azure/azure-storage-node/issues/317)
* Jeśli wybrano nieprawidłowy numer PIN/certyfikatu karty inteligentnej, należy uruchomić ponownie, aby mogła mieć Eksploratora usługi Storage zapomnij tej decyzji.
* Na panelu ustawień konta mogą być wyświetlane, należy ponownie wprowadzić poświadczenia, aby filtrowanie subskrypcji.
* Zmiana nazwy obiektów blob (pojedynczo lub w kontenerze obiektów blob zmieniono nazwę) nie zostaną zachowane migawki. Wszystkie właściwości i metadanych obiektów blob, plików oraz jednostki są zachowywane podczas zmiany nazwy.
* Mimo że usługę Azure Stack, obecnie nie obsługuje udziałów plików, udziały plików nadal pojawia się pod węzłem dołączone konto magazynu usługi Azure Stack.
* Powłoka elektronów wykorzystywane przez Eksploratora magazynu ma problemy z niektórych przyspieszania sprzętowego procesora GPU (jednostka przetwarzania grafiki). Jeśli Eksplorator usługi Storage wyświetla puste okno główne (puste), możesz spróbować uruchomić Eksplorator usługi Storage z wiersza polecenia i wyłączyć Przyspieszenie GPU, dodając przełącznik `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Dla użytkowników w systemie Ubuntu 14.04, konieczne będzie upewnij się, GCC jest aktualne — można to zrobić, uruchamiając następujące polecenia, a następnie ponownym uruchomieniu komputera:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Dla użytkowników w systemie Ubuntu 17.04 będą musieli zainstalować GConf — można to zrobić, uruchamiając następujące polecenia, a następnie ponownym uruchomieniu komputera:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-095"></a>Wersja 0.9.5
02 06 2018

### <a name="new"></a>Nowa

* Pomoc techniczna dla migawek udziałów plików:
    * Tworzenie i zarządzanie migawek dla udziałów plików.
    * Łatwo przełączać widoki między migawek udziałów plików, gdy eksplorujesz.
    * Przywróć poprzednie wersje plików.
* Obsługa usługi Azure Data Lake Store (wersja zapoznawcza):
    * Połączenie z zasobami usługi ADLS na wielu kontach.
    * Nawiązywanie połączenia i udostępnianie zasobów usługi ADLS za pomocą identyfikatorów URI usługi ADL.
    * Wykonywanie cyklicznie operacji podstawowego pliku/folderu.
    * Przypnij poszczególnych folderów do paska szybki dostęp.
    * Wyświetlanie statystyk folderów.

### <a name="fixes"></a>Poprawki
* Zwiększona wydajność uruchamiania.
* Różne poprawki.

### <a name="known-issues"></a>Znane problemy
* Eksplorator usługi Storage nie obsługują kont usług AD FS.
* Przeznaczone dla usługi Azure Stack, przekazywanie pewne pliki jako uzupełnialnych obiektów blob może zakończyć się niepowodzeniem.
* Po kliknięciu przycisku "Anuluj" do zadania, może upłynąć trochę czasu tego zadania anulować. Dzieje się tak dlatego, że używamy filtru anulowania obejścia tego problemu.
* Jeśli wybrano nieprawidłowy numer PIN/certyfikatu karty inteligentnej, należy uruchomić ponownie, aby mogła mieć Eksploratora usługi Storage zapomnij tej decyzji.
* Na panelu ustawień konta mogą być wyświetlane, należy ponownie wprowadzić poświadczenia, aby filtrowanie subskrypcji.
* Zmiana nazwy obiektów blob (pojedynczo lub w kontenerze obiektów blob zmieniono nazwę) nie zostaną zachowane migawki. Wszystkie właściwości i metadanych obiektów blob, plików oraz jednostki są zachowywane podczas zmiany nazwy.
* Mimo że usługę Azure Stack, obecnie nie obsługuje udziałów plików, udziały plików nadal pojawia się pod węzłem dołączone konto magazynu usługi Azure Stack.
* Powłoka elektronów wykorzystywane przez Eksploratora magazynu ma problemy z niektórych przyspieszania sprzętowego procesora GPU (jednostka przetwarzania grafiki). Jeśli Eksplorator usługi Storage wyświetla puste okno główne (puste), możesz spróbować uruchomić Eksplorator usługi Storage z wiersza polecenia i wyłączyć Przyspieszenie GPU, dodając przełącznik `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Dla użytkowników w systemie Ubuntu 14.04, konieczne będzie upewnij się, GCC jest aktualne — można to zrobić, uruchamiając następujące polecenia, a następnie ponownym uruchomieniu komputera:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Dla użytkowników w systemie Ubuntu 17.04 będą musieli zainstalować GConf — można to zrobić, uruchamiając następujące polecenia, a następnie ponownym uruchomieniu komputera:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-094-and-093"></a>Wersja 0.9.4 i 0.9.3
2018-01/21

### <a name="new"></a>Nowa
* Istniejącego okna Eksploratora usługi Storage będą używane ponownie gdy:
    * Otwieranie linków bezpośrednich generowane w Eksploratorze usługi Storage.
    * Otwieranie Eksploratora usługi Storage w portalu.
    * Otwieranie Eksploratora usługi Storage z rozszerzenia Azure Storage w programie VS Code (wkrótce).
* Dodano możliwość otwierania nowego okna Eksploratora usługi Storage z w Eksploratorze usługi Storage.
    * Windows jest to opcja "W nowym oknie" w Menu Plik i w menu kontekstowym na pasku zadań.
    * Dla komputerów Mac jest opcja "W nowym oknie" w menu aplikacji.

### <a name="fixes"></a>Poprawki
* Rozwiązano problem z zabezpieczeniami. Przeprowadź uaktualnienie do 0.9.4 przy najbliższej sposobności.
* Stary działania zostały nie odpowiednio są czyszczone. Ma to wpływu na wydajność długo działające zadania. One są teraz są czyszczone poprawnie.
* Akcje związane z dużą liczbą plików i katalogów spowoduje, że od czasu do czasu Eksploratora usługi Storage można zablokować. Żądania na platformie Azure dla udziałów plików teraz są ograniczone do ograniczania wykorzystania zasobów systemowych.

### <a name="known-issues"></a>Znane problemy
* Eksplorator usługi Storage nie obsługują kont usług AD FS.
* Klawisze skrótów dla "Widoku Eksploratora" i "Widoku zarządzania kontami" powinien być Ctrl / Cmd + Shift + E i Ctrl / Cmd + Shift + A odpowiednio.
* Przeznaczone dla usługi Azure Stack, przekazywanie pewne pliki jako uzupełnialnych obiektów blob może zakończyć się niepowodzeniem.
* Po kliknięciu przycisku "Anuluj" do zadania, może upłynąć trochę czasu tego zadania anulować. Dzieje się tak dlatego, że używamy filtru anulowania obejścia tego problemu.
* Jeśli wybrano nieprawidłowy numer PIN/certyfikatu karty inteligentnej, należy uruchomić ponownie, aby mogła mieć Eksploratora usługi Storage zapomnij tej decyzji.
* Na panelu ustawień konta mogą być wyświetlane, należy ponownie wprowadzić poświadczenia, aby filtrowanie subskrypcji.
* Zmiana nazwy obiektów blob (pojedynczo lub w kontenerze obiektów blob zmieniono nazwę) nie zostaną zachowane migawki. Wszystkie właściwości i metadanych obiektów blob, plików oraz jednostki są zachowywane podczas zmiany nazwy.
* Mimo że usługę Azure Stack, obecnie nie obsługuje udziałów plików, udziały plików nadal pojawia się pod węzłem dołączone konto magazynu usługi Azure Stack.
* Powłoka elektronów wykorzystywane przez Eksploratora magazynu ma problemy z niektórych przyspieszania sprzętowego procesora GPU (jednostka przetwarzania grafiki). Jeśli Eksplorator usługi Storage wyświetla puste okno główne (puste), możesz spróbować uruchomić Eksplorator usługi Storage z wiersza polecenia i wyłączyć Przyspieszenie GPU, dodając przełącznik `--disable-gpu`:

    ```
    ./StorageExplorer --disable-gpu
    ```

* Dla użytkowników w systemie Ubuntu 14.04, konieczne będzie upewnij się, GCC jest aktualne — można to zrobić, uruchamiając następujące polecenia, a następnie ponownym uruchomieniu komputera:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Dla użytkowników w systemie Ubuntu 17.04 będą musieli zainstalować GConf — można to zrobić, uruchamiając następujące polecenia, a następnie ponownym uruchomieniu komputera:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-092"></a>Wersja 0.9.2
11/01/2017

### <a name="hotfixes"></a>Poprawki
* Nieoczekiwane dane zmiany były możliwe, podczas edytowania Edm.DateTime wartości dla jednostek z tabeli w zależności od lokalnej strefy czasowej. Edytor korzysta z pola tekstowego, dając precyzyjny, spójną kontrolę nad Edm.DateTime wartości.
* Trwa przekazywanie/pobieranie grupy obiektów blob, gdy dołączony nazwą i kluczem nie może uruchomić. Ten problem został rozwiązany.
* Wcześniej Eksploratora usługi Storage będzie tylko monit o ponowne uwierzytelnianie stare konto, jeśli jeden lub więcej subskrypcji do konta został wybrany. Teraz Eksploratora usługi Storage wyświetli monit nawet wtedy, gdy konto jest w pełni odfiltrowane.
* Domena punktów końcowych dla usługi Azure instytucji rządowych USA była nieprawidłowa. Został rozwiązany.
* Przycisk Zastosuj w oknie Zarządzanie kontami czasami był trudny do kliknij. Już nie powinno to nastąpić.

### <a name="new"></a>Nowa
* Obsługa usługi Azure Cosmos DB (wersja zapoznawcza):
    * [Dokumentacja online](./cosmos-db/storage-explorer.md)
    * Tworzenie baz danych i kolekcji
    * Manipulowanie danymi
    * Zapytanie, tworzenie lub usuwanie dokumentów
    * Zaktualizuj procedury składowane, funkcje zdefiniowane przez użytkownika lub wyzwalaczy
    * Używać parametrów połączenia, aby połączyć się i zarządzanie bazami danych
* Zwiększona wydajność Trwa przekazywanie/pobieranie wielu małych obiektów blob.
* Jeśli występują błędy w grupie przekazywania obiektu blob lub grupa pobierania obiektów blob, należy dodać akcji "Ponów próbę wykonania All".
* Eksplorator usługi Storage teraz wstrzyma iteracji podczas pobierania/przekazywania obiektów blob, jeśli wykryje, że połączenie sieciowe zostało utracone. Następnie można wznowić iteracji, po ponownym nawiązaniu połączenia sieciowego.
* Dodanie do "Zamknij", "Zamknij inne" i "Zamknij" karty przy użyciu menu kontekstowego.
* Eksplorator usługi Storage korzysta z natywnych oknami dialogowymi i menu kontekstowe natywnych.
* Eksplorator usługi Storage jest teraz łatwiej dostępne. Ulepszenia obejmują:
    * Obsługiwane czytniki zawartości ekranu ulepszone, NVDA na Windows i VoiceOver na komputerze Mac
    * Ulepszone motyw o wysokim kontraście
    * Fokus klawiatury i przełączania klawiatury poprawki

### <a name="fixes"></a>Poprawki
* Jeśli próbowano otworzyć lub pobrać obiekt blob z nieprawidłową nazwą pliku Windows, operacja zakończy się niepowodzeniem. Eksplorator usługi Storage teraz wykryje, jeśli nazwa obiektu blob jest nieprawidłowy i zapytaj, czy chcesz pominąć obiektu blob lub Zakoduj je. Eksplorator usługi Storage wykryje również, jeśli nazwa pliku wydaje się być kodowany i pytanie, jeśli chcesz odkodować go przed przekazaniem.
* Podczas przekazywania obiektów blob edytor dla docelowym kontenerem obiektów blob może czasami nie prawidłowo odświeżenia. Ten problem został rozwiązany.
* Obsługa kilku rodzaje parametrów połączeń i identyfikatorów URI sygnatury dostępu Współdzielonego jest uwzględniona. Firma Microsoft zostały rozwiązane wszystkie znane problemy, ale należy wysłać opinię, jeśli dodatkowo wystąpią problemy.
* Powiadomienie o aktualizacji został przerwany dla niektórych użytkowników w 0.9.0. Ten problem został rozwiązany, a dla tych, których dotyczy usterka, można ręcznie pobrać najnowszą [wersję Eksplorator usługi Storage tego](https://azure.microsoft.com/features/storage-explorer/)problemu.

### <a name="known-issues"></a>Znane problemy
* Eksplorator usługi Storage nie obsługują kont usług AD FS.
* Klawisze skrótów dla "Widoku Eksploratora" i "Widoku zarządzania kontami" powinien być Ctrl / Cmd + Shift + E i Ctrl / Cmd + Shift + A odpowiednio.
* Przeznaczone dla usługi Azure Stack, przekazywanie pewne pliki jako uzupełnialnych obiektów blob może zakończyć się niepowodzeniem.
* Po kliknięciu przycisku "Anuluj" do zadania, może upłynąć trochę czasu tego zadania anulować. Dzieje się tak dlatego, że używamy filtru anulowania obejścia tego problemu.
* Jeśli wybrano nieprawidłowy numer PIN/certyfikatu karty inteligentnej, należy uruchomić ponownie, aby mogła mieć Eksploratora usługi Storage zapomnij tej decyzji.
* Na panelu ustawień konta mogą być wyświetlane, należy ponownie wprowadzić poświadczenia, aby filtrowanie subskrypcji.
* Zmiana nazwy obiektów blob (pojedynczo lub w kontenerze obiektów blob zmieniono nazwę) nie zostaną zachowane migawki. Wszystkie właściwości i metadanych obiektów blob, plików oraz jednostki są zachowywane podczas zmiany nazwy.
* Mimo że usługę Azure Stack, obecnie nie obsługuje udziałów plików, udziały plików nadal pojawia się pod węzłem dołączone konto magazynu usługi Azure Stack.
* Powłoka elektronów wykorzystywane przez Eksploratora magazynu ma problemy z niektórych przyspieszania sprzętowego procesora GPU (jednostka przetwarzania grafiki). Jeśli Eksplorator usługi Storage wyświetla puste okno główne (puste), możesz spróbować uruchomić Eksplorator usługi Storage z wiersza polecenia i wyłączyć Przyspieszenie GPU, dodając przełącznik `--disable-gpu`:

    ```
    ./StorageExplorer --disable-gpu
    ```

* Dla użytkowników w systemie Ubuntu 14.04, konieczne będzie upewnij się, GCC jest aktualne — można to zrobić, uruchamiając następujące polecenia, a następnie ponownym uruchomieniu komputera:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Dla użytkowników w systemie Ubuntu 17.04 będą musieli zainstalować GConf — można to zrobić, uruchamiając następujące polecenia, a następnie ponownym uruchomieniu komputera:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-091-and-090"></a>Wersja 0.9.1 i 0.9.0
10/20/2017
### <a name="new"></a>Nowa
* Obsługa usługi Azure Cosmos DB (wersja zapoznawcza):
    * [Dokumentacja online](./cosmos-db/storage-explorer.md)
    * Tworzenie baz danych i kolekcji
    * Manipulowanie danymi
    * Zapytanie, tworzenie lub usuwanie dokumentów
    * Zaktualizuj procedury składowane, funkcje zdefiniowane przez użytkownika lub wyzwalaczy
    * Używać parametrów połączenia, aby połączyć się i zarządzanie bazami danych
* Zwiększona wydajność Trwa przekazywanie/pobieranie wielu małych obiektów blob.
* Jeśli występują błędy w grupie przekazywania obiektu blob lub grupa pobierania obiektów blob, należy dodać akcji "Ponów próbę wykonania All".
* Eksplorator usługi Storage teraz wstrzyma iteracji podczas pobierania/przekazywania obiektów blob, jeśli wykryje, że połączenie sieciowe zostało utracone. Następnie można wznowić iteracji, po ponownym nawiązaniu połączenia sieciowego.
* Dodanie do "Zamknij", "Zamknij inne" i "Zamknij" karty przy użyciu menu kontekstowego.
* Eksplorator usługi Storage korzysta z natywnych oknami dialogowymi i menu kontekstowe natywnych.
* Eksplorator usługi Storage jest teraz łatwiej dostępne. Ulepszenia obejmują:
    * Obsługiwane czytniki zawartości ekranu ulepszone, NVDA na Windows i VoiceOver na komputerze Mac
    * Ulepszone motyw o wysokim kontraście
    * Fokus klawiatury i przełączania klawiatury poprawki

### <a name="fixes"></a>Poprawki
* Jeśli próbowano otworzyć lub pobrać obiekt blob z nieprawidłową nazwą pliku Windows, operacja zakończy się niepowodzeniem. Eksplorator usługi Storage teraz wykryje, jeśli nazwa obiektu blob jest nieprawidłowy i zapytaj, czy chcesz pominąć obiektu blob lub Zakoduj je. Eksplorator usługi Storage wykryje również, jeśli nazwa pliku wydaje się być kodowany i pytanie, jeśli chcesz odkodować go przed przekazaniem.
* Podczas przekazywania obiektów blob edytor dla docelowym kontenerem obiektów blob może czasami nie prawidłowo odświeżenia. Ten problem został rozwiązany.
* Obsługa kilku rodzaje parametrów połączeń i identyfikatorów URI sygnatury dostępu Współdzielonego jest uwzględniona. Firma Microsoft zostały rozwiązane wszystkie znane problemy, ale należy wysłać opinię, jeśli dodatkowo wystąpią problemy.
* Powiadomienie o aktualizacji został przerwany dla niektórych użytkowników w 0.9.0. Ten problem został rozwiązany, a dla tych, których dotyczy usterka, można ręcznie pobrać najnowszą wersję Eksplorator usługi Storage [tutaj](https://azure.microsoft.com/features/storage-explorer/)

### <a name="known-issues"></a>Znane problemy
* Eksplorator usługi Storage nie obsługują kont usług AD FS.
* Klawisze skrótów dla "Widoku Eksploratora" i "Widoku zarządzania kontami" powinien być Ctrl / Cmd + Shift + E i Ctrl / Cmd + Shift + A odpowiednio.
* Przeznaczone dla usługi Azure Stack, przekazywanie pewne pliki jako uzupełnialnych obiektów blob może zakończyć się niepowodzeniem.
* Po kliknięciu przycisku "Anuluj" do zadania, może upłynąć trochę czasu tego zadania anulować. Dzieje się tak dlatego, że używamy filtru anulowania obejścia tego problemu.
* Jeśli wybrano nieprawidłowy numer PIN/certyfikatu karty inteligentnej, należy uruchomić ponownie, aby mogła mieć Eksploratora usługi Storage zapomnij tej decyzji.
* Na panelu ustawień konta mogą być wyświetlane, należy ponownie wprowadzić poświadczenia, aby filtrowanie subskrypcji.
* Zmiana nazwy obiektów blob (pojedynczo lub w kontenerze obiektów blob zmieniono nazwę) nie zostaną zachowane migawki. Wszystkie właściwości i metadanych obiektów blob, plików oraz jednostki są zachowywane podczas zmiany nazwy.
* Mimo że usługę Azure Stack, obecnie nie obsługuje udziałów plików, udziały plików nadal pojawia się pod węzłem dołączone konto magazynu usługi Azure Stack.
* Powłoka elektronów wykorzystywane przez Eksploratora magazynu ma problemy z niektórych przyspieszania sprzętowego procesora GPU (jednostka przetwarzania grafiki). Jeśli Eksplorator usługi Storage wyświetla puste okno główne (puste), możesz spróbować uruchomić Eksplorator usługi Storage z wiersza polecenia i wyłączyć Przyspieszenie GPU, dodając przełącznik `--disable-gpu`:

    ```
    ./StorageExplorer --disable-gpu
    ```

* Dla użytkowników w systemie Ubuntu 14.04, konieczne będzie upewnij się, GCC jest aktualne — można to zrobić, uruchamiając następujące polecenia, a następnie ponownym uruchomieniu komputera:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Dla użytkowników w systemie Ubuntu 17.04 będą musieli zainstalować GConf — można to zrobić, uruchamiając następujące polecenia, a następnie ponownym uruchomieniu komputera:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-0816"></a>Wersja 0.8.16
8/21/2017

### <a name="new"></a>Nowa
* Po otwarciu obiektu blob, Eksploratora usługi Storage wyświetli monit przekazać pobrany plik, jeśli zmiana zostaje wykryta
* Udoskonalone funkcje logowania usługi Azure Stack
* Zwiększona wydajność Trwa przekazywanie/pobieranie wielu małych plików w tym samym czasie


### <a name="fixes"></a>Poprawki
* Dla niektórych typów obiektów blob wybierając pozycję "replace" podczas przekazywania konflikt czasami spowodowałaby przekazywania uruchamiany ponownie.
* W wersji 0.8.15 przekazywania będzie czasami zatrzymania na 99%.
* Podczas przekazywania plików do udziału plików, jeśli została wybrana opcja Przekaż do katalogu, który nie został jeszcze istnieje, przekazywanie zakończy się niepowodzeniem.
* Eksplorator usługi Storage została niepoprawnie Generowanie sygnatury czasowe dla sygnatury dostępu współdzielonego i zapytania w tabeli.


### <a name="known-issues"></a>Znane problemy
* Przy użyciu nazwy i parametry połączenia klucza aktualnie nie działa. Zostanie on rozwiązany w następnej wersji. W międzyczasie można dołączyć nazwą i kluczem.
* Jeśli podczas próby otwarcia pliku o nieprawidłowej nazwie pliku Windows, pliki do pobrania spowoduje błąd — nie znaleziono pliku.
* Po kliknięciu przycisku "Anuluj" do zadania, może upłynąć trochę czasu tego zadania anulować. Jest to ograniczenie biblioteki węzeł magazynu platformy Azure.
* Po zakończeniu przekazywania obiektów blob, karty, który zainicjował przekazywania zostanie odświeżony. Różni się od poprzedniego zachowania, a także spowoduje zostać przeniesiony z powrotem do kontenera, które znajdują się w katalogu głównym.
* Jeśli wybrano nieprawidłowy numer PIN/certyfikatu karty inteligentnej, należy uruchomić ponownie, aby mogła mieć Eksploratora usługi Storage zapomnij tej decyzji.
* Na panelu ustawień konta mogą być wyświetlane, należy ponownie wprowadzić poświadczenia, aby filtrowanie subskrypcji.
* Zmiana nazwy obiektów blob (pojedynczo lub w kontenerze obiektów blob zmieniono nazwę) nie zostaną zachowane migawki. Wszystkie właściwości i metadanych obiektów blob, plików oraz jednostki są zachowywane podczas zmiany nazwy.
* Mimo że usługę Azure Stack, obecnie nie obsługuje udziałów plików, udziały plików nadal pojawia się pod węzłem dołączone konto magazynu usługi Azure Stack.
* Dla użytkowników w systemie Ubuntu 14.04, konieczne będzie upewnij się, GCC jest aktualne — można to zrobić, uruchamiając następujące polecenia, a następnie ponownym uruchomieniu komputera:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Dla użytkowników w systemie Ubuntu 17.04 będą musieli zainstalować GConf — można to zrobić, uruchamiając następujące polecenia, a następnie ponownym uruchomieniu komputera:

    ```
    sudo apt-get install libgconf-2-4
    ```

### <a name="version-0814"></a>Wersja 0.8.14
06/22/2017

### <a name="new"></a>Nowa

* Zaktualizowana wersja elektronów do 1.7.2, aby można było korzystać z kilku krytyczne aktualizacje zabezpieczeń
* Możesz teraz szybko uzyskiwać dostęp online przewodnik rozwiązywania problemów z menu Pomoc
* [Przewodnik][2] rozwiązywania problemów Eksplorator usługi Storage
* [Instrukcje][3] dotyczące nawiązywania połączenia z subskrypcją Azure Stack

### <a name="known-issues"></a>Znane problemy

* Przycisków w oknie dialogowym potwierdzenia usunięcia folderu nie zarejestrujesz kliknięciami myszy w systemie Linux. Obejście polega na użyciu klawisza ENTER
* Jeśli wybierzesz nieprawidłowego certyfikatu karty inteligentnej na numer PIN/następnie konieczne będzie ponowne uruchomienie, aby mogła mieć zapomnij decyzji Eksploratora usługi Storage
* Posiadanie więcej niż 3 grup obiektów blob lub przekazywania w tym samym czasie plików może powodować błędy
* Na panelu ustawień konta mogą być wyświetlane, należy ponownie wprowadzić poświadczenia w celu filtrowania subskrypcji
* Zmiana nazwy obiektów blob (pojedynczo lub w kontenerze obiektów blob zmieniono nazwę) nie zostaną zachowane migawki. Wszystkie właściwości i metadanych obiektów blob, plików oraz jednostki są zachowywane podczas zmiany nazwy.
* Mimo że usługę Azure Stack aktualnie nie obsługuje udziałów plików, udziały plików nadal pojawia się pod węzłem dołączone konto magazynu usługi Azure Stack.
* Ubuntu 14.04 instalacji wymaga wersji kompilatora gcc, zaktualizowane lub uaktualnione — kroki niezbędne do uaktualnienia są poniżej:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

### <a name="version-0813"></a>Wersja 0.8.13
05/12/2017

#### <a name="new"></a>Nowa

* [Przewodnik][2] rozwiązywania problemów Eksplorator usługi Storage
* [Instrukcje][3] dotyczące nawiązywania połączenia z subskrypcją Azure Stack

#### <a name="fixes"></a>Poprawki

* Poprawiono: Przekazywanie pliku zadowoleni wysokiej powodować błąd braku pamięci
* Rozwiązano problem: Użytkownik może teraz Zaloguj się przy użyciu kodu PIN/karty inteligentnej
* Naprawiono: otwarta w portalu teraz współpracuje z platformą Azure Chiny 21Vianet, platformą Azure (Niemcy), platformą Azure dla instytucji rządowych i Azure Stack
* Poprawiono: Podczas przekazywania folderu do kontenera obiektów blob, "Niedozwolona operacja" mógłby wystąpić błąd, czasami
* Rozwiązano: Zaznacz wszystko zostało wyłączone migawek i zarządzania nimi
* Naprawiono: Metadane obiektu blob podstawowy może spowodować zastąpienie po obejrzeniu właściwości jego migawek

#### <a name="known-issues"></a>Znane problemy

* Jeśli wybierzesz nieprawidłowego certyfikatu karty inteligentnej na numer PIN/następnie konieczne będzie ponowne uruchomienie, aby mogła mieć zapomnij decyzji Eksploratora usługi Storage
* Gdy jest powiększony wewnątrz lub na zewnątrz, poziom powiększenia chwilowo może zresetować do domyślnego poziomu
* Posiadanie więcej niż 3 grup obiektów blob lub przekazywania w tym samym czasie plików może powodować błędy
* Na panelu ustawień konta mogą być wyświetlane, należy ponownie wprowadzić poświadczenia w celu filtrowania subskrypcji
* Zmiana nazwy obiektów blob (pojedynczo lub w kontenerze obiektów blob zmieniono nazwę) nie zostaną zachowane migawki. Wszystkie właściwości i metadanych obiektów blob, plików oraz jednostki są zachowywane podczas zmiany nazwy.
* Mimo że usługę Azure Stack, obecnie nie obsługuje udziałów plików, udziały plików nadal pojawia się pod węzłem dołączone konto magazynu usługi Azure Stack.
* Ubuntu 14.04 instalacji wymaga wersji kompilatora gcc, zaktualizowane lub uaktualnione — kroki niezbędne do uaktualnienia są poniżej:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```


### <a name="version-0812-and-0811-and-0810"></a>Wersja 0.8.12 i 0.8.11 i 0.8.10
04/07/2017

#### <a name="new"></a>Nowa

* Eksplorator usługi Storage zostanie automatycznie zamknięta podczas instalowania aktualizacji z powiadomienie o aktualizacji
* Szybki dostęp w miejscu udostępnia udoskonalone funkcje do pracy z często używanych zasobów
* W edytorze kontenera obiektów Blob można teraz zobaczyć które maszyny wirtualnej dzierżawy obiektu blob należy do
* Teraz można zwinąć panelu po lewej stronie
* Odnajdywanie teraz działa, w tym samym czasie jako plik do pobrania
* Użyj statystyki w edytorach kontenera obiektów Blob, udziału plików i tabeli, aby wyświetlić rozmiar zasobu lub zaznaczenie
* Możesz teraz zalogować do usługi Azure Active Directory (AAD) na podstawie konta usługi Azure Stack.
* Teraz możesz przekazać pliki w archiwum przekracza 32MB do kont usługi Premium storage
* Ulepszona obsługa ułatwień dostępu
* Można teraz dodać zaufane certyfikaty protokołu SSL X. 509 z kodowaniem Base-64, przechodząc do edycji&gt; certyfikaty SSL —&gt; zaimportować certyfikaty.

#### <a name="fixes"></a>Poprawki

* Poprawiono: po odświeżeniu poświadczenia konta, w widoku drzewa może czasami będzie odświeżana automatycznie
* Poprawiono: Generowanie sygnatury dostępu Współdzielonego dla emulatora kolejek i tabel w wyniku nieprawidłowy adres URL
* Naprawiono: kont usługi premium storage można teraz rozszerzać po włączeniu serwera proxy
* Poprawiono: przycisk Zastosuj, na stronie zarządzania konta nie będzie działać gdyby konta 1 lub 0, zaznaczone
* Poprawiono: przekazywanie obiektów blob, które wymagają rozwiązania konfliktu może zakończyć się niepowodzeniem - rozwiązane w 0.8.11
* Poprawiono: wysyłanie opinii został w uszkodzone 0.8.11 - rozwiązane w 0.8.12

#### <a name="known-issues"></a>Znane problemy

* Po uaktualnieniu do wersji 0.8.10, należy odświeżyć wszystkie swoje poświadczenia.
* Gdy jest powiększony wewnątrz lub na zewnątrz, poziom powiększenia chwilowo może zresetować do domyślnego poziomu.
* Posiadanie więcej niż 3 grup obiektów blob lub przekazywania w tym samym czasie plików może powodować błędy.
* Na panelu ustawień konta mogą być wyświetlane, należy ponownie wprowadzić poświadczenia, aby filtrowanie subskrypcji.
* Zmiana nazwy obiektów blob (pojedynczo lub w kontenerze obiektów blob zmieniono nazwę) nie zostaną zachowane migawki. Wszystkie właściwości i metadanych obiektów blob, plików oraz jednostki są zachowywane podczas zmiany nazwy.
* Mimo że usługę Azure Stack, obecnie nie obsługuje udziałów plików, udziały plików nadal pojawia się pod węzłem dołączone konto magazynu usługi Azure Stack.
* Ubuntu 14.04 instalacji wymaga wersji kompilatora gcc, zaktualizowane lub uaktualnione — kroki niezbędne do uaktualnienia są poniżej:

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
* Poprawka: przy użyciu portalu wygenerowanego identyfikatora URI połączenia SAS, aby dołączyć konto magazynu spowoduje wystąpienie błędu.
* Można teraz tworzyć, zarządzać i podwyższanie poziomu migawki obiektu blob.
* Możesz teraz zalogować się do konta platformy Azure z Chin, platformy Azure (Niemcy) i usługi Azure USA.
* Teraz można zmienić poziom powiększenia. Użyj opcji w menu Widok, aby powiększanie, zmniejszanie i zresetować powiększenie.
* Znaki Unicode są teraz obsługiwane w metadanych użytkownika do plików i obiektów blob.
* Ulepszenia ułatwień dostępu.
* Informacje o wersji w następnej wersji mogą być wyświetlane z powiadomienie o aktualizacji. Można również wyświetlić informacje o bieżącej wersji w menu Pomoc.

#### <a name="fixes"></a>Poprawki

* Naprawiono: numer wersji jest teraz prawidłowo wyświetlane w Panelu sterowania na Windows
* Naprawiono: wyszukiwanie nie jest już ograniczona do 50 000 węzłów
* Poprawiono: przekazywanie do udziału plików, uruchamiane zawsze, jeśli katalog docelowy nie istniał już
* Naprawiono: ulepszona stabilność dla długich przekazywanie i pobieranie

#### <a name="known-issues"></a>Znane problemy

* Gdy jest powiększony wewnątrz lub na zewnątrz, poziom powiększenia chwilowo może zresetować do domyślnego poziomu.
* Szybki dostęp działa tylko z elementami na podstawie subskrypcji. Zasoby lokalne lub dołączone za pomocą klucza lub tokenu sygnatury dostępu Współdzielonego nie są obsługiwane w tej wersji.
* Może potrwać kilka sekund, aby przejść do zasobu docelowego, w zależności od liczby zasobów, możesz mieć szybki dostęp.
* Posiadanie więcej niż 3 grup obiektów blob lub przekazywania w tym samym czasie plików może powodować błędy.

12/16/2016
### <a name="version-087"></a>Wersja 0.8.7

>[!VIDEO https://www.youtube.com/embed/Me4Y4jxoer8?ecver=1]

#### <a name="new"></a>Nowa

* Można wybrać sposób rozwiązywania konfliktów na początku sesji aktualizacja, pobieranie lub kopiowanie w oknie działań
* Umieść kursor nad kartę, aby wyświetlić pełną ścieżkę zasobów magazynu
* Po kliknięciu na karcie, synchronizuje się z lokalizacji, w okienku nawigacji po lewej stronie

#### <a name="fixes"></a>Poprawki

* Naprawiono: Eksplorator usługi Storage jest teraz zaufanych aplikacji dla komputerów Mac
* Naprawiono: Ubuntu 14.04 jest ponownie obsługiwane
* Naprawiono: Czasami Dodaj konto interfejsu użytkownika miga podczas ładowania subskrypcji
* Naprawiono: Czasami nie wszystkie zasoby magazynu zostały wymienione w okienku nawigacji po lewej stronie
* Poprawiono: Okienko akcji czasami jest wyświetlany pusty akcji
* Naprawiono: Rozmiar okna z ostatniej sesji zamknięte są teraz przechowywane
* Naprawiono: Możesz otworzyć wiele kart dla tego samego zasobu za pomocą menu kontekstowego

#### <a name="known-issues"></a>Znane problemy

* Szybki dostęp działa tylko z elementami na podstawie subskrypcji. Zasoby lokalne lub dołączone za pomocą klucza lub tokenu sygnatury dostępu Współdzielonego nie są obsługiwane w tej wersji
* Może potrwać kilka sekund, aby przejść do zasobu docelowego, w zależności od liczby zasobów, możesz mieć szybki dostęp
* Posiadanie więcej niż 3 grup obiektów blob lub przekazywania w tym samym czasie plików może powodować błędy
* Dojść wyszukiwania przeszukiwanie około 50 000 węzłów — dzięki temu może mieć wpływ na wydajność lub może spowodować, że nieobsługiwany wyjątek
* Po raz pierwszy w systemie macOS przy użyciu Eksploratora usługi Storage mogą pojawiać się wiele monitów pytania o uprawnienia użytkownika do uzyskania dostępu łańcucha kluczy. Sugerujemy, że wybierasz zawsze Zezwalaj na tak monit nie pojawi się ponownie

11/18/2016
### <a name="version-086"></a>Wersja 0.8.6

#### <a name="new"></a>Nowa

* Możesz teraz numer pin najczęściej używane usługi do paska szybki dostęp do prostą nawigację
* Teraz możesz otworzyć wiele edytorów na różnych kartach. Pojedyncze kliknięcie, aby otworzyć kartę tymczasową; Kliknij dwukrotnie, aby otworzyć kartę trwałą. Możesz również kliknąć kartę tymczasową, aby uczynić ją stałą kartą
* Wprowadziliśmy wydajność i stabilność dla przekazuje i pobiera, szczególnie w przypadku dużych plików na komputerach szybkie
* Puste foldery "wirtualne" można teraz tworzyć kontenery obiektów blob
* Ponownie wprowadziliśmy zakresu wyszukiwania za pomocą naszego nowego wyszukiwania podciągu rozszerzone, dzięki czemu masz teraz dwie opcje wyszukiwania:
    * Globalne wyszukiwanie — po prostu wprowadź wyszukiwany termin w polu tekstowym wyszukiwania
    * Wyszukiwania zakresowego — kliknij na ikonę szkła powiększającego obok węzła, a następnie dodaj wyszukiwany termin do końca ścieżki, lub kliknij prawym przyciskiem myszy i wybierz "Wyszukiwania z tutaj"
* Dodaliśmy różne kompozycje: światła (ustawienie domyślne), ciemny, wysoki kontrast — kolor czarny i wysoki kontrast — kolor biały. Przejdź do pozycji Edytuj-&gt; motywy, aby zmienić preferencje związane z motywem
* Można zmodyfikować właściwości obiektów Blob i plików
* Zapewniamy obecnie obsługę zakodowany (kodowanie base64) i komunikatów w kolejce niekodowany
* W systemie Linux, 64-bitowych systemach operacyjnych jest teraz wymagany. W tej wersji obsługiwane jest ściąganie tylko 64-bitowego systemu Ubuntu 16.04.1 LTS
* Zaktualizowaliśmy nasze logo!

#### <a name="fixes"></a>Poprawki

* Poprawiono: Ekranu zamrożenia problemów
* Naprawiono: Zwiększone zabezpieczenia
* Poprawiono: Czasami zduplikowanego dołączonych kont może się pojawić
* Naprawiono: Obiekt blob z Niezdefiniowany typ zawartości może wygenerować wyjątku
* Poprawiono: Otwieranie panelu zapytania w pustej tabeli nie było możliwe
* Naprawiono: Różni się w usłudze wyszukiwania
* Naprawiono: Zwiększenie liczby zasobów ładowane z 50 do 100 po kliknięciu przycisku "Więcej obciążenia"
* Rozwiązano problem: Przy pierwszym uruchomieniu, jeśli konto jest zalogowany, możemy teraz wszystkie subskrypcje dla tego konta domyślnie wybierane

#### <a name="known-issues"></a>Znane problemy

* Ta wersja programu Storage Explorer nie działa w systemie Ubuntu 14.04
* Aby otworzyć wiele kart dla tego samego zasobu, stale klikaj tego samego zasobu. Kliknij przycisk na inny zasób i następnie wrócić, a następnie kliknij polecenie oryginalny zasób, aby otworzyć go ponownie w innej karcie
* Szybki dostęp działa tylko z elementami na podstawie subskrypcji. Zasoby lokalne lub dołączone za pomocą klucza lub tokenu sygnatury dostępu Współdzielonego nie są obsługiwane w tej wersji
* Może potrwać kilka sekund, aby przejść do zasobu docelowego, w zależności od liczby zasobów, możesz mieć szybki dostęp
* Posiadanie więcej niż 3 grup obiektów blob lub przekazywania w tym samym czasie plików może powodować błędy
* Dojść wyszukiwania przeszukiwanie około 50 000 węzłów — dzięki temu może mieć wpływ na wydajność lub może spowodować, że nieobsługiwany wyjątek

10/03/2016
### <a name="version-085"></a>Wersja 0.8.5

#### <a name="new"></a>Nowa

* Teraz użyć klucze SAS wygenerowany przez Portal, aby dołączyć do konta magazynu i zasoby

#### <a name="fixes"></a>Poprawki

* Poprawiono: sytuacja wyścigu podczas wyszukiwania czasami spowodowane węzłów innych niż rozwijania
* Naprawiono: "Użyj protokołu HTTP" nie działa w przypadku nawiązywania połączenia z kontami magazynu przy użyciu nazwy i klucza konta
* Naprawiono: Klucze sygnatur dostępu Współdzielonego (specjalnie wygenerowanych przez Portal tymi) zwracają błąd "ukośnikiem na końcu"
* Naprawiono: Importowanie tabeli problemów
    * Czasami zostały cofnięte klucza partycji i klucz wiersza
    * Nie można odczytać "wartości null" kluczy partycji

#### <a name="known-issues"></a>Znane problemy

* Obsługuje wyszukiwanie przeszukiwanie około 50 000 węzłów — dzięki temu może mieć wpływ na wydajność
* Usługa Azure Stack aktualnie nie obsługuje plików, więc próby rozwinięcia plików spowoduje wyświetlenie błędu

09/12/2016
### <a name="version-084"></a>Wersja 0.8.4

>[!VIDEO https://www.youtube.com/embed/cr5tOGyGrIQ?ecver=1]

#### <a name="new"></a>Nowa

* Generowanie bezpośrednie linki do kont magazynu, kontenerów, kolejek, tabel lub udziały plików na potrzeby udostępniania i obsługuje łatwy dostęp do zasobów — Windows i Mac OS
* Wyszukaj kontenery obiektów blob, tabel, kolejek, udziały plików lub konta magazynu z pola wyszukiwania
* Teraz można pogrupować klauzul w Konstruktorze zapytań tabeli
* Zmień nazwę i kopiowania/wklejania kontenery obiektów blob, udziały plików, tabel, obiektów blob, foldery obiektów blob, plików i katalogów z wewnątrz dołączone sygnatury dostępu Współdzielonego konta i kontenery
* Zmiana nazwy i kopiowania obiektów blob kontenerów i udziałów plików teraz zachować, właściwości oraz metadanych

#### <a name="fixes"></a>Poprawki

* Poprawiono: nie można edytować jednostki z tabeli jeśli zawierają one właściwości logiczną lub binarny

#### <a name="known-issues"></a>Znane problemy

* Obsługuje wyszukiwanie przeszukiwanie około 50 000 węzłów — dzięki temu może mieć wpływ na wydajność

08/03/2016
### <a name="version-083"></a>Wersja 0.8.3

>[!VIDEO https://www.youtube.com/embed/HeGW-jkSd9Y?ecver=1]

#### <a name="new"></a>Nowa

* Zmień nazwę kontenerów, tabel, udziały plików
* Ulepszony interfejs konstruktora zapytań
* Możliwości, aby zapisać i załadować zapytań
* Bezpośrednie linki do kont lub kontenery, kolejek, tabel magazynu lub udziałów, udostępniania i łatwe uzyskiwanie dostępu do zasobów plików (tylko Windows - macOS obsługuje już wkrótce!)
* Możliwość zarządzania i Konfiguruj reguły mechanizmu CORS

#### <a name="fixes"></a>Poprawki

* Naprawiono: Accounts Microsoft wymagają ponowne uwierzytelnianie co 8 – 12 godzin

#### <a name="known-issues"></a>Znane problemy

* Czasami interfejsu użytkownika może sprawiać wrażenie — maksymalizacja okna pomaga rozwiązać ten problem
* Zainstaluj system macOS może wymagać podwyższonym poziomem uprawnień
* Panel ustawień konta mogą być wyświetlane, należy ponownie wprowadzić poświadczenia w celu filtrowania subskrypcji
* Zmiana nazwy udziałów plików, kontenery obiektów blob i tabel nie pozwala zachować metadane lub innych właściwości w kontenerze, takie jak limit przydziału udziału plików, poziom dostępu publicznego lub zasady dostępu
* Zmiana nazwy obiektów blob (pojedynczo lub w kontenerze obiektów blob zmieniono nazwę) nie zostaną zachowane migawki. Wszystkie inne właściwości i metadane dla obiektów blob, plików i jednostek są zachowywane podczas zmiany nazwy
* Kopiowanie lub zmiana nazwy zasobów nie działa w ramach dołączone sygnatury dostępu Współdzielonego konta

07/07/2016
### <a name="version-082"></a>Wersja 0.8.2

>[!VIDEO https://www.youtube.com/embed/nYgKbRUNYZA?ecver=1]

#### <a name="new"></a>Nowa

* Konta magazynu są pogrupowane według subskrypcji; Tworzenie magazynu i zasoby dołączone za pomocą sygnatury dostępu Współdzielonego lub klucza są wyświetlane w węźle (lokalne i dołączone)
* Wyloguj się z kont w panelu "Ustawienia konta platformy Azure"
* Skonfiguruj ustawienia serwera proxy, aby włączyć i zarządzać logowania
* Tworzenie i przerwać dzierżawy obiektu blob
* Kontenery Otwórz obiektów blob, kolejek, tabel i plików za pomocą jednego kliknięcia

#### <a name="fixes"></a>Poprawki

* Poprawiono: komunikatów w kolejce wstawiony z bibliotekami .NET lub Java są nie prawidłowo zdekodować z formatu base64
* Naprawiono: $metrics tabele nie są wyświetlane dla kont usługi Blob Storage
* Naprawiono: węzeł tabele nie działa w przypadku lokalnego magazynu (Programowanie)

#### <a name="known-issues"></a>Znane problemy

* Zainstaluj system macOS może wymagać podwyższonym poziomem uprawnień

06/15/2016
### <a name="version-080"></a>Wersja 0.8.0

>[!VIDEO https://www.youtube.com/embed/ycfQhKztSIY?ecver=1]

>[!VIDEO https://www.youtube.com/embed/k4_kOUCZ0WA?ecver=1]

>[!VIDEO https://www.youtube.com/embed/3zEXJcGdl_k?ecver=1]

#### <a name="new"></a>Nowa

* Obsługa udziału plików: przeglądania, przekazywania, pobierania, kopiowania plików i katalogów, identyfikatorów URI sygnatury dostępu Współdzielonego (Tworzenie i łączenie)
* Ulepszone środowisko użytkownika dotyczące łączenia za pomocą kluczy identyfikatorów URI sygnatury dostępu Współdzielonego lub konta magazynu
* Eksportowanie wyników zapytania tabeli
* Zmienianie kolejności kolumn w tabeli i dostosowywania
* Wyświetlanie $logs kontenery obiektów blob i tabel $metrics dla kont magazynu przy użyciu metryk włączone
* Ulepszone eksportowania i importowania zachowanie, zawiera teraz typ wartości właściwości

#### <a name="fixes"></a>Poprawki

* Poprawiono: przekazywanie lub pobieranie dużych obiektów blob może spowodować niekompletne przekazywanie/pobieranie
* Naprawiono: edytowanie, dodanie lub zaimportowanie jednostki z wartością ciągu numerycznego ("1") przekonwertuje go na double
* Poprawiono: Nie można rozwinąć węzła tabeli w lokalne Środowisko deweloperskie

#### <a name="known-issues"></a>Znane problemy

* $metrics tabele nie są widoczne dla kont usługi Blob Storage
* Kolejka komunikatów dodane programowo mogą nie być wyświetlane prawidłowo, jeśli komunikaty są zakodowane w formacie Base64

05/17/2016
### <a name="version-07201605090"></a>Wersja 0.7.20160509.0

#### <a name="new"></a>Nowa

* Awarie lepszą obsługę błędów dla aplikacji

#### <a name="fixes"></a>Poprawki

* Naprawiono usterkę, gdzie komunikaty o przerwaniu pracy czasami nie są one wyświetlane podczas należało poświadczeń logowania

#### <a name="known-issues"></a>Znane problemy

* Tabele: Dodawanie, edytowanie lub importowanie jednostki, która ma właściwość z niejednoznaczną wartością liczbową, taką jak "1" lub "1,0", a użytkownik próbuje wysłać ją jako `Edm.String`, wartość zostanie przywrócona za pomocą interfejsu API klienta jako modelu EDM. Double

03/31/2016

### <a name="version-07201603250"></a>Wersja 0.7.20160325.0

>[!VIDEO https://www.youtube.com/embed/imbgBRHX65A?ecver=1]

>[!VIDEO https://www.youtube.com/embed/ceX-P8XZ-s8?ecver=1]

#### <a name="new"></a>Nowa

* Tabela obsługi: wyświetlanie, wyszukiwanie, export, import i operacji CRUD dla jednostek
* Kolejki pomocy technicznej: przeglądanie, dodawanie dequeueing wiadomości
* Identyfikatorów URI dla kont magazynu
* Łączenie się z kontami magazynu przy użyciu identyfikatorów URI sygnatury dostępu Współdzielonego
* Powiadomienia o aktualizacji dla przyszłych aktualizacji do programu Storage Explorer
* Zaktualizowany wygląd i działanie

#### <a name="fixes"></a>Poprawki

* Ulepszenia wydajności i niezawodności

### <a name="known-issues-amp-mitigations"></a>Znane problemy &amp; środki zaradcze

* Pobieranie obiektu blob w dużych plików nie działa prawidłowo — firma Microsoft zaleca używanie narzędzia AzCopy, podczas gdy ten problem można rozwiązać
* Poświadczenia konta nie zostaną pobrane ani pamięci podręcznej, jeśli nie można odnaleźć folderu głównego, lub nie można zapisać
* Jeśli dodajesz, edytujesz lub importujesz jednostkę, która ma właściwość z niejednoznaczną wartością liczbową, taką jak "1" lub "1,0", a użytkownik spróbuje wysłać ją jako `Edm.String`, wartość zostanie przywrócona za pomocą interfejsu API klienta jako modelu EDM. Double
* Podczas importowania plików CSV z rekordami wielowierszowy, dane mogą uzyskać pociąć lub zaszyfrowane

02/03/2016

### <a name="version-07201601291"></a>Wersja 0.7.20160129.1

#### <a name="fixes"></a>Poprawki

* Lepsza ogólna wydajność, gdy przekazywanie, pobieranie i kopiowanie obiektów blob

01/14/2016

### <a name="version-07201601050"></a>Wersja 0.7.20160105.0

#### <a name="new"></a>Nowa

* Pomoc techniczna Linux support (równoważności funkcji OSX)
* Dodaj kontenery obiektów blob za pomocą klucza sygnatury dostępu współdzielonego (SAS)
* Dodawanie kont magazynu dla platformy Azure (Chiny)
* Dodawanie kont magazynu przy użyciu niestandardowych punktów końcowych
* Otwieranie i wyświetlanie obiektów blob tekst i obraz zawartości
* Wyświetlanie i edytowanie właściwości obiektu blob i metadanych

#### <a name="fixes"></a>Poprawki

* Poprawiono: przekazywanie lub pobieranie dużej liczby obiektów blob (500 i nowsze) może czasami powodują, że aplikację, aby mieć biały ekran
* Poprawiono: podczas ustawiania poziom dostępu publicznego kontenera obiektów blob, nowa wartość nie jest aktualizowana do momentu ponownie ustaw fokus w kontenerze. Ponadto okno dialogowe zawsze wartość domyślna to "Nie publicznego dostępu", a nie rzeczywiste wartości bieżącej.
* Obsługa lepszej ogólnej klawiatury/ułatwień dostępu i interfejsu użytkownika
* Linki do stron nadrzędnych historii zawijany, gdy jest za długa białymi znakami
* Okno dialogowe sygnatury dostępu Współdzielonego obsługuje walidacji danych wejściowych
* Magazyn lokalny jest nadal dostępne, nawet jeśli wygasły poświadczenia użytkownika
* Po usunięciu kontenera obiektów blob otwarty Eksplorator obiektów blob po prawej stronie jest zamknięty.

#### <a name="known-issues"></a>Znane problemy

* Instalacja systemu Linux wymaga wersji kompilatora gcc zaktualizowane lub uaktualnione — kroki niezbędne do uaktualnienia są poniżej:
    * `sudo add-apt-repository ppa:ubuntu-toolchain-r/test`
    * `sudo apt-get update`
    * `sudo apt-get upgrade`
    * `sudo apt-get dist-upgrade`

11/18/2015
### <a name="version-07201511160"></a>Wersja 0.7.20151116.0

#### <a name="new"></a>Nowa

* System macOS i Windows wersje
* Zaloguj się do wyświetlić konta magazynu — Użyj konta organizacji, Account Microsoft, funkcji 2FA itp.
* Lokalnym magazynem projektowym (Używanie emulatora magazynu tylko do Windows)
* Obsługa zasobów usługi Azure Resource Manager i Model Klasyczny
* Tworzenie i usuwanie obiektów blob, kolejek i tabel
* Wyszukiwanie konkretnych obiektów blob, kolejek i tabel
* Przejrzyj zawartość kontenerów obiektów blob
* Wyświetlać i przechodzić między katalogami
* Przekazywanie, pobieranie i usuwanie obiektów blob oraz folderów
* Wyświetlanie i edytowanie właściwości obiektu blob i metadanych
* Generowanie kluczy sygnatury dostępu Współdzielonego
* Zarządzanie i tworzenie przechowywanych zasad dostępu (SAP, Distributed File System)
* Wyszukaj obiekty BLOB według prefiksu
* Przeciągnij n umieszczają pliki w celu przekazywania lub pobierania

#### <a name="known-issues"></a>Znane problemy

* Kiedy ustawienie poziomie dostępu publicznego do kontenera obiektów blob, nowa wartość nie jest aktualizowana, dopóki ponownie ustaw fokus w kontenerze
* Podczas otwierania okna dialogowego, aby ustawić poziom dostępu publicznego go zawsze jest wyświetlany "Nie publicznego dostępu" jako domyślne, a nie rzeczywiste bieżącą wartość
* Nie można zmienić nazwy pobrany obiektów blob
* Wpisy dziennika aktywności będą czasami "zatrzymywane" w toku w stanie, gdy wystąpi błąd i nie jest wyświetlany błąd
* Czasami zawiesza się lub wyłącza całkowicie biały, podczas próby przekazywanie lub pobieranie dużej liczby obiektów blob
* Czasami anulowanie operacji kopiowania nie działa
* Podczas tworzenia kontenera (obiekt blob/kolejki/table), jeśli dane wejściowe nieprawidłową nazwę i kontynuować tworzenie drugiego, w ramach typu innego kontenera nie można ustawić fokus na nowy typ
* Nie można utworzyć nowego folderu lub zmień nazwę folderu




[2]: https://support.microsoft.com/en-us/help/4021389/storage-explorer-troubleshooting-guide
[3]: vs-azure-tools-storage-manage-with-storage-explorer.md
