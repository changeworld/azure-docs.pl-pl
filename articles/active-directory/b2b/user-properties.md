---
title: Właściwości użytkownika-gościa B2B — usługa Azure Active Directory | Dokumenty firmy Microsoft
description: Właściwości i stany użytkownika gościa usługi Azure Active Directory B2B przed i po realizacji zaproszenia
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 40f5002e361653614c966dc43301afa83eb7b200
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050797"
---
# <a name="properties-of-an-azure-active-directory-b2b-collaboration-user"></a>Właściwości użytkownika współpracy B2B usługi Azure Active Directory

W tym artykule opisano właściwości i stany obiektu użytkownika gościa B2B w usłudze Azure Active Directory (Azure AD) przed i po realizacji zaproszenia. Użytkownik współpracy biznesowej usługi Azure AD business (B2B) jest użytkownikiem z usertype = gość. Ten użytkownik-gość zazwyczaj pochodzi z organizacji partnerskiej i domyślnie ma ograniczone uprawnienia w katalogu zapraszania.

W zależności od potrzeb zapraszania organizacji użytkownik współpracy usługi Azure AD B2B może znajdować się w jednym z następujących stanów konta:

- Stan 1: Homed w zewnętrznym wystąpieniu usługi Azure AD i reprezentowane jako użytkownik-gość w organizacji zapraszania. W takim przypadku użytkownik B2B loguje się przy użyciu konta usługi Azure AD, które należy do zaproszonej dzierżawy. Jeśli organizacja partnerska nie korzysta z usługi Azure AD, użytkownik-gość w usłudze Azure AD jest nadal tworzony. Wymagania są, że realizują swoje zaproszenie i usługi Azure AD weryfikuje ich adres e-mail. Układ ten jest również nazywany just-in-time (JIT) najmu lub "wirusowe" najmu.

   > [!IMPORTANT]
   > **Począwszy od 31 marca 2021**r. firma Microsoft nie będzie już obsługiwać realizacji zaproszeń, tworząc niezarządzane konta usługi Azure AD i dzierżawy scenariuszy współpracy B2B. W ramach przygotowań zachęcamy klientów do wyrażenia zgody na [jednorazowe uwierzytelnianie kodem dostępu pocztą e-mail.](one-time-passcode.md) Cieszymy się z waszych opinii na temat tej publicznej funkcji podglądu i cieszymy się, że możemy stworzyć jeszcze więcej sposobów współpracy.

- Stan 2: Homed w microsoft lub inne konto i reprezentowane jako użytkownik-gość w organizacji hosta. W takim przypadku użytkownik-gość loguje się za pomocą konta Microsoft lub konta społecznościowego (google.com lub podobnego). Tożsamość zaproszonego użytkownika jest tworzona jako konto Microsoft w katalogu zapraszającej organizacji podczas realizacji oferty.

- Stan 3: Homed w lokalnej lokalnej usłudze Active Directory organizacji hosta i zsynchronizowany z usługą Azure AD organizacji hosta. Za pomocą usługi Azure AD Connect można synchronizować konta partnerów z chmurą jako użytkownicy usługi Azure AD B2B z usertype = gość. Zobacz [Przyznawanie lokalnie zarządzanych kont partnerów dostępu do zasobów w chmurze](hybrid-on-premises-to-cloud.md).

- Stan 4: Homed w organizacji hosta usługi Azure AD z UserType = Gość i poświadczenia, które zarządza organizacja hosta.

  ![Diagram przedstawiający cztery stany użytkownika](media/user-properties/redemption-diagram.png)


Teraz zobaczmy, jak wygląda użytkownik współpracy usługi Azure AD B2B w usłudze Azure AD.

### <a name="before-invitation-redemption"></a>Przed wykupem zaproszenia

