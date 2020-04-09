---
title: Zarządzanie udziałami w usłudze Azure Data Box Edge | Dokumenty firmy Microsoft
description: W tym artykule opisano, jak używać witryny Azure Portal do zarządzania udziałami w usłudze Azure Data Box Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/25/2019
ms.author: alkohli
ms.openlocfilehash: 4877b136f197f226f142f5ad5eb4035c63c07d7a
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80887014"
---
# <a name="use-the-azure-portal-to-manage-shares-on-your-azure-data-box-edge"></a>Zarządzanie udziałami w witrynie Azure Data Box Edge za pomocą witryny Azure Portal

W tym artykule opisano sposób zarządzania udziałami w usłudze Azure Data Box Edge. Usługę Azure Data Box Edge można zarządzać za pośrednictwem witryny Azure portal lub lokalnego interfejsu użytkownika sieci Web. Użyj witryny Azure Portal, aby dodać, usunąć, odświeżyć udziały lub zsynchronizować klucz magazynu dla konta magazynu skojarzonego z udziałami.

## <a name="about-shares"></a>Udziały — informacje

Aby przenieść dane na platformę Azure, musisz utworzyć udziały w usłudze Azure Data Box Edge. Udziały dodawanye na urządzeniu data box edge mogą być udziałami lokalnymi lub udziałami wypychaniem danych do chmury.

 - **Udziały lokalne:** Użyj tych udziałów, jeśli chcesz, aby dane były przetwarzane lokalnie na urządzeniu.
 - **Udostępnienia:** Użyj tych udziałów, jeśli chcesz, aby dane urządzenia były automatycznie wypychane na konto magazynu w chmurze. Wszystkie funkcje chmury, takie jak **Odśwież** i **Synchronizuj klucze magazynu** mają zastosowanie do udziałów.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
>
> * Dodawanie udziału
> * Usuwanie udziału
> * Odświeżanie udziałów
> * Synchronizowanie klucza magazynu

## <a name="add-a-share"></a>Dodawanie udziału

Wykonaj następujące czynności w witrynie Azure Portal, aby utworzyć udział.

1. W witrynie Azure portal przejdź do zasobu usługi Data Box Edge, a następnie przejdź do **udziału > bramy.** Wybierz **+ Dodaj udział** na pasku poleceń.

    ![Wybierz dodaj udział](media/data-box-edge-manage-shares/add-share-1.png)

2. W obszarze **Dodawanie udziału** określ ustawienia udziału. Podaj unikatową nazwę udziału.
    
    Nazwy udziałów mogą zawierać tylko cyfry, małe litery i łączniki. Nazwa udziału musi mieć długość od 3 do 63 znaków i rozpoczynać się literą lub cyfrą. Przed i za każdym łącznikiem musi znajdować się znak inny niż łącznik.

3. Wybierz **Typ** dla udziału. Dostępne są dwa typy — **SMB** i **NFS** — przy czym typ SMB jest domyślny. Typ SMB jest standardem dla klientów systemu Windows, natomiast typ NFS jest używany dla klientów systemu Linux. W zależności od tego, czy wybrano udział SMB czy NFS, wyświetlone opcje są nieco inne.

4. Podaj **konto magazynu**, w którym znajduje się udział. Na koncie magazynu zostanie utworzony kontener z nazwą udziału (jeśli jeszcze nie istnieje). Jeśli kontener już istnieje, zostanie użyty istniejący kontener.

5. Z listy rozwijanej wybierz **usługę Magazynowanie** z bloku obiektu blob, strony blob lub plików. Wybrany typ usługi zależy od tego, jaki format mają mieć dane na platformie Azure. Na przykład w tym przypadku chcemy, aby dane były rezydować jako blokowe obiekty blob na platformie Azure, dlatego wybierzmy **blokowy obiekt blob**. W przypadku **wybrania opcji Page Blob**należy upewnić się, że dane są wyrównane do 512 bajtów. Użyj **stronicowego obiektu blob** dla dysków VHD lub VHDX, które są zawsze wyrównane do 512 bajtów.

   > [!IMPORTANT]
   > Upewnij się, że konto usługi Azure Storage, którego używasz, nie ma zasad niezmienności ustawionych na nim, jeśli używasz go z urządzeniem usługi Azure Stack Edge lub Data Box Gateway. Aby uzyskać więcej informacji, zobacz [Ustawianie zasad niezmienności i zarządzanie nimi dla magazynu obiektów blob](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutability-policies-manage).

