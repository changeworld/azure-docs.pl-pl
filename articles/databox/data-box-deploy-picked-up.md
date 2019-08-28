---
title: Samouczek do dostarczania Azure Data Box z powrotem | Microsoft Docs
description: Dowiedz się, jak wysłać urządzenie Azure Data Box Disk do firmy Microsoft
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 8/27/2019
ms.author: alkohli
ms.openlocfilehash: 368439d6e15d6c94bbb96d67fcb48ab006234c95
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70098825"
---
::: zone target="docs"

# <a name="tutorial-return-azure-data-box-and-verify-data-upload-to-azure"></a>Samouczek: wysyłka zwrotna urządzenia Azure Data Box i weryfikowanie przekazania danych na platformę Azure

::: zone-end

::: zone target="chromeless"

# <a name="return-data-box-and-verify-data-upload-to-azure"></a>Zwróć urządzenie Data Box i Zweryfikuj przekazywanie danych na platformę Azure

::: zone-end

::: zone target="docs"

W tym samouczku opisano sposób zwrotu urządzenia Azure Data Box i weryfikacji danych przekazanych na platformę Azure.

Ten samouczek zawiera informacje na następujące tematy:

> [!div class="checklist"]
> * Wymagania wstępne
> * Przygotowywanie do wysłania
> * Wysyłanie urządzenia Data Box do firmy Microsoft
> * Weryfikowanie przekazania danych na platformę Azure
> * Wymazywanie danych z urządzenia Data Box

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że spełniono następujące warunki:

- Ukończenie tematu [Samouczek: kopiowanie danych na urządzenie Azure Data Box i ich weryfikacja](data-box-deploy-copy-data.md). 
- Zadania kopiowania zostały wykonane. Przygotowanie do wysłania nie będzie działać, jeśli zadania kopiowania są w toku.

## <a name="prepare-to-ship"></a>Przygotowywanie do wysłania

[!INCLUDE [data-box-prepare-to-ship](../../includes/data-box-prepare-to-ship.md)]

::: zone-end

::: zone target="chromeless"

Po zakończeniu kopiowania danych należy przygotować i dostarczyć urządzenie. Gdy urządzenie osiągnie platformę Azure centrum danych, dane są automatycznie przekazywane do platformy Azure.

## <a name="prepare-to-ship"></a>Przygotowywanie do wysłania

Przed przygotowaniem do wysłania upewnij się, że zadania kopiowania zostały ukończone.

1. Przejdź do strony **Prepare to ship** (Przygotowanie do wysłania) w lokalnym internetowym interfejsie użytkownika i rozpocznij przygotowanie do wysłania. 
2. Wyłącz urządzenie za pomocą lokalnego internetowego interfejsu użytkownika. Odłącz przewody od urządzenia. 

Następne kroki są określane przez miejsce zwrócenia urządzenia.

::: zone-end

::: zone target="docs"

## <a name="ship-data-box-back"></a>Wysyłka zwrotna urządzenia Data Box

Upewnij się, że kopiowanie danych na urządzenie zostało ukończone, a **przygotowanie do wysłania** uruchomienie zakończyło się pomyślnie. W zależności od regionu, w którym jest wysyłany urządzenie, procedura jest inna.

::: zone-end

## <a name="ship-in-us-canada-europe"></a>Wysyłka w Stanach Zjednoczonych, Kanadzie, Europie

W przypadku powrotu urządzenia do Stanów Zjednoczonych, Kanady lub Europa należy wykonać następujące czynności.

