---
title: Wytyczne dotyczące wydawcy w portalu Azure Marketplace i AppSource | Azure
description: 'Wskazówki dotyczące witryny Azure Marketplace i AppSource dla: wydawcy aplikacji i usługi'
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
ms.date: 06/13/2018
ms.author: ellacroi
ms.openlocfilehash: a0ab88b1375f5178ca6f41689de8d2f30f8d7808
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60739445"
---
# <a name="guidelines"></a>Wytyczne  

<!--
## Guidelines for AppSource  
-->
---

## <a name="guidelines-for-azure-marketplace"></a>Wskazówki dotyczące witryny Azure Marketplace  

### <a name="guidelines-for-creating-a-microsoft-id-to-manage-a-marketplace-account"></a>Wytyczne dotyczące tworzenia Identyfikatora firmy Microsoft do zarządzania kontem witryny marketplace  
Jeśli więcej niż jedna osoba wymaga dostępu do tego samego Identyfikatora Microsoft użyte do utworzenia konta witryny marketplace, należy stosować te wytyczne ułatwiające tworzenie konta firmy. 

>[!IMPORTANT]
>Aby autoryzować wielu użytkownikom dostępu do Twojego konta Microsoft Developer Center (Centrum deweloperów), firma Microsoft zaleca, usługa Azure Active Directory (Azure AD) do przypisywania ról do poszczególnych użytkowników. Każdy użytkownik musi uzyskać dostępu do konta poprzez zalogowanie za pomocą poszczególnych poświadczeń usługi Azure AD. Utwórz swój identyfikator firmy Microsoft przy użyciu adresu e-mail w domenie zarejestrowany w firmie Microsoft sugeruje, że wiadomość e-mail nie można przypisać do poszczególnych. Może to być na przykład `windowsapps@fabrikam.com`.  
>*   Aby uzyskać więcej informacji, odwiedź stronę [problemu: Identyfikator firmy Microsoft w usłudze Azure AD jest sfederowana domeny](#issue-microsoft-id-in-an-azure-ad-federated-domain) sekcji.  

*   Ograniczanie dostępu do Identyfikatora firmy Microsoft do najmniejszej możliwej liczby deweloperów. 
*   Skonfiguruj listy dystrybucyjne firmowej poczty e-mail (DL), która zawiera każdy, kto może uzyskiwać dostęp do danych konta Centrum deweloperów. Dodaj adres e-mail listy Dystrybucyjnej do informacji zabezpieczających. Listy Dystrybucyjnej umożliwia wszystkich pracowników na liście, aby otrzymać kody zabezpieczeń zleconą oraz do zarządzania informacje o zabezpieczeniach dla usługi Microsoft ID. Jeśli skonfigurowanie listy dystrybucyjnej nie jest możliwe, właściciel konta e-mail poszczególnych musi być dostępny dostęp do i udostępniania kodu zabezpieczeń po wyświetleniu monitu.  
    *   Na przykład właściciel jest monitowany o po dodaniu nowych informacji o zabezpieczeniach, identyfikator firmy Microsoft lub identyfikator firmy Microsoft jest dostępny z nowego urządzenia.  
*   Dodaj numer telefonu firmy, który nie wymaga rozszerzenia i jest dostępny dla innych członków zespołu klucza.  
*   Ogólnie rzecz biorąc należy wymagać deweloperzy mogą używać zaufanych urządzeń, aby zalogować się do danych konta Centrum deweloperów. Wszyscy członkowie zespołu klucza powinien mieć dostęp do zaufanych urządzeń. Przy użyciu zaufanych urządzeń w celu uzyskania dostępu do zmniejsza wymagania związane z przesyłaniem kody zabezpieczeń, gdy ktoś uzyskuje dostęp do konta Centrum deweloperów.  
*   W razie potrzeby do udzielania dostępu do konta Centrum deweloperów z komputera z niezaufanej należy ograniczyć dostęp do nie więcej niż pięciu deweloperów. W idealnym przypadku deweloperów powinna dostęp do konta z komputerów, które współużytkować ten sam geograficzne i lokalizacja w sieci.  
*   Często przejrzeć i sprawdzić informacji zabezpieczających.  
    *   Aby wyświetlić informacji o zabezpieczeniach, odwiedź zabezpieczeń strona Ustawienia znajdujący się w [account.live.com/proofs/Manage](https://account.live.com/proofs/Manage).

Danych konta Centrum deweloperów powinni mieć dostęp przede wszystkim z zaufanych komputerów. Ważne jest uzyskać dostęp z zaufanych komputerów, ponieważ ma limitu liczby kody wygenerowane na konto Centrum deweloperów na tydzień. Przy użyciu zaufanych komputerów umożliwia także najbezpieczniejsze i spójne środowisko logowania. 
*   Aby uzyskać więcej informacji na temat dodatkowe wytyczne dotyczące konta Centrum deweloperów i zabezpieczeń można znaleźć otwarcia strony konta dewelopera znajdujący się w [docs.microsoft.com/windows/uwp/publish/opening-a-developer-account](https://docs.microsoft.com/windows/uwp/publish/opening-a-developer-account). 

---

#### <a name="issue-microsoft-id-in-an-azure-ad-federated-domain"></a>Problem: Identyfikator firmy Microsoft w domenie federacyjnych usługi Azure AD  
Twoje konto firmowe mogą Sfederowane za pomocą usługi Azure Active Directory (Azure AD). Jeśli spróbujesz utworzyć identyfikator firmy Microsoft przy użyciu adresu firmowej poczty e-mail, które są Sfederowane z usługą Azure AD, otrzymasz błąd. Jeśli otrzymasz komunikat o błędzie, następnie należy skontaktować się z zespołem INFORMATYCZNYM, aby upewnić się, że Twoje konto jest Sfederowane za pomocą usługi Azure AD. Poczty e-mail dla usługi Azure AD federacyjnej jest to znany problem, a firma Microsoft pracuje nad rozpoznaje ją.  
*   Aby uzyskać więcej informacji na temat usługi Azure AD, można znaleźć w dokumentacji katalogu Active Azure strony znajduje się w [docs.microsoft.com/azure/active-directory](https://docs.microsoft.com/azure/active-directory).

Firma Microsoft zaleca obejście tego problemu. Wykonaj następujące kroki, aby utworzyć nowy adres e-mail w `outlook.com` domeny i Utwórz regułę do przekazywania komunikacji.  
1.  Przejdź na stronę tworzenia konta, a następnie kliknij przycisk na Get nowy link adresu e-mail. 
    *   Aby zarejestrować swój identyfikator firmy Microsoft, odwiedź stronę konta Utwórz, znajdujący się w [signup.live.com/signup](https://signup.live.com/signup).  
2.  Utwórz nowy adres e-mail, a następnie wprowadź hasło. Nowy identyfikator firmy Microsoft i skrzynki pocztowej programu poczty e-mail w `outlook.com` tworzenia domeny. Kontynuacja procesu rejestracji, konto zostanie utworzone.  

    >[!IMPORTANT]
    >Musisz podać adres e-mail adres lub listy dystrybucyjnej, która jest zarejestrowana jako identyfikator firmy Microsoft, aby zarejestrować się w Centrum deweloperów. Firma Microsoft zaleca, użyj listy dystrybucji, aby wyeliminować zależność od osób. Jeśli Twoje wiadomości e-mail adres lub listy dystrybucyjnej, nie jest zarejestrowany, następnie należy zarejestrować teraz.    

    >[!Important]
    >Jeśli Twoje dowolny adres e-mail znajduje się w `Microsoft` firmy domeny, a następnie nie jest możliwe na potrzeby rejestracji w Centrum deweloperów.  

3.  Po utworzeniu identyfikator firmy Microsoft z adresem e-mail programu Outlook, zaloguj się do skrzynką pocztową programu Outlook. Utwórz wiadomość e-mail regułę przesyłania. Regułę przesyłania wiadomości e-mail, należy przenieść wszystkie wiadomości e-mail, które są odbierane w pocztową programu Outlook na adres e-mail w domenie, który został utworzony w celu zarządzania kontem w portalu marketplace.  
    *   Aby zalogować się do skrzynką pocztową programu Outlook, odwiedź stronę programu Outlook, znajdujący się w [outlook.live.com/owa](https://outlook.live.com/owa).  
    *   Aby uzyskać więcej informacji na temat reguł przekazywania odwiedź Użyj reguł w aplikacji Outlook Web App, aby automatycznie przesyłać komunikaty do innej strony konto znajduje się w [support.office.com/article/Use-rules-in-Outlook-Web-App-to-automatically-forward-messages-to-another-account-1433e3a0-7fb0-4999-b536-50e05cb67fed](https://support.office.com/article/Use-rules-in-Outlook-Web-App-to-automatically-forward-messages-to-another-account-1433e3a0-7fb0-4999-b536-50e05cb67fed).  

1.  Reguły przekazywania wysyła wszystkie wiadomości e-mail i komunikacji trafiła do konta e-mail programu Outlook na adres e-mail w domenie zarejestrowany w firmie. Twoje `outlook.com` adresu e-mail musi używać do uwierzytelniania, zarówno w Centrum deweloperów, jak i w portalu Cloud Partner.  

## <a name="next-steps"></a>Kolejne kroki
*   Odwiedź stronę [portalu Azure Marketplace i AppSource — przewodnik dla wydawcy](./marketplace-publishers-guide.md) strony.  
 
---