6. Ten krok zależy od tego, czy tworzysz udział SMB, czy udział NFS.
   - **W przypadku tworzenia udziału SMB** — w polu **Użytkownik lokalny ze wszystkimi uprawnieniami** wybierz pozycję **Utwórz nowy** lub **Użyj istniejącego**. W przypadku tworzenia nowego użytkownika lokalnego podaj **nazwę użytkownika**, **hasło**, a następnie potwierdź hasło. Spowoduje to przypisanie uprawnień do użytkownika lokalnego. Po przypisaniu uprawnień w tym miejscu możesz następnie zmodyfikować te uprawnienia przy użyciu Eksploratora plików.

      ![Dodawanie udziału SMB](media/data-box-edge-manage-shares/add-smb-share.png)

        Jeśli zaznaczysz opcję Zezwalaj tylko na operacje odczytu dla tego udziału danych, możesz określić użytkowników z uprawnieniami tylko do odczytu.
   - **W przypadku tworzenia udziału NFS** — należy podać **adresy IP dozwolonych klientów**, którzy mogą uzyskiwać dostęp do udziału.

      ![Dodawanie udziału NFS](media/data-box-edge-manage-shares/add-nfs-share.png)

7. Aby łatwo uzyskać dostęp do udziałów z modułów obliczeniowych edge, użyj lokalnego punktu instalacji. Wybierz **opcję Użyj udziału z obliczeniami edge,** tak aby udział był automatycznie montowany po jego utworzeniu. Po wybraniu tej opcji moduł Edge może również używać obliczeń z lokalnym punktem instalacji.

8. Kliknij pozycję **Utwórz**, aby utworzyć udział. Zostanie wyświetlone powiadomienie, że trwa tworzenie udziału. Po utworzeniu udziału z określonymi ustawieniami blok **Udziały** zostanie zaktualizowany, aby odzwierciedlić nowy udział.

## <a name="add-a-local-share"></a>Dodawanie udziału lokalnego

1. W witrynie Azure portal przejdź do zasobu usługi Data Box Edge, a następnie przejdź do **udziału > bramy.** Wybierz **+ Dodaj udział** na pasku poleceń.

   ![Wybierz dodaj udział](media/data-box-edge-manage-shares/add-local-share-1.png)

2. W obszarze **Dodawanie udziału** określ ustawienia udziału. Podaj unikatową nazwę udziału.
    
    Nazwy udziałów mogą zawierać tylko cyfry, małe litery i łączniki. Nazwa udziału musi mieć długość od 3 do 63 znaków i rozpoczynać się literą lub cyfrą. Przed i za każdym łącznikiem musi znajdować się znak inny niż łącznik.

3. Wybierz **Typ** dla udziału. Dostępne są dwa typy — **SMB** i **NFS** — przy czym typ SMB jest domyślny. Typ SMB jest standardem dla klientów systemu Windows, natomiast typ NFS jest używany dla klientów systemu Linux. W zależności od tego, czy wybrano udział SMB czy NFS, wyświetlone opcje są nieco inne.

4. Aby łatwo uzyskać dostęp do udziałów z modułów obliczeniowych edge, użyj lokalnego punktu instalacji. Wybierz **opcję Użyj udziału z obliczeniami edge,** aby moduł Edge mógł używać obliczeń z lokalnym punktem instalacji.

5. Wybierz **pozycję Konfiguruj jako udziały lokalne krawędzi**. Dane w lokalnych udziałach pozostaną lokalnie na urządzeniu. Można przetwarzać te dane lokalnie.

6. W polu **Wszystkie uprawnienia Użytkownik lokalny** wybierz pozycję **Utwórz nowy** lub Użyj **istniejącego**.

