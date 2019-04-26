---
title: Azure MFA usługę lub serwer, w środowisku lokalnym lub w chmurze? — Usługa azure Active Directory
description: Jako Administrator usługi AD Azure, należy zrozumieć, która wersja usługi MFA należy wdrożyć?
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: a099fa8d223643e5b339d35c0ff5cf7a5589049c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60415576"
---
# <a name="which-version-of-azure-mfa-is-right-for-my-organization"></a>Która wersja usługi Azure MFA jest odpowiednia dla mojej organizacji?

Przed podjęciem decyzji, gdzie i w jaki sposób wdrożyć usługi Multi-Factor Authentication (MFA), należy odpowiedzieć na trzy podstawowe pytania.

* [Co chcesz zabezpieczyć?](#what-am-i-trying-to-secure)
* [Gdzie znajdują się użytkownicy?](#where-are-the-users-located)
* [Jakich funkcji potrzebujesz?](#what-features-do-i-need)

Każdy z poniższych sekcji zawiera szczegółowe informacje ułatwiające uzyskaniu odpowiedzi na pytania poprzedniego.

## <a name="what-am-i-trying-to-secure"></a>Co chcesz zabezpieczyć?

Aby określić odpowiednie rozwiązanie weryfikacji dwuetapowej, najpierw musisz odpowiedzieć na pytanie o co ma zostać objęte ochroną przy użyciu dodatkowego składnika uwierzytelniania. Czy jest to aplikacja na platformie Azure? Czy może system z dostępem zdalnym? Po określeniu, co chcesz zabezpieczyć, pozwala odpowiedzieć na pytanie, w których konieczne jest włączenie uwierzytelniania wieloskładnikowego.

| Co chcesz zabezpieczyć | Usługa MFA w chmurze | Serwer MFA |
| --- |:---:|:---:|
| Aplikacje firmy Microsoft |● |● |
| Aplikacje SaaS w galerii aplikacji |● |  |
| Aplikacje internetowe opublikowane za pośrednictwem serwera proxy aplikacji usługi Azure AD |● |  |
| Aplikacje usług IIS, które nie zostały opublikowane za pośrednictwem serwera proxy aplikacji usługi Azure AD | |● |
| Dostęp zdalny, np. sieć VPN-brama usług pulpitu zdalnego za pomocą rozszerzenia serwera NPS lub istniejącego serwera NPS | ● | ● |

## <a name="where-are-the-users-located"></a>Gdzie znajdują się użytkownicy

Następnie należy określić, gdzie użytkownicy w organizacji znajdują się pomaga ustalić, jakie rozwiązanie będzie odpowiednie do użycia w chmurze lub lokalnie przy użyciu serwera MFA.

| Lokalizacja użytkowników | Usługa MFA w chmurze | Serwer MFA |
| --- |:---:|:---:|
| Usługa Azure Active Directory |● | |
| Usługa Azure AD i lokalna usługa AD przy użyciu federacji z usługami AD FS |● |● |
| Usługa Azure AD i lokalnej usługi AD za pomocą usługi Azure AD Connect — nie synchronizacji skrótów haseł lub uwierzytelniania przekazywanego |● |● |
| Usługa Azure AD i lokalnej usługi AD za pomocą usługi Azure AD Connect — hasła wyznaczania wartości skrótu synchronizacji lub uwierzytelniania przekazywanego |● | |
| Lokalna usługa Active Directory | |● |

## <a name="what-features-do-i-need"></a>Jakich funkcji potrzebujesz?

Poniższa tabela zawiera zestawienie funkcji dostępnych w przypadku użycia usługi Multi-Factor Authentication w chmurze na serwerze Multi-Factor Authentication.

| Cecha | Usługa MFA w chmurze | Serwer MFA |
| --- |:---:|:---:|
| Powiadomienie w aplikacji mobilnej jako drugi składnik | ● | ● |
| Kod weryfikacyjny w aplikacji mobilnej jako drugi składnik | ● | ● |
| Połączenie telefoniczne jako drugi składnik | ● | ● |
| Jednokierunkowa wiadomość SMS jako drugi składnik | ● | ● |
| Tokeny sprzętowe jako drugi składnik | ● (publiczna wersja zapoznawcza) | ● |
| Hasła aplikacji dla usługi w przypadku klientów usługi Office 365, którzy nie obsługują usługi MFA | ● | |
| Kontrola administracyjna nad metodami uwierzytelniania | ● | ● |
| Tryb numeru PIN | | ● |
| Alert dotyczący wykrycia oszustwa | ● | ● |
| Raporty usługi MFA | ● | ● |
| Jednorazowe obejście | | ● |
| Niestandardowe powitania dla połączeń telefonicznych | ● | ● |
| Możliwość dostosowania identyfikacji numeru dla połączeń telefonicznych | ● | ● |
| Zaufane adresy IP | ● | ● |
| Pamiętanie uwierzytelniania MFA w przypadku zaufanych urządzeń | ● | |
| Dostęp warunkowy | ● | ● |
| Pamięć podręczna |  | ● |

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy rozumiesz różnicę między działaniem usługi Azure Multi-Factor Authentication w chmurze i serwera usługi MFA w środowisku lokalnym, nadszedł czas na skonfigurowanie usługi Azure Multi-Factor Authentication i korzystanie z niej. **Wybierz ikonę, która odpowiada Twojemu scenariuszowi**

<center>

[![Wybór dla usługi Azure MFA w chmurze](./media/concept-mfa-whichversion/cloud2.png)](howto-mfa-getstarted.md) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; [ ![Wybór  dla serwera Azure MFA w środowisku lokalnym  ](./media/concept-mfa-whichversion/server2.png)](howto-mfaserver-deploy.md) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp; </center>
