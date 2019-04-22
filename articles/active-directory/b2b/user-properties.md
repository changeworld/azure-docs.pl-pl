---
title: Właściwości B2B gościa user - usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Usługa Azure Active Directory B2B gościa użytkownika i Stany przed i po nim realizacja zaproszenia
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.custom: it-pro, seo-update-azuread-jan, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d4466e4ac7a4e818da6332254e3094eccbaf2b4
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59257607"
---
# <a name="properties-of-an-azure-active-directory-b2b-collaboration-user"></a>Właściwości użytkownika współpracy B2B usługi Azure Active Directory

Ten artykuł zawiera opis właściwości i obiektu użytkowników gości B2B w usłudze Azure Active Directory (Azure AD), przed i po nim realizacja zaproszenia. Usługa Azure AD business-to-business (B2B) współpracy użytkownik jest użytkownikiem z wartością UserType = gościa. Ten użytkownik-Gość zazwyczaj pochodzi z organizacji partnerskiej i ma ograniczone uprawnienia w katalogu zapraszający domyślnie.

W zależności od potrzeb organizacji zapraszającej użytkownik współpracy B2B usługi Azure AD może być w jednym z następujących stanów konta:

- Stan 1: Umieszczone w zewnętrznego wystąpienia usługi Azure AD, reprezentowana jako użytkownik-Gość w organizacji zapraszającej. W tym przypadku B2B użytkownik loguje się przy użyciu konta usługi Azure AD, należącego do dzierżawy usługi które otrzymało zaproszenie. Jeśli organizacja partnera nie korzysta z usługi Azure AD, tworzona jest nadal użytkownik-Gość w usłudze Azure AD. Wymagania są, że ich zrealizowanie zaproszenia i sprawdza swój adres e-mail, usługi Azure AD. Taki układ jest również nazywany just-in-time (JIT) dzierżawy lub "wirusowego" dzierżawców.

- Stan 2: Umieszczone w firmy Microsoft lub innego konta, reprezentowana jako użytkownik-Gość w organizacji hosta. W takim przypadku użytkownik-Gość zaloguje się za pomocą konta Microsoft lub kont społecznościowych (google.com lub podobny). Podczas realizacji oferty tożsamości dla zaproszonego użytkownika jest tworzona jako konta Microsoft, w katalogu organizacji zapraszającej.

- Stan 3: Umieszczone w organizacji hosta usługi Active Directory w środowisku lokalnym i zsynchronizowane z usługą organizacji hosta usługi Azure AD. Program Azure AD Connect umożliwia synchronizowanie kont partnerów w chmurze jako użytkownicy usługi Azure AD B2B z wartością UserType = gościa. Zobacz [udzielanie lokalnie zarządzanych przez partnera kont dostępu do zasobów w chmurze](hybrid-on-premises-to-cloud.md).

- Stan 4: Umieszczone w organizacji hosta usługi Azure AD z wartością UserType = gościa i poświadczenia, które zarządza hosta.

  ![Diagram przedstawiający stany czterech użytkowników](media/user-properties/redemption-diagram.png)


Teraz zobaczmy, jak wygląda użytkownik współpracy B2B usługi Azure AD w usłudze Azure AD.

### <a name="before-invitation-redemption"></a>Przed realizacja zaproszenia

Stan 1 i 2 stan konta są wynikiem zaprosisz użytkowników-gości do współpracy przy użyciu poświadczeń własnych użytkowników gości. Gdy zaproszenie początkowo jest wysyłana do użytkownika gościa, konto jest tworzone w katalogu. To konto nie ma żadnych poświadczeń skojarzonych z nią, ponieważ uwierzytelnianie jest wykonywane przez dostawcę tożsamości użytkownika gościa. **Źródła** dla konta gościa w katalogu zostaje ustalona **użytkownika Invited**. 

![Zrzut ekranu przedstawiający właściwości użytkownika przed realizacji oferty](media/user-properties/before-redemption.png)

### <a name="after-invitation-redemption"></a>Po realizacja zaproszenia

Po użytkownik-Gość akceptuje zaproszenie, **źródła** właściwość jest aktualizowane w oparciu o dostawcy tożsamości użytkownika gościa.

Dla użytkowników-gości w stanie 1 **źródła** jest **zewnętrznych usługi Azure Active Directory**.

![Stan 1 użytkownik-Gość po realizacji oferty](media/user-properties/after-redemption-state1.png)

Dla użytkowników-gości w stanie 2 **źródła** jest **Account Microsoft**.

![Użytkownik-Gość stan 2 po realizacji oferty](media/user-properties/after-redemption-state2.png)

Dla użytkowników-gości w stan 3 i 4 stanu **źródła** właściwość jest ustawiona na **usługi Azure Active Directory** lub **Windows Server Active Directory**, zgodnie z opisem w następnej sekcji.

## <a name="key-properties-of-the-azure-ad-b2b-collaboration-user"></a>Właściwości klucza obiektu użytkownika współpracy B2B usługi Azure AD
### <a name="usertype"></a>UserType
Ta właściwość określa relację użytkownika do dzierżawy hosta. Ta właściwość może mieć dwie wartości:
- Element członkowski: Ta wartość wskazuje pracownika organizacji hosta i użytkownik w organizacji, Lista płac. Na przykład ten użytkownik oczekuje, że mają dostęp do witryn tylko wewnętrzne. Ten użytkownik nie jest uważany za współpracownika zewnętrznego.

