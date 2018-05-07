---
title: Włącz połączenia LinkedIn dla usług w usłudze Azure Active Directory i aplikacji firmy Microsoft | Dokumentacja firmy Microsoft
description: Wyjaśniono, jak włączyć lub wyłączyć LinkedIn konto połączenia dla aplikacji firmy Microsoft w usłudze Azure Active Directory
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 05/02/2018
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.openlocfilehash: 9ab59033fd032cfccd2602a1a518e51a08e09581
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
---
# <a name="linkedin-account-connections-for-microsoft-apps-and-services"></a>LinkedIn konto połączenia dla usług i aplikacji firmy Microsoft
W tym artykule możesz dowiedzieć się, jak do zarządzania połączeniami konta LinkedIn dla dzierżawy w Centrum administracyjnym usługi Azure Active Directory (Azure AD). 

> [!IMPORTANT]
> Funkcja połączenia konta LinkedIn jest obecnie trwa wprowadzanie do dzierżaw usługi Azure AD. Gdy jest wycofywany dla Twojej dzierżawy, jest włączona domyślnie. Nie jest dostępna dla klientów dla instytucji rządowych Stanów Zjednoczonych i organizacji z usługą Exchange Online skrzynek pocztowych hostowanych w Australii, Kanada, Chiny, Francja, Niemczech, Indie, Korea Południowa, Zjednoczone Królestwo, Japonii i Republika Południowej Afryki. Obsługa tych lokalizacji skrzynki pocztowej będzie dostępna wkrótce.  Dla aktualnego widoku wdrożenia informacji, zobacz [Office 365 plan](https://products.office.com/business/office-365-roadmap?filters=%26freeformsearch=linkedin#abc) strony.

## <a name="benefit-to-users"></a>Korzyści dla użytkowników
Po użytkownicy łączyć swoje konta LinkedIn, LinkedIn informacje są używane do wyświetlenia spersonalizowanych informacji i funkcji w różnych aplikacji firmy Microsoft lub usług. Użytkownicy mogą zobaczyć informacjami na temat osoby się, że funkcje te działają w karcie profil firmy Microsoft, nawet jeśli te osoby spoza organizacji. Wraz z upływem czasu ich doświadczeń LinkedIn również staną się bardziej odpowiednie i dostosowane do ich pracy. Na przykład, LinkedIn może sugerować nowe połączenia na podstawie użytkowników pracować z lub na podstawie szczegółowych informacji o osobach kalendarzach danego dnia.

## <a name="how-linkedin-account-connections-appear-to-the-user"></a>Wygląd LinkedIn konto połączenia dla użytkownika
Połączenia konta LinkedIn umożliwiają użytkownikom informacje publiczne LinkedIn profil w ramach niektóre aplikacje firmy Microsoft. Użytkownicy w Twojej dzierżawie można połączyć z siecią firmową LinkedIn i firmy Microsoft lub szkolnego konta, aby wyświetlić dodatkowe informacje o profilu LinkedIn. Aby uzyskać więcej informacji, zobacz [LinkedIn informacji i funkcji w usługach i aplikacji firmy Microsoft](https://go.microsoft.com/fwlink/?linkid=850740).

Gdy użytkownicy w Twojej organizacji połączyć pracy LinkedIn i firmy Microsoft lub służbowych, mają dwie opcje: 
* Nadawanie uprawnień do udostępniania danych między oba konta. Oznacza to, że dają one uprawnienie do swojego konta LinkedIn udostępniania danych służbowym Microsoft lub konta służbowego, a także ich Microsoft konto służbowe do udostępnienia danych do swojego konta LinkedIn. Dane, które są udostępniane LinkedIn pozostawia usług online. 
* Zezwolić na udostępnianie danych tylko z konta LinkedIn z pracą firmy Microsoft i konto służbowe

Aby uzyskać więcej informacji o danych, które są współużytkowane przez użytkowników LinkedIn i Microsoft pracy lub szkołą kont, zobacz [LinkedIn w aplikacjach firmy Microsoft w miejscu pracy lub nauki](https://www.linkedin.com/help/linkedin/answer/84077). 
* [Użytkownicy mogą odłączyć kont](https://www.linkedin.com/help/linkedin/answer/85097) i usunięcie danych uprawnienia udostępniania w dowolnym momencie. 
* [Użytkownicy mogą kontrolować sposób wyświetlania swój własny profil LinkedIn](https://www.linkedin.com/help/linkedin/answer/83), łącznie z tego, czy można wyświetlić swój profil w aplikacjach firmy Microsoft.

## <a name="privacy-considerations"></a>Zagadnienia dotyczące ochrony prywatności
Włączenie LinkedIn połączenia konta umożliwia aplikacji firmy Microsoft i usługi, aby uzyskać dostęp do pewnych informacji LinkedIn użytkowników. Odczyt [zasady zachowania poufności informacji firmy Microsoft](https://privacy.microsoft.com/privacystatement/) dowiedzieć się więcej o zagadnienia dotyczące ochrony prywatności podczas włączania LinkedIn połączenia konta w usłudze Azure AD. 

## <a name="manage-linkedin-account-connections"></a>Zarządzanie połączeniami konta LinkedIn
Funkcja połączenia konta LinkedIn jest domyślnie całego dzierżawy. Można wyłączyć LinkedIn konto połączenia dla całego dzierżawy lub włączyć LinkedIn konto połączenia dla wybranych użytkowników w dzierżawie. 

### <a name="enable-or-disable-linkedin-account-connection-for-your-tenant-in-the-azure-portal"></a>Włącz lub wyłącz LinkedIn konto połączenia dla dzierżawy w portalu Azure

1. Zaloguj się do [Centrum administracyjnego usługi Azure Active Directory](https://aad.portal.azure.com/) przy użyciu konta, które jest administratorem globalnym dla dzierżawy usługi Azure AD.
2. Wybierz **użytkowników**.
3. Na **użytkowników** bloku, wybierz opcję **ustawienia użytkownika**.
4. W obszarze **LinkedIn konta połączenia**:
  * Wybierz **tak** umożliwiających LinkedIn połączenia konta dla wszystkich użytkowników w dzierżawie
  * Wybierz **wybrane** umożliwiające LinkedIn konta połączenia tylko wybrane dzierżawcy użytkowników
  * Wybierz **nr** Aby wyłączyć LinkedIn połączenia konta dla wszystkich użytkowników ![LinkedIn włączanie konta połączenia](./media/linkedin-integration/LinkedIn-integration.png)
5. Zapisz ustawienia, gdy wszystko będzie gotowe, wybierając **zapisać**.

### <a name="enable-or-disable-linkedin-account-connections-for-your-organizations-office-2016-apps-using-group-policy"></a>Włącz lub wyłącz LinkedIn konta połączenia w przypadku aplikacji pakietu Office 2016 w organizacji za pomocą zasad grupy

1. Pobierz [plików szablonów administracyjnych pakietu Office 2016 (ADMX/ŚILS)](https://www.microsoft.com/download/details.aspx?id=49030)
2. Wyodrębnij **ADMX** pliki i skopiuj je do centralnego magazynu.
3. Zarządzanie zasadami grupy Otwórz.
4. Utwórz obiekt zasad grupy z następującymi ustawieniami: **Konfiguracja użytkownika** > **Szablony administracyjne** > **Microsoft Office 2016**  >  **Różne** > **funkcji Pokaż LinkedIn w aplikacjach pakietu Office**.
5. Wybierz **włączone** lub **wyłączone**.
  * Gdy zasady są **włączone**, **funkcji Pokaż LinkedIn w aplikacjach pakietu Office** znaleźć w oknie dialogowym Opcje pakietu Office 2016 jest włączone. Oznacza to również użytkowników w organizacji można użyć funkcji LinkedIn w swoich aplikacjach pakietu Office.
  * Gdy zasady są **wyłączone**, **funkcji Pokaż LinkedIn w aplikacjach pakietu Office** znaleziono ustawienia w opcjach programu Office 2016 okna dialogowego ustawiono stan wyłączone, a użytkownicy końcowi nie można zmienić tego ustawienia. Użytkownicy w Twojej organizacji nie można użyć funkcji LinkedIn w aplikacjach pakietu Office 2016. 

Zasady grupy dotyczy tylko pakiet Office 2016 aplikacji na komputerze lokalnym. Użytkownicy mogą zobaczyć funkcje LinkedIn w profilu karty w usłudze Office 365, nawet wtedy, gdy wyłączenie LinkedIn w swoich aplikacjach pakietu Office 2016. 

### <a name="learn-more"></a>Dowiedz się więcej 
* [LinkedIn informacji i funkcji w aplikacjach firmy Microsoft](https://go.microsoft.com/fwlink/?linkid=850740)

* [Centrum pomocy LinkedIn](https://www.linkedin.com/help/linkedin)

## <a name="next-steps"></a>Kolejne kroki
Aby wyświetlić bieżących połączeń konta LinkedIn ustawienie w portalu Azure, użyj następującego łącza:

[Konfigurowanie połączeń konta LinkedIn](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings) 