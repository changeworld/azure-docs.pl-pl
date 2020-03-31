---
title: Fizyczne bezpieczeństwo centrów danych platformy Azure — Microsoft Azure | Dokumenty firmy Microsoft
description: W tym artykule opisano, co firma Microsoft robi, aby zabezpieczyć centra danych platformy Azure, w tym infrastrukturę fizyczną, zabezpieczenia i oferty zgodności.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/28/2019
ms.author: terrylan
ms.openlocfilehash: 634f89b3123902d981ad07dd1404315387104322
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68726724"
---
# <a name="azure-facilities-premises-and-physical-security"></a>Obiekty platformy Azure, pomieszczenia i zabezpieczenia fizyczne
W tym artykule opisano, co firma Microsoft robi, aby zabezpieczyć infrastrukturę platformy Azure.

## <a name="datacenter-infrastructure"></a>Infrastruktura centrum danych
Platforma Azure składa się z [globalnie rozproszonej infrastruktury centrum danych,](https://azure.microsoft.com/global-infrastructure/)obsługującej tysiące usług online i obejmującej ponad 100 wysoce bezpiecznych obiektów na całym świecie.

Infrastruktura została zaprojektowana tak, aby przybliżać aplikacje użytkownikom na całym świecie, zachowując miejsce zamieszkania danych i oferując kompleksowe opcje zgodności i odporności dla klientów. Platforma Azure ma 52 regiony na całym świecie i jest dostępna w 140 krajach/regionach.

Region to zestaw centrów danych, które są połączone za pośrednictwem ogromnej i odpornej sieci. Sieć domyślnie obejmuje dystrybucję zawartości, równoważenie obciążenia, nadmiarowość i szyfrowanie. Dzięki większej liczbie regionów globalnych niż jakikolwiek inny dostawca chmury platforma Azure zapewnia elastyczność wdrażania aplikacji tam, gdzie ich potrzebujesz.

Regiony platformy Azure są zorganizowane w regiony geograficzne. Geografia platformy Azure zapewnia, że wymagania dotyczące rezydencji danych, suwerenności, zgodności i odporności są przestrzegane w granicach geograficznych.

Obszary geograficzne umożliwiają klientom, którzy mają określone potrzeby związane z rezydencją danych i zgodnością, przechowywanie ich danych i aplikacji w bliskim sąsiedztwie. Obszary geograficzne są odporne na uszkodzenia, aby wytrzymać całkowitą awarię regionu, dzięki połączeniu z dedykowaną infrastrukturą sieciową o dużej pojemności.

Strefy dostępności są fizycznie oddzielne lokalizacje w regionie platformy Azure. Każda strefa dostępności składa się z co najmniej jednego centrum danych wyposażonego w niezależne zasilanie, chłodzenie i sieć. Strefy dostępności umożliwiają uruchamianie aplikacji o znaczeniu krytycznym o wysokiej dostępności i replikacji z małymi opóźnieniami.

Na poniższej ilustracji pokazano, jak globalna infrastruktura platformy Azure paruje regiony i strefy dostępności w obrębie tej samej granicy rezydencji danych, aby uzyskać wysoką dostępność, odzyskiwanie po awarii i tworzenie kopii zapasowych.

![Diagram przedstawiający granicę rezydencji danych](./media/physical-security/data-residency-boundary.png)

Geograficznie rozproszone centra danych umożliwiają firmie Microsoft bliskość klientów, zmniejszenie opóźnień w sieci i umożliwienie geograficznie nadmiarowej kopii zapasowej i pracy awaryjnej.

## <a name="physical-security"></a>Zabezpieczenia fizyczne
Firma Microsoft projektuje, tworzy i obsługuje centra danych w sposób ściśle kontrolujący fizyczny dostęp do obszarów, w których przechowywane są dane. Firma Microsoft rozumie znaczenie ochrony danych i dokłada wszelkich starań, aby pomóc w zabezpieczeniu centrów danych zawierających dane. W firmie Microsoft mamy cały dział poświęcony projektowaniu, tworzeniu i obsłudze obiektów fizycznych obsługujących platformę Azure. Ten zespół jest inwestowany w utrzymanie najnowocześniejszych zabezpieczeń fizycznych.

Firma Microsoft przyjmuje warstwowe podejście do zabezpieczeń fizycznych, aby zmniejszyć ryzyko uzyskania przez nieautoryzowanych użytkowników fizycznego dostępu do danych i zasobów centrum danych. Centra danych zarządzane przez firmę Microsoft mają rozbudowane warstwy ochrony: zatwierdzanie dostępu na obwodzie obiektu, na obwodzie budynku, wewnątrz budynku i na piętrze centrum danych. Warstwy zabezpieczeń fizycznych to:

- **Żądanie dostępu i zatwierdzenie.** Należy zażądać dostępu przed przybyciem do centrum danych. Musisz podać ważne uzasadnienie biznesowe wizyty, takie jak zgodność lub inspekcja. Wszystkie żądania są zatwierdzane na zasadzie konieczności dostępu przez pracowników firmy Microsoft. Podstawa dostępu do potrzeb pomaga utrzymać liczbę osób potrzebnych do wykonania zadania w centrach danych do minimum. Po udzielieniu uprawnień przez firmę Microsoft osoba fizyczna ma dostęp tylko do dyskretnego obszaru wymaganego centrum danych, na podstawie zatwierdzonego uzasadnienia biznesowego. Uprawnienia są ograniczone do określonego okresu czasu, a następnie wygasają.

- **obwodu obiektu.** Po dotarciu do centrum danych musisz przejść przez dobrze zdefiniowany punkt dostępu. Zazwyczaj wysokie ogrodzenia wykonane ze stali i betonu obejmują każdy centymetr obwodu. Wokół centrów danych znajdują się kamery, a zespół bezpieczeństwa cały czas monitoruje ich filmy.

- **Wejście do budynku.** Wejście do centrum danych jest obsadzone przez profesjonalnych pracowników ochrony, którzy przeszli rygorystyczne szkolenia i kontrole przeszłości. Ci funkcjonariusze ochrony również rutynowo patrolują centrum danych i monitorują filmy z kamer wewnątrz centrum danych przez cały czas.

- **Wewnątrz budynku.** Po wejściu do budynku należy przekazać uwierzytelnianie dwuskładnikowe z danymi biometrycznymi, aby kontynuować przechodzenie przez centrum danych. Jeśli twoja tożsamość zostanie zweryfikowana, można wprowadzić tylko część centrum danych, do której masz zatwierdzony dostęp. Możesz tam przebywać tylko przez czas zatwierdzony.

- **Podłoga centrum danych.** Na podłogę, na którą masz zgodę, do wejścia, wpuszczono tylko podłogę. Wymagane jest przejście pełnego badania przesiewowego wykrywania metali. Aby zmniejszyć ryzyko nieautoryzowanego wprowadzania lub opuszczania centrum danych bez naszej wiedzy, tylko zatwierdzone urządzenia mogą dostać się do podłogi centrum danych. Ponadto kamery wideo monitorują przód i tył każdego stojaka serwerowego. Po wyjściu z podłogi centrum danych ponownie należy przejść przez pełne przesiewanie wykrywania metali. Aby opuścić centrum danych, musisz przejść przez dodatkowe skanowanie zabezpieczeń.

Firma Microsoft wymaga od odwiedzających, aby po wyjeździe z dowolnego obiektu firmy Microsoft przekazyli odznaki.

## <a name="physical-security-reviews"></a>Przeglądy zabezpieczeń fizycznych
Okresowo przeprowadzamy fizyczne przeglądy zabezpieczeń obiektów, aby upewnić się, że centra danych prawidłowo spełniają wymagania zabezpieczeń platformy Azure. Personel dostawcy hostingu centrum danych nie zapewnia zarządzania usługami platformy Azure. Personel nie może zalogować się do systemów platformy Azure i nie ma fizycznego dostępu do pokoju kolokacji platformy Azure i klatek.

## <a name="data-bearing-devices"></a>Urządzenia do opatrzonych danymi
Firma Microsoft korzysta z najlepszych procedur i rozwiązania do wycierania [zgodnego ze standardem NIST 800-88.](https://csrc.nist.gov/publications/detail/sp/800-88/archive/2006-09-01) W przypadku dysków twardych, których nie można wyczyścić, używamy procesu niszczenia, który je niszczy i uniemożliwia odzyskanie informacji. Ten proces niszczenia może być rozpad, strzęp, sproszkować lub spalać. Określamy środki zbycia w zależności od rodzaju środka trwałego. Przechowujemy zapisy zniszczenia.  

## <a name="equipment-disposal"></a>Utylizacja sprzętu
Po zakończeniu eksploatacji systemu personel operacyjny firmy Microsoft przestrzega rygorystycznych procedur obsługi danych i usuwania sprzętu, aby upewnić się, że sprzęt zawierający dane użytkownika nie jest udostępniany niezaufanym stronom. Używamy bezpiecznego podejścia do usuwania dysków twardych, które go obsługują. W przypadku dysków twardych, których nie można wyczyścić, używamy procesu niszczenia, który niszczy dysk i uniemożliwia odzyskiwanie informacji. Ten proces niszczenia może być rozpad, strzęp, sproszkować lub spalać. Określamy środki zbycia w zależności od rodzaju środka trwałego. Przechowujemy zapisy zniszczenia. Wszystkie usługi platformy Azure korzystają z zatwierdzonych usług przechowywania multimediów i zarządzania usuwaniem.

## <a name="compliance"></a>Zgodność
Projektujemy infrastrukturę platformy Azure i zarządzamy nią, aby spełnić szeroki zestaw międzynarodowych i branżowych standardów zgodności, takich jak ISO 27001, HIPAA, FedRAMP, SOC 1 i SOC 2. Spełniamy również normy specyficzne dla kraju lub regionu, w tym Australia IRAP, UK G-Cloud i Singapur MTCS. Rygorystyczne audyty innych firm, takie jak te przeprowadzane przez British Standards Institute, weryfikują przestrzeganie ścisłych kontroli bezpieczeństwa, które nakazują te normy.

Aby uzyskać pełną listę standardów zgodności, których przestrzega platforma Azure, zobacz [oferty zgodności.](https://www.microsoft.com/trustcenter/compliance/complianceofferings)

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o tym, co firma Microsoft robi, aby zabezpieczyć infrastrukturę platformy Azure, zobacz:

- [Dostępność infrastruktury platformy Azure](infrastructure-availability.md)
- [Składniki i granice systemu informacyjnego platformy Azure](infrastructure-components.md)
- [Architektura sieci platformy Azure](infrastructure-network.md)
- [Sieć produkcyjna platformy Azure](production-network.md)
- [Funkcje zabezpieczeń usługi Azure SQL Database](infrastructure-sql.md)
- [Operacje produkcyjne i zarządzanie platformą Azure](infrastructure-operations.md)
- [Monitorowanie infrastruktury platformy Azure](infrastructure-monitoring.md)
- [Integralność infrastruktury platformy Azure](infrastructure-integrity.md)
- [Ochrona danych klientów platformy Azure](protection-customer-data.md)


