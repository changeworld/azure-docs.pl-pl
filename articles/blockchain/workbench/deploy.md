---
title: Wdrażanie aplikacji Azure Blockchain Workbench
description: Sposób wdrażania aplikacji Azure Blockchain Workbench
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/06/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: brendal
manager: femila
ms.openlocfilehash: 4fffc54428b152a060594a5c107d3ac08457aaaa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65154645"
---
# <a name="deploy-azure-blockchain-workbench"></a>Wdrażanie aplikacji Azure Blockchain Workbench

Azure Blockchain Workbench jest wdrażany za pomocą szablonu rozwiązania w witrynie Azure Marketplace. Szablon upraszcza wdrożenie składniki wymagane do tworzenia aplikacji łańcucha bloków. Po wdrożeniu aplikacji Blockchain Workbench zapewnia dostęp do aplikacji klienckich do tworzenia i zarządzania użytkownikami i aplikacji łańcucha bloków.

Aby uzyskać więcej informacji na temat składników aplikacji Blockchain Workbench, zobacz [architektury aplikacji Azure Blockchain Workbench](architecture.md).

## <a name="prepare-for-deployment"></a>Przygotowanie do wdrożenia

Blockchain Workbench umożliwia wdrażanie księgi łańcucha bloków, wraz z zestawem odpowiednich usług platformy Azure, o których najczęściej używany do tworzenia aplikacji łańcucha bloków. Wdrażanie aplikacji Blockchain Workbench powoduje następujących usług platformy Azure aprowizowane w grupie zasobów w subskrypcji platformy Azure.

* Plan usługi App Service (standardowa)
* Application Insights
* Event Grid
* W usłudze Azure Key Vault
* Service Bus
* Bazy danych SQL Database (Standard S0) + serwer logiczny SQL
* Konto usługi Azure Storage (Standard-LRS)
* Zestaw skalowania maszyn wirtualnych z pojemność wynoszącą 1
* Grupy zasobów sieci wirtualnej (przy użyciu obciążenia równoważenia, sieciowej grupy zabezpieczeń, publiczny adres IP sieci wirtualnej)
* Opcjonalnie: Usługa Azure Blockchain (podstawowa B0 wartość domyślna)

Poniżej przedstawiono przykład wdrożenia utworzone w **myblockchain** grupy zasobów.

![Przykład wdrożenia](media/deploy/example-deployment.png)

