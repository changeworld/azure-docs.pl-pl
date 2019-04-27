---
title: Atrybuty zabezpieczeń dla usług platformy Azure
description: Lista kontrolna typowych atrybutów zabezpieczeń do oceny usługi Azure Service Fabric
services: security
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: security
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: mbaldwin
ms.openlocfilehash: 2eb480e10ca3b674895d2d22cc44fb52f305f988
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60610006"
---
# <a name="common-security-attributes-for-azure-services"></a>Wspólne atrybuty zabezpieczeń dla usług platformy Azure

Zabezpieczenia są zintegrowane w każdy aspekt usługi platformy Azure. W tym artykule zbiera wspólne atrybuty zabezpieczeń dla wybranych usług systemu Azure. 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]


## <a name="azure-backupbackupbackup-security-attributesmd"></a>[Azure Backup](../backup/backup-security-attributes.md)

### <a name="preventative"></a>Zapobiegawczych

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie danych magazynowanych:<ul><li>Szyfrowanie po stronie serwera</li><li>Szyfrowanie po stronie serwera za pomocą kluczy zarządzanych przez klienta</li><li>Inne funkcje szyfrowania (na przykład po stronie klienta, są zawsze szyfrowane, itd.)</ul>| Yes | Dzięki szyfrowaniu usługi storage dla kont magazynu. |
| Szyfrowanie podczas przesyłania:<ul><li>Express route szyfrowania</li><li>W przypadku szyfrowania sieci wirtualnej</li><li>Sieć wirtualna-sieć wirtualna szyfrowania</ul>| Nie | Przy użyciu protokołu HTTPS. |
| Obsługa klucza szyfrowania (CMK BYOK, itp.)| Nie |  |
| Szyfrowanie na poziomie kolumny (usługi danych platformy Azure)| Nie |  |
| Wywołania interfejsu API szyfrowane| Yes |  |

### <a name="network-segmentation"></a>Segmentacji sieci

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Punkt końcowy usługi pomocy technicznej| Nie |  |
| Obsługa iniekcji sieci wirtualnej| Nie |  |
| Izolacja sieci / Zapora pomocy technicznej| Yes | Wymuszanie tunelowania jest obsługiwana dla kopii zapasowych maszyn wirtualnych. Wymuszone tunelowanie nie jest obsługiwana w przypadku obciążeń uruchomionych maszyn wirtualnych. |
| Obsługa wymuszonego tunelowania | Nie |  |

### <a name="detection"></a>Wykrywanie

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa (usługi Log analytics, usługi App insights itp.) do monitorowania platformy Azure| Yes | Usługa log Analytics jest świadczona za pośrednictwem dzienników diagnostycznych. Monitorowanie usługi Azure Backup chroniony obciążeń przy użyciu usługi Log Analytics (https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/) Aby uzyskać więcej informacji. |

### <a name="iam-support"></a>Obsługa zarządzania tożsamościami i Dostępem

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Zarządzanie dostępem — uwierzytelnianie| Yes | Uwierzytelnianie jest za pomocą usługi Azure Active Directory. |
| Zarządzanie dostępem - autoryzacji| Yes | Klient utworzył i wbudowane role kontroli RBAC są używane. Zobacz Use Role-Based kontrola dostępu do zarządzania usługi Azure Backup punkty odzyskiwania (/ / backup/magazynu kopii zapasowych azure — rbac-rs —) Aby uzyskać więcej informacji. |


### <a name="audit-trail"></a>Dziennik inspekcji

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Zarządzania kontrolą/Plan rejestrowania i inspekcji| Yes | Dzienniki aktywności są rejestrowane wszystkie akcje klienta wyzwalane w witrynie Azure portal. |
| Rejestrowanie i inspekcja na płaszczyźnie danych| Nie | Płaszczyzna danych w usłudze Azure Backup nie można nawiązać połączenia z bezpośrednio.  |

### <a name="configuration-management"></a>Zarządzanie konfiguracją

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracji (przechowywanie wersji konfiguracji itp.)| Yes|  |

## <a name="azure-key-vaultkey-vaultkey-vault-security-attributesmd"></a>[Usługa Azure Key Vault](../key-vault/key-vault-security-attributes.md)

