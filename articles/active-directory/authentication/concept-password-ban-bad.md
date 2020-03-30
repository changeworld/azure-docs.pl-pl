---
title: Dynamicznie zakazane hasła — usługa Azure Active Directory
description: Zamów słabe hasła w swoim środowisku za pomocą haseł z blokadą usługi Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: ef127d120b32f5344bce0f68d79f48401087f0ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264000"
---
# <a name="eliminate-bad-passwords-in-your-organization"></a>Eliminate bad passwords in your organization (Eliminowanie nieprawidłowych haseł w organizacji)

Liderzy branży mówią, aby nie używać tego samego hasła w wielu miejscach, aby uczynić je złożonym i nie uczynić go prostym jak "Password123". W jaki sposób organizacje mogą zagwarantować, że ich użytkownicy przestrzegają najlepszych praktyk? Jak mogą upewnić się, że użytkownicy nie używają słabych haseł, a nawet wariacji na temat słabych haseł?

Pierwszym krokiem w posiadaniu silniejszych haseł jest zapewnienie wskazówek dla użytkowników. Aktualne wskazówki firmy Microsoft dotyczące tego tematu można znaleźć pod następującym linkiem:

[Wskazówki dotyczące haseł firmy Microsoft](https://www.microsoft.com/research/publication/password-guidance)

Dobre wskazówki są ważne, ale nawet z tym wiemy, że wielu użytkowników nadal będzie wybierać słabe hasła. Usługa Azure AD Password Protection chroni organizację, wykrywając i blokując znane słabe hasła i ich warianty, a także opcjonalnie blokując dodatkowe słabe terminy specyficzne dla twojej organizacji.

Aby uzyskać więcej informacji na temat bieżących działań w zakresie zabezpieczeń, zobacz [Raport analizy zabezpieczeń firmy Microsoft](https://www.microsoft.com/security/operations/security-intelligence-report).

## <a name="global-banned-password-list"></a>Globalna lista zakazanych haseł

Zespół usługi Azure AD Identity Protection stale analizuje dane telemetryczne zabezpieczeń usługi Azure AD w poszukiwaniu często używanych słabych lub złamanych haseł, a dokładniej słabych terminów podstawowych, które często są używane jako podstawa słabych haseł. Po znalezieniu takich słabych terminów są one dodawane do globalnej listy zakazanych haseł. Zawartość globalnej listy zakazanych haseł nie jest oparta na żadnym zewnętrznym źródle danych. Globalna lista zakazanych haseł jest w całości oparta na bieżących wynikach telemetrii i analizy zabezpieczeń usługi Azure AD.

Za każdym razem, gdy nowe hasło zostanie zmienione lub zresetowane dla dowolnego użytkownika w dowolnej dzierżawie w usłudze Azure AD, bieżąca wersja globalnej listy zakazanych haseł jest używana jako klucz wprowadzania danych podczas sprawdzania siły hasła. Ta weryfikacja powoduje znacznie silniejsze hasła dla wszystkich klientów usługi Azure AD.

> [!NOTE]
> Cyberprzestępcy również używają podobnych strategii w swoich atakach. W związku z tym firma Microsoft nie publikuje publicznie zawartości tej listy.

## <a name="custom-banned-password-list"></a>Niestandardowa lista zablokowanych haseł

Niektóre organizacje mogą chcieć jeszcze bardziej poprawić bezpieczeństwo, dodając własne dostosowania na globalnej liście zakazanych haseł w tym, co firma Microsoft nazywa niestandardową listę zakazanych haseł. Firma Microsoft zaleca, aby terminy dodane do tej listy koncentrowały się przede wszystkim na terminach specyficznych dla organizacji, takich jak:

- Marki
- Nazwy produktów
- Lokalizacje (na przykład siedziba firmy)
- Warunki wewnętrzne specyficzne dla firmy
- Skróty, które mają określone znaczenie firmy.

Po dodaniu terminów do niestandardowej listy zakazanych haseł zostaną one połączone z terminami na globalnej liście zakazanych haseł podczas sprawdzania haseł.

> [!NOTE]
> Niestandardowa lista zakazanych haseł jest ograniczona do posiadania maksymalnie 1000 terminów. Nie jest przeznaczony do blokowania bardzo dużych list haseł. Aby w pełni wykorzystać zalety niestandardowej listy zakazanych haseł, firma Microsoft zaleca, aby najpierw przejrzeć i zrozumieć algorytm oceny hasła (zobacz [Jak są oceniane hasła)](concept-password-ban-bad.md#how-are-passwords-evaluated)przed dodaniem nowych terminów do niestandardowej listy zakazanych haseł. Zrozumienie działania algorytmu umożliwi przedsiębiorstwu efektywne wykrywanie i blokowanie dużej liczby słabych haseł i ich wariantów.

Na przykład: należy wziąć pod uwagę klienta o nazwie "Contoso", który ma siedzibę w Londynie i który sprawia, że produkt o nazwie "Widget". Dla takiego klienta, byłoby marnotrawstwa, jak również mniej bezpieczne, aby spróbować zablokować konkretne odmiany tych terminów, takich jak:

- "Contoso!1"
- "Contoso@London"
- "ContosoWidget"
- "! Contoso"
- "LondonHQ"
- ... Etcetera

Zamiast tego jest znacznie bardziej wydajne i bezpieczne, aby zablokować tylko kluczowe warunki podstawowe:

- "Contoso"
- "Londyn"
- "Widget"

Algorytm sprawdzania poprawności hasła automatycznie zablokuje słabe warianty i kombinacje powyższych.

Niestandardowa lista zablokowanych haseł i możliwość włączania lokalnej integracji usługi Active Directory jest zarządzana za pomocą witryny Azure portal.

![Modyfikowanie niestandardowej listy zakazanych haseł w obszarze Metody uwierzytelniania](./media/concept-password-ban-bad/authentication-methods-password-protection.png)

## <a name="password-spray-attacks-and-third-party-compromised-password-lists"></a>Ataki natryskiwanie hasła i listy haseł, których bezpieczeństwo zostało naruszone przez osoby trzecie

Jedną z kluczowych korzyści ochrony hasłem usługi Azure AD jest pomoc w obronie przed atakami sprayu hasła. Większość ataków natryski hasłem nie próbuje atakować danego konta więcej niż kilka razy, ponieważ takie zachowanie znacznie zwiększa prawdopodobieństwo wykrycia, za pomocą blokady konta lub w inny sposób. Większość ataków na spray hasłem polega zatem na przesłaniu tylko niewielkiej liczby znanych najsłabszych haseł do każdego konta w przedsiębiorstwie. Ta technika umożliwia osobie atakującej szybkie wyszukiwanie konta, które łatwo się narazić na szwank, przy jednoczesnym unikaniu potencjalnych progów wykrywania.

Ochrona hasłem usługi Azure AD została zaprojektowana w celu efektywnego blokowania wszystkich znanych słabych haseł, które mogą być używane w atakach natryskowe haseł, na podstawie rzeczywistych danych telemetrycznych zabezpieczeń, jak widać w usłudze Azure AD.  Firma Microsoft jest świadoma witryn sieci Web innych firm, które wyliczają miliony haseł, które zostały naruszone w przypadku poprzednich publicznie znanych naruszeń zabezpieczeń. Często produkty sprawdzania poprawności haseł innych firm mają być oparte na porównaniu siłowych z tymi milionami haseł. Firma Microsoft uważa, że takie techniki nie są najlepszym sposobem na poprawę ogólnej siły hasła, biorąc pod uwagę typowe strategie używane przez osoby atakujące w sprayu hasłem.

> [!NOTE]
> Globalna lista zablokowanych haseł firmy Microsoft nie jest w ogóle oparta na źródłach danych innych firm, w tym na listach haseł, których bezpieczeństwo zostało naruszone.

Chociaż globalna lista zakazanych microsoftów jest niewielka w porównaniu do niektórych list zbiorczych innych firm, jej efekty bezpieczeństwa są wzmacniane przez fakt, że pochodzi z rzeczywistych danych telemetrycznych bezpieczeństwa na temat rzeczywistych ataków natryskowe hasła, a także fakt, że Microsoft algorytm sprawdzania poprawności hasła wykorzystuje inteligentne techniki dopasowywania rozmytego. Efektem końcowym jest to, że będzie skutecznie wykrywać i blokować miliony najczęstszych słabych haseł używanych w przedsiębiorstwie. Klienci, którzy zdecydują się dodać terminy specyficzne dla organizacji do niestandardowej listy zakazanych haseł, również korzystają z tego samego algorytmu.

Dodatkowe informacje na temat problemów z bezpieczeństwem opartych na hasłach mogą zostać sprawdzone na [stronie Your Pa$$word nie ma znaczenia.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984)

## <a name="on-premises-hybrid-scenarios"></a>Lokalne scenariusze hybrydowe

Ochrona kont tylko w chmurze jest przydatna, ale wiele organizacji utrzymuje scenariusze hybrydowe, w tym lokalną usługę Windows Server Active Directory. Korzyści związane z zabezpieczeniami ochrony hasłem usługi Azure AD można również rozszerzyć na środowisko usługi Active Directory systemu Windows Server za pośrednictwem instalacji agentów lokalnych. Teraz użytkownicy i administratorzy, którzy zmieniają lub resetują hasła w usłudze Active Directory, muszą przestrzegać tych samych zasad haseł, co użytkownicy w chmurze.

## <a name="how-are-passwords-evaluated"></a>Jak oceniane są hasła

Za każdym razem, gdy użytkownik zmienia lub resetuje swoje hasło, nowe hasło jest sprawdzane pod kątem siły i złożoności, sprawdzając je pod kątem łącznej listy terminów z globalnych i niestandardowych zakazanych list haseł (jeśli jest skonfigurowane).

Nawet jeśli hasło użytkownika zawiera hasło zbanowane, hasło może zostać zaakceptowane, jeśli ogólne hasło jest wystarczająco silne w przeciwnym razie. Nowo skonfigurowane hasło przejdzie przez następujące kroki, aby ocenić jego ogólną siłę, aby określić, czy należy je zaakceptować lub odrzucić.

### <a name="step-1-normalization"></a>Krok 1: Normalizacja

Nowe hasło najpierw przechodzi przez proces normalizacji. Ta technika pozwala na mały zestaw zakazanych haseł, które mają być mapowane na znacznie większy zestaw potencjalnie słabych haseł.

Normalizacja ma dwie części.  Po pierwsze, wszystkie wielkie litery są zmieniane na małe litery.  Po drugie, wykonywane są substytucje znaków wspólnych, na przykład:  

| Oryginalny list  | List podstawiony |
| --- | --- |
| '0'  | "o" |
| '1'  | "l" |
| '$'  | "s" |
| '\@'  | "a" |

Przykład: załóżmy, że hasło "puste" jest zablokowane, aBl@nKużytkownik próbuje zmienić hasło na " ". Mimo żeBl@nk" " nie jest specjalnie zakazane, proces normalizacji konwertuje to hasło na "puste", co jest hasłem zbanowane.

### <a name="step-2-check-if-password-is-considered-banned"></a>Krok 2: Sprawdź, czy hasło jest uważane za zablokowane

#### <a name="fuzzy-matching-behavior"></a>Rozmyte zachowanie dopasowywania

Dopasowywanie rozmyte jest używane na znormalizowanym haśle, aby zidentyfikować, czy zawiera hasło znajdujące się na globalnych lub niestandardowych listach zakazanych haseł. Proces dopasowywania opiera się na odległości edycji jednego (1) porównania.  

Przykład: załóżmy, że hasło "abcdef" jest zablokowane, a użytkownik próbuje zmienić swoje hasło na jedno z następujących:

'abcdeg' *(ostatni znak zmieniony z 'f' na 'g')* 'abcdefg' *'(g' do końca)* 'abcde' *(końcowe 'f' zostało usunięte z końca)*

Każde z powyższych haseł nie pasuje konkretnie do zakazanego hasła "abcdef". Jednakże, ponieważ każdy przykład znajduje się w odległości 1 zakazanego terminu "abcdef", wszystkie są uważane za dopasowanie do "abcdef".

#### <a name="substring-matching-on-specific-terms"></a>Dopasowywanie podciągów (na określonych warunkach)

Dopasowywanie podciągów jest używane na znormalizowanym haśle, aby sprawdzić imię i nazwisko użytkownika, a także nazwę dzierżawy (należy pamiętać, że dopasowywanie nazw dzierżawy nie jest wykonywane podczas sprawdzania poprawności haseł na kontrolerze domeny usługi Active Directory).

Przykład: załóżmy, że mamy użytkownika, Pol, który chce zresetować swoje hasło do "P0l123fb". Po normalizacji hasło to stałoby się "pol123fb". Dopasowanie podciągów stwierdza, że hasło zawiera imię użytkownika "Pol". Mimo że "P0l123fb" nie było specjalnie na żadnej z zakazanych list haseł, podciąg dopasowania znaleziono "Pol" w haśle. W związku z tym to hasło zostanie odrzucone.

#### <a name="score-calculation"></a>Obliczanie wyniku

Następnym krokiem jest zidentyfikowanie wszystkich wystąpień zakazanych haseł w znormalizowanym nowym haśle użytkownika. Następnie:

1. Każde zablokowane hasło, które znajduje się w haśle użytkownika, otrzymuje jeden punkt.
2. Każdy pozostały unikalny znak otrzymuje jeden punkt.
3. Hasło musi zawierać co najmniej pięć (5) punktów, aby zostało zaakceptowane.

W przypadku dwóch następnych przykładów załóżmy, że contoso używa usługi Azure AD Password Protection i ma "contoso" na swojej liście niestandardowej. Załóżmy również, że "puste" znajduje się na liście globalnej.

Przykład: użytkownik zmienia hasło na "C0ntos0Blank12"

Po normalizacji to hasło staje się "contosoblank12". Proces dopasowywania stwierdza, że to hasło zawiera dwa zablokowane hasła: contoso i puste. To hasło otrzymuje następnie wynik:

[contoso] + [puste] + [1] + [2] = 4 punkty Ponieważ to hasło ma mniej niż pięć (5) punktów, zostanie odrzucone.

Przykład: użytkownik zmienia hasłoContoS0Bl@nkf9na " !".

Po normalizacji to hasło staje się "contosoblankf9!". Proces dopasowywania stwierdza, że to hasło zawiera dwa zablokowane hasła: contoso i puste. To hasło otrzymuje następnie wynik:

[contoso] + [puste] + [f] + [9] + [!] = 5 punktów Ponieważ to hasło jest co najmniej pięć (5) punktów, jest akceptowane.

   > [!IMPORTANT]
   > Należy pamiętać, że algorytm hasła zbanowane wraz z globalnej listy można i zrobić zmiany w dowolnym momencie na platformie Azure na podstawie bieżącej analizy zabezpieczeń i badań. W przypadku lokalnej usługi agenta kontrolera domeny zaktualizowane algorytmy będą obowiązywać dopiero po ponownym zainstalowaniu oprogramowania agenta kontrolera domeny.

## <a name="license-requirements"></a>Wymagania licencyjne

|   | Ochrona hasłem usługi Azure AD z globalną listę zakazanych haseł | Ochrona hasłem usługi Azure AD z niestandardową listą zakazanych haseł|
| --- | --- | --- |
| Użytkownicy tylko w chmurze | Usługa Azure AD — warstwa Bezpłatna | Usługa Azure AD Premium P1 lub P2 |
| Użytkownicy synchronizowani z lokalnej usługi Windows Server Active Directory | Usługa Azure AD Premium P1 lub P2 | Usługa Azure AD Premium P1 lub P2 |

> [!NOTE]
> Lokalni użytkownicy usługi Active Directory systemu Windows Server, którzy nie są synchronizowani z usługą Azure Active Directory, korzystają również z ochrony hasłem usługi Azure AD na podstawie istniejących licencji dla zsynchronizowanych użytkowników.

Dodatkowe informacje o licencjonowaniu, w tym koszty, można znaleźć w [witrynie cenowej usługi Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="what-do-users-see"></a>Co widzą użytkownicy

Gdy użytkownik próbuje zresetować hasło do czegoś, co byłoby zablokowane, zobaczy następujący komunikat o błędzie:

Niestety hasło zawiera słowo, frazę lub wzór, który ułatwia odgadnięcie hasła. Spróbuj ponownie przy innym haśle.

## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie niestandardowej listy zakazanych haseł](howto-password-ban-bad.md)
- [Włączanie lokalnych agentów ochrony hasłem usługi Azure AD](howto-password-ban-bad-on-premises-deploy.md)