Koszt Blockchain Workbench jest agregacją koszt podstawowych usług platformy Azure. Informacje o cenach dla usług platformy Azure mogą być obliczane przy użyciu [Kalkulator cen](https://azure.microsoft.com/pricing/calculator/).

## <a name="prerequisites"></a>Wymagania wstępne

Azure Blockchain Workbench wymaga rejestracji konfiguracji i aplikacji usługi Azure AD. Użytkownik może zrobić w usłudze Azure AD [konfiguracje ręcznie](#azure-ad-configuration) przed przystąpieniem do wdrożenia lub uruchom skrypt po wdrożeniu. W przypadku ponownego wdrażania aplikacji Blockchain Workbench, zobacz [konfiguracji usługi Azure AD](#azure-ad-configuration) Aby sprawdzić konfigurację usługi Azure AD.

> [!IMPORTANT]
> Środowisko robocze nie ma być wdrożony w tej samej dzierżawy, który jest używane do rejestrowania aplikacji usługi Azure AD. Workbench musi zostać wdrożony w dzierżawie, w którym masz wystarczające uprawnienia do wdrażania zasobów. Aby uzyskać więcej informacji na temat dzierżaw usługi Azure AD, zobacz [jak uzyskać dzierżawę usługi Active Directory](../../active-directory/develop/quickstart-create-new-tenant.md) i [Integrowanie aplikacji z usługą Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md).

## <a name="deploy-blockchain-workbench"></a>Wdrażanie aplikacji Blockchain Workbench

Po ukończeniu kroków wymagań wstępnych można przystąpić do wdrażania aplikacji Blockchain Workbench. W poniższych sekcjach opisano, jak wdrożyć platformę.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz swoje konto w prawym górnym rogu i przełącz się do żądanej dzierżawy usługi Azure AD, w której chcesz wdrożyć aplikacji Azure Blockchain Workbench.
3. W okienku po lewej stronie wybierz pozycję **Utwórz zasób**. Wyszukaj `Azure Blockchain Workbench` w **Przeszukaj witrynę Marketplace** pasku wyszukiwania. 

    ![Pasek wyszukiwania w witrynie Marketplace](media/deploy/marketplace-search-bar.png)

4. Wybierz **aplikacji Azure Blockchain Workbench**.

    ![Wyniki wyszukiwania w witrynie Marketplace](media/deploy/marketplace-search-results.png)

5. Wybierz pozycję **Utwórz**.
6. Wykonaj podstawowe ustawienia.

    ![Tworzenie aplikacji Azure Blockchain Workbench](media/deploy/blockchain-workbench-settings-basic.png)

    | Ustawienie | Opis  |
    |---------|--------------|
    | Prefiks zasobów | Krótkie Unikatowy identyfikator dla danego wdrożenia. Ta wartość jest używana jako podstawa do nadawania nazw zasobów. |
    | Nazwa użytkownika maszyny Wirtualnej | Nazwa użytkownika jest używana jako administrator dla wszystkich maszyn wirtualnych (VM). |
    | Typ uwierzytelniania | Wybierz, jeśli chcesz używać hasła lub klucza do łączenia się z maszynami wirtualnymi. |
    | Hasło | Hasło jest używane do łączenia się z maszynami wirtualnymi. |
    | Protokół SSH | Użyj klucz publiczny RSA w formacie jednowierszowym począwszy **ssh-rsa** lub wielowierszowym formacie PEM. Możesz wygenerować kluczy SSH przy użyciu `ssh-keygen` w systemie Linux i OS X lub przy użyciu narzędzia PuTTYGen Windows. Więcej informacji na temat kluczy SSH, zobacz [jak używać protokołu SSH kluczy przy użyciu Windows Azure](../../virtual-machines/linux/ssh-from-windows.md). |
    | Hasło bazy danych i łańcucha bloków | Określ hasło używane do uzyskiwania dostępu do bazy danych utworzone w ramach wdrożenia. Hasło musi spełniać trzy z czterech następujących wymagań: długość musi należeć do zakresu od 12 & 72 znaków, 1 mała litera, 1 Wielka litera, 1 cyfra i 1 znak specjalny inny sign(#) nie liczb, percent(%), przecinka (,), star(*), utworzyć kopię oferty (\`), podwójny quote("), pojedynczych cudzysłowów, myślnik (-) i semicolumn(;) |
    | Regionu geograficznego | Określ, gdzie wdrażanie zasobów aplikacji Blockchain Workbench. Aby uzyskać najlepsze dostępność, powinien on odpowiadać **lokalizacji** ustawienie. |
    | Subskrypcja | Określ subskrypcję platformy Azure, chcesz używać dla danego wdrożenia. |
    | Grupy zasobów | Utwórz nową grupę zasobów, wybierając **Utwórz nową** i określ unikatową nazwą grupy zasobów. |
    | Lokalizacja | Określ region, który chcesz wdrożyć platformę. |

7. Wybierz **OK** Zakończenie sekcji konfiguracji ustawienia podstawowe.

8. W **Zaawansowane ustawienia**, wybierz, jeśli chcesz utworzyć nową sieć łańcucha bloków lub użyć istniejącej sieci dowód uwierzytelniania łańcucha bloków.

    Aby uzyskać **Utwórz nową**:

    *Tworzenia nowych* opcja wdraża rejestr Azure Blockchain usługi kworum z podstawowej jednostki sku domyślne.

    ![Ustawienia zaawansowane dla nową sieć łańcucha bloków](media/deploy/advanced-blockchain-settings-new.png)

    | Ustawienie | Opis  |
    |---------|--------------|
    | Usługa Azure Blockchain warstwy cenowej | Wybierz **podstawowe** lub **standardowa** warstwy usług łańcucha bloków Azure, który jest używany dla aplikacji Blockchain Workbench |
    | Ustawienia usługi Azure Active Directory | Wybierz **później dodać**.</br>Uwaga: Jeśli została wybrana opcja [wstępne skonfigurowanie usługi Azure AD](#azure-ad-configuration) lub ponownego wdrażania, możliwość *Dodaj teraz*. |
    | Wybór maszyny Wirtualnej | Wybierz preferowany magazyn wydajności i rozmiaru maszyny Wirtualnej dla sieci łańcucha bloków. Wybierz mniejszego rozmiaru maszyny Wirtualnej, takie jak *standardowa DS1 wersja 2* jeśli znajdują się w subskrypcji przy użyciu limity niski usług, takich jak bezpłatną warstwę platformy Azure. |

    Aby uzyskać **Użyj istniejącej**:

    *Użyj istniejącej* opcji pozwala określić sieć łańcucha bloków Ethereum dowód uwierzytelniania (PoA). Punkty końcowe mają następujące wymagania.

   * Punkt końcowy musi być sieć łańcucha bloków Ethereum dowód uwierzytelniania (PoA).
   * Punkt końcowy musi być publicznie dostępny za pośrednictwem sieci.
   * Należy skonfigurować sieć łańcucha bloków PoA cenie gazu ustawiane na zero.

     > [!NOTE]
     > Nie są finansowane kont Blockchain Workbench. Jeśli wymaganych środków pieniężnych nie transakcji.

     ![Ustawienia zaawansowane dla istniejącej sieci łańcucha bloków](media/deploy/advanced-blockchain-settings-existing.png)

     | Ustawienie | Opis  |
     |---------|--------------|
     | Punkt końcowy Ethereum RPC | Podaj końcowych wywołań RPC w istniejącej sieci PoA łańcucha bloków. Punkt końcowy rozpoczyna się od https:// lub http:// i kończy się za pomocą numeru portu. Na przykład: `http<s>://<network-url>:<port>` |
     | Ustawienia usługi Azure Active Directory | Wybierz **później dodać**.</br>Uwaga: Jeśli została wybrana opcja [wstępne skonfigurowanie usługi Azure AD](#azure-ad-configuration) lub ponownego wdrażania, możliwość *Dodaj teraz*. |
     | Wybór maszyny Wirtualnej | Wybierz preferowany magazyn wydajności i rozmiaru maszyny Wirtualnej dla sieci łańcucha bloków. Wybierz mniejszego rozmiaru maszyny Wirtualnej, takie jak *standardowa DS1 wersja 2* jeśli znajdują się w subskrypcji przy użyciu limity niski usług, takich jak bezpłatną warstwę platformy Azure. |

9. Wybierz **OK** zakończenie ustawienia zaawansowane.

10. Przejrzyj podsumowanie, aby sprawdzić, czy parametry są poprawne.

    ![Podsumowanie](media/deploy/blockchain-workbench-summary.png)

11. Wybierz **Utwórz** zgodę na warunki i wdrażania aplikacji Azure Blockchain Workbench.

Wdrożenie może potrwać do 90 minut. Za pomocą witryny Azure portal do monitorowania postępu. W nowo utworzonej grupy zasobów, wybierz **wdrożeń > Przegląd** Aby wyświetlić stan wdrożonych artefaktów.

> [!IMPORTANT]
> Po wdrożeniu, należy wykonać ustawienia usługi Active Directory. Jeśli została wybrana opcja **dodać później**, musisz uruchomić [skryptu konfiguracji usługi Azure AD](#azure-ad-configuration-script).  Jeśli została wybrana opcja **Dodaj teraz**, musisz [skonfigurować adres URL odpowiedzi](#configuring-the-reply-url).

## <a name="blockchain-workbench-web-url"></a>Adres URL sieci Web z aplikacji Blockchain Workbench

Po zakończeniu wdrażania aplikacji Blockchain Workbench Nowa grupa zasobów zawiera zasoby aplikacji Blockchain Workbench. Usługi aplikacji Blockchain Workbench są dostępne za pośrednictwem adresu URL sieci web. Następujące kroki pokazują jak pobrać adres URL sieci web wdrożonej framework.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. W okienku nawigacji po lewej stronie wybierz **grup zasobów**
3. Wybierz nazwę grupy zasobów, podanej podczas wdrażania aplikacji Blockchain Workbench.
4. Wybierz **typu** nagłówek kolumny, aby sortować listę alfabetycznie według typu.
5. Istnieją dwa zasoby o typie **usługi App Service**. Wybierz zasób typu **usługi App Service** *bez* "— interfejs api" sufiks.

    ![Lista usług aplikacji](media/deploy/resource-group-list.png)

6. W usłudze App Service **Essentials** sekcji, skopiuj **adresu URL** wartość, która reprezentuje adres URL sieci web do Twojej wdrożonej aplikacji Blockchain Workbench.

    ![Podstawy usługi aplikacji](media/deploy/app-service.png)

Aby skojarzyć niestandardowej nazwy domeny z aplikacji Blockchain Workbench, zobacz [Konfigurowanie niestandardowej nazwy domeny dla aplikacji sieci web w usłudze Azure App Service przy użyciu usługi Traffic Manager](../../app-service/web-sites-traffic-manager-custom-domain-name.md).

## <a name="azure-ad-configuration-script"></a>Skrypt konfiguracji w usłudze Azure AD

Usługa Azure AD, musi być skonfigurowany do ukończenia wdrożenia aplikacji Blockchain Workbench. Użyjesz skrypt programu PowerShell w celu konfiguracji.

1. W przeglądarce przejdź do [Blockchain Workbench — adres URL sieci Web](#blockchain-workbench-web-url).
2. Zostaną wyświetlone instrukcje dotyczące konfigurowania usługi Azure AD przy użyciu usługi Cloud Shell. Skopiuj polecenia i uruchom usługę Cloud Shell.

    ![Uruchom skrypt usługi AAD](media/deploy/launch-aad-script.png)

3. Wybierz dzierżawę usługi Azure AD, w której została wdrożona Blockchain Workbench.
4. W usłudze Cloud Shell Wklej i uruchom polecenie.
5. Po wyświetleniu monitu wprowadź dzierżawy usługi Azure AD, do której chcesz używać dla aplikacji Blockchain Workbench. Są to dzierżawy zawierającą użytkowników dla aplikacji Blockchain Workbench.

    > [!IMPORTANT]
    > Uwierzytelniony użytkownik musi mieć uprawnienia do tworzenia rejestracje aplikacji usługi Azure AD i przyznawanie uprawnień delegowanych aplikacji w dzierżawie. Może być konieczne poproś administratora dzierżawy, aby uruchomić skrypt konfiguracji usługi Azure AD lub utworzyć nową dzierżawę.

    ![Wprowadź dzierżawę usługi Azure AD](media/deploy/choose-tenant.png)

6. Zostanie wyświetlony monit do uwierzytelniania w dzierżawie usługi Azure AD za pomocą przeglądarki. Otwórz adres URL sieci web w przeglądarce, a następnie wprowadź kod i uwierzytelniania.

    ![Uwierzytelnianie przy użyciu kodu](media/deploy/authenticate.png)

7. Skrypt generuje kilka komunikatów o stanie. Możesz uzyskać **Powodzenie** komunikat o stanie, jeśli dzierżawa została pomyślnie aprowizowana.
8. Przejdź do adresu URL aplikacji Blockchain Workbench. Prośba o zgodę, aby udzielić uprawnień do odczytu do katalogu. Dzięki temu Blockchain Workbench dostęp do aplikacji sieci web dla użytkowników w dzierżawie. Jeśli jesteś administratorem dzierżawy, można wyrazić zgodę dla całej organizacji. Ta opcja powoduje zaakceptowanie wyrażania zgody dla wszystkich użytkowników w dzierżawie. W przeciwnym razie każdy użytkownik jest monitowany o ich zgodę przy pierwszym użyciu aplikacji sieci web aplikacji Blockchain Workbench.
9. Wybierz **Akceptuj** do wyrażenia zgody.

     ![Zgoda na odczytywanie profilów użytkowników](media/deploy/graph-permission-consent.png)

10. Po zgody Blockchain Workbench aplikacji sieci web można używać.

## <a name="azure-ad-configuration"></a>Konfiguracja programu Azure AD

Jeśli użytkownik chce ręcznie skonfigurować lub zweryfikować ustawienia usługi Azure AD przed wdrożeniem, należy wykonać wszystkich kroków opisanych w tej sekcji. Jeśli chcesz automatycznie skonfigurować ustawienia usługi Azure AD, należy użyć [skryptu konfiguracji usługi Azure AD](#azure-ad-configuration-script) po wdrożeniu aplikacji Blockchain Workbench.

### <a name="blockchain-workbench-api-app-registration"></a>Rejestracja aplikacji interfejsu API usługi Blockchain Workbench

Wdrożenie aplikacji Blockchain Workbench wymaga rejestracji aplikacji usługi Azure AD. Należy dzierżawy usługi Azure Active Directory (Azure AD), aby zarejestrować aplikację. Można użyć istniejącej dzierżawy lub utworzyć nową dzierżawę. Jeśli używasz istniejącej dzierżawy usługi Azure AD, należy się wystarczające uprawnienia do rejestrowania aplikacji, udzielanie uprawnień interfejsu API programu Graph i zezwolić na dostęp gościa w ramach dzierżawy usługi Azure AD. Jeśli nie masz wystarczających uprawnień w istniejącej dzierżawie usługi Azure AD należy utworzyć nową dzierżawę.


1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz swoje konto w prawym górnym rogu, a następnie przejdź do żądanej usługi Azure AD dzierżawy. Dzierżawa powinna być administrator subskrypcji dzierżawcy subskrypcji, w którym jest wdrażana aplikacja Workbench i masz wystarczające uprawnienia do rejestrowania aplikacji.
3. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory**. Wybierz **rejestracje aplikacji** > **rejestrowanie nowej aplikacji**.

    ![Rejestracja aplikacji](media/deploy/app-registration.png)

4. Podaj **nazwa** i **adres URL logowania** dla aplikacji. Można użyć wartości symboli zastępczych, ponieważ wartości są zmieniane podczas wdrażania. 

    ![Tworzenie rejestracji aplikacji](media/deploy/app-registration-create.png)

    |Ustawienie  | Wartość  |
    |---------|---------|
    |Name (Nazwa) | `Blockchain API` |
    |Typ aplikacji |Aplikacja sieci Web / interfejsu API|
    |Adres URL logowania | `https://blockchainapi` |

5. Wybierz **Utwórz** do rejestrowania aplikacji usługi Azure AD.

### <a name="modify-manifest"></a>Zmodyfikuj manifest

Następnie należy zmodyfikować manifest, użyj ról aplikacji w usłudze Azure AD, aby określić administratorów aplikacji Blockchain Workbench.  Aby uzyskać więcej informacji na temat manifestów aplikacji, zobacz [manifest aplikacji w usłudze Azure Active Directory](../../active-directory/develop/reference-app-manifest.md).

1. Aplikacja została zarejestrowana, można wybrać **manifestu** w okienku szczegółów zarejestrowanej aplikacji.
2. Wygeneruj identyfikator GUID. Można wygenerować identyfikatora GUID za pomocą polecenia programu PowerShell [identyfikator guid]:: (NewGuid) lub identyfikator GUID nowego polecenia cmdlet. Innym rozwiązaniem jest użyć generator GUID witryny sieci Web.
3. Zamierzasz zaktualizować **appRoles** sekcji manifestu. W okienku manifestu edycji wybierz **Edytuj** i Zastąp `"appRoles": []` przy użyciu podanego formatu JSON. Pamiętaj zastąpić wartość **identyfikator** pola o identyfikatorze GUID został wygenerowany. 

    ![Edytuj manifest](media/deploy/edit-manifest.png)

    ``` json
    "appRoles": [
         {
           "allowedMemberTypes": [
             "User",
             "Application"
           ],
           "displayName": "Administrator",
           "id": "<A unique GUID>",
           "isEnabled": true,
           "description": "Blockchain Workbench administrator role allows creation of applications, user to role assignments, etc.",
           "value": "Administrator"
         }
       ],
    ```

    > [!IMPORTANT]
    > Wartość **administratora** jest potrzebna do identyfikowania administratorów aplikacji Blockchain Workbench.

4. W manifeście, należy również zmienić **Oauth2AllowImplicitFlow** wartość **true**.

    ``` json
    "oauth2AllowImplicitFlow": true,
    ```

5. Wybierz **Zapisz** można zapisać zmian manifestu.

### <a name="add-graph-api-required-permissions"></a>Dodawanie interfejsu API programu Graph wymagane uprawnienia

Aplikacja interfejsu API musi żądać uprawnienia od użytkownika, aby uzyskać dostęp do katalogu. Ustaw następujące wymagane uprawnienia dla aplikacji interfejsu API:

1. Rejestracja aplikacji interfejsu API łańcucha bloków, wybierz **Ustawienia > wymagane uprawnienia > Wybierz interfejs API > Microsoft Graph**.

    ![Wybieranie interfejsu API](media/deploy/client-app-select-api.png)

    Kliknij pozycję **Wybierz**.

2. W **Włącz dostęp za pomocą** w obszarze **delegowane uprawnienia**, wybierz **odczyt profilów podstawowych wszystkich użytkowników**.

    ![Włącz dostęp](media/deploy/client-app-read-perms.png)

    Wybierz **Zapisz** polecenie **gotowe**.

3. W **wymagane uprawnienia**, wybierz opcję **Udziel uprawnień** polecenie **tak** monitu dotyczącego weryfikacji.

   ![Udzielenie uprawnień](media/deploy/client-app-grant-permissions.png)

   Nadawanie uprawnień umożliwia aplikacji Blockchain Workbench uzyskać dostęp użytkowników w katalogu. Uprawnienia do odczytu jest wymagany do wyszukiwania i dodawanie członków do aplikacji Blockchain Workbench.

### <a name="get-application-id"></a>Uzyskiwanie Identyfikatora aplikacji

Informacje o identyfikatorze i dzierżawy aplikacji są wymagane do wdrożenia. Zbieranie i przechowywanie informacji do użycia podczas wdrażania.

1. Aplikacja została zarejestrowana, można wybrać **ustawienia** > **właściwości**.
2. W **właściwości** okienko, kopiowanie i magazynu następujące wartości do późniejszego użytku podczas wdrażania.

    ![Właściwości aplikacji interfejsu API](media/deploy/app-properties.png)

    | Ustawienia dotyczącego przechowywania  | Użyj we wdrożeniu |
    |------------------|-------------------|
    | Identyfikator aplikacji | Konfiguracja usługi Azure Active Directory > Identyfikator aplikacji |

### <a name="get-tenant-domain-name"></a>Uzyskiwanie dzierżawy nazwy domeny

Zbieranie i przechowywanie nazwa domeny dzierżawy usługi Active Directory, w których aplikacje są rejestrowane. 

W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory**. Wybierz pozycję **Niestandardowe nazwy domen**. Skopiuj i Zapisz nazwę domeny.

![Nazwa domeny](media/deploy/domain-name.png)

### <a name="guest-user-settings"></a>Ustawienia użytkownika gościa

Jeśli masz użytkowników-gości w dzierżawie usługi Azure AD, należy wykonać dodatkowe kroki w celu zapewnienia Blockchain Workbench przypisanie użytkownika i zarządzania działa poprawnie.

1. Przełączenie dzierżawy usługi Azure AD, a następnie wybierz pozycję **usługi Azure Active Directory > Ustawienia użytkownika > Zarządzaj ustawieniami współpracy zewnętrznej**.
2. Ustaw **uprawnień użytkowników gości są ograniczone** do **nie**.
    ![Ustawienia zewnętrznej współpracy](media/deploy/user-collaboration-settings.png)

## <a name="configuring-the-reply-url"></a>Konfigurowanie adresu URL odpowiedzi

Po wdrożeniu aplikacji Azure Blockchain Workbench, musisz skonfigurować aplikację klienta usługi Azure Active Directory (Azure AD) **adres URL odpowiedzi** dla wdrożonej aplikacji Blockchain Workbench adres URL sieci web.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Sprawdź, czy jesteś w dzierżawie, w którym zarejestrowana aplikacja kliencka usługi Azure AD.
3. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory**. Wybierz pozycję **Rejestracje aplikacji**.
4. Wybierz aplikację klienta usługi Azure AD zarejestrowanej w sekcji wymagań wstępnych.
5. Wybierz **Ustawienia > adresy URL odpowiedzi**.
6. Podaj adres URL głównego sieci web, wdrażania aplikacji Azure Blockchain Workbench możesz pobrać w **uzyskać adres URL sieci Web Azure Blockchain Workbench** sekcji. Adres URL odpowiedzi jest poprzedzony znakiem `https://`. Na przykład: `https://myblockchain2-7v75.azurewebsites.net`

    ![Adresy URL odpowiedzi](media/deploy/configure-reply-url.png)

7. Wybierz **Zapisz** można zaktualizować rejestracji klienta.

## <a name="remove-a-deployment"></a>Usuwanie wdrożenia

Po wdrożeniu nie jest już potrzebny, można usunąć wdrożenia, usuwając grupę zasobów aplikacji Blockchain Workbench.

1. W witrynie Azure portal przejdź do **grupy zasobów** w okienku nawigacji po lewej stronie i wybierz grupę zasobów, którą chcesz usunąć. 
2. Wybierz pozycję **Usuń grupę zasobów**. Sprawdź usunięcie, wpisując nazwę grupy zasobów, a następnie wybierz pozycję **Usuń**.

    ![Usuwanie grupy zasobów](media/deploy/delete-resource-group.png)

## <a name="next-steps"></a>Kolejne kroki

W tym artykule Ci się wdrożyć aplikacji Azure Blockchain Workbench. Informacje na temat tworzenia aplikacji łańcucha bloków, przejdź do następnego artykułu porad.

> [!div class="nextstepaction"]
> [Tworzenie aplikacji łańcucha bloków w aplikacji Azure Blockchain Workbench](create-app.md)
