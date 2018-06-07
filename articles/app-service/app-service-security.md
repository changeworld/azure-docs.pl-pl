---
title: Zabezpieczenia w usłudze Azure App Service i usługę Azure Functions | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o jak App Service pomaga bezpiecznego aplikacji i jak można dodatkowo zablokowanie aplikacji przed zagrożeniami.
keywords: usługi aplikacji Azure, aplikacji sieci web, aplikacji mobilnej, aplikacji interfejsu api, funkcja aplikacji, zabezpieczeń i bezpiecznego, zabezpieczone zgodności zgodne, certyfikatów, certyfikaty, https, ftps tls, zaufania, szyfrowania, szyfrowania, szyfrowane ograniczenia adresów ip, uwierzytelniania, autoryzacji, authn, autho, msi, tożsamość usługi zarządzanej, klucze tajne, klucz tajny, poprawki, poprawki, poprawki, wersji, izolacji izolacji sieci, przed atakami ddos, mitm
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/24/2018
ms.author: cephalin
ms.openlocfilehash: 2ca1c1518589e60a03570e1c2063381f749ed9aa
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655078"
---
# <a name="security-in-azure-app-service-and-azure-functions"></a>Zabezpieczenia w usłudze Azure App Service i funkcji platformy Azure

W tym artykule opisano sposób [usłudze Azure App Service](app-service-web-overview.md) pomaga zabezpieczyć aplikację sieci web, zaplecza aplikacji mobilnych, aplikacji interfejsu API i [usługi Azure Functions](/azure/azure-functions/). Pokazuje też, jak można dodatkowo zabezpieczyć aplikacji za pomocą wbudowanych funkcji usługi aplikacji.

Składniki platformy aplikacji usługi, maszynach wirtualnych platformy Azure, Magazyn, połączeń sieciowych, platformy sieci web, funkcje zarządzania i integracji, w tym aktywnie zabezpieczone i trybie zaostrzonym. Usługi aplikacji przechodzi przez sprawdzenie zgodności intensywnie w sposób ciągły, aby upewnić się, że:

- Zasoby aplikacji są [zabezpieczonych](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox) z innych klientów zasobów platformy Azure.
- [Wystąpień maszyn wirtualnych i środowiska uruchomieniowego oprogramowania są regularnie aktualizowane](app-service-patch-os-runtime.md) na adres nowo wykryte luki w zabezpieczeniach. 
- Komunikacja klucze tajne (takie jak parametry połączenia) między aplikacji i innych zasobów platformy Azure (takich jak [bazy danych SQL](/services/sql-database/)) w obrębie platformy Azure i nie krzyżowe żadnych granic sieci. Hasła są zawsze szyfrowane, gdy przechowywane.
- Cała komunikacja za pośrednictwem połączenia usługi aplikacji funkcje, takie jak [połączenia hybrydowego](app-service-hybrid-connections.md), są szyfrowane. 
- Połączenia z narzędzi do zarządzania zdalnego programu Azure PowerShell, interfejsu wiersza polecenia Azure, Azure SDK, interfejsów API REST, wszystkie szyfrowane.
- 24-godzinnym threat management chroni tę platformę przed złośliwym oprogramowaniem i infrastruktury distributed denial of service (DDoS), man-in--middle (MITM) i innymi zagrożeniami.

