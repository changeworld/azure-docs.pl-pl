---
title: Urządzenia platformy Azure, lokalnie i zabezpieczenia fizyczne | Dokumentacja firmy Microsoft
description: W artykule opisano centrach danych platformy Azure, w tym infrastruktury fizycznej, zabezpieczeń i ofertom obsługi zgodności.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 5f8378de18796fd679354c88880942df83399ad5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60586804"
---
# <a name="azure-facilities-premises-and-physical-security"></a>Urządzenia platformy Azure, lokalnie i zabezpieczenia fizyczne
Azure składa się z [infrastrukturę centrów danych rozproszonych globalnie](https://azure.microsoft.com/global-infrastructure/), obsługa tysięcy usług online i obejmujące więcej niż 100 kompleksowo zabezpieczonych obiektów na całym świecie.

Infrastruktura jest przeznaczona do przybliżania aplikacji do użytkowników na świecie, jednocześnie zachowuje rezydencję danych oraz oferując kompleksowe opcje zgodności i odporności dla klientów. Platforma Azure ma 52 regionów na całym świecie i jest dostępna w 140 krajach.

Region to zestaw centrów danych, która jest połączona za pośrednictwem sieci dużych i odporny na błędy. Domyślnie sieć zawiera dystrybucji zawartości, równoważenia obciążenia, nadmiarowość i szyfrowania. Za pomocą więcej regionów globalnych niż którykolwiek inny dostawca chmury platformy Azure zapewnia elastyczność do wdrażania aplikacji gdzie ich potrzebujesz.

Regiony świadczenia usługi Azure są zorganizowane w lokalizacje geograficzne. Lokalizacja geograficzna platformy Azure zapewnia, że wymagania z zakresu odporności, zgodności, niezależności i rezydencji danych są honorowane w granicach geograficznych.

Lokalizacje geograficzne pozwalają klientom z konkretnymi potrzebami w zakresie zgodności i rezydencji danych na przechowywanie swoich danych i aplikacji w bliskiej odległości. Lokalizacje geograficzne są odporne na uszkodzenia radzić sobie ze awarii całego regionu dzięki połączeniu z dedykowanym, dużej pojemności infrastruktury sieci.

Strefy dostępności to fizycznie oddzielone lokalizacje w regionie platformy Azure. Każda strefa dostępności składa się z co najmniej jeden centrów danych, wyposażone w niezależne zasilanie, chłodzenie i usługi sieciowe. Strefy dostępności umożliwiają uruchamianie kluczowych dla działalności aplikacji z wysoką dostępnością i replikacją o małych opóźnieniach.

Na poniższej ilustracji przedstawiono, jak globalna infrastruktura platformy Azure par regionów i dostępność strefy w ramach tej samej granicy rezydencji danych o wysokiej dostępności, odzyskiwania po awarii i tworzenie kopii zapasowych.

![Granicy rezydencji danych Wyświetlanie diagramu][1]

Geograficznie rozproszone centra danych umożliwia firmie Microsoft się blisko klientów w celu zmniejszenia opóźnienia sieci i umożliwienia geograficznie nadmiarowych kopii zapasowych i pracy awaryjnej.

## <a name="physical-security"></a>Zabezpieczenia fizyczne
Firma Microsoft projektuje, tworzy i działa centrów danych w sposób, który określa, ściśle fizyczny dostęp do obszarów, w którym dane są przechowywane. Firma Microsoft rozumie znaczenie ochrony danych i stara się zabezpieczaniem centrach danych, które zawierają dane. Mamy całego działu w firmie Microsoft poświęcona projektowanie, tworzenie i obsługa urządzenia fizyczne obsługi platformy Azure. Ten zespół jest inwestowanych w zachowaniu bezpieczeństwa fizycznego stanu grafiki.

Firma Microsoft podejmuje warstwowego podejścia do zabezpieczeń fizycznych, w celu ograniczenia ryzyka nieautoryzowanego użytkownicy, którzy mają fizyczny dostęp do danych i zasobów Centrum danych. Zarządzany przez firmę Microsoft centrów danych mają rozbudowane warstwom: dostęp do zatwierdzenia w sieci obwodowej funkcji, która znajduje się w sieci obwodowej budynku, wewnątrz budynku i piętra centrum danych. Warstwy zabezpieczeń fizycznych są:

- **Żądanie dostępu i zatwierdzania.** Należy zażądać dostępu przed dotarciem do centrum danych. Musisz podać uzasadnienie biznesowe, usługi można znaleźć pod adresem, takich jak zgodność lub inspekcji. Wszystkie żądania są zatwierdzane na podstawie dostępu konieczne przez pracowników firmy Microsoft. Podstawa dostępu konieczne pomaga liczba osób potrzebne do ukończenia zadania w centrach danych, aby absolutnego minimum. Po firma Microsoft nie przyznaje uprawnienia, osoba ma dostęp tylko do obszaru dyskretnych centrum danych, wymagane, oparte na uzasadnienie biznesowe zatwierdzone. Uprawnienia są ograniczone do pewien czas, a następnie wygaśnie.

- **Obwodowej placówki.** Po przyjeździe do centrum danych, musisz przejść przez punkt dostępu dobrze zdefiniowane. Zazwyczaj wysokości ogrodzenia stali i konkretny obejmują każdy cal sieci obwodowej. Brak kamer wokół centrów danych, z zespołem zabezpieczeń monitorowania własnych filmów wideo przez cały czas.

- **Tworzenie wejścia.** Wejście centrum danych jest obsadzony z specjaliści ds. profesjonalnych bezpieczeństwa które przeszły rygorystyczne testy szkolenia i tła. Te specjaliści ds. bezpieczeństwa również regularnie patrolowania centrum danych i Monitoruj wideo kamery wewnątrz centrum danych przez cały czas.

- **Budynku.** Po wprowadzeniu budynku, musi pomyślnie przejść uwierzytelnianie dwuskładnikowe za pomocą biometria, aby kontynuować, Nawigacja po centrum danych. Jeśli tożsamości zostanie zweryfikowana, można wprowadzić tylko część centrum danych, które zostały zatwierdzone dostęp do. Nadal istnieje tylko na okres czasu, zatwierdzone.

- **Datacenter floor.** Możesz tylko na Zaokrąglenie w dół, są zatwierdzone do wprowadzania. Jest wymagane do przekazywania kontroli wykrywanie systemu operacyjnego pełną treść. Aby zmniejszyć ryzyko nieautoryzowanych danych wprowadzanych lub opuszczających centrum danych bez wiedzy naszych, przedostają można w tylko zatwierdzonego urządzenia się do floor centrum danych. Ponadto monitor kamery wideo przodu i Wstecz każdego serwera w stojaku. Po zakończeniu floor centrum danych, możesz ponownie musi przechodzić przez pełną treść wykrywanie systemu operacyjnego kontroli. Aby wyjść z centrum danych, musisz przechodzić przez skanowanie za pomocą dodatkowych zabezpieczeń.

Firma Microsoft wymaga gości na przekazanie wskaźniki po wyjściu z dowolnej funkcji firmy Microsoft.

## <a name="physical-security-reviews"></a>Przeglądy zabezpieczeń fizycznych
Okresowo przeprowadzamy przeglądów zabezpieczeń fizycznych urządzeń, aby upewnić się, centrach danych poprawnie wymagania zabezpieczeń platformy Azure adresów. Personel dostawcy hostingu w centrum danych są oferowane usługi Azure service management. Pracownicy nie możesz zarejestrować systemy platformy Azure i nie masz fizyczny dostęp do pomieszczenia kolokacji platformy Azure i klatki.

## <a name="data-bearing-devices"></a>Dane żadnego wpływu urządzeń
Firma Microsoft korzysta z najlepszą praktyką procedur i wiping rozwiązanie, które jest [NIST 800-88 zgodne](https://csrc.nist.gov/publications/detail/sp/800-88/archive/2006-09-01). Dla dysków twardych, które nie mogą zostać wyczyszczone używamy proces zniszczenie niszczy go, która renderuje niemożliwe do odzyskiwania informacji. Ten proces zniszczenie może zająć do rozpaść, typu, rozpylać lub spalania. Określamy sposoby usuwania zależnie od typu zasobu. Firma Microsoft zachowuje rekordów zniszczenia.  

## <a name="equipment-disposal"></a>Usuwanie urządzenia
Od systemu zakończenia eksploatacji pracowników operacyjnych firmy Microsoft wykonaj i obsługi danych rygorystyczne procedury usuwania sprzętu, aby mieć pewność, że sprzętu zawierający dane nie będą dostępne dla niezaufane. Używamy podejście bezpiecznego wymazywania dla dysków twardych, które go obsługują. Dla dysków twardych, które nie mogą zostać wyczyszczone używamy proces zniszczenie niszczy dysku, która renderuje niemożliwe do odzyskiwania informacji. Ten proces zniszczenie może zająć do rozpaść, typu, rozpylać lub spalania. Określamy sposoby usuwania zależnie od typu zasobu. Firma Microsoft zachowuje rekordów zniszczenia. Wszystkich usług platformy Azure za pomocą usług zarządzania przechowywania i usuwania zatwierdzonych nośnika.

## <a name="compliance"></a>Zgodność
Firma Microsoft projektowania i Zarządzaj infrastrukturą platformy Azure w celu spełnienia szeroką gamę standardy zgodności międzynarodowych i branżowych, takich jak ISO 27001, HIPAA, FedRAMP, SOC 1 i SOC 2. Stosujemy również ze standardami krajowymi, takimi jak IRAP w Australii, G-Cloud Wielkiej Brytanii i Mtcs w Singapurze. Rygorystyczne audyty innych firm, takich jak to zrobić przez British Standards Institute, potwierdzają rygorystyczne kontrole zabezpieczeń narzucanymi przez te standardy.

Aby uzyskać pełną listę standardy zgodności, które platformy Azure jest zgodna, zobacz [ofertom obsługi zgodności](https://www.microsoft.com/trustcenter/compliance/complianceofferings). 

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się, co firma Microsoft używa do zabezpieczania infrastruktury platformy Azure, zobacz:

- [Dostępność infrastruktury platformy Azure](azure-infrastructure-availability.md)
- [Usługi Azure information składników systemu i granice](azure-infrastructure-components.md)
- [Architektura sieci platformy Azure](azure-infrastructure-network.md)
- [Sieć platformy Azure środowiska produkcyjnego](azure-production-network.md)
- [Funkcje zabezpieczeń w usłudze Azure SQL Database](azure-infrastructure-sql.md)
- [Operacje platformy Azure środowiska produkcyjnego i zarządzanie](azure-infrastructure-operations.md)
- [Monitorowanie infrastruktury platformy Azure](azure-infrastructure-monitoring.md)
- [Integralność infrastruktury platformy Azure](azure-infrastructure-integrity.md)
- [Ochrona danych klientów platformy Azure](azure-protection-of-customer-data.md)

<!--Image references-->
[1]: ./media/azure-physical-security/data-residency-boundary.png
