---
title: Jak wymagać uwierzytelniania wieloskładnikowego (MFA) na potrzeby dostępu z niezaufanych sieci przy użyciu dostępu warunkowego Azure Active Directory (Azure AD) | Microsoft Docs
description: Dowiedz się, jak skonfigurować zasady dostępu warunkowego w usłudze Azure Active Directory (Azure AD) na potrzeby prób dostępu z niezaufanych sieci.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 12/10/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39ec09c1ecb94a5ae189317d89cce4bc8f279b48
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73175686"
---
# <a name="how-to-require-mfa-for-access-from-untrusted-networks-with-conditional-access"></a>Instrukcje: wymaganie uwierzytelniania MFA w celu uzyskania dostępu z niezaufanych sieci z dostępem warunkowym   

Usługa Azure Active Directory (Azure AD) umożliwia logowanie jednokrotne do urządzeń, aplikacji i usług z dowolnego miejsca. Użytkownicy mogą uzyskiwać dostęp do aplikacji w chmurze, nie tylko z sieci organizacji, ale również z niezaufanej lokalizacji w Internecie. Typowym najlepszym rozwiązaniem w przypadku dostępu z niezaufanych sieci jest wymaganie uwierzytelniania wieloskładnikowego (MFA).

Ten artykuł zawiera informacje potrzebne do skonfigurowania zasad dostępu warunkowego, które wymagają uwierzytelniania wieloskładnikowego na potrzeby dostępu z niezaufanych sieci. 

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule założono, że znasz: 

- [Podstawowe koncepcje](overview.md) dostępu warunkowego usługi Azure AD 
- [Najlepsze rozwiązania](best-practices.md) dotyczące konfigurowania zasad dostępu warunkowego w Azure Portal

## <a name="scenario-description"></a>Opis scenariusza

Aby określić równowagę między zabezpieczeniami i produktywnością, może być wystarczające, aby tylko hasło logowania z sieci organizacji było wymagane. Jednak w przypadku dostępu z niezaufanej lokalizacji sieciowej istnieje większe ryzyko, że logowania nie są wykonywane przez uprawnionych użytkowników. Aby rozwiązać ten problem, można zablokować dostęp z niezaufanych sieci. Alternatywnie można również wymagać uwierzytelniania wieloskładnikowego (MFA), aby uzyskać dodatkową gwarancję, że próba została podjęta przez uprawniony właściciel konta. 

Za pomocą dostępu warunkowego usługi Azure AD można rozwiązać ten wymóg przy użyciu jednej zasady, która udziela dostępu: 

- Do wybranych aplikacji w chmurze
- Dla wybranych użytkowników i grup  
- Wymaganie uwierzytelniania wieloskładnikowego 
- Gdy dostęp pochodzi z: 
   - Lokalizacja, która nie jest zaufana

## <a name="implementation"></a>Wdrażanie

Wyzwaniem tego scenariusza jest przetłumaczenie *dostępu z niezaufanej lokalizacji sieciowej* na warunek dostępu warunkowego. W zasadach dostępu warunkowego można skonfigurować [warunek lokalizacji](location-condition.md) , aby zająć się scenariuszami związanymi z lokalizacjami sieciowymi. Warunek lokalizacji umożliwia wybranie nazwanych lokalizacji, które są logicznymi grupami zakresów adresów IP, krajów i regionów.  

Zazwyczaj organizacja jest właścicielem co najmniej jednego zakresu adresów, na przykład 199.30.16.0-199.30.16.15.
Nazwaną lokalizację można skonfigurować przez:

- Określanie tego zakresu (199.30.16.0/28) 
- Przypisywanie nazwy opisowej, takiej jak **Sieć firmowa** 

Zamiast próbować definiować, które lokalizacje nie są zaufane, możesz:

- Uwzględnij dowolną lokalizację 

   ![Dostęp warunkowy](./media/untrusted-networks/02.png)

- Wyklucz wszystkie Zaufane lokalizacje 

   ![Dostęp warunkowy](./media/untrusted-networks/01.png)

## <a name="policy-deployment"></a>Wdrażanie zasad

Z podejściem opisanym w tym artykule można teraz skonfigurować zasady dostępu warunkowego dla niezaufanych lokalizacji. Aby upewnić się, że zasady działają zgodnie z oczekiwaniami, zalecanym najlepszym rozwiązaniem jest przetestowanie go przed wycofaniem do produkcji. Najlepiej użyć dzierżawy testowej, aby sprawdzić, czy nowe zasady działają zgodnie z oczekiwaniami. Aby uzyskać więcej informacji, zobacz [jak wdrożyć nowe zasady](best-practices.md#how-should-you-deploy-a-new-policy). 

## <a name="next-steps"></a>Następne kroki

Jeśli chcesz dowiedzieć się więcej na temat dostępu warunkowego, zobacz [co to jest dostęp warunkowy w Azure Active Directory?](../active-directory-conditional-access-azure-portal.md)
