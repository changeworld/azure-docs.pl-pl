---
title: Łączenie programu Configuration Manager do usługi Azure Monitor | Dokumentacja firmy Microsoft
description: W tym artykule przedstawiono kroki do łączenia programu Configuration Manager do obszaru roboczego w usłudze Azure Monitor i zacząć analizować dane.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: f2298bd7-18d7-4371-b24a-7f9f15f06d66
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/22/2018
ms.author: magoedte
ms.openlocfilehash: 26ddb0cdd2728f9dff5d45494a14841cdc1a20cd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64922881"
---
# <a name="connect-configuration-manager-to-azure-monitor"></a>Łączenie programu Configuration Manager do usługi Azure Monitor
Można połączyć środowiska programu System Center Configuration Manager do usługi Azure Monitor synchronizować dane kolekcji urządzeń i odwołują się do tych kolekcji w usłudze Azure Monitor i Azure Automation.  

## <a name="prerequisites"></a>Wymagania wstępne

Usługa Azure Monitor obsługuje System Center Configuration Manager bieżącej gałęzi w wersji 1606 i wyższej.  

## <a name="configuration-overview"></a>Omówienie konfiguracji
Poniższe instrukcje stanowią podsumowanie czynności, aby skonfigurować integrację programu Configuration Manager z usługą Azure Monitor.  

