---
title: Samouczek dotyczący odsyłania z powrotem urządzenia Azure Data Box | Microsoft Docs
description: Dowiedz się, jak wysłać urządzenie Azure Data Box Disk do firmy Microsoft
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 09/20/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 28666aaac4ec221acca00d937d54a753a4e6a055
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/20/2019
ms.locfileid: "71172686"
---
::: zone target="docs"

# <a name="tutorial-return-azure-data-box-and-verify-data-upload-to-azure"></a>Samouczek: wysyłka zwrotna urządzenia Azure Data Box i weryfikowanie przekazania danych na platformę Azure

::: zone-end

::: zone target="chromeless"

# <a name="return-data-box-and-verify-data-upload-to-azure"></a>wysyłka zwrotna urządzenia Data Box i weryfikowanie przekazania danych na platformę Azure

::: zone-end

::: zone target="docs"

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

::: zone-end

::: zone target="chromeless"

Po zakończeniu kopiowania danych należy przygotować i dostarczyć urządzenie. Gdy urządzenie dotrze do centrum danych platformy Azure, dane zostaną automatycznie przekazane na platformę Azure.

## <a name="prepare-to-ship"></a>Przygotowanie do wysłania

Przed przygotowaniem do wysłania upewnij się, że zadania kopiowania zostały ukończone.

1. Przejdź do strony **Prepare to ship** (Przygotowanie do wysłania) w lokalnym internetowym interfejsie użytkownika i rozpocznij przygotowanie do wysłania. 
2. Wyłącz urządzenie za pomocą lokalnego internetowego interfejsu użytkownika. Odłącz przewody od urządzenia. 

Następne czynności są zależne od lokalizacji, z której zwracasz urządzenie.

::: zone-end

::: zone target="docs"

## <a name="ship-data-box-back"></a>Wysyłka zwrotna urządzenia Data Box

Upewnij się, że kopiowanie danych na urządzenie zostało ukończone, a **przygotowanie do wysłania** zakończyło się pomyślnie. W zależności od regionu, w którym jest wysyłane urządzenie, procedura jest inna.

::: zone-end

## <a name="in-us-canada-europetabin-us-canada-europe"></a>[W Stanach Zjednoczonych, Kanadzie i Europie](#tab/in-us-canada-europe)

W przypadku zwrotu urządzenia w Stanach Zjednoczonych, Kanadzie lub Europie wykonaj następujące czynności.

1. Upewnij się, że wyłączono zasilanie urządzenia, a kable zostały odłączone. 
2. Zwiń przewód zasilający dostarczony wraz z urządzeniem i umieść go bezpiecznie z tyłu urządzenia.
3. Upewnij się, że na wyświetlaczu E-ink jest widoczna etykieta wysyłkowa, i umów się na odbiór przesyłki z przewoźnikiem. Jeśli etykieta jest uszkodzona, nie ma jej lub nie jest widoczna na wyświetlaczu E-ink, skontaktuj się z pomocą techniczną firmy Microsoft. Jeśli otrzymasz takie instrukcje od pomocy technicznej, możesz przejść na stronę **Przegląd > Pobierz etykietę wysyłkową** w witrynie Azure Portal. Pobierz etykietę wysyłkową i dołącz ją do urządzenia. 
4. Jeśli zwracasz urządzenie, zamów odbiór paczki przez firmę UPS. Aby zaplanować odbiór, wykonaj następujące czynności:

    - Zadzwoń do lokalnego oddziału firmy UPS (numer bezpłatny dla danego kraju/regionu).
    - Podczas rozmowy podaj numer śledzenia przesyłki zwrotnej widoczny na wyświetlaczu E-ink lub na wydrukowanej etykiecie.
    - Jeśli nie podasz numeru śledzenia, firma UPS będzie wymagała uiszczenia dodatkowej opłaty podczas odbioru.

    Zamiast planowania odbioru możesz także dostarczyć urządzenie Data Box do najbliższego punktu UPS.
4. Po odebraniu urządzenia Data Box i zeskanowaniu go przez kuriera stan zamówienia w portalu zostanie zmieniony na **Pobrane**. Będzie też wyświetlany identyfikator śledzenia przesyłki.

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Weryfikowanie przekazania danych na platformę Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Wymazywanie danych z urządzenia Data Box
 
