---
title: Atrybuty zabezpieczeń dla usług platformy Azure
description: Lista kontrolna atrybutów zabezpieczeń do oceny usług platformy Azure
services: security
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: security
ms.subservice: security-fundamentals
ms.topic: conceptual
ms.date: 07/11/2019
ms.author: mbaldwin
ms.openlocfilehash: 43eb7e5c4cab722eb97f9e2fe819c9c79bae45d9
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68828766"
---
# <a name="security-attributes-for-azure-services"></a>Atrybuty zabezpieczeń dla usług platformy Azure

W tym artykule zebrano atrybuty zabezpieczeń dla wybranych usług platformy Azure. Atrybut zabezpieczeń jest jakością lub funkcją usługi platformy Azure. Przyczynia się to do zdolności usługi do zapobiegania, wykrywania i reagowania na luki w zabezpieczeniach.

Atrybuty zabezpieczeń są klasyfikowane jako:
* Zapobiegawczej
* Segmentacja sieci
* Wykrywanie
* Obsługa zarządzania tożsamościami i dostępem
* Dziennik inspekcji
* Kontrole dostępu (jeśli są używane)
* Zarządzanie konfiguracją (jeśli jest używana)

W każdej kategorii pokazujemy wartość "tak" lub "nie", aby wskazać, czy atrybut jest używany. W przypadku niektórych usług pokazujemy "N/A" dla atrybutu, który nie ma zastosowania. Możemy również podać komentarz lub link do dodatkowych informacji na temat atrybutu.

## <a name="api-managementazureapi-managementapi-management-security-attributes"></a>[API Management](/azure/api-management/api-management-security-attributes)

### <a name="preventative"></a>Zapobiegawczej

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie w spoczynku (takie jak szyfrowanie po stronie serwera, szyfrowanie po stronie serwera z kluczami zarządzanymi przez klienta i inne funkcje szyfrowania) | Dane poufne, takie jak certyfikaty, klucze i wartości nazwane tajne, są szyfrowane przy użyciu zarządzanych przez usługę usług według kluczy wystąpienia usługi. |
| Szyfrowanie podczas przesyłania (takie jak szyfrowanie ExpressRoute, szyfrowanie sieci wirtualnej i szyfrowanie sieci wirtualnej) | Tak | Usługa [Express Route](/azure/expressroute/index) i szyfrowanie sieci wirtualnej są udostępniane przez [Sieć Azure](/azure/virtual-network/index). |
| Obsługa kluczy szyfrowania (CMK, BYOK itp.)| Nie | Wszystkie klucze szyfrowania są dla każdego wystąpienia usługi i są zarządzane przez usługę. |
| Szyfrowanie na poziomie kolumny (Data Services platformy Azure)| ND | |
| Wywołania interfejsu API są szyfrowane| Tak | Wywołania płaszczyzny zarządzania są nawiązywane za pośrednictwem [Azure Resource Manager](/azure/azure-resource-manager/index) za pośrednictwem protokołu TLS. Wymagany jest prawidłowy token sieci Web JSON (JWT).  Wywołania płaszczyzny danych mogą być zabezpieczone przy użyciu protokołu TLS i jednego z obsługiwanych mechanizmów uwierzytelniania (na przykład certyfikatu klienta lub tokenu JWT).
 |

### <a name="network-segmentation"></a>Segmentacja sieci

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Obsługa punktów końcowych usługi| Nie | |
| Obsługa iniekcji sieci wirtualnej| Tak | |
| Izolacja sieci i obsługa zapór| Tak | Odpowiednio przy użyciu grup zabezpieczeń sieci (sieciowej grupy zabezpieczeń) i platformy Application Gateway Azure (lub innego oprogramowania). |
| Obsługa tunelowania wymuszonego| Tak | Sieć Azure udostępnia tunelowanie wymuszone. |

### <a name="detection"></a>Wykrywanie

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Pomoc techniczna dotycząca monitorowania platformy Azure (log Analytics, App Insights itp.)| Tak | |

### <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Authentication| Tak | |
| Authorization| Tak | |


### <a name="audit-trail"></a>Dziennik inspekcji

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Rejestrowanie i inspekcja płaszczyzny kontroli i zarządzania| Tak | [Azure Monitor dzienników aktywności](/azure/azure-monitor/platform/activity-logs-overview) |
| Rejestrowanie i inspekcja płaszczyzny danych| Yes | [Azure monitor dzienniki diagnostyczne](/azure/azure-monitor/platform/diagnostic-logs-overview) i (opcjonalnie) [Application Insights platformy Azure](/azure/azure-monitor/app/app-insights-overview).|

