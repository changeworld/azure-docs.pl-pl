---
title: Tworzenie tożsamości dla aplikacji platformy Azure w portalu | Microsoft Docs
description: Zawiera opis sposobu tworzenia nowej aplikacji Azure Active Directory i nazwy głównej usługi, która może być używana z kontrolą dostępu opartą na rolach w usłudze Azure Resource Manager do zarządzania dostępem do zasobów.
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
ms.custom: aaddev, seoapril2019, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: a28354f54978e8ba776d8b0da294652ff462a05f
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2019
ms.locfileid: "68853458"
---
# <a name="how-to-use-the-portal-to-create-an-azure-ad-application-and-service-principal-that-can-access-resources"></a>Instrukcje: używanie portalu do tworzenia aplikacji usługi Azure AD i jednostki usługi w celu uzyskiwania dostępu do zasobów

W tym artykule opisano sposób tworzenia nowej aplikacji Azure Active Directory (Azure AD) i nazwy głównej usługi, która może być używana z kontrolą dostępu opartą na rolach. Jeśli masz kod wymagający dostępu lub modyfikacji zasobów, możesz utworzyć tożsamość dla aplikacji. Ta tożsamość jest określana jako jednostka usługi. Następnie można przypisać wymagane uprawnienia do nazwy głównej usługi. W tym artykule pokazano, jak utworzyć jednostkę usługi przy użyciu portalu. Koncentruje się na aplikacji z jedną dzierżawą, w której aplikacja jest przeznaczona do działania tylko w jednej organizacji. Zwykle używasz aplikacji z jedną dzierżawą dla aplikacji biznesowych, które działają w organizacji.

> [!IMPORTANT]
> Zamiast tworzyć jednostkę usługi, rozważ użycie zarządzanych tożsamości dla zasobów platformy Azure dla Twojej tożsamości aplikacji. Jeśli kod jest uruchamiany w usłudze obsługującej zarządzane tożsamości i uzyskuje dostęp do zasobów, które obsługują uwierzytelnianie usługi Azure AD, tożsamości zarządzane są dla Ciebie lepszym rozwiązaniem. Aby dowiedzieć się więcej na temat tożsamości zarządzanych dla zasobów platformy Azure, w tym usług, które są obecnie obsługiwane, zobacz temat [co to są tożsamości zarządzane dla zasobów platformy Azure?](../managed-identities-azure-resources/overview.md).

## <a name="create-an-azure-active-directory-application"></a>Tworzenie aplikacji Azure Active Directory

