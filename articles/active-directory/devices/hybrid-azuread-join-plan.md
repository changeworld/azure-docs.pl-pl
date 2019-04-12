---
title: Jak zaplanować implementacji hybrydowej usługi Azure Active Directory join, w usłudze Azure Active Directory (Azure AD) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować urządzenia dołączone hybrydowo do usługi Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/10/2019
ms.author: joflore
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8827a51a23b2ea274d8096a154e630c9cecbba7c
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/11/2019
ms.locfileid: "59489522"
---
# <a name="how-to-plan-your-hybrid-azure-active-directory-join-implementation"></a>Instrukcje: Planowanie implementacji hybrydowej usługi Azure Active Directory join

Podobnie jak w przypadku użytkownika, urządzenie staje się kolejną tożsamością, którą należy chronić oraz używać do zabezpieczania zasobów w dowolnym czasie i miejscu. W tym celu można przenieść tożsamości urządzeń do usługi Azure AD przy użyciu jednej z następujących metod:

- Dołączenie do usługi Azure AD
- Dołączenie hybrydowe do usługi Azure AD
- Rejestracja w usłudze Azure AD

Przenosząc urządzenia do usługi Azure AD, można zmaksymalizować wydajność użytkowników dzięki zastosowaniu logowania jednokrotnego (SSO) w zasobach chmury i zasobach lokalnych. Jednocześnie można zapewnić bezpieczny dostęp do zasobów chmury i zasobów lokalnych przy użyciu [dostępu warunkowego](../active-directory-conditional-access-azure-portal.md).

Jeśli masz lokalne środowisko usługi Active Directory i chcesz dołączyć do usługi Azure AD urządzenia dołączone do domeny, możesz to zrobić przez skonfigurowanie urządzeń dołączonych hybrydowo do usługi Azure AD. Ten artykuł zawiera o powiązanych kroków dotyczących implementacji hybrydowej usługi Azure AD join w danym środowisku. 

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule założono, że czytelnik zna [wprowadzenie do zarządzania urządzeniami w usłudze Azure Active Directory](../device-management-introduction.md).

> [!NOTE]
> Minimalna wymagana funkcjonalności domeny i lasu poziomów funkcjonalności, dołączenie do hybrydowej usługi Azure AD w systemie Windows 10 jest system Windows Server 2008 R2. W niższych wersjach użytkownik może nie otrzymać podstawowego tokenu odświeżania podczas logowania Windows z powodu problemów z urzędu LSA.

## <a name="plan-your-implementation"></a>Planowanie implementacji

Aby zaplanować hybrydowego wdrożenia usługi Azure AD, należy się zapoznać z:

|   |   |
| --- | --- |
| ![Zaznacz][1] | Przegląd obsługiwanych urządzeń |
| ![Zaznacz][1] | Czynności przeglądu, które należy znać |
| ![Zaznacz][1] | Dowiedz się, jak kontrolować dołączenie do hybrydowej usługi Azure AD urządzeń |
| ![Zaznacz][1] | Wybierz scenariusz |

## <a name="review-supported-devices"></a>Przegląd obsługiwanych urządzeń

Dołączenie do hybrydowej usługi Azure AD obsługuje urządzenia z szerokiego zakresu Windows. Ponieważ konfiguracji dla urządzeń ze starszymi wersjami systemu Windows wymaga wykonania czynności dodatkowym lub innym, obsługiwanych urządzeń są pogrupowane na dwie kategorie:

### <a name="windows-current-devices"></a>Bieżące urządzenia Windows

- Windows 10
- Windows Server 2016
- Windows Server 2019

Dla urządzeń z systemem Windows pulpitu systemu operacyjnego, obsługiwana wersja to Windows Update rozliczenia 10 (wersja 1607) lub nowszej. Najlepszym rozwiązaniem jest uaktualnienie do najnowszej wersji systemu Windows 10.

### <a name="windows-down-level-devices"></a>Windows niższego poziomu urządzeń

- Windows 8.1
- Windows 7
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2

Pierwszym krokiem planowania należy przejrzeć środowiska i ustalić, czy potrzebujesz do obsługi systemu Windows niższego poziomu urządzeń.

## <a name="review-things-you-should-know"></a>Czynności przeglądu, które należy znać

Nie można użyć dołączenie do hybrydowej usługi Azure AD, jeśli środowiska składa się z pojedynczego lasu, który synchronizowania danych tożsamości do więcej niż jednej dzierżawy usługi Azure AD.

