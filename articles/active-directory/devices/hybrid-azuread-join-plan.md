---
title: Jak zaplanować implementacji hybrydowej usługi Azure Active Directory join, w usłudze Azure Active Directory (Azure AD) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować urządzenia dołączone hybrydowo do usługi Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/08/2019
ms.author: markvi
ms.reviewer: sandeo
ms.openlocfilehash: ab268a27d8ce2f91c52dd5b5df0c22a564831e56
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/02/2019
ms.locfileid: "55663693"
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


## <a name="plan-your-implementation"></a>Planowanie implementacji

Aby zaplanować hybrydowego wdrożenia usługi Azure AD, należy się zapoznać z:

|   |   |
|---|---|
|![Zaznacz][1]|Przegląd obsługiwanych urządzeń|
|![Zaznacz][1]|Czynności przeglądu, które należy znać|
|![Zaznacz][1]|Dowiedz się, jak kontrolować dołączenie do hybrydowej usługi Azure AD urządzeń|
|![Zaznacz][1]|Wybierz scenariusz|


 

## <a name="review-supported-devices"></a>Przegląd obsługiwanych urządzeń 

Dołączenie do hybrydowej usługi Azure AD obsługuje urządzenia z szerokiego zakresu Windows. Ponieważ konfiguracji dla urządzeń ze starszymi wersjami systemu Windows wymaga wykonania czynności dodatkowym lub innym, obsługiwanych urządzeń są pogrupowane na dwie kategorie:

**Bieżące urządzenia Windows**

- Windows 10
    
- Windows Server 2016


Dla urządzeń z systemem Windows pulpitu systemu operacyjnego, obsługiwana wersja to Windows Update rozliczenia 10 (wersja 1607) lub nowszej. Najlepszym rozwiązaniem jest uaktualnienie do najnowszej wersji systemu Windows 10.



 **Windows niższego poziomu urządzeń**

- Windows 8.1
 
- Windows 7

- Windows Server 2012 R2
 
- Windows Server 2012 
 
- Windows Server 2008 R2 


Pierwszym krokiem planowania należy przejrzeć środowiska i ustalić, czy potrzebujesz do obsługi systemu Windows niższego poziomu urządzeń.



## <a name="review-things-you-should-know"></a>Czynności przeglądu, które należy znać

Nie można użyć dołączenie do hybrydowej usługi Azure AD, jeśli środowiska składa się z pojedynczego lasu, który synchronizowania danych tożsamości do więcej niż jednej dzierżawy usługi Azure AD.

Jeśli użytkownik korzysta z narzędzia przygotowywania systemu (Sysprep), upewnij się, że tworzenie obrazów z instalacji systemu Windows, który nie został skonfigurowany na dołączenie do hybrydowej usługi Azure AD.

Jeśli używasz migawkę maszyny wirtualnej (VM), aby utworzyć dodatkowe maszyny wirtualne, upewnij się, że używasz migawki maszyny Wirtualnej, która nie została skonfigurowana dla dołączenie do hybrydowej usługi Azure AD.

Dołączenie do hybrydowej usługi Azure AD Windows niższego poziomu urządzeń:

- **Jest** obsługiwane w środowiskach inne niż federacyjne za pośrednictwem [usługi Azure Active Directory bezproblemowe logowanie jednokrotne](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-quick-start). 

- **Nie jest** obsługiwane w przypadku korzystania z uwierzytelniania usługi Azure AD przekazywanego bez bezproblemowego logowania jednokrotnego.

- **Nie jest** obsługiwane podczas korzystania z roamingu poświadczeń lub profilu użytkownika mobilnego lub w przypadku używania infrastruktury pulpitu wirtualnego (VDI).


Rejestracja systemu Windows Server działającego w roli kontrolera domeny (DC) nie jest obsługiwana.

Jeśli organizacja wymaga dostępu do Internetu za pośrednictwem uwierzytelnionego serwera proxy ruchu wychodzącego, musisz upewnić się, że komputery z systemem Windows 10 mogą pomyślnie uwierzytelnić się na serwerze proxy ruchu wychodzącego. Ponieważ komputery z systemem Windows 10 uruchamiają rejestrację urządzenia przy użyciu kontekstu maszyny, konfigurowanie uwierzytelniania serwera proxy ruchu wychodzącego należy wykonać właśnie przy użyciu kontekstu maszyny.


