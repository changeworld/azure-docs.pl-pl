---
title: Ochrona danych klientów na platformie Azure
description: Ten artykuł dotyczy sposobu, w jaki platforma Azure chroni dane klientów.
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
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 630304bec17dd34befab4e5bd9f1cfdfb6505645
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811428"
---
# <a name="azure-customer-data-protection"></a>Ochrona danych klientów platformy Azure   
Domyślnie odmawia się dostępu do danych klientów przez operacje firmy Microsoft i personel pomocy technicznej. Po udzieleniu dostępu do danych klienta wymagana jest zgoda kierownictwa, a następnie dostęp jest starannie zarządzany i rejestrowany. Wymagania dotyczące kontroli dostępu są ustanawiane przez następujące zasady zabezpieczeń platformy Azure:

- Domyślnie nie ma dostępu do danych klienta.
- Brak kont użytkowników lub administratorów na maszynach wirtualnych klientów (VM).
- Przyznaj najmniejsze uprawnienia wymagane do wykonania zadania; żądania dostępu do inspekcji i dziennika.

Pracownicy pomocy technicznej platformy Azure są przypisywani przez firmę Microsoft unikatowymi firmowymi kontami usługi Active Directory. Platforma Azure korzysta z korporacyjnej usługi Active Directory firmy Microsoft, zarządzanej przez firmę Microsoft Information Technology (MSIT), aby kontrolować dostęp do kluczowych systemów informatycznych. Wymagane jest uwierzytelnianie wieloskładnikowe, a dostęp jest przyznawany tylko z bezpiecznych konsol.

Wszystkie próby dostępu są monitorowane i mogą być wyświetlane za pomocą podstawowego zestawu raportów.

## <a name="data-protection"></a>Ochrona danych
Platforma Azure zapewnia klientom silne zabezpieczenia danych, zarówno domyślnie, jak i jako opcje dla klientów.

**Segregacja danych:** Platforma Azure jest usługą wielodostępną, co oznacza, że wiele wdrożeń klientów i maszyn wirtualnych jest przechowywanych na tym samym sprzęcie fizycznym. Platforma Azure używa izolacji logicznej do oddzielenia danych każdego klienta od danych innych osób. Segregacja zapewnia skalę i korzyści ekonomiczne usług wielodostępnych, a jednocześnie rygorystycznie uniemożliwia klientom dostęp do danych innych.

**Ochrona danych w spoczynku:** Klienci są odpowiedzialni za zapewnienie, że dane przechowywane na platformie Azure są szyfrowane zgodnie z ich standardami. Platforma Azure oferuje szeroki zakres funkcji szyfrowania, zapewniając klientom elastyczność wyboru rozwiązania, które najlepiej odpowiada ich potrzebom. Usługa Azure Key Vault ułatwia klientom łatwe utrzymywanie kontroli nad kluczami używanymi przez aplikacje i usługi w chmurze do szyfrowania danych. Szyfrowanie dysków platformy Azure umożliwia klientom szyfrowanie maszyn wirtualnych. Szyfrowanie usługi Azure Storage umożliwia szyfrowanie wszystkich danych, które są umieszczane na koncie magazynu klienta.

**Ochrona danych podczas przesyłania:** klienci mogą włączyć szyfrowanie ruchu między własnymi maszynami wirtualnymi a użytkownikami końcowymi. Platforma Azure chroni dane przesyłane do lub z zewnętrznych składników i danych przesyłanych wewnętrznie, na przykład między dwiema sieciami wirtualnymi. Platforma Azure używa standardowego protokołu TLS (Transport Layer Security) 1.2 lub nowszego z 2048-bitowymi kluczami szyfrowania RSA/SHA256, zgodnie z zaleceniami CESG/NCSC, do szyfrowania komunikacji między:

- Klient i chmura.
- Wewnętrznie między systemami platformy Azure i centrami danych.

**Szyfrowanie**: Szyfrowanie danych w magazynie i podczas przesyłania może być wdrażane przez klientów jako najlepszą praktykę zapewniającą poufność i integralność danych. Klienci mogą skonfigurować swoje usługi w chmurze platformy Azure do używania protokołu TLS do ochrony komunikacji z Internetu, a nawet między maszynami wirtualnymi hostowanymi na platformie Azure.

**Nadmiarowość danych:** firma Microsoft pomaga zapewnić ochronę danych w przypadku cyberataku lub fizycznego uszkodzenia centrum danych. Klienci mogą zdecydować się na:

- Magazyn w kraju/w regionie ze względów zgodności lub opóźnienia.
- Przechowywanie poza krajem/poza regionem w celu zapewnienia bezpieczeństwa lub odzyskiwania po awarii.

