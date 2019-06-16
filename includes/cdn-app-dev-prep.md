---
author: zhangmanling
ms.service: cdn
ms.topic: include
ms.date: 11/21/2018
ms.author: mazha
ms.openlocfilehash: 7f80c8f1773cfeb8ddfb222d068a5c6571c2e5c7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66126309"
---
## <a name="prerequisites"></a>Wymagania wstępne
Przed napisaniem kodu zarządzania sieci CDN, należy wykonać kilka operacji przygotowania, aby włączyć kod do interakcji z usługą Azure Resource Manager. Aby zrobić to przygotowanie, należy:

* Utwórz grupę zasobów zawierającą profil CDN utworzonych w tym samouczku
* Konfigurowanie usługi Azure Active Directory do uwierzytelniania aplikacji
* Stosowanie uprawnień do grupy zasobów, tak aby tylko autoryzowani użytkownicy z dzierżawy usługi Azure AD mogą wchodzić w interakcje z profilem usługi CDN

### <a name="creating-the-resource-group"></a>Tworzenie grupy zasobów
1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. Kliknij pozycję **Utwórz zasób**.
3. Wyszukaj **grupy zasobów** i w okienku grupy zasobów kliknij **Utwórz**.

    ![Tworzenie nowej grupy zasobów](./media/cdn-app-dev-prep/cdn-new-rg-1-include.png)
3. Określ nazwę grupy zasobów *CdnConsoleTutorial*.  Wybierz swoją subskrypcję i wybierz lokalizację w pobliżu Ciebie.  Jeśli chcesz, możesz kliknąć **Przypnij do pulpitu nawigacyjnego** pole wyboru, aby przypiąć grupy zasobów do pulpitu nawigacyjnego w portalu.  Przypinanie ułatwia znajdowanie w przyszłości.  Po utworzeniu wybrane opcje, kliknij przycisk **Utwórz**.

    ![Nazwy grupy zasobów](./media/cdn-app-dev-prep/cdn-new-rg-2-include.png)
4. Po utworzeniu grupy zasobów, jeśli nie przypniesz go do pulpitu nawigacyjnego, możesz go znaleźć, klikając **Przeglądaj**, następnie **grup zasobów**.  Aby go otworzyć, kliknij grupę zasobów.  Zwróć uwagę na Twoje **identyfikator subskrypcji**. Nam potrzebny później.

    ![Nazwy grupy zasobów](./media/cdn-app-dev-prep/cdn-subscription-id-include.png)

### <a name="creating-the-azure-ad-application-and-applying-permissions"></a>Tworzenie aplikacji usługi Azure AD i stosowanie uprawnień
Istnieją dwa podejścia do uwierzytelniania aplikacji za pomocą usługi Azure Active Directory: Poszczególnych użytkowników lub jednostki usługi. Jednostki usługi jest podobny do konta usługi w Windows.  A nie konkretnego użytkownika uprawnień do interakcji z profilów CDN, są zamiast tego uprawnienia do jednostki usługi.  Nazwy główne usług są zwykle używane do automatycznego, nieinterakcyjnych procesów.  Mimo że w tym samouczku zapisuje aplikację konsoli interaktywnej, skupimy się na temat podejścia do nazwy głównej usługi.

Tworzenie nazwy głównej usługi składa się z kilku kroków w tym tworzenie aplikacji usługi Azure Active Directory.  Aby go utworzyć, którą zamierzamy [wykonaj kroki tego samouczka](../articles/active-directory/develop/howto-create-service-principal-portal.md).

> [!IMPORTANT]
> Pamiętaj wykonać wszystkie kroki w [połączone samouczek](../articles/active-directory/develop/howto-create-service-principal-portal.md).  Jest *ważne* zakończyć ją dokładnie tak, zgodnie z opisem.  Upewnij się, że należy zwrócić uwagę swoje **identyfikator dzierżawy**, **nazwa_domeny_dzierżawy** (często *. onmicrosoft.com* domeny przed określeniem domenę niestandardową), **identyfikator klienta** , i **klucza uwierzytelniania klienta**, ponieważ Potrzebujemy tych informacji w dalszej części.  Uważaj je przed nieprzewidzianymi swoje **identyfikator klienta** i **klucza uwierzytelniania klienta**, jak te poświadczenia można przez dowolną osobę można wykonać operacji jako nazwy głównej usługi.
>
> Po wyświetleniu okna krok o nazwie skonfiguruj aplikację wielodostępną, wybierz **nie**.
>
> Gdy uzyskujesz do kroku [przypisywanie aplikacji do roli](../articles/active-directory/develop/howto-create-service-principal-portal.md#assign-the-application-to-a-role), użyj grupy zasobów utworzonej wcześniej, *CdnConsoleTutorial*, ale zamiast **czytnika** roli, przypisz **Współautor profilu CDN** roli.  Po przypisaniu aplikacja **Współautor profilu CDN** roli w danej grupie zasobów, wróć do tego samouczka. 
>
>

Po utworzeniu jednostki usługi i przypisany **Współautor profilu CDN** roli **użytkowników** bloku grupy zasobów powinien wyglądać podobnie do poniższej ilustracji.

![Blok użytkownicy](./media/cdn-app-dev-prep/cdn-service-principal-include.png)

### <a name="interactive-user-authentication"></a>Interakcyjnego uwierzytelniania użytkownika
Jeśli zamiast nazwy głównej usługi, należy zamiast interakcyjnego poszczególnych uwierzytelniania użytkownika, proces jest podobnie jak w przypadku nazwy głównej usługi.  W rzeczywistości należy do tej samej procedury, ale wprowadzić kilka zmian, pomocnicza.

> [!IMPORTANT]
> Wykonaj podjęcie następujących kroków tylko, jeśli użytkownik decyduje się do korzystania z uwierzytelniania użytkownika zamiast nazwy głównej usługi.
>
>

1. Podczas tworzenia aplikacji, zamiast **aplikacji sieci Web**, wybierz **aplikacji natywnej**.

    ![Aplikacja natywna](./media/cdn-app-dev-prep/cdn-native-application-include.png)
2. Na następnej stronie zostanie wyświetlony monit o **identyfikator URI przekierowania**.  Identyfikator URI nie będzie można sprawdzić poprawności, ale należy pamiętać, zostały wprowadzone. Potrzebny później.
3. Nie ma konieczności tworzenia **klucza uwierzytelniania klienta**.
4. Zamiast przypisywania nazwę główną usługi do **Współautor profilu CDN** roli, którą zamierzamy przypisać poszczególnych użytkowników lub grup.  W tym przykładzie widać, że przypisano *użytkownik demonstracyjny CDN* do **Współautor profilu CDN** roli.  

    ![Dostęp do poszczególnych użytkowników](./media/cdn-app-dev-prep/cdn-aad-user-include.png)
