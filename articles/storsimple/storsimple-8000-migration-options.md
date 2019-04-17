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
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: 78508c1227c0b278041b86c3fdd698c6ad27c132
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2019
ms.locfileid: "59608217"
---
# <a name="options-to-migrate-data-from-storsimple-5000-7000-series"></a>Opcje migracji danych z usługi StorSimple serii 5000 – 7000 

> [!IMPORTANT]
> Na 9 lipca 2019 koniec stanie pomocy technicznej (EOS) będzie korzystał z serii StorSimple 5000/7000. Zaleca się, że klienci serii StorSimple 5000/7000 migrację do jednej z alternatyw opisanych w dokumencie.

Usługa StorSimple serii 5000 – 7000 zbliża się do [wsparcie](https://support.microsoft.com/lifecycle/search?alpha=StorSimple%205000%2F7000%20Series) w lipcu 2019 r. Klienci korzystający z usługi StorSimple serii 5000 – 7000 mają opcji uaktualnienia do innych Azure firmy usług hybrydowych. W tym artykule opisano opcje hybrydowej platformy Azure dostępne do migracji danych. 

## <a name="migration-options"></a>Opcje migracji

Klientów przy użyciu usługi StorSimple serii 5000 – 7000 mieć platformy Azure lub innych opcji.

### <a name="azure-options"></a>Opcje platformy Azure

#### <a name="upgrade-to-storsimple-8000-series"></a>Uaktualnianie do programu StorSimple 8000 Series

Uaktualnienie do serii StorSimple 8000 i w efekcie nadal na platformie usługi StorSimple.  Tej ścieżki uaktualniania wymaga od klientów w celu zastąpienia ich urządzeń serii 5000 – 7000 z serii 8000. Z urządzeń serii 5000 – 7000 są migrowane dane przy użyciu narzędzia do migracji. Po pomyślnym zakończeniu migracji, urządzeń z serii StorSimple 8000 będzie warstwy danych w usłudze Azure Blob Storage. 

Aby uzyskać więcej informacji na temat migrowania danych przy użyciu serii StorSimple 8000, przejdź do [migracji danych z usługi StorSimple serii 5000 – 7000 do urządzenia z serii 8000](storsimple-8000-migrate-from-5000-7000.md).

#### <a name="migrate-to-azure-file-sync"></a>Migracja do usługi Azure File Sync

Ta nowa opcja migracji umożliwia klientom na przechowywanie udziałów plików w organizacji w usłudze Azure Files. Te udziały plików są następnie scentralizowane dla dostępu do lokalnego przy użyciu usługi Azure File Sync (AFS). AFS można wdrożyć na hoście systemu Windows Server. Następnie odbywa się migracja rzeczywistych danych jako hosta kopiowania lub za pomocą narzędzia migracji.

Aby uzyskać więcej informacji na temat migrowania danych do usługi Azure File Sync, przejdź do [migracji danych z rozwiązania StorSimple serii 5000 – 7000 do usługi Azure File Sync](https://aka.ms/StorSimpleMigrationAFS).

### <a name="third-party-options"></a>Opcje innych firm

#### <a name="migrate-to-panzura-freedom-nas"></a>Migrowanie do swobodę Panzura NAS

StorSimple 5000 – 7000 klienci mogą wybrać opcję migracji do Panzura swobodę NAS, aby zachować swoje dane na platformie Azure. Swoboda Panzura rozwiązanie zapewnia rozwiązanie NAS obejmującej centrów danych, oddziałów, chmur prywatnych i publicznych. To rozwiązanie umożliwia lokalnych, hybrydowych i przepływy pracy danych w chmurze dla systemu plików NFS, SMB i klientów mobilnych. 

Ta migracja jest obsługiwana przez Panzura i klienci mogą zacząć żądając Obsługa migracji z [Panzura witryny sieci Web](https://panzura.com/storsimple-migration/).

#### <a name="migrate-to-cohesity"></a>Migrowanie do Cohesity

Cohesity umożliwia migrację danych z bieżącego rozwiązania StorSimple 5000 – 7000 do platformy Cohesity danych na platformie Azure. Cohesity Data Platform jest rozwiązaniem skali sieci web zdefiniowanych przez oprogramowanie, która konsoliduje plików, tworzenie kopii zapasowych, obiektów i maszyn wirtualnych na jedno rozwiązanie natywnych dla chmury. Po zakończeniu migracji do platformy danych można zarządzać, ochronę i udostępniania danych i aplikacji z chmury do core za pomocą jedną taflę szkła. Dzięki firmie Cohesity Uruchom mających co najmniej trzy węzły. 

Dowiedz się więcej o [migracji do usługi Cohesity Data Platform](https://info.cohesity.com/migrate-from-storsimple-to-cohesity.html).

#### <a name="migrate-to-nasuni"></a>Migrowanie do Nasuni

Firma Nasuni ułatwia klientom StorSimple 5000 – 7000 do migracji i przechowują swoje dane na platformie Azure.  Nasuni to wiodące rozwiązanie magazynu oparte na platformie Azure NAS, wydajności i zabezpieczeń, które spełniają oczekiwane z rozwiązań lokalnych, z chmury i skali, dzięki czemu klienci.  Oprócz przechowywania plików o wysokiej wydajności, Nasuni i Azure dojście do obsługi kopii zapasowych i odzyskiwania po awarii, umożliwiając udostępnianie i współpraca na podstawie danych na całym świecie z zarządzaniem magazynem scentralizowane plików jednocześnie. 

Firma Nasuni ma środowisko aby ułatwić migrację — Zacznij już dzisiaj: https://info.nasuni.com/nasuni-storsimple-migration

#### <a name="migrate-to-talon-fast"></a>Migrowanie do Talon FAST

Firma talon ułatwia klientom StorSimple 5000 – 7000 nadal korzystać z korzyści są wyceniane tak wiele platformie StorSimple (niewielkie na miejscu rozmiary obsługiwane przez zasoby w chmurze bez ograniczeń) za pomocą funkcji jeszcze bardziej.  Dzięki rozwiązaniu firma Talon szybko migrować klientów i zachować swoje dane na platformie Azure, teraz o jeszcze mniejsze zużycie u klienta tylko do oprogramowania i dodawanie korzyści, takich jak plik globalnych blokowania, globalnej przestrzeni nazw i współpracy obejmujące wiele lokacji.  Firma talon to wiodące rozwiązanie ekosystem platformy Azure, do pracy z klientami globalne migracji obciążeń serwera plików w środowisku lokalnym zużywających skonsolidowany, oparte na platformie Azure bez uszczerbku dla przepływu pracy użytkownika i środowiska.  

Więcej informacji na temat sposobu ewoluowania przedsiębiorstwu skonsolidowane w chmurze w https://www.talonstorage.com/alliances/microsoft-storsimple.


## <a name="migration---frequently-asked-questions"></a>Migracja — często zadawane pytania

### <a name="q-when-do-the-storsimple-5000-and-7000-series-devices-reach-end-of-service"></a>PYTANIE: Kiedy StorSimple 5000 i 7000 series urządzeń Easy usługi? 

A. StorSimple serii 5000 – 7000 osiągnąć [koniec usługi](https://support.microsoft.com/lifecycle/search?alpha=StorSimple%205000%2F7000%20Series) w lipcu 2019 r. Koniec usługi oznacza, że Microsoft nie będzie możliwość zapewnienia pomocy technicznej dla sprzętu i oprogramowania z tych urządzeń po 2019 lipca. Zdecydowanie zaleca się rozpoczęcie opracowywania planu migracji danych z urządzeń, teraz.

### <a name="q-what-happens-to-the-data-i-have-stored-in-azure"></a>PYTANIE: Co się stanie z danymi, które mogę być przechowywane na platformie Azure?  

A. Można nadal używać danych na platformie Azure, po zmigrowaniu do nowszych usługi. 


### <a name="q-what-happens-to-the-data-i-have-stored-locally-on-my-storsimple-device"></a>PYTANIE: Co stanie się dane, które mają I przechowywane lokalnie na urządzeniu StorSimple? 

A. Do nowszych usługi zgodnie z opisem w dokumentach migracji można skopiować danych, która znajduje się na urządzeniu lokalnym.

### <a name="q-what-happens-if-i-want-to-keep-my-storsimple-50007000-series-appliance"></a>PYTANIE: Co się stanie, jeśli chcę przechowywać Moje urządzenia serii StorSimple 5000/7000? 

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

### <a name="q-what-is-the-pricing-model-for-both-the-migration-options"></a>PYTANIE: Co to jest model cenowy dla obu opcji migracji?

A. Koszt migracji różni się w zależności od wybranej opcji. Podczas migracji, sama jest bezpłatny, jeśli zdecydujesz się na uaktualnienie do serii StorSimple 8000, nastąpi koszt urządzenia sprzętowego. 

Podobnie korzystając z usługi Azure File Sync, mogą obowiązywać opłaty za subskrypcję usługi. W każdym przypadku klienci także trzeba płacić bieżące koszty. Zapoznaj się z poniższymi oszacowanie: 
- [Ceny usługi StorSimple](https://azure.microsoft.com/pricing/details/storsimple/)  
- [Cennik AFS]( https://azure.microsoft.com/pricing/details/storage/files/)

### <a name="q--how-long-does-it-take-to-complete-a-migration"></a>PYTANIE:  Jak długo trwa do ukończenia migracji?

A. Czas na migrację danych zależy od ilości danych i wybrania opcji uaktualniania. 

### <a name="q-what-is-the-end-of-support-date-for-storsimple-8000-series"></a>PYTANIE: Jaka jest data zakończenia wsparcia dla serii StorSimple 8000?

A. Data zakończenia wsparcia dla serii StorSimple 8000 jest publikowany [tutaj](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series).


## <a name="next-steps"></a>Kolejne kroki
 - [Migracja danych z magazynu StorSimple serii 5000 – 7000 do urządzenia z serii 8000](storsimple-8000-migrate-from-5000-7000.md).
 - [Migrowanie danych z serii StorSimple 5000 – 7000 do usługi Azure File Sync](storsimple-5000-7000-afs-migration.md)
