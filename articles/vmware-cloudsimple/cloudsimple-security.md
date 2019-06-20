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
ms.openlocfilehash: db093f6aef4f1bdb28a96bf89b3013d359a7b796
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/17/2019
ms.locfileid: "67154110"
---
# <a name="cloudsimple-security-overview"></a>Omówienie zabezpieczeń CloudSimple

Ten artykuł zawiera omówienie, jak zostały zaimplementowane zabezpieczenia rozwiązań VMware Azure CloudSimple usług, infrastruktury i centrum danych. Poznasz informacje dotyczące ochrony danych i zabezpieczeń, zabezpieczeń sieci i sposobu zarządzania luk w zabezpieczeniach i poprawki.

## <a name="shared-responsibility"></a>Wspólna odpowiedzialność

Rozwiązanie programu VMware na platformie Azure przez CloudSimple przy użyciu modelu odpowiedzialności zabezpieczeń. Zaufany bezpieczeństwa w chmurze odbywa się za pośrednictwem udostępnionego obowiązki klientami a firmą Microsoft jako dostawcy usług. Ta macierz odpowiedzialność zapewnia wyższy poziom zabezpieczeń i eliminuje pojedynczych punktów awarii.

## <a name="azure-infrastructure"></a>Infrastruktura platformy Azure 

Zagadnienia dotyczące zabezpieczeń infrastruktury platformy Azure obejmują lokalizacji centrów danych i urządzeń.

### <a name="datacenter-security"></a>Bezpieczeństwo centrów danych 