Jeśli użytkownik korzysta z narzędzia przygotowywania systemu (Sysprep), upewnij się, że obrazy utworzone na podstawie instalacji systemu Windows 10 w wersji 1803 lub wcześniej nie zostały skonfigurowane na dołączenie do hybrydowej usługi Azure AD.

Jeśli używasz migawkę maszyny wirtualnej (VM), aby utworzyć dodatkowe maszyny wirtualne, upewnij się, że używasz migawki maszyny Wirtualnej, która nie została skonfigurowana dla dołączenie do hybrydowej usługi Azure AD.

Dołączenie do hybrydowej usługi Azure AD Windows niższego poziomu urządzeń:

- **Jest** obsługiwane w środowiskach inne niż federacyjne za pośrednictwem [usługi Azure Active Directory bezproblemowe logowanie jednokrotne](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-quick-start). 
- **Nie jest** obsługiwane w przypadku korzystania z uwierzytelniania usługi Azure AD przekazywanego bez bezproblemowego logowania jednokrotnego.
- **Nie jest** obsługiwane podczas korzystania z roamingu poświadczeń lub profilu użytkownika mobilnego lub w przypadku używania infrastruktury pulpitu wirtualnego (VDI).

Rejestracja systemu Windows Server działającego w roli kontrolera domeny (DC) nie jest obsługiwana.

Jeśli organizacja wymaga dostępu do Internetu za pośrednictwem uwierzytelnionego serwera proxy ruchu wychodzącego, musisz upewnić się, że komputery z systemem Windows 10 mogą pomyślnie uwierzytelnić się na serwerze proxy ruchu wychodzącego. Ponieważ komputery z systemem Windows 10 uruchamiają rejestrację urządzenia przy użyciu kontekstu maszyny, konfigurowanie uwierzytelniania serwera proxy ruchu wychodzącego należy wykonać właśnie przy użyciu kontekstu maszyny.

Dołączenie do hybrydowej usługi Azure AD jest proces automatycznego rejestrowania urządzeń przyłączonych do domeny lokalnej za pomocą usługi Azure AD. Istnieją przypadki, w których nie chcesz wszystkich urządzeń do automatycznej rejestracji. Jeśli to PRAWDA dla Ciebie, zobacz [sposób kontrolowania dołączenie do hybrydowej usługi Azure AD urządzeń](hybrid-azuread-join-control.md).

