---
author: zhangmanling
ms.service: azure-cdn
ms.topic: include
ms.date: 11/21/2018
ms.author: mazha
ms.openlocfilehash: 41f2d4540f665137d34d262546cdc1a2edfbae3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77608753"
---
## <a name="prerequisites"></a>Wymagania wstępne
Przed napisaniem kodu zarządzania siecią CDN należy wykonać pewne przygotowanie, aby włączyć kod do interakcji z usługą Azure Resource Manager. Aby wykonać ten preparat, musisz:

* Tworzenie grupy zasobów zawierającej profil sieci CDN utworzonej w tym samouczku
* Konfigurowanie usługi Azure Active Directory w celu zapewnienia uwierzytelniania aplikacji
* Stosowanie uprawnień do grupy zasobów, tak aby tylko autoryzowani użytkownicy z dzierżawy usługi Azure AD mogli wchodzić w interakcje z profilem usługi CDN

### <a name="creating-the-resource-group"></a>Tworzenie grupy zasobów
1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com).
2. Kliknij pozycję **Utwórz zasób**.
3. Wyszukaj **grupę Zasobów** i w okienku Grupa zasobów kliknij pozycję **Utwórz**.

    ![Tworzenie nowej grupy zasobów](./media/cdn-app-dev-prep/cdn-new-rg-1-include.png)
3. Nazwij grupę zasobów *CdnConsoleTutorial*.  Wybierz subskrypcję i wybierz lokalizację w pobliżu.  Jeśli chcesz, możesz kliknąć pole wyboru **Przypnij do pulpitu nawigacyjnego,** aby przypiąć grupę zasobów do pulpitu nawigacyjnego w portalu.  Przypinanie ułatwia późniejsze znalezienie.  Po dokonaniu wyboru kliknij przycisk **Utwórz**.

    ![Nazywanie grupy zasobów](./media/cdn-app-dev-prep/cdn-new-rg-2-include.png)
4. Po utworzeniu grupy zasobów, jeśli nie przypięła jej do pulpitu nawigacyjnego, można ją znaleźć, klikając pozycję **Przeglądaj,** a następnie **grupy zasobów**.  Aby ją otworzyć, kliknij grupę zasobów.  Zanotuj swój **identyfikator subskrypcji**. Potrzebujemy go później.

    ![Nazywanie grupy zasobów](./media/cdn-app-dev-prep/cdn-subscription-id-include.png)

### <a name="creating-the-azure-ad-application-and-applying-permissions"></a>Tworzenie aplikacji usługi Azure AD i stosowanie uprawnień
Istnieją dwa podejścia do uwierzytelniania aplikacji za pomocą usługi Azure Active Directory: użytkownicy indywidualni lub podmiot usługi. Podmiot usługi jest podobny do konta usługi w systemie Windows.  Zamiast udzielania określonym użytkownikom uprawnień do interakcji z profilami usługi CDN, uprawnienia są przyznawane podmiotowi usługi.  Jednostki usługi są zwykle używane do zautomatyzowanych, nieinterakcyjnych procesów.  Mimo że ten samouczek jest pisanie interaktywnej aplikacji konsoli, skupimy się na podejście jednostki usługi.

Tworzenie jednostki usługi składa się z kilku kroków, w tym tworzenia aplikacji usługi Azure Active Directory.  Aby go utworzyć, będziemy [postępować zgodnie z tym samouczku](../articles/active-directory/develop/howto-create-service-principal-portal.md).

> [!IMPORTANT]
> Pamiętaj, aby wykonać wszystkie kroki opisane w [połączonym samouczku](../articles/active-directory/develop/howto-create-service-principal-portal.md).  *Ważne* jest, aby ukończyć go dokładnie tak, jak opisano.  Pamiętaj, aby zanotować **identyfikator dzierżawy,** **nazwę domeny dzierżawy** (zwykle domenę *onmicrosoft.com,* chyba że określono domenę niestandardową), **identyfikator klienta**i **klucz uwierzytelniania klienta,** ponieważ potrzebujemy tych informacji później.  Należy zachować ostrożność, aby chronić **identyfikator klienta** i **klucz uwierzytelniania klienta,** ponieważ te poświadczenia mogą być używane przez każdego do wykonywania operacji jako jednostki usługi.
>
> Po dojście do kroku o nazwie Konfigurowanie aplikacji wielodostępowej wybierz pozycję **Nie**.
>
> Po dojście do kroku [Przypisz aplikację do roli](../articles/active-directory/develop/howto-create-service-principal-portal.md#assign-a-role-to-the-application), użyj grupy zasobów utworzonej wcześniej, *CdnConsoleTutorial*, ale zamiast roli **Czytelnika,** przypisz rolę **współautora profilu sieci CDN.**  Po przypisaniu aplikacji roli **współautora profilu usługi CDN** w grupie zasobów wróć do tego samouczka. 
>
>

Po utworzeniu jednostki usługi i przypisaniu roli **współautora profilu usługi CDN** bloku **Użytkownicy** dla grupy zasobów powinien wyglądać podobnie do poniższej ilustracji.

![Ostrze użytkowników](./media/cdn-app-dev-prep/cdn-service-principal-include.png)

### <a name="interactive-user-authentication"></a>Interaktywne uwierzytelnianie użytkowników
Jeśli zamiast jednostki usługi, wolisz mieć interaktywne uwierzytelnianie poszczególnych użytkowników, proces jest podobny do tego dla jednostki usługi.  W rzeczywistości musisz postępować zgodnie z tą samą procedurą, ale wprowadzić kilka drobnych zmian.

> [!IMPORTANT]
> Wykonaj następujące kroki tylko wtedy, gdy zdecydujesz się użyć uwierzytelniania poszczególnych użytkowników zamiast jednostki usługi.
>
>

1. Podczas tworzenia aplikacji zamiast **aplikacji sieci Web**wybierz opcję Aplikacja **natywna**.

    ![Aplikacja natywna](./media/cdn-app-dev-prep/cdn-native-application-include.png)
2. Na następnej stronie zostanie wyświetlony monit o **podanie identyfikatora URI przekierowania**.  Identyfikator URI nie zostanie zweryfikowany, ale pamiętaj, co zostało wprowadzone. Potrzebujesz go później.
3. Nie ma potrzeby tworzenia **klucza uwierzytelniania klienta**.
4. Zamiast przypisywać jednostkę usługi do roli **współautora profilu usługi CDN,** zamierzamy przypisać poszczególnych użytkowników lub grupy.  W tym przykładzie widać, że przypisano *użytkownika demo sieci CDN* do roli **współautora profilu sieci CDN.**  

    ![Indywidualny dostęp do użytkownika](./media/cdn-app-dev-prep/cdn-aad-user-include.png)
