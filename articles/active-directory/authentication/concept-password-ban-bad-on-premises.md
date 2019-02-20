---
title: Usługa Azure AD hasło protection w wersji zapoznawczej
description: Zablokuj słabe hasła w Active Directory w środowisku lokalnym za pomocą hasła usługi Azure AD protection (wersja zapoznawcza)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/18/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: f1beae186f6eb276b9aa302d3d51f0ba8688e591
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/19/2019
ms.locfileid: "56415752"
---
# <a name="preview-enforce-azure-ad-password-protection-for-windows-server-active-directory"></a>Wersja zapoznawcza: Wymuszanie ochrona za pomocą hasła usługi Azure AD dla usługi Active Directory systemu Windows Server

|     |
| --- |
| Ochrony hasłem w usłudze Azure AD i listy niestandardowej zakazanych haseł są publicznej wersji zapoznawczej funkcji usługi Azure Active Directory. Aby uzyskać więcej informacji na temat wersji zapoznawczych, zobacz [dodatkowym warunkom użytkowania wersji zapoznawczych usług Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Ochrony hasłem w usłudze Azure AD to nowa funkcja w wersji zapoznawczej obsługiwane przez usługi Azure Active Directory (Azure AD), aby zwiększyć zasady haseł w organizacji. Wdrażanie w środowisku lokalnym ochrona za pomocą hasła usługi Azure AD używa zarówno globalnych i niestandardowe zakazane listy haseł przechowywanych w usłudze Azure AD i wykonuje ten sam kontroli lokalnej zgodnie ze zmianami oparte na chmurze usługi Azure AD.

## <a name="design-principles"></a>Zasady projektowania

Usługa Azure Protection haseł usługi AD dla usługi Active Directory została zaprojektowana z następującymi zasadami pamiętać:

* Kontrolery domeny nigdy nie są wymagane do bezpośredniego komunikowania się z Internetem
* Żadne nowe sieci porty są otwarte na kontrolerach domeny.
* Nie zmian schematu usługi Active Directory są wymagane. Oprogramowanie korzysta z istniejącego kontenera usługi Active Directory i obiektów schematu serviceConnectionPoint.
* Nie minimalnych domeny usługi Active Directory lub funkcjonalności lasu poziom (DFL\FFL) jest wymagany.
* Oprogramowanie nie tworzenie ani nie wymaga żadnych kont w domenach usługi Active Directory, które chroni.
* Hasła w postaci zwykłego tekstu użytkowników nigdy nie opuszczają kontrolera domeny (czy w trakcie operacji sprawdzania poprawności hasła lub w dowolnym momencie).
* Wdrożenie przyrostowe jest obsługiwane z zależnościami, czy zasady haseł tylko są wymuszane, którym jest zainstalowany agent kontrolera domeny.
* Zalecane jest, aby zainstalować agenta kontrolera domeny na wszystkie kontrolery domeny, aby upewnić się, wymuszania zabezpieczeń ochrony wszechobecne hasła.

## <a name="architectural-diagram"></a>Diagram architektury

Jest to istotne dla zrozumienia podstawowego projektu i funkcjonalności pojęć przed wdrożeniem ochrony haseł usługi Azure AD w środowisku usługi Active Directory w środowisku lokalnym. Na poniższym diagramie przedstawiono, jak składniki ochrony haseł usługi Azure AD współpracują ze sobą:

![W jaki sposób składniki ochrony hasła usługi Azure AD współpracują ze sobą](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

Na powyższym diagramie przedstawiono trzech składników podstawowych oprogramowania, które tworzą ochrona za pomocą hasła usługi Azure AD:

* Usługa serwera Proxy ochrony haseł usługi Azure AD jest uruchamiana na dowolnym komputerze przyłączonym do domeny w bieżącym lesie usługi Active Directory. Ich głównym celem jest przekazywać żądania pobierania zasad haseł z kontrolerów domeny do usługi Azure AD, a następnie Zwróć odpowiedź z usługi Azure AD do kontrolera domeny.
* Biblioteki dll filtru haseł usługi Azure AD DC ochronę agenta hasło odbiera żądania weryfikacji hasła użytkownika z systemu operacyjnego i przekazuje je do usługi agenta ochrony kontrolera domeny haseł usługi Azure AD działa lokalnie na kontrolerze domeny.
* Usługa agenta DC ochronę haseł usługi Azure AD odbiera żądania weryfikacji hasła z dll filtru haseł agenta kontrolera domeny, przetwarza je za pomocą bieżące zasady haseł (dostępne lokalnie) i zwraca wynik (pass\fail).

## <a name="theory-of-operations"></a>Teoretycznie operacji

Dlatego podane powyższych zasad diagram i projekt, w jaki sposób ochrony haseł usługi Azure AD faktycznie działa?

Każda usługa serwera Proxy ochrony haseł usługi Azure AD anonsuje sam kontrolerów domeny w lesie przez utworzenie obiektu serviceConnectionPoint w usłudze Active Directory.

Każda usługa agenta haseł usługi Azure AD ochrony kontrolera domeny tworzy również obiektu serviceConnectionPoint w usłudze Active Directory. Jednak służy to głównie do raportowania i informacji diagnostycznych.

Usługa agenta DC ochronę haseł usługi Azure AD jest odpowiedzialny za inicjowanie pobierania nowych zasad haseł z usługi Azure AD. Pierwszym krokiem jest zlokalizować usługi serwera Proxy ochrony haseł usługi Azure AD przez wysyłanie zapytań las dla serwera proxy serviceConnectionPoint obiektów. Po znalezieniu usługi proxy dostępne żądania pobierania zasad hasła są wysyłane z usługi agenta kontrolera domeny do usługi serwera proxy, która z kolei wysyła do usługi Azure AD, a następnie zwraca odpowiedź usługi agenta kontrolera domeny. Po odebraniu nowych zasad haseł z usługi Azure AD, Usługa agenta kontrolera domeny przechowuje zasady w dedykowanym folderze w katalogu głównym udziału sysvol w domenie. Usługa agenta kontrolera domeny także monitoruje ten folder, w przypadku nowszych zasady replikacji w od innych usług agenta kontrolera domeny w domenie.

Usługa agenta DC ochronę haseł usługi Azure AD zawsze będzie żądać nowych zasad podczas uruchamiania usługi. Po uruchomieniu usługi agenta kontrolera domeny będzie okresowo (co godzinę) sprawdź wiek bieżące zasady dostępne lokalnie; Jeśli bieżące zasady jest starsza niż jedna godzina usługę agenta kontrolera domeny będzie żądać nowych zasad z usługi Azure AD zgodnie z powyższym opisem, w przeciwnym razie agenta kontrolera domeny będzie używać bieżących zasad.

Usługa agenta DC ochrony haseł usługi Azure AD komunikuje się z usługą serwera Proxy ochrony haseł usługi Azure AD przy użyciu RPC (zdalne wywoływanie procedury) za pośrednictwem protokołu TCP. Usługa serwera Proxy nasłuchuje tych wywołań na obu portu RPC dynamiczną lub statyczną (zgodnie z konfiguracją).

Agenta ochrony kontrolera domeny haseł usługi Azure AD nigdy nie nasłuchuje na porcie dostępnych sieci i usługa serwera Proxy nigdy nie próbuje wywołać usługę agenta kontrolera domeny.

Usługa serwera Proxy ochrony haseł usługi Azure AD jest bezstanowy; nigdy nie buforuje zasad lub dowolnego innego stanu pobrany z platformy Azure.

Usługa agenta DC ochronę haseł usługi Azure AD będą oceniać tylko hasła użytkownika przy użyciu najbardziej aktualnych zasad haseł dostępne lokalnie. Jeśli nie zasady haseł jest dostępny na lokalnym kontrolerze domeny, hasło zostanie automatycznie akceptowane, a komunikat dziennika zdarzeń zostaną zarejestrowane w celu otrzymania przez administratora.

Ochrona za pomocą usługi Azure AD hasła nie jest aparat aplikacji zasad w czasie rzeczywistym. Mogą wystąpić opóźnienia w okresie między zmiana konfiguracji zasad haseł znajduje się w usłudze Azure AD i czas osiągnie i są wymuszane na wszystkich kontrolerach domeny.

## <a name="foresttenant-binding-for-azure-ad-password-protection"></a>Powiązanie Forest\tenant ochrony haseł usługi Azure AD

Wdrożenie ochrony haseł usługi Azure AD w lesie usługi Active Directory wymaga rejestracji lasu usługi Active Directory i wszelkie wdrożone usługi Proxy ochrony haseł usługi Azure AD z usługą Azure AD. Zarówno wpisy do rejestru (las i serwery proxy) są skojarzone z określonym dzierżawy usługi Azure AD, który jest identyfikowany niejawnie za pomocą poświadczeń użytych podczas rejestracji. W dowolnym momencie są pobierane zasady haseł ochrony haseł usługi Azure AD, zawsze jest przeznaczony dla tej dzierżawy (czyli zasad będzie zawsze kombinacji Microsoft globalnego zakazane hasła i listy dla dzierżawcy niestandardowe zakazanych haseł). Skonfigurować różne domeny lub serwery proxy w lesie, może być powiązane z różnymi usługami Azure AD nie jest obsługiwane dzierżaw.

## <a name="license-requirements"></a>Wymagania licencyjne

Korzyści wynikające z listy globalne zakazanych haseł mają zastosowanie do wszystkich użytkowników usługi Azure Active Directory (Azure AD).

Listy niestandardowe zakazanych haseł wymaga licencji usługi Azure AD podstawowa.

Usługa Azure Protection haseł usługi AD dla systemu Windows Server Active Directory wymaga licencji usługi Azure AD Premium.

Dodatkowe informacje o licencjonowaniu, wraz z kosztami, można znaleźć na [usługi Azure Active Directory ceny witryny](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="download"></a>Do pobrania

Dwa wymagane pliki instalacyjne agenta ochrony haseł usługi Azure AD, który można pobrać z [Centrum pobierania firmy Microsoft](https://www.microsoft.com/download/details.aspx?id=57071)

## <a name="next-steps"></a>Kolejne kroki

[Wdrażanie ochrony haseł w usłudze Azure AD](howto-password-ban-bad-on-premises-deploy.md)
