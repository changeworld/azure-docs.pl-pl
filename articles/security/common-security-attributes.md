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
ms.openlocfilehash: 460d8756c437a1212aef054cf069be2bccac8c8a
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/22/2019
ms.locfileid: "66001360"
---
# <a name="security-attributes-for-azure-services"></a>Atrybuty zabezpieczeń dla usług platformy Azure

W tym artykule zbiera wspólne atrybuty zabezpieczeń dla wybranych usług systemu Azure. 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="azure-api-managementapi-managementapi-management-security-attributesmd"></a>[Azure API Management](../api-management/api-management-security-attributes.md)

### <a name="preventative"></a>Zapobiegawczych

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie danych magazynowanych:<ul><li>Szyfrowanie po stronie serwera</li><li>Szyfrowanie po stronie serwera za pomocą kluczy zarządzanych przez klienta</li><li>Inne funkcje szyfrowania (na przykład po stronie klienta, są zawsze szyfrowane, itd.)</ul>| Tak (tylko w przypadku szyfrowania po stronie usługi) | Dane poufne, takie jak certyfikaty, klucze i wartości o nazwie klucza tajnego są zaszyfrowane przy użyciu zarządzanego przez usługę, na klucze wystąpienia usługi. |
| Szyfrowanie podczas przesyłania:<ul><li>Express route szyfrowania</li><li>W przypadku szyfrowania sieci wirtualnej</li><li>Sieć wirtualna-sieć wirtualna szyfrowania</ul>| Tak | [Expressroute](../expressroute/index.yml) i sieci wirtualnej, szyfrowanie jest obsługiwane przez [sieci platformy Azure](../virtual-network/index.yml). |
| Obsługa klucza szyfrowania (CMK BYOK, itp.)| Nie | Wszystkie klucze szyfrowania są za wystąpienie usługi i usługi zarządzane. |
| Szyfrowanie na poziomie kolumny (Azure Data Services)| ND | |
| Wywołania interfejsu API szyfrowane| Tak | Wywołania płaszczyzny zarządzania odbywa się za pomocą [usługi Azure Resource Manager](../azure-resource-manager/index.yml) za pośrednictwem protokołu TLS. Nieprawidłowy token sieci web JSON (JWT) jest wymagana.  Wywołania płaszczyzny danych mogą być chronione przy użyciu protokołu TLS i jeden z mechanizmów uwierzytelniania obsługiwanych (np. certyfikat klienta lub JWT).
 |

### <a name="network-segmentation"></a>Segmentacji sieci

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Obsługa punktu końcowego usługi| Nie | |
| Obsługa iniekcji sieci wirtualnej| Tak | |
| Izolacja sieci i zapory pomocy technicznej| Tak | Przy użyciu sieciowych grup zabezpieczeń (NSG) i Azure Application Gateway (lub inne urządzenie programowe) odpowiednio. |
| Obsługa tunelowania wymuszonego| Tak | Sieć Azure udostępnia wymuszonym tunelowaniem. |

### <a name="detection"></a>Wykrywanie

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa (usługi Log analytics, usługi App insights itp.) do monitorowania platformy Azure| Tak | |

### <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Authentication| Tak | |
| Autoryzacja| Tak | |


### <a name="audit-trail"></a>Dziennik inspekcji

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Rejestrowanie płaszczyzny zarządzania i kontroli i inspekcji| Tak | [Dzienniki aktywności w usłudze Azure Monitor](../azure-monitor/platform/activity-logs-overview.md) |
| Rejestrowanie płaszczyzny danych i inspekcji| Tak | [Dzienniki diagnostyczne platformy Azure Monitor](../azure-monitor/platform/diagnostic-logs-overview.md) i (opcjonalnie) [usługi Azure Application Insights](../azure-monitor/app/app-insights-overview.md).  |

