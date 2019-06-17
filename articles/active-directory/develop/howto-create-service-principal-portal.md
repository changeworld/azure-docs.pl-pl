---
title: Tworzenie tożsamości dla aplikacji platformy Azure w portalu | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak utworzyć nową aplikację usługi Azure Active Directory i jednostki usługi, który może służyć przy użyciu kontroli dostępu opartej na rolach w usłudze Azure Resource Manager do zarządzania dostępem do zasobów.
services: active-directory
documentationcenter: na
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/17/2019
ms.author: ryanwi
ms.reviewer: tomfitz
ms.custom: seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 33ef65f09a4e7513738a6cc6b277d06cd4cb4da8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67052408"
---
# <a name="how-to-use-the-portal-to-create-an-azure-ad-application-and-service-principal-that-can-access-resources"></a>Instrukcje: Aby utworzyć aplikację usługi Azure AD i jednostkę usługi, które mogą uzyskiwać dostęp do zasobów, użyj portalu

W tym artykule pokazano, jak utworzyć nową aplikację usługi Azure Active Directory (Azure AD) i jednostki usługi, które mogą być używane z kontroli dostępu opartej na rolach. Jeśli masz kod, który wymaga dostępu lub modyfikacji zasobów, można utworzyć tożsamości dla aplikacji. Ta tożsamość jest określana jako jednostka usługi. Następnie można przypisać uprawnienia wymagane do jednostki usługi. W tym artykule pokazano, jak utworzyć jednostkę usługi za pomocą portalu. Uwzględniono w szczególności aplikacji pojedynczej dzierżawy, w którym aplikacja jest przeznaczona do uruchamiania tylko jednej organizacji. Używa się zazwyczaj jedną dzierżawą aplikacji dla aplikacji line-of-business, które są uruchamiane w danej organizacji.

> [!IMPORTANT]
> Zamiast tworzenia nazwy głównej usługi, należy wziąć pod uwagę przy użyciu zarządzanych tożsamości dla zasobów platformy Azure dla Twojej tożsamości aplikacji. Jeśli kod jest wykonywany na to usługa, która obsługuje zarządzanych tożsamości i uzyskuje dostęp do zasobów, które obsługują uwierzytelnianie usługi Azure AD, zarządzanych tożsamości jest lepszym rozwiązaniem dla Ciebie. Aby dowiedzieć się więcej na temat zarządzanych tożsamości dla zasobów platformy Azure, w tym usług, które obecnie go obsługują, zobacz [co to jest zarządzanych tożsamości dla zasobów platformy Azure?](../managed-identities-azure-resources/overview.md).

## <a name="create-an-azure-active-directory-application"></a>Tworzenie aplikacji usługi Azure Active Directory

