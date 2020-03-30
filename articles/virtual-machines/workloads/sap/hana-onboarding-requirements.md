---
title: Wymagania dotyczące dołączania dla systemu SAP HANA na platformie Azure (duże wystąpienia) | Dokumenty firmy Microsoft
description: Wymagania dotyczące dołączania dla usługi SAP HANA na platformie Azure (duże wystąpienia).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/31/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 141a75a75a214ff4a6f136df7570d6e81f7f4e82
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617001"
---
# <a name="onboarding-requirements"></a>Wymagania dotyczące dołączania

Ta lista zawiera wymagania dotyczące uruchamiania usługi SAP HANA na platformie Azure (większe wystąpienia).

**Microsoft Azure**

- Subskrypcja platformy Azure, która może być połączona z sap HANA na platformie Azure (duże wystąpienia).
- Umowa pomocy technicznej programu Microsoft Premier. Aby uzyskać szczegółowe informacje dotyczące uruchamiania systemu SAP na platformie Azure, zobacz [Uwaga dotycząca obsługi SAP #2015553 — SAP na platformie Microsoft Azure: Wymagania wstępne dotyczące pomocy technicznej.](https://launchpad.support.sap.com/#/notes/2015553) Jeśli używasz jednostek dużych wystąpień HANA z procesorami 384 i więcej, należy również rozszerzyć umowę pomocy technicznej Premier, aby uwzględnić usługę Azure Rapid Response.
- Świadomość jednostek SKU dużych wystąpień HANA, których potrzebujesz po wykonaniu ćwiczenia zmiany rozmiaru za pomocą systemu SAP.

**Łączność sieciowa**

- Usługa ExpressRoute między lokalnymi usługami azure: Aby połączyć lokalne centrum danych z platformą Azure, należy zamówić połączenie co najmniej 1 Gb/s od usługodawcy internetowego. Łączność między jednostkami dużych wystąpień HANA a platformą Azure również korzysta z technologii Usługi ExpressRoute. To połączenie usługi ExpressRoute między jednostkami dużych wystąpień HANA a platformą Azure jest wliczone w cenę jednostek dużych wystąpień HANA, w tym wszystkie opłaty za transfer danych i wychodzące dla tego konkretnego obwodu usługi ExpressRoute. W związku z tym jako klient nie napotkać dodatkowych kosztów poza łącze usługi ExpressRoute między lokalnego i platformy Azure.

**System operacyjny**

- Licencje na SUSE Linux Enterprise Server 12 dla aplikacji SAP.

   > [!NOTE] 
   > System operacyjny dostarczony przez firmę Microsoft nie jest zarejestrowany w SUSE. Nie jest połączony z wystąpieniem narzędzia do zarządzania subskrypcją.

- Narzędzie do zarządzania subskrypcją systemu Linux SUSE wdrożone na platformie Azure na maszynie wirtualnej. To narzędzie zapewnia możliwość SAP HANA na platformie Azure (duże wystąpienia) do zarejestrowania i odpowiednio aktualizowane przez SUSE. (W centrum danych hana large instance nie ma dostępu do Internetu). 
- Licencje na system Red Hat Enterprise Linux 6.7 lub 7.x dla SAP HANA.

   > [!NOTE]
   > System operacyjny dostarczony przez firmę Microsoft nie jest zarejestrowany w Red Hat. Nie jest połączony z wystąpieniem Menedżera subskrypcji Red Hat.

- Red Hat Subscription Manager wdrożony na platformie Azure na maszynie Wirtualnej. Menedżer subskrypcji Red Hat zapewnia możliwość sap HANA na platformie Azure (duże wystąpienia) do zarejestrowania i odpowiednio aktualizowane przez Red Hat. (Nie ma bezpośredniego dostępu do Internetu z poziomu dzierżawy wdrożonej na sieczmię Azure Large Instance.)
- SAP wymaga, aby mieć umowę pomocy technicznej z dostawcą systemu Linux, jak również. To wymaganie nie jest usuwany przez rozwiązanie HANA duże wystąpienie lub fakt, że można uruchomić system Linux na platformie Azure. W przeciwieństwie do niektórych obrazów galerii platformy Azure systemu Linux opłata za usługę *nie* jest uwzględniona w ofercie rozwiązania hana duże wystąpienie. Twoim obowiązkiem jest spełnienie wymagań SAP dotyczących umów pomocy technicznej z dystrybutorem systemu Linux. 
   - W przypadku systemu SUSE Linux należy sprawdzić wymagania umów pomocy technicznej w [sap Note #1984787 - SUSE Linux Enterprise Server 12: Installation notes](https://launchpad.support.sap.com/#/notes/1984787) and SAP Note #1056161 - [SUSE priority support for SAP applications](https://launchpad.support.sap.com/#/notes/1056161).
   - W przypadku systemu Red Hat Linux należy mieć odpowiednie poziomy subskrypcji, które obejmują aktualizacje pomocy technicznej i usługi do systemów operacyjnych hana duże wystąpienie. Red Hat zaleca subskrypcję red hat enterprise linux dla rozwiązania SAP. Patrz https://access.redhat.com/solutions/3082481. 

Aby uzyskać macierz obsługi różnych wersji SAP HANA z różnymi wersjami systemu Linux, zobacz [SAP Note #2235581](https://launchpad.support.sap.com/#/notes/2235581).

Aby uzyskać matrycę zgodności systemu operacyjnego i wersji oprogramowania układowego/sterownika HLI, zapoznaj się [z programem OS Upgrade for HLI](os-upgrade-hana-large-instance.md).


> [!IMPORTANT] 
> W przypadku jednostek typu II w tym momencie obsługiwana jest tylko wersja systemu operacyjnego SP2 SLES 12. 


**baza danych**

- Licencje i składniki instalacji oprogramowania dla SAP HANA (wersja platformowa lub enterprise).

**Aplikacje**

- Licencje i składniki instalacji oprogramowania dla wszystkich aplikacji SAP, które łączą się z SAP HANA i powiązanymi umowami pomocy technicznej SAP.
- Licencje i składniki instalacji oprogramowania dla wszystkich aplikacji innych niż SAP używane z SAP HANA na platformie Azure (duże wystąpienia) środowiska i powiązanych umów pomocy technicznej.

**Umiejętności**

- Doświadczenie i znajomość usługi Azure IaaS i jej składników.
- Doświadczenie i wiedza na temat wdrażania obciążenia SAP na platformie Azure.
- Instalacja SAP HANA certyfikowana jako osobista.
- Umiejętności architekta SAP do projektowania wysokiej dostępności i odzyskiwania po awarii wokół SAP HANA.

**SAP**

- Oczekuje się, że jesteś klientem SAP i masz umowę pomocy technicznej z SAP.
- Specjalnie w przypadku implementacji klasy typu II jednostek SKU dużych wystąpień HANA, skonsultuj się z SAP w sprawie wersji sap HANA i ewentualnych konfiguracji na dużym sprzęcie skalowania w górę.

**Następne kroki**
- Odwoływanie się do [architektury SAP HANA (duże wystąpienia) na platformie Azure](hana-architecture.md)