### <a name="configuration-management"></a>Zarządzanie konfiguracją

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracją (wersja konfiguracji itp.)| Tak | Korzystanie z [zestawu Resource Kit usługi Azure API Management DevOps](https://aka.ms/apimdevops) |

### <a name="vulnerability-scans-false-positives"></a>Luka w zabezpieczeniach skanowania fałszywych pozytywów

W tej części udokumentowano typowe luki w zabezpieczeniach, które nie wpływają na API Management platformy Azure.

| Luka w zabezpieczeniach               | Opis                                                                                                                                                                                                                                                                                                               |
|-----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ticketbleed (CVE-2016-9244) | Ticketbleed jest luką w zabezpieczeniach w implementacji rozszerzenia SessionTicket protokołu TLS znalezionego w niektórych F5. Pozwala na wycieki ("wykrwawinie") do 31 bajtów danych z niezainicjowanej pamięci. Jest to spowodowane tym, że stos TLS uzupełnia identyfikator sesji, który został przesłany przez klienta z danymi, aby zapewnić 32 bitów. |


## <a name="app-serviceazureapp-serviceapp-service-security-attributes"></a>[App Service](/azure/app-service/app-service-security-attributes)

### <a name="preventative"></a>Zapobiegawczej

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie w spoczynku (takie jak szyfrowanie po stronie serwera, szyfrowanie po stronie serwera z kluczami zarządzanymi przez klienta i inne funkcje szyfrowania) | Tak | Zawartość pliku witryny sieci Web jest przechowywana w usłudze Azure Storage, która automatycznie szyfruje zawartość w stanie spoczynku. Zobacz [szyfrowanie usługi Azure Storage dla danych przechowywanych w spoczynku](/azure/storage/common/storage-service-encryption).<br><br>Klucze tajne dostarczone przez klienta są szyfrowane w stanie spoczynku. Wpisy tajne są szyfrowane w stanie spoczynku, podczas gdy są przechowywane w App Service bazach danych konfiguracji.<br><br>Opcjonalnie dyski dołączone lokalnie mogą być używane jako magazyn tymczasowy przez witryny sieci Web (D:\Local i% TMP%). Dyski dołączone lokalnie nie są szyfrowane w stanie spoczynku. |
| Szyfrowanie podczas przesyłania (takie jak szyfrowanie ExpressRoute, szyfrowanie sieci wirtualnej i szyfrowanie sieci wirtualnej) | Yes | Klienci mogą konfigurować witryny sieci Web tak, aby wymagały protokołu HTTPS i używać go do obsługi ruchu przychodzącego. Zapoznaj się z wpisem w blogu [jak utworzyć Azure App Service tylko https](https://blogs.msdn.microsoft.com/benjaminperkins/2017/11/30/how-to-make-an-azure-app-service-https-only/). |
| Obsługa kluczy szyfrowania (CMK, BYOK itp.)| Tak | Klienci mogą wybrać przechowywanie wpisów tajnych aplikacji w Key Vault i pobrać je w czasie wykonywania. Zobacz [używanie Key Vault odwołań do App Service i Azure Functions (wersja zapoznawcza)](/azure/app-service/app-service-key-vault-references).|
| Szyfrowanie na poziomie kolumny (Data Services platformy Azure)| ND | |
| Wywołania interfejsu API są szyfrowane| Tak | Wywołania zarządzania w celu skonfigurowania App Service występują za pośrednictwem [Azure Resource Manager](/azure/azure-resource-manager/index) wywołań za pośrednictwem protokołu HTTPS. |

### <a name="network-segmentation"></a>Segmentacja sieci

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Obsługa punktów końcowych usługi| Tak | Obecnie dostępne w wersji zapoznawczej dla App Service. Zobacz [ograniczenia dostępu Azure App Service](/azure/app-service/app-service-ip-restrictions). |
| Obsługa iniekcji sieci wirtualnej| Tak | Środowiska App Service są prywatnymi implementacjami App Service przeznaczonymi dla pojedynczego klienta, które zostały dodane do sieci wirtualnej klienta. Zobacz [wprowadzenie do środowisk App Service](/azure/app-service/environment/intro). |
| Izolacja sieci i obsługa zapór| Yes | W przypadku publicznej App Service odmiany wielodostępnej wielu dzierżawców klienci mogą konfigurować listy ACL sieci (ograniczenia adresów IP), aby zablokować dozwolony ruch przychodzący.  Zobacz [ograniczenia dostępu Azure App Service](/azure/app-service/app-service-ip-restrictions).  Środowiska App Service są wdrażane bezpośrednio w sieciach wirtualnych, dlatego można je zabezpieczyć za pomocą sieciowych grup zabezpieczeń. |
| Obsługa tunelowania wymuszonego| Tak | Środowiska App Service można wdrożyć w sieci wirtualnej klienta, w której jest skonfigurowany tunelowanie wymuszone. Klienci muszą postępować zgodnie z instrukcjami w temacie [konfigurowanie App Service Environment przy użyciu wymuszonego tunelowania](/azure/app-service/environment/forced-tunnel-support). |

### <a name="detection"></a>Wykrywanie

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Pomoc techniczna dotycząca monitorowania platformy Azure (log Analytics, App Insights itp.)| Tak | App Service integruje się z Application Insights dla języków, które obsługują Application Insights (pełny .NET Framework, .NET Core, Java i Node. JS).  Zobacz [monitorowanie wydajności Azure App Service](/azure/azure-monitor/app/azure-web-apps). App Service również wysyła metryki aplikacji do Azure Monitor. Zobacz [monitorowanie aplikacji w Azure App Service](/azure/app-service/web-sites-monitor). |

### <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Authentication| Yes | Klienci mogą tworzyć aplikacje na App Service, które automatycznie integrują się z [Azure Active Directory (Azure AD)](/azure/active-directory/index) , a także z innymi dostawcami tożsamości zgodnymi z uwierzytelnianiem OAuth. Zobacz [uwierzytelnianie i autoryzacja w Azure App Service](/azure/app-service/overview-authentication-authorization). W celu zarządzania dostępem do zasobów App Service, cały dostęp jest kontrolowany przez kombinację uwierzytelnionego podmiotu zabezpieczeń usługi Azure AD i Azure Resource Manager ról RBAC. |
| Authorization| Tak | W celu zarządzania dostępem do zasobów App Service, cały dostęp jest kontrolowany przez kombinację uwierzytelnionego podmiotu zabezpieczeń usługi Azure AD i Azure Resource Manager ról RBAC.  |


### <a name="audit-trail"></a>Dziennik inspekcji

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Rejestrowanie i inspekcja płaszczyzny kontroli i zarządzania| Yes | Wszystkie operacje zarządzania wykonywane na App Service obiektów odbywają się za pośrednictwem [Azure Resource Manager](/azure/azure-resource-manager/index). Dzienniki historyczne tych operacji są dostępne zarówno w portalu, jak i za pośrednictwem interfejsu wiersza polecenia; Zobacz [Azure Resource Manager operacje dostawcy zasobów](/azure/role-based-access-control/resource-provider-operations#microsoftweb) i [AZ monitor Activity-Log](/cli/azure/monitor/activity-log). |
| Rejestrowanie i inspekcja płaszczyzny danych | Nie | Płaszczyzna danych dla App Service jest zdalnym udziałem plików zawierającym zawartość witryny sieci Web wdrożonej przez klienta.  Nie istnieje Inspekcja zdalnego udziału plików. |

### <a name="configuration-management"></a>Zarządzanie konfiguracją

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracją (wersja konfiguracji itp.)| Yes | W przypadku operacji zarządzania stanem konfiguracji App Service można wyeksportować jako szablon Azure Resource Manager i wersji w czasie. W przypadku operacji środowiska uruchomieniowego klienci mogą obsługiwać wiele różnych wersji na żywo aplikacji przy użyciu funkcji App Service miejsc wdrożenia. | 



## <a name="azure-resource-managerazureazure-resource-managerazure-resource-manager-security-attributes"></a>[Azure Resource Manager](/azure/azure-resource-manager/azure-resource-manager-security-attributes)

### <a name="preventative"></a>Zapobiegawczej

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie w spoczynku (takie jak szyfrowanie po stronie serwera, szyfrowanie po stronie serwera z kluczami zarządzanymi przez klienta i inne funkcje szyfrowania) | Yes |  |
| Szyfrowanie podczas przesyłania (takie jak szyfrowanie ExpressRoute, szyfrowanie sieci wirtualnej i szyfrowanie sieci wirtualnej) | Yes | HTTPS/TLS. |
| Obsługa kluczy szyfrowania (CMK, BYOK itp.)| ND | Azure Resource Manager nie przechowuje zawartości klienta, tylko kontrolują dane. |
| Szyfrowanie na poziomie kolumny (Data Services platformy Azure)| Yes | |
| Wywołania interfejsu API są szyfrowane| Tak | |

### <a name="network-segmentation"></a>Segmentacja sieci

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Obsługa punktów końcowych usługi| Nie | |
| Obsługa iniekcji sieci wirtualnej| Tak | |
| Izolacja sieci i obsługa zapór| Nie |  |
| Obsługa tunelowania wymuszonego| Nie |  |

### <a name="detection"></a>Wykrywanie

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Pomoc techniczna dotycząca monitorowania platformy Azure (log Analytics, App Insights itp.)| Nie | |

### <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Authentication| Tak | Na podstawie [Azure Active Directory](/azure/active-directory) .|
| Authorization| Tak | |


### <a name="audit-trail"></a>Dziennik inspekcji

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Rejestrowanie i inspekcja płaszczyzny kontroli i zarządzania| Yes | Dzienniki aktywności uwidaczniają wszystkie operacje zapisu (PUT, POST, DELETE) wykonywane na zasobach; Zobacz [Wyświetlanie dzienników aktywności w celu inspekcji akcji na zasobach](/azure/azure-resource-manager/resource-group-audit). |
| Rejestrowanie i inspekcja płaszczyzny danych| ND | |

### <a name="configuration-management"></a>Zarządzanie konfiguracją

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracją (wersja konfiguracji itp.)| Yes |  |


## <a name="azure-backupazurebackupbackup-security-attributes"></a>[Azure Backup](/azure/backup/backup-security-attributes)

### <a name="preventative"></a>Zapobiegawczej

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie w spoczynku (takie jak szyfrowanie po stronie serwera, szyfrowanie po stronie serwera z kluczami zarządzanymi przez klienta i inne funkcje szyfrowania)| Tak | Korzystanie z szyfrowania usługi Storage dla kont magazynu. |
| Szyfrowanie podczas przesyłania (takie jak szyfrowanie ExpressRoute, szyfrowanie sieci wirtualnej i szyfrowanie sieci wirtualnej) | Nie | Przy użyciu protokołu HTTPS. |
| Obsługa kluczy szyfrowania (CMK, BYOK itp.)| Nie |  |
| Szyfrowanie na poziomie kolumny (Data Services platformy Azure)| Nie |  |
| Wywołania interfejsu API są szyfrowane| Tak |  |

### <a name="network-segmentation"></a>Segmentacja sieci

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Obsługa punktów końcowych usługi| Nie |  |
| Obsługa iniekcji sieci wirtualnej| Nie |  |
| Izolacja sieci i obsługa zapór| Yes | Wymuszone tunelowanie jest obsługiwane w przypadku kopii zapasowej maszyny wirtualnej. Wymuszone tunelowanie nie jest obsługiwane w przypadku obciążeń uruchamianych wewnątrz maszyn wirtualnych. |
| Obsługa tunelowania wymuszonego| Nie |  |

### <a name="detection"></a>Wykrywanie

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Pomoc techniczna dotycząca monitorowania platformy Azure (log Analytics, App Insights itp.)| Tak | Log Analytics jest obsługiwana za pośrednictwem dzienników diagnostycznych. Aby uzyskać więcej informacji, zobacz [monitorowanie Azure Backup chronionych obciążeń przy użyciu log Analytics](https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/) . |

### <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Authentication| Tak | Uwierzytelnianie odbywa się za Azure Active Directory. |
| Authorization| Tak | Są używane wbudowane role RBAC klienta. Aby uzyskać więcej informacji, zobacz temat [używanie Access Control opartych na rolach w celu zarządzania Azure Backup punktów odzyskiwania](/azure/backup/backup-rbac-rs-vault) . |


### <a name="audit-trail"></a>Dziennik inspekcji

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Rejestrowanie i inspekcja płaszczyzny kontroli i zarządzania| Tak | Wszystkie akcje wyzwalane przez klienta z Azure Portal są rejestrowane w dziennikach aktywności. |
| Rejestrowanie i inspekcja płaszczyzny danych| Nie | Nie można bezpośrednio połączyć Azure Backup płaszczyzny danych.  |

### <a name="configuration-management"></a>Zarządzanie konfiguracją

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracją (wersja konfiguracji itp.)| Yes|  |

## <a name="cosmos-dbazurecosmos-dbcosmos-db-security-attributes"></a>[Cosmos DB](/azure/cosmos-db/cosmos-db-security-attributes)

### <a name="preventative"></a>Zapobiegawczej

| Atrybut zabezpieczeń | Tak/nie | Uwagi |
|---|---|--|
| Szyfrowanie w spoczynku (takie jak szyfrowanie po stronie serwera, szyfrowanie po stronie serwera z kluczami zarządzanymi przez klienta i inne funkcje szyfrowania) | Tak | Wszystkie Cosmos DB bazy danych i kopie zapasowe są domyślnie szyfrowane; Zobacz [szyfrowanie danych w Azure Cosmos DB](/azure/cosmos-db/database-encryption-at-rest). Szyfrowanie po stronie serwera z kluczami zarządzanymi przez klienta nie jest obsługiwane. |
| Szyfrowanie podczas przesyłania (takie jak szyfrowanie ExpressRoute, szyfrowanie sieci wirtualnej i szyfrowanie sieci wirtualnej)| Tak | Wszystkie dane Azure Cosmos DB są szyfrowane podczas przesyłania. |
| Obsługa kluczy szyfrowania (CMK, BYOK itp.)| Nie |  |
| Szyfrowanie na poziomie kolumny (Data Services platformy Azure)| Yes | Tylko w interfejsie API tabel Premium. Nie wszystkie interfejsy API obsługują tę funkcję. Zobacz [wprowadzenie do Azure Cosmos DB: Interfejs API tabel](/azure/cosmos-db/table-introduction). |
| Wywołania interfejsu API są szyfrowane| Tak | Wszystkie połączenia do Azure Cosmos DB obsługują protokół HTTPS. Azure Cosmos DB obsługuje również połączenia TLS 1,2, ale nie jest to jeszcze wymuszane. Jeśli klienci wyłączają niższy poziom protokołu TLS na swoich końcach, mogą się upewnić, że nawiąże połączenie z Cosmos DB.  |

### <a name="network-segmentation"></a>Segmentacja sieci

| Atrybut zabezpieczeń | Tak/nie | Uwagi |
|---|---|--|
| Obsługa punktów końcowych usługi| Tak |  |
| Obsługa iniekcji sieci wirtualnej| Yes | Za pomocą punktu końcowego usługi sieci wirtualnej można skonfigurować konto Azure Cosmos DB, aby zezwalać na dostęp tylko z określonej podsieci sieci wirtualnej (VNet). Możesz również połączyć dostęp do sieci wirtualnej przy użyciu reguł zapory.  Zobacz [dostęp Azure Cosmos DB z sieci wirtualnych](/azure/cosmos-db/vnet-service-endpoint). |
| Izolacja sieci i obsługa zapór| Tak | Dzięki obsłudze zapory można skonfigurować konto usługi Azure Cosmos, aby zezwalać na dostęp tylko z zatwierdzonego zestawu adresów IP, zakresu adresów IP i/lub usług w chmurze. Zobacz [Konfigurowanie zapory IP w Azure Cosmos DB](/azure/cosmos-db/how-to-configure-firewall).|
| Obsługa tunelowania wymuszonego| Tak | Można skonfigurować po stronie klienta w sieci wirtualnej, w której znajdują się maszyny wirtualne.   |

### <a name="detection"></a>Wykrywanie

| Atrybut zabezpieczeń | Tak/nie | Uwagi|
|---|---|--|
| Pomoc techniczna dotycząca monitorowania platformy Azure (log Analytics, App Insights itp.)| Yes | Wszystkie żądania wysyłane do Azure Cosmos DB są rejestrowane. [Monitorowanie platformy](/azure/azure-monitor/overview)Azure, metryki platformy Azure, rejestrowanie inspekcji platformy Azure jest obsługiwane.  Można rejestrować informacje odpowiadające na żądania płaszczyzny danych, statystyki środowiska uruchomieniowego zapytań, tekst zapytania, żądania MongoDB. Możesz również skonfigurować alerty. |

### <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem

| Atrybut zabezpieczeń | Tak/nie | Uwagi|
|---|---|--|
| Authentication| Yes | Tak na poziomie konta bazy danych; na poziomie płaszczyzny danych Cosmos DB używa tokenów zasobów i dostępu do kluczy. |
| Authorization| Tak | Obsługiwane na koncie usługi Azure Cosmos z kluczami głównymi (podstawowymi i pomocniczymi) i tokenami zasobów. Możesz uzyskać dostęp do odczytu/zapisu lub tylko do odczytu do danych z kluczami głównymi. Tokeny zasobów umożliwiają ograniczony dostęp do zasobów, takich jak dokumenty i kontenery. |

### <a name="audit-trail"></a>Dziennik inspekcji

| Atrybut zabezpieczeń | Tak/nie | Uwagi|
|---|---|--|
| Rejestrowanie i inspekcja płaszczyzny kontroli i zarządzania| Yes | Dziennik aktywności platformy Azure dla operacji na poziomie konta, takich jak zapory, sieci wirtualnych, dostęp do kluczy i IAM. |
| Rejestrowanie i inspekcja płaszczyzny danych | Tak | Rejestrowanie diagnostyczne monitorowania dla operacji na poziomie kontenera, takich jak tworzenie kontenerów, obsługa przepływności, zasady indeksowania i operacje CRUD na dokumentach. |

### <a name="configuration-management"></a>Zarządzanie konfiguracją

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracją (wersja konfiguracji itp.)| Nie  | | 

### <a name="additional-security-attributes-for-cosmos-db"></a>Dodatkowe atrybuty zabezpieczeń dla Cosmos DB

| Atrybut zabezpieczeń | Tak/nie | Uwagi|
|---|---|--|
| Współużytkowanie zasobów między źródłami (CORS) | Tak | Zobacz [Konfigurowanie udostępniania zasobów między źródłami (CORS)](/azure/cosmos-db/how-to-configure-cross-origin-resource-sharing). |


## <a name="event-hubsazureevent-hubsevent-hubs-security-attributes"></a>[Event Hubs](/azure/event-hubs/event-hubs-security-attributes)

### <a name="preventative"></a>Zapobiegawczej

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie w spoczynku (takie jak szyfrowanie po stronie serwera, szyfrowanie po stronie serwera z kluczami zarządzanymi przez klienta i inne funkcje szyfrowania) |  Tak | |
| Szyfrowanie podczas przesyłania (takie jak szyfrowanie ExpressRoute, szyfrowanie sieci wirtualnej i szyfrowanie sieci wirtualnej) | Tak | |
| Obsługa kluczy szyfrowania (CMK, BYOK itp.)| Nie |  |
| Szyfrowanie na poziomie kolumny (Data Services platformy Azure)| ND | |
| Wywołania interfejsu API są szyfrowane| Tak |  |

### <a name="network-segmentation"></a>Segmentacja sieci

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Obsługa punktów końcowych usługi| Tak |  |
| Obsługa iniekcji sieci wirtualnej| Nie | |
| Izolacja sieci i obsługa zapór| Tak |  |
| Obsługa tunelowania wymuszonego| Nie |  |

### <a name="detection"></a>Wykrywanie

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Pomoc techniczna dotycząca monitorowania platformy Azure (log Analytics, App Insights itp.)| Tak | |

### <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Authentication| Yes | |
| Authorization|  Yes | |


### <a name="audit-trail"></a>Dziennik inspekcji

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Rejestrowanie i inspekcja płaszczyzny kontroli i zarządzania| Yes |  |
| Rejestrowanie i inspekcja płaszczyzny danych| Tak |   |

### <a name="configuration-management"></a>Zarządzanie konfiguracją

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracją (wersja konfiguracji itp.)| Yes | |


## <a name="expressrouteazureexpressrouteexpressroute-security-attributes"></a>[ExpressRoute](/azure/expressroute/expressroute-security-attributes)

### <a name="preventative"></a>Zapobiegawczej

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie w spoczynku (takie jak szyfrowanie po stronie serwera, szyfrowanie po stronie serwera z kluczami zarządzanymi przez klienta i inne funkcje szyfrowania) |  ND | ExpressRoute nie przechowuje danych klienta. |
| Szyfrowanie podczas przesyłania (takie jak szyfrowanie ExpressRoute, szyfrowanie sieci wirtualnej i szyfrowanie sieci wirtualnej) | Nie | |
| Obsługa kluczy szyfrowania (CMK, BYOK itp.)| ND |  |
| Szyfrowanie na poziomie kolumny (Data Services platformy Azure)| ND | |
| Wywołania interfejsu API są szyfrowane| Tak | Za pośrednictwem [Azure Resource Manager](/azure/azure-resource-manager/index) i https. |

### <a name="network-segmentation"></a>Segmentacja sieci

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Obsługa punktów końcowych usługi| ND |  |
| Obsługa iniekcji sieci wirtualnej| ND | |
| Izolacja sieci i obsługa zapór| Tak | Każdy klient jest zawarty we własnej domenie routingu i tunelowany do własnej sieci wirtualnej |
| Obsługa tunelowania wymuszonego| ND | Za pośrednictwem Border Gateway Protocol (BGP). |

### <a name="detection"></a>Wykrywanie

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Pomoc techniczna dotycząca monitorowania platformy Azure (log Analytics, App Insights itp.)| Tak | Zobacz [monitorowanie ExpressRoute, metryki i alerty](/azure/expressroute/expressroute-monitoring-metrics-alerts).|

### <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Authentication| Yes | Konto usługi dla bramy dla firmy Microsoft (GWM) (kontroler); Dostęp just in Time (JIT) do deweloperów i operacji. |
| Authorization|  Tak |Konto usługi dla bramy dla firmy Microsoft (GWM) (kontroler); Dostęp just in Time (JIT) do deweloperów i operacji. |


### <a name="audit-trail"></a>Dziennik inspekcji

| Atrybut zabezpieczeń | Tak/Nie | Uwagi| 
|---|---|--|
| Rejestrowanie i inspekcja płaszczyzny kontroli i zarządzania| Tak |  |
| Rejestrowanie i inspekcja płaszczyzny danych| Nie |   |

### <a name="configuration-management"></a>Zarządzanie konfiguracją

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracją (wersja konfiguracji itp.)| Yes | Za pośrednictwem dostawcy zasobów sieciowych (NRP). |


## <a name="key-vaultkey-vaultkey-vault-security-attributesmd"></a>[Usługa Key Vault](../../key-vault/key-vault-security-attributes.md)

### <a name="preventative"></a>Zapobiegawczej

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie w spoczynku (takie jak szyfrowanie po stronie serwera, szyfrowanie po stronie serwera z kluczami zarządzanymi przez klienta i inne funkcje szyfrowania) | Tak | Wszystkie obiekty są zaszyfrowane. |
| Szyfrowanie podczas przesyłania (takie jak szyfrowanie ExpressRoute, szyfrowanie sieci wirtualnej i szyfrowanie sieci wirtualnej) | Yes | Cała komunikacja odbywa się za pośrednictwem szyfrowanych wywołań interfejsu API |
| Obsługa kluczy szyfrowania (CMK, BYOK itp.)| Yes | Klient kontroluje wszystkie klucze w Key Vault. Gdy są określone klucze z zabezpieczeniami sprzętowego modułu zabezpieczeń (HSM), moduł HSM poziomu 2 trybu FIPS chroni klucz, certyfikat lub wpis tajny. |
| Szyfrowanie na poziomie kolumny (Data Services platformy Azure)| ND |  |
| Wywołania interfejsu API są szyfrowane| Tak | Przy użyciu protokołu HTTPS. |

### <a name="network-segmentation"></a>Segmentacja sieci

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Obsługa punktów końcowych usługi| Tak | Korzystanie z punktów końcowych usługi Virtual Network (VNet). |
| Obsługa iniekcji sieci wirtualnej| Nie |  |
| Izolacja sieci i obsługa zapór| Tak | Korzystanie z reguł zapory sieci wirtualnej. |
| Obsługa tunelowania wymuszonego| Nie |  |

### <a name="detection"></a>Wykrywanie

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Pomoc techniczna dotycząca monitorowania platformy Azure (log Analytics, App Insights itp.)| Yes | Przy użyciu Log Analytics. |

### <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Authentication| Tak | Uwierzytelnianie odbywa się za Azure Active Directory. |
| Authorization| Yes | Korzystanie z zasad dostępu Key Vault. |

### <a name="audit-trail"></a>Dziennik inspekcji

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Rejestrowanie i inspekcja w płaszczyźnie kontroli/zarządzania| Tak | Przy użyciu Log Analytics. |
| Rejestrowanie i inspekcja płaszczyzny danych| Yes | Przy użyciu Log Analytics. |

### <a name="access-controls"></a>Kontrole dostępu

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Kontrole dostępu do płaszczyzny kontroli/zarządzania | Tak | Kontrola dostępu oparta na rolach (RBAC) przy użyciu usługi Azure Resource Manager |
| Kontrola dostępu do płaszczyzny danych (na każdym poziomie usługi) | Tak | Zasady dostępu Key Vault |

## <a name="load-balancerload-balancerload-balancer-security-attributesmd"></a>[Load Balancer](../../load-balancer/load-balancer-security-attributes.md)

### <a name="preventative"></a>Zapobiegawczej

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie w spoczynku (takie jak szyfrowanie po stronie serwera, szyfrowanie po stronie serwera z kluczami zarządzanymi przez klienta i inne funkcje szyfrowania) | ND | |
| Szyfrowanie podczas przesyłania (takie jak szyfrowanie ExpressRoute, szyfrowanie sieci wirtualnej i szyfrowanie sieci wirtualnej)| ND | |
| Obsługa kluczy szyfrowania (CMK, BYOK itp.)| ND | |
| Szyfrowanie na poziomie kolumny (Data Services platformy Azure)| ND | |
| Wywołania interfejsu API są szyfrowane| Tak | Za pośrednictwem [Azure Resource Manager](/azure/azure-resource-manager/index). |

### <a name="network-segmentation"></a>Segmentacja sieci

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Obsługa punktów końcowych usługi| ND | |
| Obsługa iniekcji sieci wirtualnej| ND | . |
| Izolacja sieci i obsługa zapór| ND |  |
| Obsługa tunelowania wymuszonego| ND | |

### <a name="detection"></a>Wykrywanie

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Pomoc techniczna dotycząca monitorowania platformy Azure (log Analytics, App Insights itp.)| Yes | Zapoznaj się z [dziennikami Azure monitor dla publicznych Load Balancer podstawowych](/azure/load-balancer/load-balancer-monitor-log). |

### <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Authentication| ND |  |
| Authorization| ND |  |

### <a name="audit-trail"></a>Dziennik inspekcji

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Rejestrowanie i inspekcja płaszczyzny kontroli i zarządzania| Tak | Zapoznaj się z [dziennikami Azure monitor dla publicznych Load Balancer podstawowych](/azure/load-balancer/load-balancer-monitor-log). |
| Rejestrowanie i inspekcja płaszczyzny danych | ND |  |

### <a name="configuration-management"></a>Zarządzanie konfiguracją

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracją (wersja konfiguracji itp.)| ND |  | 

## <a name="service-bus-messagingazureservice-bus-messagingservice-bus-messaging-security-attributes"></a>[Service Bus Messaging](/azure/service-bus-messaging/service-bus-messaging-security-attributes)

### <a name="preventative"></a>Zapobiegawczej

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie w spoczynku (takie jak szyfrowanie po stronie serwera, szyfrowanie po stronie serwera z kluczami zarządzanymi przez klienta i inne funkcje szyfrowania) |  Tak w przypadku szyfrowania po stronie serwera domyślnie. | Klucze zarządzane przez klienta i BYOK nie są jeszcze obsługiwane. Szyfrowanie po stronie klienta jest odpowiedzialnością klienta |
| Szyfrowanie podczas przesyłania (takie jak szyfrowanie ExpressRoute, szyfrowanie sieci wirtualnej i szyfrowanie sieci wirtualnej) | Yes | Obsługuje standardowy mechanizm HTTPS/TLS. |
| Obsługa kluczy szyfrowania (CMK, BYOK itp.)| Nie |   |
| Szyfrowanie na poziomie kolumny (Data Services platformy Azure)| ND | |
| Wywołania interfejsu API są szyfrowane| Tak | Wywołania interfejsu API są nawiązywane za pośrednictwem [Azure Resource Manager](/azure/azure-resource-manager/index) i https. |

### <a name="network-segmentation"></a>Segmentacja sieci

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Obsługa punktów końcowych usługi| Tak (tylko warstwa Premium) | Punkty końcowe usługi sieci wirtualnej są obsługiwane tylko w przypadku [Service Bus warstwy Premium](/azure/service-bus-messaging/service-bus-premium-messaging) . |
| Obsługa iniekcji sieci wirtualnej| Nie | |
| Izolacja sieci i obsługa zapór| Tak (tylko warstwa Premium) |  |
| Obsługa tunelowania wymuszonego| Nie |  |

### <a name="detection"></a>Wykrywanie

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Pomoc techniczna dotycząca monitorowania platformy Azure (log Analytics, App Insights itp.)| Tak | Obsługiwane za pośrednictwem [Azure monitor i alertów](/azure/service-bus-messaging/service-bus-metrics-azure-monitor). |

### <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Authentication| Tak | Zarządzane przez [Azure Active Directory tożsamość usługi zarządzanej](/azure/service-bus-messaging/service-bus-managed-service-identity); Zobacz [Service Bus uwierzytelnianie i autoryzacja](/azure/service-bus-messaging/service-bus-authentication-and-authorization).|
| Authorization| Tak | Obsługuje autoryzację za pośrednictwem [RBAC](/azure/service-bus-messaging/service-bus-role-based-access-control) (wersja zapoznawcza) i token SAS; Zobacz [Service Bus uwierzytelnianie i autoryzacja](/azure/service-bus-messaging/service-bus-authentication-and-authorization). |


### <a name="audit-trail"></a>Dziennik inspekcji

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Rejestrowanie i inspekcja płaszczyzny kontroli i zarządzania| Yes | Dzienniki operacji są dostępne; Zobacz [Service Bus dzienników diagnostycznych](/azure/service-bus-messaging/service-bus-diagnostic-logs).  |
| Rejestrowanie i inspekcja płaszczyzny danych| Nie |  |

### <a name="configuration-management"></a>Zarządzanie konfiguracją

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracją (wersja konfiguracji itp.)| Tak | Obsługuje przechowywanie wersji dostawcy zasobów za pomocą [interfejsu API Azure Resource Manager](/rest/api/resources/).|


## <a name="service-bus-relayazureservice-bus-relayservice-bus-relay-security-attributes"></a>[Service Bus Relay](/azure/service-bus-relay/service-bus-relay-security-attributes)

### <a name="preventative"></a>Zapobiegawczej

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie w spoczynku (takie jak szyfrowanie po stronie serwera, szyfrowanie po stronie serwera z kluczami zarządzanymi przez klienta i inne funkcje szyfrowania) |  ND | Przekaźnik jest gniazdem internetowym i nie utrzymuje danych. |
| Szyfrowanie podczas przesyłania (takie jak szyfrowanie ExpressRoute, szyfrowanie sieci wirtualnej i szyfrowanie sieci wirtualnej) | Tak | Usługa wymaga protokołu TLS. |
| Obsługa kluczy szyfrowania (CMK, BYOK itp.)| Nie | Używa tylko certyfikatów Microsoft TLS.  |
| Szyfrowanie na poziomie kolumny (Data Services platformy Azure)| ND | |
| Wywołania interfejsu API są szyfrowane| Tak | HTTPS. |

### <a name="network-segmentation"></a>Segmentacja sieci

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Obsługa punktów końcowych usługi| Nie |  |
| Izolacja sieci i obsługa zapór| Nie |  |
| Obsługa tunelowania wymuszonego| ND | Przekaźnik jest tunelem TLS  |

### <a name="detection"></a>Wykrywanie

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Pomoc techniczna dotycząca monitorowania platformy Azure (log Analytics, App Insights itp.)| Yes | |

### <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Authentication| Tak | Za pośrednictwem SAS. |
| Authorization|  Tak | Za pośrednictwem SAS. |

### <a name="audit-trail"></a>Dziennik inspekcji

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Rejestrowanie i inspekcja płaszczyzny kontroli i zarządzania| Tak | Za [Azure Resource Manager](/azure/azure-resource-manager/index). |
| Rejestrowanie i inspekcja płaszczyzny danych| Yes | Powodzenie/Niepowodzenie połączenia i błędy oraz zarejestrowane.  |

### <a name="configuration-management"></a>Zarządzanie konfiguracją

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracją (wersja konfiguracji itp.)| Tak | Za [Azure Resource Manager](/azure/azure-resource-manager/index).|

## <a name="service-fabricservice-fabricservice-fabric-security-attributesmd"></a>[Service Fabric](../../service-fabric/service-fabric-security-attributes.md)

### <a name="preventative"></a>Zapobiegawczej

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie w spoczynku (takie jak szyfrowanie po stronie serwera, szyfrowanie po stronie serwera z kluczami zarządzanymi przez klienta i inne funkcje szyfrowania) | Yes | Klient jest właścicielem klastra i zestawu skalowania maszyn wirtualnych, na którym został skompilowany klaster. Usługę Azure Disk Encryption można włączyć na zestawie skalowania maszyn wirtualnych. |
| Szyfrowanie podczas przesyłania (takie jak szyfrowanie ExpressRoute, szyfrowanie sieci wirtualnej i szyfrowanie sieci wirtualnej) | Tak | Klient jest właścicielem klastra i zestawu skalowania maszyn wirtualnych, na którym został skompilowany klaster. Usługę Azure Disk Encryption można włączyć na zestawie skalowania maszyn wirtualnych. |
| Szyfrowanie na poziomie kolumny (Data Services platformy Azure)| ND |  |
| Wywołania interfejsu API są szyfrowane| Tak | Wywołania interfejsu API Service Fabric są nawiązywane przez Azure Resource Manager. Wymagany jest prawidłowy token sieci Web JSON (JWT). |

### <a name="network-segmentation"></a>Segmentacja sieci

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Obsługa punktów końcowych usługi| Yes |  |
| Obsługa iniekcji sieci wirtualnej| Yes |  |
| Izolacja sieci i obsługa zapór| Tak | Korzystanie z sieciowych grup zabezpieczeń (sieciowej grupy zabezpieczeń). |
| Obsługa tunelowania wymuszonego| Tak | Sieć Azure udostępnia tunelowanie wymuszone. |

### <a name="detection"></a>Wykrywanie

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Pomoc techniczna dotycząca monitorowania platformy Azure (log Analytics, App Insights itp.)| Tak | Korzystanie z pomocy technicznej usługi Azure Monitoring i pomocy technicznej innej firmy. |

### <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Authentication| Tak | Uwierzytelnianie odbywa się za Azure Active Directory. |
| Authorization| Tak | Zarządzanie tożsamościami i dostępem (IAM) dla wywołań za pośrednictwem usługi SFRP. Wywołania bezpośrednio do punktu końcowego klastra obsługują dwie role: Użytkownik i administrator. Klient może mapować interfejsy API na jedną rolę. |

### <a name="audit-trail"></a>Dziennik inspekcji

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Rejestrowanie i inspekcja płaszczyzny kontroli i zarządzania| Tak | Wszystkie operacje płaszczyzny kontroli działają w ramach procesów na potrzeby inspekcji i zatwierdzeń. |
| Rejestrowanie i inspekcja płaszczyzny danych| ND | Klient jest właścicielem klastra.  |

### <a name="configuration-management"></a>Zarządzanie konfiguracją

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracją (wersja konfiguracji itp.)| Tak | |

## <a name="sql-databasesql-databasesql-database-security-attributesmd"></a>[SQL Database](../../sql-database/sql-database-security-attributes.md)

SQL Database obejmuje zarówno [jedną bazę danych](/azure/sql-database/sql-database-single-index) , jak i [wystąpienie zarządzane](/azure/sql-database/sql-database-managed-instance). Poniższe wpisy dotyczą obu ofert, z wyjątkiem sytuacji, w których wskazano inaczej.

### <a name="preventative"></a>Zapobiegawczej

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie w spoczynku (takie jak szyfrowanie po stronie serwera, szyfrowanie po stronie serwera z kluczami zarządzanymi przez klienta i inne funkcje szyfrowania) | Tak | Nazywane "szyfrowaniem w użyciu", zgodnie z opisem w artykule [Always Encrypted](/azure/sql-database/sql-database-always-encrypted). Szyfrowanie po stronie serwera używa [przezroczystego szyfrowania danych](/azure/sql-database/transparent-data-encryption-azure-sql).|
| Szyfrowanie podczas przesyłania (takie jak szyfrowanie ExpressRoute, szyfrowanie sieci wirtualnej i szyfrowanie sieci wirtualnej) | Tak | Przy użyciu protokołu HTTPS. |
| Obsługa kluczy szyfrowania, taka jak CMK lub BYOK| Tak | Oferowana jest obsługa klucza zarządzanego przez usługę i zarządzane przez klienta. Ten drugi jest oferowany w [Azure Key Vault](/azure/key-vault/index). |
| Szyfrowanie na poziomie kolumny udostępniane przez usługi danych platformy Azure| Tak | Za [Always Encrypted](/azure/sql-database/sql-database-always-encrypted). |
| Zaszyfrowane wywołania interfejsu API| Yes | Przy użyciu protokołu HTTPS/SSL. |

### <a name="network-segmentation"></a>Segmentacja sieci

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Obsługa punktów końcowych usługi| Tak | Dotyczy tylko [pojedynczej bazy danych](/azure/sql-database/sql-database-single-index) . |
| Obsługa iniekcji Virtual Network platformy Azure| Tak | Dotyczy tylko [wystąpienia zarządzanego](/azure/sql-database/sql-database-managed-instance) . |
| Izolacja sieci i obsługa zapory| Yes | Zapora zarówno na poziomie bazy danych, jak i na poziomie serwera. Izolacja sieci dotyczy tylko [wystąpienia zarządzanego](/azure/sql-database/sql-database-managed-instance) . |
| Obsługa tunelowania wymuszonego| Tak | [Wystąpienie zarządzane](/azure/sql-database/sql-database-managed-instance) za pośrednictwem sieci VPN [ExpressRoute](/azure/expressroute/index) . |

### <a name="detection"></a>Wykrywanie

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Pomoc techniczna dotycząca monitorowania platformy Azure, taka jak Log Analytics lub Application Insights| Tak | SecureSphere rozwiązanie SIEM z Imperva jest również obsługiwane przez integrację z [usługą Azure Event Hubs](/azure/event-hubs/index) przy użyciu funkcji [inspekcji SQL](/azure/sql-database/sql-database-auditing). |

### <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Authentication| Yes | Azure Active Directory (Azure AD) |
| Authorization| Tak | Brak |

### <a name="audit-trail"></a>Dziennik inspekcji

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Rejestrowanie i inspekcja w płaszczyźnie kontroli i zarządzania| Tak | Tak tylko w przypadku niektórych zdarzeń |
| Rejestrowanie i inspekcja płaszczyzny danych | Yes | Za pośrednictwem [inspekcji SQL](/azure/sql-database/sql-database-auditing) |

### <a name="configuration-management"></a>Zarządzanie konfiguracją

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracją, taka jak wersja konfiguracji| Nie  | Brak |

### <a name="additional-security-attributes-for-sql-database"></a>Dodatkowe atrybuty zabezpieczeń dla SQL Database

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Prewencyjne: Ocena luk w zabezpieczeniach | Yes | Zapoznaj się z tematem usługa oceny luk w zabezpieczeniach w [usłudze SQL Database](/azure/sql-database/sql-vulnerability-assessment). |
| Prewencyjne: odnajdywanie i Klasyfikacja danych  | Tak | Zobacz [Azure SQL Database i SQL Data Warehouse odnajdywania danych & klasyfikację](/azure/sql-database/sql-database-data-discovery-and-classification). |
| Wykrywanie: wykrywanie zagrożeń | Tak | Zobacz [zaawansowaną ochronę przed zagrożeniami dla Azure SQL Database](/azure/sql-database/sql-database-threat-detection-overview). |


## <a name="storageazurestoragecommonstorage-security-attributes"></a>[Storage](/azure/storage/common/storage-security-attributes)

### <a name="preventative"></a>Zapobiegawczej

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie w spoczynku (takie jak szyfrowanie po stronie serwera, szyfrowanie po stronie serwera z kluczami zarządzanymi przez klienta i inne funkcje szyfrowania) | Yes |  |
| Szyfrowanie podczas przesyłania (takie jak szyfrowanie ExpressRoute, szyfrowanie sieci wirtualnej i szyfrowanie sieci wirtualnej) | Yes | Obsługa standardowych mechanizmów protokołu HTTPS/TLS.  Użytkownicy mogą także szyfrować dane przed ich przesłaniem do usługi. |
| Obsługa kluczy szyfrowania (CMK, BYOK itp.)| Tak | Zobacz [szyfrowanie usługi Storage przy użyciu kluczy zarządzanych przez klienta w programie Azure Key Vault](/azure/storage/common/storage-service-encryption-customer-managed-keys?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).|
| Szyfrowanie na poziomie kolumny (Data Services platformy Azure)| ND |  |
| Wywołania interfejsu API są szyfrowane| Tak |  |

### <a name="network-segmentation"></a>Segmentacja sieci

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Obsługa punktów końcowych usługi| Yes |  |
| Obsługa iniekcji sieci wirtualnej| ND |  |
| Izolacja sieci i obsługa zapór| Tak | |
| Obsługa tunelowania wymuszonego| ND |  |

### <a name="detection"></a>Wykrywanie

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Pomoc techniczna dotycząca monitorowania platformy Azure (log Analytics, App Insights itp.)| Tak | Dostępne są teraz metryki Azure Monitor, dzienniki rozpoczynające Podgląd |

### <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Authentication| Yes | Azure Active Directory, klucz współużytkowany, token dostępu współdzielonego. |
| Authorization| Yes | Obsługa autoryzacji za pośrednictwem list kontroli dostępu RBAC, POSIX i tokenów SAS |

### <a name="audit-trail"></a>Dziennik inspekcji

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Rejestrowanie i inspekcja płaszczyzny kontroli i zarządzania | Tak | Azure Resource Manager dziennik aktywności |
| Rejestrowanie i inspekcja płaszczyzny danych| Yes | Dzienniki diagnostyczne usługi i rejestrowanie Azure Monitor rozpoczynające się w wersji zapoznawczej  |

### <a name="configuration-management"></a>Zarządzanie konfiguracją

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracją (wersja konfiguracji itp.)| Yes | Obsługa wersji dostawcy zasobów przy użyciu interfejsów API Azure Resource Manager |

## <a name="virtual-machines-and-virtual-machine-scale-sets"></a>Virtual Machines i Virtual Machine Scale Sets

[Maszyny wirtualne](/azure/virtual-machines/windows/virtual-machines-windows-security-attributes)z systemem Linux[zestawy skalowania maszyn wirtualnych](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-security-attributes)  | [systemu Windows](/azure/virtual-machines/windows/virtual-machines-windows-security-attributes) | 


### <a name="preventative"></a>Zapobiegawczej

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie w spoczynku (takie jak szyfrowanie po stronie serwera, szyfrowanie po stronie serwera z kluczami zarządzanymi przez klienta i inne funkcje szyfrowania) | Yes | Zobacz [jak szyfrować maszynę wirtualną z systemem Linux na platformie Azure](/azure/virtual-machines/linux/encrypt-disks) i [szyfrować dyski wirtualne na maszynie wirtualnej z systemem Windows](/azure/virtual-machines/windows/encrypt-disks). |
| Szyfrowanie podczas przesyłania (takie jak szyfrowanie ExpressRoute, szyfrowanie sieci wirtualnej i szyfrowanie sieci wirtualnej)| Yes | Usługa Azure Virtual Machines obsługuje szyfrowanie [ExpressRoute](/azure/expressroute) i wirtualne. Zobacz [szyfrowanie w trakcie przesyłania na maszynach wirtualnych](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms). |
| Obsługa kluczy szyfrowania (CMK, BYOK itp.)| Tak | Klucze zarządzane przez klienta to obsługiwany scenariusz szyfrowania platformy Azure; Zobacz [Omówienie usługi Azure Encryption](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms).|
| Szyfrowanie na poziomie kolumny (Data Services platformy Azure)| ND | |
| Wywołania interfejsu API są szyfrowane| Tak | Za pośrednictwem protokołów HTTPS i SSL. |

### <a name="network-segmentation"></a>Segmentacja sieci

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Obsługa punktów końcowych usługi| Yes | |
| Obsługa iniekcji sieci wirtualnej| Yes | . |
| Izolacja sieci i obsługa zapór| Yes |  |
| Obsługa tunelowania wymuszonego| Tak | Zobacz [Konfigurowanie wymuszonego tunelowania przy użyciu Azure Resource Manager model wdrażania](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm). |

### <a name="detection"></a>Wykrywanie

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Pomoc techniczna dotycząca monitorowania platformy Azure (log Analytics, App Insights itp.)| Yes | Zobacz [monitorowanie i aktualizowanie maszyny wirtualnej z systemem Linux na platformie Azure](/azure/virtual-machines/linux/tutorial-monitoring) oraz [monitorowanie i aktualizowanie maszyny wirtualnej z systemem Windows na platformie Azure](/azure/virtual-machines/windows/tutorial-monitoring). |

### <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Authentication| Yes |  |
| Authorization| Tak |  |


### <a name="audit-trail"></a>Dziennik inspekcji

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Rejestrowanie i inspekcja płaszczyzny kontroli i zarządzania| Yes |  |
| Rejestrowanie i inspekcja płaszczyzny danych | Nie |  |

### <a name="configuration-management"></a>Zarządzanie konfiguracją

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracją (wersja konfiguracji itp.)| Tak |  | 


## <a name="vpn-gatewayazurevpn-gatewayvpn-gateway-security-attributes"></a>[VPN Gateway](/azure/vpn-gateway/vpn-gateway-security-attributes)

### <a name="preventative"></a>Zapobiegawczej

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie w spoczynku (takie jak szyfrowanie po stronie serwera, szyfrowanie po stronie serwera z kluczami zarządzanymi przez klienta i inne funkcje szyfrowania) | ND | Dane klienta dotyczące tranzytowej bramy sieci VPN nie przechowują danych klienta |
| Szyfrowanie podczas przesyłania (takie jak szyfrowanie ExpressRoute, szyfrowanie sieci wirtualnej i szyfrowanie sieci wirtualnej)| Tak | Usługa VPN Gateway szyfruje pakiety klienta między bramami sieci VPN platformy Azure i lokalnymi urządzeniami sieci VPN (S2S) lub klientami sieci VPN (P2S). Bramy VPN obsługują również szyfrowanie między sieciami wirtualnymi. |
| Obsługa kluczy szyfrowania (CMK, BYOK itp.)| Nie | Klucze wstępne określone przez klienta są szyfrowane w stanie spoczynku; ale nie jest to jeszcze zintegrowane z CMK. |
| Szyfrowanie na poziomie kolumny (Data Services platformy Azure)| ND | |
| Wywołania interfejsu API są szyfrowane| Tak | Za pośrednictwem [Azure Resource Manager](/azure/azure-resource-manager/index) i https  |

### <a name="network-segmentation"></a>Segmentacja sieci

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Obsługa punktów końcowych usługi| ND | |
| Obsługa iniekcji sieci wirtualnej| ND | . |
| Izolacja sieci i obsługa zapór| Tak | Bramy sieci VPN to dedykowane wystąpienia maszyn wirtualnych dla poszczególnych klientów Virtual Network  |
| Obsługa tunelowania wymuszonego| Yes |  |

### <a name="detection"></a>Wykrywanie

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Pomoc techniczna dotycząca monitorowania platformy Azure (log Analytics, App Insights itp.)| Tak | Zobacz [dzienniki/](/azure/vpn-gateway/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log) & alerty diagnostyki Azure monitor[Azure monitor metryki/alerty](/azure/vpn-gateway/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric).  |

### <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Authentication| Tak | [Azure Active Directory](/azure/active-directory/fundamentals/active-directory-whatis) zarządzania usługą i konfigurowania bramy sieci VPN platformy Azure. |
| Authorization| Tak | Obsługa autoryzacji za pośrednictwem [RBAC](/azure/role-based-access-control/overview). |

### <a name="audit-trail"></a>Dziennik inspekcji

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Rejestrowanie i inspekcja płaszczyzny kontroli i zarządzania| Tak | Azure Resource Manager dziennik aktywności. |
| Rejestrowanie i inspekcja płaszczyzny danych | Tak | [Azure monitor dzienników diagnostycznych](/azure/azure-resource-manager/resource-group-audit) dotyczących rejestrowania i inspekcji łączności sieci VPN. |

### <a name="configuration-management"></a>Zarządzanie konfiguracją

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracją (wersja konfiguracji itp.)| Tak | W przypadku operacji zarządzania stanem konfiguracji bramy sieci VPN platformy Azure można wyeksportować jako szablon Azure Resource Manager i wersję z biegiem czasu. | 

