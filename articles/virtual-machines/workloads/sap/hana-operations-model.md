---
title: Model operacyjny SAP HANA na platformie Azure (duże wystąpienia) | Dokumenty firmy Microsoft
description: Model operacyjny sap HANA na platformie Azure (duże wystąpienia).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e147e4a5f104ca4cd1a10a776c907e3f9f1d6128
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616977"
---
# <a name="operations-model-and-responsibilities"></a>Model działania i obowiązki

Usługa świadczona z SAP HANA na platformie Azure (duże wystąpienia) jest wyrównana z usługami Usługi Azure IaaS. Otrzymujesz wystąpienie wystąpienia dużego HANA z zainstalowanym systemem operacyjnym zoptymalizowanym pod kątem sap HANA. Podobnie jak w przypadku maszyn wirtualnych usługi Azure IaaS, większość zadań związanych z hartowaniem systemu operacyjnego, instalowaniem dodatkowego oprogramowania, instalowaniem hana, obsługą systemu operacyjnego i HANA oraz aktualizowaniem systemu operacyjnego i hana jest twoim obowiązkiem. Firma Microsoft nie wymusza aktualizacji systemu operacyjnego ani aktualizacji HANA.

![Obowiązki SAP HANA na platformie Azure (duże wystąpienia)](./media/hana-overview-architecture/image2-responsibilities.png)

Jak pokazano na diagramie, SAP HANA na platformie Azure (duże wystąpienia) jest ofertą IaaS z wieloma dzierżawami. W przeważającej części podział odpowiedzialności znajduje się na granicy infrastruktury systemu operacyjnego. Firma Microsoft jest odpowiedzialna za wszystkie aspekty usługi poniżej linii systemu operacyjnego. Użytkownik jest odpowiedzialny za wszystkie aspekty usługi powyżej linii. System operacyjny jest twoim obowiązkiem. Można nadal używać większości bieżących metod lokalnych, które mogą być stosowane do zgodności, zabezpieczeń, zarządzania aplikacjami, podstawy i zarządzania systemem operacyjnym. Systemy wyglądają tak, jakby były w sieci pod każdym względem.

Ta usługa jest zoptymalizowana pod kątem sap hana, więc istnieją obszary, w których należy współpracować z firmą Microsoft, aby korzystać z podstawowych możliwości infrastruktury, aby uzyskać najlepsze wyniki.

Poniższa lista zawiera więcej szczegółów na temat każdej z warstw i twoich obowiązków:

**Sieci:** Wszystkie sieci wewnętrzne dla stempla dużego wystąpienia z systemem SAP HANA. Odpowiedzialność obejmuje dostęp do magazynu, łączność między wystąpieniami (dla skalowania w poziomie i innych funkcji), łączność z krajobrazu i łączność z platformą Azure, gdzie warstwa aplikacji SAP jest hostowana na maszynach wirtualnych. Obejmuje również łączność WAN między centrami danych platformy Azure do replikacji na potrzeby odzyskiwania po awarii. Wszystkie sieci są podzielone na partycje przez dzierżawcę i mają jakość usługi stosowane.

**Magazyn:** Zwirtualizowany magazyn podzielony na partycje dla wszystkich woluminów potrzebnych przez serwery SAP HANA, a także dla migawek. 

**Serwery:** dedykowane serwery fizyczne do uruchamiania bloków DB SAP HANA przypisanych do dzierżawców. Serwery jednostek SKU klasy Typu I są wyodrębniane sprzętowo. W przypadku serwerów tego typu konfiguracja serwera jest zbierana i obsługiwana w profilach, które można przenosić z jednego sprzętu fizycznego na inny sprzęt fizyczny. Takie (ręczne) przeniesienie profilu przez operacje można porównać nieco do leczenia usługi platformy Azure. Serwery jednostek SKU klasy Typu II nie oferują takiej możliwości.

**SDDC**: Oprogramowanie do zarządzania używane do zarządzania centrami danych jako jednostkami zdefiniowanymi programowo. Umożliwia firmie Microsoft łączenie zasobów ze względu na skalę, dostępność i wydajność.

**O/S:** System operacyjny, który wybierzesz (SUSE Linux lub Red Hat Linux), który jest uruchomiony na serwerach. Obrazy systemu operacyjnego, które są dostarczane z zostały dostarczone przez indywidualnego dostawcę systemu Linux do firmy Microsoft do uruchamiania SAP HANA. Musisz mieć subskrypcję z dostawcą systemu Linux dla określonego obrazu zoptymalizowanego za pomocą sap HANA. Użytkownik jest odpowiedzialny za rejestrowanie obrazów u dostawcy systemu operacyjnego. 

