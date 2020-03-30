---
title: Rozwiązanie Azure VMware firmy CloudSimple — usługi Security for CloudSimple
description: Zawiera opis modeli współużytkowej odpowiedzialności za bezpieczeństwo usług CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 1a33e20ec540a05885eb13a3828d28ffc9923fff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024997"
---
# <a name="cloudsimple-security-overview"></a>Omówienie zabezpieczeń CloudSimple

Ten artykuł zawiera omówienie sposobu implementacji zabezpieczeń w usłudze Azure VMware Solution przez usługę CloudSimple, infrastrukturę i centrum danych. Dowiesz się więcej o ochronie i bezpieczeństwie danych, bezpieczeństwie sieci oraz o sposobie zarządzania lukami w zabezpieczeniach i poprawkami.

## <a name="shared-responsibility"></a>Wspólna odpowiedzialność

Rozwiązanie Azure VMware firmy CloudSimple używa modelu współużytkowania zabezpieczeń. Zaufane zabezpieczenia w chmurze są osiągane dzięki wspólnym obowiązkom klientów i firmy Microsoft jako dostawcy usług. Ta macierz odpowiedzialności zapewnia większe bezpieczeństwo i eliminuje pojedyncze punkty awarii.

## <a name="azure-infrastructure"></a>Infrastruktura platformy Azure

Zagadnienia dotyczące zabezpieczeń infrastruktury platformy Azure obejmują centra danych i lokalizację sprzętu.

### <a name="datacenter-security"></a>Bezpieczeństwo centrów danych

Firma Microsoft ma cały dział poświęcony projektowaniu, tworzeniu i obsłudze obiektów fizycznych obsługujących platformę Azure. Ten zespół jest inwestowany w utrzymanie najnowocześniejszych zabezpieczeń fizycznych. Aby uzyskać szczegółowe informacje na temat zabezpieczeń fizycznych, zobacz [obiekty platformy Azure, pomieszczenia i zabezpieczenia fizyczne.](../security/azure-physical-security.md)

### <a name="equipment-location"></a>Lokalizacja sprzętu

Nieostąpienie sprzętu sprzętowego, który uruchamia chmury prywatne jest hostowany w lokalizacjach centrum danych platformy Azure.  Klatki, w których znajduje się ten sprzęt, wymagają uwierzytelniania dwuskładnikowego opartego na biometrycznych, aby uzyskać dostęp.

## <a name="dedicated-hardware"></a>Dedykowany sprzęt

W ramach usługi CloudSimple wszyscy klienci CloudSimple otrzymują dedykowane hosty bez systemu operacyjnego z lokalnymi dyskami dołączonymi, które są fizycznie odizolowane od innego sprzętu dzierżawy. Hypervisor ESXi z vSAN działa na każdym węźle. Węzły są zarządzane za pośrednictwem klienta dedykowane VMware vCenter i NSX. Nieudzielenie sprzętu między dzierżawcami zapewnia dodatkową warstwę izolacji i ochrony zabezpieczeń.

## <a name="data-security"></a>Bezpieczeństwo danych

Klienci zachowują kontrolę i własność swoich danych. Zarządzanie danymi klienta jest obowiązkiem klienta.

### <a name="data-protection-for-data-at-rest-and-data-in-motion-within-internal-networks"></a>Ochrona danych w spoczynku i danych w ruchu w sieciach wewnętrznych

W przypadku danych w stanie spoczynku w środowisku private cloud można użyć szyfrowania vSAN. Szyfrowanie vSAN współpracuje z serwerami zarządzania kluczami zewnętrznymi (KMS) z certyfikatem VMware we własnej sieci wirtualnej lub lokalnie.  Samodzielnie sterujesz kluczami szyfrowania danych. W przypadku danych w ruchu w chmurze prywatnej vSphere obsługuje szyfrowanie danych przez sieć dla całego ruchu vmkernel (w tym ruchu vMotion).

### <a name="data-protection-for-data-that-is-required-to-move-through-public-networks"></a>Ochrona danych w przypadku danych, które są wymagane do przemieszczania się w sieciach publicznych

Aby chronić dane, które poruszają się za pośrednictwem sieci publicznych, można utworzyć tunele sieci VPN IPsec i SSL dla chmur prywatnych. Obsługiwane są typowe metody szyfrowania, w tym 128-bajtowe i 256-bajtowe AES. Przesyłane dane (w tym uwierzytelnianie, dostęp administracyjny i dane klienta) są szyfrowane za pomocą standardowych mechanizmów szyfrowania (SSH, TLS 1.2 i Secure RDP). Komunikacja, która transportuje poufne informacje, korzysta ze standardowych mechanizmów szyfrowania.

### <a name="secure-disposal"></a>Bezpieczna utylizacja

