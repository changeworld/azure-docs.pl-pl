---
title: Samouczek do dostarczania Azure Data Box Disk z powrotem | Microsoft Docs
description: Z tego samouczka dowiesz się, jak wysłać urządzenie Azure Data Box Disk do firmy Microsoft
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 07/23/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: ca42532fe6ba954180ce06ed0e3561e42f1fb447
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2019
ms.locfileid: "68424240"
---
::: zone target="docs"

# <a name="tutorial-return-azure-data-box-disk-and-verify-data-upload-to-azure"></a>Samouczek: wysyłka zwrotna urządzenia Azure Data Box Disk i weryfikowanie przekazania danych na platformę Azure

Jest to ostatni samouczek z serii: Wdrażanie urządzenia Azure Data Box Disk. Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Wysyłanie urządzenia Data Box Disk do firmy Microsoft
> * Weryfikowanie przekazania danych na platformę Azure
> * Wymazywanie danych z urządzenia Data Box Disk

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że zostały wykonane kroki opisane w artykule [Samouczek: kopiowanie danych na urządzenie Azure Data Box Disk i ich weryfikacja](data-box-disk-deploy-copy-data.md).


## <a name="ship-data-box-disk-back"></a>Wysyłka zwrotna urządzenia Data Box Disk

1. Po zakończeniu sprawdzania poprawności danych odłącz dyski. Odłącz kable połączeniowe.
2. Zapakuj wszystkie dyski i kable połączeniowe w folię bąbelkową, a następnie umieść w opakowaniu wysyłkowym. Opłaty mogą być stosowane w przypadku braku akcesoriów.
    - Ponownie Użyj opakowania z początkowego wydania.  
    - Zalecamy pakowanie dysków przy użyciu dobrze zabezpieczonego zawijania bąbelkowego.
    - Upewnij się, że dopasowanie jest Snug, aby zmniejszyć liczbę ruchów w polu.

Następne kroki są określane przez miejsce zwrócenia urządzenia.

### <a name="pick-up-in-us-canada"></a>Wybieraj w Stanach Zjednoczonych, Kanadzie

W przypadku powrotu urządzenia w Stanach Zjednoczonych lub Kanadzie wykonaj następujące czynności.

1. Użyj zwrotnej etykiety wysyłkowej, znajdującej się w przezroczystej koszulce przyklejonej do opakowania. Jeśli etykieta jest uszkodzona lub utracona:
    - Przejdź do pozycji **Przegląd > Pobierz etykietę wysyłkową**.

        ![Pobieranie etykiety wysyłkowej](media/data-box-disk-deploy-picked-up/download-shipping-label.png)

        To spowoduje pobranie zwrotnej etykiety wysyłkowej, podobnej do tej widocznej poniżej.

        ![Przykładowa etykieta wysyłkowa](media/data-box-disk-deploy-picked-up/exmple-shipping-label.png)
    - Umieszczanie etykiety na urządzeniu.

2. Zamknij i zaklej opakowanie wysyłkowe. Upewnij się, że zwrotna etykieta wysyłkowa jest widoczna.
3. Zaplanuj pobranie przy użyciu zasilacza UPS. Aby zaplanować odbiór:

    - Wywoływanie lokalnego zasilacza UPS (bezpłatny numer kierunkowy kraju/regionu).
    - W wywołaniu Zadzwoń na numer śledzenia wycofywania przesyłki, jak pokazano na wydrukowanej etykiecie.
    - Jeśli numer śledzenia nie jest ujęty w cudzysłów, UPS będzie wymagał dodatkowej opłaty za pobranie.
    - Zamiast planowania pobrania, można również wycofać Data Box Disk w najbliższej lokalizacji docelowej.


### <a name="pick-up-in-europe"></a>Pobierz w Europie

W przypadku powrotu urządzenia w Europie należy wykonać poniższe czynności.

