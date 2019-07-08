---
title: Samouczek, aby skopiować dane do urządzenia Azure Data Box mocno za pomocą usługi kopiowania danych | Dokumentacja firmy Microsoft
description: W tym samouczku dowiesz się, jak skopiować dane do Twojego urządzenia Azure Data Box mocno za pomocą usługi kopiowania danych
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: adc28ea5f74ad16d4387d246ef73618a53ea26e1
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595756"
---
# <a name="tutorial-use-the-data-copy-service-to-copy-data-into-azure-data-box-heavy-preview"></a>Samouczek: Kopiowanie danych do usługi Azure Data Box duże (wersja zapoznawcza) za pomocą usługi kopiowania danych

W tym samouczku opisano sposób pozyskiwania danych przy użyciu usługi kopiowania danych bez hosta pośredniego. Usługa kopiowania danych działa lokalnie na Azure Data Box duże, łączy się urządzenie magazynu dołączone do sieci (NAS) za pośrednictwem protokołu SMB i kopiuje dane do duże pole danych.

Z usługi kopiowania danych można korzystać w następujących scenariuszach:

- W środowiskach NAS, gdzie hosty pośrednie mogą być niedostępne.
- W przypadku korzystania z małych plików, dla których pozyskanie i przekazanie danych trwałoby tygodniami. Usługa kopiowania danych znacznie skraca czas pozyskiwania i przekazywania danych w przypadku małych plików.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Kopiowanie danych do duże pole danych

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że:

1. Ukończono następujący samouczek: [Konfigurowanie Azure Data Box mocno](data-box-heavy-deploy-set-up.md).
2. Otrzymał swoje duże pole danych, a stan zamówienia w portalu jest **dostarczone**.
3. Masz poświadczenia źródłowego urządzenia NAS, z którym połączysz się na potrzeby kopiowania danych.
4. Dysponujesz szybkim połączeniem sieciowym. Najszybszy szybkości kopiowania dwa połączenia 40 GbE (po jednej na węzeł) możesz wykorzystać równolegle. Jeśli nie ma dostępnego połączenia 40 GbE, zaleca się, że masz co najmniej dwa połączenia 10 GbE (po jednej na węzeł). 

## <a name="copy-data-to-data-box-heavy"></a>Kopiowanie danych do duże pole danych

Po nawiązaniu połączenia z urządzeniem NAS następnym krokiem jest skopiowanie danych. Przed rozpoczęciem kopiowania danych należy uwzględnić następujące kwestie:

- Podczas kopiowania danych, upewnij się, że rozmiar danych jest zgodny ze limity rozmiaru opisaną w artykule [usługi Azure storage i dane pole duże limity](data-box-heavy-limits.md).
- Jeśli dane przekazane przez duże pole danych jednocześnie jest przekazywany przez inne aplikacje poza duże pole danych, może spowodować błędy przekazywania zadania i uszkodzenie danych.
- Jeśli dane są modyfikowane podczas ich odczytywania przez usługę kopiowania danych, mogą wystąpić niepowodzenia lub uszkodzenie danych.

Aby skopiować dane za pomocą usługi kopiowania danych, należy utworzyć zadanie:

1. W przypadku lokalnego Interfejsu w przeglądarce urządzenia duże pole danych, przejdź do **Zarządzaj** > **kopiowanie danych**.
2. Na stronie **Kopiowanie danych** wybierz pozycję **Utwórz**.

    ![Wybierz pozycję Utwórz na stronie „Kopiowanie danych”](media/data-box-deploy-copy-data-via-copy-service/click-create.png)