### <a name="preventative"></a>Zapobiegawczych

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie danych magazynowanych:<ul><li>Szyfrowanie po stronie serwera</li><li>Szyfrowanie po stronie serwera za pomocą kluczy zarządzanych przez klienta</li><li>Inne funkcje szyfrowania (na przykład po stronie klienta, są zawsze szyfrowane, itd.)</ul>| Yes | Wszystkie obiekty są szyfrowane. |
| Szyfrowanie podczas przesyłania:<ul><li>Express route szyfrowania</li><li>W przypadku szyfrowania sieci wirtualnej</li><li>Sieć wirtualna-sieć wirtualna szyfrowania</ul>| Yes | Cała komunikacja jest za pomocą zaszyfrowanych wywołań interfejsu API |
| Obsługa klucza szyfrowania (CMK BYOK, itp.)| Yes | Klient kontroluje wszystkie klucze w ich usługi Key Vault. Jeśli określono klucze modułu HSM kopii zabezpieczeń sprzętowych FIPS poziom 2 przez sprzętowy moduł zabezpieczeń chroni klucza, certyfikatu lub klucza tajnego. |
| Szyfrowanie na poziomie kolumny (usługi danych platformy Azure)| ND |  |
| Wywołania interfejsu API szyfrowane| Yes | Przy użyciu protokołu HTTPS. |

### <a name="network-segmentation"></a>Segmentacji sieci

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Punkt końcowy usługi pomocy technicznej| Yes | Za pomocą punktów końcowych usługi sieci wirtualnej (Vnet). |
| Obsługa iniekcji sieci wirtualnej| Nie |  |
| Izolacja sieci / Zapora pomocy technicznej| Yes | Korzystanie z reguł zapory sieci wirtualnej. |
| Obsługa wymuszonego tunelowania | Nie |  |

### <a name="detection"></a>Wykrywanie

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa (usługi Log analytics, usługi App insights itp.) do monitorowania platformy Azure| Yes | Za pomocą usługi Log Analytics. |

### <a name="iam-support"></a>Obsługa zarządzania tożsamościami i Dostępem

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Zarządzanie dostępem — uwierzytelnianie| Yes | Uwierzytelnianie jest za pomocą usługi Azure Active Directory. |
| Zarządzanie dostępem - autoryzacji| Yes | Za pomocą zasad dostępu magazynu kluczy. |


### <a name="audit-trail"></a>Dziennik inspekcji

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Rejestrowanie i inspekcja na płaszczyźnie kontroli i zarządzania nimi.| Yes | Za pomocą usługi Log Analytics. |
| Rejestrowanie i inspekcja na płaszczyźnie danych| Yes | Za pomocą usługi Log Analytics. |

### <a name="access-controls"></a>Kontrole dostępu

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Kontroli dostępu do płaszczyzny zarządzania/kontroli | Yes | Kontrola dostępu oparta na rolach (RBAC) przy użyciu usługi Azure Resource Manager |
| Kontroli dostępu do płaszczyzny danych (na każdym poziomie usługi) | Yes | Zasady dostępu magazynu kluczy |

## <a name="azure-service-fabricservice-fabricservice-fabric-security-attributesmd"></a>[Azure Service Fabric](../service-fabric/service-fabric-security-attributes.md)

### <a name="preventative"></a>Zapobiegawczych

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie danych magazynowanych:<ul><li>Szyfrowanie po stronie serwera</li><li>Szyfrowanie po stronie serwera za pomocą kluczy zarządzanych przez klienta</li><li>Inne funkcje szyfrowania (na przykład po stronie klienta, są zawsze szyfrowane, itd.)</ul>| Yes | Klient jest właścicielem klastra i zestawu skalowania maszyn wirtualnych (VM), które klaster działa w oparciu. Usługa Azure disk encryption można włączyć dla zestawu skalowania maszyn wirtualnych. |
| Szyfrowanie podczas przesyłania:<ul><li>Express route szyfrowania</li><li>W przypadku szyfrowania sieci wirtualnej</li><li>Sieć wirtualna-sieć wirtualna szyfrowania</ul>| Yes |  |
| Obsługa klucza szyfrowania (CMK BYOK, itp.)| Yes | Klient jest właścicielem klastra i zestawu skalowania maszyn wirtualnych (VM), które klaster działa w oparciu. Usługa Azure disk encryption można włączyć dla zestawu skalowania maszyn wirtualnych. |
| Szyfrowanie na poziomie kolumny (usługi danych platformy Azure)| ND |  |
| Wywołania interfejsu API szyfrowane| Yes | Wywołania interfejsu API usługi Service Fabric są wykonywane za pośrednictwem usługi Azure Resource Manager. Nieprawidłowy token sieci web JSON (JWT) jest wymagana. |

### <a name="network-segmentation"></a>Segmentacji sieci

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Punkt końcowy usługi pomocy technicznej| Yes |  |
| Obsługa iniekcji sieci wirtualnej| Yes |  |
| Izolacja sieci / Zapora pomocy technicznej| Yes | Przy użyciu sieciowych grup zabezpieczeń (NSG). |
| Obsługa wymuszonego tunelowania | Yes | Sieć Azure udostępnia wymuszonym tunelowaniem. |

