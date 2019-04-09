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
ms.date: 04/03/2019
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 91d0dbaf9b648f42ef535d8b491df04b2c7042d7
ms.sourcegitcommit: 045406e0aa1beb7537c12c0ea1fbf736062708e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/04/2019
ms.locfileid: "59007566"
---
# <a name="home-realm-discovery-during-sign-in-for-microsoft-365-services"></a>Odnajdowanie obszaru macierzystego podczas logowania do usługi Microsoft 365

Zmieniamy zachowanie w logowania naszej usługi Azure Active Directory (Azure AD), aby zwolnić miejsce dla nowych metod uwierzytelniania i zwiększyć użyteczność. Podczas logowania usługi Azure AD Określa, gdzie użytkownik musi uwierzytelnić. Usługa Azure AD sprawia, że inteligentne decyzje za odczytywanie ustawień organizacji i użytkownika dla nazwy użytkownika, wprowadzone na stronie logowania. Jest to krok w przyszłości bez hasła, umożliwiająca dodatkowych poświadczeń, takich jak FIDO w wersji 2.0.

## <a name="home-realm-discovery-behavior"></a>Zachowanie odnajdowania obszaru macierzystego

W przeszłości odnajdowania obszaru macierzystego był prawu domeny, który znajduje się podczas logowania lub przy użyciu zasad odnajdowania obszaru macierzystego dla niektórych starszych aplikacji. Na przykład w stare zachowanie odnajdywania użytkownika usługi Azure Active Directory mogą błędnie swoją nazwę użytkownika ale nadal są odbierane na ekranie zbioru poświadczeń używanych w organizacji. Dzieje się tak, gdy użytkownik udostępnia poprawnie organizacji nazwy domeny "contoso.com". To zachowanie nie zezwala na stopień szczegółowości do dostosowywania środowiska dla poszczególnych użytkowników.

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
  
## <a name="additional-info"></a>Dodatkowe informacje

Oprócz środowisko ulepszone logowania użytkowników ta zmiana obejmuje mechanizmy, które mogą pomóc rozwiązać nadużycie wyliczenie username na dużą skalę.

Ta zmiana początkowo jest przeznaczona dla domen zarządzanych i rozpocznie się wdrażanie w maja 2019, ale nie uruchamia się wprowadzane domen federacyjnych do końca 2019 r. Dokładne daty wdrożenie w przypadku domen federacyjnych zależy od opinii klientów.

> [!IMPORTANT]
> Ta funkcja będzie mieć znaczący wpływ na domen federacyjnych, opierając się na starym poziomu domeny odnajdowania obszaru macierzystego do Federacji force. Niektóre organizacje mają skonfigurowanych pod kątem pracownikom zalogować się przy użyciu nazwy użytkownika, która nie istnieje w usłudze Azure Active Directory, ale zawiera nazwy domeny, ponieważ nazwy domen kieruje użytkowników aktualnie do endpoint domeny w swojej organizacji. Nowe zachowanie logowania nie zezwala na to. Użytkownik jest powiadamiany, aby rozwiązać nazwę użytkownika i nie mogą zalogować się przy użyciu nazwy użytkownika, który nie istnieje w usłudze Azure Active Directory.
>
> Jeśli Ty lub Twoja organizacja ma rozwiązania, które są zależne od starsze zachowanie, jest ważne dla administratorów organizacji, aby zaktualizować dokumentację logowania i uwierzytelniania pracowników i szkolenie pracowników, zaloguj się przy użyciu swoją nazwę użytkownika usługi Azure Active Directory.
  
Jeśli masz problemy z zachowaniem nowe, pozostaw uwagi w **opinii** dalszej części tego artykułu.  

## <a name="next-steps"></a>Kolejne kroki

[Dostosowywanie logowania znakowanie](../fundamentals/add-custom-domain.md)