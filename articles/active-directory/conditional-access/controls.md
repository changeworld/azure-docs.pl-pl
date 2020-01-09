---
title: Kontrola dostępu w Azure Active Directory dostęp warunkowy
description: Dowiedz się, jak działają funkcje kontroli dostępu w Azure Active Directory dostęp warunkowy.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 12/20/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 342ec46aabafec975d780aa03fe75d7e3cf50497
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75424970"
---
# <a name="what-are-access-controls-in-azure-active-directory-conditional-access"></a>Co to są kontrole dostępu w Azure Active Directory dostęp warunkowy?

Za pomocą [dostępu warunkowego usługi Azure Active Directory (Azure AD)](../active-directory-conditional-access-azure-portal.md)można kontrolować sposób, w jaki autoryzowani użytkownicy uzyskują dostęp do aplikacji w chmurze. W zasadach dostępu warunkowego należy zdefiniować odpowiedź ("zrób to") z powodu wyzwolenia zasad ("w przypadku takiej sytuacji").

![Kontrola](./media/controls/10.png)

W kontekście dostępu warunkowego,

- "**Gdy tak się stanie**" jest wywoływane **warunki**
- "**Następnie zrób to**" jest nazywana **kontrolami dostępu**

Kombinacja instrukcji Condition z kontrolkami reprezentuje zasady dostępu warunkowego.

![Kontrola](./media/controls/61.png)

Każda kontrolka jest wymaganiem, które musi zostać spełnione przez osobę lub System logowania, lub ograniczeniem działania użytkownika po zalogowaniu się.

Istnieją dwa typy kontrolek:

- **Przyznaj kontrolki** dostęp do bramy
- **Kontrolki sesji** — aby ograniczyć dostęp w ramach sesji

W tym temacie objaśniono różne kontrolki, które są dostępne w trybie dostępu warunkowego usługi Azure AD. 

## <a name="grant-controls"></a>Przyznaj kontrolki

Dzięki udzieleniu kontroli można zablokować dostęp całkowicie lub zezwolić na dostęp z dodatkowymi wymaganiami poprzez wybranie żądanych kontrolek. W przypadku wielu formantów można wymagać:

- Wszystkie wybrane kontrolki do spełnienia (*i*)
- Jedna wybrana kontrolka do spełnienia (*lub*)

![Kontrola](./media/controls/18.png)

### <a name="multi-factor-authentication"></a>Uwierzytelnianie wieloskładnikowe

Za pomocą tego formantu można wymagać uwierzytelniania wieloskładnikowego w celu uzyskania dostępu do określonej aplikacji w chmurze. Ta kontrolka obsługuje następujących dostawców wieloskładnikowych:

- Usługa Azure Multi-Factor Authentication
- Dostawca lokalnego uwierzytelniania wieloskładnikowego, połączony z Active Directory Federation Services (AD FS).

Korzystanie z uwierzytelniania wieloskładnikowego pomaga chronić zasoby przed dostępem nieautoryzowanego użytkownika, którzy mieli dostęp do podstawowych poświadczeń prawidłowego użytkownika.

### <a name="compliant-device"></a>Zgodne urządzenie

Można skonfigurować zasady dostępu warunkowego, które są oparte na urządzeniach. Celem zasad dostępu warunkowego opartego na urządzeniach jest przyznanie dostępu do wybranych aplikacji w chmurze z [zarządzanych urządzeń](require-managed-devices.md). Wymaganie, aby urządzenie było oznaczone jako zgodne, było jedną z opcji, aby ograniczyć dostęp do zarządzanych urządzeń. Urządzenie może być oznaczone jako zgodne przez usługę Intune (dla dowolnego systemu operacyjnego urządzenia) lub przez system MDM innej firmy dla urządzeń z systemem Windows 10. Systemy zarządzania urządzeniami przenośnymi innych firm dla typów systemów operacyjnych innych niż Windows 10 nie są obsługiwane. 

