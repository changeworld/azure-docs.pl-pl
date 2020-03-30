---
title: Planowanie hybrydowego sprzężenia usługi Azure Active Directory — usługa Azure Active Directory
description: Dowiedz się, jak skonfigurować urządzenia dołączone hybrydowo do usługi Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 152ff52ce52b573d7f24cbb2fafc944b1794f6d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80129255"
---
# <a name="how-to-plan-your-hybrid-azure-active-directory-join-implementation"></a>Jak: Planowanie implementacji dołączania do hybrydowej usługi Azure Active Directory

W podobny sposób do użytkownika urządzenie jest inną podstawową tożsamością, którą chcesz chronić i używać do ochrony zasobów w dowolnym czasie i z dowolnej lokalizacji. Ten cel można osiągnąć, wprowadzając tożsamości urządzeń i zarządzając nimi w usłudze Azure AD przy użyciu jednej z następujących metod:

- Dołączenie do usługi Azure AD
- Dołączenie hybrydowe do usługi Azure AD
- Rejestracja w usłudze Azure AD

Przenosząc urządzenia do usługi Azure AD, można zmaksymalizować wydajność użytkowników dzięki zastosowaniu logowania jednokrotnego (SSO) w zasobach chmury i zasobach lokalnych. Jednocześnie można zabezpieczyć dostęp do zasobów w chmurze i lokalnych za pomocą [dostępu warunkowego.](../active-directory-conditional-access-azure-portal.md)

Jeśli masz lokalne środowisko usługi Active Directory (AD) i chcesz dołączyć do komputerów przyłączonych do domeny usługi AD do usługi Azure AD, możesz to osiągnąć, wykonując hybrydowe sprzężenie usługi Azure AD. Ten artykuł zawiera powiązane kroki, aby zaimplementować hybrydowe sprzężenie usługi Azure AD w twoim środowisku. 

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule założono, że znasz [wprowadzenie do zarządzania tożsamościami urządzeń w usłudze Azure Active Directory](../device-management-introduction.md).

> [!NOTE]
> Minimalna wymagana wersja kontrolera domeny dla hybrydowego sprzężenia usługi Azure AD systemu Windows 10 to Windows Server 2008 R2.

## <a name="plan-your-implementation"></a>Zaplanuj swoją implementację

Aby zaplanować hybrydową implementację usługi Azure AD, należy zapoznać się z:

|   |   |
| --- | --- |
| ![Zaznacz][1] | Przeglądanie obsługiwanych urządzeń |
| ![Zaznacz][1] | Przejrzyj rzeczy, które powinieneś wiedzieć |
| ![Zaznacz][1] | Przeglądanie kontrolowanej weryfikacji hybrydowego sprzężenia usługi Azure AD |
| ![Zaznacz][1] | Wybierz scenariusz na podstawie infrastruktury tożsamości |
| ![Zaznacz][1] | Przeglądanie lokalnej obsługi sieci UPN usługi AD dla hybrydowego sprzężenia usługi Azure AD |

## <a name="review-supported-devices"></a>Przeglądanie obsługiwanych urządzeń

Hybrydowe sprzężenie usługi Azure AD obsługuje szeroką gamę urządzeń z systemem Windows. Ponieważ konfiguracja urządzeń ze starszymi wersjami systemu Windows wymaga dodatkowych lub różnych kroków, obsługiwane urządzenia są pogrupowane w dwie kategorie:

### <a name="windows-current-devices"></a>Bieżące urządzenia z systemem Windows

- Windows 10
- Windows Server 2016
  - **Uwaga:** Klienci chmury krajowej platformy Azure wymagają wersji 1809
- Windows Server 2019

W przypadku urządzeń z systemem operacyjnym Windows dla komputerów stacjonarnych obsługiwana wersja jest wymieniona w tym artykule [Informacje o wersji systemu Windows 10](/windows/release-information/). Najlepszym rozwiązaniem jest zalecana przez firmę Microsoft uaktualnienie do najnowszej wersji systemu Windows 10.

### <a name="windows-down-level-devices"></a>Urządzenia z systemem Windows w dół

