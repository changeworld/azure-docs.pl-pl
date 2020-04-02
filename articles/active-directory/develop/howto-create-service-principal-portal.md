---
title: Tworzenie jednostki usługi & usługi usługi Azure AD w portalu
titleSuffix: Microsoft identity platform
description: Utwórz nową aplikację usługi Azure Active Directory & jednostkę usługi, aby zarządzać dostępem do zasobów za pomocą kontroli dostępu opartej na rolach w usłudze Azure Resource Manager.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: ryanwi
ms.reviewer: tomfitz
ms.custom: aaddev, seoapril2019, identityplatformtop40
ms.openlocfilehash: d1ee8e90d1d690315b2727a050e0383d7d28dc03
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546141"
---
# <a name="how-to-use-the-portal-to-create-an-azure-ad-application-and-service-principal-that-can-access-resources"></a>Jak: Użyj portalu, aby utworzyć aplikację i jednostkę usługi Azure AD, która może uzyskiwać dostęp do zasobów

W tym artykule pokazano, jak utworzyć nową aplikację i jednostkę usługi Azure Active Directory (Azure AD), która może być używana z kontrolą dostępu opartą na rolach. Jeśli masz kod, który musi uzyskać dostęp do zasobów lub zmodyfikować, można utworzyć tożsamość dla aplikacji. Ta tożsamość jest określana jako jednostka usługi. Następnie można przypisać wymagane uprawnienia do jednostki usługi. W tym artykule pokazano, jak używać portalu do tworzenia jednostki usługi. Koncentruje się na aplikacji z jedną dzierżawą, gdzie aplikacja jest przeznaczona do uruchamiania tylko w jednej organizacji. Zazwyczaj używasz aplikacji z jedną dzierżawą dla aplikacji biznesowych, które są uruchamiane w organizacji.

> [!IMPORTANT]
> Zamiast tworzyć jednostkę usługi, należy rozważyć użycie tożsamości zarządzanych dla zasobów platformy Azure dla tożsamości aplikacji. Jeśli kod jest uruchamiany w usłudze obsługującej tożsamości zarządzane i uzyskującej dostęp do zasobów obsługujących uwierzytelnianie usługi Azure AD, tożsamości zarządzane są dla Ciebie lepszą opcją. Aby dowiedzieć się więcej na temat tożsamości zarządzanych dla zasobów platformy Azure, w tym usług, które obecnie go obsługują, zobacz [Co to są tożsamości zarządzane dla zasobów platformy Azure?](../managed-identities-azure-resources/overview.md).

## <a name="create-an-azure-active-directory-application"></a>Tworzenie aplikacji usługi Azure Active Directory

