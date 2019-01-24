---
title: Wysyłka zwrotna urządzenia Microsoft Azure Data Box | Microsoft Docs
description: Dowiedz się, jak wysłać urządzenie Azure Data Box Disk do firmy Microsoft
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 01/16/2019
ms.author: alkohli
ms.openlocfilehash: b948616aa21d390cedc877b0a09e272815abc82a
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/17/2019
ms.locfileid: "54358562"
---
# <a name="tutorial-return-azure-data-box-and-verify-data-upload-to-azure"></a>Samouczek: wysyłka zwrotna urządzenia Azure Data Box i weryfikowanie przekazania danych na platformę Azure

W tym samouczku opisano sposób zwrotu urządzenia Azure Data Box i weryfikacji danych przekazanych na platformę Azure.

Ten samouczek zawiera informacje na następujące tematy:

> [!div class="checklist"]
> * Wysyłanie urządzenia Data Box do firmy Microsoft
> * Weryfikowanie przekazania danych na platformę Azure
> * Wymazywanie danych z urządzenia Data Box

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że zostały wykonane kroki opisane w artykule [Samouczek: kopiowanie danych na urządzenie Azure Data Box i ich weryfikacja](data-box-deploy-copy-data.md).

## <a name="ship-data-box-back"></a>Wysyłka zwrotna urządzenia Data Box

1. Upewnij się, że wyłączono zasilanie urządzenia, a kable zostały odłączone. Zwiń przewód zasilający dostarczony wraz z urządzeniem i umieść go bezpiecznie z tyłu urządzenia.
2. Jeśli urządzenie jest wysyłane w Stanach Zjednoczonych, upewnij się, że na wyświetlaczu E-ink jest widoczna etykieta wysyłkowa, i umów się na odbiór przesyłki z przewoźnikiem. W przypadku utraty lub zniszczenia etykiety albo braku etykiety na wyświetlaczu E-ink pobierz etykietę wysyłkową z witryny Azure Portal i przyklej ją do przesyłki. Przejdź do pozycji **Przegląd > Pobierz etykietę wysyłkową**. 

    Jeśli urządzenie jest wysyłane w Europie, na wyświetlaczu E-ink nie jest wyświetlana etykieta wysyłkowa. Zamiast tego jest dołączona zwrotna etykieta wysyłkowa w przezroczystej kieszeni pod etykietą wysyłkową. Usuń starą etykietę wysyłkową i upewnij się, że etykieta wysyłkowa jest dobrze widoczna.
    
3. Jeśli zwracasz urządzenie, zamów odbiór paczki przez firmę UPS. Aby zaplanować odbiór, zadzwoń do lokalnego biura firmy UPS (na bezpłatny numer odpowiedni dla danego kraju) lub dostarcz urządzenie Data Box do najbliższego punktu UPS.

4. Po odebraniu urządzenia Data Box i zeskanowaniu go przez kuriera stan zamówienia w portalu zostanie zmieniony na **Pobrane**. Będzie też wyświetlany identyfikator śledzenia przesyłki.

## <a name="verify-data-upload-to-azure"></a>Weryfikowanie przekazania danych na platformę Azure

Gdy firma Microsoft odbierze i zeskanuje urządzenie, stan zamówienia zmieni się na **Odebrane**. Następnie urządzenie zostanie poddane fizycznej weryfikacji pod kątem uszkodzeń lub śladów naruszenia. 

Po zakończeniu tej weryfikacji urządzenie Data Box zostanie podłączone do sieci w centrum danych platformy Azure. Kopiowanie danych rozpocznie się automatycznie. W zależności od rozmiaru danych operacja kopiowania może potrwać od kilku godzin do kilku dni. Postęp kopiowania możesz monitorować w portalu.

Po zakończeniu kopiowania danych stan zamówienia zmieni się na **Zakończone**.

Sprawdź, czy dane znajdują się na kontach magazynu, zanim usuniesz je ze źródła. Po skopiowaniu danych na urządzenie Data Box są one zależnie od typu przekazywane do jednej z poniższych ścieżek w ramach konta usługi Azure Storage.

- W przypadku blokowych obiektów blob i stronicowych obiektów blob: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
- W przypadku usługi Azure Files: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

Możesz też przejść do swojego konta usługi Azure Storage w witrynie Azure Portal i nawigować z poziomu tej witryny.

## <a name="erasure-of-data-from-data-box"></a>Wymazywanie danych z urządzenia Data Box
 
Po zakończeniu przekazywania danych na platformę Azure dane z dysków urządzenia Data Box są bezpiecznie wymazywane zgodnie z wytycznymi [NIST SP 800-88 Revision 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi). 

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono zagadnienia dotyczące usługi Azure Data Box, takie jak:

> [!div class="checklist"]
> * Wysyłanie urządzenia Data Box do firmy Microsoft
> * Weryfikowanie przekazania danych na platformę Azure
> * Wymazywanie danych z urządzenia Data Box

Przejdź do następnego artykułu, aby dowiedzieć się, jak zarządzać urządzeniem Data Box za pomocą lokalnego internetowego interfejsu użytkownika.

> [!div class="nextstepaction"]
> [Use local web UI to administer Azure Data Box (Administrowanie urządzeniem Azure Data Box za pomocą lokalnego internetowego interfejsu użytkownika)](./data-box-local-web-ui-admin.md)