Konta stanu 1 i stan 2 są wynikiem zapraszania użytkowników-gości do współpracy przy użyciu własnych poświadczeń użytkowników-gości. Gdy zaproszenie jest początkowo wysyłane do użytkownika-gościa, w katalogu jest tworzone konto. To konto nie ma żadnych poświadczeń skojarzonych z nim, ponieważ uwierzytelnianie jest wykonywane przez dostawcę tożsamości użytkownika gościa. **Właściwość Źródło** dla konta użytkownika gościa w katalogu jest ustawiona na **Zaproszonego użytkownika**. 

![Zrzut ekranu przedstawiający właściwości użytkownika przed realizacją oferty](media/user-properties/before-redemption.png)

### <a name="after-invitation-redemption"></a>Po odkupieniu zaproszenia

Po użytkownik-gość zaakceptuje zaproszenie, **Source** właściwość jest aktualizowana na podstawie dostawcy tożsamości użytkownika gościa.

Dla użytkowników-gości w stanie 1 **źródłem** jest **zewnętrzna usługa Azure Active Directory**.

![Stan 1 użytkownik gości po realizacji oferty](media/user-properties/after-redemption-state1.png)

Dla użytkowników-gości w stanie 2 **źródłem** jest **konto Microsoft**.

![Stan 2 użytkownik gości po realizacji oferty](media/user-properties/after-redemption-state2.png)

Dla użytkowników-gości w stanie 3 i 4 **właściwość Source** jest ustawiona na **usługę Azure Active Directory** lub **windows server Active Directory**, zgodnie z opisem w następnej sekcji.

## <a name="key-properties-of-the-azure-ad-b2b-collaboration-user"></a>Najważniejsze właściwości użytkownika współpracy usługi Azure AD B2B
### <a name="usertype"></a>UserType
Ta właściwość wskazuje relację użytkownika z dzierżawą hosta. Ta właściwość może mieć dwie wartości:
- Członek: Ta wartość wskazuje pracownika organizacji hosta i użytkownika na liście płac organizacji. Na przykład ten użytkownik oczekuje, że będzie miał dostęp do witryn tylko wewnętrznych. Ten użytkownik nie jest uważany za zewnętrznego współpracownika.

- Gość: Ta wartość wskazuje użytkownika, który nie jest uważany za wewnętrznego firmy, takiego jak zewnętrzny współpracownik, partner lub klient. Nie oczekuje się, że taki użytkownik otrzyma wewnętrzną notatkę dyrektora generalnego ani otrzyma korzyści firmy, na przykład.

  > [!NOTE]
  > UserType nie ma żadnego związku z tym, jak użytkownik loguje się, rolę katalogu użytkownika i tak dalej. Ta właściwość po prostu wskazuje relację użytkownika z organizacją hosta i umożliwia organizacji wymuszanie zasad, które zależą od tej właściwości.

### <a name="source"></a>Element źródłowy
Ta właściwość wskazuje, jak użytkownik loguje się.

- Zaproszony użytkownik: Ten użytkownik został zaproszony, ale nie zrealizował jeszcze zaproszenia.

- Zewnętrzna usługa Azure Active Directory: Ten użytkownik jest w domu w organizacji zewnętrznej i uwierzytelnia się przy użyciu konta usługi Azure AD, który należy do innej organizacji. Ten typ logowania odpowiada stan 1.

- Konto Microsoft: Ten użytkownik jest opiekowy na koncie Microsoft i uwierzytelnia się przy użyciu konta Microsoft. Ten typ logowania odpowiada stan 2.

- Usługa Active Directory systemu Windows Server: ten użytkownik jest zalogowany z lokalnej usługi Active Directory należącej do tej organizacji. Ten typ logowania odpowiada stan 3.

- Usługa Azure Active Directory: Ten użytkownik uwierzytelnia się przy użyciu konta usługi Azure AD należącego do tej organizacji. Ten typ logowania odpowiada stan 4.
  > [!NOTE]
  > Źródło i UserType są niezależnymi właściwościami. Wartość Source nie oznacza określonej wartości dla UserType.

