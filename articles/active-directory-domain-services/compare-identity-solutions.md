---
title: Porównywanie usług opartych na usłudze Active Directory na platformie Azure | Dokumenty firmy Microsoft
description: W tym ekstucie porównasz różne oferty tożsamości dla Usług domenowych Active Directory, usługi Azure Active Directory i Usługi domenowe Active Directory platformy Azure.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: overview
ms.date: 01/22/2020
ms.author: iainfou
ms.openlocfilehash: d2495605cccf658b15e812fd85fd65671e84d15b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "76544280"
---
# <a name="compare-self-managed-active-directory-domain-services-azure-active-directory-and-managed-azure-active-directory-domain-services"></a>Porównanie samodzielnie zarządzanych Usług domenowych Active Directory, usługi Azure Active Directory i zarządzanych Usług domenowych Usługi domenowe Usługi active directory platformy Azure

Aby zapewnić aplikacjom, usługom lub urządzeniom dostęp do tożsamości centralnej, istnieją trzy typowe sposoby korzystania z usług opartych na usłudze Active Directory na platformie Azure. Ten wybór w rozwiązaniach tożsamości zapewnia elastyczność korzystania z najbardziej odpowiedniego katalogu dla potrzeb organizacji. Jeśli na przykład użytkownik zarządza głównie użytkownikami w chmurze, którzy uruchamiają urządzenia przenośne, tworzenie i uruchamianie własnego rozwiązania do obsługi tożsamości usług domenowych Active Directory (AD DS) może nie mieć sensu. Zamiast tego można po prostu użyć usługi Azure Active Directory.

Mimo że trzy rozwiązania tożsamości oparte na usłudze Active Directory mają wspólną nazwę i technologię, są one przeznaczone do świadczenia usług spełniania różnych wymagań klientów. Na wysokim poziomie te rozwiązania tożsamości i zestawy funkcji są:

* **Usługi domenowe Active Directory (AD DS)** — serwer LDAP (LDAP) z obsługą przedsiębiorstwa, który udostępnia kluczowe funkcje, takie jak tożsamość i uwierzytelnianie, zarządzanie obiektami komputera, zasady grupy i relacje zaufania.
    * Usługi AD DS są centralnym składnikiem w wielu organizacjach z lokalnym środowiskiem IT i zapewniają podstawowe funkcje uwierzytelniania konta użytkownika i zarządzania komputerem.
* **Usługa Azure Active Directory (Azure AD)** — oparte na chmurze zarządzanie tożsamościami i urządzeniami przenośnymi, które zapewnia usługi kont użytkowników i uwierzytelniania dla zasobów, takich jak Office 365, witryna Azure portal lub aplikacje SaaS.
    * Usługa Azure AD może być synchronizowana z lokalnym środowiskiem usług AD DS, aby zapewnić jedną tożsamość użytkownikom, którzy działają natywnie w chmurze.
* **Usługi domenowe Usługi w domenie Active Directory platformy Azure (Usługi Azure AD DS)** — zapewnia usługi domeny zarządzanej z podzbiorem w pełni zgodnych tradycyjnych funkcji usług AD DS, takich jak sprzężenie do domeny, zasady grupy, LDAP i uwierzytelnianie Kerberos / NTLM.
    * Usługi Azure AD DS integruje się z usługą Azure AD, która sama w sobie może synchronizować z lokalnym środowiskiem usług AD DS. Ta możliwość rozszerza przypadki użycia tożsamości centralnej do tradycyjnych aplikacji sieci web, które są uruchamiane na platformie Azure w ramach strategii lift-and-shift.

W tym artykule omówienie porównuje i kontrastuje sposób, w jaki te rozwiązania tożsamości mogą współpracować lub będą używane niezależnie, w zależności od potrzeb organizacji.

## <a name="azure-ad-ds-and-self-managed-ad-ds"></a>Usługi Azure AD DS i samodzielnie zarządzane usługi AD DS

