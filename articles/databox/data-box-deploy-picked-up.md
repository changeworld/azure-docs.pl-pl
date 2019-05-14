---
title: Samouczka, aby odesłanie urządzenia Azure Data Box | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wysłać urządzenie Azure Data Box Disk do firmy Microsoft
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 03/19/2019
ms.author: alkohli
ms.openlocfilehash: 5b43241be4e161cd6051dce02a3574fbdb580f28
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2019
ms.locfileid: "65606233"
---
# <a name="tutorial-return-azure-data-box-and-verify-data-upload-to-azure"></a>Samouczek: wysyłka zwrotna urządzenia Azure Data Box i weryfikowanie przekazania danych na platformę Azure

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

## <a name="ship-data-box-back"></a>Wysyłka zwrotna urządzenia Data Box

1. Upewnij się, że wyłączono zasilanie urządzenia, a kable zostały odłączone. Zwiń przewód zasilający dostarczony wraz z urządzeniem i umieść go bezpiecznie z tyłu urządzenia.
2. Upewnij się, że na wyświetlaczu E-ink jest widoczna etykieta wysyłkowa, i umów się na odbiór przesyłki z przewoźnikiem. Jeśli etykieta jest uszkodzona, nie ma jej lub nie jest widoczna na wyświetlaczu E-ink, skontaktuj się z pomocą techniczną firmy Microsoft. Jeśli sugeruje pomocy technicznej, a następnie możesz przejść do **Przegląd > Pobierz etykietę wysyłkową** w witrynie Azure portal. Pobierz etykietę wysyłkową i dołącz ją do urządzenia. 
3. Jeśli zwracasz urządzenie, zamów odbiór paczki przez firmę UPS. Aby zaplanować odbioru:

    - Wywołanie lokalne UPS (specyficzne dla kraju/regionu darmowy numer).
    - W swojej rozmowy oferty odwrotnej wydanie numer, jak pokazano na wyświetlanie pisma odręcznego E lub drukowane etykiety śledzenia.
    - Jeśli numer śledzenia nie jest ujęty w cudzysłów, UPS będą wymagać dodatkowych opłaty są naliczane podczas odbioru.

    Zamiast planować odbiór, można również usunąć wyłączyć urządzenie Data Box w najbliższej lokalizacji nadania.
4. Po odebraniu urządzenia Data Box i zeskanowaniu go przez kuriera stan zamówienia w portalu zostanie zmieniony na **Pobrane**. Będzie też wyświetlany identyfikator śledzenia przesyłki.

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
> * Przygotowywanie do wysłania
> * Wysyłanie urządzenia Data Box do firmy Microsoft
> * Weryfikowanie przekazania danych na platformę Azure
> * Wymazywanie danych z urządzenia Data Box

Przejdź do następnego artykułu, aby dowiedzieć się, jak zarządzać urządzeniem Data Box za pomocą lokalnego internetowego interfejsu użytkownika.

> [!div class="nextstepaction"]
> [Use local web UI to administer Azure Data Box (Administrowanie urządzeniem Azure Data Box za pomocą lokalnego internetowego interfejsu użytkownika)](./data-box-local-web-ui-admin.md)


