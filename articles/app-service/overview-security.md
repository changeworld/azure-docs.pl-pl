---
title: Przegląd zabezpieczeń — usłudze Azure App Service | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o jak usługi App Service ułatwia bezpieczne aplikacja i jak można dalej zablokować aplikację z poziomu zagrożenia.
keywords: usługi Azure app service, aplikacji sieci web, aplikacji mobilnych, aplikacji interfejsu api, aplikacji funkcji, bezpieczeństwo, bezpieczne i zabezpieczony, zgodności i zgodne, certyfikatów, certyfikaty, https, ftps tls, zaufanie, szyfrowanie, szyfrowanie, szyfrowane, ograniczenia adresów ip, uwierzytelnianie, autoryzacja, uwierzytelniania, autho, msi, tożsamość usługi zarządzanej, tożsamość zarządzaną, wpisów tajnych, klucz tajny, poprawek, poprawek, poprawek, wersji, izolacji, izolacji sieci, przed atakami ddos i mitm
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
ms.date: 08/24/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 0e592c03da222e5265ed53aab8ef73f3b477f33a
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475869"
---
# <a name="security-in-azure-app-service"></a>Zabezpieczenia w usłudze Azure App Service

W tym artykule przedstawiono, jak [usługi Azure App Service](overview.md) ułatwia zabezpieczenie Twojej aplikacji sieci web, zaplecza aplikacji mobilnych, aplikacji interfejsu API i [aplikacji funkcji](/azure/azure-functions/). Pokazuje również, jak można dodatkowo zabezpieczyć swoją aplikację przy użyciu wbudowanych funkcji usługi App Service.

Składniki platformy usługi App Service, w tym maszyn wirtualnych platformy Azure, Magazyn, połączeń sieciowych, struktury sieci web, zarządzanie i integrację funkcji, są aktywnie zabezpieczone i wzmocnione. Usługa App Service przechodzi przez kontrole zgodności intensywnie w sposób ciągły, aby upewnić się, że:

