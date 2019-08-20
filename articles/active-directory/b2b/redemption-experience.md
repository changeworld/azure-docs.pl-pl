---
title: Realizacja zaproszeń w współpracy B2B — Azure Active Directory | Microsoft Docs
description: Opisuje środowisko realizacji zaproszeń do współpracy B2B usługi Azure AD dla użytkowników końcowych, w tym umowę na warunki zachowania poufności informacji.
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
ms.openlocfilehash: 2d32818f9e96e931f9e8c3c13554752327c5c456
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69622614"
---
# <a name="azure-active-directory-b2b-collaboration-invitation-redemption"></a>Azure Active Directory realizacji zaproszeń do współpracy B2B

W tym artykule opisano sposób, w jaki użytkownicy-Goście mogą uzyskać dostęp do Twoich zasobów i proces wyrażania zgody. Jeśli wyślesz wiadomość e-mail z zaproszeniem do gościa, zaproszenie zawiera link, który może zostać zrealizowany przez Gościa w celu uzyskania dostępu do aplikacji lub portalu. Wiadomość e-mail z zaproszeniem jest tylko jeden z sposobów, w którym Goście mogą uzyskać dostęp do zasobów. Alternatywnie możesz dodać Gości do katalogu i udostępnić im bezpośredni link do portalu lub aplikacji, którą chcesz udostępnić. Niezależnie od używanej metody Goście są przeprowadzani przez proces wyrażania zgody po raz pierwszy. Ten proces zapewnia, że Goście zgadzają się na warunki zachowania poufności i akceptują wszelkie skonfigurowane [warunki użytkowania](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou) .

Po dodaniu użytkownika-gościa do katalogu konto użytkownika-gościa ma stan zgody (widoczny w programie PowerShell) początkowo ustawiony na **PendingAcceptance**. To ustawienie pozostanie do momentu zaakceptowania zaproszenia przez gościa i zgody na zasady ochrony prywatności oraz warunki użytkowania. Następnie stan zgody zmieni się na zaakceptowane, a strony zgody nie będą już widoczne dla gościa.

## <a name="redemption-through-the-invitation-email"></a>Wykup za pośrednictwem wiadomości e-mail z zaproszeniem

Po dodaniu użytkownika-gościa do katalogu przy [użyciu Azure Portal](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal)do gościa w procesie jest wysyłana wiadomość e-mail z zaproszeniem. Możesz również wysyłać wiadomości e-mail z zaproszeniem, gdy korzystasz z [programu PowerShell](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell) , aby dodać użytkowników-Gości do katalogu. Poniżej znajduje się opis środowiska gościa, gdy korzystają z linku w wiadomości e-mail.

