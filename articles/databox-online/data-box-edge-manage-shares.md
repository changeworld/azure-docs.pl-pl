---
title: Zarządzanie udziałami w usłudze Azure krawędź pola danych | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak zarządzać udziałami na krawędzi sieci Azure Data Box za pomocą witryny Azure portal.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/25/2019
ms.author: alkohli
ms.openlocfilehash: 73bff460db8428332a92d8deb68bf062ca4134ae
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60759189"
---
# <a name="use-the-azure-portal-to-manage-shares-on-your-azure-data-box-edge"></a>Zarządzanie udziałami na krawędzi sieci Azure Data Box za pomocą witryny Azure portal

W tym artykule opisano sposób zarządzania udziałami na krawędzi sieci Azure Data Box. Można zarządzać krawędź pola danych platformy Azure za pośrednictwem witryny Azure portal lub za pomocą lokalnego interfejsu użytkownika sieci web. Witryna Azure portal umożliwia dodawanie, usuwanie, Odśwież udziałów lub synchronizacji klucz magazynu dla konta magazynu skojarzonego z udziałów.

## <a name="about-shares"></a>Udziały — informacje

Transfer danych do platformy Azure, musisz utworzyć udziały na krawędzi sieci Azure Data Box. Udziałów, które możesz dodać na urządzeniu usługi Edge pole danych może być lokalne udziały lub akcji wykonywanych w celu wypychania danych do chmury.

 - **Lokalne udziały**: Użyj tych udziałów, gdy dane, które mają być przetwarzane lokalnie na urządzeniu.
 - **Udziały**: Użyj tych udziałów, gdy dane urządzenie ma automatycznie zostać wypchnięty na koncie magazynu w chmurze. Wszystkie chmury funkcje takie jak **Odśwież** i **Synchronizuj klucze magazynu** zastosować do akcji.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Dodawanie udziału
> * Usuwanie udziału
> * Odświeżanie udziałów
> * Synchronizowanie klucza magazynu


## <a name="add-a-share"></a>Dodawanie udziału

Wykonaj następujące czynności w witrynie Azure Portal, aby utworzyć udział.

1. W witrynie Azure portal przejdź do zasobu krawędź pola danych, a następnie przejdź do **bramy > udziałów**. Wybierz **+ Dodaj udział** na pasku poleceń.

    ![Wybierz opcję Dodaj udział](media/data-box-edge-manage-shares/add-share-1.png)

2. W obszarze **Dodawanie udziału** określ ustawienia udziału. Podaj unikatową nazwę udziału.
    
    Nazwy udziałów mogą zawierać tylko cyfry, małe litery i łączniki. Nazwa udziału musi mieć długość od 3 do 63 znaków i rozpoczynać się literą lub cyfrą. Przed i za każdym łącznikiem musi znajdować się znak inny niż łącznik.

3. Wybierz **Typ** dla udziału. Dostępne są dwa typy — **SMB** i **NFS** — przy czym typ SMB jest domyślny. Typ SMB jest standardem dla klientów systemu Windows, natomiast typ NFS jest używany dla klientów systemu Linux. W zależności od tego, czy wybrano udział SMB czy NFS, wyświetlone opcje są nieco inne.

4. Podaj **konto magazynu**, w którym znajduje się udział. Na koncie magazynu zostanie utworzony kontener z nazwą udziału (jeśli jeszcze nie istnieje). Jeśli kontener już istnieje, zostanie użyty istniejący kontener.

5. Z listy rozwijanej wybierz **usługi Storage** z blokowych obiektów blob, stronicowych obiektów blob lub plików. Wybrany typ usługi zależy od tego, jaki format mają mieć dane na platformie Azure. Na przykład, w tym przykładzie chcemy, aby dane znajdują się jako blokowe obiekty BLOB na platformie Azure, dlatego wybieramy **blokowych obiektów Blob**. Jeśli wybranie **stronicowych obiektów Blob**, należy upewnić się, że Twoje dane są 512 bajtów wyrównane. Użyj **stronicowych obiektów blob** dla dysków VHD lub VHDX, które są zawsze 512 bajtów wyrównane.

