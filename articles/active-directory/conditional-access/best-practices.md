---
title: Najlepsze rozwiązania dotyczące dostępu warunkowego w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Więcej informacji na temat czynności, które należy znać i co jest to należy unikać podczas konfigurowania zasad dostępu warunkowego.
services: active-directory
keywords: dostęp warunkowy do aplikacji, dostęp warunkowy w usłudze Azure AD, zabezpieczenia dostępu do zasobów firmy, zasady dostępu warunkowego
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.subservice: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/25/2019
ms.author: joflore
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 67811e03bfa87a991b9eeb6f80ddddd87f781335
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/29/2019
ms.locfileid: "66305735"
---
# <a name="best-practices-for-conditional-access-in-azure-active-directory"></a>Najlepsze rozwiązania w zakresie dostępu warunkowego w usłudze Azure Active Directory

Za pomocą [dostępu warunkowego usługi Azure Active Directory (Azure AD)](../active-directory-conditional-access-azure-portal.md), jak autoryzowanego dostępu użytkowników można kontrolować swoje aplikacje w chmurze. Ten artykuł zawiera informacje dotyczące:

- Czynności, które należy znać 
- Co to jest należy unikać wykonywania podczas konfigurowania zasad dostępu warunkowego. 

W tym artykule założono, że korzystały pojęcia i terminologia opisane w temacie [co to jest dostęp warunkowy w usłudze Azure Active Directory?](../active-directory-conditional-access-azure-portal.md)



## <a name="whats-required-to-make-a-policy-work"></a>Co to są wymagane w celu zmiany celu zasad działają?

Podczas tworzenia nowych zasad są nie użytkowników, grup, aplikacji lub kontroli dostępu, zaznaczone.

![Aplikacje w chmurze](./media/best-practices/02.png)


Aby ułatwić zasad usługi działa, należy skonfigurować:


| Elementy           | Jak                                  | Dlaczego |
| :--            | :--                                  | :-- |
| **Aplikacje w chmurze** |Wybierz co najmniej jedną aplikację.  | Celem zasad dostępu warunkowego jest umożliwiają kontrolowanie sposobu autoryzowani użytkownicy mogą uzyskiwać dostęp do aplikacji w chmurze.|
| **Użytkownicy i grupy** | Wybierz co najmniej jednego użytkownika lub grupę, która dysponuje autoryzacją do dostępu do aplikacji w wybranej chmurze. | Zasady dostępu warunkowego, która nie ma użytkowników i grup przypisanych, nigdy nie zostanie wywołany. |
| **Kontrola dostępu** | Wybierz co najmniej jedno dostępu kontrolkę. | Jeśli warunki są spełnione, procesor zasad musi wiedzieć, co należy zrobić. |




## <a name="what-you-should-know"></a>Co należy wiedzieć



### <a name="how-are-conditional-access-policies-applied"></a>Jak są stosowane zasady dostępu warunkowego

Więcej niż jedne zasady dostępu warunkowego może stosować, gdy uzyskujesz dostęp do aplikacji w chmurze. W takim przypadku należy spełnić wszystkie zasady, które są stosowane. Na przykład jeśli jedna zasada wymaga uwierzytelniania Wieloskładnikowego, a druga zgodnego urządzenia, należy przejść przez usługę MFA i użyj zgodnego urządzenia. 

Wszystkie zasady są wymuszane w dwóch fazach:

- W **pierwszy** fazy, wszystkie zasady są oceniane i wszystkich kontroli dostępu, które nie są spełnione są zbierane. 

- W **drugi** fazy, zostanie wyświetlony monit, aby spełniać wymagania nie zostały spełnione. Jeśli jedne z zasad zablokuje dostęp, są zablokowane i nie otrzyma monitu o spełnia inne kontrolki zasad. Jeśli żadna z zasad zablokuje użytkownika pojawia się monit o spełnia inne kontrolki zasad, w następującej kolejności:

    ![Zamówienie](./media/best-practices/06.png)
    
    Zewnętrznych dostawców usługi MFA i warunki użytkowania są dalej.



### <a name="how-are-assignments-evaluated"></a>Jak są obliczane przypisania

