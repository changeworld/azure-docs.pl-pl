---
title: Realizacja zaproszenia we współpracy b2b — usługa Azure AD
description: W tym artykule opisano środowisko realizacji zaproszenia do współpracy usługi Azure AD B2B dla użytkowników końcowych, w tym umowę dotyczącą warunków prywatności.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 043e0f3a0ff2c1c642c63a387c571b575f77cf7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050837"
---
# <a name="azure-active-directory-b2b-collaboration-invitation-redemption"></a>Realizacja zaproszenia do współpracy usługi Azure Active Directory B2B

W tym artykule opisano sposoby, w jakie użytkownicy-goście mogą uzyskiwać dostęp do zasobów i proces wyrażania zgody, który napotkają. Jeśli wyślesz wiadomość e-mail z zaproszeniem do gościa, zaproszenie zawiera link, który gość może zrealizować, aby uzyskać dostęp do aplikacji lub portalu. Wiadomość e-mail z zaproszeniem to tylko jeden ze sposobów, w jaki goście mogą uzyskać dostęp do Twoich zasobów. Alternatywnie możesz dodać gości do katalogu i nadać im bezpośredni link do portalu lub aplikacji, którą chcesz udostępnić. Niezależnie od używanej metody, goście są prowadzeni przez proces zgody po raz pierwszy. Proces ten gwarantuje, że Twoi goście akceptują warunki prywatności i akceptują wszelkie [skonfigurowane warunki użytkowania.](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou)

Po dodaniu użytkownika-gościa do katalogu, konto użytkownika-gościa ma stan zgody (widoczny w programie PowerShell), który jest początkowo ustawiony na **PendingAcceptance**. To ustawienie pozostaje do momentu, gdy gość zaakceptuje Zaproszenie i zaakceptuje twoją politykę prywatności i warunki użytkowania. Następnie stan zgody zmienia się na **Zaakceptowane,** a strony zgody nie są już prezentowane gościowi.

   > [!IMPORTANT]
   > **Począwszy od 31 marca 2021**r. firma Microsoft nie będzie już obsługiwać realizacji zaproszeń, tworząc niezarządzane konta usługi Azure AD i dzierżawy scenariuszy współpracy B2B. W ramach przygotowań zachęcamy klientów do wyrażenia zgody na [jednorazowe uwierzytelnianie kodem dostępu pocztą e-mail.](one-time-passcode.md) Cieszymy się z waszych opinii na temat tej publicznej funkcji podglądu i cieszymy się, że możemy stworzyć jeszcze więcej sposobów współpracy.

## <a name="redemption-through-the-invitation-email"></a>Realizacja za pośrednictwem wiadomości e-mail z zaproszeniem

Po dodaniu użytkownika-gościa do katalogu [przy użyciu witryny Azure portal,](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal)wiadomość e-mail z zaproszeniem jest wysyłana do gościa w procesie. Możesz również wysłać wiadomości e-mail z zaproszeniem, gdy [używasz programu PowerShell](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell) do dodawania użytkowników-gości do katalogu. Oto opis doświadczenia gościa, gdy zrealizuje link w wiadomości e-mail.

