---
title: Dynamicznie zakazane haseł — Azure Active Directory
description: Zablokuj słabe hasła ze środowiska za pomocą haseł usługi Azure AD, dynamicznie zakazane
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: cc2c125df2e3455b0e90919dbca92fe497a4b1b7
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "58884350"
---
# <a name="eliminate-bad-passwords-in-your-organization"></a>Eliminowanie nieprawidłowych haseł w organizacji

Liderów branży zorientować się, nie należy używać tego samego hasła w wielu miejscach złożoną i nie wprowadzać proste, np. / Password123. Jak organizacje gwarantuje, że postępujesz zgodnie z użytkownikom wskazówki Jak one upewnij się, że użytkownicy nie są przy użyciu wspólnego hasła lub haseł, które są znane mają zostać uwzględnione w ostatnich naruszeń danych?

## <a name="global-banned-password-list"></a>Listy globalne zakazanych haseł

Firma Microsoft nieustannie pracuje, by pozostawać o krok przed przestępcami. W związku z tym zespół usługi Azure AD Identity Protection nieustannie szukać haseł często używane i których bezpieczeństwo zostało naruszone. Następnie blokują tych haseł, które zostaną uznane za zbyt często używany w co nosi nazwę listy globalne zakazanych haseł. Cyberprzestępcy również stosują podobne strategie w ich ataków, w związku z tym Microsoft nie publikuje zawartość tej listy publicznie. Te narażone hasła są blokowane, zanim staną się prawdziwym zagrożeniem dla klientów firmy Microsoft. Aby uzyskać więcej informacji na temat bieżącego działaniach związanych z zabezpieczeniami, zobacz [Microsoft Security Intelligence Report](https://www.microsoft.com/security/operations/security-intelligence-report).

## <a name="custom-banned-password-list"></a>Lista zakazanych haseł niestandardowych

Niektóre organizacje mogą chcieć wykonać zabezpieczeń jeden krok dalej przez dodanie dostosowań na górze listy globalne zakazanych haseł co Microsoft wywołuje listy niestandardowej zakazanych haseł. Klientów korporacyjnych, takich jak Contoso następnie można zdecydować się na zablokowanie warianty ich nazwy na marki, warunki specyficzny dla firmy lub innych elementów.

Niestandardowy zablokowane lista hasła oraz możliwość włączenia Active Directory w środowisku lokalnym, integracja jest zarządzane przy użyciu witryny Azure portal.

![Modyfikowanie listy niestandardowe zakazanych haseł, w ramach metod uwierzytelniania](./media/concept-password-ban-bad/authentication-methods-password-protection.png)

## <a name="on-premises-hybrid-scenarios"></a>Scenariusze hybrydowe w środowisku lokalnym

Ochrona konta tylko w chmurze jest pomocne, ale w wielu organizacjach Obsługa scenariuszy hybrydowych, w tym usługi Active Directory lokalnego systemu Windows Server. Istnieje możliwość zainstalowania ochrona za pomocą hasła usługi Azure AD dla usługi Active Directory systemu Windows Server agentów lokalnego rozszerzenie listy zakazanych haseł w istniejącej infrastrukturze. Teraz użytkownicy i Administratorzy, którzy zmienić, ustawianie lub resetowanie haseł w środowisku lokalnym są wymagane do wykonania tych samych zasad haseł jako użytkowników tylko w chmurze.

## <a name="how-are-passwords-evaluated"></a>Sposób oceniania hasła

Zawsze, gdy użytkownik zmienia lub resetuje hasła, nowe hasło jest sprawdzane pod kątem sile i złożoność przez weryfikację przed globalnej i liście niestandardowych zakazanych haseł (jeśli są one skonfigurowane).

Nawet wtedy, gdy hasło użytkownika zawiera zakazanych haseł, hasło nadal mogą zostać zaakceptowane, jeżeli ogólną hasła jest wystarczająco silne. Nowo skonfigurowana hasło będzie przejście przez następujące kroki, aby ocenić jej ogólną siły, aby określić, jeśli powinien zostać zaakceptowane lub odrzucone.

### <a name="step-1-normalization"></a>Krok 1: Normalizacja

Nowe hasło najpierw przechodzi przez proces normalizacji. Dzięki temu w małej grupie zakazanych haseł mają być mapowane do znacznie większy zbiór potencjalnie słabe hasła.

Normalizacji ma dwie części.  Nie zostaną zmienione po pierwsze, wielkie litery na małe litery.  Drugi, typowe znak podstawienia są wykonywane na przykład:  

| Oryginalny list  | Podstawione list |
| --- | --- |
| '0'  | ' o' |
| '1'  | 'l' |
| '$'  | 's' |
| '\@'  | 'a' |

Przykład: Załóżmy, że hasło "puste" jest zablokowane, a użytkownik próbuje zmienić hasła w celu "Bl@nK". Mimo że "Bl@nk" to nie jest zablokowany, proces normalizacji konwertuje to hasło "blank", czyli zakazanych haseł.

### <a name="step-2-check-if-password-is-considered-banned"></a>Krok 2: Sprawdź, czy hasło jest traktowane jako zablokowane

#### <a name="fuzzy-matching-behavior"></a>Zachowanie dopasowywania rozmytego

Dopasowywania rozmytego jest używany na znormalizowaną hasło Aby ustalić, czy zawiera ono hasło w obu globalne lub niestandardowej zakazane listy haseł. Proces dopasowywania opiera się na odległość edycji porównania jednego (1).  

Przykład: Załóżmy, że hasło "abcdef" jest niedozwolone, a użytkownik podejmuje próbę zmiany hasła do jednej z następujących czynności:

"abcdeg"    *(ostatnie znak zmieniła się z "f", "g")* "abcdefg"   *"(g" dołączonych do końca)* "abcde"     *(końcowe "f" zostało usunięte z zakończenia)*