Dołączenie do hybrydowej usługi Azure AD jest proces automatycznego rejestrowania urządzeń przyłączonych do domeny lokalnej za pomocą usługi Azure AD. Istnieją przypadki, w których nie chcesz wszystkich urządzeń do automatycznej rejestracji. Jeśli to PRAWDA dla Ciebie, zobacz [sposób kontrolowania dołączenie do hybrydowej usługi Azure AD urządzeń](hybrid-azuread-join-control.md).

Przyłączone do domeny systemu Windows 10 urządzenia są już [usługi Azure AD zarejestrowany](https://docs.microsoft.com/azure/active-directory/devices/overview#azure-ad-registered-devices) do swojej dzierżawy, należy rozważyć usunięcie tego stanu przed włączeniem dołączenie do hybrydowej usługi Azure AD. Podwójna stanu do obu, dołączenie do hybrydowej usługi Azure AD i Azure AD, zarejestrowane urządzenia nie jest obsługiwane. Z wersji systemu Windows 10 1809 wprowadzono następujące zmiany w celu uniknięcia tego podwójną stanu: 
 - Wszelkie istniejący stan usługi Azure AD zarejestrowany będą automatycznie usuwane, gdy urządzenie jest przyłączone do usługi Azure AD hybrydowej. 
 - Użytkownik może uniemożliwić urządzenia przyłączone do domeny usługi Azure AD zarejestrowany przez dodanie tego klucza rejestru - HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin, "BlockAADWorkplaceJoin" = dword: 00000001


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


 Jeśli zainstalowanie wymaganej wersji programu Azure AD Connect nie jest dostępną opcją w, zobacz [jak ręcznie skonfigurować rejestrację urządzeń](../device-management-hybrid-azuread-joined-devices-setup.md). 


## <a name="alternate-login-id-support-in-hybrid-azure-ad-join"></a>Obsługa Identyfikatora alternatywnej nazwy logowania w dołączenie do hybrydowej usługi Azure AD

Windows 10 hybrydowych w usłudze Azure AD join udostępnia ograniczoną obsługę [alternatywnych identyfikatorów logowania](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) na podstawie typu Identyfikatora logowania alternatywnej [metodę uwierzytelniania](https://docs.microsoft.com/azure/security/azure-ad-choose-authn), typ domeny i wersji systemu Windows 10. Istnieją dwa rodzaje alternatywne identyfikatory, które może znajdować się w danym środowisku:

 - Routing alternatywnego Identyfikatora logowania: Routing alternatywny identyfikator logowania ma prawidłową domenę zweryfikowaną, która jest zarejestrowana za pomocą rejestratora domen. Na przykład, jeśli contoso.com jest domena podstawowa, contoso.org i contoso.co.uk są prawidłowe domen, które są własnością firmy Contoso i [zweryfikowane w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain)
 
 - Bez obsługi routingu alternatywnego Identyfikatora logowania: Nierutowalny alternatywnego Identyfikatora logowania nie ma zweryfikowanej domeny. Dotyczy tylko w ramach prywatnej sieci organizacji. Na przykład jeśli podstawowy domena jest domeną contoso.com, contoso.local nie jest możliwe do zweryfikowania domeny w Internecie, ale jest używany w ramach sieci firmy Contoso.
 
Poniższa tabela zawiera szczegółowe informacje na temat obsługi jednego z tych alternatywnych identyfikatorów logowania w dołączenie do hybrydowej usługi Azure AD w systemie Windows 10

|Typ Identyfikatora logowania alternatywnej|Typ domeny|Wersja systemu Windows 10|Opis|
|-----|-----|-----|-----|
|Routing|Federacyjne |W wersji 1703|Ogólnie dostępna|
|Routing|Zarządzane|W wersji 1709|Obecnie dostępna w prywatnej wersji zapoznawczej. Samoobsługowe Resetowanie HASEŁ usługi Azure AD nie jest obsługiwane. |
|Bez obsługi routingu|Federacyjne|W wersji 1803|Ogólnie dostępna|
|Bez obsługi routingu|Zarządzane|Nieobsługiwane||



## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Konfiguruj dołączenie do usługi Azure Active Directory hybrydowej przez domeny federacyjne](hybrid-azuread-join-federated-domains.md)
> [Konfiguruj hybrydowych usługi Azure Active Directory join dla domeny zarządzanej](hybrid-azuread-join-managed-domains.md)




<!--Image references-->
[1]: ./media/hybrid-azuread-join-plan/12.png