Dane mogą być replikowane w wybranym obszarze geograficznym w celu zapewnienia nadmiarowości, ale nie mogą być przesyłane poza nim. Klienci mają wiele opcji replikowania danych, w tym liczbę kopii oraz liczbę i lokalizację centrów danych replikacji.

Podczas tworzenia konta magazynu wybierz jedną z następujących opcji replikacji:

- **Magazyn nadmiarowy lokalnie (LRS)**: Magazyn nadmiarowy lokalnie przechowuje trzy kopie danych. Magazyn LRS jest replikowany trzy razy w jednym obiekcie w pojedynczym regionie. LRS chroni dane przed normalnymi awariami sprzętu, ale nie przed awarią pojedynczego obiektu.
- **Magazyn strefowy (ZRS)**: Magazyn strefowy zachowuje trzy kopie danych. ZRS jest replikowany trzy razy w dwóch do trzech obiektach, aby zapewnić większą trwałość niż LRS. Replikacja odbywa się w jednym regionie lub w dwóch regionach. Usługa ZRS pomaga zapewnić trwałość danych w jednym regionie.
- **Magazyn geograficznie nadmiarowy (GRS)**: Magazyn geograficznie nadmiarowy jest domyślnie włączony dla konta magazynu podczas jego tworzenia. Magazyn GRS przechowuje sześć kopii danych. W grs dane są replikowane trzy razy w regionie podstawowym. Dane są również replikowane trzy razy w regionie pomocniczym setki mil od regionu podstawowego, zapewniając najwyższy poziom trwałości. W przypadku awarii w regionie podstawowym usługi Azure Storage w wyniku fail over do regionu pomocniczego. GRS pomaga zapewnić, że dane są trwałe w dwóch oddzielnych regionach.

**Niszczenie danych:** Gdy klienci usuwają dane lub pozostawiają platformę Azure, firma Microsoft przestrzega ścisłych standardów zastępowania zasobów magazynu przed ich ponownym użyciem, a także fizycznego niszczenia wycofanego sprzętu. Firma Microsoft wykonuje całkowite usunięcie danych na żądanie klienta i po rozwiązaniu umowy.

## <a name="customer-data-ownership"></a>Własność danych klienta
Firma Microsoft nie sprawdza, nie zatwierdza ani nie monitoruje aplikacji wdrażanych przez klientów na platformie Azure. Ponadto firma Microsoft nie wie, jakiego rodzaju dane klienci wybierają do przechowywania na platformie Azure. Firma Microsoft nie rości sobie prawa własności do danych dotyczących informacji o klientach wprowadzonych na platformę Azure.

## <a name="records-management"></a>Zarządzanie rekordami
Platforma Azure ustanowiła wewnętrzne wymagania dotyczące przechowywania rekordów dla danych zaplecza. Klienci są odpowiedzialni za identyfikowanie własnych wymagań dotyczących przechowywania rekordów. W przypadku rekordów przechowywanych na platformie Azure klienci są odpowiedzialni za wyodrębnianie swoich danych i przechowywanie ich zawartości poza platformą Azure przez okres przechowywania określony przez klienta.

Platforma Azure umożliwia klientom eksportowanie danych i raportów inspekcji z produktu. Eksporty są zapisywane lokalnie, aby zachować informacje dla okresu przechowywania zdefiniowanego przez klienta.

## <a name="electronic-discovery-e-discovery"></a>Elektroniczne odnajdowanie (e-discovery)
Klienci platformy Azure są odpowiedzialni za spełnianie wymagań dotyczących e-odnajdowania podczas korzystania z usług platformy Azure. Jeśli klienci platformy Azure muszą zachować swoje dane klientów, mogą eksportować i zapisywać dane lokalnie. Ponadto klienci mogą zażądać eksportu swoich danych z działu obsługi klienta platformy Azure. Oprócz umożliwienia klientom eksportowania danych, platforma Azure przeprowadza szczegółowe rejestrowanie i monitorowanie wewnętrznie.

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o tym, co firma Microsoft robi, aby zabezpieczyć infrastrukturę platformy Azure, zobacz:

- [Obiekty platformy Azure, pomieszczenia i zabezpieczenia fizyczne](physical-security.md)
- [Dostępność infrastruktury platformy Azure](infrastructure-availability.md)
- [Składniki i granice systemu informacyjnego platformy Azure](infrastructure-components.md)
- [Architektura sieci platformy Azure](infrastructure-network.md)
- [Sieć produkcyjna platformy Azure](production-network.md)
- [Funkcje zabezpieczeń usługi Azure SQL Database](infrastructure-sql.md)
- [Operacje produkcyjne i zarządzanie platformą Azure](infrastructure-operations.md)
- [Monitorowanie infrastruktury platformy Azure](infrastructure-monitoring.md)
- [Integralność infrastruktury platformy Azure](infrastructure-integrity.md)