1. Upewnij się, że urządzenie jest wyłączone, a kable są usuwane. 
2. Zwiń przewód zasilający dostarczony wraz z urządzeniem i umieść go bezpiecznie z tyłu urządzenia.
3. Upewnij się, że na wyświetlaczu E-ink jest widoczna etykieta wysyłkowa, i umów się na odbiór przesyłki z przewoźnikiem. Jeśli etykieta jest uszkodzona, nie ma jej lub nie jest widoczna na wyświetlaczu E-ink, skontaktuj się z pomocą techniczną firmy Microsoft. Jeśli zasugerujesz pomoc techniczną, możesz przejść do **omówienia > Pobierz etykietę wysyłkową** w Azure Portal. Pobierz etykietę wysyłkową i dołącz ją do urządzenia. 
4. Jeśli zwracasz urządzenie, zamów odbiór paczki przez firmę UPS. Aby zaplanować odbiór:

    - Wywoływanie lokalnego zasilacza UPS (bezpłatny numer kierunkowy kraju/regionu).
    - W wywołaniu Wywołaj numer śledzenia odwrotnego, jak pokazano na ekranie poczty E-mail lub w wydrukowanej etykiecie.
    - Jeśli numer śledzenia nie jest ujęty w cudzysłów, UPS będzie wymagał dodatkowej opłaty za pobranie.

    Zamiast planowania pobrania, można również wycofać urządzenie Data Box w najbliższej lokalizacji docelowej.
4. Po odebraniu urządzenia Data Box i zeskanowaniu go przez kuriera stan zamówienia w portalu zostanie zmieniony na **Pobrane**. Będzie też wyświetlany identyfikator śledzenia przesyłki.


## <a name="ship-in-australia"></a>Wysyłka w Australii

Centra danych platformy Azure w Australii mają dodatkowe powiadomienie o zabezpieczeniach. Wszystkie wychodzące wysyłki muszą mieć zaawansowane powiadomienie. Wykonaj następujące kroki, aby dostarczyć w Australii.


1. Zachowaj oryginalne pole używane do wysłania urządzenia na potrzeby wysyłki zwrotnej.
2. Upewnij się, że kopiowanie danych na urządzenie zostało ukończone, a **przygotowanie do wysłania uruchomienie** zakończyło się pomyślnie.
3. Wyłącz urządzenie i Usuń kable.
4. Buforowanie i bezpieczne umieszczanie przewodu zasilającego, który został dostarczony z urządzeniem z tyłu urządzenia.
5. Quantium rozwiązania e-mail do żądania pobrania. Zapoznaj się z numerem referencyjnym usługi określonym w Azure Portal. Użyj następującego szablonu wiadomości e-mail:- *Request for Reverse etykieta dostawy z kodem Tau*. Upewnij się, że w wiadomości e-mail zostały uwzględnione następujące szczegóły: 

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
6. Australia Quantium Solutions zwróci wiadomość e-mail z etykietą wysyłki zwrotnej.
7. Wydrukuj etykietę zwracaną i zamocować ją w polu wysyłka.
8. Przekazanie pakietu do kuriera.

Jeśli to konieczne, możesz wysłać wiadomość e-mail z obsługą rozwiązania Quantium na Azure@quantiumsolutions.com numer telefonu.

Zapytanie dotyczące zamówienia za pośrednictwem telefonu:

- Wyślij wiadomość e-mail do odbioru jako pierwsza.
- Podaj nazwę zamówienia na telefonie.

## <a name="ship-in-japan"></a>Wysyłka w Japonii 

1. Zachowaj oryginalne pole używane do wysłania urządzenia na potrzeby wysyłki zwrotnej.
2. Wyłącz urządzenie i Usuń kable.
3. Buforowanie i bezpieczne umieszczanie przewodu zasilającego, który został dostarczony z urządzeniem z tyłu urządzenia.
4. Zapisz nazwę firmy i informacje o adresie na liście wysyłkowej jako informacje o nadawcy.
5. Quantium rozwiązanie e-mail przy użyciu następującego szablonu wiadomości e-mail.

    - Jeśli nie dołączono lub nie ma żadnej adnotacji w języku Japonia post Chakubarai, należy pamiętać, że w tej wiadomości e-mail. Rozwiązania Quantium w Japonii zażądają opublikowania przesyłki w języku Japonia.
    - Jeśli masz wiele zamówień, Wyślij wiadomość e-mail, aby zapewnić indywidualne odbiór.

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

3. Otrzymuj potwierdzenie e-mail z rozwiązań Quantium po zapisaniu odbioru. Potwierdzenie wiadomości e-mail obejmuje również informacje dotyczące Chakubarai wysyłki.

W razie konieczności można skontaktować się z pomocą techniczną rozwiązania Quantium (język japoński), wykonując następujące informacje: 

- Adres e-mail:Customerservice.JP@quantiumsolutions.com 
- Telefon: 03-5755-0150 

::: zone target="docs"

