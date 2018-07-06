---
title: Włączanie połączenia usługi LinkedIn dla aplikacji firmy Microsoft i usługi w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Wyjaśnia, jak włączyć lub wyłączyć połączenia konta usługi LinkedIn dla aplikacji firmy Microsoft w usłudze Azure Active Directory
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 06/28/2018
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.openlocfilehash: 4b3ff0b2481b42f516d28ac17f2616685730b7d5
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/06/2018
ms.locfileid: "37872420"
---
# <a name="linkedin-account-connections-for-microsoft-apps-and-services"></a>Połączenia konta usługi LinkedIn dla usług i aplikacji firmy Microsoft
W tym artykule możesz dowiedzieć się, jak zarządzać połączeniami konta LinkedIn dla dzierżawy w Centrum administracyjnym usługi Azure Active Directory (Azure AD). 

> [!IMPORTANT]
> Funkcję połączenia konta usługi LinkedIn jest obecnie udostępniana dzierżaw usługi Azure AD. Gdy jest ona udostępniona do swojej dzierżawy, jego jest domyślnie włączona. Nie jest dostępna dla klientów rządowych w Stanach Zjednoczonych i organizacji za pomocą usługi Exchange Online skrzynek pocztowych hostowanych w Australii, Kanada, (Chiny), Francja, Niemcy, Indie, Korea Południowa, Zjednoczone Królestwo, Japonia, część i Republika Południowej Afryki. Obsługa tych lokalizacjach skrzynki pocztowej będzie dostępna wkrótce.  Dla aktualnego widoku wprowadzania informacji, zobacz [Office 365 plan](https://products.office.com/business/office-365-roadmap?filters=%26freeformsearch=linkedin#abc) strony.

## <a name="benefit-to-users"></a>Korzyści dla użytkowników
Po użytkownikom połączyć swoje konto LinkedIn, LinkedIn informacje są używane do wyświetlenia spersonalizowanych informacji i funkcji w różnych aplikacjach firmy Microsoft lub usług. Użytkownicy widzą dane o osobach, które współpracują z karty profilu firmy Microsoft, nawet jeśli te osoby spoza organizacji. Wraz z upływem czasu ich środowisko pracy LinkedIn również stanie się lepiej dopasowane i dopasowane do ich pracy. Na przykład, LinkedIn zaproponować nowych połączeń, w oparciu o który użytkownicy korzystają z lub uzyskiwać szczegółowe informacje o osobach w kalendarzach tego samego dnia.

## <a name="how-linkedin-account-connections-appear-to-the-user"></a>Wygląd połączenia konta usługi LinkedIn dla użytkownika
Połączenia konta usługi LinkedIn umożliwić użytkownikom wyświetlanie publiczne informacje o profilu usługi LinkedIn w niektórych swoich aplikacjach firmy Microsoft. Użytkownicy w Twojej dzierżawie można połączyć z pracą firmy Microsoft i LinkedIn firmowego lub szkolnego konta, aby wyświetlić dodatkowe informacje o profilu usługi LinkedIn. Aby uzyskać więcej informacji, zobacz [LinkedIn informacji i funkcji w usługach i aplikacjach firmy Microsoft](https://go.microsoft.com/fwlink/?linkid=850740).

Gdy użytkownicy w Twojej organizacji połączyć swoją pracę LinkedIn i firmy Microsoft lub kont służbowych, są dostępne dwie opcje: 
* Nadaj uprawnienia do udostępniania danych między obu kont. Oznacza to, że zapewniają uprawnienia dla swojego konta usługi LinkedIn udostępnianie danych z pracą firmy Microsoft lub konta służbowego, a także ich Microsoft konta służbowego udostępniać dane za pomocą konta LinkedIn. Dane, które jest udostępnione serwisowi LinkedIn pozostawia usług online. 
* Zezwolić na udostępnianie danych tylko z ich konta LinkedIn pracą firmy Microsoft i konto służbowe

Aby uzyskać więcej informacji na temat danych, która jest współużytkowana przez użytkowników usługi LinkedIn i firmy Microsoft lub kont służbowych, zobacz [LinkedIn w aplikacjach firmy Microsoft w pracy lub nauki](https://www.linkedin.com/help/linkedin/answer/84077). 
* [Użytkownicy mogą odłączyć kont](https://www.linkedin.com/help/linkedin/answer/85097) i usunąć dane, uprawnienia do udostępniania w dowolnym momencie. 
* [Użytkownicy mogą kontrolować sposób wyświetlania własnego profilu usługi LinkedIn](https://www.linkedin.com/help/linkedin/answer/83), łącznie z tego, czy można wyświetlić swój profil w aplikacjach firmy Microsoft.

## <a name="privacy-considerations"></a>Kwestie dotyczące ochrony prywatności
Włączenie usługi LinkedIn połączenia konta umożliwia aplikacji firmy Microsoft i niektóre informacje o użytkownikach LinkedIn dostęp do usług. Odczyt [zasady zachowania poufności informacji firmy Microsoft](https://privacy.microsoft.com/privacystatement/) Aby dowiedzieć się więcej na temat kwestii związanych z prywatnością podczas włączania połączenia konta usługi LinkedIn w usłudze Azure AD. 

## <a name="manage-linkedin-account-connections"></a>Zarządzanie połączeniami konta LinkedIn
Funkcję połączenia konta usługi LinkedIn jest domyślnie dla całej dzierżawie. Istnieje możliwość połączenia konta usługi LinkedIn dla całej dzierżawie wyłączyć lub włączyć połączenia konta usługi LinkedIn dla wybranych użytkowników w dzierżawie. 

### <a name="enable-or-disable-linkedin-account-connection-for-your-tenant-in-the-azure-portal"></a>Włącz lub Wyłącz połączenie konta usługi LinkedIn dla dzierżawy w witrynie Azure portal

1. Zaloguj się do [Centrum administracyjne usługi Azure Active Directory](https://aad.portal.azure.com/) przy użyciu konta administratora globalnego dla dzierżawy usługi Azure AD.
2. Wybierz **użytkowników**.
3. Na **użytkowników** bloku wybierz **ustawienia użytkownika**.
4. W obszarze **połączenia konta usługi LinkedIn**:
  * Wybierz **tak** umożliwia połączenia konta usługi LinkedIn dla wszystkich użytkowników w dzierżawie
  * Wybierz **wybrane** umożliwiające LinkedIn konto połączenia dla tylko wybranej dzierżawy użytkowników
  * Wybierz **nie** wyłączania połączenia konta usługi LinkedIn dla wszystkich użytkowników ![połączenia konta włączenie usługi LinkedIn](./media/linkedin-integration/linkedin-integration.png)
5. Zapisz ustawienia w przypadku, gdy wszystko będzie gotowe, wybierając **Zapisz**.

### <a name="enable-or-disable-linkedin-account-connections-for-your-organizations-office-2016-apps-using-group-policy"></a>Włącz lub wyłącz połączenia konta usługi LinkedIn dla aplikacji pakietu Office 2016 w organizacji za pomocą zasad grupy

1. Pobierz [plików pakietu Office 2016 administracyjne szablon (ADMX/ŚILS)](https://www.microsoft.com/download/details.aspx?id=49030)
2. Wyodrębnij **ADMX** pliki i skopiować je do centralnego magazynu.
3. Zarządzanie zasadami grupy Otwórz.
4. Utwórz obiekt zasad grupy z następującymi ustawieniami: **Konfiguracja użytkownika** > **Szablony administracyjne** > **Microsoft Office 2016**  >  **Różne** > **Pokaż funkcje usługi LinkedIn w aplikacjach pakietu Office**.
5. Wybierz **włączone** lub **wyłączone**.
  * Gdy zasady są **włączone**, **Pokaż funkcje usługi LinkedIn w aplikacjach pakietu Office** znalezione w oknie dialogowym Opcje programu pakietu Office 2016 jest włączona. Oznacza to również, że użytkownicy w Twojej organizacji, można użyć funkcje usługi LinkedIn w swoich aplikacjach pakietu Office.
  * Gdy zasady są **wyłączone**, **Pokaż funkcje usługi LinkedIn w aplikacjach pakietu Office** ustawienie znaleziono za pomocą pakietu Office 2016 opcji okna dialogowego ustawiono w stanie wyłączenia, a użytkownicy końcowi nie można zmienić to ustawienie. Użytkownicy w organizacji nie można używać funkcji usługi LinkedIn w swoich aplikacjach pakietu Office 2016.

Te zasady grupy dotyczy tylko aplikacji pakietu Office 2016 na komputerze lokalnym. Użytkownicy mogą zobaczyć funkcje usługi LinkedIn w karty profilu w całej usłudze Office 365, nawet jeśli wyłączenie usługi LinkedIn w swoich aplikacjach pakietu Office 2016. 

### <a name="learn-more"></a>Dowiedz się więcej 
* [Informacje usługi LinkedIn i funkcje w aplikacjach firmy Microsoft](https://go.microsoft.com/fwlink/?linkid=850740)

* [Centrum pomocy usługi LinkedIn](https://www.linkedin.com/help/linkedin)

## <a name="next-steps"></a>Kolejne kroki
Użyj następującego linku, aby wyświetlić bieżących połączeń konta LinkedIn ustawienia w witrynie Azure portal:

[Konfigurowanie połączenia konta usługi LinkedIn](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings) 