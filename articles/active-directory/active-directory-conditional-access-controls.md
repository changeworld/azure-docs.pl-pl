---
title: Co to są kontroli dostępu w usłudze Azure Active Directory dostępu warunkowego? | Microsoft Docs
description: Dowiedz się, jak dostęp do formantów w pracy dostępu warunkowego w usłudze Azure Active Directory.
services: active-directory
keywords: dostęp warunkowy do aplikacji, dostęp warunkowy przy użyciu usługi Azure AD, bezpieczny dostęp do zasobów firmy, zasady dostępu warunkowego
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.component: protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/13/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: dc1e3fd3984d0ccbfb0565a3216ec907894e88ea
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37028952"
---
# <a name="what-are-access-controls-in-azure-active-directory-conditional-access"></a>Co to są kontroli dostępu w usłudze Azure Active Directory dostępu warunkowego? 

Z [dostępu warunkowego w usłudze Azure Active Directory (Azure AD)](active-directory-conditional-access-azure-portal.md), można kontrolować sposób autoryzowanym użytkownikom dostępu aplikacji w chmurze. W zasadach dostępu warunkowego zdefiniowanego odpowiedzi ("tym") Przyczyna służącą do wyzwalania zasad ("w takim przypadku"). 

![Kontrola](./media/active-directory-conditional-access-controls/10.png)


W kontekście dostępu warunkowego 

- "**w takim przypadku**" jest wywoływana **warunków**

- "**To zrobić**" jest wywoływana **dostęp do formantów**


Kombinacja instrukcji warunku z formantów reprezentuje zasady dostępu warunkowego.

![Kontrola](./media/active-directory-conditional-access-controls/61.png)

Każdej kontrolki jest wymagania, które muszą być spełnione przez osobę lub system logowania lub ograniczenie użytkownika można zrobić po zalogowaniu się. 

Istnieją dwa typy formantów: 

- **Formanty GRANT** — aby brama dostępu

- **Formanty sesji** — w celu ograniczenia dostępu w ramach sesji

W tym temacie wyjaśniono różne formantów, które są dostępne w usłudze Azure AD dostęp warunkowy. 

## <a name="grant-controls"></a>Udziel kontroli

Udziel kontroli możesz całkowicie blokowanie dostępu lub zezwolić na dostęp z dodatkowymi wymaganiami, wybierając żądane formanty. Wiele formantów można wymagać:

- Wszystkie wybrane formanty, które należy spełnić (*i*) 
- Jeden zaznaczony formant do spełnienia (*lub*)

![Kontrola](./media/active-directory-conditional-access-controls/17.png)



### <a name="multi-factor-authentication"></a>Uwierzytelnianie wieloskładnikowe

Aby wymusić uwierzytelnianie wieloskładnikowe na dostęp do aplikacji określonej chmury, można użyć tego formantu. Ten formant obsługuje następujących dostawców wieloskładnikowego: 

- Azure Multi-Factor Authentication 

- Dostawcy uwierzytelniania wieloskładnikowego lokalnymi łączyć się z usługi Active Directory Federation Services (AD FS).
 
Przy użyciu usługi Multi-Factor authentication pomaga chronić zasoby z uzyskiwany przez nieautoryzowanego użytkownika, który może mieć uzyskał dostęp do podstawowego poświadczenia prawidłowego użytkownika.



### <a name="compliant-device"></a>Zgodne urządzenie

