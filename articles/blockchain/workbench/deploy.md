---
title: Wdrażanie systemu Azure Blockchain Workbench Preview
description: Jak wdrożyć wersję roboczka łańcucha bloków platformy Azure
ms.date: 01/08/2020
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: 141bb8825e47eb2309f9f551990a2976e8f4e209
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78943199"
---
# <a name="deploy-azure-blockchain-workbench-preview"></a>Wdrażanie systemu Azure Blockchain Workbench Preview

Usługa Azure Blockchain Workbench Preview jest wdrażana przy użyciu szablonu rozwiązania w portalu Azure Marketplace. Szablon upraszcza wdrażanie składników potrzebnych do tworzenia aplikacji blockchain. Po wdrożeniu Blockchain Workbench zapewnia dostęp do aplikacji klienckich do tworzenia użytkowników i aplikacji blockchain oraz zarządzania nimi.

Aby uzyskać więcej informacji na temat składników blockchain workbench, zobacz [Architektura azure blockchain workbench](architecture.md).

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="prepare-for-deployment"></a>Przygotowanie do wdrożenia

Blockchain Workbench umożliwia wdrożenie księgi blockchain wraz z zestawem odpowiednich usług platformy Azure najczęściej używanych do tworzenia aplikacji opartej na blockchain. Wdrożenie robota Blockchain Workbench powoduje, że następujące usługi platformy Azure są aprowiedzone w ramach grupy zasobów w ramach subskrypcji platformy Azure.

* Plan usługi aplikacji (standardowy)
* Application Insights
* Event Grid
* W usłudze Azure Key Vault
* Service Bus
* Baza danych SQL (standard S0) + serwer logiczny SQL
* Konto usługi Azure Storage (standard LRS)
* Zestaw skalowania maszyny wirtualnej o pojemności 1
* Grupa zasobów sieci wirtualnej (z modułem równoważenia obciążenia, sieciową grupą zabezpieczeń, publicznym adresem IP, siecią wirtualną)
* Usługa Azure Blockchain. Jeśli używasz poprzedniego wdrożenia blockchain workbench, należy rozważyć ponowne wdrożenie azure blockchain workbench do korzystania z usługi Azure Blockchain Service.

Poniżej przedstawiono przykładowe wdrożenie utworzone w grupie zasobów **myblockchain.**

![Przykładowe wdrożenie](media/deploy/example-deployment.png)

