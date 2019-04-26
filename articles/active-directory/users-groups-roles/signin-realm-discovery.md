---
title: Wyszukiwanie nazwy użytkownika podczas logowania uwierzytelniania — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Jak na ekranie komunikatów odzwierciedla wyszukiwanie nazwy użytkownika podczas logowania
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/15/2019
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6b6846c5f907c41db16e99883be7041a68357586
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60468937"
---
# <a name="home-realm-discovery-for-azure-active-directory-sign-in-pages"></a>Odnajdowanie obszaru macierzystego dla strony logowania usługi Azure Active Directory

Zmieniamy zachowanie logowania usługi Azure Active Directory (Azure AD), aby zrobić miejsce na nowe metody uwierzytelniania i poprawić użyteczność. Podczas logowania usługa Azure AD określa, gdzie użytkownik musi się uwierzytelnić. Usługa Azure AD podejmuje inteligentne decyzje, odczytując ustawienia użytkownika i organizacji dla nazwy użytkownika wprowadzonej na stronie logowania. Jest to krok w kierunku przyszłości bez haseł, w której będzie można używać dodatkowych poświadczeń, takich jak FIDO 2.0.

## <a name="home-realm-discovery-behavior"></a>Zachowanie odnajdowania obszaru macierzystego

W przeszłości odnajdowania obszaru macierzystego był prawu domeny, który znajduje się podczas logowania lub przy użyciu zasad odnajdowania obszaru macierzystego dla niektórych starszych aplikacji. Na przykład w zachowaniu naszych odnajdywania użytkownika usługi Azure Active Directory mogą błędnie swoją nazwę użytkownika ale nadal są odbierane na ekranie zbioru poświadczeń używanych w organizacji. Dzieje się tak, gdy użytkownik udostępnia poprawnie organizacji nazwy domeny "contoso.com". Takie zachowanie nie pozwala na szczegółowe dostosowanie środowiska dla poszczególnych użytkowników.

Do obsługi szerszego zakresu poświadczeń i zwiększenia użyteczności, zachowania wyszukiwania nazwy użytkownika usługi Azure Active Directory w procesie logowania jest już uaktualniona. Nowe zachowanie sprawia, że inteligentne decyzje, czytając dzierżawy i użytkownika ustawienia poziomu oparciu o nazwę użytkownika, wprowadzone na stronie logowania. Aby to umożliwić, usługi Azure Active Directory sprawdzi, czy nazwa, którą podano na stronie logowania istnieje w ich określonej domeny lub przekierowuje użytkownika o podanie swoich poświadczeń.

Dodatkową zaletą tej pracy jest błąd Ulepszone komunikaty. Poniżej przedstawiono kilka przykładów ulepszone błąd komunikatów podczas logowania się do aplikacji, która obsługuje tylko użytkownicy usługi Azure Active Directory.

1. Nazwa użytkownika jest błędnie wpisana lub nazwa użytkownika nie ma jeszcze zsynchronizowane z usługą Azure AD:
  
    ![Nazwa użytkownika jest błędnie wpisana lub nie można odnaleźć](./media/signin-realm-discovery/typo-username.png)
  
2. Nazwa domeny jest źle wpisano:
  
    ![Nazwa domeny jest błędnie wpisana lub nie można odnaleźć](./media/signin-realm-discovery/typo-domain.png)
  
3. Użytkownik próbuje zalogować się przy użyciu domena konsumencka znane:
  
    ![Zaloguj się przy użyciu domena konsumencka znane](./media/signin-realm-discovery/consumer-domain.png)
  
4. Hasło jest źle wpisano, ale nazwa użytkownika jest dokładne:  
  
    ![hasło jest źle wpisano przy użyciu właściwej nazwy użytkownika](./media/signin-realm-discovery/incorrect-password.png)
  
> [!IMPORTANT]
> Ta funkcja może mieć wpływ na domen federacyjnych, opierając się na starym poziomu domeny odnajdowania obszaru macierzystego do Federacji force. Aktualizacje na dodania obsługi domeny federacyjnej, zobacz [Home odnajdowania obszaru podczas logowania do usługi Microsoft 365](https://azure.microsoft.com/en-us/updates/signin-hrd/). W międzyczasie niektóre organizacje mają skonfigurowanych pod kątem pracownikom zalogować się przy użyciu nazwy użytkownika, która nie istnieje w usłudze Azure Active Directory, ale zawiera nazwy domeny, ponieważ nazwy domen kieruje użytkowników aktualnie do endpoint domeny w swojej organizacji. Nowe zachowanie logowania nie zezwala na to. Użytkownik jest powiadamiany, aby rozwiązać nazwę użytkownika i nie mogą zalogować się przy użyciu nazwy użytkownika, który nie istnieje w usłudze Azure Active Directory.
>
> Jeśli Ty lub Twoja organizacja ma rozwiązania, które są zależne od starsze zachowanie, jest ważne dla administratorów organizacji, aby zaktualizować dokumentację logowania i uwierzytelniania pracowników i szkolenie pracowników, zaloguj się przy użyciu swoją nazwę użytkownika usługi Azure Active Directory.
  
Jeśli masz problemy z zachowaniem nowe, należy pozostawić uwagi w **opinii** dalszej części tego artykułu.  

## <a name="next-steps"></a>Kolejne kroki

[Dostosowywanie logowania znakowanie](../fundamentals/add-custom-domain.md)
