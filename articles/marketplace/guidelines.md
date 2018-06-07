---
title: Wytyczne dotyczące portalu Azure Marketplace i AppSource wydawcy | Azure
description: Wytyczne dotyczące portalu Azure Marketplace oraz AppSource wydawcy aplikacji i usług
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: jm-aditi-ms
manager: pabutler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 06/05/2018
ms.author: ellacroi
ms.openlocfilehash: 135f934cd6b352dad9e4cea5a14406804f31b66b
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2018
ms.locfileid: "34825215"
---
# <a name="guidelines"></a>Wytyczne  

<!--
## Guidelines for AppSource  
-->
---  

## <a name="guidelines-for-azure-marketplace"></a>Wytyczne dotyczące portalu Azure Marketplace  

### <a name="guidelines-for-creating-a-microsoft-id-to-manage-a-marketplace-account"></a>Wytyczne dotyczące tworzenia Identyfikatora Microsoft do zarządzania konta witryny marketplace  
Jeśli więcej niż jedna osoba wymaga dostępu do tego samego Identyfikatora Microsoft użyty do utworzenia konta witryny marketplace, należy wykonać te wskazówki ułatwiające tworzenie konta firmy. 

>[!IMPORTANT]
>Aby autoryzować wielu użytkownikom na dostęp do tego konta Microsoft Developer Center (Centrum deweloperów), firma Microsoft zaleca, należy użyć usługi Azure Active Directory (Azure AD) do przypisania ról do poszczególnych użytkowników. Każdy użytkownik musi uzyskać dostępu do konta poprzez zalogowanie za pomocą poszczególnych poświadczenia usługi Azure AD. Utwórz identyfikator firmy Microsoft przy użyciu adresu e-mail w domenie, w zarejestrowany w firmie Microsoft sugeruje, że wiadomości e-mail nie można przypisać do osoby. Może to być na przykład `windowsapps@fabrikam.com`.  
>*   Aby uzyskać więcej informacji, odwiedź stronę [problem: identyfikator firmy Microsoft w usłudze Azure AD federacyjnych domeny](#issue:-microsoft-id-in-an-azure-ad-federated-domain) sekcji.  

*   Ograniczanie dostępu do tego Identyfikatora Microsoft najmniejszą możliwą liczbę deweloperzy. 
*   Konfigurowanie listy dystrybucyjnej firmowej poczty e-mail (DL), która zawiera wszystkich użytkowników, którzy muszą uzyskać dostęp do danych konta Centrum deweloperów. Dodaj adres e-mail DL do informacji zabezpieczających. Listy Dystrybucyjnej, włącza wszystkich pracowników na liście do odbierania kodów zabezpieczeń na żądanie i zarządzać informacji o zabezpieczeniach dla programu Microsoft ID. Jeśli ustawienie listy dystrybucyjnej nie jest to możliwe, właściciel konta e-mail poszczególnych musi być dostępny do uzyskania dostępu i udostępniania kodu zabezpieczeń po wyświetleniu monitu.  
    *   Na przykład właściciel otrzyma monit, gdy nowe informacje zabezpieczeń są dodawane do Identyfikatora Microsoft lub identyfikator firmy Microsoft jest dostępny z nowego urządzenia.  
*   Dodaj numer telefonu firmy, który nie wymaga rozszerzenia i jest dostępna dla członków zespołu klucza.  
*   Ogólnie rzecz biorąc należy włączyć deweloperom na logowanie na koncie Centrum deweloperów przy użyciu zaufanych urządzeń. Wszyscy członkowie zespołu kluczy powinien mieć dostęp do zaufanych urządzeń. Dostęp do za pomocą zaufanych urządzeń zmniejsza wymaganie wysyła kody zabezpieczeń, gdy ktoś uzyskuje dostęp do konta Centrum deweloperów.  
*   Jeśli są wymagane, aby udzielić dostępu do konta Centrum deweloperów na komputerze z systemem innym niż zaufane, należy ograniczyć dostęp do nie więcej niż pięć deweloperów. W idealnym przypadku deweloperów powinien uzyskiwać dostęp do konta z komputerów, które identyczny geograficzne i lokalizacja w sieci.  
*   Często Przejrzyj i sprawdź informacji zabezpieczających.  
    *   Aby wyświetlić informacji o zabezpieczeniach, odwiedź zabezpieczeń w lokalizacji Ustawienia strony [account.live.com/proofs/Manage](https://account.live.com/proofs/Manage).

Twoje konto Centrum deweloperów powinni mieć dostęp przede wszystkim z zaufanych komputerów. Jest krytyczny, że dostęp do zaufanych komputerów, ponieważ istnieje limit liczby kody wygenerowane na konto Centrum deweloperów na tydzień. Przy użyciu zaufanych komputerów umożliwia także najbardziej bezpieczne i zgodne logowania. 
*   Aby uzyskać więcej informacji na temat dodatkowe wskazówki konta Centrum deweloperów i zabezpieczeń można znaleźć otwarcie strony konta dewelopera znajdujący się w [docs.microsoft.com/windows/uwp/publish/opening-a-developer-account](https://docs.microsoft.com/windows/uwp/publish/opening-a-developer-account). 

---  

#### <a name="issue-microsoft-id-in-an-azure-ad-federated-domain"></a>Problem: Domeny federacyjnej identyfikator firmy Microsoft w usłudze Azure AD  
Twoje konto firmowe mogą federacyjnych za pomocą usługi Azure Active Directory (Azure AD). Jeśli próbujesz utworzyć identyfikator firmy Microsoft przy użyciu adresu firmowej poczty e-mail, który jest Sfederowane z usługą Azure AD, zostanie wyświetlony błąd. Jeśli wystąpi błąd, następnie należy skontaktować się z zespołem IT, aby potwierdzić, że Twoje konto jest Sfederowane za pomocą usługi Azure AD. Azure AD federacyjnych poczty e-mail jest to znany problem i Microsoft pracuje rozwiązania.  
*   Aby uzyskać więcej informacji na temat usługi Azure AD, odwiedź witrynę Azure Active Directory dokumentację strony znajduje się w [docs.microsoft.com/azure/active-directory](https://docs.microsoft.com/azure/active-directory).

Firma Microsoft zaleca obejście tego problemu. Wykonaj następujące kroki, aby utworzyć nowy adres e-mail w `outlook.com` domeny i Utwórz regułę do przekazywania komunikacji.  
1.  Przejdź na stronę tworzenia konta i kliknij Get nowe łącze adresu e-mail. 
    *   W celu uzyskania Identyfikatora Microsoft, odwiedź stronę konta Utwórz znajdujący się w [signup.live.com/signup](https://signup.live.com/signup).  
2.  Utwórz nowy adres e-mail, a następnie wprowadź hasło. Nowy identyfikator firmy Microsoft i skrzynki pocztowej poczty e-mail w `outlook.com` domena jest tworzona. Proces rejestracji należy kontynuować do momentu utworzenia konta.  

    >[!IMPORTANT]
    >Należy użyć poczty e-mail adres lub listę dystrybucyjną zarejestrowany jako identyfikator firmy Microsoft, aby zarejestrować się w Centrum deweloperów. Firma Microsoft zaleca, użyj listy dystrybucyjnej usunąć zależności od osób. Jeśli lista adres lub dystrybucji poczty e-mail nie jest zarejestrowany, następnie należy zarejestrować teraz.    

    >[!Important]
    >Jeśli z dowolnego adresu e-mail znajduje się w `Microsoft` firmy domeny, a następnie nie jest możliwe użycie do rejestracji w Centrum deweloperów.  

3.  Po utworzeniu Identyfikatora Microsoft z adresem e-mail programu Outlook, zaloguj się do skrzynki pocztowej programu Outlook. Utwórz wiadomość e-mail przekazywania reguły. Zasady przekazywania wiadomości e-mail, należy przenieść wszystkie wiadomości e-mail, które są odbierane w skrzynkę pocztową programu Outlook na adres e-mail w domenie, który został utworzony w celu zarządzania konta witryny marketplace.  
    *   Aby zalogować się do skrzynki pocztowej programu Outlook, odwiedź stronę programu Outlook, znajdujący się w [outlook.live.com/owa](https://outlook.live.com/owa).  
    *   Aby uzyskać więcej informacji na temat funkcji przekazywania reguł, odwiedź stronę Użyj reguł w aplikacji Outlook Web App automatycznego przesyłania dalej wiadomości do innej strony konto znajduje się w [support.office.com/article/ Use-rules-in-Outlook-Web-App-to-automatically-forward-messages-to-another-account-1433e3a0-7fb0-4999-b536-50e05cb67fed](https://support.office.com/article/Use-rules-in-Outlook-Web-App-to-automatically-forward-messages-to-another-account-1433e3a0-7fb0-4999-b536-50e05cb67fed).  

1.  Reguła przesyłania dalej wysyła wszystkie wiadomości e-mail i komunikacji skierowany do konta e-mail programu Outlook na adres e-mail w domenie, w zarejestrowany w firmie. Twoje `outlook.com` adres e-mail należy go użyć do uwierzytelnienia zarówno w Centrum deweloperów, jak i w chmurze Portal dla partnerów.  

## <a name="next-steps"></a>Kolejne kroki
*   Odwiedź stronę [AppSource wydawcy przewodnik i portalu Azure Marketplace](./marketplace-publishers-guide.md) strony.  
 
---  