Koszt blockchain workbench jest sumą kosztów podstawowych usług platformy Azure. Informacje o cenach usług platformy Azure można obliczyć za pomocą [kalkulatora cen](https://azure.microsoft.com/pricing/calculator/).

## <a name="prerequisites"></a>Wymagania wstępne

Azure Blockchain Workbench wymaga konfiguracji usługi Azure AD i rejestracji aplikacji. Konfiguracje usługi Azure AD można wykonać [ręcznie](#azure-ad-configuration) przed wdrożeniem lub uruchomić wdrożenie po zakończeniu skryptu. Jeśli ponownie wdrożysz program Blockchain Workbench, zobacz [konfigurację usługi Azure AD,](#azure-ad-configuration) aby zweryfikować konfigurację usługi Azure AD.

> [!IMPORTANT]
> Workbench nie musi być wdrożony w tej samej dzierżawie, co ta, której używasz do rejestrowania aplikacji usługi Azure AD. Workbench musi być wdrożony w dzierżawie, gdzie masz wystarczające uprawnienia do wdrażania zasobów. Aby uzyskać więcej informacji na temat dzierżaw usługi Azure AD, zobacz [Jak uzyskać dzierżawę usługi Active Directory](../../active-directory/develop/quickstart-create-new-tenant.md) i [integrować aplikacje z usługą Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md).

## <a name="deploy-blockchain-workbench"></a>Wdrażanie warsztatu blockchain

Po wykonaniu wstępnych kroków można przystąpić do wdrożenia workbench blockchain. W poniższych sekcjach opisano sposób wdrażania struktury.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. Wybierz swoje konto w prawym górnym rogu i przełącz się do żądanej dzierżawy usługi Azure AD, w której chcesz wdrożyć pakiet Azure Blockchain Workbench.
1. W lewym górnym rogu witryny Azure Portal wybierz pozycję **Utwórz zasób**.
1. Wybierz **Blockchain** > **Azure Blockchain Workbench (wersja zapoznawcza)**.

    ![Tworzenie skoroszytu łańcucha bloków platformy Azure](media/deploy/blockchain-workbench-settings-basic.png)

    | Ustawienie | Opis  |
    |---------|--------------|
    | Prefiks zasobu | Krótki unikatowy identyfikator wdrożenia. Ta wartość jest używana jako podstawa do nazewnictwa zasobów. |
    | Nazwa użytkownika maszyny Wirtualnej | Nazwa użytkownika jest używana jako administrator dla wszystkich maszyn wirtualnych (VM). |
    | Typ uwierzytelniania | Wybierz, czy chcesz użyć hasła lub klucza do łączenia się z maszynami wirtualnymi. |
    | Hasło | Hasło służy do łączenia się z maszynami wirtualnymi. |
    | Protokół SSH | Użyj klucza publicznego RSA w formacie jednowierszowym, zaczynając od **ssh-rsa** lub użyj wielowierszowego formatu PEM. Można wygenerować `ssh-keygen` klucze SSH przy użyciu w systemie Linux i OS X lub za pomocą PuTTYGen w systemie Windows. Więcej informacji na temat kluczy SSH można znaleźć [w programie Jak używać kluczy SSH z systemem Windows na platformie Azure](../../virtual-machines/linux/ssh-from-windows.md). |
    | Hasło bazy danych i łańcucha bloków | Określ hasło, które ma być używane do uzyskiwania dostępu do bazy danych utworzonej w ramach wdrożenia. Hasło musi spełniać trzy z następujących czterech wymagań: długość musi wynosić od 12 & 72 znaków, 1 małe litery, 1 wielkie litery, 1 liczba i 1 znak specjalny, który\`nie jest znakiem liczby(#), percent(%), przecinek(,), gwiazdką(*), cytatem tylnym(), podwójnym cudzysłowem("), pojedynczym cudzysłowem('), kreską(-) i średnikiem(;) |
    | Region wdrażania | Określ, gdzie należy wdrażać zasoby blockchain workbench. Aby uzyskać najlepszą dostępność, powinno to być zgodne z ustawieniem **Lokalizacja.** |
    | Subskrypcja | Określ subskrypcję platformy Azure, której chcesz użyć do wdrożenia. |
    | Grupy zasobów | Utwórz nową grupę zasobów, wybierając pozycję **Utwórz nowe** i określ unikatową nazwę grupy zasobów. |
    | Lokalizacja | Określ region, w którym chcesz wdrożyć strukturę. |

1. Wybierz **przycisk OK,** aby zakończyć sekcję konfiguracji ustawień podstawowych.

1. W **ustawieniach zaawansowanych**wybierz, czy chcesz utworzyć nową sieć blockchain, czy też użyć istniejącej sieci blockchain proof-of-authority.

    Dla **Utwórz nowy**:

    Nowa opcja *tworzenia* wdraża księgę kworum usługi Azure Blockchain z domyślnym podstawowym sku.

    ![Zaawansowane ustawienia dla nowej sieci blockchain](media/deploy/advanced-blockchain-settings-new.png)

    | Ustawienie | Opis  |
    |---------|--------------|
    | Warstwa cenowa usługi Azure Blockchain Service | Wybieranie **warstwy Podstawowa** lub **Standardowa** usługa Azure Blockchain, która jest używana w witrynie Blockchain Workbench |
    | Ustawienia usługi Azure Active Directory | Wybierz **pozycję Dodaj później**.</br>Uwaga: Jeśli wybrano [opcję wstępnej konfiguracji usługi Azure AD](#azure-ad-configuration) lub ponowne wdrożenie, wybierz opcję Dodaj *teraz*. |
    | Wybór maszyny Wirtualnej | Wybierz preferowaną wydajność pamięci masowej i rozmiar maszyny Wirtualnej dla sieci blockchain. Wybierz mniejszy rozmiar maszyny Wirtualnej, taki jak *Standard DS1 v2,* jeśli korzystasz z subskrypcji z niskimi limitami usług, takimi jak warstwa bezpłatna platformy Azure. |

    Do **wykorzystania istniejących:**

    *Użycie istniejącej* opcji pozwala określić sieć blockchain Ethereum Proof-of-Authority (PoA). Punkty końcowe mają następujące wymagania.

   * Punktem końcowym musi być sieć łańcucha bloków Ethereum Proof-of-Authority (PoA).
   * Punkt końcowy musi być publicznie dostępny za pośrednictwem sieci.
   * Sieć blockchain PoA powinna być skonfigurowana tak, aby cena gazu była ustawiona na zero.

     > [!NOTE]
     > Konta Blockchain Workbench nie są finansowane. Jeśli wymagane są środki, transakcje nie powiodą się.

     ![Zaawansowane ustawienia istniejącej sieci blockchain](media/deploy/advanced-blockchain-settings-existing.png)

     | Ustawienie | Opis  |
     |---------|--------------|
     | Punkt końcowy RPC Ethereum | Podaj punkt końcowy RPC istniejącej sieci blockchain PoA. Punkt końcowy rozpoczyna się od https:// lub http:// i kończy się numerem portu. Na przykład: `http<s>://<network-url>:<port>` |
     | Ustawienia usługi Azure Active Directory | Wybierz **pozycję Dodaj później**.</br>Uwaga: Jeśli wybrano [opcję wstępnej konfiguracji usługi Azure AD](#azure-ad-configuration) lub ponowne wdrożenie, wybierz opcję Dodaj *teraz*. |
     | Wybór maszyny Wirtualnej | Wybierz preferowaną wydajność pamięci masowej i rozmiar maszyny Wirtualnej dla sieci blockchain. Wybierz mniejszy rozmiar maszyny Wirtualnej, taki jak *Standard DS1 v2,* jeśli korzystasz z subskrypcji z niskimi limitami usług, takimi jak warstwa bezpłatna platformy Azure. |

1. Wybierz **przycisk OK,** aby zakończyć ustawienia zaawansowane.

1. Przejrzyj podsumowanie, aby sprawdzić, czy parametry są dokładne.

    ![Podsumowanie](media/deploy/blockchain-workbench-summary.png)

1. Wybierz **pozycję Utwórz,** aby zaakceptować warunki i wdrożyć swój system Azure Blockchain Workbench.

Wdrożenie może potrwać do 90 minut. Za pomocą witryny Azure portal można monitorować postęp. W nowo utworzonej grupie zasobów wybierz pozycję **Wdrożenia > Omówienie,** aby wyświetlić stan wdrożonych artefaktów.

> [!IMPORTANT]
> Po wdrożeniu należy wykonać ustawienia usługi Active Directory. Jeśli wybrano **opcję Dodaj później,** musisz uruchomić [skrypt konfiguracji usługi Azure AD](#azure-ad-configuration-script).  Jeśli wybierzesz **Dodaj teraz,** musisz [skonfigurować adres URL odpowiedzi](#configuring-the-reply-url).

## <a name="blockchain-workbench-web-url"></a>Adres URL sieci Blockchain Workbench

Po zakończeniu wdrażania blockchain workbench, nowa grupa zasobów zawiera zasoby Blockchain Workbench. Usługi Blockchain Workbench są dostępne za pośrednictwem adresu URL sieci Web. Poniższe kroki pokazują, jak pobrać adres URL sieci Web wdrożonej struktury.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. W lewym okienku nawigacji wybierz pozycję **Grupy zasobów**.
1. Wybierz nazwę grupy zasobów określoną podczas wdrażania systemu Blockchain Workbench.
1. Wybierz nagłówek kolumny **TYP,** aby posortować listę alfabetycznie według typu.
1. Istnieją dwa zasoby z **typem App Service**. Wybierz zasób typu **App Service** *bez* sufiksu "-api".

    ![Lista usług aplikacji](media/deploy/resource-group-list.png)

1. W **przeglądzie**usługi app service skopiuj wartość **adresu URL,** która reprezentuje internetowy adres URL do wdrożonego workbencha blockchain.

    ![Podstawowe informacje o usłudze aplikacji](media/deploy/app-service.png)

Aby skojarzyć niestandardową nazwę domeny z programem Blockchain Workbench, zobacz [konfigurowanie niestandardowej nazwy domeny dla aplikacji sieci Web w usłudze Azure App Service przy użyciu usługi Traffic Manager](../../app-service/configure-domain-traffic-manager.md).

## <a name="azure-ad-configuration-script"></a>Skrypt konfiguracji usługi Azure AD

Usługa Azure AD musi być skonfigurowana do ukończenia wdrożenia programu Blockchain Workbench. Skrypt programu PowerShell służy do konfiguracji.

1. W przeglądarce przejdź do [adresu URL sieci Web Blockchain Workbench](#blockchain-workbench-web-url).
1. Zobaczysz instrukcje konfigurowania usługi Azure AD przy użyciu usługi Cloud Shell. Skopiuj polecenie i uruchom powłokę Cloud Shell.

    ![Uruchamianie skryptu AAD](media/deploy/launch-aad-script.png)

1. Wybierz dzierżawę usługi Azure AD, w której wdrożono system Blockchain Workbench.
1. W środowisku programu PowerShell programu Cloud Shell wklej i uruchom polecenie.
1. Po wyświetleniu monitu wprowadź dzierżawę usługi Azure AD, której chcesz użyć w witrynie Blockchain Workbench. Będzie to najemca zawierający użytkowników blockchain Workbench.

    > [!IMPORTANT]
    > Uwierzytelniony użytkownik wymaga uprawnień do tworzenia rejestracji aplikacji usługi Azure AD i udzielania uprawnień aplikacji delegowanych w dzierżawie. Może być konieczne zwrócenie się do administratora dzierżawy o uruchomienie skryptu konfiguracji usługi Azure AD lub utworzenie nowej dzierżawy.

    ![Wprowadź dzierżawę usługi Azure AD](media/deploy/choose-tenant.png)

1. Zostanie wyświetlony monit o uwierzytelnienie w dzierżawie usługi Azure AD przy użyciu przeglądarki. Otwórz internetowy adres URL w przeglądarce, wprowadź kod i uwierzytelnij się.

    ![Uwierzytelnij się przy użyciu kodu](media/deploy/authenticate.png)

1. Skrypt wyprowadza kilka komunikatów o stanie. Otrzymasz komunikat o stanie **SUCCESS,** jeśli dzierżawca została pomyślnie zainicjowana.
1. Przejdź do adresu URL warsztatu blockchain. Użytkownik jest proszony o wyrażenie zgody na przyznanie uprawnień do odczytu do katalogu. Dzięki temu blockchain workbench dostęp do aplikacji sieci web dla użytkowników w dzierżawie. Jeśli jesteś administratorem dzierżawy, możesz wyrazić zgodę dla całej organizacji. Ta opcja akceptuje zgodę dla wszystkich użytkowników w dzierżawie. W przeciwnym razie każdy użytkownik jest monitowany o zgodę na pierwsze użycie aplikacji internetowej Blockchain Workbench.
1. Wybierz **pozycję Zaakceptuj** do wyrażenia zgody.

     ![Zgoda na czytanie profili użytkowników](media/deploy/graph-permission-consent.png)

1. Po wyrażeniu zgody można użyć aplikacji internetowej Blockchain Workbench.

Ukończono wdrożenie usługi Azure Blockchain Workbench. Zobacz [Następne kroki,](#next-steps) aby uzyskać sugestie dotyczące rozpoczęcia korzystania z wdrożenia.

## <a name="azure-ad-configuration"></a>Konfiguracja usługi Azure AD

Jeśli zdecydujesz się ręcznie skonfigurować lub zweryfikować ustawienia usługi Azure AD przed wdrożeniem, wykonaj wszystkie kroki opisane w tej sekcji. Jeśli wolisz automatycznie konfigurować ustawienia usługi Azure AD, użyj [skryptu konfiguracji usługi Azure AD](#azure-ad-configuration-script) po wdrożeniu programu Blockchain Workbench.

### <a name="blockchain-workbench-api-app-registration"></a>Rejestracja aplikacji interfejsu API blockchain Workbench

Wdrożenie systemu Blockchain Workbench wymaga rejestracji aplikacji usługi Azure AD. Do zarejestrowania aplikacji potrzebna jest dzierżawa usługi Azure Active Directory (Azure AD). Można użyć istniejącej dzierżawy lub utworzyć nową dzierżawę. Jeśli używasz istniejącej dzierżawy usługi Azure AD, potrzebujesz wystarczających uprawnień do rejestrowania aplikacji, udzielania uprawnień interfejsu API programu Graph i zezwalania na dostęp gościa w dzierżawie usługi Azure AD. Jeśli nie masz wystarczających uprawnień w istniejącej dzierżawie usługi Azure AD, utwórz nową dzierżawę.


1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. Wybierz swoje konto w prawym górnym rogu i przełącz się do żądanej dzierżawy usługi Azure AD. Dzierżawa powinna być dzierżawą administratora subskrypcji subskrypcji, w której jest wdrażany azure blockchain workbench i masz wystarczające uprawnienia do rejestrowania aplikacji.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory**. Wybierz **rejestracje** > aplikacji**Nowa rejestracja**.

    ![Rejestracja aplikacji](media/deploy/app-registration.png)

1. Podaj wyświetlaną **nazwę** i wybierz **pozycję Konta tylko w tym katalogu organizacyjnym**.

    ![Tworzenie rejestracji aplikacji](media/deploy/app-registration-create.png)

1. Wybierz **pozycję Zarejestruj,** aby zarejestrować aplikację usługi Azure AD.

### <a name="modify-manifest"></a>Modyfikowanie manifestu

Następnie należy zmodyfikować manifest, aby użyć ról aplikacji w usłudze Azure AD, aby określić administratorów blockchain workbench.  Aby uzyskać więcej informacji na temat manifestów aplikacji, zobacz [manifest aplikacji usługi Azure Active Directory](../../active-directory/develop/reference-app-manifest.md).


1. Identyfikator GUID jest wymagany dla manifestu. Identyfikator GUID można wygenerować `[guid]::NewGuid()` za `New-GUID` pomocą polecenia programu PowerShell lub polecenia cmdlet. Inną opcją jest użycie strony internetowej generatora GUID.
1. Dla zarejestrowanej aplikacji wybierz **manifest** w sekcji **Zarządzanie.**
1. Następnie zaktualizuj sekcję **appRoles** manifestu. Wymień `"appRoles": []` na dostarczony JSON. Pamiętaj, aby zastąpić wartość pola **id** wygenerowanym identyfikatorem GUID. 

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
    > **Administrator** wartości jest potrzebny do identyfikacji administratorów Blockchain Workbench.

1. W manifeście zmień również wartość **Oauth2AllowImplicitFlow** na **true**.

    ``` json
    "oauth2AllowImplicitFlow": true,
    ```

1. Wybierz **pozycję Zapisz,** aby zapisać zmiany manifestu.

### <a name="add-graph-api-required-permissions"></a>Dodawanie wymaganych uprawnień interfejsu API wykresu

Aplikacja interfejsu API musi zażądać uprawnień od użytkownika, aby uzyskać dostęp do katalogu. Ustaw następujące wymagane uprawnienia dla aplikacji interfejsu API:

1. W rejestracji aplikacji *Interfejsu API łańcucha bloków* wybierz pozycję Uprawnienia interfejsu **API**. Domyślnie dodaje się uprawnienie Graph API **User.Read.**
1. Aplikacja Workbench wymaga dostępu do odczytu podstawowych informacji o profilu użytkowników. W *obszarze Skonfigurowane uprawnienia*wybierz pozycję Dodaj **uprawnienie**. W **interfejsach API firmy Microsoft**wybierz pozycję Microsoft **Graph**.
1. Ponieważ aplikacja Workbench używa uwierzytelnionych poświadczeń użytkownika, wybierz pozycję **Delegowane uprawnienia**.
1. W kategorii *Użytkownik* wybierz pozycję **User.ReadBasic.All** permission.

    ![Konfiguracja rejestracji aplikacji usługi Azure AD przedstawiająca dodawanie uprawnienia użytkownika programu Microsoft Graph.ReadBasic.All delegowane](media/deploy/add-graph-user-permission.png)

    Wybierz pozycję **Dodaj uprawnienia**.

1. W *obszarze Skonfigurowane uprawnienia*wybierz pozycję **Udzielaj zgody administratora** dla domeny, a następnie wybierz pozycję **Tak,** aby wyświetlić monit weryfikacyjny.

   ![Udzielenie uprawnień](media/deploy/client-app-grant-permissions.png)

   Przyznanie uprawnień umożliwia Blockchain Workbench dostęp do użytkowników w katalogu. Uprawnienie do odczytu jest wymagane do wyszukiwania i dodawania członków do Blockchain Workbench.

### <a name="get-application-id"></a>Pobierz identyfikator aplikacji

Identyfikator aplikacji i informacje o dzierżawie są wymagane do wdrożenia. Zbieranie i przechowywanie informacji do użycia podczas wdrażania.

1. Dla zarejestrowanej aplikacji wybierz **opcję Przegląd**.
1. Skopiuj i przechowuj wartość **identyfikatora aplikacji** do późniejszego użycia podczas wdrażania.

    ![Właściwości aplikacji interfejsu API](media/deploy/app-properties.png)

    | Ustawienie do przechowywania  | Użycie we wdrożeniu |
    |------------------|-------------------|
    | Identyfikator aplikacji (klienta) | > identyfikator aplikacji konfiguracji usługi Azure Active Directory |

### <a name="get-tenant-domain-name"></a>Uzyskaj nazwę domeny dzierżawy

Zbieranie i przechowywanie nazwy domeny dzierżawy usługi Active Directory, w której są zarejestrowane aplikacje. 

W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory**. Wybierz pozycję **Niestandardowe nazwy domen**. Skopiuj i przechowuj nazwę domeny.

![Nazwa domeny](media/deploy/domain-name.png)

### <a name="guest-user-settings"></a>Ustawienia użytkownika gościa

Jeśli masz użytkowników-gości w dzierżawie usługi Azure AD, wykonaj dodatkowe kroki, aby upewnić się, że przypisywanie i zarządzanie użytkownikami blockchain Workbench działa poprawnie.

1. Przełącz dzierżawę usługi Azure AD i wybierz pozycję **Ustawienia usługi Azure Active Directory > ustawienia użytkownika > Zarządzanie ustawieniami współpracy zewnętrznej**.
1. Ustaw **uprawnienia użytkownika gościa są ograniczone** do **nie**.
    ![Ustawienia zewnętrznej współpracy](media/deploy/user-collaboration-settings.png)

## <a name="configuring-the-reply-url"></a>Konfigurowanie adresu URL odpowiedzi

Po wdrożeniu workbench azure blockchain, trzeba skonfigurować adres **URL odpowiedzi** aplikacji klienta usługi Azure Active Directory (Azure AD) z wdrożonego adresu URL sieci Web blockchain Workbench.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. Sprawdź, czy jesteś w dzierżawie, w której zarejestrowano aplikację kliencką usługi Azure AD.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory**. Wybierz pozycję **Rejestracje aplikacji**.
1. Wybierz aplikację kliencką usługi Azure AD zarejestrowaną w sekcji wymagania wstępne.
1. Wybierz pozycję **Uwierzytelnianie**.
1. Określ główny adres URL sieci Web wdrożenia programu Azure Blockchain Workbench pobranego w sekcji [Adres URL sieci Web blockchain Workbench.](#blockchain-workbench-web-url) Adres URL odpowiedzi jest `https://`poprzedzony . Na przykład: `https://myblockchain2-7v75.azurewebsites.net`

    ![Adresy URL odpowiedzi uwierzytelniania](media/deploy/configure-reply-url.png)

1. W sekcji **Ustawienia zaawansowane** sprawdź **tokeny dostępu** i **tokeny identyfikatorów**.

    ![Ustawienia zaawansowane uwierzytelniania](media/deploy/authentication-advanced-settings.png)

1. Wybierz **pozycję Zapisz,** aby zaktualizować rejestrację klienta.

## <a name="remove-a-deployment"></a>Usuwanie wdrożenia

Jeśli wdrożenie nie jest już potrzebne, można usunąć wdrożenie, usuwając grupę zasobów Blockchain Workbench.

1. W witrynie Azure Portal przejdź do **grupy zasobów** w lewym okienku nawigacji i wybierz grupę zasobów, którą chcesz usunąć. 
1. Wybierz pozycję **Usuń grupę zasobów**. Zweryfikuj usunięcie, wprowadzając nazwę grupy zasobów i wybierając pozycję **Usuń**.

    ![Usuwanie grupy zasobów](media/deploy/delete-resource-group.png)

## <a name="next-steps"></a>Następne kroki

W tym artykule instruszuj wdrożono platformę Azure Blockchain Workbench. Aby dowiedzieć się, jak utworzyć aplikację blockchain, przejdź do następnego artykułu in jak to zrobić.

> [!div class="nextstepaction"]
> [Tworzenie aplikacji łańcucha bloków w usłudze Azure Blockchain Workbench](create-app.md)
