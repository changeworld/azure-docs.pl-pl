---
title: Jak zdecydować, jeśli usługi Azure AD Domain Services jest odpowiednia dla danego przypadku użycia
description: Porównanie usługi Azure Active Directory Domain Services do kontrolerów domeny możesz
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
manager: daveba
editor: curtand
ms.assetid: 165249d5-e0e7-4ed1-aa26-91a05a87bdc9
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: mstephen
ms.openlocfilehash: 17bb8eb910990e0e9da65491f8dd7361398479a5
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66246467"
---
# <a name="how-to-decide-if-azure-ad-domain-services-is-right-for-your-use-case"></a>Jak zdecydować, jeśli usługi Azure AD Domain Services jest odpowiednia dla danego przypadku użycia

Za pomocą usług domenowych Azure AD można wdrożyć obciążenia w usługach infrastruktury platformy Azure, bez konieczności martwienia się o konserwacja infrastruktury tożsamości na platformie Azure. To zarządzana usługa różni się od typowego wdrożenia usługi Active Directory systemu Windows Server, które wdrażania i administrowania na własną rękę. Usługa jest łatwa do wdrożenia i zapewnia monitorowanie kondycji zautomatyzowane i korygowania. Firma Microsoft stale ewoluuje usługę, aby dodać obsługę typowych scenariuszy wdrażania.

Zdecyduj, czy do korzystania z usług domenowych Azure AD, firma Microsoft zaleca materiałów następujące:

* Przejrzyj listę rzeczy, [funkcje dostępne w usługach domenowych Azure AD](active-directory-ds-features.md).
* Przejrzyj typowe [scenariusze wdrażania usług domenowych Azure AD](scenarios.md).
* Na koniec [Porównanie usług domenowych Azure AD, zrób to sam opcji AD](comparison.md#compare-azure-ad-domain-services-to-diy-ad-domain-in-azure).

## <a name="compare-azure-ad-domain-services-to-diy-ad-domain-in-azure"></a>Porównanie usług domenowych Azure AD do domeny możesz AD na platformie Azure

Poniższa tabela ułatwia wybór między za pomocą usług domenowych Azure AD i zarządzania nimi własnej infrastruktury usługi AD na platformie Azure.

| **Funkcja** | **Azure AD Domain Services** | **"Zrób to sam" AD maszynach wirtualnych platformy Azure** |
| --- |:---:|:---:|
| [**Usługa zarządzana**](comparison.md#managed-service) |**&#x2713;** |**&#x2715;** |
| [**Zabezpieczanie wdrożeń**](comparison.md#secure-deployments) |**&#x2713;** |Administrator musi bezpiecznego wdrażania. |
| [**Serwer DNS**](comparison.md#dns-server) |**&#x2713;** (usługa zarządzanego) |**&#x2713;** |
| [**Uprawnienia administratora domeny lub przedsiębiorstwa**](comparison.md#domain-or-enterprise-administrator-privileges) |**&#x2715;** |**&#x2713;** |
| [**Przyłączanie do domeny**](comparison.md#domain-join) |**&#x2713;** |**&#x2713;** |
| [**Uwierzytelnianie domeny przy użyciu protokołu NTLM i Kerberos**](comparison.md#domain-authentication-using-ntlm-and-kerberos) |**&#x2713;** |**&#x2713;** |
| [**Ograniczone delegowanie protokołu Kerberos**](comparison.md#kerberos-constrained-delegation)|oparte na zasobach|oparte na zasobach i oparte na koncie|
| [**Niestandardowe struktury jednostek organizacyjnych**](comparison.md#custom-ou-structure) |**&#x2713;** |**&#x2713;** |
| [**Rozszerzenia schematu**](comparison.md#schema-extensions) |**&#x2715;** |**&#x2713;** |
| [**Relacje zaufania lasu domeny usługi AD**](comparison.md#ad-domain-or-forest-trusts) |**&#x2715;** |**&#x2713;** |
| [**Przeczytaj LDAP**](comparison.md#ldap-read) |**&#x2713;** |**&#x2713;** |
| [**Bezpieczny protokół LDAP (LDAPS)** ](comparison.md#secure-ldap) |**&#x2713;** |**&#x2713;** |
| [**Zapis LDAP**](comparison.md#ldap-write) |**&#x2715;** |**&#x2713;** |
| [**Zasady grupy**](comparison.md#group-policy) |**&#x2713;** |**&#x2713;** |
| [**Wdrożeniom rozproszonym geograficznie**](comparison.md#geo-dispersed-deployments) |**&#x2715;** |**&#x2713;** |

#### <a name="managed-service"></a>Usługa zarządzana

Usługa Azure AD Domain Services domeny są zarządzane przez firmę Microsoft. Nie masz już martwić się o poprawek i aktualizacji, monitorowania, kopii zapasowych i zapewnienie dostępności domeny. Te zadania zarządzania są oferowane jako usługa Microsoft Azure dla Twoich domen zarządzanych.

#### <a name="secure-deployments"></a>Zabezpieczanie wdrożeń

Bezpiecznie domena zarządzana jest zablokowana zgodnie z zaleceń dotyczących zabezpieczeń firmy Microsoft w przypadku wdrożeń usługi AD. Zalecenia te wynikają z zespołu produktu AD wieloletnim doświadczeniu w zakresie inżynierii i obsługa wdrożeniami usługi AD. W przypadku wdrożeń utworzyliśmy należy wykonać kroki wdrażania właściwe do blokowania, dół/secure wdrożenia.

#### <a name="dns-server"></a>Serwer DNS

Domeny zarządzanej usług domenowych Azure AD zawiera zarządzane usługi DNS. Członkowie grupy "Administratorzy usługi AAD DC" można zarządzać DNS w domenie zarządzanej. Członkowie tej grupy są podane pełne uprawnienia do administrowania systemem DNS dla domeny zarządzanej. Zarządzanie systemem DNS może być wykonywane przy użyciu "Konsola administracyjna DNS" uwzględniony w pakiecie narzędzia administracji zdalnej serwera (RSAT).
[Więcej informacji](manage-dns.md)

#### <a name="domain-or-enterprise-administrator-privileges"></a>Uprawnienia do domeny lub administratora przedsiębiorstwa

Te podniesione uprawnienia nie są oferowane w domenie zarządzanej usługi AAD DS. Domen zarządzanych aplikacji, które wymagają tych podwyższonym poziomem uprawnień nie można wdrożyć przed DS usługi AAD. Mniejszy podzbiór uprawnień administracyjnych jest dostępne dla członków grupy administrację delegowaną, o nazwie "Administratorzy usługi AAD DC". Te uprawnienia obejmują uprawnienia, aby skonfigurować usługę DNS, skonfigurować zasady grupy, uzyskać uprawnienia administratora na komputerach przyłączonych do domeny itd.

#### <a name="domain-join"></a>Przyłączanie do domeny

Maszyny wirtualne mogą dołączyć do domeny zarządzanej, podobnie jak sposób dołączania komputerów do domeny usługi AD.

#### <a name="domain-authentication-using-ntlm-and-kerberos"></a>Uwierzytelnianie domeny przy użyciu protokołu NTLM i Kerberos

Za pomocą usług domenowych Azure AD można użyć poświadczeń firmowych, do uwierzytelniania za pomocą domeny zarządzanej. Poświadczenia są synchronizowane z dzierżawą usługi Azure AD. Zsynchronizowane dzierżawy Azure AD Connect zapewnia, że zmiany wprowadzone poświadczenia lokalnych są zsynchronizowane z usługą Azure AD. Z Instalatorem obsługiwanymi domeny, konieczne może być konfigurowanie domeny usługi AD relację zaufania z lokalnej usługi AD dla użytkowników do uwierzytelniania przy użyciu swoich poświadczeń firmowych. Alternatywnie może być konieczne Konfigurowanie replikacji usługi AD, aby upewnić się, że haseł użytkowników synchronizować z maszynami wirtualnymi kontrolera domeny platformy Azure.

#### <a name="kerberos-constrained-delegation"></a>Ograniczone delegowanie protokołu Kerberos

Nie masz uprawnienia "Administrator domeny" w domenie zarządzanej usługi Active Directory Domain Services. W związku z tym nie można skonfigurować oparte na koncie (tradycyjny) ograniczonego delegowania protokołu Kerberos. Można jednak skonfigurować bezpieczniejsze delegowania ograniczonego opartego na zasobach.
[Więcej informacji](deploy-kcd.md)

#### <a name="custom-ou-structure"></a>Niestandardowe struktury jednostek organizacyjnych

Członkowie grupy "Administratorzy usługi AAD DC" można tworzyć niestandardowych jednostkach organizacyjnych w domenie zarządzanej. Użytkownicy, którzy tworzą niestandardowe jednostki organizacyjne są przyznawane pełne uprawnienia administracyjne w jednostce Organizacyjnej.
[Więcej informacji](create-ou.md)

#### <a name="schema-extensions"></a>Rozszerzenia schematu

Nie można rozszerzyć schemat podstawowy do domeny zarządzanej usług domenowych Azure AD. W związku z tym aplikacje korzystające z rozszerzenia schematu usługi AD (na przykład nowych atrybutów w obiekcie użytkownika) nie można zwiększyć i przesunięte do domeny DS usługi AAD.

#### <a name="ad-domain-or-forest-trusts"></a>Domeny usługi AD lub relacji zaufania między lasami

Nie można skonfigurować domen zarządzanych do skonfigurowania (dla ruchu przychodzącego/wychodzącego) relacji zaufania z innymi domenami. W związku z tym scenariusze wdrażania lasu zasobów nie można używać usługi Azure AD Domain Services. Podobnie wdrożeń, w których nie chcesz synchronizować hasła do usługi Azure AD nie można używać usługi Azure AD Domain Services.

#### <a name="ldap-read"></a>Odczyt LDAP

Domena zarządzana obsługuje odczyt obciążeń LDAP. W związku z tym można wdrożyć aplikacje, które wykonują operacje odczytu LDAP dla domeny zarządzanej.

#### <a name="secure-ldap"></a>Protokół Secure LDAP

Można skonfigurować usługi domenowe Azure AD, aby zapewnić dostęp do Twojej domeny zarządzanej za pośrednictwem Internetu w tym secure LDAP.
[Więcej informacji](configure-ldaps.md)

#### <a name="ldap-write"></a>Zapis LDAP

Domena zarządzana jest tylko do odczytu do obiektów użytkowników. Aplikacje, które wykonują operacje zapisu LDAP atrybutów obiektu użytkownika w związku z tym, nie działają w domenie zarządzanej. Ponadto hasła użytkowników nie można zmienić z w obrębie domeny zarządzanej. Innym przykładem może być zmiana członkostwa w grupie lub grupy atrybutów w obrębie domeny zarządzanej, co jest niedozwolone. Jednak wszelkie zmiany atrybutów użytkownika lub hasła w usłudze Azure AD (za pośrednictwem programu PowerShell/portal Azure) lub lokalnej usługi AD są synchronizowane z domeny zarządzanej usługi Katalogowej usługi AAD.

#### <a name="group-policy"></a>Zasady grupy

Ma wbudowanego obiektu zasad grupy Każdy dla kontenerów "Komputery usługi AAD DC" i "Użytkownicy usługi AAD DC". Można dostosować te wbudowane obiekty zasad grupy do skonfigurowania zasad grupy. Członkowie grupy "Administratorzy usługi AAD DC" można również utworzyć niestandardowe obiekty zasad grupy i łączą je z istniejących jednostek organizacyjnych (w tym niestandardowych jednostek organizacyjnych).
[Więcej informacji](manage-group-policy.md)

#### <a name="geo-dispersed-deployments"></a>Wdrażanie rozproszone geograficznie

Usługa Azure AD Domain Services domeny zarządzane są dostępne w jednej sieci wirtualnej na platformie Azure. W przypadku scenariuszy, które wymagają kontrolerów domeny, które ma być dostępny w wielu regionach platformy Azure na całym świecie konfigurowanie kontrolerów domeny na maszynach wirtualnych Azure IaaS może być lepszą alternatywą.

## <a name="do-it-yourself-diy-ad-deployment-options"></a>"Zrób to sam" Opcje wdrażania usługi AD (DIY)

Przypadki użycia wdrożenia może być konieczne niektóre funkcje oferowane przez instalację systemu Windows Server AD. W takich przypadkach należy wziąć pod uwagę jedną z poniższych opcji, zrób to sam (DIY):

* **Domena chmury autonomicznych:** Można skonfigurować autonomiczny "domena chmury" przy użyciu maszyn wirtualnych platformy Azure, które zostały skonfigurowane jako kontrolery domeny. Ta infrastruktura nie integrują się z lokalnym środowisku usługi AD. Ta opcja wymaga innego zestawu "poświadczenia chmury" do logowania/administrowania maszynami wirtualnymi w chmurze.
* **Wdrażanie lasu zasobów:** Możesz skonfigurować domenę w topologii lasu zasobów przy użyciu maszyn wirtualnych skonfigurowane jako kontrolery domeny. Następnie należy skonfigurować relację zaufania z usługi AD z lokalnej usługi AD środowiska. Możesz przyłączania do domeny komputery (maszyny wirtualne platformy Azure) dla tego lasu zasobów w chmurze. Uwierzytelnianie użytkownika odbywa się za pośrednictwem jednego połączenia sieci VPN/ExpressRoute do katalogu lokalnego.
* **Rozszerzanie domenę lokalną na platformę Azure:** Usługa Azure virtual network możesz połączyć z siecią lokalną za pomocą połączenia sieci VPN/ExpressRoute. Taka konfiguracja zapewnia maszyn wirtualnych platformy Azure zostanie przyłączony do lokalnej usługi AD. Innym sposobem jest podwyższenie poziomu kontrolerów domeny repliki domeny lokalnych na platformie Azure jako Maszynę wirtualną. Możesz następnie skonfigurować go do replikacji za pośrednictwem połączenia sieci VPN/ExpressRoute do katalogu lokalnego. Ten tryb wdrożenia skutecznie rozszerza domenę lokalną na platformę Azure.

> [!NOTE]
> Należy określić, czy opcja możesz lepiej jest odpowiedni dla przypadki użycia wdrożenia. Należy wziąć pod uwagę [udostępniania opinii](contact-us.md) która pomoże nam zrozumieć, jakie funkcje może pomóc w przyszłości wybrano usług domenowych Azure AD. Ta opinia pomoże nam rozwijać usługę, aby lepiej dopasować się do swoich potrzeb wdrożenia i przypadki użycia.
>
>

Opublikowane dotychczas [wskazówki dotyczące wdrażania systemu Windows Server Active Directory na maszynach wirtualnych Azure](/windows-server/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100) pomagające w ułatwienia możesz instalacji.

## <a name="related-content"></a>Powiązana zawartość

* [Funkcje — Azure AD Domain Services](active-directory-ds-features.md)
* [Scenariusze wdrażania — usługi domenowe Azure AD](scenarios.md)
* [Wytyczne dotyczące wdrażania Active Directory systemu Windows Server na maszynach wirtualnych platformy Azure](/windows-server/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100)
