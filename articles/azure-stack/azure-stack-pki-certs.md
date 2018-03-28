---
title: Azure wymagania dotyczące certyfikatów infrastruktury kluczy publicznych stosu dla stosu Azure zintegrowanych systemów | Dokumentacja firmy Microsoft
description: W tym artykule opisano wymagania dotyczące wdrażania certyfikatu PKI stosu Azure stosu Azure zintegrowanych systemów.
services: azure-stack
documentationcenter: ''
author: mabriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: 455c74ca808f71258a12166c2e36bdd73d9a3e20
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="azure-stack-public-key-infrastructure-certificate-requirements"></a>Wymagania dotyczące certyfikatów infrastruktury kluczy publicznych stosu Azure
Azure stos nie zawiera publicznych infrastruktury sieci przy użyciu dostępne z zewnątrz publiczne adresy IP przypisane do niewielki zestaw usług Azure stosu i prawdopodobnie maszyny wirtualne dzierżawców. Certyfikaty PKI z odpowiedniej nazwy DNS dla tych punktów końcowych infrastruktury publicznych stosu Azure są wymagane podczas wdrażania usługi Azure stosu. Ten artykuł zawiera informacje na temat:

- Jakie certyfikaty są wymagane do wdrożenia usługi Azure stosu
- Uzyskiwanie certyfikatów dopasowania tych specyfikacji procesu
- Jak przygotować, sprawdzania poprawności i użyć tych certyfikatów podczas wdrażania

> [!NOTE]
> Podczas wdrażania certyfikatów należy skopiować do folderu wdrożenia, który odpowiada dostawcy tożsamości, które wdrażasz względem (Azure AD lub AD FS). Jeśli używasz jeden certyfikat dla wszystkich punktów końcowych, należy skopiować ten plik certyfikatu do każdego folderu wdrożenia zgodnie z opisem w poniższych tabelach. Struktura folderów wstępnie jest oparty na maszynie wirtualnej wdrożenia i znajduje się w temacie: C:\CloudDeployment\Setup\Certificates. 

## <a name="certificate-requirements"></a>Wymagania dotyczące certyfikatów
Poniższa lista zawiera opis wymagań dotyczących certyfikatów, które są wymagane do wdrożenia usługi Azure stosu: 
- Certyfikaty muszą być wystawiane z wewnętrznego urzędu certyfikacji lub publicznego urzędu certyfikacji. Jeśli jest używany publiczny urząd certyfikacji, musi być uwzględniona w obrazu podstawowego systemu operacyjnego w ramach programu Microsoft zaufanego głównego urzędu. Pełną listę można znaleźć: https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca 
- Certyfikat może być obejmujące wszystkie przestrzenie nazw w pole alternatywnej nazwy podmiotu (SAN) certyfikatu jeden symbol wieloznaczny. Alternatywnie można użyć poszczególnych certyfikatów dla punktów końcowych, takie jak usługi acs oraz gdy wymagane są usługi Key Vault za pomocą symbole wieloznaczne. 
- Algorytm podpisu certyfikatu nie może być SHA1, muszą być silniejsze. 
- Format certyfikatu musi być PFX, jak klucze publiczne i prywatne są wymagane do zainstalowania stosu Azure. 
- Pliki pfx certyfikatu musi mieć wartość "Podpis cyfrowy" i "KeyEncipherment" w polu "Użycie klucza".
- Pliki pfx certyfikatu musi mieć wartości "Uwierzytelnianie serwera (1.3.6.1.5.5.7.3.1)" i "Uwierzytelnianie klienta (1.3.6.1.5.5.7.3.2)" w polu "Ulepszone użycie klucza".
- Certyfikatu "wydany dla:" pole nie może być taka sama jak jego "wystawiony przez:" pola.
- Hasła do wszystkich plików pfx certyfikatów muszą być takie same w czasie wdrażania
- Upewnij się, że nazwy podmiotu i alternatywnej nazwy podmiotu wszystkie certyfikaty są zgodne ze specyfikacjami opisane w tym artykule, aby uniknąć wdrożenia nie powiodło się.

