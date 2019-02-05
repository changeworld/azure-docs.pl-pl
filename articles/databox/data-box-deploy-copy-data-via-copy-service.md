---
title: Kopiowanie danych na urządzenie Microsoft Azure Data Box za pośrednictwem protokołu SMB | Microsoft Docs
description: Dowiedz się, jak skopiować dane na urządzenie Azure Data Box za pośrednictwem usługi kopiowania danych
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 01/24/2019
ms.author: alkohli
ms.openlocfilehash: 9d271642a432d8a149fbe468087a0598c91e7c36
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/25/2019
ms.locfileid: "54902383"
---
# <a name="tutorial-use-data-copy-service-to-directly-ingest-data-into-azure-data-box-preview"></a>Samouczek: korzystanie z usługi kopiowania danych w celu bezpośredniego pozyskiwania danych na urządzenie Azure Data Box (wersja zapoznawcza)

Ten samouczek zawiera opis sposobu pozyskiwania danych przy użyciu usługi kopiowania danych bez konieczności używania pośredniczącego hosta. Usługa kopiowania danych działa lokalnie na urządzeniu Data Box, łączy się z urządzeniem NAS za pośrednictwem protokołu SMB i kopiuje dane do urządzenia Data Box.

Z usługi kopiowania danych można korzystać w następujących scenariuszach:

- W środowiskach magazynu dołączonego do sieci (NAS), w których pośredniczące hosty mogą być niedostępne.
- W przypadku korzystania z małych plików, dla których pozyskanie i przekazanie danych trwałoby tygodniami. Ta usługa znacznie skraca czas pozyskiwania i przekazywania danych.

Ten samouczek zawiera informacje dotyczące:

> [!div class="checklist"]
> * Wymagania wstępne
> * Kopiowanie danych na urządzenie Data Box


## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że:

1. Ukończono [Samouczek: konfigurowanie usługi Azure Data Box](data-box-deploy-set-up.md).
2. Urządzenie Data Box zostało do Ciebie dostarczone, a stan zamówienia w portalu to **Dostarczono**.
3. Masz poświadczenia źródłowego urządzenia NAS podłączonego na potrzeby kopiowania danych.
4. Dysponujesz szybkim połączeniem sieciowym. Zdecydowanie zaleca się posiadanie co najmniej jednego połączenia 10 GbE. Jeśli połączenie 10 GbE nie jest dostępne, użyj połączenia danych 1 GbE (będzie to miało negatywny wpływ na szybkość kopiowania).

## <a name="copy-data-to-data-box"></a>Kopiowanie danych na urządzenie Data Box

Po nawiązaniu połączenia z urządzeniem NAS następnym krokiem jest skopiowanie danych. Przed rozpoczęciem kopiowania danych należy uwzględnić następujące kwestie:

- Podczas kopiowania danych upewnij się, że rozmiar danych jest zgodny z ograniczeniami rozmiaru opisanymi w temacie [Limity usługi Azure Storage i urządzenia Data Box](data-box-limits.md).
- Jeśli dane przekazywane przy użyciu urządzenia Data Box będą jednocześnie przekazywane przez inne aplikacje poza urządzeniem Data Box, skutkiem może być niepowodzenie zadania przekazywania oraz uszkodzenie danych.
- Jeśli dane są modyfikowane podczas ich odczytywania przez usługę kopiowania danych, mogą wystąpić niepowodzenia lub uszkodzenie danych.

Aby skopiować dane za pomocą usługi kopiowania danych, należy utworzyć zadanie. Wykonaj następujące kroki, aby utworzyć zadanie kopiowania danych.

1. W lokalnym internetowym interfejsie użytkownika urządzenia Data Box przejdź do pozycji **Zarządzanie > Kopiowanie danych**.
2. Na stronie **Kopiowanie danych** kliknij przycisk **Utwórz**.

    ![Kliknięcie przycisku **Utwórz** na stronie **Kopiowanie danych**](media/data-box-deploy-copy-data-via-copy-service/click-create.png)

