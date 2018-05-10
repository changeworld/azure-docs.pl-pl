---
title: Konfigurowanie zasad dostępu warunkowego opartego na urządzenia usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Informacje o sposobie konfigurowania zasad dostępu warunkowego opartego na urządzenia usługi Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: a27862a6-d513-43ba-97c1-1c0d400bf243
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 07957d5ec843c414813d69b7084915bcd70a5a61
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="configure-azure-active-directory-device-based-conditional-access-policies"></a>Konfigurowanie zasad dostępu warunkowego opartego na urządzenia usługi Azure Active Directory

Z [dostępu warunkowego w usłudze Azure Active Directory (Azure AD)](active-directory-conditional-access-azure-portal.md), można kontrolować sposób autoryzowani użytkownicy mogą uzyskać dostępu do zasobów. Na przykład można ograniczyć dostęp do niektórych zasobów do zarządzanych urządzeń. Zasady dostępu warunkowego, która wymaga zarządzanego urządzenia jest nazywana zasad dostępu warunkowego opartego na urządzeniu.

W tym temacie wyjaśniono, jak można skonfigurować zasady dostępu warunkowego opartego na urządzeniu dla Azure AD połączonych aplikacji. 


## <a name="before-you-begin"></a>Przed rozpoczęciem

Ties dostępu warunkowego opartego na urządzeniu **dostępu warunkowego dla usługi Azure AD** i **zarządzania urządzeniami usługi Azure AD razem**. Jeśli nie masz doświadczenia z jednym z tych obszarów jeszcze, najpierw należy przeczytać następujące tematy:

- **[Dostęp warunkowy w usłudze Azure Active Directory](active-directory-conditional-access-azure-portal.md)**  — ten temat zawiera omówienie pojęć dotyczących dostępu warunkowego i terminologię pokrewne.

- **[Wprowadzenie do zarządzania urządzeniami w usłudze Azure Active Directory](device-management-introduction.md)**  — ten temat zawiera omówienie różnych opcji, należy podłączyć urządzenia z usługą Azure AD. 



## <a name="managed-devices"></a>Zarządzane urządzenia  

W świecie pierwszy mobile, najpierw chmury Azure Active Directory umożliwia logowanie jednokrotne do urządzeń, aplikacji i usług z dowolnego miejsca. Dla niektórych zasobów w danym środowisku, udzielanie dostępu do odpowiednich użytkowników może nie być wystarczająca. Oprócz odpowiednich użytkowników możesz także mogą wymagać czy prób dostępu można wykonać tylko przy użyciu zarządzanego urządzenia.

Zarządzane urządzenia to urządzenie, które spełniają standardy zabezpieczeń i zgodności. Proste warunków, zarządzanych urządzeń są urządzenia, które są w obszarze *jakiegoś* organizacyjnej formantu. W usłudze Azure AD wymagań wstępnych dotyczących zarządzanego urządzenia jest, że został on zarejestrowany z usługą Azure AD. Rejestrowanie urządzenia tworzy tożsamość urządzenia w postaci obiektu urządzenia. Ten obiekt jest używany przez platformę Azure, aby śledzić stan informacji o urządzeniu. Jako administrator usługi Azure AD, możesz już tego obiektu można używać do przełączania (Włączanie/wyłączanie) stan urządzenia.
  
![Warunków opartych na urządzeniu](./media/active-directory-conditional-access-policy-connected-applications/32.png)

Aby uzyskać urządzeń zarejestrowanych w usłudze Azure AD, masz trzy opcje:

- **[Urządzenia zarejestrowane usługi Azure AD](device-management-introduction.md#azure-ad-registered-devices)**  — urządzenie osobiste zarejestrowane w usłudze Azure AD

- **[Urządzeniach przyłączonych do usługi Azure AD](device-management-introduction.md#azure-ad-joined-devices)**  — Aby uzyskać organizacyjnej urządzenia systemu Windows 10, który nie jest dołączony do lokalnego zarejestrowany AD zarejestrowana w usłudze Azure AD. 

- **[Urządzeniach przyłączonych do hybrydowej usługi Azure AD](device-management-introduction.md#hybrid-azure-ad-joined-devices)**  — urządzenie z systemem Windows 10 dołączonego do lokalnej usługi AD zarejestrowane w usłudze Azure AD.

Aby stać się zarządzanego urządzenia, zarejestrowane urządzenie może być hybrydowego przyłączonych do usługi Azure AD, urządzenie lub urządzenia, który został oznaczony jako zgodne.  

![Warunków opartych na urządzeniu](./media/active-directory-conditional-access-policy-connected-applications/47.png)


 
## <a name="require-hybrid-azure-ad-joined-devices"></a>Wymagaj hybrydowe usługi Azure AD przyłączone do urządzeń

Zasady dostępu warunkowego, można wybrać **wymagają urządzenia dołączonego do hybrydowej usługi Azure AD** do stanu, że aplikacje wybranej chmury można uzyskać tylko za pomocą zarządzanego urządzenia. 

![Warunków opartych na urządzeniu](./media/active-directory-conditional-access-policy-connected-applications/10.png)

To ustawienie dotyczy tylko urządzeń z systemem Windows 10, które dołączyły do usługi Azure AD lokalnymi. Można zarejestrować tylko te urządzenia z usługą Azure AD przy użyciu sprzężenia hybrydowej usługi Azure AD, która jest [zautomatyzowanego procesu](device-management-hybrid-azuread-joined-devices-setup.md) zarejestrowane urządzenie z systemem Windows 10. 

![Warunków opartych na urządzeniu](./media/active-directory-conditional-access-policy-connected-applications/45.png)

Co sprawia, że hybrydowej usługi Azure AD łączone urządzenia zarządzanego urządzenia?  Dla urządzeń, które są częścią lokalnej usługi AD, zakłada się, czy kontrolę nad te urządzenia są wymuszane za pomocą rozwiązania do zarządzania, takich jak **programu System Center Configuration Manager (SCCM)** lub **(GP)Zasadygrupy** do zarządzania nimi. Ponieważ nie istnieje metoda dla usługi Azure AD w celu ustalenia, czy ma jednej z tych metod zostały zastosowane do urządzenia, wymaganie urządzenia dołączonego do hybrydowej usługi Azure AD jest stosunkowo słaba mechanizm wymagają zarządzanego urządzenia. Go zależy od użytkownika jako administrator, aby ocenić, czy metod, które są stosowane do lokalnej przyłączonych do domeny urządzenia są wystarczająco silne stanowić zarządzanego urządzenia, jeśli takie urządzenia jest również urządzenia dołączonego do hybrydowej usługi Azure AD.


## <a name="require-device-to-be-marked-as-compliant"></a>Wymagaj, aby urządzenie było oznaczone jako zgodne

Opcja *wymagają urządzenia może być oznaczony jako zgodne* to najwyższy formularza żądania zarządzanego urządzenia.

![Warunków opartych na urządzeniu](./media/active-directory-conditional-access-policy-connected-applications/11.png)

Ta opcja wymaga urządzenia mają być zarejestrowane w usłudze Azure AD i może być oznaczony jako zgodne przez:
         
- Usługa Intune 
- Urządzeniami przenośnymi innej firmy zarządzanych systemu, która zarządza urządzeniami z systemem Windows 10 za pomocą integracji z usługą Azure AD 
 
![Warunków opartych na urządzeniu](./media/active-directory-conditional-access-policy-connected-applications/46.png)



Urządzenia, które jest oznaczona jako zgodna można założyć, że: 

- Zarządzania urządzeniami przenośnymi, używanych przez pracowników dostępu do danych firmowych
- Zarządzane aplikacje mobilne, używanych przez pracowników
- Informacje o Twojej firmie jest chroniony przez pomoc, aby kontrolować sposób uzyskuje dostęp do pracowników i udostępnia ją
- Urządzenia i jego aplikacje są zgodne z wymaganiami zabezpieczeń firmy




## <a name="next-steps"></a>Kolejne kroki

Przed skonfigurowaniem zasad dostępu warunkowego opartego na urządzeniu w danym środowisku, należy podjąć przyjrzeć się [najlepszych rozwiązań dotyczących dostępu warunkowego w usłudze Azure Active Directory](active-directory-conditional-access-best-practices.md).