> [!NOTE]
> Obecność pośrednik urzędy certyfikacji w IS łańcucha z zaufania certyfikatów jest obsługiwane. 

## <a name="mandatory-certificates"></a>Obowiązkowe certyfikatów
W tabeli w tej sekcji opisano certyfikatów PKI publiczny punkt końcowy stosu Azure, które są wymagane dla obu usługi Azure AD i usługi AD FS Azure stosu wdrożeń. Wymagania dotyczące certyfikatów są pogrupowane według obszaru, jak również obszarów nazw używanych, i certyfikaty, które są wymagane dla każdej przestrzeni nazw. W tabeli opisano również folder, w którym dostawcy rozwiązań kopiuje różne certyfikaty na publiczny punkt końcowy. 

Certyfikaty z odpowiedniej nazwy DNS dla każdego punktu końcowego infrastruktury publicznych stosu Azure są wymagane. Nazwa DNS każdego punktu końcowego jest wyrażona w formacie:  *&lt;prefiks >.&lt; region >. &lt;fqdn >*. 

Dla danego wdrożenia [region] i [externalfqdn] wartości muszą być zgodne, regionu i nazwy domen zewnętrznych, które wybrano systemu Azure stosu. Na przykład jeśli nazwa regionu *Redmond* i nazwa domeny zewnętrznej *contoso.com*, nazwy DNS musi format *&lt;prefiks >. redmond.contoso.com*.  *&lt;Prefiks >* wartości są ustalonym przez firmę Microsoft do opisywania zabezpieczone przez certyfikatu punktu końcowego. Ponadto  *&lt;prefiks >* wartości infrastruktury zewnętrznych punktów końcowych, które są zależne od usługi Azure stosu, który korzysta z określonego punktu końcowego. 

|Folder wdrożenia|Wymaganego certyfikatu podmiotu i alternatywnej nazwy podmiotu (SAN)|Zakres (dla regionu)|Przestrzeń nazw poddomeny|
|-----|-----|-----|-----|
|Publiczny|portal.*&lt;region>.&lt;fqdn>*|Portale|*&lt;region>.&lt;fqdn>*|
|Portal administratora|adminportal.*&lt;region>.&lt;fqdn>*|Portale|*&lt;region>.&lt;fqdn>*|
|Azure Resource Manager Public|management.*&lt;region>.&lt;fqdn>*|Azure Resource Manager|*&lt;region>.&lt;fqdn>*|
|Administratora usługi Azure Resource Manager|adminmanagement.*&lt;region>.&lt;fqdn>*|Azure Resource Manager|*&lt;region>.&lt;fqdn>*|
|ACS<sup>1</sup>|Jeden certyfikat uniwersalny multi poddomeny z nazwy alternatywnej podmiotu:<br>&#42;.blob.*&lt;region>.&lt;fqdn>*<br>&#42;.queue.*&lt;region>.&lt;fqdn>*<br>&#42;.table.*&lt;region>.&lt;fqdn>*|Magazyn|blob.*&lt;region>.&lt;fqdn>*<br>table.*&lt;region>.&lt;fqdn>*<br>queue.*&lt;region>.&lt;fqdn>*|
|KeyVault|&#42;.vault.*&lt;region>.&lt;fqdn>*<br>(Wieloznaczny certyfikat SSL)|Usługa Key Vault|vault.*&lt;region>.&lt;fqdn>*|
|KeyVaultInternal|&#42;.adminvault.*&lt;region>.&lt;fqdn>*<br>(Wieloznaczny certyfikat SSL)|Wewnętrzny Keyvault|adminvault.*&lt;region>.&lt;fqdn>*|
|
<sup>1</sup> certyfikat usługi ACS wymaga trzech SAN symboli wieloznacznych na jeden certyfikat. Wiele symboli wieloznacznych sieci SAN na jeden certyfikat może nie być obsługiwany przez wszystkich publicznych urzędów certyfikacji. 

W przypadku wdrożenia stosu Azure przy użyciu trybu wdrożenia usługi Azure AD, wystarczy do żądania certyfikatów wymienione w powyższej tabeli. Jednak w przypadku wdrożenia stosu Azure przy użyciu trybu wdrożenia usług AD FS, należy również żądania certyfikatów opisanych w poniższej tabeli:

