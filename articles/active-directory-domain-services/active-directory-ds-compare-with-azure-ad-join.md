---
title: Porównanie usług domenowych Azure AD Join i Azure Active Directory | Dokumentacja firmy Microsoft
description: Przy wyborze między usług domenowych Azure AD i Azure AD Join
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 31a71d36-58c1-4839-b958-80da0c6a77eb
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2017
ms.author: maheshu
ms.openlocfilehash: 8bfc62f978b85399a64da32636627efc7ae234da
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36212557"
---
# <a name="choose-between-azure-active-directory-join-and-azure-active-directory-domain-services"></a>Wybór między sprzężenia usługi Azure Active Directory i Azure Active Directory Domain Services
W tym artykule opisano różnice między sprzężenia Azure Active Directory (AD) i usługi domenowe Azure AD i pomoże wybrać, w oparciu o przypadki użycia.

## <a name="azure-ad-registered-and-azure-ad-joined-devices"></a>Zarejestrowane w usłudze Azure AD i urządzeniach przyłączonych do usługi Azure AD
Usługi Azure AD pozwala na zarządzanie tożsamość urządzenia używana przez Twojej organizacji i kontroli dostępu do zasobów firmy z tych urządzeń. Użytkownicy mogą rejestrować swoich urządzeń osobistych (bring your own) z usługą Azure AD, która zapewnia urządzenia przy użyciu tożsamości. Później usługi Azure AD może uwierzytelniać urządzenie, gdy użytkownik loguje się do usługi Azure AD i używa urządzenia dostępu do zabezpieczonych zasobów. Ponadto można zarządzać urządzeniem za pomocą oprogramowania zarządzania urządzeniami przenośnymi (MDM), takich jak Microsoft Intune. Ta funkcja pozwala ograniczyć dostęp do poufnych zasobów z urządzenia zarządzane i zgodne z zasadami.

Można również dołączenie urządzeń do usługi Azure AD należących do organizacji. Ten mechanizm zapewnia korzyści tego samego rejestrowania urządzenia osobistego za pomocą usługi Azure AD. Ponadto można logowania użytkownika do urządzenia za pomocą poświadczeń firmowych. Azure AD połączone urządzenia zapewniają następujące korzyści:
* Single-sign-on rejestracji jednokrotnej (SSO) do aplikacji zabezpieczonej przez usługi Azure AD
* Enterprise zgodne z zasadami roaming ustawień użytkownika na urządzeniach.
* Dostęp do Sklepu Windows dla firm przy użyciu poświadczeń firmowych.
* Windows Hello dla firm
* Ograniczony dostęp do aplikacji i zasobów z urządzeń, które są zgodne z zasadami firmowymi.

| **Typ urządzenia** | **Platformy urządzeń** | **Mechanizm** |
|:---| --- | --- |
| Urządzenia osobiste | Windows 10, iOS, Android, Mac OS | Azure AD w zarejestrowany |
| Urządzenie nie jest przyłączony do należące do organizacji lokalnej usłudze AD | Windows 10 | Azure AD dołączony |
| Organizacji należące do urządzenia przyłączone do lokalnej usługi AD | Windows 10 | Hybrydowe przyłączonych do usługi Azure AD |

W usłudze Azure AD połączony lub sytuacji zarejestrowanym urządzeniu, uwierzytelnianie użytkowników przy użyciu nowoczesnych protokołów uwierzytelniania OAuth/OpenID Connect, na podstawie. Te protokoły są zaprojektowane do pracy w Internecie i są doskonałe rozwiązanie dla przenośnych scenariuszy, w którym użytkownicy uzyskują dostęp do zasobów firmy z dowolnego miejsca.


## <a name="domain-join-to-azure-ad-domain-services-managed-domains"></a>Przyłączanie do domeny do domeny zarządzanej usług domenowych w usłudze Azure AD
Usługi domenowe Azure AD zapewnia domeny AD zarządzanej w sieci wirtualnej platformy Azure. Maszyny można dołączyć do tej domeny zarządzanej za pomocą mechanizmów tradycyjnych przyłączania do domeny. Klient systemu Windows (Windows 7, Windows 10) i maszyny z systemem Windows Server mogą być przyłączone do domeny zarządzanej. Ponadto można także sprzęgać systemu Linux i Mac OS maszyny do domeny zarządzanej. Dołączenie komputera do domeny usługi AD, można logowania się do komputera za pomocą poświadczeń firmowych. Te komputery mogą być zarządzane za pomocą zasad grupy, w związku z tym Wymuszanie zgodności z zasadami zabezpieczeń organizacji.

