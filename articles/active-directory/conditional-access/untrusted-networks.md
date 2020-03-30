---
title: Wymagaj usługi MFA z niezaufanych sieci — usługa Azure Active Directory
description: Dowiedz się, jak skonfigurować zasady dostępu warunkowego w usłudze Azure Active Directory (Azure AD) do prób dostępu z niezaufanych sieci.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4cedec7f0bd51460796d8138f8d481d2982098f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74380005"
---
# <a name="how-to-require-mfa-for-access-from-untrusted-networks-with-conditional-access"></a>Jak: Wymaganie usługi MFA w celu uzyskania dostępu z niezaufanych sieci z dostępem warunkowym   

Usługa Azure Active Directory (Azure AD) umożliwia logowanie jednokrotne na urządzeniach, aplikacjach i usługach z dowolnego miejsca. Użytkownicy mogą uzyskiwać dostęp do aplikacji w chmurze nie tylko z sieci organizacji, ale także z dowolnej niezaufanej lokalizacji internetowej. Typowym najlepszym rozwiązaniem dla dostępu z niezaufanych sieci jest wymaganie uwierzytelniania wieloskładnikowego (MFA).

W tym artykule przedstawiono informacje potrzebne do skonfigurowania zasad dostępu warunkowego, która wymaga usługi MFA dla dostępu z niezaufanych sieci. 

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule założono, że jesteś zaznajomiony z: 

- [Podstawowe pojęcia](overview.md) dostępu warunkowego usługi Azure AD 
- [Najważniejsze wskazówki dotyczące](best-practices.md) konfigurowania zasad dostępu warunkowego w witrynie Azure portal

## <a name="scenario-description"></a>Opis scenariusza

Aby opanować równowagę między zabezpieczeniami a wydajnością, może być wystarczające, aby wymagać tylko hasła do logowania z sieci organizacji. Jednak w przypadku dostępu z niezaufanej lokalizacji sieciowej istnieje zwiększone ryzyko, że logowania nie są wykonywane przez legalnych użytkowników. Aby rozwiązać ten problem, można zablokować dostęp z niezaufanych sieci. Alternatywnie można również wymagać uwierzytelniania wieloskładnikowego (MFA), aby odzyskać dodatkową pewność, że próba została podjęta przez prawowitego właściciela konta. 

Za pomocą usługi Azure AD dostęp warunkowy, można rozwiązać to wymaganie za pomocą jednej zasady, która udziela dostępu: 

- Do wybranych aplikacji w chmurze
- Dla wybranych użytkowników i grup  
- Wymaganie uwierzytelniania wieloskładnikowego 
- Gdy dostęp pochodzi z: 
   - Lokalizacja, która nie jest zaufana

## <a name="implementation"></a>Wdrażanie

Wyzwaniem w tym scenariuszu jest tłumaczenie *dostępu z niezaufanej lokalizacji sieciowej* na warunek dostępu warunkowego. W zasadach dostępu warunkowego można skonfigurować [warunek lokalizacji](location-condition.md) tak, aby uwzględniał scenariusze związane z lokalizacjami sieciowymi. Warunek lokalizacji umożliwia wybranie nazwanych lokalizacji, które są logicznymi grupami zakresów adresów IP, krajów i regionów.  

Zazwyczaj organizacja jest właścicielem co najmniej jednego zakresu adresów, na przykład 199.30.16.0 - 199.30.16.15.
Nazwaną lokalizację można skonfigurować przez:

- Określenie tego zakresu (199.30.16.0/28) 
- Przypisywanie nazwy opisowej, takiej jak **Sieć firmowa** 

Zamiast próbować zdefiniować, jakie są wszystkie lokalizacje, które nie są zaufane, możesz:

- Uwzględnij dowolną lokalizację 

   ![Dostęp warunkowy](./media/untrusted-networks/02.png)

- Wykluczanie wszystkich zaufanych lokalizacji 

   ![Dostęp warunkowy](./media/untrusted-networks/01.png)

## <a name="policy-deployment"></a>Wdrażanie zasad

Dzięki podejściu opisanemu w tym artykule można teraz skonfigurować zasady dostępu warunkowego dla niezaufanych lokalizacji. Aby upewnić się, że zasady działają zgodnie z oczekiwaniami, zalecaną najlepszą praktyką jest przetestowanie go przed wprowadzeniem go do produkcji. W idealnym przypadku należy użyć dzierżawy testowej, aby sprawdzić, czy nowe zasady działają zgodnie z oczekiwaniami. Aby uzyskać więcej informacji, zobacz [Jak wdrożyć nową zasadę](best-practices.md#how-should-you-deploy-a-new-policy). 

## <a name="next-steps"></a>Następne kroki

Jeśli chcesz dowiedzieć się więcej o dostępie warunkowym, zobacz [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../active-directory-conditional-access-azure-portal.md)
