---
title: Lokalne usługi Azure AD Password Protection — często zadawane pytania — Azure Active Directory
description: Lokalna Ochrona hasłem usługi Azure AD — często zadawane pytania
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
ms.openlocfilehash: 9f1f2e06eb6b5f8d402515ff1c07a4163174495d
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2019
ms.locfileid: "68666356"
---
# <a name="azure-ad-password-protection-on-premises---frequently-asked-questions"></a>Ochrona hasłem w usłudze Azure AD — często zadawane pytania

Ta sekcja zawiera odpowiedzi na wiele często zadawanych pytań dotyczących ochrony hasłem w usłudze Azure AD.

## <a name="general-questions"></a>Pytania ogólne

**Pyt.: Jakie wskazówki powinni udzielić użytkownikom w celu wybrania bezpiecznego hasła?**

Bieżące wskazówki dotyczące tego tematu firmy Microsoft można znaleźć w następującym łączu:

[Wskazówki dotyczące haseł firmy Microsoft](https://www.microsoft.com/research/publication/password-guidance)

**Pyt.: Czy lokalna Ochrona hasłem usługi Azure AD jest obsługiwana w chmurach niepublicznych?**

Żadna lokalna Ochrona hasłem usługi Azure AD jest obsługiwana tylko w chmurze publicznej. Nie ogłoszono żadnych dat dla niepublicznej dostępności w chmurze.

**Pyt.: Jak mogę zastosować korzyści z ochrony hasłem w usłudze Azure AD do podzbioru użytkowników lokalnych?**

Nieobsługiwane. Po wdrożeniu i włączeniu ochrony hasłem usługi Azure AD nie należy odróżniać — wszyscy użytkownicy otrzymują równe korzyści z zabezpieczeń.

**Pyt.: Jaka jest różnica między zmianą hasła i ustawieniem hasła (lub resetowania)?**

Zmiana hasła jest konieczna, gdy użytkownik wybierze nowe hasło po udowodnieniu, że ma wiedzę o starym haśle. Na przykład dzieje się tak, gdy użytkownik loguje się do systemu Windows i zostanie wyświetlony monit o wybranie nowego hasła.

Ustawienie hasła (nazywane czasem resetowaniem hasła) polega na tym, że administrator zastępuje hasło na koncie z nowym hasłem, na przykład za pomocą narzędzia do zarządzania użytkownikami i komputerami Active Directory. Ta operacja wymaga wysokiego poziomu uprawnień (zazwyczaj administratora domeny), a osoba wykonująca operację zwykle nie ma informacji o starym haśle. Scenariusze pomocy technicznej często odbywają się w ten sposób, na przykład podczas wspomagania użytkownika, który zapomniał swoje hasło. W przypadku tworzenia nowego konta użytkownika po raz pierwszy przy użyciu hasła będą widoczne także zdarzenia ustawiania hasła.

Zasady walidacji hasła działają tak samo, niezależnie od tego, czy są wykonywane zmiany lub ustawienia hasła. Usługa agenta DC ochrony hasłem w usłudze Azure AD rejestruje różne zdarzenia w celu powiadomienia użytkownika o tym, czy operacja zmiany lub ustawienia hasła została ukończona.  Zobacz [monitorowanie i rejestrowanie w usłudze Azure AD Password Protection](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-ban-bad-on-premises-monitor).

**Pyt.: Dlaczego zduplikowane zdarzenia odrzucania hasła zostały zarejestrowane podczas próby ustawienia słabego hasła przy użyciu przystawki Zarządzanie użytkownikami i komputerami Active Directory?**

Przystawka Zarządzanie użytkownikami i komputerami Active Directory najpierw podejmie próbę ustawienia nowego hasła przy użyciu protokołu Kerberos. Gdy wystąpi błąd, przystawka wykona kolejną próbę ustawienia hasła przy użyciu starszego protokołu (SAM protokół RPC) (używane protokoły nie są ważne). Jeśli nowe hasło jest uznawane za słabe przez ochronę hasłem usługi Azure AD, spowoduje to zarejestrowanie dwóch zestawów zdarzeń odrzucenia resetowania hasła.

**Pyt.: Dlaczego zdarzenia weryfikacji hasła ochrony hasłem usługi Azure AD są rejestrowane przy użyciu pustej nazwy użytkownika?**

Active Directory obsługuje możliwość przetestowania hasła w celu sprawdzenia, czy przeszło bieżące wymagania dotyczące złożoności hasła domeny, na przykład za pomocą interfejsu API [NetValidatePasswordPolicy](https://docs.microsoft.com/windows/win32/api/lmaccess/nf-lmaccess-netvalidatepasswordpolicy) . Po sprawdzeniu poprawności hasła w ten sposób testowanie obejmuje również sprawdzanie poprawności przez produkty oparte na bibliotece Password-Filter-dll, takie jak ochrona hasłem usługi Azure AD, ale nazwy użytkowników przekazane do danej biblioteki DLL filtru haseł będą puste. W tym scenariuszu Ochrona hasłem w usłudze Azure AD będzie nadal weryfikować hasło przy użyciu obecnie obowiązujących zasad haseł i wystawia komunikat dziennika zdarzeń w celu przechwycenia wyniku, ale komunikat dziennika zdarzeń będzie miał puste pola nazwy użytkownika.

**Pyt.: Czy jest obsługiwana instalacja ochrony hasłem usługi Azure AD obok innych produktów opartych na filtrze haseł?**

Tak. Obsługa wielu zarejestrowanych bibliotek DLL filtru haseł jest podstawową funkcją systemu Windows i nie jest specyficzna dla ochrony hasłem usługi Azure AD. Przed zaakceptowaniem hasła wszystkie zarejestrowane biblioteki DLL filtru haseł muszą być zgodne.

**Pyt.: Jak można wdrożyć i skonfigurować ochronę hasłem w usłudze Azure AD w środowisku Active Directory bez korzystania z platformy Azure?**

Nieobsługiwane. Ochrona hasłem w usłudze Azure AD to funkcja platformy Azure, która obsługuje rozszerzanie do lokalnego środowiska Active Directory.

**Pyt.: Jak mogę zmodyfikować zawartość zasad na poziomie Active Directory?**

Nieobsługiwane. Zasady można administrować tylko za pomocą portalu zarządzania usługi Azure AD. Zobacz również poprzednie pytanie.

**Pyt.: Dlaczego Usługa DFSR jest wymagana na potrzeby replikacji folderu SYSVOL?**

Usługa FRS (technologia poprzednika do DFSR) ma wiele znanych problemów i jest całkowicie nieobsługiwana w nowszych wersjach systemu Windows Server Active Directory. W domenach skonfigurowanych przez usługę FRS nie będzie przeprowadzane testowanie ochrony hasłem w usłudze Azure AD.

Aby uzyskać więcej informacji, zobacz następujące artykuły:

[Przypadek migrowania replikacji folderu SYSVOL do usługi DFSR](https://blogs.technet.microsoft.com/askds/2010/04/22/the-case-for-migrating-sysvol-to-dfsr)

[Koniec to Nigh dla usługi FRS](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs)

**Pyt.: Ile miejsca na dysku jest wymagane przez funkcję w udziale Sysvol domeny?**

Precyzyjne użycie miejsca zależy od tego, czy jest to zależne od czynników, takich jak liczba i długość zabronionych tokenów na liście globalnie zabronionych firmy Microsoft i na liście niestandardowej dla dzierżawy oraz narzuty szyfrowania. Zawartość tych list może się zwiększać w przyszłości. Z tego względu uzasadnione jest, że funkcja będzie potrzebować co najmniej pięciu (5) megabajtów miejsca w udziale Sysvol domeny.

**Pyt.: Dlaczego wymagane jest ponowne uruchomienie w celu zainstalowania lub uaktualnienia oprogramowania agenta kontrolera domeny?**

Ten wymóg jest spowodowany przez podstawowe zachowanie systemu Windows.

**Pyt.: Czy istnieje możliwość skonfigurowania agenta DC do korzystania z określonego serwera proxy?**

Nie. Ponieważ serwer proxy jest bezstanowy, nie ma znaczenia, który z nich jest używany.

**Pyt.: Czy warto wdrożyć usługę serwera proxy ochrony haseł usługi Azure AD obok innych usług, takich jak Azure AD Connect?**

Tak. Usługa serwera proxy ochrony hasłem usługi Azure AD i Azure AD Connect nigdy nie powinna powodować konfliktu bezpośrednio ze sobą.

**Pyt.: W jakiej kolejności mają być zainstalowane i zarejestrowane agenci i serwery proxy kontrolera domeny?**

Obsługiwane jest dowolna kolejność instalacji agenta proxy, instalacji agenta kontrolera domeny, rejestracji lasów oraz rejestracji serwera proxy.

**Pyt.: Czy należy mieć na celu osiągnięcie wydajności na kontrolerach domeny przed wdrożeniem tej funkcji?**

Usługa agenta DC ochrony hasłem w usłudze Azure AD nie powinna znacząco wpływać na wydajność kontrolera domeny w istniejącym wdrożeniu Active Directory w dobrej kondycji.

W przypadku większości Active Directory operacje zmiany hasła są niewielką częścią ogólnego obciążenia na dowolnym kontrolerze domeny. Załóżmy na przykład, że Active Directory domeny z kontami użytkowników 10000 i zasadami MaxPasswordAge ustawionymi na 30 dni. Średnio w tej domenie zobaczysz w każdym dniu 10000/30 = ~ 333 operacji zmiany hasła, co stanowi niewielką liczbę operacji dla nawet jednego kontrolera domeny. Rozważmy potencjalny scenariusz najgorszego przypadku: Załóżmy, że te ~ 333 zmiany hasła na jednym kontrolerze domeny zostały wykonane w ciągu jednej godziny. Na przykład ten scenariusz może wystąpić, gdy wielu pracowników ma działać w poniedziałek rano. Nawet w takim przypadku nadal trwają około 333/60 minut = sześć zmian haseł na minutę, co nie jest znaczącym obciążeniem.

Jeśli jednak bieżące kontrolery domeny są już uruchomione na ograniczonych poziomach wydajności (na przykład maxed się w odniesieniu do procesora CPU, miejsca na dysku, we/wy dysku itp.), zaleca się dodanie kolejnych kontrolerów domeny lub rozwinięcie dostępnego miejsca na dysku przed wdrażanie tej funkcji. Należy również zapoznać się z powyższymi pytaniami dotyczącymi użycia miejsca na dysku SYSVOL.

**Pyt.: Chcę przetestować ochronę hasłem usługi Azure AD tylko w kilku domenach w domenie. Czy istnieje możliwość wymuszenia zmiany hasła użytkownika na korzystanie z tych konkretnych kontrolerów domeny?**

Nie. System operacyjny klienta systemu Windows kontroluje, który kontroler domeny jest używany, gdy użytkownik zmienia swoje hasło. Kontroler domeny jest wybierany w oparciu o takie czynniki jak Active Directory przypisań lokacji i podsieci, konfiguracji sieci specyficznej dla środowiska itp. Ochrona hasłem w usłudze Azure AD nie kontroluje tych czynników i nie ma wpływu na kontroler domeny wybrany do zmiany hasła użytkownika.

Jednym ze sposobów na częściowe osiągnięcie tego celu jest wdrożenie ochrony hasłem usługi Azure AD na wszystkich kontrolerach domeny w danej lokacji Active Directory. Takie podejście zapewni rozsądne pokrycie dla klientów systemu Windows przypisanych do tej lokacji, w związku z tym również dla użytkowników logujących się na tych klientach i zmieniających ich hasła.

**Pyt.: Jeśli zainstaluję usługę agenta DC ochrony hasłem usługi Azure AD tylko na podstawowym kontrolerze domeny (PDC), czy wszystkie inne kontrolery domeny będą również chronione?**

Nie. Gdy hasło użytkownika jest zmieniane na danym kontrolerze domeny innego niż kontroler PDC, hasło w postaci zwykłego tekstu nigdy nie jest wysyłane do kontrolera PDC (ten pomysł jest typowym niepercepcjem). Po zaakceptowaniu nowego hasła dla danego kontrolera domeny kontroler ten używa tego hasła do utworzenia różnych wartości skrótu właściwych dla protokołu uwierzytelniania danego hasła, a następnie utrwala te skróty w katalogu. Hasło w postaci zwykłego tekstu nie jest utrwalone. Zaktualizowane skróty są następnie replikowane do podstawowego kontrolera domeny. W niektórych przypadkach hasła użytkowników mogą być zmieniane bezpośrednio na podstawowym kontrolerze domeny, w zależności od różnych czynników, takich jak topologia sieci i Active Directory projektowanie lokacji. (Zobacz poprzednie pytanie).

Podsumowując, wdrożenie usługi agenta DC ochrony hasła usługi Azure AD na podstawowym kontrolerze domeny jest wymagane do uzyskania dostępu do 100% tej funkcji w całej domenie. Wdrożenie funkcji na podstawowym kontrolerze PDC nie zapewnia korzyści związanych z bezpieczeństwem ochrony hasłem usługi Azure AD dla innych kontrolerów domeny w domenie.

**Pyt.: Czy jest dostępny System Center Operations Manager pakiet administracyjny do ochrony hasłem usługi Azure AD?**

Nie.

**Pyt.: Dlaczego platforma Azure nadal odrzuca słabe hasła, mimo że skonfigurowano zasady tak, aby były w trybie inspekcji?**

Tryb inspekcji jest obsługiwany tylko w środowisku lokalnym Active Directory. Platforma Azure jest niejawnie zawsze w trybie "Wymuś" podczas obliczania haseł.

## <a name="additional-content"></a>Dodatkowa zawartość

Następujące linki nie są częścią podstawowej dokumentacji ochrony hasłem usługi Azure AD, ale mogą być użytecznym źródłem dodatkowych informacji na temat tej funkcji.

[Ochrona hasłem w usłudze Azure AD jest teraz ogólnie dostępna!](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-is-now-generally-available/ba-p/377487)

[Przewodnik po phishingu wiadomości e-mail — część 15: Zaimplementuj usługę ochrony hasłem Microsoft Azure AD (w przypadku miejsca lokalnego!)](https://blogs.technet.microsoft.com/cloudready/2018/10/14/email-phishing-protection-guide-part-15-implement-the-microsoft-azure-ad-password-protection-service-for-on-premises-too/)

[Usługa Azure AD Password Protection i inteligentna blokada są teraz dostępne w publicznej wersji zapoznawczej.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-and-Smart-Lockout-are-now-in-Public/ba-p/245423#M529)

## <a name="microsoft-premierunified-support-training-available"></a>Dostępne szkolenia Microsoft Premier\Unified support

Jeśli chcesz dowiedzieć się więcej o ochronie haseł usługi Azure AD i wdrażaniu jej w środowisku, możesz skorzystać z usługi Microsoft proaktywnie dostępnej dla tych klientów z umową Premier lub Unified support. Usługa jest nazywana Azure Active Directory: Ochrona hasłem. Aby uzyskać więcej informacji, skontaktuj się z kierownikiem ds. klientów.

## <a name="next-steps"></a>Następne kroki

Jeśli masz lokalne pytanie ochrony hasła usługi Azure AD, na które nie udzielono odpowiedzi, Prześlij element opinii poniżej — Dziękujemy!

[Wdrażanie ochrony haseł w usłudze Azure AD](howto-password-ban-bad-on-premises-deploy.md)
