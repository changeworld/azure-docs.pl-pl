---
title: Zabezpiecz swoją infrastrukturę tożsamości usługi Azure AD
titleSuffix: Azure Active Directory
description: W tym dokumencie przedstawiono listę ważnych akcji, które administratorzy powinni zaimplementować, aby pomóc im zabezpieczyć swoją organizację przy użyciu funkcji usługi Azure AD
author: martincoetzer
manager: manmeetb
tags: azuread
ms.service: security
ms.subservice: security-fundamentals
ms.topic: conceptual
ms.workload: identity
ms.date: 01/29/2020
ms.author: martinco
ms.openlocfilehash: e0db8edfdfa380697a1d8d7e262a7a84da2fb7d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77565540"
---
# <a name="five-steps-to-securing-your-identity-infrastructure"></a>Pięć kroków do zabezpieczenia infrastruktury tożsamości

Jeśli czytasz ten dokument, zdajesz sobie sprawę z znaczenia zabezpieczeń. Prawdopodobnie ponosisz już odpowiedzialność za zabezpieczenie swojej organizacji. Jeśli chcesz przekonać innych o znaczeniu bezpieczeństwa, wyślij je do przeczytania najnowszego [raportu Microsoft Security Intelligence](https://go.microsoft.com/fwlink/p/?linkid=2073747).

Ten dokument pomoże Ci uzyskać bezpieczniejszą postawę przy użyciu możliwości usługi Azure Active Directory przy użyciu pięcioetapowej listy kontrolnej, aby zaszczepić organizację przed atakami cybernetycznymi.

Ta lista kontrolna pomoże Ci szybko wdrożyć krytyczne zalecane akcje w celu ochrony organizacji natychmiast, wyjaśniając, jak:

* Wzmocnij swoje poświadczenia.
* Zmniejsz powierzchnię ataku.
* Automatyzacja reagowania na zagrożenia.
* Korzystaj z analizy chmury.
* Włącz samoobsługę użytkownika końcowego.

Podczas czytania tej listy kontrolnej należy śledzić, które funkcje i kroki zostały ukończone.

> [!NOTE]
> Wiele zaleceń zawartych w tym dokumencie dotyczy tylko aplikacji skonfigurowanych do używania usługi Azure Active Directory jako dostawcy tożsamości. Konfigurowanie aplikacji do logowania jednokrotnego zapewnia korzyści wynikające z zasad poświadczeń, wykrywania zagrożeń, inspekcji, rejestrowania i innych funkcji dodawanych do tych aplikacji. [Logowanie jednokrotne za pośrednictwem usługi Azure Active Directory](../../active-directory/manage-apps/configure-single-sign-on-non-gallery-applications.md) jest podstawą — na której opierają się wszystkie te zalecenia.

Zalecenia zawarte w tym dokumencie są zgodne z [wynikiem bezpiecznego tożsamości](../../active-directory/fundamentals/identity-secure-score.md), automatyczną oceną konfiguracji zabezpieczeń tożsamości dzierżawy usługi Azure AD. Organizacje mogą używać strony Bezpieczny wynik tożsamości w portalu usługi Azure AD, aby znaleźć luki w bieżącej konfiguracji zabezpieczeń, aby upewnić się, że przestrzegają [aktualnych najlepszych rozwiązań firmy](identity-management-best-practices.md) Microsoft dotyczących zabezpieczeń. Wdrożenie każdego zalecenia na stronie Bezpieczny wynik zwiększy twój wynik i pozwoli ci śledzić postępy, a także pomoże ci porównać implementację z innymi organizacjami o podobnej wielkości lub branżą.

![Bezpieczny wynik tożsamości](./media/steps-secure-identity/azure-ad-sec-steps0.png)

> [!NOTE]
> Wiele funkcji opisanych w tym miejscu wymaga subskrypcji usługi Azure AD Premium, podczas gdy niektóre są bezpłatne. Aby uzyskać więcej informacji, zapoznaj [się z cennikami usługi Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/) i [listą kontrolną wdrażania usługi Azure AD.](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-checklist-p2)

## <a name="before-you-begin-protect-privileged-accounts-with-mfa"></a>Przed rozpoczęciem: Ochrona kont uprzywilejowanych za pomocą usługi MFA

Przed rozpoczęciem tej listy kontrolnej upewnij się, że podczas czytania tej listy kontrolnej nie zostaniesz naruszony. Najpierw musisz chronić swoje konta uprzywilejowane.

Atakujący, którzy mają kontrolę nad uprzywilejowanymi kontami, mogą wyrządzić ogromne szkody, dlatego bardzo ważne jest, aby najpierw chronić te konta. Włącz i wymagaj [uwierzytelniania wieloskładnikowego](../../active-directory/authentication/multi-factor-authentication.md) platformy Azure dla wszystkich administratorów w organizacji przy użyciu [ustawień domyślnych zabezpieczeń usługi Azure AD](../../active-directory/fundamentals/concept-fundamentals-security-defaults.md) lub dostępu [warunkowego.](../../active-directory/conditional-access/plan-conditional-access.md) Jeśli nie wdrożyłeś usługi MFA, zrób to teraz! To ważne.

Wszystko ustawione? Zacznijmy od listy kontrolnej.

## <a name="step-1---strengthen-your-credentials"></a>Krok 1 - Wzmocnij swoje poświadczenia

Większość naruszeń zabezpieczeń przedsiębiorstwa wynika z naruszenia konta za pomocą jednej z kilku metod, takich jak rozpylanie hasła, powtarzanie naruszeń lub wyłudzanie informacji. Dowiedz się więcej o tych atakach w tym filmie (45 min):
> [!VIDEO https://www.youtube.com/embed/uy0j1_t5Hd4]

### <a name="make-sure-your-organization-uses-strong-authentication"></a>Upewnij się, że organizacja używa silnego uwierzytelniania

Biorąc pod uwagę częstotliwość haseł odgadywanych, wyłudzanych, skradzionych ze złośliwym oprogramowaniem lub ponownie, ważne jest, aby utworzyć hasło z jakąś formą silnych poświadczeń - dowiedz się więcej o [usłudze Azure Multi-Factor Authentication.](../../active-directory/authentication/multi-factor-authentication.md)

Aby łatwo włączyć podstawowy poziom zabezpieczeń tożsamości, można użyć włączenia jednym kliknięciem z [ustawieniami domyślnymi zabezpieczeń usługi Azure AD](../../active-directory/fundamentals/concept-fundamentals-security-defaults.md). Domyślne zabezpieczenia wymuszają usługę Azure MFA dla wszystkich użytkowników w dzierżawie i blokują logowania z starszych protokołów w całej dzierżawie.

### <a name="start-banning-commonly-attacked-passwords-and-turn-off-traditional-complexity-and-expiration-rules"></a>Zacznij blokować często atakowane hasła i wyłącz tradycyjną złożoność i reguły wygasania.

Wiele organizacji używa tradycyjnych reguł złożoności (wymagających znaków specjalnych, liczb, wielkich i małych liter) i reguł wygasania haseł. [Badania firmy Microsoft wykazały,](https://aka.ms/passwordguidance) że te zasady powodują, że użytkownicy wybierają hasła, które są łatwiejsze do odgadnięcia.

Funkcja [dynamicznego hasła zbanowanym programem](../../active-directory/authentication/concept-password-ban-bad.md) Azure AD używa bieżącego zachowania osoby atakującej, aby uniemożliwić użytkownikom ustawianie haseł, które można łatwo odgadnąć. Ta funkcja jest zawsze waktywna, gdy użytkownicy są tworzoni w chmurze, ale jest teraz dostępna również dla organizacji hybrydowych podczas wdrażania [ochrony hasłem usługi Azure AD dla usługi Windows Server Active Directory](../../active-directory/authentication/concept-password-ban-bad-on-premises.md). Ochrona hasłem usługi Azure AD blokuje użytkownikom wybór tych typowych haseł i można ją rozszerzyć, aby zablokować hasło zawierające określone niestandardowe słowa kluczowe. Można na przykład uniemożliwić użytkownikom wybieranie haseł zawierających nazwy produktów firmy lub lokalną drużynę sportową.

Firma Microsoft zaleca przyjęcie następujących nowoczesnych zasad haseł opartych na [wskazówkach NIST:](https://pages.nist.gov/800-63-3/sp800-63b.html)

1. Wymagaj, aby hasła miały co najmniej 8 znaków. Dłuższe nie jest koniecznie lepsze, ponieważ powodują, że użytkownicy wybierają przewidywalne hasła, zapisują hasła w plikach lub zapisują je.
2. Wyłącz reguły wygasania, które napędzają użytkowników do łatwego odgadnięcia haseł, takich jak **Wiosna 2019!**
3. Wyłącz wymagania dotyczące kompozycji znaków i uniemożliwić użytkownikom wybieranie często zaatakowanych haseł, ponieważ powodują one, że użytkownicy wybierają przewidywalne podstawienia znaków w hasłach.

Program [PowerShell](../../active-directory/authentication/concept-sspr-policy.md) służy do zapobiegania wygasania haseł dla użytkowników, jeśli tworzysz tożsamości w usłudze Azure AD bezpośrednio. Organizacje hybrydowe powinny zaimplementować te zasady przy użyciu [ustawień zasad grupy domeny](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/hh994572(v%3dws.10)) lub programu Windows [PowerShell](https://docs.microsoft.com/powershell/module/addsadministration/set-addefaultdomainpasswordpolicy).

### <a name="protect-against-leaked-credentials-and-add-resilience-against-outages"></a>Ochrona przed wyciekiem poświadczeń i zwiększanie odporności na awarie

Jeśli organizacja używa rozwiązania tożsamości hybrydowej z uwierzytelnianiem przekazu lub federacją, należy włączyć synchronizację skrótów haseł z następujących dwóch powodów:

* [Użytkownicy z raportem wyciekań poświadczeń](../../active-directory/reports-monitoring/concept-risk-events.md) w zarządzania usługą Azure AD ostrzega o parach nazw użytkowników i haseł, które zostały ujawnione w "ciemnej sieci Web". Niesamowita ilość haseł wycieka przez phishing, złośliwe oprogramowanie i ponowne użycie hasła w witrynach innych firm, które zostały później naruszone. Microsoft znajdzie wiele z tych wyciekły poświadczenia i powie, w tym raporcie, jeśli pasują do poświadczeń w organizacji - ale tylko wtedy, gdy [włączysz synchronizację hash hasła!](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)
* W przypadku awarii lokalnej (na przykład w ataku ransomware) można przełączyć się na korzystanie z [uwierzytelniania w chmurze przy użyciu synchronizacji hash hasła](choose-ad-authn.md). Ta metoda uwierzytelniania kopii zapasowej umożliwia kontynuowanie uzyskiwania dostępu do aplikacji skonfigurowanych do uwierzytelniania za pomocą usługi Azure Active Directory, w tym usługi Office 365. W takim przypadku pracownicy IT nie będą musieli uciekać się do osobistych kont e-mail, aby udostępniać dane, dopóki awaria lokalna nie zostanie rozwiązana.

Dowiedz się więcej o tym, jak działa [synchronizacja skrótów haseł.](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)

> [!NOTE]
> Jeśli włączysz synchronizację skrótów haseł i korzystasz z usług domeny usługi Azure AD, skróty protokołu Kerberos (AES 256) i opcjonalnie skróty NTLM (RC4 bez soli) będą również szyfrowane i synchronizowane z usługą Azure AD.

### <a name="implement-ad-fs-extranet-smart-lockout"></a>Implementowanie inteligentnej blokady ekstranetu usług AD FS

Organizacje, które konfigurują aplikacje do uwierzytelniania bezpośrednio do usługi Azure AD korzystają z [inteligentnej blokady usługi Azure AD.](../../active-directory/authentication/concept-sspr-howitworks.md) Jeśli używasz usług AD FS w systemie Windows Server 2012R2, zaimplementuj [ochronę przed blokadą ernet](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection)y. Jeśli używasz usług AD FS w systemie Windows Server 2016, zaimplementuj [inteligentną blokadę ekstranetu](https://support.microsoft.com/help/4096478/extranet-smart-lockout-feature-in-windows-server-2016). Blokada inteligentnej ekstranetu usług AD FS chroni przed atakami siłowymi, które są ukierunkowane na usługi AD FS, jednocześnie uniemożliwiając zablokowanie użytkowników w usłudze Active Directory.

### <a name="take-advantage-of-intrinsically-secure-easier-to-use-credentials"></a>Korzystaj z bezpiecznych, łatwiejszych w użyciu poświadczeń

Korzystając z [funkcji Windows Hello,](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)można zastąpić hasła silnym uwierzytelnianiem dwuskładnikowym na komputerach i urządzeniach przenośnych. To uwierzytelnianie składa się z nowego typu poświadczeń użytkownika, który jest bezpiecznie powiązany z urządzeniem i używa numeru BIOMETRYCZNEGO lub PIN.

## <a name="step-2---reduce-your-attack-surface"></a>Krok 2 - Zmniejsz powierzchnię ataku

Biorąc pod uwagę wszechobecność naruszenia hasła, minimalizacja powierzchni ataku w organizacji ma kluczowe znaczenie. Wyeliminowanie użycia starszych, mniej bezpiecznych protokołów, ograniczenie punktów dostępu i korzystanie z bardziej znaczącej kontroli dostępu administracyjnego do zasobów może pomóc zmniejszyć obszar ataku.

### <a name="block-legacy-authentication"></a>Blokowanie starszego uwierzytelniania

Aplikacje używające własnych starszych metod do uwierzytelniania za pomocą usługi Azure AD i uzyskiwania dostępu do danych firmy stanowią inne zagrożenie dla organizacji. Przykładami aplikacji korzystających ze starszego uwierzytelniania są klienci POP3, IMAP4 lub SMTP. Starsze aplikacje uwierzytelniania uwierzytelniają się w imieniu użytkownika i uniemożliwiają usłudze Azure AD wykonywanie zaawansowanych ocen zabezpieczeń. Alternatywne, nowoczesne uwierzytelnianie zmniejszy ryzyko związane z zabezpieczeniami, ponieważ obsługuje uwierzytelnianie wieloskładnikowe i dostęp warunkowy. Zalecamy następujące trzy akcje:

1. Blokowanie [uwierzytelniania starszego w przypadku korzystania z usług AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/access-control-policies-w2k12).
2. Instaluj [usługi SharePoint Online i Exchange Online w celu korzystania z nowoczesnego uwierzytelniania](../../active-directory/conditional-access/conditional-access-for-exo-and-spo.md).
3. Jeśli masz usługę Azure AD Premium, użyj [zasad dostępu warunkowego,](../../active-directory/conditional-access/overview.md) aby zablokować starsze uwierzytelnianie, w przeciwnym razie użyj [ustawień domyślnych zabezpieczeń usługi Azure AD.](../../active-directory/fundamentals/concept-fundamentals-security-defaults.md)

### <a name="block-invalid-authentication-entry-points"></a>Blokowanie nieprawidłowych punktów wejścia uwierzytelniania

Przy użyciu mentalność zakładać naruszenie, należy zmniejszyć wpływ poświadczeń użytkownika, którego bezpieczeństwo zostało naruszone, gdy się zdarzy. Dla każdej aplikacji w środowisku należy wziąć pod uwagę prawidłowe przypadki użycia: które grupy, które sieci, które urządzenia i inne elementy są autoryzowane — a następnie zablokować resztę. Za [pomocą usługi Azure AD Dostęp warunkowy](../../active-directory/conditional-access/overview.md)można kontrolować sposób uzyskiwania dostępu autoryzowanych użytkowników do swoich aplikacji i zasobów na podstawie określonych warunków, które definiujesz.

### <a name="restrict-user-consent-operations"></a>Ograniczanie operacji zgody użytkownika

Ważne jest, aby zrozumieć różne [środowiska zgody aplikacji usługi Azure AD,](https://docs.microsoft.com/azure/active-directory/develop/application-consent-experience) [typy uprawnień i zgody](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent)oraz ich wpływ na poziom zabezpieczeń organizacji. Domyślnie wszyscy użytkownicy usługi Azure AD mogą udzielać aplikacji korzystających z platformy tożsamości firmy Microsoft w celu uzyskania dostępu do danych organizacji. Zezwalając użytkownikom na wyrażenie zgody przez siebie pozwala użytkownikom łatwo nabyć przydatne aplikacje, które integrują się z usługami Microsoft 365, Azure i innymi usługami, może stanowić ryzyko, jeśli nie są używane i monitorowane starannie.

Firma Microsoft zaleca [wyłączenie przyszłych operacji zgody użytkownika,](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-removing-user-access#i-want-to-disable-all-future-user-consent-operations-to-any-application) aby zmniejszyć powierzchnię i ograniczyć to ryzyko. Jeśli zgoda użytkownika końcowego jest wyłączona, wcześniejsze dotacje zgody będą nadal honorowane, ale wszystkie przyszłe operacje zgody muszą być wykonywane przez administratora. Użytkownicy mogą zażądać zgody administratora za pośrednictwem [przepływu pracy żądania zgody zintegrowanego administratora](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-admin-consent-workflow) lub za pośrednictwem własnych procesów pomocy technicznej. Przed wyłączeniem zgody użytkownika końcowego skorzystaj z naszych [zaleceń,](https://docs.microsoft.com/azure/active-directory/manage-apps/manage-consent-requests) aby zaplanować tę zmianę w organizacji. W przypadku aplikacji, do których chcesz zezwolić wszystkim użytkownikom na dostęp, rozważ [udzielenie zgody w imieniu wszystkich użytkowników,](https://docs.microsoft.com/azure/active-directory/develop/v2-admin-consent)upewniając się, że użytkownicy, którzy nie wyrazili jeszcze zgody, będą mogli uzyskać dostęp do aplikacji. Jeśli nie chcesz, aby te aplikacje były dostępne dla wszystkich użytkowników we wszystkich scenariuszach, użyj [przypisania aplikacji](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-assigning-users-and-groups) i [dostępu warunkowego,](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) aby ograniczyć dostęp użytkowników do aplikacji.

Upewnij się, że użytkownicy mogą żądać zatwierdzenia przez administratorów dla nowych aplikacji, aby zmniejszyć tarcie użytkowników, zminimalizować liczbę woluminów pomocy technicznej i uniemożliwić użytkownikom rejestrowanie się w aplikacjach przy użyciu poświadczeń usługi AD innych niż Azure. Po uregulowaniu operacji zgody administratorzy powinni regularnie przeprowadzać inspekcje aplikacji i uprawnienia, na które udzielono zgody.


### <a name="implement-azure-ad-privileged-identity-management"></a>Implementowanie zarządzania tożsamościami uprzywilejowanymi usługi Azure AD

Innym skutkiem "zakładaj naruszenie" jest konieczność zminimalizowania prawdopodobieństwa, że konto, którego bezpieczeństwo zostało naruszone, może działać z uprzywilejowaną rolą.

[Usługa Azure AD Privileged Identity Management (PIM)](../../active-directory/privileged-identity-management/pim-configure.md) ułatwia zminimalizowanie uprawnień konta, pomagając:

* Identyfikowanie użytkowników przypisanych do ról administracyjnych i zarządzanie nimi.
* Opis ról nieużytkowanych lub nadmiernych uprawnień, które należy usunąć.
* Ustal reguły, aby upewnić się, że role uprzywilejowane są chronione przez uwierzytelnianie wieloskładnikowe.
* Ustal reguły, aby upewnić się, że role uprzywilejowane są przyznawane tylko wystarczająco długo, aby wykonać zadanie uprzywilejowane.

Włącz usługę Azure AD PIM, a następnie wyświetl użytkowników, którym przypisano role administracyjne, i usuń niepotrzebne konta w tych rolach. W przypadku pozostałych uprzywilejowanych użytkowników przenieś ich ze stałych do kwalifikujących się. Na koniec należy ustanowić odpowiednie zasady, aby upewnić się, kiedy trzeba uzyskać dostęp do tych ról uprzywilejowanych, mogą to zrobić bezpiecznie, z kontrolą zmian niezbędnych.

W ramach wdrażania procesu konta uprzywilejowanego postępuj zgodnie z [najlepszymi rozwiązaniami, aby utworzyć co najmniej dwa konta awaryjne,](../../active-directory/users-groups-roles/directory-admin-roles-secure.md) aby upewnić się, że nadal masz dostęp do usługi Azure AD, jeśli zablokujesz się.

## <a name="step-3---automate-threat-response"></a>Krok 3 - Automatyzacja reagowania na zagrożenia

Usługa Azure Active Directory ma wiele możliwości, które automatycznie przechwytują ataki, aby usunąć opóźnienie między wykrywaniem a odpowiedzią. Możesz zmniejszyć koszty i ryzyko, gdy skrócisz czas, w którym przestępcy używają do osadzania się w twoim środowisku. Oto konkretne kroki, które możesz podjąć.

### <a name="implement-user-risk-security-policy-using-azure-ad-identity-protection"></a>Implementowanie zasad zabezpieczeń ryzyka użytkownika przy użyciu usługi Azure AD Identity Protection

Ryzyko użytkownika wskazuje prawdopodobieństwo naruszenia tożsamości użytkownika i jest obliczane na podstawie [wykrywania ryzyka użytkownika,](../../active-directory/identity-protection/overview.md) które są skojarzone z tożsamością użytkownika. Zasady ryzyka użytkownika to zasady dostępu warunkowego, które obliczają poziom ryzyka dla określonego użytkownika lub grupy. Na podstawie niskiego, średniego i wysokiego poziomu ryzyka można skonfigurować zasady blokujące dostęp lub wymagające bezpiecznej zmiany hasła przy użyciu uwierzytelniania wieloskładnikowego. Zaleca się, aby firma Microsoft wymagała bezpiecznej zmiany hasła dla użytkowników wysokiego ryzyka.

![Użytkownicy oflagowani w związku z ryzykiem](./media/steps-secure-identity/azure-ad-sec-steps1.png)

### <a name="implement-sign-in-risk-policy-using-azure-ad-identity-protection"></a>Implementowanie zasad ryzyka logowania przy użyciu usługi Azure AD Identity Protection

Ryzyko logowania to prawdopodobieństwo, że ktoś inny niż właściciel konta próbuje zalogować się przy użyciu tożsamości. [Zasady ryzyka logowania](../../active-directory/identity-protection/overview.md) to zasady dostępu warunkowego, które oceniają poziom ryzyka dla określonego użytkownika lub grupy. Na podstawie poziomu ryzyka (wysoki/średni/niski) można skonfigurować zasady blokujące dostęp lub wymuszanie uwierzytelniania wieloskładnikowego. Upewnij się, że wymusisz uwierzytelnianie wieloskładnikowe na średnich lub wyższych logach ryzyka.

![Logowanie się za cień anonimowych adresów IP](./media/steps-secure-identity/azure-ad-sec-steps2.png)

## <a name="step-4---utilize-cloud-intelligence"></a>Krok 4 - Wykorzystaj inteligencję chmury

Inspekcja i rejestrowanie zdarzeń związanych z zabezpieczeniami i powiązanych alertów są istotnymi elementami skutecznej strategii ochrony. Dzienniki zabezpieczeń i raporty zapewniają elektroniczny rejestr podejrzanych działań i pomagają wykrywać wzorce, które mogą wskazywać na próbę lub pomyślną penetrację zewnętrzną sieci oraz ataki wewnętrzne. Inspekcję można używać do monitorowania aktywności użytkowników, dokumentowania zgodności z przepisami, przeprowadzania analiz kryminalistycznych i innych czynności. Alerty zapewniają powiadomienia o zdarzeniach zabezpieczeń.

### <a name="monitor-azure-ad"></a>Monitorowanie usługi Azure AD

Usługi i funkcje platformy Microsoft Azure zapewniają konfigurowalne opcje inspekcji zabezpieczeń i rejestrowania, które ułatwiają identyfikowanie luk w zasadach i mechanizmach zabezpieczeń oraz elizkiwanie tych luk w celu zapobiegania naruszeniom zabezpieczeń. Możesz użyć [rejestrowania i inspekcji platformy Azure](log-audit.md) oraz używać [raportów aktywności inspekcji w portalu usługi Azure Active Directory.](../../active-directory/reports-monitoring/concept-audit-logs.md)

### <a name="monitor-azure-ad-connect-health-in-hybrid-environments"></a>Monitorowanie kondycji usługi Azure AD Connect w środowiskach hybrydowych

[Monitorowanie usług AD FS za pomocą usługi Azure AD Connect Health](../../active-directory/hybrid/how-to-connect-health-adfs.md) zapewnia lepszy wgląd w potencjalne problemy i wgląd w ataki na infrastrukturę usług AD FS. Usługa Azure AD Connect Health dostarcza alerty ze szczegółami, krokami rozwiązywania problemów i łączami do powiązanej dokumentacji; analiza użycia dla kilku metryk związanych z ruchem uwierzytelniania; monitorowania wydajności i sprawozdań.

![Azure AD Connect Health](./media/steps-secure-identity/azure-ad-sec-steps4.png)

### <a name="monitor-azure-ad-identity-protection-events"></a>Monitorowanie zdarzeń usługi Azure AD Identity Protection

[Usługa Azure AD Identity Protection](../../active-directory/identity-protection/overview.md) to narzędzie do powiadamiania, monitorowania i raportowania, którego można użyć do wykrywania potencjalnych luk w zabezpieczeniach mających wpływ na tożsamość organizacji. Wykrywa wykrywanie ryzyka, takie jak wyciekające poświadczenia, niemożliwe podróże i logowania z zainfekowanych urządzeń, anonimowe adresy IP, adresy IP skojarzone z podejrzaną aktywnością i nieznane lokalizacje. Włącz alerty powiadomień, aby otrzymywać wiadomości e-mail od użytkowników zagrożonych i/lub cotygodniową wiadomość e-mail z podsumowaniem.

Usługa Azure AD Identity Protection udostępnia dwa ważne raporty, które należy monitorować codziennie:
1. Ryzykowne raporty logowania będą wyeksliwać działania logowania użytkownika, które należy zbadać, prawowity właściciel może nie wykonać logowania.
2. Ryzykowne raporty użytkowników będą powierzchni kont użytkowników, które mogły zostać naruszone, takich jak wyciek poświadczeń, który został wykryty lub użytkownik zalogowany z różnych lokalizacji powodując niemożliwe zdarzenie podróży.

![Użytkownicy oflagowani w związku z ryzykiem](./media/steps-secure-identity/azure-ad-sec-steps3.png)

### <a name="audit-apps-and-consented-permissions"></a>Inspekcja aplikacji i uprawnień, na które udzielono zgody

Użytkownicy mogą zostać nakłonieni do przejścia do witryny sieci Web lub aplikacji, które uzyskają dostęp do informacji o profilu i danych użytkownika, takich jak wiadomości e-mail. Złośliwy aktor może użyć uprawnień, które otrzymał, do szyfrowania zawartości skrzynki pocztowej i żądania okupu w celu odzyskania danych skrzynki pocztowej. [Administratorzy powinni przeglądać i kontrolować](https://docs.microsoft.com/office365/securitycompliance/detect-and-remediate-illicit-consent-grants) uprawnienia przyznane przez użytkowników lub wyłączyć możliwość domyślnego wyrażenia zgody przez użytkowników.

Oprócz inspekcji uprawnień przyznanych przez użytkowników, można [zlokalizować ryzykowne lub niechciane aplikacje OAuth](https://docs.microsoft.com/cloud-app-security/investigate-risky-oauth) w środowiskach premium.

## <a name="step-5---enable-end-user-self-service"></a>Krok 5 - Włącz samoobsługę użytkownika końcowego

W miarę możliwości będziemy chcieli zrównoważyć bezpieczeństwo z wydajnością. Zgodnie z tym samym kierunkiem zbliżania się do podróży z nastawieniem, że dajesz podwaliny pod bezpieczeństwo w dłuższej perspektywie, możesz usunąć tarcie z organizacji, wzmacniając użytkowników, zachowując czujność.

### <a name="implement-self-service-password-reset"></a>Wdrażanie samoobsługowego resetowania hasła

[Samoobsługowe resetowanie haseł usługi Azure AD (SSPR)](../../active-directory/authentication/quickstart-sspr.md) oferuje administratorom IT prosty sposób na umożliwienie użytkownikom resetowania lub odblokowywania haseł lub kont bez interwencji działu pomocy technicznej lub administratora. System zawiera szczegółowe raporty, które śledzi, kiedy użytkownicy mają zresetować swoje hasła, wraz z powiadomieniami, aby ostrzec o niewłaściwym użyciu lub nadużyciu.

### <a name="implement-self-service-group-and-application-access"></a>Wdrażanie samoobsługowego dostępu do grup i aplikacji

Usługa Azure AD umożliwia nie-administratorom zarządzanie dostępem do zasobów przy użyciu grup zabezpieczeń, grup usługi Office 365, ról aplikacji i katalogów pakietów dostępu.  [Samoobsługowe zarządzanie grupami](../../active-directory/users-groups-roles/groups-self-service-management.md) umożliwia właścicielom grup zarządzanie własnymi grupami bez konieczności przypisywania roli administracyjnej. Użytkownicy mogą również tworzyć grupy usługi Office 365 i zarządzać nimi bez polegania na administratorach do obsługi żądań, a nieużywane grupy wygasają automatycznie.  [Zarządzanie uprawnieniami usługi Azure AD](../../active-directory/governance/entitlement-management-overview.md) umożliwia dalsze delegowanie i widoczność, dzięki kompleksowym przepływom pracy żądań dostępu i automatycznemu wygasaniu.  Można delegować do innych administratorów możliwość konfigurowania własnych pakietów dostępu dla grup, zespołów, aplikacji i witryn usługi SharePoint Online, których są właścicielami, z niestandardowymi zasadami dotyczącymi tego, kto jest zobowiązany do zatwierdzenia dostępu, w tym konfigurowaniem sponsorów i partnerów biznesowych jako osoby zatwierdzające.

### <a name="implement-azure-ad-access-reviews"></a>Implementowanie przeglądów dostępu usługi Azure AD

Dzięki [przeglądom dostępu usługi Azure AD](../../active-directory/governance/access-reviews-overview.md)można zarządzać członkostwem pakietów i grup dostępu, dostępem do aplikacji dla przedsiębiorstw i przypisaniami ról uprzywilejowanych, aby upewnić się, że utrzymujesz standard zabezpieczeń.  Regularne nadzorowanie przez samych użytkowników, właścicieli zasobów i innych recenzentów zapewnia, że użytkownicy nie zachowują dostępu przez dłuższy czas, gdy nie są już potrzebni.

## <a name="summary"></a>Podsumowanie

Istnieje wiele aspektów bezpiecznej infrastruktury tożsamości, ale ta pięcioetapowa lista kontrolna pomoże Ci szybko osiągnąć bezpieczniejszą i bezpieczną infrastrukturę tożsamości:

* Wzmocnij swoje poświadczenia.
* Zmniejsz powierzchnię ataku.
* Automatyzacja reagowania na zagrożenia.
* Korzystaj z analizy chmury.
* Włącz bardziej przewidywalne i pełne bezpieczeństwo użytkowników końcowych dzięki samopomocy.

Doceniamy, jak poważnie traktujesz bezpieczeństwo tożsamości i mamy nadzieję, że ten dokument jest użytecznym planem działania na rzecz bezpieczniejszej postawy twojej organizacji.

## <a name="next-steps"></a>Następne kroki

Jeśli potrzebujesz pomocy w planowaniu i wdrażaniu zaleceń, zapoznaj się z [planami wdrażania projektu usługi Azure AD,](https://aka.ms/deploymentplans) aby uzyskać pomoc.

Jeśli masz pewność, że wszystkie te kroki są zakończone, użyj bezpiecznego [wyniku tożsamości](../../active-directory/fundamentals/identity-secure-score.md)firmy Microsoft, który będzie na bieżąco z [najnowszymi najlepszymi praktykami](identity-management-best-practices.md) i zagrożeniami bezpieczeństwa.
