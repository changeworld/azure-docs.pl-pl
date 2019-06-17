---
title: Ochrona danych klientów na platformie Azure
description: W tym artykule opisano, jak platforma Azure chroni dane klientów.
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
ms.openlocfilehash: 04163d1fa2a46a2de877702d479f439a5e8711d7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65603133"
---
# <a name="azure-customer-data-protection"></a>Ochrona danych klientów platformy Azure   
Odmowa dostępu do danych klienta przez Microsoft operations i obsługą techniczną domyślnie. Po nadaniu prawa dostępu do danych klienta kierownicze zatwierdzenie jest wymagane, a następnie dostęp jest dokładnie zarządzane i zarejestrowane. Wymagania dotyczące kontroli dostępu są wyznaczane przez następujące zasady zabezpieczeń platformy Azure:

- Brak dostępu do danych klienta, domyślnie.
- Żadnych kont użytkownika lub administratora w przypadku klientów maszyn wirtualnych (VM).
- Przyznawanie minimalnych uprawnień, które są wymagane do ukończenia zadania; Inspekcja i Rejestruj żądania dostępu.

Personel pomocy technicznej platformy Azure są przypisywane unikatowy firmowych kont usługi Active Directory przez firmę Microsoft. Azure opiera się na Microsoft firmowej usługi Active Directory, zarządzanych przez Microsoft technologii informacji (MSIT), aby kontrolować dostęp do kluczowych informacji systemów. Wymagane jest uwierzytelnianie wieloskładnikowe, a dostęp jest udzielany tylko z bezpiecznego konsol.

Wszystkie próby uzyskania dostępu są monitorowane i mogą być wyświetlane za pomocą podstawowego zestawu raportów.

## <a name="data-protection"></a>Ochrona danych
Platforma Azure zapewnia klientom zabezpieczeń typu danych domyślnie oraz jak opcje klienta.

**Podział danych**: Platforma Azure to usługa dla wielu dzierżawców, co oznacza, że wielu klientów wdrożenia i maszyny wirtualne są przechowywane na tym samym sprzęcie fizycznym. Platforma Azure używa izolacji logicznej, do oddzielania danych jednych klientów od danych innych drugich. Podział zapewnia skalowalność i korzyściach ekonomicznych wynikających z wieloma dzierżawami usługi, jednocześnie rygorystycznie pilnując klientom dostęp do danych kogoś innego.

**Ochrona danych w spoczynku**: Klienci są odpowiedzialni za zadbanie szyfrowanie danych przechowywanych na platformie Azure, zgodnie z ich standardy. Platforma Azure oferuje szeroki zakres funkcji szyfrowania, zapewniając klientom swobodę wyboru rozwiązania, które najlepiej odpowiadają ich potrzebom. Usługa Azure Key Vault pomaga klientom łatwe zachować kontrolę nad kluczami, które są używane przez aplikacje w chmurze i usług do szyfrowania danych. Usługa Azure Disk Encryption pozwala klientom szyfrować maszyny wirtualne. Szyfrowanie usługi Azure Storage sprawia, że możliwość szyfrowania wszystkich danych, które jest umieszczana w koncie magazynu klienta.

**Ochrona danych podczas przesyłania**: Klientów można włączyć szyfrowanie ruchu między maszynami wirtualnymi i użytkowników końcowych. Platforma Azure chroni dane przesyłane do lub z zewnętrznych składników i w przesyłania danych wewnętrznie przykład między dwiema sieciami wirtualnymi. Platforma Azure używa standardowych zabezpieczeń TLS (Transport Layer) 1.2 lub nowszej protokołu przy użyciu 2048 bitowych kluczy szyfrowania RSA/SHA256, zgodnie z zaleceniami CESG/NCSC do szyfrowania komunikacji między:

- Klient i chmury.
- Wewnętrznie między systemami Azure i centrami danych.

**Szyfrowanie**: Szyfrowanie danych w magazynie i w drodze można wdrożyć przez klientów, najlepszym rozwiązaniem dla zapewnienia poufności i integralności danych. Jest to prosta do konfigurowania usług w chmurze platformy Azure do używania protokołu SSL do ochrony łączności z Internetem, a nawet między swoich maszyn wirtualnych hostowanych na platformie Azure przez klientów.

**Nadmiarowość danych**: Microsoft pomaga zapewnić, że dane są chronione w przypadku cyberattack lub uszkodzeniem fizycznym centrum danych. Klienci mogą wybrać:

- Magazyn w w kraju zagadnienia dotyczące zgodności lub opóźnienia.
- Magazyn poza z limit z kraju celach recovery zabezpieczeń lub po awarii.

Dane mogą być replikowane w obrębie zaznaczonego obszaru geograficznego w celu zapewnienia nadmiarowości, ale nie mogą być przekazywane poza nim. Klienci mają wiele opcji w celu replikowania danych, takie jak liczba kopii i liczby i lokalizacji centrów danych replikacji.