Przyłączone do domeny systemu Windows 10 urządzenia są już [usługi Azure AD zarejestrowany](https://docs.microsoft.com/azure/active-directory/devices/overview#azure-ad-registered-devices) do swojej dzierżawy, zdecydowanie zalecamy usunięcie tego stanu przed włączeniem dołączenie do hybrydowej usługi Azure AD. Z wersji systemu Windows 10 1809 wprowadzono następujące zmiany w celu uniknięcia tego podwójną stanu:

- Wszelkie istniejący stan usługi Azure AD zarejestrowany będą automatycznie usuwane, gdy urządzenie jest przyłączone do usługi Azure AD hybrydowej.
- Użytkownik może uniemożliwić urządzenia przyłączone do domeny usługi Azure AD zarejestrowany przez dodanie tego klucza rejestru - HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin, "BlockAADWorkplaceJoin" = dword: 00000001.
- Ta zmiana jest teraz dostępna w wersji systemu Windows 10 w wersji 1803 z KB4489894.

Zgodne ze standardem FIPS TPM nie są obsługiwane przez dołączenie do hybrydowej usługi Azure AD. Jeśli urządzenia były zgodne ze standardem FIPS modułów TPM, należy wyłączyć je przed kontynuowaniem dołączenie do hybrydowej usługi Azure AD. Firma Microsoft zapewnia wszystkie narzędzia wyłączanie trybu FIPS dla modułów TPM, ponieważ jest on zależny od producenta modułu TPM. Uzyskać pomoc, skontaktuj się z sprzęt OEM.

## <a name="review-how-to-control-the-hybrid-azure-ad-join-of-your-devices"></a>Dowiedz się, jak kontrolować dołączenie do hybrydowej usługi Azure AD urządzeń

Dołączenie do hybrydowej usługi Azure AD jest proces automatycznego rejestrowania urządzeń przyłączonych do domeny lokalnej za pomocą usługi Azure AD. Istnieją przypadki, w których nie chcesz wszystkich urządzeń do automatycznej rejestracji. Jest to przykład wartości true, podczas początkowego wdrożenia, aby sprawdzić, czy wszystko działa zgodnie z oczekiwaniami.

Aby uzyskać więcej informacji, zobacz [sposób kontrolowania dołączenie do hybrydowej usługi Azure AD urządzeń](hybrid-azuread-join-control.md)

## <a name="select-your-scenario"></a>Wybierz scenariusz

Można skonfigurować hybrydowych usługi Azure AD join w następujących scenariuszach:

- Domeny zarządzane
- Domeny federacyjne  

Jeśli środowisko zawiera zarządzane domeny, dołączenie do hybrydowej usługi Azure AD obsługuje:

- Przekazuj uwierzytelniania (PTA)
- Synchronizacja skrótów haseł (wersji)

Począwszy od wersji 1.1.819.0, program Azure AD Connect zapewnia kreator umożliwiający konfigurowanie dołączania hybrydowego do usługi Azure AD. Kreator pozwala znacznie uprościć proces konfiguracji. Aby uzyskać więcej informacji, zobacz:

- [Konfigurowanie hybrydowego dołączania do usługi Azure Active Directory dla domen federacyjnych](hybrid-azuread-join-federated-domains.md)
- [Konfigurowanie hybrydowego dołączania do usługi Azure Active Directory dla domen zarządzanych](hybrid-azuread-join-managed-domains.md)

 Jeśli zainstalowanie wymaganej wersji programu Azure AD Connect nie jest dostępną opcją w, zobacz [jak ręcznie skonfigurować rejestrację urządzeń](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-manual). 

## <a name="on-premises-ad-upn-support-in-hybrid-azure-ad-join"></a>Obsługa UPN usługi AD w środowisku lokalnym w dołączenie do hybrydowej usługi Azure AD

Czasami lokalnej usługi AD UPN mogą się różnić od usługi platformy Azure UPN usługi AD. W takich przypadkach systemu Windows 10 hybrydowej usługi Azure AD join udostępnia ograniczoną obsługę lokalnej nazwy UPN AD na podstawie [metodę uwierzytelniania](https://docs.microsoft.com/azure/security/azure-ad-choose-authn), typ domeny i wersji systemu Windows 10. Istnieją dwa typy w środowisku lokalnym UPN AD, które może znajdować się w danym środowisku:

- Routable UPN: Routing UPN ma prawidłową domenę zweryfikowaną, która jest zarejestrowana za pomocą rejestratora domen. Na przykład, jeśli contoso.com jest domena podstawowa w usłudze Azure AD, contoso.org jest domena podstawowa w lokalnym programie AD należące do firmy Contoso i [zweryfikowane w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain)
- Nierutowalny nazwa UPN: Nierutowalny nazwy UPN nie ma zweryfikowanej domeny. Dotyczy tylko w ramach prywatnej sieci organizacji. Na przykład, jeśli contoso.com jest domena podstawowa w usłudze Azure AD, contoso.local jest domena podstawowa w lokalnej usługi AD, ale nie jest możliwe do zweryfikowania domeny w Internecie i używane w katalogu firmy Contoso tylko w sieci.

Poniższa tabela zawiera szczegółowe informacje dotyczące pomocy technicznej dla tych lokalnego UPN usługi AD systemu Windows 10 hybrydowej usługi Azure AD sprzężenia

| Typ w środowisku lokalnym UPN usługi AD | Typ domeny | Wersja systemu Windows 10 | Opis |
| ----- | ----- | ----- | ----- |
| Routing | Federacyjne | W wersji 1703 | Ogólnie dostępna |
| Routing | Zarządzane | W wersji 1709 | Obecnie dostępna w prywatnej wersji zapoznawczej. Samoobsługowe Resetowanie HASEŁ usługi Azure AD nie jest obsługiwane. |
| Bez obsługi routingu | Federacyjne | W wersji 1803 | Ogólnie dostępna |
| Bez obsługi routingu | Zarządzane | Nieobsługiwane | |

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Konfiguruj dołączenie do usługi Azure Active Directory hybrydowej przez domeny federacyjne](hybrid-azuread-join-federated-domains.md)
> [Konfiguruj hybrydowych usługi Azure Active Directory join dla domeny zarządzanej](hybrid-azuread-join-managed-domains.md)

<!--Image references-->
[1]: ./media/hybrid-azuread-join-plan/12.png
