---
title: Co to jest ochrona linii bazowej w usłudze Azure Active Directory dostępu warunkowego | Dokumentacja firmy Microsoft
description: Dowiedz się, jak ochrony linii bazowej gwarantuje, że należy co najmniej poziom planu bazowego zabezpieczeń włączone w środowisku.
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
ms.date: 06/08/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 25ae4db2cd4f2a2cea74c428a272c6868acaa5c5
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/08/2018
ms.locfileid: "35249302"
---
# <a name="what-is-baseline-protection"></a>Co to jest ochrona linii bazowej?  

W ciągu ostatniego roku ataki tożsamości wzrosła 300%. Aby chronić środowiska przed atakami coraz większe, usługi Azure Active Directory (Azure AD) wprowadzono nową funkcję o nazwie ochrony linii bazowej. Ochrona linii bazowej jest zestaw wstępnie zdefiniowanych dostępu warunkowego. Celem tych zasad jest zapewnienie, że masz co najmniej poziom planu bazowego zabezpieczeń włączone w środowisku. 

W wersji zapoznawczej należy włączyć zasady linii bazowej, aby je aktywować. Post ogólnej dostępności te zasady są domyślnie włączone. 

Zasady ochrony pierwszej linii bazowej wymagają usługi MFA dla kont uprzywilejowanych. Osoby atakujące, którzy uzyskać kontrolę nad kont uprzywilejowanych może zrobić bardzo szkody, jest bardzo istotne, aby chronić te konta, najpierw. Następujące role uprzywilejowane znajdują się w zakresie dla tej zasady: 

- Administrator globalny  

- Administrator programu SharePoint  

- Administrator programu Exchange  

- Administrator dostępu warunkowego  

- Administrator zabezpieczeń  


![Usługa Azure Active Directory](./media/active-directory-conditional-access-baseline-protection/01.png)

## <a name="how-to-get-started"></a>Jak zacząć 

Aby włączyć zasady linii bazowej:  

1. Zaloguj się do [portalu Azure](https://portal.azure.com) jako administrator globalny, administrator zabezpieczeń lub administrator dostępu warunkowego.

2. W **portalu Azure**, na lewy pasek nawigacyjny, kliknij przycisk **usługi Azure Active Directory**.

    ![Usługa Azure Active Directory](./media/active-directory-conditional-access-baseline-protection/02.png)

3. Na **usługi Azure Active Directory** strony w **Zarządzaj** kliknij **dostępu warunkowego**.

    ![Dostęp warunkowy](./media/active-directory-conditional-access-baseline-protection/03.png)

4. Na liście zasad, kliknij **zasady podstawowe: wymagają usługi MFA dla administratorów (wersja zapoznawcza)**. 

5. Aby włączyć tę zasadę, kliknij przycisk **bezpośrednio za pomocą zasad**.

6. Kliknij pozycję **Zapisz**. 
 

Zasady podstawowe udostępnia opcję, aby wykluczyć użytkowników i grup. Należy wykluczyć jeden *[konto administracyjne awaryjnego dostępu](active-directory-admin-manage-emergency-access-accounts.md)* zapewnienie dzierżawcy nie zostało zablokowane.
  
 

## <a name="what-you-should-know"></a>Co należy wiedzieć 

Role katalogu, które znajdują się w zasadach linii bazowej są najbardziej uprzywilejowanych ról usługi Azure AD. Na podstawie opinii, innych użytkowników może być uwzględnione w przyszłości. 

Jeśli masz kont z uprawnieniami administratora w skryptach, należy użyć [zarządzane tożsamości usługi (MSI)](managed-service-identity/overview.md) lub [usługi podmiotów (z certyfikatami)](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal) zamiast tego. Jako celu tymczasowego obejścia problemu można wykluczyć określone konta użytkowników z zasad linii bazowej. 

Zasady dotyczą przepływy starszych uwierzytelniania POP, IMAP, starszego klienta pulpitu pakietu Office. 

## <a name="next-steps"></a>Kolejne kroki

Jeśli chcesz wiedzieć, jak skonfigurować zasady dostępu warunkowego, zobacz [wprowadzenie dostępu warunkowego w usłudze Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md).

Jeśli wszystko jest gotowe do skonfigurowania zasad dostępu warunkowego dla danego środowiska, zobacz [najlepszych rozwiązań dotyczących dostępu warunkowego w usłudze Azure Active Directory](active-directory-conditional-access-best-practices.md). 