## <a name="verify-data-upload-to-azure"></a>Weryfikowanie przekazania danych na platformę Azure

Gdy firma Microsoft odbierze i zeskanuje urządzenie, stan zamówienia zmieni się na **Odebrane**. Następnie urządzenie zostanie poddane fizycznej weryfikacji pod kątem uszkodzeń lub śladów naruszenia.

Po zakończeniu tej weryfikacji urządzenie Data Box zostanie podłączone do sieci w centrum danych platformy Azure. Kopiowanie danych rozpocznie się automatycznie. W zależności od rozmiaru danych operacja kopiowania może potrwać od kilku godzin do kilku dni. Postęp kopiowania możesz monitorować w portalu.

Po zakończeniu kopiowania danych stan zamówienia zmieni się na **Zakończone**.

Przed usunięciem z źródła upewnij się, że dane są przekazywane na platformę Azure. Twoje dane mogą znajdować się w:

- Twoje konta usługi Azure Storage. Po skopiowaniu danych na urządzenie Data Box są one zależnie od typu przekazywane do jednej z poniższych ścieżek w ramach konta usługi Azure Storage.

  - W przypadku blokowych obiektów blob i stronicowych obiektów blob: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - W przypadku usługi Azure Files: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    Możesz też przejść do swojego konta usługi Azure Storage w witrynie Azure Portal i nawigować z poziomu tej witryny.

- Grupy zasobów dysku zarządzanego. Podczas tworzenia dysków zarządzanych wirtualne dyski twarde są przekazywane jako stronicowe obiekty blob, a następnie konwertowane na dyski zarządzane. Dyski zarządzane są dołączone do grup zasobów określonych w momencie tworzenia zamówienia. 

    - Jeśli kopiowanie do dysków zarządzanych na platformie Azure zakończyło się pomyślnie, możesz przejść do **szczegółów zamówienia** w Azure Portal i zanotować grupy zasobów określone dla dysków zarządzanych.

        ![Identyfikowanie grup zasobów dysku zarządzanego](media/data-box-deploy-copy-data-from-vhds/order-details-managed-disk-resource-groups.png)

        Przejdź do zanotowanej grupy zasobów i Znajdź dyski zarządzane.

        ![Dysk zarządzany dołączony do grup zasobów](media/data-box-deploy-copy-data-from-vhds/managed-disks-resource-group.png)

    - W przypadku skopiowania pliku VHDX lub dynamicznego/różnicowego dysku VHD dysk VHDX/VHD zostanie przekazany do konta magazynu tymczasowego jako stronicowe obiekty blob, ale konwersja dysku VHD na dysk zarządzany kończy się niepowodzeniem. Przejdź do swojego **konta magazynu tymczasowego > obiektów BLOB** , a następnie wybierz odpowiedni kontener — SSD w warstwie Standardowa, HDD w warstwie Standardowa lub SSD w warstwie Premium. Wirtualne dyski twarde są przekazywane jako stronicowe obiekty blob na koncie magazynu przemieszczania.

::: zone-end

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Weryfikowanie przekazania danych na platformę Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

::: zone-end

## <a name="erasure-of-data-from-data-box"></a>Wymazywanie danych z urządzenia Data Box
 
Po zakończeniu przekazywania danych na platformę Azure dane z dysków urządzenia Data Box są bezpiecznie wymazywane zgodnie z wytycznymi [NIST SP 800-88 Revision 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone target="docs"

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono zagadnienia dotyczące usługi Azure Data Box, takie jak:

> [!div class="checklist"]
> * Wymagania wstępne
> * Przygotowywanie do wysłania
> * Wysyłanie urządzenia Data Box do firmy Microsoft
> * Weryfikowanie przekazania danych na platformę Azure
> * Wymazywanie danych z urządzenia Data Box

Przejdź do następnego artykułu, aby dowiedzieć się, jak zarządzać urządzeniem Data Box za pomocą lokalnego internetowego interfejsu użytkownika.

> [!div class="nextstepaction"]
> [Use local web UI to administer Azure Data Box (Administrowanie urządzeniem Azure Data Box za pomocą lokalnego internetowego interfejsu użytkownika)](./data-box-local-web-ui-admin.md)

::: zone-end