1. Użyj zwrotnej etykiety wysyłkowej, znajdującej się w przezroczystej koszulce przyklejonej do opakowania. Jeśli etykieta jest uszkodzona lub utracona:
    - Przejdź do pozycji **Przegląd > Pobierz etykietę wysyłkową**.

        ![Pobieranie etykiety wysyłkowej](media/data-box-disk-deploy-picked-up/download-shipping-label.png)

        To spowoduje pobranie zwrotnej etykiety wysyłkowej, podobnej do tej widocznej poniżej.

        ![Przykładowa etykieta wysyłkowa](media/data-box-disk-deploy-picked-up/exmple-shipping-label.png)
    - Umieszczanie etykiety na urządzeniu.

2. Zamknij i zaklej opakowanie wysyłkowe. Upewnij się, że zwrotna etykieta wysyłkowa jest widoczna.
3. Jeśli zwracasz urządzenie w Europie za pośrednictwem firmy DHL, zamów odbiór paczki przez firmę DHL w witrynie internetowej firmy, podając numer listu przewozowego.
4. Przejdź do witryny sieci Web kraj/region DHL Express i wybierz pozycję **książka kolekcja kurierów > eReturn**.

    ![DHL zwrotne](media/data-box-disk-deploy-picked-up/dhl-ship-1.png)
    
3. Podaj numer listu przewozowego i kliknij przycisk **Zamówienie kuriera**, aby zaplanować odebranie przesyłki.

      ![Zaplanuj odebranie](media/data-box-disk-deploy-picked-up/dhl-ship-2.png)

### <a name="pick-up-in-asia-pacific-region"></a>Wybierz w regionie Azja-Pacyfik

Ten region zawiera instrukcje dotyczące odbioru w Japonii, Korei, Australii i Singapurze.

#### <a name="pick-up-in-australia"></a>Wybierz w Australii

Centra danych platformy Azure w Australii mają dodatkowe powiadomienie o zabezpieczeniach. Wszystkie wychodzące wysyłki muszą mieć zaawansowane powiadomienie. Wykonaj następujące kroki, aby uzyskać w Australii.

1. Wyślij `adbops@microsoft.com` wiadomość e-mail na żądanie etykiety wysyłki z unikatowym identyfikatorem ruchu przychodzącego lub kodem Tau. Umieść żądanie co najmniej 3 dni przed zaplanowaną datą wysyłki, aby uzyskać etykietę w czasie.
2. Temat wiadomości e-mail powinien zawierać *żądanie odwrócenia etykiety wysyłki z kodem Tau*. Upewnij się, że w wiadomości e-mail zostały uwzględnione następujące szczegóły: 

    - Nazwa zamówienia
    - Adres
    - Nazwa kontaktu

#### <a name="pick-up-in-japan"></a>Pobierz w Japonii

1. Zapisz nazwę firmy i informacje o adresie na liście wysyłkowej jako informacje o nadawcy.
2. Quantium rozwiązanie e-mail przy użyciu następującego szablonu wiadomości e-mail.

    - Jeśli nie dołączono lub nie ma żadnej adnotacji w języku Japonia post Chakubarai, należy pamiętać, że w tej wiadomości e-mail. Rozwiązania Quantium w Japonii zażądają opublikowania przesyłki w języku Japonia.
    - Jeśli masz wiele zamówień, Wyślij wiadomość e-mail, aby zapewnić indywidualne odbiór.

    ```
    To: Customerservice.JP@quantiumsolutions.com
    Subject: Pickup request for Azure Data Box Disk｜Job Name： 
    Body: 
    - Japan Post Yu-Pack tracking number (reference number)：
    - Requested pickup date：mmdd (Select a requested time slot from below).
        a. 08：00-13：00 
        b. 13：00-15：00 
        c. 15：00-17：00 
        d. 17：00-19：00 
    ```

3. Otrzymuj potwierdzenie e-mail z rozwiązań Quantium po zapisaniu odbioru. Potwierdzenie wiadomości e-mail obejmuje również informacje dotyczące Chakubarai wysyłki.

W razie konieczności można skontaktować się z pomocą techniczną rozwiązania Quantium (język japoński), wykonując następujące informacje: 

- Adres e-mail:Customerservice.JP@quantiumsolutions.com 
- Telefon: 03-5755-0150 

#### <a name="pick-up-in-korea"></a>Wybierz w Korei

