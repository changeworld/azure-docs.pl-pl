---
title: Lokalne często zadawane pytania dotyczące ochrony hasłem usługi Azure AD
description: Przeglądanie często zadawanych pytań dotyczących ochrony hasłem usługi Azure AD w lokalnym środowisku usług domenowych Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: abef6e52e37cfa2faeb426bc59bb0de5a52a6658
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671679"
---
# <a name="azure-ad-password-protection-on-premises-frequently-asked-questions"></a>Często zadawane pytania dotyczące ochrony haseł usługi Azure AD w środowisku lokalnym

Ta sekcja zawiera odpowiedzi na wiele często zadawanych pytań dotyczących ochrony hasłem usługi Azure AD.

## <a name="general-questions"></a>Pytania ogólne

**Pyt.: Jakie wskazówki powinni być podane przez użytkowników, jak wybrać bezpieczne hasło?**

Aktualne wskazówki firmy Microsoft dotyczące tego tematu można znaleźć pod następującym linkiem:

[Wskazówki dotyczące haseł firmy Microsoft](https://www.microsoft.com/research/publication/password-guidance)

**Pyt.: Czy lokalna ochrona hasłem usługi Azure AD jest obsługiwana w chmurach niepublicznych?**

Nie — lokalna ochrona hasłem usługi Azure AD jest obsługiwana tylko w chmurze publicznej. Nie ogłoszono daty dostępności chmury niepublicznej.

Portal usługi Azure AD zezwala na modyfikację konfiguracji "Ochrona hasłem dla usługi Active Directory systemu Windows Server" w lokalnej usłudze Windows Server nawet w chmurach niepublicznych; takie zmiany będą kontynuowane, ale w przeciwnym razie nigdy nie wejdą w życie. Rejestracja lokalnych agentów proxy lub lasów nie jest przechwycona, gdy używane są poświadczenia w chmurze niepublicznej, a wszelkie takie próby rejestracji zawsze zakończy się niepowodzeniem.

**Pyt.: Jak można zastosować korzyści usługi Azure AD Password Protection do podzbioru moich użytkowników lokalnych?**

Bez pomocy technicznej. Po wdrożeniu i włączeniu usługa Azure AD Password Protection nie dyskryminuje — wszyscy użytkownicy otrzymują równe korzyści z zabezpieczeń.

**P: Jaka jest różnica między zmianą hasła a zestawem haseł (lub resetowaniem)?**

Zmiana hasła jest wtedy, gdy użytkownik wybiera nowe hasło po udowodnieniu, że ma wiedzę na temat starego hasła. Na przykład zmiana hasła jest to, co się dzieje, gdy użytkownik loguje się do systemu Windows, a następnie jest monitowany o wybranie nowego hasła.

Zestaw haseł (czasami nazywany resetowaniem hasła) jest wtedy, gdy administrator zastępuje hasło na koncie nowym hasłem, na przykład za pomocą narzędzia do zarządzania użytkownikami i komputerami usługi Active Directory. Ta operacja wymaga wysokiego poziomu uprawnień (zwykle administrator domeny), a osoba wykonująca operację zwykle nie ma wiedzy o starym haśle. Scenariusze pomocy technicznej często wykonują zestawy haseł, na przykład podczas udzielania pomocy użytkownikowi, który zapomniał hasła. Zdarzenia zestawu haseł będą również widoczne, gdy po raz pierwszy tworzone jest zupełnie nowe konto użytkownika za pomocą hasła.

Zasady sprawdzania poprawności hasła zachowują się tak samo niezależnie od tego, czy zmiana hasła lub zestaw jest wykonywana. Usługa agenta kontrolera kontrolera ochrony hasłem usługi Azure AD rejestruje różne zdarzenia, aby poinformować, czy operacja zmiany hasła lub zestawu została wykonana.  Zobacz [monitorowanie i rejestrowanie ochrony hasłem usługi Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-ban-bad-on-premises-monitor).

**Pyt.: Dlaczego zdarzenia odrzucenia zduplikowanego hasła są rejestrowane podczas próby ustawienia słabego hasła przy użyciu przystawki Zarządzanie użytkownikami i komputerami usługi Active Directory?**

Przystawka Zarządzanie użytkownikami i komputerami usługi Active Directory najpierw spróbuje ustawić nowe hasło przy użyciu protokołu Kerberos. Po niepowodzeniu przystawka podejmie drugą próbę ustawienia hasła przy użyciu starszego protokołu (SAM RPC) (określone protokoły nie są ważne). Jeśli nowe hasło zostanie uznane za słabe przez usługę Azure AD Password Protection, to zachowanie przystawki spowoduje, że dwa zestawy zdarzeń odrzucenia resetowania hasła są rejestrowane.

**Pyt.: Dlaczego zdarzenia sprawdzania poprawności hasła usługi Azure AD Protection są rejestrowane przy przynajdytu pustej nazwy użytkownika?**

Usługa Active Directory obsługuje możliwość testowania hasła, aby sprawdzić, czy przekazuje bieżące wymagania dotyczące złożoności hasła domeny, na przykład za pomocą interfejsu API [NetValidatePasswordPolicy.](https://docs.microsoft.com/windows/win32/api/lmaccess/nf-lmaccess-netvalidatepasswordpolicy) Gdy hasło jest sprawdzane w ten sposób, testowanie obejmuje również sprawdzanie poprawności przez produkty oparte na filtrze haseł, takie jak ochrona hasłem usługi Azure AD — ale nazwy użytkowników przekazane do danej biblioteki dll filtru haseł będą puste. W tym scenariuszu usługa Azure AD Password Protection będzie nadal sprawdzać poprawność hasła przy użyciu aktualnie obowiązujących zasad haseł i wyda komunikat dziennika zdarzeń w celu przechwycenia wyniku, jednak komunikat dziennika zdarzeń będzie miał puste pola nazwy użytkownika.

**Pyt.: Czy jest obsługiwana instalacja usługi Azure AD Password Protection obok innych produktów opartych na filtrze haseł?**

Tak. Obsługa wielu zarejestrowanych bibliotek DLL filtru haseł jest podstawową funkcją systemu Windows i nie jest specyficzna dla ochrony hasłem usługi Azure AD. Wszystkie zarejestrowane biblioteki dll filtru haseł muszą wyrazić zgodę przed zaakceptowaniem hasła.

**Pyt.: Jak wdrożyć i skonfigurować usługę Azure AD Password Protection w moim środowisku usługi Active Directory bez korzystania z platformy Azure?**

Bez pomocy technicznej. Usługa Azure AD Password Protection to funkcja platformy Azure, która obsługuje rozszerzenie na lokalne środowisko usługi Active Directory.

**Pyt.: Jak mogę zmodyfikować zawartość zasad na poziomie usługi Active Directory?**

Bez pomocy technicznej. Zasady można administrować tylko za pomocą portalu usługi Azure AD. Zobacz także poprzednie pytanie.

**Py: Dlaczego dfsr jest wymagany do replikacji sysvol?**

Usługa FRS (poprzednia technologia DFSR) ma wiele znanych problemów i jest całkowicie nieobsługiwana w nowszych wersjach usługi Windows Server Active Directory. Zero testowania ochrony hasłem usługi Azure AD zostanie wykonane w domenach skonfigurowanych przez usługę FRS.

Aby uzyskać więcej informacji, zobacz następujące artykuły:

[Przypadek migracji replikacji sysvol do DFSR](https://blogs.technet.microsoft.com/askds/2010/04/22/the-case-for-migrating-sysvol-to-dfsr)

[Koniec jest Nigh dla FRS](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs)

Jeśli twoja domena nie jest jeszcze używana jako DFSR, musisz ją przeprowadzić do migracji, aby użyć dfsr przed zainstalowaniem usługi Azure AD Password Protection. Aby uzyskać więcej informacji, zobacz następujące łącze:

[Przewodnik migracji replikacji SYSVOL: Replikacja usługi FRS do systemu plików DFS](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd640019(v=ws.10))

> [!WARNING]
> Oprogramowanie agenta kontrolera kontrolera domeny usługi Azure AD Protection zostanie obecnie zainstalowane na kontrolerach domeny w domenach, które nadal używają usługi FRS do replikacji sysvol, ale oprogramowanie NIE będzie działać poprawnie w tym środowisku. Dodatkowe negatywne skutki uboczne obejmują poszczególne pliki, które nie są replikowane, oraz procedury przywracania sysvol, które wydają się zakończyć się pomyślnie, ale po cichu nie replikują wszystkich plików. Należy przeprowadzić migrację domeny, aby jak najszybciej użyć usługi DFSR, zarówno w celu uzyskania nieodłącznych korzyści z usługi DFSR, jak i odblokowania wdrożenia usługi Azure AD Password Protection. Przyszłe wersje oprogramowania zostaną automatycznie wyłączone podczas uruchamiania w domenie, która nadal korzysta z usługi FRS.

**Py: Ile miejsca na dysku wymaga tej funkcji w udziale sysvol domeny?**

Dokładne użycie miejsca różni się, ponieważ zależy od czynników, takich jak liczba i długość zakazanych tokenów na globalnej liście zablokowanych firmy Microsoft i listy niestandardowej dla dzierżawy, a także obciążenie szyfrowania. Zawartość tych list prawdopodobnie wzrośnie w przyszłości. Mając to na uwadze, uzasadnione oczekiwania jest to, że funkcja będzie potrzebować co najmniej pięć (5) megabajtów miejsca w sysvol domeny udziału.

**P: Dlaczego do zainstalowania lub uaktualnienia oprogramowania agenta kontrolera domeny wymagany jest ponowny rozruch?**

To wymaganie jest spowodowane przez podstawowe zachowanie systemu Windows.

**Pyt.: Czy istnieje sposób skonfigurowania agenta kontrolera domeny do używania określonego serwera proxy?**

Nie. Ponieważ serwer proxy jest bezstanowy, nie jest ważne, który konkretny serwer proxy jest używany.

**Pyt.: Czy można wdrożyć usługę serwera proxy ochrony haseł usługi Azure AD obok innych usług, takich jak Usługa Azure AD Connect?**

Tak. Usługa serwera proxy ochrony hasłem usługi Azure AD i usługi Azure AD Connect nigdy nie powinny kolidować bezpośrednio ze sobą.

Niestety stwierdzono niezgodność między wersją usługi Microsoft Azure AD Connect Agent Updater, która jest zainstalowana przez oprogramowanie proxy ochrony haseł usługi Azure AD, a wersją usługi zainstalowaną przez oprogramowanie [proxy aplikacji usługi Azure Active Directory Application Proxy.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) Ta niezgodność może spowodować, że usługa Agent Updater nie może skontaktować się z platformą Azure w celu uzyskania aktualizacji oprogramowania. Nie zaleca się instalowania serwera proxy usługi Azure AD Password Protection i serwera proxy aplikacji usługi Azure Active Directory na tym samym komputerze.

**Pyt.: W jakiej kolejności należy zainstalować i zarejestrować agentów kontrolera domeny i serwerów proxy?**

Obsługiwane jest uporządkowanie instalacji agenta proxy, instalacji agenta kontrolera domeny, rejestracji lasu i rejestracji serwera proxy.

**Pyt.: Czy powinienem się martwić o wydajność trafienia na moich kontrolerach domeny z wdrażania tej funkcji?**

Usługa agenta kontrolera kontrolera domeny ochrony haseł usługi Azure AD nie powinna znacząco wpływać na wydajność kontrolera domeny w istniejącym wdrożeniu dobrej usługi Active Directory.

W przypadku większości wdrożeń usługi Active Directory operacje zmiany hasła stanowią niewielką część ogólnego obciążenia na danym kontrolerze domeny. Na przykład wyobraź sobie domenę usługi Active Directory z 10000 kont użytkowników i zasadą MaxPasswordAge ustawioną na 30 dni. Średnio ta domena będzie widzieć 10000/30 = ~ 333 operacji zmiany hasła każdego dnia, co jest niewielką liczbą operacji dla nawet jednego kontrolera domeny. Rozważmy potencjalny najgorszy scenariusz: załóżmy, że te ~333 zmiany hasła na jednym kontrolerze domeny zostały wykonane w ciągu jednej godziny. Na przykład ten scenariusz może wystąpić, gdy wielu pracowników przychodzi do pracy w poniedziałek rano. Nawet w takim przypadku nadal patrzymy na ~ 333/60 minut = sześć zmian hasła na minutę, co ponownie nie jest znaczącym obciążeniem.

Jeśli jednak bieżące kontrolery domeny są już uruchomione na poziomie o ograniczonej wydajności (na przykład maksymażu w odniesieniu do procesora CPU, miejsca na dysku, we/wy dysku itp.), przed wdrożeniem tej funkcji zaleca się dodanie dodatkowych kontrolerów domeny lub rozszerzenie dostępnego miejsca na dysku. Zobacz także pytanie powyżej o sysvol użycie miejsca na dysku powyżej.

**Pyt.: Chcę przetestować ochronę hasłem usługi Azure AD na kilku kontrolerach domeny w mojej domenie. Czy można wymusić zmiany hasła użytkownika do korzystania z tych określonych kontrolerów domeny?**

Nie. System operacyjny klienta systemu Windows kontroluje, który kontroler domeny jest używany, gdy użytkownik zmienia swoje hasło. Kontroler domeny jest wybierany na podstawie takich czynników, jak przypisania lokacji i podsieci usługi Active Directory, konfiguracja sieci specyficzne dla środowiska itp. Usługa Azure AD Password Protection nie kontroluje tych czynników i nie może mieć wpływu na to, który kontroler domeny jest wybrany do zmiany hasła użytkownika.

Jednym ze sposobów częściowego osiągnięcia tego celu byłoby wdrożenie usługi Azure AD Password Protection na wszystkich kontrolerach domeny w danej lokacji usługi Active Directory. Takie podejście zapewni rozsądne pokrycie dla klientów systemu Windows, które są przypisane do tej witryny, a zatem również dla użytkowników, którzy logują się do tych klientów i zmiany ich haseł.

**Pyt.: Jeśli zainstaluję usługę agenta kontrolera domeny ochrony haseł usługi Azure AD tylko na podstawowym kontrolerze domeny (PDC), czy wszystkie inne kontrolery domeny w domenie również będą chronione?**

Nie. Gdy hasło użytkownika zostanie zmienione na danym kontrolerze domeny nieobjętym kontrolerem domeny, hasło w postaci zwykłego tekstu nigdy nie jest wysyłane do kontrolera PDC (ten pomysł jest częstym błędem w postrzeganiu). Gdy nowe hasło zostanie zaakceptowane na danym kontrolerze domeny, ten kontroler domeny używa tego hasła do tworzenia różnych skrótów specyficznych dla protokołu uwierzytelniania tego hasła, a następnie utrzymuje te skróty w katalogu. Hasło w postaci zwykłego tekstu nie jest zachowywane. Zaktualizowane skróty są następnie replikowane do kontrolera PDC. Hasła użytkowników mogą w niektórych przypadkach być zmieniane bezpośrednio w kontrolerze PDC, ponownie w zależności od różnych czynników, takich jak topologia sieci i projektowanie witryny usługi Active Directory. (Zobacz poprzednie pytanie).

Podsumowując, wdrożenie usługi Agent kontrolera domeny ochrony haseł usługi Azure AD w kontrolerze domeny kontrolera domeny jest wymagane do osiągnięcia 100% pokrycia zabezpieczeń tej funkcji w całej domenie. Wdrażanie tej funkcji tylko w kontrolerze PDC nie zapewnia korzyści zabezpieczeń usługi Azure AD dla innych kontrolerów domeny w domenie.

**Pyt.: Dlaczego niestandardowa inteligentna blokada nie działa nawet po zainstalowaniu agentów w lokalnym środowisku usługi Active Directory?**

Niestandardowa blokada inteligentna jest obsługiwana tylko w usłudze Azure AD. Zmiany w niestandardowych ustawieniach blokady inteligentnej w portalu usługi Azure AD nie mają wpływu na lokalne środowisko usługi Active Directory, nawet przy zainstalowanych agentach.

**Pyt.: Czy pakiet administracyjny programu Operations Manager systemu System Center jest dostępny dla ochrony hasłem usługi Azure AD?**

Nie.

**Pyt.: Dlaczego usługa Azure AD nadal odrzuca słabe hasła, mimo że skonfigurowano zasady w trybie inspekcji?**

Tryb inspekcji jest obsługiwany tylko w lokalnym środowisku usługi Active Directory. Usługa Azure AD jest niejawnie zawsze w trybie "enforce", gdy ocenia hasła.

**P: Moi użytkownicy widzą tradycyjny komunikat o błędzie systemu Windows, gdy hasło zostanie odrzucone przez usługę Azure AD Password Protection. Czy można dostosować ten komunikat o błędzie, aby użytkownicy wiedzieli, co naprawdę się stało?**

Nie. Komunikat o błędzie wyświetlany przez użytkowników, gdy hasło jest odrzucane przez kontroler domeny, jest kontrolowany przez komputer kliencki, a nie przez kontroler domeny. Takie zachowanie ma miejsce, niezależnie od tego, czy hasło zostanie odrzucone przez domyślne zasady haseł usługi Active Directory, czy przez rozwiązanie oparte na filtrze haseł, takie jak ochrona hasłem usługi Azure AD.

## <a name="additional-content"></a>Dodatkowa zawartość

Poniższe łącza nie są częścią podstawowej dokumentacji usługi Azure AD Password Protection, ale mogą być użytecznym źródłem dodatkowych informacji na temat tej funkcji.

[Ochrona hasłem usługi Azure AD jest teraz ogólnie dostępna!](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-is-now-generally-available/ba-p/377487)

[Przewodnik po ochronie przed wyłudzaniem informacji pocztą e-mail — część 15: Implementowanie usługi ochrony hasłem usługi Microsoft Azure AD (także w środowisku lokalnym!)](https://blogs.technet.microsoft.com/cloudready/2018/10/14/email-phishing-protection-guide-part-15-implement-the-microsoft-azure-ad-password-protection-service-for-on-premises-too/)

[Ochrona hasłem usługi Azure AD i inteligentna blokada są teraz w publicznej wersji zapoznawczej!](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-and-Smart-Lockout-are-now-in-Public/ba-p/245423#M529)

## <a name="microsoft-premierunified-support-training-available"></a>Dostępne szkolenia pomocy technicznej microsoft premier\Unified dostępne

Jeśli chcesz dowiedzieć się więcej na temat ochrony hasłem usługi Azure AD i wdrażania go w swoim środowisku, możesz skorzystać z proaktywnej usługi firmy Microsoft dostępnej dla tych klientów z umową pomocy technicznej Premier lub Unified. Usługa nosi nazwę usługi Azure Active Directory: Password Protection. Aby uzyskać więcej informacji, skontaktuj się z menedżerem konta technicznego.

## <a name="next-steps"></a>Następne kroki

Jeśli masz lokalne pytanie dotyczące ochrony hasłem usługi Azure AD, na które nie ma odpowiedzi, prześlij poniżej element opinii poniżej — dziękuję!

[Wdrażanie ochrony haseł w usłudze Azure AD](howto-password-ban-bad-on-premises-deploy.md)
