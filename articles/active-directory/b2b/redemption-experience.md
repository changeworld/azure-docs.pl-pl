---
title: Realizacja zaproszenia we współpracy B2B — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: W tym artykule opisano środowisko realizacji zaproszenia współpracy B2B usługi Azure AD dla użytkowników końcowych, w tym umowy postanowienia dotyczące prywatności.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.collection: M365-identity-device-management
ms.openlocfilehash: a80eaa134130195fce00ee6a4d68851e478c4532
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67052483"
---
# <a name="azure-active-directory-b2b-collaboration-invitation-redemption"></a>Realizacja zaproszenia współpracy w usłudze Azure Active Directory B2B

W tym artykule opisano sposób użytkowników-gości, mają dostęp do zasobów i proces zgody, jakie napotykają będzie. Jeśli wyślesz wiadomość e-mail z zaproszeniem dla gości zaproszenie zawiera łącze gościa można zrealizować uzyskanie dostępu do aplikacji lub portalu. Wiadomość e-mail z zaproszeniem są tylko jednym ze sposobów, w których goście mogą uzyskać dostęp do zasobów. Jako alternatywę można dodać gości do katalogu i zapewnić im bezpośredni link do portalu lub aplikacji, którą chcesz udostępnić. Niezależnie od metody, których używają gości jest przeprowadzany przez proces zgody po raz pierwszy. Ten proces daje pewność, że gości zgodę na postanowienia dotyczące prywatności i akceptować [warunki użytkowania](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou) zostały skonfigurowane.

Po dodaniu użytkownika-gościa do katalogu konta gościa ma zgody stan (widoczne w programie PowerShell), która jest początkowo ustawiona **PendingAcceptance**. To ustawienie pozostaje, dopóki Gość akceptuje zaproszenie i wyraża zgodę na warunki użytkowania i zasady zachowania poufności informacji. Po tym, zmiany stanu zgody na **zaakceptowano**, i nie jest już prezentowane stron wyrażania zgody dla gości.

## <a name="redemption-through-the-invitation-email"></a>Realizacji za pośrednictwem wiadomości e-mail z zaproszeniem

Po dodaniu użytkownika-gościa do katalogu, [przy użyciu witryny Azure portal](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal), Gość w procesie zostanie wysłana wiadomość e-mail z zaproszeniem. Możesz również wysłać zaproszenie w wiadomości e-mail, gdy jesteś [przy użyciu programu PowerShell](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell) dodać użytkowników-gości do katalogu. Poniżej przedstawiono opis środowiska gości, po ich łącze w wiadomości e-mail.