Po zakończeniu przekazywania danych na platformę Azure dane z dysków urządzenia Data Box są bezpiecznie wymazywane zgodnie z wytycznymi [NIST SP 800-88 Revision 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

::: zone target="docs"

[!INCLUDE [data-box-verify-upload-return](../../includes/data-box-verify-upload-return.md)]



::: zone-end


## <a name="in-australiatabin-australia"></a>[W Australii](#tab/in-australia)

W przypadku centrów danych w Australii obowiązuje dodatkowe zabezpieczenie. Zabezpieczenie to polega na powiadamianiu z wyprzedzeniem o wszystkich przesyłkach przychodzących. Wykonaj poniższe kroki w przypadku wysyłki w Australii.


1. Zachowaj oryginalne pudełko użyte do wysłania urządzenia na potrzeby wysyłki zwrotnej.
2. Upewnij się, że kopiowanie danych na urządzenie zostało ukończone, a **przygotowanie do wysłania** zakończyło się pomyślnie.
3. Wyłącz urządzenie i odłącz kable.
4. Zwiń przewód zasilający dostarczony wraz z urządzeniem i umieść go bezpiecznie z tyłu urządzenia.
5. Wyślij wiadomość e-mail do firmy Quantium Solutions, aby zlecić odbiór. Odwołaj się do numeru referencyjnego usługi określonego w witrynie Azure Portal. Użyj następującego szablonu wiadomości e-mail:- *Request for reverse shipping label with TAU code* (Żądanie zwrotnej etykiety wysyłkowej zwrotnej z kodem TAU). Upewnij się, że w wiadomości e-mail zostały uwzględnione następujące szczegóły: 

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
6. Firma Quantium Solutions Australia wyśle Ci wiadomość e-mail ze zwrotną etykietą wysyłkową.
7. Wydrukuj etykietę zwrotną i przyklej ją na opakowaniu wysyłkowym.
8. Przekaż paczkę kurierowi.

W razie potrzeby możesz skontaktować się z pomocą techniczną firmy Quantium Solution, wysyłając wiadomość e-mail na adres Azure@quantiumsolutions.com lub telefonicznie.

Aby zgłosić zapytanie dotyczące zamówienia przez telefon:

- Wyślij najpierw wiadomość e-mail dotyczącą odbioru.
- Podaj nazwę zamówienia za pomocą telefonu.

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Weryfikowanie przekazania danych na platformę Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Wymazywanie danych z urządzenia Data Box
 
Po zakończeniu przekazywania danych na platformę Azure dane z dysków urządzenia Data Box są bezpiecznie wymazywane zgodnie z wytycznymi [NIST SP 800-88 Revision 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

::: zone target="docs"

[!INCLUDE [data-box-verify-upload-return](../../includes/data-box-verify-upload-return.md)]

::: zone-end

## <a name="in-japantabin-japan"></a>[W Japonii](#tab/in-japan) 

1. Zachowaj oryginalne pudełko użyte do wysłania urządzenia na potrzeby wysyłki zwrotnej.
2. Wyłącz urządzenie i odłącz kable.
3. Zwiń przewód zasilający dostarczony wraz z urządzeniem i umieść go bezpiecznie z tyłu urządzenia.
4. Na liście przewozowym wpisz nazwę i adres swojej firmy jako dane nadawcy.
5. Wyślij wiadomość e-mail do firmy Quantium Solutions, korzystając z następującego szablonu.

    - Jeśli list przewozowy firmy Japan Post Chakubarai nie został dołączony lub został utracony, poinformuj o tym w wiadomości e-mail. Firma Quantium Solutions Japan zleci firmie Japan Post dostarczenie listu przewozowego po odebraniu przesyłki.
    - Jeśli masz wiele zamówień, wyślij stosowną wiadomość e-mail, aby zapewnić indywidualny odbiór.

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

3. Po zarezerwowaniu odbioru otrzymasz od firmy Quantium Solutions wiadomość e-mail z potwierdzeniem. Wiadomość e-mail z potwierdzeniem zawiera również informacje o liście przewozowym Chakubarai.

W razie konieczności możesz skontaktować się z pomocą techniczną firmy Quantium Solutions (język japoński), korzystając z poniższych informacji: 

- Adres e-mail: Customerservice.JP@quantiumsolutions.com 
- Numer telefonu：03-5755-0150 

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Weryfikowanie przekazania danych na platformę Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Wymazywanie danych z urządzenia Data Box
 
Po zakończeniu przekazywania danych na platformę Azure dane z dysków urządzenia Data Box są bezpiecznie wymazywane zgodnie z wytycznymi [NIST SP 800-88 Revision 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

::: zone target="docs"

[!INCLUDE [data-box-verify-upload-return](../../includes/data-box-verify-upload-return.md)]

::: zone-end



