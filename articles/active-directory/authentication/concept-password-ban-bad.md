---
title: Dynamicznie zakazane haseł w usłudze Azure AD
description: Zakaz słabe hasła ze środowiska przy użyciu usługi Azure AD dynamicznie zakazane haseł
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 06/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: rogoya
ms.openlocfilehash: 89cbe386d87c6ccb81df7fabd86b197bb69e41e1
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2018
ms.locfileid: "36295610"
---
# <a name="eliminate-bad-passwords-in-your-organization"></a>Eliminowanie nieprawidłowych haseł w Twojej organizacji

|     |
| --- |
| Ochrony hasłem w usłudze Azure AD i listy zabronionych hasła niestandardowego są funkcje publicznej wersji zapoznawczej usługi Azure Active Directory. Aby uzyskać więcej informacji na temat wersji zapoznawczych, zobacz [uzupełniające warunki użytkowania dotyczącym wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Liderów branży informujące, nie należy używać tego samego hasła w wielu miejscach złożoną i nie wprowadzać proste jak Password123. Jak organizacje gwarantuje, że użytkowników są następujące wskazówki Jak one upewnij się, że użytkownicy nie są za pomocą wspólnego hasła lub haseł, które są znane do uwzględnienia w ostatnich naruszeń danych?

## <a name="global-banned-password-list"></a>Listy globalne zabronione hasła

Aby pozostać w jednym kroku wcześniejsze przez przestępców zawsze działa firmy Microsoft. W związku z tym zespół usługi Azure AD Identity Protection stale szukać haseł powszechnie używane i którego bezpieczeństwo zostało naruszone. Następnie blokują te hasła, które zostaną uznane za zbyt często używane w tzw listy globalnego hasła zabronione. Przez przestępców również podobne strategie w ich ataków, w związku z tym Microsoft nie publikuje zawartość tej listy publicznie. Te narażone hasła są zablokowane, zanim staną się prawdziwe zagrożenia klientom firmy Microsoft. Aby uzyskać więcej informacji na temat bieżącego działania zabezpieczeń, zobacz [raportów analizy zabezpieczeń firmy Microsoft](https://www.microsoft.com/security/intelligence-report).

## <a name="preview-custom-banned-password-list"></a>Podgląd: Niestandardowy zakazane liście haseł

W niektórych organizacjach może być podjęcie kolejny krok zabezpieczeń co przez dodanie dostosowania na szczycie listy globalnego hasła zabronione co Microsoft wywołuje listy zabronionych hasła niestandardowego. Klienci korporacyjni jak Contoso następnie można zdecydować się na zablokowanie wariantów swoje firmowe, warunki specyficzne dla firmy lub innych elementów.

Niestandardowa niedozwolone liście haseł i możliwość włączenia lokalnego Active Directory integration odbywa się przy użyciu portalu Azure.

![Zmodyfikuj listę niestandardowych zabronione hasło w obszarze metod uwierzytelniania w portalu Azure](./media/concept-password-ban-bad/authentication-methods-password-protection.png)

## <a name="on-premises-hybrid-scenarios"></a>Scenariuszy hybrydowych lokalnej

Ochrona kont tylko w chmurze jest przydatne, ale w wielu organizacjach Obsługa scenariuszy hybrydowych, łącznie z lokalnego systemu Windows Server Active Directory. Istnieje możliwość zainstalowania ochrony hasłem usługi Azure AD dla systemu Windows Server Active Directory (wersja zapoznawcza) agentów lokalnego rozszerzania listy zabronionych hasła w istniejącej infrastrukturze. Teraz użytkownicy i Administratorzy, którzy zmienić, ustawiania lub resetowania haseł lokalnych są wymagane do wykonania tego samego hasła jako tylko do chmury.

## <a name="how-does-the-banned-password-list-work"></a>Jak działa listy zabronionych hasło

Lista zabronione hasła odpowiada hasła na liście przez konwertowanie ciągu na małe litery i porównywania znane haseł zabronione w edycji odległości 1 z dopasowywania rozmytego.

Przykład: Zablokowane hasła programu word w organizacji.
   - Użytkownik próbuje ustawić hasła "P@ssword" który jest konwertowany na "password" i ponieważ jest on wariant hasła jest zablokowana.
   - Administrator podejmuje próbę ustawienia hasła użytkowników "Password123!" przekonwertować "password123!" i ponieważ jest on wariant hasła jest zablokowany.

Każde użytkownik resetuje lub zmieni swoje hasło usługi Azure AD, który przelewa się przez ten proces, aby upewnić się, że nie jest na liście haseł zabronione. Ten test jest dołączony do hybrydowego scenariuszy przy użyciu hasła samodzielnego resetowania, synchronizacji skrótów haseł i uwierzytelnianie przekazywane.

## <a name="license-requirements"></a>Wymagania licencyjne

Korzyści wynikające z listy globalnego hasła zabronione mają zastosowanie do wszystkich użytkowników usługi Azure Active Directory (Azure AD).

Na liście hasła niestandardowego zabronione wymaga licencji Azure AD podstawowa.

Azure AD ochrony hasłem dla systemu Windows Server Active Directory wymaga licencji usługi Azure AD Premium. 

Dodatkowe informacje licencyjne, wraz z kosztami, można znaleźć w [usługi Azure Active Directory cennik lokacji](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="what-do-users-see"></a>Co widzą użytkownicy

Jeśli użytkownik próbuje zresetować hasło do zasobu, który będzie zakazane, zobaczy następujący komunikat o błędzie:

Niestety hasło zawiera słowo, wyrażenie lub wzorca, który umożliwia łatwe do odgadnięcia hasła. Spróbuj ponownie, używając innego hasła.

## <a name="next-steps"></a>Kolejne kroki

* [Konfigurowanie listy zabronionych hasła niestandardowego](howto-password-ban-bad.md)
* [Włączanie usługi Azure AD hasła ochrony agentów lokalnie](howto-password-ban-bad-on-premises.md)
