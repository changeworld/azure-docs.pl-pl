---
title: Jak zaplanować implementacji hybrydowej usługi Azure Active Directory join, w usłudze Azure Active Directory (Azure AD) | Dokumentacja firmy Microsoft
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
ms.openlocfilehash: c476c2b326045db37c54a358d68f4b5f8bbaed9a
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509597"
---
# <a name="how-to-plan-your-hybrid-azure-active-directory-join-implementation"></a>Instrukcje: Planowanie implementacji hybrydowej usługi Azure Active Directory join

W podobny sposób jak użytkownik urządzenie jest innej tożsamości core, który chcesz chronić i użyć jej do ochrony zasobów, w dowolnym momencie i z dowolnego miejsca. Ten cel można osiągnąć przez wprowadzenie i Zarządzanie tożsamościami urządzeń w usłudze Azure AD przy użyciu jednej z następujących metod:

- Dołączenie do usługi Azure AD
- Dołączenie hybrydowe do usługi Azure AD
- Rejestracja w usłudze Azure AD

Przenosząc urządzenia do usługi Azure AD, można zmaksymalizować wydajność użytkowników dzięki zastosowaniu logowania jednokrotnego (SSO) w zasobach chmury i zasobach lokalnych. W tym samym czasie można zabezpieczyć dostęp do zasobów w chmurze i lokalnych przy użyciu [dostępu warunkowego](../active-directory-conditional-access-azure-portal.md).

Jeśli masz w lokalnym środowisku Active Directory (AD) i chcesz dołączyć do komputerów przyłączonych do domeny usługi AD z usługą Azure AD, można to zrobić, wykonując dołączenie do hybrydowej usługi Azure AD. Ten artykuł zawiera o powiązanych kroków dotyczących implementacji hybrydowej usługi Azure AD join w danym środowisku. 

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule założono, że czytelnik zna [wprowadzenie do zarządzania tożsamościami urządzeń w usłudze Azure Active Directory](../device-management-introduction.md).

> [!NOTE]
> Minimalna wymagana funkcjonalności domeny i lasu poziomów funkcjonalności, dołączenie do hybrydowej usługi Azure AD w systemie Windows 10 jest system Windows Server 2008 R2.

## <a name="plan-your-implementation"></a>Planowanie implementacji

Aby zaplanować hybrydowego wdrożenia usługi Azure AD, należy się zapoznać z:

|   |   |
| --- | --- |
| ![Sprawdź][1] | Przegląd obsługiwanych urządzeń |
| ![Sprawdź][1] | Czynności przeglądu, które należy znać |
| ![Sprawdź][1] | Przejrzyj kontrolowana Walidacja dołączenie do hybrydowej usługi Azure AD |
| ![Sprawdź][1] | Wybierz danego scenariusza, w oparciu o infrastrukturę tożsamości |
| ![Sprawdź][1] | Przegląd lokalnej nazwy UPN z usługi AD, których obsługa hybrydowego, dołączania do usługi Azure AD |

## <a name="review-supported-devices"></a>Przegląd obsługiwanych urządzeń

Dołączenie do hybrydowej usługi Azure AD obsługuje urządzenia z szerokiego zakresu Windows. Ponieważ konfiguracji dla urządzeń ze starszymi wersjami systemu Windows wymaga wykonania czynności dodatkowym lub innym, obsługiwanych urządzeń są pogrupowane na dwie kategorie:

### <a name="windows-current-devices"></a>Bieżące urządzenia Windows

- Windows 10
- Windows Server 2016
- Windows Server 2019

