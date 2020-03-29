---
title: Planowanie zasad dostępu warunkowego w usłudze Azure Active Directory | Dokumenty firmy Microsoft
description: W tym artykule dowiesz się, jak planować zasady dostępu warunkowego dla usługi Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: martincoetzer
ms.collection: M365-identity-device-management
ms.openlocfilehash: e1c75d5022432a9a57b30aabec4dd2c4f76f2f29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671827"
---
# <a name="how-to-plan-your-conditional-access-deployment-in-azure-active-directory"></a>Jak: Planowanie wdrożenia dostępu warunkowego w usłudze Azure Active Directory

Planowanie wdrożenia dostępu warunkowego ma kluczowe znaczenie dla osiągnięcia wymaganej strategii dostępu dla aplikacji i zasobów w organizacji. Spędzaj większość czasu na etapie planowania wdrożenia, aby zaprojektować różne zasady wymagane do przyznania lub zablokowania dostępu do użytkowników wybranych warunków. W tym dokumencie wyjaśniono kroki, które należy podjąć w celu zaimplementowania zasad bezpiecznego i skutecznego dostępu warunkowego. Przed rozpoczęciem upewnij się, że rozumiesz, jak działa [dostęp warunkowy](overview.md) i kiedy należy go używać.

## <a name="what-you-should-know"></a>Co należy wiedzieć

Dostęp warunkowy można potraktować jako strukturę, która umożliwia kontrolowanie dostępu do aplikacji i zasobów organizacji, a nie funkcji autonomicznej. W związku z tym niektóre ustawienia dostępu warunkowego wymagają dodatkowych funkcji do skonfigurowania. Na przykład można skonfigurować zasadę odpowiadającą określonej [poziomowi ryzyka logowania](../identity-protection/howto-identity-protection-configure-risk-policies.md). Jednak zasady, która jest oparta na poziomie ryzyka logowania wymaga [ochrony tożsamości usługi Azure Active Directory,](../identity-protection/overview-identity-protection.md) aby włączyć.

Jeśli wymagane są dodatkowe funkcje, może być również konieczne uzyskanie powiązanych licencji. Na przykład podczas gdy dostęp warunkowy jest funkcją Usługi Azure AD Premium P1, ochrona tożsamości wymaga licencji Usługi Azure AD Premium P2.

Istnieją dwa typy zasad dostępu warunkowego: linia bazowa i standard. [Zasady bazowe](baseline-protection.md) to wstępnie zdefiniowane zasady dostępu warunkowego. Celem tych zasad jest upewnienie się, że masz co najmniej poziom bazowy zabezpieczeń włączony. Zasady linii bazowej. Zasady linii bazowej są dostępne we wszystkich wersjach usługi Azure AD i zapewniają tylko ograniczone opcje dostosowywania. Jeśli scenariusz wymaga większej elastyczności, wyłącz zasady linii bazowej i zaimplementuj wymagania w niestandardowych zasadach standardowych.

W standardowych zasadach dostępu warunkowego można dostosować wszystkie ustawienia, aby dostosować zasady do wymagań biznesowych. Standardowe zasady wymagają licencji usługi Azure AD Premium P1.

>[!NOTE]
> Zalecamy korzystanie z zasad dostępu warunkowego opartego na urządzeniach usługi Azure AD, aby uzyskać najlepsze wymuszanie po początkowym uwierzytelnianiu urządzenia. Obejmuje to zamykanie sesji, jeśli urządzenie wypadnie z zgodności i przepływu kodu urządzenia.

## <a name="draft-policies"></a>Projekt zasad

Dostęp warunkowy usługi Azure Active Directory umożliwia wprowadzenie ochrony aplikacji w chmurze na nowy poziom. Na tym nowym poziomie, jak można uzyskać dostęp do aplikacji w chmurze opiera się na dynamicznej oceny zasad zamiast konfiguracji dostępu statycznego. Za pomocą zasad dostępu warunkowego można zdefiniować odpowiedź **(zrób to)** na warunek dostępu **(w takim przypadku).**

