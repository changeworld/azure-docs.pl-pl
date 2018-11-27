---
title: Samouczek — eksportowanie danych z usługi Azure Cost Management i zarządzanie nimi | Microsoft Docs
description: W tym artykule pokazano, jak eksportować dane usługi Azure Cost Management i zarządzać nimi, aby można było używać ich w systemach zewnętrznych.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/21/2018
ms.topic: tutorial
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: e671c75b1ceee0e42b3af9ddc149edf2f3b0040c
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/16/2018
ms.locfileid: "51822368"
---
# <a name="tutorial-create-and-manage-exported-data"></a>Samouczek: eksportowanie danych i zarządzanie nimi

Jeśli znasz samouczek Analiza kosztów, już wiesz, jak ręcznie pobierać dane usługi Cost Management. Jednak możesz utworzyć cykliczne zadanie, które będzie automatycznie eksportowało dane usługi Cost Management do magazynu platformy Azure — codziennie, co tydzień lub co miesiąc. Dane są eksportowane w formacie CSV i zawierają wszystkie informacje zebrane przez usługę Cost Management. Wyeksportowanych danych znajdujących się w magazynie platformy Azure możesz następnie używać z systemami zewnętrznymi, łącząc je z własnymi danymi niestandardowymi. Możesz również użyć ich w systemie zewnętrznym, takim jak pulpit nawigacyjny lub inny system finansowy.

Przykłady w tym samouczku przeprowadzą Cię przez proces eksportowania danych zarządzania kosztami i sprawdzania, czy dane zostały pomyślnie wyeksportowane.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie codziennego eksportu
> * Sprawdzanie, czy dane są zbierane

## <a name="prerequisites"></a>Wymagania wstępne

Eksport danych jest dostępny dla wszystkich klientów z umową [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/). Na potrzeby eksportu danych przez użytkownika i grupę w ramach subskrypcji są obsługiwane następujące uprawnienia platformy Azure:

- Właściciel — może tworzyć, modyfikować i usuwać zaplanowane eksporty dla subskrypcji.
- Współautor — może tworzyć, modyfikować i usuwać zaplanowane przez siebie eksporty. Może modyfikować nazwy zaplanowanych eksportów utworzonych przez innych użytkowników.
- Czytelnik — może planować eksporty, do których ma uprawnienia.

W przypadku kont usługi Azure Storage:
- Niezależnie od uprawnień dotyczących eksportu, do zmiany skonfigurowanego konta magazynu wymagane są uprawnienia zapisu.
- Twoje konto magazynu platformy Azure musi być skonfigurowane jako magazyn obiektów blob lub magazyn plików.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure
Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com/).

## <a name="create-a-daily-export"></a>Tworzenie codziennego eksportu

Zarządzanie kosztami i rozliczenia &gt; Cost Management &gt; wybierz subskrypcję lub grupę zasobów w ramach subskrypcji &gt; Eksportuj &gt; **Dodaj**.

Wpisz nazwę zadania eksportu i wybierz opcję „Codzienny eksport kosztów od początku miesiąca”. Kliknij przycisk **Dalej**.

![Nowy eksport: typ eksportu](./media/tutorial-export-acm-data/basics_exports.png)

Podaj subskrypcję zawierającą konto usługi Azure Storage, a następnie wybierz konto usługi Storage.  Określ ścieżkę kontenera i katalogu do zapisania wyeksportowanego pliku.  Kliknij przycisk **Dalej**.

![Nowy eksport: magazyn](./media/tutorial-export-acm-data/storage_exports.png)

Przejrzyj szczegóły eksportu, a następnie kliknij przycisk **Utwórz**.

Nowy eksport pojawi się na liście eksportów. Domyślnie nowe zadania eksportu są włączone. Jeśli chcesz wyłączyć lub usunąć zaplanowany eksport, kliknij dowolny element na liście, a następnie kliknij pozycję **Wyłącz** lub **Usuń**.

Początkowo uruchomienie eksportu może zająć od jednej do dwóch godzin. Jednak wyświetlenie danych w wyeksportowanych plikach może potrwać nawet cztery godziny.

## <a name="verify-that-data-is-collected"></a>Sprawdzanie, czy dane są zbierane

Używając Eksploratora usługi Azure Storage, możesz łatwo sprawdzić, czy dane usługi Cost Management są zbierane, i otworzyć wyeksportowany plik CSV.

Na liście eksportów kliknij nazwę konta magazynu. Na stronie konta magazynu kliknij pozycję Otwórz w Eksploratorze. Jeśli zostanie wyświetlone okno dialogowe potwierdzenia, kliknij przycisk **Tak**, aby otworzyć plik w Eksploratorze usługi Azure Storage.

![Strona konta magazynu](./media/tutorial-export-acm-data/storage-account-page.png)

W Eksploratorze usługi Storage przejdź do kontenera, który chcesz otworzyć, a następnie wybierz folder odpowiadający bieżącemu miesiącowi. Zostanie wyświetlona lista plików CSV. Wybierz jeden z nich, a następnie kliknij przycisk **Otwórz**.

![Storage Explorer](./media/tutorial-export-acm-data/storage-explorer.png)

Plik zostanie otwarty za pomocą programu lub aplikacji, która została skonfigurowana do otwierania plików z rozszerzeniem CSV. Oto przykład w programie Excel.

![Przykładowe dane eksportu](./media/tutorial-export-acm-data/example-export-data.png)

## <a name="access-exported-data-from-other-systems"></a>Uzyskiwanie dostępu do wyeksportowanych danych z poziomu innych systemów

Jednym z celów eksportowania danych usługi Cost Management jest uzyskiwanie dostępu do danych z poziomu systemów zewnętrznych. Możesz na przykład użyć systemu pulpitu nawigacyjnego lub innego systemu finansowego. Takie systemy są bardzo zróżnicowane, dlatego pokazanie przykładu byłoby niepraktyczne.  Jednak możesz zacząć od sposobu uzyskiwania dostępu do danych z poziomu Twoich aplikacji w artykule [Wprowadzenie do usługi Azure Storage](../storage/common/storage-introduction.md).

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie codziennego eksportu
> * Sprawdzanie, czy dane są zbierane

Przejdź do następnego samouczka, aby przeprowadzić optymalizację i zwiększyć wydajność, identyfikując zasoby w stanie bezczynności i niedostatecznie używane.

> [!div class="nextstepaction"]
> [Zapoznawanie się z zaleceniami dotyczącymi optymalizacji i ich wdrażanie](tutorial-acm-opt-recommendations.md)
