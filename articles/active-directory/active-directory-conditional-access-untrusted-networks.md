---
title: 'Jak: Konfigurowanie usługi Azure Active Directory zasady dostępu warunkowego dla próby dostępu z niezaufanymi sieciami | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować zasady dostępu warunkowego w usłudze Azure Active Directory (Azure AD) do podejmowania prób dostępu z niezaufanymi sieciami.
services: active-directory
keywords: dostęp warunkowy do aplikacji, dostęp warunkowy w usłudze Azure AD, bezpieczny dostęp do zasobów firmy, zasady dostępu warunkowego
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.component: protection
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/23/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 2dea5686add93d93f35e82445f411035a2451e33
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2018
ms.locfileid: "39525981"
---
# <a name="how-to-configure-conditional-access-policies-for-access-attempts-from-untrusted-networks"></a>Porady: Konfigurowanie zasad dostępu warunkowego w przypadku próby uzyskania dostępu z niezaufanymi sieciami   

W świecie urządzeń przenośnych i chmurze — Azure Active Directory (Azure AD) umożliwia logowanie jednokrotne do urządzeń, aplikacji i usług z dowolnego miejsca. W efekcie użytkownicy uzyskają dostęp swoje aplikacje w chmurze, nie tylko z siecią organizacji, ale z dowolnej niezaufanej lokalizacji internetowej. Za pomocą [dostępu warunkowego usługi Azure Active Directory (Azure AD)](active-directory-conditional-access-azure-portal.md), można kontrolować sposób autoryzowani użytkownicy mogą uzyskiwać dostęp do aplikacji w chmurze. Jednym z typowych wymagań w tym kontekście jest kontrolowanie dostępu prób inicjowanych z niezaufanymi sieciami. Ten artykuł zawiera informacje potrzebne do konfigurowania zasad dostępu warunkowego, która obsługuje ten wymóg. 

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule założono, że znasz: 

- Podstawowe pojęcia związane z dostępu warunkowego usługi Azure AD 
- Konfigurowanie zasad dostępu warunkowego w witrynie Azure portal

Zobacz:

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory](active-directory-conditional-access-azure-portal.md) — Przegląd dostępu warunkowego 

- [Szybki Start: Wymagaj uwierzytelniania Wieloskładnikowego dla określonych aplikacji przy użyciu dostępu warunkowego usługi Azure Active Directory](conditional-access/app-based-mfa.md) — Aby uzyskać pewne doświadczenie w konfigurowaniu zasad dostępu warunkowego. 


## <a name="scenario-description"></a>Opis scenariusza

Do głównej równowagę między zabezpieczeń i wydajności, może być wystarczające wymagają tylko użytkownika, aby zostać uwierzytelniony przy użyciu hasła. Jednak gdy podejmowana jest próba dostępu z sieci niezaufanej lokalizacji jest zwiększone ryzyko logowania nie są wykonywane przez autoryzowanych użytkowników. Aby rozwiązać ten problem, można zablokować próby dostępu z niezaufanymi sieciami. Alternatywnie możesz również wymagać uwierzytelniania wieloskładnikowego (MFA) do uzyskania ponownie dodatkową pewność, że nastąpiła próba przez prawowitym właścicielem konta. 

Przy użyciu dostępu warunkowego usługi Azure AD można rozwiązać tego wymagania, z jedną zasadę, która udziela dostępu: 

- Wybrana chmura usługi aplikacji

- Dla wybranych użytkowników i grup  

- Wymaganie uwierzytelniania wieloskładnikowego 

- Gdy podejmowana jest próba dostępu od: 

    - Lokalizacja, która nie jest zaufana


## <a name="considerations"></a>Zagadnienia do rozważenia

Żądanie tego scenariusza jest sformułowanie *gdy podejmowana jest próba dostępu z lokalizacji, która nie jest zaufana* do warunki dostępu warunkowego. W zasadach dostępu warunkowego można skonfigurować [warunek lokalizacji](conditional-access/location-condition.md) dla scenariuszy, które są powiązane z lokalizacji sieciowych. Warunek lokalizacji umożliwia wybranie nazwane lokalizacje, które reprezentują logiczne grupy zakresów adresów IP, innych krajów i regionów.  

Zazwyczaj organizacja ma jeden lub więcej zakresów adresów, na przykład 199.30.16.0 - 199.30.16.24.
Można skonfigurować nazwanych lokalizacji przez:

- Określenie tego zakresu (199.30.16.0/24) 

- Przypisywanie opisową nazwę, takich jak **sieci firmowej** 


Zamiast próbować definiowaniu wszystkie lokalizacje, które nie są zaufane, można wykonywać następujące czynności:

- Uwzględnij 

    ![Dostęp warunkowy](./media/active-directory-conditional-access-untrusted-networks/02.png)

- Wyklucz wszystkie zaufane lokalizacje 

    ![Dostęp warunkowy](./media/active-directory-conditional-access-untrusted-networks/01.png)



## <a name="implementation"></a>Wdrażanie

W przypadku metody opisane w tym artykule można teraz skonfigurować zasady dostępu warunkowego dla niezaufanych lokalizacji. Zasady należy zawsze przetestować przed udostępnieniem jej w środowisku produkcyjnym, aby upewnić się, że działa zgodnie z oczekiwaniami. W idealnym przypadku należy wykonać początkową testów w dzierżawie testowej, jeśli jest to możliwe. Aby uzyskać więcej informacji, zobacz [jak należy wdrożyć nowe zasady](conditional-access/best-practices.md#how-should-you-deploy-a-new-policy). 



## <a name="next-steps"></a>Kolejne kroki

Jeśli chcesz dowiedzieć się więcej na temat dostępu warunkowego, zobacz [co to jest dostęp warunkowy w usłudze Azure Active Directory?](active-directory-conditional-access-azure-portal.md)