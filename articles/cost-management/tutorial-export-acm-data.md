---
title: Samouczek — eksportowanie danych z usługi Azure Cost Management i zarządzanie nimi | Microsoft Docs
description: W tym artykule dowiesz się, jak tworzyć i zarządzać wyeksportowane dane usługi Azure Cost Management, tak aby można go użyć w systemach zewnętrznych.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/14/2019
ms.topic: tutorial
ms.service: cost-management
manager: dougeby
ms.custom: seodec18
ms.openlocfilehash: 521a5f2543b9a4a84d50f8f0e53a6ae5108f760b
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65792888"
---
# <a name="tutorial-create-and-manage-exported-data"></a>Samouczek: Eksportowanie danych i zarządzanie nimi

Jeśli znasz samouczek Analiza kosztów, już wiesz, jak ręcznie pobierać dane usługi Cost Management. Jednak możesz utworzyć cykliczne zadanie, które będzie automatycznie eksportowało dane usługi Cost Management do magazynu platformy Azure — codziennie, co tydzień lub co miesiąc. Dane są eksportowane w formacie CSV i zawierają wszystkie informacje zebrane przez usługę Cost Management. Wyeksportowanych danych znajdujących się w magazynie platformy Azure możesz następnie używać z systemami zewnętrznymi, łącząc je z własnymi danymi niestandardowymi. Możesz również użyć ich w systemie zewnętrznym, takim jak pulpit nawigacyjny lub inny system finansowy.

Obejrzyj [sposób tworzenia harmonogramu eksportuje do magazynu przy użyciu usługi Azure Cost Management](https://www.youtube.com/watch?v=rWa_xI1aRzo) wideo o tworzeniu zaplanowanego eksportu platformy Azure koszt danych do usługi Azure Storage.

Przykłady w tym samouczku przeprowadzą Cię przez proces eksportowania danych zarządzania kosztami i sprawdzania, czy dane zostały pomyślnie wyeksportowane.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie codziennego eksportu
> * Sprawdzanie, czy dane są zbierane

## <a name="prerequisites"></a>Wymagania wstępne
Eksportowanie danych jest dostępne dla różnych typów konta platformy Azure, w tym dla klientów z umową [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/). Aby wyświetlić pełną listę obsługiwanych typów kont, zobacz [Omówienie danych usługi Cost Management](understand-cost-mgt-data.md). Następujące uprawnienia platformy Azure lub zakresów, są obsługiwane na subskrypcję do eksportu danych przez użytkowników i grup. Aby uzyskać więcej informacji na temat zakresów, zobacz [poznawanie i Praca z zakresami](understand-work-scopes.md).

- Właściciel — może tworzyć, modyfikować i usuwać zaplanowane eksporty dla subskrypcji.
- Współautor — może tworzyć, modyfikować i usuwać zaplanowane przez siebie eksporty. Może modyfikować nazwy zaplanowanych eksportów utworzonych przez innych użytkowników.
- Czytelnik — może planować eksporty, do których ma uprawnienia.

W przypadku kont usługi Azure Storage:
- Niezależnie od uprawnień dotyczących eksportu, do zmiany skonfigurowanego konta magazynu wymagane są uprawnienia zapisu.
- Twoje konto magazynu platformy Azure musi być skonfigurowane jako magazyn obiektów blob lub magazyn plików.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure
Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com/).

## <a name="create-a-daily-export"></a>Tworzenie codziennego eksportu

Do tworzenia lub wyświetlania Eksport danych lub zaplanować eksportu, otwórz żądany zakres w witrynie Azure portal i wybierz pozycję **analiza kosztów** w menu. Na przykład, przejdź do **subskrypcje**, wybierz subskrypcję z listy, a następnie wybierz **analiza kosztów** w menu. W górnej części strony analizy kosztów kliknij **wyeksportować** , a następnie opcję eksportu. Na przykład kliknij pozycję **zaplanować eksportu**.  

> [!NOTE]
> Oprócz subskrypcji można tworzyć eksporty na grupy zasobów, konta, działów i rejestracji. Aby uzyskać więcej informacji na temat zakresów, zobacz [poznawanie i Praca z zakresami](understand-work-scopes.md).
>
>


Kliknij przycisk **Dodaj**, wpisz nazwę eksportu, a następnie wybierz **eksportu dziennych kosztów od początku miesiąca** opcji. Kliknij przycisk **Dalej**.

![Przykład nowego eksportu z pokazanym typem eksportu](./media/tutorial-export-acm-data/basics_exports.png)

Podaj subskrypcję zawierającą konto usługi Azure Storage, a następnie wybierz konto usługi Storage.  Określ ścieżkę kontenera i katalogu do zapisania wyeksportowanego pliku.  Kliknij przycisk **Dalej**.

![Przykład nowego eksportu z pokazanymi szczegółami konta magazynu](./media/tutorial-export-acm-data/storage_exports.png)

Przejrzyj szczegóły eksportu, a następnie kliknij przycisk **Utwórz**.

Nowy eksport pojawi się na liście eksportów. Domyślnie nowe zadania eksportu są włączone. Jeśli chcesz wyłączyć lub usunąć zaplanowany eksport, kliknij dowolny element na liście, a następnie kliknij pozycję **Wyłącz** lub **Usuń**.

Początkowo uruchomienie eksportu może zająć od jednej do dwóch godzin. Jednak wyświetlenie danych w wyeksportowanych plikach może potrwać nawet cztery godziny.

