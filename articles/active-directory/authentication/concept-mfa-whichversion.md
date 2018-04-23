---
title: Wybieranie między usługą Azure MFA w chmurze i na serwerze | Microsoft Docs
description: Wybierz odpowiednie rozwiązanie zabezpieczające oparte na uwierzytelnianiu wieloskładnikowym, uwzględniając lokalizację użytkowników i zasoby, które chcesz zabezpieczyć.  Następnie wybierz chmurę lub serwer usługi MFA albo usługi AD FS.
services: multi-factor-authentication
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: ec2270ea-13d7-4ebc-8a00-fa75ce6c746d
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/02/2017
ms.author: joflore
ms.reviewer: richagi
ms.openlocfilehash: 002e473f66ea861d9257e308aaafacd420095108
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="choose-the-azure-multi-factor-authentication-solution-for-you"></a>Wybieranie odpowiedniego rozwiązania usługi Azure Multi-Factor Authentication
Istnieje kilka typów usługi Azure Multi-Factor Authentication (MFA), dlatego należy udzielić odpowiedzi na kilka pytań, aby wybrać właściwą wersję tej usługi.  Oto te pytania:

* [Co chcesz zabezpieczyć?](#what-am-i-trying-to-secure)
* [Gdzie znajdują się użytkownicy?](#where-are-the-users-located)
* [Jakich funkcji potrzebujesz?](#what-features-do-i-need)

Wskazówki zawarte w poniższych sekcjach ułatwią znalezienie odpowiedzi na te pytania.

## <a name="what-am-i-trying-to-secure"></a>Co chcesz zabezpieczyć?
Aby wybrać odpowiednie rozwiązanie weryfikacji dwuetapowej, najpierw należy określić, co ma zostać objęte ochroną przy użyciu drugiej metody uwierzytelniania.  Czy jest to aplikacja na platformie Azure?  Czy może system z dostępem zdalnym?  Po określeniu elementów, które mają zostać zabezpieczone, będzie można znaleźć odpowiedź na pytanie o miejsce włączenia usługi Multi-Factor Authentication.  

| Co chcesz zabezpieczyć | Usługa MFA w chmurze | Serwer MFA |
| --- |:---:|:---:|
| Aplikacje firmy Microsoft |● |● |
| Aplikacje SaaS w galerii aplikacji |● |  |
| Aplikacje sieci Web opublikowane za pośrednictwem serwera proxy aplikacji usługi Azure AD |● |  |
| Aplikacje usług IIS, które nie zostały opublikowane za pośrednictwem serwera proxy aplikacji usługi Azure AD | |● |
| Dostęp zdalny, np. sieć VPN lub brama usług pulpitu zdalnego | ● | ● |

## <a name="where-are-the-users-located"></a>Gdzie znajdują się użytkownicy
Następnie w zależności od tego, gdzie znajdują się użytkownicy, można określić, jakie rozwiązanie będzie odpowiednie — uwierzytelnianie w chmurze czy uwierzytelnianie lokalne przy użyciu serwera MFA.

| Lokalizacja użytkowników | Usługa MFA w chmurze | Serwer MFA |
| --- |:---:|:---:|
| Usługa Azure Active Directory |● | |
| Usługa Azure AD i lokalna usługa AD przy użyciu federacji z usługami AD FS |● |● |
| Usługa Azure AD i lokalna usługa AD używana z narzędziem DirSync, Azure AD Sync, Azure AD Connect — brak synchronizacji haseł |● |● |
| Usługa Azure AD i lokalna usługa AD używana z narzędziem DirSync, Azure AD Sync, Azure AD Connect — z synchronizacją haseł |● | |
| Lokalna usługa Active Directory | |● |

## <a name="what-features-do-i-need"></a>Jakich funkcji potrzebujesz?
Poniższa tabela zawiera zestawienie funkcji dostępnych w przypadku użycia usługi Multi-Factor Authentication w chmurze na serwerze Multi-Factor Authentication.

| Cecha | Usługa MFA w chmurze | Serwer MFA |
| --- |:---:|:---:|
| Powiadomienie w aplikacji mobilnej jako drugi składnik | ● | ● |
| Kod weryfikacyjny w aplikacji mobilnej jako drugi składnik | ● | ● |
| Połączenie telefoniczne jako drugi składnik | ● | ● |
| Jednokierunkowa wiadomość SMS jako drugi składnik | ● | ● |
| Dwukierunkowa wiadomość SMS jako drugi składnik | | ●  (Przestarzałe)| 
| Tokeny sprzętowe jako drugi składnik | | ● |
| Hasła aplikacji dla usługi w przypadku klientów usługi Office 365, którzy nie obsługują usługi MFA | ● | |
| Kontrola administracyjna nad metodami uwierzytelniania | ● | ● |
| Tryb numeru PIN | | ● |
| Alert dotyczący wykrycia oszustwa |● | ● |
| Raporty usługi MFA |● | ● |
| Jednorazowe obejście | | ● |
| Niestandardowe powitania dla połączeń telefonicznych | ● | ● |
| Możliwość dostosowania identyfikacji numeru dla połączeń telefonicznych | ● | ● |
| Zaufane adresy IP | ● | ● |
| Pamiętanie uwierzytelniania MFA w przypadku zaufanych urządzeń | ● | |
| Dostęp warunkowy | ● | ● |
| Pamięć podręczna |  | ● |

## <a name="next-steps"></a>Następne kroki

Teraz, gdy rozumiesz różnicę między działaniem usługi Azure Multi-Factor Authentication w chmurze i serwera usługi MFA w środowisku lokalnym, nadszedł czas na skonfigurowanie usługi Azure Multi-Factor Authentication i korzystanie z niej. **Wybierz ikonę, która odpowiada Twojemu scenariuszowi**

<center>

[![Usługa MFA w chmurze](./media/concept-mfa-whichversion/cloud2.png)](howto-mfa-getstarted.md)  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[![Serwer usługi MFA](./media/concept-mfa-whichversion/server2.png)](../../multi-factor-authentication/multi-factor-authentication-get-started-server.md) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp; </center>