3. W oknie dialogowym **Skonfiguruj zadanie i uruchom**  wypełnij następujące pola:
    
    |Pole                          |Wartość    |
    |-------------------------------|---------|
    |**Nazwa zadania**                       |Unikatową nazwę zadania składającą się z mniej niż 230 znaków. W nazwach zadań nie wolno używać następujących znaków: \<, \>, \|, \?, \*, \\, \:, \/ i \\\.         |
    |**Lokalizacja źródłowa**                |Podaj ścieżkę SMB do źródła danych w formacie: `\\<ServerIPAddress>\<ShareName>` lub `\\<ServerName>\<ShareName>`.        |
    |**Nazwa użytkownika**                       |Nazwa użytkownika w formacie `\\<DomainName><UserName>` używana do uzyskiwania dostępu do źródła danych. Jeśli połączenie jest kontem lokalnego administratora, potrzebują uprawnień zabezpieczeń jawne. Kliknij prawym przyciskiem myszy folder, wybierz **właściwości** , a następnie wybierz **zabezpieczeń**. Należy to dodawać administratora lokalnego w **zabezpieczeń** kartę.       |
    |**Hasło**                       |Hasło używane do uzyskiwania dostępu do źródła danych.           |
    |**Docelowe konto magazynu**    |Z listy wybierz docelowe konto magazynu, do którego będą przekazywane dane.         |
    |**Typ docelowy**       |Z listy wybierz typ magazynu docelowego: **Blokowy obiekt blob**, **Stronicowy obiekt blob** lub usługa **Azure Files**.        |
    |**Docelowy kontener/udział**    |Wprowadź nazwę kontenera lub udziału, do którego chcesz przekazać dane w ramach docelowego konta magazynu. Może to być nazwa udziału lub kontenera. Użyj na przykład nazwy `myshare` lub `mycontainer`. Nazwę możesz również wprowadzić w formacie `sharename\directory_name` lub `containername\virtual_directory_name`.        |
    |**Skopiuj pliki pasujące do wzorca**    | Nazwę pliku pasującą do wzorca możesz wprowadzić na następujące dwa sposoby:<ul><li>**Użycie wyrażeń wieloznacznych:** W wyrażeniach wieloznacznych są obsługiwane tylko znaki `*` i `?`. Na przykład do wyrażenia `*.vhd` będą pasowały wszystkie pliki z rozszerzeniem `.vhd`. Podobnie do wyrażenia `*.dl?` będą pasowały wszystkie pliki z rozszerzeniem `.dl` i zaczynające się od `.dl`, na przykład `.dll`. Wyrażenie `*foo` będzie pasowało do wszystkich plików, których nazwy kończą się na `foo`.<br>Wyrażenie wieloznaczne można wprowadzić bezpośrednio w polu. Domyślnie wartość wprowadzona w polu jest traktowana jako wyrażenie wieloznaczne.</li><li>**Użycie wyrażeń regularnych:** Obsługiwane są wyrażenia regularne oparte na modelu POSIX. Na przykład wyrażenie regularne `.*\.vhd` będzie zgodne ze wszystkimi plikami z rozszerzeniem `.vhd`. Dla wyrażeń regularnych podaj `<pattern>` bezpośrednio jako `regex(<pattern>)`. Aby uzyskać więcej informacji na temat wyrażeń regularnych, przejdź do tematu [Język wyrażeń regularnych — krótki przewodnik](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference).</li><ul>|
    |**Optymalizacja pliku**              |Kiedy ta funkcja jest włączona, pliki mniejsze niż 1 MB są pakowane podczas pozyskiwania. Przyspiesza to kopiowanie danych w przypadku małych plików. Pozwala to także zaoszczędzić czas, gdy liczba plików znacznie przekracza liczbę katalogów.        |
 
4. Wybierz pozycję **Uruchom**. Dane wejściowe są weryfikowane i jeśli ten proces zakończy się pomyślnie, zadanie jest uruchamiane. Uruchomienie zadania może potrwać kilka minut.

    ![Uruchamianie zadania z poziomu okna dialogowego „Skonfiguruj zadanie i uruchom”](media/data-box-deploy-copy-data-via-copy-service/configure-and-start.png)