Firma Microsoft ma całego działu przeznaczone na projektowanie, tworzenie i obsługa urządzenia fizyczne, które obsługują usługi Azure. Ten zespół jest inwestowanych w zachowaniu bezpieczeństwa fizycznego stanu grafiki. Aby uzyskać więcej informacji na temat zabezpieczeń fizycznych, zobacz [obiektów platformy Azure, lokalnie i zabezpieczenia fizyczne](https://docs.microsoft.com/azure/security/azure-physical-security).

### <a name="equipment-location"></a>Lokalizacja urządzenia

Sprzęt sprzętu bez systemu operacyjnego, który uruchamia chmur prywatnych znajduje się w lokalizacji centrum danych platformy Azure. Uwierzytelnianie dwuskładnikowe za pomocą biometrycznych jest wymagany do uzyskania dostępu do klatkach, w którym znajduje się tego urządzenia.

## <a name="dedicated-hardware"></a>Dedykowany sprzęt

W ramach usługi CloudSimple wszyscy klienci CloudSimple Uzyskaj dedykowanych hostów bez systemu operacyjnego za pomocą dołączone dyski lokalne, które są fizycznie odizolowane od innego sprzętu dzierżawy. Funkcji hypervisor hostów ESXi z sieć vsan, która działa w każdym węźle. Węzły są zarządzane za pośrednictwem dedykowanej klienta VMware vCenter i NSX. Nie współużytkuje sprzętu między dzierżawami zapewnia dodatkową warstwę izolacji i zabezpieczeń, ochrony.

## <a name="data-security"></a>Bezpieczeństwo danych

Klienci nadal kontrolki i ich danych. Kształtowanie danych dane klienta odpowiada klient.

### <a name="data-protection-for-data-at-rest-and-data-in-motion-within-internal-networks"></a>Ochrona danych dla danych magazynowanych i danych w ruchu w sieci wewnętrznej

W przypadku danych magazynowanych w środowisku chmury prywatnej można użyć szyfrowania sieci vSAN. szyfrowanie sieć vsan, która współpracuje z serwery VMware, certyfikat zewnętrzny zarządzania kluczami (KMS) na własnych sieci wirtualnej lub w środowisku lokalnym. Możesz kontrolować klucze szyfrowania danych samodzielnie. W przypadku danych w ruchu w ramach chmury prywatnej vSphere obsługuje szyfrowanie danych przez sieć dla całego ruchu VMkernel, w tym ruch vMotion.

### <a name="data-protection-for-data-thats-required-to-move-through-public-networks"></a>Ochrona danych dla danych, które są wymagane do przechodzenia między sieciach publicznych

Aby chronić dane, który przechodzi przez sieci publicznych, można utworzyć protokołu IPsec i SSL sieci VPN tuneli dla chmur prywatnych. Obsługiwane są typowe metody szyfrowania, w tym AES 128-bajtowych i 256 bajtów. Przesyłanych danych, w tym uwierzytelnianie, dostępu administracyjnego i dane klienta, są szyfrowane za pomocą mechanizmów szyfrowania standardowy, takich jak protokół SSH, protokołu TLS 1.2 i bezpiecznego protokołu RDP. Mechanizmy szyfrowania standardowy korzysta z komunikacji, który służy do transportu poufne informacje.

### <a name="secure-disposal"></a>Bezpieczne usuwanie 

Jeśli usługa CloudSimple wygaśnie lub zostanie zakończony, jesteś odpowiedzialny za usunięcie lub usuwania danych. CloudSimple współpracuje z Tobą w celu usunięcia lub zwrócić wszystkie dane klienta, zgodnie z postanowieniami umowy klienta, z wyjątkiem w zakresie CloudSimple jest wymagane przez obowiązujące prawo, aby zachować niektórych lub wszystkich danych osobowych. Jeśli to konieczne zachować wszystkie dane osobowe, CloudSimple archiwów danych i implementuje kroków, aby uniemożliwić dalsze przetwarzanie danych klienta.

### <a name="data-location"></a>Lokalizacja danych

Podczas tworzenia chmur prywatnych, możesz wybrać region platformy Azure, w którym zostały wdrożone. Danych maszyny wirtualnej VMware nie jest przenoszony z tego fizycznego centrum danych, chyba że będzie wykonywać migrację danych lub kopia zapasowa danych poza siedzibą firmy. Możesz również hostować obciążenia i przechowywania danych w wielu regionach platformy Azure, jeśli jest to odpowiednie dla Twoich potrzeb.

Dane klienta, które ma miejsce zamieszkania w węzłach hiperzbieżnego chmury prywatnej nie przechodzić lokalizacji bez jawnej akcji administratora dzierżawy. Jest odpowiedzialny za wdrożenie obciążenia w sposób wysoko dostępny.

### <a name="data-backups"></a>Tworzenie kopii zapasowych danych
CloudSimple nie jest tworzenie kopii zapasowych i archiwizowanie danych klienta. CloudSimple zapasową okresowe vCenter i danych NSX zapewnić wysoką dostępność serwerów zarządzania. Przed wykonaniem kopii zapasowej wszystkie dane są szyfrowane w źródle vCenter za pomocą interfejsów API programu VMware. Zaszyfrowane dane są transportowane i przechowywane w usłudze Azure blob. Klucze szyfrowania na potrzeby tworzenia kopii zapasowych są przechowywane w zabezpieczonych CloudSimple magazynu zarządzanego, który działa w sieci wirtualnej CloudSimple na platformie Azure.

## <a name="network-security"></a>Bezpieczeństwo sieci

Rozwiązanie CloudSimple zależy od warstwy zabezpieczeń sieci.

### <a name="azure-edge-security"></a>Usługa Azure edge security

Usługi CloudSimple są tworzone na podstawie zabezpieczeń sieci podstawowej, udostępnianych przez platformę Azure. Azure stosuje ochronę w głębi techniki wykrywania i czas odpowiedzi na ataki sieciowe skojarzone z nietypowych transferu danych przychodzących lub wzorców ruchu wychodzącego i rozproszonej typu "odmowa usługi" (DDoS) ataków. Ten formant zabezpieczeń ma zastosowanie do środowisk chmur prywatnych i oprogramowanie płaszczyzna kontroli, opracowane przez CloudSimple.

### <a name="segmentation"></a>Segmentacja

Usługa CloudSimple ma logicznie w oddzielnych sieciach warstwy 2, które ograniczają dostęp do sieci prywatnych w środowisku chmury prywatnej. Można dodatkowo zabezpieczyć sieci prywatnej chmury, za pomocą zapory. W portalu CloudSimple można zdefiniować reguły kontroli ruchu sieciowego wschód zachód i północ południe dla całego ruchu sieciowego, w tym ruch chmury prywatnej w obrębie, ruch między prywatnej chmury, ogólne ruch do Internetu i ruchu sieciowego do serwera lokalnego za pośrednictwem połączenia sieci VPN IPsec lub usługi Azure ExpressRoute.

## <a name="vulnerability-and-patch-management"></a>Zarządzanie luk w zabezpieczeniach i poprawki 

CloudSimple jest odpowiedzialny za poprawek zabezpieczeń okresowe zarządzanego oprogramowania VMware, takich jak ESXi, vCenter i NSX.

## <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem

Klienci mogą uwierzytelniać się do swojego konta platformy Azure (w usłudze Azure Active Directory) przy użyciu uwierzytelniania wieloskładnikowego lub logowania jednokrotnego jako preferowane. W witrynie Azure portal możesz uruchomić CloudSimple portal bez ponownego wprowadzania poświadczeń.

CloudSimple obsługuje opcjonalna konfiguracja źródła tożsamości dla serwera vCenter chmury prywatnej. Możesz użyć [źródła tożsamości lokalnych](https://docs.azure.cloudsimple.com/set-vcenter-identity), nowe źródło tożsamości chmury prywatnej lub [usługi Azure Active Directory](https://docs.azure.cloudsimple.com/azure-ad).

Domyślnie klienci otrzymują uprawnienia, które są niezbędne do codziennych operacji programu vCenter w chmurze prywatnej. Ten poziom uprawnień nie uwzględnia dostęp administracyjny do vCenter. Jeśli dostęp administracyjny tymczasowo wymagane jest, możesz to zrobić [podwyższania poziomu uprawnień użytkownika,](https://docs.azure.cloudsimple.com/escalate-private-cloud-privileges) przez ograniczony okres, podczas wykonywania zadań administracyjnych.

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się, jak [utworzyć usługę CloudSimple na platformie Azure](quickstart-create-cloudsimple-service.md).
* Dowiedz się, jak [utworzyć chmurę prywatną](https://docs.azure.cloudsimple.com/create-private-cloud/).
* Dowiedz się, jak [konfigurowania środowiska chmury prywatnej](quickstart-create-private-cloud.md).
