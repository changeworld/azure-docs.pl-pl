---
title: Samouczek, należy wysłać ponownie Azure Data Box mocno | Dokumentacja firmy Microsoft
description: Dowiedz się, jak do wysłania do usługi Azure Data pole mocno do firmy Microsoft
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 84db33e4c7ac612353c590ac9d2904ac3bc48d38
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592388"
---
# <a name="tutorial-return-azure-data-box-heavy-and-verify-data-upload-to-azure"></a>Samouczek: Zwróć Azure Data Box duże i sprawdź przekazywania danych na platformie Azure


W tym samouczku opisano sposób zwracają Azure Data Box duże i sprawdzić dane przekazywane do platformy Azure.

Ten samouczek zawiera informacje na następujące tematy:

> [!div class="checklist"]
> * Wymagania wstępne
> * Przygotowanie do wysłania
> * Dostarczaj duże pole danych do firmy Microsoft
> * Weryfikowanie przekazania danych na platformę Azure
> * Wymazywania danych z dużymi pole danych

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że spełniono następujące warunki:

- Ukończono [Samouczek: kopiowanie danych na urządzenie Azure Data Box i ich weryfikacja](data-box-heavy-deploy-copy-data.md).
- Zadania kopiowania zostały wykonane. Przygotowanie do wysłania nie będzie działać, jeśli zadania kopiowania są w toku.

## <a name="prepare-to-ship"></a>Przygotowanie do wysłania

[!INCLUDE [data-box-heavy-prepare-to-ship](../../includes/data-box-heavy-prepare-to-ship.md)]

## <a name="ship-data-box-heavy-back"></a>Dostarczanie danych pole mocno Wstecz

1. Upewnij się, że urządzenia jest wyłączona, a wszystkie kable są usuwane. Buforu i bezpiecznie umieść przewodów zasilania 4 w zasobniku, której będziesz mieć dostęp z tyłu urządzenia.
2. Urządzenie jest dostarczany Fracht LTL za pośrednictwem FedEx w Stanach Zjednoczonych i DHL w UE w sprawie

    1. Dotrzyj do [operacje na danych pole](mailto:DataBoxOps@microsoft.com) informuje o odbiór i Pobierz zwrotną etykietę wysyłkową.
    2. Zadzwoń pod numer lokalny dla operatora wysyłki można zaplanować odbiór.
    3. Upewnij się, że etykieta wysyłkowa jest wyróżniane na zewnątrz wydania.
    4. Upewnij się, że stary etykiety wysyłki z poprzedniego wydania są usuwane z urządzenia.
3. Gdy duże pole danych jest pobierane i skanowania przez operatora, raportowanie stanu zamówienia w portalu aktualizacje **pobierane**. Będzie też wyświetlany identyfikator śledzenia przesyłki.

## <a name="verify-data-upload-to-azure"></a>Weryfikowanie przekazania danych na platformę Azure

Gdy firma Microsoft odbierze i zeskanuje urządzenie, stan zamówienia zmieni się na **Odebrane**. Następnie urządzenie zostanie poddane fizycznej weryfikacji pod kątem uszkodzeń lub śladów naruszenia.

Po zakończeniu weryfikacji duże pole danych jest podłączony do sieci w centrum danych platformy Azure. Kopiowanie danych rozpocznie się automatycznie. W zależności od rozmiaru danych operacja kopiowania może potrwać od kilku godzin do kilku dni. Postęp kopiowania możesz monitorować w portalu.

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

## <a name="erasure-of-data-from-data-box-heavy"></a>Wymazywania danych z dużymi pole danych
 
Po zakończeniu przekazywania danych na platformę Azure dane z dysków urządzenia Data Box są bezpiecznie wymazywane zgodnie z wytycznymi [NIST SP 800-88 Revision 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi). Po zakończeniu wymazywania możesz [pobrać historii zamówień](data-box-portal-admin.md#download-order-history).

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku przedstawiono zagadnienia dotyczące usługi Azure Data Box, takie jak:

> [!div class="checklist"]
> * Wymagania wstępne
> * Przygotowanie do wysłania
> * Dostarczaj duże pole danych do firmy Microsoft
> * Weryfikowanie przekazania danych na platformę Azure
> * Wymazywania danych z dużymi pole danych

Przejdź z następującym artykułem, aby dowiedzieć się, jak zarządzać duże pole danych za pomocą lokalnego Interfejsu w przeglądarce.

> [!div class="nextstepaction"]
> [Use local web UI to administer Azure Data Box (Administrowanie urządzeniem Azure Data Box za pomocą lokalnego internetowego interfejsu użytkownika)](./data-box-local-web-ui-admin.md)