5. Zadanie zostanie utworzone przy użyciu wybranych ustawień. Zadanie można wstrzymać, wznowić, anulować i uruchomić ponownie. Zaznacz pole wyboru obok nazwy zadania, a następnie wybierz odpowiedni przycisk.

    ![Zarządzanie zadaniem na stronie „Kopiowanie danych”](media/data-box-deploy-copy-data-via-copy-service/select-job.png)
    
    - Można wstrzymać zadanie, jeśli ma ono wpływ na zasoby urządzenia NAS w godzinach szczytu:

        ![Wstrzymywanie zadania na stronie „Kopiowanie danych”](media/data-box-deploy-copy-data-via-copy-service/pause-job.png)

        Zadanie można wznowić później, kiedy miną godziny szczytu:

        ![Wznawianie zadania na stronie „Kopiowanie danych”](media/data-box-deploy-copy-data-via-copy-service/resume-job.png)

    - Zadanie można anulować w dowolnym momencie:

        ![Anulowanie zadania na stronie „Kopiowanie danych”](media/data-box-deploy-copy-data-via-copy-service/cancel-job.png)
        
        Anulowanie zadania musi zostać potwierdzone:

        ![Potwierdzanie anulowania zadania](media/data-box-deploy-copy-data-via-copy-service/confirm-cancel-job.png)

        Jeśli zdecydujesz się na anulowanie zadania, skopiowane już dane nie zostaną usunięte. Aby usunąć dane skopiowane na urządzenie Data Box, należy zresetować urządzenie.

        ![Resetowanie urządzenia](media/data-box-deploy-copy-data-via-copy-service/reset-device.png)

        >[!NOTE]
        > Jeśli anulujesz lub wstrzymasz zadanie, duże pliki mogą być tylko częściowo skopiowane. Te częściowo skopiowane pliki są przekazywane w takim stanie na platformę Azure. Kiedy anulujesz lub wstrzymujesz zadanie, upewnij się, że Twoje pliki zostały skopiowane poprawnie. Aby zweryfikować pliki, sprawdź udziały SMB lub pobierz plik BOM.

    - Zadanie można uruchomić ponownie, jeśli wystąpiło niepowodzenie wywołane przejściowym błędem, na przykład zakłóceniem w sieci. Zadania nie można jednak uruchomić ponownie, jeśli osiągnęło ono stan końcowy, taki jak **Powodzenie** lub **Ukończone z błędami**. Niepowodzenia zadań mogą być spowodowane problemami z nazwami lub rozmiarami plików. Te błędy są rejestrowane, ale zadania nie można uruchomić ponownie, jeśli zostało zakończone.

        ![Ponowne uruchamianie zadania zakończonego niepowodzeniem](media/data-box-deploy-copy-data-via-copy-service/restart-failed-job.png)

        Jeśli wystąpi niepowodzenie i zadania nie można uruchomić ponownie, pobierz dzienniki błędów i poszukaj niepowodzenia w plikach dziennika. Po rozwiązaniu problemu utwórz nowe zadanie kopiowania plików. Możesz również [skopiować pliki przy użyciu protokołu SMB](data-box-deploy-copy-data.md).
    
    - W tej wersji nie można usunąć zadania.
    
    - Można utworzyć nieograniczoną liczbę zadań, ale równolegle można uruchomić maksymalnie 10 zadań jednocześnie.
    - Jeśli włączono opcję **Optymalizacja plików**, małe pliki są pakowane podczas pozyskiwania w celu zwiększenia wydajności kopiowania. W takiej sytuacji zobaczysz spakowany plik (jego nazwą będzie identyfikator GUID). Nie usuwaj tego pliku. Zostanie on rozpakowany podczas przekazywania.

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
    - W kolumnie **Przetworzone** można zobaczyć liczbę i całkowity rozmiar przetworzonych plików.
    - W kolumnie **Szczegóły zadania** wybierz pozycję **Wyświetl**, aby wyświetlić szczegóły zadania.
    - Jeśli podczas kopiowania wystąpią jakieś błędy (widoczne w kolumnie **# Błędy**), przejdź do kolumny **Dziennik błędów** i pobierz dzienniki błędów w celu rozwiązania problemów.

Poczekaj na zakończenie zadania kopiowania. Ponieważ niektóre błędy są rejestrowane tylko na stronie **Połącz i skopiuj**, przed przejściem do następnego kroku upewnij się, że zadanie kopiowanie zostało zakończone bez błędów.

![Brak błędów na stronie „Połącz i skopiuj”](media/data-box-deploy-copy-data-via-copy-service/verify-no-errors-on-connect-and-copy.png)

W celu zapewnienia integralności danych podczas kopiowania obliczana jest suma kontrolna. Po zakończeniu kopiowania wybierz pozycję **Wyświetl pulpit nawigacyjny**, aby zweryfikować użyte miejsce i wolne miejsce na urządzeniu.
    
![Sprawdzanie wolnego i używanego miejsca na pulpicie nawigacyjnym](media/data-box-deploy-copy-data-via-copy-service/verify-used-space-dashboard.png)

> [!IMPORTANT]
> Powtórz tych samych instrukcji, aby skopiować dane do drugiego węzła na duże pole danych.

Po zakończeniu zadania kopiowania możesz wybrać pozycję **Przygotuj do wysłania**.

>[!NOTE]
> Opcja **Przygotuj do wysłania** nie działa, jeśli zadania kopiowania są w toku.

## <a name="next-steps"></a>Kolejne kroki

Przejdź do następnego samouczka, aby dowiedzieć się, jak dostarczanie duże pole danych urządzenia do firmy Microsoft.

> [!div class="nextstepaction"]
> [Wysłania urządzenia Azure Data Box mocno do firmy Microsoft](./data-box-heavy-deploy-picked-up.md)