1. Upewnij się, że zawarto adnotację zwrotną przesyłki.
2. Aby zażądać pobrania, gdy jest obecny komentarz dotyczący wysyłki:
    1. Zadzwoń na 070-8231-1418 *rozwiązań Quantium* w godzinach pracy (od 10 do 5 PM, od poniedziałku do piątku). Oferta *Microsoft Azure pobrania* i numer żądania obsługi w celu rozmieszczenia dla kolekcji.  
    2. Jeśli linia jest zajęta, Wyślij `microsoft@rocketparcel.com`wiadomość e-mail z tematem wiadomości e-mail *Microsoft Azure pobranie* i numer żądania obsługi jako odwołanie.
    3. Jeśli Courier nie dotarł do kolekcji, wywołaj *Quantium Solutions International* linia do alternatywnych rozwiązań. 
    4. Otrzymasz potwierdzenie wiadomości e-mail dotyczącej harmonogramu odbioru.
3. Wykonaj ten krok tylko wtedy, gdy nie ma uwagi dotyczącej wysyłki. Aby poprosić o pobranie:
    1. Zadzwoń na 070-8231-1418 *rozwiązań Quantium* w godzinach pracy (od 10 do 5 PM, od poniedziałku do piątku). Oferta *Microsoft Azure pobrania* i numer żądania obsługi w celu rozmieszczenia dla kolekcji. Określ, że potrzebujesz nowej noty wysyłkowej do rozmieszczenia dla kolekcji. Podaj nadawcę (klienta), informacje o odbiorniku (centrum danych platformy Azure) i numer referencyjny (numer żądania obsługi). 
    2. Jeśli linia jest zajęta, Wyślij `microsoft@rocketparcel.com`wiadomość e-mail z tematem wiadomości e-mail *Microsoft Azure pobranie* i numer żądania obsługi jako odwołanie.
    3. Jeśli Courier nie dotarł do kolekcji, wywołaj *Quantium Solutions International* linia do alternatywnych rozwiązań. 
    4. Jeśli żądanie zostało wysłane za pośrednictwem telefonu, otrzymasz ustne potwierdzenie.

### <a name="pick-up-in-singapore"></a>Wybierz w Singapurze

1. Wydrukuj etykietę wysyłkową i Dołącz ją do pola. Jeśli etykieta jest uszkodzona lub utracona:
    - Przejdź do pozycji **Przegląd > Pobierz etykietę wysyłkową**.

        ![Pobieranie etykiety wysyłkowej](media/data-box-disk-deploy-picked-up/download-shipping-label.png)

        To spowoduje pobranie zwrotnej etykiety wysyłkowej, podobnej do tej widocznej poniżej.

        ![Przykładowa etykieta wysyłkowa](media/data-box-disk-deploy-picked-up/exmple-shipping-label.png)
    - Umieszczanie etykiety na urządzeniu. Upewnij się, że etykieta jest widoczna.

2. Aby poprosić o pobranie:
    - Wywołaj **SingPost** linia z **6845 6485** w godzinach pracy (9:00 do 17:00, od poniedziałku do piątku).  
    - Oferta *Microsoft Azure pobrania* i numer żądania obsługi (numer śledzenia na etykiecie wysyłania zwrotnego) w celu zorganizowania kolekcji. 
    - Otrzymasz ustne potwierdzenie dla harmonogramu odbioru. 
    - Jeśli Courier nie dotarł do kolekcji, wywołaj **SingPost** o godzinie **6845 6485** , aby zapoznać się z alternatywnymi rozwiązaniami. 
3. Dostęp do kurierów. 


## <a name="verify-data-upload-to-azure"></a>Weryfikowanie przekazania danych na platformę Azure

Po odebraniu dysków przez kuriera stan zamówienia w portalu zostanie zmieniony na **Pobrane**. Będzie też wyświetlany identyfikator śledzenia przesyłki.

![Dyski zostały pobrane](media/data-box-disk-deploy-picked-up/data-box-portal-pickedup.png)

Gdy firma Microsoft odbierze i zeskanuje dysk, stan zadania zmieni się na **Odebrane**. 