6. Ten krok zależy od tego, czy tworzysz udział SMB, czy udział NFS.
    - **W przypadku tworzenia udziału SMB** — w polu **Użytkownik lokalny ze wszystkimi uprawnieniami** wybierz pozycję **Utwórz nowy** lub **Użyj istniejącego**. W przypadku tworzenia nowego użytkownika lokalnego podaj **nazwę użytkownika**, **hasło**, a następnie potwierdź hasło. Spowoduje to przypisanie uprawnień do użytkownika lokalnego. Po przypisaniu uprawnień w tym miejscu możesz następnie zmodyfikować te uprawnienia przy użyciu Eksploratora plików.

        ![Dodawanie udziału SMB](media/data-box-edge-manage-shares/add-smb-share.png)

        Jeśli zaznaczysz opcję Zezwalaj tylko na operacje odczytu dla tego udziału danych, możesz określić użytkowników z uprawnieniami tylko do odczytu.
    - **W przypadku tworzenia udziału NFS** — należy podać **adresy IP dozwolonych klientów**, którzy mogą uzyskiwać dostęp do udziału.

        ![Dodawanie udziału NFS](media/data-box-edge-manage-shares/add-nfs-share.png)

7. Aby łatwo uzyskiwać dostęp do udziałów z modułów obliczeń brzegowych, użyj punktu instalacji lokalnej. Wybierz **udziału za pomocą obliczeń brzegowych** tak, aby udział jest instalowane automatycznie po jego utworzeniu. Gdy ta opcja jest zaznaczona, moduł usługi Edge umożliwia również zasoby obliczeniowe z punktem instalacji lokalnej.

8. Kliknij pozycję **Utwórz**, aby utworzyć udział. Zostanie wyświetlone powiadomienie, że trwa tworzenie udziału. Po utworzeniu udziału z określonymi ustawieniami blok **Udziały** zostanie zaktualizowany, aby odzwierciedlić nowy udział.

## <a name="add-a-local-share"></a>Dodawanie udziału lokalnego

1. W witrynie Azure portal przejdź do zasobu krawędź pola danych, a następnie przejdź do **bramy > udziałów**. Wybierz **+ Dodaj udział** na pasku poleceń.

    ![Wybierz opcję Dodaj udział](media/data-box-edge-manage-shares/add-local-share-1.png)

2. W obszarze **Dodawanie udziału** określ ustawienia udziału. Podaj unikatową nazwę udziału.
    
    Nazwy udziałów mogą zawierać tylko cyfry, małe litery i łączniki. Nazwa udziału musi mieć długość od 3 do 63 znaków i rozpoczynać się literą lub cyfrą. Przed i za każdym łącznikiem musi znajdować się znak inny niż łącznik.

3. Wybierz **Typ** dla udziału. Dostępne są dwa typy — **SMB** i **NFS** — przy czym typ SMB jest domyślny. Typ SMB jest standardem dla klientów systemu Windows, natomiast typ NFS jest używany dla klientów systemu Linux. W zależności od tego, czy wybrano udział SMB czy NFS, wyświetlone opcje są nieco inne.

4. Aby łatwo uzyskiwać dostęp do udziałów z modułów obliczeń brzegowych, użyj punktu instalacji lokalnej. Wybierz **udziału za pomocą obliczeń brzegowych** tak, aby moduł krawędzi za pomocą obliczeń punktu instalacji lokalnej.

5. Wybierz **skonfiguruj jako udziałów lokalnych krawędzi**. Dane w lokalnych udziałach będą przechowywane lokalnie na urządzeniu. Może przetwarzać te dane lokalnie.

6. W **wszystkich użytkowników lokalnych uprawnień** wybierz opcję z **Utwórz nową** lub **Użyj istniejącej**.

