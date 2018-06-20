---
title: 'Usługi domenowe Azure AD: Porównanie usługi Azure AD z usług domenowych do kontrolerów domeny DIY | Dokumentacja firmy Microsoft'
description: Porównanie usług domenowych Azure Active Directory do kontrolerów domeny DIY
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 165249d5-e0e7-4ed1-aa26-91a05a87bdc9
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/07/2017
ms.author: maheshu
ms.openlocfilehash: 550354ceab3026cfd724cd77c3266b22682e4431
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36212575"
---
# <a name="how-to-decide-if-azure-ad-domain-services-is-right-for-your-use-case"></a>Jak stwierdzić, czy usługi domenowe Azure AD jest odpowiednia dla przypadek użycia
Z usług domenowych Azure AD można wdrażać obciążeń w usługi infrastruktury platformy Azure, nie martwiąc się o zachowaniu infrastruktury tożsamości na platformie Azure. Ta usługa zarządzanych różni się od typowe wdrożenie usługi Active Directory systemu Windows Server, wdrażania i administrowania samodzielnie. Usługa jest łatwa do wdrożenia i zapewnia monitorowanie kondycji automatycznych i korygowania. Firma Microsoft stale ewoluuje usługę, aby dodać obsługę typowych scenariuszy wdrożeń.

Umożliwia określenie, czy do korzystania z usług domenowych Azure AD zalecamy następujące materiałów:

