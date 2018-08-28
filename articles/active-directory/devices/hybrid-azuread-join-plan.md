---
title: Jak skonfigurować hybrydowych urządzeń w przyłączonych do usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak konfigurowanie urządzeń przyłączonych do usługi Azure Active Directory hybrydowych.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.component: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2018
ms.author: markvi
ms.reviewer: sandeo
ms.openlocfilehash: 808914dddcaefa4795264d3904e26ef6200f483e
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2018
ms.locfileid: "43087314"
---
# <a name="how-to-plan-your-hybrid-azure-active-directory-join-implementation"></a>Jak planowanie implementacji hybrydowej usługi Azure Active Directory join

W podobny sposób jak użytkownik urządzenie staje się innej tożsamości, które chcesz chronić, a także użyć do ochrony zasobów w dowolnym czasie i miejscu. W tym celu można wykonać, przenosząc tożsamości swoje urządzenia do usługi Azure AD przy użyciu jednej z następujących metod:

- Funkcja Azure AD join
- Dołączenie do hybrydowej usługi Azure AD
- Rejestracja w usłudze Azure AD

Między zasobami w chmurze i lokalnych, przenosząc urządzeń do usługi Azure AD, zmaksymalizuj produktywność użytkowników za pomocą logowania jednokrotnego (SSO). W tym samym czasie można zabezpieczyć dostęp do zasobów w chmurze i lokalnych przy użyciu [dostępu warunkowego](../active-directory-conditional-access-azure-portal.md).

Jeśli masz środowisko usługi Active Directory w środowisku lokalnym i chcesz dołączyć do urządzeń przyłączonych do domeny do usługi Azure AD, można to zrobić, konfigurowanie urządzeń przyłączonych do usługi Azure AD hybrydowych. Ten artykuł zawiera o powiązanych kroków dotyczących implementacji hybrydowej usługi Azure AD join w danym środowisku. 


## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule założono, że czytelnik zna [wprowadzenie do zarządzania urządzeniami w usłudze Azure Active Directory](../device-management-introduction.md).


## <a name="plan-your-implementation"></a>Planowanie implementacji

Aby zaplanować hybrydowego wdrożenia usługi Azure AD, należy się zapoznać z:

|   |   |
|---|---|
|![Zaznacz][1]|Przegląd obsługiwanych urządzeń|
|![Zaznacz][1]|Czynności przeglądu, które należy znać|
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

Rejestracja urządzenia niskiego poziomu Windows nie jest obsługiwane dla urządzeń skonfigurowanych do obsługi mobilny profil użytkownika lub mobilny dostęp do poświadczeń. Jeśli polegasz na roaming profilów lub ustawienia, należy użyć systemu Windows 10.

- Rejestracja urządzenia niskiego poziomu Windows **jest** obsługiwane w środowiskach inne niż federacyjne za pomocą bezproblemowego logowania jednokrotnego [usługi Azure Active Directory bezproblemowe logowanie jednokrotne](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-quick-start). 
 
- Rejestracja urządzenia niskiego poziomu Windows **nie** obsługiwane w przypadku korzystania z uwierzytelniania usługi Azure AD przekazywanego bez bezproblemowego logowania jednokrotnego.

- Rejestracja urządzenia niskiego poziomu Windows **nie** obsługiwane na urządzeniach przy użyciu profilów mobilnych. Jeśli polegasz na roaming profilów lub ustawienia, należy użyć systemu Windows 10.


Rejestracja systemu Windows Server działającego w roli kontrolera domeny (DC) nie jest obsługiwana.

Jeśli Twoja organizacja wymaga dostępu do Internetu za pośrednictwem uwierzytelnionego serwera proxy ruchu wychodzącego, upewnij się, że komputery z systemem Windows 10 może pomyślnie uwierzytelnić się serwera proxy ruchu wychodzącego. Ponieważ komputery z systemem Windows 10 są uruchamiane rejestracji urządzenia przy użyciu kontekstu komputera, należy skonfigurować uwierzytelnianie serwera proxy ruchu wychodzącego przy użyciu kontekstu maszyny.


Dołączenie do hybrydowej usługi Azure AD jest proces automatycznego rejestrowania urządzeń przyłączonych do domeny lokalnej za pomocą usługi Azure AD. Istnieją przypadki, w których nie chcesz wszystkich urządzeń do automatycznej rejestracji. Jeśli to PRAWDA dla Ciebie, zobacz [sposób kontrolowania dołączenie do hybrydowej usługi Azure AD urządzeń](hybrid-azuread-join-control.md).



## <a name="select-your-scenario"></a>Wybierz scenariusz

Można skonfigurować hybrydowych usługi Azure AD join w następujących scenariuszach:

- Domeny zarządzane
- Domeny federacyjne  



Jeśli środowisko zawiera zarządzane domeny, dołączenie do hybrydowej usługi Azure AD obsługuje:

- Przekazać za pośrednictwem uwierzytelniania (PTA) za pomocą bezproblemowego logowania jednokrotnego (SSO) 

- Hasło ma synchronizacji (wersji) za pomocą bezproblemowego logowania jednokrotnego (SSO) 

Począwszy od wersji 1.1.819.0, program Azure AD Connect zapewnia za pomocą kreatora, aby skonfigurować dołączenie do hybrydowej usługi Azure AD. Kreator umożliwia znacznie upraszcza proces konfigurowania. Aby uzyskać więcej informacji, zobacz:

- [Konfigurowanie hybrydowego dołączania do usługi Azure Active Directory dla domen federacyjnych](hybrid-azuread-join-federated-domains.md)


- [Konfigurowanie hybrydowego dołączania do usługi Azure Active Directory dla domen zarządzanych](hybrid-azuread-join-managed-domains.md)


 Jeśli zainstalowanie wymaganej wersji programu Azure AD Connect nie jest dostępną opcją w, zobacz [jak ręcznie skonfigurować rejestrację urządzeń](../device-management-hybrid-azuread-joined-devices-setup.md). 






## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Konfiguruj dołączenie do usługi Azure Active Directory hybrydowej przez domeny federacyjne](hybrid-azuread-join-federated-domains.md)
> [Konfiguruj hybrydowych usługi Azure Active Directory join dla domeny zarządzanej](hybrid-azuread-join-managed-domains.md)




<!--Image references-->
[1]: ./media/hybrid-azuread-join-plan/12.png
