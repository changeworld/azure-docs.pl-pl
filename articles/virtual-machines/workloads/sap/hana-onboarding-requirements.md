---
title: Dołączanie wymagania dotyczące oprogramowania SAP HANA na platformie Azure (duże wystąpienia) | Dokumentacja firmy Microsoft
description: Wymagania dotyczące przechodzenia do platformy SAP HANA na platformie Azure (duże wystąpienia).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/31/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2f596f44acfd51b1e2449bc77eed6add0d9d90b0
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707389"
---
# <a name="onboarding-requirements"></a>Wymagania dotyczące dołączania

Ta lista składa się wymagania dotyczące uruchamiania oprogramowania SAP HANA na platformie Azure (wystąpienia większy).

**Microsoft Azure**

- Subskrypcja platformy Azure, które mogą być połączone z platformą SAP HANA na platformie Azure (duże wystąpienia).
- Umowę na pomoc techniczną Premium firmy Microsoft. Aby uzyskać szczegółowe informacje związane z systemem SAP na platformie Azure, zobacz [2015553 # Uwaga SAP pomocy technicznej — SAP w systemie Microsoft Azure: Wymagania wstępne dotyczące obsługi](https://launchpad.support.sap.com/#/notes/2015553). Jeśli używasz jednostek dużych wystąpień HANA 384 i większej liczby procesorów CPU, również należy rozszerzyć Premier umowę pomocy technicznej, aby uwzględnić Azure Rapid Response.
- Rozpoznawanie HANA duże wystąpienie jednostki SKU należy po wykonaniu wykonywania zmiany rozmiaru, korzystając z oprogramowania SAP.

**Połączenie sieciowe**

- ExpressRoute między środowiska lokalnego do platformy Azure: Do łączenia z centrum danych lokalnych do platformy Azure, upewnij się uporządkować połączenia o wielkości co najmniej 1 GB/s od usługodawcy internetowego. Łączność między jednostkami dużych wystąpień HANA i Azure używa także technologii usługi ExpressRoute. To połączenie usługi ExpressRoute między jednostkami dużych wystąpień HANA i Azure znajduje się w cenie jednostki dużych wystąpień HANA, w tym wszystkie dane przychodzące i wychodzące opłaty dla tego określonego obwodu usługi ExpressRoute. W związku z tym jako klient, czy nie występują dodatkowe koszty poza link usługi ExpressRoute między lokalną i platformą Azure.

**System operacyjny**

- Licencje dla SUSE Linux Enterprise Server 12 dla aplikacji SAP.

   > [!NOTE] 
   > System operacyjny, dostarczone przez firmę Microsoft nie jest zarejestrowany w usłudze SUSE. Nie jest ona dołączona do wystąpienia narzędzia do zarządzania subskrypcją.

- SUSE Linux subskrypcji narzędzia do zarządzania wdrożonymi na platformie Azure na maszynie Wirtualnej. To narzędzie zapewnia funkcje platformy SAP Hana na platformie Azure (duże wystąpienia) były rejestrowane i odpowiednio aktualizowana przez SUSE. (Brak jest Brak dostępu do Internetu w centrum danych dużych wystąpień HANA). 
- Licencji Red Hat Enterprise Linux 6.7 lub 7.x dla oprogramowania SAP HANA.

   > [!NOTE]
   > System operacyjny, dostarczone przez firmę Microsoft nie jest zarejestrowana z firmą Red Hat. Nie jest ona dołączona do wystąpienia systemu Red Hat subskrypcji Menedżera.

- Red Hat subskrypcji Menedżera wdrożonych na platformie Azure na maszynie Wirtualnej. Menedżer subskrypcji Red Hat zapewnia funkcje platformy SAP Hana na platformie Azure (duże wystąpienia), aby były rejestrowane i odpowiednio aktualizowane przez firmy Red Hat. (Nie jest brak bezpośredniego dostępu do Internetu z w ramach dzierżawy wdrożone w sygnaturze dużego wystąpienia platformy Azure).
- Środowisko SAP wymaga posiadania pomocy technicznej kontraktu u swojego dostawcy systemu Linux. To wymaganie nie są usuwane przez to rozwiązanie, dużych wystąpień HANA lub faktu uruchomienia systemu Linux na platformie Azure. W przeciwieństwie do niektórych obrazów Galeria Azure dla systemu Linux, opłata za usługę jest *nie* uwzględnione w ofercie rozwiązania dużych wystąpień HANA. Jest odpowiedzialny za spełniają wymagania SAP dotyczące umowy pomocy technicznej za pomocą dystrybutora systemu Linux. 
   - W przypadku systemu SUSE Linux wyszukać wymagania umowy pomocy technicznej w [1984787 # Uwaga SAP — SUSE Linux Enterprise Server 12: Uwagi dotyczące instalacji](https://launchpad.support.sap.com/#/notes/1984787) i [1056161 # Uwaga SAP — SUSE priorytetowa pomoc techniczna dla aplikacji SAP](https://launchpad.support.sap.com/#/notes/1056161).
   - Dla systemu Red Hat Linux musisz mieć poziomy poprawną subskrypcję, które obejmują pomoc techniczną i aktualizowaniem, systemów operacyjnych, HANA, duże wystąpienia usługi. Red Hat zaleca subskrypcji Red Hat Enterprise Linux dla rozwiązania SAP. Zapoznaj się https://access.redhat.com/solutions/3082481. 

Macierz obsługi różnych wersji platformy SAP HANA z użyciem różnych wersji systemu Linux, można zobaczyć [2235581 # Uwaga SAP](https://launchpad.support.sap.com/#/notes/2235581).

Macierz zgodności systemu operacyjnego i wersji oprogramowania układowego/driver HLI, można znaleźć [uaktualnienia systemu operacyjnego dla HLI](os-upgrade-hana-large-instance.md).


> [!IMPORTANT] 
> Dla typu II jednostek tylko w systemie SLES 12 z dodatkiem SP2 systemu operacyjnego wersja jest obsługiwana w tym momencie. 


**Baza danych**

- Licencje i składniki instalacyjne oprogramowania dla oprogramowania SAP HANA (platforma lub enterprise edition).

**Aplikacje**

- Licencje i składniki instalacyjne oprogramowania dla wszystkich aplikacji SAP, łączące się z platformą SAP HANA i powiązane SAP umowy dotyczące pomocy technicznej.
- Licencje i składniki instalacyjne oprogramowania dla wszystkich aplikacji innych niż SAP, używane z platformą SAP HANA na platformie Azure (duże wystąpienia) środowiska i związane z umowami pomocy technicznej.

**Umiejętności**

- Środowisko i wiedzy na temat infrastruktury IaaS platformy Azure i jego składników.
- Doświadczenia i wiedzy na temat sposobu wdrażania obciążeń SAP na platformie Azure.
- Instalacja oprogramowania SAP HANA certyfikowane osobistych.
- SAP Architekt umiejętności, aby zaprojektować wysokiej dostępności i odzyskiwania po awarii całego oprogramowania SAP HANA.

**SAP**

- Oczekuje się, że jesteś klientem usługi SAP i masz pomocy technicznej umowy z oprogramowaniem SAP.
- Szczególnie w przypadku implementacji klasy typu II jednostek SKU platformy HANA duże wystąpienie zapoznaj się z oprogramowaniem SAP wersje oprogramowania SAP HANA i ostateczną konfiguracje sprzętu skalowanie w górę o dużych rozmiarach.

**Następne kroki**
- Zapoznaj się [architektura SAP HANA (duże wystąpienia) na platformie Azure](hana-architecture.md)