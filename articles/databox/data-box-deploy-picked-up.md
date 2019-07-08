---
title: Samouczka, aby odesłanie urządzenia Azure Data Box | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wysłać urządzenie Azure Data Box Disk do firmy Microsoft
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 7/08/2019
ms.author: alkohli
ms.openlocfilehash: db0f0ac3073687b7c1cd8ca60e459e4bb3aa03f4
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2019
ms.locfileid: "67626359"
---
# <a name="tutorial-return-azure-data-box-and-verify-data-upload-to-azure"></a>Samouczek: wysyłka zwrotna urządzenia Azure Data Box i weryfikowanie przekazania danych na platformę Azure

W tym samouczku opisano sposób zwrotu urządzenia Azure Data Box i weryfikacji danych przekazanych na platformę Azure.

Ten samouczek zawiera informacje na następujące tematy:

> [!div class="checklist"]
> * Wymagania wstępne
> * Przygotowanie do wysłania
> * Wysyłanie urządzenia Data Box do firmy Microsoft
> * Weryfikowanie przekazania danych na platformę Azure
> * Wymazywanie danych z urządzenia Data Box

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że spełniono następujące warunki:

- Ukończenie tematu [Samouczek: kopiowanie danych na urządzenie Azure Data Box i ich weryfikacja](data-box-deploy-copy-data.md). 
- Zadania kopiowania zostały wykonane. Przygotowanie do wysłania nie będzie działać, jeśli zadania kopiowania są w toku.

## <a name="prepare-to-ship"></a>Przygotowanie do wysłania

[!INCLUDE [data-box-prepare-to-ship](../../includes/data-box-prepare-to-ship.md)]

## <a name="ship-data-box-back"></a>Wysyłka zwrotna urządzenia Data Box

Upewnij się, że kopiowania danych na urządzeniu została zakończona i **przygotowywanie do wysłania** wykonywania zakończy się pomyślnie. Oparte na region, w którym są Wysyłka urządzenia, procedura jest inna.


### <a name="ship-in-us-canada-europe"></a>Dostarczanie w USA, Kanada, Europa Zachodnia

Jeśli zwraca urządzenia w Stanach Zjednoczonych, Kanadzie lub Europa, wykonaj następujące kroki.

1. Upewnij się, że urządzenie jest wyłączona i kable są usuwane. 
2. Zwiń przewód zasilający dostarczony wraz z urządzeniem i umieść go bezpiecznie z tyłu urządzenia.
3. Upewnij się, że na wyświetlaczu E-ink jest widoczna etykieta wysyłkowa, i umów się na odbiór przesyłki z przewoźnikiem. Jeśli etykieta jest uszkodzona, nie ma jej lub nie jest widoczna na wyświetlaczu E-ink, skontaktuj się z pomocą techniczną firmy Microsoft. Jeśli sugeruje pomocy technicznej, a następnie możesz przejść do **Przegląd > Pobierz etykietę wysyłkową** w witrynie Azure portal. Pobierz etykietę wysyłkową i dołącz ją do urządzenia. 
4. Jeśli zwracasz urządzenie, zamów odbiór paczki przez firmę UPS. Aby zaplanować odbioru:

    - Wywołanie lokalne UPS (specyficzne dla kraju/regionu darmowy numer).
    - W swojej rozmowy oferty odwrotnej wydanie numer, jak pokazano na wyświetlanie pisma odręcznego E lub drukowane etykiety śledzenia.
    - Jeśli numer śledzenia nie jest ujęty w cudzysłów, UPS będą wymagać dodatkowych opłaty są naliczane podczas odbioru.

    Zamiast planować odbiór, można również usunąć wyłączyć urządzenie Data Box w najbliższej lokalizacji nadania.
4. Po odebraniu urządzenia Data Box i zeskanowaniu go przez kuriera stan zamówienia w portalu zostanie zmieniony na **Pobrane**. Będzie też wyświetlany identyfikator śledzenia przesyłki.

### <a name="ship-in-asia-pacific-region"></a>Dostarczanie w regionie Azja i Pacyfik