1. W witrynie Azure portal Zarejestruj programu Configuration Manager jako aplikację sieci Web, aplikacji i/lub interfejs API sieci Web i upewnij się, że identyfikator klienta i klucz tajny klienta z rejestracji z usługi Azure Active Directory. Zobacz [w obsłudze portalu do tworzenia aplikacji i usługi jednostki, które mogą uzyskiwać dostęp do zasobów usługi Active Directory](../../active-directory/develop/howto-create-service-principal-portal.md) Aby uzyskać szczegółowe informacje na temat wykonywania tego kroku.
2. W witrynie Azure portal [udzielić programu Configuration Manager (rejestrowanej aplikacji internetowej) z uprawnieniami do usługi Azure Monitor](#grant-configuration-manager-with-permissions-to-log-analytics).
3. W programie Configuration Manager Dodaj połączenie za pomocą Kreatora dodawania połączenia pakietu OMS.
4. W programie Configuration Manager zaktualizować właściwości połączenia, jeśli klucz tajny klienta lub hasło nigdy nie wygaśnie lub zostanie utracony.
5. [Pobierz i zainstaluj program Microsoft Monitoring Agent](#download-and-install-the-agent) na komputerze z systemem rolę systemu lokacji punktu połączenia programu Configuration Manager service. Agent wysyła dane programu Configuration Manager do obszaru roboczego usługi Log Analytics w usłudze Azure Monitor.
6. W usłudze Azure Monitor [Importuj kolekcje z programu Configuration Manager](#import-collections) jako grup komputerów.
7. W usłudze Azure Monitor wyświetlania danych z programu Configuration Manager jako [grup komputerów](computer-groups.md).

Możesz dowiedzieć się więcej o łączeniu programu Configuration Manager do usługi Azure Monitor na [synchronizowanie danych z programu Configuration Manager do obszaru roboczego usługi Log Analytics dla programu Microsoft](https://technet.microsoft.com/library/mt757374.aspx).

## <a name="grant-configuration-manager-with-permissions-to-log-analytics"></a>Udziel programu Configuration Manager z uprawnieniami do usługi Log Analytics
W poniższej procedurze, można przyznać *Współautor* roli w obszarze roboczym usługi Log Analytics do aplikacji usługi AD i jednostki usługi utworzonej wcześniej w programie Configuration Manager.  Jeśli nie masz obszaru roboczego, zobacz [Utwórz obszar roboczy w usłudze Azure Monitor](../../azure-monitor/learn/quick-create-workspace.md) przed kontynuowaniem.  Dzięki temu programu Configuration Manager do uwierzytelnienia i nawiązania połączenia z obszarem roboczym usługi Log Analytics.  

> [!NOTE]
> Należy określić uprawnienia w obszarze roboczym usługi Log Analytics dla programu Configuration Manager. W przeciwnym razie otrzymasz komunikat o błędzie podczas korzystania z Kreatora konfiguracji w programie Configuration Manager.
>

1. W witrynie Azure Portal kliknij pozycję **Wszystkie usługi** w lewym górnym rogu. Na liście zasobów wpisz **Log Analytics**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Wybierz pozycję **Log Analytics**.
2. Na liście obszarów roboczych usługi Log Analytics wybierz obszar roboczy, aby zmodyfikować.
3. W okienku po lewej stronie wybierz **kontrola dostępu (IAM)** .
4. Strony kontrola dostępu (IAM), kliknij **Dodaj przypisanie roli** i **Dodaj przypisanie roli** zostanie wyświetlone okienko.
5. W **Dodaj przypisanie roli** okienku w obszarze **roli** listy rozwijanej wybierz **Współautor** roli.  
6. W obszarze **Przypisz dostęp do** listy rozwijanej wybierz aplikacji programu Configuration Manager wcześniej utworzone w AD, a następnie kliknij przycisk **OK**.  

## <a name="download-and-install-the-agent"></a>Pobierz i zainstaluj agenta
Zapoznaj się z artykułem [Windows łączenie komputerów do usługi Azure Monitor na platformie Azure](agent-windows.md) Aby poznać dostępne metody instalacji programu Microsoft Monitoring Agent na komputerze hostującym punkt połączenia z usługą Configuration Manager Rola systemu lokacji.  

## <a name="add-a-log-analytics-connection-to-configuration-manager"></a>Dodawanie połączenia usługi Log Analytics do programu Configuration Manager
Aby dodać połączenie usługi Log Analytics, środowiska programu Configuration Manager musi mieć [punkt połączenia z usługą](https://technet.microsoft.com/library/mt627781.aspx) skonfigurowane dla trybu online.

1. W **administracji** programu Configuration Manager, wybierz obszar roboczy **łącznika pakietu OMS**. Spowoduje to otwarcie **Dodaj Kreatora połączenia analizy dziennika**. Wybierz opcję **Dalej**.

   >[!NOTE]
   >Pakiet OMS jest teraz nazywana usługi Log Analytics, która jest funkcją usługi Azure Monitor.
   
2. Na **ogólne** ekranu, upewnij się, że zostały wykonane następujące akcje i że możesz mieć szczegółów dla każdego elementu, a następnie wybierz **dalej**.

   1. W witrynie Azure portal zarejestrowanego w programie Configuration Manager jako aplikację sieci Web, aplikacji i/lub interfejs API sieci Web i że posiadasz [identyfikator klienta z rejestracji](../../active-directory/develop/quickstart-register-app.md).
   2. W witrynie Azure portal zostanie utworzona klucza tajnego aplikacji dla aplikacji zarejestrowanych w usłudze Azure Active Directory.  
   3. W witrynie Azure portal został podany rejestrowanej aplikacji internetowej przy użyciu uprawnień dostępu do obszaru roboczego usługi Log Analytics w usłudze Azure Monitor.  
      ![Połączenie Strona ogólna Kreatora analizy dzienników](./media/collect-sccm/sccm-console-general01.png)
3. Na **usługi Azure Active Directory** ekranu, skonfigurować ustawienia połączenia do obszaru roboczego usługi Log Analytics, zapewniając Twojej **dzierżawy**, **identyfikator klienta**i **Klucz tajny klienta**, a następnie wybierz **dalej**.  
   ![Połączenie do strony Log Analytics Kreatora usługi Azure Active Directory](./media/collect-sccm/sccm-wizard-tenant-filled03.png)
4. Jeśli wykonano wszystkie inne procedury pomyślnie, następnie informacje w **Konfiguracja połączenia pakietu OMS** ekran zostanie automatycznie wyświetlona na tej stronie. Informacje dotyczące ustawień połączenia ma być wyświetlany dla Twojego **subskrypcji platformy Azure**, **grupy zasobów platformy Azure**, i **obszar roboczy pakietu Operations Management Suite**.  
   ![Połączenie do strony połączenia usługi Log Analytics kreatora Log Analytics](./media/collect-sccm/sccm-wizard-configure04.png)
5. Kreator łączy się z obszarem roboczym usługi Log Analytics, korzystając z informacji, które po danych wejściowych. Wybierz kolekcje urządzeń, które chcesz zsynchronizować z usługą, a następnie kliknij przycisk **Dodaj**.  
   ![Wybierz kolekcje](./media/collect-sccm/sccm-wizard-add-collections05.png)
6. Sprawdź ustawienia połączenia na **Podsumowanie** ekranu, a następnie wybierz **dalej**. **Postępu** ekran pokazuje stan połączenia, a następnie należy **Complete**.

> [!NOTE]
> Należy połączyć lokację najwyższego poziomu w hierarchii do usługi Azure Monitor. Jeśli połączysz usługę autonomiczną lokacją główną do usługi Azure Monitor i następnie dodać centralną lokację administracyjną do środowiska, musisz usunięcie i ponowne utworzenie połączenia w nowej hierarchii.
>
>

Po połączeniu programu Configuration Manager do usługi Azure Monitor, można dodać lub usuwać kolekcje i Wyświetl właściwości połączenia.

## <a name="update-log-analytics-workspace-connection-properties"></a>Aktualizowanie właściwości połączenia obszaru roboczego usługi Log Analytics
Jeśli klucz tajny klienta lub hasło nigdy nie wygaśnie lub zostanie utracony, należy ręcznie zaktualizować właściwości połączenia usługi Log Analytics.

1. W programie Configuration Manager przejdź do **usług w chmurze**, a następnie wybierz **łącznika pakietu OMS** otworzyć **właściwości połączenia pakietu OMS** strony.
2. Na tej stronie, kliknij przycisk **usługi Azure Active Directory** kartę, aby wyświetlić swoje **dzierżawy**, **identyfikator klienta**, **Data wygaśnięcia klucza tajnego klienta**. **Sprawdź** swoje **klucz tajny klienta** Jeśli wygasł.

## <a name="import-collections"></a>Importuj kolekcje
Po dodaniu połączenia usługi Log Analytics do programu Configuration Manager i agent zainstalowany na komputerze z programem Configuration Manager połączenia z usługą roli systemu lokacji punktu, następnym krokiem jest zaimportowanie kolekcji z programu Configuration Manager na platformie Azure Monitorowanie jako grup komputerów.

Po wykonaniu konfiguracji początkowej, aby zaimportować kolekcje z hierarchii informacje o członkostwo w kolekcji są pobierane co 3 godziny, aby zapewnić aktualność członkostwa. Można to wyłączyć w dowolnym momencie.

1. W witrynie Azure Portal kliknij pozycję **Wszystkie usługi** w lewym górnym rogu. Na liście zasobów wpisz **Log Analytics**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Wybierz **obszarów roboczych usługi Log Analytics**.
2. Na liście obszarów roboczych usługi Log Analytics wybierz obszar roboczy, którego programu Configuration Manager jest zarejestrowane w usłudze.  
3. Wybierz pozycję **Ustawienia zaawansowane**.
4. Wybierz **grup komputerów** , a następnie wybierz **SCCM**.  
5. Wybierz **członkostwa w kolekcjach programu Configuration Manager importu** a następnie kliknij przycisk **Zapisz**.  
   ![Grupy komputerów - kartę programu SCCM](./media/collect-sccm/sccm-computer-groups01.png)

## <a name="view-data-from-configuration-manager"></a>Wyświetlanie danych z programu Configuration Manager
Po dodaniu połączenia usługi Log Analytics do programu Configuration Manager i agent zainstalowany na komputerze z uruchomionym rolę systemu lokacji punktu połączenia programu Configuration Manager service, dane od agenta są wysyłane do obszaru roboczego usługi Log Analytics w usłudze Azure Monitor. W usłudze Azure Monitor kolekcji programu Configuration Manager są wyświetlane jako [grup komputerów](../../azure-monitor/platform/computer-groups.md). Wyświetlanie grup z **programu Configuration Manager** strony w obszarze **grup Settings\Computer**.

Po zaimportowaniu kolekcje, możesz zobaczyć, na ilu komputerach za pomocą członkostwa w kolekcjach zostały wykryte. Widać również wielu kolekcji, które zostały zaimportowane.

![Grupy komputerów - kartę programu SCCM](./media/collect-sccm/sccm-computer-groups02.png)

Po kliknięciu pojedynczo wyszukiwania zostanie otwarty, wszystkie zaimportowane grupy albo wszystkich komputerów, które należą do każdej grupy. Za pomocą [wyszukiwanie w dzienniku](../../azure-monitor/log-query/log-query-overview.md), możesz rozpocząć szczegółowej analizy danych programu Configuration Manager.

## <a name="next-steps"></a>Kolejne kroki
* Użyj [wyszukiwanie w dzienniku](../../azure-monitor/log-query/log-query-overview.md) Aby wyświetlić szczegółowe informacje dotyczące danych programu Configuration Manager.
