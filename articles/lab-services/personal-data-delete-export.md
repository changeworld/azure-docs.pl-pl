---
title: Sposób usuwania i wyeksportować dane osobowe z usługi Azure DevTest Labs | Dokumentacja firmy Microsoft
description: Dowiedz się, jak usunąć i eksportowanie danych osobowych z usługą Azure DevLast Labs, aby obsługiwać Twoje obowiązki w ramach ogólnego rozporządzenia o ochronie danych (RODO).
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2018
ms.author: spelluru
ms.openlocfilehash: e681652c13e521bd33524e247db65088f47a794c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60394988"
---
# <a name="export-or-delete-personal-data-from-azure-devtest-labs"></a>Eksportowanie lub usunąć dane osobowe z usługi Azure DevTest Labs
Ten artykuł zawiera instrukcje usuwania i eksportowania danych osobowych z usługi Azure DevTest Labs. 

## <a name="what-personal-data-does-devtest-labs-collect"></a>Jakie dane osobiste są zbierane w usłudze DevTest Labs?
DevTest Labs umożliwia zbieranie informacji o dwie główne części danych osobowych użytkownika. Są one: adres e-mail użytkownika i identyfikator obiektu użytkownika. Te informacje są krytyczne dla usługi w celu zapewnienia funkcji usługi laboratorium administratorów i użytkowników laboratorium.

### <a name="user-email-address"></a>Adres e-mail użytkownika
DevTest Labs używa adresu e-mail użytkownika do wysyłania powiadomień e-mail zamykania automatycznie użytkownikom laboratorium. Wiadomości e-mail umożliwia powiadomienie użytkowników ich maszyny zamykany. Użytkowników można odroczyć lub pominąć zamknięcia systemu, jeśli chcesz to zrobić. Możesz skonfigurować adres e-mail, na poziomie laboratorium lub na poziomie maszyny Wirtualnej.

**Ustawienia poczty e-mail w laboratorium:**

![Ustawienia poczty e-mail na poziomie laboratorium](./media/personal-data-delete-export/lab-user-email.png)

**Ustawienia poczty e-mail na maszynie Wirtualnej:**

![Ustawienia poczty e-mail na poziomie maszyny Wirtualnej](./media/personal-data-delete-export/vm-user-email.png)

### <a name="user-object-id"></a>Identyfikator obiektu użytkownika
DevTest Labs używa identyfikator obiektu użytkownika będzie wyświetlana trendy związane z kosztami miesiąc do miesiąca i kosztów, informacje o zasobie laboratorium administratorów. Umożliwia śledzenie kosztów i zarządzanie nimi progi w ich środowisku laboratoryjnym. 

**Szacowany koszt trend dla bieżącego miesiąca kalendarzowego:**
![szacowany koszt trend w bieżącym miesiącu](./media/personal-data-delete-export/estimated-cost-trend-per-month.png)

**Szacowany koszt od początku miesiąca przez zasób:**
![szacowany koszt od początku miesiąca według zasobu](./media/personal-data-delete-export/estimated-month-to-date-cost-by-resource.png)


## <a name="why-do-we-need-this-personal-data"></a>Dlaczego Potrzebujemy tych danych osobowych?
Usługa DevTest Labs używa danych osobowych do celów operacyjnych. Dane te są niezbędne do dostarczenia kluczowych funkcji usługi. Jeśli zasady przechowywania jest ustawiony na adres e-mail użytkownika, użytkownicy laboratorium nie otrzymywać powiadomienia e-mail zamykania automatycznie w odpowiednim czasie, po ich adres e-mail został usunięty z naszego systemu. Podobnie administrator laboratorium nie można wyświetlić miesiąc do miesiąca trendy związane z kosztami i koszt według zasobów dla maszyn w laboratorium ich, jeśli obiekt użytkownika, identyfikatory są usuwane na podstawie zasad przechowywania. Dlatego te dane muszą być przechowywane dla, tak długo, jak zasób użytkownika jest aktywny w środowisku laboratoryjnym.

## <a name="how-can-i-have-the-system-to-forget-my-personal-data"></a>Jak można mieć system zapomnieć Moje dane osobowe?
Jako użytkownik laboratorium Jeśli chcesz mieć tych danych osobowych, które zostały usunięte, możesz to zrobić, usuwając odpowiadający jej zasób w środowisku laboratoryjnym. Usługa DevTest Labs anonimowość usuniętych danych osobowych w ciągu 30 dni po jego usunięciu przez użytkownika.

Na przykład usunięcie maszyny Wirtualnej, lub usunąć swój adres e-mail, usługi DevTest Labs anonimowość tych danych w ciągu 30 dni po usunięciu zasobu. Zasady przechowywania 30 dni, po usunięciu, upewnij się, że firma Microsoft zapewnia Projekcja dokładny miesiąc do miesiąca koszt na administratorem laboratorium.

## <a name="how-can-i-request-an-export-on-my-personal-data"></a>Jak żądanie eksportu na moje dane osobowe?
Jako użytkownik laboratorium możesz poprosić Eksport danych osobowych, która przechowuje usługi DevTest Labs. Żądanie eksportu usługi, przejdź do **danych osobowych** opcja **Przegląd** strony środowiska laboratoryjnego. Wybierz **żądanie eksportu** przycisk dotyczącego utworzenie pliku do pobrania programu excel na koncie magazynu z administratorem laboratorium. Można następnie skontaktuj się z administratorem laboratorium, aby wyświetlić te dane.

1. Wybierz **danych osobowych** w menu po lewej stronie. 

    ![Strona danych osobowych](./media/personal-data-delete-export/personal-data-page.png)
2. Wybierz **grupy zasobów** zawierający laboratorium.

    ![Wybieranie grupy zasobów](./media/personal-data-delete-export/select-resource-group.png)
3. Wybierz **konta magazynu** w grupie zasobów.
4. Na **konta magazynu** wybierz opcję **obiektów blob**.

    ![Wybierz Kafelek obiektów blob](./media/personal-data-delete-export/select-blobs-tile.png)
5. Wybierz kontener o nazwie **labresourceusage** na liście kontenerów.

    ![Wybierz kontener obiektów blob](./media/personal-data-delete-export/select-blob-container.png)
6. Wybierz **folderu** nazwana środowiska laboratoryjnego. Możesz znaleźć **csv** pliki **dysków** i **maszyn wirtualnych** w środowisku laboratoryjnym, w tym folderze. Można pobierać te pliki csv, filtrować zawartość żądania dostępu użytkownika laboratorium i ją udostępnić.

    ![Pobierz plik CSV](./media/personal-data-delete-export/download-csv-file.png)

## <a name="next-steps"></a>Kolejne kroki
Zobacz następujące artykuły: 

- [Ustawianie zasad laboratorium](devtest-lab-get-started-with-lab-policies.md)
- [Często zadawane pytania](devtest-lab-faq.md)