7. Wybierz pozycję **Utwórz**. 

    ![Tworzenie udziału lokalnego](media/data-box-edge-manage-shares/add-local-share-2.png)

    Zostanie wyświetlone powiadomienie, że trwa tworzenie udziału. Po utworzeniu udziału z określonymi ustawieniami blok **Udziały** zostanie zaktualizowany, aby odzwierciedlić nowy udział.

    ![Wyświetl aktualizacje udziałów bloku](media/data-box-edge-manage-shares/add-local-share-3.png)
    
    Wybierz udział, aby wyświetlić lokalnego punktu instalacji dla modułów obliczeń brzegowych dla tego udziału.

    ![Wyświetl szczegóły udziału lokalnego](media/data-box-edge-manage-shares/add-local-share-4.png)

## <a name="mount-a-share"></a>Instalowanie udziałów usługi

Jeśli utworzono udział, przed skonfigurowaniem obliczeń na urządzeniu usługi Edge pole danych, należy zainstalować ten udział. Wykonaj następujące kroki, aby zainstalować udział.


1. W witrynie Azure portal przejdź do zasobu krawędź pola danych, a następnie przejdź do **bramy > udziałów**. Z listy akcji wybierz udział, który chcesz zainstalować. **Używane do obliczeń** kolumna pojawi się stan jako **wyłączone** dla wybranego udziału.

    ![Wybieranie udziału](media/data-box-edge-manage-shares/select-share-mount.png)

2. Wybierz **instalacji**.

    ![Wybierz opcję instalacji](media/data-box-edge-manage-shares/select-mount.png)

3. Po wyświetleniu monitu o potwierdzenie, wybierz **tak**. To będzie zainstalować ten udział.

    ![Potwierdzenie instalacji](media/data-box-edge-manage-shares/confirm-mount.png)

4. Po udział jest zainstalowany, przejdź do listy udziałów. Zobaczysz, że **używane do obliczeń** kolumna pokazuje stan udziału jako **włączone**.

    ![Zainstalowany udział](media/data-box-edge-manage-shares/share-mounted.png)

5. Wybierz udział ponownie, aby wyświetlić lokalnego punktu instalacji dla udziału. Krawędź obliczeń używa modułu to lokalnego punktu instalacji dla udziału.

    ![Lokalny punkt instalacji udziału](media/data-box-edge-manage-shares/share-mountpoint.png)

## <a name="unmount-a-share"></a>Odinstaluj udział

Wykonaj następujące czynności w witrynie Azure portal, aby odinstalować udziału.

1. W witrynie Azure portal przejdź do zasobu krawędź pola danych, a następnie przejdź do **bramy > udziałów**.

    ![Wybieranie udziału](media/data-box-edge-manage-shares/select-share-unmount.png)

2. Z listy akcji wybierz udziału, do którego chcesz odinstalować. Chcesz upewnić się, czy udział, który możesz odinstalować nie jest używany przez wszystkie moduły. Jeśli udział jest używany przez moduł, zostanie wyświetlone problemy związane z odpowiedniego modułu. Wybierz **Odinstaluj**.

    ![Wybierz pozycję Odinstaluj](media/data-box-edge-manage-shares/select-unmount.png)

3. Po wyświetleniu monitu o potwierdzenie, wybierz **tak**. Odinstaluj obraz udziału.

    ![Upewnij się, odinstaluj](media/data-box-edge-manage-shares/confirm-unmount.png)

4. Gdy udział zostanie odinstalowane, przejdź do listy udziałów. Zobaczysz, że **używane do obliczeń** kolumna pokazuje stan udziału jako **wyłączone**.

    ![Odinstalować udziału](media/data-box-edge-manage-shares/share-unmounted.png)

## <a name="delete-a-share"></a>Usuwanie udziału

Wykonaj następujące czynności w witrynie Azure Portal, aby usunąć udział.

1. Na liście udziałów kliknij udział, który chcesz usunąć.

    ![Wybieranie udziału](media/data-box-edge-manage-shares/delete-share-1.png)

2. Kliknij polecenie **Usuń**.

    ![Klikanie polecenia Usuń](media/data-box-edge-manage-shares/delete-share-2.png)

3. Po wyświetleniu monitu o potwierdzenie kliknij przycisk **Tak**.

    ![Potwierdzenie usunięcia](media/data-box-edge-manage-shares/delete-share-3.png)