Do dzieła bezpośrednio do tworzenia tożsamości. Jeśli napotkasz problem, sprawdź [wymagane uprawnienia](#required-permissions) się upewnić, że Twoje konto można utworzyć tożsamości.

1. Zaloguj się do konta platformy Azure za pośrednictwem [witryny Azure portal](https://portal.azure.com).
1. Wybierz pozycję **Azure Active Directory**.
1. Wybierz pozycję **Rejestracje aplikacji**.

   ![Wybieranie rejestracji aplikacji](./media/howto-create-service-principal-portal/select-app-registrations.png)

1. Wybierz **nowej rejestracji**.

   ![Dodawanie aplikacji](./media/howto-create-service-principal-portal/select-add-app.png)

1. Podaj nazwę dla aplikacji. Wybierz obsługiwane konto typ, który decyduje, kto za pomocą tej aplikacji. W obszarze **identyfikator URI przekierowania**, wybierz opcję **Web** dla typu aplikacji, którą chcesz utworzyć. Wprowadź identyfikator URI, gdzie token dostępu jest wysyłany do.  Nie można utworzyć poświadczenia na potrzeby [aplikacji natywnej](../manage-apps/application-proxy-configure-native-client-application.md). Nie można użyć tego typu aplikacji automatycznych. Po ustawieniu wartości, wybierz **zarejestrować**.

   ![Nadawanie nazwy aplikacji](./media/howto-create-service-principal-portal/create-app.png)

Utworzono aplikację usługi Azure AD i jednostkę usługi.

## <a name="assign-the-application-to-a-role"></a>Przypisz aplikację do roli

Aby uzyskać dostęp do zasobów w ramach subskrypcji, należy przypisać aplikacji do roli. Zdecyduj, rolę, która oferuje odpowiednie uprawnienia dla aplikacji. Aby dowiedzieć się więcej na temat dostępnych ról, zobacz [RBAC: wbudowane role](../../role-based-access-control/built-in-roles.md).

Zakres można ustawić na poziomie subskrypcji, grupy zasobów lub zasobu. Uprawnienia są dziedziczone na niższych poziomach zakresu. Na przykład dodanie aplikacji do roli Czytelnik dla grupy zasobów oznacza, że może odczytywać, grupy zasobów i wszystkie zasoby, które zawiera.

1. Przejdź do poziomu zakresu, którą chcesz przypisać aplikację. Na przykład, aby przypisać rolę w zakresie subskrypcji, wybierz **wszystkich usług** i **subskrypcje**.

   ![Wybieranie subskrypcji](./media/howto-create-service-principal-portal/select-subscription.png)

1. Wybierz określonej subskrypcji, aby przypisać aplikację.

   ![Wybierz subskrypcję do przypisania](./media/howto-create-service-principal-portal/select-one-subscription.png)

   Jeśli nie widzisz subskrypcji, czego szukasz, wybierz opcję **filtr globalny subskrypcji**. Upewnij się, subskrypcji, którą chcesz, aby wybrano dla portalu. 

1. Wybierz **kontrola dostępu (IAM)** .
1. Wybierz **Dodaj przypisanie roli**.

   ![Wybierz opcję Dodaj przypisanie roli](./media/howto-create-service-principal-portal/select-add.png)

1. Wybierz rolę, którą chcesz przypisać do aplikacji. Aby umożliwić aplikacji, które można wykonać akcji, takich jak **ponowny rozruch**, **start** i **zatrzymać** wystąpień, wybierz opcję **Współautor** roli. Domyślnie aplikacje usługi Azure AD nie są wyświetlane w dostępnych opcjach. Aby znaleźć aplikację, wyszukaj nazwę i zaznacz go.

   ![Wybór roli](./media/howto-create-service-principal-portal/select-role.png)

1. Wybierz **Zapisz** zakończenie przypisanie roli. Zostanie wyświetlona aplikacja na liście Użytkownicy przypisani do roli dla tego zakresu.

Konfigurowanie jednostki usługi. Aby rozpocząć, użycie go do uruchamiania aplikacji lub skryptów. Następna sekcja pokazuje, jak można pobrać wartości, które są wymagane podczas rejestrowania się programowo.

## <a name="get-values-for-signing-in"></a>Pobieranie wartości do logowania

Podczas programowego logowania, musisz przekazać identyfikator dzierżawy z żądaniem uwierzytelniania. Należy również identyfikator swojej aplikacji i klucza uwierzytelniania. Aby uzyskać te wartości, wykonaj następujące kroki:

1. Wybierz pozycję **Azure Active Directory**.

1. Z **rejestracje aplikacji** w usłudze Azure AD, wybierz swoją aplikację.

   ![Wybierz aplikację](./media/howto-create-service-principal-portal/select-app.png)

1. Skopiuj identyfikator katalogu (dzierżawcy) i zapisz go w kodzie aplikacji.

    ![Identyfikator dzierżawy](./media/howto-create-service-principal-portal/copy-tenant-id.png)

1. Skopiuj **identyfikator aplikacji** i zapisz go w kodzie aplikacji.

   ![Identyfikator klienta](./media/howto-create-service-principal-portal/copy-app-id.png)

## <a name="certificates-and-secrets"></a>Certyfikaty i klucze tajne
Demon umożliwia aplikacjom dwie formy poświadczeń uwierzytelniania w usłudze Azure AD: certyfikatów i kluczy tajnych aplikacji.  Firma Microsoft zaleca używanie certyfikatu, ale można również utworzyć nowy klucz tajny aplikacji.

### <a name="upload-a-certificate"></a>Przekaż certyfikat

Można użyć istniejącego certyfikatu, jeśli nie masz.  Opcjonalnie można utworzyć certyfikatu z podpisem własnym do celów testowych. Otwórz program PowerShell i uruchom [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) z następującymi parametrami, aby utworzyć certyfikat z podpisem własnym w magazynie certyfikatów użytkownika na komputerze: `$cert=New-SelfSignedCertificate -Subject "CN=DaemonConsoleCert" -CertStoreLocation "Cert:\CurrentUser\My"  -KeyExportPolicy Exportable -KeySpec Signature`.  Eksportowanie przy użyciu tego certyfikatu [Zarządzaj certyfikatem użytkownika](/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in) przystawki MMC dostępna z poziomu Panelu sterowania Windows.

Aby przekazać certyfikat:
1. Wybierz **certyfikaty i klucze tajne**.

   ![Wybierz ustawienia](./media/howto-create-service-principal-portal/select-certs-secrets.png)
1. Kliknij pozycję **Przekaż certyfikat** a następnie wybierz certyfikat (istniejącego certyfikatu lub samodzielnie podpisanych certyfikatów można wyeksportować).
    ![Przekazywanie certyfikatu](./media/howto-create-service-principal-portal/upload-cert.png)
1. Kliknij pozycję **Add** (Dodaj).

Po zarejestrowaniu certyfikatu swoją aplikację w portalu rejestracji aplikacji, musisz włączyć kodem aplikacji klienta do korzystania z certyfikatu.

### <a name="create-a-new-application-secret"></a>Utwórz nowy klucz tajny aplikacji
Jeśli nie chcesz używać certyfikatu, można utworzyć nowy klucz tajny aplikacji.
1. Wybierz **certyfikaty i klucze tajne**.

   ![Wybierz ustawienia](./media/howto-create-service-principal-portal/select-certs-secrets.png)

1. Wybierz **wpisów tajnych klienta -> Nowy wpis tajny klienta**.
1. Podaj opis wpisu tajnego i czas trwania. Po zakończeniu wybierz pozycję **Dodaj**.

   ![Zapisz klucz tajny](./media/howto-create-service-principal-portal/save-secret.png)

   Po zapisaniu klucza tajnego klienta, wyświetlana jest wartość klucza tajnego klienta. Skopiuj tę wartość, ponieważ nie można pobrać klucza później. Możesz podać wartość klucza z Identyfikatorem aplikacji do logowania w aplikacji. Zapisz wartość klucza w miejscu, z którego aplikacja będzie mogła ją pobrać.

   ![Skopiować klucza tajnego](./media/howto-create-service-principal-portal/copy-secret.png)

## <a name="required-permissions"></a>Wymagane uprawnienia

Musisz mieć wystarczające uprawnienia do rejestrowania aplikacji w dzierżawie usługi Azure AD i przypisania aplikacji do roli w subskrypcji platformy Azure.

### <a name="check-azure-ad-permissions"></a>Sprawdź uprawnienia usługi Azure AD

1. Wybierz pozycję **Azure Active Directory**.
1. Należy pamiętać, roli użytkownika. Jeśli masz **użytkownika** roli, musisz upewnić się, że użytkowników niebędących administratorami mogą rejestrować aplikacje.

   ![Znajdź użytkownika](./media/howto-create-service-principal-portal/view-user-info.png)

1. Wybierz **ustawienia użytkownika**.

   ![Wybierz pozycję Ustawienia użytkownika](./media/howto-create-service-principal-portal/select-user-settings.png)

1. Sprawdź **rejestracje aplikacji** ustawienie. Tę wartość można ustawić tylko przez administratora. Jeśli ustawiono **tak**, żaden użytkownik w dzierżawie usługi Azure AD można zarejestrować aplikację.

   ![Wyświetl rejestracje aplikacji](./media/howto-create-service-principal-portal/view-app-registrations.png)

Jeśli wartością ustawienia rejestracji aplikacji jest równa **nie**, tylko użytkownicy z rolą administratora mogą rejestrować aplikacje tego typu. Zobacz [dostępnych ról](../users-groups-roles/directory-assign-admin-roles.md#available-roles) i [uprawnienia roli](../users-groups-roles/directory-assign-admin-roles.md#role-permissions) Aby dowiedzieć się więcej o rolach administratora dostępne i określone uprawnienia w usłudze Azure AD, które są określone dla każdej roli. Jeśli Twoje konto jest przypisany do roli użytkownika, ale ustawienia rejestracji aplikacji jest ograniczona do użytkowników administratora, poproś administratora w celu przypisania do jednej z ról administratora, które można tworzyć i zarządzać wszystkie aspekty rejestracji aplikacji lub umożliwić użytkownikom Rejestrowanie aplikacji.

### <a name="check-azure-subscription-permissions"></a>Sprawdź uprawnienia subskrypcji platformy Azure

W ramach subskrypcji platformy Azure, Twoje konto musi mieć `Microsoft.Authorization/*/Write` dostępu, przypisywanie aplikacji usługi AD do roli. Ta akcja jest wykonywana za pośrednictwem roli [Właściciel](../../role-based-access-control/built-in-roles.md#owner) lub [Administrator dostępu użytkowników](../../role-based-access-control/built-in-roles.md#user-access-administrator). Jeśli Twoje konto jest przypisany do **Współautor** roli, nie masz odpowiedniego uprawnienia. Otrzymasz komunikat o błędzie podczas próby przypisania nazwy głównej usługi do roli.

Aby sprawdzić swoje uprawnienia subskrypcji:

1. Wybierz swoje konto w prawym górnym rogu, a następnie wybierz pozycję **... -> Moje uprawnienia**.

   ![Wybierz uprawnienia użytkownika](./media/howto-create-service-principal-portal/select-my-permissions.png)

1. Z listy rozwijanej wybierz subskrypcję, którą chcesz utworzyć usługę podmiotu zabezpieczeń w. Następnie wybierz **kliknij tutaj, aby wyświetlić pełny dostęp do szczegółowych informacji dla tej subskrypcji**.

   ![Znajdź użytkownika](./media/howto-create-service-principal-portal/view-details.png)

1. Wybierz **przypisań ról** można wyświetlić przypisane role oraz określić, czy istnieją odpowiednie uprawnienia do przypisywania aplikacji usługi AD do roli. Jeśli nie, poproś administratora subskrypcji o dodanie Cię do roli Administrator dostępu użytkowników. Na poniższej ilustracji użytkownik jest przypisany do roli właściciel, co oznacza, że dany użytkownik ma odpowiednie uprawnienia.

   ![Pokaż uprawnienia](./media/howto-create-service-principal-portal/view-user-role.png)

## <a name="next-steps"></a>Kolejne kroki

* Aby skonfigurować aplikację wielodostępną, zobacz [Developer's guide uwierzytelniania za pomocą interfejsu API usługi Azure Resource Manager](../../azure-resource-manager/resource-manager-api-authentication.md).
* Aby dowiedzieć się więcej na temat określania zasad zabezpieczeń, zobacz [kontroli dostępu opartej na roli Azure](../../role-based-access-control/role-assignments-portal.md).  
* Aby uzyskać listę dostępnych akcji, które można udzielić lub odmówić użytkowników, zobacz [operacji dostawcy zasobów usługi Azure Resource Manager](../../role-based-access-control/resource-provider-operations.md).