|Folder wdrożenia|Wymaganego certyfikatu podmiotu i alternatywnej nazwy podmiotu (SAN)|Zakres (dla regionu)|Przestrzeń nazw poddomeny|
|-----|-----|-----|-----|
|ADFS|adfs.*&lt;region>.&lt;fqdn>*<br>(Certyfikat SSL)|ADFS|*&lt;region>.&lt;fqdn>*|
|Graph|graph.*&lt;region>.&lt;fqdn>*<br>(Certyfikat SSL)|Graph|*&lt;region>.&lt;fqdn>*|
|

> [!IMPORTANT]
> Wszystkie certyfikaty, które są wymienione w tej sekcji muszą mieć to samo hasło. 

## <a name="optional-paas-certificates"></a>Opcjonalne certyfikaty PaaS
Jeśli planujesz wdrażania dodatkowych usług Azure stosu PaaS (SQL, MySQL i usługi App Service) po stosu Azure zostało wdrożone i skonfigurowane, należy zażądać dodatkowych certyfikatów, aby pokrywał się punkty końcowe usług PaaS. 

> [!IMPORTANT]
> Certyfikaty, które są używane dla dostawców zasobów usługi aplikacji, SQL i MySQL musi być tego samego głównego urzędu certyfikacji jak globalnych punktów końcowych stosu Azure. 

W poniższej tabeli opisano punktów końcowych i certyfikatów wymaganych dla kart SQL i MySQL i usługi aplikacji. Nie trzeba skopiować do folderu wdrożenia stosu Azure tych certyfikatów. Zamiast tego możesz zapewnić tych certyfikatów, po zainstalowaniu dostawców dodatkowych zasobów. 

|Zakres (dla regionu)|Certyfikat|Wymaganego certyfikatu podmiotu i nazwy alternatywnej podmiotu (SAN)|Przestrzeń nazw poddomeny|
|-----|-----|-----|-----|
|SQL, MySQL|SQL i bazy danych MySQL|&#42;.dbadapter.*&lt;region>.&lt;fqdn>*<br>(Wieloznaczny certyfikat SSL)|dbadapter.*&lt;region>.&lt;fqdn>*|
|App Service|Certyfikat SSL domyślne ruchu w sieci Web|&#42;.appservice.*&lt;region>.&lt;fqdn>*<br>&#42;.scm.appservice.*&lt;region>.&lt;fqdn>*<br>(Obsługa wielu domen wieloznaczny certyfikat SSL<sup>1</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|
|App Service|Interfejs API|api.appservice.*&lt;region>.&lt;fqdn>*<br>(Certyfikat SSL<sup>2</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|
|App Service|FTP|ftp.appservice.*&lt;region>.&lt;fqdn>*<br>(Certyfikat SSL<sup>2</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|
|App Service|SSO|sso.appservice.*&lt;region>.&lt;fqdn>*<br>(Certyfikat SSL<sup>2</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|

<sup>1</sup> wymaga jednego certyfikatu z wielu symboli wieloznacznych alternatywne nazwy podmiotu. Wiele symboli wieloznacznych sieci SAN na jeden certyfikat może nie być obsługiwany przez wszystkich publicznych urzędów certyfikacji 

<sup>2</sup> A &#42;.appservice. *&lt;region >. &lt;fqdn >* certyfikatu przy użyciu symboli wieloznacznych nie można użyć zamiast te trzy certyfikaty (api.appservice. *&lt;region >. &lt;fqdn >*, ftp.appservice. *&lt;region >. &lt;fqdn >*i sso.appservice. *&lt;region >. &lt;fqdn >*. Appservice jawnie wymaga użycia osobnych certyfikatów dla tych punktów końcowych. 

## <a name="learn-more"></a>Dowiedz się więcej
Dowiedz się, jak [generowania certyfikatów PKI dla wdrożenia stosu Azure](azure-stack-get-pki-certs.md). 

## <a name="next-steps"></a>Kolejne kroki
[Integracja tożsamości](azure-stack-integrate-identity.md)