7. Wybierz **pozycję Utwórz**. 

   ![Tworzenie udziału lokalnego](media/data-box-edge-manage-shares/add-local-share-2.png)

    Zostanie wyświetlone powiadomienie, że tworzenie udziału jest w toku. Po utworzeniu udziału z określonymi ustawieniami blok **Udziały** zostanie zaktualizowany, aby odzwierciedlić nowy udział.

   ![Zobacz aktualizacje Udziały bloku](media/data-box-edge-manage-shares/add-local-share-3.png)
    
    Wybierz udział, aby wyświetlić lokalny punkt instalacji dla modułów obliczeniowych Edge dla tego udziału.

   ![Wyświetlanie szczegółów udziału lokalnego](media/data-box-edge-manage-shares/add-local-share-4.png)

## <a name="mount-a-share"></a>Montowanie udziału

Jeśli utworzony jest udział przed skonfigurowaniem obliczeń na urządzeniu data box edge, należy zainstalować udział. Aby zainstalować udział, należy wykonać następujące czynności.

1. W witrynie Azure portal przejdź do zasobu usługi Data Box Edge, a następnie przejdź do **udziału > bramy.** Z listy udziałów wybierz udział, który chcesz zainstalować. Kolumna **Używane dla obliczeń** wyświetli stan **Jako Wyłączony** dla wybranego udziału.

   ![Wybieranie udziału](media/data-box-edge-manage-shares/select-share-mount.png)

2. Wybierz **pozycję Zamontuj**.

   ![Wybierz uchwyt](media/data-box-edge-manage-shares/select-mount.png)

3. Po wyświetleniu monitu o potwierdzenie wybierz pozycję **Tak**. Spowoduje to zainstalowanie udziału.

   ![Potwierdź montaż](media/data-box-edge-manage-shares/confirm-mount.png)

4. Po zamontowaniu akcji przejdź do listy akcji. Zobaczysz, że kolumna **Używane dla obliczeń** pokazuje stan udziału jako **Włączone**.

   ![Współdzielenie zamontowane](media/data-box-edge-manage-shares/share-mounted.png)

5. Wybierz ponownie udział, aby wyświetlić lokalny punkt instalacji dla udziału. Moduł obliczeniowy krawędzi używa tej lokalnej punktu instalacji dla udziału.

   ![Lokalny punkt instalacji dla udziału](media/data-box-edge-manage-shares/share-mountpoint.png)

## <a name="unmount-a-share"></a>Odinstalowywszę udział

Wykonaj następujące kroki w witrynie Azure portal, aby odinstalować udział.

1. W witrynie Azure portal przejdź do zasobu usługi Data Box Edge, a następnie przejdź do **udziału > bramy.**

   ![Wybieranie udziału](media/data-box-edge-manage-shares/select-share-unmount.png)

2. Z listy udziałów wybierz udział, który chcesz odinstalować. Chcesz upewnić się, że udział, który odinstalujesz, nie jest używany przez żadne moduły. Jeśli udział jest używany przez moduł, zobaczysz problemy z odpowiednim modułem. Wybierz **opcję Odłącz**.

   ![Wybierz opcję odinstaluj](media/data-box-edge-manage-shares/select-unmount.png)

3. Po wyświetleniu monitu o potwierdzenie wybierz pozycję **Tak**. Spowoduje to odinstalowanie udziału.

   ![Potwierdź odinstalowanie](media/data-box-edge-manage-shares/confirm-unmount.png)

4. Po odinstalowyniu akcji przejdź do listy udziałów. Zobaczysz, że **kolumna Używane dla obliczeń** pokazuje stan udziału jako **Wyłączone**.

   ![Udostępnianie niezamontowane](media/data-box-edge-manage-shares/share-unmounted.png)

## <a name="delete-a-share"></a>Usuwanie udziału

Wykonaj następujące czynności w witrynie Azure Portal, aby usunąć udział.

1. Na liście udziałów kliknij udział, który chcesz usunąć.

   ![Wybieranie udziału](media/data-box-edge-manage-shares/delete-share-1.png)

2. Kliknij **pozycję Usuń**.

   ![Klikanie polecenia Usuń](media/data-box-edge-manage-shares/delete-share-2.png)

3. Po wyświetleniu monitu o potwierdzenie kliknij przycisk **Tak**.

   ![Potwierdzenie usunięcia](media/data-box-edge-manage-shares/delete-share-3.png)