Urządzenie musi zostać zarejestrowane w usłudze Azure AD, zanim będzie można je oznaczyć jako zgodne. Do zarejestrowania urządzenia dostępne są trzy opcje: 

- Urządzenia zarejestrowane w usłudze Azure AD
- Urządzenia dołączone do usługi Azure AD  
- Urządzenia dołączone hybrydowo do usługi Azure AD

Te trzy opcje zostały omówione w artykule [co to jest tożsamość urządzenia?](../devices/overview.md)

Aby uzyskać więcej informacji, zobacz [temat jak wymagać zarządzanych urządzeń do uzyskiwania dostępu do aplikacji w chmurze przy użyciu dostępu warunkowego](require-managed-devices.md).

### <a name="hybrid-azure-ad-joined-device"></a>Urządzenie dołączone do hybrydowej usługi Azure AD

Wymaganie hybrydowego urządzenia dołączonego do usługi Azure AD jest kolejną opcją, aby skonfigurować zasady dostępu warunkowego oparte na urządzeniach. To wymaganie dotyczy komputerów stacjonarnych, laptopów i tabletów z systemem Windows, które są przyłączone do Active Directory lokalnego. W przypadku wybrania tej opcji zasady dostępu warunkowego udzielą dostępu do prób dostępu do tych urządzeń, które są przyłączone do Active Directory lokalnego i Azure Active Directory. Urządzenia Mac nie obsługują hybrydowego sprzężenia usługi Azure AD.

Aby uzyskać więcej informacji, zobacz [Konfigurowanie zasad dostępu warunkowego opartego na urządzeniach Azure Active Directory](require-managed-devices.md).

### <a name="approved-client-app"></a>Zatwierdzona aplikacja kliencka

