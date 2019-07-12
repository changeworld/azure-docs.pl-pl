---
title: W środowisku lokalnym ochrony haseł usługi Azure AD — często zadawane pytania — usługa Azure Active Directory
description: Ochrona haseł usługi Azure AD w środowisku lokalnym — często zadawane pytania
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3b4879093ed80a554219b053cc5a2bc895126725
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67702890"
---
# <a name="azure-ad-password-protection-on-premises---frequently-asked-questions"></a>Azure AD ochrony hasłem lokalnych — często zadawane pytania

## <a name="general-questions"></a>Pytania ogólne

**Pyt.: Jakie wytyczne użytkowników należy zwrócić na sposób wybierania bezpieczne hasło?**

Firmy Microsoft Bieżące wskazówki na ten temat można znaleźć pod następujący link:

[Wskazówki dotyczące hasła Microsoft](https://www.microsoft.com/research/publication/password-guidance)

**Pyt.: Czy w środowisku lokalnym ochrony haseł usługi Azure AD w chmurach bez publicznego obsługiwane?**

Nie — ochrony haseł usługi Azure AD w środowisku lokalnym jest obsługiwana tylko w chmurze publicznej. Data nie zostało zapowiedziane na dostępności w chmurze bez publicznego.

**Pyt.: Jak zastosować korzyści z ochrony haseł usługi Azure AD do podzbioru użytkowników w środowisku lokalnym?**

Nieobsługiwane. Po wdrożeniu i włączone, nie rozróżniania ochrony haseł usługi Azure AD — wszyscy użytkownicy otrzymują korzyści równy zabezpieczeń.

**Pyt.: Jaka jest różnica między zmiany hasła i ustawianie hasła (lub zresetowanie)?**

Zmiana hasła jest, gdy użytkownik wybierze nowe hasło po potwierdzające, że mają oni wiedzę na temat stare hasło. Na przykład jest to, co się dzieje, gdy użytkownik loguje się do Windows, a następnie zostanie poproszony o wybranie nowego hasła.

Ustawianie hasła (nazywanych dalej "Resetowanie hasła") jest, gdy administrator zastępuje hasło do konta przy użyciu nowego hasła, na przykład za pomocą narzędzia zarządzania użytkownicy usługi Active Directory i komputery. Ta operacja wymaga wysokim poziomem uprawnień (zwykle administrator domeny), a osoba wykonująca operację zwykle nie ma wiedzy na temat stare hasło. Scenariusze pomocy technicznej często to zrobić, na przykład gdy wspomaganie użytkownik zapomniał hasła. Zobaczysz również, że hasło ustawione zdarzenia w przypadku nowego konta użytkownika jest tworzona po raz pierwszy przy użyciu hasła.

Sprawdzanie poprawności hasła zachowuje się takie same, niezależnie od tego, czy zmiany hasła lub zestaw jest wykonywana. Usługę agenta ochrony kontrolera domeny haseł usługi Azure AD rejestrowanie różnych zdarzeń informujące, czy zmiany hasła lub przeprowadzono operacji set.  Zobacz [ochrony haseł usługi Azure AD, monitorowanie i rejestrowanie](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-ban-bad-on-premises-monitor).

**Pyt.: Dlaczego używasz zduplikowane hasło o odrzuceniu zdarzenia rejestrowane, gdy trwa próba skonfigurowania słabym hasłem użytkownicy usługi Active Directory i komputerów w przystawce zarządzania**

Użytkownicy usługi Active Directory i komputerów w przystawce zarządzania próbują używać najpierw ustawić nowe hasło przy użyciu protokołu Kerberos. W przypadku awarii przystawki spowoduje, że drugi próba ustawienia hasła, przy użyciu starszej wersji protokołu (SAM RPC) (określonych protokołów używanych nie są ważne). Jeśli nowe hasło jest uznawany za słabe przez ochrony haseł usługi Azure AD, spowoduje to dwa zestawy rejestrowane zdarzenia odrzucenia resetowania hasła.

**Pyt.: Instalowanie ochrony haseł usługi Azure AD równolegle z innymi produktami na podstawie filtru haseł jest obsługiwane?**

Tak. Obsługa wielu biblioteki DLL filtru haseł zarejestrowanych jest funkcją Windows podstawowych i nie odnoszą się do ochrony haseł usługi Azure AD. Wszystkie biblioteki DLL filtru haseł zarejestrowanych musi wyrazić zgodę, zanim zostanie zaakceptowane hasła.

**Pyt.: Jak wdrożyć i skonfigurować ochrony haseł usługi Azure AD w mojej środowiska usługi Active Directory bez korzystania z usługi Azure?**

Nieobsługiwane. Ochrona za pomocą usługi Azure AD hasła jest nowa funkcja platformy Azure, który obsługuje zostanie przedłużony do środowiska usługi Active Directory w środowisku lokalnym.

**Pyt.: Jak można zmodyfikować zawartość zasad na poziomie usługi Active Directory?**

Nieobsługiwane. Zasady można tylko zarządzane za pomocą portalu zarządzania usługi Azure AD. Zobacz też poprzednie pytanie.

**Pyt.: Dlaczego DFSR jest wymagany do replikacji folderu sysvol**

Usługa replikacji plików (technologia poprzedzająca DFSR) ma wiele znanych problemów i jest całkowicie obsługiwana w nowszych wersjach systemu Windows Server Active Directory. Testowanie zera ochrony haseł usługi Azure AD zostanie wykonane w przypadku skonfigurowana usługi FRS domen.

Aby uzyskać więcej informacji zobacz następujące artykuły:

[W przypadku replikacji folderu sysvol Migrowanie do DFSR](https://blogs.technet.microsoft.com/askds/2010/04/22/the-case-for-migrating-sysvol-to-dfsr)

[Punkt końcowy jest Nigh w przypadku usługi FRS](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs)

**Pyt.: Ilość miejsca na dysku Ta funkcja wymaga udziału sysvol domeny?**

Użycie dokładne miejsca zmienia się, ponieważ zależy od czynników, takich jak liczby i długości zakazanych tokenów w globalnej listy zakazanych firmy Microsoft i listy niestandardowej dla dzierżawcy oraz obciążenie szyfrowania. Zawartość tych list prawdopodobnie może rosnąć w przyszłości. Mając to na uwadze uzasadnione przypuszczenie, zostały jest, że tej funkcji będą potrzebne co najmniej pięciu (5) w megabajtach ilość miejsca na udział sysvol domeny.

**Pyt.: Dlaczego jest wymagane ponowne uruchomienie Zainstaluj lub Uaktualnij oprogramowanie agenta kontrolera domeny?**

To wymaganie jest spowodowany przez podstawowe zachowanie Windows.

**Pyt.: Czy istnieje sposób można skonfigurować agenta kontrolera domeny, aby użyć konkretnego serwera proxy?**

Nie. Ponieważ serwer proxy jest bezstanowy, nie jest ważna które konkretnego serwera proxy jest używany.

**Pyt.: Jest to akceptowalne wdrożyć usługę Proxy ochrony haseł usługi Azure AD równolegle z innymi usługami takimi jak program Azure AD Connect?**

Tak. Usługa serwera Proxy ochrony haseł usługi Azure AD i Azure AD Connect powinno nigdy nie są w konflikcie ze sobą bezpośrednio.

**Pyt.: W jakiej kolejności należy agentów kontrolera domeny i serwery proxy jest zainstalowany i zarejestrowany?**

Jest obsługiwana w dowolnej kolejności instalacji agenta Proxy, instalacja agenta z kontrolera domeny, lasu rejestracji i Proxy rejestracji.

**Pyt.: Czy mogę być dane dotyczące wydajności dla moich kontrolerów domeny z wdrażanie tej funkcji?**

Usługę agenta ochrony kontrolera domeny haseł usługi Azure AD znacznie nie powinny mieć wpływ na wydajność kontrolera domeny w dobrej kondycji istniejącego wdrożenia usługi Active Directory.

Dla większości hasła wdrożeń usługi Active Directory operacji zmian są niewielką część całkowitego obciążenia na żadnym kontrolerze danej domeny. Na przykład Wyobraź sobie domeny usługi Active Directory z kontami użytkowników 10000 i zasad MaxPasswordAge ustawiona na 30 dni. Średnio tej domeny zostanie wyświetlony 10000/30 = operacji zmiany hasła ~ 333 każdego dnia, która jest pomocnicza liczby operacji dla nawet jednego kontrolera domeny. Należy wziąć pod uwagę potencjalny scenariusz: Załóżmy, że te 333 ~ zmiany haseł na jednym kontrolerze domeny zostały wykonane w ciągu jednej godziny. Na przykład w tym scenariuszu może wystąpić, gdy wielu pracowników wszystkich dostarczane do pracy w poniedziałek rano. Nawet w tym przypadku, nadal chcemy minut ~333/60 = sześć zmiany haseł na minutę, czyli ponownie nie znaczne obciążenie.

Jednak jeśli bieżące kontrolery domeny są już uruchomione na poziomach wydajności limited (na przykład maksymalnego limitu względem procesora CPU, miejsca na dysku, we/wy dysku, itp.), zaleca się dodawania dodatkowych kontrolerów domeny lub rozszerzeniu dostępnego miejsca na dysku, zanim wdrażanie tej funkcji. Zobacz też pytania powyżej dotyczące powyższych użycie miejsca na dysku folderu sysvol.

**Pyt.: Czy chcesz przetestować ochronę haseł usługi Azure AD na kilka kontrolerów domeny w mojej domeny. Czy jest możliwe do wymuszenia zmiany haseł użytkowników, aby użyć tych określone kontrolery domeny?**

Nie. System operacyjny klienta Windows określa, który kontroler domeny jest używany, gdy użytkownik zmieni swoje hasło. Kontroler domeny jest zaznaczony, na podstawie czynników, takich jak przypisania lokacji i podsieci usługi Active Directory, konfiguracji specyficznych dla środowiska sieci itp. Ochrona za pomocą usługi Azure AD hasła nie jest kontrolowane przez te czynniki i nie może mieć wpływ na kontroler domeny został wybrany do zmiany hasła użytkownika.

Jednym ze sposobów częściowo osiągnąć ten cel jest Wdrażanie ochrony haseł usługi Azure AD na wszystkich kontrolerach domeny w danej lokacji usługi Active Directory. To podejście zapewni uzasadnione pokrycia dla klientów Windows, które są przypisane do tej lokacji i w związku z tym również dla użytkowników, które są logując się do tych klientów i zmienianie swoich haseł.

**Pyt.: Jeśli zainstaluję usługę agenta ochrony kontrolera domeny haseł usługi Azure AD na tylko podstawowego kontrolera domeny (PDC), będą inne kontrolery domeny w domenie również chronione?**

Nie. Po zmianie hasła użytkownika na kontrolerze domeny danego bez PDC hasła w postaci zwykłego tekstu nigdy nie są wysyłane do kontrolera PDC (ten pomysł to typowe wrażenie niewłaściwa). Po zaakceptowaniu nowe hasło na danym kontrolerze domeny kontrolera domeny używa tego hasła w celu utworzenia różne skróty uwierzytelniania protokołu specyficzne dla tego hasła, a następnie będzie się powtarzać te skróty w katalogu. Hasło w postaci zwykłego tekstu nie jest trwały. Zaktualizowano skróty są następnie replikowane do podstawowego kontrolera domeny. Hasła użytkowników może w niektórych przypadkach można zmienić bezpośrednio na podstawowym kontrolerze domeny, ponownie w zależności od różnych czynników, takich jak topologii sieci i projektu lokacji usługi Active Directory. (Zobacz poprzedniego pytania).

Podsumowanie wdrażania usługi agenta DC ochronę haseł usługi Azure AD na podstawowym kontrolerze domeny jest wymagany do osiągnięcia 100% pokrycia zabezpieczeń funkcji w całej domenie. Wdrażanie funkcji na podstawowym kontrolerze domeny tylko nie zapewnia korzyści w zakresie zabezpieczeń ochrony haseł usługi Azure AD dla innych kontrolerów domeny w domenie.

**Pyt.: Pakiet administracyjny programu System Center Operations Manager jest dostępna dla ochrony haseł usługi Azure AD?**

Nie.

**Pyt.: Dlaczego Azure nadal odrzuca słabe hasła nawet, jeśli skonfigurowano zasady aby być w trybie inspekcji?**

Tryb inspekcji jest obsługiwany tylko w środowisku usługi Active Directory w środowisku lokalnym. Azure jest niejawnie zawsze w trybie "wymusić" gdy był oceniany hasła.

## <a name="additional-content"></a>Dodatkowa zawartość

Następujące łącza nie są częścią podstawowej dokumentacji ochrony haseł usługi Azure AD, ale mogą być przydatne źródło dodatkowe informacje na temat tej funkcji.

[Ochrony hasłem w usłudze Azure AD jest teraz ogólnie dostępne!](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-is-now-generally-available/ba-p/377487)

[Wiadomości e-mail przewodnik dotyczący ochrony wyłudzania informacji — część 15: Implementowanie usługi ochrony hasła usługi Microsoft Azure AD (dla lokalnego zbyt!)](https://blogs.technet.microsoft.com/cloudready/2018/10/14/email-phishing-protection-guide-part-15-implement-the-microsoft-azure-ad-password-protection-service-for-on-premises-too/)

[Usługa Azure AD ochrony hasłem i inteligentnej blokady są teraz w publicznej wersji zapoznawczej!](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-and-Smart-Lockout-are-now-in-Public/ba-p/245423#M529)

## <a name="microsoft-premierunified-support-training-available"></a>Szkolenia pomocy technicznej firmy Microsoft Premier\Unified dostępne

Jeśli interesuje Cię Dowiedz się więcej na temat ochrony haseł usługi Azure AD i wdrażania jej w środowisku, możesz korzystać z zalet usługi proaktywne firmy Microsoft dostępnych do klientów korzystających z umowę pomocy technicznej Premier lub Unified. Usługa jest wywoływana w usłudze Azure Active Directory: Ochrona za pomocą hasła. Aby uzyskać więcej informacji, skontaktuj się z menedżerem technicznych.

## <a name="next-steps"></a>Następne kroki

Jeśli masz pytanie ochrony haseł usługi Azure AD w środowisku lokalnym, który nie ma tutaj odpowiedzi, Prześlij elementu opinii poniżej. Dziękujemy!

[Wdrażanie ochrony haseł w usłudze Azure AD](howto-password-ban-bad-on-premises-deploy.md)