Na komputerze przyłączonym do domeny uwierzytelnianie użytkownika odbywa się przy użyciu protokołów uwierzytelniania NTLM lub Kerberos. Maszyna przyłączonych do domeny wymaga wiersza z procesów do kontrolerów domeny zarządzanej w kolejności dla działania uwierzytelniania użytkownika. W związku z tym przyłączone do domeny komputera musi być w tej samej sieci wirtualnej jako domeny zarządzanej. Alternatywnie przyłączone do domeny potrzeb maszyny do przyłączania do domeny zarządzanej w sieci wirtualnej peered lub za pośrednictwem połączenia sieci VPN/ExpressRoute lokacja lokacja. W związku z tym mechanizm ten nie jest doskonałym dopasowania dla urządzeń przenośnych lub połączyć się z zasobami z spoza sieci firmowej.

> [!NOTE]
> Z technicznego punktu widzenia jest możliwe do dołączenia klienta lokalnej stacji roboczej do domeny zarządzanej za pośrednictwem połączenia sieci VPN i ExpressRoute lokacja lokacja. Jednak dla urządzeń użytkowników końcowych, które firma Microsoft zaleca się, że używasz rejestrowania urządzenia w usłudze Azure AD (urządzeń osobistych) albo dołączenie urządzenia do usługi Azure AD (urządzenia). Ten mechanizm działa lepiej w Internecie i umożliwia użytkownikom do pracy z dowolnego miejsca. Usługi domenowe Azure AD jest doskonały dla systemu Windows lub Linux serwer maszyn wirtualnych wdrożonych w sieci wirtualne platformy Azure, na których są wdrożone aplikacje.


## <a name="summary---key-differences"></a>Podsumowanie — podstawowe różnice
| **Aspekt** | **Azure AD Join** | **Usługi domenowe Azure AD** |
|:---| --- | --- |
| Kontrolowane przez urządzenia | Azure AD | Azure domeny zarządzanej usług domenowych w usłudze AD |
| Reprezentacja w katalogu | Obiekty urządzeń w katalogu usługi Azure AD. | Obiekty komputerów w domenie zarządzanej usługi AAD DS. |
| Authentication | Protokoły uwierzytelniania OAuth/OpenID Connect, na podstawie | Protokół Kerberos, protokoły NTLM |
| Zarządzanie | Oprogramowanie do zarządzania urządzeniami przenośnymi (MDM) takie jak usługi Intune | Zasady grupy |
| Networking | Działa w Internecie | Wymaga maszyn w tej samej sieci wirtualnej jako domeny zarządzanej.|
| Świetnie dla... | Urządzenia przenośne lub pulpitu użytkownika końcowego | Maszyny wirtualne serwera wdrożona na platformie Azure |


## <a name="next-steps"></a>Kolejne kroki
### <a name="learn-more-about-azure-ad-domain-services"></a>Dowiedz się więcej o usługach domenowych Azure AD
* [Omówienie usług domenowych Azure AD](active-directory-ds-overview.md)
* [Funkcje](active-directory-ds-features.md)
* [Scenariusze wdrażania](active-directory-ds-scenarios.md)
* [Dowiedz się, czy usługi domenowe Azure AD pasujące do przypadków użycia](active-directory-ds-comparison.md)
* [Zrozumienie, jak usługi domenowe Azure AD synchronizacji z katalogiem usługi Azure AD](active-directory-ds-synchronization.md)

### <a name="learn-more-about-azure-ad-join"></a>Dowiedz się więcej o usłudze Azure AD Join
* [Wprowadzenie do zarządzania urządzeniami w usłudze Azure Active Directory](../active-directory/device-management-introduction.md)

### <a name="get-started-with-azure-ad-domain-services"></a>Wprowadzenie do usług Azure AD Domain Services
* [Włączanie usług domenowych Azure AD przy użyciu portalu Azure](active-directory-ds-getting-started.md)