#### <a name="ship-in-australia"></a>Dostarczanie w Australii

Centra danych platformy Azure w Australii mają wiadomość z powiadomieniem dodatkowe zabezpieczenia. Wszystkie przychodzące wydania muszą mieć powiadomienie z jednotygodniowym. Wykonaj poniższe kroki, aby wysłać w Australii.


1. Zachowaj oryginalne okno, używane na potrzeby wysłania urządzeniu na potrzeby wysyłki zwrotnej.
2. Upewnij się, że kopiowania danych na urządzeniu została zakończona i **przygotowywanie do wysłania wykonywania** zakończy się pomyślnie.
3. Wyłącz urządzenie i Usuń kable.
4. Buforowanie i bezpiecznie umieścić kabel sieciowy zostało dostarczone z urządzeniem tyłu urządzenia.
5. Rozwiązania Quantium wiadomości e-mail do żądania pobrania. Zapoznaj się z numer referencyjny usługi, które są określone w witrynie Azure portal. Użyj następującego szablonu wiadomości e-mail:- *żądanie etykietę wysyłkową odwrotnej kodem TAU*. Upewnij się, że Podaj następujące informacje w wiadomości e-mail: 

    ```
    To: Azure@quantiumsolutions.com
    Subject: Pickup request for Azure｜Reference number：XXX XXX XXX
    Body: 
    - Company name：
    - Address:
    - Contact name:
    - Contact number:
    - Requested pickup date: mm/dd
    ```
6. Australia rozwiązania Quantium e-mail etykiety wysyłki zwrotnej.
7. Drukowanie etykietę zwrotu i umieścić go w usłudze box wysyłki.
8. Przekazać pakiet do courier.

Jeśli to konieczne, można wysłać pocztą e-mail Quantium rozwiązań pomocy technicznej związanej z Azure@quantiumsolutions.com lub numer telefonu.


Zapytanie dotyczące zamówienia za pomocą telefonu:

- Wyślij wiadomość e-mail dotyczącą pobrania najpierw.
- Podaj swoją nazwę zamówienia na telefonie.

#### <a name="ship-in-japan"></a>Dostarczanie w Japonii 

1. Zachowaj oryginalne okno, używane na potrzeby wysłania urządzeniu na potrzeby wysyłki zwrotnej.
2. Wyłącz urządzenie i Usuń kable.
3. Buforowanie i bezpiecznie umieścić kabel sieciowy zostało dostarczone z urządzeniem tyłu urządzenia.
4. Napisz firmie nazwy i adresu informacji na temat Uwaga przesyłki jako informacje o nadawcy.
5. Wyślij wiadomość e-mail rozwiązania Quantium przy użyciu następującego szablonu wiadomości e-mail.

    - Jeśli Chakubarai wpis w Japonii przesyłki Uwaga nie została dołączona lub brakuje, należy pamiętać, że w tej wiadomości e-mail. Japonia rozwiązania Quantium zażąda Japonii wpis, aby przenieść Uwaga przesyłki od pobrania.
    - Jeśli masz wiele zamówień, poczty e-mail, aby zapewnić odbiór poszczególnych.

    ```
    To: Customerservice.JP@quantiumsolutions.com
    Subject: Pickup request for Azure Data Box｜Job name： 
    Body: 
    - Japan Post Yu-Pack tracking number (reference number)：
    - Requested pickup date：mmdd (Select a requested time slot from below).
    a. 08：00-13：00 
    b. 13：00-15：00 
    c. 15：00-17：00 
    d. 17：00-19：00 
    ```

3. Otrzymywać wiadomość e-mail z potwierdzeniem Quantium rozwiązań po zostało zarezerwowane odbioru. Potwierdzenie adresu e-mail zawiera również informacje na temat Uwaga przesyłki Chakubarai.

Jeśli to konieczne, możesz skontaktować się Obsługa rozwiązań Quantium (język japoński) na następujące informacje: 

- Adres e-mail:Customerservice.JP@quantiumsolutions.com 
- Telefon: 03-5755-0150 


## <a name="verify-data-upload-to-azure"></a>Weryfikowanie przekazania danych na platformę Azure

