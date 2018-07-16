---
title: Właściwości użytkownika współpracy B2B usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Właściwości użytkownika współpracy w usłudze Azure Active Directory B2B są konfigurowane
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/25/2017
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 0cfd7888acf942e4af875c37c2472ff086f9119b
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39059641"
---
# <a name="properties-of-an-azure-active-directory-b2b-collaboration-user"></a>Właściwości użytkownika współpracy B2B usługi Azure Active Directory

Azure Active Directory (Azure AD) business-to-business (B2B) współpracy użytkownik jest użytkownikiem z wartością UserType = gościa. Ten użytkownik-Gość zazwyczaj pochodzi z organizacji partnerskiej i ma ograniczone uprawnienia w katalogu zapraszający domyślnie.

W zależności od potrzeb organizacji zapraszającej użytkownik współpracy B2B usługi Azure AD może być w jednym z następujących stanów konta:

- Stan 1: Umieszczone w zewnętrznego wystąpienia usługi Azure AD, reprezentowana jako użytkownik-Gość w organizacji zapraszającej. W tym przypadku B2B użytkownik loguje się przy użyciu konta usługi Azure AD, należącego do dzierżawy usługi które otrzymało zaproszenie. Jeśli organizacja partnera nie korzysta z usługi Azure AD, tworzona jest nadal użytkownik-Gość w usłudze Azure AD. Wymagania są, że ich zrealizowanie zaproszenia i sprawdza swój adres e-mail, usługi Azure AD. Taki układ jest również nazywany just-in-time (JIT) dzierżawy lub "wirusowego" dzierżawców.

- Stan 2: Umieszczone w konto Microsoft, reprezentowana jako użytkownik-Gość w organizacji hosta. W takim przypadku użytkownik-Gość zaloguje się za pomocą konta Microsoft. Tożsamości dla zaproszonego użytkownika sieci społecznościowej (google.com lub podobny), który nie jest kontem Microsoft jest tworzony jako konto Microsoft podczas realizacji oferty.

- Stan 3: Umieszczone w organizacji hosta usługi Active Directory w środowisku lokalnym i zsynchronizowane z usługą organizacji hosta usługi Azure AD. W tej wersji należy ręcznie zmienić UserType takich użytkowników w chmurze za pomocą programu PowerShell.

- Stan 4: Umieszczone w organizacji hosta usługi Azure AD z wartością UserType = gościa i poświadczenia, które zarządza hosta.

  ![Wyświetlanie inicjały zapraszającej](media/user-properties/redemption-diagram.png)


Teraz zobaczmy, jak wygląda użytkownik współpracy B2B usługi Azure AD w stanie 1 w usłudze Azure AD.

### <a name="before-invitation-redemption"></a>Przed realizacja zaproszenia

![Przed realizacji oferty](media/user-properties/before-redemption.png)

### <a name="after-invitation-redemption"></a>Po realizacja zaproszenia

![Po realizacji oferty](media/user-properties/after-redemption.png)

## <a name="key-properties-of-the-azure-ad-b2b-collaboration-user"></a>Właściwości klucza obiektu użytkownika współpracy B2B usługi Azure AD
### <a name="usertype"></a>UserType
Ta właściwość określa relację użytkownika do dzierżawy hosta. Ta właściwość może mieć dwie wartości:
- Element członkowski: Ta wartość wskazuje pracownika organizacji hosta i użytkownik w organizacji, Lista płac. Na przykład ten użytkownik oczekuje, że mają dostęp do witryn tylko wewnętrzne. Ten użytkownik nie będzie uwzględniane współpracownika zewnętrznego.

- Gość: Ta wartość wskazuje użytkownika, który nie jest traktowane jako wewnętrzne firmy, takich jak współpracownika zewnętrznego, partner, klienta lub podobne użytkownika. Taki użytkownik nie oczekiwano odbieranie w wewnętrznym dokumencie przedstawia Dyrektor Generalny i korzyści firmy, na przykład.

  > [!NOTE]
  > UserType nie ma związku jak użytkownik się zaloguje, rola katalogu użytkowników i tak dalej. Właściwość ta po prostu wskazuje relację użytkownika z organizacji hosta i umożliwia organizacji wymusić zasady, które są zależne od tej właściwości.

### <a name="source"></a>Element źródłowy
Ta właściwość wskazuje, jak użytkownik się zaloguje.

- Zaproszono użytkownika: Ten użytkownik został zaproszony, ale nie ma jeszcze zrealizować zaproszenia.

- Zewnętrzne usługi Active Directory: Ten użytkownik jest umieszczone w organizację zewnętrzną i uwierzytelnia się za pomocą konta usługi Azure AD, który należy do innej organizacji. Ten typ logowania odnosi się do stanu 1.

