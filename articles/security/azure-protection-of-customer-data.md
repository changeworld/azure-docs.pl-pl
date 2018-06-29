---
title: Ochrona danych klienta na platformie Azure
description: W tym artykule opisano, jak Azure chroni dane klientów.
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 9a3b00e39f78f65b05b7d730447440d481979539
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37102529"
---
# <a name="protection-of-customer-data-in-azure"></a>Ochrona danych klienta na platformie Azure   
Domyślnie jest odmowa dostępu do danych klienta Microsoft operacji i pomocy technicznej. Po udzieleniu dostęp do danych klientów kierowniczej zatwierdzenia jest wymagana, a następnie dostępu jest dokładnie zarządzane i zarejestrowane. Wymagania dotyczące kontroli dostępu są wyznaczane przez następujące zasady zabezpieczeń firmy Microsoft Azure:

- Brak dostępu do danych klienta, domyślnie
- Nie kont użytkownika lub administratora na klienta maszyn wirtualnych
- Przyznaj najniższych uprawnień wymaganych do ukończenia zadania; Inspekcja i rejestrowanie żądań dostępu

Personel pomocy technicznej Microsoft Azure są przypisywane unikatowe konta firmowe AD przez firmę Microsoft. Microsoft Azure wykorzystuje Microsoft firmowej usługi Active Directory, zarządza MSIT, aby kontrolować dostęp do kluczowych informacji systemów. Wymagane jest uwierzytelnianie wieloskładnikowe i z bezpiecznej konsoli tylko zostanie przyznany dostęp.

Wszystkie próby dostępu są monitorowane i mogą być wyświetlane przy użyciu podstawowego zestawu raportów.

## <a name="data-protection"></a>Ochrona danych
Azure zapewnia klientom z zabezpieczeniami danych silne — domyślnie oraz jako klientów.

**Podział danych** -Azure jest usługą wielodostępne, co oznacza, że wdrożeń i maszyny wirtualne wielu klientów są przechowywane na tym samym sprzęcie fizycznym. Platforma Azure korzysta izolacji logicznej może też oddzielić dane poszczególnych klientów danych z innych osób. Podział zapewnia skalowanie i korzyści ekonomiczne wielodostępnym usług podczas szczegółowa analiza uniemożliwia klientom dostęp do danych siebie nawzajem.

**Ochrona danych na rest** — klienci są odpowiedzialne za zapewnienie, że dane przechowywane na platformie Azure są szyfrowane zgodnie z ich standardów. System Azure oferuje szeroką gamę możliwości szyfrowania, zapewniając elastyczność wyboru rozwiązania, która najlepiej spełnia ich wymagania dotyczące klientów. Usługa Azure Key Vault ułatwia klientom łatwe zachować kontrolę nad kluczy używanych przez usługi i aplikacje w chmurze do szyfrowania danych. Szyfrowanie dysków Azure umożliwia klientom zaszyfrować maszyny wirtualne. Szyfrowanie usługi Magazyn Azure umożliwia szyfrowanie wszystkich danych umieszczane konta magazynu klienta.

**Ochrona danych podczas przesyłania** -klientów można włączyć szyfrowanie dla ruchu między maszynami wirtualnymi i użytkowników końcowych. Azure chroni dane w drodze do lub z składników zewnętrznych i w przesyłania danych wewnętrznie przykład między dwoma sieci wirtualnych. Azure używa standardowych zabezpieczeń TLS (Transport Layer) 1.2 lub powyżej protokołu z kluczy szyfrowania RSA/SHA256 2048-bitowego, jak zalecane przez CESG/NCSC do szyfrowania komunikacji między:

- Klient i chmury
- wewnętrznie między systemami Azure i centrów danych

**Szyfrowanie** — można wdrożyć szyfrowanie danych w magazynie i przesyłanych przez klientów najlepszym rozwiązaniem dla zapewnienia zachowania poufności i integralności danych. Jest proste dla klientów skonfigurować swoje usługi w chmurze Azure do używania protokołu SSL do ochrony komunikacji z Internetem, nawet między ich Azure hostowanych maszyn wirtualnych.

**Nadmiarowość danych** -Microsoft zapewnia dane są chronione, jeśli istnieje cyberattack lub uszkodzeniem fizycznym centrum danych. Klienci mogą wybrać:

- Magazyn w kraju dla zagadnienia dotyczące zgodności lub opóźnienia
- poza Państwa magazynu na potrzeby odzyskiwania zabezpieczeń lub po awarii

Dane mogą być replikowane w ramach wybranego obszaru geograficznego nadmiarowości, ale nie będą przesyłane poza nią. Klienci mają wiele opcji do replikacji danych, w tym liczbę kopii i liczbę i lokalizację replikację w centrach danych.