Wszystkie przypisania są logicznie **wykonywana**. Jeśli masz więcej niż jedno przypisanie, skonfigurowane, wszystkie przypisania muszą być spełnione do wyzwolenia zasad.  

Jeśli musisz skonfigurować warunek lokalizacji, która ma zastosowanie do wszystkich połączeń z spoza sieci organizacji:

- Obejmują **wszystkie lokalizacje**
- Wyklucz **wszystkie zaufane adresy IP**


### <a name="what-to-do-if-you-are-locked-out-of-the-azure-ad-admin-portal"></a>Co należy zrobić, jeśli są zablokowane z portalu administratora usługi Azure AD?

Jeśli użytkownik z zablokowanym dostępem do portalu usługi Azure AD z powodu niepoprawnego ustawienia zasad dostępu warunkowego:

- Sprawdź jest, że nie ma innych administratorów w Twojej organizacji, które nie są jeszcze zablokowane. Administrator z uprawnieniami do witryny Azure portal można wyłączyć zasady, które ma wpływ na logowanie. 

- Jeśli żaden z administratorami w organizacji można zaktualizować zasad, należy przesłać żądanie pomocy technicznej. Pomoc techniczna firmy Microsoft można przeglądać i aktualizować zasady dostępu warunkowego, które uniemożliwiają dostęp.


### <a name="what-happens-if-you-have-policies-in-the-azure-classic-portal-and-azure-portal-configured"></a>Co się stanie, jeśli masz zasady w klasycznym portalu Azure i skonfigurowany z witryny Azure portal?  

Obie zasady są wymuszane przez usługę Azure Active Directory, a użytkownik uzyskuje dostęp tylko wtedy, gdy są spełnione wszystkie wymagania.

### <a name="what-happens-if-you-have-policies-in-the-intune-silverlight-portal-and-the-azure-portal"></a>Co się stanie, jeśli masz zasady, w portalu usługi Intune z programem Silverlight i witryny Azure portal?

Obie zasady są wymuszane przez usługę Azure Active Directory, a użytkownik uzyskuje dostęp tylko wtedy, gdy są spełnione wszystkie wymagania.

### <a name="what-happens-if-i-have-multiple-policies-for-the-same-user-configured"></a>Co się stanie, jeśli mam wiele zasad dla tego samego użytkownika skonfigurowane?  

Podczas każdego logowania usługi Azure Active Directory ocenia wszystkie zasady i gwarantuje, że spełnione są wszystkie wymagania, przed udzielony dostęp do użytkownika. Blokuj dostęp trumps wszystkich innych ustawień konfiguracji. 


### <a name="does-conditional-access-work-with-exchange-activesync"></a>Dostęp warunkowy działa z programem Exchange ActiveSync?