Lista aktualizacji akcji w celu odzwierciedlenia usunięcia.


## <a name="refresh-shares"></a>Odświeżanie udziałów

Funkcja odświeżania umożliwia odświeżanie zawartości udziału. Podczas odświeżania udziału inicjowane jest wyszukiwanie w celu odnalezienia wszystkich obiektów platformy Azure, w tym obiektów blob i plików, które zostały dodane do chmury po ostatnio przeprowadzonym odświeżaniu. Te dodatkowe pliki zostaną następnie pobrane Odśwież zawartość udziału na urządzeniu.

> [!IMPORTANT]
> - Nie można odświeżyć udziałów lokalnych.
> - Uprawnienia i listy kontroli dostępu (ACL) nie są zachowywane w ramach operacji odświeżania. 

Wykonaj następujące czynności w witrynie Azure Portal, aby odświeżyć udział.

1.  W witrynie Azure Portal przejdź do sekcji **Udziały**. Kliknij udział, który chcesz odświeżyć.

    ![Wybieranie udziału](media/data-box-edge-manage-shares/refresh-share-1.png)

2.  Kliknij przycisk **Odśwież**. 

    ![Klikanie przycisku Odśwież](media/data-box-edge-manage-shares/refresh-share-2.png)
 
3.  Po wyświetleniu monitu o potwierdzenie kliknij przycisk **Tak**. Zadanie rozpoczyna odświeżanie zawartości udziału lokalnego.

    ![Potwierdzanie odświeżania](media/data-box-edge-manage-shares/refresh-share-3.png)
 
4.  W trakcie odświeżania opcja odświeżania jest wyszarzona w menu kontekstowym. Kliknij powiadomienie o zadaniu, aby wyświetlić stan zadania odświeżania.

5.  Czas odświeżania zależy od liczby plików w kontenerze platformy Azure, a także od liczby plików znajdujących się na urządzeniu. Po pomyślnym zakończeniu odświeżania znacznik czasu udziału zostanie zaktualizowany. Nawet w przypadku częściowego niepowodzenia odświeżania operacja zostanie uznana za zakończoną, a znacznik czasu zostanie zaktualizowany. Dzienniki błędów odświeżania również są aktualizowane.

    ![Zaktualizowany znacznik czasu](media/data-box-edge-manage-shares/refresh-share-4.png)
 
W przypadku awarii zostanie zgłoszony alert. Alert zawiera szczegółowe informacje o przyczynie awarii oraz zalecenia dotyczące rozwiązania problemu. Alert wskazuje również plik, który zawiera pełne podsumowanie awarii — w tym pliki, których nie udało się zaktualizować lub usunąć.


## <a name="sync-storage-keys"></a>Synchronizowanie kluczy magazynu

Jeśli klucze konta magazynu zostały wymienione, należy zsynchronizować klucze dostępu do magazynu. Przeprowadzenie synchronizacji pozwala urządzeniu uzyskać najnowsze klucze do konta magazynu.

Wykonaj następujące czynności w witrynie Azure Portal, aby zsynchronizować klucz dostępu do magazynu.

1. W zasobie przejdź do sekcji **Przegląd**. Na liście udziałów kliknij udział skojarzony z kontem magazynu, które ma zostać zsynchronizowane.

    ![Wybierz udział z kontem magazynu odpowiednie](media/data-box-edge-manage-shares/sync-storage-key-1.png)

2. Kliknij przycisk **Synchronizuj klucz magazynu**. Po wyświetleniu monitu o potwierdzenie kliknij przycisk **Tak**.

     ![Wybierz klucz magazynu synchronizacji](media/data-box-edge-manage-shares/sync-storage-key-2.png)

3. Po zakończeniu synchronizacji zamknij okno dialogowe.

>[!NOTE]
> Wystarczy zrobić to raz dla danego konta magazynu. Nie trzeba powtarzać tej czynności dla wszystkich udziałów powiązanych z tym samym kontem magazynu.


## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak [Zarządzać użytkownikami w witrynie Azure Portal](data-box-edge-manage-users.md).
