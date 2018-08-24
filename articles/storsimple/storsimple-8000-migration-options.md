---
title: Ocenę opcji migracji danych z usługi StorSimple serii 5000 – 7000 | Dokumentacja firmy Microsoft
description: Omówienie opcji migracji danych z serii StorSimple 5000 – 7000.
services: storsimple
documentationcenter: NA
author: alkohli
manager: twooley
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/23/2018
ms.author: alkohli
ms.openlocfilehash: 8f34d5a38f09f015547f52cc4b44819b780932bb
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/24/2018
ms.locfileid: "42818862"
---
# <a name="options-to-migrate-data-from-storsimple-5000-7000-series"></a>Opcje migracji danych z usługi StorSimple serii 5000 – 7000 

> [!IMPORTANT]
> Na 31 lipca 2019 koniec stanie pomocy technicznej (EOS) będzie korzystał z serii StorSimple 5000/7000. Zaleca się, że klienci serii StorSimple 5000/7000 migrację do jednej z alternatyw opisanych w dokumencie.

Usługa StorSimple serii 5000 – 7000 zbliża się do [wsparcie](https://support.microsoft.com/lifecycle/search?alpha=StorSimple%205000%2F7000%20Series) w lipcu 2019 r. Klienci korzystający z usługi StorSimple serii 5000 – 7000 mają opcji uaktualnienia do innych Azure firmy usług hybrydowych. W tym artykule opisano opcje hybrydowej platformy Azure dostępne do migracji danych. 

## <a name="migration-options"></a>Opcje migracji

Klientów przy użyciu usługi StorSimple serii 5000 – 7000 ma dwie główne opcje:

- **Uaktualnianie do programu StorSimple 8000 Series** — uaktualnienie do magazynu StorSimple serii 8000 i kontynuować na platformie usługi StorSimple.  Tej ścieżki uaktualniania wymaga od klientów w celu zastąpienia ich urządzeń serii 5000 – 7000 z serii 8000. Z urządzeń serii 5000 – 7000 są migrowane dane przy użyciu narzędzia do migracji. Po pomyślnym zakończeniu migracji, urządzeń z serii StorSimple 8000 będzie warstwy danych w usłudze Azure Blob Storage. 

    Aby uzyskać więcej informacji na temat migrowania danych przy użyciu serii StorSimple 8000, przejdź do [migracji danych z usługi StorSimple serii 5000 – 7000 do urządzenia z serii 8000](storsimple-8000-migrate-from-5000-7000.md).

- **Migrowanie do usługi Azure File Sync** — ta zupełnie nowa opcja migracji umożliwia klientom na przechowywanie udziałów plików w organizacji w usłudze Azure Files. Te udziały plików są następnie scentralizowane dla dostępu do lokalnego przy użyciu usługi Azure File Sync (AFS). AFS można wdrożyć na hoście systemu Windows Server. Następnie odbywa się migracja rzeczywistych danych jako hosta kopiowania lub za pomocą narzędzia migracji.

    Aby uzyskać więcej informacji na temat migrowania danych do usługi Azure File Sync, przejdź do [migracji danych z rozwiązania StorSimple serii 5000 – 7000 do usługi Azure File Sync](https://aka.ms/StorSimpleMigrationAFS).

## <a name="migration---frequently-asked-questions"></a>Migracja — często zadawane pytania

### <a name="q-when-do-the-storsimple-5000-and-7000-series-devices-reach-end-of-service"></a>PYTANIE: Kiedy StorSimple 5000 i 7000 series urządzeń Easy usługi? 

A. StorSimple serii 5000 – 7000 osiągnąć [koniec usługi](https://support.microsoft.com/lifecycle/search?alpha=StorSimple%205000%2F7000%20Series) w lipcu 2019 r. Koniec usługi oznacza, że Microsoft nie będzie możliwość zapewnienia pomocy technicznej dla sprzętu i oprogramowania, te urządzenia po 2019 lipca. Zdecydowanie zaleca się rozpoczęcie opracowywania planu migracji danych z urządzeń, teraz.

### <a name="q-what-happens-to-the-data-i-have-stored-in-azure"></a>PYTANIE: Co się stanie z danymi, które mogę być przechowywane na platformie Azure?  

A. Można nadal używać danych na platformie Azure, po zmigrowaniu do nowszych usługi. 


### <a name="q--what-happens-to-the-data-i-have-stored-locally-on-my-storsimple-device"></a>PYTANIE:  Co stanie się dane, które mają I przechowywane lokalnie na urządzeniu StorSimple? 

A. Do nowszych usługi zgodnie z opisem w dokumentach migracji można skopiować danych, która znajduje się na urządzeniu lokalnym.

### <a name="what-happens-if-i-want-to-keep-my-storsimple-50007000-series-appliance"></a>Co się stanie, jeśli chcę przechowywać Moje urządzenia serii StorSimple 5000/7000? 

A. Podczas usług mogą kontynuować pracę, Microsoft nie będzie do obsługi sprzętu i oprogramowania. Zdecydowanie zaleca się migrację dla ciągłości działania.

### <a name="q-what-options-are-available-to-migrate-data-from-storsimple-5000-7000-series-devices"></a>PYTANIE: Jakie opcje są dostępne do migracji danych z usługi StorSimple z urządzeń serii 5000 – 7000? 

A. W zależności od ich scenariuszu użytkownicy serii StorSimple 5000 – 7000 ma następujące opcje migracji. 

 - **Uaktualnianie do programu z serii 8000**: Użyj tej opcji, jeśli chcesz nadal na platformie usługi StorSimple. 
 - **Migrowanie do usługi Azure File Sync**: Użyj tej opcji, jeśli chcesz przełączyć się do platformy Azure formacie natywnym. Umożliwia scentralizowane zarządzanie udziałami plików usługi Azure File Sync. 

Możesz skontaktować się ze Support firmy Microsoft w celu omówienia opcji migracji niewymienione w tym miejscu.

### <a name="q-is-migration-to-other-storage-solutions-supported"></a>PYTANIE: Inne rozwiązania przechowywania w obsługiwane jest migracji?

A. Tak. Migracja do innych rozwiązań magazynu przy użyciu hosta kopii danych jest obsługiwana.

### <a name="q-is-migration-supported-by-microsoft"></a>PYTANIE: Migracja jest obsługiwana przez firmę Microsoft? 

A. Migracja z serii 5000 i 7000 jest w pełni obsługiwaną operacją. W rzeczywistości firma Microsoft zaleca skontaktowanie się do działu pomocy technicznej, przed rozpoczęciem migracji. Migracja jest obecnie asystowanej operacji. Jeśli zamierzasz migrować dane z usługi StorSimple, urządzenie z serii 5000 – 7000, [Otwórz bilet pomocy technicznej](storsimple-8000-contact-microsoft-support.md).

### <a name="q-how-does-the-cost-compare-for-the-two-listed-migrations-to-azure-hybrid-services"></a>PYTANIE: Jak wygląda porównanie kosztów dla dwie migracje wymienionych usług hybrydowej platformy Azure? 

A. Koszt migracji różni się w zależności od wybranej opcji. Podczas migracji, sama jest bezpłatny, jeśli zdecydujesz się na uaktualnienie do serii StorSimple 8000, nastąpi koszt urządzenia sprzętowego. Podobnie korzystając z usługi Azure File Sync, mogą obowiązywać opłaty za subskrypcję usługi. W każdym przypadku klienci także trzeba płacić bieżące koszty. Zapoznaj się [Kalkulator cen Microsoft dla odpowiednich usług](https://azure.microsoft.com/pricing/#product-picker) dla szacowania.  

### <a name="q--how-long-does-it-take-to-complete-a-migration"></a>PYTANIE:  Jak długo trwa do ukończenia migracji?

A. Czas na migrację danych zależy od ilości danych i wybrania opcji uaktualniania. 

## <a name="next-steps"></a>Kolejne kroki
 - [Migracja danych z magazynu StorSimple serii 5000 – 7000 do urządzenia z serii 8000](storsimple-8000-migrate-from-5000-7000.md).
 - [Migrowanie danych z serii StorSimple 5000 – 7000 do usługi Azure File Sync](storsimple-5000-7000-afs-migration.md)