Tak, można użyć programu Exchange ActiveSync w zasadach dostępu warunkowego z niektórymi [ograniczenia](https://docs.microsoft.com/azure/active-directory/conditional-access/conditional-access-for-exo-and-spo#exchange-activesync). 

### <a name="how-should-you-configure-conditional-access-with-office-365-apps"></a>Jak należy skonfigurować dostęp warunkowy przy użyciu aplikacji usługi Office 365?

Ponieważ aplikacje usługi Office 365 są połączone ze sobą, zalecane jest przypisanie najczęściej używane aplikacje razem podczas tworzenia zasad.

Typowych aplikacji połączonych ze sobą obejmują Microsoft Flow, Microsoft Planner, Microsoft Teams, Office 365 Exchange Online, usługi Office 365 SharePoint Online i Office 365 w Yammerze.

Jest to ważne w przypadku zasad, które wymagają interakcji z użytkownikami, takich jak uwierzytelnianie wieloskładnikowe, gdy dostęp jest kontrolowany na początku sesji lub zadania. Jeśli nie, użytkownicy nie będą mogli wykonać niektóre zadania w aplikacji. Na przykład jeśli wymagane jest uwierzytelnianie wieloskładnikowe na urządzeniach niezarządzanych do dostępu do programu SharePoint, ale nie do poczty e-mail, użytkowników pracujących w wiadomości e-mail nie będzie mógł dołączyć plików programu SharePoint do wiadomości. Więcej informacji można znaleźć w artykule [zależności usług co to są usługi Azure Active Directory dostępu warunkowego?](service-dependencies.md).





## <a name="what-you-should-avoid-doing"></a>Co należy zrobić

Framework dostęp warunkowy zapewnia elastyczność konfigurację wspaniałe. Jednak elastyczność oznacza także, należy dokładnie przejrzeć wszystkie zasady konfiguracji przed zwolnieniem, aby uniknąć niepożądane wyniki. W tym kontekście, należy zwrócić szczególną uwagę na przydziały wpływające na zestawy, takie jak **wszyscy użytkownicy / grupy / aplikacje w chmurze**.

W danym środowisku należy unikać następujące konfiguracje:


**Dla wszystkich użytkowników wszystkie aplikacje w chmurze:**

- **Zablokuj dostęp** — ta konfiguracja zablokuje całej organizacji, które ostatecznie nie jest dobrym pomysłem.

- **Wymagają zgodnego urządzenia** — dla użytkowników, które nie zarejestrowali swoich urządzeń, ale te zasady blokują dostęp wszystkie, łącznie z dostępem do portalu usługi Intune. Jeśli jesteś administratorem bez zarejestrowanego urządzenia, ta zasada blokuje powrót do portalu Azure do zmiany zasad.

- **Wymagane było przyłączenie do domeny** — ten blok zasad dostępu również może potencjalnie zablokować dostęp dla wszystkich użytkowników w Twojej organizacji, jeśli nie masz jeszcze urządzenia przyłączone do domeny.

- **Wymagane zasady ochrony aplikacji** — ten blok zasad dostępu również może potencjalnie zablokować dostęp dla wszystkich użytkowników w Twojej organizacji, jeśli nie masz zasad usługi Intune. Jeśli jesteś administratorem bez aplikację kliencką, która ma zasady ochrony aplikacji usługi Intune, ta zasada blokuje powrót do portale, takie jak usługa Intune i Azure.

**Dla wszystkich użytkowników, wszystkie aplikacje w chmurze, wszystkie platformy urządzeń:**

- **Zablokuj dostęp** — ta konfiguracja zablokuje całej organizacji, które ostatecznie nie jest dobrym pomysłem.


## <a name="how-should-you-deploy-a-new-policy"></a>Jak możesz wdrożyć nowe zasady

Pierwszym krokiem należy ocenić przy użyciu zasad [narzędzie analizy warunkowej](what-if-tool.md).

Gdy nowe zasady są gotowe do środowiska, należy wdrożyć je w fazach:

1. Zastosuj zasady wśród małej grupy użytkowników i sprawdź, czy działa zgodnie z oczekiwaniami. 

2.  Po rozwinięciu zasady w celu uwzględnienia większej liczby użytkowników. Przejdź do wykluczenia wszystkich administratorów z zasad, aby upewnić się, że nadal mieć dostęp i można zaktualizować zasad, jeśli jest wymagane zmiany.

3. Zastosuj zasady do wszystkich użytkowników tylko wtedy, gdy jest to konieczne. 

Najlepszym rozwiązaniem jest tworzenie konta użytkownika, który jest:

- Dedykowane administrowanie zasadami 
- Wykluczone ze wszystkich zasad


## <a name="policy-migration"></a>Migracja zasad

Należy rozważyć Migrowanie zasad, które nie zostały utworzone w witrynie Azure portal ponieważ:

- Teraz można rozwiązać scenariusze, które nie może obsłużyć przed.

- Można zmniejszyć liczbę zasad, którą trzeba zarządzać konsolidując je.   

- Możesz zarządzać wszystkie zasady dostępu warunkowego w jednej centralnej lokalizacji.

- Klasyczny portal Azure został wycofany.   


Aby uzyskać więcej informacji, zobacz [Migrowanie zasad klasycznych w witrynie Azure portal](policy-migration.md).


## <a name="next-steps"></a>Kolejne kroki

Jeśli chcesz wiedzieć:

- Jak skonfigurować zasady dostępu warunkowego, zobacz [wymagają usługi MFA dla określonych aplikacji przy użyciu dostępu warunkowego usługi Azure Active Directory](app-based-mfa.md).
- Jak zaplanować zasady dostępu warunkowego, zobacz [jak zaplanować wdrożenie dostępu warunkowego w usłudze Azure Active Directory](plan-conditional-access.md).