### <a name="detection"></a>Wykrywanie

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa (usługi Log analytics, usługi App insights itp.) do monitorowania platformy Azure| Yes | Korzystanie z platformy Azure, monitorowanie, obsługę i innych firm. |

### <a name="iam-support"></a>Obsługa zarządzania tożsamościami i Dostępem

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Zarządzanie dostępem — uwierzytelnianie| Yes | Uwierzytelnianie jest za pomocą usługi Azure Active Directory. |
| Zarządzanie dostępem - autoryzacji| Yes | Zarządzanie tożsamościami i dostępem (IAM) dla połączeń za pośrednictwem SFRP. Wywołań bezpośrednio do punktu końcowego klastra obsługuje dwie role: Użytkownika i administratora. Klienta można mapować interfejsów API do każdej roli. |


### <a name="audit-trail"></a>Dziennik inspekcji

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Zarządzania kontrolą/Plan rejestrowania i inspekcji| Yes | Wszystkie operacje warstwy kontroli przez procesy do inspekcji i zatwierdzeń. |
| Rejestrowanie i inspekcja na płaszczyźnie danych| ND | Klient jest właścicielem klastra.  |

### <a name="configuration-management"></a>Zarządzanie konfiguracją

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracji (przechowywanie wersji konfiguracji itp.)| Yes | Konfiguracja usługi jest wersjonowane i wdrażane za pomocą wdrażania na platformie Azure. Kod (aplikacji i środowiska uruchomieniowego) jest wersjonowany, za pomocą usługi Azure kompilacji.
 |

## <a name="azure-storage"></a>Azure Storage

### <a name="preventative"></a>Zapobiegawczych

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie danych magazynowanych:<ul><li>Szyfrowanie po stronie serwera</li><li>Szyfrowanie po stronie serwera za pomocą kluczy zarządzanych przez klienta</li><li>Inne funkcje szyfrowania (na przykład po stronie klienta, są zawsze szyfrowane, itd.)</ul>| Yes |  |
| Szyfrowanie podczas przesyłania:<ul><li>Express route szyfrowania</li><li>W przypadku szyfrowania sieci wirtualnej</li><li>Sieć wirtualna-sieć wirtualna szyfrowania</ul>| Yes | Obsługa standardowych mechanizmów HTTPS/TLS.  Użytkownicy mogą także szyfrować dane przed ich wysłaniem do usługi. |
| Obsługa klucza szyfrowania (CMK BYOK, itp.)| Yes | Zobacz [szyfrowanie usługi Storage przy użyciu kluczy zarządzanych przez klienta w usłudze Azure Key Vault](../storage/common/storage-service-encryption-customer-managed-keys.md).|
| Szyfrowanie na poziomie kolumny (usługi danych platformy Azure)| ND |  |
| Wywołania interfejsu API szyfrowane| Yes |  |

### <a name="network-segmentation"></a>Segmentacji sieci

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Punkt końcowy usługi pomocy technicznej| Yes |  |
| Obsługa iniekcji sieci wirtualnej| ND |  |
| Izolacja sieci / Zapora pomocy technicznej| Yes | |
| Obsługa wymuszonego tunelowania | ND |  |

### <a name="detection"></a>Wykrywanie

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa (usługi Log analytics, usługi App insights itp.) do monitorowania platformy Azure| Yes | Metryki usługi Azure Monitor dostępny teraz dzienniki począwszy od wersji zapoznawczej |

### <a name="iam-support"></a>Obsługa zarządzania tożsamościami i Dostępem

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Zarządzanie dostępem — uwierzytelnianie| Yes | Usługa Azure Active Directory, klucz współużytkowany, token dostępu współdzielonego. |
| Zarządzanie dostępem - autoryzacji| Yes | Obsługuje autoryzacji RBAC, listy ACL modelu POSIX i tokeny sygnatur dostępu Współdzielonego |


### <a name="audit-trail"></a>Dziennik inspekcji

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Zarządzania kontrolą/Plan rejestrowania i inspekcji | Yes | Dziennik aktywności platformy Azure Resource Manager |
| Rejestrowanie i inspekcja na płaszczyźnie danych| Yes | Dzienniki diagnostyczne usługi i rejestrowanie usługi Azure Monitor począwszy od wersji zapoznawczej  |

### <a name="configuration-management"></a>Zarządzanie konfiguracją

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracji (przechowywanie wersji konfiguracji itp.)| Yes | Obsługa wersji dostawcy zasobów za pośrednictwem interfejsów API usługi Azure Resource Manager |