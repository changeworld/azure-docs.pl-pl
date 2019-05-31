---
title: Zabezpieczenia usług CloudSimple
description: W tym artykule opisano modele odpowiedzialności zabezpieczeń usług CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 4/27/19
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f5f3fe32e03a9a2bb0186854a83917f8918c6647
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/29/2019
ms.locfileid: "66358126"
---
# <a name="cloudsimple-security-overview"></a>Omówienie zabezpieczeń CloudSimple

Ten artykuł zawiera omówienie, jak zostały zaimplementowane zabezpieczenia CloudSimple usług, infrastruktury i centrów danych.  Poznasz informacje dotyczące ochrony danych i zabezpieczenia, zabezpieczenia sieci i sposobu zarządzania luk w zabezpieczeniach i poprawki.

## <a name="shared-responsibility"></a>Wspólna odpowiedzialność

Rozwiązanie programu VMware na platformie Azure przez CloudSimple przy użyciu modelu odpowiedzialności zabezpieczeń. Zaufany bezpieczeństwa w chmurze odbywa się za pośrednictwem udostępnionego obowiązki klientami a firmą Microsoft jako dostawcy usług. Ta macierz odpowiedzialność zapewnia wyższy poziom zabezpieczeń i eliminuje pojedynczych punktów awarii. 

## <a name="azure-infrastructure"></a>Infrastruktura platformy Azure 

Zagadnienia dotyczące zabezpieczeń infrastruktury platformy Azure obejmują lokalizacji centrów danych i urządzeń. 

### <a name="datacenter-security"></a>Bezpieczeństwo centrów danych 

