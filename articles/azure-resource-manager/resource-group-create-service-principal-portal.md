---
title: Tworzenie tożsamości dla aplikacji platformy Azure w portalu | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak utworzyć nową aplikację usługi Azure Active Directory i jednostki usługi, który może służyć przy użyciu kontroli dostępu opartej na rolach w usłudze Azure Resource Manager do zarządzania dostępem do zasobów.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/21/2018
ms.author: tomfitz
ms.openlocfilehash: 57d017e2320e5cfea15f1716bc3b6518606e2ea4
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/27/2018
ms.locfileid: "39282251"
---
# <a name="use-portal-to-create-an-azure-active-directory-application-and-service-principal-that-can-access-resources"></a>Tworzenie aplikacji usługi Azure Active Directory i jednostki usługi, które mogą uzyskiwać dostęp do zasobów za pomocą portalu

Gdy masz kod, który wymaga dostępu lub modyfikacji zasobów, należy skonfigurować aplikację usługi Azure Active Directory (AD). Następnie można przypisać uprawnienia wymagane do aplikacji usługi AD. To podejście jest preferowane uruchamianie aplikacji z poświadczeniami użytkownika, ponieważ możesz przypisywać uprawnienia do tożsamości aplikacji, które są inne niż własnych uprawnień. Zazwyczaj te uprawnienia są ograniczone tylko do czynności, które aplikacja musi wykonywać.

W tym artykule pokazano, jak wykonać te kroki w portalu. Uwzględniono w szczególności aplikacji pojedynczej dzierżawy, w którym aplikacja jest przeznaczona do uruchamiania tylko jednej organizacji. Używa się zazwyczaj jedną dzierżawą aplikacji dla aplikacji line-of-business, które są uruchamiane w danej organizacji.

> [!IMPORTANT]
> Zamiast tworzyć jednostkę usługi, rozważ użycie tożsamości usługi zarządzanej w usłudze Azure AD na potrzeby tożsamości aplikacji. Azure AD MSI to dostępna w publicznej wersji zapoznawczej funkcja usługi Azure Active Directory, która upraszcza tworzenie tożsamości kodu. Jeśli kod jest uruchamiany w usłudze obsługującej funkcję Azure AD MSI i uzyskującej dostęp do zasobów, które obsługują uwierzytelnianie w usłudze Azure Active Directory, funkcja Azure AD MSI jest lepszym rozwiązaniem. Aby dowiedzieć się więcej na temat funkcji Azure AD MSI i zapoznać się z listą usług, które ją obecnie obsługują, zobacz [Tożsamość usługi zarządzanej dla zasobów platformy Azure](../active-directory/managed-service-identity/overview.md).

## <a name="required-permissions"></a>Wymagane uprawnienia

Aby ukończyć w tym artykule, należy mieć wystarczające uprawnienia do rejestrowania aplikacji w dzierżawie usługi Azure AD i przypisania aplikacji do roli w subskrypcji platformy Azure. Upewnijmy się, że masz odpowiednie uprawnienia do wykonywania tych kroków.

### <a name="check-azure-active-directory-permissions"></a>Sprawdzanie uprawnień usługi Azure Active Directory

1. Wybierz pozycję **Azure Active Directory**.

   ![Wybieranie pozycji Azure Active Directory](./media/resource-group-create-service-principal-portal/select-active-directory.png)

1. W usłudze Azure Active Directory wybierz pozycję **Ustawienia użytkownika**.

   ![Wybierz pozycję Ustawienia użytkownika](./media/resource-group-create-service-principal-portal/select-user-settings.png)

