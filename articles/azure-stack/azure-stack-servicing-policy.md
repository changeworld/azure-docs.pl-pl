---
title: Obsługa zasad z usługi Azure Stack | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o usłudze Azure Stack obsługi zasad i jak zapewnić zintegrowany system wspierany.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: caac3d2f-11cc-4ff2-82d6-52b58fee4c39
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2019
ms.author: sethm
ms.reviewer: harik
ms.lastreviewed: 01/11/2019
ms.openlocfilehash: 8abbf3a9db02fda0c92485ef75a12d82f4e8c8e9
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58903951"
---
# <a name="azure-stack-servicing-policy"></a>Obsługa zasad z usługi Azure Stack

W tym artykule opisano zasady obsługi dla usługi Azure Stack zintegrowane systemy i co należy zrobić, aby zapewnić systemu w stanie wspieranym.

## <a name="download-update-packages-for-integrated-systems"></a>Pobierz pakiety aktualizacji dla systemów zintegrowanych

Firma Microsoft udostępni zarówno pełne miesięczne pakiety aktualizacji, jak i pakietów poprawek w celu rozwiązania określonych problemów z.

Miesięczne pakiety aktualizacji znajdują się w bezpieczny punkt końcowy platformy Azure. Możesz pobrać je ręcznie przy użyciu [narzędzia pobierania usługi Azure Stack aktualizacje](https://aka.ms/azurestackupdatedownload). Jeśli jednostki skalowania jest połączony, aktualizacja zostanie automatycznie wyświetlony w portalu administratora jako **dostępna aktualizacja**. Pełne co miesiąc pakiety aktualizacji są dobrze udokumentowane w każdej wersji. Aby uzyskać więcej informacji na temat poszczególnych wersji kliknąć dowolną wersją z [cykl wersji pakietu aktualizacji](#update-package-release-cadence) dalszej części tego artykułu.

Pakiety aktualizacji poprawki znajdują się w tej samej bezpieczny punkt końcowy platformy Azure. Możesz pobrać je ręcznie przy użyciu łącza osadzonego we wszystkich artykułach bazy wiedzy odpowiednich poprawek; na przykład [usługi Azure Stack poprawkę 1.1809.12.114](https://support.microsoft.com/help/4481548/azure-stack-hotfix-1-1809-12-114). Podobnie jak pakiety aktualizacji pełną, miesięczną, Operatorzy usługi Azure Stack można pobrać plików XML, bin i .exe i zaimportować je za pomocą procedury w [stosowanie aktualizacji w usłudze Azure Stack](azure-stack-apply-updates.md). Operatorzy usługi Azure Stack przy użyciu jednostek skalowania połączonych zobaczą poprawki są automatycznie wyświetlane w portalu administratora z komunikatem **dostępna aktualizacja**.

Jeśli chcesz otrzymywać powiadomienia o każdym wydaniu poprawek jednostki skalowania nie jest połączony, Subskrybuj [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss) lub [ATOM](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom) kanał zauważyć w każdej wersji.  

## <a name="update-package-types"></a>Typy pakietów aktualizacji

Istnieją dwa typy pakietów aktualizacji w ramach zintegrowanych systemów:

- **Aktualizacje oprogramowania firmy Microsoft**. Firma Microsoft jest odpowiedzialna za cyklu życia obsługi end-to-end dla pakietów aktualizacji oprogramowania firmy Microsoft. Te pakiety mogą obejmować najnowsze aktualizacje zabezpieczeń systemu Windows Server, aktualizacji niezwiązanych z zabezpieczeniami i aktualizacje funkcji usługi Azure Stack. Możesz pobrać te pakiety aktualizacji bezpośrednio od firmy Microsoft.

- **Aktualizacje udostępnionych przez dostawcę sprzętu OEM**. Partnerzy usługi Azure Stack sprzętu są odpowiedzialne za end-to-end obsługi cyklu życia (w tym wskazówki) związanych ze sprzętem oprogramowania układowego i sterowników pakiety aktualizacji. Ponadto partnerów sprzętowych w usłudze Azure Stack właścicielem i obsługa wskazówki dotyczące wszystkich sprzętu na hoście cyklu życia sprzętu i oprogramowania. Dostawcy sprzętu OEM obsługuje te pakiety na własnych witryny pobierania aktualizacji.

## <a name="update-package-release-cadence"></a>Cykl wersji pakietu aktualizacji

Firma Microsoft planuje wersji pakietów aktualizacji oprogramowania w erze miesięcznych. Jednak istnieje możliwość mają wiele lub nie aktualizacji przeprowadzanych w danym miesiącu. Dostawcy sprzętu OEM ich aktualizacje na zgodnie z potrzebami.

Znajdź dokumentację na temat planowania i zarządzania aktualizacjami i jak określić bieżącej wersji w [Zarządzaj aktualizuje Przegląd](azure-stack-updates.md).

Informacje o konkretnej aktualizacji w tym jak ją pobrać, zobacz informacje o wersji dla tej aktualizacji:

- [Aktualizacja usługi Azure Stack 1903](azure-stack-update-1903.md)
- [Aktualizacja usługi Azure Stack 1902](azure-stack-update-1902.md)
- [Aktualizacja usługi Azure Stack 1901](azure-stack-update-1901.md)
- [Aktualizacja usługi Azure Stack 1811](azure-stack-update-1811.md)

## <a name="hotfixes"></a>Poprawki

Od czasu do czasu firma Microsoft oferuje poprawki dla usługi Azure Stack ten adres konkretny problem, który jest często zapobiegawczych lub zależne od czasu.  Każda poprawka jest zwolniony z odpowiednie artykuły bazy wiedzy Microsoft Knowledge Base, ze szczegółami problemu, przyczynę i rozwiązanie.

Poprawki są pobierane i instalowane podobnie jak pakiety regularne pełną aktualizację dla usługi Azure Stack. Jednak w przeciwieństwie do pełnej aktualizacji poprawek można zainstalować w ciągu kilku minut. Firma Microsoft zaleca zestawu Azure Stack operatory okna obsługi podczas instalowania poprawek. Poprawki aktualizacji wersji w chmurze usługi Azure Stack, dzięki czemu można łatwo określić, jeśli zastosowano poprawkę. Dostępna jest poprawka osobne dla każdej wersji usługi Azure Stack, która jest nadal pomocy technicznej. Każda poprawka dla określonej iteracji kumulują się i zawiera poprzednie aktualizacje dla tej samej wersji. Możesz dowiedzieć się więcej o możliwość zastosowania poprawki w poprawki, odpowiadające wiedzy artykuł.  

## <a name="keep-your-system-under-support"></a>Aktualizowanie systemu w ramach pomocy technicznej

Aby kontynuować do otrzymania pomocy technicznej, należy pozostawić wdrożenia usługi Azure Stack bieżącego. Zasada odroczenia dla aktualizacji jest: Dla wdrożenia usługi Azure Stack pozostać w obsłudze musisz niedawno wersja zaktualizowanej wersji, lub uruchomić jedno z dwóch poprzednich wersji aktualizacji. Poprawki nie są uwzględniane wersje główne aktualizacji. Jeśli chmurze usługi Azure Stack jest za zaporą *więcej niż dwie aktualizacje*, jest uznawane za niezgodne i należy zaktualizować co najmniej minimalnej obsługiwanej wersji do otrzymania pomocy technicznej.

Na przykład jeśli 1805 niedawno dostępny zaktualizowanej wersji, a poprzednie dwa pakiety aktualizacji były w wersji 1804 i 1803, zarówno w wersji 1803, jak i w wersji 1804 pozostają w pomocy technicznej. Jednak 1802 jest obsługiwany. Prawdziwe zasad, gdy istnieje bez zwalniania miesiąc lub dwóch. Na przykład jeśli nie było żadnych wersji 1804 bieżącej wersji jest 1805, poprzednie dwa pakiety aktualizacji 1803 i 1802 pozostają w pomocy technicznej.

Pakiety aktualizacji oprogramowania firmy Microsoft są skumulowanych i wymagają poprzedniego pakietu aktualizacji lub poprawek jako warunek wstępny. Jeśli zdecydujesz się mają być odroczone co najmniej jednej aktualizacji, należy wziąć pod uwagę ogólnego środowiska uruchomieniowego, jeśli chcesz przejść do najnowszej wersji.

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

Usługa Azure Stack przebiega w taki sam pomocy technicznej Azure. Klienci korporacyjni mogą postępować zgodnie z instrukcjami w [jak utworzyć żądanie pomocy technicznej platformy Azure](/azure/azure-supportability/how-to-create-azure-support-request). Jeśli jesteś klientem z dostawcy usług chmury (CSP), skontaktuj się z dostawca usług Kryptograficznych pomocy technicznej.  Aby uzyskać więcej informacji, zobacz [— często zadawane pytania dla pomocy technicznej Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Kolejne kroki

- [Zarządzanie aktualizacjami w usłudze Azure Stack](azure-stack-updates.md)