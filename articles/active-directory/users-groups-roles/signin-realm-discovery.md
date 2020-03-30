---
title: Wyszukiwanie nazwy użytkownika podczas logowania - Usługa Azure Active Directory | Dokumenty firmy Microsoft
description: Jak wiadomości na ekranie odzwierciedlają wyszukiwanie nazwy użytkownika podczas logowania w usłudze Azure Active Directory
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c8b6a65a964016f702fcf75aa4cbdab33a952e3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74024250"
---
# <a name="home-realm-discovery-for-azure-active-directory-sign-in-pages"></a>Odnajdowanie obszaru macierzystego dla stron logowania usługi Azure Active Directory

Zmieniamy nasze zachowanie logowania usługi Azure Active Directory (Azure AD), aby zrobić miejsce dla nowych metod uwierzytelniania i poprawić użyteczność. Podczas logowania usługa Azure AD określa, gdzie użytkownik musi uwierzytelnić. Usługa Azure AD podejmuje inteligentne decyzje, odczytywanie ustawień organizacji i użytkownika dla nazwy użytkownika wprowadzonej na stronie logowania. Jest to krok w kierunku przyszłości bez hasła, który umożliwia dodatkowe poświadczenia, takie jak FIDO 2.0.

## <a name="home-realm-discovery-behavior"></a>Zachowanie odnajdywania sfery domowej

Historycznie odnajdowanie obszaru macierzystego było regulowane przez domenę, która jest dostarczana podczas logowania lub przez zasady odnajdywania obszarów macierzystych dla niektórych starszych aplikacji. Na przykład w naszym zachowaniu odnajdywania użytkownik usługi Azure Active Directory może błędnie wpisać swoją nazwę użytkownika, ale nadal dociera do ekranu kolekcji poświadczeń organizacji. Dzieje się tak, gdy użytkownik poprawnie podaje nazwę domeny organizacji "contoso.com". To zachowanie nie zezwala na ziarnistość, aby dostosować środowiska dla poszczególnych użytkowników.

Aby obsługiwać szerszy zakres poświadczeń i zwiększyć użyteczność, zachowanie wyszukiwania nazwy użytkownika usługi Azure Active Directory podczas procesu logowania jest teraz aktualizowane. Nowe zachowanie podejmuje inteligentne decyzje, odczytywanie ustawień dzierżawy i poziomu użytkownika na podstawie nazwy użytkownika wprowadzonej na stronie logowania. Aby to umożliwić, usługa Azure Active Directory sprawdzi, czy nazwa użytkownika wprowadzona na stronie logowania istnieje w określonej domenie lub przekierowuje użytkownika w celu podania poświadczeń.

Dodatkową zaletą tej pracy jest ulepszona obsługa błędów. Oto kilka przykładów ulepszonej obsługi wiadomości o błędach podczas logowania się do aplikacji obsługującej tylko użytkowników usługi Azure Active Directory.

- Nazwa użytkownika jest błędnie lub nazwa użytkownika nie została jeszcze zsynchronizowana z usługą Azure AD:
  
    ![nazwa użytkownika jest błędnie lub nie znaleziono](./media/signin-realm-discovery/typo-username.png)
  
- Nazwa domeny jest błędnie wpisywała:
  
    ![nazwa domeny jest błędnie lub nie została znaleziona](./media/signin-realm-discovery/typo-domain.png)
  
- Użytkownik próbuje zalogować się przy znanej domenie konsumenta:
  
    ![logowanie się w znanej domenie konsumenta](./media/signin-realm-discovery/consumer-domain.png)
  
- Hasło jest błędnie wpisane, ale nazwa użytkownika jest dokładna:  
  
    ![hasło jest błędnie wpisywane z dobrą nazwą użytkownika](./media/signin-realm-discovery/incorrect-password.png)
  
> [!IMPORTANT]
> Ta funkcja może mieć wpływ na domeny federacyjne, opierając się na starym odnajdowaniu obszaru macierzystego na poziomie domeny, aby wymusić federację. Aby uzyskać informacje o tym, kiedy zostanie dodana obsługa domeny federacyjnej, zobacz [Odnajdowanie obszaru macierzystego podczas logowania do usług Microsoft 365](https://azure.microsoft.com/updates/signin-hrd/). W międzyczasie niektóre organizacje przeszkoliły swoich pracowników, aby logowali się przy użyciu nazwy użytkownika, która nie istnieje w usłudze Azure Active Directory, ale zawiera właściwą nazwę domeny, ponieważ nazwy domen są obecnie przekierowywane przez użytkowników do punktu końcowego domeny organizacji. Nowe zachowanie logowania nie zezwala na to. Użytkownik jest powiadamiany, aby poprawić nazwę użytkownika i nie mogą zalogować się przy użyciu nazwy użytkownika, która nie istnieje w usłudze Azure Active Directory.
>
> Jeśli ty lub Twoja organizacja masz praktyki, które zależą od starego zachowania, ważne jest, aby administratorzy organizacji aktualizuj dokumentację logowania i uwierzytelniania pracowników oraz szkolili pracowników do używania ich nazwy użytkownika usługi Azure Active Directory do logowania.
  
Jeśli masz wątpliwości dotyczące nowego zachowania, zostaw swoje uwagi w sekcji **Opinie** w tym artykule.  

## <a name="next-steps"></a>Następne kroki

[Dostosowywanie marki logowania](../fundamentals/add-custom-domain.md)
