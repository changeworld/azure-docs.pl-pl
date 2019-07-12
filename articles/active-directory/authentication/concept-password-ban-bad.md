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
ms.openlocfilehash: c043b2ed1a626e362d7edd1a83429aa14046f8ac
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67703057"
---
# <a name="eliminate-bad-passwords-in-your-organization"></a>Eliminowanie nieprawidłowych haseł w organizacji

Liderów branży zorientować się, nie należy używać tego samego hasła w wielu miejscach złożoną i nie wprowadzać proste, np. "/ Password123". Jak organizacje gwarantuje, że użytkownikom postępujesz zgodnie z najlepszych wskazówek Jak one upewnij się, że użytkownicy nie są z słabe hasła lub nawet zmian na słabe hasła?

Pierwszym krokiem w silniejszych haseł jest zapewnienie wskazówek dla użytkowników. Firmy Microsoft Bieżące wskazówki na ten temat można znaleźć pod następujący link:

[Wskazówki dotyczące hasła Microsoft](https://www.microsoft.com/research/publication/password-guidance)

Dobrą wskazówkę jest ważne, ale nawet z Zdajemy sobie sprawę, że wielu użytkowników będzie nadal znajdą się wybierając słabe hasła. Ochrona za pomocą usługi Azure AD hasła chroni Twojej organizacji, wykrywanie i blokowanie znane słabe hasła i ich warianty, a także opcjonalnie blokuje dodatkowe postanowienia słabe, które są specyficzne dla Twojej organizacji.

Aby uzyskać więcej informacji na temat bieżącego działaniach związanych z zabezpieczeniami, zobacz [Microsoft Security Intelligence Report](https://www.microsoft.com/security/operations/security-intelligence-report).

## <a name="global-banned-password-list"></a>Listy globalne zakazanych haseł

Zespół usługi Azure AD Identity Protection nieustannie analizuje dane telemetryczne z zabezpieczeń usługi Azure AD, dane dla często używanych haseł słabych lub ze złamanymi zabezpieczeniami lub dokładniej, słabym podstawowa warunki, które są często używane jako podstawa dla słabe hasła. Podczas tych postanowień słabe zostaną znalezione, są one dodane do listy globalnej zakazanych haseł. Zawartość listy globalne zakazanych haseł, nie są oparte na żadne zewnętrzne źródło danych. Listy globalne zakazanych haseł opiera się wyłącznie na bieżące wyniki telemetrii zabezpieczeń usługi Azure AD i analizy.

Zawsze, gdy nowe hasło jest zmienione lub zresetować dla dowolnego użytkownika w dowolnej dzierżawy w usłudze Azure AD, bieżąca wersja listy globalne zakazanych haseł jest używany jako klucz, w danych wejściowych podczas sprawdzania siły hasła. Tej weryfikacji powoduje dużo silniejszego hasła dla wszystkich klientów usługi Azure AD.

> [!NOTE]
> Cyberprzestępcy również często stosują podobne strategie w ich ataków. W związku z tym Microsoft nie publikuje publicznie zawartość tej listy.

## <a name="custom-banned-password-list"></a>Niestandardowe zakazane liście haseł

Niektóre organizacje mogą zdecydować zwiększyć bezpieczeństwo jeszcze bardziej poprzez dodawanie dostosowań na górze listy globalne zakazanych haseł w nazywanej przez firmę Microsoft listy niestandardowej zakazanych haseł. Firma Microsoft zaleca się, że warunki dodana do tej listy przede wszystkim koncentrują się na warunki specyficzne dla organizacji takich jak:

- Nazw marek
- Nazwy produktu
- (Na przykład, na przykład w siedzibie firmy)
- Warunki wewnętrzny specyficzny dla firmy
- Skróty, które mają określoną firmą, co oznacza.

Gdy warunki są dodawane do listy niestandardowej zakazanych haseł, ich zostanie dodany do listy globalnej zakazanych haseł podczas sprawdzania poprawności hasła.

> [!NOTE]
> Listy niestandardowe zakazanych haseł jest ograniczone do mającą co najwyżej 1000 warunków. Nie jest przeznaczona do blokowania bardzo duże listy haseł. Aby w pełni wykorzystać zalety listy niestandardowej zakazanych haseł, firma Microsoft zaleca, aby najpierw przejrzeć i zrozumieć algorytm oceny hasło (zobacz [jak są hasła obliczane](concept-password-ban-bad.md#how-are-passwords-evaluated)) przed dodaniem nowych warunków do Niestandardowa lista zabronione. Zrozumienie, jak działa algorytm umożliwi w przedsiębiorstwie w celu wydajnego wykrywać i blokować dużą liczbę słabe hasła i ich warianty.

Na przykład: należy wziąć pod uwagę klienta o nazwie "Contoso", która opiera się w Londynie, i sprawia to, że produkt o nazwie "Widżet". Dla takich klientów będzie marnotrawstwa, a także mniej bezpieczna opcja próby takich jak blokowanie określonych odmiany tych terminów:

- "Firma Contoso! 1"
- "Contoso@London"
- "ContosoWidget"
- "! Contoso"
- "LondonHQ"
- ...etcetera

Zamiast tego jest znacznie bardziej efektywne i bezpieczne blokowania kluczy podstawowych warunków:

- "Contoso"
- "Londyn"
- "Widżet"

Algorytm sprawdzania poprawności hasła następnie będzie automatycznie blokować słabe warianty oraz kombinacje powyższych.

Niestandardowy zablokowane lista hasła oraz możliwość włączenia Active Directory w środowisku lokalnym, integracja jest zarządzane przy użyciu witryny Azure portal.

![Modyfikowanie listy niestandardowe zakazanych haseł, w ramach metod uwierzytelniania](./media/concept-password-ban-bad/authentication-methods-password-protection.png)

## <a name="password-spray-attacks-and-third-party-compromised-password-lists"></a>Hasło osłony ataków i listy innych firm, których bezpieczeństwo zostało naruszone haseł

Jeden klucz korzyści ochrony haseł usługi Azure AD jest ułatwią Ci chronić przed atakami osłony hasła. Większość ataków osłony hasła, nie należy próbować ataków dowolnego danego konta poszczególnych więcej niż kilka razy, ponieważ takie zachowanie znacznie zwiększa prawdopodobieństwo wykrycia, za pośrednictwem blokady konta lub w inny sposób. Do większości ataków osłony hasła w związku z tym polegają na przesyłanie tylko niewielka liczba znanych haseł najsłabsza w odniesieniu do każdego z kont w przedsiębiorstwie. Ta technika umożliwia osobie atakującej na szybko wyszukać łatwo zagrożonych konta, przy jednoczesnym uniknięcie potencjalnych progi wykrywania.

Ochrony hasłem w usłudze Azure AD zaprojektowano w celu wydajnego zablokować wszystkie znane słabe hasła, które mogą być używane w osłony złamania hasła, w oparciu o dane telemetryczne zabezpieczeń w rzeczywistych warunkach widzianych przez usługę Azure AD.  Firma Microsoft zapoznała się z witryn sieci Web innych firm, który wylicza milionów haseł, których bezpieczeństwo zostało naruszone w poprzednim naruszenia zabezpieczeń znany. To częsty problem w produkty sprawdzania poprawności hasła innych firm pod kątem opierać się na atak metodą siłową porównanie z milionów hasła. Microsoft czuje, że techniki takie nie są najlepszym sposobem, aby zwiększyć siłę hasła ogólną typowe strategie używana przez osoby atakujące osłony hasło podane.

> [!NOTE]
> Microsoft, listy globalne zakazanych haseł nie jest oparte na żadnych danych innych firm źródeł, łącznie z listy haseł ze złamanymi zabezpieczeniami.

Mimo że globalnej listy zakazanych firmy Microsoft jest mały w porównaniu ze niektóre listy zbiorcze innych firm, to fakt, że pochodzi z rzeczywistych zabezpieczeń telemetrii na ataki osłony rzeczywiste hasło, a także fakt powiększane są jego wpływ na bezpieczeństwo, firmy Microsoft algorytm sprawdzania poprawności hasła używa inteligentnych technik dopasowywania rozmytego. Wynik końcowy to, że go będzie efektywnie wykrywać i blokować milionów najbardziej typowe słabe hasła z użycia w przedsiębiorstwie. Klienci logujący się dodać terminów specyficznych dla organizacji do listy niestandardowej zakazanych haseł, która jest również korzystać z tego samego algorytmu.

## <a name="on-premises-hybrid-scenarios"></a>Scenariusze hybrydowe w środowisku lokalnym

Ochrona konta tylko w chmurze jest pomocne, ale w wielu organizacjach Obsługa scenariuszy hybrydowych, w tym usługi Active Directory lokalnego systemu Windows Server. Istnieje możliwość zainstalowania ochrona za pomocą hasła usługi Azure AD dla usługi Active Directory systemu Windows Server agentów lokalnego rozszerzenie listy zakazanych haseł w istniejącej infrastrukturze. Teraz użytkownicy i Administratorzy, którzy zmienić, ustawianie lub resetowanie haseł w środowisku lokalnym są wymagane do wykonania tych samych zasad haseł jako użytkowników tylko w chmurze.

## <a name="how-are-passwords-evaluated"></a>Sposób oceniania hasła

Zawsze, gdy użytkownik zmienia lub resetuje hasła, nowe hasło jest sprawdzane pod kątem sile i złożoność przez weryfikację przed globalnej i liście niestandardowych zakazanych haseł (jeśli są one skonfigurowane).

Nawet wtedy, gdy hasło użytkownika zawiera zakazanych haseł, hasło nadal mogą zostać zaakceptowane, jeżeli ogólną hasła jest wystarczająco silne. Nowo skonfigurowana hasło będzie przejście przez następujące kroki, aby ocenić jej ogólną siły, aby określić, jeśli powinien zostać zaakceptowane lub odrzucone.

### <a name="step-1-normalization"></a>Krok 1: Normalizacja

Nowe hasło najpierw przechodzi przez proces normalizacji. Ta technika umożliwia niewielki zestaw zakazanych haseł można zamapować na znacznie większy zbiór potencjalnie słabe hasła.

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

Każdego z powyższych haseł jest specjalnie niezgodny zakazanych haseł "abcdef". Jednak ponieważ każdy przykład odbywa się w odległości edycji 1 zakazanych termin "abcdef", zostały one wszystkie uznane za dopasowanie zakończyło się "abcdef".

#### <a name="substring-matching-on-specific-terms"></a>Podciąg dopasowania (na warunkach określonych)

Dopasowywanie podciągów jest używana na znormalizowaną hasła sprawdź imienia użytkownika i nazwisko oraz nazwę dzierżawy (Zwróć uwagę, że dopasowywanie nazw dzierżawy odbywa się podczas sprawdzania poprawności hasła na kontrolerze domeny usługi Active Directory).

Przykład: Załóżmy, że mamy użytkownika, Pol, kto chce, aby zresetować swoje hasło, aby "P0l123fb". Po normalizacji to hasło będzie stają się "pol123fb". Dopasowywanie podciągów wykryje, że hasło zawiera imię użytkownika "Pol". Mimo że "P0l123fb" nie jest specjalnie na obu listy zakazanych haseł, dopasowywanie podciągów znaleziono "Pol" w haśle. W związku z tym to hasło będzie odrzucane.

#### <a name="score-calculation"></a>Wynik obliczania

Następnym krokiem jest, aby zidentyfikować wszystkie wystąpienia elementu zakazanych haseł w znormalizowanych nowe hasło użytkownika. Następnie:

1. Każdy zakazanych haseł, która znajduje się w haśle użytkownika znajduje się jeden punkt.
2. Każdy pozostały znak unikatowy znajduje się jeden punkt.
3. Hasło musi zawierać co najmniej pięciu (5) punkty go do zaakceptowania.

W dwóch następnych przykładach Załóżmy że Contoso używa ochrony haseł usługi Azure AD i ich listy niestandardowej ma "contoso". Załóżmy również, że "puste" znajduje się w globalnej listy.

Przykład: użytkownik zmieni hasło "C0ntos0Blank12"

Po normalizacji to hasło, staje się "contosoblank12". Proces dopasowywania wykryje, że to hasło zawiera dwa hasła zabronione: contoso i puste. To hasło następnie oblicza wynik:

[contoso] + [puste] + [1] + [2] = 4 punkty, ponieważ jest to hasło w obszarze pięciu (5) punkty, jego zostanie odrzucone.

Przykład: hasła w celu zmiany przez użytkownika "ContoS0Bl@nkf9!".

Po normalizacji to hasło, staje się "contosoblankf9!". Proces dopasowywania wykryje, że to hasło zawiera dwa hasła zabronione: contoso i puste. To hasło następnie oblicza wynik:

[contoso] + [puste] + [f] + [9] + [!] = 5 punktów to hasło jest co najmniej pięciu (5) punkty, jego akceptacją.

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

- [Skonfiguruj listę niestandardowych zakazanych haseł](howto-password-ban-bad.md)
- [Włączanie usługi Azure AD hasło ochrony agentów w środowisku lokalnym](howto-password-ban-bad-on-premises-deploy.md)
