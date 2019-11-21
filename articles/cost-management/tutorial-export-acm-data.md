---
title: Tutorial - Create and manage exported data from Azure Cost Management
description: This article shows you how you can create and manage exported Azure Cost Management data so that you can use it in external systems.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/12/2019
ms.topic: tutorial
ms.service: cost-management-billing
manager: dougeby
ms.custom: seodec18
ms.openlocfilehash: a462b3d165a596673049abbbb8b5b8d346f5fc9d
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229828"
---
# <a name="tutorial-create-and-manage-exported-data"></a>Samouczek: eksportowanie danych i zarządzanie nimi

Jeśli znasz samouczek Analiza kosztów, już wiesz, jak ręcznie pobierać dane usługi Cost Management. Jednak możesz utworzyć cykliczne zadanie, które będzie automatycznie eksportowało dane usługi Cost Management do magazynu platformy Azure — codziennie, co tydzień lub co miesiąc. Dane są eksportowane w formacie CSV i zawierają wszystkie informacje zebrane przez usługę Cost Management. Wyeksportowanych danych znajdujących się w magazynie platformy Azure możesz następnie używać z systemami zewnętrznymi, łącząc je z własnymi danymi niestandardowymi. Możesz również użyć ich w systemie zewnętrznym, takim jak pulpit nawigacyjny lub inny system finansowy.

Watch the [How to schedule exports to storage with Azure Cost Management](https://www.youtube.com/watch?v=rWa_xI1aRzo) video about creating a scheduled export of your Azure cost data to Azure Storage.

Przykłady w tym samouczku przeprowadzą Cię przez proces eksportowania danych zarządzania kosztami i sprawdzania, czy dane zostały pomyślnie wyeksportowane.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie codziennego eksportu
> * Sprawdzanie, czy dane są zbierane

## <a name="prerequisites"></a>Wymagania wstępne
Eksportowanie danych jest dostępne dla różnych typów konta platformy Azure, w tym dla klientów z umową [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/). Aby wyświetlić pełną listę obsługiwanych typów kont, zobacz [Omówienie danych usługi Cost Management](understand-cost-mgt-data.md). The following Azure permissions, or scopes, are supported per subscription for data export by user and group. For more information about scopes, see [Understand and work with scopes](understand-work-scopes.md).

- Właściciel — może tworzyć, modyfikować i usuwać zaplanowane eksporty dla subskrypcji.
- Współautor — może tworzyć, modyfikować i usuwać zaplanowane przez siebie eksporty. Może modyfikować nazwy zaplanowanych eksportów utworzonych przez innych użytkowników.
- Czytelnik — może planować eksporty, do których ma uprawnienia.

W przypadku kont usługi Azure Storage:
- Niezależnie od uprawnień dotyczących eksportu, do zmiany skonfigurowanego konta magazynu wymagane są uprawnienia zapisu.
- Twoje konto magazynu platformy Azure musi być skonfigurowane jako magazyn obiektów blob lub magazyn plików.

## <a name="sign-in-to-azure"></a>Zaloguj się w usłudze Azure
Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com/).

## <a name="create-a-daily-export"></a>Tworzenie codziennego eksportu

To create or view a data export or to schedule an export, open the desired scope in the Azure portal and select **Cost analysis** in the menu. For example, navigate to **Subscriptions**, select a subscription from the list, and then select **Cost analysis** in the menu. At the top of the Cost analysis page, click **Export** and then choose an export option. For example, click **Schedule export**.  

> [!NOTE]
> - Besides subscriptions, you can create exports on resource groups, accounts, departments, and enrollments. For more information about scopes, see [Understand and work with scopes](understand-work-scopes.md).
>- When you’re signed in as a partner at the billing account scope or on a customer’s tenant, you can export data to an Azure Storage account that’s linked to your partner storage account. However, you must have an active subscription in your CSP tenant.
>


Click **Add**, type a name for the export, and then select the **Daily export of month-to-date costs** option. Kliknij przycisk **Dalej**.

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

If you have a Pay-As-You-Go, MSDN, or Visual Studio subscription, your invoice billing period might not align to the calendar month. For those types of subscriptions and resource groups, you can create an export that's aligned to your invoice period or to calendar months. To create an export aligned to your invoice month, navigate to **Custom**, then select **Billing-period-to-date**.  To create an export aligned to the calendar month, select **Month-to-date**.
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

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie codziennego eksportu
> * Sprawdzanie, czy dane są zbierane

Przejdź do następnego samouczka, aby przeprowadzić optymalizację i zwiększyć wydajność, identyfikując zasoby w stanie bezczynności i niedostatecznie używane.

> [!div class="nextstepaction"]
> [Zapoznawanie się z zaleceniami dotyczącymi optymalizacji i ich wdrażanie](tutorial-acm-opt-recommendations.md)