- Konto Microsoft: ten użytkownik jest umieszczone na koncie Microsoft i uwierzytelnia się za pomocą konta Microsoft. Ten typ logowania odnosi się do stanu 2.

- Windows Server Active Directory: Ten użytkownik jest zalogowany z Active Directory w środowisku lokalnym, należącym do tej organizacji. Ten typ logowania odnosi się do stanu 3.

- Usługa Azure Active Directory: Ten użytkownik jest uwierzytelniany przy użyciu konta usługi Azure AD, który należy do tej organizacji. Ten typ logowania odnosi się do stanu 4.
  > [!NOTE]
  > Źródło i UserType jest niezależne właściwości. Wartość źródła nie oznacza dla UserType określoną wartość.

## <a name="can-azure-ad-b2b-users-be-added-as-members-instead-of-guests"></a>Użytkownicy usługi Azure AD B2B można dodać jako elementy członkowskie zamiast gości?
Zazwyczaj użytkownik B2B usługi Azure AD i użytkownika-gościa są równoznaczny. Dlatego użytkownik współpracy B2B usługi Azure AD zostanie dodany jako użytkownik z wartością UserType = gościa domyślnie. Jednak w niektórych przypadkach organizacji partnera jest członkiem większej organizacji, do której należy również organizacji hosta. Jeśli tak, traktować użytkowników w organizacji partnera jako elementy członkowskie zamiast Goście mają organizacji hosta. Użyj interfejsów API usługi Azure AD B2B zaproszenie Manager, aby dodać lub zaprosić użytkownika z organizacji partnerskiej organizacji hosta jako członka.

## <a name="filter-for-guest-users-in-the-directory"></a>Filtr dla użytkowników-gości w katalogu

![Filtruj użytkowników-gości](media/user-properties/filter-guest-users.png)

## <a name="convert-usertype"></a>Konwertuj UserType
Obecnie jest możliwe dla użytkowników przekonwertować UserType od elementu członkowskiego gościa i na odwrót przy użyciu programu PowerShell. Jednak Właściwość UserType powinien reprezentują relację użytkownika w organizacji. W związku z tym wartość tej właściwości należy zmieniać tylko wtedy, gdy zmieni się Relacja użytkownik w organizacji. Jeśli relacja użytkownik zmieni się, należy problemy, takie jak czy główna nazwa użytkownika (UPN), należy zmienić, rozwiązać? Użytkownik powinien nadal mieć dostęp do tych samych zasobów? Powinien być przypisany skrzynki pocztowej W związku z tym zaleca się zmianę UserType przy użyciu programu PowerShell jako atomic działania. Ponadto w przypadku, gdy ta właściwość stanie się niezmienne przy użyciu programu PowerShell, firma Microsoft nie zaleca się zależna od tej wartości.

## <a name="remove-guest-user-limitations"></a>Usuń ograniczenia użytkownika gościa
Może to być przypadki, w którym chcesz zapewnić użytkownikom gościa z wyższymi uprawnieniami. Można dodać użytkownika-gościa do dowolnej roli i nawet usuwać domyślne ograniczenia użytkownika gościa w katalogu, aby dać użytkownikowi takie same uprawnienia jak członkowie.

Istnieje możliwość wyłączyć domyślne ograniczenia użytkownika gościa, aby użytkownik-Gość w katalogu firmy otrzymuje takie same uprawnienia, jak użytkownika elementu członkowskiego.

![Usuń ograniczenia użytkownika gościa](media/user-properties/remove-guest-limitations.png)

## <a name="can-i-make-guest-users-visible-in-the-exchange-global-address-list"></a>Czy mogę utworzyć użytkowników-gości widoczne w globalnej listy adresowej Exchange?
Tak. Domyślnie obiekty gościa nie są widoczne w Twojej organizacji globalnej liście adresowej, ale można użyć programu PowerShell usługi Azure Active Directory, aby stały się widoczne. Aby uzyskać więcej informacji, zobacz **mogę sprawdzić, że obiekty gościa widoczna na globalnej liście adresowej?** w [dostęp gościa w grup usługi Office 365](https://support.office.com/article/guest-access-in-office-365-groups-bfc7a840-868f-4fd6-a390-f347bf51aff6#PickTab=FAQ). 

## <a name="next-steps"></a>Kolejne kroki

* [Czym jest współpraca B2B w usłudze Azure AD?](what-is-b2b.md)
* [Tokeny użytkownika współpracy B2B](user-token.md)
* [Mapowanie oświadczeń użytkowników współpracy B2B](claims-mapping.md)
