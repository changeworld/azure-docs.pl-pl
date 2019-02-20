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
ms.openlocfilehash: 8b29e4f6e6e877a3d03ded9edd9e282e0960e177
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/19/2019
ms.locfileid: "56414637"
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

Nieobsługiwane. Po wdrożeniu i włączony, ochrony haseł usługi Azure AD nie rozróżniania — wszyscy użytkownicy otrzymują korzyści równy zabezpieczeń.

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

Użycie dokładne miejsca nie można dokładnie określić, ponieważ zależy od czynników, takich jak liczby i długości tokenów zakazanych haseł, na liście globalnej zakazanych haseł firmy Microsoft, a niestandardowe dla dzierżawcy zakazane lista hasła, a także koszty szyfrowania. Zawartość tych list prawdopodobnie może rosnąć w przyszłości. Mając to na uwadze uzasadnione bieżącego zasadą jest, funkcji, należy co najmniej pięciu (5) w megabajtach ilość miejsca na udział sysvol domeny.

**Pyt.: Dlaczego jest wymagane ponowne uruchomienie Zainstaluj lub Uaktualnij oprogramowanie agenta kontrolera domeny?**

To wymaganie jest spowodowany przez podstawowe zachowanie Windows.

**Pyt.: Czy istnieje sposób można skonfigurować agenta kontrolera domeny, aby użyć konkretnego serwera proxy?**

Nie. Należy pamiętać, że ponieważ serwer proxy jest bezstanowy, ważne jest, które konkretnego serwera proxy jest używany.

**Pyt.: Czy jest niezbędna do wdrożenia serwera Proxy ochrony haseł usługi Azure AD usługi side-by-side na komputerze z innymi usługami, takimi jak program Azure AD Connect?**

Tak. Usługa serwera Proxy ochrony haseł usługi Azure AD i Azure AD Connect powinno nigdy nie są w konflikcie ze sobą bezpośrednio.

**Pyt.: Czy mogę być dane dotyczące wydajności dla moich kontrolerów domeny z wdrażanie tej funkcji?**

Usługa agenta DC ochronę haseł usługi Azure AD została zaprojektowana jako najbardziej efektywne i nie powinna znacznie wpływu na wydajność kontrolera domeny w istniejącego wdrożenia usługi Active Directory, która jest już odpowiednio planować.

Jest to przydatne do należy zauważyć, że dla większości hasła wdrożeń usługi Active Directory operacji zmian niewielką część całkowitego obciążenia na żadnym kontrolerze danej domeny. Na przykład Wyobraź sobie domeny usługi Active Directory z kontami użytkowników 10000 i zasad MaxPasswordAge ustawiona na 30 dni. Średnio tej domeny zostanie wyświetlony 10000/30 = operacji zmiany hasła ~ 333 każdego dnia, która jest pomocnicza liczby operacji dla nawet jednego kontrolera domeny. Wypychanie z przykładu, aby potencjalnych scenariusz, załóżmy, że te 333 ~ zmiany haseł na jednym kontrolerze domeny zostały wykonane w ciągu jednej godziny (na przykład, w tym scenariuszu może wystąpić w przypadku dużej liczby pracowników wszystkich dostarczane do pracy w poniedziałek rano); nawet w takim przypadku nadal chcemy minut ~333/60 = sześć zmiany haseł na minutę, czyli ponownie nie znaczne obciążenie.

Dzięki temu powiedział Jeśli bieżące kontrolerów domeny zostały już uruchomione na poziomach wydajności limited (na przykład maksymalnego limitu względem procesora CPU, miejsca na dysku, we/wy dysku, itp.), byłoby dobrze jest wdrażanie dodatkowych kontrolerów domeny i/lub rozwiń dostępnego miejsca na dysku Odstęp przed wdrożeniem ochrony haseł usługi Azure AD. Zobacz też pytania powyżej dotyczące powyższych użycie miejsca na dysku folderu sysvol.

