---
title: Model działania platformy SAP Hana na platformie Azure (duże wystąpienia) | Dokumentacja firmy Microsoft
description: Model działania platformy SAP HANA na platformie Azure (duże wystąpienia).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 776057a177904d086974258b5221ab474f4b1bcb
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/06/2018
ms.locfileid: "44028224"
---
# <a name="operations-model-and-responsibilities"></a>Model działania i obowiązki

Usługi za pomocą platformy SAP HANA na platformie Azure (duże wystąpienia) jest powiązana z usługami IaaS platformy Azure. Uzyskasz wystąpienie oprogramowania HANA, duże wystąpienie z zainstalowanym systemem operacyjnym, który jest zoptymalizowany pod kątem oprogramowania SAP HANA. Przy użyciu maszyn wirtualnych IaaS platformy Azure, większość zadań wzmacniania ochrony systemu operacyjnego, instalowania dodatkowego oprogramowania, instalowanie oprogramowania HANA, obsługi systemu operacyjnego i platformy HANA i aktualizowanie systemu operacyjnego i platformy HANA jest odpowiedzialny za. Microsoft nie siły aktualizacje systemu operacyjnego lub aktualizacje oprogramowania HANA należy.

![Obowiązki platformy SAP HANA na platformie Azure (duże wystąpienia)](./media/hana-overview-architecture/image2-responsibilities.png)

Jak pokazano na diagramie, SAP HANA na platformie Azure (duże wystąpienia) to wielodostępne, które oferują IaaS. W większości przypadków podział odpowiedzialności jest na granicy infrastruktury systemu operacyjnego. Firma Microsoft jest odpowiedzialna za wszystkimi aspektami usługi pod linią systemu operacyjnego. Odpowiedzialność za wszystkie aspekty usługi powyżej wiersza. System operacyjny jest odpowiedzialny za. Można nadal używać najnowszej metody w środowisku lokalnym, które mogą stosować zgodności, zabezpieczeń, zarządzanie aplikacjami, podstawy i systemu operacyjnego zarządzania. Systemy są wyświetlane, tak jakby znajdują się w Twojej sieci w odniesieniu do wszystkich.

Ta usługa jest zoptymalizowany do platformy SAP HANA, istnieje więc obszary, w których trzeba pracować z firma Microsoft ma wysyłać podstawowych możliwości infrastruktury w celu uzyskania najlepszych wyników.

Poniższa lista zawiera więcej szczegółów dotyczących każdej z warstw i Twoich obowiązkach:

**Sieć**: sieci wewnętrznych dla sygnatury duże wystąpienie uruchamiania oprogramowania SAP HANA. Odpowiedzialny za obejmuje dostęp do przechowywania danych, łączność między wystąpień (dla skalowalnego w poziomie i inne funkcje), łączność krajobrazu i połączeń z platformą Azure, w którym jest hostowana warstwa aplikacji SAP na maszynach wirtualnych. Zawiera on również połączenia z siecią WAN między centrami danych platformy Azure do replikacji celów odzyskiwania po awarii. Wszystkie sieci są partycjonowane na podstawie dzierżawy, a jakość usługi stosowane.

**Magazyn**: zwirtualizowany podzielona na partycje magazynu dla wszystkich woluminów wymagane przez serwery SAP HANA, a także migawki. 

**Serwery**: dedykowanych serwerów fizycznych do uruchamiania bazy danych HANA SAP, które zostały przypisane do dzierżawcy. Serwery typu I klasy jednostek SKU są sprzętowe wyodrębnione. Za pomocą następujących typów serwerów konfiguracji serwera są zbierane i przechowywane w profilach, mogą zostać przeniesione z jednego fizycznego sprzętu do innego sprzętu fizycznego. Takie (ręczne) przeniesienie profilu przez operacje można porównać coś do naprawianiem usług platformy Azure. Serwery jednostek SKU typu II klasy nie oferuje taką funkcję.

**SDDC**: oprogramowanie do zarządzania, który służy do zarządzania danymi centra jako jednostki zdefiniowanych przez oprogramowanie. Umożliwia firmy Microsoft do puli zasobów w celu zapewnienia skalowalności, dostępności i ze względu na wydajność.

**Systemu operacyjnego**: Wybierz (SUSE Linux lub systemie Red Hat Linux) systemu operacyjnego uruchomionego na serwerze. Obrazy systemu operacyjnego, które są dostarczane z dostarczonych przez indywidualne dostawcę systemu Linux do firmy Microsoft uruchamiania oprogramowania SAP HANA. Musi mieć subskrypcję u dostawcy systemu Linux dla określonego obrazu zoptymalizowane pod kątem oprogramowania SAP HANA. Ponosisz odpowiedzialność za zarejestrowanie obrazy z dostawcą systemu operacyjnego. 

