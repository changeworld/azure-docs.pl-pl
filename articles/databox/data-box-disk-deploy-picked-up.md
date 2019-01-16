---
title: Wysyłka zwrotna urządzenia Microsoft Azure Data Box Disk | Microsoft Docs
description: Z tego samouczka dowiesz się, jak wysłać urządzenie Azure Data Box Disk do firmy Microsoft
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 01/09/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: 357fa8a34afc8b426d308940462e22895130169f
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/09/2019
ms.locfileid: "54158775"
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
2. Zapakuj wszystkie dyski i kable połączeniowe w folię bąbelkową, a następnie umieść w opakowaniu wysyłkowym.
3. Użyj zwrotnej etykiety wysyłkowej, znajdującej się w przezroczystej koszulce przyklejonej do opakowania. W przypadku utraty lub zniszczenia etykiety pobierz nową etykietę wysyłkową z witryny Azure Portal i przyklej ją do przesyłki. Przejdź do pozycji **Przegląd > Pobierz etykietę wysyłkową**. 

    ![Pobieranie etykiety wysyłkowej](media/data-box-disk-deploy-picked-up/download-shipping-label.png)

    To spowoduje pobranie zwrotnej etykiety wysyłkowej, podobnej do tej widocznej poniżej.

    ![Przykładowa etykieta wysyłkowa](media/data-box-disk-deploy-picked-up/exmple-shipping-label.png)

4. Zamknij i zaklej opakowanie wysyłkowe. Upewnij się, że zwrotna etykieta wysyłkowa jest widoczna.
5. Jeśli zwracasz urządzenie na terenie USA, zamów odbiór paczki przez firmę UPS. Jeśli zwracasz urządzenie w Europie za pośrednictwem firmy DHL, zamów odbiór paczki przez firmę DHL w witrynie internetowej firmy, podając numer listu przewozowego. Przejdź do lokalnej witryny firmy DHL Express i wybierz pozycję **Utwórz przesyłkę zwrotną**.

    ![DHL — przesyłka zwrotna](media/data-box-disk-deploy-picked-up/dhl-ship-1.png)
    
    Podaj numer listu przewozowego i kliknij przycisk **Zamówienie kuriera**, aby zaplanować odebranie przesyłki.

      ![Zamówienie kuriera](media/data-box-disk-deploy-picked-up/dhl-ship-2.png)

7. Po odebraniu dysków przez kuriera stan zamówienia w portalu zostanie zmieniony na **Pobrane**. Będzie też wyświetlany identyfikator śledzenia przesyłki.

    ![Dyski zostały pobrane](media/data-box-disk-deploy-picked-up/data-box-portal-pickedup.png)

## <a name="verify-data-upload-to-azure"></a>Weryfikowanie przekazania danych na platformę Azure

Gdy firma Microsoft odbierze i zeskanuje dysk, stan zadania zmieni się na **Odebrane**. 

![Dyski zostały odebrane](media/data-box-disk-deploy-picked-up/data-box-portal-received.png)

Dane zostaną skopiowane automatycznie po podłączeniu dysków do serwera w centrum danych Azure. W zależności od rozmiaru danych operacja kopiowania może potrwać od kilku godzin do kilku dni. Postęp kopiowania możesz monitorować w portalu.

Po zakończeniu kopiowania danych stan zamówienia zmieni się na **Zakończone**.

![Kopiowanie danych zostało zakończone](media/data-box-disk-deploy-picked-up/data-box-portal-completed.png)

Sprawdź, czy dane znajdują się na kontach magazynu, zanim usuniesz je ze źródła. Aby sprawdzić, czy dane zostały przekazane na platformę Azure, wykonaj następujące czynności:

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


