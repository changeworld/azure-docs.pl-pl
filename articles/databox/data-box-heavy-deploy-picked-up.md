---
title: Samouczek do dostarczania Azure Data Box Heavy z powrotem | Microsoft Docs
description: Dowiedz się, jak dostarczyć Azure Data Box Heavy do firmy Microsoft
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: e438fb38afb649f6f4c7f595059ef64800977242
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70240343"
---
::: zone target = "docs"

# <a name="tutorial-return-azure-data-box-heavy-and-verify-data-upload-to-azure"></a>Samouczek: Zwróć Azure Data Box Heavy i Zweryfikuj przekazywanie danych na platformę Azure

::: zone-end

::: zone target = "chromeless"

# <a name="return-azure-data-box-heavy-and-verify-data-upload-to-azure"></a>Zwróć Azure Data Box Heavy i Zweryfikuj przekazywanie danych na platformę Azure

::: zone-end

::: zone target = "docs"

W tym samouczku opisano, jak zwrócić Azure Data Box Heavy i zweryfikować dane przekazane do platformy Azure.

Ten samouczek zawiera informacje na następujące tematy:

> [!div class="checklist"]
> * Wymagania wstępne
> * Przygotowywanie do wysłania
> * Wyślij Data Box Heavy do firmy Microsoft
> * Weryfikowanie przekazania danych na platformę Azure
> * Wymazywanie danych z Data Box Heavy

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że spełniono następujące warunki:

- Ukończono [Samouczek: kopiowanie danych na urządzenie Azure Data Box i ich weryfikacja](data-box-heavy-deploy-copy-data.md).
- Zadania kopiowania zostały wykonane. Przygotowanie do wysłania nie będzie działać, jeśli zadania kopiowania są w toku.


## <a name="prepare-to-ship"></a>Przygotowywanie do wysłania

[!INCLUDE [data-box-heavy-prepare-to-ship](../../includes/data-box-heavy-prepare-to-ship.md)]

::: zone-end

::: zone target = "chromeless"

## <a name="prepare-to-ship"></a>Przygotowywanie do wysłania

Przed przygotowaniem do wysłania upewnij się, że zadania kopiowania zostały ukończone.

1. Przejdź do strony przygotowanie do wysłania w lokalnym interfejsie użytkownika sieci Web i Rozpocznij przygotowywanie do wysłania.
2. Wyłącz urządzenie za pomocą lokalnego internetowego interfejsu użytkownika. Odłącz przewody od urządzenia.

Teraz możesz przystąpić do ponownego wysłania urządzenia.

::: zone-end

## <a name="ship-data-box-heavy-back"></a>Wyślij Data Box Heavy z powrotem

1. Upewnij się, że urządzenie jest wyłączone i wszystkie kable są usuwane. Buforowanie i bezpieczne umieszcza 4 przewody zasilające w zasobniku, do którego można uzyskać dostęp z tyłu urządzenia.
2. Urządzenie dostarcza LTL transport za pośrednictwem FedEx w Stanach Zjednoczonych i DHL w Unii Europejskiej.

    1. Uzyskaj dostęp do [urządzenie Data Box operacji](mailto:DataBoxOps@microsoft.com) , aby informować o odbiorze i uzyskać etykietę wysyłki zwrotnej.
    2. Wywołaj numer lokalny dla operatora wysyłki, aby zaplanować odbiór.
    3. Upewnij się, że etykieta wysyłkowa jest wyświetlana w widocznym miejscu na zewnątrz wysyłki.
    4. Upewnij się, że stare etykiety wysyłkowe z poprzedniego wydania są usuwane z urządzenia.
3. Gdy Data Box Heavy zostanie pobrany i zeskanowany przez przewoźnika, stan zamówienia w portalu zostanie zaktualizowany do **pobrania**. Będzie też wyświetlany identyfikator śledzenia przesyłki.

::: zone target = "docs"

## <a name="verify-data-upload-to-azure"></a>Weryfikowanie przekazania danych na platformę Azure

Gdy firma Microsoft odbierze i zeskanuje urządzenie, stan zamówienia zmieni się na **Odebrane**. Następnie urządzenie zostanie poddane fizycznej weryfikacji pod kątem uszkodzeń lub śladów naruszenia.

Po zakończeniu weryfikacji Data Box Heavy jest połączona z siecią w centrum danych platformy Azure. Kopiowanie danych rozpocznie się automatycznie. W zależności od rozmiaru danych operacja kopiowania może potrwać od kilku godzin do kilku dni. Postęp kopiowania możesz monitorować w portalu.

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

::: zone target = "chromeless"

## <a name="verify-data-upload-to-azure"></a>Weryfikowanie przekazania danych na platformę Azure

Gdy urządzenie Data Box Heavy jest połączone z siecią centrum danych platformy Azure, zostanie automatycznie uruchomione przekazywanie na platformę Azure. Usługa urządzenie Data Box powiadamia o ukończeniu kopiowania danych za pośrednictwem Azure Portal.

- Sprawdź dzienniki błędów pod kątem ewentualnych niepowodzeń i podejmij odpowiednie działania.
- Sprawdź, czy dane znajdują się na kontach magazynu, zanim usuniesz je ze źródła.

::: zone-end

## <a name="erasure-of-data-from-data-box-heavy"></a>Wymazywanie danych z Data Box Heavy
 
Po zakończeniu przekazywania danych na platformę Azure dane z dysków urządzenia Data Box są bezpiecznie wymazywane zgodnie z wytycznymi [NIST SP 800-88 Revision 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi). Po ukończeniu wymazywania można [pobrać historię zamówień](data-box-portal-admin.md#download-order-history).

::: zone target = "docs"

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono zagadnienia dotyczące usługi Azure Data Box, takie jak:

> [!div class="checklist"]
> * Wymagania wstępne
> * Przygotowywanie do wysłania
> * Wyślij Data Box Heavy do firmy Microsoft
> * Weryfikowanie przekazania danych na platformę Azure
> * Wymazywanie danych z Data Box Heavy

Przejdź do poniższego artykułu, aby dowiedzieć się, jak zarządzać Data Box Heavy za pośrednictwem lokalnego interfejsu użytkownika sieci Web.

> [!div class="nextstepaction"]
> [Use local web UI to administer Azure Data Box (Administrowanie urządzeniem Azure Data Box za pomocą lokalnego internetowego interfejsu użytkownika)](./data-box-local-web-ui-admin.md)

::: zone-end


