---
title: Samouczek do wysłania dysku Azure Data Box ponownie | Dokumentacja firmy Microsoft
description: Z tego samouczka dowiesz się, jak wysłać urządzenie Azure Data Box Disk do firmy Microsoft
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 05/06/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: 023542dbc22234fc57e4ce8b662a9760be4efe04
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65150759"
---
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
2. Zapakuj wszystkie dyski i kable połączeniowe w folię bąbelkową, a następnie umieść w opakowaniu wysyłkowym. Jeśli brakuje Akcesoria, mogą być naliczane opłaty.
    - Ponowne użycie pakietu od początkowego wydania.  
    - Firma Microsoft zaleca dodatkiem Service pack dysków przy użyciu dobrze zabezpieczone zawijania bubbled.
    - Upewnij się, że dopasowania jest dobrze osadzone, aby zmniejszyć wszelkie przepływów w ramach tego pola.

Następne kroki są określane przez gdzie jest zwracany urządzenia.

### <a name="pick-up-in-us-canada"></a>Podnieś w Stanach Zjednoczonych, Kanadzie

Jeśli zwraca urządzenia w Stanach Zjednoczonych lub Kanadzie, należy wykonać następujące czynności.

1. Użyj zwrotnej etykiety wysyłkowej, znajdującej się w przezroczystej koszulce przyklejonej do opakowania. Jeśli etykieta jest uszkodzony lub utracony:
    - Przejdź do pozycji **Przegląd > Pobierz etykietę wysyłkową**.

        ![Pobieranie etykiety wysyłkowej](media/data-box-disk-deploy-picked-up/download-shipping-label.png)

        To spowoduje pobranie zwrotnej etykiety wysyłkowej, podobnej do tej widocznej poniżej.

        ![Przykładowa etykieta wysyłkowa](media/data-box-disk-deploy-picked-up/exmple-shipping-label.png)
    - Umieszcza etykiety na urządzeniu.

2. Zamknij i zaklej opakowanie wysyłkowe. Upewnij się, że zwrotna etykieta wysyłkowa jest widoczna.
3. Zaplanować odbioru UPS. Aby zaplanować odbioru:

    - Wywołanie lokalne UPS (specyficzne dla kraju darmowy numer).
    - W swojej rozmowy oferty odwrotnej wydanie numer, jak pokazano w drukowanej etykiety śledzenia.
    - Jeśli numer śledzenia nie jest ujęty w cudzysłów, UPS będą wymagać dodatkowych opłaty są naliczane podczas odbioru.
    - Zamiast planować odbiór, można również usunąć off dysku Data Box w najbliższej lokalizacji nadania.


### <a name="pick-up-in-europe"></a>Podnieś w Europie

Jeśli zwraca urządzenie w Europie, wykonaj następujące kroki.

1. Użyj zwrotnej etykiety wysyłkowej, znajdującej się w przezroczystej koszulce przyklejonej do opakowania. Jeśli etykieta jest uszkodzony lub utracony:
    - Przejdź do pozycji **Przegląd > Pobierz etykietę wysyłkową**.

        ![Pobieranie etykiety wysyłkowej](media/data-box-disk-deploy-picked-up/download-shipping-label.png)

        To spowoduje pobranie zwrotnej etykiety wysyłkowej, podobnej do tej widocznej poniżej.

        ![Przykładowa etykieta wysyłkowa](media/data-box-disk-deploy-picked-up/exmple-shipping-label.png)
    - Umieszcza etykiety na urządzeniu.

2. Zamknij i zaklej opakowanie wysyłkowe. Upewnij się, że zwrotna etykieta wysyłkowa jest widoczna.
3. Jeśli zwracasz urządzenie w Europie za pośrednictwem firmy DHL, zamów odbiór paczki przez firmę DHL w witrynie internetowej firmy, podając numer listu przewozowego.
4. Przejdź do lokalnej witryny firmy DHL Express i wybierz pozycję **Utwórz przesyłkę zwrotną**.

    ![Przez firmę DHL wysyłki zwrotnej](media/data-box-disk-deploy-picked-up/dhl-ship-1.png)
    
3. Podaj numer listu przewozowego i kliknij przycisk **Zamówienie kuriera**, aby zaplanować odebranie przesyłki.

      ![Zamówienie kuriera](media/data-box-disk-deploy-picked-up/dhl-ship-2.png)

### <a name="pick-up-in-asia-pacific-region"></a>Podnieś w regionie Azja i Pacyfik

Ten region zawiera instrukcje dotyczące pobrania w Japonii, Korei i Australii.

#### <a name="pick-up-in-australia"></a>Podnieś w Australii