Jeśli masz aplikacje i usługi, które potrzebują dostępu do tradycyjnych mechanizmów uwierzytelniania, takich jak Kerberos lub NTLM, istnieją dwa sposoby świadczenia Usług domenowych Active Directory w chmurze:

* Domena *zarządzana* utworzona przy użyciu usług domenowych Active Directory azure (Usługi Usługi Azure AD DS). Firma Microsoft tworzy wymagane zasoby i zarządza nimi.
* Samodzielnie *zarządzana* domena tworzona i konfigurowana przy użyciu tradycyjnych zasobów, takich jak maszyny wirtualne (maszyny wirtualne), system operacyjny gościa systemu Windows Server i usługi domenowe Active Directory (AD DS). Następnie należy kontynuować administrowanie tymi zasobami.

Dzięki usługom Azure AD DS podstawowe składniki usługi są wdrażane i obsługiwane przez firmę Microsoft jako środowisko domeny *zarządzanej.* Infrastruktury usług AD DS nie wdraża, nie wdrażasz infrastruktury usług AD DS, takich jak maszyny wirtualne, system operacyjny Windows Server lub kontrolery domeny (DC) — nie wdrażasz, nie można go zabezpieczać ani zabezpieczać infrastruktury usług AD DS.

Usługa Azure AD DS udostępnia mniejszy podzbiór funkcji do tradycyjnego środowiska usług AD DS zarządzanego przez siebie, co zmniejsza niektóre złożoność projektowania i zarządzania. Na przykład nie ma żadnych lasów, domen, lokacji i łączy replikacji usługi AD do projektowania i obsługi. W przypadku aplikacji i usług, które działają w chmurze i potrzebują dostępu do tradycyjnych mechanizmów uwierzytelniania, takich jak Kerberos lub NTLM, usługa Azure AD DS zapewnia środowisko domeny zarządzanej przy minimalnej ilości nakładów administracyjnych.

Podczas wdrażania i uruchamiania samodzielnie zarządzanego środowiska usług AD DS należy zachować wszystkie skojarzone składniki infrastruktury i katalogów. Istnieje dodatkowe obciążenie związane z konserwacją w samodzielnie zarządzanym środowisku usług AD DS, ale następnie można wykonać dodatkowe zadania, takie jak rozszerzanie schematu lub tworzenie relacji zaufania lasu.

Typowe modele wdrażania dla samodzielnie zarządzanego środowiska usług AD DS, które zapewnia tożsamość aplikacjom i usługom w chmurze, są następujące:

* **Autonomiczne usługi AD DS tylko w chmurze** — maszyny wirtualne platformy Azure są skonfigurowane jako kontrolery domeny i tworzone jest oddzielne środowisko usług AD DS tylko w chmurze. To środowisko usług AD DS nie integruje się z lokalnym środowiskiem usług AD DS. Inny zestaw poświadczeń jest używany do logowania się i administrowania maszynami wirtualnymi w chmurze.
* **Wdrażanie lasu zasobów** — maszyny wirtualne platformy Azure są skonfigurowane jako kontrolery domeny i tworzona jest domena usług AD DS, która jest częścią istniejącego lasu. Relacja zaufania jest następnie konfigurowana w lokalnym środowisku usług AD DS. Inne maszyny wirtualne platformy Azure można przyłączyć domeny do tego lasu zasobów w chmurze. Uwierzytelnianie użytkownika jest uruchamiane za pośrednictwem połączenia VPN / ExpressRoute z lokalnym środowiskiem usług AD DS.
* **Rozszerzanie domeny lokalnej na platformę Azure** — sieć wirtualna platformy Azure łączy się z siecią lokalną przy użyciu połączenia VPN /ExpressRoute. Maszyny wirtualne platformy Azure łączą się z tą siecią wirtualną platformy Azure, co umożliwia im dołączanie do domeny w lokalnym środowisku usług AD DS.
    * Alternatywą jest tworzenie maszyn wirtualnych platformy Azure i promowanie ich jako repliki kontrolerów domeny z lokalnej domeny usług AD DS. Te kontrolery domeny są replikowane za pośrednictwem połączenia VPN / ExpressRoute z lokalnym środowiskiem usług AD DS. Lokalna domena usług AD DS jest skutecznie rozszerzana na platformę Azure.