![Dyski zostały odebrane](media/data-box-disk-deploy-picked-up/data-box-portal-received.png)

Dane zostaną skopiowane automatycznie po podłączeniu dysków do serwera w centrum danych Azure. W zależności od rozmiaru danych operacja kopiowania może potrwać od kilku godzin do kilku dni. Postęp kopiowania możesz monitorować w portalu.

Po zakończeniu kopiowania danych stan zamówienia zmieni się na **Zakończone**.

![Kopiowanie danych zostało zakończone](media/data-box-disk-deploy-picked-up/data-box-portal-completed.png)

Jeśli kopia zakończy się z błędami, zobacz [Rozwiązywanie problemów](data-box-disk-troubleshoot-upload.md)z przekazywaniem.

Sprawdź, czy dane znajdują się na kontach magazynu, zanim usuniesz je ze źródła. Twoje dane mogą znajdować się w:

- Twoje konta usługi Azure Storage. Po skopiowaniu danych na urządzenie Data Box są one zależnie od typu przekazywane do jednej z poniższych ścieżek w ramach konta usługi Azure Storage.

  - W przypadku blokowych obiektów blob i stronicowych obiektów blob: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - W przypadku usługi Azure Files: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    Możesz też przejść do swojego konta usługi Azure Storage w witrynie Azure Portal i nawigować z poziomu tej witryny.

- Grupy zasobów dysku zarządzanego. Podczas tworzenia dysków zarządzanych wirtualne dyski twarde są przekazywane jako stronicowe obiekty blob, a następnie konwertowane na dyski zarządzane. Dyski zarządzane są dołączone do grup zasobów określonych w momencie tworzenia zamówienia.

  - Jeśli kopiowanie do dysków zarządzanych na platformie Azure zakończyło się pomyślnie, możesz przejść do **szczegółów zamówienia** w Azure Portal i zanotować grupę zasobów określoną dla dysków zarządzanych.

      ![Wyświetl szczegóły zamówienia](media/data-box-disk-deploy-picked-up/order-details-resource-group.png)

    Przejdź do zanotowanej grupy zasobów i Znajdź dyski zarządzane.

      ![Grupa zasobów dla dysków zarządzanych](media/data-box-disk-deploy-picked-up/resource-group-attached-managed-disk.png)

  - W przypadku skopiowania pliku VHDX lub dynamicznego/różnicowego dysku VHD dysk VHDX/VHD zostanie przekazany do konta magazynu tymczasowego jako blokowy obiekt BLOB. Przejdź do swojego **konta magazynu tymczasowego > obiektów BLOB** , a następnie wybierz odpowiedni kontener — StandardSSD, StandardHDD lub PremiumSSD. Plik VHDX/VHD powinien być wyświetlany jako blokowe obiekty blob na koncie magazynu przemieszczania.

Aby sprawdzić, czy dane zostały przekazane na platformę Azure, wykonaj następujące czynności:

1. Przejdź do konta magazynu skojarzonego z zamówieniem dysku.
2. Przejdź do pozycji **Blob Service > Przeglądaj obiekty blob**. Zostanie wyświetlona lista kontenerów. Na koncie magazynu są tworzone kontenery o nazwach odpowiadających nazwom podfolderów utworzonych przez Ciebie w folderach *BlockBlob* i *PageBlob*.
    Jeśli nazwy folderów są niezgodne z konwencją nazewnictwa platformy Azure, przekazywanie danych na platformę Azure zakończy się niepowodzeniem.

4. Aby upewnić się, że cały zestaw danych został przekazany, użyj Eksploratora usługi Microsoft Azure Storage. Dołącz konto magazynu powiązane z zamówieniem dysków, a następnie sprawdź listę kontenerów obiektów blob. Wybierz kontener, kliknij pozycję **Więcej**, a następnie pozycję **Statystyka folderu**. W okienku **Działania** zostaną wyświetlone statystyki dotyczące tego folderu, w tym liczba i łączny rozmiar obiektów blob. Łączny rozmiar obiektów blob w bajtach powinien być taki sam, jak rozmiar zestawu danych.

    ![Statystyka folderu w Eksploratorze usługi Storage](media/data-box-disk-deploy-picked-up/folder-statistics-storage-explorer.png)

