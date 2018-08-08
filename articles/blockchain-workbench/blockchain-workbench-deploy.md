---
title: Wdrażanie aplikacji Azure Blockchain Workbench
description: Sposób wdrażania aplikacji Azure Blockchain Workbench
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 7/13/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 1a0bc85063a80854ff6b970b0a57a991acfb3750
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/07/2018
ms.locfileid: "39593965"
---
# <a name="deploy-azure-blockchain-workbench"></a>Wdrażanie aplikacji Azure Blockchain Workbench

Azure Blockchain Workbench jest wdrażany za pomocą szablonu rozwiązania w witrynie Azure Marketplace. Szablon upraszcza wdrożenie składniki wymagane do tworzenia aplikacji łańcucha bloków. Po wdrożeniu aplikacji Blockchain Workbench zapewnia dostęp do aplikacji klienckich do tworzenia i zarządzania użytkownikami i aplikacji łańcucha bloków.

Aby uzyskać więcej informacji na temat składników aplikacji Blockchain Workbench, zobacz [architektury aplikacji Azure Blockchain Workbench](blockchain-workbench-architecture.md).

## <a name="prepare-for-deployment"></a>Przygotowanie do wdrożenia

Blockchain Workbench umożliwia wdrażanie księgi łańcucha bloków, wraz z zestawem odpowiednich usług platformy Azure, o których najczęściej używany do tworzenia aplikacji łańcucha bloków. Wdrażanie aplikacji Blockchain Workbench powoduje następujących usług platformy Azure aprowizowane w grupie zasobów w subskrypcji platformy Azure.

* Temat usługi 1 event Grid
* 1 usługi Service Bus Namespace
* 1 usługa application Insights
* 1 bazy danych SQL Database (Standard S0)
* 2 app Services (standardowa)
* 2 magazyny kluczy platformy azure
* 2 kont usługi azure Storage (Standard-LRS)
* 2 zestawów skalowania maszyn wirtualnych (w przypadku węzłów modułu sprawdzania poprawności i proces roboczy)
* 2 sieci wirtualnej (w tym moduł równoważenia obciążenia, sieciowej grupy zabezpieczeń i publiczny adres IP dla każdej sieci wirtualnej)
* Opcjonalnie: Usługa Azure Monitor

Poniżej przedstawiono przykład wdrożenia utworzone w **myblockchain** grupy zasobów.

![Przykład wdrożenia](media/blockchain-workbench-deploy/example-deployment.png)