### <a name="configuration-management"></a>Zarządzanie konfiguracją

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracji (przechowywanie wersji konfiguracji itp.)| Tak | Za pomocą [usługi Azure API Management DevOps Resource Kit](https://aka.ms/apimdevops) |


## <a name="azure-app-serviceapp-serviceapp-service-security-attributesmd"></a>[Usługa Azure App Service](../app-service/app-service-security-attributes.md)

### <a name="preventative"></a>Zapobiegawczych

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie w spoczynku (na przykład szyfrowanie po stronie serwera, szyfrowanie po stronie serwera za pomocą kluczy zarządzanych przez klienta i inne funkcje szyfrowania) | Tak | Zawartość pliku witryny sieci Web jest przechowywana w usłudze Azure Storage automatycznie szyfruje zawartość w stanie spoczynku. Zobacz [szyfrowanie usługi Azure Storage dla danych magazynowanych](../storage/common/storage-service-encryption.md).<br><br>Wpisy tajne klienta dostarczane są szyfrowane, gdy. Wpisy tajne są szyfrowane, gdy podczas przechowywane w bazach danych konfiguracji usługi App Service.<br><br>Lokalnie dołączonych dysków może opcjonalnie służyć jako tymczasowy magazyn przez witryny sieci Web (D:\local i % TMP %). Podłączone lokalnie dyski nie są szyfrowane w stanie spoczynku. |
| Szyfrowanie podczas transferu (np. szyfrowanie usługi ExpressRoute w sieci wirtualnej, szyfrowania i szyfrowania sieć wirtualna-sieć wirtualna)| Tak | Klienci mogą skonfigurować witryny sieci web w celu wymagania i używać protokołu HTTPS dla ruchu przychodzącego. Zobacz wpis w blogu [jak usługi Azure App Service tylko HTTPS](https://blogs.msdn.microsoft.com/benjaminperkins/2017/11/30/how-to-make-an-azure-app-service-https-only/). |
| Obsługa klucza szyfrowania (CMK BYOK, itp.)| Tak | Klienci mogą wybrać opcję przechowywania wpisów tajnych aplikacji w usłudze Key Vault i pobierać je w czasie wykonywania. Zobacz [odwołuje się do użycia magazynu kluczy dla usługi App Service i Azure Functions (wersja zapoznawcza)](../app-service/app-service-key-vault-references.md).|
| Szyfrowanie na poziomie kolumny (Azure Data Services)| ND | |
| Wywołania interfejsu API szyfrowane| Tak | Występować wywołań zarządzania można konfigurować usługę App Service za pośrednictwem [usługi Azure Resource Manager](../azure-resource-manager/index.yml) wywołań za pośrednictwem protokołu HTTPS. |

### <a name="network-segmentation"></a>Segmentacji sieci

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Obsługa punktu końcowego usługi| Tak | Obecnie dostępne w wersji zapoznawczej dla usługi App Service. Zobacz [ograniczenia dostępu do usługi Azure App Service](../app-service/app-service-ip-restrictions.md). |
| Obsługa iniekcji sieci wirtualnej| Tak | Środowiska usługi App Service są implementacji prywatnej usługi App Service dedykowanego pojedynczemu klientowi, które są wstrzykiwane do sieci wirtualnej klienta. Zobacz [wprowadzenie do środowisk App Service Environment](../app-service/environment/intro.md). |
| Izolacja sieci i Firewalling pomocy technicznej| Tak | Publiczne odmiany wielodostępnej usługi App Service klienci mogą skonfigurować listy ACL (ograniczenia adresów IP) do blokowania dozwolonego ruchu przychodzącego sieci.  Zobacz [ograniczenia dostępu do usługi Azure App Service](../app-service/app-service-ip-restrictions.md).  Środowiska usługi App Service są wdrażane bezpośrednio do sieci wirtualnych i dlatego mogą być chronione przy użyciu sieciowych grup zabezpieczeń. |
| Obsługa tunelowania wymuszonego| Tak | Środowiska usługi App Service można wdrożyć w sieci wirtualnej klienta, gdy wymuszanie tunelowania jest konfigurowane. Klienci muszą postępuj zgodnie z instrukcjami [skonfigurować środowisko App Service Environment przy wymuszonego tunelowania](../app-service/environment/forced-tunnel-support.md). |

### <a name="detection"></a>Wykrywanie

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa (usługi Log analytics, usługi App insights itp.) do monitorowania platformy Azure| Tak | Usługa App Service integruje się z usługą Application Insights dla języków, które obsługują usługi Application Insights (pełny program .NET Framework, .NET Core, Java i Node.JS).  Zobacz [wydajności monitorowania usługi Azure App Service](../azure-monitor/app/azure-web-apps.md). Usługa App Service wysyła również metryki aplikacji w usłudze Azure Monitor. Zobacz [monitorować aplikacje w usłudze Azure App Service](../app-service/web-sites-monitor.md). |

### <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Authentication| Tak | Klienci mogą tworzyć aplikacje w usłudze App Service, które integrują się automatycznie z [usługi Azure Active Directory (Azure AD)](../active-directory/index.md) oraz innych dostawców tożsamości zgodnych OAuth; zobacz [uwierzytelnianie i autoryzacja w Usługa Azure App Service](../app-service/overview-authentication-authorization.md). Zarządzanie dostępu do zasobów usługi App Service wszystkie dostęp jest kontrolowany przy użyciu kombinacji nazwy głównej usługi Azure AD uwierzytelniony i ról RBAC usługi Resource Manager platformy Azure. |
| Autoryzacja| Tak | Zarządzanie dostępu do zasobów usługi App Service wszystkie dostęp jest kontrolowany przy użyciu kombinacji nazwy głównej usługi Azure AD uwierzytelniony i ról RBAC usługi Resource Manager platformy Azure.  |


### <a name="audit-trail"></a>Dziennik inspekcji

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Rejestrowanie płaszczyzny zarządzania i kontroli i inspekcji| Tak | Wszystkie operacje zarządzania wykonywane na obiektach usługi App Service wystąpić za pośrednictwem [usługi Azure Resource Manager](../azure-resource-manager/index.yml). Historyczne Dzienniki te operacje są dostępne zarówno w portalu, jak i za pomocą interfejsu wiersza polecenia; zobacz [operacji dostawcy zasobów usługi Azure Resource Manager](../role-based-access-control/resource-provider-operations.md#microsoftweb) i [az monitor z dziennika aktywności](/cli/azure/monitor/activity-log). |
| Rejestrowanie płaszczyzny danych i inspekcji | Nie | Płaszczyzny danych dla usługi App Service jest zdalnym udziale plików zawierających zawartości witryny sieci web s wdrożonych klientów.  Nie ma żadnych inspekcji w zdalnym udziale plików. |

### <a name="configuration-management"></a>Zarządzanie konfiguracją

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracji (przechowywanie wersji konfiguracji itp.)| Tak | Dla operacji zarządzania stan konfiguracji usługi App Service można wyeksportować jako szablon usługi Azure Resource Manager i wersjonowanymi wraz z upływem czasu. Dla operacji środowiska uruchomieniowego klienci mogą zachować wiele różnych wersji na żywo aplikacji przy użyciu funkcji miejsc wdrożenia usługi App Service. | 


## <a name="azure-resource-managerazure-resource-managerazure-resource-manager-security-attributesmd"></a>[Azure Resource Manager](../azure-resource-manager/azure-resource-manager-security-attributes.md)

### <a name="preventative"></a>Zapobiegawczych

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie danych magazynowanych:<ul><li>Szyfrowanie po stronie serwera</li><li>Szyfrowanie po stronie serwera za pomocą kluczy zarządzanych przez klienta</li><li>Inne funkcje szyfrowania (na przykład po stronie klienta, są zawsze szyfrowane, itd.)</ul>| Tak |  |
| Szyfrowanie podczas przesyłania:<ul><li>Express route szyfrowania</li><li>W przypadku szyfrowania sieci wirtualnej</li><li>Sieć wirtualna-sieć wirtualna szyfrowania</ul>| Tak | HTTPS/TLS. |
| Obsługa klucza szyfrowania (CMK BYOK, itp.)| ND | Usługa Azure Resource Manager przechowuje żadnej zawartości klientów tylko danych formantu. |
| Szyfrowanie na poziomie kolumny (Azure Data Services)| Tak | |
| Wywołania interfejsu API szyfrowane| Tak | |

### <a name="network-segmentation"></a>Segmentacji sieci

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Obsługa punktu końcowego usługi| Nie | |
| Obsługa iniekcji sieci wirtualnej| Tak | |
| Izolacja sieci i zapory pomocy technicznej| Nie |  |
| Obsługa tunelowania wymuszonego| Nie |  |

### <a name="detection"></a>Wykrywanie

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa (usługi Log analytics, usługi App insights itp.) do monitorowania platformy Azure| Nie | |

### <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Authentication| Tak | [Usługa Azure Active Directory](/azure/active-directory) na podstawie.|
| Autoryzacja| Tak | |


### <a name="audit-trail"></a>Dziennik inspekcji

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Rejestrowanie płaszczyzny zarządzania i kontroli i inspekcji| Tak | Dzienniki aktywności udostępniają wszystkie operacje zapisu w magazynie (PUT, POST, DELETE) wykonywanych względem zasobów; zobacz [wyświetlanie dzienników aktywności do inspekcji akcje na zasobach](../azure-resource-manager/resource-group-audit.md). |
| Rejestrowanie płaszczyzny danych i inspekcji| ND | |

### <a name="configuration-management"></a>Zarządzanie konfiguracją

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracji (przechowywanie wersji konfiguracji itp.)| Tak |  |


## <a name="azure-backupbackupbackup-security-attributesmd"></a>[Azure Backup](../backup/backup-security-attributes.md)

### <a name="preventative"></a>Zapobiegawczych

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie danych magazynowanych:<ul><li>Szyfrowanie po stronie serwera</li><li>Szyfrowanie po stronie serwera za pomocą kluczy zarządzanych przez klienta</li><li>Inne funkcje szyfrowania (na przykład po stronie klienta, są zawsze szyfrowane, itd.)</ul>| Tak | Dzięki szyfrowaniu usługi storage dla kont magazynu. |
| Szyfrowanie podczas przesyłania:<ul><li>Express route szyfrowania</li><li>W przypadku szyfrowania sieci wirtualnej</li><li>Sieć wirtualna-sieć wirtualna szyfrowania</ul>| Nie | Przy użyciu protokołu HTTPS. |
| Obsługa klucza szyfrowania (CMK BYOK, itp.)| Nie |  |
| Szyfrowanie na poziomie kolumny (Azure Data Services)| Nie |  |
| Wywołania interfejsu API szyfrowane| Tak |  |

### <a name="network-segmentation"></a>Segmentacji sieci

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Obsługa punktu końcowego usługi| Nie |  |
| Obsługa iniekcji sieci wirtualnej| Nie |  |
| Izolacja sieci i zapory pomocy technicznej| Tak | Wymuszanie tunelowania jest obsługiwana dla kopii zapasowych maszyn wirtualnych. Wymuszone tunelowanie nie jest obsługiwana w przypadku obciążeń uruchomionych maszyn wirtualnych. |
| Obsługa tunelowania wymuszonego| Nie |  |

### <a name="detection"></a>Wykrywanie

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa (usługi Log analytics, usługi App insights itp.) do monitorowania platformy Azure| Tak | Usługa log Analytics jest świadczona za pośrednictwem dzienników diagnostycznych. Monitorowanie usługi Azure Backup chroniony obciążeń przy użyciu usługi Log Analytics (https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/) Aby uzyskać więcej informacji. |

### <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Authentication| Tak | Uwierzytelnianie jest za pomocą usługi Azure Active Directory. |
| Autoryzacja| Tak | Klient utworzył i wbudowane role kontroli RBAC są używane. Zobacz Use Role-Based kontrola dostępu do zarządzania usługi Azure Backup punkty odzyskiwania (/ / backup/magazynu kopii zapasowych azure — rbac-rs —) Aby uzyskać więcej informacji. |


### <a name="audit-trail"></a>Dziennik inspekcji

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Rejestrowanie płaszczyzny zarządzania i kontroli i inspekcji| Tak | Dzienniki aktywności są rejestrowane wszystkie akcje klienta wyzwalane w witrynie Azure portal. |
| Rejestrowanie płaszczyzny danych i inspekcji| Nie | Płaszczyzna danych w usłudze Azure Backup nie można nawiązać połączenia z bezpośrednio.  |

### <a name="configuration-management"></a>Zarządzanie konfiguracją

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracji (przechowywanie wersji konfiguracji itp.)| Tak|  |


## <a name="azure-key-vaultkey-vaultkey-vault-security-attributesmd"></a>[Usługa Azure Key Vault](../key-vault/key-vault-security-attributes.md)

### <a name="preventative"></a>Zapobiegawczych

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie danych magazynowanych:<ul><li>Szyfrowanie po stronie serwera</li><li>Szyfrowanie po stronie serwera za pomocą kluczy zarządzanych przez klienta</li><li>Inne funkcje szyfrowania (na przykład po stronie klienta, są zawsze szyfrowane, itd.)</ul>| Tak | Wszystkie obiekty są szyfrowane. |
| Szyfrowanie podczas przesyłania:<ul><li>Express route szyfrowania</li><li>W przypadku szyfrowania sieci wirtualnej</li><li>Sieć wirtualna-sieć wirtualna szyfrowania</ul>| Tak | Cała komunikacja jest za pomocą zaszyfrowanych wywołań interfejsu API |
| Obsługa klucza szyfrowania (CMK BYOK, itp.)| Tak | Klient kontroluje wszystkie klucze w ich usługi Key Vault. Jeśli określono klucze modułu HSM kopii zabezpieczeń sprzętowych FIPS poziom 2 przez sprzętowy moduł zabezpieczeń chroni klucza, certyfikatu lub klucza tajnego. |
| Szyfrowanie na poziomie kolumny (Azure Data Services)| ND |  |
| Wywołania interfejsu API szyfrowane| Tak | Przy użyciu protokołu HTTPS. |

### <a name="network-segmentation"></a>Segmentacji sieci

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Obsługa punktu końcowego usługi| Tak | Za pomocą punktów końcowych usługi sieci wirtualnej (VNet). |
| Obsługa iniekcji sieci wirtualnej| Nie |  |
| Izolacja sieci i zapory pomocy technicznej| Tak | Korzystanie z reguł zapory sieci wirtualnej. |
| Obsługa tunelowania wymuszonego| Nie |  |

### <a name="detection"></a>Wykrywanie

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa (usługi Log analytics, usługi App insights itp.) do monitorowania platformy Azure| Tak | Za pomocą usługi Log Analytics. |

### <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Authentication| Tak | Uwierzytelnianie jest za pomocą usługi Azure Active Directory. |
| Autoryzacja| Tak | Za pomocą zasad dostępu magazynu kluczy. |


### <a name="audit-trail"></a>Dziennik inspekcji

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Rejestrowanie i inspekcja na płaszczyźnie kontroli i zarządzania nimi.| Tak | Za pomocą usługi Log Analytics. |
| Rejestrowanie płaszczyzny danych i inspekcji| Tak | Za pomocą usługi Log Analytics. |

### <a name="access-controls"></a>Kontrole dostępu

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Kontroli dostępu do płaszczyzny zarządzania/kontroli | Tak | Kontrola dostępu oparta na rolach (RBAC) przy użyciu usługi Azure Resource Manager |
| Kontroli dostępu do płaszczyzny danych (na każdym poziomie usługi) | Tak | Zasady dostępu magazynu kluczy |

## <a name="azure-service-bus-messagingservice-bus-messagingservice-bus-messaging-security-attributesmd"></a>[Azure komunikaty usługi Service Bus](../service-bus-messaging/service-bus-messaging-security-attributes.md)

### <a name="preventative"></a>Zapobiegawczych

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie danych magazynowanych:<ul><li>Szyfrowanie po stronie serwera</li><li>Szyfrowanie po stronie serwera za pomocą kluczy zarządzanych przez klienta</li><li>Inne funkcje szyfrowania (na przykład po stronie klienta, są zawsze szyfrowane, itd.)</ul>|  Tak po stronie serwera szyfrowania podczas spoczynku domyślnie. | Klucze zarządzaną przez klienta i scenariusza BYOK nie są jeszcze obsługiwane. Szyfrowanie po stronie klienta jest odpowiedzialny za klienta |
| Szyfrowanie podczas przesyłania:<ul><li>Express route szyfrowania</li><li>W przypadku szyfrowania sieci wirtualnej</li><li>Sieć wirtualna-sieć wirtualna szyfrowania</ul>| Tak | Obsługuje standardowego mechanizmu HTTPS/TLS. |
| Obsługa klucza szyfrowania (CMK BYOK, itp.)| Nie |   |
| Szyfrowanie na poziomie kolumny (Azure Data Services)| ND | |
| Wywołania interfejsu API szyfrowane| Tak | Wywołania interfejsu API odbywa się za pomocą [usługi Azure Resource Manager](../azure-resource-manager/index.yml) i HTTPS. |

### <a name="network-segmentation"></a>Segmentacji sieci

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Obsługa punktu końcowego usługi| Tak (tylko w warstwie Premium) | Punkty końcowe usługi sieci wirtualnej są obsługiwane w przypadku [warstwy usługi Service Bus w warstwie Premium](../service-bus-messaging/service-bus-premium-messaging.md) tylko. |
| Obsługa iniekcji sieci wirtualnej| Nie | |
| Izolacja sieci i zapory pomocy technicznej| Tak (tylko w warstwie Premium) |  |
| Obsługa tunelowania wymuszonego| Nie |  |

### <a name="detection"></a>Wykrywanie

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa (usługi Log analytics, usługi App insights itp.) do monitorowania platformy Azure| Tak | Obsługiwane za pośrednictwem [usługa Azure Monitor i alerty](../service-bus-messaging/service-bus-metrics-azure-monitor.md). |

### <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Authentication| Tak | Zarządzane za pośrednictwem [usługi Azure Active Directory tożsamości usługi zarządzanej](../service-bus-messaging/service-bus-managed-service-identity.md); zobacz [usługi Service Bus, uwierzytelnianie i autoryzacja](../service-bus-messaging/service-bus-authentication-and-authorization.md).|
| Autoryzacja| Tak | Obsługuje autoryzację za pośrednictwem [RBAC](../service-bus-messaging/service-bus-role-based-access-control.md) (wersja zapoznawcza) i tokenu sygnatury dostępu Współdzielonego, zobacz [usługi Service Bus, uwierzytelnianie i autoryzacja](../service-bus-messaging/service-bus-authentication-and-authorization.md). |

### <a name="audit-trail"></a>Dziennik inspekcji

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Rejestrowanie płaszczyzny zarządzania i kontroli i inspekcji| Tak | Dzienniki czynności są dostępne; zobacz [dzienników diagnostycznych usługi Service Bus](../service-bus-messaging/service-bus-diagnostic-logs.md).  |
| Rejestrowanie płaszczyzny danych i inspekcji| Nie |  |

### <a name="configuration-management"></a>Zarządzanie konfiguracją

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracji (przechowywanie wersji konfiguracji itp.)| Tak | Obsługuje przechowywanie wersji dostawcy zasobu za pośrednictwem [interfejsu API usługi Azure Resource Manager](/rest/api/resources/).|


## <a name="azure-service-bus-relayservice-bus-relayservice-bus-relay-security-attributesmd"></a>[Azure Service Bus Relay](../service-bus-relay/service-bus-relay-security-attributes.md)

### <a name="preventative"></a>Zapobiegawczych

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie danych magazynowanych:<ul><li>Szyfrowanie po stronie serwera</li><li>Szyfrowanie po stronie serwera za pomocą kluczy zarządzanych przez klienta</li><li>Inne funkcje szyfrowania (na przykład po stronie klienta, są zawsze szyfrowane, itd.)</ul>|  ND | Przekaźnik jest gniazda sieci web i nie utrwala dane. |
| Szyfrowanie podczas przesyłania:<ul><li>Express route szyfrowania</li><li>W przypadku szyfrowania sieci wirtualnej</li><li>Sieć wirtualna-sieć wirtualna szyfrowania</ul>| Tak | Usługa wymaga protokołu TLS. |
| Obsługa klucza szyfrowania (CMK BYOK, itp.)| Nie | Używa tylko certyfikaty TLS firmy Microsoft.  |
| Szyfrowanie na poziomie kolumny (Azure Data Services)| ND | |
| Wywołania interfejsu API szyfrowane| Tak | HTTPS. |

### <a name="network-segmentation"></a>Segmentacji sieci

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Obsługa punktu końcowego usługi| Nie |  |
| Izolacja sieci i zapory pomocy technicznej| Nie |  |
| Obsługa tunelowania wymuszonego| ND | Przekaźnik to tunelu TLS  |

### <a name="detection"></a>Wykrywanie

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa (usługi Log analytics, usługi App insights itp.) do monitorowania platformy Azure| Tak | |

### <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Authentication| Tak | Via SAS. |
| Autoryzacja|  Tak | Via SAS. |


### <a name="audit-trail"></a>Dziennik inspekcji

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Rejestrowanie płaszczyzny zarządzania i kontroli i inspekcji| Tak | Za pomocą [usługi Azure Resource Manager](../azure-resource-manager/index.yml). |
| Rejestrowanie płaszczyzny danych i inspekcji| Tak | Pomyślnego połączenia / awarii i błędów i rejestrowane.  |

### <a name="configuration-management"></a>Zarządzanie konfiguracją

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracji (przechowywanie wersji konfiguracji itp.)| Tak | Za pomocą [usługi Azure Resource Manager](../azure-resource-manager/index.yml).|


## <a name="azure-service-fabricservice-fabricservice-fabric-security-attributesmd"></a>[Azure Service Fabric](../service-fabric/service-fabric-security-attributes.md)

### <a name="preventative"></a>Zapobiegawczych

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie danych magazynowanych:<ul><li>Szyfrowanie po stronie serwera</li><li>Szyfrowanie po stronie serwera za pomocą kluczy zarządzanych przez klienta</li><li>Inne funkcje szyfrowania (na przykład po stronie klienta, są zawsze szyfrowane, itd.)</ul>| Tak | Klient jest właścicielem klastra i zestawu skalowania maszyn wirtualnych (VM), które klaster działa w oparciu. Usługa Azure disk encryption można włączyć dla zestawu skalowania maszyn wirtualnych. |
| Szyfrowanie podczas przesyłania:<ul><li>Express route szyfrowania</li><li>W przypadku szyfrowania sieci wirtualnej</li><li>Sieć wirtualna-sieć wirtualna szyfrowania</ul>| Tak |  |
| Obsługa klucza szyfrowania (CMK BYOK, itp.)| Tak | Klient jest właścicielem klastra i zestawu skalowania maszyn wirtualnych (VM), które klaster działa w oparciu. Usługa Azure disk encryption można włączyć dla zestawu skalowania maszyn wirtualnych. |
| Szyfrowanie na poziomie kolumny (Azure Data Services)| ND |  |
| Wywołania interfejsu API szyfrowane| Tak | Wywołania interfejsu API usługi Service Fabric są wykonywane za pośrednictwem usługi Azure Resource Manager. Nieprawidłowy token sieci web JSON (JWT) jest wymagana. |

### <a name="network-segmentation"></a>Segmentacji sieci

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Obsługa punktu końcowego usługi| Tak |  |
| Obsługa iniekcji sieci wirtualnej| Tak |  |
| Izolacja sieci i zapory pomocy technicznej| Tak | Przy użyciu sieciowych grup zabezpieczeń (NSG). |
| Obsługa tunelowania wymuszonego| Tak | Sieć Azure udostępnia wymuszonym tunelowaniem. |

### <a name="detection"></a>Wykrywanie

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa (usługi Log analytics, usługi App insights itp.) do monitorowania platformy Azure| Tak | Korzystanie z platformy Azure, monitorowanie, obsługę i innych firm. |

### <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Authentication| Tak | Uwierzytelnianie jest za pomocą usługi Azure Active Directory. |
| Autoryzacja| Tak | Zarządzanie tożsamościami i dostępem (IAM) dla połączeń za pośrednictwem SFRP. Wywołań bezpośrednio do punktu końcowego klastra obsługuje dwie role: Użytkownika i administratora. Klienta można mapować interfejsów API do każdej roli. |

### <a name="audit-trail"></a>Dziennik inspekcji

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Rejestrowanie płaszczyzny zarządzania i kontroli i inspekcji| Tak | Wszystkie operacje warstwy kontroli przez procesy do inspekcji i zatwierdzeń. |
| Rejestrowanie płaszczyzny danych i inspekcji| ND | Klient jest właścicielem klastra.  |

### <a name="configuration-management"></a>Zarządzanie konfiguracją

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracji (przechowywanie wersji konfiguracji itp.)| Tak | Konfiguracja usługi jest wersjonowane i wdrażane za pomocą wdrażania na platformie Azure. Kod (aplikacji i środowiska uruchomieniowego) jest wersjonowany, za pomocą usługi Azure kompilacji.
 |

## <a name="azure-sql-databasesql-databasesql-database-security-attributesmd"></a>[Azure SQL Database](../sql-database/sql-database-security-attributes.md)

### <a name="preventative"></a>Zapobiegawczych

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie danych magazynowanych:<ul><li>Szyfrowanie po stronie serwera</li><li>Szyfrowanie po stronie serwera za pomocą kluczy zarządzanych przez klienta</li><li>Inne funkcje szyfrowania (na przykład po stronie klienta, są zawsze szyfrowane, itd.)</ul>| Tak | Określane jako "szyfrowania używanego", zgodnie z opisem w artykule [Always Encrypted](../sql-database/sql-database-always-encrypted.md). Szyfrowanie po stronie usługi używa [technologii transparent data encryption](../sql-database/transparent-data-encryption-azure-sql.md) (TDE).|
| Szyfrowanie podczas przesyłania:<ul><li>Szyfrowanie usługi ExpressRoute</li><li>W przypadku szyfrowania sieci wirtualnej</li><li>Sieć wirtualna-sieć wirtualna szyfrowania</ul>| Tak | Przy użyciu protokołu HTTPS. |
| Obsługa klucza szyfrowania (CMK BYOK, itp.)| Tak | Oferowana obsługa klucza zarządzanego przez usługę i zarządzane przez klienta (ostatnie za pośrednictwem [usługi Azure Key Vault](../key-vault/index.yml). |
| Szyfrowanie na poziomie kolumny (Azure Data Services)| Tak | Za pomocą [Always Encrypted](../sql-database/sql-database-always-encrypted.md). |
| Wywołania interfejsu API szyfrowane| Tak | Przy użyciu protokołu HTTPS/SSL. |

### <a name="network-segmentation"></a>Segmentacji sieci

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Obsługa punktu końcowego usługi| Tak | Dotyczy [pojedynczej bazy danych](../sql-database/sql-database-single-index.yml) tylko. |
| Obsługa iniekcji sieci wirtualnej| Tak | Dotyczy [wystąpienia zarządzanego](../sql-database/sql-database-managed-instance.md) tylko. |
| Izolacja sieci i zapory pomocy technicznej| Tak | Zapora w obu bazy danych i serwera poziomie; Izolacja sieci [wystąpienia zarządzanego](../sql-database/sql-database-managed-instance.md) tylko |
| Obsługa tunelowania wymuszonego| Tak | [wystąpienie zarządzane](../sql-database/sql-database-managed-instance.md) za pośrednictwem [usługi Azure ExpressRoute](../expressroute/index.yml) sieci VPN |

### <a name="detection"></a>Wykrywanie

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa (usługi Log analytics, usługi App insights itp.) do monitorowania platformy Azure| Tak | Rozwiązanie SIEM innej firmy od firmy Imperva (SecureSphere) jest również obsługiwana, za pomocą [usługi Azure Event Hubs](../event-hubs/index.yml) integrację za pośrednictwem [inspekcji SQL](../sql-database/sql-database-auditing.md). |

### <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Authentication| Tak | Azure Active Directory. |
| Autoryzacja| Tak |  |


### <a name="audit-trail"></a>Dziennik inspekcji

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Rejestrowanie płaszczyzny zarządzania i kontroli i inspekcji| Tak | Tak, aby tylko niektóre zdarzenia. |
| Rejestrowanie płaszczyzny danych i inspekcji | Tak | Za pomocą [inspekcji SQL](../sql-database/sql-database-auditing.md). |

### <a name="configuration-management"></a>Zarządzanie konfiguracją

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracji (przechowywanie wersji konfiguracji itp.)| Nie  | | 

### <a name="additional-security-attributes-for-sql-database"></a>Atrybuty dodatkowe zabezpieczenia dla bazy danych SQL

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Zapobiegawczych: Ocena luk w zabezpieczeniach | Tak | Zobacz [oceny luk w zabezpieczeniach SQL service pomaga zidentyfikować luki w zabezpieczeniach bazy danych](../sql-database/sql-vulnerability-assessment.md). |
| Zapobiegawczych: danych, odnajdowanie i klasyfikację  | Tak | Zobacz [usługi Azure SQL Database i SQL Data Warehouse odnajdywanie i klasyfikacja danych](../sql-database/sql-database-data-discovery-and-classification.md). |
| Wykrywanie: wykrywanie zagrożeń | Tak | Zobacz [Zaawansowana ochrona przed zagrożeniami dla usługi Azure SQL Database](../sql-database/sql-database-threat-detection-overview.md). |

## <a name="azure-storagestoragecommonstorage-security-attributesmd"></a>[Azure Storage](../storage/common/storage-security-attributes.md)

### <a name="preventative"></a>Zapobiegawczych

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie danych magazynowanych:<ul><li>Szyfrowanie po stronie serwera</li><li>Szyfrowanie po stronie serwera za pomocą kluczy zarządzanych przez klienta</li><li>Inne funkcje szyfrowania (na przykład po stronie klienta, są zawsze szyfrowane, itd.)</ul>| Tak |  |
| Szyfrowanie podczas przesyłania:<ul><li>Express route szyfrowania</li><li>W przypadku szyfrowania sieci wirtualnej</li><li>Sieć wirtualna-sieć wirtualna szyfrowania</ul>| Tak | Obsługa standardowych mechanizmów HTTPS/TLS.  Użytkownicy mogą także szyfrować dane przed ich wysłaniem do usługi. |
| Obsługa klucza szyfrowania (CMK BYOK, itp.)| Tak | Zobacz [szyfrowanie usługi Storage przy użyciu kluczy zarządzanych przez klienta w usłudze Azure Key Vault](../storage/common/storage-service-encryption-customer-managed-keys.md).|
| Szyfrowanie na poziomie kolumny (Azure Data Services)| ND |  |
| Wywołania interfejsu API szyfrowane| Tak |  |

### <a name="network-segmentation"></a>Segmentacji sieci

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Obsługa punktu końcowego usługi| Tak |  |
| Obsługa iniekcji sieci wirtualnej| ND |  |
| Izolacja sieci i zapory pomocy technicznej| Tak | |
| Obsługa tunelowania wymuszonego| ND |  |

### <a name="detection"></a>Wykrywanie

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa (usługi Log analytics, usługi App insights itp.) do monitorowania platformy Azure| Tak | Metryki usługi Azure Monitor dostępny teraz dzienniki począwszy od wersji zapoznawczej |

### <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Authentication| Tak | Usługa Azure Active Directory, klucz współużytkowany, token dostępu współdzielonego. |
| Autoryzacja| Tak | Obsługuje autoryzacji RBAC, listy ACL modelu POSIX i tokeny sygnatur dostępu Współdzielonego |


### <a name="audit-trail"></a>Dziennik inspekcji

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Rejestrowanie płaszczyzny zarządzania i kontroli i inspekcji | Tak | Dziennik aktywności platformy Azure Resource Manager |
| Rejestrowanie płaszczyzny danych i inspekcji| Tak | Dzienniki diagnostyczne usługi i rejestrowanie usługi Azure Monitor począwszy od wersji zapoznawczej  |

### <a name="configuration-management"></a>Zarządzanie konfiguracją

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracji (przechowywanie wersji konfiguracji itp.)| Tak | Obsługa wersji dostawcy zasobów za pośrednictwem interfejsów API usługi Azure Resource Manager |