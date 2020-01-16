---
title: Samouczek — Tworzenie eksportowanych danych i zarządzanie nimi z Azure Cost Management
description: W tym artykule pokazano, jak można tworzyć eksportowane Azure Cost Management dane i zarządzać nimi, aby można było ich używać w systemach zewnętrznych.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 01/14/2020
ms.topic: tutorial
ms.service: cost-management-billing
manager: jasonh
ms.custom: seodec18
ms.openlocfilehash: 76ee5aba0f1d0769e15a5969409dfef2a018e477
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75987285"
---
# <a name="tutorial-create-and-manage-exported-data"></a>Samouczek: eksportowanie danych i zarządzanie nimi

Jeśli znasz samouczek Analiza kosztów, już wiesz, jak ręcznie pobierać dane usługi Cost Management. Jednak możesz utworzyć cykliczne zadanie, które będzie automatycznie eksportowało dane usługi Cost Management do magazynu platformy Azure — codziennie, co tydzień lub co miesiąc. Dane są eksportowane w formacie CSV i zawierają wszystkie informacje zebrane przez usługę Cost Management. Wyeksportowanych danych znajdujących się w magazynie platformy Azure możesz następnie używać z systemami zewnętrznymi, łącząc je z własnymi danymi niestandardowymi. Możesz również użyć ich w systemie zewnętrznym, takim jak pulpit nawigacyjny lub inny system finansowy.

Obejrzyj, [jak zaplanować eksporty do magazynu za pomocą Azure Cost Management](https://www.youtube.com/watch?v=rWa_xI1aRzo) klip wideo dotyczący tworzenia zaplanowanego eksportu danych kosztów platformy Azure do usługi Azure Storage.

Przykłady w tym samouczku przeprowadzą Cię przez proces eksportowania danych zarządzania kosztami i sprawdzania, czy dane zostały pomyślnie wyeksportowane.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie codziennego eksportu
> * Sprawdzanie, czy dane są zbierane

## <a name="prerequisites"></a>Wymagania wstępne
Eksport danych jest dostępny dla różnych typów kont platformy Azure, w tym [Umowa Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) i klienci z [umowami od firmy Microsoft](get-started-partners.md) . Aby wyświetlić pełną listę obsługiwanych typów kont, zobacz [Omówienie danych usługi Cost Management](understand-cost-mgt-data.md). Następujące uprawnienia lub zakresy platformy Azure są obsługiwane na subskrypcję na potrzeby eksportu danych przez użytkownika i grupę. Aby uzyskać więcej informacji na temat zakresów, zobacz [Opis i współpraca z zakresami](understand-work-scopes.md).

- Właściciel — może tworzyć, modyfikować i usuwać zaplanowane eksporty dla subskrypcji.
- Współautor — może tworzyć, modyfikować i usuwać zaplanowane przez siebie eksporty. Może modyfikować nazwy zaplanowanych eksportów utworzonych przez innych użytkowników.
- Czytelnik — może planować eksporty, do których ma uprawnienia.

W przypadku kont usługi Azure Storage:
- Niezależnie od uprawnień dotyczących eksportu, do zmiany skonfigurowanego konta magazynu wymagane są uprawnienia zapisu.
- Twoje konto magazynu platformy Azure musi być skonfigurowane jako magazyn obiektów blob lub magazyn plików.

## <a name="sign-in-to-azure"></a>Zaloguj się w usłudze Azure
Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com/).

## <a name="create-a-daily-export"></a>Tworzenie codziennego eksportu

Aby utworzyć lub wyświetlić eksport danych lub zaplanować eksport, otwórz żądany zakres w Azure Portal a następnie wybierz pozycję **Analiza kosztów** w menu. Na przykład przejdź do **subskrypcji**, wybierz subskrypcję z listy, a następnie wybierz pozycję **Analiza kosztów** w menu. W górnej części strony Analiza kosztów wybierz pozycję **Eksportuj** a następnie wybierz opcję eksportowania. Na przykład wybierz pozycję **Zaplanuj eksport**.  

> [!NOTE]
> - Oprócz subskrypcji można tworzyć eksporty dla grup zasobów, kont, działów i rejestracji. Aby uzyskać więcej informacji na temat zakresów, zobacz [Opis i współpraca z zakresami](understand-work-scopes.md).
>- Gdy logujesz się jako partner w zakresie konta rozliczeń lub dzierżawcy klienta, możesz wyeksportować dane do konta usługi Azure Storage połączonego z Twoim kontem magazynu partnerskiego. Należy jednak mieć aktywną subskrypcję w dzierżawie dostawcy usług kryptograficznych.
>


Wybierz pozycję **Dodaj**, wpisz nazwę eksportu, a następnie wybierz opcję **dzienny eksport kosztów miesiąca do daty** . Wybierz opcję **Dalej**.

![Przykład nowego eksportu z pokazanym typem eksportu](./media/tutorial-export-acm-data/basics_exports.png)

Podaj subskrypcję zawierającą konto usługi Azure Storage, a następnie wybierz konto usługi Storage.  Określ ścieżkę kontenera i katalogu do zapisania wyeksportowanego pliku. Wybierz opcję **Dalej**.

![Przykład nowego eksportu z pokazanymi szczegółami konta magazynu](./media/tutorial-export-acm-data/storage_exports.png)

Przejrzyj szczegóły eksportu, a następnie wybierz przycisk **Utwórz**.