1. Gość otrzymuje [wiadomość e-mail](https://docs.microsoft.com/azure/active-directory/b2b/invitation-email-elements) z zaproszeniem, która jest wysyłana z **zaproszeń firmy Microsoft**.
2. Gość wybiera **wprowadzenie w** wiadomości e-mail.
3. Jeśli gość nie ma konta usługi Azure AD, konta Microsoft (MSA) lub konta e-mail w organizacji federacyjnej, zostanie poproszony o utworzenie usługi MSA (chyba że funkcja [jednorazowego kodu](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode) jest włączona, co nie wymaga msa).
4. Gość jest prowadzony przez [doświadczenie zgody](#consent-experience-for-the-guest) opisane poniżej.

## <a name="redemption-through-a-direct-link"></a>Odkupienie za pośrednictwem bezpośredniego łącza

Alternatywą dla wiadomości e-mail z zaproszeniem można nadać gościowi bezpośredni link do aplikacji lub portalu. Najpierw należy dodać użytkownika-gościa do katalogu za pośrednictwem [witryny Azure portal](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal) lub [programu PowerShell.](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell) Następnie można użyć dowolnego z [konfigurowalnych sposobów wdrażania aplikacji](https://docs.microsoft.com/azure/active-directory/manage-apps/end-user-experiences)dla użytkowników, w tym bezpośrednich linków logowania. Gdy gość użyje bezpośredniego linku zamiast wiadomości e-mail z zaproszeniem, nadal będzie prowadzony przez środowisko zgody po raz pierwszy.

> [!IMPORTANT]
> Bezpośrednie łącze musi być specyficzne dla dzierżawy. Innymi słowy musi zawierać identyfikator dzierżawy lub zweryfikowaną domenę, aby gość mógł być uwierzytelniany w dzierżawie, w której znajduje się udostępniona aplikacja. Wspólny adres https://myapps.microsoft.com URL, taki jak nie będzie działać dla gościa, ponieważ będzie przekierować do ich dzierżawy domowej do uwierzytelniania. Oto kilka przykładów bezpośrednich powiązań z kontekstem dzierżawy:
 > - Panel dostępu https://myapps.microsoft.com/?tenantid=&ltdo aplikacji: ;identyfikator dzierżawy&gt; 
 > - Panel dostępu do aplikacji dla https://myapps.microsoft.com/&ltzweryfikowanej domeny: ;zweryfikowana domena&gt;
 > - Witryna https://portal.azure.com/&ltAzure portal: ;identyfikator dzierżawy&gt;
 > - Indywidualna aplikacja: zobacz, jak korzystać z [bezpośredniego linku logowania](../manage-apps/end-user-experiences.md#direct-sign-on-links)

Istnieją przypadki, w których wiadomość e-mail z zaproszeniem jest zalecana za pośrednictwem bezpośredniego łącza. Jeśli te specjalne przypadki są ważne dla Twojej organizacji, zalecamy zaproszenie użytkowników przy użyciu metod, które nadal wysyłają wiadomość e-mail z zaproszeniem:
 - Użytkownik nie ma konta usługi Azure AD, msa ani konta e-mail w organizacji federacyjnej. Jeśli nie używasz funkcji jednorazowego kodu dostępu, gość musi zrealizować wiadomość e-mail z zaproszeniem, aby przejść przez kroki tworzenia usługi MSA.
 - Czasami obiekt zaproszonego użytkownika może nie mieć adresu e-mail z powodu konfliktu z obiektem kontaktu (na przykład obiektem kontaktu programu Outlook). W takim przypadku użytkownik musi kliknąć adres URL realizacji w wiadomości e-mail z zaproszeniem.
 - Użytkownik może zalogować się przy za pomocą aliasu adresu e-mail, który został zaproszony. (Alias to dodatkowy adres e-mail skojarzony z kontem e-mail). W takim przypadku użytkownik musi kliknąć adres URL realizacji w wiadomości e-mail z zaproszeniem.

## <a name="consent-experience-for-the-guest"></a>Zgoda dla gościa

Gdy gość loguje się, aby uzyskać dostęp do zasobów w organizacji partnerskiej po raz pierwszy, jest prowadzony przez następujące strony. 

1. Gość przegląda stronę **Uprawnienia recenzji** opisującą zasady zachowania poufności informacji organizacji zapraszającej. Aby kontynuować, użytkownik musi **zaakceptować** korzystanie z jego informacji zgodnie z zasadami ochrony prywatności organizacji zapraszającej.

   ![Zrzut ekranu przedstawiający stronę Uprawnienia recenzji](media/redemption-experience/review-permissions.png) 

   > [!NOTE]
   > Aby uzyskać informacje o tym, jak jako administrator dzierżawy możesz połączyć się z zasadami zachowania poufności informacji organizacji, zobacz [Instrukcje: Dodawanie informacji o ochronie prywatności organizacji w usłudze Azure Active Directory](https://aka.ms/adprivacystatement).

2. Jeśli warunki użytkowania są skonfigurowane, gość otwiera i przegląda warunki użytkowania, a następnie wybiera **opcję Zaakceptuj**. 

   ![Zrzut ekranu przedstawiający nowe warunki użytkowania](media/redemption-experience/terms-of-use-accept.png) 

   > [!NOTE]
   > W obszarze **Zarządzanie** > **relacjami organizacyjnymi** > **można**skonfigurować warunki [użytkowania](../governance/active-directory-tou.md) .

3. O ile nie określono inaczej, gość jest przekierowywał do panelu dostęp do aplikacji, który zawiera listę aplikacji, do których gość może uzyskać dostęp.

   ![Zrzut ekranu przedstawiający panel Dostęp do aplikacji](media/redemption-experience/myapps.png) 

W katalogu zaproszenie gościa **zaakceptowane** wartość zmienia się na **Tak**. Jeśli utworzono msa, **źródło** gościa pokazuje **konto Microsoft**. Aby uzyskać więcej informacji na temat właściwości konta użytkownika gościa, zobacz [Właściwości użytkownika współpracy usługi Azure AD B2B](user-properties.md). 

## <a name="next-steps"></a>Następne kroki

- [Czym jest współpraca B2B w usłudze Azure AD?](what-is-b2b.md)
- [Dodawanie użytkowników współpracy usługi Azure Active Directory B2B w witrynie Azure portal](add-users-administrator.md)
- [W jaki sposób pracownicy informacji dodają użytkowników współpracy B2B do usługi Azure Active Directory?](add-users-information-worker.md)
- [Dodawanie użytkowników współpracy usługi Azure Active Directory B2B przy użyciu programu PowerShell](customize-invitation-api.md#powershell)
- [Opuszczanie organizacji jako użytkownika-gościa](leave-the-organization.md)