Jeśli usługa CloudSimple wygaśnie lub zostanie zakończona, użytkownik jest odpowiedzialny za usunięcie lub usunięcie danych. CloudSimple będzie współpracować z Tobą w celu usunięcia lub zwrotu wszystkich danych klienta zgodnie z umową z klientem, z wyjątkiem zakresu, w jakim CloudSimple jest wymagane przez obowiązujące prawo do przechowywania niektórych lub wszystkich danych osobowych. Jeśli jest to konieczne do zachowania jakichkolwiek danych osobowych, CloudSimple zarchiwizuje dane i wdroży rozsądne środki, aby zapobiec dalszemu przetwarzaniu danych klientów.

### <a name="data-location"></a>Lokalizacja danych

Podczas konfigurowania chmury prywatne, należy wybrać region platformy Azure, w którym zostaną wdrożone. Dane maszyny wirtualnej VMware nie są przenoszone z tego fizycznego centrum danych, chyba że wykonasz migrację danych lub tworzenie kopii zapasowej danych poza nim. Można również hostować obciążenia i przechowywać dane w wielu regionach platformy Azure, jeśli jest to odpowiednie dla Twoich potrzeb.

Dane klienta, który jest rezydentem w private cloud węzłów hiperkonwergentnych nie przechodzi przez lokalizacje bez jawnej akcji administratora dzierżawy. Twoim obowiązkiem jest wdrożenie obciążeń w sposób o wysokiej dostępności.

### <a name="data-backups"></a>Kopie zapasowe danych

CloudSimple nie archiwizuje kopii zapasowych ani nie archiwizuje danych klientów. CloudSimple wykonuje okresowe tworzenie kopii zapasowych danych vCenter i NSX w celu zapewnienia wysokiej dostępności serwerów zarządzania. Przed tworzeniem kopii zapasowej wszystkie dane są szyfrowane w źródle vCenter przy użyciu interfejsów API VMware. Zaszyfrowane dane są transportowane i przechowywane w obiekcie blob platformy Azure. Klucze szyfrowania kopii zapasowych są przechowywane w wysoce bezpiecznym magazynie zarządzanym CloudSimple uruchomionym w sieci wirtualnej CloudSimple na platformie Azure.

## <a name="network-security"></a>Bezpieczeństwo sieci

Rozwiązanie CloudSimple opiera się na warstwach zabezpieczeń sieci.

### <a name="azure-edge-security"></a>Zabezpieczenia brzegowe platformy Azure

Usługi CloudSimple są tworzone na podstawie zabezpieczeń sieci podstawowej zapewnianych przez platformę Azure. Platforma Azure stosuje techniki obrony w głębi do wykrywania i terminowej odpowiedzi na ataki sieciowe skojarzone z nietypowymi wzorcami ruchu przychodzącego lub wychodzącego i rozproszonymi atakami typu "odmowa usługi" (DDoS). Ta kontrola zabezpieczeń ma zastosowanie do środowisk private cloud i oprogramowania płaszczyzny sterowania opracowanego przez CloudSimple.

### <a name="segmentation"></a>Segmentacja

Usługa CloudSimple ma logicznie oddzielne sieci warstwy 2, które ograniczają dostęp do własnych sieci prywatnych w środowisku chmury prywatnej. Zaporę można dodatkowo chronić sieci private cloud. Portal CloudSimple umożliwia definiowanie reguł kontroli ruchu sieciowego EW i NS dla całego ruchu sieciowego, w tym ruchu wewnątrz chmury prywatnej, ruchu między chmurą prywatną, ogólnego ruchu do Internetu i ruchu sieciowego lokalnego za pośrednictwem sieci VPN IPsec lub Połączenie usługi ExpressRoute.

## <a name="vulnerability-and-patch-management"></a>Zarządzanie lukami w zabezpieczeniach i poprawkami

CloudSimple jest odpowiedzialny za okresowe instalowanie poprawek zabezpieczeń zarządzanego oprogramowania VMware (ESXi, vCenter i NSX).

## <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem

Klienci mogą uwierzytelniać się na swoim koncie platformy Azure (w usłudze Azure AD) przy użyciu uwierzytelniania wieloskładnikowego lub jednokrotnego zgodnie z preferencjami. W witrynie Azure portal można uruchomić cloudsimple portalu bez ponownego wprowadzania poświadczeń.

CloudSimple obsługuje opcjonalną konfigurację źródła tożsamości dla centrum vCenter private cloud. Można użyć [lokalnego źródła tożsamości,](set-vcenter-identity.md)nowego źródła tożsamości dla chmury prywatnej lub [usługi Azure AD.](azure-ad.md)

Domyślnie klienci otrzymują uprawnienia, które są niezbędne do codziennych operacji vCenter w chmurze prywatnej. Ten poziom uprawnień nie obejmuje dostępu administracyjnego do vCenter. Jeśli dostęp administracyjny jest tymczasowo wymagany, można [eskalować swoje uprawnienia](escalate-private-cloud-privileges.md) przez ograniczony okres podczas wykonywania zadań administracyjnych.