## <a name="can-azure-ad-b2b-users-be-added-as-members-instead-of-guests"></a>Czy użytkownicy usługi Azure AD B2B mogą być dodawane jako członkowie zamiast gości?
Zazwyczaj użytkownik usługi Azure AD B2B i użytkownik-gość są synonimami. W związku z tym użytkownik współpracy usługi Azure AD B2B jest dodawany jako użytkownik z UserType = Gość domyślnie. Jednak w niektórych przypadkach organizacja partnerska jest członkiem większej organizacji, do której należy również organizacja hosta. Jeśli tak, organizacja hosta może chcieć traktować użytkowników w organizacji partnerskiej jako członków, a nie gości. Użyj interfejsów API Menedżera zaproszeń usługi Azure AD B2B, aby dodać lub zaprosić użytkownika z organizacji partnerskiej do organizacji hosta jako członka.

## <a name="filter-for-guest-users-in-the-directory"></a>Filtr dla użytkowników-gości w katalogu

![Zrzut ekranu przedstawiający filtr dla użytkowników-gości](media/user-properties/filter-guest-users.png)

## <a name="convert-usertype"></a>Konwertowanie typu użytkownika
Istnieje możliwość konwersji UserType z Elementu członkowskiego do gościa i odwrotnie przy użyciu programu PowerShell. Jednak UserType właściwość reprezentuje relację użytkownika z organizacją. W związku z tym należy zmienić tę właściwość tylko wtedy, gdy relacja użytkownika z organizacją ulegnie zmianie. Jeśli relacja użytkownika ulegnie zmianie, czy główna nazwa użytkownika (UPN) powinna ulec zmianie? Czy użytkownik powinien nadal mieć dostęp do tych samych zasobów? Czy należy przypisać skrzynkę pocztową? Nie zaleca się zmiany UserType przy użyciu programu PowerShell jako działania niepodzielnego. Ponadto w przypadku, gdy ta właściwość staje się niezmienne przy użyciu programu PowerShell, nie zaleca się przy zależności od tej wartości.

## <a name="remove-guest-user-limitations"></a>Usuwanie ograniczeń użytkownika-gościa
Mogą wystąpić przypadki, w których chcesz przyznać użytkownikom-gościom wyższe uprawnienia. Można dodać użytkownika-gościa do dowolnej roli, a nawet usunąć domyślne ograniczenia użytkownika gościa w katalogu, aby nadać użytkownikowi takie same uprawnienia jak członkowie.

Jest możliwe, aby wyłączyć domyślne ograniczenia, tak aby użytkownik-gość w katalogu firmy ma takie same uprawnienia jak użytkownik członkowski.

![Zrzut ekranu przedstawiający opcję Użytkownicy zewnętrzni w ustawieniach użytkownika](media/user-properties/remove-guest-limitations.png)

## <a name="can-i-make-guest-users-visible-in-the-exchange-global-address-list"></a>Czy mogę uwidocznić użytkowników-gości na globalnej liście adresów programu Exchange?
Tak. Domyślnie obiekty gościa nie są widoczne na globalnej liście adresów organizacji, ale można użyć programu Azure Active Directory PowerShell, aby były widoczne. Aby uzyskać szczegółowe informacje, zobacz **Czy obiekty gościa mogą być widoczne na globalnej liście adresów?** [Manage guest access in Office 365 Groups](https://docs.microsoft.com/office365/admin/create-groups/manage-guest-access-in-groups?redirectSourcePath=%252fen-us%252farticle%252fmanage-guest-access-in-office-365-groups-9de497a9-2f5c-43d6-ae18-767f2e6fe6e0&view=o365-worldwide#add-guests-to-the-global-address-list) 

## <a name="next-steps"></a>Następne kroki

* [Czym jest współpraca B2B w usłudze Azure AD?](what-is-b2b.md)
* [Tokeny użytkowników współpracy B2B](user-token.md)
* [Mapowanie oświadczeń użytkowników współpracy B2B](claims-mapping.md)
