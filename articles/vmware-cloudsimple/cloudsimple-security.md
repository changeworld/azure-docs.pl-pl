---
title: Azure VMware Solutions (Automatyczna synchronizacja) — zabezpieczenia dla usług automatycznej synchronizacji
description: Opisuje współużytkowane modele odpowiedzialności dla bezpieczeństwa usług automatycznej synchronizacji
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d2d45f827d4165175a4a5429f0b9393a55e2ff1e
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024997"
---
# <a name="avs-security-overview"></a>Omówienie funkcji automatycznej aktualizacji zabezpieczeń

Ten artykuł zawiera omówienie sposobu implementacji zabezpieczeń w rozwiązaniu VMware platformy Azure przez zaautomatyczna synchronizacja wersji usługi, infrastruktury i centrum danych. Informacje o ochronie danych i zabezpieczeniach, zabezpieczeniach sieci oraz sposobie zarządzania lukami i poprawkami.

## <a name="shared-responsibility"></a>Wspólna odpowiedzialność

Rozwiązanie VMware platformy Azure przez narzędzie do automatycznej synchronizacji używa wspólnego modelu odpowiedzialności do zabezpieczeń. Zaufane zabezpieczenia w chmurze są realizowane za pomocą wspólnych obowiązków klientów i firmy Microsoft jako dostawcy usług. Ta macierz odpowiedzialności zapewnia wyższy poziom bezpieczeństwa i eliminuje pojedyncze punkty awarii.

## <a name="azure-infrastructure"></a>Infrastruktura platformy Azure

Zagadnienia dotyczące zabezpieczeń infrastruktury platformy Azure obejmują centra danych i lokalizację sprzętu.

### <a name="datacenter-security"></a>Zabezpieczenia centrum danych

Firma Microsoft ma cały dział poświęcony na projektowanie, kompilowanie i obsługiwanie fizycznych obiektów, które obsługują platformę Azure. Ten zespół jest inwestowany z zachowaniem bezpieczeństwa fizycznego. Aby uzyskać szczegółowe informacje na temat zabezpieczeń fizycznych, zobacz [usługi platformy Azure, lokalne i zabezpieczenia fizyczne](../security/azure-physical-security.md).

### <a name="equipment-location"></a>Lokalizacja sprzętu

Sprzęt komputerowy bez systemu operacyjnego, który uruchamia chmurę prywatną automatycznej synchronizacji, jest hostowany w lokalizacji centrum danych platformy Azure. Klatki, w których znajduje się ten sprzęt, wymagają uwierzytelniania dwuskładnikowego opartego na bioczynnikach biometrycznych w celu uzyskania dostępu.

## <a name="dedicated-hardware"></a>Dedykowany sprzęt

W ramach usługi automatycznej synchronizacji wszyscy klienci wersji zapoznają się z dedykowanymi hostami bez systemu operacyjnego i lokalnymi dołączonymi dyskami, które są fizycznie odizolowane od innego sprzętu dzierżawców. Funkcja hypervisor ESXi z sieci vSAN jest uruchamiana na każdym węźle. Węzły są zarządzane za pomocą dedykowanych przez klienta rozwiązań VMware vCenter i NSX. Udostępnianie sprzętu między dzierżawcami zapewnia dodatkową warstwę izolacji i ochrony zabezpieczeń.

## <a name="data-security"></a>Bezpieczeństwo danych

Klienci zachowują kontrolę i prawo własności do swoich danych. Stewardship danych klienta jest odpowiedzialny za klienta.

### <a name="data-protection-for-data-at-rest-and-data-in-motion-within-internal-networks"></a>Ochrona danych w czasie spoczynku i danych w ruchu w sieciach wewnętrznych

W przypadku danych przechowywanych w środowisku chmury prywatnej do automatycznej synchronizacji można użyć szyfrowania sieci vSAN. szyfrowanie sieci vSAN współpracuje z zewnętrznymi serwerami zarządzania kluczami (KMS) oprogramowania VMware w własnej sieci wirtualnej lub lokalnie. Klucze szyfrowania danych są kontrolowane samodzielnie. W przypadku danych w ramach chmury prywatnej do automatycznej synchronizacji usługa vSphere obsługuje szyfrowanie danych za pośrednictwem sieci dla całego ruchu VMkernel (łącznie z ruchem vMotion).

### <a name="data-protection-for-data-that-is-required-to-move-through-public-networks"></a>Ochrona danych danych wymaganych do poruszania się w sieciach publicznych

Aby chronić dane przenoszone za pośrednictwem sieci publicznych, można utworzyć tunele sieci VPN protokołu IPsec i protokołu SSL dla chmur prywatnych. Obsługiwane są typowe metody szyfrowania, w tym 128-bajtowe i 256-Byte AES. Dane przesyłane (łącznie z uwierzytelnianiem, dostępem administracyjnym i danymi klienta) są szyfrowane przy użyciu standardowych mechanizmów szyfrowania (SSH, TLS 1,2 i Secure RDP). Komunikacja, która transportuje poufne informacje korzysta z standardowych mechanizmów szyfrowania.

### <a name="secure-disposal"></a>Bezpieczne usuwanie