Aby uzyskać więcej informacji na zabezpieczenia infrastruktury i platformy na platformie Azure, zobacz [Centrum zaufania Azure](https://azure.microsoft.com/overview/trusted-cloud/).

Poniższe sekcje pokazują, jak zabezpieczyć aplikację usługi aplikacji przed zagrożeniami.

## <a name="https-and-certificates"></a>HTTPS i certyfikatów

Usługa App Service umożliwia zabezpieczenia aplikacji z [HTTPS](https://wikipedia.org/wiki/HTTPS). Podczas tworzenia aplikacji, domyślnej nazwy domeny (\<nazwa_aplikacji >. azurewebsites.net) jest już dostępna przy użyciu protokołu HTTPS. Jeśli użytkownik [konfigurować domeny niestandardowej dla aplikacji](app-service-web-tutorial-custom-domain.md), należy również [zabezpiecz ją przy użyciu niestandardowego certyfikatu](app-service-web-tutorial-custom-ssl.md) tak, aby klient przeglądarki mogą wysyłać bezpiecznych połączeń HTTPS z domeny niestandardowej. Są to zrobić na dwa sposoby:

- **Certyfikat usługi aplikacji** — Utwórz certyfikat bezpośrednio na platformie Azure. Certyfikat jest zabezpieczone w [usługi Azure Key Vault](/azure/key-vault/)i można je zaimportować do aplikację usługi aplikacji. Aby uzyskać więcej informacji, zobacz [kupowanie i konfigurowanie certyfikatu SSL usługi aplikacji Azure](web-sites-purchase-ssl-web-site.md).
- **Certyfikatów innych firm** — Przekaż certyfikat SSL niestandardowych zakupionego od zaufanego urzędu certyfikacji i powiązać aplikację usługi aplikacji. Usługi aplikacji obsługuje zarówno certyfikaty w jednej domenie i certyfikatów z symbolami wieloznacznymi. Obsługuje także certyfikaty z podpisem własnym do celów testowych. Aby uzyskać więcej informacji, zobacz [Powiąż istniejący certyfikat SSL niestandardowych do aplikacji sieci Web Azure](app-service-web-tutorial-custom-ssl.md).

## <a name="insecure-protocols-http-tls-10-ftp"></a>Niebezpieczne protokołów (HTTP, TLS 1.0, FTP)

Aby zabezpieczyć aplikację przed wszystkie nieszyfrowanego połączenia (HTTP), usługa App Service zapewnia konfiguracji jednym kliknięciem do wymuszania protokołu HTTPS. Niezabezpieczone żądania są włączane optymalizacji przed dotarciem nawet kod aplikacji. Aby uzyskać więcej informacji, zobacz [wymusić HTTPS](app-service-web-tutorial-custom-ssl.md#enforce-https).

[TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1.0 jest już uznawany za bezpieczny przez standardy branżowe, takie jak [PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard). Usługa App Service umożliwia wyłącz protokoły nieaktualne przez [Wymuszanie protokołu TLS 1.1/1.2](app-service-web-tutorial-custom-ssl.md#enforce-tls-1112).

Usługi aplikacji obsługuje FTP i FTPS wdrażania plików. Jednak FTPS należy używać zamiast FTP, jeśli to możliwe. W przypadku jednego lub obu tych protokołów nie są używane, należy [je wyłączyć](app-service-deploy-ftp.md#enforce-ftps).

## <a name="static-ip-restrictions"></a>Ograniczenia statycznych adresów IP

Domyślnie aplikację usługi aplikacji akceptuje żądania od wszystkich adresów IP z Internetu, ale można ograniczyć, że dostęp do małego podzbioru adresów IP. Usługa aplikacji w systemie Windows pozwala zdefiniować listę adresów IP, które mogą uzyskać dostępu do aplikacji. Lista dozwolonych elementów mogą zawierać pojedyncze adresy IP lub zakres adresów IP określone przez maskę podsieci. Aby uzyskać więcej informacji, zobacz [Azure App Service statycznych ograniczenia adresów IP](app-service-ip-restrictions.md).

Usługi aplikacji w systemie Windows, można również ograniczyć adresy IP dynamicznie przez skonfigurowanie _web.config_. Aby uzyskać więcej informacji, zobacz [dynamicznych zabezpieczeń protokołu IP <dynamicIpSecurity> ](https://docs.microsoft.com/iis/configuration/system.webServer/security/dynamicIpSecurity/).

## <a name="client-authentication-and-authorization"></a>Klient uwierzytelniania i autoryzacji

Usługa Azure App Service zawiera gotowe uwierzytelniania i autoryzacji użytkowników lub klienta aplikacji. Włączone, może się zarejestrować w przystawce Użytkownicy i aplikacje klienckie niewielkiego lub żadnego kodu aplikacji. Może wdrożyć własne rozwiązanie uwierzytelniania i autoryzacji, lub zezwolić aplikacji usługi do obsługi go dla Ciebie. Moduł uwierzytelniania i autoryzacji obsługuje żądania sieci web przed przekazaniem ich do kodu aplikacji, a jego nie zezwala na nieautoryzowanych żądań przed dotarciem kodu.

Usługi aplikacji uwierzytelniania i autoryzacji obsługuje wielu dostawców uwierzytelniania, w tym usługi Azure Active Directory, konta Microsoft, Facebook, Google i Twitter. Aby uzyskać więcej informacji, zobacz [uwierzytelnianie i autoryzację w usłudze Azure App Service](app-service-authentication-overview.md).

## <a name="service-to-service-authentication"></a>Uwierzytelnianie między usługami

Podczas uwierzytelniania na usługi zaplecza, usługa App Service zapewnia dwa mechanizmy różne w zależności od potrzeb:

- **Usługa tożsamości** — Zaloguj się do zasobu zdalnego przy użyciu tożsamości aplikacji. Usługa App Service umożliwia łatwe tworzenie [tożsamość usługi zarządzanej](app-service-managed-service-identity.md), które służy do uwierzytelniania za pomocą innych usług, takich jak [bazy danych SQL Azure](/azure/sql-database/) lub [usługi Azure Key Vault](/azure/key-vault/). Samouczek end-to-end tego podejścia, zobacz [tożsamość usługi zarządzanej połączeń Secure bazy danych SQL Azure z usługi aplikacji przy użyciu](app-service-web-tutorial-connect-msi.md).
- **W imieniu z (OBO)** -ułatwić delegowany dostęp do zasobów zdalnych w imieniu użytkownika. Azure Active Directory jako dostawcy uwierzytelniania, aplikację usługi aplikacji umożliwiają wykonywanie delegowanego logowanie do zdalnej usługi, takich jak [interfejsu API usługi Azure Active Directory Graph](../active-directory/develop/active-directory-graph-api.md) lub zdalnej aplikacji interfejsu API w usłudze App Service. Samouczek end-to-end tego podejścia, zobacz [uwierzytelniania i autoryzacji użytkowników end-to-end w usłudze Azure App Service](app-service-web-tutorial-auth-aad.md).

## <a name="connectivity-to-remote-resources"></a>Łączność z zasobami zdalnymi

Istnieją trzy typy zasobów zdalnych, które aplikacji mogą uzyskać dostęp: 

- [Zasoby platformy Azure](#azure-resources)
- [Zasoby w sieci wirtualnej platformy Azure](#resources-inside-an-azure-virtual-network)
- [Zasobów lokalnych](#on-premises-resources)

W każdym z tych przypadków App Service umożliwia nawiązywanie bezpiecznych połączeń, ale nadal należy przestrzegać najlepszych rozwiązań dotyczących zabezpieczeń. Na przykład zawsze używać połączeń szyfrowanych nawet wtedy, gdy zasób zaplecza zezwala na połączenia nieszyfrowanego. Ponadto upewnij się, że poziomu zaplecza Azure usługi zezwala minimalny zbiór adresów IP. Wychodzące adresów IP można znaleźć aplikację w [ruchu przychodzącego i wychodzącego adresów IP w usłudze Azure App Service](app-service-ip-addresses.md).

### <a name="azure-resources"></a>Zasoby platformy Azure

Gdy aplikacja łączy się z zasobów platformy Azure, takich jak [bazy danych SQL](/services/sql-database/) i [usługi Azure Storage](/azure/storage/), połączenie pozostaje w obrębie platformy Azure i nie krzyżowe żadnych granic sieci. Jednak połączenie przechodzi przez udostępniony sieci na platformie Azure, więc zawsze upewnij się, że połączenie jest szyfrowane. 

Jeśli aplikacja jest hostowana w [środowiska usługi aplikacji](environment/intro.md), wykonaj następujące czynności [nawiązać obsługiwane usług platformy Azure przy użyciu punktów końcowych usługi sieci wirtualnej](../virtual-network/virtual-network-service-endpoints-overview.md).

### <a name="resources-inside-an-azure-virtual-network"></a>Zasoby w sieci wirtualnej platformy Azure

Aplikacja może uzyskiwać dostęp do zasobów w [sieci wirtualnej Azure](/azure/virtual-network/) za pośrednictwem [integracji sieci wirtualnej](web-sites-integrate-with-vnet.md). Integracja jest nawiązywane z sieć wirtualną przy użyciu sieci VPN punkt lokacja. Aplikację można następnie uzyskać dostęp do zasobów w sieci wirtualnej przy użyciu swoich prywatnych adresów IP. Połączenie punkt lokacja, jednak nadal są przesyłane za pośrednictwem udostępnionego sieci na platformie Azure. 

Aby wyizolować łączność zasobów całkowicie z udostępnionym sieci na platformie Azure, Utwórz aplikację w [środowiska usługi aplikacji](environment/intro.md). Ponieważ środowisko usługi aplikacji jest wdrażana zawsze dedykowanego sieci wirtualnej, łączności między zasobami w sieci wirtualnej i aplikacji, na których jest pełni izolowanym. Innych aspektów zabezpieczeń sieciowych w środowisku usługi aplikacji, zobacz [izolacja sieci](#network-isolation).

### <a name="on-premises-resources"></a>Zasobów lokalnych

Można bezpieczny dostęp do zasobów lokalnych, takich jak bazy danych na trzy sposoby: 

- [Połączenia hybrydowe](app-service-hybrid-connections.md) -ustanawia połączenie punkt-punkt zasobu zdalnego za pośrednictwem tunelu protokołu TCP. Tunel protokołu TCP jest tworzony, za pomocą protokołu TLS 1.2 z kluczami sygnatury dostępu Współdzielonego dostępu współdzielonego.
- [Integracja sieci wirtualnych](web-sites-integrate-with-vnet.md) z siecią VPN lokacja lokacja — zgodnie z opisem w [zasobów w sieci wirtualnej platformy Azure](#resources-inside-an-azure-virtual-network), ale sieci wirtualnej można podłączyć do sieci lokalnej za pośrednictwem [ sieci VPN typu lokacja lokacja](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md). W tej topologii sieci aplikację można połączyć się z zasobów lokalnych, takich jak innych zasobów w sieci wirtualnej.
- [Środowisko usługi aplikacji](environment/intro.md) z siecią VPN lokacja lokacja — zgodnie z opisem w [zasobów w sieci wirtualnej platformy Azure](#resources-inside-an-azure-virtual-network), ale sieci wirtualnej można podłączyć do sieci lokalnej za pośrednictwem [lokacja lokacja Sieć VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md). W tej topologii sieci aplikację można połączyć się z zasobów lokalnych, takich jak innych zasobów w sieci wirtualnej.

## <a name="application-secrets"></a>Klucze tajne aplikacji

Nie należy przechowywać klucze tajne aplikacji, takich jak poświadczenia bazy danych, tokeny interfejsu API i kluczy prywatnych w plikach konfiguracji lub kodu. Powszechnie zaakceptowany podejście jest dostęp do nich jako [zmiennych środowiskowych](https://wikipedia.org/wiki/Environment_variable) przy użyciu standardowego wzorca w wybranym języku. W usłudze App Service jest sposób definiowania zmiennych środowiskowych [ustawień aplikacji](web-sites-configure.md#app-settings) (i, szczególnie w przypadku aplikacji .NET [parametry połączenia](web-sites-configure.md#connection-strings)). Ustawienia aplikacji i parametry połączenia są przechowywane w postaci zaszyfrowanej na platformie Azure i są one odszyfrowane tylko przed są wstrzykiwane do pamięci procesu aplikacji, po uruchomieniu aplikacji. Klucze szyfrowania są obracane regularnie.

Alternatywnie można zintegrować aplikację usługi aplikacji z [usługi Azure Key Vault](/azure/key-vault/) zarządzania zaawansowanych kluczy tajnych. Przez [podczas uzyskiwania dostępu do usługi Key Vault za pomocą tożsamości zarządzanych usług](../key-vault/tutorial-web-application-keyvault.md), aplikację usługi aplikacji może bezpiecznego dostępu do kluczy tajnych należy.

## <a name="network-isolation"></a>Izolacja sieci

Z wyjątkiem **izolowany** warstwy cenowej, wszystkie warstwy uruchamiać aplikacje w infrastrukturze sieci udostępnionej w usłudze App Service. Na przykład publicznych adresów IP i moduły równoważenia obciążenia frontonu są współużytkowane z innymi dzierżawcami. **Izolowany** warstwy zapewnia izolację sieci pełną przez uruchomienie aplikacji w dedykowanej [środowiska usługi aplikacji](environment/intro.md). Środowisko usługi aplikacji jest uruchamiany w własnego wystąpienia [sieci wirtualnej Azure](/azure/virtual-network/). Umożliwia: 

- Ograniczenie dostępu do sieci z [sieciowej grupy zabezpieczeń](../virtual-network/virtual-networks-nsg.md). 
- Obsługiwać aplikacje za pomocą dedykowanego publiczny punkt końcowy, z dedykowanym front kończy się.
- Obsługi wewnętrznych aplikacji przy użyciu wewnętrznego modułu równoważenia obciążenia (ILB), która zezwala na dostęp tylko z wewnątrz sieci wirtualnej platformy Azure. ILB ma adres IP z sieci prywatnej podsieci, które zapewnia izolację całkowita liczba aplikacji z Internetu.
- [Użyj ILB za zaporą aplikacji sieci web (WAF)](environment/integrate-with-application-gateway.md). Zapory aplikacji sieci Web zapewnia ochronę przedsiębiorstw do aplikacji publicznych, takie jak ochrona przed atakami DDoS, filtrowania identyfikatora URI i zapobiegania iniekcji kodu SQL.

Aby uzyskać więcej informacji, zobacz [wprowadzenie do środowiska usługi aplikacji Azure](environment/intro.md).