1. Gość otrzymuje [wiadomość e-mail](https://docs.microsoft.com/azure/active-directory/b2b/invitation-email-elements) z zaproszeniem, która jest wysyłana z **zaproszeń firmy Microsoft**.
2. Gość wybierze opcję **Rozpocznij** w wiadomości e-mail.
3. Jeśli gość nie ma konta usługi Azure AD, konta Microsoft (MSA) lub konta e-mail w organizacji federacyjnej, zostanie wyświetlony monit o utworzenie elementu MSA (chyba że funkcja [jednorazowy kod dostępu](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode) jest włączona, co nie wymaga MSA).
4. Gościa jest przeprowadzana w opisany [](#consent-experience-for-the-guest) poniżej sposób.

## <a name="redemption-through-a-direct-link"></a>Umorzenie za pośrednictwem bezpośredniego linku

Alternatywą dla wiadomości e-mail z zaproszeniem jest nadanie gościa bezpośredniego linku do aplikacji lub portalu. Najpierw musisz dodać użytkownika-gościa do katalogu za pośrednictwem [Azure Portal](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal) lub [PowerShell](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell). Następnie możesz użyć dowolnie [dostosowywalnych metod wdrażania aplikacji dla użytkowników](https://docs.microsoft.com/azure/active-directory/manage-apps/end-user-experiences), w tym bezpośrednich linków logowania. Gdy gość korzysta ze bezpośredniego linku zamiast wiadomości e-mail z zaproszeniem, nadal będzie przeprowadzany po raz pierwszy.

> [!IMPORTANT]
> Link bezpośredni musi być specyficzny dla dzierżawy. Innymi słowy, musi zawierać identyfikator dzierżawy lub zweryfikowaną domenę, aby można było uwierzytelnić gościa w dzierżawie, gdzie znajduje się aplikacja udostępniona. Wspólny adres URL, https://myapps.microsoft.com taki jak nie będzie działał dla gościa, ponieważ zostanie przekierowany do dzierżawy domowej w celu uwierzytelnienia. Oto kilka przykładów bezpośrednich linków z kontekstem dzierżawy:
 > - Panel dostępu do aplikacji https://myapps.microsoft.com/?tenantid=&lt:; Identyfikator dzierżawy&gt; 
 > - Panel dostępu do aplikacji dla zweryfikowanej domeny https://myapps.microsoft.com/&lt:; zweryfikowana domena&gt;
 > - Azure Portal: https://portal.azure.com/&lt; identyfikator dzierżawy&gt;
 > - Poszczególna aplikacja: Zobacz, jak używać [linku bezpośredniego logowania](../manage-apps/end-user-experiences.md#direct-sign-on-links)

Istnieją sytuacje, w których wiadomość e-mail z zaproszeniem zaleca się za pośrednictwem linku bezpośredniego. Jeśli te specjalne przypadki są ważne dla organizacji, zalecamy Zapraszanie użytkowników przy użyciu metod, które nadal wysyłają wiadomość e-mail z zaproszeniem:
 - Użytkownik nie ma konta usługi Azure AD, elementu MSA ani konta e-mail w organizacji federacyjnej. Jeśli nie korzystasz z funkcji jednorazowego kodu dostępu, gość musi zrealizować wiadomość e-mail z zaproszeniem, aby zapoznać się z instrukcjami tworzenia konta MSA.
 - Czasami obiekt zaproszony użytkownik może nie mieć adresu e-mail z powodu konfliktu z obiektem Contact (na przykład obiektem Contact programu Outlook). W takim przypadku użytkownik musi kliknąć adres URL wykupu w wiadomości e-mail z zaproszeniem.
 - Użytkownik może zalogować się przy użyciu aliasu zaproszonego adresu e-mail. (Alias to dodatkowy adres e-mail skojarzony z kontem e-mail). W takim przypadku użytkownik musi kliknąć adres URL wykupu w wiadomości e-mail z zaproszeniem.

## <a name="consent-experience-for-the-guest"></a>Środowisko zgody gościa

Gdy gość loguje się w celu uzyskania dostępu do zasobów w organizacji partnerskiej po raz pierwszy, są one przekierowane przez następujące strony. 

1. Gość przegląda stronę **przeglądanie uprawnień** z opisem zasad zachowania poufności informacji w organizacji. Użytkownik musi **zaakceptować** użycie informacji zgodnie z zasadami zachowania poufności w organizacji zapraszania, aby kontynuować.

   ![Zrzut ekranu przedstawiający stronę przegląd uprawnień](media/redemption-experience/review-permissions.png) 

   > [!NOTE]
   > Aby uzyskać informacje o tym, jak Administrator dzierżawy może połączyć się z zasadami zachowania poufności informacji [w organizacji, zobacz How to: Dodaj informacje o ochronie prywatności organizacji w](https://aka.ms/adprivacystatement)Azure Active Directory.

2. W przypadku skonfigurowania warunków użytkowania gość otwiera i przegląda warunki użytkowania, a następnie wybiera pozycję **Zaakceptuj**. 

   ![Zrzut ekranu przedstawiający nowe warunki użytkowania](media/redemption-experience/terms-of-use-accept.png) 

   > [!NOTE]
   > Można skonfigurować [warunki użytkowania](../governance/active-directory-tou.md) w temacie **Zarządzanie** > **relacjami** > organizacyjnymi**warunki użytkowania**.

3. O ile nie określono inaczej, Gość zostanie przekierowany do panelu dostępu do aplikacji, który zawiera listę aplikacji, do których gość może uzyskać dostęp.

   ![Zrzut ekranu przedstawiający panel dostępu aplikacje](media/redemption-experience/myapps.png) 

W katalogu wartość zaakceptowana przez **zaproszenie** gościa zmieni się na **tak**. Jeśli element MSA został utworzony, **Źródło** gościa pokazuje **konto Microsoft**. Aby uzyskać więcej informacji na temat właściwości konta użytkownika-gościa, zobacz [właściwości użytkownika współpracy B2B usługi Azure AD](user-properties.md). 

## <a name="next-steps"></a>Następne kroki

- [Czym jest współpraca B2B w usłudze Azure AD?](what-is-b2b.md)
- [Dodają użytkowników we współpracy B2B usługi Azure Active Directory w witrynie Azure portal](add-users-administrator.md)
- [Jak Pracownicy przetwarzający informacje mogą dodawać do Azure Active Directory użytkowników współpracy B2B?](add-users-information-worker.md)
- [Dodawanie Azure Active Directory użytkowników współpracy B2B przy użyciu programu PowerShell](customize-invitation-api.md#powershell)
- [Opuść organizację jako użytkownika-gościa](leave-the-organization.md)