Od momentu przekazania przez firmę Microsoft odpowiedzialność za wszelkie dalsze stosowanie poprawek systemu operacyjnego Linux. Tej poprawki, które obejmuje dodatkowych pakietów, która może być konieczne do pomyślnej instalacji oprogramowania SAP HANA i które nie zostały zawarte przez określonego dostawcę systemu Linux w ich platformy SAP HANA zoptymalizowanych obrazów systemu operacyjnego. (Aby uzyskać więcej informacji, zobacz dokumentacji instalacji oprogramowania HANA SAP i SAP Notes). 

Odpowiedzialność za stosowania poprawek systemu operacyjnego z powodu nieprawidłowego działania lub Optymalizacja systemu operacyjnego i jego sterowniki względem sprzętu określonego serwera. Możesz również są odpowiedzialne za bezpieczeństwo lub funkcjonalności stosowanie poprawek systemu operacyjnego. 

Odpowiedzialny za zawiera także monitorowania i planowania wydajności:

- Użycie zasobów Procesora.
- Zużycie pamięci.
- Ilość wolnego miejsca, operacje We/Wy i opóźnienia związane z woluminami.
- Wolumin ruchem sieciowym między dużych wystąpień HANA i warstwy aplikacji SAP.

Podstawowa infrastruktura dużych wystąpień HANA oferuje funkcję tworzenia kopii zapasowych i przywracanie woluminu systemu operacyjnego. Za pomocą tej funkcji jest również odpowiedzialny za.

**Oprogramowanie pośredniczące**: przede wszystkim wystąpienia platformy SAP HANA. Administracja, operacji i monitorowania, spoczywa wyłącznie usługi. Funkcjonalność podana służy do użycia magazynu migawek dla celów odzyskiwania kopii zapasowej i przywracanie i odzyskiwanie po awarii. Te możliwości są zapewniane przez infrastrukturę. Twoje obowiązki obejmują również projektowania stopnia dostępności i odzyskiwania po awarii dzięki takim funkcjom korzystanie z nich i monitorowania w celu określenia, czy migawek magazynu wykonany pomyślnie.

**Dane**: danych zarządzanych przez platformę SAP HANA i inne dane, takie jak kopie zapasowe udziałów plików znajdujących się na woluminach lub pliku. Twoje obowiązki obejmują monitorowania wolnego miejsca na dysku i zarządzania zawartością w woluminach. Możesz również są odpowiedzialne za monitorowanie pomyślne wykonanie kopii zapasowych woluminów dysków i migawek magazynu. Pomyślne wykonanie replikacji danych do lokacji odzyskiwania po awarii jest odpowiedzialność firmy Microsoft.

**Aplikacje:** wystąpień aplikacji SAP lub, w przypadku aplikacji innych niż SAP, warstwy aplikacji w tych aplikacjach. Twoje obowiązki obejmują wdrażania, administrowania, operacji i monitorowania tych aplikacji. Odpowiedzialność za planowanie pojemności zużycia zasobów procesora CPU, użycie pamięci, użycie usługi Azure Storage i zużycie przepustowości sieci w ramach sieci wirtualnej. Możesz również odpowiadają za planowanie pojemności dla zużycia zasobów z sieci wirtualnych do platformy SAP HANA na platformie Azure (duże wystąpienia).

**Sieci WAN**: połączenia ustanowić ze środowiska lokalnego do wdrożeń platformy Azure dla obciążeń. Wszyscy klienci z dużych wystąpień HANA usługa Azure ExpressRoute dla łączności. To połączenie nie jest częścią platformy SAP HANA na platformie Azure (duże wystąpienia) rozwiązanie. Odpowiedzialność za instalację tego połączenia.

**Archiwum**: można wybrać do archiwizacji kopii danych przy użyciu własnych metod w ramach kont magazynu. Archiwizacja wymaga zarządzania, zgodności, koszty i operacji. Odpowiedzialność za Generowanie archiwum i kopii zapasowych na platformie Azure i przechowywania ich w sposób zgodny z.

Zobacz [umowa SLA dla oprogramowania SAP HANA na platformie Azure (duże wystąpienia)](https://azure.microsoft.com/support/legal/sla/sap-hana-large/v1_0/).

**Następne kroki**
- Zapoznaj się [architektura SAP HANA (duże wystąpienia) na platformie Azure](hana-architecture.md)