1. Gość otrzyma [wiadomości e-mail z zaproszeniem](https://docs.microsoft.com/azure/active-directory/b2b/invitation-email-elements) wysyłanego z **Invitations Microsoft**.
2. Wybiera gościa **wprowadzenie** w wiadomości e-mail.
3. Jeśli gościa nie ma konta usługi Azure AD, konta Microsoft (MSA) lub konto e-mail w organizacji federacyjnego, mogą się monit o utworzenie konta Microsoft (chyba że [jednorazowy kod dostępu](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode) funkcja jest włączona, które nie wymagają konta Microsoft ).
4. Gość jest przeprowadzany przez [zgody środowisko](#consent-experience-for-the-guest) opisane poniżej.

## <a name="redemption-through-a-direct-link"></a>Realizacji za pomocą linku bezpośredniego

Jako alternatywy dla wiadomości e-mail z zaproszeniem można nadać Gość bezpośredni link do aplikacji lub portalu. Najpierw należy dodać użytkownika gościa do katalogu za pomocą [witryny Azure portal](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal) lub [PowerShell](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell). Następnie można użyć dowolnego z [można dostosowywać sposoby wdrażania aplikacji dla użytkowników](https://docs.microsoft.com/azure/active-directory/manage-apps/end-user-experiences), w tym linki bezpośrednie logowanie jednokrotne. Gdy gość używa bezpośredni link zamiast wiadomości e-mail z zaproszeniem, są nadal poprowadzą Cię przez proces zgody po raz pierwszy.

> [!IMPORTANT]
> Bezpośredni link musi być specyficznym dla dzierżawy. Innymi słowy musi zawierać identyfikator dzierżawy lub zweryfikowaną domenę, dzięki czemu gościa może zostać uwierzytelniony w swojej dzierżawie, w którym znajduje się w aplikacji udostępnionej. Typowe adresu URL, takich jak https://myapps.microsoft.com nie będzie działać dla gościa, ponieważ nastąpi przekierowanie do ich dzierżawy głównej dla uwierzytelniania. Poniżej przedstawiono kilka przykładów bezpośrednie linki z kontekstem dzierżawy:
 > - Panel dostępu aplikacji: https://myapps.microsoft.com/?tenantid=&lt; identyfikator dzierżawcy&gt; 
 > - Aplikacje panelu dostępu dla zweryfikowanej domeny: https://myapps.microsoft.com/&lt; zweryfikowanej domeny&gt;
 > - Witryna Azure portal: https://portal.azure.com/&lt; identyfikator dzierżawcy&gt;
 > - Poszczególnych aplikacji: jak używać [linku bezpośredniego logowania jednokrotnego](../manage-apps/end-user-experiences.md#direct-sign-on-links)

Istnieją przypadki, gdy wiadomości e-mail z zaproszeniem są zalecane przez bezpośredni link. Jeśli te przypadki specjalne są ważne dla organizacji, firma Microsoft zaleca zaprosić użytkowników przy użyciu metod, które nadal wysyłać wiadomości e-mail z zaproszeniem:
 - Użytkownik nie ma konta usługi Azure AD, konta Microsoft lub konto e-mail w organizacji federacyjnego. Jeśli nie używasz funkcji jednorazowy kod dostępu, gość musi zrealizować wiadomości e-mail z zaproszeniem do jest przeprowadzany przez procedurę tworzenia konta Microsoft.
 - Czasami obiekt zaproszonego użytkownika nie może być adres e-mail ze względu na konflikt z obiektem kontaktu (na przykład obiekt kontaktów programu Outlook). W takim przypadku użytkownik musi kliknąć adres URL realizacji w wiadomości e-mail z zaproszeniem.
 - Użytkownik może zalogować się przy użyciu aliasu adresu e-mail, który został zaproszony. (Alias jest adres e-mail dodatkowych skojarzony z kontem e-mail). W takim przypadku użytkownik musi kliknąć adres URL realizacji w wiadomości e-mail z zaproszeniem.

## <a name="consent-experience-for-the-guest"></a>Środowisko wyrażania zgody dla gościa

Po zalogowaniu Gość dostęp do zasobów w organizacji partnerskiej po raz pierwszy masz z przewodnikiem na następujących stronach. 

1. Przeglądy gościa **Przegląd uprawnień** strona zawierająca opis zasady zachowania poufności informacji w organizacji zapraszającej. Użytkownik musi **Akceptuj** wykorzystywania ich informacji zgodnie z zasady zachowania poufności informacji organizacji zapraszającej, aby kontynuować.

   ![Zrzut ekranu przedstawiający stronę przeglądu uprawnienia](media/redemption-experience/review-permissions.png) 

   > [!NOTE]
   > Aby dowiedzieć się, jak jak Administrator dzierżawy można połączyć z zasady zachowania poufności informacji w organizacji, zobacz [porad: Dodaj informacje o prywatności w organizacji w usłudze Azure Active Directory](https://aka.ms/adprivacystatement).

2. Jeśli skonfigurowano warunki użytkowania, Gość zostanie otwarty i przeglądów warunki użytkowania i następnie wybiera **Akceptuj**. 

   ![Zrzut ekranu przedstawiający nowe warunki użytkowania](media/redemption-experience/terms-of-use-accept.png) 

   > [!NOTE]
   > Można skonfigurować, zobacz [warunki użytkowania](../governance/active-directory-tou.md) w **Zarządzaj** > **relacje w organizacji** > **warunki użytkowania**.

3. O ile nie określono inaczej, Gość zostanie przeniesiona do panelu dostępu do aplikacji, która zawiera listę aplikacji, do których dostęp gościa.

   ![Zrzut ekranu pokazujący panel dostępu aplikacji](media/redemption-experience/myapps.png) 

W swoim katalogu, Gość **zaproszenie zostało zaakceptowane** zmiany wartości na **tak**. Jeśli utworzono konto MSA, Gość **źródła** pokazuje **Account Microsoft**. Aby uzyskać więcej informacji na temat właściwości konta użytkownika gościa, zobacz [właściwości użytkownika współpracy B2B usługi Azure AD](user-properties.md). 

## <a name="next-steps"></a>Kolejne kroki

- [Czym jest współpraca B2B w usłudze Azure AD?](what-is-b2b.md)
- [Dodają użytkowników we współpracy B2B usługi Azure Active Directory w witrynie Azure portal](add-users-administrator.md)
- [Jak pracownicy przetwarzający informacje mogą dodać użytkowników we współpracy B2B do usługi Azure Active Directory?](add-users-information-worker.md)
- [Dodają użytkowników we współpracy B2B usługi Azure Active Directory przy użyciu programu PowerShell](customize-invitation-api.md#powershell)
- [Opuścić organizację jako użytkownika-gościa](leave-the-organization.md)