Centra danych platformy Azure w Australii mają wiadomość z powiadomieniem dodatkowe zabezpieczenia. Wszystkie przychodzące wydania muszą mieć powiadomienie z jednotygodniowym. Wykonaj następujące kroki dla pobrania w Australii.

1. Adres e-mail `adbops@microsoft.com` do etykiety o wysłaniu żądania przy użyciu unikatowego Identyfikatora dla ruchu przychodzącego lub kod TAU. Umieść żądanie 3-dniowym wyprzedzeniem o planowanych daty można pobrać etykiety w czasie.
2. Temat wiadomości e-mail powinny być - *żądanie etykietę wysyłkową odwrotnej kodem TAU*. Upewnij się, że Podaj następujące informacje w wiadomości e-mail: 

    - Nazwa zamówienia
    - Adres
    - Nazwisko osoby kontaktowej

#### <a name="pick-up-in-japan"></a>Podnieś w Japonii

1. Upewnij się, że zawierają zwracany connote z Chakubarai wpis w Japonii.
2. Napisz firmie nazwy i adresu informacji na temat connote jako informacje o nadawcy.
3. Numer wpis w Japonii żądanie odbioru 0800-0800-111 (Wybieranie bezpłatna). Wybierania 7-cyfrowy kod pocztowy dla adresu odbioru, a następnie prześlij je dalej do najbliższej biura post.
    - Dostępne godziny żądanie odbioru zależą od odpowiednich urzędów post.
    - Informuje, że przesyłka znajduje się Chakubarai wpis dla Japonii Yu-Pack.
    - Użyj Chakubarai oznacza rzeczywistej, który został dołączony.
4. Czy oznacza rzeczywistej Chakubarai wpis w Japonii nie uwzględnione w wiadomości e-mail *rozwiązania Quantium* na `Customerservice.JP@quantiumsolutions.com`. *Rozwiązania Quantium* zażąda Japonii Post na odebranie i poproś go, aby wyświetlić connote od pobrania.
    - Wskazuje odwołanie numer Chakubarai oznacza rzeczywistej kolumny uwagi, pozwalających zapewnić wpis w Japonii.
    - Wprowadź adres docelowy, jak pokazano poniżej:   
        ```
        3F N7 Prologis Park Tokyo Ohta, 1-3-6 Tokai Ohta-ku, Tokyo 143-0001
        Microsoft Service Center c/o Quantium Solutions Japan
        TEL: 03-5755-0150
        ```

Jeśli oznacza rzeczywistej Chakubarai jest Brak, możesz poprosić o odbiór za pośrednictwem poczty e-mail. Następujący szablon wiadomości e-mail umożliwia żądanie pobrania.

```
To: Customerservice.JP@quantiumsolutions.com
Subject: Pickup request for Azure Data Box Disk｜Job Name： 
Body: 
- Azure Data Box Disk job name：
- Reference number:  
- Requested pickup date：mmdd (Select a requested time slot from below).
    a. 08：00-13：00 
    b. 13：00-15：00 
    c. 15：00-17：00 
    d. 17：00-19：00 
```

#### <a name="pick-up-in-korea"></a>Podnieś w Korei

1. Upewnij się, że zawierają connote zwrotu.
2. Aby żądanie pobrania:
    1. Wywołaj *Quantium Solutions International* linia informacyjna na 070 8231 1418 podczas godzin pracy (10: 00 do 17: 00, od poniedziałku do piątku). Oferta *odbioru Microsoft* numer connote rozmieścić dla kolekcji.  
    2. Jeśli linia informacyjna jest zajęty, Wyślij wiadomość e-mail `microsoft@rocketparcel.com`, temat wiadomości e-mail *Microsoft Pickup* numer connote jako odwołanie.
    3. Jeśli courier nie dotrze do kolekcji, należy wywołać *Quantium Solutions International* linia informacyjna mechanizmy alternatywne. 

## <a name="verify-data-upload-to-azure"></a>Weryfikowanie przekazania danych na platformę Azure

Po odebraniu dysków przez kuriera stan zamówienia w portalu zostanie zmieniony na **Pobrane**. Będzie też wyświetlany identyfikator śledzenia przesyłki.

![Dyski zostały pobrane](media/data-box-disk-deploy-picked-up/data-box-portal-pickedup.png)

Gdy firma Microsoft odbierze i zeskanuje dysk, stan zadania zmieni się na **Odebrane**. 

![Dyski zostały odebrane](media/data-box-disk-deploy-picked-up/data-box-portal-received.png)

Dane zostaną skopiowane automatycznie po podłączeniu dysków do serwera w centrum danych Azure. W zależności od rozmiaru danych operacja kopiowania może potrwać od kilku godzin do kilku dni. Postęp kopiowania możesz monitorować w portalu.

