---
title: Tworzenie bezpiecznych aplikacji w systemie Microsoft Azure
description: W tym artykule omówiono najlepsze rozwiązania, należy wziąć pod uwagę podczas fazy implementacji i weryfikacji projektu aplikacji sieci web.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/12/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: f68f6c366571b8f0f04f8507606c1a4008ab0405
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443255"
---
# <a name="develop-secure-applications-on-azure"></a>Tworzenie bezpiecznych aplikacji na platformie Azure
W tym artykule, firma Microsoft przedstawia działania dotyczące zabezpieczeń i kontroli należy wziąć pod uwagę podczas opracowywania aplikacji dla chmury. Pytania zabezpieczające i koncepcji, które należy wziąć pod uwagę podczas fazy implementacji i weryfikacji firmy Microsoft [cykl projektowania zabezpieczeń (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) zostały uwzględnione. Celem jest pomagającym w zdefiniowaniu działań i usług platformy Azure służących do tworzenia bardziej bezpiecznych aplikacji.

W tym artykule omówione są następujące fazy SDL:

- Wdrażanie
- Weryfikacja

## <a name="implementation"></a>Wdrażanie
Fokus fazy wdrożenia jest ustanowienie najlepsze rozwiązania dotyczące zapobiegania wczesne i wykryć i usunąć problemy dotyczące zabezpieczeń z kodu.
Załóżmy, że aplikacja będzie używane w sposób, że przypadkowo może być ona używana. Dzięki temu można zabezpieczyć się przed przypadkowym lub zamierzone niewłaściwe korzystanie z aplikacji.

### <a name="perform-code-reviews"></a>Wykonaj przeglądów kodu

Przed zaewidencjonowaniem kodu postępowania [przeglądami kodu](https://docs.microsoft.com/azure/devops/learn/devops-at-microsoft/code-reviews-not-primarily-finding-bugs) do zwiększenia ogólnej jakości kodu i zmniejszenia ryzyka powstawania usterek. Możesz użyć [programu Visual Studio](https://docs.microsoft.com/azure/devops/repos/tfvc/get-code-reviewed-vs?view=vsts) Zarządzanie proces przeglądu kodu.

### <a name="perform-static-code-analysis"></a>Wykonywanie analizy kodu statycznego

[Statyczna analiza kodu](https://www.owasp.org/index.php/Static_Code_Analysis) (znany także jako *źródła analizy kodu*) jest zazwyczaj wykonywane w ramach przeglądu kodu. Statyczna analiza kodu często odnosi się do uruchamiania znajdowanie potencjalnych luk w zabezpieczeniach w kodzie bez uruchamiania przy użyciu technik, takich jak narzędzia do analizy kodu statycznego [skażenia sprawdzanie](https://en.wikipedia.org/wiki/Taint_checking) i [analizy przepływu danych](https://en.wikipedia.org/wiki/Data-flow_analysis).

Portal Azure Marketplace oferuje [narzędzi deweloperskich](https://azuremarketplace.microsoft.com/marketplace/apps/category/developer-tools?page=1&search=code%20review) , wykonywać analizy kodu statycznego i uzyskanymi przeglądów kodu.

### <a name="validate-and-sanitize-every-input-for-your-application"></a>Sprawdzanie poprawności i oczyszczanie co dane wejściowe dla swojej aplikacji

Traktuj wszystkie dane wejściowe jako niezaufane, aby ochronić aplikację przed najbardziej powszechnych luk w zabezpieczeniach aplikacji sieci web. Niezaufane dane są pojazdu na ataki przez iniekcję kodu. Dane wejściowe na potrzeby aplikacji zawiera parametry w adresie URL, dane wejściowe użytkownika, dane z bazy danych z interfejsu API i wszystkie elementy, które zostały przekazane, użytkownik może potencjalnie manipulować. Aplikacja powinna [zweryfikować](https://www.owasp.org/index.php/OWASP_Proactive_Controls_2016#4:_Validate_All_Inputs) dane są składniowo i semantycznie prawidłowy, zanim aplikacja wykorzystuje dane w dowolny sposób (łącznie z wyświetleniem go do użytkownika).

Sprawdzanie poprawności danych wejściowych w wcześnie przepływu danych, aby upewnić się, że tylko poprawnie sformułowany dane są wprowadzane przepływu pracy. Nie chcesz, aby uszkodzone dane utrwalanie w bazie danych lub wyzwalanie usterek w składniku podrzędnego.

Blacklisting i umieszczania na białej liście znajdują się dwa ogólne podejścia do wykonywania sprawdzania poprawności danych wejściowych składni:

  - Wpisywanie na czarną listę próbuje sprawdzić, czy dane wejściowe użytkownika danego nie zawiera zawartość "znane jako złośliwe".

  - Umieszczania na białej liście próby sprawdź, czy dane wejściowe użytkownika danego zgodny zestaw danych wejściowych "dobrze znanych". Opartego na znakach umieszczania na białej liście jest formą umieszczania na białej liście, gdzie aplikacja sprawdza, czy dane wejściowe użytkownika zawiera znaki tylko "znane dobre" lub że danych wejściowych odpowiada znanego formatu.
    Na przykład może to obejmować sprawdzenie, czy nazwa użytkownika zawiera tylko znaki alfanumeryczne lub zawiera dokładnie dwie cyfry.

Umieszczania na białej liście jest preferowanym podejściem do tworzenia bezpiecznego oprogramowania.
Wpisywanie na czarną listę jest podatne na błędy, ponieważ nie jest możliwe jest myśleć o pełnej listy potencjalnie nieprawidłowe dane wejściowe.

Wykonaj tę pracę na serwerze, nie po stronie klienta (lub na serwerze i po stronie klienta).

### <a name="verify-your-applications-outputs"></a>Sprawdź dane wyjściowe aplikacji

Żadnych danych wyjściowych, który przedstawić wizualnie lub w dokumencie zawsze powinien być kodowany i poprzedzone znakiem zmiany znaczenia. [Anulowanie](https://www.owasp.org/index.php/Injection_Theory#Escaping_.28aka_Output_Encoding.29), znane również jako *kodowanie wyjścia*, służy do zapewnienia, że niezaufanych danych nie można przeprowadzić przy użyciu ataku polegającego na iniekcji. Anulowanie w połączeniu z sprawdzania poprawności danych, zawiera warstwowym mechanizmom obronnym, aby zwiększyć bezpieczeństwo systemu jako całości.

Anulowanie sprawia, że się, że wszystko jest wyświetlany jako *danych wyjściowych.* Anulowanie umożliwia również interpreter wiedzieć, że dane nie są przeznaczone do wykonywania i zapobiega atakom pracy. Jest to inny ataku technikę o nazwie *skryptów między witrynami* (XSS).

Jeśli używasz struktury sieci web, od osób trzecich, można sprawdzić opcje danych wyjściowych kodowania w witrynach sieci Web przy użyciu [ściągawka dotycząca zapobiegania OWASP XSS](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.md).

### <a name="use-parameterized-queries-when-you-contact-the-database"></a>Użyć sparametryzowanych zapytań, gdy skontaktować się z bazy danych

Nigdy nie twórz wbudowane zapytanie bazy danych "w locie" w kodzie oraz wysyłać je bezpośrednio do bazy danych. Złośliwy kod wstawiać do aplikacji, mogą potencjalnie powodować skradzione, wyczyszczone lub zmodyfikowanie bazy danych. Aplikację można również uruchomić polecenia złośliwego systemu operacyjnego w systemie operacyjnym, który jest hostem bazy danych.

Zamiast tego należy użyć sparametryzowanych zapytań lub procedur składowanych. Gdy używasz sparametryzowanych zapytań, można bezpiecznie wywołać procedury z kodu i przekazania go ciągu, nie martwiąc się, że będą traktowane jako część instrukcji kwerendy.

### <a name="remove-standard-server-headers"></a>Usuwanie nagłówków standardowy serwer

Nagłówki, takich jak serwer X-obsługiwane — za i X-AspNet-Version ujawnić informacje dotyczące serwera i technologie bazowego. Firma Microsoft zaleca, aby pominąć te nagłówki, aby uniknąć odcisków aplikacji.
Zobacz [usuwanie nagłówków standardowy serwer w usłudze Azure websites](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/).

### <a name="segregate-your-production-data"></a>Oddzielenie czynności związanych z danymi produkcyjnymi

Danych produkcyjnych lub danych "członu real", nie powinien używane dla rozwoju, testowania i innych celów niż przeznaczone firmy. Maskowane ([anonimowe](https://en.wikipedia.org/wiki/Data_anonymization)) zestaw danych powinien być używany dla wszystkich programowania i testowania.

Oznacza to, że mniej osoby mają dostęp do danych rzeczywistych, co zmniejsza ataków. Oznacza to również, że mniejszej liczby pracowników Zobacz osobistych danych, które pozwala wyeliminować potencjalne naruszenia zabezpieczeń w poufności.

### <a name="implement-a-strong-password-policy"></a>Zaimplementuj silne hasła

Obrony przed siłowych i oparte na słownik zgadywania, musisz zaimplementować zasady silnych haseł, aby upewnić się, że użytkownicy tworzyć złożone hasło (na przykład 12 znaków minimalnej długości i konieczności znakami alfanumerycznymi i specjalnymi).

Platforma tożsamości umożliwia tworzenie i wymuszanie zasad haseł. Usługa Azure AD B2C umożliwia zarządzanie hasłami, zapewniając [wbudowane zasady](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies#create-a-password-reset-policy), [samoobsługowego resetowania haseł](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-sspr)i nie tylko.

Do ochrony przed atakami na domyślnych kontach, sprawdź wszystkich kluczy i haseł są zastąpienia i są one generowane lub zastąpiona po zainstalowaniu zasobów.

Jeśli aplikacja musi automatycznego generowania haseł, upewnij się, że wygenerowanego hasła są losowych i mają o wysokiej entropii.

### <a name="validate-file-uploads"></a>Sprawdź poprawność przekazywania plików

Jeśli aplikacja zezwala [pliku przekazywania](https://www.owasp.org/index.php/Unrestricted_File_Upload), należy wziąć pod uwagę różne środki ostrożności, które można wykonać w przypadku takie ryzykowne działania. Pierwszym etapem wiele ataków jest uzyskanie niektórych złośliwego kodu do systemu, który jest atakowana. Przy użyciu przekazywanie pliku pomaga osoba atakująca to osiągnąć. OWASP oferuje rozwiązania do zweryfikowania pliku do upewnij się, że plik, który jest przekazywany jest bezpieczne.

Ochrona przed złośliwym kodem ułatwia identyfikowanie i usuwanie wirusów, programów szpiegujących oraz innego złośliwego oprogramowania. Możesz zainstalować [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) lub rozwiązanie do ochrony punktu końcowego partnera firmy Microsoft ([Trend Micro](https://www.trendmicro.com/azure/), [Symantec](https://www.symantec.com/products), [McAfee](https://www.mcafee.com/us/products.aspx), [Usługi Windows Defender](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10), i [programu System Center Endpoint Protection](https://docs.microsoft.com/sccm/protect/deploy-use/endpoint-protection)).

[Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) zawiera funkcje, takie jak ochrona w czasie rzeczywistym, zaplanowane skanowanie, korygowaniem złośliwego oprogramowania, aktualizacji sygnatur, aktualizacje aparatu, przykłady raportowania i zbierania zdarzeń wykluczeń. Można zintegrować z rozwiązania Microsoft Antimalware i partnerów z [usługi Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-partner-integration) w celu ułatwienia wdrażania i wbudowane funkcje wykrywania (alerty i zdarzenia).

### <a name="dont-cache-sensitive-content"></a>Nie buforuj poufnej zawartości

Nie buforuj poufnej zawartości w przeglądarce. Przeglądarki może przechowywać informacje o pamięci podręcznej i historię. Buforowane pliki są przechowywane w folderze, takich jak folderu tymczasowych plików internetowych, w przypadku programu Internet Explorer. Kiedy te strony są określane ponownie, przeglądarka wyświetla strony z pamięci podręcznej. Jeśli poufnych informacji (adres karty kredytowej, numer ubezpieczenia społecznego, nazwa użytkownika) jest wyświetlany użytkownikowi, informacje mogą być przechowywane w pamięci podręcznej przeglądarki i pobieranie, sprawdzając pamięci podręcznej przeglądarki lub po prostu nacisnąć przeglądarki **Ponownie** przycisku.

## <a name="verification"></a>Weryfikacja
Faza weryfikacji obejmuje kompleksowe nakład pracy, aby upewnić się, że dany kod spełnia założenia bezpieczeństwo i ochrona prywatności, które zostały utworzone w poprzednich faz.

### <a name="find-and-fix-vulnerabilities-in-your-application-dependencies"></a>Znajdowanie i eliminowanie luk w zabezpieczeniach w zależności aplikacji

Skanowania aplikacji i jej zależne biblioteki, aby zidentyfikować wszystkie znane składniki na ataki. Obejmują produkty, które są dostępne, aby wykonać to skanowanie [Sprawdzanie zależności OWASP](https://www.owasp.org/index.php/OWASP_Dependency_Check),[Snyk](https://snyk.io/), i [kaczka czarny](https://www.blackducksoftware.com/).

Skanowanie luk w zabezpieczeniach działającemu [usługi Tinfoil Security](https://www.tinfoilsecurity.com/) jest dostępna dla usługi Azure App Service Web Apps. [Usługi tinfoil Security skanowanie za pomocą usługi App Service](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/) oferuje deweloperom i administratorom szybkie, zintegrowane i ekonomiczne metody odnajdywania i adresowania luk w zabezpieczeniach, zanim złośliwego aktora można z nich skorzystać.

> [!NOTE]
> Możesz również [integracji usługi Tinfoil Security z usługą Azure AD](https://docs.microsoft.com/azure/active-directory/saas-apps/tinfoil-security-tutorial). Integrowanie usługi Tinfoil Security z usługą Azure AD zapewnia następujące korzyści:
>  - W usłudze Azure AD można kontrolować, kto ma dostęp do usługi Tinfoil Security.
>  - Użytkownicy mogą być automatycznie zalogowany do usługi Tinfoil Security (logowanie jednokrotne) za pomocą ich kont usługi Azure AD.
>  - Możesz zarządzać kont użytkownika w jednym, centralnym miejscu witryny Azure portal.

### <a name="test-your-application-in-an-operating-state"></a>Testowanie aplikacji w stanie działania

Aplikacja dynamiczna testowanie zabezpieczeń (DAST) to proces testowania aplikacji w stanie działania, aby znaleźć luki w zabezpieczeniach. Narzędzia DAST analizować programów, podczas, gdy są one wykonywane można znaleźć luki w zabezpieczeniach, takie jak uszkodzenie pamięci, konfiguracja serwera niezabezpieczone, skryptów między witrynami, problemy z uprawnieniami użytkownika, wstrzykiwanie kodu SQL i innych krytycznych bezpieczeństwem.

DAST różni się od zabezpieczeń aplikacji statyczne testowania (SAST). Narzędzia SAST analizowanie kodu źródłowego lub skompilowane wersje kodu, gdy nie wykonuje kod, aby można było znaleźć luki w zabezpieczeniach.

Najlepiej wykonać DAST, przy pomocy professional zabezpieczeń ( [tester penetracji](https://docs.microsoft.com/azure/security/azure-security-pen-testing) lub jednostkę zajmującą się oceną luk w zabezpieczeniach). Jeśli professional zabezpieczeń jest niedostępna, możesz wykonać DAST samodzielnie za pomocą skanera serwera proxy sieci web i niektóre szkolenia. Podłącz skaner DAST na wczesnym etapie, aby upewnić się, że w kodzie, nie spowodują problemów bezpieczeństwa. Zobacz [OWASP](https://www.owasp.org/index.php/Category:Vulnerability_Scanning_Tools) witryny, aby uzyskać listę skanery luk w zabezpieczeniach aplikacji sieci web.

### <a name="perform-fuzz-testing"></a>Testowanie argumentu rozmycie

W [argumentu rozmycie testowania](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/), powodować awarię programu celowo wprowadzając źle sformułowane lub losowe dane do aplikacji. Wykonuje awarię programu pozwala ujawnić potencjalne problemy przed udostępnieniem aplikacji.

[Zagrożenie dla bezpieczeństwa, wykrywanie](https://docs.microsoft.com/security-risk-detection/) jest argumentu rozmycie unikatowy Microsoft testowania usługi służące do znajdowania usterek zabezpieczenia krytyczny w oprogramowaniu.

### <a name="conduct-attack-surface-review"></a>Przeprowadzanie przeglądu powierzchni ataku

Przeglądanie obszar narażony na ataki, po uzupełnianie kodu pomaga zapewnić, że wszelkie zaprojektowano lub zaimplementowano zmieni się z aplikacją lub uznawane za system. Pomaga upewnić się, że wszelkie nowe wektory ataków, które zostały utworzone w wyniku zmiany, w tym modele zagrożeń zostały przejrzane i uniemożliwione.

Możesz utworzyć obraz obszar narażony na ataki przez skanowanie aplikacji. Firma Microsoft oferuje narzędzia analizy powierzchniowe ataku, o nazwie [analizatora powierzchni ataku](https://www.microsoft.com/download/details.aspx?id=24487). Można wybrać wiele komercyjnych testowania dynamicznej i luk w zabezpieczeniach skanowania narzędzia lub usługi, w tym [OWASP ' c ataku Proxy projektu](https://www.owasp.org/index.php/OWASP_Zed_Attack_Proxy_Project), [Arachni](http://arachni-scanner.com/), [Skipfish](https://code.google.com/p/skipfish/), i [w3af](http://w3af.sourceforge.net/). Te narzędzia skanowania można przeszukiwać w swojej aplikacji i zamapuj części aplikacji, które są dostępne w sieci web. Można także przeszukać w portalu Azure Marketplace podobne [narzędzi deweloperskich](https://azuremarketplace.microsoft.com/marketplace/apps/category/developer-tools?page=1).

### <a name="perform-security-penetration-testing"></a>Przeprowadzanie testów penetracyjnych zabezpieczeń

Zapewnienie, że aplikacja jest bezpieczna jest tak ważna jak testowania innych funkcji. Wprowadź [testów penetracyjnych](https://docs.microsoft.com/azure/security/azure-security-pen-testing) standardowa część procesu kompilacji i wdrażania. Zaplanuj testy regularne zabezpieczeń i wyszukiwaniu luk w zabezpieczeniach na wdrożonych aplikacji i Monitoruj otwartych portów, punktów końcowych i atakami.

### <a name="run-security-verification-tests"></a>Uruchom testy weryfikacji zabezpieczeń

[Secure DevOps Kit dla platformy Azure](https://azsk.azurewebsites.net/index.html) (AzSK) zawiera SVTs dla wielu usług platformy Azure. Możesz uruchomić te SVTs okresowo, aby upewnić się, że Twoja subskrypcja platformy Azure i różnymi zasobami, wchodzące w skład aplikacji w stanie bezpiecznym. Testy te można też zautomatyzować przy użyciu funkcji rozszerzenia ciągłej integracji/ciągłego wdrażania (CI/CD) AzSK, co sprawia, że SVTs jest dostępna jako rozszerzenie programu Visual Studio.

## <a name="next-steps"></a>Kolejne kroki
W następujących artykułach firma Microsoft zaleca środki kontroli bezpieczeństwa i działań, które mogą pomóc Ci projektowanie i wdrażanie bezpiecznych aplikacji.

- [Projektowanie bezpiecznych aplikacji](secure-design.md)
- [Wdrażaj bezpieczne aplikacje](secure-deploy.md)