Dla urządzeń z systemem Windows pulpitu systemu operacyjnego obsługiwaną wersję są wymienione w tym artykule [informacje o wersji systemu Windows 10](https://docs.microsoft.com/windows/release-information/). Najlepszym rozwiązaniem firma Microsoft zaleca się uaktualnienie do najnowszej wersji systemu Windows 10.

### <a name="windows-down-level-devices"></a>Windows niższego poziomu urządzeń

- Windows 8.1
- Windows 7. Aby uzyskać informacje dotyczące obsługi Windows 7, można znaleźć w tym artykule [kończy się pomocy technicznej dla Windows 7](https://www.microsoft.com/en-us/windowsforbusiness/end-of-windows-7-support)
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2

Pierwszym krokiem planowania należy przejrzeć środowiska i ustalić, czy potrzebujesz do obsługi systemu Windows niższego poziomu urządzeń.

## <a name="review-things-you-should-know"></a>Czynności przeglądu, które należy znać

Dołączenie do hybrydowej usługi Azure AD nie jest obecnie obsługiwane, jeśli środowiska składa się z pojedynczym lasem usługi AD synchronizowania danych tożsamości do więcej niż jednej dzierżawy usługi Azure AD.

Dołączenie do hybrydowej usługi Azure AD nie jest obecnie obsługiwane podczas korzystania z infrastruktury pulpitu wirtualnego (VDI).

Dołączenie do hybrydowej usługi Azure AD nie jest obsługiwana dla modułów TPM zgodne ze standardem FIPS. Jeśli urządzenia były zgodne ze standardem FIPS modułów TPM, należy wyłączyć je przed kontynuowaniem dołączenie do hybrydowej usługi Azure AD. Firma Microsoft zapewnia wszystkie narzędzia wyłączanie trybu FIPS dla modułów TPM, ponieważ jest on zależny od producenta modułu TPM. Uzyskać pomoc, skontaktuj się z sprzęt OEM.

Dołączenie do hybrydowej usługi Azure AD nie jest obsługiwana dla systemu Windows Server działającego w roli kontrolera domeny (DC).

Dołączenie do hybrydowej usługi Azure AD nie jest obsługiwana na urządzeniach z Windows niższego poziomu, podczas korzystania z roamingu poświadczeń lub użytkownik profilu mobilnego.

W przypadku korzystania z narzędzia przygotowywania systemu (Sysprep), a jeśli używasz **systemami starszymi niż Windows 10 1809** obrazu instalacji, upewnij się, że obraz nie jest od urządzenia, która jest już zarejestrowana w usłudze Azure AD jako dołączenie do hybrydowej usługi Azure AD.

Jeśli używasz migawkę maszyny wirtualnej (VM), aby utworzyć dodatkowe maszyny wirtualne, upewnij się, że tej migawki nie pochodzi z maszyną Wirtualną, która jest już zarejestrowany w usłudze Azure AD jako dołączenie do hybrydowej usługi Azure AD.

Przyłączone do domeny systemu Windows 10 urządzenia są już [usługi Azure AD zarejestrowany](https://docs.microsoft.com/azure/active-directory/devices/overview#azure-ad-registered-devices) do swojej dzierżawy, zdecydowanie zalecamy usunięcie tego stanu przed włączeniem dołączenie do hybrydowej usługi Azure AD. Z wersji systemu Windows 10 1809 wprowadzono następujące zmiany w celu uniknięcia tego podwójną stanu:

- Wszelkie istniejący stan usługi Azure AD zarejestrowany będą automatycznie usuwane, gdy urządzenie jest przyłączone do usługi Azure AD hybrydowej.
- Użytkownik może uniemożliwić urządzenia przyłączone do domeny usługi Azure AD zarejestrowany przez dodanie tego klucza rejestru - HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin, "BlockAADWorkplaceJoin" = dword: 00000001.
- Ta zmiana jest teraz dostępna w wersji systemu Windows 10 w wersji 1803 z KB4489894 stosowane. Jednak jeśli masz Windows Hello dla firm skonfigurowane, użytkownik będzie musiał ponownie setup Windows Hello dla firm po podwójnym stan czyszczenia.

## <a name="review-controlled-validation-of-hybrid-azure-ad-join"></a>Przejrzyj kontrolowana Walidacja dołączenie do hybrydowej usługi Azure AD

Gdy wszystkie wymagania wstępne zostały spełnione, urządzenia Windows są automatycznie rejestrowane jako urządzenia w Twojej dzierżawie usługi Azure AD. Stan tych tożsamości urządzeń w usłudze Azure AD jest określane jako dołączenie do hybrydowej usługi Azure AD. Więcej informacji na temat pojęć omówione w tym artykule znajdują się w artykułach [wprowadzenie do zarządzania tożsamościami urządzeń w usłudze Azure Active Directory](overview.md) i [planu usługi hybrydowe usługi Azure Active Directory join Implementacja](hybrid-azuread-join-plan.md).

Organizacje mogą chcieć wykonać kontrolowana Walidacja dołączenie do hybrydowej usługi Azure AD przed jego włączeniem w całej organizacji wszystkie na raz. Zapoznaj się z artykułem [kontrolowana Walidacja dołączenie do hybrydowej usługi Azure AD](hybrid-azuread-join-control.md) zrozumieć sposób jego osiągnięcia.

## <a name="select-your-scenario-based-on-your-identity-infrastructure"></a>Wybierz danego scenariusza, w oparciu o infrastrukturę tożsamości

Dołączenie do hybrydowej usługi Azure AD działa w środowiskach, zarządzane i federacyjnego.  

### <a name="managed-environment"></a>Zarządzane środowisko

Zarządzane środowisko może być wdrożone za pośrednictwem [synchronizacji skrótów haseł (wersji)](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-phs) lub [przekazać za pośrednictwem uwierzytelniania (PTA)](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta) z [bezproblemowego logowania jednokrotnego](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso).

Te scenariusze nie wymagają skonfigurowania serwera federacyjnego uwierzytelniania.

### <a name="federated-environment"></a>Środowisku federacyjnym

Środowisku federacyjnym powinna mieć dostawcę tożsamości, która obsługuje następujące wymagania:

- **Protokół WS-Trust:** Ten protokół jest wymagany do uwierzytelniania Windows urządzenia z usługą Azure AD przyłączone do bieżącego hybrydowej usługi Azure AD.
- **WIAORMULTIAUTHN oświadczeń:** To oświadczenie jest wymagany do hybrydowej usługi Azure AD join dla Windows niższego poziomu urządzeń.

Jeśli masz środowisku federacyjnym przy użyciu usługi Active Directory Federation Services (AD FS), powyższe wymagania już są obsługiwane.

> [!NOTE]
> Usługa Azure AD nie obsługuje w domenach zarządzanych kart inteligentnych lub certyfikatów.

Począwszy od wersji 1.1.819.0, program Azure AD Connect zapewnia kreator umożliwiający konfigurowanie dołączania hybrydowego do usługi Azure AD. Kreator pozwala znacznie uprościć proces konfiguracji. Jeśli zainstalowanie wymaganej wersji programu Azure AD Connect nie jest dostępną opcją w, zobacz [jak ręcznie skonfigurować rejestrację urządzeń](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-manual). 

Oparta na scenariuszu, który dopasowuje swoją infrastrukturę tożsamości, zobacz:

- [Konfiguruj dołączenie do usługi Azure Active Directory hybrydowej w środowisku federacyjnym](hybrid-azuread-join-federated-domains.md)
- [Konfiguruj dołączenie do usługi Azure Active Directory hybrydowej w środowisku zarządzanym](hybrid-azuread-join-managed-domains.md)

## <a name="review-on-premises-ad-upn-support-for-hybrid-azure-ad-join"></a>Przegląd środowiska lokalnego AD UPN obsługę dołączenie do hybrydowej usługi Azure AD

Czasami lokalnej usługi AD UPN mogą się różnić od usługi platformy Azure UPN usługi AD. W takich przypadkach systemu Windows 10 hybrydowej usługi Azure AD join udostępnia ograniczoną obsługę lokalnej nazwy UPN AD na podstawie [metodę uwierzytelniania](https://docs.microsoft.com/azure/security/azure-ad-choose-authn), typ domeny i wersji systemu Windows 10. Istnieją dwa typy w środowisku lokalnym UPN AD, które może znajdować się w danym środowisku:

- Routable UPN: Routing UPN ma prawidłową domenę zweryfikowaną, która jest zarejestrowana za pomocą rejestratora domen. Na przykład, jeśli contoso.com jest domena podstawowa w usłudze Azure AD, contoso.org jest domena podstawowa w lokalnym programie AD należące do firmy Contoso i [zweryfikowane w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain)
- Nierutowalny nazwa UPN: Nierutowalny nazwy UPN nie ma zweryfikowanej domeny. Dotyczy tylko w ramach prywatnej sieci organizacji. Na przykład, jeśli contoso.com jest domena podstawowa w usłudze Azure AD, contoso.local jest domena podstawowa w lokalnej usługi AD, ale nie jest możliwe do zweryfikowania domeny w Internecie i używane w katalogu firmy Contoso tylko w sieci.

Poniższa tabela zawiera szczegółowe informacje dotyczące pomocy technicznej dla tych lokalnego UPN usługi AD systemu Windows 10 hybrydowej usługi Azure AD sprzężenia

| Typ w środowisku lokalnym UPN usługi AD | Typ domeny | Wersja systemu Windows 10 | Opis |
| ----- | ----- | ----- | ----- |
| Routing | Federacyjna | W wersji 1703 | Ogólnie dostępna |
| Bez obsługi routingu | Federacyjna | W wersji 1803 | Ogólnie dostępna |
| Routing | Zarządzane | Nieobsługiwane | |
| Bez obsługi routingu | Zarządzane | Nieobsługiwane | |

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Konfiguruj dołączenie do usługi Azure Active Directory hybrydowej dla federacyjnego enviornment](hybrid-azuread-join-federated-domains.md)
> [Konfiguruj dołączenie do usługi Azure Active Directory hybrydowej w środowisku zarządzanym](hybrid-azuread-join-managed-domains.md)

<!--Image references-->
[1]: ./media/hybrid-azuread-join-plan/12.png