W poniższej tabeli przedstawiono niektóre funkcje, które mogą być potrzebne w organizacji, oraz różnice między zarządzaną domeną usług Azure AD DS lub samodzielnie zarządzaną domeną usług AD DS:

| **Funkcja** | **Azure AD DS** | **Samodzielnie zarządzane usługi AD DS** |
| ----------- |:---------------:|:----------------------:|
| **Usługa zarządzana**                               | **&#x2713;** | **&#x2715;** |
| **Bezpieczne wdrożenia**                            | **&#x2713;** | Administrator zabezpiecza wdrożenie |
| **Serwer DNS**                                    | **&#x2713;** (usługa zarządzana) |**&#x2713;** |
| **Uprawnienia administratora domeny lub przedsiębiorstwa** | **&#x2715;** | **&#x2713;** |
| **Przyłączanie do domeny**                                   | **&#x2713;** | **&#x2713;** |
| **Uwierzytelnianie domeny przy użyciu ntlm i protokołu Kerberos** | **&#x2713;** | **&#x2713;** |
| **Delegowanie z ograniczeniami protokołu Kerberos**               | Oparte na zasobach | Oparte na zasobach & oparte na kontach|
| **Niestandardowa struktura OU**                           | **&#x2713;** | **&#x2713;** |
| **Zasady grupy**                                  | **&#x2713;** | **&#x2713;** |
| **Rozszerzenia schematu**                             | **&#x2715;** | **&#x2713;** |
| **Relacje zaufania domeny /lasu usługi AD**                     | **&#x2713;** (tylko wychodzące zaufania lasu wychodzącego) | **&#x2713;** |
| **Bezpieczny protokół LDAP (LDAPS)**                           | **&#x2713;** | **&#x2713;** |
| **Odczyt LDAP**                                     | **&#x2713;** | **&#x2713;** |
| **Zapis LDAP**                                    | **&#x2713;** (w domenie zarządzanej) | **&#x2713;** |
| **Wdrożenia rozproszone geograficznie**                   | **&#x2715;** | **&#x2713;** |

## <a name="azure-ad-ds-and-azure-ad"></a>Usługi Azure AD DS i usługi Azure AD

