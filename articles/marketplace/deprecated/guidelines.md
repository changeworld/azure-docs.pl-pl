---
title: Wskazówki dla wydawcy portalu Azure Marketplace i appsource | Azure
description: Wskazówki dotyczące portalu Azure Marketplace i usługi AppSource dla wydawców aplikacji i usług
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: jm-aditi-ms
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 06/13/2018
ms.author: dsindona
ms.openlocfilehash: f41aeb75253c803eac03b856d1e1ed0edb74a7e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80281531"
---
# <a name="guidelines"></a>Wytyczne  

<!--
## Guidelines for AppSource  
-->
---

## <a name="guidelines-for-azure-marketplace"></a>Wskazówki dotyczące portalu Azure Marketplace  

### <a name="guidelines-for-creating-a-microsoft-id-to-manage-a-marketplace-account"></a>Wskazówki dotyczące tworzenia identyfikatora Microsoft ID w celu zarządzania kontem w portalu marketplace  
Jeśli więcej niż jedna osoba wymaga dostępu do tego samego identyfikatora Microsoft, który jest używany do tworzenia konta w portalu Marketplace, należy postępować zgodnie z tymi wytycznymi, aby ułatwić utworzenie konta firmowego. 

>[!IMPORTANT]
>Aby autoryzować wielu użytkowników do uzyskiwania dostępu do konta Centrum deweloperów firmy Microsoft, firma Microsoft zaleca przypisywanie ról poszczególnym użytkownikom za pomocą usługi Azure Active Directory (Azure AD). Każdy użytkownik musi uzyskać dostęp do konta, logując się przy użyciu indywidualnych poświadczeń usługi Azure AD. Utwórz swój identyfikator Microsoft, używając adresu e-mail w domenie zarejestrowanej w firmie Microsoft sugeruje, aby wiadomość e-mail nie była przypisana do danej osoby. Może to być na przykład `windowsapps@fabrikam.com`.  
>*   Aby uzyskać więcej informacji, odwiedź problem: Identyfikator firmy Microsoft w sekcji [domeny federacyjnej usługi Azure AD.](#issue-microsoft-id-in-an-azure-ad-federated-domain)  

*   Ogranicz dostęp do identyfikatora Microsoft ID do jak najmniejszej liczby deweloperów. 
*   Skonfiguruj firmową listę dystrybucyjną poczty e-mail (DL), która obejmuje wszystkich, którzy muszą uzyskać dostęp do twojego konta Centrum deweloperów. Dodaj adres e-mail DL do informacji zabezpieczających. Biblioteka DL umożliwia wszystkim pracownikom na liście odbieranie kodów zabezpieczeń na żądanie i zarządzanie informacjami o zabezpieczeniach identyfikatora Microsoft. Jeśli skonfigurowanie listy dystrybucyjnej nie jest możliwe, właściciel indywidualnego konta e-mail musi być dostępny, aby uzyskać dostęp do kodu zabezpieczającego i udostępnić go po wyświetleniu monitu.  
    *   Na przykład właściciel jest monitowany o dodaniem nowych informacji o zabezpieczeniach do identyfikatora Microsoft lub o dostępie do identyfikatora Microsoft ID z nowego urządzenia.  
*   Dodaj firmowy numer telefonu, który nie wymaga rozszerzenia i jest dostępny dla kluczowych członków zespołu.  
*   Ogólnie rzecz biorąc, należy wymagać od deweloperów używania zaufanych urządzeń do logowania się na konto Centrum deweloperów. Wszyscy kluczowi członkowie zespołu powinni mieć dostęp do zaufanych urządzeń. Korzystanie z zaufanych urządzeń w celu uzyskania dostępu zmniejsza wymagania dotyczące wysyłania kodów zabezpieczeń, gdy ktoś uzyskuje dostęp do konta Centrum deweloperów.  
*   Jeśli wymagane jest udzielenie dostępu do konta Centrum deweloperów z nieuznanego komputera, należy ograniczyć dostęp do nie więcej niż pięciu deweloperów. W idealnym przypadku deweloperzy powinni uzyskać dostęp do konta z komputerów, które mają tę samą lokalizację geograficzną i sieciową.  
*   Często przeglądaj i weryfikuj informacje zabezpieczające.  
    *   Aby wyświetlić informacje zabezpieczające, odwiedź stronę Ustawienia zabezpieczeń znajdującą się w [account.live.com/proofs/Manage](https://account.live.com/proofs/Manage).

Dostęp do konta Centrum deweloperów powinien być dostępny przede wszystkim z zaufanych komputerów. Bardzo ważne jest, aby uzyskać dostęp z zaufanych komputerów, ponieważ istnieje limit liczby kodów generowanych na konto Centrum deweloperów tygodniowo. Korzystanie z zaufanych komputerów umożliwia również najbardziej bezpieczne i spójne środowisko logowania. 
*   Aby uzyskać więcej informacji na temat dodatkowych wytycznych dotyczących konta i zabezpieczeń Centrum deweloperów, odwiedź stronę Otwieranie konta dewelopera znajdującą się w [docs.microsoft.com/windows/uwp/publish/opening-a-developer-account](https://docs.microsoft.com/windows/uwp/publish/opening-a-developer-account). 

---

#### <a name="issue-microsoft-id-in-an-azure-ad-federated-domain"></a>Problem: Identyfikator firmy Microsoft w domenie federacyjnej usługi Azure AD  
Twoje konto firmowe może być sfederowane za pośrednictwem usługi Azure Active Directory (Azure AD). Jeśli spróbujesz utworzyć identyfikator Microsoft ID przy użyciu firmowego adresu e-mail, który jest sfederowany z usługą Azure AD, zostanie wyświetlony błąd. Jeśli zostanie wyświetlony błąd, należy skontaktować się z zespołem IT, aby potwierdzić, że twoje konto jest sfederowane za pośrednictwem usługi Azure AD. Usługa Azure AD federacyjne wiadomości e-mail jest znany problem i firma Microsoft pracuje nad jego rozwiązaniem.  
*   Aby uzyskać więcej informacji na temat usługi Azure AD, odwiedź stronę Dokumentacji usługi Azure Active Directory znajdującą się pod [adresem docs.microsoft.com/azure/active-directory](https://docs.microsoft.com/azure/active-directory).

Firma Microsoft zaleca obejście problemu. Wykonaj następujące kroki, aby utworzyć `outlook.com` nowy adres e-mail w domenie i utworzyć regułę przekazywania dalej komunikacji.  
1.  Przejdź do strony Utwórz konto i kliknij link Uzyskaj nowy adres e-mail. 
    *   Aby zarejestrować się w celu utworzenia identyfikatora Microsoft ID, odwiedź stronę Utwórz konto znajdującą się w [signup.live.com/signup](https://signup.live.com/signup).  
2.  Utwórz nowy adres e-mail i wprowadź hasło. Zostanie utworzony nowy identyfikator firmy Microsoft `outlook.com` i skrzynka pocztowa poczty e-mail w domenie. Kontynuuj proces rejestracji do momentu utworzenia konta.  

    >[!IMPORTANT]
    >Aby zarejestrować się w Centrum deweloperów, należy użyć adresu e-mail lub listy dystrybucyjnej zarejestrowanej jako identyfikator Microsoft. Firma Microsoft zaleca użycie listy dystrybucyjnej w celu usunięcia zależności od osób fizycznych. Jeśli Twój adres e-mail lub lista dystrybucyjna nie są zarejestrowane, musisz się zarejestrować teraz.    

    >[!Important]
    >Jeśli dowolny adres e-mail `Microsoft` znajduje się w domenie firmy, nie możesz go używać do rejestracji w Centrum deweloperów.  

3.  Po utworzeniu identyfikatora Microsoft ID przy za pomocą adresu e-mail programu Outlook zaloguj się do skrzynki pocztowej programu Outlook. Utwórz regułę przekazywania wiadomości e-mail. Reguła przekazywania wiadomości e-mail powinna przenosić wszystkie wiadomości e-mail odebrane w skrzynce pocztowej programu Outlook na adres e-mail w domenie utworzony w celu zarządzania kontem w portalu marketplace.  
    *   Aby zalogować się do skrzynki pocztowej programu Outlook, odwiedź stronę programu Outlook znajdującą się pod [adresem outlook.live.com/owa](https://outlook.live.com/owa).  
    *   Aby uzyskać więcej informacji na temat reguł przekazywania, odwiedź stronę Reguły użytkowania w aplikacji Outlook Web App, aby automatycznie przesyłać dalej wiadomości na inną stronę konta znajdującą się pod [adresem support.office.com/article/Use-rules-in-Outlook-Web-App-to-automatically-forward-messages-to-another-account-1433e3a0-7fb0-4999-b536-50e05cb67fed](https://support.office.com/article/Use-rules-in-Outlook-Web-App-to-automatically-forward-messages-to-another-account-1433e3a0-7fb0-4999-b536-50e05cb67fed).  

1.  Reguła przekazywania wiadomości wysyła wszystkie wiadomości e-mail i wiadomości odebrane na koncie e-mail programu Outlook na adres e-mail w domenie zarejestrowanej w firmie. Twój `outlook.com` adres e-mail musi być używany do uwierzytelniania zarówno w Centrum deweloperów, jak i w portalu cloud partner.  

## <a name="next-steps"></a>Następne kroki

*   Odwiedź stronę [Przewodnik po portalu Azure Marketplace i Przewodnik dla wydawców appsource.](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide) 
 
---
