---
title: Jak - wymagają zarządzanych urządzeń do uzyskania dostępu do aplikacji w chmurze przy użyciu dostępu warunkowego usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Informacje o sposobie konfigurowania zasad dostępu warunkowego opartego na urządzenia usługi Azure Active Directory (Azure AD), wymagających dostępu do aplikacji w chmurze zarządzanych urządzeń.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: a27862a6-d513-43ba-97c1-1c0d400bf243
ms.service: active-directory
ms.component: protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 066d25e8953a2be4bd64cdd1af79b7f2a25dd5f9
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37036070"
---
# <a name="how-to-require-managed-devices-for-cloud-app-access-with-conditional-access"></a>Porady: Wymagaj zarządzanych urządzeń do uzyskania dostępu do aplikacji w chmurze przy użyciu dostępu warunkowego

W świecie pierwszy mobile, najpierw chmury Azure Active Directory (Azure AD) umożliwia logowanie jednokrotne do aplikacji i usług z dowolnego miejsca. Autoryzowani użytkownicy mogą uzyskać dostępu do aplikacji w chmurze z szerokiej gamy urządzeniami, takimi jak telefon komórkowy, a także urządzeń osobistych. Jednak w wielu środowiskach jest co najmniej kilka aplikacji, które powinni mieć dostęp tylko przez urządzenia, które spełniają standardy zabezpieczeń i zgodności. Te urządzenia są również znane jako zarządzanych urządzeń. 

W tym artykule opisano, jak konfigurować zasady dostępu warunkowego, które wymagają zarządzanych urządzeń, aby uzyskać dostępu do niektórych aplikacji w chmurze w swoim środowisku. 


## <a name="prerequisites"></a>Wymagania wstępne

Wymaganie zarządzanych urządzeń do chmury aplikacji dostępu ties **dostępu warunkowego dla usługi Azure AD** i **zarządzania urządzeniami w usłudze Azure AD** ze sobą. Jeśli nie masz doświadczenia z jednym z tych obszarów jeszcze, najpierw należy przeczytać następujące tematy:

- **[Dostęp warunkowy w usłudze Azure Active Directory](active-directory-conditional-access-azure-portal.md)**  — ten artykuł zawiera omówienie pojęć dotyczących dostępu warunkowego i terminologię pokrewne.

- **[Wprowadzenie do zarządzania urządzeniami w usłudze Azure Active Directory](device-management-introduction.md)**  — ten artykuł zawiera przegląd różnych opcji, należy pobrać urządzeń pod kontrolą organizacji. 


## <a name="scenario-description"></a>Opis scenariusza

Opanowanie równowagi między bezpieczeństwo i wydajność jest trudne. Mnożenie obsługiwanych urządzeń można uzyskać dostępu do zasobów w chmurze pozwala zwiększyć wydajność użytkowników. Na stronie Przerzucanie prawdopodobnie nie ma niektórych zasobów w danym środowisku, można uzyskać dostępu do urządzenia z poziomu ochrony nieznany. Dla odpowiednich zasobów należy włączyć, czy użytkownicy mogą uzyskiwać dostęp tylko do je za pomocą zarządzanego urządzenia. 

Przy użyciu dostępu warunkowego dla usługi Azure AD można spełnić to wymaganie z jedną zasadę, która udziela dostępu:

- Do aplikacji w wybranej chmurze.

- Dla wybranych użytkowników i grup

- Wymaganie zarządzanego urządzenia


## <a name="managed-devices"></a>Zarządzane urządzenia  

Proste warunków, zarządzanych urządzeń są urządzenia, które są w obszarze *jakiegoś* organizacyjnej formantu. W usłudze Azure AD wymagań wstępnych dotyczących zarządzanego urządzenia jest, że został on zarejestrowany z usługą Azure AD. Rejestrowanie urządzenia tworzy tożsamość urządzenia w postaci obiektu urządzenia. Ten obiekt jest używany przez platformę Azure, aby śledzić stan informacji o urządzeniu. Jako administrator usługi Azure AD, możesz już tego obiektu można używać do przełączania (Włączanie/wyłączanie) stan urządzenia.
  
![Warunków opartych na urządzeniu](./media/active-directory-conditional-access-policy-connected-applications/32.png)

Aby uzyskać urządzeń zarejestrowanych w usłudze Azure AD, masz trzy opcje:

- **[Urządzenia zarejestrowane usługi Azure AD](device-management-introduction.md#azure-ad-registered-devices)**  — urządzenie osobiste zarejestrowane w usłudze Azure AD

- **[Urządzeniach przyłączonych do usługi Azure AD](device-management-introduction.md#azure-ad-joined-devices)**  — Aby uzyskać organizacyjnej urządzenia systemu Windows 10, który nie jest dołączony do lokalnego AD zarejestrowane w usłudze Azure AD. 

- **[Urządzeniach przyłączonych do hybrydowej usługi Azure AD](device-management-introduction.md#hybrid-azure-ad-joined-devices)**  — urządzenie z systemem Windows 10 dołączonego do lokalnej usługi AD zarejestrowane w usłudze Azure AD.

Aby stać się zarządzanego urządzenia, zarejestrowane urządzenie musi być albo **urządzenia przyłączone do hybrydowej usługi Azure AD** lub **urządzenia, który został oznaczony jako zgodne**.  

![Warunków opartych na urządzeniu](./media/active-directory-conditional-access-policy-connected-applications/47.png)

 
## <a name="require-hybrid-azure-ad-joined-devices"></a>Wymagaj hybrydowe usługi Azure AD przyłączone do urządzeń

Zasady dostępu warunkowego, można wybrać **wymagają urządzenia dołączonego do hybrydowej usługi Azure AD** do stanu, że aplikacje wybranej chmury można uzyskać tylko za pomocą zarządzanego urządzenia. 

![Warunków opartych na urządzeniu](./media/active-directory-conditional-access-policy-connected-applications/10.png)

To ustawienie dotyczy tylko urządzeń z systemem Windows 10, które są częścią lokalnej usługi AD. Można zarejestrować tylko te urządzenia z usługą Azure AD przy użyciu sprzężenia hybrydowej usługi Azure AD, która jest [zautomatyzowanego procesu](device-management-hybrid-azuread-joined-devices-setup.md) zarejestrowane urządzenie z systemem Windows 10. 

![Warunków opartych na urządzeniu](./media/active-directory-conditional-access-policy-connected-applications/45.png)

Co sprawia, że hybrydowej usługi Azure AD łączone urządzenia zarządzanego urządzenia?  Dla urządzeń, które są częścią lokalnej usługi AD, zakłada się, czy kontrolę nad te urządzenia są wymuszane za pomocą rozwiązania do zarządzania, takich jak **programu System Center Configuration Manager (SCCM)** lub **(GP)Zasadygrupy** do zarządzania nimi. Ponieważ nie istnieje metoda dla usługi Azure AD w celu ustalenia, czy ma jednej z tych metod zostały zastosowane do urządzenia, wymaganie urządzenia dołączonego do hybrydowej usługi Azure AD jest stosunkowo słaba mechanizm wymagają zarządzanego urządzenia. Go zależy od użytkownika jako administrator, aby ocenić, czy metod, które są stosowane do lokalnej przyłączonych do domeny urządzenia są wystarczająco silne stanowić zarządzanego urządzenia, jeśli takie urządzenia jest również urządzenia dołączonego do hybrydowej usługi Azure AD.


## <a name="require-device-to-be-marked-as-compliant"></a>Wymagaj, aby urządzenie było oznaczone jako zgodne

Opcja *wymagają urządzenia może być oznaczony jako zgodne* to najwyższy formularza żądania zarządzanego urządzenia.

![Warunków opartych na urządzeniu](./media/active-directory-conditional-access-policy-connected-applications/11.png)

Ta opcja wymaga urządzenia mają być zarejestrowane w usłudze Azure AD i może być oznaczony jako zgodne przez:
         
- Usługa Intune.
- System zarządzania urządzeniami Przenośnymi urządzeniami przenośnymi innej firmy, która zarządza urządzeniami z systemem Windows 10 za pomocą integracji z usługą Azure AD. Systemy zarządzania urządzeniami Przenośnymi innej firmy dla systemu operacyjnego urządzenia o typie innym niż Windows 10 nie są obsługiwane.
 
![Warunków opartych na urządzeniu](./media/active-directory-conditional-access-policy-connected-applications/46.png)



Urządzenia, które jest oznaczona jako zgodna można założyć, że: 

- Zarządzania urządzeniami przenośnymi, używanych przez pracowników dostępu do danych firmowych
- Zarządzane aplikacje mobilne, używanych przez pracowników
- Informacje o Twojej firmie jest chroniony przez pomoc, aby kontrolować sposób uzyskuje dostęp do pracowników i udostępnia ją
- Urządzenia i jego aplikacje są zgodne z wymaganiami zabezpieczeń firmy




## <a name="next-steps"></a>Kolejne kroki

Przed skonfigurowaniem zasad dostępu warunkowego opartego na urządzeniu w danym środowisku, należy podjąć przyjrzeć się [najlepszych rozwiązań dotyczących dostępu warunkowego w usłudze Azure Active Directory](active-directory-conditional-access-best-practices.md).