## <a name="erasure-of-data-from-data-box-disk"></a>Wymazywanie danych z urządzenia Data Box Disk

Po zakończeniu kopiowania i zweryfikowaniu przekazania danych na konto magazynu na platformie Azure dyski zostaną w bezpieczny sposób wymazane zgodnie z normą NIST.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono zagadnienia dotyczące urządzenia Azure Data Box Disk, takie jak:

> [!div class="checklist"]
> * Wysyłanie urządzenia Data Box Disk do firmy Microsoft
> * Weryfikowanie przekazania danych na platformę Azure
> * Wymazywanie danych z urządzenia Data Box Disk


Przejdź do następnego tematu, aby zapoznać się z instrukcjami zarządzania usługą Data Box Disk w witrynie Azure Portal.

> [!div class="nextstepaction"]
> [Administrowanie usługą Data Box Disk w witrynie Azure Portal](./data-box-portal-ui-admin.md)

::: zone-end


::: zone target="chromeless"

# <a name="return-the-disk-and-verify-upload-to-azure"></a>Zwróć dysk i sprawdź poprawność przekazywania na platformę Azure

## <a name="ship-to-azure"></a>Wysyłka do platformy Azure

1. Po zakończeniu walidacji danych Odłącz dyski. Odłącz kable połączeniowe.
2. Zapakuj wszystkie dyski i kable połączeniowe w folię bąbelkową, a następnie umieść w opakowaniu wysyłkowym. Opłaty mogą być stosowane w przypadku braku akcesoriów.
    - Ponownie Użyj opakowania z początkowego wydania.  
    - Zalecamy pakowanie dysków przy użyciu dobrze zabezpieczonego zawijania bąbelkowego.
    - Upewnij się, że dopasowanie jest Snug, aby zmniejszyć liczbę ruchów w polu.
3. Następne kroki są określane przez miejsce zwrócenia urządzenia.
    - Zaplanuj powracanie [za pomocą zasilacza UPS w przypadku powrotu urządzenia do Stanów Zjednoczonych i Kanady](data-box-disk-deploy-picked-up.md#pick-up-in-us-canada).
    - [Zaplanuj pobranie z DHL dla Europy](data-box-disk-deploy-picked-up.md#pick-up-in-europe) , odwiedzając swoją witrynę sieci Web i podając numer rachunku Airway.
    - [Zaplanuj odbiór dla krajów w regionie Australii](data-box-disk-deploy-picked-up.md#pick-up-in-asia-pacific-region) , takim jak Australia, Japonia, Korea i Singapur.
4. Po pobraniu dysków przez operatora zostanie wyświetlony stan zamówienia w portalu aktualizacje i identyfikator śledzenia.

## <a name="verify-upload-to-azure"></a>Weryfikuj przekazywanie do platformy Azure

Po przekazaniu danych na platformę Azure Sprawdź, czy Twoje dane są na kontach magazynu przed usunięciem go ze źródła. Twoje dane mogą znajdować się w:

- Twoje konta usługi Azure Storage. Po skopiowaniu danych na urządzenie Data Box są one zależnie od typu przekazywane do jednej z poniższych ścieżek w ramach konta usługi Azure Storage.

    - **Dla blokowych obiektów blob i stronicowych obiektów BLOB**: https://< storage_account_name >. blob. Core.<containername>Windows. NET//Files/a.txt

    - **W przypadku Azure Files**: https://< storage_account_name >. plik. Core. Windows. NET<sharename>//Files/a.txt

    Możesz też przejść do swojego konta usługi Azure Storage w witrynie Azure Portal i nawigować z poziomu tej witryny.

- Grupy zasobów dysku zarządzanego. Podczas tworzenia dysków zarządzanych wirtualne dyski twarde są przekazywane jako stronicowe obiekty blob, a następnie konwertowane na dyski zarządzane. Dyski zarządzane są dołączone do grup zasobów określonych w momencie tworzenia zamówienia.

::: zone-end