Przejdźmy od razu do tworzenia tożsamości. Jeśli napotkasz problem, sprawdź [wymagane uprawnienia,](#required-permissions) aby upewnić się, że Twoje konto może utworzyć tożsamość.

1. Zaloguj się do konta platformy Azure za pośrednictwem [witryny Azure portal](https://portal.azure.com).
1. Wybierz pozycję **Azure Active Directory**.
1. Wybierz pozycję **Rejestracje aplikacji**.
1. Wybierz **pozycję Nowa rejestracja**.
1. Nazwij aplikację. Wybierz typ obsługiwanego konta, który określa, kto może korzystać z aplikacji. W obszarze **Przekierowanie identyfikatora URI**wybierz pozycję **Web** dla typu aplikacji, którą chcesz utworzyć. Wprowadź identyfikator URI, do którego jest wysyłany token dostępu. Nie można utworzyć poświadczeń dla [aplikacji](../manage-apps/application-proxy-configure-native-client-application.md)macierzystej . Nie można użyć tego typu dla aplikacji zautomatyzowanej. Po ustawieniu wartości wybierz pozycję **Zarejestruj**.

   ![Wpisz nazwę aplikacji](./media/howto-create-service-principal-portal/create-app.png)

Utworzono aplikację i jednostkę usługi Azure AD.

## <a name="assign-a-role-to-the-application"></a>Przypisywanie roli do aplikacji

Aby uzyskać dostęp do zasobów w ramach subskrypcji, należy przypisać rolę do aplikacji. Zdecyduj, która rola oferuje odpowiednie uprawnienia dla aplikacji. Aby dowiedzieć się więcej o dostępnych rolach, zobacz [RBAC: Wbudowane role](../../role-based-access-control/built-in-roles.md).

Można ustawić zakres na poziomie subskrypcji, grupy zasobów lub zasobu. Uprawnienia są dziedziczone do niższych poziomów zakresu. Na przykład dodanie aplikacji do roli czytelnika dla grupy zasobów oznacza, że może odczytać grupę zasobów i wszystkie zasoby, które zawiera.

1. W witrynie Azure portal wybierz poziom zakresu, do którego chcesz przypisać aplikację. Na przykład, aby przypisać rolę w zakresie subskrypcji, wyszukaj i wybierz **subskrypcje**lub wybierz **subskrypcje** na stronie **głównej.**

   ![Na przykład przypisz rolę w zakresie subskrypcji](./media/howto-create-service-principal-portal/select-subscription.png)

1. Wybierz określoną subskrypcję, do aby przypisać aplikację.

   ![Wybierz subskrypcję dla przypisania](./media/howto-create-service-principal-portal/select-one-subscription.png)

   Jeśli nie widzisz subskrypcji, której szukasz, wybierz **filtr subskrypcji globalnych**. Upewnij się, że żądana subskrypcja jest wybrana dla portalu.

1. Wybierz **pozycję Kontrola dostępu (IAM)**.
1. Wybierz pozycję **Dodaj przypisanie roli**.
1. Wybierz rolę, którą chcesz przypisać do aplikacji. Na przykład, aby umożliwić aplikacji wykonywanie akcji, takich jak **ponowne uruchomienie**, **uruchamianie** i **zatrzymywania** wystąpień, wybierz rolę **współautora.**  Dowiedz się więcej o [dostępnych rolach](../../role-based-access-control/built-in-roles.md) Domyślnie aplikacje usługi Azure AD nie są wyświetlane w dostępnych opcjach. Aby znaleźć aplikację, wyszukaj jej nazwę i wybierz ją.

   ![Wybierz rolę, która ma zostać przypisana do aplikacji](./media/howto-create-service-principal-portal/select-role.png)

1. Wybierz **pozycję Zapisz,** aby zakończyć przypisywanie roli. Zostanie wyświetlona aplikacja na liście użytkowników z rolą dla tego zakresu.

Jednostki usługi jest skonfigurowany. Możesz zacząć go używać do uruchamiania skryptów lub aplikacji. W następnej sekcji pokazano, jak uzyskać wartości, które są potrzebne podczas logowania programowo.

## <a name="get-values-for-signing-in"></a>Pobierz wartości do logowania

Podczas programowego logowania należy przekazać identyfikator dzierżawy z żądaniem uwierzytelnienia. Potrzebny jest również identyfikator aplikacji i klucz uwierzytelniania. Aby uzyskać te wartości, wykonaj następujące kroki:

1. Wybierz pozycję **Azure Active Directory**.
1. Z **rejestracji aplikacji** w usłudze Azure AD wybierz aplikację.
1. Skopiuj identyfikator katalogu (dzierżawy) i zapisz go w kodzie aplikacji.

    ![Skopiuj katalog (identyfikator dzierżawy) i zapisz go w kodzie aplikacji](./media/howto-create-service-principal-portal/copy-tenant-id.png)

1. Skopiuj **identyfikator aplikacji** i zapisz go w kodzie aplikacji.

   ![Kopiowanie identyfikatora aplikacji (klienta)](./media/howto-create-service-principal-portal/copy-app-id.png)

## <a name="certificates-and-secrets"></a>Certyfikaty i wpisy tajne
Aplikacje demonów można użyć dwóch form poświadczeń do uwierzytelniania za pomocą usługi Azure AD: certyfikaty i wpisy tajne aplikacji.  Zalecamy użycie certyfikatu, ale można również utworzyć nowy klucz tajny aplikacji.

### <a name="upload-a-certificate"></a>Przekazywanie certyfikatu

Można użyć istniejącego certyfikatu, jeśli go masz.  Opcjonalnie można utworzyć certyfikat z podpisem własnym tylko do *celów testowych.* Otwórz program PowerShell i uruchom [certyfikat New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) z następującymi parametrami, aby utworzyć certyfikat z podpisem własnym w magazynie certyfikatów użytkownika na komputerze: 

```powershell
$cert=New-SelfSignedCertificate -Subject "CN=DaemonConsoleCert" -CertStoreLocation "Cert:\CurrentUser\My"  -KeyExportPolicy Exportable -KeySpec Signature
```

Wyeksportuj ten certyfikat do pliku przy użyciu przystawki Program MMC [Zarządzaj certyfikatem użytkownika](/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in) dostępnej z Panelu sterowania systemu Windows.

1. Wybierz **polecenie Uruchom** z menu **Start,** a następnie wprowadź plik **certmgr.msc**.

   Pojawi się narzędzie Menedżer certyfikatów dla bieżącego użytkownika.

1. Aby wyświetlić certyfikaty, w obszarze **Certyfikaty — bieżący użytkownik** w lewym okienku rozwiń katalog **osobisty.**
1. Kliknij prawym przyciskiem myszy utworzony certyfikat, wybierz pozycję **Wszystkie zadania >Eksportuj**.
1. Postępuj zgodnie z kreatorem eksportu certyfikatów.  Nie należy eksportować klucza prywatnego i eksportować do pliku . CER.

Aby przesłać certyfikat:

1. Wybierz pozycję **Azure Active Directory**.
1. Z **rejestracji aplikacji** w usłudze Azure AD wybierz aplikację.
1. Wybierz **pozycję Certyfikaty & wpisy tajne**.
1. Wybierz **pozycję Przekaż certyfikat** i wybierz certyfikat (istniejący certyfikat lub wyeksportowany certyfikat z podpisem własnym).

    ![Wybierz pozycję Przekaż certyfikat i wybierz certyfikat, który chcesz dodać](./media/howto-create-service-principal-portal/upload-cert.png)

1. Wybierz pozycję **Dodaj**.

Po zarejestrowaniu certyfikatu w aplikacji w portalu rejestracji aplikacji należy włączyć kod aplikacji klienckiej, aby użyć certyfikatu.

### <a name="create-a-new-application-secret"></a>Tworzenie nowego klucza tajnego aplikacji

Jeśli nie chcesz używać certyfikatu, możesz utworzyć nowy klucz tajny aplikacji.

1. Wybierz **pozycję Certyfikaty & wpisy tajne**.
1. Wybierz **pozycję Wpisy tajne klienta -> Nowy klucz tajny klienta**.
1. Podaj opis klucza tajnego i czas trwania. Po zakończeniu wybierz pozycję **Dodaj**.

   Po zapisaniu klucza tajnego klienta wyświetlana jest wartość klucza tajnego klienta. Skopiuj tę wartość, ponieważ nie będzie można pobrać klucza później. Wartość klucza zostanie podaj wartość aplikacji, aby zalogować się jako aplikacja. Zapisz wartość klucza w miejscu, z którego aplikacja będzie mogła ją pobrać.

   ![Skopiuj wartość tajną, ponieważ nie można pobrać tej wartości później](./media/howto-create-service-principal-portal/copy-secret.png)

## <a name="configure-access-policies-on-resources"></a>Konfigurowanie zasad dostępu dla zasobów
Należy pamiętać, że może być konieczne skonfigurowanie uprawnień do dodawania zasobów, które aplikacja musi uzyskać dostęp. Na przykład należy również [zaktualizować zasady dostępu magazynu kluczy,](/azure/key-vault/key-vault-secure-your-key-vault#data-plane-and-access-policies) aby dać aplikacji dostęp do kluczy, wpisów tajnych lub certyfikatów.  

1. W [witrynie Azure portal](https://portal.azure.com)przejdź do magazynu kluczy i wybierz pozycję **Zasady programu Access**.  
1. Wybierz **pozycję Dodaj zasady dostępu**, a następnie wybierz uprawnienia klucza, klucza tajnego i certyfikatu, które chcesz udzielić aplikacji.  Wybierz jednostkę usługi utworzoną wcześniej.
1. Wybierz **pozycję Dodaj,** aby dodać zasady dostępu, a następnie **pozycję Zapisz,** aby zatwierdzić zmiany.
    ![Dodawanie zasad dostępu](./media/howto-create-service-principal-portal/add-access-policy.png)

## <a name="required-permissions"></a>Wymagane uprawnienia

Musisz mieć wystarczające uprawnienia, aby zarejestrować aplikację w dzierżawie usługi Azure AD i przypisać do aplikacji rolę w subskrypcji platformy Azure.

### <a name="check-azure-ad-permissions"></a>Sprawdzanie uprawnień usługi Azure AD

1. Wybierz pozycję **Azure Active Directory**.
1. Zanotuj swoją rolę. Jeśli masz rolę **Użytkownik,** należy upewnić się, że nie-administratorzy mogą rejestrować aplikacje.

   ![Znajdź swoją rolę. Jeśli jesteś Użytkownikiem, upewnij się, że osoby niebędące administratorami mogą rejestrować aplikacje](./media/howto-create-service-principal-portal/view-user-info.png)

1. W lewym okienku wybierz **pozycję Ustawienia użytkownika**.
1. Sprawdź ustawienie **Rejestracje aplikacji.** Tę wartość może ustawić tylko administrator. Jeśli ustawiona jest **na Tak,** każdy użytkownik w dzierżawie usługi Azure AD może zarejestrować aplikację.

Jeśli ustawienie rejestracji aplikacji jest ustawione na **Nie,** tylko użytkownicy z rolą administratora mogą rejestrować tego typu aplikacje. Zobacz [dostępne role](../users-groups-roles/directory-assign-admin-roles.md#available-roles) i uprawnienia do [ról,](../users-groups-roles/directory-assign-admin-roles.md#role-permissions) aby dowiedzieć się więcej o dostępnych rolach administratora i określonych uprawnieniach w usłudze Azure AD, które są nadane każdej roli. Jeśli twojemu kontu jest przypisana rola Użytkownik, ale ustawienie rejestracji aplikacji jest ograniczone do użytkowników administratorów, poproś administratora o przypisanie jednej z ról administratora, które mogą tworzyć i zarządzać wszystkimi aspektami rejestracji aplikacji, lub aby umożliwić użytkownikom rejestrowanie aplikacji.

### <a name="check-azure-subscription-permissions"></a>Sprawdzanie uprawnień subskrypcji platformy Azure

W ramach subskrypcji platformy Azure `Microsoft.Authorization/*/Write` twoje konto musi mieć dostęp do przypisywania roli do aplikacji USŁUGI AD. Ta akcja jest wykonywana za pośrednictwem roli [Właściciel](../../role-based-access-control/built-in-roles.md#owner) lub [Administrator dostępu użytkowników](../../role-based-access-control/built-in-roles.md#user-access-administrator). Jeśli twojemu kontu przypisano rolę **Współautor,** nie masz odpowiednich uprawnień. Podczas próby przypisania jednostki usługi roli zostanie wyświetlony błąd.

Aby sprawdzić uprawnienia subskrypcji:

1. Wyszukaj i wybierz **subskrypcje**lub wybierz **subskrypcje** na stronie **głównej.**

   ![Wyszukaj](./media/howto-create-service-principal-portal/select-subscription.png)

1. Wybierz subskrypcję, w której chcesz utworzyć jednostkę usługi.

   ![Wybierz subskrypcję dla przypisania](./media/howto-create-service-principal-portal/select-one-subscription.png)

   Jeśli nie widzisz subskrypcji, której szukasz, wybierz **filtr subskrypcji globalnych**. Upewnij się, że żądana subskrypcja jest wybrana dla portalu.

1. Wybierz **pozycję Moje uprawnienia**. Następnie wybierz pozycję **Kliknij tutaj, aby wyświetlić pełne szczegóły dostępu dla tej subskrypcji**.

   ![Wybierz subskrypcję, w której chcesz utworzyć jednostkę usługi w](./media/howto-create-service-principal-portal/view-details.png)

1. Wybierz **pozycję Wyświetl** w **przypisaniach ról,** aby wyświetlić przypisane role, i określ, czy masz odpowiednie uprawnienia do przypisywania roli do aplikacji AD. Jeśli nie, poproś administratora subskrypcji o dodanie Cię do roli Administratora dostępu użytkownika. Na poniższej ilustracji użytkownik otrzymuje przypisaną rolę Właściciel, co oznacza, że użytkownik ma odpowiednie uprawnienia.

   ![W tym przykładzie pokazano, że użytkownikowi jest przypisana rola Właściciel](./media/howto-create-service-principal-portal/view-user-role.png)

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o określaniu zasad zabezpieczeń, zobacz [Kontrola dostępu oparta na rolach platformy Azure](../../role-based-access-control/role-assignments-portal.md).  
* Aby uzyskać listę dostępnych akcji, które mogą być przyznane lub odrzucone użytkownikom, zobacz [Operacje dostawcy zasobów usługi Azure Resource Manager](../../role-based-access-control/resource-provider-operations.md).
