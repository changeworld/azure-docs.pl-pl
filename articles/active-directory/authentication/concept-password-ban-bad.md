---
title: Hasła dynamicznie zakazane w usłudze Azure AD
description: Zablokuj słabe hasła ze środowiska za pomocą haseł usługi Azure AD, dynamicznie zakazane
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: rogoya
ms.openlocfilehash: 4c5fead0a7f4634a8f5ee005114d24cae9a2590f
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/01/2018
ms.locfileid: "50739828"
---
# <a name="eliminate-bad-passwords-in-your-organization"></a>Eliminowanie nieprawidłowych haseł w organizacji

|     |
| --- |
| Ochrony hasłem w usłudze Azure AD i listy niestandardowej zakazanych haseł są publicznej wersji zapoznawczej funkcji usługi Azure Active Directory. Aby uzyskać więcej informacji na temat wersji zapoznawczych, zobacz [dodatkowym warunkom użytkowania wersji zapoznawczych usług Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Liderów branży zorientować się, nie należy używać tego samego hasła w wielu miejscach złożoną i nie wprowadzać proste, np. / Password123. Jak organizacje gwarantuje, że postępujesz zgodnie z użytkownikom wskazówki Jak one upewnij się, że użytkownicy nie są przy użyciu wspólnego hasła lub haseł, które są znane mają zostać uwzględnione w ostatnich naruszeń danych?

## <a name="global-banned-password-list"></a>Listy globalne zakazanych haseł

Firma Microsoft nieustannie pracuje, by pozostawać o krok przed przestępcami. W związku z tym zespół usługi Azure AD Identity Protection nieustannie szukać haseł często używane i których bezpieczeństwo zostało naruszone. Następnie blokują tych haseł, które zostaną uznane za zbyt często używany w co nosi nazwę listy globalne zakazanych haseł. Cyberprzestępcy również stosują podobne strategie w ich ataków, w związku z tym Microsoft nie publikuje zawartość tej listy publicznie. Te narażone hasła są blokowane, zanim staną się prawdziwym zagrożeniem dla klientów firmy Microsoft. Aby uzyskać więcej informacji na temat bieżącego działaniach związanych z zabezpieczeniami, zobacz [Microsoft Security Intelligence Report](https://www.microsoft.com/security/intelligence-report).

## <a name="preview-custom-banned-password-list"></a>Wersja zapoznawcza: Niestandardowe zakazane liście haseł

Niektóre organizacje mogą chcieć wykonać zabezpieczeń jeden krok dalej przez dodanie dostosowań na górze listy globalne zakazanych haseł co Microsoft wywołuje listy niestandardowej zakazanych haseł. Klientów korporacyjnych, takich jak Contoso następnie można zdecydować się na zablokowanie warianty ich nazwy na marki, warunki specyficzny dla firmy lub innych elementów.

Niestandardowy zablokowane lista hasła oraz możliwość włączenia Active Directory w środowisku lokalnym, integracja jest zarządzane przy użyciu witryny Azure portal.

![Modyfikowanie listy niestandardowej zakazanych haseł w ramach metod uwierzytelniania w witrynie Azure portal](./media/concept-password-ban-bad/authentication-methods-password-protection.png)

## <a name="on-premises-hybrid-scenarios"></a>Scenariusze hybrydowe w środowisku lokalnym

Ochrona konta tylko w chmurze jest pomocne, ale w wielu organizacjach Obsługa scenariuszy hybrydowych, w tym usługi Active Directory lokalnego systemu Windows Server. Istnieje możliwość zainstalowania ochrona za pomocą hasła usługi Azure AD dla usługi Windows Server Active Directory (wersja zapoznawcza) agentów lokalnego rozszerzenie listy zakazanych haseł w istniejącej infrastrukturze. Teraz użytkownicy i Administratorzy, którzy zmienić, ustawianie lub resetowanie haseł w środowisku lokalnym są wymagane do wykonania tych samych zasad haseł jako użytkowników tylko w chmurze.

## <a name="how-does-the-banned-password-list-work"></a>Jak działa listy zakazanych haseł

Listy zakazanych haseł dopasowuje haseł na liście przez przekonwertowanie ciągu na małe litery i porównywanie do znanego zakazanych haseł w odległości edycji 1 za pomocą dopasowywania rozmytego.

Przykład: Hasło word jest zablokowana dla organizacji
   - Użytkownik próbuje ustawić hasła "P@ssword" który jest konwertowany na "password", a ponieważ wariant hasło będzie zablokowany.
   - Administrator próbuje ustawić użytkownicy hasło "/ Password123!" które konwertowane na "/ password123!" a ponieważ wariant hasła jest zablokowany.

Każde użytkownik resetuje lub zmienia swoje hasło usługi Azure AD, który przepływa ona przez ten proces, aby upewnić się, że nie jest na listy zakazanych haseł. To sprawdzenie znajduje się w hybrydowe resetowania scenariuszy przy użyciu haseł, synchronizacji skrótów haseł i uwierzytelniania przekazywanego.

## <a name="license-requirements"></a>Wymagania licencyjne

|   | Usługa Azure AD ochrona za pomocą hasła przy użyciu listy globalne zakazanych haseł | Usługa Azure AD ochrona za pomocą hasła przy użyciu listy niestandardowej zakazanych haseł|
| --- | --- | --- |
| Użytkownicy tylko w chmurze | Usługa Azure AD — warstwa Bezpłatna | Usługa Azure AD — warstwa Podstawowa |
| Użytkownicy są synchronizowani z lokalnego systemu Windows Server Active Directory | Usługa Azure AD Premium P1 lub P2 | Usługa Azure AD Premium P1 lub P2 |

Dodatkowe informacje o licencjonowaniu, wraz z kosztami, można znaleźć na [usługi Azure Active Directory ceny witryny](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="what-do-users-see"></a>Co widzą użytkownicy

Gdy użytkownik próbuje zresetować hasło do zasobu, który będzie zablokowany, zobaczą następujący komunikat o błędzie:

Niestety Twoje hasło zawiera słowo, frazę lub wzorzec, który sprawia, że hasło jest łatwe do odgadnięcia. Spróbuj ponownie, używając innego hasła.

## <a name="next-steps"></a>Kolejne kroki

* [Skonfiguruj listę niestandardowych zakazanych haseł](howto-password-ban-bad.md)
* [Włączanie usługi Azure AD hasło ochrony agentów w środowisku lokalnym](howto-password-ban-bad-on-premises-deploy.md)