### <a name="export-schedule"></a>Harmonogram eksportu

Na zaplanowane eksporty wpływa czas (godzina i dzień tygodnia) początkowego utworzenia eksportu. Po utworzeniu zaplanowanego eksportu eksport będzie uruchamiany o tej samej porze dnia dla każdego kolejnego wystąpienia eksportu. Na przykład załóżmy, że codzienny eksport utworzysz o 13:00. Następny eksport zostanie uruchomiony o 13:00 następnego dnia. Bieżący czas (godzina i dzień) wpływa w ten sam sposób na wszystkie pozostałe typy eksportu — są zawsze uruchamiane o tej samej godzinie dnia, co w chwili początkowego utworzenia. W innym przykładzie utworzysz cotygodniowy eksport w poniedziałek o 16:00. Następny eksport zostanie uruchomiony o 16:00 w następny poniedziałek. *Wyeksportowane dane będą dostępne w ciągu czterech godzin od czasu uruchomienia.*

Dla każdego eksportu tworzony jest nowy plik, więc starsze eksporty nie są zastępowane.

Istnieją trzy typy opcji eksportu:

**Codzienny eksport kosztów od początku miesiąca** — początkowy eksport jest uruchamiany natychmiast. Kolejne eksporty są uruchamiane następnego dnia o tej samej godzinie, co eksport początkowy. Najnowsze dane są agregowane z poprzednich codziennych eksportów.

**Tygodniowy eksport kosztów z ostatnich 7 dni** — początkowy eksport jest uruchamiany natychmiast. Kolejne eksporty są uruchamiane w następnych tygodniach w tym samym dniu tygodnia i o tej samej godzinie, co eksport początkowy. Uwzględniane są koszty z ostatnich siedmiu dni.

**Niestandardowe** — umożliwia zaplanowanie cotygodniowych i comiesięcznych eksportów z opcjami od początku tygodnia i od początku miesiąca. *Początkowy eksport zostanie uruchomiony natychmiast.*

Jeśli masz subskrypcję płatność za rzeczywiste użycie, MSDN lub Visual Studio, okresu rozliczeniowego faktur mogą być niewyrównane z miesiącem kalendarzowym. Dla tych typów subskrypcji i grup zasobów można utworzyć eksportu, który jest wyrównany do okresu faktury lub miesięcy kalendarzowych. Aby utworzyć Eksport wyrównane do miesiąca Twoja faktury, przejdź do **niestandardowe**, a następnie wybierz **rozliczeń okres do chwili**.  Aby utworzyć Eksport wyrównane z miesiącem kalendarzowym, wybierz **data miesiąca**.
>
>

![Nowy eksport — karta Podstawowe przedstawiająca wybór opcji Niestandardowe, Co tydzień i Od początku tygodnia](./media/tutorial-export-acm-data/tutorial-export-schedule-weekly-week-to-date.png)

## <a name="verify-that-data-is-collected"></a>Sprawdzanie, czy dane są zbierane

Używając Eksploratora usługi Azure Storage, możesz łatwo sprawdzić, czy dane usługi Cost Management są zbierane, i otworzyć wyeksportowany plik CSV.

Na liście eksportów kliknij nazwę konta magazynu. Na stronie konta magazynu kliknij pozycję Otwórz w Eksploratorze. Jeśli zostanie wyświetlone okno dialogowe potwierdzenia, kliknij przycisk **Tak**, aby otworzyć plik w Eksploratorze usługi Azure Storage.

![Strona konta magazynu z przykładowymi informacjami i linkiem Otwórz w programie Explorer](./media/tutorial-export-acm-data/storage-account-page.png)

W Eksploratorze usługi Storage przejdź do kontenera, który chcesz otworzyć, a następnie wybierz folder odpowiadający bieżącemu miesiącowi. Zostanie wyświetlona lista plików CSV. Wybierz jeden z nich, a następnie kliknij przycisk **Otwórz**.

![Przykładowe informacje wyświetlane w Eksploratorze usługi Storage](./media/tutorial-export-acm-data/storage-explorer.png)

Plik zostanie otwarty za pomocą programu lub aplikacji, która została skonfigurowana do otwierania plików z rozszerzeniem CSV. Oto przykład w programie Excel.

![Przykładowe wyeksportowane dane w formacie CSV wyświetlane w programie Excel](./media/tutorial-export-acm-data/example-export-data.png)


## <a name="access-exported-data-from-other-systems"></a>Uzyskiwanie dostępu do wyeksportowanych danych z poziomu innych systemów

Jednym z celów eksportowania danych usługi Cost Management jest uzyskiwanie dostępu do danych z poziomu systemów zewnętrznych. Możesz na przykład użyć systemu pulpitu nawigacyjnego lub innego systemu finansowego. Takie systemy są bardzo zróżnicowane, dlatego pokazanie przykładu byłoby niepraktyczne.  Jednak możesz zacząć od sposobu uzyskiwania dostępu do danych z poziomu Twoich aplikacji w artykule [Wprowadzenie do usługi Azure Storage](../storage/common/storage-introduction.md).

## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie codziennego eksportu
> * Sprawdzanie, czy dane są zbierane

Przejdź do następnego samouczka, aby przeprowadzić optymalizację i zwiększyć wydajność, identyfikując zasoby w stanie bezczynności i niedostatecznie używane.

> [!div class="nextstepaction"]
> [Zapoznawanie się z zaleceniami dotyczącymi optymalizacji i ich wdrażanie](tutorial-acm-opt-recommendations.md)