Usługa Azure AD umożliwia zarządzanie tożsamością urządzeń używanych przez organizację i kontrolowanie dostępu do zasobów firmowych z tych urządzeń. Użytkownicy mogą również zarejestrować swoje urządzenie osobiste (model bring-your-own (BYO) za pomocą usługi Azure AD, która zapewnia urządzeniu tożsamość. Usługa Azure AD następnie uwierzytelnia urządzenie, gdy użytkownik loguje się do usługi Azure AD i używa urządzenia, aby uzyskać dostęp do zabezpieczonych zasobów. Urządzeniem można zarządzać za pomocą oprogramowania do zarządzania urządzeniami przenośnymi (MDM), takiego jak microsoft intune. Ta umiejętność zarządzania umożliwia ograniczenie dostępu do poufnych zasobów do urządzeń zarządzanych i zgodnych z zasadami.

Tradycyjne komputery i laptopy mogą również dołączyć do usługi Azure AD. Ten mechanizm oferuje te same korzyści z rejestrowania urządzenia osobistego za pomocą usługi Azure AD, takie jak umożliwienie użytkownikom logowania się na urządzeniu przy użyciu poświadczeń firmowych.

Urządzenia przyłączone do usługi Azure AD zapewniają następujące korzyści:

* Logowanie jednokrotne (Logowanie jednokrotne) do aplikacji zabezpieczonych przez usługę Azure AD.
* Roaming ustawień użytkownika na różnych urządzeniach zgodny z zasadami przedsiębiorstwa.
* Dostęp do Sklepu Windows dla Firm przy użyciu poświadczeń firmowych.
* Funkcja Windows Hello dla firm.
* Ograniczony dostęp do aplikacji i zasobów z urządzeń zgodnych z zasadami firmowymi.

Urządzenia mogą być przyłączane do usługi Azure AD z lub bez wdrożenia hybrydowego, który zawiera lokalne środowisko usług AD DS. W poniższej tabeli przedstawiono typowe modele własności urządzenia i sposób, w jaki zazwyczaj są one przyłączane do domeny:

| **Typ urządzenia**                                        | **Platformy urządzeń**             | **Mechanizm**          |
|:----------------------------------------------------------| -------------------------------- | ---------------------- |
| Urządzenia osobiste                                          | Windows 10, iOS, Android, Mac OS | Zarejestrowane w usłudze Azure AD    |
| Urządzenie należące do organizacji nie przyłączone do lokalnych usług AD DS | Windows 10                       | Dołączone do usługi Azure AD        |
| Urządzenie należące do organizacji przyłączone do lokalnego systemu AD DS  | Windows 10                       | hybrydowym dołączonym do usługi Azure AD. |

Na urządzeniu przyłączanym do usługi Azure AD lub zarejestrowanym uwierzytelnianie użytkownika odbywa się przy użyciu nowoczesnych protokołów opartych na OAuth / OpenID Connect. Protokoły te są przeznaczone do pracy przez Internet, więc są idealne dla scenariuszy mobilnych, w których użytkownicy uzyskują dostęp do zasobów firmowych z dowolnego miejsca.

W przypadku urządzeń przyłączonych do usług Azure AD DS aplikacje mogą używać protokołów Kerberos i NTLM do uwierzytelniania, dzięki czemu mogą obsługiwać starsze aplikacje zmigrowane w celu uruchomienia na maszynach wirtualnych platformy Azure w ramach strategii przenoszenia i zmiany. W poniższej tabeli przedstawiono różnice w sposobie przedstawiania urządzeń i mogą uwierzytelniać się względem katalogu:

| **Aspekt**                      | **Przyłączone do usługi Azure AD**                                 | **Przyłączone do usług Azure AD DS**                                                    |
|:--------------------------------| --------------------------------------------------- | ------------------------------------------------------------------------- |
| Urządzenie sterowane przez            | Azure AD                                            | Domena zarządzana usługą Azure AD DS                                                |
| Reprezentacja w katalogu | Obiekty urządzenia w katalogu usługi Azure AD            | Obiekty komputera w domenie zarządzanej usług Azure AD DS                        |
| Uwierzytelnianie                  | Protokoły oparte na OAuth / OpenID Connect              | Protokoły Kerberos i NTLM                                               |
| Zarządzanie                      | Oprogramowanie do zarządzania urządzeniami przenośnymi (MDM), takie jak usługa Intune | Zasady grupy                                                              |
| Obsługa sieci                      | Działa przez Internet                             | Musi być połączony z siecią wirtualną lub z nią równorzędny, w którym wdrożona jest domena zarządzana |
| Świetne dla...                    | Urządzenia mobilne lub stacjonarne użytkownika końcowego                  | Maszyny wirtualne serwera wdrożone na platformie Azure                                              |

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć korzystanie z usług Azure AD DS, [utwórz domenę zarządzaną usługą Azure AD DS przy użyciu witryny Azure portal][tutorial-create].

<!-- INTERNAL LINKS -->
[manage-dns]: manage-dns.md
[deploy-kcd]: deploy-kcd.md
[custom-ou]: create-ou.md
[manage-gpos]: manage-group-policy.md
[tutorial-ldaps]: tutorial-configure-ldaps.md
[tutorial-create]: tutorial-create-instance.md
