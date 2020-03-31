---
title: Opcje migracji danych z urządzeń storsimple serii 5000-7000
description: Zawiera omówienie opcji migracji danych z serii StorSimple 5000-7000.
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
ms.openlocfilehash: 44a05ecb273bdf7582300c3b6a9110e2ada0994c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471826"
---
# <a name="options-to-migrate-data-from-storsimple-5000-7000-series"></a>Opcje migracji danych z urządzeń StorSimple serii 5000-7000 

> [!IMPORTANT]
> 9 lipca 2019 r. seria StorSimple 5000/7000 osiągnie status końca wsparcia (EOS). Firma Microsoft zaleca, aby klienci serii StorSimple 5000/7000 migrować do jednej z alternatyw opisanych w dokumencie.

Seria StorSimple 5000-7000 kończy [wsparcie](https://support.microsoft.com/lifecycle/search?alpha=StorSimple%205000%2F7000%20Series) w lipcu 2019. Klienci korzystający z serii StorSimple 5000-7000 mają możliwość uaktualnienia do innych usług hybrydowych platformy Azure. W tym artykule opisano opcje hybrydowe platformy Azure dostępne do migracji danych. 

## <a name="migration-options"></a>Opcje migracji

Klienci korzystający z serii StorSimple 5000-7000 mają opcje platformy Azure lub innych firm.

### <a name="azure-options"></a>Opcje platformy Azure

#### <a name="upgrade-to-storsimple-8000-series"></a>Uaktualnienie do storsimple serii 8000

Uaktualnij do serii StorSimple 8000 i w ten sposób kontynuuj na platformie StorSimple.  Ta ścieżka uaktualnienia wymaga od klientów zastąpienia urządzeń z serii 5000-7000 na serię 8000. Dane są migrowane z urządzenia serii 5000-7000 za pomocą narzędzia migracji. Po pomyślnym zakończeniu migracji urządzenia z serii StorSimple 8000 będą nadal warstwy danych do usługi Azure Blob Storage. 

Aby uzyskać więcej informacji na temat migracji danych przy użyciu serii StorSimple 8000, przejdź do [tematu Migrowanie danych z serii StorSimple serii 5000-7000 do urządzenia z serii 8000.](storsimple-8000-migrate-from-5000-7000.md)

#### <a name="migrate-to-azure-file-sync"></a>Migracja do usługi Azure File Sync

Ta zupełnie nowa opcja migracji umożliwia klientom przechowywanie udziałów plików organizacji w plikach azure. Te udziały plików są następnie scentralizowane dla dostępu lokalnego przy użyciu usługi Azure File Sync (AFS). System AFS można wdrożyć na hoście systemu Windows Server. Rzeczywista migracja danych jest następnie wykonywana jako kopia hosta lub narzędzie migracji.

Aby uzyskać więcej informacji na temat migracji danych do usługi Azure File Sync, przejdź do [migrowania danych z serii StorSimple 5000-7000 do usługi Azure File Sync](storsimple-5000-7000-afs-migration.md).

### <a name="third-party-options"></a>Opcje innych firm

#### <a name="migrate-to-panzura-freedom-nas"></a>Migrowanie do serwera Panzura Freedom NAS

Klienci StorSimple 5000-7000 mogą przeprowadzić migrację do serwera Panzura Freedom NAS, aby zachować swoje dane na platformie Azure. Rozwiązanie Panzura Freedom to rozwiązanie NAS obejmujące centra danych, biura, chmury publiczne i prywatne. Rozwiązanie umożliwia lokalne, hybrydowe i w chmurze przepływy pracy danych dla nfs, SMB i klientów mobilnych. 

Ta migracja jest obsługiwana przez Panzurę, a klienci mogą rozpocząć, prosząc o pomoc w migracji ze [strony internetowej Panzura](https://panzura.com/storsimple-migration/).

#### <a name="migrate-to-cohesity"></a>Migrowanie do spójności

Spójność umożliwia migrację danych z bieżącego storsimple 5000–7000 do platformy danych cohesity na platformie Azure. Platforma danych Cohesity to definiowane programowo rozwiązanie do skalowania sieci Web, które konsoliduje pliki, kopie zapasowe, obiekty i maszyny wirtualne na jednym rozwiązaniu natywnym dla chmury. Po migracji do platformy danych można zarządzać, chronić i aprowizować dane i aplikacje z chmury do rdzenia za pomocą jednego okienka szkła. Z Cohesity, zacznij od zaledwie trzech węzłów. 

Dowiedz się więcej na [temat migracji do platformy danych cohesity](https://info.cohesity.com/migrate-from-storsimple-to-cohesity.html).

#### <a name="migrate-to-nasuni"></a>Migruj do Nasuni

Nasuni ułatwia klientom StorSimple 5000-7000 migrację i przechowywanie danych na platformie Azure.  Nasuni to wiodące rozwiązanie pamięci masowej NAS oparte na platformie Azure, zapewniające klientom wydajność i bezpieczeństwo, których oczekują od rozwiązań przedpremierowych, z ekonomią chmury i skalą.  Oprócz magazynu plików o wysokiej wydajności, Nasuni i Azure obsługują kopie zapasowe i odzyskiwania po awarii, umożliwiając jednocześnie udostępnianie i współpracę nad danymi na całym świecie za pomocą scentralizowanego zarządzania magazynem plików. 

Nasuni ma doświadczenie, aby ułatwić migrację – zacznij już dziś:https://info.nasuni.com/nasuni-storsimple-migration

#### <a name="migrate-to-talon-fast"></a>Migruj do Talon FAST

Talon ułatwia klientom StorSimple 5000-7000 dalsze korzystanie z korzyści, które cenili tak bardzo na platformie StorSimple (niewielkie rozmiary na miejscu, wspierane przez nieograniczone zasoby w chmurze) z jeszcze większą funkcją.  Dzięki rozwiązaniu Talon FAST klienci mogą migrować i przechowywać swoje dane na platformie Azure, a teraz mają jeszcze mniejszy rozmiar tylko dla oprogramowania na miejscu i dodają korzyści, takie jak globalne blokowanie plików, globalna przestrzeń nazw i współpraca z wieloma lokacjami.  Talon to wiodące rozwiązanie ekosystemu platformy Azure, współpracujące z klientami globalnymi w celu migracji ich lokalnych obciążeń serwera plików do skonsolidowanego śladu opartego na platformie Azure bez uszczerbku dla przepływu pracy lub środowiska użytkownika.  

Dowiedz się więcej o tym, jak ewoluować do przedsiębiorstwa skonsolidowanego w chmurze pod adresem https://www.talonstorage.com/alliances/microsoft-storsimple.


## <a name="migration---frequently-asked-questions"></a>Migracja — często zadawane pytania

### <a name="q-when-do-the-storsimple-5000-and-7000-series-devices-reach-end-of-service"></a>PYTANIE: Kiedy urządzenia z serii StorSimple 5000 i 7000 kończą służbę? 

A. StorSimple serii 5000-7000 zakończy [się](https://support.microsoft.com/lifecycle/search?alpha=StorSimple%205000%2F7000%20Series) w lipcu 2019 roku. Koniec usługi oznacza, że firma Microsoft nie będzie już mogła zapewnić pomocy technicznej zarówno dla sprzętu, jak i oprogramowania tych urządzeń po lipcu 2019. Zdecydowanie zaleca się, aby rozpocząć formułowanie planu migracji danych z urządzeń teraz.

### <a name="q-what-happens-to-the-data-i-have-stored-in-azure"></a>PYTANIE: Co się stanie z danymi przechowywanymi na platformie Azure?  

A. Możesz nadal używać danych na platformie Azure po migracji do nowszej usługi. 


### <a name="q-what-happens-to-the-data-i-have-stored-locally-on-my-storsimple-device"></a>PYTANIE: Co się stanie z danymi przechowywanymi lokalnie na urządzeniu StorSimple? 

A. Dane, które znajduje się na urządzeniu lokalnym mogą być kopiowane do nowszej usługi, jak opisano w dokumentach migracji.

### <a name="q-what-happens-if-i-want-to-keep-my-storsimple-50007000-series-appliance"></a>PYTANIE: Co się stanie, jeśli chcę zachować urządzenie z serii StorSimple 5000/7000? 

A. Chociaż usługi mogą nadal działać, firma Microsoft nie będzie już mogła zapewnić pomocy technicznej dla sprzętu i oprogramowania. Migracja jest zdecydowanie zalecana dla ciągłości biznesowej.

### <a name="q-what-options-are-available-to-migrate-data-from-storsimple-5000-7000-series-devices"></a>PYTANIE: Jakie opcje są dostępne do migracji danych z urządzeń storsimple serii 5000-7000? 

A. W zależności od ich scenariusza StorSimple 5000-7000 użytkowników serii mają następujące opcje migracji. 

 - **Uaktualnij do serii 8000:** Użyj tej opcji, jeśli chcesz kontynuować na platformie StorSimple. 
 - **Migruj do usługi Azure File Sync:** Użyj tej opcji, jeśli chcesz przełączyć się na format macierzysty platformy Azure. Za pomocą usługi Azure File Sync można używać do scentralizowanego zarządzania udziałami plików. 

Możesz skontaktować się z pomocą techniczną firmy Microsoft, aby omówić opcje migracji, których nie ma w tym miejscu.

### <a name="q-is-migration-to-other-storage-solutions-supported"></a>PYTANIE: Czy migracja do innych rozwiązań magazynu jest obsługiwana?

A. Tak. Migracja do innych rozwiązań magazynu przy użyciu kopii hosta danych jest obsługiwana.

### <a name="q-is-migration-supported-by-microsoft"></a>PYTANIE: Czy migracja jest obsługiwana przez firmę Microsoft? 

A. Migracja z serii 5000 lub 7000 jest w pełni obsługiwaną operacją. W rzeczywistości firma Microsoft zaleca dotarcie do pomocy technicznej przed rozpoczęciem migracji. Migracja jest obecnie operacją wspomaganą. Jeśli zamierzasz przeprowadzić [migrację](storsimple-8000-contact-microsoft-support.md)danych z urządzenia z serii StorSimple 5000-7000, otwórz bilet pomocy technicznej .

### <a name="q-what-is-the-pricing-model-for-both-the-migration-options"></a>PYTANIE: Jaki jest model cenowy dla obu opcji migracji?

A. Koszt migracji różni się w zależności od wybranego wariantu. Podczas samej migracji jest bezpłatna, jeśli zdecydujesz się uaktualnić do serii StorSimple 8000, będzie koszt urządzenia sprzętowego. 

Podobnie podczas korzystania z usługi Azure File Sync mogą obowiązywać opłaty subskrypcyjne za usługę. W każdym przypadku klienci będą musieli również pokryć bieżące koszty przechowywania. Aby uzyskać informacje na temat wartości szacunkowej, zapoznaj się z następującymi informacjemi: 
- [Ceny StorSimple](https://azure.microsoft.com/pricing/details/storsimple/)  
- [Ceny AFS]( https://azure.microsoft.com/pricing/details/storage/files/)

### <a name="q--how-long-does-it-take-to-complete-a-migration"></a>PYTANIE:  Jak długo trwa ukończenie migracji?

A. Czas migracji danych zależy od ilości danych i wybranej opcji uaktualniania. 

### <a name="q-what-is-the-end-of-support-date-for-storsimple-8000-series"></a>PYTANIE: Jaka jest data zakończenia pomocy technicznej dla serii StorSimple 8000?

A. Data zakończenia wsparcia dla serii StorSimple 8000 jest opublikowana [tutaj](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series).


## <a name="next-steps"></a>Następne kroki
 - [Migrowanie danych z serii StorSimple 5000-7000 do urządzenia z serii 8000](storsimple-8000-migrate-from-5000-7000.md).
 - [Migrowanie danych z serii StorSimple 5000–7000 do usługi Azure File Sync](storsimple-5000-7000-afs-migration.md)