3. W oknie dialogowym **Skonfiguruj i uruchom** podaj następujące dane wejściowe.
    
    |Pole                          |Wartość    |
    |-------------------------------|---------|
    |Nazwa zadania                       |Unikatową nazwę zadania składającą się z mniej niż 230 znaków. W nazwach zadań nie wolno używać następujących znaków — \<, \>, \|, \?, \*, \\, \:, \/ ani \\\.         |
    |Lokalizacja źródła                |Podaj ścieżkę SMB do źródła danych w formacie: `\\<ServerIPAddress>\<ShareName>` lub `\\<ServerName>\<ShareName>`.        |
    |Nazwa użytkownika                       |Nazwa użytkownika w formacie `\\<DomainName><UserName>` używana do uzyskiwania dostępu do źródła danych.        |
    |Hasło                       |Hasło używane do uzyskiwania dostępu do źródła danych.           |
    |Docelowe konto magazynu    |Z listy rozwijanej wybierz docelowe konto magazynu, do którego będą przekazywane dane.         |
    |Docelowy typ magazynu       |Wybierz docelowy typ magazynu: blokowy obiekt blob, stronicowy obiekt blob lub Azure Files.        |
    |Docelowy kontener/udział    |Wprowadź nazwę kontenera lub udziału, do którego będą przekazywane dane w ramach docelowego konta magazynu. Może to być nazwa udziału lub kontenera. Na przykład: `myshare` lub `mycontainer`. Nazwę możesz wprowadzić również w formacie `sharename\directory_name` lub `containername\virtual_directory_name` w chmurze.        |
    |Kopiowanie plików pasujących do wzorca    | Wprowadź nazwę pliku pasującą do wzorca na następujące dwa sposoby.<ul><li>**Użycie wyrażenia z symbolami wieloznacznymi** — w wyrażeniach z symbolami wieloznacznymi są obsługiwane tylko znaki `*` i `?`. Na przykład do wyrażenia `*.vhd` będą pasowały wszystkie pliki z rozszerzeniem .vhd. Podobnie do wyrażenia `*.dl?` będą pasowały wszystkie pliki z rozszerzeniami `.dl` i `.dll`. Wyrażenie `*foo` będzie pasowało do wszystkich plików, których nazwy kończą się na `foo`.<br>Wyrażenie z symbolami wieloznacznymi można wprowadzić bezpośrednio w polu. Domyślnie wartość wprowadzona w polu jest traktowana jako wyrażenie z symbolami wieloznacznymi.</li><li>**Użycie wyrażeń regularnych** — obsługiwane są wyrażenia regularne oparte na modelu POSIX. Na przykład wyrażenie regularne `.*\.vhd` będzie zgodne ze wszystkimi plikami z rozszerzeniem `.vhd`. Dla wyrażenia regularnego podaj `<pattern>` bezpośrednio jako `regex(<pattern>)`. <li>Aby uzyskać więcej informacji na temat wyrażeń regularnych, przejdź do [Regular expression language - a quick reference](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference) (Język wyrażeń regularnych — krótki przewodnik).</li><ul>|
    |Optymalizacja pliku              |Po włączeniu tej opcji pliki mniejsze niż 1 MB są pakowane podczas pozyskiwania. Przyspiesza to kopiowania danych w przypadku małych plików. Kiedy liczba plików znacznie przekracza liczbę katalogów, można zauważyć dużą oszczędność czasu.        |
 
4. Kliknij przycisk **Uruchom**. Dane wejściowe są weryfikowane i jeśli ten proces zakończy się pomyślnie, zadanie zostaje uruchomione. Uruchomienie zadania może potrwać kilka minut.

    ![Uruchamianie zadania z poziomu okna dialogowego Skonfiguruj zadanie i uruchom](media/data-box-deploy-copy-data-via-copy-service/configure-and-start.png)

