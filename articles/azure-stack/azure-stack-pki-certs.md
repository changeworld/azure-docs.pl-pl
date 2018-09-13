---
title: Azure wymagania dotyczące certyfikatów infrastruktury kluczy publicznych stosu dla usługi Azure Stack zintegrowane systemy | Dokumentacja firmy Microsoft
description: W tym artykule opisano wymagania dotyczące wdrażania certyfikatów infrastruktury kluczy publicznych do usługi Azure Stack dla usługi Azure Stack, zintegrowanych systemów.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: 8de34e4ac01dea9cf4a0c718883e8cc828be6403
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "44714621"
---
# <a name="azure-stack-public-key-infrastructure-certificate-requirements"></a>Wymagania dotyczące usługi Azure Stack infrastruktury kluczy publicznych certyfikatów

Usługa Azure Stack ma sieci publicznych infrastruktury przy użyciu dostępną z zewnątrz publiczne adresy IP przypisane do małej grupy usługi Azure Stack i ewentualnie dzierżawy maszyn wirtualnych. Certyfikaty PKI odpowiednimi nazwami DNS dla tych punktów końcowych publicznych infrastruktury Azure Stack są wymagane podczas wdrażania usługi Azure Stack. Ten artykuł zawiera informacje na temat:

- Jakie certyfikaty są wymagane do wdrożenia usługi Azure Stack
- Proces uzyskiwania liczbę certyfikatów zgodnych z tymi specyfikacjami
- Jak przygotować, weryfikacji i korzystać z tych certyfikatów podczas wdrażania

> [!Note]  
> Podczas wdrażania certyfikaty należy skopiować do folderu wdrożenia, który pasuje do dostawcy tożsamości, który instalujesz względem (Azure AD lub AD FS). Jeśli używasz jednego certyfikatu dla wszystkich punktów końcowych, należy skopiować ten plik certyfikatu do każdego folderu wdrożenia, co zostało opisane w poniższych tabelach. Struktura folderów wbudowanych w wdrożenia maszyny wirtualnej i znajduje się w temacie: C:\CloudDeployment\Setup\Certificates. 

## <a name="certificate-requirements"></a>Wymagania dotyczące certyfikatów
Poniższa lista zawiera opis wymagań dotyczących certyfikatów, które są wymagane do wdrożenia usługi Azure Stack: 
- Certyfikaty muszą być wystawiane z wewnętrznego urzędu certyfikacji lub publicznego urzędu certyfikacji. Jeśli publiczny urząd certyfikacji jest używany, muszą być zawarte w obrazu podstawowego systemu operacyjnego w ramach programu Microsoft zaufanego głównego urzędu. Pełną listę można znaleźć: https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca 
- Infrastruktury Azure Stack musi mieć dostęp do sieci do lokalizacji listy odwołania certyfikatów (CRL) urzędu certyfikacji, opublikowane w certyfikacie. Tę listę CRL musi być punkt końcowy http
- W przypadku rotacji certyfikatów, certyfikaty musi być albo wystawionych przez ten sam organ wewnętrznego certyfikatu używanego do podpisywania certyfikatów podane w wdrożenia lub dowolnym publicznego urzędu certyfikacji z powyższych
- Korzystanie z certyfikatów z podpisem własnym nie są obsługiwane.
- Do wdrożenia i obrót, możesz użyć pojedynczego certyfikatu, obejmujące wszystkie przestrzenie nazw pól Nazwa podmiotu i alternatywnej nazwy podmiotu (SAN) certyfikatu lub można użyć poszczególne certyfikaty dla każdej przestrzeni nazw poniżej usługi Azure Stack usługi, której planujesz korzystanie z wymagają. Uwaga: oba podejścia wymaga użycia symbole wieloznaczne dla punktów końcowych, w którym są one wymagane, takie jak **KeyVault** i **KeyVaultInternal**. 
- Algorytm podpisu certyfikatu musi być 3DES. Algorytm nie może być SHA1, muszą być silniejsza. 
- Format certyfikatu musi być PFX, ponieważ klucze publiczne i prywatne są wymagane do zainstalowania usługi Azure Stack. 
- Plik pfx certyfikatu musi mieć wartość "Podpis cyfrowy" i "KeyEncipherment" w polu "Użycie klucza".
- Plik pfx certyfikatu musi mieć wartość "Uwierzytelnianie serwera (1.3.6.1.5.5.7.3.1)" i "Uwierzytelnianie klienta (1.3.6.1.5.5.7.3.2)" w polu "Ulepszone użycie klucza".
- Certyfikat "wystawiony dla:" pole nie może być taka sama jak jego "wystawiony przez:" pole.
- Hasła do wszystkich plików pfx certyfikatów musi być taka sama w czasie wdrażania
- Hasło do pliku pfx certyfikatu musi być złożone hasło.
- Upewnij się, że nazwy podmiotu i alternatywnych nazw podmiotu dopasowania rozszerzenia (x509v3_config) alternatywnej nazwy podmiotu. Pole alternatywna nazwa podmiotu umożliwia określenie nazwy hostów dodatkowe (witryn sieci Web, adresy IP, nazwy pospolite) mają być chronione przy użyciu pojedynczego certyfikatu SSL.

