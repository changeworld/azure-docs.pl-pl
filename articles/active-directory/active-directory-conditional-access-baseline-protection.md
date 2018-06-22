---
title: Co to jest ochrona linii bazowej w usłudze Azure Active Directory dostępu warunkowego? -preview | Dokumentacja firmy Microsoft
description: Dowiedz się, jak ochrony linii bazowej gwarantuje, że należy co najmniej poziom planu bazowego zabezpieczeń włączone w środowisku usługi Azure Active Directory.
services: active-directory
keywords: dostęp warunkowy do aplikacji, dostęp warunkowy przy użyciu usługi Azure AD, bezpieczny dostęp do zasobów firmy, zasady dostępu warunkowego
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.component: protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/21/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 86b57a82573760ac73975e851b2bb4caf769845b
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/21/2018
ms.locfileid: "36308564"
---
# <a name="what-is-baseline-protection---preview"></a>Co to jest ochrona linii bazowej? -preview  

W ciągu ostatniego roku ataki tożsamości wzrosła 300%. Aby chronić środowiska przed atakami coraz większe, usługi Azure Active Directory (Azure AD) wprowadzono nową funkcję o nazwie ochrony linii bazowej. Ochrona linii bazowej jest zestaw wstępnie zdefiniowanych [zasady dostępu warunkowego](active-directory-conditional-access-azure-portal.md). Celem tych zasad jest zapewnienie, że masz co najmniej poziom planu bazowego zabezpieczeń włączona we wszystkich wersjach usługi Azure AD. 

Ten artykuł zawiera omówienie ochrony linii bazowej w usłudze Azure Active Directory.


 
## <a name="require-mfa-for-admins"></a>Uwierzytelniania MFA można wymagać dla administratorów

Użytkownicy z dostępem do kont uprzywilejowanych mają nieograniczony dostęp do środowiska. Z powodu zasilania, który ma te konta należy potraktować je z szczególną. Jednej wspólnej metody poprawiające ochronę kont uprzywilejowanych jest wymagają silniejszego formę weryfikacji konta, gdy są one używane do logowania. W usłudze Azure Active Directory wymagając uwierzytelniania wieloskładnikowego (MFA) można uzyskać silniejszych weryfikacji konta.  

**Uwierzytelniania MFA można wymagać dla administratorów** są to zasady linii bazowej, które wymagają usługi MFA dla następujących ról katalogu: 

- Administrator globalny  

- Administrator programu SharePoint  

- Administrator programu Exchange  

- Administrator dostępu warunkowego  

- Administrator zabezpieczeń  


![Usługa Azure Active Directory](./media/active-directory-conditional-access-baseline-protection/01.png)

Ta zasada linii bazowej udostępnia opcję, aby wykluczyć użytkowników i grup. Należy wykluczyć jeden *[konto administracyjne awaryjnego dostępu](active-directory-admin-manage-emergency-access-accounts.md)* zapewnienie dzierżawcy nie zostało zablokowane.


## <a name="enable-a-baseline-policy"></a>Włącz zasady linii bazowej 

Zasady linii bazowej są w wersji zapoznawczej, są one domyślnie nie jest aktywowany. Musisz ręcznie włączyć zasadę, jeśli chcesz aktywować go. Jak ta funkcja została osiągnięta ogólnej dostępności, zasady są domyślnie aktywowany. Zmiana zachowania planowane jest przyczyny, dlaczego masz oprócz aktywowanie i dezaktywowanie trzecia opcja można ustawić stanu zasady: **automatycznie włączaj w przyszłości zasady**. Po zaznaczeniu tej opcji, możesz powierzyć firmie Microsoft podjęcie decyzji dotyczącej aktywować zasad.      


**Aby włączyć zasadę linii bazowej:**  

1. Zaloguj się do [portalu Azure](https://portal.azure.com) jako administrator globalny, administrator zabezpieczeń lub administrator dostępu warunkowego.

2. W **portalu Azure**, na lewy pasek nawigacyjny, kliknij przycisk **usługi Azure Active Directory**.

    ![Usługa Azure Active Directory](./media/active-directory-conditional-access-baseline-protection/02.png)

3. Na **usługi Azure Active Directory** strony w **Zarządzaj** kliknij **dostępu warunkowego**.

    ![Dostęp warunkowy](./media/active-directory-conditional-access-baseline-protection/03.png)

4. Na liście zasad, kliknij zasady, która rozpoczyna się od **zasady podstawowe:**. 

5. Aby włączyć tę zasadę, kliknij przycisk **bezpośrednio za pomocą zasad**.

6. Kliknij pozycję **Zapisz**. 
 


  
 

## <a name="what-you-should-know"></a>Co należy wiedzieć 

Gdy zasady dostępu warunkowego niestandardowych zarządzania wymaga licencji usługi Azure AD Premium, linii bazowej zasady są dostępne we wszystkich wersjach usługi Azure AD.     

Role katalogu, które znajdują się w zasadach linii bazowej są najbardziej uprzywilejowanych ról usługi Azure AD. 

Jeśli ma uprzywilejowane konta, które są używane w skryptach, należy zastąpić je za pomocą [zarządzane tożsamości usługi (MSI)](./managed-service-identity/overview.md) lub [usługi podmiotów z certyfikatami](../azure-resource-manager/resource-group-authenticate-service-principal.md). Jako celu tymczasowego obejścia problemu można wykluczyć określone konta użytkowników z zasad linii bazowej. 

Zasady linii bazowej stosowana do przepływów starszych uwierzytelniania POP, IMAP, starszego klienta pulpitu pakietu Office. 




## <a name="next-steps"></a>Kolejne kroki

Jeśli chcesz wiedzieć, jak skonfigurować zasady dostępu warunkowego, zobacz [wprowadzenie dostępu warunkowego w usłudze Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md).

Jeśli wszystko jest gotowe do skonfigurowania zasad dostępu warunkowego dla danego środowiska, zobacz [najlepszych rozwiązań dotyczących dostępu warunkowego w usłudze Azure Active Directory](active-directory-conditional-access-best-practices.md). 