Przejdźmy bezpośrednio do tworzenia tożsamości. Jeśli napotkasz problem, sprawdź [wymagane uprawnienia](#required-permissions) , aby upewnić się, że konto może utworzyć tożsamość.

1. Zaloguj się do konta platformy Azure za pomocą [Azure Portal](https://portal.azure.com).
1. Wybierz pozycję **Azure Active Directory**.
1. Wybierz pozycję **Rejestracje aplikacji**.
1. Wybierz pozycję **Nowa rejestracja**.
1. Nadaj nazwę aplikacji. Wybierz obsługiwany typ konta, który określa, kto może korzystać z aplikacji. W obszarze **Identyfikator URI przekierowania**wybierz pozycję **Sieć Web** dla typu aplikacji, którą chcesz utworzyć. Wprowadź identyfikator URI, do którego jest wysyłany token dostępu. Nie można utworzyć poświadczeń dla [aplikacji natywnej](../manage-apps/application-proxy-configure-native-client-application.md). Nie można użyć tego typu dla zautomatyzowanej aplikacji. Po ustawieniu wartości wybierz pozycję **zarejestruj**.

   ![Wpisz nazwę aplikacji](./media/howto-create-service-principal-portal/create-app.png)

Utworzono aplikację usługi Azure AD i nazwę główną usługi.

## <a name="assign-the-application-to-a-role"></a>Przypisywanie aplikacji do roli

Aby uzyskać dostęp do zasobów w ramach subskrypcji, musisz przypisać aplikację do roli. Zdecyduj, która rola oferuje odpowiednie uprawnienia dla aplikacji. Aby dowiedzieć się więcej o dostępnych rolach [, zobacz RBAC: wbudowane role](../../role-based-access-control/built-in-roles.md).

Zakres można ustawić na poziomie subskrypcji, grupy zasobów lub zasobu. Uprawnienia są dziedziczone na niższych poziomach zakresu. Na przykład dodanie aplikacji do roli czytelnik dla grupy zasobów oznacza, że może ona odczytać grupę zasobów i wszystkie zawarte w niej zasoby.

1. Przejdź do poziomu zakresu, do którego chcesz przypisać aplikację. Aby na przykład przypisać rolę w zakresie subskrypcji, wybierz pozycję **wszystkie usługi** i **subskrypcje**.

   ![Na przykład Przypisz rolę w zakresie subskrypcji](./media/howto-create-service-principal-portal/select-subscription.png)

1. Wybierz określoną subskrypcję, do której ma zostać przypisana aplikacja.

   ![Wybierz subskrypcję do przypisania](./media/howto-create-service-principal-portal/select-one-subscription.png)

   Jeśli nie widzisz subskrypcji, której szukasz, wybierz pozycję **Filtr subskrypcje globalne**. Upewnij się, że wybrano subskrypcję dla portalu.

1. Wybierz **kontrola dostępu (IAM)** .
1. Wybierz **Dodaj przypisanie roli**.
1. Wybierz rolę, którą chcesz przypisać do aplikacji. Aby umożliwić aplikacji wykonywanie akcji takich jak **ponowny rozruch**, **Uruchamianie** i zatrzymywanie wystąpień, wybierz rolę **współautor** . Domyślnie aplikacje usługi Azure AD nie są wyświetlane w dostępnych opcjach. Aby znaleźć aplikację, wyszukaj ją i wybierz ją.

   ![Wybierz rolę, która ma zostać przypisana do aplikacji](./media/howto-create-service-principal-portal/select-role.png)

1. Wybierz **Zapisz** zakończenie przypisanie roli. Aplikacja zostanie wyświetlona na liście użytkowników przypisanych do roli dla tego zakresu.

Nazwa główna usługi została skonfigurowana. Możesz rozpocząć korzystanie z niego do uruchamiania skryptów lub aplikacji. W następnej sekcji pokazano, jak uzyskać wartości, które są konieczne podczas logowania programowo.

## <a name="get-values-for-signing-in"></a>Pobierz wartości logowania

Podczas programowego logowania należy przekazać identyfikator dzierżawy do żądania uwierzytelnienia. Wymagany jest również identyfikator aplikacji i klucz uwierzytelniania. Aby uzyskać te wartości, wykonaj następujące kroki:

1. Wybierz pozycję **Azure Active Directory**.
1. W obszarze **rejestracje aplikacji** w usłudze Azure AD wybierz aplikację.
1. Skopiuj identyfikator katalogu (dzierżawy) i Zapisz go w kodzie aplikacji.

    ![Skopiuj katalog (identyfikator dzierżawy) i Zapisz go w kodzie aplikacji](./media/howto-create-service-principal-portal/copy-tenant-id.png)

1. Skopiuj **identyfikator aplikacji** i zapisz go w kodzie aplikacji.

   ![Kopiowanie identyfikatora aplikacji (klienta)](./media/howto-create-service-principal-portal/copy-app-id.png)

## <a name="certificates-and-secrets"></a>Certyfikaty i wpisy tajne
Aplikacje demona mogą używać dwóch form poświadczeń do uwierzytelniania w usłudze Azure AD: certyfikaty i wpisy tajne aplikacji.  Zalecamy używanie certyfikatu, ale można również utworzyć nowy klucz tajny aplikacji.

### <a name="upload-a-certificate"></a>Przekaż certyfikat

Jeśli masz istniejący certyfikat, możesz go użyć.  Opcjonalnie można utworzyć certyfikat z podpisem własnym na potrzeby testowania. Otwórz program PowerShell i uruchom polecenie [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) z poniższymi parametrami, aby utworzyć certyfikat z podpisem własnym w magazynie certyfikatów użytkownika na `$cert=New-SelfSignedCertificate -Subject "CN=DaemonConsoleCert" -CertStoreLocation "Cert:\CurrentUser\My"  -KeyExportPolicy Exportable -KeySpec Signature`komputerze:.  Wyeksportuj ten certyfikat za pomocą przystawki [Zarządzanie certyfikatem użytkownika](/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in) programu MMC dostępnej w panelu sterowania systemu Windows.

Aby przekazać certyfikat:

1. Wybierz pozycję **certyfikaty &** wpisy tajne.
1. Wybierz pozycję **Przekaż certyfikat** i wybierz certyfikat (istniejący certyfikat lub wyeksportowany certyfikat z podpisem własnym).

    ![Wybierz pozycję Przekaż certyfikat i wybierz ten, który chcesz dodać.](./media/howto-create-service-principal-portal/upload-cert.png)

1. Wybierz pozycję **Dodaj**.

Po zarejestrowaniu certyfikatu w aplikacji w portalu rejestracji aplikacji należy włączyć użycie tego certyfikatu w kodzie aplikacji klienta.

### <a name="create-a-new-application-secret"></a>Utwórz nowy klucz tajny aplikacji

Jeśli zdecydujesz się nie używać certyfikatu, możesz utworzyć nowy klucz tajny aplikacji.

1. Wybierz pozycję **certyfikaty &** wpisy tajne.
1. Wybierz pozycję wpisy **tajne klienta — > nowego klucza tajnego klienta**.
1. Podaj opis klucza tajnego i czas trwania. Po zakończeniu wybierz pozycję **Dodaj**.

   Po zapisaniu klucza tajnego klienta zostanie wyświetlona wartość wpisu tajnego klienta. Skopiuj tę wartość, ponieważ nie można pobrać klucza później. Podaj wartość klucza z IDENTYFIKATORem aplikacji, aby zalogować się jako aplikacja. Zapisz wartość klucza w miejscu, z którego aplikacja będzie mogła ją pobrać.

   ![Skopiuj wartość klucza tajnego, ponieważ nie można pobrać jej później](./media/howto-create-service-principal-portal/copy-secret.png)

## <a name="required-permissions"></a>Wymagane uprawnienia

Musisz mieć wystarczające uprawnienia do zarejestrowania aplikacji w dzierżawie usługi Azure AD i przypisania aplikacji do roli w ramach subskrypcji platformy Azure.

### <a name="check-azure-ad-permissions"></a>Sprawdź uprawnienia usługi Azure AD

1. Wybierz pozycję **Azure Active Directory**.
1. Zanotuj rolę użytkownika. Jeśli masz rolę **użytkownika** , musisz upewnić się, że użytkownicy niebędący administratorami mogą rejestrować aplikacje.

   ![Znajdź rolę. Jeśli jesteś użytkownikiem, upewnij się, że użytkownicy niebędący administratorami mogą rejestrować aplikacje](./media/howto-create-service-principal-portal/view-user-info.png)

1. Wybierz pozycję **Ustawienia użytkownika**.
1. Sprawdź ustawienie **rejestracje aplikacji** . Tę wartość można ustawić tylko przez administratora. W przypadku wybrania **opcji tak**każdy użytkownik w dzierżawie usługi Azure AD może zarejestrować aplikację.

Jeśli ustawienie rejestracje aplikacji ma wartość **nie**, tylko użytkownicy z rolą administratora mogą rejestrować te typy aplikacji. Zobacz [dostępne role](../users-groups-roles/directory-assign-admin-roles.md#available-roles) i [uprawnienia roli](../users-groups-roles/directory-assign-admin-roles.md#role-permissions) , aby dowiedzieć się więcej na temat dostępnych ról administratorów i określonych uprawnień w usłudze Azure AD, które są nadawane każdej roli. Jeśli Twoje konto jest przypisane do roli użytkownika, ale ustawienie rejestracji aplikacji jest ograniczone do użytkowników administracyjnych, poproszenie administratora o przypisanie do jednej z ról administratora, które mogą tworzyć wszystkie aspekty rejestracji aplikacji i zarządzać nimi, lub aby umożliwić użytkownikom Zarejestruj aplikacje.

### <a name="check-azure-subscription-permissions"></a>Sprawdź uprawnienia subskrypcji platformy Azure

W ramach subskrypcji platformy Azure Twoje konto musi mieć `Microsoft.Authorization/*/Write` dostęp do przypisywania aplikacji usługi AD do roli. Ta akcja jest wykonywana za pośrednictwem roli [Właściciel](../../role-based-access-control/built-in-roles.md#owner) lub [Administrator dostępu użytkowników](../../role-based-access-control/built-in-roles.md#user-access-administrator). Jeśli Twoje konto jest przypisane do roli **współautor** , nie masz wystarczających uprawnień. Wystąpił błąd podczas próby przypisania nazwy głównej usługi do roli.

Aby sprawdzić uprawnienia do subskrypcji:

1. Wybierz swoje konto w prawym górnym rogu, a następnie wybierz pozycję **...-> moje uprawnienia**.

   ![Wybierz swoje konto i uprawnienia użytkownika](./media/howto-create-service-principal-portal/select-my-permissions.png)

1. Z listy rozwijanej wybierz subskrypcję, w której chcesz utworzyć nazwę główną usługi. Następnie wybierz **pozycję kliknij tutaj, aby wyświetlić pełne szczegóły dostępu dla tej subskrypcji**.

   ![Wybierz subskrypcję, w której chcesz utworzyć nazwę główną usługi](./media/howto-create-service-principal-portal/view-details.png)

1. Wybierz **przypisania ról** , aby wyświetlić przypisane role i określić, czy masz odpowiednie uprawnienia do przypisywania aplikacji usługi AD do roli. Jeśli nie, skontaktuj się z administratorem subskrypcji, aby dodać Cię do roli administratora dostępu użytkownika. Na poniższej ilustracji użytkownik jest przypisany do roli właściciela, co oznacza, że użytkownik ma odpowiednie uprawnienia.

   ![Ten przykład pokazuje, że użytkownik jest przypisany do roli właściciela](./media/howto-create-service-principal-portal/view-user-role.png)

## <a name="next-steps"></a>Kolejne kroki

* Aby skonfigurować aplikację wielodostępną, zapoznaj się [z przewodnikiem dewelopera na potrzeby autoryzacji za pomocą interfejsu API Azure Resource Manager](../../azure-resource-manager/resource-manager-api-authentication.md).
* Aby dowiedzieć się więcej na temat określania zasad zabezpieczeń, zobacz [Access Control oparte na rolach platformy Azure](../../role-based-access-control/role-assignments-portal.md).  
* Aby uzyskać listę dostępnych akcji, które można udzielić lub odmówić użytkownikom, zobacz [Azure Resource Manager operacje dostawcy zasobów](../../role-based-access-control/resource-provider-operations.md).