- Guest: Ta wartość wskazuje użytkownika, który nie jest traktowane jako wewnętrzne firmy, takich jak współpracownika zewnętrznego, partnera lub klienta. Taki użytkownik nie powinien otrzymywać wewnętrznym dokumencie przedstawia Dyrektor Generalny firmy lub korzyści firmy, na przykład.

  > [!NOTE]
  > UserType nie ma związku jak użytkownik się zaloguje, rola katalogu użytkowników i tak dalej. Właściwość ta po prostu wskazuje relację użytkownika z organizacji hosta i umożliwia organizacji wymusić zasady, które są zależne od tej właściwości.

### <a name="source"></a>Element źródłowy
Ta właściwość wskazuje, jak użytkownik się zaloguje.

- Zaproszono użytkownika: Ten użytkownik został zaproszony, ale nie ma jeszcze zrealizować zaproszenia.

- Zewnętrzne usługi Active Directory: Ten użytkownik jest umieszczone w organizację zewnętrzną i uwierzytelnia się za pomocą konta usługi Azure AD, który należy do innej organizacji. Ten typ logowania odnosi się do stanu 1.

- Konto Microsoft: Ten użytkownik jest umieszczone na koncie Microsoft i uwierzytelnia się za pomocą konta Microsoft. Ten typ logowania odnosi się do stanu 2.

- Windows Server Active Directory: Ten użytkownik jest zalogowany z Active Directory w środowisku lokalnym, należącym do tej organizacji. Ten typ logowania odnosi się do stanu 3.

- Azure Active Directory: Ten użytkownik jest uwierzytelniany przy użyciu konta usługi Azure AD, który należy do tej organizacji. Ten typ logowania odnosi się do stanu 4.
  > [!NOTE]
  > Źródło i UserType jest niezależne właściwości. Wartość źródła nie oznacza dla UserType określoną wartość.

## <a name="can-azure-ad-b2b-users-be-added-as-members-instead-of-guests"></a>Użytkownicy usługi Azure AD B2B można dodać jako elementy członkowskie zamiast gości?
Zazwyczaj użytkownik B2B usługi Azure AD i użytkownika-gościa są równoznaczny. Dlatego użytkownik współpracy B2B usługi Azure AD zostanie dodany jako użytkownik z wartością UserType = gościa domyślnie. Jednak w niektórych przypadkach organizacji partnera jest członkiem większej organizacji, do której należy również organizacji hosta. Jeśli tak, traktować użytkowników w organizacji partnera jako elementy członkowskie zamiast Goście mają organizacji hosta. Użyj interfejsów API usługi Azure AD B2B zaproszenie Manager, aby dodać lub zaprosić użytkownika z organizacji partnerskiej organizacji hosta jako członka.

## <a name="filter-for-guest-users-in-the-directory"></a>Filtr dla użytkowników-gości w katalogu

![Zrzut ekranu przedstawiający filtr dla użytkowników-gości](media/user-properties/filter-guest-users.png)

## <a name="convert-usertype"></a>Konwertuj UserType
Istnieje możliwość konwersji UserType elementu członkowskiego gościa i na odwrót przy użyciu programu PowerShell. Jednak Właściwość UserType reprezentuje relację użytkownika w organizacji. W związku z tym należy zmienić tylko wtedy, gdy właściwość relacja użytkownika do zmian w organizacji. Jeśli relacja użytkownik zmieni się, należy zmienić nazwę główną użytkownika (UPN)? Użytkownik powinien nadal mieć dostęp do tych samych zasobów? Powinien być przypisany skrzynki pocztowej Nie zaleca się zmianę UserType przy użyciu programu PowerShell jako atomic działania. Ponadto w przypadku, gdy ta właściwość stanie się niezmienne przy użyciu programu PowerShell, nie zaleca się zależna od tej wartości.

## <a name="remove-guest-user-limitations"></a>Usuń ograniczenia użytkownika gościa
Może to być przypadki, w którym chcesz zapewnić użytkownikom gościa z wyższymi uprawnieniami. Można dodać użytkownika-gościa do dowolnej roli i nawet usuwać domyślne ograniczenia użytkownika gościa w katalogu, aby dać użytkownikowi takie same uprawnienia jak członkowie.

Istnieje możliwość wyłączenia ograniczenia domyślne, tak aby użytkownik-Gość w katalogu firmy ma takie same uprawnienia, jak użytkownika elementu członkowskiego.

![Zrzut ekranu przedstawiający opcję w ustawieniach użytkownika dla użytkowników zewnętrznych](media/user-properties/remove-guest-limitations.png)

## <a name="can-i-make-guest-users-visible-in-the-exchange-global-address-list"></a>Czy mogę utworzyć użytkowników-gości widoczne w globalnej listy adresowej Exchange?
Tak. Domyślnie obiekty gościa nie są widoczne w Twojej organizacji globalnej liście adresowej, ale można użyć programu PowerShell usługi Azure Active Directory, aby stały się widoczne. Aby uzyskać więcej informacji, zobacz **mogę sprawdzić, że obiekty gościa widoczna na globalnej liście adresowej?** w [zarządzanie dostępem gości w grup usługi Office 365](https://docs.microsoft.com/office365/admin/create-groups/manage-guest-access-in-groups?redirectSourcePath=%252fen-us%252farticle%252fmanage-guest-access-in-office-365-groups-9de497a9-2f5c-43d6-ae18-767f2e6fe6e0&view=o365-worldwide#faq). 

## <a name="next-steps"></a>Kolejne kroki

* [Czym jest współpraca B2B w usłudze Azure AD?](what-is-b2b.md)
* [Tokeny użytkownika współpracy B2B](user-token.md)
* [Mapowanie oświadczeń użytkowników współpracy B2B](claims-mapping.md)
