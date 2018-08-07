---
title: Co to jest ochrona linii bazowej dostępu warunkowego usługi Azure Active Directory? -preview | Dokumentacja firmy Microsoft
description: Dowiedz się, jak ochronę gwarantuje, że masz co najmniej poziomu linii bazowej zabezpieczeń włączone w danym środowisku usługi Azure Active Directory.
services: active-directory
keywords: dostęp warunkowy do aplikacji, dostęp warunkowy w usłudze Azure AD, bezpieczny dostęp do zasobów firmy, zasady dostępu warunkowego
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
ms.date: 07/02/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: dc28f4099661d027305faeeabf27de06cc98678d
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2018
ms.locfileid: "39531333"
---
# <a name="what-is-baseline-protection-preview"></a>Co to jest ochrona linii bazowej (wersja zapoznawcza)?  

W ciągu ostatniego roku atakami na tożsamość została zwiększona przez 300%. Dla ochrony środowiska przed atakami coraz większe, usługi Azure Active Directory (Azure AD) wprowadzono nową funkcję o nazwie ochrona linii bazowej. Ochrona linii bazowej to zestaw wstępnie zdefiniowanych [zasady dostępu warunkowego](../active-directory-conditional-access-azure-portal.md). Celem tych zasad jest, aby upewnić się, że masz co najmniej poziomu linii bazowej zabezpieczeń włączona we wszystkich wersjach programu Azure AD. 

Ten artykuł zawiera omówienie ochrony linii bazowej w usłudze Azure Active Directory.


 
## <a name="require-mfa-for-admins"></a>Wymagać uwierzytelniania Wieloskładnikowego dla administratorów

Użytkownicy z dostępem do kont uprzywilejowanych mają nieograniczony dostęp do danego środowiska. Ze względu na mocy, posiadane przez te konta należy potraktować je za pomocą szczególną uwagę. Jednej wspólnej metody w celu zwiększenia ochrony uprzywilejowanych kont jest wymagają silniejsze formularz weryfikacji konta, gdy są one używane do logowania. W usłudze Azure Active Directory możesz uzyskać silniejsze weryfikacji konta, wymagając uwierzytelniania wieloskładnikowego (MFA).  

**Wymagać uwierzytelniania Wieloskładnikowego dla administratorów** zasady linii bazowej, która wymaga uwierzytelniania Wieloskładnikowego dla następujących ról w katalogu: 

- Administrator globalny  

- Administrator programu SharePoint  

- Administrator programu Exchange  

- Administrator dostępu warunkowego  

- Administrator zabezpieczeń  


![Usługa Azure Active Directory](./media/baseline-protection/01.png)

Te zasady linii bazowej zapewnia możliwość wykluczenia użytkowników i grup. Możesz chcieć wykluczyć jeden *[konta administracyjnego dostępu awaryjnego](../users-groups-roles/directory-emergency-access.md)* zapewnienie dzierżawy nie są zablokowane.


## <a name="enable-a-baseline-policy"></a>Włącz zasady linii bazowej 

Mimo że zasady linii bazowej w wersji zapoznawczej, są domyślnie nie jest aktywowany. Musisz ręcznie włączyć zasadę, aby ją uaktywnić. Jak najszybciej po publicznym udostępnieniu produktów tej funkcji zasady są domyślnie aktywowany. Zmiana zachowania planowane jest powód, dlaczego masz oprócz Włączanie i wyłączanie trzecią opcję, aby ustawić stan zasady: **automatycznie Włącz zasady w przyszłości**. Po zaznaczeniu tej opcji, możesz zezwolić firma Microsoft będzie decydować, kiedy należy aktywować zasad.      


**Aby włączyć zasadę punktu odniesienia:**  

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) jako administratora globalnego, administratora zabezpieczeń lub administrator dostępu warunkowego.

2. W **witryny Azure portal**, na lewym pasku nawigacyjnym kliknij **usługi Azure Active Directory**.

    ![Usługa Azure Active Directory](./media/baseline-protection/02.png)

3. Na **usługi Azure Active Directory** stronie **zabezpieczeń** kliknij **dostępu warunkowego**.

    ![Dostęp warunkowy](./media/baseline-protection/05.png)

4. Na liście zasad kliknij zasady, które zaczyna się od **bazowymi zasadami:**. 

5. Aby włączyć zasady, kliknij przycisk **Użyj zasad natychmiast**.

6. Kliknij pozycję **Zapisz**. 
 
  
 

## <a name="what-you-should-know"></a>Co należy wiedzieć 

Podczas zarządzania niestandardowe zasady dostępu warunkowego wymaga licencji usługi Azure AD Premium, zasady linii bazowej są dostępne we wszystkich wersjach programu Azure AD.     

Role katalogu, które znajdują się w zasadach linii bazowej są najbardziej uprzywilejowanych ról usługi Azure AD. 

Jeśli ma uprzywilejowane konta, które są używane w skryptach, należy zastąpić je za pomocą [tożsamości usługi zarządzanej (MSI)](../managed-service-identity/overview.md) lub [jednostki przy użyciu certyfikatów usług](../../azure-resource-manager/resource-group-authenticate-service-principal.md). Jako rozwiązanie tymczasowe można wykluczyć określone konta użytkowników z zasad linii bazowej. 

Plan bazowy zasady mają zastosowanie do przepływów uwierzytelniania starszej wersji, takich jak POP, IMAP, starsze kliencie komputerowym pakietu Office. 




## <a name="next-steps"></a>Kolejne kroki

Jeśli chcesz wiedzieć, jak skonfigurować zasady dostępu warunkowego, zobacz [Rozpoczynanie pracy przy użyciu dostępu warunkowego w usłudze Azure Active Directory](../active-directory-conditional-access-azure-portal-get-started.md).

Jeśli wszystko jest gotowe do skonfigurowania zasad dostępu warunkowego dla danego środowiska, zobacz [najlepsze rozwiązania dotyczące dostępu warunkowego w usłudze Azure Active Directory](best-practices.md). 