Podczas tworzenia konta magazynu, wybierz jedną z następujących opcji replikacji:

- **Magazyn lokalnie nadmiarowy (LRS)** : Magazyn lokalnie nadmiarowy przechowuje trzy kopie danych. Magazyn LRS jest replikowany trzy razy w jednym obiekcie w pojedynczym regionie. Magazyn LRS chroni dane przed zwykłymi awariami sprzętu, ale nie przed awarią pojedynczego obiektu.
- **Magazyn strefowo nadmiarowy (ZRS)** : Magazyn strefowo nadmiarowy przechowuje trzy kopie danych. Magazyn ZRS jest replikowany trzy razy w dwóch do trzech lokalizacjach, aby zapewnić większą trwałość niż magazyn LRS. Replikacja odbywa się w jednym regionie lub w dwóch regionach. Magazyn ZRS pomaga upewnić się, że Twoje dane są trwałe w pojedynczym regionie.
- **Magazyn geograficznie nadmiarowy (GRS)** : Magazyn geograficznie nadmiarowy jest domyślnie włączany dla konta magazynu podczas jego tworzenia. Magazyn GRS przechowuje sześć kopii danych. W przypadku magazynu GRS Twoje dane są replikowane trzy razy w regionie podstawowym. Dane są także replikowane trzy razy w regionie pomocniczym oddalonym setki odległości od regionu podstawowego, co zapewnia najwyższy poziom trwałości. Jeśli wystąpi awaria w regionie podstawowym usługi Azure Storage awaryjnie do regionu pomocniczego. GRS zapewnia, że Twoje dane są trwałe w dwóch oddzielnych regionach.

**Zniszczenie danych**: Gdy klienci usuwania danych lub pozostaw platformy Azure, Microsoft stosuje rygorystyczne standardy dla zastępowanie zasobów magazynu przed ich ponownego użycia, jak również fizyczne zniszczenie zlikwidowana sprzętu. Microsoft wykonuje pełną usuwania danych na żądanie klienta, a na zakończenie umowy.

## <a name="customer-data-ownership"></a>Prawa własności do danych klienta
Microsoft nie kontrolują, zatwierdzanie i monitorować aplikacje, które klienci wdrażanie na platformie Azure. Ponadto firmy Microsoft nie zna dokonanego wyboru rodzaju danych klientów do przechowywania na platformie Azure. Microsoft zastrzega sobie prawa własności do danych za pośrednictwem informacje o kliencie, wprowadzony na platformie Azure.

## <a name="records-management"></a>Zarządzanie rekordami
Azure ustanowiła wewnętrznego przechowywania rekordy dotyczące danych zaplecza. Klienci są zobowiązani do identyfikowania obowiązujących ich wymagań przechowywania rekordu. Aby uzyskać rekordy, które są przechowywane na platformie Azure klienci są odpowiedzialni wyodrębnianie swoje dane i zachowywanie ich zawartości spoza platformy Azure na okres przechowywania określony przez klienta.

Platforma Azure umożliwia klientom eksportować dane i raporty z produktu inspekcji. Polecenie eksportuje te są zapisywane lokalnie do przechowywania informacji do przechowywania danych zdefiniowanej przez klienta przedziale czasu.

## <a name="electronic-discovery-e-discovery"></a>Odnajdywanie elektronicznej (e-discovery)
Klienci platformy Azure są zobowiązani do przestrzegania wymagań zbieranie elektronicznych materiałów dowodowych ich użytkowania usług platformy Azure. Jeśli klienci platformy Azure muszą zachować swoje dane klienta, mogą wyeksportować i zapisać je lokalnie. Ponadto klienci mogą poprosić eksportuje dane z działu obsługi klienta systemu Azure. Oprócz umożliwienia klienci mogą eksportować swoje dane, Azure wykonuje rozległe rejestrowania i monitorowania wewnętrznie.

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat firmy Microsoft jest zapewnienie infrastruktury platformy Azure, zobacz:

- [Urządzenia platformy Azure, lokalnie i zabezpieczenia fizyczne](azure-physical-security.md)
- [Dostępność infrastruktury platformy Azure](azure-infrastructure-availability.md)
- [Usługi Azure information składników systemu i granice](azure-infrastructure-components.md)
- [Architektura sieci platformy Azure](azure-infrastructure-network.md)
- [Sieć platformy Azure środowiska produkcyjnego](azure-production-network.md)
- [Funkcje zabezpieczeń w usłudze Azure SQL Database](azure-infrastructure-sql.md)
- [Operacje platformy Azure środowiska produkcyjnego i zarządzanie](azure-infrastructure-operations.md)
- [Monitorowanie infrastruktury platformy Azure](azure-infrastructure-monitoring.md)
- [Integralność infrastruktury platformy Azure](azure-infrastructure-integrity.md)
