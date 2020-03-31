---
title: Ochrona hasłem usługi Azure AD — usługa Azure Active Directory
description: Zamów słabe hasła w lokalnej usłudze Active Directory przy użyciu ochrony hasłem usługi Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/18/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1b9d07099f8de996181948921330ef6744b302a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74848650"
---
# <a name="enforce-azure-ad-password-protection-for-windows-server-active-directory"></a>Enforce Azure AD password protection for Windows Server Active Directory (Wymuszanie ochrony hasłem usługi Azure AD dla usługi Active Directory systemu Windows Server)

Ochrona hasłem usługi Azure AD to funkcja, która poprawia zasady haseł w organizacji. Lokalne wdrażanie ochrony hasłem używa zarówno globalnych, jak i niestandardowych list haseł zbanowanych, które są przechowywane w usłudze Azure AD. Wykonuje te same kontrole lokalne, co usługa Azure AD w przypadku zmian w chmurze. Te kontrole są wykonywane podczas zmiany hasła i resetowania hasła scenariuszy.

## <a name="design-principles"></a>Zasady projektowania

Ochrona hasłem usługi Azure AD została zaprojektowana z myślą o następujących zasadach:

* Kontrolery domeny nigdy nie muszą komunikować się bezpośrednio z Internetem.
* Na kontrolerach domeny nie są otwierane żadne nowe porty sieciowe.
* Nie są wymagane żadne zmiany schematu usługi Active Directory. Oprogramowanie korzysta z istniejącego **kontenera** usługi Active Directory i obiektów schematu **serviceConnectionPoint.**
* Nie jest wymagany minimalny poziom funkcjonalności usługi Active Directory lub lasu (DFL/FFL).
* Oprogramowanie nie tworzy ani nie wymaga kont w domenach usługi Active Directory, które chroni.
* Hasła w postaci zwykłego tekstu użytkownika nigdy nie opuszczają kontrolera domeny podczas operacji sprawdzania hasła ani w żadnym innym czasie.
* Oprogramowanie nie jest zależne od innych funkcji usługi Azure AD; na przykład synchronizacja skrótów haseł usługi Azure AD nie jest powiązana i nie jest wymagana, aby ochrona hasłem usługi Azure AD działała.
* Wdrożenie przyrostowe jest obsługiwane, jednak zasady haseł są wymuszane tylko wtedy, gdy jest zainstalowany agent kontrolera domeny (agent kontrolera domeny). Zobacz następny temat, aby uzyskać więcej informacji.

## <a name="incremental-deployment"></a>Wdrażanie przyrostowe

Ochrona hasłem usługi Azure AD obsługuje stopniowe wdrażanie między kontrolerami domeny w domenie usługi Active Directory, ale ważne jest, aby zrozumieć, co to naprawdę oznacza i jakie są kompromisy.

Oprogramowanie agenta kontrolera domeny ochrony hasłem usługi Azure AD może sprawdzać poprawność haseł tylko wtedy, gdy jest zainstalowane na kontrolerze domeny i tylko w przypadku zmian haseł wysyłanych do tego kontrolera domeny. Nie można kontrolować, które kontrolery domeny są wybierane przez maszyny klienckie systemu Windows do przetwarzania zmian hasła użytkownika. Aby zagwarantować spójne zachowanie i uniwersalne egzekwowanie zabezpieczeń ochrony hasłem, oprogramowanie agenta kontrolera domeny musi być zainstalowane na wszystkich kontrolerach domeny w domenie.

Wiele organizacji będzie chciało dokładnie przetestować ochronę hasłem usługi Azure AD na podzbiorze kontrolerów domeny przed wykonaniem pełnego wdrożenia. Ochrona hasłem usługi Azure AD obsługuje częściowe wdrożenie, czyli oprogramowanie agenta kontrolera domeny na danym kontrolerze domeny będzie aktywnie sprawdzać poprawność haseł, nawet jeśli inne kontrolery domeny w domenie nie mają zainstalowanego oprogramowania agenta kontrolera domeny. Częściowe wdrożenia tego typu NIE są bezpieczne i nie są zalecane inne niż do celów testowych.

## <a name="architectural-diagram"></a>Diagram architektoniczny

Przed wdrożeniem ochrony hasłem usługi Azure AD w lokalnym środowisku usługi Active Directory należy zapoznać się z podstawowymi pojęciami dotyczącymi projektowania i funkcji. Na poniższym diagramie pokazano, jak składniki ochrony hasłem współpracują ze sobą:

![Współpraca składników ochrony hasłem usługi Azure AD](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

* Usługa serwera proxy ochrony hasłem usługi Azure AD jest uruchamiana na dowolnym komputerze przyłączanym do domeny w bieżącym lesie usługi Active Directory. Jego głównym celem jest przekazywanie żądań pobierania zasad haseł z kontrolerów domeny do usługi Azure AD. Następnie zwraca odpowiedzi z usługi Azure AD do kontrolera domeny.
* Biblioteka DLL filtru haseł agenta kontrolera domeny odbiera żądania sprawdzania poprawności hasła użytkownika z systemu operacyjnego. Przekazuje je do usługi agenta kontrolera domeny, która działa lokalnie na kontrolerze domeny.
* Usługa agenta kontrolera domeny ochrony hasłem odbiera żądania sprawdzania poprawności hasła z biblioteki DLL filtru haseł agenta kontrolera domeny. Przetwarza je przy użyciu bieżącej (lokalnie dostępnej) zasady haseł i zwraca wynik: *pass* lub *fail*.

## <a name="how-password-protection-works"></a>Jak działa ochrona hasłem

Każde wystąpienie usługi Proxy ochrony haseł usługi Azure AD anonsuje się kontrolerom domeny w lesie, tworząc obiekt **serviceConnectionPoint** w usłudze Active Directory.

Każda usługa agenta kontrolera domeny do ochrony hasłem tworzy również obiekt **serviceConnectionPoint** w usłudze Active Directory. Ten obiekt jest używany głównie do raportowania i diagnostyki.

Usługa agenta kontrolera domeny jest odpowiedzialna za inicjowanie pobierania nowych zasad haseł z usługi Azure AD. Pierwszym krokiem jest zlokalizowanie usługi serwera proxy ochrony haseł usługi Azure AD przez zapytanie do lasu obiektów usługi **proxyConnectionPoint.** Po znalezieniu dostępnej usługi proxy agent kontrolera domeny wysyła żądanie pobierania zasad haseł do usługi proxy. Usługa proxy z kolei wysyła żądanie do usługi Azure AD. Usługa serwera proxy zwraca następnie odpowiedź do usługi agenta kontrolera domeny.

Po usługa agenta kontrolera domeny odbiera nowe zasady haseł z usługi Azure AD, usługa przechowuje zasady w dedykowanym folderze w katalogu głównym jego udziału folderu *sysvol* domeny. Usługa agenta kontrolera domeny monitoruje również ten folder w przypadku, gdy nowsze zasady są replikowane z innych usług agenta kontrolera domeny w domenie.

Usługa agenta kontrolera domeny zawsze żąda nowych zasad podczas uruchamiania usługi. Po uruchomieniu usługi agenta kontrolera domeny sprawdza wiek bieżących zasad dostępnych lokalnie co godzinę. Jeśli zasada jest starsza niż jedna godzina, agent kontrolera domeny żąda nowych zasad z usługi Azure AD za pośrednictwem usługi proxy, zgodnie z wcześniejszym opisem. Jeśli bieżąca zasada nie jest starsza niż jedna godzina, agent kontrolera domeny nadal używa tej zasady.

Za każdym razem, gdy pobierana jest zasada ochrony hasłem usługi Azure AD, ta zasada jest specyficzna dla dzierżawy. Innymi słowy, zasady haseł są zawsze kombinacją globalnej listy zablokowanych haseł firmy Microsoft i niestandardowej listy haseł zbanowanych na dzierżawę.

Agent kontrolera domeny komunikuje się z usługą proxy za pośrednictwem protokołu RPC za pośrednictwem protokołu TCP. Usługa proxy nasłuchuje tych wywołań na dynamicznym lub statycznym porcie RPC, w zależności od konfiguracji.

Agent kontrolera domeny nigdy nie nasłuchuje na porcie dostępnym w sieci.

Usługa serwera proxy nigdy nie wywołuje usługi agenta kontrolera domeny.

Usługa proxy jest bezstanowa. Nigdy nie buforuje zasad ani innego stanu pobranego z platformy Azure.

Usługa agenta kontrolera domeny zawsze używa najnowszych zasad haseł dostępnych lokalnie do oceny hasła użytkownika. Jeśli na lokalnym kontrolerze domeny nie są dostępne żadne zasady haseł, hasło jest automatycznie akceptowane. W takim przypadku jest rejestrowany komunikat o zdarzeniu, aby ostrzec administratora.

Ochrona hasłem usługi Azure AD nie jest aparatem aplikacji zasad w czasie rzeczywistym. Może wystąpić opóźnienie między wprowadzeniem zmiany konfiguracji zasad haseł w usłudze Azure AD a wprowadzeniem tej zmiany i wymuszania ich na wszystkich kontrolerach domeny.

Ochrona hasłem usługi Azure AD działa jako uzupełnienie istniejących zasad haseł usługi Active Directory, a nie zastąpienie. Obejmuje to wszelkie inne biblioteki dll filtru haseł innych firm, które mogą być zainstalowane. Usługa Active Directory zawsze wymaga, aby wszystkie składniki sprawdzania poprawności hasła zgadzały się przed zaakceptowaniem hasła.

## <a name="foresttenant-binding-for-password-protection"></a>Powiązanie lasu/dzierżawy w celu ochrony hasłem

Wdrożenie ochrony hasłem usługi Azure AD w lesie usługi Active Directory wymaga rejestracji tego lasu za pomocą usługi Azure AD. Każda wdrożona usługa serwera proxy musi być również zarejestrowana w usłudze Azure AD. Te rejestry lasów i serwerów proxy są skojarzone z określoną dzierżawą usługi Azure AD, która jest identyfikowana niejawnie przez poświadczenia, które są używane podczas rejestracji.

Las usługi Active Directory i wszystkie wdrożone usługi proxy w lesie muszą być zarejestrowane w tej samej dzierżawie. Nie jest obsługiwana możliwość rejestrowania lasu usługi Active Directory ani żadnych usług proxy w tym lesie w różnych dzierżawach usługi Azure AD. Symptomy takiego nieprawidłowo skonfigurowanego wdrożenia obejmują niemożność pobrania zasad haseł.

## <a name="download"></a>Pobierz

Dwa wymagane instalatory agentów do ochrony hasłem usługi Azure AD są dostępne w [Centrum pobierania Microsoft.](https://www.microsoft.com/download/details.aspx?id=57071)

## <a name="next-steps"></a>Następne kroki
[Wdrażanie ochrony haseł w usłudze Azure AD](howto-password-ban-bad-on-premises-deploy.md)
