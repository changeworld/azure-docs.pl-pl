---
title: Usługa Azure AD ochrona za pomocą hasła — usługi Azure Active Directory
description: Zablokuj słabe hasła w usłudze Active Directory w środowisku lokalnym za pomocą ochrona za pomocą hasła usługi Azure AD
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
ms.openlocfilehash: d58c019cf3d801ce938a4ca6eca70b1606bf4ff6
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59264475"
---
# <a name="enforce-azure-ad-password-protection-for-windows-server-active-directory"></a>Wymuszanie ochrona za pomocą hasła usługi Azure AD dla usługi Active Directory systemu Windows Server

Ochrony hasłem w usłudze Azure AD jest funkcją, która rozszerza zasady haseł w organizacji. Ochrona za pomocą hasła wdrożenia w środowisku lokalnym używa zarówno globalnych i niestandardowych zakazanych haseł listy, które są przechowywane w usłudze Azure AD. Robi się tego samego sprawdzenia lokalnego jako usługi Azure AD dla zmian oparte na chmurze.

## <a name="design-principles"></a>Zasady projektowania

Ochrony hasłem w usłudze Azure AD zaprojektowano z tymi zasadami, należy pamiętać:

* Kontrolery domeny nigdy nie mają się komunikować się bezpośrednio z Internetu.
* Żadne nowe sieci porty są otwarte na kontrolerach domeny.
* Nie zmian schematu usługi Active Directory są wymagane. Oprogramowanie korzysta z istniejącą usługą Active Directory **kontenera** i **serviceConnectionPoint** obiektów schematu.
* Nie minimalnych usługi Active Directory domeny lub lasu poziom funkcjonalności (DFL/FFL) jest wymagany.
* Oprogramowanie nie jest tworzenie i wymagają kont w domenach usługi Active Directory, które chroni.
* Hasła w postaci zwykłego tekstu użytkowników nigdy nie opuszczają kontrolera domeny, podczas wykonywania operacji sprawdzania poprawności hasła lub w dowolnym innym czasie.
* Oprogramowanie nie jest zależny od innych funkcji usługi Azure AD; na przykład synchronizacji skrótów haseł usługi Azure AD nie jest powiązana i nie jest wymagany w kolejności ochrona za pomocą hasła usługi Azure AD do funkcji.
* Wdrożenie przyrostowe jest obsługiwane, ale z zasadami haseł jest wymuszana tylko którym jest zainstalowany Agent kontrolera domeny (DC Agent). Zobacz następny temat, aby uzyskać więcej informacji.

## <a name="incremental-deployment"></a>Wdrożenie przyrostowe

Ochrony hasłem w usłudze Azure AD obsługuje wdrożenie przyrostowe między kontrolerami domeny w domenie usługi Active Directory, ale jest ważne zrozumieć, co tak naprawdę oznacza to, i jakie są wady i zalety.

Oprogramowanie agenta usługi Azure AD hasło ochrony kontrolera domeny można tylko weryfikowania haseł, gdy jest zainstalowany na kontrolerze domeny, a tylko w przypadku zmiany hasła, które są wysyłane do tego kontrolera domeny. Nie jest możliwe do kontroli kontrolerach domeny, które zostały wybrane przez komputery klienckie Windows do przetwarzania wprowadzania zmian hasła użytkownika. Aby można było zagwarantować spójne zachowanie oraz wzmacnianiu zabezpieczeń ochrony universal hasła, oprogramowanie agenta kontrolera domeny musi zainstalowany na wszystkich kontrolerach domeny w domenie.

Wiele organizacji będą chcieli dokładnej testowanie ochrony haseł usługi Azure AD w podzestawie kontrolerów domeny przed pełnym wdrożeniem czynności. Ochrony hasłem w usłudze Azure AD obsługuje wdrożenia częściowe, ie oprogramowanie agenta kontrolera domeny na danym kontrolerze domeny będą aktywnie weryfikowania haseł, nawet wtedy, gdy inne kontrolery domeny w domenie, nie masz zainstalowanego oprogramowani kontrolera domeny. Częściowe wdrożenia tego typu nie są bezpieczny i nie są inne niż zalecane do celów testowych.

## <a name="architectural-diagram"></a>Diagram architektury

Należy zapoznać się z podstawowej projektowania i pojęcia związane z funkcjami przed wdrożeniem ochrona za pomocą hasła usługi Azure AD w środowisku usługi Active Directory w środowisku lokalnym. Na poniższym diagramie przedstawiono, jak składniki ochrony hasłem współpracują ze sobą:

![W jaki sposób składniki ochrony hasła usługi Azure AD współpracują ze sobą](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

* Usługa serwera Proxy ochrony haseł usługi Azure AD jest uruchamiana na dowolnym komputerze przyłączonym do domeny w bieżącym lesie usługi Active Directory. Ich głównym celem jest do przekazywania żądań pobierania zasad haseł z kontrolerów domeny do usługi Azure AD. Następnie zwraca odpowiedzi z usługi Azure AD do kontrolera domeny.
* Filtr haseł biblioteki DLL agenta DC odbiera żądania sprawdzenie poprawności hasła użytkownika z systemu operacyjnego. Przesyła je do usługi agenta kontrolera domeny, która jest uruchomiona lokalnie na kontrolerze domeny.
* Usługa agenta kontrolera domeny, hasło ochrony odbiera żądania sprawdzenie poprawności hasła z filtru haseł biblioteki DLL agenta kontrolera domeny. Przetwarza je za pomocą bieżące zasady haseł (dostępne lokalnie) i zwraca wynik: *przekazać* lub *się nie powieść*.

## <a name="how-password-protection-works"></a>Jak działa ochrona za pomocą hasła

Każde wystąpienie usługi serwera Proxy ochrony haseł usługi Azure AD anonsuje się do kontrolerów domeny w lesie, tworząc **serviceConnectionPoint** obiektu w usłudze Active Directory.

Każda usługa kontrolera domeny, Agent ochrony hasłem wzrasta, powstaje **serviceConnectionPoint** obiektu w usłudze Active Directory. Ten obiekt jest używany głównie do raportowania i informacji diagnostycznych.

Usługa agenta kontrolera domeny jest odpowiedzialny za inicjowanie pobierania nowych zasad haseł z usługi Azure AD. Pierwszym krokiem jest zlokalizowanie usługi Proxy ochrony haseł usługi Azure AD przez wysyłanie zapytań las dla serwera proxy **serviceConnectionPoint** obiektów. Po znalezieniu usługi proxy dostępne DC Agent wysyła żądanie pobierania zasad haseł z usługą serwera proxy. Usługa serwera proxy z kolei wysyła żądanie do usługi Azure AD. Usługa serwera proxy następnie zwraca odpowiedź usługi agenta kontrolera domeny.

Gdy Usługa agenta DC otrzyma nowe zasady haseł z usługi Azure AD, Usługa przechowuje zasady w dedykowanym folderze w katalogu głównym domeny *sysvol* udziału folderu. Usługa agenta kontrolera domeny także monitoruje ten folder, w przypadku nowszych zasady replikacji w od innych usług agenta kontrolera domeny w domenie.

Usługa agenta kontrolera domeny zawsze żąda nowych zasad podczas uruchamiania usługi. Po uruchomieniu usługi agenta kontrolera domeny, sprawdza wiek bieżące zasady dostępne lokalnie co godzinę. Jeśli zasady są starsze niż jedna godzina, agenta kontrolera domeny żąda nowych zasad z usługi Azure AD za pośrednictwem usługi Serwer proxy, zgodnie z wcześniejszym opisem. Jeśli bieżące zasady nie jest starsza niż jedna godzina, Agent kontrolera domeny w dalszym ciągu używa tych zasad.

Przy każdym pobraniu zasad haseł usługi Azure AD hasło ochrony tej zasady jest specyficzne dla dzierżawy. Innymi słowy zasady dotyczące haseł są zawsze kombinacją globalnej listy zakazanych haseł firmy Microsoft i niestandardowej listy zakazanych haseł dla dzierżawy.

DC Agent komunikuje się z usługą serwera proxy, za pośrednictwem usługi RPC za pośrednictwem protokołu TCP. Usługa serwera proxy nasłuchuje tych wywołań na dynamiczną lub statyczną portu RPC, w zależności od konfiguracji.

Agent kontrolera domeny nigdy nie nasłuchuje na porcie dostępne w sieci.

Usługa serwera proxy nigdy nie wywołuje usługę agenta kontrolera domeny.

Usługa serwera proxy jest bezstanowy. Nigdy nie buforuje zasad lub dowolnego innego stanu pobrany z platformy Azure.

Usługa agenta kontrolera domeny zawsze używa najnowszych zasad haseł dostępne lokalnie można obliczyć wartości hasła użytkownika. Jeśli nie zasady haseł jest dostępny na lokalnym kontrolerze domeny, hasło jest automatycznie akceptowane. Jeśli tak się stanie, aby ostrzec administratora jest rejestrowany komunikat zdarzenia.

Ochrony hasłem w usłudze Azure AD nie jest aparat aplikacji zasad w czasie rzeczywistym. Może to być opóźnienie między podczas wprowadzania zmian konfiguracji zasad haseł w usłudze Azure AD, kiedy zmienić przypada oraz są wymuszane na wszystkich kontrolerach domeny.

Ochrony hasłem w usłudze Azure AD działa jako uzupełnienie istniejących zasad haseł usługi Active Directory, nie mogą zastąpić. Dotyczy to również wszelkich innych firm 3 hasło biblioteki DLL filtru, może być zainstalowana. Zawsze usługi Active Directory wymaga, że wszystkie składniki sprawdzania poprawności hasła zgodę przed zaakceptowaniem hasła.

## <a name="foresttenant-binding-for-password-protection"></a>Powiązanie las/dzierżawy ochrony hasłem

Wdrożenie ochrona za pomocą hasła usługi Azure AD w lesie usługi Active Directory wymaga rejestracji tego lasu w usłudze Azure AD. Każda usługa serwera proxy, który jest wdrożony również musi być zarejestrowany w usłudze Azure AD. Tyto registrace lasu i serwera proxy są skojarzone z określonym dzierżawy usługi Azure AD, która jest niejawnie identyfikowane za pomocą poświadczeń, które są używane podczas rejestracji.

Las usługi Active Directory i wszystkie usługi proxy wdrożonej w obrębie lasu muszą być zarejestrowane przy użyciu tej samej dzierżawy. Masz lasu usługi Active Directory lub usług serwera proxy w tym dzierżawy lasu jest zarejestrowany do innej usługi Azure AD nie jest obsługiwana. Objawy źle skonfigurowane wdrożenia to brak możliwości pobrania zasad haseł.

## <a name="download"></a>Do pobrania

Dwa pliki instalacyjne wymagane agenta ochrony hasłem w usłudze Azure AD są dostępne z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=57071).

## <a name="next-steps"></a>Kolejne kroki
[Wdrażanie ochrony haseł w usłudze Azure AD](howto-password-ban-bad-on-premises-deploy.md)