![Powód i odpowiedź](./media/plan-conditional-access/10.png)

Zdefiniuj wszystkie zasady dostępu warunkowego, które chcesz zaimplementować przy użyciu tego modelu planowania. Ćwiczenie planowania:

- Pomaga nakreślić odpowiedzi i warunki dla każdej zasady.
- Wyniki w dobrze udokumentowanym katalogu zasad dostępu warunkowego dla twojej organizacji. 

Za pomocą katalogu można ocenić, czy implementacja zasad odzwierciedla wymagania biznesowe organizacji. 

Użyj poniższego przykładowego szablonu, aby utworzyć zasady dostępu warunkowego dla organizacji:

|W *takim* przypadku:|Następnie zrób *to:*|
|-|-|
|Podejmowana jest próba dostępu:<br>- Do aplikacji*<br>w chmurze - Przez użytkowników i grupy*<br>Za pomocą:<br>- Warunek 1 (na przykład poza siecią Corp)<br>- Warunek 2 (na przykład platformy urządzeń)|Blokowanie dostępu do aplikacji|
|Podejmowana jest próba dostępu:<br>- Do aplikacji*<br>w chmurze - Przez użytkowników i grupy*<br>Za pomocą:<br>- Warunek 1 (na przykład poza siecią Corp)<br>- Warunek 2 (na przykład platformy urządzeń)|Udziel dostępu z (AND):<br>- Wymaganie 1 (na przykład mfa)<br>- Wymaganie 2 (na przykład zgodność urządzenia)|
|Podejmowana jest próba dostępu:<br>- Do aplikacji*<br>w chmurze - Przez użytkowników i grupy*<br>Za pomocą:<br>- Warunek 1 (na przykład poza siecią Corp)<br>- Warunek 2 (na przykład platformy urządzeń)|Udziel dostępu z (OR):<br>- Wymaganie 1 (na przykład mfa)<br>- Wymaganie 2 (na przykład zgodność urządzenia)|

Co najmniej, **gdy tak się stanie,** definiuje głównego zobowiązanego (**kto**), który próbuje uzyskać dostęp do aplikacji w chmurze (**co**). W razie potrzeby można również uwzględnić **sposób** wykonywania próby dostępu. W dostępie warunkowym: elementy, które definiują kto, co i jak są znane jako warunki. Aby uzyskać więcej informacji, zobacz [Jakie są warunki w usłudze Azure Active Directory Dostęp warunkowy?](concept-conditional-access-conditions.md) 

**Następnie należy to zrobić,** należy zdefiniować odpowiedź zasad na warunek dostępu. W odpowiedzi można zablokować lub udzielić dostępu z dodatkowymi wymaganiami, na przykład uwierzytelnianie wieloskładnikowe (MFA). Aby uzyskać pełny przegląd, zobacz [Co to są formanty dostępu w usłudze Azure Active Directory dostęp warunkowy?](controls.md)  

Kombinacja warunków z formantami dostępu reprezentuje zasady dostępu warunkowego.

![Powód i odpowiedź](./media/plan-conditional-access/51.png)