Gdy firma Microsoft odbierze i zeskanuje urządzenie, stan zamówienia zmieni się na **Odebrane**. Następnie urządzenie zostanie poddane fizycznej weryfikacji pod kątem uszkodzeń lub śladów naruszenia.

Po zakończeniu tej weryfikacji urządzenie Data Box zostanie podłączone do sieci w centrum danych platformy Azure. Kopiowanie danych rozpocznie się automatycznie. W zależności od rozmiaru danych operacja kopiowania może potrwać od kilku godzin do kilku dni. Postęp kopiowania możesz monitorować w portalu.

Po zakończeniu kopiowania danych stan zamówienia zmieni się na **Zakończone**.

Sprawdź, czy Twoje dane są przekazywane na platformie Azure, zanim usuniesz je ze źródła. Dane mogą należeć:

- Konta magazynu platformy Azure. Po skopiowaniu danych na urządzenie Data Box są one zależnie od typu przekazywane do jednej z poniższych ścieżek w ramach konta usługi Azure Storage.

  - W przypadku blokowych obiektów blob i stronicowych obiektów blob: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - W przypadku usługi Azure Files: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    Możesz też przejść do swojego konta usługi Azure Storage w witrynie Azure Portal i nawigować z poziomu tej witryny.

- Swojej grupy zasobów dysku zarządzanego. Podczas tworzenia dysków zarządzanych, wirtualne dyski twarde są przekazywane jako stronicowe obiekty BLOB, a następnie konwertowana do dysków zarządzanych. Dyski zarządzane są dołączone do grupy zasobów, określony w momencie tworzenia zamówienia. 

    - Jeśli Twoja kopia do usługi managed disks na platformie Azure zakończyło się pomyślnie, możesz przejść do **szczegóły zamówienia** w witrynie Azure portal i upewnij, należy pamiętać, grupy zasobów określona dla dysków zarządzanych.

        ![Identyfikowanie grup zasobów dysku zarządzanego](media/data-box-deploy-copy-data-from-vhds/order-details-managed-disk-resource-groups.png)

        Przejdź do grupy zasobów wspomniano, a następnie zlokalizuj dysków zarządzanych.

        ![Dołączone do grupy zasobów dysku zarządzanego](media/data-box-deploy-copy-data-from-vhds/managed-disks-resource-group.png)

    - Jeśli został skopiowany plik VHDX lub dynamiczne/różnicowego dysku VHD, VHDX/wirtualny dysk twardy zostanie przekazany do konta magazynu przejściowego jako stronicowy obiekt blob, ale konwersja wirtualnego dysku twardego na dysk zarządzany kończy się niepowodzeniem. Przejdź do swojej przemieszczania **konta magazynu > obiekty BLOB** , a następnie wybierz odpowiedniego kontenera — SSD w warstwie standardowa, HDD standardowa lub Premium SSD. Wirtualne dyski twarde są przekazywane jako stronicowe obiekty BLOB na koncie magazynu przejściowego.

## <a name="erasure-of-data-from-data-box"></a>Wymazywanie danych z urządzenia Data Box
 
Po zakończeniu przekazywania danych na platformę Azure dane z dysków urządzenia Data Box są bezpiecznie wymazywane zgodnie z wytycznymi [NIST SP 800-88 Revision 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku przedstawiono zagadnienia dotyczące usługi Azure Data Box, takie jak:

> [!div class="checklist"]
> * Wymagania wstępne
> * Przygotowanie do wysłania
> * Wysyłanie urządzenia Data Box do firmy Microsoft
> * Weryfikowanie przekazania danych na platformę Azure
> * Wymazywanie danych z urządzenia Data Box

Przejdź do następnego artykułu, aby dowiedzieć się, jak zarządzać urządzeniem Data Box za pomocą lokalnego internetowego interfejsu użytkownika.

> [!div class="nextstepaction"]
> [Use local web UI to administer Azure Data Box (Administrowanie urządzeniem Azure Data Box za pomocą lokalnego internetowego interfejsu użytkownika)](./data-box-local-web-ui-admin.md)