Koszt Blockchain Workbench jest agregacją koszt podstawowych usług platformy Azure. Informacje o cenach dla usług platformy Azure mogą być obliczane przy użyciu [Kalkulator cen](https://azure.microsoft.com/pricing/calculator/).

Azure Blockchain Workbench wymaga kilka wymagań wstępnych przed wdrożeniem. Wymagania wstępne obejmują usługi Azure AD, konfiguracji i zastosowaniu rejestracji.

### <a name="blockchain-workbench-api-app-registration"></a>Rejestracja aplikacji Blockchain Workbench z interfejsu API

Wdrożenie aplikacji Blockchain Workbench wymaga rejestracji aplikacji usługi Azure AD. Należy dzierżawy usługi Azure Active Directory (Azure AD), aby zarejestrować aplikację. Można użyć istniejącej dzierżawy lub utworzyć nową dzierżawę. Jeśli używasz istniejącej dzierżawy usługi Azure AD, należy się wystarczające uprawnienia do rejestrowania aplikacji i udzielanie uprawnień interfejsu API programu Graph w dzierżawie usługi Azure AD. Jeśli nie masz wystarczających uprawnień w istniejącej dzierżawie usługi Azure AD należy utworzyć nową dzierżawę. 

> [!IMPORTANT]
> Środowisko robocze nie ma być wdrożony w tej samej dzierżawy, który jest używane do rejestrowania aplikacji usługi Azure AD. Workbench musi zostać wdrożony w dzierżawie, w którym masz wystarczające uprawnienia do wdrażania zasobów. Aby uzyskać więcej informacji na temat dzierżaw usługi Azure AD, zobacz [jak uzyskać dzierżawę usługi Active Directory](../active-directory/develop/quickstart-create-new-tenant.md) i [Integrowanie aplikacji z usługą Azure Active Directory](../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md).

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz swoje konto w prawym górnym rogu, a następnie przejdź do żądanej usługi Azure AD dzierżawy. Dzierżawa powinna być administrator subskrypcji dzierżawcy subskrypcji, w którym jest wdrażana aplikacja Workbench i masz wystarczające uprawnienia do rejestrowania aplikacji.
3. W okienku nawigacji po lewej stronie wybierz **usługi Azure Active Directory** usługi. Wybierz **rejestracje aplikacji** > **rejestrowanie nowej aplikacji**.

    ![Rejestracja aplikacji](media/blockchain-workbench-deploy/app-registration.png)

4. Podaj **nazwa** i **adres URL logowania** dla aplikacji. Można użyć wartości symboli zastępczych, ponieważ wartości są zmieniane podczas wdrażania. 

    ![Tworzenie rejestracji aplikacji](media/blockchain-workbench-deploy/app-registration-create.png)

    |Ustawienie  | Wartość  |
    |---------|---------|
    |Name (Nazwa) | `Blockchain API` |
    |Typ aplikacji |Interfejs API/aplikacja internetowa|
    |Adres URL logowania | `https://blockchainapi` |

5. Wybierz **Utwórz** do rejestrowania aplikacji usługi Azure AD.

### <a name="modify-application-manifest"></a>Modyfikowanie manifestu aplikacji

Następnie należy zmodyfikować manifest aplikacji do użycia ról aplikacji w usłudze Azure AD można określić administratorów aplikacji Blockchain Workbench.  Aby uzyskać więcej informacji na temat manifestów aplikacji, zobacz [manifest aplikacji w usłudze Azure Active Directory](../active-directory/develop/reference-app-manifest.md).

1. Aplikacja została zarejestrowana, można wybrać **manifestu** w okienku szczegółów zarejestrowanej aplikacji.
2. Wygeneruj identyfikator GUID. Można wygenerować identyfikatora GUID za pomocą polecenia programu PowerShell [identyfikator guid]:: NewGuid () lub identyfikator GUID nowego polecenia cmdlet. Innym rozwiązaniem jest użyć generator GUID witryny sieci Web.
3. Zamierzasz zaktualizować **appRoles** sekcji manifestu. W okienku manifestu edycji wybierz **Edytuj** i Zastąp `"appRoles": []` przy użyciu podanego formatu JSON. Pamiętaj zastąpić wartość **identyfikator** pola o identyfikatorze GUID został wygenerowany. 

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
    > Wartość **administratora** jest potrzebna do identyfikowania administratorów aplikacji Blockchain Workbench.

4.  Kliknij przycisk **Zapisz** można zapisać zmian manifestu aplikacji.

### <a name="add-graph-api-required-permissions"></a>Dodawanie interfejsu API programu Graph wymagane uprawnienia

Aplikacja interfejsu API musi żądać uprawnienia od użytkownika, aby uzyskać dostęp do katalogu. Ustaw następujące wymagane uprawnienia dla aplikacji interfejsu API:

1. Rejestracja aplikacji interfejsu API łańcucha bloków, wybierz **Ustawienia > wymagane uprawnienia > Wybierz interfejs API > Microsoft Graph**.

    ![Wybieranie interfejsu API](media/blockchain-workbench-deploy/client-app-select-api.png)

    Kliknij pozycję **Wybierz**.

2. W **Włącz dostęp za pomocą** w obszarze **uprawnienia aplikacji**, wybierz **przeczytaj pełne profile wszystkich użytkowników**.

    ![Włącz dostęp](media/blockchain-workbench-deploy/client-app-read-perms.png)

    Kliknij przycisk **wybierz** kliknięcie **gotowe**.

3. W **wymagane uprawnienia**, wybierz opcję **Udziel uprawnień** polecenie **tak** monitu dotyczącego weryfikacji.

   ![Udzielenie uprawnień](media/blockchain-workbench-deploy/client-app-grant-permissions.png)

   Nadawanie uprawnień umożliwia aplikacji Blockchain Workbench uzyskać dostęp użytkowników w katalogu. Uprawnienia do odczytu jest wymagany do wyszukiwania i dodawanie członków do aplikacji Blockchain Workbench.

### <a name="add-graph-api-key-to-application"></a>Dodaj klucz interfejsu API programu Graph do aplikacji

Blockchain Workbench używa usługi Azure AD jako systemu zarządzania tożsamościami głównego dla użytkowników, interakcji z aplikacji łańcucha bloków. Aby Blockchain Workbench dostęp do usługi Azure AD i pobierania informacji o użytkowniku, takie jak nazwy i wiadomości e-mail musisz dodać klucz dostępu. Blockchain Workbench używa klucza uwierzytelniania za pomocą usługi Azure AD.

1. Aplikacja została zarejestrowana, można wybrać **ustawienia** w okienku szczegółów zarejestrowanej aplikacji.
2. Wybierz pozycję **Klucze**.
3. Dodaj nowy klucz, określając klucz **opis** i wybierając pozycję **wygasa** wartości czasu trwania. 

    ![Utwórz klucz](media/blockchain-workbench-deploy/app-key-create.png)

    |Ustawienie  | Wartość  |
    |---------|---------|
    | Opis | `Service` |
    | Wygasa | Wybierz czas wygaśnięcia |

4. Wybierz pozycję **Zapisz**. 
5. Skopiuj wartość klucza i zapisz go na później. Niezbędnych do wdrożenia.

    > [!IMPORTANT]
    >  Jeśli nie zostanie zapisany klucz dla wdrożenia, należy wygenerować nowy klucz. Nie można pobrać wartości klucza później z poziomu portalu.

### <a name="get-application-id"></a>Uzyskiwanie Identyfikatora aplikacji

Informacje o identyfikatorze i dzierżawy aplikacji są wymagane do wdrożenia. Zbieranie i przechowywanie informacji do użycia podczas wdrażania.

1. Aplikacja została zarejestrowana, można wybrać **ustawienia** > **właściwości**.
2.  W **właściwości** okienko, kopiowanie i magazynu następujące wartości do późniejszego użytku podczas wdrażania.

    ![Właściwości aplikacji interfejsu API](media/blockchain-workbench-deploy/app-properties.png)

    | Ustawienia dotyczącego przechowywania  | Użyj we wdrożeniu |
    |------------------|-------------------|
    | Identyfikator aplikacji | Konfiguracja usługi Azure Active Directory > Identyfikator aplikacji |

### <a name="get-tenant-domain-name"></a>Uzyskiwanie dzierżawy nazwy domeny

Zbieranie i przechowywanie nazwa domeny dzierżawy usługi Active Directory, w których aplikacje są rejestrowane. 

W okienku nawigacji po lewej stronie wybierz **usługi Azure Active Directory** usługi. Wybierz pozycję **Niestandardowe nazwy domen**. Skopiuj i Zapisz nazwę domeny.

![Nazwa domeny](media/blockchain-workbench-deploy/domain-name.png)

## <a name="deploy-blockchain-workbench"></a>Wdrażanie aplikacji Blockchain Workbench

Po ukończeniu kroków wymagań wstępnych można przystąpić do wdrażania aplikacji Blockchain Workbench. W poniższych sekcjach opisano, jak wdrożyć platformę.

1.  Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2.  Wybierz swoje konto w prawym górnym prawym rogu i przejdź do żądanej usługi Azure AD dzierżawy, którym chcesz wdrożyć aplikacji Azure Blockchain Workbench.
3.  W okienku po lewej stronie wybierz **Utwórz zasób**. Wyszukaj `Azure Blockchain Workbench` w **Przeszukaj witrynę Marketplace** pasku wyszukiwania. 

    ![Pasek wyszukiwania w witrynie Marketplace](media/blockchain-workbench-deploy/marketplace-search-bar.png)

4.  Wybierz **aplikacji Azure Blockchain Workbench**.

    ![Wyniki wyszukiwania w witrynie Marketplace](media/blockchain-workbench-deploy/marketplace-search-results.png)

4.  Wybierz pozycję **Utwórz**.
5.  Wykonaj podstawowe ustawienia.

    ![Tworzenie aplikacji Azure Blockchain Workbench](media/blockchain-workbench-deploy/blockchain-workbench-settings-basic.png)

    | Ustawienie | Opis  |
    |---------|--------------|
    | Prefiks zasobów | Krótkie Unikatowy identyfikator dla danego wdrożenia. Ta wartość jest używana jako podstawa do nadawania nazw zasobów. |
    | Nazwa użytkownika maszyny Wirtualnej | Nazwa użytkownika jest używana jako administrator dla wszystkich maszyn wirtualnych (VM). |
    | Typ uwierzytelniania | Wybierz, jeśli chcesz używać hasła lub klucza do łączenia się z maszynami wirtualnymi. |
    | Hasło | Hasło jest używane do łączenia się z maszynami wirtualnymi. |
    | Protokół SSH | Użyj klucz publiczny RSA w formacie jednowierszowym począwszy **ssh-rsa** lub wielowierszowym formacie PEM. Możesz wygenerować kluczy SSH przy użyciu `ssh-keygen` w systemie Linux i OS X lub przy użyciu narzędzia PuTTYGen Windows. Więcej informacji na temat kluczy SSH, zobacz [jak używać protokołu SSH kluczy przy użyciu Windows Azure](../virtual-machines/linux/ssh-from-windows.md). |
    | Hasła bazy danych / Potwierdź hasło bazy danych | Określ hasło używane do uzyskiwania dostępu do bazy danych utworzone w ramach wdrożenia. |
    | Regionu geograficznego | Określ, gdzie wdrażanie zasobów aplikacji Blockchain Workbench. Aby uzyskać najlepsze dostępność, powinien on odpowiadać **lokalizacji** ustawienie. |
    | Subskrypcja | Określ subskrypcję platformy Azure, chcesz używać dla danego wdrożenia. |
    | Grupy zasobów | Utwórz nową grupę zasobów, wybierając **Utwórz nową** i określ unikatową nazwą grupy zasobów. |
    | Lokalizacja | Określ region, który chcesz wdrożyć platformę. |

6.  Wybierz **OK** Zakończenie sekcji konfiguracji ustawienia podstawowe.

7.  Wykonaj **instalacji usługi Azure Active Directory**.

    ![Konfiguracja usługi Azure AD](media/blockchain-workbench-deploy/blockchain-workbench-settings-aad.png)

    | Ustawienie | Opis  |
    |---------|--------------|
    | Nazwa domeny, | Użyj usługi Azure AD dzierżawy zebranych w [nazwa_domeny_dzierżawy Get](#get-tenant-domain-name) sekcji wymagań wstępnych. |
    | Identyfikator aplikacji | Użyj Identyfikatora aplikacji z rejestracji aplikacji łańcucha bloków klienta zebranych w [uzyskiwanie Identyfikatora aplikacji](#get-application-id) sekcji wymagań wstępnych. |
    | Klucz aplikacji | Użyj klucza aplikacji z rejestracji aplikacji łańcucha bloków klienta, zbierane w [klucz interfejsu API programu Graph dodać do aplikacji](#add-graph-api-key-to-application) sekcji wymagań wstępnych. |


8.  Kliknij przycisk **OK** Zakończenie sekcji konfiguracji parametrów usługi Azure AD.

9.  W **ustawienia sieci i wydajności**, wybierz, jeśli chcesz utworzyć nową sieć łańcucha bloków lub użyć istniejącej sieci dowód uwierzytelniania łańcucha bloków.

    Aby uzyskać **Utwórz nową**:

    *Tworzenia nowych* opcja tworzy zestaw węzłów dowód z Ethereum urzędu (PoA) w ramach subskrypcji jeden element członkowski. 

    ![Ustawienia sieci i wydajności](media/blockchain-workbench-deploy/blockchain-workbench-settings-network-new.png)

    | Ustawienie | Opis  |
    |---------|--------------|
    | Liczba węzłów łańcucha bloków | Wybierz liczbę Ethereum PoA węzłów modułu sprawdzania poprawności, można wdrożyć w sieci. |
    | Wydajność magazynu | Wybierz preferowaną wydajność magazynu maszyny Wirtualnej dla sieci łańcucha bloków. |
    | Rozmiar maszyny wirtualnej | Wybierz preferowany rozmiar maszyny Wirtualnej dla sieci łańcucha bloków. |

    Aby uzyskać **Użyj istniejącej**:

    *Użyj istniejącej* opcji pozwala określić sieć łańcucha bloków Ethereum dowód uwierzytelniania (PoA). Punkty końcowe mają następujące wymagania.

    * Punkt końcowy musi być sieć łańcucha bloków Ethereum dowód uwierzytelniania (PoA).
    * Punkt końcowy musi być publicznie dostępny za pośrednictwem sieci.
    * Należy skonfigurować sieć łańcucha bloków PoA cenie gaz, należy ustawić na zero (Uwaga: nie finansowania kont Blockchain Workbench. Jeśli wymaganych środków transakcji nie).

    ![Ustawienia sieci i wydajności](media/blockchain-workbench-deploy/blockchain-workbench-settings-network-existing.png)

    | Ustawienie | Opis  |
    |---------|--------------|
    | Punkt końcowy Ethereum RPC | Podaj końcowych wywołań RPC w istniejącej sieci PoA łańcucha bloków. Punkt końcowy rozpoczyna się od http:// i kończy się za pomocą numeru portu. Na przykład: `http://contoso-chain.onmicrosoft.com:8545` |
    | Wydajność magazynu | Wybierz preferowaną wydajność magazynu maszyny Wirtualnej dla sieci łańcucha bloków. |
    | Rozmiar maszyny wirtualnej | Wybierz preferowany rozmiar maszyny Wirtualnej dla sieci łańcucha bloków. |

10. Wybierz **OK** zakończenie ustawienia sieci i wydajności.

11. Wykonaj **usługi Azure Monitor** ustawienia.

    ![Azure Monitor](media/blockchain-workbench-deploy/blockchain-workbench-settings-oms.png)

    | Ustawienie | Opis  |
    |---------|--------------|
    | Monitorowanie | Wybierz, czy chcesz włączyć usługi Azure Monitor do monitorowania sieci łańcucha bloków |
    | Nawiązać połączenie z istniejącym wystąpieniem usługi Log Analytics | Wybierz, czy chcesz użyć istniejącej usługi Log Analytics wystąpienie lub Utwórz nową. W przypadku używania istniejącego wystąpienia, wprowadź identyfikator obszaru roboczego i klucz podstawowy. |

12. Kliknij przycisk **OK** Zakończenie sekcji usługi Azure Monitor.

13. Przejrzyj podsumowanie, aby sprawdzić, czy parametry są poprawne.

    ![Podsumowanie](media/blockchain-workbench-deploy/blockchain-workbench-summary.png)

14. Wybierz **Utwórz** zgodę na warunki i wdrażania aplikacji Azure Blockchain Workbench.

Wdrożenie może potrwać do 90 minut. Za pomocą witryny Azure portal do monitorowania postępu. W nowo utworzonej grupy zasobów, wybierz **wdrożeń > Przegląd** Aby wyświetlić stan wdrożonych artefaktów.

## <a name="blockchain-workbench-web-url"></a>Adres URL sieci Web z aplikacji Blockchain Workbench

Po zakończeniu wdrażania aplikacji Blockchain Workbench Nowa grupa zasobów zawiera zasoby aplikacji Blockchain Workbench. Usługi aplikacji Blockchain Workbench są dostępne za pośrednictwem adresu URL sieci web. Następujące kroki pokazują jak pobrać adres URL sieci web wdrożonej framework.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. W okienku nawigacji po lewej stronie wybierz **grup zasobów**
3. Wybierz nazwę grupy zasobów, podanej podczas wdrażania aplikacji Blockchain Workbench.
4. Kliknij przycisk **typu** nagłówek kolumny, aby sortować listę alfabetycznie według typu.
5. Istnieją dwa zasoby o typie **usługi App Service**. Wybierz zasób typu **usługi App Service** *bez* "— interfejs api" sufiks.

    ![Lista usług aplikacji](media/blockchain-workbench-deploy/resource-group-list.png)

6.  W usłudze App Service **Essentials** sekcji, skopiuj **adresu URL** wartość, która reprezentuje adres URL sieci web do Twojej wdrożonej aplikacji Blockchain Workbench.

    ![Podstawy usługi aplikacji](media/blockchain-workbench-deploy/app-service.png)

Aby skojarzyć niestandardowej nazwy domeny z aplikacji Blockchain Workbench, zobacz [Konfigurowanie niestandardowej nazwy domeny dla aplikacji sieci web w usłudze Azure App Service przy użyciu usługi Traffic Manager](../app-service/web-sites-traffic-manager-custom-domain-name.md).

## <a name="configuring-the-reply-url"></a>Konfigurowanie adresu URL odpowiedzi

Po wdrożeniu aplikacji Azure Blockchain Workbench następnym krokiem jest upewnienie się, aplikacja kliencka usługi Azure Active Directory (Azure AD) jest zarejestrowana w celu prawidłowego **adres URL odpowiedzi** dla wdrożonej aplikacji Blockchain Workbench adres URL sieci web.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Sprawdź, czy jesteś w dzierżawie, w którym zarejestrowana aplikacja kliencka usługi Azure AD.
3. W okienku nawigacji po lewej stronie wybierz **usługi Azure Active Directory** usługi. Wybierz pozycję **Rejestracje aplikacji**.
4. Wybierz aplikację klienta usługi Azure AD zarejestrowanej w sekcji wymagań wstępnych.
5. Wybierz **Ustawienia > adresy URL odpowiedzi**.
6. Podaj adres URL głównego sieci web, wdrażania aplikacji Azure Blockchain Workbench możesz pobrać w **uzyskać adres URL sieci Web Azure Blockchain Workbench** sekcji. Adres URL odpowiedzi jest poprzedzony znakiem `https://`. Na przykład: `https://myblockchain2-7v75.azurewebsites.net`

    ![Adresy URL odpowiedzi](media/blockchain-workbench-deploy/configure-reply-url.png)

7. Wybierz **Zapisz** można zaktualizować rejestracji klienta.

## <a name="remove-a-deployment"></a>Usuwanie wdrożenia

Po wdrożeniu nie jest już potrzebny, można usunąć wdrożenia, usuwając grupę zasobów aplikacji Blockchain Workbench.

1. W witrynie Azure portal przejdź do **grupy zasobów** w okienku nawigacji po lewej stronie i wybierz grupę zasobów, którą chcesz usunąć. 
2. Wybierz pozycję **Usuń grupę zasobów**. Sprawdź usunięcie, wpisując nazwę grupy zasobów, a następnie wybierz pozycję **Usuń**.

    ![Usuwanie grupy zasobów](media/blockchain-workbench-deploy/delete-resource-group.png)

## <a name="next-steps"></a>Kolejne kroki

W tym artykule Ci się wdrożyć aplikacji Azure Blockchain Workbench. Informacje na temat tworzenia aplikacji łańcucha bloków, przejdź do następnego artykułu porad.

> [!div class="nextstepaction"]
> [Tworzenie aplikacji łańcucha bloków w aplikacji Azure Blockchain Workbench](blockchain-workbench-create-app.md)
