---
title: Wdrażanie usługi Azure Workbench Blockchain
description: Jak wdrożyć Azure Blockchain Workbench
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/17/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: bcd08ac8563edfaf4297e26ad42ed8bc62d86918
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34831639"
---
# <a name="deploy-azure-blockchain-workbench"></a>Wdrażanie usługi Azure Workbench Blockchain

Azure Blockchain Workbench jest wdrażany w portalu Azure Marketplace przy użyciu szablonu rozwiązania. Szablon upraszcza wdrażanie składników niezbędnych do tworzenia aplikacji blockchain. Po wdrożeniu Blockchain Workbench zapewnia dostęp do aplikacji klienta, tworzenie i zarządzanie nimi użytkowników i blockchain aplikacji.

Aby uzyskać więcej informacji o składnikach Blockchain Workbench, zobacz [architektura Azure Blockchain Workbench](blockchain-workbench-architecture.md).

## <a name="prepare-for-deployment"></a>Przygotowanie do wdrożenia

Blockchain Workbench umożliwia wdrażanie księgowe blockchain wraz z zestawem odpowiednich usług Azure najczęściej używanych do tworzenia aplikacji opartych na blockchain. Wdrażanie Blockchain Workbench powoduje następujących usług platformy Azure, są udostępniane w ramach grupy zasobów w Twojej subskrypcji platformy Azure.

* Temat siatki zdarzenia 1
* Namespace magistrali usługi 1
* 1 application Insights
* 1 bazy danych SQL (standardowa S0)
* Usługi aplikacji 2 (standardowy)
* 2 magazynów kluczy azure
* 2 konta usługi azure Storage (Standard-LRS)
* 2 zestawy skalowania maszyny wirtualnej (w przypadku węzłów modułu sprawdzania poprawności i proces roboczy)
* 2 sieci wirtualnych (w tym usługi równoważenia obciążenia, grupy zabezpieczeń sieci i publiczny adres IP dla każdej sieci wirtualnej)
* Opcjonalnie: Monitor Azure

Poniżej przedstawiono przykład wdrożenia utworzone w **myblockchain** grupy zasobów.

![Przykład wdrożenia](media/blockchain-workbench-deploy/example-deployment.png)