* Lista [funkcje oferowane przez usługi domenowe Azure AD](active-directory-ds-features.md).
* Przejrzyj typowe [scenariusze wdrażania usług domenowych Azure AD](active-directory-ds-scenarios.md).
* Na koniec [Porównanie usług domenowych Azure AD, zrób opcji AD](active-directory-ds-comparison.md#compare-azure-ad-domain-services-to-diy-ad-domain-in-azure).

## <a name="compare-azure-ad-domain-services-to-diy-ad-domain-in-azure"></a>Porównanie usług domenowych Azure AD do domeny DIY AD na platformie Azure
Poniższa tabela ułatwia wybór między przy użyciu usług domenowych Azure AD i zarządzanie nimi własną infrastrukturę AD na platformie Azure.

| **Funkcja** | **Usługi domenowe Azure AD** | **"Zrób" AD na maszynach wirtualnych platformy Azure** |
| --- |:---:|:---:|
| [**Zarządzana usługa**](active-directory-ds-comparison.md#managed-service) |**&#x2713;** |**&#x2715;** |
| [**Zabezpieczanie wdrożenia**](active-directory-ds-comparison.md#secure-deployments) |**&#x2713;** |Administrator musi bezpiecznego wdrażania. |
| [**Serwer DNS**](active-directory-ds-comparison.md#dns-server) |**&#x2713;**(usługa zarządzane) |**&#x2713;** |
| [**Uprawnienia administratora domeny lub Enterprise**](active-directory-ds-comparison.md#domain-or-enterprise-administrator-privileges) |**&#x2715;** |**&#x2713;** |
| [**Przyłączanie do domeny**](active-directory-ds-comparison.md#domain-join) |**&#x2713;** |**&#x2713;** |
| [**Uwierzytelnianie domeny przy użyciu protokołu NTLM i Kerberos**](active-directory-ds-comparison.md#domain-authentication-using-ntlm-and-kerberos) |**&#x2713;** |**&#x2713;** |
| [**Ograniczone delegowanie protokołu Kerberos**](active-directory-ds-comparison.md#kerberos-constrained-delegation)|oparte na zasobach|oparte na zasobach & na podstawie konta|
| [**Niestandardowe struktury jednostek organizacyjnych**](active-directory-ds-comparison.md#custom-ou-structure) |**&#x2713;** |**&#x2713;** |
| [**Rozszerzenia schematu**](active-directory-ds-comparison.md#schema-extensions) |**&#x2715;** |**&#x2713;** |
| [**Relacje zaufania lasu domeny usługi AD**](active-directory-ds-comparison.md#ad-domain-or-forest-trusts) |**&#x2715;** |**&#x2713;** |
| [**LDAP do odczytu**](active-directory-ds-comparison.md#ldap-read) |**&#x2713;** |**&#x2713;** |
| [**Bezpieczny protokół LDAP (LDAPS)**](active-directory-ds-comparison.md#secure-ldap) |**&#x2713;** |**&#x2713;** |
| [**Zapis LDAP**](active-directory-ds-comparison.md#ldap-write) |**&#x2715;** |**&#x2713;** |
| [**Zasady grupy**](active-directory-ds-comparison.md#group-policy) |**&#x2713;** |**&#x2713;** |
| [**Rozproszona geograficznie wdrożenia**](active-directory-ds-comparison.md#geo-dispersed-deployments) |**&#x2715;** |**&#x2713;** |

#### <a name="managed-service"></a>Zarządzana usługa
Azure domeny usług domenowych AD są zarządzane przez firmę Microsoft. Nie trzeba martwić poprawki, aktualizacje, monitorowanie kopii zapasowych i zapewnienie dostępności domeny. Te zadania zarządzania są oferowane jako usługa przez Microsoft Azure w domenach zarządzanych.

#### <a name="secure-deployments"></a>Zabezpieczanie wdrożenia
Bezpieczne domeny zarządzanej jest zablokowana zgodnie z harmonogramem zalecenia dotyczące zabezpieczeń firmy Microsoft dla wdrożeń usługi AD. Te zalecenia wynika z zespołu produktu AD dekad doświadczenia inżynierii i obsługi wdrożenia usługi AD. W przypadku wdrożeń Zrób należy wykonać kroki wdrażania, aby zablokować, dół/secure wdrożenia.

#### <a name="dns-server"></a>Serwer DNS
Domeny zarządzanej usług domenowych Azure AD zawiera zarządzanych usług DNS. Członkowie grupy "Administratorzy kontrolera domeny usługi AAD" można zarządzać DNS dla domeny zarządzanej. Członkowie tej grupy są podane pełne uprawnienia do administrowania systemem DNS dla domeny zarządzanej. Zarządzanie systemem DNS mogą być wykonywane za pomocą "konsoli administracyjnej DNS" uwzględniony w pakiecie narzędzia administracji zdalnej serwera (RSAT).
[Więcej informacji](active-directory-ds-admin-guide-administer-dns.md)

#### <a name="domain-or-enterprise-administrator-privileges"></a>Uprawnienia do domeny lub administratora przedsiębiorstwa
Te podniesione uprawnienia nie są dostępne w domenie zarządzanej usługi AAD DS. Domen zarządzanych aplikacji, które wymagają tych podniesione uprawnienia nie można wdrożyć przed DS usługi AAD. Mniejszego podzestawu uprawnienia administracyjne są dostępne dla członków grupy administracji delegowanej o nazwie "Administratorzy usługi AAD kontrolera domeny". Uprawnienia te obejmują uprawnienia do konfigurowania serwera DNS, skonfigurować zasady grupy, uzyskanie uprawnień administratora na komputerach przyłączonych do domeny itp.

#### <a name="domain-join"></a>Przyłączanie do domeny
Maszyny wirtualne można przyłączyć do domeny zarządzanej, podobnie jak przyłączania komputerów do domeny usługi AD.

#### <a name="domain-authentication-using-ntlm-and-kerberos"></a>Uwierzytelnianie domeny przy użyciu protokołu NTLM i Kerberos
Z usług domenowych Azure AD poświadczenia firmowe, służy do uwierzytelniania za pomocą domeny zarządzanej. Poświadczenia są zsynchronizowane z dzierżawą usługi Azure AD. Zsynchronizowane dzierżawy Azure AD Connect zapewnia, że zmiany wprowadzone poświadczenia lokalnego są synchronizowane z usługą Azure AD. Z konfiguracją Zrób domeny, konieczne może być konfigurowanie domeny usługi AD relację zaufania z lokalnymi AD użytkownikom uwierzytelnianie przy użyciu poświadczeń firmowych. Alternatywnie może być konieczne Konfigurowanie replikacji AD, aby upewnić się, że hasła użytkowników synchronizacji do sieci maszyn wirtualnych kontrolerów domeny platformy Azure.

#### <a name="kerberos-constrained-delegation"></a>Ograniczone delegowanie protokołu Kerberos
Nie masz uprawnień administratora domeny w domenie zarządzanej usług domenowych w usłudze Active Directory. W związku z tym nie można skonfigurować na podstawie konta (tradycyjny) ograniczone delegowanie protokołu Kerberos. Można jednak skonfigurować bezpieczniejsze oparte na zasobach ograniczonego delegowania.
[Więcej informacji](active-directory-ds-enable-kcd.md)

#### <a name="custom-ou-structure"></a>Niestandardowe struktury jednostek organizacyjnych
Członkowie grupy "Administratorzy kontrolera domeny usługi AAD" można tworzyć niestandardowe jednostek organizacyjnych w domenie zarządzanej. Użytkownicy, którzy tworzą niestandardowej jednostki organizacyjne są przyznawane pełne uprawnienia administracyjne w jednostce Organizacyjnej.
[Więcej informacji](active-directory-ds-admin-guide-create-ou.md)

#### <a name="schema-extensions"></a>Rozszerzenia schematu
Nie można rozszerzyć schematu podstawowego domeny zarządzanej usług domenowych Azure AD. W związku z tym aplikacje korzystające z rozszerzenia schematu usług AD (na przykład nowych atrybutów obiektu użytkownika) nie można unosiło i przesunięte do domen DS usługi AAD.

#### <a name="ad-domain-or-forest-trusts"></a>AD domeny lub zaufanie lasu
Nie można skonfigurować domen zarządzanych do skonfigurowania (przychodzącego/wychodzącego) relacji zaufania z innymi domenami. W związku z tym scenariuszy wdrażania lasu zasobów nie można użyć usług domenowych Azure AD. Podobnie wdrożeń, których nie chcesz synchronizować hasła do usługi Azure AD nie można użyć usług domenowych Azure AD.

#### <a name="ldap-read"></a>Odczyt LDAP
Domeny zarządzanej obsługuje LDAP odczytu obciążeń. W związku z tym można wdrażać aplikacje, które wykonują operacje odczytu LDAP względem domeny zarządzanej.

#### <a name="secure-ldap"></a>Secure LDAP
Można skonfigurować usługi domenowe Azure AD, aby zapewnić bezpieczny dostęp LDAP do domeny zarządzanej, łącznie z Internetem.
[Więcej informacji](active-directory-ds-admin-guide-configure-secure-ldap.md)

#### <a name="ldap-write"></a>Zapis LDAP
Domeny zarządzanej jest tylko do odczytu obiektów użytkownika. Aplikacje, które wykonują operacje zapisu LDAP atrybutów obiektu użytkownika w związku z tym nie działają w domeny zarządzanej. Ponadto hasła użytkownika nie można zmienić z wewnątrz domeny zarządzanej. Innym przykładem może być zmiana członkostwa grupy lub grupy atrybutów w obrębie domeny zarządzanej, co jest niedozwolone. Jednak wszystkie dokonane zmiany atrybutów użytkownika lub hasła w usłudze Azure AD (za pośrednictwem portalu programu PowerShell/Azure) lub lokalnie AD są synchronizowane z domeny zarządzanej usługi AAD DS.

#### <a name="group-policy"></a>Zasady grupy
Brak wbudowanego obiektu zasad grupy poszczególnych kontenerów "AADDC komputery" i "Użytkownicy AADDC". Można dostosować te wbudowane obiekty zasad grupy do konfigurowania zasad grupy. Członkowie grupy "Administratorzy kontrolera domeny usługi AAD" można utworzyć niestandardowe obiekty zasad grupy i łączenia ich z istniejących jednostek organizacyjnych (w tym niestandardowe jednostek organizacyjnych).
[Więcej informacji](active-directory-ds-admin-guide-administer-group-policy.md)

#### <a name="geo-dispersed-deployments"></a>Rozproszone geograficznie wdrożenia
Azure domen zarządzanych w usługach domenowych AD są dostępne w jednej sieci wirtualnych na platformie Azure. Scenariusze, które wymagają kontrolerów domeny, które ma być dostępny w wielu regionach platformy Azure w całym świecie, w przypadku konfigurowania kontrolerów domeny w maszynach wirtualnych IaaS platformy Azure może być lepszą alternatywę.


## <a name="do-it-yourself-diy-ad-deployment-options"></a>Opcje wdrażania (DIY) AD "Zrób"
Przypadki użycia wdrożenia może być konieczne niektóre możliwości oferowane przez instalację systemu Windows Server AD. W takich sytuacjach należy wziąć pod uwagę jedną z poniższych opcji, zrób (DIY):

* **Domeny chmury autonomicznej:** można skonfigurować autonomiczny "domenę chmury" przy użyciu maszyn wirtualnych platformy Azure, które zostały skonfigurowane jako kontrolery domeny. Ta infrastruktura nie jest zintegrowana z lokalnej usługi AD środowiska. Ta opcja wymaga inny zestaw "poświadczeń w chmurze" do logowania/administrowania maszynami wirtualnymi w chmurze.
* **Wdrażanie lasu zasobu:** można skonfigurować w domenie w topologii lasu zasobów, przy użyciu maszyn wirtualnych platformy Azure skonfigurowane jako kontrolery domeny. Następnie należy skonfigurować relację zaufania usługi AD z lokalnej usługi AD środowiska. Przyłączanie do domeny komputerów (maszynach wirtualnych platformy Azure) można w tym lesie zasobów w chmurze. Uwierzytelnianie użytkownika odbywa się za pośrednictwem jednego połączenia sieci VPN/ExpressRoute do katalogu lokalnego.
* **Rozszerzanie domeny lokalnej do platformy Azure:** sieci wirtualnej platformy Azure można połączyć z siecią lokalną przy użyciu połączenia sieci VPN/ExpressRoute. Taka konfiguracja zapewnia maszynach wirtualnych platformy Azure do lokalnej usługi AD. Alternatywą jest podwyższenie poziomu kontrolerów domeny repliki domeny lokalnej na platformie Azure jako Maszynę wirtualną. Następnie jednak można skonfigurować go do replikowania za pośrednictwem połączenia sieci VPN/ExpressRoute do katalogu lokalnego. W tym trybie wdrożenia skutecznie rozszerza domeny lokalnej na platformie Azure.

> [!NOTE]
> Należy określić, czy opcja DIY lepiej nadaje się do wdrożenia przypadki użycia. Należy wziąć pod uwagę [udostępnianie opinii](active-directory-ds-contact-us.md) aby pomóc nam zrozumieć, jakie funkcje może pomóc w przyszłości wybrano usługi domenowe Azure AD. Ta opinia pomoże nam rozwijać usługę, aby lepiej własnych potrzeb wdrożenia i przypadki użycia.
>
>

Firma Microsoft opublikowano [wskazówki dotyczące wdrażania systemu Windows Server Active Directory na maszynach wirtualnych Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx) aby ułatwić DIY instalacji.

## <a name="related-content"></a>Powiązana zawartość
* [Funkcje — usługi domenowe Azure AD](active-directory-ds-features.md)
* [Scenariusze wdrażania - usługi domenowe Azure AD](active-directory-ds-scenarios.md)
* [Wskazówki dotyczące wdrażania systemu Windows serwer usługi Active Directory na maszynach wirtualnych Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx)