Od momentu przekazania przez Microsoft, jesteś odpowiedzialny za wszelkie dalsze poprawki systemu operacyjnego Linux. To łatanie zawiera dodatkowe pakiety, które mogą być niezbędne do pomyślnej instalacji SAP HANA i które nie zostały uwzględnione przez określonego dostawcę systemu Linux w ich sap HANA zoptymalizowane obrazy systemu operacyjnego. (Aby uzyskać więcej informacji, zobacz dokumentację instalacji HANA firmy SAP i uwagi SAP). 

Użytkownik jest odpowiedzialny za łatanie systemu operacyjnego z powodu nieprawidłowego działania lub optymalizacji systemu operacyjnego i jego sterowników w stosunku do określonego sprzętu serwera. Użytkownik jest również odpowiedzialny za bezpieczeństwo lub funkcjonalne łatanie systemu operacyjnego. 

Twoim obowiązkiem jest również monitorowanie i planowanie zdolności:

- Zużycie zasobów procesora CPU.
- Zużycie pamięci.
- Woluminy dysków związane z wolnego miejsca, We/Wy i opóźnienia.
- Ruch woluminu sieciowego między wystąpieniem dużym HANA a warstwą aplikacji SAP.

Podstawowa infrastruktura hana duże wystąpienie zapewnia funkcje tworzenia kopii zapasowych i przywracania woluminu systemu operacyjnego. Korzystanie z tej funkcji jest również twoim obowiązkiem.

**Oprogramowanie pośredniczące:** Sap HANA wystąpienie, przede wszystkim. Administracja, operacje i monitorowanie są twoim obowiązkiem. Za pomocą podanych funkcji można używać migawek magazynu do tworzenia kopii zapasowych i przywracania oraz odzyskiwania po awarii. Te możliwości są dostarczane przez infrastrukturę. Obowiązki obejmują również projektowanie wysokiej dostępności lub odzyskiwania po awarii z tych funkcji, wykorzystując je i monitorowania w celu określenia, czy migawki magazynu wykonywane pomyślnie.

**Dane**: Dane zarządzane przez SAP HANA i inne dane, takie jak kopie zapasowe plików znajdujących się na woluminach lub udziałach plików. Obowiązki obejmują monitorowanie wolnego miejsca na dysku i zarządzanie zawartością woluminów. Użytkownik jest również odpowiedzialny za monitorowanie pomyślnego wykonywania kopii zapasowych woluminów dysków i migawek magazynu. Za pomyślne wykonanie replikacji danych do lokacji odzyskiwania po awarii odpowiada firma Microsoft.

**Zastosowania:** Wystąpienia aplikacji SAP lub, w przypadku aplikacji innych niż SAP, warstwy aplikacji tych aplikacji. Obowiązki użytkownika obejmują wdrażanie, administrowanie, operacje i monitorowanie tych aplikacji. Użytkownik jest odpowiedzialny za planowanie pojemności zużycia zasobów procesora, zużycia pamięci, zużycia usługi Azure Storage i zużycia przepustowości sieci w sieciach wirtualnych. Użytkownik jest również odpowiedzialny za planowanie pojemności dla zużycia zasobów z sieci wirtualnych do SAP HANA na platformie Azure (duże wystąpienia).

**Sieci WAN:** połączenia, które można ustanowić z lokalnych do wdrożenia platformy Azure dla obciążeń. Wszyscy klienci z dużym wystąpieniem HANA używają usługi Azure ExpressRoute dla łączności. To połączenie nie jest częścią rozwiązania SAP HANA na platformie Azure (duże wystąpienia). Użytkownik jest odpowiedzialny za konfigurację tego połączenia.

**Archiwum:** Możesz zarchiwizować kopie danych przy użyciu własnych metod na kontach magazynu. Archiwizacja wymaga zarządzania, zgodności, kosztów i operacji. Użytkownik jest odpowiedzialny za generowanie kopii archiwalnych i kopii zapasowych na platformie Azure i przechowywanie ich w zgodny sposób.

Zobacz [umowy SLA dla SAP HANA na platformie Azure (duże wystąpienia).](https://azure.microsoft.com/support/legal/sla/sap-hana-large/)

**Następne kroki**
- Odwoływanie się do [architektury SAP HANA (duże wystąpienia) na platformie Azure](hana-architecture.md)
