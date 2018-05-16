---
title: Utwórz tożsamość aplikacji usługi Azure w portalu | Dokumentacja firmy Microsoft
description: Opisuje sposób tworzenia nowej aplikacji usługi Azure Active Directory i nazwy głównej usługi, który może służyć z kontroli dostępu opartej na rolach w usłudze Azure Resource Manager do zarządzania dostępem do zasobów.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/21/2018
ms.author: tomfitz
ms.openlocfilehash: e5d93963dddb4acb1147042ae338b32cb5d7646f
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="use-portal-to-create-an-azure-active-directory-application-and-service-principal-that-can-access-resources"></a>Aby utworzyć aplikację usługi Azure Active Directory i nazwy głównej usługi, który ma dostęp do zasobów za pomocą portalu

Jeśli masz kod, który wymaga dostępu lub modyfikacji zasobów, należy skonfigurować aplikację Azure Active Directory (AD). Wymagane uprawnienia są przypisywane do aplikacji usługi AD. Ta metoda jest preferowana, na którym uruchomiono aplikację z poświadczeniami użytkownika, ponieważ można przypisać uprawnienia do tożsamości aplikacji, które są inne niż własnych uprawnień. Zazwyczaj te uprawnienia są ograniczone do dokładnie co aplikacja powinna wykonać.

W tym artykule przedstawiono sposób wykonywania tych kroków za pośrednictwem portalu. Głównie aplikacji pojedynczej dzierżawy, gdzie aplikacja jest przeznaczona do uruchamiania tylko jednej organizacji. Zwykle użyć pojedynczej dzierżawy aplikacji dla aplikacji — biznesowych, które uruchamiane w danej organizacji.

> [!IMPORTANT]
> Zamiast tworzenia nazwy głównej usługi, należy wziąć pod uwagę przy użyciu usługi Azure AD zarządzane tożsamości usługi dla tożsamości użytkownika aplikacji. Azure AD MSI jest w publicznej wersji zapoznawczej usługi Azure Active Directory, które upraszcza tworzenie tożsamości dla kodu. Jeśli kod jest uruchomiony na to usługa, która obsługuje program Azure AD MSI i uzyskuje dostęp do zasobów, które obsługują uwierzytelnianie usługi Azure Active Directory, Azure AD MSI jest lepszym rozwiązaniem dla Ciebie. Aby dowiedzieć się więcej na temat usługi Azure AD MSI, łącznie z usług, które obecnie obsługuje, zobacz [zarządzane tożsamość usługi Azure zasobów](../active-directory/managed-service-identity/overview.md).

## <a name="required-permissions"></a>Wymagane uprawnienia

Aby ukończyć w tym artykule, należy wystarczających uprawnień do rejestrowania aplikacji w dzierżawie usługi Azure AD i przypisywanie aplikacji do roli w ramach subskrypcji platformy Azure. Upewnij się, że masz odpowiednie uprawnienia do wykonania tych kroków.

### <a name="check-azure-active-directory-permissions"></a>Sprawdź uprawnienia usługi Azure Active Directory

1. Wybierz pozycję **Azure Active Directory**.

   ![Wybieranie pozycji Azure Active Directory](./media/resource-group-create-service-principal-portal/select-active-directory.png)

1. W usłudze Azure Active Directory, zaznacz **ustawienia użytkownika**.

   ![Wybierz ustawienia użytkownika](./media/resource-group-create-service-principal-portal/select-user-settings.png)