Lista udostępnień jest aktualizowana w celu odzwierciedlenia usunięcia.

## <a name="refresh-shares"></a>Odświeżanie udziałów

Funkcja odświeżania umożliwia odświeżenie zawartości udziału. Podczas odświeżania udziału inicjowane jest wyszukiwanie w celu odnalezienia wszystkich obiektów platformy Azure, w tym obiektów blob i plików, które zostały dodane do chmury po ostatnio przeprowadzonym odświeżaniu. Te dodatkowe pliki są następnie pobierane w celu odświeżenia zawartości udziału na urządzeniu.

> [!IMPORTANT]
>
> - Nie można odświeżyć udziałów lokalnych.
> - Uprawnienia i listy kontroli dostępu (Listy kontroli dostępu) nie są zachowywane w całej operacji odświeżania. 

Wykonaj następujące czynności w witrynie Azure Portal, aby odświeżyć udział.

1. W witrynie Azure Portal przejdź do sekcji **Udziały**. Kliknij udział, który chcesz odświeżyć.

   ![Wybieranie udziału](media/data-box-edge-manage-shares/refresh-share-1.png)

2. Kliknij przycisk **Odśwież**.

   ![Klikanie przycisku Odśwież](media/data-box-edge-manage-shares/refresh-share-2.png)
 
3. Po wyświetleniu monitu o potwierdzenie kliknij przycisk **Tak**. Zadanie rozpoczyna odświeżanie zawartości udziału lokalnego.

   ![Potwierdzanie odświeżania](media/data-box-edge-manage-shares/refresh-share-3.png)

4. W trakcie odświeżania opcja odświeżania jest wyszarzona w menu kontekstowym. Kliknij powiadomienie o zadaniu, aby wyświetlić stan zadania odświeżania.

5. Czas odświeżania zależy od liczby plików w kontenerze platformy Azure, a także od liczby plików znajdujących się na urządzeniu. Po pomyślnym zakończeniu odświeżania znacznik czasu udziału zostanie zaktualizowany. Nawet w przypadku częściowego niepowodzenia odświeżania operacja zostanie uznana za zakończoną, a znacznik czasu zostanie zaktualizowany. Dzienniki błędów odświeżania są również aktualizowane.

   ![Zaktualizowany znacznik czasu](media/data-box-edge-manage-shares/refresh-share-4.png)
 
W przypadku awarii zostanie zgłoszony alert. Alert zawiera szczegółowe informacje o przyczynie awarii oraz zalecenia dotyczące rozwiązania problemu. Alert wskazuje również plik, który zawiera pełne podsumowanie awarii — w tym pliki, których nie udało się zaktualizować lub usunąć.

## <a name="sync-storage-keys"></a>Synchronizowanie kluczy magazynu

Jeśli klucze konta magazynu zostały wymienione, należy zsynchronizować klucze dostępu do magazynu. Przeprowadzenie synchronizacji pozwala urządzeniu uzyskać najnowsze klucze do konta magazynu.

Wykonaj następujące czynności w witrynie Azure Portal, aby zsynchronizować klucz dostępu do magazynu.

1. W zasobie przejdź do sekcji **Przegląd**. Na liście udziałów kliknij udział skojarzony z kontem magazynu, które ma zostać zsynchronizowane.

    ![Wybierz udostępnianie z odpowiednim kontem magazynu](media/data-box-edge-manage-shares/sync-storage-key-1.png)

2. Kliknij przycisk **Synchronizuj klucz magazynu**. Po wyświetleniu monitu o potwierdzenie kliknij przycisk **Tak**.

     ![Wybieranie klucza magazynu synchronizacji](media/data-box-edge-manage-shares/sync-storage-key-2.png)

3. Po zakończeniu synchronizacji zamknij okno dialogowe.

>[!NOTE]
> Wystarczy zrobić to raz dla danego konta magazynu. Nie trzeba powtarzać tej czynności dla wszystkich udziałów powiązanych z tym samym kontem magazynu.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [Zarządzać użytkownikami w witrynie Azure Portal](data-box-edge-manage-users.md).