Można skonfigurować zasady dostępu warunkowego, które są oparte na urządzeniu. Celem zasad dostępu warunkowego opartego na urządzeniu jest udzielić dostępu do zasobów skonfigurowany tylko z [zarządzanych urządzeń](active-directory-conditional-access-policy-connected-applications.md#managed-devices). Wymaganie zgodnego urządzenia jest jedną z opcji należy zdefiniować urządzenia zarządzanego. Jeśli ta opcja jest zaznaczona, zasady dostępu warunkowego udziela dostępu do dostępu podjętych z urządzeniami, które są [zarejestrowany](device-management-introduction.md) do platformy Azure Active Directory i są oznaczone jako zgodne przez usługę Intune (dla wszystkich urządzeń systemu operacyjnego) lub przez użytkownika system MDM innej firmy dla urządzeń z systemem Windows 10. Systemy zarządzania urządzeniami Przenośnymi innej firmy dla systemu operacyjnego urządzenia o typie innym niż Windows 10 nie są obsługiwane.

Aby uzyskać więcej informacji, zobacz [Konfigurowanie zasad dostępu warunkowego opartego na urządzenia usługi Azure Active Directory](active-directory-conditional-access-policy-connected-applications.md).

### <a name="hybrid-azure-ad-joined-device"></a>Urządzenia dołączone do hybrydowej usługi Azure AD

Wymaganie hybrydowego urządzenia dołączone do usługi Azure AD jest inną opcją, należy skonfigurować zasady dostępu warunkowego opartego na urządzeniu. To wymaganie dotyczy komputerów stacjonarnych, laptopów i tabletów przedsiębiorstwa, które są częścią lokalnej usługi Active Directory systemu Windows. Jeśli ta opcja jest zaznaczona, zasady dostępu warunkowego przyznaje próby dostępu do dostępu z urządzeń, które są częścią lokalnej usługi Active Directory i Azure Active Directory.  

Aby uzyskać więcej informacji, zobacz [Konfigurowanie zasad dostępu warunkowego opartego na urządzenia usługi Azure Active Directory](active-directory-conditional-access-policy-connected-applications.md).





### <a name="approved-client-app"></a>Aplikacja kliencka zatwierdzone

Pracownicy używają urządzeń przenośnych dla obu osobiste i zadań, możesz mieć możliwość ochrony danych firmowych dostęp za pomocą urządzeń, nawet w przypadku, gdy nie są zarządzane przez użytkownika.
Można użyć [zasad ochrony aplikacji usługi Intune](https://docs.microsoft.com/intune/app-protection-policy) w celu ochrony danych firmowych, niezależnie od wszelkich rozwiązań do zarządzania urządzeniami przenośnymi (MDM).


W aplikacjach klienckich zatwierdzone może wymagać aplikację klienta, który próbuje uzyskać dostęp do obsługi aplikacji w chmurze [zasad ochrony aplikacji usługi Intune](https://docs.microsoft.com/intune/app-protection-policy). Na przykład można ograniczyć dostęp do usługi Exchange Online do aplikacji Outlook. Zasady dostępu warunkowego, która wymaga aplikacji klienta zatwierdzonych jest także znana jako [zasad dostępu warunkowego opartego na aplikacji](active-directory-conditional-access-mam.md). Aby uzyskać listę obsługiwanych klientów zatwierdzonych aplikacji, zobacz [zatwierdzone wymagania aplikacji klienta](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement).


### <a name="terms-of-use"></a>Warunki użytkowania

Użytkownik może być wymagane w dzierżawie, aby wyrazić zgodę na warunki użytkowania, zanim zostanie im przyznany dostęp do zasobu. Administrator może skonfigurować i dostosować warunki użytkowania przekazując dokumentu PDF. Jeśli użytkownik znajduje się w zakresie tej kontroli dostępu do aplikacji jest przyznane tylko jeśli zostały Akceptuję warunki użytkowania. 


### <a name="custom-controls"></a>Formanty niestandardowe 

Formanty niestandardowe można tworzyć w dostępu warunkowego, które przekierować użytkowników do usługi zgodne w celu dalszego spełniają wymagania poza usługą Azure Active Directory. Dzięki temu można używać niektórych zewnętrznych uwierzytelnianie wieloskładnikowe i dostawców weryfikacji do wymuszania zasad dostępu warunkowego lub tworzenie niestandardowych usługi. W celu spełnienia tego formantu, przeglądarka użytkownika jest przekierowywany do zewnętrznej usługi, wykonuje wszelkie wymagane uwierzytelnianie lub sprawdzania poprawności działania i jest następnie przekierowany z powrotem do usługi Azure Active Directory. Jeśli użytkownik został pomyślnie uwierzytelniony lub sprawdzić poprawności, użytkownik nadal w przepływ dostępu warunkowego. 

## <a name="custom-controls"></a>Formanty niestandardowe

Kontrolki niestandardowe są możliwości wersji usługi Azure Active Directory Premium P2. Podczas korzystania z niestandardowych formantów, użytkownicy są przekierowywani do zgodne usługi by spełnić dodatkowe wymagania poza usługą Azure Active Directory. W celu spełnienia tego formantu, przeglądarka użytkownika jest przekierowywany do zewnętrznej usługi, wykonuje wszelkie wymagane uwierzytelnianie lub sprawdzania poprawności działania i jest następnie przekierowany z powrotem do usługi Azure Active Directory. Azure Active Directory sprawdza odpowiedź i, jeśli użytkownik został pomyślnie uwierzytelniony lub sprawdzić poprawności, użytkownik będzie nadal występował w przepływ dostępu warunkowego.

Tych kontrolek Zezwalaj na korzystanie z niektórych usług zewnętrznych lub niestandardowych jako kontroli dostępu warunkowego, a rozszerzenie możliwości dostępu warunkowego.

Obecnie oferty usługi zgodne dostawców obejmują:

- [Duo zabezpieczeń](https://duo.com/docs/azure-ca)

- RSA

- [Trusona](https://www.trusona.com/docs/azure-ad-integration-guide)

Aby uzyskać więcej informacji dotyczących tych usług skontaktuj się bezpośrednio z dostawców.

### <a name="creating-custom-controls"></a>Tworzenie niestandardowych formantów

Aby utworzyć niestandardowego formantu, najpierw należy skontaktować się dostawcę, który chcesz wykorzystać. Każdy dostawca firmy Microsoft ma swoją własną procesu i wymagania dotyczące Załóż, subskrypcji lub w przeciwnym razie staną się częścią usługi i wskazywać, że chcesz zintegrować z dostępu warunkowego. W tym momencie dostawcy zapewnia bloku danych w formacie JSON. Te dane dostawcy i dostępu warunkowego współdziałają ze sobą dzierżawy, tworzy nowego formantu i określa, jak dostęp warunkowy można stwierdzić, czy użytkownicy wykonali pomyślnie weryfikacji z dostawcy.

Skopiuj dane JSON, a następnie wkleić ją w polu tekstowym powiązane. Nie wprowadzaj żadnych zmian do formatu JSON bez jawnie zrozumienia podczas wprowadzania zmian. Wprowadzania zmian może przerwać połączenie między dostawcą a firmą Microsoft i potencjalnie Zablokuj użytkownicy spoza konta.

Można utworzyć niestandardowego formantu jest **Zarządzaj** sekcji **dostępu warunkowego** strony.

![Kontrola](./media/active-directory-conditional-access-controls/82.png)

Kliknięcie przycisku **nowej kontrolki niestandardowej**, powoduje otwarcie bloku z pola tekstowego danych JSON formantu.  


![Kontrola](./media/active-directory-conditional-access-controls/81.png)


### <a name="deleting-custom-controls"></a>Usuwanie kontrolek niestandardowych

Aby usunąć kontrolkę niestandardową, należy najpierw upewnić, że nie jest używany w żadnych zasad dostępu warunkowego. Po jego zakończeniu:

1. Przejdź do listy formanty niestandardowe

2. Kliknij przycisk...  

3. Wybierz pozycję **Usuń**.

### <a name="editing-custom-controls"></a>Niestandardowe formanty edycji

Aby edytować kontrolkę niestandardową, należy usunąć bieżącego formantu i Utwórz nowy formant zaktualizowanych informacji.




## <a name="session-controls"></a>Formanty sesji

Kontrolki sesji umożliwiają korzystanie z ograniczonego środowiska w ramach aplikacji w chmurze. Formanty sesji są wymuszane przez aplikacje w chmurze i polegać na dodatkowe informacje dostarczane przez usługę Azure AD do aplikacji o sesji.

![Kontrola](./media/active-directory-conditional-access-controls/31.png)

### <a name="use-app-enforced-restrictions"></a>Użyj ograniczeń wymuszonych przez aplikację

Aby wymagać usługi Azure AD do przekazywania informacji o urządzeniu do aplikacji w chmurze, można użyć tego formantu. Dzięki temu można sprawdzić, czy użytkownik jest pochodzi z zgodne urządzenie lub urządzenia przyłączone do domeny aplikacji w chmurze. Ten formant jest obecnie obsługiwana tylko z programem SharePoint jako aplikacji w chmurze. SharePoint używa informacji o urządzeniu umożliwia wyświetlanie użytkownikom bardziej ograniczony lub pełne środowisko w zależności od stanu urządzenia.
Aby dowiedzieć się więcej na temat sposobu wymagać ograniczony dostęp z programem SharePoint, zobacz [kontroli dostępu z urządzeń niezarządzanych](https://aka.ms/spolimitedaccessdocs).



## <a name="next-steps"></a>Kolejne kroki

- Jeśli chcesz wiedzieć, jak skonfigurować zasady dostępu warunkowego, zobacz [wymagają usługi MFA dla określonych aplikacji przy użyciu dostępu warunkowego usługi Azure Active Directory](active-directory-conditional-access-app-based-mfa.md).

- Jeśli wszystko jest gotowe do skonfigurowania zasad dostępu warunkowego dla danego środowiska, zobacz [najlepszych rozwiązań dotyczących dostępu warunkowego w usłudze Azure Active Directory](active-directory-conditional-access-best-practices.md). 