> [!NOTE]  
> Samodzielna podpisane certyfikaty nie są obsługiwane.

> [!NOTE]  
> Obecność pośrednika urzędy certyfikacji w jest typu "łańcuch zaufania" certyfikatu są obsługiwane. 

## <a name="mandatory-certificates"></a>Wymagane certyfikaty
W tabeli w tej sekcji przedstawiono certyfikaty infrastruktury kluczy publicznych publiczny punkt końcowy usługi Azure Stack, które są wymagane do usługi Azure AD i wdrożenia usługi AD FS Azure Stack. Wymagania dotyczące certyfikatów są pogrupowane według obszaru, jak również przestrzeń nazw używaną i certyfikaty, które są wymagane dla każdej przestrzeni nazw. W tabeli opisano również folder, w którym dostawcy rozwiązań kopiuje różnych certyfikatów na publicznym punktem końcowym. 

Wymagane są certyfikaty z odpowiednimi nazwami DNS dla każdego punktu końcowego publicznego infrastruktury Azure Stack. Każdy punkt końcowy, nazwę DNS jest wyrażona w formacie:  *&lt;prefiksu >.&lt; region >. &lt;fqdn >*. 

Dla danego wdrożenia [region] i [externalfqdn] wartości muszą być zgodne, region i nazw domen zewnętrznych, które wybrano w systemie Azure Stack. Na przykład jeśli nazwa regionu *Redmond* i nazwy domeny zewnętrznej *contoso.com*, nazwy DNS może mieć format *&lt;prefiksu >. redmond.contoso.com*. *&lt;Prefiksu >* wartości są ustalonym przez firmę Microsoft w celu opisania punktu końcowego zabezpieczone przez certyfikat. Ponadto  *&lt;prefiksu >* wartości infrastruktury zewnętrzne punkty końcowe są zależne od usługi Azure Stack, który używa określonego punktu końcowego. 

> [!note]  
> Certyfikaty mogą być podane jako pojedynczego certyfikatu wieloznacznego obejmujące wszystkie obszary nazw, w polu podmiotu i alternatywnej nazwy podmiotu (SAN) skopiowane do wszystkich katalogów lub poszczególne certyfikaty dla każdego punktu końcowego skopiowany do odpowiedniego katalogu. Należy pamiętać, że obie opcje wymagają można używać symboli wieloznacznych certyfikaty dla punktów końcowych, takich jak **acs** oraz usługi Key Vault, gdzie są one wymagane. 

| Folder wdrożenia | Temat wymaganego certyfikatu i alternatywnej nazwy podmiotu (SAN) | Zakres (na region) | Przestrzeń nazw poddomeny |
|-------------------------------|------------------------------------------------------------------|----------------------------------|-----------------------------|
| Publiczny | portal.&lt;region>.&lt;fqdn> | Portale | &lt;region>.&lt;fqdn> |
| Portal administracyjny | adminportal. &lt;region >. &lt;fqdn > | Portale | &lt;region>.&lt;fqdn> |
| Azure Resource Manager Public | management.&lt;region>.&lt;fqdn> | Azure Resource Manager | &lt;region>.&lt;fqdn> |
| Administratora usługi Azure Resource Manager | adminmanagement.&lt;region>.&lt;fqdn> | Azure Resource Manager | &lt;region>.&lt;fqdn> |
| ACSBlob | *.blob.&lt;region>.&lt;fqdn><br>(Wieloznaczny certyfikat SSL) | Blob Storage | blob.&lt;region>.&lt;fqdn> |
| ACSTable | *.table.&lt;region>.&lt;fqdn><br>(Wieloznaczny certyfikat SSL) | Table Storage | table.&lt;region>.&lt;fqdn> |
| ACSQueue | *.queue.&lt;region>.&lt;fqdn><br>(Wieloznaczny certyfikat SSL) | Queue Storage | queue.&lt;region>.&lt;fqdn> |
| KeyVault | *.vault.&lt;region>.&lt;fqdn><br>(Wieloznaczny certyfikat SSL) | Usługa Key Vault | vault.&lt;region>.&lt;fqdn> |
| KeyVaultInternal | *.adminvault.&lt;region>.&lt;fqdn><br>(Wieloznaczny certyfikat SSL) |  Wewnętrzny magazyn kluczy |  adminvault.&lt;region>.&lt;fqdn> |
| Host rozszerzenia administratora | *.adminhosting. \<region >. \<fqdn > (wieloznaczne certyfikaty SSL) | Host rozszerzenia administratora | adminhosting. \<region >. \<fqdn > |
| Host rozszerzenia publiczne | * .hosting. \<region >. \<fqdn > (wieloznaczne certyfikaty SSL) | Host rozszerzenia publiczne | hosting. \<region >. \<fqdn > |

