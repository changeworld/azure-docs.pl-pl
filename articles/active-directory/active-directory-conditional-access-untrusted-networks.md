---
title: Jak można — Konfigurowanie usługi Azure Active Directory zasady dostępu warunkowego dla próby dostępu z niezaufanymi sieciami | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować zasady dostępu warunkowego w usłudze Azure Active Directory (Azure AD), aby prób dostępu z niezaufanymi sieciami.
services: active-directory
keywords: dostęp warunkowy do aplikacji, dostęp warunkowy przy użyciu usługi Azure AD, bezpieczny dostęp do zasobów firmy, zasady dostępu warunkowego
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/13/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 952a3a3952a96c7125e0b0dbe770b72c17a57101
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36232515"
---
# <a name="how-to-configure-conditional-access-policies-for-access-attempts-from-untrusted-networks"></a>Porady: Konfigurowanie zasad dostępu warunkowego dla prób dostępu z niezaufanych sieci   

W świecie pierwszy mobile, najpierw chmury Azure Active Directory (Azure AD) umożliwia logowanie jednokrotne do urządzeń, aplikacji i usług z dowolnego miejsca. W wyniku tego użytkownika użytkownicy mają dostęp do aplikacji w chmurze nie tylko z siecią organizacji, ale z dowolnej niezaufanej lokalizacji internetowej. Z [dostępu warunkowego w usłudze Azure Active Directory (Azure AD)](active-directory-conditional-access-azure-portal.md), można kontrolować sposób autoryzowani użytkownicy mogą uzyskiwać dostęp do aplikacji w chmurze. Jednym z typowych wymagań w tym kontekście jest kontrolowanie prób dostępu inicjowane z niezaufanymi sieciami. Ten artykuł zawiera informacje, które należy skonfigurować zasady dostępu warunkowego, które obsługuje to wymaganie. 

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule przyjęto założenie, że czytelnik zna: 

- Podstawowe pojęcia związane z dostępu warunkowego dla usługi Azure AD 
- Konfigurowanie zasad dostępu warunkowego w portalu Azure

Zobacz:

- [Co to jest dostępu warunkowego w usłudze Azure Active Directory](active-directory-conditional-access-azure-portal.md) — Omówienie dostępu warunkowego 

- [Szybki Start: Wymagają usługi MFA dla określonych aplikacji przy użyciu dostępu warunkowego usługi Azure Active Directory](active-directory-conditional-access-app-based-mfa.md) — Aby uzyskać pewne doświadczenie w konfigurowaniu zasad dostępu warunkowego. 


## <a name="scenario-description"></a>Opis scenariusza

Aby wzorca równowagi między zabezpieczeń i wydajności, może być wystarczające tylko wymaga uwierzytelnienia przy użyciu hasła użytkownika. Gdy podejmowana jest próba dostępu z sieci niezaufanej lokalizacji ma jednak zwiększone ryzyko, że logowania nie są wykonywane przez autoryzowanych użytkowników. Aby rozwiązać ten problem, można zablokować próby dostępu z niezaufanymi sieciami. Alternatywnie możesz również wymagać uwierzytelniania wieloskładnikowego (MFA), aby uzyskać wstecz dodatkowe gwarantują, że nastąpiła próba przez wiarygodnego właściciela konta. 

Przy użyciu dostępu warunkowego dla usługi Azure AD można spełnić to wymaganie z jedną zasadę, która udziela dostępu: 

- Do aplikacji w wybranej chmurze.

- Dla wybranych użytkowników i grup  

- Wymaganie uwierzytelniania wieloskładnikowego 

- Gdy podejmowana jest próba dostępu z: 

    - Lokalizacja, która nie jest zaufana


## <a name="considerations"></a>Zagadnienia do rozważenia

Wyzwanie ten scenariusz jest sformułowanie *gdy podejmowana jest próba dostępu z lokalizacji, która nie jest zaufana* do warunki dostępu warunkowego. W zasadach dostępu warunkowego, można skonfigurować [warunku lokalizacje](active-directory-conditional-access-locations.md) do scenariuszy adresów, które odnoszą się do lokalizacji sieciowych. Warunek lokalizacji umożliwia wybranie [o nazwie lokalizacje](active-directory-conditional-access-locations.md#named-locations), które reprezentują logiczne grupy zakresów adresów IP, innych krajów i regionów.  

Zazwyczaj jeden lub więcej zakresów adresów, na przykład 199.30.16.0 - 199.30.16.24 własnością Twojej organizacji.
Można skonfigurować lokalizację nazwane przez:

- Określanie tego zakresu (199.30.16.0/24) 

- Takich jak przypisywanie nazwę opisową **sieci firmowej** 


Zamiast w trakcie definiowaniu wszystkie lokalizacje, które nie są zaufane, można:

- Uwzględnij 

    ![Dostęp warunkowy](./media/active-directory-conditional-access-untrusted-networks/02.png)

- Wyklucz wszystkie zaufane lokalizacje 

    ![Dostęp warunkowy](./media/active-directory-conditional-access-untrusted-networks/01.png)



## <a name="implementation"></a>Wdrażanie

Dzięki podejście opisane w tym artykule można teraz skonfigurować zasady dostępu warunkowego dla niezaufanych lokalizacjach. Zawsze należy sprawdzić zasad przed udostępnieniem jej w środowisku produkcyjnym, aby upewnić się, że działa zgodnie z oczekiwaniami. W idealnym przypadku należy wykonać początkowej testów w dzierżawie testu, jeśli to możliwe. Aby uzyskać więcej informacji, zobacz [powinien wdrażania nowych zasad](active-directory-conditional-access-best-practices.md#how-should-you-deploy-a-new-policy). 



## <a name="next-steps"></a>Kolejne kroki

Jeśli chcesz dowiedzieć się więcej na temat dostępu warunkowego, zobacz [co to jest dostępu warunkowego w usłudze Azure Active Directory?](active-directory-conditional-access-azure-portal.md)