Ze względu na to, że pracownicy korzystają z urządzeń przenośnych zarówno do zadań osobistych, jak i służbowych, warto mieć możliwość ochrony danych firmowych, do których uzyskuje się dostęp za pomocą urządzeń, nawet w przypadku, gdy nie są zarządzane przez Ciebie.
[Zasady ochrony aplikacji usługi Intune](https://docs.microsoft.com/intune/app-protection-policy) umożliwiają ochronę danych firmy niezależnie od dowolnego rozwiązania do zarządzania urządzeniami mobilnymi (MDM).

W przypadku zatwierdzonych aplikacji klienckich można wymagać aplikacji klienckiej, która próbuje uzyskać dostęp do aplikacji w chmurze w celu obsługi [zasad ochrony aplikacji usługi Intune](https://docs.microsoft.com/intune/app-protection-policy). Na przykład możesz ograniczyć dostęp do usługi Exchange Online do aplikacji Outlook. Zasady dostępu warunkowego, które wymagają zatwierdzonych aplikacji klienckich, są również nazywane [zasadami dostępu warunkowego na podstawie aplikacji](app-based-conditional-access.md). Aby uzyskać listę obsługiwanych zatwierdzonych aplikacji klienckich, zobacz [wymagania dotyczące zatwierdzonej aplikacji klienckiej](technical-reference.md#approved-client-app-requirement).

### <a name="app-protection-policy-preview"></a>Zasady ochrony aplikacji (wersja zapoznawcza)

Ze względu na to, że pracownicy korzystają z urządzeń przenośnych zarówno do zadań osobistych, jak i służbowych, warto mieć możliwość ochrony danych firmowych, do których uzyskuje się dostęp za pomocą urządzeń, nawet w przypadku, gdy nie są zarządzane przez Ciebie.
[Zasady ochrony aplikacji usługi Intune](https://docs.microsoft.com/intune/app-protection-policy) umożliwiają ochronę danych firmy niezależnie od dowolnego rozwiązania do zarządzania urządzeniami mobilnymi (MDM).

Korzystając z zasad ochrony aplikacji, można ograniczyć dostęp do aplikacji klienckich, które zostały zgłoszone do usługi Azure AD, po odebraniu [zasad ochrony aplikacji w usłudze Intune](https://docs.microsoft.com/intune/app-protection-policy). Można na przykład ograniczyć dostęp do usługi Exchange Online do aplikacji Outlook, która ma zasady ochrony aplikacji w usłudze Intune. Zasady dostępu warunkowego, które wymagają zasad ochrony aplikacji, są również znane jako [zasady dostępu warunkowego opartego na ochronie aplikacji](app-protection-based-conditional-access.md). 

Urządzenie musi zostać zarejestrowane w usłudze Azure AD, aby można było oznaczyć aplikację jako chronioną przez zasady.

Aby uzyskać listę obsługiwanych aplikacji klienckich chronionych przez zasady, zobacz [wymagania dotyczące zasad ochrony aplikacji](technical-reference.md#app-protection-policy-requirement).

### <a name="terms-of-use"></a>Warunki użytkowania

Możesz wymagać od użytkownika w dzierżawie wyrazić zgodę na warunki użytkowania przed udzieleniem dostępu do zasobu. Jako administrator możesz skonfigurować i dostosować warunki użytkowania przez przekazanie dokumentu PDF. Jeśli użytkownik mieści się w zakresie kontroli dostępu do aplikacji, zostanie udzielony tylko w przypadku, gdy warunki użytkowania zostały uzgodnione.

## <a name="custom-controls-preview"></a>Formanty niestandardowe (wersja zapoznawcza)

Kontrolki niestandardowe są możliwością Azure Active Directory — wersja Premium wersji P1. W przypadku korzystania z kontrolek niestandardowych użytkownicy są przekierowywani do zgodnej usługi, aby spełnić dalsze wymagania poza Azure Active Directory. W celu spełnienia tej kontrolki przeglądarka użytkownika zostanie przekierowana do usługi zewnętrznej, wykonuje wszelkie wymagane operacje uwierzytelniania lub walidacji, a następnie zostanie przekierowana z powrotem do Azure Active Directory. Azure Active Directory weryfikuje odpowiedź i, jeśli użytkownik został pomyślnie uwierzytelniony lub zweryfikowany, użytkownik kontynuuje w przepływie dostępu warunkowego.

Te kontrolki umożliwiają użycie niektórych usług zewnętrznych lub niestandardowych jako kontroli dostępu warunkowego i ogólnie rozszerzając możliwości dostępu warunkowego.

Dostawcy oferujący obecnie zgodną usługę obejmują:

- [Duo — zabezpieczenia](https://duo.com/docs/azure-ca)
- [Entrust Datacard](https://www.entrustdatacard.com/products/authentication/intellitrust)
- [GSMA](https://mobileconnect.io/azure/)
- [Wyślij polecenie ping do tożsamości](https://documentation.pingidentity.com/pingid/pingidAdminGuide/index.shtml#pid_c_AzureADIntegration.html)
- [RSA](https://community.rsa.com/docs/DOC-81278)
- [SecureAuth](https://docs.secureauth.com/pages/viewpage.action?pageId=47238992#)
- [Silverfort](https://www.silverfort.io/company/using-silverfort-mfa-with-azure-active-directory/)
- [Adres VIP firmy Symantec](https://help.symantec.com/home/VIP_Integrate_with_Azure_AD)
- [Firmy Thales (firmy Gemalto)](https://resources.eu.safenetid.com/help/AzureMFA/Azure_Help/Index.htm)
- [Trusona](https://www.trusona.com/docs/azure-ad-integration-guide)

Aby uzyskać więcej informacji na temat tych usług, skontaktuj się bezpośrednio z dostawcami.

### <a name="creating-custom-controls"></a>Tworzenie niestandardowych formantów

Aby utworzyć kontrolkę niestandardową, należy najpierw skontaktować się z dostawcą, którego chcesz użyć. Każdy dostawca firmy innej niż Microsoft ma własny proces i wymagania, aby zarejestrować się, subskrybować lub w inny sposób stać się częścią usługi i wskazać, że chcesz zintegrować się z dostępem warunkowym. W tym momencie dostawca dostarczy blok danych w formacie JSON. Te dane umożliwiają dostawcy i dostęp warunkowy współdziałanie dla dzierżawy, tworzy nową kontrolkę i definiuje, jak dostęp warunkowy może stwierdzić, czy użytkownicy pomyślnie przeprowadzili weryfikację u dostawcy.

Kontrolki niestandardowe nie mogą być używane z automatyzacją usługi Identity Protection wymagającą uwierzytelniania wieloskładnikowego lub podniesienia poziomu ról w programie Privileged Identity Manager (PIM).

Skopiuj dane JSON, a następnie wklej je do powiązanego pola tekstowego. Nie wprowadzaj żadnych zmian w pliku JSON, chyba że jawnie zrozumiesz wprowadzaną zmianę. Wprowadzenie jakichkolwiek zmian może spowodować przerwanie połączenia między dostawcą i firmą Microsoft oraz zablokowanie użytkownika i użytkowników poza kontami.

Opcja tworzenia kontrolki niestandardowej znajduje się w sekcji **Zarządzanie** na stronie **dostęp warunkowy** .

![Kontrola](./media/controls/82.png)

Kliknięcie przycisku **Nowy formant niestandardowy**powoduje otwarcie bloku z polem tekstowym dla danych JSON formantu.  

![Kontrola](./media/controls/81.png)

### <a name="deleting-custom-controls"></a>Usuwanie formantów niestandardowych

Aby usunąć kontrolkę niestandardową, należy najpierw upewnić się, że nie jest ona używana w żadnym z zasad dostępu warunkowego. Po ukończeniu:

1. Przejdź do listy formantów niestandardowych
1. Kliknij przycisk...  
1. Wybierz pozycję **Usuń**.

### <a name="editing-custom-controls"></a>Edytowanie formantów niestandardowych

Aby edytować kontrolkę niestandardową, należy usunąć bieżącą kontrolkę i utworzyć nową kontrolkę z zaktualizowanymi informacjami.

## <a name="session-controls"></a>Kontrolki sesji

Kontrolki sesji umożliwiają ograniczoną obsługę w ramach aplikacji w chmurze. Kontrolki sesji są wymuszane przez aplikacje w chmurze i polegają na dodatkowych informacjach dostarczonych przez usługę Azure AD do aplikacji dotyczącej sesji.

![Kontrola](./media/controls/31.png)

### <a name="use-app-enforced-restrictions"></a>Użyj ograniczeń wymuszonych przez aplikację

Za pomocą tego formantu można wymagać, aby usługa Azure AD mogła przekazać informacje o urządzeniu do wybranych aplikacji w chmurze. Informacje o urządzeniu umożliwiają aplikacjom w chmurze dowiedzieć się, czy połączenie jest inicjowane z urządzenia zgodnego lub przyłączonego do domeny. Ta kontrolka obsługuje tylko usługi SharePoint Online i Exchange Online jako wybrane aplikacje w chmurze. Po wybraniu tej opcji aplikacja w chmurze korzysta z informacji o urządzeniu, aby zapewnić użytkownikom, w zależności od stanu urządzenia, z ograniczoną lub pełnym doświadczeniem.

Aby dowiedzieć się więcej, zobacz:

- [Włączanie ograniczonego dostępu za pomocą usługi SharePoint Online](https://aka.ms/spolimitedaccessdocs)
- [Włączanie ograniczonego dostępu za pomocą usługi Exchange Online](https://aka.ms/owalimitedaccess)

## <a name="next-steps"></a>Następne kroki

- Jeśli chcesz dowiedzieć się, jak skonfigurować zasady dostępu warunkowego, zobacz [Wymagaj uwierzytelniania wieloskładnikowego dla określonych aplikacji, Azure Active Directory dostępu warunkowego](app-based-mfa.md).
- Aby skonfigurować zasady dostępu warunkowego dla środowiska, zobacz [najlepsze rozwiązania dotyczące dostępu warunkowego w Azure Active Directory](best-practices.md).