Podczas tworzenia konta magazynu, musisz wybrać jedną z następujących opcji replikacji:

- Magazyn lokalnie nadmiarowy (LRS). Magazyn lokalnie nadmiarowy przechowuje trzy kopie danych. Magazyn LRS jest replikowany trzy razy w jednym obiekcie w pojedynczym regionie. Magazyn LRS chroni dane przed zwykłymi awariami sprzętu, lecz nie przed awarią pojedynczego obiektu.
- Magazyn strefowo nadmiarowy (ZRS). Magazyn strefowo nadmiarowy przechowuje trzy kopie danych. Magazyn ZRS jest replikowany trzykrotnie w dwóch do trzech lokalizacjach, aby zapewnić większą trwałość niż magazyn LRS. Replikacja odbywa się w jednym lub dwóch regionach. Magazyn ZRS zapewnia, że dane są trwałe w pojedynczym regionie.
- Magazyn geograficznie nadmiarowy (GRS). Magazyn geograficznie nadmiarowy jest domyślnie włączany dla konta magazynu podczas jego tworzenia. Magazyn GRS przechowuje sześć kopii danych. W wypadku magazynu GRS dane są replikowane trzy razy w regionie podstawowym. Dane są także replikowane trzy razy w regionie pomocniczym setki odległości od regionu podstawowego, co zapewnia najwyższy poziom trwałości. Jeśli w regionie podstawowym wystąpi awaria, usługa Azure Storage przejdzie w tryb failover w regionie pomocniczym. Magazyn GRS zapewnia, że dane są trwałe w dwóch oddzielnych regionach.

**Niszczenie danych** — w przypadku klientów usuwania danych lub pozostaw Azure, Microsoft następuje strict standardy zastępowanie przed fizycznego niszczenia wycofany z eksploatacji sprzętu, a także ponownego użycia zasobów magazynu. Microsoft wykonuje pełne usuwania danych na żądanie klienta i po zakończeniu kontraktu.

## <a name="customer-data-ownership"></a>Własność danych klienta
Microsoft nie kontrolują, zatwierdzanie lub monitorowania aplikacji, których klienci wdrażanie na platformie Azure. Ponadto firmy Microsoft nie może określić rodzaj danych klientów wybierz można przechowywać na platformie Azure. Microsoft nie rości własność danych za pośrednictwem klienta wprowadzonej na platformie Azure.

## <a name="records-management"></a>Zarządzanie rekordami
Azure zostało ustanowione wymagania dotyczące przechowywania rekordów wewnętrzny dla danych zaplecza. Klienci są zobowiązani do identyfikacji wymagań przechowywania rekordu. Rekordy przechowywane na platformie Azure klient jest odpowiedzialny za wyodrębniania danych i przechowywanie zawartości poza platformą Azure w danym okresie przechowywania określonych przez klienta.

Platforma Azure udostępnia klienta możliwość eksportowania danych i inspekcji raporty z produktu. Polecenie eksportuje te są zapisywane lokalnie do przechowywania informacji okresu przechowywania zdefiniowany przez klienta przedziale czasu.

## <a name="electronic-discovery-e-discovery"></a>Odnajdywanie elektronicznych (zbieranie elektronicznych materiałów dowodowych)
Azure klienci są zobowiązani do przestrzegania zbieranie elektronicznych materiałów dowodowych wymagań dotyczących ich użycia usług Azure. Jeśli odbiorcy Azure muszą zachować swoje dane klienta, mogą eksportować i zapisać je lokalnie. Ponadto klienci mogą żądać eksportuje dane z działu obsługi klienta usługi Azure. Oprócz umożliwienia klientom wyeksportować swoje dane, Azure prowadzi szeroką gamę rejestrowania i monitorowania wewnętrznie.

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat firmy Microsoft jest zapewnienie infrastruktury platformy Azure, zobacz:

- [Urządzenia platformy Azure, lokalne i zabezpieczenia fizyczne](azure-physical-security.md)
- [Dostępność infrastruktury platformy Azure](azure-infrastructure-availability.md)
- [Składniki systemu Azure informacji i granice](azure-infrastructure-components.md)
- [Architektura sieci platformy Azure](azure-infrastructure-network.md)
- [Sieci Azure środowiska produkcyjnego](azure-production-network.md)
- [Funkcje zabezpieczeń bazy danych SQL Azure firmy Microsoft](azure-infrastructure-sql.md)
- [Operacje Azure środowiska produkcyjnego i zarządzania](azure-infrastructure-operations.md)
- [Monitorowanie infrastruktury platformy Azure](azure-infrastructure-monitoring.md)
- [Integralność infrastruktury platformy Azure](azure-infrastructure-integrity.md)