Koszt Blockchain Workbench jest sumą kosztów podstawowej usług Azure. Informacje o cenach dla usługi Azure może być obliczona w [Kalkulator cen](https://azure.microsoft.com/pricing/calculator/).

Azure Blockchain Workbench wymaga kilka wymagań wstępnych przed ich wdrożeniem. Wymagania wstępne obejmują rejestracji konfiguracji i aplikacji usługi Azure AD.

### <a name="blockchain-workbench-api-app-registration"></a>Rejestracja aplikacji interfejsu API Workbench Blockchain

Deployment Blockchain Workbench wymaga rejestracji aplikacji usługi Azure AD. Należy dzierżawy usługi Azure Active Directory (Azure AD) do rejestrowania aplikacji. Można użyć istniejącej dzierżawy lub utworzyć nową dzierżawę. Jeśli korzystasz z istniejącej dzierżawy usługi Azure AD, należy wystarczające uprawnienia do rejestrowania aplikacji w dzierżawie usługi Azure AD. Rejestracje aplikacji muszą być administratora subskrypcji dzierżawcy subskrypcji, w którym jest wdrażany Workbench. Aby uzyskać więcej informacji dotyczących dzierżaw usługi Azure AD, zobacz [jak uzyskać dzierżawę usługi Active Directory](../active-directory/develop/active-directory-howto-tenant.md) i [Integrowanie aplikacji z usługą Azure Active Directory](../active-directory/develop/active-directory-integrating-applications.md).

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz konto w prawym górnym rogu i przejdź do żądanego usługi Azure AD dzierżawy. Dzierżawa powinna być administratora subskrypcji dzierżawcy subskrypcji, w którym jest wdrażany Workbench i masz wystarczające uprawnienia do rejestrowania aplikacji.
3. W okienku nawigacji po lewej stronie wybierz **usługi Azure Active Directory** usługi. Wybierz **rejestracji aplikacji** > **nowej rejestracji aplikacji**.

    ![Rejestracja aplikacji](media/blockchain-workbench-deploy/app-registration.png)

4. Podaj **nazwa** i **adres URL logowania** dla aplikacji. Można użyć wartości symbolu zastępczego, ponieważ wartości są zmieniane podczas wdrażania. 

    ![Tworzenie rejestracji aplikacji](media/blockchain-workbench-deploy/app-registration-create.png)

    |Ustawienie  | Wartość  |
    |---------|---------|
    |Name (Nazwa) | `Blockchain API` |
    |Typ aplikacji |Interfejs API/aplikacja internetowa|
    |Adres URL logowania | `https://blockchainapi` |

5. Wybierz **Utwórz** można zarejestrować aplikacji usługi Azure AD.

### <a name="modify-application-manifest"></a>Zmodyfikuj manifest aplikacji

Następnie należy zmodyfikować manifest aplikacji do korzystania z ról aplikacji w usłudze Azure AD, aby określić Blockchain Workbench Administratorzy.  Aby uzyskać więcej informacji na temat manifestów aplikacji, zobacz [manifest aplikacji usługi Azure Active Directory](../active-directory/develop/active-directory-application-manifest.md).

1. Dla aplikacji został zarejestrowany, wybierz **manifestu** w okienku szczegółów zarejestrowanej aplikacji.
2. Generuje identyfikator GUID. Można wygenerować identyfikatora GUID przy użyciu polecenia programu PowerShell [identyfikator guid]:: NewGuid () lub identyfikator GUID nowego polecenia cmdlet. Innym rozwiązaniem jest na identyfikator GUID generator witrynę sieci Web.
3. Zamierzasz zaktualizować **appRoles** sekcji manifestu. W okienku manifestu edycji wybierz **Edytuj** i Zastąp `"appRoles": []` z podanych JSON. Pamiętaj zastąpić wartość **identyfikator** o identyfikatorze GUID wygenerowanego pola. 

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

    ![Edytowanie manifestu](media/blockchain-workbench-deploy/edit-manifest.png)

    > [!IMPORTANT]
    > Wartość **administratora** są potrzebne, aby zidentyfikować Blockchain Workbench Administratorzy.

4.  Kliknij przycisk **zapisać** można zapisać zmian manifestu aplikacji.

### <a name="add-graph-api-required-permissions"></a>Dodaj uprawnienia wymagane interfejsu API programu Graph

Aplikacja interfejsu API musi poprosić uprawnienia użytkownika do dostępu do katalogu. Ustaw następujące uprawnienia wymagane dla aplikacji interfejsu API:

1. Interfejs API Blockchain rejestracji aplikacji, wybierz **Ustawienia > wymagane uprawnienia > Wybierz interfejs API > Microsoft Graph**.

    ![Wybieranie interfejsu API](media/blockchain-workbench-deploy/client-app-select-api.png)

    Kliknij pozycję **Wybierz**.

2. W **Włącz dostęp** w obszarze **uprawnienia aplikacji**, wybierz **odczytu pełne profile wszystkich użytkowników**.

    ![Włącz dostęp](media/blockchain-workbench-deploy/client-app-read-perms.png)

    Kliknij przycisk **wybierz** kliknięcie **gotowe**.

3. W **wymagane uprawnienia**, wybierz pozycję **udzielanie uprawnień** następnie wybierz **tak** monitu weryfikacji.

   ![Udzielenie uprawnień](media/blockchain-workbench-deploy/client-app-grant-permissions.png)

   Udzielanie uprawnień umożliwia Blockchain Workbench do użytkowników w katalogu. Uprawnienia do odczytu jest wymagane do wyszukiwania i dodawać członków do Blockchain Workbench.

### <a name="add-graph-api-key-to-application"></a>Dodaj klucz interfejsu API programu Graph do aplikacji

Blockchain Workbench korzysta z usługi Azure AD jako tożsamość główna systemu zarządzania interakcji z aplikacjami blockchain użytkowników. Aby Blockchain Workbench dostępu do usługi Azure AD i pobierania informacji o użytkownikach, takich jak nazwy i wiadomości e-mail musisz dodać klucz dostępu. Blockchain Workbench korzysta z klucza uwierzytelniania za pomocą usługi Azure AD.

1. Dla aplikacji został zarejestrowany, wybierz **ustawienia** w okienku szczegółów zarejestrowanej aplikacji.
2. Wybierz pozycję **Klucze**.
3. Dodaj nowy klucz przez określenie klucza **opis** i wybierając polecenie **wygasa** wartości czasu trwania. 

    ![Utwórz klucz](media/blockchain-workbench-deploy/app-key-create.png)

    |Ustawienie  | Wartość  |
    |---------|---------|
    | Opis | `Service` |
    | Wygasa | Wybierz czas trwania wygasania |

4. Wybierz pozycję **Zapisz**. 
5. Skopiuj wartość klucza i zapisze go na później. Będzie potrzebny do wdrożenia.

    > [!IMPORTANT]
    >  Nie zapisuj klucza dla wdrożenia, należy wygenerować nowy klucz. Nie można pobrać wartości klucza później z portalu.

### <a name="get-application-id"></a>Uzyskiwanie Identyfikatora aplikacji

Informacje o identyfikatorze i dzierżawcy aplikacji są wymagane do wdrożenia. Zbieranie i przechowywanie informacji do użycia podczas wdrażania.

1. Dla aplikacji został zarejestrowany, wybierz **ustawienia** > **właściwości**.
2.  W **właściwości** okienka, kopiowania i magazynu następujące wartości do późniejszego użytku podczas wdrażania.

    ![Właściwości aplikacji interfejsu API](media/blockchain-workbench-deploy/app-properties.png)

    | Ustawienie do przechowywania  | Użyj we wdrożeniu |
    |------------------|-------------------|
    | Identyfikator aplikacji | Instalacja usługi Azure Active Directory > Identyfikator aplikacji |

### <a name="get-tenant-domain-name"></a>Uzyskiwanie dzierżawy nazwy domeny

Zbieranie i przechowywanie nazwę domeny dzierżawy usługi Active Directory, w których aplikacje są rejestrowane. 

W okienku nawigacji po lewej stronie wybierz **usługi Azure Active Directory** usługi. Wybierz **niestandardowych nazw domen**. Skopiuj i Zapisz nazwę domeny.

![Nazwa domeny](media/blockchain-workbench-deploy/domain-name.png)

## <a name="deploy-blockchain-workbench"></a>Wdrażanie Blockchain Workbench

Po ukończeniu wstępnie wymagane kroki można przystąpić do wdrażania Blockchain Workbench. W poniższych sekcjach opisano sposób wdrażania platformę.

1.  Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2.  Wybierz konto w górnym rogu, że rogu i przełącznika do żądanej usługi Azure AD dzierżawy, którym chcesz wdrożyć Azure Blockchain Workbench.
3.  W okienku po lewej stronie wybierz **Utwórz zasób**. Wyszukaj `Azure Blockchain Workbench` w **wyszukiwania portalu Marketplace** pasek wyszukiwania. 

    ![Pasek wyszukiwania portalu Marketplace](media/blockchain-workbench-deploy/marketplace-search-bar.png)

4.  Wybierz **Azure Blockchain Workbench**.

    ![Wyniki wyszukiwania portalu Marketplace](media/blockchain-workbench-deploy/marketplace-search-results.png)

4.  Wybierz pozycję **Utwórz**.
5.  Ukończ podstawowych ustawień.

    ![Utwórz Azure Blockchain Workbench](media/blockchain-workbench-deploy/blockchain-workbench-settings-basic.png)

    | Ustawienie | Opis  |
    |---------|--------------|
    | Prefiks zasobów | Krótki Unikatowy identyfikator dla danego wdrożenia. Ta wartość jest używana jako podstawa dla nazw zasobów. |
    | Nazwa użytkownika maszyny Wirtualnej | Nazwa użytkownika jest używana jako administrator dla wszystkich maszyn wirtualnych (VM). |
    | Typ uwierzytelniania | Wybierz, jeśli chcesz skorzystać z hasła lub klucza do nawiązywania połączenia z maszynami wirtualnymi. |
    | Hasło | Hasło służy do nawiązywania połączenia z maszynami wirtualnymi. |
    | Protokół SSH | Użyj klucza publicznego RSA w formacie jednowierszowego zaczyna się od **ssh-rsa** lub użyj formatu PEM wiele wierszy. Możesz wygenerować klucze SSH za pomocą `ssh-keygen` w systemie Linux i OS X lub przy użyciu PuTTYGen w systemie Windows. Więcej informacji na temat kluczy SSH, zobacz [kluczy sposobu korzystania z protokołu SSH z systemem Windows Azure](../virtual-machines/linux/ssh-from-windows.md). |
    | Hasła bazy danych / Potwierdź hasło bazy danych | Określ hasło używane do uzyskiwania dostępu do bazy danych utworzone jako część wdrożenia. |
    | Region wdrożenia | Określ, gdzie do wdrożenia środowiska roboczego Blockchain zasobów. Najlepsze dostępności powinna odpowiadać **lokalizacji** ustawienie. |
    | Subskrypcja | Określ subskrypcję Azure mają być używane dla danego wdrożenia. |
    | Grupy zasobów | Utwórz nową grupę zasobów, wybierając **Utwórz nowy** i określ nazwę grupy zasobów unikatowy. |
    | Lokalizacja | Określ region, który chcesz wdrożyć w ramach. |

6.  Wybierz **OK** na zakończenie sekcji podstawowe ustawienia konfiguracji.

7.  Zakończenie **Instalatora usługi Azure Active Directory**.

    ![Instalacja usługi Azure AD](media/blockchain-workbench-deploy/blockchain-workbench-settings-aad.png)

    | Ustawienie | Opis  |
    |---------|--------------|
    | Nazwa domeny | Zebrane dzierżawy użycia usługi Azure AD w [nazwę domeny dzierżawy Get](#get-tenant-domain-name) sekcji wymagań wstępnych. |
    | Identyfikator aplikacji | Użyj Identyfikatora aplikacji z rejestracji aplikacji klienta Blockchain zebrane w [uzyskiwanie Identyfikatora aplikacji](#get-application-id) sekcji wymagań wstępnych. |
    | Klucz aplikacji | Za pomocą klawisza aplikacji z zebranych podczas rejestracji aplikacji klienta Blockchain [klucz interfejsu API programu Graph dodać do aplikacji](#add-graph-api-key-to-application) sekcji wymagań wstępnych. |


8.  Kliknij przycisk **OK** na zakończenie sekcji konfiguracji parametrów usługi Azure AD.

9.  Zakończenie **sieci rozmiaru i wydajności** ustawienia.

    ![Ustawienia sieci i wydajności](media/blockchain-workbench-deploy/blockchain-workbench-settings-network.png)

    | Ustawienie | Opis  |
    |---------|--------------|
    | Liczba węzłów blockchain | Wybierz liczbę Ethereum PoA węzłów modułu sprawdzania poprawności do wdrożenia w sieci. |
    | Wydajność magazynu | Wybierz preferowany wydajność magazynu maszyny Wirtualnej w sieci blockchain. |
    | Rozmiar maszyny wirtualnej | Wybierz preferowany rozmiar maszyny Wirtualnej dla sieci blockchain. |

10. Wybierz **OK** na zakończenie sekcji rozmiaru i wydajności sieci.

11. Zakończenie **Azure Monitor** ustawienia.

    ![Azure Monitor](media/blockchain-workbench-deploy/blockchain-workbench-settings-oms.png)

    | Ustawienie | Opis  |
    |---------|--------------|
    | Monitorowanie | Wybierz, czy chcesz włączyć Azure Monitor do monitorowania sieci blockchain |
    | Nawiązać połączenia z istniejącym wystąpieniem analizy dzienników | Wybierz, czy chcesz użyć istniejącego analizy dzienników wystąpienie lub Utwórz nową. W przypadku używania istniejącego wystąpienia, należy wprowadzić identyfikator i klucz podstawowy. |

12. Kliknij przycisk **OK** na zakończenie sekcji Azure Monitor.

13. Przejrzyj podsumowanie, aby sprawdzić, czy parametry są poprawne.

    ![Podsumowanie](media/blockchain-workbench-deploy/blockchain-workbench-summary.png)

14. Wybierz **Utwórz** zgody na warunki i wdrożyć Blockchain Workbench Twojego Azure.

Wdrażanie może potrwać do 90 minut. Aby monitorować postęp, można użyć portalu Azure. W nowo utworzony zasób grupy, wybierz **wdrożeń > Przegląd** Sprawdź stan wdrożonych artefaktów.

## <a name="blockchain-workbench-web-url"></a>Adres URL sieci Web Blockchain Workbench

Po zakończeniu wdrożenia środowiska roboczego Blockchain nową grupę zasobów zawiera zasoby Blockchain Workbench. Blockchain Workbench usług są dostępne za pośrednictwem adresu URL sieci web. Poniższe kroki pokazują, jak pobrać adresu URL sieci web platformy wdrożone.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. W okienku nawigacji po lewej stronie wybierz **grup zasobów**
3. Wybierz nazwę grupy zasobów określone w przypadku wdrażania Blockchain Workbench.
4. Kliknij przycisk **typu** nagłówek kolumny, aby posortować listę alfabetycznie według typu.
5. Dostępne są dwa zasoby z typem **usługi aplikacji**. Wybierz zasób typu **usługi aplikacji** *bez* "-interfejsu api" sufiks.

    ![Lista usług aplikacji](media/blockchain-workbench-deploy/resource-group-list.png)

6.  W usłudze App Service **Essentials** sekcji, skopiuj **adres URL** wartość, która reprezentuje adres URL sieci web z wdrożonym Blockchain Workbench.

    ![Podstawowe informacje dotyczące usługi aplikacji](media/blockchain-workbench-deploy/app-service.png)

Aby skojarzyć niestandardowej nazwy domeny z Blockchain Workbench, zobacz [Konfigurowanie niestandardowej nazwy domeny dla aplikacji sieci web w usłudze Azure App Service przy użyciu Menedżera ruchu](../app-service/web-sites-traffic-manager-custom-domain-name.md).

## <a name="configuring-the-reply-url"></a>Konfigurowanie adresu URL odpowiedzi

Po wdrożeniu Azure Blockchain Workbench, następnym krokiem jest upewnij się, że aplikacja klienta usługi Azure Active Directory (Azure AD) jest zarejestrowana na prawidłowe **adres URL odpowiedzi** z wdrożonym Blockchain Workbench adres URL sieci web.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Sprawdź, czy są w dzierżawie rejestracji aplikacji klienta usługi Azure AD.
3. W okienku nawigacji po lewej stronie wybierz **usługi Azure Active Directory** usługi. Wybierz pozycję **Rejestracje aplikacji**.
4. Wybierz aplikację klienta usługi Azure AD, zarejestrowany w sekcji wymagań wstępnych.
5. Wybierz **Ustawienia > Odpowiedz adresy URL**.
6. Określ adres URL sieci web głównego deployment Azure Blockchain Workbench pobierana w **uzyskać adres URL sieci Web Azure Blockchain Workbench** sekcji. Adres URL odpowiedzi jest prefiksem `https://`. Na przykład: `https://myblockchain2-7v75.azurewebsites.net`

    ![Adresy URL odpowiedzi](media/blockchain-workbench-deploy/configure-reply-url.png)

7. Wybierz **zapisać** aktualizację rejestracji klienta.

## <a name="remove-a-deployment"></a>Usuń wdrożenie

Po wdrożeniu nie jest już potrzebne, można usunąć wdrożenia, usuwając Blockchain Workbench grupy zasobów.

1. W portalu Azure, przejdź do **grupy zasobów** w lewym okienku nawigacyjnym i wybierz grupę zasobów, które chcesz usunąć. 
2. Wybierz pozycję **Usuń grupę zasobów**. Sprawdź usunięcia, wprowadzając nazwę grupy zasobów, a następnie wybierz **usunąć**.

    ![Usuwanie grupy zasobów](media/blockchain-workbench-deploy/delete-resource-group.png)

## <a name="next-steps"></a>Kolejne kroki

W tym artykule należy wdrożyć Azure Blockchain Workbench. Aby dowiedzieć się, jak utworzyć aplikację blockchain, nadal dalej artykule.

> [!div class="nextstepaction"]
> [Tworzenie aplikacji blockchain w Azure Blockchain Workbench](blockchain-workbench-create-app.md)
