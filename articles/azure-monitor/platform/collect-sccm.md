---
title: Łączenie programu Menedżer konfiguracji z monitorem Platformy Azure | Dokumenty firmy Microsoft
description: W tym artykule przedstawiono kroki, aby połączyć menedżera konfiguracji z obszarem roboczym w usłudze Azure Monitor i rozpocząć analizowanie danych.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/28/2019
ms.openlocfilehash: 3140c0de6fbe090e3d040202cd581c455f03b6d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77655260"
---
# <a name="connect-configuration-manager-to-azure-monitor"></a>Łączenie programu Menedżer konfiguracji z monitorem platformy Azure
Środowisko programu Microsoft Endpoint Configuration Manager można połączyć z usługą Azure Monitor, aby zsynchronizować dane zbierania urządzeń i odwoływać się do tych kolekcji w usłudze Azure Monitor i usługa Azure Automation.  

## <a name="prerequisites"></a>Wymagania wstępne

Usługa Azure Monitor obsługuje bieżącą gałąź programu Configuration Manager w wersji 1606 lub nowszej.

>[!NOTE]
>Funkcja łączenia programu Configuration Manager z obszarem roboczym usługi Log Analytics jest opcjonalna i domyślnie nie jest włączona. Przed użyciem tej funkcji należy włączyć tę funkcję. Aby uzyskać więcej informacji, zobacz [Enable optional features from updates](https://docs.microsoft.com/configmgr/core/servers/manage/install-in-console-updates#bkmk_options).

## <a name="configuration-overview"></a>Przegląd konfiguracji

Poniższe kroki podsumowują kroki konfigurowania integracji programu Configuration Manager z usługą Azure Monitor.  

1. W usłudze Azure Active Directory zarejestruj program Configuration Manager jako aplikację sieci Web i/lub aplikację interfejsu API sieci Web i upewnij się, że masz identyfikator klienta i klucz tajny klienta z rejestracji z usługi Azure Active Directory. Zobacz [Tworzenie aplikacji i jednostki usługi Active Directory, która może uzyskać dostęp do zasobów, zobacz Tworzenie aplikacji i jednostki usługi Active Directory, które mogą uzyskiwać dostęp](../../active-directory/develop/howto-create-service-principal-portal.md) do zasobów, aby uzyskać szczegółowe informacje dotyczące wykonywania tego kroku.

2. W usłudze Azure Active Directory [należy udzielić programu Configuration Manager (zarejestrowanej aplikacji sieci web) z uprawnieniami dostępu do usługi Azure Monitor](#grant-configuration-manager-with-permissions-to-log-analytics).

3. W programie Menedżer konfiguracji dodaj połączenie za pomocą kreatora **usług platformy Azure.**

4. [Pobierz i zainstaluj agenta analizy dzienników dla systemu Windows](#download-and-install-the-agent) na komputerze z rolą systemu lokacji punktu połączenia usługi menedżer konfiguracji. Agent wysyła dane programu Configuration Manager do obszaru roboczego usługi Log Analytics w usłudze Azure Monitor.

5. W usłudze Azure Monitor [zaimportuj kolekcje z programu Configuration Manager](#import-collections) jako grupy komputerów.

6. W usłudze Azure Monitor wyświetlaj dane z programu Menedżer konfiguracji jako [grupy komputerów](computer-groups.md).

## <a name="grant-configuration-manager-with-permissions-to-log-analytics"></a>Udziel menedżera konfiguracji z uprawnieniami do usługi Log Analytics

W poniższej procedurze należy przyznać rolę *współautora* w obszarze roboczym usługi Log Analytics do aplikacji usługi AD i jednostki usługi utworzonej wcześniej dla programu Configuration Manager. Jeśli nie masz jeszcze obszaru roboczego, zobacz [Tworzenie obszaru roboczego w usłudze Azure Monitor](../../azure-monitor/learn/quick-create-workspace.md) przed kontynuowaniem. Dzięki temu program Menedżer konfiguracji może uwierzytelniać się i łączyć z obszarem roboczym usługi Log Analytics.  

> [!NOTE]
> Należy określić uprawnienia w obszarze roboczym usługi Log Analytics dla programu Configuration Manager. W przeciwnym razie podczas korzystania z kreatora konfiguracji w programie Configuration Manager zostanie wyświetlony komunikat o błędzie.
>

1. W witrynie Azure Portal kliknij pozycję **Wszystkie usługi** w lewym górnym rogu. Na liście zasobów wpisz **Log Analytics**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Wybierz pozycję **Log Analytics**.

2. Na liście obszarów roboczych usługi Log Analytics wybierz obszar roboczy do zmodyfikowania.

3. W lewym okienku wybierz pozycję **Kontrola dostępu (IAM)**.

4. Na stronie Kontrola dostępu (IAM) kliknij pozycję **Dodaj przypisanie roli** i zostanie wyświetlona okienko **Dodaj przypisanie roli.**

5. W okienku **Dodawanie przypisania roli** w obszarze listy rozwijanej **Rola** wybierz rolę **współautora.**  

6. W obszarze listy rozwijanej **Przypisywanie dostępu do** wybierz aplikację Programu Menedżer konfiguracji utworzoną wcześniej w usłudze AD, a następnie kliknij przycisk **OK**.  

## <a name="download-and-install-the-agent"></a>Pobierz i zainstaluj agenta

Zapoznaj się z artykułem [Łączenie komputerów z systemem Windows z monitorem platformy Azure na platformie Azure,](agent-windows.md) aby zapoznać się z metodami dostępnymi do instalowania agenta analizy dzienników dla systemu Windows na komputerze obsługującym rolę systemu lokacji punktu połączenia usługi programu Configuration Manager.  

## <a name="connect-configuration-manager-to-log-analytics-workspace"></a>Łączenie programu Menedżer konfiguracji z obszarem roboczym usługi Log Analytics

>[!NOTE]
> Aby dodać połączenie usługi Log Analytics, środowisko programu Menedżer konfiguracji musi mieć [skonfigurowany punkt połączenia usługi](https://docs.microsoft.com/configmgr/core/servers/deploy/configure/about-the-service-connection-point) dla trybu online.

> [!NOTE]
> Musisz połączyć lokację najwyższej warstwy w hierarchii z usługą Azure Monitor. Jeśli połączysz autonomiczną lokację główną z usługą Azure Monitor, a następnie dodasz do środowiska centralną lokację administracyjną, musisz usunąć i ponownie utworzyć połączenie w nowej hierarchii.

1. W obszarze roboczym **Administracja** programu Configuration Manager wybierz pozycję **Usługi w usługach w usługach w usługach chmurowych,** a następnie wybierz pozycję **Usługi platformy Azure**. 

2. Kliknij prawym przyciskiem myszy pozycję **Usługi platformy Azure,** a następnie wybierz pozycję **Konfiguruj usługi platformy Azure**. Zostanie wyświetlona strona **Konfigurowanie usług Platformy Azure.** 
   
3. Na ekranie **Ogólne** upewnij się, że wykonaliśmy następujące czynności i że masz szczegółowe informacje dotyczące każdego elementu, a następnie wybierz przycisk **Dalej**.

4. Na stronie Usługi platformy Azure Kreatora usług platformy Azure:

    1. Określ **nazwę** obiektu w programie Menedżer konfiguracji.
    2. Określ opcjonalny **opis,** który ułatwia identyfikację usługi.
    3. Wybierz łącznik **usługi Azure OMS**.

    >[!NOTE]
    >Usługa OMS jest teraz określana jako usługa Log Analytics, która jest funkcją usługi Azure Monitor.

5. Wybierz **przycisk Dalej,** aby przejść do strony właściwości aplikacji platformy Azure Kreatora usług platformy Azure.

6. Na stronie **Aplikacji** Kreatora usług platformy Azure najpierw wybierz środowisko platformy Azure z listy, a następnie kliknij przycisk **Importuj**.

7. Na stronie **Importowanie aplikacji** określ następujące informacje:

    1. Określ **nazwę dzierżawy usługi Azure AD** dla aplikacji.

    2. Określ dla **usługi Azure AD Tenant ID** dzierżawy usługi Azure AD. Informacje te można znaleźć na stronie **Właściwości** usługi Azure Active Directory. 

    3. Określ dla **nazwy aplikacji** nazwę aplikacji.

    4. Określ dla **identyfikatora klienta**, identyfikator aplikacji utworzonej wcześniej aplikacji usługi Azure AD.

    5. Określ klucz **tajny**— klucz tajny klienta utworzonej aplikacji usługi Azure AD.

    6. Określ **wygaśnięcie klucza tajnego**, datę wygaśnięcia klucza.

    7. Określ identyfikator **URI identyfikatora aplikacji**, identyfikator URI identyfikatora aplikacji utworzonej wcześniej.

    8. Wybierz **zweryfikuj** i po prawej stronie wyniki powinny być wyświetlane **Pomyślnie zweryfikowane!**.

8. Na stronie **Konfiguracja** przejrzyj informacje, aby zweryfikować **subskrypcje platformy Azure,** **grupę zasobów platformy Azure**i pola **obszaru roboczego pakietu Operations Management Suite** są wstępnie wypełnione, wskazując, że aplikacja usługi Azure AD ma wystarczające uprawnienia w grupie zasobów. Jeśli pola są puste, oznacza to, że aplikacja nie ma wymaganych praw. Wybierz kolekcje urządzeń do zbierania i przesyłania dalej do obszaru roboczego, a następnie wybierz pozycję **Dodaj**.

9. Przejrzyj opcje na stronie **Potwierdź ustawienia** i wybierz przycisk **Dalej,** aby rozpocząć tworzenie i konfigurowanie połączenia.

10. Po zakończeniu konfiguracji zostanie wyświetlona strona **Zakończenie.** Wybierz polecenie **Zamknij**. 

Po połączeniu programu Configuration Manager z usługą Azure Monitor można dodawać lub usuwać kolekcje oraz wyświetlać właściwości połączenia.

## <a name="update-log-analytics-workspace-connection-properties"></a>Aktualizowanie właściwości połączenia obszaru roboczego usługi Log Analytics

Jeśli hasło lub klucz tajny klienta wygaśnie lub zostanie utracony, musisz ręcznie zaktualizować właściwości połączenia usługi Log Analytics.

1. W obszarze roboczym **Administracja** programu Configuration Manager wybierz pozycję **Usługi w chmurze,** a następnie wybierz pozycję **Łącznik systemu OMS,** aby otworzyć stronę **Właściwości połączenia OMS.**
2. Na tej stronie kliknij kartę **Usługi Azure Active Directory,** aby wyświetlić **identyfikator dzierżawy**, **identyfikator klienta**, **wygaśnięcie klucza tajnego klienta**. **Sprawdź** **klucz tajny klienta,** jeśli wygasł.

## <a name="import-collections"></a>Importowanie kolekcji

Po dodaniu połączenia usługi Log Analytics do programu Configuration Manager i zainstalowaniu agenta na komputerze z uruchomioną rolą systemu lokacji punktu połączenia usługi programu Configuration Manager następnym krokiem jest importowanie kolekcji z programu Configuration Manager na platformie Azure Monitoruj jako grupy komputerów.

Po zakończeniu początkowej konfiguracji do importowania kolekcji urządzeń z hierarchii informacje o kolekcji są pobierane co 3 godziny, aby zachować aktualność członkostwa. Możesz to wyłączyć w dowolnym momencie.

1. W witrynie Azure Portal kliknij pozycję **Wszystkie usługi** w lewym górnym rogu. Na liście zasobów wpisz **Log Analytics**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Wybierz **obszary robocze usługi Log Analytics**.
2. Na liście obszarów roboczych usługi Log Analytics wybierz program Menedżer konfiguracji obszaru roboczego.  
3. Wybierz pozycję **Ustawienia zaawansowane**.
4. Wybierz **pozycję Grupy komputerów,** a następnie wybierz pozycję **SCCM**.  
5. Wybierz **pozycję Importuj członkostwo w kolekcji programu Menedżer konfiguracji,** a następnie kliknij przycisk **Zapisz**.  
   
    ![Grupy komputerów — karta SCCM](./media/collect-sccm/sccm-computer-groups01.png)

## <a name="view-data-from-configuration-manager"></a>Wyświetlanie danych z programu Configuration Manager

Po dodaniu połączenia usługi Log Analytics do programu Configuration Manager i zainstalowaniu agenta na komputerze z uruchomioną rolą systemu lokacji punktu połączenia usługi programu Configuration Manager dane z agenta są wysyłane do obszaru roboczego usługi Log Analytics w usłudze Azure Monitor. W usłudze Azure Monitor kolekcje programu Menedżer konfiguracji są wyświetlane jako [grupy komputerów](../../azure-monitor/platform/computer-groups.md). Grupy można wyświetlić ze strony **Menedżera konfiguracji** w obszarze **Ustawienia\Grupy komputerów**.

Po zaimportowaniu kolekcji można zobaczyć, ile komputerów z członkostwem w kolekcji zostało wykrytych. Można również zobaczyć liczbę kolekcji, które zostały zaimportowane.

![Grupy komputerów — karta SCCM](./media/collect-sccm/sccm-computer-groups02.png)

Po kliknięciu jednego z nich otworzy się edytor zapytań dziennika, w którym wyświetlane są wszystkie zaimportowane grupy lub wszystkie komputery należące do każdej grupy. Za pomocą [wyszukiwania dziennika,](../../azure-monitor/log-query/log-query-overview.md)można wykonać dalsze szczegółowe analizy danych członkostwa kolekcji.

## <a name="next-steps"></a>Następne kroki

Użyj [wyszukiwania dziennika,](../../azure-monitor/log-query/log-query-overview.md) aby wyświetlić szczegółowe informacje o danych programu Menedżer konfiguracji.