Jeśli usługa automatycznej synchronizacji wygaśnie lub zostanie zakończona, użytkownik jest odpowiedzialny za usunięcie lub usunięcie danych. Automatyczna synchronizacja będzie współpracować z licencjobiorcą w celu usunięcia lub zwrócenia wszystkich danych klienta zgodnie z umową klienta, z wyjątkiem sytuacji, w której w stopniu automatyczna synchronizacja jest wymagana przez prawo, aby zachować niektóre lub wszystkie dane osobowe. Jeśli to konieczne, aby zachować dane osobowe, funkcja automatycznej synchronizacji będzie archiwizować dane i wdrożyć odpowiednie środki, aby zapobiec dalszemu przetwarzaniu danych klienta.

### <a name="data-location"></a>Lokalizacja danych

Podczas konfigurowania chmur prywatnych w ramach automatycznej synchronizacji należy wybrać region platformy Azure, w którym zostaną wdrożone. Dane maszyny wirtualnej VMware nie są przenoszone z tego fizycznego centrum danych, chyba że wykonywana jest migracja danych ani kopia zapasowa danych poza siedzibą firmy. Możesz również hostować obciążenia i przechowywać dane w wielu regionach świadczenia usługi Azure, jeśli są odpowiednie dla Twoich potrzeb.

Dane klienta, które są rezydentne w ramach automatycznej synchronizacji chmur prywatnych w chmurze, nie przechodzą na lokalizacje bez jawnej akcji administratora dzierżawy. Jest on odpowiedzialny za wdrożenie obciążeń w wysokiej dostępności.

### <a name="data-backups"></a>Kopie zapasowe danych

Automatyczna synchronizacja nie tworzy kopii zapasowych ani nie archiwizuje danych klientów. Automatyczna synchronizacja danych programu vCenter i NSX umożliwia tworzenie kopii zapasowych w celu zapewnienia wysokiej dostępności serwerów zarządzania. Przed utworzeniem kopii zapasowej wszystkie dane są szyfrowane w źródle programu vCenter przy użyciu interfejsów API VMware. Zaszyfrowane dane są transportowane i przechowywane w usłudze Azure Blob. Klucze szyfrowania kopii zapasowych są przechowywane w wysoce zabezpieczonym magazynie usługi automatycznej synchronizacji działającym w sieci wirtualnej automatycznej synchronizacji na platformie Azure.

## <a name="network-security"></a>Bezpieczeństwo sieci

Rozwiązanie do automatycznej synchronizacji opiera się na warstwach zabezpieczeń sieci.

### <a name="azure-edge-security"></a>Zabezpieczenia usługi Azure Edge

Usługi automatycznej synchronizacji są oparte na podstawowych zabezpieczeniach sieci zapewnianych przez platformę Azure. Na platformie Azure są stosowane kompleksowe techniki wykrywania i reagowania w odpowiedzi na ataki sieciowe związane z niestandardowymi wzorcami ruchu przychodzącego lub wychodzącego oraz rozproszone ataki typu "odmowa usługi" (DDoS). Ta kontrola zabezpieczeń ma zastosowanie do automatycznej synchronizacji środowisk chmur prywatnych oraz oprogramowania płaszczyzny kontroli opracowanego w wyniku automatycznej synchronizacji.

### <a name="segmentation"></a>Segmentacji

Usługa automatycznej synchronizacji ma logicznie oddzielić sieci warstwy 2, które ograniczają dostęp do własnych sieci prywatnych w środowisku chmury prywatnej. W celu zapewnienia dalszej ochrony sieci chmur prywatnych w chmurze za pomocą zapory. Portal automatycznej synchronizacji umożliwia definiowanie reguł dla ruchu sieciowego typu wa i NS dla całego ruchu sieciowego, w tym między innymi automatyczna synchronizacja ruchu w chmurze, ruch w chmurze prywatnej, ogólny ruch do Internetu oraz ruch sieciowy do sieci lokalnej za pośrednictwem protokołu IPsec VPN lub Połączenie ExpressRoute.

## <a name="vulnerability-and-patch-management"></a>Luki w zabezpieczeniach i zarządzaniu poprawkami

Automatyczna synchronizacja jest odpowiedzialna za okresowe poprawianie zabezpieczeń zarządzanego oprogramowania VMware (ESXi, vCenter i NSX).

## <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem

Klienci mogą uwierzytelniać się na koncie platformy Azure (w usłudze Azure AD) przy użyciu uwierzytelniania wieloskładnikowego lub logowania jednokrotnego jako preferowanego. W Azure Portal można uruchomić Portal automatycznej synchronizacji bez ponownego wprowadzania poświadczeń.

Funkcja automatycznej synchronizacji obsługuje opcjonalną konfigurację źródła tożsamości dla programu vCenter w chmurze prywatnej chmury. Możesz użyć [lokalnego źródła tożsamości](set-vcenter-identity.md), nowego źródła tożsamości dla chmury prywatnej automatycznej synchronizacji lub [usługi Azure AD](azure-ad.md).

Domyślnie klienci mają uprawnienia, które są niezbędne do codziennej operacji programu vCenter w chmurze prywatnej automatycznej synchronizacji. Ten poziom uprawnień nie obejmuje dostępu administracyjnego do serwera vCenter. Jeśli dostęp administracyjny jest tymczasowo wymagany, można [eskalować swoje uprawnienia](escalate-private-cloud-privileges.md) przez ograniczony okres czasu podczas wykonywania zadań administracyjnych.