5. Zadanie zostanie utworzone przy użyciu wybranych ustawień. Zaznacz pole wyboru, aby móc wstrzymywać, wznawiać, anulować lub ponownie uruchamiać zadanie.

    ![Zarządzanie zadaniem za pośrednictwem strony kopiowania danych](media/data-box-deploy-copy-data-via-copy-service/select-job.png)
    
    - To zadanie można wstrzymać, jeśli ma ono wpływ na zasoby urządzenia NAS w godzinach szczytu.

        ![Wstrzymywanie zadania](media/data-box-deploy-copy-data-via-copy-service/pause-job.png)

        Zadanie można wznowić później, kiedy miną godziny szczytu.

        ![Wznawianie zadania](media/data-box-deploy-copy-data-via-copy-service/resume-job.png)

    - Zadanie można anulować w dowolnym momencie.

        ![Anulowanie zadania](media/data-box-deploy-copy-data-via-copy-service/cancel-job.png) Anulowanie zadania musi zostać potwierdzone.

        ![Potwierdzanie anulowania zadania](media/data-box-deploy-copy-data-via-copy-service/confirm-cancel-job.png)

        Jeśli zdecydujesz się na anulowanie zadania, skopiowane już dane nie zostaną usunięte. Aby usunąć dane skopiowane na urządzenie Data Box, należy je zresetować.

        ![Resetowanie urządzenia](media/data-box-deploy-copy-data-via-copy-service/reset-device.png)

        >[!NOTE]
        > Jeśli anulujesz lub wstrzymasz zadanie, kopiowane właśnie duże pliki mogą pozostać w stanie częściowo skopiowanym. Te pliki zostaną w takim stanie przekazane na platformę Azure. Podczas próby anulowania lub wstrzymania zadania upewnij się, że pliki zostały prawidłowo skopiowane. Aby zweryfikować pliki, sprawdź udziały SMB lub pobierz plik BOM.

    - Zadanie można ponownie uruchomić, jeśli wystąpiło nagłe niepowodzenie wywołane przejściowym błędem, na przykład zakłóceniem w sieci. Zadania nie można ponownie uruchomić, jeśli osiągnęło ono stan końcowy, na przykład zostało zakończone pomyślnie lub z błędami. Niepowodzenia mogą mieć związek z problemami dotyczącymi nazw plików lub ich rozmiaru. Te błędy są rejestrowane, ale zadania nie można ponownie uruchomić po jego zakończeniu.

        ![Ponowne uruchamianie zadania zakończonego niepowodzeniem](media/data-box-deploy-copy-data-via-copy-service/restart-failed-job.png)

        Jeśli wystąpi niepowodzenie i zadania nie można ponownie uruchomić, pobierz dzienniki błędów i poszukaj niepowodzenia w plikach dziennika. Po rozwiązaniu problemu możesz utworzyć nowe zadanie kopiowania plików. Możesz również [skopiować pliki przy użyciu protokołu SMB](data-box-deploy-copy-data.md).
    
    - W tej wersji nie można usunąć zadania.
    
    - Można utworzyć nieograniczoną liczbę zadań, ale równolegle może działać maksymalnie 10 zadań jednocześnie.
    - Jeśli włączono optymalizację plików, małe pliki są pakowane podczas pozyskiwania w celu zwiększenia wydajności kopiowania. W tych przypadkach widoczny będzie spakowany plik (z nazwą w postaci identyfikatora GUID). Nie należy usuwać tego pliku, ponieważ zostanie on rozpakowany podczas przekazywania.

6. Podczas przetwarzania zadania na stronie **Kopiowanie danych** można zauważyć następujące elementy:

    - W kolumnie **Stan** można wyświetlić stan zadania kopiowania. Możliwe stany to:
        - **Uruchomiono**
        - **Niepowodzenie**
        - **Powodzenie**
        - **Wstrzymywanie**
        - **Wstrzymano**
        - **Anulowanie**
        - **Anulowano**
        - **Zakończone z błędami**
    - W kolumnie **Pliki** można zobaczyć liczbę kopiowanych plików i ich całkowity rozmiar.
    - W kolumnie **Przetworzone** można zobaczyć liczbę i rozmiar przetworzonych plików.
    - W kolumnie **Szczegóły** kliknij przycisk **Wyświetl**, aby zobaczyć szczegóły zadania.
    - Jeśli podczas kopiowania wystąpiły jakieś błędy (widoczne w kolumnie **# Błędy**), przejdź do kolumny **Dziennik błędów** i pobierz dzienniki błędów pomocne przy rozwiązywaniu problemów.

Poczekaj na zakończenie zadań kopiowania. Ponieważ niektóre błędy są rejestrowane tylko na stronie **Połącz i skopiuj**, przed przejściem do następnego kroku upewnij się, że zadania kopiowania zostały zakończone bez błędów.

![Brak błędów na stronie **Połącz i skopiuj**](media/data-box-deploy-copy-data-via-copy-service/verify-no-errors-on-connect-and-copy.png)

W celu zapewnienia integralności danych podczas kopiowania obliczana jest suma kontrolna. Po zakończeniu kopiowania sprawdź ilość używanego i wolnego miejsca na urządzeniu.
    
![Sprawdzanie wolnego i używanego miejsca na pulpicie nawigacyjnym](media/data-box-deploy-copy-data-via-copy-service/verify-used-space-dashboard.png)

Po zakończeniu zadania kopiowania możesz przejść do sekcji **Przygotowanie do wysłania**.

>[!NOTE]
> Przygotowanie do wysłania nie będzie działać, jeśli zadania kopiowania są w toku.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono zagadnienia dotyczące usługi Azure Data Box, takie jak:

> [!div class="checklist"]
> * Wymagania wstępne
> * Kopiowanie danych na urządzenie Data Box


Przejdź do następnego samouczka, aby dowiedzieć się, jak odesłać urządzenie Data Box do firmy Microsoft.

> [!div class="nextstepaction"]
> [Wysyłka urządzenia Azure Data Box do firmy Microsoft](./data-box-deploy-picked-up.md)

