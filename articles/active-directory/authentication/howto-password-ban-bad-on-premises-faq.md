---
title: Ochrona haseł usługi Azure AD w środowisku lokalnym — często zadawane pytania
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
ms.openlocfilehash: 4d3b0f7cdacfb781ba7925be8146c10919c5269b
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/20/2019
ms.locfileid: "56455538"
---
# <a name="preview-azure-ad-password-protection-on-premises---frequently-asked-questions"></a>Wersja zapoznawcza: Azure AD ochrony hasłem lokalnych — często zadawane pytania

|     |
| --- |
| Ochrony hasłem w usłudze Azure AD jest funkcją publicznej wersji zapoznawczej usługi Azure Active Directory. Aby uzyskać więcej informacji na temat wersji zapoznawczych, zobacz [dodatkowym warunkom użytkowania wersji zapoznawczych usług Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="general-questions"></a>Pytania ogólne

**Pyt.: Podczas ochrony haseł usługi Azure AD będzie korzystał z ogólna dostępność (GA)?**

GA planowane jest wprowadzenie Q1 CY2019 (końca marca 2019). Dziękujemy wszystkim, który dostarczył opinie na temat funkcji Data — Jesteśmy wdzięczni za jej!

**Pyt.: Jakie wytyczne użytkowników należy zwrócić na sposób wybierania bezpieczne hasło?**

Firmy Microsoft Bieżące wskazówki na ten temat można znaleźć pod następujący link:

[Wskazówki dotyczące hasła Microsoft](https://www.microsoft.com/en-us/research/publication/password-guidance)

**Pyt.: Czy w środowisku lokalnym ochrony haseł usługi Azure AD w chmurach bez publicznego obsługiwane?**

Nie — ochrony haseł usługi Azure AD w środowisku lokalnym jest obsługiwana tylko w chmurze publicznej. Data nie zostało zapowiedziane na dostępności w chmurze bez publicznego.

**Pyt.: Jak zastosować korzyści z ochrony haseł usługi Azure AD do podzbioru użytkowników w środowisku lokalnym?**

Nieobsługiwane. Po wdrożeniu i włączone, nie rozróżniania ochrony haseł usługi Azure AD — wszyscy użytkownicy otrzymują korzyści równy zabezpieczeń.

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

## <a name="additional-content"></a>Dodatkowa zawartość

Następujące łącza nie są częścią podstawowej dokumentacji ochrony haseł usługi Azure AD, ale mogą być przydatne źródło dodatkowe informacje na temat tej funkcji.

[Wiadomości e-mail przewodnik dotyczący ochrony wyłudzania informacji — część 15: Implementowanie usługi ochrony hasła usługi Microsoft Azure AD (dla lokalnego zbyt!)](https://blogs.technet.microsoft.com/cloudready/2018/10/14/email-phishing-protection-guide-part-15-implement-the-microsoft-azure-ad-password-protection-service-for-on-premises-too/)

[Usługa Azure AD ochrony hasłem i inteligentnej blokady są teraz w publicznej wersji zapoznawczej!](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-and-Smart-Lockout-are-now-in-Public/ba-p/245423#M529)

## <a name="microsoft-premierunified-support-training-available"></a>Szkolenia pomocy technicznej firmy Microsoft Premier\Unified dostępne

Jeśli interesuje Cię Dowiedz się więcej na temat ochrony haseł usługi Azure AD i wdrażania jej w środowisku, możesz korzystać z zalet usługi proaktywne firmy Microsoft dostępnych do klientów korzystających z umowę pomocy technicznej Premier lub Unified. Usługa jest wywoływana w usłudze Azure Active Directory: Ochrona za pomocą hasła. Aby uzyskać więcej informacji, skontaktuj się z menedżerem technicznych.

## <a name="next-steps"></a>Kolejne kroki

Jeśli masz pytanie ochrony haseł usługi Azure AD w środowisku lokalnym, który nie ma tutaj odpowiedzi, Prześlij elementu opinii poniżej. Dziękujemy!

[Wdrażanie ochrony haseł w usłudze Azure AD](howto-password-ban-bad-on-premises-deploy.md)