**Pyt.: Czy chcesz przetestować ochronę haseł usługi Azure AD na kilka kontrolerów domeny w mojej domeny. Czy jest możliwe do wymuszenia zmiany haseł użytkowników, aby użyć tych określone kontrolery domeny?**

Nie. Jeśli użytkownik zmieni swoje hasło, kontroler domeny, który jest używany jest wybierany przez system operacyjny Windows klienta na podstawie czynników, takich jak przypisania lokacji i podsieci usługi Active Directory, konfiguracji specyficznych dla środowiska sieci itp. Ochrony hasłem w usłudze Azure AD nie wpływa na lub kontrolować te czynniki i w związku z tym nie może mieć wpływ na kontroler domeny został wybrany do zmiany hasła użytkownika.

Dzięki temu powiedział metody, która jest blisko symulowania ten cel jest Wdrażanie ochrony haseł usługi Azure AD na wszystkich kontrolerach domeny w danej lokacji usługi Active Directory. To podejście zapewni rozsądnie wszechobecne pokrycia dla klientów Windows przypisany do tej lokacji i w związku z tym również dla użytkowników, które są logując się do tych klientów i zmienianie swoich haseł.

**Pyt.: Jeśli zainstaluję usługę agenta ochrony kontrolera domeny haseł usługi Azure AD na tylko podstawowego kontrolera domeny (PDC), będą inne kontrolery domeny w domenie również chronione?**

Nie. Po zmianie hasła użytkownika na kontrolerze domeny danego bez PDC hasła w postaci zwykłego tekstu nigdy nie są wysyłane do kontrolera PDC (ten pomysł to typowe wrażenie niewłaściwa). Po zaakceptowaniu nowe hasło na danym kontrolerze domeny kontrolera domeny używa tego hasła w celu utworzenia różne skróty uwierzytelniania protokołu specyficzne dla tego hasła i następnie będzie się powtarzać te skróty w katalogu. Te zaktualizować skróty są następnie replikowane do podstawowego kontrolera domeny — ale ochrony haseł usługi Azure AD nie jest zaangażowana w tym procesie. Hasła użytkowników może w niektórych przypadkach można zmienić bezpośrednio na podstawowym kontrolerze domeny, ponownie w zależności od różnych czynników, takich jak topologii sieci i projektu lokacji usługi Active Directory. (Zobacz poprzedniego pytania).

Podsumowując: wdrożenie usługi hasło ochrony kontrolera domeny agenta programu Azure AD na podstawowym kontrolerze domeny jest wymagany do osiągnięcia 100% pokrycia zabezpieczeń funkcji, ale która sama nie zapewnia korzyści w zakresie zabezpieczeń ochrony haseł usługi Azure AD dla innych kontrolerów domeny w domenie.

**Pyt.: Jest pakiet administracyjny programu System Center Operations Manager (SCOM) dla ochrony haseł usługi Azure AD?**

Nie.

## <a name="additional-content"></a>Dodatkowa zawartość

Następujące łącza nie są częścią podstawowej dokumentacji ochrony haseł usługi Azure AD, ale mogą być przydatne źródło dodatkowe informacje na temat tej funkcji.

[Wiadomości e-mail przewodnik dotyczący ochrony wyłudzania informacji — część 15: Implementowanie usługi ochrony hasła usługi Microsoft Azure AD (dla lokalnego zbyt!)](https://blogs.technet.microsoft.com/cloudready/2018/10/14/email-phishing-protection-guide-part-15-implement-the-microsoft-azure-ad-password-protection-service-for-on-premises-too/)

[Usługa Azure AD ochrony hasłem i inteligentnej blokady są teraz w publicznej wersji zapoznawczej!](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-and-Smart-Lockout-are-now-in-Public/ba-p/245423#M529)

## <a name="next-steps"></a>Kolejne kroki

Jeśli masz pytanie ochrony haseł usługi Azure AD w środowisku lokalnym, który nie ma tutaj odpowiedzi, Prześlij elementu opinii poniżej. Dziękujemy!

[Wdrażanie ochrony haseł w usłudze Azure AD](howto-password-ban-bad-on-premises-deploy.md)