Każdego z powyższych haseł jest specjalnie niezgodny zakazanych haseł "abcdef". Jednak ponieważ każdy przykład odbywa się w odległości edycji 1 zakazanych tokenu "abcdef", zostały one wszystkie uznane za dopasowanie zakończyło się "abcdef".

#### <a name="substring-matching-on-specific-terms"></a>Podciąg dopasowania (na warunkach określonych)

Dopasowywanie podciągów jest używana na znormalizowaną hasła sprawdź imienia użytkownika i nazwisko oraz nazwę dzierżawy (Zwróć uwagę, że dopasowywanie nazw dzierżawy odbywa się podczas sprawdzania poprawności hasła na kontrolerze domeny usługi Active Directory).

Przykład: Załóżmy, że mamy użytkownika John Doe, który chce, aby zresetować swoje hasło, aby "J0hn123fb". Po normalizacji to hasło będzie stają się "john123fb". Dopasowywanie podciągów wykryje, że hasło zawiera imię użytkownika "John". Mimo że "J0hn123fb" nie jest specjalnie na obu listy zakazanych haseł, dopasowywanie podciągów znaleziono "John" w haśle. W związku z tym to hasło będzie odrzucane.

#### <a name="score-calculation"></a>Wynik obliczania

Następnym krokiem jest, aby zidentyfikować wszystkie wystąpienia elementu zakazanych haseł w znormalizowanych nowe hasło użytkownika. Następnie:

1. Każdy zakazanych haseł, która znajduje się w haśle użytkownika znajduje się jeden punkt.
2. Każdy pozostały znak unikatowy znajduje się jeden punkt.
3. Hasło musi zawierać co najmniej 5 punktów dla niego do zaakceptowania.

W dwóch następnych przykładach Załóżmy że Contoso używa ochrony haseł usługi Azure AD i ich listy niestandardowej ma "contoso". Załóżmy również, że "puste" znajduje się w globalnej listy.

Przykład: użytkownik zmieni hasło "C0ntos0Blank12"

Po normalizacji to hasło, staje się "contosoblank12". Proces dopasowywania wykryje, że to hasło zawiera dwa hasła zabronione: contoso i puste. To hasło następnie oblicza wynik:

[contoso] + [puste] + [1] + [2] = 4 punkty, ponieważ jest to hasło w obszarze 5 punktów, jego zostanie odrzucone.

Przykład: hasła w celu zmiany przez użytkownika "ContoS0Bl@nkf9!".

Po normalizacji to hasło, staje się "contosoblankf9!". Proces dopasowywania wykryje, że to hasło zawiera dwa hasła zabronione: contoso i puste. To hasło następnie oblicza wynik:

[contoso] + [puste] + [f] + [9] + [!] = 5 punktów to hasło jest co najmniej 5 punktów, jego akceptacją.

   > [!IMPORTANT]
   > Należy pamiętać, że algorytm zakazanych haseł, wraz z globalnej listy można i zmienić w dowolnym momencie na platformie Azure na podstawie analizy bezpieczeństwa i badań. Dla usługi agenta kontrolera domeny w środowisku lokalnym algorytmy zaktualizowany zostanie aktywowane dopiero po ponownym zainstalowaniu oprogramowanie agenta kontrolera domeny.

## <a name="license-requirements"></a>Wymagania licencyjne

|   | Usługa Azure AD ochrona za pomocą hasła przy użyciu listy globalne zakazanych haseł | Usługa Azure AD ochrona za pomocą hasła przy użyciu listy niestandardowej zakazanych haseł|
| --- | --- | --- |
| Użytkownicy tylko w chmurze | Usługa Azure AD — warstwa Bezpłatna | Usługa Azure AD Premium P1 lub P2 |
| Użytkownicy są synchronizowani z lokalnego systemu Windows Server Active Directory | Usługa Azure AD Premium P1 lub P2 | Usługa Azure AD Premium P1 lub P2 |

> [!NOTE]
> Lokalnych użytkowników usługi Active Directory systemu Windows Server, które nie są zsynchronizowane z usługą Azure Active Directory również skorzystać z zalet oparte na istniejącej licencji dla użytkowników zsynchronizowanych ochrona za pomocą hasła usługi Azure AD.

Dodatkowe informacje o licencjonowaniu, wraz z kosztami, można znaleźć na [usługi Azure Active Directory ceny witryny](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="what-do-users-see"></a>Co widzą użytkownicy

Gdy użytkownik próbuje zresetować hasło do zasobu, który będzie zablokowany, zobaczą następujący komunikat o błędzie:

Niestety Twoje hasło zawiera słowo, frazę lub wzorzec, który sprawia, że hasło jest łatwe do odgadnięcia. Spróbuj ponownie, używając innego hasła.

## <a name="next-steps"></a>Kolejne kroki

* [Skonfiguruj listę niestandardowych zakazanych haseł](howto-password-ban-bad.md)
* [Włączanie usługi Azure AD hasło ochrony agentów w środowisku lokalnym](howto-password-ban-bad-on-premises-deploy.md)
