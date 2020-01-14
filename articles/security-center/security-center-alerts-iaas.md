---
title: Wykrywanie zagrożeń dla maszyn wirtualnych i serwerów w Azure Security Center | Microsoft Docs
description: W tym temacie przedstawiono alerty dotyczące maszyn wirtualnych i serwerów dostępne w Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: dd2eb069-4c76-4154-96bb-6e6ae553ef46
ms.service: security-center
ms.topic: conceptual
ms.date: 01/05/2020
ms.author: memildin
ms.openlocfilehash: a5ed91cef6e49fcb71c35f2262479be45a018651
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2020
ms.locfileid: "75754307"
---
# <a name="threat-detection-for-vms-and-servers-in-azure-security-center"></a>Wykrywanie zagrożeń dla maszyn wirtualnych i serwerów w Azure Security Center

W tym temacie przedstawiono różne typy metod wykrywania i alerty dostępne dla maszyn wirtualnych i serwerów z następującymi systemami operacyjnymi. Aby uzyskać listę obsługiwanych wersji, zobacz [platform i funkcje obsługiwane przez Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-os-coverage).

* [Windows](#windows-machines)
* [Linux](#linux-machines)

## Systemy<a name="windows-machines"></a>

Azure Security Center integruje się z usługami platformy Azure w celu monitorowania i ochrony maszyn z systemem Windows. Security Center przedstawia alerty i sugestie dotyczące korygowania ze wszystkich tych usług w łatwym do użycia formacie.

* Usługa **Microsoft Defender ATP** <a name="windows-atp"></a> — Security Center rozszerza swoją platformę ochrony obciążeń w chmurze przez integrację z usługą Microsoft Defender Advanced Threat Protection (ATP). Zapewnia to kompleksowe możliwości wykrywania i reagowania punktów końcowych (EDR).

    > [!NOTE]
    > Czujnik ATP programu Microsoft Defender jest automatycznie włączany na serwerach z systemem Windows, które używają Security Center.

    Gdy usługa Microsoft Defender ATP wykryje zagrożenie, wyzwala alert. Alert jest wyświetlany na pulpicie nawigacyjnym Security Center. Z poziomu pulpitu nawigacyjnego można przestawiać do konsoli usługi Microsoft Defender ATP i przeprowadzić szczegółowe badanie w celu odzyskania zakresu ataku. Aby uzyskać więcej informacji o usłudze Microsoft Defender ATP, zobacz Dołączanie [serwerów do usługi Microsoft Defender ATP](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints).

* <a name="windows-dump"></a> **Analiza zrzutu awaryjnego** — w przypadku awarii oprogramowania zrzut awaryjny przechwytuje część pamięci w chwili awarii.

    Awaria może być spowodowana przez złośliwe oprogramowanie lub zawiera złośliwe oprogramowanie. Aby uniknąć wykrycia przez produkty zabezpieczające, różne rodzaje złośliwego oprogramowania korzystają z ataku bez plików, który pozwala uniknąć zapisywania na dysku lub zaszyfrować składniki oprogramowania napisane na dysku. Atak tego typu jest trudny do wykrycia przy użyciu tradycyjnych metod opartych na dyskach.

    Jednak za pomocą analizy pamięci można wykryć ten rodzaj ataku. Analizując pamięć w zrzucie awaryjnym, Security Center mogą wykrywać techniki używane przez atak. Na przykład atakujący może próbować wykorzystać luki w zabezpieczeniach oprogramowania, uzyskać dostęp do poufnych danych i dyskretnego pozostania go w ramach zagrożonego komputera. Security Center działa z minimalnym wpływem na wydajność hostów.

    Aby uzyskać szczegółowe informacje na temat alertów analizy zrzutu awaryjnego, zobacz [tabelę referencyjną alertów](alerts-reference.md#alerts-windows).

* <a name="windows-fileless"></a> **Wykrywanie ataków bezplikowych** — ataki bezplikowe ukierunkowane na punkty końcowe są wspólne. Aby uniknąć wykrywania, ataki bez plików wsuwają złośliwe ładunki do pamięci. Ładunki osoby atakującej pozostają w dużej ilości zagrożonych procesów i wykonują szeroką gamę złośliwych działań.

    W przypadku wykrywania ataków bez plików, zautomatyzowane metody śledczej pamięci identyfikują narzędzia, techniki i zachowania ataku bez plików. To rozwiązanie okresowo skanuje maszynę w czasie wykonywania i wyodrębnia informacje bezpośrednio z pamięci procesów krytycznych dla zabezpieczeń.

    Pozwala znaleźć dowód wykorzystania, iniekcję kodu i wykonywanie złośliwych ładunków. Wykrywanie ataków bezplikowych generuje szczegółowe alerty zabezpieczeń, które przyspieszają Klasyfikacja alertów, korelację i czas odpowiedzi podrzędnej. To podejście uzupełnia oparte na zdarzeniach rozwiązania EDR, co zapewnia większe pokrycie wykrywania.

    Aby uzyskać szczegółowe informacje o alertach dotyczących wykrywania ataków bez plików, zobacz [tabelę referencyjną alertów](alerts-reference.md#alerts-windows).

> [!NOTE]
> Alerty systemu Windows można symulować przez pobranie [Azure Security Center element PlayBook: alerty zabezpieczeń](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046).

## System<a name="linux-machines"></a>

Security Center zbiera rekordy inspekcji z maszyn z systemem Linux przy użyciu **inspekcji**, jednej z najpopularniejszych struktur inspekcji systemu Linux. Inspekcja w jądrze linii głównej. 

* Monitorowane **alerty i Microsoft Monitoring Agent (MMA)** <a name="linux-auditd"></a> z systemem Linux — system podlegający inspekcji składa się z podsystemu poziomu jądra, który jest odpowiedzialny za wywołania systemu monitorowania. Filtruje je według określonego zestawu reguł i zapisuje komunikaty dla nich w gnieździe. Security Center integruje funkcje z pakietu objętego inspekcją w ramach Microsoft Monitoring Agent (MMA). Ta Integracja umożliwia zbieranie zdarzeń inspekcji we wszystkich obsługiwanych dystrybucjach systemu Linux bez wymagań wstępnych.  

    rekordy poddane inspekcji są zbierane, wzbogacane i agregowane do zdarzeń przy użyciu agenta MMA systemu Linux. Security Center ciągle dodaje nową analizę, która używa sygnałów systemu Linux do wykrywania złośliwych zachowań na maszynach w chmurze i lokalnych systemach Linux. Podobnie jak w przypadku funkcji systemu Windows, te analizy obejmują między podejrzanymi procesami, podejrzanych prób logowania, ładowanie modułu jądra i inne działania. Te działania mogą wskazywać, że komputer jest atakowany lub został naruszony.  

    Poniżej przedstawiono kilka przykładów analizy, które rozciągają się na różne etapy cyklu życia.

    Listę alertów systemu Linux można znaleźć w [tabeli referencyjnej alertów](alerts-reference.md#alerts-linux).

> [!NOTE]
> Alerty systemu Linux można symulować przez pobranie [Azure Security Center element PlayBook: wykrywania systemu Linux](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef).

 
 ## <a name="next-steps"></a>Następne kroki

Aby uzyskać przykłady i więcej informacji na temat wykrywania Security Center, zobacz:

* [Jak Azure Security Center automatyzuje wykrywanie ataku cybernetycznymi](https://azure.microsoft.com/blog/leverage-azure-security-center-to-detect-when-compromised-linux-machines-attack/)
* [Jak Azure Security Center wykrywa luki w zabezpieczeniach przy użyciu narzędzi administracyjnych](https://azure.microsoft.com/blog/azure-security-center-can-detect-emerging-vulnerabilities-in-linux/)
* [Skorzystaj z Azure Security Center, aby wykryć, kiedy atak na komputery z systemem Linux zostanie naruszony](https://azure.microsoft.com/blog/leverage-azure-security-center-to-detect-when-compromised-linux-machines-attack/)
* [Azure Security Center może wykryć pojawiające się luki w zabezpieczeniach systemu Linux](https://azure.microsoft.com/blog/azure-security-center-can-detect-emerging-vulnerabilities-in-linux/)