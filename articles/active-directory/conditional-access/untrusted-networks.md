---
title: Instrukcje ustawiania wymogu uwierzytelniania wieloskładnikowego (MFA) dla dostępu z niezaufanymi sieciami przy użyciu dostępu warunkowego usługi Azure Active Directory (Azure AD) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować zasady dostępu warunkowego w usłudze Azure Active Directory (Azure AD) do podejmowania prób dostępu z niezaufanymi sieciami.
services: active-directory
keywords: Dostęp warunkowy do aplikacji, dostęp warunkowy w usłudze Azure AD, bezpieczny dostęp do zasobów firmy, zasady dostępu warunkowego
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.subservice: conditional-access
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2018
ms.author: joflore
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa9760c44df13c33687c279478935600325dd085
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67111878"
---
# <a name="how-to-require-mfa-for-access-from-untrusted-networks-with-conditional-access"></a>Instrukcje: Wymagać uwierzytelniania Wieloskładnikowego, aby uzyskać dostęp z niezaufanymi sieciami przy użyciu dostępu warunkowego   

Usługa Azure Active Directory (Azure AD) umożliwia logowanie jednokrotne do urządzeń, aplikacji i usług z dowolnego miejsca. Użytkownicy uzyskają dostęp swoje aplikacje w chmurze, nie tylko z siecią organizacji, ale z dowolnej niezaufanej lokalizacji internetowej. Typowe, najlepszym rozwiązaniem dla dostępu z niezaufanymi sieciami jest wymaganie uwierzytelniania wieloskładnikowego (MFA).

Ten artykuł zawiera informacje potrzebne do konfigurowania zasad dostępu warunkowego, która wymaga uwierzytelniania Wieloskładnikowego dla dostępu z niezaufanymi sieciami. 

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule założono, że znasz: 

- [Podstawowe pojęcia](overview.md) dostępu warunkowego usługi Azure AD 
- [Najlepsze praktyki](best-practices.md) na temat konfigurowania zasad dostępu warunkowego w witrynie Azure portal



## <a name="scenario-description"></a>Opis scenariusza

Do głównej równowagę między zabezpieczeń i wydajności, może być wystarczające tylko Wymagaj hasła do logowania z siecią organizacji. Aby uzyskać dostęp z lokalizacji niezaufanych sieci, ma jednak zwiększoną ryzyko logowania nie są wykonywane przez autoryzowanych użytkowników. Aby rozwiązać ten problem, możesz zablokować dostęp z niezaufanymi sieciami. Alternatywnie możesz również wymagać uwierzytelniania wieloskładnikowego (MFA) do uzyskania ponownie dodatkową pewność, że nastąpiła próba przez prawowitym właścicielem konta. 

Przy użyciu dostępu warunkowego usługi Azure AD można rozwiązać tego wymagania, z jedną zasadę, która udziela dostępu: 

- Wybrana chmura usługi aplikacji

- Dla wybranych użytkowników i grup  

- Wymaganie uwierzytelniania wieloskładnikowego 

- Gdy dostęp jest pochodzi od: 

    - Lokalizacja, która nie jest zaufana


## <a name="implementation"></a>Wdrażanie

Żądanie tego scenariusza jest sformułowanie *dostępu z sieci niezaufanej lokalizacji* do warunki dostępu warunkowego. W zasadach dostępu warunkowego można skonfigurować [warunek lokalizacji](location-condition.md) dla scenariuszy, które są powiązane z lokalizacji sieciowych. Warunek lokalizacji umożliwia wybranie nazwane lokalizacje, będące logicznie zgrupowanymi zakresów adresów IP, krajów i regionów.  

Zazwyczaj organizacja ma jeden lub więcej zakresów adresów, na przykład 199.30.16.0 - 199.30.16.24.
Można skonfigurować nazwanych lokalizacji przez:

- Określenie tego zakresu (199.30.16.0/24) 

- Przypisywanie opisową nazwę, takich jak **sieci firmowej** 


Zamiast próbować definiowaniu wszystkie lokalizacje, które nie są zaufane, można wykonywać następujące czynności:

- Obejmują z dowolnego miejsca 

    ![Dostęp warunkowy](./media/untrusted-networks/02.png)

- Wyklucz wszystkie zaufane lokalizacje 

    ![Dostęp warunkowy](./media/untrusted-networks/01.png)



## <a name="policy-deployment"></a>Wdrażanie zasad

W przypadku metody opisane w tym artykule można teraz skonfigurować zasady dostępu warunkowego dla niezaufanych lokalizacji. Aby upewnić się, że zasad usługi działa zgodnie z oczekiwaniami, zalecanym najlepszym rozwiązaniem jest przetestowanie go przed udostępnieniem jej w środowisku produkcyjnym. W idealnym przypadku umożliwia to dzierżawa testowa Sprawdź, czy nowe zasady działa zgodnie z oczekiwaniami. Aby uzyskać więcej informacji, zobacz [wdrażanie nowych zasad](best-practices.md#how-should-you-deploy-a-new-policy). 



## <a name="next-steps"></a>Kolejne kroki

Jeśli chcesz dowiedzieć się więcej na temat dostępu warunkowego, zobacz [co to jest dostęp warunkowy w usłudze Azure Active Directory?](../active-directory-conditional-access-azure-portal.md)