W przypadku wdrożenia usługi Azure Stack przy użyciu trybu wdrożenia usługi Azure AD, wystarczy do żądania certyfikatów wymienione w powyższej tabeli. Jednak w przypadku wdrożenia usługi Azure Stack przy użyciu trybu wdrożenia usług AD FS, należy także żądania certyfikatów opisanych w poniższej tabeli:

|Folder wdrożenia|Temat wymaganego certyfikatu i alternatywnej nazwy podmiotu (SAN)|Zakres (na region)|Przestrzeń nazw poddomeny|
|-----|-----|-----|-----|
|ADFS|adfs.*&lt;region>.&lt;fqdn>*<br>(Certyfikat SSL)|ADFS|*&lt;region>.&lt;fqdn>*|
|Graph|graph.*&lt;region>.&lt;fqdn>*<br>(Certyfikat SSL)|Graph|*&lt;region>.&lt;fqdn>*|
|

> [!IMPORTANT]
> Wszystkie certyfikaty, które są wymienione w tej sekcji musi mieć tego samego hasła. 

## <a name="optional-paas-certificates"></a>Opcjonalnie certyfikaty PaaS
Jeśli planujesz wdrożyć dodatkowe usługi Azure Stack w usłudze PaaS (SQL, MySQL i App Service) po usłudze Azure Stack została wdrożona i skonfigurowana, należy zażądać dodatkowych certyfikatów, aby pokrywał się z punktami końcowymi usługi PaaS. 

> [!IMPORTANT]
> Certyfikaty, których używasz dla dostawców zasobów usługi App Service, SQL i MySQL, trzeba mieć tego samego głównego urzędu certyfikacji jako używanych dla globalnych punktów końcowych usługi Azure Stack. 

W poniższej tabeli opisano punktów końcowych i certyfikatów wymaganych dla kart SQL i bazy danych MySQL i dla usługi App Service. Nie potrzebujesz skopiować te certyfikaty do folderu wdrożenia usługi Azure Stack. Zamiast tego należy podać te certyfikaty, po zainstalowaniu dostawców dodatkowych zasobów. 

|Zakres (na region)|Certyfikat|Wymaganego certyfikatu podmiotu i nazwy alternatywnej podmiotu (SAN)|Przestrzeń nazw poddomeny|
|-----|-----|-----|-----|
|SQL, MySQL|SQL i bazy danych MySQL|&#42;.dbadapter.*&lt;region>.&lt;fqdn>*<br>(Wieloznaczny certyfikat SSL)|dbadapter.*&lt;region>.&lt;fqdn>*|
|App Service|Ruch w sieci Web, w którym jest domyślny certyfikat SSL|&#42;.appservice.*&lt;region>.&lt;fqdn>*<br>&#42;.scm.appservice.*&lt;region>.&lt;fqdn>*<br>&#42;.sso.appservice.*&lt;region>.&lt;fqdn>*<br>(Obsługa wielu domen wieloznaczny certyfikat SSL<sup>1</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|
|App Service|Interfejs API|api.appservice.*&lt;region>.&lt;fqdn>*<br>(Certyfikat SSL<sup>2</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|
|App Service|FTP|ftp.appservice.*&lt;region>.&lt;fqdn>*<br>(Certyfikat SSL<sup>2</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|
|App Service|Logowanie jednokrotne|sso.appservice.*&lt;region>.&lt;fqdn>*<br>(Certyfikat SSL<sup>2</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|

<sup>1</sup> wymaga jednego certyfikatu z wielu symboli wieloznacznych alternatywne nazwy podmiotu. Wiele symboli wieloznacznych sieci SAN na jeden certyfikat może nie być obsługiwany przez wszystkie publiczne urzędy certyfikacji 

<sup>2</sup> A &#42;.appservice. *&lt;region >. &lt;fqdn >* certyfikatu wieloznacznego nie można użyć zamiast te trzy certyfikaty (api.appservice. *&lt;region >. &lt;fqdn >*, ftp.appservice. *&lt;region >. &lt;fqdn >* i sso.appservice. *&lt;region >. &lt;fqdn >*. Appservice wymaga jawnego Używanie osobnych certyfikatów dla tych punktów końcowych. 

## <a name="learn-more"></a>Dowiedz się więcej
Dowiedz się, jak [wygenerować certyfikaty PKI dla wdrożenia usługi Azure Stack](azure-stack-get-pki-certs.md). 

## <a name="next-steps"></a>Kolejne kroki
[Integracja tożsamości](azure-stack-integrate-identity.md)