Po zakończeniu kopiowania danych stan zamówienia zmieni się na **Zakończone**.

![Kopiowanie danych zostało zakończone](media/data-box-disk-deploy-picked-up/data-box-portal-completed.png)

Sprawdź, czy dane znajdują się na kontach magazynu, zanim usuniesz je ze źródła. Dane mogą należeć:

- Konta magazynu platformy Azure. Po skopiowaniu danych na urządzenie Data Box są one zależnie od typu przekazywane do jednej z poniższych ścieżek w ramach konta usługi Azure Storage.

  - W przypadku blokowych obiektów blob i stronicowych obiektów blob: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - W przypadku usługi Azure Files: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    Możesz też przejść do swojego konta usługi Azure Storage w witrynie Azure Portal i nawigować z poziomu tej witryny.

- Swojej grupy zasobów dysku zarządzanego. Podczas tworzenia dysków zarządzanych, wirtualne dyski twarde są przekazywane jako stronicowe obiekty BLOB, a następnie konwertowana do dysków zarządzanych. Dyski zarządzane są dołączone do grupy zasobów, określony w momencie tworzenia zamówienia.

  - Jeśli Twoja kopia do usługi managed disks na platformie Azure zakończyło się pomyślnie, możesz przejść do **szczegóły zamówienia** w witrynie Azure portal i upewnij, należy pamiętać, grupy zasobów określona dla dysków zarządzanych.

      ![Wyświetl szczegóły zamówienia](media/data-box-disk-deploy-picked-up/order-details-resource-group.png)

    Przejdź do grupy zasobów wspomniano, a następnie zlokalizuj dysków zarządzanych.

      ![Grupy zasobów dla dysków zarządzanych](media/data-box-disk-deploy-picked-up/resource-group-attached-managed-disk.png)

  - Jeśli został skopiowany plik VHDX lub dynamiczne/różnicowego dysku VHD, VHDX/wirtualny dysk twardy jest przekazywany w do konta magazynu przejściowego jako blokowe obiekty blob. Przejdź do swojej przemieszczania **konta magazynu > obiekty BLOB** , a następnie wybierz odpowiedniego kontenera — StandardSSD, StandardHDD lub PremiumSSD. Dysk VHDX/wirtualne dyski twarde powinny wyświetlane jako blokowe obiekty BLOB na koncie magazynu przejściowego.

Aby sprawdzić, czy dane zostały przekazane na platformę Azure, wykonaj następujące czynności:

1. Przejdź do konta magazynu skojarzonego z zamówieniem dysku.
2. Przejdź do pozycji **Blob Service > Przeglądaj obiekty blob**. Zostanie wyświetlona lista kontenerów. Na koncie magazynu są tworzone kontenery o nazwach odpowiadających nazwom podfolderów utworzonych przez Ciebie w folderach *BlockBlob* i *PageBlob*.
    Jeśli nazwy folderów są niezgodne z konwencją nazewnictwa platformy Azure, przekazywanie danych na platformę Azure zakończy się niepowodzeniem.

4. Aby upewnić się, że cały zestaw danych został przekazany, użyj Eksploratora usługi Microsoft Azure Storage. Dołącz konto magazynu powiązane z zamówieniem dysków, a następnie sprawdź listę kontenerów obiektów blob. Wybierz kontener, kliknij pozycję **Więcej**, a następnie pozycję **Statystyka folderu**. W okienku **Działania** zostaną wyświetlone statystyki dotyczące tego folderu, w tym liczba i łączny rozmiar obiektów blob. Łączny rozmiar obiektów blob w bajtach powinien być taki sam, jak rozmiar zestawu danych.

    ![Statystyka folderu w Eksploratorze usługi Storage](media/data-box-disk-deploy-picked-up/folder-statistics-storage-explorer.png)

## <a name="erasure-of-data-from-data-box-disk"></a>Wymazywanie danych z urządzenia Data Box Disk

Po zakończeniu kopiowania i zweryfikowaniu przekazania danych na konto magazynu na platformie Azure dyski zostaną w bezpieczny sposób wymazane zgodnie z normą NIST.

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku przedstawiono zagadnienia dotyczące urządzenia Azure Data Box Disk, takie jak:

> [!div class="checklist"]
> * Wysyłanie urządzenia Data Box Disk do firmy Microsoft
> * Weryfikowanie przekazania danych na platformę Azure
> * Wymazywanie danych z urządzenia Data Box Disk


Przejdź do następnego tematu, aby zapoznać się z instrukcjami zarządzania usługą Data Box Disk w witrynie Azure Portal.

> [!div class="nextstepaction"]
> [Administrowanie usługą Data Box Disk w witrynie Azure Portal](./data-box-portal-ui-admin.md)