Firma Microsoft ma całego działu przeznaczone na projektowanie, tworzenie i obsługa urządzenia fizyczne obsługi platformy Azure. Ten zespół jest inwestowanych w zachowaniu bezpieczeństwa fizycznego stanu grafiki. Szczegółowe informacje na temat zabezpieczeń fizycznych, [obiektów platformy Azure, lokalnie i zabezpieczenia fizyczne](https://docs.microsoft.com/azure/security/azure-physical-security).

### <a name="equipment-location"></a>Lokalizacja urządzenia

Urządzeń bez sprzętu bez systemu operacyjnego, który działa z Chmurami prywatnymi znajduje się w lokalizacji centrum danych platformy Azure.  Klatkach, w przypadku tego urządzenia wymaga uwierzytelniania biometrycznego na podstawie two-Factor Authentication do uzyskania dostępu.

## <a name="dedicated-hardware"></a>Dedykowany sprzęt

W ramach usługi CloudSimple wszyscy klienci CloudSimple Uzyskaj dedykowanego systemu od zera hostów od zera za pomocą dołączone dyski lokalne, które są fizycznie odizolowane od innego sprzętu dzierżawy. Funkcji hypervisor hostów ESXi z sieć vsan, która działa w każdym węźle. Węzły są zarządzane przez klienta w wersji dedykowanej VMware vCenter i NSX. Nie współużytkuje sprzętu między dzierżawami zapewnia dodatkową warstwę izolacji i zabezpieczeń, ochrony.

## <a name="data-security"></a>Bezpieczeństwo danych

Klienci nadal kontrolki i ich danych. Kształtowanie danych dane klienta odpowiada klient.

### <a name="data-protection-for-data-at-rest-and-data-in-motion-within-internal-networks"></a>Ochrona danych dla danych magazynowanych i danych w ruchu w sieci wewnętrznej

W przypadku danych magazynowanych w środowisku chmury prywatnej można użyć szyfrowania sieci vSAN. szyfrowanie sieć vsan, która współpracuje z certyfikowane serwery zewnętrzne zarządzania kluczami (KMS) programu VMware na własnych sieci wirtualnej lub w środowisku lokalnym.  Możesz kontrolować klucze szyfrowania danych samodzielnie. W przypadku danych w ruchu w ramach chmury prywatnej vSphere obsługuje szyfrowanie danych przez sieć dla całego ruchu vmkernel (w tym ruch narzędzia vMotion).

### <a name="data-protection-for-data-that-is-required-to-move-through-public-networks"></a>Ochrona danych dla danych, które jest wymagane do przechodzenia między sieciach publicznych

Aby chronić dane, który przechodzi przez sieci publicznych, można utworzyć protokołu IPsec i SSL sieci VPN tuneli usługi chmur prywatnych. Obsługiwane są typowe metody szyfrowania, w tym AES 128-bajtowych i 256 bajtów. Dane przesyłane (w tym uwierzytelnianie, dostępu administracyjnego i danych klientów) jest szyfrowana za pomocą mechanizmów szyfrowania standardowy (protokół SSH, protokołu TLS 1.2 i bezpiecznego protokołu RDP). Mechanizmy szyfrowania standardowy korzysta z komunikacji, który służy do transportu poufne informacje.

### <a name="secure-disposal"></a>Bezpieczne usuwanie 

Jeśli usługa CloudSimple wygaśnie lub zostanie zakończony, ponosisz odpowiedzialność za usunięcie lub usuwania danych. CloudSimple będzie współpracować z Tobą w celu usunięcia lub zwrócić wszystkie dane klienta, zgodnie z postanowieniami umowy klienta, z wyjątkiem w zakresie CloudSimple jest wymagane przez obowiązujące prawo, aby zachować niektórych lub wszystkich danych osobowych. Jeśli to konieczne zachować wszystkie dane osobowe, CloudSimple archiwizowanie danych i zaimplementować kroków, aby uniemożliwić dalsze przetwarzanie danych klienta.

### <a name="data-location"></a>Lokalizacja danych

Podczas konfigurowania usługi chmury prywatnej, możesz wybrać region platformy Azure, w którym zostanie wdrożony. Danych maszyny wirtualnej VMware nie jest przenoszony z tym fizycznych centrum danych, chyba że będzie wykonywać migrację danych lub kopia zapasowa danych poza siedzibą firmy. Można również hostowania obciążeń i przechowywania danych w wielu regionach platformy Azure, jeśli jest to odpowiednie dla Twoich potrzeb.

Dane klienta, który jest w chmurze prywatnej hiperkonwergentnych węzłów nie przechodzić lokalizacji bez jawnej akcji administratora dzierżawy. Jest odpowiedzialny za wdrożenie obciążenia w sposób wysoko dostępny.

### <a name="data-backups"></a>Tworzenie kopii zapasowych danych
CloudSimple nie jest tworzenie kopii zapasowych i archiwizowanie danych klienta. CloudSimple zapasową okresowe vCenter i danych NSX zapewnić wysoką dostępność serwerów zarządzania. Przed wykonaniem kopii zapasowej wszystkie dane są szyfrowane, gdy źródło vCenter za pomocą interfejsów API programu VMware. Zaszyfrowane dane są transportowane i przechowywane w usłudze Azure blob. Klucze szyfrowania na potrzeby tworzenia kopii zapasowych są przechowywane w bardzo bezpiecznego magazynu zarządzanego CloudSimple działać w sieci wirtualnej CloudSimple na platformie Azure.

## <a name="network-security"></a>Bezpieczeństwo sieci

Rozwiązanie CloudSimple zależy od warstwy zabezpieczeń sieci.

### <a name="azure-edge-security"></a>Usługa Azure edge security

Usługi CloudSimple są tworzone na podstawie zabezpieczeń sieci podstawowej, udostępnianych przez platformę Azure. Azure stosuje ochronę w głębi techniki wykrywania i czas odpowiedzi na ataki sieciowe skojarzone z nietypowych transferu danych przychodzących lub wzorców ruchu wychodzącego i rozproszonej typu "odmowa usługi" (DDoS) ataków. Ten formant zabezpieczeń ma zastosowanie do środowiska chmury prywatnej i oprogramowanie płaszczyzna kontroli, opracowane przez CloudSimple.

### <a name="segmentation"></a>Segmentacja

Usługa CloudSimple ma logicznie w oddzielnych sieciach warstwy 2, które ograniczają dostęp do prywatnej sieci w Twoim środowisku chmury prywatnej. Można dodatkowo zabezpieczyć sieci chmury prywatnej za pomocą zapory. CloudSimple Portal umożliwia zdefiniuj Folder i NS reguły ruchu sieciowego formantów dla całego ruchu sieciowego, w tym wewnątrz ruchu w chmurze prywatnej, ruch między prywatnej chmury, ogólne ruch do Internetu, a ruch sieciowy do środowiska lokalnego za pośrednictwem sieci VPN IPsec lub Połączenie usługi ExpressRoute.

## <a name="vulnerability-and-patch-management"></a>Zarządzanie poprawkami i luk w zabezpieczeniach 

CloudSimple jest odpowiedzialny za poprawek zabezpieczeń okresowe zarządzanego oprogramowania VMware (ESXi, vCenter i NSX).

## <a name="identity-and-access-management"></a>Tożsamość i zarządzanie dostępem

Klienci mogą uwierzytelniać się do swojego konta platformy Azure (w usłudze Azure AD) przy użyciu usługi Multi-Factor authentication lub logowanie Jednokrotne jako preferowane. W witrynie Azure portal możesz uruchomić CloudSimple Portal bez ponownego wprowadzania poświadczeń.

CloudSimple obsługuje opcjonalna konfiguracja źródła tożsamości dla serwera vCenter chmury prywatnej. Możesz użyć [źródła tożsamości lokalnych](https://docs.azure.cloudsimple.com/set-vcenter-identity), nowe źródło tożsamości w chmurze prywatnej lub [usługi Azure AD](https://docs.azure.cloudsimple.com/azure-ad).

Domyślnie klienci otrzymują uprawnienia, które są niezbędne do codziennych operacji programu vCenter w ramach chmury prywatnej. Ten poziom uprawnień nie uwzględnia dostęp administracyjny do vCenter. Jeśli dostęp administracyjny tymczasowo wymagane jest, możesz to zrobić [podwyższania poziomu uprawnień użytkownika,](https://docs.azure.cloudsimple.com/escalate-private-cloud-privileges) przez ograniczony okres, podczas wykonywania zadań administracyjnych.

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się, jak [utworzyć usługę CloudSimple na platformie Azure](quickstart-create-cloudsimple-service.md)
* Dowiedz się, jak [utworzyć chmurę prywatną](https://docs.azure.cloudsimple.com/create-private-cloud/)
* Dowiedz się, jak [konfigurowania środowiska chmury prywatnej](quickstart-create-private-cloud.md)