1. Sprawdź **rejestracje aplikacji** ustawienie. Jeśli ustawiono **tak**, użytkownicy niebędący administratorami mogą rejestrować aplikacje usługi AD. To ustawienie oznacza, że każdy użytkownik w dzierżawie usługi Azure AD może zarejestrować aplikację. Możesz przejść do [uprawnienia do subskrypcji Azure Sprawdź](#check-azure-subscription-permissions).

   ![Wyświetl rejestracje aplikacji](./media/resource-group-create-service-principal-portal/view-app-registrations.png)

1. Jeśli wartością ustawienia rejestracji aplikacji jest równa **nie**, tylko [Administratorzy globalni](../active-directory/users-groups-roles/directory-assign-admin-roles.md) mogą rejestrować aplikacje. Sprawdź, czy Twoje konto jest administratorem dzierżawy usługi Azure AD. Wybierz **Przegląd** i przyjrzyj się informacje o użytkowniku. Jeśli Twoje konto jest przypisany do roli użytkownika, ale ustawienia rejestracji aplikacji (z poprzedniego kroku) jest ograniczona do użytkowników administratora, poproś administratora o jedną przypisanie do roli administratora globalnego lub umożliwić użytkownikom rejestrowanie aplikacji.

   ![Znajdź użytkownika](./media/resource-group-create-service-principal-portal/view-user-info.png)

### <a name="check-azure-subscription-permissions"></a>Sprawdź uprawnienia subskrypcji platformy Azure

W ramach subskrypcji platformy Azure, Twoje konto musi mieć `Microsoft.Authorization/*/Write` dostępu, przypisywanie aplikacji usługi AD do roli. Ta akcja jest wykonywana za pośrednictwem roli [Właściciel](../role-based-access-control/built-in-roles.md#owner) lub [Administrator dostępu użytkowników](../role-based-access-control/built-in-roles.md#user-access-administrator). Jeśli Twoje konto jest przypisany do **Współautor** roli, nie masz odpowiedniego uprawnienia. Otrzymasz komunikat o błędzie podczas próby przypisania nazwy głównej usługi do roli.

Aby sprawdzić swoje uprawnienia subskrypcji:

1. Wybierz swoje konto w prawym górnym rogu, a następnie wybierz pozycję **Moje uprawnienia**.

   ![Wybierz uprawnienia użytkownika](./media/resource-group-create-service-principal-portal/select-my-permissions.png)

1. Z listy rozwijanej wybierz subskrypcję. Wybierz **kliknij tutaj, aby wyświetlić pełny dostęp do szczegółowych informacji dla tej subskrypcji**.

   ![Znajdź użytkownika](./media/resource-group-create-service-principal-portal/view-details.png)

1. Wyświetl przypisane role i określić, czy istnieją odpowiednie uprawnienia do przypisywania aplikacji usługi AD do roli. Jeśli nie, poproś administratora subskrypcji o dodanie Cię do roli Administrator dostępu użytkowników. Na poniższej ilustracji użytkownik jest przypisany do roli właściciel, co oznacza, że dany użytkownik ma odpowiednie uprawnienia.

   ![Pokaż uprawnienia](./media/resource-group-create-service-principal-portal/view-user-role.png)

## <a name="create-an-azure-active-directory-application"></a>Tworzenie aplikacji usługi Azure Active Directory

1. Zaloguj się do konta platformy Azure za pośrednictwem [witryny Azure portal](https://portal.azure.com).
1. Wybierz pozycję **Azure Active Directory**.

   ![Wybieranie pozycji Azure Active Directory](./media/resource-group-create-service-principal-portal/select-active-directory.png)

1. Wybierz pozycję **Rejestracje aplikacji**.

   ![Wybieranie rejestracji aplikacji](./media/resource-group-create-service-principal-portal/select-app-registrations.png)

1. Wybierz pozycję **Rejestrowanie nowej aplikacji**.

   ![Dodawanie aplikacji](./media/resource-group-create-service-principal-portal/select-add-app.png)

1. Podaj nazwę i adres URL aplikacji. Wybierz **aplikację internetową/interfejs API** dla typu aplikacji, którą chcesz utworzyć. Nie można utworzyć poświadczenia na potrzeby [aplikacji natywnej](../active-directory/manage-apps/application-proxy-configure-native-client-application.md); w związku z tym, że typ nie działa w przypadku zautomatyzowanych aplikacji. Po ustawieniu wartości, wybierz **Utwórz**.

   ![Nadawanie nazwy aplikacji](./media/resource-group-create-service-principal-portal/create-app.png)

Utworzono aplikację.

## <a name="get-application-id-and-authentication-key"></a>Pobierz aplikację Identyfikatora i klucza uwierzytelniania

Gdy logujesz się w sposób programowy, potrzebujesz identyfikatora aplikacji i klucza uwierzytelniania. Aby uzyskać te wartości, wykonaj następujące kroki:

1. W oknie **Rejestracje aplikacji** w usłudze Azure Active Directory wybierz aplikację.

   ![Wybieranie aplikacji](./media/resource-group-create-service-principal-portal/select-app.png)

1. Skopiuj **identyfikator aplikacji** i zapisz go w kodzie aplikacji. W niektórych [przykładowych aplikacjach](#log-in-as-the-application) ta wartość występuje jako identyfikator klienta.

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

Aby uzyskać dostęp do zasobów w ramach subskrypcji, należy przypisać aplikacji do roli. Zdecyduj, rolę, która reprezentuje odpowiednie uprawnienia dla aplikacji. Aby dowiedzieć się więcej na temat dostępnych ról, zobacz [RBAC: Built in Roles](../role-based-access-control/built-in-roles.md).

Zakres można ustawić na poziomie subskrypcji, grupy zasobów lub zasobu. Uprawnienia są dziedziczone na niższych poziomach zakresu. Na przykład dodanie aplikacji do roli Czytelnik dla grupy zasobów oznacza, że może odczytywać, grupy zasobów i wszystkie zasoby, które zawiera.

1. Przejdź do poziomu zakresu, którą chcesz przypisać aplikację. Na przykład, aby przypisać rolę w zakresie subskrypcji, wybierz **subskrypcje**. Zamiast tego można wybrać grupy zasobów lub zasobu.

   ![Wybierz subskrypcję](./media/resource-group-create-service-principal-portal/select-subscription.png)

1. Umożliwia określonej subskrypcji (grupy zasobów lub zasobu), aby przypisać tę aplikację.

   ![Wybierz subskrypcję do przypisania](./media/resource-group-create-service-principal-portal/select-one-subscription.png)

1. Wybierz **kontrola dostępu (IAM)**.

   ![Wybierz dostępu](./media/resource-group-create-service-principal-portal/select-access-control.png)

1. Wybierz pozycję **Dodaj**.

   ![Wybieranie opcji dodawania](./media/resource-group-create-service-principal-portal/select-add.png)

1. Wybierz rolę, którą chcesz przypisać do aplikacji. Aby umożliwić aplikacji wykonywanie akcji, takich jak **ponowny rozruch**, **start** i **zatrzymać** przypadkach konieczne jest posiadanie wybrać rolę **Współautor**. Na poniższej ilustracji przedstawiono **czytnika** roli.

   ![Wybierz rolę](./media/resource-group-create-service-principal-portal/select-role.png)

1. Domyślnie aplikacje usługi Azure Active Directory nie są wyświetlane w dostępnych opcjach. Aby znaleźć aplikację, należy podać jego nazwę w polu wyszukiwania. Wybierz go.

   ![Wyszukaj aplikację](./media/resource-group-create-service-principal-portal/search-app.png)

1. Wybierz **Zapisz** zakończenie przypisanie roli. Zostanie wyświetlona aplikacja na liście Użytkownicy przypisani do roli dla tego zakresu.

## <a name="next-steps"></a>Kolejne kroki
* Aby skonfigurować aplikację wielodostępną, zobacz [Developer's guide uwierzytelniania za pomocą interfejsu API usługi Azure Resource Manager](resource-manager-api-authentication.md).
* Aby dowiedzieć się więcej na temat określania zasad zabezpieczeń, zobacz [kontroli dostępu opartej na roli Azure](../role-based-access-control/role-assignments-portal.md).  
* Aby uzyskać listę dostępnych akcji, które można udzielić lub odmówić użytkowników, zobacz [operacji dostawcy zasobów usługi Azure Resource Manager](../role-based-access-control/resource-provider-operations.md).