1. Sprawdź **rejestracji aplikacji** ustawienie. Jeśli ustawiono **tak**, użytkownicy niebędący administratorami można zarejestrować aplikacji usługi AD. To ustawienie oznacza, że każdy użytkownik w dzierżawie usługi Azure AD można zarejestrować aplikacji. Możesz przejść do [uprawnienia subskrypcji Azure Sprawdź](#check-azure-subscription-permissions).

   ![Wyświetlanie rejestracji aplikacji](./media/resource-group-create-service-principal-portal/view-app-registrations.png)

1. Jeśli ustawiono rejestracji aplikacji, ustawianie **nr**, tylko [Administratorzy globalni](../active-directory/active-directory-assign-admin-roles-azure-portal.md) można zarejestrować aplikacji. Sprawdź, czy Twoje konto administratora dla dzierżawy usługi Azure AD. Wybierz **omówienie** i przyjrzyj się informacje o użytkowniku. Jeśli Twoje konto jest przypisany do roli użytkownika, ale ustawienia rejestracji aplikacji (z poprzedniego kroku) jest ograniczona do administratorów, skontaktuj się z administratorem, aby albo przypisanie do roli administratora globalnego lub aby użytkownicy mogli zarejestrować aplikacji.

   ![Znajdź użytkownika](./media/resource-group-create-service-principal-portal/view-user-info.png)

### <a name="check-azure-subscription-permissions"></a>Sprawdź uprawnienia subskrypcji platformy Azure

W Twojej subskrypcji platformy Azure, jego konto musi mieć `Microsoft.Authorization/*/Write` dostępu przypisany do roli aplikacji usługi AD. Ta akcja jest przyznawane za pośrednictwem [właściciela](../role-based-access-control/built-in-roles.md#owner) roli lub [Administrator dostępu użytkowników](../role-based-access-control/built-in-roles.md#user-access-administrator) roli. Jeśli Twoje konto jest przypisany do **współautora** roli, nie masz odpowiedniego uprawnienia. Błąd podczas próby przypisać nazwę główną usługi do roli.

Aby sprawdzić uprawnienia subskrypcji:

1. Wybierz konto w prawym górnym rogu i wybierz **Moje uprawnienia**.

   ![Wybierz uprawnienia użytkownika](./media/resource-group-create-service-principal-portal/select-my-permissions.png)

1. Z listy rozwijanej wybierz subskrypcję. Wybierz **kliknij tutaj, aby wyświetlić pełny dostęp do szczegółowych informacji dla tej subskrypcji**.

   ![Znajdź użytkownika](./media/resource-group-create-service-principal-portal/view-details.png)

1. Wyświetl przypisane role i określić, czy masz odpowiednie uprawnienia, aby przypisać aplikację usługi AD do roli. Jeśli nie, poproś administratora subskrypcji możesz dodać do roli Administrator dostępu użytkowników. Na poniższej ilustracji użytkownik jest przypisany do właściciela roli, co oznacza, że użytkownik ma odpowiednie uprawnienia.

   ![Pokaż uprawnienia](./media/resource-group-create-service-principal-portal/view-user-role.png)

## <a name="create-an-azure-active-directory-application"></a>Tworzenie aplikacji usługi Azure Active Directory

1. Zaloguj się do konta platformy Azure za pośrednictwem [portalu Azure](https://portal.azure.com).
1. Wybierz pozycję **Azure Active Directory**.

   ![Wybieranie pozycji Azure Active Directory](./media/resource-group-create-service-principal-portal/select-active-directory.png)

1. Wybierz pozycję **Rejestracje aplikacji**.

   ![Wybieranie rejestracji aplikacji](./media/resource-group-create-service-principal-portal/select-app-registrations.png)

1. Wybierz pozycję **Rejestrowanie nowej aplikacji**.

   ![Dodawanie aplikacji](./media/resource-group-create-service-principal-portal/select-add-app.png)

1. Podaj nazwę i adres URL aplikacji. Wybierz **aplikację internetową/interfejs API** dla typu aplikacji, którą chcesz utworzyć. Nie można utworzyć poświadczenia dla [aplikacji natywnej](../active-directory/manage-apps/application-proxy-configure-native-client-application.md); w związku z tym, że typ nie działa w przypadku zautomatyzowanych aplikacji. Po ustawieniu wartości, wybierz **Utwórz**.

   ![Nadawanie nazwy aplikacji](./media/resource-group-create-service-principal-portal/create-app.png)

Utworzono aplikację.

## <a name="get-application-id-and-authentication-key"></a>Pobierz klucz aplikacji identyfikator i uwierzytelniania

Gdy logujesz się w sposób programowy, potrzebujesz identyfikatora aplikacji i klucza uwierzytelniania. Aby uzyskać te wartości, wykonaj następujące kroki:

1. W oknie **Rejestracje aplikacji** w usłudze Azure Active Directory wybierz aplikację.

   ![Wybieranie aplikacji](./media/resource-group-create-service-principal-portal/select-app.png)

1. Skopiuj **identyfikator aplikacji** i zapisz go w kodzie aplikacji. Niektóre [przykładowe aplikacje](#log-in-as-the-application) odwołują się do tej wartości jako identyfikatora klienta.

   ![Identyfikator klienta](./media/resource-group-create-service-principal-portal/copy-app-id.png)

1. Aby wygenerować klucz uwierzytelniania, wybierz pozycję **Ustawienia**.

   ![Wybieranie pozycji Ustawienia](./media/resource-group-create-service-principal-portal/select-settings.png)

1. Aby wygenerować klucz uwierzytelniania, wybierz pozycję **Klucze**.

   ![Wybieranie pozycji Klucze](./media/resource-group-create-service-principal-portal/select-keys.png)

1. Podaj opis i czas trwania klucza. Po zakończeniu wybierz pozycję **Zapisz**.

   ![Zapisywanie klucza](./media/resource-group-create-service-principal-portal/save-key.png)

   Po zapisaniu klucza zostanie wyświetlona jego wartość. Skopiuj tę wartość, ponieważ później nie będzie można pobrać klucza. Wartość klucza podaje się z identyfikatorem aplikacji w celu zalogowania się jako aplikacja. Zapisz wartość klucza w miejscu, z którego aplikacja będzie mogła ją pobrać.

   ![Zapisany klucz](./media/resource-group-create-service-principal-portal/copy-key.png)

## <a name="get-tenant-id"></a>Pobieranie identyfikatora dzierżawy

Gdy logujesz się w sposób programowy, musisz przekazać identyfikator dzierżawy z żądaniem uwierzytelniania.

1. Wybierz pozycję **Azure Active Directory**.

   ![Wybieranie pozycji Azure Active Directory](./media/resource-group-create-service-principal-portal/select-active-directory.png)

1. Aby uzyskać identyfikator dzierżawy, wybierz pozycję **Właściwości** dla swojej dzierżawy usługi Azure AD.

   ![Wybieranie pozycji Właściwości usługi Azure AD](./media/resource-group-create-service-principal-portal/select-ad-properties.png)

1. Skopiuj **identyfikator katalogu**. Ta wartość jest Twoim identyfikatorem dzierżawy.

   ![Identyfikator dzierżawy](./media/resource-group-create-service-principal-portal/copy-directory-id.png)

## <a name="assign-application-to-role"></a>Przypisywanie aplikacji do roli

Aby uzyskać dostęp do zasobów w ramach subskrypcji, należy przypisać aplikacji do roli. Zdecyduj, które roli reprezentuje odpowiednich uprawnień dla aplikacji. Aby dowiedzieć się więcej o dostępnych ról, zobacz [RBAC: Built in Roles](../role-based-access-control/built-in-roles.md).

Na poziomie subskrypcji, grupy zasobów lub zasobów można ustawić zakresu. Uprawnienia są dziedziczone na niższe poziomy zakresu. Na przykład dodawanie aplikacji do roli czytnik dla grupy zasobów oznacza, że mogą odczytywać, grupy zasobów i wszystkie zasoby, które zawiera.

1. Przejdź na poziom zakresu, który chcesz przypisać aplikację do. Na przykład, aby przypisać rolę w zakresie subskrypcji, wybierz **subskrypcje**. Zamiast tego możesz określić, grupy zasobów lub zasobu.

   ![Wybierz subskrypcję](./media/resource-group-create-service-principal-portal/select-subscription.png)

1. Umożliwia określonej subskrypcji (grupy zasobów lub zasobów), aby przypisać tę aplikację.

   ![Wybierz subskrypcję do przypisania](./media/resource-group-create-service-principal-portal/select-one-subscription.png)

1. Wybierz **(IAM) kontroli dostępu**.

   ![Wybierz dostępu](./media/resource-group-create-service-principal-portal/select-access-control.png)

1. Wybierz pozycję **Dodaj**.

   ![Wybierz opcję Dodaj](./media/resource-group-create-service-principal-portal/select-add.png)

1. Wybierz rolę, którą chcesz przypisać do aplikacji. Poniższy obraz przedstawia **czytnika** roli.

   ![Wybierz rolę](./media/resource-group-create-service-principal-portal/select-role.png)

1. Domyślnie aplikacje usługi Azure Active Directory nie są wyświetlane w dostępnych opcji. Aby znaleźć aplikacji, należy podać nazwę go w polu wyszukiwania. Wybierz go.

   ![Wyszukiwanie aplikacji](./media/resource-group-create-service-principal-portal/search-app.png)

1. Wybierz **zapisać** na zakończenie przypisanie roli. Zostanie wyświetlona aplikacja w listy użytkowników przypisanych do roli dla tego zakresu.

## <a name="next-steps"></a>Kolejne kroki
* Aby skonfigurować aplikację wielu dzierżawców, zobacz [przewodnik dewelopera do autoryzacji przy użyciu interfejsu API Menedżera zasobów Azure](resource-manager-api-authentication.md).
* Aby dowiedzieć się więcej na temat określania zasad zabezpieczeń, zobacz [kontroli dostępu opartej na roli Azure](../role-based-access-control/role-assignments-portal.md).  
* Aby uzyskać listę dostępnych akcji, które można udzielić lub odmówić dla użytkowników, zobacz [operacji dostawcy zasobów usługi Azure Resource Manager](../role-based-access-control/resource-provider-operations.md).