Nowy eksport pojawi się na liście eksportów. Domyślnie nowe zadania eksportu są włączone. Jeśli chcesz wyłączyć lub usunąć zaplanowany eksport, zaznacz dowolny element na liście, a następnie wybierz opcję **Wyłącz** lub **Usuń**.

Początkowo uruchomienie eksportu może zająć od jednej do dwóch godzin. Jednak wyświetlenie danych w wyeksportowanych plikach może potrwać nawet cztery godziny.

### <a name="export-schedule"></a>Harmonogram eksportu

Na zaplanowane eksporty wpływa czas (godzina i dzień tygodnia) początkowego utworzenia eksportu. Podczas tworzenia zaplanowanego eksportu, eksport przebiega z taką samą częstotliwością dla każdego kolejnego wystąpienia eksportu. Na przykład w przypadku ustawienia Eksportuj z dowolnego miesiąca na dzień, eksport przebiega codziennie. Podobnie w przypadku eksportu tygodniowego Eksport jest uruchamiany co tydzień w tym samym dniu, w którym jest zaplanowana. Dokładny czas dostarczania eksportu nie jest gwarantowany, a eksportowane dane są dostępne w ciągu czterech godzin czasu wykonywania.
Dla każdego eksportu tworzony jest nowy plik, więc starsze eksporty nie są zastępowane.

Istnieją dwa typy opcji eksportu:

**Codzienny eksport kosztów od początku miesiąca** — początkowy eksport jest uruchamiany natychmiast. Kolejne eksporty są uruchamiane następnego dnia o tej samej godzinie, co eksport początkowy. Najnowsze dane są agregowane z poprzednich codziennych eksportów.

**Niestandardowe** — umożliwia zaplanowanie cotygodniowych i comiesięcznych eksportów z opcjami od początku tygodnia i od początku miesiąca. *Początkowy eksport zostanie uruchomiony natychmiast.*

Jeśli masz subskrypcję z płatnością zgodnie z rzeczywistym użyciem, MSDN lub programu Visual Studio, okres rozliczeniowy na fakturze może nie być zgodny z miesiącem kalendarzowym. Dla tych typów subskrypcji i grup zasobów można utworzyć eksport zgodny z okresem na fakturze lub z miesiącami kalendarzowymi. Aby utworzyć eksport wyrównany do miesiąca faktury, przejdź do **niestandardowego**, a następnie wybierz pozycję **rozliczenia — okres do daty**.  Aby utworzyć eksport wyrównany do miesiąca kalendarzowego, wybierz opcję **miesiąc-do-Data**.
>
>

![Nowy eksport — karta Podstawowe przedstawiająca wybór opcji Niestandardowe, Co tydzień i Od początku tygodnia](./media/tutorial-export-acm-data/tutorial-export-schedule-weekly-week-to-date.png)

## <a name="verify-that-data-is-collected"></a>Sprawdzanie, czy dane są zbierane

Używając Eksploratora usługi Azure Storage, możesz łatwo sprawdzić, czy dane usługi Cost Management są zbierane, i otworzyć wyeksportowany plik CSV.

Na liście eksportów wybierz nazwę konta magazynu. Na stronie konto magazynu wybierz pozycję Otwórz w Eksploratorze. Jeśli zobaczysz pole potwierdzenia, wybierz pozycję **tak** , aby otworzyć plik w Eksplorator usługi Azure Storage.

![Strona konta magazynu z przykładowymi informacjami i linkiem Otwórz w programie Explorer](./media/tutorial-export-acm-data/storage-account-page.png)

W Eksploratorze usługi Storage przejdź do kontenera, który chcesz otworzyć, a następnie wybierz folder odpowiadający bieżącemu miesiącowi. Zostanie wyświetlona lista plików CSV. Wybierz jeden z nich, a następnie wybierz pozycję **Otwórz**.

![Przykładowe informacje wyświetlane w Eksploratorze usługi Storage](./media/tutorial-export-acm-data/storage-explorer.png)

Plik zostanie otwarty za pomocą programu lub aplikacji, która została skonfigurowana do otwierania plików z rozszerzeniem CSV. Oto przykład w programie Excel.

![Przykładowe wyeksportowane dane w formacie CSV wyświetlane w programie Excel](./media/tutorial-export-acm-data/example-export-data.png)


## <a name="access-exported-data-from-other-systems"></a>Uzyskiwanie dostępu do wyeksportowanych danych z poziomu innych systemów

Jednym z celów eksportowania danych usługi Cost Management jest uzyskiwanie dostępu do danych z poziomu systemów zewnętrznych. Możesz na przykład użyć systemu pulpitu nawigacyjnego lub innego systemu finansowego. Takie systemy są bardzo zróżnicowane, dlatego pokazanie przykładu byłoby niepraktyczne.  Jednak możesz zacząć od sposobu uzyskiwania dostępu do danych z poziomu Twoich aplikacji w artykule [Wprowadzenie do usługi Azure Storage](../../storage/common/storage-introduction.md).

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie codziennego eksportu
> * Sprawdzanie, czy dane są zbierane

Przejdź do następnego samouczka, aby przeprowadzić optymalizację i zwiększyć wydajność, identyfikując zasoby w stanie bezczynności i niedostatecznie używane.

> [!div class="nextstepaction"]
> [Zapoznawanie się z zaleceniami dotyczącymi optymalizacji i ich wdrażanie](tutorial-acm-opt-recommendations.md)