- Windows 8.1
- Obsługa systemu Windows 7 zakończyła się 14 stycznia 2020 roku. Aby uzyskać więcej informacji, zobacz [Pomoc techniczna dla systemu Windows 7 została zakończona](https://support.microsoft.com/en-us/help/4057281/windows-7-support-ended-on-january-14-2020).
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2. Aby uzyskać informacje dotyczące pomocy technicznej w systemach Windows Server 2008 i 2008 R2, zobacz [Przygotowanie do zakończenia pomocy technicznej dla systemu Windows Server 2008](https://www.microsoft.com/cloud-platform/windows-server-2008).

Jako pierwszy krok planowania należy przejrzeć środowisko i określić, czy trzeba obsługiwać urządzenia z systemem Windows w dół.

## <a name="review-things-you-should-know"></a>Przejrzyj rzeczy, które powinieneś wiedzieć

### <a name="unsupported-scenarios"></a>Nieobsługiwane scenariusze
- Hybrydowe sprzężenie usługi AD nie jest obecnie obsługiwane, jeśli środowisko składa się z jednego lasu usługi AD synchronizującego dane tożsamości z więcej niż jedną dzierżawą usługi Azure AD.

- Hybrydowe sprzężenie usługi AD nie jest obsługiwane w systemie Windows Server z rolą kontrolera domeny.Hybrid Azure AD join is not supported for Windows Server running the Domain Controller (DC) role.

- Hybrydowe sprzężenie usługi AD nie jest obsługiwane na urządzeniach z systemem Windows w dół podczas korzystania z roamingu poświadczeń lub profilu użytkownika mobilnego lub profilu obowiązkowego.

- Server Core OS nie obsługuje żadnego typu rejestracji urządzenia.

### <a name="os-imaging-considerations"></a>Zagadnienia dotyczące obrazowania systemu operacyjnego
- Jeśli korzystasz z narzędzia przygotowania systemu (Sysprep) i jeśli używasz obrazu **sprzed systemu Windows 10 1809** do instalacji, upewnij się, że obraz nie pochodzi z urządzenia, które jest już zarejestrowane w usłudze Azure AD jako hybrydowe sprzężenie usługi AZURE AD.

- Jeśli korzystasz z migawki maszyny wirtualnej (VM) w celu utworzenia dodatkowych maszyn wirtualnych, upewnij się, że migawka nie pochodzi z maszyny wirtualnej, która jest już zarejestrowana w usłudze Azure AD jako sprzężenie usługi Hybrid Azure AD.

- Jeśli używasz [ujednoliconego filtru zapisu](/windows-hardware/customize/enterprise/unified-write-filter) i podobnych technologii, które wyczyszczają zmiany na dysku po ponownym uruchomieniu komputera, muszą zostać zastosowane po przyłączeniu do usługi Hybrid Azure AD. Włączenie takich technologii przed zakończeniem hybrydowego sprzężenia usługi Azure AD spowoduje, że urządzenie zostanie odłączone przy każdym ponownym uruchomieniu komputera

### <a name="handling-devices-with-azure-ad-registered-state"></a>Obsługa urządzeń w stanie zarejestrowanym w usłudze Azure AD
Jeśli urządzenia przyłączone do domeny systemu Windows 10 są [zarejestrowane w](overview.md#getting-devices-in-azure-ad) usłudze Azure AD w dzierżawie, może to prowadzić do podwójnego stanu przyłączonych do usługi Azure AD i urządzenia zarejestrowanego w usłudze Azure AD. Zalecamy uaktualnienie do systemu Windows 10 1803 (z zastosowaną aktualizacją KB4489894) lub wyższym, aby automatycznie rozwiązać ten scenariusz. W wersjach sprzed 1803 r. należy ręcznie usunąć stan rejestracji usługi Azure AD przed włączeniem hybrydowego sprzężenia usługi AZURE AD. W 1803 i powyżej wydania, następujące zmiany zostały wprowadzone w celu uniknięcia tego stanu podwójnego:

- Każdy istniejący stan zarejestrowany usługi Azure AD dla użytkownika zostanie automatycznie usunięty <i>po tym, jak urządzenie zostanie przyłączone do usługi Hybrid Azure AD i zaloguje się tym samym użytkownikiem.</i> Na przykład jeśli użytkownik A miał stan zarejestrowany usługi Azure AD na urządzeniu, podwójny stan dla użytkownika A jest czyszczony tylko wtedy, gdy użytkownik A loguje się do urządzenia. Jeśli na tym samym urządzeniu jest wielu użytkowników, stan podwójny jest czyszczony indywidualnie, gdy ci użytkownicy się logują.
- Możesz uniemożliwić urządzeniu przyłączonemu do domeny zarejestrowanie usługi Azure AD przez dodanie tego klucza rejestru — HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin, "BlockAADWorkplaceJoin"=dword:00000001.
- W systemie Windows 10 1803, jeśli masz skonfigurowany windows hello dla firm, użytkownik musi ponownie skonfigurować Windows Hello for Business po oczyszczeniu stanu podwójnego. Ten problem został rozwiązany z KB4512509

> [!NOTE]
> Urządzenie zarejestrowane w usłudze Azure AD nie zostanie automatycznie usunięte, jeśli jest zarządzane przez usługę Intune.

### <a name="additional-considerations"></a>Dodatkowe zagadnienia
- Jeśli środowisko korzysta z infrastruktury pulpitu wirtualnego (VDI), zobacz [Tożsamość urządzenia i wirtualizacja pulpitu](/azure/active-directory/devices/howto-device-identity-virtual-desktop-infrastructure).

- Hybrydowe sprzężenie usługi Azure AD jest obsługiwane dla modułu TPM 2.0 zgodnego ze standardem FIPS i nie jest obsługiwane dla modułu TPM 1.2. Jeśli twoje urządzenia mają zgodny z FIPS moduł TPM 1.2, należy je wyłączyć przed przystąpieniem do usługi Azure Hybrid. Firma Microsoft nie udostępnia żadnych narzędzi do wyłączania trybu FIPS dla modułów TPM, ponieważ jest on zależny od producenta modułu TPM. Aby uzyskać pomoc techniczną, skontaktuj się ze swoim sprzętowym oem. 

- Począwszy od wersji systemu Windows 10 1903, TPM 1.2 nie są używane z hybrydowym sprzężeniem usługi Azure AD, a urządzenia z tymi modułami TPM będą traktowane tak, jakby nie miały modułu TPM.

## <a name="review-controlled-validation-of-hybrid-azure-ad-join"></a>Przeglądanie kontrolowanej weryfikacji hybrydowego sprzężenia usługi Azure AD

Gdy wszystkie wymagania wstępne są w miejscu, urządzenia z systemem Windows automatycznie zarejestruje się jako urządzenia w dzierżawie usługi Azure AD. Stan tych tożsamości urządzeń w usłudze Azure AD jest określany jako hybrydowe sprzężenie usługi Azure AD. Więcej informacji na temat pojęć omówionych w tym artykule można znaleźć w artykule [Wprowadzenie do zarządzania tożsamościami urządzeń w usłudze Azure Active Directory](overview.md).

Organizacje mogą chcieć wykonać kontrolowane sprawdzanie poprawności hybrydowego sprzężenia usługi Azure AD przed włączeniem jej w całej organizacji naraz. Przejrzyj [artykuł kontrolowane sprawdzanie poprawności hybrydowej usługi Azure AD join,](hybrid-azuread-join-control.md) aby dowiedzieć się, jak to zrobić.

## <a name="select-your-scenario-based-on-your-identity-infrastructure"></a>Wybierz scenariusz na podstawie infrastruktury tożsamości

Hybrydowe sprzężenie usługi Azure AD działa zarówno ze środowiskami zarządzanymi, jak i federowanymi w zależności od tego, czy sieć UPN jest rutowalna, czy nie rutowalna. Zobacz dolnej części strony dla tabeli na obsługiwane scenariusze.  

### <a name="managed-environment"></a>Środowisko zarządzane

Zarządzane środowisko można wdrożyć za pomocą [synchronizacji skrótów haseł (PHS)](/azure/active-directory/hybrid/whatis-phs) lub [uwierzytelniania przekazywanego (PTA)](/azure/active-directory/hybrid/how-to-connect-pta) z [bezproblemowym logowaniem jednokrotnym.](/azure/active-directory/hybrid/how-to-connect-sso)

Te scenariusze nie wymagają konfigurowania serwera federacyjnego do uwierzytelniania.

### <a name="federated-environment"></a>Sfederowane środowisko

Środowisko federacyjne powinno mieć dostawcę tożsamości, który obsługuje następujące wymagania. Jeśli masz środowisko federacyjne korzystające z usług federacyjnych Active Directory (AD FS), poniższe wymagania są już obsługiwane.

- **WIAORMULTIAUTHN twierdzenie:** To oświadczenie jest wymagane do hybrydowego sprzężenia usługi Azure AD dla urządzeń w dół systemu Windows.
- **Protokół WS-Trust:** Ten protokół jest wymagany do uwierzytelniania obecnych hybrydowych urządzeń platformy Azure AD z usługą Azure AD. Podczas korzystania z usług AD FS należy włączyć następujące punkty końcowe WS-Trust:`/adfs/services/trust/2005/windowstransport`  
`/adfs/services/trust/13/windowstransport`  
  `/adfs/services/trust/2005/usernamemixed` 
  `/adfs/services/trust/13/usernamemixed`
  `/adfs/services/trust/2005/certificatemixed` 
  `/adfs/services/trust/13/certificatemixed` 

> [!WARNING] 
> Zarówno **adfs/services/trust/2005/windowstransport** lub **adfs/services/trust/13/windowstransport** powinny być włączone tylko jako punkty końcowe skierowane do intranetu i NIE mogą być udostępniane jako punkty końcowe skierowane do ekstranetu za pośrednictwem serwera proxy aplikacji sieci Web. Aby dowiedzieć się więcej na temat wyłączania punktów końcowych systemu Windows w systemie WS-Trust, zobacz [Wyłączanie punktów końcowych systemu Windows w systemie WS-Trust w serwerze proxy](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#disable-ws-trust-windows-endpoints-on-the-proxy-ie-from-extranet). Punkty końcowe są włączone za pośrednictwem konsoli zarządzania usługami AD FS w obszarze**Punkty końcowe** **usługi** > .

> [!NOTE]
> Usługa Azure AD nie obsługuje kart inteligentnych ani certyfikatów w domenach zarządzanych.

Począwszy od wersji 1.1.819.0, program Azure AD Connect zapewnia kreator umożliwiający konfigurowanie dołączania hybrydowego do usługi Azure AD. Kreator pozwala znacznie uprościć proces konfiguracji. Jeśli zainstalowanie wymaganej wersji usługi Azure AD Connect nie jest opcją, zobacz, [jak ręcznie skonfigurować rejestrację urządzenia](hybrid-azuread-join-manual.md). 

Na podstawie scenariusza, który pasuje do infrastruktury tożsamości, zobacz:

- [Konfigurowanie hybrydowego sprzężenia usługi Azure Active Directory dla środowiska federacyjnego](hybrid-azuread-join-federated-domains.md)
- [Konfigurowanie hybrydowego sprzężenia usługi Azure Active Directory dla środowiska zarządzanego](hybrid-azuread-join-managed-domains.md)

## <a name="review-on-premises-ad-users-upn-support-for-hybrid-azure-ad-join"></a>Przeglądanie lokalnej pomocy technicznej sieci UPN użytkowników usług AD dla hybrydowego dołączania do usługi AD

Czasami lokalne sieci UPN użytkowników usług AD mogą się różnić od sieci UPN usługi Azure AD. W takich przypadkach sprzężenie hybrydowe usługi Azure AD systemu Windows 10 zapewnia ograniczoną obsługę lokalnych sieci UPN ad na podstawie [metody uwierzytelniania,](/azure/security/fundamentals/choose-ad-authn)typu domeny i wersji systemu Windows 10. Istnieją dwa typy lokalnych sieci UPN ad, które mogą istnieć w twoim środowisku:

- Nazwa UPN użytkowników routingu: routingowa nazwa UPN ma prawidłową zweryfikowaną domenę zarejestrowaną u rejestratora domen. Jeśli na przykład contoso.com jest domeną podstawową w usłudze Azure AD, contoso.org jest domeną podstawową w lokalnej usłudze AD należącej do firmy Contoso i [zweryfikowaną w usłudze Azure AD](/azure/active-directory/fundamentals/add-custom-domain)
- Nazwa UPN użytkowników nierozerwalnych: nierutowalna nazwa UPN nie ma zweryfikowanej domeny. Ma zastosowanie tylko w sieci prywatnej organizacji. Na przykład jeśli contoso.com jest domeną podstawową w usłudze Azure AD, contoso.local jest domeną podstawową w lokalnej usłudze AD, ale nie jest weryfikowalną domeną w Internecie i jest używana tylko w sieci contoso.

> [!NOTE]
> Informacje zawarte w tej sekcji dotyczą tylko lokalnej sieci UPN użytkowników. Nie ma zastosowania do sufiksu domeny komputera lokalnego (przykład: computer1.contoso.local).

W poniższej tabeli przedstawiono szczegółowe informacje na temat obsługi lokalnych sieci UPN ad w usłudze Windows 10 Hybrid Azure AD join

| Typ lokalnej sieci UPN usługi AD | Typ domeny | Wersja systemu Windows 10 | Opis |
| ----- | ----- | ----- | ----- |
| Routingu | Federacyjni | Od wersji 1703 | Ogólnie dostępne |
| Nie rutowalne | Federacyjni | Od wersji 1803 | Ogólnie dostępne |
| Routingu | Zarządzani | Od wersji 1803 | Ogólnie dostępne odwzwiaźne SSPR usługi Ad w usłudze Windows lockscreen nie są obsługiwane |
| Nie rutowalne | Zarządzani | Nieobsługiwane | |

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Konfigurowanie hybrydowego sprzężenia usługi Azure Active Directory dla środowiska](hybrid-azuread-join-federated-domains.md)
> federacyjnego[Konfigurowanie hybrydowego sprzężenia usługi Azure Active Directory dla środowiska zarządzanego](hybrid-azuread-join-managed-domains.md)

<!--Image references-->
[1]: ./media/hybrid-azuread-join-plan/12.png