- Zasoby aplikacji są [zabezpieczonej](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox) z zasobów platformy Azure innych klientów.
- [Wystąpienia maszyn wirtualnych i oprogramowania środowiska uruchomieniowego są regularnie aktualizowane,](overview-patch-os-runtime.md) na adres nowo odnalezione luki w zabezpieczeniach. 
- Komunikacja z wpisy tajne (takie jak parametry połączenia) między aplikacją i innymi zasobami platformy Azure (takie jak [bazy danych SQL](https://azure.microsoft.com/services/sql-database/)) pozostaje w obrębie platformy Azure i nie między wszystkie granice sieci. Klucze tajne są zawsze szyfrowane, gdy przechowywane.
- Cała komunikacja za pośrednictwem połączenia usługi App Service funkcje, takie jak [połączenia hybrydowego](app-service-hybrid-connections.md), są szyfrowane. 
- Połączenia za pomocą narzędzi do zarządzania zdalnego programu Azure PowerShell, interfejsu wiersza polecenia platformy Azure, zestawy SDK platformy Azure, interfejsy API REST, wszystkie szyfrowane.
- 24-godzinny zagrożeń, zarządzania zapewnia ochronę infrastruktury i platformy, przed złośliwym oprogramowaniem, distributed denial of service (DDoS), ataków typu man-in--middle (MITM) i innymi zagrożeniami.

Aby uzyskać więcej informacji na temat zabezpieczeń infrastruktury i platformy na platformie Azure, zobacz [Centrum zaufania systemu Azure](https://azure.microsoft.com/overview/trusted-cloud/).

Poniższe sekcje pokazują, jak jeszcze lepiej chronić aplikację usługi App Service przed zagrożeniami.

## <a name="https-and-certificates"></a>Protokołu HTTPS i certyfikatów

Usługa App Service umożliwia zabezpieczanie aplikacji przy użyciu [HTTPS](https://wikipedia.org/wiki/HTTPS). Podczas tworzenia aplikacji, domyślnej nazwy domeny (\<nazwa_aplikacji >. azurewebsites.net) jest już dostępny, przy użyciu protokołu HTTPS. Jeśli użytkownik [konfigurowanie domeny niestandardowej na potrzeby aplikacji](app-service-web-tutorial-custom-domain.md), należy również [zabezpiecz ją przy użyciu niestandardowego certyfikatu](app-service-web-tutorial-custom-ssl.md) tak, aby klient przeglądarki mogą wysyłać bezpiecznego połączenia HTTPS z domeny niestandardowej. Istnieją dwa sposoby, aby to zrobić:

- **Certyfikat usługi App Service** — Utwórz certyfikat z bezpośrednio na platformie Azure. Certyfikat jest zabezpieczony [usługi Azure Key Vault](/azure/key-vault/)i mogą być importowane do aplikacji usługi app Service. Aby uzyskać więcej informacji, zobacz [Kup i skonfiguruj certyfikat SSL dla usługi Azure App Service](web-sites-purchase-ssl-web-site.md).
- **Certyfikatów innych firm** — Przekaż niestandardowy certyfikat protokołu SSL zakupiony od zaufanego urzędu certyfikacji i powiązać ją z aplikacji usługi app Service. Usługa App Service obsługuje zarówno jednej domeny i certyfikaty symboli wieloznacznych. Obsługuje także certyfikaty z podpisem własnym do celów testowych. Aby uzyskać więcej informacji, zobacz [powiązania istniejącego niestandardowego certyfikatu SSL w usłudze Azure App Service](app-service-web-tutorial-custom-ssl.md).

## <a name="insecure-protocols-http-tls-10-ftp"></a>Protokoły niezabezpieczonego (HTTP, TLS 1.0, FTP)

Aby zabezpieczyć aplikację przed wszystkie nieszyfrowanego połączenia (HTTP), usługa App Service udostępnia konfigurację jednym kliknięciem, aby Wymuszanie protokołu HTTPS. Niezabezpieczone żądania są natychmiast wyłączone, zanim osiągną one nawet kodu aplikacji. Aby uzyskać więcej informacji, zobacz [Wymuszanie protokołu HTTPS](app-service-web-tutorial-custom-ssl.md#enforce-https).

[TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1.0 przestaje być uważany za bezpieczny przez standardy branżowe, takie jak [PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard). Usługa App Service umożliwia wyłączenie nieaktualne protokołów przez [Wymuszanie protokołu TLS 1.1/1.2](app-service-web-tutorial-custom-ssl.md#enforce-tls-versions).

Usługa App Service obsługuje zarówno protokołu FTP i FTPS wdrażania plików. Jednak FTPS powinny być używane zamiast protokołu FTP, jeśli to możliwe. Jeśli co najmniej jeden z tych protokołów nie są używane, należy [je wyłączyć](deploy-ftp.md#enforce-ftps).

## <a name="static-ip-restrictions"></a>Ograniczenia statycznych adresów IP

Domyślnie aplikację usługi App Service akceptuje żądania ze wszystkich adresów IP z Internetu, ale można ograniczyć, że dostęp do małego podzbioru adresów IP. Usługa App Service w Windows pozwala zdefiniować listę adresów IP, które mogą uzyskiwać dostęp do aplikacji. Lista dozwolonych może zawierać pojedyncze adresy IP lub zakres adresów IP określone przez maskę podsieci. Aby uzyskać więcej informacji, zobacz [usługi Azure App Service statyczne ograniczenia adresów IP](app-service-ip-restrictions.md).

Dla usługi App Service na Windows, można również ograniczyć adresy IP dynamicznie, konfigurując _web.config_. Aby uzyskać więcej informacji, zobacz [dynamicznych zabezpieczeń IP \<dynamicIpSecurity >](https://docs.microsoft.com/iis/configuration/system.webServer/security/dynamicIpSecurity/).

## <a name="client-authentication-and-authorization"></a>Klient uwierzytelniania i autoryzacji

Usługa Azure App Service zapewnia kompleksową uwierzytelniania i autoryzacji użytkowników lub klienta aplikacji. Po włączeniu można zarejestrować się w przystawce Użytkownicy i aplikacje klienckie za pomocą niewielkiego lub żadnego kodu aplikacji. Mogą implementować własne rozwiązanie uwierzytelniania i autoryzacji lub zezwalać na usługi App Service obsłużyć go dla Ciebie. Moduł uwierzytelniania i autoryzacji obsługuje żądania sieci web przed przekazaniem ich do kodu aplikacji i odrzuca nieautoryzowanych żądań, zanim dotrą swój kod.

Usługa App Service, uwierzytelnianie i autoryzacja obsługuje wielu dostawców uwierzytelniania, w tym usługi Azure Active Directory, konta Microsoft, Facebook, Google i Twitter. Aby uzyskać więcej informacji, zobacz [uwierzytelnianie i autoryzacja w usłudze Azure App Service](overview-authentication-authorization.md).

## <a name="service-to-service-authentication"></a>Uwierzytelnianie między usługami

Podczas uwierzytelniania w odniesieniu do usługi zaplecza, usługa App Service udostępnia dwa różne mechanizmy, w zależności od Twoich potrzeb:

- **Usługa tożsamości** — Zaloguj się do zasobu zdalnego przy użyciu tożsamości aplikacji. Usługa App Service umożliwia łatwe tworzenie [tożsamości zarządzanej](overview-managed-identity.md), które służy do uwierzytelniania za pomocą innych usług, takich jak [usługi Azure SQL Database](/azure/sql-database/) lub [usługi Azure Key Vault](/azure/key-vault/). Samouczek end-to-end tej metody, zobacz [połączenia Zabezpieczanie usługi Azure SQL Database z usługi App Service za pomocą tożsamości zarządzanej](app-service-web-tutorial-connect-msi.md).
- **W imieniu z (OBO)** -wprowadzić delegowanego dostępu do zasobów zdalnych w imieniu użytkownika. Za pomocą usługi Azure Active Directory jako dostawcy uwierzytelniania, aplikacji usługi app Service można wykonywać delegowanego logowania do usługi zdalnej, takich jak [interfejsu API usługi Azure Active Directory Graph](../active-directory/develop/active-directory-graph-api.md) lub zdalnej aplikacji interfejsu API w usłudze App Service. Samouczek end-to-end tej metody, zobacz [uwierzytelnianie i autoryzowanie użytkowników end-to-end w usłudze Azure App Service](app-service-web-tutorial-auth-aad.md).

## <a name="connectivity-to-remote-resources"></a>Łączność z zasobami zdalnymi

Istnieją trzy typy zasobów zdalnych, które aplikacja może być konieczne dostępu: 

- [Zasoby platformy Azure](#azure-resources)
- [Zasoby znajdujące się w sieci wirtualnej platformy Azure](#resources-inside-an-azure-virtual-network)
- [Zasoby lokalne](#on-premises-resources)

W każdym z tych przypadków usługa App Service zapewnia sposób nawiązywanie bezpiecznych połączeń, ale nadal należy przestrzegać najlepszych rozwiązań dotyczących zabezpieczeń. Na przykład zawsze używać połączeń szyfrowanych nawet wtedy, gdy zasób zaplecza zezwala na połączenia nieszyfrowanego. Ponadto upewnij się, że zaplecza usługi Azure zezwala minimalny zestaw adresów IP. Wychodzące adresy IP można znaleźć Twoją aplikację [przychodzące i wychodzące adresy IP w usłudze Azure App Service](overview-inbound-outbound-ips.md).

### <a name="azure-resources"></a>Zasoby platformy Azure

Gdy aplikacja łączy się z zasobami platformy Azure, takich jak [bazy danych SQL](https://azure.microsoft.com/services/sql-database/) i [usługi Azure Storage](/azure/storage/), połączenie pozostaje w obrębie platformy Azure i nie cross wszystkie granice sieci. Jednak połączenie przechodzi przez udostępnionej sieci na platformie Azure, dlatego zawsze upewnij się, że połączenie jest szyfrowane. 

Jeśli aplikacja jest hostowana w [środowiska App Service environment](environment/intro.md), należy [Połącz obsługiwane usługi platformy Azure za pomocą punktów końcowych usługi sieci wirtualnej](../virtual-network/virtual-network-service-endpoints-overview.md).

### <a name="resources-inside-an-azure-virtual-network"></a>Zasoby znajdujące się w sieci wirtualnej platformy Azure

Aplikacja może uzyskiwać dostęp do zasobów w [Azure Virtual Network](/azure/virtual-network/) za pośrednictwem [Integracja sieci wirtualnej](web-sites-integrate-with-vnet.md). Integracja jest nawiązywane z sieci wirtualnej przy użyciu sieci VPN punkt lokacja. Aplikacja następnie dostęp do zasobów w sieci wirtualnej za pomocą prywatnych adresów IP. Połączenia punkt lokacja, jednak nadal przesyłane za pośrednictwem udostępnionych sieci na platformie Azure. 

Aby wyizolować łączności zasobów całkowicie z udostępnionej sieci na platformie Azure, utworzyć aplikację [środowiska App Service environment](environment/intro.md). Ponieważ środowiska usługi App Service zawsze jest wdrażana w dedykowanej sieci wirtualnej, łączność między zasobami w sieci wirtualnej i aplikacji, na których jest całkowicie odizolowane. Aby uzyskać inne aspekty zabezpieczeń sieci w środowisku usługi App Service, zobacz [izolację sieci](#network-isolation).

### <a name="on-premises-resources"></a>Zasoby lokalne

Można bezpieczny dostęp do zasobów lokalnych, takich jak bazy danych na trzy sposoby: 

- [Połączenia hybrydowe](app-service-hybrid-connections.md) -ustanawia połączenie typu punkt-punkt do zdalnego zasobu za pośrednictwem tunelu protokołu TCP. Tunel protokołu TCP jest tworzony, przy użyciu kluczy (SAS) sygnatury dostępu współdzielonego przy użyciu protokołu TLS 1.2.
- [Integracja z siecią wirtualną](web-sites-integrate-with-vnet.md) za pomocą połączenia VPN lokacja lokacja — zgodnie z opisem w [zasobów w obrębie usługi Azure Virtual Network](#resources-inside-an-azure-virtual-network), ale sieć wirtualną można połączyć z sieci lokalnej za pomocą [ Site-to-site VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md). W tej topologii sieci aplikację można połączyć z zasobami lokalnymi, jak inne zasoby w sieci wirtualnej.
- [Środowisko usługi App Service](environment/intro.md) za pomocą połączenia VPN lokacja lokacja — zgodnie z opisem w [zasobów w obrębie usługi Azure Virtual Network](#resources-inside-an-azure-virtual-network), ale sieć wirtualną można połączyć z sieci lokalnej za pomocą [site-to-site Sieć VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md). W tej topologii sieci aplikację można połączyć z zasobami lokalnymi, jak inne zasoby w sieci wirtualnej.

## <a name="application-secrets"></a>Wpisy tajne aplikacji

Nie należy przechowywać kluczy tajnych aplikacji, takich jak poświadczenia bazy danych, tokeny interfejsu API i klucze prywatne w plikach kodu lub konfiguracji. Powszechnie akceptowane podejściem jest dostęp do nich jako [zmienne środowiskowe](https://wikipedia.org/wiki/Environment_variable) przy użyciu standardowego wzorca w wybranym języku. W usłudze App Service jest to sposób definiowania zmiennych środowiskowych [ustawienia aplikacji](configure-common.md#configure-app-settings) (i, szczególnie w przypadku aplikacji .NET [parametry połączenia](configure-common.md#configure-connection-strings)). Ustawienia aplikacji i parametry połączenia są przechowywane w postaci zaszyfrowanej na platformie Azure i są one odszyfrowywane tylko przed są wstrzykiwane do pamięci procesu aplikacji, po uruchomieniu aplikacji. Klucze szyfrowania są obracane regularnie.

Alternatywnie można zintegrować aplikację usługi App Service przy użyciu [usługi Azure Key Vault](/azure/key-vault/) zarządzania zaawansowane wpisów tajnych. Przez [uzyskiwania dostępu do usługi Key Vault za pomocą tożsamości zarządzanej](../key-vault/tutorial-web-application-keyvault.md), aplikacji usługi app Service bezpieczny dostęp do danych poufnych, potrzebujesz.

## <a name="network-isolation"></a>Izolacja sieci

Z wyjątkiem **izolowany** warstwy cenowej, dla wszystkich warstw uruchamiaj swoje aplikacje w udostępnionej infrastrukturze sieciowej w usłudze App Service. Na przykład publiczne adresy IP i równoważenia obciążenia frontonu są współużytkowane z innymi dzierżawami. **Izolowany** warstwy zapewnia izolację sieci pełną, uruchamiając swoje aplikacje w dedykowanej [środowiska App Service environment](environment/intro.md). Środowiska usługi App Service uruchamia wystąpienia programu [Azure Virtual Network](/azure/virtual-network/). Dzięki temu można: 

- Obsługiwać swoje aplikacje za pomocą dedykowanego publicznego punktu końcowego, za pomocą dedykowanego frontonów.
- Obsługiwać wewnętrznej aplikacji przy użyciu wewnętrznego modułu równoważenia obciążenia (ILB), która zezwala na dostęp tylko z wewnątrz sieci wirtualnej platformy Azure. Wewnętrznego modułu równoważenia obciążenia ma adres IP z podsieci prywatnej, co zapewnia pełną izolację aplikacji z Internetu.
- [Użyj wewnętrznego modułu równoważenia obciążenia za zaporą aplikacji sieci web (WAF)](environment/integrate-with-application-gateway.md). Zapora aplikacji sieci Web zapewnia ochronę klasy korporacyjnej w aplikacjach publicznego, takie jak ochrona przed atakami DDoS, filtrowanie identyfikatora URI i zapobiegania iniekcji SQL.

Aby uzyskać więcej informacji, zobacz [wprowadzenie do środowisk Azure App Service Environment](environment/intro.md). 