Aby uzyskać więcej informacji, [zobacz, co jest wymagane do działania zasad](best-practices.md#whats-required-to-make-a-policy-work).

W tym momencie jest to dobry moment, aby zdecydować się na standard nazewnictwa dla zasad. Standard nazewnictwa pomaga znaleźć zasady i zrozumieć ich cel bez otwierania ich w witrynie administratora platformy Azure. Nadaj nazwę zasadom, aby pokazać:

- Numer sekwencyjny
- Aplikacja w chmurze, do jakiej się stosuje
- Odpowiedź
- Kogo dotyczy
- Kiedy ma to zastosowanie (jeśli dotyczy)
 
![Standard nazewnictwa](./media/plan-conditional-access/11.png)

Podczas gdy opisowa nazwa pomaga zachować przegląd implementacji dostępu warunkowego, numer sekwencyjny jest przydatny, jeśli trzeba odwołać się do zasad w konwersacji. Na przykład, jeśli rozmawiasz z innym administratorem przez telefon, możesz poprosić go o otwarcie zasady EM063 w celu rozwiązania problemu.

Na przykład następująca nazwa stwierdza, że zasada wymaga usługi MFA dla użytkowników marketingowych w sieciach zewnętrznych przy użyciu aplikacji Dynamics CRP:

`CA01 - Dynamics CRP: Require MFA For marketing When on external networks`

Oprócz aktywnych zasad zaleca się również wdrożenie wyłączonych zasad, które działają jako [pomocnicze odporne kontrole dostępu w scenariuszach awarii/sytuacji awaryjnych.](../authentication/concept-resilient-controls.md) Twój standard nazewnictwa dla zasad awaryjnych powinien zawierać jeszcze kilka elementów: 

- `ENABLE IN EMERGENCY`na początku, aby nazwa wyróżniała się wśród innych polityk.
- Nazwa zakłóceń, do których powinna się odnosić.
- Numer sekwencyjny zamawiania, aby pomóc administratorowi wiedzieć, w jakiej kolejności zasady powinny być włączone. 

Na przykład następująca nazwa wskazuje, że ta zasada jest pierwszą zasadą z czterech, które należy włączyć, jeśli występuje zakłócenie usługi MFA:

`EM01 - ENABLE IN EMERGENCY, MFA Disruption[1/4] - Exchange SharePoint: Require hybrid Azure AD join For VIP users`

## <a name="plan-policies"></a>Zasady planu

Planując rozwiązanie zasad dostępu warunkowego, należy ocenić, czy należy utworzyć zasady, aby osiągnąć następujące wyniki. 

### <a name="block-access"></a>Blokowanie dostępu

Opcja blokowania dostępu jest potężna, ponieważ:

- Przebija wszystkie inne zadania dla użytkownika
- Ma możliwość zablokowania rejestracji całej organizacji w dzierżawie
 
Jeśli chcesz zablokować dostęp dla wszystkich użytkowników, należy co najmniej wykluczyć jednego użytkownika (zazwyczaj konta dostępu awaryjnego) z zasad. Aby uzyskać więcej informacji, zobacz [wybieranie użytkowników i grup](block-legacy-authentication.md#select-users-and-cloud-apps).  

### <a name="require-mfa"></a>Wymaganie usługi MFA

Aby uprościć środowisko logowania użytkowników, możesz zezwolić im na logowanie się do aplikacji w chmurze przy użyciu nazwy użytkownika i hasła. Jednak zazwyczaj istnieją co najmniej niektóre scenariusze, dla których wskazane jest, aby wymagać silniejszej formy weryfikacji konta. Za pomocą zasad dostępu warunkowego można ograniczyć wymagania dotyczące usługi MFA do niektórych scenariuszy. 

Typowe przypadki użycia wymagające usługi MFA to dostęp:

- [Według adminów](howto-baseline-protect-administrators.md)
- [Do określonych aplikacji](app-based-mfa.md) 
- [Z lokalizacji sieciowych nie ufasz](untrusted-networks.md).

### <a name="respond-to-potentially-compromised-accounts"></a>Odpowiadanie na potencjalnie przejęte konta

Za pomocą zasad dostępu warunkowego można zaimplementować automatyczne odpowiedzi na logowania z potencjalnie naruszonych tożsamości. Prawdopodobieństwo naruszenia zabezpieczeń konta jest wyrażone w postaci poziomów ryzyka. Istnieją dwa poziomy ryzyka obliczone na podstawie ochrony tożsamości: ryzyko logowania i ryzyko użytkownika. Aby zaimplementować odpowiedź na ryzyko logowania, dostępne są dwie opcje:

- [Warunek ryzyka logowania w zasadach](concept-conditional-access-conditions.md#sign-in-risk) dostępu warunkowego
- [Zasady ryzyka logowania w ochronie](../identity-protection/howto-sign-in-risk-policy.md) tożsamości 

Adresowanie ryzyka logowania jako warunek jest preferowaną metodą, ponieważ daje więcej opcji dostosowywania.

Poziom ryzyka użytkownika jest dostępny tylko jako [zasady ryzyka użytkownika](../identity-protection/howto-user-risk-policy.md) w ochronie tożsamości. 

Aby uzyskać więcej informacji, zobacz [Co to jest ochrona tożsamości usługi Azure Active Directory?](../identity-protection/overview.md) 

### <a name="require-managed-devices"></a>Wymaganie urządzeń zarządzanych

Rozprzestrzenianie się obsługiwanych urządzeń w celu uzyskania dostępu do zasobów w chmurze pomaga zwiększyć produktywność użytkowników. Z drugiej strony prawdopodobnie nie chcesz, aby niektóre zasoby w danym środowisku były dostępne dla urządzeń o nieznanym poziomie ochrony. W przypadku zasobów, których dotyczy problem, należy wymagać, aby użytkownicy mogli uzyskiwać do nich dostęp tylko przy użyciu urządzenia zarządzanego. Aby uzyskać więcej informacji, zobacz [Jak wymagać zarządzanych urządzeń do dostępu do aplikacji w chmurze za pomocą dostępu warunkowego](require-managed-devices.md). 

### <a name="require-approved-client-apps"></a>Wymaganie zatwierdzonych aplikacji klienckich

Jedną z pierwszych decyzji, które należy podjąć dla przynieść własne urządzenia (BYOD) scenariusze, jest to, czy trzeba zarządzać całym urządzeniem lub tylko dane na nim. Pracownicy używają urządzeń przenośnych zarówno do celów służbowych, jak i prywatnych. Upewniając się, że pracownicy mogą być produktywni, chcesz również zapobiec utracie danych. Dzięki dostępowi warunkowemu usługi Azure Active Directory (Azure AD) można ograniczyć dostęp do aplikacji w chmurze do zatwierdzonych aplikacji klienckich, które mogą chronić dane firmowe. Aby uzyskać więcej informacji, zobacz [Jak wymagać zatwierdzonych aplikacji klienckich do dostępu do aplikacji w chmurze z dostępem warunkowym](app-based-conditional-access.md).

### <a name="block-legacy-authentication"></a>Blokowanie starszego uwierzytelniania

Usługa Azure AD obsługuje kilka najczęściej używanych protokołów uwierzytelniania i autoryzacji, w tym uwierzytelnianie starsze. Jak zapobiec dostępowi aplikacji korzystających ze starszego uwierzytelniania do zasobów dzierżawy? Zalecenie jest po prostu zablokować je za pomocą zasad dostępu warunkowego. W razie potrzeby zezwalasz tylko niektórym użytkownikom i określonym lokalizacjom sieciowym na korzystanie z aplikacji opartych na starszym uwierzytelnianiu. Aby uzyskać więcej informacji, zobacz [Jak zablokować starsze uwierzytelnianie w usłudze Azure AD za pomocą dostępu warunkowego](block-legacy-authentication.md).

## <a name="test-your-policy"></a>Testowanie zasad

Przed wprowadzeniem zasad do produkcji, należy przetestować jest sprawdzenie, czy zachowuje się zgodnie z oczekiwaniami.

1. Tworzenie użytkowników testowych
1. Tworzenie planu testów
1. Konfigurowanie zasad
1. Ocena symulowanego logowania
1. Testowanie zasad
1. Czyszczenie

### <a name="create-test-users"></a>Tworzenie użytkowników testowych

Aby przetestować zasady, należy utworzyć zestaw użytkowników, który jest podobny do użytkowników w środowisku. Tworzenie użytkowników testowych umożliwia sprawdzenie, czy zasady działają zgodnie z oczekiwaniami, zanim wpłyniesz na rzeczywistych użytkowników i potencjalnie zakłócisz ich dostęp do aplikacji i zasobów. 

Niektóre organizacje mają dzierżawy testowe w tym celu. Jednak może być trudne do ponownego tworzenia wszystkich warunków i aplikacji w dzierżawie testowej, aby w pełni przetestować wynik zasad. 

### <a name="create-a-test-plan"></a>Tworzenie planu testów

Plan testu jest ważne, aby mieć porównanie oczekiwanych wyników i rzeczywistych wyników. Zawsze należy mieć oczekiwania przed przetestowaniem czegoś. W poniższej tabeli przedstawiono przykładowe przypadki testowe. Dostosuj scenariusze i oczekiwane wyniki na podstawie sposobu konfigurowania zasad urzędu certyfikacji.

|Zasady |Scenariusz |Oczekiwany wynik | Wynik |
|---|---|---|---|
|[Wymagaj pomocy makrofinansowej, gdy nie pracujesz](/azure/active-directory/conditional-access/untrusted-networks)|Autoryzowany użytkownik loguje się do *aplikacji* w zaufanej lokalizacji / pracy|Użytkownik nie jest monitowany o uwierzytelnianie usługi MFA| |
|[Wymagaj pomocy makrofinansowej, gdy nie pracujesz](/azure/active-directory/conditional-access/untrusted-networks)|Autoryzowany użytkownik loguje się do *aplikacji,* gdy nie ma zaufanej lokalizacji / pracy|Użytkownik jest monitowany o uwierzytelnianie wieloskładnikowe i może zalogować się pomyślnie| |
|[Wymagaj usługi MFA (dla administratora)](/azure/active-directory/conditional-access/howto-baseline-protect-administrators)|Globalny administrator loguje się do *aplikacji*|Administrator jest monitowany o uwierzytelnianie wieloskładnikowe| |
|[Ryzykowne logowania](/azure/active-directory/identity-protection/howto-sign-in-risk-policy)|Użytkownik loguje się do *aplikacji* za pomocą [przeglądarki Tora](/azure/active-directory/active-directory-identityprotection-playbook)|Administrator jest monitowany o uwierzytelnianie wieloskładnikowe| |
|[Zarządzanie urządzeniami](/azure/active-directory/conditional-access/require-managed-devices)|Autoryzowany użytkownik próbuje zalogować się z autoryzowanego urządzenia|Przyznany dostęp| |
|[Zarządzanie urządzeniami](/azure/active-directory/conditional-access/require-managed-devices)|Autoryzowany użytkownik próbuje zalogować się z nieautoryzowanego urządzenia|Dostęp zablokowany| |
|[Zmiana hasła dla ryzykownych użytkowników](/azure/active-directory/identity-protection/howto-user-risk-policy)|Autoryzowany użytkownik próbuje zalogować się przy użyciu naruszonych poświadczeń (logowanie wysokiego ryzyka)|Użytkownik jest monitowany o zmianę hasła lub dostęp jest zablokowany na podstawie zasad| |

### <a name="configure-the-policy"></a>Konfigurowanie zasad

Zarządzanie zasadami dostępu warunkowego jest zadaniem ręcznym. W witrynie Azure portal można zarządzać zasadami dostępu warunkowego w jednej centralnej lokalizacji — na stronie Dostęp warunkowy. Jednym z punktów wejścia na stronę Dostęp warunkowy jest sekcja **Zabezpieczenia** w okienku nawigacji **usługi Active Directory.** 

![Dostęp warunkowy](media/plan-conditional-access/03.png)

Aby dowiedzieć się więcej o tworzeniu zasad dostępu warunkowego, zobacz [Wymaganie usługi MFA dla określonych aplikacji z dostępem warunkowym usługi Azure Active Directory](app-based-mfa.md). Ten przewodnik Szybki start pomaga:

- Zapoznaj się z interfejsem użytkownika.
- Pierwsze wrażenie o tym, jak działa dostęp warunkowy. 

### <a name="evaluate-a-simulated-sign-in"></a>Ocena symulowanego logowania

Teraz, gdy skonfigurowano zasady dostępu warunkowego, prawdopodobnie chcesz wiedzieć, czy działa zgodnie z oczekiwaniami. W pierwszym kroku użyj narzędzia Dostępu [warunkowego, jeśli narzędzie zasad](what-if-tool.md) do symulowania logowania użytkownika testowego. Symulacja szacuje wpływ tego logowania na zasady i generuje raport symulacji.

>[!NOTE]
> Podczas symulowanego przebiegu daje wrażenie wpływu zasady dostępu warunkowego ma, nie zastępuje rzeczywistego przebiegu testu.

### <a name="test-your-policy"></a>Testowanie zasad

Uruchom przypadków testowych zgodnie z planem testu. W tym kroku można uruchomić za pomocą testu end-to-end każdej zasady dla użytkowników testowych, aby upewnić się, że każda zasada działa poprawnie. Użyj scenariuszy utworzonych powyżej, aby wykonać każdy test.

Ważne jest, aby upewnić się, że testujesz kryteria wykluczenia zasad. Na przykład można wykluczyć użytkownika lub grupy z zasad, które wymagają usługi MFA. Sprawdź, czy wykluczoni użytkownicy są monitowani o uwierzytelnianie wieloskładnikowe, ponieważ kombinacja innych zasad może wymagać usługi MFA dla tych użytkowników.

### <a name="cleanup"></a>Czyszczenie

Procedura oczyszczania składa się z następujących kroków:

1. Wyłącz zasady.
1. Usuń przypisanych użytkowników i grupy.
1. Usuń użytkowników testowych.  

## <a name="move-to-production"></a>Przenoszenie do środowiska produkcyjnego

Gdy nowe zasady są gotowe dla środowiska, wdrażaj je etapami:

- Zapewnij użytkownikom końcowym komunikację wewnętrzną w sprawie zmian.
- Zacznij od małego zestawu użytkowników i sprawdź, czy zasady zachowują się zgodnie z oczekiwaniami.
- Po rozwinięciu zasad o większą liczby użytkowników należy nadal wykluczać wszystkich administratorów. Wykluczenie administratorów gwarantuje, że ktoś nadal ma dostęp do zasad, jeśli wymagana jest zmiana.
- Zastosuj zasady do wszystkich użytkowników tylko wtedy, gdy jest to wymagane.

Najlepszym rozwiązaniem jest utworzenie co najmniej jednego konta użytkownika, które jest:

- Dedykowane do zarządzania politykami
- Wykluczone ze wszystkich zasad

## <a name="rollback-steps"></a>Kroki wycofywania

W przypadku konieczności wycofania nowo wdrożonych zasad należy użyć co najmniej jednej z następujących opcji, aby wycofać:

1. **Wyłącz zasady** — wyłączenie zasad powoduje, że nie ma ona zastosowania, gdy użytkownik próbuje się zalogować. Zawsze możesz wrócić i włączyć zasady, gdy chcesz z niej korzystać.

   ![Wyłączanie zasad](media/plan-conditional-access/07.png)

1. **Wykluczanie użytkownika / grupy z zasad** — jeśli użytkownik nie może uzyskać dostępu do aplikacji, możesz wykluczyć użytkownika z zasad

   ![Użytkownicy Exluce](media/plan-conditional-access/08.png)

   > [!NOTE]
   > Ta opcja powinna być używana oszczędnie, tylko w sytuacjach, gdy użytkownik jest zaufany. Użytkownik powinien zostać dodany z powrotem do zasad lub grupy tak szybko, jak to możliwe.

1. **Usuń zasady** — jeśli zasada nie jest już wymagana, usuń ją.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [dokumentacją dostępu warunkowego usługi Azure AD,](index.yml) aby uzyskać omówienie dostępnych informacji.
